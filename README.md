### Algorithmic-Trading-Bot-Using-Python

### Introduction
Trading online has become one of the moat popular investment in the current world today. 

The likes of cryptocurrency and forex being the leading area.

Due to this popularity, programmers have emerged trying to come with
in which the trading process can be automated for more profits.

In this tutorial, we are to look at how one can start off his/her journey
in programming a trading bot.

### What is a Trading Bot?

As stated in the introduction, a trading bot is simply a robot 
in form of a software that automates the trading process.

It uses past data to give out expected outcomes that look down to the 
past data patterns.
From these past patterns, it generates patterns expected in future.

### Prerequisites/ Requirements

The main prerequisite for this tutorial is the basic knowledge of python
and its algorithms. For the matter of testing, we will use **QUANTCONNECT** which uses lean engine to integrate your code with the trading site.

That means you don't actually require an offline editor since the site provides
its own editor.


### How to develop the Trading Bot

With you prerequisites and requirements ready, you can now code along for

a practical understanding.

Go to https//: www.quantconnect.com and sign up to setup your coding environment. You can 
also use an offline editor and upload the code later for testing.




Let's get started...

### Developing/ coding it out.
We are going the develop it stepwise. Follow the steps below;

1. #### **Create a new Algorithm**

From the options on the left side of the page, click *Create new Algorithm* as shown in the photo below, you will be taken to the editor with with a class generated automatically.

For my case, here is the class generated
```python
class GeekyBlueSeahorse(QCAlgorithm):

    def Initialize(self):


    def OnData(self, data):



```

2. **Import required Library**
In this case, we will only require one library i.e. *numpy*.

Import at the top as follows;

```python

import numpy as np


```

3. **Initialize** required variables

Under the initialize method, we will initialize several parameters;
- Initialize cash for the purpose of the backtest(we call it the strategy cash) which would be  used on a real account.
- Set the start and end date for the backtest.
The code is as follows;

```python
self.SetStartDate(2015, 3, 26)  # Set Start Date
        self.SetEndDate(2021, 9, 25)
        self.SetCash(100000)  # Set Strategy Cash

```

- Still under the initialize method, we will;
    - Use AddEquity function to monitor the resolution of the intended data.

    - Initialize the amount of days we will look back to determine our break point.
        - Set limits for the lookback i.e a lower and an upper limit

    In this case, we will use a daily resolution.

```python

    self.symbol = self.AddEquity("SPY", Resolution.Daily).Symbol

    self.lookback = 20

    self.ceiling, self.floor = 30, 10


```

- The last thing to initialize is the stop loss extent.

```python
    self.initialStopRisk = 0.98

    self.trailingStopRisk = 0.9

```
The first variable determines *how close our stop loss will be to the security price* meaning that it will allow a 2% loss before it gets hit.

The second variable indicates how close our trading stop will follow the assets' price.
This means that it will trail the price for 10% which is quite big but it gives more room for price flexibility.

