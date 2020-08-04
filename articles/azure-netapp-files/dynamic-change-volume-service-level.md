---
title: 動態變更適用于 Azure NetApp Files 的磁片區服務等級 |Microsoft Docs
description: 描述如何動態變更磁片區的服務等級。
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
ms.date: 07/24/2020
ms.author: b-juche
ms.openlocfilehash: e19db61efbf93e3191d5780d07952f3d195c7a59
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2020
ms.locfileid: "87533034"
---
# <a name="dynamically-change-the-service-level-of-a-volume"></a>動態變更磁碟區的服務等級

您可以藉由將磁片區移到另一個使用您想要用於磁片區之[服務等級](azure-netapp-files-service-levels.md)的容量集區，來變更現有磁片區的服務等級。 此磁片區的就地服務層級變更不需要您遷移資料。 它也不會影響磁片區的存取權。  

此功能可讓您依需求滿足您的工作負載需求。  您可以變更現有的磁片區，以使用較高的服務等級以獲得更好的效能，或使用較低的服務等級來進行成本優化。 例如，如果磁片區目前在使用*標準*服務層級的容量集區中，而您想要讓磁片區使用*premium*服務層級，您可以將磁片區動態移到使用*premium*服務層級的容量集區。  

您想要移動磁片區的容量集區必須已經存在。 容量集區可以包含其他磁片區。  如果您想要將磁片區移到全新的容量集區，您必須先[建立容量集](azure-netapp-files-set-up-capacity-pool.md)區，再移動磁片區。  

## <a name="considerations"></a>考量

* 將磁片區移到另一個容量集區之後，您將無法再存取先前的大量活動記錄和磁片區計量。 磁片區會以新的容量集區下的新活動記錄和計量作為開頭。

* 如果您將磁片區移到較高服務層級的容量集區（例如，從*標準*移至*premium*或*Ultra*服務層級），您必須至少等候七天，才能再次將磁片區移至較低服務層級的容量集區（例如，從*Ultra*移至*premium*或*Standard*）。  
如果您將磁片區移到具有相同服務層級或較低服務層級的容量集區，則不適用此等候期間。

## <a name="register-the-feature"></a>註冊功能

將磁片區移到另一個容量集區的功能目前為預覽狀態。 如果您是第一次使用這項功能，您必須先註冊此功能。

1. 註冊功能： 

    ```azurepowershell-interactive
    Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFTierChange
    ```

2. 檢查功能註冊的狀態： 

    > [!NOTE]
    > 在變更為之前， **RegistrationState**可能處於 `Registering` 數分鐘的狀態 `Registered` 。 等候狀態為 [**已註冊**]，再繼續進行。

    ```azurepowershell-interactive
    Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFTierChange
    ```

## <a name="move-a-volume-to-another-capacity-pool"></a>將磁片區移到另一個容量集區

1.  在 [磁片區] 頁面上，以滑鼠右鍵按一下您要變更其服務等級的磁片區。 選取 [**變更集**區]。

    ![以滑鼠右鍵按一下 [磁片區]](../media/azure-netapp-files/right-click-volume.png)

2. 在 [變更集區] 視窗中，選取您想要移動磁片區的容量集區。 

    ![變更集區](../media/azure-netapp-files/change-pool.png)

3.  按一下 [確定]。


## <a name="next-steps"></a>後續步驟  

* [Azure NetApp Files 的服務等級](azure-netapp-files-service-levels.md)
* [設定容量集區](azure-netapp-files-set-up-capacity-pool.md)
