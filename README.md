[![CI](https://github.com/org-arl/AcousticRayTracers.jl/workflows/CI/badge.svg)](https://github.com/org-arl/AcousticRayTracers.jl/actions)
[![Codecov](https://codecov.io/gh/org-arl/AcousticRayTracers.jl/branch/main/graph/badge.svg)](https://codecov.io/gh/org-arl/AcousticRayTracers.jl)

# AcousticRayTracers

`RaySolver` is a differentiable 2½D Gaussian beam tracer for use with [`UnderwaterAcoustics.jl`](https://github.com/org-arl/UnderwaterAcoustics.jl).
It is similar to [Bellhop](http://oalib.hlsresearch.com/AcousticsToolbox/), but fully written in Julia to be compatible with automatic differentiation
tool such as `ForwardDiff`, `ReverseDiff` and `Zygote`, and other tools including `Turing`, `Measurements`, etc.

NOTE: The `RaySolver` model is experimental and being actively developed, and should be considered as a **beta release** at present. Feedback and bug reports are welcome.

---

## Installation

```julia
julia> # press ]
pkg> add UnderwaterAcoustics
pkg> add AcousticRayTracers
pkg> # press BACKSPACE
julia> using UnderwaterAcoustics
julia> using AcousticRayTracers
julia> models()
2-element Vector{Any}:
 PekerisRayModel
 RaySolver
```

## Usage

The propagation modeling API is detailed in the [UnderwaterAcoustics](https://org-arl.github.io/UnderwaterAcoustics.jl/stable/) documentation.
We assume that the reader is familiar with it. This documentation only provides guidance on specific use of `RaySolver` propagation model.

Additional options available with `RaySolver`:

- `nbeams` -- number of beams used for ray tracing (default: auto)
- `minangle` -- minimum beam angle in radians (default: -80°)
- `maxangle` -- maximum beam angle in radians (default: 80°)
- `ds` -- ray trace step size in meters (default: 1.0)
- `atol` -- absolute tolerance of differential equation solver (default: 1e-4)
- `rugocity` -- measure of small-scale variations of surfaces (default: 1.5)
- `athreshold` -- relative amplitude threshold below which rays are dropped (default: 1e-5)
- `solver` -- differential equation solver (default: auto)
- `solvertol` -- differential equation solver tolerance (default: 1e-4)

**Example:**

```julia
using UnderwaterAcoustics
using AcousticRayTracers
using Plots

env = UnderwaterEnvironment(
  seasurface = SeaState2,
  seabed = SandyClay,
  ssp = SampledSSP(0.0:20.0:40.0, [1540.0, 1510.0, 1510.0], :linear),
  bathymetry = SampledDepth(0.0:100.0:200.0, [40.0, 35.0, 38.0], :linear)
)
pm = RaySolver(env; nbeams=1000)
tx = AcousticSource(0.0, -5.0, 1000.0)
rx = AcousticReceiver(200.0, -20.0)
r = eigenrays(pm, tx, rx)
plot(env; sources=[tx], receivers=[rx], rays=r)
```

![](https://raw.githubusercontent.com/org-arl/AcousticRayTracers.jl/main/docs/images/eigenrays2.png)

For more information on how to use the propagation models, see [Propagation modeling toolkit](https://org-arl.github.io/UnderwaterAcoustics.jl/stable/pm_basic.html).
