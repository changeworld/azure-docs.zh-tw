---
title: 安全性模組和裝置 twins
description: 瞭解安全性模組 twins 的概念，以及如何在 IoT 的 Azure 資訊安全中心中使用它們。
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
ms.openlocfilehash: d598d291612c6e4f58caf77e1b213b2bc3f42820
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81311453"
---
# <a name="security-module"></a>安全性模組

本文說明 IoT Azure 資訊安全中心如何使用裝置 twins 和模組。

## <a name="device-twins"></a>裝置對應項

對 Azure 內所建置的 IoT 解決方案來說，裝置對應項在裝置管理和程序自動化中同時扮演重要的角色。

適用於 IoT 的 Azure 資訊安全中心可與您現有的 IoT 裝置管理平台完全整合，讓您能夠管理裝置的安全性狀態，以及利用現有的裝置控制功能。 藉由使用 IoT 中樞對應項機制來達成整合。

在 Azure IoT 中樞中深入瞭解[裝置 twins](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins)的概念。

## <a name="security-module-twins"></a>安全性模組 twins

適用于 IoT 的 Azure 資訊安全中心會為服務中的每個裝置維護一個安全性模組對應項。
安全性模組對應項會保留您解決方案中每個特定裝置的裝置安全性相關資訊。
裝置安全性屬性會保留在專用的安全性模組對應項中，以進行更安全的通訊，以及啟用需要較少資源的更新和維護。

請參閱[建立安全性模組](quickstart-create-security-twin.md)對應項和[設定安全性代理](how-to-agent-configuration.md)程式，以瞭解如何建立、自訂和設定對應項。 請參閱[瞭解模組 twins](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins) ，以深入瞭解模組 twins 在 IoT 中樞中的概念。

## <a name="see-also"></a>請參閱

- [IoT 的 Azure 資訊安全中心總覽](overview.md)
- [部署安全性代理程式](how-to-deploy-agent.md)
- [安全性代理程式驗證方法](concept-security-agent-authentication-methods.md)
