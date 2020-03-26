---
title: 教學課程 - 調整 Azure Red Hat OpenShift 叢集
description: 了解如何使用 Azure CLI 調整 Microsoft Azure Red Hat OpenShift 叢集
author: jimzim
ms.author: jzim
ms.topic: tutorial
ms.service: container-service
ms.date: 05/06/2019
ms.openlocfilehash: c6334aa20b543dfbf87fedcfe45d54bbcf7a219a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "79477012"
---
# <a name="tutorial-scale-an-azure-red-hat-openshift-cluster"></a>教學課程：調整 Azure Red Hat OpenShift 叢集

本教學課程是一個系列的第二部分。 您將了解如何使用 Azure CLI 建立 Microsoft Azure Red Hat OpenShift 叢集、進行調整，然後予以刪除來清除資源。

在本系列的第二部分中，您將瞭解如何：

> [!div class="checklist"]
> * 調整 Red Hat OpenShift 叢集

在本教學課程系列中，您將了解如何：
> [!div class="checklist"]
> * [建立 Azure Red Hat OpenShift 叢集](tutorial-create-cluster.md)
> * 調整 Azure Red Hat OpenShift 叢集
> * [刪除 Azure Red Hat OpenShift 叢集](tutorial-delete-cluster.md)

## <a name="prerequisites"></a>Prerequisites

開始進行本教學課程之前：

* 依照[建立 Azure Red Hat OpenShift 叢集](tutorial-create-cluster.md)教學課程的指示建立叢集。

## <a name="step-1-sign-in-to-azure"></a>步驟 1:登入 Azure

如果您是在本機執行 Azure CLI，請執行 `az login` 以登入 Azure。

```azurecli
az login
```

如果您可存取多個訂用帳戶，請執行 `az account set -s {subscription ID}` 並以您要使用的訂用帳戶取代 `{subscription ID}`。

## <a name="step-2-scale-the-cluster-with-additional-nodes"></a>步驟 2:以額外的節點調整叢集

從 Bash 終端機，將 CLUSTER_NAME 變數設為您的叢集名稱：

```bash
CLUSTER_NAME=yourclustername
```

現在，我們將使用 Azure CLI 將叢集調整為五個節點：

```azurecli
az openshift scale --resource-group $CLUSTER_NAME --name $CLUSTER_NAME --compute-count 5
```

`az openshift scale` 在幾分鐘後即會順利完成並傳回 JSON 文件，其中包含已調整的叢集詳細資料。

## <a name="next-steps"></a>後續步驟

在教學課程的這個部分中，您已了解如何：

> [!div class="checklist"]
> * 調整 Azure Red Hat OpenShift 叢集

前進到下一個教學課程：
> [!div class="nextstepaction"]
> [刪除 Azure Red Hat OpenShift 叢集](tutorial-delete-cluster.md)
