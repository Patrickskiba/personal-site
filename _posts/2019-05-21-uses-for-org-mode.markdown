--- 
layout: post
title:  "My Org-mode use cases"
date:   2019-05-22 10:32:23 -0600
categories: org-mode
---
Org-mode is one of the most useful pieces of software that has entered my life. What seems like a simple app for taking notes is actually deceptively feature rich. Reading through org-mode's documentation will take you down a rabbit hole of functionality that you would not have guessed could exist from an app that operates purely on plain text files. 

I wanted to share some of the areas where I had a real life problem and turned to org-mode as a solution. Note that I won't even come close to covering all of org-mode's features, such as its text based spreadsheet functionality. I just want to spark some ideas and share the good word of org-mode. 
 
## Household Catalog
I misplace things all the time in my house. This is mostly due to the fact that I store a lot of objects in my house in bins, that are not organized. While most people would attempt to create some sort of organization system that involves categorizing objects with similar properties, I don't find that particularly intuitive because some objects could fall into multiple groups or niche groups (that don't even fill a whole bin) and I'll end up looking through most of my bins every time I'm looking for something in the end.

My solution is to keep a catalog of the contents of every single bin in the house, like so:

<img src="/assets/images/org-mode-uses/item_catalog.png" alt="Household Catalog Screenshot" />

The general strategy is as such:

 - Small and frequently used items go in a cube shelf bin and the location is expressed in a grid fashion
 - Larger items go in larger bins and are placed in a storage area and numbered to correspond with a series of entries in the catalog
 - Kitchen and bathroom items are organized simply by the numbered cabinet or drawer the item is in

This comes with a number of benefits

 1. Items do not need to fit into arbitrary categories, just fit as many items into each bin as you can
 2. You know whether you own something and how many you have (i.e. number of mini usb cables)
 3. Other housemates can search through the catalog and find what they are looking for and, most importantly, **where to put it back**
 4. You can store items without having to think about how it fits alongside everything else


## Interactive Notebooks
With org-babel, a user can embed snippets of code into their notes and have the code execute and output the result into the notebook. This is similar functionality to a Jupiter Notebook, but with [far more languages supported](https://orgmode.org/worg/org-contrib/babel/languages.html) and arguably a better platform for note taking. 

I primarily make use of org-babel's ability to work with Emacs config files and [Ledger](https://www.ledger-cli.org/) files.

### Annotated Emacs Configs
Config files can often be terse, unorganized, and jumbled together. Using org-babel, it's possible to mix org-mode style notation, with chunks of elisp snippets, to create a configuration that is less cumbersome. I can organize parts of my config inside collapsible headlines where they can be hidden until I need to read them. Through Org-mode I can annotate more elegantly than what is typically possible with the native comments most config files support. 

<img src="/assets/images/org-mode-uses/annotated_config.png" alt="Org-babel embedded emacs config" />

Finally, when the config is ready and is checked into GitHub, it gets nicely formatted for other people's reading pleasure. 

<img src="/assets/images/org-mode-uses/github_preview.png" alt="Github rendered org file" />

This coding style of treating annotation as a first class citizen is inspired by the Literate Programming techniques invented by Donald Knuth for his book series *The Art of Programming*.

### Accounting
I fully endorse keeping track of your finances using a [plain text accounting](https://plaintextaccounting.org/) software like Ledger. Org-babel is a fantastic companion tool to Ledger as it allows the user to create documents containing reports and budgets all formatted elegantly in one org file. For a full write up, I recommend reading this [post](https://orgmode.org/worg/org-contrib/babel/languages/ob-doc-ledger.html) on using org-babel and Ledger for accounting. 

## Prioritized Todo List
I'm most productive when I have a prioritized list of things to do, otherwise I end up getting overwhelmed with what I should do next. Additionally, I often have ideas of things I should do in the future, but if don't write them down I will forget them and those ideas get lost in the abyss. 

<img src="/assets/images/org-mode-uses/todo_list.png" alt="Org-mode todo list" />

Emacs makes creating these task lists incredibly easy with built-in support for: 
 - marking things as TODO or DONE
 - setting deadlines for time sensitive tasks and integrates with a calendar view called Org-agenda
 - checkboxes for tracking smaller tasks embedded in a larger headline task

Emacs supports many more features and their uses are only limited by your creativity. 

For questions and comments feel free to email me at [pskiba@posteo.net](mailto:pskiba@posteo.net).
