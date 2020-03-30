---
title: 使用 TLS 保護 Web 服務
titleSuffix: Azure Machine Learning
description: 瞭解如何啟用 HTTPS 以保護通過 Azure 機器學習部署的 Web 服務。 Azure 機器學習使用 TLS 版本 1.2 來保護作為 Web 服務部署的模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: a58b0120feaba907c62bc646f4f85d9185227fed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80287334"
---
# <a name="use-tls-to-secure-a-web-service-through-azure-machine-learning"></a>使用 TLS 通過 Azure 機器學習保護 Web 服務
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

本文介紹如何保護通過 Azure 機器學習部署的 Web 服務。

您可以使用[HTTPS](https://en.wikipedia.org/wiki/HTTPS)限制對 Web 服務的訪問，並保護用戶端提交的資料。 HTTPS 通過加密兩者之間的通信，説明保護用戶端和 Web 服務之間的通信。 加密使用[傳輸層安全 （TLS）。](https://en.wikipedia.org/wiki/Transport_Layer_Security) TLS 有時仍稱為*安全通訊端層*（SSL），它是 TLS 的前身。

> [!TIP]
> Azure 機器學習 SDK 對與安全通信相關的屬性使用術語"SSL"。 這並不意味著您的 Web 服務不使用*TLS。* SSL 只是一個更普遍識別的術語。
>
> 具體而言，通過 Azure 機器學習部署的 Web 服務僅支援 TLS 版本 1.2。

TLS 和 SSL 都依賴于*數位憑證*，這有助於加密和身份驗證。 有關數位憑證如何工作的詳細資訊，請參閱維琪百科主題["公開金鑰基礎結構](https://en.wikipedia.org/wiki/Public_key_infrastructure)"。

> [!WARNING]
> 如果您不使用 HTTPS 進行 Web 服務，則發送到和從該服務發送的資料可能對 Internet 上的其他人可見。
>
> HTTPS 還使用戶端能夠驗證其連接到的伺服器的真實性。 此功能可保護用戶端免受[中間人](https://en.wikipedia.org/wiki/Man-in-the-middle_attack)攻擊。

這是保護 Web 服務的一般過程：

1. 取得網域名稱。

2. 獲取數位憑證。

3. 啟用 TLS 後部署或更新 Web 服務。

4. 更新您的 DNS 以指向 Web 服務。

> [!IMPORTANT]
> 如果要部署到 Azure 庫伯奈斯服務 （AKS），則可以購買自己的證書或使用 Microsoft 提供的證書。 如果您使用 Microsoft 的證書，則無需獲取功能變數名稱或 TLS/SSL 憑證。 有關詳細資訊，請參閱本文的[啟用 TLS 和部署](#enable)部分。

當您在[部署目標](how-to-deploy-and-where.md)之間保護時，存在細微差異。

## <a name="get-a-domain-name"></a>取得網域名稱

如果您還沒有擁有功能變數名稱，則從*功能變數名稱註冊商*處購買功能變數名稱。 註冊商之間的流程和價格不同。 註冊商提供管理功能變數名稱的工具。 您可以使用這些工具將完全限定的功能變數名稱 （FQDN）（如 www\.contoso.com）映射到託管 Web 服務的 IP 位址。

## <a name="get-a-tlsssl-certificate"></a>獲取 TLS/SSL 憑證

有許多方法可以獲取 TLS/SSL 憑證（數位憑證）。 最常見的是從*憑證授權單位*（CA） 購買。 無論您從何處獲得證書，都需要以下檔：

* **憑證**。 證書必須包含完整的憑證連結，並且必須是"PEM 編碼的"。
* **金鑰**。 金鑰還必須對 PEM 進行編碼。

請求證書時，必須提供計畫用於 Web 服務的位址的 FQDN（例如，www\.contoso.com）。 比較標記到證書的位址和用戶端使用的位址以驗證 Web 服務的身份。 如果這些位址不匹配，用戶端將收到一條錯誤訊息。

> [!TIP]
> 如果憑證授權單位無法將證書和金鑰作為 PEM 編碼的檔提供，則可以使用[OpenSSL](https://www.openssl.org/)等實用程式來更改格式。

> [!WARNING]
> 僅使用*自簽名*證書進行開發。 不要在生產環境中使用它們。 自我簽署的憑證可能會造成用戶端應用程式的問題。 有關詳細資訊，請參閱用戶端應用程式使用的網路庫的文檔。

## <a name="enable-tls-and-deploy"></a><a id="enable"></a>啟用 TLS 並部署

要部署（或重新部署）啟用 TLS 的服務，請將*ssl_enabled*參數設置為"True"，只要適用。 將*ssl_certificate*參數設置為*證書*檔的值。 將*ssl_key*設置為*金鑰*檔的值。

### <a name="deploy-on-aks-and-field-programmable-gate-array-fpga"></a>部署在 AKS 和現場可程式設計柵極陣列 （FPGA） 上

  > [!NOTE]
  > 當您為設計器部署安全 Web 服務時，本節中的資訊也適用于。 如果您不熟悉使用 Python SDK，請參閱什麼是 Python[的 Azure 機器學習 SDK？](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)。

部署到 AKS 時，可以創建新的 AKS 群集或附加現有群集。 有關創建或附加群集的詳細資訊，請參閱[將模型部署到 Azure 庫伯奈斯服務群集](how-to-deploy-azure-kubernetes-service.md)。
  
-  如果創建新群集，請使用**[AksCompute.provisioning_configuration（）](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#provisioning-configuration-agent-count-none--vm-size-none--ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--location-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--service-cidr-none--dns-service-ip-none--docker-bridge-cidr-none--cluster-purpose-none--load-balancer-type-none--load-balancer-subnet-none-)**。
- 如果附加現有群集，請使用**[AksCompute.attach_configuration（）](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)**。 兩者都返回具有**enable_ssl**方法的設定物件。

**enable_ssl**方法可以使用 Microsoft 提供的證書或您購買的證書。

  * 使用 Microsoft 的證書時，必須使用*leaf_domain_label*參數。 此參數生成服務的 DNS 名稱。 例如，"contoso"的值創建一個功能變數名稱"contoso\<六隨機字元>。\<azure 區域>.cloudapp.azure.com"，其中\<azure 區域>是包含服務的區域。 或者，可以使用*overwrite_existing_domain*參數覆蓋現有*leaf_domain_label*。

    要部署（或重新部署）啟用 TLS 的服務，請將*ssl_enabled*參數設置為"True"，只要適用。 將*ssl_certificate*參數設置為*證書*檔的值。 將*ssl_key*設置為*金鑰*檔的值。

    > [!IMPORTANT]
    > 當您使用 Microsoft 的證書時，您無需購買自己的證書或功能變數名稱。

    以下示例演示如何創建啟用 Microsoft TLS/SSL 憑證的配置：

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

  * 使用*購買的證書時，* 可以使用*ssl_cert_pem_file、ssl_key_pem_file*和*ssl_cname*參數。 *ssl_key_pem_file* 以下示例演示如何使用 *.pem*檔創建使用您購買的 TLS/SSL 憑證的配置：

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

有關*enable_ssl*的詳細資訊，請參閱[Aks預配配置.enable_ssl（）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksprovisioningconfiguration?view=azure-ml-py#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-)和[AksAttach配置.enable_ssl（）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksattachconfiguration?view=azure-ml-py#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-)。

### <a name="deploy-on-azure-container-instances"></a>部署在 Azure 容器實例上

部署到 Azure 容器實例時，為與 TLS 相關的參數提供值，如以下程式碼片段所示：

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(
    ssl_enabled=True, ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
```

有關詳細資訊，請參閱[AciWebservice.deploy_configuration（）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-)。

## <a name="update-your-dns"></a>更新您的 DNS

接下來，您必須更新 DNS 以指向 Web 服務。

+ **對於容器實例：**

  使用功能變數名稱註冊商提供的工具更新功能變數名稱的 DNS 記錄。 記錄必須指向服務的 IP 位址。

  用戶端解析功能變數名稱的時間可能會延遲幾分鐘或幾小時，具體取決於註冊商和為功能變數名稱配置的"存留時間"（TTL）。

+ **對於 AKS：**

  > [!WARNING]
  > 如果使用*leaf_domain_label*使用 Microsoft 的證書創建服務，請不要手動更新群集的 DNS 值。 應自動設置該值。

  在左側窗格中的 **"設置"** 下的 **"配置"** 選項卡上更新 AKS 群集的公共 IP 位址的 DNS。 （請參閱下圖。公共 IP 位址是在包含 AKS 代理節點和其他網路資源的資源組下創建的資源類型。

  [![Azure 機器學習：使用 TLS 保護 Web 服務](./media/how-to-secure-web-service/aks-public-ip-address.png)](./media/how-to-secure-web-service/aks-public-ip-address-expanded.png)

## <a name="update-the-tlsssl-certificate"></a>更新 TLS/SSL 憑證

TLS/SSL 憑證過期，必須續訂。 通常每年都會發生這種情況。 使用以下部分中的資訊更新和續訂部署到 Azure Kubernetes 服務的模型的證書：

### <a name="update-a-microsoft-generated-certificate"></a>更新 Microsoft 生成的證書

如果證書最初由 Microsoft 生成（使用*leaf_domain_label*創建服務時），請使用以下示例之一更新證書：

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

有關詳細資訊，請參閱以下參考文檔：

* [Ssl 配置](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.sslconfiguration?view=azure-ml-py)
* [阿克塞更新配置](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksupdateconfiguration?view=azure-ml-py)

### <a name="update-custom-certificate"></a>更新自訂證書

如果證書最初由憑證授權單位生成，請使用以下步驟：

1. 使用憑證授權單位提供的文檔續訂證書。 此過程創建新的證書檔。

1. 使用 SDK 或 CLI 使用新證書更新服務：

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

有關詳細資訊，請參閱以下參考文檔：

* [Ssl 配置](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.sslconfiguration?view=azure-ml-py)
* [阿克塞更新配置](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksupdateconfiguration?view=azure-ml-py)

## <a name="disable-tls"></a>禁用 TLS

要禁用部署到 Azure 庫伯奈斯服務的模型的 TLS，請`SslConfiguration`創建`status="Disabled"`

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
+ [使用部署為 Web 服務的機器學習模型](how-to-consume-web-service.md)
+ [在 Azure 虛擬網路內安全地運行實驗和推理](how-to-enable-virtual-network.md)
