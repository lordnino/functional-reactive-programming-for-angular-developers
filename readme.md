# Functional Reactive Programming for Angular Developers RxJs and Observables

## resources
Here is a link to the [article](https://blog.angular-university.io/functional-reactive-programming-for-angular-2-developers-rxjs-and-observables/)

## A new asynchronous programming concept - the stream.

A stream is a sequence of values in time.  
Take for example the following stream of numeric values  
0, 1, 2, 3, 4, 5  

Another example of a stream is a sequence of mouse click events, with x and y coordinates
(100, 200), (110, 300), (400, 50) ...  

Everything that happens in the brwoser can be a stream: the sequence of browser events that are triggered when the user 
interacts with the page, data arriving from the server, timeouts getting triggered  

## A new asynchronous development primitive - The observable

In order for streams to be useful to build a program, we need a way to create streams, subscribe to them, 
react to new values, and combine streams together to build new ones.  

One important thing to bear in mind is that observables are not streams, those are two different concepts. 
What we need at this point is a library that implements the Observable primitive, and thats where Rxjs comes in.

## Introducing RxJs

RxJs stands for Reactive Extensions for Javascript, and it's an implementation of Observables for Javascript.   

```javascript
    var obs = Rx.Observable.interval(1000).take(5);
```

## Introducing Functional Reactive Programming

Functional Programming is a paradigm for software development that says that entire programs can be built uniquely around 
the notion of streams. Not only frontend programs, but any program in general.

## The core goal of FRP

The main idea of FRP is to build programs in a declarative only way, by defining what are streams, how they are linkded together 
and what happens if a new value arrives over time.  

Programs such as this can be built with very little to no application state variables, which are in general a source of error.  
To make it clearer: the application does have a state, but that state it's typically stored on certain streams or in the DOM, not 
in the application itself.

## Stateless UIs, but which part?

This abscene of state is meant mostly for smart components that have data services injected. Pure components 
can consume observables but might want to keep some internal state variable in practice, think for example **isOpen** flag
for a dropdown.

## The essential of how Observable work

```javascript
    var obs = Rx.Observable.interval(500)
                .take(5)
                .do(i => console.log(i))
```

Note that you probably want to avoid the **do()** operator as its only purpose is to produce side effects  
If we run this program, you might be surprised by the fact that nothing gets printes to the console! this is 
because of one of the main properties of this type of Observable.

## Observables are either hot or cold

If the plain Obseravble has no subscribers, it will not be triggered!  
The observable is said to be cold because it does not generate new values if no subscriptions exist. To get the 
numeric values printed to the console, we need to subscribe to the observable:  
```javascript
    obs.subscribe();
```

Here is an example:  
```javascript
    var obs = Rx.Obseravble.interval(500).take(5)
                .do(i => console.log("obs value " + i))
    obs.subscribe(value => console.log("observer 1 received " + value));
    obs.subscribe(value => console.log("observer 2 received " + value))

    // output
    //obs value 0
    //observer 1 received 0
    //obs value 0
    //observer 2 received 0

    //obs value 1
    //observer 1 received 1
    //obs value 1
    //observer 2 received 1

```

Looks like the side effect is being called twice! This leads to a second important property of Observables

## Observables are not shared by default

When we create a subscriber, we are setting up a whole new separate processing chain. The **obs** variable is just a definition, 
a blueprint of how an a functional processing chain of operators should be set up from the source of the event up until the sink of the event.

Being an **obs** just a blueprint of how to build an operation chain, what happen when we subscribe two observers is that two separate process chain 
are set up, causing the side effect to be printed twice, one for each chain

The important is to realize that we should keep two things in mind at all times when dealing with observables:  

* is the observable hot or cold ?
* is the observable share or not ?

## How does angular use Observables

Angular currently uses RxJs observables in two different ways:  

* as an internal implementation mechanism, to implement some of its core 
logix like **EventEmitter**
* as part of its public API, namely in Forms and the HTTP module.

## The map operator

The map operator is probably the most well-known functional programming operator our there, and Observable 
of course provides it. The map operator simply takes an Obseravble, and adds a transforming function that processes 
the output of the stream. For example:  
```javascript
    var obs = Rx.Obseravble.interval(500).take(5)
                .map(i => 2 * i)
```

It's important to note that the output of **map** is still another observable. What we have here is still only a definition 
of an operation chain. We still need to subsrcibe to this observable to get an output out of it.

## Map and filter used to do form validation

Another commonly used operator is **filter**. In Angular, forms can be handled as observables that we subscribe to. Meaning 
that the value of the whole form is itself observable, and that the value of each individual field itself an observable. 
Let's take for example a simple form:  
```html
    <form [ngFormModel]="form" (ngSubmit)="onSubmit()">
        <P>
            <label>First Name:</label>
            <input type="text" ngcontrol="firstName" />
        </p>
    </form>
```

Using observable we can combine the map and filter operations to get an uppercase and Validated version of the form content:

```javascript
    this.form.valueChanges
            .map((value) => {
                value.firstName = value.firstName.toUpperCase();
                return value;
            })
            .filter((value) => this.form.valid)
            .subscribe(validValue => ...);
```

## The share operator

One important property we saw at the beginning of this post, is that we subsribe to an observable, it triggers the instantiation of 
a separate processing chain. The **share** operator allows us to share a single subscription of a processing chain with other subscibers. For Example:  

```javascript
    var obs = Rx.Observable.interval(500).take(5)
                .do(i => console.log("obs value " + i))
                .share();
    obs.subscribe(value => console.log("observer 1 received " + value));
    obs.subscribe(value => console.log("observer 2 received " + value));

    //output => 
    //obs value 0
    //observer 1 received 0
    //observer 2 received 0

    //obs value 1
    //observer 1 received 1
    //observer 2 received 1
```