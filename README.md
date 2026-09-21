# Audio Quality WASM

A WebAssembly-based audio quality processing module for browser
applications.

The module provides the following audio quality pipeline:

```text
Audio Blob
   ↓
Audio Quality Check
   ↓
Result
```

The WebAssembly module and JavaScript helper can be integrated into an existing web application by copying the provided files into the appropriate project folders.

---

## Repository Structure

```text
audio-quality-wasm/
│
├── audio-quality-package/
│   ├── audioQuality.js
│   ├── audioQuality.wasm
│   └── audioQualityChecker.js
│
└── react-example/
    ├── public/
    │   ├── audioQuality.js
    │   └── audioQuality.wasm
    │
    ├── src/
    │   ├── components/
    |   |   ├── StoryRecorder.jsx
    │   │   └── StoryRecorder.css
    |   |
    │   └── utils/
    │       └── audioQualityChecker.js
    │
    ├── App.jsx
    ├── main.jsx
    └── ...
```

### `audio-quality-package/`

This folder contains the files that should be copied into the application:

- `audioQuality.wasm` 
- `audioQuality.js`
- `audioQualityChecker.js`

### `react-example/`

This is a React example showing how we can integrate and use the audio quality module.

The example contains copies of the WASM and glue files in `public/` and
the helper file in `src/utils/`.

---

# Integration

Copy the provided WASM files and JavaScript helper into the appropriate locations in the project.

## 1. Copy the WASM Files

Copy these two files from `audio-quality-package/`:

```text
audioQuality.js
audioQuality.wasm
```

Place them in the project's `public/` folder:

```text
your-project/
│
├── public/
│   ├── audioQuality.js
│   └── audioQuality.wasm
│
└── src/
```

The files must be available from the application root because
`audioQualityChecker.js` loads the glue script using:

```js
/audioQuality.js
```

---

## 2. Copy the Audio Quality Checker

Copy:

```text
audioQualityChecker.js
```

into a utility/helper folder in the project, for example:

```text
src/
└── utils/
    └── audioQualityChecker.js
```

The `audioQualityChecker.js` file processes the recorded audio and returns the audio quality result.

---

# Using `checkAudioQuality()`

The main function exposed by `audioQualityChecker.js` is:

```js
checkAudioQuality(audioBlob);
```

It expects an actual JavaScript `Blob` containing the recorded audio.

Example:

```js
import { checkAudioQuality } from "./utils/audioQualityChecker";

const result = await checkAudioQuality(audioBlob);

console.log(result);
```

The `audioBlob` should be the Blob produced by the application's audio
recorder.

---

# Response Structure

The `checkAudioQuality()` function returns a JSON object containing the result of the audio quality checks.

### Successful Response

When the audio passes the quality checks:

```json
{
  "isBlank": false,
  "isNoisy": false
}

---

# Integration Requirements

The application should ensure:

- `audioQuality.js` and `audioQuality.wasm` are present in the
  `public/` folder.
- `audioQualityChecker.js` is copied into the application's
  utility/helper folder.
- The application passes an actual `Blob` to `checkAudioQuality()`.

The helper expects the WASM glue script to be available at:

```text
/audioQuality.js
```

and the Emscripten-generated code will load:

```text
/audioQuality.wasm
```

from the same public location.

---

# React Example

The `react-example/` directory provides a complete reference implementation.

Its relevant structure is:

```text
react-example/
│
├── public/
│   ├── audioQuality.js
│   └── audioQuality.wasm
│
└── src/
    ├── components/
    │   └── StoryRecorder.jsx
    │
    └── utils/
        └── audioQualityChecker.js
```

The example follows the same integration approach described above.

In the recorder component, the helper is imported:

```js
import { checkAudioQuality } from "../utils/audioQualityChecker";
```

After recording stops, the recorded chunks are converted into a Blob:

```js
const blob = new Blob(audioChunksRef.current, {
  type: options.mimeType,
});
```

The Blob is then passed directly to:

```js
const result = await checkAudioQuality(blob);
```

The example handles the returned result and displays the response:

```js
console.log("Audio quality response:", result);

if (result.isBlank) {
  alert("Blank audio detected.");
}

if (result.isNoisy) {
  alert("Noisy audio detected.");
}
```

The `react-example/` directory can be used as the reference
implementation for integrating the same flow into a React project.
