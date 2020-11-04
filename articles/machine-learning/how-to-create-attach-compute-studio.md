---
title: '建立定型 & 部署 (studio 的計算) '
titleSuffix: Azure Machine Learning
description: 使用 studio 來建立訓練和部署計算資源 (針對機器學習) 的計算目標
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 08/06/2020
ms.topic: conceptual
ms.custom: how-to, contperfq1
ms.openlocfilehash: 6cb455880852295d7176e813208a93919a2c14bb
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93318264"
---
# <a name="create-compute-targets-for-model-training-and-deployment-in-azure-machine-learning-studio"></a>在 Azure Machine Learning studio 中建立模型定型和部署的計算目標

在本文中，您將瞭解如何在 Azure Machine studio 中建立和管理計算目標。  您也可以使用下列方式來建立和管理計算目標：

* Azure Machine Learning Learning SDK 或適用于 Azure Machine Learning 的 CLI 擴充功能
  * [計算執行個體](how-to-create-manage-compute-instance.md)
  * [計算叢集](how-to-create-attach-compute-cluster.md)
  * [Azure Kubernetes Service 叢集](how-to-create-attach-kubernetes.md)
  * [其他計算資源](how-to-attach-compute-targets.md)
* Azure Machine Learning 的 [VS Code 延伸](how-to-manage-resources-vscode.md#compute-clusters) 模組。


## <a name="prerequisites"></a>必要條件

* 如果您沒有 Azure 訂用帳戶，請在開始前先建立免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning](https://aka.ms/AMLFree)
* [Azure Machine Learning 工作區](how-to-manage-workspace.md)

## <a name="whats-a-compute-target"></a>什麼是計算目標？ 

使用 Azure Machine Learning，您可以在各種資源或環境 (統稱為 [__計算目標__](concept-azure-machine-learning-architecture.md#compute-targets)) 上將您的模型定型。 計算目標可以是本機電腦或雲端資源，例如 Azure Machine Learning Compute、Azure HDInsight 或遠端虛擬機器。  您也可以建立用於部署模型的計算目標，如[模型的部署位置和方法](how-to-deploy-and-where.md)中所述。

## <a name="view-compute-targets"></a><a id="portal-view"></a>檢視計算目標

若要查看工作區的所有計算目標，請使用下列步驟：

1. 瀏覽至 [Azure Machine Learning Studio](https://ml.azure.com)。
 
1. 在 [ __管理__ ] 底下，選取 [ __計算__ ]。

1. 選取頂端的索引標籤，以顯示每種類型的計算目標。

    :::image type="content" source="media/how-to-create-attach-studio/view-compute-targets.png" alt-text="查看計算目標的清單":::

## <a name="create-compute-target"></a><a id="portal-create"></a>建立計算目標

依照上述步驟來檢視計算目標的清單。 接著，使用下列步驟建立計算目標：

1. 選取頂端的索引標籤，以對應到您將建立的計算類型。

1. 如果您沒有任何計算目標，請選取頁面中間的 [  **建立** ]。
  
    :::image type="content" source="media/how-to-create-attach-studio/create-compute-target.png" alt-text="建立計算目標":::

1. 如果您看到計算資源的清單，請選取清單上方的 [ **+ 新增** ]。

    :::image type="content" source="media/how-to-create-attach-studio/select-new.png" alt-text="選取 [新增]":::


1. 填寫您計算類型的表單：

  * [計算執行個體](#compute-instance)
  * [計算叢集](#amlcompute)
  * [推斷叢集](#inference-clusters)
  * [附加的計算](#attached-compute)

1. 選取 [建立]。

1. 從清單選取計算目標以檢視建立作業的狀態：

    :::image type="content" source="media/how-to-create-attach-studio/view-list.png" alt-text="從清單中查看計算狀態":::


### <a name="compute-instance"></a>計算執行個體

使用 [上述步驟](#portal-create) 來建立計算實例。  然後填寫表單，如下所示：

:::image type="content" source="media/concept-compute-instance/create-compute-instance.png" alt-text="建立新的計算實例":::


|欄位  |描述  |
|---------|---------|
|計算名稱     |  <li>名稱是必要的，且長度必須介於3到24個字元之間。</li><li>有效字元是大寫和小寫字母、數位及  **-** 字元。</li><li>名稱必須以字母開頭</li><li>名稱在 Azure 區域內的所有現有計算中都必須是唯一的。 如果您選擇的名稱不是唯一的，您會看到警示</li><li>如果 **-**  使用字元，則在名稱後面必須接著至少一個字母</li>     |
|虛擬機器類型 |  選擇 CPU 或 GPU。 建立之後，即無法變更此類型     |
|虛擬機器大小     |  受支援的虛擬機器大小可能會在您的區域中受到限制。 檢查 [可用性清單](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)     |
|啟用/停用 SSH 存取     |   預設會停用 SSH 存取。  無法使用 SSH 存取。 在建立後變更。 如果您打算以互動方式使用[VS Code 遠端](how-to-set-up-vs-code-remote.md)進行調試，請務必啟用存取   |
|進階設定     |  選擇性。 設定虛擬網路。 指定 **資源群組** 、 **虛擬網路** 和 **子網** ，以在 Azure 虛擬網路內建立計算實例， (vnet) 。 如需詳細資訊，請參閱這些 vnet 的 [網路需求](./how-to-secure-training-vnet.md) 。  |

### <a name="compute-clusters"></a><a name="amlcompute"></a> 計算叢集

針對您的訓練、batch 推斷或增強式學習工作負載，建立單一或多個節點的計算叢集。 使用 [上述步驟](#portal-create) 來建立計算叢集。  然後填寫表單，如下所示：


|欄位  |描述  |
|---------|---------|
|計算名稱     |  <li>名稱是必要的，且長度必須介於3到24個字元之間。</li><li>有效字元是大寫和小寫字母、數位及  **-** 字元。</li><li>名稱必須以字母開頭</li><li>名稱在 Azure 區域內的所有現有計算中都必須是唯一的。 如果您選擇的名稱不是唯一的，您會看到警示</li><li>如果 **-**  使用字元，則在名稱後面必須接著至少一個字母</li>     |
|虛擬機器類型 |  選擇 CPU 或 GPU。 建立之後，即無法變更此類型     |
|虛擬機器優先順序 | 選擇 [ **專用** ] 或 [ **低] 優先順序** 。  低優先順序的虛擬機器較便宜，但不保證計算節點。 您的作業可能會被優先佔用。
|虛擬機器大小     |  受支援的虛擬機器大小可能會在您的區域中受到限制。 檢查 [可用性清單](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)     |
|最小節點數目 | 您要布建的節點數目下限。 如果您想要有專用數目的節點，請在這裡設定該計數。 將最小值設定為0，即可節省成本，因此當叢集閒置時，您不需要支付任何節點的費用。 |
|節點數目上限 | 您要布建的節點數目上限。 當提交工作時，計算會自動調整為此節點計數的最大值。 |
|進階設定     |  選擇性。 設定虛擬網路。 指定 **資源群組** 、 **虛擬網路** 和 **子網** ，以在 Azure 虛擬網路內建立計算實例， (vnet) 。 如需詳細資訊，請參閱這些 vnet 的 [網路需求](./how-to-secure-training-vnet.md) 。   同時附加 [受控](#managed-identity) 識別以授與資源的存取權     |

#### <a name="set-up-managed-identity"></a><a name="managed-identity"></a> 設定受控識別

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-intro.md)]

在叢集建立期間或編輯計算叢集詳細資料時，請在 [ **Advanced settings** ] 中，切換 **指派受控識別** ，並指定系統指派的身分識別或使用者指派的身分識別。

#### <a name="managed-identity-usage"></a>受控識別使用方式

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-default.md)]

### <a name="inference-clusters"></a>推斷叢集

> [!IMPORTANT]
> 搭配使用 Azure Kubernetes Service 與 Azure Machine Learning 有多個設定選項。 某些案例（例如網路）需要額外的設定和設定。 如需搭配使用 AKS 與 Azure ML 的詳細資訊，請參閱 [建立和附加 Azure Kubernetes Service](how-to-create-attach-kubernetes.md)叢集。

針對大規模推斷建立或附加 Azure Kubernetes Service (AKS) 叢集。 使用 [上述步驟](#portal-create) 來建立 AKS 叢集。  然後填寫表單，如下所示：


|欄位  |描述  |
|---------|---------|
|計算名稱     |  <li>需要名稱。 名稱的長度必須介於2到16個字元之間。 </li><li>有效字元是大寫和小寫字母、數位及  **-** 字元。</li><li>名稱必須以字母開頭</li><li>名稱在 Azure 區域內的所有現有計算中都必須是唯一的。 如果您選擇的名稱不是唯一的，您會看到警示</li><li>如果 **-**  使用字元，則在名稱後面必須接著至少一個字母</li>     |
|Kubernetes Service | 選取 [ **建立新** 的]，填寫表單的其餘部分。  或選取 [ **使用現有** 的]，然後從您的訂用帳戶中選取現有的 AKS 叢集。
|區域 |  選取將在其中建立叢集的區域 |
|虛擬機器大小     |  受支援的虛擬機器大小可能會在您的區域中受到限制。 檢查 [可用性清單](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)     |
|叢集用途  | 選取 **生產** 或 **開發/測試** |
|節點數目 | 節點數目乘以虛擬機器的核心數目 (個 vcpu) 必須大於或等於12。 |
| 網路組態 | 選取 [ **Advanced** ]，在現有的虛擬網路內建立計算。 如需在虛擬網路中 AKS 的詳細資訊，請參閱 [使用私人端點和虛擬網路進行定型和推斷期間的網路隔離](./how-to-secure-inferencing-vnet.md)。 |
| 啟用 SSL 設定 | 使用此設定來設定計算上的 SSL 憑證 |

### <a name="attached-compute"></a>附加的計算

若要使用在 Azure Machine Learning 外建立的計算目標，您必須進行連結。 連結計算目標可讓您的工作區使用它們。  使用 **附加計算** 來附加用於 **定型** 的計算目標。  使用 **推斷** 叢集來附加 AKS 叢集以進行 **推斷** 。

使用 [上述步驟](#portal-create) 來附加計算。  然後填寫表單，如下所示：

1. 輸入計算目標的名稱。 
1. 選取要附加的計算類型。 並非所有計算類型都可以從 Azure Machine Learning Studio 連結。 目前可以附加以進行定型的目標類型包括：
    * 遠端 VM
    * Azure Databricks (適用於機器學習管線)
    * Azure Data Lake Analytics (適用於機器學習管線)
    * Azure HDInsight

1. 填寫表單，並提供必要屬性的值。

    > [!NOTE]
    > Microsoft 建議使用 SSH 金鑰，因為它們比密碼更安全。 密碼很容易遭受暴力密碼破解攻擊。 SSH 金鑰需要密碼編譯簽章。 如需有關如何建立 SSH 金鑰以搭配 Azure 虛擬機器使用的資訊，請參閱下列文件：
    >
    > * [在 Linux 或 macOS 上建立及使用 SSH 金鑰](../virtual-machines/linux/mac-create-ssh-keys.md)
    > * [在 Windows 上建立及使用 SSH 金鑰](../virtual-machines/linux/ssh-from-windows.md)

1. 選取 [附加]。 


## <a name="next-steps"></a>後續步驟

建立目標並將其連結至您的工作區之後，您可以在 [執行](how-to-set-up-training-targets.md) 設定中搭配 `ComputeTarget` 物件使用它：

```python
from azureml.core.compute import ComputeTarget
myvm = ComputeTarget(workspace=ws, name='my-vm-name')
```

* 使用計算資源來 [提交定型](how-to-set-up-training-targets.md)回合。
* [教學課程：定型模型](tutorial-train-models-with-aml.md) 使用受控計算目標來定型模型。
* 了解如何[有效率地微調超參數](how-to-tune-hyperparameters.md)以便建置更好的模型。
* 擁有定型的模型之後，請了解[部署模型的方式和位置](how-to-deploy-and-where.md)。
* [搭配使用 Azure Machine Learning 與 Azure 虛擬網路](./how-to-network-security-overview.md)