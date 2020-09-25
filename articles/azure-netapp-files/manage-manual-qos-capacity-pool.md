---
title: 管理 Azure NetApp Files 的手動 QoS 容量集區 |Microsoft Docs
description: 說明如何管理使用手動 QoS 類型的容量集區，包括設定手動 QoS 容量集區，以及將容量集區變更為使用手動 QoS。
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
ms.date: 09/22/2020
ms.author: b-juche
ms.openlocfilehash: 1bbd6ad9e33aab8d3564865b86485d70df5a108f
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91342026"
---
# <a name="manage-a-manual-qos-capacity-pool"></a>管理手動 QoS 容量集區

本文說明如何管理使用手動 QoS 類型的容量集區。  

請參閱 azure netapp files 的 [儲存體](azure-netapp-files-understand-storage-hierarchy.md) 階層和 [azure Netapp files 的效能考慮](azure-netapp-files-performance-considerations.md) ，以瞭解 QoS 類型的考慮。  

## <a name="register-the-feature"></a>註冊功能
手動 QoS 類型功能目前為預覽狀態。 如果您是第一次使用這項功能，就必須先註冊該功能。
  
1.  註冊功能：

    ```azurepowershell-interactive
    Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFFlexPool
    ```

2. 檢查功能註冊的狀態： 

    > [!NOTE]
    > 在變更為之前， **>registrationstate** 可能會處於 `Registering` 最多60分鐘的狀態 `Registered` 。 等到狀態 **註冊** 後再繼續。

    ```azurepowershell-interactive
    Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFFlexPool
    ```
您也可以使用 [Azure CLI 命令](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest&preserve-view=true) `az feature register` ，並 `az feature show` 註冊功能並顯示註冊狀態。 

## <a name="set-up-a-new-manual-qos-capacity-pool"></a>設定新的手動 QoS 容量集區 

使用手動 QoS 類型建立新的容量集區：

1. 依照 [設定容量集](azure-netapp-files-set-up-capacity-pool.md)區中的步驟執行。  

2. 在 [新增容量集區] 視窗中，選取 [ **手動 QoS** ] 類型。  

## <a name="change-a-capacity-pool-to-use-manual-qos"></a><a name="change-to-qos"></a>變更容量集區以使用手動 QoS

您可以將目前使用自動 QoS 類型的容量集區變更為使用手動 QoS 類型。  

> [!IMPORTANT]
> 將容量類型設定為手動 QoS 是永久性變更。 您無法將手動 QoS 類型容量工具轉換成自動 QoS 容量集區。 

1. 從您 NetApp 帳戶的管理分頁，按一下 [ **容量** 集區] 以顯示現有的容量集區。   
 
2.  按一下您想要變更為使用手動 QoS 的容量集區。

3.  按一下 [ **變更 QoS 類型**]。 然後將 **新的 QoS 類型** 設定為 [ **手動**]。 按一下 [確定]。 

![變更 QoS 類型](../media/azure-netapp-files/change-qos-type.png)


## <a name="monitor-the-throughput-of-a-manual-qos-capacity-pool"></a>監視手動 QoS 容量集區的輸送量  

計量可協助您監視磁片區的讀取和寫入輸送量。  請參閱 [Azure NetApp Files 的計量](azure-netapp-files-metrics.md)。  

## <a name="modify-the-allotted-throughput-of-a-manual-qos-volume"></a>修改手動 QoS 磁片區的分配輸送量 

如果磁片區包含在手動 QoS 容量集區中，您可以視需要修改分配的磁片區輸送量。

1. 在 [ **磁片** 區] 頁面上，選取您要修改其輸送量的磁片區。   

2. 按一下 [ **變更輸送量**]。 指定您想要 ** (MiB/S) 的輸送量 ** 。 按一下 [確定]。 

    ![變更 QoS 輸送量](../media/azure-netapp-files/change-qos-throughput.png)

## <a name="next-steps"></a>後續步驟  

* [設定容量集區](azure-netapp-files-set-up-capacity-pool.md)
* [Azure NetApp Files 的計量](azure-netapp-files-metrics.md)
* [適用於 Azure NetApp Files 的效能考量](azure-netapp-files-performance-considerations.md)
* [針對容量集區問題進行疑難排解](troubleshoot-capacity-pools.md)
