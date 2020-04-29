---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 04/15/2020
ms.author: ccompy
ms.openlocfilehash: f7208307df51ecefb76f9adaedea59b327cdc19e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81604890"
---
使用區域 VNet 整合可讓您的應用程式存取：

* VNet 中的資源與您的應用程式位於相同的區域。
* Vnet 中的資源會對等互連到與您的應用程式整合的 VNet。
* 服務端點保護的服務。
* 跨 Azure ExpressRoute 連線的資源。
* 您所整合之 VNet 中的資源。
* 跨對等互連連接的資源，包括 Azure ExpressRoute 連線。
* 私人端點 

當您在相同區域中搭配 Vnet 使用 VNet 整合時，您可以使用下列 Azure 網路功能：

* **網路安全性群組（nsg）**：您可以使用放在整合子網上的 NSG 來封鎖輸出流量。 輸入規則不適用，因為您無法使用 VNet 整合來提供應用程式的輸入存取。
* **路由表（udr）**：您可以將路由表放在整合子網上，以在您想要的地方傳送輸出流量。

根據預設，您的應用程式只會將 RFC1918 流量路由傳送至您的 VNet。 如果您想要將所有輸出流量路由傳送至 VNet，請將應用程式設定 WEBSITE_VNET_ROUTE_ALL 套用至您的應用程式。 若要設定應用程式設定：

1. 在您的應用程式入口網站**中，移至 [設定**] UI。 選取 [**新增應用程式設定**]。
1. 在 [**名稱**] 方塊中輸入**WEBSITE_VNET_ROUTE_ALL** ，然後在 [**值**] 方塊中輸入**1** 。

   ![提供應用程式設定][4]

1. 選取 [確定]  。
1. 選取 [儲存]  。

如果您將所有輸出流量路由傳送至 VNet，則會受限於套用至您的整合子網的 Nsg 和 Udr。 當您將所有輸出流量路由傳送至 VNet 時，您的輸出位址仍會是應用程式屬性中所列的輸出位址，除非您提供路由以將流量傳送到其他位置。

在相同的區域中使用 VNet 與 Vnet 的整合有一些限制：

* 您無法跨全球對等互連連線連線到資源。
* 此功能僅適用于支援 PremiumV2 App Service 方案的較新 Azure App Service 縮放單位。
* 整合子網只能由一個 App Service 方案使用。
* App Service 環境中的隔離式方案應用程式無法使用此功能。
* 此功能需要在 Azure Resource Manager VNet 中有一個/27 具有32位址或更大的未使用子網。
* 應用程式和 VNet 必須位於相同的區域。
* 您無法使用整合式應用程式刪除 VNet。 請先移除整合，再刪除 VNet。
* 您只能將與應用程式位於相同訂用帳戶中的 Vnet 整合。
* 根據 App Service 方案，您只能有一個區域 VNet 整合。 相同 App Service 方案中的多個應用程式可以使用相同的 VNet。
* 當有使用區域 VNet 整合的應用程式時，您無法變更應用程式或方案的訂用帳戶。
* 您的應用程式無法在沒有設定變更的情況下解析 Azure DNS 私人區域中的位址

每個方案實例會使用一個位址。 如果您將應用程式調整為五個實例，則會使用五個位址。 因為子網大小在指派之後無法變更，所以您必須使用夠大的子網，以容納您的應用程式可能會達到的任何規模。 包含64位址的/26 是建議的大小。 具有64位址的/26 會容納具有30個實例的 Premium 方案。 當您相應增加或減少方案時，您需要一小段時間才會有兩次的位址。

如果您想要讓其他方案中的應用程式連線到另一個方案中已由應用程式連接的 VNet，請選取與預先存在的 VNet 整合所使用的子網不同的子網。

適用于 Linux 的功能處於預覽狀態。 這項功能的 Linux 形式僅支援呼叫 RFC 1918 位址（10.0.0.0/8、172.16.0.0/12、192.168.0.0/16）。

### <a name="web-or-function-app-for-containers"></a>容器的 Web 或函數應用程式

如果您使用內建映射在 Linux 上裝載您的應用程式，區域 VNet 整合就能運作，而不需要進行其他變更。 如果您使用容器的 Web 或函數應用程式，您必須修改 docker 映射以使用 VNet 整合。 在您的 docker 映射中，使用埠環境變數作為主要 web 伺服器的接聽埠，而不是使用硬式編碼的埠號碼。 平臺會在容器啟動時自動設定埠環境變數。 如果您使用 SSH，則當您使用區域 VNet 整合時，SSH 背景程式必須設定為接聽 SSH_PORT 環境變數所指定的埠號碼。 Linux 上不支援閘道所需的 VNet 整合。

### <a name="service-endpoints"></a>服務端點

區域 VNet 整合可讓您使用服務端點。 若要將服務端點與您的應用程式搭配使用，請使用區域 VNet 整合來連線到選取的 VNet，然後在您用於整合的子網上，使用目的地服務來設定服務端點。 如果您想要透過服務端點存取服務：

1. 設定與 web 應用程式的區域 VNet 整合
1. 移至目的地服務，並針對用於整合的子網設定服務端點

### <a name="network-security-groups"></a>網路安全性群組

您可以使用網路安全性群組來封鎖 VNet 中資源的輸入和輸出流量。 使用區域 VNet 整合的應用程式可以使用[網路安全性群組][VNETnsg]來封鎖對 VNet 或網際網路中資源的輸出流量。 若要封鎖公用位址的流量，您必須將應用程式設定 WEBSITE_VNET_ROUTE_ALL 設為1。 NSG 中的輸入規則不會套用至您的應用程式，因為 VNet 整合只會影響來自您應用程式的輸出流量。

若要控制應用程式的輸入流量，請使用「存取限制」功能。 無論套用至您的整合子網的任何路由為何，套用至您的整合子網的 NSG 都會生效。 如果 WEBSITE_VNET_ROUTE_ALL 設定為1，而且您沒有任何路由會影響整合子網上的公用位址流量，則您的所有輸出流量仍會受限於指派給您的整合子網的 Nsg。 如果未設定 WEBSITE_VNET_ROUTE_ALL，Nsg 只會套用至 RFC1918 流量。

### <a name="routes"></a>路由

您可以使用路由表，將來自您應用程式的輸出流量路由傳送到您想要的任何位置。 根據預設，路由表只會影響您的 RFC1918 目的地流量。 如果您將 WEBSITE_VNET_ROUTE_ALL 設定為1，則所有的輸出呼叫都會受到影響。 在您的整合子網上設定的路由，不會影響對輸入應用程式要求的回復。 一般目的地可以包含防火牆裝置或閘道。

如果您想要路由傳送內部部署的所有輸出流量，您可以使用路由表將所有輸出流量傳送到您的 ExpressRoute 閘道。 如果您將流量路由傳送至閘道，請務必設定外部網路中的路由，以傳送任何回復。

邊界閘道協定（BGP）路由也會影響您的應用程式流量。 如果您有來自類似 ExpressRoute 閘道的 BGP 路由，您的應用程式輸出流量會受到影響。 根據預設，BGP 路由只會影響您的 RFC1918 目的地流量。 如果 WEBSITE_VNET_ROUTE_ALL 設定為1，則您的 BGP 路由可能會影響所有輸出流量。

### <a name="azure-dns-private-zones"></a>Azure DNS 私人區域 

當您的應用程式與 VNet 整合之後，它會使用您的 VNet 所設定的相同 DNS 伺服器。 根據預設，您的應用程式無法與 Azure DNS 私人區域搭配使用。 若要使用 Azure DNS 私人區域您需要新增下列應用程式設定：

1. 值為168.63.129.16 的 WEBSITE_DNS_SERVER 
1. 值為1的 WEBSITE_VNET_ROUTE_ALL

除了讓您的應用程式使用 Azure DNS 私人區域之外，這些設定也會將您應用程式的所有輸出呼叫傳送至 VNet。

### <a name="private-endpoints"></a>私人端點

如果您想要呼叫[私人端點][privateendpoints]，則需要與 Azure DNS 私人區域整合，或在應用程式所使用的 DNS 伺服器中管理私人端點。 

<!--Image references-->
[4]: ../includes/media/web-sites-integrate-with-vnet/vnetint-appsetting.png

<!--Links-->
[VNETnsg]: https://docs.microsoft.com/azure/virtual-network/security-overview/
[privateendpoints]: https://docs.microsoft.com/azure/app-service/networking/private-endpoint
