---
title: Azure Stack Edge Pro GPU 共用管理 |Microsoft Docs
description: 說明如何使用 Azure 入口網站來管理 Azure Stack Edge Pro GPU 上的共用。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 01/04/2021
ms.author: alkohli
ms.openlocfilehash: b1ff10290e0059295f14b55e90ed05e9690c9f5c
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2021
ms.locfileid: "97896599"
---
# <a name="use-azure-portal-to-manage-shares-on-your-azure-stack-edge-pro"></a>使用 Azure 入口網站管理 Azure Stack Edge Pro 上的共用

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

本文說明如何管理 Azure Stack Edge Pro 上的共用。 您可以透過 Azure 入口網站或透過本機 web UI 來管理 Azure Stack Edge Pro。 使用 Azure 入口網站來新增、刪除、重新整理共用，或針對與共用相關聯的儲存體帳戶同步儲存體金鑰。 本文適用于 Azure Stack Edge Pro GPU、Azure Stack Edge Pro R 和 Azure Stack Edge 迷你 R 裝置。

## <a name="about-shares"></a>關於共用

若要將資料傳輸至 Azure，您必須在 Azure Stack Edge Pro 上建立共用。 您在 Azure Stack Edge Pro 裝置上新增的共用可以是本機共用，也可以是將資料推送至雲端的共用。

 - **本機共用**：當您想要在本機裝置上處理資料時，請使用這些共用。
 - **共用**：當您希望裝置資料自動推送到雲端中的儲存體帳戶時，請使用這些共用。 所有雲端 **功能，例如** 重新整理和 **同步儲存體金鑰** 都適用于共用。


## <a name="add-a-share"></a>新增共用

請在 Azure 入口網站中執行下列步驟來建立共用。

1. 在 Azure 入口網站中，移至您的 Azure Stack Edge 資源，然後移至 **雲端儲存體閘道 > 共用**。 選取命令列上的 [+ 新增共用]。

    ![選取 [新增共用]](media/azure-stack-edge-j-series-manage-shares/add-share-1.png)

2. 在 [新增共用] 中，指定共用設定。 為共用提供唯一的名稱。
    
    共用名稱只能包含數字、小寫字母和連字號。 共用名稱的長度必須介於 3 到 63 個字元之間，且開頭為字母或數字。 每個連字號前後都必須緊接非連字號的字元。

3. 選取共用的 [類型]  。 類型可以是 **SMB** 或 **NFS**，並以 SMB 為預設值。 SMB 是 Windows 用戶端的標準，NFS 則用於 Linux 用戶端。 視您選擇 SMB 或 NFS 共用而定，所顯示的選項會有些許不同。

4. 提供共用所在的 [儲存體帳戶]。 如果容器已不存在，則會使用共用名稱在儲存體帳戶中建立容器。 如果容器已存在，則會使用現有的容器。

5. 從下拉式清單中，選擇區塊 blob、分頁 blob 或檔案中的 **儲存體服務** 。 所選擇的服務類型取決於您想要資料以哪一種格式存在 Azure 中。 例如，在此例中，我們想要資料以區塊 blob 的形式存在於 Azure 中，因此我們選取 [ **區塊 Blob**]。 如果選擇 [ **分頁 Blob**]，您必須確定資料的大小為512個位元組。 針對一律為512位元組對齊的 Vhd 或 VHDX 使用 **分頁 blob** 。

6. 此步驟取決於您要建立 SMB 還是 NFS 共用。
    - **如果建立 SMB 共用** - 在 [完整權限本機使用者] 欄位中，從 [新建] 或 [使用現有的] 中擇一。 如果建立新的本機使用者，請提供 [使用者名稱]、[密碼]，然後確認密碼。 這會將使用權限指派給本機使用者。 當您在此指派權限之後，就可以使用 [檔案總管] 來修改這些權限。

        ![新增 SMB 共用](media/azure-stack-edge-j-series-manage-shares/add-smb-share.png)

        如果您針對此共用資料勾選 [僅允許讀取作業]，則可指定唯讀使用者。
    - **如果建立 NFS 共用** - 您需要 **針對允許存取共用的用戶端提供其 IP 位址**。

        ![新增 NFS 共用](media/azure-stack-edge-j-series-manage-shares/add-nfs-share.png)

7. 若要從 Edge 計算模組輕鬆存取共用，請使用本機掛接點。 選取 [搭配 Edge 計算來使用共用]，以在建立共用之後自動掛接共用。 若選取此選項，Edge 模組也可以搭配本機掛接點來使用計算。

8. 按一下 [ **建立** ] 以建立共用。 正在建立共用時會通知您。 使用指定的設定建立共用之後， **共用** 分頁會更新以反映新的共用。

## <a name="add-a-local-share"></a>新增本機共用

1. 在 Azure 入口網站中，移至您的 Azure Stack Edge 資源，然後移至 **雲端儲存體閘道 > 共用**。 選取命令列上的 [+ 新增共用]。

    ![選取 [新增共用 2]](media/azure-stack-edge-j-series-manage-shares/add-local-share-1.png)

2. 在 [新增共用] 中，指定共用設定。 為共用提供唯一的名稱。
    
    共用名稱只能包含數位、小寫字母和大寫字母，以及連字號。 共用名稱的長度必須介於 3 到 63 個字元之間，且開頭為字母或數字。 每個連字號前後都必須緊接非連字號的字元。

3. 選取共用的 [類型]  。 類型可以是 **SMB** 或 **NFS**，並以 SMB 為預設值。 SMB 是 Windows 用戶端的標準，NFS 則用於 Linux 用戶端。 視您選擇 SMB 或 NFS 共用而定，所顯示的選項會有些許不同。

   > [!IMPORTANT]
   > 如果您將 Azure 儲存體帳戶與 Azure Stack Edge Pro 或資料箱閘道裝置搭配使用，請確定您所使用的 Azure 儲存體帳戶並未設定了不變性原則。 如需詳細資訊，請參閱[設定和管理 Blob 儲存體的不變性原則](../storage/blobs/storage-blob-immutability-policies-manage.md)。

4. 若要從 Edge 計算模組輕鬆存取共用，請使用本機掛接點。 選取 [使用 Edge 計算的共用]，如此 Edge 模組可使用本機掛接點的計算。

5. 選取 [設定為 Edge 本機共用]。 本機共用中的資料將會以本機方式維持在裝置上。 您可以以本機方式處理此資料。

6. 在 [完整權限本機使用者] 欄位中，從 [新建] 或 [使用現有的] 中擇一。

7. 選取 [建立]  。 

    ![建立本機共用](media/azure-stack-edge-j-series-manage-shares/add-local-share-2.png)

    共用正在建立時，您會看見通知。 使用指定的設定建立共用之後， **共用** 分頁會更新以反映新的共用。

    ![檢視更新共用刀鋒視窗](media/azure-stack-edge-j-series-manage-shares/add-local-share-3.png)
    
    選取共用以檢視此共用的 Edge 計算模組的本機掛接點。

    ![檢視本機共用詳細資料](media/azure-stack-edge-j-series-manage-shares/add-local-share-4.png)

## <a name="mount-a-share"></a>掛接共用

如果您在 Azure Stack Edge Pro 裝置上設定計算之前已建立共用，您將需要掛接共用。 請採取下列步驟來掛接共用。


1. 在 Azure 入口網站中，移至您的 Azure Stack Edge 資源，然後移至 **雲端儲存體閘道 > 共用**。 從共用清單中，選取您想要掛接的共用。 [用於計算] 資料行會將所選共用的狀態顯示為 [已停用]。

    ![選取共用](media/azure-stack-edge-j-series-manage-shares/mount-share-1.png)

2. 選取 [掛接]。

    ![選取掛接](media/azure-stack-edge-j-series-manage-shares/mount-share-2.png)

3. 系統提示您確認時，請選取 [是]。 這會掛接共用。

    ![確認掛接](media/azure-stack-edge-j-series-manage-shares/mount-share-3.png)

4. 掛接共用之後，請移至共用清單。 您會看到 [用於計算] 資料行顯示共用的狀態為 [已啟用]。

    ![已掛接共用](media/azure-stack-edge-j-series-manage-shares/mount-share-4.png)

5. 再次選取共用以檢視此共用的本機掛接點。 Edge 計算模組會對此共用使用這個本機掛接點。

    ![共用的本機掛接點](media/azure-stack-edge-j-series-manage-shares/mount-share-5.png)

## <a name="unmount-a-share"></a>取消掛接共用

請在 Azure 入口網站中執行下列步驟以取消掛接共用。

1. 在 Azure 入口網站中，移至您的 Azure Stack Edge 資源，然後移至 **雲端儲存體閘道 > 共用**。 從共用清單中，選取您想要取消掛接的共用。 請確定沒有任何模組使用您取消掛接的共用。 如果有模組使用該共用，則您會看到對應模組發生問題。

    ![選取共用2](media/azure-stack-edge-j-series-manage-shares/unmount-share-1.png)

2.  選取 [ **卸載**]。

    ![選取取消掛接](media/azure-stack-edge-j-series-manage-shares/unmount-share-2.png)

3. 系統提示您確認時，請選取 [是]。 這會取消掛接共用。

    ![確認取消掛接](media/azure-stack-edge-j-series-manage-shares/unmount-share-3.png)

4. 取消掛接共用之後，請移至共用清單。 您會看到 [用於計算] 資料行顯示共用的狀態為 [已停用]。

    ![已取消掛接共用](media/azure-stack-edge-j-series-manage-shares/unmount-share-4.png)

## <a name="delete-a-share"></a>刪除共用

在 Azure 入口網站中執行下列步驟，以刪除共用。

1. 從共用清單中，選取並按一下您想要刪除的共用。

    ![選取共用3](media/azure-stack-edge-j-series-manage-shares/delete-share-1.png)

2. 按一下 **[刪除]** 。

    ![按一下刪除](media/azure-stack-edge-j-series-manage-shares/delete-share-2.png)

3. 當提示確認時，請按一下 [是]。

    ![Confirm delete](media/azure-stack-edge-j-series-manage-shares/delete-share-3.png)

共用清單會更新以反映刪除。

## <a name="refresh-shares"></a>重新整理共用

重新整理功能可讓您重新整理共用的內容。 重新整理共用時，系統會起始搜尋以尋找所有 Azure 物件，包括自從上次重新整理後新增至雲端的 Blob 和檔案。 然後，會下載這些額外檔案以重新整理裝置上的共用內容。

> [!IMPORTANT]
> - 您無法重新整理本機共用。
> - 權限和存取控制清單 (ACL) 不會保留在重新整理作業中。 

在 Azure 入口網站中執行下列步驟，以重新整理共用。

1.  在 Azure 入口網站中，移至 [共用]。 選取並按一下您想要重新整理的共用。

    ![選取共用4](media/azure-stack-edge-j-series-manage-shares/refresh-share-1.png)

2.  按一下 [重新整理]。 

    ![按一下 [重新整理]](media/azure-stack-edge-j-series-manage-shares/refresh-share-2.png)
 
3.  當提示確認時，請按一下 [是]。 系統會開始一項作業，以重新整理內部部署共用的內容。

    ![確認重新整理](media/azure-stack-edge-j-series-manage-shares/refresh-share-3.png)
 
4.   進行重新整理時，操作功能表中的重新整理選項會呈現灰色。 按一下作業通知來檢視重新整理作業狀態。

5.   重新整理的時間取決於 Azure 容器中的檔案數目，以及裝置上的檔案。 成功完成重新整理之後，共用時間戳記就會更新。 即使重新整理有部分失敗，此作業都會被視為成功，而且時間戳記會更新。 重新整理錯誤記錄檔也會一併更新。

![已更新的時間戳記](media/azure-stack-edge-j-series-manage-shares/refresh-share-4.png)
 
如有錯誤，則會引發警示。 警示會詳述原因和修正問題的建議。 警示也會連結到具有完整失敗摘要的檔案，包括無法更新或刪除的檔案。

## <a name="sync-pinned-files"></a>同步釘選的檔案 

若要自動同步處理已釘選的檔案，請在 Azure 入口網站中執行下列步驟：
 
1. 選取現有的 Azure 儲存體帳戶。 

2. 移至 [ **容器** ]，然後選取 [ **+ 容器** ] 以建立容器。 將此容器命名為 *>newcontainer*。 將 [ **公用存取層級** ] 設定為 [容器]。

    ![自動同步處理已釘選的檔案1](media/azure-stack-edge-j-series-manage-shares/image-1.png)

3. 選取容器名稱，並設定下列中繼資料：  

    - Name = "釘選" 
    - 值 = "True" 

    ![自動同步處理已釘選的檔案2](media/azure-stack-edge-j-series-manage-shares/image-2.png)
 
4. 在您的裝置上建立新的共用。 選擇 [現有的容器] 選項，將其對應至已釘選的容器。 將共用標示為唯讀。 建立新的使用者，並指定此共用的使用者名稱和對應的密碼。  

    ![自動同步處理已釘選的檔案3](media/azure-stack-edge-j-series-manage-shares/image-3.png)
 
5. 在 Azure 入口網站中，流覽至您所建立的容器。 將您要釘選的檔案上傳至 >newcontainer，而該檔案的中繼資料已設定為釘選。 

6. 選取裝置 Azure 入口網站中的 [重新整理 **資料** ]，以下載該特定 Azure 儲存體容器的釘選原則。  

    ![自動同步處理已釘選的檔案4](media/azure-stack-edge-j-series-manage-shares/image-4.png)
 
7. 存取在裝置上建立的新共用。 已上傳至儲存體帳戶的檔案現在已下載至本機共用。 

    當裝置中斷連線或重新連線時，它會觸發重新整理。 重新整理只會顯示已變更的檔案。 


## <a name="sync-storage-keys"></a>同步儲存體金鑰

如果已輪替您的儲存體帳戶金鑰，您就必須同步儲存體存取金鑰。 同步處理可協助裝置取得您儲存體帳戶的最新金鑰。

在 Azure 入口網站中執行下列步驟，以同步儲存體存取金鑰。

1. 移至您資源的 [概觀]。 從共用清單中，選擇並按一下與您需要同步的儲存體帳戶相關聯的共用。

    ![選取相關儲存體帳戶的共用](media/azure-stack-edge-j-series-manage-shares/sync-storage-key-1.png)

2. 按一下 [同步儲存體金鑰]。 在出現確認提示時，按一下 [是]。

     ![選取同步儲存體金鑰](media/azure-stack-edge-j-series-manage-shares/sync-storage-key-2.png)

3. 在同步完成後結束對話方塊。

>[!NOTE]
> 您只需要針對指定的儲存體帳戶執行此作業一次。 不需要針對與相同儲存體帳戶相關聯的所有共用，重複此動作。


## <a name="next-steps"></a>後續步驟

- 了解如何[透過 Azure 入口網站管理使用者](azure-stack-edge-j-series-manage-users.md)。