---
title: 掛接 Azure HPC Cache
description: 如何將用戶端連線至 Azure HPC Cache 服務
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/30/2020
ms.author: v-erkel
ms.openlocfilehash: e29d031bc3461c4adab87b1d784ef19c89c7c12d
ms.sourcegitcommit: 06ba80dae4f4be9fdf86eb02b7bc71927d5671d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/01/2020
ms.locfileid: "91612996"
---
# <a name="mount-the-azure-hpc-cache"></a>掛接 Azure HPC Cache

建立快取之後，NFS 用戶端就可以使用簡單的命令來存取它 `mount` 。 此命令會將 Azure HPC Cache 上的特定儲存目標路徑連接至用戶端電腦上的本機目錄。

掛接命令是由下列元素所組成：

* 快取總覽頁面上列出的其中一個快取裝載位址 () 
* 您為儲存體目標設定的虛擬命名空間路徑 (列在 [快取命名空間] 頁面上) 
* 要在用戶端上使用的本機路徑
* 可優化這類 NFS 掛接成功的命令參數

快取的 [ **掛接指示** ] 頁面會為您收集資訊和建議的選項，並建立可供您複製的原型掛接命令。 如需詳細資訊， [請參閱使用掛接指示公用程式](#use-the-mount-instructions-utility) 。

## <a name="prepare-clients"></a>準備用戶端

遵循本節中的指導方針，確定您的用戶端能夠掛接 Azure HPC Cache。

### <a name="provide-network-access"></a>提供網路存取

用戶端電腦必須具有快取之虛擬網路和私人子網的網路存取權。

例如，在相同的虛擬網路內建立用戶端 Vm，或使用虛擬網路中的端點、閘道或其他解決方案來從外部存取。  (記住，快取本身以外的任何專案都應該裝載在快取的子網內。 ) 

### <a name="install-utilities"></a>安裝公用程式

安裝適當的 Linux 公用程式軟體以支援 NFS 掛接命令：

* 針對 Red Hat Enterprise Linux 或 SuSE： `sudo yum install -y nfs-utils`
* 針對 Ubuntu 或 Debian： `sudo apt-get install nfs-common`

### <a name="create-a-local-path"></a>建立本機路徑

在每個用戶端上建立本機目錄路徑，以連接到快取。 針對您想要掛接的每個命名空間路徑建立路徑。

範例：`sudo mkdir -p /mnt/hpc-cache-1/target3`

Azure 入口網站中的 [ [掛接指示](#use-the-mount-instructions-utility) ] 頁面包含可供您複製的原型命令。

當您將用戶端電腦連線到快取時，您會將此路徑與代表儲存體目標匯出的虛擬命名空間路徑產生關聯。 針對用戶端將使用的每個虛擬命名空間路徑建立目錄。

## <a name="use-the-mount-instructions-utility"></a>使用掛接指示公用程式

您可以使用 Azure 入口網站中的 [ **掛接指示** ] 頁面來建立可複製掛接命令。 從入口網站的 [快取] 視圖的 [ **設定** ] 區段中開啟頁面。

在用戶端上使用命令之前，請確定用戶端符合必要條件，並擁有使用 NFS 命令所需的軟體（ `mount` 如上述的 [準備用戶端](#prepare-clients)所述）。

![入口網站中 Azure HPC Cache 實例的螢幕擷取畫面，其中已載入設定 > 掛接指示頁面](media/mount-instructions.png)

遵循此程式以建立掛接命令。

1. 自訂 [ **用戶端路徑** ] 欄位。 此欄位提供範例命令，可讓您用來建立用戶端上的本機路徑。 用戶端會在此目錄中本機存取 Azure HPC Cache 的內容。

   按一下欄位，然後編輯命令以包含您想要的目錄名稱。 名稱會出現在字串的結尾。 `sudo mkdir -p`

   ![[用戶端路徑] 欄位的螢幕擷取畫面，其中游標位於結尾](media/mount-edit-client.png)

   完成欄位編輯之後，頁面底部的掛接命令會以新的用戶端路徑進行更新。

1. 從清單中選擇快取 **裝入位址** 。 此功能表會列出所有快取的 [用戶端掛接點](#find-mount-command-components)。

   在所有可用的裝載位址之間平衡用戶端負載，以獲得更佳的快取效能。

   ![[快取裝入位址] 欄位的螢幕擷取畫面，其中顯示顯示三個 IP 位址可供選擇的選擇器](media/mount-select-ip.png)

1. 選擇要用於用戶端的 **虛擬命名空間路徑** 。 這些路徑會連結至後端儲存體系統上的匯出。

   ![[命名空間路徑] 欄位的螢幕擷取畫面，其中開啟選取器](media/mount-select-target.png)

   您可以在 **命名空間** 入口網站頁面上，查看及變更虛擬命名空間路徑。 請參閱 [設定匯總的命名空間](add-namespace-paths.md) ，以瞭解做法。

   若要深入瞭解 Azure HPC Cache 的匯總命名空間功能，請參閱 [計畫匯總的命名空間](hpc-cache-namespace.md)。

1. 步驟3中的 [ **掛接命令** ] 欄位會自動填入自訂掛接命令，該命令會使用您在先前欄位中設定的掛接位址、虛擬命名空間路徑和用戶端路徑。

   按一下欄位右邊的複製符號，自動將它複製到剪貼簿。

   ![[原型掛接命令] 欄位的螢幕擷取畫面，其中顯示 [複製到剪貼簿] 按鈕的滑鼠停留文字](media/mount-command-copy.png)

1. 在用戶端電腦上使用複製的 mount 命令，將它連接到 Azure HPC Cache。 您可以直接從用戶端命令列發出命令，或在用戶端安裝腳本或範本中包含 mount 命令。

## <a name="understand-mount-command-syntax"></a>瞭解掛接命令語法

掛接命令的形式如下：

> sudo 掛接 {*options*} *cache_mount_address*：/*namespace_path* *local_path*

範例：

```bash
root@test-client:/tmp# mkdir hpccache
root@test-client:/tmp# sudo mount -o hard,proto=tcp,mountproto=tcp,retry=30 10.0.0.28:/blob-demo-0722 hpccache
root@test-client:/tmp#
```

此命令成功之後，儲存體匯出的內容會顯示在 ``hpccache`` 用戶端上的目錄中。

### <a name="mount-command-options"></a>掛接命令選項

針對穩固的用戶端掛接，請在掛接命令中傳遞這些設定和引數：

> 掛接-o hard、proto = tcp、mountproto = tcp、retry = 30 $ {CACHE_IP_ADDRESS}:/$ {NAMESPACE_PATH} $ {LOCAL_FILESYSTEM_MOUNT_POINT}

| 建議的掛接命令設定 | 說明 |
--- | ---
``hard`` | 軟掛接至 Azure HPC Cache 會與應用程式失敗和可能的資料遺失相關聯。
``proto=tcp`` | 此選項支援適當處理 NFS 網路錯誤的功能。
``mountproto=tcp`` | 此選項支援在掛接作業中適當處理網路錯誤的功能。
``retry=<value>`` | 設定 ``retry=30`` 可避免暫時性的掛接失敗。 (執行前景掛接時建議使用不同的值)。

### <a name="find-mount-command-components"></a>尋找掛接命令元件

如果您想要建立裝載命令，而不使用 [ **掛接指示** ] 頁面，您可以在 [快取 **總覽** ] 頁面和 [ **命名空間** ] 頁面上的 [虛擬命名空間路徑] 中找到裝入位址。

![Azure HPC Cache 實例的 [總覽] 頁面的螢幕擷取畫面，並在右下角的 [掛接位址] 清單周圍顯示反白顯示方塊](media/hpc-cache-mount-addresses.png)

> [!NOTE]
> 快取裝載位址會對應至快取子網內的網路介面。 在資源群組中，系統會列出這些 Nic，且名稱結尾為 `-cluster-nic-` 和數位。 請勿變更或刪除這些介面，否則快取將會變成無法使用。

虛擬命名空間路徑會顯示在快取的 **命名空間** 設定頁面上。

![入口網站設定 > 命名空間頁面的螢幕擷取畫面，其中包含資料表第一個資料行的醒目提示方塊：「命名空間路徑」](media/view-namespace-paths.png)

## <a name="next-steps"></a>後續步驟

* 若要將資料移至快取的儲存體目標，請參閱 [填入新的 Azure Blob 儲存體](hpc-cache-ingest.md)。
