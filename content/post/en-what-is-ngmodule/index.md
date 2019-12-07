---
title: What is NgModule?
date: 2019-09-12
lastmod: 2019-09-13
diagram: true
image:
  placement: 3
  caption: ''
draft: false
highlight: true
highlight_languages: ["typescript"]  # Add support for highlighting additional languages
---

## What's NgModule

Welcome to Angular Fundamental Series 📚

The story behind this blog is that I saw the Chrome Dev Tools 🛠️error in my coworker screen. The error is

```typescript
Error: Template parse errors:
'mat-tab' is not a known element:
1. If 'mat-tab' is an Angular component, then verify that it is part of this module.
2. If 'mat-tab' is a Web Component then add 'CUSTOM_ELEMENTS_SCHEMA' to the '@NgModule.schemas' of this component to suppress this message.
```

Did you forget to import MatTableModule into the feature module that you are developing?
I said.

After she checked, and she found that she didn't import it. She then fixed it, and it works fine.

I keep this problem in my mind, and ask to my ex-coworker who often use Angular.
หลังจากนั้นผมเก็บความผิดพลาดนี้ไว้ แล้วจึงไปถามรุ่นพี่ใช้ Angular อยู่เป็นประจำว่า 
"Do you really understand what **NgModule** is"

He replyed "I don't really understand what **NgModule** is. I just know how to make it work." 😱

Thus, I started this blog to clarify **NgModule**.

Let's start.

## NgModule - Unit of Compilation & Distribution

NgModule is JavaScript Class enhanced behaviors with decorator namely  
**@NgModule**. 
**@NgModule** receives metadata object.

```typescript
import { BrowserModule } from '@angular/platform-browser';
import { NgModule } from '@angular/core';
import { AppComponent } from './app.component';

// @NgModule decorator with metadata object (declarations, imports, ...)
@NgModule({
  declarations: [AppComponent],
  imports: [BrowserModule],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule {}
```

What does **NgModule** do?
NgModule receives metadata for

1. Tell Angular Compiler how to compile Components, Templates, Directives, Pipes
2. Define Components, Directives, Pipes to be public via metadata that is **exports** in order to let other modules access and exploit it.
3. Add Services or Providers for Dependency Injection in Component

Let's see case 1,2 via the below example.

```typescript
@NgModule({
  imports: [
    CommonModule,
    FlexLayoutModule,
    BrowserAnimationsModule,
    MatCardModule,
    MatButtonModule,
    MatIconModule,
    MatProgressSpinnerModule,
  ],
  declarations: [
    CompanyCardComponent,
    TechToIconPipe,
    CompanyListComponent
  ],
  exports: [
    CompanyListComponent,
  ]
})
export class CompanyModule { }
```

[CompanyModule's full code](https://github.com/AngularThailand/who-use-angular-in-thailand/blob/master/apps/who-use-angular-in-thailand/src/app/company/company.module.ts)

There are interesting metadatas - imports, declarations, exports.

However, the order will be exports, imports, and declarations respectively.

</br></br>
**exports**

Define Components, Directives, Pipes to be exported in scope that import this module.

For example, ModuleA import ModuleB, then everything being exported in ModuleB will be in scope of compilation of ModuleA

Therefore, ModuleA can used exported Components, Directives, and Pipes in ModuleB.

The below example shows **CompanyListComponent** is exported in **CompanyModule**

```typescript
@NgModule({
  ...,
  exports: [
    CompanyListComponent,
    // XXXPipe,
    // XXXDirective
  ]
})
export class CompanyModule { }
```

Then, **AppModule** import **CompanyModule**.

```typescript
@NgModule({
  declarations: [
    AppComponent
  ]
  imports: [
    ...,
    CompanyModule,
    ...
  ],
  ...
})
export class AppModule { }
```

[AppModule's full code](https://github.com/AngularThailand/who-use-angular-in-thailand/blob/master/apps/who-use-angular-in-thailand/src/app/app.module.ts)

**AppComponent** inside declarations understands **CompanyListComponent**. and it is able to use **CompanyListComponent** in its template.

![Export CompanyModule](./exports-company-module.png)

Here is the example of **AppComponent** Template (app.component.html) uses `<angular-th-company-list></angular-th-company-list>` that is selector in **CompanyListComponent**.

```html
...
<angular-th-company-list [companies]="companies$ | async" [loaded]="loaded"></angular-th-company-list>
...
```

[app.component.html's full code](https://github.com/AngularThailand/who-use-angular-in-thailand/blob/master/apps/who-use-angular-in-thailand/src/app/app.component.html)

</br></br></br>
**imports**

imports other modules, then all Components, Pipes, Directives in that module will in the scope at Compile-time.

Providers registered in that Module can be used also at Run-time (Dependency Injection).

```typescript
@NgModule({
  imports: [
    CommonModule,
    FlexLayoutModule,
    BrowserAnimationsModule,
    MatCardModule,
    MatButtonModule,
    MatIconModule,
    MatProgressSpinnerModule,
    // XXXModule
  ],
  ...
})
export class CompanyModule { }
```

From above example.
**CompanyCardComponent** (company-card.component.html) uses `<mat-card></mat-card>`
and Directive fxLayoutAlign `<div fxLayoutAlign="center center">`

```html
<mat-card fxLayout="column" *ngIf="company">
  ...
  <div fxLayoutAlign="center center" style="height: 300px">
    ...
  </div>
  ...
</mat-card>
```

[company-card.component.html's full code](https://github.com/AngularThailand/who-use-angular-in-thailand/blob/master/apps/who-use-angular-in-thailand/src/app/company/company-card/company-card.component.html#L1)

To use mat-card (Component) and fxLayoutAlign (Directive), we have to tell Angular Compiler that we are going to use **MatCardComponent**, **FxLayoutAlignDirective**.
In **MatCardModule** and **FxLayoutModule** have already exported it.
Thus, we just imports **MatCardModule** and **FlexLayoutModule** to use it.

How's about **CommonModule**? What does it actually do?
**CommonModule** is important.
**CommonModule** exports `*ngIf, *ngFor, [ngClass], AsyncPipe (| async), CurrencyPipe (| currency)` that why we can use all of it.

[Read more about CommonModule](https://angular.io/api/common/CommonModule)

Other components such as **MatButton, BrowserAnimationsModule, MatIcon, MatProgressBar** has the same concept.

In order to use Angular form, **FormsModule**, or **ReactiveFormsModule** has to be imported also.
![Import CompanyModule](./imports-company-module.png)

</br></br></br>
**declarations**

Define all Component, Directive, Pipe to tell Angular Compiler that we are going to use it. **In this module scope only**

```typescript
@NgModule({
  ...
  declarations: [
    CompanyCardComponent,
    TechToIconPipe,
    CompanyListComponent,
    // XXXDirective
  ],
  ...
})
export class CompanyModule { }
```

From the above example,

Component (**CompanyCardComponent, CompanyListComponent**) and Pipes (**TechToIconPipe**) 
know each other because they are in the same compilation scope. Hence, they can use the others interchangably.

**CompanyCardComponent** uses **TechToIconPipe** in Template

```html
...
<img *ngIf="tech | techToIcon as icon; else techText"/>
...
```

[company-card.component.html's full code](https://github.com/AngularThailand/who-use-angular-in-thailand/blob/master/apps/who-use-angular-in-thailand/src/app/company/company-card/company-card.component.html#L24)

**CompanyListComponent** loops to create `<angular-th-company-card></angular-th-company-card>` via `*ngFor` in Template.

```html
...
<angular-th-company-card *ngFor="let company of companies" [company]="company"></angular-th-company-card>
...
```

[company-list.component.html's full code](https://github.com/AngularThailand/who-use-angular-in-thailand/blob/master/apps/who-use-angular-in-thailand/src/app/company/company-list/company-list.component.html#L3)

![Declaration Company Module](./declarations-company-module.png)

</br></br></br>
**providers**

After Angular Version 6.0, we can create Singleton Service by defining `providedIn: 'root'` in metadata of `@Injectable()` to tell Angular registering Service into Application Root. This process make compiler be able to Tree-shaking unused services.

```typescript
@Injectable({
  providedIn: 'root'
})
export class CompanyService {

  constructor(private http: HttpClient) { }
  getCompanies() {
    return this.http.get<Company[]>('/assets/data/companies.json').pipe(
      shareReplay(1));
  }
}
```

Before Version 6.0, we registers services at module or component level.

```typescript
@NgModule({
  ...
  providers: [
    CompanyService,
    // {
    //   provide: HTTP_INTERCEPTORS, useClass: HttpInterceptorService, multi: true
    // },
  ],
  ...
})
```

**forRoot, forChild, forFeature, forXXX, xxx is ModulewithProviders**

Everyone may notice forRoot, forChild, forFeature, and so on. What is it? 🤔

```typescript
@NgModule({
  imports: [RouterModule.forRoot(...)],
  exports: [RouterModule]
})
export class AppRoutingModule {}

@NgModule({
  imports: [RouterModule.forChild(...)],
  exports: [RouterModule]
})
export class CompanyRoutingModule { }

@NgModule({
  imports: [
    StoreModule.forFeature(...),
    TranslateModule.forChild(...),
    EffectsModule.forFeature(...)
  ],
})
export class ExamplesModule { }

@NgModule({
  imports: [
    AngularFireModule.initializeApp(...),
    StoreModule.forRoot(...),
    StoreRouterConnectingModule.forRoot(),
    EffectsModule.forRoot(...),
    StoreDevtoolsModule.instrument(...),
    TranslateModule.forRoot(...)
  ],
})
export class AppModule { }
```

It seems like to configure something. Right? Let's check the example via AngularFireModule 🔥.

```typescript
@NgModule({
  imports: [
    AngularFireModule.initializeApp({
      apiKey: 'perjerzKey',
      authDomain: 'perjerz.app',
      projectId: 'perjerzId',
      databaseURL: 'https://perjerz.firebaseio.com',
      storageBucket: 'perjerz.appspot.com',
      messagingSenderId: '1212312121',
      appId: '1150',
    }),
    ...
  ],
})
export class AppModule { }
```


Yes, it was used to configure. ✅ We can configure value in tokens and services to use Dependency Injection in component. Component will be able to use that values.

Let's see the example via Setup AngularFire. Angular 🅰️ App want to talk to Firebase 🔥 Therefore, we have to configure where is the our Firebase.

The code below is AngularFireModule code from @angular/fire. It passes parameters to config providers - FirebaseOptionsToken and FirebaseNameOrConfigToken.

```typescript
const FirebaseAppProvider = {
    provide: FirebaseApp,
    useFactory: _firebaseAppFactory,
    deps: [
        FirebaseOptionsToken,
        [new Optional(), FirebaseNameOrConfigToken]
    ]
};

@NgModule({
    providers: [ FirebaseAppProvider ],
})
export class AngularFireModule {
    static initializeApp(options: FirebaseOptions, nameOrConfig?: string | FirebaseAppConfig) { // Pass options, nameOrConfig values here <----
        return {
            ngModule: AngularFireModule,
            providers: [
                { provide: FirebaseOptionsToken, useValue: options },
                { provide: FirebaseNameOrConfigToken, useValue: nameOrConfig }
            ]
        }
    }
}
```

[AngularFireModule's full code](https://github.com/angular/angularfire2/blob/master/src/core/firebase.app.module.ts#L58)\

When we use Firebase services such as AngularFireDatabase, AngularFireAuth, we can use it directly without specifing apiKey, authDomain, and databaseURL every times. It has already resolved values from tokens that we configure (register) in the module.

```typescript
@Injectable()
export class AppGuard implements CanActivate {
  constructor(
    private db: AngularFireDatabase,
    private auth: AngularFireAuth,
    private router: Router,
  ) { }
   canActivate(): Observable<boolean | UrlTree> {
    return this.auth.authState.pipe(switchMap(user => {
        return this.db.object(`...`).valueChanges().pipe(...)
      }
   }
}
```

In addition, newcomers are able to read configuration at the module easily.


[อ่านเรื่อง Dependency Injection ได้ที่ DevNote](https://medium.com/devnote/%E0%B8%97%E0%B8%B3%E0%B8%84%E0%B8%A7%E0%B8%B2%E0%B8%A1%E0%B8%A3%E0%B8%B9%E0%B9%89%E0%B8%88%E0%B8%B1%E0%B8%81%E0%B8%81%E0%B8%B1%E0%B8%9A-dependency-injection-%E0%B9%83%E0%B8%99-angular-880cbf483239)

</br></br></br>
**entryComponents**

ไว้ระบุ Component ที่จะต้อง Compile บอก Angular Compiler ว่าเราจะใช้ Component เหล่านี้แน่ๆ สร้าง Component Factory ทำ Dynamic Load ณ Run-time (Imperatively) ไม่ต้อง Tree Shake ลบ Component นี้ออกไป

โดยปกติแล้ว Component ที่เราใช้ใน Template `<angular-th-company-card></angular-th-company-card>` Compiler มันรู้ได้เลยจากการใช้ (Reference) จึงสามารถ Inline Instantiation ได้ (Statically, Declaratively)

แต่กลับกันในกรณีการทำ [Dynamic Component Loader](https://angular.io/guide/dynamic-component-loader) (Load Component ตอน Runtime) ซึ่งต้องใช้ entryComponents

ตัวอย่างที่ชัดเจนเลยคือ [MatDialog](https://material.angular.io/components/dialog/overview#configuring-dialog-content-via-code-entrycomponents-code-) (Material Dialog) ที่เราต้องระบุ Component ที่เราสร้างไว้สำหรับเปิด Dialog ขึ้นมา

```typescript
@NgModule({
  imports: [
    // ...
    MatDialogModule
  ],

  declarations: [
    AppComponent,
    ExampleDialogComponent
  ],

  entryComponents: [
    ExampleDialogComponent // Must add this Component, however for Ivy, it is not neccessary anymore
  ],

  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule {}
```

อีกกรณีหนึ่งคือการ Load Component ใน Routes (RouterModule) นั้นใช้ Component Factory เหมือนกันเพราะมัน Dynamic Load ณ Runtime เพียงแต่ว่าเราไม่ต้องระบุ Component ใน entryComponents เองเพราะ RouterModule แอบเพิ่มให้เองตอน Compile

```typescript
const routes: Routes = [
  {
    path: 'a',
    component: ComponentA
  },
  {
    path: 'b',
    component: ComponentB
  }
];

@NgModule({
  imports: [
    RouterModule.forRoot(routes)
  ],
  exports: [RouterModule],
})
export class AppRoutingModule {}
```

มีอีกเรื่องหนึ่งที่น่าสนใจคือการที่ เราประกาศ Declarations และ Exports ComponentA, ComponentB, ComponentC ที่ ModuleA แล้วเรา Import ModuleA ใน AppModule แต่ปรากฏว่าใน Template เราไม่ได้มีการใช้ (Reference) สิ่งเหล่านั้น Angular Compiler จะถือว่าเราไม่ได้ใช้ จึงไม่รวมเข้าไปใน Bundle เพื่อทำให้มันเล็กลง ยกตัวอย่างเช่นกรณีของ Custom MatModule (Material Design Module) ที่ declarations และ exports ทุกอย่าง ถึงเราจะ import MatButtonModule, MatCardModule เข้าแต่เราไม่ได้ใช้ ใน Template ก็ไม่ต้องจ่าย Cost ของขนาด Bundle ที่ใหญ่ขึ้น

```typescript
@NgModule({
  imports: [
    MatToolbarModule,
    MatMenuModule,
    ...
  ],
  exports: [
    MatToolbarModule,
    MatMenuModule,
    ...
  ], // ถึงแม้จะ export ออกไป ถ้าไม่ได้ใช้ก็ไม่ต้องจ่าย Bundle Size
  declarations: []
})
export class CustomMatModule { }
```

</br></br></br>
**bootstrap**

ระบุ Component ที่เอาไว้สร้างตอนเริ่มรัน App (Bootstrap) โดยปกติคือ Root Component ซึ่งก็คือ AppComponent
Bootstrap Component นั้นจะถูกเพิ่มเข้าไปใน entryComponents โดยอัตโนมัติ

ตัวอย่างที่ชัดเจนเลยคือ AppModule

```typescript
@NgModule({
  declarations: [AppComponent],
  imports: [BrowserModule],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule {}
```

</br></br></br>
**schemas**

ระบุ Schema ที่อนุญาตให้ใช้ใน Scope ของ NgModule โดยมีสองค่าที่ใช้ได้คือ [NO_ERRORS_SCHEMA](https://github.com/angular/angular/blob/master/packages/core/src/metadata/schema.ts#L38) และ [CUSTOM_ELEMENTS_SCHEMA](https://github.com/angular/angular/blob/master/packages/core/src/metadata/schema.ts#L29)

**NO_ERRORS_SCHEMA - บอก Angular Compiler ว่าอนุญาตทุก Element ทุก Property เป็นอะไรก็ได้**

สำหรับการทำ [Shallow Testing](https://vsavkin.com/three-ways-to-test-angular-2-components-dcea8e90bd8d) เราต้องการจะ Test Angular Template โดยละ Dependencies ไว้ในฐานที่เข้าใจ ของ Component (ไม่ต้องสนใจ Template Error ว่า Require อะไร)
เราสามารถ Configure Module เพื่อบอก Angular Component บางตัวที่เราสร้างให้คิดซะว่าเป็นแค่ DOM ธรรมดา

ตัวอย่างด้านล่างคือมี Component ชื่อ ConversationsCmp ที่เราต้องการเทสว่า มัน Render ข้อความข้างในถูกต้องซึ่งใน template นั้นใช้ `<mat-card></mat-card>` หรือ MatCardComponent  ซึ่งเราไม่ได้สนใจหน้าตาความสวยงามของมัน เราต้องการเช็คว่ามีข้อความข้างในและถูกต้องไหม

ตอนสร้าง TestBed เลย Configure Module โดยใส่ schemas `NO_ERRORS_SCHEMA` ไม่ต้องฟ้อง Error จากการไม่ import `MatCardModule` ให้ Angular Compiler เข้าใจ Scope ของ `MatCardComponent` ด้วย แล้วก็รันเทสตามปกติ

```html
<mat-card *ngFor="let c of conversations | async" [routerLink]="[c.id]">
  <h3>
    <a [routerLink]="[c.id]">{{c.title}}</a>
  </h3>
  <p>
    <span class="light">{{c.user.name}} [{{c.user.email}}]</span>
  </p>
</mat-card>
```

```typescript
@Component({templateUrl: 'conversations.html'})
export class ConversationsCmp {
  folder: Observable<string>;
  conversations: Observable<Conversation[]>;

  constructor(route: ActivatedRoute) {
    this.folder = route.params.pluck<string>('folder');
    this.conversations = route.data.pluck<Conversation[]>('conversations');
  }
}
```

```typescript
describe('ConversationsCmp', () => {
  let params: BehaviorSubject<string>;
  let data: BehaviorSubject<any>;

  beforeEach(async(() => {
    params = of({
      folder: 'inbox'
    });

    data = of({
      conversations: [
        { id: 1, title: 'On the Genealogy of Morals by Nietzsche', user: {name: 'Kate', email: 'katez@example.com'} },
        { id: 2, title: 'Ethics by Spinoza', user: {name: 'Corin', email: 'corin@example.com'} }
      ]
    });

    TestBed.configureTestingModule({
      declarations: [ConversationsCmp],
      providers: [
        { provide: ActivatedRoute, useValue: {params, data} }
      ],
      // Tells the compiler not to error on unknown elements and attributes
      schemas: [NO_ERRORS_SCHEMA]
    });
    TestBed.compileComponents();
  }));

  it('updates the list of conversations', () => {
    const f = TestBed.createComponent(ConversationsCmp);
    f.detectChanges();

    expect(f.debugElement.nativeElement).toHaveText('inbox');
    expect(f.debugElement.nativeElement).toHaveText('On the Genealogy of Morals');
    expect(f.debugElement.nativeElement).toHaveText('Ethics');

    params.next({
      folder: 'drafts'
    });

    data.next({
      conversations: [
        { id: 3, title: 'Fear and Trembling by Kierkegaard', user: {name: 'Someone Else', email: 'someonelse@example.com'} }
      ]
    });
    f.detectChanges();

    expect(f.debugElement.nativeElement).toHaveText('drafts');
    expect(f.debugElement.nativeElement).toHaveText('Fear and Trembling');
  });
});
```

**CUSTOM_ELEMENTS_SCHEMA - บอก Angular Compiler ว่าอนุญาต Non-Angular Elements และ Properties ด้วย Dash Case ซึ่ง Dash Case เป็น Convention ของ Custom Elements**

โดยปกติแล้ว Angular จะเข้าใจว่า Custom HTML Tag เป็น Angular Component หมดเวลาเจออะไรแปลกๆไม่รู้จักใน Scope ก็เด้ง error ในกรณีที่เราต้องการใช้ Custom Element เลยต้องบอกว่า Angular Compiler เราจะใช้ Custom Element นะไม่ต้อง งง จากนั้นมันจะ Compile ผ่าน

```typescript
import { BrowserModule } from '@angular/platform-browser';
import { NgModule, CUSTOM_ELEMENTS_SCHEMA } from '@angular/core';
import { ReactiveFormsModule } from '@angular/forms';

import { AppComponent } from './app.component';

@NgModule({
  declarations: [AppComponent],
  schemas: [CUSTOM_ELEMENTS_SCHEMA] // <-- บอกตรงนี้,
  imports: [BrowserModule, ReactiveFormsModule],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule {}
```

[ศึกษาเรื่อง Custom Element ใน Angular ต่อ](https://vaadin.com/tutorials/using-web-components-in-angular)

## สรุป NgModule

1. อธิบายวิธีการ Compile Components, Templates, Directives, Pipes ให้กับ Angular Compiler
2. ระบุ Components, Directives, Pipes ให้เป็นสาธารณะ (Public) ผ่าน metadata ที่มีชื่อว่า **exports** เพื่อให้ Module อื่น ที่ import Module นี้เรียกใช้งานได้
3. เพิ่ม Services หรือ Providers เพื่อใช้ Dependency Injection ใน Component ได้

ก่อนจบบทความนี้ไป ผมมีปริศนาฟ้าแลบ ⚡ ให้ทุกท่านทาย คำถามมีอยู่ว่า

ตอนนี้ Angular App 🅰️ ของเราได้ทำการแบ่ง Feature Module ไว้เรียบร้อยแล้ว

CEO 😎 ต้องการให้ Angular App ทำให้ช่องกรอกเบอร์โทรศัพท์ 📞 mask เบอร์จาก 0999999999 เป็น 099-999-999 **เหมือนกันทุกช่อง input ใน App**

พวกเราเหล่า Developers 👨💻 จึง Search หา Library ใน Google แล้วพบตัวนึงที่มีชื่อว่า [ngx-mask](https://github.com/JsDaddy/ngx-mask)

โอเคเรา `npm install --save ngx-mask` เรียบร้อย

ใน ngx-mask github ได้แนะนำวิธีการ import Module ไว้ด้านล่าง

```typescript
import { NgxMaskModule } from 'ngx-mask'

export const options: Partial<IConfig> | (() => Partial<IConfig>);

@NgModule({
  (...)
  imports: [
    NgxMaskModule.forRoot(options)
  ]
  (...)
})
```

คำถามคือเราจะต้อง import `NgxMaskModule` ยังไงและที่ไหนบ้าง?

1. import NgxMaskModule.forRoot(options) ที่ AppModule ที่เดียวจบใช้ได้ทั้ง App
2. import NgxMaskModule.forRoot(options) ที่ AppModule และ ทุก Feature Module ที่เราแบ่งไว้
3. import NgxMaskModule.forRoot(options) ที่ AppModule และ import NgxMaskModule ทุก Feature Module
4. import NgxMaskModule ที่ AppModule และ ทุก Featured Module
5. import NgxMaskModule ที่ AppModule และ import NgxMaskModule.forRoot(options) ทุก Feature Module
6. import NgxMaskModule ที่ AppModule ที่เดียวจบใช้ได้ทั้ง App

แล้วถึงสามารถใช้โค๊ดด้านล่างได้

```html
<input type="text" mask="000-000-000">
```

```html
<span>{{phone | mask: '000-000-0000'}}</span>
```

แล้วจะมาเฉลยในบทความต่อไปของ NgModule 😈

จบไปแล้วสำหรับ NgModule เบื้องต้น 🔚 ยังมีอีกหลายเรื่องใน NgModule ที่คุยกันต่อได้เช่น 💬

- ลำดับการสร้างตอน Bootstrap App ของ NgModule
- Feature Module คืออะไรกันนะ? 🤔
- ประเภทของ Feature Module
- Lazy-Loading Module คืออะไรกัน ❓
- NgModule พื้นฐานเช่น RouterModule, HttpClientModule, FormsModule

ฝากแชร์ 🔗 ต่อให้เพื่อนพี่น้อง ชาว Angular ได้อัพเดทกัน

แล้วเจอกันบทความหน้า 👋 สวัสดีครับ 🙏

<a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/"><img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-nc-sa/4.0/88x31.png" /></a><br />This article uses<a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/"> Attribution-NonCommercial-ShareAlike 4.0 International (CC BY-NC-SA 4.0)</a>.
