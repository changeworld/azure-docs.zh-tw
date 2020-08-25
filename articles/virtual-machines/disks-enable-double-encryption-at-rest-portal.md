---
title: 啟用靜態加密-Azure 入口網站管理的磁片
description: 使用 Azure 入口網站為您的受控磁片資料啟用雙重靜態加密。
author: roygara
ms.date: 08/24/2020
ms.topic: how-to
ms.author: rogarana
ms.service: virtual-machines-linux
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: bf2531536796965f145a9ac3e6a23cbb6634852a
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/25/2020
ms.locfileid: "88817489"
---
# <a name="use-the-azure-portal-to-enable-double-encryption-at-rest-for-managed-disks"></a>使用 Azure 入口網站啟用受控磁片的雙重靜態加密

Azure 磁碟儲存體支援受控磁片的雙重靜態加密。 如需有關靜態加密和其他受控磁片加密類型的概念資訊，請參閱磁片加密文章中的 **雙重加密加密** 一節：

- 若為 Linux： [雙重加密靜態](./linux/disk-encryption.md#double-encryption-at-rest)
- 若為 Windows： [靜態加密](./windows/disk-encryption.md#double-encryption-at-rest)

## <a name="supported-regions"></a>支援區域

[!INCLUDE [virtual-machines-disks-double-encryption-at-rest-regions](../../includes/virtual-machines-disks-double-encryption-at-rest-regions.md)]

## <a name="getting-started"></a>開始使用

1. 登入 [Azure 入口網站](https://aka.ms/diskencryptionupdates)。

    > [!IMPORTANT]
    > 您必須使用 [提供的連結](https://aka.ms/diskencryptionupdates) 來存取 Azure 入口網站。 靜態加密目前不會顯示在公用 Azure 入口網站中，而不需要使用連結。

1. 搜尋並選取 **磁片加密集**。

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-disk-encryption-sets-search.png" alt-text="主要 Azure 入口網站的螢幕擷取畫面，在搜尋列中會反白顯示磁片加密集。":::

1. 選取 [+ 新增] 。

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-add-disk-encryption-set.png" alt-text="磁片加密集分頁的螢幕擷取畫面，並反白顯示 [+ 新增]。":::

1. 選取其中一個支援區域。
1. 針對 [ **加密類型**]，選取 [ **使用平臺管理和客戶管理的金鑰進行雙重加密**]。

    > [!NOTE]
    > 一旦您建立具有特定加密類型的磁片加密集，就無法變更。 如果您想要使用不同的加密類型，則必須建立新的磁片加密集。

1. 填寫剩餘的資訊。

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-create-disk-encryption-set-blade.png" alt-text="醒目提示 [磁片加密集建立] 分頁的螢幕擷取畫面、區域，以及平臺管理和客戶管理金鑰的雙重加密。":::

1. 選取 Azure Key Vault 和金鑰，或視需要建立一個新的金鑰。

    > [!NOTE]
    > 如果您建立 Key Vault 實例，您必須啟用虛刪除和清除保護。 使用 Key Vault 來加密受控磁片時，這些設定是必要的，而且因為意外刪除，所以無法防止資料遺失。

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-select-key-vault.png" alt-text="Key Vault 建立] 分頁的螢幕擷取畫面。":::

1. 選取 [建立]  。
1. 流覽至您所建立的磁片加密集，然後選取所顯示的錯誤。 這會將您的磁片加密集設定為可運作。

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-disk-set-error.png" alt-text="磁片加密集顯示錯誤的螢幕擷取畫面，錯誤文字為：若要將磁片、映射或快照集與此磁片加密集建立關聯，您必須授與金鑰保存庫的許可權。":::

    通知應該會彈出並成功。 這樣做可讓您將磁片加密集與您的金鑰保存庫搭配使用。
    
    ![您金鑰保存庫的成功許可權和角色指派的螢幕擷取畫面。](media/virtual-machines-disks-double-encryption-at-rest-portal/disk-encryption-notification-success.png)

1. 流覽至您的磁片。
1. 選取 [ **加密**]。
1. 針對 [ **加密類型**]，選取 [ **使用平臺管理和客戶管理的金鑰進行雙重加密**]。
1. 選取您的磁片加密集。
1. 選取 [儲存]。

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-enable-disk-blade.png" alt-text="您受控磁片之加密分頁的螢幕擷取畫面，會反白顯示上述的加密類型。":::

您現在已在受控磁片上啟用雙重靜態加密。


## <a name="next-steps"></a>後續步驟

- [Azure PowerShell-使用伺服器端加密管理的磁片來啟用客戶管理的金鑰](./windows/disks-enable-customer-managed-keys-powershell.md)
- [Azure Resource Manager 範本範例](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/DoubleEncryption)
- [使用伺服器端加密來啟用客戶管理的金鑰-範例](./linux/disks-enable-customer-managed-keys-cli.md#examples)