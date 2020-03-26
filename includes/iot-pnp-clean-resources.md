---
author: baanders
ms.author: baanders
ms.service: iot-pnp
ms.topic: include
ms.date: 11/15/2019
ms.openlocfilehash: d88d166e001e71cabdabc3a3d344adc4da19aa8c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "75453723"
---
## <a name="clean-up-resources"></a>清除資源

如果您打算繼續參閱其他關於 IoT 隨插即用的文章，您可以保留在本快速入門中使用的資源，並加以重複使用。 否則，您可以刪除在本快速入門中建立的資源，以避免產生額外費用。

您可以使用 Azure CLI 的下列命令刪除整個資源群組，以同時刪除中樞和已註冊的裝置。 (不過，如果這些資源與您用於不同用途的其他資源共用同一個資源群組，請不要使用此方式。)

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