---
title: 配置標識 - Azure 事件網格 IoT 邊緣 |微軟文檔
description: 配置事件網格模組的標識
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/05/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 0aedeea2a6ad08e1627c2d1a6ebde6c91a4d02d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841760"
---
# <a name="configure-identity-for-the-event-grid-module"></a>配置事件網格模組的標識

本文演示如何為 Edge 上的網格配置標識。 預設情況下，事件網格模組顯示其標識證書，由 IoT 安全守護進程配置。 邊緣上的事件網格在傳遞事件時顯示其標識證書及其傳出調用。 然後，訂閱者可以驗證它是在接收之前發送事件的事件網格模組。

有關所有可能的配置，請參閱[安全和身份驗證](security-authentication.md)指南。

## <a name="always-present-identity-certificate"></a>始終顯示身份證書
下面是始終在傳出調用上顯示標識證書的示例配置。 

```json
 {
  "Env": [
    "outbound__clientAuth__clientCert__enabled=true",
    "outbound__clientAuth__clientCert__source=IoTEdge"
  ]
}
 ```

## <a name="dont-present-identity-certificate"></a>不顯示身份證書
下面是未在傳出調用上顯示標識證書的示例配置。 

```json
 {
  "Env": [
    "outbound__clientAuth__clientCert__enabled=false"
  ]
}
 ```
