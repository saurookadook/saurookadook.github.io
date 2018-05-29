---
layout: post
title:      "Ohhhh, JavaScript...."
date:       2018-05-29 20:06:55 +0000
permalink:  ohhhh_javascript
---


Where to even start with this project. Unlike the previous Rails project, the addition of JavaScript to my existing application proved to be a big challenge. Largely due to the history of its creation, I knew that JavaScript could be a bit finnicky, but some of the issues I found myself fixing were very unexpected.

For starters, one piece of advice I have for anyone beginning to learn the language with Rails: REMEMBER THAT ALL OF YOUR FILES IN THE `app/assets/javascripts` DIRECTORY WILL BE MASHED INTO ONE FILE WHEN RUNNING YOUR APPLICATION IN THE BROWSER. That's in all caps not because I aspire to be an infamous, internet troll, but because it will save you from a few possible headaches that I ran into. One of them is that if you have two different functions with the same name in different files, then the only functions that will run (on any page) is the last one that was defined. In my case, I had two functions called `attachListeners` and found after the creation of the second one, that the feature I had just created wasn't working. In fact, clicking on the button resulted in nothing happening at all, not even an error message. 

The biggest challenge following that, JavaScript seems to be very picky about the conditions that allow it to run properly. What I mean by that, is that the same code that works for one person won't for another, whether it be a difference in browser, Chrome extensions, operating system, etc. I can't exactly say which one of these things is the main culprit, but I did notice that very similar code that worked in other people's projects either didn't work entirely for me or needed to be tweaked. 

To wrap up, I think one of the biggest things I've learned is to read the documentation thoroughly, even more so than with Rails. Although I ran into a troublesome things while creating my initial Rails project, I found that because JavaScript can be difficult to work with at times and almost nonsensical in its reasoning at others, the necessity for reading the documentation about various functions and methods makes it that much more important. I certainly know that I will be paying much closer attention to them in the future after working on this project.
