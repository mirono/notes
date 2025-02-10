https://www.quant-galore.com/p/relative-value-quant-betting-the?utm_source=substack&utm_campaign=post_embed&utm_medium=email

Sports betting
# Relative Value Quant Betting: The OddsJam Edition

### It's that time again — we're going back to Vegas.

Mar 17, 2024

Spring. A time of itchy eyes, warmer weather, and the return of chirpings at dawn. But above all, there’s something else starting soon — _glorious_, _magnificent_ **baseball**.

Here at The Quant’s Playbook, we **love** baseball season — not for noble reasons like childhood nostalgia, but rather because it gives us the chance to assert our quantitative dominance. With over 2,400 games per season, we are in trader’s paradise with tens of thousands of different opportunities to creatively squeeze out profits.

Baseball is a sport we happen to actually enjoy, so unlike with NBA or NFL, we put in some effort and really take this seriously.

To understand how we’ll need to approach things differently this season, let’s quickly recap where we left off on our last trip to Vegas.

Last season, we went through the wringer to build out a machine learning model that would aim to “beat” the sportsbook through _player prop bets_. Essentially, these are bets on what specific players would do in a given game — for instance, a bet for whether or not a given batter would hit a home run or not. The goal was to have our model accurately win a large number of bets and collect the profits — simple, honest clean work.

But there was a problem, check this out:

[

![](https://substackcdn.com/image/fetch/w_1456,c_limit,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2Fa3ce2c3b-e6f2-4424-ae74-2ef138a9be80_1074x839.png)



](https://substackcdn.com/image/fetch/f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2Fa3ce2c3b-e6f2-4424-ae74-2ef138a9be80_1074x839.png)

This sample strategy took 1 bet per day for a player to record a hit when the sportsbook indicated a ~70% chance (-233 odds) of it happening. **Based on the curve, what do you think the win rate of this strategy was?**

Believe it or not, the above image represents the PnL generated from a **74%** win rate.

Let’s dive deeper and look at some of the bets:

[

![](https://substackcdn.com/image/fetch/w_1456,c_limit,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F1b8a9398-a784-479e-933a-2c04126be9d8_649x631.png)



](https://substackcdn.com/image/fetch/f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F1b8a9398-a784-479e-933a-2c04126be9d8_649x631.png)

As demonstrated above, even though we knock out our bets at great win rates, 1 bad streak of losses is just barely offset by streaks of 7+. So, although you still made money for getting the bets right, you had to bet **a lot** just to scratch out a few hundred bucks for the season. This led us to the realization that **accurately betting on events is no thing, it’s all about the price you pay for the bet.**

While the above figure isn’t the exact strategy we used, it still captures the big picture idea of price being the problem, not accuracy.

Now, this left us with a few choices:

1. **Build a better model than the sportsbook:**
    
    1. For example, if our model deduces the true probability of an event to be 60%, but the sportsbook thinks it’s 50%, we have a 10% edge.
        
    2. _The problem:_ Our model uses the same public historical and weather data the sportsbook does, so the odds are often very similar with **occasional** large differences. See; [Sport Markets Aren't So Efficient After All...](https://quantgalore.substack.com/p/sport-markets-arent-so-efficient)
        
2. **Try to exploit weird strategies:**
    
    1. For example, buying cheap favorites for NBA moneyline bets has been very profitable in recent times (See; [Primitive Quant Sports Betting: NBA Edition](https://quantgalore.substack.com/p/primitive-quant-sports-betting-nba)):
        
        [
        
        ![](https://substackcdn.com/image/fetch/w_1456,c_limit,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F7507d6e9-74a4-4bea-a07c-c1d972a6b40b_3314x2630.png)
        
        
        
        ](https://substackcdn.com/image/fetch/f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F7507d6e9-74a4-4bea-a07c-c1d972a6b40b_3314x2630.png)
        
    2. The problem: There isn’t _**really**_ a strong intuition for why these approaches work and thus could very likely stop working at any given moment.
        
3. **Waiting for a sportsbook to **** up.**
    
    1. Sportsbooks are generally sophisticated and tightly-run operations; but sometimes, they aren’t.
        
        1. A sportsbook is a lot like a stock exchange; different exchanges have different quotes but all try to be around the same price. However, **sometimes, an exchange might have a price markedly different from the other exchanges — this creates opportunity.**
            

That may sound a bit opaque, so let’s dive in deeper.

#### Relative Value Betting (Positive +EV)

We’re a quant-finance letter first, so let’s conceptualize this with a finance example:

---

Assume AAPL’s stock is trading on both the New York Stock Exchange and the NASDAQ for a bid of **157.49** and an ask of **157.51 (**bid = the price investor A is willing to pay, ask = the price investor B is willing to sell for).

However, you notice that on the IEX exchange, the bid/ask for AAPL is **157.43**x**157.48**.

A savvy investor would take advantage of this situation in a few ways:

4. If they are an arbitrageur, they would buy AAPL on the IEX exchange at the ask of 157.48, then immediately sell the shares on the NYSE at the bid of 157.49 for a $0.01 profit per share.
    
5. If they wanted to establish a long position in the stock because their view is that AAPL will be higher in the future, they buy the shares on the IEX at an advantageous price relative to the best available price.
    

---

In traditional finance, there are armies of HFT funds who try to collect this exact arbitrage in milliseconds, making this opportunity very difficult to capture — _but what if we looked elsewhere?_

The sportsbook ecosystem is not totally dissimilar to the exchange ecosystem seen in finance. There are hundreds, if not thousands, of sportsbooks each with different prices on the same event — different prices on the same stock. Now, in practice, most books set their prices to be similar to the price listed by a “sharp book”, most often [Pinnacle](https://www.pinnacle.com/en/). The theory is that the sharp book’s prices are the “correct” ones and your listed odds shouldn’t be too far off from that.

While this is often the case (i.e. , most books have similar odds), there are a few factors that might lead to one sportsbook having a markedly different price than another on the same event:

6. **Betting Imbalances**
    
    1. If off-shore sportsbook Z has just taken a one-sided bet from a whale, it desperately needs to hedge this exposure — if the whale is right, the book would be on the hook for a massive payout. To hedge this risk and entice other bettors, the book will offer a better price on the opposite side. So, if the average price across books for the opposite bet is for 2x your money, the book may offer 3x until their risk is hedged.
        
7. **Technological Inefficiency**
    
    2. Sportsbooks like DraftKings and FanDuel have the funding, technology, and bodies to keep up with minute-to-minute price changes. However, they are not the norm — because data for sportsbooks is still a fragmented and decentralized market, books with smaller headcounts are generally the slowest to the table.
        
        1. For instance, assume your book just copies the lines from DraftKings. There is no official data source, so you’re relying on your custom scraper or that of a third-party. If your data slips up or even lags just a bit, you’ll be a target.
            

So, if we can be able to quickly spot where one book has deviated from the norm —because of lag, hedging, whatever — we can get the prices necessary to make **way** more money in the long-haul.

#### [The OddsJam Angle](https://oddsjam.com/?ref=m2mzmjj)

Historically, attacking this problem was a **serious** technical challenge:

[

![](https://substackcdn.com/image/fetch/w_1456,c_limit,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2Ffb13488b-74ac-43e1-ba16-536a23374349_1920x1080.png)



](https://substackcdn.com/image/fetch/f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2Ffb13488b-74ac-43e1-ba16-536a23374349_1920x1080.png)

Simple steps, but you would spend a few days/weeks getting the infrastructure ready in the red-zone, just to change it up if the book changed their data schema or web layout.

But now, there’s a simpler way.

We can use [OddsJam’s Positive EV finder](https://oddsjam.com/?ref=m2mzmjj) to conveniently pull up real-time trade opportunities where there’s a book whose pricing is out of line:

[

![](https://substackcdn.com/image/fetch/w_2400,c_limit,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F89c3e235-903c-48c9-85ff-0824313bcab5_1555x810.png)



](https://substackcdn.com/image/fetch/f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F89c3e235-903c-48c9-85ff-0824313bcab5_1555x810.png)

In the above image, we are shown a bet with an edge of ~9%. The bet is an NBA player prop bet for Isaiah Stewart to record 0 blocks for the game in question. Looking at the rightmost arrow, we see that the Bet365 sportsbook offers this bet for +200, but looking at the bottommost arrow, we see that the average price offered across books is +163 (e.g., the “true” price).

So, essentially, most exchanges are offering about 1.63x our money for Mr. Stewart to not make any blocks, but **we can go to a different exchange and get 2x our money for the same bet.**

Further, we can also get the optimal bet size based on the Kelly criterion:

[

![](https://substackcdn.com/image/fetch/w_2400,c_limit,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2Fe6d2b15d-14f5-410c-9af4-af7038472c42_1173x412.png)



](https://substackcdn.com/image/fetch/f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2Fe6d2b15d-14f5-410c-9af4-af7038472c42_1173x412.png)

We can also go down the strip to see what other opportunities are available:

[

![](https://substackcdn.com/image/fetch/w_2400,c_limit,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F8a4ee57f-5a11-40af-952a-84d0e7b4c447_1760x498.png)



](https://substackcdn.com/image/fetch/f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F8a4ee57f-5a11-40af-952a-84d0e7b4c447_1760x498.png)

But before we continue any further, **is any of this even worth it?**

To figure this out, let’s go back to our sample strategy from earlier. We would use the EV tool to get improvements in odds, so **if we got, say, 5% better odds on our bets, we would expect to make significantly more in the long-haul.**

Let’s see if the data confirms that:

[

![](https://substackcdn.com/image/fetch/w_1456,c_limit,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2Fea66e354-9ace-4ffb-a6e6-8ae5055636c9_1074x839.png)



](https://substackcdn.com/image/fetch/f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2Fea66e354-9ace-4ffb-a6e6-8ae5055636c9_1074x839.png)

Without changing our betting at all, when we improved our price probabilities by just 5%, the strategy went from generating a measly ~30% to more than **doubling** our initial bankroll.

Now, this sample represents only ~86 bets from April to July 2023, but the power of positive EV betting comes from large sample sizes. Remember, the name positive EV comes from the positive expected value generated from a given sports bet:

_Expected Value = (Prob of winning * Amount won) - (Prob of losing * Amount lost)_

_**Expected Value** = (70% * $50) - (30% * $100) = **$5 → 5%**_

In the above example, if we are offered odds of -200 (66% prob) for a bet with a probability of 70% (-233), we would have a positive expected value of 5%. This means that if we were to make this bet hundreds if not thousands of times, we would expect to have an average profit of 5% in each instance. At 86 bets that results in a profit you can’t sneeze at, but at 1000 — well, you can probably see it yourself.

This approach seems like the answer to our biggest problem, so let’s map out exactly how we’ll go about trading this:

8. First, we need to make an account at as many sportsbooks as we can. It’s unlikely that the standard DraftKings and FanDuel will be where the wonky lines show up, so we’ll need to be able to execute on the smaller exchanges whenever the opportunity arises. Based on what generally appears on OddsJam, this list is mainly:
    
    1. _**BetMGM, Bet365, Bovada, Borgata, Bodog, BetOnline, ESPNBet, HardRock Sportsbook, MyBookie, XBet, Unibet, Caesars, DraftKings, Fanatics, FanDuel, BetRivers**_
        
    2. Once the accounts are opened, we just park some _walk-around money_ at each venue while we wait for the opportunities to come up.
        
9. On a day that we want to make bets, we pull up the +EV finder and try grabbing as many of the most profitable opportunities we can (>5%+). Most bets will have 1-click betting enabled (i.e., a site redirect), so it’s just a matter of keeping the tab open. We can also see columns like time last updated and the optimal bet size based on Kelly, so we don’t need to do much active calculation.
    
10. On any given day, we can expect to place at least 25-50 uncorrelated bets, each with a theoretical edge; so, we can reasonably expect to profit with each day we run this.
    
11. Repeat and test the limitations.
    

So, it looks like we’ve got a plan.

#### Final Thoughts

Before we dive in heads first, there are a few important considerations:

12. Positive EV makes a profit over a large number of bets, but it is susceptible to short-term variance. In our first example, the average probability of Isaiah Stewart not making a block was ~38% (+163). Even though we are getting a greatly advantageous price at 33% (+200), the probability of a payoff is still pretty low. However, if we made 100 bets like this, where the probability was 38% but we pay a price implying 33%, then we are almost guaranteed an abnormal profit — but **only** after a sufficient amount of bets.
    
      
    However, this can be resolved through applying filters. For example, if we want an edge on bets that will payout 45-50% of the time, we can set a maximum price of +150 so that we can greatly boost how much we can make in the short-term:
    
    [
    
    ![](https://substackcdn.com/image/fetch/w_1456,c_limit,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F3868f6f2-ce79-4ac7-a72e-4b189a0aa216_620x522.png)
    
    
    
    ](https://substackcdn.com/image/fetch/f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F3868f6f2-ce79-4ac7-a72e-4b189a0aa216_620x522.png)
    
13. Successfully running this is a part-time job. While you can make pretty decent profits from pre-game +EV betting, take a look at the kind of opportunities offered for in-game bets:
    
    [
    
    ![](https://substackcdn.com/image/fetch/w_2400,c_limit,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F9ca2d470-ea43-4297-bfc6-1239fcfef8fc_1591x827.png)
    
    
    
    ](https://substackcdn.com/image/fetch/f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F9ca2d470-ea43-4297-bfc6-1239fcfef8fc_1591x827.png)
    
    Each day there are hundreds of these bets across the major sports but the caveat is that these opportunities are captured relatively quickly. Not so quick that HFT programs are swooping up the offers, but quick enough that a trade you see at dawn might not be there by the noon. So, if you’re going to try this — you need to keep your finger on the pulse.
    
14. Depending on how much you wager each time, it is highly likely that some books will restrict your betting. It is highly rumored that ESPNBet is quick to limit wagers to no more than $10, so it is important to have as many sportsbook accounts as you can. Although this is one of those good problems (you only get limited after making too much money), it adds a challenge to scaling above side-hustle-like profits.
    

While this demonstrates extremely high potential, it can be turbo-charged if used in conjunction with **arbitrage betting:**

[

![](https://substackcdn.com/image/fetch/w_2400,c_limit,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F01ce6293-1806-4d8a-8c13-e922c1788d46_1796x558.png)



](https://substackcdn.com/image/fetch/f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F01ce6293-1806-4d8a-8c13-e922c1788d46_1796x558.png)

[

![](https://substackcdn.com/image/fetch/w_2400,c_limit,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F6727391d-613a-46b9-a918-dbb3c26ca6d9_1783x567.png)



](https://substackcdn.com/image/fetch/f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F6727391d-613a-46b9-a918-dbb3c26ca6d9_1783x567.png)

I want to personally try executing these before we dive into it as a strategy, but essentially, arbitrage betting is placing opposing bets on the same outcome at different books to guarantee a profit. This is right up our alley as traders, but diving into that area will need to be a post of its own.

So, all-in-all; we needed last season’s “Ls” to point us in the right direction for this season. As of writing, there are still a few weeks until opening day, so we’ll be setting up our infrastructure in the meantime to get ready to put this into action.

I’ve personally already started testing this +EV approach to build up a sample size and have already generated a profit:

[

![](https://substackcdn.com/image/fetch/w_1456,c_limit,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F5eb66f81-f626-4c43-82cb-c3ee004e88aa_1088x746.png)



](https://substackcdn.com/image/fetch/f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F5eb66f81-f626-4c43-82cb-c3ee004e88aa_1088x746.png)

[

![](https://substackcdn.com/image/fetch/w_1456,c_limit,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F48948cc6-8eb6-4b24-91c2-a417f8b1d0e6_1470x811.png)



](https://substackcdn.com/image/fetch/f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F48948cc6-8eb6-4b24-91c2-a417f8b1d0e6_1470x811.png)

We don’t like to get too ahead of ourselves, but this is going to be good.

---

Happy trading! 😄

---

_Disclaimer: This post includes an affiliate link for OddsJam. No further compensation is provided for this post nor for our use of the OddsJam suite. We strongly believe and practice **never** advocating for services or tools that we don’t personally use and trust._

#algo #sport #quantgalore 