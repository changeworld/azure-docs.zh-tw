---
title: Azure ExpressRoute：非對稱式路由
description: 本文將逐步解說在有多個連結連至一個目的地的網路中，您可能會遇到的非對稱式路由問題。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 12/14/2020
ms.author: duau
ms.openlocfilehash: 0713c52c7f07db93d9ae9084062ef2c3b25a9074
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/16/2020
ms.locfileid: "97560503"
---
# <a name="asymmetric-routing-with-multiple-network-paths"></a>非對稱式路由與多個網路路徑
本文說明在網路來源與目的地之間有多個路由時，網路流量可能會如何採取不同的路徑。

您必須知道兩個概念，才能瞭解非對稱式路由。 第一個是多重網路路徑的影響。 另一個則是裝置的方式，例如防火牆如何保持狀態。 這些裝置類型稱為具狀態裝置。 結合這兩個因素後，就可以建立一種案例，讓具狀態裝置丟棄網路流量。  因為流量未偵測到來自本身的流量，所以會中斷。

## <a name="multiple-network-paths"></a>多個網路路徑
當商業網路透過網際網路服務提供者只有一個網際網路連結時，進出網際網路的所有流量都會傳送相同的路徑。 公司通常會購買多個線路來建立重複的路徑，以改善網路執行時間。 使用這種類型的設定時，流量可能會輸出到網際網路的一個連結，並透過不同的連結來返回。 此案例通常稱為非對稱式路由。 在非對稱式路由中，傳回的網路流量會從原始輸出流程取得不同的路徑。

![具有多個路徑的網路](./media/expressroute-asymmetric-routing/AsymmetricRouting3.png)

雖然非對稱式路由通常會在進入網際網路時發生。 當引入多個路徑的組合時，也會發生這種情況。 第一個範例是當您的網際網路路徑和私用路徑移至相同的目的地時。 第二個範例是當您有多個同時也會前往相同目的地的私用路徑時。

在來源和目的地之間的路徑中，每個路由器都會計算到達目的地所要採取的最佳路徑。 路由器會根據兩個主要因素判斷最可能的路徑：

* 外部網路之間的路由是以「邊界閘道通訊協定」(BGP) 路由通訊協定為基礎。 BGP 會接受芳鄰的通告並經由一連串的步驟來執行這些通告，以決定到達預定目的地的最佳路徑。 它會在其路由表中儲存最佳路徑。
* 與路由相關聯的子網路遮罩的長度會影響路由路徑。 如果路由器收到相同 IP 位址的多個公告，則路由器會選取具有較長子網路遮罩的路徑，因為這會被視為更特定的路由。

## <a name="stateful-devices"></a>具狀態裝置
路由器會為了進行路由傳送而查看封包的 IP 標頭。 有些裝置看起來在封包的更深處。 一般而言，這些裝置會查看第4層-傳輸控制通訊協定 (TCP) 或使用者資料包協定 (UDP) ，甚至是第7層 (應用層) 標頭。 這幾種裝置不是安全性裝置，就是頻寬最佳化裝置。 

防火牆是具狀態裝置的常見範例。 防火牆允許或拒絕封包根據各種準則傳遞其介面。 這些準則包括但不限於通訊協定、TCP/UDP 埠和 URL 標頭。 這一層的封包檢查可能會對裝置造成大量的處理負載。 

為了改善效能，防火牆會檢查流程的第一個封包。 如果允許封包通過其介面，則會將流程資訊保留在其狀態資料表中。 然後，會根據初始判斷，允許任何與此流程相關的後續封包。 屬於現有流程一部分的封包，可能會在其不是源自的防火牆抵達。 因為它沒有初始流程的先前狀態資訊，防火牆會捨棄封包。

## <a name="asymmetric-routing-with-expressroute"></a>非對稱式路由與 ExpressRoute
當您透過 Azure ExpressRoute 連接到 Microsoft 時，您的網路會發生下列變更：

* 您有 Microsoft 的多個連結。 其中一個連結是您現有的網際網路連線，另一個則是透過您的 ExpressRoute 連接。 目的地為 Microsoft 的特定流量可能會通過網際網路連線，但會傳回您的 ExpressRoute 連線。 當流量通過 ExpressRoute，但透過網際網路路徑傳回時，也會發生相同的情況。
* 您從 ExpressRoute 線路收到更明確的 IP 位址。 因此，當來自您網路的流量移至 Microsoft，以透過 ExpressRoute 提供的服務時，您的路由器一律會偏好使用 ExpressRoute 連線。

若要瞭解這兩項變更對網路的影響，讓我們考慮一些案例。 例如，您有網際網路的線路，而您透過網際網路使用所有 Microsoft 服務。 從您的網路到 Microsoft 和來自 Microsoft 的流量會通過相同的網際網路連結，並通過防火牆。 防火牆會在看到第一個封包時記錄流程。 允許該交談的每個後續封包，因為該流程存在於狀態資料表中。

![非對稱式路由與 ExpressRoute](./media/expressroute-asymmetric-routing/AsymmetricRouting1.png)

然後，您會帶出 ExpressRoute 線路，以取用 Microsoft 透過 ExpressRoute 提供的服務。 Microsoft 的所有其他服務都是透過網際網路取用。 您可以在連線到 ExpressRoute 連線的邊緣上部署個別的防火牆。 Microsoft 會針對特定服務，透過 ExpressRoute 向您的網路通告更明確的首碼。 您的路由基礎結構會選擇 ExpressRoute 做為這些前置詞的慣用路徑。 

如果您未透過 ExpressRoute 向 Microsoft 公告公用 IP 位址。 Microsoft 會透過網際網路與您的公用 IP 位址進行通訊。 從您的網路傳送到 Microsoft 的流量會使用 ExpressRoute 連線，但來自 Microsoft 的退回流量接著會使用網際網路路徑。 當邊緣的防火牆看到不知道的流程的回應封包時，它會捨棄這些封包。

如果您選擇公告相同的網路位址轉譯 (適用于 ExpressRoute 和網際網路的 NAT) 集區。 您會發現您的網路中的用戶端有類似的私人 IP 位址問題。 對服務（例如 Windows Update）的要求會通過網際網路，因為這些服務的 IP 位址不會透過 ExpressRoute 公告。 不過，傳回的流量會透過 ExpressRoute 回傳。 因為 Microsoft 收到的 IP 位址具有與網際網路和 ExpressRoute 相同的子網路遮罩，所以慣用的路徑一律為 ExpressRoute。 如果您的網路邊緣上的防火牆或其他具狀態裝置對 ExpressRoute 連線沒有任何先前的流程相關資訊，則會捨棄這些封包。

## <a name="asymmetric-routing-solutions"></a>非對稱式路由解決方案
您有兩個可用的選項，可解決非對稱式路由的問題。 第一個是透過路由，第二個是使用以來源為基礎的 NAT (SNAT) 。

### <a name="routing"></a>路由
請確定您的公用 IP 位址已公告至適當的廣域網路 (WAN) 連結。 例如，如果您想要使用網際網路進行驗證流量，並使用 ExpressRoute 來傳送郵件流量。 請勿 (透過 ExpressRoute AD FS) 公用 IP 位址來公告您的 Active Directory 同盟服務。 此外，請務必不要將內部部署 AD FS 伺服器公開到路由器透過 ExpressRoute 接收的 IP 位址。 透過 ExpressRoute 收到的路由更加明確，所以會讓 ExpressRoute 成為 Microsoft 驗證流量的慣用路徑。 如果您不想要如何在網路非對稱式路由問題中進行路由傳送，請注意。

如果您想要使用 ExpressRoute 進行驗證，請確定您在沒有 NAT 的情況下，透過 ExpressRoute 通告 AD FS 的公用 IP 位址。 以這種方式設定時，來自 Microsoft 的流量會移至您的內部部署 AD FS server 將會透過 ExpressRoute。 從網路傳送到 Microsoft 的流量會使用 ExpressRoute，因為它是透過網際網路的慣用路由。

### <a name="source-based-nat"></a>以來源為基礎的 NAT
解決非對稱式路由問題的另一種方法是使用 SNAT。 例如，您選擇不將內部部署 Simple Mail Transfer Protocol 的公用 IP 位址， (SMTP) server 透過 ExpressRoute 來公告。 相反地，您想要使用網際網路進行這種類型的通訊。 源自 Microsoft 的要求會前往您的內部部署 SMTP 伺服器來進行網際網路。 您會對內部 IP 位址的連入要求進行 NAT 處理。 從 SMTP 伺服器傳回的流量將會移至您用於 NAT) 的邊緣防火牆 (，而不是透過 ExpressRoute。 因此，傳回的流量將會採用網際網路路徑。

![以來源為基礎的 NAT 網路組態](./media/expressroute-asymmetric-routing/AsymmetricRouting2.png)

## <a name="asymmetric-routing-detection"></a>非對稱式路由偵測
路徑追蹤是確保網路流量會周遊預期路徑的最佳方式。 如果您預期從內部部署 SMTP 伺服器至 Microsoft 的流量會採用網際網路路徑，則預期的追蹤路由是從 SMTP 伺服器 Microsoft 365。 結果會驗證流量確實讓您的網路朝向網際網路，而不是向 ExpressRoute。

