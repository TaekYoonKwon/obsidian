Currently the problem is that the guidance module is deeply coupled with OSQP optimiser. As we foresee that there can be many shortcomings of OSQP, and also the greedy and reactive type of guidance, and we may need to migrate to unconstrained NLP and trajectory based guidance, we must decouple the optimiser from the guidance at the architectural level.

## Current Status
Currently the guidance module's tick is deeply coupled with OSQP. It directly calls all OSQP specific functions which are inside the guidance module. The guidance module also has all the buffers and structs specific to OSQP. 
```c++
void build_cost(const SafetySnapshot &snap, const FollowerTargetVelocity<N> &target,const VehicleState &vehicle);
void build_csc(const ConstraintTerm<N> *constraints, int count);
void build_constraints(const SafetySnapshot &snap);
[[nodiscard]] bool solve(const VehicleState &vehicle);
```
Ideally, the guidance module should have no knowledge of which optimisation engine it is running, and invoking the building cost and constraints etc, should not be guidance module's duty. This means keeping track of the SafetySnapshot - should be done by another module, and the SafetySnapshot will be a polymorphed class for a set of half planes or ESDF occupancy grid, safe corridor, local trajectories etc.

## Goal State
Once the refactor is finished, I would like the guidance module to reduce down to:
```c++
# Guidance Module tick()
bool GuidanceModule::tick() {
    report_status_periodically();

    if (!is_in_guided_mode()) return false;

    if (!preconditions_ok()) {
        handle_precondition_failure();
        return false;
    }

    if (!p_optimiser_->solve()) {
        handle_solve_failure();   // → LOITER, etc.
        return false;
    }

    // optimiser pushed output via callback already
    return true;
}


# Now the optimiser interface:
#include <functional>

class IOptimiser {
public:
    virtual ~IOptimiser() = default;
    virtual bool init() = 0;
    virtual bool solve() = 0;
    virtual void shutdown() = 0;

    // Define the signature of the callback
    using OutputCallback = std::function<void(const ControlOutput&)>;
    
    // Pass a callback instead of a sink object
    void set_output_callback(OutputCallback cb) { callback_ = std::move(cb); }

protected:
    OutputCallback callback_;
};

// Inside QPOptimiser
bool QPOptimiser::solve() {
    auto* front = active_.load(std::memory_order_acquire);
    if (!front->solve()) return false;
    
    // Just call the function directly
    if (callback_) callback_(front->result());
    return true;
}

# Solvers subscribe directly to SafetySnapshots and whatever else to do the precompute.
# Inside QPOptimiser:
# Runs on its own thread
template<int N, int dim>
class QPOptimiser : IOptimiser<N, dim> {
public:
	void prepare(void);
	[[nodiscard]] bool solve(double vx, double vy, double vz);
private:

}

template<int N, int dim>
class QPInstance {
public:
	[[nodiscard]] bool warm_up(const HalfPlane2D<N>& safety, const VehicleState& vehicle);
	[[nodiscard]] bool solve(void);
private:
	OSQP_Solver solver_;
	void eigen_to_osqp_safe(const Eigen::SparseMatrix<double, Eigen::ColMajor> &mat, OSQPCscMatrix *out, OSQPFloat *x_buf, OSQPInt *i_buf, OSQPInt *p_buf);
	void build_csc(const ConstraintTerm<N> *constraints, int count);
	void build_cost(const SafetySnapshot &snap, const FollowerTargetVelocity<N> &target,
}

template<int N, int dim>
void QPOptimiser<N, dim>::prepare(void){
	std::array<HalfPlane2D,N> safety = safety_cache_->read()
	VehicleState vehicle = vehicle_cache_->read()
    auto* back = (active_.load() == &instances_[0]) ? &instances_[1] : &instances_[0];
	# We have two OSQP instance, we warm up the one that is not pointed at by the guidance module.
	OSQP_warmUp(back, safety, vehicle);
	active_.store(back, std::memory_order_release);
}

template<int N, int dim>
[[nodiscard]] bool QPOptimiser<N, dim>::solve(){
    auto* front = active_.load(std::memory_order_acquire);       return front->solve();
}


# Safety Snapshot definition
template<int N, typename t>
class ISafetySnapshot 
{
	virtual ~ISafetySnapshot() = default
public:
	[[nodiscard]] virtual bool write(const t* data, int n) = 0;
	[[nodiscard]] virtual bool read() = 0;
private:
	seqLockBuf<t> buf{};
}

# HalfPlane SafetySnapshot
template<int N, typename HalfPlane2D>
class HalfPlane2DSnapshot: public ISafetySnapshot<N, HalfPlane2D> {
public:
	
private:
}
```

## Refactor strategy
First of all, we want to decouple OSQP specific logic from the guidance module. This is easy to do, but more consideration is required for different approaches of guidance. The trajectory based 