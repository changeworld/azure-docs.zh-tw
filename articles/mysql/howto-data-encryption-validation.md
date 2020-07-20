---
title: 如何確保適用於 MySQL 的 Azure 資料庫資料加密的驗證
description: 瞭解如何使用客戶管理的金鑰來驗證適用於 MySQL 的 Azure 資料庫資料加密的加密。
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: how-to
ms.date: 04/28/2020
ms.openlocfilehash: 9cd1db0365cdc4f834516fd4efb0d80dacc75eca
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86118523"
---
# <a name="validating-data-encryption-for-azure-database-for-mysql"></a>驗證適用於 MySQL 的 Azure 資料庫的資料加密

本文可協助您使用客戶管理的金鑰來驗證資料加密，適用於 MySQL 的 Azure 資料庫如預期般運作。

## <a name="check-the-encryption-status"></a>檢查加密狀態

### <a name="from-portal"></a>從入口網站

1. 如果您想要驗證客戶的金鑰是否用於加密，請遵循下列步驟：

    * 在 [Azure 入口網站中，流覽至**Azure Key Vault**  ->  **金鑰**
    * 選取用於伺服器加密的金鑰。
    * 將**啟用**的金鑰狀態設定為 [**否**]。
  
       經過一段時間（**~ 15 分鐘**）之後，就應該無法**存取**適用於 MySQL 的 Azure 資料庫伺服器**狀態**。 針對伺服器所做的任何 i/o 作業都會失敗，這會驗證服務器是否確實以客戶金鑰加密，而且金鑰目前無效。
    
       為了讓伺服器**可供使用**，您可以重新驗證金鑰。 
    
    * 將 [Key Vault 中的機碼狀態設定為 **[是]**。
    * 在 [伺服器**資料加密**] 上，選取 [重新**驗證金鑰**]。
    * 重新驗證金鑰成功之後，伺服器**狀態**會變更為 [**可用**]。

2. 在 Azure 入口網站上，如果您可以確定已設定加密金鑰，則會使用在 Azure 入口網站中使用的客戶金鑰來加密資料。

  ![存取原則總覽](media/concepts-data-access-and-security-data-encryption/byok-validate.png)

### <a name="from-cli"></a>從 CLI

1. 我們可以使用*AZ CLI*命令來驗證用於適用於 MySQL 的 Azure 資料庫伺服器的金鑰資源。

    ```azurecli-interactive
   az mysql server key list --name  '<server_name>'  -g '<resource_group_name>'
    ```

    針對未設定資料加密的伺服器，此命令會產生空的 set []。

### <a name="azure-audit-reports"></a>Azure audit 報告

您也可以[審核 Audit 報告](https://servicetrust.microsoft.com)，以提供符合資料保護標準和法規需求的相關資訊。

## <a name="next-steps"></a>後續步驟

若要深入瞭解資料加密，請參閱[使用客戶管理的金鑰適用於 MySQL 的 Azure 資料庫資料加密](concepts-data-encryption-mysql.md)。