---
title: Apache Kafka REST proxy-Azure HDInsight
description: 瞭解如何在 Azure HDInsight 上使用 Kafka REST proxy 執行 Apache Kafka 作業。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: d99a3b803b80dc41990a63e647d3ba928deb31af
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198900"
---
# <a name="interact-with-apache-kafka-clusters-in-azure-hdinsight-using-a-rest-proxy"></a>使用 REST proxy 與 Azure HDInsight 中的 Apache Kafka 叢集互動

Kafka REST Proxy 可讓您透過 HTTP 透過 REST API 與您的 Kafka 叢集互動。 這表示您的 Kafka 用戶端可以位於虛擬網路外部。 此外，用戶端可以進行簡單的 HTTP 呼叫，將訊息傳送和接收到 Kafka 叢集，而不是依賴 Kafka 程式庫。 本教學課程將說明如何建立已啟用 REST proxy 的 Kafka 叢集，並提供範例程式碼，示範如何呼叫 REST proxy。

## <a name="rest-api-reference"></a>REST API 參考資料

如需 Kafka REST API 支援的完整作業規格，請參閱[HDInsight KAFKA REST PROXY API 參考](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy)。

## <a name="background"></a>背景

![Kafka REST proxy 架構](./media/rest-proxy/rest-proxy-architecture.png)

如需 API 支援之作業的完整規格，請參閱[Apache KAFKA REST PROXY API](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy)。

### <a name="rest-proxy-endpoint"></a>REST Proxy 端點

建立具有 REST proxy 的 HDInsight Kafka 叢集，會為您的叢集建立新的公用端點，您可以在 Azure 入口網站的 HDInsight 叢集「屬性」中找到它。

### <a name="security"></a>安全性

Kafka REST proxy 的存取權是以 Azure Active Directory 安全性群組進行管理。 建立已啟用 REST proxy 的 Kafka 叢集時，您會提供應該具有 REST 端點存取權的 Azure Active Directory 安全性群組。 需要存取 REST proxy 的 Kafka 用戶端（應用程式），應由群組擁有者向此群組註冊。 群組擁有者可以透過入口網站或透過 Powershell 來執行此動作。

對 REST proxy 端點提出要求之前，用戶端應用程式應該取得 OAuth 權杖，以驗證正確安全性群組的成員資格。 請在以下的[用戶端應用程式範例](#client-application-sample)中找到說明如何取得 OAuth 權杖。 一旦用戶端應用程式具有 OAuth 權杖，就必須在對 REST proxy 發出的 HTTP 要求中傳遞該權杖。

> [!NOTE]  
> 若要深入瞭解 AAD 安全性群組，請參閱[使用 Azure Active Directory 群組來管理應用程式和資源存取](../../active-directory/fundamentals/active-directory-manage-groups.md)。 如需 OAuth 權杖如何使用的詳細資訊，請參閱[使用 OAuth 2.0 程式碼授與流程來授權存取 Azure Active Directory web 應用程式](../../active-directory/develop/v1-protocols-oauth-code.md)。

## <a name="prerequisites"></a>必要條件

1. 向 Azure AD 註冊應用程式。 您撰寫來與 Kafka REST proxy 互動的用戶端應用程式，將會使用此應用程式的識別碼和密碼向 Azure 進行驗證。
1. 建立 Azure AD 安全性群組，並將您向 Azure AD 註冊的應用程式新增到安全性群組。 這個安全性群組將用來控制哪些應用程式可以與 REST proxy 互動。 如需建立 Azure AD 群組的詳細資訊，請參閱[建立基本群組和使用 Azure Active Directory 新增成員](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)。

## <a name="create-a-kafka-cluster-with-rest-proxy-enabled"></a>建立已啟用 REST proxy 的 Kafka 叢集

1. 在 Kafka 叢集建立工作流程的 [安全性 + 網路] 索引標籤中，勾選 [啟用 Kafka REST proxy] 選項。

     ![啟用 Kafka REST proxy 並選取安全性群組](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest.png)

1. 按一下 [**選取安全性群組**]。 從安全性群組清單中，選取您想要存取 REST proxy 的安全性群組。 您可以使用 [搜尋] 方塊來尋找適當的安全性群組。 按一下底部的 [**選取**] 按鈕。

     ![啟用 Kafka REST proxy 並選取安全性群組](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest2.png)

1. 完成其餘步驟來建立您的叢集，如在[使用 Azure 入口網站的 Azure HDInsight 中建立 Apache Kafka 叢集中](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-get-started)所述。

1. 建立叢集之後，請移至叢集屬性以記錄 Kafka REST proxy URL。

     ![view REST proxy URL](./media/rest-proxy/apache-kafka-rest-proxy-view-proxy-url.png)

## <a name="client-application-sample"></a>用戶端應用程式範例

您可以使用下列 python 程式碼，與 Kafka 叢集上的 REST proxy 進行互動。 若要使用程式碼範例，請遵循下列步驟：

1. 將範例程式碼儲存在已安裝 Python 的電腦上。
1. 藉由執行 `pip3 install adal` 和 `pip install msrestazure`，安裝所需的 python 相依性。
1. 修改程式碼區段*設定這些屬性*，並為您的環境更新下列屬性：
    1.  *租使用者識別碼*–您的訂用帳戶所在的 Azure 租使用者。
    1.  [*用戶端識別碼*] –您在安全性群組中註冊之應用程式的識別碼。
    1.  [*用戶端密碼*]-您在安全性群組中註冊之應用程式的密碼
    1.  *Kafkarest_endpoint* –從叢集總覽的 [屬性] 索引標籤取得此值，如[部署一節](#create-a-kafka-cluster-with-rest-proxy-enabled)中所述。 應採用下列格式– `https://<clustername>-kafkarest.azurehdinsight.net`
3. 從命令列中，執行以執行 python 檔案 `python <filename.py>`

此程式碼會執行以下動作：

1. 從 Azure AD 提取 OAuth 權杖
1. 示範如何提出要求以 Kafka REST proxy

如需有關在 python 中取得 OAuth 權杖的詳細資訊，請參閱[Python AuthenticationCoNtext 類別](https://docs.microsoft.com/python/api/adal/adal.authentication_context.authenticationcontext?view=azure-python)。 您可能會看到延遲，而未透過 Kafka REST proxy 建立或刪除的主題會反映在該處。 此延遲是因為快取重新整理所造成。

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

* [Kafka REST proxy API 參考檔](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy/)
