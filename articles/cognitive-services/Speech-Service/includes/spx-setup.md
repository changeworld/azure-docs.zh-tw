---
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/15/2020
ms.author: v-demjoh
ms.openlocfilehash: 87af99d5136a8bed93854e9396895cddeb22549a
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/17/2021
ms.locfileid: "98540468"
---
## <a name="download-and-install"></a>下載並安裝

#### <a name="windows-install"></a>[Windows 安裝](#tab/windowsinstall)

請遵循下列步驟，在 Windows 上安裝語音 CLI：

1. 在 Windows 上，您需根據平台來選擇[適用於 Visual Studio 2019 的 Microsoft Visual C++ 可轉散發套件](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)。 第一次安裝時可能需要重新開機。
2. 下載語音 CLI [zip 封存](https://aka.ms/speech/spx-zips.zip)，然後將其解壓縮。
3. 移至 `spx-zips` 的解壓縮目錄。 此資料夾包含適用於各種平台上之語音 CLI 的程式檔。 
4. 針對您的平台 (適用於 .NET Framework 4.7 的 `spx-net471` 或 x64 CPU 上適用於 .NET Core 3.0 的 `spx-netcore-win-x64`) 將檔案解壓縮。 請記住，您將從此目錄執行 `spx`。

### <a name="run-the-speech-cli"></a>執行語音 CLI

1. 開啟命令提示字元或 PowerShell，然後瀏覽至您解壓縮語音 CLI 的目錄。  
2. 輸入 `spx` 以查看語音 CLI 的說明命令。

> [!NOTE]
> 在尋找命令時，Powershell 不會檢查本機目錄。 在 Powershell 中，將目錄變更為 `spx` 的位置，然後輸入 `.\spx` 來呼叫工具。
> 如果您將此目錄新增至您的路徑，Powershell 和 Windows 命令提示字元會從任何目錄尋找 `spx`，但不包括 `.\` 前置詞。

### <a name="font-limitations"></a>字型限制

在 Windows 上，語音 CLI 只能顯示本機電腦上的命令提示字元可用的字型。
[Windows 終端機](https://www.microsoft.com/en-us/p/windows-terminal/9n0dx20hk701) 支援語音 CLI 以互動方式產生的所有字型。

如果您輸出至檔案，像是記事本或網頁瀏覽器 (例如 Microsoft Edge) 的文字編輯器也可以顯示所有字型。

#### <a name="linux-install"></a>[Linux 安裝](#tab/linuxinstall)

請遵循下列步驟，在 x64 CPU 的 Linux 上安裝語音 CLI：

1. 安裝 [.NET Core 3.0](https://dotnet.microsoft.com/download/dotnet-core/3.0)。
2. 下載語音 CLI [zip 封存](https://aka.ms/speech/spx-zips.zip)，然後將其解壓縮。
3. 移至您從下載解壓縮的根目錄 `spx-zips`，並將 `spx-netcore-30-linux-x64` 解壓縮至新的 `~/spx` 目錄。
4. 在終端機中，輸入下列命令：
   1. `cd ~/spx`
   2. `sudo chmod +r+x spx`
   3. `PATH=~/spx:$PATH`

輸入 `spx` 以查看語音 CLI 的說明。

#### <a name="docker-install-windows-linux-macos"></a>[Docker 安裝 (Windows、Linux、macOS)](#tab/dockerinstall)

請遵循下列步驟，在 Docker 容器中安裝語音 CLI：

1. 為您的平台<a href="https://www.docker.com/get-started" target="_blank">安裝 Docker Desktop<span class="docon docon-navigate-external x-hidden-focus"></span></a> (如果尚未安裝)。
2. 在新的命令提示字元或終端機中，輸入下列命令：
   ```console   
   docker pull msftspeech/spx
   ```
3. 輸入此命令。 您應該會看到語音 CLI 的說明資訊：
   ```console 
   docker run -it --rm msftspeech/spx help
   ```

### <a name="mount-a-directory-in-the-container"></a>在容器中掛接目錄

語音 CLI 工具會將組態設定儲存為檔案，並在執行任何命令 (help 命令除外) 時載入這些檔案。
在 Docker 容器內使用語音 CLI 時，您必須從容器掛接本機目錄，如此一來，工具就可以儲存或尋找組態設定，也可以讀取或寫入命令所需的任何檔案，例如語音的音訊檔案。

在 Windows 上，輸入此命令來建立可供語音 CLI 從容器內使用的本機目錄：

`mkdir c:\spx-data`

或者在 Linux 或 macOS 上，於終端機中輸入此命令來建立目錄，並查看其絕對路徑：

```bash
mkdir ~/spx-data
cd ~/spx-data
pwd
```

當您呼叫語音 CLI 時，將會使用絕對路徑。

### <a name="run-speech-cli-in-the-container"></a>在容器中執行語音 CLI

本文件顯示在非 Docker 安裝中使用的語音 CLI `spx` 命令。
在 Docker 容器中呼叫 `spx` 命令時，您必須將容器中的目錄掛接至您的檔案系統，讓語音 CLI 可以在其中儲存和尋找設定值，並讀取和寫入檔案。

在 Windows 上，命令的開頭會如下所示：

```console
docker run -it -v c:\spx-data:/data --rm msftspeech/spx
```

在 Linux 或 macOS 上，您的命令看起來會如下列範例所示。 以掛接目錄的絕對路徑取代 `ABSOLUTE_PATH`。 上一節的 `pwd` 命令已傳回此路徑。 

如果您在設定金鑰和區域之前執行此命令，則會收到錯誤，告知您要設定金鑰和區域：
```console   
sudo docker run -it -v ABSOLUTE_PATH:/data --rm msftspeech/spx
```

若要使用容器中安裝的 `spx` 命令，請一律輸入如上所示的完整命令，後面接著您的要求參數。
例如，在 Windows 上，此命令會設定您的金鑰：

```console
docker run -it -v c:\spx-data:/data --rm msftspeech/spx config @key --set SUBSCRIPTION-KEY
```

若要使用命令列工具進行更多延伸的互動，您可以藉由新增 entrypoint 參數來啟動具有互動式 Bash 殼層的容器。
在 Windows 上，輸入此命令來啟動會公開互動式命令列介面的容器，您可以在其中輸入多個 `spx` 命令：
```console
docker run -it --entrypoint=/bin/bash -v c:\spx-data:/data --rm msftspeech/spx
```

> [!WARNING]
> 在 Docker 容器內執行語音 CLI 時，無法使用電腦的喇叭。 不過，您可以在本機掛接的目錄中讀取及儲存音訊檔案。 

<!-- Need to troubleshoot issues with docker pull image

### Optional: Create a command line shortcut

If you're running the the Speech CLI from a Docker container on Linux or macOS you can create a shortcut. 

Follow these instructions to create a shortcut:
1. Open `.bash_profile` with your favorite text editor. For example:
   ```shell
   nano ~/.bash_profile
   ```
2. Next, add this function to your `.bash_profile`. Make sure you update this function with the correct path to your mounted directory:
   ```shell   
   spx(){
       sudo docker run -it -v ABSOLUTE_PATH:/data --rm msftspeech/spx
   }
   ```
3. Source your profile:
   ```shell
   source ~/.bash_profile
   ```
4. Now instead of running `sudo docker run -it -v ABSOLUTE_PATH:/data --rm msftspeech/spx`, you can just type `spx` followed by arguments. For example: 
   ```shell
   // Get some help
   spx help recognize

   // Recognize speech from an audio file 
   spx recognize --file /mounted/directory/file.wav
   ```

> [!WARNING]
> If you change the mounted directory that Docker is referencing, you need to update the function in `.bash_profile`.
--->
***

## <a name="create-subscription-config"></a>建立訂用帳戶設定

若要開始使用語音 CLI，您必須輸入語音訂用帳戶金鑰和區域識別碼。 請依照[免費試用語音服務](../overview.md#try-the-speech-service-for-free)中的步驟，來取得這些認證。
一旦您擁有訂用帳戶金鑰和區域識別碼 (例如， `eastus`、`westus`)，請執行下列命令。

```console
spx config @key --set SUBSCRIPTION-KEY
spx config @region --set REGION
```

現在會儲存您的訂用帳戶驗證以供未來的 SPX 要求之用。 如果您需要移除其中一個儲存的值，請執行 `spx config @region --clear` 或 `spx config @key --clear`。
