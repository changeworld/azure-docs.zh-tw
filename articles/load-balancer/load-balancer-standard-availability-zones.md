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
ms.date: 05/07/2020
ms.author: allensu
ms.openlocfilehash: 541aa7da3e804931c1793e455bcbfca83c809dae
ms.sourcegitcommit: 5d7f8c57eaae91f7d9cf1f4da059006521ed4f9f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2020
ms.locfileid: "89669187"
---
# <a name="standard-load-balancer-and-availability-zones"></a>標準 Load Balancer 和可用性區域

Azure Standard Load Balancer 支援可用性區域案例。 您可以使用標準負載平衡器，藉由調整資源和跨區域散發來提高整個案例的可用性。 可用性區域與標準負載平衡器組合是一種廣泛且有彈性的功能集，可建立許多不同的案例。  請檢閱本文以了解這些[概念](#concepts)，以及基本案例的[設計指引](#design)。

## <a name="availability-zones-concepts-applied-to-load-balancer"></a><a name="concepts"></a>適用於 Load Balancer 的可用性區域概念

負載平衡器會從其元件繼承區域設定： 

* 前端
* 規則
* 後端集區定義

在可用性區域的內容中，負載平衡器規則的行為和屬性會描述為區域多餘或區域性。  在負載平衡器的內容中，區域冗余一律表示 **多個區域** 和區域性表示將服務隔離到 **單一區域**。 Azure Load Balancer 有兩種類型：公用和內部。 這兩種類型的負載平衡器都支援區域冗余和區域性部署。  這兩個負載平衡器類型可以視需要將流量導向跨區域。

## <a name="frontend"></a>前端

負載平衡器前端是前端 IP 設定，可參考虛擬網路子網內的公用 IP 位址或私人 IP 位址。 它會構成服務公開所在的負載平衡端點。

負載平衡器資源可以同時包含具有區域性和區域冗余前端的規則。 當公用或私人 IP 保證區域時，區域性 (或缺少它) 不會改變。 若要變更或省略公用或私人 IP 位址前端的區域性，請在適當的區域中重新建立 IP。 

可用性區域不會變更多個前端的條件約束。 如需這項功能的詳細資訊，請參閱 [Load Balancer 的多個前端](load-balancer-multivip-overview.md) 。

### <a name="zone-redundant"></a>區域備援 

在具有可用性區域的區域中，標準負載平衡器前端可以是區域冗余。 多個區域可以服務區域中的輸入或輸出。 此流量是由單一 IP 位址提供服務。 您不需要進行 DNS 備援配置。 

單一前端 IP 位址會存活區域失敗。 前端 IP 可用來觸達所有 (不受影響的) 後端集區成員，不論區域為何。 一或多個可用性區域可能會失敗，而且只要區域中的一個區域維持良好狀況，資料路徑就會繼續生存。 

前端的 IP 位址會由多個可用性區域中的多個獨立基礎結構部署同時提供服務。 任何在不受區域失敗影響的其他區域中，任何重試或重新建立作業都會成功。 

<p align="center">
  <img src="./media/az-zonal/zone-redundant-lb-1.svg" alt="Figure depicts a zone-redundant standard load balancer directing traffic in three different zones to three different subnets in a zone redundant configuration." width="512" title="虛擬網路 NAT">
</p>

*圖：區域重複的負載平衡器*

### <a name="zonal"></a>緯 向

您可以選擇讓某個前端保證屬於單一區域，我們稱之為*區域性前端*。  此案例表示區域中的單一區域會提供任何輸入或輸出流程。  您的前端會具有與區域相同的健康情況。  非保證區域中的失敗皆不會對資料路徑造成影響。 您可以使用區域性前端來公開每個可用性區域的 IP 位址。  

此外，也支援在每個區域內，直接針對負載平衡的端點使用區域性前端。 您可以使用此設定來公開每個區域的負載平衡端點，以個別監視每個區域。 針對公用端點，您可以將它們與 DNS 負載平衡產品（例如 [流量管理員](../traffic-manager/traffic-manager-overview.md) ）整合，並使用單一 DNS 名稱。


<p align="center">
  <img src="./media/az-zonal/zonal-lb-1.svg" alt="Figure depicts three zonal standard load balancers each directing traffic in a zone to three different subnets in a zonal configuration." width="512" title="虛擬網路 NAT">
</p>

*圖：區域性負載平衡器*

如果您想要綜合這些概念 (相同後端的區域備援和區域性)，請檢閱 [Azure Load Balancer 的多個前端](load-balancer-multivip-overview.md)。

針對公用負載平衡器前端，您可以將 **區域** 參數新增至公用 IP。 此公用 IP 是由個別規則所使用的前端 IP 設定所參考。

若為內部負載平衡器前端，請將 **區域** 參數新增至內部負載平衡器前端 IP 設定。 區域性前端可保證子網中特定區域的 IP 位址。

## <a name="backend"></a>後端

Azure Load Balancer 可與虛擬機器實例搭配運作。 這些實例可以是獨立、可用性設定組或虛擬機器擴展集。 單一虛擬網路中的任何虛擬機器都可以是後端集區的一部分，即虛擬機器保證的任何區域。

若要使用單一區域來對齊並保證前端和後端，請只將相同區域內的虛擬機器放入對應的後端集區。

若要解決跨多個區域的虛擬機器，請將多個區域中的虛擬機器放入相同的後端集區。 

使用虛擬機器擴展集時，請將一或多個虛擬機器擴展集放入相同的後端集區。 擴展集可以在單一或多個區域中。

## <a name="outbound-connections"></a>輸出連線

區域冗余和區域性適用于 [輸出](load-balancer-outbound-connections.md)連線。 所有區域都會提供用於輸出連線的區域冗余公用 IP 位址。 區域性公用 IP 位址只會由其保證所在的區域提供。 

輸出連線 SNAT 埠配置存活區域失敗，而如果不受區域失敗影響，您的案例仍會提供輸出 SNAT 連線能力。 如果流量是由受影響的區域所提供，區域故障可能需要重新建立區域冗余案例的連接。 受影響區域以外區域內的流程不會受到影響。

SNAT 埠預先配置演算法與或沒有可用性區域相同。

## <a name="health-probes"></a>健康狀態探查

您現有的健康情況探查定義會保持不變，因為它們沒有可用性區域。 不過，我們已在基礎結構層級擴充健康情況模型。 

使用區域多餘的前端時，負載平衡器會展開其內部健康情況檢查。 負載平衡器會從每個區域獨立探查虛擬機器的可用性，並在不需要介入的情況下，關閉跨區域的路徑。  

其他可存取此 VM 的區域將可繼續從其各自的前端為 VM 提供服務。 在失敗事件期間，每個區域可能會有不同的新流程分佈，同時保護服務的整體健全狀況。

## <a name="design-considerations"></a><a name="design"></a> 設計考慮

負載平衡器在可用性區域的內容中具有彈性。 您可以針對每個規則選擇對齊區域或區域冗余。 提高可用性可能會增加複雜度。 為可用性設計提供最佳效能。

### <a name="zone-redundancy"></a>區域-冗余

Load Balancer 可簡化以單一 IP 作為區域備援前端的作業。 區域冗余 IP 位址可以在任何區域中提供區域性資源。  只要一個區域在區域內保持良好狀態，IP 就可以存留一或多個區域失敗。  相反地，區域前端會將服務縮減為單一區域，並與各自的區域共用 fate。

區域冗余不表示表示資料路徑或控制平面;它是資料平面。 區域備援流量可以使用任何區域，且客戶的流量將會使用區域中所有狀況良好的區域。 在區域失敗中，使用狀況良好的區域的流量不會受到影響。 

在區域失敗時使用區域的流量會受到影響，但應用程式可以復原。 當 Azure 交集區域失敗時，流量會在區域內的狀況良好區域內繼續進行。

### <a name="cross-zone-boundaries"></a><a name="xzonedesign"></a>跨區域界限

請務必瞭解，每當服務跨越區域時，您共用的 fate 不是一個區域，而是可能有多個區域。 因此，您的服務可能尚未獲得非區域部署的任何可用性。

避免引進非預期的跨區域相依性，這會在使用可用性區域時使失效可用性提升。 如果您的應用程式包含多個重要元件，請確保區域失敗時的生存。

如果您的應用程式只存在於存活區域以外的區域 (s) ，您的應用程式的單一重要元件可能會影響整個應用程式。 考慮區域還原，以及您的應用程式將如何融合。 瞭解您的應用程式如何回應部分失敗。 請檢查重點，並在您想要的特定案例中使用這些重點來解決問題。

- 如果您的應用程式有兩個元件：

    * IP 位址
    * 具有受控磁片的虛擬機器

它們是在區域1和區域2中設定。 當區域1失敗時，您的服務將不會存活。 除非您完全瞭解您要建立潛在的危險失敗模式，否則請勿跨區域使用區域性案例。 允許此案例提供彈性。

- 如果您的應用程式有兩個元件：

    * IP 位址
    * 具有受控磁片的虛擬機器

它們已設定為區域冗余和區域1。 除非區域1失敗，否則您的服務將會存活區域2、區域3或兩者的區域失敗。 但是，如果您要觀察到的服務健康狀態是前端的可連線性，您就會失去一些原因。  請考慮開發更大範圍的健康情況和容量模型。  您可以一起使用區域備援和區域性概念來擴充見解與管理能力。

- 如果您的應用程式有兩個元件：

    * 區域-多餘的負載平衡器前端
    * 三個區域中的跨區域虛擬機器擴展集

您將可取得未受失敗影響之區域中的資源。 在失敗期間，您的服務容量可能會降低。 從基礎結構的觀點來看，您的部署可能會在一或多個區域失敗時存活。 此案例會引發下列問題：

  - 您是否瞭解應用程式如何回應失敗和降級的容量？
  - 您的服務中是否需要有保護措施，以在必要時強制容錯移轉至某個區域組？
  - 您將如何監視、偵測並消弭這種案例？ 您可以使用標準負載平衡器診斷來加強服務效能的監視。 請考慮可用的內容，以及可能需要增強的功能。

- 區域有助於您了解及抑制失敗。 發生超時、重試和輪詢演算法時，區域失敗與其他失敗並無不同。 Azure Load Balancer 提供區域冗余路徑。 負載平衡器會在封包層級即時嘗試快速復原。 重新傳輸或 reestablishments 可能會在驗證失敗時發生。 請務必瞭解您的應用程式處理失敗的方式。 您的負載平衡配置將會存留下來，但您必須規劃下列問題：

  - 當區域失敗時，您的服務是否瞭解這項失敗，如果狀態遺失，您將如何復原？
  - 區域回復時，您的應用程式了解如何安全地收斂嗎？

查看 [Azure 雲端設計模式](https://docs.microsoft.com/azure/architecture/patterns/) ，以改善應用程式對失敗案例的復原能力。

## <a name="next-steps"></a>接下來的步驟
- 深入瞭解 [可用性區域](../availability-zones/az-overview.md)
- 深入瞭解 [Standard Load Balancer](load-balancer-standard-overview.md)
- 了解如何[使用具有區域前端的標準 Load Balancer 來進行區域內的 VM 負載平衡](load-balancer-standard-public-zonal-cli.md)
- 了解如何[使用具有區域備援前端的標準 Load Balancer 來進行跨區域的 VM 負載平衡](load-balancer-standard-public-zone-redundant-cli.md)
- 深入瞭解 [Azure 雲端設計模式](https://docs.microsoft.com/azure/architecture/patterns/) ，以改善應用程式對失敗案例的復原能力。
