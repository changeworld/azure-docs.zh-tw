---
title: 靜止資料的表單辨識器服務加密
titleSuffix: Azure Cognitive Services
description: Microsoft 提供 Microsoft 管理的加密金鑰，也可讓您使用自己的金鑰（稱為客戶管理的金鑰）來管理認知服務訂用帳戶， (CMK) 。 本文涵蓋表單辨識器的靜止資料加密，以及如何啟用和管理 CMK。
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: 86b15b0059c2e3466ef65daeb53780798b3882d0
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2020
ms.locfileid: "89079246"
---
# <a name="form-recognizer-encryption-of-data-at-rest"></a>靜止資料的表單辨識器加密

Azure 表單辨識器會在您將資料保存到雲端時，自動將資料加密。 表單辨識器加密可保護您的資料，並協助您符合組織的安全性和合規性承諾。

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> 客戶管理的金鑰僅提供11月 11 2020 日之後建立的可用資源。 若要搭配使用 CMK 與表單辨識器，您將需要建立新的表單辨識器資源。 建立資源之後，您可以使用 Azure Key Vault 來設定受控識別。

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>後續步驟

* [表單辨識器客戶管理的金鑰要求表單](https://aka.ms/cogsvc-cmk)
* [深入瞭解 Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)