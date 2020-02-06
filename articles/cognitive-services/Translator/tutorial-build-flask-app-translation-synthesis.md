---
title: 教學課程：建置 Flask 應用程式以翻譯、合成和分析文字 - 翻譯工具文字 API
titleSuffix: Azure Cognitive Services
description: 在本教學課程中，您將建置以 Flask 為基礎的 Web 應用程式來翻譯文字、分析情緒，以及將翻譯的文字合成為語音。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: tutorial
ms.date: 12/09/2019
ms.author: swmachan
ms.openlocfilehash: 0075862e198ce67cc7367efe94d624ad18e6eb3b
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/04/2020
ms.locfileid: "76984162"
---
# <a name="tutorial-build-a-flask-app-with-azure-cognitive-services"></a>教學課程：建置採用 Azure 認知服務的 Flask 應用程式

在本教學課程中，您將建置使用 Azure 認知服務來翻譯文字、分析情緒，以及將翻譯的文字合成為語音的 Flask Web 應用程式。 我們主要將說明啟用應用程式的 Python 程式碼和 Flask 路由，但對於整合應用程式的 HTML 和 Javascript，我們也會提供相關協助。 如果您遇到任何問題，請使用下方的意見反應按鈕告訴我們。

本教學課程涵蓋下列內容：

> [!div class="checklist"]
> * 取得 Azure 訂用帳戶金鑰
> * 設定開發環境及安裝相依性
> * 建立 Flask 應用程式
> * 使用翻譯工具文字 API 來翻譯文字
> * 使用文字分析來分析輸入文字和翻譯的正面/負面情感
> * 使用語音服務將翻譯的文字轉換成合成語音
> * 在本機執行 Flask 應用程式

> [!TIP]
> 如果您想要略過而直接查看所有程式碼，您可以在 [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Flask-App-Tutorial) 取得整個範例和建置指示。

## <a name="what-is-flask"></a>什麼是 Flask？

Flask 是一個可建立 Web 應用程式的微架構。 這表示 Flask 會提供工具、程式庫和技術，讓您建置 Web 應用程式。 此 Web 應用程式可以是某些網頁、部落格、Wiki，或是以 Web 為基礎的行事曆應用程式或商業網站之類的實質項目。

如果您在完成本教學課程後想要深入探索，可以參考以下幾個有用的連結：

* [Flask 文件](http://flask.pocoo.org/)
* [Flask for Dummies - Flask 的初學者指南](https://codeburst.io/flask-for-dummies-a-beginners-guide-to-flask-part-uno-53aec6afc5b1)

## <a name="prerequisites"></a>Prerequisites

我們要先檢視本教學課程所將需要的軟體和訂用帳戶金鑰。

* [Python 3.5.2 或更新版本](https://www.python.org/downloads/)
* [Git 工具](https://git-scm.com/downloads)
* IDE 或文字編輯器，例如 [Visual Studio Code](https://code.visualstudio.com/) 或 [Atom](https://atom.io/)  
* [Chrome](https://www.google.com/chrome/browser/) 或 [Firefox](https://www.mozilla.org/firefox)
* **翻譯工具文字**訂用帳戶金鑰 (請注意，您不需要選取區域。)
* **美國西部**區域的**文字分析**訂用帳戶金鑰。
* **美國西部**區域的**語音服務**訂用帳戶金鑰。

## <a name="create-an-account-and-subscribe-to-resources"></a>建立帳戶並訂閱資源

如前所述，您在本教學課程中將需要三個訂用帳戶金鑰。 這表示您必須在 Azure 帳戶中建立下列項目的資源：
* 翻譯文字
* 文字分析
* 語音服務

請使用[在 Azure 入口網站中建立認知服務帳戶](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)，取得建立資源的逐步指示。

> [!IMPORTANT]
> 在本教學課程中，請在美國西部區域建立您的資源。 如果使用不同的區域，您將必須在每個 Python 檔案中調整基底 URL。

## <a name="set-up-your-dev-environment"></a>設定開發環境

在建置 Flask Web 應用程式之前，您必須建立專案的工作目錄，並安裝幾個 Python 套件。

### <a name="create-a-working-directory"></a>建立工作目錄

1. 開啟命令列 (Windows) 或終端機 (macOS/Linux)。 然後，為您的專案建立工作目錄和子目錄：  

   ```
   mkdir -p flask-cog-services/static/scripts && mkdir flask-cog-services/templates
   ```
2. 切換至專案的工作目錄：  

   ```
   cd flask-cog-services
   ```

### <a name="create-and-activate-your-virtual-environment-with-virtualenv"></a>使用 `virtualenv` 建立並啟用虛擬環境

我們將使用 `virtualenv` 建立 Flask 應用程式的虛擬環境。 使用虛擬環境，可確保您將以全新的環境展開工作。

1. 在您的工作目錄中執行下列命令，以建立虛擬環境：**macOS/Linux：**
   ```
   virtualenv venv --python=python3
   ```
   我們已明確宣告虛擬環境應使用 Python 3。 這可確保具有多個 Python 安裝的使用者將使用正確的版本。

   **Windows CMD/Windows Bash：**
   ```
   virtualenv venv
   ```
   為了簡單起見，我們將虛擬環境命名為 venv。

2. 用來啟用虛擬環境的命令取決於您的平台/殼層：   

   | 平台 | 殼層 | Command |
   |----------|-------|---------|
   | macOS/Linux | bash/zsh | `source venv/bin/activate` |
   | Windows | Bash | `source venv/Scripts/activate` |
   | | 命令列 | `venv\Scripts\activate.bat` |
   | | PowerShell | `venv\Scripts\Activate.ps1` |

   執行此命令之後，您的命令列或終端機工作階段應會在前面加上 `venv`。

3. 您可以隨時在命令列或終端機中輸入下列內容，以停用工作階段：`deactivate`。

> [!NOTE]
> Python 有詳盡的文件會說明如何建立和管理虛擬環境，請參閱 [virtualenv](https://virtualenv.pypa.io/en/latest/)。

### <a name="install-requests"></a>安裝要求

要求是用來傳送 HTTP 1.1 要求的常用模組。 您不需要手動將查詢字串新增至 URL，或對 POST 資料進行表單編碼。

1. 若要安裝要求，請執行：

   ```
   pip install requests
   ```

> [!NOTE]
> 如果您想要深入了解要求，請參閱[要求：HTTP for Humans](https://2.python-requests.org/en/master/)。

### <a name="install-and-configure-flask"></a>安裝和設定 Flask

接下來，我們必須安裝 Flask。 Flask 會處理 Web 應用程式的路由，讓我們能夠進行伺服器對伺服器的呼叫，而對使用者隱藏我們的訂用帳戶金鑰。

1. 若要安裝 Flask，請執行：
   ```
   pip install Flask
   ```
   我們必須確定 Flask 已安裝。 請執行：
   ```
   flask --version
   ```
   版本應列印至終端機。 若非如此，表示有錯誤發生。

2. 若要執行 Flask 應用程式，您可以使用 flask 命令，或將 Python 的 -m 參數與 Flask 搭配使用。 您必須先匯出 `FLASK_APP` 環境變數，向終端機指出所要使用的應用程式，才能這麼做：

   **macOS/Linux**：
   ```
   export FLASK_APP=app.py
   ```

   **Windows**：
   ```
   set FLASK_APP=app.py
   ```

## <a name="create-your-flask-app"></a>建立您的 Flask 應用程式

在本節中，您將建立在使用者叫用您應用程式的根目錄時會傳回 HTML 檔案的簡易 Flask 應用程式。 請不要花太多時間在程式碼的細節上，我們稍後將回過頭來更新此檔案。

### <a name="what-is-a-flask-route"></a>什麼是 Flask 路由？

我們將花點時間討論「[路由](http://flask.pocoo.org/docs/1.0/api/#flask.Flask.route)」。 路由可用來將 URL 繫結至特定函式。 Flask 會使用路由裝飾項目將函式註冊到特定 URL。 例如，當使用者瀏覽至 Web 應用程式的根目錄 (`/`) 時，將會轉譯 `index.html`。  

```python
@app.route('/')
def index():
    return render_template('index.html')
```

我們來看看另一個範例以進一步說明。

```python
@app.route('/about')
def about():
    return render_template('about.html')
```

此程式碼可確保當使用者瀏覽至 `http://your-web-app.com/about` 時，將會轉譯 `about.html` 檔案。

雖然這些範例說明如何為使用者呈現 HTML 網頁，但路由也可在按下按鈕時用來呼叫 API，或取用任何數量的動作而無須離開首頁。 您在建立翻譯、情感和語音合成的路由時，將會看到此類動作的執行。

### <a name="get-started"></a>開始使用

1. 在 IDE 中開啟專案，然後在工作目錄的根目錄中建立名為 `app.py` 的檔案。 接著，將此程式碼複製到 `app.py` 中並儲存：

   ```python
   from flask import Flask, render_template, url_for, jsonify, request

   app = Flask(__name__)
   app.config['JSON_AS_ASCII'] = False

   @app.route('/')
   def index():
       return render_template('index.html')
   ```

   此程式碼區塊會指示應用程式應在每次使用者瀏覽至您 Web 應用程式的根目錄 (`/`) 時顯示 `index.html`。

2. 接下來，我們將為 Web 應用程式建立前端。 在 `templates` 目錄中建立名為 `index.html` 的檔案。 然後，將此程式碼複製到 `templates/index.html` 中。

   ```html
   <!doctype html>
   <html lang="en">
     <head>
       <!-- Required metadata tags -->
       <meta charset="utf-8">
       <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
       <meta name="description" content="Translate and analyze text with Azure Cognitive Services.">
       <!-- Bootstrap CSS -->
       <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css" integrity="sha384-Gn5384xqQ1aoWXA+058RXPxPg6fy4IWvTNh0E263XmFcJlSAwiGgFAW/dAiS6JXm" crossorigin="anonymous">
       <title>Translate and analyze text with Azure Cognitive Services</title>
     </head>
     <body>
       <div class="container">
         <h1>Translate, synthesize, and analyze text with Azure</h1>
         <p>This simple web app uses Azure for text translation, text-to-speech conversion, and sentiment analysis of input text and translations. Learn more about <a href="https://docs.microsoft.com/azure/cognitive-services/">Azure Cognitive Services</a>.
        </p>
        <!-- HTML provided in the following sections goes here. -->

        <!-- End -->
       </div>

       <!-- Required Javascript for this tutorial -->
       <script src="https://code.jquery.com/jquery-3.2.1.slim.min.js" integrity="sha384-KJ3o2DKtIkvYIK3UENzmM7KCkRr/rE9/Qpg6aAZGJwFDMVNA/GpGFF93hXpG5KkN" crossorigin="anonymous"></script>
       <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>
       <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.12.9/umd/popper.min.js" integrity="sha384-ApNbgh9B+Y1QKtv3Rn7W3mgPxhU9K/ScQsAP7hUibX39j7fakFPskvXusvfa0b4Q" crossorigin="anonymous"></script>
       <script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/js/bootstrap.min.js" integrity="sha384-JZR6Spejh4U02d8jOt6vLEHfe/JQGiRRSQQxSfFWpi1MquVdAyjUar5+76PVCmYl" crossorigin="anonymous"></script>
       <script type = "text/javascript" src ="static/scripts/main.js"></script>
     </body>
   </html>
   ```

3. 我們將測試 Flask 應用程式。 在終端機中，執行：

   ```
   flask run
   ```

4. 開啟瀏覽器並瀏覽至提供的 URL。 您應該會看到單頁應用程式。 請按 **Ctrl + c** 以終止應用程式。

## <a name="translate-text"></a>翻譯文字

現在您已大致了解簡易 Flask 應用程式的運作方式，接下來我們將：

* 撰寫 Python 以呼叫翻譯工具文字 API 並傳回回應
* 建立 Flask 路由以呼叫 Python 程式碼
* 使用文字輸入和翻譯的區域、語言選取器和翻譯按鈕來更新 HTML
* 撰寫可讓使用者從 HTML 與您的 Flask 應用程式互動的 Javascript

### <a name="call-the-translator-text-api"></a>呼叫翻譯工具文字 API

首要工作是撰寫用來呼叫翻譯工具文字 API 的函式。 此函式會採用兩個引數：`text_input` 和 `language_output`。 每當有使用者在您的應用程式中按下翻譯按鈕時，就會呼叫此函式。 HTML 中的文字區域會以 `text_input` 的形式傳送，而 HTML 中的語言選取值則以 `language_output` 的形式傳送。

1. 首先，我們要在工作目錄的根目錄中建立名為 `translate.py` 的檔案。
2. 接著，將下列程式碼新增至 `translate.py`。 此函式會採用兩個引數：`text_input` 和 `language_output`。
   ```python
   import os, requests, uuid, json

   # Don't forget to replace with your Cog Services subscription key!
   # If you prefer to use environment variables, see Extra Credit for more info.
   subscription_key = 'YOUR_TRANSLATOR_TEXT_SUBSCRIPTION_KEY'
   
   # Don't forget to replace with your Cog Services location!
   # Our Flask route will supply two arguments: text_input and language_output.
   # When the translate text button is pressed in our Flask app, the Ajax request
   # will grab these values from our web app, and use them in the request.
   # See main.js for Ajax calls.
   def get_translation(text_input, language_output):
       base_url = 'https://api.cognitive.microsofttranslator.com'
       path = '/translate?api-version=3.0'
       params = '&to=' + language_output
       constructed_url = base_url + path + params

       headers = {
           'Ocp-Apim-Subscription-Key': subscription_key,
           'Ocp-Apim-Subscription-Region': 'location',
           'Content-type': 'application/json',
           'X-ClientTraceId': str(uuid.uuid4())
       }

       # You can pass more than one object in body.
       body = [{
           'text' : text_input
       }]
       response = requests.post(constructed_url, headers=headers, json=body)
       return response.json()
   ```
3. 新增您的翻譯工具文字訂用帳戶金鑰並儲存。

### <a name="add-a-route-to-apppy"></a>將路由新增至 `app.py`

接著，您必須在呼叫 `translate.py` 的 Flask 應用程式中建立路由。 每當有使用者在您的應用程式中按下翻譯按鈕時，就會呼叫此路由。

就此應用程式而言，您的路由將接受 `POST` 要求。 這是因為函式預期應有要翻譯的文字和翻譯的輸出語言。

Flask 提供的協助程式函式可協助您剖析和管理每個要求。 在提供的程式碼中，`get_json()` 會從 `POST` 要求傳回 JSON 形式的資料。 然後，文字和輸出語言值會透過 `data['text']` 和 `data['to']` 傳至可從 `translate.py` 使用的 `get_translation()` 函式。 最後一個步驟是傳回 JSON 形式的回應，因為您必須在 Web 應用程式中顯示這項資料。

在以下幾節中，您將重複此程序，以建立用於情感分析和語音合成的路由。

1. 開啟 `app.py` 並在 `app.py` 的最上方找出匯入陳述式，然後新增以下這一行：

   ```python
   import translate
   ```
   現在，我們的 Flask 應用程式已可使用可透過 `translate.py` 使用的方法。

2. 將此程式碼複製到 `app.py` 結尾處並儲存：

   ```python
   @app.route('/translate-text', methods=['POST'])
   def translate_text():
       data = request.get_json()
       text_input = data['text']
       translation_output = data['to']
       response = translate.get_translation(text_input, translation_output)
       return jsonify(response)
   ```

### <a name="update-indexhtml"></a>更新 `index.html`

現在，您已有用來翻譯文字的函式，且 Flask 應用程式中已有可呼叫此函式的路由，下一個步驟就是開始建置應用程式的 HTML。 下列 HTML 會執行幾個動作：

* 提供一個文字區域讓使用者可輸入要翻譯的文字。
* 加入語言選取器。
* 加入 HTML 元素，用以轉譯偵測到的語言以及在翻譯期間傳回的信賴分數。
* 提供用來顯示翻譯輸出的唯讀文字區域。
* 為您稍後將在本教學課程中新增至此檔案的情感分析和語音合成程式碼加入預留位置。

我們將更新 `index.html`。

1. 開啟 `index.html` 並找出下列程式碼註解：
   ```html
   <!-- HTML provided in the following sections goes here. -->

   <!-- End -->
   ```

2. 將這些程式碼註解取代為下列 HTML 區塊：
   ```html
   <div class="row">
     <div class="col">
       <form>
         <!-- Enter text to translate. -->
         <div class="form-group">
           <label for="text-to-translate"><strong>Enter the text you'd like to translate:</strong></label>
           <textarea class="form-control" id="text-to-translate" rows="5"></textarea>
         </div>
         <!-- Select output language. -->
         <div class="form-group">
           <label for="select-language"><strong>Translate to:</strong></label>
           <select class="form-control" id="select-language">
             <option value="ar">Arabic</option>
             <option value="ca">Catalan</option>
             <option value="zh-Hans">Chinese (Simplified)</option>
             <option value="zh-Hant">Chinese (Traditional)</option>
             <option value="hr">Croatian</option>
             <option value="en">English</option>
             <option value="fr">French</option>
             <option value="de">German</option>
             <option value="el">Greek</option>
             <option value="he">Hebrew</option>
             <option value="hi">Hindi</option>
             <option value="it">Italian</option>
             <option value="ja">Japanese</option>
             <option value="ko">Korean</option>
             <option value="pt">Portuguese</option>
             <option value="ru">Russian</option>
             <option value="es">Spanish</option>
             <option value="th">Thai</option>
             <option value="tr">Turkish</option>
             <option value="vi">Vietnamese</option>
           </select>
         </div>
         <button type="submit" class="btn btn-primary mb-2" id="translate">Translate text</button></br>
         <div id="detected-language" style="display: none">
           <strong>Detected language:</strong> <span id="detected-language-result"></span><br />
           <strong>Detection confidence:</strong> <span id="confidence"></span><br /><br />
         </div>

         <!-- Start sentiment code-->

         <!-- End sentiment code -->

       </form>
     </div>
     <div class="col">
       <!-- Translated text returned by the Translate API is rendered here. -->
       <form>
         <div class="form-group" id="translator-text-response">
           <label for="translation-result"><strong>Translated text:</strong></label>
           <textarea readonly class="form-control" id="translation-result" rows="5"></textarea>
         </div>

         <!-- Start voice font selection code -->

         <!-- End voice font selection code -->

       </form>

       <!-- Add Speech Synthesis button and audio element -->

       <!-- End Speech Synthesis button -->

     </div>
   </div>
   ```

下一個步驟是撰寫 JavaScript。 這是您的 HTML 與 Flask 路由之間的橋樑。

### <a name="create-mainjs"></a>建立 `main.js`  

`main.js` 檔案是您的 HTML 與 Flask 路由之間的橋樑。 您的應用程式將使用 jQuery、Ajax 和 XMLHttpRequest 的組合來轉譯內容，以及對您的 Flask 路由發出 `POST` 要求。

在下列程式碼中，會使用 HTML 中的內容來建構對 Flask 路由的要求。 具體來說，文字區域和語言選取器的內容會指派給變數，並隨著要求傳至 `translate-text`。

然後，程式碼會逐一查看回應，並以翻譯、偵測到的語言和信賴分數更新 HTML。

1. 從您的 IDE，在 `static/scripts` 目錄中建立名為 `main.js` 的檔案。
2. 將此程式碼複製到 `static/scripts/main.js` 中：
   ```javascript
   //Initiate jQuery on load.
   $(function() {
     //Translate text with flask route
     $("#translate").on("click", function(e) {
       e.preventDefault();
       var translateVal = document.getElementById("text-to-translate").value;
       var languageVal = document.getElementById("select-language").value;
       var translateRequest = { 'text': translateVal, 'to': languageVal }

       if (translateVal !== "") {
         $.ajax({
           url: '/translate-text',
           method: 'POST',
           headers: {
               'Content-Type':'application/json'
           },
           dataType: 'json',
           data: JSON.stringify(translateRequest),
           success: function(data) {
             for (var i = 0; i < data.length; i++) {
               document.getElementById("translation-result").textContent = data[i].translations[0].text;
               document.getElementById("detected-language-result").textContent = data[i].detectedLanguage.language;
               if (document.getElementById("detected-language-result").textContent !== ""){
                 document.getElementById("detected-language").style.display = "block";
               }
               document.getElementById("confidence").textContent = data[i].detectedLanguage.score;
             }
           }
         });
       };
     });
     // In the following sections, you'll add code for sentiment analysis and
     // speech synthesis here.
   })
   ```

### <a name="test-translation"></a>文字翻譯

我們將在應用程式中測試翻譯。

```
flask run
```

瀏覽至提供的伺服器位址。 在輸入區域中輸入文字、選取語言，然後按翻譯按鈕。 您應會取得翻譯。 若非如此，請確定您已新增訂用帳戶金鑰。

> [!TIP]
> 若未顯示您所做的變更，或應用程式的運作不符合預期，請嘗試清除快取或開啟 private/incognito 視窗。

按 **CTRL + c** 以終止應用程式，然後前往下一節。

## <a name="analyze-sentiment"></a>分析情感

[文字分析 API](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) 可用來執行情感分析、從文字中擷取關鍵片語，或偵測原始語言。 在此應用程式中，我們將使用情感分析來判斷提供的文字是正面、中性還是負面的。 此 API 會傳回 0 到 1 之間的數值分數。 接近 1 的分數表示正面情感；接近 0 的分數表示負面情感。

在本節中，您將執行幾項工作：

* 撰寫呼叫文字分析 API 的 Python，以執行情感分析並傳回回應
* 建立 Flask 路由以呼叫 Python 程式碼
* 使用情感分數的區域和用來執行分析的按鈕進行 HTML 更新
* 撰寫可讓使用者從 HTML 與您的 Flask 應用程式互動的 Javascript

### <a name="call-the-text-analytics-api"></a>呼叫文字分析 API

我們將撰寫用來呼叫文字分析 API 的函式。 此函式會採用四個引數：`input_text`、`input_language`、`output_text` 和 `output_language`。 每當有使用者在您的應用程式中按下執行情感分析按鈕時，就會呼叫此函式。 使用者從文字區域和語言選取器提供的資料，以及偵測到的語言和翻譯輸出，會隨著每個要求提供。 回應物件包含來源和翻譯的情感分數。 在以下幾節中，您將撰寫用來剖析回應並其用於應用程式中的 Javascript。 目前我們先將焦點放在呼叫文字分析 API 上。

1. 我們要在工作目錄的根目錄中建立名為 `sentiment.py` 的檔案。
2. 接著，將下列程式碼新增至 `sentiment.py`。
   ```python
   import os, requests, uuid, json

   # Don't forget to replace with your Cog Services subscription key!
   subscription_key = 'YOUR_TEXT_ANALYTICS_SUBSCRIPTION_KEY'

   # Our Flask route will supply four arguments: input_text, input_language,
   # output_text, output_language.
   # When the run sentiment analysis button is pressed in our Flask app,
   # the Ajax request will grab these values from our web app, and use them
   # in the request. See main.js for Ajax calls.

   def get_sentiment(input_text, input_language, output_text, output_language):
       base_url = 'https://westus.api.cognitive.microsoft.com/text/analytics'
       path = '/v2.0/sentiment'
       constructed_url = base_url + path

       headers = {
           'Ocp-Apim-Subscription-Key': subscription_key,
           'Content-type': 'application/json',
           'X-ClientTraceId': str(uuid.uuid4())
       }

       # You can pass more than one object in body.
       body = {
           'documents': [
               {
                   'language': input_language,
                   'id': '1',
                   'text': input_text
               },
               {
                   'language': output_language,
                   'id': '2',
                   'text': output_text
               }
           ]
       }
       response = requests.post(constructed_url, headers=headers, json=body)
       return response.json()
   ```
3. 新增您的文字分析訂用帳戶金鑰並儲存。

### <a name="add-a-route-to-apppy"></a>將路由新增至 `app.py`

我們將在呼叫 `sentiment.py` 的 Flask 應用程式中建立路由。 每當有使用者在您的應用程式中按下執行情感分析按鈕時，就會呼叫此路由。 如同翻譯的路由，此路由也會接受 `POST` 要求，因為函式預期應有引數。

1. 開啟 `app.py` 並在 `app.py` 的最上方找出匯入陳述式，然後加以更新：

   ```python
   import translate, sentiment
   ```
   現在，我們的 Flask 應用程式已可使用可透過 `sentiment.py` 使用的方法。

2. 將此程式碼複製到 `app.py` 結尾處並儲存：
   ```python
   @app.route('/sentiment-analysis', methods=['POST'])
   def sentiment_analysis():
       data = request.get_json()
       input_text = data['inputText']
       input_lang = data['inputLanguage']
       output_text = data['outputText']
       output_lang =  data['outputLanguage']
       response = sentiment.get_sentiment(input_text, input_lang, output_text, output_lang)
       return jsonify(response)
   ```

### <a name="update-indexhtml"></a>更新 `index.html`

現在，您已有用來執行情感分析的函式，且 Flask 應用程式中已有可呼叫此函式的路由，下一個步驟就是開始撰寫應用程式的 HTML。 下列 HTML 會執行幾個動作：

* 在您的應用程式中新增用來執行情感分析的按鈕
* 新增說明情感評分的元素
* 新增顯示情感分數的元素

1. 開啟 `index.html` 並找出下列程式碼註解：
   ```html
   <!-- Start sentiment code-->

   <!-- End sentiment code -->
   ```

2. 將這些程式碼註解取代為下列 HTML 區塊：
   ```html
   <button type="submit" class="btn btn-primary mb-2" id="sentiment-analysis">Run sentiment analysis</button></br>
   <div id="sentiment" style="display: none">
      <p>Sentiment scores are provided on a 1 point scale. The closer the sentiment score is to 1, indicates positive sentiment. The closer it is to 0, indicates negative sentiment.</p>
      <strong>Sentiment score for input:</strong> <span id="input-sentiment"></span><br />
      <strong>Sentiment score for translation:</strong> <span id="translation-sentiment"></span>
   </div>
   ```

### <a name="update-mainjs"></a>更新 `main.js`

在下列程式碼中，會使用 HTML 中的內容來建構對 Flask 路由的要求。 具體來說，文字區域和語言選取器的內容會指派給變數，並隨著要求傳至 `sentiment-analysis` 路由。

然後，程式碼會逐一查看回應，並以情感分數更新 HTML。

1. 從您的 IDE，在 `static` 目錄中建立名為 `main.js` 的檔案。

2. 將此程式碼複製到 `static/scripts/main.js` 中：
   ```javascript
   //Run sentinment analysis on input and translation.
   $("#sentiment-analysis").on("click", function(e) {
     e.preventDefault();
     var inputText = document.getElementById("text-to-translate").value;
     var inputLanguage = document.getElementById("detected-language-result").innerHTML;
     var outputText = document.getElementById("translation-result").value;
     var outputLanguage = document.getElementById("select-language").value;

     var sentimentRequest = { "inputText": inputText, "inputLanguage": inputLanguage, "outputText": outputText,  "outputLanguage": outputLanguage };

     if (inputText !== "") {
       $.ajax({
         url: "/sentiment-analysis",
         method: "POST",
         headers: {
             "Content-Type":"application/json"
         },
         dataType: "json",
         data: JSON.stringify(sentimentRequest),
         success: function(data) {
           for (var i = 0; i < data.documents.length; i++) {
             if (typeof data.documents[i] !== "undefined"){
               if (data.documents[i].id === "1") {
                 document.getElementById("input-sentiment").textContent = data.documents[i].score;
               }
               if (data.documents[i].id === "2") {
                 document.getElementById("translation-sentiment").textContent = data.documents[i].score;
               }
             }
           }
           for (var i = 0; i < data.errors.length; i++) {
             if (typeof data.errors[i] !== "undefined"){
               if (data.errors[i].id === "1") {
                 document.getElementById("input-sentiment").textContent = data.errors[i].message;
               }
               if (data.errors[i].id === "2") {
                 document.getElementById("translation-sentiment").textContent = data.errors[i].message;
               }
             }
           }
           if (document.getElementById("input-sentiment").textContent !== '' && document.getElementById("translation-sentiment").textContent !== ""){
             document.getElementById("sentiment").style.display = "block";
           }
         }
       });
     }
   });
   // In the next section, you'll add code for speech synthesis here.
   ```

### <a name="test-sentiment-analysis"></a>測試情感分析

我們將在應用程式中測試情感分析。

```
flask run
```

瀏覽至提供的伺服器位址。 在輸入區域中輸入文字、選取語言，然後按翻譯按鈕。 您應會取得翻譯。 然後，按執行情感分析按鈕。 您應該會看到兩個分數。 若非如此，請確定您已新增訂用帳戶金鑰。

> [!TIP]
> 若未顯示您所做的變更，或應用程式的運作不符合預期，請嘗試清除快取或開啟 private/incognito 視窗。

按 **CTRL + c** 以終止應用程式，然後前往下一節。

## <a name="convert-text-to-speech"></a>將文字轉換成語音

[文字轉語音 API](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech) 可讓您的應用程式將文字轉換為仿真人的合成語音。 此服務支援標準、神經和自訂語音。 我們的範例應用程式只會使用幾個可用的語音，如需完整清單，請參閱[支援的語言](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#text-to-speech)。

在本節中，您將執行幾項工作：

* 撰寫 Python 以使用文字轉語音 API 將文字轉換為語音
* 建立 Flask 路由以呼叫 Python 程式碼
* 使用將文字轉換為語音的按鈕和音訊播放的元素更新 HTML
* 撰寫可讓使用者與您的 Flask 應用程式互動的 Javascript

### <a name="call-the-text-to-speech-api"></a>呼叫文字轉語音 API

我們將撰寫將文字轉換為語音的函式。 此函式會採用兩個引數：`input_text` 和 `voice_font`。 每當有使用者在您的應用程式中按下將文字轉換為語音按鈕時，就會呼叫此函式。 `input_text` 是翻譯文字的呼叫所傳回的翻譯輸出，`voice_font` 是 HTML 中的聲音音調選取器的值。

1. 我們要在工作目錄的根目錄中建立名為 `synthesize.py` 的檔案。

2. 接著，將下列程式碼新增至 `synthesize.py`。
   ```Python
   import os, requests, time
   from xml.etree import ElementTree

   class TextToSpeech(object):
       def __init__(self, input_text, voice_font):
           subscription_key = 'YOUR_SPEECH_SERVICES_SUBSCRIPTION_KEY'
           self.subscription_key = subscription_key
           self.input_text = input_text
           self.voice_font = voice_font
           self.timestr = time.strftime('%Y%m%d-%H%M')
           self.access_token = None

       # This function performs the token exchange.
       def get_token(self):
           fetch_token_url = 'https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken'
           headers = {
               'Ocp-Apim-Subscription-Key': self.subscription_key
           }
           response = requests.post(fetch_token_url, headers=headers)
           self.access_token = str(response.text)

       # This function calls the TTS endpoint with the access token.
       def save_audio(self):
           base_url = 'https://westus.tts.speech.microsoft.com/'
           path = 'cognitiveservices/v1'
           constructed_url = base_url + path
           headers = {
               'Authorization': 'Bearer ' + self.access_token,
               'Content-Type': 'application/ssml+xml',
               'X-Microsoft-OutputFormat': 'riff-24khz-16bit-mono-pcm',
               'User-Agent': 'YOUR_RESOURCE_NAME',
           }
           # Build the SSML request with ElementTree
           xml_body = ElementTree.Element('speak', version='1.0')
           xml_body.set('{http://www.w3.org/XML/1998/namespace}lang', 'en-us')
           voice = ElementTree.SubElement(xml_body, 'voice')
           voice.set('{http://www.w3.org/XML/1998/namespace}lang', 'en-US')
           voice.set('name', 'Microsoft Server Speech Text to Speech Voice {}'.format(self.voice_font))
           voice.text = self.input_text
           # The body must be encoded as UTF-8 to handle non-ascii characters.
           body = ElementTree.tostring(xml_body, encoding="utf-8")

           #Send the request
           response = requests.post(constructed_url, headers=headers, data=body)

           # Write the response as a wav file for playback. The file is located
           # in the same directory where this sample is run.
           return response.content
   ```
3. 新增您的語音服務訂用帳戶，並加以儲存。

### <a name="add-a-route-to-apppy"></a>將路由新增至 `app.py`

我們將在呼叫 `synthesize.py` 的 Flask 應用程式中建立路由。 每當有使用者在您的應用程式中按下將文字轉換為語音按鈕時，就會呼叫此路由。 如同翻譯和情感分析的路由，此路由也會接受 `POST` 要求，因為函式預期應有兩個引數：要合成的文字，和播放的聲音音調。

1. 開啟 `app.py` 並在 `app.py` 的最上方找出匯入陳述式，然後加以更新：

   ```python
   import translate, sentiment, synthesize
   ```
   現在，我們的 Flask 應用程式已可使用可透過 `synthesize.py` 使用的方法。

2. 將此程式碼複製到 `app.py` 結尾處並儲存：

   ```Python
   @app.route('/text-to-speech', methods=['POST'])
   def text_to_speech():
       data = request.get_json()
       text_input = data['text']
       voice_font = data['voice']
       tts = synthesize.TextToSpeech(text_input, voice_font)
       tts.get_token()
       audio_response = tts.save_audio()
       return audio_response
   ```

### <a name="update-indexhtml"></a>更新 `index.html`

現在，您已有可將文字轉換為語音的函式，且 Flask 應用程式中已有可呼叫此函式的路由，下一個步驟就是開始撰寫應用程式的 HTML。 下列 HTML 會執行幾個動作：

* 提供語音選取下拉式清單
* 新增將文字轉換為語音的按鈕
* 新增音訊元素，用以播放合成的語音

1. 開啟 `index.html` 並找出下列程式碼註解：
   ```html
   <!-- Start voice font selection code -->

   <!-- End voice font selection code -->
   ```

2. 將這些程式碼註解取代為下列 HTML 區塊：
   ```html
   <div class="form-group">
     <label for="select-voice"><strong>Select voice font:</strong></label>
     <select class="form-control" id="select-voice">
       <option value="(ar-SA, Naayf)">Arabic | Male | Naayf</option>
       <option value="(ca-ES, HerenaRUS)">Catalan | Female | HerenaRUS</option>
       <option value="(zh-CN, HuihuiRUS)">Chinese (Mainland) | Female | HuihuiRUS</option>
       <option value="(zh-CN, Kangkang, Apollo)">Chinese (Mainland) | Male | Kangkang, Apollo</option>
       <option value="(zh-HK, Tracy, Apollo)">Chinese (Hong Kong)| Female | Tracy, Apollo</option>
       <option value="(zh-HK, Danny, Apollo)">Chinese (Hong Kong) | Male | Danny, Apollo</option>
       <option value="(zh-TW, Yating, Apollo)">Chinese (Taiwan)| Female | Yaiting, Apollo</option>
       <option value="(zh-TW, Zhiwei, Apollo)">Chinese (Taiwan) | Male | Zhiwei, Apollo</option>
       <option value="(hr-HR, Matej)">Croatian | Male | Matej</option>
       <option value="(en-US, Jessa24kRUS)">English (US) | Female | Jessa24kRUS</option>
       <option value="(en-US, Guy24kRUS)">English (US) | Male | Guy24kRUS</option>
       <option value="(en-IE, Sean)">English (IE) | Male | Sean</option>
       <option value="(fr-FR, Julie, Apollo)">French | Female | Julie, Apollo</option>
       <option value="(fr-FR, HortenseRUS)">French | Female | Julie, HortenseRUS</option>
       <option value="(fr-FR, Paul, Apollo)">French | Male | Paul, Apollo</option>
       <option value="(de-DE, Hedda)">German | Female | Hedda</option>
       <option value="(de-DE, HeddaRUS)">German | Female | HeddaRUS</option>
       <option value="(de-DE, Stefan, Apollo)">German | Male | Apollo</option>
       <option value="(el-GR, Stefanos)">Greek | Male | Stefanos</option>
       <option value="(he-IL, Asaf)">Hebrew (Isreal) | Male | Asaf</option>
       <option value="(hi-IN, Kalpana, Apollo)">Hindi | Female | Kalpana, Apollo</option>
       <option value="(hi-IN, Hemant)">Hindi | Male | Hemant</option>
       <option value="(it-IT, LuciaRUS)">Italian | Female | LuciaRUS</option>
       <option value="(it-IT, Cosimo, Apollo)">Italian | Male | Cosimo, Apollo</option>
       <option value="(ja-JP, Ichiro, Apollo)">Japanese | Male | Ichiro</option>
       <option value="(ja-JP, HarukaRUS)">Japanese | Female | HarukaRUS</option>
       <option value="(ko-KR, HeamiRUS)">Korean | Female | Haemi</option>
       <option value="(pt-BR, HeloisaRUS)">Portuguese (Brazil) | Female | HeloisaRUS</option>
       <option value="(pt-BR, Daniel, Apollo)">Portuguese (Brazil) | Male | Daniel, Apollo</option>
       <option value="(pt-PT, HeliaRUS)">Portuguese (Portugal) | Female | HeliaRUS</option>
       <option value="(ru-RU, Irina, Apollo)">Russian | Female | Irina, Apollo</option>
       <option value="(ru-RU, Pavel, Apollo)">Russian | Male | Pavel, Apollo</option>
       <option value="(ru-RU, EkaterinaRUS)">Russian | Female | EkaterinaRUS</option>
       <option value="(es-ES, Laura, Apollo)">Spanish | Female | Laura, Apollo</option>
       <option value="(es-ES, HelenaRUS)">Spanish | Female | HelenaRUS</option>
       <option value="(es-ES, Pablo, Apollo)">Spanish | Male | Pablo, Apollo</option>
       <option value="(th-TH, Pattara)">Thai | Male | Pattara</option>
       <option value="(tr-TR, SedaRUS)">Turkish | Female | SedaRUS</option>
       <option value="(vi-VN, An)">Vietnamese | Male | An</option>
     </select>
   </div>
   ```

3. 然後，找出下列程式碼註解：
   ```html
   <!-- Add Speech Synthesis button and audio element -->

   <!-- End Speech Synthesis button -->
   ```

4. 將這些程式碼註解取代為下列 HTML 區塊：

```html
<button type="submit" class="btn btn-primary mb-2" id="text-to-speech">Convert text-to-speech</button>
<div id="audio-playback">
  <audio id="audio" controls>
    <source id="audio-source" type="audio/mpeg" />
  </audio>
</div>
```

5. 確實儲存您的工作。

### <a name="update-mainjs"></a>更新 `main.js`

在下列程式碼中，會使用 HTML 中的內容來建構對 Flask 路由的要求。 具體來說，翻譯和聲音音調會指派給變數，並隨著要求傳至 `text-to-speech` 路由。

然後，程式碼會逐一查看回應，並以情感分數更新 HTML。

1. 從您的 IDE，在 `static` 目錄中建立名為 `main.js` 的檔案。
2. 將此程式碼複製到 `static/scripts/main.js` 中：
   ```javascript
   // Convert text-to-speech
   $("#text-to-speech").on("click", function(e) {
     e.preventDefault();
     var ttsInput = document.getElementById("translation-result").value;
     var ttsVoice = document.getElementById("select-voice").value;
     var ttsRequest = { 'text': ttsInput, 'voice': ttsVoice }

     var xhr = new XMLHttpRequest();
     xhr.open("post", "/text-to-speech", true);
     xhr.setRequestHeader("Content-Type", "application/json");
     xhr.responseType = "blob";
     xhr.onload = function(evt){
       if (xhr.status === 200) {
         audioBlob = new Blob([xhr.response], {type: "audio/mpeg"});
         audioURL = URL.createObjectURL(audioBlob);
         if (audioURL.length > 5){
           var audio = document.getElementById("audio");
           var source = document.getElementById("audio-source");
           source.src = audioURL;
           audio.load();
           audio.play();
         }else{
           console.log("An error occurred getting and playing the audio.")
         }
       }
     }
     xhr.send(JSON.stringify(ttsRequest));
   });
   // Code for automatic language selection goes here.
   ```
3. 就快要完成了。 您的最後一項工作是將程式碼新增至 `main.js`，以根據為翻譯選取的語言自動選取聲音音調。 將下列程式碼區塊新增至 `main.js`：
   ```javascript
   // Automatic voice font selection based on translation output.
   $('select[id="select-language"]').change(function(e) {
     if ($(this).val() == "ar"){
       document.getElementById("select-voice").value = "(ar-SA, Naayf)";
     }
     if ($(this).val() == "ca"){
       document.getElementById("select-voice").value = "(ca-ES, HerenaRUS)";
     }
     if ($(this).val() == "zh-Hans"){
       document.getElementById("select-voice").value = "(zh-HK, Tracy, Apollo)";
     }
     if ($(this).val() == "zh-Hant"){
       document.getElementById("select-voice").value = "(zh-HK, Tracy, Apollo)";
     }
     if ($(this).val() == "hr"){
       document.getElementById("select-voice").value = "(hr-HR, Matej)";
     }
     if ($(this).val() == "en"){
       document.getElementById("select-voice").value = "(en-US, Jessa24kRUS)";
     }
     if ($(this).val() == "fr"){
       document.getElementById("select-voice").value = "(fr-FR, HortenseRUS)";
     }
     if ($(this).val() == "de"){
       document.getElementById("select-voice").value = "(de-DE, HeddaRUS)";
     }
     if ($(this).val() == "el"){
       document.getElementById("select-voice").value = "(el-GR, Stefanos)";
     }
     if ($(this).val() == "he"){
       document.getElementById("select-voice").value = "(he-IL, Asaf)";
     }
     if ($(this).val() == "hi"){
       document.getElementById("select-voice").value = "(hi-IN, Kalpana, Apollo)";
     }
     if ($(this).val() == "it"){
       document.getElementById("select-voice").value = "(it-IT, LuciaRUS)";
     }
     if ($(this).val() == "ja"){
       document.getElementById("select-voice").value = "(ja-JP, HarukaRUS)";
     }
     if ($(this).val() == "ko"){
       document.getElementById("select-voice").value = "(ko-KR, HeamiRUS)";
     }
     if ($(this).val() == "pt"){
       document.getElementById("select-voice").value = "(pt-BR, HeloisaRUS)";
     }
     if ($(this).val() == "ru"){
       document.getElementById("select-voice").value = "(ru-RU, EkaterinaRUS)";
     }
     if ($(this).val() == "es"){
       document.getElementById("select-voice").value = "(es-ES, HelenaRUS)";
     }
     if ($(this).val() == "th"){
       document.getElementById("select-voice").value = "(th-TH, Pattara)";
     }
     if ($(this).val() == "tr"){
       document.getElementById("select-voice").value = "(tr-TR, SedaRUS)";
     }
     if ($(this).val() == "vi"){
       document.getElementById("select-voice").value = "(vi-VN, An)";
     }
   });
   ```

### <a name="test-your-app"></a>測試應用程式

我們在應用程式中測試語音合成。

```
flask run
```

瀏覽至提供的伺服器位址。 在輸入區域中輸入文字、選取語言，然後按翻譯按鈕。 您應會取得翻譯。 接著，選取語音，然後按將文字轉換為語音按鈕。 翻譯應會以合成語音播放。 若非如此，請確定您已新增訂用帳戶金鑰。

> [!TIP]
> 若未顯示您所做的變更，或應用程式的運作不符合預期，請嘗試清除快取或開啟 private/incognito 視窗。

就這樣，您已有可執行翻譯、分析情緒和合成語音的應用程式。 請按 **CTRL + c** 以終止應用程式。 請務必查看其他 [Azure 認知服務](https://docs.microsoft.com/azure/cognitive-services/)。

## <a name="get-the-source-code"></a>取得原始程式碼

您可以在 [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Flask-App-Tutorial) 上找到此專案的原始程式碼。

## <a name="next-steps"></a>後續步驟

* [Translator Text API 參考](https://docs.microsoft.com/azure/cognitive-services/Translator/reference/v3-0-reference)
* [文字分析 API 參考](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7)
* [文字轉換語音 API 參考](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-text-to-speech)
