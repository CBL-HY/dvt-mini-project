# DVT Mini Project

This repository contains a mini research project based on Denoising Vision Transformer (DVT),
focusing on reproduction, ablation studies, and analysis of F/G/H decomposition.


## Acknowledgements

This repository is based on the original DVT codebase:
- Repository: https://github.com/Jiawei-Yang/Denoising-ViT/
- Original authors: Jiawei Yang
- License: MIT

I have modified and extended the code for a mini-project (e.g., experiments, refactors, and additional scripts).


## Motivation

Denoising Vision Transformer (DVT) introduces a decomposition of visual representations
into content, artifact, and residual components. This project aims to:
1) Reproduce the core results of DVT,
2) Explore the behavior of F/G/H components through controlled ablations.


## Contributions

- Reproduced the main DVT training pipeline
- Implemented FG-only / FGH ablation settings
- Conducted preliminary experiments on VOC2012


## Experiment details

- Part A – Training dynamics of F/G/H

  This part studies how the F (content), G (grid artifact), and H (residual) components behave
  under different training configurations on VOC2012.

  * A1 – Reconstruction error (FG-only vs FGH)
    Compute per-image reconstruction error between raw ViT features and the denoised outputs
    for both FG-only and FGH settings, and visualize their relationship (scatter + histogram).

  * A2 – Parameter change before/after enabling H
    Using paired checkpoints (_beforeH and final FGH checkpoints), measure the relative L2
    change of F_neural_field and G_shared_artifacts when the residual predictor H is enabled.

- Part B – Statistical structure of the artifact field G

  This part directly analyzes the learned artifact field G_shared_artifacts to assess its dependence on spatial position and image content.

  * B1 – Location consistency of G
    For each spatial location, aggregate G vectors across images, normalize them, and compute a
    location-consistency score (L2 norm of the mean unit vector), producing a heatmap over the grid.

  * B2 – Same-location vs different-location cosine in G
    Sample cosine similarities of G vectors for:
    1) same spatial location across different images, and  
    2) different locations within the same image and compare their empirical distributions.


## Results (Preliminary)

- Part A – Training dynamics

  * A1 – Reconstruction error (FG-only vs FGH)  
    - On ~950 VOC2012 images, FG-only consistently achieves lower reconstruction MSE to raw ViT features than FGH, and the errors exhibit an approximately linear relationship.

  * A2 – Parameter change before/after enabling H
    - After enabling the residual predictor H, the field F undergoes a substantial update, with relative L2 change around 50%.
    - Plausible reason: the introduction of H allows F to reconfigure towards smoother and more spatially coherent structure, while G stays fixed as a position-dependent prior.

- Part B – Statistical consistency of G

  * B1 – Location consistency of G
    - The location-consistency score of G is high for both settings (mean = 0.92, std = 0.03), indicating strong cross-image consistency at each spatial location.  
    - The spatial pattern of this consistency is similar between FG-only and FGH, suggesting a stable, position-dependent structure in G.

  * B2 – Same-location vs different-location cosine in G
    - For G, cosine similarity at the same location across different images is high (mean = 0.85).  
    - Cosine similarity between different locations within the same image is close to zero (mean = 0.005), indicating that G is strongly location-specific and weakly correlated across positions.

