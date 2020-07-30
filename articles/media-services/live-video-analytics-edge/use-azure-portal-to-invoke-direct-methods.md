---
title: 如何使用 Azure 入口網站叫用直接方法
description: 本文概述如何使用 Azure 入口網站叫用直接方法。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.subservice: ''
ms.workload: ''
ms.topic: how-to
ms.custom: ''
ms.date: 07/24/2020
ms.author: inhenkel
ms.openlocfilehash: 763dd82c8263a5e180468f9fbd7f86526295a80d
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87279282"
---
# <a name="how-to-use-azure-portal-to-invoke-direct-methods"></a>如何使用 Azure 入口網站叫用直接方法

「IoT 中樞」能讓您從雲端在邊緣裝置上叫用[直接方法](/azure/iot-hub/iot-hub-devguide-direct-methods#method-invocation-for-iot-edge-modules)。 IoT Edge 上的即時影片分析 (LVA) 模組會公開數個[直接方法](/azure/media-services/live-video-analytics-edge/direct-methods)，可以用來定義、部署及具現化不同的工作流程，以便分析即時影片。

在本文中，您將了解如何透過 Azure 入口網站在 IoT Edge 模組的即時影片分析上叫用直接方法呼叫。

## <a name="prerequisites"></a>必要條件

* 您可以使用以下位置所述的方法，在您的邊緣裝置上執行 IoT Edge 模組上的即時影片分析：[快速入門：IoT Edge 上的即時影片分析](/azure/media-services/live-video-analytics-edge/get-started-detect-motion-emit-events-quickstart)或使用[入口網站](/azure/media-services/live-video-analytics-edge/deploy-iot-edge-device)。

* 您了解[即時影片分析](/azure/media-services/live-video-analytics-edge/overview)和[媒體圖表概念](/azure/media-services/live-video-analytics-edge/media-graph-concept)。

## <a name="invoking-direct-methods-via-azure-portal"></a>透過 Azure 入口網站叫用直接方法

LVA 模組所公開的每個[直接方法](/azure/media-services/live-video-analytics-edge/direct-methods)都可以透過 Azure 入口網站叫用。 下列步驟提供一個直接方法的詳細資料。 您可以使用類似的步驟來叫用其他直接方法。 不過，每個直接方法都需要特定的 JSON 主體。

使用 `GraphTopologyList` 方法呼叫來取出目前部署於 IoT Edge 模組上即時影片分析的所有圖表拓撲清單。 使用下列步驟來叫用此直接方法：

1. 登入 Azure 入口網站
1. 從您的入口網站首頁尋找相關的資源群組，以找出您的 IoT 中樞，或者如果您知道 IoT 中樞，請加以選取。
    ![入口網站首頁中的資源群組](media/use-azure-portal-to-invoke-directs-methods/portal-rg-home.png)
1. 在 [IoT 中樞] 頁面上，選取 [自動裝置管理] 下的 [IoT Edge]，以列出各種裝置識別碼。 選取相關的裝置識別碼，以列出裝置上執行的模組。
    ![IoT 中樞頁面](media/use-azure-portal-to-invoke-directs-methods/iot-hub-page.png)
1. 選取 IoT Edge 模組上的即時影片分析，以顯示其設定頁面。<br><br>
    ![選取 IoT Edge 模組上的即時影片分析，以顯示其設定頁面](media/use-azure-portal-to-invoke-directs-methods/modules.png)
1. 選取 [直接方法] 功能表選項。 <br><br>
    ![按一下 [直接方法] 功能表選項](media/use-azure-portal-to-invoke-directs-methods/module-details.png)
    > [!NOTE]
    > 您可能需要在 [連接字串] 區段中新增一個值，如您在目前的頁面上所見。 您不需要隱藏或變更 [設定名稱] 區段中的任何項目。 可以讓其成為公用。

1. 在 [方法名稱] 欄位中，輸入「GraphTopologyList」。
1. 在 [承載] 欄位中複製並貼上下列 JSON。
    ```json
    {
    "@apiVersion":
    }
    ```
1. 選取頁面頂端的 [叫用方法] 按鈕。<br><br>
    ![叫用方法按鈕](media/use-azure-portal-to-invoke-directs-methods/direct-method.png)
1. 您應該會在 [結果] 區域中看到狀態 200 訊息。<br><br>
    ![連線逾時](media/use-azure-portal-to-invoke-directs-methods/connection-timeout.png)

## <a name="responses"></a>回應

| 條件             | 狀態碼 | 詳細的錯誤碼 |
|-----------------------|-------------|---------------------|
| Success               | 200         | N/A                 |
| 一般使用者錯誤   | 400 範圍   |                     |
| 一般伺服器錯誤 | 500 範圍   |                     |

## <a name="next-steps"></a>後續步驟

您可以在[直接方法](/azure/media-services/live-video-analytics-edge/direct-methods)頁面上找到更多的直接方法。

> [!NOTE]
> 圖表執行個體會具現化特定拓撲，因此請先確定您已設定正確的拓撲，再建立圖表執行個體。

[快速入門：偵測動作發出事件](/azure/media-services/live-video-analytics-edge/get-started-detect-motion-emit-events-quickstart)是了解直接方法呼叫確切順序的良好參考。
