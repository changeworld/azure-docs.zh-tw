---
title: 透過 SMB 在 Azure 資料箱上複製資料的教學課程 | Microsoft Docs
description: 在本教學課程中，了解如何使用 SMB 搭配本機 Web UI，來連線至主機電腦並將資料從中複製到 Azure 資料箱。
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 09/29/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: fcdc5d0e7254b8e491285baae6c2a1bc6979e437
ms.sourcegitcommit: d9ba60f15aa6eafc3c5ae8d592bacaf21d97a871
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/06/2020
ms.locfileid: "91766315"
---
::: zone target="docs"

# <a name="tutorial-copy-data-to-azure-data-box-via-smb"></a>教學課程：透過 SMB 將資料複製到 Azure 資料箱

::: zone-end

::: zone target="chromeless"

## <a name="copy-data-to-azure-data-box"></a>將資料複製到 Azure 資料箱

::: zone-end

::: zone target="docs"

本教學課程說明如何使用本機 Web UI 來連線至主機電腦並從中複製資料。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
>
> * 必要條件
> * 連線至資料箱
> * 將資料複製到資料箱

## <a name="prerequisites"></a>必要條件

在您開始前，請確定：

1. 您已完成[教學課程：設定 Azure 資料箱](data-box-deploy-set-up.md)。
2. 您已收到資料箱，且入口網站中的訂購狀態為 [已交付]。
3. 您有一部主機電腦，其中包含要複製到資料箱的資料。 您的主機電腦必須符合下列條件：
   * 執行[支援的作業系統](data-box-system-requirements.md)。
   * 連線至高速網路。 強烈建議您具有至少一個 10 GbE 的連線。 如果無法使用 10 GbE 連線，請使用 1 GbE 資料連結，但是複製速度會受到影響。

## <a name="connect-to-data-box"></a>連線至資料箱

根據選取的儲存體帳戶，資料箱最多會建立：

* 每個相關聯的儲存體帳戶三個共用 (針對 GPv1 與 GPv2)。
* 一個適用於進階儲存體的共用。
* 一個適用於 Blob 儲存體帳戶的共用。

在區塊 Blob 與分頁 Blob 共用底下，第一層實體是容器，而第二層實體是 Blob。 在 Azure 檔案共用底下，第一層實體是共用，第二層實體是檔案。

下表顯示您資料箱上的共用 UNC 路徑，以及用於上傳資料的 Azure 儲存體路徑 URL。 最終的 Azure 儲存體路徑 URL 可以衍生自 UNC 共用路徑。
 
|Azure 儲存體類型  | 資料箱共用            |
|-------------------|--------------------------------------------------------------------------------|
| Azure 區塊 Blob | <li>共用的 UNC 路徑：`\\<DeviceIPAddress>\<StorageAccountName_BlockBlob>\<ContainerName>\files\a.txt`</li><li>Azure 儲存體 URL：`https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Azure 分頁 Blob  | <li>共用的 UNC 路徑：`\\<DeviceIPAddres>\<StorageAccountName_PageBlob>\<ContainerName>\files\a.txt`</li><li>Azure 儲存體 URL：`https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Azure 檔案       |<li>共用的 UNC 路徑：`\\<DeviceIPAddres>\<StorageAccountName_AzFile>\<ShareName>\files\a.txt`</li><li>Azure 儲存體 URL：`https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |      

如果使用 Windows Server 主機電腦，請依照下列步驟來連線至資料箱。

1. 第一個步驟是驗證並啟動工作階段。 移至 [連線並複製]。 選取 [SMB]，以取得您儲存體帳戶相關共用的存取認證。 

    ![取得 SMB 共用的共用認證](media/data-box-deploy-copy-data/get-share-credentials1.png)

2. 在 [存取共用及複製資料] 對話方塊中，複製與共用相對應的 [使用者名稱] 和 [密碼]。 選取 [確定]。
    
    ![取得共用的使用者名稱和密碼](media/data-box-deploy-copy-data/get-share-credentials2.png)

3. 若要從主機電腦存取與您的儲存體帳戶相關聯的共用 (在下列範例中為 utsac1)，請開啟命令視窗。 在命令提示字元中，輸入：

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    視您的資料格式而定，共用路徑如下所示：
    - Azure 區塊 Blob - `\\10.126.76.138\utSAC1_202006051000_BlockBlob`
    - Azure 分頁 Blob - `\\10.126.76.138\utSAC1_202006051000_PageBlob`
    - Azure 檔案服務 - `\\10.126.76.138\utSAC1_202006051000_AzFile`

4. 出現提示時，請輸入共用的密碼。 下列範例說明如何透過前述命令連線至共用。

    ```
    C:\Users\Databoxuser>net use \\10.126.76.138\utSAC1_202006051000_BlockBlob /u:testuser1
    Enter the password for 'testuser1' to connect to '10.126.76.138':
    The command completed successfully.
    ```

4. 按 Windows + R。在 [執行] 視窗中，指定 `\\<device IP address>`。 選取 [確定] 以開啟檔案總管。
    
    ![透過檔案總管連線至共用](media/data-box-deploy-copy-data/connect-shares-file-explorer1.png)

    您現在應可看到以資料夾形式呈現的共用。
    
    ![檔案總管中顯示的共用](media/data-box-deploy-copy-data/connect-shares-file-explorer2.png)

    **一律針對您想複製的檔案，在共用之下建立一個資料夾，然後將檔案複製到該資料夾**。 在區塊 Blob 和分頁 Blob 底下建立的資料夾，代表資料以 Blob 形式上傳至的容器。 您無法將檔案直接複製到儲存體帳戶中的 [root] 資料夾。
    
使用 Linux 用戶端時，請使用下列命令來掛接 SMB 共用。 下方的 "vers" 參數是您的 Linux 主機支援的 SMB 版本。 請在下列命令中插入適當的版本。 如需資料箱支援哪些 SMB 版本的相關資訊，請參閱 [Linux 用戶端支援的檔案系統](https://docs.microsoft.com/azure/databox/data-box-system-requirements#supported-file-systems-for-linux-clients) 

```console
sudo mount -t nfs -o vers=2.1 10.126.76.138:/utSAC1_202006051000_BlockBlob /home/databoxubuntuhost/databox
```

## <a name="copy-data-to-data-box"></a>將資料複製到資料箱

連線至資料箱共用後，下一個步驟是複製資料。 開始複製資料之前，請檢閱下列注意事項：

* 確實將資料複製到與適當資料格式相對應的共用。 例如，將區塊 Blob 資料複製到區塊 Blob 的共用。 將 VHD 複製到分頁 Blob。 如果資料格式不符合適當的共用類型，則在稍後步驟中，資料上傳至 Azure 的作業將會失敗。
* 複製資料時，請確定資料大小符合 [Azure 儲存體帳戶大小限制](data-box-limits.md#azure-storage-account-size-limits)中所述的大小限制。
* 如果資料 (由資料箱上傳) 同時由資料箱以外的其他應用程式上傳，則可能導致上傳作業失敗和資料損毀。
* 我們的建議如下：
  * 您不要同時使用 SMB 與 NFS。
  * 將相同的資料複製到 Azure 上的相同最終目的地。

  在這類情況下，無法判斷最後的結果。
* 一律針對您想複製的檔案，在共用之下建立一個資料夾，然後將檔案複製到該資料夾。 在區塊 Blob 和分頁 Blob 底下建立的資料夾，代表資料以 Blob 形式上傳至的容器。 您無法將檔案直接複製到儲存體帳戶中的 [root] 資料夾。

> [!IMPORTANT]
> 在確認資料箱已將您的資料移轉至 Azure 儲存體之前，請務必先保留一份來源資料複本。

連線至 SMB 共用之後，開始複製資料。 您可以使用任何與 SMB 相容的檔案複製工具 (例如 Robocopy) 來複製資料。 使用 Robocopy 可起始多個複製作業。 使用下列命令：

```console
robocopy <Source> <Target> * /e /r:3 /w:60 /is /nfl /ndl /np /MT:32 or 64 /fft /Log+:<LogFile>
```

下表說明這些屬性。
    
|屬性  |描述  |
|---------|---------|
|/e     |複製子目錄，包含空的目錄。         |
|/r:     |指定失敗複製的重試次數。         |
|/w:     |指定重試之間的等待時間 (以秒為單位)。         |
|/is     |包含相同的檔案。         |
|/nfl     |指定不記錄檔案名稱。         |
|/ndl    |指定不記錄目錄名稱。        |
|/np     |指定將不會顯示複製作業的進度 (到目前為止複製的檔案或目錄數目)。 顯示進度會大幅降低效能。         |
|/MT     | 使用多執行緒，建議使用 32 或 64 個執行緒。 此選項無法用於加密檔案。 您可能需要區分加密和未加密的檔案。 不過，單一執行緒複製會大幅降低效能。           |
|/fft     | 用以降低任何檔案系統的時間戳記細微性。        |
|/b     | 以備份模式複製檔案。        |
|/z    | 以重新啟動模式複製檔案；如果環境不穩定，請使用此選項。 此選項會因為額外的記錄而降低輸送量。      |
| /zb     | 使用重新啟動模式。 如果存取遭拒，此選項會使用備份模式。 此選項會因為執行檢查點而降低輸送量。         |
|/efsraw     | 以 EFS 原始模式複製所有加密檔案。 這只適用於加密檔案。         |
|log+:\<LogFile>| 將輸出附加至現有的記錄檔。|    
 
下列範例顯示將檔案複製到資料箱的 robocopy 命令輸出。

```output
C:\Users>robocopy

    -------------------------------------------------------------------------------
    ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------

        Started : Thursday, March 8, 2018 2:34:53 PM
        Simple Usage :: ROBOCOPY source destination /MIR

        source :: Source Directory (drive:\path or \\server\share\path).
        destination :: Destination Dir  (drive:\path or \\server\share\path).
                /MIR :: Mirror a complete directory tree.

    For more usage information run ROBOCOPY /?

    ****  /MIR can DELETE files as well as copy them !

C:\Users>Robocopy C:\Git\azure-docs-pr\contributor-guide \\10.126.76.172\devicemanagertest1_AzFile\templates /MT:32

    -------------------------------------------------------------------------------
    ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------

        Started : Thursday, March 8, 2018 2:34:58 PM
        Source : C:\Git\azure-docs-pr\contributor-guide\
            Dest : \\10.126.76.172\devicemanagertest1_AzFile\templates\

        Files : *.*

        Options : *.* /DCOPY:DA /COPY:DAT /MT:32 /R:5 /W:60

    ------------------------------------------------------------------------------

    100%        New File                 206        C:\Git\azure-docs-pr\contributor-guide\article-metadata.md
    100%        New File                 209        C:\Git\azure-docs-pr\contributor-guide\content-channel-guidance.md
    100%        New File                 732        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-index.md
    100%        New File                 199        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pr-criteria.md
                New File                 178        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pull-request-co100%  .md
                New File                 250        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pull-request-et100%  e.md
    100%        New File                 174        C:\Git\azure-docs-pr\contributor-guide\create-images-markdown.md
    100%        New File                 197        C:\Git\azure-docs-pr\contributor-guide\create-links-markdown.md
    100%        New File                 184        C:\Git\azure-docs-pr\contributor-guide\create-tables-markdown.md
    100%        New File                 208        C:\Git\azure-docs-pr\contributor-guide\custom-markdown-extensions.md
    100%        New File                 210        C:\Git\azure-docs-pr\contributor-guide\file-names-and-locations.md
    100%        New File                 234        C:\Git\azure-docs-pr\contributor-guide\git-commands-for-master.md
    100%        New File                 186        C:\Git\azure-docs-pr\contributor-guide\release-branches.md
    100%        New File                 240        C:\Git\azure-docs-pr\contributor-guide\retire-or-rename-an-article.md
    100%        New File                 215        C:\Git\azure-docs-pr\contributor-guide\style-and-voice.md
    100%        New File                 212        C:\Git\azure-docs-pr\contributor-guide\syntax-highlighting-markdown.md
    100%        New File                 207        C:\Git\azure-docs-pr\contributor-guide\tools-and-setup.md
    ------------------------------------------------------------------------------

                Total    Copied   Skipped  Mismatch    FAILED    Extras
    Dirs :         1         1         1         0         0         0
    Files :        17        17         0         0         0         0
    Bytes :     3.9 k     3.9 k         0         0         0         0
C:\Users>
```

若要將效能最佳化，複製資料時請使用下列 robocopy 參數。

|    平台    |    大部分是 < 512 KB 的小檔案                           |    大部分是 512 KB - 1 MB 的中型檔案                      |    大部分是 > 1 MB 的大檔案                             |   
|----------------|--------------------------------------------------------|--------------------------------------------------------|--------------------------------------------------------|
|    資料箱         |    2 個 Robocopy 工作階段 <br> 每個工作階段 16 個執行緒    |    3 個 Robocopy 工作階段 <br> 每個工作階段 16 個執行緒    |    2 個 Robocopy 工作階段 <br> 每個工作階段 24 個執行緒    |

如需 Robocopy 命令的詳細資訊，請移至 [Robocopy 和數個範例](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx)。

在複製程序期間如果發生任何錯誤，您會看到通知。

![連線並複製中的複製錯誤通知](media/data-box-deploy-copy-data/view-errors-1.png)

選取 [下載問題清單]。

![下載及檢視 [連線並複製] 頁面上的錯誤 2](media/data-box-deploy-copy-data/view-errors-2.png)

開啟清單以檢視錯誤的詳細資料，並選取 [解析 URL] 以檢視建議的解決方法。

![下載及檢視 [連線並複製] 頁面上的錯誤 3](media/data-box-deploy-copy-data/view-errors-3.png)

如需詳細資訊，請參閱[在資料複製到資料箱期間檢視錯誤記錄](data-box-logs.md#view-error-log-during-data-copy)。 如需資料複製期間的詳細錯誤清單，請參閱[針對資料箱問題進行疑難排解](data-box-troubleshoot.md)。

為了確保資料完整性，在複製資料時會計算內嵌的總和檢查碼。 複製完成後，請確認您的裝置上已使用的空間和可用空間。

![確認儀表板上的可用空間和已使用的空間](media/data-box-deploy-copy-data/verify-used-space-dashboard.png)

::: zone-end

::: zone target="chromeless"

您可以透過 SMB、NFS、REST、資料複製服務或受控磁碟，將資料從來源伺服器複製到您的資料箱。

在每個案例中，請確定共用和資料夾的名稱及資料大小遵循 [Azure 儲存體和資料箱服務限制](data-box-limits.md)中所述的指導方針。

## <a name="copy-data-via-smb"></a>透過 SMB 複製資料

若要透過 SMB 複製資料：

1. 如果使用 Windows 主機，請使用下列命令來連線到 SMB 共用：

    `\\<IP address of your device>\ShareName`

2. 若要取得共用存取認證，請前往資料箱之本機 Web UI 中的 [連線並複製] 頁面。
3. 使用與 SMB 相容的檔案複製工具 (例如 Robocopy) 來將資料複製到共用。 

如需逐步指示，請移至[教學課程：透過 SMB 將資料複製到 Azure 資料箱](data-box-deploy-copy-data.md)。

## <a name="copy-data-via-nfs"></a>透過 NFS 複製資料

若要透過 NFS 複製資料：

1. 如果使用 NFS 主機，請使用下列命令在您的資料箱上掛接 NFS 共用：

    `sudo mount <Data Box device IP>:/<NFS share on Data Box device> <Path to the folder on local Linux computer>`

2. 若要取得共用存取認證，請前往資料箱之本機 Web UI 中的 [連線並複製] 頁面。
3. 使用 `cp` 或 `rsync` 命令來複製您的資料。

如需逐步指示，請移至[教學課程：透過 NFS 將資料複製到 Azure 資料箱](data-box-deploy-copy-data-via-nfs.md)。

## <a name="copy-data-via-rest"></a>透過 REST 複製資料

若要透過 REST 複製資料：

1. 若要透過 REST API 使用資料箱 Blob 儲存體來複製資料，您可以透過 HTTP 或 HTTPS 來進行連線。
2. 若要將資料複製到資料箱 Blob 儲存體，您可以使用 AzCopy。

如需逐步指示，請移至[教學課程：透過 REST API 將資料複製到 Azure 資料箱 Blob 儲存體](data-box-deploy-copy-data-via-nfs.md)。

## <a name="copy-data-via-data-copy-service"></a>透過資料複製服務來複製資料

若要透過資料複製服務來複製資料：

1. 若要使用資料複製服務來複製資料，您需要建立一項作業。 在您資料箱的本機 Web UI 中，移至 [管理] > [複製資料] > [建立]。
2. 填寫參數並建立作業。

如需逐步指示，請移至[教學課程：使用資料複製服務將資料複製到 Azure 資料箱](data-box-deploy-copy-data-via-copy-service.md)。

## <a name="copy-data-to-managed-disks"></a>將資料複製到受控磁碟

若要複製資料受控磁碟：

1. 訂購資料箱裝置時，您應該已選取受控磁碟作為儲存體目的地。
2. 您可以透過 SMB 或 NFS 共用連線到資料箱。
3. 然後，您可以透過 SMB 或 NFS 工具來複製資料。

如需逐步指示，請移至[教學課程：使用資料箱匯入資料以作為 Azure 中的受控磁碟](data-box-deploy-copy-data-from-vhds.md)。

::: zone-end

::: zone target="docs"

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解 Azure 資料箱的相關主題，像是：

> [!div class="checklist"]
>
> * 必要條件
> * 連線至資料箱
> * 將資料複製到資料箱

請繼續進行下一個教學課程，了解如何將資料箱送回給 Microsoft。

> [!div class="nextstepaction"]
> [將您的 Azure 資料箱寄送給 Microsoft](./data-box-deploy-picked-up.md)

::: zone-end
