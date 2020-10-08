---
title: 自動調整規模和區域備援應用程式閘道 v2
description: 本文介紹 Azure 應用程式 Standard_v2 和 WAF_v2 SKU，其包含自動調整和區域備援功能。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 06/06/2020
ms.author: victorh
ms.custom: fasttrack-edit, references_regions
ms.openlocfilehash: 8c989e426faa77025a84515fe0a19424cb6bfa89
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/07/2020
ms.locfileid: "91826628"
---
# <a name="autoscaling-and-zone-redundant-application-gateway-v2"></a>自動調整規模和區域備援應用程式閘道 v2 

應用程式閘道可在 Standard_v2 SKU 下取得。 WAF_v2 SKU 下提供 Web 應用程式防火牆 (WAF) 。 v2 SKU 提供效能增強功能，並新增自動調整、區域備援和靜態 VIP 支援等重要新功能的支援。 除了＜[比較](#differences-from-v1-sku)＞一節中所列的幾個例外項目之外，新 v2 SKU 會繼續支援標準和 WAF SKU 下的現有功能。

新的 v2 SKU 包含下列增強功能：

- 自動**調整：自動**調整 SKU 下的應用程式閘道或 WAF 部署可以根據變動的流量負載模式來相應放大或縮小。 自動調整規模也可讓您在佈建時，無須選擇部署大小或執行個體計數。 此 SKU 可提供真正的靈活彈性。 在 Standard_v2 和 WAF_v2 SKU 中，「應用程式閘道」可在固定容量 (停用自動調整) 模式下運作，也可在啟用自動調整模式下運作。 固定容量模式適用於工作負載一致且可預測的案例。 自動調整模式則有利於在應用程式流量中可看到變異的應用程式。
- **區域備援**：「應用程式閘道」或 WAF 部署可跨多個「可用性區域」，因此無須使用流量管理員在每個區域中佈建個別的「應用程式閘道」執行個體。 您可選擇已部署「應用程式閘道」執行個體的單一或多個區域，以便更能在區域失敗後復原。 應用程式的後端集區可以均等分散在可用性區域間。

  區域備援僅適用於可使用 Azure 區域的位置。 在其他區域中，則支援所有其他功能。 如需詳細資訊，請參閱 [Azure 中的區域和可用性區域](../availability-zones/az-overview.md)
- **靜態 VIP**：應用程式閘道 v2 SKU 現在獨家支援靜態 VIP 類型。 這可確保與應用程式閘道建立關聯的 VIP 即使在重新啟動之後，在部署的存留期間也不會變更。  v1 中沒有靜態 VIP，因此必須使用應用程式閘道 URL，而不是使用網域名稱的 IP 位址來透過應用程式閘道路由到應用程式服務。
- **標頭重寫**：應用程式閘道可供使用 v2 SKU 來新增、移除或更新 HTTP 要求和回應標頭。 如需詳細資訊，請參閱[使用應用程式閘道來重寫 HTTP 標題](rewrite-http-headers.md)
- **金鑰保存庫整合**：對於在已啟用 HTTPS 的接聽程式中所附加伺服器認證，應用程式閘道 v2 支援與金鑰保存庫整合。 如需詳細資訊，請參閱[具備金鑰保存庫認證的 TLS 終止](key-vault-certs.md)。
- **Azure Kubernetes Service 輸入控制器**：應用程式閘道 v2 輸入控制器可讓 Azure 應用程式閘道作為 Azure Kubernetes Service (AKS) 輸入，稱為 AKS 叢集。 如需詳細資訊，請參閱[什麼是應用程式閘道輸入控制器？](ingress-controller-overview.md)。
- **效能增強功能**：相較於標準/WAF SKU，v2 SKU 提供最多 5 倍的 TLS 卸載效能提升。
- **更快的佈建和更新速度**：與標準/WAF SKU 相比，v2 SKU 提供更快的佈建和更新速度。 這也包括 WAF 設定變更。

![自動調整區域的圖表。](./media/application-gateway-autoscaling-zone-redundant/application-gateway-autoscaling-zone-redundant.png)

## <a name="supported-regions"></a>支援區域

Standard_v2 和 WAF_v2 SKU 可在下列區域取得：美國中北部、美國中南部、美國西部、美國西部 2、美國東部、美國東部 2、美國中部、北歐、西歐、東南亞、法國中部、英國西部、日本東部、日本西部、澳大利亞東部、澳大利亞東南部、巴西南部、加拿大中部、加拿大東部、東亞、韓國中部、韓國南部、英國南部、印度中部、印度西部、印度南部。

## <a name="pricing"></a>定價

使用 v2 SKU，定價模式是以耗用量來驅動，不再附加至執行個體計數或大小。 v2 SKU 定價有兩個元件：

- **固定價格** - 這是佈建 Standard_v2 或 WAF_v2 閘道的每小時 (或部分小時) 價格。 請注意，0 個額外的最低執行個體仍會確保服務高可用性，這一律包含於固定價格。
- **容量單位價格** - 這是以耗用量計費的成本，且加計固定成本。 容量單位費用也是依每小時或部分小時的方式計算。 容量單位有三個維度：計算單位、持續連線數和輸送量。 計算單位是已耗用多少處理器容量的量值。 影響計算單位的因素包括 TLS 連線數/秒、URL 重寫計算，以及 WAF 規則處理。 持續連線數是在指定的計費期間，對應用程式閘道建立的 TCP 連線數量值。 「輸送量」是在指定的計費期間，系統處理的平均 MB/秒。  計費是針對保留執行個體計數以上的任何項目，在容量單位層級上完成。

每個容量單位最多包含：1個計算單位、2500個持續連線和 2.22-Mbps 的輸送量。

若要深入瞭解，請參閱 [瞭解定價](understanding-pricing.md)。

## <a name="scaling-application-gateway-and-waf-v2"></a>調整應用程式閘道和 WAF v2

應用程式閘道和 WAF 可設定為在兩種模式中調整︰

- **自動調整** - 啟用自動調整後，應用程式閘道和 WAF v2 SKU 會根據應用程式流量需求相應增加或減少。 此模式可為應用程式提供更好的彈性，並免於猜測應用程式閘道大小或執行個體計數。 此模式也可供節省成本，因為閘道不需要以尖峰佈建容量執行，以應付預期的最大流量負載。 您必須指定最小執行個體計數並選擇性地指定最大執行個體計數。 最小容量可確保應用程式閘道和 WAF v2 不會低於所指定的最小執行個體計數 (即使是在沒有流量的情況下)。 每個執行個體大約等於 10 個額外的保留容量單位。 零表示沒有保留容量，且在本質上純粹是自動調整。 您也可以選擇性地指定最大執行個體計數，以確保應用程式閘道不會擴增超過指定的執行個體數目。 您只需支付閘道所服務流量的費用。 執行個體計數的範圍可從 0 到 125。 如果未指定，則最大執行個體計數的預設值為 20。
- **手動** - 您也可以選擇 [手動] 模式，閘道將不會自動調整。 在此模式中，如果流量超過應用程式閘道或 WAF 可處理的流量，可能會導致流量遺失。 使用手動模式時，必須指定執行個體計數。 執行個體計數的變更範圍可能從 1 到 125。

## <a name="autoscaling-and-high-availability"></a>自動調整和高可用性

Azure 應用程式閘道一律會以高可用性的方式部署。 此服務是由多個執行個體所構成，其建立時會依照設定 (如果已停用自動調整)，或依應用程式負載所需 (如果已啟用自動調整)。 請注意，從使用者的觀點來看，您不一定會看到個別的執行個體，而只會看到應用程式閘道服務整體。 如果某個執行個體發生問題並停止運作，Azure 應用程式閘道將會以透明的方式建立新執行個體。

請注意，即使您以零個最少執行個體來設定自動調整，服務仍會是高可用性，這一律會包含在固定價格中。

不過，建立新的執行個體可能需要一些時間 (大約六到七分鐘)。 因此，如果不想要處理此停機時間，您可將最小執行個體計數設定為 2，這最適合可用性區域支援。 如此一來，在正常情況下， Azure 應用程式閘道內至少會有兩個執行個體，因此如果其中一個發生問題，則在建立新執行個體時，另一個會嘗試處理流量。 請注意，Azure 應用程式閘道執行個體可支援大約 10 個容量單位，因此，建議根據通常有的流量，將最小執行個體自動調整設定值設為高於 2 的值。

## <a name="feature-comparison-between-v1-sku-and-v2-sku"></a>v1 SKU 與 v2 SKU 之間的功能比較

下表將比較各個 SKU 的可用功能。

| 特徵                                           | v1 SKU   | v2 SKU   |
| ------------------------------------------------- | -------- | -------- |
| 自動調整                                       |          | &#x2713; |
| 區域備援                                   |          | &#x2713; |
| 靜態 VIP                                        |          | &#x2713; |
| Azure Kubernetes Service (AKS) 輸入控制器 |          | &#x2713; |
| Azure 金鑰保存庫整合                       |          | &#x2713; |
| 重寫 HTTP(S) 標頭                           |          | &#x2713; |
| URL 型路由                                 | &#x2713; | &#x2713; |
| 多網站裝載                             | &#x2713; | &#x2713; |
| 流量重新導向                               | &#x2713; | &#x2713; |
| Web 應用程式防火牆 (WAF)                    | &#x2713; | &#x2713; |
| WAF 自訂規則                                  |          | &#x2713; |
| 傳輸層安全性 (TLS)/安全通訊端層 (SSL) 終止            | &#x2713; | &#x2713; |
| 端對端 TLS 加密                         | &#x2713; | &#x2713; |
| 工作階段親和性                                  | &#x2713; | &#x2713; |
| 自訂錯誤頁面                                | &#x2713; | &#x2713; |
| WebSocket 支援                                 | &#x2713; | &#x2713; |
| HTTP/2 支援                                    | &#x2713; | &#x2713; |
| 清空連線                               | &#x2713; | &#x2713; |

> [!NOTE]
> 自動調整 v2 SKU 現在支援[預設健康狀態探查](application-gateway-probe-overview.md#default-health-probe)，以自動監視其後端集區中所有資源的健康狀態，並突顯狀況不良的後端成員。 預設健康狀態探查會針對沒有任何自訂探查設定的後端自動設定。 若要深入了解，請參閱[應用程式閘道中的健康狀態探查](application-gateway-probe-overview.md)。

## <a name="differences-from-v1-sku"></a>與 v1 SKU 的差異

本節描述 v2 SKU 與 v1 SKU 不同的功能和限制。

|差異|詳細資料|
|--|--|
|驗證憑證|不支援。<br>如需詳細資訊，請參閱[應用程式閘道端對端 TLS 的概觀](ssl-overview.md#end-to-end-tls-with-the-v2-sku)。|
|在相同子網路上混合使用 Standard_v2 和標準應用程式閘道|不支援|
|應用程式閘道子網路上的使用者定義路由 (UDR)|支援 (特殊案例)。 處於預覽狀態。<br> 如需支援案例的詳細資訊，請參閱[應用程式閘道設定概觀](configuration-infrastructure.md#supported-user-defined-routes)。|
|輸入連接埠範圍的 NSG| - Standard_v2 SKU 適用 65200 至 65535<br>- 標準 SKU 適用 65503 至 65534<br>如需詳細資訊，請參閱[常見問題集](application-gateway-faq.md#are-network-security-groups-supported-on-the-application-gateway-subnet)。|
|Azure 診斷中的效能記錄|不支援。<br>應該使用 Azure 計量。|
|計費|排定於 2019 年 7 月 1 日開始計費。|
|FIPS 模式|目前不支援。|
|純 ILB 模式|目前不支援。 支援將公用與 ILB 模式搭配使用。|
|網路監看員整合|不支援。|
|Azure 資訊安全中心整合|尚未提供。

## <a name="migrate-from-v1-to-v2"></a>從 v1 遷移至 v2

PowerShell 資源庫中有提供 Azure PowerShell 指令碼，可協助從 v1 應用程式閘道/WAF 遷移至 v2 自動調整 SKU。 此指令碼可協助從 v1 閘道複製設定。 流量遷移仍是您的責任。 如需詳細資訊，請參閱[將 Azure 應用程式閘道從 v1 遷移至 v2](migrate-v1-v2.md)。

## <a name="next-steps"></a>後續步驟

- [快速入門：使用 Azure 應用程式閘道引導網路流量 - Azure 入口網站](quick-create-portal.md)
- [使用 Azure PowerShell 建立具有保留虛擬 IP 位址的自動調整規模、區域備援應用程式閘道](tutorial-autoscale-ps.md)
- 深入了解[應用程式閘道](overview.md)。
- 深入了解 [Azure 防火牆](../firewall/overview.md)。
