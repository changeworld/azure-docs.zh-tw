---
title: Azure 應用程式閘道的健全狀況監視概觀
description: Azure 應用程式閘道會監視其後端集區中所有資源的健全狀況，並自動從集區中移除任何被視為狀況不良的資源。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 02/20/2020
ms.author: victorh
ms.openlocfilehash: c5a53167c6a4ca6c886b858a1608eaa173185bd8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80335852"
---
# <a name="application-gateway-health-monitoring-overview"></a>應用程式閘道健全狀況監視概觀

Azure 應用程式閘道預設會監視其後端集區中所有資源的健康狀況，並自動從集區中移除任何被視為狀況不良的資源。 應用程式閘道會繼續監視狀況不良的執行個體，一旦其恢復可用狀態並回應健康狀況探查，就會將其新增回狀況良好後端集區中。 應用程式閘道會以後端 HTTP 設定中定義的相同連接埠傳送健康狀態探查。 此組態可確保探查所測試的連接埠會和客戶用來連接到後端的連接埠相同。 

應用程式閘道用於健康情況探查的來源 IP 位址取決於後端集區：
 
- 如果後端集區是公用端點，則來源位址會是應用程式閘道前端公用 IP 位址。
- 如果後端集區是私人端點，則來源 IP 位址是來自應用程式閘道子網的私人 IP 位址空間。


![應用程式閘道探查範例][1]

除了使用預設的健全狀況探查監視，您也可以自訂健全狀況探查，以符合應用程式的需求。 本文會探討預設和自訂健全狀態探查。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="default-health-probe"></a>預設的健全狀況探查

如果您沒有設定任何自訂探查組態，應用程式閘道就會自動設定預設健全狀況探查。 監視行為的運作方式是對後端集區所設定的 IP 位址提出 HTTP 要求。 針對預設探查，如果後端 http 設定設為使用 HTTPS，探查也會使用 HTTPS 測試後端的健康狀態。

例如：您將應用程式閘道設定為使用 A、B 和 C 後端伺服器來接收連接埠 80 上的 HTTP 網路流量。 預設健全狀況監視每 30 秒就會對三部伺服器進行一次測試，以取得狀況良好的 HTTP 回應。 狀況良好的 HTTP 回應具有 200 到 399 之間的 [狀態碼](https://msdn.microsoft.com/library/aa287675.aspx) 。

如果伺服器 A 的預設探查檢查失敗，應用程式閘道就會將其從後端集區中移除，網路流量也不會再流向此伺服器。 預設探查仍會繼續每 30 秒檢查一次伺服器 A。 當伺服器 A 成功回應預設健康情況探查中的一個要求時，會將其新增回後端集區的狀況良好，然後流量會再次開始流向伺服器。

### <a name="probe-matching"></a>探查比對

根據預設，狀態碼介於200和399之間的 HTTP （S）回應視為狀況良好。 自訂的健全狀況探查額外支援兩個比對準則。 比對準則可用於選擇性地修改做出狀況良好回應的預設轉譯。

以下為比對準則： 

- **HTTP 回應狀態碼比對**：探查比對準則，以接受使用者指定的 HTTP 回應碼或回應碼範圍。 支援以逗號分隔的個別回應狀態碼或一個狀態碼範圍。
- **HTTP 回應主體比對**：探查比對準則，其會查看 HTTP 回應主體並與使用者指定的字串進行比對。 比對只會在回應主體中尋找是否有使用者指定的字串，而不是完整的正則運算式相符。

比對準則是使用 `New-AzApplicationGatewayProbeHealthResponseMatch` Cmdlet 來指定的。

例如：

```azurepowershell
$match = New-AzApplicationGatewayProbeHealthResponseMatch -StatusCode 200-399
$match = New-AzApplicationGatewayProbeHealthResponseMatch -Body "Healthy"
```
一旦指定比對準則之後，就可使用 PowerShell 中的 `-Match` 參數，將它附加至探查設定。

### <a name="default-health-probe-settings"></a>預設的健全狀況探查設定

| 探查屬性 | 值 | 描述 |
| --- | --- | --- |
| 探查 URL |http://127.0.0.1:\<port\>/ |URL 路徑 |
| 間隔 |30 |在傳送下一個健康情況探查之前的等候時間，以秒為單位。|
| 逾時 |30 |應用程式閘道在將探查標示為狀況不良之前等待探查回應的時間，以秒為單位。 如果傳回狀況良好的探查，則對應的後端會立即標示為狀況良好。|
| 狀況不良臨界值 |3 |控制在正常健康情況探查失敗時要傳送的探查數目。 這些額外的健康情況探查會快速地連續傳送，以快速判斷後端的健康狀態，而不會等待探查間隔。 此 behaivor 僅適用于 v1 SKU。 在 v2 SKU 的案例中，健康情況探查會等候間隔。 連續探查失敗計數到達狀況不良臨界值後，就會將後端伺服器標示為故障。 |

> [!NOTE]
> 連接埠會是和後端 HTTP 設定相同的連接埠。

預設探查只會查看 HTTP：\//127.0.0.1：\<埠\> ，以判斷健全狀況狀態。 如果您需要設定健康情況探查，使其移至自訂 URL 或修改任何其他設定，則必須使用自訂探查。

### <a name="probe-intervals"></a>探查間隔

所有應用程式閘道執行個體會彼此獨立地探查後端。 每個應用程式閘道執行個體都會套用相同的探查組態。 例如，如果探查組態是每 30 秒傳送健康情況探查一次，而應用程式閘道具有兩個執行個體，則這兩個執行個體都每 30 秒傳送健康情況探查一次。

此外，如果有多個接聽程式，則每個接聽程式會彼此獨立地探查後端。 例如，如果有兩個接聽程式在兩個不同的連接埠上指向相同的後端集區 (由兩個後端 HTTP 設定所設定)，則這兩個接聽程式會獨立地探查相同的後端。 在此情況下，每個應用程式閘道執行個體會有兩個探查，分別來自兩個接聽程式。 如果在相同的情況下有兩個應用程式閘道執行個體，則後端虛擬機器在每個設定的探查間隔內將會看到四個探查。

## <a name="custom-health-probe"></a>自訂的健全狀況探查

自訂探查可讓您更細微地控制健全狀況監視。 使用自訂探查時，您可以設定探查間隔、要測試的 URL 和路徑，以及在將後端集區執行個體標示為狀況不良前，可接受的失敗回應次數。

### <a name="custom-health-probe-settings"></a>自訂的健全狀況探查設定

下表提供自訂健全狀況探查的屬性定義。

| 探查屬性 | 描述 |
| --- | --- |
| 名稱 |探查的名稱。 此名稱用來在後端 HTTP 設定中指出探查。 |
| 通訊協定 |用來傳送探查的通訊協定。 探查會使用後端 HTTP 設定中定義的通訊協定 |
| 主機 |用來傳送探查的主機名稱。 只有當應用程式閘道上設定多站台時適用，否則請使用 '127.0.0.1'。 此值與 VM 主機名稱不同。 |
| Path |探查的相對路徑。 有效路徑的開頭為 '/'。 |
| 間隔 |探查間隔 (秒)。 這個值是兩個連續探查之間的時間間隔。 |
| 逾時 |探查逾時 (秒)。 如果在此超時期間內未收到有效的回應，則會將探查標示為失敗。  |
| 狀況不良臨界值 |探查重試計數。 連續探查失敗計數到達狀況不良臨界值後，就會將後端伺服器標示為故障。 |

> [!IMPORTANT]
> 如果已將應用程式閘道設定為單一站台，根據預設，除非已在自訂探查中加以設定，否則應將主機名稱指定為 '127.0.0.1'。
> 僅供參考，自訂探查會傳送到 \<通訊協定\>://\<主機\>:\<連接埠\>\<路徑\>。 所使用的連接埠會是和後端 HTTP 設定中所定義者相同的連接埠。

## <a name="nsg-considerations"></a>NSG 考量

您必須允許應用程式閘道 v1 SKU 的 TCP 埠65503-65534 上的連入網際網路流量，以及使用目的地子網做為**Any**和 Source as **GatewayManager**服務標籤的 v2 sku 的 tcp 埠65200-65535。 Azure 基礎結構通訊需要此連接埠範圍。

此外，無法封鎖輸出網際網路連線，且必須允許來自**AzureLoadBalancer**標記的輸入流量。

如需詳細資訊，請參閱[應用程式閘道設定概觀](configuration-overview.md#network-security-groups-on-the-application-gateway-subnet)。

## <a name="next-steps"></a>後續步驟
在了解應用程式閘道的健全狀態監視之後，您可以在 Azure 入口網站中[自訂健全狀態探查](application-gateway-create-probe-portal.md)，或使用 PowerShell 和 Azure Resource Manager 部署模型設定[自訂健全狀態探查](application-gateway-create-probe-ps.md)。

[1]: ./media/application-gateway-probe-overview/appgatewayprobe.png
