---
title: 安裝和使用 Azure IoT 資源管理器 |微軟文檔
description: 安裝 Azure IoT 資源管理器工具，並使用它與連接到 IoT 中心的 IoT 隨插即用預覽設備進行交互。
author: miagdp
ms.author: miag
ms.date: 12/27/2019
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 963421fa1ef06599448c9a4197f0d7a6ad2e142d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159178"
---
# <a name="install-and-use-azure-iot-explorer"></a>安裝和使用 Azure IoT 資源管理器

Azure IoT 資源管理器是一種圖形工具，用於與 IoT 隨插即用預覽設備進行交互和測試。 在本地電腦上安裝該工具後，可以使用該工具連接到設備。 您可以使用該工具查看設備發送的遙測資料、使用裝置屬性和調用命令。

本文示範如何：

- 安裝和配置 Azure IoT 資源管理器工具。
- 使用該工具與設備交互和測試設備。

## <a name="prerequisites"></a>Prerequisites

要使用 Azure IoT 資源管理器工具，您需要：

- Azure IoT 中樞。 有許多方法可以將 IoT 中心添加到 Azure 訂閱中，例如[使用 Azure CLI 創建 IoT 中心](../iot-hub/iot-hub-create-using-cli.md)。 您需要 IoT 中心連接字串來運行 Azure IoT 資源管理器工具。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 在 IoT 中心註冊的設備。 可以使用以下 Azure CLI 命令註冊設備。 請務必用值替換`{YourIoTHubName}`和`{YourDeviceID}`預留位置：

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id {YourDeviceID}
    ```

## <a name="install-azure-iot-explorer"></a>安裝 Azure IoT 資源管理器

轉到[Azure IoT 資源管理器版本](https://github.com/Azure/azure-iot-explorer/releases)並展開最新版本的資產清單。 下載並安裝應用程式的最新版本。

## <a name="use-azure-iot-explorer"></a>使用 Azure IoT 資源管理器

對於設備，您可以連接自己的設備，或使用我們的類比示例設備之一。 按照[這些說明](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/iothub_client/samples)運行類比設備示例。

### <a name="connect-to-your-hub"></a>連接到您的中心

首次運行 Azure IoT 資源管理器時，系統會提示您使用 IoT 中心的連接字串。 添加連接字串後，選擇 **"連接**"。 您可以使用該工具的設置通過更新連接字串切換到另一個 IoT 中心。

IoT 隨插即用裝置的模型定義存儲在公共存儲庫、公司存儲庫或連接的設備中。 預設情況下，該工具在公共模型存儲庫和連接的設備中查找模型定義。 您可以在 **"設置"** 中添加和刪除源，或配置源的優先順序：

要添加源：

1. 移至 [設定]****。
1. 選擇 **"新建"** 並選擇源。
1. 如果要添加公司模型存儲庫，請提供連接字串。

要刪除源：

1. 移至 [設定]****。
1. 查找要刪除的源。
1. 選擇**X**將其刪除。 無法刪除公共模型存儲庫，因為公共介面定義來自此存儲庫。

更改源優先順序：

您可以將其中一個模型定義源拖放到清單中的不同排名。 如果存在衝突，排名較高的定義源將覆蓋排名較低的源。

### <a name="view-devices"></a>檢視裝置

該工具連接到 IoT 中心後，將顯示列出在 IoT 中心註冊的設備標識**的設備**清單頁。 您可以展開清單中的任何條目以查看詳細資訊。

在 **"設備"** 清單頁上，您可以：

- 選擇 **"添加"** 可向集線器註冊新設備。 然後輸入裝置識別碼。 使用預設設置自動生成身份驗證金鑰並啟用到集線器的連接。
- 選擇設備，然後選擇 **"刪除**"以刪除設備標識。 在完成此操作之前，請查看設備詳細資訊，以確保刪除正確的設備標識。
- 查詢者`capabilityID``interfaceID`和 。 添加或`capabilityID``interfaceID`作為參數來查詢設備。

## <a name="interact-with-a-device"></a>與設備交互

在"**設備"** 清單頁上，在 **"裝置識別碼"** 列中選擇一個值以查看已註冊設備的詳細資訊頁。 對於每個設備有兩個部分：**設備和****數位孿生**。

### <a name="device"></a>裝置

本節包括**設備標識**、**設備孿生**、**遙測**、**直接方法和****雲到設備消息**選項卡。

- 您可以在 **"設備標識**"選項卡上查看和更新[設備標識](../iot-hub/iot-hub-devguide-identity-registry.md)資訊。
- 您可以在 **"設備孿生"** 選項卡上訪問[設備孿生](../iot-hub/iot-hub-devguide-device-twins.md)資訊。
- 如果設備已連接並主動發送資料，則可以在 **"遙測"** 選項卡上查看[遙測](../iot-hub/iot-hub-devguide-messages-read-builtin.md)資料。
- 您可以在**Direct 方法**選項卡上調用設備上[的直接方法](../iot-hub/iot-hub-devguide-direct-methods.md)。
- 您可以在"**雲到設備消息**"選項卡上發送[雲到設備消息](../iot-hub/iot-hub-devguide-messages-c2d.md)。

### <a name="digital-twin"></a>數位孿生

您可以使用該工具查看設備的數位孿生實例。 對於 IoT 隨插即用裝置，與裝置功能模型關聯的所有介面都顯示在工具的此部分中。 選擇一個介面來擴展其相應的[IoT 隨插即用基元](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL)。

### <a name="interface"></a>介面

在 **"介面"** 頁上，您可以查看介面的 JSON 定義。

#### <a name="properties"></a>屬性

您可以查看在 **"不可寫入屬性**"頁上的介面中定義的唯讀屬性。 您可以更新"**可寫入屬性**"頁上的介面中定義的可寫入屬性：

1. 轉到 **"可寫屬性"** 頁。
1. 按一下要更新的屬性。
1. 輸入屬性的新值。
1. 預覽要發送到設備的有效負載。
1. 提交更改。

提交更改後，可以跟蹤更新狀態：**同步**、**成功**或**錯誤**。 合成完成後，您將在 **"報告屬性"** 列中看到屬性的新值。 如果在合成完成之前導航到其他頁面，該工具仍會在更新完成後通知您。 您還可以使用工具的通知中心查看通知歷史記錄。

#### <a name="commands"></a>命令

要向設備發送命令，請轉到 **"命令"** 頁：

1. 在命令清單中，展開要觸發的命令。
1. 輸入命令的任何必需值。
1. 預覽要發送到設備的有效負載。
1. 提交命令。

#### <a name="telemetry"></a>遙測

要查看所選介面的遙測資料，請訪問其**遙測**頁面。

## <a name="next-steps"></a>後續步驟

在本"執行"一文中，您學習了如何安裝和使用 Azure IoT 資源管理器與 IoT 隨插即用裝置進行交互。 建議的下一步是瞭解如何[安裝和使用 Azure CLI 擴展](./howto-install-pnp-cli.md)。
