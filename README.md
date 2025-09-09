# ROAMM: Reading Observed At Mindless Moments

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Python 3.8+](https://img.shields.io/badge/python-3.8+-blue.svg)](https://www.python.org/downloads/)
[![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-orange.svg)](https://jupyter.org/)

## 📖 Overview

ROAMM is a comprehensive dataset for studying reading behavior through simultaneous EEG and eye-tracking recordings with precisely temporal human attention annotations. This repository contains tools for preprocessing, analyzing, and visualizing multimodal data collected during reading tasks. Note that all path variables are anonymized for blind review. 

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

## 🔧 Load Data

We put a lot of effort into making ROAMM easy to use, even if you’ve never worked with EEG or eye-tracking data before. Everything is pre-aligned and stored in **pandas DataFrames**, so you can load it with just a few lines of Python. 

Once you download the dataset from [OSF](https://osf.io/kmvgb/?view_only=688b268d5b784ff39eba5b73bc10171e), here’s how you can get started: 

**1. Import pakcages and set up paths**

```python
import os
import pandas as pd

# define data root
# this is the path to the ROAMM folder on local machine
roamm_root = r"/Users/~/ROAMM/" # change this to your path
ml_data_root = os.path.join(roamm_root, 'subject_ml_data')
```
**2. Load a single run for one subject**

```python
subject_id = 's10014'
subject_dir = os.path.join(ml_data_root, subject_id)
run_number = 1
df_sub_single_run = pd.read_pickle(os.path.join(subject_dir, f'{subject_id}_run{run_number}_ml_data.pkl'))
```

**3. Load all runs for one subject**
```python
pkl_files = [f for f in os.listdir(subject_dir) if f.endswith('.pkl')]
df_sub_all_runs = pd.DataFrame()
for pkl_file in pkl_files:
    df_sub_single_run = pd.read_pickle(os.path.join(subject_dir, pkl_file))
    df_sub_all_runs = pd.concat([df_sub_all_runs, df_sub_single_run])
```

**4. Load all subjects, filtered to first-pass reading**

```python
# load all runs for all subjects
all_subjects = [d for d in os.listdir(ml_data_root) if d.startswith('s') and os.path.isdir(os.path.join(ml_data_root, d))]
df = pd.DataFrame()
for subject_id in all_subjects:
    subject_dir = os.path.join(ml_data_root, subject_id)
    pkl_files = [f for f in os.listdir(subject_dir) if f.endswith('.pkl')]

    # make sure each subject has 5 runs of data
    if len(pkl_files) != 5:
        raise ValueError(f"Subject {subject_id} has {len(pkl_files)} runs instead of 5")
    
    for pkl_file in pkl_files:
        df_sub_single_run = pd.read_pickle(os.path.join(subject_dir, pkl_file))
        # I highly recommend you to filter out reading runs that are not the first pass reading
        # to save memory
        df_sub_single_run = df_sub_single_run[df_sub_single_run['first_pass_reading'] == 1]
        # add subject id to the dataframe   
        df_sub_single_run['subject_id'] = subject_id
        # convert bool col explicitly to avoid pandas warning
        for col in ['is_blink', 'is_saccade', 'is_fixation', 'is_mw', 'first_pass_reading']:
            df_sub_single_run[col] = df_sub_single_run[col] == True
        # append to the dataframe
        df = pd.concat([df, df_sub_single_run])
    
    print(f'Subject {subject_id} has been loaded.')
```

## 📈 Dataset Statistics

- **Total Subjects**: 44 participants
- **EEG Channels**: 64 channels 
- **Sampling Rate**: 256 Hz
- **Eye-tracking Features**: Gaze position, pupil size, fixations, saccades, blinks
- **Reading Labels**: First-pass reading, mind-wandering states
- **Story Word Counts**: ~2,000-2,300 words per story


## 📄 License

This project is licensed under the MIT License - see the LICENSE file for details.


**Note**: This dataset is part of ongoing research into reading comprehension and attention. Please cite appropriately if using this data in your research.
