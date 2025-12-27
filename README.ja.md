> 🇺🇸 [English version](README.md)

# Soboro

**Swift Oriented Base for Operating Runtime Objects**

Soboro は、Linux カーネル上でユーザーランド全体を Swift で実装することを目指す実験的 OS プロジェクトです。既存の C ライブラリ（musl、libdrm、libinput など）を活用しながら、型安全でモダンな Swift によるシステムプログラミングの新しい形を提示します。

## プロジェクトビジョン

```
┌─────────────────────────────────────────┐
│         Swift製アプリケーション            │
├─────────────────────────────────────────┤
│         Swift製デスクトップ環境            │
│   (Wayland compositor / WM / UI Kit)    │
├─────────────────────────────────────────┤
│         Swift製システムサービス            │
│   (init / デバイス管理 / ネットワーク)      │
├─────────────────────────────────────────┤
│         Swift薄いラッパー層               │
│   (libdrm / libinput / D-Bus etc.)     │
├─────────────────────────────────────────┤
│              musl libc                  │
├─────────────────────────────────────────┤
│            Linuxカーネル                 │
└─────────────────────────────────────────┘
```

---

## リポジトリ構造

```
soboro/
├── README.md
├── CLAUDE.md                     # Claude Code 開発ガイドライン
├── CONTRIBUTING.md
├── LICENSE
├── Package.swift                 # ルートマニフェスト（ワークスペース）
│
├── docs/                         # ドキュメント
│   ├── architecture/             # アーキテクチャ設計書
│   │   ├── ja/                   # 日本語版
│   │   └── en/                   # 英語版
│   ├── api/                      # API リファレンス
│   ├── guides/                   # 開発ガイド
│   │   ├── ja/
│   │   └── en/
│   └── decisions/                # ADR (Architecture Decision Records)
│       ├── ja/
│       └── en/
│
├── core/                         # コアシステム
│   ├── soboro-init/              # init システム (PID 1)
│   │   ├── Package.swift
│   │   └── Sources/
│   ├── soboro-service/           # サービス管理デーモン
│   ├── soboro-log/               # ログシステム
│   └── soboro-config/            # システム設定管理
│
├── bindings/                     # C ライブラリバインディング
│   ├── CLinux/                   # Linux syscall / ioctl
│   ├── CDrm/                     # libdrm
│   ├── CInput/                   # libinput
│   ├── CWayland/                 # wayland-server
│   ├── CDBus/                    # D-Bus
│   └── CUdev/                    # libudev
│
├── libs/                         # 共有ライブラリ
│   ├── SoboroFoundation/         # 基盤ユーティリティ
│   ├── SoboroIPC/                # プロセス間通信
│   ├── SoboroNetwork/            # ネットワーク管理
│   ├── SoboroDevice/             # デバイス抽象化
│   └── SoboroGraphics/           # グラフィックス抽象化
│
├── services/                     # システムサービス
│   ├── soboro-deviced/           # デバイス管理 (udev相当)
│   ├── soboro-networkd/          # ネットワーク管理
│   ├── soboro-sessiond/          # セッション管理
│   └── soboro-powerd/            # 電源管理
│
├── desktop/                      # デスクトップ環境
│   ├── soboro-compositor/        # Wayland compositor
│   ├── soboro-shell/             # デスクトップシェル
│   ├── soboro-panel/             # パネル / タスクバー
│   └── SoboroUI/                 # UI ツールキット
│
├── apps/                         # 基本アプリケーション
│   ├── soboro-terminal/          # ターミナルエミュレータ
│   ├── soboro-files/             # ファイルマネージャ
│   ├── soboro-settings/          # 設定アプリ
│   └── soboro-editor/            # テキストエディタ
│
├── tools/                        # 開発・運用ツール
│   ├── soboro-pkg/               # パッケージマネージャ
│   ├── soboro-build/             # ビルドシステム拡張
│   └── soboro-debug/             # デバッグツール
│
├── tests/                        # 統合テスト
│   ├── integration/
│   └── e2e/
│
└── dist/                         # ディストリビューション
    ├── rootfs/                   # ルートファイルシステム構成
    ├── kernel/                   # カーネル設定
    └── images/                   # ビルド済みイメージ
```

---

## 命名規則

### パッケージ・モジュール

| 種別 | 形式 | 例 |
|------|------|-----|
| 実行可能バイナリ | `soboro-{name}` (kebab-case) | `soboro-init`, `soboro-compositor` |
| ライブラリ | `Soboro{Name}` (PascalCase) | `SoboroFoundation`, `SoboroUI` |
| C バインディング | `C{Name}` (PascalCase) | `CDrm`, `CWayland` |
| デーモン | `soboro-{name}d` | `soboro-deviced`, `soboro-networkd` |

### ソースコード

| 要素 | 形式 | 例 |
|------|------|-----|
| 型 (struct/class/enum) | PascalCase | `ServiceManager`, `DeviceEvent` |
| プロトコル | PascalCase + 形容詞/名詞 | `Configurable`, `DeviceProvider` |
| 関数・メソッド | camelCase | `startService()`, `handleEvent()` |
| 変数・プロパティ | camelCase | `isRunning`, `deviceList` |
| 定数 | camelCase | `defaultTimeout`, `maxRetries` |
| ファイル名 | PascalCase.swift | `ServiceManager.swift` |

### Git ブランチ

| ブランチ | 目的 |
|---------|------|
| `main` | 安定版、常にビルド可能 |
| `develop` | 開発統合ブランチ |
| `feature/{component}/{description}` | 機能開発 |
| `fix/{component}/{description}` | バグ修正 |
| `docs/{description}` | ドキュメント更新 |

例: `feature/init/service-dependency-resolution`, `fix/compositor/cursor-hotspot`

### コミットメッセージ

```
{type}({scope}): {description}

{body}

{footer}
```

type: `feat`, `fix`, `docs`, `refactor`, `test`, `chore`
scope: コンポーネント名 (`init`, `compositor`, `foundation` など)

例:
```
feat(init): add service dependency resolution

Implement topological sort for service startup order.
Services can now declare dependencies via `requires` and `after` fields.

Closes #42
```

---

## 開発スケジュール

### Phase 0: 基盤構築（約6ヶ月）

**目標**: 最小限のSwiftシステムが起動する状態を確立

| マイルストーン | 成果物 |
|---------------|--------|
| 開発環境構築 | ビルドシステム、CI/CD、Cバインディング基盤 |
| 最小init実装 | PID 1として起動、シェル起動可能 |
| 基本サービス管理 | サービスの起動・停止・依存解決 |

**Phase 0 完了条件**:
- QEMUでLinuxカーネル + Soboro initが起動
- シェルプロンプトが表示される
- 基本的なサービス(ログ等)が動作

### Phase 1: システムサービス（約6ヶ月）

**目標**: 実用的なCLI環境の完成

| マイルストーン | 成果物 |
|---------------|--------|
| デバイス管理 | soboro-deviced (udev相当機能) |
| ネットワーク | soboro-networkd (DHCP, DNS, 基本設定) |
| ストレージ・電源 | マウント管理、電源状態管理 |

**Phase 1 完了条件**:
- ネットワーク接続可能
- USB デバイスのホットプラグ対応
- 適切なシャットダウン処理

### Phase 2: グラフィックス基盤（約6ヶ月）

**目標**: 基本的なGUI環境の動作

| マイルストーン | 成果物 |
|---------------|--------|
| DRM/KMS統合 | 画面出力、モード設定 |
| Wayland compositor | 基本的なウィンドウ管理 |
| 入力処理 | キーボード、マウス、タッチ |

**Phase 2 完了条件**:
- Waylandクライアントが描画可能
- ウィンドウの移動・リサイズ
- キーボード・マウス入力が動作

### Phase 3: デスクトップ環境（約6ヶ月）

**目標**: 日常使用可能なデスクトップ

| マイルストーン | 成果物 |
|---------------|--------|
| UIツールキット | SoboroUI基本コンポーネント |
| シェル・パネル | デスクトップシェル、通知 |
| 基本アプリ | ターミナル、ファイルマネージャ、設定 |

**Phase 3 完了条件**:
- デスクトップ環境でログイン可能
- 基本アプリが動作
- システム設定がGUIで変更可能

### Phase 4: エコシステム（継続的）

**目標**: 持続可能なプロジェクト基盤

| 項目 | 内容 |
|------|------|
| パッケージ管理 | soboro-pkg (SwiftPM統合) |
| 開発者ツール | IDE統合、デバッガ、プロファイラ |
| ドキュメント | API リファレンス、チュートリアル |
| コミュニティ | コントリビューションガイド、ガバナンス |

---

## 開発環境セットアップ

### 必要要件

- **Swift 6.2 以上**（Swift 6 の Concurrency 改善、Embedded Swift 対応を活用）
- **OS**: Linux (Ubuntu 24.04+ 推奨)、WSL2、または macOS (クロスコンパイル)
- **QEMU**: テスト用仮想環境
- **依存ライブラリ**: `musl-dev`, `libdrm-dev`, `libinput-dev`, `wayland-protocols` 等

### Linux (Ubuntu / Debian) でのセットアップ

```bash
# リポジトリのクローン
git clone https://github.com/example/soboro.git
cd soboro

# Swift 6.2 のインストール（swiftly推奨）
curl -L https://swiftlang.github.io/swiftly/swiftly-install.sh | bash
swiftly install latest

# 依存関係のインストール
sudo apt update
sudo apt install -y \
    musl-dev musl-tools \
    libdrm-dev libinput-dev \
    wayland-protocols libwayland-dev \
    libdbus-1-dev libudev-dev \
    qemu-system-x86

# ビルド
swift build

# テスト
swift test

# 開発用イメージ作成
./tools/soboro-build/create-image.sh
```

### WSL2 (Windows Subsystem for Linux) でのセットアップ

WSL2 上での開発も完全にサポートしています。

#### 1. WSL2 の準備

```powershell
# PowerShell (管理者権限) で実行
wsl --install -d Ubuntu-24.04
```

#### 2. WSL2 内でのセットアップ

```bash
# WSL2 Ubuntu 内で実行
sudo apt update && sudo apt upgrade -y

# Swift 6.2 のインストール
curl -L https://swiftlang.github.io/swiftly/swiftly-install.sh | bash
swiftly install latest

# 依存関係のインストール（Linux と同様）
sudo apt install -y \
    musl-dev musl-tools \
    libdrm-dev libinput-dev \
    wayland-protocols libwayland-dev \
    libdbus-1-dev libudev-dev

# リポジトリのクローン
git clone https://github.com/example/soboro.git
cd soboro

# ビルド
swift build
```

#### 3. WSL2 での QEMU 実行

WSL2 では GUI を使わない `-nographic` モードが推奨されます。

```bash
qemu-system-x86_64 \
    -kernel dist/kernel/vmlinuz \
    -initrd dist/images/initramfs.img \
    -append "init=/usr/bin/soboro-init console=ttyS0" \
    -nographic \
    -m 1G
```

GUI テストが必要な場合は、WSLg（Windows 11）または X Server（VcXsrv など）を使用してください。

### macOS でのクロスコンパイル

```bash
# Swift 6.2 のインストール（Xcode 16.2+ に含まれる）
xcode-select --install

# Linux SDK のインストール（Swift SDK）
swift sdk install https://download.swift.org/swift-6.2-release/static-sdk/swift-6.2-RELEASE/swift-6.2-RELEASE_static-linux-0.0.1.artifactbundle.tar.gz

# クロスコンパイル
swift build --swift-sdk x86_64-swift-linux-musl
```

### QEMU での実行

```bash
# 開発用イメージで起動
qemu-system-x86_64 \
    -kernel dist/kernel/vmlinuz \
    -initrd dist/images/initramfs.img \
    -append "init=/usr/bin/soboro-init console=ttyS0" \
    -nographic \
    -m 1G
```

---

## 技術スタック

### 言語・ランタイム

| 項目 | 選択 | 理由 |
|------|------|------|
| 言語 | Swift 6.2+ | 完全な Concurrency safety、Embedded Swift、improved actor isolation |
| libc | musl | 静的リンク可能、軽量 |
| ランタイム | Swift Runtime (Linux) | 公式サポート |

### Swift 6 の活用機能

- **Complete Concurrency Checking**: データ競合のコンパイル時検出
- **Actor Isolation**: サービス間の安全な状態管理
- **Typed Throws**: システムエラーの型安全なハンドリング
- **Embedded Swift** (実験的): 将来的な組み込みターゲット対応
- **Swift Testing**: 新しいテストフレームワーク
- **Subprocess** (Swift 6.2): プロセス管理の簡素化

### 主要依存ライブラリ

| ライブラリ | 用途 | バインディング |
|-----------|------|---------------|
| libdrm | GPU / ディスプレイ制御 | `CDrm` |
| libinput | 入力デバイス | `CInput` |
| wayland-server | Wayland プロトコル | `CWayland` |
| D-Bus | プロセス間通信 | `CDBus` |
| libudev | デバイス検出 | `CUdev` |

---

## ドキュメント

ドキュメントは日本語版と英語版の両方を維持しています。

| カテゴリ | 日本語 | English |
|---------|--------|---------|
| アーキテクチャ | `docs/architecture/ja/` | `docs/architecture/en/` |
| 開発ガイド | `docs/guides/ja/` | `docs/guides/en/` |
| ADR | `docs/decisions/ja/` | `docs/decisions/en/` |

---

## ライセンス

MIT License

---

## コントリビューション

CONTRIBUTING.md を参照してください。

Claude Code を使った開発については CLAUDE.md を参照してください。

---

## 参考リソース

- [Swift System](https://github.com/apple/swift-system) - 低レベルシステムAPI
- [SwiftNIO](https://github.com/apple/swift-nio) - 非同期I/O
- [Swift Subprocess](https://github.com/apple/swift-subprocess) - プロセス管理
- [wlroots](https://gitlab.freedesktop.org/wlroots/wlroots) - Wayland compositor ライブラリ
- [systemd](https://systemd.io/) - init システムの参考
- [Wayland Protocol](https://wayland.freedesktop.org/docs/html/) - Wayland 仕様
- [Swift 6.2 Release Notes](https://www.swift.org/blog/swift-6.2-released/) - Swift 6.2 の新機能
