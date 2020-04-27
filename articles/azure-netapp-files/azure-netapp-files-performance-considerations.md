---
title: Azure NetApp Files 的效能考慮 |Microsoft Docs
description: 說明 Azure NetApp Files 的效能考慮。
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
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/26/2020
ms.locfileid: "67454135"
---
# <a name="performance-considerations-for-azure-netapp-files"></a>適用於 Azure NetApp Files 的效能考量

磁片區的[輸送量限制](azure-netapp-files-service-levels.md)是由指派給磁片區的配額和所選取服務等級的組合所決定。 當您針對 Azure NetApp Files 進行效能計畫時，您需要瞭解幾個考慮。 

## <a name="quota-and-throughput"></a>配額和輸送量  

輸送量限制只是將會實現的實際效能的其中一個行列式。  

典型的儲存體效能考慮，包括讀取和寫入混合、傳輸大小、隨機或連續模式，以及許多其他因素將會影響所提供的整體效能。  

測試中已觀察到的最大經驗輸送量為 4500 MiB/s。  在高階儲存層中，70.31 TiB 的磁片區配額會布建夠高的輸送量限制，以達到此層級的效能。  

如果您考慮指派超過 70.31 TiB 的磁片區配額，則可能會將額外的配額指派給磁片區，以儲存額外的資料。 不過，增加的配額不會導致實際輸送量增加。  

如需其他資訊，請參閱[Azure NetApp Files 的效能基準](azure-netapp-files-performance-benchmarks.md)。

## <a name="overprovisioning-the-volume-quota"></a>過度布建磁片區配額

如果工作負載的效能受限於輸送量限制，則可以 overprovision 磁片區配額來設定較高的輸送量層級，並達到更高的效能。  

例如，如果 Premium 儲存層中的磁片區只有 500 GiB 的資料，但需要 128 MiB/秒的輸送量，您可以將配額設定為 2 TiB，以據此設定輸送量層級（64 MiB/秒/每 TB * 2 TiB = 128 MiB/秒）。  

如果您一直 overprovision 磁片區以達到較高的輸送量，請考慮改為使用較高的服務層級。  在上述範例中，您可以使用 Ultra 儲存層來達到相同的輸送量限制一半的磁片區配額（每個 TiB * 1 TiB = 128 MiB/秒 128 MiB/秒）。

## <a name="dynamically-increasing-or-decreasing-volume-quota"></a>動態增加或減少磁片區配額

如果您的效能需求是暫時性的，或如果您在一段固定的時間內增加了效能需求，您可以動態地增加或減少磁片區配額，以立即調整輸送量限制。  請注意下列考量： 

* 磁片區配額可以增加或減少，而不需要暫停 IO，而且磁片區的存取不會中斷或受影響。  

    您可以在對磁片區進行作用中 i/o 交易期間調整配額。  請注意，磁片區配額絕不能減少到磁片區中儲存的邏輯資料量之下。

* 當磁片區配額變更時，輸送量限制的對應變更幾乎是瞬間的。 

    這種變更不會中斷或影響磁片區存取或 i/o。  

* 調整磁片區配額需要變更容量集區大小。  

    容量集區大小可以動態調整，而不會影響磁片區的可用性或 i/o。

## <a name="next-steps"></a>後續步驟

- [Azure NetApp Files 的服務等級](azure-netapp-files-service-levels.md)
- [適用於 Azure NetApp Files 的效能基準測試](azure-netapp-files-performance-benchmarks.md)