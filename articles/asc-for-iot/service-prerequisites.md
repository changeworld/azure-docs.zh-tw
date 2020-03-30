---
title: 適用于 IoT 先決條件的 Azure 安全中心*微軟文檔
description: 使用 Azure 安全中心開始使用 IoT 服務先決條件所需的一切詳細資訊。
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
ms.openlocfilehash: 4440fec98d1f561da6375bcaadba4282076cc53b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "71299470"
---
# <a name="azure-security-center-for-iot-prerequisites"></a>適用于 IoT 先決條件的 Azure 安全中心

本文介紹了 Azure IoT 服務安全中心的不同構建基塊、需要開始的內容，並解釋了説明瞭解服務的基本概念。 

## <a name="minimum-requirements"></a>最低需求

- IoT 中心標準層
    - RBAC 角色**擁有者**級別許可權 
- [Log Analytics 工作區](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) 
- Azure 安全中心（推薦）
    - 使用 Azure 安全中心是一項建議，而不是一項要求。 如果沒有 Azure 安全中心，您將無法查看 IoT 中心中的其他 Azure 資源。 
 
## <a name="working-with-azure-security-center-for-iot-service"></a>使用 Azure IoT 服務安全中心

Azure 安全中心可用於 IoT 見解和報告，可通過 Azure IoT 中心和 Azure 安全中心提供。 要在 Azure IoT 中心啟用 IoT 的 Azure 安全中心，需要具有**擁有者**級別許可權的帳戶。 在 IoT 中心中為 IoT 啟用 ASC 後，用於 IoT 見解的 Azure 安全中心將顯示為 Azure IoT 中心**中的安全**功能和 Azure 安全中心的**IoT。** 

## <a name="supported-service-regions"></a>支援的服務區域 

IoT 的 Azure 安全中心當前支援以下 Azure 區域中的 IoT 中心：
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

IoT Azure 安全中心將所有流量從所有歐洲區域路由到西歐區域資料中心和所有剩餘區域到美國中部區域資料中心。  
  
## <a name="wheres-my-iot-hub"></a>我的 IoT 中心在哪裡？

在開始之前，請檢查 IoT 中心位置以驗證服務可用性。 

1. 開啟 IoT 中樞。 
2. 按一下 [概觀]****。 
3. 驗證列出的位置與[受支援的服務區域](#supported-service-regions)之一匹配。 


## <a name="supported-platforms-for-agents"></a>支援的代理平臺 

IoT 代理的 Azure 安全中心支援越來越多的設備和平臺清單。 請參閱[支援的平臺清單](how-to-deploy-agent.md)以檢查現有或計畫的設備庫。  

## <a name="next-steps"></a>後續步驟
- 閱讀 Azure IoT 安全[概述](overview.md)
- 瞭解如何[啟用服務](quickstart-onboard-iot-hub.md)
- 閱讀[Azure 安全中心瞭解 IoT 常見問題解答](resources-frequently-asked-questions.md)
- 瞭解如何瞭解[IoT 警報的 Azure 安全中心](concept-security-alerts.md)
