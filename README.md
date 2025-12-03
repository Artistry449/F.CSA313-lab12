# Лабораторийн ажил №12: Төгсгөлөг төлөвийн машин — Лифтний хяналтын систем

<div align="center">

# **ЛАБОРАТОРИЙН АЖИЛ №12**

<br><br><br>

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
```

# Шинж (Properties) — Ямар зүйлийг шалгах ёстой

## Safety (Аюулгүй байдал)

- **S1: Хаалга нээлттэй үед лифт хөдөлж болохгүй.**  
  *Бодит шалгалт:* ямар ч үед `DoorOpen_*` төлөвт байхад `MovingUp`/`MovingDown` төлөв байхгүй байх.  
  *(Тест: DoorOpen_1 төлөв дээр байх үед startMotorUp/startMotorDown үйлдэл үүсэхгүй эсэхийг батална.)*

- **S2: Хаалга зөвхөн лифт тухайн давхарт зогссон үед нээгдэх ёстой.**  
  *Бодит шалгалт:* `openDoor()` үйлдэл нь зөвхөн `Arrive(f)`-ийн дараа буюу Idle төлөвт байх үед хийгдэх.  
  *(Тест: MovingUp үед openDoor үйлдэл үүсэхгүй байгааг шалгах.)*

- **S3: Давхраас гадна (B-ээс доош, 2-оос дээш) шилжилт байхгүй.**  
  *Бодит шалгалт:* Шилжилтүүд зөвхөн B ↔ 1 ↔ 2 хооронд хийгдэж байгаа эсэхийг батлах.  
  *(Тест: CabinRequest(3) зэрэг утга орж ирсэн үед систем зөвшөөрөлгүй алдааг барина/игнорхойлно.)*

- **S4: Давхар дээр ирээд зогсох үед тухайн давхрын бүх холбогдох хүсэлтүүд арилна.**  
  *Бодит шалгалт:* `Arrive(f) → openDoor()` дарааллын дараа `clearRequestsAtFloor(f)` дуудлагдсан эсэхийг шалгах.

## Liveness (Амьдралтай байдал)

- **L1: Хүсэлт алгасагдахгүй байх (eventual service).**  
  *Бодит шалгалт:* Нэг cabin эсвэл hall хүсэлт өгсөний дараа тодорхой хугацааны дотор лифт тухайн давхарт ирж хаалгаа нээх ёстой.  
  *(Тест: GIVEN CabinRequest(2) WHEN system idle THEN eventually Arrive(2) / DoorOpen_2.)*

- **L2: Систем мөнх зогсон хэвтэхгүй байх (progress).**  
  *Бодит шалгалт:* Idle төлөв дээр байх үед хүсэлт ирвэл мотор эхэлж хөдөлдөг эсэхийг шалгах.

## Preconditions (Урьдчилсан нөхцөл ба invariant-ууд)

- **P1:** `startMotorUp/Down()` дуудахаас өмнө хаалга хаалттай байх ёстой.  
  *(DoorOpen_* дээрээс startMotor дуудлага үүсэхгүй.)*

- **P2:** `openDoor()` үйлдлийг зөвхөн зогсолтын төлөв (`Arrive → stop`) дээр гүйцэтгэнэ.

- **P3:** Emergency stop болсон тохиолдолд мотор зогсож бүх хөдөлгөөн таслагдана.

---

# Тест кейс, сценари

Тестүүд FSM-ийн бүх төлөв, шилжилт, гол замуудыг хамарч байх ёстой. Доорх хүснэгтэнд гол тестүүдийг оруулсан.

## Тест кэйсийн хүснэгт

| Test ID | Тайлбар | Эхлэлийн төлөв | Оролтууд (Events) | Хүлээгдэж буй төлөв/шилжилт | Шалгагдах шинж |
|---------|---------|----------------|------------------|-----------------------------|----------------|
| TC-01 | B → 1 (Cabin) энгийн | Idle_B (S0) | CabinRequest(1), Arrive(1), DoorTimerExpired | S0 → S3 → S8 → S1 | S1, S2, L1 |
| TC-02 | B → 2 (Cabin) — дамжин 1 дээр зогсох/үргэжлэх | Idle_B | CabinRequest(2), Arrive(1), (no stop at 1), Arrive(2), DoorTimerExpired | S0 → S3 → (pass1) → S4 → S9 → S2 | L1, S3 |
| TC-03 | 2 → B (Down) | Idle_2 (S2) | CabinRequest(B), Arrive(1), Arrive(B), DoorTimerExpired | S2 → S5 → S8 → S6 → S7 → S0 | L1, S3 |
| TC-04 | Door open үед хөдөлгөөн эхлэхгүй байх | DoorOpen_1 (S8) | CabinRequest(2), (immediate) verify no startMotor | S8 (no movement) → DoorTimerExpired → Idle_1 → startMotorUp → ... | P1, S1, S2 |
| TC-05 | Олон хүсэлт нэг дор | Idle_B | HallCallUp(B), CabinRequest(2), HallCallDown(2) | Verify scheduling: on-way requests served first | L1, scheduling policy |
| TC-06 | Emergency stop while moving | MovingUp_12 (S4) | EmergencyStop | stopMotor -> Stopped (DoorClosed) | Safety: motor off |
| TC-07 | Хаалга нээлтлээ request clear байх | Any DoorOpen_X | DoorTimerExpired | After open -> clearRequestsAtFloor(f) | S4, S1 |

---

## Дэлгэрэнгүй сценари (жишээ)

### Сценари A — Олон хүний өглөөний урсгал (B → 1 → 2)

1. **Эхлэл:** Idle_B (S0)  
2. **Оролт:** CabinRequest(1), CabinRequest(2) дарааллан ирнэ  
3. **Хүлээгдэж буй зам:** S0 → S3 (startUp) → Arrive(1) → S8 (DoorOpen_1) → DoorTimerExpired → S4 (MovingUp_12) → Arrive(2) → S9 → DoorTimerExpired → S2  
4. **Шалгах:** хаалга нээлттэй үед мотор ажиллаагүй, дарааллын дагуу бүх хүсэлт биелсэн.

### Сценари B — Нэг давхарт зэрэг олон заавар (1 дээр Up & Down)

1. **Эхлэл:** Idle_1 (S1)  
2. **Оролт:** HallCallUp(1) болон HallCallDown(1) зэрэг ирнэ  
3. **Шалгах:** системийн бодлого (current direction priority) дээр тулгуурлан зохион байгуулалт хийх — Up эхэлж үйлчилсэн эсэх, дараа Down үйлчилсэн эсэхийг шалгах.

---

## Тестийн хамрах хүрээ (Coverage)

- **State coverage:** S0–S9-ийг дор хаяж нэг удаа туршина.  
- **Transition coverage:** Idle ↔ Moving, Moving → DoorOpen, DoorOpen → Idle/Motion шилжилтүүдийг хамруулна.  
- **Property checks:** Safety (S1–S4) болон Liveness (L1–L2) шинжүүдийг автоматаар/гараар шалгана.

---

## Дүгнэлт

Энэхүү лабораторийн ажилд лифтний хяналтын системийг төгсгөлөг төлөвийн машин хэлбэрээр амжилттай загварчилсан. FSM нь:

- Төлөвүүд (Idle, MovingUp/Down, DoorOpen)-ийг тодорхойлж, давхар тус бүрийн нарийвчилсан төлөвүүдийг боловсруулсан.  
- Үзэгдэл/үйлдлүүдийг ялган, шилжилт бүрт шаардлагатай үйлдлийг зааж өгсөн.  
- Аюулгүй байдлын дүрмүүд (хаалганы нөхцөл, хөдөлгөөний хяналт) болон амьдралтай байдлын шаардлагуудыг тодорхойлсон.  
- FSM дээр тулгуурлан тест кейс, сценари боловсруулж, системийн state/transition coverage-ийг хангах загварыг бэлдсэн.
