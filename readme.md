# Quail Localization Project

## The framework
![alt text](https://github.com/dnl2k/Mel_BiLSTM/blob/main/Figures/Quail_Localization_Framework.png)

<p align="center">
Figure 1: Overview of the framework
<p>

The overview of the frame work can be seen in Fig. 1.

### Data Preprocessing

![alt text](https://github.com/dnl2k/Mel_BiLSTM/blob/main/Figures/Data_Preprocessing.png)

<p align="center">
Figure 2: Data Preprocessing. The red line is the label with the high value correspond to quail call signature and the low value correspond to not quail signature.
<p>

  First, the raw waveform will be convert into Mel features using the periodic Hann window with the window size of 800, the overlap length of 400, the FFT length of 1600, the number of frequency bands of 128, the frequency range between 500 Hz and 4000 Hz. That frequency range was selected because the quail calls' signatures are commonly resided between 1000 Hz and 3200 Hz. The Mel features will have the shape of 128xT, where T is the number of timesteps. Second, the normalized Mel features are obtained by converting the Mel features to dB scale and normalized using mean and standard deviation (Fig. 2).  

  More examples with different classes can be seen in Appendix A1.

### Network Architecture for Mel_BiLSTM

Adopted and modified the Mel_BiGRU framework proposed by [1], the Mel_BiLSTM network architecture can be seen in Fig. 3.

![alt text](https://github.com/dnl2k/Mel_BiLSTM/blob/main/Figures/Network_Architecture.png)

<p align="center">
Figure 3: Mel_BiLSTM Network Architecture
<p>

### Matching Call

![alt text](https://github.com/dnl2k/Mel_BiLSTM/blob/main/Figures/Matching_Example_png.png)

<p align="center">
Figure 4: Matching Example. All microphones are synchronized. The red lines are the quail signature by the Mel_BiLSTM network. The black lines are the window contained the matched calls generated using an algorithm developed by the author.
<p>

Matched calls are the calls coming from the same bird but due to the differences in distances from the bird location to the microphones' locations, the calls recorded by the synchronized microphones are time-shifted (Fig. 4).

### Denoising

![alt text](https://github.com/dnl2k/Mel_BiLSTM/blob/main/Figures/Denoising_Results.png)

<p align="center">
Figure 5: Denoising Example. The spectrogram of the raw audio signals before (left) and after denoising (right).
<p>

Quail calls are detected on microphones' location 1, 3, 4, 6, 8 (Fig. 4). Bandpass and Wiener filters are applied to the audio signal to remove correlated noise and increase the signal-to-noise ratio (SNR). The result can be seen as the difference between the spectrograms of the raw audio signals before and after denoising (Fig. 5).

### TDOA estimation

![alt text](https://github.com/dnl2k/Mel_BiLSTM/blob/main/Figures/Time_Lags_Example.png)

<p align="center">
Figure 6: TDOA Estimation Example. The non-NaN values are the time delays estimated by the normalized cross-correlation algorithm using the first detection (Mic 8) signal as the reference signal (see Fig. 5). The NaN values indicates that those microphones' recordings were not used by the algorithm to estimate the time delays.
<p>

Normalized cross-correlation are applied to the raw audio segments of microphones' location 1, 3, 4, 6, 8. Other microphones' locations are not used because Mel-BiLSTM didn't detect the quail signatures on those locations, and are marked as NaN (Fig. 6). Because of the low SNR of those locations, TDOA estimation will be much less accurate. The highest peaks of the cross-correlation coeffiecients are used to estimate the TDOA values for high SNR signals. The first detection (Mic 8) is used as the reference signal, making the TDOA value of Mic 8 equal to zero (Fig. 6). 

### Spatial Localization

![alt text](https://github.com/dnl2k/Mel_BiLSTM/blob/main/Figures/Localization_Example.png)

<p align="center">
Figure 7: Localization Example. The red x are the microphones' locations. To the right of the microphones' locations are the names of the microphones and the microphone numbers inside of the pairs of round brackets (first line) and the numbers of calls detected and used to localize the predicted location.  
<p>

Using the TDOA from Fig. 6, the localization result is shown on Fig. 7.

## Experiment Results

![alt text](https://github.com/dnl2k/Mel_BiLSTM/blob/main/Figures/Pitchfork_221119_Satelline.png)

<p align="center">
Figure 8: The satelline view of Pitchfork site. The yellow pins are the microphones' locations. The name and the microphone's number can be seen near each microphone's location.
<p>

A microphone array was set up at Pitchfork study site on November 19th, 2022 (Fig. 8). A speaker was placed on various locations on the study site. 8 quail calls are played for each location. The recordings of the experiment were used to generate the localization results shown on Fig. 9 to 14.


![alt text](https://github.com/dnl2k/Mel_BiLSTM/blob/main/Figures/Pitchfork_221119_1330.png)

<p align="center">
Figure 9: Localization results for speaker's location 1. 7 matched windows were generated from the network detections and 7 predicted locations were produced by the framework. 
<p>

![alt text](https://github.com/dnl2k/Mel_BiLSTM/blob/main/Figures/Pitchfork_221119_1340.png)

<p align="center">
Figure 10: Localization results for speaker's location 2. 7 matched windows were generated from the network detections and 7 predicted locations were produced by the framework. The location with the note "Consistent time lags" indicates that the TDOA values for that location was consistent with the TDOA values for other 6 locations and the error was caused by the spatial localization algorithm.
<p>

![alt text](https://github.com/dnl2k/Mel_BiLSTM/blob/main/Figures/Pitchfork_221119_1345.png)

<p align="center">
Figure 11: Localization results for speaker's location 3. 7 matched windows were generated from the network detections and 7 predicted locations were produced by the framework. 
<p>

![alt text](https://github.com/dnl2k/Mel_BiLSTM/blob/main/Figures/Pitchfork_221119_1350.png)

<p align="center">
Figure 12: Localization results for speaker's location 4. 6 matched windows were generated from the network detections and 6 predicted locations were produced by the framework. 
<p>

![alt text](https://github.com/dnl2k/Mel_BiLSTM/blob/main/Figures/Pitchfork_221119_1355.png)

<p align="center">
Figure 13: Localization results for speaker's location 5. 6 matched windows were generated from the network detections and 1 predicted locations were produced by the framework. This indicates a problem of the spatial localization algorithm with the quail outside of the array.
<p>

![alt text](https://github.com/dnl2k/Mel_BiLSTM/blob/main/Figures/Pitchfork_221119_1405.png)

<p align="center">
Figure 14: Localization results for speaker's location 6. 2 matched windows were generated from the network detections and 2 predicted locations were produced by the framework. 
<p>

## Future work

New spatial localization algorithm needs to be developed.

More field experiments need to be conducted to evaluate the network performance. 

## Reference

[1] A. Mesaros, T. Heittola, T. Virtanen, and M. D. Plumbley, “Sound event detection: A tutorial,” IEEE Signal Processing Magazine, vol. 38, no. 5, pp. 67–83, 2021. 

  
## Appendix
  
### A1. The classes used to train the Mel_BiLSTM model
  
![alt text](https://github.com/dnl2k/Mel_BiLSTM/blob/main/Figures/Bird_B_Sample.png)

<p align="center">
Figure A1_1: An example of Bird_B signature with STFT spectrogram (left) and normalized Mel features (right). Bird_B signature is commonly mistaken with quail signature.  
<p>
  
![alt text](https://github.com/dnl2k/Mel_BiLSTM/blob/main/Figures/Bird_C_Sample.png)

<p align="center">
Figure A1_2: An example of Bird_C signature with STFT spectrogram (left) and normalized Mel features (right). Bird_C signature is commonly mistaken with quail signature. 
<p>
  
 ![alt text](https://github.com/dnl2k/Mel_BiLSTM/blob/main/Figures/Bird_D_Sample.png)

<p align="center">
Figure A1_3: An example of Bird_D signature with STFT spectrogram (left) and normalized Mel features (right). Bird_D signature is commonly mistaken with quail signature. 
<p>
  
 ![alt text](https://github.com/dnl2k/Mel_BiLSTM/blob/main/Figures/Coyotes_Sample.png)

<p align="center">
Figure A1_4: An example of Coyotes signature with STFT spectrogram (left) and normalized Mel features (right). Coyotes signature is commonly mistaken with quail signature. 
<p>
  
 ![alt text](https://github.com/dnl2k/Mel_BiLSTM/blob/main/Figures/Quail_A_Sample.png)

<p align="center">
Figure A1_5: An example of Quail_A signature with STFT spectrogram (left) and normalized Mel features (right). Detecting Quail_A signature is the main objective of the framework. 
<p>
  
 ![alt text](https://github.com/dnl2k/Mel_BiLSTM/blob/main/Figures/Quail_B_Sample.png)

<p align="center">
Figure A1_6: An example of Quail_B signature with STFT spectrogram (left) and normalized Mel features (right). Detecting Quail_B signature is the secondary objective of the framework. 
<p>
  
 ![alt text](https://github.com/dnl2k/Mel_BiLSTM/blob/main/Figures/Quail_C_Sample.png)

<p align="center">
Figure A1_7: An example of Quail_C signature with STFT spectrogram (left) and normalized Mel features (right). Detecting Quail_C signature is the secondary objective of the framework. 
<p>
