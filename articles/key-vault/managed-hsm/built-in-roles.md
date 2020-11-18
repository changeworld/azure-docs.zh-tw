---
title: 受控 HSM 本機 RBAC 內建角色 - Azure Key Vault | Microsoft Docs
description: 概略說明可指派給使用者、服務主體、群組和受控識別的受控 HSM 內建角色
services: key-vault
author: amitbapat
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: a5ecd8f13a3255a565168f62f358a6a38e3cbab4
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94445212"
---
# <a name="managed-hsm-local-rbac-built-in-roles"></a>受控 HSM 本機 RBAC 內建角色

受控 HSM 本機 RBAC 有數個內建角色，可供您指派給使用者、服務主體、群組和受控識別。 若要允許主體執行作業，您必須為其指派適當角色，藉以授與執行該作業的權限。 這些角色和作業都只能讓您管理資料平面作業的權限。 若要管理受控 HSM 資源的控制平面權限 (例如，建立新的受控 HSM，或更新、移動、刪除現有的受控 HSM)，您必須使用 [Azure 角色型存取控制 (Azure RBAC)](../../role-based-access-control/overview.md)。

## <a name="built-in-roles"></a>內建角色

|角色名稱|描述|ID|
|---|---|---|
|受控 HSM 管理員| 授與所有資料動作的完整存取權。|a290e904-7015-4bba-90c8-60543313cdb4|
|受控 HSM 密碼編譯人員| 授與所有金鑰管理和金鑰密碼編譯作業的完整存取權|515eb02d-2335-4d2d-92f2-b1cbdf9c3778|
|受控 HSM 密碼編譯使用者|授與建立金鑰並將其用於密碼編譯作業的存取權。 無法永久刪除金鑰。|21dbd100-6940-42c2-9190-5d6cb909625b|
|受控 HSM 原則管理員| 授與建立和刪除角色指派的權限|4bd23610-cdcf-4971-bdee-bdc562cc28e4|
|受控 HSM 密碼編譯稽核員|授與讀取 (但非使用) 金鑰的讀取權限|2c18b078-7c48-4d3a-af88-5a3a1b3f82b3|
|受控 HSM 密碼編譯服務加密| 授與使用金鑰進行服務加密的權限。 |33413926-3206-4cdd-b39a-83574fe37a17|
|受控 HSM 備份| 授與執行單一金鑰或完整 HSM 備份的權限。 |7b127d3c-77bd-4e3e-bbe0-dbb8971fa7f8|

## <a name="permitted-operations"></a>允許的運算
> [!NOTE]  
> - 'X' 表示允許角色執行資料動作。 空的資料格表示角色沒有執行該資料動作的權限。
> - 所有資料動作名稱都具有 'Microsoft.KeyVault/managedHsm' 前置詞，為了簡潔起見，在表格中會加以省略。
> - 所有角色名稱都具有「受控 HSM」前置詞，為了簡潔起見，在下表中會加以省略。

|資料動作 | 系統管理員 | 密碼編譯人員 | 密碼編譯使用者 | [原則系統管理員] | 密碼編譯服務加密 | 備份 | 密碼編譯稽核員|
|---|---|---|---|---|---|---|---|
|**安全性網域管理**|
/securitydomain/download/action|<center>X</center>||||||
/securitydomain/upload/action|<center>X</center>||||||
/securitydomain/upload/read|<center>X</center>||||||
/securitydomain/transferkey/read|<center>X</center>||||||
|**金鑰管理**|
|/keys/read/action|<center>X</center>|<center>X</center>|<center>X</center>||<center>X</center>||<center>X</center>|
|/keys/write/action|<center>X</center>|<center>X</center>|<center>X</center>||||
|/keys/create|<center>X</center>|<center>X</center>|<center>X</center>||||
|/keys/delete|<center>X</center>|<center>X</center>|||||
|/keys/deletedKeys/read/action|<center>X</center>|<center>X</center>|||||
|/keys/deletedKeys/recover/action|<center>X</center>|<center>X</center>|||||
|/keys/deletedKeys/delete|<center>X</center>|<center>X</center>|||||<center>X</center>|
|/keys/backup/action|<center>X</center>|<center>X</center>|<center>X</center>|||<center>X</center>|
|/keys/restore/action|<center>X</center>|<center>X</center>|||||
|/keys/export/action|<center>X</center>|<center>X</center>|||||
|/keys/import/action|<center>X</center>|<center>X</center>|||||
|**金鑰密碼編譯作業**|
|/keys/encrypt/action|<center>X</center>|<center>X</center>|<center>X</center>||||
|/keys/decrypt/action|<center>X</center>|<center>X</center>|<center>X</center>||||
|/keys/wrap/action|<center>X</center>|<center>X</center>|<center>X</center>||<center>X</center>||
|/keys/unwrap/action|<center>X</center>|<center>X</center>|<center>X</center>||<center>X</center>||
|/keys/sign/action|<center>X</center>|<center>X</center>|<center>X</center>||||
|/keys/verify/action|<center>X</center>|<center>X</center>|<center>X</center>||||
|**角色管理**|
|/roleAssignments/delete/action|<center>X</center>|||<center>X</center>|||
|/roleAssignments/read/action|<center>X</center>|||<center>X</center>|||
|/roleAssignments/write/action|<center>X</center>|||<center>X</center>|||
|/roleDefinitions/read/action|<center>X</center>|||<center>X</center>|||
|**備份/還原管理**|
|/backup/start/action|<center>X</center>|||||<center>X</center>|
|/backup/status/action|<center>X</center>|||||<center>X</center>|
|/restore/start/action|<center>X</center>||||||
|/restore/status/action|<center>X</center>||||||
||||||||

## <a name="next-steps"></a>下一步

- 請參閱 [Azure 角色型存取控制 (Azure RBAC)](../../role-based-access-control/overview.md) 的概觀。
- 請參閱[受控 HSM 角色管理](role-management.md)的教學課程
