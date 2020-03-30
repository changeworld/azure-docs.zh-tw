---
title: 疑難排解資料加密 - Azure 資料庫，用於 PostgreSQL - 單個伺服器
description: 瞭解如何對 Azure 資料庫上的資料加密進行故障排除，以便 PostgreSQL - 單伺服器
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: 2902ff17ac14a48f1a11259339c2ab1bc4595980
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299255"
---
# <a name="troubleshoot-data-encryption-in-azure-database-for-postgresql---single-server"></a>在 Azure 資料庫中排除資料加密故障，用於 PostgreSQL - 單個伺服器

本文可説明您識別和解決使用客戶管理金鑰配置資料加密時為 PostgreSQL 配置 Azure 資料庫的單伺服器部署中可能出現的常見問題。

## <a name="introduction"></a>簡介

將資料加密配置為在 Azure 金鑰保存庫中使用客戶管理的金鑰時，伺服器需要持續訪問該金鑰。 如果伺服器無法訪問 Azure 金鑰保存庫中的客戶託管金鑰，它將拒絕所有連接、返回相應的錯誤訊息，並將其狀態更改為 Azure 門戶中的***不可訪問***狀態。

如果不再需要 PostgreSQL 伺服器無法訪問的 Azure 資料庫，則可以將其刪除以停止產生成本。 在還原金鑰保存庫且伺服器可用之前，不允許對伺服器上的其他操作。 當資料加密使用客戶管理的金鑰加密時，也不可能將資料`Yes`加密選項從（客戶管理`No`）更改為不可訪問的伺服器上（服務管理）。 在再次訪問伺服器之前，您必須手動重新驗證金鑰。 此操作對於在吊銷客戶管理金鑰的許可權時防止資料被未經授權的訪問是必需的。

## <a name="common-errors-causing-server-to-become-inaccessible"></a>導致伺服器無法訪問的常見錯誤

以下錯誤配置導致使用 Azure 金鑰保存庫金鑰的資料加密問題：

- 金鑰保存庫不可用或不存在：
  - 意外刪除金鑰保存庫。
  - 間歇網路錯誤導致無法使用金鑰保存庫。

- 您沒有訪問金鑰保存庫的許可權，或者金鑰不存在：
  - 金鑰已過期或意外刪除或禁用。
- 意外刪除 PostgreSQL 實例的 Azure 資料庫的託管標識。
  - PostgreSQL 實例的 Azure 資料庫的託管標識具有足夠的金鑰許可權。 例如，許可權不包括獲取、換行和取消包裝。
  - 已吊銷或刪除 PostgreSQL 實例的 Azure 資料庫的託管標識許可權。

## <a name="identify-and-resolve-common-errors"></a>識別及解決常見的錯誤

### <a name="errors-on-the-key-vault"></a>金鑰保存庫上的錯誤

#### <a name="disabled-key-vault"></a>禁用金鑰保存庫

- `AzureKeyVaultKeyDisabledMessage`
- **說明**： 由於 Azure 金鑰保存庫金鑰已禁用，因此無法在伺服器上完成該操作。

#### <a name="missing-key-vault-permissions"></a>缺少金鑰保存庫許可權

- `AzureKeyVaultMissingPermissionsMessage`
- **說明**：伺服器沒有 Azure 金鑰保存庫所需的獲取、換行和取消包裝許可權。 使用 ID 向服務主體授予任何缺少的許可權。

### <a name="mitigation"></a>降低

- 確認金鑰保存庫中存在客戶管理的金鑰。
- 識別金鑰保存庫，並在 Azure 入口網站中移至該金鑰保存庫。
- 確保金鑰 URI 標識存在金鑰。

## <a name="next-steps"></a>後續步驟

[使用 Azure 門戶在 PostgreSQL 的 Azure 資料庫上使用客戶管理的金鑰設置資料加密](howto-data-encryption-portal.md)
