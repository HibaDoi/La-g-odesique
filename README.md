# Geodesic Computation: Direct & Inverse Problem Solver on an Ellipsoid 

## Problem Solved  
- Implemented **Vincenty's geodetic algorithm** to compute **direct and inverse geodesic problems** on an ellipsoid.  
- Given an **initial geographic coordinate, azimuth, and distance**, the solver computes the **destination coordinates and reverse azimuth**.  
- Addresses the **challenge of geodesic curvature** and **non-spherical Earth models** by accounting for **ellipsoidal flattening**.  

## Tech Stack  
- **Programming Language:** Python  
- **Mathematical Models:** Vincenty's formulae, Ellipsoidal Geodesy  
- **Libraries Used:** `math` (trigonometric calculations)  

## Key Contributions  
- **Implemented the direct geodesic problem**, calculating the **final latitude, longitude, and azimuth** after a given distance.  
- **Developed inverse geodesic calculations** to determine the **shortest path between two points** on an ellipsoid.  
- **Optimized the iterative solution** using **Vincenty's convergence method** for greater numerical precision.  
- **Added boundary conditions** to handle **extreme latitude and longitude cases**.  

## Key Features  
✅ **Supports ellipsoidal geodesy calculations**  
✅ **Accurate latitude, longitude, and azimuth outputs**  
✅ **Handles edge cases for extreme latitudes/longitudes**  
✅ **Refines calculations iteratively to minimize errors**  

## Key Results  
✅ **Provides high-precision geodesic computations** with iterative convergence.  
✅ Can be **extended for geospatial applications, surveying, and navigation systems**.  
✅ **Enables accurate pathfinding on Earth's surface**, crucial for **GIS and mapping applications**.  
