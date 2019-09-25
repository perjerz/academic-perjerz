---
title: What is NgModule?
date: 2019-09-12
lastmod: 2019-09-13
diagram: true
image:
  placement: 3
  caption: 'Image credit: [**angular.io**](https://angular.io/guide/architecture-modules#angular-libraries)'
draft: false
highlight: true
highlight_languages: ["typescript"]  # Add support for highlighting additional languages
---

## อะไรคือ NgModule กันนะ

สวัสดีครับขอต้อนรับเข้าสู่ Angular Fundamental Series

ที่มาของบทความนี้เกิดจากผมได้มีโอกาสเข้าไปเขียนโค๊ดกับรุ่นน้องคนหนึ่ง ผมเห็น Error บน Chrome Dev Tools แจ้งประมาณว่า

```typescript
Error: Template parse errors:
'mat-tab' is not a known element:
1. If 'mat-tab' is an Angular component, then verify that it is part of this module.
2. If 'mat-tab' is a Web Component then add 'CUSTOM_ELEMENTS_SCHEMA' to the '@NgModule.schemas' of this component to suppress this message.
```

ผมบอกน้องไปว่า "น้องลืมใส่ import MatTabModule ใน Module ที่น้องกำลังใช้อยู่หรือเปล่า"

น้องคนนั้นก็เปิดไฟล์ Module นั้นแล้วก็พบว่า ... ยังไม่ได้ใส่ MatTabModule เข้าไปที่ "imports" ใน "metadata" ของ **NgModule**

หลังจากน้องทำการแก้ไขตามที่ผมบอก ระบบทำงานได้ตามปกติ

หลังจากนั้นผมเก็บความผิดพลาดนี้ไว้ แล้วจึงไปถามรุ่นพี่ใช้ Angular อยู่เป็นประจำว่า "เอาจริงๆพี่เข้าใจ **NgModule** ไหม"

พี่เขาตอบกลับมาว่า "ทุกวันนี้พี่ยังไม่เข้าใจมันเลยน้องเอ้ย"

จึงเกิดเป็นบทความนี้เพื่ออธิบาย **NgModule** ทั้งภาพกว้างและเชึงลึก

มาเริ่มกันเลยดีกว่า

## NgModule - Unit of Compilation & Distribution

NgModules คือ JavaScript Class ที่ถูกเพิ่มความสามารถด้วย Decorator ที่มีชื่อว่า **@NgModule** โดยที่ **@NgModule** รับ metadata object

```typescript
import { BrowserModule } from '@angular/platform-browser';
import { NgModule } from '@angular/core';
import { AppComponent } from './app.component';

// @NgModule decorator พร้อมด้วย metadata object (declarations, imports, ...)
@NgModule({
  declarations: [AppComponent],
  imports: [BrowserModule],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule {}
```
<!-- **ผมเรียกพวกนี้ว่าพิธีกรรมซึ่งมีเหตุผลข้อดีข้อเสียของมัน แต่ละ Frameworks และ Libraries มีพิธีกรรมไม่เหมือนกัน** -->

สิ่งสำคัญคือ **NgModule** มีไว้เพื่ออะไร
NgModule ใช้ metadata

1. อธิบายวิธีการ Compile Components, Templates, Directives, Pipes ให้กับ Angular Compiler
2. ระบุ Components, Directives, Pipes ให้เป็นสาธารณะ (Public) ผ่าน metadata ที่มีชื่อว่า **exports** เพื่อให้ Module อื่น ที่ import Module นี้เรียกใช้งานได้
3. เพิ่ม Services หรือ Providers เพื่อใช้ Dependency Injection ใน Component ได้

เรามาดูเคส 1, 2 ผ่านตัวอย่างด้านล่างนี้กันดีกว่า

```typescript
import { FlexLayoutModule } from '@angular/flex-layout';
import { BrowserAnimationsModule } from '@angular/platform-browser/animations';
import { MatButtonModule } from '@angular/material/button';
import { MatCardModule } from '@angular/material/card';
import { MatIconModule } from '@angular/material/icon';
import { MatProgressSpinnerModule } from '@angular/material/progress-spinner';
import { CompanyListComponent } from './company-list/company-list.component';
import { TechToIconPipe } from './company-card/tech-to-icon.pipe';
import { CompanyCardComponent } from './company-card/company-card.component';
import { NgModule } from '@angular/core';
import { CommonModule } from '@angular/common';

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

ในตัวอย่างนี้มี metadata ที่น่าสนใจคือ imports, declarations, exports

แต่จะขอเล่าจาก exports, imports, declarations ตามลำดับ

</br></br></br>
**exports**

ใช้ระบุ Components, Directives, Pipes เพื่อส่งออกให้เรียกใช้ใน Scope นั้นๆได้

ความหมายคือหาก ModuleA import ModuleB นี้เข้าไป ทุกสิ่งที่ exports ใน ModuleB จะไปอยู่ใน Scope ของการ Compile ModuleA

ทำให้ ModuleA รู้จักและสามารถเรียกใช้สิ่งที่ ModuleB export ได้

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

ตัวอย่าง **CompanyListComponent** ถูก exports ใน **CompanyModule** และเมื่อ **AppModule** import **CompanyModule** เข้าไป

```typescript
import { CompanyModule } from './company/company.module';
...

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

[AppModule ฉบับเต็ม](https://github.com/AngularThailand/who-use-angular-in-thailand/blob/master/apps/who-use-angular-in-thailand/src/app/app.module.ts)


**AppComponent** ที่อยู่ใน declarations จึงรู้จักและสามารถเรียกใช้ **CompanyListComponent** ใน Template ได้

![Export CompanyModule](./exports-company-module.png)

ด้านล่างเป็นตัวอย่างของการที่ **AppComponent** Template (app.component.html) เรียกใช้ `<angular-th-company-list></angular-th-company-list>` ซึ่งก็คือ selector ใน **CompanyListComponent**

```html
...
<angular-th-company-list [companies]="companies$ | async" [loaded]="loaded"></angular-th-company-list>
...
```

ซึ่งเรียกใช้ได้ตามปกติเพราะรู้จักแล้ว

[app.component.html ฉบับเต็ม](https://github.com/AngularThailand/who-use-angular-in-thailand/blob/master/apps/who-use-angular-in-thailand/src/app/app.component.html)

</br></br></br>
**imports**

ใช้สำหรับ imports Module อื่นๆ เพื่อใช้สิ่งที่ Module นั้น exports ออกมาไม่ว่าจะเป็น Components, Pipes, Directives

รวมถึง Register Providers ที่ Module นี้ไว้สำหรับใช้ตอน Run-time

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

จากตัวอย่างโค๊ดด้านบน
**CompanyCardComponent** (company-card.component.html) ได้ใช้ `<mat-card></mat-card>`
และ Directive fxLayoutAlign `<div fxLayoutAlign="center center">`

[company-card.component.html ฉบับเต็ม](https://github.com/AngularThailand/who-use-angular-in-thailand/blob/master/apps/who-use-angular-in-thailand/src/app/company/company-card/company-card.component.html#L1)

การที่จะใช้สิ่งเหล่านี้ เราต้องบอก Angular Compiler ว่าเราใช้ **MatCardComponent**, **FxLayoutAlignDirective**
ซึ่งใน **MatCardModule**, **FxLayoutModule** ได้ exports ไว้แล้ว
ดังนั้นเราแค่ import **MatCardModule** และ **FlexLayoutModule** ก็สามารถใช้ได้

ทุกท่านอาจจะสงสัยว่า **CommonModule** มีไว้ทำอะไร
**CommonModule** นั้นมีความสำคัญมาก
**CommonModule** ทำให้เราสามารถใช้ `*ngIf, *ngFor, [ngClass], AsyncPipe (| async), CurrencyPipe (| currency)` และอื่นๆ เพราะตัวมันเอง exports สิ่งเหล่านี้ให้เราใช้

[อ่าน CommonModule เพิ่มเติม](https://angular.io/api/common/CommonModule)

Components อื่นๆเช่น **MatButton, BrowserAnimationsModule, MatIcon, MatProgressBar** ใช้หลักการเหมือนกับข้างบน

**FormsModule, ReactiveFormsModule** ถ้าจะใช้ต้อง import เข้ามาเช่นกัน
![Import CompanyModule](./imports-company-module.png)

</br></br></br>
**declarations**

ระบุ Component, Directive, Pipe ทั้งหมดเพื่อบอก Angular Compiler ว่าใช้ในการ Compile **เฉพาะใน Scope ของ Module นี้เท่านั้น**

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

จากตัวอย่างโค๊ดด้านบน สิ่งที่มันอธิบายคือ
Component (**CompanyCardComponent, CompanyListComponent**) และ Pipes (**TechToIconPipe**) รู้จักกันสามารถเรียกใช้งานระหว่างกันได้
**CompanyCardComponent** เรียกใช้ **TechToIconPipe** ใน Template

```html
...
<img *ngIf="tech | techToIcon as icon; else techText"/>
...
```

[company-card.component.html ฉบับเต็ม](https://github.com/AngularThailand/who-use-angular-in-thailand/blob/master/apps/who-use-angular-in-thailand/src/app/company/company-card/company-card.component.html#L24)

**CompanyListComponent** วนลูปสร้าง `<company-card></company-card>` ด้วย `*ngFor` ใน Template

```html
...
<angular-th-company-card *ngFor="let company of companies" [company]="company"></angular-th-company-card>
...
```

[company-list.component.html ฉบับเต็ม](https://github.com/AngularThailand/who-use-angular-in-thailand/blob/master/apps/who-use-angular-in-thailand/src/app/company/company-list/company-list.component.html#L3)

![Declaration Company Module](./declarations-company-module.png)

**providers**

**entryComponents**

**bootstrap**


## Ivy Spec
## forRoot, forFeature, Module with Provider
## NgModules Constructor Order
## CommonModule, BrowserModule, RouterModule, SharedModule
## Best Practices of NgModules
## Angular ในยุคที่ไม่มี NgModules

<a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/"><img alt="สัญญาอนุญาตของครีเอทีฟคอมมอนส์" style="border-width:0" src="https://i.creativecommons.org/l/by-nc-sa/4.0/88x31.png" /></a><br />บทความนี้ใช้<a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/">สัญญาอนุญาตของครีเอทีฟคอมมอนส์แบบ แสดงที่มา-ไม่ใช้เพื่อการค้า-อนุญาตแบบเดียวกัน 4.0 International</a>.
