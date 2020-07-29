---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/07/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: f0a706cc51027bc503da9ee3bc637216e9e52853
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2020
ms.locfileid: "87171374"
---
## <a name="restrictions"></a>限制

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](virtual-machines-disks-encryption-at-host-restrictions.md)]

### <a name="supported-regions"></a>支援的區域

[!INCLUDE [virtual-machines-disks-encryption-at-host-regions](virtual-machines-disks-encryption-at-host-regions.md)]

### <a name="supported-vm-sizes"></a>支援的 VM 大小

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](virtual-machines-disks-encryption-at-host-suported-sizes.md)]

## <a name="prerequisites"></a>必要條件

為了能夠針對您的 Vm 或虛擬機器擴展集使用主機上的加密，您必須在訂用帳戶上啟用此功能。 將電子郵件傳送至 encryptionAtHost@microsoft。 com 與您的訂用帳戶識別碼，以便為您的訂閱啟用此功能。

使用[提供的連結](https://aka.ms/diskencryptionupdates)登入 Azure 入口網站。

> [!IMPORTANT]
> 您必須使用[提供的連結](https://aka.ms/diskencryptionupdates)來存取 Azure 入口網站。 在不使用連結的情況下，在主機上的加密目前不會顯示在公用 Azure 入口網站中。

### <a name="create-an-azure-key-vault-and-disk-encryption-set"></a>建立 Azure Key Vault 和磁片加密集

啟用此功能之後，您必須設定 Azure Key Vault 和磁片加密集（如果尚未安裝的話）。

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-portal](virtual-machines-disks-encryption-create-key-vault-portal.md)]

## <a name="deploy-a-vm"></a>部署 VM

您必須部署新的 VM，才能在主機上啟用加密，而無法在現有的 Vm 上啟用它。

1. 搜尋**虛擬機器**，然後選取 [ **+ 新增**] 以建立 VM。
1. 建立新的虛擬機器，選取適當的區域和支援的 VM 大小。
1. 視需要在 [**基本**] 分頁上填入其他值，然後繼續進行 [**磁片**] 分頁。

    :::image type="content" source="media/virtual-machines-disks-encryption-at-host-portal/disks-encryption-at-host-basic-blade.png" alt-text="已反白顯示虛擬機器建立基本功能分頁、區域和 V M 大小的螢幕擷取畫面。":::

1. 在 [**磁片**] 分頁上，針對 [**在主機加密** **] 選取 [是]** 。
1. 依您的需要進行其餘的選擇。

    :::image type="content" source="media/virtual-machines-disks-encryption-at-host-portal/disks-encryption-at-host-disk-blade.png" alt-text="[虛擬機器建立磁片] 分頁的螢幕擷取畫面，已反白顯示 [在主機上加密]。":::

1. 完成 VM 部署程式，並選擇符合您環境的選項。

您現在已部署已啟用加密的 VM，並已在主機上使用加密來加密其所有相關聯的磁片。