---
title: 包含檔案
description: 包含檔案
services: azure-communication-services
author: tomaschladek
manager: nmurav
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: tchladek
ms.openlocfilehash: e307265cc95815f426317cee69d64b210bcd67a9
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/11/2020
ms.locfileid: "94506231"
---
## <a name="prerequisites"></a>必要條件

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Python](https://www.python.org/downloads/) 2.7、3.5 或更新版本。
- 作用中的 Azure 通訊服務資源和連接字串。 [建立通訊服務資源](../create-communication-resource.md)。

## <a name="setting-up"></a>設定

### <a name="create-a-new-python-application"></a>建立新的 Python 應用程式

1. 開啟您的終端機或命令視窗，為您的應用程式建立新的目錄，並瀏覽至該目錄。

   ```console
   mkdir access-tokens-quickstart && cd access-tokens-quickstart
   ```

1. 使用文字編輯器，在專案根目錄中建立名為 **issue-access-tokens.py** 的檔案，然後新增程式的結構，包括基本例外狀況處理。 您會在下列各節中，將本快速入門的所有原始程式碼新增至此檔案。

   ```python
   import os
   from azure.communication.administration import CommunicationIdentityClient

   try:
      print('Azure Communication Services - Access Tokens Quickstart')
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

## <a name="create-an-identity"></a>建立身分識別

Azure 通訊服務會維護輕量身分識別目錄。 使用 `create_user` 方法，在目錄中建立具有唯一 `Id` 的新項目。 儲存已接收的身分識別，並對應至您應用程式的使用者。 例如，將其儲存在您應用程式伺服器的資料庫中。 後續將需要以身分識別簽發存取權杖。

```python
identity = client.create_user()
print("\nCreated an identity with ID: " + identity.identifier + ":")
```

## <a name="issue-access-tokens"></a>發行存取權杖

使用 `issue_token` 方法來簽發現有通訊服務身分識別的存取權杖。 參數 `scopes` 會定義一組基本類型，進行此存取權杖的授權。 請參閱[支援的動作清單](../../concepts/authentication.md)。 您可以根據 Azure 通訊服務身分識別的字串表示法，建立參數 `communicationUser` 的新執行個體。

```python
# Issue an access token with the "voip" scope for an identity
token_result = client.issue_token(user, ["voip"])
expires_on = token_result.expires_on.strftime('%d/%m/%y %I:%M %S %p')
print("\nIssued an access token with 'voip' scope that expires at " + expires_on + ":")
print(token_result.token)
```

存取權杖是需要重新簽發的短期認證。 若未這麼做，可能會導致應用程式的使用者體驗中斷。 `expires_on` 回應屬性會指出存取權杖的存留期。

## <a name="refresh-access-tokens"></a>重新整理存取權杖

若要重新整理存取權杖，請使用 `CommunicationUser` 物件來重新簽發：

```python  
# Value existingIdentity represents identity of Azure Communication Services stored during identity creation
identity = CommunicationUser(existingIdentity)
token_result = client.issue_token( identity, ["voip"])
```

## <a name="revoke-access-tokens"></a>撤銷存取權杖

在某些情況下，您可能要明確地撤銷存取權杖。 例如，當應用程式的使用者變更用來向您的服務進行驗證的密碼時。 方法 `revoke_tokens` 會使簽發給身分識別的所有作用中存取權杖失效。

```python  
client.revoke_tokens(identity)
print("\nSuccessfully revoked all access tokens for identity with ID: " + identity.identifier)
```

## <a name="delete-an-identity"></a>刪除身分識別

刪除身分識別會撤銷所有作用中的存取權杖，並防止您核發身分識別的存取權杖。 此外也會移除所有與身分識別相關聯的保存內容。

```python
client.delete_user(identity)
print("\nDeleted the identity with ID: " + identity.identifier)
```

## <a name="run-the-code"></a>執行程式碼

從主控台提示中，瀏覽至包含 *issue-access-token.py* 檔案的目錄，然後執行下列 `python` 命令以執行應用程式。

```console
python ./issue-access-token.py
```
