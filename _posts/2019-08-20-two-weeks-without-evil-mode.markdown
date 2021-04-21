---
layout: post
title:  "Two Weeks Without Evil Mode"
date:   2019-08-20 10:32:23 -0600
categories: emacs
---
In the last two weeks, I've challenged myself to leave the comfort of Vim and dive into the unknown chasm of Emacs with no Evil keybindings. 

Since discovering Vim and its modal editing style I have rarely looked back. Even when turning to other editors like Visual Studio Code, I've always required my Vim keybindings to come with me given there was a reasonable Vim emulator plugin. 

This thought process though has prevented me from exploring other alternatives like Emacs. Sure, Emacs may not have the best default keybindings, but it is essentially a blank slate editor; everything can be changed and its scripting language Elisp is incredibly powerful. Many alternative key binding layouts have already been devised that borrow the best ideas of different editors while not feeling as alien as Vim in Emacs.

So after two weeks, dozens of config file changes, and many installed plugins, I've made the following takeaways:

## The Bad Parts
### Default Emacs key bindings are terrible

There is no getting used to the default Emacs keybindings. If you did, for whatever reason, devote yourself to mastering dozens of key chords and convincing yourself that ctrl-b, ctrl-f are reasonable navigation keys, you'll probably end up with the dreaded Emacs pinkie (a form of Emacs-related repetitive stress injury). You'll most likely spend more time with your pinkie firmly pressing down ctrl than not. Without needing to beat a dead horse, it's safe to say at this point there was a need to start customizing.

### Text Object Support
Emacs, much like many other editors, supports deleting more than just one character at a time. You can delete a whole word for instance or even a whole line. There are a handful of other options that are less relevant to a programmer and more relevant to someone writing a book, such as delete sentence and delete paragraph. 

However, many features important to manipulating text for programming aren't supported. This includes the idea of manipulating matched pairs, a feature that so many Vim users are used to. If I wanted to delete everything inside of a pair of matching brackets there is no command for that using the native key bindings in Emacs.

I looked around for some plugins that could help replicate this functionality and found Expand-region and Change-inner. 

Expand-region was a library that would start by highlighting a word, then if you hit the key command again it would expand to the next contextually smart region. I thought it was a pretty good idea, but in programming environments with new or unusual syntax, like React, the expand-region command just completely ignored all the JSX, expanding way beyond the region I would want to highlight.

Meanwhile, change-inner was a horrible emulation of Vim's ci and co keys; it isn't worth anyone's time and isn't actively being worked on.

## The Good Parts 
### Avy is a fantastic plugin
For those who are unfamiliar, [Avy](https://github.com/abo-abo/avy) is a plugin written by the incredibility prolific Dutch Emacs hacker named abo-abo. It facilitates moving the cursor from place to place on the screen by allowing the user to type the first few characters. Avy will then produce a decision tree of all places where those characters occur. For those Vimmers reading, this is not too dissimilar from the Vim plugin Easymotion.

This plugin alleviated a great deal of the slowdown I experienced when moving away from Vim keybindings. In fact, I was able to navigate to arbitrary points of the screen with ease. 

Avy has several functions you can call to complete your cursor navigation, with **avy-goto-char-timer** as my preferred command. With this function, you can type as many characters as you want and as soon as you stop typing the decision tree pops up to ask which word you would like to jump to. I prefer this to the one char or two char versions because I like fewer options in my decision tree.

<img src="/assets/images/two-weeks-without-evil/avy.png" alt="Spacemacs Screenshot" />


Jumping was not Avy's only feature, as it provided ways to easily copy and delete entire lines and regions. With just a few key strokes you could copy an arbitrary range of lines without even needing to navigate over to that part of the screen.

### Xah Fly Keys
Due to Emac's customizability and the risk of developing a repetitive strain injury, a number of alternative key bindings have been developed to reduce the amount of CTRL/META key commands. 

Evil, the emulator of Vim keybindings, is of course the big one, but this challenge is to try things that aren't Vim. Other options include Modalka, Boon, Fingers, and God-mode. However, the one that caught my eye was [Xah Fly Keys](https://github.com/xahlee/xah-fly-keys) by Xah Lee. 

It's a modal style of key bindings with two modes, insert and command. Insert mode behaves much like insert mode in Vim; the user types and those characters appear on the screen. Command mode is similar to normal mode, but the keys have been rearranged and simplified. Below is the default qwerty layout for command mode.

<img src="/assets/images/two-weeks-without-evil/xah-fly-querty.svg" alt="Xah Fly Qwerty Layout" />


The most commonly hit keys are placed in the home row with the least common placed on both the bottom row and the number row (these being the hardest rows to hit). This is one of the few times I've seen a keybinding layout take into account the frequency of the commands being called and where they would be most comfortable to use.

 Additionally, there are fewer commands requiring modifiers, even when compared to Vim keybindings. While this limits the amount of commands that can be expressed with a single keybinding, it does make the ones available more accessible. 

I think the navigation keys are an improvement when compared to Vim. For instance **hjkl** becomes **jikl**. This forms a triangle much like the **wasd** keys many gamers are used to. The keys to the left and right of the respective left and right directional keys are extremes of those versions. If **g** goes left one character, then **h** will go to the beginning of the line. If it's already at the beginning of the line, it will move to the beginning of the next block. Hold it down and you can scroll up the screen with more ease than CTRL-u or Alt-v. 
### Which-key
The [which-key](https://github.com/justbur/emacs-which-key) plugin is probably something a lot of people have seen but they don't actually know what it's called. It's prominently featured in the editor Spacemacs as the contextual menu that pops up when hitting the space bar, guiding the user to the command they are trying to use.

<img src="/assets/images/two-weeks-without-evil/spacemacs.png" alt="Spacemacs Screenshot" />

I dislike forgetting a random key binding in Vim and having to look it up on Google. Which-key solves that by displaying all of the bindings under a given prefix. If you organize the majority of your key bindings under a single prefix and have them categorized, then your keys become more easily discoverable such as "window manipulation commands" under **w** and all "project commands" under **p**.

While Which-key does exist in Vim, it hasn't been adopted as widely as it has in Emacs.

## Conclusion

By the end of two weeks I was a bit conflicted. I could go back to Vim, but then I would be giving up many of the benefits of Emacs. I would have to stop using plugins like Projectile, Magit, and Counsel, which beat their respective Vim equivalents in functionality.

Additionally, my Org notes would go back to existing in a separate editor from the one I write code in.

So I decided to give in and install Evil mode to stay with Emacs, but I would apply my takeaways from the two weeks without Evil.

Taking some inspiration from Spacemacs, I now have a Which-key buffer pop up when hitting the space bar, revealing my own hand-selected set of keybinding options.

Avy is prominently represented by both "space-j" mapped to avy-goto-char-timer and "space-l" mapped to avy-goto-line, greatly increasing the speed of navigation and requiring less effort.

Evil mode is still a bit janky. Some modes like org-agenda and elfeed default to the native Emacs keybindings without configuration, but my greater familiarization with customizing Emacs can help me resolve these issues and achieve the best-of-both-worlds scenario that I've wanted.

For questions and comments feel free to email me at [pskiba@posteo.net](mailto:pskiba@posteo.net).
