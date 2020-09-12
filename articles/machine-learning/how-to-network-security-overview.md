---
title: 虛擬網路隔離和隱私權總覽
titleSuffix: Azure Machine Learning
description: 使用隔離的 Azure 虛擬網路與 Azure Machine Learning 來保護工作區資源和計算環境。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 07/07/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, references_regions
ms.openlocfilehash: 57746b833e238bbd0cc99ba103f710a9239ee5ba
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2020
ms.locfileid: "89664081"
---
# <a name="virtual-network-isolation-and-privacy-overview"></a>虛擬網路隔離和隱私權總覽

在本文中，您將瞭解如何使用虛擬網路 (Vnet) 來保護 Azure Machine Learning 中的網路通訊。 本文使用範例案例，示範如何設定完整的虛擬網路。

本文是五部分系列的第一篇，將逐步引導您保護 Azure Machine Learning 的工作流程。 強烈建議您先閱讀這篇簡介文章，以瞭解這些概念。 

以下是本系列的其他文章：

**1. VNet 總覽**  >  [2。保護工作區](how-to-secure-workspace-vnet.md)  >  [3。保護定型環境](how-to-secure-training-vnet.md)  >  [4。保護推斷環境](how-to-secure-inferencing-vnet.md)  >  [5。啟用 studio 功能](how-to-enable-studio-virtual-network.md)

## <a name="prerequisites"></a>必要條件

本文假設您已熟悉下列主題：
+ [Azure 虛擬網路](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)
+ [IP 網路](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)
+ [Azure Private Link](how-to-configure-private-link.md)
+ [網路安全性群組 (NSG)](../virtual-network/security-overview.md)
+ [網路防火牆](../firewall/overview.md)

## <a name="example-scenario"></a>範例案例

在本節中，您會瞭解如何設定一般網路案例，以保護與私人 IP 位址 Azure Machine Learning 通訊。

下表比較服務如何存取 Azure Machine Learning 網路的不同部分，以及 VNet 和沒有 VNet。

| 案例 | 工作區 | 相關聯的資源 | 訓練計算環境 | 推斷計算環境 |
|-|-|-|-|-|-|
|**沒有虛擬網路**| 公用 IP | 公用 IP | 公用 IP | 公用 IP |
|**保護虛擬網路中的資源**| 私人 IP (私人端點)  | 公用 IP (服務端點)  <br> **等於** <br> 私人 IP (私人端點)  | 私人 IP | 私人 IP  | 

* **工作區** -從您的 VNet 建立私人端點，以連線至工作區上的 Private Link。 私人端點會透過數個私人 IP 位址，將工作區連接至 vnet。
* **相關聯的資源** -使用服務端點或私人端點來連線至工作區資源，例如 azure 儲存體、Azure Key Vault 和 Azure Container service。
    * **服務端點** 會將虛擬網路的身分識別提供給 Azure 服務。 當您在虛擬網路中啟用服務端點之後，您可以新增虛擬網路規則，以保護虛擬網路的 Azure 服務資源。 服務端點會使用公用 IP 位址。
    * **私人端點** 是一種網路介面，可安全地將您連接到 Azure Private Link 提供的服務。 私人端點會使用您 VNet 中的私人 IP 位址，有效地將服務帶入您的 VNet 中。
* 使用私人 IP 位址來**訓練計算存取**權定型計算目標，例如 Azure Machine Learning 計算實例，以及 Azure Machine Learning 計算叢集。 
* **推斷計算存取權** -存取 Azure Kubernetes SERVICES (AKS) 具有私人 IP 位址的計算叢集。


接下來的五個章節將說明如何保護上述的網路案例。 若要保護您的網路，您必須：

1. 保護 [**工作區和相關聯的資源**](#secure-the-workspace-and-associated-resources)。
1. 保護 [**定型環境**](#secure-the-training-environment)。
1. 保護 [**推斷環境**](#secure-the-inferencing-environment)。
1. 選擇性： [**啟用 studio 功能**](#optional-enable-studio-functionality)。
1. 設定 [**防火牆設定**](#configure-firewall-settings)

> [!TIP]
>  虛擬網路和 Azure 服務的部分組合需要企業版工作區。 請使用下表來了解需要 Enterprise Edition 的狀況：
>
> | 狀況 | Enterprise</br>edition | 基本</br>edition |
> | ----- |:-----:|:-----:| 
> | 沒有虛擬網路或 Private Link | ✔ | ✔ |
> | 沒有 Private Link 的工作區。 虛擬網路中的其他資源 (Azure Container Registry 除外) | ✔ | ✔ |
> | 沒有 Private Link 的工作區。 具有 Private Link 的其他資源 | ✔ | |
> | 具有 Private Link 的工作區。 虛擬網路中的其他資源 (Azure Container Registry 除外) | ✔ | ✔ |
> | 具有 Private Link 的工作區和任何其他資源 | ✔ | |
> | 具有 Private Link 的工作區。 沒有 Private Link 或虛擬網路的其他資源 | ✔ | ✔ |
> | 虛擬網路中的 Azure Container Registry | ✔ | |
> | 工作區的客戶管理金鑰 | ✔ | |
>


## <a name="secure-the-workspace-and-associated-resources"></a>保護工作區和相關聯的資源

使用下列步驟來保護您的工作區和相關聯的資源。 這些步驟可讓您的服務在虛擬網路中進行通訊。

1. 建立 [已啟用 Private Link 的工作區](how-to-secure-workspace-vnet.md#secure-the-workspace-with-private-endpoint) ，以啟用您的 VNet 和工作區之間的通訊。
1. 將 Azure Key Vault 新增至具有 [服務端點](../key-vault/general/overview-vnet-service-endpoints.md) 或 [私人端點](../key-vault/general/private-link-service.md)的虛擬網路。 將 Key Vault 設定為「 [允許信任的 Microsoft 服務略過此防火牆](how-to-secure-workspace-vnet.md#secure-azure-key-vault)」。
1. 將您的 Azure 儲存體帳戶新增至具有[服務端點](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts)或[私人端點](../storage/common/storage-private-endpoints.md)的虛擬網路
1. [將 Azure Container Registry 設定為使用私人端點](how-to-secure-workspace-vnet.md#enable-azure-container-registry-acr) ，並 [在 Azure 容器實例中啟用子網委派](how-to-secure-inferencing-vnet.md#enable-azure-container-instances-aci)。

![此架構圖表顯示工作區和相關聯的資源如何透過服務端點或 VNet 內的私人端點彼此通訊](./media/how-to-network-security-overview/secure-workspace-resources.png)

如需如何完成這些步驟的詳細指示，請參閱 [保護 Azure Machine Learning 工作區](how-to-secure-workspace-vnet.md)。 

### <a name="limitations"></a>限制

保護您的工作區和虛擬網路中的相關聯資源具有下列限制：
- 工作區 Private Link 僅適用于下欄區域： eastus、westus2、southcentralus
    - 這項限制不適用於相關聯的資源。 例如，您可以在任何 Azure Machine Learning 區域中啟用 VNet 以進行儲存。
- 所有資源都必須位於相同的 VNet 後方。 不過，也允許相同 VNet 內的子網。
- 某些 studio 功能（例如設計工具、AutoML、標籤和資料分析）不能與設定為使用私人端點的儲存體帳戶搭配使用。 如果您需要使用這些 studio 功能，請改用服務端點。

## <a name="secure-the-training-environment"></a>保護定型環境

在本節中，您將瞭解如何在 Azure Machine Learning 中保護定型環境。 您也會瞭解 Azure Machine Learning 如何完成訓練作業，以瞭解網路設定如何一起運作。

若要保護定型環境，請使用下列步驟：

1. [在虛擬網路中建立 Azure Machine Learning 計算實例和電腦](how-to-secure-training-vnet.md#compute-instance)叢集，以執行定型工作。
1. [允許來自 Azure Batch 服務的輸入通訊](how-to-secure-training-vnet.md#mlcports) ，讓 Batch 服務可以將作業提交至您的計算資源。 

![顯示如何保護受控計算叢集和實例的架構圖表](./media/how-to-network-security-overview/secure-training-environment.png)

如需如何完成這些步驟的詳細指示，請參閱 [保護定型環境](how-to-secure-training-vnet.md)。 

### <a name="example-training-job-submission"></a>定型作業提交範例 

在本節中，您會瞭解 Azure Machine Learning 如何在服務之間安全地進行通訊，以提交定型作業。 這會顯示您所有的設定如何一起運作，以保護通訊安全。

1. 用戶端會將定型腳本和定型資料上傳至使用服務或私人端點保護的儲存體帳戶。

1. 用戶端會透過私人端點將定型作業提交至 Azure Machine Learning 工作區。

1. Azure Batch 的服務會從工作區接收工作，並透過以計算資源布建的公用負載平衡器，將定型作業提交至計算環境。 

1. 計算資源會接收作業並開始訓練。 計算資源會存取安全的儲存體帳戶，以下載訓練檔案和上傳輸出。 

![顯示如何在使用 VNet 時提交 Azure Machine Learning 定型作業的架構圖表](./media/how-to-network-security-overview/secure-training-job-submission.png)


### <a name="limitations"></a>限制

- Azure 計算實例和 Azure 計算叢集必須位於與工作區相同的 VNet、區域和訂用帳戶及其相關聯的資源。 

## <a name="secure-the-inferencing-environment"></a>保護推斷環境

在本節中，您會瞭解可用來保護推斷環境的選項。 建議您使用 Azure Kubernetes Services (AKS) 叢集，以進行高規模的生產環境部署。

您有兩個選項可讓您在虛擬網路中 AKS 叢集：

- 將預設 AKS 叢集部署或連結至您的 VNet。
- 將私人 AKS 叢集附加至您的 VNet。

**預設 AKS** 叢集具有具有公用 IP 位址的控制平面。 您可以在部署期間將預設 AKS 叢集新增至 VNet，或在建立叢集之後將其連接。

**私用 AKS** 叢集具有控制平面，只能透過私人 ip 存取。 您必須在建立叢集之後附加私用 AKS 叢集。

如需有關如何新增預設和私人叢集的詳細指示，請參閱 [安全推斷環境](how-to-secure-inferencing-vnet.md)。 

下列網狀圖顯示安全的 Azure Machine Learning 工作區，其中包含附加至虛擬網路的私人 AKS 叢集。

![顯示如何將私人 AKS 叢集連接至虛擬網路的架構圖表。 AKS 控制平面位於客戶 VNet 之外](./media/how-to-network-security-overview/secure-inferencing-environment.png)

### <a name="limitations"></a>限制
- AKS 叢集必須屬於與工作區和其相關聯資源相同的 VNet。 

## <a name="optional-enable-studio-functionality"></a>選擇性：啟用 studio 功能

[保護工作區](#secure-the-workspace-and-associated-resources)  > [保護定型環境](#secure-the-training-environment)  > [保護推斷環境](#secure-the-inferencing-environment)  > **啟用 studio 功能**  > [設定防火牆設定](#configure-firewall-settings)

雖然 studio 可以存取以服務端點設定的儲存體帳戶中的資料，但某些功能預設為停用：

* 預覽 studio 中的資料。
* 視覺化設計工具中的資料。
* 提交 AutoML 實驗。
* 開機磁碟區標專案。

若要在使用儲存體服務端點時啟用完整功能，請參閱 [在虛擬網路中使用 Azure Machine Learning studio](how-to-enable-studio-virtual-network.md#access-data-using-the-studio)。 目前，studio 不支援儲存體私人端點。

### <a name="limitations"></a>限制
- Studio 無法存取設定為使用私人端點之儲存體帳戶中的資料。 如需完整的功能，您必須使用服務端點來儲存及使用受控識別。

## <a name="configure-firewall-settings"></a>設定防火牆設定

設定您的防火牆，以控制 Azure Machine Learning 工作區資源和公用網際網路的存取權。 雖然我們建議使用 Azure 防火牆，但您應該能夠使用其他防火牆產品來保護您的網路。 如果您有關于如何允許透過防火牆進行通訊的問題，請參閱您所使用之防火牆的檔。

如需防火牆設定的詳細資訊，請參閱在 [防火牆後方使用工作區](how-to-access-azureml-behind-firewall.md)。

## <a name="next-steps"></a>接下來的步驟

本文是四部分虛擬網路系列的第一篇。 請參閱文章的其餘部分，以瞭解如何保護虛擬網路：

* [第2部分：虛擬網路總覽](how-to-secure-workspace-vnet.md)
* [第3部分：保護定型環境的安全](how-to-secure-training-vnet.md)
* [第4部分：保護推斷環境](how-to-secure-inferencing-vnet.md)
* [第5部分：啟用 studio 功能](how-to-enable-studio-virtual-network.md)
