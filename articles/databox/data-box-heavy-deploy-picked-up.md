---
title: 寄回 Azure Data Box Heavy 的教學課程 | Microsoft Docs
description: 了解如何將您的 Azure Data Box Heavy 寄送給 Microsoft
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: cfb9f54f5ba219a4db87144ab1e7ebff2b72b69e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "79214185"
---
::: zone target = "docs"

# <a name="tutorial-return-azure-data-box-heavy-and-verify-data-upload-to-azure"></a>教學課程：送回 Azure Data Box Heavy，並確認資料已上傳至 Azure

::: zone-end

::: zone target = "chromeless"

# <a name="return-azure-data-box-heavy-and-verify-data-upload-to-azure"></a>送回 Azure Data Box Heavy，並確認資料已上傳至 Azure

::: zone-end

::: zone target = "docs"

本教學課程說明如何送回 Azure Data Box Heavy，並確認資料已上傳至 Azure。

在本教學課程中，您將了解如下所列的主題：

> [!div class="checklist"]
> * Prerequisites
> * 準備寄送
> * 將 Data Box Heavy 寄送給 Microsoft
> * 確認資料上傳至 Azure
> * 清除 Data Box Heavy 中的資料

## <a name="prerequisites"></a>Prerequisites

在您開始前，請確定：

- 您已完成[教學課程：將資料複製到 Azure 資料箱並確認](data-box-heavy-deploy-copy-data.md)。
- 複製作業已完成。 如果複製作業正在進行，則無法執行寄送準備。


## <a name="prepare-to-ship"></a>準備寄送

[!INCLUDE [data-box-heavy-prepare-to-ship](../../includes/data-box-heavy-prepare-to-ship.md)]

::: zone-end

::: zone target = "chromeless"

## <a name="prepare-to-ship"></a>準備寄送

在您準備寄送之前，請確定複製作業已完成。

1. 前往本機 Web UI 中的 [準備寄送] 頁面，並開始準備寄送。
2. 從本機 Web UI 關閉裝置。 從裝置移除纜線。

您現在已準備好而可將裝置寄回。

::: zone-end

## <a name="ship-data-box-heavy-back"></a>寄回 Data Box Heavy

1. 確定裝置的電源已關閉，然後所有纜線皆已卸下。 纏繞 4 個電源線，並將其妥善置於可從裝置背面開關的匣盒中。
2. 在美國和歐盟，分別會透過 FedEx 和 DHL 以 LTL 貨運來運送裝置

    1. 連絡 [Data Box Operations](mailto:DataBoxOps@microsoft.com)，告知取貨事宜並取得寄返標籤。
    2. 撥打當地貨運商的電話號碼，並約定取貨時間。
    3. 確認貨物外部有醒目顯示的出貨標籤。
    4. 確實從裝置上移除前次出貨的舊出貨標籤。
3. 一旦貨運業者收取 Data Box Heavy 並進行掃描，入口網站的訂單狀態就會更新為 [已取貨]  。 此外，也會顯示追蹤識別碼。

::: zone target = "docs"

## <a name="verify-data-upload-to-azure"></a>確認資料上傳至 Azure

當 Microsoft 收到並掃描裝置之後，訂單狀態會更新為 [已收到]  。 然後，裝置會進行實體驗證，以檢查是否有損毀或遭到竄改的跡象。

完成驗證之後，Data Box Heavy 會連線到 Azure 資料中心的網路。 資料複製會自動啟動。 視資料大小而定，複製作業可能需要數小時到數天才能完成。 您可以在入口網站中監視複製作業進度。

一旦複製完成，訂單狀態會更新為 [已完成]  。

請先確認您的資料已上傳至 Azure，然後再從來源予以刪除。 您的資料可以位於：

- 您的 Azure 儲存體帳戶。 當您將資料複製到資料箱時，資料會上傳到 Azure 儲存體帳戶中的下列其中一個路徑，視類型而定。

  - 區塊 Blob 與分頁 Blob：`https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - Azure 檔案：`https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    或者，您可以在 Azure 入口網站中移至您 Azure 儲存體帳戶，並從該處瀏覽。

- 您的受控磁碟資源群組。 建立受控磁碟時，會上傳 VHD 以作為分頁 Blob，接著轉換為受控磁碟。 受控磁碟會連結至訂單建立時所指定的資源群組。 

    - 如果您已成功複製到 Azure 中的受控磁碟，則可前往 Azure 入口網站中的 [訂單詳細資料]  ，並記下針對受控磁碟所指定的資源群組。

        ![識別受控磁碟資源群組](media/data-box-deploy-copy-data-from-vhds/order-details-managed-disk-resource-groups.png)

        前往所記下的資源群組，並找出您的受控磁碟。

        ![連結至資源群組的受控磁碟](media/data-box-deploy-copy-data-from-vhds/managed-disks-resource-group.png)

    - 如果您複製了 VHDX 或動態/差異 VHD，則會將 VHDX/VHD 上傳至暫存的儲存體帳戶以作為分頁 Blob，但將 VHD 轉換為受控磁碟會失敗。 移至您暫存的 [儲存體帳戶] > [Blob]  ，然後選取適當的容器：標準 SSD、標準 HDD 或進階 SSD。 VHD 會以分頁 Blob 形式上傳到暫存的儲存體帳戶。
    
::: zone-end

::: zone target = "chromeless"

## <a name="verify-data-upload-to-azure"></a>確認資料上傳至 Azure

當 Data Box Heavy 裝置連線到 Azure 資料中心網路時，會自動開始將資料上傳到 Azure。 Data Box 服務會通知您，已透過 Azure 入口網站完成資料複製。

- 檢查所有失敗的錯誤記錄，並採取適當的動作。
- 請先確認您的資料位於儲存體帳戶中，再從來源予以刪除。

::: zone-end

## <a name="erasure-of-data-from-data-box-heavy"></a>清除 Data Box Heavy 中的資料
 
完成上傳到 Azure 之後，資料箱會根據 [NIST SP 800-88 修訂 1 指導方針](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi)清除磁碟中的資料。 清除完成後，您可以[下載訂單記錄](data-box-portal-admin.md#download-order-history)。

::: zone target = "docs"

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解 Azure 資料箱的相關主題，像是：

> [!div class="checklist"]
> * Prerequisites
> * 準備寄送
> * 將 Data Box Heavy 寄送給 Microsoft
> * 確認資料上傳至 Azure
> * 清除 Data Box Heavy 中的資料

繼續參閱下列文章，了解如何透過本機 Web UI 管理 Data Box Heavy。

> [!div class="nextstepaction"]
> [使用本機 Web UI 來管理 Azure 資料箱](./data-box-local-web-ui-admin.md)

::: zone-end


