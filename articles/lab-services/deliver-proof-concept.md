---
title: 提供概念驗證 - Azure 開發人員測試實驗室 |微軟文檔
description: 瞭解如何提供概念驗證，以便 Azure 開發人員測試實驗室可以成功集成到企業環境中。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: tanmayeekamath
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2018
ms.author: takamath
ms.openlocfilehash: ca843213760cee60799568a6f33059c2bd91c835
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75643282"
---
# <a name="deliver-a-proof-of-concept"></a>提供概念證明 

Azure 開發人員測試實驗室的關鍵方案之一是在雲中啟用開發和測試環境。 範例包括：

* 在雲中創建開發人員桌面。
* 配置用於測試的環境。
* 啟用對虛擬機器和其他 Azure 資源的訪問。
* 設置一個沙箱區域，供開發人員學習和實驗。

DevTest Labs 還提供策略保護欄和成本控制，使企業能夠向遵守公司安全、法規和合規性策略的開發人員提供"自助服務 Azure"。 

每個企業對於如何成功地將 Azure 開發人員測試實驗室集成到其環境中有不同的要求。 本文介紹了企業為確保概念驗證成功而需要完成的最常見步驟。 概念證明是成功進行端到端部署的第一步。 

## <a name="getting-started"></a>開始使用 

開始提供概念證明。 花一些時間瞭解 Azure 和開發人員測試實驗室非常重要。  下面是一些起始資源： 

* [瞭解 Azure 門戶](https://azure.microsoft.com/features/azure-portal/)
* [開發測試實驗室基礎知識](devtest-lab-overview.md)
* [開發人員測試實驗室支援的方案](devtest-lab-guidance-get-started.md)
* [開發人員測試實驗室企業文檔](devtest-lab-guidance-prescriptive-adoption.md)
* [Azure 網路簡介](../virtual-network/virtual-networks-overview.md)

## <a name="prerequisites"></a>Prerequisites 

要通過 DevTest Labs 成功完成試驗或概念驗證，有以下幾個先決條件： 

* **Azure 訂閱**：企業通常擁有支援訪問 Azure 的現有[企業協定](https://azure.microsoft.com/pricing/purchase-options/enterprise-agreement/)，並且可以使用 DevTest Labs 的現有訂閱或新訂閱。 或者，企業可以在試驗期間使用[Visual Studio 訂閱](https://azure.microsoft.com/pricing/member-offers/visual-studio-subscriptions/)（利用免費的 Azure 積分）。 如果這兩個選項都不可用，則企業可以創建和使用[免費的 Azure 帳戶](https://azure.microsoft.com/free/search/?&OCID=AID719825_SEM_g4lyBqgB&lnkd=Bing_Azure_Brand&msclkid=ecc4275a31b61375749e7a5322c20de8&dclid=CMGW5-m78-ICFaLt4QodmUwGtQ)。 如果有企業協定，使用[企業開發人員/測試訂閱](https://azure.microsoft.com/offers/ms-azr-0148p/)是訪問 Windows 10/Windows 8.1 用戶端作業系統和開發和測試工作負載的折扣率的絕佳選擇。 
* **Azure 活動目錄租戶**：要啟用管理使用者（例如，添加使用者或添加實驗室擁有者），這些使用者必須是試用版 Azure 訂閱中使用的[Azure 活動目錄租戶](https://azure.microsoft.com/services/active-directory/)的一部分。 通常，企業會設置[混合標識](../active-directory/hybrid/whatis-hybrid-identity.md)，使使用者能夠在雲中使用其本地標識，但 DevTest Labs 試驗不需要這樣做。 

## <a name="scoping-of-the-pilot"></a>試點範圍 

在開始實施之前規劃試驗非常重要。 提前知道資源不會無限期地存在，這為試點使用者設定了適當的期望。 

> [!IMPORTANT]
> 我們再強調一下，明確確定試點範圍和預先設定期望值的重要性是不夠的。

在啟動飛行員之前回答以下關鍵問題： 

* 你想學什麼，成功對飛行員來說是什麼樣子的？ 
* 試驗將涵蓋哪些工作負載或方案？ 請務必僅定義一個小型集，以確保可以快速確定試驗的範圍並完成。 
* 實驗室中必須提供哪些資源？ 例如：自訂映射、市場映射、策略、網路拓撲。 
* 誰將參與試點以驗證體驗的使用者和團隊？  
* 飛行員的持續時間是多少？ 選擇與計畫範圍很好地一致的時間範圍，例如兩周或一個月。 
* 試驗完成後，在試驗期間使用的已分配資源會發生什麼情況？ 您是否計畫放棄試驗資源？ 您可能會認為：
   
   "如果我們可以在試驗結束時計畫放棄虛擬機器和實驗室，那麼我們可以為試點設置單個訂閱，並在那裡完成所有工作，同時並行解決規模部署問題。 

有一種普遍的趨勢，使試點"完美"，所以它同樣代表最終狀態後，服務將在公司推出。 這是一個錯誤的假設。 離"完美"越近，在開始試航*之前*，你越需要完成。 試點的目的是在擴大和推出最終服務方面做出正確的決定。 

試點的重點應該是選擇最起碼的必需工作負載和依賴項，以便回答 Azure DevTest Labs 是否適合您的企業服務的問題。 我們建議您選擇依賴性最小的最簡單的工作負載，以説明確保快速、乾淨地成功。 如果這是不可能的，選擇最具代表性的工作負載，以暴露潛在的複雜性，以便在橫向擴展階段複製試驗階段的成功。 

下面的示例演示了範圍廣的概念證明。 

## <a name="example-proof-of-concept-plan"></a>示例：概念驗證計畫 

本節顯示了用於界定 DevTest Labs 試驗概念驗證的示例。 

> [!TIP]
> 為了最大程度地將專案設置為失敗的可能性，我們強烈建議您不要跳過本節中描述的示例。 

### <a name="overview"></a>總覽 

我們計畫在 Azure 中開發一個基於 DevTest Labs 的新環境，供供應商用作與商業網路隔離的環境。 為了確定解決方案是否滿足要求，我們將開發一個概念驗證來驗證端到端解決方案。 我們包括幾個供應商來嘗試和驗證體驗。 

### <a name="outcomes"></a>結果 

構建概念驗證時，我們首先關注結果（我們試圖實現什麼）。 在概念證明結束時，我們期望： 

* 供應商使用 Azure 活動目錄 （Azure AD） 中的來賓帳戶訪問 Azure 中的隔離環境的工作端到端解決方案。 環境具有高效工作所需的資源。 
* 列舉和理解影響更廣泛使用和採用的任何潛在阻塞問題。
* 參與開發概念證明的個人對所有代碼都有很好的瞭解。 他們還瞭解所涉及的附帶關係，並有信心更廣泛地採用。

### <a name="open-questions-and-prerequisites"></a>開放式問題和先決條件 

* 我們是否創建了可用於此專案的訂閱？ 
* 我們是否確定了 Azure AD 租戶和 Azure AD 全域管理員，誰可以為 Azure AD 相關問題提供説明和指導？ 
* 我們有地方為從事該專案的個人進行合作嗎？ 

   * 原始程式碼和腳本（如 Azure 存儲庫） 
   * 文檔（如微軟團隊或 SharePoint）  
   * 對話（如微軟團隊） 
   * 工作項（如 Azure 板） 
* 供應商需要哪些資源？ 這包括網路上可用的應用程式，無論是在虛擬機器和其他必需伺服器上的本地應用程式。 
* 虛擬機器是否會加入到 Azure 中的域？ 如果是，這將是 Azure 活動目錄域服務 （Azure AD DS） 或其他內容？ 
* 我們是否確定了將成為概念證明目標的團隊或供應商？ 誰將是環境的客戶？
* 我們將使用哪個 Azure 區域來驗證概念？ 
* 除了 IaaS （VM）之外，我們是否還有供應商允許通過 DevTest Labs 使用的服務清單？ 
* 我們計畫如何培訓供應商/使用者使用實驗室？ 

### <a name="components-of-the-proof-of-concept-solution"></a>概念驗證解決方案的組成部分 

我們期望解決方案具有以下元件： 

* 各種供應商團隊將在 Azure 中使用一組實驗室。
* 實驗室連接到支援這些要求的網路基礎結構。
* 供應商可以通過 Azure AD 和角色指派訪問實驗室。
* 供應商有一種方法來成功連接到其資源。 具體來說，網站到網站 VPN 允許在沒有公共 IP 位址的情況下直接存取虛擬機器。
* 一組工件涵蓋了供應商在虛擬機器上所需的軟體。

## <a name="additional-planning-and-design-decisions"></a>其他規劃和設計決策 

在發佈完整的 DevTest Labs 解決方案之前，您必須做出一些重要的規劃和設計決策。 研究概念證明的經驗可以説明您做出這些決策。 進一步考慮包括： 

* **訂閱拓撲**：Azure 中資源的企業級要求可以擴展到[單個訂閱中的可用配額之外](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits)。 這需要多個 Azure 訂閱和/或服務請求來增加初始訂閱限制。 預先決定如何在訂閱之間分配資源非常重要。 一個有價值的資源是[訂閱決策指南](https://docs.microsoft.com/azure/architecture/cloud-adoption/decision-guides/subscriptions/)，因為以後很難將資源移動到另一個訂閱。 例如，實驗室在創建後無法移動到其他訂閱。  
* **網路拓撲**：DevTest Labs 自動創建的[預設網路基礎結構](../app-service/networking-features.md)可能不足以滿足企業使用者的要求和約束。 通常可以看到[Azure ExpressRoute 連接的虛擬網路](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/)、跨訂閱連接[的集線器和分支](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)，甚至[強制路由](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md)以確保僅本地連接。 DevTest Labs 允許將現有虛擬網路連接到實驗室，以便在您在實驗室中創建新虛擬機器時啟用這些網路。 
* **虛擬機器的遠端存取**：有許多選項可以遠端存取位於 DevTest Labs 中的虛擬機器。 最簡單的是使用公共 IP 或共用公共 IP。 這些是[實驗室中可用的設置](devtest-lab-shared-ip.md)。 如果這些選項不夠，則使用遠端存取閘道也是一個選項。 此選項顯示在[DevTest Labs 企業參考體系結構](devtest-lab-reference-architecture.md)上，並在[DevTest Labs 遠端桌面閘道文檔中](configure-lab-remote-desktop-gateway.md)進一步描述。 企業還可以使用 ExpressRoute 或網站到網站 VPN 將其實驗室連接到其本地網路。 此選項允許基於虛擬機器的私人 IP 位址直接連接到虛擬機器，無需暴露 Internet。 
* **處理許可權**：開發人員測試實驗室中常用的兩個金鑰許可權是[擁有者和實驗室使用者](devtest-lab-add-devtest-user.md)。 在廣泛推出 DevTest Labs 之前，必須決定誰將受委託在實驗室中進行每個級別的訪問。 常見模型是作為實驗室擁有者的預算擁有者（例如，團隊主管）和作為實驗室使用者的團隊成員。 此模型使負責預算的人員（團隊領導）能夠調整策略設置，並使團隊保持在預算之內。  

## <a name="completing-the-proof-of-concept"></a>完成概念證明 

完成預期學習後，是時候完成試驗了。 現在是時候收集使用者的回饋，確定試驗是否成功，並決定組織是否會在企業中大規模推出 DevTest Labs。 這也是考慮自動部署 DevTest 實驗室和相關資源以確保整個規模部署的一致性的好時機。 

## <a name="next-steps"></a>後續步驟 

* [開發人員測試實驗室企業文檔](devtest-lab-guidance-prescriptive-adoption.md)
* [企業參考體系結構](devtest-lab-reference-architecture.md)
* [擴展開發測試實驗室部署](devtest-lab-guidance-orchestrate-implementation.md)
* [協調 Azure DevTest Labs 的實作](devtest-lab-guidance-orchestrate-implementation.md)
