---
title: 使用 Azure CLI 的 Azure IoT 擴展與 IoT 隨插即用預覽設備進行交互 |微軟文檔
description: 安裝 Azure CLI 的 Azure IoT 擴展，並用它來與連接到 IoT 中心的 IoT 隨插即用裝置進行交互。
author: ChrisGMsft
ms.author: chrisgre
ms.date: 12/26/2019
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: b5907c0fb127947e90352e68b2726a22f5afea0d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80234693"
---
# <a name="install-and-use-the-azure-iot-extension-for-the-azure-cli"></a>安裝和使用 Azure CLI 的 Azure IoT 擴展

[Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest)是一種開源跨平臺命令列工具，用於管理 Azure 資源（如 IoT 中心）。 Azure CLI 在 Windows、Linux 和 MacOS 上可用。 Azure CLI 也預先安裝在 Azure[雲外殼](https://shell.azure.com)中。 Azure CLI 允許您管理 Azure IoT 中心資源、設備預配服務實例和連結中心，而無需安裝任何擴展。

Azure CLI 的 Azure IoT 擴展是一個命令列工具，用於與 IoT 隨插即用預覽設備進行交互和測試。 您可以使用擴展：

- 連接到設備。
- 查看設備發送的遙測資料。
- 使用裝置屬性。
- 調用設備命令。

本文示範如何：

- 為 Azure CLI 安裝和配置 Azure IoT 擴展。
- 使用擴展與設備交互和測試設備。
- 使用擴展管理模型存儲庫中的介面。

## <a name="install-azure-iot-extension-for-the-azure-cli"></a>為 Azure CLI 安裝 Azure IoT 擴展

### <a name="step-1---install-the-azure-cli"></a>步驟 1 - 安裝 Azure CLI

按照[安裝說明](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)在環境中設置 Azure CLI。 要使用以下所有命令，Azure CLI 版本必須是版本 2.0.73 或以上。 使用 `az -–version` 進行驗證。

### <a name="step-2---install-iot-extension"></a>步驟 2 - 安裝 IoT 擴展

[IoT 擴充功能讀我檔案](https://github.com/Azure/azure-iot-cli-extension)說明安裝此擴充功能的數種方式。 最簡單的方式就是執行 `az extension add --name azure-iot`。 安裝之後，您可以使用 `az extension list` 來驗證目前安裝的擴充功能，或使用 `az extension show --name azure-iot` 來查看有關 IoT 擴充功能的詳細資料。 若要移除此擴充功能，您可以使用 `az extension remove --name azure-iot`。

## <a name="use-azure-iot-extension-for-the-azure-cli"></a>對 Azure CLI 使用 Azure IoT 擴展

### <a name="prerequisites"></a>Prerequisites

要登錄到 Azure 訂閱，請運行以下命令：

```azurecli
az login
```

> [!NOTE]
> 如果使用 Azure 雲外殼，則會自動登入，無需運行以前的命令。

要對 Azure CLI 使用 Azure IoT 擴展，需要：

- Azure IoT 中樞。 有許多方法可以將 IoT 中心添加到 Azure 訂閱，例如使用[Azure CLI 創建 IoT 中心](../iot-hub/iot-hub-create-using-cli.md)。 您需要 IoT 中心的連接字串來運行 Azure IoT 擴展命令。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

- 在 IoT 中心註冊的設備。 可以使用以下 Azure CLI 命令註冊設備，請確保將`{YourIoTHubName}`和`{YourDeviceID}`預留位置替換為值：

    ```azurecli
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id {YourDeviceID}
    ```

- 某些命令需要公司模型存儲庫的連接字串。 首次[登載為 IoT 門戶的 Azure 認證](howto-onboard-portal.md)時，將創建公司的模型存儲庫。 協力廠商可能會與您共用其模型存儲庫連接字串，以便允許您訪問其介面和模型。

### <a name="interact-with-a-device"></a>與設備交互

您可以使用擴展查看連接到 IoT 中心的 IoT 隨插即用裝置並與之交互。 擴展適用于表示 IoT 隨插即用裝置的數位孿生。

#### <a name="list-devices-and-interfaces"></a>列出設備和介面

列出 IoT 中心上的所有設備：

```azurecli
az iot hub device-identity list --hub-name {YourIoTHubName}
```

列出 IoT 隨插即用裝置註冊的所有介面：

```azurecli
az iot dt list-interfaces --hub-name {YourIoTHubName} --device-id {YourDeviceID}
```

#### <a name="properties"></a>屬性

列出設備上介面的所有屬性和屬性值：

```azurecli
az iot dt list-properties --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --source private --repo-login "{YourCompanyModelRepoConnectionString}"
```

設置讀寫屬性的值：

```azurecli
az iot dt update-property --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface-payload {JSONPayload or FilePath}
```

將設備**感應器**介面上**的名稱**屬性設置為**Contoso**的示例有效負載檔如下所示：

```json
{
  "sensor": {
    "properties": {
      "name": {
        "desired": {
          "value": "Contoso"
        }
      }
    }
  }
}
```

#### <a name="commands"></a>命令

列出設備上介面的所有命令：

```azurecli
az iot dt list-commands --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --source private --repo-login {YourCompanyModelRepoConnectionString}
```

如果沒有參數`--repo-login`，此命令將使用公共模型存儲庫。

調用命令：

```azurecli
az iot dt invoke-command --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --cn {CommandName} --command-payload {CommandPayload or FilePath}
```

#### <a name="digital-twin-events"></a>數位雙胞胎活動

監控來自特定設備和介面的所有 IoT 隨插即用數位孿生事件，並連接到 **$Default**事件中心消費者組：

```azurecli
az iot dt monitor-events --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID}
```

監控來自特定設備和特定消費群體的所有 IoT 隨插即用數位孿生事件：

```azurecli
az iot dt monitor-events --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --consumer-group {YourConsumerGroup}
```

### <a name="manage-interfaces-in-a-model-repository"></a>管理模型存儲庫中的介面

以下命令使用公共 IoT 隨插即用模型存儲庫。 要使用公司模型存儲庫，`--login`請使用模型存儲庫連接字串添加參數。

在公共 IoT 隨插即用模型存儲庫中列出介面：

```azurecli
az iot pnp interface list
```

在公共 IoT 隨插即用模型存儲庫中顯示介面：

```azurecli
az iot pnp interface show --interface {YourInterfaceId}
```

在 IoT 隨插即用公司模型存儲庫中創建介面：

```azurecli
az iot pnp interface create --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

不能直接在公共模型存儲庫中創建介面。

更新 IoT 隨插即用公司模型存儲庫中的介面：

```azurecli
az iot pnp interface update --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

不能直接更新公共模型存儲庫中的介面。

將介面從 IoT 隨插即用公司模型存儲庫發佈到公共模型存儲庫。 此操作使介面不可變：

```azurecli
az iot pnp interface publish --interface {YourInterfaceID} --login {YourCompanyModelRepoConnectionString}
```

只有 Microsoft 合作夥伴才能將介面發佈到公共模型存儲庫。

### <a name="manage-device-capability-models-in-a-model-repository"></a>在模型存儲庫中管理裝置功能模型

以下命令使用公共 IoT 隨插即用模型存儲庫。 要使用公司模型存儲庫，`--login`請使用模型存儲庫連接字串添加參數。

在 IoT 隨插即用公共模型存儲庫中列出裝置功能模型：

```azurecli
az iot pnp capability-model list
```

在 IoT 隨插即用公共模型存儲庫中顯示裝置功能模型：

```azurecli
az iot pnp capability-model show --model {YourModelID}
```

在 IoT 隨插即用公司模型存儲庫中創建裝置功能模型：

```azurecli
az iot pnp capability-model create --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

不能直接在公共模型存儲庫中創建模型。

更新 IoT 隨插即用公司模型存儲庫中的裝置功能模型：

```azurecli
az iot pnp capability-model update --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

不能直接更新公共模型存儲庫中的模型。

將裝置功能模型從 IoT 隨插即用公司模型存儲庫發佈到公共模型存儲庫。 此操作使模型不可變：

```azurecli
az iot pnp capability-model publish --model {YourModelID} --login {YourCompanyModelRepoConnectionString}
```

只有 Microsoft 合作夥伴可以將模型發佈到公共模型存儲庫。

## <a name="next-steps"></a>後續步驟

在本"執行"一文中，您學習了如何安裝和使用 Azure CLI 的 Azure IoT 擴展與隨插即用裝置進行交互。 建議的下一步是學習如何[管理模型](./howto-manage-models.md)。
