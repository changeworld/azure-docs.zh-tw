---
title: 確認資料已從 Azure 資料箱磁碟上傳至儲存體帳戶的教學課程 | Microsoft Docs
description: 使用本教學課程，了解如何確認資料已從 Azure 資料箱磁碟上傳至 Azure 儲存體帳戶。
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.localizationpriority: high
ms.date: 09/17/2019
ms.author: alkohli
ms.openlocfilehash: abe09cf10e241ec95ceed767e7038cde07667fc9
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91322728"
---
::: zone target="docs"

# <a name="tutorial-verify-data-upload-from-azure-data-box-disk"></a>教學課程：確認資料已從 Azure 資料箱磁碟上傳

這是一系列教學課程的最後一個：部署 Azure 資料箱磁碟。 在本教學課程中，您將了解：

> [!div class="checklist"]
> * 確認資料上傳至 Azure
> * 清除資料箱磁碟的資料

## <a name="prerequisites"></a>Prerequisites

開始之前，請確定您已完成[教學課程：送回 Azure 資料箱磁碟](data-box-disk-deploy-picked-up.md)。


## <a name="verify-data-upload-to-azure"></a>確認資料上傳至 Azure

一旦承運業者收取磁碟，入口網站的訂單狀態會更新為 [已取貨]  。 此外，也會顯示追蹤識別碼。

![磁碟已取貨](media/data-box-disk-deploy-picked-up/data-box-portal-pickedup.png)

當 Microsoft 收到並掃描磁碟之後，作業狀態會更新為 [已收到]  。 

![磁碟已收到](media/data-box-disk-deploy-picked-up/data-box-portal-received.png)

一旦磁碟連線到 Azure 資料中心的伺服器，系統會自動複製資料。 視資料大小而定，複製作業可能需要數小時到數天才能完成。 您可以在入口網站中監視複製作業進度。

一旦複製完成，訂單狀態會更新為 [已完成]  。

![資料複製已完成](media/data-box-disk-deploy-picked-up/data-box-portal-completed.png)

如果複製完成但發生錯誤，請參閱[對上傳錯誤進行疑難排解](data-box-disk-troubleshoot-upload.md)。

請先確認您的資料位於儲存體帳戶中，再從來源予以刪除。 您的資料可以位於：

- 您的 Azure 儲存體帳戶。 當您將資料複製到資料箱時，資料會上傳到 Azure 儲存體帳戶中的下列其中一個路徑，視類型而定。

  - 區塊 Blob 與分頁 Blob：`https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - Azure 檔案：`https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    或者，您可以在 Azure 入口網站中移至您 Azure 儲存體帳戶，並從該處瀏覽。

- 您的受控磁碟資源群組。 建立受控磁碟時，會上傳 VHD 以作為分頁 Blob，接著轉換為受控磁碟。 受控磁碟會連結至訂單建立時所指定的資源群組。

  - 如果您已成功複製到 Azure 中的受控磁碟，則可前往 Azure 入口網站中的 [訂單詳細資料]  ，並記下針對受控磁碟所指定的資源群組。

      ![檢閱訂單詳細資料](media/data-box-disk-deploy-picked-up/order-details-resource-group.png)

    前往所記下的資源群組，並找出您的受控磁碟。

      ![受控磁碟的資源群組](media/data-box-disk-deploy-picked-up/resource-group-attached-managed-disk.png)

    > [!NOTE]
    > 如果分頁 Blob 未在資料複製期間成功轉換為受控磁碟，其會保留在儲存體帳戶中，您需要支付儲存體費用。

  -  如果您複製了 VHDX 或動態/差異 VHD，則會將 VHDX/VHD 上傳至暫存的儲存體帳戶以作為區塊 Blob。 移至您暫存的 [儲存體帳戶] > [Blob]****，然後選取適當的容器：標準 SSD、標準 HDD 或進階 SSD。 VHDX/VHD 應該會在您暫存的儲存體帳戶中顯示為區塊 Blob。
  

  
::: zone-end

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>確認資料上傳至 Azure

資料上傳至 Azure 後，請先確認您的資料位於儲存體帳戶中，再從來源予以刪除。 您的資料可以位於：

- 您的 Azure 儲存體帳戶。 當您將資料複製到資料箱時，資料會上傳到 Azure 儲存體帳戶中的下列其中一個路徑，視類型而定。

    - **區塊 Blob 和分頁 Blob**：https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt

    - **Azure 檔案儲存體**：https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt

- 您的受控磁碟資源群組。 建立受控磁碟時，會上傳 VHD 以作為分頁 Blob，接著轉換為受控磁碟。 受控磁碟會連結至訂單建立時所指定的資源群組。

::: zone-end

若要確認資料已上傳至 Azure，請採取下列步驟：

1. 移至與磁碟訂單相關聯的儲存體帳戶。
2. 移至 [Blob 服務 > 瀏覽 Blob]****。 容器清單隨即顯示。 對應至您在 BlockBlob** 和 PageBlob** 資料夾底下建立的子資料夾，系統會在儲存體帳戶中建立具有相同名稱的容器。
    如果資料夾名稱不符合 Azure 命名慣例，則資料上傳至 Azure 會失敗。

3. 若要確認已載入整個資料集，請使用 Microsoft Azure 儲存體總管。 連接對應到資料箱磁碟訂單的儲存體帳戶，然後查看 Blob 容器的清單。 選取容器，按一下 [...更多]****，然後按一下 [資料夾統計資料]****。 在 [活動]**** 窗格中，會顯示該資料夾的統計資料，包括 Blob 數目和 Blob 大小總計。 Blob 大小總計 (以位元組為單位) 應該符合資料集的大小。

    ![儲存體總管中的資料夾統計資料](media/data-box-disk-deploy-picked-up/folder-statistics-storage-explorer.png)

## <a name="erasure-of-data-from-data-box-disk"></a>清除資料箱磁碟的資料

上傳到 Azure 之後，資料箱磁碟會根據 [NIST SP 800-88](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi) 標準清除磁碟中的資料。

::: zone target="docs"

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解 Azure 資料箱磁碟的相關主題，像是：

> [!div class="checklist"]
> * 確認資料上傳至 Azure
> * 清除資料箱磁碟的資料


請前進到下一個操作說明，以了解如何透過 Azure 入口網站管理資料箱磁碟。

> [!div class="nextstepaction"]
> [使用 Azure 入口網站來管理 Azure 資料箱磁碟](./data-box-portal-ui-admin.md)

::: zone-end




