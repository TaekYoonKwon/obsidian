Eigen Sparse Matrix by default is column major.
## Column Major Representation
$$\begin{bmatrix}
0 & 3 & 0 & 0 & 0 \\
22 & 0 & 0 & 0 & 17 \\
7 & 5 & 0 & 1 & 0 \\
0 & 0 & 0 & 0 & 0 \\
0 & 0 & 14 & 0 & 8
\end{bmatrix}$$
This matrix in column major representation becomes:

|    Values    | 22  |  7  |  _  |  3  |  5  |  _  | 14  |  _  |  1  |  _  | 17  |  8  |
| :----------: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: |
| InnerIndices |  1  |  2  |  _  |  0  |  2  |  _  |  4  |  _  |  2  |  _  |  1  |  4  |
Values just contain the non zero entries within the matrix. It is counted from top to bottom, from leftmost column to the rightmost column. This is the feature of column major representation. If it was row major, it would be counted left to right, from top row to bottom row.
InnerIndices are just the row index at which the corresponding values are found.

*The _ denotes empty space. This is reserved to make it more efficient to add, while the matrix is in **non-compressed mode **

| OuterStarts | 0   | 3   | 5   | 8   | 10  | 12  |
| ----------- | --- | --- | --- | --- | --- | --- |
| InnerNNZs   | 2   | 2   | 1   | 1   | 2   |     |
OuterStarts count how many memory blocks are allocated for that column. So we can think of it as 3 memory entries allocated for the first column (22, 7, _ ), 2 memory entries allocated for the second column (3, 5) and 3 memory entries allocated for the third column (_ , 14, _ ).
Here we see that OuterStarts for the third column, shows there is 3 memory blocks allocated for the third column, when there is only one non zero value, and exactly 2 memory blocks for the second column. This means if we were to insert a non zero value to the second column, we will have to allocate more memory blocks.  
InnerNNZs describe how many non zero entries there are for each column. 

## Compressed Mode - makeCompressed()
The empty spaces denoted above, provides utility for when you want to add non-zero elements quickly. They do not represent any valuable information for the matrix itself. In addition, InnerNNZs, can be easily derived in absence of the empty spaces. To ensure minimum data storage, and consistency with external libraries which often take the inputs in the compressed form (CSC - Compressed Sparse Column), we can call makeCompressed() on a SparseMatrix. This turns an "Uncompressed" SparseMatrix to "Compressed" SparseMatrix.

The example from above becomes:

|    Values    | 22  |  7  |  3  |  5  | 14  |  1  | 17  |  8  |
| :----------: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: |
| InnerIndices |  1  |  2  |  0  |  2  |  4  |  2  |  1  |  4  |

| OuterStarts | 0       | 2   | 4   | 5   | 6   | 8   |
| ----------- | ------- | --- | --- | --- | --- | --- |
| InnerNNZs   | nullptr |     |     |     |     |     |
## Inserting new non zero entries
- insert(row, col):
  This operation turns a **compressed** matrix into a **non-compressed** matrix
  If resize() has not been called earlier and there is no memory, this call also reserves memory for 2x the number of rows for column major. The caveat is that
- coeffRef(row, col):
  If the element does not exist, it is **inserted**. If this element exists already, it will simply be added.

## Reserve space
Common pattern to use Sparse matrix is to reserve during initialisation, the theoretical maximum amount of memory which may be used by this matrix. Doing so prevents dynamic allocation of the memory during runtime, and ensure the main tick loop has real time guarantee.
There are two reserve function signatures in Eigen C++ library. The motivation for two different signatures are so that the library can handle both compressed and uncompressed mode.
**1. inline void reserve(const SizesType& reserveSizes, const typename SizesType::value_type& enableif = typename SizesType::value_type())**
This is used for both compressed and non-compressed modes.
   - Compressed mode:
	1. Convert it back to non-compressed mode, by recalculating OuterStarts and innerNNZs
	2. Loops through every column, to calculate how much total memory needs to be allocated.
	3. Then reserve, grab the large memory block.
	4. Loop through the column backwards. Slide the data blocks to the right.
   - Non-compressed mode:
	1. Calculates how much gap space a column already has, versus how much extra is needed. Reserve never shrinks the allocated memory.
	2. Reserve, grab the large memory block
	3. Loop through the column backwards. Slide the data blocks to the right.
**2. inline void reserve(Index reserveSize)**
This is only applicable to compressed mode. The reason being, with a scalar reserveSize with no indication to the actual distribution of the requested matrix size. 

*"When one requests for 1000 free space, how should they be distributed among the existing columns?"*

The storage of non-compressed matrix, with _ (free memory blocks) and innerNNZs, means it is conceptually incompatible to allocate "x amount of memory" and be it ready to insert, because scalar tells us nothing about which column needs more space. Any request has to specify what is the distribution. Only then it can update the underlying structure accordingly. 
In the case of non-compressed mode, there is no physical distance between the memory fences (OuterStarts). The internal memory is fluid, even though the actual matrix may be rigid. To add to a column, resize must be performed. Otherwise the extra memory that has been reserved will be sitting at the end of the tail like:
`[Col 0] [Col 1] [Col 2] [ ... 100 empty slots ... ]`
And new non zero entries cannot be added to the desired place.
