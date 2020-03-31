---
title: Azure NetApp 檔的性能注意事項 |微軟文檔
description: 描述 Azure NetApp 檔的性能注意事項。
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
ms.date: 06/25/2019
ms.author: b-juche
ms.openlocfilehash: 97e3c6212edd2ade4eabb96db3543e9b3b68e2ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "67454135"
---
# <a name="performance-considerations-for-azure-netapp-files"></a>適用於 Azure NetApp Files 的效能考量

卷的[輸送量限制](azure-netapp-files-service-levels.md)由分配給卷的配額和所選服務等級的組合決定。 在創建有關 Azure NetApp 檔的性能計畫時，需要瞭解幾個注意事項。 

## <a name="quota-and-throughput"></a>配額和輸送量  

輸送量限制只是實際性能的一個決定因素。  

典型的存儲性能注意事項，包括讀寫混合、傳輸大小、隨機或順序模式以及許多其他因素，都將有助於交付的總體性能。  

在測試中觀察到的最大經驗輸送量為 4，500 MiB/s。  在高級存儲層中，容量配額為 70.31 TiB 將預配足以達到此性能級別的輸送量限制。  

如果您正在考慮分配超出 70.31 TiB 的卷配額金額，則可以為存儲其他資料的卷分配額外的配額。 但是，添加的配額不會導致實際輸送量的進一步增加。  

有關其他資訊，請參閱[Azure NetApp 檔的性能基準](azure-netapp-files-performance-benchmarks.md)。

## <a name="overprovisioning-the-volume-quota"></a>過度預配卷配額

如果工作負載的性能是輸送量限制綁定的，則可能會過度調配卷配額以設置更高的輸送量級別並實現更高的性能。  

例如，如果高級存儲層中的卷只有 500 GiB 的資料，但需要 128 MiB/s 的輸送量，則可以將配額設置為 2 TiB，以便相應地設置輸送量級別（每個 TB 64 MiB/s = 2 TiB = 128 MiB/s）。  

如果始終過度調配卷以實現更高的輸送量，請考慮改用更高的服務等級。  在上面的示例中，您可以使用 Ultra 存儲層（每個 TiB 128 MiB/s = 1 TiB = 128 MiB/s），實現相同輸送量限制，使用一半的卷配額。

## <a name="dynamically-increasing-or-decreasing-volume-quota"></a>動態增加或減少卷配額

如果性能要求是臨時性的，或者在固定時間段內提高了性能需求，則可以動態增加或減少音量配額以即時調整輸送量限制。  請注意下列考量： 

* 無需暫停 IO 即可增加或減少卷配額，並且對卷的訪問不會中斷或受到影響。  

    您可以在活動 I/O 事務期間針對卷調整配額。  請注意，卷配額永遠不會降低到存儲在卷中的邏輯資料量以下。

* 當音量配額更改時，輸送量限制的相應變化幾乎是暫態的。 

    更改不會中斷或影響卷訪問或 I/O。  

* 調整容量配額需要更改容量池大小。  

    容量池大小可以動態調整，而不會影響容量可用性或 I/O。

## <a name="next-steps"></a>後續步驟

- [Azure NetApp Files 的服務等級](azure-netapp-files-service-levels.md)
- [適用於 Azure NetApp Files 的效能基準測試](azure-netapp-files-performance-benchmarks.md)