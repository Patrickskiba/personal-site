---
layout: post
title:  "Emacs for the React developer"
date:   2019-09-07 8:59:23 -0400
categories: emacs
---
React support in Emacs has always been a little wonky in my experience. This can mostly be attributed to React's mix of Javascript/JSX syntax, a type of syntax that does not play well with Emacs' one major mode requirement which lends itself best to single language files. 

Historically, users of Emacs have turned to Web-mode for mixed syntax files. My experience with Web-mode for JSX has been less than ideal, and other users have clearly felt the same because a plugin called RJSX-mode has gained a lot of popularity among React programmers. This mode is an extension to JS2-mode with a JSX syntax parser that is built to the official specification. 

## Emacs 27 built in JSX mode

If for whatever reason you can only use Emacs 26 or below, I recommend sticking to RJSX. 

However, with the upcoming Emacs 27, a natively supported JSX has been added that works as beautifully as any other editor I've ever seen. 

As of this post's publication date, this is the recommended way to program with React according to the [JS2-mode](https://github.com/mooz/js2-mode) author, with optional support for JS2 as an enabled minor mode.

### Installing Emacs 27

**Arch Linux**
```
yogurt -S emacs-git
```
*Note: You can replace yogurt with your AUR installer of choice*

**Ubuntu**
```
sudo add-apt-repository ppa:ubuntu-elisp/ppa
sudo apt-get update
sudo apt-get install emacs-snapshot
```

**macOS**
```
brew install emacs --with-gnutls --with-librsvg --with-cocoa --with-imagemagick@6 --HEAD`
```
*Alternative: Download the daily builds from  [emacsformacosx.com](https://emacsformacosx.com/builds)*

If I didn't mention your OS of choice, you can always build Emacs from source or find an option that works for you.

## TIDE
Tide is a development environment for Javascript and Typescript. It provides **completions**, **jump to definition**, **Eldoc integration**, **Flycheck integration**, and so many more useful features. Much of the work is powered by Microsoft's Typescript server, providing info back to the editor relevant to your project.

You'll need to have the following packages installed in Emacs.

 - [tide](http://melpa.org/#/tide)  
 - [company](http://melpa.org/#/company) (for completion)
 - [flycheck](http://melpa.org/#/flycheck) (for syntax checking)

TIDE's documentation suggests you create a setup function enabling all of the features you want TIDE to have turned on.

I then add a hook that runs this function when entering js-mode.
```
(defun setup-tide-mode ()
  "Setup function for tide."
  (interactive)
  (tide-setup)
  (flycheck-mode +1)
  (setq flycheck-check-syntax-automatically '(save mode-enabled))
  (eldoc-mode +1)
  (tide-hl-identifier-mode +1)
  (company-mode +1))

(setq company-tooltip-align-annotations t)

(add-hook 'js-mode-hook #'setup-tide-mode)
```

If you don't already have Typescript, you should install it through npm.
```
npm install -g typescript
```

Flycheck will also require a backend to use. You can install esLint, but I prefer Standard as my linter and you should too. However, Standard isn't fully compatible with my prefered formatter, Prettier, so we will need to install StandardX which allows some additional customizability.
```
npm install -g standardx
```
After you install Standardx, create a folder in your directory named .eslintrc and put the following contents in the file to disable the 'space before function paren' requirement. This is a linting feature that isn't compatible with Prettier.

```
{
  "rules": {
      "space-before-function-paren": ["error", "never"],
  }
}
```

In your Emacs config you'll want to set StandardX as the executable for default Standard.
```
(flycheck-javascript-standard-executable "/usr/bin/standardx")
```

In the root of your project you'll also want to create a folder called jsconfig.json with the following settings for TIDE to use.

```
{
  "compilerOptions": {
    "target": "es2017",
    "allowSyntheticDefaultImports": true,
    "noEmit": true,
    "checkJs": true,
    "jsx": "react",
    "lib": [ "dom", "es2017" ]
  }
}
```


## Formatting with Prettier
While TIDE has its own formatter, I don't use it. I find prettier to be the best formatter for JSX currently available. Emacs has a great package with Prettier integration.

The name of the Emacs package you can use is [prettier-js](http://melpa.org/#/prettier-js).

You will also need to install the npm package.

```
npm install -g prettier
```

In your Emacs config you'll need to add a hook to enable prettier-js-mode on js-mode.
```
(add-hook 'js-mode-hook 'prettier-js-mode)
```

Then you'll need to pass in rules on your preferred formatting style. Below are my rules, which are ideal for those who have good taste. If you would like to add or replace some of them you can get a list of arguments on [prettier's website](https://prettier.io/docs/en/options.html).

```
(setq prettier-js-args '(
  "--trailing-comma" "none"
  "--bracket-spacing" "true"
  "--single-quote" "true"
  "--no-semi" "true"
  "--jsx-single-quote" "true"
  "--jsx-bracket-same-line" "true"
  "--print-width" "100"))
``` 

## Wrap Up
That is pretty much it. These are all the packages I use for my ideal setup when developing React applications. However, one library I have not integrated into my workflow that some might find useful is [Indium](https://indium.readthedocs.io/en/latest/). This is an in-editor debugger but I prefer to use the built-in debugger in Chromium/Firefox.

If you have any questions, feel free to email me at pskiba@posteo.net. If you wish to look at my configuration with everything that we covered in this post, plus more, you can check it out here at [https://github.com/Patrickskiba/dotfiles/blob/master/.emacs](https://github.com/Patrickskiba/dotfiles/blob/master/.emacs).
