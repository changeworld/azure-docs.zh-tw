---
title: 使用適用于 Azure CLI 的 Azure IoT 擴充功能與 IoT 隨插即用裝置互動 |Microsoft Docs
description: 安裝適用於 Azure CLI 的 Azure IoT 延伸模組，並用來與連線到 IoT 中樞的 IoT 隨插即用裝置互動。
author: dominicbetts
ms.author: dobett
ms.date: 07/20/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 680cd4ef4f73c63850a2137b344fd0af6b27c673
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91577453"
---
# <a name="install-and-use-the-azure-iot-extension-for-the-azure-cli"></a>安裝並使用適用於 Azure CLI 的 Azure IoT 延伸模組

[Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest&preserve-view=true) 是開放原始碼的跨平台命令列工具，其用於管理 IoT 中樞等 Azure 資源。 Azure CLI 可在 Windows、Linux 和 macOS 上取得。 Azure CLI 可供管理 Azure IoT 中樞資源、裝置佈建服務執行個體，以及不需要安裝任何延伸模組的連結中樞。

適用於 Azure CLI 的 Azure IoT 延伸模組是一種命令列工具，可用來與 IoT 隨插即用裝置互動並加以測試。 您可使用此延伸模組來執行下列動作：

- 連線到裝置。
- 檢視裝置所傳送的遙測。
- 使用裝置屬性。
- 呼叫裝置命令。

本文示範如何：

- 安裝並設定適用於 Azure CLI 的 Azure IoT 延伸模組。
- 使用此延伸模組來與裝置互動並加以測試。

## <a name="install-azure-iot-extension-for-the-azure-cli"></a>安裝適用於 Azure CLI 的 Azure IoT 延伸模組

### <a name="step-1---install-the-azure-cli"></a>步驟 1 - 安裝 Azure CLI

請遵循[安裝指示](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true)，以在環境中設定 Azure CLI。 為了獲得最佳體驗，您的 Azure CLI 版本應該是2.9.1 版或更新版本。 使用 `az -–version` 進行驗證。

### <a name="step-2---install-iot-extension"></a>步驟 2 - 安裝 IoT 延伸模組

[IoT 擴充功能讀我檔案](https://github.com/Azure/azure-iot-cli-extension)說明安裝此擴充功能的數種方式。 最簡單的方式就是執行 `az extension add --name azure-iot`。 安裝之後，您可以使用 `az extension list` 來驗證目前安裝的擴充功能，或使用 `az extension show --name azure-iot` 來查看有關 IoT 擴充功能的詳細資料。 在撰寫本文時，擴充功能的版本號碼為 `0.10.0` 。

若要移除此擴充功能，您可以使用 `az extension remove --name azure-iot`。

## <a name="use-azure-iot-extension-for-the-azure-cli"></a>使用適用於 Azure CLI 的 Azure IoT 延伸模組

### <a name="prerequisites"></a>必要條件

若要登入 Azure 訂用帳戶，請執行下列命令：

```azurecli
az login
```

若要使用適用於 Azure CLI 的 Azure IoT 延伸模組，您需要：

- Azure IoT 中樞。 有許多方法可將 IoT 中樞新增至 Azure 訂用帳戶，例如[使用 Azure CLI 建立 IoT 中樞](../iot-hub/iot-hub-create-using-cli.md)。 您需要 IoT 中樞的連接字串才能執行 Azure IoT 延伸模組命令。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

- 在 IoT 中樞內註冊的裝置。 您可使用下列 Azure CLI 命令來註冊裝置，請務必以值取代 `{YourIoTHubName}` 和 `{YourDeviceID}` 預留位置：

    ```azurecli
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id {YourDeviceID}
    ```

### <a name="interact-with-a-device"></a>與裝置互動

您可使用此延伸模組來檢視連線到 IoT 中樞的 IoT 隨插即用裝置並與其互動。 此延伸模組適用於代表 IoT 隨插即用裝置的數位對應項。

#### <a name="list-devices"></a>列出裝置

列出 IoT 中樞上的所有裝置：

```azurecli
az iot hub device-identity list --hub-name {YourIoTHubName}
```

#### <a name="view-digital-twin"></a>查看數位對應項

查看裝置的數位對應項：

```azurecli
az iot pnp twin show -n {iothub_name} -d {device_id}
```

#### <a name="properties"></a>屬性

設定讀寫屬性的值：

```azurecli
az iot pnp twin update --hub-name {iothub_name} --device-id {device_id} --json-patch '{"op":"add", "path":"/thermostat1/targetTemperature", "value": 54}'
```

#### <a name="commands"></a>命令

叫用命令：

```azurecli
az iot pnp twin invoke-command --cn getMaxMinReport -n {iothub_name} -d {device_id} --component-path thermostat1
```

#### <a name="digital-twin-events"></a>數位對應項事件

監視來自特定裝置和介面的所有 IoT 隨插即用數位對應項事件，這些裝置和介面會用於 **$Default** 事件中樞取用者群組：

```azurecli
az iot hub monitor-events -n {iothub_name} -d {device_id} -i {interface_id}
```

### <a name="manage-models-in-the-model-repository"></a>管理模型存放庫中的模型

您可以使用 Azure CLI 模型存放庫命令來管理存放庫中的模型。

#### <a name="create-model-repository"></a>建立模型存放庫

如果您是租使用者中的第一個使用者，請為您的租使用者建立新的 IoT 隨插即用公司存放庫：

```azurecli
az iot pnp repo create
```

#### <a name="manage-model-repository-tenant-roles"></a>管理模型存放庫租使用者角色

針對特定資源建立使用者或服務主體的角色指派。

例如，授與 user@consoso.com 租使用者的 **ModelsCreator** 角色：

```azurecli
az iot pnp role-assignment create --resource-id {tenant_id} --resource-type Tenant --subject-id {user@contoso.com} --subject-type User --role ModelsCreator
```

或授 user@consoso.com 與特定模型的 **ModelAdministrator** 角色：

```azurecli
az iot pnp role-assignment create --resource-id {model_id} --resource-type Model --subject-id {user@contoso.com} --subject-type User --role ModelAdministrator
```

#### <a name="create-a-model"></a>建立模型

在公司存放庫中建立新的模型：

```azurecli
az iot pnp model create --model {model_json or path_to_file}
```

#### <a name="search-a-model"></a>搜尋模型

列出符合特定關鍵字的模型：

```azurecli
az iot pnp model list -q {search_keyword}
```

#### <a name="publish-a-model"></a>發佈模型

將位於公司存放庫中的裝置型號發佈至公用存放庫。

例如，將具有識別碼的模型設為公用 `dtmi:com:example:ClimateSensor;1` ：

```azurecli
az iot pnp model publish --dtmi "dtmi:com:example:ClimateSensor;1"
```

若要發佈模型，您必須符合下列需求：

- 公司或組織租使用者必須是 Microsoft 合作夥伴。 
- 使用者或服務主體必須是存放庫租使用者之 **發行者** 角色的成員。

## <a name="next-steps"></a>後續步驟

在此操作說明文章中，您已瞭解如何安裝和使用適用于 Azure CLI 的 Azure IoT 擴充功能，以與您的 IoT 隨插即用裝置互動。 建議的下一個步驟是瞭解如何搭配使用 [Azure IoT explorer 與您的裝置](./howto-use-iot-explorer.md)。
