---
title: 適用於雲端解決方案提供者的 ExpressRoute - Azure | Microsoft Docs
description: 本文提供的資訊適用於想要將 Azure 服務和 ExpressRoute 併入其供應項目的雲端解決方案提供者。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 10/10/2016
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 31660d13fe69d393a19167eaea8a69bfb6260467
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/19/2020
ms.locfileid: "92202067"
---
# <a name="expressroute-for-cloud-solution-providers-csp"></a>適用於雲端解決方案提供者 (CSP) 的 ExpressRoute
Microsoft 為傳統的轉銷商和經銷商 (CSP) 提供超大規模的服務，以便為您的客戶快速佈建新的服務和解決方案，而不需要投資開發這些新服務。 若要讓雲端解決方案提供者 (CSP) 能夠直接管理這些新服務，Microsoft 提供了一些程式和 API，讓 CSP 可以代表您的客戶管理 Microsoft Azure 資源。 其中一個資源是 ExpressRoute。 ExpressRoute 可讓 CSP 將現有的客戶資源連接到 Azure 服務。 ExpressRoute 是 Azure 中服務的高速私人通訊連結。 

ExpressRoute 是由一對適用于高可用性的線路所組成，其會附加至單一客戶的訂用帳戶 () ，且不能由多個客戶共用。 每個電路應在不同的路由器中終止，以維持高可用性。

> [!NOTE]
> 每個 ExpressRoute 線路上的頻寬和連線數目可能有所限制。 如果單一客戶的需求超過這些限制，則需要多個 ExpressRoute 線路以進行混合式網路的執行。
> 
> 

Microsoft Azure 提供越來越多的服務，您可以將這些服務提供給您的客戶。 ExpressRoute 可提供 Microsoft Azure 環境的高速低延遲存取，協助您和您的客戶利用這些服務。

## <a name="microsoft-azure-management"></a>Microsoft Azure 管理
Microsoft 提供 Csp Api 來管理 Azure 客戶訂用帳戶，其方式是允許以程式設計方式與您自己的服務管理系統整合。 [這裡](/previous-versions/windows/mt844538(v=win.10))可以找到支援的管理功能。

## <a name="microsoft-azure-resource-management"></a>Microsoft Azure 資源管理
您與客戶簽訂的合約將會決定訂用帳戶的管理方式。 CSP 可以直接管理資源的建立和維護，或客戶可以持續控制 Microsoft Azure 訂用帳戶並建立他們所需的 Azure 資源。 如果您的客戶在其 Microsoft Azure 訂用帳戶中管理資源的建立，則會使用兩個模型的其中一種：「連線*到*」模型或「*直接到*」模型。 下列各節會詳細說明這些模型。  

### <a name="connect-through-model"></a>Connect-Through 模型
![顯示「連接到」模型的圖表。](./media/expressroute-for-cloud-solution-providers/connect-through.png)  

在 Connect-Through 模型中，CSP 會在您的資料中心與您客戶的 Azure 訂用帳戶之間建立直接連線。 使用 ExpressRoute 進行直接連線，連接您的網路與 Azure。 然後您的客戶會連接到您的網路。 此案例需要客戶通過 CSP 網路來存取 Azure 服務。 

如果您的客戶有其他未由您管理的 Azure 訂用帳戶，則會使用公用網際網路或自己的私人連線來連線到在非 CSP 訂用帳戶下布建的服務。 

針對管理 Azure 服務的 CSP，假設 CSP 有先前建立的客戶身分識別存放區，接著會將其複寫到 Azure Active Directory，以透過代理者 (AOBO) 來管理其 CSP 訂用帳戶。 此案例的關鍵驅動因素包括：指定的夥伴或服務提供者與客戶建立關聯性、客戶目前使用提供者服務，或夥伴想要提供提供者裝載和 Azure 託管解決方案的組合，以提供彈性並解決 CSP 本身無法滿足的客戶挑戰。 下 **圖** 說明此模型。

![顯示「連接到」模型之詳細案例的圖表。](./media/expressroute-for-cloud-solution-providers/connect-through-model.png)

### <a name="connect-to-model"></a>Connect-To 模型
![顯示「連接到」模型的圖表。](./media/expressroute-for-cloud-solution-providers/connect-to.png)

在 Connect-To 模型中，服務提供者會使用 ExpressRoute 透過客戶的 (客戶) 網路，在其客戶的資料中心與 CSP 佈建的 Azure 訂用帳戶之間建立直接連線。

> [!NOTE]
> 在 ExpressRoute 中，客戶需要建立和維護 ExpressRoute 電路。  
> 
> 

此連線案例要求客戶直接透過客戶網路連接，以存取受 CSP 管理的 Azure 訂用帳戶，使用建立、擁有及管理的直接網路連線，無論是由客戶完全或部分管理。 針對這些客戶，假設提供者目前未建立客戶身分識別存放區，而且提供者會協助客戶將其目前的身分識別存放區複寫到 Azure Active Directory，以透過 AOBO 管理其訂用帳戶。 此案例的關鍵驅動因素包括特定的夥伴或服務提供者已建立起與客戶的關聯性、客戶目前使用提供者服務，或夥伴想要提供單獨以 Azure 裝載的解決方案為基礎的服務，而不需要現有的提供者資料中心或基礎結構。

![顯示「連接到」模型詳細案例的圖表。](./media/expressroute-for-cloud-solution-providers/connect-to-model.png)

這兩個選項之間的選擇，取決於您客戶的需求，以及您目前提供 Azure 服務的需求。 下列連結涵蓋這些模型的詳細資料，以及相關聯的角色型存取控制、網路和身分識別設計模式︰

* **Azure 以角色為基礎的存取控制 (AZURE RBAC) ** – RBAC 是以 Azure Active Directory 為基礎。  如需有關 Azure RBAC 的詳細資訊，請參閱 [這裡](../role-based-access-control/role-assignments-portal.md)。
* **網路** – 涵蓋 Microsoft Azure 中的各種網路主題。
* **Azure Active Directory (Azure AD) ** – Azure AD 提供 Microsoft Azure 和協力廠商 SaaS 應用程式的身分識別管理。 如需 Azure AD 的詳細資訊，請參閱 [這裡](https://azure.microsoft.com/documentation/services/active-directory/)。  

## <a name="network-speeds"></a>網路速度
ExpressRoute 支援從 50 Mb/s 到 10 Gb/s 的網路速度。 這可讓客戶購買其獨特環境所需的網路頻寬量。

> [!NOTE]
> 您可以在不干擾通訊的情況下，視需要增加網路頻寬，但若要降低網路速度，則需拆解電路並以較低的網路速度加以重建。  
> 
> 

ExpressRoute 支援多個 vNet 至單一 ExpressRoute 電路的連線，以便更加妥善利用高速連線。 同一個客戶所擁有的多個 Azure 訂用帳戶可以共用單一 ExpressRoute 電路。

## <a name="configuring-expressroute"></a>設定 ExpressRoute
ExpressRoute 可設定為透過單一 ExpressRoute 電路支援三種類型的流量 ([路由網域](#expressroute-routing-domains))。 此流量會隔離到私人對等互連、Microsoft 對等互連，以及 (淘汰) 的公用對等互連。 視 ExpressRoute 電路的大小和您的客戶所需的隔離而定，您可以選擇透過單一 ExpressRoute 電路傳送一個或所有類型的流量，或使用多個 ExpressRoute 電路。 您的客戶的安全狀態可能不允許公用流量和私用流量透過相同的電路周遊。

### <a name="connect-through-model"></a>Connect-Through 模型
在連線設定中，您將負責將客戶的資料中心資源連接到裝載于 Azure 中的訂用帳戶的所有網路結構性支援。 每個想要使用 Azure 功能的客戶都需要自己的 ExpressRoute 連線，這會由您管理。 您將使用客戶用來購買 ExpressRoute 線路的相同方法。 您將遵循《 [ExpressRoute](expressroute-workflows.md) workflow for 線路布建和線路狀態」一文所述的相同步驟。 接著，您將設定邊界閘道協定 (BGP) 路由，以控制在內部部署網路與 Azure vNet 之間流動的流量。

### <a name="connect-to-model"></a>Connect-To 模型
在 [連線到設定] 中，您的客戶已經有 Azure 的現有連線，或會起始與網際網路服務提供者的連線，將 ExpressRoute 從自己的資料中心直接連結到 Azure，而不是您的資料中心。 若要開始佈建程序，您的客戶將依照上面 Connect-Through 模型中所述的步驟執行。 一旦建立線路之後，您的客戶就必須設定內部部署路由器，以存取您的網路和 Azure Vnet。

您可以設定連接及設定路由，允許您資料中心的資源與您資料中心的用戶端資源進行通訊，或與 Azure 中裝載的資源進行通訊。

## <a name="expressroute-routing-domains"></a>ExpressRoute 路由網域
ExpressRoute 提供兩個路由網域給新線路：私用對等互連和 Microsoft 對等互連。 在主動-主動設定中，每個路由網域都會設定為具有相同的路由器，以提供高可用性。 如需 ExpressRoute 路由網域的詳細資訊，請參閱 [這裡](expressroute-circuit-peerings.md)。

您可以定義自訂路由篩選器，只允許您想允許或需要的路由。 如需詳細資訊或了解如何進行這些變更，請參閱以下文章︰ [使用 PowerShell 建立和修改 ExpressRoute 電路的路由](expressroute-howto-routing-classic.md) ，以取得路由篩選器的詳細資訊。

> [!NOTE]
> 針對 Microsoft 對等互連，連線必須是客戶或 CSP 所擁有的公用 IP 位址，且必須遵守所有已定義的規則。 如需詳細資訊，請參閱 [ExpressRoute 必要條件](expressroute-prerequisites.md) 頁面。  
> 
> 

## <a name="routing"></a>路由
ExpressRoute 會透過 Azure 虛擬網路閘道連接到 Azure 網路。 網路閘道提供 Azure 虛擬網路的路由。

建立 Azure 虛擬網路時，也會建立 vNet 的預設路由表，以便從 vNet 的子網路雙向導引流量。 如果預設路由表對解決方案而言不足，則可以建立自訂路由，將連出流量路由傳送至自訂設備，或封鎖特定子網或外部網路的路由。

### <a name="default-routing"></a>預設路由
預設路由表包含下列路由：

* 子網路內的路由
* 虛擬網路內的子網路對子網路路由
* 對網際網路的路由
* 使用 VPN 閘道的虛擬網路對虛擬網路路由
* 使用 VPN 或 ExpressRoute 閘道的虛擬網路對內部部署網路路由

![顯示預設路由選項的圖表。](./media/expressroute-for-cloud-solution-providers/default-routing.png)  

### <a name="user-defined-routing-udr"></a>使用者定義的路由 (UDR)
使用者定義的路由能夠控制從虛擬網路中指派的子網路輸出至其他子網路的流量，或透過其中一個其他預先定義的閘道 (ExpressRoute、網際網路或 VPN) 輸出的流量。 使用者定義的路由表可以取代預設的系統路由表，其以自訂路由取代預設的路由表。 利用使用者定義的路由，客戶可以建立對應用裝置 (例如防火牆或入侵偵測應用裝置) 的特定路由，或封鎖從裝載使用者定義之路由的子網路對特定子網路的存取。 如需 User-Defined 路由的總覽，請參閱 [這裡](../virtual-network/virtual-networks-udr-overview.md)。 

## <a name="security"></a>安全性
視正在使用中的模型 (Connect-To 或 Connect-Through) 而定，您的客戶會在其 vNet 中定義安全性原則，或提供安全性原則需求給 CSP 來定義其 vNet。 可以定義下列安全性準則︰

1. **客戶隔離** — Azure 平台會將客戶識別碼和 vNet 資訊儲存在安全的資料庫中 (用來封裝 GRE 通道中每個客戶的流量)，藉此提供客戶隔離。
2. **網路安全性群組 (NSG)** 規則用於定義在 Azure 中傳入和傳出 vNet 子網路的流量。 根據預設，NSG 包含封鎖規則以封鎖從網際網路到 vNet 的流量，並允許 vNet 內的流量規則。 如需網路安全性群組的詳細資訊，請參閱 [這裡](https://azure.microsoft.com/blog/network-security-groups/)。
3. **強制通道** — 這個選項可透過 ExpressRoute 連線，將源自於 Azure 的網際網路繫結流量重新導向至內部部署資料中心。 如需強制通道的詳細資訊，請參閱 [這裡](expressroute-routing.md#advertising-default-routes)。  
4. **加密** — 即使 ExpressRoute 電路專用於特定客戶，網路提供者仍可能違反規定，讓入侵者檢查封包流量。 若要解決這個可能性，客戶或 CSP 可以針對在內部部署資源與 Azure 資源之間流動的所有流量，定義 IPSec 通道模式原則，進而將透過連線的流量加密 (請參閱上面圖 5：ExpressRoute 安全性中客戶 1 的選擇性通道模式 IPSec)。 第二個選項是在 ExpressRoute 電路的每個端點使用防火牆應用裝置。 這將需要在兩端安裝額外的協力廠商防火牆 Vm/設備，以透過 ExpressRoute 線路加密流量。

![替代文字](./media/expressroute-for-cloud-solution-providers/expressroute-security.png)  

## <a name="next-steps"></a>後續步驟
雲端解決方案提供者服務可讓您提升您對於客戶的價值，而不需要購買昂貴的基礎結構和功能，同時讓您維持主要外包提供者的地位。 透過 CSP API 可達成與 Microsoft Azure 的緊密整合，讓您將 Microsoft Azure 的管理整合在現有的管理架構內。  

在下列連結中可以找到額外的資訊︰

[Azure 雲端解決方案提供者方案](/azure/cloud-solution-provider)。  
[做好準備以雲端解決方案提供者身分交易](https://partner.microsoft.com/solutions/cloud-reseller-pre-launch)。  
[Microsoft Cloud 解決方案提供者資源](https://partner.microsoft.com/solutions/cloud-reseller-resources)。