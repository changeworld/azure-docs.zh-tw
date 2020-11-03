---
title: 針對資料加密進行疑難排解-適用於 PostgreSQL 的 Azure 資料庫-單一伺服器
description: 瞭解如何對適用於 PostgreSQL 的 Azure 資料庫-單一伺服器上的資料加密進行疑難排解
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 02/13/2020
ms.openlocfilehash: c315e1df473f3d23bab7e2a78ce166f22272ee70
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/03/2020
ms.locfileid: "93242240"
---
# <a name="troubleshoot-data-encryption-in-azure-database-for-postgresql---single-server"></a>針對適用於 PostgreSQL 的 Azure 資料庫-單一伺服器中的資料加密進行疑難排解

本文可協助您找出並解決在使用客戶管理的金鑰進行資料加密設定時，在適用於 PostgreSQL 的 Azure 資料庫的單一伺服器部署中可能發生的常見問題。

## <a name="introduction"></a>簡介

當您在 Azure Key Vault 中將資料加密設定為使用客戶管理的金鑰時，伺服器需要持續存取金鑰。 如果伺服器無法存取 Azure Key Vault 中客戶管理的金鑰，將會拒絕所有連線、傳回適當的錯誤訊息，並將其狀態變更為 [Azure 入口網站中的 [ **無法存取** ]。

如果您不再需要無法存取的適用於 PostgreSQL 的 Azure 資料庫伺服器，可以將它刪除，以停止產生成本。 除非已還原金鑰保存庫的存取權，而且伺服器可供使用，否則不允許伺服器上的任何其他動作。 當使用客戶管理的金鑰來加密時，也無法將資料加密選項從 `Yes` (客戶管理的) 變更為 `No` 無法存取的伺服器上 (服務管理的) 。 您必須手動重新驗證金鑰，才能再次存取伺服器。 當撤銷客戶管理金鑰的許可權時，此動作是保護資料免于未經授權存取的必要動作。

## <a name="common-errors-causing-server-to-become-inaccessible"></a>導致伺服器變成無法存取的常見錯誤

下列錯誤配置會導致使用 Azure Key Vault 金鑰的資料加密的大部分問題：

- 金鑰保存庫無法使用或不存在：
  - 意外刪除金鑰保存庫。
  - 間歇網路錯誤導致無法使用金鑰保存庫。

- 您沒有存取金鑰保存庫的許可權或金鑰不存在：
  - 金鑰已過期或被意外刪除或停用。
- 不小心刪除了適用於 PostgreSQL 的 Azure 資料庫實例的受控識別。
  - 適用於 PostgreSQL 的 Azure 資料庫實例的受控識別沒有足夠的金鑰許可權。 例如，許可權不包括 Get、Wrap 和解除包裝。
  - 已撤銷或刪除適用於 PostgreSQL 的 Azure 資料庫實例的受控識別許可權。

## <a name="identify-and-resolve-common-errors"></a>識別及解決常見的錯誤

### <a name="errors-on-the-key-vault"></a>Key vault 上的錯誤

#### <a name="disabled-key-vault"></a>已停用金鑰保存庫

- `AzureKeyVaultKeyDisabledMessage`
- _ * 說明 * *：因為 Azure Key Vault 索引鍵已停用，所以無法在伺服器上完成操作。

#### <a name="missing-key-vault-permissions"></a>遺漏金鑰保存庫許可權

- `AzureKeyVaultMissingPermissionsMessage`
- **說明** ：伺服器沒有 Azure Key Vault 所需的取得、包裝和解除包裝許可權。 將任何缺少的許可權授與具有識別碼的服務主體。

### <a name="mitigation"></a>降低

- 確認客戶管理的金鑰存在於金鑰保存庫中。
- 識別金鑰保存庫，並在 Azure 入口網站中移至該金鑰保存庫。
- 確定金鑰 URI 可識別存在的金鑰。

## <a name="next-steps"></a>下一步

[使用 Azure 入口網站，在適用於 PostgreSQL 的 Azure 資料庫上使用客戶管理的金鑰來設定資料加密](howto-data-encryption-portal.md)
