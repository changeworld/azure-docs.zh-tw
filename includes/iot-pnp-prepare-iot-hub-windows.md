---
author: baanders
ms.author: baanders
ms.service: iot-pnp
ms.topic: include
ms.date: 10/24/2019
ms.openlocfilehash: 6b6c481a7443953a70fe3db45121f5f9982d8710
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2019
ms.locfileid: "74152001"
---
## <a name="prepare-an-iot-hub"></a>準備 IoT 中樞

您的 Azure 訂用帳戶中也必須要有 Azure IoT 中樞，才能完成本快速入門。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 如果您沒有 IoT 中樞，請依照[下列指示建立一個](../articles/iot-hub/iot-hub-create-using-cli.md)。

> [!IMPORTANT]
> 在公開預覽期間，IoT 隨插即用功能只能在**美國中部**、**歐洲北部**和**日本東部**區域中建立的 IoT 中樞上使用。

執行下列命令，將適用於 Azure CLI 的 Microsoft Azure IoT 擴充功能新增至您的 Cloud Shell 執行個體：

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

執行下列命令，在 IoT 中樞建立裝置身分識別。 將 **YourIoTHubName** 和 **YourDeviceID** 預留位置取代為您自行選擇的「IoT 中樞名稱」  和「裝置識別碼」  。

```azurecli-interactive
az iot hub device-identity create --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

執行下列命令，以針對您剛註冊的裝置取得「裝置連接字串」  (請記下以供後續使用)：

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDevice> --output table
```

執行下列命令，以取得中樞的 _IoT 中樞連接字串_ (請記下以供後續使用)：

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```