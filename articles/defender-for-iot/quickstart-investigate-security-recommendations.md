---
title: 快速入門：調查安全性建議
description: 使用適用於 IoT 安全性服務的 Defender 調查安全性建議。
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: 66b5694823096f0747ea099b631a53198a819816
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/31/2020
ms.locfileid: "97835035"
---
# <a name="quickstart-investigate-security-recommendations"></a>快速入門：調查安全性建議


適用於 IoT 的 Defender 提供的建議及時分析和緩和措施，是改善安全性狀態並減少 IoT 解決方案受攻擊面的最佳方式。

在本快速入門中，我們將探索每個 IoT 安全性建議中可用的資訊，並說明如何向下切入並使用每個建議和相關裝置的詳細資料，以降低風險。

讓我們開始這次的教學。

## <a name="investigate-new-recommendations"></a>調查新建議

IoT 中樞建議清單會顯示您 IoT 中樞的所有彙總安全性建議。

1.  在 Azure 入口網站中，開啟想要調查其新建議的 **IoT 中樞**。

1.  從 **安全性** 功能表中，選取 [建議]。 IoT 中樞的所有安全性建議都會顯示，而包含 **新** 旗標的建議會標示您過去 24 小時的建議。 

    :::image type="content" source="media/quickstart/investigate-security-recommendations-expanded.png#lightbox" alt-text="使用適用於 IoT 的 ASC 調查安全性建議](media/quickstart/investigate-security-recommendations-inline.png)":::


1.  從清單中選取並開啟任何建議，即可開啟建議詳細資料並向下切入至細節。

## <a name="security-recommendation-details"></a>安全性建議詳細資料

開啟每個彙總建議，以顯示每個觸發建議之裝置的詳細建議描述、補救步驟、裝置識別碼。 如此也會顯示使用 Log Analytics 的建議嚴重性和直接調查存取。

1.  從 **IoT 中樞**\>**安全性** \> **建議** 清單中，選取並開啟任何安全性建議。

1.  針對彙總期間發出此建議的所有裝置，檢閱建議 **描述**、**嚴重性**、**裝置詳細資料** 裝置詳細資料。 

1.  檢閱建議細節之後，請使用 **手動補救步驟** 指示，協助修復並解決造成建議的問題。 

    [ :::image type="content" source="media/quickstart/remediate-security-recommendations-expanded.png#lightbox" alt-text="使用適用於 IoT 的 ASC 的補救安全性建議](media/quickstart/remediate-security-recommendations-inline.png)":::


1.  在向下切入頁面中選取所需的裝置，以探索特定裝置的建議詳細資料。

    [:::image type="content" source="media/quickstart/explore-security-recommendation-detail-expanded.png#lightbox" alt-text="使用適用於 IoT 的 ASC 調查特定的安全性建議](media/quickstart/explore-security-recommendation-detail-inline.png)":::


1.  如果需要進一步調查，請使用 **調查 Log Analytics 中的建議** 連結。 


## <a name="next-steps"></a>後續步驟

請前往下一篇文章，以了解如何建立自訂警示...

> [!div class="nextstepaction"]
> [建立自訂警示](quickstart-create-custom-alerts.md)
