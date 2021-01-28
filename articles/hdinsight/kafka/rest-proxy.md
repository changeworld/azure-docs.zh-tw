---
title: Apache Kafka REST Proxy - Azure HDInsight
description: 了解如何在 Azure HDInsight 上使用 Kafka REST Proxy 執行 Apache Kafka 作業。
ms.service: hdinsight
ms.topic: how-to
ms.custom: has-adal-ref, devx-track-python
ms.date: 04/03/2020
ms.openlocfilehash: a9a007d33226c508e193368b08b189001bf53401
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944072"
---
# <a name="interact-with-apache-kafka-clusters-in-azure-hdinsight-using-a-rest-proxy"></a>使用 REST Proxy 與 Azure HDInsight 中的 Apache Kafka 叢集互動

Kafka REST Proxy 可讓您透過 HTTPS 的 REST API，與您的 Kafka 叢集互動。 此動作表示您的 Kafka 用戶端可以位於虛擬網路外部。 用戶端可以對 Kafka 叢集進行簡單、安全的 HTTPS 呼叫，而不需依賴 Kafka 程式庫。 本文將說明如何建立已啟用 REST Proxy 的 Kafka 叢集。 也會提供範例程式碼，說明如何呼叫 REST Proxy。

## <a name="rest-api-reference"></a>REST API 參考資料

若要了解 Kafka REST API 支援的作業，請參閱 [HDInsight Kafka REST Proxy API 參考](/rest/api/hdinsight-kafka-rest-proxy)。

## <a name="background"></a>背景

![Kafka REST Proxy 設計](./media/rest-proxy/rest-proxy-architecture.png)

如需 API 所支援作業的完整規格，請參閱 [Apache Kafka REST Proxy API](/rest/api/hdinsight-kafka-rest-proxy)。

### <a name="rest-proxy-endpoint"></a>REST Proxy 端點

建立具有 REST Proxy 的 HDInsight Kafka 叢集，會為您的叢集建立新的公用端點，其位於 Azure 入口網站上的 HDInsight 叢集 **屬性** 中。

### <a name="security"></a>安全性

Kafka REST Proxy 的存取權會以 Azure Active Directory 安全性群組進行管理。 建立 Kafka 叢集時，請提供具有 REST 端點存取權的 Azure AD 安全性群組。 應由此群組的擁有者將需要存取 REST Proxy 的 Kafka 用戶端註冊到群組。 群組擁有者可以透過入口網站或透過 PowerShell 註冊。

針對 REST Proxy 端點要求，用戶端應用程式應該會取得 OAuth 權杖。 權杖會用來驗證安全性群組成員資格。 請在下方尋找[用戶端應用程式範例](#client-application-sample)，其中會說明如何取得 OAuth 權杖。 用戶端應用程式會將 HTTPS 要求中的 OAuth 權杖傳遞給 REST proxy。

> [!NOTE]
> 若要深入了解 AAD 安全性群組，請參閱[使用 Azure Active Directory 群組來管理應用程式和資源存取](../../active-directory/fundamentals/active-directory-manage-groups.md)。 如需有關 OAuth 權杖使用方式的詳細資訊，請參閱[使用 OAuth 2.0 授權碼授與流程，授權存取 Azure Active Directory Web 應用程式](../../active-directory/azuread-dev/v1-protocols-oauth-code.md)。

## <a name="kafka-rest-proxy-with-network-security-groups"></a>搭配網路安全性群組使用 Kafka REST Proxy
如果您使用網路安全性群組來攜帶自己的 VNet 並控制網路流量，除了連接埠 443 以外，請允許連接埠 **9400** 上的 **輸入** 流量。 這可確保可觸達 Kafka REST Proxy 伺服器。

## <a name="prerequisites"></a>Prerequisites

1. 向 Azure AD 註冊應用程式。 您撰寫來與 Kafka REST Proxy 互動的用戶端應用程式，將使用此應用程式的識別碼和密碼向 Azure 進行驗證。

1. 建立 Azure AD 安全性群組。 將您向 Azure AD 註冊的應用程式新增到安全性群組，以作為該群組的 **成員**。 此安全性群組將用來控制哪些應用程式能與 REST Proxy 互動。 如需建立 Azure AD 群組的詳細資訊，請參閱[使用 Azure Active Directory 建立基本群組和新增成員](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)。

    確認群組屬於 **安全性** 類型。
    ![安全性群組](./media/rest-proxy/rest-proxy-group.png)

    確認該應用程式是群組的成員。
    ![檢查成員資格](./media/rest-proxy/rest-proxy-membergroup.png)

## <a name="create-a-kafka-cluster-with-rest-proxy-enabled"></a>建立已啟用 REST Proxy 的 Kafka 叢集

下列步驟是使用 Azure 入口網站。 如需使用 Azure CLI 的範例，請參閱[使用 Azure CLI 建立 Apache Kafka REST Proxy 叢集](tutorial-cli-rest-proxy.md)。

1. 在建立 Kafka 叢集的工作流程中，於 [安全性 + 網路] 索引標籤中，勾選 [啟用 Kafka REST Proxy] 選項。

     ![螢幕擷取畫面顯示 [建立 H D 深入解析叢集] 頁面，其中已選取 [安全性 + 網路]。](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest.png)

1. 按一下 [選取安全性群組]。 從安全性群組清單中，選取您想讓其存取 REST Proxy 的安全性群組。 您可以使用搜尋方塊來尋找適當的安全性群組。 按一下底部的 [選取] 按鈕。

     ![螢幕擷取畫面顯示 [建立 H D 深入解析叢集] 頁面，其中包含選取安全性群組的選項。](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest2.png)

1. 完成剩餘步驟來建立您的叢集，如＜[使用 Azure 入口網站在 Azure HDInsight 中建立 Apache Kafka 叢集](./apache-kafka-get-started.md)＞中所述。

1. 建立叢集之後，請移至叢集屬性來記錄 Kafka REST Proxy URL。

     ![Kafka REST Proxy URL](./media/rest-proxy/apache-kafka-rest-proxy-view-proxy-url.png)

## <a name="client-application-sample"></a>用戶端應用程式範例

您可以使用下列 Python 程式碼，與 Kafka 叢集上的 REST Proxy 進行互動。 若要使用程式碼範例，請遵循下列步驟：

1. 將程式碼範例儲存在已安裝 Python 的機器上。
1. 藉由執行 `pip3 install msal`，安裝所需的 Python 相依性。
1. 修改 **設定這些屬性** 的程式碼區段，並為您的環境更新下列屬性：

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

如需在 Python 中取得 OAuth 權杖的詳細資訊，請參閱 [Python AuthenticationCoNtext 類別](/python/api/adal/adal.authentication_context.authenticationcontext)。 當未透過 Kafka REST Proxy 建立或刪除的 `topics` 反映在該處時，您可能會看到延遲。 此延遲是因為快取重新整理所造成。 已增強生產者 API 的 [ **值** ] 欄位。 現在，它接受 JSON 物件和任何序列化的表單。

```python
#Required python packages
#pip3 install msal

import json
import msal
import random
import requests
import string
import sys
import time

def get_random_string():
    letters = string.ascii_letters
    random_string = ''.join(random.choice(letters) for i in range(7))

    return random_string


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

# Get access token
# Scope
scope = 'https://hib.azurehdinsight.net/.default'
#Authority
authority = 'https://login.microsoftonline.com/' + tenant_id

app = msal.ConfidentialClientApplication(
    client_id , client_secret, authority,
    #cache - For details on how look at this example: https://github.com/Azure-Samples/ms-identity-python-webapp/blob/master/app.py
)

# The pattern to acquire a token looks like this.
result = None
result = app.acquire_token_for_client(scopes=[scope])
accessToken = result['access_token']
verify_https = True
request_timeout = 10

# Print access token
print("Access token: " + accessToken)

# API format
api_version = 'v1'
api_format = kafkarest_endpoint + '/{api_version}/{rest_api}'
get_topic_api = 'metadata/topics'
topic_api_format = 'topics/{topic_name}'
producer_api_format = 'producer/topics/{topic_name}'
consumer_api_format = 'consumer/topics/{topic_name}/partitions/{partition_id}/offsets/{offset}?count={count}'  # by default count = 1

# Request header
headers = {
    'Authorization': 'Bearer ' + accessToken,
    'Content-type': 'application/json'          # set Content-type to 'application/json'
}

# New topic
new_topic = 'hello_topic_' + get_random_string()
print("Topic " + new_topic + " is going to be used for demo.")

topics = []

# Create a  new topic
# Example of topic config
topic_config = {
    "partition_count": 1,
    "replication_factor": 1,
    "topic_properties": {
        "retention.ms": 604800000,
        "min.insync.replicas": "1"
    }
}

create_topic_url = api_format.format(api_version=api_version, rest_api=topic_api_format.format(topic_name=new_topic))
response = requests.put(create_topic_url, headers=headers, json=topic_config, timeout=request_timeout, verify=verify_https)
print(response.content)

if response.ok:
    while new_topic not in topics:
        print("The new topic " + new_topic + " is not visible yet. sleep 30 seconds...")
        time.sleep(30)
        # List Topic
        get_topic_url = api_format.format(api_version=api_version, rest_api=get_topic_api)

        response = requests.get(get_topic_url, headers={'Authorization': 'Bearer ' + accessToken}, timeout=request_timeout, verify=verify_https)
        topic_list = response.json()
        topics = topic_list.get("topics", [])
else:
    print("Topic " + new_topic + " was created. Exit.")
    sys.exit(1)

# Produce messages to new_topic
# Example payload of Producer REST API
payload_json = {
    "records": [
        {
            "key": "key1",
            "value": "**********"         # A string                              
        },
        {
            "partition": 0,
            "value": 5                    # An integer
        },
        {
            "value": 3.14                 # A floating number
        },
        {
            "value": {                    # A JSON object
                "id": 1,
                "name": "HDInsight Kafka REST proxy"
            }
        },
        {
            "value": [                    # A list of JSON objects
                {
                    "id": 1,
                    "name": "HDInsight Kafka REST proxy 1"
                },
                {
                    "id": 2,
                    "name": "HDInsight Kafka REST proxy 2"
                },
                {
                    "id": 3,
                    "name": "HDInsight Kafka REST proxy 3"
                }
            ]
        },
        {
            "value": {                  # A nested JSON object
                "group id": 1,
                "HDI Kafka REST": {
                    "id": 1,
                    "name": "HDInsight Kafka REST proxy 1"
                },
                "HDI Kafka REST server info": {
                    "id": 1,
                    "name": "HDInsight Kafka REST proxy 1",
                    "servers": [
                        {
                            "server id": 1,
                            "server name": "HDInsight Kafka REST proxy server 1"
                        },
                        {
                            "server id": 2,
                            "server name": "HDInsight Kafka REST proxy server 2"
                        },
                        {
                            "server id": 3,
                            "server name": "HDInsight Kafka REST proxy server 3"
                        }
                    ]
                }
            }
        }
    ]
}

print("Payloads in a Producer request: \n", payload_json)
producer_url = api_format.format(api_version=api_version, rest_api=producer_api_format.format(topic_name=new_topic))
response = requests.post(producer_url, headers=headers, json=payload_json, timeout=request_timeout, verify=verify_https)
print(response.content)

# Consume messages from the topic
partition_id = 0
offset = 0
count = 2

while True:
    consumer_url = api_format.format(api_version=api_version, rest_api=consumer_api_format.format(topic_name=new_topic, partition_id=partition_id, offset=offset, count=count))
    print("Consuming " + str(count) + " messages from offset " + str(offset))

    response = requests.get(consumer_url, headers=headers, timeout=request_timeout, verify=verify_https)

    if response.ok:
        messages = response.json()
        print("Consumed messages: \n" + json.dumps(messages, indent=2))
        next_offset = response.headers.get("NextOffset")
        if offset == next_offset or not messages.get("records", []):
            print("Consumer caught up with producer. Exit for now...")
            break

        offset = next_offset

    else:
        print("Error " + str(response.status_code))
        break
        
# List partitions
get_partitions_url = api_format.format(api_version=api_version, rest_api=partitions_api_format.format(topic_name=new_topic))
print("Fetching partitions from  " + get_partitions_url)

response = requests.get(get_partitions_url, headers={'Authorization': 'Bearer ' + accessToken}, timeout=request_timeout, verify=verify_https)
partition_list = response.json()
print("Partition list: \n" + json.dumps(partition_list, indent=2))

# List a partition
get_partition_url = api_format.format(api_version=api_version, rest_api=partition_api_format.format(topic_name=new_topic, partition_id=partition_id))
print("Fetching metadata of a partition from  " + get_partition_url)

response = requests.get(get_partition_url, headers={'Authorization': 'Bearer ' + accessToken}, timeout=request_timeout, verify=verify_https)
partition = response.json()
print("Partition metadata: \n" + json.dumps(partition, indent=2))

```

在下面的另一個範例中，您會看到如何使用 curl 命令從 Azure 中取得適用於 REST Proxy 的權杖。 **請注意，在取得權杖時，我們需要指定 `scope=https://hib.azurehdinsight.net/.default`。**

```cmd
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=<clientid>&client_secret=<clientsecret>&grant_type=client_credentials&scope=https://hib.azurehdinsight.net/.default' 'https://login.microsoftonline.com/<tenantid>/oauth2/v2.0/token'
```

## <a name="next-steps"></a>後續步驟

* [Kafka REST Proxy API 參考文件](/rest/api/hdinsight-kafka-rest-proxy/)
