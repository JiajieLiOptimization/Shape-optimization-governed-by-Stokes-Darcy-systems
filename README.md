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
|   |-- three-dimensional Taylor test/
```

## Common Numerical Settings

All codes are implemented in FreeFEM++. The free-flow region $\Omega_f$ is governed by the Stokes equations, and the porous-medium region $\Omega_p$ is governed by the Darcy equation. The two regions are coupled on the interface $\Gamma$ by the normal-flux condition, the normal-stress condition, and the Beavers--Joseph--Saffman condition.

Unless otherwise stated, $g=1$ and the body force terms are neglected. The Beavers–Joseph constant $\alpha$ is the fundamental interface parameter. For isotropic conductivity $\mathbf K=k_p\mathbf I$, the BJS coefficient is computed as $\gamma=\alpha\sqrt{\mu_f g/k_p}$ for each parameter set; it is not held fixed when $k_p$ or $\mu_f$ varies. The objective-change tolerance is specified separately for each example below. For the free-flow region, a prescribed inlet velocity is imposed on $\Gamma_{f,D}$, and a natural boundary condition $T_f n_{\Omega_f}=0$ is imposed on the outlet boundary $\Gamma_{f,N}$. For the porous-medium region, a fixed hydraulic head $\phi=0$ is imposed on $\Gamma_{p,D}$, and no-flux conditions are imposed on the remaining porous boundaries.

The interface is updated by a moving body-fitted mesh. The mesh connectivity is kept fixed during the optimization. In two dimensions, the FreeFEM++ function `checkmovemesh` is used with step-size halving to prevent triangle inversion. The three-dimensional optimization codes use conservative fixed step sizes. No remeshing is used in the present codes.

The two-dimensional optimization examples use the distributed Eulerian derivative, while the three-dimensional examples use the interface-type Eulerian derivative. The manuscript reports a two-dimensional Taylor test for both derivative expressions in Case 4.1.2. A separate three-dimensional Taylor-test script is provided under Sec. 4.4.

The fluid-volume constraint is treated using an augmented Lagrangian,

$
\mathbb G(\Omega_f)=\mathcal J(\Omega_f)+l\bigl(|\Omega_f|-V_0\bigr)
+\frac{\beta_1}{2}\bigl(|\Omega_f|-V_0\bigr)^2,
\qquad l\leftarrow l+\beta_2\bigl(|\Omega_f|-V_0\bigr).
$

Here, $\beta_1$ is the penalty parameter and $\beta_2$ is the multiplier-update step. The stopping criterion monitors the relative change in the augmented objective, subject to a maximum iteration count. The final relative volume error $\bigl||\Omega_f|-V_0\bigr|/V_0$ is reported separately and is not an additional stopping criterion. Reaching the iteration cap does not establish convergence to a constrained minimizer.

## Sec 4.1 Single-interface Around a Porous Region

This folder contains the codes for Example 1. It considers a Stokes--Darcy coupled system with a single moving interface around a porous region. In this example, the velocity Dirichlet boundary is divided into the inflow boundary $\Gamma_{f,i}$ and the no-slip wall boundary $\Gamma_{f,w}$. The prescribed inflow profile $\mathbf u_d$ is imposed on $\Gamma_{f,i}$, and homogeneous no-slip conditions are imposed on $\Gamma_{f,w}$.

### Case 4.1.1

Case 4.1.1 is motivated by surface water flow over a permeable riverbed sediment layer. The computational domain is $\Omega=[0,2]\times[0,1]$, where an elliptical porous-medium region is embedded in the lower part of the domain:
$
\Omega_p=\left\{(x,y)\in\mathbb R^2:\frac{(x-1)^2}{0.5^2}+\frac{y^2}{0.4^2}\leq 1,\ y\geq0\right\}.
$
The parameters are $\mu_f=10^{-4}$, $k_p=1$, $\alpha=100$, $\beta_1=1000$, $\beta_2=1$, $V_0=1.1|\Omega_f^0|$, and $\varepsilon_{\mathrm{obj}}=5\times10^{-4}$. The inlet velocity is $\mathbf u_d=[0.2,0]^\top$. The initial mesh contains 6246 fluid triangles and 1130 porous-medium triangles.

The code solves the state and adjoint systems, computes the shape gradient, and updates the fluid--porous interface by the moving-mesh method. It also evaluates the mass-conservation residual in the Stokes region and the residuals of the Stokes--Darcy interface conditions, including the normal-flux condition, the normal-stress balance, and the Beavers--Joseph--Saffman condition. The reported interface becomes smoother and the energy dissipation decreases. The final relative volume error is $6.85\times10^{-4}$; the equation and interface residuals are reported in the manuscript.

### Case 4.1.2

Case 4.1.2 considers another initial single-interface geometry with a more strongly curved fluid--porous interface. The parameters are $\mu_f=1$, $k_p=10^{-3}$, $\alpha=0.1$, $\beta_1=100$, $\beta_2=0.01$, $V_0=1.02|\Omega_f^0|$, and $\varepsilon_{\mathrm{obj}}=5\times10^{-5}$. The inlet velocity is $\mathbf u_d=[0.2,0]^\top$. The code follows the same optimization loop as Case 4.1.1. This case is used to test the robustness of the proposed moving body-fitted mesh method for a more curved initial interface. It is also used in the manuscript to validate the derived Eulerian shape derivatives by a Taylor test.

## Sec 4.2 Multi-interface Optimization in an L-shaped Configuration

This folder contains the code for Example 2. It considers a multi-interface shape optimization problem in a two-dimensional L-shaped configuration. The computational domain is $\Omega=[0,1]^2$. A uniform inlet velocity $\mathbf u_d=[1.2,0]^\top$ is imposed. The main parameters are $\mu_f=0.5$, $k_p=5\times10^{-5}$, $\alpha=0.01$, $\beta_1=300$, $\beta_2=0.1$, $V_0=0.62|\Omega_f^0|$, and $\varepsilon_{\mathrm{obj}}=10^{-6}$. The maximum iteration count is 250. Compared with Sec 4.1, this example contains multiple fluid--porous interface components and non-smooth geometric features. The code constructs the L-shaped geometry, solves the Stokes--Darcy state and adjoint systems, computes the distributed Eulerian derivative, and moves all admissible interface components during the optimization process. A curvature-based term with coefficient $\alpha_\Gamma=30$ is added only when computing the deformation direction. This external smoothing term is excluded from the monitored augmented objective, the stopping criterion, and the objective values reported in the manuscript. It does not guarantee a decrease in the monitored objective at every iteration.

## Sec 4.3 Diffuser Model

This folder contains the codes for Example 3, the diffuser model with porous walls. It includes both two-dimensional and three-dimensional implementations. This example investigates the influence of the porous-medium permeability on the optimized interface shape and flow performance. The viscosity is fixed as $\mu_f=1.0$, and the permeability values are $k_p=0.01$ and $k_p=0.005$. For the permeability study, the target volume and optimization parameters are kept fixed in each dimensional setting, and the fundamental constant $\alpha$ is fixed. The permeability $k_p$ is varied and $\gamma$ is recomputed accordingly.

### Two-dimensional code

For the two-dimensional diffuser case, a uniform inlet velocity $\mathbf u_d=[0.1,0]^\top$ is imposed. The parameters are $\alpha=0.01$, $\beta_1=30$, $\beta_2=10^{-4}$, $V_0=1.35|\Omega_f^0|$, and $\varepsilon_{\mathrm{obj}}=10^{-3}$. The code solves the 2D Stokes--Darcy state and adjoint systems, computes the shape gradient, and updates the diffuser interface. It outputs the optimized shape, velocity field, pressure fields, objective history, and volume-error history.

### Three-dimensional code

For the three-dimensional diffuser case, a uniform inlet velocity $\mathbf u_d=[0,1,0]^\top$ is imposed. The parameters are $\alpha=0.1$, $\beta_1=2500$, $\beta_2=15$, $V_0=1.5|\Omega_f^0|$, and $\varepsilon_{\mathrm{obj}}=10^{-8}$. The initial mesh contains 11407 tetrahedra. Both reported permeability runs terminate at the prescribed maximum of 400 iterations. The code extends the diffuser model to a 3D Stokes--Darcy coupled system. It uses the interface-type Eulerian derivative and demonstrates that the proposed shape-gradient method can be applied to three-dimensional interface optimization problems.

## Sec 4.4 Embedded Vessel–Tissue Coupling Model

This folder contains the codes for Example 4, the embedded vessel--tissue coupling model. It includes both two-dimensional and three-dimensional implementations. The free-flow region represents a vessel-like channel, and the porous region represents the surrounding biological tissue.

### Two-dimensional code

The two-dimensional code considers a planar vessel--tissue configuration and studies the influence of the fluid viscosity on the optimized interface. The viscosity values are $\mu_f=1.0$, $0.1$, and $0.01$. The remaining parameters are $k_p=10^{-5}$, $\alpha=0.003$, $\beta_1=1000$, $\beta_2=1$, $V_0=1.3|\Omega_f^0|$, and $\varepsilon_{\mathrm{obj}}=10^{-3}$. The inlet velocity is $\mathbf u_d=[1,0]^\top$. The BJS coefficient is recomputed for each viscosity. The code solves the coupled state system, computes the adjoint variables and shape gradient, updates the vessel--tissue interface, and records the optimized flow fields and convergence histories.

### Three-dimensional code

The three-dimensional code considers a spatial embedded vessel--tissue configuration. In the reported 3D optimization, the parameters are $\mu_f=0.01$, $k_p=10^{-4}$, $\alpha=0.01$, $\beta_1=1000$, $\beta_2=10^{-3}$, $V_0=4|\Omega_f^0|$, and $\varepsilon_{\mathrm{obj}}=10^{-5}$. The inlet velocity is $\mathbf u_d=[0,1.5,0]^\top$. The supplied mesh contains 2262 fluid tetrahedra and 78142 porous-medium tetrahedra, totaling 80404. The reported run terminates at the prescribed maximum of 450 iterations. The code solves the 3D Stokes--Darcy system, computes the interface-type Eulerian derivative, updates the vessel--tissue interface, and outputs the optimized geometry, velocity field, pressure fields, objective history, and volume-error history.

### Three-dimensional Taylor test

The separate script `Taylor _test_3D.edp` evaluates the finite-difference error for the interface-type derivative using a fixed prescribed perturbation field. The field vanishes on the fixed outer boundary and is normalized by the largest absolute nodal component. The state system is solved again for each perturbation size $\delta_0\in\{0.04,0.02,0.01,0.005\}$, without a volume-constraint contribution to the tested objective.

The test uses $\mu_f=0.01$, $k_p=10^{-4}$, $g=1$, and $\alpha=0.1$. Thus, its BJS parameter differs from that of the three-dimensional optimization run above.

**Implementation note:** In the current package, the optimization codes project $G_3$ and the vector field defining $G_4$ into continuous surface $P_1$ spaces before computing their surface divergences. The Taylor-test script instead uses directly expanded facewise expressions. The test therefore does not yet verify exactly the same discrete interface-derivative implementation as the optimization codes.

