---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 10/09/2020
ms.author: v-jawe
ms.openlocfilehash: 029cb1e59876eb01c609ef8b7ba0412ed35a2a1b
ms.sourcegitcommit: 541bb46e38ce21829a056da880c1619954678586
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2020
ms.locfileid: "91940733"
---
在本快速入門中，您將了解如何使用語音服務和 cURL 將文字轉換成語音。

如需有關文字轉換語音概念的高階探討，請參閱[概觀](../../../text-to-speech.md)文章。

## <a name="prerequisites"></a>必要條件

本文假設您具有 Azure 帳戶和語音服務訂用帳戶。 如果您沒有該帳戶和訂用帳戶，請[免費試用語音服務](../../../overview.md#try-the-speech-service-for-free)。

## <a name="convert-text-to-speech"></a>將文字轉換成語音

在命令提示字元中執行下列命令。 您必須將下列值插入命令中。
- 您的語音服務訂用帳戶金鑰。
- 您的語音服務區域。

您也可能想要變更下列值。
- `X-Microsoft-OutputFormat` 標頭值，可控制音訊輸出格式。 您可以在[文字轉換語音 REST API 參考](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-text-to-speech#audio-outputs)中找到支援的音訊輸出格式清單。
- 輸出語音。 若要取得語音端點可用的語音清單，請參閱下一節。
- 輸出檔案。 在此範例中，我們會將伺服器的回應導向至名為 `output.wav` 的檔案。

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/text-to-speech.sh":::

## <a name="list-available-voices-for-your-speech-endpoint"></a>列出語音端點可用的語音

若要列出語音端點可用的語音，請執行下列命令。

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/get-voices.sh" id="request":::

您應該會收到類似下列的回應。

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/get-voices.sh" id="response":::
