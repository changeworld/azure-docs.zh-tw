---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 04/15/2020
ms.author: ccompy
ms.openlocfilehash: f7208307df51ecefb76f9adaedea59b327cdc19e
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604890"
---
使用區域 VNet 整合使用套用的應用程式可以存取:

* 與應用位於同一區域的 VNet 中的資源。
* VNet 中的資源與應用整合的 VNet 進行對等。
* 服務終結點保護服務。
* 跨 Azure 快速路由連接的資源。
* 您要整合的 VNet 中的資源。
* 跨對等連接的資源,其中包括 Azure 快速路由連接。
* 專用終結點 

在同一區域中使用 VNet 整合時,可以使用以下 Azure 網路功能:

* **網路安全組 (NSG):** 您可以使用放置在整合子網上的 NSG 來阻止出站流量。 入站規則不適用,因為無法使用 VNet 整合來提供對應用的入站訪問許可權。
* **路由表 (UDR):** 您可以在整合子網上放置路由表,以便將出站流量發送到所需的位置。

默認情況下,你的應用僅將 RFC1918 流量路由到 VNet 中。 如果要將所有出站流量路由到 VNet 中,請應用應用應用WEBSITE_VNET_ROUTE_ALL應用。 要設定應用程式設定:

1. 轉到應用門戶中的 **「配置**UI」。。 選擇**新應用程式設定**。
1. 在 **「名稱**」框中輸入**WEBSITE_VNET_ROUTE_ALL,** 並在 **「值」** 框中輸入**1。**

   ![提供應用程式設定][4]

1. 選取 [確定]  。
1. 選取 [儲存]  。

如果將所有出站流量路由到 VNet,則受應用於整合子網的 NSG 和 UDR 的約束。 當您將所有出站流量路由到 VNet 時,您的出站位址仍然是應用屬性中列出的出站位址,除非您提供路由將流量發送到其他位置。

在同一區域中使用 VNet 整合與 VNet 存在一些限制:

* 無法跨全域對等互連連接到達資源。
* 此功能僅從支援先進 V2 應用服務計畫的較新的 Azure 應用服務擴展單位提供。
* 集成子網只能由一個應用服務計劃使用。
* 此功能不能由應用服務環境中的隔離計劃應用使用。
* 此功能需要一個未使用的子網,該子網是具有 32 個位址或 Azure 資源管理器 VNet 中更大的 /27。
* 應用和 VNet 必須位於同一區域中。
* 不能刪除具有整合應用的 VNet。 刪除 VNet 之前刪除集成。
* 您只能與 VNet 整合在與應用相同的訂閱中。
* 每個應用服務計畫只能有一個區域 VNet 整合。 同一應用服務計劃中的多個應用可以使用相同的 VNet。
* 當有一個應用使用區域 VNet 整合時,不能更改應用或計劃的訂閱。
* 如果不進行設定更改,你的應用程式無法解決 Azure DNS 專用區域中的位址

每個計劃實例使用一個位址。 如果將應用縮放為五個實例,則使用五個位址。 由於子網大小在分配后無法更改,因此必須使用足夠大的子網,以適應應用可能達到的任何比例。 建議的尺寸是帶 64 個位址的 /26。 帶 64 個位址的 /26 適合包含 30 個實例的高級計劃。 向上或向下擴展計劃時,需要在短時間內使用兩倍的位址。

如果希望其他計畫中的應用訪問已由另一個計畫中的應用連接到的 VNet,請選擇與預先存在的 VNet 整合使用的子網不同的子網。

此功能在 Linux 預覽中。 該功能的 Linux 形式僅支援對 RFC 1918 位址(10.0.0.0/8、172.16.0.0/12、192.168.0.0/16) 進行調用。

### <a name="web-or-function-app-for-containers"></a>容器的 Web 或功能套用

如果您在 Linux 上使用內建映射託管應用,則區域 VNet 整合無需其他更改即可工作。 如果對容器使用 Web 或函數應用,則必須修改 Docker 映射才能使用 VNet 整合。 在 docker 映像中,使用 PORT 環境變數作為主 Web 伺服器的偵聽埠,而不是使用硬編碼埠號。 PORT 環境變數由平臺在容器啟動時自動設置。 如果使用 SSH,則必須將 SSH 守護程序配置為在使用區域 VNet 整合時偵聽 SSH_PORT 環境變數指定的埠號。 在 Linux 上不支援閘道要求的 VNet 整合。

### <a name="service-endpoints"></a>服務端點

區域 VNet 整合讓您能夠使用服務終結點。 要將服務終結點與應用一起使用,請使用區域 VNet 整合連接到選定的 VNet,然後使用用於整合的子網上的目標服務配置服務終結點。 如果然後想要通過服務終結點存取服務:

1. 設定與 Web 應用的區域 VNet 整合
1. 轉到目標服務,並針對用於整合的子網設定服務終結點

### <a name="network-security-groups"></a>網路安全性群組

您可以使用網路安全組來阻止 VNet 中資源的入站和出站流量。 使用區域 VNet 整合的應用可以使用[網路安全組][VNETnsg]來阻止向外出流量到 VNet 或 Internet 中的資源。 要阻止公共位址的流量,必須將應用程式設置WEBSITE_VNET_ROUTE_ALL設置為 1。 NSG 中的入站規則不適用於你的應用,因為 VNet 集成僅影響應用中的出站流量。

要控制應用的入站流量,請使用"訪問限制"功能。 應用於整合子網的 NSG 都有效,無論應用於整合子網的任何路由如何。 如果WEBSITE_VNET_ROUTE_ALL設置為 1,並且集成子網上沒有任何影響公共位址流量的路由,則所有出站流量仍受分配給集成子網的 NSG 的約束。 如果未設置WEBSITE_VNET_ROUTE_ALL,則 NSG 僅應用於 RFC1918 流量。

### <a name="routes"></a>路由

您可以使用路由表將出站流量從應用路由到所需的任何位置。 默認情況下,路由表僅影響 RFC1918 目標流量。 如果將WEBSITE_VNET_ROUTE_ALL設置為 1,則所有出站呼叫都受到影響。 在整合子網上設置的路由不會影響對入站應用請求的回覆。 常見目標可以包括防火牆設備或閘道。

如果要路由本地所有出站流量,可以使用路由表將所有出站流量發送到 ExpressRoute 閘道。 如果確實將流量路由到閘道,請確保在外部網路中設置路由以發送回任何答覆。

邊境閘道協定 (BGP) 路由也會影響應用流量。 如果您有來自快速路由閘道等內容的 BGP 路由,則應用出站流量將受到影響。 默認情況下,BGP 路由僅影響您的 RFC1918 目標流量。 如果WEBSITE_VNET_ROUTE_ALL設置為 1,則所有出站流量都可能受 BGP 路由的影響。

### <a name="azure-dns-private-zones"></a>Azure DNS 專用區域 

應用與 VNet 整合後,它將使用 VNet 配置的相同 DNS 伺服器。 默認情況下,你的應用將不能與 Azure DNS 專用區域配合使用。 要使用 Azure DNS 專用區域,您需要新增以下應用設定:

1. 值 168.63.129.16 的WEBSITE_DNS_SERVER 
1. 值 1 的WEBSITE_VNET_ROUTE_ALL

除了使應用能夠使用 Azure DNS 專用區域外,這些設置還將將應用的所有出站呼叫發送到 VNet。

### <a name="private-endpoints"></a>專用終結點

如果要對[專用終結點][privateendpoints]進行調用,則需要與 Azure DNS 專用區域集成,或者管理應用使用的 DNS 伺服器中的專用終結點。 

<!--Image references-->
[4]: ../includes/media/web-sites-integrate-with-vnet/vnetint-appsetting.png

<!--Links-->
[VNETnsg]: https://docs.microsoft.com/azure/virtual-network/security-overview/
[privateendpoints]: https://docs.microsoft.com/azure/app-service/networking/private-endpoint
