# mcast

## Description

This is a command line utility and library written to test out multicast
traffic flows on the network, and stress test the network and devices.

This utility is capable of entirely disrupting an L2 environment that isn't
robustly configured, so caution is urged.

mcast is a command line utility capable of sending and recieving multicast
or generic UDP traffic. It also allows simulation of IGMP joins, leaves, and
querying.

__NOTE:__ Currently developed and tested on Mac OSX 10.13.3 with Go version 1.10.
The program should work on any Posix / Unix, but currently doesn't fully work on
Windows. Superuser rights may be required for some functionality.

## License

This repository is licenses under GPLv3. See [LICENSE.md](./LICENSE.md) for details.

Copyright (C) 2018 Will Smith

This program is free software: you can redistribute it and/or modify
it under the terms of the GNU General Public License as published by
the Free Software Foundation, either version 3 of the License, or
(at your option) any later version.

This program is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
GNU General Public License for more details.

You should have received a copy of the GNU General Public License
along with this program.  If not, see <http://www.gnu.org/licenses/>.

## Quickstart

You must have go installed. If you don't, or you're not sure, visit [https://golang.org/doc/install](https://golang.org/doc/install). You must also be running a supported and tested
OS and Go version.

Install program with

    go install github.com/individuwill/mcast

Or alternatively download pre-built binaries from releases [v0.1](https://github.com/individuwill/mcast/releases/download/v0.1/binaries.zip)

Run the receiver on one computer

    mcast receive

Run the sender on another computer

    mcast send

Multicast routing must be enable between hosts

## Usage

mcast is driven by subcommands. You invoke mcast like:

    mcast subcommand [-options...]

The list of subcommands are:

* help
* send
* receive
* join
* leave
* query

Each subcommand then has a set of options to control its behavior. Many of
the commands share similar options, and the option syntax is the same when
this is the case. Below is a detailed explanation of each subcommand and
its associated options.

### help

Display help and usage message for the application

    mcast help [command]

Help for a specific subcommand can be shown by specifying the command you want
help with.

### send

Will send UDP traffic to the IP address specified. Will send continuously
in a loop at specified interval until the program is terminated or max number of messages
are sent.

    mcast send [-options...]

The options are:

* -group : IP destination address. Can use CIDR notation to send to multiple addresses.
  * default : 239.1.1.5
* -port : Destination UDP port
  * default : 5050
* -interface-ip : Interface to use defined by IP addrress. Must be in 0.0.0.0:0000 format. Default allows system to decide.
* -ttl : IP ttl (time to live)
  * default : 50
* -tos : TOS / DSCP to be set. Only works on unicast addresses. 0xB8 for voice.
  * default : 0
* -text : Text / data to send to the receiver. Use '{c}' to access counter
  * default : This is test number: {c}
* -padding : Length to pad the message. Will make message take up specified number of bytes.
  * default : 0
* -interval : Interval between sending messages (milliseconds).
  * default : 1000
* -start-value : Non-negative start value message incrementer / counter
  * default : 1
* -max : Number of packets to send. '0' for continuous send
  * default : 0

### receive

Will listen to UDP traffic on the IP address specified and print out the text
content of the received UDP messages if the option is enabled.

    mcast receive [-options...]

The options are:

* -group : IP multicast destination address. Can use CIDR notation to listen to multiple multicast address.
  * default : 239.1.1.5
* -port : Listen UDP port
  * default : 5050
* -interface : Interface name to listen on. Default allows system to decide.
* -show : Print the text contained in the received UDP message.
  * default : true

### join

Not implemented yet

### leave

Not implemented yet

### query

Not implemented yet

## Testing

Some basic code tests are currently present in the repository, but much more
extensive coverage is needed.

You can manually execute the tests with 1 of the following commands:

    go test ./..
    go test github.com/individuwill/mcast
    go test github.com/individuwill/mcast/multicast

There is a Jenkins file in the root of the repository for automatically running
the tests, cross-compiling for 3 operating systems, and creating a zip artifact.
There is a git-hook in the repo that runs this on-commit, and it will mark the
commit as pass or fail.

The plan is to test the code on the 3 operating systems, and test the interaction
between sending and receiving multicast in the jenkins cluster.

There is also a plan to automatically create a new release with binaries when
a new tagged commit to master is made and it passes the tests.

Currently only the code tests are executed. They are executed in a linux
environment through the official golang docker container. The binaries are stored
in Jenkins, and manual releases must be made.

## Background

I wrote this program to test multicast functionality in my network designs as I found
existing tooling for testing multicast lacking. I needed a small portable binary
that I could copy to any host for quick testing. This is especially useful for
virtual lab environments.

I also had a need to test and be able to observe traffic behavior in wireless
networks to understand vendor configuration options that lead to the most robust
multicast configuration for a wireless environment. To this point, with the correct
set of options with this program, a single host can cause a DoS to an entire wireless
segment if the wireless infrastructure isn't properly configured or the vendor does
not provide the ability to configure or control multicast behavior.

I took the opportunity to use this program to learn more about multicast and
do some coding in Golang.

I used the [https://github.com/troglobit/mtools](https://github.com/troglobit/mtools]) suite for initial testing of the program while developing it. That toolset was also inspiration for mcast. I chose not to extend
mtools as I wanted easy concurrency and easy cross compiling and cross platform binaries.

### Other tools for testing multicast

Here are some other tools I use or used for testing multicast functionality

* [VLC](https://www.videolan.org)
* [iPerf](https://iperf.fr/) (version 2)
* [Wireshark](https://www.wireshark.org/)
