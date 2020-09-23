---
title: 定價和相關成本
description: 瞭解與 Defender for IoT 相關聯的成本，以及如何控制這些成本。
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/04/2020
ms.author: mlottner
ms.openlocfilehash: 24ae6c4014948639aa737a0d2d88ec15f98a7cb4
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90934486"
---
# <a name="pricing-and-associated-costs"></a>定價和相關成本

本文說明適用于 IoT 的 Defender 計價模式，摘要說明所有相關成本，並說明如何管理它們。

## <a name="pricing"></a>定價

適用于 iot 的 Defender 定價模型是由兩個部分所組成，而且在 Defender for IoT 中 [啟用](quickstart-onboard-iot-hub.md) iot 中樞之後計費：

- 依裝置內建的安全性功能，以 IoT 中樞記錄分析為基礎的成本。

- 依訊息增強的安全性功能，根據 IoT Edge 或分葉裝置的安全性訊息來收費。

如需詳細資訊，請參閱資訊 [安全中心定價](https://azure.microsoft.com/pricing/details/security-center/)。

## <a name="associated-costs"></a>相關成本

適用于 IoT 的 Defender 有相關聯的成本，不是直接定價的一部分：

- Log Analytics 儲存體成本

您可以退出宣告特定解決方案功能，以降低相關成本。 變更您的設定，退出宣告。

若要變更您的設定：

1. 開啟 IoT 中樞。

1. 在 [ **安全性**] 底下，按一下 [ **設定**]。

1. 按一下 [ **資料收集**]。

下表提供每個選項相關成本和含意的摘要。

| 選項 | 使用方式 | 註解 |
| --- | --- | --- |
| **Log Analytics 儲存體** |  |
| 裝置建議和警示| 服務所產生的安全性建議和警示 | 非選擇性 |
| 原始安全性資料| 來自 IoT 裝置的原始安全性資料，由安全性代理程式收集 | 停用 _存放區原始裝置安全性事件_ |
|

>[!Important]
> 退出宣告對 Defender 的 IoT 安全性功能可用性有嚴重的影響。

| 退出 | 含意 |
| --- | --- |
| _對應項元資料集合_ | 停用 [自訂警示](quickstart-create-custom-alerts.md) |
| | 停用 IoT Edge 資訊清單建議 |
| | 停用裝置身分識別型建議和警示 |
| _儲存原始裝置安全性事件_ | 裝置作業系統基準建議的詳細資料無法使用 |
| | [警示](concept-security-alerts.md)和[建議](concept-recommendations.md)調查的詳細資料無法使用 |
|

## <a name="see-also"></a>另請參閱

- 存取您的 [原始安全性資料](how-to-security-data-access.md)
- [調查裝置](how-to-investigate-device.md)
- 瞭解及探索 [安全性建議](concept-recommendations.md)
- 瞭解及探索 [安全性警示](concept-security-alerts.md)
