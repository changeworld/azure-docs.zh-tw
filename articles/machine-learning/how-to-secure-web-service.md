---
title: 使用 TLS 保護 web 服務
titleSuffix: Azure Machine Learning
description: 瞭解如何啟用 HTTPS 與 TLS 1.2 版，以保護透過 Azure Machine Learning 部署的 web 服務。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 11/18/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 86cd5a5cbbb17dc3d3e4d56e4267be2718f6081d
ms.sourcegitcommit: beacda0b2b4b3a415b16ac2f58ddfb03dd1a04cf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/31/2020
ms.locfileid: "97830865"
---
# <a name="use-tls-to-secure-a-web-service-through-azure-machine-learning"></a>使用 TLS 來透過 Azure Machine Learning 保護 Web 服務


本文說明如何保護透過 Azure Machine Learning 部署的 web 服務。

您可以使用 [HTTPS](https://en.wikipedia.org/wiki/HTTPS) 來限制對 web 服務的存取，並保護用戶端所提交的資料。 HTTPS 可以加密兩者之間的通訊，以協助保護用戶端與 web 服務之間的通訊安全。 加密會使用 [傳輸層安全性 (TLS) ](https://en.wikipedia.org/wiki/Transport_Layer_Security)。 TLS 有時仍稱為 *安全通訊端層* (SSL) ，也就是 tls 的前身。

> [!TIP]
> Azure Machine Learning SDK 會針對與安全通訊相關的屬性使用「SSL」一詞。 這並不表示您的 web 服務不會使用 *TLS*。 SSL 只是較常辨識的詞彙。
>
> 具體而言，透過 Azure Machine Learning 部署的 web 服務支援 AKS 和 ACI 的 TLS 1.2 版。 針對 ACI 部署，如果您是在較舊的 TLS 版本上，建議您重新部署以取得最新的 TLS 版本。

TLS 和 SSL 都依賴 *數位憑證*，可協助進行加密和身分識別驗證。 如需有關數位憑證運作方式的詳細資訊，請參閱維琪百科主題 [公開金鑰基礎結構](https://en.wikipedia.org/wiki/Public_key_infrastructure)。

> [!WARNING]
> 如果您的 web 服務未使用 HTTPS，則網際網路上的其他人可能會看到傳送至服務和來自該服務的資料。
>
> HTTPS 也可讓用戶端驗證其所連接之伺服器的真實性。 這項功能可保護用戶端抵禦 [攔截](https://en.wikipedia.org/wiki/Man-in-the-middle_attack) 式攻擊。

這是保護 web 服務的一般程式：

1. 取得網域名稱。

2. 取得數位憑證。

3. 部署或更新啟用 TLS 的 web 服務。

4. 更新您的 DNS 以指向 Web 服務。

> [!IMPORTANT]
> 如果您要部署 Azure Kubernetes Service (AKS) ，您可以購買自己的憑證，或使用 Microsoft 提供的憑證。 如果您使用來自 Microsoft 的憑證，則不需要取得功能變數名稱或 TLS/SSL 憑證。 如需詳細資訊，請參閱本文的「 [啟用 TLS 和部署](#enable) 」一節。

當您在 [部署目標](how-to-deploy-and-where.md)之間保護時，會有些微差異。

## <a name="get-a-domain-name"></a>取得網域名稱

如果您還沒有定義功能變數名稱稱，請向 *網域註冊機構* 購買一個。 註冊機構之間的流程和價格有所不同。 註冊機構提供管理功能變數名稱的工具。 您可以使用這些工具，將完整功能變數名稱 (FQDN)  (例如 www \. contoso.com) ，對應至裝載 web 服務的 IP 位址。

## <a name="get-a-tlsssl-certificate"></a>取得 TLS/SSL 憑證

有許多方法可取得 (數位憑證) 的 TLS/SSL 憑證。 最常見的是從 *憑證授權單位* 單位購買一個 (CA) 。 無論您取得憑證的位置為何，您都需要下列檔案：

* **憑證**。 憑證必須包含完整的憑證鏈，而且必須是「PEM 編碼」。
* **金鑰**。 金鑰也必須是 PEM 編碼。

當您要求憑證時，您必須提供您打算用於 web 服務的位址 FQDN (例如，www \. contoso.com) 。 系統會將已加上戳記的位址和用戶端所使用的位址進行比較，以驗證 web 服務的身分識別。 如果這些位址不相符，用戶端會收到錯誤訊息。

> [!TIP]
> 如果憑證授權單位單位無法以 PEM 編碼的檔案形式提供憑證和金鑰，您可以使用 [OpenSSL](https://www.openssl.org/) 之類的公用程式來變更格式。

> [!WARNING]
> 使用 *自我簽署* 憑證僅供開發之用。 請勿在生產環境中使用它們。 自我簽署的憑證可能會造成用戶端應用程式的問題。 如需詳細資訊，請參閱用戶端應用程式所使用網路程式庫的檔。

## <a name="enable-tls-and-deploy"></a><a id="enable"></a> 啟用 TLS 並部署

**針對 AKS 部署**，您可以在 [建立或附加](how-to-create-attach-kubernetes.md) AML 工作區中的 AKS 叢集時，啟用 TLS 終止。 在 AKS 模型部署期間，您可以停用部署設定物件的 TLS 終止，否則所有 AKS 模型部署預設會在 AKS 叢集建立或附加時間啟用 TLS 終止。

針對 ACI 部署，您可以在部署設定物件的模型部署時間啟用 TLS 終止。


### <a name="deploy-on-azure-kubernetes-service"></a>在 Azure Kubernetes Service 上部署

  > [!NOTE]
  > 當您為設計工具部署安全的 web 服務時，本節中的資訊也適用。 如果您不熟悉如何使用 Python SDK，請參閱 [什麼是適用于 python 的 AZURE MACHINE LEARNING SDK？](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py)。

當您在 AML 工作區中 [建立或附加 AKS](how-to-create-attach-kubernetes.md) 叢集時，可以使用 **[AksCompute.provisioning_configuration ( # B1](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py&preserve-view=true#&preserve-view=trueprovisioning-configuration-agent-count-none--vm-size-none--ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--location-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--service-cidr-none--dns-service-ip-none--docker-bridge-cidr-none--cluster-purpose-none--load-balancer-type-none--load-balancer-subnet-none-)** 和 **[AksCompute.attach_configuration ( # B3](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py&preserve-view=true#&preserve-view=trueattach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)** 設定物件來啟用 TLS 終止。 這兩種方法都會傳回具有 **enable_ssl** 方法的設定物件，您可以使用 **enable_ssl** 方法來啟用 TLS。

您可以使用 Microsoft 憑證或從 CA 購買的自訂憑證來啟用 TLS。 

* **當您使用來自 Microsoft 的憑證時**，您必須使用 *leaf_domain_label* 參數。 此參數會產生服務的 DNS 名稱。 例如，"contoso" 的值會建立功能變數名稱 "contoso \<six-random-characters> . \<azureregion> 。cloudapp.azure.com "，其中 \<azureregion> 是包含服務的區域。 （選擇性）您可以使用 *overwrite_existing_domain* 參數來覆寫現有的 *leaf_domain_label*。 下列範例示範如何建立可啟用 TLS 與 Microsoft 憑證的設定：

    ```python
    from azureml.core.compute import AksCompute

    # Config used to create a new AKS cluster and enable TLS
    provisioning_config = AksCompute.provisioning_configuration()

    # Leaf domain label generates a name using the formula
    #  "<leaf-domain-label>######.<azure-region>.cloudapp.azure.net"
    #  where "######" is a random series of characters
    provisioning_config.enable_ssl(leaf_domain_label = "contoso")


    # Config used to attach an existing AKS cluster to your workspace and enable TLS
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                          cluster_name = cluster_name)

    # Leaf domain label generates a name using the formula
    #  "<leaf-domain-label>######.<azure-region>.cloudapp.azure.net"
    #  where "######" is a random series of characters
    attach_config.enable_ssl(leaf_domain_label = "contoso")
    ```
    > [!IMPORTANT]
    > 當您使用來自 Microsoft 的憑證時，您不需要購買自己的憑證或功能變數名稱。

    > [!WARNING]
    > 如果您的 AKS 叢集已設定為使用內部負載平衡器，則 __不支援__ 使用 Microsoft 提供的憑證，而且您必須使用自訂憑證來啟用 TLS。

* **當您使用您所購買的自訂憑證時**，您會使用 *ssl_cert_pem_file*、 *ssl_key_pem_file* 和 *ssl_cname* 參數。 下列範例示範如何使用 pem 檔案來建立使用您所購買 TLS/SSL 憑證的設定：
 
    ```python
    from azureml.core.compute import AksCompute

    # Config used to create a new AKS cluster and enable TLS
    provisioning_config = AksCompute.provisioning_configuration()
    provisioning_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")

    # Config used to attach an existing AKS cluster to your workspace and enable SSL
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
    attach_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    ```

如需 *enable_ssl* 的詳細資訊，請參閱 [AksProvisioningConfiguration.enable_ssl ( # B1](/python/api/azureml-core/azureml.core.compute.aks.aksprovisioningconfiguration?preserve-view=true&view=azure-ml-py#&preserve-view=trueenable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-) 和 [AksAttachConfiguration.enable_ssl ( # B3](/python/api/azureml-core/azureml.core.compute.aks.aksattachconfiguration?preserve-view=true&view=azure-ml-py#&preserve-view=trueenable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-)。

### <a name="deploy-on-azure-container-instances"></a>在 Azure 容器執行個體上部署

當您部署至 Azure 容器實例時，您會提供 TLS 相關參數的值，如下列程式碼片段所示：

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(
    ssl_enabled=True, ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
```

如需詳細資訊，請參閱 [AciWebservice.deploy_configuration ( # B1 ](/python/api/azureml-core/azureml.core.webservice.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-)。

## <a name="update-your-dns"></a>更新您的 DNS

針對使用自訂憑證或 ACI 部署的 AKS 部署，您必須更新您的 DNS 記錄，以指向評分端點的 IP 位址。

  > [!IMPORTANT]
  > 當您使用 Microsoft 的憑證進行 AKS 部署時，您不需要手動更新叢集的 DNS 值。 值應該會自動設定。

您可以遵循下列步驟來更新自訂功能變數名稱的 DNS 記錄：
* 從計分端點 URI 取得計分端點 IP 位址，其格式通常為 *http://104.214.29.152:80/api/v1/service/<service-name>/score* 。 
* 使用您的網域註冊機構中的工具，更新功能變數名稱的 DNS 記錄。 記錄必須指向計分端點的 IP 位址。
* 在 DNS 記錄更新之後，您可以使用 *nslookup 自訂網功能變數名稱稱* 命令驗證 dns 解析。 如果 DNS 記錄已正確更新，自訂功能變數名稱會指向評分端點的 IP 位址。
* 可能會有幾分鐘或幾小時的時間，用戶端可以根據註冊機構和針對功能變數名稱設定的「存留時間」 (TTL) 來解析功能變數名稱。


## <a name="update-the-tlsssl-certificate"></a>更新 TLS/SSL 憑證

TLS/SSL 憑證過期，必須更新。 這通常會在每年發生。 使用下列各節中的資訊，針對部署至 Azure Kubernetes Service 的模型更新和更新您的憑證：

### <a name="update-a-microsoft-generated-certificate"></a>更新 Microsoft 產生的憑證

如果憑證最初是由 Microsoft (在使用 *leaf_domain_label* 建立服務) 時，請使用下列其中一個範例來更新憑證：

> [!IMPORTANT]
> * 如果現有的憑證仍然有效，請使用 `renew=True` (SDK) 或 `--ssl-renew` (CLI) 來強制進行更新。 例如，如果現有的憑證仍有效10天，而您未使用 `renew=True` ，則憑證可能不會更新。
> * 最初部署服務時， `leaf_domain_label` 會使用此模式來建立 DNS 名稱 `<leaf-domain-label>######.<azure-region>.cloudapp.azure.net` 。 若要保留現有的名稱 (包括原本產生的) 6 位數，請使用原始 `leaf_domain_label` 值。 請勿包含產生的6位數。

**使用 SDK**

```python
from azureml.core.compute import AksCompute
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute.aks import SslConfiguration

# Get the existing cluster
aks_target = AksCompute(ws, clustername)

# Update the existing certificate by referencing the leaf domain label
ssl_configuration = SslConfiguration(leaf_domain_label="myaks", overwrite_existing_domain=True, renew=True)
update_config = AksUpdateConfiguration(ssl_configuration)
aks_target.update(update_config)
```

**使用 CLI**

```azurecli
az ml computetarget update aks -g "myresourcegroup" -w "myresourceworkspace" -n "myaks" --ssl-leaf-domain-label "myaks" --ssl-overwrite-domain True --ssl-renew
```

如需詳細資訊，請參閱下列參考檔：

* [SslConfiguration](/python/api/azureml-core/azureml.core.compute.aks.sslconfiguration?preserve-view=true&view=azure-ml-py)
* [AksUpdateConfiguration](/python/api/azureml-core/azureml.core.compute.aks.aksupdateconfiguration?preserve-view=true&view=azure-ml-py)

### <a name="update-custom-certificate"></a>更新自訂憑證

如果憑證原本是由憑證授權單位單位所產生，請使用下列步驟：

1. 使用憑證授權單位單位提供的檔來更新憑證。 此進程會建立新的憑證檔案。

1. 使用 SDK 或 CLI，以新的憑證來更新服務：

    **使用 SDK**

    ```python
    from azureml.core.compute import AksCompute
    from azureml.core.compute.aks import AksUpdateConfiguration
    from azureml.core.compute.aks import SslConfiguration
    
    # Read the certificate file
    def get_content(file_name):
        with open(file_name, 'r') as f:
            return f.read()

    # Get the existing cluster
    aks_target = AksCompute(ws, clustername)
    
    # Update cluster with custom certificate
    ssl_configuration = SslConfiguration(cname="myaks", cert=get_content('cert.pem'), key=get_content('key.pem'))
    update_config = AksUpdateConfiguration(ssl_configuration)
    aks_target.update(update_config)
    ```

    **使用 CLI**

    ```azurecli
    az ml computetarget update aks -g "myresourcegroup" -w "myresourceworkspace" -n "myaks" --ssl-cname "myaks"--ssl-cert-file "cert.pem" --ssl-key-file "key.pem"
    ```

如需詳細資訊，請參閱下列參考檔：

* [SslConfiguration](/python/api/azureml-core/azureml.core.compute.aks.sslconfiguration?preserve-view=true&view=azure-ml-py)
* [AksUpdateConfiguration](/python/api/azureml-core/azureml.core.compute.aks.aksupdateconfiguration?preserve-view=true&view=azure-ml-py)

## <a name="disable-tls"></a>停用 TLS

若要針對部署至 Azure Kubernetes Service 的模型停用 TLS，請 `SslConfiguration` 使用建立 `status="Disabled"` ，然後執行更新：

```python
from azureml.core.compute import AksCompute
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute.aks import SslConfiguration

# Get the existing cluster
aks_target = AksCompute(ws, clustername)

# Disable TLS
ssl_configuration = SslConfiguration(status="Disabled")
update_config = AksUpdateConfiguration(ssl_configuration)
aks_target.update(update_config)
```

## <a name="next-steps"></a>後續步驟
了解如何：
+ [使用部署為 web 服務的機器學習模型](how-to-consume-web-service.md)
+ [虛擬網路隔離和隱私權總覽](how-to-network-security-overview.md)