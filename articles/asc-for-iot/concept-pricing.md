---
title: 定價和相關成本
description: 瞭解與 IoT Azure 資訊安全中心相關聯的成本，以及如何控制它們。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: ef839708-4574-4a40-bc45-07005f8e9daf
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/27/2019
ms.author: mlottner
ms.openlocfilehash: fe117cf8d05ba3392b71858acf94d1fc88c1a527
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81311570"
---
# <a name="pricing-and-associated-costs"></a>定價和相關成本

本文說明 IoT 定價模式的 Azure 資訊安全中心、摘要所有相關成本，並說明如何管理它們。

## <a name="pricing"></a>定價

IoT 計價模式的 Azure 資訊安全中心由兩個部分組成，一旦在 IoT 的 Azure 資訊安全中心中[啟用](quickstart-onboard-iot-hub.md)IoT 中樞，就會計費：

- 根據 IoT 中樞記錄分析的裝置內建安全性功能所產生的成本。

- 根據來自 IoT Edge 或分葉裝置的安全性訊息，以訊息增強的安全性功能來計算成本。

如需詳細資訊，請參閱[資訊安全中心定價](https://azure.microsoft.com/pricing/details/security-center/)。

## <a name="associated-costs"></a>相關成本

IoT 的 Azure 資訊安全中心有相關聯的成本，而這不是直接定價的一部分：

- Log Analytics 儲存體成本

您可以退出宣告某些解決方案功能，以降低相關成本。 藉由變更您的設定來退出宣告。

若要變更您的設定：

1. 開啟 IoT 中樞。

1. 在 [**安全性**] 底下，按一下 **[總覽**]。

1. 按一下 [設定]  。

下表提供每個選項的相關費用和含意的摘要。

|     | 使用方式 | 註解 |
| --- | --- | --- |
| **Log Analytics 儲存體** |  |
| 裝置建議和警示| 服務產生的安全性建議和警示 | 非選擇性 |
| 原始的安全性資料| 來自 IoT 裝置的原始安全性資料，由安全性代理程式收集 | 停用_儲存原始裝置安全性事件_ |
|

>[!Important]
> 退出宣告對 IoT 安全性功能可用性 Azure 資訊安全中心有嚴重的影響。

| 退出宣告 | 含意 |
| --- | --- |
| _對應項元資料集合_ | 停用[自訂警示](quickstart-create-custom-alerts.md) |
| | 停用 IoT Edge 資訊清單建議 |
| | 停用裝置身分識別型建議和警示 |
| _儲存原始裝置安全性事件_ | 裝置作業系統基準建議的詳細資料無法使用 |
| | [警示](concept-security-alerts.md)和[建議](concept-recommendations.md)調查的詳細資料無法使用 |
|

## <a name="see-also"></a>請參閱

- 存取未經處理的[安全性資料](how-to-security-data-access.md)
- [調查裝置](how-to-investigate-device.md)
- 瞭解及探索[安全性建議](concept-recommendations.md)
- 瞭解及探索[安全性警示](concept-security-alerts.md)
