---
title: 使用 Azure HPC 緩存和 Azure NetApp 檔
description: 如何使用 Azure HPC 緩存來改進對 Azure NetApp 檔存儲的資料的訪問
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: 38f9d0338ce4c47024d670e6d3ee89a97faecc91
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80238686"
---
# <a name="use-azure-hpc-cache-with-azure-netapp-files"></a>將 Azure HPC 緩存與 Azure NetApp 檔一起使用

您可以將[Azure NetApp 檔](https://azure.microsoft.com/services/netapp/)用作 Azure HPC 緩存的存儲目標。 本文介紹了兩個服務如何協同工作，並給出了設置它們的技巧。

Azure NetApp 檔將其 ONTAP 作業系統與 Microsoft Azure 的可擴充性和速度相結合。 這種組合允許使用者在不重寫代碼的情況下將已建立的工作流轉移到雲中。

添加 Azure HPC 緩存元件可以通過在一個聚合命名空間中顯示多個 Azure NetApp 檔卷來提高檔訪問。 它可以為位於其他服務區域中的卷提供邊緣緩存。 它還可以按需提高在較低級別服務等級創建的卷的性能，以節省成本。

## <a name="overview"></a>總覽

要使用 Azure NetApp 檔案系統作為 Azure HPC 緩存的後端存儲，請按照此過程操作。

1. 根據[下面的"計畫系統"](#plan-your-azure-netapp-files-system)中的指南創建 Azure NetApp 檔案系統和卷。
1. 在需要檔訪問的區域創建 Azure HPC 緩存。 （使用創建 Azure [HPC 緩存](hpc-cache-create.md)中的說明。
1. 在緩存中定義指向 Azure NetApp 檔卷的[存儲目標](#create-storage-targets-in-the-cache)。 為每個用於訪問卷的唯一 IP 位址創建一個緩存存儲目標。
1. 讓用戶端[裝載 Azure HPC 緩存](#mount-storage-targets)，而不是直接安裝 Azure NetApp 檔卷。

## <a name="plan-your-azure-netapp-files-system"></a>規劃 Azure NetApp 檔案系統

規劃 Azure NetApp 檔案系統時，請注意本節中的專案，以確保可以將其與 Azure HPC 緩存順利集成。

在創建卷以與 Azure HPC 緩存一起使用之前，還要閱讀[Azure NetApp 檔文檔](../azure-netapp-files/index.yml)。

### <a name="nfs-client-access-only"></a>僅 NFS 用戶端訪問

Azure HPC 緩存當前僅支援 NFS 訪問。 不能將其與 SMB ACL 或 POSIX 模式位卷一起使用。

### <a name="exclusive-subnet-for-azure-netapp-files"></a>Azure NetApp 檔的獨佔子網

Azure NetApp 檔對其卷使用單個委派子網。 任何其他資源都無法使用該子網。 此外，虛擬網路中只有一個子網可用於 Azure NetApp 檔。 有關詳細資訊，在[Azure NetApp 檔網路規劃指南](../azure-netapp-files/azure-netapp-files-network-topologies.md)中。

### <a name="delegated-subnet-size"></a>委派子網大小

創建要與 Azure HPC 緩存一起使用的 Azure NetApp 檔案系統時，請使用委派子網的最小大小。

使用網遮罩 /28 指定的最小大小提供 16 個 IP 位址。 實際上，Azure NetApp 檔僅使用其中三個可用的 IP 位址進行卷訪問。 這意味著您只需在 Azure HPC 緩存中創建三個存儲目標，即覆蓋所有卷。

如果委派的子網太大，Azure NetApp 檔卷使用的 IP 位址可能比單個 Azure HPC 緩存實例可以處理的 IP 位址多。 單個緩存最多隻能有 10 個存儲目標。

Azure NetApp 檔文檔中的快速入門示例對委派的子網使用 10.7.0.0/16，該子網提供過大的子網。

### <a name="capacity-pool-service-level"></a>容量池服務等級

為容量池選擇服務等級時，請考慮工作流。 如果經常將資料寫回 Azure NetApp 檔卷，則如果回寫時間較慢，緩存的性能可能會受到限制。 為頻繁寫入的卷選擇高服務等級。

服務等級較低的卷在緩存預填充內容時，在任務開始時也可能顯示一些延遲。 在緩存啟動並運行完一組良好的工作檔後，延遲應變得不明顯。

提前規劃容量池服務等級非常重要，因為創建後無法更改該服務等級。 需要在不同的容量池中創建新的卷，並複製資料。

請注意，您可以更改卷的存儲配額和容量池的大小，而不會中斷訪問。

## <a name="create-storage-targets-in-the-cache"></a>在緩存中創建存儲目標

設置 Azure NetApp 檔案系統並創建 Azure HPC 緩存後，在指向檔案系統卷的緩存中定義存儲目標。

為 Azure NetApp 檔卷使用的每個 IP 位址創建一個存儲目標。 IP 位址列在卷的裝載說明頁中。

如果多個卷共用同一個 IP 位址，則可以對所有卷使用一個存儲目標。  

按照[Azure NetApp 檔文檔中的裝載說明](../azure-netapp-files/azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)查找要使用的 IP 位址。

您還可以使用 Azure CLI 查找 IP 位址：

```azurecli
az netappfiles volume list -g ${RESOURCE_GROUP} --account-name ${ANF_ACCOUNT} --pool-name ${POOL} --query "[].mountTargets[].ipAddress" | grep -Ee '[0-9]+[.][0-9]+[.][0-9]+[.][0-9]+' | tr -d '"' | tr -d , | sort | uniq
```

Azure NetApp 檔案系統上的匯出名稱具有單個路徑元件。 不要嘗試在 Azure NetApp 檔中為根``/``匯出創建存儲目標，因為該匯出不提供檔存取權限。

對於這些存儲目標的虛擬命名空間路徑，沒有特殊限制。

## <a name="mount-storage-targets"></a>裝載存儲目標

用戶端電腦應裝載緩存，而不是直接裝載 Azure NetApp 檔卷。 按照裝載 Azure [HPC 緩存中的](hpc-cache-mount.md)說明進行操作。

## <a name="next-steps"></a>後續步驟

* 閱讀有關設置和使用 Azure [NetApp 檔的更多內容](../azure-netapp-files/index.yml)
* 有關規劃和設置 Azure HPC 緩存系統以使用 Azure NetApp 檔的説明，[請與支援 人員聯繫](hpc-cache-support-ticket.md)。
