---
title: 靜態資料的內容仲裁加密
titleSuffix: Azure Cognitive Services
description: 靜態資料的內容仲裁加密。
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: 1401108a594e30790e842ec379724603f11d493f
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2020
ms.locfileid: "92913699"
---
# <a name="content-moderator-encryption-of-data-at-rest"></a>靜態資料的內容仲裁加密

當資料保存到雲端時，內容仲裁會自動將您的資料加密，以協助滿足您組織的安全性和合規性目標。

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> 客戶管理的金鑰僅適用于 E0 定價層。 若要要求使用客戶管理金鑰的能力，請填寫並提交 [內容仲裁 Customer-Managed 金鑰要求表單](https://aka.ms/cogsvc-cmk)。 大約需要3-5 個工作天的時間，才會收到要求的狀態。 視需求而定，您可能會被放入佇列中，並在可用的空間獲得核准。 核准使用 CMK 與內容仲裁服務之後，您將需要建立新的內容仲裁資源，並選取 E0 作為定價層。 一旦建立了具有 E0 定價層的內容仲裁資源之後，您就可以使用 Azure Key Vault 來設定受控識別。

[!INCLUDE [cognitive-services-cmk](../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="enable-data-encryption-for-your-content-moderator-team"></a>為您的內容仲裁小組啟用資料加密

若要為內容仲裁者審核小組啟用資料加密，請參閱 [快速入門：在網路上試用內容仲裁](quick-start.md#create-a-review-team)。  

> [!NOTE]
> 您必須提供具有內容仲裁 E0 定價層的 _資源識別碼_ 。

## <a name="next-steps"></a>下一步

* 如需支援 CMK 的服務完整清單，請參閱 [客戶管理的認知服務金鑰](../encryption/cognitive-services-encryption-keys-portal.md)
* [什麼是 Azure Key Vault](../../key-vault/general/overview.md)？
* [認知服務 Customer-Managed 金鑰要求表單](https://aka.ms/cogsvc-cmk)