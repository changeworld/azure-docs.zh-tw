---
title: 使用金鑰保存庫的最佳做法 - Azure 金鑰保存庫 |微軟文檔
description: 本文檔介紹了使用金鑰保存庫的一些最佳實踐
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-key-vault
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 03/07/2019
ms.author: mbaldwin
ms.openlocfilehash: cb4bec8170466f0fc667b592d44b0858c41ccd84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270988"
---
# <a name="best-practices-to-use-key-vault"></a>使用金鑰保存庫的最佳做法

## <a name="control-access-to-your-vault"></a>控制對保存庫的訪問

Azure Key Vault 是用來保護加密金鑰和祕密 (例如憑證、連接字串和密碼) 的雲端服務。 由於這項資料相當敏感且攸關業務，所以您必須藉由只允許獲得授權的應用程式和使用者，來保護金鑰保存庫的存取權。 [本文](key-vault-secure-your-key-vault.md)概述了金鑰保存庫訪問模型。 文中會說明驗證和授權，並說明如何保護金鑰保存庫的存取權。

控制對保存庫的訪問時的建議如下：
1. 鎖定對訂閱、資源組和金鑰保存庫 （RBAC） 的訪問
2. 為每個保存庫創建訪問策略
3. 使用最低許可權訪問主體授予存取權限
4. 打開防火牆和[VNET 服務終結點](key-vault-overview-vnet-service-endpoints.md)

## <a name="use-separate-key-vault"></a>使用單獨的金鑰保存庫

我們的建議是，每個環境（開發、生產前和生產）每個應用程式使用保存庫。 這有助於您不跨環境共用機密，並在發生違規時減少威脅。

## <a name="backup"></a>Backup 

請確保在更新/刪除/創建保存庫中的物件時定期備份[保存庫](https://blogs.technet.microsoft.com/kv/2018/07/20/announcing-backup-and-restore-of-keys-secrets-and-certificates/)。

## <a name="turn-on-logging"></a>打開日誌記錄

[打開](key-vault-logging.md)保存庫的日誌記錄。 還設置警報。

## <a name="turn-on-recovery-options"></a>打開恢復選項

1. 打開["虛刪除](key-vault-ovw-soft-delete.md)"。
2. 如果要防止強制刪除機密/保存庫，即使在虛刪除打開後，也打開清除保護。
