# Zeiss LSM 900 Confocal Microscope #2 — Training Notes

**Location:** Furth Building, Room F1116, City of Hope  
**Software:** ZEN 3.8 (ZEN system)  
**Date:** April 22, 2026

---

## 1. Instrument Overview

The Zeiss LSM 900 is a laser scanning confocal microscope (LSM = Laser Scanning Microscope) equipped with two acquisition modes:

- **Confocal mode** — standard point-scanning with a physical pinhole and PMT detectors
- **Airyscan mode** — super-resolution mode using a 32-unit hexagonal detector array (SR = Super Resolution)

---

## 2. Core Principle: Confocal Microscopy

### 2.1 Why Confocal?

In conventional widefield fluorescence microscopy, the entire sample thickness is illuminated simultaneously. Out-of-focus fluorescence above and below the focal plane enters the detector, causing image blur. Confocal microscopy solves this by rejecting out-of-focus light through a **pinhole**.

### 2.2 Light Path

```
Laser → Objective → Sample focal plane (fluorescence generated)
                          ↓
                   Fluorescence returns
                          ↓
               Dichroic mirror (separates excitation/emission)
                          ↓
                    Pinhole ← in-focus light passes through
                          ↓       out-of-focus light is blocked
                  Detector (PMT)
```

The pinhole is optically conjugate to the objective focal point — this is the origin of the term **"con"focal**.

### 2.3 Point-by-Point Scanning

Unlike widefield microscopy, confocal imaging acquires signal **one point at a time** by scanning the laser across the sample. Each point is detected individually and assembled into a complete image. This enables high contrast and clean background, at the cost of slower acquisition speed.

### 2.4 Stokes Shift: Excitation vs. Emission Color

Fluorophores always emit light at a **longer wavelength** than the excitation laser (lower energy). This shift is called the **Stokes shift**.

| Fluorophore | Excitation | Emission | Laser color | Image color |
|-------------|------------|----------|-------------|-------------|
| DAPI | 405 nm | ~461 nm | Violet | Blue |
| Alexa 488 / FITC | 488 nm | ~519 nm | Blue | Green |
| Alexa 555 / TRITC | 555 nm | ~570 nm | Green | Orange-red |
| Alexa 647 / Cy5 | 647 nm | ~668 nm | Red | Deep red |

> **Note:** When observing through the eyepiece (not during digital acquisition), the color seen is the **true emission color** of the fluorophore — no software processing is applied. Selecting a green laser and seeing red/orange through the eyepiece is expected for dyes like Alexa 555.

---

## 3. Key Optical Concepts

### 3.1 Numerical Aperture (NA)

$$NA = n \cdot \sin\theta$$

- **n**: refractive index of the immersion medium
- **θ**: half-angle of the maximum light cone collected by the objective

| Objective type | Medium | n | Typical NA |
|----------------|--------|---|------------|
| Dry | Air | 1.0 | 0.4 – 0.95 |
| Water immersion | Water | 1.33 | 0.8 – 1.2 |
| Oil immersion | Immersion oil | 1.515 | 1.3 – 1.4 |

Higher NA = better lateral resolution + greater light collection efficiency (∝ NA²).

### 3.2 Airy Disk and Airy Unit (AU)

Due to diffraction, a point source does not focus to an infinitely small point — it forms a concentric diffraction pattern called the **Airy pattern**. The central bright disk is the **Airy disk**.

$$d_{Airy} = \frac{1.22 \cdot \lambda}{NA}$$

**Examples:**

| Condition | Airy disk diameter |
|-----------|--------------------|
| 488 nm, NA 1.4 | ~0.43 μm |
| 647 nm, NA 1.4 | ~0.56 μm |
| 488 nm, NA 0.8 | ~0.74 μm |
| 647 nm, NA 0.8 | ~0.99 μm |

The **Airy Unit (AU)** is a normalized pinhole size unit defined relative to the Airy disk diameter. It automatically accounts for wavelength and NA differences across channels.

### 3.3 Pinhole and Optical Section Thickness

The pinhole controls **how thick a Z-slice** is collected:

$$z_{section} \propto \frac{\lambda \cdot n}{NA^2} + \frac{n \cdot d_{pinhole}}{NA}$$

| Pinhole size | Section thickness | Signal intensity |
|-------------|-------------------|-----------------|
| < 1 AU | Thinner, but minimal gain | Weak |
| **1 AU** | **Optimal (truly confocal)** | **Balanced** |
| > 1 AU | Thicker, more out-of-focus light | Strong |

**1 AU is "truly confocal"** because the pinhole exactly matches the Airy disk — the detection volume precisely coincides with the excitation focal point. Reducing the pinhole further below 1 AU yields negligible resolution improvement at significant signal cost.

---

## 4. Detector: PMT (Photomultiplier Tube)

The PMT converts weak fluorescence photons into measurable electrical current through cascade electron multiplication:

```
Photon → Photocathode (photoelectric effect) → Primary electron
       → Dynode 1 → multiplied electrons
       → Dynode 2 → further multiplication
       → ... (8–12 stages)
       → Anode → output current (~10⁶× amplification)
```

### 4.1 Gain (Master Gain)

The **Gain voltage** controls the acceleration potential between dynodes:

- Higher voltage → higher secondary electron yield per dynode (δ) → exponential increase in total multiplication (M = δⁿ)
- Total gain is exponentially sensitive to voltage — small changes in voltage produce large changes in signal

$$M = \delta^n$$

> In ZEN, Gain is displayed in volts (e.g., 642V for confocal, 737V for Airyscan). Higher Gain amplifies both signal and noise equally — prefer increasing laser power over Gain when possible.

---

## 5. ZEN Software: Acquisition Workflow

### 5.1 Phase 1 — Locate (Eyepiece Observation)

Before switching to laser-based digital acquisition, first locate and assess the sample visually using the **Locate** tab in ZEN. This phase uses the mercury lamp (widefield fluorescence), not the scanning laser.

1. Open ZEN and go to the **Locate** tab
2. Set up display channels — typically: **Red, Green, Blue, BF (Brightfield)**
3. Start with **Brightfield (BF)** to find the sample and confirm tissue/cell morphology
4. Switch to fluorescence channels to assess signal presence and distribution
5. Begin with the **10× objective** for an overview, then switch to **20×** to find a representative region, then move to **63× oil immersion** for final acquisition

> **Laser safety — critical:** Once you switch to the **Acquisition** tab and activate the scanning laser, **do not look through the eyepiece**. The LSM scanning laser is not safe for direct eye exposure. All observation from this point is done on-screen only.

---

### 5.2 Phase 2 — Acquisition Setup

1. Switch to the **Acquisition** tab
2. Select experiment type: **Confocal** or **Airyscan (SR)**
3. Load or configure the appropriate preset (e.g., `ALEXA647-ALEXA555-ALEXA488-DAPI-CONFOCAL` or `-AIRYSCAN`)

---

### 5.3 Phase 3 — Per-Channel Parameter Setup (Confocal)

For each channel/track:

1. Select the channel/track (laser line + fluorophore)
2. Set **Pinhole = 1 AU**
3. Click **Live** to start real-time preview
4. Enable **Range Indicator** (saturated pixels shown in red, zero-signal pixels in blue)
5. Adjust **Laser Power** and **Master Gain** until:
   - No red pixels (no saturation)
   - Signal region is clearly visible (not underexposed)
6. Click **Snap** to acquire final image
7. Repeat for each channel

### 5.4 Phase 3 (Airyscan variant) — Per-Channel Parameter Setup

Same as confocal, with additional attention to:

1. Set **Pixel Size ~0.06 μm** (ZEN will suggest this via the SR preset)
2. Set **Scan Speed** to 5 or slower
3. Set **Averaging** as needed (2× or 4× for weak signals)
4. Click **Snap**
5. After acquisition: go to **Processing** tab → **Airyscan Processing** → **Create** to generate the final reconstructed image before saving

### 5.2 Key Acquisition Parameters

| Parameter | Description |
|-----------|-------------|
| **Pixel Size (μm)** | Physical size of each pixel in the sample plane; determined by zoom and frame size |
| **Scan Speed** | Speed at which the laser sweeps across the sample; controls pixel dwell time |
| **Pixel Dwell Time (μs)** | Time the laser spends on each pixel; inversely proportional to scan speed |
| **Averaging** | Number of times each line/frame is scanned and averaged; reduces noise by √n |
| **Bits per Pixel** | Dynamic range of intensity values (8-bit = 0–255; 16-bit = 0–65535) |
| **Frame Time** | Total time to acquire one complete image frame |

### 5.3 Range Indicator

A pseudocolor display mode that overlays diagnostic colors on the grayscale image:

- **Red pixels**: saturated (signal at maximum — data lost)
- **Blue pixels**: zero intensity (no signal detected)
- **Normal color**: signal within the valid dynamic range

Use Range Indicator during Live mode when setting exposure parameters for each channel.

---

## 6. Z-Stack Acquisition

A Z-stack is a series of optical sections acquired at sequential Z-positions (depths) through the sample.

```
Z-axis (depth)
     ↑
─────────────  Slice N   (top)
─────────────  Slice N-1
─────────────  ...
─────────────  Slice 2
─────────────  Slice 1   (bottom)
```

### Applications

- **3D reconstruction** of sample volume
- **Maximum Intensity Projection (MIP)**: collapses all Z-slices into a single image by taking the maximum value at each XY position
- **Accurate signal counting**: ensures fluorescent puncta (e.g., FISH spots) distributed throughout the nucleus are not missed

### Z-step Size

ZEN automatically recommends the optimal Z-step based on objective NA and pinhole setting (Nyquist criterion). Use the recommended value for unbiased Z-sampling.

---

## 7. Airyscan Mode

### 7.1 How Airyscan Differs from Standard Confocal

| | Standard Confocal | Airyscan |
|--|-------------------|----------|
| Detector | Single PMT | 32-unit hexagonal array |
| Pinhole | Physical aperture (1 AU) | Each unit acts as ~0.2 AU sub-aperture |
| Processing | Direct output | Pixel reassignment + deconvolution |
| Lateral resolution | ~200–250 nm | ~120–140 nm (~1.7× improvement) |
| SNR | Standard | Improved (collects more photons) |
| Acquisition speed | Faster | Slower |

Airyscan collects all photons — including those at the edges of the Airy disk that standard confocal discards — and mathematically reassigns them to their correct spatial origin.

### 7.2 Airyscan Processing Steps

1. **Pixel reassignment**: each of the 32 detector units has a slightly different view of the focal point; signals are computationally shifted and combined into a single high-resolution image
2. **Deconvolution**: uses the point spread function (PSF) to sharpen the image and further enhance resolution

These two steps together achieve the ~1.7× lateral resolution improvement.

### 7.3 Why Airyscan Requires Manual Processing After Snap

After clicking **Snap** in Airyscan mode, ZEN saves **raw data from all 32 detector units** — this is not yet a final image. The Airyscan processing step must be applied before the image can be used:

```
Snap → Raw 32-channel data → [Airyscan Processing] → Final super-resolution image
```

In standard confocal mode, the PMT directly outputs a single intensity value per pixel — no post-processing is required.

### 7.4 Airyscan-Specific Parameter Considerations

Because Airyscan reconstruction is sensitive to data quality, three parameters require careful tuning:

**Pixel Size**
- Must satisfy Nyquist sampling for the enhanced resolution (~0.06 μm recommended)
- Standard confocal uses ~0.31 μm; Airyscan requires ~5× smaller pixels
- Undersampling defeats the purpose of Airyscan — the reconstruction cannot recover resolution that was not captured

**Scan Speed**
- Slower speed = longer pixel dwell time = more photons per pixel = better SNR
- Airyscan reconstruction produces artifacts if SNR is insufficient
- Typical setting: 5 (vs. 7 for standard confocal)

**Averaging**
- Repeated scans are averaged to reduce random noise by √n
- Trade-off: acquisition time multiplies proportionally; photobleaching risk increases
- Use when signal is weak or SNR is critical for publication-quality images

---

## 8. Confocal vs. Airyscan: When to Use Each

| Criterion | Standard Confocal | Airyscan |
|-----------|-------------------|----------|
| Throughput | High (faster) | Low (slower) |
| Resolution needed | Standard (~200 nm) | Super-resolution (~120 nm) |
| Sample number | Many samples | Representative images |
| Photobleaching concern | Lower | Higher |
| Post-processing | None | Required |
| Typical use in FISH workflow | Batch scanning (Observer 2) | High-res representative images |

---

## 9. Objective Lenses Available

| Magnification | NA | Notes |
|---------------|----|-------|
| 5× | 0.16 | Low magnification overview |
| 10× | 0.3 | |
| 20× | 0.8 | |
| 40× | 0.95 | |
| **63×** | **1.4** | Oil immersion — primary objective for FISH imaging |

> **For FISH imaging:** 63× oil immersion (NA 1.4) is the standard choice — maximum NA ensures best lateral resolution and light collection efficiency.

---

## 10. Sample Channels Observed During Training

From the training session (ZEN configuration: `ALEXA647-ALEXA555-ALEXA488-DAPI`):

| Track | Mode | Fluorophore | Laser | Emission color |
|-------|------|-------------|-------|----------------|
| Track 1 | Confocal / SR | AF647 | 640 nm | Deep red |
| Track 2 | Confocal / SR | AF555 | 561 nm | Orange-red |
| Track 3 | Confocal / SR | AF488 (AfIgG) | 488 nm | Green |
| Track 4 | Confocal / SR | DAPI | 405 nm | Blue |

---

*Notes compiled from LSM 900 #2 training session, April 22, 2026.*