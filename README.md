# OBS WebApp <img width="32" alt="obs-logo" src="https://github.com/user-attachments/assets/59380d99-f2a7-4a4d-a681-aa66762bdc4c" />


<img width="1621" height="903" alt="screenshot of the main OBS Web interface featuring Studio Mode" src="https://github.com/user-attachments/assets/e63b0b06-1e38-4bee-a6a6-28c89e3ba7df" />

This OBS WebApp is a fully browser-based live video compositing, recording, and non-linear editing (NLE) suite. Inspired by OBS Studio, this WebApp brings recording and other production tools directly into your browser. It requires zero backend servers and zero installations it utilizes HTML5 Canvas, Web Audio APIs, MediaRecorder technologies and more.. 

---

## ✨ Core Features & Capabilities

### 🎛️ Advanced Studio Mode
Split your workflow:
* **Preview & Program Panes:** Stage your upcoming scene in the "Preview" window (Left Screen). Make live adjustments, resize sources, or fix typos silently. When ready, push it to the "Program" window (Right Screen) for the live audience.
* **Transition Engine:** A comprehensive suite of hardware-accelerated transitions including Cut, Fade, Slide, Zoom In, Spin, Melt, Fall Apart, and Circle Reveal.
* **Interactive T-Bar:** Manually scrub through transitions between the Preview and Program scenes with a fully functional vertical T-Bar and Quick Transition preset buttons (e.g., Fade to Black).

![Studio Mode active with the T-Bar transition column highlighted in the middle](https://github.com/user-attachments/assets/0b8efb06-6220-4b95-aea9-38cbc282dfe2)

### 🖼️ High-Performance Canvas & Compositing
A custom, dual-canvas rendering pipeline ensures liquid-smooth 60 FPS performance.
* **Interactive Bounding Boxes:** Every visual source features a red interactive bounding box. Drag, drop, scale, and rotate elements pixel-perfectly on the canvas.
* **Nested Scenes:** Treat entire scenes as individual sources. Build complex overlays or camera setups once, and nest them infinitely into other scenes.
* **White to Transpant:** Because web browsers restrict websites from manipulating cross-origin iframes (like pulling pixel data to make it transparent), the engine uses an CSS compositing trick. By applying specific mix-blend-modes, it mathematically forces pure white backgrounds to become 100% completely transparent, letting the canvas elements beneath show through flawlessly.
* **Browser Scaling:** When scaling a Browser Source, the engine does not just resize the iframe's internal bounding box. Dragging the red canvas handles instantly recalculates and adjusts the internal `Zoom` property. The iframe sits precisely anchored to its center point, scaling uniformly inside the selection box without breaking CSS layouts.

![adjusting the red boundary box of a Browser Source, demonstrating the perfect scale and transparent background](https://github.com/user-attachments/assets/d0d35210-8f31-4ad3-9027-e3241857b8cd)


### 📡 Extensive Source Support
Bring virtually any media format into your broadcast.
* **Browser Sources:** Create sources via a sleek modal (defaulting to obsproject.com/browser-source). Features dynamic switching between URLs and Local Files, full-width resolution inputs, Custom CSS injection, and OBS permission dropdowns. 
* **Media & VLC Sources:** Drop in local videos or audio files. Features include looping, playback speed sliders, hardware decode toggling, YUV range settings, and FFmpeg option fields.
* **Display & Window Capture:** Directly capture your desktop, specific application windows, or browser tabs natively.
* **GDI+ Style Text Engine:** Highly customizable text sources supporting custom fonts, read-from-file, multi-line alignments, vertical text stacking, dynamic gradients, background opacities, and stroke outlines.
* **Image & Slideshows:** Auto or manual slideshow playback, crossfade/slide transitions, random looping, and bounding box constraints. Image sources support flip, opacity, and blend modes (Add, Screen, Multiply, Overlay).

<img width="1124" height="605" alt="Screenshot of the detailed Properties Window for a Text (GDI+) and Browser Source" src="https://github.com/user-attachments/assets/e175d2d7-211b-43e6-a6f0-612149b4aebd" />

&nbsp;
<table>
  <tr>
    <td width="70%">
      <h3>🎚️ Professional Audio Mixer (Web Audio API)</h3>
      <p>A complete digital audio workstation built directly into the UI.</p>
      <ul>
        <li><strong>Real-Time VU Meters:</strong> Liquid-smooth, dual-channel audio meters with accurate, color-coded clipping indicators (Green/Yellow/Red) and decibel value displays.</li>
        <li><strong>Dynamic Audio Routing:</strong> Every single source has a dedicated <code>GainNode</code> and <code>AnalyserNode</code>, routed mathematically to a master output to prevent peaking, distortion, and phasing. Nested scene audio is correctly funneled into the parent scene's mix.</li>
        <li><strong>Advanced Audio Filters:</strong> Click the triple-dot context menu on any audio track to access a massive suite of professional filters. Includes active UI panels for 3-Band Equalizers, Compressors, Expanders, Limiters, Noise Gates, Noise Suppression, Invert Polarity, and Upward Compressors.</li>
      </ul>
    </td>
    <td width="30%" align="center">
      <img src="https://github.com/user-attachments/assets/6afe4344-6c4d-4a98-9806-e89a18276fa7" width="200" alt="Audio Mixer Interface Preview">
    </td>
  </tr>
</table>

### ✂️ Integrated Non-Linear Video Editor (NLE)
AsWell as recording you can edit your footage immediately without ever leaving the web app (though there is no audio in the editor due to browser security, but it will export with audio).
* **Timeline:** A scrollable timeline with a moving playhead, track headers, and zoom capabilities.
* **Clip Management:** Split clips instantly at the playhead, delete segments, and drag clip edges (trim handles) to trim footage dynamically.
* **Interactive Crop Tool:** Select a clip and open the Crop Overlay. Drag the blue box and resize the corners over your video; clicking "Apply Crop" calculates normalized coordinates and mathematically crops the video source directly on the canvas without re-encoding.
* **Media Library:** Import external media or automatically drop your fresh recordings right into the project bin for immediate editing.
* **Full-Screen Render Overlay:** When you hit "Export Final Video", the screen dims and a massive, un-hideable progress bar appears with a Cancel button. The engine mathematically scans all timeline tracks, finds the exact millisecond the last clip ends, and renders the final WebM file frame-by-frame.

<img width="1332" height="912" alt="the Recordings and Editor modal, highlighting the multi-track timeline, the media library, and the interactive crop tool" src="https://github.com/user-attachments/assets/deddc2cf-0a5a-47aa-a9bb-be2a73e8b32f" />


---

## 🧠 Under the Hood: Technical tricks & Performance Hacks

OBS WebApp relies on several browser tricks to maintain desktop-level rendering performance:

* **The GPU Wake Trick (Muted Playback):** Browsers violently throttle background or hidden video elements to save RAM. To bypass this, the engine forces videos into a permanently muted, auto-playing state inside a hidden DOM container. The browser never throttles it, keeping hardware acceleration fully engaged and allowing the engine to violently scrub and draw video frames to the canvas perfectly.
* **Decoupled DOM Updates:** UI state updates are entirely decoupled from the visual render loop. This ensures your CPU dedicates 100% of its processing power to drawing video arrays, keeping the UI snappy and the recording butter-smooth.
* **Drift Correction Engine:** The preview mode features a custom 0.25-second drift-tolerance algorithm. If your computer momentarily hiccups under heavy load, it gracefully catches up rather than triggering an endless, freezing buffering loop.
* **Shift-Guard Protection:** Destructive actions and file downloads (like deleting a scene or downloading raw blobs) are protected by specific state checks and Shift-Guard modifiers to prevent accidental data loss during a live session.

<img width="602" height="304" alt="Screenshot of the Stats Window showing 60FPS, Render Times, Disk Space, and CPU usage" src="https://github.com/user-attachments/assets/a3db3d37-60ee-4163-8b0c-675eb820952a" />


## 🎨 UI, UX & Aesthetics

The interface is meticulously crafted to make OBS Studio veterans feel right at home while looking incredibly sleek.
* **Authentic Splash Screen:** Launching the app triggers an authentic WebM video splash screen. It features a deep vignette shadow and a progress bar with a custom "stuttering" algorithm to simulate a heavy, authentic software asset load.
* **Modular, Draggable Docks:** The entire workspace is modular. Tear off the Scenes, Sources, Controls, or Mixer docks using the clone icon. Drag them around the screen, snap them into new orders, or pop them out into floating, resizable windows.
* **Dark Theme Integration:** A soothing, high-contrast dark mode using deep grays, OBS blues, and precise 1px borders for maximum readability during long streams.
* **Global Modals:** Fully draggable properties windows, transitions menus, and settings pages that act and feel exactly like native desktop OS windows.

<img width="925" height="716" alt="Screenshot showing a popped-out modular dock floating over the main interface, alongside the Add Source context menu" src="https://github.com/user-attachments/assets/27101b0f-4bc9-4fa4-b660-89621f5bf566" />

---

## 🛠️ Usage & Setup

Because this application relies entirely on client-side browser technologies (HTML, CSS, Vanilla JS), setup is completely frictionless.

1. Clone or download the repository.
2. Ensure all files (`index.html`, `style.css`, `script.js`) are in the same directory.
3. Host the folder on any basic static web server (e.g., VSCode Live Server, GitHub Pages, or Nginx).
4. Open the URL in a modern desktop browser (Google Chrome or Microsoft Edge highly recommended for optimal MediaRecorder and Hardware Encoding support).
5. Start creating!

## 📜 Disclaimer
This project is not affiliated with, endorsed by, or sponsored by the OBS project. All UI designs, references, and aesthetics are utilized under fair use for parody and historical recreation purposes.
