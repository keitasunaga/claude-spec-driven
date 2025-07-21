---
description: Generate bilingual requirements - analyzes existing code and supports both quick mode and interactive mode
---

# Spec-Driven Requirements Generator / 仕様駆動要件ジェネレーター

## Mode Selection / モード選択

Analyzing your input: "$ARGUMENTS"

!echo "Input received: $ARGUMENTS"

## Step 1: Analyze Existing Project / 既存プロジェクトの分析

First, let me check if this is an existing project with source code:

!find . -type f -name "*.py" -o -name "*.js" -o -name "*.ts" -o -name "*.java" -o -name "*.go" | head -20

If source code exists, I'll analyze:
- Current project structure and architecture
- Existing features and functionalities  
- Dependencies and technology stack
- Code patterns and conventions
- Areas that might need enhancement

Based on your input, I'll determine the best approach:

### Quick Mode (Simple Description) / クイックモード（簡単な説明）
If you provided a brief project description (e.g., "株価を分析するPythonアプリ"), I'll:
- Analyze the core concept
- Infer common requirements for this type of application
- Generate comprehensive requirements based on best practices
- Create a complete requirement.md with standard features

### Interactive Mode (Detailed Requirements) / 対話モード（詳細要件）
If you want more control or have specific requirements, I'll guide you through:
1. Project overview and goals
2. Specific functional requirements
3. Technical constraints and preferences
4. User experience considerations

### How to trigger each mode:
- **Quick Mode**: Just describe your project in one sentence
  - Example: "create a todo app" / "TODOアプリを作って"
  - Example: "株価分析ツール" / "stock analysis tool"

- **Interactive Mode**: Add "interactive" or "詳細" to your command
  - Example: "/spec-init interactive"
  - Example: "/spec-init 詳細に聞いて"

---

## Step 2: Determine Spec Directory / 仕様ディレクトリの決定

Based on your input "$ARGUMENTS", I'll create or use an appropriate directory structure:

```
.specs/
├── stock-analysis-app/
│   ├── requirement.md
│   ├── design.md
│   └── tasks.md
├── email-opt-out-feature/
│   ├── requirement.md
│   ├── design.md
│   └── tasks.md
└── user-auth-system/
    ├── requirement.md
    ├── design.md
    └── tasks.md
```

Let me determine the spec directory name from your input:

!echo "Creating spec directory based on: $ARGUMENTS"

The spec files will be organized in: `.specs/[feature-or-app-name]/`

## Step 3: Generating/Updating Requirements / 要件の生成・更新

Based on "$ARGUMENTS" and the analysis above, I'll:

**Directory Creation:**
1. Parse your input to generate a directory name
   - "株価分析アプリ" → `.specs/stock-analysis-app/`
   - "add email opt-out" → `.specs/email-opt-out-feature/`
   - "ユーザー認証システム" → `.specs/user-auth-system/`

2. Create the directory if it doesn't exist:
   ```bash
   mkdir -p .specs/[feature-name]/
   ```

3. Generate requirement.md in that directory:
   ```
   .specs/[feature-name]/requirement.md
   ```

**Benefits of this approach:**
- Each feature/app has its own spec set
- No overwriting of existing specs
- Clear history of all features
- Easy to track what was implemented when
- Can reference other specs for integration

The generated requirement.md will be saved to the appropriate directory!