# Audio Craft

**動画から音声だけを取り出す。音声を好きな形式へまとめて変換する。**  
Audio Craftは、FFmpegを利用した日本語GUIの音声抽出・一括変換ツールです。

![Python 3.10+](https://img.shields.io/badge/Python-3.10%2B-3776AB?logo=python&logoColor=white)
![Platform](https://img.shields.io/badge/Platform-Windows-0078D4?logo=windows&logoColor=white)
![FFmpeg](https://img.shields.io/badge/Powered%20by-FFmpeg-007808?logo=ffmpeg&logoColor=white)

## Audio Craftでできること

- MP4、MOV、MKV、WebMなどの動画から音声だけを抽出
- 元の音声コーデックを維持した、再エンコードなしの無劣化抽出
- 複数ファイルやフォルダー配下のファイルをまとめて並列処理
- FLAC、ALAC、WavPack、WAV、AIFFへの可逆変換
- MP3、Opus、AAC、Ogg Vorbisへの高音質変換
- サンプルレート、チャンネル、ビットレートなどの詳細指定
- タグ、カバー画像、元メタデータをできる限り維持
- 同名ファイルの連番保存、上書き、スキップ
- 失敗した途中ファイルを完成品として残さない安全な出力

すべての処理はPC内で行われます。音声や動画が外部サービスへ送信されることはありません。

## まずは3ステップ

1. 「ファイルを選ぶ」または「フォルダーを選ぶ」で入力を追加します。
2. 目的に合う出力方法を選びます。
3. 「音声をそのまま取り出す」または「○○へ変換する」を押します。

出力先を空欄にすると、元ファイルと同じフォルダーへ新しいファイルとして保存します。元ファイルは削除されません。

### かんたん選択

| 選択肢 | 用途 |
|---|---|
| そのまま抽出 | 元音声を再エンコードせず、最速・無劣化で取り出す |
| FLAC・無劣化 | 音質を維持したまま可逆圧縮する |
| MP3・汎用 | PC、スマートフォン、カーオーディオなどで幅広く再生する |
| AAC・スマホ向け | M4Aとして保存し、スマートフォンなどで利用する |

## 動作環境

- Windows 10 / 11
- Python 3.10以上
- [FFmpeg](https://ffmpeg.org/) と FFprobe
- Pythonパッケージ: [Mutagen](https://mutagen.readthedocs.io/)

> Windowsを主な対象として開発しています。PythonとTkinterが利用できる他のOSでも動作する可能性がありますが、GPU補助や起動方法の一部はWindows向けです。

## インストール

### 1. ファイルを取得

GitHub画面の **Code → Download ZIP** からダウンロードして展開するか、Gitでリポジトリをクローンします。

### 2. Python依存パッケージを導入

プロジェクトフォルダーでPowerShellを開き、次を実行します。

```powershell
python -m pip install -r requirements.txt
```

### 3. FFmpegを用意

`ffmpeg` と `ffprobe` を環境変数 `PATH` に登録するか、実行ファイルを `audio_converter.py` と同じフォルダーへ配置します。

確認方法:

```powershell
python --version
ffmpeg -version
ffprobe -version
```

## 起動方法

Windowsでは [`起動.bat`](起動.bat) をダブルクリックします。

PowerShellから起動する場合:

```powershell
python audio_converter.py
```

音声・動画ファイルやフォルダーを `起動.bat` へドラッグ＆ドロップすると、対象を追加した状態で起動できます。フォルダーを渡した場合は、配下の対応ファイルを再帰的に検索します。

## 対応形式

### 主な入力形式

| 種類 | 形式 |
|---|---|
| 動画 | MP4、MOV、MKV、WebM、AVI、MPEG、M4V、WMV、TS、M2TS、3GPなど |
| 音声 | FLAC、ALAC、WAV、AIFF、WavPack、APE、TAK、TTA、MP3、AAC、M4A、Opus、Ogg、WMA、DSDなど |

実際に読み込める形式は、使用しているFFmpegビルドに依存します。動画に複数の音声トラックがある場合は、先頭の音声トラックを処理します。

### 出力形式と初期設定

| 形式 | 初期設定 | 特性 |
|---|---|---|
| 自動抽出 | 元の音声ストリームをコピー | 再エンコードなし・無劣化 |
| FLAC | compression level 12 | 可逆圧縮 |
| ALAC / M4A | Apple Lossless | 可逆圧縮 |
| WavPack | compression level 8 | 可逆圧縮 |
| WAV / AIFF | 入力に合わせたPCM | 可逆・無圧縮 |
| MP3 | LAME VBR quality 0 | 非可逆圧縮 |
| Opus | 256 kbps VBR / complexity 10 | 非可逆圧縮 |
| AAC / M4A | 320 kbps | 非可逆圧縮 |
| Ogg Vorbis | VBR quality 10 | 非可逆圧縮 |

自動抽出では、AAC / ALACはM4A、MP3はMP3、OpusはOpusなど、コーデックに合う音声ファイルとして保存します。専用の拡張子を割り当てられないコーデックは、原則としてMKAコンテナへ格納します。

## 音質設定

通常は初期設定のままで使用できます。「音質を調整…」から次の項目を変更できます。

| 項目 | 選択範囲 |
|---|---|
| サンプルレート | 入力のまま、5.512～384 kHzの対応値 |
| チャンネル | 入力のまま、モノラル、ステレオ、3 / 4 / 5.1 / 7.1 ch |
| FLAC圧縮レベル | 0～12 |
| WavPack圧縮レベル | 0～8 |
| WAV / AIFFビット深度 | 入力のまま、8 / 16 / 24 / 32 bit |
| MP3 | VBR品質0～9、ABR / CBR 8～320 kbps |
| Opus | 6～512 kbps、VBR / 制約付きVBR / CBR |
| AAC | 8～512 kbps |
| Ogg Vorbis | 品質-1～10（0.5刻み） |

サンプルレートを変更する場合は、SoX Resamplerを高精度設定で使用します。入力と同じサンプルレート・チャンネル数を選ぶのが最も忠実です。FLACとWavPackの圧縮レベルは音質ではなく、圧縮率と処理時間に影響します。

## メタデータとカバー画像

形式を指定して変換する場合は、FFmpegで標準タグをコピーし、Mutagenでカバー画像と元メタデータのスナップショットを出力ファイル内へ保存します。外部JSONや外部カバー画像は作成しません。

元の完全な情報は、ファイル内部の専用タグ `CODEX_ORIGINAL_METADATA` に保存されます。形式に応じてID3、Vorbis Comment、APEv2、MP4タグなどを使用します。

自動抽出では音声ストリームを変更せず、FFmpegが対応する標準メタデータをコピーします。動画の映像トラックや添付画像は出力しません。

## 保存時の安全性

- 通常モードでは必ず元ファイルを残し、新しいファイルとして保存します。
- 出力先が空欄の場合も、元ファイルの隣へ新規保存します。
- 動画ファイルは置換モードを選んでも削除しません。
- 「元ファイルを小さい場合のみ置換」は音声ファイルにだけ適用されます。
- 置換前に再生時間、内部メタデータ、カバー画像を検証します。
- 変換後が元より小さくない場合は結果を破棄し、元ファイルを維持します。
- 変換中は一時ファイルを使用し、失敗やキャンセル時に未完成品を削除します。

重要なファイルを扱う場合は、置換モードを使わず、別の出力先を指定することをおすすめします。

## フォルダー処理

「フォルダーを選ぶ」は、配下の対応ファイルを再帰的に追加します。「追加したフォルダーの階層を出力先にも維持」を有効にすると、選択した最上位フォルダー名を含む階層を出力先へ再現します。

```text
入力: C:\Music\Album\Disc 1\track.flac
選択: C:\Music\Album
出力: <出力先>\Album\Disc 1\track.opus
```

## GPU補助について

音声エンコード自体はCPUで処理します。NVIDIA NVENC、AMD AMF、Intel Quick Syncは、FLAC、MP3、AAC、Opusなどの音声エンコードには利用できません。

GPUを選択した場合は、埋め込みカバー画像のデコードや縮小処理で利用を試みます。利用できない形式やドライバー環境では、自動的にCPU処理へ戻ります。通常はカバー画像が1枚だけのため、GPUによる速度向上は限定的です。

## キーボードショートカット

| 操作 | キー |
|---|---|
| ファイルを追加 | `Ctrl+O` |
| フォルダーを追加 | `Ctrl+Shift+O` |
| 変換・抽出を開始 | `Ctrl+Enter` |
| 選択項目を削除 | `Delete` |
| 一覧を全選択 | `Ctrl+A` |
| 実行中の処理をキャンセル | `Esc` |

入力一覧を右クリックすると、元ファイルの場所を開く、フルパスをコピーする、一覧から削除する操作を選べます。完了一覧はダブルクリック、または右クリックで出力ファイルの場所を開けます。

## よくある質問

### FFmpeg / FFprobeが見つからない

両方が同じFFmpeg配布物に含まれていることを確認してください。`PATH`へ登録するか、`audio_converter.py` と同じフォルダーへ配置してからアプリを再起動します。

### MP3やOpusへの変換に失敗する

使用しているFFmpegに `libmp3lame`、`libopus`、`libvorbis` が含まれているか確認してください。一般的なフルビルドのFFmpegを推奨します。

### FLACへ変換すれば失われた音質は戻る？

戻りません。MP3やAACなどですでに失われた情報は、可逆形式へ変換しても復元できません。

### 自動抽出と変換の違いは？

自動抽出は元の圧縮済み音声データをそのままコピーするため、速く、追加の音質劣化がありません。形式を指定した変換では、音声を一度復号して選択した形式へ再エンコードします。

### 保護された動画も処理できる？

DRMで保護されたコンテンツの解除には対応していません。

## プロジェクト構成

```text
.
├── audio_converter.py   # アプリ本体
├── requirements.txt     # Python依存パッケージ
├── 起動.bat             # Windows用ランチャー
├── docs/
│   └── screenshot.png   # README用スクリーンショット
└── README.md
```

## 開発・動作確認

構文チェック:

```powershell
python -m py_compile audio_converter.py
```

起動確認:

```powershell
python audio_converter.py
```

## コントリビューション

不具合報告や改善提案はGitHub Issuesへお願いします。プルリクエストを送る場合は、変更内容、確認方法、UI変更がある場合はスクリーンショットを添えてください。

## ライセンス

現在、このリポジトリにはライセンスファイルが含まれていません。第三者による利用・改変・再配布の条件を明確にする場合は、公開前に `LICENSE` を追加してください。

## 使用ライブラリ

- [FFmpeg](https://ffmpeg.org/) — 音声・動画の解析、抽出、変換
- [Mutagen](https://mutagen.readthedocs.io/) — 音声メタデータとカバー画像の処理
- [Python / Tkinter](https://docs.python.org/3/library/tkinter.html) — GUI
