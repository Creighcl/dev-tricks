# How to Use a Spy to Replace Service Calls

Suppose you have a service file with methods `RetrievePerson('person')` and
a method `GetPersonFromMyDatabase('person')`.

When we call `RetrievePerson()`, we expect it to call `GetPersonFromMyDatabase()` after some validation. The problem is, we just want to completely avoid `GetPersonFromMyDatabase()` being actually executed.

## Why is this tricky?

Welp. You'd think you could just spy on that method, but you'd be wrong. Turns out that you can't be sure that you're spying on that method within the right context.

## First order of business

In the service file, you will be exporting the database call, so that sucks. Export both methods, in fact. *Here's the gotcha* if you want to be able to spy on `RetrievePerson`'s direct interaction with `GetPersonFromMyDatabase` then you'll need to ensure it calls that method slightly differently.

```javascript
export const GetPersonFromMyDatabase = (personName) => ...

const RetrievePerson = personName => {
  ...
  return exports.GetPersonFromMyDatabase(personName);  //NOTICE! exports.
}

```

or, more likely if promises are involved...

```javascript

export const GetPersonFromMyDatabase = (personName) => ...some Promise...

const RetrievePerson = personName => new Promise((resolve) => {
  if (notValid) {
    throw new Error(Reason);
  }

  resolve(exports.GetPersonFromMyDatabase(personName));
})

```

## Meanwhile, in our tests...

The import must import both methods together

```javascript
import * as PersonService from '../path/to/service';
```

Now we can easily spy against *the* version of `GetPersonFromMyDatabase` that we know `RetrievePerson` will interact with! If using promises, don't forget to use *done*

```javascript

const databaseSpy = spyOn(PersonService, 'GetPersonFromMyDatabase').and.returnValue(Promise.resolve('abc'));
PersonService.default(validParams)
  .then(() => { 
    expect(databaseSpy).toHaveBeenCalled(); // returns true
    done();
  })
  .catch(() => {
    fail('should have resolved!');
  });

```

Also worth discussing is that instead of mocking a promise, you'll have headaches if you don't know to use the static `Promise.resolve()` and `Promise.reject()` methods. Which instantly resolve or reject.

# TL:DR
Discussion on writing tests on a method and trying to prevent that method from calling some other method in the same file during testing
- Make sure to call each other via `exports.othermethod` to ensure the context is kept together for later
- use `import * as Something from '../some/where';` to import the methods together and with a sealed context
- use `spyOn(Something, 'methodname')`. You can do `.and.returnValue` or `.and.callFake`
- For Promises, don't try to return a pretend Promise. Just return `Promise.resolve()` and `Promise.reject()`
- For Promise testing, don't forget to use `done()` in any case, even if its not meant to be asynchronous