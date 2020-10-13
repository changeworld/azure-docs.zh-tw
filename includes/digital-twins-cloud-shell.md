---
author: baanders
description: 包含 Azure Digital Twins 的檔案 - 設定 Cloud Shell 和 IoT 延伸模組
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: b664303d86f8588fc210b11b363b21d523d63295
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87507431"
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

[!INCLUDE [digital-twins-cloud-shell-extensions.md](digital-twins-cloud-shell-extensions.md)]

現在，您已準備好在 Cloud Shell 中使用 Azure Digital Twins。

您可以隨時執行 `az dt -h` 來查看可用的頂層 Azure Digital Twins 命令清單，以確認這一點。