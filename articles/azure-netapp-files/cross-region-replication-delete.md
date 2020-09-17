---
title: 刪除 Azure NetApp Files 跨區域複寫複製Microsoft Docs
description: 描述如何刪除來源和目的地磁片區之間不再需要的複寫連接。
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
ms.topic: how-to
ms.date: 09/16/2020
ms.author: b-juche
ms.openlocfilehash: 0904ac36a9453e51dbb1efc50eee2b9bf3c669c7
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/16/2020
ms.locfileid: "90708586"
---
# <a name="delete-replications"></a>刪除複寫

您可以藉由刪除磁片區複寫，在來源與目的地磁片區之間終止複寫連接。 您可以從來源或目的地磁片區執行刪除操作。 刪除作業只會移除複寫的授權;它不會移除來源或目的地磁片區。 

## <a name="steps"></a>步驟

1. 若要刪除磁片區複寫，請選取來源或目的地磁片 **區的複寫** 。  

2. 按一下 **[刪除]** 。    

3. 輸入 **[是]** ，然後按一下 [ **刪除**]，以確認刪除。   

    ![刪除複寫](../media/azure-netapp-files/cross-region-replication-delete-replication.png)

## <a name="next-steps"></a>下一步  

* [跨區域複寫](cross-region-replication-introduction.md)
* [使用跨區域複寫的需求和考慮](cross-region-replication-requirements-considerations.md)
* [顯示覆寫關聯性的健全狀況狀態](cross-region-replication-display-health-status.md)
* [針對跨區域複寫進行疑難排解](troubleshoot-cross-region-replication.md)

