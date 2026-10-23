# Numerical Modeling of Convective and Diffusive Transport in the Lungs

**Biomedical Engineering | Fluid Mechanics | Partial Differential Equations | Scientific Computing**

Academic group project, EMINES – Mohammed VI Polytechnic University (UM6P), Morocco  
**December 2024**

## Overview

How does airway geometry influence the transport of air and oxygen in the lungs? This project investigates the question through two complementary mathematical and computational approaches:

1. **Convection:** a two-dimensional, low-Reynolds-number model of airflow based on the steady Stokes equations.
2. **Diffusion:** a stationary oxygen-transport model based on Fick's law, studied analytically and through stochastic particle simulations.

The emphasis is on translating physical equations into numerical models, imposing meaningful boundary conditions, comparing simulations with theoretical predictions, and examining how asymmetric geometries redistribute flow and concentration.

> **Project scope:** This is an academic study using idealized geometries and simplified physical assumptions. It is not a patient-specific lung model or a clinically validated simulation.

## 1. Research Question and Physical Background

The respiratory tree is geometrically asymmetric. Air transport is predominantly convective in larger airways, while diffusion becomes increasingly important at smaller scales. We studied these mechanisms separately to explore the consequences of geometry and boundary conditions for velocity, pressure, concentration, and oxygen flux.

Our work was organized around four questions:

- How can steady viscous airflow be approximated in a simplified airway geometry?
- How do mesh resolution and geometric deformation affect the numerical solution?
- Can stochastic particle transport reproduce the expected concentration profile of a diffusion equation?
- How do obstacles and asymmetric boundaries change local oxygen concentration and flux?

## 2. Convection: Two-Dimensional Stokes Flow

### Governing equations

For incompressible, highly viscous flow at low Reynolds number, the model uses the steady Stokes system:

$$
-\nabla p + \mu \Delta \mathbf{u}=\mathbf{0},
\qquad
\nabla\cdot\mathbf{u}=0,
$$

where $\mathbf{u}=(u_x,u_y)$ is velocity, $p$ is pressure, and $\mu$ is dynamic viscosity.

A simplified channel/bronchiole geometry is used to examine Poiseuille-type flow, both **without deformation** and **with a geometric obstruction/deformation**.

### Spatial discretization and numerical solution

We constructed a two-dimensional rectangular grid with $n\times m$ nodes and spacing $\Delta x$ and $\Delta y$. The equations for the pressure and two velocity components were discretized on this grid, resulting in a linear algebra problem:

$$
A\mathbf{x}=\mathbf{b},
\qquad
\mathbf{x}=\begin{bmatrix}\mathbf{u}_x\\\mathbf{u}_y\\\mathbf{p}\end{bmatrix}.
$$

The resulting formulation contains **three unknown fields per grid node**, for a total of $3nm$ discrete unknowns before accounting for boundary constraints.

The study examines the role of:

- **No-slip walls:** zero velocity at rigid walls.
- **Pressure constraints:** prescribed inlet and outlet pressure to drive the flow.
- **Axial periodicity and transverse-flow constraints:** conditions introduced in the simplified reference configuration.
- **Geometric deformation:** comparison with a more complex flow domain.

The report discusses numerical discretization in the context of finite-element methods; the displayed grid equations themselves use a finite-difference-style stencil. For this reason, the computational approach is described here more generally as **grid-based numerical discretization** rather than claiming a specific finite-element implementation.

### Analyses performed

- Computed and visualized velocity and pressure fields in the undeformed domain.
- Evaluated equation residuals and how numerical errors changed with mesh refinement.
- Repeated the analysis with a deformed geometry/obstacle.
- Examined flow rate as a function of pressure difference and channel dimensions.
- Compared numerical responses under reversed flow conditions (inspiration versus expiration) to examine approximate reversibility in the simplified Stokes regime.

### Observations

In the reference geometry, the numerical results closely matched the theoretical low-Reynolds-number behavior, with very small reported equation residuals. The mesh-resolution analysis illustrated a trade-off between accuracy and computational cost. With deformation, the velocity and pressure distributions became more spatially complex, while the simulations remained consistent with the prescribed incompressible-flow formulation.

The study also reported nearly symmetric responses for reversed driving conditions, as expected for the idealized linear model used here. These observations should not be generalized to full physiological airflow without a richer model and validation.

## 3. Diffusion: Oxygen Transport in the Acinus

### Governing equations

Diffusion was modeled using Fick's law:

$$
\frac{\partial c}{\partial t}=D\Delta c,
\qquad
\mathbf{J}=-D\nabla c,
$$

where $c$ denotes oxygen concentration, $D$ the diffusion coefficient, and $\mathbf{J}$ the diffusive flux.

Under the **stationary-state assumption**, the concentration satisfies:

$$
\Delta c=0.
$$

### Boundary conditions and analytical reference

We explored three types of boundary conditions:

- **Dirichlet:** prescribed concentration at a source boundary.
- **Neumann:** zero normal concentration gradient at reflective boundaries.
- **Robin:** partially absorbing boundary representing transfer across an idealized membrane, with absorption governed by a permeability parameter $\kappa$.

For the symmetric reference geometry, an analytical concentration profile was derived under the stated assumptions. This provided a baseline against which to compare stochastic simulation results.

### Stochastic particle simulation

The complementary approach modeled diffusion as a random walk of particles on a discretized domain. At each time step, particles move randomly between neighboring cells and interact with boundaries according to the specified rules:

1. Initialize the domain, particle positions, and transport parameters.
2. Move particles randomly in the four grid directions.
3. Apply **reflection** at impermeable boundaries and **probabilistic absorption** at the membrane.
4. Reintroduce particles through the source mechanism used in the simulation.
5. Accumulate cell visits and normalize them to estimate a concentration map.
6. Estimate flux components from numerical concentration gradients:

$$
J_x=-D\frac{\partial c}{\partial x},
\qquad
J_y=-D\frac{\partial c}{\partial y}.
$$

The report uses an idealized acinar domain of approximately **3 mm**, with illustrative parameters $D=20\ \mathrm{mm^2/s}$ and membrane permeability $\kappa=0.08\ \mathrm{mm/s}$. These are modeling inputs, not values independently measured during the project.

### Simulation scenarios and sensitivity analysis

Two geometric cases were compared:

- **Symmetric domain:** a reference geometry for comparison with the analytical diffusion profile.
- **Asymmetric/deformed domain:** a geometry containing triangular obstacles or indentations, used to investigate local changes in concentration and flux.

A parameter study varied the **number of particles** and **number of simulation steps** to assess the stability of the estimated transport quantities. In the tested discretization, the reported results became more stable beyond roughly 5,000 particles and 5,000 steps. This is a result for the study's settings, **not a universal convergence threshold**.

### Observations

- In the symmetric domain, the simulated concentration profile followed the expected predominantly vertical gradient, with little horizontal flux.
- Introducing asymmetric obstacles redistributed particle visits and generated a nonzero horizontal component of diffusive flux.
- The resulting concentration and current maps showed local flux deflection and reduced transport in some regions behind obstacles.
- Increasing the simulated particle count and time horizon reduced stochastic fluctuations in the reported parametric experiments.

## 4. Main Lessons

This project highlighted the complementary roles of **deterministic numerical methods** and **stochastic simulations**:

| Deterministic flow/discretization | Stochastic diffusion simulation |
|---|---|
| Works directly with governing equations for continuum fields | Approximates microscopic transport through particle movement |
| Produces velocity and pressure distributions | Produces concentration and flux estimates |
| Sensitive to mesh resolution and boundary implementation | Sensitive to particle count, simulation duration, and boundary rules |
| Particularly useful for comparing idealized flow scenarios | Particularly useful for visualizing transport in complex geometries |

Across both approaches, **geometry and boundary conditions** had a central influence on the computed transport behavior.

## 5. Limitations and Reproducibility

- The geometries are deliberately simplified, two-dimensional representations of pulmonary structures.
- The convective and diffusive subproblems are analyzed separately rather than combined into a fully coupled physiological model.
- The results depend on model parameters, boundary assumptions, and discretization choices.
- The numerical values and observations summarize the original academic report; independent replication and clinical validation were not performed here.
- **This repository is documentation-focused.** The original simulation scripts were not provided for this public repository, so the reported experiments cannot currently be rerun from this README alone.

## 6. Academic Context and Contributors

**Institution:** EMINES – School of Industrial Management, Mohammed VI Polytechnic University (UM6P)  
**Course/project:** Biomedical Engineering — *Asymmetric Convective and Diffusive Transport*  
**Supervisor listed in the original report:** Marcel Filoche
