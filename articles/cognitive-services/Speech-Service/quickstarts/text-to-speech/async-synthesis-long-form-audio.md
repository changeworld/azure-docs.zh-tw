---
title: 快速入門：用於長格式音訊（預覽）的非同步合成 - 語音服務
titleSuffix: Azure Cognitive Services
description: 使用長音訊 API 將文本非同步轉換為語音，並從服務提供的 URI 檢索音訊輸出。 此 REST API 非常適合需要將大於 10，000 個字元的文字檔或 50 個段落轉換為合成語音的內容提供者。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: erhopf
ms.openlocfilehash: d3cd330001bcf53e7bd4fb9e6955c76a9ef20511
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78331071"
---
# <a name="quickstart-asynchronous-synthesis-for-long-form-audio-in-python-preview"></a>快速入門：用於 Python 中長格式音訊的非同步合成（預覽版）

在此快速入門中，您將使用長音訊 API 非同步將文本轉換為語音，並從服務提供的 URI 檢索音訊輸出。 此 REST API 非常適合需要從大於 5，000 個字元（或長度超過 10 分鐘）的文本合成音訊的內容提供者。 有關詳細資訊，請參閱[長音訊 API](../../long-audio-api.md)。

> [!NOTE]
> 長格式音訊的非同步合成只能與[自訂神經語音](../../how-to-custom-voice.md#custom-neural-voices)一起使用。

## <a name="prerequisites"></a>Prerequisites

本快速入門需要：

* Python 2.7.x 或 3.x。
* [視覺工作室](https://visualstudio.microsoft.com/downloads/)，[視覺工作室代碼](https://code.visualstudio.com/download)，或你最喜歡的文字編輯器。
* Azure 訂閱和語音服務訂閱金鑰。 [創建 Azure 帳戶](../../get-started.md#new-resource)並[創建語音資源](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#new-resource)以獲取金鑰。 創建"語音"資源時，請確保定價層設置為**S0**，並且位置設置為[受支援的區域](../../regions.md#standard-and-neural-voices)。

## <a name="create-a-project-and-import-required-modules"></a>建立專案，並匯入所需的模組

在您慣用的 IDE 或編輯器建立新的 Python 專案。 然後，將此程式碼片段複製到名為`voice_synthesis_client.py`的檔中。

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

> [!NOTE]
> 如果您尚未使用這些模組，則需要在運行程式之前安裝這些模組。 若要安裝這些套件，請執行：`pip install requests urllib3`。

這些模組用於分析參數、構造 HTTP 要求和調用文本到語音轉換長音訊 REST API。

## <a name="get-a-list-of-supported-voices"></a>獲取支援的聲音清單

此代碼獲取可用於將文本轉換為語音的可用語音的清單。 將代碼添加到`voice_synthesis_client.py`：

```python
parser = argparse.ArgumentParser(description='Cris client tool to submit voice synthesis requests.')
parser.add_argument('--voices', action="store_true", default=False, help='print voice list')
parser.add_argument('-key', action="store", dest="key", required=True, help='the cris subscription key, like ff1eb62d06d34767bda0207acb1da7d7 ')
parser.add_argument('-region', action="store", dest="region", required=True, help='the region information, could be centralindia, canadacentral or uksouth')
args = parser.parse_args()
baseAddress = 'https://%s.cris.ai/api/texttospeech/v3.0-beta1/' % args.region

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

### <a name="test-your-code"></a>測試您的程式碼

讓我們測試一下到目前為止你所做的工作。 您需要在下面的請求中更新一些內容：

* 以您的語音服務訂用帳戶金鑰取代 `<your_key>`。 此資訊在[Azure 門戶](https://aka.ms/azureportal)中的資源的 **"概述"** 選項卡中提供。
* 替換為`<region>`創建語音資源的區域（例如：`eastus`或`westus`）。 此資訊在[Azure 門戶](https://aka.ms/azureportal)中的資源的 **"概述"** 選項卡中提供。

請執行這個命令：

```console
python voice_synthesis_client.py --voices -key <your_key> -region <Region>
```

您將看到如下所示的輸出：

```console
There are xx voices available:

Name: Microsoft Server Speech Text to Speech Voice (en-US, xxx), Description: xxx , Id: xxx, Locale: en-US, Gender: Male, PublicVoice: xxx, Created: 2019-07-22T09:38:14Z
Name: Microsoft Server Speech Text to Speech Voice (zh-CN, xxx), Description: xxx , Id: xxx, Locale: zh-CN, Gender: Female, PublicVoice: xxx, Created: 2019-08-26T04:55:39Z
```

## <a name="prepare-input-files"></a>準備輸入檔

準備輸入文字檔。 它可以是純文字或 SSML 文本。 有關輸入檔的要求，請參閱如何[準備內容以進行合成](https://docs.microsoft.com/azure/cognitive-services/speech-service/long-audio-api#prepare-content-for-synthesis)。

## <a name="convert-text-to-speech"></a>將文本轉換為語音

準備輸入文字檔後，將此語音合成代碼添加到`voice_synthesis_client.py`：

> [!NOTE]
> "串聯結果"是一個可選參數。 如果未設置此參數，則每個段落將生成音訊輸出。 您還可以通過設置參數將音訊串聯到 1 個輸出中。 預設情況下，音訊輸出設置為 riff-16khz-16 位單聲道 pcm。 有關受支援的音訊輸出的詳細資訊，請參閱[音訊輸出格式](https://docs.microsoft.com/azure/cognitive-services/speech-service/long-audio-api#audio-output-formats)。

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

### <a name="test-your-code"></a>測試您的程式碼

讓我們請求使用輸入檔作為源合成文本。 您需要在下面的請求中更新一些內容：

* 以您的語音服務訂用帳戶金鑰取代 `<your_key>`。 此資訊在[Azure 門戶](https://aka.ms/azureportal)中的資源的 **"概述"** 選項卡中提供。
* 替換為`<region>`創建語音資源的區域（例如：`eastus`或`westus`）。 此資訊在[Azure 門戶](https://aka.ms/azureportal)中的資源的 **"概述"** 選項卡中提供。
* 替換為`<input>`已為文本到語音轉換準備的文字檔的路徑。
* 替換為`<locale>`所需的輸出地區設定。 有關詳細資訊，請參閱[語言支援](../../language-support.md#neural-voices)。
* 替換為`<voice_guid>`所需的輸出語音。 使用[獲取支援的聲音清單返回的](#get-a-list-of-supported-voices)語音之一。

使用此命令將文本轉換為語音：

```console
python voice_synthesis_client.py --submit -key <your_key> -region <Region> -file <input> -locale <locale> -voiceId <voice_guid>
```

> [!NOTE]
> 如果您有多個輸入檔，則需要提交多個請求。 有一些需要注意的限制。 
> * 允許用戶端為每個 Azure 訂閱帳戶每秒向伺服器提交最多**5 個**請求。 如果超出限制，用戶端將獲得 429 錯誤代碼（請求太多）。 請減少每秒的請求金額
> * 允許伺服器為每個 Azure 訂閱帳戶運行和排隊多達**120**個請求。 如果超過限制，伺服器將返回 429 錯誤代碼（請求太多）。 請等待並避免提交新請求，直到某些請求完成

您將看到如下所示的輸出：

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

結果包含輸入文本和服務生成的音訊輸出檔。 您可以在 zip 中下載這些檔。

## <a name="remove-previous-requests"></a>刪除以前的請求

伺服器將為每個 Azure 訂閱帳戶保留多達**20，000**個請求。 如果您的請求金額超過此限制，請在發出新請求之前刪除以前的請求。 如果不刪除現有請求，您將收到錯誤通知。

將代碼添加到`voice_synthesis_client.py`：

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

### <a name="test-your-code"></a>測試您的程式碼

現在，讓我們查看您以前提交的請求。 在繼續之前，您需要更新此請求中的一些內容：

* 以您的語音服務訂用帳戶金鑰取代 `<your_key>`。 此資訊在[Azure 門戶](https://aka.ms/azureportal)中的資源的 **"概述"** 選項卡中提供。
* 替換為`<region>`創建語音資源的區域（例如：`eastus`或`westus`）。 此資訊在[Azure 門戶](https://aka.ms/azureportal)中的資源的 **"概述"** 選項卡中提供。

請執行這個命令：

```console
python voice_synthesis_client.py --syntheses -key <your_key> -region <Region>
```

這將返回您所做的綜合請求的清單。 您將看到如下所示的輸出：

```console
There are <number> synthesis requests submitted:
ID : xxx , Name : xxx, Status : Succeeded
ID : xxx , Name : xxx, Status : Running
ID : xxx , Name : xxx : Succeeded
```

現在，讓我們刪除以前提交的請求。 您需要更新以下代碼中的一些內容：

* 以您的語音服務訂用帳戶金鑰取代 `<your_key>`。 此資訊在[Azure 門戶](https://aka.ms/azureportal)中的資源的 **"概述"** 選項卡中提供。
* 替換為`<region>`創建語音資源的區域（例如：`eastus`或`westus`）。 此資訊在[Azure 門戶](https://aka.ms/azureportal)中的資源的 **"概述"** 選項卡中提供。
* 替換為`<synthesis_id>`上一個請求中返回的值。

> [!NOTE]
> 無法刪除或刪除狀態為"正在運行"/"等待"的請求。

請執行這個命令：

```console
python voice_synthesis_client.py --delete -key <your_key> -region <Region> -synthesisId <synthesis_id>
```

您將看到如下所示的輸出：

```console
delete voice synthesis xxx
delete successful
```

## <a name="get-the-full-client"></a>獲取完整用戶端

已完成`voice_synthesis_client.py`的可在[GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Python/voiceclient.py)上下載。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [瞭解有關長音訊 API 的更多資訊](../../long-audio-api.md)
