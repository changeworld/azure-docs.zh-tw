---
title: 如何刪除 HDInsight 叢集 - Azure
description: 有關刪除 Azure HDInsight 群集的各種方法的資訊
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.date: 11/29/2019
ms.openlocfilehash: 2912777c001a36ec913e2cfd618091ada5aa107a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74807131"
---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-cli"></a>使用您的瀏覽器、PowerShell 或 Azure CLI 刪除 HDInsight 叢集

HDInsight 叢集的計費起自叢集建立時，終至叢集刪除時。 計費是每分鐘按比例計費的，因此，當群集不再使用時，應始終將其刪除。 在本文檔中，您將瞭解如何使用[Azure 門戶](https://portal.azure.com)[、Azure PowerShell Az 模組](https://docs.microsoft.com/powershell/azure/overview)和 Azure [CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)刪除群集。

> [!IMPORTANT]  
> 刪除 HDInsight 叢集並不會刪除與叢集建立關聯的 Azure 儲存體帳戶或 Data Lake Storage。 您可以在日後重複使用這些服務中儲存的資料。

## <a name="azure-portal"></a>Azure 入口網站

1. 登錄到 Azure[門戶](https://portal.azure.com)。

2. 從左側功能表導航到分析**Analytics** > **HDInsight 群集****的所有服務** > 並選擇群集。

3. 在預設視圖中，選擇 **"刪除**"圖示。 按照提示刪除群集。

    ![HDInsight 刪除群集按鈕](./media/hdinsight-delete-cluster/hdinsight-delete-cluster.png)

## <a name="azure-powershell"></a>Azure PowerShell

在`CLUSTERNAME`下面的代碼中替換為 HDInsight 群集的名稱。 從 PowerShell 提示符中，輸入以下命令以刪除群集：

```powershell
Remove-AzHDInsightCluster -ClusterName CLUSTERNAME
```

## <a name="azure-cli"></a>Azure CLI

替換為`CLUSTERNAME`HDInsight 群集的名稱，並在`RESOURCEGROUP`下面的代碼中替換資源組的名稱。  從命令提示符中，輸入以下內容以刪除群集：

```azurecli
az hdinsight delete --name CLUSTERNAME --resource-group RESOURCEGROUP
```
