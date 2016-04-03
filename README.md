# vectiler

A wavefront obj vector tile builder and exporter based on [mapzen](https://mapzen.com) [vector tiles](https://mapzen.com/projects/vector-tiles).

- build and export mesh in obj files based on tile coordinates (find your tiles of interest [here](http://www.maptiler.org/google-maps-coordinates-tile-bounds-projection/))
- bake ambiant occlusion and save it in an atlas (using [aobaker](https://github.com/prideout/aobaker))

![](http://karim.naaji.fr/images/manhattan-ao.png)

**fetch submodules**

First initialize submodules by running:
```sh
$ git submodule update --init --recursive
```

**install dependecies (OS X)**

aobaker needs [embree](https://embree.github.io/) for optimal ray tracing and tbb, you can install it using [homebrew](http://brew.sh/):

```sh
$ cp 3rdparty/aobaker/embree.rb /usr/local/Library/Formula
$ brew install embree tbb cmake
```

**build**

To build with cmake in a `build/` directory run the following:
```sh
$ cmake . -Bbuild
$ cmake --build build
```

**usage**

```
  Usage: ./objexport [options]

  Options:
    --name                     File name ((null))
    --apikey                   Developer API key (https://mapzen.com/developers/) (vector-tiles-qVaBcRA)
    --splitMeshes              Generate one mesh per feature in wavefront file (0)
    --tilex                    Tile X (can be a tile range: 19294/19295) (19294)
    --tiley                    Tile Y (can be a tile range: 24642/24643) (24642)
    --tilez                    Tile Z (zoom) (16)
    --offsetx                  Global tile Offset on X coordinate (0.0)
    --offsety                  Global tile Offset on Y coordinate (0.0)
    --append                   Append the obj to an existing obj file (0)
    --buildings                Whether to export building geometry (1)
    --buildingsExtrusionScale  Building height scale factor (1.0)
    --terrain                  Generate terrain elevation topography (0)
    --terrainSubdivision       Terrain mesh subdivision (64)
    --terrainExtrusionScale    Terrain mesh extrusion scale (1.0)
    --aoBaking                 Generate ambiant occlusion baked atlas (0)
    --aoAtlasSize              Controls resolution of atlas (512)
    --aoSamples                Number of samples for ambient occlusion (256)
    --version                  Output version
    --help                     Output help
```

**example**

Download one tile with ambiant occlusion baked in a texture:
```sh
./objexport --tilex 19294 --tiley 24642 --tilez 16 --sizehint 512 --nsamples 128
```
Output files are:
- `[tilex].[tiley].[tilez].obj`: a simple mesh containing coordinates and normals
- `[tilex].[tiley].[tilez]-ao.obj`: a mesh containing coordinates and texture uvs for ambiant occlusion rendering
- `[tilex].[tiley].[tilez].png`: an atlas containing the baked ambiant occlusion

Download a _big_ tile, in a file named `manhattan.obj` from a shell script:
```sh
./objexport --name manhattan --tilex 19293/19298 --tiley 24639/24643
```

![](img/vectiler.png)

**build and run the viewer (OS X)**

A minimal viewer using [oglw](https://github.com/karimnaaji/oglw) can be used to preview the obj tiles:

```sh
$ cd renderer
$ cmake . -Bbuild
$ cmake --build build
$ open viewer.app --args ~/dev/obj-export/build/19294.24642.16-ao.obj ~/dev/obj-export/build/19294.24642.16.png
```

