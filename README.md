# Infrastructure CI/CD & DevSecOps Pipeline

GitLab CI/CDを活用し、インフラコード（Ansible）の**構文チェック**から**セキュリティ脆弱性スキャン（Trivy）**までを自動化したCI/CDパイプラインのポートフォリオプロジェクトです。

## 🚀 概要
開発の初期段階で不具合やセキュリティリスクを検知する**「Shift Left（シフトレフト）」**の思想に基づき、コードを `git push` した瞬間に自動で品質・セキュリティ診断が行われる仕組みを構築し、デプロイ完了までを作業しました。

---

## 🛠️ 使用技術 (Tech Stack)
* **CI/CD Platform:** GitLab CI/CD (GitLab Runner 19.2.0 / Shell Executor)
* **Configuration Management:** Ansible
* **Security & Vulnerability Scanning:** Trivy (Aquasec)
* **Container Environment:** Docker (Python slim, Trivy official image)
* **Authentication:** Non-passphrase ED25519 SSH Key Pair (`id_ed25519`)
* **Privilege Escalation:** Sudo NOPASSWD Configuration

---

## 📊 パイプラインの構成 (`.gitlab-ci.yml`)

このプロジェクトのパイプラインは、以下の2つのステージで構成されています。

1. **Test Stage (`ansible_syntax_check`)**
   * 軽量なPython環境 (`python:3.10-slim`) を動的に立ち上げ。
   * `ansible-core` をインストールし、プレイブック（`test.yml` 等）の構文エラー（Syntax Error）を自動チェック。
2. **Security Stage (`trivy_security_scan`)**
   * セキュリティスキャナー `Trivy` (`aquasec/trivy:latest`) を使用。
   * インフラ設定ファイル全体をスキャンし、不適切なセキュリティ設定やリスクを検知。
3. **Deploy Stage (`deploy_to_production`)**
   * 実機環境（`vm1-runner`）上の Runner で動作し、Ansible 経由でターゲットサーバー（`192.168.0.100`）へ構成管理タスクを適用。
   * `main` ブランチのみ生成し、誤デプロイ防止のために **`when: manual`（手動実行承認）** を適用。

## 結果
<img width="520" height="580" alt="image" src="https://github.com/user-attachments/assets/32402b6a-4921-40c8-86eb-7f0f1b7ed56d" />

<img width="810" height="140" alt="スクリーンショット 2026-07-28 094419" src="https://github.com/user-attachments/assets/c9213d97-1ffe-4669-90d2-ebd7975c13e8" />


## 💡工夫した点、学び
* 自動化による手戻りの防止: 本番環境へのデプロイ前に、コードの記述ミスやセキュリティリスクを機械的に弾く仕組みを実装しました。
* 軽量なコンテナの活用: 不要なツールを省いたスリムなイメージを使用することで、パイプラインの実行速度と安定性を高めています。
* SSH 認証失敗 (Permission denied) の解消:手動接続時と CI 実行時での SSH 鍵評価順序の違いを特定。専用の非対話型 ED25519 鍵を生成し、ansible.cfg で IdentitiesOnly=yes を強制適用して認証を完全安定化。
* 特権昇格エラー (sudo: パスワードが必要です) の解消:Ansible が root 権限でタスクを実行できるよう、ターゲット側の sudoers.d を適切に設定して無人デプロイを実現。
