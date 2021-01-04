---
title: 使用防火牆
titleSuffix: Azure Machine Learning
description: 使用 Azure 防火牆來控制 Azure Machine Learning 工作區的存取權。 瞭解您必須透過防火牆允許的主機。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 11/18/2020
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 0fa3492555b2870ae7b95abec08bbd3280cdc985
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/20/2020
ms.locfileid: "97705059"
---
# <a name="use-workspace-behind-a-firewall-for-azure-machine-learning"></a>使用防火牆後方的工作區進行 Azure Machine Learning

在本文中，您將瞭解如何設定 Azure 防火牆，以控制對您 Azure Machine Learning 工作區和公用網際網路的存取。 若要深入瞭解如何保護 Azure Machine Learning，請參閱 [Azure Machine Learning 的企業安全性](concept-enterprise-security.md)。

> [!WARNING]
> 只有在 code first 體驗中才支援存取防火牆後方的資料儲存體。 不支援使用 [Azure Machine Learning studio](overview-what-is-machine-learning-studio.md) 存取防火牆背後的資料。 若要在具有 studio 的私人網路上使用資料存放區，您必須先 [設定虛擬網路](../virtual-network/quick-create-portal.md) ，並 [讓 studio 存取儲存在虛擬網路內的資料](how-to-enable-studio-virtual-network.md)。

## <a name="azure-firewall"></a>Azure 防火牆

使用 Azure 防火牆時，請使用 __目的地網路位址轉譯 (DNAT)__ 來建立輸入流量的 NAT 規則。 針對輸出流量，建立 __網路__ 和/或 __應用程式__ 規則。 這些規則集合會在 [一些 Azure 防火牆概念](../firewall/firewall-faq.md#what-are-some-azure-firewall-concepts)中更詳細地說明。

### <a name="inbound-configuration"></a>輸入設定

如果您使用 Azure Machine Learning __計算實例__ 或 __計算__ 叢集，請為包含 Azure Machine Learning 資源的子網新增 [使用者定義的路由 (udr)](../virtual-network/virtual-networks-udr-overview.md) 。 此路由會強制 __從__ 和資源的 ip 位址 `BatchNodeManagement` `AzureMachineLearning` 到計算實例和計算叢集的公用 ip 的流量。

這些 UDR 可讓 Batch 服務與計算節點通訊，以排程工作。 此外，也請為資源所在的 Azure Machine Learning 服務新增 IP 位址，必須有此 IP 位址才能存取計算執行個體。 若要取得 Batch 服務和 Azure Machine Learning 服務的 IP 位址清單，請使用下列其中一種方法：

* 下載 [Azure IP 範圍和服務標籤](https://www.microsoft.com/download/details.aspx?id=56519)並搜尋檔案中的 `BatchNodeManagement.<region>` 和 `AzureMachineLearning.<region>`，其中 `<region>` 是您的 Azure 區域。

* 使用 [Azure CLI](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest) 下載該資訊。 下列範例會下載 IP 位址資訊，並篩選出美國東部 2 區域的資訊：

    ```azurecli-interactive
    az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'Batch')] | [?properties.region=='eastus2']"
    az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'AzureMachineLearning')] | [?properties.region=='eastus2']"
    ```

    > [!TIP]
    > 如果您使用美國弗吉尼亞州、US-Arizona 地區或中國東部2區域，則這些命令不會傳回任何 IP 位址。 相反地，請使用下列其中一個連結來下載 IP 位址清單：
    >
    > * [適用于 Azure Government 的 Azure IP 範圍和服務標記](https://www.microsoft.com/download/details.aspx?id=57063)
    > * [適用于 Azure 中國的 Azure IP 範圍和服務標記](https://www.microsoft.com//download/details.aspx?id=57062)

當您新增 UDR 時，請為每個相關的「批次 IP」位址首碼定義路由，然後將 [下一個躍點類型] 設定為 [網際網路]。 下圖顯示此 UDR 在 Azure 入口網站中的範例：

![位址首碼的 UDR 範例](./media/how-to-enable-virtual-network/user-defined-route.png)

> [!IMPORTANT]
> IP 位址可能會隨著時間而變更。

如需詳細資訊，請參閱[在虛擬網路中建立 Azure Batch 集區](../batch/batch-virtual-network.md#user-defined-routes-for-forced-tunneling)。

### <a name="outbound-configuration"></a>輸出設定

1. 新增 __網路規則__， __以__ 允許 __來自__ 下列服務標記的流量：

    * AzureActiveDirectory
    * AzureMachineLearning
    * AzureResourceManager
    * 儲存區
    * KeyVault 區域
    * >microsoft.containerregistry 區域

    如果您打算使用 Microsoft 提供的預設 Docker 映射，以及啟用使用者管理的相依性，您也必須新增下列服務標記：

    * MicrosoftContainerRegistry 區域
    * AzureFrontDoor.FirstParty

    針對包含的專案 `region` ，將取代為您所使用的 Azure 區域。 例如： `keyvault.westus` 。

    針對 __通訊協定__，選取 `TCP` 。 針對 [來源] 和 [目的地 __埠__]，選取 `*` 。

1. 新增下列主機的 __應用程式規則__ ：

    > [!NOTE]
    > 這不是網際網路上所有 Python 資源所需的完整主機清單，只是最常使用的主機。 例如，如果您需要存取 GitHub 存放庫或其他主機，您必須在該案例中找出並新增所需的主機。

    | **主機名稱** | **目的** |
    | ---- | ---- |
    | **anaconda.com**</br>**\*. anaconda.com** | 用來安裝預設封裝。 |
    | **\*. anaconda.org** | 用來取得存放庫資料。 |
    | **pypi.org** | 用來列出預設索引的相依性（如果有的話），而且使用者設定不會覆寫索引。 如果覆寫索引，您也必須允許 **\* pythonhosted.org**。 |
    | **cloud.r-project.org** | 在安裝 R 開發的 CRAN 套件時使用。 |
    | **\*pytorch.org** | 以 PyTorch 為基礎的一些範例使用。 |
    | **\*. tensorflow.org** | 以 Tensorflow 為基礎的一些範例使用。 |

    針對 [ __通訊協定：埠__]，選取 [使用 __HTTP]、[HTTPs__]。

    如需有關設定應用程式規則的詳細資訊，請參閱 [部署和設定 Azure 防火牆](../firewall/tutorial-firewall-deploy-portal.md#configure-an-application-rule)。

1. 若要限制對部署至 Azure Kubernetes Service (AKS) 之模型的存取權，請參閱 [限制 Azure Kubernetes Service 中的輸出流量](../aks/limit-egress-traffic.md)。

## <a name="other-firewalls"></a>其他防火牆

本節中的指導方針是泛型，因為每個防火牆都有自己的術語和特定設定。 如果您有關于如何允許透過防火牆進行通訊的問題，請參閱您所使用之防火牆的檔。

如果未正確設定，防火牆可能會在使用您的工作區時發生問題。 Azure Machine Learning 工作區會使用各種不同的主機名稱。 下列各節列出 Azure Machine Learning 所需的主機。

### <a name="microsoft-hosts"></a>Microsoft 主機

本節中的主機是由 Microsoft 所擁有，並提供工作區正常運作所需的服務。 下表列出 Azure 公用、Azure Government 和 Azure 中國世紀區域的主機名稱。

**一般 Azure 主機**

| **必須用於** | **Azure 公用** | **Azure Government** | **Azure China 21Vianet** |
| ----- | ----- | ----- | ----- |
| Azure Active Directory | login.microsoftonline.com | login.microsoftonline.us | login.chinacloudapi.cn |
| Azure 入口網站 | management.azure.com | management.azure.us | management.azure.cn |

**Azure Machine Learning 主機**

| **必須用於** | **Azure 公用** | **Azure Government** | **Azure China 21Vianet** |
| ----- | ----- | ----- | ----- |
| Azure Machine Learning Studio | ml.azure.com | ml.azure.us | studio.ml.azure.cn |
| API |\*. azureml.ms | \*. ml.azure.us | \*. ml.azure.cn |
| 實驗、歷程記錄、Hyperdrive、標籤 | \*. experiments.azureml.net | \*. ml.azure.us | \*. ml.azure.cn |
| 模型管理 | \*. modelmanagement.azureml.net | \*. ml.azure.us | \*. ml.azure.cn |
| 管線 | \*. aether.ms | \*. ml.azure.us | \*. ml.azure.cn |
| Designer (studio 服務)  | \*. studioservice.azureml.com | \*. ml.azure.us | \*. ml.azure.cn |
| 整合式筆記本 | \*. notebooks.azure.net | \*. notebooks.usgovcloudapi.net |\*. notebooks.chinacloudapi.cn |
| 整合式筆記本 | \*. file.core.windows.net | \*. file.core.usgovcloudapi.net | \*. file.core.chinacloudapi.cn |
| 整合式筆記本 | \*.dfs.core.windows.net | \*. dfs.core.usgovcloudapi.net | \*. dfs.core.chinacloudapi.cn |
| 整合式筆記本 | \*.blob.core.windows.net | \*.blob.core.usgovcloudapi.net | \*. blob.core.chinacloudapi.cn |
| 整合式筆記本 | graph.microsoft.com | graph.microsoft.us | graph.chinacloudapi.cn |
| 整合式筆記本 | \*. aznbcontent.net |  | |

**Azure Machine Learning 計算實例和計算叢集主機**

| **必須用於** | **Azure 公用** | **Azure Government** | **Azure China 21Vianet** |
| ----- | ----- | ----- | ----- |
| 計算叢集/實例 | \*. batchai.core.windows.net | \*. batchai.core.usgovcloudapi.net |\*. batchai.ml.azure.cn |
| 計算執行個體 | \*. instances.azureml.net | \*. instances.azureml.us | \*. instances.azureml.cn |
| 計算執行個體 | \*. instances.azureml.ms |  |  |

**Azure Machine Learning 所使用的相關聯資源**

| **必須用於** | **Azure 公用** | **Azure Government** | **Azure China 21Vianet** |
| ----- | ----- | ----- | ----- |
| Azure 儲存體帳戶 | core.windows.net | core.usgovcloudapi.net | core.chinacloudapi.cn |
| Azure Key Vault | vault.azure.net | vault.usgovcloudapi.net | vault.azure.cn |
| Azure Container Registry | azurecr.io | azurecr.us | azurecr.cn |
| Microsoft 容器登錄 | mcr.microsoft.com | mcr.microsoft.com | mcr.microsoft.com |


> [!TIP]
> 如果您打算使用同盟身分識別，請遵循 [保護 Active Directory 同盟服務文章的最佳作法](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs) 。

此外，請使用 [強制通道](how-to-secure-training-vnet.md#forced-tunneling) 中的資訊，為和新增 IP 位址 `BatchNodeManagement` `AzureMachineLearning` 。

如需限制部署至 Azure Kubernetes Service (AKS) 的模型存取權的相關資訊，請參閱 [限制 Azure Kubernetes Service 中的輸出流量](../aks/limit-egress-traffic.md)。

### <a name="python-hosts"></a>Python 主機

本節中的主機用來安裝 Python 套件。 開發、定型和部署期間都需要它們。 

> [!NOTE]
> 這不是網際網路上所有 Python 資源所需的完整主機清單，只是最常使用的主機。 例如，如果您需要存取 GitHub 存放庫或其他主機，您必須在該案例中找出並新增所需的主機。

| **主機名稱** | **目的** |
| ---- | ---- |
| **anaconda.com**</br>**\*. anaconda.com** | 用來安裝預設封裝。 |
| **\*. anaconda.org** | 用來取得存放庫資料。 |
| **pypi.org** | 用來列出預設索引的相依性（如果有的話），而且使用者設定不會覆寫索引。 如果覆寫索引，您也必須允許 **\* pythonhosted.org**。 |
| **\*pytorch.org** | 以 PyTorch 為基礎的一些範例使用。 |
| **\*. tensorflow.org** | 以 Tensorflow 為基礎的一些範例使用。 |

### <a name="r-hosts"></a>R 主機

本節中的主機用來安裝 R 套件。 開發、定型和部署期間都需要它們。

> [!NOTE]
> 這不是網際網路上所有 R 資源所需的主機完整清單，只是最常使用的主機。 例如，如果您需要存取 GitHub 存放庫或其他主機，您必須在該案例中找出並新增所需的主機。

| **主機名稱** | **目的** |
| ---- | ---- |
| **cloud.r-project.org** | 在安裝 CRAN 套件時使用。 |

> [!IMPORTANT]
> 就內部而言，適用于 Azure Machine Learning 的 R SDK 會使用 Python 套件。 因此，您也必須允許 Python 主機通過防火牆。
## <a name="next-steps"></a>後續步驟

* [教學課程：使用 Azure 入口網站部署和設定 Azure 防火牆](../firewall/tutorial-firewall-deploy-portal.md)
* [保護 Azure 虛擬網路內的 Azure ML 實驗和推斷作業](how-to-network-security-overview.md)
