---
title: 裝載 Azure HPC 快取
description: 如何將用戶端連線至 Azure HPC 快取服務
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 04/15/2020
ms.author: v-erkel
ms.openlocfilehash: fb3e4fd4935afc4869e50ccbc35c53333d43b1df
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85515518"
---
# <a name="mount-the-azure-hpc-cache"></a>掛接 Azure HPC Cache

建立快取之後，NFS 用戶端就可以使用簡單的命令來存取它 `mount` 。 命令會將 Azure HPC 快取上的特定儲存體目標路徑連接到用戶端電腦上的本機目錄。

Mount 命令是由下列元素所組成：

* 其中一個快取的掛接位址（列于快取總覽頁面）
* 您在建立儲存體目標時所設定的虛擬命名空間路徑
* 要在用戶端上使用的本機路徑
* 可將這類 NFS 掛接的成功優化的命令參數

您快取的 [**掛接指示**] 頁面會收集資訊和建議的選項，並建立可供您複製的原型掛接命令。 如需詳細資訊，[請參閱使用掛接指示公用程式](#use-the-mount-instructions-utility)。

## <a name="prepare-clients"></a>準備用戶端

請確定您的用戶端能夠遵循本節中的指導方針來掛接 Azure HPC 快取。

### <a name="provide-network-access"></a>提供網路存取

用戶端電腦必須具有快取的虛擬網路和私人子網的網路存取權。

例如，在相同的虛擬網路內建立用戶端 Vm，或使用端點、閘道或虛擬網路中的其他解決方案，從外部進行存取。 （請記住，快取本身以外的任何內容都應該裝載于快取的子網內）。

### <a name="install-utilities"></a>安裝公用程式

安裝適當的 Linux 公用程式軟體以支援 NFS 掛接命令：

* 針對 Red Hat Enterprise Linux 或 SuSE：`sudo yum install -y nfs-utils`
* 針對 Ubuntu 或 Debian：`sudo apt-get install nfs-common`

### <a name="create-a-local-path"></a>建立本機路徑

在每個用戶端上建立本機目錄路徑，以連接到快取。 為您要裝載的每個命名空間路徑建立路徑。

範例：`sudo mkdir -p /mnt/hpc-cache-1/target3`

Azure 入口網站中的 [[掛接指示](#use-the-mount-instructions-utility)] 頁面包含可供您複製的原型命令。

當您將用戶端電腦連線到快取時，您會將此路徑與代表儲存體目標匯出的虛擬命名空間路徑產生關聯。 為用戶端將使用的每個虛擬命名空間路徑建立目錄。

## <a name="use-the-mount-instructions-utility"></a>使用掛接指示公用程式

您可以使用 Azure 入口網站中的 [**掛接指示**] 頁面來建立可複製 Mount 命令。 在入口網站中，從快取視圖的 [**設定**] 區段開啟頁面。

在用戶端上使用命令之前，請確定用戶端符合必要條件，並具有 `mount` 如先前[準備用戶端](#prepare-clients)中所述的使用 NFS 命令所需的軟體。

![入口網站中的 Azure HPC 快取實例的螢幕擷取畫面，其中已載入 [設定 > 掛接指示] 頁面](media/mount-instructions.png)

請遵循此程式來建立掛接命令。

1. 自訂 [**用戶端路徑**] 欄位。 此欄位提供一個範例命令，您可以用來在用戶端上建立本機路徑。 用戶端會從本機的 Azure HPC 快取存取此目錄中的內容。

   按一下欄位並編輯命令，以包含您想要的目錄名稱。 名稱會出現在字串結尾的後面`sudo mkdir -p`

   ![[用戶端路徑] 欄位的螢幕擷取畫面，其中游標位於結尾處](media/mount-edit-client.png)

   完成欄位的編輯後，頁面底部的 mount 命令會以新的用戶端路徑更新。

1. 從清單中選擇快取**裝載位址**。 此功能表會列出所有快取的[用戶端掛接點](#find-mount-command-components)。

   平衡所有可用掛接位址的用戶端負載，以獲得更好的快取效能。

   ![[快取裝載位址] 欄位的螢幕擷取畫面，其中顯示可供選擇的三個 IP 位址的選擇器](media/mount-select-ip.png)

1. 選擇要用於用戶端的**虛擬命名空間路徑**。 這些路徑連結至後端儲存體系統上的匯出。

   ![[命名空間路徑] 欄位的螢幕擷取畫面，並開啟選取器](media/mount-select-target.png)

   您可以在儲存體目標入口網站頁面上，查看和變更虛擬命名空間路徑。 如要瞭解，請閱讀[新增儲存體目標](hpc-cache-add-storage.md)。

   若要深入瞭解 Azure HPC Cache 的匯總命名空間功能，請參閱[規劃匯總的命名空間](hpc-cache-namespace.md)。

1. 步驟3中的 [**掛接命令**] 欄位會使用自訂的掛接命令，自動填入您在先前欄位中設定的掛接位址、虛擬命名空間路徑和用戶端路徑。

   按一下欄位右側的 [複製] 符號，自動將它複製到剪貼簿。

   ![[命名空間路徑] 欄位的螢幕擷取畫面，並開啟選取器](media/mount-command-copy.png)

1. 在用戶端電腦上使用複製的掛接命令，將它連線到 Azure HPC 快取。 您可以直接從用戶端命令列發出命令，或在用戶端安裝腳本或範本中包含 mount 命令。

## <a name="understand-mount-command-syntax"></a>瞭解掛接命令語法

Mount 命令的格式如下：

> sudo mount {*options*} *cache_mount_address*：/*namespace_path* *local_path*

範例：

```bash
root@test-client:/tmp# mkdir hpccache
root@test-client:/tmp# sudo mount -o hard,proto=tcp,mountproto=tcp,retry=30 10.0.0.28:/blob-demo-0722 hpccache
root@test-client:/tmp#
```

此命令成功之後，就會在用戶端的目錄中看到儲存體匯出的內容 ``hpccache`` 。

### <a name="mount-command-options"></a>掛接命令選項

若是健全的用戶端掛接，請在您的 mount 命令中傳遞這些設定和引數：

> 掛接-o hard，proto = tcp，mountproto = tcp，重試 = 30 $ {CACHE_IP_ADDRESS}:/$ {NAMESPACE_PATH} $ {LOCAL_FILESYSTEM_MOUNT_POINT}

| 建議的掛接命令設定 | |
--- | ---
``hard`` | 軟掛接至 Azure HPC 快取會與應用程式失敗和可能的資料遺失相關聯。
``proto=tcp`` | 此選項支援適當處理 NFS 網路錯誤的功能。
``mountproto=tcp`` | 此選項支援在掛接作業中適當處理網路錯誤的功能。
``retry=<value>`` | 設定 ``retry=30`` 可避免暫時性的掛接失敗。 (執行前景掛接時建議使用不同的值)。

### <a name="find-mount-command-components"></a>尋找掛接命令元件

如果您想要建立掛接命令，而不使用 [**掛接指示**] 頁面，您可以在 [快取**總覽**] 頁面上找到掛接位址，並在 [**儲存體目標**] 頁面上尋找虛擬命名空間路徑。

![Azure HPC 快取實例 [總覽] 頁面的螢幕擷取畫面，並在右下方的 [掛接位址] 清單周圍加上醒目提示方塊](media/hpc-cache-mount-addresses.png)

> [!NOTE]
> 快取掛接位址會對應至快取子網內的網路介面。 在資源群組中，這些 Nic 會以結尾為的名稱 `-cluster-nic-` 和數位來列出。 請勿更改或刪除這些介面，否則快取將會變成無法使用。

虛擬命名空間路徑會顯示在每個儲存體目標的詳細資料頁面上。 按一下個別的儲存體目標名稱以查看其詳細資料，包括與其相關聯的匯總命名空間路徑。

![儲存體目標詳細資料頁面（標頭「更新儲存體目標」）的螢幕擷取畫面。 資料表之 [虛擬命名空間路徑] 資料行中的專案周圍有一個醒目提示方塊](media/hpc-cache-view-namespace-paths.png)

## <a name="next-steps"></a>後續步驟

* 若要將資料移至快取的儲存體目標，請閱讀[填入新的 Azure Blob 儲存體](hpc-cache-ingest.md)。
