---
title: 共用集區
titleSuffix: Azure Data Science Virtual Machine
description: 瞭解如何建立 & 將資料科學虛擬機器 (Dsvm) 的共用集區部署為小組的共用資源。
keywords: 深度學習, 資料科學工具, 資料科學虛擬機器, 地理空間分析, 小組資料科學流程
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 12/10/2018
ms.openlocfilehash: 0745957620aab7ed4d08cb016c706b56e6da1c5a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91708963"
---
# <a name="create-a-shared-pool-of-data-science-virtual-machines"></a>建立共用的資料科學虛擬機器集區

在本文中，您將瞭解如何為小組 (Dsvm) 建立資料科學虛擬機器的共用集區。 使用共用集區的優點包括更佳的資源使用率、更輕鬆的共用和共同作業，以及更有效地管理 DSVM 資源。

您可以使用許多方法和技術來建立 DSVM 集區。 本文著重于 (Vm) 的互動式虛擬機器集區。 替代的受控計算基礎結構是 Azure Machine Learning Compute。 如需詳細資訊，請參閱 [建立計算](../how-to-create-attach-compute-cluster.md)叢集。

## <a name="interactive-vm-pool"></a>互動式虛擬機器集區

整個 AI/資料科學小組所共用的互動式 VM 集區可讓使用者登入 DSVM 的可用執行個體，而不必讓每一組使用者擁有專用的執行個體。 這項設定可提供更佳的可用性和更有效的資源使用率。

您可以使用 [Azure 虛擬機器擴展集](https://docs.microsoft.com/azure/virtual-machine-scale-sets/) 技術來建立互動式 VM 集區。 您可以使用擴展集來建立和管理一組負載平衡且可自動調整的相同 VM。

使用者會登入主要集區的 IP 或 DNS 位址。 擴展集會自動將工作階段路由傳送至擴展集內的可用 DSVM。 由於使用者想要使用一致且熟悉的環境，不論它們所登入的 VM 為何，擴展集中的 VM 的所有實例都會掛接共用網路磁碟機機，例如 Azure 檔案儲存體共用或網路檔案系統 (NFS) 共用。 使用者的共用工作區通常會保存在已掛接在每個執行個體上的共用檔案存放區。

您可以在 [GitHub](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json) 上找到使用 Ubuntu DSVM 執行個體建立擴展集的 Azure Resource Manager 範本範例。 您可以在相同位置找到 Azure Resource Manager 範本的 [參數](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.parameters.json) 檔範例。

您可以藉由在 Azure CLI 中指定參數檔的值，從 Azure Resource Manager 範本建立擴展集：

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

上述範本可在前端擴展集到後端 Ubuntu DSVM 集區之間實現 SSH 和 Jupyterhub 連接埠。 以使用者的身份，您以一般方式登入安全殼層 (SSH) 或 JupyterHub 上的 VM。 因為 VM 實例可以動態地相應增加或減少，所以任何狀態都必須儲存在掛接的 Azure 檔案儲存體共用中。 您可以使用相同的方法來建立 Windows DSVM 集區。

在 GitHub 的 Azure DataScienceVM 存放庫中也可取得[可掛接 Azure 檔案服務共用的指令碼](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Extensions/General/mountazurefiles.sh)。 此指令碼會在參數檔案中指定的掛接點掛接 Azure 檔案服務共用。 此指令碼也會在初始使用者的主目錄中建立所掛接磁碟機的軟式連結。 Azure 檔案儲存體共用中的使用者特定筆記本目錄會與目錄進行軟連結， `$HOME/notebooks/remote` 讓使用者可以存取、執行及儲存其 Jupyter 的筆記本。 在虛擬機器上建立其他使用者，以將每個使用者的 Jupyter 工作區指向 Azure 檔案服務共用時，也可以使用相同的慣例。

虛擬機器擴展集支援自動調整。 您可以設定有關何時建立其他實例的規則，以及何時縮小實例的時機。 例如，您可以相應減少為零個執行個體，以在完全不使用 VM 時節省雲端硬體使用成本。 虛擬機器擴展集的文件頁面會提供[自動調整](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview)的詳細步驟。

## <a name="next-steps"></a>後續步驟

* [設定通用身分識別](dsvm-common-identity.md)
* [安全地儲存用來存取雲端資源的認證](dsvm-secure-access-keys.md)
