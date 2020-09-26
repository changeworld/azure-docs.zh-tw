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
ms.date: 08/26/2020
ms.author: b-juche
ms.openlocfilehash: 9050982338c4a6096ef180b34c0d0a0dca931427
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91278306"
---
# <a name="dynamically-change-the-service-level-of-a-volume"></a>動態變更磁碟區的服務等級

您可以藉由將磁片區移至另一個使用磁片區所需 [服務層級](azure-netapp-files-service-levels.md) 的容量集區，來變更現有磁片區的服務層級。 此磁片區的就地服務層級變更不需要您遷移資料。 它也不會影響對磁片區的存取。  

此功能可讓您依需求滿足您的工作負載需求。  您可以變更現有的磁片區，以使用較高的服務層級來提高效能，或使用較低的服務層級來進行成本優化。 例如，如果磁片區目前位於使用 *標準* 服務層級的容量集區，而您想要讓磁片區使用 *premium* 服務層級，您可以將磁片區動態移至使用 *premium* 服務層級的容量集區。  

您要移動磁片區的容量集區必須已經存在。 容量集區可包含其他磁片區。  如果您想要將磁片區移至全新的容量集區，您必須先 [建立容量集](azure-netapp-files-set-up-capacity-pool.md) 區，然後再移動磁片區。  

## <a name="considerations"></a>考量

* 將磁片區移至另一個容量集區之後，您就無法再存取先前的磁片區活動記錄和磁片區計量。 磁片區會從新的容量集區下的新活動記錄和計量開始。

* 如果您將磁片區移至較高服務層級的容量集區 (例如，從 *標準* 移至 *Premium* 或 *Ultra* 服務層級) ，您必須至少等候七天，才能將該磁片區 *再次* 移至較低服務層級的容量集區 (例如，從 *Ultra* 移至 *Premium* 或 *標準*) 。  

## <a name="register-the-feature"></a>註冊功能

將磁片區移至另一個容量集區的功能目前為預覽狀態。 如果您是第一次使用這項功能，就必須先註冊該功能。

1. 註冊功能： 

    ```azurepowershell-interactive
    Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFTierChange
    ```

2. 檢查功能註冊的狀態： 

    > [!NOTE]
    > 在變更為之前， **>registrationstate** 可能會處於 `Registering` 最多60分鐘的狀態 `Registered` 。 等到狀態 **註冊** 後再繼續。

    ```azurepowershell-interactive
    Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFTierChange
    ```
您也可以使用 [Azure CLI 命令](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest&preserve-view=true) `az feature register` ，並 `az feature show` 註冊功能並顯示註冊狀態。 

## <a name="move-a-volume-to-another-capacity-pool"></a>將磁片區移至另一個容量集區

1.  在 [磁片區] 頁面上，以滑鼠右鍵按一下您要變更其服務層級的磁片區。 選取 [ **變更集**區]。

    ![以滑鼠右鍵按一下 [磁片區]](../media/azure-netapp-files/right-click-volume.png)

2. 在 [變更集區] 視窗中，選取您要移動磁片區的容量集區。 

    ![變更集區](../media/azure-netapp-files/change-pool.png)

3.  按一下 [確定]。


## <a name="next-steps"></a>後續步驟  

* [Azure NetApp Files 的服務等級](azure-netapp-files-service-levels.md)
* [設定容量集區](azure-netapp-files-set-up-capacity-pool.md)
