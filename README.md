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