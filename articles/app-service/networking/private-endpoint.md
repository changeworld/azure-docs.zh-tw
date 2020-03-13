---
title: 使用 Azure 私用端點私下連接到 Web 應用程式
description: 使用 Azure 私用端點私下連接到 Web 應用程式
author: ericgre
ms.assetid: 2dceac28-1ba6-4904-a15d-9e91d5ee162c
ms.topic: article
ms.date: 03/12/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: ad7e04d611129766fe9fb72285fe35ccfbb17626
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/12/2020
ms.locfileid: "79136667"
---
# <a name="using-private-endpoints-for-azure-web-app-preview"></a>使用 Azure Web 應用程式的私用端點（預覽）

您可以使用 Azure Web 應用程式的私用端點，以允許位於私人網路中的用戶端透過私人連結安全地存取應用程式。 私人端點會使用 Azure VNet 位址空間中的 IP 位址。 私人網路上的用戶端與 Web 應用程式之間的網路流量，會透過 Vnet 和 Microsoft 骨幹網路上的私人連結來進行，以避免公開網際網路的暴露。

使用 Web 應用程式的私用端點可讓您：

- 藉由設定服務端點來保護您的 Web 應用程式，以排除公開風險
- 使用 VPN 或 ExpressRoute 私用對等互連，從連線到 Vnet 的內部部署網路安全地連線到 Web 應用程式。

如果您只需要 Vnet 與 Web 應用程式之間的安全連線，服務端點是最簡單的解決方案。 如果您也需要透過 Azure 閘道從內部部署連線到 web 應用程式，區域內對等互連 Vnet 或全域對等互連 Vnet，私用端點是解決方案。  

如需[服務端點][serviceendpoint]的詳細資訊

## <a name="conceptual-overview"></a>概念概觀

私人端點是您的 Azure Web 應用程式在您的虛擬網路（Vnet）中的子網中的特殊網路介面（nic）。
當您建立 Web 應用程式的私用端點時，它會在您的私人網路上的用戶端與 Web 應用程式之間提供安全的連線。 私人端點會從 Vnet 的 IP 位址範圍指派 IP 位址。
私人端點與 Web 應用程式之間的連線會使用安全的[私用連結][privatelink]。 私人端點僅用於傳入流量至您的 Web 應用程式。 傳出流程不會使用此私人端點，但您可以透過[Vnet 整合功能][vnetintegrationfeature]，在不同的子網中將傳出的流量插入至您的網路。

您插入私人端點的子網可以有其他資源，您不需要專用的空白子網。
您可以在與 Web 應用程式不同的區域中部署私用端點。 

> [!Note]
>Vnet 整合功能無法使用與私人端點相同的子網，這是 Vnet 整合功能的限制

從安全性的觀點來看：

- 當您啟用 Web 應用程式的服務端點時，您會停用所有公用存取
- 您可以在其他 Vnet 和子網中啟用多個私人端點，包括其他區域中的 Vnet
- 私人端點 NIC 的 IP 位址必須是動態的，但在您刪除私人端點之前，會保持相同
- 私人端點的 NIC 不能有相關聯的 NSG
- 裝載私人端點的子網可以有相關聯的 NSG，但您必須停用私用端點的網路原則強制執行，請參閱[這篇文章][disablesecuritype]。 因此，您無法以任何 NSG 存取您的私用端點來進行篩選
- 當您啟用 Web 應用程式的私用端點時，不會評估 Web 應用程式的[存取限制][accessrestrictions]設定。
- 您可以藉由移除目的地為網際網路或 Azure 服務的所有 NSG 規則，來減少 vnet 的資料外泄風險。 但是，在您的子網中新增 Web App Service 端點，可讓您連接到裝載于相同戳記並公開至網際網路的任何 Web 應用程式。

Web 應用程式的私用端點可用於層 PremiumV2，並與外部 ASE 隔離。

在 Web 應用程式的 Web HTTP 記錄中，您會找到用戶端來源 IP。 我們已實作為 TCP Proxy 通訊協定，轉送至 Web 應用程式的用戶端 IP 屬性。 如需詳細資訊，請參閱 [本篇文章][tcpproxy]。

![全域總覽][1]


## <a name="dns"></a>DNS

因為這項功能處於預覽狀態，所以我們不會在預覽期間變更 DNS 專案。 您必須自行管理私人 DNS 伺服器或 Azure DNS 私人區域中的 DNS 專案。 如果您需要使用自訂 DNS 名稱，您必須在 Web 應用程式中新增自訂名稱。 在預覽期間，您必須使用公用 DNS 解析來驗證自訂名稱，就像任何自訂名稱一樣。 [自訂 DNS 驗證技術參考][dnsvalidation]

## <a name="pricing"></a>價格

如需定價詳細資料，請參閱 [Azure Private Link 定價][pricing]。

## <a name="limitations"></a>限制

我們會定期改善私人連結功能和私人端點，請參閱[這篇文章][pllimitations]，以取得有關限制的最新資訊。

## <a name="next-steps"></a>後續步驟

若要透過入口網站部署 Web 應用程式的私用端點，請參閱[如何私下連接到 Web 應用程式][howtoguide]


<!--Image references-->
[1]: ./media/private-endpoint/schemaglobaloverview.png

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
