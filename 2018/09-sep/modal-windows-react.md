# Task

Centrally display modal windows in React

# Solution

Displaying modal windows is essential to any web application
today. They are used from authenticaion to payments to alerts
and more. React provides a way to display modal windows using
[React Portals](https://reactjs.org/docs/portals.html).

However, this requires that the modal window component be
imported at the place of action, and the actual DOM node where
modals are to be displayed are hard-coded in action component.

Or, we pass in events from the action component to the common
parent using events, and then passing the same data from this
common parent to the actual modal container. Both these options
are inconvenient as a developer.

Hence, I came across another way to build modals centrally. I
delegated the details passing mechanism to document events. Let's
assume we want to display the authentication modal from `Header`
component. `Application` component includes the `Header` component,
and also the `ModalContainer` component.

To achieve the same, let's add a new `document` event called `show-modal`
that will be raised to let us know which modal to display. The
same modal component to be displayed shall be set in the `state`
of the `Application` component.

```jsx
export default class Application extends React.Component {

    state = {
        // no modal is to be displayed at load of application
        currentModal : null
    }

    componentDidMount() {
        // create the event listener for application
        document.addEventListener('show-modal', this.handleModalDisplay);
    }

    componentWillUnmount() {
        // remove to prevent memory leaks
        document.removeEventListener('show-modal', this.handleModalDisplay);
    }
    
    /**
     * This method is responsible for displaying modal
     * window based on the `show-modal` event that has
     * been raised
     */
    handleModalDisplay = (event) => {
        // set the modal name to the one sent by event
        this.setState( { currentModal : event.modalName } );
    }

    render() {
        return <Group>
            <Header />

            <ModalContainer modal={ this.state.currentModal } />
        </Group>;
    }
}
```

Now let's see how to use this event inside our own `Header`
component to show the modal windows.

```jsx
export default class Header extends React.Component {
    
    showSignInModal = () => {
        // whenever we need to show a modal window
        // we just raise the right event with the
        // modal name
        document.dispatchEvent(new Event('show-modal', { modalName : 'signInModal' } ) );
    }

    render() {
        return <Group>
            <ul class='navbar'>
                <li class='nav-item'>
                    <a class='btn btn-primary' href='#' onClick={ this.showSignInModal }>Sign-in</a>
                </li>
            </ul>
        </Group>;
    }
}
```

The modal container can be plainly simple to display the right
window depending on the modal name:

```jsx
export default class ModalContainer extends React.Component {
    
    render() {
        return <Group>
            <If condition={ 'signInModal' === this.props.modal }>
                <SignInModal />
            </If>
        </Group>
    }
}
```

This article uses the [IfClause](https://github.com/nsinghal12/web-learnings/blob/master/2018/09-sep/declarative-if-component.md) and [Group](https://github.com/nsinghal12/web-learnings/blob/master/2018/09-sep/declarative-group-component.md) components that
have been explained in other articles in this repository.
