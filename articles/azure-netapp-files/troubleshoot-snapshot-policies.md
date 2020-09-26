---
title: 針對 Azure NetApp Files 的快照集原則進行疑難排解 |Microsoft Docs
description: 描述可協助您針對 Azure NetApp Files 的快照集原則管理問題進行疑難排解的錯誤訊息和解決方式。
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
ms.date: 09/23/2020
ms.author: b-juche
ms.openlocfilehash: 1d7a91de8fa505fe4c2b06eea59f3acc2ae1f7e8
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91342022"
---
# <a name="troubleshoot-snapshot-policies"></a>針對快照集原則進行疑難排解

本文說明您在管理 Azure NetApp Files 快照集原則時可能遇到的錯誤案例。 它也提供可協助您解決問題的解決方案。

## <a name="snapshot-policy-creation-failing-with-invalid-snapshot-policy-name"></a>快照集原則建立失敗，快照集原則名稱無效

如果快照集原則名稱無效，則在建立快照集原則時，就會發生錯誤。 下列指導方針適用于快照集原則名稱：  

* 快照集原則名稱不能包含非 ASCII 或特殊字元。
* 快照集原則名稱必須以字母或數位開頭，而且只能包含字母、數位、底線 ( ' _ ' ) 和連字號 ( '-' ) 。
* 快照集原則名稱必須介於1到64個字元之間。  

您應該根據指導方針修改快照集原則名稱。  

## <a name="snapshot-policy-creation-failing-with-invalid-values"></a>快照集原則建立失敗，值無效 

如果您輸入不正確欄位值（例如或），則 Azure NetApp Files 無法建立快照集 `Number of snapshots to keep` 原則 `Minute on the hour to take snapshot` 。  
 
有效值如下：   

* 值必須是有效的數位。
* 值必須介於0到59之間。

請確定已為欄位提供有效的值。

## <a name="snapshot-policy-creation-failing-with-total-number-of-snapshots-to-keep-exceeds-255-error"></a>快照集原則建立失敗，出現「要保留的快照集總數超過255」錯誤 

每個磁片區 [最多](azure-netapp-files-resource-limits.md)可以有255個快照集。 最大值包含所有每小時、每日、每週和每月快照的總和。 您應減少 `Snapshots to keep` 該值，然後再試一次。

## <a name="assigning-policy-to-a-volume-failing-with-total-snapshot-policy-is-over-the-max-255-error"></a>將原則指派給磁片區失敗，「快照集原則總計超過最大值 ' 255 '」錯誤

每個磁片區 [最多](azure-netapp-files-resource-limits.md)可以有255個快照集。 當所有隨選、每小時、每日、每週和每月快照的總和超過最大值時，就會發生錯誤。 請降低 `snapshots to keep` 值，或刪除某些隨選快照集，然後再試一次。

## <a name="next-steps"></a>後續步驟  

* [管理快照集原則](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies)
