### What are the features of React?

- Uses JSX syntax, a syntax extension of JS that allows developers to write HTML in their JS code.
- It uses Virtual DOM instead of Real DOM considering that Real DOM manipulations are expensive.
- Supports server-side rendering which is useful for Search Engine Optimizations(SEO).
- Follows Unidirectional or one-way data flow or data binding.
- Uses reusable/composable UI components to develop the view.

### What is AJAX?

Asynchronous JavaScript and XML (Ajax, or AJAX) is a web development technique in which a web app fetches content from the server by making asynchronous HTTP requests, and uses the new content to update the relevant parts of the page without requiring a full page load and reloading.

Ajax can be used to create single-page apps, in which the entire web app consists of a single document, which uses Ajax to update its content as needed.

It is implemented with fetch API .

**Advantages:** performance gains, a more dynamic experience

**disadvantages:** SEO

### How does react work?

**Step 1: Trigger**

1. initial render (createRoot)
2. state changes in parent can triger re-render of all of its children

somethings to note during trigger:

**The state updates are not immediate, this because the setCount is asyncronous and it batches update**

```
function update() {
	setIsTitleExpanded(!isTitleExpanded)
	setIsTextExpanded(!isTextExpanded)
	console.log(isTitleExpanded)
	console.log(isTextExpanded)
}

// logged isTextExpanded value will be the same
```

**react only re-renders only after all the state in a component and its children are updated**

```
function update() {
	setIsTitleExpanded(!isTitleExpanded)
	setIsTextExpanded(!isTextExpanded)
}

function App(){
	console.log("sdsd")
	....
}

// "sdsd" is logged only once despite multiple state update
```

**Step 2: Render**

It will figure out which components have changed in the virtual DOM by comparing with the real DOM.

**Step 3: Commit**

bundle all changes and actually write to Real DOM

### Why Virtual DOM and Why not directly write to the DOM?

it expensive to write to the DOM compared to writing to a lightweight representation of DOM with JS objects (Virtual DOM) is used

The Virtual DOM (VDOM) is an in-memory representation of Real DOM. The representation of a UI is kept in memory and synced with the "real" DOM

### States

states are not persisted on refresh

states for objects are refrence so changing the value of reference don't update until re-render

### React Scanning and Keys

react scans a trees side by side

if there is difference in even single element , it'll rebuild everything that's nested in it

```
old tree

<div>
      <h2>Rerender Example</h2>
      <p><h2>Rerender Example</h2></p>
      <button onClick={handleRerender}>Rerender</button>
 </div>

 new tree

<div>
      <h2>Rerender Example</h2>
      <article>   // changed rebuold everything inside
	  	<h1>Rerender Example</h1>
		<button onClick={handleRerender}>Rerender</button>
	  </article>
      <button onClick={handleRerender}>Rerender</button>
 </div>
```

don't use array index for keys if the elements in the array could change. they are used to identify if an element has changed or not.

```
['apple', 'banana', 'orange']

remove 'apple'

they keys of "banana" and "orange" have changed as well even tho they didn't change
```

### What is root div?

All react pages gets injected into the div with id root by the javascript running the react library, this is called client side render

### What is JSX?

JSX stands for JavaScript XML and it is an XML-like syntax extension to ECMAScript. Basically it just provides the syntactic sugar for the React.createElement(type, props, ...children) function, giving us expressiveness of JavaScript along with HTML like template syntax.

In the example below, the text inside `<h1>` tag is returned as JavaScript function to the render function.

```javascript
export default function App() {
  return <h1 className="greeting">{"Hello, this is a JSX Code!"}</h1>;
}
```

If you don't use JSX syntax then the respective JavaScript code should be written as below,

```javascript
import { createElement } from "react";

export default function App() {
  return createElement(
    "h1",
    { className: "greeting" },
    "Hello, this is a JSX Code!"
  );
}
```

### Explain the purpose of {} in react.

`{}` typically represents an expression that is used to embed JavaScript expressions or variables within JSX (JavaScript XML)

```
style ={{width: 100%}}
key={id}
```

### What casing should you use for inline style and attribute for html?

camel casing should be used instead of seperating words with `-`

### How to create html elements from values in an array?

```Javascript
{ messages.map((message, index) => (
                  <p key={index} className="mb-2" style={userColorStyle}>
                    {message.name + ' ' + message.message}
                  </p>
  			   )
)}
```

### What is key?

`key` is the attribute used to identify html elements render from an array.

### Conditionally render html elements ?

```Javascript
{admin && <h1>admin</h1>}
```

### When to use () in React?

Parenthesis are used in an arrow function to return an object.

```
() => ({ name: 'Amanda' })  // Shorthand to return an object
```

Parenthesis are used to group multiline of codes on JavaScript return statement so to prevent semicolon inserted automatically in the wrong place.

Ney, this doesn’t compile.

```javascript
class StarsComponent {
  constructor(size) {
    this.size = size;
  }

  render() {
    return <div>  // <--JavaScript engine inserts semicolon here
           *
           </div>
  }
}
```

Yay, this works.

```javascript
class StarsComponent {
  constructor(size) {
    this.size = size;
  }

  render() {
    return <div>*</div>; // <--JavaScript engine inserts semicolon here
  }
}
```

### File extension js vs jsx?

no difference

### useContext

wrapping component with useContext provider will make the store available for all component down this tree. It helps us avoid prop drilling

```
import PokemonInfo from "./components/PokemonInfo";
import PokemonContext from "./PokemonContext";
import PokemonFilter from "./components/PokemonFilter";
import PokemonTable from "./components/PokemonTable";

function App() {
  return (
    <PokemonContext.Provider
      value={{
        filter,
        pokemon,
        filterSet,
        pokemonSet,
        selectedPokemon,
        selectedPokemonSet,
      }}
    >
          <div>
            <PokemonFilter />
            <PokemonTable />
          </div>
          <PokemonInfo />
    </PokemonContext.Provider>
  );
}
```

```
const PokemonFilter = () => {
  const { filter, filterSet } = useContext(PokemonContext);

  return (
    <Input
      type="text"
      value={filter}
      onChange={(evt) => filterSet(evt.target.value)}
    />
  );
};
```

```
const PokemonContext = React.createContext({});
```

### UseReducer

it can help with combining many use state into a single state manager kinda like redux but locally for a component. it helps us avoid having many useState and allows us to mutate the state centrally depending on the type of action.

```
import PokemonInfo from "./components/PokemonInfo";
import PokemonContext from "./PokemonContext";
import PokemonFilter from "./components/PokemonFilter";
import PokemonTable from "./components/PokemonTable";


const stateReducer = (state, { type, payload }) => {
  switch (type) {
    case "SET_FILTER":
      return {
        ...state,
        filter: payload,
      };
    case "SET_POKEMON":
      return {
        ...state,
        pokemon: payload,
      };
    case "SET_SELECTED_POKEMON":
      return {
        ...state,
        selectedPokemon: payload, // leave every other property as it is except for this one
      };
    default:
      throw new Error();
  }
};

function App() {
  const [state, dispatch] = React.useReducer(stateReducer, {
    filter: "",
    pokemon: [],
    selectedPokemon: null,
  });

  React.useEffect(() => {
    fetch("/starting-react/pokemon.json")
      .then((resp) => resp.json())
      .then((payload) =>
        dispatch({
          type: "SET_POKEMON",
          payload,
        })
      );
  }, []);

  if (!state.pokemon) {
    return <div>Loading data</div>;
  }

  return (
    <PokemonContext.Provider
      value={{
        state,
        dispatch,
      }}
    >
      <PageContainer>
        <CssBaseline />
        <Title>Pokemon Search</Title>
        <TwoColumnLayout>
          <div>
            <PokemonFilter />
            <PokemonTable />
          </div>
          <PokemonInfo />
        </TwoColumnLayout>
      </PageContainer>
    </PokemonContext.Provider>
  );
}

export default App;
```

### UseMemo

It can be useful for optimizing performance by memoizing the result of expensive calculations. In the context of a memo application, we might use useMemo to memoize the list of memos, preventing unnecessary re-renders when the state of the memos changes.

calculate if dependency array change

```
const [numbers] = useState([10, 20, 30]);
const total = useMemo(
() => numbers.reduce((acc, number) => acc + number, 0),
[numbers]);

// the numbers don't change
```

bad use

```
const [count1, setCount1] = useState(0);
const [count2, setCount2] = useState(0);

const total = useMemo(
() => count1 + count2,
[count1, count2]);

```

### UseCallback

When a parent component renders, any functions defined within that component are recreated on each render. This can lead to unnecessary re-renders of child components if they rely on those functions as props, even if the function logic hasn't changed. useCallback helps to optimize this scenario by memoizing the function, returning the same reference to the function if its dependencies (specified in the dependency array) haven't changed.

```
function App() {
  const sortFunc = useCallback((a, b) => a.localeCompare(b) * -1, []);
}
```

### UseEffect

creating a interval in the component without useEffect will create infinite intervals without stop, putting them in useEffect will create a single interval

```
const Stopwatch = () => {
  const [time, setTime] = useState(0);

  useEffect(() => {
    const interval = setInterval(() => {
      setTime((t) => {
        console.log(t);
        return t + 1;
      });
    }, 1000);
    return () => clearInterval(interval);
  }, []);

  return <div>Time: {time}</div>;
};

```

### UseRef

useRef is a React Hook that provides a way to create mutable object called a ref object. The useRef hook is commonly used for accessing or interacting with a DOM element, but it can also be used to persist values across renders without causing re-renders.

```
function App() {
  const inputRef = useRef(null);

  useEffect(() => {
    inputRef.current.focus();
  }, []);

  const idRef = useRef(1);
  const [names, setNames] = useState([
    { id: idRef.current++, name: "John" },
    { id: idRef.current++, name: "Jane" },
  ]);

  const onAddName = () => {
    setNames([
      ...names,
      {
        id: idRef.current++,
        name: inputRef.current.value,
      },
    ]);
    inputRef.current.value = "";
  };

  return (
    <div>
      <input type="text" ref={inputRef} />
    </div>
  );
}
```

### UseState

the `setState` function in React is asynchronous, and it does not guarantee that the state is immediately updated after the function it is in completes execution. React batches state updates for performance reasons, and the actual update may be applied at a later time.

```
function update() {
	setCount(count + 1)
	setCount(count + 1)
	setCount(count + 1)
	setCount(count + 1)
}

// the value remain the same in this example
```

solution:

```
function update() {
	setCount((preCount) => count + 1)
	setCount((preCount) => count + 1)
	setCount((preCount) => count + 1)
	setCount((preCount) => count + 1)
}

// the value remain the same in this example
```

React ensures that the function receives the latest state at the time it is executed. However, it doesn't immediately apply the state update. Instead, React queues the state update, and the component will be re-rendered with the updated state at some point in the future.

### What is the difference between Element and Component?

An Element is a plain object describing what you want to appear on the screen in terms of the DOM nodes or other components. Once an element is created, it cannot be mutated.

The JavaScript representation(Without JSX) of React Element would be as follows:

```
const element = React.createElement("div", { id: "login-btn" }, "Login");

// result: <div id="login-btn">Login</div>
```

The above React.createElement() function returns an object as below:

```
{
  type: 'div',
  props: {
    children: 'Login',
    id: 'login-btn'
  }
}
```

Finally, this element renders to the DOM using ReactDOM.render().

Whereas a component can be declared in several different ways. It can be a class with a render() method or it can be defined as a function. In either case, it takes props as an input, and returns a JSX tree as the output:

```
const Button = ({ handleLogin }) => (
  <div id={"login-btn"} onClick={handleLogin}>
    Login
  </div>
);
```

Then JSX gets transpiled to a React.createElement() function tree:

```
const Button = ({ handleLogin }) =>
  React.createElement(
    "div",
    { id: "login-btn", onClick: handleLogin },
    "Login"
  );
```

### What is the use of refs?

The ref is used to return a reference to the element. They should be avoided in most cases, however, they can be useful when you need a direct access to the DOM element or an instance of a component.

### What are fragments?

It's a common pattern or practice in React for a component to return multiple elements. Fragments let you group a list of children without adding extra nodes to the DOM. You need to use either or a shorter syntax having empty tag (<></>).

### Why fragments are better than container divs?

Below are the list of reasons to prefer fragments over container DOM elements,

- Fragments are a bit faster and use less memory by not creating an extra DOM node. This only has a real benefit on very large and deep trees.
- Some CSS mechanisms like Flexbox and CSS Grid have a special parent-child relationships, and adding divs in the middle makes it hard to keep the desired layout.
- The DOM Inspector is less cluttered.
