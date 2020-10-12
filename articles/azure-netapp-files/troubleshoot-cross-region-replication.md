---
title: 針對 Azure NetApp Files 的跨區域複寫進行疑難排解 |Microsoft Docs
description: 描述可協助您針對 Azure NetApp Files 的跨區域複寫問題進行疑難排解的錯誤訊息和解決方式。
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
ms.topic: troubleshooting
ms.date: 09/16/2020
ms.author: b-juche
ms.openlocfilehash: 3aaa5d2bc6fdbda0d1db212539c719aa65cae61b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90708763"
---
# <a name="troubleshoot-cross-region-replication"></a>針對跨區域複寫進行疑難排解

本文說明可協助您針對 Azure NetApp Files 的跨區域複寫問題進行疑難排解的錯誤訊息和解決方式。 

## <a name="errors-creating-replication"></a>建立複寫時發生錯誤  

|     錯誤訊息    |     解決方案    |
|-|-|
|     `Volume {0} cannot   be used as source because it is already in replication`    |     您無法使用已經在資料複寫關聯性中的來源磁片區來建立複寫。    |
|     `Peered region   '{0}' is not accepted`    |     您正在嘗試建立未對等互連區域之間的複寫。    |
|     `RemoteVolumeResource   '{0}' of wrong type '{1}'`    |     驗證遠端資源識別碼是磁片區資源識別碼。    |

## <a name="errors-authorizing-volume"></a>授權磁片區時發生錯誤  

|     錯誤訊息    |     解決方案    |
|-|-|
|     `Missing value   for 'AuthorizeSourceReplication'`    |     `RemoteResourceID`UI 或 API 要求中遺漏或無效， (修正錯誤訊息) 。    |
|     `Missing value   for 'RemoteVolumeResourceId'`    |     `RemoteResourceID`UI 或 API 要求中遺失或無效。    |
|     `Data   Protection volume not found for RemoteVolumeResourceId: {remoteResourceId}`    |     驗證   `RemoteResourceID` 使用者是否正確或是否存在。    |
|     `Remote volume   '{0}' is not configured for replication`    |     目的地磁片區不是資料保護磁片區。    |
|     `Remote volume   '{0}' does not have source volume '{1}' as RemoteVolumeResourceId`    |     資料保護磁片區在其遠端資源識別碼中沒有此來源磁片區， (輸入錯誤的來源識別碼) 。    |
|     `The   destination volume replication creation failed (message: {0})`    |     此錯誤表示伺服器錯誤。 請連絡支援人員。    |

## <a name="errors-deleting-replication"></a>刪除複寫時發生錯誤

|     錯誤訊息    |     解決方案    |
|-|-|
|     `Replication   cannot be deleted, mirror state needs to be in status: Broken before deleting`    |     請驗證複寫是否已中斷，或未初始化，或是閒置 (失敗的初始化) 。    |
|     `Cannot delete   source replication`    |     不允許從來源端刪除複寫。 請確定您要從目的地端刪除複寫。    |

## <a name="errors-resyncing-volume"></a>正在重新同步磁片區錯誤

|     錯誤訊息    |     解決方案    |
|-|-|
|     `Volume Replication is in invalid status: (Mirrored|Uninitialized) for operation: 'ResyncReplication'`     |     驗證磁片區複寫處於「已中斷」狀態。    |

## <a name="errors-deleting-snapshot"></a>刪除快照集時發生錯誤 

|     錯誤訊息    |     解決方案    |
|-|-|
|     `Snapshot   cannot be deleted, parent volume is a Data Protection volume with a   replication object`    |     如果您想要刪除此快照集，請驗證您是否已中斷磁片區的複寫。    |
|     `Cannot delete   volume replication generated snapshot`    |     不允許刪除複寫基準快照集。    |

## <a name="next-steps"></a>後續步驟  

* [跨區域複寫](cross-region-replication-introduction.md)
* [使用跨區域複寫的需求和考慮](cross-region-replication-requirements-considerations.md)
* [建立複寫對等互連](cross-region-replication-create-peering.md)
* [顯示複寫關聯性的健全狀態](cross-region-replication-display-health-status.md)
* [管理災害復原](cross-region-replication-manage-disaster-recovery.md)
* [針對跨區域複寫進行疑難排解](troubleshoot-cross-region-replication.md)
