---
title: 使用防火牆後方的 Azure Machine Learning
titleSuffix: Azure Machine Learning
description: 安全地使用 Azure 防火牆後方的 Azure Machine Learning。 深入瞭解您必須透過防火牆允許的主機，Azure Machine Learning 才能正常運作。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 04/27/2020
ms.openlocfilehash: 2c5fe9d46ef408775cc4b99d4820a652a91683c4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82196318"
---
# <a name="use-azure-machine-learning-workspace-behind-azure-firewall"></a>使用 Azure 防火牆後方的 Azure Machine Learning 工作區

本文包含有關設定 Azure 防火牆以與 Azure Machine Learning 搭配使用的資訊。

您可以使用 Azure 防火牆來控制 Azure Machine Learning 工作區和公用網際網路的存取權。 如果未正確設定，防火牆可能會在使用您的工作區時發生問題。

## <a name="network-rules"></a>網路規則

在您的防火牆上，建立網路規則，允許本文中進出位址的流量。

> [!TIP]
> 新增網路規則時，請將__通訊協定__設定為 [任何]，並`*`將埠設為。
>
> 如需設定 Azure 防火牆的詳細資訊，請參閱[部署和設定 Azure 防火牆](../firewall/tutorial-firewall-deploy-portal.md#configure-a-network-rule)。

## <a name="microsoft-hosts"></a>Microsoft 主機

本節中的主機是由 Microsoft 所擁有，並提供工作區正常運作所需的服務。

| **主機名稱** | **目的** |
| ---- | ---- |
| **\*. batchai.core.windows.net** | 定型叢集 |
| **ml.azure.com** | Azure Machine Learning Studio |
| **\*. azureml.ms** | Azure Machine Learning Api 使用 |
| **\*. experiments.azureml.net** | 由 Azure Machine Learning 中執行的實驗所使用|
| **\*. modelmanagement.azureml.net** | 用來註冊和部署模型|
| **mlworkspace.azure.ai** | 在查看工作區時由 Azure 入口網站使用 |
| **\*. aether.ms** | 在執行 Azure Machine Learning 管線時使用 |
| **\*. instances.azureml.net** | Azure Machine Learning 計算實例 |
| **windows.net** | Azure Blob 儲存體 |
| **vault.azure.net** | Azure 金鑰保存庫 |
| **microsoft.com** | 基底 docker 映射 |
| **azurecr.io** | Azure Container Registry |

## <a name="python-hosts"></a>Python 主機

本節中的主機是用來安裝 Python 套件。 開發、訓練和部署期間都需要它們。 

| **主機名稱** | **目的** |
| ---- | ---- |
| **anaconda.com** | 安裝 conda 套件時使用 |
| **pypi.org** | 安裝 pip 套件時使用 |

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