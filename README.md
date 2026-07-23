# Infrastructure CI/CD & DevSecOps Pipeline

GitLab CI/CDを活用し、インフラコード（Ansible）の**構文チェック**から**セキュリティ脆弱性スキャン（Trivy）**までを自動化したCI/CDパイプラインのポートフォリオプロジェクトです。

## 🚀 概要
開発の初期段階で不具合やセキュリティリスクを検知する**「Shift Left（シフトレフト）」**の思想に基づき、コードを `git push` した瞬間に自動で品質・セキュリティ診断が行われる仕組みを構築しています。

---

## 🛠️ 使用技術 (Tech Stack)
* **CI/CD:** GitLab CI/CD
* **Configuration Management:** Ansible
* **Security & Vulnerability Scanning:** Trivy (Aquasec)
* **Container Environment:** Docker (Python slim, Trivy official image)

---

## 📊 パイプラインの構成 (`.gitlab-ci.yml`)

このプロジェクトのパイプラインは、以下の2つのステージで構成されています。

1. **Test Stage (`ansible_syntax_check`)**
   * 軽量なPython環境 (`python:3.10-slim`) を動的に立ち上げ。
   * `ansible-core` をインストールし、プレイブック（`test.yml` 等）の構文エラー（Syntax Error）を自動チェック。
2. **Security Stage (`trivy_security_scan`)**
   * セキュリティスキャナー `Trivy` (`aquasec/trivy:latest`) を使用。
   * インフラ設定ファイル全体をスキャンし、不適切なセキュリティ設定やリスクを検知。
