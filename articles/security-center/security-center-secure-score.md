---
title: Azure 資訊安全中心中的安全分數 |Microsoft Docs
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
ms.openlocfilehash: 30405ce5cc875144fcd1cf83d4a3f883a0304989
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79415775"
---
# <a name="improve-your-secure-score-in-azure-security-center"></a>改善您 Azure 資訊安全中心的安全分數

> [!NOTE]
> 本文是關於先前版本的安全分數。 此安全分數體驗仍然可以從 UI 取得，但會隨著時間而被淘汰。 兩個安全分數體驗會並存執行，以實現更順暢的轉換。
>
> 如需較新安全分數的詳細資訊，請參閱[這裡](secure-score-security-controls.md)。
>

有這麼多提供安全優勢的服務，通常很難知道首先要採取哪些步驟來保護和加強您的工作負載。 安全分數會審查您的安全性建議，並為您排定優先順序，讓您知道要先執行哪些建議。 這可協助您找出最嚴重的資訊安全漏洞，以便優先安排調查。 安全分數是一種工具，可協助您評估工作負載的安全性狀態。

## <a name="secure-score-calculation"></a>安全分數計算

「資訊安全中心」會模擬安全分析師的工作，檢閱您的安全性建議並套用進階演算法來判斷每項建議的重要性。
Azure 資訊安全中心會持續審查您的使用中建議，並根據其計算您的安全分數，建議的分數是從其嚴重性和安全性最佳做法衍生而來，這會影響您的工作負載安全性。

資訊安全中心也會提供您**整體的安全分數**。 

**整體安全分數**是您所有建議分數的累積。 您可以根據您選取的內容，在訂用帳戶或管理群組中查看您的整體安全分數。 分數將根據所選訂用帳戶和這些訂用帳戶使用中的建議而有所不同。

若要檢查哪些建議會影響您的安全分數，您可以在 [資訊安全中心儀表板] 中查看前三個最影響力的建議，也可以使用 [**安全分數影響**] 資料行，在 [建議] 清單分頁中排序建議。

若要查看您的整體安全分數：

1. 在 Azure 儀表板中，按一下 [**資訊安全中心**]，然後按一下 [**安全分數**]。

2. 在頂端，您可以看到安全分數重點：
   - **整體安全分數**代表每個所選訂用帳戶每個原則的分數
   - **依類別區分的安全分數**會顯示需要最多注意的資源
   - 「**安全分數影響」的最佳建議**會提供一份建議清單，讓您在執行時，能夠改善您的安全分數。
 
   ![安全分數](./media/security-center-secure-score/secure-score-dashboard.png)

3. 在下表中，您可以看到每個訂用帳戶和每個訂閱的整體安全分數。

   > [!NOTE]
   > 每個訂用帳戶的安全分數總和不等於整體安全分數。 安全分數是根據您狀況良好資源和每個建議的總資源之間的比率來計算，而不是在訂用帳戶之間的安全分數總和。 
   >
4. 按一下 [ **View 建議**] 以查看該訂用帳戶的建議，您可以進行補救以改善您的安全分數。
4. 在建議清單中，您可以看到每個建議都有代表**安全分數影響**的資料行。 如果您遵循建議，此數位代表您的整體安全分數會有多大的改善。 例如，在下面的畫面中，如果您**修復容器安全性設定中的弱點**，則您的安全分數會增加35點。

   ![安全分數](./media/security-center-secure-score/security-center-secure-score1.png)



## <a name="individual-secure-score"></a>個別安全分數

此外，若要查看個別的安全分數，您可以在個別的建議分頁中找到這些分數。  

**建議的安全分數**是根據狀況良好資源與總資源之間的比率計算。 如果狀況良好的資源數目等於資源總數，您會取得建議的最大安全分數50。 若要嘗試讓您的安全分數接近最高分，請遵循建議來修正狀況不良的資源。

**建議的影響**可讓您知道當您套用建議步驟時，安全分數的改善程度。 例如，如果您的安全分數為42，而**建議的影響**為 + 3，則執行建議中所述的步驟，可將您的分數提升為45。

該建議會顯示未執行補救步驟時，工作負載會面臨哪些威脅。

![個別建議安全分數](./media/security-center-secure-score/indiv-recommendation-secure-score.png)


## <a name="next-steps"></a>後續步驟
本文說明如何使用 Azure 資訊安全中心中的**安全分數**來改善安全性狀態。 若要深入了解資訊安全中心，請參閱：

* [Azure 資訊安全中心常見問題集](security-center-faq.md)-- 尋找有關使用服務的常見問題。
* [Azure 資訊安全中心的安全性健康狀態監視](security-center-monitoring.md)--了解如何監視 Azure 資源的健康狀態。
