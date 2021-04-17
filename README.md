# schedulers

Objects in this library run callbacks asynchronously, allowing useful pauses 
between calls. This can be used for load balancing, rate limiting, lazy execution.

# IntervalScheduler

Runs tasks asynchronously, maintaining a fixed time interval between starts.

``` dart
final scheduler = IntervalScheduler(delay: Duration(seconds: 1));

scheduler.run(()=>downloadPage('pageA')); // starts download immediately
scheduler.run(()=>downloadPage('pageB')); // will start one second later
scheduler.run(()=>downloadPage('pageC')); // will start two seconds later
```

# LazyScheduler

Runs only the last added task and only if no new tasks have been added during 
the time interval.

``` dart
final scheduler = LazyScheduler(latency: Duration(seconds: 1));

scheduler.run(()=>pushUpdate('1')); // maybe we will push 1
scheduler.run(()=>pushUpdate('1+1')); // no we will push 1+1
scheduler.run(()=>pushUpdate('1+1-1')); // no we will push 1+1-1
scheduler.run(()=>pushUpdate('1')); // it's good we so lazy
scheduler.run(()=>pushUpdate('777')); // maybe we will push this
```

And one second later the `scheduler` runs `pushUpdate('777')`.

# RateScheduler

Runs no more than N tasks in a certain period of time.

``` dart
final scheduler = RateScheduler(3, Duration(seconds: 1)); // 3 per second

// the following tasks are executed immediately
scheduler.run(()=>downloadPage('pageA'));
scheduler.run(()=>downloadPage('pageB'));
scheduler.run(()=>downloadPage('pageC'));

// the following tasks are executed one second later
scheduler.run(()=>downloadPage('pageD'));
scheduler.run(()=>downloadPage('pageE'));
scheduler.run(()=>downloadPage('pageF'));
 
// the following tasks are executed two seconds later
scheduler.run(()=>downloadPage('pageG'));
scheduler.run(()=>downloadPage('pageH'));
scheduler.run(()=>downloadPage('pageI'));

scheduler.run(()=>pushUpdate('1')); // maybe we will push 1
scheduler.run(()=>pushUpdate('1+1')); // no we will push 1+1
scheduler.run(()=>pushUpdate('1+1-1')); // no we will push 1+1-1
scheduler.run(()=>pushUpdate('1')); // it's good we so lazy
scheduler.run(()=>pushUpdate('777')); // maybe we will push this
```