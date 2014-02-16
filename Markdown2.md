#moboNet: A Home Automation Experiment

- - -
###Development Log
- - -
####December 1st, 2013
I have been able to succesfully implement the DFT Matrix but it currently is only 11 terms of which i only calculate the first 5. There are a few concerns i need to take into account in order to optomize this.

* Number of terms increases resolution but also increases computation time.
* Decreasing the sampling frequency will allow greater resolution in the freqnecies im interested in (80-3000Hz) but will also incrase computation time.
* Applying a window will clean up the transform but will again increase the computation time.

For the preliminary test i using a 32-bit ARM SAM3X made by ATMEL (arduino due board). This board will handle the computations much more easily but i wanted to have somthing overpowered for the initial proof of concept which i can then scale down to the 8-bit AVR ATMEGA 328. 
####November 24th, 2013
I have gotten the high pass filter working well but the results leave much to be desired. Creating an interesting pattern for high frequencies is much different than that of the low pass filter analysis which just follows the general magnitude of low frequencies to find a beat. I previously explored the possibility of taking a DFT of the signal but it was too time consuming. However if there is a set sample length i should be able to use a static DFT matrix to computer the DFT. Having access to a DFT would allow me to make much more complicated and interesting responses to the higher frequencies.
####November 21st, 2013
I've been able to determine for sure the source of the noise in my circuit. After placing caps and inductors all over the place i have determined that the slight voltage drop due to the PWM of the microcontroller is the cause. I had assumed before that the regulator would be fast enough to compensate but i misjudged it. I am now using two 5 volt regulators, one for digital and one for analog and the results are good. Now i can finally nail down high frequency response.
####November 12th, 2013
I am still having problems with noise when i use a HPF and simultaneously use PWM to control the LEDs. I have known for a while that the PWM causes noise on the supply but i previously assumed it was due to the large current draw from the LEDs. My solution was to up the supply voltage to 9 volts, which also served the purpose of letting me use two LEDs in series. By doing this, the LDO regulator would no longer dip and cease regulating. Even after this change, there is still considerable noise propagating through the analog circuitry as can be seen here.

<p align="center"><iframe width="640" height="360" src="//www.youtube.com/embed/A965EX2jdWM" frameborder="0" allowfullscreen></iframe></p>

These problems are a learning experience and take me a while to figure out considering the entirety of my testing equipment consists of a multimeter and scope software on an Arduino.
####August, 2013
I switch from using my previous method outlined in the summer section to digital filtering to give me a better information about the signal and create a more interesting algorithm.
####Summer, 2013
*These notes were from a very early and rough attempt*
I have begun to implement the music detection system. The basis for the whole system is contingent upon the frequency response of the low pass filter. I have chosen a simple filter with passband edge around 300 Hz. In this design a large gap between the passband and the stop-band is desired because of how i plan to implement the software side of the music detection. Here is a rough graph of the frequency response:

                        |H(w)| for LPF (1st order fc = 300Hz)
              (dB)
             0  |x x x x x x x x x x x 
                |                      x 
            -5  |                        x 
                |                          x
            -10 |                            x
                |                              x              
            -15 |                                x               
                |                                  x         
            -20 |                                    x           
                |                                      x         
            -25 |                                        x            
                |                                          x            
            -30 |                                           x                  
                |                                              x      
            -35 |                                                x      
                -------|----------|-----------|------------|----- Hz (log)
                0     10         100        1000         10000   

The basis for the software side is having an understanding of the filters response and using this to determine whether or not the input signal is a Low, Mid or High. I am doing it this way to avoid any additional circuitry because i want a small package. It is easiest to detect the beat and very low frequencies because these are the largest in magnitude. I want a large gap between the passband and stop-band edges because i want to be able to detect other frequencies at attenuated magnitudes. To overcome the problem of detecting loud mids as a bass and other situations, i take an average of the last 64 samples as a reference. Given that reference the respective frequencies will then be function of the average unique to those frequency bands. To obtain a reference i sampled the desired frequency at different volumes IE different  values of the variable "ave". I then applied best fit lines and use them as cutoffs to determine which band a sample should be classified to. The best fits i have found are: 
    Lows  (0-230Hz)   Threshold = 67*log(ave-1)-10
    Mids  (230-800Hz) Threshold = 25*log(ave-1)-5
    Highs (800+Hz)    Threshold = 12*log(ave-1)-5

It is much more difficult to detect a high with this filter because signals higher than 800 Hz are attenuated more severely beginning with 800 Hz at -9 dB. The other method for doing this type of thing would be with a ~6000 Hz LPF and performing an FFT which i will do to see if its viable given the constraints.

My preliminary DFT implementation with a 128 sample input and no special windowing takes around 2.5 seconds per DFT which is much too long. When N is 32, it takes about 155 mS. This would mean a sampling rate of T > 155 mS which a sampling frequency of less than 6 Hz which is pretty much useless. The to attain a Nyquist frequency of a signal of 2000 Hz i need to have a sampling frequency of 4000 Hz which means the DFT finish in < 250uS. I have found an FFT library for Arduino but it takes more than 600 uS to complete the FFT. This means i will continue with my previous method of implementing the lump spectral analysis.