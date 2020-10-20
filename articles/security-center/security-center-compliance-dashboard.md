---
title: 教學課程：法規合規性檢查 - Azure 資訊安全中心
description: 教學課程：了解如何使用 Azure 資訊安全中心改善您的法規合規性。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 5f50c4dc-ea42-418d-9ea8-158ffeb93706
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/11/2020
ms.author: memildin
ms.openlocfilehash: 3108dd32b9d7338e418a1d10684115ad7265702e
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/12/2020
ms.locfileid: "91951191"
---
# <a name="tutorial-improve-your-regulatory-compliance"></a>教學課程：改善法規合規性

Azure 資訊安全中心會利用**法規合規性儀表板**，協助您簡化達到法規合規性需求的程序。 在儀表板中，資訊安全中心會根據 Azure 環境的連續評估，提供合規性狀況的深入解析。 資訊安全中心會根據安全性最佳做法，來分析混合式雲端環境中的風險因素。 這些評量會對應到一組所支援標準中的合規性控制項。 在法規合規性儀表板中，您可以看到環境內所有這些評量在特定標準或法規下的狀態。 當您採取建議動作並降低環境中的風險因素後，您的合規性狀況便已改善。

在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 使用法規合規性儀表板評估您的法規合規性
> * 採取建議動作來改善您的合規性狀況

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/) 。

## <a name="prerequisites"></a>Prerequisites

若要逐步執行本教學課程中涵蓋的功能，您必須啟用 [Azure Defender](azure-defender.md)。 您可以免費試用 Azure Defender 30 天。

##  <a name="assess-your-regulatory-compliance"></a>評估法規合規性

資訊安全中心會持續評估資源的組態，以識別安全性問題與弱點。 這些評估會顯示為建議，專門用於改善您的安全性防護。 在法規合規性儀表板中，您可以檢視一組合規性標準及其需求，其中支援的需求會對應到適當的安全性評量。 這可讓您根據這些評估的狀態，檢視與標準有關的合規性狀況。

法規合規性儀表板檢視可協助您將注意力放在合規性與重要標準或法規之間的差異。 此重點檢視也可讓您在一段時間內，持續監視動態雲端和混合式環境中的合規性分數。

>[!NOTE]
> 根據預設，資訊安全中心支援下列法規標準：Azure CIS、PCI DSS 3.2、ISO 27001 和 SOC TSP。 
>
> [動態相容性套件 (預覽)](update-regulatory-compliance-packages.md) 功能可讓您將法規合規性儀表板中顯示的標準升級至新的*動態*套件。 您也可以使用相同的預覽功能來新增合規性套件，並監視其他標準的合規性。 

1. 從資訊安全中心的功能表中，選取 [法規合規性]。 <br>
在畫面頂端，您會看到合規性狀態的概觀和一組支援的合規性規章顯示在儀表板上。 您可以看到整體的合規性分數，以及每個標準評量的成功和失敗數目。

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-dashboard.png" alt-text="法規合規性儀表板":::

1. 選取與您相關的合規性標準索引標籤 (1)。 您會看到套用標準的訂用帳戶 (2)，以及該標準的所有控制項清單 (3)。 對於適用的控制項，您可以檢視與該控制項相關聯的成功和失敗評量詳細資料 (4)，以及受影響的資源數目 (5)。 有些控制項呈現灰色。這些控制項沒有任何相關聯的資訊安全中心評量。 請檢查這些控制項的需求，並自行在環境中對其進行評估。 其中有些項目可能與處理程序相關，而非技術性項目。

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-drilldown.png" alt-text="法規合規性儀表板":::

1. 若要產生並下載內有目前特殊標準合規性狀態摘要的 PDF 報告，請按一下 [下載報告]  。

    此報告會根據資訊安全中心的評估資料來為所選定的標準提供合規性狀態高階摘要，並且會根據該特殊標準的控制來加以組織。 此報告可與利害關係人共用，且可作為證據來提供給內部和外部稽核員。

    :::image type="content" source="./media/security-center-compliance-dashboard/download-report.png" alt-text="法規合規性儀表板":::

## <a name="improve-your-compliance-posture"></a>改善您的合規性狀況

基於法規合規性儀表板中的資訊，您可以藉由在儀表板內直接處理建議來改善合規性狀況。

1.  按一下儀表板上顯示的任何失敗評量，即可檢視該項建議的詳細資料。 每項建議都包含一組應遵循著來解決問題的補救步驟。

1.  您可以選取特定資源以檢視更多詳細資料，並處理該資源的建議。 <br>例如，在 [Azure CIS 1.1.0 (新版) 標準] 中，您可以選取**應在虛擬機器上套用磁碟加密**建議。

    :::image type="content" source="./media/security-center-compliance-dashboard/sample-recommendation.png" alt-text="法規合規性儀表板":::

1. 在此範例中，當您從建議詳細資料頁面中選取 [採取動作] 時，將會進入 Azure 入口網站的 [Azure 虛擬機器] 頁面，您可以在此處開啟 [安全性] 索引標籤，並啟用加密：

    :::image type="content" source="./media/security-center-compliance-dashboard/encrypting-vm-disks.png" alt-text="法規合規性儀表板":::

    如需有關如何套用建議的詳細資訊，請參閱[實作 Azure 資訊安全中心的安全性建議](security-center-recommendations.md)。

1.  在您採取行動來處理建議之後，您會看到合規性儀表板報告中產生了影響，因為合規性分數已改善。

    > [!NOTE]
    > 系統大約會每隔 12 小時執行一次評量，因此您必須在下次執行相關評量後，才會看到合規性資料受到的影響。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何使用資訊安全中心的法規合規性儀表板來執行下列動作：

-   檢視及監視與重要標準和法規相關的合規性狀況。
-   藉由處理相關建議來改善合規性狀態，並觀看合規性分數的改善。

法規合規性儀表板可以大幅簡化合規性程序，並大幅減少為 Azure 和混合式環境收集合規性辨識項所需的時間。

若要深入了解，請參閱下列相關文章：

-   [在法規合規性儀表板 (預覽) 中更新為動態合規性套件](update-regulatory-compliance-packages.md) - 了解這項預覽版功能如何讓您將法規合規性儀表板中顯示的標準更新為新的*動態*套件。 您也可以使用相同的預覽功能來新增合規性套件，並監視其他標準的合規性。 
-   [Azure 資訊安全中心的安全性健全狀況監視](security-center-monitoring.md) - 了解如何監視 Azure 資源的健全狀況。
-   [管理 Azure 資訊安全中心的安全性建議](security-center-recommendations.md)了解如何使用 Azure 資訊安全中心的建議來協助您保護 Azure 資源。
-   [改善 Azure 資訊安全中心的安全分數](secure-score-security-controls.md) - 了解要如何設定弱點與安全性建議的優先順序，才最能改善安全性狀況。
