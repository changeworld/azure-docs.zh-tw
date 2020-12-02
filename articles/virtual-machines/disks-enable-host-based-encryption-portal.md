---
title: 在主機 Azure 入口網站管理的磁片上使用加密來啟用端對端加密
description: 在主機上使用加密來啟用 Azure 受控磁片上的端對端加密-Azure 入口網站。
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/24/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: fd74872bc1c46f3dddda1b6d15f14f26dc2187cb
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499368"
---
# <a name="use-the-azure-portal-to-enable-end-to-end-encryption-using-encryption-at-host"></a>使用 Azure 入口網站來啟用在主機使用加密的端對端加密

當您在主機上啟用加密時，儲存在 VM 主機上的資料會在待用時加密，並將流量加密至儲存體服務。 如需有關在主機上加密以及其他受控磁片加密類型的概念資訊，請參閱：

* Linux： [VM 資料的主機端對端加密](./disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)加密。

* Windows： [您 VM 資料在主機端對端加密的加密](./disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)。

## <a name="restrictions"></a>限制

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]

### <a name="supported-regions"></a>支援區域

[!INCLUDE [virtual-machines-disks-encryption-at-host-regions](../../includes/virtual-machines-disks-encryption-at-host-regions.md)]

### <a name="supported-vm-sizes"></a>支援的 VM 大小

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

## <a name="prerequisites"></a>先決條件

若要能夠針對您的 Vm 或虛擬機器擴展集使用主機加密，您必須在訂用帳戶上啟用此功能。 將電子郵件傳送至 encryptionAtHost@microsoft。 具有您訂用帳戶識別碼的 com，可讓您的訂用帳戶啟用此功能。

使用 [提供的連結](https://aka.ms/diskencryptionupdates)登入 Azure 入口網站。

> [!IMPORTANT]
> 您必須使用 [提供的連結](https://aka.ms/diskencryptionupdates) 來存取 Azure 入口網站。 在不使用連結的情況下，公用 Azure 入口網站目前不會顯示在主機上的加密。

### <a name="create-an-azure-key-vault-and-disk-encryption-set"></a>建立 Azure Key Vault 和磁片加密集

啟用此功能之後，您必須設定 Azure Key Vault 和磁片加密集（如果尚未安裝）。

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-portal](../../includes/virtual-machines-disks-encryption-create-key-vault-portal.md)]

## <a name="deploy-a-vm"></a>部署 VM

您必須部署新的 VM，才能在主機上啟用加密，但無法在現有的 Vm 上啟用。

1. 搜尋 **虛擬機器** ，然後選取 [ **+ 新增** ] 以建立 VM。
1. 建立新的虛擬機器，選取適當的區域和支援的 VM 大小。
1. 視需要在 [ **基本** ] 分頁上填入其他值，然後繼續進行 [ **磁片** ] 分頁。

    :::image type="content" source="media/virtual-machines-disks-encryption-at-host-portal/disks-encryption-at-host-basic-blade.png" alt-text="已反白顯示虛擬機器建立基本功能分頁、區域和 V M 大小的螢幕擷取畫面。":::

1. 在 [ **磁片** ] 分頁上，選取 **[是]** ， **在主機** 上進行加密。
1. 依您的需要進行其餘的選取專案。

    :::image type="content" source="media/virtual-machines-disks-encryption-at-host-portal/disks-encryption-at-host-disk-blade.png" alt-text="虛擬機器建立磁片分頁的螢幕擷取畫面，已反白顯示 [在主機加密]。":::

1. 完成 VM 部署程式，並選取符合您環境的選項。

您現在已在啟用主機加密的情況下部署 VM，其所有相關聯的磁片將會使用「在主機加密」進行加密。

## <a name="next-steps"></a>後續步驟

[Azure Resource Manager 範本範例](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/EncryptionAtHost)