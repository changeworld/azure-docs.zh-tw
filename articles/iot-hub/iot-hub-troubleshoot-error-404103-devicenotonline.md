---
title: 故障排除 Azure IoT 中心錯誤 404103 設備不線上
description: 瞭解如何修復錯誤 404103 設備不線上
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: e648428f924cfc33421c8591c41f7ac85b05a033
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960811"
---
# <a name="404103-devicenotonline"></a>404103 DeviceNotOnline

本文介紹了**404103 設備不線上**錯誤的原因和解決方案。

## <a name="symptoms"></a>徵狀

直接到設備的方法失敗，錯誤**404103 設備NotOnline，** 即使設備處於線上狀態也是如此。 

## <a name="cause"></a>原因

如果您知道設備處於線上狀態，但仍收到錯誤，則很可能是因為直接方法回檔未在設備上註冊。

## <a name="solution"></a>解決方法

要正確配置設備以進行直接方法回檔，請參閱[在設備上處理直接方法](iot-hub-devguide-direct-methods.md#handle-a-direct-method-on-a-device)。