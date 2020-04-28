---
title: 如何停止監視混合式 Kubernetes 叢集 |Microsoft Docs
description: 本文說明如何使用容器的 Azure 監視器來停止監視混合式 Kubernetes 叢集。
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: f2f3a8671c1f2baf60d399cc87f2f843dfee4f70
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82196214"
---
# <a name="how-to-stop-monitoring-your-hybrid-cluster"></a>如何停止監視混合式叢集

啟用監視 Azure Stack 或內部部署上執行的 Kubernetes 叢集之後，如果您決定不想再監視該叢集，可以使用容器的 Azure 監視器停止監視該叢集。 本文說明如何完成這項工作。  

## <a name="how-to-stop-monitoring-using-helm"></a>如何使用 Helm 停止監視

1. 若要先找出安裝在叢集上的容器 helm 圖版本的 Azure 監視器，請執行下列 helm 命令。

    ```
    helm list
    ```

    輸出看起來會像下面這樣：

    ```
    NAME                            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
    azmon-containers-release-1      default         3               2020-04-21 15:27:24.1201959 -0700 PDT   deployed        azuremonitor-containers-2.7.0   7.0.0-1
    ```

    *azmon-容器-release-1*代表適用于容器的 Azure 監視器的 helm 圖表版本。

2. 若要刪除圖表版本，請執行下列 helm 命令。

    `helm delete <releaseName>`

    範例：

    `helm delete azmon-containers-release-1`

    這會從叢集移除發行。 您可以執行下列`helm list`命令來確認：

    ```
    NAME                            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
    ```

可能需要幾分鐘的時間才能完成設定變更。 由於 Helm 會在您刪除發行之後追蹤，因此您可以使用`helm rollback`來審核叢集的歷程記錄，甚至將版本的取消刪除。

## <a name="next-steps"></a>後續步驟

如果 Log Analytics 工作區是為了支援監視叢集而建立的，而且不再需要，您必須手動將其刪除。 如果您不熟悉如何刪除工作區，請參閱[刪除 Azure Log Analytics 工作區](../../log-analytics/log-analytics-manage-del-workspace.md)。
