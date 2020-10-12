---
title: 提供概念證明-Azure DevTest Labs |Microsoft Docs
description: 瞭解如何提供概念證明，讓 Azure DevTest Labs 可以成功併入企業環境。
ms.topic: article
ms.date: 06/2/2020
ms.openlocfilehash: 9c28cf9eebd8a39a2edce48e4fb8b96dc7608d80
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87288023"
---
# <a name="deliver-a-proof-of-concept"></a>提供概念證明 

Azure DevTest Labs 的其中一個主要案例是在雲端中啟用開發與測試環境。 範例包括：

* 在雲端中建立開發人員桌面。
* 設定用於測試的環境。
* 啟用虛擬機器和其他 Azure 資源的存取權。
* 設定沙箱區域以供開發人員學習及實驗。

DevTest Labs 也提供原則護欄和成本控制，讓企業能夠將「自助 Azure」提供給符合公司安全性、法規和合規性政策的開發人員。 

每個企業都有不同的需求，可將 Azure DevTest Labs 成功併入其環境中。 本文描述企業完成的最常見步驟，以確保成功的概念證明。 概念證明是成功進行端對端部署的第一步。 

## <a name="getting-started"></a>開始使用 

以開始提供概念證明。 請務必花一些時間來瞭解 Azure 和 DevTest Labs。  以下是一些啟動資源： 

* [瞭解 Azure 入口網站](https://azure.microsoft.com/features/azure-portal/)
* [DevTest Labs 的基本概念](devtest-lab-overview.md)
* [DevTest Labs 支援的案例](devtest-lab-guidance-get-started.md)
* [DevTest Labs 企業檔](devtest-lab-guidance-prescriptive-adoption.md)
* [Azure 網路簡介](../virtual-network/virtual-networks-overview.md)

## <a name="prerequisites"></a>Prerequisites 

若要使用 DevTest Labs 順利完成試驗或概念證明，有幾個必要條件： 

* **Azure 訂**用帳戶：企業通常有現成的 [Enterprise 合約](https://azure.microsoft.com/pricing/purchase-options/enterprise-agreement/) 可存取 Azure，而且可以使用現有或新的訂用帳戶來 DevTest Labs。 或者，企業可以在試驗期間使用 [Visual Studio 訂](https://azure.microsoft.com/pricing/member-offers/visual-studio-subscriptions/) 用帳戶， (利用免費的 Azure 點數) 。 如果這兩個選項都無法使用，企業就可以建立及使用 [免費的 Azure 帳戶](https://azure.microsoft.com/free/search/?&OCID=AID719825_SEM_g4lyBqgB&lnkd=Bing_Azure_Brand&msclkid=ecc4275a31b61375749e7a5322c20de8&dclid=CMGW5-m78-ICFaLt4QodmUwGtQ)。 如果有 Enterprise 合約，使用 [Enterprise 開發/測試訂](https://azure.microsoft.com/offers/ms-azr-0148p/) 用帳戶是存取 Windows 10/Windows 8.1 用戶端作業系統的絕佳選項，以及開發和測試工作負載的折扣費率。 
* **Azure Active Directory 租**使用者：若要啟用管理使用者 (例如，新增使用者或新增實驗室擁有者) ，這些使用者必須是 Azure 訂用帳戶中用於試驗之 [Azure Active Directory 租](https://azure.microsoft.com/services/active-directory/) 使用者的一部分。 企業通常會設定混合式身分 [識別](../active-directory/hybrid/whatis-hybrid-identity.md) ，讓使用者能夠在雲端中使用其內部部署身分識別，但 DevTest Labs 試驗並不需要這麼做。 

## <a name="scoping-of-the-pilot"></a>試驗範圍 

開始執行之前，請務必規劃試驗。 事先知道資源不會無限期地為試驗的使用者設定適當的期望。 

> [!IMPORTANT]
> 我們無法強調 crisply 範圍設定試驗和事先設定期望的重要性。

開始進行試驗之前，請先回答下列重要問題： 

* 您想要學習什麼，以及成功的試驗結果為何？ 
* 試驗中將涵蓋哪些工作負載或案例？ 請務必只定義一小部分，以確保試驗可以設定範圍並快速完成。 
* 實驗室中必須提供哪些資源？ 例如：自訂映射、marketplace 映射、原則、網路拓撲。 
* 哪些使用者和小組將參與試驗以驗證體驗？  
* 試驗的持續時間為何？ 選擇適合規劃範圍的時間範圍，例如兩周或一個月。 
* 完成試驗之後，在試驗期間使用的配置資源會發生什麼事？ 您是否計畫捨棄試驗資源？ 您可能會想：
   
   「如果我們可以規劃在試驗結束時擲回虛擬機器和實驗室，則可以為試驗設定單一訂用帳戶，並在其中執行所有工作，同時解決大規模推出問題。」 

一般的趨勢是讓試驗成為「完美的」，讓它完全代表在公司推出服務之後，最終狀態將會是什麼。 這是誤假設。 越接近「完美」，您就必須在開始試驗 *之前* 完成更多的準備工作。 試驗的目的是要在擴充和推出最終服務方面做出適當的決策。 

試驗的重點應該是挑選最少的必要工作負載和相依性，以回答 Azure DevTest Labs 是否為您企業的正確服務的相關問題。 建議您選擇具有最少相依性的最簡單工作負載，以協助確保快速且乾淨的成功。 如果無法這麼做，請挑選最具代表性的工作負載，以公開潛在的複雜性，讓試驗階段中的成功可以在相應放大階段中複寫。 

下列範例示範完整的概念證明。 

## <a name="example-proof-of-concept-plan"></a>範例：概念證明方案 

本節顯示的範例可用於界定 DevTest Labs 試驗的概念證明。 

> [!TIP]
> 為了將設定專案失敗的可能性降至最低，我們強烈建議您不要略過本節所述的範例。 

### <a name="overview"></a>概觀 

我們打算根據 DevTest Labs 在 Azure 中開發新環境，以供廠商用來作為來自公司網路的獨立環境。 為了判斷解決方案是否符合需求，我們會開發概念證明來驗證端對端解決方案。 我們已包含數個廠商來試用並驗證體驗。 

### <a name="outcomes"></a>結果 

建立概念證明時，我們會先著重在結果 (我們嘗試達成) 的結果。 在概念證明結束時，我們預期： 

* 適用于廠商的工作端對端解決方案，可在 Azure Active Directory (Azure AD) 中使用來賓帳戶來存取 Azure 中的隔離環境。 環境具有其生產力所需的資源。 
* 任何會影響更廣泛規模使用和採用的潛在封鎖問題，都是列舉和理解。
* 參與開發概念證明的人員對所有程式碼都有充分的瞭解。 它們也會瞭解牽涉到的相關附屬客戶，並且可信賴于更廣泛的採用。

### <a name="open-questions-and-prerequisites"></a>開啟問題和必要條件 

* 我們是否建立了可用於此專案的訂用帳戶？ 
* 我們是否有 Azure AD 租使用者和 Azure AD 全域管理員識別誰可以提供關於 Azure AD 相關問題的說明和指引？ 
* 我們是否有地點可以共同作業專案的個人？ 

   * 原始程式碼和腳本 (例如 Azure Repos)  
   * 檔 (例如 Microsoft 小組或 SharePoint)   
   * 交談 (像是 Microsoft 小組)  
   * 工作專案 (例如 Azure Boards)  
* 廠商需要哪些資源？ 這包括網路上的可用應用程式，兩者都在虛擬機器本機和其他必要的伺服器上。 
* 虛擬機器將加入 Azure 中的網域嗎？ 如果是，這會是 Azure Active Directory Domain Services (Azure AD DS) 還是其他專案？ 
* 我們是否已找出將作為概念證明目標的小組或廠商？ 誰將是環境的客戶？
* 我們將使用哪一個 Azure 區域進行概念證明？ 
* 除了 IaaS (Vm) 之外，是否有廠商可透過 DevTest Labs 使用的服務清單？ 
* 我們打算如何使用實驗室訓練廠商/使用者？ 

### <a name="components-of-the-proof-of-concept-solution"></a>概念證明解決方案的元件 

我們預期解決方案具有下列元件： 

* 各廠商團隊將在 Azure 中使用一組實驗室。
* 實驗室會連線至支援需求的網路基礎結構。
* 廠商可透過 Azure AD 和角色指派來存取實驗室。
* 廠商可以成功連接到其資源。 具體而言，站對站 VPN 可讓您直接存取不具公用 IP 位址的虛擬機器。
* 一組構件涵蓋廠商在虛擬機器上所需的必要軟體。

## <a name="additional-planning-and-design-decisions"></a>其他規劃與設計決策 

在您發行完整的 DevTest Labs 解決方案之前，您必須先進行一些重要的規劃和設計決策。 使用概念證明的體驗可協助您做出這些決策。 進一步考慮包括： 

* **訂**用帳戶拓撲： Azure 中的資源企業層級需求可延伸至 [單一訂用帳戶內的可用配額](../azure-resource-manager/management/azure-subscription-service-limits.md)之外。 這需要有多個 Azure 訂用帳戶和/或服務要求，才能增加初始訂用帳戶限制。 務必事先決定如何跨訂用帳戶分配資源。 有一項重要的資源是訂用帳戶 [決策指南](/azure/architecture/cloud-adoption/decision-guides/subscriptions/) ，因為很難稍後將資源移至另一個訂用帳戶。 例如，實驗室無法在建立之後移至另一個訂用帳戶。  
* **網路拓撲**： DevTest Labs 自動建立的 [預設網路基礎結構](../app-service/networking-features.md) ，可能不足以符合企業使用者的需求和限制。 您通常會看到 [Azure ExpressRoute 連線的虛擬網路](/azure/architecture/reference-architectures/hybrid-networking/)、 [中樞和輪輻](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) 可供跨訂用帳戶連線，甚至是 [強制路由](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) ，以確保僅限內部部署連線能力。 當您在實驗室中建立新的虛擬機器時，DevTest Labs 可讓現有的虛擬網路連線至實驗室，以啟用其使用方式。 
* **虛擬機器的遠端存取**：有許多選項可從遠端存取位於 DevTest Labs 中的虛擬機器。 最簡單的方式是使用公用 Ip 或共用的公用 Ip。 這些是 [實驗室中可用的設定](devtest-lab-shared-ip.md)。 如果這些選項不足夠，也可以選擇使用遠端存取閘道。 此選項會顯示在 [DevTest labs 企業參考架構](devtest-lab-reference-architecture.md) 中，並在 [DevTest Labs 遠端桌面閘道檔](configure-lab-remote-desktop-gateway.md)中進一步說明。 企業也可以使用 ExpressRoute 或站對站 VPN，將其實驗室連線到其內部部署網路。 此選項會根據虛擬機器的私人 IP 位址，啟用對虛擬機器的直接遠端桌面或 SSH 連線，而不會暴露于網際網路。 
* **處理許可權**： DevTest Labs 中經常使用的兩個主要許可權是 [擁有者和實驗室使用者](devtest-lab-add-devtest-user.md)。 在推出 DevTest Labs 之前，請務必先決定是否要將受實驗室中的每個存取層級鎖定。 常見的模型是 (小組負責人的預算擁有者，例如) 作為實驗室擁有者，以及做為實驗室使用者的團隊成員。 此模型可讓 (小組負責人的人員) 負責調整原則設定，並讓小組保持在預算內。  

## <a name="completing-the-proof-of-concept"></a>完成概念證明 

在涵蓋預期的學習之後，就可以完成試驗。 這是向使用者收集意見反應的時間、判斷試驗是否成功，以及決定組織是否會在企業中的 DevTest Labs 規模擴大推出時繼續進行。 這也是一個很好的時機，可考慮將 DevTest Labs 和相關聯資源的部署自動化，以確保整個擴展推出的一致性。 

## <a name="next-steps"></a>接下來的步驟 

* [DevTest Labs 企業檔](devtest-lab-guidance-prescriptive-adoption.md)
* [企業的參考架構](devtest-lab-reference-architecture.md)
* [擴大您的 DevTest Labs 部署](devtest-lab-guidance-orchestrate-implementation.md)
* [協調 Azure DevTest Labs 的實作](devtest-lab-guidance-orchestrate-implementation.md)
