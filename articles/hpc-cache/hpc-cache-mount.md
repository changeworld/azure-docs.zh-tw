---
title: 裝載 Azure HPC 緩存
description: 如何將用戶端連接到 Azure HPC 快取服務
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: d906ed9a1a55e936c6374806a9037085c47e3b01
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73582219"
---
# <a name="mount-the-azure-hpc-cache"></a>掛接 Azure HPC Cache

創建緩存後，NFS 用戶端可以使用簡單的裝載命令訪問它。

裝載命令由兩個元素組成：

* 緩存的裝載位址之一（列在緩存概述頁上）
* 創建存儲目標時設置的虛擬命名空間路徑

![Azure HPC 緩存實例的"概述"頁面的螢幕截圖，右下角的裝載地址清單周圍有一個突出顯示框](media/hpc-cache-mount-addresses.png)

> [!NOTE] 
> 緩存裝載位址對應于緩存子網中的網路介面。 在資源組中，這些 NIC 列出的名稱以 結尾`-cluster-nic-`，以數位結尾。 不要更改或刪除這些介面，否則緩存將不可用。

虛擬命名空間路徑顯示在 **"存儲目標"** 頁中。 按一下單個存儲目標名稱以查看其詳細資訊，包括與其關聯的聚合命名空間路徑。

![緩存存儲目標面板的螢幕截圖，在表的 Path 列中條目周圍有一個突出顯示框](media/hpc-cache-view-namespace-paths.png)

## <a name="mount-command-syntax"></a>裝載命令語法

使用安裝命令，如下所示：

> 蘇多安裝*cache_mount_address*：/*namespace_pathlocal_path* *local_path* [*選項*]

範例：

```
root@test-client:/tmp# mkdir hpccache
root@test-client:/tmp# sudo mount 10.0.0.28:/blob-demo-0722 ./hpccache/ -orw,tcp,mountproto=tcp,vers3,hard
root@test-client:/tmp# 
```

此命令成功後，存儲匯出的內容應在用戶端的``hpccache``目錄中可見。

> [!NOTE] 
> 用戶端必須能夠訪問存放緩存的虛擬網路和子網。 例如，在同一虛擬網路中創建用戶端 VM，或使用虛擬網路中的終結點、閘道或其他解決方案從外部訪問。 請記住，在緩存的子網內無法託管任何其他內容。

### <a name="mount-command-options"></a>裝載命令選項

對於可靠的用戶端裝載，在裝載命令中傳遞這些設置和參數： 

``mount -o hard,proto=tcp,mountproto=tcp,retry=30 ${CACHE_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}``

| 建議的裝載命令設置 | |
--- | --- 
``hard`` | Azure HPC 緩存的軟裝載與應用程式故障和可能的資料丟失相關聯。 
``proto=netid`` | 此選項支援適當處理 NFS 網路錯誤的功能。
``mountproto=netid`` | 此選項支援在掛接作業中適當處理網路錯誤的功能。
``retry=n`` | 設定 ``retry=30`` 可避免暫時性的掛接失敗。 (執行前景掛接時建議使用不同的值)。

## <a name="next-steps"></a>後續步驟

* 要將資料移動到緩存的存儲目標，請閱讀[填充新的 Azure Blob 存儲](hpc-cache-ingest.md)。
