# As-Rigid-As-Possible Surface Modeling

## Run the code and parameters settings

The code can be directly run without any arguments.

To change the loaded mesh, change the file path in ```ARAP::init``` function. Other minor changes such as enabling or disabling parallelization are directlyt marked in code.

## ARAP Implementation

### Demos

**One Anchor**

Anchoring exactly one point, then moving that point, results in perfectly rigid motion (mostly translation, though some rotation for smaller meshes is acceptable).



https://github.com/brown-cs-224/arap-Junyu-Liu-Nate/assets/75256586/8469b5f6-b537-4d93-b463-2dd6cce8669d



**Two Anchors**

Anchoring exactly two points, then rotating one point around the other at a fixed distance, results in perfectly rigid rotation.



https://github.com/brown-cs-224/arap-Junyu-Liu-Nate/assets/75256586/6a7a0aab-8677-4e62-8e20-47e647f4e670



**Permanant Deformation**

Deformations are "permanent"; i.e. un-anchoring previously moved points leaves the mesh in its deformed state, and further deformations can be made on the deformed mesh.



https://github.com/brown-cs-224/arap-Junyu-Liu-Nate/assets/75256586/705b5eab-d049-4ccf-8280-f253b401e085



**Armadillo**

You should be able to make the armadillo wave.



https://github.com/brown-cs-224/arap-Junyu-Liu-Nate/assets/75256586/e4ff4871-425a-4ae9-a57b-2adb7bc79fdb



**Single Tetradedron**

Attempting to deform tetrahedron.obj should not cause it to collapse or behave erratically.



https://github.com/brown-cs-224/arap-Junyu-Liu-Nate/assets/75256586/690abbfd-0e39-4bb0-9528-f8790663430f



**Large Mesh**

Attempting to deform a (large) mesh like bunny.obj or peter.obj should not cause your code to crash.



https://github.com/brown-cs-224/arap-Junyu-Liu-Nate/assets/75256586/88d21d20-311d-40d7-872c-3dc04acd4400



### Implemenetation details

**Build L Matrix**: 
The L matrix is built by assigning corresponding edge weights into positions where i != j and assigning the diagonal positions by summing up the weights around each vertex.

L is computed and pre-factored once during the initialisation.

**Build RHS**:
The RHS is built by summing up the surrounding weights timed by R and movements of each vertex.

RHS is being updated during iterative solving.

**Apply Constraints**:
- For L matrix:
  Detailed implementation can be found in ```void ARAP::assembleL(const std::unordered_set<int>& anchors)```
  - The diagonal position (i = j) on the corresponding row is set to 1.
  - All other positions on the corresponding row is set to 0.
- For RHS:
  Detailed implementation can be found in ```void ARAP::assenbleRHS(const std::unordered_set<int>& anchors)```
  - The corresponding row is set to the exact known (fixed) positions.
  - Other rows (j != i) are added by the weights times the corresponding vertices positions. 

**Iterative Solving**: 
- I use ```Eigen::SimplicialLDLT``` to solve the linear system.
- The default number of iterations per simulation step is ```10```.
- Both directly solving RHS of size n by 3 and separatly solving RHS for each axis (parallel) are implemented.


## Extra Credits  

### Parallelization

The linear system is solved by separatly solving RHS for each axis (parallel). I tried both the **Openmp** and **QtConcurrent** for parallelization. There are minor improvements when using ```peter.obj``` in testing.

- No Parallelization
  
  https://github.com/brown-cs-224/arap-Junyu-Liu-Nate/assets/75256586/54bfd5fc-39d0-457c-a8df-710551e4dc1a

- OpenMP

  https://github.com/brown-cs-224/arap-Junyu-Liu-Nate/assets/75256586/6a6642b1-1701-480d-9a19-51867ca329f7

- QtConcurrent   

  https://github.com/brown-cs-224/arap-Junyu-Liu-Nate/assets/75256586/42d0ca06-7e8c-43c3-a2bf-339b042a6088


## Collaboration/References

I clarify that there is no collaboration include when I do this project.

References for mathematical formulas are coming from the original paper.

## Known Bugs

The main bug is the non-obvious efficiency improvement in Parallelize your code part. The main reason could be that the overhead introduced by managing threads is large.
