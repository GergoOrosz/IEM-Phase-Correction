# IEM-Phase-Correction

# Minimum phase enforcement filter for Shure SE846

In this project, a digital Finite Impulse Response (FIR) filter is developed to convert the phase response of the popular Shure SE846 In-Ear monitor to minimum phase behaviour. The aim is to improve the sound quality of these already excellent IEMs by better aligning the sound output of its three balanced armature drivers.
The FIR filter posted in this repository can achieve this without affecting the magnitude response of the IEMs. Accordingly, all changes/improvements in the tonality of the SE846 are achieved through improved synchronisation.

To try this, the FIR filter should be loaded into a DSP convolution engine in the audio playback system. Convolution engines are common for room correction and can be found in systems such as Roon and foobar2000, but the required convolution can also be implemented in DAW software using convolution, room correction, or reverb plugins. In any reverb plugin, it is critical to mute the "dry" signal path - all of the music should be passed through the "wet" channel. It should also be possible to implement this filter on a dedicated digital signal processing system such as MiniDSP.

The FIR filter is 40000 taps long, sampled at 32bit/48kHz. Most convolution engines will handle sample rate conversions if necessary.
The FIR filter has been tested in Roon's MUSE DSP system and in Reaper DAW with the ReaVerb plug-in.

# Why does the phase response matter?

The phase response of an audio reproduction system describes its timing characteristics as a function of frequency. It is often desirable to maintain a minimum phase response, which means that the system's phase response is entirely determined by its magnitude response. In other words, the phase shift is as low as possible. Since group delay is the negative derivative of phase with respect to frequency, a minimum phase system also has minimum group delay and accordingly the cleanest transient response.

A non-minimum phase, or mixed phase system has (in most cases) undesirable mechanisms that introduce further group delay that may vary across the system's operating frequency range and thus adds time-domain distortion to the reproduced signal.

Like many multi-driver IEMs on the market today, the SE846 is a 3-way design. It has a dual subwoofer, a dedicated midrange, and tweeter Balanced Armature (BA) drivers. Crossovers and filtering are achieved acoustically on the subwoofer and electrically for the midrange and the tweeter.
Looking at individual balanced armature drivers, even when they are connected to their corresponding crossover components, the expected phase characteristics are likely very close to minimum phase. However, when the low, mid and high stages are connected in parallel, the minimum phase behaviour is lost.
Generally, one can cascade (connect in series) stages, but as soon as they are connected in parallel, the minimum phase quality cannot be maintained in most cases.

This makes sense, as for instance, the group delay characteristics of low-pass and high-pass filtering in their respective pass-band is different. When the two signals are added together, the high-passed signal components will likely be 'faster' than those that went through the low-pass filter.
The issue can be illustrated looking at the SE846's measured impulse response (IR). The minimum phase response can be calculated from the measured frequency response magnitude and thus the 'ideal' minimum phase IR of the SE846 can be constructed digitally.

<img width="1266" alt="image" src="https://github.com/user-attachments/assets/4dabbd28-f70d-483c-ad92-f0dd66f336ed" />

Figure 1: Measured Impulse response of SE846 (Left), 'Ideal' Minimum phase impulse response of SE846 constructed using the measured frequency response magnitude (Right).
Comparison of the two impulse responses shown in Figure 1 confirms that the SE846's response is indeed non-minimum phase. In the real measurement, the main impulse part of the response appears to be split into a leading negative part and then a positive peak. In the ideal reconstruction, there is a much cleaner single positive peak in the response.

Using some digital filtering, it is possible to extract the approximate low-midrange and tweeter impulse components. Figure 2 shows this decomposition, achieved with 3 kHz low and high pass filters, noting some imperfections that are due to the filtering process and potentially inexact crossover frequency.

![image](https://github.com/user-attachments/assets/6609c10a-237a-4d8f-aea0-97fc1279e9be)

Figure 2: IR decomposition (Midrange+sub / Tweeter), Shure SE846

It is evident from the decomposition in Figure 2 that the high frequency components from the tweeter arrive at the eardrum approximately 0.1 milliseconds earlier than the lagging lower frequency components. It is also apparent that the tweeter is wired in opposite polarity to the midrange and sub drivers. The latter is common practice to avoid cancellation in the magnitude response around the crossover region; however, it does mean that the low-mid and high frequency components are technically 180 degrees out-of-phase.

# The effect of the phase correction FIR filter

The aim of the proposed FIR filter is to align the drivers in time and to eliminate the opposite polarity behaviour seen on the tweeter with respect to the other two drivers. Figure 3 shows the impulse response decomposition of a measurement performed with the phase correction filter active in the audio playback chain.

![image](https://github.com/user-attachments/assets/b7520e93-77d9-4f92-9616-350100e38ad2)

Figure 3: IR decomposition (Midrange+sub / Tweeter), Shure SE846 with the Phase correction filter applied to the audio playback chain.

Figure 3 shows that the phase correction filter successfully achieves the objectives of this project. The Midrange+sub and Tweeter components are aligned in time and both appear to have the same polarity. The resulting impulse response has a single/clean positive impulse component that is very similar to the digitally reconstructed minimum phase response. Also, before the main impulse peak, pre-ringing appears to be minimal

Applying this filter of course results in some added latency, but looking at the impulse response, there should be an improvement in the sound signature, particularly in the transient response of the SE846.

# How does this sound?

To my ears, the effect is subtle but quite notable. Vocals particularly sound more focused and the imaging, instrument separation, and reverb get clearer. Interestingly, the tonality also changes a bit, particularly the bass sounds punchier, despite the fact that the filter does not alter the magnitude response of the SE846s at all. To me, this confirms just how important the phase response of audio systems can be and shows that the frequency response magnitude does not tell the full story of an IEM.
I would like to invite everyone reading this who can try it on their Shure SE846s to share their thoughts on the potential subjective improvements in the sound. Of course, it is important to do any evaluations using good quality recordings.

Please note that this filter will not work as intended on any other IEM models.

Any questions, please get in touch in the comments.


# Example Setup in Roon to Run the FIR Filter for Shure SE846
To apply the FIR filter in Roon's DSP engine, follow these steps:

1. Open Roon and Navigate to DSP Settings

   Launch Roon and play any track on your Shure SE846 setup.
   Click on the volume icon in the bottom-right corner of the Roon interface.
   Select DSP from the menu (sine wave icon).

2. Add a Convolution Filter
   In the DSP Settings menu, click on Add Filter and select Convolution.
   Click on the gear icon next to Convolution to open the settings.

3. Load the FIR Filter

   In the Convolution settings, click Browse to upload the FIR filter file.
   Make sure you have the .wav or .zip file containing the FIR filter provided in this project.
   Roon will automatically recognize the sample rate (48 kHz in this case) and apply it correctly.
   
   
4. Save and Test

   Save the DSP settings and return to playback.
   Listen to your music and evaluate the changes in sound quality, especially in clarity, imaging, and transient response.
   If you want to compare the sound with and without the FIR filter, toggle the Convolution filter on and off in the DSP settings.

# Setup in Reaper (DAW) Using ReaVerb Plugin

1. In Reaper, load the track or audio you want to process. Alternatively, create a virtual loopback from your computer's audio out to the Reaper track.
2. Click on the FX button of the track and add ReaVerb from the plugin list.
3. Load the FIR Filter:
   In ReaVerb, click Add > File.
   Select the FIR filter file and load it.
4. Mute the Dry Signal Path:
  In ReaVerb, ensure the Dry Signal slider is set to -inf dB (muted).
  Only the processed (wet) signal should pass through, this can be set to 0 dB.
5.  If you want to compare the sound with and without the FIR filter, toggle the Convolution filter on and off in the track settings.




