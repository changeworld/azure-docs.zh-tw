---
title: 針對 Azure 範疇中的連線進行疑難排解
description: 本文說明在 Azure 範疇中針對連線進行疑難排解的步驟。
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: f76a05757f86308785d4ca678675b87b3fa9d63e
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2020
ms.locfileid: "96552081"
---
# <a name="troubleshoot-your-connections-in-azure-purview"></a>針對 Azure 範疇中的連線進行疑難排解

本文說明如何在 Azure 範疇中設定資料來源的掃描時，針對連接錯誤進行疑難排解。

## <a name="permission-the-credential-on-the-data-source"></a>認證在資料來源上的許可權

如果您使用受控識別或服務主體作為掃描的驗證方法，則必須允許這些身分識別存取您的資料來源。

每個來源類型都有特定的指示：

- [Azure Blob 儲存體](register-scan-azure-blob-storage-source.md#setting-up-authentication-for-a-scan)
- [Azure Cosmos DB](register-scan-azure-cosmos-database.md#setting-up-authentication-for-a-scan)
- [Azure 資料總管](register-scan-azure-data-explorer.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen1](register-scan-adls-gen1.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen2](register-scan-adls-gen2.md#setting-up-authentication-for-a-scan) \(部分機器翻譯\)
- [Azure SQL Database](register-scan-azure-sql-database.md)
- [Azure SQL Database 受控執行個體](register-scan-azure-sql-database-managed-instance.md#setting-up-authentication-for-a-scan)
- [Azure Synapse Analytics](register-scan-azure-synapse-analytics.md#setting-up-authentication-for-a-scan)
- [SQL Server](register-scan-on-premises-sql-server.md#setting-up-authentication-for-a-scan)
- [Power BI](register-scan-power-bi-tenant.md)

## <a name="storing-your-credential-in-your-key-vault-and-using-the-right-secret-name-and-version"></a>將您的認證儲存在金鑰保存庫中，並使用正確的秘密名稱和版本

您也必須將認證儲存在 Azure Key Vault 的實例中，並使用正確的秘密名稱和版本。

遵循下列步驟來確認這一點：

1. 流覽至您的 Key Vault。
1. 選取 [設定] > [祕密]。
1. 選取您要用來對資料來源進行驗證的秘密，以進行掃描。
1. 選取您要使用的版本，然後按一下 [ **顯示秘密值**] 確認密碼或帳戶金鑰是否正確。 

## <a name="verify-permissions-for-the-purview-managed-identity-on-your-azure-key-vault"></a>確認 Azure Key Vault 上範疇受控識別的許可權

確認已針對範疇受控識別設定正確的許可權，以存取您的 Azure Key Vault。

若要確認這一點，請執行下列步驟：

1. 流覽至您的金鑰保存庫和 [ **存取原則** ] 區段
1. 確認範疇受控識別顯示在 [*目前存取原則*] 區段 **下，至少有**

如果您沒有看到列出的範疇受控識別，則請遵循 [建立和管理認證的](manage-credentials.md) 步驟，以新增掃描的認證。 

## <a name="next-steps"></a>後續步驟

- [流覽 Azure 範疇資料目錄](how-to-browse-catalog.md)
- [搜尋 Azure 範疇資料目錄](how-to-search-catalog.md)
