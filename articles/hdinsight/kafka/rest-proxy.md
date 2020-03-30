---
title: 阿帕奇卡夫卡 REST 代理 - Azure HDInsight
description: 瞭解如何在 Azure HDInsight 上使用 Kafka REST 代理執行 Apache Kafka 操作。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: d99a3b803b80dc41990a63e647d3ba928deb31af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77198900"
---
# <a name="interact-with-apache-kafka-clusters-in-azure-hdinsight-using-a-rest-proxy"></a>使用 REST 代理與 Azure HDInsight 中的 Apache Kafka 群集進行交互

Kafka REST 代理使您能夠通過 REST API 通過 HTTP 與卡夫卡群集進行交互。 這意味著您的 Kafka 用戶端可以位於虛擬網路之外。 此外，用戶端可以進行簡單的 HTTP 調用，以向 Kafka 群集發送和接收消息，而不是依賴 Kafka 庫。 本教程將介紹如何創建啟用 REST 代理的 Kafka 群集，並提供示例代碼，演示如何調用 REST 代理。

## <a name="rest-api-reference"></a>REST API 參考資料

有關卡夫卡 REST API 支援的操作的完整規範，請參閱[HDInsight Kafka REST 代理 API 參考](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy)。

## <a name="background"></a>背景

![卡夫卡 REST 代理體系結構](./media/rest-proxy/rest-proxy-architecture.png)

有關 API 支援的操作的完整規範，請參閱 Apache [Kafka REST 代理 API](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy)。

### <a name="rest-proxy-endpoint"></a>REST 代理終結點

使用 REST 代理創建 HDInsight Kafka 群集會為群集創建新的公共終結點，您可以在 Azure 門戶上的 HDInsight 群集"屬性"中找到該終結點。

### <a name="security"></a>安全性

使用 Azure 活動目錄安全性群組管理對 Kafka REST 代理的訪問。 在啟用 REST 代理的情況下創建 Kafka 群集時，將提供應有權訪問 REST 終結點的 Azure 活動目錄安全性群組。 需要訪問 REST 代理的 Kafka 用戶端（應用程式）應由組擁有者註冊到此組。 組擁有者可以通過門戶或通過 Powershell 執行此操作。

在向 REST 代理終結點發出請求之前，用戶端應用程式應獲取 OAuth 權杖以驗證正確的安全性群組的成員身份。 請在下面找到[一個用戶端應用程式示例](#client-application-sample)，其中演示如何獲取 OAuth 權杖。 一旦用戶端應用程式具有 OAuth 權杖，它們就必須在提供給 REST 代理的 HTTP 要求中傳遞該權杖。

> [!NOTE]  
> 請參閱[使用 Azure 活動目錄組管理應用和資源訪問](../../active-directory/fundamentals/active-directory-manage-groups.md)，以瞭解有關 AAD 安全性群組詳細資訊。 有關 OAuth 權杖的工作原理的詳細資訊，請參閱使用[OAuth 2.0 代碼授予流授權訪問 Azure 活動目錄 Web 應用程式](../../active-directory/develop/v1-protocols-oauth-code.md)。

## <a name="prerequisites"></a>Prerequisites

1. 向 Azure AD 註冊應用程式。 您為與 Kafka REST 代理交互而編寫的用戶端應用程式將使用此應用程式的 ID 和機密對 Azure 進行身份驗證。
1. 創建 Azure AD 安全性群組，並將已註冊到 Azure AD 的應用程式添加到安全性群組。 此安全性群組將用於控制允許哪些應用程式與 REST 代理進行交互。 有關創建 Azure AD 組的詳細資訊，請參閱[創建基本組並使用 Azure 活動目錄添加成員](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)。

## <a name="create-a-kafka-cluster-with-rest-proxy-enabled"></a>創建啟用 REST 代理的卡夫卡群集

1. 在 Kafka 群集創建工作流期間，在"安全 + 網路"選項卡中，選中"啟用 Kafka REST 代理"選項。

     ![啟用 Kafka REST 代理並選擇安全性群組](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest.png)

1. 按一下 **"選擇安全性群組**"。 從安全性群組清單中，選擇要有權訪問 REST 代理的安全性群組。 您可以使用搜索框查找相應的安全性群組。 按一下底部的 **"選擇"** 按鈕。

     ![啟用 Kafka REST 代理並選擇安全性群組](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest2.png)

1. 完成其餘步驟，使用[Azure 門戶 在 Azure HDInsight 中創建 Apache Kafka 群集中](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-get-started)所述，請創建群集。

1. 創建群集後，轉到群集屬性以記錄 Kafka REST 代理 URL。

     ![查看 REST 代理 URL](./media/rest-proxy/apache-kafka-rest-proxy-view-proxy-url.png)

## <a name="client-application-sample"></a>用戶端應用程式示例

您可以使用下面的 python 代碼與 Kafka 群集上的 REST 代理進行交互。 要使用代碼示例，請按照以下步驟操作：

1. 在安裝了 Python 的電腦上保存示例代碼。
1. 通過 執行`pip3 install adal`和`pip install msrestazure`安裝所需的 python 依賴項。
1. 修改代碼部分*配置這些屬性*並更新環境的以下屬性：
    1.  *租戶 ID* = 訂閱所在的 Azure 租戶。
    1.  *用戶端 ID* = 在安全性群組中註冊的應用程式的 ID。
    1.  *用戶端金鑰*- 您在安全性群組中註冊的應用程式的機密
    1.  *Kafkarest_endpoint* = 從群集概述中的"屬性"選項卡獲取此值，如[部署部分](#create-a-kafka-cluster-with-rest-proxy-enabled)所述。 它應採用以下格式 |`https://<clustername>-kafkarest.azurehdinsight.net`
3. 從命令列中，通過執行 python 檔來執行 python 檔`python <filename.py>`

此程式碼會執行以下動作：

1. 從 Azure AD 獲取 OAuth 權杖
1. 演示如何向卡夫卡 REST 代理髮出請求

有關在 python 中獲取 OAuth 權杖的詳細資訊，請參閱[Python 身份驗證上下文類](https://docs.microsoft.com/python/api/adal/adal.authentication_context.authenticationcontext?view=azure-python)。 您可能會看到延遲，而未通過 Kafka REST 代理創建或刪除的主題將反映在那裡。 此延遲是由於緩存刷新。

```python
#Required python packages
#pip3 install adal
#pip install msrestazure

import adal
from msrestazure.azure_active_directory import AdalAuthentication
from msrestazure.azure_cloud import AZURE_PUBLIC_CLOUD
import requests

#--------------------------Configure these properties-------------------------------#
# Tenant ID for your Azure Subscription
tenant_id = 'ABCDEFGH-1234-1234-1234-ABCDEFGHIJKL'
# Your Client Application Id
client_id = 'XYZABCDE-1234-1234-1234-ABCDEFGHIJKL'
# Your Client Credentials
client_secret = 'password'
# kafka rest proxy -endpoint
kafkarest_endpoint = "https://<clustername>-kafkarest.azurehdinsight.net"
#--------------------------Configure these properties-------------------------------#

#getting token
login_endpoint = AZURE_PUBLIC_CLOUD.endpoints.active_directory
resource = "https://hib.azurehdinsight.net"
context = adal.AuthenticationContext(login_endpoint + '/' + tenant_id)

token = context.acquire_token_with_client_credentials(
    resource,
    client_id,
    client_secret)

accessToken = 'Bearer ' + token['accessToken']

print(accessToken)

# relative url
getstatus = "/v1/metadata/topics"
request_url = kafkarest_endpoint + getstatus

# sending get request and saving the response as response object
response = requests.get(request_url, headers={'Authorization': accessToken})
print(response.content)
```

## <a name="next-steps"></a>後續步驟

* [卡夫卡 REST 代理 API 參考文檔](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy/)
