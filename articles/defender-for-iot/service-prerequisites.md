---
title: 元件 & 必要條件
description: 開始使用 Azure Defender for IoT 服務必要條件所需的所有專案詳細資料。
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/07/2020
ms.author: rkarlin
ms.custom: references_regions
ms.openlocfilehash: b0913dc48f807c26396a38e31d293877b4561b7d
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2020
ms.locfileid: "92089174"
---
# <a name="azure-defender-for-iot-prerequisites"></a>適用于 IoT 的 Azure Defender 必要條件

本文說明 Defender for IoT 服務的不同元件、您需要著手的部分，並說明可協助您瞭解服務的基本概念。

## <a name="minimum-requirements"></a>最低要求條件

- IoT 和 OT 裝置的無代理程式監視 (以 CyberX 技術為基礎) 
    - 透過 SPAN 埠支援流量監視的網路交換器
    - NTA 感應器的硬體設備，如需詳細資訊，請參閱 [經認證的硬體](https://aka.ms/AzureDefenderforIoTBareMetalAppliance)
    - Azure 訂用帳戶 **參與者** 角色 (只有在上架定義已認可的裝置時才需要) 
    - IoT 中樞 (適用于雲端連線管理的免費或標準層) **參與者** 角色 () 
- 透過 Azure IoT 中樞管理之受管理 IoT 裝置的安全性
    - IoT 中樞 (標準層) **參與者** 角色
    - IoT 中樞：應啟用 **適用于 IoT 的 Azure Defender** 功能切換
    - 裝置層級安全性模組支援  
        - Defender for IoT 代理程式支援不斷增長的裝置和平臺清單，請參閱 [支援的平臺清單](how-to-deploy-agent.md)


## <a name="supported-service-regions"></a>支援的服務區域

如需詳細資訊，請參閱 [IoT 中樞支援的區域](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub) 。 

適用于 IoT 的 Defender 會將所有歐洲區域的所有流量路由傳送至西歐區域資料中心，並將所有其他區域路由傳送至美國中部區域資料中心。

## <a name="next-steps"></a>後續步驟

- 閱讀 Azure IoT 安全性 [總覽](overview.md)
- 瞭解如何 [啟用服務](quickstart-onboard-iot-hub.md)
- 閱讀 [適用于 IoT 的 DEFENDER 常見問題](resources-frequently-asked-questions.md)
- 探索如何 [瞭解適用于 IoT 的 Defender 警示](concept-security-alerts.md)
