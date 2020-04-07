---
title: 使用 Azure 專用終結點私下連接到 Web 應用
description: 使用 Azure 專用終結點私下連接到 Web 應用
author: ericgre
ms.assetid: 2dceac28-1ba6-4904-a15d-9e91d5ee162c
ms.topic: article
ms.date: 03/18/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.custom: fasttrack-edit
ms.openlocfilehash: 4d139cfa50afa94621066995314737fac70bbafe
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756287"
---
# <a name="using-private-endpoints-for-azure-web-app-preview"></a>為 Azure Web 應用使用專用終結點(預覽)

> [!Note]
> 預覽版在美國東部和美國西部 2 區提供,適用於所有高級 V2 Windows 和 Linux Web 應用和彈性高級功能。 

您可以將 Azure Web 應用的專用終結點用於允許位於專用網路中的用戶端透過專用連結安全地存取應用。 專用終結點使用 Azure VNet 位址空間中的 IP 位址。 專用網路上的用戶端和 Web 應用之間的網路流量會透過 VNet 和 Microsoft 骨幹網路上的專用連結遍歷,從而消除公共 Internet 的暴露。

為 Web 應用使用專用終結點使您能夠:

- 通過配置專用終結點來保護 Web 應用,從而消除公共公開。
- 使用 VPN 或 ExpressRoute 專用對等互連從連接到 VNet 的本地網路安全地連接到 Web 應用。

如果只需要 VNet 和 Web 應用之間的安全連接,則服務終結點是最簡單的解決方案。 如果需要透過 Azure 閘道、區域對等 VNet 或全域對等 VNet 從本地訪問 Web 應用,則私有終結點就是解決方案。  

有關詳細資訊,請參閱[服務終結點][serviceendpoint]。

## <a name="conceptual-overview"></a>概念概觀

專用終結點是虛擬網路 (VNet) 中子網中的 Azure Web 應用的特殊網路介面 (NIC)。
為 Web 應用創建專用終結點時,它可在專用網路上的用戶端和 Web 應用之間提供安全連接。 從 VNet 的 IP 位址範圍內為專用終結點分配了 IP 位址。
專用終結點與 Web 應用之間的連線使用安全的[專用連結][privatelink]。 專用終結點僅用於傳入 Web 應用的流。 傳出流不會使用此專用終結點,但您可以通過[VNet 整合功能][vnetintegrationfeature]將傳出流注入到其他子網路中的網路。

插入專用終結點的子網可以具有其他資源,您不需要專用空子網。
您還可以將專用終結點部署到與 Web 應用不同的區域中。 

> [!Note]
>VNet 集成功能不能使用與專用終結點相同的子網,這是 VNet 集成功能的限制。

從安全角度來看:

- 當您將專用終結點啟用到 Web 應用時,將禁用所有公共訪問。
- 您可以在其他 VNet 和子網中啟用多個專用終結點,包括其他地區的 VNet。
- 專用終結點 NIC 的 IP 位址必須是動態的,但在刪除專用終結點之前將保持不變。
- 專用終結點的 NIC 不能關聯 NSG。
- 承載專用終結點的子網可以關聯 NSG,但必須禁用專用終結點的網路策略實施:請參閱[禁用專用終結點的網路策略][disablesecuritype]。 因此,您不能通過任何 NSG 篩選對私有終結點的訪問。
- 當您將專用終結點啟用到 Web 應用時,不會評估 Web 應用[的訪問限制][accessrestrictions]配置。
- 您可以通過刪除目標為 Internet 或 Azure 服務的所有 NSG 規則來降低來自 VNet 的數據外洩風險。 但是,在子網中添加 Web 應用專用終結點將允許您訪問在同一部署戳中託管並暴露到 Internet 的任何 Web 應用。

在 Web 應用程式的 Web HTTP 紀錄中,您將找到用戶端源 IP。 這是使用 TCP 代理協議實現的,將用戶端 IP 屬性轉發到 Web 應用。 有關詳細資訊,請參閱使用[TCP 代理 v2 取得連接資訊][tcpproxy]。


  > [!div class="mx-imgBorder"]
  > ![Web 應用專用終結點全域概述](media/private-endpoint/global-schema-web-app.png)

## <a name="dns"></a>DNS

由於此功能處於預覽狀態,因此在預覽期間我們不會更改 DNS 條目。 您需要自行管理專用 DNS 伺服器或 Azure DNS 專用區域中的 DNS 條目。
如果需要使用自訂 DNS 名稱,則必須在 Web 應用中添加自訂名稱。 在預覽期間,必須像使用公共 DNS 解析一樣驗證自定義名稱。 有關詳細資訊,請參閱[自訂 DNS 驗證][dnsvalidation]。

## <a name="pricing"></a>定價

如需定價詳細資料，請參閱 [Azure Private Link 定價][pricing]。

## <a name="limitations"></a>限制

我們正在定期改進專用連結功能和專用終結點,請查看[本文][pllimitations],瞭解有關限制的最新資訊。

## <a name="next-steps"></a>後續步驟

要透過門戶部署 Web 應用的專用終結點,請參閱[如何私下連接到 Web 應用][howtoguide]




<!--Links-->
[serviceendpoint]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview
[privatelink]: https://docs.microsoft.com/azure/private-link/private-link-overview
[vnetintegrationfeature]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[disablesecuritype]: https://docs.microsoft.com/azure/private-link/disable-private-endpoint-network-policy
[accessrestrictions]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[tcpproxy]: ../../private-link/private-link-service-overview.md#getting-connection-information-using-tcp-proxy-v2
[dnsvalidation]: https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain
[pllimitations]: https://docs.microsoft.com/azure/private-link/private-endpoint-overview#limitations
[pricing]: https://azure.microsoft.com/pricing/details/private-link/
[howtoguide]: https://docs.microsoft.com/azure/private-link/create-private-endpoint-webapp-portal
