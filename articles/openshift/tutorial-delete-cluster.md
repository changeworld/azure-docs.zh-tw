---
title: 教學課程 - 刪除 Azure Red Hat OpenShift 叢集
description: 在本教學課程中，請了解如何使用 Azure CLI 刪除 Azure Red Hat OpenShift 叢集
author: jimzim
ms.author: jzim
ms.topic: tutorial
ms.service: container-service
ms.date: 05/06/2019
ms.openlocfilehash: c335236a2b0b05f03bef1ebef37f1129a5d0352b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "76278763"
---
# <a name="tutorial-delete-an-azure-red-hat-openshift-cluster"></a>教學課程：刪除 Azure Red Hat OpenShift 叢集

本教學課程即將結束。 當您完成範例叢集的測試後，可以依照下列方式刪除叢集及其相關聯的資源，以避免未使用的部分產生費用。

在系列的第三部分中，您將了解如何：

> [!div class="checklist"]
> * 刪除 Azure Red Hat OpenShift 叢集

在本教學課程系列中，您將了解如何：
> [!div class="checklist"]
> * [建立 Azure Red Hat OpenShift 叢集](tutorial-create-cluster.md)
> * [調整 Azure Red Hat OpenShift 叢集](tutorial-scale-cluster.md)
> * 刪除 Azure Red Hat OpenShift 叢集

## <a name="prerequisites"></a>Prerequisites

開始進行本教學課程之前：

* 依照[建立 Azure Red Hat OpenShift 叢集](tutorial-create-cluster.md)教學課程的指示建立叢集。

## <a name="step-1-sign-in-to-azure"></a>步驟 1:登入 Azure

如果您是在本機執行 Azure CLI，請執行 `az login` 以登入 Azure。

```bash
az login
```

如果您可存取多個訂用帳戶，請執行 `az account set -s {subscription ID}` 並以您要使用的訂用帳戶取代 `{subscription ID}`。

## <a name="step-2-delete-the-cluster"></a>步驟 2:選取叢集

開啟 Bash 終端機，並將 CLUSTER_NAME 變數設為您的叢集名稱：

```bash
CLUSTER_NAME=yourclustername
```

現在，刪除您的叢集：

```bash
az openshift delete --resource-group $CLUSTER_NAME --name $CLUSTER_NAME
```

系統會詢問您是否要刪除叢集。 在您以 `y` 進行確認後，系統會以數分鐘的時間刪除叢集。 命令完成後，整個資源群組及其包含的所有資源都會刪除，包括叢集在內。

## <a name="deleting-a-cluster-using-the-azure-portal"></a>使用 Azure 入口網站刪除叢集

或者，您可以透過線上 Azure 入口網站刪除與您的叢集相關聯的資源群組。 資源群組的名稱與您的叢集名稱相同。

目前，您在建立叢集時建立的 `Microsoft.ContainerService/openShiftManagedClusters` 資源會隱藏在 Azure 入口網站中。 在 `Resource group` 檢視中，核取 `Show hidden types` 以檢視資源群組。

![隱藏類型核取方塊的螢幕擷取畫面](./media/aro-portal-hidden-type.png)

刪除資源群組時，將一併刪除您在建置 Azure Red Hat OpenShift 叢集時建立的所有相關資源。

## <a name="next-steps"></a>後續步驟

在教學課程的這個部分中，您已了解如何：
> [!div class="checklist"]
> * 刪除 Azure Red Hat OpenShift 叢集

參考官方 [Red Hat OpenShift 文件](https://docs.openshift.com/aro/welcome/index.html)以深入了解如何使用 OpenShift
