---
title: Azure 開發人員測試實驗室的企業參考體系結構
description: 本文為企業中的 Azure 開發人員測試實驗室提供了參考體系結構指南。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 77e6ab588f74c8b810f211e069c1c24043155111
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132844"
---
# <a name="azure-devtest-labs-reference-architecture-for-enterprises"></a>面向企業的 Azure 開發人員測試實驗室參考體系結構
本文提供了參考體系結構，以説明您在企業中部署基於 Azure 開發人員測試實驗室的解決方案。 它包括以下內容：
- 通過 Azure 快速路由進行本地連接
- 遠端桌面閘道，可遠端登入到虛擬機器
- 連接到私有專案的專案存儲庫
- 實驗室中使用的其他 PaaS 服務

![參考體系結構圖](./media/devtest-lab-reference-architecture/reference-architecture.png)

## <a name="architecture"></a>架構
這些是參考體系結構的關鍵元素：

- **Azure 活動目錄 （Azure AD）：** 開發人員測試實驗室使用[Azure AD 服務進行標識管理](../active-directory/fundamentals/active-directory-whatis.md)。 在授予使用者基於 DevTest Labs 的環境存取權限時，請考慮以下兩個關鍵方面：
    - **資源管理**：它提供對 Azure 門戶的訪問來管理資源（創建虛擬機器;創建環境;啟動、停止、重新開機、刪除和應用專案;等等）。 使用基於角色的存取控制 （RBAC） 在 Azure 中執行資源管理。 將角色指派給使用者並設置資源和存取層級許可權。
    - **虛擬機器（網路級）：** 在預設配置中，虛擬機器使用本地管理員帳戶。 如果存在可用的域[（Azure AD 域服務](../active-directory-domain-services/overview.md)、本地域或基於雲的域），則可以將電腦加入域。 然後，使用者可以使用其基於域的標識連接到 VM。
- **本地連接**：在我們的體系結構圖中，使用[ExpressRoute。](../expressroute/expressroute-introduction.md) 但是您也可以使用[網站到網站 VPN](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md)。 儘管 DevTest 實驗室不需要 ExpressRoute，但它在企業中很常見。 僅當您需要訪問公司資源時，才需要 ExpressRoute。 常見方案包括：
    - 您有無法移動到雲的本地資料。
    - 您希望將實驗室的虛擬機器加入本地域。
    - 您希望通過本地防火牆強制所有網路流量進出雲環境，以確保安全/合規性。
- **網路安全性群組**：根據源和目標 IP 位址限制流量到雲環境（或雲環境中）的一種常見方法是使用[網路安全性群組](../virtual-network/security-overview.md)。 例如，您希望僅允許來自公司網路的流量進入實驗室的網路。
- **遠端桌面閘道**：企業通常會在公司防火牆上阻止傳出遠端桌面連線。 在 DevTest 實驗室中，有幾個選項可用於實現與基於雲的環境的連接，包括：
  - 使用[遠端桌面閘道](/windows-server/remote/remote-desktop-services/desktop-hosting-logical-architecture)，並允許閘道負載等化器的靜態 IP 位址。
  - 通過 ExpressRoute/網站到網站 VPN 連接[引導所有傳入的 RDP 流量](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md)。 當企業規劃 DevTest Labs 部署時，此功能是常見的考慮因素。
- **網路服務（虛擬網路、子網）：Azure**[網路](../networking/networking-overview.md)拓撲是 DevTest Labs 體系結構中的另一個關鍵元素。 它控制實驗室的資源是否可以通信和訪問本地和互聯網。 我們的體系結構圖包括客戶使用 DevTest Labs 的最常見方式：所有實驗室通過使用[中心輻條模型](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)連接到到本地的 ExpressRoute/網站到網站 VPN 連接，通過[虛擬網路對等互連](../virtual-network/virtual-network-peering-overview.md)進行連接。 但是 DevTest Labs 直接使用 Azure 虛擬網路，因此對如何設置網路基礎結構沒有限制。
- **開發人員測試實驗室**：開發人員測試實驗室是整個體系結構的關鍵區段。 要瞭解有關該服務的更多內容，請參閱[有關開發人員測試實驗室](devtest-lab-overview.md)。
- **虛擬機器和其他資源（SaaS、PaaS、IaaS）：** 虛擬機器是 DevTest Labs 與其他 Azure 資源一起支援的關鍵工作負載。 開發人員測試實驗室使企業能夠輕鬆快速地提供對 Azure 資源（包括 VM 和其他 Azure 資源）的訪問。 詳細瞭解[開發人員](devtest-lab-developer-lab.md)和[測試人員](devtest-lab-test-env.md)訪問 Azure。

## <a name="scalability-considerations"></a>延展性考量
儘管 DevTest Labs 沒有內置配額或限制，但在實驗室的典型操作中使用的其他 Azure 資源確實具有[訂閱級配額](../azure-resource-manager/management/azure-subscription-service-limits.md)。 因此，在典型的企業部署中，需要多個 Azure 訂閱來覆蓋 DevTest Labs 的大規模部署。 企業最常達到的配額包括：

- **資源組**：在預設配置中，DevTest Labs 為每個新虛擬機器創建一個資源組，或者使用者使用該服務創建環境。 訂閱最多可以包含[980 個資源組](../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits)。 因此，這是訂閱中虛擬機器和環境的限制。 還有另外兩個配置需要考慮：
    - **[所有虛擬機器都轉到同一資源組](resource-group-control.md)**：儘管此設置可説明您滿足資源組限制，但它會影響資源類型/資源組限制。
    - **使用共用公共 IP：** 相同大小和區域的所有 VM 都進入同一資源組。 如果允許虛擬機器具有公共 IP 位址，則此配置是資源組配額和資源類型/資源組配額之間的"中間地帶"。
- **每個資源類型的資源組**：[每個資源組每個資源類型的資源的預設限制為 800](../azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits)。  當您使用所有*VM 轉到同一資源組*配置時，使用者會更快地達到此訂閱限制，尤其是在 VM 具有許多額外磁片的情況下。
- **存儲帳戶**：DevTest 實驗室中的實驗室附帶存儲帳戶。 [每個訂閱每個區域的存儲帳戶數](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits)的 Azure 配額為 250 。 同一區域中的 DevTest 實驗室的最大數量也是 250。
- **角色指派**：角色指派是授予使用者或主體對資源（擁有者、資源、權限等級）的存取權限的方式。 在 Azure 中，[每個訂閱有 2，000 個角色指派的限制](../azure-resource-manager/management/azure-subscription-service-limits.md#role-based-access-control-limits)。 預設情況下，DevTest Labs 服務為每個 VM 創建一個資源組。 擁有者被授予開發人員測試實驗室 VM*的擁有者*許可權，向資源組授予*讀取器*許可權。 這樣，您創建的每個新 VM 除了在授予使用者實驗室許可權時使用分配外，還使用兩個角色指派。
- **API 讀取/寫入**：自動執行 Azure 和開發人員測試實驗室的方法有多種，包括 REST API、PowerShell、Azure CLI 和 Azure SDK。 通過自動化，您可能會對 API 請求達到另一個限制：每個訂閱允許每小時最多[12，000 個讀取請求和 1，200 個寫入請求](../azure-resource-manager/management/request-limits-and-throttling.md)。 在自動執行 DevTest 實驗室時，請注意此限制。

## <a name="manageability-considerations"></a>管理性考量
DevTest Labs 具有出色的管理使用者介面，可用於使用單個實驗室。 但在企業中，您可能有多個 Azure 訂閱和許多實驗室。 對所有實驗室進行一致的更改需要腳本/自動化。 以下是 DevTest Labs 部署的一些示例和最佳管理實踐：

- **對實驗室設置的更改**：常見方案是更新部署中所有實驗室中的特定實驗室設置。 例如，新的 VM 實例大小可用，並且必須更新所有實驗室以允許它。 最好使用 PowerShell 腳本、CLI 或 REST API 自動執行這些更改。  
- **專案存儲庫個人訪問權杖**：通常，Git 存儲庫的個人訪問權杖將在 90 天、一年或兩年後過期。 為了確保連續性，擴展個人訪問權杖或創建新訪問權杖非常重要。 然後使用自動化將新的個人訪問權杖應用於所有實驗室。
- **限制對實驗室設置的更改**：通常，必須限制特定設置（例如允許使用市場映射）。 可以使用 Azure 策略來防止對資源類型的更改。 或者，您可以創建自訂角色，並授予使用者該角色，而不是實驗室*的擁有者*角色。 您可以針對實驗室中的大多數設置（內部支援、實驗室公告、允許的 VM 大小等）執行此操作。
- **要求 VM 遵循命名約定**：經理通常希望輕鬆識別屬於基於雲的開發和測試環境的 VM。 可以使用[Azure 策略](https://github.com/Azure/azure-policy/tree/master/samples/TextPatterns/allow-multiple-name-patterns)執行此操作。

請務必注意，DevTest Labs 使用以相同方式管理的基礎 Azure 資源：網路、磁片、計算等。 例如，Azure 策略適用于在實驗室中創建的虛擬機器。 Azure 安全中心可以報告 VM 合規性。 Azure 備份服務可以為實驗室中的 VM 提供定期備份。

## <a name="security-considerations"></a>安全性考量
Azure 開發人員測試實驗室使用 Azure 中的現有資源（計算、網路等）。 因此，它會自動受益于平臺中內置的安全功能。 例如，要要求傳入的遠端桌面連線僅來自公司網路，只需將網路安全性群組添加到遠端桌面閘道上的虛擬網路即可。 唯一額外的安全考慮是您授予每天使用實驗室的團隊成員的權限等級。 最常見的許可權是[*擁有者*和*使用者*](devtest-lab-add-devtest-user.md)。 有關這些角色的詳細資訊，請參閱在[Azure 開發人員測試實驗室中添加擁有者和使用者](devtest-lab-add-devtest-user.md)。

## <a name="next-steps"></a>後續步驟
請參閱本系列的下一篇文章：[向上擴展 Azure 開發人員測試實驗室基礎結構](devtest-lab-guidance-scale.md)。
