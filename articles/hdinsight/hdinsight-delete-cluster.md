---
title: 如何刪除 HDInsight 叢集 - Azure
description: 可供您刪除 Azure HDInsight 叢集之各種方式的資訊
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.date: 11/29/2019
ms.openlocfilehash: 3903500e87dd162779d1732a4cfe405ba34e702c
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87074735"
---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-cli"></a>使用您的瀏覽器、PowerShell 或 Azure CLI 刪除 HDInsight 叢集

HDInsight 叢集的計費起自叢集建立時，終至叢集刪除時。 計費是以每分鐘按比例計算，因此當您的叢集不再使用時，您應該一律刪除該叢集。 在本檔中，您將瞭解如何使用 [ [Azure 入口網站](https://portal.azure.com)]、[ [Azure PowerShell Az 模組](https://docs.microsoft.com/powershell/azure/)] 和 [ [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)] 來刪除叢集。

> [!IMPORTANT]  
> 刪除 HDInsight 叢集並不會刪除與叢集建立關聯的 Azure 儲存體帳戶或 Data Lake Storage。 您可以在日後重複使用這些服務中儲存的資料。

## <a name="azure-portal"></a>Azure 入口網站

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 從左側功能表中，流覽至 [**所有服務**] [分析] [HDInsight 叢集]  >  **Analytics**  >  **HDInsight clusters** ，然後選取您的叢集。

3. 從預設的 [] 視圖中，選取 [**刪除**] 圖示。 依照提示刪除您的叢集。

    ![HDInsight 刪除叢集按鈕](./media/hdinsight-delete-cluster/hdinsight-delete-cluster.png)

## <a name="azure-powershell"></a>Azure PowerShell

以 `CLUSTERNAME` 您的 HDInsight 叢集名稱取代下列程式碼中的。 從 PowerShell 提示字元中，輸入下列命令來刪除叢集：

```powershell
Remove-AzHDInsightCluster -ClusterName CLUSTERNAME
```

## <a name="azure-cli"></a>Azure CLI

`CLUSTERNAME`請以您的 HDInsight 叢集名稱取代，並 `RESOURCEGROUP` 使用下列程式碼中的資源組名取代。  從命令提示字元中，輸入下列內容以刪除叢集：

```azurecli
az hdinsight delete --name CLUSTERNAME --resource-group RESOURCEGROUP
```
