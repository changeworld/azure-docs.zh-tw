---
title: Azure NetApp Files 的手動 QoS 容量集區 |Microsoft Docs
description: 提供手動 QoS 容量集區的簡介，以及其他資訊的參考。
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
ms.date: 09/23/2020
ms.author: b-juche
ms.openlocfilehash: 14b905c69f0dab933159b414028db3e985d314a3
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91935699"
---
# <a name="manual-qos-capacity-pool"></a>手動 QoS 容量集區

本文提供手動服務品質 (QoS) 容量集區功能的簡介。

## <a name="how-manual-qos-differs-from-auto-qos"></a>手動 QoS 與自動 QoS 有何不同

[QoS 類型](azure-netapp-files-understand-storage-hierarchy.md#qos_types)是容量集區的屬性。 Azure NetApp Files 提供兩種 QoS 類型的容量集區– auto (預設) 和手動。  

在 *手動* QoS 容量集區中，您可以獨立指派磁片區的容量和輸送量。 以手動 QoS 容量集區建立的所有磁碟區輸送量總計，受限於集區的輸送量總計。 這是由集區大小和服務層級輸送量的組合所決定。 

在 *自動* QoS 容量集區中，會將輸送量自動指派給集區中的磁片區，並與指派給磁片區的大小配額成正比。  

如需 QoS 類型的考慮，請參閱 azure netapp files 的 [儲存體](azure-netapp-files-understand-storage-hierarchy.md) 階層和 [azure Netapp files 的效能考慮](azure-netapp-files-performance-considerations.md) 。

## <a name="how-to-specify-the-manual-qos-type"></a>如何指定手動 QoS 類型

當您 [建立容量集](azure-netapp-files-set-up-capacity-pool.md)區時，可以指定容量集區使用手動 QoS 類型。  您也可以 [變更現有的容量集](manage-manual-qos-capacity-pool.md#change-to-qos) 區，以使用手動 QoS 類型。 

將容量類型設定為手動 QoS 是永久性變更。 您無法將手動 QoS 類型容量工具轉換成自動 QoS 容量集區。 

使用手動 QoS 類型需要您 [註冊此功能](manage-manual-qos-capacity-pool.md#register-the-feature)。  

## <a name="next-steps"></a>後續步驟

* [管理手動 QoS 容量集區](manage-manual-qos-capacity-pool.md)
* [設定容量集區](azure-netapp-files-set-up-capacity-pool.md)
* [儲存體階層](azure-netapp-files-understand-storage-hierarchy.md) 
* [Azure NetApp Files 的服務等級](azure-netapp-files-service-levels.md)
* [適用於 Azure NetApp Files 的效能考量](azure-netapp-files-performance-considerations.md)
* [適用於Azure NetApp Files 的成本模型](azure-netapp-files-cost-model.md)
* [Azure NetApp Files 的資源限制](azure-netapp-files-resource-limits.md)
* [建立 NFS 磁碟區](azure-netapp-files-create-volumes.md)
* [建立 SMB 磁碟區](azure-netapp-files-create-volumes-smb.md)
* [建立雙重通訊協定磁碟區](create-volumes-dual-protocol.md)
* [Azure NetApp Files 的計量](azure-netapp-files-metrics.md)
* [針對容量集區問題進行疑難排解](troubleshoot-capacity-pools.md)
