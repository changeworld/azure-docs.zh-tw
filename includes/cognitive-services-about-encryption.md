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
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/14/2020
ms.locfileid: "79372404"
---
## <a name="about-cognitive-services-encryption"></a>關於認知服務加密

資料會使用[FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2)相容[的256位 AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)加密來加密和解密。 加密和解密都是透明的，這表示會為您管理加密和存取。 您的資料預設是安全的，而且您不需要修改程式碼或應用程式，就能利用加密。

## <a name="about-encryption-key-management"></a>關於加密金鑰管理

根據預設，您的訂用帳戶會使用 Microsoft 管理的加密金鑰。 如果您使用的定價層支援客戶管理的金鑰，您可以在[Azure 入口網站](https://portal.azure.com)的 [**加密**] 區段中看到資源的加密設定，如下圖所示。

![查看加密設定](../articles/cognitive-services/media/cognitive-services-encryption/encryptionblade.png)

對於僅支援受 Microsoft 管理之加密金鑰的訂用帳戶，您將不會有**加密**區段。

## <a name="customer-managed-keys-with-azure-key-vault"></a>客戶管理的金鑰與 Azure Key Vault

您也可以選擇使用自己的金鑰來管理您的訂用帳戶。 客戶管理的金鑰（CMK）也稱為「自備金鑰」（BYOK），可提供更大的彈性來建立、輪替、停用及撤銷存取控制。 您也可以審核用來保護資料的加密金鑰。
