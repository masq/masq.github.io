---
title: 'How to Make Your Wireshark Not Suck'
date: '2021-07-03'
tags:
	- wireshark
	- tools
	- network
	- packet
	- analysis
	- traffic
	- malware
	- column
	- configuration
	- tool

---

## TL;DR:
* Right-click On the column headers of wireshark.
* Click on "Column Preferences".
* Add 3 new columns. I'll refer to these as A, B, and C.
* Change column A to "Source Port (unresolved)" and name it "src.port"
* Change column B to "Destination Port (unresolved)" and name it "dst.port"
* Change column C to "custom", and name it "host"
* Under the "fields" for C, put in
	* http.host
	* dns.qry.name
	* tls.request.handshake.extension_server_name
* Rename the "number" column to "#"
* Rename most of the other columns from e.g. "Source" to just "src"
* Move them around as you'd like, my preferred order is
	* # (number)
	* ts (timestamp)
	* src.addr (Source Addr (unresolved))
	* src.port (Source Port (unresolved))
	* dst.port (Destination Port (unresolved))
	* dst.addr (Destination Addr (unresolved))
	* host (custom)
	* proto (Protocol)
	* len (length)
	* info (Info)

## Wireshark
What is wireshark? Wireshark is a great program that allows you to look at
network traffic, either live off-the-wire or through previously captured network
traffic in the form of a `.pcap` or `.pcapng` file.

### Downloading and the Code
TODO TODO TODO: ensure links are right and good
You can [get wireshark on the wireshark.org website](https://wireshark.org/download)
for free! Additionally, it supports pretty much any platform which is great. It
is written in C and is FOSS with it having a [github
repo](https://github.com/wireshark) so you can look at the
source code or tinker around and make your own additions potentially.

## What to analyze?
Network traffic analysis is an amazing way to understand what happened across
many hosts quickly.

## Yeah, but why?
Great question! There's a ton of reasons you might want to analyze some network
traffic.

### Debugging
Have you ever been trying to reach some server and the host just will not
resolve, and then you try to get to any other address, you know, to see if you
can get to any host? But then you realize that you can't resolve anything all of
a sudden. Annoying, but it happens. So you check your resolv.conf or somsething
and test to see that your DNS servers are properly set... and they are. Now it's
weird. Are you getting attacked, are you even sending packets at all? This is
where wireshark / tcpdump / tshark can come in handy. I've done this a few
times, where I expect a DNS request to resolve, but seem to be unable to. I've
had it happen where my DNS requests are not going over the proper interface
(VPNs, anyone?), amongst other issues.

I've also run into issues with other protocols, such as HTTP. I personally don't
use Powershell much, but I've heard good things and was trying to use it as an
API client for some HTTP/REST based API, and that's when things got weird. I was
using invoke-webrequest using Powershell 5 (i.e. not the "new" "core" powershell,
which I've been emphatically told is FAR SUPERIOR to using the baked-in MS
powershell). Well, I was trying to authenticate against the API, but the damn
thing kept giving me a response like I wasn't authenticated as anyone but an
anonymous user... okay, weird. Well, I was able to whip out my handy dandy
wireshark and debug that mofo. Turns out, in powershell 5, powershell will not
send creds to the server the first POST request, seeing if it "really" needs to
send the creds... smfh, well that doesn't work for me, so I had to develop a
workaround for that, which was a massive PitA, but ah well. Wireshark. It's
great as a debugging tool!

### Malware
Malware can be really obfuscated, and hard to understand what it's doing. Or
it's evasive and hides what it does on a host so well that you can't even see it
unless you do an image of the host using external tools (e.g. a good rootkit).
The network does not lie. In fact it can't lie, otherwise the malware wouldn't
be able to be controlled by its operator and/or communicate out off of the
host. This is one of the beauties of network traffic analysis, it's pretty
matter of fact, though it can be verbose. This said, there are of course means
that people can use to try to hide their traffic, some methods more effective
than others... Perhaps I can write other blog entries on that topic, as I could
probably go on and on about this.

If you haven't, I highly recommend checking out [Brad Duncan's Malware Traffic
Analysis site](https://malware-traffic-analysis.net). Brad gives several
examples of malware and the artifacts it leaves on the network. He also
provides identification and live malware artifacts that he collected! Fantastic
resource of which you should be aware.

### Performance testing
If you've ever had to troubleshoot networking, you'll know that sometimes
routing can be dumb, or someone plugged something in in a way that produces a
feedback loop. I've had it happen before, such that it was actually taking down
a network.

### Visibility / Privacy
Whenever I add a new device to the network, I feel myself conceding some control
of the traffic on my network. For example, my girlfriend brought over an certain
smart speaker that allows you to activate it by name. While this is cool, it
does concern me a bit that I have no idea if the device is acting in a way I
expect it to be acting on my network or not, as I can't get in on it and run
`ps` or pull up Task Manager or something.

With this in mind, we can use packet captures and/or Wireshark to help us
understand what exactly each device on the network is doing. First, we can use the
information that we might glean from our DHCP server or `nmap` to find which IP
on the network our smart speaker occupies. From there, we can place ourselves in
a good place on the network (ARP poisoning to pretend to be the router, or just
have some means of traffic mirroring/SPAN port, or be the router itself so we
sit upstream of the devices attached to our network). Then, from our upstream
position on the network, we can begin capturing! Now, when we first begin
capturing, I recommend doing a full capture, with no capture filters on so we
get the full picture. However, you will generally quickly realize that the
"whole picture" is actually pretty uninteresting and not that valuable for us to
keep on. So, I recommend understanding which parts you will want to capture,
such as HTTP, DNS, TLS, SMB, FTP, and SMTP traffic. Then you'll want to get some
capture filters in place so that we don't collect a ton of garbage. For a more
persistent solution We then might throw some storage at the problem and maybe
even pass all this by some IDS tech like Suricata or Zeek paired with some log
searching platform such as HELK or Splunk or something.

However, for just some light testing, Wireshark is a good tool for the job.

### CTFs / CCDC
TODO TODO TODO TODO: links

If you're unfamiliar with [CTFs](https://ctftime.org), you're missing out.
The TL;DR of them is that they're hacking competitions which are
either jeopardy (TODO: sp???) style, or they're attack-defense
style, where teams will attempt to exploit each other for
flags and defend themselves against the other teams. [CCDC]() is similar to the
latter competition type, except it's students vs professionals and the pros are
the attacks and the students are only defending.

I've been in an attack-defense style CTF where we were able to use wireshark to
watch other teams attempt to exploit us on the network. We could see which
exploits were successful and sending back flags to the enemy teams. Then we
could just take the enemy team exploits and use them ourselves or at least
understand the vulnerability they might be exploiting based on their payload(s).
Wireshark was incredibly valuable for this! (Though, at the time of the
competition, there was a fairly well known DoS vuln in Wireshark that was not
yet patched and teams were definitely taking advantage by spraying the network
with that (in order to crash our Wireshark. Just meant we had to write some
crafy capture filters to get around that).

## Okay, okay, I downloaded it; now what?
Great question! The first thing I have to do with any new installation of
Wireshark is to configure the columns on it. Because frankly, the columns as is
are kinda lame.

As a networking/malware analysis guy, I've figured out what port numbers map to
what services (usually), so I tend to have the ports listed in an unresolved
state, but I would recommend others to also leave them unresolved since that
will allow you to learn/memorize the ports as you use them rather than staying
up and playing with flash cards. That said, I generally have the following
configuration.

`# <Number> | ts <UTC date, as YYYY-MM-DD, and time> | src.ip <Src addr (unresolved)> | src.port <Src port (unresolved) | dst.ip <Dest addr (unresolved)> | dst.port <Dest port (unresolved) | proto <Protocol> | len <Packet length (bytes)> | host <Custom [http.host || dns.qry.name || ssl.handshake.extensions_server_name]> | info <Information>`
_Figure X: Linear listing of wireshark column configuration_

or, as a list of things where top represents left and bottom represents right:
```
# <Number>
ts <UTC date, as YYYY-MM-DD, and time>
src.ip <Src addr (unresolved)>
src.port <Src port (unresolved)
dst.ip <Dest addr (unresolved)>
dst.port <Dest port (unresolved)
proto <Protocol>
len <Packet length (bytes)>
host <Custom [http.host || dns.qry.name || ssl.handshake.extensions_server_name]>
info <Information>
```
_Figure X: Listing of wirehsark column configuration_

I like this configuration because it allows me to see the things I'm typically
most interested in right out-the-gates with an investigations / Incident Response (IR).

For example, source and destination IPs with ports is readily available which is
great so I know what applications on which IPs might have been causing the
traffic (especially if I can line it up with host logs around the same
timestamp).

I like keeping the number along with
the time to help in being able to index into packets using the menus (sometimes
an export of packets requires the index to be specified) as well as understand
how long of a time span we're looking at. Though, to be honest, with these I
tend to also hide them if I kinda know what I'm looking at and I don't need them
that very second (hiding/unhiding is much easier than reconfiguring the columns
/shrug). For example, if I've got a display filter in to narrow in on one src.ip
anyhow, don't really need to see the src ip column... or in cases where I kinda
know the timespan I'm looking at and don't need to see when happened precisely
like in cases where I'm looking at a pcap that takes place over 5 minutes or
something (as opposed to 48 hours for example).

## Conclusion
Wireshark is a really powerful tool, that can be utilized in a ton of different
situations. These situations are various, and having a good column configuration
can help you to work faster and better. As they always say, don't work harder,
work smarter.

