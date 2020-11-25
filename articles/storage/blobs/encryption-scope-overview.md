---
title: Blob 儲存體 (預覽) 的加密範圍
description: 加密範圍提供在容器層級或個別 blob 管理加密的功能。 您可以使用加密範圍來建立位於相同儲存體帳戶但屬於不同客戶的資料之間的安全界限。
services: storage
author: tamram
ms.service: storage
ms.date: 09/22/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 881a7fc915ab986577599b85f8412fa8107f7902
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017384"
---
# <a name="encryption-scopes-for-blob-storage-preview"></a>Blob 儲存體 (預覽) 的加密範圍

加密範圍提供在容器層級或個別 blob 管理加密的功能。 您可以使用加密範圍來建立位於相同儲存體帳戶但屬於不同客戶的資料之間的安全界限。

根據預設，儲存體帳戶會使用限定于整個儲存體帳戶的金鑰進行加密。 使用加密範圍，您可以指定一或多個容器以僅限這些容器範圍的金鑰加密。

您可以選擇使用儲存在 Azure Key Vault 中的 Microsoft 管理金鑰或由客戶管理的金鑰，來保護及控制加密資料的金鑰存取權。 相同儲存體帳戶上的不同加密範圍可以使用 Microsoft 管理或客戶管理的金鑰。

建立加密範圍之後，您可以在要求上指定該加密範圍來建立容器或 blob。 如需如何建立加密範圍的詳細資訊，請參閱 [建立及管理加密範圍 (預覽) ](encryption-scope-manage.md)。

> [!NOTE]
> 在預覽期間，讀取權限異地冗余儲存體 (GRS) 或讀取權限地理區域冗余儲存體 (RA-GZRS) 帳戶不支援加密範圍。

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

> [!IMPORTANT]
> 加密範圍預覽僅供非生產環境使用。 生產環境的服務等級協定 (SLA) 目前無法使用。
>
> 為了避免非預期的成本，請務必停用您目前不需要的任何加密範圍。

## <a name="create-a-container-or-blob-with-an-encryption-scope"></a>使用加密範圍建立容器或 blob

在加密範圍下建立的 blob 會以該範圍指定的金鑰進行加密。 您可以在建立 blob 時指定個別 blob 的加密範圍，也可以在建立容器時指定預設的加密範圍。 在容器層級指定預設加密範圍時，該容器中的所有 blob 都會使用與預設範圍相關聯的金鑰進行加密。

當您在具有預設加密範圍的容器中建立 blob 時，如果容器設定為允許覆寫預設加密範圍，您就可以指定覆寫預設加密範圍的加密範圍。 若要避免覆寫預設加密範圍，請將容器設定為拒絕個別 blob 的覆寫。

只要加密範圍未停用，就會以透明的方式執行屬於加密範圍之 blob 的讀取作業。

## <a name="disable-an-encryption-scope"></a>停用加密範圍

當您停用加密範圍時，任何使用加密範圍進行的後續讀取或寫入作業都會失敗，並出現 HTTP 錯誤碼 403 (禁止) 。 如果您重新啟用加密範圍，讀取和寫入作業將會再次正常執行。

當加密範圍停用時，就不會再向您收費。 停用任何不需要的加密範圍，以避免不必要的費用。

如果您的加密範圍受到客戶管理的金鑰保護，則您也可以在金鑰保存庫中刪除相關聯的金鑰，以便停用加密範圍。 請記住，客戶管理的金鑰受限於金鑰保存庫中的虛刪除和清除保護，而且已刪除的金鑰受限於這些屬性所定義的行為。 如需詳細資訊，請參閱 Azure Key Vault 檔中的下列其中一個主題：

- [透過 PowerShell 使用虛刪除](../../key-vault/general/key-vault-recovery.md)
- [如何透過 CLI 使用虛刪除](../../key-vault/general/key-vault-recovery.md)

> [!NOTE]
> 不可能刪除加密範圍。

## <a name="next-steps"></a>後續步驟

- [待用資料的 Azure 儲存體加密](../common/storage-service-encryption.md)
- [建立及管理加密範圍 (預覽) ](encryption-scope-manage.md)
- [Azure 儲存體加密的客戶管理金鑰](../common/customer-managed-keys-overview.md)
- [什麼是 Azure 金鑰保存庫？](../../key-vault/general/overview.md)