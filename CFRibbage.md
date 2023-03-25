## CFRibbage: A counterfactual regret minimization solver for cribbage, plus the tools to use it
[Cribbage](https://en.wikipedia.org/wiki/Cribbage) is a card game popular in some regions of the US. It's usually a two-player game, but it can be modified for more. It's a lot like [euchre](https://en.wikipedia.org/wiki/Euchre) in one important way: it involves meaningful strategy, but it's not very complicated --- so it's great for killing time or having a conversation while you play.

That *also* makes it the perfect size for a machine learning project on consumer hardware. This page documents such a project, including conception & design, the code, analysis of the results, and a frontend for querying and playing against the computed solution.

### The rules of cribbage
CFRibbage concerns the two-player game only. In two-player cribbage, one player is the **dealer** and the other is the **pone** (hat-tip to [Jawn via Metafilter](https://ask.metafilter.com/359767/what-to-call-the-player-who-isnt-the-dealer#5133796) for the latter terminology, which is short for 'opponent'). 

***The Deal***
Each player is dealt six cards, two of which must be discarded. The discarded cards go into the **crib**, which belongs to the dealer. 

> *Discard strategies are very different for the two players, because the dealer wants to maximize the points in the crib while the pone wants to minimize them.*

Once the players have discarded, the top card of the deck, called the **turn card**, is turned over. If it's a jack, the dealer gets two points --- "for his nibs" --- and then play begins.

> *If either player reaches 121 points at any time, the game ends immediately. This makes turn order very important.*


***The Play***
Starting with the pone, players take turns laying cards from their hand face-up in front of them. During the play, there is a **count** which updates based on the rank of each card played. An ace counts for one, twos through tens are worth their rank, and jacks, queens, and kings all increase the count by 10. A player may play any card in their hand, so long as the resulting count is no more than 31. They must play if they are able.


Fifteen is the magic number in cribbage, and if the count equals fifteen as the result of my play, I get two points. There are other ways to score during the play, too: 
 - playing the same card my opponent just played is a pair, and is worth 2 points --- unless it's the third in a row, in which case it's a pair royale, worth 6, or the fourth, when it's worth 12.
 - completing or adding to a run of cards, in any order, is worth the length of the resulting run.
 - getting the count to 31 exactly is worth 2 points, one for the 'go' (more on this in a second) and another for the feat.

> *Note that card suit is not important during the play.*

The last way to score during the play is to render your opponent unable to play, either because she is out of cards or because all of her cards would cause the count to go above 31. If a player cannot play, she calls 'go', and her opponent gets a point and may play again if they are able, scoring fifteens, pairs, and runs as normal. When neither player can play, but at least one of them has cards remaining, the count is set to zero. Any potential pairs or runs are considered null, and must begin again from scratch. The play is over when both players have played all their cards, the final player getting a point for the last 'go'.

***The Show***
If nobody has reached the goal score yet (usually 121 points), the game moves to the **show** phase. Players score their four card hands, *starting with the pone*.
>Points are scored immediately, so if the pone reaches 121 during the show, they win, even if the dealer would have reached a higher score after scoring their hand & crib.

Scoring is similar to the play, but with more ways to score. During the show, the turn card is considered a part of whichever hand is being scored, so scoring is always calculated on a five-card hand. 

Each distinct pair in a player's hand is worth two points (e.g. a three-of-a-kind contains three distinct pairs, for 6 total points). Each distinct combination of cards whose values sum to fifteen is worth two points. Each distinct run is worth its length in points. Flushes of either four or five cards are also possible during the show, and are worth the number of cards in the flush, with one important exception. The crib *does not* score a four card flush if the fourth card is the turn. The pone and the dealer's hand do, just not the crib.

Once the show is complete, the cards are collected and shuffled, and the players swap roles; a new hand is dealt.
