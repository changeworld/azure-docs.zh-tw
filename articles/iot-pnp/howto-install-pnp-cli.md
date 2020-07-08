---
title: 使用適用於 Azure CLI 的 Azure IoT 延伸模組與 IoT 隨插即用 Preview 裝置互動 |Microsoft Docs
description: 安裝適用於 Azure CLI 的 Azure IoT 延伸模組，並用來與連線到 IoT 中樞的 IoT 隨插即用裝置互動。
author: Philmea
ms.author: philmea
ms.date: 12/26/2019
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 1ccb32996cd8f15805a810dd5b5985aeb5f87c26
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81770449"
---
# <a name="install-and-use-the-azure-iot-extension-for-the-azure-cli"></a>安裝並使用適用於 Azure CLI 的 Azure IoT 延伸模組

[Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) 是開放原始碼的跨平台命令列工具，其用於管理 IoT 中樞等 Azure 資源。 Azure CLI 適用於 Windows、Linux 和 MacOS。 Azure CLI 也會預先安裝在 [Azure Cloud Shell](https://shell.azure.com) 中。 Azure CLI 可供管理 Azure IoT 中樞資源、裝置佈建服務執行個體，以及不需要安裝任何延伸模組的連結中樞。

適用於 Azure CLI 其 Azure IoT 延伸模組是用來與 IoT 隨插即用 Preview 裝置互動和測試的命令列工具。 您可使用此延伸模組來執行下列動作：

- 連線到裝置。
- 檢視裝置所傳送的遙測。
- 使用裝置屬性。
- 呼叫裝置命令。

本文示範如何：

- 安裝並設定適用於 Azure CLI 的 Azure IoT 延伸模組。
- 使用此延伸模組來與裝置互動並加以測試。
- 使用此延伸模組來管理模型存放庫中的介面。

## <a name="install-azure-iot-extension-for-the-azure-cli"></a>安裝適用於 Azure CLI 的 Azure IoT 延伸模組

### <a name="step-1---install-the-azure-cli"></a>步驟 1 - 安裝 Azure CLI

請遵循[安裝指示](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)，以在環境中設定 Azure CLI。 若要使用以下所有命令， 則 Azure CLI 版本必須為 2.0.73 或更新版本。 使用 `az -–version` 進行驗證。

### <a name="step-2---install-iot-extension"></a>步驟 2 - 安裝 IoT 延伸模組

[IoT 擴充功能讀我檔案](https://github.com/Azure/azure-iot-cli-extension)說明安裝此擴充功能的數種方式。 最簡單的方式就是執行 `az extension add --name azure-iot`。 安裝之後，您可以使用 `az extension list` 來驗證目前安裝的擴充功能，或使用 `az extension show --name azure-iot` 來查看有關 IoT 擴充功能的詳細資料。 若要移除此擴充功能，您可以使用 `az extension remove --name azure-iot`。

## <a name="use-azure-iot-extension-for-the-azure-cli"></a>使用適用於 Azure CLI 的 Azure IoT 延伸模組

### <a name="prerequisites"></a>必要條件

若要登入 Azure 訂用帳戶，請執行下列命令：

```azurecli
az login
```

> [!NOTE]
> 如果使用 Azure Cloud Shell，您就會自動登入，而不需要執行上述命令。

若要使用適用於 Azure CLI 的 Azure IoT 延伸模組，您需要：

- Azure IoT 中樞。 有許多方法可將 IoT 中樞新增至 Azure 訂用帳戶，例如[使用 Azure CLI 建立 IoT 中樞](../iot-hub/iot-hub-create-using-cli.md)。 您需要 IoT 中樞的連接字串才能執行 Azure IoT 延伸模組命令。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

- 在 IoT 中樞內註冊的裝置。 您可使用下列 Azure CLI 命令來註冊裝置，請務必以值取代 `{YourIoTHubName}` 和 `{YourDeviceID}` 預留位置：

    ```azurecli
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id {YourDeviceID}
    ```

- 某些命令需要公司模型存放庫的連接字串。 當第一次[在 Azure IoT 認證入口網站上線](howto-onboard-portal.md)時，系統會建立公司的模型存放庫。 您也可以共用第三方模型存放庫連接字串，以存取其介面和模型。

### <a name="interact-with-a-device"></a>與裝置互動

您可使用此延伸模組來檢視連線到 IoT 中樞的 IoT 隨插即用裝置並與其互動。 此延伸模組適用於代表 IoT 隨插即用裝置的數位對應項。

#### <a name="list-devices-and-interfaces"></a>列出裝置和介面

列出 IoT 中樞上的所有裝置：

```azurecli
az iot hub device-identity list --hub-name {YourIoTHubName}
```

列出 IoT 隨插即用裝置所註冊的所有介面：

```azurecli
az iot dt list-interfaces --hub-name {YourIoTHubName} --device-id {YourDeviceID}
```

#### <a name="properties"></a>屬性

列出裝置上介面的所有屬性和屬性值：

```azurecli
az iot dt list-properties --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --source private --repo-login "{YourCompanyModelRepoConnectionString}"
```

設定讀寫屬性的值：

```azurecli
az iot dt update-property --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface-payload {JSONPayload or FilePath}
```

在裝置的 **sensor** 介面上，將 **name** 屬性設定為 **Contoso** 的範例承載檔案會如下所示：

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

列出裝置上介面的所有命令：

```azurecli
az iot dt list-commands --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --source private --repo-login {YourCompanyModelRepoConnectionString}
```

如果沒有 `--repo-login` 參數，則此命令會使用公用模型存放庫。

叫用命令：

```azurecli
az iot dt invoke-command --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --cn {CommandName} --command-payload {CommandPayload or FilePath}
```

#### <a name="digital-twin-events"></a>數位對應項事件

監視來自特定裝置和介面的所有 IoT 隨插即用數位對應項事件，這些裝置和介面會用於 **$Default** 事件中樞取用者群組：

```azurecli
az iot dt monitor-events --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID}
```

監視來自特定裝置的所有 IoT 隨插即用數位對應項事件，這些裝置和介面會用於特定取用者群組：

```azurecli
az iot dt monitor-events --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --consumer-group {YourConsumerGroup}
```

### <a name="manage-interfaces-in-a-model-repository"></a>管理模型存放庫中的介面

下列命令會使用公用 IoT 隨插即用模型存放庫。 若要使用公司模型存放庫，請使用模型存放庫連接字串來新增 `--login` 引數。

列出公用 IoT 隨插即用模型存放庫中的介面：

```azurecli
az iot pnp interface list
```

顯示公用 IoT 隨插即用模型存放庫中的介面：

```azurecli
az iot pnp interface show --interface {YourInterfaceId}
```

在 IoT 隨插即用公司模型存放庫中建立介面：

```azurecli
az iot pnp interface create --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

您無法直接在公用模型存放庫中建立介面。

更新 IoT 隨插即用公司模型存放庫中的介面：

```azurecli
az iot pnp interface update --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

您無法直接更新公用模型存放庫中的介面。

將介面從 IoT 隨插即用公司模型存放庫發佈到公用模型存放庫。 此作業會固定介面：

```azurecli
az iot pnp interface publish --interface {YourInterfaceID} --login {YourCompanyModelRepoConnectionString}
```

只有 Microsoft 合作夥伴可將介面發佈至公用模型存放庫。

### <a name="manage-device-capability-models-in-a-model-repository"></a>管理模型存放庫中的裝置功能模型

下列命令會使用公用 IoT 隨插即用模型存放庫。 若要使用公司模型存放庫，請使用模型存放庫連接字串來新增 `--login` 引數。

列出 IoT 隨插即用公用模型存放庫中的裝置功能模型：

```azurecli
az iot pnp capability-model list
```

顯示 IoT 隨插即用公用模型存放庫中的裝置功能模型：

```azurecli
az iot pnp capability-model show --model {YourModelID}
```

在 IoT 隨插即用公司模型存放庫中建立裝置功能模型：

```azurecli
az iot pnp capability-model create --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

您無法直接在公用模型存放庫中建立模型。

更新 IoT 隨插即用公司模型存放庫中的裝置功能模型：

```azurecli
az iot pnp capability-model update --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

您無法直接更新公用模型存放庫中的模型。

將裝置功能模型從 IoT 隨插即用公司模型存放庫發佈到公用模型存放庫。 此作業會固定模型：

```azurecli
az iot pnp capability-model publish --model {YourModelID} --login {YourCompanyModelRepoConnectionString}
```

只有 Microsoft 合作夥伴可將模型發佈至公用模型存放庫。

## <a name="next-steps"></a>後續步驟

在此操作說明文章中，您已了解如何安裝並使用適用於 Azure CLI 的 Azure IoT 延伸模組，以與隨插即用裝置互動。 下一個建議步驟是了解如何[管理模型](./howto-manage-models.md)。
