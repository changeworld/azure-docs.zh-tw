---
title: 設定 Azure NetApp Files 的容量集區 | Microsoft Docs
description: 說明如何設定容量集區，以便您可以在其中建立磁碟區。
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
ms.date: 04/02/2020
ms.author: b-juche
ms.openlocfilehash: d76af4901103b0eed8cd1cffac744f8fb41d9689
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85483494"
---
# <a name="set-up-a-capacity-pool"></a>設定容量集區

設定容量集區可讓您在其中建立磁碟區。  

## <a name="before-you-begin"></a>開始之前 

您必須已經建立 NetApp 帳戶。   

[建立 NetApp 帳戶](azure-netapp-files-create-netapp-account.md)

## <a name="steps"></a>步驟 

1. 請移至 NetApp 帳戶的 [管理] 刀鋒視窗，然後在導覽窗格中按一下 [容量集區]****。  
    
    ![瀏覽至容量集區](../media/azure-netapp-files/azure-netapp-files-navigate-to-capacity-pool.png)

2. 按一下 [+ 新增集區]**** 來建立新的容量集區。   
    [新增容量集區] 視窗隨即出現。

3. 請提供新容量集區的下列資訊：  
   * **名稱**  
     指定容量集區的名稱。  
     每個 NetApp 帳戶的容量集區名稱必須是唯一的。

   * **服務等級**   
     此欄位會顯示容量集區的目標效能。  
     指定容量集區的服務層級： [**Ultra**](azure-netapp-files-service-levels.md#Ultra)、 [**Premium**](azure-netapp-files-service-levels.md#Premium)或[**Standard**](azure-netapp-files-service-levels.md#Standard)。

   * **容量**     
     指定您購買的容量集區大小。        
     最小容量集區大小是 4 TB。 您可以建立大小是 4 TB 倍數的集區。   
      
     ![新增容量集區](../media/azure-netapp-files/azure-netapp-files-new-capacity-pool.png)

4. 按一下 [確定]。

## <a name="next-steps"></a>後續步驟 

- [Azure NetApp Files 的服務等級](azure-netapp-files-service-levels.md)
- 如需不同服務層級的價格，請參閱 [Azure NetApp Files 定價頁面](https://azure.microsoft.com/pricing/details/storage/netapp/)
- [將子網路委派至 Azure NetApp Files](azure-netapp-files-delegate-subnet.md)
