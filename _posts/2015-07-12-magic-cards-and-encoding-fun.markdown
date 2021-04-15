---
author: admin
comments: true
date: 2015-07-12 15:27:41+00:00
layout: post
link: https://roberthorvick.com/2015/07/12/magic-cards-and-encoding-fun/
slug: magic-cards-and-encoding-fun
title: Magic Cards and Encoding Fun
wordpress_id: 469
categories:
- Programming
tags:
- encoding
---

When I was in 8th grade we had a school talent show – it wasn’t everyone-up-on-stage, but rather classrooms dedicated to certain talents.  Music.  Karate.  Acting.  Magic.

I was in the magic room.

I wasn’t really into magic.  I didn’t own any tricks or really know how to do anything except hide a hanky under a fake thumb.  I was there because Andy asked me to help him with a trick.  Like any good magician, Andy needed an assistant.  Like any good assistant, I wasn’t just there to look pretty.

I don’t recall the exact trick – but I think it went something like this:

A 3x3 grid was drawn on a black board.  Andy would leave the room, the audience would pick one of the squares in the grid – telling me which one.  He would then return and correctly guess which square they had picked on the first attempt.  There was probably more to it – but that was the gist of it.

That leads me to today when a friend posed a question to me.

_There are two magicians on stage – one of them is holding a normal deck of 52 cards.  The second magician is unable to see or hear what is occurring.  The first magician, the one holding the cards, brings an audience member up and has them pick 5 cards at random.  The first magician then brings up another audience member who removes one of the five cards.  This is the “magic card”.  The second magician now returns and is handed the four cards that were pulled from the deck.  Looking only at these four cards, the second magician would then be able to tell the audience what the magic card is._

The instructions were _“Take 30 minutes to think about it.  No cheating.”_

So – it’s an encoding problem.

I started by thinking about the known variables:

52 cards, 4 known.  1 Magic.  That’s 48 unknown – so I’ll need 6 bits.  I don’t think I’ll be able to encode 6 bits of information in 4 cards so I tried to decompose it a bit.  Its 4 suits and 13 values (Ace to King).  2 bits for the suit and 4 for the number.  Hmmm … still 6.

What do we really know?  

We can’t rely on the four cards being anything in particular.  Ignore suits and values – what is left?

Ordering.

Let’s think about those four cards not as suits and values, but as being in their relative order.  There is a card with the least relative value – let’s call that 1.  And the highest relative value- 4.

There are 24 permutations of “1234”

Now we’re getting somewhere.

So let’s define some values:

`
Order - Card
1234 - Ace
1243 - 2
1324 - 3
1342 - 4
1423 - 5
1432 - 6
2134 - 7
2143 - 8
2314 - 9
2341 - 10
2413 - Jack
2431 - Queen
3124 - King
`

Now we’ve encoded the value.  But what about the suit?  We don’t have enough permutations left to add in suit.

So what else do we know?

Well – continuing down the path of ordering – we know not only the cards relative order, but we know the specific order they are handed to the second magician.  They are in a pile.  Notice that every card in the first position is succeeded by a higher value card.  We know this because the “4” value is never the first card.

So – let’s say if the deck is handed in ascending order that means a black suit, and in descending order it means a red suit.

Now we’re really close.

But I need on more bit to define whether the red card is a heart or diamond or the black card is a club or spade.

So I thought about Andy and his trick.

When Andy came back into the room I would go to the front of the room and introduce The Amazing Andy – pointing at a placard or something – it was viewed as neutral to the trick but it’s where the magic happened.  Where I pointed told Andy what the answer was.  Pointing at one location meant one grid area, pointing somewhere else meant another.

To the audience I was just goofing around introducing Andy, but by the time Andy got to the board he already knew the answer.  I had encoded it in my gesture.

Maybe that can be my last bit.

If the first magician hands the deck length-wise, the bit is 0, if width-wise, the bit is 1.  Or maybe if they use their left hand it is 0 and right hand is 1.  Heck, you could go further.  Between the two of them they have four hands.  We could encode four values in LL, LR, RL, RR pairs.  The point is that every interaction is an opportunity to encode a little bit of data.

So in the end my solution was: relative card order defines the card value (Ace to King), deck ordering (ascending or descending) combined with hand-off position define the suit.

It’s probably not optimal, but given the amount of time allowed to think about it I think it defines a reasonable solution that might even be worthy of The Amazing Andy.
