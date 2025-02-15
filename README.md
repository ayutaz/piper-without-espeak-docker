# **Piper TTS Docker 環境のセットアップ**

## **概要**
Piper TTS を Docker で動作させるためのセットアップ手順を説明します。
本リポジトリには、CUDA 版（GPU）と CPU 版の 2 種類の `Dockerfile` が含まれています。

- `Dockerfile.gpu`: **CUDA 12.4 + Python 3.11 を使用した GPU 版の Piper TTS**  
- `Dockerfile.cpu`: **CPU のみで動作する Piper TTS**

このドキュメントでは、それぞれのビルド方法と使用方法について説明します。

---

## **環境構築**

### **1. Docker イメージのビルド**
それぞれの `Dockerfile` を明示的に指定して Docker イメージをビルドします。

#### **GPU 版（CUDA 12.4 対応）**
```sh
docker build -t piper-tts-gpu -f Dockerfile.gpu .
```

#### CPU 版

```sh
docker build -t piper-tts-cpu -f Dockerfile.cpu .
```

### 2. コンテナの実行方法
GPU 版（CUDA 12.4 使用）
以下のコマンドで GPU を使用して音声合成を実行できます。

```powershell
echo "Hello, this is a test." | docker run --rm -i --gpus all -v ${PWD}/data:/data piper-tts-gpu:latest \
     --model /data/en_US-lessac-medium.onnx \
     --config /data/en_US-lessac-medium.onnx.json \
     --output_file /data/test.wav
```

CPU 版
```powershell
echo "Hello, this is a test." | docker run --rm -i -v ${PWD}/data:/data piper-tts-cpu:latest \
     --model /data/en_US-lessac-medium.onnx \
     --config /data/en_US-lessac-medium.onnx.json \
     --output_file /data/test.wav
```

データディレクトリの説明
Docker コンテナが使用する音声モデルや出力ファイルを /data に保存するため、以下のディレクトリ構成を推奨します。

```sh
piper-without-espeak/
├── Dockerfile.gpu
├── Dockerfile.cpu
├── data/                # モデル・出力ファイル保存ディレクトリ
│   ├── en_US-lessac-medium.onnx
│   ├── en_US-lessac-medium.onnx.json
│   └── output.wav
data/ ディレクトリの作成:
```
```sh
mkdir data
```

このフォルダに音声モデルを保存し、コンテナ実行時に -v ${PWD}/data:/data でマウントします。
### 補足

--gpus all をつけることで GPU 版の Piper を実行できます。
CPU 版は GPU のない環境でも動作します。
data/ ディレクトリにモデルファイルがない場合、初回実行時に自動ダウンロードされます。