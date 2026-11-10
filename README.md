# Icing 지상 절차 체크리스트

A321 FCOM의 결빙 관련 지상 절차를 단계별 체크리스트와 자동 타이머로 수행할 수 있게 만든 단일 HTML 페이지입니다.

- 페이지: https://claude.ai/artifact/LybfTWycwJxgxDUudZ1kcs
- 파일: `engine-ice-shedding.html` (외부 라이브러리 없이 HTML, CSS, JavaScript 한 파일)

> **참조용 보조 도구입니다.** 시간 계산과 순서 확인을 돕기 위한 것이며, FCOM/SOP와 다를 경우 항상 FCOM/SOP가 우선합니다.

---

## 1. 구성

페이지 맨 위의 탭으로 두 절차를 전환합니다. 두 절차의 진행 상태는 서로 독립적으로 저장됩니다.

| 탭 | 내용 | FCOM 출처 |
|---|---|---|
| **Deicing / Anti-icing** | 기체 방제(스프레이) 전, 후, 이륙 전 체크리스트, Holdover 타이머, 이륙 판단 트리 | PRO-NOR-SUP-ADVWXR p.1~5 (Applicable to: ALL) |
| **Engine Ice Shedding** | 지상 결빙 조건 엔진 가동 시 ice shedding 절차(출발 Taxi-out, 도착 Taxi-in)와 30분 가속 타이머 | PRO-NOR-SUP-ADVWXR p.11~16 (18 FEB 26) |

### 상단 고정 바

어느 탭에서든 항상 보입니다.

- 현재 단계
- 경과 시간과 다음 가속까지 남은 시간 (Ice Shedding)
- **HOLDOVER 남은 시간**: 시작 → 종료 시각 포함. HOT를 설정했을 때만 표시
- **이륙 5분 한도**: 판단 트리에서 "within 5 minutes" 결과를 선택했을 때만 표시

---

## 2. Deicing / Anti-icing 탭

네 개의 하위 탭으로 진행합니다.

| 탭 | 내용 |
|---|---|
| **A 스프레이 전** | 기장 책임, CAUTION/WARNING, 교신, fluid 확인, BLEED OFF, DITCHING ON, THR LEVERS IDLE, "AIRCRAFT PREPARED FOR SPRAYING" |
| **B 종료 후** | PITOTS/STATICS, REPORT 수령, HOLDOVER 결정, DITCHING OFF, OUTFLOW VALVE, ENG BLEED ON(+1분), APU BLEED(+5분), 타이머와 HOT 설정 |
| **C 이륙 전** | NORMAL PROCEDURE 재개, ANTI ICE, FLAPS, HOLDOVER 조정, BEFORE TAKEOFF |
| **D 이륙 판단** | Takeoff Decision Tree(Cockpit, Cabin, External check) |

### 기능

- 항목마다 **CAPT / F/O** 표시가 있고, 위쪽에서 전체/CAPT/F/O 필터를 바꿀 수 있습니다. 표 기준은 FCOM p.5의 역할 분담입니다.
- 항목의 긴 보충 설명은 "설명"을 눌러 펼쳐 봅니다.
- 상단 단계 버튼으로 A, B, C, D를 자유롭게 오갈 수 있고, B 단계 맨 아래에는 C로 가는 버튼이 있습니다.
- **스프레이 종료 버튼**을 누르면 아래 타이머가 시작됩니다.

### 타이머

| 타이머 | 기준 | 알림 |
|---|---|---|
| ENG BLEED ON 가능 | 스프레이 종료 + 1분 | 도달 시 |
| APU BLEED | 스프레이 종료 + 5분 | 도달 시 |
| BLEED OFF 경과 | ENG 1/2, APU BLEED OFF를 모두 체크한 시점부터 | 15분, 20분 (양쪽 PACK OFF 20분 이상 지상 운영은 권장하지 않음) |
| **HOLDOVER** | 최종 도포 시작 시각(24시간 HH:MM, 기본값 현재 시각, 수정 가능) + HOT 분 | 10분 전, 5분 전, 만료 |
| 이륙 5분 | 판단 트리에서 "TAKEOFF within 5 minutes" 결과 선택 시 | 1분 전, 만료 |

- BLEED ON 체크리스트 항목(ENG 1, ENG 2, APU) 오른쪽에도 남은 시간이 표시됩니다. 도달하면 `가능`, 체크하면 `완료`로 바뀝니다.
- **HOT 분은 직접 입력합니다.** 제공된 FCOM 페이지에 holdover 표가 없습니다.

### 이륙 판단 트리

p.4 다이어그램을 질문 순서대로 따라갑니다.

1. Holdover 진행 중이면 COCKPIT → CABIN → EXTERNAL CHECK 순서
2. Holdover 만료 후에는 기상 조건(Frost/Freezing fog/Snow 또는 Freezing drizzle/Light freezing rain/Rain on cold soaked wings)과 fluid type(Type I 또는 II & IV)으로 분기
3. 결과: `TAKEOFF`, `TAKEOFF within 5 minutes`(불가 시 반복할 항목 안내), `DE/ANTI-ICE AIRCRAFT`
4. 재방제 결과에서는 버튼 한 번으로 A 단계로 돌아가며 체크와 타이머가 초기화됩니다.

---

## 3. Engine Ice Shedding 탭

### 시작 전 선택

첫 화면에서 **출발 (Taxi-out)** 또는 **도착 (Taxi-in)** 을 선택합니다. 절차가 시작되면 바꿀 수 없습니다.

### 출발 흐름

```
사전 확인 → Taxi-out → Before Takeoff / After Line-up → 이륙 후
```

1. **사전 확인**: Icing/OAT ≤ +3 °C 여부, FZFG, visibility(m, RVR 아님), LOGBOOK의 직전 taxi-in 시간과 정비 가동 시간, 시동 전 엔진 deice 정비 여부를 입력하면 첫 가속까지의 최대 시간을 계산합니다. (30분 − (직전 taxi-in + 정비 가동), 시동 전 deice 정비를 했다면 두 값은 무시)
2. FZFG이고 visibility가 150 m 미만이면 **TAKEOFF NOT AUTHORIZED** 로 표시하고 시작을 막습니다.
3. **Taxi-out**: 첫 엔진 시동 버튼으로 시작합니다. 경과 시간과 다음 가속까지의 시간이 표시되고, 가속을 완료할 때마다 30분으로 다시 시작합니다.
4. **Line-up 이후**: 이륙 전 최종 가속(ENG PARAMETERS 확인 포함)을 수행하고, 완료 후 5분 이내에 takeoff thrust를 선택하는 타이머가 돌아갑니다.

### 도착 흐름

```
Touchdown → Taxi-in → 두 엔진 Shutdown → Parking
```

- Touchdown 버튼으로 30분 타이머를 시작합니다. 가속 완료 시 30분으로 다시 시작합니다.
- Shutdown 시 **LOGBOOK에 기록할 Taxi-in 시간**을 보여줍니다. 마지막 가속을 했다면 그 이후의 시간입니다.
- 올림한 분 값이 이 기기에 저장되어 다음 비행 입력의 제안값으로 채워집니다.

### 가속 체크리스트 (공용)

Taxi-out, Line-up 이후, Taxi-in에서 같은 체크리스트를 씁니다.

`JET BLAST 확인 → GND SURFACE COND & AREA → ATC → PARKING BRAKE → THR LEVERS 60% N1 30초 → IDLE (→ ENG PARAMETERS, 최종 가속만)`

- 60% N1 30초 타이머가 있고, 끝나면 소리와 진동으로 IDLE을 알립니다.
- WARNING(jet blast)과 CAUTION(기체가 움직이면 즉시 IDLE, 30초가 어려우면 가능한 만큼 유지)을 함께 표시합니다.
- **방송 멘트**(한글/English)를 가속 체크리스트 위에 "참고 멘트(FCOM 항목 아님)"로 보여줍니다. 문구는 페이지에서 직접 수정할 수 있고 이 기기에 저장됩니다.
- 가속 기록(회차, 단계, 시각)이 남습니다.

### 일시 중지

Taxi 중 엔진을 껐다 켜는 경우를 위해 **엔진 정지 — 타이머 일시 중지** 버튼이 있습니다. 중지하는 동안 경과 시간과 남은 시간이 멈추고, **재개** 시 멈춘 만큼을 빼서 이어집니다. 중지 중에는 가속, Line-up, Shutdown 버튼이 숨겨집니다. 가속 체크리스트가 열려 있을 때는 중지할 수 없습니다.

---

## 4. 알림과 저장

- **알림**: 소리(Web Audio)와 진동. 기기에서 지원하는 경우에만 동작하고, 브라우저 정책상 페이지를 한 번 터치한 뒤부터 소리가 납니다.
- **화면 꺼짐 방지**: 엔진 타이머를 시작할 때 화면이 꺼지지 않도록 요청합니다. 지원하는 브라우저에서만 동작합니다.
- **시간 계산**: 시작 시각 기준 timestamp로 계산하므로 화면이 꺼지거나 탭이 백그라운드로 가도 시간이 틀어지지 않습니다.
- **저장**: 진행 상태는 이 브라우저의 localStorage에 저장되어 새로고침해도 이어집니다. 서버로 전송되거나 다른 기기와 공유되지 않습니다.
- **초기화**: 각 탭 맨 아래의 초기화 버튼(Deicing 초기화, 처음부터 다시 시작)은 한 번 더 눌러야 동작합니다. 두 탭의 초기화는 서로 영향을 주지 않습니다.

---

## 5. 해석과 한계

문서에 명시되지 않아 구현자가 해석한 부분입니다. FCOM과 다르면 FCOM을 따르세요.

- 첫 가속 한도가 0분 이하이면 "시동 직후 가속 필요"로 표시합니다.
- 이륙 5분 한도가 지나면 빨간색으로 초과만 표시합니다.
- 엔진 정지 후 재시동 시 정지 시간을 빼고 이어서 계산합니다.
- BLEED OFF 경과 시간(PACK OFF 20분 주의)은 BLEED OFF 체크 시점부터 계산합니다.
- 판단 트리에서 만료 후 경로의 **Type I 선택은 재방제**로 가는 것으로 다이어그램을 읽었습니다.
- 올림한 분 값을 다음 비행 LOGBOOK 입력 제안값으로 저장합니다.

### 포함하지 않은 것

- Engine Ice Shedding은 **HL8505 그룹**(HL8505, HL8506, HL8509, HL8528, HL8529, HL8530, HL8531, HL8532, HL8556~HL8559, HL8589~HL8591, HL8710~HL8713, HL8747) 절차만 반영했습니다. FCOM에는 다른 기체 목록(HL8356 그룹)의 같은 제목 절차가 이어지며, 내용이 같은지는 확인하지 않았습니다.
- Holdover time 표(fluid 종류와 기상별 시간)
- FCOM p.6 이후의 Ground Operations in Cold Weather Conditions(안전 외부점검, IRS 정렬 등)

---

## 6. 기준 문서

| 절차 | Ident / 개정일 |
|---|---|
| Engine Operations on Ground in Icing Conditions | PRO-NOR-SUP-ADVWXR-00026014.0004001 / 18 FEB 26 |
| Airframe Deicing/Anti-icing Procedure on Ground | PRO-NOR-SUP-ADVWXR-A-00020694.0001001 / 06 NOV 25 외 (판단 트리는 13 JUN 22) |

FCOM이 개정되면 페이지 내용도 함께 갱신해야 합니다.

## 7. 수정하기

HTML 파일을 직접 편집하거나, 이 페이지를 만든 대화에서 수정 사항을 요청하면 같은 링크의 페이지가 갱신됩니다.
