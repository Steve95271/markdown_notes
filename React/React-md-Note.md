# React data flow

 

React data flow is from top to down.

- parent -> childs



useState() method itself is a hook.



When updating "useState()" variable for example:

```javascript
const [step, setStep] = useState(1);
```

We should NEVER directly pass variable to update it like this:

```javascript
function handlePrevious() {
    setStep(step > 1 ? step - 1 : step);
  }

function handleNext() {
    setStep(step < 3 ? step + 1 : step);
  }
```

Correct way, pass callback function:

```javascript
  function handlePrevious() {
    // This is wrong way
    // setStep(step > 1 ? step - 1 : step)

    // Correct way: using call back function
    if (step > 1) {
      setStep((step) => step - 1);
    }
  }

  function handleNext() {
    // This is wrong way
    // setStep(step < 3 ? step + 1 : step);

    // Correct way: using call back function
    setStep((step) => {
      return step < 3 ? step + 1 : step;
    });
  }
```



State is seprate for each components.

Each component is managed by it's own state.



# State Management

### Local State

- State is only be using by one or few components
- Only that component and child components have access to it



### Global State

- Many components might need
- Shared state that is accessible to every component in the entire application



### STATE: When and Where

#### WHEN

Will data change at some point?

- yes?
  - Can be computed from existing state/props?
    - yes?
      - Derive state
    - no?
      - Should it re-render component?
        - yes?
          - Place a new piece of state in component
        - no?
          - Ref(useRef)
- no?
  - Regular const variable



#### WHERE

Only used by this component?

- yes?
  - Leave in component
- no?
  - Also used by a child component?
    - yes?
      - Pass to child via props
    - no?
      - Used by one of a few sibling components?
        - yes?
          - Lift state up to first common parent
        - no?
          - ...