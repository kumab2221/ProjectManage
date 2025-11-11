# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**ProjectManage** - ClaudeCodeを主軸に置いたAI駆動開発のプロセスを定義する
(A project to define AI-driven development processes centered on ClaudeCode)

This is a Python-based project currently in its initial setup phase.

## Development Commands

Since this project is in early stages, the following are standard Python development commands that will likely be used once the project structure is established:

**Virtual Environment Setup:**
```bash
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate
```

**Installing Dependencies:**
```bash
pip install -r requirements.txt  # Once requirements.txt exists
# or
pip install -e .  # If setup.py/pyproject.toml exists
```

**Running Tests:**
```bash
pytest                    # Run all tests
pytest tests/test_file.py # Run specific test file
pytest -v                 # Verbose output
pytest -k "test_name"     # Run specific test by name
```

**Code Quality:**
```bash
ruff check .              # Lint with ruff (if configured)
black .                   # Format with black (if configured)
mypy .                    # Type checking (if configured)
```

## Project Structure

This repository is currently minimal with only:
- README.md - Project description
- .gitignore - Python-specific ignores

The project structure will evolve as the AI-driven development process documentation and tooling are added.
- 本プロジェクトはアプリ開発フロー.mdの全体フローに沿って、チーム開発におけるプロセス定義する（ルール化する）。また、プロセスの成果物（要件定義書、ADR、設計書、テストレポートなど）についてもテンプレートを定義する。その中で、以下は絶対的なルールとする\
1. 見出し構造（#, ##, ###）は絶対に変更しないこと
2. 見出しの追加・削除・順序変更は禁止\
3. 内容のみを記入すること