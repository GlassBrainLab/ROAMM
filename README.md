# ROAMM: Reading Observed At Mindless Moments

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Python 3.8+](https://img.shields.io/badge/python-3.8+-blue.svg)](https://www.python.org/downloads/)
[![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-orange.svg)](https://jupyter.org/)

## 📖 Overview

ROAMM is a comprehensive dataset for studying reading behavior through simultaneous EEG and eye-tracking recordings with precisely temporal human attention annotations. This repository contains tools for preprocessing, analyzing, and visualizing multimodal data collected during reading tasks. Note that all path variables are anonymized for blink review. 

## 🧠 Dataset Description

The ROAMM dataset includes:
- **EEG Data**: BioSemi ActiveTwo 64-channel recordings
- **Eye-tracking Data**: EyeLink 1000 Plus: Gaze position, pupil size, and eye movement events
- **Reading Materials**: Five different stories with word-level coordinate mappings
- **Behavioral Labels**: Mind-wandering annotations and first-pass reading indicators

### Reading Stories
- History of Film
- Pluto  
- Prisoners Dilemma
- Serena Williams
- The Voynich Manuscript

## 📁 Repository Structure

```
ROAMM/
├── notebooks/
│   └── file_preprocessing.ipynb    # Main analysis and visualization notebook
├── res/
│   ├── history_of_film_control_coordinates.csv
│   ├── pluto_control_coordinates.csv
│   ├── prisoners_dilemma_control_coordinates.csv
│   ├── serena_williams_control_coordinates.csv
│   └── the_voynich_manuscript_control_coordinates.csv
└── README.md
```

## 🚀 Getting Started

### Prerequisites

```bash
# Core dependencies
pip install pandas numpy matplotlib seaborn
pip install mne scipy jupyter
```

### Quick Start

1. **Clone the repository**:
   ```bash
   git clone <repository-url>
   cd ROAMM
   ```

2. **Launch Jupyter**:
   ```bash
   jupyter notebook
   ```

3. **Open the main analysis notebook**:
   ```
   notebooks/file_preprocessing.ipynb
   ```

## 🔧 Main Functions

### Data Loading
```python
# Load ML-ready dataset for a specific subject and run
df = load_ml_data(subject_id='s10014', run_number=1)

# Load and visualize dataset statistics
evaluate_dataset_scale(data_root='/path/to/data')
```

### Visualization
```python
# Create synchronized EEG and eye-tracking plots
plot_eeg_eyetracking_sync(
    df_data=df, 
    time_start=100, 
    time_end=110,
    channel_mode='reading'  # 'comprehensive', 'reading', 'attention', 'minimal'
)
```

## 📈 Dataset Statistics

- **Total Subjects**: 44 participants
- **EEG Channels**: 64 channels 
- **Sampling Rate**: 256 Hz
- **Eye-tracking Features**: Gaze position, pupil size, fixations, saccades, blinks
- **Reading Labels**: First-pass reading, mind-wandering states
- **Story Word Counts**: ~2,000-2,300 words per story

## 🧪 Analysis Examples

### 1. Basic Data Exploration
```python
# Load a single subject's data
df = load_ml_data('s10014', 1)

# Check data structure
print(f"Data shape: {df.shape}")
print(f"Time range: {df['time'].min():.2f} - {df['time'].max():.2f} seconds")
print(f"First-pass reading: {df['first_pass_reading'].sum()} samples")
```

### 2. EEG Channel Analysis
```python
# Analyze EEG channels by brain region
eeg_cols = [col for col in df.columns if any(ch in col for ch in ['Fp', 'F', 'C', 'P', 'O'])]
regional_channels = get_representative_channels(eeg_cols, mode='comprehensive')
```

### 3. Eye Movement Analysis
```python
# Analyze fixation patterns
fixations = df[df['is_fixation'] == 1]
print(f"Total fixations: {len(fixations)}")
print(f"Average fixation duration: {fixations.groupby('fixation_word').size().mean():.2f} samples")
```


## 📄 License

This project is licensed under the MIT License - see the LICENSE file for details.


**Note**: This dataset is part of ongoing research into reading comprehension and attention. Please cite appropriately if using this data in your research.
