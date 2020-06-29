---
layout: post
title:  "Reactive Programming in Angular"
categories: "reactive angular rxjs"
---

### What is Reactive programming?

I highly recommend you read this tutorial:

- [Reactive Programming with RxJS](https://codecraft.tv/courses/angular/reactive-programming-with-rxjs/overview/)

Like the author said, the essence of reactive programming comprise of:

- **Stream**: &nbsp;&nbsp;&nbsp;&nbsp; A sequence of values over time
- **Operator**: &nbsp; Manipulate the data stream, like filter, map, etc.
- **React**: &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Subscribe to the stream and do something.

In my understanding, what sets Reactive programming apart are:

**1. The data is not a single value.**
     
     It's a sequence of values.

**2. The data is immutable**

     The values are emitted over time. When a new value is generated,
     instead of changing the old value, you publish a new one into the stream.

**3. It uses Publish and Subscribe pattern.**
     
     The stream becomes hot only when it's subscribed.

RxJS Observables are streams + operators. Angular uses Observables in:

- **EventEmitter**
- **HTTP**
- **Reactive Form**

### HTTP with Observables

I highly recommend you read this tutorial:

- [HTTP Example with Observables](https://codecraft.tv/courses/angular/http/http-with-observables/)

Angular HTTP returns Observables. You don't have to convert it like the way you turn it into Promise. You can consume it right away in your component HTML with **async** pipe.

```html
<div  *ngFor="let user of getUsers$ | async"> ... </div>
```

### NgRx Store

I hignly recommend you read this tutorial:

- [React Redux Tutorial for Beginners: The Complete Guide (2020)](https://www.valentinog.com/blog/redux/)

The following diagram represents the overall general flow of application state in NgRx.

![Cascading Selector](/assets/2020-6-29-state-management-lifecycle.png "NgRx State Store")

I would like to compare the NgRx Store to a bank. It's a centralized place where our money are stored. Without the bank, when we need some money, we would simply go and fetch it from our Dad. And then we spend it.

> i-need-some-money.component.ts

```typescript
money$: Observable<Money>;

ngOnInit() {
    this.money$ = this.dadService.getSomeCash(200);
}
```

> i-need-some-money.component.html

```html
<div>Let's spend it { { money$ | async } }</div>
```

The whole process looks very straightforward. After all, dealing with Dad is always simple and easy.

However, dealing with Mom is another story. Mom is not happy with our spending. She wants to monitor our pocket, as always. So we can never go and ask for money from Dad directly. Instead, we have to request it from a central bank.

If you look at the diagram above:
1. the **NgRx Store** is the centralized bank.
2. The **Selector** is our bank account.
3. The **Action** is our poor request DRAW_SOME_CASH_ACTION.
4. The **Effects** will go and fetch some money from our Dad.
5. The **Reducer** will then put the money into our account.

> i-need-some-money.component.ts

```typescript
ngOnInit() {
    this.money$ = this.store.pipe(select(this.storeSelectors.getMyMoney));

    this.store.dispatch(new EntityActions.DrawSomeCashAction(200));
}
```

> draw-some-cash-action.ts

```typescript
export const ACTION_TYPES = {
    DRAW_SOME_CASH: type('[Poor Me] request some money'),
    DRAW_SOME_CASH_SUCCESS: type('[Poor Me] request some money success'),
    DRAW_SOME_CASH_FAIL: type('[Poor Me] request some money fail'),
}

export class DrawSomeCashAction implements Action {
    readonly type = ACTION_TYPES.DRAW_SOME_CASH;

    constructor(public payload: number) {}
}

export class DrawSomeCashSuccessAction implements Action {
    readonly type = ACTION_TYPES.DRAW_SOME_CASH_SUCCESS;

    constructor(public payload: Money) {}
}

export class DrawSomeCashFailAction implements Action {
    readonly type = ACTION_TYPES.DRAW_SOME_CASH_FAIL;

    constructor(public payload?: any) {}
}

export type Actions
    = DrawSomeCashAction
    | DrawSomeCashSuccessAction
    | DrawSomeCashFailAction;
```

> put-the-money-into-my-account-reducer.ts

```typescript
export interface State {
    money: Money;
}

export const initialState: State = {
    money: null
}

export function reducer(state: State = initialState, action: DrawSomeCashActions.Actions): State {
    switch (action.type) {
        case DrawSomeCashActions.ACTION_TYPES.DRAW_SOME_CASH: {
            return Object.assign({}, state, {
                wait: true
            });
        }

        case DrawSomeCashActions.ACTION_TYPES.DRAW_SOME_CASH_SUCCESS: {
            return Object.assign({}, state, {
            	money: action.payload,
                wait: false
            });
        }

        case DrawSomeCashActions.ACTION_TYPES.DRAW_SOME_CASH_FAIL: {
            return Object.assign({}, state, {
            	money: null,
            	notifications: [...state.notifications, action.payload],
                wait: false
            });
        }

        default:
            return state;
    }
}
```

> go-and-get-money-from-dad-effects.ts

```typescript
@Injectable()
export class GoAndGetMoneyFromDadEffects {
    constructor(private actions$: Actions,
        private dadService: DadService) {

        this.initGetMoneyFromDad$();
    }

    private initGetMoneyFromDad$() {
    	this.actions$.pipe(
            ofType(DrawSomeCashActions.ACTION_TYPES.DRAW_SOME_CASH),
            switchMap((act: DrawSomeCashActions.DrawSomeCashActions) => {
                return this.dadService.getSomeCash(action.payload)
                    .pipe(map(money => new DrawSomeCashSuccessAction(money)));
            }),
            catchError(err => new DrawSomeCashFailAction("Your Mom says you spent too much!!"))
        });
    }
}
```

Look, even though you are still getting money from your Dad, but the whole process becomes far more complicated. The benefit is your mom is able to monitor your pocket now.

Save is Love!