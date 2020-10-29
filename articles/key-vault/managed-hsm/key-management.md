---
title: 管理受控 HSM 中的金鑰 - Azure Key Vault | Microsoft Docs
description: 使用本文來管理受控 HSM 中的金鑰
services: key-vault
author: amitbapat
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 3f054638e09061c652946c9c2db1a32db73c23d9
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/24/2020
ms.locfileid: "92521028"
---
# <a name="manage-a-managed-hsm-using-the-azure-cli"></a>使用 Azure CLI 管理受控 HSM

> [!NOTE]
> Key Vault 支援兩種類型的資源：保存庫和受控 HSM。 本文討論 **受控 HSM** 。 如果您想要了解如何管理保存庫，請參閱[使用 Azure CLI 來管理 Key Vault](../general/manage-with-cli2.md)。

如需受控 HSM 的概觀，請參閱[什麼是受控 HSM？](overview.md)

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>必要條件

若要完成本文中的步驟，您必須具有下列項目︰

* Microsoft Azure 訂用帳戶。 如果您沒有帳戶，您可以註冊[免費試用](https://azure.microsoft.com/pricing/free-trial)。
* Azure CLI 2.12.0 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI]( /cli/azure/install-azure-cli)。
* 訂用帳戶中的受控 HSM。 請參閱[快速入門：使用 Azure CLI 佈建並啟動受控 HSM](quick-create-cli.md)，以佈建並啟動受控 HSM。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-to-azure"></a>登入 Azure

若要使用 CLI 登入 Azure，您可以輸入：

```azurecli
az login
```

若要進一步了解透過 CLI 的登入選項，請參閱[使用 Azure CLI 進行登入](/cli/azure/authenticate-azure-cli?view=azure-cli-latest&preserve-view=true)

> [!NOTE]
> 下列所有命令都會顯示兩種使用方法。 一種方法使用 **--hsm-name** and **--name** (用於金鑰名稱) 參數，另一種方法使用 **--id** 參數，您可以在適當位置指定包含金鑰名稱的完整 URL。 當呼叫者 (使用者或應用程式) 沒有控制平面的讀取權限，而對資料平面只有受限制的存取權時，第二種方法就很有用。

## <a name="create-an-hsm-key"></a>建立 HSM 金鑰

使用 `az keyvault key create` 命令建立金鑰。

### <a name="create-an-rsa-key"></a>建立 RSA 金鑰

下列範例說明如何建立僅用於 **wrapKey、unwrapKey** 作業 (--ops) 的 3072 位元 **RSA** 金鑰。 


```azurecli-interactive
az keyvault key create --hsm-name ContosoMHSM --name myrsakey --ops wrapKey unwrapKey --kty RSA-HSM --size 3072

## OR
# Note the key name (myrsakey) in the URI

az keyvault key create --id https://ContosoMHSM.managedhsm.azure.net/keys/myrsakey --ops wrapKey unwrapKey --kty RSA-HSM --size 3072
```

請注意，`get` 作業只會傳回公開金鑰和金鑰屬性。 它不會傳回私密金鑰 (在非對稱金鑰的案例中) 或金鑰內容 (在對稱金鑰的案例中)。

### <a name="create-an-ec-key"></a>建立 EC 金鑰

下列範例說明如何建立具有 P-256 曲線的 **EC** 金鑰，此金鑰只會用於 **簽署和驗證** 作業 (--ops)，且具有 **usage** 和 **appname** 兩個標籤。 這些標籤可協助您將其他中繼資料新增至金鑰，以進行追蹤和管理。

```azurecli-interactive
az keyvault key create --hsm-name ContosoMHSM --name myec256key --ops sign verify  --tags ‘usage=signing] appname=myapp’ --kty EC-HSM --curve P-256

## OR
# Note the key name (myec256key) in the URI

az keyvault key create --id https://ContosoMHSM.managedhsm.azure.net/keys/myec256key --ops sign verify  --tags ‘usage=signing] appname=myapp’ --kty EC-HSM --curve P-256
```

### <a name="create-a-256-bit-symmetric-key"></a>建立 256 位元對稱金鑰

下列範例說明如何建立僅用於 **加密和解密** 作業 (--ops) 的 256 位元 **對稱** 金鑰。

```azurecli-interactive
az keyvault key create --hsm-name ContosoMHSM --name myaeskey --ops encrypt decrypt  --tags --kty oct-HSM --size 256

## OR
# Note the key name (myaeskey) in the URI

az keyvault key create --id https://ContosoMHSM.managedhsm.azure.net/keys/myaeskey --ops encrypt decrypt  --tags ‘usage=signing] appname=myapp’ --kty oct-HSM --size 256
```

## <a name="view-key-attributes-and-tags"></a>檢視金鑰屬性和標籤

使用 `az keyvault key show` 命令檢視金鑰的屬性、版本和標籤。

```azurecli-interactive
az keyvault key show --hsm-name ContosoHSM --name myrsakey

## OR
# Note the key name (myaeskey) in the URI

az keyvault key show --id https://ContosoMHSM.managedhsm.azure.net/keys/myrsakey

```

## <a name="list-keys"></a>列出金鑰

使用 `az keyvault key list` 命令列出受控 HSM 內的所有金鑰。

```azurecli-interactive
az keyvault key list --hsm-name ContosoHSM

## OR
# use full URI

az keyvault key list --id https://ContosoMHSM.managedhsm.azure.net/

```

## <a name="delete-a-key"></a>刪除金鑰

使用 `az keyvault key delete` 命令從受控 HSM 中刪除金鑰。 請注意，虛刪除一律會開啟。 因此，已刪除的金鑰會保持在已刪除狀態，且可進行復原，直到保留天數結束；屆時，金鑰將被清除 (永久刪除)，而無法再復原。

```azurecli-interactive
az keyvault key delete --hsm-name ContosoHSM --name myrsakey

## OR
# Note the key name (myaeskey) in the URI

az keyvault key delete --id https://ContosoMHSM.managedhsm.azure.net/keys/myrsakey
```

## <a name="list-deleted-keys"></a>列出已刪除的金鑰

使用 `az keyvault key list-deleted` 命令列出受控 HSM 中所有處於已刪除狀態的金鑰。

```azurecli-interactive
az keyvault key list-deleted --hsm-name ContosoHSM

## OR
# use full URI

az keyvault key list-deleted --id https://ContosoMHSM.managedhsm.azure.net/
```

## <a name="recover-undelete-a-deleted-key"></a>復原 (取消刪除) 已刪除的金鑰

使用 `az keyvault key list-deleted` 命令列出受控 HSM 中所有處於已刪除狀態的金鑰。 如果您在復原已刪除的金鑰時需要使用 --id 參數來復原 (取消刪除) 金鑰，您必須記下從 `az keyvault key list-deleted` 命令取得的已刪除金鑰的 `recoveryId` 值。

```azurecli-interactive
az keyvault key recover --hsm-name ContosoHSM --name myrsakey

## OR
# Note the key name (myaeskey) in the URI

az keyvault key recover --id https://ContosoMHSM.managedhsm.azure.net/deletedKeys/myrsakey
```

## <a name="purge-permanently-delete-a-key"></a>清除 (永久刪除) 金鑰

使用 `az keyvault key purge` 命令將金鑰清除 (永久刪除)。

> [!NOTE]
> 如果受控 HSM 已啟用清除保護，則不允許執行清除作業。 保留期間結束後，將會自動清除金鑰。

```azurecli-interactive
az keyvault key purge --hsm-name ContosoHSM --name myrsakey

## OR
# Note the key name (myaeskey) in the URI

az keyvault key recover --id https://ContosoMHSM.managedhsm.azure.net/deletedKeys/myrsakey

```

## <a name="create-a-single-key-backup"></a>建立單一金鑰備份

使用 `az keyvault key backup` 建立金鑰備份。 備份檔案是加密的 Blob，會透過密碼編譯繫結至來源 HSM 的安全性網域。 您只能在共用相同安全性網域的 HSM 中加以還原。 深入了解[安全性網域](security-domain.md)。

```azurecli-interactive
az keyvault key backup --hsm-name ContosoHSM --name myrsakey --file myrsakey.backup

## OR
# Note the key name (myaeskey) in the URI

az keyvault key backup --id https://ContosoMHSM.managedhsm.azure.net/deletedKeys/myrsakey  --file myrsakey.backup

```

## <a name="restore-a-single-key-from-backup"></a>從備份還原單一金鑰

使用 `az keyvault key restore` 還原單一金鑰。 備份建立所在的來源 HSM 必須與要還原金鑰的目標 HSM 共用相同的安全性網域。

> [!NOTE]
> 如果有名稱相同的金鑰處於作用中或已刪除狀態，則還原將會失敗。

```azurecli-interactive
az keyvault key restore --hsm-name ContosoHSM --name myrsakey --file myrsakey.bakup

## OR
# Note the key name (myaeskey) in the URI

az keyvault key recover --id https://ContosoMHSM.managedhsm.azure.net/deletedKeys/myrsakey --file myrsakey.bakup

```

## <a name="import-a-key-from-a-file"></a>從檔案匯入金鑰

使用 `az keyvault key import` 命令從檔案匯入金鑰 (僅限 RSA 和 EC)。 憑證檔案必須具有私密金鑰，且必須使用 PEM 編碼 (如 RFC [1421](https://tools.ietf.org/html/rfc1421)、[1422](https://tools.ietf.org/html/rfc1422)、[1423](https://tools.ietf.org/html/rfc1423)、[1424](https://tools.ietf.org/html/rfc1424) 所定義)。

```azurecli-interactive
az keyvault key import --hsm-name ContosoHSM --name myrsakey --pem-file mycert.key --password 'mypassword'

## OR
# Note the key name (myaeskey) in the URI

az keyvault key recover --id https://ContosoMHSM.managedhsm.azure.net/deletedKeys/myrsakey --pem-file mycert.key --password 'mypassword'
```

若要將金鑰從內部部署 HSM 匯入至受控 HSM，請參閱[將受 HSM 保護的金鑰匯入至受控 HSM (BYOK)](hsm-protected-keys-byok.md)

## <a name="next-steps"></a>後續步驟

- 如需金鑰保存庫命令的完整 Azure CLI 參考，請參閱 [Key Vault CLI 參考](/cli/azure/keyvault)。
- 如需程式設計參考，請參閱 [Azure 金鑰保存庫開發人員指南](../general/developers-guide.md)。
- 深入了解[受控 HSM 角色管理](role-management.md)
- 深入了解[受控 HSM 最佳做法](best-practices.md)
