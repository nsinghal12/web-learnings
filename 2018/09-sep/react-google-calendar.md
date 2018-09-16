# Task

Use Google Calendar in a React application.

# Solution

When building my [Meeting Minutes](https://github.com/nsinghal12/minutes)
application, I had the need to schedule new meeting events directly 
into a user's Google Calendar. This was required to shoot reminders and 
agenda emails to other participants.

The first step for the integration is to integrate with Google
Login in your React application. I have explained how to achieve
this in [my previous article on Google Login](https://github.com/nsinghal12/react-learnings/blob/master/2018/09-sep/react-google-login.md).

The next step is to enable Google Calendar API in the Google Developer Console.
This allows Google to display to the user, that our application is requiring
access to the user's calendar, when the call to authenticate is sent. We shall
use the [Google Calendar Quickstart Wizard](https://developers.google.com/calendar/quickstart/js)
to enable the API. Make sure you choose the *Browser Quickstart* wizard.

There are two different modes in which the Google Calendar can be integrated:

* Read-only mode
* Read-write

As evident from the naming convention, the `read-only` scope only allows
our application to view current events in the user's calendar. The `read-write`
scope allows us to create, edit, and remove evnets in the Calendar. We will
request the `read-write` scope for our example.

Once we have configured the Google Calendar API access in our Google project,
we now just need to invoke the Google Calendar API when needed (post user-sign-in
event).

Let's add a convenience method to our `Utils.js` file to create a new event
whenever we have the need to schedule one:

The following value-object class `Meeting` can be used to send our
new event information:

```js
export default class MeetingInfo {

  // the title to the display
  title:string;
  
  // location to display
  location:string;
  
  // description - may contain agenda notes
  description:string;
  
  // date-time at which meeting will start
  startDate:Date;
  
  // date-time at which the meeting will end
  endDate:Date;
  
  // email addresses of user's whom we want to add to the meeting
  attendees:string[]

}
```

```js
export default class Utils {

    /**
     * Method to schedule a new meeting in Google Calendar.
     * This method takes a `MeetingInfo` object as an argument.
     *
     * Refer: https://developers.google.com/calendar/create-events
     * for more details
     */
    static createEventInGoogleCalendar(meeting) {
        if(!meeting) {
            return;
        }
        
        const event = {
            'summary': meeting.title,
            'location': meeting.location,
            'description': meeting.description,
            'start': {
              'dateTime': meeting.startDate.toISOString(),
              'timeZone': 'America/Los_Angeles'
            },
            'end': {
              'dateTime': meeting.endDate.toISOString(),
              'timeZone': 'America/Los_Angeles'
            },
            'attendees': [
            ],
            'reminders': {
              'useDefault': false,
              'overrides': [
                {'method': 'email', 'minutes': 24 * 60},
                {'method': 'popup', 'minutes': 10}
              ]
            }
        };

        // let's add all our attendees to the meeting
        for(let index = 0; index < meeting.attendees; index++) {
            event.attendees.push(meeting.attendees[index]);
        }

        // https://developers.google.com/calendar/v3/reference/events/insert
        window.gapi.client.load('calendar', 'v3', function() {
            let request = window.gapi.client.calendar.events.insert({
                'calendarId': 'primary',
                'resource': event
            });

            request.execute(function(event) {
                console.log('Google calendar event created as: ' + event.htmlLink);
            });
        });
    }
}
```

Once created, we get the [Events resource](https://developers.google.com/calendar/v3/reference/events#resource)
object that consists of the `htmlLink` property that can be used to open 
the event in a new Google Calendar tab/window.
