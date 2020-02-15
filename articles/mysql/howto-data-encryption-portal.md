---
title: 使用 Azure 入口網站適用於 MySQL 的 Azure 資料庫的資料加密
description: 瞭解如何使用 Azure 入口網站設定和管理適用於 MySQL 的 Azure 資料庫的資料加密。
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 42b7ceb86e360f192c55fc1090f291f5b7fe7ac5
ms.sourcegitcommit: 79cbd20a86cd6f516acc3912d973aef7bf8c66e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/14/2020
ms.locfileid: "77252020"
---
# <a name="data-encryption-for-azure-database-for-mysql-by-using-the-azure-portal"></a>使用 Azure 入口網站適用於 MySQL 的 Azure 資料庫的資料加密

瞭解如何使用 Azure 入口網站來設定和管理適用於 MySQL 的 Azure 資料庫的資料加密。

## <a name="prerequisites-for-azure-cli"></a>Azure CLI 的必要條件

* 您必須具有 Azure 訂用帳戶，並且是該訂用帳戶的系統管理員。
* 在 Azure Key Vault 中，建立金鑰保存庫和金鑰，以用於客戶管理的金鑰。
* 金鑰保存庫必須具有下列屬性，才能當做客戶管理的金鑰使用：
  * [虛刪除](../key-vault/key-vault-ovw-soft-delete.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [清除受保護](../key-vault/key-vault-ovw-soft-delete.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```

* 金鑰必須具有下列屬性，才能當做客戶管理的金鑰使用：
  * 沒有到期日
  * 未停用
  * 能夠執行取得、包裝金鑰、解除包裝金鑰作業

## <a name="set-the-right-permissions-for-key-operations"></a>設定金鑰作業的正確許可權

1. 在 Key Vault 中，選取 **存取**原則 > **新增存取原則**。

   ![已反白顯示存取原則和新增存取原則的 Key Vault 螢幕擷取畫面](media/concepts-data-access-and-security-data-encryption/show-access-policy-overview.png)

2. 選取 [**金鑰許可權**]，然後選取 [**取得**]、[包裝]、[解除**封裝**] 和 [**主體** **]，這**是 MySQL 伺服器的名稱。 如果在現有主體的清單中找不到您的伺服器主體，您必須註冊它。 當您第一次嘗試設定資料加密時，系統會提示您註冊伺服器主體，否則會失敗。

   ![存取原則總覽](media/concepts-data-access-and-security-data-encryption/access-policy-wrap-unwrap.png)

3. 選取 [儲存]。

## <a name="set-data-encryption-for-azure-database-for-mysql"></a>設定適用於 MySQL 的 Azure 資料庫的資料加密

1. 在 適用於 MySQL 的 Azure 資料庫中，選取 **資料加密** 來設定客戶管理的金鑰。

   ![適用於 MySQL 的 Azure 資料庫的螢幕擷取畫面，其中已反白顯示資料加密](media/concepts-data-access-and-security-data-encryption/data-encryption-overview.png)

2. 您可以選取金鑰保存庫和金鑰組，或輸入金鑰識別碼。

   ![適用於 MySQL 的 Azure 資料庫的螢幕擷取畫面，其中已反白顯示資料加密選項](media/concepts-data-access-and-security-data-encryption/setting-data-encryption.png)

3. 選取 [儲存]。

4. 若要確保所有檔案（包括暫存檔案）都已完全加密，請重新開機伺服器。

## <a name="restore-or-create-a-replica-of-the-server"></a>還原或建立伺服器複本

適用於 MySQL 的 Azure 資料庫使用儲存在 Key Vault 中的客戶管理金鑰加密之後，任何新建立的伺服器複本也會一併加密。 您可以透過本機或異地還原作業，或透過複本（本機/跨區域）作業來建立此新複本。 因此，對於已加密的 MySQL 伺服器，您可以使用下列步驟來建立加密的已還原伺服器。

1. 在您的伺服器上，選取 **[總覽**] > [**還原**]。

   ![適用於 MySQL 的 Azure 資料庫的螢幕擷取畫面，其中已反白顯示總覽和還原](media/concepts-data-access-and-security-data-encryption/show-restore.png)

   或針對已啟用複寫功能的伺服器，在 [**設定**] 標題底下 **，選取 [** 複寫]。

   ![已反白顯示覆寫的適用於 MySQL 的 Azure 資料庫螢幕擷取畫面](media/concepts-data-access-and-security-data-encryption/mysql-replica.png)

2. 在還原作業完成之後，新建立的伺服器會以主伺服器的金鑰進行加密。 不過，伺服器上的功能和選項已停用，而且伺服器無法存取。 這會防止任何資料操作，因為新伺服器的身分識別尚未獲得存取金鑰保存庫的許可權。

   ![適用於 MySQL 的 Azure 資料庫的螢幕擷取畫面，反白顯示狀態為無法存取](media/concepts-data-access-and-security-data-encryption/show-restore-data-encryption.png)

3. 若要讓伺服器可供存取，請重新驗證已還原伺服器上的金鑰。 選取 [**資料加密**] > 重新**驗證金鑰**。

   > [!NOTE]
   > 第一次重新驗證的嘗試將會失敗，因為必須將金鑰保存庫的存取權提供給新伺服器的服務主體。 若要產生服務主體，請選取 [重新**驗證金鑰**]，這將會顯示錯誤，但會產生服務主體。 之後，請參閱本文稍早的[步驟](#set-the-right-permissions-for-key-operations)。

   ![適用於 MySQL 的 Azure 資料庫的螢幕擷取畫面，反白顯示重新驗證步驟](media/concepts-data-access-and-security-data-encryption/show-revalidate-data-encryption.png)

   您必須將金鑰保存庫的存取權授與新的伺服器。

4. 註冊服務主體之後，再次重新驗證金鑰，伺服器就會繼續正常運作。

   ![適用於 MySQL 的 Azure 資料庫的螢幕擷取畫面，其中顯示已還原的功能](media/concepts-data-access-and-security-data-encryption/restore-successful.png)

## <a name="next-steps"></a>後續步驟

 若要深入瞭解資料加密，請參閱[使用客戶管理的金鑰適用於 MySQL 的 Azure 資料庫資料加密](concepts-data-encryption-mysql.md)。
