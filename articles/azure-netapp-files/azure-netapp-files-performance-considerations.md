---
title: Azure NetApp Files 的效能考慮 |Microsoft Docs
description: 瞭解 Azure NetApp Files 的效能，包括配額和輸送量限制的關聯性，以及如何動態增加/減少磁片區配額。
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
ms.date: 09/22/2020
ms.author: b-juche
ms.openlocfilehash: edfebe3d9470defbe70b3694d5574e58ca3b5938
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91325516"
---
# <a name="performance-considerations-for-azure-netapp-files"></a>適用於 Azure NetApp Files 的效能考量

具有自動 QoS 之磁片區的 [輸送量限制](azure-netapp-files-service-levels.md) 是由指派給磁片區的配額和選取的服務等級所組成。 對於具有手動 QoS 的磁片區，可以個別定義輸送量限制。 當您對 Azure NetApp Files 進行效能計畫時，您需要瞭解幾個考慮。 

## <a name="quota-and-throughput"></a>配額和輸送量  

輸送量限制只是將實現的實際效能的一個行列式。  

典型的儲存體效能考慮，包括讀取和寫入混合、傳輸大小、隨機或連續模式，以及許多其他因素，都將有助於提供的整體效能。  

在測試中觀察到的最大經驗輸送量是 4500 MiB/s。  在 Premium 儲存層，70.31 TiB 的自動 QoS 磁片區配額會布建足夠的輸送量限制，以達到此層級的效能。  

在自動 QoS 磁片區的情況下，如果您考慮將磁片區配額數量指派超過 70.31 TiB，則可能會將額外的配額指派給磁片區，以儲存其他資料。 但是，新增的配額不會導致實際的輸送量增加。  

相同的經驗輸送量上限適用于具有手動 QoS 的磁片區。 可以指派給磁片區的最大輸送量為 4500 MiB/s。

## <a name="automatic-qos-volume-quota-and-throughput"></a>自動 QoS 磁片區配額和輸送量

本節說明具有自動 QoS 類型之磁片區的配額管理和輸送量。

### <a name="overprovisioning-the-volume-quota"></a>過度布建磁片區配額

如果工作負載的效能是輸送量限制，則可以過度布建自動 QoS 磁片區配額來設定較高的輸送量層級，並達到更高的效能。  

例如，如果 Premium 儲存層中的自動 QoS 磁片區只有 500 GiB 的資料，但需要 128 MiB/秒的輸送量，您可以將配額設定為 2 TiB，如此一來，輸送量層級會據以設定 (64 MiB/秒 * 2 TiB = 128 MiB/s) 。  

如果您一直過度布建磁片區以達到更高的輸送量，請考慮使用手動 QoS 磁片區，或改用較高的服務等級。  在上述範例中，您可以使用 Ultra 儲存層來達到相同的輸送量限制一半的自動 QoS 磁片區配額，而不是 (每個 TiB 128 MiB/秒 * 1 TiB = 128 MiB/s) 。

### <a name="dynamically-increasing-or-decreasing-volume-quota"></a>動態增加或減少磁片區配額

如果您的效能需求是暫時性的，或是您在一段固定時間內的效能需求增加，您可以動態增加或減少磁片區配額，以立即調整輸送量限制。  請注意下列考量： 

* 您可以增加或減少磁片區配額，而不需要暫停 IO，而且磁片區的存取不會中斷或受影響。  

    您可以根據磁片區來調整使用中 i/o 交易期間的配額。  請注意，磁片區配額絕不能減少到磁片區中儲存的邏輯資料量之下。

* 當磁片區配額變更時，輸送量限制的對應變更幾乎是瞬間的。 

    此變更不會中斷或影響磁片區存取或 i/o。  

* 調整磁片區配額可能需要變更容量集區大小。  

    容量集區大小可動態調整，而不會影響磁片區可用性或 i/o。

## <a name="manual-qos-volume-quota-and-throughput"></a>手動 QoS 磁片區配額和輸送量 

如果您使用手動 QoS 磁片區，則不需要過度布建磁片區配額以達到較高的輸送量，因為輸送量可以個別指派給每個磁片區。 不過，您仍然需要確定容量集區已預先布建，且具有足夠的輸送量可滿足您的效能需求。 容量集區的輸送量會根據其大小和服務層級進行布建。 如需詳細資訊，請參閱 [Azure NetApp Files 的服務等級](azure-netapp-files-service-levels.md) 。


## <a name="next-steps"></a>後續步驟

- [Azure NetApp Files 的服務等級](azure-netapp-files-service-levels.md)
- [Linux 的效能評定](performance-benchmarks-linux.md)