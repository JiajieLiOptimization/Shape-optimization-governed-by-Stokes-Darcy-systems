# Shape-Optimization-Stokes-Darcy-System

## Abstract

We propose a novel shape optimization model governed by the Stokes–Darcy system with the Beavers–Joseph–Saffman interface conditions, aiming to minimize energy dissipation within the free-flow region. By performing shape sensitivity analysis that accounts for the effects arising from the nonhomogeneous interface conditions and multiphysics coupling, we derive both distributed and interface-type Eulerian derivatives. To numerically solve this multiphysics coupling PDE-constrained optimization problem, we propose a shape gradient flow-based algorithm, which is discretized via the finite element method and realized with the moving body-fitted grid technique. Numerical examples demonstrate that the proposed algorithm enables shape optimization simulations under diverse permeability and viscosity coefficients, with effectiveness in both 2D and 3D configurations.

## Code Package Structure

The code package is organized as follows:

```text
Shape-Optimization-Stokes-Darcy-System/
|
|-- Sec 4.1: Single-interface around a porous region/
|   |-- Case 4.1.1/
|   |-- Case 4.1.2/
|
|-- Sec 4.2: Multi-interface optimization in an L-shaped configuration/
|
|-- Sec 4.3: Diffuser model/
|   |-- 2D/
|   |-- 3D/
|
|-- Sec 4.4: Embedded vessel-tissue coupling model/
|   |-- 2D/
|   |-- 3D/
