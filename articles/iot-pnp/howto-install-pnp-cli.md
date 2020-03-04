---
title: 使用適用于 Azure CLI 的 Azure IoT 擴充功能與 IoT 隨插即用預覽裝置進行互動 |Microsoft Docs
description: 安裝適用于 Azure CLI 的 Azure IoT 擴充功能，並使用它來與連線到 IoT 中樞的 IoT 隨插即用裝置進行互動。
author: ChrisGMsft
ms.author: chrisgre
ms.date: 12/26/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 8dead08017f15a7429655b4bf17b6e8c8e481114
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/03/2020
ms.locfileid: "78251017"
---
# <a name="install-and-use-the-azure-iot-extension-for-the-azure-cli"></a>安裝並使用適用于 Azure CLI 的 Azure IoT 擴充功能

[Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest)是一種開放原始碼跨平臺命令列工具，可用於管理 Azure 資源，例如 IoT 中樞。 Azure CLI 適用于 Windows、Linux 和 MacOS。 Azure CLI 也會預先安裝在[Azure Cloud Shell](https://shell.azure.com)中。 此 Azure CLI 可讓您管理 Azure IoT 中樞資源、裝置布建服務實例，以及連結中樞，而不需要安裝任何延伸模組。

適用于 Azure CLI 的 Azure IoT 擴充功能是用來與 IoT 隨插即用預覽裝置互動和測試的命令列工具。 您可以使用擴充功能來執行下列動作：

- 連接到裝置。
- 查看裝置所傳送的遙測。
- 使用裝置屬性。
- 呼叫裝置命令。

本文示範如何：

- 安裝和設定適用于 Azure CLI 的 Azure IoT 擴充功能。
- 使用延伸模組來與您的裝置互動並加以測試。
- 使用擴充功能來管理模型存放庫中的介面。

## <a name="install-azure-iot-extension-for-the-azure-cli"></a>安裝適用于 Azure CLI 的 Azure IoT 擴充功能

### <a name="step-1---install-the-azure-cli"></a>步驟 1-安裝 Azure CLI

遵循[安裝指示](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)，在您的環境中設定 Azure CLI。 若要使用下列所有命令，您的 Azure CLI 版本必須是2.0.73 或更新版本。 使用 `az -–version` 進行驗證。

### <a name="step-2---install-iot-extension"></a>步驟 2-安裝 IoT 擴充功能

[IoT 擴充功能讀我檔案](https://github.com/Azure/azure-iot-cli-extension)說明安裝此擴充功能的數種方式。 最簡單的方式就是執行 `az extension add --name azure-iot`。 安裝之後，您可以使用 `az extension list` 來驗證目前安裝的擴充功能，或使用 `az extension show --name azure-iot` 來查看有關 IoT 擴充功能的詳細資料。 若要移除此擴充功能，您可以使用 `az extension remove --name azure-iot`。

## <a name="use-azure-iot-extension-for-the-azure-cli"></a>使用適用于 Azure CLI 的 Azure IoT 擴充功能

### <a name="prerequisites"></a>必要條件

若要登入您的 Azure 訂用帳戶，請執行下列命令：

```cmd/sh
az login
```

> [!NOTE]
> 如果您使用 Azure cloud shell，您就會自動登入，而不需要執行先前的命令。

若要使用適用于 Azure CLI 的 Azure IoT 擴充功能，您需要：

- Azure IoT 中樞。 有許多方法可將 IoT 中樞新增至您的 Azure 訂用帳戶，例如[使用 Azure CLI 建立 iot 中樞](../iot-hub/iot-hub-create-using-cli.md)。 您需要 IoT 中樞的連接字串，才能執行 Azure IoT 擴充功能命令。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

    > [!NOTE]
    > 在公開預覽期間，IoT 隨插即用功能只能在**美國中部**、**北歐**和**日本東部**區域中建立的 IoT 中樞上使用。

- 在 IoT 中樞註冊的裝置。 您可以使用下列 Azure CLI 命令來註冊裝置，請務必以您的值取代 `{YourIoTHubName}` 和 `{YourDeviceID}` 預留位置：

    ```cmd/sh
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id {YourDeviceID}
    ```

- 某些命令需要公司模型存放庫的連接字串。 當您第一次登入[Azure IoT 認證入口網站](howto-onboard-portal.md)時，會建立公司的模型存放庫。 協力廠商可能會與您共用其模型存放庫連接字串，讓您能夠存取其介面和模型。

### <a name="interact-with-a-device"></a>與裝置互動

您可以使用此延伸模組來查看連線到 IoT 中樞的 IoT 隨插即用裝置並與其互動。 此延伸模組適用于代表 IoT 隨插即用裝置的數位對應項。

#### <a name="list-devices-and-interfaces"></a>列出裝置和介面

列出 IoT 中樞上的所有裝置：

```cmd/sh
az iot hub device-identity list --hub-name {YourIoTHubName}
```

列出 IoT 隨插即用裝置註冊的所有介面：

```cmd/sh
az iot dt list-interfaces --hub-name {YourIoTHubName} --device-id {YourDeviceID}
```

#### <a name="properties"></a>屬性

列出裝置上介面的所有屬性和屬性值：

```cmd/sh
az iot dt list-properties --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --source private --repo-login "{YourCompanyModelRepoConnectionString}"
```

設定讀寫屬性的值：

```cmd/sh
az iot dt update-property --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface-payload {JSONPayload or FilePath}
```

將裝置**感應器**介面上的**名稱**屬性設定為**Contoso**的範例承載檔案看起來如下：

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

```cmd/sh
az iot dt list-commands --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --source private --repo-login {YourCompanyModelRepoConnectionString}
```

如果沒有 `--repo-login` 參數，此命令會使用公用模型存放庫。

叫用命令：

```cmd/sh
az iot dt invoke-command --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --cn {CommandName} --command-payload {CommandPayload or FilePath}
```

#### <a name="digital-twin-events"></a>數位對應項事件

監視來自特定裝置和介面的所有 IoT 隨插即用數位對應項事件，進入 **$Default**事件中樞取用者群組：

```cmd/sh
az iot dt monitor-events --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID}
```

監視來自特定裝置的所有 IoT 隨插即用數位對應項事件，以及進入特定取用者群組的介面：

```cmd/sh
az iot dt monitor-events --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --consumer-group {YourConsumerGroup}
```

### <a name="manage-interfaces-in-a-model-repository"></a>管理模型存放庫中的介面

下列命令會使用公用 IoT 隨插即用模型存放庫。 若要使用公司模型存放庫，請使用您的模型存放庫連接字串來新增 `--login` 引數。

列出公用 IoT 隨插即用模型存放庫中的介面：

```cmd/sh
az iot pnp interface list
```

顯示公用 IoT 隨插即用模型存放庫中的介面：

```cmd/sh
az iot pnp interface show --interface {YourInterfaceId}
```

在您的 IoT 隨插即用公司模型存放庫中建立介面：

```cmd/sh
az iot pnp interface create --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

您無法直接在公用模型存放庫中建立介面。

更新 IoT 隨插即用公司模型存放庫中的介面：

```cmd/sh
az iot pnp interface update --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

您無法直接更新公用模型存放庫中的介面。

將介面從 IoT 隨插即用公司模型存放庫發佈到公用模型存放庫。 這種作業會使介面變得不變：

```cmd/sh
az iot pnp interface publish --interface {YourInterfaceID} --login {YourCompanyModelRepoConnectionString}
```

只有 Microsoft 合作夥伴可以將介面發行至公用模型存放庫。

### <a name="manage-device-capability-models-in-a-model-repository"></a>管理模型存放庫中的裝置功能模型

下列命令會使用公用 IoT 隨插即用模型存放庫。 若要使用公司模型存放庫，請使用您的模型存放庫連接字串來新增 `--login` 引數。

列出 IoT 隨插即用公用模型存放庫中的裝置功能模型：

```cmd/sh
az iot pnp capability-model list
```

在 IoT 隨插即用公用模型存放庫中顯示裝置功能模型：

```cmd/sh
az iot pnp capability-model show --model {YourModelID}
```

在 IoT 隨插即用公司模型存放庫中建立裝置功能模型：

```cmd/sh
az iot pnp capability-model create --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

您無法直接在公用模型存放庫中建立模型。

更新 IoT 隨插即用公司模型存放庫中的裝置功能模型：

```cmd/sh
az iot pnp capability-model update --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

您無法直接更新公用模型存放庫中的模型。

將裝置功能模型從您的 IoT 隨插即用公司模型存放庫發佈到公用模型存放庫。 這種作業會使模型變得不變：

```cmd/sh
az iot pnp capability-model publish --model {YourModelID} --login {YourCompanyModelRepoConnectionString}
```

只有 Microsoft 合作夥伴可以將模型發行至公用模型存放庫。

## <a name="next-steps"></a>後續步驟

在此操作說明文章中，您已瞭解如何安裝和使用適用于 Azure CLI 的 Azure IoT 擴充功能，以與您的隨插即用裝置互動。 建議的下一個步驟是瞭解如何[管理模型](./howto-manage-models.md)。
