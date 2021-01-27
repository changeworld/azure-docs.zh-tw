---
title: 翻譯自訂-翻譯工具
titleSuffix: Azure Cognitive Services
description: 以慣用的術語和樣式，使用 Microsoft Translator Hub 建置您自己的電腦翻譯系統。
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: lajanuar
ms.openlocfilehash: 0930024a791fe8b76c90e8ab4249a070b22a1c16
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98898031"
---
# <a name="customize-your-text-translations"></a>自訂文字翻譯

自訂翻譯是 Translator 服務的功能，可讓使用者在使用翻譯工具轉譯文字時，自訂 Microsoft Translator 的 advanced 類神經機器翻譯 (第3版) 。

此功能在搭配使用[認知服務語音](../speech-service/index.yml)時，也可用來自訂語音翻譯。

## <a name="custom-translator"></a>自訂翻譯工具

利用自訂翻譯工具，您可以建置類神經翻譯系統，以了解您自己的企業和產業中使用的術語。 然後，自訂的翻譯系統會整合到現有的應用程式、工作流程和網站。

### <a name="how-does-it-work"></a>運作方式

使用您先前翻譯過的檔 (傳單、網頁、檔等 ) 建立轉譯系統，以反映您的領域專屬術語和樣式（優於標準翻譯系統）。 使用者可以上傳 TMX、XLIFF、TXT、DOCX 和 XLSX 文件。  

系統也接受在文件層級上為平行處理、但在句子層級上尚未對齊的資料。 若使用者可以存取使用多種語言、但位於在不同文件中的相同內容版本，自訂翻譯工具就能夠自動比對文件之間的句子。  系統還可以使用其中一或二種語言的單語資料，補充平行訓練資料來改善翻譯。

接著，您可以使用類別參數定期呼叫轉譯程式來使用自訂系統。

假設有適當的訓練資料類型和數量，透過使用自訂翻譯工具預期翻譯品質取得介於 5 到 10 之間或甚至更多的 BLEU 點數並不罕見。

如需根據可用資料之各種自訂層級的詳細資料，請參閱[自訂翻譯工具使用者指南](./custom-translator/overview.md)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [使用自訂翻譯工具設定自訂的語言系統](./custom-translator/overview.md)
