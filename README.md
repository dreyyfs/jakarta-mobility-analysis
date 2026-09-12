# Jakarta Pusat Urban Mobility & Spatial Analysis

Spatial network analysis and routing pipeline evaluating traffic flow, accessibility, and structural bottlenecks along the Monas – Bundaran HI corridor in Central Jakarta.

## Overview
This repository contains a Python-based geospatial data pipeline that models urban infrastructure as a mathematical graph. The project evaluates vehicular routing efficiency, service reachability via drive-time polygons, public transit integration, and network-wide traffic choke points using graph theory metrics.

## Methodology & Pipeline
1. **Network Extraction:** Pulls street network geometries and topological nodes for Jakarta Pusat using `osmnx` and transforms distances into projected local UTM coordinates.
2. **Shortest-Path Routing:** Computes optimal vehicular paths between critical urban anchors (Monas to Jalan M.H. Thamrin / Bundaran HI).
3. **Accessibility Modeling:** Generates 10-minute drive-time isochrones to map spatial service coverage boundaries.
4. **Topology & Centrality Analysis:** Computes **betweenness centrality** across network edges using `networkx` to mathematically identify high-traffic arterial intersections and structural bottlenecks.
5. **Multi-Modal Overlay:** Integrates public transport data (MRT and bus transit nodes) along the corridor.

## Tech Stack
* **Language:** Python 3.x
* **Graph & Network Theory:** `networkx`, `osmnx`
* **Spatial Processing:** `geopandas`, `shapely`
* **Visualization:** `folium` (Interactive HTML Web Mapping)

## Repository Structure
* `jakarta_route_map.html` — The primary interactive multi-layer visualization dashboard.
* `monas_to_bundaran_hi_route.geojson` — Exported shortest-path route geometry.
* `monas_isochrone_10min.geojson` — 10-minute drive-time polygon layer.
