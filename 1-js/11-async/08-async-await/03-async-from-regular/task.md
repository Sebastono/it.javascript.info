
# Call async from non-async

<<<<<<< HEAD
Abbiamo una funzione "regolare". Come chiamare `async` da questa ed usare il suo risultato?
=======
We have a "regular" function called `f`. How can you call the `async` function `wait()` and use its result inside of `f`?
>>>>>>> 99e59ba611ab11319ef9d0d66734b0bea2c3f058

```js
async function wait() {
  await new Promise(resolve => setTimeout(resolve, 1000));

  return 10;
}

function f() {
<<<<<<< HEAD
  // ...cosa bisonga scrivere qui?
  // dobbiamo chiamare async wait() ed aspettare per ricevere 10
  // ricorda, non possiamo usare "await"
=======
  // ...what should you write here?
  // we need to call async wait() and wait to get 10
  // remember, we can't use "await"
>>>>>>> 99e59ba611ab11319ef9d0d66734b0bea2c3f058
}
```

P.S. Il task è tecnicamente molto semplice, ma la domanda è piuttosto comune per gli sviluppatori nuovi ad async/await.
