# HDF5 Viewer

A professional, browser-based inspection tool for **HDF5 (.h5)** files. This utility allows users to explore file hierarchies, view metadata (attributes), and visualize complex datasets instantly without any server-side processing.

try it out at my [test page](http://www.basvanetten.nl/hdf5)

## Key Features
* **Privacy First:** Files are processed locally via WebAssembly. Your data never leaves your computer.
* **High Performance:** Uses `uPlot` and custom decimation logic to render datasets with millions of points smoothly.
* **LabVIEW Compatibility:** Advanced logic to parse LabVIEW-specific compound timestamps and clusters.
* **Interactive Graphics:** Zoom, pan, and manually edit axis limits by double-clicking the axis gutters.
* **Dark Mode:** Automatic system detection with a manual toggle.

---

## User Manual

### Getting Started
1. **Open the App:** Open the `index.html` file in any modern web browser.
2. **Environment Note:** For the WASM library to load correctly, it is recommended to serve the file via a local server (e.g., `npx serve`, Python `http.server`, or Live Server in VS Code) rather than using the `file://` protocol.
3. **Load Data:** Drag and drop an `.h5`, `.hdf5`, or `.he5` file into the sidebar or click the "Open" icon.
4. **Navigate:** Use the tree view in the left sidebar to explore the internal structure of the file.

### Interacting with Data
* **Visualizing:** Click the checkbox next to a 1D dataset to add it to the graph.
* **Inspecting:** Click the **Table Icon** next to a dataset name to open a modal containing a raw JSON preview and a tabular view of the first 1,000 rows.
* **Metadata:** Click any group or dataset name in the tree to view its specific HDF5 attributes in the bottom-left properties panel.

### Chart Controls
* **Zoom:** Click and drag on the chart area to create a zoom box.
* **Reset Zoom:** Double-click anywhere inside the chart area.
* **Edit Axes:** Double-click the X or Y axis gutters to manually enter a min/max value.
* **Toggle Autoscale:** Right-click an axis gutter to return it to automatic scaling.

---

## Maintainer Manual

### Architecture
The application is a "Single File Application" (SFA) using vanilla JavaScript (ESM) and modern CSS frameworks.

* **Logic:** Managed in a single `<script type="module">`.
* **Data Parsing:** Powered by **h5wasm**, which provides the C++ HDF5 library compiled to WebAssembly.
* **Rendering:** Powered by **uPlot**, a memory-efficient canvas-based charting library.
* **Styling:** Uses **Tailwind CSS** (via CDN) for UI components and layouts.

### Key Dependencies
| Library | Purpose | Source |
| :--- | :--- | :--- |
| **h5wasm** | Core HDF5 file reading and WASM filesystem management. | `cdn.jsdelivr.net/npm/h5wasm` |
| **uPlot** | High-performance canvas charting. | `cdn.jsdelivr.net/npm/uplot` |
| **Tailwind CSS** | Responsive UI and Dark Mode styling. | `cdn.tailwindcss.com` |

### Key Code Sections
* **The Decimator:** To prevent browser lag on massive datasets, `downsampleSeries()` implements a min-max decimation algorithm that reduces the data to roughly twice the horizontal pixel width of the container while preserving outliers.
* **Compound Parser:** `tryParseLabviewCompound()` is a specialized function that looks for specific HDF5 structures used by National Instruments' LabVIEW, specifically the `{seconds, fractions}` timestamp cluster.
* **WAVEFORM Logic:** The app automatically looks for attributes like `t0`, `dt`, or `WAVEFORM_T0` to reconstruct time-series axes from flat arrays.

### Known Limitations
* **Memory:** While the viewer uses decimation for plotting, the initial file read requires enough browser memory to hold the WASM-virtualized file (roughly the size of the `.h5` file).
* **2D/3D Data:** Currently optimized for 1D time-series. Multi-dimensional data is viewable in the Table Inspection modal but not yet rendered as a heatmap.

---

## License
This project is open-source and available under the MIT License.
