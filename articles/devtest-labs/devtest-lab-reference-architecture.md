---
title: Azure DevTest Labs 的企業參考架構
description: 本文提供企業內 Azure DevTest Labs 的參考架構指引。
ms.topic: article
ms.date: 06/26/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 29f739c2fb9dd1cc58bf6c400eeee1bebb6243c2
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92328839"
---
# <a name="azure-devtest-labs-reference-architecture-for-enterprises"></a>Azure DevTest Labs 企業的參考架構
本文提供的參考架構可協助您根據企業中的 Azure DevTest Labs 來部署解決方案。 其中包括下列各項：
- 透過 Azure ExpressRoute 的內部部署連線能力
- 遠端桌面閘道，可遠端登入虛擬機器
- 私用成品的構件存放庫連接
- 實驗室中使用的其他 PaaS 服務

![參考架構圖表](./media/devtest-lab-reference-architecture/reference-architecture.png)

## <a name="architecture"></a>架構
以下是參考架構的重要元素：

- **Azure Active Directory (Azure AD) **： DevTest Labs 使用 [Azure AD 服務進行身分識別管理](../active-directory/fundamentals/active-directory-whatis.md)。 當您授與使用者以 DevTest Labs 為基礎之環境的存取權時，請考慮下列兩個重要層面：
    - **資源管理**：提供 Azure 入口網站的存取權，以管理資源 (建立虛擬機器;建立環境;啟動、停止、重新開機、刪除和套用構件;) 。 您可以使用 Azure 角色型存取控制 (Azure RBAC) 來進行資源管理。 您將角色指派給使用者，並設定資源和存取層級許可權。
    - **虛擬機器 (網路層級) **：在預設設定中，虛擬機器使用本機系統管理員帳戶。 如果有可用的網域 ([Azure AD Domain Services](../active-directory-domain-services/overview.md)、內部部署網域或雲端架構網域) ，則可以將電腦加入網域。 然後，使用者可以使用其網域身分識別來連線至 Vm。
- **內部部署連線能力**：在我們的架構圖中，會使用 [ExpressRoute](../expressroute/expressroute-introduction.md) 。 但是您也可以使用 [站對站 VPN](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md)。 雖然 DevTest Labs 不需要 ExpressRoute，但它通常會在企業中使用。 只有當您需要存取公司資源時，才需要 ExpressRoute。 常見案例包括：
    - 您有無法移至雲端的內部部署資料。
    - 您偏好將實驗室的虛擬機器加入內部部署網域。
    - 您想要透過內部部署防火牆，在安全性/合規性中強制進出雲端環境的所有網路流量。
- **網路安全性群組**：根據來源和目的地 IP 位址，將流量限制在雲端環境 (或雲端環境) 的常見方式是使用 [網路安全性群組](../virtual-network/network-security-groups-overview.md)。 例如，您只想要允許源自公司網路的流量進入實驗室的網路。
- **遠端桌面閘道**：企業通常會封鎖公司防火牆上的連出遠端桌面連線。 有幾個選項可讓您在 DevTest Labs 中連線到雲端式環境，包括：
  - 使用 [遠端桌面閘道](/windows-server/remote/remote-desktop-services/desktop-hosting-logical-architecture)，並允許閘道負載平衡器的靜態 IP 位址。
  - 透過 ExpressRoute/站對站 VPN 連線將[所有連入的 RDP 流量](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md)導向。 這項功能是企業規劃 DevTest Labs 部署的一般考慮。
- **網路服務 (虛擬網路、子網) **： [Azure 網路](../networking/networking-overview.md) 拓撲是 DevTest Labs 架構中的另一個重要元素。 它會控制來自實驗室的資源是否可以進行通訊，並可存取內部部署和網際網路。 我們的架構圖包含客戶使用 DevTest Labs 的最常見方式：所有實驗室都會使用[中樞輪輻模型](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)，透過[虛擬網路對等互連](../virtual-network/virtual-network-peering-overview.md)來連線到內部部署的 ExpressRoute/站對站 VPN 連線。 但 DevTest Labs 會直接使用 Azure 虛擬網路，因此您設定網路基礎結構的方式並沒有任何限制。
- **DevTest labs**： DevTest labs 是整體架構的重要部分。 若要深入瞭解此服務，請參閱 [關於 DevTest Labs](devtest-lab-overview.md)。
- **虛擬機器和其他資源 (SaaS、PaaS、IaaS) **：虛擬機器是 DevTest Labs 支援的重要工作負載，以及其他 Azure 資源。 DevTest Labs 可讓企業輕鬆快速地提供 Azure 資源的存取權， (包括 Vm 和其他 Azure 資源) 。 深入瞭解適用于 [開發人員](devtest-lab-developer-lab.md) 和 [測試](devtest-lab-test-env.md)人員的 Azure 存取權。

## <a name="scalability-considerations"></a>延展性考量
雖然 DevTest Labs 沒有內建配額或限制，但在一般的實驗室作業中使用的其他 Azure 資源都有訂用帳戶 [層級配額](../azure-resource-manager/management/azure-subscription-service-limits.md)。 因此，在一般的企業部署中，您需要多個 Azure 訂用帳戶來涵蓋大型部署的 DevTest Labs。 企業最常接觸的配額如下：

- **資源群組**：在預設設定中，DevTest Labs 會為每個新的虛擬機器建立資源群組，或使用者使用服務建立環境。 訂用帳戶 [最多可以包含980個資源群組](../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits)。 因此，這是訂用帳戶中的虛擬機器和環境限制。 另外還有兩個您應該考慮的設定：
    - **[所有虛擬機器都會移至相同的資源群組](resource-group-control.md)**：雖然此設定可協助您符合資源群組的限制，但它會影響資源類型的每個資源群組限制。
    - **使用共用的公用 ip**：相同大小和區域的所有 vm 都會進入相同的資源群組。 如果允許虛擬機器具有公用 IP 位址，則這項設定會在資源群組配額與資源類型的每個資源群組配額之間進行「中間」。
- 每個資源類型的資源**群組**：每個資源類型每個資源群組的資源預設限制[為 800](../azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits)。  當您使用 *所有 vm 移至相同的資源群組* 設定時，使用者會更快達到此訂用帳戶的限制，尤其是當 vm 有許多額外的磁片時。
- **儲存體帳戶**： DevTest Labs 中的實驗室隨附儲存體帳戶。 每 [個訂用帳戶每個區域的儲存體帳戶數目](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits)的 Azure 配額為250。 相同區域中的 DevTest Labs 數目上限也是250。
- **角色**指派：角色指派是讓使用者或主體存取資源 (擁有者、資源、許可權等級) 的方式。 在 Azure 中， [每個訂用帳戶的角色指派限制為 2000](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-role-based-access-control-limits)。 根據預設，DevTest Labs 服務會為每個 VM 建立資源群組。 擁有者會被授與 DevTest Labs VM 的 *擁有* 者許可權，以及資源群組的 *讀取* 者許可權。 如此一來，您所建立的每個新 VM 都會使用兩個角色指派，以及當您授與使用者實驗室的許可權時所使用的指派。
- **API 讀取/寫入**：有各種方式可自動化 Azure 和 DevTest Labs，包括 REST Api、PowerShell、Azure CLI 和 Azure SDK。 透過自動化，您可能會達到 API 要求的另一個限制：每個訂用帳戶最多允許 [每小時12000個讀取要求和1200個寫入要求](../azure-resource-manager/management/request-limits-and-throttling.md)。 當您將 DevTest Labs 自動化時，請注意這項限制。

## <a name="manageability-considerations"></a>管理性考量
DevTest Labs 有絕佳的系統管理使用者介面，可搭配單一實驗室使用。 但在企業中，您可能有多個 Azure 訂用帳戶和許多實驗室。 以一致的方式進行所有實驗室的變更都需要腳本/自動化。 以下是 DevTest Labs 部署的一些範例和最佳管理作法：

- **實驗室設定的變更**：常見的案例是在部署中的所有實驗室更新特定的實驗室設定。 例如，有新的 VM 實例大小可用，而且所有實驗室都必須更新為允許它。 最佳做法是使用 PowerShell 腳本、CLI 或 REST Api 來自動化這些變更。  
- 構件存放**庫個人存取權杖**：通常，Git 存放庫的個人存取權杖會在90天、一年或兩年內到期。 為了確保持續性，請務必擴充個人存取權杖或建立新的權杖。 然後使用 automation 將新的個人存取權杖套用至所有實驗室。
- **限制實驗室設定的變更**：通常必須限制特定的設定 (例如允許使用) 的 marketplace 映射。 您可以使用 Azure 原則來防止變更資源類型。 或者，您可以建立自訂角色，並為使用者授與該角色，而不是實驗室的 *擁有* 者角色。 您可以在實驗室中進行大部分的設定， (內部支援、實驗室公告、允許的 VM 大小等等) 。
- **需要 vm 遵循命名慣例**：管理員通常會想要輕鬆地識別屬於雲端式開發和測試環境一部分的 vm。 您可以使用 [Azure 原則](https://github.com/Azure/azure-policy/tree/master/samples/TextPatterns/allow-multiple-name-patterns)來完成這項作業。

請務必注意，DevTest Labs 會使用以相同方式管理的基礎 Azure 資源：網路、磁片、計算等等。 例如，Azure 原則適用于在實驗室中建立的虛擬機器。 Azure 資訊安全中心可以報告 VM 相容性。 而 Azure 備份服務可以針對實驗室中的 Vm 提供定期備份。

## <a name="security-considerations"></a>安全性考量
Azure DevTest Labs 使用 Azure 中現有的資源 (計算、網路等) 。 因此，它會自動受益于平臺內建的安全性功能。 例如，若要要求連入的遠端桌面連線只源自公司網路，只要將網路安全性群組新增至遠端桌面閘道上的虛擬網路即可。 唯一的額外安全性考慮是您授與每日使用實驗室之小組成員的許可權層級。 最常見的許可權是 [*擁有* 者和 *使用者*](devtest-lab-add-devtest-user.md)。 如需這些角色的詳細資訊，請參閱 [在 Azure DevTest Labs 中新增擁有者和使用者](devtest-lab-add-devtest-user.md)。

## <a name="next-steps"></a>後續步驟
請參閱本系列的下一篇文章： [擴大您的 Azure DevTest Labs 基礎結構](devtest-lab-guidance-scale.md)。