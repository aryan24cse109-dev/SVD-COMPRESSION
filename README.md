# 🗜️ SVD Image Compression

> **IIT Kharagpur Summer Research Internship Project**
> Built by **Garishma** · Linear Algebra × Image Processing · NumPy

[![Live Demo](https://img.shields.io/badge/Live%20Demo-GitHub%20Pages-7c6dfa?style=flat-square&logo=github)](https://yourusername.github.io/svd-compression)
[![Made with](https://img.shields.io/badge/Made%20with-Vanilla%20JS%20%2B%20HTML-fa6d8c?style=flat-square)](https://developer.mozilla.org/en-US/docs/Web/JavaScript)
[![Math](https://img.shields.io/badge/Math-Singular%20Value%20Decomposition-6dfad0?style=flat-square)](https://en.wikipedia.org/wiki/Singular_value_decomposition)
[![License](https://img.shields.io/badge/License-MIT-green?style=flat-square)](LICENSE)

---

## 📌 Overview

This project demonstrates **image compression using Singular Value Decomposition (SVD)** — a foundational technique in linear algebra. By decomposing an image matrix into three components (U, Σ, Vᵀ), we can reconstruct a visually similar image using only the top-k singular values, drastically reducing data size while preserving perceptual quality.

The app runs **entirely in the browser** — no backend, no server, no NumPy install required. The full SVD pipeline (Jacobi eigendecomposition) is implemented from scratch in vanilla JavaScript.

$$A \approx U_k \cdot \Sigma_k \cdot V_k^T$$

---

## 🌐 Live Demo

👉 **[Try it here](https://github.com/aryan24cse109-dev/SVD-COMPRESSION)**

Upload any image or try the built-in demo images (portrait, landscape, texture) and drag the rank-k slider to explore compression in real time.

---

## ✨ Features

| Feature | Description |
|---|---|
| 📤 **Image Upload** | PNG, JPG, WEBP — up to 512×512 (auto-resized) |
| 🎚️ **Rank-k Slider** | Live compression — drag to see quality change instantly |
| 🔢 **Preset buttons** | Quick-set k = 5, 10, 20, 50, 100 |
| 🎨 **RGB & Grayscale** | SVD applied per-channel or in grayscale mode |
| 📊 **4 Live Metrics** | Compression ratio, PSNR (dB), energy retained %, bytes saved |
| 📉 **SV Spectrum** | Singular value bar chart (green = used, gray = discarded) |
| 📈 **PSNR Curve** | Quality vs k chart with live marker |
| 🔍 **Error Map** | Difference image (amplified ×5) to visualise information loss |
| ⬇️ **Download** | Export compressed image as PNG |
| 🖼️ **Demo Images** | Three synthetic test images built-in |

---

## 🧮 The Math

### SVD Decomposition

Any real matrix **A** of size m×n can be factored as:

```
A = U · Σ · Vᵀ
```

where:
- **U** (m×m) — left singular vectors (orthogonal)
- **Σ** (m×n) — diagonal matrix of singular values in descending order
- **Vᵀ** (n×n) — right singular vectors (orthogonal)

### Rank-k Approximation

Keeping only the top-k singular values gives the **best rank-k approximation** (by Eckart–Young theorem):

```
A_k = U_k · Σ_k · Vₖᵀ
```

### Compression Ratio

```
Original size  = m × n  (pixels)
Compressed size = k × (1 + m + n)  (storing U_k, Σ_k, Vₖᵀ)

Compression ratio = (m × n) / (k × (1 + m + n))
```

### Quality Metric — PSNR

Peak Signal-to-Noise Ratio (higher = better):

```
PSNR = 20 · log₁₀(255 / √MSE)
```

A PSNR above ~30 dB is generally considered good visual quality.

### Algorithm Used

The SVD is computed via **Jacobi eigendecomposition** on AᵀA:
1. Compute AᵀA (n×n symmetric matrix)
2. Run Jacobi iterations to diagonalise → get eigenvalues and V
3. Singular values σᵢ = √λᵢ
4. Recover U columns: uᵢ = A·vᵢ / σᵢ

---

## 🗂️ Project Structure

```
svd-compression/
│
├── index.html          ← Entire app (self-contained, no dependencies)
├── README.md           ← This file
└── LICENSE             ← MIT License
```

The app is a **single self-contained HTML file** — all CSS, JavaScript, and SVD math is inline. No build step, no npm, no framework.

---

## 🚀 Run Locally

No installation needed. Just open the file:

```bash
# Clone the repo
git clone https://github.com/aryan24cse109-dev/SVD-COMPRESSION
cd svd-compression

# Open in browser (any of these)
open index.html                        # macOS
start index.html                       # Windows
xdg-open index.html                    # Linux

# Or serve locally
python -m http.server 8000
# Then visit http://localhost:8000
```

---

## 🔬 Python Equivalent (NumPy)

The browser app mirrors this Python/NumPy pipeline exactly:

```python
import numpy as np
from PIL import Image
import matplotlib.pyplot as plt

# Step 1 — Load image as float matrix
img = np.array(Image.open("photo.jpg").convert("L"), dtype=np.float64)
# img.shape → (512, 512)

# Step 2 — SVD decomposition
U, S, Vt = np.linalg.svd(img, full_matrices=False)
# U: (512, 512), S: (512,), Vt: (512, 512)

# Step 3 — Keep only top-k singular values
def compress(U, S, Vt, k):
    return U[:, :k] @ np.diag(S[:k]) @ Vt[:k, :]

# Step 4 — Reconstruct
k = 20
reconstructed = compress(U, S, Vt, k)

# Step 5 — Visualise
fig, (ax1, ax2) = plt.subplots(1, 2, figsize=(12, 5))
ax1.imshow(img, cmap='gray'); ax1.set_title('Original')
ax2.imshow(np.clip(reconstructed, 0, 255), cmap='gray')
ax2.set_title(f'Compressed (k={k})')
plt.tight_layout()
plt.savefig('comparison.png', dpi=150)

# PSNR
mse = np.mean((img - np.clip(reconstructed, 0, 255)) ** 2)
psnr = 20 * np.log10(255.0 / np.sqrt(mse))
print(f"PSNR: {psnr:.2f} dB")

# Compression ratio
orig_size = img.shape[0] * img.shape[1]
comp_size = k * (1 + img.shape[0] + img.shape[1])
print(f"Compression ratio: {orig_size/comp_size:.1f}x")
```

---

## 📸 Screenshots

| Original | k=5 | k=20 | k=50 |
|---|---|---|---|
| Full quality | High compression | Balanced | Near-original |
| — | ~10× smaller | ~4× smaller | ~2× smaller |

---

## 📚 Key Concepts Covered

- **Singular Value Decomposition** — core matrix factorisation technique
- **Low-rank matrix approximation** — Eckart–Young–Mirsky theorem
- **Information theory** — energy compaction in singular value spectra
- **Image processing** — pixel matrix operations, channel decomposition
- **Numerical methods** — Jacobi iteration for symmetric eigenproblems
- **Quality metrics** — MSE, PSNR, compression ratio

---

## 🛠️ Tech Stack

| Layer | Technology |
|---|---|
| Language | Vanilla JavaScript (ES6+) |
| Math | Custom Jacobi SVD — no libraries |
| Rendering | HTML5 Canvas API |
| Styling | CSS3 with custom properties |
| Fonts | Space Mono + Syne (Google Fonts) |
| Deployment | GitHub Pages / Vercel / Netlify |

---

## 📖 References

- Golub, G. H., & Van Loan, C. F. (2013). *Matrix Computations* (4th ed.). Johns Hopkins University Press.
- Eckart, C., & Young, G. (1936). The approximation of one matrix by another of lower rank. *Psychometrika*, 1(3), 211–218.
- [NumPy linalg.svd documentation](https://numpy.org/doc/stable/reference/generated/numpy.linalg.svd.html)
- [Wikipedia: Singular Value Decomposition](https://en.wikipedia.org/wiki/Singular_value_decomposition)

---

## 👩‍💻 Author

**Garishma**
Summer Research Intern · IIT Kharagpur
Project: NumPy / Pandas / Matplotlib Internship

---

## 📄 License

This project is licensed under the MIT License — see [LICENSE](LICENSE) for details.

---

<div align="center">
  Made with ♥ for IIT Kharagpur Summer Research Internship
</div>
