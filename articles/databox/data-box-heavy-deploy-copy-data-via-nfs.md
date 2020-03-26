---
title: 透過 NFS 將資料複製到 Azure Data Box Heavy 的教學課程 | Microsoft Docs
description: 了解如何透過 NFS 將資料複製到您的 Azure Data Box Heavy
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 4361cee3d07408c3abb5031d2ab18c15c92c5e0a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "79222805"
---
# <a name="tutorial-copy-data-to-azure-data-box-heavy-via-nfs"></a>教學課程：透過 NFS 將資料複製到 Azure Data Box Heavy

本教學課程說明如何使用 Azure Data Box Heavy 的本機 Web UI，連接主機電腦並從中複製資料。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * Prerequisites
> * 連線至 Data Box Heavy
> * 將資料複製到 Data Box Heavy

## <a name="prerequisites"></a>Prerequisites

在您開始前，請確定：

1. 您已完成[教學課程：設定 Azure Data Box Heavy](data-box-heavy-deploy-set-up.md)。
2. 您已收到 Data Box Heavy，且入口網站中的訂購狀態為 [已交付]  。
3. 您有一部主機電腦，其中包含要複製到 Data Box Heavy 的資料。 您的主機電腦必須符合下列條件：
    - 執行[支援的作業系統](data-box-heavy-system-requirements.md)。
    - 連線至高速網路。 如需最快的複製速度，可平行使用兩個 40 GbE 的連線 (每個節點一個連線)。 如果您沒有 40 GbE 連線可用，我們建議您至少有兩個 10 GbE 的連線 (每個節點一個連線)。 

## <a name="connect-to-data-box-heavy"></a>連線至 Data Box Heavy

根據選取的儲存體帳戶，Data Box Heavy 最多會建立：
- 每個相關聯的儲存體帳戶三個共用 (針對 GPv1 與 GPv2)。
- 一個適用於進階儲存體的共用。
- 一個適用於 Blob 儲存體帳戶的共用。

這些共用會同時建立於裝置的兩個節點上。

在區塊 Blob 和分頁 Blob 共用之下：
- 第一層實體是容器。
- 第二層實體是 Blob。

在 Azure 檔案儲存體的共用之下：
- 第一層實體是共用。
- 第二層實體是檔案。

下表顯示您 Data Box Heavy 上的共用 UNC 路徑，以及用於上傳資料的 Azure 儲存體路徑 URL。 最終的 Azure 儲存體路徑 URL 可以衍生自 UNC 共用路徑。
 
|                   |                                                            |
|-------------------|--------------------------------------------------------------------------------|
| Azure 區塊 Blob | <li>共用的 UNC 路徑：`//<DeviceIPAddress>/<StorageAccountName_BlockBlob>/<ContainerName>/files/a.txt`</li><li>Azure 儲存體 URL：`https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Azure 分頁 Blob  | <li>共用的 UNC 路徑：`//<DeviceIPAddres>/<StorageAccountName_PageBlob>/<ContainerName>/files/a.txt`</li><li>Azure 儲存體 URL：`https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Azure 檔案       |<li>共用的 UNC 路徑：`//<DeviceIPAddres>/<StorageAccountName_AzFile>/<ShareName>/files/a.txt`</li><li>Azure 儲存體 URL：`https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |

如果您使用 Linux 主機電腦，請執行下列步驟設定您的裝置，以允許 NFS 用戶端的存取。

1. 針對允許存取共用的用戶端提供其 IP 位址。 在本機 Web UI 中，移至 [連線並複製]  頁面。 在 [NFS 設定]  下方，按一下 [NFS 用戶端存取]  。 

    ![設定 NFS 用戶端存取 1](media/data-box-deploy-copy-data/nfs-client-access.png)

2. 提供 NFS 用戶端的 IP 位址，然後按一下 [新增]  。 您可以重複此步驟，以設定多個 NFS 用戶端的存取。 按一下 [確定]  。

    ![設定 NFS 用戶端存取 2](media/data-box-deploy-copy-data/nfs-client-access2.png)

2. 確定 Linux 主機電腦已安裝NFS 用戶端的[支援版本](data-box-system-requirements.md)。 針對您的 Linux 發行版本，請使用特定版本。 

3. 安裝 NFS 用戶端之後，請使用下列命令將 NFS 共用掛接在您的資料箱裝置上：

    `sudo mount <Data Box Heavy device IP>:/<NFS share on Data Box Heavy device> <Path to the folder on local Linux computer>`

    下列範例說明如何透過 NFS 連線至 Data Box Heavy 共用。 Data Box Heavy IP 為 `10.161.23.130`，共用 `Mystoracct_Blob` 會掛接在 ubuntuVM 上，掛接點為 `/home/databoxheavyubuntuhost/databoxheavy`。

    `sudo mount -t nfs 10.161.23.130:/Mystoracct_Blob /home/databoxheavyubuntuhost/databoxheavy`
    
    針對 Mac 用戶端，您必須新增額外的選項，如下所示： 
    
    `sudo mount -t nfs -o sec=sys,resvport 10.161.23.130:/Mystoracct_Blob /home/databoxheavyubuntuhost/databoxheavy`

    **一律針對您想複製的檔案，在共用之下建立一個資料夾，然後將檔案複製到該資料夾**。 在區塊 Blob 和分頁 Blob 底下建立的資料夾，代表資料以 Blob 形式上傳至的容器。 您無法將檔案直接複製到儲存體帳戶中的 [root]  資料夾。

## <a name="copy-data-to-data-box-heavy"></a>將資料複製到 Data Box Heavy

連線至 Data Box Heavy 共用後，下一個步驟是複製資料。 開始複製資料之前，請檢閱下列注意事項：

- 確實將資料複製到與適當資料格式相對應的共用。 例如，將區塊 Blob 資料複製到區塊 Blob 的共用。 將 VHD 複製到分頁 Blob。 如果資料格式不符合適當的共用類型，則在稍後步驟中，資料上傳至 Azure 的作業將會失敗。
-  複製資料時，請確定資料大小符合 [Azure 儲存體和 Data Box Heavy 限制](data-box-heavy-limits.md) 中所述的大小限制。 
- 如果資料 (由 Data Box Heavy 上傳) 同時由 Data Box Heavy 以外的其他應用程式上傳，則可能導致上傳作業失敗和資料損毀。
- 建議您不要同時使用 SMB 與 NFS，或將相同的資料複製到 Azure 上的相同最終目的地。 在這種情況下，無法判斷最後的結果。
- **一律針對您想複製的檔案，在共用之下建立一個資料夾，然後將檔案複製到該資料夾**。 在區塊 Blob 和分頁 Blob 底下建立的資料夾，代表資料以 Blob 形式上傳至的容器。 您無法將檔案直接複製到儲存體帳戶中的 [root]  資料夾。
- 如果從 NFS 共用將區分大小寫的目錄和檔案名稱內嵌至 Data Box Heavy 上的 NFS： 
    - 在名稱中會保留大小寫。
    - 檔案不區分大小寫。
    
    例如，如果複製 `SampleFile.txt` 和 `Samplefile.Txt`，在複製到裝置時，名稱將會保留大小寫，但第二個檔案將會覆寫第一個，因為兩者被視為相同的檔案。


如果您使用 Linux 主機電腦，請使用類似於 Robocopy 的複製公用程式。 在 Linux 中有替代工具可供使用，包括 [rsync](https://rsync.samba.org/)、[FreeFileSync](https://www.freefilesync.org/)、[Unison](https://www.cis.upenn.edu/~bcpierce/unison/) 或 [Ultracopier](https://ultracopier.first-world.info/)。  

`cp` 命令是複製目錄的最佳選項之一。 如需其使用方式的詳細資訊，請移至 [cp 手冊頁](http://man7.org/linux/man-pages/man1/cp.1.html)。

如果使用 rsync 選項進行多執行緒複製，請遵循下列指導方針：

 - 根據您的 Linux 用戶端所使用的檔案系統，安裝 **CIFS Utils** 或 **NFS Utils** 套件。

    `sudo apt-get install cifs-utils`

    `sudo apt-get install nfs-utils`

 -  安裝 **Rsync** 和 **Parallel** (依 Linux 發行的版本而有所不同)。

    `sudo apt-get install rsync`
   
    `sudo apt-get install parallel` 

 - 建立掛接點。

    `sudo mkdir /mnt/databoxheavy`

 - 掛接磁碟區。

    `sudo mount -t NFS4  //Databox-heavy-IP-Address/share_name /mnt/databoxheavy` 

 - 鏡像資料夾目錄結構。  

    `rsync -za --include='*/' --exclude='*' /local_path/ /mnt/databoxheavy`

 - 複製檔案。 

    `cd /local_path/; find -L . -type f | parallel -j X rsync -za {} /mnt/databoxheavy/{}`

     其中，j 會指定平行處理數目，X = 平行複製數目

     建議您一開始先使用 16 個平行複製，然後再根據可用的資源增加執行緒數目。

> [!IMPORTANT]
> 不支援下列 Linux 檔案類型：符號連結、字元檔案、區塊檔案、通訊端和管線。 這些檔案類型會導致在**準備寄送**步驟期間發生失敗。

開啟目標資料夾，以檢視並確認已複製的檔案。 如果您在複製程序期間遇到任何錯誤，請下載錯誤檔以進行疑難排解。 如需詳細資訊，請參閱[在資料複製到 Data Box Heavy 期間檢視錯誤記錄](data-box-logs.md#view-error-log-during-data-copy)。 如需資料複製期間的詳細錯誤清單，請參閱[針對 Data Box Heavy 問題進行疑難排解](data-box-troubleshoot.md)。

為了確保資料完整性，在複製資料時會計算內嵌的總和檢查碼。 複製完成後，請確認您的裝置上已使用的空間和可用空間。
    
   ![確認儀表板上的可用空間和已使用的空間](media/data-box-deploy-copy-data/verify-used-space-dashboard.png)


## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解 Azure Data Box Heavy 的相關主題，像是：

> [!div class="checklist"]
> * Prerequisites
> * 連線至 Data Box Heavy
> * 將資料複製到 Data Box Heavy


請繼續進行下一個教學課程，了解如何將資料箱送回給 Microsoft。

> [!div class="nextstepaction"]
> [將 Azure Data Box Heavy 寄送給 Microsoft](./data-box-heavy-deploy-picked-up.md)

