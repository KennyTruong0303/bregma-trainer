# Bregma Bench

Bregma Bench is a browser-based trainer for locating the bregma and lambda landmarks on a C57BL/6J mouse skull under a simulated surgical microscope view.

The skull scene is generated procedurally in the browser, so each session can present different suture paths, vessels, tissue, hair, moisture, and surgical noise. The trainer scores landmark placement against the generated reference and reports the error in micrometres.

## Features

- Quiz and exploration views
- Easy, medium, and hard guidance levels
- Procedural skull variants with coronal, sagittal, and lambdoid sutures
- Saline, swab, press, and marker tools that change the view
- Zoom, pan, fit-to-view, and touch/pinch support
- Bregma/lambda scoring, timing, pass threshold, and best-fit explanation
- Browser-local progress storage and CSV export
- No build step or runtime dependency

## Run locally

This is a self-contained static page. Open `index.html` directly in a modern browser, or serve the project with any static web server:

```bash
python -m http.server 8000
```

Then open <http://localhost:8000>.

The page loads IBM Plex fonts from Google Fonts when online; the app itself does not require a backend.

## Basic workflow

1. Choose **Quiz** and a difficulty.
2. Use **Saline** to improve visibility, then **Swab** to bring the surface to the clearest tacky state.
3. Use **Press** to inspect the plate seams.
4. Select **Bregma** and click the landmark, then select **Lambda** and click the landmark.
5. Submit the attempt to see the landmark errors, measured distance, elapsed time, and fitted-reference explanation.

Use **Explore** when you want to inspect the generated skull without completing a scored attempt.

## Controls

| Action | Control |
| --- | --- |
| Place a landmark or use a tool | Click and drag on the scope field |
| Pan | Select **Pan**, or right/middle-drag |
| Zoom | Mouse wheel, pinch, zoom slider, or `+`/`−` |
| Reset the view | **Fit** |

## Project structure

The application currently lives in a single file:

- `index.html` — markup, styling, procedural rendering, interaction logic, scoring, and local progress handling

## License

No license has been assigned yet.
