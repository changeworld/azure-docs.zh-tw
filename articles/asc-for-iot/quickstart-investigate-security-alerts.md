---
title: 快速入門：調查安全性警示
description: 在 IoT 裝置上了解、向下切入和調查「適用於 IoT 的 Azure 資訊安全中心」的安全性警示。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/30/2020
ms.author: mlottner
ms.openlocfilehash: a42b8f64c59126f08f09cd7c167e7aa133040a0e
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/11/2020
ms.locfileid: "88068395"
---
# <a name="quickstart-investigate-security-alerts"></a>快速入門：調查安全性警示

針對「適用於 IoT 的 Azure 資訊安全中心」發出的警示排定調查和補救，是確保 IoT 解決方案符合規範且安全的最佳方式。

在本快速入門中，我們將探索每個 IoT 安全性警示中可用的資訊，並說明如何向下切入並使用每個警示和相關裝置的詳細資料，以進行回應和補救。 

讓我們開始這次的教學。 


## <a name="investigate-new-security-alerts"></a>調查新的安全性警示

[IoT 中樞安全性警示] 清單會顯示您 IoT 中樞的所有彙總安全性警示。 

1. 在 Azure 入口網站中，開啟您想要調查其新警示的 **IoT 中樞**。
1. 從 [安全性] 功能表中，選取 [警示]。 IoT 中樞的所有安全性警示都會顯示，而具有**新**旗標的警示代表過去 24 小時內產生的警示。
:::image type="content" source="media/quickstart/investigate-new-security-alerts.png" alt-text="使用新的警示旗標調查新的 IoT 安全性警示":::
1. 從清單中選取並開啟任何警示，即可開啟警示詳細資料並向下切入至警示細節。 

## <a name="security-alert-details"></a>安全性警示詳細資料

開啟每個彙總警示後，即會顯示每個觸發警示的裝置及其詳細警示描述、補救步驟和裝置識別碼，以及警示嚴重性和使用 Log Analytics 進行直接調查的存取。 

1. 從 [IoT 中樞] > [安全性] > [警示] 清單中，選取並開啟任一安全性警示。 
1. 針對彙總期間發出此警示的所有裝置，檢閱警示**描述**、**嚴重性**、**偵測來源**、**裝置詳細資料**。
:::image type="content" source="media/quickstart/drill-down-iot-alert-details.png" alt-text="向下切入並檢閱彙總警示中每個裝置的詳細資料"::: 
1. 檢閱警示細節之後，請使用**手動補救步驟**指示，協助修復及/或解決造成警示的問題。 
:::image type="content" source="media/quickstart/iot-alert-manual-remediation-steps.png" alt-text="請遵循手動補救步驟來協助解決或修復您的裝置安全性警示":::

1. 如果需要進一步調查，請使用連結**來調查 Log Analytics 中的警示**。 
:::image type="content" source="media/quickstart/investigate-iot-alert-log-analytics.png" alt-text="若要進一步調查警示，請使用畫面上提供的 [使用 Log Analytics 調查] 連結":::

## <a name="next-steps"></a>後續步驟

繼續閱讀下一篇文章，以深入了解 Azure 資訊安全中心警示類型和可能的自訂內容。

> [!div class="nextstepaction"]
> [了解 IoT 安全性警示](concept-security-alerts.md)
