---
title: 教學課程：使用 AzCopy 將內部部署資料遷移至 Azure 儲存體 | Microsoft Docs
description: 在本教學課程中，您將使用 AzCopy 從 Blob、資料表以及檔案內容，來回移轉資料或複製資料。 輕鬆地將資料從您的本機儲存體移轉至 Azure 儲存體。
author: normesta
ms.service: storage
ms.topic: tutorial
ms.date: 05/14/2019
ms.author: normesta
ms.reviewer: seguler
ms.subservice: common
ms.openlocfilehash: 154a7b17fc09c55e83b65eef8d479904c36e87eb
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92791183"
---
#  <a name="tutorial-migrate-on-premises-data-to-cloud-storage-with-azcopy"></a>教學課程：使用 AzCopy 將內部部署資料遷移至雲端儲存體

AzCopy 是命令列工具，可使用簡單的命令從 Azure Blob 儲存體、Azure 檔案和 Azure 資料表儲存體來回複製資料。 這些命令的設計目的是獲得最佳效能。 您可以使用 AzCopy 在檔案系統和儲存體帳戶之間，或者儲存體帳戶之間複製資料。 AzCopy 可用來將本機 (內部) 資料複製到儲存體帳戶。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 建立儲存體帳戶。 
> * 使用 AzCopy 上傳您的所有資料。
> * 基於測試目的修改資料。
> * 建立排程工作或 cron 作業來識別要上傳的新檔案。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="prerequisites"></a>Prerequisites

若要完成本教學課程，請下載最新版本的 AzCopy。 請參閱[開始使用 AzCopy](storage-use-azcopy-v10.md)。

如果您使用 Windows，您將需要 [Schtasks](/windows/win32/taskschd/schtasks)，因為本教學課程會用它來排程工作。 Linux 使用者則應使用 crontab 命令。

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

## <a name="create-a-container"></a>建立容器

第一個步驟是建立容器，因為 Blob 一律須上傳至容器中。 容器會作為組織 Blob 群組的方法，就像您在電腦上使用資料夾來組織檔案一樣。

請遵循下列步驟來建立容器：

1. 選取主頁面中的 [儲存體帳戶]  按鈕，並選取您所建立的儲存體帳戶。
2. 選取 [服務]  底下的 [Blob]  ，然後選取 [容器]  。

   ![顯示容器建立的螢幕擷取畫面](media/storage-azcopy-migrate-on-premises-data/CreateContainer.png)
 
容器名稱必須以字母或數字開頭。 它們可以包含字母、數字和連字號字元 (-)。 如需 Blob 和容器的其他命名規則，請參閱[命名和參考容器、Blob 及中繼資料](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)。

## <a name="download-azcopy"></a>下載 AzCopy

下載 AzCopy V10 可執行檔。

- [Windows](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [Linux](https://aka.ms/downloadazcopy-v10-linux) (tar)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

將 AzCopy 檔案放在電腦上的任何位置。 將檔案位置新增至您的系統路徑變數，即可從您電腦上的任何資料夾參考此可執行檔。

## <a name="authenticate-with-azure-ad"></a>使用 Azure AD 進行驗證

首先，將[儲存體 Blob 資料參與者](../../role-based-access-control/built-in-roles.md#storage-queue-data-contributor)角色指派到您的身分識別。 請參閱[使用 Azure 入口網站來指派用於存取 Blob 和佇列資料的 Azure 角色](./storage-auth-aad-rbac-portal.md)。

然後，開啟命令提示字元，輸入下列命令並按下 ENTER 鍵。

```azcopy
azcopy login
```

此命令傳回驗證碼和網站的 URL。 開啟網站，提供程式碼，然後選擇 [下一步]  按鈕。

![顯示登入提示的螢幕擷取畫面](media/storage-use-azcopy-v10/azcopy-login.png)

隨即會出現登入視窗。 在該視窗中，使用您的 Azure 帳戶認證登入 Azure 帳戶。 順利登入之後，您可以關閉瀏覽器視窗，然後開始使用 AzCopy。

## <a name="upload-contents-of-a-folder-to-blob-storage"></a>將資料夾的內容上傳至 Blob 儲存體

您可以在 [Windows](./storage-use-azcopy-v10.md) 或 [Linux](./storage-use-azcopy-v10.md) 上使用 AzCopy，將資料夾中的所有檔案都上傳至 Blob 儲存體。 若要上傳資料夾中的所有 blob，請輸入下列 AzCopy 命令：

```AzCopy
azcopy copy "<local-folder-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive=true
```

* 將 `<local-folder-path>` 預留位置取代為包含檔案的資料夾路徑 (例如：`C:\myFolder` 或 `/mnt/myFolder`)。

* 使用您的儲存體帳戶名稱取代 `<storage-account-name>` 預留位置。

* 將 `<container-name>` 預留位置取代為您建立的容器名稱。

若要以遞迴方式將指定目錄的內容上傳到 Blob 儲存體，請指定 `--recursive` 選項。 當您使用此選項執行 AzCopy 時，所有的子資料夾和其檔案也都會上傳。

## <a name="upload-modified-files-to-blob-storage"></a>將修改過的檔案上傳到 Blob 儲存體

您可以根據檔案的上次修改時間，使用 AzCopy 來上傳檔案。 

若要這麼做，請基於測試目的，在您的來源目錄中修改或建立新的檔案。 然後，使用 AzCopy `sync` 命令。

```AzCopy
azcopy sync "<local-folder-path>" "https://<storage-account-name>.blob.core.windows.net/<container-name>" --recursive=true
```

* 將 `<local-folder-path>` 預留位置取代為包含檔案的資料夾路徑 (例如：`C:\myFolder` 或 `/mnt/myFolder`)。

* 使用您的儲存體帳戶名稱取代 `<storage-account-name>` 預留位置。

* 將 `<container-name>` 預留位置取代為您建立的容器名稱。

若要深入了解 `sync` 命令，請參閱[同步檔案](storage-use-azcopy-blobs.md#synchronize-files)。

## <a name="create-a-scheduled-task"></a>建立排程工作

您可以建立執行 AzCopy 命令指令碼的排程工作或 cron 作業。 該指令碼會識別新的內部部署資料，並在特定時間間隔將其上傳至雲端儲存體。

將 AzCopy 命令複製到文字編輯器。 將 AzCopy 命令的參數值更新為適當的值。 針對 AzCopy 將檔案另存為 `script.sh` (Linux) 或 `script.bat` (Windows)。 

這些範例會假設您的資料夾名稱是 `myFolder`、儲存體帳戶名稱是 `mystorageaccount`，以及您的容器名稱是 `mycontainer`。

> [!NOTE]
> Linux 範例會附加 SAS 權杖。 您必須在命令中提供該權杖。 目前版本的 AzCopy V10 不支援 Cron 作業中的 Azure AD 授權。

# <a name="linux"></a>[Linux](#tab/linux)

```bash
azcopy sync "/mnt/myfiles" "https://mystorageaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-05-30T06:57:40Z&st=2019-05-29T22:57:40Z&spr=https&sig=BXHippZxxx54hQn%2F4tBY%2BE2JHGCTRv52445rtoyqgFBUo%3D" --recursive=true
```

# <a name="windows"></a>[Windows](#tab/windows)

```bash
azcopy sync "C:\myFolder" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive=true
```

---

在本教學課程中，[Schtasks](/windows/win32/taskschd/schtasks) 會用來在 Windows 上建立排程工作。 [Crontab](http://crontab.org/) 命令會用來在 Linux 上建立 cron 作業。

 **Schtasks** 可讓系統管理員建立、刪除、查詢、變更、執行和結束本機或遠端電腦上的排程工作。 **Cron** 可讓 Linux 和 Unix 使用者在指定的日期和時間執行命令或指令碼，方法是使用 [cron 運算式](https://en.wikipedia.org/wiki/Cron#CRON_expression)。

# <a name="linux"></a>[Linux](#tab/linux)

若要在 Linux 上建立 cron 作業，請在終端機上輸入下列命令：

```bash
crontab -e
*/5 * * * * sh /path/to/script.sh
```

在命令中指定 cron 運算式 `*/5 * * * *` 會指出殼層指令碼 `script.sh` 應該每隔五分鐘執行一次。 您可以排程指令碼在每日、每月或每年的指定時間執行。 若要深入了解針對作業執行設定日期和時間，請參閱 [cron 運算式](https://en.wikipedia.org/wiki/Cron#CRON_expression)。

# <a name="windows"></a>[Windows](#tab/windows)

若要在 Windows 上建立排程工作，請於命令提示字元或者 PowerShell 中輸入下列命令：

此範例會假設您的指令碼位於電腦的根磁碟機，但您可以將指令碼放在任何位置。

```cmd
schtasks /CREATE /SC minute /MO 5 /TN "AzCopy Script" /TR C:\script.bat
```

該命令會使用：
- `/SC` 參數來指定以分鐘排程。
- `/MO` 參數來指定五分鐘的間隔。
- `/TN` 參數來指定工作名稱。
- `/TR` 參數來指定 `script.bat` 檔案的路徑。

若要深入了解在 Windows 上建立排程工作的相關資訊，請參閱 [Schtasks](/previous-versions/orphan-topics/ws.10/cc772785(v=ws.10)#BKMK_minutes)。

---

若要驗證排程工作/cron 作業能夠正確執行，請在您的 `myFolder` 目錄中建立新的檔案。 等候五分鐘以確認新檔案已上傳到您的儲存體帳戶。 請移至您的記錄目錄，以檢視排程工作或 cron 作業的輸出記錄。

## <a name="next-steps"></a>後續步驟

若要深入了解將內部部署資料移動至 Azure 儲存體 (或反向作業) 的相關方式，請參考下列連結：

* [從 Azure 儲存體來回移動資料](./storage-choose-data-transfer-solution.md?toc=%252fazure%252fstorage%252ffiles%252ftoc.json)。  

如需有關 AzCopy 的詳細資訊，請參閱下列文章：

* [開始使用 AzCopy](storage-use-azcopy-v10.md)

* [使用 AzCopy 和 Blob 儲存體轉送資料](storage-use-azcopy-blobs.md)

* [使用 AzCopy 和檔案儲存體轉送資料](storage-use-azcopy-files.md)

* [使用 AzCopy 和 Amazon S3 貯體轉送資料](storage-use-azcopy-s3.md)
 
* [對 AzCopy 進行設定、最佳化及疑難排解](storage-use-azcopy-configure.md)