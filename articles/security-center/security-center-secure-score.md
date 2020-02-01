---
title: Azure 資訊安全中心的安全分數 | Microsoft Docs
description: " 使用 Azure 資訊安全中心中的安全分數，設定安全性建議的優先順序。 "
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/15/2019
ms.author: memildin
ms.openlocfilehash: 79154a13722654ef5cbbe7ac99bb67d4b761fe60
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2020
ms.locfileid: "76903455"
---
# <a name="improve-your-secure-score-in-azure-security-center"></a>在 Azure 資訊安全中心中改善您的安全分數

> [!NOTE]
> 預覽中提供了增強的安全分數。 增強的安全分數最終會取代現有的安全分數，但會在一段時間並存執行，以簡化轉換。
>
> 如需增強型安全分數之優點的詳細資訊，請參閱[這裡](secure-score-security-controls.md)。
>
> 若要參與預覽，請開啟 Azure 入口網站，啟動 Azure 資訊安全中心，然後選取 [安全分數]。 在那裡，您會在頁面頂端看到一個橫幅，提供新的安全分數體驗。 或者，按一下[這裡](https://aka.ms/ascnewscore)。

有這麼多提供安全優勢的服務，通常很難知道首先要採取哪些步驟來保護和加強您的工作負載。 Azure 安全分數會檢閱您的安全性建議，並為您排列這些建議的優先順序，讓您了解要先執行哪些建議。 這可協助您找出最嚴重的資訊安全漏洞，以便優先安排調查。 安全分數是一個可協助您評估工作負載安全性狀態的工具。

## <a name="secure-score-calculation"></a>安全分數的計算

「資訊安全中心」會模擬安全分析師的工作，檢閱您的安全性建議並套用進階演算法來判斷每項建議的重要性。
Azure 資訊安全中心會持續審查您的使用中建議，並根據其計算您的安全分數，建議的分數是從其嚴重性和安全性最佳做法衍生而來，這會影響您的工作負載安全性。

「資訊安全中心」也會為您提供一個**整體安全分數**。 

**整體安全分數**是您所有建議分數的累計。 您可以在訂用帳戶或管理群組中檢視整體安全分數，具決於您選取的項目。 分數將根據所選訂用帳戶和這些訂用帳戶使用中的建議而有所不同。

若要檢查哪些建議會對您的安全分數產生最大影響，您可以檢視「資訊安全中心」儀表板中前 3 個最具影響力的建議，或使用 [安全分數影響] 資料行來排序建議清單刀鋒視窗中的建議。

檢視您的整體安全分數：

1. 在 Azure 資訊安全中心儀表板上按一下 [資訊安全中心]，然後按一下 [安全分數]。

2. 您可以在頂端看到安全性分數重點：
   - [整體安全性分數] 代表每個所選訂用帳戶每個原則的分數
   - [依類別計的安全分數] 顯示最需要注意的資源
   - [依安全性分數的重要建議] 提供建議清單，如果您實作其中的項目將能大幅改善您的安全性分數。
 
   ![安全分數](./media/security-center-secure-score/secure-score-dashboard.png)

3. 您可以在下面的的表格中查看每個您的訂用帳戶及每個的整體安全性分數。

   > [!NOTE]
   > 每個訂用帳戶的安全性分數總和不等於整體安全性分數。 安全性分數的計算方式是根據每個建議計算您健康資源和您總資源的比例，而不是您所有訂用帳戶安全性分數的總和。 
   >
4. 按一下 [檢視建議] 來查看該訂用帳戶的建議，根據建議來修復可以改善您的安全性分數。
4. 在建議清單中，您可以看到每個建議都有代表**安全分數影響**的資料行。 此數字代表如果您遵循建議，將會改善多少整體安全分數。 例如，在下面畫面中，如果您**補救容器安全性設定中的漏洞**，您的安全分數將增加 35 分。

   ![安全分數](./media/security-center-secure-score/security-center-secure-score1.png)



## <a name="individual-secure-score"></a>個別安全分數

此外，若要檢視個別安全分數，您可以在個別建議刀鋒視窗中找到這些分數。  

**建議安全分數**會根據您狀況良好資源與總資源之間的比率來計算。 如果狀況良好的資源數目等於資源總數，您會獲得建議的最高安全分數 50。 若要讓您的安全分數接近最高分，請遵循建議來修正狀況不良的資源。

[建議的影響] 可讓您了解如果套用建議步驟，您的安全分數會提升多少。 例如，如果您的安全分數為 42 且 [建議的影響] 為 +3，則執行建議中所述的步驟可將您的安全分數提升至 45。

該建議會顯示未執行補救步驟時，工作負載會面臨哪些威脅。

![個別建議安全分數](./media/security-center-secure-score/indiv-recommendation-secure-score.png)







## <a name="next-steps"></a>後續步驟
本文說明如何使用 Azure 資訊安全中心的**安全分數**來改善安全性狀態。 若要深入了解資訊安全中心，請參閱：

* [Azure 資訊安全中心常見問題集](security-center-faq.md)-- 尋找有關使用服務的常見問題。
* [Azure 資訊安全中心的安全性健康狀態監視](security-center-monitoring.md)--了解如何監視 Azure 資源的健康狀態。


