---
title: Apache Kafka REST proxy-Azure HDInsight
description: 瞭解如何在 Azure HDInsight 上使用 Kafka REST proxy 執行 Apache Kafka 作業。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/03/2020
ms.openlocfilehash: 265e15713f8159e370ef22a197ffe931200a88f7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81758991"
---
# <a name="interact-with-apache-kafka-clusters-in-azure-hdinsight-using-a-rest-proxy"></a>使用 REST proxy 與 Azure HDInsight 中的 Apache Kafka 叢集互動

Kafka REST Proxy 可讓您透過 HTTP 透過 REST API 與您的 Kafka 叢集互動。 此動作表示您的 Kafka 用戶端可以位於虛擬網路外部。 用戶端可以對 Kafka 叢集進行簡單的 HTTP 呼叫，而不是依賴 Kafka 程式庫。 本文將說明如何建立已啟用 REST proxy 的 Kafka 叢集。 也會提供範例程式碼，說明如何呼叫 REST proxy。

## <a name="rest-api-reference"></a>REST API 參照

如需 Kafka REST API 支援的作業，請參閱[HDInsight KAFKA REST PROXY API 參考](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy)。

## <a name="background"></a>背景

![Kafka REST proxy 設計](./media/rest-proxy/rest-proxy-architecture.png)

如需 API 支援之作業的完整規格，請參閱[Apache KAFKA REST PROXY API](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy)。

### <a name="rest-proxy-endpoint"></a>REST Proxy 端點

建立具有 REST proxy 的 HDInsight Kafka 叢集，會為您的叢集建立新的公用端點，您可以在 Azure 入口網站的 HDInsight 叢集**屬性**中找到此端點。

### <a name="security"></a>安全性

Kafka REST proxy 的存取權是以 Azure Active Directory 安全性群組進行管理。 建立 Kafka 叢集時，請提供具有 REST 端點存取權的 Azure AD 安全性群組。 需要存取 REST proxy 的 Kafka 用戶端，應由群組擁有者向此群組註冊。 群組擁有者可以透過入口網站或透過 PowerShell 註冊。

若是 REST proxy 端點要求，用戶端應用程式應該會取得 OAuth 權杖。 權杖是用來驗證安全性群組成員資格。 尋找下面的[用戶端應用程式範例](#client-application-sample)，其中顯示如何取得 OAuth 權杖。 用戶端應用程式會將 HTTP 要求中的 OAuth 權杖傳遞至 REST proxy。

> [!NOTE]  
> 若要深入瞭解 AAD 安全性群組，請參閱[使用 Azure Active Directory 群組來管理應用程式和資源存取](../../active-directory/fundamentals/active-directory-manage-groups.md)。 如需 OAuth 權杖如何使用的詳細資訊，請參閱[使用 OAuth 2.0 程式碼授與流程來授權存取 Azure Active Directory web 應用程式](../../active-directory/develop/v1-protocols-oauth-code.md)。

## <a name="prerequisites"></a>先決條件

1. 向 Azure AD 註冊應用程式。 您撰寫來與 Kafka REST proxy 互動的用戶端應用程式，將會使用此應用程式的識別碼和密碼向 Azure 進行驗證。

1. 建立 Azure AD 安全性群組。 將您向 Azure AD 註冊的應用程式新增至安全性群組，做為群組的**成員**。 這個安全性群組將用來控制哪些應用程式可以與 REST proxy 互動。 如需建立 Azure AD 群組的詳細資訊，請參閱[建立基本群組和使用 Azure Active Directory 新增成員](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)。

    驗證群組的**安全性**類型。
    ![安全性群組](./media/rest-proxy/rest-proxy-group.png)

    驗證該應用程式是否為群組的成員。
    ![檢查成員資格](./media/rest-proxy/rest-proxy-membergroup.png)

## <a name="create-a-kafka-cluster-with-rest-proxy-enabled"></a>建立已啟用 REST proxy 的 Kafka 叢集

1. 在 Kafka 叢集建立工作流程期間，于 [**安全性 + 網路**] 索引標籤中，選取 [**啟用 Kafka REST proxy** ] 選項。

     ![啟用 Kafka REST proxy 並選取安全性群組](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest.png)

1. 按一下 [**選取安全性群組**]。 從安全性群組清單中，選取您想要存取 REST proxy 的安全性群組。 您可以使用 [搜尋] 方塊來尋找適當的安全性群組。 按一下底部的 [**選取**] 按鈕。

     ![啟用 Kafka REST proxy 並選取安全性群組](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest2.png)

1. 完成其餘步驟來建立您的叢集，如在[使用 Azure 入口網站的 Azure HDInsight 中建立 Apache Kafka 叢集中](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-get-started)所述。

1. 建立叢集之後，請移至叢集屬性以記錄 Kafka REST proxy URL。

     ![view REST proxy URL](./media/rest-proxy/apache-kafka-rest-proxy-view-proxy-url.png)

## <a name="client-application-sample"></a>用戶端應用程式範例

您可以使用下列 python 程式碼，與 Kafka 叢集上的 REST proxy 進行互動。 若要使用程式碼範例，請遵循下列步驟：

1. 將範例程式碼儲存在已安裝 Python 的電腦上。
1. 藉由執行`pip3 install msal`來安裝所需的 python 相依性。
1. 修改程式碼區段**設定這些屬性**，並為您的環境更新下列屬性：

    |屬性 |描述 |
    |---|---|
    |租用戶識別碼|您的訂用帳戶所在的 Azure 租使用者。|
    |用戶端識別碼|您在安全性群組中註冊之應用程式的識別碼。|
    |用戶端密碼|您在安全性群組中註冊之應用程式的密碼。|
    |Kafkarest_endpoint|從叢集總覽的 [**屬性**] 索引標籤取得此值，如[部署一節](#create-a-kafka-cluster-with-rest-proxy-enabled)中所述。 應採用下列格式–`https://<clustername>-kafkarest.azurehdinsight.net`|

1. 從命令列，執行以執行 python 檔案`sudo python3 <filename.py>`

此程式碼會執行下列動作：

1. 從 Azure AD 提取 OAuth token。
1. 示範如何提出要求以 Kafka REST proxy。

如需有關在 python 中取得 OAuth 權杖的詳細資訊，請參閱[Python AuthenticationCoNtext 類別](https://docs.microsoft.com/python/api/adal/adal.authentication_context.authenticationcontext?view=azure-python)。 您可能會看到延遲， `topics`而不是透過 Kafka REST proxy 來建立或刪除，會反映在該處。 這項延遲是因為快取重新整理所造成。

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

在下面的另一個範例中，尋找如何使用捲曲命令從 Azure for REST proxy 取得權杖。 **請注意，在取得`scope=https://hib.azurehdinsight.net/.default`權杖時，我們需要指定的。**

```cmd
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=<clientid>&client_secret=<clientsecret>&grant_type=client_credentials&scope=https://hib.azurehdinsight.net/.default' 'https://login.microsoftonline.com/<tenantid>/oauth2/v2.0/token'
```

## <a name="next-steps"></a>後續步驟

* [Kafka REST proxy API 參考檔](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy/)
