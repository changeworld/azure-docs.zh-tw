---
title: 如何將 Azure 磁片對應至 Linux VM 來賓磁片
description: 如何判斷基礎 Linux VM 之來賓磁片的 Azure 磁片。
author: timbasham
ms.service: virtual-machines-linux
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/17/2020
ms.author: tibasham
ms.openlocfilehash: 4f0e48bf1c14728c54d4e89f30700017b0420d7d
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96523494"
---
# <a name="how-to-map-azure-disks-to-linux-vm-guest-disks"></a>如何將 Azure 磁片對應至 Linux VM 來賓磁片

您可能需要判斷傳回 VM 之來賓磁片的 Azure 磁片。 在某些情況下，您可以比較磁片或磁片區大小與連接的 Azure 磁片大小。 如果有多個相同大小的 Azure 磁片連接到 VM，您必須使用邏輯單元編號 (資料磁片的 LUN) 。 

## <a name="what-is-a-lun"></a>什麼是 LUN？

邏輯單元編號 (LUN) 是用來識別特定存放裝置的數位。 每個儲存裝置都會被指派一個唯一的數值識別碼，從零開始。 裝置的完整路徑會以匯流排號碼、目標識別碼和邏輯單元編號表示， (LUN) 。 

例如： ***匯流排編號0、目標識別碼0、LUN 3** _

針對我們的練習，您只需要使用 LUN。

## <a name="finding-the-lun"></a>尋找 LUN

以下列出在 Linux 中尋找磁片 LUN 的兩種方法。

### <a name="lsscsi"></a>lsscsi

1. 連接至 VM
1. `sudo lsscsi`

列出的第一個資料行將包含 LUN，格式為 [Host： Channel： Target： _ * LUN * *]。

### <a name="listing-block-devices"></a>列出封鎖裝置

1. 連接至 VM
1. `sudo ls -l /sys/block/*/device`

所列的最後一個資料行會包含 LUN，格式為 [Host： Channel： Target：**LUN**]

## <a name="finding-the-lun-for-the-azure-disks"></a>尋找 Azure 磁片的 LUN

您可以使用 Azure 入口網站 Azure CLI 來找出 Azure 磁片的 LUN。

### <a name="finding-an-azure-disks-lun-in-the-azure-portal"></a>在 Azure 入口網站中尋找 Azure 磁片的 LUN

1. 在 Azure 入口網站中，選取 [虛擬機器] 以顯示您的虛擬機器清單
1. 選取虛擬機器
1. 選取 [磁片]
1. 從已連接的磁片清單中選取資料磁片。
1. 磁片的 LUN 將會顯示在 [磁片詳細資料] 窗格中。 此處顯示的 LUN 與您使用 lsscsi 在來賓中查閱的 Lun 相關聯，或列出封鎖裝置。

### <a name="finding-an-azure-disks-lun-using-azure-cli"></a>使用 Azure CLI 尋找 Azure 磁片的 LUN

```azurecli-interactive
az vm show -g myResourceGroup -n myVM --query "storageProfile.dataDisks"
```
