---
title: 開始使用 - 翻譯工具文字 API
titleSuffix: Azure Cognitive Services
description: 本文將說明如何註冊 Azure 認知服務翻譯工具文字 API 並取得訂用帳戶金鑰。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.custom: seodec18
ms.openlocfilehash: cfae5318213e8af164c8d0cc9ed378705a5644b0
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "73835696"
---
# <a name="how-to-sign-up-for-the-translator-text-api"></a>如何註冊翻譯工具文字 API

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

- 還沒有帳戶嗎？ 您可以建立[免費帳戶](https://azure.microsoft.com/free/)進行實驗，完全不需付費。
- 已經有帳戶了嗎? [登入](https://ms.portal.azure.com/)

## <a name="create-a-subscription-to-the-translator-text-api"></a>建立翻譯工具文字 API 的訂用帳戶

登入入口網站之後，您可以建立翻譯工具文字 API 的訂用帳戶，如下所示：

1. 選取 [ **+ 建立資源**]。
1. 在 [搜尋 Marketplace]**** 搜尋方塊中，輸入**翻譯工具文字**，然後從結果中選取它。
1. 選取 [建立]**** 定義訂用帳戶的詳細資料。
1. 從 [定價層]**** 清單中，選取最符合您需求的定價層。
    1. 每個訂用帳戶都有一個免費層。 免費層與付費方案的特色與功能都相同，但是不會過期。
    1. 您的帳戶只能有一個免費的訂用帳戶。
1. 選取 [建立]**** 即可完成建立訂用帳戶。

## <a name="authentication-key"></a>驗證金鑰

當您註冊翻譯工具文字時，就是取得訂用帳戶獨有的一個個人化存取金鑰。 每次呼叫翻譯工具文字 API 時，都需要這個金鑰。

1. 先選取適當的訂用帳戶，擷取驗證金鑰。
1. 在訂用帳戶詳細資料的 [資源管理]**** 區段中，選取 [金鑰]****。
1. 複製訂用帳戶所列的其中一個金鑰。

## <a name="learn-test-and-get-support"></a>了解、測試和取得支援

- [GitHub 上的範例程式碼](https://github.com/MicrosoftTranslator)
- [Microsoft Translator 支援論壇](https://www.aka.ms/TranslatorForum)

Microsoft 翻譯工具通常會在驗證訂用帳戶狀態之前讓您最初的幾個要求通過。 如果前幾個 Microsoft 翻譯工具 API 要求成功，但後續的呼叫失敗，錯誤回應會指出問題。 請記錄 API 回應，以便檢視原因。

## <a name="pricing-options"></a>定價選項

- [Translator Text API](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)

## <a name="customization"></a>自訂

從一般的 Microsoft Translator 神經機器翻譯系統開始，使用自訂翻譯工具來自訂您的翻譯，以及針對您自己的術語和風格建立微調的翻譯系統。 [深入了解](customization.md)

## <a name="additional-resources"></a>其他資源

- [開始使用 Azure (3 分鐘影片)](https://azure.microsoft.com/get-started/?b=16.24)
- [如何按發票付款](https://azure.microsoft.com/pricing/invoicing/)
