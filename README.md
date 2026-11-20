# Mix Revision Analyzer

I built this project to connect my mixing experience with Python and data analysis, so I could see the actual EQ cuts as precisely as possible and compare them with the original final mix. The notebook uses a controlled EQ edit and a uniform gain reduction to test whether the measurements can distinguish changes in frequency balance from changes in overall level. 

## Experiment

The source is a 30-second excerpt from a song of mine; both audio files are 44.1 kHz stereo, 32-bit WAV files. 

| Version | Change |
| --- | --- |
| Original | Reference recording |
| EQ revision | Bell cut centered near 300 Hz, approximately −6.08 dB, applied during 10–20 seconds |
| Gain-only control | Uniform −3 dB gain applied to the original throughout the clip in Python |

## Method

1. Load both WAV exports and check their sample rates and dimensions.
 
2. Compute an STFT for each channel using 4096-sample Hann Windows, a 1024-sample hop, and no boundary padding. At 44.1 kHz, these correspond to approximately 93 ms windows and 23 ms spacing. 

3. Sum squared STFT magnitudes across channels and frequency bins in five bands spanning 20 Hz to 10 kHz.

4. Calculate the revision to the original band power ratio in dB for each time window. 

5. Compare each band's share of total analyzed power by subtracting the total-power change from its band power change. 

6. Plot the measurements over time and run checks against the known edits. 

Raw band power changes describe the measured reduction or increase in each band. Band share changes describe its changing proportion of the total analyzed power. A band's share can increase when another band is cut. 

## Results

Median changes for the EQ revision during 12 - 18 are shown below. 

| Frequency band | Raw power change | Power-share change |
| --- | --- | --- |
| 20–200 Hz | −0.80 dB | +1.42 dB |
| 200–500 Hz | **−5.57 dB** | **−2.96 dB** |
| 500–2,000 Hz | −1.61 dB | +0.93 dB |
| 2,000–5,000 Hz | −0.15 dB | +2.19 dB |
| 5,000–10,000 Hz | −0.03 dB | +2.32 dB |

The plots localize the low-to-mid reduction to approximately 10-20 seconds. The gain-only control measures approximately - 3db in every raw band. After normalization, its maximum absolute band-share change is approximately 0.000015 dB across the valid windows. 

All controlled experiment checks passed. The required finite measurements, control balance changes below 0.001 dB, the strongest raw cut in 200 - 500 Hz, a target band median share reduction exceeding 1 dB, and near-zero balance changes outside the edit with a 0.2-second transition margin. 

The figures and validation output are included in the notebook. 

## Run the notebook

Install Python and the required packages in your project environment.

```bash
python -m pip install numpy scipy librosa matplotlib notebook
```

Place the project notebook and both WAV files in the same directory. Keep the input filenames as they are. 

| Input file | Role |
| --- | --- |
| `A original.wav` | Original reference |
| `B Revision.wav` | EQ revision |

Start Jupyter from that directory.

```bash
python -m notebook
```

Open the project notebook, restart its kernel, and run all cells in order. The final validation cell should report `All controlled-experiment checks passed.

## Scope and limitations

- validation covers one recording, one bell cut, and one uniform gain reduction. Broader reliability requires additional examples of recordings and processing. 

- Inputs must contain aligned versions of the same audio. The current workflow uses stereo 32-bit WAV exports. 

- Broad bands identify the affected region. Exact recovery of EQ center frequency, gain, and Q would require further analysis. 

- Silence and very weak signals require explicit handling for broader use. Current validation expects finite measurements throughout this clip.

- Acceptance thresholds were chosen for this controlled experiment. Reliability on new mixes requires further validation.

## Planned version 2

I'm planning to take this analyzer to the next step by adding a two-file upload interface that validates compatible WAV exports, runs the comparison, and automatically presents the time and frequency-band results. Wish me luck!



