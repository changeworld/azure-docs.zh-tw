---
title: Azure 安全中心的安全分數 |微軟文檔
description: " 使用 Azure 安全中心中的安全分數確定安全建議的優先順序。 "
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79415775"
---
# <a name="improve-your-secure-score-in-azure-security-center"></a>改善您 Azure 資訊安全中心的安全分數

> [!NOTE]
> 本文是關於安全分數的早期版本。 此安全分數體驗仍可從 UI 獲得，但隨著時間的推移將逐漸消除。 兩種安全分數體驗並排運行，以實現更平滑的過渡。
>
> 有關較新的安全分數的詳細資訊，請參閱[此處](secure-score-security-controls.md)。
>

有這麼多提供安全優勢的服務，通常很難知道首先要採取哪些步驟來保護和加強您的工作負載。 安全分數會審核您的安全建議並為其設定優先權，以便您知道首先執行哪些建議。 這可協助您找出最嚴重的資訊安全漏洞，以便優先安排調查。 安全分數是説明您評估工作負載安全狀況的工具。

## <a name="secure-score-calculation"></a>安全分數計算

「資訊安全中心」會模擬安全分析師的工作，檢閱您的安全性建議並套用進階演算法來判斷每項建議的重要性。
Azure 安全中心會不斷審核您的活動建議並基於它們計算安全分數，建議的分數源自其嚴重性和安全最佳實踐，這些最佳實踐對工作負荷安全性的影響最大。

安全中心還為您提供**整體安全分數**。 

**總體安全分數**是您所有推薦分數的累積。 您可以跨訂閱或管理組查看總體安全分數，具體取決於您選擇的內容。 分數將根據所選訂用帳戶和這些訂用帳戶使用中的建議而有所不同。

要檢查哪些建議對安全分數的影響最大，您可以在安全中心儀表板中查看影響最大的三個建議，也可以使用 **"安全分數影響**"列對建議清單邊欄中的建議進行排序。

要查看您的整體安全分數：

1. 在 Azure 儀表板中，按一下 **"安全中心**"，然後按一下 **"安全分數**"。

2. 在頂部，您可以看到安全分數亮點：
   - **總體安全分數**表示每個策略的分數，每個選定的訂閱
   - **按類別進行安全分數**顯示哪些資源最需要關注
   - **安全分數影響的頂級建議**為您提供了建議清單，這些建議將在實施時最提高您的安全分數。
 
   ![安全分數](./media/security-center-secure-score/secure-score-dashboard.png)

3. 在下表中，您可以看到每個訂閱以及每個訂閱的總體安全分數。

   > [!NOTE]
   > 每個訂閱的安全分數總和不等於總體安全分數。 安全分數是根據您的健康資源和每個建議的總資源之間的比率計算的，而不是整個訂閱的安全分數的總和。 
   >
4. 按一下 **"查看建議**"以查看該訂閱的建議，您可以修復這些建議以提高安全分數。
4. 在建議清單中，您可以看到，對於每個建議，都有一個表示**安全分數影響的**列。 此數位表示如果您遵循建議，您的總體安全分數將提高多少。 例如，在下面的螢幕中，如果**修復容器安全配置中的漏洞**，您的安全分數將增加 35 分。

   ![安全分數](./media/security-center-secure-score/security-center-secure-score1.png)



## <a name="individual-secure-score"></a>個人安全得分

此外，要查看單個安全分數，您可以在單個推薦邊欄選項卡中找到這些分數。  

**建議安全分數**是基於您的健康資源和總資源之間的比率的計算。 如果健康資源的數量等於資源總數，您將獲得建議 50 的最大安全分數。 要嘗試使安全分數更接近最高分數，請按照建議修復不正常的資源。

通過 **"建議"影響**，您可以瞭解應用建議步驟時的安全分數會提高多少。 例如，如果安全分數為 42，**並且"建議影響"為**+3，則執行建議中概述的步驟會提高您的分數變為 45。

該建議會顯示未執行補救步驟時，工作負載會面臨哪些威脅。

![個別建議安全分數](./media/security-center-secure-score/indiv-recommendation-secure-score.png)


## <a name="next-steps"></a>後續步驟
本文介紹如何使用 Azure 安全中心**中的安全分數**改進安全狀況。 若要深入了解資訊安全中心，請參閱：

* [Azure 資訊安全中心常見問題集](security-center-faq.md)-- 尋找有關使用服務的常見問題。
* [Azure 資訊安全中心的安全性健康狀態監視](security-center-monitoring.md)--了解如何監視 Azure 資源的健康狀態。
