---
description: Generate implementation tasks from design document - creates ordered, actionable development tasks
---

# Spec-Driven Tasks Generator / 仕様駆動タスクジェネレーター

## Step 1: Locate Design Document / 設計書の特定

I'll find and analyze the design document to create implementation tasks.

Input: "$ARGUMENTS"

!echo "Looking for design document based on: $ARGUMENTS"

First, let me find the appropriate spec directory:

!find .specs -name "design.md" -type f | grep -i "$ARGUMENTS" || find .specs -type d -name "*$ARGUMENTS*"

## Step 2: Analyze Project Context / プロジェクトコンテキストの分析

Let me understand if this is a new project or feature addition:

!test -f package.json && echo "Node.js project detected" || echo "Not a Node.js project"
!test -f requirements.txt && echo "Python project detected" || echo "Not a Python project"
!test -d .git && echo "Git repository detected" || echo "Not a Git repository"

For existing projects, I'll check:
- Current development status
- Existing test infrastructure
- Build and deployment setup
- Development workflow

!test -f Makefile && cat Makefile | grep -E "^(test|build|install):" | head -5
!test -f package.json && cat package.json | jq '.scripts' 2>/dev/null | head -10

## Step 3: Read Design Document / 設計書の読み込み

Once I locate the design.md file, I'll analyze:
- Architecture components and their dependencies
- Module and class specifications
- Data models and database schemas
- Integration points and APIs
- Testing requirements
- How this fits with existing code

@.specs/*/design.md

## Step 4: Generate Implementation Tasks / 実装タスクの生成

Based on the design AND project context, I'll create tasks that:

**For NEW projects:**
- Start with complete environment setup
- Create project structure from scratch
- Install all dependencies
- Set up development tools

**For EXISTING projects (feature additions):**
- Skip redundant setup tasks
- Focus on feature-specific implementation
- Integrate with existing test suite
- Use established build processes
- Only add missing dependencies

Task generation will be context-aware:

### 3.1 Task Structure / タスク構造
Each task will include:
- **Task ID**: Unique identifier for tracking
- **Task Name**: Clear, actionable description
- **Dependencies**: Other tasks that must be completed first
- **Estimated Time**: Hours/days for completion
- **Acceptance Criteria**: How to verify completion
- **Implementation Notes**: Technical details and tips

### 3.2 Task Categories / タスクカテゴリー
1. **Environment Setup** / 環境構築
   - Project initialization
   - Dependency installation
   - Configuration setup

2. **Infrastructure** / インフラストラクチャ
   - Database setup
   - Cache implementation
   - Logging configuration

3. **Core Components** / コアコンポーネント
   - Data layer implementation
   - Service layer implementation
   - API development

4. **Business Logic** / ビジネスロジック
   - Feature implementation
   - Algorithm development
   - Integration work

5. **UI Development** / UI開発
   - Component creation
   - Layout implementation
   - Internationalization

6. **Testing** / テスト
   - Unit test creation
   - Integration testing
   - Performance testing

7. **Documentation** / ドキュメント
   - API documentation
   - User guides
   - Deployment docs

### 3.3 Task Prioritization / タスク優先順位
Tasks will be ordered by:
1. Technical dependencies (what needs to be built first)
2. Critical path items (what blocks other work)
3. Risk level (complex items tackled early)
4. Business value (high-impact features)

## Step 4: Save Tasks Document / タスク文書の保存

The tasks.md will be saved in the same directory as design.md:
`.specs/[feature-name]/tasks.md`

The document will include:
- Numbered task list with clear dependencies
- Gantt chart representation (ASCII)
- Sprint/milestone suggestions
- Command examples for common operations
- Bilingual descriptions