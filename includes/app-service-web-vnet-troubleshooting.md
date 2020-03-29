---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: 2d2a82552a846cedfa5da3bb6ec6df8a40b67732
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671527"
---
儘管功能易於設定，這不表示您的體驗不會遇到問題。 如果您在存取所需端點時遇到問題，有一些公用程式，您可以用來從應用程式主控台測試連線功能。 有兩個您可以使用的主控台。 一個是 Kudu 主控台，另一個則是 Azure 入口網站中的主控台。 若要從您的應用程式觸達 Kudu 主控台，請移至 [工具] -> [Kudu]。 您還可以在 [網站名稱]scm.azuresite.net 上訪問 Kudo 主控台。 網站載入後，轉到調試主控台選項卡。要訪問 Azure 門戶託管主控台，請從應用轉到工具 ->主控台。 

#### <a name="tools"></a>工具
由於安全限制 **，ping、nslook****和** **tracert**的工具無法通過主控台工作。 為了填補空白，添加了兩個單獨的工具。 為了測試 DNS 功能，已加入名為 nameresolver.exe的工具。 語法為：

    nameresolver.exe hostname [optional: DNS Server]

您可以使用 **nameresolver** 來檢查應用程式依賴的主機名稱。 如此一來您可以測試是否有任何項目未正確設定，而無法與 DNS 搭配，或可能對 DNS 伺服器沒有存取權。 通過查看環境變數WEBSITE_DNS_SERVER和WEBSITE_DNS_ALT_SERVER，可以看到應用將在主控台中使用的 DNS 伺服器。

下一個工具可讓您測試主機是否可與 TCP 連線，以及連接埠組合。 此工具稱為 **tcpping**，語法如下：

    tcpping.exe hostname [optional: port]

**cpping**實用程式告訴您是否可以到達特定的主機和埠。 只有在下列情況才會顯示成功：有一個應用程式監聽主機和連接埠組合，並且有網路從您的應用程式存取到指定的主機和連接埠。

#### <a name="debugging-access-to-vnet-hosted-resources"></a>偵錯 VNet 裝載資源的存取
有一些事物，可以阻止您的應用程式觸達特定的主機和連接埠。 大部分的情況下，會是下列三個情況的其中一種︰

* **路線中有防火牆。** 如果路線中有防火牆，則將會達到 TCP 逾時。 此案例中 TCP 逾時值就是 21 秒。 使用 **tcpping** 工具來測試連線能力。 TCP 逾時可能是因為許多項目越過防火牆並啟動。 
* **DNS 無法存取。** DNS 逾時值為三秒 (每部 DNS 伺服器)。 如果您有兩部 DNS 伺服器，逾時為 6 秒。 使用 nameresolver 來檢查 DNS 是否運作。 請記住，您不能使用 nslookup，因為它不會使用您的 VNet 設定使用的 DNS。 如果無法訪問，則可能會有防火牆或 NSG 阻止對 DNS 的訪問，或者該防火牆或 NSG 可能會關閉。

如果這些專案不能回答您的問題，請首先查找諸如： 

**區域 VNet 集成**
* 您的目的地是否為非 RFC1918 位址，而您沒有將WEBSITE_VNET_ROUTE_ALL設置為 1？
* 是否有 NSG 阻止來自集成子網的退出？
* 如果通過 ExpressRoute 或 VPN，您的本地閘道是否配置為將流量路由回 Azure？ 如果可以到達 VNet 中的終結點，但不能到達本地，請檢查路由。
* 您是否有足夠的許可權在集成子網上設置委派？ 在區域 VNet 集成配置期間，集成子網將委派給 Microsoft.Web。 VNet 集成 UI 將自動將子網委派給 Microsoft.Web。 如果您的帳戶沒有足夠的網路許可權來設置委派，則需要可以在集成子網上設置屬性的人員來委派子網。 要手動委派集成子網，請訪問 Azure 虛擬網路子網 UI 並為 Microsoft.Web 設置委派。 

**閘道需要 VNet 集成**
* RFC 1918 範圍（10.0.0.0-10.255.255/ 172.16.0.0-172.31.255.255 / 192.168.0-192.168.255.255）中的點到網站位址範圍嗎？
* 閘道是否顯示為正在入口網站中啟動？ 如果您的閘道已關閉，請重新啟動。
* 證書是否顯示為同步，或者您懷疑網路設定已更改？  如果您的證書不同步，或者您懷疑 VNet 配置發生了未與 ASP 同步的更改，則點擊"同步網路"。
* 如果穿過 VPN，是否配置本地閘道將流量路由回 Azure？ 如果可以到達 VNet 中的終結點，但不能到達本地，請檢查路由。
* 您是否正在嘗試使用同時支援指向網站和 ExpressRoute 的共存閘道？ VNet 集成不支援共存閘道。

調試網路問題是一個挑戰，因為在那裡您看不到阻止對特定主機：埠組合的訪問。 部分原因包括：

* 主機上安裝了防火牆，阻止從點到網站 IP 範圍訪問應用程式埠。 跨子網路通常需要公用存取權。
* 目標主機已關閉。
* 您的應用程式已關閉。
* 您擁有錯誤的 IP 或主機名稱。
* 您的應用程式偵聽的埠與預期不同。 您可以在端點主機上使用 "netstat -aon"，將處理序識別碼與接聽連接埠進行比對。 
* 網路安全性群組的配置方式使網路安全性群組無法阻止從點到網站 IP 範圍訪問應用程式主機和埠。

請記住，您不知道你的應用實際將使用什麼位址。 它可以是集成子網或點對點位址範圍內的任何位址，因此您需要允許從整個位址範圍進行訪問。 

其他偵錯步驟包括：

* 連接到 VNet 中的 VM 並嘗試從那裡訪問資源主機：埠。 若要測試 TCP 存取，請使用 PowerShell 命令 **test-netconnection**。 語法為：

      test-netconnection hostname [optional: -Port]

* 在 VM 上啟動應用程式，並使用**tcpping**從應用的主控台測試對該主機和埠的訪問

#### <a name="on-premises-resources"></a>內部部署資源 ####

如果您的應用程式無法連接內部部署資源，則請檢查您是否可以從 VNet 連接資源。 使用 **test-netconnection** PowerShell 命令來檢查是否有 TCP 存取權。 如果您的 VM 無法訪問本地資源，則 VPN 或 ExpressRoute 連接可能未正確配置。

如果 VNet 裝載的 VM 可以連接您的內部部署系統，但您的應用程式無法連接此系統，則很可能是下列其中一個原因：

* 您的路由未配置與本地閘道中的子網或指向網站位址範圍。
* 您的網路安全性群組正在阻止對網站 IP 範圍的訪問。
* 本地防火牆正在阻止來自點對點 IP 範圍的流量。
* 您正在嘗試使用區域 VNet 集成功能訪問非 RFC 1918 位址。