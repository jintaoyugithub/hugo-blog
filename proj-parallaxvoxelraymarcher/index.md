# Parallax Voxel Ray Marcher


<!--more-->

# Parallax Voxel Ray Marcher

{{< youtube 21KFuvCqHIU >}}


## Overview

`Ray Marching`:

0-1 -> -1-1

0-wid -> -wid-wid

x-0 / wid-0 = x'+wid / 2wid
x/wid * 2wid = x'+wid
x = 2x - wid;

uv = x / res.y; //y控制在-1-1，而x根据y的比例缩放

`Prepare`:

In VS:

- posWS
- viewDirMS + camPosMS
- dot(ViewDirWS, cubeNormalWS)?

In PS:

several function here:

1. finding start point

```c++
start findStartPoint() {
    // ray theory
    interPoint = startPos + t * dir;
    t = (interPoint - startPos) / dir;

    // aabb detestation theory
    // the cube x,y,z coord are in [0-1]
    t1 = (0.0 - startPos.x) / dir.x;
    t2 = (1.0 - startPos.x) / dir.x;
    ..
    t5 = (0.0 - startPos.z) / dir.z;
    t6 = (1.0 - startPos.z) / dir.z;

    // 最后一个进去volume才算整个完全进入
    tmin = max(max(min(t1, t2), min(t2, t3), min(t3, t4)));
    // 最早一个出去的就算已经出去了
    tmax = min(min(max(t1, t2), max(t2, t3), max(t3, t4)));

    // if camera is insi
}
```

2. distance field + sphere tracing

make sure we won't miss any hit,

this is an optimization for this project, because we still step on grid every time

3. hit function

```c++
hit fixed_step() {
    // get cur voxel id
    vec3 cur = vec3(posWS.x/voxelSize, ...);
    vec3 dir = normalize(viewDirMS);

    float stepX = dir.x > 0 ? 1 : -1;
    // same for stepY and stepZ

    // find the next voxel
    float next_voxel_x = (cur.x + stepX) * voxelSize;
    // same for next_voxel_y and z

    // compute tmaxX, Y and Z
    // 他们表示从当前的voxel pos到边界，要多少t

    // compute the deltaX, Y and Z
    // 他们按照当前的dir分量，走一个voxel size需要多少t

    for() {
        // follow the minimum tmax
        // every loop, step forward along the dir of minimum tmax
        // tmax += tDelta
    }
}
```

4. cal normal


## 3D Celluar Automata

rules:

- survival: a cell will survive only if it has the amount of neighbours which *survival* specified

- spawn/birth: a empty cell will be give birth only if ...

- state: hidden state vaule, it will decrease by 1 each round after the cell has been determined as dying, the cell will actual die only if the state hits zero

- neighbour: moore and 3d von neumann

moore: any anything one cell away will be counted as neighbour, which means cells that are diagonally adjacent to the center cell is also considered a neighbor

3d von neumann: only consider the cells as neighbour only if their faces meet each other

### Optimization

1. I could use unordered set to store different rules, so I can fast check if the cell meet the requirements.

## Terrain Generation

`Perlin Noise`

Hash function to generate random number

point at similar space should have similar results

value noise, lerp to generate smooth nosie value

perlin noise (gradient noise)

unidorm distributed random number -> hash function

5 order function to smooth the interpolation, because linear or smooth step function, a third order function, both of they have problem of non-continouse property at the borderline, because of the particial derivative(first and second)

`fbm`

`with water?`

min, max height dynamically compute the heigth of water

min + (max - min) * std::abs(min/max)


## Questions

1. why project to the back side of the cube?

to avoid the situation like when we are inside the volume, if we project to the front face, then nothing will be rendered if we are inside the volume.

2. why don't use sdf to determine the traversal step?

3. why choose model space?

- we only get one box in the scene, if we use clip space, most of the ray is not necessary
- it simpilified some calculation

4. explain the theory of bounding box, how did you do the intersection?

in out situation, it's faily simple, because we execute the ray marching in model space, so the cube is range from (0, 1), if it's in world space, then the `box_min` and `box_max` should be stored in actual box position in world space


5. explain the traversal algo work

follow the edge of the voxel

6. we use color pallate to optimize the data, so we only store 1 byte data for each voxel, and we use thses data to select the actual color in the color pallate

7. order of rendering of multiple obj will be a problem

how about the multiple terrain

8. how you calculate the normal?

```c++
hit = fixed_step();
if (isInside(hit.pixel_pos - 0.01 * hit.normal * voxel_size) < 0.5){
    discard;
}
```

`hit.pixel_pos - 0.01 * hit.normal * voxel_size` this is to make the boundary more clear, because some time the hit result is not very accurate, if the hit pos is 1.000001 or something similar to this, it will discard this one, but we want to keep it to make the boundary more sharp, so we move it hit pos back a little bit along the normal direction.


## References

`Videos`:

[Tear down engine technical dive](https://www.youtube.com/watch?v=tZP7vQKqrl8)
[3D Celluar Automata](https://www.youtube.com/watch?v=63qlEpO73C4)

`Papers`:


`Blog`:

[Softopogy's Blog - 3D Celluar Automata](https://softologyblog.wordpress.com/2019/12/28/3d-cellular-automata-3/)

