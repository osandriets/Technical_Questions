# Technical_Questions

#### 1. Give examples of lifecycle hooks in Angular (ngOnChanges, ngAfterViewInit, ngOnDestroy, ngAfterViewChecked, ngDoCheck, etc.).

**ngOnChanges** - is called when any data-bound property of a directive changes(update form if some property changed  )
```sh
import ...

@Component({
  selector: 'app-ui-account-edit',
  templateUrl: './edit-account.component.html',
  styleUrls: ['./edit-account.component.scss'],
  changeDetection: ChangeDetectionStrategy.OnPush,
})
export class EditAccountComponent implements OnDestroy, OnChanges {
  @Input() formData!: CreateAccountFormData;
  @Input() customers!: Customer[] | null;
  @Input() plants!: Plant[] | null;
  @Input() managers!: Manager[] | null;
  @Input() status!: Statuses | null;
  @Output() save: EventEmitter<CreateAccountFormData> = new EventEmitter<CreateAccountFormData>();

  accountTypes = ACCOUNT_TYPES;

  form!: UntypedFormGroup;
  subscriptions = new Subscription();

  constructor(private readonly fb: UntypedFormBuilder) {
    this.form = this.fb.group({
      customerName: [{ value: '', disabled: true }, [Validators.required]],
      soldTo: ['', [Validators.required]],
      plantId: [{ value: '', disabled: true }, [Validators.required]],
      crewId: [{ value: '', disabled: true }],
      accountType: [{ value: '', disabled: true }, [Validators.required]],
    });

    this.subscriptions.add(this.soldToControl?.valueChanges.subscribe(soldTo => {
      this.customerControl?.setValue(
        this.customers?.find(c => c.soldToID === soldTo)?.customerName ?? '',
        { onlySelf: false, emitValue: false },
      );
    }));
  }

  get soldToControl(): AbstractControl | null {
    return this.form.get('soldTo');
  }

  get customerControl(): AbstractControl | null {
    return this.form.get('customerName');
  }

  get plantControl(): AbstractControl | null {
    return this.form.get('plantId');
  }

  ngOnDestroy(): void {
    this.subscriptions.unsubscribe();
  }

  ngOnChanges(changes: SimpleChanges): void {
    this.updateFormValue(changes);
  }

  handleSave(): void {
    this.save.emit({
      ...this.form.value,
      customerName: this.customers?.find(c => c.soldToID === this.soldToControl?.value)?.customerName ?? '',
      manager: this.managers?.find(m => m.plantId === this.plantControl?.value)?.plantManagerName ?? '',
      plant: this.plants?.find(p => p.plantID === this.plantControl?.value),
    });
  }

  private updateFormValue(changes: SimpleChanges): void {
    if (!changes.formData || !this.form) {
      return;
    }

    if(this.formData.soldTo === 0) {
      this.formData.soldTo = null;
    }

    this.form.patchValue(this.formData);
  }

}

```

**ngAfterViewInit** - A lifecycle hook that is called after Angular has fully initialized a component's view. Define an ngAfterViewInit() method to handle any additional initialization tasks (for example you need update map or table, usually necessary if you use third party libraries  )

```sh
import ...

@Component({
  selector: 'app-shared-data-table',
  templateUrl: './data-table.component.html',
  styleUrls: ['./data-table.component.scss'],
})
export class DataTableComponent<T> implements OnInit, OnDestroy, AfterViewInit {

  constructor(
    private readonly primengConfig: PrimeNGConfig,
    private readonly cdr: ChangeDetectorRef,
  ) {
  }

  ngOnInit(): void {
    ...
  }

  ngAfterViewInit(): void {
    this.cdr.detectChanges();

    if(this.filters && Object.keys(this.filters).length) {
      this.showFilters = true;
    }
  }

  ngOnDestroy(): void {
    localStorage.setItem(this.tableType, JSON.stringify(this.showedColumns));
  }
}

```

**ngOnDestroy** - A lifecycle hook that is called when a directive, pipe, or service is destroyed. Use for any custom cleanup that needs to occur when the instance is destroyed (unsubscribe or save or clean date) 
```sh
import ....

@Component({
  selector: 'app-auth-container',
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
    
    localStorage.setItem('index', this.index);
  }
  
  private randomNumber(): number {
    const n = Math.floor( Math.random() * 6 );

    return n || 1;
  }
}
```

**ngAfterViewChecked** - A lifecycle hook that is called after the default change detector has completed checking a component's view for changes (didn't find any examples in my projects)

**ngDoCheck** - A lifecycle hook that invokes a custom change-detection function for a directive, in addition to the check performed by the default change-detector (didn't find any examples in my projects)


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
