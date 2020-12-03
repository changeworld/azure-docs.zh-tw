---
title: 建立及管理掃描的認證
description: 本文說明在 Azure 範疇中建立和管理認證的步驟。
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: c991559d550b351ce70bcc5834f96f313f856a82
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2020
ms.locfileid: "96551880"
---
# <a name="credentials-for-source-authentication-in-azure-purview"></a>Azure 範疇中來源驗證的認證

本文說明如何在 Azure 範疇中建立認證，以快速重複使用並將已儲存的驗證資訊套用至您的資料來源掃描。

## <a name="prerequisites"></a>先決條件

* Azure key vault。 如果您還沒有這項 (插入 KV 建立文章的連結) 建立一個發行項。

## <a name="introduction"></a>簡介
認證是 Azure 範疇可用來向您註冊的資料來源進行驗證的驗證資訊。 您可以為各種類型的驗證案例建立認證物件 (例如需要使用者名稱/) 密碼的基本驗證，而且會根據所選的驗證方法類型，來取得所需的特定資訊。 認證會使用您現有的 Azure 金鑰保存庫秘密，在認證建立程式期間取得敏感性驗證資訊。

## <a name="using-purview-managed-identity-to-set-up-scans"></a>使用範疇受控識別來設定掃描
如果您使用範疇受控識別來設定掃描，就不需要明確建立認證，並將您的金鑰保存庫連結至範疇加以儲存。 如需新增範疇受控識別以存取來掃描資料來源的詳細指示，請參閱下列資料來源的特定驗證章節：

- [Azure Blob 儲存體](register-scan-azure-blob-storage-source.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen1](register-scan-adls-gen1.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen2](register-scan-adls-gen2.md#setting-up-authentication-for-a-scan) \(部分機器翻譯\)
- [Azure SQL Database](register-scan-azure-sql-database.md)
- [Azure SQL Database 受控執行個體](register-scan-azure-sql-database-managed-instance.md#setting-up-authentication-for-a-scan)
- [Azure Synapse Analytics](register-scan-azure-synapse-analytics.md#setting-up-authentication-for-a-scan)

## <a name="create-azure-key-vaults-connections-in-your-azure-purview-account"></a>在您的 Azure 範疇帳戶中建立 Azure 金鑰保存庫連接

建立認證之前，您必須先將一個或多個現有的 Azure Key Vault 實例與您的 Azure 範疇帳戶建立關聯。

1. 從 Azure 範疇導覽功能表中，流覽至 [管理中心]，然後流覽至 [認證]

2. 從認證命令列中，選取 [管理 Key Vault 連接]

    :::image type="content" source="media/manage-credentials/manage-kv-connections.png" alt-text="管理 AKV 連接":::

3. 從 [管理 Key Vault 連線] 面板中選取 [+ 新增] 

4. 提供必要的資訊，然後選取 [建立]

5. 確認您的 Key Vault 已成功與您的 Azure 範疇帳戶建立關聯

    :::image type="content" source="media/manage-credentials/view-kv-connections.png" alt-text="View AKV 連接":::

## <a name="grant-the-purview-managed-identity-access-to-your-azure-key-vault"></a>將範疇受控識別存取權授與您的 Azure Key Vault

流覽至您的金鑰保存庫-> 存取原則-> 新增存取原則]。 在 [秘密許可權] 下拉式清單中授與 Get 許可權，然後選取 [主體] 作為範疇 MSI。 

:::image type="content" source="media/manage-credentials/add-msi-to-akv.png" alt-text="將範疇 MSI 新增至 AKV":::


:::image type="content" source="media/manage-credentials/add-access-policy.png" alt-text="新增存取原則":::


:::image type="content" source="media/manage-credentials/save-access-policy.png" alt-text="儲存存取原則":::

## <a name="create-a-new-credential"></a>建立新的認證

範疇目前支援的認證類型：
* 基本驗證：您將會在金鑰保存庫中新增 **密碼** 做為秘密
* 服務主體：您會將 **服務主體金鑰** 新增為 key vault 中的秘密 
* SQL 驗證：您將會在金鑰保存庫中新增 **密碼** 做為秘密
* 帳戶金鑰：您會將 **帳戶金鑰** 新增為 key vault 中的秘密

以下是如何將秘密新增至金鑰保存庫的詳細資訊： (插入金鑰保存庫文章) 

將秘密儲存在金鑰保存庫之後，請從 [認證] 命令列選取 [+ 新增] 來建立新的認證。 提供必要的資訊，包括選取驗證方法，以及從中選取秘密的 Key Vault 實例。 填入所有詳細資料之後，請按一下 [建立]。

:::image type="content" source="media/manage-credentials/new-credential.png" alt-text="新增認證":::

確認您的新認證顯示在認證清單視圖中，並且已可供使用

:::image type="content" source="media/manage-credentials/view-credentials.png" alt-text="查看認證":::

## <a name="manage-your-key-vault-connections"></a>管理您的金鑰保存庫連接

1. 依名稱搜尋/尋找 Key Vault 連接

    :::image type="content" source="media/manage-credentials/search-kv.png" alt-text="搜尋金鑰保存庫":::

1. 刪除一或多個 Key Vault 連接
 
    :::image type="content" source="media/manage-credentials/delete-kv.png" alt-text="刪除 key vault":::

## <a name="manage-your-credentials"></a>管理您的認證

1. 依名稱搜尋/尋找認證
  
2. 選取並更新現有的認證

3. 刪除一或多個認證