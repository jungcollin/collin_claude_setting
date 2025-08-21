#!/bin/bash

# Phase 2 Worktree Remover
# 현재 워크트리를 안전하게 삭제하고 메인 저장소로 이동

# 색상 정의
RED='\033[0;31m'
GREEN='\033[0;32m'
YELLOW='\033[1;33m'
NC='\033[0m' # No Color

# 현재 디렉토리 확인
CURRENT_DIR=$(pwd)

# Git 워크트리인지 확인
if ! git rev-parse --git-dir > /dev/null 2>&1; then
    echo -e "${RED}Error: 현재 디렉토리는 Git 저장소가 아닙니다.${NC}"
    exit 1
fi

# 워크트리 목록 가져오기
WORKTREE_LIST=$(git worktree list --porcelain)

# 현재 디렉토리가 워크트리인지 확인
IS_WORKTREE=$(echo "$WORKTREE_LIST" | grep -c "^worktree $CURRENT_DIR$")

if [ "$IS_WORKTREE" -eq 0 ]; then
    echo -e "${YELLOW}현재 디렉토리는 Git 워크트리가 아닙니다.${NC}"
    exit 1
fi

# 메인 워크트리 경로 가져오기
MAIN_WORKTREE=$(echo "$WORKTREE_LIST" | head -n1 | sed 's/^worktree //')

if [ "$CURRENT_DIR" = "$MAIN_WORKTREE" ]; then
    echo -e "${RED}Error: 메인 워크트리는 삭제할 수 없습니다.${NC}"
    exit 1
fi

# 현재 브랜치 이름 가져오기
CURRENT_BRANCH=$(git branch --show-current)

# 변경사항 확인
if ! git diff --quiet || ! git diff --cached --quiet; then
    echo -e "${YELLOW}⚠️  커밋되지 않은 변경사항이 있습니다:${NC}"
    git status --short
    echo ""
    read -p "정말로 삭제하시겠습니까? (y/N): " -n 1 -r
    echo
    if [[ ! $REPLY =~ ^[Yy]$ ]]; then
        echo "취소되었습니다."
        exit 0
    fi
fi

echo -e "${GREEN}워크트리 정보:${NC}"
echo "  현재 워크트리: $CURRENT_DIR"
echo "  현재 브랜치: $CURRENT_BRANCH"
echo "  메인 저장소: $MAIN_WORKTREE"
echo ""

# 삭제 확인
read -p "이 워크트리를 삭제하시겠습니까? (y/N): " -n 1 -r
echo
if [[ ! $REPLY =~ ^[Yy]$ ]]; then
    echo "취소되었습니다."
    exit 0
fi

# 메인 워크트리로 이동
echo -e "${GREEN}메인 저장소로 이동 중...${NC}"
cd "$MAIN_WORKTREE" || exit 1

# 워크트리 삭제
echo -e "${GREEN}워크트리 삭제 중...${NC}"
if git worktree remove "$CURRENT_DIR" 2>/dev/null; then
    echo -e "${GREEN}✅ 워크트리가 성공적으로 삭제되었습니다.${NC}"
elif git worktree remove --force "$CURRENT_DIR" 2>/dev/null; then
    echo -e "${YELLOW}⚠️  워크트리가 강제로 삭제되었습니다.${NC}"
else
    echo -e "${RED}Error: 워크트리 삭제에 실패했습니다.${NC}"
    exit 1
fi

# 현재 위치 확인
echo -e "${GREEN}현재 위치: $(pwd)${NC}"

# 워크트리 목록 표시
echo ""
echo -e "${GREEN}남은 워크트리 목록:${NC}"
git worktree list

echo ""
echo -e "${GREEN}✨ 완료되었습니다!${NC}"