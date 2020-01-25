---
title: Azure Standard Load Balancer 和可用性區域
titleSuffix: Azure Load Balancer
description: 透過此學習路徑，開始使用 Azure Standard Load Balancer 和可用性區域。
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/07/2019
ms.author: allensu
ms.openlocfilehash: 0d61ad33b97b97c3a45334704544d72809e56848
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2020
ms.locfileid: "76715265"
---
# <a name="standard-load-balancer-and-availability-zones"></a>標準 Load Balancer 和可用性區域

Azure Standard Load Balancer 支援[可用性區域](../availability-zones/az-overview.md)案例。 您可以使用 Standard Load Balancer，藉由將資源與區域進行對齊，並將它們分散到不同區域，以將您的端對端案例中的可用性優化。  請參閱[可用性區域](../availability-zones/az-overview.md)，以取得可用性區域、目前支援可用性區域的區域，以及其他相關概念和產品的指引。 可用性區域與 Standard Load Balancer 結合，是一種大規模且彈性的功能集，可以建立許多不同的案例。  請檢閱本文以了解這些[概念](#concepts)，以及基本案例的[設計指引](#design)。

>[!IMPORTANT]
>請參閱相關主題的[可用性區域](../availability-zones/az-overview.md)，包括任何區域特定資訊。

## <a name="concepts"></a>適用於 Load Balancer 的可用性區域概念

Load Balancer 資源與實際的基礎結構之間沒有直接關聯性；建立 Load Balancer 並不會建立執行個體。 Load Balancer 資源是一種物件，而您可以在其中表示 Azure 應如何對其預先建置的多租用戶基礎結構進行設計程式，以達到您想要建立的案例。  這在可用性區域的內容中很重要，因為單一 Load Balancer 資源可以控制多個可用性區域中的基礎結構程式設計，而區域冗余服務會從客戶的觀點呈現為一個資源。  

Load Balancer 資源本身是區域的，且絕不具區域性。  VNet 和子網路則一律是區域的，且絕不具區域性。 您可以設定的資料細微性會受到前端、規則和後端集區定義的每個設定所限制。

在可用性區域的內容中，Load Balancer 規則的行為和屬性會描述為區域多餘或區域性。  區域備援和區域性說明屬性的區域性。  在 Load Balancer 的內容中，區域冗余一律表示*多個區域*，而區域性則表示將服務隔離至*單一區域*。

公用和內部的 Load Balancer 均支援區域備援和區域性案例，且兩者都可視需要在區域之間進行流量導向 (「跨區域負載平衡」)。 

### <a name="frontend"></a>前端

Load Balancer 前端是一種前端 IP 設定，它會參考虛擬網路資源子網內的公用 IP 位址資源或私人 IP 位址。  它會構成服務公開所在的負載平衡端點。

Load Balancer 資源可以包含同時具有區域性和區域冗余前端的規則。 

當公用 IP 資源或私人 IP 位址已保證為區域時，區域性（或缺乏）無法變動。  如果您想要變更或省略公用 IP 或私人 IP 位址前端的區域性，您必須在適當的區域中重新建立公用 IP。  可用性區域不會變更多個前端的條件約束，請參閱[Load Balancer 的多個前端](load-balancer-multivip-overview.md)，以取得這項功能的詳細資料。

#### <a name="zone-redundant-by-default"></a>預設具有區域備援功能

在具有可用性區域的區域中，Standard Load Balancer 前端預設為區域多餘的。  區域冗余表示區域中的多個可用性區域，會同時使用單一 IP 位址來提供所有的輸入或輸出流量。 您不需要進行 DNS 備援配置。 單一前端 IP 位址可能會存活區域失敗，而且可以用來觸及所有（不受影響的）後端集區成員，而不論該區域。 一或多個可用性區域可能會失敗，而且只要區域中的一個區域維持良好狀況，資料路徑就會不受。 前端的單一 IP 位址會由多個可用性區域中的多個獨立基礎結構部署同時提供服務。  這並不表示表示資料路徑，但任何重試或重新建立作業將會在不受區域失敗影響的其他區域中成功。   

下列摘要說明如何定義公用 IP，以用於您的公用 Standard Load Balancer 的區域多餘公用 IP 位址。 如果您在組態中使用現有的 Resource Manager 範本，請將 **sku** 區段新增到這些範本。

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "public_ip_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
```

下列摘要說明如何為您的內部 Standard Load Balancer 定義區域多餘的前端 IP 位址。 如果您在組態中使用現有的 Resource Manager 範本，請將 **sku** 區段新增到這些範本。

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/loadBalancers",
            "name": "load_balancer_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
            "properties": {
                "frontendIPConfigurations": [
                    {
                        "name": "zone_redundant_frontend",
                        "properties": {
                            "subnet": {
                                "Id": "[variables('subnetRef')]"
                            },
                            "privateIPAddress": "10.0.0.6",
                            "privateIPAllocationMethod": "Static"
                        }
                    },
                ],
```

前面的摘錄不是完整的範本，而是為了示範如何表達可用性區域屬性。  您必須將這些語句併入您的範本中。

#### <a name="optional-zone-isolation"></a>選擇性區域隔離

您可以選擇讓某個前端保證屬於單一區域，我們稱之為*區域性前端*。  這表示任何輸入或輸出流量都會由區域中的單一區域提供服務。  您的前端會具有與區域相同的健康情況。  非保證區域中的失敗皆不會對資料路徑造成影響。 您可以使用區域性前端來公開每個可用性區域的 IP 位址。  

此外，您可以直接對每個區域內的負載平衡端點使用區域性前端。 您也可以使用此前端公開每個區域的負載平衡端點，以個別監視每個區域。  針對公用端點，您可以將它們與 DNS 負載平衡產品（例如[流量管理員](../traffic-manager/traffic-manager-overview.md)）整合，並使用單一 DNS 名稱。 接著，用戶端會將此 DNS 名稱解析為多個區域性 IP 位址。  

如果您想要綜合這些概念 (相同後端的區域備援和區域性)，請檢閱 [Azure Load Balancer 的多個前端](load-balancer-multivip-overview.md)。

針對公用 Load Balancer 前端，您可以將*區域*參數新增至個別規則所使用的前端 IP 設定所參考的公用 ip 資源。

對於內部 Load Balancer 前端，請將*區域*參數新增至內部 Load Balancer 前端 IP 組態。 區域性前端會使 Load Balancer 保證子網路中的某個 IP 位址屬於特定區域。

下列摘要說明如何定義可用性區域1中的區域性標準公用 IP 位址。 如果您在組態中使用現有的 Resource Manager 範本，請將 **sku** 區段新增到這些範本。

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "public_ip_standard",
            "location": "region",
            "zones": [ "1" ],
            "sku":
            {
                "name": "Standard"
            },
```

下列摘要說明如何在可用性區域1中定義內部 Standard Load Balancer 前端。 如果您在組態中使用現有的 Resource Manager 範本，請將 **sku** 區段新增到這些範本。 此外，也請定義子資源之前端 IP 組態中的 **zones** 屬性。

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/loadBalancers",
            "name": "load_balancer_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
            "properties": {
                "frontendIPConfigurations": [
                    {
                        "name": "zonal_frontend_in_az1",
                        "zones": [ "1" ],
                        "properties": {
                            "subnet": {
                                "Id": "[variables('subnetRef')]"
                            },
                            "privateIPAddress": "10.0.0.6",
                            "privateIPAllocationMethod": "Static"
                        }
                    },
                ],
```

前面的摘錄不是完整的範本，而是為了示範如何表達可用性區域屬性。  您必須將這些語句併入您的範本中。

### <a name="cross-zone-load-balancing"></a>跨區域負載平衡

跨區域負載平衡是 Load Balancer 可觸達任何區域中的後端端點，並與前端及其區域性無關的能力。  任何負載平衡規則都可以將任何可用性區域或區域實例中的後端實例作為目標。

您必須小心以表示可用性區域概念的方式來建立您的案例。 例如，您需要保證單一區域或多個區域內的虛擬機器部署，並將區域性前端和區域性後端資源對應至相同區域。  如果您的跨可用性區域僅具有區域性資源，則此案例將可運作，但可能不會有與可用性區域相關的明確失敗模式。 

### <a name="backend"></a>後端

Load Balancer 適用于虛擬機器實例。  這些可以是獨立、可用性設定組或虛擬機器擴展集。  單一虛擬網路中的任何虛擬機器實例都可以是後端集區的一部分，而不論其是否保證為區域或保證的區域。

如果您想要以單一區域對齊並保證您的前端和後端，只需將相同區域內的虛擬機器放置在個別的後端集區中。

如果您想要跨多個區域處理虛擬機器，只要將虛擬機器從多個區域放入相同的後端集區即可。  使用虛擬機器擴展集時，您可以將一或多個虛擬機器擴展集放入相同的後端集區中。  而且，每個虛擬機器擴展集可位於單一或多個區域中。

### <a name="outbound-connections"></a>輸出連線

相同的區域冗余和區域性屬性會套用至[輸出](load-balancer-outbound-connections.md)連線。  用於輸出連線的區域多餘公用 IP 位址是由所有區域提供服務。 區域公用 IP 位址只能由其保證所在的區域提供。  輸出連線 SNAT 埠配置會存活區域失敗，而如果不受區域失敗影響，您的案例將會繼續提供輸出 SNAT 連線能力。  如果流程是由受影響的區域所服務，這可能需要傳輸或針對區域冗余案例重新建立連接。  在受影響區域以外的區域中的流程不會受到影響。

SNAT 埠預先配置演算法與或不含可用性區域相同。

### <a name="health-probes"></a>健康狀態探查

您現有的健康情況探查定義會維持不變，但不含可用性區域。  不過，我們已在基礎結構層級擴充健康情況模型。 

使用區域冗余前端時，Load Balancer 擴充其內部健康情況模型，以獨立探查每個可用性區域中虛擬機器的可連線性，並關閉可能已失敗的區域之間的路徑，而不需要客戶介入。  如果某個區域的 Load Balancer 基礎結構無法使用指定的路徑至另一個區域中的虛擬機器，Load Balancer 可以偵測並避免此失敗。 其他可存取此 VM 的區域將可繼續從其各自的前端為 VM 提供服務。  因此，在失敗事件期間，每個區域可能會有稍微不同的新流程散發，同時保護您端對端服務的整體健全狀況。

## <a name="design"></a>設計考量

Load Balancer 在可用性區域的內容中是刻意彈性的。 您可以選擇對齊區域，也可以選擇每個規則的區域-多餘。  增加可用性可能會導致複雜性隨之提高，因此您的可用性設計必須顧及最佳效能。  以下我們將討論一些重要的設計考量。

### <a name="automatic-zone-redundancy"></a>自動區域備援

Load Balancer 可簡化以單一 IP 作為區域備援前端的作業。 區域備援 IP 位址可安全地為任何區域中的區域性資源提供服務，且在一或多個區域失敗時仍可存留，只要區域內有一個區域維持良好狀況即可。 相反地，區域性前端則是將服務縮減為單一區域，且個別的區域將會有共同的結果。

區域備援不代表無中斷的資料路徑或控制平面；它實際上是資料平面。 區域備援流量可以使用任何區域，且客戶的流量將會使用區域中所有狀況良好的區域。 如果區域失敗，在該時間點使用良好區域的流量並不會受到影響。  在區域失敗時使用區域的流量可能會受到影響，但應用程式可以復原。 當 Azure 已將區域故障交集時，這些流程可以在重新傳輸或重新建立作業區域內的剩餘狀況良好區域中繼續進行。

### <a name="xzonedesign"></a>跨區域界限

請務必了解，無論何時，使用跨區域的端對端服務都不會產生來自單一區域的結果，而是可能來自多個區域的結果。  因此，您的端對端服務可能無法透過非區域性部署獲得任何可用性。

避免導入非預期的跨區域相依性，這會在使用可用性區域時使失效可用性提升。  如果您的應用程式包含多個元件，而您想要有發生區域失敗後的復原能力，您必須謹慎確保有足夠的重要元件能夠在發生區域失敗時存留。  例如，如果應用程式的單一重要元件僅存在於存留區域以外的一個區域中，就可能對整個應用程式造成影響。  此外，也請考慮區域還原以及應用程式的收斂方式。 您必須瞭解應用程式在其部分失敗方面的影響。 接下來我們將回顧一些要點，藉以激發您在思考特定案例所應提出的問題。

- 如果您的應用程式有兩個元件，例如 IP 位址和具有受控磁片的虛擬機器，且可在區域1和區域2中保證，當區域1失敗時，您的端對端服務將無法存留。  除非您完全瞭解您正在建立可能危險的失敗模式，否則請不要跨區域使用區域性案例。  此案例允許提供彈性。

- 如果您的應用程式有兩個元件，例如 IP 位址和具有受控磁片的虛擬機器，且其保證分別為區域冗余和區域1，則您的端對端服務將會在區域2、區域3的區域失敗，或兩者都存在，除非區域1失敗。  不過，如果您所關注的都只是前端的可存取性，您研判服務健康情況的能力就會削弱。  請考慮開發更大範圍的健康情況和容量模型。  您可以一起使用區域備援和區域性概念來擴充見解與管理能力。

- 如果您的應用程式在三個區域中有兩個元件 (例如，區域備援 Load Balancer 前端和跨區域虛擬機器擴展集)，則在區域失敗期間，不受失敗影響之區域中的資源仍將可供使用，但您的端對端服務容量可能會降低。 從基礎結構的觀點而言，您的部署在歷經一或多個區域失敗後仍可存留，而這就產生了下列問題：
  - 您是否了解應用程式如何判讀這類失敗和降低的容量？
  - 您的服務中是否需要有保護措施，以在必要時強制容錯移轉至某個區域組？
  - 您將如何監視、偵測並消弭這種案例？ 您可以使用標準 Load Balancer 診斷，以加強您對於端對端服務效能的監視。 請考量可用項目以及可能需要增強的層面，以勾勒完整藍圖。

- 區域有助於您了解及抑制失敗。  不過，就逾時、重試和輪詢演算法等概念而言，區域失敗與其他失敗並無不同。 即使 Azure Load Balancer 提供區域備援路徑，而且會嘗試在封包層級即時快速復原，但在失敗發生時，仍可能會執行重新傳輸或重新建立，因此請務必了解您的應用程式處理失敗的方式。 您的負載平衡配置將繼續存留，但您必須進行下列規劃：
  - 當區域失敗時，您的端對端服務是否了解此狀況？且在狀態遺失時，您將如何復原？
  - 區域回復時，您的應用程式了解如何安全地收斂嗎？

請參閱[Azure 雲端設計模式](https://docs.microsoft.com/azure/architecture/patterns/)，以改善應用程式對失敗案例的復原能力。

### <a name="zonalityguidance"></a>區域備援與區域性的比較

區域冗余可以提供簡單區域的選項，並使用與服務的單一 IP 位址相同的時間彈性選項。  複雜性也可因而降低。  區域備援也具有跨區域的行動性，並可以安全地用於任何區域中的資源。  此外，它會在沒有可用性區域的區域中進行未來的證明，這可能會在區域取得可用性區域之後，限制所需的變更。  區域冗余 IP 位址或前端的設定語法會在任何區域中成功，包括沒有可用性區域的位置：未在資源的區域：屬性中指定區域。

區域性可以為區域提供明確的保證，並明確地與區域的健康情況共用 fate。 如果您的連結資源是相同區域中的區域性虛擬機器，則建立具有區域 IP 位址前端或區域性內部 Load Balancer 前端的 Load Balancer 規則可能是理想的做法。  或者，您的應用程式可能需要事先知道資源的所在區域，而您想要明確瞭解個別區域中的可用性。  您可以選擇將端對端服務的多個區域性前端分散公開於各區域間 (也就是，每個區域各有多個區域性虛擬機器擴展集的區域性前端)。  如果您的區域性前端是公用 IP 位址，您可以透過 [Traffic Manager](../traffic-manager/traffic-manager-overview.md) 使用這幾個區域性前端來公開您的服務。  或者，您可以使用多個區域性前端透過第三方監視解決方案取得個別區域的健康情況和效能深入資訊，並使用區域備援前端公開整體的服務。 您僅應為區域性資源提供與相同區域對應的區域性前端，並避免將可能有害的跨區域案例用於區域性資源。  只有存在可用性區域的區域中才會有區域性資源。

在未了解服務架構的情況下，並沒有通則可說明哪個選擇較為理想。  請參閱[Azure 雲端設計模式](https://docs.microsoft.com/azure/architecture/patterns/)，以改善應用程式對失敗案例的復原能力。

## <a name="next-steps"></a>後續步驟
- 深入了解[可用性區域](../availability-zones/az-overview.md)
- 深入了解[標準 Load Balancer](load-balancer-standard-overview.md)
- 了解如何[使用具有區域前端的標準 Load Balancer 來進行區域內的 VM 負載平衡](load-balancer-standard-public-zonal-cli.md)
- 了解如何[使用具有區域備援前端的標準 Load Balancer 來進行跨區域的 VM 負載平衡](load-balancer-standard-public-zone-redundant-cli.md)
- 瞭解[Azure 雲端設計模式](https://docs.microsoft.com/azure/architecture/patterns/)，以改善應用程式對失敗案例的復原能力。
