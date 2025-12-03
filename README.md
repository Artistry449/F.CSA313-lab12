# Лабораторийн ажил №12: Төгсгөлөг төлөвийн машин — Лифтний хяналтын систем

<div align="center">

**Шинжлэх Ухаан, Технологийн Их Сургууль**  
**Мэдээлэл, Холбооны Технологийн Сургууль**

<br><br><br>

**F.CSA313 Программ хангамжийн чанарын баталгаа ба туршилт**

<br><br><br><br><br>

# **ЛАБОРАТОРИЙН АЖИЛ №12**

<br><br><br><br><br>

**Шалгасан багш:** __________________ /__________________/  
**Гүйцэтгэсэн оюутан:** __________________ /__________________/

<br><br><br>

**Улаанбаатар хот**  
**2025 он**

</div>

## Зорилго

Лифтний хяналтын системийг **төгсгөлөг төлөвийн машин (Finite State Machine, FSM)** хэлбэрээр загварчилж, төлөв болон шилжилтүүдийн, мөн системийн аюулгүй байдал ба амьдралтай байдлын шинжүүдийн зөв байдлыг шинжилж шалгах. Энэхүү лабораторийн ажлын хүрээнд:

- Лифтний боломжит төлөвүүд, шилжилтүүдийг тодорхойлно.  
- Төлөв шилжилт үүсгэх **үзэгдэл (event)**, **үйлдэл (action)**-уудыг тодорхойлно.  
- FSM-ийн диаграмм (ASCII/mermaid/draw.io-д зурахад бэлэн) бэлдэнэ.  
- Системийн **аюулгүй (safety)** ба **амьдралтай (liveness)** шинжүүдийг тодорхойлно.  
- FSM дээр үндэслэн тест кейс, сценари боловсруулж бүрэн шалгана.

---

## Системийн товч тодорхойлолт

### Давхарын загвар

Лифт дараах 3 давхарт ажиллана:

- **B** — Зоорьны давхар  
- **1** — 1-р давхар  
- **2** — 2-р давхар

### Үндсэн хөдөлгөөний төлөвүүд

- **Idle (Сул)** — Лифт тухайн давхарт зогссон, хаалга хаалттай.  
- **MovingUp (Дээшээ явж буй)** — Давхруудын хооронд дээш чиглэлд хөдөлж буй.  
- **MovingDown (Доошоо явж буй)** — Давхруудын хооронд доош чиглэлд хөдөлж буй.  
- **DoorOpen (Хаалга нээлттэй)** — Лифт тухайн давхарт зогсож, хаалга нээлттэй.

> Анхаар: Хаалгыг тусад нь төлөв болгон авч үзнэ — хаалга нээлттэй бол лифт хөдөлж болохгүй.

### Оролт / Товчлуурууд ба Мэдрэгч

- **CabinRequest(f)** — Лифт доторх давхарын товч (f ∈ {B,1,2})  
- **HallCallUp(f)** — Давхар дээрээс дээш дуудах товч (f ∈ {B,1})  
- **HallCallDown(f)** — Давхар дээрээс доош дуудах товч (f ∈ {1,2})  
- **Arrive(f)** — Давхар дээр ирсэн мэдрэгчийн дохио  
- **DoorTimerExpired** — Хаалга нээлттэй хугацаа дууссан дохио  
- **EmergencyStop** — Онцгой зогсолтын дохио

---

## FSM — Төлөвүүд (States)

Нарийвчилсан төлөвүүд (давхар + хөдөлгөөн + хаалга):

- **S0 — Idle_B**: B давхарт зогссон, хаалга хаалттай  
- **S1 — Idle_1**: 1-р давхарт зогссон, хаалга хаалттай  
- **S2 — Idle_2**: 2-р давхарт зогссон, хаалга хаалттай  
- **S3 — MovingUp_B1**: B → 1 хооронд дээшээ явж буй  
- **S4 — MovingUp_12**: 1 → 2 хооронд дээшээ явж буй  
- **S5 — MovingDown_21**: 2 → 1 хооронд доошоо явж буй  
- **S6 — MovingDown_1B**: 1 → B хооронд доошоо явж буй  
- **S7 — DoorOpen_B**: B давхарт хаалга нээлттэй  
- **S8 — DoorOpen_1**: 1-р давхарт хаалга нээлттэй  
- **S9 — DoorOpen_2**: 2-р давхарт хаалга нээлттэй

---

## Үзэгдлүүд (Events) ба Үйлдлүүд (Actions)

**Үзэгдлүүд:**

- `e1: CabinRequest(f)` — Доторх товч дарах  
- `e2: HallCallUp(f)` — Давхарт дээш дуудах  
- `e3: HallCallDown(f)` — Давхарт доош дуудах  
- `e4: Arrive(f)` — Давхар дээр ирсэн дохио  
- `e5: DoorTimerExpired` — Хаалга хаах хугацаа дууссан  
- `e6: EmergencyStop` — Онцгой зогсолт

**Үйлдлүүд:**

- `a1: registerRequest(f, type)` — Хүсэлтийг бүртгэх  
- `a2: startMotorUp()` / `startMotorDown()` — Мотор эхлүүлэх  
- `a3: stopMotor()` — Мотор зогсоох  
- `a4: openDoor()` / `a5: closeDoor()` — Хаалга нээх/хаах  
- `a6: clearRequestsAtFloor(f)` — Тухайн давхрын хүсэлтүүдийг цэвэрлэх

---

## Төлөв хоорондын шилжилт (Transitions) — Гол дүрмүүд

**Ерөнхий аюулгүй дүрэм:** Хаалганы төлөв (`DoorOpen_*`) дээр байх үед `MovingUp` эсвэл `MovingDown` руу шилжих боломжгүй.

### Idle → Moving

- S0 (Idle_B) —[CabinRequest(1) or HallCallUp(B)] / a2(startMotorUp)→ S3 (MovingUp_B1)  
- S0 (Idle_B) —[CabinRequest(2)] / a2(startMotorUp)→ S3 (MovingUp_B1)  
- S1 (Idle_1) —[CabinRequest(2) or HallCallUp(1)] / a2(startMotorUp)→ S4 (MovingUp_12)  
- S1 (Idle_1) —[CabinRequest(B) or HallCallDown(1)] / a2(startMotorDown)→ S6 (MovingDown_1B)  
- S2 (Idle_2) —[CabinRequest(1/B) or HallCallDown(2)] / a2(startMotorDown)→ S5 (MovingDown_21)

### Moving → Arrive → DoorOpen

- S3 (MovingUp_B1) —[Arrive(1)] / a3(stopMotor), a4(openDoor), a6(clearRequestsAtFloor(1)) → S8 (DoorOpen_1)  
- S4 (MovingUp_12) —[Arrive(2)] / stop, openDoor, clearRequests → S9 (DoorOpen_2)  
- S5 (MovingDown_21) —[Arrive(1)] / stop, openDoor, clearRequests → S8 (DoorOpen_1)  
- S6 (MovingDown_1B) —[Arrive(B)] / stop, openDoor, clearRequests → S7 (DoorOpen_B)

### DoorOpen → DoorClose → Idle эсвэл Дахин хөдөлгөөний захиалга

- S7 (DoorOpen_B) —[DoorTimerExpired] / a5(closeDoor) →  
  - хэрвээ хүсэлт үлдсэн бол a2(startMotorUp) → S3 эсвэл startMotorDown() → S6; эсвэл хүсэлт байхгүй бол S0 (Idle_B)  
- S8 (DoorOpen_1) —[DoorTimerExpired] / closeDoor → шийдвэрээр S1 (Idle_1) эсвэл S4 / S6 (өдөрцөлөөр)  
- S9 (DoorOpen_2) —[DoorTimerExpired] / closeDoor → S2 (Idle_2) эсвэл S5 (MovingDown_21)

### AnyState → Emergency

- **AnyState** —[EmergencyStop] → a3(stopMotor) → Sx (Stopped with DoorClosed at current floor) (additional policy: may open door)

---

## FSM диаграм

```text
      [Idle_B] --CabinRequest(1)/startUp--> [MovingUp_B1] --Arrive(1)/stop,open--> [DoorOpen_1]
         ^                                                                           |
         |                                                                           | DoorTimerExpired/close
         |                                                                           v
   DoorTimerExpired/close                                                           [Idle_1]
         |                                                                           |
         +-------------------(no requests)-----------------------------------------+

Other major flows:
[Idle_1] --CabinRequest(2)/startUp--> [MovingUp_12] --Arrive(2)--> [DoorOpen_2] --DoorTimerExpired--> [Idle_2]
[Idle_2] --CabinRequest(B)/startDown--> [MovingDown_21] --Arrive(1)--> [DoorOpen_1]
[Idle_1] --CabinRequest(B)/startDown--> [MovingDown_1B] --Arrive(B)--> [DoorOpen_B]
