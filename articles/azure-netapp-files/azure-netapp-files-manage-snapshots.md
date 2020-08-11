---
title: 使用 Azure NetApp Files 管理快照集 | Microsoft Docs
description: 說明如何使用 Azure NetApp Files 來建立和管理快照集。
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
ms.openlocfilehash: 85990aee5143c9ccc0362a00597a748763977204
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/11/2020
ms.locfileid: "88080210"
---
# <a name="manage-snapshots-by-using-azure-netapp-files"></a>使用 Azure NetApp Files 管理快照集

Azure NetApp Files 支援建立隨選快照集，並使用快照集原則來排程自動建立快照集。  您也可以將快照集還原至新的磁片區。  

## <a name="create-an-on-demand-snapshot-for-a-volume"></a>建立磁碟區的隨選快照集

您可以視需要建立磁片區快照集。 

1.  移至您想要建立快照集的磁片區。 按一下 [**快照**集]。

    ![瀏覽快照集](../media/azure-netapp-files/azure-netapp-files-navigate-to-snapshots.png)

2.  按一下 [+ 新增快照集]**** 以為磁碟區建立隨選快照集。

    ![新增快照集](../media/azure-netapp-files/azure-netapp-files-add-snapshot.png)

3.  在 [新增快照集] 視窗中，為您要建立的新快照集提供名稱。   

    ![新增快照集](../media/azure-netapp-files/azure-netapp-files-new-snapshot.png)

4. 按一下 [確定]。 

## <a name="manage-snapshot-policies"></a>管理快照集原則

您可以使用快照集原則，將磁片區快照集排程為自動執行。 您也可以視需要修改快照集原則，或刪除不再需要的快照集原則。  

### <a name="register-the-feature"></a>註冊功能

**快照集原則**功能目前為預覽狀態。 如果您是第一次使用這項功能，您必須先註冊此功能。 

1. 註冊功能： 

    ```azurepowershell-interactive
    Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSnapshotPolicy
    ```

2. 檢查功能註冊的狀態： 

    > [!NOTE]
    > **RegistrationState** `Registering` 最多可能處於60分鐘的狀態，然後再變更為 `Registered` 。 等候狀態為 [**已註冊**]，再繼續進行。

    ```azurepowershell-interactive
    Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSnapshotPolicy
    ```

### <a name="create-a-snapshot-policy"></a>建立快照集原則 

快照集原則可讓您指定每小時、每日、每週或每月迴圈的快照集建立頻率。 您也需要指定磁片區要保留的快照集數目上限。  

1.  從 [NetApp 帳戶] 視圖中，按一下 [**快照集原則**]。

    ![快照集原則導覽](../media/azure-netapp-files/snapshot-policy-navigation.png)

2.  在 [快照集原則] 視窗中，將 [原則狀態] 設定為 [**啟用**]。 

3.  按一下 [**每小時**]、[**每天**]、[**每週**] 或 [**每月**] 索引標籤，以建立每小時、每天、每週或每月 指定**要保留的快照集數目**。  

    如需磁片區允許的快照集數目上限，請參閱[Azure NetApp Files 的資源限制](azure-netapp-files-resource-limits.md)。 

    下列範例會顯示每小時快照集原則設定。 

    ![每小時快照集原則](../media/azure-netapp-files/snapshot-policy-hourly.png)

    下列範例會顯示每日快照集原則設定。

    ![每日快照集原則](../media/azure-netapp-files/snapshot-policy-daily.png)

    下列範例會顯示每週的快照集原則設定。

    ![每週快照集原則](../media/azure-netapp-files/snapshot-policy-weekly.png)

    下列範例會顯示每月快照集原則設定。

    ![每月快照集原則](../media/azure-netapp-files/snapshot-policy-monthly.png) 

4.  按一下 [儲存]。  

如果您需要建立其他快照集原則，請重複步驟3。
您所建立的原則會顯示在 [快照集原則] 頁面中。

如果您想要讓磁片區使用快照集原則，則必須[將該原則套用至磁片](azure-netapp-files-manage-snapshots.md#apply-a-snapshot-policy-to-a-volume)區。 

### <a name="apply-a-snapshot-policy-to-a-volume"></a>將快照集原則套用至磁片區

如果您想要讓磁片區使用您所建立的快照集原則，則必須將該原則套用至磁片區。 

1.  移至 [**磁片**區] 頁面，以滑鼠右鍵按一下您想要套用快照集原則的磁片區，然後選取 [**編輯**]。

    ![磁片區滑鼠右鍵功能表](../media/azure-netapp-files/volume-right-cick-menu.png) 

2.  在 [編輯] 視窗的 [**快照集原則**] 底下，選取要用於磁片區的原則。  按一下 **[確定]** 以套用原則。  

    ![快照集原則編輯](../media/azure-netapp-files/snapshot-policy-edit.png) 

### <a name="modify-a-snapshot-policy"></a>修改快照集原則 

您可以修改現有的快照集原則，以變更原則狀態、快照集頻率 (每小時、每日、每週或每月) ，或要保留的快照集數目。  
 
1.  從 [NetApp 帳戶] 視圖中，按一下 [**快照集原則**]。

2.  以滑鼠右鍵按一下您要修改的快照集原則，然後選取 [**編輯**]。

    ![快照集原則右鍵功能表](../media/azure-netapp-files/snapshot-policy-right-click-menu.png) 

3.  在出現的 [快照集原則] 視窗中進行變更，然後按一下 [**儲存**]。 

### <a name="delete-a-snapshot-policy"></a>刪除快照集原則 

您可以刪除不再需要保留的快照集原則。   

1.  從 [NetApp 帳戶] 視圖中，按一下 [**快照集原則**]。

2.  以滑鼠右鍵按一下您想要修改的快照集原則，然後選取 [**刪除**]。

    ![快照集原則右鍵功能表](../media/azure-netapp-files/snapshot-policy-right-click-menu.png) 

3.  按一下 **[是]** ，確認您想要刪除快照集原則。   

    ![快照集原則刪除確認](../media/azure-netapp-files/snapshot-policy-delete-confirm.png) 

## <a name="restore-a-snapshot-to-a-new-volume"></a>將快照集還原至新磁碟區

目前，您只能將快照集還原至新的磁碟區。 
1. 從 [磁片區] 分頁選取 [**快照**集]，以顯示快照清單。 
2. 在要還原的快照集上按一下滑鼠右鍵，然後從功能表選項中選取 [**還原至新的磁片**區]。  

    ![將快照集還原至新磁碟區](../media/azure-netapp-files/azure-netapp-files-snapshot-restore-to-new-volume.png)

3. 在 [建立磁片區] 視窗中，提供新磁片區的資訊：  
    * **檔案名**   
        為您要建立的磁碟區指定名稱。  
        
        名稱在資源群組內必須是唯一的。 長度至少必須有三個字元。  可以使用任何英數字元。

    * **配額**  
        指定您要配置給磁片區的邏輯儲存體數量。  

    ![還原到新的磁片區](../media/azure-netapp-files/snapshot-restore-new-volume.png) 

4. 按一下 [**檢查 + 建立**]。  按一下 [建立]。   
    新的磁片區會使用快照集所使用的相同通訊協定。   
    快照集還原到的新磁碟區會出現在 [磁碟區] 刀鋒視窗中。

## <a name="next-steps"></a>後續步驟

* [了解 Azure NetApp Files 的儲存體階層](azure-netapp-files-understand-storage-hierarchy.md)
* [Azure NetApp Files 的資源限制](azure-netapp-files-resource-limits.md)
