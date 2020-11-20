---
title: 跨 Azure 區域的 SAP HANA 可用性 | Microsoft Docs
description: 在多個 Azure 區域中的 Azure VM 上執行 SPA HANA 時的可用性考量概觀。
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/12/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 799034d9060e8afe101e86e7b98ac7e01c28d191
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2020
ms.locfileid: "94950239"
---
# <a name="sap-hana-availability-across-azure-regions"></a>跨 Azure 區域的 SAP HANA 可用性

本文會說明跨不同 Azure 區域的 SAP HANA 可用性相關案例。 由於 Azure 區域之間的距離，在多個 Azure 區域中設定 SAP HANA 可用性會涉及特殊考量。

## <a name="why-deploy-across-multiple-azure-regions"></a>跨多個 Azure 區域部署的理由

Azure 區域通常會相隔遙遠的距離。 根據地緣政治區域，Azure 區域之間的距離可能相隔數百甚至數千英哩，例如美國境內就是如此。 由於距離的關係，部署於兩個不同 Azure 區域的資產，其之間的流量會經歷明顯的網路往返延遲。 此延遲明顯到足以排除典型 SAP 工作負載底下兩個 SAP HANA 執行個體之間的同步資料交換。 

另一方面，組織通常會要求主要資料中心的位置要與次要資料中心相隔一段距離。 這個距離要求有助於在大範圍地理位置發生天然災害時提供可用性。 範例包括 2017 年 9 月和 10 月侵襲加勒比海和佛羅里達州的颶風。 貴組織或許至少會有最小距離要求。 對於大部分的 Azure 客戶來說，最小距離定義會讓您必須設計跨 [Azure 區域](https://azure.microsoft.com/regions/)的可用性。 因為兩個 Azure 區域之間的距離太遠，而無法使用 HANA 同步複寫模式，所以 RTO 和 RPO 需求可能會迫使您在某個區域內部署可用性設定，再於第二個區域中補上其他部署。

此案例的另一個考慮面向是容錯移轉和用戶端重新導向。 在此假設兩個不同 Azure 區域中 SAP HANA 執行個體之間的容錯移轉一律為手動容錯移轉。 因為 SAP HANA 系統複寫的複寫模式設定為非同步，所以有可能在主要 HANA 執行個體中認可的資料尚未認可到次要 HANA 執行個體。 因此對使用非同步複寫的設定來說，自動容錯移轉無法成為其選項之一。 即使使用手動控制的容錯移轉，如同在容錯移轉練習中，您都需要採取相關措施，以確定在主要端上的所有認可資料，在手動移至其他 Azure 區域之前認可至次要執行個體。
 
Azure 虛擬網路會使用不同的 IP 位址範圍。 這些 IP 位址會部署在第二個 Azure 區域中。 因此，您必須變更 SAP HANA 用戶端設定，或者最好是建立步驟來變更名稱解析。 如此一來，就可將用戶端重新導向至新的次要網站伺服器 IP 位址。 如需詳細資訊，請參閱 SAP 文章[接管後的用戶端連線復原](https://help.sap.com/doc/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/c93a723ceedc45da9a66ff47672513d3.html)。   

## <a name="simple-availability-between-two-azure-regions"></a>兩個 Azure 區域之間的簡單可用性

您可以選擇不要將任何可用性設定放在單一區域內的位置，但您仍需要在發生災害時能夠處理工作負載。 這類情節的典型案例為非生產系統。 雖然您可以承受系統關閉半天或甚至一天，但是不允許系統無法使用 48 小時 (含) 以上。 若要降低設定成本，請在 VM 中執行另一個較不重要的系統。 這另一個系統會作為目的地。 您也可以將次要區域中的 VM 調整為較小的大小，並選擇不要預先載入資料。 由於容錯移轉是手動進行的，而且需要更多步驟才能容錯移轉完整應用程式堆疊，所以關閉 VM、調整其大小及再次重新啟動 VM 的額外時間是可接受的。

如果您的使用案例是在 VM 中與品管系統共用 DR 目標，您需要考慮下列事項：

- 有兩個[作業模式](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/627bd11e86c84ec2b9fcdf585d24011c.html) delta_datashipping 和 logreplay 適用於這類案例
- 這兩種作業模式有不同的記憶體需求，但不需要預先載入資料
- 相較於 logreplay，沒有預先載入選項的情況下，Delta_datashipping 可能需要極少的記憶體。 請參閱 SAP 文件的章節 4.3 [如何執行 SAP HANA 的系統複寫](https://archive.sap.com/kmuuid2/9049e009-b717-3110-ccbd-e14c277d84a3/How%20to%20Perform%20System%20Replication%20for%20SAP%20HANA.pdf)
- 沒有預先載入的 Logreplay 作業模式所需的記憶體並不確定，端視載入的資料行存放區結構而定。 在極端情況下，您可能需要主要執行個體記憶體的 50%。 Logreplay 作業模式的記憶體與您之前是否選擇預先載入資料無關。


![兩個區域有兩部 VM 的圖表](./media/sap-hana-availability-two-region/two_vm_HSR_async_2regions_nopreload.PNG)

> [!NOTE]
> 在此組態中，您無法提供 RPO=0，因為您的 HANA 系統複寫模式是非同步的。 如果您需要提供 RPO=0，此組態不是適合選擇的組態。

您可對設定進行的小變動可能是將資料設定為預先載入。 不過由於手動容錯移轉的本質和應用程式層級也必須移至第二個區域的事實，預先載入資料可能沒有意義。 

## <a name="combine-availability-within-one-region-and-across-regions"></a>結合一個區域內和跨區域的可用性 

下列因素可促進區域內和跨區域可用性的結合：

- Azure 區域內 RPO=0 的需求。
- 組織不願意或無法讓全域作業受到會影響較大區域的大型天然災難所影響。 過去幾年侵襲加勒比海的幾個颶風就是這種情況。
- 要求主要與次要網站之間的距離明顯超過 Azure 可用性區域可提供之距離的規定。

在這些情況下，您可以使用 HANA 系統複寫來設定哪個 SAP 會呼叫 [SAP HANA 多層式系統複寫設定](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/ca6f4c62c45b4c85a109c7faf62881fc.html)。 架構如下所示：

![兩個區域有三部 VM 的圖表](./media/sap-hana-availability-two-region/three_vm_HSR_async_2regions_ha_and_dr.PNG)

SAP 引進了[多目標系統複寫](https://help.sap.com/viewer/42668af650f84f9384a3337bcd373692/2.0.03/en-US/0b2c70836865414a8c65463180d18fec.html) (含 HANA 2.0 SPS3)。 多目標系統複寫為更新案例帶來一些優點。 例如，當次要 HA 網站關閉以便維護或更新時，DR 網站 (區域 2) 不受影響。 您可以在[這裡](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.03/en-US/ba457510958241889a459e606bbcf3d3.html)深入了解 HANA 多目標系統複寫。
採用多目標複寫的可能架構如下所示：

![兩個區域有三部 VM (多目標) 的圖表](./media/sap-hana-availability-two-region/saphanaavailability_hana_system_2region_HA_and_DR_multitarget_3VMs.PNG)

如果組織在次要 (DR) Azure 區域中有高可用性整備的需求時，則架構如下所示：

![此圖顯示在第二個 (DR) Azure 區域中，具有高可用性需求的組織。](./media/sap-hana-availability-two-region/saphanaavailability_hana_system_2region_HA_and_DR_multitarget_4VMs.PNG)


此設定使用 logreplay 做為作業模式，會在主要區域內以低 RTO 提供 RPO=0。 如果程序涉及移動至第二個區域，此設定也會提供不錯的 RPO。 第二個區域中的 RTO 時間取決於資料是否會預先載入。 許多客戶會使用次要區域中的 VM 來執行測試系統。 在該使用案例中，系統無法預先載入資料。

> [!IMPORTANT]
> 在不同層之間的作業模式必須有同質性。 您 **無法** 在第 1 層和第 2 層以及 delta_datashipping 之間使用 logreply 做為作業模式來提供第 3 層。 您只能選擇需要對於所有層級保持一致的一個或另一個作業模式。 由於 delta_datashipping 不適用提供 RPO=0，因此，針對這類多層組態而言合理的作業模式仍然是 logreplay。 如需作業模式和一些限制的詳細資料，請參閱 SAP 文章 [SAP HANA 系統複寫的作業模式](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/627bd11e86c84ec2b9fcdf585d24011c.html)。 

## <a name="next-steps"></a>後續步驟

如需在 Azure 中設定這些設定的逐步指引，請參閱：

- [在 Azure 虛擬機器中設定 SAP Hana 系統複寫](sap-hana-high-availability.md)
- [使用系統複寫的 SAP HANA 高可用性](https://blogs.sap.com/2018/01/08/your-sap-on-azure-part-4-high-availability-for-sap-hana-using-system-replication/)

 



 
  
