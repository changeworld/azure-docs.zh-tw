---
title: 監視 IoT Edge 部署 - Azure IoT Edge
description: 包括 edgeHub 和 edgeAgent 報告屬性和自動部署計量的高階監視。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/21/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 4ca22c39fcd7b488f527326e4fdeaa56b0485cb9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84220298"
---
# <a name="monitor-iot-edge-deployments"></a>監視 IoT Edge 部署

Azure IoT Edge 提供報告功能，可讓您監視部署至 IoT Edge 裝置之模組的即時資訊。 IoT 中樞服務會從裝置擷取狀態，並將其提供給操作員使用。 對於包含自動部署和分層部署的[大規模部署](module-deployment-monitoring.md)而言，監視也非常重要。

裝置和模組都有類似的資料 (例如連線能力)，因此會根據裝置識別碼或模組識別碼取得值。

IoT 中樞服務會收集裝置和模組對應項所回報的資料，並提供裝置可能擁有的各種狀態計數。 IoT 中樞服務會將此資料分成四個計量群組：

| 類型 | 描述 |
| --- | ---|
| 目標 | 顯示符合部署目標條件的 IoT Edge 裝置。 |
| 已套用 | 顯示另一個優先順序較高的部署並未設為目標的目標 IoT Edge 裝置。 |
| 回報成功 | 顯示回報模組部署成功的 IoT Edge 裝置。 |
| 報告失敗 | 顯示回報一或多個模組未部署成功的 IoT Edge 裝置。 若要進一步調查此錯誤，請遠端連線到那些裝置並檢視記錄檔。 |

IoT 中樞服務可讓您在 Azure 入口網站和 Azure CLI 中監視此資料。

## <a name="monitor-a-deployment-in-the-azure-portal"></a>在 Azure 入口網站中監視部署

若要檢視部署的詳細資料並監視正在執行該部署的裝置，請使用下列步驟：

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後瀏覽至 IoT 中樞。
1. 從左側窗格功能表中選取 [IoT Edge]。
1. 選取 [IoT Edge 部署] 索引標籤。
1. 檢查部署清單。 針對每個部署，您可以檢視下列詳細資料：

    | 資料行 | 描述 |
    | --- | --- |
    | ID | 部署的名稱。 |
    | 類型 | 部署的類型，可以是 [部署] 或 [分層部署]。 |
    | 目標條件 | 用來定義目標裝置的標記。 |
    | 優先順序 | 指派給部署的優先順序號碼。 |
    | 系統計量 | 在 IoT 中樞，符合目標條件的裝置對應項數目。 [已套用] 會指定已在 IoT 中樞內將部署內容套用到其模組對應項的裝置數目。 |
    | 裝置計量 | 從 IoT Edge 用戶端執行階段報告成功或錯誤的 IoT Edge 裝置數目。 |
    | 自訂計量 | 針對您為部署所定義的任何計量回報資料的 IoT Edge 裝置數目。 |
    | 建立時間 | 建立部署時的時間戳記。 當兩個部署具有相同的優先順序時，可使用此時間戳記來中斷繫結。 |

1. 選取您想要監視的部署。  
1. 在 [部署詳細資料] 頁面上，向下捲動至最下方區段，然後選取 [目標條件] 索引標籤。選取 [檢視] 可列出符合目標條件的裝置。 您可以變更條件，也可以變更 [優先順序]。 如果您進行變更，請選取 [儲存]。

   ![檢視用於部署的目標裝置](./media/how-to-monitor-iot-edge-deployments/target-devices.png)

1. 選取 [計量] 索引標籤。如果您從 [選取計量] 下拉式清單中選擇一個計量，就會出現一個 [檢視] 按鈕，為您顯示結果。 您也可以選取 [編輯計量] 來調整您所定義之任何自訂計量的準則。 如果您進行變更，請選取 [儲存]。

   ![檢視用於部署的計量](./media/how-to-monitor-iot-edge-deployments/deployment-metrics-tab.png)


若要對您的部署進行變更，請參閱[修改部署](how-to-deploy-at-scale.md#modify-a-deployment)。

## <a name="monitor-a-deployment-with-azure-cli"></a>使用 Azure CLI 監視部署

使用 [az IoT Edge deployment show](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/edge/deployment?view=azure-cli-latest#ext-azure-iot-az-iot-edge-deployment-show) 命令顯示單一部署的詳細資料：

```cli
az iot edge deployment show --deployment-id [deployment id] --hub-name [hub name]
```

deployment show 命令接受下列參數︰

* **--deployment-id** - 存在於 IoT 中樞的部署名稱。 必要參數。
* **--hub-name** - 部署存在於其中的 IoT 中樞名稱。 中樞必須在目前訂用帳戶中。 使用 `az account set -s [subscription name]` 命令切換到所需的訂用帳戶

在命令視窗中檢查部署。 **metrics** 屬性會列出每個中樞所評估每個計量的計數：

* **targetedCount** - 系統計量，指定 IoT 中樞內符合目標條件的裝置對應項數目。
* **appliedCount** - 系統計量，指定已在 IoT 中樞內將部署內容套用到其模組對應項的裝置數目。
* **reportedSuccessfulCount** - 裝置計量，指定從 IoT Edge 用戶端執行階段回報成功之部署中的 IoT Edge 裝置數目。
* **reportedFailedCount** - 裝置計量，指定從 IoT Edge 用戶端執行階段回報失敗之部署中的 IoT Edge 裝置數目。

您可以使用 [az IoT Edge deployment show-metric](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/edge/deployment?view=azure-cli-latest#ext-azure-iot-az-iot-edge-deployment-show-metric) 命令，顯示每個計量的裝置識別碼或物件的清單：

```cli
az iot edge deployment show-metric --deployment-id [deployment id] --metric-id [metric id] --hub-name [hub name]
```

deployment show-metric 命令接受下列參數︰

* **--deployment-id** - 存在於 IoT 中樞的部署名稱。
* **--metric-id** - 您想要在其中查看裝置識別碼清單的計量名稱，例如 `reportedFailedCount`。
* **--hub-name** - 部署存在於其中的 IoT 中樞名稱。 中樞必須在目前訂用帳戶中。 使用 `az account set -s [subscription name]` 命令切換到所需的訂用帳戶。

若要對您的部署進行變更，請參閱[修改部署](how-to-deploy-cli-at-scale.md#modify-a-deployment)。

## <a name="next-steps"></a>後續步驟

瞭解如何[監視模組 twins](how-to-monitor-module-twins.md)，主要是 IoT Edge 代理程式和 IoT Edge 中樞執行時間模組，以取得 IoT Edge 部署的連線和健康狀態。
