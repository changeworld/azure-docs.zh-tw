---
title: 包含檔案
description: 包含檔案
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: egeaney
ms.custom: include
ms.openlocfilehash: 2a348827b1c992e0ef0a4592cc0b9c5c0fa0ca19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372404"
---
## <a name="about-cognitive-services-encryption"></a>關於認知服務加密

使用[FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2)相容[的 256 位 AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)加密對資料進行加密和解密。 加密和解密是透明的，這意味著加密和訪問是為您管理的。 預設情況下，您的資料是安全的，您無需修改代碼或應用程式就可以利用加密。

## <a name="about-encryption-key-management"></a>關於加密金鑰管理

預設情況下，您的訂閱使用 Microsoft 管理的加密金鑰。 如果使用的是支援客戶管理的金鑰的定價層，則可以在[Azure 門戶](https://portal.azure.com)的 **"加密"** 部分中查看資源的加密設定，如下圖所示。

![查看加密設定](../articles/cognitive-services/media/cognitive-services-encryption/encryptionblade.png)

對於僅支援 Microsoft 管理的加密金鑰的訂閱，您將沒有**加密**部分。

## <a name="customer-managed-keys-with-azure-key-vault"></a>使用 Azure 金鑰保存庫的客戶管理金鑰

還有一個選項可以用自己的金鑰管理訂閱。 客戶管理的金鑰 （CMK）也稱為"自帶金鑰 （BYOK"），為創建、旋轉、禁用和撤銷存取控制提供了更大的靈活性。 您還可以審核用於保護資料的加密金鑰。
