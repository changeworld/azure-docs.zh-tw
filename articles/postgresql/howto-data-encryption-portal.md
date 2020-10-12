---
title: 資料加密-Azure 入口網站-適用于適用於 PostgreSQL 的 Azure 資料庫單一伺服器
description: 瞭解如何使用 Azure 入口網站來設定和管理適用於 PostgreSQL 的 Azure 資料庫單一伺服器的資料加密。
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: how-to
ms.date: 01/13/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 0db0a705d97743bb199550bc74ade8e270c7472c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90907471"
---
# <a name="data-encryption-for-azure-database-for-postgresql-single-server-by-using-the-azure-portal"></a>使用 Azure 入口網站適用於 PostgreSQL 的 Azure 資料庫單一伺服器的資料加密

瞭解如何使用 Azure 入口網站來設定及管理適用於 PostgreSQL 的 Azure 資料庫單一伺服器的資料加密。

## <a name="prerequisites-for-azure-cli"></a>Azure CLI 的必要條件

* 您必須具有 Azure 訂用帳戶，並且是該訂用帳戶的系統管理員。
* 在 Azure Key Vault 中，建立要用於客戶管理金鑰的金鑰保存庫和金鑰。
* 金鑰保存庫必須具有下列屬性，才能用來作為客戶管理的金鑰：
  * [虛刪除](../key-vault/general/soft-delete-overview.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -test -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [清除受保護](../key-vault/general/soft-delete-overview.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```

* 金鑰必須具有下列屬性，才能做為客戶管理的金鑰：
  * 沒有到期日
  * 未停用
  * 能夠執行 get、wrap key 和解除包裝金鑰作業

## <a name="set-the-right-permissions-for-key-operations"></a>設定金鑰作業的正確許可權

1. 在 Key Vault 中，選取 [**存取**原則  >  **新增存取原則**]。

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/show-access-policy-overview.png" alt-text="醒目提示 [存取原則] 和 [新增存取原則] Key Vault 的螢幕擷取畫面":::

2. 選取 [ **金鑰許可權**]，然後選取 [ **取得**]、[ **換行** **]、[** 解除包裝] 和 [ **主體**]，也就是于 postgresql 伺服器的名稱。 如果您在現有主體清單中找不到您的伺服器主體，則需要加以註冊。 當您第一次嘗試設定資料加密時，系統會提示您註冊您的伺服器主體，而且它會失敗。  

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/access-policy-wrap-unwrap.png" alt-text="醒目提示 [存取原則] 和 [新增存取原則] Key Vault 的螢幕擷取畫面":::

3. 選取 [儲存]****。

## <a name="set-data-encryption-for-azure-database-for-postgresql-single-server"></a>設定適用於 PostgreSQL 的 Azure 資料庫單一伺服器的資料加密

1. 在適用於 PostgreSQL 的 Azure 資料庫中，選取 [ **資料加密** ] 以設定客戶管理的金鑰。

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/data-encryption-overview.png" alt-text="醒目提示 [存取原則] 和 [新增存取原則] Key Vault 的螢幕擷取畫面":::

2. 您可以選取金鑰保存庫和金鑰組，或輸入金鑰識別碼。

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/setting-data-encryption.png" alt-text="醒目提示 [存取原則] 和 [新增存取原則] Key Vault 的螢幕擷取畫面":::

3. 選取 [儲存]****。

4. 為了確保所有檔案 (包括) 的暫存檔案都已完全加密，請重新開機伺服器。

## <a name="using-data-encryption-for-restore-or-replica-servers"></a>針對還原或複本伺服器使用資料加密

在「適用於 PostgreSQL 單一伺服器的 Azure 資料庫」使用儲存於 Key Vault 的客戶管理金鑰加密之後，任何新建立的伺服器複本也會一併加密。 您可以透過本機或異地還原作業，或透過 (本機/跨區域) 作業的複本來建立這個新的複本。 因此，針對加密的于 postgresql 伺服器，您可以使用下列步驟來建立加密的還原伺服器。

1. 在您的伺服器上，選取 **[總覽**  >  **還原**]。

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/show-restore.png" alt-text="醒目提示 [存取原則] 和 [新增存取原則] Key Vault 的螢幕擷取畫面":::

   若為已啟用複寫的伺服器，**請選取 [****設定**] 標題下的 [複寫]。

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/postgresql-replica.png" alt-text="醒目提示 [存取原則] 和 [新增存取原則] Key Vault 的螢幕擷取畫面":::

2. 在還原作業完成之後，新建立的伺服器會使用主伺服器的金鑰進行加密。 但是，伺服器上的功能和選項會停用，而且無法存取伺服器。 這可防止任何資料操作，因為新伺服器的身分識別尚未獲得存取金鑰保存庫的許可權。

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/show-restore-data-encryption.png" alt-text="醒目提示 [存取原則] 和 [新增存取原則] Key Vault 的螢幕擷取畫面":::

3. 若要讓伺服器可供存取，請在還原的伺服器上重新驗證金鑰。 選取**資料加密**重新  >  **驗證金鑰**。

   > [!NOTE]
   > 第一次嘗試重新驗證將會失敗，因為新伺服器的服務主體必須獲得金鑰保存庫的存取權。 若要產生服務主體，請選取 [重新 **驗證金鑰**]，這會顯示錯誤，但會產生服務主體。 之後，請參閱本文稍早的 [這些步驟](#set-the-right-permissions-for-key-operations) 。

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/show-revalidate-data-encryption.png" alt-text="醒目提示 [存取原則] 和 [新增存取原則] Key Vault 的螢幕擷取畫面":::

   您必須將金鑰保存庫的存取權授與新的伺服器。

4. 註冊服務主體之後，再次重新驗證金鑰，伺服器會繼續其正常功能。

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/restore-successful.png" alt-text="醒目提示 [存取原則] 和 [新增存取原則] Key Vault 的螢幕擷取畫面":::

## <a name="next-steps"></a>後續步驟

 若要深入瞭解資料加密，請參閱 [使用客戶管理的金鑰適用於 PostgreSQL 的 Azure 資料庫單一伺服器資料加密](concepts-data-encryption-postgresql.md)。
