https://www.quant-galore.com/p/insights-from-a-small-scale-quantitative?utm_source=post-email-title&publication_id=1073868&post_id=151718386&utm_campaign=email-post-title&isFreemail=true&r=1rq2d0&triedRedirect=true&utm_medium=email

https://github.com/quantgalore/at-the-money/blob/main/atm-hedging-spx.py

# Insights from a Small-Scale Quantitative Trading Operation

### Also, odds optimization, pseudo-arbitrage, and more synthetic data usage.
Nov 24, 2024

You may have noticed that it’s been quite awhile since our last post. While we truly regret to keep you waiting, our absence was not without merit.

You see, there’s been some trading — well, kind of a lot of it:

Naturally, from just being out in the field trying things, we learned **a lot.**

So, today, we’ll be doing a deep dive into what we discovered, some new insights, and a few new strategies that we’re getting ready to launch.

#### **It’s All About The Price, Baby**

[

![](https://substackcdn.com/image/fetch/w_1456,c_limit,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F84c8ab51-fab6-41d9-bd53-5c9324a44a72_888x499.png)



](https://substackcdn.com/image/fetch/f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F84c8ab51-fab6-41d9-bd53-5c9324a44a72_888x499.png)

Although we’re quantitative traders, we briefly lived a past life where we tried to apply the same concepts to sports betting.

To quicky recap, we settled on the conclusion that in sports betting, it’s all about the price. You see, making a net profit from sports betting isn’t about having a good model or understanding the given sport, it’s simply about paying a price that is cheaper than the “fair” price.

Professional bettors essentially just take the fair price from a “sharp” sportsbook like Pinnacle, then search for sportsbooks that provide a deviation from that line. When there’s a sportsbook that’s too far off, you bet big — do that 100 times a day and you’re “guaranteed” a profit.

We expand more on this, along with the results of our own experiment in:

[

![](https://substackcdn.com/image/fetch/w_56,c_limit,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2Fa86cfca0-0c4c-4800-a60d-fea150a8712a_500x500.png)The Quant's Playbook

Relative Value Quant Betting: The OddsJam Edition

Spring. A time of itchy eyes, warmer weather, and the return of chirpings at dawn. But above all, there’s something else starting soon — glorious, magnificent baseball…

Read more

a year ago · 6 likes · 8 comments · Quant Galore

](https://www.quant-galore.com/p/relative-value-quant-betting-the?utm_source=substack&utm_campaign=post_embed&utm_medium=web)

So, if we can say it all comes down to being a shark — hammering prices when they’re less than fair, can’t that same logic be applied to markets?

To test this idea, we first need a way to convert market prices into implied probabilities. One way of doing this is to use the prices on credit spreads, as explained by the following graphic:

[

![](https://substackcdn.com/image/fetch/w_2400,c_limit,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F31353910-b497-4230-bd3e-a798500c227b_1046x469.png)



](https://substackcdn.com/image/fetch/f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F31353910-b497-4230-bd3e-a798500c227b_1046x469.png)

Using the options chain featured above, we can say that at timestamp t, the market implied that there was a 70% probability that the index would be above 5880 at contract expiration — so, 70% represents the “square price” or the fair odds that we should look to improve upon.

Naturally, in similar nature to sports betting odds, this probability will generally end up being accurate. _However_, also like in sports betting, if odds do anything — **they change.**

Sometimes, the price of a sports bet at a given sportsbook will change based on things like dollar imbalances (e.g., too much or not enough $ on team A), stale updates, or just general variance. The same applies to markets, where as prices change, so do the odds of a given strike landing out-of-the-money.

To see that in action, take a look at what happened just minutes after the above screenshot was taken:

[

![](https://substackcdn.com/image/fetch/w_2400,c_limit,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2Fbcd5228b-92bb-42d2-b05e-d6cf2168b90c_1028x523.png)



](https://substackcdn.com/image/fetch/f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2Fbcd5228b-92bb-42d2-b05e-d6cf2168b90c_1028x523.png)

The index rallied upwards by ~30 points, so the same bet that was trading at odds of 70% (1.50 credit) was now trading at odds of **84%** (0.80 credit).

So, if we assume that the odds at time t-1, 70%, were the fair odds, our view is that this current price is “rich”/“wrong”.

In +EV betting theory, this would represent a theoretical 14% edge (implied_odds - market_odds). If the odds at 70% are true and we took this bet 1000 times (buying the spread @ 0.80 until it gets back to 1.50), we would _theoretically_ be guaranteed a profit.

Now, of course, knowing which price is the fair one is tricky — just a few minutes after the above screenshot, the odds changed again to 88%.

One simple way is to create a model that just uses the price at time t, say, 9:35 EST, as the true one. Remember, a model is just that — a way of modeling a theory —sometimes it’ll be wrong, but on average it should generally perform okay.

If put into a strategy, we would take all option prices at 9:35 that day and treat it as gospel. So, if at 9:35 the market implied there was an 80% chance for SPX to close above 6000, but a few hours later that probability changed to 25%, we would take that bet.

To quickly test that idea, we can use a modified version of our core SPX strategy:

- _**Core SPX Strategy:**_ We use the value of the VIX1D index at 9:35 EST, paired with the present market regime to determine the optimal SPX spread to sell that day.
    

The historical win rate of the strategy is ~80%, so every price at better odds than that (spread price >= 1.05) offers us a theoretical edge. Let’s take a quick look at how the performance differed if instead of trading at the first available price, we only traded with implied odds of 70% (spread price = 1.5).

First, the baseline performance (trading at the first available price after the signal is generated):

[

![](https://substackcdn.com/image/fetch/w_1456,c_limit,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F71c0d71b-bd4b-46c6-b740-cc3f03e52a4f_1097x845.png)



](https://substackcdn.com/image/fetch/f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F71c0d71b-bd4b-46c6-b740-cc3f03e52a4f_1097x845.png)

Now, with optimized odds:

[

![](https://substackcdn.com/image/fetch/w_1456,c_limit,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2Fe5fee22e-6fa9-4f46-a8a5-3606640e83c8_1115x845.png)



](https://substackcdn.com/image/fetch/f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2Fe5fee22e-6fa9-4f46-a8a5-3606640e83c8_1115x845.png)

As you may have noticed, our base strategy experienced a bit of difficulty recently as the market flattened out and got more volatile (we’ll get to that in a sec), but nevertheless, the version with a 10% odds improvement absolutely **dogs** the un-optimized version.

Now, of course, there’s no free lunches and getting this optimized price can be a bit tricky.

In a study of a smaller time series, the price of this spread reached $1.5 only ~65% of the time and of that 65%, it ended up actually paying off ~73% of the time. So, when the market implied probability changes, it does end up actually decreasing the probability of the event realizing, but not in a drastic enough way to make the optimization unprofitable.

Nevertheless, if these bets at advantageous prices are peppered into our trades, we can systematically increase and ensure our edge.

Making sure that we actually get filled on those prices though is a fun exercise in algorithmic order execution:

---

_**Brain Teaser: An Order Execution Puzzle**_

_Scenario:_ Everyday, the market will offer you $1.00 for something that has an 80% chance of paying off. If it’s wrong, you’ll lose $4.00. You’re smart, so you know that you need a price of at least $1.05 to come out ahead in the long-run. You also know that sometimes, the price gets as high as 1.50, which would cement your edge even further.

_Problem:_ You don’t know what the best price of the day will be, sometimes it’ll be exactly $1.05, sometimes it might even be $4. **How do you balance getting filled daily with making sure you’re also exposed to the best prices if/when they arise?**

A sample solution is as follows:

1. Your fair probability is 80%, so you start off with a base limit order of $1.05 or the mid price if it’s higher:
    
    1. np.maximum(1.05, mkt_price)
        
2. You monitor prices within the first 2 hours and wait for the price of the spread to get to 1.50 (70% prob).
    
    2. If it doesn’t happen, you collect the full base credit anyway and move onto the next trading day.
        
    3. If it does, you sell more contracts up to the desired risk limit of the day.
        
    4. You only wait for 2 hours, since if the prob drastically changes in say, the last 10 minutes of trading, then it’s likely a legitimate bad bet at that point.
        

As the sample size grows, this process will increase the average trade price and prove to be more efficient than just taking the first available price.

---

Now, while getting an improvement on price is the most surefire way to profits, it won’t _totally_ save a bad strategy.

To see this, let’s take another sample strategy: selling 1-DTE iron condors using the VIX1D value near close.

For this strategy, if we wait until say, 5 minutes before close, that gives us very little room for price improvement. So, rather, we can take the VIX1D value at 2:00 (2 hours before close) and use that as the baseline “true” value. That gives us 2 hours to let prices fluctuate, giving us the best chance to get a fill at an advantageous price.

So, here’s our sample strategy:

1. At 2:00 PM EST, we use the VIX1D to get the implied move for the next day.
    
2. Based on the implied move, we sell an iron condor directly outside of that range.
    
    1. We make a profit if the next day’s realized move is less than what the market implied (e.g., market implied prices to move 0.9%, but it only ended up moving 0.5%)
        
3. Since the prices at 2:00 are our “fair” odds, we use that as the baseline for improvement. So, for the remaining 2 hours we wait for a 10% improvement in price (e.g., “fair” price at 2 = 1.00 | 10% improved price = 1.10).
    
    1. If the price doesn’t ever reach that amount, the order doesn’t get executed and we don’t take a trade.
        
        1. We do a 10% improvement in spread _price_ since a 10% difference in implied probability would be too infrequent at strikes this far OTM.
            

Let’s see how that compared to if we just traded outright every time:

[

![](https://substackcdn.com/image/fetch/w_1456,c_limit,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F96c92cac-76e1-41c6-addd-5b08b28c91ce_1060x845.png)



](https://substackcdn.com/image/fetch/f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F96c92cac-76e1-41c6-addd-5b08b28c91ce_1060x845.png)

When starting from 2 PM, the price ended up improving by 10% about **90%** of the time. However, because pure short vol strategies like this tend to lose in clusters, even getting a 10% improvement doesn’t save the strategy.

So, if you have a strategy that makes money, figuring out the odds (e.g., historical win rate) and delaying execution for price optimization can make you a whole lot more, but the prerequisite is that the strategy is viable in the first place.

Now, with that covered, let’s move onto another matter of business.

#### Current + New Strategy Updates

At The Quant’s Playbook, we’ve been running 2 quantitative strategies in production.

To quickly recap, we have a strategy that uses a short-term prediction model to predict the next day direction of TSLA’s stock. Based on that prediction, we buy a call or put at near market close and sell it at market open the next day.

Because TSLA routinely experiences volatile moves that outpace implied volatility, its options provide greatly asymmetric returns which allow us to have a positive expected value even if we just go 50/50.

Put simply, this strategy, has been making us rich — month after month:

[

![](https://substackcdn.com/image/fetch/w_1456,c_limit,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F467fd678-c2f1-4ce4-b0b5-1b0dd7b7c6cc_4598x3378.png)



](https://substackcdn.com/image/fetch/f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F467fd678-c2f1-4ce4-b0b5-1b0dd7b7c6cc_4598x3378.png)

Despite the model only having a 53% accuracy rate for the year, the incredibly asymmetric nature of the option payoffs allowed us to have an expected value of **13%** per trade.

We’ve been grateful to see that the realized volatility of the stock has continued to increase on election-related fundamentals. Since we’re taking straight-up directional bets, we’ve been extra fortunate to be on the right side of the trade when the stock had exceptionally high moves.

While this strategy is more of a purely quantitative play, it should continue do well if volatility stays high.

For more information on replicating this strategy, see:

- [Papa's Got A Brand New Options System](https://www.quant-galore.com/p/papas-got-a-brand-new-options-system)
    
- [The Volatility Trading Bible](https://www.quant-galore.com/p/the-volatility-trading-bible-but)
    

Moving on — our main SPX strategy has gotten a bit more complex:

[

![](https://substackcdn.com/image/fetch/w_1456,c_limit,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F7806f7e6-e564-4e1a-8109-e3eb100ce397_1097x845.png)



](https://substackcdn.com/image/fetch/f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F7806f7e6-e564-4e1a-8109-e3eb100ce397_1097x845.png)

To quickly recap, this strategy takes the value of the VIX1D index at 9:35 (5 minutes after open) to get the implied daily move. Next, an adjustment to that value is applied, since using the full 1-day implied move would overshoot the intraday move by way too much. Once we have our estimated move for the day, we pull the current market regime and sell a credit spread outside of that range in the direction of the regime (e.g., selling puts in bull regimes, calls in bear).

The strength/purpose of the strategy is that it turns the performance of the S&P 500 into real-world cash flow. About 80% of the time, we make ~$105, 20% of the time we lose ~$395.

However, a few weaknesses have come to light:

4. A feature, not a bug — but nevertheless, the strategy is _extremely_ correlated to the S&P 500.
    
    1. This makes sense since we sell the present market regime, so when markets go straight up, we’re selling puts — when they’re going straight down, we’re selling calls.
        
        1. Because we use a lagging regime feature, we are often a bit slow to the latest regime changes, resulting in losses as the system takes time to switch regimes.
            
5. The skew is too negative.
    
    2. Skew refers to the “extremeness” of our expected PnL. So, positive skew strategies (like with TSLA) tend to have right-tailed, positive extreme moves (i.e., making a lot every once in a while). With selling options, extreme left-tailed (negative) losses are more common (i.e., _losing_ a lot every once in a while).
        
    3. Naturally, we expect a negative skew especially since we’re selling options the market already prices-in as being likely to expire OTM. However, the 1:4 ratio of this strategy means that for 1 loss, you need to win 4 times in a row just to get back to profit/even.
        
        2. This is fine when you consistently win in periods where the market goes in one direction for a long time (like in the first half of 2024), but when things get choppy, you can actually spend months just being at about even.
            

In the long run, the performance curve of the strategy should generally resemble the S&P and end up okay, but the short-term opportunity cost is real.

We started selling 0-DTEs in the first place for the potential to systematically pull out thousands per day, but if we’re taking a loss, we should have the reasonable expectation of making back that loss in a few days, not in a few weeks/months.

It took **a lot** of work, but we just might have something that should make us whole.

#### **No Risk, No Reward**

Naturally, as we pursue greater returns, we **must** accept greater risk. So, instead of starting with the safest strikes to sell, we should focus on the **riskiest** strikes to sell.

These are the _at/in_ the money strikes — your 50/50s, the 48/52s.

With these strikes, if we took the same $1,000 of risk per day, we stand to either lose it or >= double our money. When we take a loss, there’s a good chance we’ll get back to even as soon as **tomorrow.**

Now, these returns are more attractive for a reason — conventional wisdom states that these coin-flips are impossible to successfully predict.

Of course, life wouldn’t be very fun if we didn’t at least try, so, we’ve got a few ideas:

#### Proposed Strategy I: The Pseudo-Arbitrage

The inspiration for this also came from a prior experiment in the land of sports betting:

[

![](https://substackcdn.com/image/fetch/w_56,c_limit,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2Fa86cfca0-0c4c-4800-a60d-fea150a8712a_500x500.png)The Quant's Playbook

Kick-Off! Quasi-Arbitraging NFL Markets [Code Included]

In the last major sport season (MLB), we built machine learning models to help us predict game outcomes (e.g., team A will defeat team B), as well as the outcomes for player proposition bets (player X will record a valid hit in this game…

Read more

a year ago · 10 likes · 5 comments · Quant Galore

](https://www.quant-galore.com/p/kick-off-quasi-arbitraging-nfl-markets?utm_source=substack&utm_campaign=post_embed&utm_medium=web)

To quickly summarize the core idea:

- Imagine a bet for Team A vs Team B.
    
    - The market will give you 3x your money if Team A wins the game.
        
    - There is another derivative bet that will give you 3x your money if Team B wins by 10+ points.
        
        - You know that, on average, this historical matchup ends in a difference of at least 20 points, so you take the bet.
            
    - If Team A wins, your $100 bet makes $300 in profits, while your Team B bet loses $100, for a net profit of +$200
        
    - If Team B wins by the margin, you lose your Team A bet, but you make +$300 on that bet for a net +$200.
        

Now, there’s a slim probability that that Team B will win by only say, 3 points, in which case you would lose on both of your bets. You estimate the probability of this to be extremely low however, so you make the bet.

It’s _kind of_ like an arbitrage — you make money on any team winning, but like merger arbitrage, there’s a slight chance that the low probability event ends up happening and you end up taking a large loss — so, a _**pseudo-arbitrage**_**.**

We can setup a similar risk profile trade with ATM SPX options:

6. Say we start off the trading session with an at-the-money put spread.
    
    1. The starting probability will be ~48-53% (spread credit of 2.6-2.35)
        
7. As trading continues, we watch how that probability changes:
    
    2. If the probability continues to increase, we do nothing as expiration approaches.
        
    3. If the probability of the spread expiring OTM drops to say, 25%, we sell the **opposite** direction at the same short strike in a quantity large enough to cover our max loss on the original spread.
        

That might be a bit confusing, so let’s walkthrough an example:

The price of SPX opens at 6000 so we sell the -6000/+5995 put spread for a premium of 2.5 (odds of 50%). At 12:00 PM, the index dropped to 5895 and now the spread is worth 3.75 (odds of winning are now 25% → (5-3.75) / 5).

Our short put strike is 6000, so if the index closes below that, which is now 75% likely to happen, we’ll lose money. So, we need to take a bet that makes money if the index **does** close below our short strike.

To do that, we sell a call spread at the -6000/+6005 strikes.

If the index stays below 6000, we’ll lose on the original put spread, but it’ll be offset by the credit generated by the call spread.

The prices offered will vary, but if the call spread at that time is worth $1.00, we would sell 3 of those to cover our max loss on the put spread ($-2.5).

If things stay the way they are, we’ll lose $250 on the put spread but make $300 on the call spread for a gross profit of $50.

This situation has 3 outcomes:

8. Best case: The starting put spread rides off into the sunset and we collect the full premium at expiration without needing to hedge.
    
9. Base case: We lock-in a small profit / no loss when we hedge the spread.
    
10. Worst case: The market reverses **again** and now the original spread is profitable, but the hedging spread is at risk.
    
    1. In the above example, in order to get that $300 in credit from the call spread, we had to take $1,200 of risk ($4 max loss x 3).
        
    2. At expiration, we’d make $250 on the original spread, but lose $-1,200 on the hedge, for a gross $-950.
        
11. Semi-worst case:
    
    1. If the market reverses and sends the spread into a loss at near market close, the opposite spread may be trading for something like 0.10 which would force us to risk tens of thousands in order to make enough to cover the max loss, so in that case we would be better off just taking the original max loss.
        

Of course, in the worst-case scenario, we can just sell more of the original spread to make up for the loss on the hedging spread, but that potentially starts an _**infinite hedging loop**_:

If the market reverses _again_ after we hedge_,_ then we’ll have to sell more of the original spread to make up for the new losses the hedge, but if the market reverses _**again**_ after that_**,**_ we’ll have to sell **even more** of the hedge spread to make up for the new losses on the original spread — this can go on-and-on until we run out of money to hedge again.

The probability of that happening in 1 trading day is virtually 0, but over a large enough sample, it’ll happen at least once — and it’ll potentially be game over. So, we’ll stick to just hedging once — for now.

The curve of this risk profile should consistently go upwards to the right since the base case is a small profit, but it should take a massive pooper once in awhile when the hedge proves to be a waste.

The question now is: _**how often does the worst case happen, and when it does, how bad do the losses look?**_

Before we dive into the result to answer that, we want to provide some insight into how we make sure that the backtests we run are as accurate as possible.

---

**How We Maintain Accuracy**

For the given spread or option we want to use in a test, we use [polygon.io](https://polygon.io/) to pull **every** nanosecond quote, as follows:

[

![](https://substackcdn.com/image/fetch/w_1456,c_limit,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F36465005-69ff-4c8c-9b83-585e9c01d588_873x484.png)



](https://substackcdn.com/image/fetch/f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F36465005-69ff-4c8c-9b83-585e9c01d588_873x484.png)

While these are the real top-of-book quotes that represent the actual prices we would be able to trade at, there are millions of these quotes per day. So, for each minute, we use the median bid/ask quote to _represent_ the prices available at that moment:

[

![](https://substackcdn.com/image/fetch/w_1456,c_limit,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F23598e1a-a181-4c22-a563-01a156c8ce6c_771x450.png)



](https://substackcdn.com/image/fetch/f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F23598e1a-a181-4c22-a563-01a156c8ce6c_771x450.png)

This method of using NBBO (national-best-bid-and-offer) quotes has proven to be substantially more effective at replicating real-world PnL/execution than standard option OHLC values.

---

So, now that you understand **how** we run these tests, let’s get back to the strategy:

[

![](https://substackcdn.com/image/fetch/w_1456,c_limit,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F9e283ab4-fb35-47b8-a4ac-77ac0fe40757_1077x807.png)



](https://substackcdn.com/image/fetch/f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F9e283ab4-fb35-47b8-a4ac-77ac0fe40757_1077x807.png)

Strategy stats:

- 88% of the time, we walked away with a net profit
    
- We ended up hedging 61% of the time:
    
    - 81% of the time, this hedge ended up paying off
        
        - Of the 19% of the time where the hedge ended up taking a loss, the worst case loss was realized ~59% of the time
            

All combined, the strategy posted an expected value of **$24** per spread sold.

**Not bad.**

While the curve is a bit volatile, losses are made back relatively quickly while the probability of making a daily profit is high. The losses can be particularly large, especially if/when the worst case scenario happens n-times in a row, but as we expected, this only happens a small percentage of the time.

The main drawback of this strategy is the enhanced capital requirements. For instance, if we put up $1,000 of risk for our initial spread, we _may_ have to put up an additional $4,000 or _more_ to hedge away that risk if/when the time comes. Still not that bad, but it’s something to consider.

We want to develop this idea a bit more before diving head-first into production, but it’s high up on the docket and its implementation is near-imminent.

So, with that strategy currently on the bench, there’s one more we’re looking at.

#### Proposed Strategy II: Just Fade, Man

A partial inspiration for this strategy comes from the paper:

[Overnight Return, the Invisible Hand Behind Intraday Returns?](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=2689719)

The crux of the paper is that there tends to be an inverse relationship between overnight and intraday returns. In other words, the market tends to fade the overnight move.

Because this relationship is so simple to test, we can go back multiple years to see if it proved to be a legitimately predictive relationship.

In order to do this, we’ll want to use a model that will capture the following relationship:

- If the market has a positive overnight move, predict that it will go down intraday (sell calls); if it has a negative overnight move, predict that it will go up (sell puts)
    
- There are times, when it’s obvious that fading the market isn’t smart (e.g., crisis, better than expected CPI, etc.), so if the overnight move is above average, we want to just assume that direction will continue.
    
    - If there is a large negative move overnight that is above average (e.g. > 3-month historical vol), predict that it will continue to go down (sell calls) and vice versa.
        

Now, market data is very noisy — a given relationship can exist in markets, but too much noise in the training data may make a model unable to work the way you want it to. Additionally, there is a general path-dependency where your model might give different predictions for the same event based on the time period used in the training data.

To remedy this issue, we started experimenting with synthetic datasets:

[

![](https://substackcdn.com/image/fetch/w_56,c_limit,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2Fa86cfca0-0c4c-4800-a60d-fea150a8712a_500x500.png)The Quant's Playbook

Making Money with Synthetic Data and Milk Futures

Commodities are a fascinating product and are the quantitative traders’ dream — they move for clear, predictable reasons and when they trend, they trend hard…

Read more

4 months ago · 5 likes · Quant Galore

](https://www.quant-galore.com/p/making-money-with-synthetic-data?utm_source=substack&utm_campaign=post_embed&utm_medium=web)

Essentially, this is creating a noise-free dataset that demonstrates the relationship you want to capture. This way, the model has an easy time understanding the relationship you want and its behavior will be very predictable. Additionally, you only train this model once, so you don’t have to worry about which historical period to use, which dataset size, and so on.

While we didn’t _invent_ this, we’re one of the first publicly using it for quantitative trading.

Anyway, here’s what our synthetic dataset will look like for this idea:

[

![](https://substackcdn.com/image/fetch/w_1456,c_limit,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F56d86578-d222-4758-9af1-7c83e792538a_253x186.png)



](https://substackcdn.com/image/fetch/f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F56d86578-d222-4758-9af1-7c83e792538a_253x186.png)

2s represent strong market moves (overnight_vol > 3m_vol) and 1s and 0s represent standard ups and downs.

So, for each trading day, we’ll pass in the overnight returns (today’s open - yesterday’s close), and based on the prediction, we sell at-the-money puts or calls. If we get a 1 (market to go up), we sell a put; a 0 (market to go down), calls.

Let’s see how this quick logic performed:

[

![](https://substackcdn.com/image/fetch/w_1456,c_limit,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F18a4e0cb-f782-4259-b511-c3eb34e46b64_1077x807.png)



](https://substackcdn.com/image/fetch/f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F18a4e0cb-f782-4259-b511-c3eb34e46b64_1077x807.png)

Shockingly, this simple approach generated a 55% accuracy rate over the sample for an expected value of $23 per trade.

Now, considering how soft the logic is, we want to make sure that this wasn’t just a fluke over the time period. 0-DTEs were only invented around late 2022, so replicating the historical trades going back further becomes a bit tricky.

A potential solution is to just assume that if the prediction for a given day is correct, we make $2.50 (50% odds on 5-width spread), and if it’s wrong, we lose $-2.50. Obviously, even if there were 0-DTEs available back then, the real value might not be exactly 2.50 depending on where the index is (e.g., SPX at 1.6k in 2013), but it’s a reasonable enough representation of taking 50-50 odds.

Regardless, just to make sure this simulation is a good proxy, here’s what the simulated returns look like compared to the real trades pictured above, over the same sample:

[

![](https://substackcdn.com/image/fetch/w_1456,c_limit,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F47cbda96-a3aa-4003-b6e5-608d765566e9_1077x807.png)



](https://substackcdn.com/image/fetch/f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F47cbda96-a3aa-4003-b6e5-608d765566e9_1077x807.png)

If you look hard enough you’ll see the variance, but big picture, it’s a close enough proxy.

So, let’s go back a few years:

[

![](https://substackcdn.com/image/fetch/w_1456,c_limit,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F05e5b706-869f-466b-b84d-99281472dbca_1077x775.png)



](https://substackcdn.com/image/fetch/f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F05e5b706-869f-466b-b84d-99281472dbca_1077x775.png)

Over an approximately 20 year sample size, this approach was accurate ~53% of the time, yet as we addressed earlier, since we are getting odds of 50%, this 3% edge differential _seriously_ adds up in the long-term.

This strategy is kind of weird in the sense that while the approach works, the **why** isn’t really there. Because of this, there are relatively long periods where the strategy seems to have poor performance (e.g., 2013-2015) that can’t really be explained outright.

Now, a potential solution to avoiding long losing streaks is to only trade when the model is “hot”. For instance, if out of the **prior** 10 trades, the model successfully predicted the outcome 60% of the time, the system is “hot”. If it only successfully predicted 3 out of the last 10, it’s “cold” and shouldn’t be traded. The results of the prior 10 trades would be available to us at trade time, so while this kind of trade optimization can get risky, we’re playing it safe.

So, here’s what the performance looked like when we only made bets when the system was “hot”:

[

![](https://substackcdn.com/image/fetch/w_1456,c_limit,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F81fcc1ad-0e63-4699-b5ad-dd2acb86997f_1077x813.png)



](https://substackcdn.com/image/fetch/f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F81fcc1ad-0e63-4699-b5ad-dd2acb86997f_1077x813.png)

The general accuracy remained unchanged at 53%, but staying out during cold streaks seemed to have greatly helped the overall curve.

Because we were able to get a sense of long-term performance with this second strategy, it is slated for launch into production and we will likely already be trading this version by the time you’re reading this.

#### Code

As always, we’ll include the code below which allows you to replicate everything covered here for yourself:

[https://github.com/quantgalore/at-the-money](https://github.com/quantgalore/at-the-money)

To access the data, you will need to provide your own polygon.io api key. You can use code “QUANTGALORE” for 10% off. We receive no compensation for your use of the code or Polygon’s services.

**Strategy I — Hedging**

Since there isn’t a production version of this script yet, only the file for recreating the backtest is included:

12. Navigate to the “at-the-money” repository, then download and run the “atm-hedging-spx.py” file.
    

**Strategy II — Fading**

We’re a bit ahead of the curve on this strategy, so we’ve included the files for both backtests (simulated and real option values) as well as a production version that you can run in real-time to get daily predictions:

##### Backtesting

13. To run the simulated backtest over multiple years, head to the “at-the-money” repository, then download and run the “fade-backtest-sim.py” file.
    
14. To run the backtest that uses options, run the “fade-backtest.py” file.
    

##### Production

15. Run the “fade-production.py” file.
    
    1. The default logic assumes that you only want to make trades when the model is “hot”, so a text output will print letting you know the direction to sell in if the model is in such state, and if it isn’t it’ll tell you so.
        

If you don’t have much experience in Python, we strongly recommend taking our [volatility trading bible](https://www.quantgalore.com/courses/the-volatility-trading-bible). It’s essentially a streamlined series that replicates the exact tech we use, allowing you to quickly replicate these experiments and implement your own derivations.

---

Happy trading! 😄

---

_**Still needing to scratch that mental itch?**_ Check out a few more posts just like this:

- [The Volatility Trading Bible, But Better.](https://www.quant-galore.com/p/the-volatility-trading-bible-but)
    
- [A Junior Quant's Guide to Prediction](https://www.quant-galore.com/p/a-junior-quants-guide-to-prediction)
    
- [Relative Value Quant Betting](https://www.quant-galore.com/p/relative-value-quant-betting-the)
    
- [A Junior Quant's Guide to +EV Options Trading](https://www.quant-galore.com/p/a-junior-quants-guide-to-ev-options)
    
- [Making Money with Synthetic Data and Milk Futures](https://www.quant-galore.com/p/making-money-with-synthetic-data)
    

### Subscribe to The Quant's Playbook

By Quant Galore · Hundreds of paid subscribers

The premier source on quantitative finance.

Subscribe

By subscribing, I agree to Substack's [Terms of Use](https://substack.com/tos), and acknowledge its [Information Collection Notice](https://substack.com/ccpa#personal-data-collected) and [Privacy Policy](https://substack.com/privacy).

[

![](https://substackcdn.com/image/fetch/w_32,h_32,c_fill,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2Fbf1be702-7244-4f29-a030-505151a8f4af_144x144.png)



](https://substack.com/profile/269935912-byron)

[

![](https://substackcdn.com/image/fetch/w_32,h_32,c_fill,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2Fce9c43fd-0b6d-4c5b-ae88-116bfdfefd10_96x96.png)



](https://substack.com/profile/139844537-ryan-b)

[

![](https://substackcdn.com/image/fetch/w_32,h_32,c_fill,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2Ff4be0077-2927-4ecc-bf1b-d53826b67bb4_144x144.png)



](https://substack.com/profile/108040655-matias-vargas)

[

![](https://substackcdn.com/image/fetch/w_32,h_32,c_fill,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F08d0b267-ec14-401f-a76c-77300c9fa9d1_144x144.png)



](https://substack.com/profile/50486196-davis-pulins)

[

![](https://substackcdn.com/image/fetch/w_32,h_32,c_fill,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F6e91a101-418a-4015-8006-2351034bebae_1080x1384.jpeg)



](https://substack.com/profile/170263511-edward-corona)

18 Likes∙

[3 Restacks](https://substack.com/note/p-151718386/restacks?utm_source=substack&utm_content=facepile-restacks)

18

[

1

](https://www.quant-galore.com/p/insights-from-a-small-scale-quantitative/comments)

3

Share

#### Discussion about this post

CommentsRestacks

![](https://substackcdn.com/image/fetch/w_32,h_32,c_fill,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack.com%2Fimg%2Favatars%2Fdefault-light.png)

[

![](https://substackcdn.com/image/fetch/w_32,h_32,c_fill,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fbucketeer-e05bbc84-baa3-437e-9518-adb32be77984.s3.amazonaws.com%2Fpublic%2Fimages%2Fe719f67a-cca7-4d3e-8188-4e553f905667_144x144.png)



](https://substack.com/profile/90637251-ksb?utm_source=comment)

[ksb](https://substack.com/profile/90637251-ksb?utm_source=substack-feed-item)

[Nov 24](https://www.quant-galore.com/p/insights-from-a-small-scale-quantitative/comment/78668955 "Nov 24, 2024, 8:37 PM")

Thanks QG for another cool article! I'm looking forward to studying it in detail, but already have one (silly) question: at the beginning, what is the rational on why you calculate the implied_odds? I.e. were did you get that little equation of implied_odds = maxloss / strike_width?

MAny thanks indeed!

Like

Reply

Share

TopLatestDiscussions

[We Did It. We've Solved The Options Market. [Code Included]](https://www.quant-galore.com/p/we-did-it-weve-solved-the-options)

[Our last model made some money. Then it made some more money. And now, we're making even more.](https://www.quant-galore.com/p/we-did-it-weve-solved-the-options)

Oct 1, 2023 • 

[Quant Galore](https://substack.com/@quantgalore)

32

[

41

](https://www.quant-galore.com/p/we-did-it-weve-solved-the-options/comments)

![](https://substackcdn.com/image/fetch/w_320,h_213,c_fill,f_auto,q_auto:good,fl_progressive:steep,g_center/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F3ecbca5e-8f66-44b1-8801-eda3ec991449_1416x593.png)

[Building a Volatility Trading Empire [Code Included]](https://www.quant-galore.com/p/building-a-volatility-trading-empire)

[It's time for a new challenge.](https://www.quant-galore.com/p/building-a-volatility-trading-empire)

Oct 15, 2023 • 

[Quant Galore](https://substack.com/@quantgalore)

24

[

11

](https://www.quant-galore.com/p/building-a-volatility-trading-empire/comments)

![](https://substackcdn.com/image/fetch/w_320,h_213,c_fill,f_auto,q_auto:good,fl_progressive:steep,g_center/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F84a4945d-4cb5-447b-9ecc-cf273d851779_524x499.png)

[You Can Be A Volatility King Too. [Code Included]](https://www.quant-galore.com/p/you-can-be-a-volatility-king-too)

[We're in the deep end now. Like, for real.](https://www.quant-galore.com/p/you-can-be-a-volatility-king-too)

Jan 7, 2024 • 

[Quant Galore](https://substack.com/@quantgalore)

23

[

27

](https://www.quant-galore.com/p/you-can-be-a-volatility-king-too/comments)

![](https://substackcdn.com/image/fetch/w_320,h_213,c_fill,f_auto,q_auto:good,fl_progressive:steep,g_center/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2Fbc038129-6135-4486-b1d7-49a8ddb58bf3_3191x2434.png)

See all

Ready for more?

Subscribe

© 2025 Quant Galore

[Privacy](https://substack.com/privacy) ∙ [Terms](https://substack.com/tos) ∙ [Collection notice](https://substack.com/ccpa#personal-data-collected)

[Start Writing](https://substack.com/signup?utm_source=substack&utm_medium=web&utm_content=footer)[Get the app](https://substack.com/app/app-store-redirect?utm_campaign=app-marketing&utm_content=web-footer-button)

[Substack](https://substack.com/) is the home for great culture

#0dte #quantgalore 