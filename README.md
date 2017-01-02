# dabtools

## Introduction

This is a fork of basicmaster/dabtools for use on Mac OS X. The aim is to provide a complete working DAB/DAB+ receiver solution for both command line and GUI. The complete suite would consist off:

rtl-sdr (USB device interface and tools)

dabtools (for dab2eti)

dablin (tuner)

mpg123/sox or similar to play the audio stream

dabtools is work-in-progress set of tools for reception, recording and playback of DAB and DAB+ digital radio broadcasts. It currently supports any SDR tuner supported by the RTL-SDR project.

It is heavily based on David May's "rtl-dab" SDR DAB demodulator, and wouldn't have been possible without their work (and other contributors to those projects).

dabtools currently consists of the following tools:

dab2eti - receive a DAB ensemble and output an ETI stream to STDOUT

eti2mpa - extract an MPEG audio stream from an ETI stream.

ETI is the standard file format for the storage and transport of a DAB ensemble. It is defined in ETSI 300 799.

It consists of a set of fixed-size (6144 byte) frames, each containing 24ms of audio and other data.

## Hardware support

Note that there is currently no way to specify the device - dab2eti
will first look for and use a /dev/wavefinder0 device, and if that is
not found, it will search for RTL-SDR devices.

dab2eti on a Wavefinder requires around 51% CPU (on an Intel(R)
Core(TM)2 Duo CPU E6750 @ 2.66GHz) to decode the BBC National DAB
ensemble.

dab2eti on an RTL-SDR dongle requires around 68% CPU on the same CPU
and ensemble.

There is an experimental Viterbi decoder (which uses x64 SSE
instructions, so will only compile for x64) which can be enabled by
uncommenting the "ENABLE_SPIRAL_VITERBI" line in the Makefile.  Please
ensure you do a "make clean" whenever changing this option.

This Viterbi decoder gives a massive performance boost - reducing CPU
usage from about 51% to about 7% when used with a Wavefinder, and from
about 68% to about 28% when used with an RTL-SDR device.


Note however that in every 5th transmission frame the Wavefinder skips
the FIC symbols and doesn't provide them to the host computer.  This
means that an ETI file created from a Wavefinder will be missing the
FIBs in 4 frames out of every 20 (dab2eti writes FIBs with 100%
padding in their place).

### Psion Wavefinder

The Psion Wavefinder is a USB DAB receiver sold between 2000 and 2002.
The COFDM demodulation is performed on two DSPs and the samples are
then transferred over USB for further software processing on the host
computer.

The Wavefinder has a built-in antenna, which performs very well in my
testing - much better than any of the small telescopic aerials
commonly provided with the RTL-SDR dongles.

To use dabtools with the Wavefinder, you need to build and install the
driver in the wavefinder-driver directory.  The DSP firmware is
included in the wavefinder.fw file in that directory and must be
copied to /lib/firmware/wavefinder.fw

Note that this firmware file is simply the concatenation of the
rsDSPa.bin and rsDSPb.bin files included in the original Windows
driver.

This driver is an extended version of David Crawley's driver included
with OpenDAB, but with the low-level functionality from the OpenDAB
application moved into the driver in order to provide a higher-level
API.

dab2eti is used to receive an ETI stream, and the frequency is
specified in Hz.  e.g.

./dab2eti 218640000 > dump.eti

to record a stream or

./dab2eti 218640000 | eti2mpa 2 | madplay -v -

to play sub-channel 2 from the ensemble.


### RTL-SDR devices

dab2eti should work with any device supported by librtlsdr, but it
seems that the E4000 tuner is the only one (amongst those I have
tested) which is able to lock onto a DAB ensemble using auto-gain.
For my other devices (one with an FC00013, and one with an R828D), I
need to very carefully set the gain manually.

To set the gain, you use the optional second parameter to dab2eti,
which is the gain specified in tenths of a decibel.

e.g. to record an ensemble broadcasting at 218.640MHz with 9dB gain:

./dab2eti 218640000 90 > dump.eti

dab2eti will display the list of supported gain values - each tuner
supports a different set of gain values.


## Building

dabtools requires librtlsdr and libfftw3.  The former can be found at
http://sdr.osmocom.org/trac/wiki/rtl-sdr and the latter should be
available via your distribution's package manage (e.g. libfftw3-dev).

## Other ETI tools

OpenDigitalRadio maintains a list of other open source ETI tools here:

http://wiki.opendigitalradio.org/Ensemble_Transport_Interface

## Copyright

dabtools is written by Dave Chapman <dave@dchapman.com> 

Large parts of the code are copied verbatim (or with trivial
modifications) from David Crawley's OpenDAB and hence retain his
copyright.

The RTL-SDR code is (C) David May.

This program is free software: you can redistribute it and/or modify
it under the terms of the GNU General Public License as published by
the Free Software Foundation, either version 3 of the License, or (at
your option) any later version.

This program is distributed in the hope that it will be useful, but
WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the GNU
General Public License for more details.

You should have received a copy of the GNU General Public License
along with this program.  If not, see <http://www.gnu.org/licenses/>.
