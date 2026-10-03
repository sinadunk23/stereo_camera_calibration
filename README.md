# Hand-eye calibration: one camera or two?

A self-contained notebook comparing two ways to calibrate fixed cameras to a robot (eye-to-hand):

| | Board pose from | Hand-eye solver |
|---|---|---|
| **Typical pipeline** | one camera, `cv2.solvePnP` | `cv2.calibrateHandEye` (Tsai) |
| **Stereo pipeline** | both cameras, triangulated corners | joint least squares (`scipy`) |

Both are scored by **leave-one-out**: calibrate on 30 poses, predict the 31st from the robot pose
alone, and measure the miss.

**[Open the notebook →](handeye_calibration.ipynb)**


## What it shows

* **PnP gets depth wrong, and that one camera can't see the error.** Its error lies almost entirely
  along the line of sight. A second camera ~90° away catches it immediately.
* **Triangulating with two cameras removes the depth ambiguity.** In the simulation, the board-pose
  error drops from 0.66 mm to 0.03 mm.
* **The difference grows with tiny intrinsics errors.** With camera 1's focal length off by only
  0.1 %, held-out error is 2.25 mm for the typical pipeline and 0.27 mm for the stereo one.
* On a real two-camera cell, the stereo pipeline had **57 % less** held-out position error than the
  typical pipeline (1.36 mm vs 3.14 mm).

The notebook runs on a **simulated** cell, with realistic camera geometry, detection noise and robot
accuracy, so it needs no data and every number can be reproduced. To use your own robot poses and
detected corners, see the last section of the notebook.

## Run it

```bash
pip install -r requirements.txt
jupyter notebook handeye_calibration.ipynb
```

Runs in under a minute on a laptop.
