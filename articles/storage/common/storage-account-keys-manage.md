---
title: 管理帳戶存取金鑰
titleSuffix: Azure Storage
description: 瞭解如何查看、管理和輪換存儲帳戶訪問金鑰。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/18/2019
ms.author: tamram
ms.openlocfilehash: 13adf6de420b54299d04a226dab81e75cbb9fef2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75975793"
---
# <a name="manage-storage-account-access-keys"></a>管理存儲帳戶訪問金鑰

您建立儲存體帳戶時，Azure 會產生兩個 512 位元儲存體帳戶存取金鑰。 這些金鑰可用於通過共用金鑰授權授權訪問存儲帳戶中的資料。

Microsoft 建議使用 Azure 金鑰保存庫來管理訪問金鑰，並定期輪換和重新生成金鑰。 使用 Azure 金鑰保存庫可以輕鬆旋轉金鑰，而不會中斷應用程式。 您還可以手動旋轉鍵。

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

## <a name="view-access-keys-and-connection-string"></a>查看訪問金鑰和連接字串

[!INCLUDE [storage-view-keys-include](../../../includes/storage-view-keys-include.md)]

## <a name="use-azure-key-vault-to-manage-your-access-keys"></a>使用 Azure 金鑰保存庫管理訪問金鑰

Microsoft 建議使用 Azure 金鑰保存庫來管理和旋轉訪問金鑰。 應用程式可以在金鑰保存庫中安全地訪問金鑰，以便避免將金鑰與應用程式代碼一起存儲。 有關將金鑰保存庫用於金鑰管理的詳細資訊，請參閱以下文章：

- [使用 Azure 金鑰保存庫和 PowerShell 管理存儲帳戶金鑰](../../key-vault/key-vault-overview-storage-keys-powershell.md)
- [使用 Azure 金鑰保存庫和 Azure CLI 管理存儲帳戶金鑰](../../key-vault/key-vault-ovw-storage-keys.md)

## <a name="manually-rotate-access-keys"></a>手動旋轉便捷鍵

Microsoft 建議您定期輪換訪問金鑰，以説明保護存儲帳戶的安全。 如果可能，請使用 Azure 金鑰保存庫來管理訪問金鑰。 如果不使用金鑰保存庫，則需要手動旋轉金鑰。

由於指派兩個存取金鑰，因此您可以旋轉金鑰。 擁有兩個金鑰可確保應用程式在整個過程中保持對 Azure 存儲的訪問。

> [!WARNING]
> 重新產生存取金鑰會影響相依於儲存體帳戶金鑰的應用程式或 Azure 服務。 使用帳戶金鑰來存取儲存體帳戶的任何用戶端必須更新並使用新的金鑰，包括媒體服務、雲端、桌面和行動應用程式，以及 Azure 儲存體的圖形化使用者介面應用程式，例如 [Azure 儲存體總管](https://azure.microsoft.com/features/storage-explorer/)。

請遵循此程序來旋轉您的儲存體帳戶金鑰：

1. 更新您的應用程式碼中的連接字串，以使用次要金鑰。
2. 重新產生儲存體帳戶的主要存取金鑰。 在 Azure 入口網站中的 [存取金鑰]**** 刀鋒視窗上，按一下 [重新產生 Key1]****，然後按一下 [是]**** 確認您要重新產生新的金鑰。
3. 更新程式碼中的連接字串，以參考新的主要存取金鑰。
4. 以同樣的方式重新產生次要存取金鑰。

> [!NOTE]
> Microsoft 建議同一時間在您的所有應用程式中僅使用其中一個金鑰。 如果您在某些地方使用金鑰 1 並在其他地方使用金鑰 2，您就無法在沒有部分應用程式遺失存取的情況下輪換您的金鑰。

## <a name="next-steps"></a>後續步驟

- [Azure 儲存體帳戶概觀](storage-account-overview.md)
- [創建存儲帳戶](storage-account-create.md)
