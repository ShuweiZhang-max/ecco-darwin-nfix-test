# ecco-darwin-nfix-test
ecco-darwin-nfix-test

This repository contains two configurations for testing Nitrogen Fixation (Nfix) in the 1-degree ECCO-Darwin model.

## 1. `1_baseline_without_NQUOTA` (Working)
This is the baseline setup where **Denitrification** (`DARWIN_ALLOW_DENIT`) and **Diazotrophy** (`DARWIN_ALLOW_DIAZ`) are enabled, but the Nitrogen Quota mechanism (`DARWIN_ALLOW_NQUOTA`) is **OFF**.
* **Status:** The model runs successfully (35 tracers).
* **Issue:** As discussed with Manfredi, because `NQUOTA` is off, the math enforces `Nfix = 0` (uptakeN is entirely from ambient NH4/NO2/NO3). Thus, the `Nfix` diagnostic output is always zero.

## 2. `2_attempt_with_NQUOTA` (Error)
To allow C3 (Synechococcus) to fix nitrogen, we enabled `DARWIN_ALLOW_NQUOTA` in `DARWIN_OPTIONS.h` and assigned C3 as a diazotroph.
* **Modifications:**
  * Increased tracers from 35 to 42 (added `n01` - `n07` for N quotas).
  * Updated `darwin_traits.txt` (C3 `DIAZO=1`, added `VMAXN`, `QNMAX`, `R_NC`, etc., based on Monteiro 2010 / Dutkiewicz 2012).
  * Updated `data.ptracers` and `data.diagnostics` to match the 42 tracers.
  * Extended the pickup file to 42 tracers using MATLAB.
* **Current Error:**
  During the 8-step test run, the model crashes with:
  `severe (18): too many values for NAMELIST variable, unit 11, file ... line 4, position 30`
  This seems to be a dimension mismatch during compilation (possibly `PTRACERS_advScheme` array size is still 35).

## Notes
* Pickup files and large forcing data are not included due to GitHub file size limits.
* Any suggestions on the compilation dimension issue are highly appreciated!
