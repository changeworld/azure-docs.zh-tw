---
title: 使用 Azure Sentinel 調查事件 |Microsoft Docs
description: 在本教學課程中，您將瞭解如何使用 Azure Sentinel 來建立警示規則，以產生您可以指派和調查的事件。
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
ms.openlocfilehash: aa9160f01ed0040123bd8ac932cfd2443f557bb6
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96511724"
---
# <a name="tutorial-investigate-incidents-with-azure-sentinel"></a>教學課程：使用 Azure Sentinel 調查事件

> [!IMPORTANT]
> 調查圖表目前為 **預覽** 狀態。 請參閱 [Microsoft Azure 預覽的補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) ，以取得適用于 Azure 功能（Beta、預覽或尚未發行正式運作）的其他法律條款。


本教學課程可協助您使用 Azure Sentinel 調查事件。 當您將資料來源連接到 Azure Sentinel 之後，您想要在發生可疑問題時收到通知。 為了讓您能夠這樣做，Azure Sentinel 可讓您建立高階警示規則，以產生您可以指派和調查的事件。

本文將說明：
> [!div class="checklist"]
> * 調查事件
> * 使用調查圖表
> * 回應威脅

事件可以包含多個警示。 它是特定調查的所有相關辨識項的匯總。 系統會根據您在 [ **分析** ] 頁面中建立的分析規則來建立事件。 與警示相關的屬性（例如嚴重性和狀態）都是在事件層級設定。 當您讓 Azure Sentinel 知道您要尋找的威脅種類，以及如何找到它們之後，您可以藉由調查事件來監視偵測到的威脅。

## <a name="prerequisites"></a>Prerequisites
- 如果您在設定分析規則時使用實體對應欄位，您將只能調查事件。 調查圖形要求您的原始事件必須包含實體。

- 如果您有需要指派事件的來賓使用者，則必須將 Azure AD 租使用者中的 [目錄讀取](../active-directory/roles/permissions-reference.md#directory-readers) 者角色指派給使用者。 一般 (非來賓) 使用者預設會指派此角色。

## <a name="how-to-investigate-incidents"></a>如何調查事件

1. 選取 **事件**。 [ **事件** ] 頁面可讓您知道有多少事件、開啟的數目、已設定 **的進度**，以及關閉的數目。 針對每個事件，您可以看到發生的時間，以及事件的狀態。 查看嚴重性，以決定要先處理哪些事件。

    ![查看事件嚴重性](media/tutorial-investigate-cases/incident-severity.png)

1. 您可以視需要篩選事件，例如依狀態或嚴重性。

1. 若要開始調查，請選取特定的事件。 您可以在右側看到事件的詳細資訊，包括其嚴重性、相關實體數目摘要、觸發此事件的原始事件，以及事件的唯一識別碼。

1. 若要查看事件中警示和實體的更多詳細資料，請在 [事件] 頁面中選取 [ **查看完整詳細資料** ]，並查看摘要事件資訊的相關索引標籤。 在 [ **警示** ] 索引標籤中，檢查警示本身。 您可以看到警示的所有相關資訊，也就是觸發警示的查詢、每個查詢傳回的結果數目，以及在警示上執行操作手冊的能力。 若要進一步向下切入到事件，請選取 **事件** 的數目。 這會開啟產生結果的查詢，以及在 Log Analytics 中觸發警示的事件。 在 [ **實體** ] 索引標籤中，您可以看到您對應為警示規則定義一部分的所有實體。

    ![檢視警示詳細資料](media/tutorial-investigate-cases/alert-details.png)

1. 如果您正在積極調查事件，最好先將事件的狀態設定為 [ **進行中** ]，直到您關閉為止。

1. 事件可以指派給特定使用者。 針對每個事件，您可以藉由設定 [ **事件擁有** 者] 欄位來指派擁有者。 所有事件都會以未指派的形式啟動。 您也可以新增批註，讓其他分析師能夠瞭解您所調查的內容，以及事件的顧慮。

    ![將事件指派給使用者](media/tutorial-investigate-cases/assign-incident-to-user.png)

1. 選取 [ **調查** ] 以查看調查對應。

## <a name="use-the-investigation-graph-to-deep-dive"></a>使用調查圖表深入探討

調查圖表可讓分析師針對每項調查提出正確的問題。 調查圖表可將相關資料與任何相關實體相關聯，協助您瞭解潛在安全性威脅的範圍，並找出根本原因。 您可以選取並選擇不同的展開選項，以深入瞭解並調查圖形中顯示的任何實體。  
  
調查圖表提供您：

- **原始資料的視覺內容**：即時、視覺圖形會顯示從原始資料自動解壓縮的實體關聯性。 這可讓您輕鬆地查看不同資料來源之間的連接。

- **完整調查範圍探索**：使用內建的探索查詢擴展您的調查範圍，以呈現缺口的完整範圍。

- **內建的調查步驟**：使用預先定義的探索選項，以確保您會在面對威脅時詢問正確的問題。

若要使用調查圖表：

1. 選取事件，然後選取 [ **調查**]。 這會帶您前往調查圖表。 圖形會提供直接連線至警示的實體的說明，並進一步連接每個資源。

   > [!IMPORTANT] 
   > - 如果您在設定分析規則時使用實體對應欄位，您將只能調查事件。 調查圖形要求您的原始事件必須包含實體。
   >
   > - Azure Sentinel 目前支援 **最多30天的事件** 調查。

   ![檢視地圖](media/tutorial-investigate-cases/map1.png)

1. 選取實體以開啟 [ **實體** ] 窗格，讓您可以查看該實體的資訊。

    ![在地圖中查看實體](media/tutorial-investigate-cases/map-entities.png)
  
1. 將滑鼠停留在每個實體上，以顯示我們的安全性專家和分析師針對每個實體類型所設計的問題清單，以加深您的調查。 我們會呼叫這些選項 **探索查詢**。

    ![探索更多詳細資料](media/tutorial-investigate-cases/exploration-cases.png)

   例如，您可以在電腦上要求相關的警示。 如果您選取探索查詢，則會將產生的有權新增回圖形。 在此範例中，選取 **相關的警示** 會在圖形中傳回下列警示：

    ![查看相關警示](media/tutorial-investigate-cases/related-alerts.png)

1. 針對每個探索查詢，您可以選取 [**事件 \>**]，以選取開啟原始事件結果和 Log Analytics 中所用查詢的選項。

1. 為了瞭解事件，圖形提供平行的時間軸。

    ![在地圖中查看時間軸](media/tutorial-investigate-cases/map-timeline.png)

1. 將滑鼠停留在時間軸上，以查看圖形上的哪些專案發生在哪個時間點。

    ![使用地圖中的時間軸調查警示](media/tutorial-investigate-cases/use-timeline.png)

## <a name="closing-an-incident"></a>關閉事件

一旦您解決特定事件 (例如，當您的調查已達到其結論) 時，您應該將事件的狀態設定為 [ **已關閉**]。 當您這樣做時，系統會要求您指定關閉事件的原因，以將事件分類。 此為必要步驟。 按一下 [ **選取分類** ]，然後從下拉式清單中選擇下列其中一項：

- 真肯定的可疑活動
- 良性正面-可疑但預期
- 錯誤的正面-不正確的警示邏輯
- 錯誤正面-不正確的資料
- 待定

:::image type="content" source="media/tutorial-investigate-cases/closing-reasons-dropdown.png" alt-text="醒目顯示 [選取分類] 清單中可用分類的螢幕擷取畫面。":::

選擇適當的分類之後，請在 [ **批註** ] 欄位中加入一些描述性文字。 當您需要回頭參考此事件時，這會很有用。 當 **您** 完成時，請按一下 [套用]，事件將會關閉。

:::image type="content" source="media/tutorial-investigate-cases/closing-reasons-comment-apply.png" alt-text="{替代文字}":::

## <a name="next-steps"></a>後續步驟
在本教學課程中，您已瞭解如何使用 Azure Sentinel 開始調查事件。 繼續進行教學課程，以瞭解 [如何使用自動化的操作手冊來回應威脅](tutorial-respond-threats-playbook.md)。
> [!div class="nextstepaction"]
> [回應威脅](tutorial-respond-threats-playbook.md) 以將威脅的回應自動化。

