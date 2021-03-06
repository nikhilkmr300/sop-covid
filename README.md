# covid-audio

This is the repository for <i>A Three-Fold Machine Learning Approach to Detecting COVID-19 from Audio Data</i>.

## Instructions
The data has not been uploaded to this repository.
It has been acquired through an academic license from the University of Cambridge.
Refer [this](https://covid-19-sounds.org/en/blog/data_sharing.html) link for instructions on licensing their data.

<img src="docs/images/Data_Collection.png" alt="Data_Collection" width="500">

Once you have downloaded the dataset, move it to the `./data_raw` directory. Then unzip all the zip files in the directory.

Run any of the following commands, depending on what data you want to generate:
* `make all`: Generates everything that is required. If you are unsure of which option to use, use this, or just run `make`.  
* `make raw`: Generates audio samples in a more organized form, split into train, validation and test sets.  
* `make rnn`: Generates data in the form required by recurrent neural network models.  
* `make spec`: Generates spectrograms.  
* `make struc`: Generates csv files containing handcrafted features to pass to traditional ML models.
For other targets, refer `Makefile` in the `voice` directory.

For further details and instructions, read the following sections.

## Directory structure
```
.
├── Makefile
├── data_raw
├── data_rnn
├── data_spec
├── data_struc
├── eda
└── set_audio_params.py
```

* `data_raw`: Contains data in raw audio format (.wav or .webm). Running `make` in this directory generates `data_clean`, which contains the audio files in a more organized form suitable for this project. Also performs train-valid-test split.  
* `data_rnn`: Contains data with handcrafted features, preserving time-ordering, to pass into a recurrent neural network.  
* `data_spec`: Contains log and mel spectrograms.  
* `data_struc`: Contains data with handcrafted features aggregated over the audio sample, in a structured format (csv), to pass into a traditional ML model.  
* `eda`: Contains some exploratory data analysis on the data.  
* `set_audio_params.py`: Sets the hyperparameters such as sampling rate and number of samples in a frame. Make sure you run `make all` after editing any hyperparameters in this file, for the changes to take effect. Refer this file for more details on what hyperparameters can be tweaked.

<img src="docs/images/Data_Preprocessing.png" alt="Data_Preprocessing" width="800">

## Filename convention
Files are renamed in `data_clean` according to the convention `AUDIOTYPE_CLASS_isCoughSymptom_datasource_[uniqueID]_originalFileName`.  
* AUDIOTYPE: Type of audio data –– breath, cough.  
* CLASS: Class for the classification model  asthma, covid, normal.  
* isCoughSymptom: Whether the patient had cough as a symptom or not.  
* datasource: Source of the data –– Android application, web application. 
* uniqueID: Several files collected from the web application have the same names. Unique ID is to prevent overwriting on copying to `data_clean`.  
* originalFileName: Original name of the file as given in the raw data.  

## Recurrent models
Data fed to a Keras RNN requires the shape `[num_samples, num_timesteps, num_features]`. Running `make rnn` generates train, validation and test data with this shape as `.npy` files.

## Spectrograms
We have used two kinds of spectrograms:
* Log spectrograms: Frequency in log scale, amplitude in dB (using max value as reference)  
* Mel spectrograms: Frequency in mel scale, amplitude in dB (using max value as reference)

<img src="docs/images/Spectrogram.png" alt="Spectrogram" width="800">

## Feature extraction
We extract handcrafted features from the audio data for the recurrent models and traditional ML models. These are present in the `data_struc` directory as csv files.

Audio features come in two types:  
* Instantaneous: Extracted for each frame in an audio sample.
* Global: Extracted for the audio sample as a whole.

For the recurrent models, we use only the instantaneous features, to preserve the time-ordering. (Global features do not contain any time-related information, as they are taken over the complete audio sample.)

For the traditional ML models, we use global and aggregated instantaneous features, so time-ordering is not preserved. We need representations of the instantaneous features (calculated over each frame) over the whole audio sample. So we use aggregate statistics to summarize the instantaneous features over the whole audio sample. You can change which features to use and the statistics used to aggregate the instantaneous features in `./feature_extraction/generate_features.py`.

<img src="docs/images/Feature_Extraction.png" alt="Feature_Extraction" width="600">

## Web Application

<img src="docs/images/Web_App_Frontend.png" alt="Web_App_Frontend" width="800">

Web application has been deployed on `Heroku`. It can be accessed at: [link](https://covid-audio.herokuapp.com/)

## Group Members

| Name                | ID            |
| ------------------- | ------------- |
| Nikhil Kumar        | 2017B5A70658P |
| Vishal Mittal       | 2017A7PS0080P |


## References

[Exploring Automatic Diagnosis of COVID-19 from Crowdsourced Respiratory Sound Data](docs/Exploring_Automatic_Diagnosis_of_COVID19_from_Crowdsourced_Respiratory_Sound_Data.pdf) (Paper)