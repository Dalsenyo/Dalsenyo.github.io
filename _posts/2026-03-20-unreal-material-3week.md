---
title: "[언리얼] 게임 그래픽 엔진 심화 3주차 - 머터리얼 심화"
date: 2026-03-20 00:00:00 +0900
categories: [엔진, 언리얼]
tags: [언리얼, 머터리얼, 셰이더, 노말맵, Substrate]
---

## 텍스처 맵 용어 정리

셰이더에서 자주 헷갈리는 맵들의 차이를 정리해보자.

### Bump / Normal / Height / Displacement

| 용어 | 설명 |
|------|------|
| **Bump** | 표면의 거칠기를 표현하는 눈속임 |
| **Normal** | 수학적으로 표면과 수직인 법선 벡터. 면의 방향을 저장 |
| **Height** | 실제 높이값. 보이는 높이를 구현. 터레인 툴에서 주로 사용 |
| **Displacement** | Height와 유사하지만 실제로 버텍스를 밀어냄 |
| **Parallax** | 동일한 Height 데이터를 사용하지만 시차(시점 각도)를 계산해 튀어나온 것처럼 보이는 눈속임 |

> 노말에서 높이를 줄 수 있지만 실제로 높아진 건 아님. Height, Displacement, Parallax는 같은 데이터를 사용하지만 상황에 따라 다른 용어를 씀.

### Parallax 관련 노드

- **BumpOffset** : 가볍지만 깊이 조절이 제한적
- **ParallaxOcclusionMapping** : 깊이값이 풍부하지만 연산량이 많음

---

## SRGB 설정

- **SRGB ON** → 사진(색상 데이터)
- **SRGB OFF** → 수치 데이터 (Normal, ORM 등)

> ORM 텍스처(Ambient Occlusion / Roughness / Metallic)는 반드시 SRGB를 꺼줘야 함

---

## 노말맵 채널 구조

노말맵의 채널은 다음과 같이 구성됨:

- **R** : 가로 방향
- **G** : 세로 방향
- **B** : 법선 (카메라 방향)

각 프로그램마다 좌표계가 달라서 방향이 반전될 수 있음.  
언리얼에서는 **Flip G** 옵션으로 노말 방향을 뒤집을 수 있음.

### Compression Settings - NormalMap (BC5)
R과 G 채널만 저장하는 압축 방식. B는 수학적으로 복원 가능하기 때문에 용량을 줄일 수 있음.

---

## 머터리얼 인스턴스 (Material Instance)

- **마스터 머터리얼** : 셰이더 로직(작동 원리)이 들어있는 원본
- **머터리얼 인스턴스(MI)** : 마스터에서 파생된 복사본. 파라미터 값만 조절 가능

인스턴스는 셰이더를 새로 컴파일하지 않기 때문에 최적화에 유리함.  
많이 필요하면 `Ctrl + D`로 복사해서 사용.

### 자주 사용되는 파라미터 노드

- **ScalarParameter (S)** : 숫자 값 (강도, 타일링 등)
- **VectorParameter (V)** : 색상, 방향 등
- **TextureSamplerParameter2D** : 텍스처 슬롯

---

## 블렌드 모드 (Blend Mode)

| 모드 | 설명 | 포토샵 유사 |
|------|------|------------|
| **Opaque** | 불투명 | - |
| **Masked** | 알파로 폴리곤을 날림. 그림자 구현 가능 | - |
| **Translucent** | 반투명. 그림자 처리 불가 | - |
| **Additive** | RGB 값을 더함. 겹칠수록 밝아짐. BaseColor 비활성화, Emissive만 사용 | Add |
| **Modulate** | 곱셈 방식. 겹칠수록 어두워짐 | Multiply |

> **Additive 텍스처** : 검은색 배경(0)을 더하면 변화가 없으므로, 검은 배경에 밝은 이펙트가 있는 텍스처에 사용  
> **Modulate 텍스처** : 흰색(1)을 곱하면 변화가 없으므로, 어둡게 만들 부분만 있는 텍스처에 사용

### Masked vs Translucent 그림자 처리

- **Masked** : Z-buffer(거리 정보)를 이용해 그림자 구현 가능
- **Translucent** : 반투명은 Z-buffer로 처리할 수 없어 그림자가 안 나옴. 바라보는 각도에 따라 앞뒤가 뒤바뀌는 이슈도 있음

---

## Substrate (언리얼 5.4+)

Project 설정에서 활성화 가능. 5.4 이전에는 베타였으나 이제 기본 활성화됨.

빛을 **컬러로 투과**할지 **무색으로 투과**할지 선택 가능해짐.

### Translucent 유색 투광 모드

| 설정 | 설명 |
|------|------|
| **ColoredTransmittanceOnly (= Modulate)** | 유색 투광 |
| **TranslucentGreyTransmittance** | 기존 Translucent 방식 |

> Substrate는 퀄리티가 크게 올라가지만 그만큼 부하가 심함. 중요한 캐릭터나 오브젝트에 한정해서 활용하는 것이 좋음.

---

## OverDraw (과도한 렌더링)

반투명 오브젝트는 뒤에 있는 오브젝트도 함께 그려야 하기 때문에 최적화에 불리함.  
불투명은 뒤를 그릴 필요가 없어 효율적.

반투명 레이어가 겹칠수록 드로우 횟수가 늘어나므로 남용하지 않는 것이 좋음.

---

## 굴절 (Refraction)

언리얼에서는 기본적으로 불투명 오브젝트에만 굴절이 적용됨.  
반투명 재질에서도 굴절 효과를 적용하려면 **Translucency Pass → Before DoF** 로 변경해줘야 함.

---

## 머터리얼 도메인

- **Deferred Decal** : 머터리얼의 개념 자체가 달라지는 도메인. 데칼 전용.

---

## SSS (Subsurface Scattering)

피부, 왁스 등 빛이 표면 아래로 침투하는 재질에 사용.  
**Clear Coat** 사용 시 `Enable Second Normal` 옵션을 켜야 함.

테스트는 `SubsurfaceScatteringTest` 레벨에서 확인 가능.