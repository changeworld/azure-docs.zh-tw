---
author: baanders
description: 包含 Azure Digital Twins 的檔案 - 設定 Cloud Shell 和 IoT 延伸模組
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: b7c91d648c06970d53799c6ff505919dea17b3c0
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87032184"
---
若要在開啟的 [Azure Cloud Shell](https://shell.azure.com) 視窗中開始使用 Azure Digital Twins，您要做的第一件事就是登入，並針對此工作階段來設定訂用帳戶的殼層內容。 在您的 Cloud Shell 中執行下列命令：

```azurecli
az login
az account set --subscription "<your-Azure-subscription-ID>"
```
> [!TIP]
> 您也可以在上述命令中使用訂用帳戶名稱而不是識別碼。 

如果這是您第一次使用此包含 Azure Digital Twins 的訂用帳戶，請執行此命令以向 Azure Digital Twins 命名空間註冊。 (如果不確定，您可以再執行一次，即使以前執行過也沒關係。)

```azurecli
az provider register --namespace 'Microsoft.DigitalTwins'
```

接下來，您會將 [**Azure CLI 的 Microsoft Azure IoT 擴充功能**](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot?view=azure-cli-latest)新增至您的 Cloud Shell，以啟用可與 Azure Digital Twins 和其他 IoT 服務互動的命令。 

首先，執行此命令來查看已安裝的所有擴充功能清單。

```azurecli-interactive
az extension list
```

其輸出會是您目前擁有的所有擴充功能陣列。 尋找每個清單項目的 `"name"` 欄位，以查看擴充功能的名稱。

使用此輸出來判斷要執行下列哪些命令 (可能會執行多個) 來設定擴充功能。
* 如果清單中包含 `azure-iot`：您已有該擴充功能。 執行此命令以確定您有最新的更新，而且已沒有其他可用的更新：

   ```azurecli-interactive
   az extension update --name azure-iot
   ```

* 如果清單中**未**包含 `azure-iot`：您必須安裝此擴充功能。 使用此命令：

    ```azurecli-interactive
    az extension add --name azure-iot
    ```

* 如果清單中包含 `azure-iot-cli-ext`：這是舊版的擴充功能。 一次只應安裝一個擴充功能版本，因此請解除安裝舊版擴充功能。 使用此命令：

   ```azurecli-interactive
   az extension remove --name azure-cli-iot-ext
   ```

現在，您已準備好在 Cloud Shell 中使用 Azure Digital Twins。

您可以隨時執行 `az dt -h` 來查看可用的頂層 Azure Digital Twins 命令清單，以確認這一點。