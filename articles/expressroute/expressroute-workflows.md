---
title: Azure ExpressRoute：線路設定工作流程
description: 此頁面會顯示設定 ExpressRoute 線路和對等互連的工作流程
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/24/2020
ms.author: duau
ms.custom: contperfq1
ms.openlocfilehash: bb0d3cebd9382ef2c81b217417cfbcb6198b6ebb
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89565918"
---
# <a name="expressroute-workflows-for-circuit-provisioning-and-circuit-states"></a>ExpressRoute 工作流程線路佈建和線路狀態

本文將逐步引導您完成服務布建和路由設定工作流程的高層級。 下列各節概述布建 ExpressRoute 線路端對端的工作。

## <a name="workflow-steps"></a>工作流程步驟

### <a name="1-prerequisites"></a>1.Prerequisites

確定符合必要條件。 如需完整清單，請參閱 [必要條件和檢查](expressroute-prerequisites.md)清單。

* 已建立 Azure 訂用帳戶。
* 已建立與 ExpressRoute 合作夥伴的實體連線，或透過 ExpressRoute Direct 進行設定。 查看位置，請參閱 [位置和合作夥伴](expressroute-locations-providers.md#partners) ，以瞭解跨對等互連位置的 expressroute 合作夥伴和 expressroute Direct 連線能力。

### <a name="2-order-connectivity-or-configure-expressroute-direct"></a>2. 訂購連線能力或設定 ExpressRoute Direct

從服務提供者訂購連線能力，或設定 ExpressRoute Direct。

#### <a name="expressroute-partner-model"></a>ExpressRoute 夥伴模型

向服務提供者訂購連線能力。 此過程視情況而異。 如需有關如何訂購連線能力的詳細資訊，請連絡連線提供者。

* 選取 ExpressRoute 合作夥伴
* 選取對等互連位置
* 選取頻寬
* 選取計費模型
* 選取標準或 premium 附加元件

#### <a name="expressroute-direct-model"></a>ExpressRoute Direct 模型

* 查看跨對等互連位置的可用 ExpressRoute Direct 容量。
* 在您的 Azure 訂用帳戶中建立 ExpressRoute Direct 資源，以保留埠。
* 要求和接收授權的字母，並排序對等互連位置提供者的實體交叉連接。
* 使用 [Azure 監視器](expressroute-monitoring-metrics-alerts.md#expressroute-direct-metrics)啟用系統管理狀態，並查看光線等級和實體連結。

### <a name="3-create-an-expressroute-circuit"></a>3. 建立 ExpressRoute 線路

#### <a name="expressroute-partner-model"></a>ExpressRoute 夥伴模型

確認 ExpressRoute 合作夥伴已準備好布建連線能力。 ExpressRoute 線路會從發出服務金鑰時開始收費。 使用 [建立 ExpressRoute 線路](expressroute-howto-circuit-portal-resource-manager.md) 中的指示來建立您的線路。

#### <a name="expressroute-direct-model"></a>ExpressRoute Direct 模型

確定已在兩個連結上啟用實體連結和系統管理員狀態。 請參閱 [如何設定 ExpressRoute Direct](how-to-expressroute-direct-portal.md) 以取得指導方針。 ExpressRoute 線路會從發出服務金鑰時開始收費。 使用 [建立 ExpressRoute 線路](expressroute-howto-circuit-portal-resource-manager.md) 中的指示來建立您的線路。

### <a name="4-service-provider-provisions-connectivity"></a>4. 服務提供者布建連線能力

本節僅適用于 ExpressRoute 夥伴連線模型：

* 提供服務金鑰 (的連接提供者) 的金鑰。
* 提供連線提供者所需的其他資訊 (例如，VPN 識別碼) 。
* 如果提供者管理路由設定，請提供必要的詳細資料。

您可以使用 PowerShell、Azure 入口網站或 CLI 來確認 ExpressRoute 線路布建狀態，以確定已成功布建線路。

### <a name="5-configure-routing-domains"></a>5. 設定路由網域

設定路由網域。 如果您的連線提供者會管理第3層設定，則會為您的線路設定路由。 如果您的連線提供者僅提供第2層服務，或您使用 ExpressRoute Direct，您必須根據 [路由需求](expressroute-routing.md) 和 [路由](expressroute-howto-routing-classic.md) 設定文章中所述的指導方針來設定路由。

#### <a name="for-azure-private-peering"></a>適用于 Azure 私人對等互連

啟用私人對等互連，以連線至部署在 Azure 虛擬網路內的 Vm 和雲端服務。

* 路徑 1 (/30) 的對等互連子網
* 路徑 2 (/30) 的對等互連子網
* 對等互連的 VLAN 識別碼
* 適用于對等互連的 ASN
* ExpressRoute ASN = 12076
* MD5 雜湊 (選擇性) 

#### <a name="for-microsoft-peering"></a>適用于 Microsoft 對等互連

啟用此來存取 Microsoft 線上服務，例如 Microsoft 365。 此外，透過 Microsoft 對等互連可存取所有 Azure PaaS 服務。 您必須確保使用不同的 proxy/edge 來連線至 Microsoft，而不是您用來連線到網際網路。 ExpressRoute 和網際網路使用相同的邊緣會導致路由不對稱，並造成網路連線中斷。

* 路徑 1 (/30) 的對等互連子網必須是公用 IP
* 路徑 2 (/30) 的對等互連子網必須是公用 IP
* 對等互連的 VLAN 識別碼
* 適用于對等互連的 ASN
* 公告的首碼-必須是公用 IP 首碼
* 客戶 ASN (選擇性（如果不同于對等互連 ASN) 
* IP 和 ASN 驗證的 RIR/IRR
* ExpressRoute ASN = 12076
* MD5 雜湊 (選擇性) 

### <a name="6-start-using-the-expressroute-circuit"></a>6. 開始使用 ExpressRoute 線路

* 您可以將 Azure 虛擬網路連結至 ExpressRoute 線路，以啟用從內部部署至 Azure 虛擬網路的連線能力。 如需指引，請參閱將 [VNet 連結至線路](expressroute-howto-linkvnet-arm.md) 文章。 這些 VNet 可以與 ExpressRoute 線路位於相同的 Azure 訂用帳戶中，也可以在不同的訂用帳戶中。
* 透過 Microsoft 對等互連來連線到 Azure 服務和 Microsoft 雲端服務。

##  <a name="expressroute-partner-circuit-provisioning-states"></a><a name="expressroute-circuit-provisioning-states"></a>ExpressRoute 合作夥伴線路布建狀態

下一節將概述 ExpressRoute 夥伴連接模型的不同 ExpressRoute 線路狀態。
每個 ExpressRoute 合作夥伴電路都有兩個狀態：

* **ServiceProviderProvisioningState** 代表連接提供者端的狀態。 可能是 *NotProvisioned*、*Provisioning* 或 *Provisioned*。 ExpressRoute 線路必須處於已布建狀態，才能設定對等互連。 **此狀態只適用于 expressroute 夥伴電路，而且不會顯示在 Expressroute Direct 線路的屬性中**。

* **狀態** 代表 Microsoft 的布建狀態。 當您建立 ExpressRoute 線路時，這個屬性會設定為 [已啟用]

### <a name="possible-states-of-an-expressroute-circuit"></a>ExpressRoute 線路的可能狀態

本節將概述 ExpressRoute 夥伴連線模型下建立的 ExpressRoute 電路可能狀態。

**在建立時**

ExpressRoute 線路會在建立資源時報告下列狀態。

```output
ServiceProviderProvisioningState : NotProvisioned
Status                           : Enabled
```

**當連線提供者正在布建線路的過程中**

當連線提供者正在布建線路時，ExpressRoute 線路將會報告下列狀態。

```output
ServiceProviderProvisioningState : Provisioning
Status                           : Enabled
```

**當連線提供者完成布建程式時**

當連線提供者成功布建線路之後，ExpressRoute 線路將會報告下列狀態。

```output
ServiceProviderProvisioningState : Provisioned
Status                           : Enabled
```

**當連線提供者正在取消布建線路時**

如果需要取消布建 ExpressRoute 線路，當服務提供者完成取消布建程式之後，線路將會報告下列狀態。

```output
ServiceProviderProvisioningState : NotProvisioned
Status                           : Enabled
```

如有需要，您可以選擇重新啟用線路，或執行 PowerShell Cmdlet 刪除線路。  

> [!IMPORTANT]
> 布建或布建 ServiceProviderProvisioningState 時，無法刪除電路。 連線提供者必須先取消布建電路，才能將其刪除。 在 Azure 中刪除 ExpressRoute 電路資源之前，Microsoft 將繼續為線路計費。
> 

## <a name="routing-session-configuration-state"></a>路由工作階段組態狀態

BGP 布建狀態會報告是否已在 Microsoft edge 上啟用 BGP 會話。 必須啟用狀態，才能使用私人或 Microsoft 對等互連。

務必檢查 BGP 工作階段狀態，特別是 Microsoft 對等。 除了 BGP 佈建狀態，還有另一個狀態稱為 *已公告公用首碼狀態*。 公告的公用首碼狀態必須處於 *設定* 狀態，才能讓 BGP 會話啟動，而且您的路由會端對端運作。 

如果已公告公用首碼狀態設為 *需要驗證* 狀態，則不會啟用 BGP 工作階段，因為已公告的首碼不符合任何路由登錄中的 AS 編號。

> [!IMPORTANT]
> 如果公告的公用首碼狀態處於 *手動驗證* 狀態，您需要向 [Microsoft 支援](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) 服務開啟支援票證，並提供您所通告的 IP 位址與相關聯的自發系統編號的辨識項。
> 
> 

## <a name="next-steps"></a>接下來的步驟

* 設定 ExpressRoute 連線。
  
  * [建立 ExpressRoute 線路](expressroute-howto-circuit-arm.md)
  * [設定路由](expressroute-howto-routing-arm.md)
  * [將 VNet 連結到 ExpressRoute 線路](expressroute-howto-linkvnet-arm.md)