# Phase 1 Worktree Starter

현재 디렉토리에서 git status를 확인하고:
1. 변경 있으면 commit/stash/skip 옵션 제공
2. 작업 타입(issue/feature/fix/refactor/chore) 선택
3. 브랜치 네이밍 규칙 적용
4. base 브랜치 (develop > main > stg) auto 탐지
5. git worktree add 실행 (../<repo-name>-worktrees/ 경로)
6. dry-run → 사용자 확인 → 실행
7. 완료 후 Branch/Path 출력