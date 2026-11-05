# ASTRO TILT: Meteor Defense

เกมยิงอุกกาบาตแนวตั้ง Offline สำหรับ Android/iOS ด้วย Expo SDK 58, React Native และ TypeScript

## เริ่มรัน

ใช้ Node.js 22.13+ (ตรวจงานด้วย Node 24) และ npm ตาม package-lock.json:

```sh
npm ci
npx expo start
```

โทรศัพท์และคอมพิวเตอร์ต้องอยู่เครือข่ายเดียวกัน เปิด QR ด้วย Expo Go ที่รองรับ **SDK 58**: Android สแกนใน Expo Go; iOS สแกนด้วย Camera แล้วเปิด Expo Go หากเวอร์ชันในร้านค้ายังไม่รองรับ SDK 58 จะเปิดโปรเจกต์นี้ไม่ได้ ต้องใช้ Expo Go รุ่นที่ตรงกันหรือ development build ภายหลัง ไม่ควรลด SDK โดยไม่ตรวจ dependencies ทั้งชุด

เอกสาร Expo ณ วันที่ตรวจงานยังมีคำเตือนช่วงเปลี่ยน SDK 58 จึงยังไม่ยืนยันความเข้ากันได้กับ Expo Go ที่ติดตั้งบนโทรศัพท์ของผู้ใช้ ดู [Expo Router / SDK transition](https://docs.expo.dev/router/introduction/) และ [ตั้งค่าสภาพแวดล้อม](https://docs.expo.dev/get-started/set-up-your-environment/)

ทดสอบบนคอมพิวเตอร์:

```sh
npm run web
```

เปิด Settings → Touch Control (สำหรับทดสอบ) แล้วใช้ปุ่มซ้าย–ขวาและ FIRE เว็บใช้ CanvasKit ที่บรรจุใน public/canvaskit.wasm ไม่ดึง CDN ระหว่างเล่น ทั้งนี้เว็บเดโมยังไม่มี service worker จึงไม่รับประกันการโหลดหน้าเว็บแบบ offline; ตัวเกม native ไม่มี backend หรือ API ภายนอก

## วิธีเล่น

- เลือกยานจริง 4 แบบใน Main Menu Astro-01 HP 3/ความเร็ว 310, Striker HP 2/310 ยิงถี่ขึ้น 25%, Tank HP 5/220, Interceptor HP 2/420
- Start Game → ถือเครื่องแนวตั้งในท่าที่สบาย → ตั้งมุมกลางและเริ่มเกม
- เอียงซ้าย/ขวาเพื่อเคลื่อนที่ กลับท่ากลางเพื่อชะลอ ความไวและกลับทิศปรับได้ใน Settings
- ยิงอัตโนมัติเปิดเป็นค่าเริ่มต้น ปิดเพื่อแตะ FIRE ยิงสั้นเอง; ยิงปกติครั้งละ 2 นัด กดค้าง 0.22 วินาทีเริ่มลำแสง 2 แนว สูงสุด 3 วินาที พัก 2 วินาที (Striker 1 วินาที); ปล่อยลำแสงก่อนครบก็พักเต็มเวลา ระหว่างพักยังยิงกระสุนได้
- ลำแสงโดนเป้าหมายใกล้สุดทุก 0.25 วินาที (Striker 0.20 วินาที) Quad Laser เพิ่มเป็นสี่แนว ทั้งกระสุนและลำแสง
- อุกกาบาตเล็ก HP 4/+10, กลาง HP 7/+20, ใหญ่ HP 10/+30
- เหรียญ +10, Shield 5 วินาที, Quad Laser 8 วินาที, Repair +1 HP ไม่เกิน HP สูงสุดของยาน
- ด่าน EARTH ORBIT และ METEOR STORM ต้องรอดด่านละ 75 วินาที ส่วน FINAL IMPACT มีไฟแดง BOSS เตือน 3 วินาทีก่อนบอส HP 80/+300 ปรากฏหลัง 60 วินาที; ยิงปกติ 3 ลูก และชุดพิเศษ 7 ลูกทุก 10 วินาที ชนะแล้วเข้าสู่เครดิตอังกฤษก่อนสรุปคะแนน เมื่อเลื่อนจบมีปุ่ม Thai language เพื่ออ่านเครดิตภาษาไทย
- ชนเสีย 1 HP และกันความเสียหายซ้ำ 1.2 วินาที อุกกาบาตหลุดจอไม่ลด HP
- Pause มี Resume, Restart, Settings, Recalibrate และ Main Menu; เวลาเกม/ไอเทมหยุดขณะพัก
- แอปออก background จะพักอัตโนมัติ ไม่ Resume เอง; ต้องกด Resume (อาจต้องปรับเทียบใหม่เมื่อข้อมูลเก่า)
- High Score และ Settings เก็บใน AsyncStorage; Restart ไม่ลบสถิติ

## เซนเซอร์และการทดสอบบนโทรศัพท์

ใช้ DeviceMotion เป็นหลัก อ่าน lateral acceleration including gravity หน่วย m/s² หารด้วย Gravity เพื่อได้ค่า g ไม่ใช้ angular velocity เป็นมุม; ใช้ Accelerometer เป็น fallback หน่วย g มี smoothing แบบ exponential, dead zone, sensitivity, inversion และปรับเทียบ offset

ตรวจโค้ด Expo Sensors SDK 58 แล้ว: DeviceMotion ของ Android ปรับ convention ให้ตรงกับ iOS อยู่แล้ว ส่วน Accelerometer แบบดิบของ Android ต้องกลับเครื่องหมาย X เมื่อเทียบ iOS. การตรวจโค้ดนี้ **ไม่ใช่การทดสอบอุปกรณ์จริง** มี Sensor Debug แสดง source/raw/neutral/axis เพื่อใช้ตรวจจริง

1. Android และ iOS: ยอมรับ motion permission เมื่อระบบถาม ปิด Touch Control
2. ถือโทรศัพท์ในท่าเล่นและตั้งมุมกลาง ยานควรหยุดใกล้ตำแหน่งเดิม เอียงซ้าย/ขวาแล้วยานไปทิศเดียวกัน
3. ทดสอบมุมตื้น/ลึก ปรับ sensitivity และกลับทิศ หากตำแหน่งถือเปลี่ยนให้ Pause → Recalibrate
4. ปิดยิงอัตโนมัติ แตะ FIRE แล้วปล่อย ต้องออกกระสุนสั้น; กดค้างต้องได้ลำแสงและหยุดทันทีเมื่อปล่อย
5. ทดสอบ Pause/Resume/Restart หลายรอบ กด Home/ล็อกจอ/กลับแอป ตรวจว่าไม่ยิงค้าง ไม่เคลื่อนกระโดด และเวลา Shield/Double ไม่ลดระหว่างพัก
6. ทดสอบไม่มีสิทธิ์/ไม่มีเซนเซอร์: ต้องมีข้อความแจ้ง และผู้เล่นเลือก Touch Control เอง ไม่สลับโดยเงียบ
7. ตรวจ Game Over, Victory, คะแนนสูงสุดหลังปิดเปิดแอป, safe areas และ multi-touch (เคลื่อนพร้อมยิง)
8. วัด FPS และหน่วยความจำบนเครื่องจริงก่อนอ้างว่าได้ 60 FPS

## โครงสร้างสำคัญ

- src/app/: Splash, Menu, How to Play, Settings, Gameplay และผลแพ้/ชนะ (Expo Router)
- src/game/config.ts: กติกา HP ความเร็ว คะแนน cooldown และเป้าหมายด่าน
- src/game/engine.ts: pure simulation, collision, stage/boss, pickups, pause/start/stop/reset ด้วย Game instance ใหม่
- src/hooks/useTilt.ts: permission, fallback, calibration, smoothing, stale-data watchdog, cleanup
- src/components/GameFieldNative.tsx: Skia renderer ใช้ Picture/shared value โดยไม่ setState วัตถุทั้งเกมทุกเฟรม
- src/components/GameField.web.tsx: client-only CanvasKit loader ป้องกัน server-rendering crash
- src/state/preferences.tsx: validate/โหลด/บันทึก preferences และ high score พร้อมลำดับการเขียน
- tests/engine.test.cjs: unit tests ใช้ Node test runner และ TypeScript ที่มีอยู่ ไม่เพิ่ม test framework

Simulation ใช้ requestAnimationFrame และ delta time จำกัดที่ 50ms; UI สถานะอัปเดตประมาณ 10Hz. จำกัด meteors 32, bullets 70, particles 160, pickups 8 และล้างวัตถุหมดอายุ กระสุนตรวจ swept segment กันยิงทะลุเป้าหมาย เฟรมวาดใช้ Skia picture ใหม่และให้ runtime จัดการอายุ resource จึงยังควร profile บนอุปกรณ์จริง

## Asset ที่ใช้จริง

- assets/backgrounds/: main-menu.png, earth-space.png, red-nebula.png, boss-nebula.png
- assets/sprites/: player-spaceship.png, stealth-phantom.png, heavy-defender.png, interceptor-green.png
- meteor-small.png, meteor-medium.png, meteor-large.png, meteor-boss.png
- score-coin.png, powerup-shield.png, powerup-double-laser.png, powerup-health.png
- กระสุน ลำแสง เปลวไฟ ดาวเลื่อน hit flash ระเบิด shield overlay และ +10 วาดด้วยโค้ด Skia
- asset_game/ เก็บต้นฉบับเดิมไว้ ไม่แก้หรือลบ
- เพลงใน Music/ เล่นตามหน้าเมนู ด่าน และเครดิต ส่วน Effects/ ใช้สำหรับเสียงยิงและระเบิด เปิดหรือปิดเพลงและเอฟเฟกต์ได้แยกกันใน Settings
- app icon และ launch splash ยังใช้ภาพเดิมของโปรเจกต์; Splash ภายในแอปใช้โลโก้ข้อความ ASTRO TILT ก่อนเข้าสู่เมนู

## ตรวจงาน

```sh
npx expo lint
npx tsc --noEmit
npm test
npx expo install --check
npx expo-doctor
npx expo export --platform android --platform ios --output-dir dist-mobile
```

export ตรวจว่ารวมโค้ดและ asset ได้ ไม่ใช่ APK/IPA และไม่ใช่การรันบนอุปกรณ์. โฟลเดอร์ example/ เป็น starter เก่าที่เก็บไว้ จึงแยกออกจาก typecheck ของแอปจริง

ผลการตรวจและข้อจำกัดล่าสุดดู docs/TESTING.md

## Android APK ด้วย EAS

กำหนด Android application ID เป็น `com.phoow.astrotilt` และเตรียม profile `preview` ใน eas.json สำหรับ APK ติดตั้งได้โดยตรง

```sh
npx eas-cli@latest build -p android --profile preview
```

ใช้บัญชี Expo ที่เชื่อมกับโปรเจกต์ @phoowadul/astro_tilt และเก็บ signing key บน EAS เพื่อใช้ลงนามอัปเดตครั้งถัดไป

หากต้องใช้ development build ให้ติดตั้ง npx expo install expo-dev-client และตั้ง developmentClient: true แยก profile ก่อนสร้าง. Native modules หลักที่ใช้มีใน Expo Go รุ่นที่ตรง SDK ตาม [Skia SDK 58](https://docs.expo.dev/versions/v58.0.0/sdk/skia/) แต่การเพิ่ม native module ที่ Expo Go ไม่บรรจุต้องสร้าง development build ใหม่

อ่าน [EAS APK](https://docs.expo.dev/build-reference/apk/) และ [DeviceMotion SDK 58](https://docs.expo.dev/versions/v58.0.0/sdk/devicemotion/) ก่อนเปลี่ยน config. ไม่สร้างหรือแก้ ios/ และ android/ ด้วยมือ

## Ultimate และ Overdrive

- Energy Cell สีฟ้าตกทุก 8–12 วินาที เก็บครบ 5 แล้วกด ULTIMATE เอง ยิงพัดด้านหน้า 10 นัด นัดละ 10 ดาเมจต่ออุกกาบาต (แตกทุกขนาดในนัดเดียว) และ 3 ดาเมจต่อบอส พร้อมเสียง Ultimate เฉพาะ เกจกลับเป็น 0
- Upgrade Part สีทองดรอป 20% จากอุกกาบาตที่ยิงทำลาย เก็บครบ 3 แปลงร่าง 15 วินาที ยิงเร็วขึ้น 50% ทั้งกระสุนและจังหวะลำแสง และมีโล่รับชนหนึ่งครั้ง ไม่เปลี่ยน HP/ความเร็วเคลื่อนที่
- Overdrive คงยิง 2/4 แนว เก็บครบอีก 3 รีเฟรชเวลาและโล่โดยไม่สะสม; โล่จับเวลาและช่วงอมตะป้องกันก่อนโล่ครั้งเดียว
- เกจและชิ้นส่วนคงอยู่ข้ามด่าน เวลาแปลงร่างหยุดระหว่าง Pause/transition และ Restart ล้างพลังทั้งหมด
- ปุ่ม Ultimate อยู่ข้าง FIRE แยกแถวจาก Touch arrows รองรับการควบคุมพร้อมกัน; ทดสอบบนมือถือจริงเพิ่มเติมก่อนเผยแพร่ APK รุ่นใหม่
