# Design Patter

## Singleton

### Funcrion based
```js
const SingleTon = (function (){
   let instance;
   function createInstance() {
      const object = new Object()
      return object;
  }
  return {
    getInstance: function () {
      if(!instance) {
        instance = createInstance();
      }
      return instance;
    }
  }
})()

const o1 = SingleTon.getInstance();
const o2 = SingleTon.getInstance();
console.log(o1, o2, o1 === o2);
```

### classbased
```js
class Singleton {
    constructor() {
        if (!Singleton.instance) {
            // simulate a slow process that might cause race conditions
            Singleton.instance = this;
        }
        return Singleton.instance;
    }

    someMethod() {
        console.log("I'm the singleton instance");
    }

    static getInstance() {
        if (!Singleton.instance) {
            Singleton.instance = new Singleton();
        }
        return Singleton.instance;
    }
}

// Simulating multiple requests (async in nature)
async function requestSingleton() {
    return new Singleton();
}

(async () => {
    const results = await Promise.all([requestSingleton(), requestSingleton(), Singleton.getInstance()]);
    console.log(results);
    console.log(results[0] === results[2] );  // true
})();

```

## Observer

### Function based
```js
const Move = function() {
    this.handlers = [];
    this.subscribe = function(fn) {
        this.handlers.push(fn)
    }

    this.unsubscribe = function(fn) {
        this.handlers = this.handlers.filter(item => item != fn)
    }

    this.fire = function(data, thisArgs) {
        const scope = thisArgs || globalThis;

        this.handlers.forEach(item => item.call(scope, data));
    }
}

const handler1 = function(item) {
    console.log('handler1', item);
}

const handler2 = function(item) {
    console.log('handler2', item);
}

const move = new Move();

move.subscribe(handler1)
move.fire('event1')  // handler1 event1

move.unsubscribe(handler1)
move.fire('event2') //

move.subscribe(handler1)
move.subscribe(handler2)
move.fire('event3') // handler1 event1 handler2 event2
```

### Class based
```js
class Subject {
    constructor() {
        this.observers = []
    }

    subscribe(observer) {
        this.observers.push(observer)
    }

    unsubscribe(observer) {
        this.observers = this.observers.filter(obs => obs !== observer)
    }

    notify(data) {
        this.observers.forEach(observer => observer.update(data))
    }
} 

class Observer {
    constructor(name) {
        this.name = name
    }

    update(data) {
        console.log(`${this.name} received update`, data)
    }
}

const subject = new Subject();

const observer1 = new Observer('Observer1')
const observer2 = new Observer('Observer2')

subject.subscribe(observer1)
subject.subscribe(observer2)

subject.notify('State change1')

subject.unsubscribe(observer1);
subject.notify('State change2')
```

## Circut Breaker
```js
const CircuitBreaker = function (fn, failureCount, timeThresold) {
  let failures = 0;
  let lastCalled = 0;
  let isClosed = flase;

  if (isClosed) {
    const diff = Date.now() - lastCalled;

    if (diff > timeThresold) {
      isClosed = false;
    } else {
      return;
    }
  }

  try {
    const result = fn(...args);
    failures = 0;
    return 0;
  } catch (e) {
    failures++;
    lastCalled = Date.now();
    if (failures >= failureCount) {
      isClosed = true;
    }
  }
};
```
## Builder Design pattern
## Prototype design pattern
## Iterator
## Resource /Object pool


## Chain of responsibility
```js
class Approver {
    constructor() {
        this.nextApprover = null
    }

    setNext(approver){
        this.nextApprover = approver
        return approver
    }

    approveRequest(request) {
        if(this.nextApprover) {
            return this.nextApprover.approveRequest(request)
        } else {
            console.log("No one can approve this request");
        }
    }
}

class Manager extends Approver {
    approveRequest(request) {
        if(request.amount < 1000) {
            console.log(`Manger approve request of amount $${request.amount}`)
        } else {
            console.log('Manger can\'t approve the request. Passing it to Director');
            super.approveRequest(request)
        }
    }
}

class Director extends Approver {
    approveRequest(request) {
        if( request.amount < 5000) {
            console.log(`Director approve request of amount $${request.amount}`)
        } else {
            console.log('Director can\'t approve the request. Passing it to CEO');
            super.approveRequest(request)
        }
    }
}

class CEO extends Approver {
    approveRequest(request) {
        if( request.amount < 10000) {
            console.log(`CEO approve request of amount $${request.amount}`)
        } else {
            console.log("Request denied. No one can approve this request.");
        }
    }
}

class ExpenseRequest {
    constructor(amount) {
        this.amount = amount
    }
}

const manger = new Manager();
const director = new Director();
const ceo = new CEO();

manger.setNext(director).setNext(ceo);

const request1 = new ExpenseRequest(900);
const request2 = new ExpenseRequest(3000);
const request3 = new ExpenseRequest(6000);
const request4 = new ExpenseRequest(16000);

manger.approveRequest(request4);
```

## visitor

