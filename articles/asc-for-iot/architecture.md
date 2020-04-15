---
title: 方案架構
description: 瞭解 IoT 服務的 Azure 安全中心中的資訊流。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 4cc7d1982555f058a4ea23f7d8a6fdc2d83e484d
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311758"
---
# <a name="azure-security-center-for-iot-architecture"></a>適用於 IoT 的 Azure 資訊安全中心架構

本文介紹了適用於 IoT 解決方案的 Azure 安全中心的功能系統體系結構。

## <a name="azure-security-center-for-iot-components"></a>IoT 元件的 Azure 安全中心

IoT 的 Azure 安全中心由以下元件組成:

- IoT 中心整合
- 裝置代理 (選擇性的)
- 傳送安全訊息 SDK
- 分析導管

### <a name="azure-security-center-for-iot-workflows"></a>IoT 工作流的 Azure 安全中心

IoT 的 Azure 安全中心在兩個功能工作流之一中工作:內置和增強

### <a name="built-in"></a>內建

在**內建**模式下,當您選擇打開 IoT 中心**中的安全**選項時,將啟用 IoT 的 Azure 安全中心。 內建模式提供即時監控、建議和警報,提供單步設備可見性和無與倫比的安全性。 內建模式不需要在任何設備上安裝代理,並且對記錄的活動使用高級分析來分析和保護您的現場設備。

### <a name="enhanced"></a>增強

在**增強**模式下,在打開 IoT 中心**中的「安全**」選項並在裝置上安裝 IoT 裝置代理 Azure 安全中心後,代理會從裝置收集、聚合和分析原始安全事件。 原始安全事件可以包括IP連接、程序創建、使用者登錄和其他安全相關資訊。 IoT 設備代理的 Azure 安全中心還處理事件聚合,以説明避免高網路輸送量。 代理高度可自定義,允許您將其用於特定任務,例如僅以最快的 SLA 發送重要資訊,或將大量安全資訊和上下文聚合到更大的市場區隔中,從而避免更高的服務成本。

![適用於 IoT 的 Azure 資訊安全中心架構](./media/architecture/azure-iot-security-architecture.png)

設備代理和其他應用程式使用 Azure**發送安全訊息 SDK**將安全資訊發送到 Azure IoT 中心。 IoT 中心拾取此資訊並將其轉發到 Azure IoT 服務安全中心。

啟用 IoT 服務的 Azure 安全中心後,除了轉發的數據外,IoT 中心還會發送其所有內部數據,供 Azure IoT 安全中心進行分析。 此數據包括設備雲操作日誌、設備標識和集線器配置。 所有這些資訊都有助於創建適用於 IoT 分析管道的 Azure 安全中心。

用於 IoT 分析導管的 Azure 安全中心還接收來自 Microsoft 和 Microsoft 合作夥伴內部各種來源的其他威脅情報流。 適用於 IoT 整個分析導管的 Azure 安全中心適用於服務上所做的每個客戶配置(例如自訂警報和使用發送安全訊息 SDK)。

使用分析管道,IoT Azure 安全中心將所有資訊流合併,以生成可操作的建議和警報。 管道包含由安全研究人員和專家創建的自定義規則,以及機器學習模型,用於查找偏離標準設備行為和風險分析。

用於 IoT 建議和警報的 Azure 安全中心(分析管道輸出)將寫入每個客戶的日誌分析工作區。 包括工作區中的原始事件以及警報和建議,可以使用檢測到的可疑活動的確切詳細資訊進行深入的潛水調查和查詢。

## <a name="next-steps"></a>後續步驟

在本文中,您瞭解了適用於 IoT 解決方案的 Azure 安全中心的基本體系結構和工作流。 要瞭解有關先決條件以及如何在 IoT 中心入門和啟用安全解決方案的更多資訊,請參閱以下文章:

- [服務必要條件](service-prerequisites.md)
- [開始](getting-started.md)
- [設定您的解決方案](quickstart-configure-your-solution.md)
- [在 IoT 中啟用安全性](quickstart-onboard-iot-hub.md)
- [適用於 IoT 常見問題解答的 Azure 安全中心](resources-frequently-asked-questions.md)
- [適用於 IoT 的 Azure 資訊安全中心的安全性警示](concept-security-alerts.md)
