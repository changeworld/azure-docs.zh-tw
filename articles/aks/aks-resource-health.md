---
title: '檢查會影響 AKS 叢集 (Preview 的資源健康狀態事件) '
description: 使用 Azure 資源健康狀態檢查 AKS 叢集的健康情況狀態。
services: container-service
author: yunjchoi
ms.topic: troubleshooting
ms.date: 08/18/2020
ms.author: yunjchoi
ms.openlocfilehash: a409ac944c51d893fc344f82ae83d97559e055ef
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2020
ms.locfileid: "92070651"
---
# <a name="check-for-resource-health-events-impacting-your-aks-cluster-preview"></a>檢查會影響 AKS 叢集 (Preview 的資源健康狀態事件) 


當您在 AKS 上執行容器工作負載時，您想要確保您可以在問題發生時立即進行疑難排解並修正問題，以將對工作負載可用性的影響降至最低。 [Azure 資源健康狀態](../service-health/resource-health-overview.md) 可讓您查看可能導致 AKS 叢集無法使用的各種健康情況事件。

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="open-resource-health"></a>開啟資源健康狀態

### <a name="to-access-resource-health-for-your-aks-cluster"></a>若要存取 AKS 叢集的資源健康狀態：

- 在 [Azure 入口網站](https://portal.azure.com)中，流覽至您的 AKS 叢集。
- 選取左側導覽中的 **資源健康狀態** 。

### <a name="to-access-resource-health-for-all-clusters-on-your-subscription"></a>若要存取訂用帳戶上所有叢集的資源健康狀態：

- 搜尋[Azure 入口網站](https://portal.azure.com)中的服務健康狀態，然後流覽至該**服務健康狀態**。
- 選取左側導覽中的 [ **資源健康狀態** ]。
- 選取您的訂用帳戶，並將資源類型設為 Azure Kubernetes Service (AKS) 。

![螢幕擷取畫面顯示 A K S 叢集的資源健康狀態。](./media/aks-resource-health/resource-health-check.png)

## <a name="check-the-health-status"></a>檢查健康狀態

Azure 資源健康狀態可協助您診斷並取得影響 Azure 資源的服務問題支援。 資源健康狀態報告您的資源目前和過去的健康狀態，並協助您判斷問題是否由使用者起始的動作或平臺事件所造成。

資源健康狀態接收受控叢集的信號，以判斷叢集的健康情況狀態。 它會檢查 AKS 叢集的健全狀況狀態，並報告每個健康情況信號所需的動作。 這些信號的範圍包括自動解決問題、規劃的更新、未規劃的健康情況事件，以及使用者起始的動作所造成的無法使用。 這些信號會使用 Azure 資源健康狀態的健康情況狀態進行分類： [ *可用*]、[ *無法使用*]、[ *不明*] 和 [ *降級*]。

- **可用**：當沒有任何已知問題影響到叢集的健康狀態時，資源健康狀態會將您的叢集回報為 *可用*。

- **無法使用**：當平臺或非平臺事件影響叢集的健康狀態時，資源健康狀態會將您的叢集回報為 *無法使用*。

- **未知**：當叢集的健康情況計量發生暫時性連線中斷時，資源健康狀態會將您的叢集回報為 *未知*。

- **降級**：當有健康情況問題需要您的動作時，資源健康狀態會將您的叢集回報為 *降級*。

如需每個健全狀況狀態的詳細資訊，請造訪 [資源健康狀態總覽](../service-health/resource-health-overview.md#health-status)。

### <a name="view-historical-data"></a>查看歷程記錄資料

您也可以在 [健康情況歷程 **記錄** ] 區段中，查看過去30天的歷程記錄資源健康狀態資訊。

## <a name="next-steps"></a>後續步驟

在您的叢集上執行檢查，以使用 [AKS 診斷](./concepts-diagnostics.md)進一步針對叢集問題進行疑難排解。