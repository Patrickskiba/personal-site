---
layout: post
title:  "Introduction to ledger and text-based accounting"
date:   2019-12-07 10:32:23 -0600
categories: ledger-cli
---
Let's play out a hypothetical situation. Say I were to approach you on the street and ask you the following question: "Do you know exactly how much money you have, including all stocks, bank accounts, and pocket change, minus all of the debts from your credit cards?".

Chances are you would probably tell me to go away, leave you alone, and stay away from your family. But after taking a few moments to ponder the question, there's a decent chance you probably couldn't answer my inquiry, even if you wanted to. 

Another thing you probably don't know is what exactly you're spending your money on. In this modern world of inserting a piece of plastic with a chip on the end into your local coffee shop's iPad, it becomes hard to mentally track where your money is going.

To someone not really thinking about it, without having all of their expenses laid out,  they probably wouldn't notice that their total coffee expenses for a year adds up to more than a thousand dollars, or that they are eating out way too much.

The solution is to track your finances. Now when I tell you that, some images might be popping up in your head, perhaps of a dreary looking Accountant in a tie; maybe he wears a funny tie on Fridays just to get wild. He probably has an acronym next to his name on a plaque somewhere, C, P, and A. But tracking your finances doesn't have to be boring and you don't need a CPA, you just need to know a little bit about **double entry bookkeeping**.

## What is double entry bookkeeping?

Double entry bookkeeping is the art of recording transactions from one account to another. Money can never come from thin air, so two accounts must be associated with every transaction.

In double entry bookkeeping, people typically talk about one side of the transaction as a debit and the other as a credit.

Throw away any preconceived notions you have about debits and credits from the cards your bank gives you. In accounting, these words are used a little differently.

Debits - Increase an asset or expense account, or decrease a liability or equity account.

Credits- Increase a liability or equity account, or decrease an asset or expense account.

<img src="/assets/images/intro-to-ledger/double-entry.png" alt="Examples accounts in double entry accounting" />

If you're not an expert yet after looking at this picture, that's fine. I find examples make the concepts more understandable so let's go over some examples of common transactions after we install Ledger.


## Installation

Depending on your operating system, installing Ledger is typically pretty easy. I use Arch btw, but I included a few other options.

### Arch Linux / Manjaro
```
sudo pacman -S ledger
```

### Ubuntu
```
sudo add-apt-repository ppa:mbudde/ledger
sudo apt-get update
sudo apt-get install ledger
```

### macOS
```
brew install ledger
```

### Micro$oft Windows
#### Option 1:
Use Windows Subsystem for Linux like a good citizen and follow the steps described under Ubuntu
#### Option 2:
Continue living in sin and navigate to this page to download a Windows binary:
[https://github.com/AlexanderAA/ledger_binaries_windows](https://github.com/AlexanderAA/ledger_binaries_windows)

## Your first entry

Starting off a ledger may seem a little unintuitive at first. If you need to put money in your assets account to get started, where does that money come from? The answer is your equity account.

Let's start by creating a new text file called `main.ledger` and pasting in the following starting balance transaction.

The structure of a typical transcation in Ledger is quite simple:

- The first line contains the date and a description of the transaction.
- The second line contains the "debit".
- The last line contains the "credit".


```
2019/11/25 Inital Balance
    Assets:Checking                 $8.32
    Equity:StartingBalance         -$8.32
``` 

Go ahead and add an entry that looks like the snippet above to your `main.ledger` file, save, and then run the following command to see your current balance.

```
ledger -f main.ledger bal
```

You should see the following output from that command:

<img src="/assets/images/intro-to-ledger/simple-starting-bal.png" alt="Example of basic ledger balance report" />

Let's modify this entry to include more accounts, including some investments.

```
2019/11/25 Inital Balance
    Assets:Checking                   $8.32
    Assets:Savings                    $15.00
    Assets:CouchCushion               $0.72
    Assets:Crypto:DOGE                4,000,000 DOGE @ $0.002289
    Equity:StartingBalance

```

Ledger will allow the dollar value of one account to be omitted. In this example, I omitted the credit amount to **Equity:StartingBalance** but Ledger can extrapolate that value would have been **-$9,156.04**.

If we were to run the previous command again, Ledger would not calculate the dollar value of my investments. Instead, Ledger would return the value of my account in quantity of Dogecoin. Personally, I prefer to see everything in the same currency, so I use the `-V` flag to have investment values be calculated into my preferred currency.

<img src="/assets/images/intro-to-ledger/complete-starting-bal.png" alt="Example of ledger showing how much dogecoin I have" />

## Everyday transactions
I primarily purchase things with my credit card and then pay off my credit card at the end of each month. 

Your credit card falls under the **Liabilities:CreditCard** category; that money then flows into an expense account. 

If you don't use a credit card, then you would use your **Assets:Checking** or **Assets:Cash** account to make purchases against an expense account.

```
2019/11/26 Food - stop and shop
    Expense:Grocery                   $200.00
    Liabilites:CreditCard:Apple

2019/11/26 Data
    Expense:Bills                     $150.00
    Liabilites:CreditCard:Apple

2019/12/01 Rent
    Expense:Rent                      $800.00
    Liabilites:CreditCard:Apple

2019/12/02 Candles
    Expense:Recreational              $3,600.00
    Liabilites:CreditCard:Apple

2019/12/03 Utility
    Expense:Bills                     $150.00
    Liabilites:CreditCard:Apple
```

If you run the balance command again, you'll see that the **Liabilites** account is now in the negative. This value will need to be brought to zero within a month in order to avoid paying interest to the credit card company.

<img src="/assets/images/intro-to-ledger/expenses-with-credit-card.png" alt="Example of ledger after spending alot of money on my credit card" />

Since I don't have sufficient funds to pay off my credit card from my checking account, I will have to sell off my Dogecoin investments and move that money to my checking account.

```
2019/12/10 Selling Crypto to pay debt
    Assets:Crypto:DOGE            -3,000,000 DOGE {$0.002289} @ $0.002287
    Assets:Checking 
``` 

In the transaction above, the value `{$0.002289}` in the brackets is the amount I paid for the Dogecoin. The value of `@ $0.002287` is the amount I sold it for.

Now that I have enough money in my checking account to pay off my credit card, let's create a transaction to do that:

```
2019/12/11 Paying off card
    Liabilites:CreditCard:Apple               $4900
    Assets:Patrick:Checking 
```

<img src="/assets/images/intro-to-ledger/payed-off-card.png" alt="Example of ledger after paying off credit cards" />

You'll see that the **Liabilites** account is no longer returned because that account no longer has a value.

Most people with jobs will also recieve money in the form of income, so here is an example of your typical paycheck transaction to a checking account.

```
2019/12/12 IT'S PAYDAY
    Assets:Checking                           $90.00
    Income:Work
```

<img src="/assets/images/intro-to-ledger/balance-example.png" alt="Example of ledger after pay day" />

## Ledger Configuration
Typing in the `-V` flag every time I check my balance isn't very convenient, and I know that I'm going to forget to add it at some point later. This leaves me with that undesired ledger balance consisting of mixed currencies rather than all USD. While there are many ways to fix this, I prefer to use the `~/.ledgerrc` file.

So let's create a new file in your home directory called `.ledgerrc`. In that file let's type in the argument that we want run every time I use Ledger, and for clarity I will write out the long form of `-V` which is `--market`.
```
--market
```

Now if you run `ledger -f main.ledger bal`, you'll get the same results as if you had manually added the `-V` flag.
## Reporting

There are 4 primary reporting commands: 
### Register
This will produce a list of transactions as they occur over time similar to a checkbook.

<img src="/assets/images/intro-to-ledger/register-example.png" alt="Examples accounts in double entry accounting" />

### Balance 
This will print out the balance of every account that has a value.

<img src="/assets/images/intro-to-ledger/balance-example.png" alt="Examples accounts in double entry accounting" />

### Equity 
Equity is useful if you are starting a new ledger file and would like to roll over all of your accounts from the previous ledger. It will essentially produce the first `Opening Balances` transaction that you can use.

<img src="/assets/images/intro-to-ledger/equity-example.png" alt="Examples accounts in double entry accounting" />

### Print 
This will print out the file in its pure text format.

<img src="/assets/images/intro-to-ledger/print-example.png" alt="Examples accounts in double entry accounting" />

## Building reporting queries
Querying allows you to filter down reports to specific periods of time and individual/compound accounts. Here are a couple common queries that you can run, which you can then modify to suit your needs.

### All transactions to a category of accounts within a period of time
If I wanted to look at all of my credit card transactions for November, I would run the following query:
```
ledger -f main.ledger -b "Nov" -e "Dec" register CreditCard 
```

The `-b` flag takes in a begin date and
the `-e` flag takes in an end date.

I've selected to see the `register` which will give me a list of every transaction plus a running balance.

I've filtered this register on any account that matches the regex for `CreditCard`.

### Current balance of an account

If I want to look at all of my assets without having all the other accounts be returned, it is also very simple:
```
ledger -f main.ledger balance Assets
```

You can list as many arguments as you want, so if you want both assets and expense you can do:
```
ledger -f main.ledger balance Assets Expense
```

If you want all of your accounts **except** one like your equity account, you can negate that account as such:
```
ledger -f main.ledger balance not Equity
```

There are a lot more options for querying as these examples only show a few use cases. If you want the full depth of what is possible, please see the `value expressions` listed in the [Ledger manual](ledger-cli.org/3.0/doc/ledger3.html#Value-Expressions). I would also recommend taking a look at the `report options`.


## Conclusion

Ledger has so many features that this overview really only scratched the surface. Hopefully I've given you enough information to get started and begin tracking your finances. 

For more advanced features of Ledger please reference the manual. Stay tuned for more articles in the future where I dive deeper into some of the workflows not explained here, such as budgeting and the very exciting Emacs integration.

For questions and comments feel free to email me at [pskiba@posteo.net](mailto:pskiba@posteo.net).
