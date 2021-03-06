# Featherjs Services and appropiate methods

**For services: featherjs provides default methods that should handle the majority of cases**

**Any method that will override will have additional explaniation**

## Character service

We should have some sort of validation on a character, maybe?
For testing right now it's fine.

Then again, they will only be allowed to create character's that we let them create. Not sure how easy it would be for someone to create a user, hit the api to login and then send a create request that way? As well, not sure what benefit that would give them? But still, it' something to consider at least.


Create character - create character for the current user and update the user with the character id and so forth

find character - find character owned by current user with whatever query params

Update character

Delete character

Get character

### Character object/model
* Character
  * Class
  * Level
  * Race
  * Stats
  * Health
  * Actions
  * Spells (if any)
  * Equipment/loot
* the user that owns it

When we update the character, we will send the complete character to the api and then update it. The reason for updating, instead of patching, is that for a character we don't want to carry over anything.
For example,

If a character loses an item, when we update that character to remove that item, that shouldn't be carried over

I can't think of any cases where we would want to patch instead of update for a character but if there is, we can handle that pretty easily

## User service
Create user - create a user

Delete user

Update user - Update the user

Get user

**Character Creation**
This will be the same as creating a party

1. Call an update on the user with the character info and from the user.class call character.create and then link the id to the user

^ this way, we can more easily link the character id to the user

## Party service

**We will have to handle the case of the DM, who will not have one specific character**

Create party - create a party for the current user. Pass in name and generate link using shortid. Link will be added to the database

Delete party

Edit party

Get party

find party - find party owned by or containing current user with whatever query params

Permission levels for party:

Admin - the creator of the party: Normally a DM

Member - A member of the party - There may be instances that a party member can see certain info. That may be decided by the DM. Or that member will have a note section for info that they learn

What we should do...is when a user tries to get a character...If they own it they can see it completely
If they are a member of a party containing the character, they can see some
If they are an admin of a party containing the character, they can see it completely
If they have a share link, they can see some/completely, depending on the settings

In the character service, when the get method is called we first fetch the character that is being requested, then we deal with whether or not the current user can see it or not. All of our endpoints will be locked down and will only be accessible by a signed-in user. 


I feel like, a party specific endpoint would be making uneccesary work. Also, I'm not completely sure how to do something like `party/{id}/characters` or
`party/{id}/characters/{id}` with featherjs.

That is still a possibility but I'm pretty confident the above does roughly the same thing, but without the extra work required. 


## Concerning Business Rule Number 3

> 3. A character that is in a party is independent from other characters (This means that any health, stat, or equipment changes only applies to that 
> character in that party) (This may be harder than I thought) (Related to a party specific endpoint for characters?) 

Like I note above, this may be more dificult than I thought at first. The reason for this is that, I'm not completely sure about the most efficient way to do this. The easiest way would be to simply clone the character and make updates to that character. But If you have, say, 3 parties using the same character, that's 3 copies. Then you multiple that by however many users are doing this in total and you can see that this number would quickly grow out of hand. Now, maybe I'm over thinking this and maybe this isn't even neccesary. Not sure. I would have to do some research into the best way to do this. 

The party could contain all of the info of all of the characters like so:
            [{
                user: 'whatever',
                character: {
                    complete info
                }
            }]
But I feel like that is just running into the same problem as making a copy. Since that is essentially a copy. And it would be stored in the database.


Maybe we could use channels or events somehow? If each party has a record of events, we could simply grab the character and then apply all of those events. But again, Think of the countless number of events that a DND campaign can have. 

something like

/party/character/{id} and then depending on the permission level, return different info

The problem is though, certain party members could know something others don't. But this will be covered by the concept of notes.

**Notes may also not be implemented in this iteration**

Notes would be party specific and user specific

So a member of a party can have notes.

notes would be added to the party members info. like so

        {
          members: [{
            id: 'whatever',
            permission: 'whatever',
            character: 'whatever',
            notes: 'some notes'
          }]
        }
 Notes could be time based, as well. Like a journal entry. But again, That will most likely be out of scope in this iteration. Most likely, we wouldn't store it just as plain text though. We would do some string escapes and maybe have an array of notes containing a timestamp and the actual note. We could also enforce a character limit on notes as well
 
# Business Rules/Logic

1. A character is a created DND character sheet
2. A character can be in multiple parties
3. A character that is in a party is independent from other characters (This means that any health, stat, or equipment changes only applies to that character in that party) (This may be hardere than I thought) (Related to a party specific endpoint for characters?) 
4. A user can have multiple characters in multiple parties
5. A user can create parties
6. A user can delete characters
7. A user can update characters
8. A user can share characters
9. A user can copy a character

# Channels
See https://docs.feathersjs.com/api/channels.html#example

We should make it so that only party members or the current user can see updates to a characters stats, etc

other than that, we won't use this for much.
## Thoughts

Does a character have to be in a party in order to track stats?
How easy should it be to create a character and just track stats? We don't want a user to have to go through a bunch of hoops in order to do so.

More thoughts to come....

## The reason behind the character/party creation logic 

The reason for designing it this way is that originally, my thought was that we would simply call the create call on character/party. 
And then in order to find the users character we would do a find call on character/party with the userId.

However, in my opinion, this will be inefficent and will not scale well. So now the plan is that, we would create the charcter/party and then add that charcter/party id
to the user like so:

    user: {

      email,

      characters: [{
          name: 'Some name',
          class,
          race,
          level
          id    
      }],

      parties: [{
          name,
          id
      }]

    }

That way, when we want to show all of the users characters/parties, we can simply just get the current user. And when we want to view a specific party/character, we would simply
do a get on character/party with the attached id.

So, the easiest way to do this would be to first create the character/party and then update the user with the resulting id and name and any other relevant info

This would actually be a good fit for a hook. We would have a hook for the character and party create that would create the character/party and pass it down to the user. 
https://docs.feathersjs.com/api/hooks.html#quick-example
We could put it in the app hook and check if the service being called is either charcter or party. And if so then create the character/party and pass down the id and relevant info to the user
