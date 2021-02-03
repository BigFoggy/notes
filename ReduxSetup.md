1. install redux
```
npx create-react-app redux-intro --scripts-version=4.0.1
npm install redux react-redux
```
2. import into index.js. The following steps are all on index.js:
```
import { createStore, combineReducers } from 'redux';
import { Provider } from 'react-redux';
```
3. Add the provider tag:
```
ReactDOM.render(
  <React.StrictMode>
    <Provider store={storeInstance}>
        <App />
    </Provider>
  </React.StrictMode>,
  document.getElementById('root')
);
```
4. add a reducer to index.js, or however many you need. Here is one example with two actions, the return is an integer that is modified:
```
const count = (state = 0, action) => {
    if (action.type === 'INCREASE') {
        return state + 1;
    } else if (action.type === 'DECREASE') {
        return state - 1;
    }
    return state;
};

const elementList = (state = [], action) => {
    return state;
};
```
- Here is another that works with an array. Never use .push, always use a spread operator:
```
const elementList = (state = [], action) => {
    if (action.type === 'ADD_ELEMENT') {
        return [...state, action.payload];
    }
    return state;
};
```
5. Create the store and pass in the reducer to the store
```
const storeInstance = createStore(
    count
);
```
- Combine the two, if needed, with the combineReducers
```
const storeInstance = createStore(
    combineReducers(
        {
            count,
            elementList,
        }
    )
);
```
6. GETTING DATA - Head to the component you need to access the data from. Use  useSelector to acess data from the store. 
```
import { useSelector } from 'react-redux';
```
7. grab the info from the store and create a variable for it. This goes under the function but before the return:
```
const count = useSelector(store => store.count)
```
8. Display the data like you would display state, by using the variable:
```
<p>Count is: {count}</p>
```
9. SETTING DATA - Use useDispatch to cause an action to happen and set data. First import it, along with useSelector as well if needed:
```
import { useSelector, useDispatch } from 'react-redux';
```
10. set the dispatch to a local variable within the function, but before the return:
```
const dispatch = useDispatch();
```
11. add the dispatch to an event, and specify the type (defined in the reducer). You can see here the 
```
<button onClick={() => dispatch({ type: 'INCREASE' })}>Increase</button>
<button onClick={() => dispatch({ type: 'DECREASE' })}>Decrease</button>
```
12. On your form component, add a variable with useState():
```
const [newElement, setNewElement] = useState('');
```
13. tie the input to local state:
```
<input value={newElement} />
```
14. add an onChange event to the input to add the input data to the local state:
```
<input 
    value={newElement} 
    onChange={event => setNewElement(event.target.value)}  
/>
```
15. Add an onSubmit function. This example defines a local function to handle the submit:
```
<form onSubmit={handleSubmit}>
```
- and then the function. 1. Prevent default to prevent the page from refreshing 2. dispatch the data to the store 3. clear the input field
```
const handleSubmit = (event) => {
        event.preventDefault();
        dispatch({
            type: 'ADD_ELEMENT',
            payload: newElement
        });
        setNewElement('');
    };
```
16. DISPLAY THE LIST -  on a component that imports useSelector (step 6) grab data you want from the store and place it into a variable:
```
const elementList = useSelector( store => store.elementList);
```
17. map over the data in the array and for each item display it as a list item. 
```
return (
        <ul>
            {elementList.map((element, i) => (
                <li key = {i} >{element}</li>
            ))}
        </ul>
    );
}
```
18. This could be in it's own componenent. No props needed!
