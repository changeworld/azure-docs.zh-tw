---
title: 列出文字轉語音的語音，Python - 語音服務
titleSuffix: Azure Cognitive Services
description: 在本文中，您將瞭解如何使用 Python 取得區域/端點的標準和類神經語音完整清單。 清單會傳回為 JSON，而語音可用性會因區域而異。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: how-to
ms.date: 02/10/2020
ms.author: dapine
ms.openlocfilehash: 51fe6cea80e097f34432ab8dc7293c758bd8d720
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/11/2020
ms.locfileid: "77119804"
---
# <a name="get-the-list-of-text-to-speech-voices-using-python"></a>使用 Python 取得文字轉語音的語音清單

在本文中，您將瞭解如何使用 Python 取得區域/端點的標準和類神經語音完整清單。 清單會傳回為 JSON，而語音可用性會因區域而異。 如需支援區域的清單，請參閱[區域](regions.md)。

本文需要具有語音服務資源的[Azure 認知服務帳戶](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)。 如果您還沒有帳戶，可以使用[免費試用](get-started.md)來取得訂用帳戶金鑰。

## <a name="prerequisites"></a>必要條件

* Python 2.7.x 或 3.x
* [Visual Studio](https://visualstudio.microsoft.com/downloads/)、[Visual Studio Code](https://code.visualstudio.com/download)，或您最愛的文字編輯器
* 適用於語音服務的 Azure 訂用帳戶金鑰

## <a name="create-a-project-and-import-required-modules"></a>建立專案，並匯入所需的模組

在您慣用的 IDE 或編輯器建立新的 Python 專案。 然後，將下列程式碼片段複製到您的專案中名為 `get-voices.py` 的檔案。

```python
import requests
```

> [!NOTE]
> 如果您未曾使用這些模組，則必須先加以安裝，再執行您的程式。 若要安裝這些套件，請執行：`pip install requests`。

要求用於文字轉語音服務的 HTTP 要求。

## <a name="set-the-subscription-key-and-create-a-prompt-for-tts"></a>設定訂用帳戶金鑰，並為 TTS 建立提示

在接下來的幾節中，您將建立方法來處理授權、呼叫文字轉語音 API，並驗證回應。 一開始請先建立類別。 這是我們會放置權杖交換方法的位置，並且會呼叫文字轉語音 API。

```python
class GetVoices(object):
    def __init__(self, subscription_key):
        self.subscription_key = subscription_key
        self.access_token = None
```

`subscription_key` 是來自 Azure 入口網站的唯一金鑰。

## <a name="get-an-access-token"></a>取得存取權杖

此端點需要存取權杖來進行驗證。 若要取得存取權杖，則必須進行交換。 此範例會使用 `issueToken` 端點，以語音服務訂用帳戶金鑰交換存取權杖。

此範例假設您的語音服務訂用帳戶位於美國西部區域。 如果您使用不同的區域，請更新 `fetch_token_url` 的值。 如需完整清單，請參閱[區域](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis)。

請將此程式碼複製到 `GetVoices` 類別：

```python
def get_token(self):
    fetch_token_url = "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken"
    headers = {
        'Ocp-Apim-Subscription-Key': self.subscription_key
    }
    response = requests.post(fetch_token_url, headers=headers)
    self.access_token = str(response.text)
```

> [!NOTE]
> 如需驗證的詳細資訊，請參閱[使用存取權杖進行驗證](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token)。

## <a name="make-a-request-and-save-the-response"></a>提出要求並儲存回應

在此，您要建置要求並儲存傳回語音的清單。 首先，您必須設定 `base_url` 和 `path`。 此範例假設您會使用美國西部端點。 如果您的資源註冊至不同區域，請務必更新 `base_url`。 如需詳細資訊，請參閱[語音服務區域](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech)。

接下來，為要求新增必要的標頭。 最後，您會對服務提出要求。 如果要求成功，且傳回狀態碼 200，則回應會寫入檔案。

請將此程式碼複製到 `GetVoices` 類別：

```python
def get_voices(self):
    base_url = 'https://westus.tts.speech.microsoft.com'
    path = '/cognitiveservices/voices/list'
    get_voices_url = base_url + path
    headers = {
        'Authorization': 'Bearer ' + self.access_token
    }
    response = requests.get(get_voices_url, headers=headers)
    if response.status_code == 200:
        with open('voices.json', 'wb') as voices:
            voices.write(response.content)
            print("\nStatus code: " + str(response.status_code) +
                  "\nvoices.json is ready to view.\n")
    else:
        print("\nStatus code: " + str(
            response.status_code) + "\nSomething went wrong. Check your subscription key and headers.\n")
```

## <a name="put-it-all-together"></a>組合在一起

就快要完成了。 最後一個步驟是具現化類別並呼叫函式。

```python
if __name__ == "__main__":
    subscription_key = "YOUR_KEY_HERE"
    app = GetVoices(subscription_key)
    app.get_token()
    app.get_voices()
```

## <a name="run-the-sample-app"></a>執行範例應用程式

就這樣，您準備好要執行範例了。 從命令列 (或終端機工作階段)，請瀏覽至您的專案目錄，然後執行：

```console
python get-voices.py
```

## <a name="clean-up-resources"></a>清除資源

請務必從您的範例應用程式來源程式碼中移除任何機密資訊，例如訂用帳戶金鑰。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [探索 GitHub 上的 Python 範例](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/Python) \(英文\)

## <a name="see-also"></a>另請參閱

* [文字轉換語音 API 參考](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [建立自訂語音調](how-to-customize-voice-font.md)
* [錄製語音樣本來建立自訂語音](record-custom-voice-samples.md)
