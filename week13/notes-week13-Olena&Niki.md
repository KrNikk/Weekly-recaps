# Hooks
With hooks you can use states and other React features without using class.


```
import React, { useState } from 'react';

function Example() {
  // Declare a new state variable, which we'll call "count"
 const [count, setCount] = useState(0);
  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  );
}
```


**Context APi**
Context provides a way to pass data through the component tree without having to pass props down manually at every level.
Steps (with example) :  
1. Initialize the Context 
(MyContext.js)

```
import React from 'react';

const MyContext = React.createContext();

export default MyContext;
```


2. Create the Provider
(MyProvider.js)


```javascript
import MyContext from './MyContext';

class MyProvider extends Component {
    state = {
        cars: {
            car001: { name: 'Honda', price: 100 },
            car002: { name: 'BMW', price: 150 },
            car003: { name: 'Mercedes', price: 200 }
        }
    };

    render() {
        return (
            <MyContext.Provider
                value={{
                    cars: this.state.cars,
                    incrementPrice: selectedID => {
                        const cars = Object.assign({}, this.state.cars);
                        cars[selectedID].price = cars[selectedID].price + 1;
                        this.setState({
                            cars
                        });
                    },
                    decrementPrice: selectedID => {
                        const cars = Object.assign({}, this.state.cars);
                        cars[selectedID].price = cars[selectedID].price - 1;
                        this.setState({
                            cars
                        });
                    }
                }}
            >
                {this.props.children}
            </MyContext.Provider>
        );
    }
}
```

3. Create the Consumer
(Cars.js)
```
const Cars = () => (
    <MyContext.Consumer>
        {context => (
            <Fragment>
                <h4>Cars:</h4>
                {Object.keys(context.cars).map(carID => (
                    <Car
                        key={carID}
                        name={context.cars[carID].name}
                        price={context.cars[carID].price}
                        incrementPrice={() => context.incrementPrice(carID)}
                        decrementPrice={() => context.decrementPrice(carID)}
                    />
                ))}
            </Fragment>
        )}
    </MyContext.Consumer>
);
```

(ProductList.js)
```
const ProductList = () => (
    <div className="product-list">
        <h2>Product list:</h2>
        <Cars />
        {/* Other potential product categories which we will skip for this demo: */}
        {/* <Electronics /> */}
        {/* <Clothes /> */}
        {/* <Shoes /> */}
    </div>
);
```

## Custom Hooks

A custom Hook is a JavaScript function whose name starts with ”use” and that may call other Hooks. For example, useFriendStatus below is our first custom Hook:


```import { useState, useEffect } from "react";

function useFriendStatus(friendID) {
  const [isOnline, setIsOnline] = useState(null);

  useEffect(() => {
    function handleStatusChange(status) {
      setIsOnline(status.isOnline);
    }

    ChatAPI.subscribeToFriendStatus(friendID, handleStatusChange);
    return () => {
      ChatAPI.unsubscribeFromFriendStatus(friendID, handleStatusChange);
    };
  });

  return isOnline;
}

```


we can use it like that:


```
function FriendStatus(props) {
  const isOnline = useFriendStatus(props.friend.id);

  if (isOnline === null) {
    return "Loading...";
  }
  return isOnline ? "Online" : "Offline";
}
```


### Rules of Hooks

* Only Call Hooks at the Top Level

* Only Call Hooks from React Functions

(instead you can
✅ Call Hooks from React function components.
✅ Call Hooks from custom Hooks
)



# Formik
### The Gist 


Formik keeps track of your form's state and then exposes it plus a few reusable methods and event handlers (handleChange, handleBlur, and handleSubmit) to your form via props. handleChange and handleBlur work exactly as expected--they use a name or id attribute to figure out which field to update.


Example:

https://imgur.com/a/T11cHop


Installation
```
npm install formik --save
```
or
```
yarn add formik
```
Formik is a small library that helps you with the 3 parts:

1.  Getting values in and out of form state
2.  Validation and error messages
3.  Handling form submission

`<Formik>` is a component that helps you with building forms. It uses a render props pattern made popular by libraries like React Motion and React Router.

Formik keeps track of your form's state and then exposes it plus a few reusable methods and event handlers (`handleChange`, `handleBlur`, and `handleSubmit`) to your form via `props`. `handleChange` and `handleBlur` work exactly as expected--they use a `name` or `id` attribute to figure out which field to update.


# Authentication
It's create stateful information for stateless HTTP protocol.

The server compares the user input with the database information about the user (e.g with the combination of user's email and password).
**authentication:** verify user identity
**authorization:** verify user permissions

## Session
Server needs to create and store information (session) about the user. 

**Cookies**
Small data that server sends to user's web browser.
Process flow: 
- user visiting  the website for the 1st time:
>server creates a session ID for user, sending to the browser to store it in (as cookie) and at the same time session ID is going to be stored in a database from the server side
- user visiting again the website:
> server reads the session ID from browser, search in the database  and identify the user


**Http Headers**
HTTP headers: let the client and the server pass additional information with an HTTP request or response.

**CSRF Attack** (cross-site request forgery)
Attacker force a logged-in user to perform an important action without their consent or knowledge.

## Token
User authenticate with username and password in order to obtain a token which allows to fetch a specific resource. 
It's more secure than cookies, becaues tokens have expiration date (e.g can change every 60 seconds). 

Process flow:
 - user visiting the website for the 1st time:
 > server generates a token with expiration date, sending to the browser
 - user visiting the website again:
 > browser sends token to the server, server reads the token, checks the authentication and expiration date. If everyting is fine, sending back the requested data and the  token (with new expiration date if it's necessary). If authentication failed or token is expired, server refuse the request. 

**JWT** (JSON Web Token)
Information exchange method.
Composed of three parts (each encoded in base64url):
- Header 
Contains: 
type of token, and the signing algorithm.

for example:

    {
     "alg": "HS256",
     "typ": "JWT" 
     }
    
- Payload 
Contains: registered, public and private claims

for example:

    {
     "sub": "1234567890", 
     "name": "John Doe", 
     "admin": true 
     }

- Signature
It's used to verify the message wasn't changed along the way, and, in the case of tokens signed with a private key, it can also verify that the sender of the JWT is who it says it is.

for example:

    HMACSHA256( base64UrlEncode(header) + "." + base64UrlEncode(payload), secret)

Putting all together:

    eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiYWRtaW4iOnRydWV9.TJVA95OrM7E2cBab30RMHrHDcEfxjoYZgeFONFh7HgQ

**XSS Attacks**
Attacker  injects client-side scripts into web pages viewed by other users.


