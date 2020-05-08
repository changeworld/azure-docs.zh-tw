---
title: 使用 Key Vault Azure Key Vault 的最佳做法 |Microsoft Docs
description: 本檔說明使用的一些最佳作法 Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-key-vault
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 03/07/2019
ms.author: mbaldwin
ms.openlocfilehash: cf8d461485cefd37b9508031f5cce7ae0a070ef5
ms.sourcegitcommit: d662eda7c8eec2a5e131935d16c80f1cf298cb6b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/01/2020
ms.locfileid: "82653205"
---
# <a name="best-practices-to-use-key-vault"></a>使用 Key Vault 的最佳做法

## <a name="control-access-to-your-vault"></a>控制您保存庫的存取權

Azure Key Vault 是用來保護加密金鑰和祕密 (例如憑證、連接字串和密碼) 的雲端服務。 由於這項資料相當敏感且攸關業務，所以您必須藉由只允許獲得授權的應用程式和使用者，來保護金鑰保存庫的存取權。 [本文](secure-your-key-vault.md)提供 Key Vault 存取模型的總覽。 文中會說明驗證和授權，並說明如何保護金鑰保存庫的存取權。

控制對保存庫的存取時的建議如下：
1. 鎖定您的訂用帳戶、資源群組和金鑰保存庫（RBAC）的存取權
2. 為每個保存庫建立存取原則
3. 使用最低許可權存取主體來授與存取權
4. 開啟防火牆和[VNET 服務端點](overview-vnet-service-endpoints.md)

## <a name="use-separate-key-vault"></a>使用不同的 Key Vault

我們的建議是在每個環境中使用每個應用程式的保存庫（開發、生產前和生產）。 這可協助您在不同的環境之間共用秘密，同時也會在發生缺口時減少威脅。

## <a name="backup"></a>Backup 

請確定您在保存庫中的物件上進行更新/刪除/建立時，定期備份您的保存[庫](https://blogs.technet.microsoft.com/kv/2018/07/20/announcing-backup-and-restore-of-keys-secrets-and-certificates/)。

## <a name="turn-on-logging"></a>開啟記錄功能

開啟保存庫的[記錄功能](logging.md)。 同時設定警示。

## <a name="turn-on-recovery-options"></a>開啟修復選項

1. 開啟虛[刪除](overview-soft-delete.md)。
2. 如果您想要防止在開啟虛刪除之後強制刪除秘密/保存庫，請開啟 [清除保護]。
