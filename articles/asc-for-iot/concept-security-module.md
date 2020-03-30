---
title: 瞭解 IoT 安全模組孿生的 Azure 安全中心*微軟文檔
description: 瞭解安全模組孿生的概念，以及如何在 Azure IoT 安全中心使用它們。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: a5c25cba-59a4-488b-abbe-c37ff9b151f9
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/24/2019
ms.author: mlottner
ms.openlocfilehash: ab3b6e740e644a1ed1495eb776045888be448047
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "68596507"
---
# <a name="security-module"></a>安全性模組


本文介紹 IoT 的 Azure 安全中心如何使用設備孿生和模組。 

## <a name="device-twins"></a>裝置對應項

對 Azure 內所建置的 IoT 解決方案來說，裝置對應項在裝置管理和程序自動化中同時扮演重要的角色。  

適用於 IoT 的 Azure 資訊安全中心可與您現有的 IoT 裝置管理平台完全整合，讓您能夠管理裝置的安全性狀態，以及利用現有的裝置控制功能。 集成是通過利用 IoT 中心孿生機制實現的。  

詳細瞭解 Azure IoT 中心中的[設備孿生概念](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins)。 

## <a name="security-module-twins"></a>安全模組雙胞胎

IoT 的 Azure 安全中心為服務中的每個設備維護一個安全模組孿生。
安全模組孿生包含解決方案中每個特定設備與設備安全性相關的所有資訊。
設備安全屬性保存在專用安全模組孿生中，以便更安全的通信，並啟用需要較少資源的更新和維護。  

請參閱[創建安全模組孿生](quickstart-create-security-twin.md)和[配置安全代理](how-to-agent-configuration.md)，瞭解如何創建、自訂和配置孿生。 [請參閱瞭解模組孿生](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins)，瞭解有關 IoT 中心中模組孿生概念的更多資訊。 
 

## <a name="see-also"></a>另請參閱
- [用於 IoT 概述的 Azure 安全中心](overview.md)
- [部署安全性代理程式](how-to-deploy-agent.md)
- [安全代理身份驗證方法](concept-security-agent-authentication-methods.md)