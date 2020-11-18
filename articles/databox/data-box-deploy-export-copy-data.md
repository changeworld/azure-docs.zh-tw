---
title: 透過 SMB 從 Azure 資料箱複製資料的教學課程 | Microsoft Docs
description: 了解如何透過 SMB 將資料複製到您的 Azure 資料箱
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 07/10/2020
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: ecc6e1e1a543f3190e9f73512ca0b9ae45cc3fe9
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2020
ms.locfileid: "94335208"
---
# <a name="tutorial-copy-data-from-azure-data-box-via-smb-preview"></a>教學課程：透過 SMB 從 Azure 資料箱複製資料 (預覽)

本教學課程說明如何使用本機 Web UI 連線至內部部署伺服器，並將資料從資料箱複製到該伺服器中。 資料箱裝置包含從 Azure 儲存體帳戶匯出的資料。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
>
> * 必要條件
> * 連線至資料箱
> * 從資料箱複製資料

[!INCLUDE [Data Box feature is in preview](../../includes/data-box-feature-is-preview-info.md)]

## <a name="prerequisites"></a>必要條件

在您開始前，請確定：

1. 您已建立 Azure 資料箱的訂單。
    - 針對匯入訂單，請參閱[教學課程：訂購 Azure 資料箱](data-box-deploy-ordered.md)。
    - 針對匯出訂單，請參閱[教學課程：訂購 Azure 資料箱](data-box-deploy-export-ordered.md)。
2. 您已收到資料箱，且入口網站中的訂購狀態為 [已交付]。
3. 您有一部主機電腦，且想要將資料從資料箱複製到其中。 您的主機電腦必須符合下列條件：
   * 執行[支援的作業系統](data-box-system-requirements.md)。
   * 連線至高速網路。 強烈建議您具有至少一個 10 GbE 的連線。 如果無法使用 10 GbE 連線，請使用 1 GbE 資料連結，但是複製速度會受到影響。

## <a name="connect-to-data-box"></a>連線至資料箱

[!INCLUDE [data-box-shares](../../includes/data-box-shares.md)]

如果使用 Windows Server 主機電腦，請依照下列步驟來連線至資料箱。

1. 第一個步驟是驗證並啟動工作階段。 移至 [連線並複製]。 選取 [取得認證]，以取得您儲存體帳戶相關共用的存取認證。 

    ![取得共用認證](media/data-box-deploy-export-copy-data/get-share-credentials-1.png)

2. 在 [存取共用及複製資料] 對話方塊中，複製與共用相對應的 [使用者名稱] 和 [密碼]。 選取 [確定]。
    
    ![取得共用認證、存取共用及複製資料](media/data-box-deploy-export-copy-data/get-share-credentials-2.png)

3. 若要從主機電腦存取與您的儲存體帳戶相關聯的共用 (在下列範例中為 *exportbvtdataset2*)，請開啟命令視窗。 在命令提示字元中，輸入：

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    視您的資料格式而定，共用路徑如下所示：
    - Azure 區塊 Blob - `\\169.254.143.85\exportbvtdataset2_BlockBlob`
    - Azure 分頁 Blob - `\\169.254.143.85\exportbvtdataset2_PageBlob`
    - Azure 檔案服務 - `\\169.254.143.85\exportbvtdataset2_AzFile`

4. 出現提示時，請輸入共用的密碼。 下列範例說明如何透過前述命令連線至共用。

    ```
    C:\Users\Databoxuser>net use \\169.254.143.85\exportbvtdataset2_BlockBlob /u:exportbvtdataset2
    Enter the password for 'exportbvtdataset2' to connect to '169.254.143.85':
    The command completed successfully.
    ```

5. 按 Windows + R。在 [執行] 視窗中，指定 `\\<device IP address>`。 選取 [確定] 以開啟檔案總管。
    
    ![透過檔案總管連線至共用，輸入裝置 IP](media/data-box-deploy-export-copy-data/connect-shares-file-explorer-1.png)

    您現在應可看到以資料夾形式呈現的共用。
    
    ![透過檔案總管連線至共用，檢視共用](media/data-box-deploy-export-copy-data/connect-shares-file-explorer-2.png)

    
使用 Linux 用戶端時，請使用下列命令來掛接 SMB 共用。 下方的 "vers" 參數是您的 Linux 主機支援的 SMB 版本。 請在下列命令中插入適當的版本。 如需資料箱支援哪些 SMB 版本的相關資訊，請參閱 [Linux 用戶端支援的檔案系統](./data-box-system-requirements.md#supported-file-transfer-protocols-for-clients) 

```console
sudo mount -t nfs -o vers=2.1 169.254.143.85:/exportbvtdataset2_BlockBlob /home/databoxubuntuhost/databox
```

## <a name="copy-data-from-data-box"></a>從資料箱複製資料

連線至資料箱共用後，下一個步驟是複製資料。

[!INCLUDE [data-box-export-review-logs](../../includes/data-box-export-review-logs.md)]


 連線至 SMB 共用之後，開始複製資料。 您可以使用任何與 SMB 相容的檔案複製工具 (例如 Robocopy) 來複製資料。 使用 Robocopy 可起始多個複製作業。 


如需 Robocopy 命令的詳細資訊，請移至 [Robocopy 和數個範例](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx)。

複製完成後，請移至 [儀表板]，並確認您的裝置上已使用的空間和可用空間。

您現在可以繼續將資料箱寄送到 Microsoft 了。


## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解 Azure 資料箱的相關主題，像是：

> [!div class="checklist"]
>
> * 必要條件
> * 連線至資料箱
> * 從資料箱複製資料

請繼續進行下一個教學課程，了解如何將資料箱送回給 Microsoft。

> [!div class="nextstepaction"]
> [將您的 Azure 資料箱寄送給 Microsoft](./data-box-deploy-export-picked-up.md)