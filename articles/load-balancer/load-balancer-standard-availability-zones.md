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
ms.openlocfilehash: 55a86eeee4f819955e3f8adfcc0f55f24d58bed0
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87420306"
---
# <a name="standard-load-balancer-and-availability-zones"></a>標準 Load Balancer 和可用性區域

Azure Standard Load Balancer 支援可用性區域案例。 您可以使用標準負載平衡器，藉由將資源與分散在不同區域，以提高整個案例的可用性。 可用性區域結合標準負載平衡器是一種大規模且彈性的功能集，可以建立許多不同的案例。  請檢閱本文以了解這些[概念](#concepts)，以及基本案例的[設計指引](#design)。

## <a name="availability-zones-concepts-applied-to-load-balancer"></a><a name="concepts"></a>適用於 Load Balancer 的可用性區域概念

負載平衡器會從其元件繼承區域設定： 

* 前端
* 規則
* 後端集區定義

在可用性區域的內容中，負載平衡器規則的行為和屬性會描述為區域多餘或區域性。  在負載平衡器的內容中，區域冗余一律表示**多個區域**，而區域性則表示將服務隔離至**單一區域**。 Azure Load Balancer 有兩種類型： [公用] 和 [內部]。 這兩種類型的負載平衡器都支援區域冗余和區域性部署。  這兩個負載平衡器類型可以視需要將流量導向各個區域。

## <a name="frontend"></a>前端

負載平衡器前端是一種前端 IP 設定，它會參考虛擬網路子網內的公用 IP 位址或私人 IP 位址。 它會構成服務公開所在的負載平衡端點。

負載平衡器資源可以包含同時具有區域性和區域冗余前端的規則。 當公用或私人 IP 保證為區域時，區域性（或缺少它）不會專案 read-write。 若要變更或省略公用或私人 IP 位址前端的區域性，請在適當的區域中重新建立 IP。 

可用性區域不會變更多個前端的條件約束。 如需此功能的詳細資訊，請參閱[多個前端 Load Balancer](load-balancer-multivip-overview.md) 。

### <a name="zone-redundant"></a>區域備援 

在具有可用性區域的區域中，標準負載平衡器前端可以是區域多餘的。 多個區域可以提供區域中的輸入或輸出。 此流量由單一 IP 位址提供服務。 您不需要進行 DNS 備援配置。 

單一前端 IP 位址會在區域失敗後存活。 前端 IP 可用來觸達所有（不受影響）的後端集區成員，不論該區域為何。 一或多個可用性區域可能會失敗，而且只要區域中的一個區域維持良好狀況，資料路徑就會不受。 

前端的 IP 位址會由多個可用性區域中的多個獨立基礎結構部署同時提供服務。 在其他區域中，任何重試或重新建立作業都會成功，而不受區域失敗影響。 

<p align="center">
  <img src="./media/az-zonal/zone-redundant-lb-1.svg" alt="Figure depicts a zone-redundant standard load balancer directing traffic in three different zones to three different subnets in a zone redundant configuration." width="512" title="虛擬網路 NAT">
</p>

*圖：區域多餘的負載平衡器*

### <a name="zonal"></a>區域性

您可以選擇讓某個前端保證屬於單一區域，我們稱之為*區域性前端*。  此案例表示任何輸入或輸出流程由區域中的單一區域提供服務。  您的前端會具有與區域相同的健康情況。  非保證區域中的失敗皆不會對資料路徑造成影響。 您可以使用區域性前端來公開每個可用性區域的 IP 位址。  

此外，支援將區域性前端直接用於每個區域內的負載平衡端點。 您可以使用此設定來公開每個區域的負載平衡端點，以個別監視每個區域。 針對公用端點，您可以將其與 DNS 負載平衡產品（例如[流量管理員](../traffic-manager/traffic-manager-overview.md)）整合，並使用單一 DNS 名稱。


<p align="center">
  <img src="./media/az-zonal/zonal-lb-1.svg" alt="Figure depicts three zonal standard load balancers each directing traffic in a zone to three different subnets in a zonal configuration." width="512" title="虛擬網路 NAT">
</p>

*圖：區域性多餘的負載平衡器*

如果您想要綜合這些概念 (相同後端的區域備援和區域性)，請檢閱 [Azure Load Balancer 的多個前端](load-balancer-multivip-overview.md)。

針對公用負載平衡器前端，您可以將**區域**參數新增至公用 IP。 個別規則所使用的前端 IP 設定會參考此公用 IP。

若為內部負載平衡器前端，請將**區域**參數新增至內部負載平衡器前端 IP 設定。 區域性前端可保證子網中特定區域的 IP 位址。

## <a name="backend"></a>後端

Azure Load Balancer 適用于虛擬機器實例。 這些實例可以是獨立、可用性設定組或虛擬機器擴展集。 單一虛擬網路中的任何虛擬機器都可以是後端集區的一部分，這是虛擬機器保證的任何區域。

若要以單一區域對齊並保證您的前端和後端，只需將相同區域內的虛擬機器放入對應的後端集區。

若要處理跨多個區域的虛擬機器，請將多個區域中的虛擬機器放入相同的後端集區。 

使用虛擬機器擴展集時，請將一或多個虛擬機器擴展集放入相同的後端集區。 擴展集可以位於單一或多個區域中。

## <a name="outbound-connections"></a>輸出連線

區域冗余和區域性適用于[輸出連接](load-balancer-outbound-connections.md)。 用於輸出連線的區域多餘公用 IP 位址是由所有區域提供服務。 區域公用 IP 位址只能由其保證所在的區域提供。 

輸出連線 SNAT 埠配置會存活區域失敗，而如果不受區域失敗影響，您的案例將會繼續提供輸出 SNAT 連線能力。 如果流量是由受影響的區域所服務，則區域失敗可能需要針對區域冗余案例重新建立連接。 在受影響區域以外的區域中的流程不會受到影響。

SNAT 埠預先配置演算法與或不含可用性區域相同。

## <a name="health-probes"></a>健康狀態探查

您現有的健康情況探查定義會維持不變，但不含可用性區域。 不過，我們已在基礎結構層級擴充健康情況模型。 

使用區域冗余前端時，負載平衡器會展開其內部健康情況檢查。 負載平衡器會獨立探查每個區域中虛擬機器的可用性，並關閉已失敗而不介入的區域之間的路徑。  

其他可存取此 VM 的區域將可繼續從其各自的前端為 VM 提供服務。 在失敗事件期間，每個區域可能會有不同的新流程散發，同時保護服務的整體健全狀況。

## <a name="design-considerations"></a><a name="design"></a>設計考慮

負載平衡器在可用性區域的內容中具有彈性。 您可以選擇對齊區域，或針對每個規則進列區域冗余。 增加可用性可能會增加複雜度的代價。 設計可用性以獲得最佳效能。

### <a name="zone-redundancy"></a>區域-冗余

Load Balancer 可簡化以單一 IP 作為區域備援前端的作業。 區域多餘的 IP 位址可以服務任何區域中的區域性資源。  只要一個區域在區域內保持良好狀態，IP 就可以在一或多個區域失敗後存活。  相反地，區域性前端是將服務縮減為單一區域，並與各自的區域共用 fate。

區域冗余並不表示表示資料路徑或控制平面;它是資料平面。 區域備援流量可以使用任何區域，且客戶的流量將會使用區域中所有狀況良好的區域。 在區域失敗中，使用狀況良好區域的流量不會受到影響。 

在區域失敗時使用區域的流量可能會受到影響，但應用程式可以復原。 當 Azure 在區域失敗時，傳輸會在區域內的狀況良好區域中繼續進行。

### <a name="cross-zone-boundaries"></a><a name="xzonedesign"></a>跨區域界限

請務必瞭解，每當服務跨越區域時，您就可以共用 fate，而不是一個區域，但可能會有多個區域。 因此，您的服務可能無法透過非區域部署取得任何可用性。

避免導入非預期的跨區域相依性，這會在使用可用性區域時使失效可用性提升。 如果您的應用程式包含多個重要元件，請確保在區域失敗時的生存。

如果您的應用程式只存在於倖存的區域以外的區域中，就會影響整個應用程式的單一關鍵元件。 請考慮進列區域還原，以及應用程式的交集程度。 瞭解您的應用程式如何回應其中部分的失敗。 查看重點，並在您考慮到特定案例時，使用它們來處理問題。

- 如果您的應用程式有兩個元件：

    * IP 位址
    * 具有受控磁片的虛擬機器

它們是在區域1和區域2中設定。 當區域1失敗時，您的服務將無法存留。 除非您完全瞭解您正在建立可能危險的失敗模式，否則請勿跨區域使用區域性案例。 此案例允許提供彈性。

- 如果您的應用程式有兩個元件：

    * IP 位址
    * 具有受控磁片的虛擬機器

它們已設定為區域多餘和區域1。 除非區域1失敗，否則您的服務將會在區域2、區域3或兩者的區域失敗後存活。 不過，如果您所觀察到的都是前端的可連線性，您就會失去部分服務健全狀況的能力。  請考慮開發更大範圍的健康情況和容量模型。  您可以一起使用區域備援和區域性概念來擴充見解與管理能力。

- 如果您的應用程式有兩個元件：

    * 區域-多餘的負載平衡器前端
    * 三個區域中的跨區域虛擬機器擴展集

您的資源在不受失敗影響的區域中將可供使用。 在失敗期間，您的服務容量可能會降低。 從基礎結構的觀點來看，您的部署可能會存活一或多個區域失敗。 此案例會引發下列問題：

  - 您瞭解應用程式如何回應失敗和降低的容量？
  - 您的服務中是否需要有保護措施，以在必要時強制容錯移轉至某個區域組？
  - 您將如何監視、偵測並消弭這種案例？ 您可以使用標準負載平衡器診斷來增加服務效能的監視。 請考慮可供使用的內容，以及可能需要擴大的內容。

- 區域有助於您了解及抑制失敗。 區域失敗與發生超時、重試和輪詢演算法時的其他失敗並無不同。 Azure Load Balancer 提供區域多餘的路徑。 負載平衡器會在封包層級即時嘗試快速復原。 萌芽失敗時可能會發生重新傳輸或 reestablishments。 請務必瞭解您的應用程式如何處理失敗。 您的負載平衡配置會存留下來，但您必須規劃下列問題：

  - 當區域失敗時，您的服務是否瞭解此失敗，如果狀態遺失，您將如何復原？
  - 區域回復時，您的應用程式了解如何安全地收斂嗎？

請參閱[Azure 雲端設計模式](https://docs.microsoft.com/azure/architecture/patterns/)，以改善應用程式對失敗案例的復原能力。

## <a name="next-steps"></a>後續步驟
- 深入瞭解[可用性區域](../availability-zones/az-overview.md)
- 深入瞭解[Standard Load Balancer](load-balancer-standard-overview.md)
- 了解如何[使用具有區域前端的標準 Load Balancer 來進行區域內的 VM 負載平衡](load-balancer-standard-public-zonal-cli.md)
- 了解如何[使用具有區域備援前端的標準 Load Balancer 來進行跨區域的 VM 負載平衡](load-balancer-standard-public-zone-redundant-cli.md)
- 瞭解[Azure 雲端設計模式](https://docs.microsoft.com/azure/architecture/patterns/)，以改善應用程式對失敗案例的復原能力。
