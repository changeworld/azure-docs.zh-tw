---
title: 定價和相關成本
description: 瞭解與 IoT Azure 安全中心關聯的成本,以及如何控制這些成本。
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
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311570"
---
# <a name="pricing-and-associated-costs"></a>定價和相關成本

本文介紹了 IoT 定價模型的 Azure 安全中心,總結了所有相關成本,並解釋了如何管理這些成本。

## <a name="pricing"></a>定價

IoT 定價的 Azure 安全中心由兩部分組成,一旦在 Azure IoT 安全中心[中啟用](quickstart-onboard-iot-hub.md)IoT 中心,將計費:

- 按設備計算的成本 - 基於 IoT 中心日誌分析的內建安全功能。

- 按消息計算的成本 - 基於來自 IoT Edge 或葉設備的安全消息增強的安全功能。

有關詳細資訊,請參閱[安全中心定價](https://azure.microsoft.com/pricing/details/security-center/)。

## <a name="associated-costs"></a>相關成本

IoT 的 Azure 安全中心具有相關成本,這些成本不是直接定價的一部分:

- 紀錄分析儲存成本

您可以通過選擇宣告某些解決方案功能來降低相關成本。 通過更改設置退出宣告。

要更改設置,

1. 打開 IoT 中心。

1. 在 **「安全」** 下,單擊 **「概述**」。。

1. 按一下 [設定]****。

下表概述了每個備選方案的相關費用和影響。

|     | 使用量 | 註解 |
| --- | --- | --- |
| **紀錄分析儲存** |  |
| 裝置建議與警報| 服務產生的安全建議和警示 | 不是選擇的 |
| 原始安全資料| 由安全代理收集的 IoT 裝置的原始安全資料 | 關閉_儲存原始裝置安全事件_ |
|

>[!Important]
> 退出退出對 Azure 安全中心具有嚴重影響,這些安全中心具有 IoT 安全功能可用性。

| 離開 | 含意 |
| --- | --- |
| _雙元元集_ | 關閉[自訂警示](quickstart-create-custom-alerts.md) |
| | 關閉 IoT 邊緣清單建議 |
| | 關閉基於裝置識別的建議和警報 |
| _儲存原始裝置安全事件_ | 有關裝置作業系統基線建議的詳細資訊不可用 |
| | 有關[警報](concept-security-alerts.md)[和建議調查](concept-recommendations.md)的詳細資訊,請提供 |
|

## <a name="see-also"></a>另請參閱

- 存取[原始安全資料](how-to-security-data-access.md)
- [調查裝置](how-to-investigate-device.md)
- 瞭解並探討[安全建議](concept-recommendations.md)
- 瞭解和探索[安全警報](concept-security-alerts.md)
