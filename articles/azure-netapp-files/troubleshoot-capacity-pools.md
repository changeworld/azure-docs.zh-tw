---
title: 針對 Azure NetApp Files 的容量集區問題進行疑難排解 |Microsoft Docs
description: 描述管理容量集區時可能發生的潛在問題，並提供問題的解決方案。
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
ms.date: 09/22/2020
ms.author: b-juche
ms.openlocfilehash: 0b5558501042dd7816202ea05b3a332b23400ff4
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91342025"
---
# <a name="troubleshoot-capacity-pool-issues"></a>針對容量集區問題進行疑難排解

本文說明管理容量集區時可能發生之問題的解決方式。 

## <a name="issues-creating-a-capacity-pool"></a>建立容量集區的問題

請確定容量集區計數不超過限制。 請參閱 [Azure NetApp Files 的資源限制](azure-netapp-files-resource-limits.md)。  如果計數小於限制，而且您仍然遇到問題，請提出支援票證，並指定容量集區名稱。

## <a name="issues-deleting-a-capacity-pool"></a>刪除容量集區的問題

確定您已在嘗試刪除容量集區的訂用帳戶中，移除所有的 Azure NetApp Files 磁片區和快照集。   

如果您已移除所有磁片區和快照集，但仍然無法刪除容量集區，則資源的參考可能仍會存在，而不會顯示在入口網站中。 在此情況下，請提出支援票證，並指定您已執行上述的建議步驟。 

## <a name="volume-creation-or-modification-fails-with-requested-throughput-not-available-error"></a>磁片區建立或修改失敗，並出現「要求的輸送量無法使用」錯誤

磁片區的可用輸送量取決於其容量集區的大小和服務層級。 如果沒有足夠的輸送量，您應該增加集區大小或調整現有的磁片區輸送量。


## <a name="next-steps"></a>後續步驟  

* [設定容量集區](azure-netapp-files-set-up-capacity-pool.md)
* [管理手動 QoS 容量集區](manage-manual-qos-capacity-pool.md)
