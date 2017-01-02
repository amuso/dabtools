# NOTE! Not quite ready yet. Will soon be updated.

# dabtools

## Introduction

This is a fork of basicmaster/dabtools for use on Mac OS X. The aim is to provide a complete working DAB/DAB+ receiver solution for both command line and GUI. The complete suite would consist off:

- rtl-sdr (USB device interface and receiver)
- dabtools (for dab2eti DAB ensemble decoder)
- dablin (actual tuner to select an ensemble channel)
- mpg123/sox or similar to play the received audio stream (MP2 or MP4)

dabtools is work-in-progress set of tools for reception, recording and playback of DAB and DAB+ digital radio broadcasts. It currently supports any SDR tuner supported by the RTL-SDR project.

It is heavily based on David May's "rtl-dab" SDR DAB demodulator, and wouldn't have been possible without their work (and other contributors to those projects).

This Mac OS X port is just tweaks made to the basicmaster/dabtools code which is otherwise untouched.

dabtools currently consists of the following tools:

- dab2eti (receive a DAB ensemble and output an ETI stream to STDOUT)
- eti2mpa (extract an MPEG audio stream from an ETI stream)

ETI is the standard file format for the storage and transport of a DAB ensemble. It is defined in ETSI 300 799.

It consists of a set of fixed-size (6144 byte) frames, each containing 24ms of audio and other data.

## Hardware support

Note that there is currently no way to specify the device. dab2eti
will use the first RTL-SDR device it finds.

dab2eti on an RTL-SDR requires around 68% CPU (on an Intel(R)
Core(TM)2 Duo CPU E6750 @ 2.66GHz) to decode the BBC National DAB
ensemble.

There is an experimental Viterbi decoder (which uses x64 SSE
instructions, so will only compile for x64) which can be enabled by
uncommenting the "ENABLE_SPIRAL_VITERBI" line in the Makefile.  Please
ensure you do a "make clean" whenever changing this option.

This Viterbi decoder gives a massive performance boost, reducing CPU
usage from about 68% to about 28% when used with an RTL-SDR device.


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

dabtools requires librtlsdr (https://github.com/amuso/rtl-sdr) and
libfftw3-dev (https://github.com/amuso/libfttw3).

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
