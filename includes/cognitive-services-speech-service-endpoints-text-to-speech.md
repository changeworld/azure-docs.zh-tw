---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 05/06/2019
ms.author: wolfma
ms.openlocfilehash: 650ce0cc9586118b30593767c6a3ddb92f494ac3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "95972638"
---
### <a name="standard-and-neural-voices"></a>標準和類神經語音

您可以使用此資料表來依區域/端點判斷標準和類神經語音的可用性：

| 區域 | 端點 | 標準語音 | 類神經語音 |
|--------|----------|-----------------|---------------|
| 澳大利亞東部 | `https://australiaeast.tts.speech.microsoft.com/cognitiveservices/v1` | 是 | 是 |
| 巴西南部 | `https://brazilsouth.tts.speech.microsoft.com/cognitiveservices/v1` | 是 | 否 |
| 加拿大中部 | `https://canadacentral.tts.speech.microsoft.com/cognitiveservices/v1` | 是 | 是 |
| 美國中部 | `https://centralus.tts.speech.microsoft.com/cognitiveservices/v1` | 是 | 否 |
| 東亞 | `https://eastasia.tts.speech.microsoft.com/cognitiveservices/v1` | 是 | 否 |
| 美國東部 | `https://eastus.tts.speech.microsoft.com/cognitiveservices/v1` | 是 | 是 |
| 美國東部 2 | `https://eastus2.tts.speech.microsoft.com/cognitiveservices/v1` | 是 | 否 |
| 法國中部 | `https://francecentral.tts.speech.microsoft.com/cognitiveservices/v1` | 是 | 否 |
| 印度中部 | `https://centralindia.tts.speech.microsoft.com/cognitiveservices/v1` | 是 | 是 |
| 日本東部 | `https://japaneast.tts.speech.microsoft.com/cognitiveservices/v1` | 是 | 否 |
| 日本西部 | `https://japanwest.tts.speech.microsoft.com/cognitiveservices/v1` | 是 | 否 |
| 南韓中部 | `https://koreacentral.tts.speech.microsoft.com/cognitiveservices/v1` | 是 | 否 |
| 美國中北部 | `https://northcentralus.tts.speech.microsoft.com/cognitiveservices/v1` | 是 | 否 |
| 北歐 | `https://northeurope.tts.speech.microsoft.com/cognitiveservices/v1` | 是 | 否 |
| 美國中南部 | `https://southcentralus.tts.speech.microsoft.com/cognitiveservices/v1` | 是 | 是 |
| 東南亞 | `https://southeastasia.tts.speech.microsoft.com/cognitiveservices/v1` | 是 | 是 |
| 英國南部 | `https://uksouth.tts.speech.microsoft.com/cognitiveservices/v1` | 是 | 是 |
| 西歐 | `https://westeurope.tts.speech.microsoft.com/cognitiveservices/v1` | 是 | 是 |
| 美國西部 | `https://westus.tts.speech.microsoft.com/cognitiveservices/v1` | 是 | 否 |
| 美國西部 2 | `https://westus2.tts.speech.microsoft.com/cognitiveservices/v1` | 是 | 是 |

> [!TIP]
> [預覽版](../articles/cognitive-services/Speech-Service/language-support.md#neural-voices-in-preview) 中的語音僅適用于下列3個區域：美國東部、西歐和東南亞。

### <a name="custom-voices"></a>自訂語音

如果您已建立自訂語音字型，請使用您所建立的端點。 您也可以使用下面所列的端點，將取代為 `{deploymentId}` 語音模型的部署識別碼。

| 區域 | 端點 |
|--------|----------|
| 澳大利亞東部 | `https://australiaeast.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 巴西南部 | `https://brazilsouth.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 加拿大中部 | `https://canadacentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 美國中部 | `https://centralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 東亞 | `https://eastasia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 美國東部 | `https://eastus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 美國東部 2 | `https://eastus2.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 法國中部 | `https://francecentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 印度中部 | `https://centralindia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 日本東部 | `https://japaneast.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 日本西部 | `https://japanwest.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 南韓中部 | `https://koreacentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 美國中北部 | `https://northcentralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 歐洲北部 | `https://northeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 美國中南部 | `https://southcentralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 東南亞 | `https://southeastasia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 英國南部 | `https://uksouth.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 西歐 | `https://westeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 美國西部 | `https://westus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 美國西部 2 | `https://westus2.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
