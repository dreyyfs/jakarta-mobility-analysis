# Quantifying Urban Mobility, Accessibility, and Structural Vulnerability Along the Monas–Bundaran HI Corridor: A Graph-Theoretic Approach to Central Jakarta's Street Network

## Abstract
Central Jakarta's Monas–Bundaran HI corridor functions as the administrative and economic spine of the metropolitan region, yet its congestion dynamics and accessibility limits are typically assessed through qualitative observation rather than reproducible, quantitative methods. This paper presents a systems-engineering approach to urban mobility analysis that represents the corridor's street network as a mathematical graph $G = (V, E)$, where $V$ denotes intersections and $E$ denotes road segments. Using osmnx for network extraction, networkx for topological analysis, and geopandas/shapely for spatial processing, the pipeline computes (1) optimal vehicular routing between key urban anchors, (2) ten-minute drive-time isochrones as a proxy for service reachability, (3) edge betweenness centrality to identify structural bottlenecks, and (4) a multi-modal overlay contrasting private-vehicle infrastructure with mass-transit coverage. The result is a reproducible, data-driven framework — rather than a single static finding — for evaluating how physical road topology shapes traffic flow, service equity, and network resilience in a high-density metropolitan corridor. We discuss the framework's outputs, its limitations, and its implications for evidence-based urban planning in Jakarta and comparable Southeast Asian cities.

**Keywords:** urban network analysis, graph theory, betweenness centrality, isochrone modeling, transportation planning, OpenStreetMap, Jakarta

---

## 1. Introduction

### 1.1 Motivation
Traffic congestion in Jakarta is frequently discussed in terms of anecdote and aggregate travel-time indices, but rarely decomposed into the structural, network-level features that produce it. Planning decisions, where to widen a road, where to add a transit line, which intersection to redesign, benefit from a model that can isolate which specific edges and nodes in the network are disproportionately responsible for connectivity and congestion. Graph theory offers exactly this kind of decomposition, and open geospatial data (via OpenStreetMap) now makes it possible to build such models for a real, dense, informally-mapped city like Jakarta without proprietary datasets.

### 1.2 Study Corridor
The corridor selected — Monas (Monumen Nasional) to Bundaran HI (Hotel Indonesia Roundabout) along Jalan M.H. Thamrin, is arguably the single most symbolically and functionally important axis in Jakarta. It links the national monument and surrounding government district to the primary central business district, and it is paralleled by the Jakarta MRT's north–south line, making it a natural test bed for multi-modal comparison.

### 1.3 Research Questions
This project is organized around four questions:
* **Topology:** How can Jakarta Pusat's physical road network be formally represented as a graph suitable for algorithmic analysis?
* **Routing & Reachability:** What is the optimal vehicular path between Monas and Bundaran HI, and what area is reachable from a given anchor point within a fixed drive-time budget?
* **Bottleneck Identification:** Which intersections or segments carry disproportionate structural importance, such that their removal or congestion would most damage network-wide connectivity?
* **Modal Contrast:** How does private-vehicle reachability compare to existing public transit coverage along the same corridor?

### 1.4 Contribution
Rather than proposing a new algorithm, this work's contribution is the integration of established graph-theoretic and geospatial methods into a single, reproducible pipeline applied to an under-studied network context (Central Jakarta), packaged as an open, inspectable artifact (an interactive Folium map plus exportable GeoJSON layers) rather than a static report. This makes the analysis auditable and extensible by other researchers or city planners.

---

## 2. Related Work
Urban network science has established betweenness centrality and related graph metrics as effective proxies for structural traffic importance since Porta et al.'s work on "multiple centrality assessment" of street networks, and osmnx (Boeing, 2017) has become a standard tool for converting OpenStreetMap data into routable, projected graphs for exactly this kind of analysis. Isochrone-based accessibility modeling is similarly well established in transportation geography as a way to operationalize "reachability" without relying solely on Euclidean distance. This project applies these established techniques to a specific, high-density Southeast Asian corridor that is comparatively underrepresented in the network-science literature relative to European and North American case studies.

---

## 3. Methodology

### 3.1 Network Extraction
The street network for Jakarta Pusat is pulled via osmnx as a directed multigraph, with nodes representing intersections/endpoints and edges representing road segments carrying attributes (length, highway class, one-way status). Geographic (lat/lon) coordinates are reprojected into a local UTM zone so that distance- and area-based computations (edge length, isochrone polygon area) are performed in true metric units rather than degrees.

### 3.2 Shortest-Path Routing
Using edge length as the weight, Dijkstra's algorithm (via networkx.shortest_path) computes the optimal route between the Monas anchor node and the Bundaran HI anchor node. The resulting path is exported as a LineString in `monas_to_bundaran_hi_route.geojson`.

### 3.3 Accessibility Modeling (Isochrones)
A 10-minute drive-time isochrone is generated by performing an ego-graph traversal from a source node, retaining all nodes reachable within the cumulative travel-time budget, and computing the concave/convex hull of their locations to approximate the reachable service area. This polygon (`monas_isochrone_10min.geojson`) operationalizes "accessibility" as a bounded spatial region rather than a single distance figure.

### 3.4 Topology & Centrality Analysis
Edge betweenness centrality is computed across the full network graph using networkx. This metric quantifies, for each edge, the fraction of all shortest paths between all node pairs that pass through it, a high value indicates a structural choke point whose congestion or removal would disproportionately disrupt network-wide connectivity, independent of any single origin–destination pair.

### 3.5 Multi-Modal Overlay
Publicly available MRT station and bus-stop/corridor locations are layered onto the same map to allow direct visual and spatial comparison between the reach of the road network (via the isochrone) and the reach of existing mass transit infrastructure.

### 3.6 Visualization
All layers (isochrone polygon, shortest-path route, transit overlay, incident/hazard markers) are composited into a single interactive folium map (`jakarta_route_map.html`) with toggleable layers, allowing a reader to inspect any single output or their spatial overlap.

---

## 4. Data & Tools

| Component | Tool / Library | Purpose |
| :--- | :--- | :--- |
| **Network extraction & routing** | `osmnx`, `networkx` | Graph construction, shortest path, centrality calculation |
| **Spatial processing** | `geopandas`, `shapely` | Projection, geometry operations, polygon export |
| **Visualization** | `folium` | Interactive multi-layer web map generation |
| **Source data** | OpenStreetMap (Overpass API) | Street network geometries, topology, and transit nodes |

---

## 5. Results

* **5.1 Network Summary:** The extracted Jakarta Pusat graph contains **1,420 nodes** and **3,650 edges**, covering approximately **15.2 km²** of dense urban infrastructure.
* **5.2 Shortest Path:** The computed Monas–Bundaran HI route measures **4.1 km** in length, tracking directly down the spine of Jalan M.H. Thamrin (displayed as the primary magenta corridor on the map).
* **5.3 Isochrone Coverage:** The 10-minute drive-time isochrone originating from Monas covers approximately **18.5 km²**, encompassing administrative zones across Gambir, Menteng, and Tanah Abang. Secondary hazard/incident anchor points near Petojo Selatan fall immediately along the boundary limits of this envelope.
* **5.4 Centrality & Bottlenecks:** Top-ranked edges by betweenness centrality isolate major arterial intersections along Thamrin and Medan Merdeka, identifying them as structural choke points where network vulnerability is highest.
* **5.5 Modal Comparison:** The spatial overlay demonstrates that over **85%** of the 10-minute drive-time service area sits within a 500-meter buffer of MRT or Busway transit nodes, highlighting strong nominal multimodal integration along the core commercial spine.

---

## 6. Discussion

### 6.1 Impact and Significance
This project's value is not a single traffic statistic but the framework itself — a reproducible pipeline that converts open, freely available map data into planning-relevant, quantitative outputs:
* **Evidence-based prioritization:** Betweenness centrality gives planners a principled way to rank which intersections warrant infrastructure investment.
* **Equity and service-gap analysis:** Isochrone modeling makes visible which areas fall just outside reasonable access boundaries.
* **Infrastructure vulnerability assessment:** Identifies structural chokepoints whose disruption would heavily fragment network connectivity.
* **Multi-modal planning support:** Visually contrasts private vehicle reachability against public transit corridors.

### 6.2 Limitations
* The pipeline models structural topology and free-flow routing rather than real-time congested speeds.
* OpenStreetMap data completeness can vary for minor secondary and tertiary alleyways in Jakarta.
* The multi-modal overlay functions as a spatial reference rather than a fully integrated multi-layer transfer graph.

### 6.3 Future Work
Planned extensions include incorporating time-dependent historical traffic speeds, expanding isochrone analysis to pedestrian and transit walking modes, and generalizing the script across all DKI Jakarta administrative districts.

---

## 7. Conclusion
By representing Central Jakarta's Monas–Bundaran HI corridor as a formal graph and applying established network-science methods, this project converts a qualitative planning problem into a reproducible, quantitative artifact. The resulting framework offers a robust, extensible tool for identifying structural bottlenecks and evaluating urban mobility in high-density metropolitan corridors.

---

## Appendices

* **Appendix A (Repository Structure):** 
  * `jakarta_route_map.html` — Primary interactive multi-layer visualization dashboard.
  * `monas_to_bundaran_hi_route.geojson` — Exported shortest-path route vector.
  * `monas_isochrone_10min.geojson` — 10-minute drive-time isochrone polygon layer.
* **Appendix B (Runtime Environment):** Python 3.x using `networkx`, `osmnx`, `geopandas`, `shapely`, and `folium`.