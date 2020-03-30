---
title: Azure 快速路由：電路配置工作流
description: 此頁面會示範設定 ExpressRoute 線路和對等互連的工作流程
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: cherylmc
ms.openlocfilehash: e833e20085d7cfd8f727acb394851e96e7e19368
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75864361"
---
# <a name="expressroute-workflows-for-circuit-provisioning-and-circuit-states"></a>ExpressRoute 工作流程線路佈建和線路狀態
這個頁面以高階觀點引導您完成服務佈建和路由設定的工作流程。

![線路工作流程](./media/expressroute-workflows/expressroute-circuit-workflow.png)

下圖和相應的步驟概述了端到端預配 ExpressRoute 電路的任務。 

1. 使用 PowerShell 來設定 ExpressRoute 線路。 如需更多詳細資料，請依照 [建立 ExpressRoute 線路](expressroute-howto-circuit-classic.md) 一文中的指示進行。
2. 向服務提供者訂購連線能力。 此過程視情況而異。 如需有關如何訂購連線能力的詳細資訊，請連絡連線提供者。
3. 請透過 PowerShell 驗證 ExpressRoute 線路佈建狀態，以確定線路已佈建成功。 
4. 設定路由網域。 如果您的連接供應商管理第 3 層配置，他們將為您的電路配置路由。 如果連接供應商僅提供第 2 層服務，則必須根據[路由要求](expressroute-routing.md)和[路由配置](expressroute-howto-routing-classic.md)頁中描述的準則配置路由。
   
   * 啟用 Azure 私用對等互連 - 啟用此對等互連才能連接到部署在虛擬網路內的虛擬機器 / 雲端服務。

   * 啟用 Microsoft 對等互連 - 啟用此功能以訪問 Microsoft 聯機服務，如 Office 365。 透過 Microsoft 對等互連可存取所有 Azure PaaS 服務。
     
     > [!IMPORTANT]
     > 必須確定您使用個別的 Proxy / 邊緣來連接到 Microsoft，而不是您用於網際網路的 Proxy / 邊緣。 ExpressRoute 和網際網路使用相同的邊緣會導致路由不對稱，並造成網路連線中斷。
     > 
     > 
     
     ![路由工作流程](./media/expressroute-workflows/routing-workflow.png)
5. 將虛擬網路連結到 ExpressRoute 線路 - 您可以將虛擬網路連結到 ExpressRoute 線路。 請依照指示 [連結 VNet](expressroute-howto-linkvnet-arm.md) 到您的線路。 這些 VNet 可以與 ExpressRoute 線路位於相同的 Azure 訂用帳戶中，也可以在不同的訂用帳戶中。

## <a name="expressroute-circuit-provisioning-states"></a>ExpressRoute 線路佈建狀態
每個 ExpressRoute 線路有兩種狀態：

* 服務提供者佈建狀態
* 狀態

Status 代表 Microsoft 的佈建狀態。 這個屬性會在您建立 Expressroute 循環時設定為 [Enabled]

連線提供者佈建狀態代表連線提供者那端的狀態。 可能是 *NotProvisioned*、*Provisioning* 或 *Provisioned*。 ExpressRoute 電路必須處於預配狀態，以便配置對等互連。

### <a name="possible-states-of-an-expressroute-circuit"></a>ExpressRoute 線路的可能狀態
本節列出了 ExpressRoute 電路的可能狀態。

**在建立時**

ExpressRoute 電路將在資源創建時報告以下狀態。

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


**當連線提供者正在佈建線路時**

當連接供應商正在調配電路時，ExpressRoute 電路將報告以下狀態。

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled


**當連線提供者完成佈建程序時**

連接供應商成功預配電路後，ExpressRoute 電路將報告以下狀態。

    ServiceProviderProvisioningState : Provisioned
    Status                           : Enabled


**當連線提供者正在取消佈建循環時**

如果需要取消預配 ExpressRoute 電路，則一旦服務提供者完成取消預配過程，該電路將報告以下狀態。

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


如有需要，您可以選擇重新啟用線路，或執行 PowerShell Cmdlet 刪除線路。  

> [!IMPORTANT]
> 當服務提供者預配狀態正在預配或預配時，無法刪除電路。 連接提供程式需要取消配置電路，然後才能刪除它。 在 Azure 中刪除 ExpressRoute 電路資源之前，Microsoft 將繼續向電路收費。
> 

## <a name="routing-session-configuration-state"></a>路由工作階段組態狀態
如果已在 Microsoft 邊緣啟用了 BGP 會話，則 BGP 預配狀態將報告。 必須啟用狀態才能使用私有或 Microsoft 對等互連。

務必檢查 BGP 工作階段狀態，特別是 Microsoft 對等。 除了 BGP 佈建狀態，還有另一個狀態稱為 *已公告公用首碼狀態*。 播發的公共首碼狀態必須處於*配置*狀態，BGP 會話必須啟動，路由必須端到端地工作。 

如果已公告公用首碼狀態設為 *需要驗證* 狀態，則不會啟用 BGP 工作階段，因為已公告的首碼不符合任何路由登錄中的 AS 編號。 

> [!IMPORTANT]
> 如果播發的公共首碼狀態處於*手動驗證*狀態，則需要在[Microsoft 支援](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)下打開支援票證，並提供您擁有所通告的 IP 位址以及關聯的自治系統號碼的證據。
> 
> 

## <a name="next-steps"></a>後續步驟
* 設定 ExpressRoute 連線。
  
  * [創建快速路由電路](expressroute-howto-circuit-arm.md)
  * [設定路由](expressroute-howto-routing-arm.md)
  * [將 VNet 連結到 ExpressRoute 線路](expressroute-howto-linkvnet-arm.md)

