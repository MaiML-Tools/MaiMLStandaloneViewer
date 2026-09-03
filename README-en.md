# MaiMLStandaloneViewer

A standalone web application for visualizing and analyzing MaiML (Measurement Analysis Instrument Markup Language) files.

## 🌟 Key Features

### 📊 Graph Visualization
- **Petri net–based workflow visualization**: displays the state transitions of an experimental process as a node graph
  - **Place (circle)**: represents the state of a sample or data
  - **Transition (rectangle)**: represents a process or event
- **Reference relationship visualization** (each type can be toggled on/off in the 🔗 Link panel at the top-left of the canvas):
  - **Template Ref (yellow dashed line)**: reference relationships between templates
  - **Instance Ref (green dashed line)**: reference relationships between instances
  - **Chain Link (orange dashed line)**: chain (`<chain>`) relationships between different MaiML files, including hash-based integrity verification
  - **Parent Link (purple dashed line)**: visualization of chain (`<parent>`) relationships with the revision source file
  - **Same UUID: Template (blue-violet dashed line)**: correspondence between Template nodes that share the same UUID
  - **Same UUID: Instance (light purple dashed line)**: correspondence between Instance nodes that share the same UUID

### 📄 Metadata Management and Display
- **Document Info panel**: unified display of author, UUID, description, and vendor information
- **Detail panel**: displays related template/instance information for the selected node
- **Template/instance hierarchy view**: automatically extracts the relevant data when a node is selected

### 📁 File Support
- **`.maiml` file support**: full support for the MaiML standard format
- **`.zip` archive support**: automatically links MaiML files inside a ZIP with their associated images/data
- **Embedded file preview**: choose the display format from 🖼️ Image / 📊 Chart / 📋 Table / 📝 Text / 📈 EMSA Spectrum
  - Direct display of images (JPG, PNG, TIFF, etc.)
  - Table display of CSV/text data
  - Multi-page TIFF support
  - Display of EMSA/MAS spectrum format

### 🎨 UI/UX Features
- **Light/dark theme support**: seamless switching via CSS variables
- **Responsive design**: horizontal/vertical resizers to adjust panel sizes
- **Canvas toolbar**: three panels at the top-left of the graph area - 🔗 Link / ⚙ View / 📷 Image (only one opens at a time; settings are saved in the browser and restored on next launch)
- **Graph operations**:
  - Zoom with the mouse wheel (0.25× to 6×)
  - Pan by dragging
  - Manual node placement (Drag Edit in the ⚙ View panel)

### 🎯 Layout Features
- **Force-directed layout** (✧ Auto): automatic placement based on a physics model
- **Dagre hierarchical layout** (⌗ Dagre): automatic hierarchical arrangement
- **Reset placement** (⟲ Reset)
- **Grid display/snap**: Show Grid / Snap to Grid in the ⚙ View panel. The grid spacing can be changed between 10 and 100 px

### 📷 Petri Net Diagram Image Export
The **📷 Image** panel at the top-left of the canvas exports the Petri net diagram as an image. **The export always covers the whole graph, regardless of the current zoom/pan position.**

- **Copy to Clipboard**: copy as a PNG image (paste directly into Word / PowerPoint)
- **Save as PNG**: save as a PNG file
- **Save as SVG (vector)**: save in vector format. It does not degrade when scaled, making it suitable for figures in papers and documents
- **Options**:
  - **Scale**: PNG output scale (1x-4x, default 2x)
  - **Background**: `Theme` (the current theme color, default) / `White` / `Transparent`
  - **Include grid**: include grid lines in the image
- ℹ️ Copying to the clipboard works in Chrome / Edge. If the browser does not support it or it fails, it automatically falls back to saving a PNG file
- ℹ️ In the dark theme the text is white, so choosing `Transparent` makes the text invisible when pasted onto a white document
- ℹ️ Fonts in the image are replaced with the environment's standard fonts, so it may differ slightly from the on-screen display

### 🤖 AI Analysis Features
- **Cloud / local LLM switching**: AI summarization of detailed data
  - **☁️ Cloud (Gemini)**: integrates with the Google Gemini API; high accuracy and speed, but data is sent to Google's servers
  - **🖥️ Local (Ollama)**: uses a local LLM (Ollama) to summarize without sending data externally (requires Ollama to be installed)
- **Multiple prompt templates**:
  - Quick Summary
  - Standard Summary
  - Detailed Report
  - Custom Prompt (user-defined)
- **Results shown in Markdown format**, with save/copy functionality

### 📊 Data Plotting Features
- **Three chart types**:
  - **Line**: shows time-series trends
  - **Bar**: shows a histogram
  - **Scatter**: shows correlation analysis
- **Interactive controls**: zoom, pan, and reset
- Integrated with Chart.js + a chart zoom plugin

### 📈 Metadata Export
Supports exporting data in multiple formats:
- **Excel (.xlsx)**: listed by sheet structure
- **JSON (.json)**: raw data preserving the hierarchical structure
- **CSV (.csv)**: for use with general-purpose tools

### 🔐 XML Encrypted Data Support (JIS K0200 compliant)
- **AES-256-GCM + PBKDF2-SHA256 decryption support**: compliant with W3C XML Encryption 1.1
- **Encryption banner notification**: shown automatically when encrypted data is detected
- **Batch decryption**: decrypt all elements at once with the "Decrypt All" button
- **Individual decryption option**: decrypt node by node, step by step
- **Password caching**: automatically reused during the session
- ⚠️ **Limitation**: only the password-based method is supported (RSA/key-wrap methods are not supported)

### 🛢️ Data Lake (bulk management of a local folder)
- **Folder connection**: connect an entire local folder; recursively scans and lists `.maiml` / `.maiml.zip` files, including those in subfolders
- **Two-layer search**:
  - **Index** (for narrowing only) - holds file name, relative path, date, instrument, creator, vendor, owner, and UUIDs
  - **Direct full-text scan** - covers arbitrary keywords such as property keys/values and descriptions, so terms absent from the index still match
- **Filters**: measurement date/time range (From / To) and Instrument / Creator / Vendor / Owner checkboxes, combined with the keyword as an AND condition
- **Sorting**: sort by date (newest/oldest first), file name, or file size
- **Bulk loading**: select filtered files and load multiple files into the viewer at once
- **Cross-session cache**: caches the folder index in `localStorage` for instant restoration on next launch; use "Sync / Re-Verify" to rescan and reconnect
- ℹ️ **Supported browsers**: Chrome / Edge (uses the File System Access API). Firefox / Safari fall back to limited functionality
- Folder access is read-only (no files are modified or deleted)
- ⚠️ **This is a simple search intended for small-scale use**, with the following deliberate trade-offs:
  - If the total size to scan exceeds 200 MB, the scan is not run automatically; a warning and a button are shown instead
  - ZIP files are loaded into memory in full to be expanded, so those larger than 64 MB are excluded
  - Scan results are not cached, so changing the keyword re-reads the files
  - Building the index (the first time a folder is opened) is far more expensive than a full-text scan - on the order of minutes for several hundred files

### 🔏 XML Signature Verification (ds:Signature)
- **Automatic signature integrity verification**: when loading a MaiML file signed per the W3C XML Signature standard, the viewer automatically verifies that the file has not been altered since it was signed
- **No fixed algorithm**: the algorithm is selected automatically from `ds:SignatureMethod`, so **both RSA-SHA256 and ECDSA-SHA256 can be verified** (MaiML Studio outputs RSA-SHA256)
- **Verification result badge**: shown at the top of the Document Metadata panel in the upper right
  - 🔏 **Verifying...** — verification in progress while the file is loading
  - ✅ **Integrity OK** — the signature and hash match the file contents. The signature algorithm name and the public key fingerprint (first 4 bytes) are shown alongside
  - ❌ **Integrity check failed** — a mismatch was detected in the hash or signature value
  - ⚠️ **Unsupported signature format** — a signature format this viewer cannot interpret (for example, the `dsig11:ECKeyValue` key info produced by older MaiML Studio versions). **The file itself may well be intact, and this is deliberately distinguished from tampering**
- **Key fingerprint**: the first 4 bytes of the SHA-256 of the key material in `ds:KeyInfo`. Because it does not depend on the key format, it lets you visually match "several files signed with the same key"
- ⚠️ **Note**: this verification only checks integrity ("has it been altered since signing?") — it does not prove the signer's authenticity ("who signed it"), since the public key is bundled within the file itself

## 🚀 How to Use

### Installation and Launch
1. Download `MaiMLStandaloneViewer.html`
2. Open it directly in your browser (no server required)
3. Or serve it from any web server

### Basic Operations

#### Loading Files
Load files using the **📂Upload Files…** button in the top-left of the screen, or by dragging and dropping onto the screen.

- **Supported formats**: `.maiml` files and `.zip` files containing them
- **ZIP support**: when you upload a ZIP file, the MaiML files inside are automatically linked with their image/data files and expanded
- If you want to manage a local folder in bulk, the **🛢️ Data Lake** feature is also available (Chrome/Edge only; see "Data Lake" above for details)

#### Graph Operations

| Operation | Method |
|------|------|
| Pan | Drag on an empty area |
| Zoom | Mouse wheel (0.25× to 6×) |
| Move a node | Drag each node (circle or rectangle) (enable/disable with **Drag Edit** in the ⚙ View panel) |
| Auto-adjust layout | **✧ Auto** (force-directed model) or **⌗ Dagre** (hierarchical) button in the header |
| Reset placement | **⟲ Reset** button in the header |
| Grid display/snap | **Show Grid** / **Snap to Grid** in the ⚙ View panel (spacing adjustable from 10 to 100 px with +/-) |
| Toggle relationship lines | The six checkboxes in the 🔗 Link panel (templateRef / instanceRef / chain / parent / same uuid:Template / same uuid:Instance) |
| Export the diagram as an image | The 📷 Image panel (see "Petri Net Diagram Image Export" above) |
| Toggle light/dark | **🌙 Theme** button in the header |

> The dot next to the 🔗 Link panel's label shows whether anything is hidden: **lit (a green ●) means all relationship lines are visible**, while **unlit (a hollow ○) means some types are hidden**. Because this is visible even when the panel is collapsed, it prevents the mistake of leaving lines hidden and later reading the diagram as if those relationships did not exist. Hovering over the dot shows how many types are hidden.
