---
title: My Internship Experience at IITB
date: 2025-07-18
tags: ["post", "internship"]
layout: post.njk
permalink: "blog/intern/index.html"
---

## I Tried Sending Music Over RF... and It Was Pure Chaos (at First)
This internship project turned out to be way more than I expected. When I started, the goal sounded simple: take a music file, transmit it using an FPGA and SDR setup, and receive it wirelessly. Easy, right?

*Nope. Not even close.*

### The Plan (in My Head)
The idea was:
1. Use GNU Radio to take a .wav audio file.
2. Convert it to I/Q samples (interleaved).
3. Send it over UART at 1 Mbps to an FPGA (MAX 10).
4. Use the FPGA to drive analog outputs for I and Q using R-2R DACs.
5. Feed that into an IQ modulator board sitting at around 1.121252 GHz.
6. Receive it all with an RTL-SDR dongle.
7. Demodulate and recover the original audio.

Sounds logical. Looked nice on paper. But in reality? 😂

The First Steps: Getting started with an FPGA
I had never worked with an FPGA previously so first thing was to install the required software, undoubtedly Quartus (I have seen my college mates working with it so was all cool😌).
So once everything was set up, I thought of flashing a basic inverter code just to verify everything was working fine. That's when I got to know that Quartus won't recognize the hardware cable attached. I went on to find out a solution and I was introduced to UrJTAG. (I was familiar with JTAG, but what is UrJTAG?)

[UrJTAG](https://urjtag.sourceforge.io/book/_jtag_commands.html) is a free, open-source command-line tool for communicating with devices over JTAG, offering enhanced features for interacting with flash chips, CPUs, and more. 
I had to generate an svf file of my verilog code and then flash it through UART.

The Second Step: Write a UART receiver code for FPGA and a python script with sampled values for transmission
This stage was pretty easy as I had a bit of experience with Verilog. But yes, I learnt about FSMs(Finite State Machines) to optimize the code flow. Once the code was ready, I could see LEDs blinking as values were sent through Python script. But yes, I had to optimize the baud rate and delay to get higher frequencies at the output. I used a R-2R ladder at the FPGA output to get analog signals. Here's the output:
![](/src/assets/img/blog_images/iitb_intern/sine_wave.jpeg)

The Third Step: Generating samples through GNU Radio
GNU Radio is a software development toolkit that provides signal processing blocks to implement software-defined radios and signal processing systems. I spent sometime navigating though the available blocks and then made the first flowgraph. GNU Radio allows adding custom python blocks so I just had to write a similar python code for tranmission. The only difference was the generation of the signal (comparing it with the previous stage). This was the flowgraph:
![](/src/assets/img/blog_images/iitb_intern/sine_ip.png)
I have used a Hilbert Transform block as I had to generate I/Q signals so required a 90 degree phase shift. I also tried transmitting a audio file. The flowgraph is mostly the same, just the generation is through a .wav file. I converted an .mp3 to .wav using ffmpeg, quite simple.
![](/src/assets/img/blog_images/iitb_intern/wav_ip.png)

I observed the outputs on a DSO as well as by connecting speakers at the DAC output.

The Fourth Step: Receiving it through RTL-SDR and observing it on GNU Radio
Transmission was all fine but reception, not at all!

The Noise Begins
I spent days wondering why the output audio sounded like TV static from the 90s. Was it baud rate mismatch? Was UART too slow? Was I sending the samples wrong? Or maybe the issue was in the receive path - wrong center frequency, wrong gain, bad filtering?

Spoiler: it was all of the above.

It turned out that when I sent a single tone - say, a 1 kHz sine wave, everything worked fine. I could hear it on the output after demodulation. But real music (which is a mix of thousands of frequencies) completely broke the system.

### The Real Troubleshooting
So I began debugging everything:

UART bitrate: I set it to 88200 baud to match my 44.1 kHz audio, since each sample had 2 bytes (I and Q interleaved).

Filtering: I added band-pass filters on both ends, before modulation and after demodulation, to keep only the relevant audio band.

Gain: I kept fiddling with RF, IF, and baseband gain on the RTL-SDR until it stopped clipping.

Resampling: The most annoying part. I had to match the GNU Radio and FPGA timings so that I don’t flood the UART or underflow it. It took a LOT of tweaking.

A Few Wins
There were moments where things just clicked. Hearing that first tone come back kind of audible felt great. 

## Final Thoughts
I started off thinking this was going to be a small summer project. It ended up being a full deep dive into DSP, SDRs, FPGAs, UART bottlenecks and just general signal chaos. There’s still a long way to go - cleaner audio, real-time streaming, stereo support maybe - but I’m happy of what I managed to get working.

I owe a deep gratitude to Prof. Siddharth Tallur who gave me an opportunity to work on this project which helped me gain fruitful knowledge in digital signal processing and Verilog HDL. 