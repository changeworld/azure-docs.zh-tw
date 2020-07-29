---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/08/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 8313259d5375bb19e7caa08a01a658707453d133
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2020
ms.locfileid: "87177005"
---
## <a name="supported-regions"></a>支援的區域

[!INCLUDE [virtual-machines-disks-double-encryption-at-rest-regions](virtual-machines-disks-double-encryption-at-rest-regions.md)]

## <a name="getting-started"></a>開始使用

1. 登入 [Azure 入口網站](https://aka.ms/diskencryptionupdates)。

    > [!IMPORTANT]
    > 您必須使用[提供的連結](https://aka.ms/diskencryptionupdates)來存取 Azure 入口網站。 靜態加密目前不會顯示在公用 Azure 入口網站中，而不需要使用連結。

1. 搜尋並選取 [**磁片加密集**]。

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-disk-encryption-sets-search.png" alt-text="主要 Azure 入口網站的螢幕擷取畫面，在搜尋列中會反白顯示磁片加密集。":::

1. 選取 [+ 新增]。

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-add-disk-encryption-set.png" alt-text="[磁片加密集] 分頁的螢幕擷取畫面，[+ 新增] 會反白顯示。":::

1. 選取其中一個支援區域。
1. 針對 [**加密類型**]，選取 [**使用平臺管理和客戶管理的金鑰進行雙重加密**]。

    > [!NOTE]
    > 建立具有特定加密類型的磁片加密集之後，就無法變更它。 如果您想要使用不同的加密類型，您必須建立新的磁片加密集。

1. 填寫剩餘的資訊。

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-create-disk-encryption-set-blade.png" alt-text="反白顯示 [磁片加密組建立] 分頁、[區域] 和 [使用平臺管理的雙重加密] 和 [客戶管理] 金鑰的螢幕擷取畫面。":::

1. 選取 Azure Key Vault 和金鑰，或視需要建立一個新的金鑰。

    > [!NOTE]
    > 如果您建立 Key Vault 實例，您必須啟用「虛刪除」和「清除保護」。 當您使用 Key Vault 來加密受控磁片時，這些設定是必要的，並防止您因為意外刪除而遺失資料。

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-select-key-vault.png" alt-text="[Key Vault 建立] 分頁的螢幕擷取畫面。":::

1. 選取 [建立]。
1. 流覽至您所建立的磁片加密集，然後選取所顯示的錯誤。 這會將您的磁片加密設定為 [工作]。

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-disk-set-error.png" alt-text="磁片加密組顯示錯誤的螢幕擷取畫面，錯誤文字為：若要將磁片、映射或快照集與此磁片加密組產生關聯，您必須授與金鑰保存庫的許可權。":::

    通知應該會彈出並成功。 這麼做可讓您使用金鑰保存庫中的磁片加密集。
    
    ![金鑰保存庫的成功許可權和角色指派的螢幕擷取畫面。](media/virtual-machines-disk-encryption-portal/disk-encryption-notification-success.png)

1. 流覽至您的磁片。
1. 選取 [**加密**]。
1. 針對 [**加密類型**]，選取 [**使用平臺管理和客戶管理的金鑰進行雙重加密**]。
1. 選取您的磁片加密集。
1. 選取 [儲存]。

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-enable-disk-blade.png" alt-text="受控磁片的加密分頁螢幕擷取畫面，上述的加密類型會反白顯示。":::

您現在已在受控磁片上啟用雙重加密靜態。
