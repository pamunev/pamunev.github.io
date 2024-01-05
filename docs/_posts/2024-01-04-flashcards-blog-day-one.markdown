---
layout: post
title: "Flashcards Blog Day One"
date: 2024-01-04 23:47:25 -0600
categories: blog post
---

I’ve decided to start a blog documenting my progress on an API I’m building for a front-end project that I have (flashcards for studying).

I’m starting it because I’ve been working on it for a while now, and I feel like I’m learning new things every work session, so I want to have a log of it so that I can come back and learn things better.

There’s too much I’ve learned, and too much of this journey I’ve already traversed with this project, so I’m not going to try to recap all of it right now. I’ll just start with where I’m at currently.

Today I tried to get my “list” knex function in my decks.service.js file to return the decks in my database (including the id, name, and description of each deck), along with a “cards” key for each deck.
My cards table is associated with my decks via a foreign key called deckId. Thus, the columns in my cards table are: id, front, back, and deckId.

I found a lesson in my Thinkful curriculum about joining tables with Knex (I remembered I had learned about it at some point), and I adjusted my “list” function according to what the lesson said. One of the key parts of this lesson was to create a utility function called mapProperties, and they provided code for me to use.

However, these instructions were for an exercise where we were trying to get a “products” table that had a “supplier_id” foreign key, and join the supplier_id info, so that all of that would be returned. In other words, return each product with their corresponding supplier information (a many to one relationship).

This isn’t working for my project, and I’ll explain my hypotheses regarding why, but first I’ll describe what’s being returned when I call the “list” function: an object containing other objects. These other objects are each a card, and they include information about the deck they belong to, and about the card itself. Therefore, I’m receiving 4 objects within this general object, since I have 4 cards in my database (I have 2 decks).

I need the list function to return an ARRAY (so that I can later use the .map method on it) of decks, with all the keys that a deck usually has, plus a “cards” key. Each “cards” key should contain the cards within that deck. Whether I want that to be an object or an array, I’m not sure right now.

Before today, when the “list” function looked like this:
{% highlight ruby %}
function list() {
return knex("decks as d").select("\*");
}
{% endhighlight %}

it returned an array that looked like this:
{% highlight ruby %}
[{id: 2, name: 'second deck', description: 'second description'}, {id: 1, name: 'Testing', description: 'test description'}]
{% endhighlight %}

Whew. An array. At least it was an array.

Now that the “list” function looks like this:

Oh, wait. You know what? I had forgotten to restart the back-end! Now that I did, it’s kind of working.

Good lesson to learn! Always restart the back-end to see the changes you just made there.

Here’s what happened:
Earlier today, the “list” function looked like this:
{% highlight ruby %}
function list() {
return knex("decks as d")
.leftJoin("cards as c", "d.id", "c.deckId")
.select("d._", "c._")
.then(addCards);
}
{% endhighlight %}

It was returning an object with several other objects representing cards, like I said before.

Now, the “list” function looks like this:
{% highlight ruby %}
function list() {
return knex("decks as d")
.leftJoin("cards as c", "d.id", "c.deckId")
.select("d._", "c._")
.then((data) => {
return data.map((deckData) => addCards(deckData));
});
}
{% endhighlight %}

Now, my front-end is not showing any errors. It’s showing three decks with the same name and description – one per card in that deck. Then a fourth deck with the single card that belongs to deck 2.

My next step is figuring out how to make those 3 decks be only 1 deck with all 3 cards within it.
