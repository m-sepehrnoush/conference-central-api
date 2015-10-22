# Conference Organization
An API server using Google App Engine to support a provided organization application that exists on the web as well as a native Android application.
This API supports the following functionality:
	- user authentication
	- user profiles
	- conference information
	- various manners in which to query data

## Quick start
There are several methodes for accessing and testing the API Server:
 * You can access API server's web front-end on [ConferenceCentral](https://udacityconferencecentral.appspot.com)
 * To test the API using *Google API Explorer*:
	- Login to your [Google Developers Console](https://console.developers.google.com)
	- And visit [ConferenceCentral's API Explorer](https://apis-explorer.appspot.com/apis-explorer/?base=https://udacityconferencecentral.appspot.com/_ah/api#p/conference/v1/)
 * To run locally you need the following:
	- Clone the api at [Github](https://github.com/m-sepehrnoush/conference-central-api.git) or simply download the zip file.
	- python2.7
	- [Google App Engine SDK](https://cloud.google.com/appengine/downloads?csw=1)
	- Update the value of `application` in `app.yaml` to the app ID you have registered in the App Engine admin console and would like to use to host your instance of this sample.
	- Update the values at the top of `settings.py` to reflect the respective client IDs you have registered in the [Developer Console](https://console.developers.google.com/)
	- Update the value of CLIENT_ID in `static/js/app.js` to the Web client ID
	- Run the API using **Google App Engine Launcher**

### Task 1:
 * Adding classes for session and session form and the required endpoints
	- getConferenceSessions(webSafeConferenceKey)
		given a conference return all sessions
	- getConferenceSessionsByType(webSafeKey, typeOfSession)
		given a conference, return all sessions of a specified typeOfSession
	- getSessionBySpeaker(speaker)
		given a speaker, return all sessions given by this speaker
	- createSession(SessionForm, webSafeKey)
		open only to organizer of the session
 * **Session** was created as an entity with **Conference** as an *ancestor*. 
	I defined duration as an *IntegerProperty*, date as a *DateProperty*,
	startTime as a *TimeProperty*, and all the other fields as *StringProperty*.
 * Since the only query on *speaker* was on *speaker*'s name and *speaker*s belonged
   to sessions, I decided to add *speaker* as a property of session object.
	
### Task 2:
 * Adding session to whishlist
	- addSessionToWishlist(SessionKey)
	adds the session to the list of sessions each user is interested to attend
 * Listing whishlist items
	- getSessionsInAWishlist()
	query for all the sessions in a conference that the user is interested in
### Task 3:
 * Additional Queries:
	- Added the following endpoints to get a list of sessions based on duration, and
	to get list of sessions of a certain type which are **not** given by the specified speaker.
		- getConferenceSessionsByDuration()
		- getConferenceSessionsOfTypeNotBySpeaker()
	- All the queries were tried locally for Indexing support.
 * Query Problem
	*Let’s say that you don't like workshops and you don't like sessions after 7 pm. How would you handle a query for all non­workshop sessions before 7 pm? What is the problem for implementing this query? What ways to solve it did you think of?*
	Performing the said query would need inequalty filters on *2* properties and according
	to App Engine Docs [inequalty filters are limited to at most one property.](https://cloud.google.com/appengine/docs/python/datastore/queries?csw=1#Python_Restrictions_on_queries) 
	the reason being to avoid having to scan the entire index table.
	So maybe one way is to filter the results based on one property and then loop through the results manually to filter the other property.
	and if it is possible also sort the results based on the other property so we can have a smaller result set to loop through!
	And the other way I can think of is if we could implement the properties in a different way; For instance, we could define a 
	new property which consists of the two other properties(eg workshop7, workshop5, lecture9),
	this way we could query based on one property.

### Task 4:
 * Implemented **getFeaturedSpeaker()** using App Engine's *Task Queue*
