---
title: 資料加密-Azure 入口網站-適用于適用於 PostgreSQL 的 Azure 資料庫-單一伺服器
description: 瞭解如何使用 Azure 入口網站設定和管理適用於 PostgreSQL 的 Azure 資料庫單一伺服器的資料加密。
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: how-to
ms.date: 01/13/2020
ms.openlocfilehash: 00fd6ff9d79a59421a13d02ad4bafcf3f0a964fa
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86119662"
---
# <a name="data-encryption-for-azure-database-for-postgresql-single-server-by-using-the-azure-portal"></a>使用 Azure 入口網站適用於 PostgreSQL 的 Azure 資料庫單一伺服器的資料加密

瞭解如何使用 Azure 入口網站來設定及管理適用於 PostgreSQL 的 Azure 資料庫單一伺服器的資料加密。

## <a name="prerequisites-for-azure-cli"></a>Azure CLI 的必要條件

* 您必須具有 Azure 訂用帳戶，並且是該訂用帳戶的系統管理員。
* 在 Azure Key Vault 中，建立金鑰保存庫和金鑰，以用於客戶管理的金鑰。
* 金鑰保存庫必須具有下列屬性，才能當做客戶管理的金鑰使用：
  * [虛刪除](../key-vault/general/overview-soft-delete.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -test -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [清除受保護](../key-vault/general/overview-soft-delete.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```

* 金鑰必須具有下列屬性，才能當做客戶管理的金鑰使用：
  * 沒有到期日
  * 未停用
  * 能夠執行取得、包裝金鑰和解除包裝金鑰作業

## <a name="set-the-right-permissions-for-key-operations"></a>設定金鑰作業的正確許可權

1. 在 Key Vault 中，選取 [**存取原則**] [  >  **新增存取原則**]。

   ![已反白顯示存取原則和新增存取原則的 Key Vault 螢幕擷取畫面](media/concepts-data-access-and-security-data-encryption/show-access-policy-overview.png)

2. 選取 [**金鑰許可權**]，然後選取 [**取得**]、[**換行**]、[解除包裝] 和 [**主體** **]，這**是于 postgresql 伺服器的名稱。 如果在現有主體的清單中找不到您的伺服器主體，您必須註冊它。 當您第一次嘗試設定資料加密時，系統會提示您註冊伺服器主體，否則會失敗。  

   ![存取原則總覽](media/concepts-data-access-and-security-data-encryption/access-policy-wrap-unwrap.png)

3. 選取 [儲存]。

## <a name="set-data-encryption-for-azure-database-for-postgresql-single-server"></a>設定適用於 PostgreSQL 的 Azure 資料庫單一伺服器的資料加密

1. 在 [適用於 PostgreSQL 的 Azure 資料庫中，選取 [**資料加密**] 來設定客戶管理的金鑰。

   ![適用於 PostgreSQL 的 Azure 資料庫的螢幕擷取畫面，其中已反白顯示資料加密](media/concepts-data-access-and-security-data-encryption/data-encryption-overview.png)

2. 您可以選取金鑰保存庫和金鑰組，或輸入金鑰識別碼。

   ![適用於 PostgreSQL 的 Azure 資料庫的螢幕擷取畫面，其中已反白顯示資料加密選項](media/concepts-data-access-and-security-data-encryption/setting-data-encryption.png)

3. 選取 [儲存]。

4. 若要確保所有檔案（包括暫存檔案）都已完全加密，請重新開機伺服器。

## <a name="using-data-encryption-for-restore-or-replica-servers"></a>針對還原或複本伺服器使用資料加密

在「適用於 PostgreSQL 單一伺服器的 Azure 資料庫」使用儲存於 Key Vault 的客戶管理金鑰加密之後，任何新建立的伺服器複本也會一併加密。 您可以透過本機或異地還原作業，或透過複本（本機/跨區域）作業來建立此新複本。 因此，如果是加密的于 postgresql 伺服器，您可以使用下列步驟來建立加密的已還原伺服器。

1. 在您的伺服器上，選取 **[總覽] [**  >  **還原**]。

   ![適用於 PostgreSQL 的 Azure 資料庫的螢幕擷取畫面，其中已反白顯示總覽和還原](media/concepts-data-access-and-security-data-encryption/show-restore.png)

   或針對已啟用複寫功能的伺服器，在 [**設定**] 標題底下 **，選取 [** 複寫]。

   ![已反白顯示覆寫的適用於 PostgreSQL 的 Azure 資料庫螢幕擷取畫面](media/concepts-data-access-and-security-data-encryption/postgresql-replica.png)

2. 在還原作業完成之後，新建立的伺服器會以主伺服器的金鑰進行加密。 不過，伺服器上的功能和選項已停用，而且伺服器無法存取。 這會防止任何資料操作，因為新伺服器的身分識別尚未獲得存取金鑰保存庫的許可權。

   ![適用於 PostgreSQL 的 Azure 資料庫的螢幕擷取畫面，反白顯示狀態為無法存取](media/concepts-data-access-and-security-data-encryption/show-restore-data-encryption.png)

3. 若要讓伺服器可供存取，請重新驗證已還原伺服器上的金鑰。 選取 [**資料加密**重新  >  **驗證金鑰**]。

   > [!NOTE]
   > 第一次重新驗證的嘗試將會失敗，因為必須將金鑰保存庫的存取權提供給新伺服器的服務主體。 若要產生服務主體，請選取 [重新**驗證金鑰**]，這將會顯示錯誤，但會產生服務主體。 之後，請參閱本文稍早的[步驟](#set-the-right-permissions-for-key-operations)。

   ![適用於 PostgreSQL 的 Azure 資料庫的螢幕擷取畫面，反白顯示重新驗證步驟](media/concepts-data-access-and-security-data-encryption/show-revalidate-data-encryption.png)

   您必須將金鑰保存庫的存取權授與新的伺服器。

4. 註冊服務主體之後，再次重新驗證金鑰，伺服器就會繼續正常運作。

   ![適用於 PostgreSQL 的 Azure 資料庫的螢幕擷取畫面，其中顯示已還原的功能](media/concepts-data-access-and-security-data-encryption/restore-successful.png)

## <a name="next-steps"></a>後續步驟

 若要深入瞭解資料加密，請參閱[使用客戶管理的金鑰適用於 PostgreSQL 的 Azure 資料庫單一伺服器資料加密](concepts-data-encryption-postgresql.md)。
