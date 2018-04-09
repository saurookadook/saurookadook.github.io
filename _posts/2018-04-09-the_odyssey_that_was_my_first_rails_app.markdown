---
layout: post
title:      "The Odyssey That Was My First Rails App"
date:       2018-04-09 18:48:12 +0000
permalink:  the_odyssey_that_was_my_first_rails_app
---

Well, many moons later and after several figurative oceans crossed, I've finally built my first Rails app, a site called Adopt Don't Shop (yes, taken from the hashtag).

And holy shit, did I underestimate the size of this project.

Perhaps it felt more daunting in part because the Sinatra project seemed fairly straightforward for me, even with the occasional curveball I threw myself. However, I found that for all of the amazing things Rails has to offer, there are certain aspects which can be very difficult to navigate.

My first blunder was attempting to use Devise for my user authentication, despite the explicit warning in the README: "If you are building your first Rails application, we recommend you do not use Devise." For those of you, like me, who read that and decide to themselves, "I'm sure it won't be easy, but figuring it out will be great in the long run," *listen to them.* I spent the better part of a week trying to incorporate Devise, since I thought it would be good practice, only to find myself buried in the source code and running across bizarre errors with little way to debug them, given my current knowledge of both Rails and coding in general. The tipping point for me was trying to incorporate custom views (I wanted Users to be able to log in with a username and password, instead of Devise's default email and password). Despite getting the views to display correctly and checking that information was being passed appropriately, I was getting a strange authorization error, which I had little clue how to fix even after scouring several forums discussing similar issues.

As with my Sinatra project, my second challenge came with setting up my database in a sensible way. After changing around my migrations entirely once I decided against using Devise, I still ran across issues which I hadn't encountered before. After several (emphasis on *several*) revisions, my database ended up containing six tables: Users, Pets, Shelters, Employees, Caretakers, and Addresses. The main issue presented itself with the relationship of Pets to both Users and Shelters. My idea was that Users and Shelters both have many Pets, but a Pet could only belong to one User or Shelter at any given time. Enter Polymorphic Associations, a type of relationship I was not familiar with, but with which I was quickly forced to become accustomed. These types of associations allow the `belongs_to` component of a `has_many` relationship to become flexible, by including special columns in the migration and a slight change to the usual syntax in the appropriate models:

```
# in migration
class CreatePets < ActiveRecord::Migration[5.1]
   def change
	    create_table :pets do |t|
			   ...
				 
				 t.references :owner, polymorphic: true, index: true
				 
				 ...
		 end
  end
end

# in models
class Pet < ApplicationRecord
   belongs_to :owner, polymorphic: true
end

class User < ApplicationRecord
   has_many :pets, as: :owner
end

class Shelter < ApplicationRecord
   has_many :pets, as: :owner
end
```

The above code in the `Pets` migration creates `owner_id` and `owner_type` columns, both of which either relate to a User or Shelter. For example, if `jim` (`User`) has an `id` of `1` and is the owner of `booboo`, the `Pet` instance of `booboo` will have the attributes `owner_id = 1` and `owner_type = "User"`, but if `booboo`'s owner is `SPCA`(`Shelter`), which has an `id` of `3`, then `booboo` will instead have the attributes `owner_id = 3` and `owner_type = "Shelter"`. Moreover, these associations can be changed by assigning a new owner to a pet and then saving that instance, simultaneously adding that pet to the new owner and removing it from the previous one.

On that thought, my other greatest obstacle with this project (among many others....) was that of persisting data properly. Some of this was not entirely understanding the inner workings of the  `accepts_nested_attributes_for` method. After reading through the source code and several different docs, I realized that there are several ways of dealing with this problem, between setting your own methods, using `reject_if:` to ignore blank entries (i.e. ignore custom entries for an object in a nested form if you also have selection boxes for any of those objects which already exist on your site), checking to make sure that your strong params are accepting/permitting the correct data, having `hidden_field`s for `id`s of objects which you may need to pass to a params method but don't necessarily want visible on your site, etc. There are at least a dozen different ways of dealing with data persistence, and found through my debugging that the variance is there to give flexibility for different types of data structures.

All in all, I DID have fun working on the project, and looking forward to polishing it even more. There is still quite a bit of cleaning up to do in my code, and there are other features which have yet to be added that I'm looking forward to working out and buliding. 

