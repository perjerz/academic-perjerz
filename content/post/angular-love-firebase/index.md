---
title: Recap Keynote Angular Connect Day 1
date: 2019-09-20
lastmod: 2019-09-22
image:
  placement: 3
  caption: 'Image credit: [**@ankitsharma_007**](https://twitter.com/ankitsharma_007/status/1174607831263252480)'
draft: false
highlight: false
---

## Angular ❤ Firebase

🅰️ ❤️ 🔥

Angular นั้นรัก Firebase อย่างกับพี่น้องเพราะมาจากพ่อแม่เดียวกันนั้นคือ Google 𝗚

สวัสดีครับผมเจมส์จาก Angular Thailand วันนี้จะมาเล่าวิธีการใช้ Library ตัวหนึ่งที่มีชื่อว่า [AngularFire](https://github.com/angular/angularfire) 🅰️🔥 ซึ่งเป็น Official Library จากทีม Angular ดูแลร่วมกับ Open Source Maintainer ท่านอื่นๆ

โดยเราจะสร้างโปรเจคเล็กๆ Live Feed โดยที่ผู้ใช้ที่ลงทะเบียนสามารถโพสรูปพร้อมข้อความและกดไลค์หัวใจ

เรามาเริ่มกันเลยดีกว่า

ขั้นแรกลง @angular/cli version 9.0.0-rc.0

```
npm install -g @angular/cli@next
```

[Install Angular](./angular-next.png)

สร้างโปรเจคด้วย ng new ไม่ใส่ Route เลือก style เป็น scss

```
ng new firebase ivy live-feed --routing=false --style=scss
```

[Angular New Project](./ng-new.jp2)

ลง Angular​ Material ด้วยคำสั่ง

```shell
ng add @angular/material
```

 โดย schematics จะมี x-prompt ถามเราให้เลือก

- Theme ผมเลือก Custom Theme เพราะ Prebuilt Theme Pallete สีเหลือง
- HammerJS ใช้สำหรับจับ Gesture ซึ่งถูกใช้ในบาง Component เช่น mat-slide-toggle, mat-slider, matToolTip โดยผมตอบ No ไม่ได้ใช้
- Import BrowserAnimationModule สำหรับ Angular Animation ซึ่งส่วนใหญ่ใช้ใน Angular Material โดยผมตอบ No เช่นกัน

นอกจากนี้ ng add ยังอำนวยความสะดวกให้เราด้วยการ

- เพิ่ม dependency @angular/cdk @angular/material ใน package.json
- ใส่ Font Roboto ให้ใน index.html (display: swap ให้ด้วย)
- ใส่ Material Design Icon ให้ใน index.html
- เพิ่ม global css style ลบ margin จาก body ออก, height: 100% ให้กับ html, body และตั้ง Roboto Font เป็น Default
อะไรมันจะสะดวก สบายเยี่ยงนี้ 😎

[ที่มา](https://material.angular.io/guide/getting-started#install-angular-material)

[Angular Material](./angular-material.jpg)

ต่อไปลง AngularFire ด้วยคำสั่ง

```shell
ng add @angular/fire
```

- Authenticatation
- Firestore
- Realtime
- Upload Fire
- Push Notification
- Direct Call Cloud Functions
- ng deploy
https://github.com/angular/angularfire/blob/master/docs/deploy/getting-started.md


จบแล้ว เป็นไงกันบ้างสำหรับการอัพเดทครั้งนี้ ฝากแชร์ต่อให้เพื่อนพี่น้อง ชาว Angular ได้อัพเดทกัน

แล้วเจอกันที่งาน Firebase Dev Day วันที่ 9 พฤศจิกายน 2019 ที่ Academic Bangkok, True Digital Park

[Firebase Dev Day](./index.md)
https://dev.wi.th/event/firebasedevday2019

<a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/"><img alt="สัญญาอนุญาตของครีเอทีฟคอมมอนส์" style="border-width:0" src="https://i.creativecommons.org/l/by-nc-sa/4.0/88x31.png" /></a><br />บทความนี้ใช้<a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/">สัญญาอนุญาตของครีเอทีฟคอมมอนส์แบบ แสดงที่มา-ไม่ใช้เพื่อการค้า-อนุญาตแบบเดียวกัน 4.0 International</a>.
