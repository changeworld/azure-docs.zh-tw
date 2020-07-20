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
ms.openlocfilehash: 3b975d9f0953c874c627e30b382ad8c7fa7cfc09
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/03/2020
ms.locfileid: "84307793"
---
## <a name="about-cognitive-services-encryption"></a>關於認知服務加密

資料會使用[FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2)相容[的256位 AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)加密來加密和解密。 加密和解密都是透明的，這表示會為您管理加密和存取。 您的資料預設是安全的，而且您不需要修改程式碼或應用程式，就能利用加密。

## <a name="about-encryption-key-management"></a>關於加密金鑰管理

根據預設，您的訂用帳戶會使用 Microsoft 管理的加密金鑰。 不過，您也可以使用自己的加密金鑰來管理您的訂用帳戶。 客戶管理的金鑰（CMK）也稱為「自備金鑰」（BYOK），可提供更大的彈性來建立、輪替、停用及撤銷存取控制。 您也可以審核用來保護資料的加密金鑰。