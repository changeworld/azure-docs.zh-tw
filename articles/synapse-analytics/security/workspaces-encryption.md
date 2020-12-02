---
title: Azure Synapse Analytics 加密
description: 說明 Azure Synapse Analytics 中加密的文章
author: nanditavalsan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 11/19/2020
ms.author: nanditav
ms.reviewer: jrasnick
ms.openlocfilehash: d9a9d3c303739e68b5b8ef28053d6cf0b071f955
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96501051"
---
# <a name="encryption-for-azure-synapse-analytics-workspaces"></a>Azure Synapse Analytics 工作區的加密

本文將說明︰
* 加密 Synapse Analytics 工作區中的待用資料。
* 設定 Synapse 工作區，以使用客戶管理的金鑰來啟用加密。
* 管理用來將工作區中的資料加密的金鑰。

## <a name="encryption-of-data-at-rest"></a>待用資料加密

完整的靜態加密解決方案可確保資料永遠不會以未加密的形式保存。 靜態資料的雙重加密可透過兩個不同的加密層級來防止威脅，以防止任何單一層的危害。 Azure Synapse Analytics 為您的工作區中的資料提供使用客戶管理金鑰的第二層加密。 此金鑰會在您的 [Azure Key Vault](../../key-vault/general/overview.md)中受到保護，讓您能夠取得金鑰管理和輪替的擁有權。

Azure 服務的第一層加密是使用平臺管理的金鑰來啟用。 根據預設，Azure 磁片和 Azure 儲存體帳戶中的資料會自動加密。 深入瞭解如何在 [Azure 加密總覽](../../security/fundamentals/encryption-overview.md)的 Microsoft Azure 中使用加密。

## <a name="azure-synapse-encryption"></a>Azure Synapse 加密

本節將協助您進一步瞭解如何在 Synapse 工作區中啟用和強制執行客戶管理的金鑰加密。 這項加密會使用現有的金鑰或 Azure Key Vault 中產生的新金鑰。 使用單一金鑰來加密工作區中的所有資料。 Synapse 工作區支援具有2048和3072位元組大小的 RSA 金鑰。

> [!NOTE]
> Synapse 工作區不支援使用 Elliptic-Curve 的密碼編譯 (ECC) 金鑰進行加密。

下列 Synapse 元件中的資料會使用在工作區層級設定的客戶管理金鑰進行加密：
* SQL 集區
 * 專用的 SQL 集區
 * 無伺服器 SQL 集區
* Apache Spark 集區
* Azure Data Factory 整合執行時間、管線和資料集。

## <a name="workspace-encryption-configuration"></a>工作區加密設定

工作區可設定為在建立工作區時，使用客戶管理的金鑰來啟用雙重加密。 建立新的工作區時，請在 [安全性] 索引標籤上，選取 [使用客戶管理的金鑰啟用雙重加密] 選項。 您可以選擇輸入金鑰識別碼 URI，或從與工作區位於 **相同區域** 的金鑰保存庫清單中選取。 Key Vault 本身必須 **啟用清除保護**。

> [!IMPORTANT]
> 建立工作區之後，就無法變更雙重加密的設定設定。

:::image type="content" source="./media/workspaces-encryption/workspaces-encryption.png" alt-text="下圖顯示必須選取的選項，才能使用客戶管理的金鑰來啟用雙精度加密的工作區。":::

### <a name="key-access-and-workspace-activation"></a>金鑰存取和工作區啟用

具有客戶管理金鑰的 Azure Synapse 加密模型牽涉到存取 Azure Key Vault 中金鑰的工作區，並視需要進行加密和解密。 您可以透過存取原則或 Azure Key Vault RBAC 存取 ([預覽](../../key-vault/general/rbac-guide.md)) ，讓工作區存取金鑰。 透過 Azure Key Vault 存取原則授與許可權時，請在原則建立期間選擇 [ [僅限應用程式]](../../key-vault/general/secure-your-key-vault.md#key-vault-authentication-options) 選項 (選取工作區的受控識別，並不要將它新增為已授權的應用程式) 。

 工作區受控識別必須先授與金鑰保存庫所需的許可權，才能啟用工作區。 此工作區啟用的階段式方法可確保工作區中的資料會以客戶管理的金鑰加密。 請注意，您可以針對專用的 SQL 集區啟用或停用加密-預設不會啟用每個集區進行加密。

#### <a name="permissions"></a>權限

若要加密或解密待用資料，工作區受控身分識別必須具有下列許可權：
* WrapKey (在建立新的金鑰) 時，將索引鍵插入 Key Vault。
* UnwrapKey (取得解密) 的金鑰。
* 取得 (，以讀取金鑰的公開部分) 

#### <a name="workspace-activation"></a>工作區啟用

在您的工作區 (啟用雙重加密) 建立之後，將會維持「擱置」狀態，直到啟用成功為止。 您必須先啟用工作區，才能完全使用所有功能。 例如，啟用成功時，您只能建立新的專用 SQL 集區。 將金鑰保存庫的受控識別存取權授與工作區，然後按一下工作區 Azure 入口網站橫幅中的啟用連結。 啟用成功完成後，您的工作區就已準備好使用，以保證其中的所有資料都會以客戶管理的金鑰來保護。 如先前所述，金鑰保存庫必須啟用清除保護，才能成功啟用。

:::image type="content" source="./media/workspaces-encryption/workspace-activation.png" alt-text="下圖顯示具有工作區啟用連結的橫幅。":::


### <a name="manage-the-workspace-customer-managed-key"></a>管理工作區客戶管理的金鑰 

您可以從 Azure 入口網站中的 [ **加密** ] 頁面變更用來加密資料的客戶管理金鑰。 此外，您也可以使用金鑰識別碼來選擇新的金鑰，或從您可在與工作區相同的區域中存取的金鑰保存庫中選取。 如果您在不同的金鑰保存庫中選擇先前使用的金鑰，請將新金鑰保存庫的「取得」、「包裝」和「解除包裝」許可權授與工作區受控識別。 工作區將會驗證其對新金鑰保存庫的存取權，而且工作區中的所有資料都會以新的金鑰重新加密。

:::image type="content" source="./media/workspaces-encryption/workspace-encryption-management.png" alt-text="下圖顯示 Azure 入口網站中的工作區加密區段。":::

>[!IMPORTANT]
>變更工作區的加密金鑰時，請保留金鑰，直到您以新的金鑰取代工作區中的金鑰。 這是為了在使用新的金鑰重新加密之前，先允許使用舊金鑰解密資料。

自動定期輪替金鑰的 Azure 金鑰保存庫原則或金鑰的動作，可能會導致建立新的金鑰版本。 您可以選擇使用最新版本的作用中金鑰來重新加密工作區中的所有資料。 若要重新加密，請將 Azure 入口網站中的機碼變更為暫時金鑰，然後切換回您要用於加密的金鑰。 舉例來說，若要使用最新版本的 active key Key1 來更新資料加密，請將工作區客戶管理的金鑰變更為暫時金鑰 Key2。 等候具有 Key2 的加密完成。 然後，將工作區客戶管理的金鑰切換回工作區中的 Key1 資料，將會使用最新版本的 Key1 重新加密。

> [!NOTE]
> Azure Synapse Analytics 正在努力新增功能，以在建立新的金鑰版本時自動重新加密資料。 在可使用這項功能之前，若要確保您的工作區一致，請使用上述程式來強制重新加密資料。

#### <a name="sql-transparent-data-encryption-with-service-managed-keys"></a>具有服務管理之金鑰的 SQL 透明資料加密

SQL 透明資料加密 (TDE) 適用于 *未* 啟用雙重加密的工作區中的專用 sql 集區。 在這種類型的工作區中，服務管理的金鑰是用來針對專用 SQL 集區中的資料提供雙重加密。 使用服務管理金鑰的 TDE 可以針對個別的專用 SQL 集區啟用或停用。

## <a name="next-steps"></a>後續步驟

[使用內建的 Azure 原則來為 Synapse 工作區執行加密保護](../policy-reference.md)

