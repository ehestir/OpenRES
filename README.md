# Open Riverine Ecosystem Synthesis (OpenRES):

## A QGIS plugin for automated extraction of hydrogeomorphic features to support functional process zone classification of river networks
[![Open Source Love svg1](https://badges.frapsoft.com/os/v1/open-source.svg?v=103)](https://github.com/ellerbrock/open-source-badges/)
[![made-with-python](https://img.shields.io/badge/Made%20with-Python-ffd040.svg)](https://www.python.org/)
[![License: GPL v3](https://img.shields.io/badge/License-GPL%20v3-blue.svg)]([https://www.gnu.org/licenses/old-licenses/gpl-3.0.html](https://www.gnu.org/licenses/gpl-3.0.html#license-text))
[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.17307006.svg)](https://doi.org/10.5281/zenodo.17307006)
[![OpenRES](https://img.shields.io/badge/QGIS%20Repo-OpenRES-589632)](https://plugins.qgis.org/plugins/OpenRES)
[![GitHub release](https://img.shields.io/github/v/release/jollygoodjacob/OpenRES)](https://github.com/jollygoodjacob/OpenRES/releases)
[![GitHub commits](https://img.shields.io/github/commits-since/jollygoodjacob/OpenRES/v1.0.1)](https://github.com/jollygoodjacob/OpenRES/commits)
[![Maintenance](https://img.shields.io/badge/Maintained%3F-yes-green.svg)](https://GitHub.com/jollygoodjacob/OpenRES/graphs/commit-activity)

## Citation

If you use this plugin in your work, please cite it as:

> **Cite:** Nesslage, J., & Hestir, E. (2025). OpenRES (Open Riverine Ecosystem Synthesis): A QGIS plugin for automated extraction of hydrogeomorphic features to support functional process zone classification of river networks (Version 1.0.1) [Software]. Zenodo. <https://doi.org/10.5281/zenodo.17307006>

``` bibtex
@software{nesslage2025OpenRES,
  author       = {Nesslage, Jacob and
                  Hestir, Erin},
  title        = {OpenRES (Open Riverine Ecosystem Synthesis): A
                   QGIS plugin for automated extraction of
                   hydrogeomorphic features to support functional
                   process zone classification of river networks
                  },
  month        = oct,
  year         = 2025,
  publisher    = {Zenodo},
  version      = {v1.0.1},
  doi          = {10.5281/zenodo.17307006},
  url          = {https://doi.org/10.5281/zenodo.17307006}
}
                

```

## General Information

**OpenRES** enables QGIS users to extract up to fifteen physical and environmental features along river segments (typically 5--10 km) to support classification of river networks into **Functional Process Zones (FPZs)**.

**Functional Process Zone (FPZ)** classification is a method used to divide a river network into river valley scale (5-10 km) segments (or "zones") that share similar physical, hydrological, and geomorphic characteristics. Rather than treating a river as a continuous longitudinal gradient of changing physical conditions, FPZ classification recognizes that rivers are composed of a discontinuous set of hydrogeomorphic patches, each shaped by different landscape and hydrologic processes ([Hestir 2007](https://www.researchgate.net/profile/Erin-Hestir/publication/265026989_Functional_Process_Zones_and_the_River_Continuum_Concept/links/546248c00cf2c0c6aec1ade8/Functional-Process-Zones-and-the-River-Continuum-Concept.pdf)). These zones reflect how the river behaves in a given segment, including how it flows, how it transports sediment, how it interacts with its floodplain, and what types of habitats it supports. After classifying a river network in FPZs, research questions posed by the tenets of the Riverine Ecosystem Synthesis hypothesis ([Thorp et al. 2006](https://onlinelibrary.wiley.com/doi/abs/10.1002/rra.901), [Thorp et al. 2023](https://www.frontiersin.org/journals/ecology-and-evolution/articles/10.3389/fevo.2023.1184433/full)) can be explored.

## Data Prerequisites

There are six required datasets needed prior to the extraction of hydrogeomorphic features along a user's watershed of interest using **OpenRES** in QGIS:

-   **Mean annual precipitation** (.geotiff): A rasterized mean annual precipitation layer of the watershed of interest.

-   **Digital Elevation Model (DEM)** (.geotiff): A rasterized digital elevation model of the watershed of interest.

-   **Simplified geology** (.shp): A geology polygon layer that contains geologic classification of surficial or underlying geology. Often, this layer is a simplified version of the source geology layer that is classified into bedrock, mixed, or alluvial classes.

-   **Geomorphically corrected stream network** (.shp): This is a stream network generated from your DEM, which is then manually corrected to ensure that the stream network follows the course of the river as observed from imagery during the period of interest.

-   **Valley-boundary line layer** (.shp): A line layer delineating both valley-bottom boundaries and the intersecting microsheds or isobasins. This layer is created by (1) delineating the valley bottom using `OpenRES` geomorphology tools or some other method, (2) manually editing outputs to remove holes and ensure realistic extents, (3) generating 1--2 km² microsheds or isobasins from the DEM, and (4) applying vector operations (intersection, difference, polygon-to-line) to extract the combined boundaries of the valley floor and confining valley tops as a line layer.

-   **Channel Belt** (.shp): A line layer delineating the channel belt, defined as the portion of the valley floor occupied or recently influenced by river channel movement, including the active channel and associated depositional features. This layer can be created by (1) using the `Generate Channel Belt` tool provided by `OpenRES` to create offset lines from your stream network at a specified width, and (2) manually edit the channel belt such that it contains the edges of the river meanders and observable depositional features.

## Core Functionality

### Geomorphology Tools

To aid in producing the **valley-boundary line layer** and the **channel belt layer**, OpenRES provides several geomorphology utilities that can be used prior to data extraction.

-   **Generate Channel Belt**: This tool creates left and right offset lines from the user's stream network by a user-defined distance, representing the lateral extent of the active or recently active channel zone. These offsets are merged into a single line layer, which should be manually refined by the user to match visible channel-belt boundaries. The output forms the basis for subsequent extraction of channel belt width and sinuosity.

-   **Valley Floor Delineation - Sechu**: This tool implements a modified cost-accumulation approach to identify the valley floor from a DEM (based on methods described by [Sechu et al. 2021](https://www.mdpi.com/2073-4441/13/6/827)). The algorithm propagates elevation cost outward from the channel until a threshold in slope is reached, effectively distinguishing low-relief floodplain areas from confining valley walls. The resulting polygon can be used as a starting point to produce the valley-floor boundaries needed for later transect intersection analyses.

### Feature Extraction Tools

As of this version of the plugin, the core functionality of OpenRES provides up to 15 features that can be used to delineate FPZs from river networks:

-   **Elevation (ELE)**: Elevation value (often in meters), extracted from the center of each stream segment.
-   **Mean Annual Precipitation (PRE)**: Mean annual precipitation value (often in mm), extracted from the center of each stream segment.
-   **Geology (GEO)**: Geology field value, extracted from the center of each stream segment.
-   **Valley Floor Width (VFW)**: Width (in meters) between the first intersections of the transects for each stream segment on the left and right sides of the stream and the valley line layer. When correctly generated, the first intersection of the valley line layer should correspond with the boundaries of the valley floor.
-   **Valley Width (VW)**: Width (in meters) between the second intersections of the transects for each stream segment on the left and right sides of the stream and the valley line layer. When correctly generated, the second intersection of the valley line layer should correspond with tops of hydrologically connected basins that intersect the valley floor, which approximates the tops of valleys.
-   **Ratio of valley width and valley floor width (RAT)**: The ratio between valley width and valley floor width. An indicator of whether a valley is confined/V-shaped or unconfined/U-shaped.
-   **Right Valley Slope (RVS)**: Slope (in %) between the first and second intersection of a transect with the valley line layer on the right side of the river, as defined from a downstream direction. This essentially is the slope between the tops of the valley and the valley bottom on the right side of the river.
-   **Left Valley Slope (LVS)**: Slope (in %) between the first and second intersection of a transect with the valley line layer on the left side of the river, as defined looking downstream. This essentially is the slope between the tops of the valley and the valley bottom on the left side of the river.
-   **Mean Valley Slope (MVS)**: The average lateral valley slope as calculated between the LVS and RVS. An indicator of whether a valley is confined/V-shaped or unconfined/U-shaped.
-   **Down Valley Slope (DVS)**: The slope (in %) between the starting point and endpoint of a given stream segment. An indicator of the stream's gradient/power.
-   **River Sinuosity (SIN)**: The ratio of the true stream distance and the straight line distance between the starting point and endpoint of a given stream segment.
-   **Channel Belt Width (CBW)**: Width (in meters) of the channel belt, as measured by the intersecting transect.
-   **Left Channel Sinuosity (LCS)**: The ratio of the true channel belt distance and the straight line distance between the starting point and endpoint of a given channel belt segment, measured from the left side.
-   **Right Channel Sinuosity (RCS)**: The ratio of the true channel belt distance and the straight line distance between the starting point and endpoint of a given channel belt segment, measured from the right side.
-   **Channel Belt Sinuosity (CBS)**: Average channel belt sinuosity between LCS and RCS, which should approximate the channel belt sinuosity of the river.

These fifteen features (or a subset thereof) can then be used to classify stream networks using unsupervised hierarchical classification methods (see examples in [Thorp et al. 2010](https://books.google.com/books?hl=en&lr=&id=9N9rz8YH_u0C&oi=fnd&pg=PP1&dq=thorp+et+al+2010&ots=eBk_vZsNWA&sig=1XQ2cIyXRnD_t4t1MOtwe6xpQDQ#v=onepage&q=thorp%20et%20al%202010&f=false), [Elgueta et al. 2019](https://onlinelibrary.wiley.com/doi/full/10.1002/rra.3557), [Maasri et al. 2021](https://onlinelibrary.wiley.com/doi/full/10.1002/rra.3784)). The classes obtained using these methods define stream segments that share similar physical, hydrological, and geomorphic characteristics.

### Geomorphology Tools

## Installation

> **Note:** OpenRES requires QGIS version \>=3.28.

**Installation from QGIS:**

- Open QGIS -> Plugins -> Manage and Install Plugins... -> select All tab -> search for OpenRES --> select and install plugin

**Offline installation from .zip file** :

-   Go to releases of this repository -\> select desired version -\> download the .zip file.

-   Open QGIS -\> Plugins -\> Manage and Install Plugins... -\> install from ZIP tab --\> select the downloaded zip --\> install plugin (ignore warnings, if any).

## Example usage: Eerste River catchment, South Africa

> **Note:** All the following processing steps should be done in a sequential manner, following the instructions below. Sample data for hydrogeomorphic feature extraction is provided in [sample_data](/sample_data/) folder.

To demonstrate the use of OpenRES, we have provided a dataset from the Eerste River catchment, a small watershed located in the Greater Cape Floristic Region of South Africa.

::: {style="text-align: center;"}
<img src="imgs/Eerste_watershed.png" alt="Eerste River catchment, South Africa" width="600"/>
:::

The Eerste River originates in the Jonkershoek Mountains, part of the Hottentots-Holland mountain range, and flows westward through the Stellenbosch area before reaching the False Bay coast near Strand. It drains a catchment area of approximately 390 km². Dominated by fynbos vegetation, the area hosts numerous endemic plant species and is under increasing pressure from urban development, invasive species, and agricultural runoff.

------------------------------------------------------------------------

### Prerequisites

Before starting the OpenRES workflow:

-   Ensure all input data are properly prepared:
    -   Stream network (.shp)
    -   Valley boundaries (.shp)
    -   Elevation raster (.tif)
    -   Precipitation raster (.tif)
    -   Geology polygons with a classification field (e.g., `LITH`, `TYPE`, or `GEO`) (.shp)
-   The **OpenRES** plugin is installed and enabled in QGIS.
-   The **Processing Toolbox** is open (via `Processing > Toolbox`).

------------------------------------------------------------------------

### Overview of Extracted Features

The following table summarizes the nine geomorphic and environmental features that will be automatically derived across the Eerste River catchment using the OpenRES tool suite. Each transect, generated perpendicular to the stream network, will be assigned a unique identifier `t_ID`, and the attributes listed below will be extracted or calculated at the transect or segment level. Transects, segment centers, and the river network segments are all linked together by the `t_ID` field, enabling subsequent FPZ classification methods to link using joins and relates to the stream network, river segment centers, or transects as desired for visualization purposes.

| Step | Feature                     | Attribute Code |
|------|-----------------------------|----------------|
| 1    | Transects & segment centers | `t_ID`         |
| 2    | Elevation                   | `ELE`          |
| 2    | Precipitation               | `PRE`          |
| 2    | Geology class               | `GEO`          |
| 3    | Valley Floor Width          | `VFW`          |
| 3    | Valley Width                | `VW`           |
| 3    | Ratio of VW and VFW         | `RAT`          |
| 4    | Left/Right Valley Slope     | `LVS`, `RVS`   |
| 4    | Mean Valley Slope           | `MVS`          |
| 5    | Down-Valley Slope           | `DVS`          |
| 5    | Sinuosity                   | `SIN`          |
| 6    | Channel Belt Width          | `CBW`          |

------------------------------------------------------------------------

### Step 1: Generate Transects

Use: `"[1] Generate Transects"`\
Location: `Processing Toolbox > OpenRES > Feature Extraction`

::: {style="text-align: center;"}
<img src="imgs/generate_transects_window.png" width="800"/>
:::

#### Inputs

-   **River Network Layer** (polyline)
-   **Valley Lines Layer** (line)
-   **Extension Increment** (optional, default = 250m)
-   **Max Length** (optional, default = 50000m)

#### Outputs

-   **Transects** -- Multiline layer across the valley
-   **Segment Centers** -- Points at the center of each transect

#### Notes

-   Each river segment gets a unique `t_ID`.
-   Transects are generated perpendicular to the river segment direction.
-   **Verify** that transects intersect both valley lines properly; occasional mismatches may occur due to geometry errors.
-   Stream network will also be updated to include the `t_ID` field.

------------------------------------------------------------------------

### Step 2: Extract Elevation, Precipitation, and Geology

Use: `"[2] Extract Point Data"`\
Location: `Processing Toolbox > OpenRES > Feature Extraction`

::: {style="text-align: center;"}
<img src="imgs/extract_point_window.png" width="800"/>
:::

#### Inputs

-   **Segment Centers Layer** (from Step 1)
-   **Elevation Raster**
-   **Precipitation Raster**
-   **Geology Polygon Layer**
-   **Geology Field** (attribute from polygon layer, e.g., `GEO` or `LITH`)

#### Output

-   **Segment Centers with ELE, PRE, GEO** -- Updated point layer

#### Notes

-   Elevation and precipitation are sampled directly from rasters.
-   Geology is assigned from intersecting polygon based on the selected field.
-   Missing data (e.g., no polygon overlap or invalid raster) will be filled with fallback values (e.g., -9999 or "No Data").

------------------------------------------------------------------------

### Step 3: Extract Valley Width (VW), Valley Floor Width (VFW), and their ratio (RAT)

Use: `"[3] Extract VW, VFW", and RAT`\
Location: `Processing Toolbox > OpenRES > Feature Extraction`

::: {style="text-align: center;"}
<img src="imgs/extract_vw_window.png" width="800"/>
:::

#### Inputs

-   **Transects Layer** (from Step 1)
-   **Segment Centers Layer** (from Step 2)
-   **Valley Lines Layer**
-   **Stream Network Layer**

#### Outputs

-   **Left/Right VFW Reference Points**
-   **Left/Right VW Reference Points**
-   **Updated Segment Centers** with:
    -   `VFW` -- Valley floor width
    -   `VW` -- Valley width

#### Notes

-   Uses intersection logic to find points where transects intersect valley floor and valley edge.
-   Then calculates distance between these intersections to compute `VFW` and `VW`.
-   Reference points (left/right) are saved as point layers for inspection or QA/QC.

------------------------------------------------------------------------

### Step 4: Extract Side Slopes (LVS, RVS, and MVS)

Use: `"[4] Extract LVS, RVS, and MVS"`\
Location: `Processing Toolbox > OpenRES > Feature Extraction`

::: {style="text-align: center;"}
<img src="imgs/lvs_rvs_window.png" width="800"/>
:::

#### Inputs

-   **Segment Centers Layer** (from Step 3)
-   **Left VW / VFW Reference Points**
-   **Right VW / VFW Reference Points**
-   **Elevation Raster**

#### Output

-   **Segment Centers** updated with:
    -   `LVS` -- Left valley side slope (%)
    -   `RVS` -- Right valley side slope (%)

#### Notes

-   Slopes are computed as the elevation difference between VFW and VW reference points on each side divided by the horizontal distance.
-   All calculations are in percent slope (`rise/run * 100`).
-   Output replaces the segment center layer with updated slope fields.

------------------------------------------------------------------------

### Step 5: Extract Down-Valley Slope and Sinuosity (DVS and SIN)

Use: `"[5] Extract DVS and SIN"`\
Location: `Processing Toolbox > OpenRES > Feature Extraction`

::: {style="text-align: center;"}
<img src="imgs/dvs_sin_window.png" width="800"/>
:::

#### Inputs

-   **Segment Centers Layer** (from Step 4)
-   **Stream Network Layer**
-   **Elevation Raster**

#### Output

-   **Final OpenRES Segment Centers** with:
    -   `DVS` -- Down-valley slope (%)
    -   `SIN` -- Sinuosity (unitless)

#### Notes

-   For each stream segment:
    -   Elevation is sampled at start and end points.
    -   `DVS` is calculated as `(start - end) / length * 100`.
    -   `SIN` is the ratio of actual segment length to straight-line distance.
-   Features with insufficient geometry or elevation data are skipped.

------------------------------------------------------------------------

### Completion

At the end of Step 5, your segment center point layer will contain **all 15 hydrogeomorphic attributes**:

-   `t_ID`, `ELE`, `PRE`, `GEO`, `VFW`, `VW`, `RAT`,`LVS`, `RVS`,`MVS`, `DVS`, `SIN`, `CBW`, `LCS`, `RCS`, `CBS`

::: {style="text-align: center;"}
<img src="imgs/openres_output_table.png" width="800"/>
:::

------------------------------------------------------------------------

## Issues

1)  Report issues or problems with the software here: <https://github.com/jollygoodjacob/OpenRES/issues>

2)  For questions about the OpenRES plugin, contact: [jnesslage\@ucmerced.edu](mailto:jnesslage@ucmerced.edu){.email}

## References

Elgueta, Anaysa, Martin C Thoms, Konrad Górski, Gustavo Díaz, and Evelyn M Habit. 2019. "Functional Process Zones and Their Fish Communities in Temperate Andean River Networks." River Research and Applications 35 (10): 1702--11.

Hestir, Erin L. 2007. "Functional Process Zones and the River Continuum Concept." Center for Watershed Sciences, University of California, Davis, Los Angeles, USA.

Maasri, Alain, James H Thorp, Jon K Gelhaus, Flavia Tromboni, Sudeep Chandra, and Scott J Kenner. 2019. "Communities Associated with the Functional Process Zone Scale: A Case Study of Stream Macroinvertebrates in Endorheic Drainages." Science of the Total Environment 677: 184--93.

Sechu, Gasper L., Bertel Nilsson, Bo V. Iversen, Mette B. Greve, Christen D. Børgesen, and Mogens H. Greve. 2021. "A Stepwise GIS Approach for the Delineation of River Valley Bottom within Drainage Basins Using a Cost Distance Accumulation Analysis." *Water* 13 (6). <https://doi.org/10.3390/w13060827>.

Thorp, James H, Martin C Thoms, and Michael D Delong. 2006. "The Riverine Ecosystem Synthesis: Biocomplexity in River Networks across Space and Time." River Research and Applications 22 (2): 123--47.

Thorp, James H, Martin C Thoms, and Michael D Delong. 2010. The Riverine Ecosystem Synthesis: Toward Conceptual Cohesiveness in River Science. Elsevier.

Thorp, James H, Martin C Thoms, Michael D Delong, and Alain Maasri. 2023. "The Ecological Nature of Whole River Macrosystems: New Perspectives from the Riverine Ecosystem Synthesis." Frontiers in Ecology and Evolution 11: 1184433.

Williams, Bradley S, Ellen D'Amico, Jude H Kastens, James H Thorp, Joseph E Flotemersch, and Martin C Thoms. 2013. "Automated Riverine Landscape Characterization: GIS-Based Tools for Watershed-Scale Research, Assessment, and Management." Environmental Monitoring and Assessment 185: 7485--99.
