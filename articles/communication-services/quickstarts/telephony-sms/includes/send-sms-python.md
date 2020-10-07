---
title: 包含檔案
description: 包含檔案
services: azure-communication-services
author: danieldoolabh
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 09/03/2020
ms.topic: include
ms.custom: include file
ms.author: dadoolab
ms.openlocfilehash: 9265caa3054cde6af311e655db2b2496524e8d24
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/06/2020
ms.locfileid: "91757110"
---
藉由使用通訊服務 Python SMS 用戶端程式庫來傳送 SMS 訊息，以開始使用 Azure 通訊服務。

完成本快速入門後，您的 Azure 帳戶中會產生幾美分或更少的少許費用。

<!--**TODO: update all these reference links as the resources go live**

[API reference documentation](../../../references/overview.md) | [Library source code](#todo-sdk-repo) | [Package (PiPy)](#todo-nuget) | [Samples](#todo-samples)--> 

## <a name="prerequisites"></a>必要條件

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
- [Python](https://www.python.org/downloads/) 2.7、3.5 或更新版本。
- 作用中的 Azure 通訊服務資源和連接字串。 [建立通訊服務資源](../../create-communication-resource.md)。
- 已啟用 SMS 的電話號碼。 [取得電話號碼](../get-phone-number.md)。

### <a name="prerequisite-check"></a>先決條件檢查

- 在終端機或命令視窗中執行 `python --version` 命令，確認已安裝 Python。
- 若要檢視與您通訊服務資源相關聯的電話號碼，請登入 [Azure 入口網站](https://portal.azure.com/)、尋找您的通訊服務資源，然後從左側瀏覽窗格開啟 [電話號碼] 索引標籤。

## <a name="setting-up"></a>設定

### <a name="create-a-new-python-application"></a>建立新的 Python 應用程式

開啟您的終端機或命令視窗，為您的應用程式建立新的目錄，並瀏覽至該目錄。

```console
mkdir sms-quickstart && cd sms-quickstart
```

使用文字編輯器，在專案根目錄中建立名為 **send-sms.py** 的檔案，然後新增程式的結構，包括基本例外狀況處理。 您會在下列各節中，將本快速入門的所有原始程式碼新增至此檔案。

```python
import os
from azure.communication.sms import PhoneNumber
from azure.communication.sms import SendSmsOptions
from azure.communication.sms import SmsClient

try:
    # Quickstart code goes here
except Exception as ex:
    print('Exception:')
    print(ex)
```

### <a name="install-the-package"></a>安裝套件

若您仍在應用程式目錄中，請使用 `pip install` 命令安裝適用於 Python 套件的 Azure 通訊服務 SMS 用戶端程式庫。

```console
pip install azure-communication-sms
```

## <a name="object-model"></a>物件模型

下列類別和介面會處理 Azure 通訊服務 SMS 用戶端程式庫的一些主要 Python 功能。

| Name                                  | 描述                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| SmsClient | 這是所有 SMS 功能所需的類別。 您可以使用您的訂用帳戶資訊將其具現化，並用來傳送 SMS 訊息。 |
| SendSmsOptions | 此類別提供設定傳遞報告的選項。 如果 enable_delivery_report 設定為 True，則事件會在傳遞成功時發出。 |

## <a name="authenticate-the-client"></a>驗證用戶端

使用連接字串具現化 **SmsClient**。 以下程式碼會從名為 `COMMUNICATION_SERVICES_CONNECTION_STRING` 的環境變數中，擷取資源的連接字串。 了解如何[管理資源的連接字串](../../create-communication-resource.md#store-your-connection-string)。

```python
# This code demonstrates how to fetch your connection string
# from an environment variable.
connection_string = os.getenv('COMMUNICATION_SERVICES_CONNECTION_STRING')

# Create the SmsClient object which will be used to send SMS messages
sms_client = SmsClient.from_connection_string(connection_string)
```

## <a name="send-an-sms-message"></a>傳送 SMS 訊息

呼叫 Send 方法，以傳送 SMS 訊息。 將此程式碼加入到 **send-sms.py** 中 `try` 區塊的結尾處：

```python

# calling send() with sms values
sms_response = sms_client.send(
        from_phone_number=PhoneNumber("<leased-phone-number>"),
        to_phone_numbers=[PhoneNumber("<to-phone-number>")],
        message="Hello World via SMS",
        send_sms_options=SendSmsOptions(enable_delivery_report=True)) # optional property

```

您應該將 `<leased-phone-number>` 取代為與您通訊服務相關聯且已啟用 SMS 的電話號碼，並使用您想要對其傳送訊息的電話號碼取代 `<to-phone-number>`。 

`send_sms_options` 參數是選擇性參數，可讓您用來設定傳遞報告。 這適用於想要在傳遞 SMS 訊息時發出事件的案例。 請參閱[處理 SMS 事件](../handle-sms-events.md)快速入門，以設定 SMS 訊息的傳遞報告。

## <a name="run-the-code"></a>執行程式碼

使用 `python` 命令從您的應用程式目錄執行應用程式。

```console
python send-sms.py
```
