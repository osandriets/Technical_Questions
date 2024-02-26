# Technical_Questions

#### 1. Give examples of lifecycle hooks in Angular (ngOnChanges, ngafterviewinit, ngOnDestroy, ngafterviewchecked, ngdocheck, etc.).

ngOnChanges

ngafterviewinit

**ngOnDestroy** - unsubscribe
```sh
import ....

@Component({
  selector: 'app-nx-ionic-auth-container',
  templateUrl: './auth-container.component.html',
  styleUrls: ['./auth-container.component.scss'],
  changeDetection: ChangeDetectionStrategy.OnPush,
  animations: [
    trigger('flyInOut', [
      state('in', style({ opacity: 1 })),
      transition('void => *', [
        style({ opacity: 0 }),
        animate(2000),
      ]),
      transition('* => void', [
        animate(2000, style({ opacity: 1 })),
      ]),
    ]),
  ],
})
export class AuthContainerComponent implements OnInit, OnDestroy {
  private _index: Subject<number> = new BehaviorSubject(this.randomNumber());
  index$: Observable<number> = this._index.asObservable();

  private destroy$ = new Subject<void>();

  constructor(
  ) {
  }

  ngOnInit(): void {
    this.index$
      .pipe(
        takeUntil(this.destroy$),
        filter(i => !!i),
      )
      .subscribe(() => {
         setTimeout(() => {
          this._index.next(this.randomNumber());
        }, 6000);
      });
  }

  ngOnDestroy(): void {
    this.destroy$.next();
  }
  
  private randomNumber(): number {
    const n = Math.floor( Math.random() * 6 );

    return n || 1;
  }
}

```

ngafterviewchecked

ngdocheck


#### 2. When are uppercase and lowercase used in Angular?

UPPER_UNDERSCORE_CASE: Traditional for constants. For example "NUMBER_OF_USERS"

camelCase: Symbols, properties, methods, pipe names. For example: "userComponent"

UpperCamelCase: Class names, interfaces. For example: "UserInterface"

kebab-case: file names, component selectors. For example: "app-users-list"

underscore_case: Not typically used in Angular.


#### 3. What is the fundamental difference between Angular and JavaScript expressions?

One major difference between Angular expressions and JavaScript expressions is that Angular expressions are compiled while JavaScript expressions are not. This means that Angular expressions are more efficient since they're already pre-processed. Additionally, Angular expressions can access scope properties while JavaScript expressions cannot. Finally, Angular expressions support some additional features such as filters and directives which aren't available in JavaScript expressions.

#### 4. What design patterns did you apply (intentionally or unintentionally) during your last project?

- Dependency Injection
- Singleton
- Component
- Decorator
- Observer
- Builder

- strategy
- factory

#### 5. How do you add an Angular router to your application?
app.module.ts
```sh
...
import { AppRoutingModule } from './app-routing.module';

@NgModule({
  ...
  imports: [
  ...
    AppRoutingModule,
  ],
})
export class AppModule {
}
```

app-routing.module.ts
```sh
import { Route } from '@angular/router';
import { AuthGuard } from '@lib/lib-core';

import { AppContainerComponent } from './containers/app-container/app-container.component';

export const routes: Route[] = [
  {
    path: '',
    redirectTo: 'landing',
    pathMatch: 'full',
  },
  {
    path: '',
    component: AppContainerComponent,
    children: [
      {
        path: 'auth',
        loadChildren: () =>
          import('@lib/lib-auth').then(m => m.AuthModule),
      },
      {
        path: 'landing',
        canActivate: [AuthGuard],
        children: [
          {
            path: '',
            loadChildren: () =>
              import('@lib/lib-landing').then(m => m.LandingModule),
          },
        ],
      },
      {
        path: '',
        canActivate: [AuthGuard],
        children: [
          {
            path: '',
            loadChildren: () =>
              import('@lib/lib-items').then(m => m.MobilityModule),
          },
        ],
      },
    ],
  },
];

@NgModule({
  imports: [
    RouterModule.forRoot(routes),
  ],
  exports: [
    RouterModule,
  ],
})
export class AppRoutingModule { }

```

#### 6. Provide an example of how your implementation of a client-side framework helped you effectively create a single-page application.

https://apx.halliburton.com/, https://bondswithoutborders.com/ - for see all you need access to, I don't have it now. But I can explain the most interesting fiches.

#### 7. Differences between Angular.js and Angular? When are they better than REACT?

1. Angular.js and Angular - different library. Angular was built on based Angular.js. AngularJS is the older of the two, while Angular is its newer, more advanced version. The main difference between the two is that AngularJS is based on JavaScript and Angular is based on TypeScript.

2. Difference between Angular and REACT. If we talk about history, previously there was a division, you need SEO, choose react, you need the performance of Angular. Angular also has everything you need out of the box, for example, routing. Also, easier to use for people who work with the Backend. REACT is easy to start and use like a render machine. If we talk about now, I prefer Angular because I know best practices and have more experience with it.

#### 8. Key advantages of Angular (open source, MVC pattern architecture, supported validations, clear syntax, etc.).

Angular is opinionated by definition.
Reactive form and template driven form with validations.
Routing with lazy loading.
Angular Material + CDK
EsLint (before TSLint)

