# Geometric Distortions in GNS

## Steps to Estimate and Apply Geometric Distortions (GD) on GNS2

---

### 0. Prepare Cubes for GD Corrections
- Run `cubes_for_gd_corrections.py` to group all the cleaned cubes (reduced for sky, flat-fielded, and cleaned for cosmic rays) into four FITS files, one for each chip.
- This script automatically runs `missfits`, which groups all the slices into a cube and deletes the individual slices afterward.
- The generated cubes will have the correct format for use with Astromatic software. **Inspect the generated cubes carefully**.

#### 0.1 Optional: Remove Bad Frames
- Use `delete_bad.py` if additional bad slices are detected during inspection. In principle, the cubes used in `cubes_for_gd_corrections.py` should already be cleaned of bad frames.
- The script moves the bad slices to a `bad_slices` folder for each field. (**Send these folders to Hervé**.)

---

### Astromatic.py
- This script runs all three steps required for GD corrections (#1, #2, and #3), namely: `sextractor`, `scamp`, and `swarp`.

---

### Alternative: Running Steps Separately
If desired, you can run `sextractor`, `scamp`, and `swarp` separately as described below:

#### 1. Run Source Extractor
- Execute `source-extractor cube.fits -c default_c[1,2,3,4].sex`. This will generate a **MEF (Multi-Extension FITS)** file with different pointings, with an extension for each image.
- Ensure that this cube was created using the `cubes_for_gd_corrections.py` script.
- Edit the `default.param` file to select the variables required by SCAMP. A list of all available SExtractor variables can be found in `default.sex`.

#### 2. Run SCAMP
- Run the command: `scamp sextractor.cat -c scamp.conf`.
- SExtractor will generate a catalog to feed into SCAMP, which calculates the geometric distortion. SCAMP will generate a `header.head` file.

#### 3. Run SWarp
- Run `SWarp cube.fits`. 
- The `.head` file generated by SCAMP is used to modify the original `cube.fits`. Ensure this `.head` file is correctly referenced in the configuration.

---

### 4. Apply Axis Correction
- Run `axis_correction.py` to equalize the axes in the FITS files generated by SWarp. This step updates the WCS information accordingly.

---

Follow these steps to properly apply geometric distortion corrections to GNS2 data.

