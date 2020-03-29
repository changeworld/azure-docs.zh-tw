---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 05/06/2019
ms.author: wolfma
ms.openlocfilehash: a1de313245e826511a7e9c864165ac44bcb48530
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80234165"
---
### <a name="standard-and-neural-voices"></a>標準和神經語音

使用此表可按區域/終結點確定標準和神經語音的可用性：

| 區域 | 端點 | 標準語音 | 神經聲音 |
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

### <a name="custom-voices"></a>自訂語音

如果已創建自訂語音字體，請使用已創建的終結點。 您還可以使用下面列出的終結點，`{deploymentId}`將 替換為語音模型的部署 ID。

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
| 北歐 | `https://northeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 美國中南部 | `https://southcentralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 東南亞 | `https://southeastasia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 英國南部 | `https://uksouth.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 西歐 | `https://westeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 美國西部 | `https://westus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 美國西部 2 | `https://westus2.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
