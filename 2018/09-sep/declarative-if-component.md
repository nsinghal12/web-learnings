# Task

How to build a declarative `if` component.

# Solution

When learning React development, writing `if-then` conditions
using JSX syntax was not very convenient. I was reading and
figuring out a way to have the usage show something like:

```jsx
export default class Usage extends React.Component {
    
    render() {
        return <IfClause condition={ someBooleanCondition }>
        </IfClause>;
    }

}
```

Thus, I happened to create the following component:

```jsx
export default class IfClause extends React.Component {
    
    render() {
        if(this.props.condition) {
            // condition is met, return all children
            return this.props.children;
        }

        // when condition is false, there is no need to return anything
        return null;
    }
}
```

Hope this helps you.
