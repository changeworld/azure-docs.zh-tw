---
title: 了解資源的健康情況
titleSuffix: Azure Digital Twins
description: 瞭解如何使用 Azure 資源健康狀態來檢查 Azure 數位 Twins 實例的健康情況。
author: baanders
ms.author: baanders
ms.date: 10/6/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: b64ee4d1538276bbbcab3fe09054f399ad20c162
ms.sourcegitcommit: 9706bee6962f673f14c2dc9366fde59012549649
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/13/2020
ms.locfileid: "94616544"
---
# <a name="troubleshooting-azure-digital-twins-resource-health"></a>針對 Azure 數位 Twins 進行疑難排解：資源健康狀態

[Azure 資源健康狀態](../service-health/resource-health-overview.md) 可協助您診斷並取得影響 Azure 資源的服務問題支援。 它會報告您的資源目前和過去的健康情況。

本文說明如何取得 Azure 數位 Twins 實例的 **資源健康狀態** 資訊。

## <a name="use-azure-resource-health"></a>使用 Azure 資源健康狀態

Azure 資源健康狀態可協助您監視 Azure 數位 Twins 實例是否已啟動且正在執行。 您也可以使用它來瞭解區域性中斷是否會影響實例的健康情況。

若要檢查實例的健全狀況，請遵循下列步驟：

1. 登入 [Azure 入口網站](https://portal.azure.com) ，然後流覽至您的 Azure 數位 Twins 實例。 您可以在入口網站的搜尋列中輸入其名稱來尋找它。 

2. 從實例的功能表中，選取 [ *支援 + 疑難排解* ] 下的 [ _**資源健康狀態**_ ]。 這會將您帶到頁面來查看資源健康狀態歷程記錄。 

    :::image type="content" source="media/troubleshoot-resource-health/resource-health.png" alt-text="顯示 [資源健康狀態] 頁面的螢幕擷取畫面。有一個 [健康情況歷程記錄] 區段顯示過去九天的每日報告。每天都會顯示「可用」的狀態。":::

在上圖中，此實例顯示為 *可用* ，且已在過去九天內。 若要深入瞭解 *可用* 狀態以及可能出現的其他狀態類型，請參閱 [*Azure 資源健康狀態總覽*](../service-health/resource-health-overview.md)。

您也可以深入瞭解在 [*azure 資源健康狀態中的資源類型和健康情況檢查*](../service-health/resource-health-checks-resource-types.md)中，針對不同類型的 azure 資源進入資源健康狀態的不同檢查。

## <a name="next-steps"></a>後續步驟

請參閱下列文章，以瞭解監視 Azure 數位 Twins 實例的其他方式：
* [*疑難排解：使用 Azure 監視器來查看度量*](troubleshoot-metrics.md)
* [*疑難排解：設定診斷*](troubleshoot-diagnostics.md)。
* [*疑難排解：設定警示*](troubleshoot-alerts.md)
