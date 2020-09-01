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
ms.openlocfilehash: 90d5aae559a317ed509d04c8db3310ff8a5de026
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2020
ms.locfileid: "89069899"
---
## <a name="about-cognitive-services-encryption"></a>關於認知服務加密

資料會使用 [FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2) 相容 [的256位 AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 加密進行加密和解密。 加密和解密是透明的，這表示會為您管理加密和存取。 根據預設，您的資料會受到保護，因此您無須修改程式碼或應用程式來利用加密功能。

## <a name="about-encryption-key-management"></a>關於加密金鑰管理

根據預設，您的訂用帳戶會使用由 Microsoft 管理的加密金鑰。 您也可以選擇使用您自己的金鑰來管理訂用帳戶，稱為客戶管理的金鑰 (CMK) 。 CMK 提供更大的彈性來建立、輪替、停用及撤銷存取控制。 您也可稽核用來保護資料的加密金鑰。 如果已針對您的訂用帳戶設定 CMK，則會提供雙重加密，以提供第二層保護，並可讓您透過 Azure Key Vault 控制加密金鑰。