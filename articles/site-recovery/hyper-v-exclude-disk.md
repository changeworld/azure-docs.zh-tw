---
title: 使用 Azure Site Recovery 排除 Hyper-v VM 磁片免于災難復原至 Azure 的嚴重損壞修復
description: 如何使用 Azure Site Recovery 將 Hyper-v VM 磁片從複寫中排除到 Azure。
author: mayurigupta13
manager: rochakm
ms.topic: conceptual
ms.author: mayg
ms.date: 11/12/2019
ms.openlocfilehash: 373cffe96119af4a2fc0d74e2090e6cc24dcaf8f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86131257"
---
# <a name="exclude-disks-from-replication"></a>從複寫排除磁碟

本文說明如何在將 Hyper-v Vm 複寫至 Azure 時排除磁片。 您可能會想要將磁片從複寫中排除，原因有很多：

- 確定排除的磁片上不重要的資料流程失不會被覆寫。
- 藉由排除不需要複寫的磁片，將已耗用的複寫頻寬或目標端資源優化。
- 藉由不復寫您不需要的資料來儲存儲存體和網路資源。

將磁片從複寫中排除之前：

- [深入了解](exclude-disks-replication.md)排除磁碟。
- 請參閱 [典型的排除案例](exclude-disks-replication.md#typical-scenarios) 和 [範例](exclude-disks-replication.md#example-1-exclude-the-sql-server-tempdb-disk) ，其中會顯示磁片不包括如何影響複寫、容錯移轉和容錯回復。

## <a name="before-you-start"></a>開始之前

開始之前，請注意下列事項：

- 複寫 **：依**預設會複寫機器上的所有磁片。
- **磁片類型**：
    - 您可從複寫排除基本磁碟。
    - 您無法排除作業系統磁碟。
    - 我們建議不要排除動態磁碟。 Site Recovery 無法識別來賓 VM 中的「基本」或「動態」 VHD。  如果您未排除所有的相依動態磁碟區磁片，受保護的動態磁碟會在容錯移轉的 VM 上變成失敗的磁片，且該磁片上的資料無法存取。
- **新增/移除/排除磁片**：啟用複寫後，您無法新增/移除/排除磁片以進行複寫。 如果您想要新增/移除或排除磁片，您必須停用 VM 的保護，然後再次啟用。
- **故障**轉移：容錯移轉之後，如果已容錯移轉的應用程式需要排除磁片才能運作，您需要手動建立這些磁片。 或者，您可以將 Azure 自動化整合至復原方案，以在機器容錯移轉期間建立磁片。
- 容錯**回復**：當您在容錯移轉之後容錯回復至內部部署網站時，您在 Azure 中手動建立的磁片不會容錯回復。 例如，如果您容錯移轉三個磁片，並直接在 Azure VM 上建立兩個磁片，則只有三個容錯移轉的磁片會容錯回復。 您無法包含以手動方式建立在容錯回復中或反向複寫 Vm 的磁片。

## <a name="exclude-disks"></a>排除磁碟

1. 當您[啟用](./hyper-v-azure-tutorial.md)hyper-v VM 的複寫時，若要排除磁片，請在選取您想要複寫的 vm 之後，在 [**啟用**複寫內容設定內容]  >  **Properties**  >  **Configure properties**頁面中，檢查 [要複寫的**磁片**] 資料行。 預設會選取所有磁片進行複寫。
2. 如果您不想要複寫特定磁片，請在 [要複寫的 **磁片** ] 中清除您要排除之任何磁片的選取專案。 

    ![從複寫排除磁碟](./media/hyper-v-exclude-disk/enable-replication6-with-exclude-disk.png)


## <a name="next-steps"></a>接下來的步驟
在您的部署設定完成並開始執行之後，請 [深入了解](failover-failback-overview.md) 不同類型的容錯移轉。
