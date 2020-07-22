---
title: 什麼是 Azure NetApp Files 的儲存體階層 | Microsoft Docs
description: 描述儲存體階層，包括 Azure NetApp Files 帳戶、容量集區和磁碟區。
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
ms.topic: overview
ms.date: 07/15/2020
ms.author: b-juche
ms.openlocfilehash: 0b150491fff953434062cc583566e1113947a679
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86504898"
---
# <a name="what-is-the-storage-hierarchy-of-azure-netapp-files"></a>什麼是 Azure NetApp Files 的儲存體階層

在 Azure NetApp Files 中建立磁碟區之前，您必須先購買及設定佈建容量的集區。  若要設定容量集區，您必須具有 NetApp 帳戶。 了解儲存體階層有助於設定和管理您的 Azure NetApp Files 資源。

> [!IMPORTANT] 
> Azure NetApp Files 目前不支援訂用帳戶之間的資源移轉。

## <a name="netapp-accounts"></a><a name="azure_netapp_files_account"></a>NetApp 帳戶

- NetApp 帳戶可充當組成容量集區的系統管理群組。  
- NetApp 帳戶與一般 Azure 儲存體帳戶不同。 
- NetApp 帳戶的範圍具有區域性。   
- 您在一個區域中可以有多個 NetApp 帳戶，但是每個 NetApp 帳戶都只繫結至單一區域。

## <a name="capacity-pools"></a><a name="capacity_pools"></a>容量集區

- 容量集區是依據其佈建容量進行測量。  
- 容量是以您購買的固定 SKU (例如 4-TiB 容量) 進行佈建。
- 容量集區只能有一個服務等級。  
- 每個容量集區都只能屬於一個 NetApp 帳戶。 不過，您的 NetApp 帳戶內可以有多個容量集區。  
- 容量集區不能跨越 NetApp 帳戶移動。   
  例如，在下方[儲存體階層的概念圖表](#conceptual_diagram_of_storage_hierarchy)中，容量集區 1 無法從美國東部 NetApp 帳戶移至美國西部 2 NetApp 帳戶。  
- 必須先刪除容量集區內的所有磁碟區之後，才能刪除容量集區。

## <a name="volumes"></a><a name="volumes"></a>磁碟區

- 磁碟區會依據邏輯容量耗用量進行測量，且可以調整。 
- 磁碟區的容量耗用量是根據其集區的佈建容量進行計算。
- 每個磁碟區都只屬於一個集區，但是一個集區可以包含多個磁碟區。 
- 磁碟區不能跨越容量集區移動。 <!--Within the same NetApp account, you can move a volume across pools.  -->   
  例如，在下方的[儲存體階層的概念圖表](#conceptual_diagram_of_storage_hierarchy)中，您可以將磁碟區從容量集區 1 移至容量集區 2。

## <a name="conceptual-diagram-of-storage-hierarchy"></a><a name="conceptual_diagram_of_storage_hierarchy"></a>儲存體階層的概念圖表 
下列範例顯示 Azure 訂用帳戶、NetApp 帳戶、容量集區和磁碟區之間的關係。   

![儲存體階層的概念圖表](../media/azure-netapp-files/azure-netapp-files-storage-hierarchy.png)

## <a name="next-steps"></a>後續步驟

- [Azure NetApp Files 的資源限制](azure-netapp-files-resource-limits.md)
- [註冊 Azure NetApp Files](azure-netapp-files-register.md)
