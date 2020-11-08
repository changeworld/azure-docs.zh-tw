---
title: 靜態資料的個人化工具服務加密
titleSuffix: Azure Cognitive Services
description: Microsoft 提供 Microsoft 管理的加密金鑰，也可讓您使用自己的金鑰（稱為客戶管理的金鑰）來管理認知服務訂用帳戶， (CMK) 。 本文涵蓋個人化工具待用資料加密的內容，以及如何啟用和管理 CMK。
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: 1a27199930587c1a096dd99462ebd0c9d65054ee
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2020
ms.locfileid: "94369353"
---
# <a name="personalizer-service-encryption-of-data-at-rest"></a>靜態資料的個人化工具服務加密

當您將資料保存到雲端時，個人化工具服務會自動將您的資料加密。 個人化工具服務加密可保護您的資料，並協助您符合組織的安全性和合規性承諾。

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> 客戶管理的金鑰僅適用于 E0 定價層。 若要要求使用客戶管理金鑰的能力，請填寫並提交 [個人化工具服務 Customer-Managed 金鑰要求表單](https://aka.ms/cogsvc-cmk)。 大約需要3-5 個工作天的時間，才會收到要求的狀態。 視需求而定，您可能會被放入佇列中，並在可用的空間獲得核准。 核准使用 CMK 搭配個人化工具服務之後，您將需要建立新的個人化工具資源，並選取 E0 作為定價層。 一旦建立了具有 E0 定價層的個人化工具資源之後，您就可以使用 Azure Key Vault 來設定受控識別。

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>後續步驟

* [個人化工具 Service Customer-Managed 金鑰要求表單](https://aka.ms/cogsvc-cmk)
* [深入瞭解 Azure Key Vault](../../key-vault/general/overview.md)