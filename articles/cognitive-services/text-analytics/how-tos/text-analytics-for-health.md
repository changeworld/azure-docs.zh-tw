---
title: 如何使用文字分析的健全狀況
titleSuffix: Azure Cognitive Services
description: 瞭解如何使用文字分析的健全狀況，從非結構化的臨床文字中將醫療資訊解壓縮並加上標籤。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 08/06/2020
ms.author: aahi
ms.openlocfilehash: 71cbf03a36dd95eb66c3dcbaffbf4b63d889f507
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/11/2020
ms.locfileid: "88121573"
---
# <a name="how-to-use-text-analytics-for-health-preview"></a>如何：使用文字分析進行健康情況 (預覽) 

> [!NOTE]
> 健康情況容器的文字分析最近已更新。 如需最近變更的詳細資訊，請參閱[新功能](../whats-new.md)。 請記得提取最新的容器，以使用所列的更新。

> [!IMPORTANT] 
> 健全狀況的文字分析是以「原樣」提供的預覽功能，以及「所有的錯誤」。 因此，**健康情況 (預覽) 的文字分析不應在任何實際執行環境中執行或部署。** 適用于健康情況的文字分析並非適用于醫療裝置、臨床支援、診斷工具或其他要用於診斷、解決、緩和、處理或預防疾病或其他條件的技術，而且 Microsoft 不會授與授權或權利以用於這種用途。 這項功能並非設計或部署為替代專業醫學建議或醫療保健專業人員的醫療意見、診斷、治療或臨床判斷，因此不應使用。 客戶完全負責任何使用文字分析健康情況。 Microsoft 不保證健康情況的文字分析或與該功能連線所提供的任何資料，都能滿足任何醫療的目的，或符合任何人的健康或醫療需求。 


「健康情況文字分析」是一種容器化服務，可從非結構化的文字（例如醫生的記事、放電摘要、臨床檔和電子醫療記錄）中，將相關醫療資訊解壓縮並加以標示。  

## <a name="features"></a>特性

健全狀況容器的文字分析目前會在您自己的開發環境中，針對符合您特定安全性和資料控管需求的英文文字，執行名為實體辨識 (NER) 、關聯性、實體否定和實體連結。

#### <a name="named-entity-recognition"></a>[具名實體辨識](#tab/ner)

「命名實體辨識」可偵測非結構化文字中所提及的單字和片語，這些文字可以與一或多個語義類型相關聯，例如診斷、藥物名稱、徵兆/正負號或年齡。

> [!div class="mx-imgBorder"]
> ![健全狀況 NER](../media/ta-for-health/health-named-entity-recognition.png)

#### <a name="relation-extraction"></a>[關聯性解壓縮](#tab/relation-extraction)

關聯性解壓縮會識別文字中所述概念之間有意義的連接。 例如，藉由將條件名稱與時間產生關聯，即可找到「條件的時間」關聯性。 

> [!div class="mx-imgBorder"]
> ![健全狀況重新](../media/ta-for-health/health-relation-extraction.png)


#### <a name="entity-linking"></a>[實體連結](#tab/entity-linking)

實體連結會厘清相異的實體，方法是將文字中提及的已命名實體與概念的預先定義資料庫中找到的概念產生關聯。 例如，統一的醫學語言系統 (UMLS) 。

> [!div class="mx-imgBorder"]
> ![Health EL](../media/ta-for-health/health-entity-linking.png)

文字分析健康狀態支援連結至統一醫學語言系統中找到的健康情況和生物醫學詞彙 ([UMLS](https://www.nlm.nih.gov/research/umls/sourcereleasedocs/index.html)) Metathesaurus 知識來源。

#### <a name="negation-detection"></a>[否定偵測](#tab/negation-detection) 

醫療內容的意義會受到如否定之類的修飾詞高度影響，如果 misdiagnosed，可能會有重大隱含的含意。 文字分析的健全狀況會針對文字中提及的不同實體支援否定偵測。 

> [!div class="mx-imgBorder"]
> ![健全狀況 NEG](../media/ta-for-health/health-negation.png)

---

如需所支援實體的完整清單，請參閱文字分析的健全狀況所傳回的[實體類別](../named-entity-types.md?tabs=health)。

## <a name="supported-languages"></a>支援的語言

文字分析的健全狀況僅支援英文語言檔。

## <a name="request-access-to-the-container-registry"></a>要求存取容器登錄

填寫並提交[認知服務容器要求表單](https://aka.ms/cognitivegate)，以要求容器的存取權。 目前，您不需要支付健康情況使用文字分析的費用。 

[!INCLUDE [Request access to the container registry](../../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="install-the-container"></a>安裝容器

有多種方式可供您安裝和執行容器。 

- 使用[Azure 入口網站](text-analytics-how-to-install-containers.md?tabs=healthcare)建立文字分析資源，並使用 Docker 來取得您的容器。
- 使用下列 PowerShell 和[Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)腳本，將資源部署容器設定自動化。

### <a name="install-the-container-using-azure-web-app-for-containers"></a>使用 Azure 用於容器的 Web App 安裝容器

Azure[用於容器的 Web App](https://azure.microsoft.com/services/app-service/containers/)是專門用來在雲端中執行容器的 azure 資源。 它引進了現成的功能，例如自動調整、支援 docker 容器和 docker 撰寫、HTTPS 支援等等。

> [!NOTE]
> 使用 Azure Web 應用程式，您將會自動取得網域，其格式為`<appservice_name>.azurewebsites.net`

使用 Azure CLI 來執行此 PowerShell 腳本，以使用您的訂用帳戶和透過 HTTPS 的容器映射來建立用於容器的 Web App。 在提交第一個要求之前，請等候腳本完成 (大約25-30 分鐘的) 。

```bash
$subscription_name = ""                    # THe name of the subscription you want you resource to be created on.
$resource_group_name = ""                  # The name of the resource group you want the AppServicePlan
                                           #    and AppSerivce to be attached to.
$resources_location = ""                   # This is the location you wish the AppServicePlan to be deployed to.
                                           #    You can use the "az account list-locations -o table" command to
                                           #    get the list of available locations and location code names.
$appservice_plan_name = ""                 # This is the AppServicePlan name you wish to have.
$appservice_name = ""                      # This is the AppService resource name you wish to have.
$TEXT_ANALYTICS_RESOURCE_API_KEY = ""      # This should be taken from the Text Analytics resource.
$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT = "" # This should be taken from the Text Analytics resource.
$DOCKER_REGISTRY_SERVER_PASSWORD = ""      # This will be provided separately.
$DOCKER_REGISTRY_SERVER_USERNAME = ""      # This will be provided separately.
$DOCKER_IMAGE_NAME = "containerpreview.azurecr.io/microsoft/cognitive-services-healthcare:latest"

az login
az account set -s $subscription_name
az appservice plan create -n $appservice_plan_name -g $resource_group_name --is-linux -l $resources_location --sku P3V2
az webapp create -g $resource_group_name -p $appservice_plan_name -n $appservice_name -i $DOCKER_IMAGE_NAME -s $DOCKER_REGISTRY_SERVER_USERNAME -w $DOCKER_REGISTRY_SERVER_PASSWORD
az webapp config appsettings set -g $resource_group_name -n $appservice_name --settings Eula=accept Billing=$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT ApiKey=$TEXT_ANALYTICS_RESOURCE_API_KEY

# Once deployment complete, the resource should be available at: https://<appservice_name>.azurewebsites.net
```

### <a name="install-the-container-using-azure-container-instance"></a>使用 Azure 容器實例安裝容器

您也可以使用 Azure 容器實例 (ACI) 讓部署變得更容易。 ACI 是一項資源，可讓您在受控、無伺服器的 Azure 環境中隨選執行 Docker 容器。 

如需使用 Azure 入口網站部署 ACI 資源的步驟，請參閱[如何使用 Azure 容器實例](text-analytics-how-to-use-container-instances.md)。 您也可以使用下列 PowerShell 腳本來使用 Azure CLI，這會使用容器映射在您的訂用帳戶上建立 ACI。  在提交第一個要求之前，請等候腳本完成 (大約25-30 分鐘的) 。  由於每個 ACI 資源的 Cpu 最大數目限制，如果您預期提交超過5個大型檔，則請勿選取此選項， (每個要求) 大約5000個字元。
如需可用性資訊，請參閱[ACI 區域支援](https://docs.microsoft.com/azure/container-instances/container-instances-region-availability)文章。 

> [!NOTE] 
> Azure 容器實例不包含適用于內建網域的 HTTPS 支援。 如果您需要 HTTPS，您必須手動設定它，包括建立憑證和註冊網域。 您可以在下方找到使用 NGINX 來執行此動作的指示。

```bash
$subscription_name = ""                    # The name of the subscription you want you resource to be created on.
$resource_group_name = ""                  # The name of the resource group you want the AppServicePlan
                                           # and AppService to be attached to.
$resources_location = ""                   # This is the location you wish the web app to be deployed to.
                                           # You can use the "az account list-locations -o table" command to
                                           # Get the list of available locations and location code names.
$azure_container_instance_name = ""        # This is the AzureContainerInstance name you wish to have.
$TEXT_ANALYTICS_RESOURCE_API_KEY = ""      # This should be taken from the Text Analytics resource.
$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT = "" # This should be taken from the Text Analytics resource.
$DOCKER_REGISTRY_SERVER_PASSWORD = ""      # This will be provided separately.
$DOCKER_REGISTRY_SERVER_USERNAME = ""      # This will be provided separately.
$DNS_LABEL = ""                            # This is the DNS label name you wish your ACI will have
$DOCKER_REGISTRY_LOGIN_SERVER = "containerpreview.azurecr.io"
$DOCKER_IMAGE_NAME = "containerpreview.azurecr.io/microsoft/cognitive-services-healthcare:latest"

az login
az account set -s $subscription_name
az container create --resource-group $resource_group_name --name $azure_container_instance_name --image $DOCKER_IMAGE_NAME --cpu 4 --memory 12 --registry-login-server $DOCKER_REGISTRY_LOGIN_SERVER --registry-username $DOCKER_REGISTRY_SERVER_USERNAME --registry-password $DOCKER_REGISTRY_SERVER_PASSWORD --port 5000 --dns-name-label $DNS_LABEL --environment-variables Eula=accept Billing=$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT ApiKey=$TEXT_ANALYTICS_RESOURCE_API_KEY

# Once deployment complete, the resource should be available at: http://<unique_dns_label>.<resource_group_region>.azurecontainer.io:5000
```

### <a name="secure-aci-connectivity"></a>安全 ACI 連線能力

根據預設，搭配使用 ACI 與容器 API 時，不會提供任何安全性。 這是因為容器通常會做為 pod 的一部分來執行，而這是由網路橋接器的外部所保護。 不過，您可以使用正面元件修改容器，讓容器端點成為私用。 下列範例會使用[NGINX](https://www.nginx.com)作為輸入閘道，以支援 HTTPS/SSL 和用戶端憑證驗證。

> [!NOTE]
> NGINX 是開放原始碼、高效能的 HTTP 伺服器和 proxy。 NGINX 容器可以用來終止單一容器的 TLS 連線。 也可以進行更複雜的 NGINX 輸入型 TLS 終止解決方案。

#### <a name="set-up-nginx-as-an-ingress-gateway"></a>將 NGINX 設定為輸入閘道

NGINX 會使用[設定檔](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/)來啟用執行時間的功能。 若要啟用另一項服務的 TLS 終止，您必須指定 SSL 憑證來終止 TLS 連線，並 `proxy_pass` 指定服務的位址。 以下提供範例。


> [!NOTE]
> `ssl_certificate`需要在 NGINX 容器的本機檔案系統內指定路徑。 為指定的位址 `proxy_pass` 必須可從 NGINX 容器的網路內取得。

NGINX 容器會將中裝載的所有檔案載入 `_.conf_` `/etc/nginx/conf.d/` 至 HTTP 設定路徑。

```nginx
server {
  listen              80;
  return 301 https://$host$request_uri;
}
server {
  listen              443 ssl;
  # replace with .crt and .key paths
  ssl_certificate     /cert/Local.crt;
  ssl_certificate_key /cert/Local.key;

  location / {
    proxy_pass http://cognitive-service:5000;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Real-IP  $remote_addr;
  }
}
```

#### <a name="example-docker-compose-file"></a>Docker 撰寫檔案範例

下列範例顯示如何建立[docker 撰寫](https://docs.docker.com/compose/reference/overview)檔案，以部署健康情況容器的 NGINX 和文字分析：

```yaml
version: "3.7"
services:
  cognitive-service:
    image: {IMAGE_ID}
    ports:
      - 5000:5000
    environment:
      - eula=accept
      - billing={ENDPOINT_URI}
      - apikey={API_KEY}
      - Logging:Disk:Format=json
    volumes:
        # replace with path to logs folder
      - <path-to-logs-folder>:/output
  nginx:
    image: nginx
    ports:
      - 443:443
    volumes:
        # replace with paths for certs and conf folders
      - <path-to-certs-folder>:/cert
      - <path-to-conf-folder>:/etc/nginx/conf.d/
```

若要起始此 Docker 撰寫檔案，請從檔案根目錄層級的主控台執行下列命令：

```bash
docker-compose up
```

如需詳細資訊，請參閱[NGINX SSL 終止](https://docs.nginx.com/nginx/admin-guide/security-controls/terminating-ssl-http/)的 NGINX 檔。


## <a name="example-api-request"></a>API 要求範例
容器會提供以 REST 為基礎的查詢預測端點 API。

使用下面的範例捲曲要求，將查詢提交至您已部署的容器，以 `serverURL` 適當的值取代變數。

```bash
curl -X POST 'http://<serverURL>:5000/text/analytics/v3.2-preview.1/entities/health' --header 'Content-Type: application/json' --header 'accept: application/json' --data-binary @example.json

```

下列 JSON 是附加至健康情況 API 要求的 POST 主體之文字分析的 JSON 檔案範例：

```json
example.json

{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Patient reported itchy sores after swimming in the lake."
    },
    {
      "language": "en",
      "id": "2",
      "text": "Prescribed 50mg benadryl, taken twice daily."
    }
  ]
}
```

## <a name="api-response-body"></a>API 回應主體

下列 JSON 是「健康情況 API」回應主體的文字分析範例：

```json
{
    "documents": [
        {
            "id": "1",
            "entities": [
                {
                    "id": "0",
                    "offset": 17,
                    "length": 11,
                    "text": "itchy sores",
                    "category": "SymptomOrSign",
                    "ConfidenceScore": 1.0,
                    "isNegated": false
                }
            ]
        },
        {
            "id": "2",
            "entities": [
                {
                    "id": "0",
                    "offset": 11,
                    "length": 4,
                    "text": "50mg",
                    "category": "Dosage",
                    "ConfidenceScore": 1.0,
                    "isNegated": false
                },
                {
                    "id": "1",
                    "offset": 16,
                    "length": 8,
                    "text": "benadryl",
                    "category": "MedicationName",
                    "ConfidenceScore": 1.0,
                    "isNegated": false,
                    "links": [
                        {
                            "dataSource": "UMLS",
                            "id": "C0700899"
                        },
                        {
                            "dataSource": "CHV",
                            "id": "0000044903"
                        },
                        {
                            "dataSource": "MMSL",
                            "id": "899"
                        },
                        {
                            "dataSource": "MSH",
                            "id": "D004155"
                        },
                        {
                            "dataSource": "NCI",
                            "id": "C300"
                        },
                        {
                            "dataSource": "NCI_DTP",
                            "id": "NSC0033299"
                        },
                        {
                            "dataSource": "PDQ",
                            "id": "CDR0000039163"
                        },
                        {
                            "dataSource": "PSY",
                            "id": "05760"
                        },
                        {
                            "dataSource": "RXNORM",
                            "id": "203457"
                        }
                    ]
                },
                {
                    "id": "2",
                    "offset": 32,
                    "length": 11,
                    "text": "twice daily",
                    "category": "Frequency",
                    "ConfidenceScore": 1.0,
                    "isNegated": false
                }
            ],
            "relations": [
                {
                    "relationType": "DosageOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/1/entities/0",
                    "target": "#/documents/1/entities/1"
                },
                {
                    "relationType": "FrequencyOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/1/entities/2",
                    "target": "#/documents/1/entities/1"
                }
            ]
        }
    ],
    "errors": [],
    "modelVersion": "2020-07-24"
}
```

### <a name="negation-detection-output"></a>否定偵測輸出

使用否定偵測時，在某些情況下，單一否定詞彙可能會一次處理數個詞彙。 已辨識實體的否定會以旗標的布林值在 JSON 輸出中表示 `isNegated` ：

```json
{
  "id": "2",
  "offset": 90,
  "length": 10,
  "text": "chest pain",
  "category": "SymptomOrSign",
  "score": 0.9972,
  "isNegated": true,
  "links": [
    {
      "dataSource": "UMLS",
      "id": "C0008031"
    },
    {
      "dataSource": "CHV",
      "id": "0000023593"
    },
    ...
```

### <a name="relation-extraction-output"></a>關聯性的提取輸出

關聯性的提取輸出包含關聯性*來源*及其*目標*的 URI 參考。 具有關聯角色的實體 `ENTITY` 會指派給 `target` 欄位。 具有關聯角色的實體 `ATTRIBUTE` 會指派給 `source` 欄位。 縮寫關聯包含雙向 `source` 和 `target` 欄位，而且 `bidirectional` 會設定為 `true` 。 

```json
"relations": [
  {
      "relationType": "DosageOfMedication",
      "score": 1.0,
      "bidirectional": false,
      "source": "#/documents/2/entities/0",
      "target": "#/documents/2/entities/1",
      "entities": [
          {
              "id": "0",
              "role": "ATTRIBUTE"
          },
          {
              "id": "1",
              "role": "ENTITY"
          }
      ]
  },
...
]
```

## <a name="see-also"></a>另請參閱

* [文字分析概觀](../overview.md)
* [命名實體類別](../named-entity-types.md)
* [新功能](../whats-new.md)
