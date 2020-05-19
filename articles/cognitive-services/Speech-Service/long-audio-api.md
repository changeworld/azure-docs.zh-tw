---
title: 長音訊 API （預覽）-語音服務
titleSuffix: Azure Cognitive Services
description: 瞭解如何針對長格式文字轉換語音的非同步合成，進行長音訊 API 的設計。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: trbye
ms.openlocfilehash: 0e18fd0c52fd4090477599f53cd0ef0bc05855f2
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83587335"
---
# <a name="long-audio-api-preview"></a>長音訊 API （預覽）

「完整的音訊 API」是針對長格式文字轉換語音的非同步合成而設計（例如：「音訊書籍」、「新聞文章」和「檔」）。 此 API 不會即時傳回合成的音訊，而是預期您會輪詢回應，並取用輸出（從服務中取得），而不會有任何結果。 不同于語音 SDK 所使用的文字轉換語音 API，較長的音訊 API 可以建立超過10分鐘的合成音訊，使其適合用於發行者和音訊內容平臺。

較長音訊 API 的其他優點：

* 服務所傳回的合成語音會使用最佳的類神經語音。
* 不需要部署語音端點，因為它會以無即時批次模式合成語音。

> [!NOTE]
> 較長的音訊 API 現在支援[公用類神經語音](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#neural-voices)和[自訂神經語音](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-custom-voice#custom-neural-voices)。

## <a name="workflow"></a>工作流程

一般而言，使用完整的音訊 API 時，您會提交要合成的文字檔、輪詢狀態，然後如果狀態為 [成功]，則您可以下載音訊輸出。

此圖表提供工作流程的高階總覽。

![長音訊 API 工作流程圖表](media/long-audio-api/long-audio-api-workflow.png)

## <a name="prepare-content-for-synthesis"></a>準備內容以進行合成

準備您的文字檔時，請確定它：

* 這是純文字（.txt）或 SSML 文字（.txt）
* [使用位元組順序標記（BOM）](https://www.w3.org/International/questions/qa-utf8-bom.en#bom)編碼為 utf-8
* 是單一檔案，而不是 zip
* 針對 SSML 文字，包含超過400個字元的純文字或400個可[計費字元](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech#pricing-note)，且小於10000個段落
  * 若為純文字，則會叫用**Enter/Return** -View[純文字輸入範例](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/en-US.txt)來分隔每個段落
  * 針對 SSML 文字，每個 SSML 片段都會被視為一個段落。 SSML 片段應以不同段落分隔-View [SSML 文字輸入範例](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/SSMLTextInputSample.txt)
> [!NOTE]
> 若為中文（大陸）、中文（香港特別行政區）、中文（臺灣）、日文和韓文，則會將一個單字計為兩個字元。 

## <a name="submit-synthesis-requests"></a>提交合成要求

準備輸入內容之後，請遵循[長格式的音訊合成快速入門](https://aka.ms/long-audio-python)來提交要求。 如果您有多個輸入檔案，就必須提交多個要求。 

**HTTP 狀態碼**表示常見的錯誤。

| API | HTTP 狀態碼 | 描述 | 提案 |
|-----|------------------|-------------|----------|
| 建立 | 400 | 未在此區域中啟用語音合成。 | 將語音訂用帳戶金鑰變更為支援的區域。 |
|        | 400 | 只有此區域的**標準**語音訂用帳戶是有效的。 | 將語音訂用帳戶金鑰變更為「標準」定價層。 |
|        | 400 | 超過 Azure 帳戶的20000要求限制。 請先移除一些要求，再提交新的要求。 | 伺服器會針對每個 Azure 帳戶保留最多20000個要求。 在提交新要求之前先將其刪除。 |
|        | 400 | 此模型無法用於語音合成： {modelID}。 | 請確定 {modelID} 的狀態正確。 |
|        | 400 | 要求的區域不符合模型的區域： {modelID}。 | 請確定 {modelID} 的區域符合要求的區域。 |
|        | 400 | 語音合成僅支援使用位元組順序標記之 UTF-8 編碼的文字檔。 | 請確定輸入檔的格式為 utf-8 編碼，並包含位元組順序標記。 |
|        | 400 | 語音合成要求中僅允許有效的 SSML 輸入。 | 請確定輸入 SSML 運算式正確。 |
|        | 400 | 在輸入檔中找不到語音名稱 {voiceName}。 | 輸入 SSML 語音名稱與模型識別碼不一致。 |
|        | 400 | 輸入檔中的段落數量應小於10000。 | 請確定檔案中的段落小於10000。 |
|        | 400 | 輸入檔應超過400個字元。 | 請確定您的輸入檔超過400個字元。 |
|        | 404 | 找不到在語音合成定義中宣告的模型： {modelID}。 | 請確定 {modelID} 正確。 |
|        | 429 | 超過使用中的語音合成限制。 請等候一些要求完成。 | 允許伺服器執行，並針對每個 Azure 帳戶佇列最多120個要求。 請等候並避免提交新的要求，直到部分要求完成為止。 |
| 全部       | 429 | 要求太多。 | 針對每個 Azure 帳戶，用戶端可以提交最多5個對伺服器的要求。 請減少每秒的要求數量。 |
| 刪除    | 400 | 語音合成工作仍在使用中。 | 您只能刪除已**完成**或**失敗**的要求。 |
| GetByID   | 404 | 找不到指定的實體。 | 請確定合成識別碼正確。 |

## <a name="regions-and-endpoints"></a>區域與端點

較長的音訊 API 可在具有唯一端點的多個區域中使用。

| 區域 | 端點 |
|--------|----------|
| 澳大利亞東部 | `https://australiaeast.customvoice.api.speech.microsoft.com` |
| 加拿大中部 | `https://canadacentral.customvoice.api.speech.microsoft.com` |
| 美國東部 | `https://eastus.customvoice.api.speech.microsoft.com` |
| 印度中部 | `https://centralindia.customvoice.api.speech.microsoft.com` |
| 美國中南部 | `https://southcentralus.customvoice.api.speech.microsoft.com` |
| 東南亞 | `https://southeastasia.customvoice.api.speech.microsoft.com` |
| 英國南部 | `https://uksouth.customvoice.api.speech.microsoft.com` |
| 西歐 | `https://westeurope.customvoice.api.speech.microsoft.com` |
| 美國西部 2 | `https://westus2.customvoice.api.speech.microsoft.com` |

## <a name="audio-output-formats"></a>音訊輸出格式

我們支援彈性的音訊輸出格式。 您可以藉由設定 ' concatenateResult ' 參數，為每個段落產生音訊輸出，或將音訊輸出串連成單一輸出。 較長的音訊 API 支援下列音訊輸出格式：

> [!NOTE]
> 預設的音訊格式為 riff-riff-16khz-16bit-mono-pcm-dxil 16 位-mono-pcm。

* riff-8khz-dxil 16 位-mono-pcm
* riff-riff-16khz-16bit-mono-pcm-dxil 16 位-mono-pcm
* riff-24khz-dxil 16 位-mono-pcm
* riff-48khz-dxil 16 位-mono-pcm
* 音訊-riff-16khz-16bit-mono-pcm-32kbitrate-單聲道-mp3
* 音訊-riff-16khz-16bit-mono-pcm-64kbitrate-單聲道-mp3
* 音訊-riff-16khz-16bit-mono-pcm-128kbitrate-單聲道-mp3
* 音訊-24khz-48kbitrate-單聲道-mp3
* 音訊-24khz-96kbitrate-單聲道-mp3
* 音訊-24khz-160kbitrate-單聲道-mp3

## <a name="quickstarts"></a>快速入門

我們提供的快速入門旨在協助您順利執行長音訊 API。 此表格包含以語言組織的長音訊 API 快速入門清單。

* [快速入門： Python](https://aka.ms/long-audio-python)

## <a name="sample-code"></a>範例程式碼
長音訊 API 的範例程式碼可在 GitHub 上取得。

* [範例程式碼： Python](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/Python)
* [範例程式碼： C#](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/CSharp)
* [範例程式碼： JAVA](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/)
