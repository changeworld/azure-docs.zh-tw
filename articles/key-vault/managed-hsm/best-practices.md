---
title: 使用 Azure Key Vault 受控 HSM 的最佳作法
description: 本檔說明使用的一些最佳作法 Key Vault
services: key-vault
author: amitbapat
tags: azure-key-vault
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: conceptual
ms.date: 09/17/2020
ms.author: ambapat
ms.openlocfilehash: 7a30a7ab6689b602bc9ad4f696a6fe54c80f2151
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90994544"
---
# <a name="best-practices-when-using-managed-hsm"></a>使用受控 HSM 的最佳作法

## <a name="control-access-to-your-managed-hsm"></a>控制對受控 HSM 的存取

受控 HSM 是保護加密金鑰的雲端服務。 因為這些金鑰具有敏感性和商務關鍵性，請務必只允許已授權的應用程式和使用者，以保護對受控 Hsm 的存取。 本文 [提供存取](access-control.md) 模型的總覽。 它會說明驗證和授權，以及角色型存取控制。
- 為 HSM 系統管理員建立 [Azure Active Directory 安全性群組](../../active-directory/fundamentals/active-directory-manage-groups.md) (而不是將系統管理員角色指派給個人) 。 這將會在刪除個別帳戶時防止「系統管理鎖定」。
- 鎖定管理群組、訂用帳戶、資源群組和受控 Hsm 的存取權-使用 Azure RBAC 來控制管理群組、訂用帳戶和資源群組的存取權
- 使用[受控 HSM 本機 RBAC](access-control.md#data-plane-and-managed-hsm-local-rbac)建立每個金鑰角色指派
- 使用最低許可權存取主體指派角色

## <a name="choose-regions-that-support-availability-zones"></a>選擇支援可用性區域的區域

- 若要確保最佳的高可用性和區域復原，請選擇支援 [可用性區域](../../availability-zones/az-overview.md) 的 Azure 區域。 這些區域在 Azure 入口網站中會顯示為「建議的區域」。

## <a name="backup"></a>備份

- 請務必定期備份 HSM。 備份可在 HSM 層級和特定金鑰上完成。 

## <a name="turn-on-logging"></a>開啟記錄功能

- [開啟 HSM 的記錄功能](logging.md) 。 也請設定警示。

## <a name="turn-on-recovery-options"></a>開啟修復選項

- 預設會開啟虛[刪除](../general/soft-delete-overview.md)。
- 如果您想要防止在虛刪除開啟之後強制刪除 HSM，請開啟 [清除保護]。

## <a name="next-steps"></a>下一步

- 如需完整 HSM 備份/還原的詳細資訊，請參閱 [完整備份/還原](backup-restore.md) 。
- 請參閱 [受控 HSM 記錄](logging.md) ，以瞭解如何使用 Azure 監視器設定記錄
- 請參閱 [管理管理 HSM 金鑰](key-management.md) 以進行金鑰管理。
- 請參閱管理角色指派的管理 [HSM 角色管理](role-management.md) 。
