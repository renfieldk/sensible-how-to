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
** Unzip Sensible folder into someplace obvious, for example /user/renfield/
* Download and install Mozilla Firefox: https://www.mozilla.org/en-US/firefox/
** Open Firefox -> Tools -> Web Developer -> WebIDE
** In the top right corner Select Runtime -> Install Simulator -> Firefox OS 2.0 Simulator (stable) -> Click INSTALL button
* Download and install node: https://nodejs.org/download/

You also need a terminal (the standard OSX app "Terminal" works, though iTerm is pretty great too: http://www.iterm2.com)

## Gnarly Yosemite Bug Warning
There a known and unpleasant bug in Yosemite around mNDS networks. Basically, it's broke.
mDNSResponder used to work, but it's been replaced by discoveryd, and it sometimes doesn't work.
The fix is...non-pleasant:
Type following command in the terminal to disable discoveryd:
sudo launchctl unload -w /System/Library/LaunchDaemons/com.apple.discoveryd.plist

Note that if you do the above, this runcible how-to will work, but your internet connection will likely be broken until you restore discoveryd with:
sudo launchctl load -w /System/Library/LaunchDaemons/com.apple.discoveryd.plist

Which then of course means this runcible how-to will break. Sigh, aren't OSX bugs awesome?