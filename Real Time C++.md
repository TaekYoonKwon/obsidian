For software to be considered real time and safe, it is about guaranteeing worst case performance. This is crucial in GNC software, where missing a deadline for execution, such as calculating actuator outputs or perception pipeline, could cause catastrophic failure. 

For most absolute critical pieces of software, it is often done in embedded systems, using RTOS. However, the reality with robotics industry is that much of the development is now happening with Linux devices. As opposed to running RTOS, which has hard guarantees around scheduling and the narrow scope of running just the user firmware, real time systems on Embedded Linux comes with much challenges, and some even argue it is impossible. To go into detail, refer to [[Real Time Linux]]

## 1.  