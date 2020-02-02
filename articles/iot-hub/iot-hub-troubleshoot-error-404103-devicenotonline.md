---
title: 疑難排解 Azure IoT 中樞錯誤 404103 DeviceNotOnline
description: 瞭解如何修正錯誤 404103 DeviceNotOnline
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: e648428f924cfc33421c8591c41f7ac85b05a033
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/01/2020
ms.locfileid: "76960811"
---
# <a name="404103-devicenotonline"></a>404103 DeviceNotOnline

本文說明**404103 DeviceNotOnline**錯誤的原因和解決方案。

## <a name="symptoms"></a>徵狀

裝置的直接方法會因錯誤**404103 DeviceNotOnline**而失敗，即使裝置已上線也一樣。 

## <a name="cause"></a>原因

如果您知道裝置已上線，而且仍然收到錯誤，可能是因為直接方法回呼未在裝置上註冊。

## <a name="solution"></a>解決方案

若要正確設定您的裝置以進行直接方法回呼，請參閱[在裝置上處理直接方法](iot-hub-devguide-direct-methods.md#handle-a-direct-method-on-a-device)。