---
title: 使用 Azure NetApp Files 管理快照集 | Microsoft Docs
description: 說明如何使用 Azure NetApp Files 建立及管理快照集。
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
ms.date: 09/04/2020
ms.author: b-juche
ms.openlocfilehash: e1be0879af02fac0f7ae926a02ea23fd6be84de4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91325684"
---
# <a name="manage-snapshots-by-using-azure-netapp-files"></a>使用 Azure NetApp Files 管理快照集

Azure NetApp Files 支援建立隨選快照集，並使用快照集原則來排程自動建立快照集。  您也可以將快照集還原至新的磁片區，或使用用戶端來還原單一檔案。  

## <a name="create-an-on-demand-snapshot-for-a-volume"></a>建立磁碟區的隨選快照集

您可以視需要建立磁片區快照集。 

1.  移至您想要為其建立快照集的磁片區。 按一下 [ **快照**集]。

    ![瀏覽快照集](../media/azure-netapp-files/azure-netapp-files-navigate-to-snapshots.png)

2.  按一下 [+ 新增快照集]**** 以為磁碟區建立隨選快照集。

    ![新增快照集](../media/azure-netapp-files/azure-netapp-files-add-snapshot.png)

3.  在 [新增快照集] 視窗中，為您要建立的新快照集提供名稱。   

    ![新增快照集](../media/azure-netapp-files/azure-netapp-files-new-snapshot.png)

4. 按一下 [確定]。 

## <a name="manage-snapshot-policies"></a>管理快照集原則

您可以使用快照集原則來排程要自動執行的磁片區快照集。 您也可以視需要修改快照集原則，或刪除不再需要的快照集原則。  

### <a name="register-the-feature"></a>註冊功能

**快照集原則**功能目前為預覽狀態。 如果您是第一次使用這項功能，就必須先註冊該功能。 

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
您也可以使用 [Azure CLI 命令](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest&preserve-view=true) `az feature register` ，並 `az feature show` 註冊功能並顯示註冊狀態。 

### <a name="create-a-snapshot-policy"></a>建立快照集原則 

快照集原則可讓您指定每小時、每日、每週或每月迴圈的快照建立頻率。 您也需要指定要保留給磁片區的快照集數目上限。  

1.  從 NetApp 帳戶視圖中，按一下 [ **快照集原則**]。

    ![快照集原則導覽](../media/azure-netapp-files/snapshot-policy-navigation.png)

2.  在 [快照集原則] 視窗中，將 [原則狀態] 設定為 [ **啟用**]。 

3.  按一下 [ **每小時**]、[ **每天**]、[ **每週**] 或 [ **每月** ] 索引標籤，建立每小時、每日、每週或每月 指定 **要保留的快照集數目**。  

    查看有關磁片區允許的快照集數目上限的 [Azure NetApp Files 資源限制](azure-netapp-files-resource-limits.md) 。 

    下列範例顯示每小時的快照集原則設定。 

    ![每小時快照集原則](../media/azure-netapp-files/snapshot-policy-hourly.png)

    下列範例顯示每日快照集原則設定。

    ![每日快照集原則](../media/azure-netapp-files/snapshot-policy-daily.png)

    下列範例會顯示每週的快照集原則設定。

    ![每週快照集原則](../media/azure-netapp-files/snapshot-policy-weekly.png)

    下列範例顯示每月快照集原則設定。

    ![每月快照集原則](../media/azure-netapp-files/snapshot-policy-monthly.png) 

4.  按一下 **[儲存]** 。  

如果您需要建立其他快照集原則，請重複步驟3。
您建立的原則會出現在 [快照集原則] 頁面中。

如果您想要讓磁片區使用快照集原則，您必須 [將原則套用至磁片](azure-netapp-files-manage-snapshots.md#apply-a-snapshot-policy-to-a-volume)區。 

### <a name="apply-a-snapshot-policy-to-a-volume"></a>將快照集原則套用至磁片區

如果您想要讓磁片區使用您所建立的快照集原則，您必須將原則套用至磁片區。 

1.  移至 [ **磁片** 區] 頁面，以滑鼠右鍵按一下您想要套用快照集原則的磁片區，然後選取 [ **編輯**]。

    ![磁片區右鍵功能表](../media/azure-netapp-files/volume-right-cick-menu.png) 

2.  在 [編輯] 視窗的 [ **快照集原則**] 底下，選取要用於磁片區的原則。  按一下 **[確定]** 以套用原則。  

    ![快照集原則編輯](../media/azure-netapp-files/snapshot-policy-edit.png) 

### <a name="modify-a-snapshot-policy"></a>修改快照集原則 

您可以修改現有的快照集原則，以變更原則狀態、快照頻率 (每小時、每天、每週或每月) ，或要保留的快照集數目。  
 
1.  從 NetApp 帳戶視圖中，按一下 [ **快照集原則**]。

2.  在您要修改的快照集原則上按一下滑鼠右鍵，然後選取 [ **編輯**]。

    ![快照集原則按右鍵功能表](../media/azure-netapp-files/snapshot-policy-right-click-menu.png) 

3.  在出現的 [快照集原則] 視窗中進行變更，然後按一下 [ **儲存**]。 

### <a name="delete-a-snapshot-policy"></a>刪除快照集原則 

您可以刪除不再需要保留的快照集原則。   

1.  從 NetApp 帳戶視圖中，按一下 [ **快照集原則**]。

2.  在您要修改的快照集原則上按一下滑鼠右鍵，然後選取 [ **刪除**]。

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

4. 按一下 [ **審核 + 建立**]。  按一下 [建立]。   
    新的磁片區會使用快照集所使用的相同通訊協定。   
    快照集還原到的新磁碟區會出現在 [磁碟區] 刀鋒視窗中。

## <a name="restore-a-file-from-a-snapshot-using-a-client"></a>使用用戶端從快照集還原檔案

如果您不想要將 [整個快照集還原到磁片](#restore-a-snapshot-to-a-new-volume)區，您可以選擇使用已掛接磁片區的用戶端，從快照集還原檔案。  

掛接的磁片區包含 NFS 用戶端中名為 (的快照集目錄  `.snapshot` ，) 或可供 `~snapshot` 用戶端存取的 SMB 用戶端) 中的 (。 快照集目錄包含對應到磁片區快照集的子目錄。 每個子目錄都包含快照集的檔案。 如果您不小心刪除或覆寫檔案，您可以將檔案從快照子目錄複寫到讀寫目錄，將檔案還原至父讀寫目錄。 

如果您在建立磁片區時選取 [隱藏快照路徑] 核取方塊，則會隱藏快照集目錄。 您可以藉由選取磁片區來查看磁片區的隱藏快照路徑狀態。 您可以按一下磁片區頁面上的 [ **編輯** ]，以編輯 [隱藏快照路徑] 選項。  

![編輯磁片區快照集選項](../media/azure-netapp-files/volume-edit-snapshot-options.png) 

### <a name="restore-a-file-by-using-a-linux-nfs-client"></a>使用 Linux NFS 用戶端來還原檔 

1. 使用 `ls` Linux 命令可列出您想要從目錄還原的檔案 `.snapshot` 。 

    例如：

    `$ ls my.txt`   
    `ls: my.txt: No such file or directory`   

    `$ ls .snapshot`   
    `daily.2020-05-14_0013/              hourly.2020-05-15_1106/`   
    `daily.2020-05-15_0012/              hourly.2020-05-15_1206/`   
    `hourly.2020-05-15_1006/             hourly.2020-05-15_1306/`   

    `$ ls .snapshot/hourly.2020-05-15_1306/my.txt`   
    `my.txt`

2. 使用命令將檔案 `cp` 複製到上層目錄。  

    例如： 

    `$ cp .snapshot/hourly.2020-05-15_1306/my.txt .`   

    `$ ls my.txt`   
    `my.txt`   

### <a name="restore-a-file-by-using-a-windows-client"></a>使用 Windows 用戶端來還原檔 

1. 如果 `~snapshot` 已隱藏磁片區的目錄，請在上層目錄中顯示要顯示的 [隱藏專案](https://support.microsoft.com/help/4028316/windows-view-hidden-files-and-folders-in-windows-10) `~snapshot` 。

    ![顯示隱藏的項目](../media/azure-netapp-files/snapshot-show-hidden.png) 

2. 流覽至內的子目錄， `~snapshot` 以尋找您想要還原的檔案。  以滑鼠右鍵按一下該檔案。 選取 [複製]****。  

    ![複製要還原的檔案](../media/azure-netapp-files/snapshot-copy-file-restore.png) 

3. 返回父目錄。 在上層目錄中按一下滑鼠右鍵，然後選取 `Paste` 將檔案貼到目錄中。

    ![貼上要還原的檔案](../media/azure-netapp-files/snapshot-paste-file-restore.png) 

4. 您也可以在上層目錄上按一下滑鼠右鍵， **選取 [** 內容]，按一下 [ **舊版** ] 索引標籤以查看快照集清單，然後選取 [ **還原** ] 來還原檔案。  

    ![先前的屬性版本](../media/azure-netapp-files/snapshot-properties-previous-version.png) 

## <a name="next-steps"></a>後續步驟

* [針對快照集原則進行疑難排解](troubleshoot-snapshot-policies.md)
* [Azure NetApp Files 的資源限制](azure-netapp-files-resource-limits.md)
* [Azure NetApp Files 快照101影片](https://www.youtube.com/watch?v=uxbTXhtXCkw&feature=youtu.be)
