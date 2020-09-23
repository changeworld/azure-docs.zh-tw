---
title: 元件 & 必要條件
description: 開始使用 Azure Defender for IoT 服務必要條件所需的所有專案詳細資料。
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
ms.date: 09/09/2020
ms.author: mlottner
ms.custom: references_regions
ms.openlocfilehash: 19263f8db58c8d20288d3ae74c24efd85667bc33
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90933988"
---
# <a name="azure-defender-for-iot-prerequisites"></a>適用于 IoT 的 Azure Defender 必要條件

本文說明 Defender for IoT 服務的不同元件、您需要著手的部分，並說明可協助您瞭解服務的基本概念。

## <a name="minimum-requirements"></a>最低需求

- IoT 中樞標準層
  - Azure 角色 **擁有** 者層級許可權
- [Log Analytics 工作區](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)
- Azure 資訊安全中心 (建議的) 
  - 使用 Azure 資訊安全中心是建議，而不是需求。 若沒有 Azure 資訊安全中心，您將無法在 IoT 中樞內查看其他 Azure 資源。

## <a name="working-with-defender-for-iot-service"></a>使用 Defender for IoT 服務

適用于 IoT 見解和報告的 Defender 可使用 Azure IoT 中樞和 Azure 資訊安全中心取得。 若要在您的 Azure IoT 中樞上啟用 Defender for IoT，則需要具有 **擁有** 者層級許可權的帳戶。 在 IoT 中樞中啟用適用于 IoT 的 ASC 之後，適用于 IoT insights 的 Defender 會顯示為 Azure IoT 中樞中的 **安全性** 功能，以及 Azure 資訊安全中心中的  **iot** 。

## <a name="supported-service-regions"></a>支援的服務區域

Iot 中樞目前支援適用于 IoT 的 Defender 適用于下列 Azure 區域：

- 美國中部
- 美國東部
- 美國東部 2
- 美國中西部
- 美國西部
- 美國西部 2
- 美國中南部
- 美國中北部
- 加拿大中部
- 加拿大東部
- 北歐
- 巴西南部
- 法國中部
- 英國西部
- 英國南部
- 西歐
- 北歐
- 日本西部
- 日本東部
- 澳大利亞東南部
- 澳大利亞東部
- 東亞
- 東南亞
- 南韓中部
- 南韓南部
- 印度中部
- 印度南部

適用于 IoT 的 Defender 會將所有歐洲區域的所有流量路由傳送至西歐區域資料中心，並將所有其他區域路由傳送至美國中部區域資料中心。

## <a name="wheres-my-iot-hub"></a>我的 IoT 中樞在哪裡？

在開始之前，請先檢查您的 IoT 中樞位置以確認服務可用性。

1. 開啟 IoT 中樞。
1. 按一下 [概觀]  。
1. 確認列出的位置符合其中一個 [支援的服務區域](#supported-service-regions)。

## <a name="supported-platforms-for-agents"></a>代理程式支援的平臺

適用于 IoT 的 Defender 代理程式支援不斷增長的裝置與平臺清單。 請參閱 [支援的平臺清單](how-to-deploy-agent.md) ，以檢查您現有或已規劃的裝置程式庫。

## <a name="next-steps"></a>下一步

- 閱讀 Azure IoT 安全性 [總覽](overview.md)
- 瞭解如何 [啟用服務](quickstart-onboard-iot-hub.md)
- 閱讀 [適用于 IoT 的 DEFENDER 常見問題](resources-frequently-asked-questions.md)
- 探索如何 [瞭解適用于 IoT 的 Defender 警示](concept-security-alerts.md)
