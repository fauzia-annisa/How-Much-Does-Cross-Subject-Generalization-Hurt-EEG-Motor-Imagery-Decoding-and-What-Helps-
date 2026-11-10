# How Much Does Cross-Subject Generalization Hurt EEG Motor-Imagery Decoding?

**Question:** How well do models trained on some people decode motor imagery
in people they have never seen, and what affects it?

**Data:** PhysioNet EEG Motor Movement/Imagery (imagined left vs right fist),
50 subjects, 64 channels, 160 Hz, ~45 trials per subject (2,250 total).
Split by subject: 35 train / 5 validation / 10 test.

**Pipeline:** 8-30 Hz bandpass -> epochs 0.5-3.5 s -> per-trial global scaling
-> models. Best epoch chosen on validation subjects only.

## Results (10 unseen test subjects)
| Model | Setting | Test acc |
|---|---|---|
| Band-power + LogReg | cross-subject | 57.8% |
| CSP + LDA | cross-subject | 67.3% |
| CSP + LDA | within-subject (5-fold CV) | 60.0% |
| EEGNet (3 seeds) | cross-subject | 67.8% ± 2.4 |
| EEGNet + augmentation (3 seeds) | cross-subject | 59.3% ± 8.2 |

## Findings
1. EEGNet matches classical CSP+LDA (67.8% vs 67.3%, within noise) and beats
   band-power features (57.8%).
2. Within-subject CSP+LDA (60.0%) was lower than cross-subject, likely because
   each person has only ~45 trials. Pooling subjects helps here.
3. Augmentation (noise, time shift, channel dropout) did not help and made
   results unstable (one seed at 48.2%).
4. Training-set size: EEGNet was near chance with 5-20 subjects and 67.8%
   with 35. Not a smooth trend; confounded by fewer gradient updates on
   smaller sets (fixed epochs).
5. Physiology check: right-minus-left mu-band lateralization at C3/C4 is
   significant (p = 0.0003; 37/50 subjects in the expected direction).
6. Channel occlusion: the model relies most on CP4, CP2 and C4, over the
   right sensorimotor cortex, consistent with left-hand imagery.
7. Accuracy varies a lot across people (roughly 47% to 89% for seed 0).

## Limitations
10 test subjects, 3 seeds, no hyperparameter search, single dataset, one
EEGNet architecture, scaling experiment not step-matched, CSP+LDA not run
across training sizes, occlusion analysis on one seed.

## Future work
Step-matched scaling curve for EEGNet and CSP+LDA, EEGNet within-subject
baseline, self-supervised pretraining, per-subject calibration, other datasets.
