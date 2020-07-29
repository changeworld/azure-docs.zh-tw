---
title: 安裝和使用 Azure IoT explorer |Microsoft Docs
description: 安裝 Azure IoT explorer 工具，並使用它來與連線到 IoT 中樞的 IoT 隨插即用預覽裝置互動。
author: rido-min
ms.author: rmpablos
ms.date: 05/06/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 083dcde44e56af34f17d952c46e554b234818f27
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87352160"
---
# <a name="install-and-use-azure-iot-explorer"></a>安裝和使用 Azure IoT explorer

Azure IoT explorer 是一種圖形化工具，可與您的 IoT 隨插即用預覽裝置互動和測試。 在本機電腦上安裝此工具之後，您就可以使用它來連線到中樞。 您可以使用此工具來查看裝置正在傳送的遙測資料、使用裝置屬性，以及叫用命令。

本文示範如何：

- 安裝和設定 Azure IoT explorer 工具。
- 使用此工具來與您的裝置互動並加以測試。

## <a name="prerequisites"></a>先決條件

若要使用 Azure IoT explorer 工具，您需要：

- Azure IoT 中樞。 有許多方法可將 IoT 中樞新增至您的 Azure 訂用帳戶，例如[使用 Azure CLI 建立 iot 中樞](../iot-hub/iot-hub-create-using-cli.md)。 您需要 IoT 中樞連接字串，才能執行 Azure IoT explorer 工具。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 在 IoT 中樞內註冊的裝置。 您可以使用下列 Azure CLI 命令來註冊裝置。 請務必以您的 `{YourIoTHubName}` 值取代和 `{YourDeviceID}` 預留位置：

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id {YourDeviceID}
    ```

## <a name="install-azure-iot-explorer"></a>安裝 Azure IoT explorer

移至[Azure IoT explorer [發行](https://github.com/Azure/azure-iot-explorer/releases)]，並展開最新版本的資產清單。 下載並安裝最新版本的應用程式。

>[!Important]
>從版本0.11.0 中，IoT Explorer 僅支援 IoT 隨插即用2020預覽版本。 若要使用先前預覽版本（2019年8月）中可用的功能，您必須安裝 0.10. x 版。

## <a name="use-azure-iot-explorer"></a>使用 Azure IoT explorer

針對裝置，您可以連接您自己的裝置，或使用我們的其中一個範例模擬裝置。 請遵循[這些指示](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/iothub_client/samples)來執行模擬裝置範例。

### <a name="connect-to-your-hub"></a>連接到您的中樞

第一次執行 Azure IoT explorer 時，系統會提示您輸入 IoT 中樞的連接字串。 新增連接字串之後，請選取 **[連接]**。 藉由更新連接字串，您可以使用此工具的設定來切換至另一個 IoT 中樞。

IoT 隨插即用裝置的模型定義會儲存在公用儲存機制、連接的裝置或本機資料夾中。 根據預設，此工具會在公用存放庫和已連線的裝置中尋找您的模型定義。 您可以新增和移除來源，或在 [**設定**] 中設定來源的優先順序：

若要新增來源：

1. 前往**Home/IoT 隨插即用設定**
2. 選取 [**新增**]，然後從存放庫或本機資料夾選擇您的來源。

若要移除來源：

1. 前往**Home/IoT 隨插即用設定**
2. 尋找您要移除的來源。
3. 選取**X**以將它移除。

變更來源優先順序：

您可以將其中一個模型定義來源拖放到清單中的不同等級。 

### <a name="view-devices"></a>檢視裝置

在此工具連線到您的 IoT 中樞之後，它會顯示 [**裝置**清單] 頁面，其中會列出向 IoT 中樞註冊的裝置身分識別。 您可以選取清單中的任何專案，以查看詳細資訊。

在 [**裝置**清單] 頁面上，您可以：

- 選取 [**新增**] 以向您的中樞註冊新的裝置。 然後輸入 [裝置識別碼]。 使用預設設定來自動產生驗證金鑰，並啟用與中樞的連線。
- 選取裝置，然後選取 [**刪除**] 以刪除裝置身分識別。 完成此動作之前，請先檢查裝置詳細資料，以確定您正在刪除正確的裝置身分識別。

## <a name="interact-with-a-device"></a>與裝置互動

在 [**裝置**清單] 頁面上，選取 [**裝置識別碼**] 欄中的值，以查看已註冊裝置的詳細資料頁面。 每個裝置都有兩個區段： [**裝置**] 和 [**數位**對應項]。

### <a name="device"></a>裝置

本節包含裝置身分**識別**、**裝置**對應項、**遙測**、**直接方法**、**雲端到裝置訊息**、模組身分**識別**索引標籤。

- 您可以在 [**裝置身分識別**] 索引標籤上，查看並更新[裝置身分識別](../iot-hub/iot-hub-devguide-identity-registry.md)資訊。
- 您可以在 [**裝置**對應項] 索引標籤上存取[裝置](../iot-hub/iot-hub-devguide-device-twins.md)對應項資訊。
- 如果裝置已連線並主動傳送資料，您可以在 [**遙測**] 索引標籤上查看[遙測](../iot-hub/iot-hub-devguide-messages-read-builtin.md)。
- 您可以在 [**直接方法**] 索引標籤上呼叫裝置上的[直接方法](../iot-hub/iot-hub-devguide-direct-methods.md)。
- 您可以在 [**雲端到裝置訊息**] 索引標籤上傳送[雲端到裝置訊息](../iot-hub/iot-hub-devguide-messages-c2d.md)。
- 您可以存取[模組](../iot-hub/iot-hub-devguide-module-twins.md)對應項資訊。

### <a name="iot-plug-and-play-components"></a>IoT 隨插即用元件

如果裝置使用**模型識別碼**連線到中樞，此工具會顯示 [ **IoT 隨插即用元件**] 索引標籤 ' '，您可以在其中看到**模型識別碼**。

如果其中一個已設定的來源-公用存放庫或本機資料夾中有可用的**模型識別碼**，則會顯示元件清單。 選取元件會顯示可用的屬性、命令和遙測。

在 [**元件**] 頁面上，您可以查看唯讀屬性、更新可寫入的屬性、叫用命令，以及查看此元件所產生的遙測訊息。

:::image type="content" source="media/howto-use-iot-explorer/components-iot-explorer.png" alt-text="在 Azure IoT explorer 中查看元件":::

#### <a name="properties"></a>屬性

:::image type="content" source="media/howto-use-iot-explorer/properties-iot-explorer.png" alt-text="Azure IoT explorer 中的視圖屬性":::

您可以在 [**屬性（唯讀）** ] 索引標籤上，查看介面中定義的唯讀屬性。您可以在 [**屬性（可寫入）** ] 索引標籤上更新介面中定義的可寫入屬性：

1. 移至 [**屬性（可寫入）** ] 索引標籤。
1. 按一下您要更新的屬性。
1. 輸入屬性的新值。
1. 預覽要傳送至裝置的承載。
1. 提交變更。

提交變更之後，您可以追蹤更新狀態： [**同步**]、[**成功**] 或 [**錯誤**]。 當同步完成時，您會在 [**回報的屬性**] 資料行中看到屬性的新值。 如果您在同步完成之前流覽至其他頁面，此工具仍會在更新完成時通知您。 您也可以使用工具的通知中心查看通知歷程記錄。

#### <a name="commands"></a>命令

若要將命令傳送至裝置，請移至 [**命令**] 索引標籤：

1. 在命令清單中，展開您要觸發的命令。
1. 輸入命令所需的任何值。
1. 預覽要傳送至裝置的承載。
1. 提交命令。

#### <a name="telemetry"></a>遙測

若要查看所選介面的遙測，請移至其 [**遙測**] 索引標籤。

## <a name="next-steps"></a>後續步驟

在此操作說明文章中，您已瞭解如何安裝和使用 Azure IoT explorer，以與您的 IoT 隨插即用裝置互動。 建議的下一個步驟是瞭解如何使用[Azure CLI IoT 隨插即用命令](./howto-use-iot-pnp-cli.md)。
