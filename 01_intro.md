# Intro

## Programming: a general overview

* I don't think I'm qualified enough to teach you programming as an art — that's something you have to master yourself
    * You was taught how to write in the school – but they don't teach you how to write poems
* How it's going to go for people who'll stick around:
    * ifs and loops — what we starting with today
    * writing own context managers and using metaclasses to force your will onto customers — best example is [So you want to be a Python expert?](https://www.youtube.com/watch?v=cKPlPJyQrt4) by James Powell
      * I highly recommend all of his lectures, start with basic ones
    * ifs and loops — [as said](https://twitter.com/ID_AA_Carmack/status/1466934223831506951) by one of the best programmers there is — John Carmack, THE John Carmack of Doom and Quake fame. And he knows what he's talking about — his code runs on anything with a processor and screen.
* What I can help you with is the tool — Python in our case

## Why Python

* You tell me — you came here

  * Ask for actual answers

* What is see as good in Python — but mind that I'm not trying to sell it to you:

  * **Show and tell!**

  * quick to start getting things done

    * ```python
      python
      'Hello, World!'
      ```

  * simple syntax and concepts

  * lot comes in the box and even more made available by huge and active community

    * ```python
      import itertools
      list(itertools.combinations('ABCD', 2))
      ```

  * popular — thus a lot of [opportunities](https://spectrum.ieee.org/top-programming-languages-2022)

  * `import this`

* What I'm not a fan of (but remember that I'm biased):

  * my personal pain of last few days — it's easy to get into dependency hell, especially on ARM CPUs
  * whitespace code block structure — it seems weird at first, nice after that and makes you irrationaly angry in the end, more so when you try to keep your code to some standards of cleanliness

## Course structure and goals

* For me:

  * At first I was just learning along with the group
  * Now I'm getting some kind of pleasure from teaching — which I wouldn't expect few years ago
  * Making me doing something aside from work and degrading with youtube

* For you:

  * You'll know Python a bit better than before
  * You'll have project done if you through with it
  * You'll have something else to do aside from work and degrading with youtube

* What's Nielsen's interest in it

  * Why do they allow this in work time, especially when we now have such lack of hands in ML team?
    * Ask for actual answers

  ![](./img/forces.jpg)

* Structure:

  * I'm not entirely sure, but mostly will be based on last editions of Learning Python (Lutz) and Think Python (Allen B. Downey) — you might as well get these and flip through in free time. They're pretty easy to find both in paper or digitally. Get English version.
  * We will be going in the small-to-big direction: learning small things and trying to build bigger picture with the parts we know so far. Except for next lecture, but you'll see.
  * This is going to be learning experience for both parties — I'll be writing the course (and publishing the notes on GitHub) as we go
  * I'll try to find exercises for topics and publish them in group channel
  * Few lectures in we'll have first big task, and probably second most difficult one — come up with projects to finish before the end.

## Prerequisites

* It's best to have \*NIX laptop — Linux is perfect, older (x86) Mac is good too. Apple silicon macs are ok, Windows — my condolences
  * Linux comes with Python, or at least it's easy to get it and get it working properly
    * **show** `prepare_ec2`
  * Macs do come with Python, but on lot of versions it's broken, and default is 2
  * Mx Macs have different CPU architecture which means additional packages have to be built (and most importantly C extensions compiled) for it before distribution — and not every developer has access to Mxs. Python itself works fine
  * Windows is a mess — I used to have Python on my personal win machine, which is left in Odessa for now; and I ended up getting entire separate laptop for running Linux for Python reasons. Situation is getting better, I've heard, and there are many good guide — start with Realpython [one](https://realpython.com/python-coding-setup-windows/)
* For first few lectures all we'll gonna need is working Python3 interpreter
* End result of your preparation should like this:
  * `python` or `python3` launches Python REPL (Read–eval–print loop)
  * `pip`, `pip3` or worst-case scenario `python -m pip` should produce pip help info
* Personal GitHub account — for now just have it, we'll use it later
  * Don't forget RSA!

## Closing word

* Python has great [documentation](https://docs.python.org/3/), get used to it
  * You might even find *why* it's called Python there
* IDE of your choice:
  * PyCharm if you like swiss army knife approach
  * VSCode if you word «Electron» doesn't send shivers down your spine
  * Sublime or Notepad++ for now will work wonders
* Terminal is good for you:
  * even windows one, although I'll recommend getting some ConEmu for it — conemu or tabby, with either WSL or at least GitBash
  * On \*NIX that's your first tool of choice, and any UI solution is second
* Best way to learn is do:
  * Python was created as addition/replacement for Bash scripts to make small handy tools — you should do it too
  * Don't bite more than you can chew from the get go, but don't underestimate yourself — most likely you'll be able to do it with enough patience and persistence
  * Automate it!
* Writing lectures is far more difficult that I imagined — I've been writing for almost two albums (those would be Protomen — Father Of Death, Act II and Polkadot Cadaver — Last Calls in Jonestown) now with ideas being with me for a few days at least; and editor shows that I'm only at around thousand words, which is about three minutes of reading
* Don't hesitate with questions and findings — we have (do we?) a channel just for that reason. Don't keep your problems and discoveries to yourself. Teamwork makes a dreamwork!

## Questions

* Language != programming
