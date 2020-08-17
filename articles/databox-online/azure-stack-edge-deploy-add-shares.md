---
title: 使用 Azure Stack Edge 傳輸要共用之資料的教學課程 | Microsoft Docs
description: 在本教學課程中，了解如何新增和連線至 Azure Stack Edge 裝置上的共用，讓 Azure Stack Edge 可以將資料傳輸至 Azure。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to add and connect to shares on Azure Stack Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: fc47497eeceff20e8357195226c2d44cea64fa88
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2020
ms.locfileid: "87926105"
---
# <a name="tutorial-transfer-data-with-azure-stack-edge"></a>教學課程：使用 Azure Stack Edge 傳輸資料

本教學課程說明如何新增及連線到 Azure Stack Edge 裝置上的共用。 在新增共用之後，Azure Stack Edge 即可將資料傳輸至 Azure。

此程序可能需要大約 10 分鐘才能完成。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 新增共用
> * 連線至共用

 
## <a name="prerequisites"></a>Prerequisites

將共用新增至 Azure Stack Edge 之前，請確定：

- 您已依照[安裝 Azure Stack Edge](azure-stack-edge-deploy-install.md) 中的說明安裝實體裝置。

- 您已依照[連線、設定及啟用 Azure Stack Edge](azure-stack-edge-deploy-connect-setup-activate.md) 中的說明啟動實體裝置。


## <a name="add-a-share"></a>新增共用

若要建立共用，請執行下列程序：

1. 在 [Azure 入口網站](https://portal.azure.com/)中，選取您的 Azure Stack Edge 資源，然後移至 [概觀]  。 您的裝置應在線上。

   ![線上裝置](./media/azure-stack-edge-deploy-add-shares/device-online-1.png)

2. 選取裝置命令列上的 [+ 新增共用]  。

   ![新增共用](./media/azure-stack-edge-deploy-add-shares/select-add-share-1.png)

3. 在 [新增共用]  窗格中，執行下列程序：

    a. 在 [名稱]  方塊中，為共用提供唯一的名稱。  
    共用名稱只能包含小寫字母、數字和連字號。 此名稱必須介於 3 到 63 個字元，並且以字母或數字開頭。 連字號的前後必須緊鄰字母或數字。
    
    b. 選取共用的 [類型]  。  
    類型可以是 **SMB** 或 **NFS**，並以 SMB 為預設值。 SMB 是 Windows 用戶端的標準，NFS 則用於 Linux 用戶端。  
    視您選擇 SMB 或 NFS 共用而定，其餘選項可能會有些許不同。 

    c. 提供共用所在的儲存體帳戶。 

    > [!IMPORTANT]
    > 如果您將 Azure 儲存體帳戶與 Azure Stack Edge 或資料箱閘道裝置搭配使用，請確定您所使用的 Azure 儲存體帳戶並未設定了不變性原則。 如需詳細資訊，請參閱[設定和管理 Blob 儲存體的不變性原則](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutability-policies-manage)。
    
    d. 在 [儲存體服務]  下拉式清單中，選取 [區塊 Blob]  、[分頁 Blob]  或 [檔案]  。  
    您選取的服務類型，取決於您想要讓資料在 Azure 中使用的格式。 在此範例中，我們想要將資料以區塊 Blob 的形式儲存在 Azure 中，因此我們選取 [區塊 Blob]  。 如果您選取 [分頁 Blob]  ，請確定您的資料是 512 位元組規格。 例如，VHDX 一律是 512 位元組規格。

    e. 建立新的 Blob 容器，或使用從下拉式清單中的現有容器。 如果要建立 Blob 容器，請提供容器名稱。 如果容器尚不存在，則會使用新建共用名稱在儲存體帳戶中建立容器。
   
    f. 根據您已建立的是 SMB 共用還是 NFS 共用，執行下列其中一個步驟： 
     
    - **SMB 共用**：在 [完整權限本機使用者]  下方，選取 [新建]  或 [使用現有的]  。 如果您建立新的本機使用者，請輸入使用者名稱和密碼，然後確認密碼。 此動作會將權限指派給本機使用者。 在此指派權限之後，您可以使用 [檔案總管] 加以修改。

        如果您針對此共用資料選取 [僅允許讀取作業]  核取方塊，則可以指定唯讀使用者。

        ![新增 SMB 共用](./media/azure-stack-edge-deploy-add-shares/add-share-smb-1.png)
   
    - **NFS 共用**：針對經允許可存取共用的用戶端輸入其 IP 位址。

        ![新增 NFS 共用](./media/azure-stack-edge-deploy-add-shares/add-share-nfs-1.png)
   
4. 選取 [建立]  以建立共用。
    
    共用正在建立時，您會收到通知。 使用指定的設定來建立共用之後，[共用]  圖格將會更新，以反映新的共用。
    

## <a name="connect-to-the-share"></a>連線至共用

您現在可以連線至在上一個步驟中建立的一或多個共用。 根據您使用的是 SMB 還是 NFS 共用，步驟可能有所不同。

### <a name="connect-to-an-smb-share"></a>連線至 SMB 共用

在連線到 Azure Stack Edge 裝置的 Windows Server 用戶端上，輸入下列命令以連線到 SMB 共用：


1. 在命令視窗中，輸入：

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

2. 如果系統提示您輸入共用的密碼，請加以輸入。  
   此命令的輸出範例會出現在這裡。

    ```powershell
    Microsoft Windows [Version 10.0.16299.192)
    (c) 2017 Microsoft Corporation. All rights reserved.
    
    C: \Users\DataBoxEdgeUser>net use \\10.10.10.60\newtestuser /u:Tota11yNewUser
    Enter the password for 'TotallyNewUser' to connect to '10.10.10.60':
    The command completed successfully.
    
    C: \Users\DataBoxEdgeUser>
    ```   


3. 在鍵盤上選取 Windows + R。

4. 在 [執行]  視窗中指定 `\\<device IP address>`，然後選取 [確定]  。  
   [檔案總管] 隨即開啟。 現在您應可以資料夾的形式檢視您所建立的共用。 在 [檔案總管] 中按兩下某個共用 (資料夾)，即可檢視其內容。
 
    ![連線至 SMB 共用](./media/azure-stack-edge-deploy-add-shares/connect-to-share2.png)

    資料產生時會寫入這些共用，且裝置會將資料推送至雲端。

### <a name="connect-to-an-nfs-share"></a>連線到 NFS 共用

在連線到 Azure Stack Edge 裝置的 Linux 用戶端上，執行下列程序：

1. 確定用戶端已安裝 NFSv4 用戶端。 若要安裝 NFS 用戶端，請使用下列命令：

   `sudo apt-get install nfs-common`

    如需詳細資訊，請移至[安裝 NFSv4 用戶端](https://help.ubuntu.com/community/SettingUpNFSHowTo#NFSv4_client)。

2. 安裝 NFS 用戶端之後，請使用下列命令掛接您在 Azure Stack Edge 裝置上建立的 NFS 共用：

   `sudo mount -t nfs -o sec=sys,resvport <device IP>:/<NFS shares on device> /home/username/<Folder on local Linux computer>`

    > [!IMPORTANT]
    > 在裝載共用時使用 `sync` 選項，可以改善大型檔案的傳輸速率。
    > 掛接共用之前，請確定已建立要在本機電腦上作為掛接點的目錄。 這些目錄不應包含任何檔案或子資料夾。

    下列範例說明如何透過 NFS 連線到 Azure Stack Edge 裝置上的共用。 裝置 IP 為 `10.10.10.60`。 共用 `mylinuxshare2` 會掛接在 ubuntuVM 上。 共用掛接點為 `/home/databoxubuntuhost/edge`。

    `sudo mount -t nfs -o sec=sys,resvport 10.10.10.60:/mylinuxshare2 /home/databoxubuntuhost/Edge`

> [!NOTE] 
> 下列需要注意的事項適用於此版本︰
> - 在共用中建立檔案之後，將不支援檔案的重新命名。 
> - 從共用中刪除檔案，並不會刪除儲存體帳戶中的項目。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解下列 Azure Stack Edge 主題：

> [!div class="checklist"]
> * 新增共用
> * 連線到共用

若要了解如何使用 Azure Stack Edge 來傳輸資料，請繼續進行下一個教學課程：

> [!div class="nextstepaction"]
> [使用 Azure Stack Edge 轉換資料](./azure-stack-edge-deploy-configure-compute.md)


