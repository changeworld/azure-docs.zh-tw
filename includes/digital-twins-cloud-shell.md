---
author: baanders
description: 包含 Azure Digital Twins 的檔案 - 設定 Cloud Shell 和 IoT 延伸模組
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: 6f472865c131b873f1ae0a21fa9ec55865fb2b29
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2020
ms.locfileid: "86277904"
---
[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]

### <a name="set-up-cloud-shell-session"></a>設定 Cloud Shell 工作階段

開啟 Cloud Shell 視窗之後，第一件事就是登入，並設定此工作階段的訂用帳戶 Shell 內容。 在您的 Cloud Shell 中執行下列命令：

```azurecli
az login
az account set --subscription <your-Azure-subscription-ID>
```
> [!TIP]
> 您也可以使用您的訂用帳戶名稱來設定訂用帳戶。 請使用此命令： 
> ```azurecli
> az account set --subscription "your-Azure-subscription-name"
> 
如果這是您第一次使用此包含 Azure Digital Twins 的訂用帳戶，請執行此命令以向 Azure Digital Twins 命名空間註冊。 (如果不確定，您可以再執行一次，即使以前執行過也沒關係。)

```azurecli
az provider register --namespace 'Microsoft.DigitalTwins'
```

接下來，您會將 [**Azure CLI 的 Microsoft Azure IoT 擴充功能**](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot?view=azure-cli-latest)新增至您的 Cloud Shell，以啟用可與 Azure Digital Twins 和其他 IoT 服務互動的命令。 

首先，執行此命令來查看已安裝的所有延伸模組清單。

```azurecli-interactive
az extension list
```

在輸出中，尋找 `"name"` 每個清單專案的欄位，以查看擴充功能的名稱。

使用輸出來判斷要針對延伸模組安裝執行下列哪一個命令 (您可以執行一個以上的) 。
* 如果清單包含 `azure-iot` ：您已有此延伸模組。 執行此命令以確定您有最新的更新：

   ```azurecli-interactive
   az extension update --name azure-iot
   ```

* 如果清單**不包含** `azure-iot` ：您必須安裝延伸模組。 請使用此命令：

    ```azurecli-interactive
    az extension add --name azure-iot
    ```

* 如果清單包含 `azure-iot-cli-ext` ：這是延伸模組的舊版版本。 一次只能安裝一個延伸模組版本，因此您應該卸載舊版的延伸模組。 請使用此命令：

   ```azurecli-interactive
   az extension remove --name azure-cli-iot-ext
   ```

現在，您已準備好在 Cloud Shell 中使用 Azure Digital Twins。

您可以隨時執行 `az dt -h` 來查看可用的頂層 Azure Digital Twins 命令清單，以確認這一點。