---
title: 載入 Azure HPC 快取
description: 如何將客戶端連接到 Azure HPC 快取服務
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: rohogue
ms.openlocfilehash: f176e30cfaf9a52e4f58091b7fc76098a4c88a48
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657372"
---
# <a name="mount-the-azure-hpc-cache"></a>掛接 Azure HPC Cache

創建快取後,NFS 用戶端可以使用`mount`簡單 命令存取它。 該命令將 Azure HPC 快取上的特定儲存目標路徑連接到用戶端電腦上的本地目錄。

載入指令是由以下元素組成:

* 快取的載入位址之一(列在快取概述頁上)
* 建立儲存目標時設定的虛擬命名空間路徑
* 要在用戶端上使用的本地端路徑
* 最佳化 NFS 載入成功的指令參數

快取的 **「裝載指令」** 頁會收集資訊和推薦的選項,並創建可以複製的原型裝載命令。 閱讀[使用裝載說明實用程式](#use-the-mount-instructions-utility)瞭解詳細資訊。

## <a name="prepare-clients"></a>準備客戶

按照本節中的指南,確保客戶端能夠裝載 Azure HPC 緩存。

### <a name="provide-network-access"></a>提供網路存取

用戶端計算機必須具有對快取的虛擬網路和專用子網路的網路存取許可權。

例如,在同一虛擬網路中創建用戶端 VM,或使用虛擬網路中的終結點、閘道或其他解決方案從外部存取。 (請記住,除了緩存本身之外,其他內容都不應託管在緩存的子網內。

### <a name="install-utilities"></a>安裝公用程式

安裝適當的 Linux 實用程式軟體以支援 NFS 裝載命令:

* 對於紅帽企業 Linux 或 SuSE:`sudo yum install -y nfs-utils`
* 對於烏本圖或德比安:`sudo apt-get install nfs-common`

### <a name="create-a-local-path"></a>建立本地端路徑

在每個用戶端上創建本地目錄路徑以連接到快取。 為要裝載的每個存儲目標創建路徑。

範例： `sudo mkdir -p /mnt/hpc-cache-1/target3`

## <a name="use-the-mount-instructions-utility"></a>使用載入說明實用程式

從 Azure 門戶中的緩存檢視的 **「設定**」部分打開 **「載入說明」** 頁。

![門戶中的 Azure HPC 緩存實例的螢幕截圖,載入了「設定>裝載指令頁](media/mount-instructions.png)

裝載命令頁包括有關用戶端裝載過程和先決條件的資訊,以及可用於創建可複製裝載命令的欄位。

要使用此頁面,請按照以下步驟操作:

<!--1.  In step one of **Mounting your file system**, enter the path that the client will use to access the Azure HPC Cache storage target.

   * This path is local to the client.
   * After you provide the directory name, the field populates with a command you can copy. Use this command on the client directly or in a setup script to create the directory path on the client VM. -->

1. 查看用戶端先決條件並安裝使用 NFS`mount`命令所需的實用程式,如上述[在「準備用戶端](#prepare-clients)」中所述。

1. **安裝檔案系統**的步驟一<!-- label will change --> 給出了在用戶端上創建本地路徑的範例命令。 這是用戶端將用於從 Azure HPC 快取內容的路徑。

   請注意路徑名稱,以便在需要時可以在命令中對其進行修改。

1. 在步驟 2 中,選擇其中一個可用的 IP 位址。 這裡列出快取所有[客戶端載入點](#find-mount-command-components)。 確保您有一個系統來平衡所有 IP 位址之間的負載。

1. 步驟三中的欄位自動填充原型裝載命令。 按下欄位右側的複製符號,自動將其複製到剪貼簿。

   > [!NOTE]
   > 在使用複製命令之前,請檢查該命令。 您可能需要自定義用戶端裝載路徑和儲存目標虛擬命名空間路徑,這些路徑在此介面中尚未選擇。 您還應更新載入指令選項以反映以下[建議的選項](#mount-command-options)。 閱讀[「瞭解裝載」命令語法](#understand-mount-command-syntax)以尋求説明。

1. 在用戶端電腦上使用複製的裝載命令(如果需要進行編輯)將其連接到 Azure HPC 緩存上的存儲目標。 可以直接從用戶端命令行發出該命令,或在用戶端設置腳本或範本中包括裝載命令。

## <a name="understand-mount-command-syntax"></a>瞭解載入命令語法

載入指令的以下形式:

> 蘇多安裝*cache_mount_address*:/*namespace_pathlocal_path* *local_path* [*選項*]

範例：

```bash
root@test-client:/tmp# mkdir hpccache
root@test-client:/tmp# sudo mount 10.0.0.28:/blob-demo-0722 ./hpccache/ -o hard,proto=tcp,mountproto=tcp,retry=30
root@test-client:/tmp#
```

此命令成功後,存儲匯出的內容將在用戶端的``hpccache``目錄中可見。

### <a name="mount-command-options"></a>載入指令選項

對於可靠的用戶端裝載,在載入指令中傳遞這些設定和參數:

> 安裝 -o 硬,proto_tcp,mountproto_tcp,retry_30 {CACHE_IP_ADDRESS}:/$NAMESPACE_PATH} {LOCAL_FILESYSTEM_MOUNT_POINT}

| 建議的載入指令設定 | |
--- | ---
``hard`` | Azure HPC 快取的軟載載與應用程式故障和可能的資料遺失相關聯。
``proto=tcp`` | 此選項支援適當處理 NFS 網路錯誤的功能。
``mountproto=tcp`` | 此選項支援在掛接作業中適當處理網路錯誤的功能。
``retry=<value>`` | 設定 ``retry=30`` 可避免暫時性的掛接失敗。 (執行前景掛接時建議使用不同的值)。

### <a name="find-mount-command-components"></a>尋找載入指令元件

如果要創建裝載命令而不使用 **「裝載指令」** 頁,可以在快取**概述**頁上找到裝載位址和**儲存目標**頁上的虛擬命名空間路徑。

![Azure HPC 快取實體的「概述」頁面的螢幕截圖,右下角的裝載地址清單周圍有一個突出顯示框](media/hpc-cache-mount-addresses.png)

> [!NOTE]
> 快取載入位址對應於緩存子網中的網路介面。 在資源組中,這些 NIC 列出的名稱以`-cluster-nic-`結尾 ,以數位結尾。 不要更改或刪除這些介面,否則緩存將不可用。

虛擬命名空間路徑顯示在**儲存目標頁面**中。 單擊單個儲存目標名稱以查看其詳細資訊,包括與其關聯的聚合命名空間路徑。

![快取儲存目標面板的螢幕截圖,在表的 Path 列中條目周圍有一個突出顯示框](media/hpc-cache-view-namespace-paths.png)

## <a name="next-steps"></a>後續步驟

* 要將資料移至快取的儲存目標,請閱讀[填充新的 Azure Blob 儲存](hpc-cache-ingest.md)。
