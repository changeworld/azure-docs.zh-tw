---
title: 如何停止監視您的 Azure 和 Red Hat OpenShift v4 叢集 |Microsoft Docs
description: 本文說明如何使用適用于容器的 Azure 監視器來停止監視 Azure Red Hat OpenShift 和 Red Hat OpenShift 第4版叢集。
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: bf61457b9c8cff40eb3fee2c93c7184fbaae6db5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87091142"
---
# <a name="how-to-stop-monitoring-your-azure-and-red-hat-openshift-v4-cluster"></a>如何停止監視您的 Azure 和 Red Hat OpenShift v4 叢集

在您啟用 Azure Red Hat OpenShift 和 Red Hat OpenShift 4.x 叢集的監視之後，如果您決定不想再監視叢集，可以停止使用容器的 Azure 監視器來監視叢集。 本文說明如何完成這項工作。  

## <a name="how-to-stop-monitoring-using-helm"></a>如何使用 Helm 停止監視

1. 若要先找出叢集上安裝的容器 helm 圖表版本的 Azure 監視器，請執行下列 helm 命令。

    ```
    helm list
    ```

    輸出看起來會像下面這樣：

    ```
    NAME                            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
    azmon-containers-release-1      default         3               2020-04-21 15:27:24.1201959 -0700 PDT   deployed        azuremonitor-containers-2.7.0   7.0.0-1
    ```

    *azmon-容器-發行-1* 表示容器 Azure 監視器的 helm 圖表版本。

2. 若要刪除圖表版本，請執行下列 helm 命令。

    `helm delete <releaseName>`

    範例：

    `helm delete azmon-containers-release-1`

    這會從叢集中移除發行。 您可以藉由 `helm list` 執行下列命令來進行驗證：

    ```
    NAME                            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
    ```

可能需要幾分鐘的時間才能完成設定變更。 因為 Helm 會在您刪除您的發行之後進行追蹤，所以您可以使用來審核叢集的歷程記錄，甚至取消刪除發行 `helm rollback` 。

## <a name="next-steps"></a>接下來的步驟

如果已建立 Log Analytics 工作區來支援監視叢集，且不再需要該工作區，則您必須手動將其刪除。 如果您不熟悉如何刪除工作區，請參閱 [刪除 Azure Log Analytics 工作區](../platform/delete-workspace.md)。
