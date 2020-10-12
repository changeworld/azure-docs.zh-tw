---
title: 開始使用-Translator
titleSuffix: Azure Cognitive Services
description: 本文將說明如何註冊 Azure 認知服務 Translator 並取得訂用帳戶金鑰。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: swmachan
ms.openlocfilehash: a9050b85c974a3c04ca717e8fe3d5188c1bf7a40
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88244307"
---
# <a name="how-to-sign-up-for-translator"></a>如何註冊翻譯工具

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

- 還沒有帳戶嗎？ 您可以建立[免費帳戶](https://azure.microsoft.com/free/cognitive-services)進行實驗，完全不需付費。
- 已經有帳戶了嗎? [登入](https://ms.portal.azure.com/)

## <a name="create-a-subscription-for-translator"></a>建立 Translator 的訂用帳戶

登入入口網站之後，您可以依照下列方式建立對翻譯工具的訂用帳戶：

1. 選取 [+ 建立資源]。
1. 在 [ **搜尋 Marketplace** 搜尋] 方塊中，輸入 **Translator** ，然後從結果中選取。
1. 選取 [建立]**** 定義訂用帳戶的詳細資料。
1. 從 [定價層]**** 清單中，選取最符合您需求的定價層。
    1. 每個訂用帳戶都有一個免費層。 免費層與付費方案的特色與功能都相同，但是不會過期。
    1. 您的帳戶只能有一個免費的訂用帳戶。
1. 選取 [建立]**** 即可完成建立訂用帳戶。

## <a name="authentication-key"></a>驗證金鑰

當您註冊翻譯工具時，您會取得訂用帳戶獨有的個人化存取金鑰。 每次呼叫翻譯工具都需要此金鑰。

1. 先選取適當的訂用帳戶，擷取驗證金鑰。
1. 在訂用帳戶詳細資料的 [資源管理]**** 區段中，選取 [金鑰]****。
1. 複製訂用帳戶所列的其中一個金鑰。

## <a name="learn-test-and-get-support"></a>了解、測試和取得支援

- [GitHub 上的範例程式碼](https://github.com/MicrosoftTranslator)
- [Microsoft Translator 支援論壇](https://www.aka.ms/TranslatorForum)

Microsoft 翻譯工具通常會在驗證訂用帳戶狀態之前讓您最初的幾個要求通過。 如果前幾個翻譯工具要求成功，則呼叫會失敗，錯誤回應會指出問題。 請記錄 API 回應，以便檢視原因。

## <a name="pricing-options"></a>定價選項

- [翻譯工具](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)

## <a name="customization"></a>自訂

從一般的 Microsoft Translator 神經機器翻譯系統開始，使用自訂翻譯工具來自訂您的翻譯，以及針對您自己的術語和風格建立微調的翻譯系統。 [深入了解](customization.md)

## <a name="additional-resources"></a>其他資源

- [開始使用 Azure (3 分鐘影片)](https://azure.microsoft.com/get-started/?b=16.24)
- [如何按發票付款](https://azure.microsoft.com/pricing/invoicing/)
