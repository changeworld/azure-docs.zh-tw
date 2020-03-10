---
title: 將資料複製到 Azure 資料箱磁碟的教學課程 | Microsoft Docs
description: 使用本教學課程以了解如何將資料複製到您的 Azure 資料箱磁碟
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 93a7181a3b720a3b313bb75855b2564c4cd33bc1
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78380464"
---
::: zone target="docs"

# <a name="tutorial-copy-data-to-azure-data-box-disk-and-verify"></a>教學課程：將資料複製到 Azure 資料箱磁碟並確認

::: zone-end

::: zone target="chromeless"

## <a name="copy-data-to-azure-data-box-disk-and-validate"></a>將資料複製到 Azure 資料箱磁碟並驗證

連接磁碟並將其解除鎖定之後，您可以將資料從來源資料伺服器複製到磁碟。 資料複製完成之後，請驗證資料以確保其會成功上傳至 Azure。

::: zone-end

::: zone target="docs"

本教學課程說明如何從主機電腦複製資料，然後產生總和檢查碼來確認資料完整性。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 將資料複製到資料箱磁碟
> * 驗證資料

## <a name="prerequisites"></a>Prerequisites

在您開始前，請確定：
- 您已完成[教學課程：安裝及設定您的 Azure 資料箱磁碟](data-box-disk-deploy-set-up.md)。
- 您的磁碟已解除鎖定，並連線到用戶端電腦。
- 您用來將資料複製到磁碟的用戶端電腦必須執行[支援的作業系統](data-box-disk-system-requirements.md#supported-operating-systems-for-clients)。
- 請確定資料的預定儲存體類型符合[支援的儲存體類型](data-box-disk-system-requirements.md#supported-storage-types-for-upload)。
- 檢閱 [Azure 物件大小限制中的受控磁碟限制](data-box-disk-limits.md#azure-object-size-limits)。


## <a name="copy-data-to-disks"></a>將資料複製到磁碟

檢閱下列考量，然後再將資料複製到磁碟：

- 您必須負責確保將資料複製到資料夾中，該資料夾對應至適當的資料格式。 例如，將區塊 Blob 資料複製到區塊 Blob 的資料夾。 如果資料格式與適當資料夾 (儲存體類型) 不相符，則在稍後步驟中，資料上傳至 Azure 會失敗。
- 複製資料時，請確定資料大小符合 [Azure 儲存體和資料箱磁碟限制](data-box-disk-limits.md)中所述的大小限制。
- 如果資料 (由資料箱磁碟上傳) 同時由資料箱磁碟以外的其他應用程式上傳，則可能導致上傳作業失敗和資料損毀。

   > [!IMPORTANT]
   >  如果在建立順序時，將受控磁碟指定為其中一個儲存體目的地，則以下部分適用。

- 在所有預先建立的資料夾之間以及在所有資料箱磁碟之間，您在一個資源群組中只能有一個具指定名稱的受控磁碟。 這表示上傳至預先建立資料夾的 VHD 應具備唯一名稱。 請確定指定的名稱不會與資源群組中現有的受控磁碟相符。 如果 VHD 具有相同名稱，則只會將一個 VHD 轉換為具該名稱的受控磁碟。 其他 VHD 會以分頁 Blob 形式上傳至暫存的儲存體帳戶。
- 一律將 VHD 複製到其中一個預先建立的資料夾。 如果您在這些資料夾外部或您所建立的資料夾中複製 VHD，就會將 VHD 上傳至 Azure 儲存體帳戶以作為分頁 Blob，而非受控磁碟。
- 只能上傳固定的 VHD 來建立受控磁碟。 不支援動態 VHD、差異 VHD 或 VHDX 檔案。


執行下列步驟以從電腦連線到資料箱磁碟，並且複製資料。

1. 檢視已解除鎖定磁碟機的內容。 根據建立資料箱磁碟訂單時所選取的選項而定，磁碟中預先建立的資料夾和子資料夾清單會有所不同。

    |選取的儲存體目的地  |儲存體帳戶類型|暫存的儲存體帳戶類型 |資料夾和子資料夾  |
    |---------|---------|---------|------------------|
    |儲存體帳戶     |GPv1 或 GPv2                 | NA | BlockBlob <br> PageBlob <br> AzureFile        |
    |儲存體帳戶     |Blob 儲存體帳戶         | NA | BlockBlob        |
    |受控磁碟     |NA | GPv1 或 GPv2         | ManagedDisk<ul> <li>PremiumSSD</li><li>StandardSSD</li><li>StandardHDD</li></ul>        |
    |儲存體帳戶 <br> 受控磁碟     |GPv1 或 GPv2 | GPv1 或 GPv2         |BlockBlob <br> PageBlob <br> AzureFile <br> ManagedDisk<ul> <li> PremiumSSD </li><li>StandardSSD</li><li>StandardHDD</li></ul>         |
    |儲存體帳戶 <br> 受控磁碟    |Blob 儲存體帳戶 | GPv1 或 GPv2         |BlockBlob <br> ManagedDisk<ul> <li>PremiumSSD</li><li>StandardSSD</li><li>StandardHDD</li></ul>         |

    以下顯示已指定 GPv2 儲存體帳戶的訂單範例螢幕擷取畫面：

    ![磁碟機的內容](media/data-box-disk-deploy-copy-data/data-box-disk-content.png)
 
2. 將需要以區塊 Blob 形式匯入的資料複製到 *BlockBlob* 資料夾。 同樣地，將 VHD/VHDX 等資料複製到 *PageBlob* 資料夾，並將資料複製到 *AzureFile* 資料夾中。

    系統會在 Azure 儲存體帳戶中為 BlockBlob 和 PageBlob 資料夾下的每個子資料夾建立容器。 BlockBlob 和 PageBlob 資料夾下的所有檔案，都會複製到 Azure 儲存體帳戶下的預設容器 `$root`。 `$root` 容器中的任何檔案一律會上傳為區塊 Blob。

   將檔案複製到 *AzureFile* 資料夾內的資料夾。 *AzureFile* 資料夾內的子資料夾會建立檔案共用。 直接複製到 *AzureFile* 資料夾的檔案會失敗並上傳為區塊 Blob。

    如果根目錄中有檔案和資料夾存在，則您必須在開始複製資料之前，將這些項目移到不同的資料夾。

    > [!IMPORTANT]
    > 所有的容器、Blob 及檔案名稱都應符合 [Azure 命名慣例](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions)。 如果未遵循這些規則，則將資料上傳至 Azure 會失敗。

3. 複製檔案時，確定檔案的區塊 Blob 不會超過約 4.7 TiB、分頁 Blob 不會超過約 8 TiB，而 Azure 檔案儲存體不會超過約 1 TiB。 
4. 您可以使用檔案總管的拖放功能來複製資料。 您也可以使用任何 SMB 相容的檔案複製工具 (例如 Robocopy) 來複製資料。 可以使用下列 Robocopy 命令來起始多個複製作業：

    `Robocopy <source> <destination>  * /MT:64 /E /R:1 /W:1 /NFL /NDL /FFT /Log:c:\RobocopyLog.txt` 
    
    命令的參數和選項會以表格形式顯示，如下所示：
    
    |參數/選項  |描述 |
    |--------------------|------------|
    |來源            | 指定來源目錄的路徑。        |
    |Destination       | 指定目的地目錄的路徑。        |
    |/E                  | 複製子目錄，包含空的目錄。 |
    |/MT[:N]             | 建立具有 N 個執行緒的多執行緒複製，其中 N 是介於 1 到 128 之間的整數。 <br>N 的預設值為 8。        |
    |/R： \<N >             | 指定失敗複製的重試次數。 N 的預設值為 1000000 (1 百萬次重試)。        |
    |/W： \<N >             | 指定重試之間的等待時間 (以秒為單位)。 N 的預設值為 30 (等候時間 30 秒)。        |
    |/NFL                | 指定不會記錄檔案名稱。        |
    |/NDL                | 指定不會記錄目錄名稱。        |
    |/FFT                | 假設 FAT 檔案時間 (兩秒精確度)。        |
    |/Log:\<記錄檔>     | 將狀態輸出寫入至記錄檔 (覆寫現有記錄檔)。         |

    您可採平行方式使用多個磁碟，在每個磁碟上執行多項作業。

6. 當作業正在進行中時，檢查複製狀態。 下列範例顯示將檔案複製到資料箱磁碟的 robocopy 命令輸出。

    ```
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
    
    C:\Users>Robocopy C:\Git\azure-docs-pr\contributor-guide \\10.126.76.172\devicemanagertest1_AzFile\templates /MT:64 /E /R:1 /W:1 /FFT 
    -------------------------------------------------------------------------------
       ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------
    
      Started : Thursday, March 8, 2018 2:34:58 PM
       Source : C:\Git\azure-docs-pr\contributor-guide\
         Dest : \\10.126.76.172\devicemanagertest1_AzFile\templates\
    
        Files : *.*
    
      Options : *.* /DCOPY:DA /COPY:DAT /MT:8 /R:1000000 /W:30
    
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
       Times :   0:00:05   0:00:00                       0:00:00   0:00:00
        
       Speed :                5620 Bytes/sec.
       Speed :               0.321 MegaBytes/min.
       Ended : Thursday, March 8, 2018 2:34:59 PM
        
    C:\Users>
    ```
 
    若要將效能最佳化，複製資料時請使用下列 robocopy 參數。

    |    平台    |    大部分是 < 512 KB 的小檔案                           |    大部分是 512 KB - 1 MB 的中型檔案                      |    大部分是 > 1 MB 的大檔案                             |   
    |----------------|--------------------------------------------------------|--------------------------------------------------------|--------------------------------------------------------|
    |    資料箱磁碟        |    4 個 Robocopy 工作階段* <br> 每個工作階段 16 個執行緒    |    2 個 Robocopy 工作階段* <br> 每個工作階段 16 個執行緒    |    2 個 Robocopy 工作階段* <br> 每個工作階段 16 個執行緒    |
    
    **每個 Robocopy 工作階段最多可以有 7,000 個目錄和 1 億 5 千萬個檔案。*
    
    >[!NOTE]
    > 上述建議參數以室內測試所使用的環境為基礎。
    
    如需 Robocopy 命令的詳細資訊，請移至 [Robocopy 和數個範例](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx)。

6. 開啟目標資料夾，以檢視並確認已複製的檔案。 如果您在複製程序期間遇到任何錯誤，請下載記錄檔以進行疑難排解。 記錄檔位於 robocopy 命令所指定的位置。
 
### <a name="split-and-copy-data-to-disks"></a>將資料分割並複製到磁碟

如果您使用多個磁碟，而且具有必須加以分割並複製到所有磁碟上的大型資料集，則您可能會用到此選擇性程序。 「資料箱分割複製」工具可協助您分割和複製 Windows 電腦上的資料。

>[!IMPORTANT]
> 「資料箱分割複製」工具也會驗證您的資料。 如果您使用「資料箱分割複製」工具來複製資料，您可以略過[驗證步驟](#validate-data)。
> 受控磁碟不支援分割複製工具。

1. 在您的 Windows 電腦上，請確定已下載「資料箱分割複製」工具，並且已在本機資料夾中解壓縮。 此工具會在您下載適用於 Windows 的資料箱磁碟工具組時一併下載。
2. 開啟檔案總管。 請記下指派給資料箱磁碟的資料來源磁碟機和磁碟機代號。 

     ![分割複製資料](media/data-box-disk-deploy-copy-data/split-copy-1.png)
 
3. 識別要複製的來源資料。 例如，在此案例中是：
    - 已識別到下列區塊 Blob 資料。

         ![分割複製資料](media/data-box-disk-deploy-copy-data/split-copy-2.png)    

    - 已識別到下列分頁 Blob 資料。

         ![分割複製資料](media/data-box-disk-deploy-copy-data/split-copy-3.png)
 
4. 請移至軟體解壓縮後所在的資料夾。 在該資料夾中找出 `SampleConfig.json` 檔案。 這是您可以修改並儲存的唯讀檔案。

   ![分割複製資料](media/data-box-disk-deploy-copy-data/split-copy-4.png)
 
5. 修改 `SampleConfig.json` 檔案。
 
   - 提供作業名稱。 這會在資料箱磁碟中建立資料夾，而這最後會在與這些磁碟相關聯的 Azure 儲存體帳戶中成為容器。 作業名稱必須遵循 Azure 容器的命名慣例。 
   - 提供來源路徑並記下 `SampleConfigFile.json` 中的路徑格式。 
   - 輸入對應到目標磁碟的磁碟機代號。 資料會取自來源路徑，並複製到多個磁碟。
   - 提供記錄檔的路徑。 根據預設，這會傳送到 `.exe` 目前所在的目錄。

     ![分割複製資料](media/data-box-disk-deploy-copy-data/split-copy-5.png)

6. 若要驗證檔案格式，請移至 `JSONlint`。 將檔案儲存為 `ConfigFile.json`。 

     ![分割複製資料](media/data-box-disk-deploy-copy-data/split-copy-6.png)
 
7. 開啟命令提示字元視窗。 

8. 執行 `DataBoxDiskSplitCopy.exe`。 類型

    `DataBoxDiskSplitCopy.exe PrepImport /config:<Your-config-file-name.json>`

     ![分割複製資料](media/data-box-disk-deploy-copy-data/split-copy-7.png)
 
9. 按 Entert 後繼續執行指令碼。

    ![分割複製資料](media/data-box-disk-deploy-copy-data/split-copy-8.png)
  
10. 分割和複製資料集後，複製工作階段的分割複製工具摘要會隨即顯示。 下方顯示一項範例輸出。

    ![分割複製資料](media/data-box-disk-deploy-copy-data/split-copy-9.png)
 
11. 請確認資料分散在目標磁碟上。 
 
    ![分割複製資料](media/data-box-disk-deploy-copy-data/split-copy-10.png)
    ![分割複製資料](media/data-box-disk-deploy-copy-data/split-copy-11.png)
     
    如果您進一步檢查 `n:` 磁碟機的內容，您會看到對應至區塊 Blob 和分頁 Blob 格式資料的兩個子資料夾已建立。
    
     ![分割複製資料](media/data-box-disk-deploy-copy-data/split-copy-12.png)

12. 如果複製工作階段動作失敗，則使用下列命令來復原並繼續：

    `DataBoxDiskSplitCopy.exe PrepImport /config:<configFile.json> /ResumeSession`

如果您在使用分割複製工具時看到錯誤，請移至如何[針對分割複製工具錯誤進行疑難排解](data-box-disk-troubleshoot-data-copy.md)。

資料複製完成之後，接下來您可以驗證資料。 如果您使用「分割複製」工具，請略過驗證 (「分割複製」工具也會進行驗證)，並進入下一個教學課程。


## <a name="validate-data"></a>驗證資料

如果您未使用「分割複製」工具複製資料，則必須驗證資料。 若要確認資料，請執行下列步驟。

1. 在磁碟機的 DataBoxDiskImport`DataBoxDiskValidation.cmd`*資料夾中執行*，以進行總和檢查碼驗證。
    
    ![資料箱磁碟驗證工具的輸出](media/data-box-disk-deploy-copy-data/data-box-disk-validation-tool-output.png)

2. 請選擇適當的選項。 **我們建議您選取選項 2，一律驗證檔案並產生總和檢查碼**。 視您的資料大小而定，此步驟可能需要一段時間。 指令碼完成之後，結束命令視窗。 如果在驗證和產生總和檢查碼期間發生任何錯誤，您會收到通知及錯誤記錄的連結。

    ![總和檢查碼輸出](media/data-box-disk-deploy-copy-data/data-box-disk-checksum-output.png)

    > [!TIP]
    > - 請在兩次的執行之間重設工具。
    > - 如果處理包含小檔案 (~KB) 的大型資料集，請使用選項 1。 此選項只會驗證檔案，因為產生總和檢查碼可能需要很長的時間，而且效能可能會變得非常慢。

3. 如果使用了多個磁碟，請對每個磁碟執行命令。

如果您在驗證期間看到錯誤，請參閱[針對驗證錯誤進行疑難排解](data-box-disk-troubleshoot.md)。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解 Azure 資料箱磁碟的相關主題，像是：

> [!div class="checklist"]
> * 將資料複製到資料箱磁碟
> * 確認資料完整性

請前進到下一個教學課程，以了解如何寄回資料箱磁碟，並確認資料上傳至 Azure。

> [!div class="nextstepaction"]
> [將您的 Azure 資料箱寄回給 Microsoft](./data-box-disk-deploy-picked-up.md)

::: zone-end

::: zone target="chromeless"

### <a name="copy-data-to-disks"></a>將資料複製到磁碟

執行下列步驟以從電腦連線到資料箱磁碟，並且複製資料。

1. 檢視已解除鎖定磁碟機的內容。 根據建立資料箱磁碟訂單時所選取的選項而定，磁碟中預先建立的資料夾和子資料夾清單會有所不同。
2. 將資料複製到與適當資料格式相對應的資料夾。 例如，將非結構化資料複製到 *BlockBlob* 資料夾、將 VHD 或 VHDX 資料複製到 *PageBlob* 資料夾，以及將檔案複製到 *AzureFile*。 如果資料格式與適當資料夾 (儲存體類型) 不相符，則在稍後步驟中，資料上傳至 Azure 的作業將會失敗。

    - 確定所有容器、Blob 和檔案都符合 [Azure 命名慣例](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions)和 [Azure 物件大小限制](data-box-disk-limits.md#azure-object-size-limits)。 如果未遵循這些規則或限制，則將資料上傳至 Azure 的作業將會失敗。     
    - 如果訂單的其中一個儲存體目的地為受控磁碟，請參閱[受控磁碟](data-box-disk-limits.md#managed-disk-naming-conventions)的命名慣例。
    - 系統會在 Azure 儲存體帳戶中為 BlockBlob 和 PageBlob 資料夾下的每個子資料夾建立容器。 BlockBlob 和 PageBlob 資料夾下的所有檔案，都會複製到 Azure 儲存體帳戶下的預設容器 $root。 $root 容器中的任何檔案一律會上傳為區塊 Blob。
    - 在 AzureFile 資料夾內建立子資料夾。 這個子資料夾會對應至雲端中的檔案共用。 將檔案複製到子資料夾。 直接複製到 *AzureFile* 資料夾的檔案會失敗並上傳為區塊 Blob。
    - 如果根目錄中有檔案和資料夾存在，則您必須在開始複製資料之前，將這些項目移到不同的資料夾。

3. 請使用檔案總管的拖放功能，或任何與 SMB 相容的檔案複製工具 (例如 Robocopy)，來複製資料。 您可以使用下列命令起始多個複製作業：

    ```
    Robocopy <source> <destination>  * /MT:64 /E /R:1 /W:1 /NFL /NDL /FFT /Log:c:\RobocopyLog.txt
    ```
4. 開啟目標資料夾，以檢視並確認已複製的檔案。 如果您在複製程序期間遇到任何錯誤，請下載記錄檔以進行疑難排解。 記錄檔位於 robocopy 命令所指定的位置。

如果您使用多個磁碟，而且具有必須分割並複製到所有磁碟上的大型資料集，請使用[分割和複製](data-box-disk-deploy-copy-data.md#split-and-copy-data-to-disks)的選擇性程序。

### <a name="validate-data"></a>驗證資料

請執行下列步驟以驗證資料。

1. 在磁碟機的 DataBoxDiskImport`DataBoxDiskValidation.cmd`*資料夾中執行*，以進行總和檢查碼驗證。
2. 使用選項 2 來驗證您的檔案並產生總和檢查碼。 視您的資料大小而定，此步驟可能需要一段時間。 如果在驗證和產生總和檢查碼期間發生任何錯誤，您會收到通知及錯誤記錄的連結。

    如需有關資料驗證的詳細資訊，請參閱[驗證資料](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-copy-data#validate-data)。 如果您在驗證期間遇到錯誤，請參閱[針對驗證錯誤進行疑難排解](data-box-disk-troubleshoot.md)。

::: zone-end
