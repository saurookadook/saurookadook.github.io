---
layout: post
title:      "Challenges of the Ruby Gem CLI Project"
date:       2018-01-26 20:58:27 +0000
permalink:  challenges_of_the_ruby_gem_cli_project
---


If you asked me what I'd be doing 5 years ago, I certainly wouldn't have guessed I'd be building my first Ruby Gem, a CLI application called *Astrological*, but here I am! And I'm already looking forward to working on the next projects, though I don't doubt that the process of building this project will help me in the future.

The intent of *Astrological* is fairly simple: ask a user for an astrological sign they would like to view, then ask for a time period for the reading they would like to view, and finally returning excerpts of readings from a few different sites, along with the corresponding URLs pointing to their origin. All in all, I think the CLI was fairly easy to build, and perhaps that's why it was one of the first things I decided to build. Following Avi's advice from the walkthrough video, I layed out the logic of how I wanted my program to interact with the user and, where necessary, put in temporary values to test that it was working correctly.

The difficult aspects of this project for me consisted largely of two things: scraping certain websites and restructuring my code. In scraping a couple of the websites I had chosen, I began to realize why program design is so important, as many of the HTML documents were written in a way that made scraping particular pieces of information fairly difficult. A few of the websites in particular I just couldn't seem to scrape from at all, later realizing that the information I was looking to scrape was being added via JavaScript. Though it *is* possible to scrape information from JavaScript with Nokogiri, I luckily ended up not entirely needing information from that site and opted to leave it out.

Restructuring, on the other hand, posed a set of different challenges. Again following Avi's `daily-deal` walkthrough, I originally had all of my code responsible for scraping inside of my `sign` class, something I realized was not ideal but it at least allowed me to create a working program upon which I could then further improve. In restructuring, it was suggested that I model my `scraper` class similarly to the way it was done in the Studen Scraper lab, which had various scraped values stored as values in a hash, the corresponding keys of which aligned themselves with instance variables in the `student` class (in my case, the `sign` class). This design ended up working extremely well because of the nature of the data I was scraping and looking to `puts`, and allowed for much cleaner and well-organized code.

Though my project is pending review, I'm proud of the work I've done on it and, again, never would have thought I would be making things like *Astrological*. However the more I learn to code, the more I love it, and I hope that the challenges in building this Gem are just the start of many more to come.

If you'd like to view *Astrological*, the GitHub repo can be found [here](https://github.com/saurookadook/astrological-cli-app.git).






