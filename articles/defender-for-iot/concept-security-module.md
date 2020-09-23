---
title: 安全性模組和裝置 twins
description: 瞭解安全性模組 twins 的概念，以及如何在適用于 IoT 的 Defender 中使用它們。
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
ms.date: 07/24/2019
ms.author: mlottner
ms.openlocfilehash: a5382313c837482f116f498f3a05c36447062b0a
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90936879"
---
# <a name="security-module"></a>安全性模組

本文說明 Defender for IoT 如何使用裝置 twins 和模組。

## <a name="device-twins"></a>裝置對應項

對 Azure 內所建置的 IoT 解決方案來說，裝置對應項在裝置管理和程序自動化中同時扮演重要的角色。

Defender for IoT 可與您現有的 IoT 裝置管理平臺完全整合，讓您能夠管理裝置的安全性狀態，以及利用現有的裝置控制功能。 藉由使用 IoT 中樞對應項機制來達成整合。

深入瞭解 Azure IoT 中樞中 [裝置 twins](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins) 的概念。

## <a name="security-module-twins"></a>安全性模組 twins

Defender for IoT 會為服務中的每個裝置維護安全性模組對應項。
安全性模組對應項會針對您解決方案中的每個特定裝置，保存與裝置安全性相關的所有資訊。
裝置安全性屬性是在專用的安全性模組對應項中維護，以便進行更安全的通訊，以及啟用需要較少資源的更新與維護。

請參閱 [建立安全性模組](quickstart-create-security-twin.md) 對應項及 [設定安全性代理](how-to-agent-configuration.md) 程式，以瞭解如何建立、自訂和設定對應項。 若要深入瞭解 IoT 中樞內模組 twins 的概念，請參閱 [瞭解模組 twins](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins) 。

## <a name="see-also"></a>另請參閱

- [Defender for IoT 總覽](overview.md)
- [部署安全性代理程式](how-to-deploy-agent.md)
- [安全性代理程式驗證方法](concept-security-agent-authentication-methods.md)
