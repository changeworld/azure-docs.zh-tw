---
title: 教學課程 - 刪除 Azure Red Hat OpenShift 叢集
description: 在本教學課程中，請了解如何使用 Azure CLI 刪除 Azure Red Hat OpenShift 叢集
author: sakthi-vetrivel
ms.custom: fasttrack-edit
ms.author: suvetriv
ms.topic: tutorial
ms.service: container-service
ms.date: 04/24/2020
ms.openlocfilehash: 254a9737b805aeeae2008923a8178cd971602132
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92677757"
---
# <a name="tutorial-delete-an-azure-red-hat-openshift-4-cluster"></a>教學課程：刪除 Azure Red Hat OpenShift 4 叢集

在本教學課程中 (三部中的第三部)，我們會刪除執行 OpenShift 4 的 Azure Red Hat OpenShift 叢集。 您會了解如何：

> [!div class="checklist"]
> * 刪除 Azure Red Hat OpenShift 叢集


## <a name="before-you-begin"></a>開始之前

在先前的教學課程中，我們已使用 OpenShift Web 主控台建立 Azure Red Hat OpenShift 叢集並與之連線。 如果您尚未完成這些步驟，而且想要跟著做，請從[教學課程 1 - 建立 Azure Red Hat OpenShift 4 叢集](tutorial-create-cluster.md)開始。

如果您選擇在本機安裝和使用 CLI，本教學課程會要求您執行 Azure CLI 2.6.0 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)。

## <a name="sign-in-to-azure"></a>登入 Azure

如果您是在本機執行 Azure CLI，請執行 `az login` 以登入 Azure。

```bash
az login
```

如果您可存取多個訂用帳戶，請執行 `az account set -s {subscription ID}` 並以您要使用的訂用帳戶取代 `{subscription ID}`。

## <a name="delete-the-cluster"></a>選取叢集

在先前的教學課程中，已設定下列變數。

```bash
CLUSTER=yourclustername
RESOURCEGROUP=yourresourcegroup
```

使用這些值來刪除您的叢集：

```bash
az aro delete --resource-group $RESOURCEGROUP --name $CLUSTER
```

系統會詢問您是否要刪除叢集。 在您以 `y` 進行確認後，系統會以數分鐘的時間刪除叢集。 命令完成後，整個資源群組及其包含的所有資源都會刪除，包括叢集在內。

## <a name="next-steps"></a>後續步驟

在教學課程的這個部分中，您已了解如何：
> [!div class="checklist"]
> * 刪除 Azure Red Hat OpenShift 4 叢集

參考官方 [Red Hat OpenShift 文件](https://www.openshift.com/try)以深入了解如何使用 OpenShift
