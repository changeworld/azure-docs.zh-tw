---
title: 針對資料加密進行疑難排解-適用於 MySQL 的 Azure 資料庫
description: 瞭解如何針對適用於 MySQL 的 Azure 資料庫中的資料加密進行疑難排解
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: 42956d115590fd322d2851fd546c505a76a851fa
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79297035"
---
# <a name="troubleshoot-data-encryption-in-azure-database-for-mysql"></a>針對適用於 MySQL 的 Azure 資料庫中的資料加密進行疑難排解

本文說明如何在使用客戶管理的金鑰進行資料加密設定時，識別和解決適用於 MySQL 的 Azure 資料庫中可能發生的常見問題。

## <a name="introduction"></a>簡介

當您設定資料加密以在 Azure Key Vault 中使用客戶管理的金鑰時，伺服器需要持續存取金鑰。 如果伺服器無法存取 Azure Key Vault 中客戶管理的金鑰，它會拒絕所有連線、傳回適當的錯誤訊息，並將其狀態變更為在 Azure 入口網站中***無法***存取。

如果您不再需要適用於 MySQL 的 Azure 資料庫伺服器無法存取，您可以將它刪除，以停止產生成本。 除非已還原金鑰保存庫的存取權，而且伺服器可供使用，否則不允許伺服器上的其他任何動作。 當您使用客戶管理的金鑰加密時，也無法在無法存取的伺服器上，將 [資料加密] 選項從 [`Yes`（由客戶管理）] 變更為 [`No` （服務管理）]。 您必須手動重新驗證金鑰，才能再次存取伺服器。 這是保護資料免于未經授權存取的必要動作，同時撤銷對客戶管理的金鑰的許可權。

## <a name="common-errors-that-cause-the-server-to-become-inaccessible"></a>導致伺服器變成無法存取的常見錯誤

下列錯誤配置會導致使用 Azure Key Vault 金鑰之資料加密的大部分問題：

- 金鑰保存庫無法使用或不存在：
  - 意外刪除金鑰保存庫。
  - 間歇網路錯誤導致無法使用金鑰保存庫。

- 您沒有存取金鑰保存庫的許可權，或機碼不存在：
  - 金鑰已過期或不小心遭到刪除或停用。
  - 不小心刪除了適用於 MySQL 的 Azure 資料庫實例的受控識別。
  - 適用於 MySQL 的 Azure 資料庫實例的受控識別沒有足夠的索引鍵許可權。 例如，許可權不包含 Get、Wrap 和解除包裝。
  - 已撤銷或刪除適用於 MySQL 的 Azure 資料庫實例的受控識別許可權。

## <a name="identify-and-resolve-common-errors"></a>識別及解決常見的錯誤

### <a name="errors-on-the-key-vault"></a>金鑰保存庫上的錯誤

#### <a name="disabled-key-vault"></a>已停用金鑰保存庫

- `AzureKeyVaultKeyDisabledMessage`
- **說明**：無法在伺服器上完成作業，因為 Azure Key Vault 金鑰已停用。

#### <a name="missing-key-vault-permissions"></a>遺失金鑰保存庫許可權

- `AzureKeyVaultMissingPermissionsMessage`
- **說明**：伺服器沒有所需的 Get、Wrap 和解除包裝許可權可 Azure Key Vault。 將任何缺少的許可權授與識別碼為的服務主體。

### <a name="mitigation"></a>降低

- 確認客戶管理的金鑰存在於金鑰保存庫中。
- 識別金鑰保存庫，並在 Azure 入口網站中移至該金鑰保存庫。
- 請確定金鑰 URI 識別出存在的索引鍵。

## <a name="next-steps"></a>後續步驟

[使用 Azure 入口網站，以在適用於 MySQL 的 Azure 資料庫上以客戶管理的金鑰設定資料加密](howto-data-encryption-portal.md)
