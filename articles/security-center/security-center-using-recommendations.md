---
title: 使用 Azure 資訊安全中心建議增強安全性 | Microsoft Docs
description: " 了解如何使用「Azure 資訊安全中心」中的安全性原則和建議，來協助降低安全性攻擊的危害。 "
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2019
ms.author: memildin
ms.openlocfilehash: a1034eb47010da2b0e795ee8c79646f06151cac1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77603277"
---
# <a name="use-azure-security-center-recommendations-to-enhance-security"></a>使用 Azure 資訊安全中心建議增強安全性
您可以設定安全性原則，然後實作 Azure 資訊安全中心提供的建議，以降低發生重大安全性事件的機會。 本文說明如何使用資訊安全中心的安全性原則和建議，以協助減少安全性攻擊。 

安全中心自動運行連續掃描以分析 Azure 資源的安全狀態。 當資訊安全中心發現潛在的安全性弱點時會建立建議，引導您完成設定所需安全性控制項的程序。 安全中心在 24 小時內更新其建議，但以下例外情況除外：

- 作業系統安全配置建議在 48 小時內更新
- 端點保護問題建議將在 8 小時內更新

## <a name="scenario"></a>狀況
此案例示範如何使用資訊安全中心，透過監控資訊安全中心建議並採取動作，以協助降低發生安全性事件的機會。 此案例使用虛構公司 Contoso，以及資訊安全中心[規劃與操作指南](security-center-planning-and-operations-guide.md#security-roles-and-access-controls)中出現的角色。 在此案例中，我們著重於下列人物的角色：

![案例角色](./media/security-center-using-recommendations/scenario-roles.png)

Contoso 最近將一些內部部署資源移轉至 Azure。 Contoso 想要保護其資源，並減少雲端中其資源的弱點。

## <a name="use-azure-security-center"></a>使用 Azure 資訊安全中心
來自 Contoso IT 安全性部門的 David，已經選擇將 Contoso 訂用帳戶上的資訊安全中心上架到 Azure 資訊安全中心，以防止和偵測安全性弱點。 

資訊安全中心會自動分析 Contoso Azure 資源的安全性狀態並套用預設安全性原則。 當安全中心識別潛在的安全性漏洞時，它會根據安全性原則中設置的控制項創建**建議**。 

David 在所有訂閱中運行 Azure 安全標準層，以獲得完整的建議和安全功能套件。 Jeff 還將所有尚未遷移到雲的現有本機伺服器放在一起，以便他們可以利用其[Windows](quick-onboard-windows-computer.md)和[Linux](quick-onboard-linux-computer.md)伺服器的安全中心混合支援。

Jeff 是雲端工作負載擁有者。 Jeff 負責根據 Contoso 的安全性原則，套用安全性控制項。 

Jeff 會執行下列工作：

- 監視資訊安全中心提供的安全性建議
- 評估安全建議並決定是否應應用或拒絕這些建議。
- 套用安全性建議

### <a name="remediate-threats-using-recommendations"></a>利用建議補救威脅
作為日常監視活動的一部分，Jeff 登錄到 Azure 並打開安全中心。 

1. Jeff 選擇工作負荷的訂閱。

2. Jeff 檢查**安全分數**，瞭解訂閱的安全性，並看到分數為 548。

3. Jeff 必須決定要先處理哪些建議。 因此，傑夫點擊安全得分，並開始處理建議的基礎上，它多少改善他的[安全得分的影響](security-center-secure-score.md)。

4. Jeff 有大量已連線的 VM 和伺服器，因此決定著重於 [計算和應用程式]****。

5. 當 Jeff 按一下 **"計算"和"應用**"時，他們會看到建議清單，並根據安全分數影響處理這些建議。

6. Jeff 有許多面向 Internet 的 VM，並且由於它們的埠暴露，他們擔心攻擊者可能會控制伺服器。 因此，Jeff 選擇使用[**即時 VM 訪問**](security-center-just-in-time.md)。

Jeff 繼續瀏覽高優先權和中優先順序的建議，然後進行實作決策。 對於每個建議，Jeff 都會查看安全中心提供的詳細資訊，以瞭解哪些資源受到影響、安全分數影響是什麼、每個建議的含義以及如何緩解每個問題的補救步驟。

## <a name="conclusion"></a>結論
監控資訊安全中心的建議有助於在發生攻擊之前消除安全性弱點。 修復建議時，安全分數和工作負載的安全狀態會得到改善。 資訊安全中心會自動探索您所部署的新資源、根據您的安全性原則進行評估，並提供新的建議來保護它們。


## <a name="next-steps"></a>後續步驟
請確定您已備妥監視程序，您會在監視過程中定期檢查資訊安全中心的建議，以確保資源在一段時間內保持安全無虞。

本案例示範如何使用資訊安全中心的安全性原則和建議，以協助減少安全性攻擊。

瞭解如何通過[管理和回應安全警報](security-center-managing-and-responding-alerts.md)來回應威脅。
