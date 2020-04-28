---
title: 使用 Azure HPC Cache 和 Azure NetApp Files
description: 如何使用 Azure HPC 快取來改善以 Azure NetApp Files 儲存之資料的存取權
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: 6d4dd69b30acb26d02218fe05a60ace9aa855ddc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82194953"
---
# <a name="use-azure-hpc-cache-with-azure-netapp-files"></a>搭配 Azure NetApp Files 使用 Azure HPC Cache

您可以使用[Azure NetApp Files](https://azure.microsoft.com/services/netapp/)作為 azure HPC 快取的儲存體目標。 本文說明這兩項服務如何搭配運作，並提供設定它們的秘訣。

Azure NetApp Files 結合了其 ONTAP 作業系統與 Microsoft Azure 的擴充性和速度。 此組合可讓使用者將已建立的工作流程轉移至雲端，而不需要重寫程式碼

新增 Azure HPC 快取元件可以在一個匯總的命名空間中呈現多個 Azure NetApp Files 磁片區，以改善檔案存取。 它可以為位於不同服務區域中的磁片區提供邊緣快取。 它也可以改善在較低層服務層級所建立之磁片區的需求效能，以節省成本。

## <a name="overview"></a>概觀

若要使用 Azure NetApp Files 系統作為 Azure HPC Cache 的後端儲存體，請遵循此程式。

1. 依照[下列規劃系統](#plan-your-azure-netapp-files-system)中的指導方針，建立 Azure NetApp Files 系統和磁片區。
1. 在您需要檔案存取的區域中建立 Azure HPC 快取。 （使用[建立 AZURE HPC](hpc-cache-create.md)快取中的指示）。
1. 在快取中定義指向 Azure NetApp Files 磁片[區的儲存體目標](#create-storage-targets-in-the-cache)。 針對用來存取磁片區的每個唯一 IP 位址建立一個快取儲存體目標。
1. 讓用戶端[掛接 AZURE HPC](#mount-storage-targets)快取，而不是直接裝載 Azure NetApp Files 磁片區。

## <a name="plan-your-azure-netapp-files-system"></a>規劃您的 Azure NetApp Files 系統

規劃您的 Azure NetApp Files 系統時，請注意本節中的專案，以確保您可以使用 Azure HPC 快取順暢地整合。

在建立磁片區以搭配使用 Azure HPC 快取之前，也請閱讀[Azure NetApp Files 檔](../azure-netapp-files/index.yml)。

### <a name="nfs-client-access-only"></a>僅限 NFS 用戶端存取

Azure HPC 快取目前僅支援 NFS 存取。 它不能與 SMB ACL 或 POSIX 模式位磁片區搭配使用。

### <a name="exclusive-subnet-for-azure-netapp-files"></a>適用于 Azure NetApp Files 的專有子網

Azure NetApp Files 會針對其磁片區使用單一委派的子網。 沒有其他資源可以使用該子網。 此外，虛擬網路中只有一個子網可用於 Azure NetApp Files。 深入瞭解[Azure NetApp Files 網路規劃的指導方針](../azure-netapp-files/azure-netapp-files-network-topologies.md)。

### <a name="delegated-subnet-size"></a>委派的子網大小

建立 Azure NetApp Files 系統以與 Azure HPC 快取搭配使用時，請使用委派子網的大小下限。

使用網路遮罩/28 指定的大小下限會提供16個 IP 位址。 實際上，Azure NetApp Files 只會使用其中三個可用的 IP 位址來存取磁片區。 這表示您只需要在 Azure HPC 快取中建立三個儲存體目標，即可涵蓋所有的磁片區。

如果委派的子網太大，Azure NetApp Files 磁片區可能會使用比單一 Azure HPC 快取實例所能處理的更多 IP 位址。 單一快取最多可以有10個儲存體目標。

Azure NetApp Files 檔中的快速入門範例會針對委派的子網使用 10.7.0.0/16，這會提供太大的子網。

### <a name="capacity-pool-service-level"></a>容量集區服務層級

選擇容量集區的服務等級時，請考慮您的工作流程。 如果您經常將資料寫回 Azure NetApp Files 磁片區，當回寫時間變慢時，可以限制快取的效能。 針對將經常寫入的磁片區選擇高服務等級。

服務層級較低的磁片區也可能會在工作開始時顯示一些延遲，而快取會預先填入內容。 在快取啟動並執行一組良好的檔案之後，延遲應該就會變成到不。

請務必提早規劃容量集區服務層級，因為它在建立之後就無法變更。 必須在不同的容量集區中建立新的磁片區，並複製該資料。

請注意，您可以變更磁片區的儲存體配額和容量集區的大小，而不會中斷存取。

## <a name="create-storage-targets-in-the-cache"></a>在快取中建立儲存體目標

設定 Azure NetApp Files 系統並建立 Azure HPC 快取之後，請在快取中定義指向檔案系統磁片區的儲存體目標。

針對 Azure NetApp Files 磁片區所使用的每個 IP 位址建立一個儲存體目標。 此 IP 位址會列在磁片區的 [掛接指示] 頁面中。

如果有多個磁片區共用相同的 IP 位址，您可以將一個儲存體目標用於所有的。  

遵循[Azure NetApp Files 檔中的掛接指示](../azure-netapp-files/azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)，尋找要使用的 IP 位址。

您也可以使用 Azure CLI 來尋找 IP 位址：

```azurecli
az netappfiles volume list -g ${RESOURCE_GROUP} --account-name ${ANF_ACCOUNT} --pool-name ${POOL} --query "[].mountTargets[].ipAddress" | grep -Ee '[0-9]+[.][0-9]+[.][0-9]+[.][0-9]+' | tr -d '"' | tr -d , | sort | uniq
```

Azure NetApp Files 系統上的匯出名稱具有單一路徑元件。 請勿嘗試針對 Azure NetApp Files 中的根匯出``/``建立儲存體目標，因為該匯出不提供檔案存取。

這些儲存體目標的虛擬命名空間路徑沒有特殊限制。

## <a name="mount-storage-targets"></a>掛接儲存體目標

用戶端電腦應該掛接快取，而不是直接裝載 Azure NetApp Files 磁片區。 請遵循[掛接 AZURE HPC](hpc-cache-mount.md)快取中的指示。

## <a name="next-steps"></a>後續步驟

* 深入瞭解如何設定及使用[Azure NetApp Files](../azure-netapp-files/index.yml)
* 如需規劃和設定 Azure HPC 快取系統以使用 Azure NetApp Files 的協助，請[聯絡支援](hpc-cache-support-ticket.md)人員。
