---
layout: page
title: Sparse Voxels Rasterization with SDF-based Learning
description: SVRaster Geometry Refinement via SDF Learning and NeuS-style Volume Rendering
img: assets/img/lego_novel_ours.jpg
importance: 1
category: work
published: true
related_publications: true
---

## 1. Motivation & Main Contribution

---

### Motivation

- **SVRaster** provides fast performance with morton code based sparse voxel rasterization, but suffers from limited geometry quality due to its density-based representation.
- **NeuS** achieves high-fidelity geometry using SDF-based surface rendering, but is computationally heavy and not real-time friendly.
- **Neuralangelo** introduced second-order derivative regularization to suppress noise and produce smoother surfaces.

This work integrates **SDF-based learning into the SVRaster framework** to improve geometry quality while preserving rasterization efficiency.

---

### Main Contributions

1. **Extension of SVRaster density mode**: Added `density_mode='sdf'` to apply NeuS-style $\alpha$ formulation.
2. **Voxel refinement improvement**: Introduced SDF value criteria into subdivision and pruning to refine voxels near surfaces and prune irrelevant regions.
3. **Grid-Voxel mapping tables**:
   - Motivation: In order for **regularization losses (e.g., Eikonal, smoothness)** to propagate **continuously across neighboring voxels**, we discretize the scene into grids and enable **fast neighbor queries**.
   - Implemented structures:
     - (A) Dense 3D bitmask (grid validity)
     - (B) Compact index table (valid grid idx list)
     - (C) Mapping table (grid idx $\rightarrow$ voxel idx, unique)
   - Implemented with CUDA for efficient neighbor queries.
4. **Extended Loss Functions**:
   - **Eikonal loss** (numerical gradient norm $\approx 1$)
   - **Second-order smoothness loss** (Neuralangelo-style Laplacian/Hessian $=0$)
   - **Random sampling loss**: loss terms evaluated not only at grid centers but also at random positions within grid cells, improving convergence and reconstruction sharpness.

---

## 2. Theoretical Background

- **NeRF**: A framework for learning implicit representations via volume rendering.
- **SVRaster**: Fast novel view synthesis with sparse voxel rasterization and density-based representation.
- **NeuS**: SDF-based volume rendering for more accurate geometry.
- **Neuralangelo**: Introduced second-order derivative regularization to produce smooth and noise-free surfaces.

---

## 3. Methodology

---

### 3.1 SDF Learning within Sparse Voxels

We extend the SVRaster framework to support Signed Distance Functions (SDFs) as the scene representation.

- Each voxel maintains an implicit SDF field defined at its corner grid coordinates.
- For a ray intersecting a voxel, we compute entry and exit points ($t_{\text{entry}}, t_{\text{exit}}$), evaluate the SDF at these positions, and apply the NeuS closed-form $\alpha$ formulation:

$$
\alpha = \max\!\left(\frac{\Phi_s(f(t_{\text{entry}})) - \Phi_s(f(t_{\text{exit}}))}{\Phi_s(f(t_{\text{entry}}))},\, 0\right)
$$

where $\Phi_s$ is the logistic CDF and $f(\cdot)$ is the SDF.

- During backpropagation, we store the first surface hit $sdf0\_t$ to compute gradients for normals and depth, and distribute these gradients to the eight voxel corners by trilinear weights.

---

### 3.2 Voxel Refinement with SDF Criteria

Sparse voxels are adaptively subdivided or pruned during training.

- **Subdivision**: A voxel is split into eight smaller voxels if its SDF values indicate the presence of a surface crossing (i.e., sign changes across corners or small absolute values).
- **Pruning**: Voxels with all SDF values consistently far from zero are removed to save computation.
- This ensures that refinement is focused only on surface-relevant regions.

---

### 3.3 Grid--Voxel Mapping and Neighbor Search

To enable continuous propagation of regularization losses (e.g., Eikonal, smoothness, Laplacian) across space, we introduce a three-part data structure that explicitly links grid cells (fine resolution sampling points) with voxels (coarse sparse blocks). This mapping allows the system to efficiently query spatial neighbors and enforce differential constraints.

#### Data Structures

1. Dense Bitmask (A):

   - A boolean array of size G^3 marking whether each grid coordinate is covered by any voxel.
   - Acts as a global occupancy mask for the grid, enabling O(1) checks of whether a location is valid.

2. Compact Index Table (B):

   - A linear array storing only valid grid indices in ascending order.
   - Built from (A) using prefix-sum compaction, ensuring memory efficiency while retaining ordering.
   - Querying the position of a grid index within this table requires binary search, yielding O(log M) time (where M is the number of valid grid cells).

3. Voxel Index Mapping (C):
   - For each valid grid index in (B), we store the voxel index that owns this grid point.
   - Each grid cell belongs to exactly one voxel, guaranteeing uniqueness and avoiding conflicts.
   - Retrieval from this mapping is O(1) once the compacted index is known.

#### Neighbor Query

- Given a grid coordinate:

  1. Validity Check: Query (A) -> O(1).
  2. Index Localization: If valid, binary search (B) to find the compacted position -> O(log M).
  3. Voxel Retrieval: Use (C) to obtain the corresponding voxel index -> O(1).

- Therefore, fetching the 6 neighbors of a grid coordinate costs O(6 log M) ~ O(log M).
- On the GPU, these queries are executed in parallel across thousands of threads, so in practice the effective cost approaches constant time.

#### Advantages

- Loss Propagation Continuity: Ensures that regularization terms applied at the grid level naturally propagate across voxel boundaries.
- GPU Efficiency: Combination of dense mask and compacted table balances memory usage (sparse storage) with parallel-friendly random access.
- Scalability: Grid resolution can be scaled independently of voxel resolution, allowing fine-grained regularization even in sparse voxel settings.

---

### 3.4 Loss Functions

We design two core losses, both computed **per valid grid index in parallel**, using finite differences.

#### (a) Eikonal Loss (numerical gradient)

We enforce the gradient norm of the SDF to be close to 1.  
For each grid coordinate $x_i$, the gradient is approximated by central differences:

$$
\partial_x f(x_i) \;=\; \frac{f(x_i+\epsilon_x) - f(x_i-\epsilon_x)}{2\epsilon}, \quad
\partial_y f(x_i), \;\partial_z f(x_i)\;\;\text{similarly.}
$$

Then the loss is

$$
L_{\text{eik}} = \frac{1}{N}\sum_{i=1}^N \Bigl|\,\|\nabla f(x_i)\|_2 - 1\,\Bigr|.
$$

#### (b) Smoothness Loss (second-order Laplacian)

We regularize the second derivatives so that curvature vanishes, similar to Neuralangelo.  
At each grid coordinate $x_i$, the Laplacian is estimated with second-order finite differences:

$$
\nabla^{2} f(x_i) \;\approx\;
\frac{f(x_i+\epsilon_x) - 2f(x_i) + f(x_i-\epsilon_x)}{\epsilon^2} +
\frac{f(x_i+\epsilon_y) - 2f(x_i) + f(x_i-\epsilon_y)}{\epsilon^2} +
\frac{f(x_i+\epsilon_z) - 2f(x_i) + f(x_i-\epsilon_z)}{\epsilon^2}.
$$

The smoothness loss is then

$$
L_{\text{smooth}} = \frac{1}{N}\sum_{i=1}^N \bigl|\,\nabla^{2} f(x_i)\,\bigr|.
$$

#### (c) Random Sampling within Grid Cells

Both eikonal and smoothness losses are also evaluated at **random positions inside each grid cell**:

$$
x' = x + \delta,\qquad \delta \sim \mathcal{U}(0.0, 1.0)\cdot h
$$

where $h$ is the grid spacing.  
This stochastic sampling improved stability and resulted in sharper and less noisy geometry in our experiments.

---

## 4. Experimental Results

---

### Dataset

- Replica, DTU, and synthetic NeRF datasets.

---

### Baselines

- Original SVRaster (density-based).
- NeuS.

---

### Metrics

- **Novel view synthesis**: PSNR, SSIM.
- **Geometry quality**: Chamfer Distance, Normal Consistency.

---

### Results Summary

- Novel view synthesis quality is on par with original SVRaster.
- Geometry quality improves significantly, approaching NeuS while remaining more efficient.
- Random sampling loss yields additional improvements in surface sharpness and noise reduction.

---
