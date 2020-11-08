---
title: 動態變更 Azure NetApp Files 的磁片區服務層級 |Microsoft Docs
description: 說明如何動態變更磁片區的服務層級。
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
ms.date: 11/06/2020
ms.author: b-juche
ms.openlocfilehash: fe4b2925a34ae7c06bb0b597f0bcdcc3f4d80896
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2020
ms.locfileid: "94363216"
---
# <a name="dynamically-change-the-service-level-of-a-volume"></a>動態變更磁碟區的服務等級

> [!IMPORTANT] 
> 這項功能的公開預覽註冊會持續保留，直到進一步通知為止。 

您可以藉由將磁片區移至另一個使用磁片區所需 [服務層級](azure-netapp-files-service-levels.md) 的容量集區，來變更現有磁片區的服務層級。 此磁碟區的就地服務層級變更不需要您遷移資料。 它也不會影響對磁片區的存取。  

此功能可讓您依需求滿足您的工作負載需求。  您可以變更現有的磁碟區，以使用較高的服務層級來獲得更好的效能，或使用較低的服務等級來獲得成本最佳化。 例如，如果磁片區目前位於使用 *標準* 服務層級的容量集區，而您想要讓磁片區使用 *premium* 服務層級，您可以將磁片區動態移至使用 *premium* 服務層級的容量集區。  

您要移動磁片區的容量集區必須已經存在。 容量集區可包含其他磁片區。  如果您想要將磁片區移至全新的容量集區，您必須先 [建立容量集](azure-netapp-files-set-up-capacity-pool.md) 區，然後再移動磁片區。  

## <a name="considerations"></a>考量

* 將磁片區移至另一個容量集區之後，您就無法再存取先前的磁片區活動記錄和磁片區計量。 磁片區會從新的容量集區下的新活動記錄和計量開始。

* 如果您將磁片區移至較高服務層級的容量集區 (例如，從 *標準* 移至 *Premium* 或 *Ultra* 服務層級) ，您必須至少等候七天，才能將該磁片區 *再次* 移至較低服務層級的容量集區 (例如，從 *Ultra* 移至 *Premium* 或 *標準* ) 。  
<!-- 
## Register the feature

The feature to move a volume to another capacity pool is currently in preview. If you are using this feature for the first time, you need to register the feature first.

1. Register the feature: 

    ```azurepowershell-interactive
    Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFTierChange
    ```

2. Check the status of the feature registration: 

    > [!NOTE]
    > The **RegistrationState** may be in the `Registering` state for up to 60 minutes before changing to`Registered`. Wait until the status is **Registered** before continuing.

    ```azurepowershell-interactive
    Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFTierChange
    ```
You can also use [Azure CLI commands](/cli/azure/feature?preserve-view=true&view=azure-cli-latest) `az feature register` and `az feature show` to register the feature and display the registration status. 
--> 
## <a name="move-a-volume-to-another-capacity-pool"></a>將磁片區移至另一個容量集區

1.  在 [磁片區] 頁面上，以滑鼠右鍵按一下您要變更其服務層級的磁片區。 選取 [ **變更集** 區]。

    ![以滑鼠右鍵按一下 [磁片區]](../media/azure-netapp-files/right-click-volume.png)

2. 在 [變更集區] 視窗中，選取您要移動磁片區的容量集區。 

    ![變更集區](../media/azure-netapp-files/change-pool.png)

3.  按一下 [確定]。


## <a name="next-steps"></a>後續步驟  

* [Azure NetApp Files 的服務等級](azure-netapp-files-service-levels.md)
* [設定容量集區](azure-netapp-files-set-up-capacity-pool.md)
