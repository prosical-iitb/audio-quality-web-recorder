# React Example

This folder contains a React application that demonstrates how to
integrate and use the Audio Quality WASM module.

The example provides a simple audio recording interface. After a
recording is stopped, the recorded audio is passed to the audio quality
checker, which processes the recording and returns the audio quality
result.

## Example Flow

```text
Start Recording
      ↓
Record Audio
      ↓
Stop Recording
      ↓
Create Audio Blob
      ↓
Audio Quality Check
      ↓
Display Result
```

## Project Structure

```text
react-example/
│
├── public/
│   ├── audioQuality.js
│   └── audioQuality.wasm
│
├── src/
│   ├── components/
│   │   ├── StoryRecorder.jsx
│   │   └── StoryRecorder.css
│   │
│   ├── utils/
│   │   └── audioQualityChecker.js
│   │
│   ├── App.jsx
│   ├── index.css
│   └── main.jsx
│
├── package.json
├── vite.config.js
└── ...
```

### `public/`

Contains the WASM module and the Emscripten-generated JavaScript glue
code:

```text
audioQuality.js
audioQuality.wasm
```

These files are loaded by `audioQualityChecker.js`.

### `src/components/`

Contains the recorder UI and its styling.

- `StoryRecorder.jsx` --- React audio recorder component.
- `StoryRecorder.css` --- Styling for the recorder interface.

### `src/utils/`

Contains:

- `audioQualityChecker.js` --- Utility used to run the audio quality
  check.

### `App.jsx`

The main React application component.

### `main.jsx`

The entry point that renders the React application.

---

# Prerequisites

Make sure the following are installed:

- Node.js
- npm

You can verify the installations with:

```bash
node --version
npm --version
```

---

# Setup

Navigate to the `react-example` directory:

```bash
cd react-example
```

Install the project dependencies:

```bash
npm install
```

---

# Run the Application

Start the Vite development server:

```bash
npm run dev
```

Vite will display the local development URL in the terminal, for
example:

```text
http://localhost:5173/
```

Open the displayed URL in a browser.
