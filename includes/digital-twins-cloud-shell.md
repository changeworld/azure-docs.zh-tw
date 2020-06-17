---
author: baanders
description: 包含 Azure Digital Twins 的檔案 - 設定 Cloud Shell 和 IoT 延伸模組
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: 76480959d94b9e2a2e04857c85b7359105e73e4f
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/09/2020
ms.locfileid: "84611470"
---
[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]

### <a name="set-up-cloud-shell-session"></a>設定 Cloud Shell 工作階段

開啟 Cloud Shell 視窗之後，第一件事就是登入，並設定此工作階段的訂用帳戶 Shell 內容。 

```azurecli
az login
az account set --subscription <your-Azure-subscription-ID>
```

如果這是您第一次使用此包含 Azure Digital Twins 的訂用帳戶，請執行此命令以向 Azure Digital Twins 命名空間註冊。 (如果不確定，您可以再執行一次，即使以前執行過也沒關係。)

```azurecli
az provider register --namespace 'Microsoft.DigitalTwins'
```

然後，在您的 Cloud Shell 執行個體中執行下列命令，新增適用於 Azure CLI 的 Microsoft Azure IoT 延伸模組。

   ```azurecli-interactive
   az extension add --name azure-iot
   ```

> [!NOTE]
> 本文使用最新版的 Azure IoT 擴充功能，稱為 `azure-iot`。 舊版則稱為 `azure-iot-cli-ext`。您一次只能安裝一個版本。 您可以使用命令 `az extension list` 來驗證目前安裝的擴充功能。
> 使用 `az extension remove --name azure-cli-iot-ext` 移除舊版的擴充功能。
> 使用 `az extension add --name azure-iot` 新增新版的擴充功能。 若要查看您已安裝的擴充功能，請使用 `az extension list`。

> [!TIP]
> 您可以執行 `az dt -h`，以查看最上層的 Azure Digital Twins 命令。