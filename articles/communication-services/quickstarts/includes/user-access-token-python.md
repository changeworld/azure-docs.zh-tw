---
title: 包含檔案
description: 包含檔案
services: azure-communication-services
author: matthewrobertson
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: marobert
ms.openlocfilehash: 4be8821a949527fefcc9005b1de7f4f7c438c568
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90943886"
---
## <a name="prerequisites"></a>必要條件

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Python](https://www.python.org/downloads/) 2.7、3.5 或更新版本。
- 作用中的 Azure 通訊服務資源和連接字串。 [建立通訊服務資源](../create-communication-resource.md)。

## <a name="setting-up"></a>設定

### <a name="create-a-new-python-application"></a>建立新的 Python 應用程式

1. 開啟您的終端機或命令視窗，為您的應用程式建立新的目錄，並瀏覽至該目錄。

   ```console
   mkdir user-tokens-quickstart && cd user-tokens-quickstart
   ```

1. 使用文字編輯器，在專案根目錄中建立名為 **issue-tokens.py** 的檔案，然後新增程式的結構，包括基本例外狀況處理。 您會在下列各節中，將本快速入門的所有原始程式碼新增至此檔案。

   ```python
   import os
   from azure.communication.administration import CommunicationIdentityClient

   try:
      print('Azure Communication Services - User Access Tokens Quickstart')
      # Quickstart code goes here
   except Exception as ex:
      print('Exception:')
      print(ex)
   ```

### <a name="install-the-package"></a>安裝套件

若您仍在應用程式目錄中，請使用 `pip install` 命令安裝適用於 Python 套件的 Azure 通訊服務系統管理用戶端程式庫。

```console
pip install azure-communication-administration
```

[!INCLUDE [User Access Tokens Object Model](user-access-tokens-object-model.md)]

## <a name="authenticate-the-client"></a>驗證用戶端

使用連接字串具現化 `CommunicationIdentityClient`。 以下程式碼會從名為 `COMMUNICATION_SERVICES_CONNECTION_STRING` 的環境變數中，擷取資源的連接字串。 了解如何[管理資源的連接字串](../create-communication-resource.md#store-your-connection-string)。

在 `try` 區塊內加入此程式碼：

```python
# This code demonstrates how to fetch your connection string
# from an environment variable.
connection_string = os.environ['COMMUNICATION_SERVICES_CONNECTION_STRING']

# Instantiate the identity client
client = CommunicationIdentityClient.from_connection_string(connection_string)
```

## <a name="create-a-user"></a>建立使用者

Azure 通訊服務會維護輕量身分識別目錄。 使用 `create_user` 方法，在目錄中建立具有唯一 `Id` 的新項目。 您應該維護應用程式使用者與通訊服務所產生身分識別之間的對應 (例如，將其儲存在應用程式伺服器的資料庫中)。

```python
user = client.create_user()
print("\nCreated a user with ID: " + user.identifier + ":")
```

## <a name="issue-user-access-tokens"></a>發行使用者存取權杖

使用 `issue_token` 方法來發行通訊服務使用者的存取權杖。 如果您未提供選擇性的 `user` 參數，則會建立新的使用者，並連同權杖一起傳回。

```python
# Issue an access token with the "voip" scope for a new user
token_result = client.issue_token(user, ["voip"])
expires_on = token_result.expires_on.strftime('%d/%m/%y %I:%M %S %p')
print("\nIssued a token with 'voip' scope that expires at " + expires_on + ":")
print(token_result.token)
```

使用者存取權杖是短期的認證，需要重新發行才能防止使用者發生服務中斷。 `expires_on` 回應屬性會指出權杖的存留期。

## <a name="revoke-user-access-tokens"></a>撤銷使用者存取權杖

在某些情況下，您可能需要明確撤銷使用者存取權杖，例如，當使用者變更用來向服務進行驗證的密碼時。 這是透過 Azure 通訊服務系統管理用戶端程式庫提供的功能。

```python  
client.revoke_tokens(user)
print("\nSuccessfully revoked all tokens for user with ID: " + user.identifier)
```

## <a name="delete-a-user"></a>刪除使用者

刪除身分識別會撤銷所有作用中的權杖，並防止您發行身分識別的後續權杖。 也會移除所有與使用者相關聯的保存內容。

```python
client.delete_user(user)
print("\nDeleted the user with ID: " + user.identifier)
```

## <a name="run-the-code"></a>執行程式碼

從主控台提示中，瀏覽至包含 issue-token.py 檔案的目錄，然後執行下列 `python` 命令來執行應用程式。

```console
python ./issue-token.py
```
