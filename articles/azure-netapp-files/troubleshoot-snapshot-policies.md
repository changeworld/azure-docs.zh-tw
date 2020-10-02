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
ms.openlocfilehash: 6ba8b18876bdae2754a6a772ce3909ff2f5a71b7
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/02/2020
ms.locfileid: "91650999"
---
# <a name="troubleshoot-snapshot-policies"></a>針對快照集原則進行疑難排解

本文說明您在管理 Azure NetApp Files 快照集原則時可能遇到的錯誤案例。 它也提供可協助您解決問題的解決方案。

## <a name="error-conditions-and-resolutions"></a>錯誤狀況和解決方式 

|     錯誤狀況    |     解決方案    |
|-|-|
| 因為快照集原則名稱無效，所以建立快照集原則失敗。 | 如果快照集原則名稱無效，則在建立快照集原則時，就會發生錯誤。 下列指導方針適用于快照集原則名稱：  <ul><li> 快照集原則名稱不能包含非 ASCII 或特殊字元。 </li> <li> 快照集原則名稱必須以字母或數位開頭，而且只能包含字母、數位、底線 ( ' _ ' ) 和連字號 ( '-' ) 。 </li> <li> 快照集原則名稱必須介於1到64個字元之間。  </li></ul> 根據指導方針修改快照集原則名稱。  |
| 快照集原則建立失敗，值無效。 | 如果您輸入不正確欄位值（例如或），則 Azure NetApp Files 無法建立快照集 `Number of snapshots to keep` 原則 `Minute on the hour to take snapshot` 。 有效值如下：  <ul><li>值必須是有效的數位。</li> <li>值必須介於0到59之間。</li></ul> 請確定已為欄位提供有效的值。 | 
| 因為發生錯誤，所以快照集原則建立失敗 `Total number of snapshots to keep exceeds 255` 。 | 每個磁片區 [最多](azure-netapp-files-resource-limits.md)可以有255個快照集。 最大值包含所有每小時、每日、每週和每月快照的總和。 <br> 請減少 `Snapshots to keep` 該值，然後再試一次。 |
| 將原則指派給磁片區失敗，並出現錯誤 `Total snapshot policy is over the max '255'` 。 | 每個磁片區 [最多](azure-netapp-files-resource-limits.md)可以有255個快照集。 當所有隨選、每小時、每日、每週和每月快照的總和超過最大值時，就會發生錯誤。 <br> 請降低 `snapshots to keep` 值，或刪除某些隨選快照集，然後再試一次。 | 

## <a name="next-steps"></a>後續步驟  

* [管理快照集原則](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies)
