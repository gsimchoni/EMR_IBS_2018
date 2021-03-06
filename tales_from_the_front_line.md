Tales from the Front Line: How Statistics is REALLY used in the Industry
========================================================
author: Giora Simchoni
date: December 18th, 2018
autosize: true

EMR-IBS 2018

This RPres/html is available at Github or at:
http://bit.ly/emribs2018

Who am I
========================================================

- Graduated MSc Statistics from TAU in 2010
- Data Scientist (otherwise they won't hire me) subspecies Statistician
- 888 2 years, ebay 3 years, IBM 1.5 years, vFunction less than a year
- Blogger: [Sex, Drugs and Data](http://giorasimchoni.com)
- R/Python enthusiast: [Github](https://github.com/gsimchoni)
- Here to talk about the Horror I've seen

<div class="footer">bit.ly/emribs2018</div>

Attack of the Automated Tests
========================================================
title: FALSE
type: back_image

# Attack of the Automated Tests

I dare not say "Randomized Controlled Experiment"
========================================================

- ebay holds dozens of A/B Tests on millions of users, at any given time
- ebay prides itself on being "Data-Driven"
- No feature will be released until it passed the alpha = 0.05 threshold
- The one dependent variable that matters: `$$$`
- A t-test. Always a t-test.
- Automated platform for a Product Manager to define an experiment, submit it and see the results
- N is in the millions, what could possibly go wrong?

<div class="footer">bit.ly/emribs2018</div>

What could possibly go wrong?
========================================================

- This is a real ebay experiment, from 2012 ([source](https://users.soe.ucsc.edu/~draper/Reading-2015-Day-5.html))
- Results were analyzed by David Draper of UC Santa Cruz
- 12M users in Treatment group (new feature) spent 8c less on average during 2 weeks, than 12M users in Control group
- p = 0.18, lift = `mean(y.T)/mean(y.C) - 1 = -0.82%`
- Result: Feature not deployed


```r
y.T.nonzero <- scan('data/ebay_treatment.txt')
y.C.nonzero <- scan('data/ebay_control.txt')
n.zero.T.values <- 11100587
n.zero.C.values <- 11096065
y.T <- c(rep(0, n.zero.T.values), y.T.nonzero)
y.C <- c(rep(0, n.zero.C.values), y.C.nonzero)
t.test(y.T, y.C)
```

Anyone can t-test
========================================================


```

	Welch Two Sample t-test

data:  y.T and y.C
t = -1.3396, df = 24061000, p-value = 0.1804
alternative hypothesis: true difference in means is not equal to 0
95 percent confidence interval:
 -0.1854994  0.0348771
sample estimates:
mean of x mean of y 
 9.127794  9.203105 
```

<div class="footer">bit.ly/emribs2018</div>

Histograms much?
========================================================
![plot of chunk ebay03](tales_from_the_front_line-figure/ebay03-1.png)

***

The `$$$` metric is problematic at best:
- 90% of users in both groups spent 0$
- highest obs in Control: `$161K`, in Treatment: `$91K`

Power Analysis?
========================================================

```r
power.t.test(n=12000000, delta=0.0005, sd=140)
```

```

     Two-sample t test power calculation 

              n = 1.2e+07
          delta = 5e-04
             sd = 140
      sig.level = 0.05
          power = 0.02551569
    alternative = two.sided

NOTE: n is number in *each* group
```

- effect-size = `(mean(y.C) - mean(y.T)/sd(y.C) ~ 0.0005` --> low power for tiny lifts, even with n = 24M!
- haven't even begun talking about making dozens of tests at any given time

A simple Sensitivity Analysis
========================================================



![](images/ebay04-1.png)

***

Would you trust my test if I told you that shifting 1 millionth of observations from Control to Treatment completely overturns results?

See much more in [David Draper]()'s work.

Multiple Testing Carnage
========================================================
title: FALSE
type: back_image

# Multiple Testing Carnage

Remember "The Platform"?
========================================================

![](images/optimizely.png)

<div class="footer">bit.ly/emribs2018</div>

You get a P-value, you get a P-value!
========================================================

- There are countless web start-ups offering "a platform" for A/B tests
- A typical package: configuration, analysis and reporting
- A shiny email delivered to your "doorstep" daily, containing 3D pie-charts and the P-value between the A and B groups:
  - for yesterday
  - for the entire period
  - for men, for women
  - for people below 35, for people over 35 (or, you get to choose! Why so traditional?)
  - for users from US, for users from UK
- Stopped counting after 100 P-values.

<div class="footer">bit.ly/emribs2018</div>

The Battle of Inference
========================================================
title: FALSE
type: back_image

# The Battle of Inference

Can you predict if a handbag will sell or not?
========================================================

- Gathered 120K random handbags
- Left out 20K random handbags as validation set
- And additional 20K as testing set
- Of the remaining 80K training set about 8K sold
- Took 8K which sold + random 8K which did not sell
- Feature Engineering Galore
- Balanced dataset, tried LR, SVM, GBT, RF, NN...
- RF had the best result on validation set in AUC terms, say 90% precision and 80% recall with cutoff score of 0.5
- Chose RF, trained on training + validation sets, reported results on testing set

But we have 20 millions handbags at any given moment!
========================================================

- Learned Scala (~1 week)
- Learned Spark (~1 week)
- Completely re-written Data extraction, Feature Engineering and RF code(~2-3 months)
- Fitted RF model on 2M sold handbags vs. 2M unsold
- Result: 90% precision and 80% recall with cutoff score of 0.5

<div class="footer">bit.ly/emribs2018</div>

Rubber Stamp Offensive
========================================================
title: FALSE
type: back_image

# Rubber Stamp Offensive

And there are, of course, Project Managers and Product People
========================================================

- A team of 20 people has worked for over a year on a new platform for an online gambling site
- The platform uses modern architecture, is slicker, more flexible, allows for never before seen gaming features
- Again, the A/B Test
- Users visit the site, download the software, sign in, play some for free, give money (this was 2011)
- Typically each step in the Conversion funnel is tested with a proportion test:

<div class="footer">bit.ly/emribs2018</div>

Who volunteers to tell them?
========================================================

![](images/ab_funnels2.png)

- <span style="color:red">Significantly less people sign-in after download</span>
- About the same % start playing with bonus
- Slightly more % deposit but it's not significant
- Bottomline: slightly better conversion for new platform, but it's not significant

<div class="footer">bit.ly/emribs2018</div>

What do you think the PM heared?
========================================================
incremental: true

![](images/ab_funnels2.png)

- It turns out the download file for the new platform was 70% larger than old file.
- Install time was considerably longer.
- A bit of a scandal, really

<div class="footer">bit.ly/emribs2018</div>

Invasion of Data Science
========================================================
title: FALSE
type: back_image

# Invasion of Data Science

What do I want from teachers of Statistics?
========================================================
incremental: true

- Everyone learns Statistics because everyone needs Statistics and actually uses Statistics
- So teach them *all* the best you can, or don't teach them at all
- More emphasis on diagnostics
- More emphasis on case studies such as these and projects
- Real, messy, unbalanced, biased, uncomfortable, big data
- Log-normal distribution

<div class="footer">bit.ly/emribs2018</div>

What do I want from students of Statistics?
========================================================
incremental: true

- Everyone uses Statistics, it's called Data Science now
- However it's called it's YOUR responsibility
- Be there when the feature is developed, when the experiment is configured
- Power analysis
- Translate to business owners the business meaning of your estimates and decisions

<div class="footer">bit.ly/emribs2018</div>

Thank You
========================================================
title: FALSE
type: back_image

# Thank You.
