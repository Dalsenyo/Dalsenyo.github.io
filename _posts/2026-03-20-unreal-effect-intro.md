---
title: 언리얼 이펙트 기능 소개 정리
date: 2026-03-20 00:00:00 +0900
categories: [엔진, 언리얼]
tags: [언리얼, 이펙트, 나이아가라, 파티클]
---

## 기본 조작

- 더블클릭으로 선 정리 가능
- 이펙트 창에서 `F` 키로 포커스 가능
- `Space` 누르면 이펙트 재시작
- 씬에서 재시작하려면 `/` 누르기
- `Delete` 로 이펙트 옵션 삭제 가능 (예: 기존 옵션 지우고 버스트로 교체)

## 렌더링 우선순위

이펙트 우선순위를 정하고 싶을 때 렌더링에서 번호 부여 가능 → 유니티 레이어 개념과 동일

## Emitter 설정

- `Add Emitter` → 이펙트 창에서 이미터 추가
- `Align Sprite to Mesh Orientation` 추가 후 방향을 Z로 맞추기
- `Emitter State`에서 **Multiple** 설정하면 여러 개 동시 출력 가능

## 크기 애니메이션

- 스케일 컬러에서 **Scale Mode** 변경 → 유니티 인터페이스 스타일로 전환 가능
- `Particle Update` → `Size` 추가 → `Scale Sprite Size` 로 점점 커지는 효과

## 텍스처 & 쉐이더

- 포토샵: `Filter → Other → Minimum` 효과 활용
- 언리얼에서 UV 움직임: **Panner** 노드 하나로 간단하게 구현 가능
- `Dynamic Parameter` 활용
- 텍스처 경계 사각 라인 문제 해결 필요

## 기타 메모

- 코사인을 이용한 얇은 링 제작 가능
- `Solve Force` 는 아래 방향이 자연스러움
- 나이아가라 이후 내용은 영상 참고