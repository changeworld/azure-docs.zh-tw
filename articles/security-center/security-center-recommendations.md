---
title: Azure 資訊安全中心的安全性建議
description: 本文件將逐步引導您了解「Azure 資訊安全中心」的建議如何協助您保護 Azure 資源及遵守安全性原則。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 86c50c9f-eb6b-4d97-acb3-6d599c06133e
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/29/2019
ms.author: memildin
ms.openlocfilehash: 6363100c844d071a3bb47521cec6ff7e988f6af8
ms.sourcegitcommit: 64ad2c8effa70506591b88abaa8836d64621e166
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/17/2020
ms.locfileid: "88263198"
---
# <a name="security-recommendations-in-azure-security-center"></a>Azure 資訊安全中心的安全性建議 
本主題說明如何查看並瞭解 Azure 資訊安全中心中的建議，以協助保護您的 Azure 資源。

> [!NOTE]
> 本文件將使用範例部署來介紹服務。  本文件不是一份逐步解說指南。
>

## <a name="what-are-security-recommendations"></a>有哪些安全性建議？

建議是讓您採取的動作，以便保護您的資源。

安全中心會定期分析 Azure 資源的安全性狀態，以找出潛在的安全性弱點。 接著，它會提供有關如何補救這些弱點的建議。

每項建議都會提供：

- 問題的簡短描述。
- 執行建議所需執行的補救步驟。
- 受影響的資源。

## <a name="monitor-recommendations"></a>監視建議 <a name="monitor-recommendations"></a>

安全性中心會分析您資源的安全性狀態，以找出潛在的弱點。 [ **建議** ] 圖格底下的 [建議] 磚 **會顯示安全性** 中心所識別的建議總數。

![安全性中心總覽](./media/security-center-recommendations/asc-overview.png)

1. 選取 [**總覽**] 下的 [**建議] 磚**。 **建議**清單隨即開啟。

1. 建議會分組為安全性控制項。

      ![依安全性控制分組的建議](./media/security-center-recommendations/view-recommendations.png)

1. 展開控制項，然後選取特定建議以查看建議頁面。

    :::image type="content" source="./media/security-center-recommendations/recommendation-details-page.png" alt-text="建議詳細資料頁面。" lightbox="./media/security-center-recommendations/recommendation-details-page.png":::

    此頁面包含：

    - **嚴重性指標**
    - 相關) 的有效**時間間隔** ( 
    - **描述** -問題的簡短描述
    - **補救步驟** -在受影響的資源上補救安全性問題時所需的手動步驟的說明。 如需「快速修正」的建議，您可以選取 [ **View 補救邏輯** ]，然後再將建議的修正套用至您的資源。 
    - **受影響的資源** -您的資源會分組到索引標籤中：
        - **狀況良好的資源** -可能不會影響或已補救問題的相關資源。
        - **狀況不良的資源** –仍受識別問題影響的資源。
        - **不適用的資源** –建議無法提供明確答案的資源。 [不適用] 索引標籤也包含每個資源的原因。 

            :::image type="content" source="./media/security-center-recommendations/recommendations-not-applicable-reasons.png" alt-text="不適用資源，原因如下。":::


 
## <a name="next-steps"></a>後續步驟

在本文件中，已向您介紹「資訊安全中心」的安全性建議。 若要瞭解如何補救建議：

* [補救建議](security-center-remediate-recommendations.md) -瞭解如何為您的 Azure 訂用帳戶和資源群組設定安全性原則。
