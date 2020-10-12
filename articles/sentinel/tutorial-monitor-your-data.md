---
title: 使用 Azure Sentinel 中的 Azure 監視器活頁簿將資料視覺化 | Microsoft Docs
description: 使用此教學課程來了解如何使用 Azure Sentinel 中的活頁簿，將您的資料視覺化。
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
ms.date: 05/04/2020
ms.author: yelevin
ms.openlocfilehash: 63a9a6dc9f052c01a7440f616f0baeaab083ad73
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91843068"
---
# <a name="tutorial-visualize-and-monitor-your-data"></a>教學課程：將資料視覺化並加以監視



一旦[將資料來源連線](quickstart-onboard.md) 至 Azure Sentinel，您可以使用 Azure Sentinel 採用的 Azure 監視器活頁簿來監視資料，此整合可提供建立自訂儀表板的豐富功能。 雖然活頁簿在 Azure Sentinel 中的顯示方式不同，但很適合用於查看如何[使用 Azure 監視器活頁簿建立互動式報告](../azure-monitor/platform/workbooks-overview.md)。 Azure Sentinel 可讓您建立資料的自訂活頁簿，同時還隨附內建活頁簿範本，可讓您在連線資料來源後快速取得資料的見解。


此教學課程可協助您在 Azure Sentinel 中將資料視覺化。
> [!div class="checklist"]
> * 使用內建活頁簿
> * 建立新活頁簿

## <a name="prerequisites"></a>Prerequisites

- 在 Azure Sentinel 工作區的資源群組上，您至少必須擁有活頁簿讀者或活頁簿參與者權限。

> [!NOTE]
> 您可以在 Azure Sentinel 中看到的活頁簿會儲存在 Azure Sentinel 工作區的資源群組內，並由其建立所在的工作區加以標記。

## <a name="use-built-in-workbooks"></a>使用內建活頁簿

1. 移至 [活頁簿]，然後選取 [範本]，以查看 Azure Sentinel 內建活頁簿的完整清單。 若要查看與您所連線資料類型相關的活頁簿，每個活頁簿中的 [需要的資料類型] 欄位將會列出綠色核取記號旁的資料類型 (如果您已經將相關資料串流至 Azure Sentinel)。
  ![移至活頁簿](./media/tutorial-monitor-data/access-workbooks.png)
1. 按一下 [ **視圖範本** ] 以查看填入資料的範本。
  
1. 若要編輯活頁簿，請選取 [ **儲存**]，然後選取您要儲存範本之 JSON 檔案的位置。 

   > [!NOTE]
   > 這會根據相關的範本建立 Azure 資源，並儲存活頁簿的 JSON 檔案，而不是資料。


1. 選取 [ **View 已儲存**的活頁簿]。 然後，按一下頂端的 [編輯] 按鈕。 您現在可以編輯活頁簿，並根據您的需求進行加以自訂。 如需如何自訂活頁簿的詳細資訊，請參閱如何[使用 Azure 監視器活頁簿建立互動式報表](../azure-monitor/platform/workbooks-overview.md)。
![檢視活頁簿](./media/tutorial-monitor-data/workbook-graph.png)
1. 進行變更之後，您可以儲存活頁簿。 

1. 您也可以複製活頁簿：選取 [編輯]，然後選取 [另存新檔]，請務必在相同的訂用帳戶和資源群組下，以另一個名稱儲存該活頁簿。 這些複製的活頁簿會顯示在 [我的活頁 **簿** ] 索引標籤底下。


## <a name="create-new-workbook"></a>建立新的活頁簿

1. 移至 [活頁簿]，然後選取 [新增活頁簿]，以從頭開始建立新的活頁簿。
  ![顯示 [新增活頁簿] 畫面的螢幕擷取畫面。](./media/tutorial-monitor-data/create-workbook.png)

1. 若要編輯活頁簿，請選取 [編輯]，然後視需要新增文字、查詢和參數。 如需如何自訂活頁簿的詳細資訊，請參閱如何[使用 Azure 監視器活頁簿建立互動式報表](../azure-monitor/platform/workbooks-overview.md)。 

1. 建立查詢時，請確定 **資料來源** 已設定為 [ **記錄** ]，並將 [ **資源類型** ] 設定為 [ **Log Analytics**]，然後選擇 (s) 的相關工作區。 

1. 建立活頁簿之後，請儲存活頁簿，並確定您將它儲存在 Azure Sentinel 工作區的訂用帳戶和資源群組底下。

1. 如果您想要讓組織中的其他人使用活頁簿，請在 [儲存至] 底下，選取 [共用報表]。 如果您想要讓此活頁簿僅供您使用，請選取 [我的報表]。

1. 若要在工作區的活頁簿之間切換，您可以選取 **開啟**活頁 ![ 簿的開啟圖示。 ](./media/tutorial-monitor-data/switch.png)在任何活頁簿的上方窗格中。 在右側開啟的視窗中，於活頁簿之間切換。

   ![切換活頁簿](./media/tutorial-monitor-data/switch-workbooks.png)


## <a name="how-to-delete-workbooks"></a>如何刪除活頁簿

若要刪除已儲存的活頁簿 (儲存的範本或自訂的活頁簿) ，請在 [活頁簿] 頁面中，選取您要刪除的已儲存活頁簿，然後選取 [ **刪除**]。 這會移除已儲存的活頁簿。

> [!NOTE]
> 這會移除活頁簿資源以及您對範本所做的任何變更。 原始範本將會保持可用。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已瞭解如何使用 Azure 活頁簿將 Azure Sentinel 中的資料視覺化。

若要了解如何將威脅的回應自動化，請參閱[在 Azure Sentinel 中設定自動化威脅回應](tutorial-respond-threats-playbook.md)。
