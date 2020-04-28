---
title: 提供概念證明-Azure DevTest Labs |Microsoft Docs
description: 瞭解如何提供概念證明，讓 Azure DevTest Labs 可以成功合併到企業環境中。
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75643282"
---
# <a name="deliver-a-proof-of-concept"></a>提供概念證明 

Azure DevTest Labs 的其中一個主要案例是在雲端中啟用開發和測試環境。 範例包括：

* 在雲端建立開發人員桌面。
* 正在設定測試環境。
* 啟用虛擬機器和其他 Azure 資源的存取權。
* 設定沙箱區域讓開發人員學習和實驗。

DevTest Labs 也提供原則護欄和成本控制，讓企業能夠將「自助 Azure」提供給遵循公司安全性、法規和合規性政策的開發人員。 

每個企業都有不同的需求，可將 Azure DevTest Labs 成功合併到其環境中。 本文說明企業需要完成的最常見步驟，以確保成功的概念證明。 概念證明是成功完成端對端部署的第一個步驟。 

## <a name="getting-started"></a>開始使用 

以開始提供概念證明。 請務必花一些時間來瞭解 Azure 和 DevTest Labs。  以下是一些啟動資源： 

* [瞭解 Azure 入口網站](https://azure.microsoft.com/features/azure-portal/)
* [DevTest Labs 的基本概念](devtest-lab-overview.md)
* [DevTest Labs 支援的案例](devtest-lab-guidance-get-started.md)
* [DevTest Labs 企業檔](devtest-lab-guidance-prescriptive-adoption.md)
* [Azure 網路功能簡介](../virtual-network/virtual-networks-overview.md)

## <a name="prerequisites"></a>Prerequisites 

若要使用 DevTest Labs 成功完成試驗或概念證明，有幾個必要條件： 

* **Azure 訂**用帳戶：企業通常已有可存取 Azure 的現有[Enterprise 合約](https://azure.microsoft.com/pricing/purchase-options/enterprise-agreement/)，而且他們可以針對 DevTest Labs 使用現有或新的訂用帳戶。 或者，企業可以在試驗期間使用[Visual Studio 訂](https://azure.microsoft.com/pricing/member-offers/visual-studio-subscriptions/)用帳戶（利用免費的 Azure 點數）。 如果這兩個選項都無法使用，企業就可以建立並使用[免費的 Azure 帳戶](https://azure.microsoft.com/free/search/?&OCID=AID719825_SEM_g4lyBqgB&lnkd=Bing_Azure_Brand&msclkid=ecc4275a31b61375749e7a5322c20de8&dclid=CMGW5-m78-ICFaLt4QodmUwGtQ)。 如果有 Enterprise 合約，使用[Enterprise 開發/測試訂](https://azure.microsoft.com/offers/ms-azr-0148p/)用帳戶是存取 Windows 10/Windows 8.1 用戶端作業系統的絕佳選項，以及用於開發和測試工作負載的折扣費率。 
* **Azure Active Directory 租**使用者：若要啟用管理使用者（例如，新增使用者或新增實驗室擁有者），這些使用者必須是 Azure 訂用帳戶中用於試驗的[Azure Active Directory 租](https://azure.microsoft.com/services/active-directory/)使用者的一部分。 企業通常會設定混合式身分[識別](../active-directory/hybrid/whatis-hybrid-identity.md)，讓使用者能夠在雲端中使用他們的內部部署身分識別，但 DevTest Labs 試驗並不需要這麼做。 

## <a name="scoping-of-the-pilot"></a>試驗的範圍 

在開始執行之前，請務必規劃試驗。 事先瞭解資源不會無限期地為試驗的使用者設定適當的期望。 

> [!IMPORTANT]
> 我們無法強調 crisply 界定試驗的重要性，並事先設定期望。

開始進行試驗之前，請回答下列重要問題： 

* 您想要瞭解哪些內容，以及試驗的成功外觀為何？ 
* 試驗會涵蓋哪些工作負載或案例？ 請務必只定義一小部分，以確保試驗可以進行範圍設定並快速完成。 
* 實驗室中必須提供哪些資源？ 例如：自訂映射、marketplace 映射、原則、網路拓撲。 
* 哪些使用者和小組會參與試驗以驗證體驗？  
* 試驗的持續時間為何？ 選擇適合規劃範圍的時間範圍，例如兩周或一個月。 
* 試驗完成後，在試驗期間所使用的配置資源會發生什麼事？ 您是否計畫捨棄試驗資源？ 您可能會想：
   
   「如果我們可以計畫在試驗結束時擲回虛擬機器和實驗室，則我們可以為試驗設定單一訂用帳戶，並在我們的所有工作中完成，同時解決大規模推出的問題。」 

一般的趨勢是讓試驗「完美」，使其完全代表在公司推出服務後的最終狀態。 這是假的假設。 越接近「完美」，您就必須完成更多的工作，*才能*開始進行試驗。 試驗的目的是要做出相應增加和推出最終服務的適當決策。 

試驗的重點應該是選擇最小的必要工作負載和相依性，以回答 Azure DevTest Labs 是否為您企業的正確服務問題。 我們建議您選擇最低相依性最簡單的工作負載，以協助確保快速且乾淨的成功。 如果無法這麼做，請挑選最具代表性的工作負載來公開潛在的複雜性，以便在向外延展階段中複寫試驗階段的成功。 

下列範例示範範圍良好的概念證明。 

## <a name="example-proof-of-concept-plan"></a>範例：概念證明計畫 

本節顯示的範例可用來界定 DevTest Labs 試驗的概念證明。 

> [!TIP]
> 若要將您的專案設定失敗的可能性降到最低，我們強烈建議您不要略過本節中所述的範例。 

### <a name="overview"></a>概觀 

我們打算以 DevTest Labs 為基礎，在 Azure 中開發新的環境，以供廠商用來做為公司網路的隔離環境。 為了判斷解決方案是否符合需求，我們會開發驗證端對端解決方案的概念證明。 我們已包含數個廠商，可以試用並驗證體驗。 

### <a name="outcomes"></a>結果 

建立概念證明時，我們會先將重點放在結果上（我們想要達成什麼目標）。 在概念證明的結尾，我們預期： 

* 適用于廠商的端對端解決方案，可使用 Azure Active Directory （Azure AD）中的來賓帳戶來存取 Azure 中的隔離環境。 環境具有其生產力所需的資源。 
* 系統會列舉並瞭解任何會影響更廣泛規模使用和採用的潛在封鎖問題。
* 開發概念證明的人員對所有程式碼都有充分的瞭解。 他們也瞭解涉及的附屬物，並且確信會廣泛採用。

### <a name="open-questions-and-prerequisites"></a>開啟問題和必要條件 

* 我們是否已建立可用於此專案的訂用帳戶？ 
* 我們有 Azure AD 租使用者和 Azure AD 全域系統管理員識別誰可以提供 Azure AD 相關問題的說明和指引嗎？ 
* 我們有一個地方可以共同處理專案的個人嗎？ 

   * 原始程式碼和腳本（例如 Azure Repos） 
   * 檔（例如 Microsoft 小組或 SharePoint）  
   * 交談（例如 Microsoft 團隊） 
   * 工作專案（例如 Azure Boards） 
* 廠商所需的資源有哪些？ 這包括網路上可用的應用程式，兩者都在虛擬機器本機和其他必要的伺服器上。 
* 虛擬機器將會加入 Azure 中的網域嗎？ 若是如此，這會是 Azure Active Directory Domain Services （Azure AD DS）還是其他東西？ 
* 我們識別出將成為概念證明目標的小組或廠商嗎？ 誰會是環境的客戶？
* 我們會使用哪個 Azure 區域來進行概念證明？ 
* 除了 IaaS （Vm）以外，我們還有廠商可透過 DevTest Labs 使用的服務清單嗎？ 
* 我們打算如何使用實驗室來訓練廠商/使用者？ 

### <a name="components-of-the-proof-of-concept-solution"></a>概念證明解決方案的元件 

我們預期解決方案具有下列元件： 

* 各種廠商小組會在 Azure 中使用一組實驗室。
* 實驗室會連線到支援需求的網路基礎結構。
* 廠商可以透過 Azure AD 和角色指派來存取實驗室。
* 廠商有方法可以成功連線到其資源。 具體而言，站對站 VPN 可讓您直接存取虛擬機器，而不需要公用 IP 位址。
* 一組成品涵蓋廠商在虛擬機器上所需的軟體。

## <a name="additional-planning-and-design-decisions"></a>其他規劃和設計決策 

在您發行完整的 DevTest Labs 解決方案之前，您必須先進行一些重要的規劃和設計決策。 處理概念證明的經驗可以協助您進行這些決策。 進一步考慮包括： 

* **訂**用帳戶拓撲 .. 在 Azure 中，資源的企業層級需求可以延伸超過[單一訂用帳戶內可用的配額](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits)。 這需要多個 Azure 訂用帳戶和/或服務要求，以增加初始訂閱限制。 請務必事先決定如何在訂用帳戶之間散發資源。 有一項重要的資源是訂用帳戶[決策指南](https://docs.microsoft.com/azure/architecture/cloud-adoption/decision-guides/subscriptions/)，因為稍後很難以將資源移至另一個訂用帳戶。 例如，在建立實驗室之後，無法將其移至另一個訂用帳戶。  
* **網路拓撲**： DevTest Labs 自動建立的[預設網路基礎結構](../app-service/networking-features.md)可能不足以符合企業使用者的需求和限制。 常見的情況是查看[Azure ExpressRoute 連線的虛擬網路](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/)、[中樞和輪輻](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)，以在訂用帳戶之間連接，甚至是[強制路由](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md)，以確保僅內部部署連線能力。 DevTest Labs 可讓現有的虛擬網路連接到實驗室，以在您于實驗室中建立新的虛擬機器時啟用其使用。 
* **虛擬機器的遠端存取**：有許多選項可供遠端存取位於 DevTest Labs 中的虛擬機器。 最簡單的方法是使用公用 Ip 或共用公用 Ip。 這些是[實驗室中可用的設定](devtest-lab-shared-ip.md)。 如果這些選項不夠，使用遠端存取閘道也是一個選項。 此選項會顯示在[DevTest labs 的「企業參考架構](devtest-lab-reference-architecture.md)」上，並在[DevTest Labs 遠端桌面閘道檔](configure-lab-remote-desktop-gateway.md)中進一步說明。 企業也可以使用 ExpressRoute 或站對站 VPN，將其實驗室連線到其內部部署網路。 此選項可讓您根據其私人 IP 位址，對虛擬機器進行直接遠端桌面或 SSH 連線，而不會暴露在網際網路上。 
* **處理許可權**： DevTest Labs 常使用的兩個主要許可權是[擁有者和實驗室使用者](devtest-lab-add-devtest-user.md)。 請務必先決定要在實驗室中的每個存取層級上進行信賴的 DevTest Labs。 一般模型是預算擁有者（例如，小組組長）做為實驗室擁有者，而小組成員則是實驗室使用者。 此模型可讓負責預算的人員（小組負責人）調整原則設定，並讓小組維持在預算內。  

## <a name="completing-the-proof-of-concept"></a>完成概念證明 

涵蓋預期的學習之後，即可完成試驗。 這是收集使用者意見反應的時間，判斷試驗是否成功，並決定組織是否會在企業中的 DevTest Labs 擴展首度推出。 這也是考慮自動化部署 DevTest Labs 和相關資源的絕佳時機，以確保整個擴展推出的一致性。 

## <a name="next-steps"></a>後續步驟 

* [DevTest Labs 企業檔](devtest-lab-guidance-prescriptive-adoption.md)
* [企業的參考架構](devtest-lab-reference-architecture.md)
* [相應增加您的 DevTest Labs 部署](devtest-lab-guidance-orchestrate-implementation.md)
* [協調 Azure DevTest Labs 的實作](devtest-lab-guidance-orchestrate-implementation.md)
