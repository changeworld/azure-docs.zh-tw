---
title: 使用 Azure 資料箱閘道傳輸資料 | Microsoft Docs
description: 了解如何新增及連線到資料箱閘道裝置上的共用。
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 03/08/2019
ms.author: alkohli
ms.openlocfilehash: 4817db0ce9723f46ceac4f4720915a9bfddcf915
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "82561746"
---
# <a name="tutorial-transfer-data-with-azure-data-box-gateway"></a>教學課程：使用 Azure Data Box Gateway 轉送資料


## <a name="introduction"></a>簡介

本文說明如何新增及連線到 Data Box Gateway 上的共用。 新增共用之後，Data Box Gateway 裝置即可將資料轉送至 Azure。

此程序可能需要大約 10 分鐘才能完成。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
>
> * 新增共用
> * 連線到共用

## <a name="prerequisites"></a>Prerequisites

您將共用新增至資料箱閘道之前，請確定：

- 您已根據[在 Hyper-V 中佈建 Data Box Gateway](data-box-gateway-deploy-provision-hyperv.md) 或[在 VMware 中佈建 Data Box Gateway](data-box-gateway-deploy-provision-vmware.md) 中的詳細說明，佈建虛擬裝置並加以連線。

- 您已依照[連線並啟動 Azure Data Box Gateway](data-box-gateway-deploy-connect-setup-activate.md) 中的說明啟動虛擬裝置。

- 裝置已可供您建立共用及資料傳輸。

## <a name="add-a-share"></a>新增共用

若要建立共用，請執行下列程序：

1. 在 [Azure 入口網站](https://portal.azure.com/)中，選取您的 Data Box Gateway 資源，然後移至 [概觀]  。 您的裝置應在線上。 選取裝置命令列上的 [+ 新增共用]  。
   
   ![新增共用](./media/data-box-gateway-deploy-add-shares/click-add-share.png)

4. 在 [新增共用]  中，執行下列程序：

    1. 為共用提供唯一的名稱。 共用名稱只能包含小寫字母、數字和連字號。 共用名稱的長度必須介於 3 到 63 個字元之間，且開頭為字母或數字。 每個連字號前後都必須緊接非連字號的字元。
    
    2. 選取共用的 [類型]  。 類型可以是 SMB 或 NFS，並以 SMB 為預設值。 SMB 是 Windows 用戶端的標準，NFS 則用於 Linux 用戶端。 視您選擇 SMB 或 NFS 共用而定，所顯示的選項會有些許不同。

    3. 提供共用所在的儲存體帳戶。 如果容器尚不存在，則會使用新建共用名稱在儲存體帳戶中建立容器。 如果容器已存在，則會使用該容器。
       > [!IMPORTANT]
       > 如果您將 Azure 儲存體帳戶與 Azure Stack Edge 或資料箱閘道裝置搭配使用，請確定您所使用的 Azure 儲存體帳戶並未設定了不變性原則。 如需詳細資訊，請參閱[設定和管理 Blob 儲存體的不變性原則](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutability-policies-manage)。
    
    4. 從區塊 Blob、分頁 Blob 或檔案中選擇 [儲存體服務]  。 所選擇的服務類型取決於您想要資料以哪一種格式存在 Azure 中。 例如，在本例中，我們想要資料以區塊 Blob 存在 Azure 中，因此，我們選取 [區塊 Blob]。 如果選擇 [分頁 Blob]，您必須確定資料對齊 512 個位元組。 例如，VHDX 一律是 512 位元組規格。
   
    5. 此步驟取決於您要建立 SMB 還是 NFS 共用。
     
    - **SMB 共用** - 在 [完整權限本機使用者]  下方，選取 [新建]  或 [使用現有的]  。 如果您建立新的本機使用者，請輸入**使用者名稱**和**密碼**，然後**確認密碼**。 此動作會將權限指派給本機使用者。 目前不支援修改共用層級權限。
    
        ![新增 SMB 共用](./media/data-box-gateway-deploy-add-shares/add-share-smb-1.png)
        
        如果您針對此共用資料選取 [僅允許讀取作業]  核取方塊，則可以指定唯讀使用者。
        
    - **NFS 共用** - 針對允許存取共用的用戶端輸入其 IP 位址。

        ![新增 NFS 共用](./media/data-box-gateway-deploy-add-shares/add-share-nfs-1.png)
   
9. 選取 [建立]  以建立共用。
    
    共用正在建立時，您會收到通知。 使用指定的設定來建立共用之後，[共用]  圖格將會更新，以反映新的共用。
    
    ![已更新的共用圖格](./media/data-box-gateway-deploy-add-shares/updated-list-of-shares.png) 

## <a name="connect-to-the-share"></a>連線至共用

您現在可以連線至在上一個步驟中建立的一或多個共用。 根據您使用的是 SMB 還是 NFS 共用，步驟可能有所不同。

### <a name="connect-to-an-smb-share"></a>連線至 SMB 共用

在連線到 Data Box Gateway 的 Windows Server 用戶端上，輸入下列命令以連線到 SMB 共用：


1. 在命令視窗中，輸入：

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    出現提示時，請輸入共用的密碼。 此命令的輸出範例會出現在這裡。

    ```powershell
    Microsoft Windows [Version 18.8.16299.192) 
    (c) 2017 microsoft Corporation. All rights reserved . 
    
    C: \Users\GatewayUser>net use \\10.10.10.60\newtestuser /u:Tota11yNewUser 
    Enter the password for 'TotallyNewUser' to connect to '10.10.10.60'  
    The command completed successfully. 
    
    C: \Users\GatewayUser>
    ```   


2. 在鍵盤上選取 Windows + R。 
3. 在 [執行]  視窗中指定 `\\<device IP address>`，然後選取 [確定]  。 [檔案總管] 隨即開啟。 現在您應可以資料夾的形式檢視您所建立的共用。 在 [檔案總管] 中按兩下某個共用 (資料夾)，即可檢視其內容。
 
    ![連線至 SMB 共用](./media/data-box-gateway-deploy-add-shares/connect-to-share2.png)-->

    資料產生時會寫入這些共用，且裝置會將資料推送至雲端。

### <a name="connect-to-an-nfs-share"></a>連線到 NFS 共用

在連線到 Azure Stack Edge 裝置的 Linux 用戶端上，執行下列程序：

1. 確定用戶端已安裝 NFSv4 用戶端。 若要安裝 NFS 用戶端，請使用下列命令：

   `sudo apt-get install nfs-common`

    如需詳細資訊，請移至[安裝 NFSv4 用戶端](https://help.ubuntu.com/community/SettingUpNFSHowTo#NFSv4_client)。

2. 安裝 NFS 用戶端之後，使用下列命令來掛接您在資料箱閘道裝置上建立的 NFS 共用：

   `sudo mount -t nfs -o sec=sys,resvport <device IP>:/<NFS shares on device> /home/username/<Folder on local Linux computer>`

    設定掛接之前，請確定已建立本機電腦上要作為掛接點的目錄，且請勿包含任何檔案或子資料夾。

    下列範例示範如何透過 NFS 連線到閘道裝置上的共用。 虛擬裝置 IP 為 `10.10.10.60`，共用 `mylinuxshare2` 會掛接在 ubuntuVM 上，掛接點為 `/home/databoxubuntuhost/gateway`。

    `sudo mount -t nfs -o sec=sys,resvport 10.10.10.60:/mylinuxshare2 /home/databoxubuntuhost/gateway`

> [!NOTE] 
> 下列需要注意的事項適用於此版本︰
> - 在共用中建立檔案之後，不支援重新命名檔案。
> - 從共用中刪除檔案並不會刪除儲存體帳戶中的項目。
> - 如果使用 `rsync` 複製資料，則不支援 `rsync -a` 選項。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解資料箱閘道的相關主題，像是：

> [!div class="checklist"]
> * 新增共用
> * 連線到共用


請繼續進行下一個教學課程，以了解如何管理資料箱閘道。

> [!div class="nextstepaction"]
> [使用本機 Web UI 來管理資料箱閘道](https://aka.ms/dbg-docs)


