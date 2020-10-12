---
title: 如何使用 Twilio for Voice and SMS (Python) | Microsoft Docs
description: 了解如何在 Azure 上使用 Twilio API 服務撥打電話及傳送簡訊。 程式碼範例以 Python 撰寫。
services: ''
documentationcenter: python
author: georgewallace
ms.assetid: 561bc75b-4ac4-40ba-bcba-48e901f27cc3
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 02/19/2015
ms.author: gwallace
ms.custom: devx-track-python
ms.openlocfilehash: ba93591ade730c4e9c9bdb6a42232e71e10d6469
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87850146"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-python"></a>如何在 Python 中透過 Twilio 使用語音和簡訊功能
本指南示範如何在 Azure 上透過 Twilio API 服務執行常見的程式設計工作。 涵蓋的案例包括打電話和傳送簡訊 (SMS)。 如需在應用程式中 Twilio 及使用語音和 SMS 的詳細資訊，請參閱 [後續步驟](#NextSteps) 一節。

## <a name="what-is-twilio"></a><a id="WhatIs"></a>什麼是 Twilio？
Twilio 正在形塑商業環境的未來，可讓開發人員將語音、VoIP 和訊息傳送內嵌到應用程式中。 它們將雲端、全球化環境中所需的整個基礎結構虛擬化，透過 Twilio 通訊 API 平台來揭露基礎結構。 輕鬆就可建立和擴充應用程式。 享受隨收隨付定價的彈性和雲端可靠性的好處。

**Twilio 語音** 可讓應用程式撥打和接聽電話。
**Twilio 簡訊** 可讓應用程式收發簡訊。
**Twilio 用戶端**可讓您從任何電話、平板電腦或瀏覽器撥打 VoIP 電話，且支援 WebRTC。

## <a name="twilio-pricing-and-special-offers"></a><a id="Pricing"></a>Twilio 定價和特別供應項目
Azure 客戶可在升級 Twilio 帳戶時獲得價值 10 元美金的 Twilio 點數[特殊供應項目][special_offer]。 此 Twilio 點數可用來折抵任何 Twilio 使用量 ($10 點數相當於最多傳送 1,000 則簡訊，或最多接收 1000 分鐘的撥入語音，視電話號碼所在地點或通話目的地而定)。 請兌換此 [Twilio 點數][special_offer]，並開始使用。

Twilio 是隨用隨付的服務。 不需要設定費，隨時都可結清帳戶。 如需詳細資訊，請參閱 [Twilio 價格][twilio_pricing]。

## <a name="concepts"></a><a id="Concepts"></a>概念
Twilio API 是一套為應用程式提供語音和簡訊功能的 RESTful API。 用戶端程式庫有多種語言版本，相關清單請參閱 [Twilio API 程式庫][twilio_libraries]。

Twilio API 的兩大重點是 Twilio 動詞和 Twilio 標記語言 (TwiML)。

### <a name="twilio-verbs"></a><a id="Verbs"></a>Twilio 動詞
API 會使用 Twilio 動詞;例如， ** &lt; 說 &gt; **動詞命令會指示 Twilio 語音在呼叫上傳遞訊息。

以下是 Twilio 動詞清單。 如需了解其他動詞和功能，請參閱 [Twilio 標記語言文件][twiml]。

* ** &lt; 撥號 &gt; **：將呼叫端連接到另一個電話。
* ** &lt; 收集 &gt; **：收集電話鍵盤上輸入的數位。
* ** &lt; 掛斷 &gt; **：結束通話。
* ** &lt; 暫停 &gt; **：以無訊息模式等候指定的秒數。
* ** &lt; Play &gt; **：播放音訊檔案。
* ** &lt; Queue &gt; **：將加入至呼叫端的佇列。
* ** &lt; 記錄 &gt; **：記錄呼叫端的語音，並傳回包含錄製之檔案的 URL。
* 重新** &lt; 導向 &gt; **：將呼叫或 SMS 的控制權傳送至 TwiML 的不同 URL。
* ** &lt; 拒絕 &gt; **：拒絕 Twilio 號碼的來電而不計費。
* ** &lt; 例如 &gt; **：將在呼叫上進行的文字轉換為語音。
* ** &lt; Sms &gt; **：傳送 sms 訊息。

### <a name="twiml"></a><a id="TwiML"></a>TwiML
TwiML 是以 Twilio 動詞為基礎的一組 XML 指令，可指示 Twilio 如何處理來電或簡訊。

例如，下列 TwiML 會將 **Hello World** 文字轉換成語音。

```xml
<?xml version="1.0" encoding="UTF-8" ?>
  <Response>
    <Say>Hello World</Say>
  </Response>
```

當應用程式呼叫 Twilio API 時，其中一個 API 參數是傳回 TwiML 回應的 URL。 在開發用途上，您可以使用 Twilio 提供的 URL 來提供應用程式所使用的 TwiML 回應。 您也可以裝載您自己的 URL 來產生 TwiML 回應，另一種選擇是使用 `TwiMLResponse` 物件。

如需 Twilio 動詞、屬性和 TwiML 的詳細資訊，請參閱 [TwiML][twiml]。 如需 Twilio API 的詳細資訊，請參閱 [Twilio API][twilio_api]。

## <a name="create-a-twilio-account"></a><a id="CreateAccount"></a>建立 Twilio 帳戶
準備取得 Twilio 帳戶時，請至[試用 Twilio 註冊][try_twilio]。 您可以先使用免費帳戶，稍後再升級帳戶。

註冊 Twilio 帳戶時，您會收到帳戶 SID 和驗證權杖。 兩者皆為呼叫 Twilio API 所需。 為了防止未經授權存取您的帳戶，您妥善保管驗證權杖。 在 [Twilio 主控台][twilio_console]標示為 **ACCOUNT SID** 和 **AUTH TOKEN** 的欄位中，分別可檢視您的帳戶 SID 和驗證權杖。

## <a name="create-a-python-application"></a><a id="create_app"></a>建立 Python 應用程式
使用 Twilio 服務且執行於 Azure 的 Python 應用程式，與其他使用 Twilio 服務的 Python 應用程式並無不同。 雖然 Twilio 服務是以 REST 為基礎，並且可透過數種方式從 Python 撥打，但本文的重點是要說明如何搭配使用 Twilio 服務與[適用於 Python 的 Twiliofor 程式庫 (由 GitHub 提供)][twilio_python]。 如需使用適用于 Python 的 Twilio 程式庫的詳細資訊，請參閱 [https://www.twilio.com/docs/libraries/python][twilio_lib_docs] 。

首先，[set-up a new Azure Linux VM][azure_vm_setup]，以作為新的 Python Web 應用程式主機。 當虛擬機器執行時，您必須在公用連接埠上公開應用程式，如下所述。

### <a name="add-an-incoming-rule"></a>新增傳入規則
  1. 移至 [Network Security Group][azure_nsg] 頁面。
  2. 選取與您的虛擬機器對應的網路安全性群組。
  3. 新增**連接埠 80** 的**傳出規則**。 務必允許來自任何位址的傳入。

### <a name="set-the-dns-name-label"></a>設定 DNS 名稱標籤
  1. 移至 [公用 IP 位址][azure_ips] 頁面。
  2. 選取與您的虛擬機器對應的公用 IP 位址。
  3. 在 [組態]**** 區段中設定 [DNS 名稱標籤]****。 在此範例的情況下，它看起來類似 *your-domain-label*.centralus.cloudapp.azure.com

可透過 SSH 連線到虛擬機器連線之後，就可以安裝您所選擇的 Web 架構 (Python 中最廣為人知的兩個為 [Flask](http://flask.pocoo.org/) 和 [Django](https://www.djangoproject.com))。 只要執行 `pip install` 命令即可安裝其中一項。

請記住，我們已將虛擬機器設定為僅允許連接埠 80 的流量。 因此請務必將應用程式設定為使用此連接埠。

## <a name="configure-your-application-to-use-twilio-libraries"></a><a id="configure_app"></a>設定應用程式以使用 Twilio 程式庫
您可以透過兩種方式設定應用程式，以使用適用於 Python 的 Twilio 程式庫：

* 以 Pip 封裝的形式，安裝適用於 Python 的 Twilio 程式庫。 您可以使用下列命令進行此安裝：
   
  `$ pip install twilio`

    -或-

* 從 GitHub () 下載適用于 Python 的 Twilio 程式庫 [https://github.com/twilio/twilio-python][twilio_python] ，並將其安裝如下：

  `$ python setup.py install`

安裝了適用於 Python 的 Twilio 程式庫之後，您可以在 Python 檔案中對它進行 `import`︰

  `import twilio`

如需詳細資訊，請參閱 [twilio_github_readme](https://github.com/twilio/twilio-python/blob/master/README.md)。

## <a name="how-to-make-an-outgoing-call"></a><a id="howto_make_call"></a>作法：撥出電話
下列程式碼將說明如何向外撥打電話。 此程式碼也使用 Twilio 提供的網站來傳回 Twilio 標記語言 (TwiML) 回應。 請將 **from_number** 和 **to_number** 電話號碼的值換成您的值，並在執行程式碼之前，已驗證 Twilio 帳戶的 **from_number** 電話號碼。

```python
from urllib.parse import urlencode

# Import the Twilio Python Client.
from twilio.rest import TwilioRestClient

# Set your account ID and authentication token.
account_sid = "your_twilio_account_sid"
auth_token = "your_twilio_authentication_token"

# The number of the phone initiating the call.
# This should either be a Twilio number or a number that you've verified
from_number = "NNNNNNNNNNN"

# The number of the phone receiving call.
to_number = "NNNNNNNNNNN"

# Use the Twilio-provided site for the TwiML response.
url = "https://twimlets.com/message?"

# The phone message text.
message = "Hello world."

# Initialize the Twilio client.
client = TwilioRestClient(account_sid, auth_token)

# Make the call.
call = client.calls.create(to=to_number,
                           from_=from_number,
                           url=url + urlencode({'Message': message}))
print(call.sid)
```

如前所述，此程式碼使用 Twilio 提供的網站來傳回 TwiML 回應。 您可以改用自己的網站來提供 TwiML 回應；如需詳細資訊，請參閱 [如何從您自己的網站提供 TwiML 回應](#howto_provide_twiml_responses)。

## <a name="how-to-send-an-sms-message"></a><a id="howto_send_sms"></a>作法：傳送簡訊
以下顯示如何使用 `TwilioRestClient` 類別傳送 SMS 訊息。 **from_number** 號碼由 Twilio 提供給試用帳戶來傳送簡訊。 執行程式碼之前，必須驗證您 Twilio 帳戶的 **to_number** 號碼。

```python
# Import the Twilio Python Client.
from twilio.rest import TwilioRestClient

# Set your account ID and authentication token.
account_sid = "your_twilio_account_sid"
auth_token = "your_twilio_authentication_token"

from_number = "NNNNNNNNNNN"  # With trial account, texts can only be sent from your Twilio number.
to_number = "NNNNNNNNNNN"
message = "Hello world."

# Initialize the Twilio client.
client = TwilioRestClient(account_sid, auth_token)

# Send the SMS message.
message = client.messages.create(to=to_number,
                                    from_=from_number,
                                    body=message)
```

## <a name="how-to-provide-twiml-responses-from-your-own-website"></a><a id="howto_provide_twiml_responses"></a>作法：從您自己的網站提供 TwiML 回應
當您的應用程式開始呼叫 Twilio API 時，Twilio 會將要求傳送至 URL，然後應該會傳回 TwiML 回應。 上述範例使用 Twilio 提供的 URL [https://twimlets.com/message][twimlet_message_url] 。 (雖然 TwiML 是設計給 Twilio 使用的，但您也可以在瀏覽器中檢視 TwiML。 例如，按一下 [https://twimlets.com/message][twimlet_message_url] 以查看空的 `<Response>` 元素，另一個範例 [https://twimlets.com/message?Message%5B0%5D=Hello%20World][twimlet_message_url_hello_world] 是按一下以查看 `<Response>` 包含元素的元素 `<Say>` 。 ) 

除了使用 Twilio 提供的 URL 以外，您也可以建立自己的網站來傳回 HTTP 回應。 您可以使用任何語言建立會傳回 XML 回應的網站；本主題假設您將使用 Python 建立 TwiML。

下列範例將輸出 TwiML 回應，其會在通話中說出 **Hello World**。

使用 Flask：

```python
from flask import Response
@app.route("/")
def hello():
    xml = '<Response><Say>Hello world.</Say></Response>'
    return Response(xml, mimetype='text/xml')
```

使用 Django：

```python
from django.http import HttpResponse
def hello(request):
    xml = '<Response><Say>Hello world.</Say></Response>'
    return HttpResponse(xml, content_type='text/xml')
```

從以上範例中可知，TwiML 回應只是一份 XML 文件。 適用於 Python 的 Twilio 程式庫包含可為您產生 TwiML 的類別。 下列範例會產生同上的回應，但改用適用於 Python 的 Twilio 程式庫中的 `twiml` 模組：

```python
from twilio import twiml

response = twiml.Response()
response.say("Hello world.")
print(str(response))
```

如需 TwiML 的詳細資訊，請參閱 [https://www.twilio.com/docs/api/twiml][twiml_reference] 。

將 Python 應用程式設定來提供 TwiML 回應之後，請使用應用程式的 URL 作為傳遞到 `client.calls.create` 方法的 URL。 例如，如果您有部署到 Azure 託管服務、名為**MyTwiML** 的 Web 應用程式，您可以使用其 URL 作為 webhook，如下列範例所示︰

```python
from twilio.rest import TwilioRestClient

account_sid = "your_twilio_account_sid"
auth_token = "your_twilio_authentication_token"
from_number = "NNNNNNNNNNN"
to_number = "NNNNNNNNNNN"
url = "http://your-domain-label.centralus.cloudapp.azure.com/MyTwiML/"

# Initialize the Twilio client.
client = TwilioRestClient(account_sid, auth_token)

# Make the call.
call = client.calls.create(to=to_number,
                           from_=from_number,
                           url=url)
print(call.sid)
```

## <a name="how-to-use-additional-twilio-services"></a><a id="AdditionalServices"></a>如何：使用其他 Twilio 服務
除了此處所示的範例以外，Twilio 還提供網頁式 API，方便您從 Azure 應用程式中充份利用其他 Twilio 功能。 如需完整詳細資料，請參閱 [Twilio API 文件][twilio_api]。

## <a name="next-steps"></a><a id="NextSteps"></a>後續步驟
了解基本的 Twilio 服務之後，請參考下列連結以取得更多資訊：

* [Twilio 安全性方針][twilio_security_guidelines]
* [Twilio 作法指南與範例程式碼][twilio_howtos]
* [Twilio 快速入門教學課程][twilio_quickstarts]
* [GitHub 上的 Twilio][twilio_on_github]
* [洽詢 Twilio 支援][twilio_support]

[special_offer]: https://ahoy.twilio.com/azure
[twilio_python]: https://github.com/twilio/twilio-python
[twilio_lib_docs]: https://www.twilio.com/docs/libraries/python
[twilio_github_readme]: https://github.com/twilio/twilio-python/blob/master/README.md

[twimlet_message_url]: https://twimlets.com/message
[twimlet_message_url_hello_world]: https://twimlets.com/message?Message%5B0%5D=Hello%20World
[twiml_reference]: https://www.twilio.com/docs/api/twiml
[twilio_pricing]: https://www.twilio.com/pricing

[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api]: https://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_console]:  https://www.twilio.com/console
[twilio_security_guidelines]: https://www.twilio.com/docs/security
[twilio_howtos]: https://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: https://www.twilio.com/help/contact
[twilio_quickstarts]: https://www.twilio.com/docs/quickstart
