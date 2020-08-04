---
title: 使用防火牆
titleSuffix: Azure Machine Learning
description: 使用 Azure 防火牆控制 Azure Machine Learning 工作區的存取權。 深入瞭解您必須透過防火牆允許的主機，Azure Machine Learning 才能正常運作。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 07/17/2020
ms.custom: how-to, tracking-python
ms.openlocfilehash: 27b625dfa31b366d95922e1dd0bad7fda6e86ed4
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2020
ms.locfileid: "87540065"
---
# <a name="use-workspace-behind-a-firewall-for-azure-machine-learning"></a>針對 Azure Machine Learning 使用防火牆後方的工作區

在本文中，您將瞭解如何設定 Azure 防火牆以搭配 Azure Machine Learning 工作區使用。

> [!IMPORTANT]
> 雖然本檔中的資訊是以 Azure 防火牆為基礎，但您應該能夠將它與其他防火牆產品搭配使用。 如果您有關于如何允許透過防火牆進行通訊的問題，請參閱您所使用之防火牆的檔。

您可以使用 Azure 防火牆來控制 Azure Machine Learning 工作區和公用網際網路的存取權。 如果未正確設定，防火牆可能會在使用您的工作區時發生問題。 Azure Machine Learning 工作區會使用各種主機名稱，如本文中所述。

## <a name="network-rules"></a>網路規則

在您的防火牆上，建立網路規則，允許本文中進出位址的流量。

> [!TIP]
> 新增網路規則時，請將__通訊協定__設定為 [任何]，並將埠設為 `*` 。
>
> 如需設定 Azure 防火牆的詳細資訊，請參閱[部署和設定 Azure 防火牆](../firewall/tutorial-firewall-deploy-portal.md#configure-a-network-rule)。

## <a name="microsoft-hosts"></a>Microsoft 主機

本節中的主機是由 Microsoft 所擁有，並提供工作區正常運作所需的服務。

| **主機名稱** | **目的** |
| ---- | ---- |
| **\*. batchai.core.windows.net** | 定型叢集 |
| **ml.azure.com** | Azure Machine Learning Studio |
| **default.exp-tas.com** | 由 Azure Machine Learning studio 使用 |
| **\*. azureml.ms** | Azure Machine Learning Api 使用 |
| **\*. experiments.azureml.net** | 由 Azure Machine Learning 中執行的實驗所使用 |
| **\*. modelmanagement.azureml.net** | 用來註冊和部署模型|
| **mlworkspace.azure.ai** | 在查看工作區時由 Azure 入口網站使用 |
| **\*. aether.ms** | 在執行 Azure Machine Learning 管線時使用 |
| **\*. instances.azureml.net** | Azure Machine Learning 計算實例 |
| **\*. instances.azureml.ms** | 當工作區已啟用私用連結時，Azure Machine Learning 計算實例 |
| **windows.net** | Azure Blob 儲存體 |
| **vault.azure.net** | Azure 金鑰保存庫 |
| **azurecr.io** | Azure Container Registry |
| **mcr.microsoft.com** | 適用于基底 docker 映射的 Microsoft Container Registry |
| **your-acr-server-name.azurecr.io** | 只有當您的 Azure Container Registry 位於虛擬網路後方時才需要。 在此設定中，會從 Microsoft 環境建立私人連結至您訂用帳戶中的 ACR 實例。 針對您的 Azure Machine Learning 工作區使用 ACR 伺服器名稱。 |

## <a name="python-hosts"></a>Python 主機

本節中的主機是用來安裝 Python 套件。 開發、訓練和部署期間都需要它們。 

| **主機名稱** | **目的** |
| ---- | ---- |
| **anaconda.com** | 用來安裝預設封裝。 |
| **\*. anaconda.org** | 用來取得存放庫資料。 |
| **pypi.org** | 用來列出來自預設索引的相依性（如果有的話），而且使用者設定不會覆寫索引。 如果覆寫索引，您也必須允許** \* pythonhosted.org**。 |

## <a name="r-hosts"></a>R 主機

本節中的主機是用來安裝 R 套件。 開發、訓練和部署期間都需要它們。

> [!IMPORTANT]
> 就內部而言，適用于 Azure Machine Learning 的 R SDK 會使用 Python 套件。 因此，您也必須允許 Python 主機通過防火牆。

| **主機名稱** | **目的** |
| ---- | ---- |
| **cloud.r-project.org** | 在安裝 CRAN 套件時使用。 |

後續步驟

* [[部署和設定 Azure 防火牆](../firewall/tutorial-firewall-deploy-portal.md)]
* [在 Azure 虛擬網路中保護 Azure ML 實驗和推斷作業](how-to-enable-virtual-network.md)
