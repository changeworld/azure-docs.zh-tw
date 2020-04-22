---
title: 阿帕奇卡夫卡 REST 代理 - Azure HDInsight
description: 瞭解如何在 Azure HDInsight 上使用 Kafka REST 代理執行 Apache Kafka 操作。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/03/2020
ms.openlocfilehash: 265e15713f8159e370ef22a197ffe931200a88f7
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758991"
---
# <a name="interact-with-apache-kafka-clusters-in-azure-hdinsight-using-a-rest-proxy"></a>使用 REST 代理與 Azure HDInsight 中的 Apache Kafka 叢集互動

Kafka REST 代理使您能夠通過 REST API 通過 HTTP 與卡夫卡群集進行交互。 此操作意味著您的 Kafka 用戶端可以位於虛擬網路之外。 用戶端可以對 Kafka 群集進行簡單的 HTTP 調用,而不是依賴於卡夫卡庫。 本文將介紹如何創建啟用 REST 代理的 Kafka 群集。 還提供了一個範例代碼,演示如何調用 REST 代理。

## <a name="rest-api-reference"></a>REST API 參考資料

有關卡夫卡 REST API 支援的操作,請參閱[HDInsight 卡夫卡 REST 代理 API 參考](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy)。

## <a name="background"></a>背景

![卡夫卡 REST 代理程式](./media/rest-proxy/rest-proxy-architecture.png)

有關 API 支援的操作的完整規範,請參閱[Apache Kafka REST 代理 API](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy)。

### <a name="rest-proxy-endpoint"></a>REST 代理終結點

使用 REST 代理創建 HDInsight Kafka 群集會為叢集創建新的公共終結點,您可以在 Azure 門戶上的 HDInsight 群集**屬性**中找到該終結點。

### <a name="security"></a>安全性

使用 Azure 活動目錄安全組管理對 Kafka REST 代理的訪問。 創建 Kafka 群集時,請向 Azure AD 安全組提供 REST 終結點訪問許可權。 需要存取 REST 代理的 Kafka 客戶端應由組擁有者註冊到此組。 組擁有者可以通過門戶或通過 PowerShell 進行註冊。

對於 REST 代理終結點請求,用戶端應用程式應獲取 OAuth 權杖。 令牌用於驗證安全組成員資格。 尋找下面的[客戶端應用程式範例](#client-application-sample),該示例示範如何取得 OAuth 權杖。 用戶端應用程式將 HTTP 請求中的 OAuth 權杖傳遞給 REST 代理。

> [!NOTE]  
> 請參閱[使用 Azure 活動目錄組管理應用和資源訪問](../../active-directory/fundamentals/active-directory-manage-groups.md),以瞭解有關 AAD 安全組詳細資訊。 有關 OAuth 權杖的工作原理的詳細資訊,請參閱使用[OAuth 2.0 碼授權存取 Azure 活動目錄 Web 應用程式](../../active-directory/develop/v1-protocols-oauth-code.md)。

## <a name="prerequisites"></a>Prerequisites

1. 向 Azure AD 註冊應用程式。 您為與 Kafka REST 代理互動而編寫的用戶端應用程式將使用此應用程式的 ID 和機密對 Azure 進行身份驗證。

1. 創建 Azure AD 安全組。 將 Azure AD 註冊的應用程式新增到安全群組,作為群組**的成員**。 此安全組將用於控制允許哪些應用程式與 REST 代理進行互動。 有關建立 Azure AD 群組的詳細資訊,請參閱[建立基本群組並使用 Azure 活動目錄新增成員](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)。

    驗證組的類型**為"安全**"。
    ![安全性群組](./media/rest-proxy/rest-proxy-group.png)

    驗證應用程式是組的成員。
    ![檢查會員資格](./media/rest-proxy/rest-proxy-membergroup.png)

## <a name="create-a-kafka-cluster-with-rest-proxy-enabled"></a>建立開啟 REST 代理的卡夫卡群集

1. 在 Kafka 叢集建立工作流期間,在 **「安全+ 網路**」選項卡中,選中**啟用 Kafka REST 代理**選項。

     ![開啟 Kafka REST 代理並選擇安全性群組](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest.png)

1. 按下 **「選擇安全組**」。 從安全組清單中,選擇要有權訪問 REST 代理的安全組。 您可以使用搜尋框尋找相應的安全組。 按下底部的 **「選擇」** 按鈕。

     ![開啟 Kafka REST 代理並選擇安全性群組](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest2.png)

1. 完成其餘步驟,使用[Azure 門戶 在 Azure HDInsight 中創建 Apache Kafka 群集中](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-get-started)所述,請創建群集。

1. 創建群集後,轉到群集屬性以記錄 Kafka REST 代理 URL。

     ![檢視 REST 代理網址](./media/rest-proxy/apache-kafka-rest-proxy-view-proxy-url.png)

## <a name="client-application-sample"></a>用戶端應用程式範例

您可以使用下面的 python 代碼與 Kafka 群集上的 REST 代理進行互動。 要使用代碼範例,請按照以下步驟操作:

1. 在安裝了 Python 的電腦上保存範例代碼。
1. 通過 執行安裝所需的 python`pip3 install msal`依賴項。
1. 變更代碼部分**設定這些屬性**並更新環境的以下屬性:

    |屬性 |描述 |
    |---|---|
    |租用戶識別碼|訂閱所在的 Azure 租戶。|
    |用戶端識別碼|您在安全組中註冊的應用程式的 ID。|
    |用戶端密碼|您在安全組中註冊的應用程式的機密。|
    |Kafkarest_endpoint|從群集概述中的 **「屬性」** 選項卡獲取此值,如[部署部分](#create-a-kafka-cluster-with-rest-proxy-enabled)所述。 它應採用以下格式 |`https://<clustername>-kafkarest.azurehdinsight.net`|

1. 從命令列中,透過執行 python 檔來執行 python 檔案`sudo python3 <filename.py>`

此程式碼執行以下操作:

1. 從 Azure AD 獲取 OAuth 權杖。
1. 演示如何向 Kafka REST 代理發出請求。

有關在 python 中取得 OAuth 權杖的詳細資訊,請參考[Python 身份驗證內容 。](https://docs.microsoft.com/python/api/adal/adal.authentication_context.authenticationcontext?view=azure-python) 您可能會看到延遲,而`topics`未通過 Kafka REST 代理創建或刪除的延遲將反映在其中。 此延遲是由於緩存刷新。

```python
#Required python packages
#pip3 install msal

import msal

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

# Scope
scope = 'https://hib.azurehdinsight.net/.default'
#Authority
authority = 'https://login.microsoftonline.com/' + tenant_id

# Create a preferably long-lived app instance which maintains a token cache.
app = msal.ConfidentialClientApplication(
    client_id , client_secret, authority,
    #cache - For details on how look at this example: https://github.com/Azure-Samples/ms-identity-python-webapp/blob/master/app.py
    )

# The pattern to acquire a token looks like this.
result = None

result = app.acquire_token_for_client(scopes=[scope])

print(result)
accessToken = result['access_token']

# relative url
getstatus = "/v1/metadata/topics"
request_url = kafkarest_endpoint + getstatus

# sending get request and saving the response as response object
response = requests.get(request_url, headers={'Authorization': accessToken})
print(response.content)
```

下面查找有關如何使用 curl 命令從 AZURE 獲取 REST 代理權杖的另一個範例。 **請注意,我們需要在`scope=https://hib.azurehdinsight.net/.default`獲取令牌時指定。**

```cmd
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=<clientid>&client_secret=<clientsecret>&grant_type=client_credentials&scope=https://hib.azurehdinsight.net/.default' 'https://login.microsoftonline.com/<tenantid>/oauth2/v2.0/token'
```

## <a name="next-steps"></a>後續步驟

* [卡夫卡 REST 代理 API 參考文件](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy/)
