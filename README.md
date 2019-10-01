# cypress-dnd-project
Web-based DND character creator and adventure tracker

# Basic Info

## Frontend:

Reactjs based

character creation:

Choose from classes, skills, race, etc



## Backend:

Stores character info and stat modifyiers

Should be able to save a charcter and retrieve it


## Strech Goal:
Multiplayer party. Create a charcter and then join a party. Completely online based. With rolls, attacks, etc, etc

# Design

## Frontend design:

Features:

* able to create a new character
* able to save character
* able to share/print character
* able to edit character
* able to create a party and/or join a session
* roll/stat modifyiers
* able to login/link your google account
* keep track of health, equipment, loot, stats, level, etc during a session


## Backend design:

Features:

* able to store info about characters
* able to update info about characters
* able to delete info about characters
* able to generate a share link (this could be done on front-end as well
* handle any auth req.
* Multiplayer sessions (using featherjs or something similar)

# Tools/frameworks

  ## Frontend:
   * reactjs
   * redux
   * yarn
  ## Backend
   * nodejs
   * express/featherjs
  ### Note
  Still on the fence about using featherjs but since I'm familar with it and it would come in handy for auth and so on
  I will give it a shot
  
# NPM Modules
* [roll parser for generating rolls](https://www.npmjs.com/package/roll-parser)
* [Possible API for spells and whatnot?](http://www.dnd5eapi.co/)
* [featherjs for realtime stat tracking?](https://docs.feathersjs.com/)
* [material-ui](https://www.npmjs.com/package/@material-ui/core)
* [Icons](https://github.com/game-icons/icons)





