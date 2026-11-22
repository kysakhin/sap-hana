# Module 2: Graph Viewer

Open: Fiori Launchpad (`http://<host>:<port>/`) → Curriculum #6 - Graph Processing → **Graph Viewer**.
Select workspace **GBI_GRAPH.GBI_GRAPH_DEMO** in the dropdown.

## Task 1: Explore the graph and use the filter
**PDF:** `01_Introduction_to_Graph_Processing_Chapter_01_Introduction_to_Graphs_Exercise_en` (curriculum 6), Exercises 1 and 2

1. Taxonomy tab (opens first): click **COUNTRY** to see the pie chart of countries. Click **X** to close.
2. Click **Visualize Graph** (top right). The graph shows all vertices and edges.
3. **Toggle Vertices Key** shows the keys inside the vertices.
4. Click a vertex or an edge to see its details in the left popup.
5. Drag to move the graph and use the mouse wheel to zoom. **Center Graph** resets the view.
6. **Filter** tab → **Add Filter Condition** → `TYPE` `=` `Supplier` → OK → **Apply Filter**.

## Task 2: Single Source Shortest Path (rubber to finished tire)
**PDF:** `03_Introduction_to_Graph_Processing_Chapter_02_Graph_Processing_on_SAP_HANA_Exercise_en` (curriculum 6), Exercise 3

1. **Visualize Graph** → open the **Graph Interaction** tab on the right.
2. Tab **Single Source Shortest Path**:
   - Start Vertex: `RBR00`
   - Target Vertex: `TYRE1000`
3. Click **Execute**. The path is coloured in the graph and listed in the Result tab.
4. Click a vertex in the Result tab to highlight it. **Clear Color** resets.

## Task 3: Neighborhood Search (Touring Seat Kit)
**PDF:** same PDF, Exercise 4

1. **Visualize Graph** → Graph Interaction → **Neighborhood Search**.
2. Start Vertex: `TRSK1000`, Direction: **incoming**.
3. Click **Execute** (default depth is 1000). The Result tab lists the components and their distance, and they are coloured in the graph.

## Task 4: Pattern Matching (suppliers in China)
**PDF:** same PDF, Exercise 5

1. **Visualize Graph** → click the **Pattern Editor** button (top right).
2. Select vertex **V1** → add operation: `TYPE` (VARCHAR) `=` `Supplier` → **Add**.
3. Select vertex **V2** → add operation: `COUNTRY` (VARCHAR) `=` `China` → **Add**.
4. Click **Execute**. The result subgraphs and the result table show the components shipped from China.
