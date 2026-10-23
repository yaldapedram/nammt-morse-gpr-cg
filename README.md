# Physics-informed ML for Na-MMT coarse-grained interactions

This repository contains the data-processing workflows, coarse-grained geometry construction, pairwise-distance calculations, Morse + Gaussian process regression (GPR) fitting notebooks, tabulated pair potentials, and LAMMPS examples associated with the development of a coarse-grained interaction model for sodium montmorillonite (Na-MMT).

The model represents platelet interactions using center and edge coarse-grained sites. A Morse potential is used as the baseline interaction, with a GPR correction fitted to reproduce reference all-atom potentials of mean force (PMFs).

## Related manuscript

This repository is associated with the manuscript:

Y. Pedram et al., *A Gaussian process coarse-grained potential for Na-montmorillonite*, submitted.

The full citation and DOI will be added after publication.

## Model overview

Three coarse-grained pair interactions are considered:

- **CC:** center-center interaction
- **EC/CE:** edge-center interaction
- **EE:** edge-edge interaction

The interaction model combines a Morse baseline with a GPR correction. The interaction components are fitted sequentially using PMF data from selected training configurations.

The repository also contains workflows for constructing several platelet configurations and calculating the corresponding pairwise-distance datasets used in the fitting and testing procedure.

## Training and testing configurations

The PMF configurations were divided into training and testing sets. A separate validation set was not used.

### Training configurations

The training data include:

- Small hexagonal platelet–small hexagonal platelet, face-to-face
- Small hexagonal platelet–small hexagonal platelet, edge-to-edge
- Fully periodic sheet–fully periodic sheet, face-to-face
- Fully periodic sheet–small hexagonal platelet, face-to-face

### Testing configurations

The fitted model was evaluated without refitting on additional configurations, including:

- Semi-periodic platelet–semi-periodic platelet, face-to-face
- Fully periodic sheet–small hexagonal platelet, face-to-edge
- Small hexagonal platelet–large hexagonal platelet, face-to-face
- Small hexagonal platelet–small hexagonal platelet with a 30° in-plane rotation

The 30° face-to-face and 63° face-to-edge workflow notebooks reproduce the relative orientation and placement used for these testing configurations.

## PMF and geometry conventions

For the PMF datasets included in this repository:

- **Separation / d-spacing:** distance between the centers of mass of the two interacting platelets
- **Distance units:** Å
- **PMF energy units:** kcal/mol
- **Area normalization:** no area normalization was applied; the PMF values are used directly
- **Energy-zero convention:** each PMF curve was shifted so that its minimum-energy value is zero

The PMF values are sorted by separation before pairwise-distance datasets are generated.

Pairwise distances are calculated using a cutoff of **25 Å** in the corresponding workflow notebooks.

For periodic and semi-periodic configurations, neighboring images are explicitly included where necessary so that interactions crossing the simulation-cell boundaries are not omitted.

## Repository layout

- `src/geometry/` — reusable geometry-building utilities
- `src/pairwise/` — utilities related to pairwise-distance datasets
- `src/pmf/` — utilities for loading and processing PMF data
- `src/training/` — training utilities
- `src/workflows/` — notebooks that construct specific CG configurations and generate their pairwise-distance datasets
- `scripts/` — runnable example and post-processing scripts
- `data/` — PMF reference data and generated pairwise-distance datasets
- `lammps/` — LAMMPS examples and workflow templates
- `tabulated_pair_potentials/` — final tabulated coarse-grained pair interactions

## Geometry and pairwise-distance workflows

The notebooks in `src/workflows/` construct the coarse-grained geometries and calculate the pairwise distances used by the interaction model.

These include workflows for configurations such as:

- Small hexagonal platelet–small hexagonal platelet, face-to-face
- Small hexagonal platelet–small hexagonal platelet, edge-to-edge
- Fully periodic sheet–small hexagonal platelet, face-to-face
- Small hexagonal platelet–small hexagonal platelet, 30° face-to-face
- Fully periodic sheet–small hexagonal platelet, 63° face-to-edge

The notebooks preserve the platelet orientations, relative placement, periodic-image treatment, and pairwise-distance calculations used in the original model-development workflow.

## Model fitting

The fitting notebooks are provided in `src/training/`.

The fitting procedure separates the center-center, edge-center, and edge-edge contributions and uses a Morse baseline together with a GPR correction.

The available notebooks include the EC and EE Morse/GPR fitting stages and preserve the fitting workflow used to generate the coarse-grained interactions.

The generated pairwise-distance dictionaries contain the distances associated with each PMF separation and are used to evaluate the corresponding coarse-grained interaction energy.

## Tabulated pair potentials

The final pair-interaction tables used for coarse-grained simulations are provided in:

```text
tabulated_pair_potentials/
```

The tables contain the coarse-grained interactions for:

```text
center-center
center-edge / edge-center
edge-edge
```

These tables can be used with the LAMMPS `pair_style table` implementation.

## LAMMPS examples

The `lammps/` directory contains examples related to both the underlying PMF calculations and the coarse-grained model.

### Mean-force / PMF workflow

The folder:

```text
lammps/mean_force_pmf_nammt/
```

contains a LAMMPS workflow template for fixed-separation Na-MMT simulations used in PMF analysis.

The workflow applies prescribed displacements between MMT platelets, performs equilibration and production simulations, and outputs quantities required for PMF analysis.

The resulting simulations at different platelet separations can be post-processed to obtain a relative PMF profile.

### Coarse-grained simulation example

A coarse-grained LAMMPS example is also provided to demonstrate the use of the tabulated CC, EC/CE, and EE interactions.

The coarse-grained site types are:

```text
type 1 — center site
type 2 — edge site
```

The corresponding interaction tables are assigned to the appropriate LAMMPS pair coefficients.

## Reproducibility

The repository is intended to provide the main components required to reproduce and inspect the coarse-grained interaction workflow:

1. Reference PMF datasets
2. Coarse-grained platelet geometry construction
3. Periodic-image treatment
4. Pairwise-distance generation
5. Morse + GPR interaction fitting
6. Final tabulated CC, EC/CE, and EE interactions
7. Example use of the interactions in LAMMPS

The testing configurations are evaluated using the model fitted to the training configurations; the interaction model is not refitted to the testing PMFs.
