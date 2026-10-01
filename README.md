# Audio Quality WASM

A WebAssembly-based audio quality processing module for browser applications.

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

The WASM files must be available from the application's public path.

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

## 3. Preload the WASM Module

The WASM module should be preloaded during application/component initialization, before calling `checkAudioQuality()`.

Use `preloadWasm()` during the React component initialization. See the React Example section for the implementation.


> **Note:** If the application is deployed under a subpath, update the
> `audioQuality.js` path in `audioQualityChecker.js` accordingly.
>
> ```js
> await loadScript("/audioQuality.js", "AudioQualityModule");
> ```
>
> For Vite applications, use:
>
> ```js
> await loadScript(
>   `${import.meta.env.BASE_URL}audioQuality.js`,
>   "AudioQualityModule"
> );

---

# Using `checkAudioQuality()`

The main function exposed by `audioQualityChecker.js` is:

```js
checkAudioQuality(audioBlob);
```

It expects an actual JavaScript `Blob` containing the recorded audio.

Example:

```js
const result = await checkAudioQuality(audioBlob);

console.log(result);
```

The `audioBlob` should be the Blob produced by the application's audio recorder.

> **Note:** Make sure preloadWasm() has completed before calling checkAudioQuality(). 
> See the React Example section for the recommended initialization flow.

---

# Response Structure

The `checkAudioQuality()` function returns a JSON object containing the result of the audio quality checks.

### Successful Response

When the audio passes the quality checks:

### Valid Audio (Not blank or noisy)
```json
{
  "isBlank": false,
  "noSpeech": false,
  "isNoisy": false
}
```

### Blank Audio (No sound)

```json
{
  "isBlank": true,
  "noSpeech": false,
  "isNoisy": false
}
```

### No Speech (No significant speech)

```json
{
  "isBlank": false,
  "noSpeech": true,
  "isNoisy": false
}
```

### Noisy Audio (Note: This check needs at least 7 seconds of audio to return a decision. Any audio below this duration will always return "isNoisy": false)

```json
{
  "isBlank": false,
  "noSpeech": false,
  "isNoisy": true
}
```

### Error Response

If an error occurs while processing the audio, the function returns an error response:

```json
{
  "error": "Error message"
}
```

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
import { checkAudioQuality, preloadWasm } from "../utils/audioQualityChecker";
```

The WASM module is preloaded once when the React component is mounted:

```js
useEffect(() => {
  let mounted = true;

  preloadWasm()
    .then(() => {
      if (mounted) {
        setWasmReady(true);
      }
    })
    .catch((error) => {
      console.error("WASM preload failed:", error);

      if (mounted) {
        setWasmReady(false);
      }
    });

  return () => {
    mounted = false;
  };
}, []);
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
const result = await checkAudioQuality(blob);

if (result.error) {
  throw new Error(result.error);
}

if (result.isBlank) {
  // Handle blank audio
  return;
}

if (result.isNoisy) {
  // Handle noisy audio
  return;
}

if (result.noSpeech) {
  // Handle audio with no significant speech
  return;
}
```

The `react-example/` directory can be used as the reference implementation for integrating the same flow into a React project.
