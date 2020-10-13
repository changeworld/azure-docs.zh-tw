---
title: 使用 Azure HPC Cache 和 Azure NetApp Files
description: 如何使用 Azure HPC Cache 來改善使用 Azure NetApp Files 儲存的資料存取權
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 10/30/2019
ms.author: v-erkel
ms.openlocfilehash: e955ddc14bb2b0a7abc0dc815c6955247568876b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86497007"
---
# <a name="use-azure-hpc-cache-with-azure-netapp-files"></a>使用 Azure HPC Cache 搭配 Azure NetApp Files

您可以使用 [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) 作為 Azure HPC Cache 的儲存體目標。 本文說明這兩個服務如何一起運作，並提供設定這些服務的秘訣。

Azure NetApp Files 結合了其 ONTAP 作業系統與 Microsoft Azure 的擴充性和速度。 此組合可讓使用者將已建立的工作流程移至雲端，而不需要重寫程式碼

新增 Azure HPC Cache 元件可以在一個匯總的命名空間中呈現多個 Azure NetApp Files 磁片區，以改善檔案存取。 它可為位於不同服務區域的磁片區提供邊緣快取。 它也可以改善在較低層級服務層級所建立之磁片區的需求效能，以節省成本。

## <a name="overview"></a>概觀

若要搭配 Azure HPC Cache 使用 Azure NetApp Files 系統作為後端儲存體，請遵循此程式。

1. [請根據以下的規劃系統](#plan-your-azure-netapp-files-system)指導方針，建立 Azure NetApp Files 系統和磁片區。
1. 在您需要存取檔案的區域中建立 Azure HPC Cache。  (使用 [ [建立 Azure HPC Cache](hpc-cache-create.md)] 中的指示。 ) 
1. 在快取中定義指向 Azure NetApp Files 磁片[區的儲存體目標](#create-storage-targets-in-the-cache)。 針對用來存取磁片區的每個唯一 IP 位址，各建立一個快取儲存體目標。
1. 讓用戶端 [掛接 Azure HPC Cache，](#mount-storage-targets) 而不是直接裝載 Azure NetApp Files 磁片區。

## <a name="plan-your-azure-netapp-files-system"></a>規劃您的 Azure NetApp Files 系統

規劃 Azure NetApp Files 系統時，請注意本節中的專案，以確保您可以順暢地與 Azure HPC Cache 整合。

此外，請先閱讀 [Azure NetApp Files 檔](../azure-netapp-files/index.yml) ，再建立用於 Azure HPC Cache 的磁片區。

### <a name="nfs-client-access-only"></a>僅限 NFS 用戶端存取

Azure HPC Cache 目前僅支援 NFS 存取。 它無法搭配 SMB ACL 或 POSIX 模式位磁片區使用。

### <a name="exclusive-subnet-for-azure-netapp-files"></a>Azure NetApp Files 的專有子網

Azure NetApp Files 使用單一委派的子網作為其磁片區。 沒有其他資源可以使用該子網。 此外，虛擬網路中只有一個子網可用於 Azure NetApp Files。 深入瞭解 [Azure NetApp Files 網路規劃的指導方針](../azure-netapp-files/azure-netapp-files-network-topologies.md)。

### <a name="delegated-subnet-size"></a>委派的子網大小

建立 Azure NetApp Files 系統以搭配 Azure HPC Cache 使用時，請使用委派子網的大小下限。

以網路遮罩/28 指定的大小下限會提供16個 IP 位址。 在實務上，Azure NetApp Files 只會使用其中三個可用的 IP 位址來存取磁片區。 這表示您只需要在 Azure HPC Cache 中建立三個儲存體目標，以涵蓋所有磁片區。

如果委派的子網太大，Azure NetApp Files 磁片區可能會使用比單一 Azure HPC Cache 實例可以處理的更多 IP 位址。 單一快取最多可以有10個儲存體目標。

Azure NetApp Files 檔中的快速入門範例會針對委派的子網使用 10.7.0.0/16，以提供太大的子網。

### <a name="capacity-pool-service-level"></a>容量集區服務層級

選擇容量集區的服務層級時，請考慮您的工作流程。 如果您經常將資料寫回 Azure NetApp Files 磁片區，當回寫時間很慢時，可以限制快取的效能。 針對將頻繁寫入的磁片區選擇較高的服務等級。

服務層級低的磁片區也可能會在快取預先填入內容時，于工作開始時顯示一些延隔時間。 在快取啟動並執行一組良好的檔案之後，延遲應該會變成到不。

請務必事先規劃容量集區服務層級，因為它在建立之後就無法變更。 您必須在不同的容量集區中建立新的磁片區，並將資料複製到該磁片區。

請注意，您可以變更磁片區的儲存配額和容量集區的大小，而不會中斷存取。

## <a name="create-storage-targets-in-the-cache"></a>在快取中建立儲存體目標

設定好 Azure NetApp Files 系統並建立 Azure HPC Cache 之後，請在快取中定義指向檔案系統磁片區的存放裝置目標。

為您的 Azure NetApp Files 磁片區所使用的每個 IP 位址建立一個儲存體目標。 IP 位址會列在磁片區的 [掛接指示] 頁面中。

如果有多個磁片區共用相同的 IP 位址，您可以為所有磁片區使用一個儲存體目標。  

遵循 [Azure NetApp Files 檔中的掛接指示](../azure-netapp-files/azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md) ，尋找要使用的 IP 位址。

您也可以使用 Azure CLI 尋找 IP 位址：

```azurecli
az netappfiles volume list -g ${RESOURCE_GROUP} --account-name ${ANF_ACCOUNT} --pool-name ${POOL} --query "[].mountTargets[].ipAddress" | grep -Ee '[0-9]+[.][0-9]+[.][0-9]+[.][0-9]+' | tr -d '"' | tr -d , | sort | uniq
```

Azure NetApp Files 系統上的匯出名稱具有單一路徑元件。 請勿嘗試在 Azure NetApp Files 中建立根匯出的儲存體目標 ``/`` ，因為該匯出並未提供檔案存取。

這些儲存體目標的虛擬命名空間路徑沒有特殊限制。

## <a name="mount-storage-targets"></a>裝載儲存體目標

用戶端電腦應該掛接快取，而不是直接裝載 Azure NetApp Files 磁片區。 遵循 [掛接 Azure HPC Cache](hpc-cache-mount.md)中的指示。

## <a name="next-steps"></a>接下來的步驟

* 深入瞭解如何設定和使用 [Azure NetApp Files](../azure-netapp-files/index.yml)
* 如需協助規劃及設定您的 Azure HPC Cache 系統以使用 Azure NetApp Files，請 [聯絡支援人員](hpc-cache-support-ticket.md)。
