---
title: 斯瓦格文檔 - 語音服務
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
ms.openlocfilehash: fcc43caf895dadfaf832a47c3254f9b828bcb71a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77430800"
---
# <a name="swagger-documentation"></a>Swagger 文件

語音服務提供一個 Swagger 規範，用於與用於導入資料、創建模型、測試模型準確性、創建自訂終結點、排隊批次處理轉錄和管理訂閱的一些 REST API 進行交互。 透過自訂語音入口網站提供的大部分作業都可以使用這些 API 以程式設計方式來完成。

> [!NOTE]
> 支援語音轉換文字和文字轉換語音的作業作為 REST API，然後在 Swagger 規格中進行記錄。

## <a name="generating-code-from-the-swagger-specification"></a>從 Swagger 規格產生程式碼

[Swagger 規格](https://cris.ai/swagger/ui/index)具有可讓您快速測試各種路徑的選項。 但是，有時需要為所有路徑產生程式碼，建立一個未來解決方案可以作為基礎的單一呼叫程式庫。 讓我們看看產生 Python 程式庫的流程。

您需要將 Swagger 設置為與語音服務訂閱相同的區域。 您可以在"語音服務"資源下的 Azure 門戶中確認區域。 有關支援區域的完整清單，請參閱[區域](regions.md)。

1. 移至 https://editor.swagger.io。
2. 按一下 [檔案]****，然後按一下 [匯入]****
3. 輸入誇張的 URL，包括語音服務訂閱的區域`https://<your-region>.cris.ai/docs/v2.0/swagger`
4. 按一下 [產生用戶端]****，然後選取 [Python]
5. 儲存用戶端程式庫

您可以使用在[GitHub 上使用語音服務示例](https://aka.ms/csspeech/samples)生成的 Python 庫。

## <a name="reference-docs"></a>參考文件

* [REST（搖擺）：批量轉錄和自訂](https://westus.cris.ai/swagger/ui/index)
* [REST API：語音到文本](rest-speech-to-text.md)
* [REST API：文本到語音](rest-text-to-speech.md)

## <a name="next-steps"></a>後續步驟

* [GitHub 上的語音服務示例](https://aka.ms/csspeech/samples)。
* [免費取得語音服務的訂用帳戶金鑰](get-started.md)
