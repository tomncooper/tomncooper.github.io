+++
title = "EuroVision Sweepstakes Python Application"
date = "2014-05-11"
+++

Every year I use the great sonic festival that is [EuroVision](http://www.eurovision.tv/)
as an excuse to get my friends together and have a laugh. In an effort to make things more
interesting I run a sweepstake, every one chips in a few pounds and the countries in the
final are divided evenly between the group, with whoever was allotted the winning country
taking home the pot.

The allocation of countries originally took the form of the tried and tested "paper in a
hat" method. However, now that I am computer science student, I thought there must be a
way to do this more efficiently. Also my printer is broken and I didn't want to write all
the counties on a piece of paper. I had been looking for an excuse to learn Python and web
frameworks so put together a simple application that assigns countries from the EuroVision
Final to a list of user supplied people. This currently uses Flask as the web framework to
make a simple web interface that people on the local network can log onto and see which
countries they have been assigned. It is open source and up on
[Github](https://github.com/tomncooper/EuroVisionSelector).

Some features I want to add in the future are:

- A web interface for entering data about names and countries.
- A method to scrape the final list of countries from the web so the user doesn't have to
  enter it
- The system currently reads information from 2 text files (names in one and countries in
  the other). Eventually I would like to have a database which holds all the information
  about each countries entries, odds, votes form local users etc.
- Each user who logs on can vote at the end of the each countries song and therefore your
  party can have its own results page updated live.
- Find some way to scrape the votes information from the web so that a live leader board
  of your party can be maintained. If everyone has 2 or more countries an aggregate score
  could be calculated.
- Make the app standalone so it can be installed on 1 pc which can host the application on
  the local network.

If anyone wants to contribute I would be very grateful!
