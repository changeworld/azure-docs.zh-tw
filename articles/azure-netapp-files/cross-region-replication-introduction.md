---
title: Azure NetApp Files 磁片區的跨區域複寫 |Microsoft Docs
description: 說明 Azure NetApp Files 跨區域複寫功能、支援的區域組、服務層級目標、資料耐久性和成本模型。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/16/2020
ms.author: b-juche
ms.openlocfilehash: e7d424435c29eb3e7a6779a7036816a7bedd2085
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/16/2020
ms.locfileid: "90708583"
---
# <a name="cross-region-replication-of-azure-netapp-files-volumes"></a>Azure NetApp Files 磁片區的跨區域複寫

Azure NetApp Files 複製功能可透過跨區域磁片區複寫提供資料保護。 您可以將資料從某個區域中的 Azure NetApp Files 磁片區 (來源) ，以非同步方式複寫至另一個區域中的另一個 Azure NetApp Files 磁片區 (目的地) 。  這項功能可讓您在整個區域發生中斷或損毀時，容錯移轉您的重要應用程式。

> [!IMPORTANT]
> 跨區域複寫功能目前處於公開預覽狀態。 您必須透過 [Azure NetApp Files 跨區域複寫等候清單提交頁面](https://aka.ms/anfcrrpreviewsignup)提交等候清單要求，以存取此功能。 使用跨區域複寫功能之前，請先從 Azure NetApp Files 團隊等候官方確認電子郵件。

## <a name="supported-region-pairs"></a>支援的區域配對

Azure NetApp Files 磁片區複寫目前提供下列固定區域配對：  

* 美國西部和美國東部
* 美國西部2和美國東部 
* 美國中南部和美國中部 
* 美國中南部和美國東部
* 美國中南部和美國東部2 
* 美國東部2和美國中部 
* 北歐和西歐
* 英國南部和英國西部
* 澳大利亞東部和澳大利亞東南部
* 加拿大中部和加拿大東部
* 日本東部和日本西部
* 英國南部和德國中西部
* 東南亞和澳大利亞東部

## <a name="service-level-objectives"></a>服務層級目標

復原點目標 (RPO) 或最大可容忍資料遺失，會定義為複寫排程的兩倍。  所觀察到的實際 RPO 可能會因各種因素而異，例如資料集大小總計和變更率、資料覆寫的百分比，以及可供傳送的複寫頻寬。   

* 若為10分鐘的複寫排程，最大 RPO 為20分鐘。  
* 針對每小時複寫排程，最大 RPO 為兩小時。  
* 針對每日複寫排程，最大 RPO 為兩天。  

復原時間目標 (RTO) ，或可容忍的商務應用程式停機時間上限，取決於啟動應用程式並提供第二個網站資料存取權的因素。 RTO 的儲存部分會中斷對等互連關聯性，以啟用目的地磁片區，並提供第二個網站中的讀取和寫入資料存取權，在一分鐘內應會完成。

## <a name="cost-model-for-cross-region-replication"></a>跨區域複寫的成本模型  

透過 Azure NetApp Files 跨區域複寫，您只需支付所複寫的資料量。 沒有設定費用或最小使用費用。 複寫價格取決於複寫頻率，以及您在初始複寫設定期間選擇的 *目的地* 磁片區區域。 如需詳細資訊，請參閱 [Azure NetApp Files 定價](https://azure.microsoft.com/pricing/details/netapp/) 頁面。  

一般的 Azure NetApp Files 儲存體容量費用適用于複寫目的地磁片區 (也稱為 *資料保護* 磁片區) 。 

### <a name="pricing-examples"></a>計費範例

每月計費的跨區域複寫量是以該月份在跨區域複寫功能中複寫的資料量為基礎。 複寫的資料量會以 GiB 來測量。 它代表從來源磁片區到目的地磁片區的所有一般複寫期間，以及在所有從目的地磁片區到來源磁片區的重新同步複寫期間，在兩個區域之間複寫的資料總和。

#### <a name="example-1-month-1-baseline-replication-and-incremental-replications"></a>範例1：第1個月的基準複寫和增量複製

假設下列情況：

* *來源*磁片區來自 Azure NetApp Files *Premium*服務層級。 它的磁片區配額大小為 1000 GiB，而磁片區的磁片區大小為 500 GiB，在每個月的第一天開始。 此磁片區位於 *美國中南部* 區域中。
* 您的 *目的地* 磁片區來自 Azure NetApp Files *標準* 服務層級。 它位於 *美國東部 2* 區域。
* 您已設定上述兩個磁片區之間以 *小時* 為基礎的跨區域複寫。 因此，每個 GiB 的複寫價格為 $0.12。
* 為了簡單起見，假設來源磁片區的每小時都有固定的 0.5-GiB 資料變更，但所耗用的總磁片區大小並未增加 (保持在 500 GiB) 。 

初始設定完成後，就會立即進行基準複寫。  

* 基準複寫期間複寫的資料量： `500 GiB`
* 基準複寫費用： `500 GiB * $0.12 = $60`

在基準複寫之後，只會複寫變更的區塊。 因此，在後續的累加式複寫中，每個小時只會複寫 0.5 GiB 的資料。

* 30天內複寫跨增量複寫的資料量總和： `0.5 GiB * 24 hours * 30 days = 360 GiB`
* 增量複寫費用： `360 GiB * $0.12 = $43.2`

在月底1結束時，跨區域複寫的總費用如下所示：  

*  每月1的跨區域複寫費用總計： `$60 + $43.2 = $103.2`

一般的 Azure NetApp Files 儲存體容量費用會套用至目的地磁片區。 不過，目的地磁片區可以使用不同于 (的儲存層，也可以比) 來源磁片區層更便宜。

#### <a name="example-2-month-2-incremental-replications-and-resync-replications"></a>範例2：第2個月增量複製和重新同步複寫  

假設您有來源磁片區、目的地磁片區，以及兩個設定之間的複寫關聯性，如範例1所述。 在第二個月的29天 (30 天的月) ，每小時的複寫會如預期般發生。

* 在增量複寫間複寫的資料量總和，29天： `0.5 GiB * 24 hours * 29 days = 348 GiB`

假設在當月的最後一天，來源區域發生未預期的中斷，且您容錯移轉至目的地磁片區。 在2小時後，來源區域會復原，而且您會執行從目的地磁片區至來源磁片區的重新同步複寫。 在2小時期間，會在目的地磁片區上發生 0.8 GiB 的資料變更，而且需要 resynced 至來源。

* 過去一天22小時，在一般複寫間複寫的資料量總和： `0.5 GiB * 22 hours = 11 GiB`
* 在一次重新同步複寫期間複寫的資料量： `0.8 GiB`

因此，在第2個月結束時，跨區域複寫的總費用如下所示：  

* 每月2的跨區域複寫費用總計： `(348 GiB + 11 GiB + 0.8 GiB) * $0.12 = $43.18`

第2個月2的一般 Azure NetApp Files 儲存體容量費用適用于目的地磁片區。

## <a name="next-steps"></a>下一步
* [使用跨區域複寫的需求和考慮](cross-region-replication-requirements-considerations.md)
* [建立複寫對等互連](cross-region-replication-create-peering.md)
* [顯示覆寫關聯性的健全狀況狀態](cross-region-replication-display-health-status.md)
* [管理災害復原](cross-region-replication-manage-disaster-recovery.md)
* [磁片區複寫計量](azure-netapp-files-metrics.md#replication)
* [針對跨區域複寫進行疑難排解](troubleshoot-cross-region-replication.md)


