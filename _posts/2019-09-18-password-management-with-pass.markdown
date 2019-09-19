---
layout: post
title:  "Password Management with the Pass and Emacs"
date:   2019-09-18 07:20:23 -0400
categories: unix tools
---
There are many popular services available that offer password management. Companies like Lastpass or Dashlane probably come to mind. 

However, these services are not free, both monetarily and ethically. You have to place a lot of trust in a company to never leak any passwords, despite being both a target for hackers and powered by teams of humans who are inherently error prone.

My solution to this problem is the command line utility [Pass](https://www.passwordstore.org/).

With Pass you get full control over your data, it's free and simple to use, and has clients for many platforms and libraries, including Emacs and dmenu (which brings a tear to my eye). Unlike the previously mentioned commercial solutions, you can also fully understand how your privacy is being secured. Pass is essentially just a light wrapper around GPG, which facilitates the creation and management of GPG encrypted files, each containing just one password and optionally some metadata.

To get started, first download Pass from your package manager of choice.

```
sudo pacman -S pass
```

You'll then want to generate your GPG public/private key pair. For those who are unfamiliar with GPG, never reveal your private key - that's why it's private - but your public key can be sent to your friends so they can send you secret messages.

In a team password sharing scenario, you would need to have a copy of all your team members' public keys so that each member's private key can be used to decrypt the file. This allows multiple key pairs to be associated with the same file.

Below is the command for generating  a new key pair, which comes with a few sane defaults such as 2048 RSA as the level of encryption.

```
gpg --generate-key
```
You'll be prompted to enter your name and email address. It should be your actual name and email address, especially if you plan on sharing your key with other people.

Then you'll be asked to enter a good password. Don't forget this; write it down somewhere. 

With the below command you can list out all of the keys on your computer. You should see the new key you just generated.
```
gpg --list-keys
```

When specifying which gpg key to use in the CLI, most utilities will accept the key id, fingerprint, email address, or name associated to that gpg key as an identifier.

The command below will initialize your default pass directory located at .password-store. All passwords you generate will be located in this directory.
```
pass init pskiba@posteo.net
```
Then to create an entry for a new password, you use the `insert` command. By default, Pass is designed to store just the password in each entry. 

```
pass insert -m email/gmail
```
I personally prefer to also include my username, and Pass allows for adding metadata to your password file with the `--multiline` flag or `-m` for short.

```
pass insert -m CreditCard/Visa
```
The first line in a multi-line file should be your password. When you are done populating the file with the data you want saved, CTRL-d will exit the entry prompt.

```
ThisIsATestPassword
Username: this_is_the_username
```

To retrieve your password type pass, followed by the name of your account including its prefix:
```
pass CreditCard/Visa
```
If you use the `-c` flag, the password will be copied to your clipboard and cleared after 45 seconds.

```
pass -c CreditCard/Visa
```

In some cases, you may want a subgroup of passwords that are separate from your primary set. In the below example I create a subgroup called TeamPasswords with a list of individuals, already with their public keys registered in my key list, to give them access to decrypt these files.
```
pass init -p TeamPasswords pskiba@posteo.net jeff@gmail.com steve@aol.com billytester@hotmail.com
```

That subgroup can then be added to, and viewed via, the prefix name you gave it. For example:
```
pass insert TeamPasswords/SQL/DEV
```

<div>
<amp-ad width="100vw" height="320" type="adsense" data-ad-client="ca-pub-3392919614221850" data-ad-slot="2178961690" data-auto-format="rspv" data-full-width>
      <div overflow></div>
</amp-ad>
</div>

## Git Integration
Git integration is super simple. Pass provides a command to initialize a new git repository to keep track of your password changes. Simply run:

```
pass git init
```
The benefit to creating a git repository through Pass vs running the traditional `git init` command in your .password-store directory, is Pass's repository management feature. Each time you add, delete, and edit a password, Pass will generate a new commit for you automatically.

Feel free to push this repository out to your service of choice, or your own self-hosted solution. Since these files are encrypted, they are perfectly safe out in the wild as long as you keep your private key and master password away from prying eyes and your snooping girlfriend.

I would recommend though, using a service with a private repository option, since nobody wants to see your folder of encrypted passwords anyway.

```
pass git remote add origin respostory_url_here
pass git push -u --all
```

## Emacs Integration
If you are a sophisticated individual who partakes in the finer things in life, you might also be interested in the Emacs package for Pass, which provides all your favorite commands in your favorite operating system. 

Install the [pass](https://melpa.org/#/pass) library from melpa and you're good to go. Run pass from M-x and you'll be greeted with this lovely UI.

<div>
<amp-img  height="683" width="1247" layout="responsive" src="/assets/images/pass-cli/emacs-pass.png" alt="Emacs Pass Package" />
</div>

The top area reminds the user of all the keyboard commands available. With just a few keystrokes you can select an account, copy a password, launch your browser of choice, and have that password be cleared from your clipboard in 45 seconds.

All of these commands are associated with functions that can be called directly. `Password-store-copy`, for example, will create a prompt in the mini buffer, listing all the available passwords, and the selected password will be copied to the clipboard. Since this is 90% or more of people's interactions with Pass, I recommend associating a keyboard shortcut to this command so it can always be accessible. 

## Conclusion

That wraps up this summary on using Pass for password management. Let me know if you have any questions or comments. Feel free to send me an email at [pskiba@posteo.net](mailto:pskiba@posteo.net).
