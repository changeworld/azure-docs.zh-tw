---
title: 使用 Azure 私用端點私下連接到 Web 應用程式
description: 使用 Azure 私用端點私下連接到 Web 應用程式
author: ericgre
ms.assetid: 2dceac28-1ba6-4904-a15d-9e91d5ee162c
ms.topic: article
ms.date: 05/12/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.custom: fasttrack-edit
ms.openlocfilehash: 6a95c021153a458a4e3f804e64724b73ea1f1937
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83198826"
---
# <a name="using-private-endpoints-for-azure-web-app-preview"></a>使用 Azure Web 應用程式的私用端點（預覽）

> [!Note]
> 預覽適用于所有 PremiumV2 Windows 和 Linux Web Apps 和彈性 Premium 功能的美國東部和美國西部2區域。 

您可以使用 Azure Web 應用程式的私用端點，以允許位於私人網路中的用戶端透過私人連結安全地存取應用程式。 私人端點會使用 Azure VNet 位址空間中的 IP 位址。 私人網路上的用戶端與 Web 應用程式之間的網路流量，會透過 VNet 和 Microsoft 骨幹網路上的私人連結來進行，以消除公開網際網路的暴露。

使用 Web 應用程式的私用端點可讓您：

- 藉由設定私人端點來保護您的 Web 應用程式，以排除公開風險。
- 使用 VPN 或 ExpressRoute 私用對等互連，從連線到 VNet 的內部部署網路安全地連線到 Web 應用程式。

如果您只需要 VNet 與 Web 應用程式之間的安全連線，服務端點是最簡單的解決方案。 如果您也需要透過 Azure 閘道、區域內對等互連 VNet 或全域對等互連 VNet，從內部部署連線到 web 應用程式，則私用端點是解決方案。  

如需詳細資訊，請參閱[服務端點][serviceendpoint]。

## <a name="conceptual-overview"></a>概念概觀

私人端點是您的 Azure Web 應用程式在虛擬網路（VNet）的子網中的特殊網路介面（NIC）。
當您建立 Web 應用程式的私用端點時，它會在您的私人網路上的用戶端與 Web 應用程式之間提供安全的連線。 私人端點會從 VNet 的 IP 位址範圍指派 IP 位址。
私人端點與 Web 應用程式之間的連線會使用安全的[私用連結][privatelink]。 私人端點僅用於傳入流量至您的 Web 應用程式。 傳出流程不會使用此私人端點，但您可以透過[VNet 整合功能][vnetintegrationfeature]，在不同的子網中將傳出的流量插入至您的網路。

您插入私人端點的子網可以有其他資源，您不需要專用的空白子網。
您也可以在與 Web 應用程式不同的區域中部署私用端點。 

> [!Note]
>VNet 整合功能無法使用與私人端點相同的子網，這是 VNet 整合功能的限制。

從安全性的觀點來看：

- 當您啟用 Web 應用程式的私用端點時，會停用所有公用存取。
- 您可以在其他 Vnet 和子網中啟用多個私人端點，包括其他區域中的 Vnet。
- 私人端點 NIC 的 IP 位址必須是動態的，但在您刪除私人端點之前，它會保持不變。
- 私人端點的 NIC 不能有相關聯的 NSG。
- 裝載私人端點的子網可以有相關聯的 NSG，但您必須停用私用端點的網路原則強制執行：請參閱[停用私人端點的網路原則][disablesecuritype]。 因此，您無法以任何 NSG 存取您的私用端點來進行篩選。
- 當您啟用 Web 應用程式的私用端點時，不會評估 Web 應用程式的[存取限制][accessrestrictions]設定。
- 您可以藉由移除目的地標記為網際網路或 Azure 服務的所有 NSG 規則，來減少 VNet 的資料外泄風險。 但是，在您的子網中新增 Web 應用程式私人端點，可讓您連線到裝載于相同部署戳記中的任何 Web 應用程式，並對網際網路公開。

在 Web 應用程式的 Web HTTP 記錄中，您會找到用戶端來源 IP。 這會使用 TCP Proxy 通訊協定來執行，將用戶端 IP 屬性轉送到 Web 應用程式。 如需詳細資訊，請參閱[使用 TCP Proxy V2 取得連接資訊][tcpproxy]。


  > [!div class="mx-imgBorder"]
  > ![Web 應用程式私用端點全域總覽](media/private-endpoint/global-schema-web-app.png)

## <a name="dns"></a>DNS

因為這項功能處於預覽狀態，所以我們不會在預覽期間變更 DNS 專案。 您必須自行管理私人 DNS 伺服器或 Azure DNS 私人區域中的 DNS 專案。
如果您需要使用自訂 DNS 名稱，您必須在 Web 應用程式中新增自訂名稱。 在預覽期間，您必須使用公用 DNS 解析來驗證自訂名稱，就像任何自訂名稱一樣。 如需詳細資訊，請參閱[自訂 DNS 驗證][dnsvalidation]。

如果您需要使用 Kudu 主控台，或 Kudu REST API （例如，使用 Azure DevOps 自我裝載代理程式部署），您需要在 Azure DNS 私人區域或自訂 DNS 伺服器中建立兩個記錄。 
- PrivateEndpointIP yourwebappname.azurewebsites.net 
- PrivateEndpointIP yourwebappname.scm.azurewebsites.net 

## <a name="pricing"></a>定價

如需定價詳細資料，請參閱 [Azure Private Link 定價][pricing]。

## <a name="limitations"></a>限制

當您在具有私用端點的彈性 Premium 方案中使用 Azure 函式時，若要在 Azure 入口網站中執行或執行函式，您必須具有直接的網路存取權，否則會收到 HTTP 403 錯誤。 換句話說，您的瀏覽器必須能夠連線到私人端點，才能從 Azure 入口網站執行此函式。 

在預覽期間，只有生產位置會在私人端點後方公開，而其他位置僅供公用端點連線。

我們會定期改善私人連結功能和私人端點，請參閱[這篇文章][pllimitations]，以取得有關限制的最新資訊。

## <a name="next-steps"></a>後續步驟

若要透過入口網站部署 Web 應用程式的私用端點，請參閱[如何私下連接到 Web 應用程式][howtoguide]




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
