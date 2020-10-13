---
title: 控制輸入流量 v1
description: 瞭解如何控制 App Service 環境的輸入流量。 本文件僅提供給使用舊版 v1 ASE 的客戶。
author: ccompy
ms.assetid: 4cc82439-8791-48a4-9485-de6d8e1d1a08
ms.topic: article
ms.date: 01/11/2017
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: fe9326ea9ebd5afe981b7ba6c34b1a5d51e084b0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88962055"
---
# <a name="how-to-control-inbound-traffic-to-an-app-service-environment"></a>如何控制 App Service 環境的輸入流量
## <a name="overview"></a>概觀
App Service 環境 **可以在 Azure Resource Manager** 虛擬網路 **或** 傳統部署模型 [虛擬網路][virtualnetwork]中建立。  建立 APP Service 環境時，可以定義新的虛擬網路和新的子網路。 相反地，您可以在既有的虛擬網路和預先存在的子網中建立 App Service 環境。  從2016年6月起，Ase 也可以部署到使用公用位址範圍或 RFC1918 位址空間 (私人位址) 的虛擬網路。  如需詳細資訊，請參閱 [如何建立 App Service 環境][HowToCreateAnAppServiceEnvironment]。

一律在子網內建立 App Service 環境。 子網會提供網路界限，可用來鎖定上游裝置和服務後方的輸入流量。 此設定只允許特定的上游 IP 位址接受 HTTP 和 HTTPS 流量。

您可以使用[網路安全性群組][NetworkSecurityGroups]來控制子網路上的輸入和輸出網路流量。 若要控制輸入流量，請在網路安全性群組中建立網路安全性規則。 然後，將包含 App Service 環境的子網指派給網路安全性群組。

一旦將網路安全性群組指派給子網，就會根據網路安全性群組中定義的允許和拒絕規則，允許或封鎖 App Service 環境中的應用程式輸入流量。

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="inbound-network-ports-used-in-an-app-service-environment"></a>App Service Environment 中使用的輸入網路連接埠
在您鎖定網路安全性群組的輸入網路流量之前，請先知道 App Service 環境所使用的必要和選用網路埠集合。  意外關閉送至某些連接埠的流量，可能會導致在 App Service 環境中喪失功能。

下列清單包含 App Service 環境所使用的埠。 除非明確註明，否則所有連接埠為 **TCP**：

* 454： Azure 基礎結構用來透過 TLS 管理及維護 App Service 環境  **所需的埠** 。  不要封鎖此埠的流量。  此連接埠一律繫結至 ASE 的公用 VIP。
* 455： Azure 基礎結構用來透過 TLS 管理及維護 App Service 環境  **所需的埠** 。  不要封鎖此埠的流量。  此連接埠一律繫結至 ASE 的公用 VIP。
* 80：對於在 App Service 環境的 App Service 方案中執行的應用程式，其輸入 HTTP 流量的預設連接埠。  在啟用 ILB 的 ASE 上，此連接埠繫結至 ASE 的 ILB 位址。
* 443：在 App Service 環境的 App Service 方案中執行的應用程式之輸入 TLS 流量的預設埠。  在啟用 ILB 的 ASE 上，此連接埠繫結至 ASE 的 ILB 位址。
* 21：FTP 的控制通道。  如果未使用 FTP，就可以安全地封鎖此埠。  在啟用 ILB 的 ASE 上，此連接埠可以繫結至 ASE 的 ILB 位址。
* 990：FTPS 的控制通道。  如果未使用 FTPS，可以安全地封鎖此埠。  在啟用 ILB 的 ASE 上，此連接埠可以繫結至 ASE 的 ILB 位址。
* 10001-10020：FTP 的資料通道。  如同控制通道，如果未使用 FTP，就可以安全地封鎖這些埠。  在啟用 ILB 的 ASE 上，此連接埠可以繫結至 ASE 的 ILB 位址。
* 4016：用於 Visual Studio 2012 的遠端偵錯。  如果未使用該功能，就可以安全地封鎖此埠。  在啟用 ILB 的 ASE 上，此連接埠繫結至 ASE 的 ILB 位址。
* 4018：用於 Visual Studio 2013 的遠端偵錯。  如果未使用該功能，就可以安全地封鎖此埠。  在啟用 ILB 的 ASE 上，此連接埠繫結至 ASE 的 ILB 位址。
* 4020：用於 Visual Studio 2015 的遠端偵錯。  如果未使用該功能，就可以安全地封鎖此埠。  在啟用 ILB 的 ASE 上，此連接埠繫結至 ASE 的 ILB 位址。

## <a name="outbound-connectivity-and-dns-requirements"></a>輸出連線和 DNS 需求
若要讓 App Service Environment 正確運作，它還需要不同端點的輸出存取權。 [ExpressRoute 的網路組態](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity) 文章的＜需要的網路連線＞一節中有提供 ASE 所使用的外部端點完整清單。

App Service 環境需要針對虛擬網路設定的有效 DNS 基礎結構。  如果 DNS 設定在建立 App Service 環境之後變更，開發人員可以強制 App Service 環境挑選新的 DNS 設定。  如果您使用 [ **重新開機** ] 圖示觸發輪流環境重新開機，則環境會挑選新的 DNS 設定。  (**重新開機** 圖示位於 [App Service 環境管理] 分頁頂端的 [ [Azure 入口網站][NewPortal]] 中。 ) 

此外，也建議您事先設定 vnet 上的任何自訂 DNS 伺服器，然後再建立 App Service 環境。  如果虛擬網路的 DNS 設定在建立 App Service 環境期間有所變更，則 App Service 環境建立程式將會失敗。  同樣地，如果在 VPN 閘道的另一端無法連線或無法使用自訂 DNS 伺服器，App Service 環境建立程式也會失敗。

## <a name="creating-a-network-security-group"></a>建立網路安全性群組
如需有關網路安全性群組如何運作的完整詳細資料，請參閱下列[資訊][NetworkSecurityGroups]。  以下的 Azure 服務管理範例會討論網路安全性群組的重點。 此範例會設定網路安全性群組，並將其套用至包含 App Service 環境的子網。

**注意：** 您可以使用 [Azure 入口網站](https://portal.azure.com) 或透過 Azure PowerShell，以圖形方式設定網路安全性群組。

網路安全性群組首次會建立為與訂用帳戶相關聯的獨立實體。 由於網路安全性群組是在 Azure 區域中建立的，因此請在與 App Service 環境相同的區域中建立網路安全性群組。

下列命令示範如何建立網路安全性群組：

```azurepowershell-interactive
New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"
```

建立網路安全性群組後，會加入一或多個網路安全性規則。  因為規則集可能會隨著時間而變更，所以您應該將用於規則優先順序的編號配置變成空間。 這種作法可讓您輕鬆地在一段時間內插入額外的規則。

在下列範例中，規則會明確授與 Azure 基礎結構所需管理埠的存取權，以管理及維護 App Service 環境。  所有的管理流量都會流經 TLS，並受到用戶端憑證的保護。 即使埠已開啟，但 Azure 管理基礎結構以外的任何實體都無法存取。

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP
```

當您鎖定埠80和443的存取權以「隱藏」上游裝置或服務後方的 App Service 環境時，請記住上游 IP 位址。  例如，如果您使用 web 應用程式防火牆 (WAF) ，則 WAF 將會有自己的 IP 位址或位址。 當將流量 proxy 處理至下游 App Service 環境時，WAF 會使用它們。  您必須在網路安全性規則的 *SourceAddressPrefix* 參數中使用此 IP 位址。

在下面範例中，明確允許來自特定上游 IP 位址的輸入流量。  位址 *1.2.3.4* 會做為上游 WAF 的 IP 位址預留位置。  變更此值，以符合您的上游裝置或服務所使用的位址。

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTP" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTPS" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP
```

如果需要 FTP 支援，請使用下列規則作為範本，以授與 FTP 控制埠和資料通道埠的存取權。  因為 FTP 是具狀態的通訊協定，所以您可能無法透過傳統 HTTP/HTTPS 防火牆或 proxy 裝置來路由 FTP 流量。  在此情況下，您必須將 *SourceAddressPrefix* 設定為不同的值，例如，執行 FTP 用戶端之開發人員或部署電腦的 IP 位址範圍。 

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPCtrl" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '21' -Protocol TCP
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPDataRange" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '10001-10020' -Protocol TCP
```

(**注意**：資料通道連接埠範圍可能會在預覽期間變更。)

如果使用 Visual Studio 遠端偵錯，則下列規則會示範如何授與存取權。  每個支援的 Visual Studio 版本都有個別的規則，因為每個版本都會使用不同的埠進行遠端偵錯程式。  如同 FTP 存取，遠端偵錯流量可能不會透過傳統 WAF 或 Proxy 裝置正確傳送。  可將 *SourceAddressPrefix* 改為設定成執行 Visual Studio 之開發人員電腦的 IP 位址範圍。

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2012" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4016' -Protocol TCP
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2013" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4018' -Protocol TCP
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2015" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4020' -Protocol TCP
```

## <a name="assigning-a-network-security-group-to-a-subnet"></a>將網路安全性群組指派給子網路
網路安全性群組具有預設安全性規則，可拒絕所有外部流量的存取。 當您結合此規則與上述網路安全性規則時，只有來自與 [ *允許* ] 動作相關聯之來源位址範圍的流量，才能將流量傳送至在 App Service 環境中執行的應用程式。

當網路安全性群組填入安全性規則之後，請將它指派給包含 App Service 環境的子網。  指派命令會參考兩個名稱： App Service 環境所在的虛擬網路名稱，以及建立 App Service 環境之子網的名稱。  

下列範例顯示要指派給子網路和虛擬網路的網路安全性群組：

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'
```

指派作業是長時間執行的作業，可能需要幾分鐘的時間才能完成。 一旦網路安全性群組指派成功之後，只有符合 *允許* 規則的輸入流量會成功抵達 App Service 環境中的應用程式。

為了完整起見，下列範例會示範如何從子網中移除和取消關聯網路安全性群組：

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Remove-AzureNetworkSecurityGroupFromSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'
```

## <a name="special-considerations-for-explicit-ip-ssl"></a>明確 IP-SSL 的特殊考量
如果應用程式是以明確的 IP SSL 位址設定 (*僅* 適用于具有公用 VIP) 的 ase，而不是使用 App Service 環境的預設 IP 位址，則 HTTP 和 HTTPS 流量都會透過埠80和443以外的埠流向子網中。

若要尋找每個 IP SSL 位址使用的個別埠配對，請移至入口網站，並查看 App Service 環境的詳細資料 UX 分頁。  選取 [**所有設定**]  >  **IP 位址**。  [ **IP 位址** ] 分頁會顯示 App Service 環境的所有明確設定 IP SSL 位址的表格。 此分頁也會顯示特殊的埠組，用來路由與每個 IP SSL 位址相關聯的 HTTP 和 HTTPS 流量。  在網路安全性群組中設定規則時，請對 DestinationPortRange 參數使用此埠組。

當 ASE 上的應用程式設定為使用 IP SSL 時，外部客戶將不會看到或需要擔心特殊的埠配對對應。  應用程式流量會正常流向設定的 IP-SSL 位址。  在路由傳送流量的最後一個階段至包含 ASE 的子網期間，會自動在內部進行特殊埠組的轉譯。 

## <a name="getting-started"></a>開始使用
若要開始使用 App Service 環境，請參閱 [App Service 環境簡介][IntroToAppServiceEnvironment]。

如需詳細資訊，請參閱 [從 App Service 環境安全地連接到後端資源][SecurelyConnecttoBackend]。

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: ../../virtual-network/virtual-networks-faq.md
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md
[NetworkSecurityGroups]: ../../virtual-network/virtual-network-vnet-plan-design-arm.md
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md
[SecurelyConnecttoBackend]:  app-service-app-service-environment-securely-connecting-to-backend-resources.md
[NewPortal]:  https://portal.azure.com  

<!-- IMAGES -->