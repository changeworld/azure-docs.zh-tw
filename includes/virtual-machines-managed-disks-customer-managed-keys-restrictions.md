---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 08/24/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 75acbb30c2bf811b7ae72d6939b9f164554fdd32
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2021
ms.locfileid: "98859546"
---
- 僅支援大小為2048位、3072位和4096位的 [軟體和 HSM RSA 金鑰](../articles/key-vault/keys/about-keys.md) ，不支援其他金鑰或大小。
    - [HSM](../articles/key-vault/keys/hsm-protected-keys.md) 金鑰需要 Azure 金鑰保存庫的 **premium** 層。
- 從自訂映像 (使用伺服器端加密和客戶管理的金鑰加密) 建立的磁碟，必須使用相同的客戶管理的金鑰進行加密，而且必須在相同的訂用帳戶中。
- 從磁碟 (使用伺服器端加密和客戶管理的金鑰加密) 建立的快照集，必須使用相同的客戶管理金鑰進行加密。
- 與客戶管理金鑰相關的所有資源 (Azure Key Vault、磁碟加密集、VM、磁碟和快照集) 必須位於相同的訂用帳戶和區域中。
- 以客戶管理的金鑰加密的磁片、快照集和映射無法移至另一個資源群組和訂用帳戶。
- 目前或先前使用 Azure 磁碟加密加密的受控磁片無法使用客戶管理的金鑰進行加密。
- 每個訂用帳戶最多隻能為每個區域建立1000個磁片加密集。
- 如需將客戶管理的金鑰與共用映像資源庫搭配使用的詳細資訊，請參閱[預覽：使用客戶管理的金鑰加密映像](../articles/virtual-machines/image-version-encryption.md)。
