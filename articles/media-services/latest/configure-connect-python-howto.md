---
title: 連接到 Azure 媒體服務 v3 API - Python
description: 本文演示如何使用 Python 連接到媒體服務 v3 API。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/15/2019
ms.author: juliako
ms.openlocfilehash: 98a8cdf4120cf56184eb5735249640e3423acdf4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74888456"
---
# <a name="connect-to-media-services-v3-api---python"></a>連接到媒體服務 v3 API - Python

本文介紹如何使用服務主體登錄方法連接到 Azure 媒體服務 v3 Python SDK。

## <a name="prerequisites"></a>Prerequisites

- 從[python.org](https://www.python.org/downloads/)下載 Python
- 確保設置`PATH`環境變數
- [創建媒體服務帳戶](create-account-cli-how-to.md)。 請務必記住資源組名稱和媒體服務帳戶名稱。
- 按照[訪問 API](access-api-cli-how-to.md)主題中的步驟操作。 記錄後面的步驟中所需的訂閱 ID、應用程式 ID（用戶端 ID）、身份驗證金鑰（機密）和租戶 ID。

> [!IMPORTANT]
> 查看[命名約定](media-services-apis-overview.md#naming-conventions)。

## <a name="install-the-modules"></a>安裝模組

要使用 Python 使用 Azure 媒體服務，需要安裝這些模組。

* 該`azure-mgmt-resource`模組，包括活動目錄的 Azure 模組。
* 模組`azure-mgmt-media`，包括媒體服務實體。

打開命令列工具並使用以下命令安裝模組。

```
pip3 install azure-mgmt-resource
pip3 install azure-mgmt-media==1.1.1
```

## <a name="connect-to-the-python-client"></a>連接到 Python 用戶端

1. 使用副檔名`.py`創建檔
1. 在您最喜愛的編輯器中打開檔
1. 將後面的代碼添加到檔中。 代碼導入所需的模組並創建連接到媒體服務所需的 Active Directory 認證物件。

      將變數的值設置為從[Access API](access-api-cli-how-to.md)獲得的值

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

1. 運行檔

## <a name="next-steps"></a>後續步驟

- 使用 [Python SDK](https://aka.ms/ams-v3-python-sdk)。
- 檢閱媒體服務 [Python 參考](https://aka.ms/ams-v3-python-ref)文件。
