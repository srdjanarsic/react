# React

Short notes about react.

## Initialization

Create starter app using `Create React App` boilerplate toolkit.

```
npm install -g create-react-app
create-react-app my-app
cd my-app
npm start
```

## JSX

JSX example  

```jsx
const element = (
  <h1 className="greeting">
    Hello, world!
  </h1>
);
```
converts to 
```js
const element = React.createElement(
  'h1',
  {className: 'greeting'},
  'Hello, world!'
);
```


**JSX example - simple post list (key usage)**  


Note: put a key attribute, it have to be uid. idx can cause bugs.

```jsx
 render() {
        const posts = this.props.posts.map((post) => {
            return (
                <Post 
                    id={post.id} 
                    key={post.id} 
                    post={post} 
                />
            );
        })

        return (
            <ul>{posts}</ul>
        )
    }

```

## props

Remember that `props` are read-only. They should not be modified in any way:

## props.children

`props.children` is available on every component. It contains the content between the opening and closing tags of a component.

```html
<Welcome>Hello world!</Welcome>
```

The string `Hello world!` is available in props.children in the Welcome component


```jsx
function Welcome(props) {
  return <p>{props.children}</p>;
}
```

For components defined as class

```jsx
class Welcome extends React.Component {
  render() {
    return <p>{this.props.children}</p>;
  }
}
```

## state

State is a usually POJO and it's managed by component itself.

Only place you can directly write to this.state should be the components constructor

The most important difference between `state` and `props` is that `props` are passed from a parent component, but state is managed by the component itself. A component cannot change its `props`, but it can change its `state`. To do so, it must call `this.setState()`. Only components defined as classes can have state.

Consider this situation

```js
// assuming this.state = { counter: 0 };
this.setState({ counter: this.state.counter + 1});
this.setState({ counter: this.state.counter + 1});

// this.state.counter WILL BE 1 (not 2 like we would expect)!!!
```

Avoid this by using callback function

```js
this.setState((prevState, props) => {
  return {counter: prevState.counter + props.step};
});
```

## Conditional rendering

```jsx
{unreadMessages.length > 0 && ....
```

```jsx
render(){

    let button = null;
    if(this.props.isLoggedIn){ 
        button = <LogoutButton onClick={this.handleLogoutClick} />
    } else{ 
        button = <LoginButton onClick={this.handleLoginClick} />
    }

    return (
      <div>
        {button}
      </div>
    );

}
```

## Event handling

```js
export default class TestComp extends Component {

  constructor(params) {
    super(params);
    this.state = { toggleOn: true };

    // This binding is necessary to make `this` work in the callback
    this.handleClick = this.handleClick.bind(this);
  }

  handleClick(e) {
    this.setState((prevState, props) => ({
      toggleOn: !prevState.toggleOn
    }));
  }

  render() {
    return (
      <div>
        <button onClick={this.handleClick}>{this.state.toggleOn ? "ON" : "OFF"}</button>
        <button onClick={this.deleteClick.bind(this, "Delete pressed")}>Delete</button>
      </div>
    );
  }
}
```

Arrow fn style

```jsx
<button onClick={() => this.handleClick(id)} />
```

Passing params using data attribute

```jsx
handleClick(e) {
  this.setState({
    justClicked: e.target.dataset.letter
  });
}

  ...


<li key={letter} data-letter={letter} onClick={this.handleClick}>
    {letter}
</li>
```

## Lifting state up

Execute method on parent using props

```js
handleChange(e) {
  this.props.onTemperatureChange(e.target.value);
}
```

## Choosing the Type at Runtime

```jsx
const components = {
  photo: PhotoStory,
  video: VideoStory
};

function Story(props) {
  // Correct! JSX type can be a capitalized variable.
  const SpecificStory = components[props.storyType];
  return <SpecificStory story={props.story} />;
}
```

## Spread attributes

These two are equivalent

```jsx
function App1() {
  return <Greeting firstName="Ben" lastName="Hector" />;
}

function App2() {
  const props = {firstName: 'Ben', lastName: 'Hector'};
  return <Greeting {...props} />;
}
```

## Functions as children

```js
// Calls the children callback numTimes to produce a repeated component
function Repeat(props) {
  let items = [];
  for (let i = 0; i < props.numTimes; i++) {
    items.push(props.children(i));
  }
  return <div>{items}</div>;
}

function ListOfTenThings() {
  return (
    <Repeat numTimes={10}>
      {(index) => <div key={index}>This is item {index} in the list</div>}
    </Repeat>
  );
}
```

## Exposing DOM reference using ref attr

```js
function CustomTextInput(props) {
  // textInput must be declared here so the ref callback can refer to it
  let textInput = null;

  function handleClick() {
    textInput.focus();
  }

  return (
    <div>
      <input
        ref={(input) => { textInput = input; }} />
....
```

## Exposing DOM Refs to Parent Components

```js
function CustomTextInput(props) {
  return (
    <div>
      <input ref={props.inputRef} />
    </div>
  );
}

class Parent extends React.Component {
  render() {
    return (
      <CustomTextInput
        inputRef={el => this.inputElement = el}
      />
    );
  }
}
```

## Uncontrolled component and default values

`<input type="checkbox">` and `<input type="radio">` supports `defaultChecked`  
`<select>` and `<textarea>` supports `defaultValue`. 

## Manual render control

Sometimes we can manually decide should component render.

```js
shouldComponentUpdate(nextProps, nextState) {
  return true;
}
```

## Fragments

Use fragments to avoid extra element.  
React.Fragment won't be added to html.

```jsx
class Columns extends React.Component {
  render() {
    return (
      <React.Fragment>
        <td>Hello</td>
        <td>World</td>
      </React.Fragment>
    );
  }
}
```

Shorthand

```
<>
  <td>Hello</td>
  <td>World</td>
</>
```

## Portal

Render component in outside DOM node. Node outside of parent component hierarchy.

`Modal` component

```js
render() {
  return ReactDOM.createPortal(
    this.props.children,
    this.el
  );
}
```

`Parent` component

```jsx
<Modal>
    <Child />
</Modal>
```

## Error boundaries

A class component becomes an error boundary if it defines a new life-cycle method called `componentDidCatch(error, info)`

```jsx
class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false };
  }

  componentDidCatch(error, info) {
    // Display fallback UI
    this.setState({ hasError: true });
    // You can also log the error to an error reporting service
    logErrorToMyService(error, info);
  }

  render() {
    if (this.state.hasError) {
      // You can render any custom fallback UI
      return <h1>Something went wrong.</h1>;
    }
    return this.props.children;
  }
}
```

```
<ErrorBoundary>
  <MyWidget />
</ErrorBoundary>
```

## High Order Components

Instead of mixin create high order function that returns new component.

```jsx
const CommentListWithSubscription = withSubscription(
  CommentList,
  (DataSource) => DataSource.getComments()
);

const BlogPostWithSubscription = withSubscription(
  BlogPost,
  (DataSource, props) => DataSource.getBlogPost(props.id)
);
```

function `withSubscription()` 

```jsx
// This function takes a component...
function withSubscription(WrappedComponent, selectData) {
  // ...and returns another component...
  return class extends React.Component {
    constructor(props) {
      super(props);
      this.handleChange = this.handleChange.bind(this);
      this.state = {
        data: selectData(DataSource, props)
      };
    }

    componentDidMount() {
      // ... that takes care of the subscription...
      DataSource.addChangeListener(this.handleChange);
    }

    componentWillUnmount() {
      DataSource.removeChangeListener(this.handleChange);
    }

    handleChange() {
      this.setState({
        data: selectData(DataSource, this.props)
      });
    }

    render() {
      // ... and renders the wrapped component with the fresh data!
      // Notice that we pass through any additional props
      return <WrappedComponent data={this.state.data} {...this.props} />;
    }
  };
}
```

 Containers manage things like subscriptions and state, and pass props to components that handle things like rendering UI. HOC-s use containers as part of their implementation. You can think of HOC-s as parameterized container component definitions.

## Accessibility

In JSX `for` is written as `htmlFor`   

```html
<label htmlFor="namedInput">Name</label>
<input id="namedInput" type="text" name="name"/>
```

## Comments

```jsx
<div>
  {/* Comment goes here */}
  Hello, {name}!
</div>
```

## Plugins

* [create-react-app](https://github.com/facebook/create-react-app) - react boilerplate creator app (easy starter boilerplate)
* [react-transition-group](https://github.com/reactjs/react-transition-group) - transitions betwen components (used in combination with router)

**UI**

* [react-bootstrap](https://github.com/react-bootstrap/react-bootstrap) - bootstrap - https://react-bootstrap.github.io/
* [react-bootstrap](https://github.com/react-bootstrap/react-bootstrap) - material - http://www.material-ui.com/#/
* [react-virtualized](https://bvaughn.github.io/react-virtualized/#/components/List) - "windowing" technique for long lists, grids, etc.

**UI plugins**
* [react-dnd](https://github.com/react-dnd/react-dnd) - DnD
* [react-waypoint](http://brigade.github.io/react-waypoint/) - react waypoint

**Duck redux proposal**
* [ducks-modular-redux](https://github.com/erikras/ducks-modular-redux) - ducks redux proposal
* [redux-duck](https://github.com/PlatziDev/redux-duck) - CLI for creating duck redux boilerplate 
 
**Router and Lazy load**

* [react-loadable](https://github.com/jamiebuilds/react-loadable) loadable
* [react-router](https://reacttraining.com/react-router/) - react router

**HTTP Request**

* [axios](https://github.com/axios/axios) - axios

**Transitions**

* [react-transition-group](https://reactcommunity.org/react-transition-group) - react-transition-group
* [react-motion](https://github.com/chenglou/react-motion) - react-motion