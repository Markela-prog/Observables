# Rxjs

## Observables

RxJS Observables emit values over time - you can set up subscriptions to handle them

In order to listen to values that emitted and in order to use them, you must set a subscription (For every observable)

```
import { Component, DestroyRef, inject, OnInit } from '@angular/core';

import { interval } from 'rxjs';

@Component({
  selector: 'app-root',
  standalone: true,
  templateUrl: './app.component.html'
})
export class AppComponent implements OnInit {
  private destroyRef = inject(DestroyRef);

  ngOnInit(): void {
    const subscription = interval(1000).subscribe({
      next: (val) => console.log(val),
      complete: () => {},
      error: () => {}
    });

    this.destroyRef.onDestroy(() => {
      subscription.unsubscribe();
    })
  }
}
```

By the default, interval will do nothing, unless you have subscriber, because internally rxjs recognizes that if there is no one who is interested in values, it does not make sense to emit any

<br>

## RxJs Operators

Operators are the functions you can pipe into your observable data stream to perform transformations or any other kind of operation on those observable values

```
ngOnInit(): void {
    const subscription = interval(1000).pipe(
      map((val) => val * 2)
    ).subscribe({
      next: (val) => console.log(val),
      complete: () => {},
      error: () => {}
    });

    this.destroyRef.onDestroy(() => {
      subscription.unsubscribe();
    })
  }
```

## Observables vs signals

Observables - pipe line of values that are emitted over time (subscription needed to get notified about values) + no init value

- Good for events where values do arive async over time

Signals - value container (No subscription needed, you can look/read the value)

- Good for managing app state (data which is set to init value and which may change over time and wheere those changes should be reflected in UI)

<hr>

You can convert signal -> observable and observable -> signal

Signal -> Observable use toObservable()

```
export class AppComponent implements OnInit {
  clickCount = signal(0);
  clickCount$ = toObservable(this.clickCount);
  // interval = signal(0);
  // doubleInterval = computed(() => this.interval() * 2);

  private destroyRef = inject(DestroyRef);

  constructor() {
    // effect(() => {
    //   console.log(`Clicked button ${this.clickCount()} times`)
    // })
  }

  ngOnInit(): void {
    // setInterval(() => {
    //   this.interval.update(prevIntverbalNumber => prevIntverbalNumber + 1);
    //   // update signal
    // }, 1000);

    // const subscription = interval(1000).pipe(
    //   map((val) => val * 2)
    // ).subscribe({
    //   next: (val) => console.log(val),
    //   complete: () => {},
    //   error: () => {}
    // });

    // this.destroyRef.onDestroy(() => {
    //   subscription.unsubscribe();
    // })

    const subscription = this.clickCount$.subscribe({
      next: (val) => console.log(`Clicked button ${this.clickCount()} times`),
    });

    this.destroyRef.onDestroy(() => {
      subscription.unsubscribe();
    });
  }

  onClick() {
    this.clickCount.update((prevCount) => prevCount + 1);
  }
}
```

Observable -> Signal use toSignal()
```
@Component({
  selector: 'app-root',
  standalone: true,
  templateUrl: './app.component.html',
})
export class AppComponent implements OnInit {
  clickCount = signal(0);
  clickCount$ = toObservable(this.clickCount);
  interval$ = interval(1000);
  intervalSignal = toSignal(this.interval$, { initialValue: 0 });
  // interval = signal(0);
  // doubleInterval = computed(() => this.interval() * 2);

  private destroyRef = inject(DestroyRef);

  constructor() {
    // effect(() => {
    //   console.log(`Clicked button ${this.clickCount()} times`)
    // })
  }

  ngOnInit(): void {
    // setInterval(() => {
    //   this.interval.update(prevIntverbalNumber => prevIntverbalNumber + 1);
    //   // update signal
    // }, 1000);

    // const subscription = interval(1000).pipe(
    //   map((val) => val * 2)
    // ).subscribe({
    //   next: (val) => console.log(val),
    //   complete: () => {},
    //   error: () => {}
    // });

    // this.destroyRef.onDestroy(() => {
    //   subscription.unsubscribe();
    // })

    const subscription = this.clickCount$.subscribe({
      next: (val) => console.log(`Clicked button ${this.clickCount()} times`),
    });

    this.destroyRef.onDestroy(() => {
      subscription.unsubscribe();
    });
  }

  onClick() {
    this.clickCount.update((prevCount) => prevCount + 1);
  }
}

```

**toSignal removes subscription automatically**