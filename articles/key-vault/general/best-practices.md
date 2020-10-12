---
title: 使用 Key Vault Azure Key Vault 的最佳作法 |Microsoft Docs
description: 瞭解 Azure Key Vault 的最佳作法，包括控制存取、使用個別金鑰保存庫、備份、記錄和修復選項的時機。
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-key-vault
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 03/07/2019
ms.author: mbaldwin
ms.openlocfilehash: cec3ad4e113fd6ee3f4e30ad2a6877b886a958e0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88189886"
---
# <a name="best-practices-to-use-key-vault"></a>使用 Key Vault 的最佳作法

## <a name="control-access-to-your-vault"></a>控制您保存庫的存取權

Azure Key Vault 是用來保護加密金鑰和祕密 (例如憑證、連接字串和密碼) 的雲端服務。 由於這項資料相當敏感且攸關業務，所以您必須藉由只允許獲得授權的應用程式和使用者，來保護金鑰保存庫的存取權。 本文 [提供 Key Vault](secure-your-key-vault.md) 存取模型的總覽。 文中會說明驗證和授權，並說明如何保護金鑰保存庫的存取權。

控制保存庫存取時的建議如下：
1. 鎖定訂用帳戶、資源群組和金鑰保存庫 (RBAC) 的存取權
2. 建立每個保存庫的存取原則
3. 使用最低許可權存取主體來授與存取權
4. 開啟防火牆和 [VNET 服務端點](overview-vnet-service-endpoints.md)

## <a name="use-separate-key-vault"></a>使用個別 Key Vault

我們的建議是在每個環境中使用每個應用程式的保存庫 (開發、進入生產階段前和生產) 。 這可協助您在不同的環境中共用秘密，也可在發生缺口時減少威脅。

## <a name="backup"></a>Backup

請確定您在保存庫中的物件更新/刪除/建立時，定期備份保存庫。

### <a name="azure-powershell-backup-commands"></a>Azure PowerShell 備份命令

* [備份憑證](https://docs.microsoft.com/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultCertificate?view=azurermps-6.13.0)
* [備份金鑰](https://docs.microsoft.com/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultKey?view=azurermps-6.13.0)
* [備份密碼](https://docs.microsoft.com/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultSecret?view=azurermps-6.13.0)

### <a name="azure-cli-backup-commands"></a>Azure CLI 備份命令

* [備份憑證](https://docs.microsoft.com/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-backup)
* [備份金鑰](https://docs.microsoft.com/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-backup)
* [備份密碼](https://docs.microsoft.com/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-backup)


## <a name="turn-on-logging"></a>開啟記錄

[開啟](logging.md) 您保存庫的記錄。 也請設定警示。

## <a name="turn-on-recovery-options"></a>開啟修復選項

1. 開啟虛 [刪除](soft-delete-overview.md)。
2. 如果您想要防止在開啟虛刪除之後強制刪除秘密/保存庫，請開啟 [清除保護]。
