---
title: 長音訊 API (預覽) -語音服務
titleSuffix: Azure Cognitive Services
description: 瞭解「長音訊 API」是針對將長格式文字轉換成語音的非同步合成所設計。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/11/2020
ms.author: trbye
ms.openlocfilehash: 255cfe11f8601abc89a1d96f702f453c2af1ccbd
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96533055"
---
# <a name="long-audio-api-preview"></a>長音訊 API (預覽) 

完整的音訊 API 設計用於將長格式文字轉換成語音的非同步合成 (例如：音訊書籍、新聞文章和檔) 。 此 API 不會即時傳回合成音訊，而是預期您會輪詢 (s 的回應) 並取用輸出 () 從服務提供這些輸出。 不同于語音 SDK 所使用的文字轉換語音 API，長音訊 API 可以建立合成音訊超過10分鐘，因此非常適合發行者和音訊內容平臺。

長音訊 API 的其他優點：

* 服務所傳回的合成語音會使用最佳的神經語音。
* 在無即時批次模式中會合成語音時，不需要部署語音端點。

> [!NOTE]
> 長音訊 API 現在支援 [公用神經語音](./language-support.md#neural-voices) 和 [自訂神經語音](./how-to-custom-voice.md#custom-neural-voices)。

## <a name="workflow"></a>工作流程

一般而言，使用長音訊 API 時，您會提交要合成的文字檔、輪詢狀態，然後如果狀態為 [成功]，您可以下載音訊輸出。

此圖表提供工作流程的高階總覽。

![長音訊 API 工作流程圖](media/long-audio-api/long-audio-api-workflow.png)

## <a name="prepare-content-for-synthesis"></a>準備內容以進行合成

準備文字檔時，請確定：

* 純文字 ( .txt) 或 SSML 文字 ( .txt) 
* [使用 (BOM 的位元組順序標記](https://www.w3.org/International/questions/qa-utf8-bom.en#bom)，編碼為 utf-8) 
* 是單一檔案，不是 zip
* 包含400個字元，適用于純文字或400個可 [計費字元](./text-to-speech.md#pricing-note) （適用于 SSML 文字）及少於10000個段落
  * 若為純文字，則會按下 **Enter/Return** -View [純文字輸入範例](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/en-US.txt)來分隔每個段落
  * 針對 SSML 文字，會將每個 SSML 片段視為段落。 SSML 片段應以不同的段落分隔-View [SSML 文字輸入範例](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/SSMLTextInputSample.txt)
> [!NOTE]
> 針對中文 (大陸) 、中文 (香港特別行政區) 、中文 (臺灣) 、日文和韓文，一個單字將會計算為兩個字元。 

## <a name="python-example"></a>Python 範例

本節包含的 Python 範例會顯示長音訊 API 的基本使用方式。 在您慣用的 IDE 或編輯器建立新的 Python 專案。 然後將此程式碼片段複製到名為的檔案中 `voice_synthesis_client.py` 。

```python
import argparse
import json
import ntpath
import urllib3
import requests
import time
from json import dumps, loads, JSONEncoder, JSONDecoder
import pickle

urllib3.disable_warnings(urllib3.exceptions.InsecureRequestWarning)
```

這些程式庫可用來剖析引數、建立 HTTP 要求，並呼叫文字轉換語音的長音訊 REST API。

### <a name="get-a-list-of-supported-voices"></a>取得支援的語音清單

這段程式碼可讓您取得可使用的特定區域/端點的完整語音清單。 將程式碼新增至 `voice_synthesis_client.py` ：

```python
parser = argparse.ArgumentParser(description='Text-to-speech client tool to submit voice synthesis requests.')
parser.add_argument('--voices', action="store_true", default=False, help='print voice list')
parser.add_argument('-key', action="store", dest="key", required=True, help='the speech subscription key, like fg1f763i01d94768bda32u7a******** ')
parser.add_argument('-region', action="store", dest="region", required=True, help='the region information, could be centralindia, canadacentral or uksouth')
args = parser.parse_args()
baseAddress = 'https://%s.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0-beta1/' % args.region

def getVoices():
    response=requests.get(baseAddress+"voicesynthesis/voices", headers={"Ocp-Apim-Subscription-Key":args.key}, verify=False)
    voices = json.loads(response.text)
    return voices

if args.voices:
    voices = getVoices()
    print("There are %d voices available:" % len(voices))
    for voice in voices:
        print ("Name: %s, Description: %s, Id: %s, Locale: %s, Gender: %s, PublicVoice: %s, Created: %s" % (voice['name'], voice['description'], voice['id'], voice['locale'], voice['gender'], voice['isPublicVoice'], voice['created']))
```

使用命令執行腳本 `python voice_synthesis_client.py --voices -key <your_key> -region <region>` ，並取代下列值：

* 以您的語音服務訂用帳戶金鑰取代 `<your_key>`。 您可以在 [Azure 入口網站](https://aka.ms/azureportal)中資源的 [**總覽**] 索引標籤中找到這項資訊。
* 將取代 `<region>` 為您的語音資源建立所在的區域 (例如： `eastus` 或 `westus`) 。 您可以在 [Azure 入口網站](https://aka.ms/azureportal)中資源的 [**總覽**] 索引標籤中找到這項資訊。

您會看到如下所示的輸出：

```console
There are xx voices available:

Name: Microsoft Server Speech Text to Speech Voice (en-US, xxx), Description: xxx , Id: xxx, Locale: en-US, Gender: Male, PublicVoice: xxx, Created: 2019-07-22T09:38:14Z
Name: Microsoft Server Speech Text to Speech Voice (zh-CN, xxx), Description: xxx , Id: xxx, Locale: zh-CN, Gender: Female, PublicVoice: xxx, Created: 2019-08-26T04:55:39Z
```

如果 **PublicVoice** 參數為 **True**，則語音為公開類神經語音。 否則，它是自訂神經語音。

### <a name="convert-text-to-speech"></a>將文字轉換成語音

以純文字或 SSML 文字準備輸入文字檔，然後將下列程式碼新增至 `voice_synthesis_client.py` ：

> [!NOTE]
> ' concatenateResult ' 是選擇性參數。 如果未設定此參數，則會產生每個段落的音訊輸出。 您也可以藉由設定參數，將音訊串連成1個輸出。 根據預設，音訊輸出會設定為 riff-16khz-16bit-mono-pcm。 如需支援的音訊輸出的詳細資訊，請參閱 [音訊輸出格式](#audio-output-formats)。

```python
parser.add_argument('--submit', action="store_true", default=False, help='submit a synthesis request')
parser.add_argument('--concatenateResult', action="store_true", default=False, help='If concatenate result in a single wave file')
parser.add_argument('-file', action="store", dest="file", help='the input text script file path')
parser.add_argument('-voiceId', action="store", nargs='+', dest="voiceId", help='the id of the voice which used to synthesis')
parser.add_argument('-locale', action="store", dest="locale", help='the locale information like zh-CN/en-US')
parser.add_argument('-format', action="store", dest="format", default='riff-16khz-16bit-mono-pcm', help='the output audio format')

def submitSynthesis():
    modelList = args.voiceId
    data={'name': 'simple test', 'description': 'desc...', 'models': json.dumps(modelList), 'locale': args.locale, 'outputformat': args.format}
    if args.concatenateResult:
        properties={'ConcatenateResult': 'true'}
        data['properties'] = json.dumps(properties)
    if args.file is not None:
        scriptfilename=ntpath.basename(args.file)
        files = {'script': (scriptfilename, open(args.file, 'rb'), 'text/plain')}
    response = requests.post(baseAddress+"voicesynthesis", data, headers={"Ocp-Apim-Subscription-Key":args.key}, files=files, verify=False)
    if response.status_code == 202:
        location = response.headers['Location']
        id = location.split("/")[-1]
        print("Submit synthesis request successful")
        return id
    else:
        print("Submit synthesis request failed")
        print("response.status_code: %d" % response.status_code)
        print("response.text: %s" % response.text)
        return 0

def getSubmittedSynthesis(id):
    response=requests.get(baseAddress+"voicesynthesis/"+id, headers={"Ocp-Apim-Subscription-Key":args.key}, verify=False)
    synthesis = json.loads(response.text)
    return synthesis

if args.submit:
    id = submitSynthesis()
    if (id == 0):
        exit(1)

    while(1):
        print("\r\nChecking status")
        synthesis=getSubmittedSynthesis(id)
        if synthesis['status'] == "Succeeded":
            r = requests.get(synthesis['resultsUrl'])
            filename=id + ".zip"
            with open(filename, 'wb') as f:  
                f.write(r.content)
                print("Succeeded... Result file downloaded : " + filename)
            break
        elif synthesis['status'] == "Failed":
            print("Failed...")
            break
        elif synthesis['status'] == "Running":
            print("Running...")
        elif synthesis['status'] == "NotStarted":
            print("NotStarted...")
        time.sleep(10)
```

使用命令執行腳本 `python voice_synthesis_client.py --submit -key <your_key> -region <region> -file <input> -locale <locale> -voiceId <voice_guid>` ，並取代下列值：

* 以您的語音服務訂用帳戶金鑰取代 `<your_key>`。 您可以在 [Azure 入口網站](https://aka.ms/azureportal)中資源的 [**總覽**] 索引標籤中找到這項資訊。
* 將取代 `<region>` 為您的語音資源建立所在的區域 (例如： `eastus` 或 `westus`) 。 您可以在 [Azure 入口網站](https://aka.ms/azureportal)中資源的 [**總覽**] 索引標籤中找到這項資訊。
* 取代為 `<input>` 您已備妥文字轉換語音之文字檔的路徑。
* 取代 `<locale>` 為所需的輸出地區設定。 如需詳細資訊，請參閱 [語言支援](language-support.md#neural-voices)。
* 取代 `<voice_guid>` 為所需的輸出聲音。 使用您先前呼叫端點所傳回的其中一個語音 `/voicesynthesis/voices` 。

您會看到如下所示的輸出：

```console
Submit synthesis request successful

Checking status
NotStarted...

Checking status
Running...

Checking status
Running...

Checking status
Succeeded... Result file downloaded : xxxx.zip
```

結果包含服務所產生的輸入文字和音訊輸出檔。 您可以在 zip 中下載這些檔案。

> [!NOTE]
> 如果您有1個以上的輸入檔案，您將需要提交多個要求。 有一些需要注意的限制。 
> * 用戶端可以針對每個 Azure 訂用帳戶，將最多每秒 **5** 個要求提交到伺服器。 如果超過限制，用戶端會收到429錯誤碼 () 的要求太多。 請減少每秒的要求數量
> * 您可以針對每個 Azure 訂用帳戶執行伺服器，並將每個 Azure 訂用帳戶的要求排入 **120** 佇列。 如果超過限制，伺服器將會傳回429錯誤碼 () 的要求太多。 請等候並避免提交新的要求，直到某些要求完成為止

### <a name="remove-previous-requests"></a>移除先前的要求

服務會為每個 Azure 訂用帳戶保留最多 **20000** 個要求。 如果您的要求金額超過此限制，請先移除先前的要求，再建立新的要求。 如果您未移除現有的要求，您會收到錯誤通知。

將下列程式碼新增至 `voice_synthesis_client.py`：

```python
parser.add_argument('--syntheses', action="store_true", default=False, help='print synthesis list')
parser.add_argument('--delete', action="store_true", default=False, help='delete a synthesis request')
parser.add_argument('-synthesisId', action="store", nargs='+', dest="synthesisId", help='the id of the voice synthesis which need to be deleted')

def getSubmittedSyntheses():
    response=requests.get(baseAddress+"voicesynthesis", headers={"Ocp-Apim-Subscription-Key":args.key}, verify=False)
    syntheses = json.loads(response.text)
    return syntheses

def deleteSynthesis(ids):
    for id in ids:
        print("delete voice synthesis %s " % id)
        response = requests.delete(baseAddress+"voicesynthesis/"+id, headers={"Ocp-Apim-Subscription-Key":args.key}, verify=False)
        if (response.status_code == 204):
            print("delete successful")
        else:
            print("delete failed, response.status_code: %d, response.text: %s " % (response.status_code, response.text))

if args.syntheses:
    synthese = getSubmittedSyntheses()
    print("There are %d synthesis requests submitted:" % len(synthese))
    for synthesis in synthese:
        print ("ID : %s , Name : %s, Status : %s " % (synthesis['id'], synthesis['name'], synthesis['status']))

if args.delete:
    deleteSynthesis(args.synthesisId)
```

執行 `python voice_synthesis_client.py --syntheses -key <your_key> -region <region>` 以取得您所進行的合成要求清單。 您會看到如下所示的輸出：

```console
There are <number> synthesis requests submitted:
ID : xxx , Name : xxx, Status : Succeeded
ID : xxx , Name : xxx, Status : Running
ID : xxx , Name : xxx : Succeeded
```

若要刪除要求，請執行 `python voice_synthesis_client.py --delete -key <your_key> -region <Region> -synthesisId <synthesis_id>` 並取代 `<synthesis_id>` 為前一個要求所傳回的要求識別碼值。

> [!NOTE]
> 處於「執行中」/「正在等候」狀態的要求無法移除或刪除。

已完成的 `voice_synthesis_client.py` 可在 [GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Python/voiceclient.py)上取得。

## <a name="http-status-codes"></a>HTTP 狀態碼

下表詳細說明 REST API 中的 HTTP 回應碼和訊息。

| API | HTTP 狀態碼 | 描述 | 解決方法 |
|-----|------------------|-------------|----------|
| 建立 | 400 | 未在此區域中啟用語音合成。 | 使用支援的區域變更語音訂用帳戶金鑰。 |
|        | 400 | 只有此區域的 **標準** 語音訂用帳戶有效。 | 將語音訂用帳戶金鑰變更為「標準」定價層。 |
|        | 400 | 超過 Azure 帳戶的20000要求限制。 提交新的要求之前，請先將其移除。 | 伺服器會針對每個 Azure 帳戶最多保留20000的要求。 提交新的要求之前，請先將其刪除。 |
|        | 400 | 此模型不能用於語音合成： {modelID}。 | 請確定 {modelID} 的狀態是正確的。 |
|        | 400 | 要求的區域不符合模型的區域： {modelID}。 | 請確定 {modelID} 的區域符合要求的區域。 |
|        | 400 | 語音合成僅支援 UTF-8 編碼中的文字檔和位元組順序標記。 | 請確定輸入檔案的格式為 utf-8 編碼和位元組順序標記。 |
|        | 400 | 語音合成要求中只允許有效的 SSML 輸入。 | 請確定輸入 SSML 運算式正確無誤。 |
|        | 400 | 在輸入檔中找不到語音名稱 {voiceName}。 | 輸入 SSML 語音名稱未與模型識別碼對齊。 |
|        | 400 | 輸入檔中的段落數目應小於10000。 | 請確定檔案中的段落數目小於10000。 |
|        | 400 | 輸入檔應超過400個字元。 | 請確定您的輸入檔超過400個字元。 |
|        | 404 | 找不到語音合成定義中宣告的模型： {modelID}。 | 請確定 {modelID} 正確無誤。 |
|        | 429 | 超過主動式語音合成限制。 請等候某些要求完成。 | 您可以針對每個 Azure 帳戶執行伺服器，並將最多120個要求排入佇列。 請等候並避免提交新的要求，直到某些要求完成為止。 |
| 全部       | 429 | 要求太多。 | 用戶端最多可針對每個 Azure 帳戶提交每秒5個要求至伺服器。 請減少每秒的要求數量。 |
| 刪除    | 400 | 語音合成工作仍在使用中。 | 您只能刪除已 **完成** 或 **失敗** 的要求。 |
| GetByID   | 404 | 找不到指定的實體。 | 請確定合成識別碼正確。 |

## <a name="regions-and-endpoints"></a>區域與端點

長音訊 API 可在具有唯一端點的多個區域中使用。

| 區域 | 端點 |
|--------|----------|
| 美國東部 | `https://eastus.customvoice.api.speech.microsoft.com` |
| 印度中部 | `https://centralindia.customvoice.api.speech.microsoft.com` |
| 東南亞 | `https://southeastasia.customvoice.api.speech.microsoft.com` |
| 英國南部 | `https://uksouth.customvoice.api.speech.microsoft.com` |
| 西歐 | `https://westeurope.customvoice.api.speech.microsoft.com` |

## <a name="audio-output-formats"></a>音訊輸出格式

我們支援彈性的音訊輸出格式。 您可以藉由設定 ' concatenateResult ' 參數，為每個段落產生音訊輸出，或將音訊輸出串連成單一輸出。 長音訊 API 支援下列音訊輸出格式：

> [!NOTE]
> 預設音訊格式為 riff-16khz-16bit-mono-pcm。

* riff-8khz-16bit-mono-pcm
* riff-16khz-16bit-mono-pcm
* riff-24khz-16bit-mono-pcm
* riff-48khz-16bit-mono-pcm
* 音訊-16khz-32kbitrate-mono-mp3
* 音訊-16khz-64kbitrate-mono-mp3
* 音訊-16khz-128kbitrate-mono-mp3
* 音訊-24khz-48kbitrate-mono-mp3
* 音訊-24khz-96kbitrate-mono-mp3
* 音訊-24khz-160kbitrate-mono-mp3

## <a name="sample-code"></a>範例程式碼
適用于長音訊 API 的範例程式碼可在 GitHub 上取得。

* [範例程式碼： Python](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/Python)
* [範例程式碼： C#](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/CSharp)
* [範例程式碼： JAVA](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/)