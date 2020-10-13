---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 09/30/2019
ms.author: alkohli
ms.openlocfilehash: ca7b83d24f2416b224963559361faf5a7775cd0d
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/01/2020
ms.locfileid: "91631526"
---
當 Microsoft 收到並掃描裝置之後，訂單狀態會更新為 [已收到]  。 然後，裝置會進行實體驗證，以檢查是否有損毀或遭到竄改的跡象。

完成驗證之後，資料箱會連線到 Azure 資料中心的網路。 資料複製會自動啟動。 視資料大小而定，複製作業可能需要數小時到數天才能完成。 您可以在入口網站中監視複製作業進度。

一旦複製完成，訂單狀態會更新為 [已完成]  。

請先確認您的資料已上傳至 Azure，然後再從來源予以刪除。 您的資料可以位於：

- 您的 Azure 儲存體帳戶。 當您將資料複製到資料箱時，資料會上傳到 Azure 儲存體帳戶中的下列其中一個路徑：

  - 區塊 Blob 與分頁 Blob：`https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - Azure 檔案：`https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    或者，您可以在 Azure 入口網站中移至您 Azure 儲存體帳戶，並從該處瀏覽。

- 您的受控磁碟資源群組。 建立受控磁碟時，會上傳 VHD 以作為分頁 Blob，接著轉換為受控磁碟。 受控磁碟會連結至訂單建立時所指定的資源群組。 

    - 如果您已成功複製到 Azure 中的受控磁碟，則可前往 Azure 入口網站中的 [訂單詳細資料]  ，並記下針對受控磁碟所指定的資源群組。

        ![識別受控磁碟資源群組](media/data-box-verify-upload-return/order-details-managed-disk-resource-groups.png)

        前往所記下的資源群組，並找出您的受控磁碟。

        ![連結至資源群組的受控磁碟](media/data-box-verify-upload-return/managed-disks-resource-group.png)

    - 如果您複製了 VHDX 或動態或差異 VHD，則會將 VHDX 或 VHD 上傳至暫存的儲存體帳戶以作為分頁 Blob，但是將 VHD 轉換為受控磁碟會失敗。 移至您暫存的 [儲存體帳戶] > [Blob]，然後選取適當的容器：標準 SSD、標準 HDD 或進階 SSD。 VHD 會以分頁 Blob 形式上傳到暫存的儲存體帳戶，並且產生費用。


## <a name="erasure-of-data-from-data-box"></a>清除資料箱的資料
 
完成上傳到 Azure 之後，資料箱會根據 [NIST SP 800-88 修訂 1 指導方針](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi)清除磁碟中的資料。


## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解 Azure 資料箱的相關主題，像是：

> [!div class="checklist"]
> * Prerequisites
> * 準備寄送
> * 將資料箱寄送給 Microsoft
> * 確認資料上傳至 Azure
> * 清除資料箱的資料

請前往下列文章，了解如何透過本機 Web UI 管理資料箱。

> [!div class="nextstepaction"]
> [使用本機 Web UI 來管理 Azure 資料箱](../articles/databox/data-box-local-web-ui-admin.md)