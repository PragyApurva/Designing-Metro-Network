# Metro Line and Station Planning
Designing a optimal metro layout for minimizing cost of construct and maximizing the utility for the City of Delhi
### [Presentation](https://docs.google.com/presentation/d/1fXRUyP1170q0n0C6NAv4aY4aHkEdTgCn88-ijSAk_Gg/edit#slide=id.g2474ec3135d_2_0)


This project focuses on optimizing metro station placement and designing metro lines to maximize transportation utility while minimizing construction costs in Delhi. The solution leverages **population density data** from the Kontour dataset, which divides the city into hexagonal grids for better spatial analysis.

## 📊 Data Overview
- **Dataset**: Kontour Population Density Dataset
  - The dataset uses hexagonal grids instead of square grids to ensure equal distance between neighboring cells, simplifying spatial analysis.
  - Focused on Delhi by filtering longitude and latitude coordinates to the Delhi region.
  - Each hexagon contains data on population density and coordinates, allowing precise station placement.

## 🛠️ Methodology
### 1. **Station Selection**
- **Goal**: Select 250 optimal metro station locations from a pool of nodes based on population density.
- **Node Value Calculation**:
  - For each node (hexagon center), we calculated a value based on population within its vicinity using the formula:
    $$
    n_i = \int_A e^{-\frac{r^2}{\sigma^2}} \rho dA
    $$
  - We discretized this integral using:
    $$
    \text{Value} = \text{mean}\left(e^{-(0.95)^2} \times P_a\right) + \text{mean}\left(e^{-(1.90)^2} \times P_b\right)
    $$
    - `0.95 km` is the distance to immediate neighbors (first circle).
    - `1.90 km` is the distance to the second layer of neighbors.
- **Station Filtering**:
  - Nodes are sorted in a **priority queue (max-heap)** based on their value.
  - Selected nodes are added to the metro network unless they are within `1.5 km` of an already selected station to avoid clustering.

### 2. **Metro Line Construction**
- **Goal**: Connect the selected stations efficiently while minimizing construction cost and travel time.
- **Initialization**:
  - Start with one random station, adding it to an initialized graph `new_G`.
- **Line Optimization**:
  - For each station in `new_G`, find its nearest 5 neighbors (using BFS for shortest paths).
  - Calculate the cost ($C_{ij}$) and time ($T_{ij}$) for potential connections:
    $$
    C_{ij} = \lambda_c \times D_{ij}, \quad T_{ij} = \frac{(P_i + P_j)}{2}
    $$
  - Use the optimization function to determine the best connection:
    $$
    F_{ij} = \frac{1}{C_{ij}} + \frac{\lambda_t}{T_{ij}}
    $$
  - Modified the optimization function to prevent far connections skewing results:
    $$
    V_{ij} = \left(1 - \frac{D_{ij}}{\text{total\_distance\_sum}}\right)^2 \times F_{ij}
    $$
- **Result**:
  - Added pairs $(i, j)$ to the network until all 250 nodes are connected, ensuring a fully connected metro graph.

## 🔄 Optimization Function
- **Objective**: Maximize the function to optimize station connectivity and minimize cost:
  $$
  f_{\text{obj}} = \sum_i n_i + \sum_i \sum_j n_{gij} \rightarrow \text{optmax}
  $$
  - $n_i$: Population demand satisfied by station $i$.
  - $n_{gij}$: Population demand from generators (stadiums, airports, etc.) served by station $i$.


## 🗺️ Results and Comparison
- **Accuracy**: The generated metro layout achieved a **68% match** with the existing Delhi metro network based on Euclidean distance.
- **Challenges**:
  - Initially used geodesic calculations for distances but switched to **BFS-based shortest paths** to reduce errors in line construction.
  - Improved the north-south connectivity by adding extra segments based on shortest paths.

## 🚀 Future Enhancements
- **Extending the Network**: Assign line colors and expand existing metro lines for optimal city coverage as population density increases.
- **Parallel Route Planning**: Use game theory to plan routes in areas with overlapping bus and metro services.
- **Dynamic Population Handling**: Adapt the model to account for future changes in population density, ensuring continued efficiency.

## 📚 References
- Path-based algorithms for metro network design
- Kontur Population Dataset
- Relevant research on optimization and transportation network design.

## 🖥️ Contributors
- Gautam Bansal
- Apurva Pragya
- Shivam Kapadia
- Under the guidance of **Dr. Arvind Gupta**

