---
title: 適用於 MySQL 的 Azure 資料庫疑難排解的資料加密
description: 瞭解如何針對您的適用於 MySQL 的 Azure 資料庫進行資料加密的疑難排解
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: 4b517a463ec949d804798787ad4b35b53145a4a8
ms.sourcegitcommit: f255f869c1dc451fd71e0cab340af629a1b5fb6b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/16/2020
ms.locfileid: "77371549"
---
# <a name="troubleshooting-data-encryption-with-customer-managed-keys-in-azure-database-for-mysql"></a>在適用於 MySQL 的 Azure 資料庫中使用客戶管理的金鑰進行資料加密的疑難排解
本文說明如何使用客戶管理的金鑰，來識別和解決在使用資料加密設定的適用於 MySQL 的 Azure 資料庫上發生的常見問題/錯誤。

## <a name="introduction"></a>簡介
當資料加密設定為在 Azure Key Vault 中使用客戶管理的金鑰時，伺服器必須持續存取此金鑰才能保持可用。 如果伺服器無法存取 Azure Key Vault 中客戶管理的金鑰，伺服器將會開始拒絕所有連線，並顯示適當的錯誤訊息，並將其狀態變更為在 Azure 入口網站中***無法***存取。

如果不再需要無法存取的適用於 MySQL 的 Azure 資料庫伺服器，可以立即刪除，以停止產生成本。 除非已還原 Azure 金鑰保存庫的存取權，且伺服器已恢復可用，否則不允許伺服器上的所有其他動作。 當伺服器以客戶管理的方式加密時，也無法在無法存取的伺服器上將資料加密選項從「是」（由客戶管理）變更為「否」（受服務管理）。 您必須手動重新驗證金鑰，讓伺服器可供使用。 這是保護資料不受未經授權存取的必要動作，而對客戶管理的金鑰的許可權已被撤銷。

## <a name="common-errors-causing-server-to-become-inaccessible"></a>導致伺服器變成無法存取的常見錯誤

當您使用資料加密與 Azure Key Vault 時所發生的大部分問題，都是由下列其中一個錯誤處理所造成-

金鑰保存庫無法使用或不存在

* 意外刪除金鑰保存庫。
* 間歇網路錯誤導致無法使用金鑰保存庫。

沒有存取金鑰保存庫的權限或金鑰不存在

* 意外刪除、停用金鑰或金鑰已過期。
* 不小心刪除了適用於 MySQL 的 Azure 資料庫實例管理的身分識別。
* 授與適用於 MySQL 的 Azure 資料庫伺服器受控識別金鑰的許可權不足（不包括取得、包裝和解除封裝）。
* 已撤銷適用於 MySQL 的 Azure 資料庫伺服器實例管理的身分識別的許可權。

## <a name="identify-and-resolve-common-errors"></a>識別及解決常見的錯誤
### <a name="errors-on-the-key-vault"></a>金鑰保存庫上的錯誤

#### <a name="disabled-key-vault"></a>已停用金鑰保存庫
* AzureKeyVaultKeyDisabledMessage
* **說明**：無法在伺服器上完成作業，因為 Azure Key Vault 金鑰已停用。

#### <a name="missing-key-vault-permissions"></a>遺失金鑰保存庫許可權
* AzureKeyVaultMissingPermissionsMessage
* 伺服器沒有 Azure Key Vault 許可權所需的 Get、Wrap 和解除包裝許可權。 將任何缺少的許可權授與識別碼為的服務主體。

### <a name="mitigation"></a>降低
* 確認 Key Vault 中是否有客戶管理的金鑰：
* 識別金鑰保存庫，並在 Azure 入口網站中移至該金鑰保存庫。
* 確認有金鑰 URI 所識別的金鑰。


## <a name="next-steps"></a>後續步驟
[使用 Azure 入口網站，以客戶管理的 Azure database For MySQL 的金鑰設定資料加密](howto-data-encryption-portal.md)。