---
title: 使用 Azure NetApp Files 管理快照集 | Microsoft Docs
description: 說明如何使用 Azure NetApp Files 來建立、管理和使用快照集。
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
ms.date: 11/05/2020
ms.author: b-juche
ms.openlocfilehash: 0d7839b11e48e3e260f4d6b1323d1831e28222de
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2020
ms.locfileid: "93421856"
---
# <a name="manage-snapshots-by-using-azure-netapp-files"></a>使用 Azure NetApp Files 管理快照集

Azure NetApp Files 支援建立隨選快照集，並使用快照集原則來排程自動建立快照集。 您也可以將快照集還原至新的磁片區、使用用戶端還原單一檔案，或使用快照集還原現有的磁片區。

## <a name="create-an-on-demand-snapshot-for-a-volume"></a>建立磁碟區的隨選快照集

您可以視需要建立磁片區快照集。 

1.  移至您想要為其建立快照集的磁片區。 按一下 [ **快照** 集]。

    ![瀏覽快照集](../media/azure-netapp-files/azure-netapp-files-navigate-to-snapshots.png)

2.  按一下 [+ 新增快照集] 以為磁碟區建立隨選快照集。

    ![新增快照集](../media/azure-netapp-files/azure-netapp-files-add-snapshot.png)

3.  在 [新增快照集] 視窗中，為您要建立的新快照集提供名稱。   

    ![新增快照集](../media/azure-netapp-files/azure-netapp-files-new-snapshot.png)

4. 按一下 [確定]  。 

## <a name="manage-snapshot-policies"></a>管理快照集原則

您可以使用快照集原則來排程要自動執行的磁片區快照集。 您也可以視需要修改快照集原則，或刪除不再需要的快照集原則。  

### <a name="register-the-feature"></a>註冊功能

**快照集原則** 功能目前為預覽狀態。 如果您是第一次使用這項功能，您必須先註冊此功能。 

1. 註冊功能： 

    ```azurepowershell-interactive
    Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSnapshotPolicy
    ```

2. 檢查功能註冊的狀態： 

    > [!NOTE]
    > 在變更為之前， **>registrationstate** 可能會處於 `Registering` 最多60分鐘的狀態 `Registered` 。 等到狀態 **註冊** 後再繼續。

    ```azurepowershell-interactive
    Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSnapshotPolicy
    ```
您也可以使用 [Azure CLI 命令](/cli/azure/feature?preserve-view=true&view=azure-cli-latest) `az feature register` ，並 `az feature show` 註冊功能並顯示註冊狀態。 

### <a name="create-a-snapshot-policy"></a>建立快照集原則 

快照集原則可讓您指定每小時、每日、每週或每月迴圈的快照建立頻率。 您也需要指定要保留給磁片區的快照集數目上限。  

1.  從 NetApp 帳戶視圖中，按一下 [ **快照集原則** ]。

    ![快照集原則導覽](../media/azure-netapp-files/snapshot-policy-navigation.png)

2.  在 [快照集原則] 視窗中，將 [原則狀態] 設定為 [ **啟用** ]。 

3.  按一下 [ **每小時** ]、[ **每天** ]、[ **每週** ] 或 [ **每月** ] 索引標籤，建立每小時、每日、每週或每月 指定 **要保留的快照集數目** 。  

    查看有關磁片區允許的快照集數目上限的 [Azure NetApp Files 資源限制](azure-netapp-files-resource-limits.md) 。 

    下列範例顯示每小時的快照集原則設定。 

    ![每小時快照集原則](../media/azure-netapp-files/snapshot-policy-hourly.png)

    下列範例顯示每日快照集原則設定。

    ![每日快照集原則](../media/azure-netapp-files/snapshot-policy-daily.png)

    下列範例會顯示每週的快照集原則設定。

    ![每週快照集原則](../media/azure-netapp-files/snapshot-policy-weekly.png)

    下列範例顯示每月快照集原則設定。

    ![每月快照集原則](../media/azure-netapp-files/snapshot-policy-monthly.png) 

4.  按一下 [儲存]。  

如果您需要建立其他快照集原則，請重複步驟3。
您建立的原則會出現在 [快照集原則] 頁面中。

如果您想要讓磁片區使用快照集原則，您必須 [將原則套用至磁片](azure-netapp-files-manage-snapshots.md#apply-a-snapshot-policy-to-a-volume)區。 

### <a name="apply-a-snapshot-policy-to-a-volume"></a>將快照集原則套用至磁片區

如果您想要讓磁片區使用您所建立的快照集原則，您必須將原則套用至磁片區。 

1.  移至 [ **磁片** 區] 頁面，以滑鼠右鍵按一下您想要套用快照集原則的磁片區，然後選取 [ **編輯** ]。

    ![磁片區右鍵功能表](../media/azure-netapp-files/volume-right-cick-menu.png) 

2.  在 [編輯] 視窗的 [ **快照集原則** ] 底下，選取要用於磁片區的原則。  按一下 **[確定]** 以套用原則。  

    ![快照集原則編輯](../media/azure-netapp-files/snapshot-policy-edit.png) 

### <a name="modify-a-snapshot-policy"></a>修改快照集原則 

您可以修改現有的快照集原則，以變更原則狀態、快照頻率 (每小時、每天、每週或每月) ，或要保留的快照集數目。  
 
1.  從 NetApp 帳戶視圖中，按一下 [ **快照集原則** ]。

2.  在您要修改的快照集原則上按一下滑鼠右鍵，然後選取 [ **編輯** ]。

    ![快照集原則按右鍵功能表](../media/azure-netapp-files/snapshot-policy-right-click-menu.png) 

3.  在出現的 [快照集原則] 視窗中進行變更，然後按一下 [ **儲存** ]。 

### <a name="delete-a-snapshot-policy"></a>刪除快照集原則 

您可以刪除不再需要保留的快照集原則。   

1.  從 NetApp 帳戶視圖中，按一下 [ **快照集原則** ]。

2.  在您要修改的快照集原則上按一下滑鼠右鍵，然後選取 [ **刪除** ]。

    ![快照集原則按右鍵功能表](../media/azure-netapp-files/snapshot-policy-right-click-menu.png) 

3.  按一下 **[是]** 以確認您要刪除快照集原則。   

    ![快照集原則刪除確認](../media/azure-netapp-files/snapshot-policy-delete-confirm.png) 

## <a name="restore-a-snapshot-to-a-new-volume"></a>將快照集還原至新磁碟區

目前，您只能將快照集還原至新的磁碟區。 
1. 從 [磁片區] 分頁選取 **快照** 集，以顯示快照清單。 
2. 以滑鼠右鍵按一下要還原的快照集，然後從功能表選項中選取 [ **還原至新磁片** 區]。  

    ![將快照集還原至新磁碟區](../media/azure-netapp-files/azure-netapp-files-snapshot-restore-to-new-volume.png)

3. 在 [建立磁片區] 視窗中，提供新磁片區的資訊：  
    * **名稱**   
        為您要建立的磁碟區指定名稱。  
        
        名稱在資源群組內必須是唯一的。 長度至少必須有三個字元。  可以使用任何英數字元。

    * **配額**  
        指定您要配置給磁片區的邏輯儲存體數量。  

    ![還原至新的磁片區](../media/azure-netapp-files/snapshot-restore-new-volume.png) 

4. 按一下 [ **審核 + 建立** ]。  按一下頁面底部的 [新增]  。   
    新的磁片區會使用快照集所使用的相同通訊協定。   
    快照集還原到的新磁碟區會出現在 [磁碟區] 刀鋒視窗中。

## <a name="restore-a-file-from-a-snapshot-using-a-client"></a>使用用戶端從快照集還原檔案

如果您不想要將 [整個快照集還原到磁片](#restore-a-snapshot-to-a-new-volume)區，您可以選擇使用已掛接磁片區的用戶端，從快照集還原檔案。  

掛接的磁片區包含 NFS 用戶端中名為 (的快照集目錄  `.snapshot` ，) 或可供 `~snapshot` 用戶端存取的 SMB 用戶端) 中的 (。 快照集目錄包含對應到磁片區快照集的子目錄。 每個子目錄都包含快照集的檔案。 如果您不小心刪除或覆寫檔案，您可以將檔案從快照子目錄複寫到讀寫目錄，將檔案還原至父讀寫目錄。 

如果您在建立磁片區時選取 [隱藏快照路徑] 核取方塊，則會隱藏快照集目錄。 您可以藉由選取磁片區來查看磁片區的隱藏快照路徑狀態。 您可以按一下磁片區頁面上的 [ **編輯** ]，以編輯 [隱藏快照路徑] 選項。  

![編輯磁片區快照集選項](../media/azure-netapp-files/volume-edit-snapshot-options.png) 

### <a name="restore-a-file-by-using-a-linux-nfs-client"></a>使用 Linux NFS 用戶端來還原檔 

1. 使用 `ls` Linux 命令可列出您想要從目錄還原的檔案 `.snapshot` 。 

    例如︰

    `$ ls my.txt`   
    `ls: my.txt: No such file or directory`   

    `$ ls .snapshot`   
    `daily.2020-05-14_0013/              hourly.2020-05-15_1106/`   
    `daily.2020-05-15_0012/              hourly.2020-05-15_1206/`   
    `hourly.2020-05-15_1006/             hourly.2020-05-15_1306/`   

    `$ ls .snapshot/hourly.2020-05-15_1306/my.txt`   
    `my.txt`

2. 使用命令將檔案 `cp` 複製到上層目錄。  

    例如︰ 

    `$ cp .snapshot/hourly.2020-05-15_1306/my.txt .`   

    `$ ls my.txt`   
    `my.txt`   

### <a name="restore-a-file-by-using-a-windows-client"></a>使用 Windows 用戶端來還原檔 

1. 如果 `~snapshot` 已隱藏磁片區的目錄，請在上層目錄中顯示要顯示的 [隱藏專案](https://support.microsoft.com/help/4028316/windows-view-hidden-files-and-folders-in-windows-10) `~snapshot` 。

    ![顯示隱藏的項目](../media/azure-netapp-files/snapshot-show-hidden.png) 

2. 流覽至內的子目錄， `~snapshot` 以尋找您想要還原的檔案。  以滑鼠右鍵按一下該檔案。 選取 [複製]。  

    ![複製要還原的檔案](../media/azure-netapp-files/snapshot-copy-file-restore.png) 

3. 返回父目錄。 在上層目錄中按一下滑鼠右鍵，然後選取 `Paste` 將檔案貼到目錄中。

    ![貼上要還原的檔案](../media/azure-netapp-files/snapshot-paste-file-restore.png) 

4. 您也可以在上層目錄上按一下滑鼠右鍵， **選取 [** 內容]，按一下 [ **舊版** ] 索引標籤以查看快照集清單，然後選取 [ **還原** ] 來還原檔案。  

    ![先前的屬性版本](../media/azure-netapp-files/snapshot-properties-previous-version.png) 

## <a name="revert-a-volume-using-snapshot-revert"></a>使用快照集還原復原磁碟區

快照集還原功能可讓您快速地將磁片區還原到取得特定快照集時的狀態。 在大部分的情況下，復原磁碟區的速度遠快于從快照集還原至使用中檔案系統的個別檔案。 相較于將快照集還原至新的磁片區，這種方式也更有空間效益。 

您可以在磁片區的 [快照集] 功能表中找到 [復原磁碟區] 選項。 在您選取回復的快照集之後，Azure NetApp Files 會將磁片區還原至所選快照集所包含的資料和時間戳記。 

> [!IMPORTANT]
> 建立所選快照集之後所建立的作用中檔案系統資料和快照集將會遺失。 快照集還原作業會將目標磁片區中的 *所有* 資料取代為所選快照集內的資料。 選取快照集時，您應該注意快照集的內容和建立日期。 您無法復原快照集還原作業。

1. 移至磁片區的 [ **快照** 集] 功能表。  以滑鼠右鍵按一下要用於還原作業的快照集。 選取 [ **復原磁碟** 區]。 

    ![描述快照之右鍵功能表的螢幕擷取畫面](../media/azure-netapp-files/snapshot-right-click-menu.png) 

2. 在 [將磁片區還原為快照集] 視窗中，輸入磁片區的名稱，然後按一下 [ **還原** ]。   

    磁片區現在會還原至所選快照集的時間點。

    ![將磁片區還原至快照集視窗的螢幕擷取畫面](../media/azure-netapp-files/snapshot-revert-volume.png) 

## <a name="delete-snapshots"></a>刪除快照集  

您可以刪除不再需要保留的快照。 

1. 移至磁片區的 [ **快照** 集] 功能表。 以滑鼠右鍵按一下您要刪除的快照集。 選取 [刪除]。

    ![描述快照之右鍵功能表的螢幕擷取畫面](../media/azure-netapp-files/snapshot-right-click-menu.png) 

2. 在 [刪除快照集] 視窗中，按一下 [ **是]** ，確認您要刪除該快照集。 

    ![確認刪除快照集的螢幕擷取畫面](../media/azure-netapp-files/snapshot-confirm-delete.png)  

## <a name="next-steps"></a>後續步驟

* [針對快照集原則進行疑難排解](troubleshoot-snapshot-policies.md)
* [Azure NetApp Files 的資源限制](azure-netapp-files-resource-limits.md)
* [Azure NetApp Files 快照101影片](https://www.youtube.com/watch?v=uxbTXhtXCkw&feature=youtu.be)