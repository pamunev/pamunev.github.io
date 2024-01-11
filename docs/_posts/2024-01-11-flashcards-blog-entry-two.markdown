---
layout: post
title: "Flashcards Blog Entry Two"
date: 2024-01-11 15:29:25 -0600
categories: blog post
---

Okay, a small success today.

Now the decks are listed where they should be, and each one of them says it has 0 cards. It used to be blank where it says 0. Progress!

I need to make adjustments, but the way I achieved it (after some research) was this:

I created a utility function that uses lodash and maps out the properties of an object in a certain way. It’s called map-properties.js and looks like this:

{% highlight ruby %}
const lodash = require("lodash");

function mapProperties(configuration) {
return (data) => {
if (data) {
return Object.entries(data).reduce((accumulator, [key, value]) => {
return lodash.set(accumulator, configuration[key] || key, value);
}, {});
}
console.log(`Mapping property ${key} to ${configuration[key] || key}`);
return data;
};
}

module.exports = mapProperties;
{% endhighlight %}

Then I invoked it in my decks.service.js file, and created the configuration for the cards like this:

{% highlight ruby %}
const mapProperties = require("../utils/map-properties");

const addCards = mapProperties({
id: "cards.id",
front: "cards.front",
back: "cards.back",
deckId: "cards.deckId",
});
{% endhighlight %}

Then I altered my list() function so that now it looks like this:

{% highlight ruby %}
function list() {
//return { hello: "world" };
return knex("decks as d")
.leftJoin("cards as c", "d.id", "c.deckId")
.select("d._", "c._")
.then((data) => {
const decks = [];
// Organizing the data into decks
data.forEach((row) => {
let deck = decks.find((d) => d.id === row.id);

        if (!deck) {
          deck = { ...row, cards: [] };
          decks.push(deck);
        }

        if (row.cardId) {
          // I'll use addCards to map card properties
          const card = addCards(row);
          // I'll add the mapped card to the "cards" array
          decks.cards.push(card);
        }
      });
      return decks;
    });

}
{% endhighlight %}

I still need to figure out how to get the cards that correspond to each deck to be nested within the decks they’re associated with. I’ll need to make some adjustments to this list formula.

However, I’m getting closer.
