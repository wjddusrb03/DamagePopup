<div align="center">

# DamagePopup

### RPG-style floating damage numbers for your Minecraft server.

A lightweight **Paper 1.21+** plugin that displays floating damage numbers above entities using the modern **TextDisplay** entity API. Every hit, heal, and kill gets a stylish popup with color-coded damage types, critical hit effects, and smooth fade-out animations.
**No dependencies. Fully configurable. Just install and fight.**

[![GitHub Release](https://img.shields.io/github/v/release/wjddusrb03/DamagePopup?style=for-the-badge&label=Download&color=brightgreen)](https://github.com/wjddusrb03/DamagePopup/releases/latest)
![Paper](https://img.shields.io/badge/Paper-1.21+-blue?style=for-the-badge)
![Java](https://img.shields.io/badge/Java-17+-orange?style=for-the-badge)

**[English](#features)** | **[한국어](#기능)**

</div>

---

## Screenshots

<div align="center">

*Screenshots coming soon!*

<!--
| Damage Popup | Critical Hit | Heal Popup |
|:-:|:-:|:-:|
| ![Damage](screenshots/damage.png) | ![Critical](screenshots/critical.png) | ![Heal](screenshots/heal.png) |
-->

</div>

---

## Features

| Feature | Description |
|---------|-------------|
| **Color-coded damage types** | Each damage type has a distinct color — physical (red), fire (orange), magic (purple), poison (green), wither (dark gray), fall (gray) |
| **Critical hit detection** | Detects vanilla critical hits (falling + not on ground) and arrow criticals. Displays with larger bold red text and a **✦** symbol |
| **Healing popup** | When a player regenerates health, a green popup with a **❤** symbol floats up |
| **Kill popup** | On entity death, a bold **☠ KILL** text appears above the victim |
| **Smooth fade-out animation** | Popups float upward, then shrink and fade to transparent in the final third of their lifetime |
| **Per-player toggle** | Each player can toggle their popups on/off with `/dp toggle` |
| **Fully configurable** | Customize colors, symbols, scale, duration, decimal places, minimum damage threshold, and more via `config.yml` |
| **Attacker-only mode** | Optional mode where only the attacker sees the damage popup (not all nearby players) |
| **Random offset** | Popups spawn with a small random horizontal offset to prevent overlapping when hitting rapidly |
| **Lightweight** | Uses TextDisplay entities with no persistence — zero impact on world saves |
| **No dependencies** | Fully standalone — no Vault, no ProtocolLib, no resource packs required |

### Damage Type Colors

| Type | Color | Triggers |
|------|-------|----------|
| Physical | Red `#FF5555` | Melee attacks, arrows, thorns |
| Critical | Bold Red `#FF0000` | Falling critical hits, critical arrows |
| Fire | Orange `#FF8C00` | Fire, lava, magma blocks |
| Magic | Purple `#AA00FF` | Potions, dragon breath, sonic boom |
| Poison | Green `#00AA00` | Poison effect |
| Wither | Dark Gray `#333333` | Wither effect |
| Fall | Gray `#AAAAAA` | Fall damage, fly into wall |
| Heal | Light Green `#55FF55` | Health regeneration |
| Other | White `#FFFFFF` | All other damage sources |

## Requirements

| Requirement | Version | Notes |
|-------------|---------|-------|
| **Server** | Paper 1.21+ | Tested on Paper 1.21.4. Uses TextDisplay entity API (1.19.4+) |
| **Java** | 17+ | Required by Paper 1.21 |
| **Dependencies** | None | Fully standalone |

> **Spigot / Folia:** This plugin uses Paper-specific APIs (TextDisplay, Adventure Components). It will **not** work on Spigot. Folia compatibility has not been tested.

## Installation

1. Download **`DamagePopup-1.0.0.jar`** from the [Releases](../../releases/latest) page
2. Place the JAR in your server's `plugins/` folder
3. Start or restart the server
4. Hit any mob — you'll see floating damage numbers immediately!

A default `config.yml` is generated on first run. Customize to your liking.

## Commands & Permissions

| Command | Aliases | Description | Permission | Default |
|---------|---------|-------------|------------|---------|
| `/dp` | `/damagepopup`, `/dpopup` | Show help | — | Everyone |
| `/dp toggle` | — | Toggle your damage popups on/off | `damagepopup.toggle` | Everyone |
| `/dp reload` | — | Reload configuration | `damagepopup.admin` | OP only |

### Permissions

| Permission | Description | Default |
|------------|-------------|---------|
| `damagepopup.see` | See damage popups | Everyone |
| `damagepopup.toggle` | Toggle popups on/off | Everyone |
| `damagepopup.admin` | Admin commands (reload) | OP |

## Configuration

The plugin generates a `config.yml` on first run with all available options:

```yaml
# How long the popup lasts (in ticks, 20 = 1 second)
duration: 30

# How high the popup floats up (in blocks)
float-height: 1.5

# Text scale (1.0 = normal size)
scale: 1.5

# Critical hit scale multiplier (applied on top of base scale)
critical-scale: 2.0

# Only attacker sees the popup? (false = everyone nearby sees it)
attacker-only: false

# Damage type colors (hex format)
colors:
  physical: "#FF5555"
  critical: "#FF0000"
  fire: "#FF8C00"
  magic: "#AA00FF"
  poison: "#00AA00"
  wither: "#333333"
  fall: "#AAAAAA"
  heal: "#55FF55"
  other: "#FFFFFF"

# Symbols
symbols:
  critical-prefix: "✦ "
  heal-prefix: "❤ "
  suffix: ""

# Decimal places for damage numbers
decimal-places: 1

# Minimum damage to show popup (prevents spam)
min-damage: 0.5

# Random horizontal offset range (prevents overlapping)
random-offset: 0.3

# Kill popup on entity death
show-death: true
death-text: "☠ KILL"
```

## How It Works

```
Entity takes damage → Classify damage type → Determine color & scale
                                                      ↓
                                              Spawn TextDisplay
                                              (colored, scaled, bold if crit)
                                                      ↓
                                              Animate: float up each tick
                                                      ↓
                                              Last 1/3: fade out + shrink
                                                      ↓
                                              Remove entity after duration
```

1. **Damage events** are monitored at `MONITOR` priority (after all other plugins process)
2. The damage cause is classified into one of 9 types, each with its own color
3. **Critical hits** are detected using vanilla conditions (falling, not on ground, not in water, etc.)
4. A **TextDisplay** entity spawns above the victim with random horizontal offset
5. Every tick, the popup floats upward by `float-height / duration` blocks
6. In the final third, opacity decreases from 255 to 0 and scale shrinks to 50%
7. After the duration expires, the entity is removed

## Performance

- **Entity lifetime:** Each popup exists for only 1.5 seconds (30 ticks) by default
- **No persistence:** `setPersistent(false)` ensures popups are never saved to world files
- **Automatic cleanup:** All active popups are removed on plugin disable/server stop
- **Thread-safe:** Uses `CopyOnWriteArrayList` for concurrent popup tracking
- **Minimal overhead:** TextDisplay entities are lightweight — even rapid combat produces negligible server load

---

## Issues & Feedback

Found a bug? Have a suggestion? Please open an **[Issue](../../issues)**!

When reporting a bug, please include:
- Server software and version (e.g., Paper 1.21.4 build 232)
- Java version
- Steps to reproduce the problem
- Full error message or stack trace from the console (if any)
- Other plugins installed (to check for conflicts)

---

<div align="center">

# DamagePopup (한국어)

### 마인크래프트 서버를 위한 RPG 스타일 플로팅 데미지 숫자.

가벼운 **Paper 1.21+** 플러그인으로, 최신 **TextDisplay** 엔티티 API를 사용하여
엔티티 위에 떠오르는 데미지 숫자를 표시합니다. 모든 공격, 회복, 처치에
색상별 데미지 타입, 크리티컬 히트 효과, 부드러운 페이드아웃 애니메이션이 적용됩니다.
**의존성 없음. 완벽한 커스터마이징. 설치하고 바로 전투하세요.**

</div>

---

## 스크린샷

<div align="center">

*스크린샷 준비 중!*

</div>

---

## 기능

| 기능 | 설명 |
|------|------|
| **데미지 타입별 색상** | 각 데미지 타입마다 고유한 색상 — 물리(빨강), 화염(주황), 마법(보라), 독(초록), 위더(짙은 회색), 낙하(회색) |
| **크리티컬 히트 감지** | 바닐라 크리티컬 히트(낙하 중 + 착지 아님) 및 화살 크리티컬 감지. 크고 굵은 빨간 글씨와 **✦** 기호로 표시 |
| **회복 팝업** | 플레이어가 체력을 회복하면 **❤** 기호와 함께 초록색 팝업이 떠오름 |
| **처치 팝업** | 엔티티 사망 시 굵은 **☠ KILL** 텍스트가 피해자 위에 표시 |
| **부드러운 페이드아웃 애니메이션** | 팝업이 위로 떠오른 후 수명의 마지막 1/3에서 축소되며 투명해짐 |
| **플레이어별 토글** | 각 플레이어가 `/dp toggle`로 팝업을 켜거나 끌 수 있음 |
| **완벽한 커스터마이징** | `config.yml`에서 색상, 기호, 크기, 지속 시간, 소수점 자릿수, 최소 데미지 등 조정 가능 |
| **공격자 전용 모드** | 공격자만 데미지 팝업을 볼 수 있는 선택적 모드 |
| **랜덤 오프셋** | 빠른 연타 시 겹침을 방지하기 위해 약간의 랜덤 수평 오프셋 적용 |
| **경량** | 지속성 없는 TextDisplay 엔티티 사용 — 월드 저장에 영향 없음 |
| **무의존성** | 완전 독립 — Vault, ProtocolLib, 리소스팩 불필요 |

### 데미지 타입별 색상

| 타입 | 색상 | 발동 조건 |
|------|------|----------|
| 물리 | 빨강 `#FF5555` | 근접 공격, 화살, 가시 |
| 크리티컬 | 굵은 빨강 `#FF0000` | 낙하 크리티컬, 크리티컬 화살 |
| 화염 | 주황 `#FF8C00` | 불, 용암, 마그마 블록 |
| 마법 | 보라 `#AA00FF` | 포션, 드래곤 브레스, 소닉 붐 |
| 독 | 초록 `#00AA00` | 독 효과 |
| 위더 | 짙은 회색 `#333333` | 위더 효과 |
| 낙하 | 회색 `#AAAAAA` | 낙하 데미지, 벽 충돌 |
| 회복 | 연두 `#55FF55` | 체력 재생 |
| 기타 | 흰색 `#FFFFFF` | 그 외 모든 데미지 |

## 요구 사항

| 요구 사항 | 버전 | 비고 |
|-----------|------|------|
| **서버** | Paper 1.21+ | Paper 1.21.4에서 테스트됨. TextDisplay 엔티티 API (1.19.4+) 사용 |
| **Java** | 17+ | Paper 1.21 요구 사항 |
| **의존성** | 없음 | 완전 독립 |

> **Spigot / Folia:** 이 플러그인은 Paper 전용 API를 사용합니다 (TextDisplay, Adventure Components). Spigot에서는 **작동하지 않습니다**. Folia 호환성은 테스트되지 않았습니다.

## 설치 방법

1. [Releases](../../releases/latest) 페이지에서 **`DamagePopup-1.0.0.jar`** 다운로드
2. 서버의 `plugins/` 폴더에 JAR 파일 넣기
3. 서버 시작 또는 재시작
4. 아무 몹이나 때려보세요 — 즉시 플로팅 데미지 숫자가 표시됩니다!

첫 실행 시 기본 `config.yml`이 생성됩니다. 원하는 대로 커스터마이징하세요.

## 명령어 및 권한

| 명령어 | 별칭 | 설명 | 권한 | 기본값 |
|--------|------|------|------|--------|
| `/dp` | `/damagepopup`, `/dpopup` | 도움말 표시 | — | 모든 플레이어 |
| `/dp toggle` | — | 데미지 팝업 켜기/끄기 | `damagepopup.toggle` | 모든 플레이어 |
| `/dp reload` | — | 설정 다시 로드 | `damagepopup.admin` | OP 전용 |

### 권한

| 권한 | 설명 | 기본값 |
|------|------|--------|
| `damagepopup.see` | 데미지 팝업 표시 | 모든 플레이어 |
| `damagepopup.toggle` | 팝업 토글 | 모든 플레이어 |
| `damagepopup.admin` | 관리자 명령어 (reload) | OP |

## 설정

플러그인 첫 실행 시 `config.yml`이 생성됩니다. 사용 가능한 모든 옵션:

```yaml
# 팝업 지속 시간 (틱 단위, 20 = 1초)
duration: 30

# 팝업이 떠오르는 높이 (블록 단위)
float-height: 1.5

# 텍스트 크기 (1.0 = 기본 크기)
scale: 1.5

# 크리티컬 히트 크기 배율 (기본 크기에 곱해짐)
critical-scale: 2.0

# 공격자만 팝업을 볼 수 있게 할지 (false = 주변 모두 볼 수 있음)
attacker-only: false

# 데미지 타입별 색상 (16진수 형식)
colors:
  physical: "#FF5555"
  critical: "#FF0000"
  fire: "#FF8C00"
  magic: "#AA00FF"
  poison: "#00AA00"
  wither: "#333333"
  fall: "#AAAAAA"
  heal: "#55FF55"
  other: "#FFFFFF"

# 기호
symbols:
  critical-prefix: "✦ "
  heal-prefix: "❤ "
  suffix: ""

# 데미지 숫자 소수점 자릿수
decimal-places: 1

# 팝업을 표시할 최소 데미지 (스팸 방지)
min-damage: 0.5

# 랜덤 수평 오프셋 범위 (겹침 방지)
random-offset: 0.3

# 엔티티 사망 시 처치 팝업
show-death: true
death-text: "☠ KILL"
```

## 작동 원리

```
엔티티가 데미지를 받음 → 데미지 타입 분류 → 색상 및 크기 결정
                                                      ↓
                                              TextDisplay 생성
                                              (색상, 크기, 크리티컬은 굵게)
                                                      ↓
                                              애니메이션: 매 틱마다 위로 이동
                                                      ↓
                                              마지막 1/3: 페이드아웃 + 축소
                                                      ↓
                                              지속 시간 후 엔티티 제거
```

1. **데미지 이벤트**를 `MONITOR` 우선순위로 감시합니다 (다른 플러그인이 처리한 후)
2. 데미지 원인을 9가지 타입 중 하나로 분류하며, 각각 고유한 색상을 가집니다
3. **크리티컬 히트**는 바닐라 조건(낙하 중, 착지 아님, 물 속 아님 등)으로 감지합니다
4. **TextDisplay** 엔티티가 피해자 위에 랜덤 수평 오프셋과 함께 생성됩니다
5. 매 틱마다 `float-height / duration` 블록만큼 위로 떠오릅니다
6. 마지막 1/3에서 투명도가 255에서 0으로, 크기가 50%로 줄어듭니다
7. 지속 시간이 끝나면 엔티티가 제거됩니다

## 성능

- **엔티티 수명:** 기본 1.5초(30틱)만 존재
- **지속성 없음:** `setPersistent(false)`로 월드 파일에 저장되지 않음
- **자동 정리:** 플러그인 비활성화/서버 종료 시 모든 활성 팝업 제거
- **스레드 안전:** `CopyOnWriteArrayList`로 동시 팝업 추적
- **최소 오버헤드:** TextDisplay 엔티티는 경량 — 빠른 전투에서도 서버 부하 무시할 수준

---

## 문제 및 피드백

버그를 발견하셨나요? 제안이 있으신가요? **[Issue](../../issues)**를 열어주세요!

버그 리포트 시 포함해 주세요:
- 서버 소프트웨어 및 버전 (예: Paper 1.21.4 빌드 232)
- Java 버전
- 문제 재현 방법
- 콘솔의 전체 에러 메시지 또는 스택 트레이스 (있다면)
- 설치된 다른 플러그인 목록 (충돌 확인용)

---

<div align="center">

**Made with TextDisplay API**

MIT License

</div>
