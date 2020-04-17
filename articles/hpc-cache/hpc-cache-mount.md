---
title: 載入 Azure HPC 快取
description: 如何將客戶端連接到 Azure HPC 快取服務
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: v-erkel
ms.openlocfilehash: a44232f06b455e20530271723e816c2117b339a0
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458339"
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

在每個用戶端上創建本地目錄路徑以連接到快取。 為要裝載的每個命名空間路徑創建路徑。

範例： `sudo mkdir -p /mnt/hpc-cache-1/target3`

Azure 門戶中的[「裝載說明」](#use-the-mount-instructions-utility)頁包含可以複製的原型命令。

將用戶端電腦連接到緩存時,將此路徑與表示存儲目標導出的虛擬命名空間路徑相關聯。 為用戶端將使用的每個虛擬命名空間路徑創建目錄。

## <a name="use-the-mount-instructions-utility"></a>使用載入說明實用程式

可以使用 Azure 門戶中的 **「裝載指令」** 頁創建可複製裝載命令。 從門戶中緩存檢視的 **「設定**」部分打開頁面。

在用戶端上使用 命令之前,請確保客戶端滿足先決條件,並具有使用`mount`NFS 命令所需的軟體,如上述在[「準備用戶端](#prepare-clients)」中所述。

![門戶中的 Azure HPC 緩存實例的螢幕截圖,載入了「設定>裝載指令頁](media/mount-instructions.png)

按照此過程創建裝載命令。

1. 自訂**用戶端路徑**欄位。 此欄位提供一個範例命令,可用於在用戶端上創建本地路徑。 用戶端在此目錄中本地訪問 Azure HPC 快取中的內容。

   按一下該欄位並編輯該指令以包含所需的目錄名稱。 這個名稱顯示在字串的末尾`sudo mkdir -p`

   ![用戶端路徑欄位的螢幕截圖,游標位於末尾](media/mount-edit-client.png)

   完成編輯欄位後,頁面底部的裝載命令將更新新用戶端路徑。

1. 從清單中選擇**快取載入位址**。 此選單列的快取所有[客戶端載入點](#find-mount-command-components)。

   平衡所有可用裝載位址的客戶端負載,以更好的緩存性能。

   ![快取載入位址欄位的螢幕擷取,選擇器顯示三個 IP 位址可供選擇](media/mount-select-ip.png)

1. 選擇要用於客戶端的**虛擬命名空間路徑**。 這些路徑連結到後端存儲系統上的匯出。

   ![命名空間路徑欄位的螢幕截圖,選擇器開啟](media/mount-select-target.png)

   您可以在「存儲目標」 門戶頁上查看和更改虛擬命名空間路徑。 閱讀[添加存儲目標](hpc-cache-add-storage.md)以查看如何。

   要瞭解有關 Azure HPC 快取集合的總命名空間功能的詳細資訊,請閱讀[規劃聚合命名空間](hpc-cache-namespace.md)。

1. 步驟三中的 **「裝載」命令**欄位使用自訂裝載命令自動填充,該命令使用在前面的欄位中設置的裝載位址、虛擬命名空間路徑和用戶端路徑。

   按下欄位右側的複製符號,自動將其複製到剪貼簿。

   ![命名空間路徑欄位的螢幕截圖,選擇器開啟](media/mount-command-copy.png)

1. 使用用戶端電腦上的複製載入命令將其連接到 Azure HPC 快取。 可以直接從用戶端命令行發出該命令,或在用戶端設置腳本或範本中包括裝載命令。

## <a name="understand-mount-command-syntax"></a>瞭解載入命令語法

載入指令的以下形式:

> sudo 安裝 [*選項*] *cache_mount_address*:/*namespace_pathlocal_path* *local_path*

範例：

```bash
root@test-client:/tmp# mkdir hpccache
root@test-client:/tmp# sudo mount -o hard,proto=tcp,mountproto=tcp,retry=30 10.0.0.28:/blob-demo-0722 hpccache
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

虛擬命名空間路徑顯示在每個儲存目標的詳細資訊頁上。 單擊單個儲存目標名稱以查看其詳細資訊,包括與其關聯的聚合命名空間路徑。

![儲存目標詳細資訊頁面的螢幕截圖(標題"更新存儲目標")。 表的虛擬命名空間路徑列中項目周圍有一個突出顯示框](media/hpc-cache-view-namespace-paths.png)

## <a name="next-steps"></a>後續步驟

* 要將資料移至快取的儲存目標,請閱讀[填充新的 Azure Blob 儲存](hpc-cache-ingest.md)。
