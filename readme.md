# Functional Reactive Programming for Angular Developers RxJs and Observables

## resources
Here is a link to the article [https://blog.angular-university.io/functional-reactive-programming-for-angular-2-developers-rxjs-and-observables/]

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