# sensible-how-to
How-to for first-time users of the Sensible framework

## Introduction
This is a simple how-to for those of you new to the Sensible framework.
It is a rather simple guide that will walk you through downloading and setting up Sensible, running some code, making some changes, and running some more code.
The core platform is based on open web standards, as championed by Mozilla.

## Who be you?
This is for the intrepid folk who know some html & css, perhaps some javascript, arguably familiar with the command-line, and chock full of curiosity.

## What you need?
A computer (assumption here is Mac running OSX; if you are using Linux, this is too basic for you, and if you are using Windows, well then you'll just have to improvise), a network (wifi works), maybe a smarphone, and an intertubes connection.

# Setup
* Download and install Sensible: http://sensible.mono.hm/
 * Unzip Sensible folder into someplace obvious, for example /user/renfield/
* Download and install Mozilla Firefox: https://www.mozilla.org/en-US/firefox/
 * Open Firefox -> Tools -> Web Developer -> WebIDE
 * In the top right corner Select Runtime -> Install Simulator -> Firefox OS 2.0 Simulator (stable) -> Click INSTALL button
* Download and install node: https://nodejs.org/download/

You also need a terminal (the standard OSX app "Terminal" works, though iTerm is pretty great too: http://www.iterm2.com)

## Gnarly Yosemite Bug Warning
There a known and unpleasant bug in Yosemite around mNDS networks. Basically, it's broke.
mDNSResponder used to work, but it's been replaced by discoveryd, and it sometimes doesn't work.
The fix is...both not really a fix, and non-pleasant:
Type following command in the terminal to disable discoveryd:<br>
`sudo launchctl unload -w /System/Library/LaunchDaemons/com.apple.discoveryd.plist`

Note that if you do the above, this sensible how-to will work, but your internet connection will likely be broken until you restore discoveryd with:<br>
`sudo launchctl load -w /System/Library/LaunchDaemons/com.apple.discoveryd.plist`

Which then of course means this sensible how-to will break. Sigh, aren't OSX bugs awesome?

# Fire it up!
The first thing we're going to do is run some of the example code, to get an idea of how sensible works.
The basic theory of sensible is pretty simple.
Every sensible thing (as in "Web of Things" thing) does two things:

1. DISCOVERY (via what is called the "wrapper" application)
1. INTERACTION (via what is called the "service" application)

_Discovery_ uses [multicast DNS](http://en.wikipedia.org/wiki/Multicast_DNS) (aka Bonjour on OSX) to:
* Announce it's existence to the network
* Find others on the network

_Interaction_ means responding to HTTP requests with HTTP responses with either:
* HTML/Javascript or
* JSON data

Let's take a look at an example, so that we can match the service names to the code.

## The Concept
First, imagine a Web of Things jukebox. It is a device, say sitting on your living room shelf, that has some basic networking capabilities, enough computing power to run the sensible technology stack, and maybe an amplifier and some speakers.
First, it announces itself on the network, basically broadcasting "Yo! I'm a jukebox! Come ask me what music I have! Ask me to play some tunes!" This is DISCOVERY.

Imagine you pull out your sensible-enabled smart phone. It has a browser, network capabilities, etc. So you look around on the network at find the Jukebox. You click on the Jukebox and it tells you the songs it has in its library, and give you a simple UI to cue up tracks.
You select a song and add it to the playlist.
The Jukebox, seeing a song on its playlist, starts playing that song.
This in INTERACTION.

Imagine your buddy comes to your house with his sensible-enabled smart phone. He pulls out HIS phone, connects to the Jukebox, selects a few tracks, and adds them to the playlist.

Imagine you are having a party at your house; tens of friends with their sensible-enabled cell phones, connecting to your jukebox and cueing up songs -- it's a crowd-sourced party playlist!

## The Example Code
OK, you get conceptually how it works; a Web of sensible-enabled Things, finding each other and interacting through established internet protocols. Now let's match the concept to the code.
We are going to do two things to replicate the conceptual interaction above:
1. Your computer is the Jukebox
2. The Firefox OS 2.0 Simulator (running in Firefox's WebIDE on your computer) is your sensible-enabled smart phone (so just pretend it's actually your smart phone that you pulled out of your pocket, not the same computer as your jukebox!)

### jukebox
First, the jukebox.
1. Open up your terminal and go to `/sensible/apps/node/jukebox/` by typing<br>
`cd /sensible/apps/node/jukebox/`
2. Start up the Jukebox by typing<br>
`sensible-app.command`
You should see something like
```
changing directory to /Users/renfield/sensible/apps/node/jukebox
ApplicationFactory.createApplication()
sensible.Application()
WebSocket server listening
StrategyFactory.createStrategy()
making node strategy
sensible.MDNS.registerService(Jukebox)
server advertised as Jukebox._sensible._tcp.local:10.66.48.90:3003
node.Application.registerHost()
Jukebox onAfterStart() called
Application.loadDirectory(music)
```
If instead you see:
```
changing directory to /Users/renfield/sensible/apps/node/jukebox
ApplicationFactory.createApplication()
sensible.Application()
WebSocket server listening
StrategyFactory.createStrategy()
making node strategy
events.js:85
      throw er; // Unhandled 'error' event
            ^
Error: bind EADDRINUSE
    at exports._errnoException (util.js:746:11)
    at dgram.js:224:28
    at dns.js:85:18
    at process._tickCallback (node.js:355:11)
```
Then congratulations! You have Gnarly Yosemite Bug! (see above).
Type the following command in the terminal to disable discoveryd:<br>
`sudo launchctl unload -w /System/Library/LaunchDaemons/com.apple.discoveryd.plist`

OK, so now your Jukebox is up and running; it's announcing its existence to anyone who will listen.
To confirm, let's use the command line tool lsof ("LiSt Open Files") -- since basically everything (in the command line context) is a "file", we use the -i flag to specify that we want all internet and network files. Since this will return a possibly long list, we'll use grep to filter any lines that contain "node":<br>
`lsof -i | grep node`
You should get something like
```
node      65366 renfield   12u  IPv4 0x65bda10666d97df3      0t0  TCP *:cgms (LISTEN)
node      65366 renfield   13u  IPv4 0x65bda106560c8d53      0t0  UDP *:mdns
```
The second column (65366, but you will see a different number for sure) is the pid (Process ID).
We can confirm that it's our jukebox using lsof with the -p flag (Process) and a grep filter again on the word "jukebox":<br>
`lsof -p 65366 | grep jukebox` (again, use whatever number you see for pid)
Should result in something like:
```
node    65366 renfield  cwd      DIR                1,4       408 8833764 /Users/renfield/sensible/apps/node/jukebox
```

Our jukebox is on the network, announcing its existence and waiting for some friends.
So let's give him a friend!

### jukebox client
Now we'll simulate pulling out our sensible-enabled smart phone, discovering the jukebox, and interacting.
