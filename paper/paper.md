---
title: 'Open Riverine Ecosystem Synthesis (OpenRES): A QGIS plugin for automated extraction of hydrogeomorphic features to support Functional Process Zone classification of river networks'
tags:
  - QGIS
  - PyQGIS
  - riverine ecosystem synthesis
  - functional process zones
  - hydrogeomorphic
authors:
  - name: Jacob Nesslage
    orcid: 0000-0001-9219-8365
    affiliation: 1 
    corresponding: true
  - name: Erin L. Hestir
    orcid: 0000-0002-4673-5745
    affiliation: 1

affiliations:
 - name: Department of Civil and Environmental Engineering, University of California, Merced, Merced, CA, USA
   index: 1

date: 13 Nov 2025
bibliography: paper.bib
---

# Statement of need

Functional Process Zones (FPZs) represent recurring hydrogeomorphic units within river corridors that operate at the reach-to-valley scale. Accurate classification and mapping of these zones are essential for evaluating how spatial variations in hydrogeomorphic structure influence ecological communities and ecosystem functioning within the Riverine Ecosystem Synthesis (RES) framework (@thorp_2006). However, the delineation and classification of FPZs have been constrained by the deprecation of earlier standardized spatial analysis tools (e.g., RESonate for ArcMap; @williams_2013) and by the absence of open-source alternatives capable of extracting the diverse hydrogeomorphic features required for FPZ classification across entire river networks. This limitation has hindered reproducibility and comparability across studies seeking to test or extend the RES framework. OpenRES addresses this gap by providing an open-source, modular, and GUI-accessible QGIS plugin that automates the extraction of key hydrogeomorphic features necessary for FPZ classification. By integrating reproducible methods within a widely adopted open-source GIS platform, OpenRES promotes standardization, accessibility, and scalability in riverine ecosystem analyses.

# Background

Since the publication of the RES in 2006, evidence from rivers across five continents has demonstrated that hydrogeomorphology is a keystone physical driver shaping ecological community composition and ecosystem function. In contrast to the traditional view of rivers as continuous longitudinal gradients of physicochemical conditions (the River Continuum Concept; @vannote_1980), the RES conceptualizes river networks as downstream arrays of large, discrete, and repeating hydrogeomorphic patches formed by interactions among catchment geomorphology, climate, and hydrology (@thorp_2010). Within the RES framework, FPZs are classified at an intermediate spatial scale (typically 5–10 km) between reaches and valleys, using a standardized set of hydrogeomorphic features: elevation, mean annual precipitation, geology class, valley width, valley-floor width, valley-side slopes, down-valley slope, river sinuosity, channel-belt width, and channel-belt sinuosity (@thorp_2010). FPZs characterize spatial variability in river behavior and delineate how each segment mediates flow dynamics, sediment transport, floodplain connectivity, and habitat diversity (@hestir_2007). Because the RES applies to entire river networks and their watersheds, comprehensive classification requires spatially consistent data over large extents. Collecting the data needed is often impractical in the field. Consequently, automated, reproducible spatial tools are essential for extracting and integrating these hydrogeomorphic features across watersheds of interest.

# OpenRES Audience

`OpenRES` is intended for students, instructors, researchers, and practitioners in river science, geomorphology, hydrology, and ecosystem management who use QGIS and need a standardized, open-source tool to delineate FPZs and conduct studies on riverine ecosystems. 

# OpenRES Functionality

## Data Preparation
There are six required datasets needed prior to the extraction of hydrogeomorphic features along a user's watershed of interest using OpenRES in QGIS:

- **Mean annual precipitation** (.geotiff): A rasterized mean annual precipitation layer of the watershed of interest.

- **Digital Elevation Model (DEM) ** (.geotiff): A rasterized digital elevation model of the watershed of interest.

- **Simplified geology** (.shp): A geology polygon layer that contains geologic classification of surficial or underlying geology. Often, this layer is a simplified version of the source geology layer that is classified into bedrock, mixed, or alluvial classes.

- **Geomorphically corrected stream network** (.shp): This is a stream network generated from your DEM, which is then manually corrected to ensure that the stream network follows the course of the river as observed from imagery during the period of interest. 

- **Valley-boundary line layer** (.shp): A line layer delineating both valley-bottom boundaries and the intersecting microsheds or isobasins. This layer is created by (1) delineating the valley bottom using `OpenRES` geomorphology tools or some other method, (2) manually editing outputs to remove holes and ensure realistic extents, (3) generating 1–2 km² microsheds or isobasins from the DEM, and (4) applying vector operations (intersection, difference, polygon-to-line) to extract the combined boundaries of the valley floor and confining valley tops as a line layer.

- **Channel belt layer** (.shp): A line layer delineating the channel belt, defined as the portion of the valley floor occupied or recently influenced by river channel movement, including the active channel and associated depositional features. This layer can be created by (1) using the `Generate Channel Belt` tool provided by `OpenRES` to create offset lines from your stream network at a specified width, and (2) manually edit the channel belt such that it contains the edges of the river meanders and observable depositional features.

## Using OpenRES

### Geomorphology Tools

To aid in producing the **valley-boundary line layer** and the **channel belt layer**, OpenRES provides several geomorphology utilities that can be used prior to data extraction.

-   **Generate Channel Belt**: This tool creates left and right offset lines from the user’s stream network by a user-defined distance, representing the lateral extent of the active or recently active channel zone. These offsets are merged into a single line layer, which should be manually refined by the user to match visible channel-belt boundaries. The output forms the basis for subsequent extraction of channel belt width and sinuosity.

-   **Valley Floor Delineation - Sechu**: This tool implements a modified cost-accumulation approach to identify the valley floor from a DEM (@sechu_2021). The algorithm propagates elevation cost outward from the channel until a threshold in slope is reached, effectively distinguishing low-relief floodplain areas from confining valley walls. The resulting polygon can be used as a starting point to produce the valley-floor boundaries needed for later transect intersection analyses.

### Data Extraction Tools
The core functionality of `OpenRES` is contained in seven data extraction tools, each of which is intended to be used sequentially to automate the extraction of fifteen hydrogeomorphic features.

-   **[1] Generate Transects**: The Generate Transects tool projects a perpendicular transect from the center of each user defined stream segment in a river network to the top of the river valley, as defined by the valley line layer. Transect generation is an iterative process where the tool extends a transect by a user defined amount per iteration per side, counts the number of intersections with the valley-boundary line layer, and does not stop generating until at least two intersections are found on both the left and right side of the river network layer. This tool takes as input a river network layer and a line layer that denotes the boundaries of the valley bottom and adjacent valleys, as well as several numerical inputs to define the distance the transects should extend by per iteration and the maximum distance to extend to (to prevent runaway calculations).

-   **[2] Extract ELE, PRE, and GEO**: The Extract Elevation (ELE), Precipitation (PRE), and Geology (GEO) tool uses the river network layer as the spatial reference, the tool samples elevation (ELE) from a user-specified DEM or elevation raster, mean annual precipitation (PRE) from a gridded climate/precipitation raster, and geology class (GEO) from a polygon geology layer. 

-   **[3] Extract VW, VFW, and RAT**: The Extract Valley Width (VW) and Valley Floor Width (VFW) tool uses the previously generated transects to quantify how wide the river valley is at each stream segment. For each transect, the tool identifies the first left and right intersections with the valley-boundary line layer, then measures the distance between those intersections to derive VFW. The second intersections of each side are used to derive VW. RAT is calculated as the ratio between VW and VFW.

-   **[4] Extract LVS, RVS, and MVS**: The Left Valley Slope (LVS), Right Valley Slope (RVS), and Mean Valley Slope (MVS) tool characterizes valley asymmetry by using the same transect–intersection framework. The tool samples elevation values at the left and right intersection points between the top of the microshed and the valley bottom intersection, then computes RVS and LVS separately for each transect. MVS is calculated by averaging the RVS and LVS.

-   **[5] Extract DVS and SIN**: The Down-Valley Slope (DVS) and River Sinuosity (SIN) tool summarizes along-channel controls on energy and planform. DVS is calculated by linking the start and end point of each river segment and computing the longitudinal slope in the downstream direction. SIN is computed by comparing the actual channel length of a segment to the straight-line distance between its endpoints. 

-   **[6] Extract CBW**: The Extract Channel Belt Width (CBW) tool measures the lateral extent of the active channel system at each stream segment. For each transect, the tool intersects the “Channel Belt” layer and identifies the first left and right intersections relative to the transect midpoint. The distance between these intersections represents the Channel Belt Width (CBW).

-   **[7] Extract LCS, RCS, and CBS**: The Extract Left Channel Sinuosity (LCS), Right Channel Sinuosity (RCS), and Channel Belt Sinuosity (CBS) tool quantifies within-belt planform complexity. For each stream segment, LCS and RCS are computed by tracing the channel on the left and right halves of the channel belt and dividing that curvilinear distance by the corresponding straight-line distance between the starting and ending point of each channel belt segment. CBS is calculated as the mean sinuosity obtained between LCS and RCS.

## After OpenRES: Unsupervised Classification

After executing all seven extraction tools, each river-segment feature contains a complete suite of fifteen hydrogeomorphic features that collectively describe the longitudinal and lateral structure of the river corridor. These standardized metrics enable reproducible FPZ classification and facilitate cross-watershed comparisons. Users can export the resulting river-segment attribute table (or centerline shapefile) to statistical environments such as Python or R for unsupervised hierarchical clustering to delineate FPZ classes (see @maasri_2019; @elgueta_2019). The resulting FPZ clusters can then be joined back to the river network for spatial visualization and further analysis within QGIS. Once FPZs have been classified and mapped, researchers can evaluate the hypotheses posed by the Riverine Ecosystem Synthesis (RES) framework (see @thorp_2023 for the complete list), including relationships among hydrogeomorphic structure, ecological composition, and ecosystem function across multiple spatial scales.

# Acknowledgements
`OpenRES` was developed by members of the Earth Observation and Remote Sensing Laboratory at the University of California, Merced. The authors would like to thank Matthew Rossi, Rachel S. Meyer, Natasha Stavros, Madeline Slimp, and Meghan T. Hayden for their feedback, suggestions, and support during the development of OpenRES.

# References
