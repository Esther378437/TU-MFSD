# TU-MFSD: Research Resources for Mizo Folk-Song Classification

## Overview

This repository provides supporting research resources for the classification and acoustic analysis of Mizo folk songs from Mizoram, North-East India. The resources are intended to support reproducibility, methodological transparency, and future research on low-resource music classification and culturally significant vocal traditions.

The study investigates multiple representations of folk-song recordings, including handcrafted acoustic features, Mel-frequency cepstral coefficients (MFCCs), and pretrained OpenL3 audio embeddings. Classification approaches include a dual channel hybrid CNN, consisting of one-dimensional convolutional neural networks (1D-CNNs), two-dimensional convolutional neural networks (2D-CNNs), hybrid feature-fusion, and an OpenL3-based classification pipeline.

The original dataset contains six Mizo folk-song categories and approximately 180 minutes of recordings. This repository provides the research materials that can be shared under the applicable permissions. **The presence of metadata, features, or benchmark files in this repository does not imply that the original audio recordings are publicly available or may be redistributed. Raw audio files may be obtained upon request to author.**

## Research Objectives

The resources support the following research objectives:

- Classification of Mizo folk songs into six categories.
- Investigation of handcrafted acoustic descriptors for folk-song classification.
- Comparison of acoustic-only features, MFCC-based features, and fusion feature.
- Evaluation of hybrid fusion between handcrafted acoustic features and learned representations.
- Evaluation of pretrained OpenL3 embeddings for audio classification.
- Investigation of pitch-shifting, time-stretching, and frequency-masking augmentation.

## Dataset Description
The research dataset comprises recordings of Mizo folk songs organised into six classification categories. Audio recordings were segmented for analysis, and multiple acoustic and learned representations were evaluated.

The principal data characteristics are:

- **Domain:** Mizo folk-song classification and acoustic analysis.
- **Geographical and cultural context:** Mizoram, North-East India.
- **Number of categories:** Six.
- **Approximate total recording duration:** 180 minutes.
- **Audio sample rate used in the classification pipeline:** 24,000 Hz.
- **Segment duration:** 3 seconds.
- **Classification approaches:** 1D-CNN, 2D-CNN, hybrid feature fusion, and OpenL3-based classification.
- **Augmentation methods investigated:** Pitch shifting (PSA), time stretching (TSA), and frequency masking (FMA).

The exact category names, class-label mapping, and any recording-level metadata are documented in `data/class_labels.csv` and `docs/DATA_DICTIONARY.md`.

## Available Data and Access Conditions

<!--
The release may include the following materials, depending on the permissions obtained:

| Resource               | Description                                                          | Access                                    |
| ---------------------- | -------------------------------------------------------------------- | ----------------------------------------- |
| Metadata               | Permitted, non-identifying information about recordings and segments | Specify release status                    |
| Class labels           | Mapping between numerical labels and song categories                 | Specify release status                    |
| Dataset partitions     | Training, validation, and test identifiers                           | Specify release status                    |
| Acoustic features      | Numerical handcrafted acoustic descriptors                           | Specify release status                    |
| Feature configurations | Parameters used for acoustic and time–frequency feature extraction   | Specify release status                    |
| Evaluation results     | Baseline and augmentation metrics                                    | Specify release status                    |
| Original audio         | Source WAV recordings                                                | Not included unless explicitly authorised |
-->
The repository does not grant permission to access or redistribute source recordings. Researchers who require access to restricted materials should follow the access procedure documented by the dataset creators.

## Metadata Format

The metadata schema is shown below:

| Field                    | Description                                                                               |
| ------------------------ | ----------------------------------------------------------------------------------------- |
| `sample_id`              | Stable, non-identifying identifier for a segment                                          |
| `song_id`                | Non-identifying identifier for the source song                                            |
| `filename`               | Permitted filename or pseudonymous recording identifier                                   |
| `label`                  | Numerical class label                                                                     |
| `category`               | Name of the corresponding song category                                                   |
| `duration_seconds`       | Duration of the segment in seconds                                                        |
| `sample_rate_hz`         | Audio sampling rate in hertz                                                              |
| `split`                  | Assigned partition, such as train, validation, or test                                    |


## Dataset Partitioning and Validation

The experiments use song-level grouped partitioning to reduce the risk of data leakage between training, validation, and test sets. Segments originating from the same song are assigned to the same partition.

Two dataset configurations were used in the deep-learning experiments:

| Experiment configuration                    | Total samples | Training | Validation | Test |
| ------------------------------------------- | ------------: | -------: | ---------: | ---: |
| Acoustic 1D-CNN branch                      |         3,145 |    2,017 |        493 |  635 |
| MFCC-based 2D-CNN branch, hybrid, and OpenL3|         3,096 |    1,991 |        497 |  608 |

The difference in sample counts reflects feature representation-specific data validity filtering. The validation set was used for model selection and training control, including early stopping. The test set was reserved for final evaluation. The grouping variable was song identity rather than singer identity. Therefore, the protocol supports song-independent evaluation but should not be interpreted as a singer-independent evaluation. The traditional machine-learning baselines used a separate 80:20 partition and five-fold cross-validation protocol. Details of that protocol should be consulted before directly comparing its results with those from the grouped deep-learning experiments.


## Split Assignments Format

The file `split_assignments.csv` provides the training, validation, and test partition assignments used in the Acoustic 1D-CNN experiment branch. Each row identifies an audio segment, its source song, its class label, and its assigned partition.  The partitioning is song-independent, meaning that segments belonging to the same source song are assigned to the same partition. The `split_assignments_2d.csv` provides the partition assignments for the 2D-CNN branch, the fusion classifier, and the openL3 classifier.

| Field | Description |
|---|---|
| `sample_id` | Identifier for an audio segment. |
| `song_id` | Identifier for the original song from which the segment was extracted. |
| `label` | Numerical class label used in the experiments. |
| `split` | Assigned partition: `train`, `validation`, or `test`. |



## Data Augmentation

Three augmentation techniques were investigated:

- **Pitch shifting (PSA):** modification of pitch within the specified augmentation range.
- **Time stretching (TSA):** modification of playback duration without intentionally changing pitch.
- **Frequency masking (FMA):** masking of frequency regions in time–frequency representations.

Augmentation was applied only to training data. Validation and test samples remained unaugmented and unchanged across the corresponding baseline and augmentation experiments. Synthetic augmented samples are not independent original recordings.

## Feature Representations

The experiments investigate the following representations:

### Handcrafted Acoustic Features

The acoustic feature set contains 29 numerical descriptors, including fundamental-frequency statistics, zero-crossing rate, energy, excitation-related measures, cepstral peak prominence measures, MFCC coefficients, and formant-related descriptors.

The exact list of released features, their definitions, units, and extraction settings should be provided in `features/feature_dictionary.csv`.

### MFCC Representation

MFCCs are extracted from audio segments to represent the spectral envelope in the cepstral domain. The classification pipeline uses 13 MFCC coefficients over successive time frames.

### OpenL3 Embeddings

OpenL3 is used as a pretrained audio representation model. The experimental configuration uses the music content type, a Mel-128 input representation, and 512-dimensional embeddings. Frame-level embeddings are mean-pooled to obtain one vector per segment, which is then supplied to a trainable classification head.

The configuration files and implementation details should be consulted for the complete extraction settings.

## Evaluation

Classification performance is evaluated using multiple metrics, including:

- Accuracy.
- Balanced accuracy.
- Macro-averaged precision, recall, and F1-score.
- Weighted precision, recall, and F1-score.
- Class-wise precision, recall, and F1-score.
- Confusion matrices.

Macro-averaged metrics are particularly relevant because the six song categories are not equally represented in the dataset.


## Ethical, Copyright, and Cultural-Use Considerations

Mizo folk songs represent culturally significant musical and vocal traditions. Access to these materials must be consistent with the permissions and restrictions applicable to the recordings and the communities concerned.

The following conditions apply to this release:

- Only materials authorized for distribution are included.
- Public availability of metadata or numerical features does not imply permission to redistribute source recordings.
- Researchers must comply with the applicable licence and any additional access conditions.
- Cultural protocols and restrictions specified by the dataset creators must be respected.
- Recordings, metadata, and features must not be used in ways that violate performer consent, copyright, privacy, or documented community restrictions.
- Requests for restricted materials should be directed to the designated dataset contact, subject to the applicable approval process.

## Licence

Currently, licensing information has not been specified explicitly for the TU-MFSD. It will be updated in future releases.
<!--
The licence for each released component must be specified explicitly.

- **Code:** [Insert the selected software licence, if applicable.]
- **Metadata:** [Insert the approved metadata licence.]
- **Feature tables:** [Insert the approved feature-data licence.]
- **Audio recordings:** [State whether they are included, restricted, or available by permission.]

Do not assume that the source recordings, metadata, features, and code can all be distributed under the same licence. If permissions are unclear, do not publish the affected files until the restrictions have been resolved.
-->

## Citation

If you use the resource files in a publication, presentation, or other research output, please cite the associated paper for TU-MFSD as below:

**Suggested citation format**
```bibtex
@article{ramdinmawii2024database,
  title={Database creation and preliminary acoustic analysis of Mizo folk songs},
  author={Ramdinmawii, Esther and Mittal, Vinay Kumar and Nath, Sanghamitra},
  journal={SN Computer Science},
  volume={5},
  number={8},
  pages={1153},
  year={2024},
  publisher={Springer}
}.
```
The citation will be updated when the DOI and persistent identifier have been confirmed.

## Contact

For questions concerning the research resources, documentation, or access conditions, contact:

- **Dataset contact:** Esther Ramdinmawii
- **Contact email:** esther.rdmchhakchhuak@gmail.com
<!-- - **Institution:** [Insert institution.]-->

## Acknowledgements

The authors acknowledge the performers, contributors, and relevant custodians whose participation and knowledge support the curation of this database and the research on Mizo folk-song traditions.

