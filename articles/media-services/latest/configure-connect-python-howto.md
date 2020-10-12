---
title: 連接到 Azure 媒體服務 v3 API-Python
description: 本文將示範如何使用 Python 連線至媒體服務 v3 API。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-python
ms.openlocfilehash: 68620b644ef85f7b627b7b6380c16959b25299f0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89297326"
---
# <a name="connect-to-media-services-v3-api---python"></a>連接至媒體服務 v3 API-Python

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

本文說明如何使用服務主體登入方法連接到 Azure 媒體服務 v3 Python SDK。

## <a name="prerequisites"></a>必要條件

- 從[python.org](https://www.python.org/downloads/)下載 Python
- 請務必設定 `PATH` 環境變數
- [建立媒體服務帳戶](./create-account-howto.md)。 請務必記住資源群組名稱和「媒體服務」帳戶名稱。
- 請依照[存取 API](./access-api-howto.md) 主題中的步驟進行。 記錄訂用帳戶識別碼、應用程式識別碼 (用戶端識別碼) 、驗證金鑰 (秘密) ，以及您在稍後的步驟中需要的租使用者識別碼。

> [!IMPORTANT]
> 檢閱[命名慣例](media-services-apis-overview.md#naming-conventions)。

## <a name="install-the-modules"></a>安裝模組

若要使用 Python 來處理 Azure 媒體服務，您必須安裝這些模組。

* 此 `azure-mgmt-resource` 模組包含適用于 Active Directory 的 Azure 模組。
* `azure-mgmt-media`包含媒體服務實體的模組。

開啟命令列工具，並使用下列命令來安裝模組。

```
pip3 install azure-mgmt-resource
pip3 install azure-mgmt-media==1.1.1
```

## <a name="connect-to-the-python-client"></a>連接至 Python 用戶端

1. 建立副檔名為的檔案 `.py`
1. 在您最愛的編輯器中開啟檔案
1. 將後面的程式碼新增至檔案。 程式碼會匯入所需的模組，並建立您連接到媒體服務所需的 Active Directory 認證物件。

      將變數的值設定為您從[存取 api](./access-api-howto.md)取得的值

      ```
      import adal
      from msrestazure.azure_active_directory import AdalAuthentication
      from msrestazure.azure_cloud import AZURE_PUBLIC_CLOUD
      from azure.mgmt.media import AzureMediaServices
      from azure.mgmt.media.models import MediaService

      RESOURCE = 'https://management.core.windows.net/'
      # Tenant ID for your Azure Subscription
      TENANT_ID = '00000000-0000-0000-000000000000'
      # Your Service Principal App ID
      CLIENT = '00000000-0000-0000-000000000000'
      # Your Service Principal Password
      KEY = '00000000-0000-0000-000000000000'
      # Your Azure Subscription ID
      SUBSCRIPTION_ID = '00000000-0000-0000-000000000000'
      # Your Azure Media Service (AMS) Account Name
      ACCOUNT_NAME = 'amsv3account'
      # Your Resource Group Name
      RESOUCE_GROUP_NAME = 'AMSv3ResourceGroup'

      LOGIN_ENDPOINT = AZURE_PUBLIC_CLOUD.endpoints.active_directory
      RESOURCE = AZURE_PUBLIC_CLOUD.endpoints.active_directory_resource_id

      context = adal.AuthenticationContext(LOGIN_ENDPOINT + '/' + TENANT_ID)
      credentials = AdalAuthentication(
          context.acquire_token_with_client_credentials,
          RESOURCE,
          CLIENT,
          KEY
      )

      # The AMS Client
      # You can now use this object to perform different operations to your AMS account.
      client = AzureMediaServices(credentials, SUBSCRIPTION_ID)

      print("signed in")

      # Now that you are authenticated, you can manipulate the entities.
      # For example, list assets in your AMS account
      print (client.assets.list(RESOUCE_GROUP_NAME, ACCOUNT_NAME).get(0))
      ```

1. 執行檔案

## <a name="next-steps"></a>後續步驟

- 使用 [PYTHON SDK](https://aka.ms/ams-v3-python-sdk)。
- 檢閱媒體服務 [Python 參考](https://aka.ms/ams-v3-python-ref)文件。
