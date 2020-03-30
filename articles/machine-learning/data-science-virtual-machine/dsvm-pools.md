---
title: 共用池
titleSuffix: Azure Data Science Virtual Machine
description: 瞭解如何創建&將資料科學虛擬機器 （DSVM） 的共用池部署為團隊的共用資源。
keywords: 深度學習, 資料科學工具, 資料科學虛擬機器, 地理空間分析, 小組資料科學流程
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 12/10/2018
ms.openlocfilehash: cc0efc0a076ddc3fc9425999f1e38b4a32dec7a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477335"
---
# <a name="create-a-shared-pool-of-data-science-virtual-machines"></a>建立共用的資料科學虛擬機器集區

在本文中，您將瞭解如何為團隊創建資料科學虛擬機器 （DSVM） 的共用池。 使用共用池的好處包括更好的資源利用率、更輕鬆的共用和協作以及更有效地管理 DSVM 資源。

您可以使用許多方法和技術來建立 DSVM 集區。 本文重點介紹互動式虛擬機器 （VM） 的池。 替代的受控計算基礎結構是 Azure Machine Learning Compute。 有關詳細資訊，請參閱[設置計算目標](../how-to-set-up-training-targets.md#amlcompute)。

## <a name="interactive-vm-pool"></a>互動式虛擬機器集區

整個 AI/資料科學小組所共用的互動式 VM 集區可讓使用者登入 DSVM 的可用執行個體，而不必讓每一組使用者擁有專用的執行個體。 此設置可實現更好的可用性和更有效的資源利用。

您可以使用[Azure 虛擬機器縮放集](https://docs.microsoft.com/azure/virtual-machine-scale-sets/)技術來創建互動式 VM 池。 您可以使用擴展集來建立和管理一組負載平衡且可自動調整的相同 VM。

使用者會登入主要集區的 IP 或 DNS 位址。 擴展集會自動將工作階段路由傳送至擴展集內的可用 DSVM。 由於使用者希望有一個一致且熟悉的環境，而不管他們登錄到哪個 VM，因此規模集中的所有 VM 實例都裝載共用網路磁碟機，如 Azure 檔共用或網路檔案系統 （NFS） 共用。 使用者的共用工作區通常會保存在已掛接在每個執行個體上的共用檔案存放區。

您可以在 [GitHub](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json) 上找到使用 Ubuntu DSVM 執行個體建立擴展集的 Azure Resource Manager 範本範例。 您將在同一位置找到 Azure 資源管理器範本的[參數檔](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.parameters.json)示例。

您可以通過在 Azure CLI 中指定參數檔的值，從 Azure 資源管理器範本創建比例集：

```azurecli-interactive
az group create --name [[NAME OF RESOURCE GROUP]] --location [[ Data center. For eg: "West US 2"]
az group deployment create --resource-group  [[NAME OF RESOURCE GROUP ABOVE]]  --template-uri https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json --parameters @[[PARAMETER JSON FILE]]
```

上述命令假設您有：

* 參數檔案的複本，內含針對您的擴展集執行個體指定的值。
* 虛擬機器執行個體的數目。
* Azure 檔案服務共用的指標。
* 每個 VM 上將掛接的儲存體帳戶的認證。

在命令中，參數檔會在本機參考。 您也可以透過內嵌方式傳遞參數，或在指令碼中提示這些參數。  

上述範本可在前端擴展集到後端 Ubuntu DSVM 集區之間實現 SSH 和 Jupyterhub 連接埠。 作為使用者，您以正常方式在安全外殼 （SSH） 或 JupyterHub 上登錄到 VM。 由於 VM 實例可以動態向上或向下縮放，因此必須將任何狀態保存在裝載的 Azure 檔共用中。 您可以使用相同的方法來建立 Windows DSVM 集區。

在 GitHub 的 Azure DataScienceVM 存放庫中也可取得[可掛接 Azure 檔案服務共用的指令碼](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Extensions/General/mountazurefiles.sh)。 此指令碼會在參數檔案中指定的掛接點掛接 Azure 檔案服務共用。 此指令碼也會在初始使用者的主目錄中建立所掛接磁碟機的軟式連結。 Azure 檔共用中特定于使用者的筆記本目錄與`$HOME/notebooks/remote`該目錄進行軟連結，以便使用者可以訪問、運行和保存其 Jupyter 筆記本。 在虛擬機器上建立其他使用者，以將每個使用者的 Jupyter 工作區指向 Azure 檔案服務共用時，也可以使用相同的慣例。

虛擬機器擴展集支援自動調整。 您可以設置有關何時創建其他實例和何時縮小實例的規則。 例如，您可以相應減少為零個執行個體，以在完全不使用 VM 時節省雲端硬體使用成本。 虛擬機器擴展集的文件頁面會提供[自動調整](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview)的詳細步驟。

## <a name="next-steps"></a>後續步驟

* [設定通用身分識別](dsvm-common-identity.md)
* [安全地儲存用來存取雲端資源的認證](dsvm-secure-access-keys.md)
