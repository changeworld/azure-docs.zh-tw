---
author: baanders
description: 包含 Azure Digital Twins 的檔案 - 設定 Cloud Shell 和 IoT 延伸模組
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: 4aa016294f0ef3bd26f7f3ef6fa374e9367b672d
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/24/2020
ms.locfileid: "85296962"
---
[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]

### <a name="set-up-cloud-shell-session"></a>設定 Cloud Shell 工作階段

開啟 Cloud Shell 視窗之後，第一件事就是登入，並設定此工作階段的訂用帳戶 Shell 內容。 在您的 Cloud Shell 中執行下列命令：

```azurecli
az login
az account set --subscription <your-Azure-subscription-ID>
```

如果這是您第一次使用此包含 Azure Digital Twins 的訂用帳戶，請執行此命令以向 Azure Digital Twins 命名空間註冊。 (如果不確定，您可以再執行一次，即使以前執行過也沒關係。)

```azurecli
az provider register --namespace 'Microsoft.DigitalTwins'
```

接下來，您會將 [**Azure CLI 的 Microsoft Azure IoT 擴充功能**](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot?view=azure-cli-latest)新增至您的 Cloud Shell，以啟用可與 Azure Digital Twins 和其他 IoT 服務互動的命令。 使用此命令來新增擴充功能：

   ```azurecli-interactive
   az extension add --name azure-iot
   ```

如果您過去已安裝了此擴充功能，輸出可能會顯示「已安裝擴充功能 'azure-iot'」。 如果發生這種情況，請執行下列動作以確定您有最新的更新： 

   ```azurecli-interactive
   az extension update --name azure-iot
   ```

現在，您已準備好在 Cloud Shell 中使用 Azure Digital Twins。

您可以隨時執行 `az dt -h` 來查看可用的頂層 Azure Digital Twins 命令清單，以確認這一點。