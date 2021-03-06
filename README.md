App Engine application for the Udacity training course.

## Products
- [App Engine][1]

## Language
- [Python][2]

## APIs
- [Google Cloud Endpoints][3]

## Setup Instructions
1. Update the value of `application` in `app.yaml` to the app ID you
   have registered in the App Engine admin console and would like to use to host
   your instance of this sample.
1. Update the values at the top of `settings.py` to
   reflect the respective client IDs you have registered in the
   [Developer Console][4].
1. Update the value of CLIENT_ID in `static/js/app.js` to the Web client ID
1. (Optional) Mark the configuration files as unchanged as follows:
   `$ git update-index --assume-unchanged app.yaml settings.py static/js/app.js`
1. Run the app with the devserver using `dev_appserver.py DIR`, and ensure it's running by visiting your local server's address (by default [localhost:8080][5].)
1. (Optional) Generate your client library(ies) with [the endpoints tool][6].
1. Deploy your application.


[1]: https://developers.google.com/appengine
[2]: http://python.org
[3]: https://developers.google.com/appengine/docs/python/endpoints/
[4]: https://console.developers.google.com/
[5]: https://localhost:8080/
[6]: https://developers.google.com/appengine/docs/python/endpoints/endpoints_tool



## Task 1

### Design Choices

Session objects are children of Conference objects since Sessions are specific to each Conference.

    class Session(ndb.Model):
        name            = ndb.StringProperty(required=True)
        highlights      = ndb.StringProperty(repeated=True)
        speaker         = ndb.StringProperty()
        duration        = ndb.IntegerProperty()
        typeOfSession   = ndb.StringProperty()
        date            = ndb.DateProperty()
        startTime       = ndb.TimeProperty()

name, highlights and typeOfSession are implemented as strings with highlights repeatable to allow  multiple highlights.
I chose  the string type since the property values would be words or names.

speaker is also implemented as a string for simplicity.

The types for date and startTime are self explanatory.

For duration, IntegerProperty was selected since this would be representing a number of minutes (ex 30, 45, 60, 90, etc)


## Task 2

### Session Wishlist

Wishlists are a repeated key property of Profile containing a list of session keys.


- `addSessionToWishlist()`
   Adds a session to the wishlist

- `deleteSessionInWishlist()`
   Removes a session from the wishlist

- `getSessionsInWishlist()`
   returns all the sessions in the wishlist

## Task 3

### Additional Queries

- `getConferenceSessionsByDate`
   returns all sessions within a specified conference by date

- `getConferenceSessionsByHighlight`
   returns all sessions within a specified conference by highlight

### Query Problem

*Solve the following query related problem: Let’s say that you don't like workshops and you don't like sessions after 7 pm. How would you handle a query for all non-workshop sessions before 7 pm? What is the problem for implementing this query? What ways to solve it did you think of?*

The problem is due to inequality filters can only be applied to one property - two properties would require such a filter: startTime and typeOfSession.


To work around this limitation, first get all non-workshop sessions.  Then iterate through the results and create a new list of sessions by adding sessions that are 7PM or earlier. Return the new list.



## Task 4

### Featured Speaker

When a session is added to a conference, a task is called at the endpoint /tasks/set_speaker.  That task in turns invokes _cacheSpeaker.
If the speaker for the newly created session is a speaker for previously created session in the conference, that speaker becomes the new featured speaker.  That speaker and their sessions are added to memcache.

- `getFeaturedSpeaker()`
   returns the featured speaker in memcache
