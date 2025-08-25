# Phase 2 (Plan): TDD → 구현 → 병렬 코드리뷰 → 테스트/커버리지 검증

## 목적
아래 순서를 자동으로 실행:
1) 테스트 작성: ~/.claude/CLAUDE.local.md의 테스트 코드 표준을 참고하여 구현
2) 코드 구현: ~/.claude/CLAUDE.local.md를 참고하여 구현
3) 코드 리뷰: collin-code-reviewer (메인) → 서브 5개 병렬 호출 후 통합 리포트/패치
4) 커버리지 검증: collin-test-coverage-analyzer (기본 임계치 80%)
5) 마이그레이션 스크립트 생성: collin-migration-script-agent

## 입력 (옵션)
- lang: kotlin (고정 추천)
- test_cmd: ./gradlew test
- cov: 80
- path_filter: 리뷰·테스트 대상 경로(선택)

## 플랜
- 단계1: "CLAUDE.local.md의 테스트 코드 표준에 따라 테스트 코드 작성 (Kotest 통합 테스트 우선)"
- 단계2: "테스트가 실패하는 상태를 기준으로 최소 구현을 하여 GREEN을 달성해줘. 과도한 리팩터링 금지."
- 단계3: "collin-code-reviewer 실행 path_filter={path_filter}"
- 단계4: "collin-test-coverage-analyzer 실행 test_cmd={test_cmd} cov={cov}"
- 단계5: "collin-migration-script-agent 실행"

## 정책
- 테스트 3회 연속 실패 시: 원인·패치 제안 후 중단
- 리뷰 결과 critical/high 있으면: 우선 패치 적용 후 재테스트
