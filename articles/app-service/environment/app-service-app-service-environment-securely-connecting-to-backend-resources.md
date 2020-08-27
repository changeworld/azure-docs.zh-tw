---
title: 連接到後端 v1
description: 了解如何安全地從 App Service 環境連接到後端資源。 本文件僅提供給使用舊版 v1 ASE 的客戶。
author: stefsch
ms.assetid: f82eb283-a6e7-4923-a00b-4b4ccf7c4b5b
ms.topic: article
ms.date: 10/04/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 9f8e288f771b9d584a0fd3430115f5fa60f68e47
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "88961800"
---
# <a name="connect-securely-to-back-end-resources-from-an-app-service-environment"></a>安全地從 App Service 環境連線到後端資源
因為 App Service 環境一律會在 Azure Resource Manager 虛擬網路或者**** 傳統式部署模型****[虛擬網路][virtualnetwork]兩者之一中建立，從 App Service 環境傳出至其他後端資源的連線可以獨佔方式透過虛擬網路傳送。 從2016年6月起，Ase 也可以部署到使用公用位址範圍或 RFC1918 位址空間 (私人位址) 的虛擬網路。  

例如，SQL Server 可能會在已鎖定連接埠 1433 的虛擬機器叢集上執行。  此端點可能已納入 ACL，只允許從相同虛擬網路上的其他資源進行存取。  

另一個例子則是，敏感性端點可能會執行內部部署並透過[站台對站台][SiteToSite]或 [Azure ExpressRoute][ExpressRoute] 連線至 Azure。  因此，只有連線到站對站或 ExpressRoute 通道的虛擬網路中的資源，才能存取內部部署端點。

針對所有這些案例，在 App Service 環境上執行的應用程式可以安全地連線到各種伺服器和資源。 如果從應用程式的輸出流量 App Service 環境執行于相同虛擬網路中的私人端點，或連線到相同的虛擬網路，則它只會流經虛擬網路。  私人端點的輸出流量不會透過公用網際網路傳送。

其中一個問題適用于從 App Service 環境到虛擬網路內端點的輸出流量。 App Service 環境無法達到與 App Service 環境位於 **相同** 子網中的虛擬機器端點。 這項限制通常不會造成問題，如果 App Service 環境部署到已保留供 App Service 環境獨佔使用的子網中。

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="outbound-connectivity-and-dns-requirements"></a>輸出連線和 DNS 需求
為了讓 App Service 環境正確運作，它需要不同端點的輸出存取權。 [ExpressRoute 的網路組態](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity) 文章的＜需要的網路連線＞一節中有提供 ASE 所使用的外部端點完整清單。

App Service 環境需要針對虛擬網路設定的有效 DNS 基礎結構。  如果 DNS 設定在建立 App Service 環境之後變更，開發人員可以強制 App Service 環境挑選新的 DNS 設定。 在入口網站的 [App Service 環境管理] 分頁頂端，選取 [ **重新開機** ] 圖示以觸發輪流環境重新開機，這會導致環境挑選新的 DNS 設定。

此外，也建議您事先設定 vnet 上的任何自訂 DNS 伺服器，然後再建立 App Service 環境。  如果虛擬網路的 DNS 設定在建立 App Service 環境期間有所變更，則會導致 App Service 環境建立程式失敗。 在 VPN 閘道的另一端，如果有無法連線或無法使用的自訂 DNS 伺服器，App Service 環境建立程式也會失敗。

## <a name="connecting-to-a-sql-server"></a>連接至 SQL Server
常見的 SQL Server 組態會有在連接埠 1433 上接聽的端點：

![SQL Server 端點][SqlServerEndpoint]

有兩種方法可限制送至此端點的流量：

* [網路存取控制清單][NetworkAccessControlLists] (網路 ACL)
* [網路安全性群組][NetworkSecurityGroups]

## <a name="restricting-access-with-a-network-acl"></a>利用網路 ACL 限制存取
使用網路存取控制清單可以保護連接埠 1433。  下列範例會將用戶端定址的指派許可權新增至虛擬網路內，並封鎖對所有其他用戶端的存取。

![網路存取控制清單範例][NetworkAccessControlListExample]

在 App Service 環境中執行的任何應用程式，在與 SQL Server 相同的虛擬網路中，都可以連接到 SQL Server 實例。 使用 SQL Server 虛擬機器的 **VNet 內部** IP 位址。  

下列連接字串範例會使用其私密 IP 位址參考 SQL Server。

`Server=tcp:10.0.1.6;Database=MyDatabase;User ID=MyUser;Password=PasswordHere;provider=System.Data.SqlClient`

雖然虛擬機器也有公用端點，但使用公用 IP 位址的連線嘗試將會遭到拒絕，因為網路 ACL。 

## <a name="restricting-access-with-a-network-security-group"></a>利用網路安全性群組限制存取
另一種保護存取安全的方法是利用網路安全性群組。  網路安全性群組可以套用到個別的虛擬機器，或含有虛擬機器的子網路。

首先，您必須建立網路安全性群組：

```azurepowershell-interactive
New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"
```

使用網路安全性群組來限制只有 VNet 內部流量的存取權很簡單。  網路安全性群組中的預設規則只允許從相同虛擬網路中的其他網路用戶端存取。

因此，鎖定 SQL Server 的存取很簡單。 只需將具有預設規則的網路安全性群組套用至執行 SQL Server 的虛擬機器，或包含虛擬機器的子網。

下列範例將網路安全性群組套用至包含的子網路：

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGExample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-1'
```

最終結果是一組安全性規則，可封鎖外部存取，同時允許 VNet 內部存取：

![預設網路安全性規則][DefaultNetworkSecurityRules]

## <a name="getting-started"></a>開始使用
若要開始使用 App Service Environment，請參閱 [App Service Environment 簡介][IntroToAppServiceEnvironment]

如需控制 App Service 環境輸入流量的詳細資訊，請參閱[控制 App Service 環境的輸入流量][ControlInboundASE]

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: ../../virtual-network/virtual-networks-faq.md
[ControlInboundTraffic]:  app-service-app-service-environment-control-inbound-traffic.md
[SiteToSite]: ../../vpn-gateway/vpn-gateway-multi-site.md
[ExpressRoute]: https://azure.microsoft.com/services/expressroute/
[NetworkAccessControlLists]: /previous-versions/azure/virtual-network/virtual-networks-acl
[NetworkSecurityGroups]: ../../virtual-network/virtual-network-vnet-plan-design-arm.md
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md
[ControlInboundASE]:  app-service-app-service-environment-control-inbound-traffic.md

<!-- IMAGES -->
[SqlServerEndpoint]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/SqlServerEndpoint01.png
[NetworkAccessControlListExample]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/NetworkAcl01.png
[DefaultNetworkSecurityRules]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/DefaultNetworkSecurityRules01.png