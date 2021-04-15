---
author: robert
comments: true
date: 2009-08-15 04:05:30+00:00
layout: post
link: https://roberthorvick.com/2009/08/15/snap-crackle-pop-clicks-and-pops-in-cakewalk-sonar-8/
slug: snap-crackle-pop-clicks-and-pops-in-cakewalk-sonar-8
title: Snap! Crackle! Pop!  - Clicks and Pops in Cakewalk Sonar 8
wordpress_id: 98
categories:
- Home Studio
tags:
- alesis
- homestudio
- music
- photon
- sonar
---

I’m may not be the brightest bulb in the pack but I do know a couple-a-things about a buncha-stuff.  One of those things is that when I plug a keyboard into my computer and open Cakewalk Sonar (8 Producer Trial) I expect to hear sounds.  The other is that when I finally hear those sounds they shouldn’t be punctuated with a loud (louder than the audio being played) click ever half second or so.

 

Not even exaggerating – it was like a slightly off-beat metronome was overpowering what I was playing.

 

**Getting the Sound…**

 

I plugged my Alesis Photon x25 into my Windows 7 laptop and then started Sonar 8.  I’m immediately greeted with the message that either the Photon is not supported or in use.  I’m going to assume it’s in use by Windows and just move on … click “Use Anyway”

 

Create a new project … delete all the audio tracks … add a MIDI track … hit a key.  Whoa!  Hmmm … no sound.  Oh!  Forgot to add a synth (I came across this when doing “Tutorial 1” in the Sonar help file … yeah … I read it.  I read the Readme too).  So I add a synth.  Now I hit a key and … still nothing.  The signal indicator is peaking so I know I’m getting the input from the keyboard to the DAW.  Time to try some random things and then hit the tubes looking for an answer.

 

Let me save you the trouble:

 

[http://www.cakewalk.com/Support/FAQ/MIDI2Audio.aspx](http://www.cakewalk.com/Support/FAQ/MIDI2Audio.aspx)

 

Read the whole thing.  It was section three I cared about.  Specifically to check “All Synth Audio Outputs: Stereo” and to map the outputs to the synth.

 

But still no sound – oh crap – it’s playing through the Photon and not my sound card!  Awesome!  I plugged in a set of head phones and I’m off.  This is great.

 

**But it sounds horrible …**

 

clicks and pops like mad.

 

Again … let me save you the trouble:

 

[http://www.cakewalk.com/support/FAQ/SR_FAQ.aspx#9](http://www.cakewalk.com/support/FAQ/SR_FAQ.aspx#9)

 

Adjusting my buffer to “Safe” made it better so I knew I was on the right track – but changing my driver from WDM to MME was the key (ASIO didn’t work – no sound … I’ll look into that some other time).  Once I was switch to MME I was able to drop my latency back to 10ms.  At 10ms I get an occasional pop.  At 30ms almost none.  At 90 – I’ve yet to hear any.  Beauty!

 

Now to find some free VST synths because the ones in the Trial are … just horrible.  It’s like they are punishing you for using the trial.  Give me a drum kit or piano please!  Some congas and a shaker … anything.  Jeez.

 

(they are probably there and I just don’t know how to use them yet … but the forums I read all said things like “the trial has crap sounds … get free VSTs”).
