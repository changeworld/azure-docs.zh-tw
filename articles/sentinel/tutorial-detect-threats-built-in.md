---
title: 使用 Azure 哨兵* 調查警報*微軟文檔
description: 使用本教程瞭解如何使用 Azure Sentinel 調查警報。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: df855aa768fdeb279482b8407259be1a644322d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77585198"
---
# <a name="tutorial-detect-threats-out-of-the-box"></a>教程：檢測開箱即用的威脅


> [!IMPORTANT]
> 開箱即用的威脅檢測當前處於公共預覽版中。
> 此功能在沒有服務等級協定的情況下提供，不建議用於生產工作負載。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

 [將資料來源](quickstart-onboard.md) 連接到 Azure Sentinel 後，希望在發生可疑情況時收到通知。 為了使您能夠執行此操作，Azure Sentinel 為您提供了現成的內置範本。 這些範本由 Microsoft 的安全專家和分析人員團隊根據已知威脅、常見攻擊媒介和可疑活動升級鏈設計。 啟用這些範本後，它們將自動搜索整個環境中任何看起來可疑的活動。 許多範本可以根據您的需求進行自訂，以搜索或篩選出活動。 這些範本生成的警報將創建可在環境中分配和調查的事件。

本教程可説明您使用 Azure Sentinel 檢測威脅：

> [!div class="checklist"]
> * 使用開箱即用的檢測
> * 將威脅回應自動化

## <a name="about-out-of-the-box-detections"></a>關於現成的偵測

若要檢視所有現成的偵測，請依序移至 [分析]**** 和 [規則範本]****。 此索引標籤包含所有 Azure Sentinel 內建規則。

   ![使用內建偵測來尋找 Azure Sentinel 的威脅](media/tutorial-detect-built-in/view-oob-detections.png)

以下範本類型可用：

- **Microsoft 安全**- Microsoft 安全範本自動從其他 Microsoft 安全解決方案中生成的警報即時創建 Azure Sentinel 事件。 您可以使用 Microsoft 安全規則作為範本來創建具有類似邏輯的新規則。 有關安全規則的詳細資訊，請參閱從[Microsoft 安全警報自動創建事件](create-incidents-from-alerts.md)。
- **Fusion** - 基於 Fusion 技術，Azure Sentinel 中的高級多階段攻擊檢測使用可擴展的機器學習演算法，可以將多個產品中的許多低傳真警報和事件關聯到高保真和可操作的事件中。 預設情況下啟用融合。 由於邏輯是隱藏的，因此不能將它用作範本來創建多個規則。
- **機器學習行為分析**- 這些範本基於專有的 Microsoft 機器學習演算法，因此您無法查看其工作方式和執行時間的內部邏輯。 由於邏輯是隱藏的，因此不能將它用作範本來創建多個規則。
-   **計畫**— 計畫分析規則是由 Microsoft 安全專家編寫的計畫查詢。 您可以看到查詢邏輯並對其進行更改。 可以使用計畫規則作為範本創建具有類似邏輯的新規則。

## <a name="use-out-of-the-box-detections"></a>使用開箱即用的檢測

1. 為了使用內置範本，請按一下 **"創建規則"** 以基於該範本創建新的活動規則。 每個條目都有一個自動檢查所需的資料來源的清單，這可能導致**禁用 Create 規則**。
  
   ![使用內建偵測來尋找 Azure Sentinel 的威脅](media/tutorial-detect-built-in/use-built-in-template.png)
 
1. 這將基於所選範本打開規則創建嚮導。 所有詳細資訊都是自動填滿的，對於**計畫規則**或**Microsoft 安全規則**，您可以自訂邏輯以更好地適合您的組織，或者基於內置範本創建其他規則。 按照規則創建嚮導中的步驟並完成基於範本創建規則的步驟後，新規則將顯示在 **"活動規則"** 選項卡中。

有關嚮導中欄位的詳細資訊，請參閱[教程：創建自訂分析規則以檢測可疑威脅](tutorial-detect-threats-custom.md)。



## <a name="next-steps"></a>後續步驟
在本教程中，您學習了如何使用 Azure Sentinel 開始檢測威脅。 

要瞭解如何自動回應威脅，請[在 Azure Sentinel 中設置自動威脅回應](tutorial-respond-threats-playbook.md)。

