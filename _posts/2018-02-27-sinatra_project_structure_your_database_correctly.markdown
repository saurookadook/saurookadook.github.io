---
layout: post
title:      "Sinatra Project: STRUCTURE YOUR DATABASE CORRECTLY"
date:       2018-02-27 20:29:26 +0000
permalink:  sinatra_project_structure_your_database_correctly
---


Two down, three to go. 

In some ways, the Sinatra project was easier than the CLI Gem, though it definitely posed its own set of challenges.  Going in a decidedly different direction from my CLI Gem, my Sinatra project consists of a dynamic web-app in which users can save entries of games they own, along with their genre and any notes they'd like to keep in reference to them, being anything from details about their character(s), playthroughs, general tips for other users, or reviews.

I think the difference in difficulty is due to my growing familiarity with programming, especially the with Ruby language. Since my work on the CLI Gem, I've found that debugging is becoming quicker and that I can find where an issue might be more readily (aside from the occasional typo or small brain fart). Additionally, I found most of the work in the controllers, models, and views to be fairly smooth. There were, of course, challenges with writing this code, and by no means was I able to write the code in each file in one go, but I found that finding problems was just a matter of persistence and that I was relatively able to predict the outcomes of my code.

The biggest challenge for me with this project was the organization of my database, something I will likely restructure in a future refactor. To start, the issue posed by the nature of project was one that I was unsure how to solve: a user has many games and a game has many users, while a game has many platforms but only one platform as it relates to a given user. After a few different attempts, I arrived with the following solution: I would have two game models, `OwnedGame` and `ArchivedGame`, where each of the former would only belong to one user, thereby having only one platform associated with it and the notes that a user would add for their journal, and each of the latter functioning as the site's internal library, providing a list of all games of which the site knows and all of the platforms for which they're available. 

In some ways, I know this is inefficient and plan on refactoring it. For instance, there are multiple instances of 'same'  `OwnedGame` but belonging to different users, along with an accompanying `ArchivedGame` that stores much of the same information. However with the schema I chose to use, it did provide a challenge in saving and presenting the desired data in each view (lines such as `@owned_game = @user.owned_games.detect{|o_g| o_g == OwnedGame.find_by_slug(params[:slug])}` tested my own awareness of return values of each my methods and how to grab the correct information in a meaninful way). As much as I'm happy with the finished product, I'm also already looking forward to refactoring it over time and making it even more concise and efficient as my programming knowledge grows.
