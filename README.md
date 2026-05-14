# pyWSciML

A Python library for Weak-Form Scientific Machine Learning, implementing the [WENDy](https://doi.org/10.1007/s11538-023-01208-6) (parameter estimation) and [WSINDy](https://doi.org/10.1137/20M1343166) (equation discovery) frameworks.

This library is a refactored, object-oriented reorganization of the research code accompanying the paper ["Weak Form Scientific Machine Learning: Test Function Construction for System Identification"](https://arxiv.org/abs/2507.03206) (Tran & Bortz, 2025). For a detailed description of the library's design, structure, and motivation, see the [full report (PDF)](docs/pyWSciML_report.pdf).

## Structure

```
WData                        # Base data container and configuration
  └── OLS_Solver             # Test function construction + OLS solve
        ├── IRLS_Solver      # Iteratively reweighted least squares (WENDy)
        └── SparsifyDynamicsSolver  # Sequential thresholding (WSINDy)

Simulation                   # Forward integration of recovered models
```

## Quick Start

```python
import numpy as np
from IRLS_Solver import IRLS_Solver
from Simulation import Simulation

# Define your feature library (list of lists of lambda functions)
features = [
    [lambda x, y, z: y, lambda x, y, z: x],
    [lambda x, y, z: x, lambda x, y, z: x*z, lambda x, y, z: y],
    [lambda x, y, z: x*y, lambda x, y, z: z]
]

# Fit the IRLS solver
model = IRLS_Solver(features, xobs, tobs, type_tf=0, toggle_SVD=False,
                    gap=1, p=16, S=1, mu=[1, 2, 1], diag_reg=1e-10,
                    Mtilde=None, trunc=0, radius=None, type_rad=0)
model.fit_IRLS()

# Simulate forward using recovered parameters
sim = Simulation(features=features, w_hat=model.w_hat, x0=xobs[0], t=tobs)
u_hat = sim.simulate()
```

See `pyWENDy_script.ipynb` for full working examples on the Lorenz system, logistic growth, and overcomplete library sparsification.

## Dependencies

- NumPy
- SciPy
- SymPy
- Matplotlib

## Acknowledgments

All algorithms implemented in this library are the original research contributions of **David M. Bortz** and **April Tran** at the University of Colorado Boulder, along with their collaborators. The underlying code was written by Tran and Bortz. This repository does not introduce new algorithms or methods; my contribution was limited to reorganizing the existing codebase into a structured, object-oriented library.
