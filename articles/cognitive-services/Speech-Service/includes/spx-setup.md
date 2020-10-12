---
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/15/2020
ms.author: v-demjoh
ms.openlocfilehash: 3c176f103371bfb1b35231f222b2045f1f4a3ef9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91327011"
---
## <a name="download-and-install"></a>下載並安裝

#### <a name="windows-install"></a>[Windows 安裝](#tab/windowsinstall)

請遵循下列步驟，在 Windows 上安裝語音 CLI：

1. 下載語音 CLI [zip 封存](https://aka.ms/speech/spx-zips.zip)，然後將其解壓縮。
2. 移至您從下載解壓縮的根目錄 `spx-zips`，並解壓縮您需要的子目錄 (.NET Framework 4.7 為 `spx-net471`，或在 x64 CPU 上的 .NET Core 3.0 為 `spx-netcore-win-x64`)。

在命令提示字元中，將目錄變更至此位置，然後輸入 `spx` 以查看語音 CLI 的說明。

> [!NOTE]
> 在 Windows 上，語音 CLI 只能顯示本機電腦上的命令提示字元可用的字型。
> [Windows 終端機](https://www.microsoft.com/en-us/p/windows-terminal/9n0dx20hk701) 支援語音 CLI 以互動方式產生的所有字型。
> 如果您輸出至檔案，像是記事本或網頁瀏覽器 (例如 Microsoft Edge) 的文字編輯器也可以顯示所有字型。

> [!NOTE]
> 在尋找命令時，Powershell 不會檢查本機目錄。 在 Powershell 中，將目錄變更為 `spx` 的位置，然後輸入 `.\spx` 來呼叫工具。
> 如果您將此目錄新增至您的路徑，Powershell 和 Windows 命令提示字元會從任何目錄尋找 `spx`，但不包括 `.\` 前置詞。

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

#### <a name="docker-install"></a>[Docker 安裝](#tab/dockerinstall)

> [!NOTE]
> 必須<a href="https://www.docker.com/get-started" target="_blank">為您的平台安裝 Docker Desktop<span class="docon docon-navigate-external x-hidden-focus"></span></a>。

請遵循下列步驟，在 Docker 容器中安裝語音 CLI：

1. 在新的命令提示字元或終端機中，輸入下列命令：`docker pull msftspeech/spx`
2. 輸入此命令。 您應該會看到語音 CLI 的說明資訊：`docker run -it --rm msftspeech/spx help`

### <a name="mount-a-directory-in-the-container"></a>在容器中掛接目錄

語音 CLI 工具會將組態設定儲存為檔案，並在執行任何命令 (help 命令除外) 時載入這些檔案。
在 Docker 容器內使用語音 CLI 時，您必須從容器掛接本機目錄，如此一來，工具就可以儲存或尋找組態設定，也可以讀取或寫入命令所需的任何檔案，例如語音的音訊檔案。

在 Windows 上，輸入此命令來建立可供語音 CLI 從容器內使用的本機目錄：

`mkdir c:\spx-data`

或者在 Linux 或 Mac 上，於終端機中輸入此命令來建立目錄，並查看其絕對路徑：

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

`docker run -it -v c:\spx-data:/data --rm msftspeech/spx`

在 Linux 或 Mac 上，命令的開頭會如下所示：

`sudo docker run -it -v /ABSOLUTE_PATH:/data --rm msftspeech/spx`

> [!NOTE]
> 將 `/ABSOLUTE_PATH` 取代為上一節中 `pwd` 命令所顯示的絕對路徑。

若要使用容器中安裝的 `spx` 命令，請一律輸入如上所示的完整命令，後面接著您的要求參數。
例如，在 Windows 上，此命令會設定您的金鑰：

`docker run -it -v c:\spx-data:/data --rm msftspeech/spx config @key --set SUBSCRIPTION-KEY`

> [!NOTE]
> 在 Docker 容器內執行語音 CLI 時，無法使用電腦的麥克風或喇叭。
> 若要使用這些裝置，請將音訊檔案傳入和傳出語音 CLI，以在 Docker 容器外錄製/播放。
> 語音 CLI 工具可以存取您在上述步驟中設定的本機目錄。

***

## <a name="create-subscription-config"></a>建立訂用帳戶設定

若要開始使用語音 CLI，您必須先輸入語音訂用帳戶金鑰和區域資訊。 請參閱[區域支援](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk)頁面，以尋找您的區域識別碼。 一旦您擁有訂用帳戶金鑰和區域識別碼 (例如， `eastus`、`westus`)，請執行下列命令。

```shell
spx config @key --set SUBSCRIPTION-KEY
spx config @region --set REGION
```

現在會儲存您的訂用帳戶驗證以供未來的 SPX 要求之用。 如果您需要移除其中一個儲存的值，請執行 `spx config @region --clear` 或 `spx config @key --clear`。
