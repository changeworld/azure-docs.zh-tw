---
title: 安裝和使用 Azure IoT explorer |Microsoft Docs
description: 安裝 Azure IoT explorer 工具，並用它來與連線到 IoT 中樞的 IoT 隨插即用裝置互動。
author: rido-min
ms.author: rmpablos
ms.date: 09/23/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: bf68bdafbb8b6fde187a2d787bb5464e5ece4cb2
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92019149"
---
# <a name="install-and-use-azure-iot-explorer"></a>安裝和使用 Azure IoT explorer

Azure IoT explorer 是一種圖形化工具，可與您的 IoT 隨插即用裝置互動並進行測試。 在本機電腦上安裝此工具之後，您就可以使用它來連線到中樞。 您可以使用此工具來查看裝置正在傳送的遙測資料、使用裝置屬性，以及叫用命令。

本文示範如何：

- 安裝和設定 Azure IoT explorer 工具。
- 使用工具來與您的裝置互動並進行測試。

## <a name="prerequisites"></a>必要條件

若要使用 Azure IoT explorer 工具，您需要：

- Azure IoT 中樞。 有許多方式可將 IoT 中樞新增至 Azure 訂用帳戶，例如 [使用 Azure CLI 建立 iot 中樞](../iot-hub/iot-hub-create-using-cli.md)。 您需要 IoT 中樞連接字串，才能執行 Azure IoT explorer 工具。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 在 IoT 中樞內註冊的裝置。 您可以使用 IoT Explorer 來建立和管理 IoT 中樞內的裝置註冊。

## <a name="install-azure-iot-explorer"></a>安裝 Azure IoT explorer

移至 [Azure IoT explorer 版本](https://github.com/Azure/azure-iot-explorer/releases) ，並展開最新版本的資產清單。 下載並安裝最新版本的應用程式。

>[!Important]
> 更新至2.x 版，以根據下列任何存放庫解析模型 [https://github.com/Azure/iot-plugandplay-models](https://github.com/Azure/iot-plugandplay-models)

## <a name="use-azure-iot-explorer"></a>使用 Azure IoT explorer

針對裝置，您可以連接您自己的裝置，或使用其中一個模擬裝置範例。 請遵循下列 [指示](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples) 來執行模擬裝置範例。

### <a name="connect-to-your-hub"></a>連接到您的中樞

當您第一次執行 Azure IoT explorer 時，系統會提示您輸入 IoT 中樞的連接字串。 加入連接字串之後，請選取 **[連接]**。 您可以使用此工具的設定，藉由更新連接字串來切換至另一個 IoT 中樞。

IoT 隨插即用裝置的模型定義會儲存在公用存放庫、連線的裝置或本機資料夾中。 根據預設，此工具會在公用存放庫和您的連線裝置中尋找您的模型定義。 您可以新增和移除來源，或在 **設定**中設定來源的優先順序：

若要加入來源：

1. 移至 **首頁/IoT 隨插即用設定**
2. 從儲存機制或本機資料夾中，選取 [ **新增** ] 並選擇您的來源。

若要移除來源：

1. 移至 **首頁/IoT 隨插即用設定**
2. 尋找您想要移除的來源。
3. 選取 [ **X** ] 以移除它。

變更來源優先順序：

您可以將其中一個模型定義來源拖放到清單中的不同排名。

### <a name="view-devices"></a>檢視裝置

當此工具連接到您的 IoT 中樞之後，它會顯示 [ **裝置** ] 清單頁面，其中會列出向 iot 中樞註冊的裝置身分識別。 您可以選取清單中的任何專案，以查看詳細資訊。

在 [ **裝置** 清單] 頁面上，您可以：

- 選取 [ **新增** ] 以向您的中樞註冊新裝置。 然後輸入裝置識別碼。 使用預設設定來自動產生驗證金鑰，並啟用與中樞的連線。
- 選取裝置，然後選取 [ **刪除** ] 以刪除裝置身分識別。 在您完成此動作之前，請先檢查裝置詳細資料，確定您刪除的是正確的裝置身分識別。

## <a name="interact-with-a-device"></a>與裝置互動

在 [ **裝置** ] 清單頁面上，選取 [ **裝置識別碼** ] 資料行中的值，以查看已註冊裝置的詳細資料頁面。 每個裝置都有兩個區段： **裝置** 和 **數位**對應項。

### <a name="device"></a>裝置

本節包含裝置身分 **識別**、  **裝置**對應項、 **遙測**、 **直接方法**、 **雲端到裝置訊息**、模組身分 **識別**  索引標籤。

- 您可以在 [**裝置身分識別**] 索引標籤上，查看並更新[裝置身分識別](../iot-hub/iot-hub-devguide-identity-registry.md)資訊。
- 您可以在 [**裝置**對應項] 索引標籤上存取[裝置](../iot-hub/iot-hub-devguide-device-twins.md)對應項資訊。
- 如果裝置已連線並主動傳送資料，您可以在 [**遙測**] 索引標籤上查看[遙測](../iot-hub/iot-hub-devguide-messages-read-builtin.md)。
- 您可以在 [**直接方法**] 索引標籤上的裝置上呼叫[直接方法](../iot-hub/iot-hub-devguide-direct-methods.md)。
- 您可以在 [**雲端到裝置訊息**] 索引標籤上傳送[雲端到裝置訊息](../iot-hub/iot-hub-devguide-messages-c2d.md)。
- 您可以存取 [模組](../iot-hub/iot-hub-devguide-module-twins.md) 對應項資訊。

### <a name="iot-plug-and-play-components"></a>IoT 隨插即用元件

如果裝置使用 **模型識別碼**連線到中樞，此工具會顯示 [ **IoT 隨插即用元件** ] 索引標籤，您可以在其中查看 **模型識別碼**。

如果 **模型識別碼** 可在其中一個已設定的來源（公用存放庫或本機資料夾）中使用，則會顯示元件清單。 選取元件會顯示可用的屬性、命令和遙測。

在 [ **元件** ] 頁面上，您可以看到唯讀屬性、更新可寫入的屬性、叫用命令，以及查看由此元件產生的遙測訊息。

:::image type="content" source="media/howto-use-iot-explorer/components-iot-explorer.png" alt-text="在 Azure IoT 總管中檢視元件":::

#### <a name="properties"></a>屬性

:::image type="content" source="media/howto-use-iot-explorer/properties-iot-explorer.png" alt-text="在 Azure IoT 總管中檢視元件":::

您可以在 [ **屬性] ([唯讀]) ** 索引標籤上，查看介面中定義的唯讀屬性。您可以在 [ **可寫入的) ** ] 索引標籤的 [屬性] (，更新介面中定義的可寫入屬性：

1. 移至 [ **屬性] (可寫入) ** ] 索引標籤。
1. 按一下您想要更新的屬性。
1. 輸入屬性的新值。
1. 預覽要傳送至裝置的承載。
1. 提交變更。

提交變更之後，您可以追蹤更新狀態：「 **同步**」、「 **成功**」或「 **錯誤**」。 當同步完成時，您會在 **報告的屬性** 資料行中看到新的屬性值。 如果您在同步完成之前流覽到其他頁面，此工具仍會在更新完成時通知您。 您也可以使用工具的通知中心來查看通知歷程記錄。

#### <a name="commands"></a>命令

若要將命令傳送至裝置，請移至 [ **命令** ] 索引標籤：

1. 在命令清單中，展開您要觸發的命令。
1. 為命令輸入任何必要的值。
1. 預覽要傳送至裝置的承載。
1. 提交命令。

#### <a name="telemetry"></a>遙測

若要查看所選介面的遙測，請移至其 [ **遙測** ] 索引標籤。

#### <a name="known-issues"></a>已知問題

- IoT Edge 支援：目前的版本不會在裝置清單中顯示 IoT Edge 裝置。
- DTDL 語言功能： IoT Explorer 0.12 未完全符合 DTDL v2 規範，不支援的功能包括：
  - 介面繼承與 `extends`
  - 地圖 (嵌套地圖) 
  - 陣列型別
  - 自訂架構
  - 自訂語義類型

## <a name="next-steps"></a>後續步驟

在此操作說明文章中，您已瞭解如何安裝和使用 Azure IoT explorer 來與您的 IoT 隨插即用裝置互動。 建議的下一個步驟是瞭解如何 [安裝和使用 DTDL authoring tools](howto-use-dtdl-authoring-tools.md)。
