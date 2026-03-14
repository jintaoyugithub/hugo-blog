# Fluid Simulation


<!--more-->

# Parallax Voxel Ray Marcher

> [!TODO]

{{< youtube N4Vo6mc0ijE >}}

## Overview

- incompressible fluid

modify velocity values -> make the fluid incompressible(projection) -> move the velocity field (advection)

`forcing incompressibility(projection)`

     vi, j+1
ui,j         ui+1, j
     vi,j

d = ui+1,j + vi,j+1 - ui, j - vi, j

- d > 0 too much outflow
- d < 0 too much inflow


`advection`

obtain the previous velocity:

prevV(x) = x - deltaT * curV(x);

## Questions

1. collocated grid vs. staggered grid

2. what's divergence? (total ouflow)




