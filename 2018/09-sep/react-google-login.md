# Problem 

Add Google Login/Authentication to a `React` application.

# Solution

The first step is to create a basic React application that will
serve as the bare-bones skeleton for our application:

```sh
$ create-react-app
$ npm run start
```

Now we have an application that can be launched at http://localhost:3000.
Launch your browser and test that the default react application is available.

## Setting up Google Login

We will be using the `react-google-login` library that provides a convenience
component to integrate with Google authentication.

```sh
$ npm install react-google-login
```

## Adding up UI entry points

Now, in your `App.js` file import the following additional components so that
we can leverage them in our code:

```js
import { GoogleLogin } from 'react-google-login';
import { GoogleLogout } from 'react-google-login';
```

Say, we will store the user-profile in the component state in a variable called
`profile`, thus, we add:

```js
state = {
    profile : null
}
```

Next, we will add 2 buttons in our view that shall serve as the `sign-in` and
`sign-out` entry points in our application.

```jsx
<GoogleLogin
    clientId="some-clientId.apps.googleusercontent.com"
    buttonText="Sign-in with Google"
    className='btn btn-success'
    onSuccess={ this.onGoogleSuccess }
    onFailure={ this.onGoogleFailure } />
```

In the above code, you will need to provide your own `Google ClientID` in the
`clientId` parameter to the component. Refer below on how to generate one.

I have used `className='btn btn-success'` as an attribute on 
the component. The `btn btn-success` classes come from `Bootstrap 4` framework
that I usually use to skin my applications. You can change them to whatever that
suits your CSS framework.

I also have added two event handlers, `onGoogleSuccess` and `onGoogleFailure` to
be invoked when google authentication succeeds or fails.

The event handlers can be added as:

```jsx
onGoogleSuccess = (userObject) => {
    console.log('google object has to offer these values: ', userObject)
    this.setState( { profile : userObject });
}
```

In case authentication is successful, a `GoogleAuth` object is returned 
which provides access to all of the `GoogleUser` methods listed here: https://developers.google.com/identity/sign-in/web/reference#users

You can also access the returned values via the following properties on 
the returned object.

 Attribute  | Type      | Details
------------|-----------|--------------------------
googleId    | string    | Google user ID
tokenObj    | object    | Token details object
profileObj  | object    | Profile details object
tokenId     | string    | Token Id
accessToken | string    | Access Token

Over, to the authentication failure handler:

```jsx
onGoogleFailure = (error) => {
    console.log('unable to connect with google due to error: ', error);
}
```

In case Google authentication fails, the error callback receives an `error` object
with the following properties:

Attribute   | Type    | Details
------------|---------|--------------------------
error       | string  | Error code
details     | string  | Detailed error description

To add the Sign-out button, we just need to remove the Google profile from the
component state as:

```jsx
onGoogleSignOut = () => {
    this.setState( { profile : null });
}

<GoogleLogout buttonText='Sign Out'
    type='button'
    onLogoutSuccess={ this.onGoogleSignOut } 
    className='btn btn-info dropdown-item' /> 
```

And voila. If we now deploy and test our application, we shall see that when we
click the `Sign in with Google` button and authorize the incoming Google message, we
receive the user profile in the `onGoogleSuccess` handler.

## Generating Google Client ID

* Make sure you are signed-in to your Google account. 
* [Navigate to Google Developer Site](https://developers.google.com/identity/sign-in/web/sign-in) 
* Click on button `Configure A Project`
* Click on `Create a new project +` in the dropdown window
* Enter your project name here, click Next
* Enter the name for your application that you may want to see on console, click Next
* Specify the scope to use for this application
  * For example, use `Web browser` for client-side login usage (browser based authentication)
* Specify the URL where our application will be running. Add http://localhost:3000 and hit the Create button
* `ClientId` will be shown on the screen - copy it
* Use the value obtained above in the `clientId` parameter in `GoogleLogin` component usage above

There needs to be two method defined on the react component that specify set of actions to be taken in case Google login is successful or in case Google login is failed.


Additional documentation can be found at:

* [GoogleAuth.then(onInit, onError)](https://developers.google.com/identity/sign-in/web/reference#googleauththenoninit-onerror)
* [GoogleAuth.signIn(options)](https://developers.google.com/identity/sign-in/web/reference#googleauthsigninoptions)
* [GoogleAuth.grantOfflineAccess(options)](https://developers.google.com/identity/sign-in/web/reference#googleauthgrantofflineaccessoptions)

## Summary

Using `react-google-login` library makes it super-easy to add Google authentication
to our website.
