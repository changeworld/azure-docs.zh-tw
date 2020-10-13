---
title: 使用防火牆
titleSuffix: Azure Machine Learning
description: 使用 Azure 防火牆來控制 Azure Machine Learning 工作區的存取權。 瞭解您必須透過防火牆允許的主機，Azure Machine Learning 才能正確運作。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 07/17/2020
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 081c07be49178be2415edccbfc2026336eb8a8a5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90604405"
---
# <a name="use-workspace-behind-a-firewall-for-azure-machine-learning"></a>使用防火牆後方的工作區進行 Azure Machine Learning

在本文中，您將瞭解如何設定 Azure 防火牆，以控制對您 Azure Machine Learning 工作區和公用網際網路的存取。   若要深入瞭解如何保護 Azure Machine Learning，請參閱 [Azure Machine Learning 的企業安全性](concept-enterprise-security.md)

雖然本檔中的資訊是以使用 [Azure 防火牆](../firewall/tutorial-firewall-deploy-portal.md)為基礎，但您應該能夠搭配其他防火牆產品使用。 如果您有關于如何允許透過防火牆進行通訊的問題，請參閱您所使用之防火牆的檔。

## <a name="application-rules"></a>應用程式規則

在您的防火牆上，建立 _應用程式規則_ ，以允許來自本文中位址的流量。

> [!TIP]
> 新增網路規則時，將 __通訊協定__ 設定為 [任何]，並將埠設定為 `*` 。
>
> 如需有關設定 Azure 防火牆的詳細資訊，請參閱 [部署和設定 Azure 防火牆](../firewall/tutorial-firewall-deploy-portal.md#configure-an-application-rule)。

## <a name="routes"></a>路由

設定包含 Azure Machine Learning 資源之子網的輸出路由時，請使用 [ [強制通道](how-to-secure-training-vnet.md#forced-tunneling) ] 區段中的指引來保護定型環境。

## <a name="microsoft-hosts"></a>Microsoft 主機

如果未正確設定，防火牆可能會在使用您的工作區時發生問題。 Azure Machine Learning 工作區會使用各種不同的主機名稱。

本節中的主機是由 Microsoft 所擁有，並提供工作區正常運作所需的服務。

| **主機名稱** | **目的** |
| ---- | ---- |
| **login.microsoftonline.com** | 驗證 |
| **management.azure.com** | 用來取得工作區資訊 |
| **\*. batchai.core.windows.net** | 定型叢集 |
| **ml.azure.com** | Azure Machine Learning Studio |
| **default.exp-tas.com** | 由 Azure Machine Learning studio 使用 |
| **\*. azureml.ms** | Azure Machine Learning Api 使用 |
| **\*. experiments.azureml.net** | 由 Azure Machine Learning 中執行的實驗所使用 |
| **\*. modelmanagement.azureml.net** | 用來註冊和部署模型|
| **mlworkspace.azure.ai** | 在觀看工作區時由 Azure 入口網站使用 |
| **\*. aether.ms** | 在執行 Azure Machine Learning 管線時使用 |
| **\*. instances.azureml.net** | Azure Machine Learning 計算實例 |
| **\*. instances.azureml.ms** | 當工作區已啟用 Private Link 時 Azure Machine Learning 計算實例 |
| **windows.net** | Azure Blob 儲存體 |
| **vault.azure.net** | Azure 金鑰保存庫 |
| **azurecr.io** | Azure Container Registry |
| **mcr.microsoft.com** | 適用于基底 docker 映射的 Microsoft Container Registry |
| **your-acr-server-name.azurecr.io** | 只有當您的 Azure Container Registry 位於虛擬網路後方時才需要。 在此設定中，會在您的訂用帳戶中，從 Microsoft 環境建立私人連結至 ACR 實例。 針對您的 Azure Machine Learning 工作區使用 ACR server 名稱。 |
| **\*. notebooks.azure.net** | Azure Machine Learning studio 中的筆記本需要。 |
| **graph.windows.net** | 筆記本所需 |

> [!TIP]
> 如果您打算使用同盟身分識別，請遵循 [保護 Active Directory 同盟服務文章的最佳作法](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs) 。

## <a name="python-hosts"></a>Python 主機

本節中的主機用來安裝 Python 套件。 開發、定型和部署期間都需要它們。 

| **主機名稱** | **目的** |
| ---- | ---- |
| **anaconda.com**</br>**\*. anaconda.com** | 用來安裝預設封裝。 |
| **\*. anaconda.org** | 用來取得存放庫資料。 |
| **pypi.org** | 用來列出預設索引的相依性（如果有的話），而且使用者設定不會覆寫索引。 如果覆寫索引，您也必須允許** \* pythonhosted.org**。 |

## <a name="r-hosts"></a>R 主機

本節中的主機用來安裝 R 套件。 開發、定型和部署期間都需要它們。

> [!IMPORTANT]
> 就內部而言，適用于 Azure Machine Learning 的 R SDK 會使用 Python 套件。 因此，您也必須允許 Python 主機通過防火牆。

| **主機名稱** | **目的** |
| ---- | ---- |
| **cloud.r-project.org** | 在安裝 CRAN 套件時使用。 |

## <a name="azure-government-region"></a>Azure Government 區域

Azure Government 區域所需的 Url。

| **主機名稱** | **目的** |
| ---- | ---- |
| **usgovarizona.api.ml.azure.us** | US-Arizona 區域 |
| **usgovvirginia.api.ml.azure.us** | US-Virginia 區域 |

## <a name="next-steps"></a>後續步驟

* [教學課程：使用 Azure 入口網站部署和設定 Azure 防火牆](../firewall/tutorial-firewall-deploy-portal.md)
* [保護 Azure 虛擬網路內的 Azure ML 實驗和推斷作業](how-to-network-security-overview.md)
