---
title: 使用 Azure Sentinel 調查警示 |Microsoft Docs
description: 瞭解如何使用現成可用的內建 Azure 威脅偵測範本，在發生可疑問題時通知您。
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
ms.date: 07/06/2020
ms.author: yelevin
ms.openlocfilehash: 5d73337c25c812363b7a542bf42372ca3baa10e8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88605444"
---
# <a name="tutorial-detect-threats-out-of-the-box"></a>教學課程：偵測現成的威脅 (機器翻譯)


> [!IMPORTANT]
> 現成的威脅偵測目前處於公開預覽狀態。
> 這項功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

當您將[資料來源連線](quickstart-onboard.md)   到 Azure Sentinel 之後，您會想要在發生可疑問題時收到通知。 這就是為什麼 Azure Sentinel 提供現成可用的內建範本，以協助您建立威脅偵測規則。 這些範本是由 Microsoft 的安全性專家和分析師團隊所設計，並根據已知的威脅、常見的攻擊媒介和可疑的活動呈報鏈。 從這些範本建立的規則將會自動在您的環境中搜尋任何看似可疑的活動。 您可以根據您的需求，自訂許多範本以搜尋活動或篩選出活動。 這些規則所產生的警示將會建立可在您的環境中指派和調查的事件。

本教學課程可協助您偵測 Azure Sentinel 的威脅：

> [!div class="checklist"]
> * 使用現成可用的威脅偵測
> * 將威脅回應自動化

## <a name="about-out-of-the-box-detections"></a>關於現成的偵測

若要檢視所有現成的偵測，請依序移至 [分析] 和 [規則範本]。 此索引標籤包含所有 Azure Sentinel 內建規則。

   :::image type="content" source="media/tutorial-detect-built-in/view-oob-detections.png" alt-text="使用內建偵測來尋找 Azure Sentinel 的威脅":::

以下是可用的範本類型：

- **Microsoft 安全性**
   
   Microsoft 安全性範本會自動從其他 Microsoft 安全性解決方案所產生的警示中，即時建立 Azure Sentinel 事件。 您可以使用 Microsoft 安全性規則作為範本，建立具有類似邏輯的新規則。 如需安全性規則的詳細資訊，請參閱 [從 Microsoft 安全性警示自動建立事件](create-incidents-from-alerts.md)。

- **融合** 

    以融合技術為基礎，Azure Sentinel 中的先進多階段攻擊偵測使用可調整的機器學習演算法，可讓多個產品的許多低精確度警示和事件與高精確度和可採取動作的事件相互關聯。 依預設會啟用融合。 由於邏輯是隱藏的，因此無法自訂，因此您只能使用此範本建立一個規則。

- **機器學習行為分析**

    這些範本是以專屬的 Microsoft 機器學習演算法為基礎，因此您看不到其運作方式和執行的內部邏輯。 由於邏輯是隱藏的，因此無法自訂，因此您只能使用此類型的每個範本建立一個規則。

- **排程**

    排程的分析規則是以 Microsoft 安全性專家所撰寫的內建查詢為基礎。 您可以查看查詢邏輯，並對其進行變更。 您可以使用 [已排程規則] 範本，並自訂查詢邏輯和排程設定來建立新的規則。

## <a name="use-out-of-the-box-detections"></a>使用現成可用的偵測

1. 若要使用內建範本，請按一下範本名稱，然後按一下 [詳細資料] 窗格上的 [ **建立規則** ] 按鈕，根據該範本建立新的作用中規則。 每個範本都有必要的資料來源清單。 當您開啟範本時，系統會自動檢查資料來源的可用性。 如果有可用性問題，可能會停用 [ **建立規則** ] 按鈕，否則您可能會看到該效果的警告。
  
    :::image type="content" source="media/tutorial-detect-built-in/use-built-in-template.png" alt-text="使用內建偵測來尋找 Azure Sentinel 的威脅":::
 
1. 按一下 [ **建立規則** ] 按鈕，就會根據選取的範本開啟規則建立嚮導。 所有詳細資料都是自動填入，而使用已 **排程** 或 **Microsoft 的安全性** 範本，您可以自訂邏輯和其他規則設定，以更符合您的特定需求。 您可以重複此程式，根據內建範本建立其他規則。 遵循規則建立嚮導中的步驟結束之後，您將完成根據範本建立規則。 新的規則會顯示在 [作用中 **規則** ] 索引標籤中。

    如需如何在規則建立嚮導中自訂規則的詳細資訊，請參閱 [教學課程：建立自訂分析規則來偵測威脅](tutorial-detect-threats-custom.md)。

## <a name="next-steps"></a>接下來的步驟

在本教學課程中，您已瞭解如何使用 Azure Sentinel 開始偵測威脅。 

若要瞭解如何將您對威脅的回應自動化，請 [在 Azure Sentinel 中設定自動威脅回應](tutorial-respond-threats-playbook.md)。

