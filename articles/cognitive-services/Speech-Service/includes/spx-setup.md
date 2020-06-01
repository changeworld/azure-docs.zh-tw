---
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/15/2020
ms.author: v-demjoh
ms.openlocfilehash: c57187ae15e5f15a601edbc74921b3c879abe715
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/22/2020
ms.locfileid: "83800683"
---
## <a name="prerequisites"></a>Prerequisites

唯一的先決條件是 Azure 語音訂用帳戶。 如果您還沒有訂用帳戶，請參閱[指南](../get-started.md#new-resource)來建立新的訂用帳戶。

## <a name="download-and-install"></a>下載並安裝

#### <a name="windows-install"></a>[Windows 安裝](#tab/windowsinstall)

請遵循下列步驟，在 Windows 上安裝語音 CLI：

1. 安裝 [.NET Framework 4.7](https://dotnet.microsoft.com/download/dotnet-framework/net471) 或 [.NET Core 3.0](https://dotnet.microsoft.com/download/dotnet-core/3.0)
2. 下載語音 CLI [zip 封存](https://aka.ms/speech/spx-zips.zip)，然後將其解壓縮。
3. 移至您從下載解壓縮的根目錄 `spx-zips`，並解壓縮您需要的子目錄 (.NET Framework 4.7 為 `spx-net471`，或在 x64 CPU 上的 .NET Core 3.0 為 `spx-netcore-win-x64`)。

在命令提示字元中，將目錄變更至此位置，然後輸入 `spx` 以查看語音 CLI 的說明。

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

***

## <a name="create-subscription-config"></a>建立訂用帳戶設定

若要開始使用語音 CLI，您必須先輸入語音訂用帳戶金鑰和區域資訊。 請參閱[區域支援](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk)頁面，以尋找您的區域識別碼。 一旦您擁有訂用帳戶金鑰和區域識別碼 (例如， `eastus`、`westus`)，請執行下列命令。

```shell
spx config @key --set YOUR-SUBSCRIPTION-KEY
spx config @region --set YOUR-REGION-ID
```

現在會儲存您的訂用帳戶驗證以供未來的 SPX 要求之用。 如果您需要移除其中一個儲存的值，請執行 `spx config @region --clear` 或 `spx config @key --clear`。
