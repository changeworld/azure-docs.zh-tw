---
title: Swagger 檔-語音服務
titleSuffix: Azure Cognitive Services
description: Swagger 文件可用於為多種程式設計語言自動產生 SDK。 Swagger 支援我們服務中的所有作業
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: reference
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 30e8224b6cb757f044a5eac598d834cee838391e
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/01/2020
ms.locfileid: "91629976"
---
# <a name="swagger-documentation"></a>Swagger 文件

語音服務提供了一種 Swagger 規格，可與一些用來匯入資料、建立模型、測試模型精確度、建立自訂端點、將批次轉譯排入佇列，以及管理訂閱的 REST Api 互動。 透過自訂語音入口網站提供的大部分作業都可以使用這些 API 以程式設計方式來完成。

> [!NOTE]
> 支援語音轉換文字和文字轉換語音的作業作為 REST API，然後在 Swagger 規格中進行記錄。

## <a name="generating-code-from-the-swagger-specification"></a>從 Swagger 規格產生程式碼

[Swagger 規格](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)具有可讓您快速測試各種路徑的選項。 但是，有時需要為所有路徑產生程式碼，建立一個未來解決方案可以作為基礎的單一呼叫程式庫。 讓我們看看產生 Python 程式庫的流程。

您必須將 Swagger 設定為與您的語音服務訂用帳戶相同的區域。 您可以在語音服務資源下的 Azure 入口網站中確認您的區域。 如需支援區域的完整清單，請參閱 [區域](regions.md)。

1. 在瀏覽器中，移至您區域的 Swagger 規格 `https://<your-region>.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0` 。 在該頁面上，按一下 [ **API 定義**]，然後按一下 [ **Swagger**]。 複製所顯示頁面的 URL。
1. 在新的瀏覽器中，移至 https://editor.swagger.io
1. 按一下 [檔案] **，按一下 [** 匯 **入 url**]，貼上 Url，然後按一下 **[確定]**。
1. 按一下 [ **產生用戶端** ]，然後選取 [ **python**]。 用戶端程式庫會下載到您的電腦。

您可以使用您在 [GitHub 上以語音服務範例](https://aka.ms/csspeech/samples)產生的 Python 程式庫。

## <a name="reference-docs"></a>參考文件

* [REST (Swagger) ：批次轉譯和自訂](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)
* [REST API：語音轉文字](rest-speech-to-text.md)
* [REST API：文字轉語音](rest-text-to-speech.md)

## <a name="next-steps"></a>後續步驟

* [GitHub 上的語音服務範例](https://aka.ms/csspeech/samples)。
* [免費取得語音服務的訂用帳戶金鑰](overview.md#try-the-speech-service-for-free)
