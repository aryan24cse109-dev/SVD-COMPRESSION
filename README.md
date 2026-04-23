# SVD Image Compressor

<div align="center">

[![Live Demo](https://img.shields.io/badge/▶_Live_Demo-GitHub_Pages-7c6dfa?style=for-the-badge&logo=github)](https://aryan24cse109-dev.github.io/SVD-COMPRESSION/)
[![JavaScript](https://img.shields.io/badge/Vanilla_JS-ES6+-fa6d8c?style=for-the-badge&logo=javascript&logoColor=white)](https://developer.mozilla.org/en-US/docs/Web/JavaScript)
[![Math](https://img.shields.io/badge/Jacobi_SVD-from_scratch-6dfad0?style=for-the-badge)](https://en.wikipedia.org/wiki/Jacobi_eigenvalue_algorithm)
[![License](https://img.shields.io/badge/License-MIT-green?style=for-the-badge)](LICENSE)

**Interactive, browser-native image compression using Singular Value Decomposition**  
Built entirely in Vanilla JS — no backend, no libraries, no build step.

[Try the live demo →](https://aryan24cse109-dev.github.io/SVD-COMPRESSION/)

</div>

---

## What It Does

Upload any image and compress it in real time using **rank-k matrix approximation** via SVD. Drag the slider to choose how many singular values to keep — from aggressive compression (k=5) to near-lossless (k=max). The entire SVD pipeline runs in your browser, including:

- Per-channel Jacobi eigendecomposition on AᵀA
- Live reconstruction as you drag the slider
- PSNR quality metric and compression ratio
- Error map visualisation (normalised difference heatmap)
- Lossless PNG download at exact selected quality

---

## The Math

Any image channel (m×n matrix) can be factored via SVD:

$$A = U \cdot \Sigma \cdot V^T$$

Keeping only the top-k singular values gives the **best rank-k approximation** (Eckart–Young theorem):

$$A_k = U_k \cdot \Sigma_k \cdot V_k^T \quad \text{where } \|A - A_k\|_F \text{ is minimised}$$

**Compression ratio:**

$$\text{ratio} = \frac{m \times n}{k \times (1 + m + n)}$$

**Quality metric (PSNR):**

$$\text{PSNR} = 20 \cdot \log_{10}\!\left(\frac{255}{\sqrt{\text{MSE}}}\right) \quad \text{dB}$$

A PSNR above ~30 dB is considered perceptually good quality.

---

## Algorithm: Jacobi SVD from Scratch

The browser has no `numpy.linalg.svd`. This app implements the full pipeline:

```
1. Extract R, G, B channel matrices  (Float64Array)
2. For each channel:
   a. Compute AᵀA  (n×n symmetric)
   b. Jacobi iterations → orthogonal V, eigenvalues λᵢ
   c. Singular values σᵢ = √λᵢ,  sort descending
   d. Recover U columns: uᵢ = A·vᵢ / σᵢ
3. For slider value k:
   reconstruct = Σ(i=0..k-1) σᵢ · uᵢ · vᵢᵀ
4. Clamp to [0,255], write to ImageData
```

The same pipeline in NumPy (for reference):

```python
import numpy as np
from PIL import Image

img = np.array(Image.open("photo.jpg").convert("RGB"), dtype=np.float64)

def svd_compress(channel, k):
    U, S, Vt = np.linalg.svd(channel, full_matrices=False)
    return np.clip(U[:, :k] @ np.diag(S[:k]) @ Vt[:k, :], 0, 255)

k = 30
compressed = np.stack([svd_compress(img[:,:,c], k) for c in range(3)], axis=2)

# Quality metrics
mse = np.mean((img - compressed) ** 2)
psnr = 20 * np.log10(255.0 / np.sqrt(mse))
ratio = (img.shape[0]*img.shape[1]) / (k*(1+img.shape[0]+img.shape[1]))
print(f"PSNR: {psnr:.1f} dB | Compression: {ratio:.1f}×")
```

---

## Features

| Feature | Detail |
|---|---|
| 📤 **Image Upload** | PNG, JPG, WEBP — drag & drop or click, up to 800×800 |
| 🎚️ **Rank-k Slider** | Real-time reconstruction as you drag — full range k=1 to min(W,H) |
| 📊 **4 Live Metrics** | Compression ratio, PSNR (dB), energy retained %, bytes saved |
| 📉 **SV Spectrum** | Singular value bar chart — see which components are kept/discarded |
| 📈 **PSNR Curve** | Quality vs k chart with live position marker |
| 🔍 **Error Map** | Normalised blue→red heatmap of reconstruction error |
| 🎨 **RGB & Grayscale** | SVD applied per-channel (RGB) or on luminance (grayscale) |
| ⬇️ **HQ Download** | Lossless PNG, fresh full-resolution reconstruction at exact k value |
| 🖼️ **Demo Images** | Three synthetic test images (portrait, landscape, texture) |
| 📱 **Mobile-friendly** | Responsive layout, touch-optimised slider, high-quality scaling |

---

## Project Structure

```
SVD-COMPRESSION/
├── index.html          ← Entire app — all CSS, JS, SVD math inline (no build)
├── README.md           ← This file
├── notebook.ipynb      ← Python/NumPy equivalent implementation
├── report.pdf          ← Full mathematical writeup
├── requirements.txt    ← Python deps for notebook
└── results/            ← Sample compression outputs
    ├── error_maps.png
    ├── psnr_vs_k_curves.png
    ├── singular_value_spectrum.png
    └── *_compression_grid.png
```

---

## Run Locally

No install needed — just open the file:

```bash
git clone https://github.com/aryan24cse109-dev/SVD-COMPRESSION
cd SVD-COMPRESSION

# Option 1 — direct open
open index.html          # macOS
start index.html         # Windows
xdg-open index.html      # Linux

# Option 2 — local server (recommended)
python -m http.server 8000
# → http://localhost:8000
```

For the Python notebook:

```bash
pip install -r requirements.txt
jupyter notebook notebook.ipynb
```

---

## Tech Stack

| Layer | Detail |
|---|---|
| Language | Vanilla JavaScript (ES6+, no framework) |
| Math | Custom Jacobi SVD — zero libraries |
| Rendering | HTML5 Canvas API + ImageData |
| Styling | CSS3, CSS custom properties, responsive grid |
| Fonts | Space Mono + Syne (Google Fonts) |
| Deployment | GitHub Pages (auto-deploy from `main`) |

---

## Key Concepts

- **Singular Value Decomposition** — matrix factorisation, U·Σ·Vᵀ
- **Low-rank approximation** — Eckart–Young–Mirsky theorem
- **Jacobi eigendecomposition** — iterative algorithm for symmetric matrices
- **Information compaction** — energy in the singular value spectrum
- **PSNR / MSE** — standard image quality metrics
- **HTML5 Canvas API** — pixel-level image manipulation in the browser

---

## References

- Golub, G. H., & Van Loan, C. F. (2013). *Matrix Computations* (4th ed.). Johns Hopkins University Press.
- Eckart, C., & Young, G. (1936). The approximation of one matrix by another of lower rank. *Psychometrika*, 1(3), 211–218.
- [NumPy linalg.svd](https://numpy.org/doc/stable/reference/generated/numpy.linalg.svd.html)
- [Wikipedia: Singular Value Decomposition](https://en.wikipedia.org/wiki/Singular_value_decomposition)

---

## Author

**Aryan Agarwal** · [GitHub](https://github.com/aryan24cse109-dev)

---

<div align="center">
  <sub>MIT License · Deployed on GitHub Pages</sub>
</div>
