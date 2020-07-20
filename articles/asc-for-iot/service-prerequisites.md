---
title: 元件 & 必要條件
description: 開始使用 IoT 服務必要條件 Azure 資訊安全中心所需的所有專案的詳細資料。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 790cbcb7-1340-4cc1-9509-7b262e7c3181
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2019
ms.author: mlottner
ms.custom: references_regions
ms.openlocfilehash: 19e4ea37aa10c90d15a2b7dcdf962c131c8e473d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84193220"
---
# <a name="azure-security-center-for-iot-prerequisites"></a>適用于 IoT 必要條件的 Azure 資訊安全中心

本文說明 IoT 服務的 Azure 資訊安全中心的不同元件、您需要開始的事項，並說明可協助您瞭解服務的基本概念。

## <a name="minimum-requirements"></a>最低要求條件

- IoT 中樞標準層
  - RBAC 角色**擁有**者層級許可權
- [Log Analytics 工作區](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)
- Azure 資訊安全中心（建議）
  - Azure 資訊安全中心的使用是建議事項，而不是必要條件。 若沒有 Azure 資訊安全中心，您將無法在 IoT 中樞內查看其他 Azure 資源。

## <a name="working-with-azure-security-center-for-iot-service"></a>使用適用于 IoT 服務的 Azure 資訊安全中心

您可以使用 Azure IoT 中樞和 Azure 資訊安全中心取得 IoT 深入解析和報告的 Azure 資訊安全中心。 若要在您的 Azure IoT 中樞上啟用 IoT 的 Azure 資訊安全中心，則需要具有**擁有**者層級許可權的帳戶。 在您的 IoT 中樞中啟用適用于 IoT 的 ASC 之後，IoT 深入解析的 Azure 資訊安全中心會顯示為 Azure IoT 中樞中的**安全性**功能，以及 Azure 資訊安全中心中的**iot** 。

## <a name="supported-service-regions"></a>支援的服務區域

下列 Azure 區域中的 IoT 中樞目前支援 IoT 的 Azure 資訊安全中心：

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

適用于 IoT 的 Azure 資訊安全中心會將所有歐洲地區的所有流量路由傳送至西歐地區資料中心，並將所有其他區域路由至美國中部區域資料中心。

## <a name="wheres-my-iot-hub"></a>我的 IoT 中樞在哪裡？

開始之前，請先檢查您的 IoT 中樞位置以確認服務可用性。

1. 開啟 IoT 中樞。
1. 按一下 [概觀]  。
1. 確認列出的位置符合其中一個[支援的服務區域](#supported-service-regions)。

## <a name="supported-platforms-for-agents"></a>代理程式支援的平臺

IoT 代理程式的 Azure 資訊安全中心支援不斷增長的裝置和平臺清單。 請參閱[支援的平臺清單](how-to-deploy-agent.md)，以檢查您現有或已規劃的裝置程式庫。

## <a name="next-steps"></a>後續步驟

- 閱讀 Azure IoT 安全性[總覽](overview.md)
- 瞭解如何[啟用服務](quickstart-onboard-iot-hub.md)
- 閱讀[適用于 IoT 的 AZURE 資訊安全中心常見問題](resources-frequently-asked-questions.md)
- 探索如何[瞭解 IoT 警示的 Azure 資訊安全中心](concept-security-alerts.md)
