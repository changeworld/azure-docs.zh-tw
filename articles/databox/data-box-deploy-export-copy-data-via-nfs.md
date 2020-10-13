---
title: 透過 NFS 從 Azure 資料箱複製資料的教學課程 | Microsoft Docs
description: 了解如何透過 NFS 從 Azure 資料箱複製資料
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 10/01/2020
ms.author: alkohli
ms.openlocfilehash: bd8e6d4175c57bd31c3fd83bf6f9669d2b65ffb2
ms.sourcegitcommit: 487a9f5272300d60df2622c3d13e794d54680f90
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/02/2020
ms.locfileid: "91660833"
---
# <a name="tutorial-copy-data-from-azure-data-box-via-nfs-preview"></a>教學課程：透過 NFS 從 Azure 資料箱複製資料 (預覽)

本教學課程說明如何透過 NFS 連線至內部部署資料伺服器，並將資料從資料箱的本機 Web UI 複製到該伺服器中。 資料箱上的資料會從您的 Azure 儲存體帳戶匯出。

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

如果您使用 Linux 主機電腦，請執行下列步驟設定資料箱，以允許 NFS 用戶端的存取。 資料箱一次最多可以連線五個 NFS 用戶端。

1. 針對允許存取共用的用戶端提供其 IP 位址：

    1.  在本機 Web UI 中，移至 [連線並複製] 頁面。 在 [NFS 設定] 下方，按一下 [NFS 用戶端存取]。 

        ![開啟 NFS 用戶端存取](media/data-box-deploy-export-copy-data/nfs-client-access-1.png)

    1. 若要新增 NFS 用戶端，請提供用戶端的 IP 位址，然後按一下 [新增]。 資料箱一次最多可以連線五個 NFS 用戶端。 完成時，請按一下 [確定]。

         ![新增 NFS 用戶端](media/data-box-deploy-export-copy-data/nfs-client-access-2.png)

2. 確定 Linux 主機電腦已安裝NFS 用戶端的[支援版本](data-box-system-requirements.md)。 針對您的 Linux 發行版本，請使用特定版本。 

3. 安裝 NFS 用戶端之後，請使用下列命令將 NFS 共用掛接在您的資料箱裝置上：

    `sudo mount <Data Box device IP>:/<NFS share on Data Box device> <Path to the folder on local Linux computer>`

    下列範例說明如何透過 NFS 連線至資料箱共用。 資料箱裝置 IP 為 `10.161.23.130`，共用 `Mystoracct_Blob` 會掛接在 ubuntuVM 上，掛接點為 `/home/databoxubuntuhost/databox`。

    `sudo mount -t nfs 10.161.23.130:/Mystoracct_Blob /home/databoxubuntuhost/databox`
    
    針對 Mac 用戶端，您必須新增額外的選項，如下所示： 
    
    `sudo mount -t nfs -o sec=sys,resvport 10.161.23.130:/Mystoracct_Blob /home/databoxubuntuhost/databox`

    **一律針對您想複製的檔案，在共用之下建立一個資料夾，然後將檔案複製到該資料夾**。 在區塊 Blob 和分頁 Blob 底下建立的資料夾，代表資料以 Blob 形式上傳至的容器。 您無法將檔案直接複製到儲存體帳戶中的 [root] 資料夾。

## <a name="copy-data-from-data-box"></a>從資料箱複製資料

連線至資料箱共用後，下一個步驟是複製資料。

[!INCLUDE [data-box-export-review-logs](../../includes/data-box-export-review-logs.md)]

 您現在可以開始複製資料了。 如果您使用 Linux 主機電腦，請使用類似於 Robocopy 的複製公用程式。 在 Linux 中有替代工具可供使用，包括 [rsync](https://rsync.samba.org/)、[FreeFileSync](https://www.freefilesync.org/)、[Unison](https://www.cis.upenn.edu/~bcpierce/unison/) 或 [Ultracopier](https://ultracopier.first-world.info/)。  

`cp` 命令是複製目錄的最佳選項之一。 如需其使用方式的詳細資訊，請移至 [cp 手冊頁](http://man7.org/linux/man-pages/man1/cp.1.html)。

如果使用 rsync 選項進行多執行緒複製，請遵循下列指導方針：

* 根據您的 Linux 用戶端所使用的檔案系統，安裝 **CIFS Utils** 或 **NFS Utils** 套件。

    `sudo apt-get install cifs-utils`

    `sudo apt-get install nfs-utils`

* 安裝 **Rsync** 和 **Parallel** (依 Linux 發行的版本而有所不同)。

    `sudo apt-get install rsync`
   
    `sudo apt-get install parallel` 

* 建立掛接點。

    `sudo mkdir /mnt/databox`

* 掛接磁碟區。

    `sudo mount -t NFS4  //Databox IP Address/share_name /mnt/databox` 

* 鏡像資料夾目錄結構。  

    `rsync -za --include='*/' --exclude='*' /local_path/ /mnt/databox`

* 複製檔案。

    `cd /local_path/; find -L . -type f | parallel -j X rsync -za {} /mnt/databox/{}`

     其中，j 會指定平行處理數目，X = 平行複製數目

     建議您一開始先使用 16 個平行複製，然後再根據可用的資源增加執行緒數目。

> [!IMPORTANT]
> 不支援下列 Linux 檔案類型：符號連結、字元檔案、區塊檔案、通訊端和管線。 這些檔案類型會導致在**準備寄送**步驟期間發生失敗。

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
