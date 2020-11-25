---
title: 什麼是自訂翻譯工具？
titleSuffix: Azure Cognitive Services
description: 自訂翻譯工具所提供的功能，類似於 Microsoft Translator Hub 針對統計機器翻譯 (SMT) 所提供的功能，但前者專供類神經機器翻譯 (NMT) 系統來使用。
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 12/09/2019
ms.author: swmachan
ms.topic: overview
ms.openlocfilehash: 6a7301fd878eb65e4c1dd540275611bcf9348afc
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/22/2020
ms.locfileid: "95241738"
---
# <a name="what-is-custom-translator"></a>什麼是自訂翻譯工具？

[自訂翻譯工具](https://portal.customtranslator.azure.ai)是 Microsoft Translator 服務的功能，可讓翻譯工具企業、應用程式開發人員和語言服務提供者建置自訂的類神經機器翻譯 (NMT) 系統。 自訂的翻譯系統會順暢地整合到現有應用程式、工作流程和網站。

使用[自訂翻譯工具](https://portal.customtranslator.azure.ai)所建置的翻譯系統，可透過相同的雲端式、[安全](https://cognitive.uservoice.com/knowledgebase/articles/1147537-api-and-customization-confidentiality)、高效能、具有高度擴充性的 Microsoft Translator [文字 API V3](../reference/v3-0-translate.md?tabs=curl)來使用，其每天支援數十億次翻譯。

自訂翻譯工具支援三十幾種以上的語言，並會直接對應至 NMT 可用的語言。 如需完整清單，請參閱 [Microsoft Translator 語言](../language-support.md#customization)。

## <a name="features"></a>特性

自訂翻譯工具提供了不同的功能，來供您建置自訂翻譯系統並於後續存取該系統。

|功能  |描述  |
|---------|---------|
|[利用類神經機器翻譯技術](https://www.microsoft.com/translator/blog/2016/11/15/microsoft-translator-launching-neural-network-based-translations-for-all-its-speech-languages/)     |  利用自訂翻譯工具所提供的類神經機器翻譯 (NMT) 來改善您的翻譯。       |
|[建置知道您商務術語的系統](what-are-parallel-documents.md)     |  使用平行處理的文件來自訂和建置翻譯系統，其了解您自己的企業和產業中所使用的術語。       |
|[使用字典來建置模型](what-is-dictionary.md)     |   如果您沒有訓練資料集，則可以只使用字典資料來訓練模型。       |
|[與其他人共同作業](how-to-manage-settings.md#share-your-workspace)     |   藉由與不同的人共享工作，來與您的小組共同作業。     |
|[存取自訂翻譯模型](../reference/v3-0-translate.md?tabs=curl)     |  現有應用程式/程式隨時可以透過 Microsoft Translator 文字 API V3 存取您的自訂轉譯模型。       |

## <a name="get-better-translations"></a>獲得更好的翻譯

Microsoft Translator 在 2016 年發行了[類神經機器翻譯 (NMT)](https://www.microsoft.com/translator/blog/2016/11/15/microsoft-translator-launching-neural-network-based-translations-for-all-its-speech-languages/)。 和業界標準的[統計機器翻譯 (SMT)](https://en.wikipedia.org/wiki/Statistical_machine_translation) 技術相比，NMT 的翻譯品質有了長足進步。 NMT 會先對整個句子的背景有更好的了解，再翻譯整個句子，因此翻譯品質更好、發音更像真人，且翻譯的內容會更通順。 [自訂翻譯工具](https://portal.customtranslator.azure.ai)會提供 NMT 而讓您的自訂模型產生更好的翻譯品質。

您可以使用先前翻譯好的文件來建置翻譯系統。 這些文件中會有優於標準翻譯系統的領域專屬術語和風格。 使用者可以上傳 ALIGN、PDF、LCL、HTML、HTM、XLF、TMX、XLIFF、TXT、DOCX 和 XLSX 文件。

自訂翻譯工具也可接受在文件層級進行處理的資料，以便更有效地收集和準備資料。 若使用者可以存取使用多種語言、但位於在不同文件中的相同內容版本，自訂翻譯工具就能夠自動比對文件之間的句子。

如果有提供適當的訓練資料類型和數量，則會經常看到使用自訂翻譯工具能獲得 5 到 10 分的 [BLEU 分數](what-is-bleu-score.md)的情況。

## <a name="be-productive-and-cost-effective"></a>具生產力且符合成本效益

使用[自訂翻譯工具](https://portal.customtranslator.azure.ai)時，不需要任何程式設計技能就能訓練和部署自訂系統。

使用安全的[自訂翻譯工具](https://portal.customtranslator.azure.ai)入口網站，使用者便可透過直覺式的使用者介面，來上傳訓練資料、對系統進行訓練、測試系統，然後將系統部署至生產環境。 在幾小時內 (實際時間取決於訓練資料大小)，系統便可供大規模使用。

透過[專用 API](https://custom-api.cognitive.microsofttranslator.com/swagger/) (目前處於預覽狀態)，也能以程式設計方式存取[自訂翻譯工具](https://portal.customtranslator.azure.ai)。 此 API 可讓使用者透過自己的應用程式或 WebService，定期管理訓練的建立或更新。

使用自訂模型來翻譯內容的成本，取決於使用者的「翻譯工具文字 API」定價層。 如需定價層的詳細資料，請參閱認知服務[翻譯工具文字 API 定價網頁](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)。

## <a name="securely-translate-anytime-anywhere-on-all-your-apps-and-services"></a>隨時隨地在所有應用程式和服務上安全地進行翻譯

透過標準 REST 技術，自訂系統可供透過 Microsoft Translator 文字 API 順暢地進行存取並整合到任何產品或商務工作流程中，且不受裝置限制。

## <a name="next-steps"></a>後續步驟

- 閱讀[定價詳細資料](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)。

- 使用[快速入門](quickstart-build-deploy-custom-model.md)，了解如何在自訂翻譯工具中建置翻譯模型。
