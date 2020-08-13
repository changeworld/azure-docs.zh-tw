---
title: Apache Kafka REST Proxy - Azure HDInsight
description: 了解如何在 Azure HDInsight 上使用 Kafka REST Proxy 執行 Apache Kafka 作業。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: how-to
ms.custom: has-adal-ref, devx-track-python
ms.date: 04/03/2020
ms.openlocfilehash: 57c2fb125547149a7fea6643a483e29f5fecb495
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2020
ms.locfileid: "88167040"
---
# <a name="interact-with-apache-kafka-clusters-in-azure-hdinsight-using-a-rest-proxy"></a>使用 REST Proxy 與 Azure HDInsight 中的 Apache Kafka 叢集互動

Kafka REST Proxy 可讓您使用 REST API 透過 HTTP 與您的 Kafka 叢集互動。 此動作表示您的 Kafka 用戶端可以位於虛擬網路外部。 用戶端可以對 Kafka 叢集進行簡單的 HTTP 呼叫，而不用依賴 Kafka 程式庫。 本文將說明如何建立已啟用 REST Proxy 的 Kafka 叢集。 也會提供範例程式碼，說明如何呼叫 REST Proxy。

## <a name="rest-api-reference"></a>REST API 參考資料

若要了解 Kafka REST API 支援的作業，請參閱 [HDInsight Kafka REST Proxy API 參考](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy)。

## <a name="background"></a>背景

![Kafka REST Proxy 設計](./media/rest-proxy/rest-proxy-architecture.png)

如需 API 所支援作業的完整規格，請參閱 [Apache Kafka REST Proxy API](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy)。

### <a name="rest-proxy-endpoint"></a>REST Proxy 端點

建立具有 REST Proxy 的 HDInsight Kafka 叢集，會為您的叢集建立新的公用端點，其位於 Azure 入口網站上的 HDInsight 叢集**屬性**中。

### <a name="security"></a>安全性

Kafka REST Proxy 的存取權會以 Azure Active Directory 安全性群組進行管理。 建立 Kafka 叢集時，請提供具有 REST 端點存取權的 Azure AD 安全性群組。 應由此群組的擁有者將需要存取 REST Proxy 的 Kafka 用戶端註冊到群組。 群組擁有者可以透過入口網站或透過 PowerShell 註冊。

針對 REST Proxy 端點要求，用戶端應用程式應該會取得 OAuth 權杖。 權杖會用來驗證安全性群組成員資格。 請在下方尋找[用戶端應用程式範例](#client-application-sample)，其中會說明如何取得 OAuth 權杖。 用戶端應用程式會將 HTTP 要求中的 OAuth 權杖傳遞至 REST Proxy。

> [!NOTE]
> 若要深入了解 AAD 安全性群組，請參閱[使用 Azure Active Directory 群組來管理應用程式和資源存取](../../active-directory/fundamentals/active-directory-manage-groups.md)。 如需有關 OAuth 權杖使用方式的詳細資訊，請參閱[使用 OAuth 2.0 授權碼授與流程，授權存取 Azure Active Directory Web 應用程式](../../active-directory/develop/v1-protocols-oauth-code.md)。

## <a name="kafka-rest-proxy-with-network-security-groups"></a>搭配網路安全性群組使用 Kafka REST Proxy
如果您使用網路安全性群組來攜帶自己的 VNet 並控制網路流量，除了連接埠 443 以外，請允許連接埠 **9400** 上的**輸入**流量。 這可確保可觸達 Kafka REST Proxy 伺服器。

## <a name="prerequisites"></a>Prerequisites

1. 向 Azure AD 註冊應用程式。 您撰寫來與 Kafka REST Proxy 互動的用戶端應用程式，將使用此應用程式的識別碼和密碼向 Azure 進行驗證。

1. 建立 Azure AD 安全性群組。 將您向 Azure AD 註冊的應用程式新增到安全性群組，以作為該群組的**成員**。 此安全性群組將用來控制哪些應用程式能與 REST Proxy 互動。 如需建立 Azure AD 群組的詳細資訊，請參閱[使用 Azure Active Directory 建立基本群組和新增成員](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)。

    確認群組屬於**安全性**類型。
    ![安全性群組](./media/rest-proxy/rest-proxy-group.png)

    確認該應用程式是群組的成員。
    ![檢查成員資格](./media/rest-proxy/rest-proxy-membergroup.png)

## <a name="create-a-kafka-cluster-with-rest-proxy-enabled"></a>建立已啟用 REST Proxy 的 Kafka 叢集

下列步驟是使用 Azure 入口網站。 如需使用 Azure CLI 的範例，請參閱[使用 Azure CLI 建立 Apache Kafka REST Proxy 叢集](tutorial-cli-rest-proxy.md)。

1. 在建立 Kafka 叢集的工作流程中，於 [安全性 + 網路] 索引標籤中，勾選 [啟用 Kafka REST Proxy] 選項。

     ![啟用 Kafka REST Proxy 並選取安全性群組](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest.png)

1. 按一下 [選取安全性群組]。 從安全性群組清單中，選取您想讓其存取 REST Proxy 的安全性群組。 您可以使用搜尋方塊來尋找適當的安全性群組。 按一下底部的 [選取] 按鈕。

     ![啟用 Kafka REST Proxy 並選取安全性群組](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest2.png)

1. 完成剩餘步驟來建立您的叢集，如＜[使用 Azure 入口網站在 Azure HDInsight 中建立 Apache Kafka 叢集](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-get-started)＞中所述。

1. 建立叢集之後，請移至叢集屬性來記錄 Kafka REST Proxy URL。

     ![Kafka REST Proxy URL](./media/rest-proxy/apache-kafka-rest-proxy-view-proxy-url.png)

## <a name="client-application-sample"></a>用戶端應用程式範例

您可以使用下列 Python 程式碼，與 Kafka 叢集上的 REST Proxy 進行互動。 若要使用程式碼範例，請遵循下列步驟：

1. 將程式碼範例儲存在已安裝 Python 的機器上。
1. 藉由執行 `pip3 install msal`，安裝所需的 Python 相依性。
1. 修改**設定這些屬性**的程式碼區段，並為您的環境更新下列屬性：

    |屬性 |描述 |
    |---|---|
    |租用戶識別碼|您訂用帳戶所在的 Azure 租用戶。|
    |用戶端識別碼|您在安全性群組中註冊的應用程式識別碼。|
    |用戶端密碼|您在安全性群組中註冊的應用程式密碼。|
    |Kafkarest_endpoint|從叢集概觀中的 [屬性] 索引標籤取得此值，如[部署區段](#create-a-kafka-cluster-with-rest-proxy-enabled)中所述。 格式應如下：`https://<clustername>-kafkarest.azurehdinsight.net`|

1. 從命令列中，藉由執行 `sudo python3 <filename.py>` 來執行 Python 檔案

此程式碼會執行以下動作：

1. 從 Azure AD 擷取 OAuth 權杖。
1. 示範如何對 Kafka REST Proxy 提出要求。

如需有關在 Python 中取得 OAuth 權杖的詳細資訊，請參閱 [Python AuthenticationContext class 類別](https://docs.microsoft.com/python/api/adal/adal.authentication_context.authenticationcontext?view=azure-python)。 當未透過 Kafka REST Proxy 建立或刪除的 `topics` 反映在該處時，您可能會看到延遲。 此延遲是因為快取重新整理所造成。

```python
#Required python packages
#pip3 install msal

import msal
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
response = requests.get(request_url, headers={'Authorization': 'Bearer ' + 'accessToken})
print(response.content)
```

在下面的另一個範例中，您會看到如何使用 curl 命令從 Azure 中取得適用於 REST Proxy 的權杖。 **請注意，在取得權杖時，我們需要指定 `scope=https://hib.azurehdinsight.net/.default`。**

```cmd
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=<clientid>&client_secret=<clientsecret>&grant_type=client_credentials&scope=https://hib.azurehdinsight.net/.default' 'https://login.microsoftonline.com/<tenantid>/oauth2/v2.0/token'
```

## <a name="next-steps"></a>後續步驟

* [Kafka REST Proxy API 參考文件](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy/)
