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

2. #### **Import required Library**
In this case, we will only require one library i.e. *numpy*.

Import at the top as follows;

```python

import numpy as np


```

3. ### **Initialize** required variables

Under the initialize method, we will initialize several parameters;
- Initialize cash for the purpose of the backtest(we call it the strategy cash) which would be  used on a real account.
- Set the start and end date for the backtest.
The code is as follows;

```python
        self.SetStartDate(2015, 3, 26)  # Set Start Date

        self.SetEndDate(2021, 9, 25) # Set End Date

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

4. #### Define a Method to Plot the Data

We will define the onData method to create a plot of the price of the securities.

This gives a benchmark to compare our algorithm performance.

```python
def OnData(self, data):

        self.Plot("Data Chart", self.symbol, self.Securities[self.symbol].Close)


```
This will also determine the closing price.

5. #### **Create the Trading method**

The next step will be to create the method that will do all the trading for us which will be called after every market open.

We will call it **EveryMarketOpen** for simplicity.

```python
def EveryMarketOpen(self):

```

For this, we will have to initialize one more function in the initialize method.
This the Schedule.On function which takes three parameters
- The first specifies on which day the method is called.

- The second specifies at which time the method is called

- The last specifies which method is called, in this case it's EveryMarketOpen method.

Add this to the initialize method;

```python
self.Schedule.On(self.DateRules.EveryDay(self.symbol), \

                self.TimeRules.AfterMarketOpen(self.symbol, 20), \

                Action(self.EveryMarketOpen))

```

6. #### **Implement the EveryMarketOpen method**

First, we will determine the lookback length for our breakout.

Within a utility of 30 days we will compare the current value today with the same value yesterday.

This will help determine the length of the lookback window.

- Call the History function to get data for the last 31 days or you prefered number of days.

- This is where we use the numpy library to calculate the standard deviation for the two days.

- We will list all the highest and lowest prices within a specified range, for this case, 30days

The following code falls under this *EveryMarketOpen* method to perform all the comparisons required to give a result;

```python


    def EveryMarketOpen(self):
        close = self.History(self.symbol, 31, Resolution.Daily)["close"]
        todayvol = np.std(close[1:31])
        yesterdayvol = np.std(close[0:30])
        deltavol = (todayvol - yesterdayvol) / (todayvol)

        self.lookback = round(self.lookback * (1 + deltavol))

        if self.lookback > self.ceiling:
            self.lookback = self.ceiling
        elif self.lookback < self.floor:
            self.lookback = self.floor

        self.high = self.History(self.symbol, self.lookback, Resolution.Minute)["high"]

        if not self.Securities(self.Symbol).Invested and \
                self.Securities(self.symbol).Close >= max(self.high[:-1]):

                self.SetHoldings(self.symbol, 1)
                self.breakoutlvl = max(selt.high[:-1])
                self.highestPrice = self.breakoutlvl

        if self.Securities(self.Symbol).Invested:

            if not self.Transactions.GetOpenOrders(self.symbol):
                self.StopMarketTicket = self.StopMarketOrder(self.symbol, \
                                        -self.Portfolio[self.symbol].Quantity, \
                                        self.initialStopRisk * self.breakoutlvl)


            if not self.Securities(self.Symbol).Close > self.highestPrice and \
                    self.initialStopRisk * self.breakoutlvl < self.Securities[self.symbol].Close * self.trailingStopRisk:

                    self.highestPrice = self.Securities[self.symbol].Close
                    updateFields = UpdateOrderFields()
                    updateFields.stopPrice = self.Securities[self.symbol] * self.trailingStopRisk
                    self.stopMarketTicket.Update(updateFields)

```

