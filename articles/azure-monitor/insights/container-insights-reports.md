---
title: 容器 Azure 監視器中的報表
description: 描述可用來分析容器 Azure 監視器所收集之資料的報表。
ms.topic: conceptual
ms.date: 12/07/2020
ms.openlocfilehash: 3cc2f8fb9bfaa278ce06b4a8cd6d379397b7129a
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/09/2020
ms.locfileid: "96907488"
---
# <a name="reports-in-azure-monitor-for-containers"></a>容器 Azure 監視器中的報表
容器 Azure 監視器中的報表是建議的現成 [Azure 活頁簿](../platform/workbooks-overview.md)。 本文說明可用的不同報表，以及如何存取這些報表。

## <a name="viewing-reports"></a>檢視報表
從 Azure 入口網站的 [ **Azure 監視器** ] 功能表中，選取 [ **容器**]。 在 [**監視**] 區段中選取 [**深入** 解析]，選擇特定的叢集，然後選取 [**報表] (預覽)** ] 頁面。 

[![報表頁面](media/container-insights-reports/reports-page.png)](media/container-insights-reports/reports-page.png#lightbox)

## <a name="create-a-custom-workbook"></a>建立自訂活頁簿
若要根據這些活頁簿來建立自訂活頁簿，請選取 [ **View 活頁簿** ] 下拉式清單，然後移至下拉式清單底部的 [ **AKS 圖庫** ]。 如需活頁簿和使用活頁簿範本的詳細資訊，請參閱 [Azure 監視器](../platform/workbooks-overview.md) 的活頁簿。

[![AKS 資源庫](media/container-insights-reports/aks-gallery.png)](media/container-insights-reports/aks-gallery.png#lightbox)

## <a name="node-workbooks"></a>節點活頁簿

- **磁片容量**：每個磁片的互動式磁片使用量圖表，以下列觀點呈現至容器內的節點：

    - 所有磁片的磁片使用量百分比。
    - 所有磁片的可用磁碟空間。
    - 顯示每個節點的磁片、其已使用空間百分比、已使用空間百分比、可用磁碟空間 (GiB) 的方格，以及可用磁碟空間 (GiB) 的趨勢。 在資料表中選取資料列時，已使用的空間百分比和可用磁碟空間 (GiB) 會顯示在資料列下方。

- **磁片 IO**：每個磁片的互動式磁片使用量圖表，以下列觀點呈現給容器內的節點：

    - 磁片 i/o 會依讀取位元組/秒、寫入位元組/秒，以及讀取和寫入位元組/秒的趨勢，在所有磁片上摘要。
    - 八個效能圖表會顯示關鍵效能指標，以協助測量和識別磁片 i/o 瓶頸。

- **Gpu**：適用于每個 Gpu 感知 Kubernetes 叢集節點的互動式 GPU 使用量圖表。

## <a name="resource-monitoring-workbooks"></a>資源監視活頁簿

- **部署**：部署的狀態 & 水準 Pod 自動調整程式 (HPA) 包括自訂 HPA。 
  
- **工作負載詳細資料**：互動式圖表顯示命名空間工作負載的效能統計資料。 包含多個索引標籤：

  - 依 POD 的 CPU 和記憶體使用量總覽。
  - POD/容器狀態顯示 POD 重新開機趨勢、容器重新開機趨勢，以及 POD 的容器狀態。
  - Kubernetes 顯示控制器事件摘要的事件。

- **Kubelet**：包含兩個顯示主要節點操作統計資料的方格：

    - 依節點方格的總覽摘要列出每個節點的總作業數、總錯誤數，以及成功的作業（依百分比和趨勢）。
    - 依作業類型的總覽摘要說明作業總數、錯誤總數，以及依百分比和趨勢的成功作業。
## <a name="billing-workbooks"></a>帳單活頁簿

- **資料使用量**：協助您以視覺化方式呈現資料的來源，而不需要建立您自己的查詢程式庫，使其在檔中共用。 在此活頁簿中，您可以使用圖表來查看來自這類透視圖的可計費資料，如下所示：

  - 依解決方案的計費資料內嵌（GB）總計
  - 容器記錄所內嵌的可計費資料 (應用程式記錄檔) 
  - 依 Kubernetes 命名空間的可計費容器記錄資料內嵌
  - 依叢集名稱隔離的可計費容器記錄資料內嵌
  - 依 logsource 專案內嵌的可計費容器記錄資料
  - 診斷主要節點記錄所內嵌的可計費診斷資料

## <a name="networking-workbooks"></a>網路活頁簿

- **NPM** 設定：監視透過網路原則管理員設定的網路設定 (NPM) 。

  - 有關整體設定複雜性的摘要資訊。
  - 原則、規則和一段時間的設定計數，可讓您深入瞭解三個之間的關聯性，以及新增時間維度來進行設定的偵錯工具。
  - 所有 IPSets 和每個 IPSet 中的專案數。
  - 將元件新增至網路設定時，每個節點的最差和平均案例效能。

- **網路**：每個節點的網路介面卡的互動式網路使用圖表，且方格會顯示關鍵效能指標，以協助測量網路介面卡的效能。



## <a name="next-steps"></a>後續步驟

- 如需 Azure 監視器中活頁簿的詳細資訊，請參閱 Azure 監視器的活頁 [簿](../platform/workbooks-overview.md) 。
