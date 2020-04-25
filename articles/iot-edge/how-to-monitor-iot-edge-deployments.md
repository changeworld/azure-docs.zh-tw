---
title: 監視 IoT Edge 部署-Azure IoT Edge
description: 高階監視，包括 edgeHub 和 edgeAgent 報告的屬性和自動部署計量。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/21/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: cc7c1fd1dff85908c96e2fd7b2276df3d833e37f
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/24/2020
ms.locfileid: "82134308"
---
# <a name="monitor-iot-edge-deployments"></a>監視 IoT Edge 部署

Azure IoT Edge 提供報告功能，可讓您監視部署至 IoT Edge 裝置之模組的即時資訊。 IoT 中樞服務會從裝置抓取狀態，並將其提供給操作員使用。 對於[大規模的部署](module-deployment-monitoring.md)（包含自動部署和分層部署）而言，監視也非常重要。

裝置和模組都有類似的資料，例如連線能力，因此會根據裝置識別碼或模組識別碼取得值。

IoT 中樞服務會收集裝置和模組 twins 所報告的資料，並提供裝置可能擁有的各種狀態計數。 IoT 中樞服務會將這項資料組織成四個計量群組：

| 類型 | 說明 |
| --- | ---|
| 目標式 | 顯示符合部署目標條件的 IoT Edge 裝置。 |
| 已套用 | 顯示以較高優先順序的其他部署為目標的目標 IoT Edge 裝置。 |
| 報告成功 | 顯示已回報已成功部署模組的 IoT Edge 裝置。 |
| 報告失敗 | 顯示已回報一或多個模組尚未成功部署的 IoT Edge 裝置。 若要進一步調查此錯誤，請遠端連線到那些裝置並檢視記錄檔。 |

IoT 中樞服務可讓您在 Azure 入口網站和 Azure CLI 中監視此資料。

## <a name="monitor-a-deployment-in-the-azure-portal"></a>監視 Azure 入口網站中的部署

若要檢視部署的詳細資料並監視正在執行該部署的裝置，請使用下列步驟：

1. 登入[Azure 入口網站](https://portal.azure.com)並流覽至您的 IoT 中樞。
1. 從左窗格功能表中選取 [ **IoT Edge** ]。
1. 選取 [ **IoT Edge 部署**] 索引標籤。
1. 檢查部署清單。針對每個部署，您可以檢視下列詳細資料：

    | 資料行 | 描述 |
    | --- | --- |
    | 識別碼 | 部署的名稱。 |
    | 類型 | 部署的類型，可能是**部署**或**分層部署**。 |
    | 目標條件 | 用來定義目標裝置的標記。 |
    | 優先順序 | 指派給部署的優先順序編號。 |
    | 系統計量 | IoT 中樞中符合目標條件的裝置 twins 數目。 [已套用]**會指定在**IoT 中樞中，已將部署內容套用至其模組 twins 的裝置數目。 |
    | 裝置計量 | 從 IoT Edge 用戶端執行時間報告成功或錯誤的 IoT Edge 裝置數目。 |
    | 自訂計量 | 針對您為部署所定義的任何計量，報告資料的 IoT Edge 裝置數目。 |
    | 建立時間 | 建立部署時的時間戳記。 當兩個部署具有相同的優先順序時，可使用此時間戳記來中斷繫結。 |

1. 選取您想要監視的部署。  
1. 在 [**部署詳細資料**] 頁面上，向下流覽至底部區段，然後選取 [**目標條件**] 索引標籤。選取 [**查看**]，列出符合目標條件的裝置。 您也可以變更條件和**優先順序**。 如果您進行變更，請選取 [**儲存**]。

   ![查看部署的目標裝置](./media/how-to-monitor-iot-edge-deployments/target-devices.png)

1. 選取 [**計量**] 索引標籤。如果您從 [**選取**計量] 下拉式選單中選擇 [度量]，則會顯示 [ **View** ] 按鈕以顯示結果。 您也可以選取 [**編輯計量**]，針對您已定義的任何自訂計量調整準則。 如果您進行變更，請選取 [**儲存**]。

   ![查看部署的計量](./media/how-to-monitor-iot-edge-deployments/deployment-metrics-tab.png)


若要對您的部署進行變更，請參閱[修改部署](how-to-deploy-at-scale.md#modify-a-deployment)。

## <a name="monitor-a-deployment-with-azure-cli"></a>使用 Azure CLI 監視部署

使用[az IoT Edge deployment show](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-show)命令來顯示單一部署的詳細資料：

```cli
az iot edge deployment show --deployment-id [deployment id] --hub-name [hub name]
```

Deployment show 命令會採用下列參數：

* **--deployment-id** - 存在於 IoT 中樞的部署名稱。 必要參數。
* **--中樞-名稱**-部署所在的 IoT 中樞名稱。 中樞必須在目前訂用帳戶中。 使用 `az account set -s [subscription name]` 命令切換到所需的訂用帳戶

在命令視窗中檢查部署。**metrics** 屬性會列出每個中樞所評估每個計量的計數：

* **targetedCount** - 系統計量，指定 IoT 中樞內符合目標條件的裝置對應項數目。
* **appliedCount** - 系統計量，指定已在 IoT 中樞內將部署內容套用到其模組對應項的裝置數目。
* **reportedSuccessfulCount** -裝置計量，指定從 IoT Edge 用戶端執行時間報告成功的部署中 IoT Edge 裝置數目。
* **reportedFailedCount** -裝置計量，指定從 IoT Edge 用戶端執行時間報告失敗的部署中 IoT Edge 裝置數目。

您可以使用[az IoT Edge deployment show-公制](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-show-metric)命令，顯示每個計量的裝置識別碼或物件清單：

```cli
az iot edge deployment show-metric --deployment-id [deployment id] --metric-id [metric id] --hub-name [hub name]
```

Deployment show-公制命令會採用下列參數：

* **--deployment-id** - 存在於 IoT 中樞的部署名稱。
* **--計量-識別碼**-您想要查看裝置識別碼清單的度量名稱，例如`reportedFailedCount`。
* **--中樞-名稱**-部署所在的 IoT 中樞名稱。 中樞必須在目前訂用帳戶中。 使用 `az account set -s [subscription name]` 命令切換到所需的訂用帳戶。

若要對您的部署進行變更，請參閱[修改部署](how-to-deploy-cli-at-scale.md#modify-a-deployment)。

## <a name="next-steps"></a>後續步驟

瞭解如何[使用內建的直接方法與 EdgeAgent 通訊](how-to-edgeagent-direct-method.md)。
