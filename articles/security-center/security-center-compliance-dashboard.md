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
ms.date: 11/12/2019
ms.author: memildin
ms.openlocfilehash: 285245e499cfe8a5d7e365044c188191325e4267
ms.sourcegitcommit: 52d2f06ecec82977a1463d54a9000a68ff26b572
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/15/2020
ms.locfileid: "84782852"
---
# <a name="tutorial-improve-your-regulatory-compliance"></a>教學課程：改善法規合規性
---

Azure 資訊安全中心會利用**法規合規性儀表板**，協助您簡化達到法規合規性需求的程序。 在儀表板中，資訊安全中心會根據 Azure 環境的連續評估，提供合規性狀況的深入解析。 資訊安全中心會根據安全性最佳做法，來分析混合式雲端環境中的風險因素。 這些評量會對應到一組所支援標準中的合規性控制項。 在法規合規性儀表板中，您可以看到環境內所有這些評估在特定標準或法規下的狀態。 當您採取建議動作並降低環境中的風險因素後，您的合規性狀況便已改善。

在本教學課程中，您將學會如何：

-   使用法規合規性儀表板評估您的法規合規性

-   採取建議動作來改善您的合規性狀況

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/) 。

## <a name="prerequisites"></a>Prerequisites

若要逐步執行本教學課程中涵蓋的功能，您必須有資訊安全中心的標準定價層。 您可以免費試用資訊安全中心標準層。
若要深入了解，請參閱[價格頁面](https://azure.microsoft.com/pricing/details/security-center/)。 [將 Azure 訂用帳戶上架到資訊安全中心標準定價層](https://docs.microsoft.com/azure/security-center/security-center-get-started)快速入門會為您逐步解說如何升級至「標準」定價層。

##  <a name="assess-your-regulatory-compliance"></a>評估法規合規性

資訊安全中心會持續評估資源的組態，以識別安全性問題與弱點。 這些評估會顯示為建議，專門用於改善您的安全性防護。 在法規合規性儀表板中，您可以檢視一組合規性標準及其需求，其中支援的需求會對應到適當的安全性評量。 這可讓您根據這些評估的狀態，檢視與標準有關的合規性狀況。

法規合規性儀表板檢視可協助您將注意力放在合規性與重要標準或法規之間的差異。 此重點檢視也可讓您在一段時間內，持續監視動態雲端和混合式環境中的合規性分數。

>[!NOTE]
> 根據預設，資訊安全中心支援下列法規標準：Azure CIS、PCI DSS 3.2、ISO 27001 和 SOC TSP。 
>
> [動態相容性套件 (預覽)](update-regulatory-compliance-packages.md) 功能可讓您將法規合規性儀表板中顯示的標準升級至新的*動態*套件。 您也可以使用相同的預覽功能來新增合規性套件，並監視其他標準的合規性。 

1.  在資訊安全中心主功能表中的 [原則與合規性]  底下，選取 [法規合規性]  。 <br>
在畫面頂端，您會看到合規性狀態的概觀和一組支援的合規性規章顯示在儀表板上。 您可以看到整體的合規性分數，以及每個標準評量的成功和失敗數目。

    ![電腦描述高信賴度](./media/security-center-compliance-dashboard/compliance-dashboard.png)

2.  選取與您相關的合規性標準索引標籤。 您會看到該標準的所有控制項清單。 對於適用的控制項，您可以檢視與該控制項相關的成功和失敗評量詳細資料。 有些控制項呈現灰色。這些控制項沒有任何相關聯的資訊安全中心評量。 請檢查這些控制項的需求，並自行在環境中對其進行評估。 其中有些項目可能與處理程序相關，而非技術性項目。

    ![合規性索引標籤](./media/security-center-compliance-dashboard/compliance-pci.png)

1. 若要產生並下載內有目前特殊標準合規性狀態摘要的 PDF 報告，請按一下 [下載報告]  。

    此報告會根據資訊安全中心的評估資料來為所選定的標準提供合規性狀態高階摘要，並且會根據該特殊標準的控制來加以組織。 此報告可與利害關係人共用，且可作為證據來提供給內部和外部稽核員。

    ![下載這篇文章](./media/security-center-compliance-dashboard/download-report.png)

## <a name="improve-your-compliance-posture"></a>改善您的合規性狀況

基於法規合規性儀表板中的資訊，您可以藉由在儀表板內直接處理建議來改善合規性狀況。

1.  按一下儀表板上顯示的任何失敗評量，即可檢視該項建議的詳細資料。 每項建議都包含一組應遵循著來解決問題的補救步驟。

1.  您可以選取特定資源以檢視更多詳細資料，並處理該資源的建議。 <br>例如，在 [Azure CIS 標準]  索引標籤上，您可以按一下建議：**需要安全傳輸至儲存體帳戶**。

    ![合規性建議](./media/security-center-compliance-dashboard/compliance-recommendation.png)

1. 當您按一下建議資訊並選取狀況不良的資源後，系統會直接將您引導至 Azure 入口網站中啟用**安全儲存體傳輸**的體驗。

    如需有關如何套用建議的詳細資訊，請參閱[實作 Azure 資訊安全中心的安全性建議](security-center-recommendations.md)。

    ![合規性建議](./media/security-center-compliance-dashboard/compliance-remediate-recommendation.png)

1.  在您採取行動來處理建議之後，您會看到合規性儀表板報告中產生了影響，因為合規性分數已改善。

    > [!NOTE]
    > 系統大約會每隔 12 小時執行一次評量，因此您只會在評量執行後看到合規性資料上的影響。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何使用資訊安全中心的法規合規性儀表板來執行下列動作：

-   檢視及監視與重要標準和法規相關的合規性狀況。

-   藉由處理相關建議來改善合規性狀態，並觀看合規性分數的改善。

法規合規性儀表板可以大幅簡化合規性程序，並大幅減少為 Azure 和混合式環境收集合規性辨識項所需的時間。

若要深入了解，請參閱：

-   [在法規合規性儀表板 (預覽) 中更新為動態合規性套件](update-regulatory-compliance-packages.md) - 了解這項預覽版功能如何讓您將法規合規性儀表板中顯示的標準更新為新的*動態*套件。 您也可以使用相同的預覽功能來新增合規性套件，並監視其他標準的合規性。 

-   [Azure 資訊安全中心的安全性健全狀況監視](security-center-monitoring.md) - 了解如何監視 Azure 資源的健全狀況。

-   [管理 Azure 資訊安全中心的安全性建議](security-center-recommendations.md)了解如何使用 Azure 資訊安全中心的建議來協助您保護 Azure 資源。

-   [改善 Azure 資訊安全中心的安全分數](secure-score-security-controls.md) - 了解要如何設定弱點與安全性建議的優先順序，才最能改善安全性狀況。