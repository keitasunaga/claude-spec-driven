---
description: Generate technical design document from requirements - creates architecture, class design, and data flow
---

# Spec-Driven Design Generator / 仕様駆動設計ジェネレーター

## Step 1: Locate Requirements / 要件の特定

I'll find and analyze the requirements to create a technical design.

Input: "$ARGUMENTS"

!echo "Looking for requirements based on: $ARGUMENTS"

First, let me find the appropriate spec directory:

!find .specs -name "requirement.md" -type f | grep -i "$ARGUMENTS" || find .specs -type d -name "*$ARGUMENTS*"

## Step 2: Analyze Existing Project Structure / 既存プロジェクト構造の分析

Let me check if this is an existing project or a new one:

!find . -name "package.json" -o -name "requirements.txt" -o -name "pom.xml" -o -name "go.mod" -o -name "Cargo.toml" | head -5

If this is an existing project, I'll analyze:
- Current directory structure and file organization
- Existing technology stack and dependencies
- Code patterns and architectural decisions
- Available integration points

!ls -la src/ 2>/dev/null || ls -la app/ 2>/dev/null || ls -la lib/ 2>/dev/null || echo "No standard source directories found"

## Step 3: Read Requirements / 要件の読み込み

Once I locate the requirement.md file, I'll analyze:
- Functional requirements and their complexity
- Technical constraints and preferences
- Performance and scalability needs
- Integration requirements
- How new features fit into existing architecture

@.specs/*/requirement.md

## Step 4: Generate Technical Design / 技術設計の生成

Based on the requirements AND existing project analysis, I'll create a design that:

**For NEW projects:**
- Define complete architecture from scratch
- Choose optimal technology stack
- Create standard project structure
- Set up all infrastructure components

**For EXISTING projects (feature additions):**
- Integrate with current architecture patterns
- Reuse existing services and utilities
- Follow established coding conventions
- Minimize changes to existing code
- Add only necessary new dependencies

The design document will adapt based on project context:

### 3.1 Architecture Overview / アーキテクチャ概要
- High-level system architecture
- Component interaction diagram
- Data flow visualization

### 3.2 Technology Stack / 技術スタック
Detailed technology choices with specific versions:

- **Language / 言語**: 
  - Primary: [Language v.X.X]
  - Secondary: [if applicable]
  
- **Framework / フレームワーク**:
  - Web: [Framework v.X.X]
  - API: [Framework v.X.X]
  - Testing: [Framework v.X.X]
  
- **Libraries / ライブラリ**:
  - Core dependencies with versions
  - Purpose and rationale for each
  - Security/performance considerations
  
- **Development Tools / 開発ツール**:
  - Build: [Tool v.X.X]
  - Test: [Tool v.X.X]  
  - Lint: [Tool v.X.X]
  - Package Manager: [Tool v.X.X]
  
- **Infrastructure / インフラ**:
  - Database: [DB v.X.X]
  - Cache: [Cache v.X.X]
  - Container: [Docker v.X.X]

### 3.3 Module and Class Design / モジュール・クラス設計
For each major component:
- Purpose and responsibilities
- Public interfaces (methods, APIs)
- Dependencies and interactions
- Error handling strategies

### 3.4 Data Design / データ設計
- Data models and schemas
- Database design (if applicable)
- Data flow between components
- Storage and caching strategies

### 3.5 Technical Decisions / 技術的決定事項
- Framework and library choices with rationale
- Design patterns to be used
- Performance optimization strategies
- Security considerations

### 3.6 Implementation Guidelines / 実装ガイドライン
- Coding standards and conventions
- Testing strategies
- Deployment considerations
- Monitoring and logging approach

## Step 4: Save Design Document / 設計書の保存

The design.md will be saved in the same directory as requirement.md:
`.specs/[feature-name]/design.md`

The document will include:
- Bilingual sections (Japanese/English)
- Diagrams in ASCII or Mermaid format
- Clear mapping to requirements
- Revision history