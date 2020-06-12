---
title: 使用 Azure 私人端點私下連線至 Web 應用程式
description: 使用 Azure 私人端點私下連線至 Web 應用程式
author: ericgre
ms.assetid: 2dceac28-1ba6-4904-a15d-9e91d5ee162c
ms.topic: article
ms.date: 05/25/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.custom: fasttrack-edit
ms.openlocfilehash: 4c48a2fad927812cc45543243b48a2df81acf73b
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/26/2020
ms.locfileid: "83846948"
---
# <a name="using-private-endpoints-for-azure-web-app-preview"></a>使用 Azure Web 應用程式的私人端點 (預覽)

> [!Note]
> 在預覽重新整理中，我們發行了資料外泄保護功能。
> 所有 PremiumV2 Windows 與 Linux Web Apps 和彈性進階函式的預覽適用於美國東部和美國西部 2 區域。 

您可以使用 Azure Web 應用程式的私人端點來允許位於私人網路中的用戶端透過 Private Link 安全地存取應用程式。 私人端點會使用 Azure VNet 位址空間中的 IP 位址。 私人網路上的用戶端與 Web 應用程式之間的網路流量會流經 VNet 和 Microsoft 骨幹網路上的 Private Link，以排除公開網際網路的風險。

使用 Web 應用程式的私人端點可讓您：

- 藉由設定私人端點來保護您的 Web 應用程式，以排除公開風險。
- 使用 VPN 或 ExpressRoute 私人對等互連，安全地從連線至 VNet 的內部部署網路連線到 Web 應用程式。
- 避免從您的 VNet 外泄任何資料。 

如果您只需要 VNet 與 Web 應用程式之間的安全連線，服務端點是最簡單的解決方案。 如果您也需要透過 Azure 閘道、區域對等互連 VNet 或全域對等互連 VNet，從內部部署連線到 Web 應用程式，則私用端點是解決方案。  

如需詳細資訊，請參閱[服務端點][serviceendpoint]。

## <a name="conceptual-overview"></a>概念概觀

私人端點是您虛擬網路 (VNet) 的子網路中，Azure Web 應用程式的特殊網路介面 (NIC)。
當您建立 Web 應用程式的私人端點時，其會在您私人網路的用戶端與 Web 應用程式之間提供安全的連線。 私人端點會從 VNet 的 IP 位址範圍指派 IP 位址。
私人端點與 Web 應用程式之間的連線會使用安全的 [Private Link][privatelink]。 私人端點僅用於您 Web 應用程式的傳入流量。 傳出流量不會使用此私人端點，但您可以透過 [VNet 整合功能][vnetintegrationfeature]，將傳出流量插入不同子網路中的網路。

您插入私人端點的子網路在其中可以有其他資源，您不需要專用的空白子網路。
您也可以在與 Web 應用程式不同的區域中部署私人端點。 

> [!Note]
>VNet 整合功能無法使用與私人端點相同的子網路，這是 VNet 整合功能的限制。

從安全性的觀點來看：

- 當您啟用 Web 應用程式的私人端點時，會停用所有公用存取。
- 您可以在其他 VNet 和子網路啟用多個私人端點，包括其他區域中的 VNet。
- 私人端點 NIC 的 IP 位址必須是動態的，但在您刪除私人端點之前，其會保持不變。
- 私人端點的 NIC 不能與 NSG 建立關聯。
- 裝載私人端點的子網路可與 NSG 建立關聯，但您必須停用私人端點的網路原則強制執行：請參閱[停用私人端點的網路原則][disablesecuritype]。 因此，您無法依任何 NSG 來篩選您私人端點的存取權。
- 當您啟用 Web 應用程式的私人端點時，不會評估 Web 應用程式的[存取限制][accessrestrictions]設定。
- 您可以移除目的地為標記網際網路或 Azure 服務的所有 NSG 規則，藉此排除 VNet 中的資料外泄風險。 當您部署 Web 應用程式的私人端點時，只能透過私人端點存取此特定 Web 應用程式。 如果您有另一個 Web 應用程式，則必須針對這個其他 Web 應用程式部署另一個專用的私人端點。

在 Web 應用程式的 Web HTTP 記錄中，您會找到用戶端來源 IP。 這會使用 TCP Proxy 通訊協定來實作，將用戶端 IP 屬性轉送到 Web 應用程式。 如需詳細資訊，請參閱[使用 TCP Proxy v2 取得連線資訊][tcpproxy]。


  > [!div class="mx-imgBorder"]
  > ![Web 應用程式私人端點全域概觀](media/private-endpoint/global-schema-web-app.png)

## <a name="dns"></a>DNS

因為這項功能處於預覽狀態，所以我們不會在預覽期間變更 DNS 項目。 您必須自行管理私人 DNS 伺服器或 Azure DNS 私人區域中的 DNS 項目。
如果您需要使用自訂 DNS 名稱，則必須在 Web 應用程式中新增自訂名稱。 在預覽期間，您必須使用公用 DNS 解析來驗證自訂名稱，就如同任何自訂名稱一樣。 如需詳細資訊，請參閱[自訂 DNS 驗證][dnsvalidation]。

如果您需要使用 Kudu 主控台或 Kudu REST API (例如，使用 Azure DevOps 自我裝載式代理程式進行部署)，則必須在 Azure DNS 私人區域或自訂 DNS 伺服器中建立兩個記錄。 
- PrivateEndpointIP yourwebappname.azurewebsites.net 
- PrivateEndpointIP yourwebappname.scm.azurewebsites.net 

## <a name="pricing"></a>定價

如需定價詳細資料，請參閱 [Azure Private Link 定價][pricing]。

## <a name="limitations"></a>限制

當您在彈性進階方案中使用 Azure 函式搭配私人端點時，若要在 Azure Web 入口網站中執行函式，您必須具有直接網路存取權，否則會收到 HTTP 403 錯誤。 換句話說，您的瀏覽器必須能夠連線到私人端點，才能從 Azure Web 入口網站執行此函式。 

在預覽期間，只有生產位置會在私人端點後方公開，而其他位置必須由公用端點進行連線。

我們會定期改善 Private Link 功能和私人端點，請查看[這篇文章][pllimitations]，以取得有關限制的最新資訊。

## <a name="next-steps"></a>後續步驟

若要透過入口網站部署 Web 應用程式的私人端點，請參閱[如何私下連線至 Web 應用程式][howtoguide]




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
