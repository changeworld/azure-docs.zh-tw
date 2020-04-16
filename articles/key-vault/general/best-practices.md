---
title: 使用金鑰保管庫的最佳做法 - Azure 密鑰保管庫 |微軟文件
description: 本文件介紹了使用金鑰保管庫的一些最佳實踐
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-key-vault
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 03/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 57bb897978f47a66adebac069d8892d596ba78f5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430860"
---
# <a name="best-practices-to-use-key-vault"></a>使用金鑰保存的庫的最佳做法

## <a name="control-access-to-your-vault"></a>控制對保管庫的存取

Azure Key Vault 是用來保護加密金鑰和祕密 (例如憑證、連接字串和密碼) 的雲端服務。 由於這項資料相當敏感且攸關業務，所以您必須藉由只允許獲得授權的應用程式和使用者，來保護金鑰保存庫的存取權。 [本文](secure-your-key-vault.md)概述了密鑰保管庫訪問模型。 文中會說明驗證和授權，並說明如何保護金鑰保存庫的存取權。

控制對保管庫的訪問時的建議如下:
1. 鎖定對訂閱、資源群組和金鑰保管庫 (RBAC) 的存取
2. 為每個保存庫建立存取原則
3. 使用最低權限存取主體權限
4. 開啟防火牆與[VNET 服務終結點](overview-vnet-service-endpoints.md)。

## <a name="use-separate-key-vault"></a>使用單獨的金鑰保存庫

我們的建議是,每個環境(開發、生產前和生產)每個應用程式使用保管庫。 這有助於您不跨環境共享機密,並在發生違規時減少威脅。

## <a name="backup"></a>Backup

請確定在更新/刪除/建立保管庫中的物件時定期備份[保管庫](https://blogs.technet.microsoft.com/kv/2018/07/20/announcing-backup-and-restore-of-keys-secrets-and-certificates/)。

## <a name="turn-on-logging"></a>開啟紀錄記錄

[開啟紀錄記錄](logging.md))為您的保險庫。 還設置警報。

## <a name="turn-on-recovery-options"></a>開啟回復選項

1. 打開[「軟刪除](overview-soft-delete.md)」。。
2. 如果要防止強制刪除機密/保管庫,即使在軟刪除打開后,也打開清除保護。
