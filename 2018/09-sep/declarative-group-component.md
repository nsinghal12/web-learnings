# Task

Grouping multiple components in `React.render` without
adding extra `div` or using `React Fragments`

# Solution

When learning React development I learnt that there is no
direct way to return multiple components from the `render`
method without adding either an extra `DIV`. 

So I wrote the following simple component:

```jsx
export default class Group extends React.Component {
    
    render() {
        return this.props.children;
    }
}
```

Using this component is as easy as:

```jsx
export default class Usage extends React.Component {
    
    render() {
        return <Group>
            { /** the needed children **/ }
        </Group>
    }
}
```

Hope this helps.
