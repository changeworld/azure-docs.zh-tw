---
author: dominicbetts
ms.author: dominicbetts
ms.service: iot-pnp
ms.topic: include
ms.date: 11/15/2019
ms.openlocfilehash: 6cd983b6421fc821853aa22d4dc2c297f672d292
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87336885"
---
## <a name="clean-up-resources"></a>清除資源

如果您打算繼續參閱其他關於 IoT 隨插即用的文章，您可以保留在此文章中使用的資源，並加以重複使用。 否則，您可以刪除自己在此文章中建立的資源，以避免產生額外費用。

您可以藉由使用下列 Azure CLI 命令刪除整個資源群組，同時刪除中樞和已註冊的裝置。 如果這些資源與您想要保留的其他資源共用同一個資源群組，請不要使用此命令。

```azurecli-interactive
az group delete --name <YourResourceGroupName>
```

若只要刪除 IoT 中樞，請使用 Azure CLI 執行下列命令：

```azurecli-interactive
az iot hub delete --name <YourIoTHubName>
```

若只要刪除您向 IoT 中樞註冊的裝置身分識別，請使用 Azure CLI 執行下列命令：

```azurecli-interactive
az iot hub device-identity delete --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

您也可以從開發電腦移除複製的範例檔案。
