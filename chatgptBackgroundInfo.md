# Biophoton Brain Project – Coding & Analysis Spec

## 1. Context

We are a neuroscience / psychology class interested in **biophotons** (ultraweak photon emissions, UPEs) from the **human brain**.

Biophotons / UPEs:

- Extremely faint light emitted by living tissue in the **UV–visible–near-visible** range (roughly 200–800 nm), distinct from thermal IR radiation.
- Intensity is typically **~1–1000 photons/cm²/s**, many orders of magnitude dimmer than normal room light.
- Current consensus: they arise primarily from **oxidative metabolic processes**:
  - Reactive oxygen species (ROS) in mitochondria and other pathways.
  - Excited species like **singlet oxygen (¹O₂)** and **excited carbonyls (R=O\*)**, especially from **lipid peroxidation**.
  - When these excited molecules relax, they emit photons.

Recently, **Casey et al. (2025, iScience)** measured UPEs **from human brains through the skull** using **photomultiplier tubes (PMTs)** placed around participants’ heads in a dark room. They showed:

- Brain-derived UPEs can be **distinguished from room background**.
- UPE patterns **change with cognitive state** (eyes open vs closed, auditory tasks).
- UPE dynamics **do not simply mirror EEG**, suggesting they may reflect slower, metabolic aspects of brain function.

We want to:

1. Understand these signals conceptually.
2. Design a **simple experiment** inspired by the iScience paper.
3. Build a **coding demo** that simulates and analyzes such data, suitable for teaching.

---

## 2. Key scientific questions

The class discussed these specific questions, which the demo should help illustrate:

1. **Colour / wavelength**
   - In what **wavelength range** are biophotons emitted from the brain?
   - Does the **spectrum / “colour”** of the photons change with brain state, or are we just measuring total counts?

2. **Intensity (how much light?)**
   - Rough magnitude of photon emission from tissue (order of **photons per cm² per second**).
   - Why such emissions are **not visible** and require ultra-sensitive detectors.

3. **Mechanism**
   - Which **biochemical processes** produce the light (e.g., ROS, mitochondria, lipid peroxidation)?
   - Why these emissions are considered **non-thermal** (not just heat / IR).

4. **Light vs heat**
   - Why UPEs are **distinct from thermal radiation**:
     - Different wavelength range (visible/near-visible vs IR).
     - Different dependence on **oxidative metabolism** vs temperature.

5. **Electromagnetic environment**
   - Do **static charges** or low-frequency electrical fields (e.g., mains power) emit photons that contaminate the measurement?
   - Why typical room EM fields are in **radio / low-frequency ranges** and are not directly detected by **visible-band PMTs**.
   - The difference between **electrical noise in cables** vs **real photons** hitting the detector.

6. **Instrumentation drift**
   - How **sensor sensitivity** can drift over time (dark counts, gain changes).
   - The need for:
     - Dark-count baselines (no subject in chair).
     - Background detectors facing away from the head.
     - Randomizing task order.
     - Possible calibration light source.

7. **Biological confounds**
   - Background UPE from:
     - Skin, hair, scalp.
     - Microbes on skin (bacteria, fungi) and in the body.
   - We likely measure **total photons from the head region**, not “pure brain” photons, and rely on **task-dependent changes** and **head vs background comparisons** to infer brain contributions.

8. **Environmental light confounds**
   - Major extraneous variables:
     - Cracks under doors (light leaks).
     - Windows.
     - Computer monitors, phones, LEDs, optical equipment.
     - Other people in the room moving or carrying light sources.
   - Mitigation:
     - Light-tight door and windows (blackout curtains, tape, foam).
     - No displays or LEDs, or fully taped/covered and powered down.
     - Only one participant (plus possibly one very still experimenter).

---

## 3. Experimental design (conceptual)

We want to mirror the logic of the iScience “photoencephalography” experiment in simplified form.

### 3.1. Ideal experimental setup

- **Detectors**
  - 3–4 **photomultiplier tubes (PMTs)** or other single-photon-sensitive detectors:
    - Spectral sensitivity ~**350–700 nm** (visible/near-visible).
    - Outputs: **photon counts per time bin** (e.g., counts per 100 ms).

- **Geometry**
  - **Head detectors**:
    - At least two PMTs placed near:
      - **Occipital region** (back of the head – visual cortex).
      - **Temporal region(s)** (sides – auditory cortex).
  - **Background detectors**:
    - 1–2 PMTs placed at similar distances but **pointing away** from the head to measure room background.

- **Environment**
  - **Dark room** with:
    - No windows or fully blacked-out windows.
    - Doors sealed (no visible light leaks).
    - All monitors/LEDs off and covered.
  - Optional **very dim red setup light** that is turned completely off during data collection.

- **Additional signals**
  - Optional EEG recording to illustrate that:
    - **Alpha rhythm** changes with eyes open/closed.
    - UPE and EEG have **related but distinct** patterns.

### 3.2. Conditions per participant

- **Dark adaptation**:
  - 10–15 minutes in the dark to adapt and stabilize.

- **Task blocks** (each ~2 minutes, randomized order):
  1. Eyes closed, relaxed (alpha-dominant EEG expected).
  2. Eyes open, fixating in darkness.
  3. Auditory task (e.g., detect oddball tones).
  4. Empty-chair baseline (no subject) at start and end to track drift.

- Sampling rate:
  - Photon counts sampled at **10 Hz or higher** (e.g., 100-ms or 1-s bins).

---

## 4. Coding demo: what we want you (AI agent) to build

Because we probably **don’t** have real PMTs for the class, we want a **simulation + analysis toolkit** that mirrors the real experiment and answers the questions above.

### 4.1. Goals for the simulation

Create a **synthetic dataset** (e.g., in Python or JavaScript) that models:

1. **Biophoton signal from the brain**
   - Per head detector, a base **Poisson-like photon count** process with:
     - Low mean rate (e.g., tens of counts/s).
     - Slow fluctuations (<1 Hz) to mimic metabolic rhythms.

2. **Task-dependent changes**
   - Change the mean rate and/or low-frequency modulation depending on:
     - Eyes open vs closed.
     - Auditory task vs rest.
   - Changes should be subtle (small percentage differences).

3. **Background light**
   - Background detectors (facing away) with:
     - Lower mean counts than head detectors.
     - No task-dependent modulation (except shared drift).

4. **Instrument drift**
   - Slow time-dependent drift in:
     - Dark count rates.
     - Gain (optional multiplicative factor).
   - Drift should affect *all detectors* similarly, so that:
     - Head–background comparisons can reveal brain-specific effects.

5. **Noise sources**
   - Include:
     - Shot noise (Poisson variability).
     - Optional rare “light leak” events (brief count spikes) to simulate, e.g., door crack or accidental light flash.

6. **Biological confounds**
   - Represent skin/microbe UPE as part of the **constant baseline** from the head detectors.
   - The **brain-task modulation** is added on top of this baseline.

The code should allow toggling parameters such as:

- Mean photon rates per condition.
- Size of task-related modulation.
- Drift magnitude.
- Amplitude and frequency of low-frequency metabolic rhythms.
- Occurrence and size of “light leak” events.

### 4.2. Analysis pipeline to implement

For each participant, detector, and condition:

1. **Basic statistics**
   - Compute:
     - Mean counts per second.
     - Standard deviation.
     - Coefficient of variation (CV = SD / mean).

2. **Frequency analysis**
   - Compute power spectra or at least low-frequency power (<1 Hz) from the simulated photon time series.
   - Show that:
     - Biophoton signals can have prominent slow components (<1 Hz).
     - These components may change with task.

3. **Head vs background comparison**
   - Demonstrate how:
     - Head detectors differ from background detectors.
     - Task-related changes appear in **head–background differences** but not in background alone.
   - Example visualization:
     - Line plots of mean counts for each condition, separated by detector type.
     - Plots of CV or low-frequency power across conditions.

4. **Drift handling**
   - Show how including **empty-chair baselines** helps detect and correct for drift.
   - For example:
     - Compare pre- and post-session baseline mean counts.
     - Apply a simple linear correction or normalization if needed.

5. **Sensitivity exploration**
   - Provide tools or examples where students can:
     - Increase simulated light leaks and see how they corrupt the data.
     - Increase drift and see how non-random task order can create false effects.
     - Adjust brain modulation amplitude and see when effects become undetectable.

### 4.3. Deliverables

Produce:

1. **Simulation code**
   - Either Python (e.g., Jupyter-friendly) or JavaScript (browser-friendly).
   - Well-commented functions for:
     - Generating synthetic photon count time series per detector, condition, and participant.
     - Injecting drift and noise.

2. **Analysis notebook / script**
   - Loads the simulated data.
   - Runs the analyses described above.
   - Produces clear plots and summary tables suitable for teaching.

3. **Short explanatory text**
   - Embedded in comments or markdown cells:
     - Explain what biophotons are.
     - Explain why experiments must control for light leaks and drift.
     - Show how the analysis distinguishes **brain-related signal** from **background and artifacts**.

---

## 5. Constraints and style

- Assume **very low count rates** (tens of counts per second per detector).
- Use **Poisson or related stochastic processes** to model photon arrivals.
- Keep code **modular and easy to understand** for upper-undergrad neuroscience / psychology students with some coding experience.
- Include at least one example where:
  - A naive analysis (ignoring drift or background) gives **misleading results**.
  - A more careful analysis (head vs background, baselines) gives **correct conclusions**.
