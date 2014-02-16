#moboNet: A Home Automation Experiment

- - -
###Description

This goal of this project was to create an affordable and expandable home network system and along the way learn Java, C#, Matlab, OrCAD, and PSpice. 

- - -
###Video
<p align="center"><iframe width="640" height="360" src="//www.youtube.com/embed/LThQ7OVa-BA" frameborder="0" allowfullscreen></iframe></p>
<small>

- - -
###Diagram

<p align="center"><img src="http://i.imgur.com/7VIPh6O.png" alt="Drawing" style="width: 700px;"/></p>

####Components
#####Client

The clients consist of any device with the means of sending commands to the server module. This means Android application, Desktop application in addition to the door knocker module which sends notifications over the 315MHz network.

#####Server

The server module is an arduino uno with an Ethernet shield which transfers data from LAN to a 418MHz network. I designed a relatively simple communication protocol for the RF links to provide reliable communication from server to light module. 

#####Light Modules

The light module consists of RGB LEDs which receives commands via the 418MHz network. The might module is what sits under the cube in the video and will eventually be equipped to be screwed into a standard light bulb socket.

- - -
###Control
####Android Application
<p align="center"><img src="http://i.imgur.com/ecZfp9x.png" alt="Drawing" style="width: 300px;"/>
<img src="http://i.imgur.com/T7TJgyy.png" alt="Drawing" style="width: 300px;"/>
<img src="http://i.imgur.com/WtuYH2U.png" alt="Drawing" style="width: 300px;"/>
</p>

This is the Android application I wrote to control the light modules from my phone. With this application i can control all aspects of the module in addition to the 4 channels of the RF AC relays. To communicate with the sever it must be connected to the wifi network that the server is on however I am working on a secure way to control the lights from anywhere so that i can control systems when I'm away.

####Desktop Application
<p align="center">![DesktopApp](http://i.imgur.com/YgeQw6P.png)</p>

Written in C# in visual studio, the desktop application operates in the same way as the Android application, by sending UDP packets to the server on the network. 

- - -
###Light Module
####Design

<p align="center"><img src="http://i.imgur.com/KZJO7HZ.png" alt="Drawing" style="width: 500px;"/></p>

####PCB

<p align="center"><img src="http://i.imgur.com/8SJUMXG.png" alt="Drawing"/></p>

####Audio Mode and Signal Analysis
#####Description

This mode is a part of the light module which responds to music, here i will go over how the input signal is generated and processed.

#####Circuit

There are five main components to the Light module. 

+   Control System
+   Power Supply
+   LEDs and current driving circuit
+   Communication
+   Analog audio processing circuit

The control system i use is an ATMega238 which ties all the components together. The power supply for this circuit i use a 9V supply into a 5V regulator for logic. The LEDs i use are two 3W RGB LEDs in series. Each diode is 1W and there are 3 individual LEDs in each package. As for communication i use the RX module of a 418MHz RF link to receive messages from the server. The analog circuit consists of a BJT preamplifier in a common emitter configuration which provides around 40dB of amplification. This is not enough by itself however so i then cascade that into an LM386 amplifier in a configuration providing another 20dB of amplification which puts the voltage into a range easily readable for the ADC on the microcontroller.

#####Frequency Response

<p align="center"><img src="http://i.imgur.com/iS6kP5U.png" alt="Drawing" style="width: 900px;"/></p>

Here are the stages of the analog frequency response. It is generated from the circuit model in PSpice by performing an AC frequency sweep from 1Hz to 1MHz.

#####Digital Sampling and Filtering

After the signal is amplified and filtered, it is then sampled at 20Khz. Further processing is then done through digital filtering. I am using a 30th order FIR filter. This method doesn't provide continuous digital signal but rather a burst of 31 samples every time the audio processing loop is entered. I have found that with the limited processing speed of the ATMEGA328, it is best to sample in this method rather than is interrupt based. Currently there are only two channels, 0-150Hz and 1KHz-10KHz. I hope to push this to more resolution in the future so i can design a music algorithm with more complexity while still staying within a 60Hz loop frequency. When the computations begin to take longer than ~20mS there becomes noticeable jitter. I may yet be able to compensate for this if i need to when adding more channels.

- - -
###Next on the List

+   Scale to incorporate more light module nodes and add controllability for clients
+   Redo PCB to reflect hardware changes
+   Audio Algorithm v.2 based on DFT matrix

- - -
###[Development Log]("../page2.html")