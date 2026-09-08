# OCARR

Supplementary figures and data for **Operator Conditioned Adaptive Randomized Reconstruction for Phased Array Radar Super Resolution Imaging in Low-Altitude Remote Sensing**.

Xiangxiang Wang, Haining Yang, Yebo Wang, Na Li, and Yujian Cheng  
University of Electronic Science and Technology of China (UESTC)

[Simulation results](#simulation-results) · [Field experiments](#field-experiments) · [Data](#data) · [Contact](#contact)

## Overview

We study angular super-resolution for millimeter-wave phased array radar in low-altitude remote sensing. Closely spaced scatterers produce overlapping echoes, while the array beam pattern changes with steering angle. These effects make it difficult to separate weak targets using a single shift-invariant point-spread function.

Our method, operator conditioned adaptive randomized reconstruction (OCARR), uses the two sided model $Y=KXG+N$, with separate operators for the range response and direction-dependent array pattern. The operators shape the random probes used for echo denoising. We then select the truncation rank locally in overlapping blocks to account for strong returns, weak returns, and noise. Sparse reconstruction starts with full-image FISTA and continues within regions of interest identified from the intermediate estimate.

The experiments examine three components: operator conditioned sketching (OCS), block adaptive rank selection (BARS), and adaptive ROI constrained FISTA (ARF). This repository currently contains their result figures and processed MATLAB data; the reconstruction code is not included.

## Simulation results

The simulated scene contains five target pairs on a 256 × 256 range–azimuth grid spanning 0–48 m and −40° to 40°. Target amplitudes vary from 0.4 to 1.0. We compare OCARR with TRD, truncated SVD (TSVD), and two-sided randomized SVD (TS-RSVD) at three signal-to-noise ratios.

The table below gives the OCARR results reported in the manuscript.

| SNR | Angular resolution | Recovered targets | Mean location error (pixels) | Runtime (s) |
|---:|---:|---:|---:|---:|
| 25 dB | 0.94° | 10/10 | 0.80 | 12.0 |
| 17.5 dB | 0.94° | 9/10 | 1.44 | 21.9 |
| 10 dB | 2.20° | 8/10 | 1.71 | 30.7 |

Angular resolution is measured separately by retaining P1 and P2 and varying their separation. Target recovery uses a five-pixel matching tolerance; the location error is averaged over recovered targets.

At 10 dB, TRD, TSVD, and TS-RSVD recover 0, 1, and 0 of the 10 targets, respectively. The following figures compare TS-RSVD and OCARR for this case.

<table>
  <tr>
    <th>TS-RSVD, 10 dB</th>
    <th>OCARR, 10 dB</th>
  </tr>
  <tr>
    <td width="50%"><img src="Comparison%20and%20ablation%20study/10dB/Figure/10dB_nophy_noblcok_noroi_Xhat.png" alt="TS-RSVD reconstruction of the simulated scene at 10 dB" width="100%"></td>
    <td width="50%"><img src="Comparison%20and%20ablation%20study/10dB/Figure/10dB_phy_blcok_roi_Xhat.png" alt="OCARR reconstruction of the simulated scene at 10 dB" width="100%"></td>
  </tr>
</table>

The ablations use TS-RSVD as the common baseline and add OCS, BARS, and ARF individually and in combination. OCS reduces the two-sided projection residual at a fixed sketch dimension. BARS gives the largest individual increase in target recovery, while ARF reduces runtime once the target support has been estimated. At low SNR, ARF alone can lose targets when the initial support estimate is unreliable.

Browse the full comparisons and ablations: [25 dB](Comparison%20and%20ablation%20study/25dB/) · [17.5 dB](Comparison%20and%20ablation%20study/17.5dB/) · [10 dB](Comparison%20and%20ablation%20study/10dB/).

## Field experiments

We collected two ground-scene measurements with a developed 94 GHz phased array radar. The transmitting and receiving arrays each contain 16 elements. The radar has a bandwidth of 400 MHz, a range resolution of 0.375 m, and a broadside 3-dB beamwidth of 6.7°.

Field 1 uses a longer sensing range and a higher oblique angle than Field 2. The OCARR reconstructions below follow the island and shoreline contours marked by the red dashed curves. These field comparisons assess agreement with the observed scene; an exact reference scattering distribution is unavailable.

<table>
  <tr>
    <th>Field 1</th>
    <th>Field 2</th>
  </tr>
  <tr>
    <td width="50%"><img src="Field%20Experiments/Figure/Field1_phy_blcok_roi_Xhat_outline.png" alt="OCARR field reconstruction with reference island and shoreline contours" width="100%"></td>
    <td width="50%"><img src="Field%20Experiments/Figure/Field2_phy_blcok_roi_Xhat_outline.png" alt="OCARR field reconstruction with the reference shoreline contour" width="100%"></td>
  </tr>
</table>

Processing times reported in the manuscript:

| Method | Field 1 (s) | Field 2 (s) |
|---|---:|---:|
| TRD | 50.3 | 48.0 |
| TSVD | 47.2 | 45.8 |
| TS-RSVD | 40.4 | 38.5 |
| OCARR | 29.6 | 30.1 |

The measured-echo images and all four reconstructions are available in [Field Experiments/Figure](Field%20Experiments/Figure/).

## Data

| Folder | Contents |
|---|---|
| [Angle resolution test](Angle%20resolution%20test/) | Results for the two target angular resolution tests |
| [Comparison and ablation study](Comparison%20and%20ablation%20study/) | Simulated noisy echoes, reference method results, and component ablations |
| [Field Experiments](Field%20Experiments/) | Reconstruction data and figures from the two field measurements |

The simulation folders are grouped by SNR: `25dB`, `17.5dB`, and `10dB`. Each has a `Data` directory for `.mat` files and a `Figure` directory for `.png` files. The field folder uses the same two subdirectories, with filenames beginning with `Field1` or `Field2`.

Filenames retain the labels used during the experiments:

| Filename label | Configuration |
|---|---|
| `TRD` | TRD reference method |
| `TTRD` | TSVD reference method |
| `nophy_noblcok_noroi` | TS-RSVD baseline |
| `phy` / `nophy` | Operator conditioned / isotropic Gaussian sketching |
| `blcok` / `noblcok` | Block adaptive / global rank selection |
| `roi` / `noroi` | ROI constrained / full image FISTA |
| `phy_blcok_roi` | OCARR with all three components |

The spelling `blcok` is retained from the original filenames. `Xhat` denotes a reconstruction, `Ynoisy` denotes a noisy echo, and `outline` identifies field figures with reference contours.

For example, after downloading the repository, run the following from its root directory in MATLAB:

```matlab
file = fullfile('Comparison and ablation study', '25dB', ...
    'Data', '25dB_phy_blcok_roi_Xhat.mat');
whos('-file', file)
result = load(file);
```

## Contact

For questions about the manuscript or data, please contact Haining Yang at [hnyang@uestc.edu.cn](mailto:hnyang@uestc.edu.cn).
