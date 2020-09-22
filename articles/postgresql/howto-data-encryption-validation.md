---
title: 如何確保適用於 PostgreSQL 的 Azure 資料庫資料加密的驗證
description: 瞭解如何使用客戶管理的金鑰來驗證適用於 PostgreSQL 的 Azure 資料庫資料加密的加密。
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: how-to
ms.date: 04/28/2020
ms.openlocfilehash: 0a1b8eaee0a483fb4ce09a33d3c7ca9fe6f980e1
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90884350"
---
# <a name="validating-data-encryption-for-azure-database-for-postgresql"></a>正在驗證適用於 PostgreSQL 的 Azure 資料庫的資料加密

本文可協助您使用客戶管理的金鑰來驗證適用於 PostgreSQL 的 Azure 資料庫的資料加密是否如預期般運作。

## <a name="check-the-encryption-status"></a>檢查加密狀態

### <a name="from-portal"></a>從入口網站

1. 如果您想要確認客戶的金鑰用於加密，請遵循下列步驟：

    * 在 Azure 入口網站中，流覽至**Azure Key Vault**的  ->  **金鑰**
    * 選取用於伺服器加密的金鑰。
    * 將 **啟用** 的金鑰狀態設定為 [ **否**]。
  
       經過一段時間後 (**~ 15 分鐘** 的) ，適用於 PostgreSQL 的 Azure 資料庫伺服器的 **狀態** 應該是無法 **存取**的。 針對伺服器所執行的任何 i/o 作業都會失敗，這會驗證服務器確實已使用客戶金鑰加密，且金鑰目前無效。
    
        為了讓伺服器 **可供使用** ，您可以重新驗證金鑰。 
    
    * 將 Key Vault 中的金鑰狀態設定為 **[是]**。
    * 在 [伺服器 **資料加密**] 上，選取 [重新 **驗證金鑰**]。
    * 成功重新驗證金鑰之後，伺服器**狀態**會變更為 [**可用**]

2. 在 Azure 入口網站上，如果您可以確定已設定加密金鑰，則會使用 Azure 入口網站中使用的客戶金鑰來加密資料。

  :::image type="content" source="media/concepts-data-access-and-security-data-encryption/byok-validate.png" alt-text="存取原則總覽":::

### <a name="from-cli"></a>從 CLI

1. 我們可以使用 *AZ CLI* 命令驗證適用於 PostgreSQL 的 Azure 資料庫伺服器所使用的金鑰資源。

    ```azurecli-interactive
   az postgres server key list --name  '<server_name>'  -g '<resource_group_name>'
    ```

    如果伺服器沒有資料加密集，此命令將會產生空的 set []。

### <a name="azure-audit-reports"></a>Azure 審核報告

您也可以複習[審核報告](https://servicetrust.microsoft.com)，以提供有關資料保護標準和法規需求的合規性資訊。

## <a name="next-steps"></a>下一步

若要深入瞭解資料加密，請參閱 [使用客戶管理的金鑰適用於 PostgreSQL 的 Azure 資料庫單一伺服器資料加密](concepts-data-encryption-postgresql.md)。