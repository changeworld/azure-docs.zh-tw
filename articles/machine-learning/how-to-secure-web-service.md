---
title: 使用 TLS 保護 web 服務
titleSuffix: Azure Machine Learning
description: 瞭解如何啟用 HTTPS，以保護透過 Azure Machine Learning 部署的 web 服務。 Azure Machine Learning 使用 TLS 1.2 版來保護部署為 web 服務的模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: 1f4b699476902fa24fa285754f13b1c61ddca8f0
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78355538"
---
# <a name="use-tls-to-secure-a-web-service-through-azure-machine-learning"></a>透過 Azure Machine Learning 使用 TLS 來保護 web 服務
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

本文說明如何保護透過 Azure Machine Learning 部署的 web 服務。

您可以使用[HTTPS](https://en.wikipedia.org/wiki/HTTPS)來限制對 web 服務的存取，並保護用戶端所提交的資料。 HTTPS 藉由加密兩者之間的通訊，協助保護用戶端與 web 服務之間的通訊。 加密使用[傳輸層安全性（TLS）](https://en.wikipedia.org/wiki/Transport_Layer_Security)。 TLS 有時仍然稱為*安全通訊端層*（SSL），這是 tls 的前身。

> [!TIP]
> Azure Machine Learning SDK 會針對與安全通訊相關的屬性使用「SSL」一詞。 這並不表示您的 web 服務不會使用*TLS*。 SSL 只是較常辨識的字詞。
>
> 具體而言，透過 Azure Machine Learning 部署的 web 服務僅支援 TLS 版本1.2。

TLS 和 SSL 都依賴*數位憑證*，這有助於加密和身分識別驗證。 如需數位憑證如何使用的詳細資訊，請參閱維琪百科主題[公開金鑰基礎結構](https://en.wikipedia.org/wiki/Public_key_infrastructure)。

> [!WARNING]
> 如果您的 web 服務未使用 HTTPS，則網際網路上的其他人可能會看到與服務之間傳送的資料。
>
> HTTPS 也可讓用戶端驗證所連接之伺服器的真實性。 這項功能可保護用戶端免于[攔截](https://en.wikipedia.org/wiki/Man-in-the-middle_attack)式攻擊。

這是保護 web 服務的一般程式：

1. 取得網域名稱。

2. 取得數位憑證。

3. 部署或更新已啟用 SSL 的 web 服務。

4. 更新您的 DNS 以指向 Web 服務。

> [!IMPORTANT]
> 如果您要部署到 Azure Kubernetes Service （AKS），您可以購買自己的憑證，或使用 Microsoft 所提供的憑證。 如果您使用來自 Microsoft 的憑證，就不需要取得功能變數名稱或 SSL 憑證。 如需詳細資訊，請參閱本文的[啟用 SSL 和部署](#enable)一節。

當您跨[部署目標](how-to-deploy-and-where.md)保護時，會有些微的差異。

## <a name="get-a-domain-name"></a>取得網域名稱

如果您還沒有功能變數名稱，請向*網域註冊機構*購買一個名稱。 「註冊機構」之間的程式和價格各不相同。 註冊機構會提供工具來管理功能變數名稱。 您可以使用這些工具，將完整功能變數名稱（FQDN）（例如 www\.contoso.com）對應至裝載 web 服務的 IP 位址。

## <a name="get-an-ssl-certificate"></a>取得 SSL 憑證

有許多方法可以取得 SSL 憑證（數位憑證）。 最常見的是向*憑證授權單位*單位（CA）購買一個。 無論您取得憑證的位置為何，您都需要下列檔案：

* **憑證**。 憑證必須包含完整的憑證鏈，而且必須是「PEM 編碼」。
* **金鑰**。 金鑰也必須以 PEM 編碼。

當您要求憑證時，必須提供您計畫用於 web 服務之位址的 FQDN （例如，www\.contoso.com）。 相較于驗證 web 服務的身分識別，會比較在憑證中加上戳記的位址，以及用戶端所使用的位址。 如果這些位址不相符，用戶端會收到錯誤訊息。

> [!TIP]
> 如果憑證授權單位單位無法提供憑證和金鑰做為 PEM 編碼的檔案，您可以使用[OpenSSL](https://www.openssl.org/)之類的公用程式來變更格式。

> [!WARNING]
> 使用*自我簽署*憑證僅供開發之用。 請勿在生產環境中使用它們。 自我簽署的憑證可能會造成用戶端應用程式的問題。 如需詳細資訊，請參閱用戶端應用程式所使用之網路程式庫的檔。

## <a id="enable"></a>啟用 SSL 並部署

若要在啟用 SSL 的情況下部署（或重新部署）服務，請將*ssl_enabled*參數設為 "True" （不論其適用的位置）。 將*ssl_certificate*參數設定為*憑證*檔案的值。 將*ssl_key*設定為*金鑰*檔的值。

### <a name="deploy-on-aks-and-field-programmable-gate-array-fpga"></a>在 AKS 和可現場程式化閘道陣列（FPGA）上部署

  > [!NOTE]
  > 當您部署設計工具的安全 web 服務時，本節中的資訊也適用。 如果您不熟悉如何使用 Python SDK，請參閱[什麼是適用于 python 的 AZURE MACHINE LEARNING SDK？](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)。

當您部署到 AKS 時，您可以建立新的 AKS 叢集，或附加現有的叢集。 如需建立或連接叢集的詳細資訊，請參閱[將模型部署到 Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)叢集。
  
-  如果您建立新的叢集，請使用 **[Provisioning_configuration AksCompute （）](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#provisioning-configuration-agent-count-none--vm-size-none--ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--location-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--service-cidr-none--dns-service-ip-none--docker-bridge-cidr-none--cluster-purpose-none--load-balancer-type-none--load-balancer-subnet-none-)** 。
- 如果您附加現有的叢集，請使用 **[Attach_configuration AksCompute （）](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)** 。 這兩個都會傳回具有**enable_ssl**方法的設定物件。

**Enable_ssl**方法可以使用由 Microsoft 提供的憑證，或您購買的憑證。

  * 當您使用 Microsoft 的憑證時，您必須使用*leaf_domain_label*參數。 此參數會產生服務的 DNS 名稱。 例如，"contoso" 的值會建立 "contoso\<六個隨機字元 > 的功能變數名稱。\<azureregion >. cloudapp. azure .com」，其中 \<azureregion > 是包含服務的區域。 （選擇性）您可以使用*overwrite_existing_domain*參數來覆寫現有的*leaf_domain_label*。

    若要在啟用 SSL 的情況下部署（或重新部署）服務，請將*ssl_enabled*參數設為 "True" （不論其適用的位置）。 將*ssl_certificate*參數設定為*憑證*檔案的值。 將*ssl_key*設定為*金鑰*檔的值。

    > [!IMPORTANT]
    > 當您使用 Microsoft 的憑證時，您不需要購買自己的憑證或功能變數名稱。

    下列範例示範如何建立可啟用 Microsoft 的 SSL 憑證的設定：

    ```python
    from azureml.core.compute import AksCompute
    # Config used to create a new AKS cluster and enable SSL
    provisioning_config = AksCompute.provisioning_configuration()
    # Leaf domain label generates a name using the formula
    #  "<leaf-domain-label>######.<azure-region>.cloudapp.azure.net"
    #  where "######" is a random series of characters
    provisioning_config.enable_ssl(leaf_domain_label = "contoso")


    # Config used to attach an existing AKS cluster to your workspace and enable SSL
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                          cluster_name = cluster_name)
    # Leaf domain label generates a name using the formula
    #  "<leaf-domain-label>######.<azure-region>.cloudapp.azure.net"
    #  where "######" is a random series of characters
    attach_config.enable_ssl(leaf_domain_label = "contoso")
    ```

  * 當您使用*購買的憑證*時，您可以使用*ssl_cert_pem_file*、 *ssl_key_pem_file*和*ssl_cname*參數。 下列範例示範如何使用*pem*檔案來建立設定，以使用您購買的 SSL 憑證：

    ```python
    from azureml.core.compute import AksCompute
    # Config used to create a new AKS cluster and enable SSL
    provisioning_config = AksCompute.provisioning_configuration()
    provisioning_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    # Config used to attach an existing AKS cluster to your workspace and enable SSL
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
    attach_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    ```

如需*enable_ssl*的詳細資訊，請參閱[AksProvisioningConfiguration. enable_ssl （）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksprovisioningconfiguration?view=azure-ml-py#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-)和[AksAttachConfiguration。 enable_ssl （）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksattachconfiguration?view=azure-ml-py#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-)。

### <a name="deploy-on-azure-container-instances"></a>在 Azure 容器實例上部署

當您部署至 Azure 容器實例時，您會提供 SSL 相關參數的值，如下列程式碼片段所示：

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(
    ssl_enabled=True, ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
```

如需詳細資訊，請參閱[AciWebservice。 deploy_configuration （）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-)。

## <a name="update-your-dns"></a>更新您的 DNS

接下來，您必須更新 DNS 以指向 Web 服務。

+ **針對容器實例：**

  使用您的網域註冊機構所提供的工具，更新功能變數名稱的 DNS 記錄。 記錄必須指向服務的 IP 位址。

  用戶端可以根據註冊機構和針對功能變數名稱設定的「存留時間」（TTL）來解析功能變數名稱，可能需要幾分鐘或數小時的時間。

+ **針對 AKS：**

  > [!WARNING]
  > 如果您使用*leaf_domain_label*透過 Microsoft 的憑證來建立服務，請勿手動更新叢集的 DNS 值。 此值應自動設定。

  在左窗格中 [**設定**] 底下的 [設定] 索引標籤**上，更新**AKS 叢集之公用 IP 位址的 DNS。 （請參閱下圖）。公用 IP 位址是在包含 AKS 代理程式節點和其他網路資源的資源群組下建立的資源類型。

  [![Azure Machine Learning：使用 SSL 保護 web 服務](./media/how-to-secure-web-service/aks-public-ip-address.png)](./media/how-to-secure-web-service/aks-public-ip-address-expanded.png)

## <a name="update-the-ssl-certificate"></a>更新 SSL 憑證

SSL 憑證過期，必須加以更新。 通常每年都會發生這種情況。 使用下列各節中的資訊，針對部署至 Azure Kubernetes Service 的模型，更新及更新您的憑證：

### <a name="update-a-microsoft-generated-certificate"></a>更新 Microsoft 產生的憑證

如果憑證原本是由 Microsoft 產生（使用*leaf_domain_label*來建立服務），請使用下列其中一個範例來更新憑證：

**使用 SDK**

```python
from azureml.core.compute import AksCompute
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute.aks import SslConfiguration

# Get the existing cluster
aks_target = AksCompute(ws, clustername)

# Update the existing certificate by referencing the leaf domain label
ssl_configuration = SslConfiguration(leaf_domain_label="myaks", overwrite_existing_domain=True)
update_config = AksUpdateConfiguration(ssl_configuration)
aks_target.update(update_config)
```

**使用 CLI**

```azurecli
az ml computetarget update aks -g "myresourcegroup" -w "myresourceworkspace" -n "myaks" --ssl-leaf-domain-label "myaks" --ssl-overwrite-domain True
```

如需詳細資訊，請參閱下列參考檔：

* [SslConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.sslconfiguration?view=azure-ml-py)
* [AksUpdateConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksupdateconfiguration?view=azure-ml-py)

### <a name="update-custom-certificate"></a>更新自訂憑證

如果憑證原本是由憑證授權單位單位所產生，請使用下列步驟：

1. 使用憑證授權單位單位所提供的檔來更新憑證。 此程式會建立新的憑證檔案。

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

* [SslConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.sslconfiguration?view=azure-ml-py)
* [AksUpdateConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksupdateconfiguration?view=azure-ml-py)

## <a name="disable-ssl"></a>停用 SSL

若要針對部署至 Azure Kubernetes Service 的模型停用 SSL，請使用 `status="Disabled"`建立 `SslConfiguration`，然後執行更新：

```python
from azureml.core.compute import AksCompute
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute.aks import SslConfiguration

# Get the existing cluster
aks_target = AksCompute(ws, clustername)

# Disable SSL
ssl_configuration = SslConfiguration(status="Disabled")
update_config = AksUpdateConfiguration(ssl_configuration)
aks_target.update(update_config)
```

## <a name="next-steps"></a>後續步驟
了解如何：
+ [使用部署為 Web 服務的機器學習模型](how-to-consume-web-service.md)
+ [在 Azure 虛擬網路內安全地執行實驗和推斷](how-to-enable-virtual-network.md)
