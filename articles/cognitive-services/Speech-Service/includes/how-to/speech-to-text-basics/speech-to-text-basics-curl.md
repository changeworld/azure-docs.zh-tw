---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 10/09/2020
ms.author: v-jawe
ms.openlocfilehash: 0964872d03d6e321d25d51a18edbb4a6f0be8a4f
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94424996"
---
在本快速入門中，您將了解如何使用語音服務和 cURL 將語音轉換成文字。

如需有關語音轉換文字概念的高階探討，請參閱[概觀](../../../speech-to-text.md)文章。

## <a name="prerequisites"></a>必要條件

本文假設您具有 Azure 帳戶和語音服務訂用帳戶。 如果您沒有該帳戶和訂用帳戶，請[免費試用語音服務](../../../overview.md#try-the-speech-service-for-free)。

## <a name="convert-speech-to-text"></a>語音轉換文字

在命令提示字元中執行下列命令。 您必須將下列值插入命令中。
- 您的語音服務訂用帳戶金鑰。
- 您的語音服務區域。
- 輸入音訊檔案路徑。 您可以使用[文字轉換語音](../../../get-started-text-to-speech.md)來產生音訊檔案。

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speech-to-text.sh" id="request":::

您應該會收到類似下列的回應。

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speech-to-text.sh" id="response":::

如需詳細資訊，請參閱[語音轉換文字 REST API 參考](../../../rest-speech-to-text.md)。