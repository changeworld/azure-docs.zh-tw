---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/10/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: c303527c7411ead585e70fc2e31db4dd2d35e996
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2020
ms.locfileid: "86259824"
---
- 僅支援2048位、3072位和4096位大小的[軟體和 HSM RSA 金鑰](../articles/key-vault/keys/about-keys.md)，沒有其他金鑰或大小。
    - [HSM](../articles/key-vault/keys/hsm-protected-keys.md)金鑰需要 Azure 金鑰保存庫的**premium**層。
- 從自訂映像 (使用伺服器端加密和客戶管理的金鑰加密) 建立的磁碟，必須使用相同的客戶管理的金鑰進行加密，而且必須在相同的訂用帳戶中。
- 從磁碟 (使用伺服器端加密和客戶管理的金鑰加密) 建立的快照集，必須使用相同的客戶管理金鑰進行加密。
- 與客戶管理金鑰相關的所有資源 (Azure Key Vault、磁碟加密集、VM、磁碟和快照集) 必須位於相同的訂用帳戶和區域中。
- 使用客戶管理的金鑰加密的磁碟、快照集和映像無法移至另一個訂用帳戶。
- 使用客戶管理的金鑰加密的受控磁碟，也無法使用 Azure 磁碟加密進行加密。
- 每個訂用帳戶的每個區域最多隻能建立50個磁片加密集。
- 如需將客戶管理的金鑰與共用映像資源庫搭配使用的詳細資訊，請參閱[預覽：使用客戶管理的金鑰加密映像](../articles/virtual-machines/image-version-encryption.md)。
