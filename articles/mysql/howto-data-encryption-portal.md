---
title: 資料加密-Azure 入口網站-適用於 MySQL 的 Azure 資料庫
description: 瞭解如何使用 Azure 入口網站來設定和管理適用於 MySQL 的 Azure 資料庫的資料加密。
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 01/13/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 9de4a4534551c4a41b2c81c1d10fecf6118ff868
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96434510"
---
# <a name="data-encryption-for-azure-database-for-mysql-by-using-the-azure-portal"></a>使用 Azure 入口網站適用於 MySQL 的 Azure 資料庫的資料加密

瞭解如何使用 Azure 入口網站來設定和管理適用於 MySQL 的 Azure 資料庫的資料加密。

## <a name="prerequisites-for-azure-cli"></a>Azure CLI 的必要條件

* 您必須具有 Azure 訂用帳戶，並且是該訂用帳戶的系統管理員。
* 在 Azure Key Vault 中，建立要用於客戶管理金鑰的金鑰保存庫和金鑰。
* 金鑰保存庫必須具有下列屬性，才能用來作為客戶管理的金鑰：
  * [虛刪除](../key-vault/general/soft-delete-overview.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [清除受保護](../key-vault/general/soft-delete-overview.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```
  * 保留天數設定為90天
  
    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --retention-days 90
    ```

* 金鑰必須具有下列屬性，才能做為客戶管理的金鑰：
  * 沒有到期日
  * 未停用
  * 執行 **取得**、 **包裝**、 **解除** 包裝作業
  * recoverylevel 屬性設定為 **可** 復原。

您可以使用下列命令來驗證金鑰的上述屬性：

```azurecli-interactive
az keyvault key show --vault-name <key_vault_name> -n <key_name>
```

## <a name="set-the-right-permissions-for-key-operations"></a>設定金鑰作業的正確許可權

1. 在 Key Vault 中，選取 [**存取** 原則  >  **新增存取原則**]。

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/show-access-policy-overview.png" alt-text="醒目提示 [存取原則] 和 [新增存取原則] Key Vault 的螢幕擷取畫面":::

2. 選取 [ **金鑰許可權**]，然後選取 [ **取得**]、[ **換行** **]、[** 解除包裝] 和 [ **主體**]，也就是 MySQL 伺服器的名稱。 如果您在現有主體清單中找不到您的伺服器主體，則需要加以註冊。 當您第一次嘗試設定資料加密時，系統會提示您註冊您的伺服器主體，而且它會失敗。

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/access-policy-wrap-unwrap.png" alt-text="存取原則總覽":::

3. 選取 [儲存]。

## <a name="set-data-encryption-for-azure-database-for-mysql"></a>設定適用於 MySQL 的 Azure 資料庫的資料加密

1. 在適用於 MySQL 的 Azure 資料庫中，選取 [ **資料加密** ] 以設定客戶管理的金鑰。

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/data-encryption-overview.png" alt-text="適用於 MySQL 的 Azure 資料庫的螢幕擷取畫面，其中反白顯示資料加密":::

2. 您可以選取金鑰保存庫和金鑰組，或輸入金鑰識別碼。

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/setting-data-encryption.png" alt-text="適用於 MySQL 的 Azure 資料庫的螢幕擷取畫面，其中反白顯示資料加密選項":::

3. 選取 [儲存]。

4. 為了確保所有檔案 (包括) 的暫存檔案都已完全加密，請重新開機伺服器。

## <a name="using-data-encryption-for-restore-or-replica-servers"></a>針對還原或複本伺服器使用資料加密

在「適用於 MySQL 的 Azure 資料庫」使用儲存於 Key Vault 的客戶管理金鑰加密之後，任何新建立的伺服器複本也會一併加密。 您可以透過本機或異地還原作業，或透過 (本機/跨區域) 作業的複本來建立這個新的複本。 因此，針對加密的 MySQL 伺服器，您可以使用下列步驟來建立加密的還原伺服器。

1. 在您的伺服器上，選取 **[總覽**  >  **還原**]。

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/show-restore.png" alt-text="醒目提示和還原醒目提示適用於 MySQL 的 Azure 資料庫的螢幕擷取畫面":::

   若為已啟用複寫的伺服器，**請選取 [****設定**] 標題下的 [複寫]。

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/mysql-replica.png" alt-text="適用於 MySQL 的 Azure 資料庫的螢幕擷取畫面，其中已反白顯示覆寫":::

2. 在還原作業完成之後，新建立的伺服器會使用主伺服器的金鑰進行加密。 但是，伺服器上的功能和選項會停用，而且無法存取伺服器。 這可防止任何資料操作，因為新伺服器的身分識別尚未獲得存取金鑰保存庫的許可權。

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/show-restore-data-encryption.png" alt-text="適用於 MySQL 的 Azure 資料庫的螢幕擷取畫面，其中反白顯示 [無法存取] 狀態":::

3. 若要讓伺服器可供存取，請在還原的伺服器上重新驗證金鑰。 選取 **資料加密** 重新  >  **驗證金鑰**。

   > [!NOTE]
   > 第一次嘗試重新驗證將會失敗，因為新伺服器的服務主體必須獲得金鑰保存庫的存取權。 若要產生服務主體，請選取 [重新 **驗證金鑰**]，這會顯示錯誤，但會產生服務主體。 之後，請參閱本文稍早的 [這些步驟](#set-the-right-permissions-for-key-operations) 。

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/show-revalidate-data-encryption.png" alt-text="適用於 MySQL 的 Azure 資料庫的螢幕擷取畫面，其中反白顯示重新驗證步驟":::

   您必須將金鑰保存庫的存取權授與新的伺服器。

4. 註冊服務主體之後，再次重新驗證金鑰，伺服器會繼續其正常功能。

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/restore-successful.png" alt-text="適用於 MySQL 的 Azure 資料庫的螢幕擷取畫面，其中顯示已還原的功能":::

## <a name="next-steps"></a>後續步驟

 若要深入瞭解資料加密，請參閱 [使用客戶管理的金鑰適用於 MySQL 的 Azure 資料庫資料加密](concepts-data-encryption-mysql.md)。
