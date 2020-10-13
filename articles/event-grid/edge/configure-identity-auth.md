---
title: 設定身分識別-Azure 事件方格 IoT Edge |Microsoft Docs
description: 設定事件方格模組的身分識別
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 2418c8518bbf298a102d4f29b4a973f6121de2eb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86171681"
---
# <a name="configure-identity-for-the-event-grid-module"></a>設定事件方格模組的身分識別

本文說明如何設定 Edge 上方格的身分識別。 根據預設，事件方格模組會顯示其身分識別憑證，如 IoT 安全性背景程式所設定。 Edge 上的事件方格在傳遞事件時，會顯示其身分識別憑證與其傳出的呼叫。 接著，訂閱者可以驗證它是在接受之前傳送事件的事件方格模組。

如需所有可能的設定，請參閱 [安全性和驗證](security-authentication.md) 指南。

## <a name="always-present-identity-certificate"></a>一律顯示身分識別憑證
以下是在撥出電話上一律呈現身分識別憑證的範例設定。 

```json
 {
  "Env": [
    "outbound__clientAuth__clientCert__enabled=true",
    "outbound__clientAuth__clientCert__source=IoTEdge"
  ]
}
 ```

## <a name="dont-present-identity-certificate"></a>不出示身分識別憑證
以下是未在外寄呼叫上呈現身分識別憑證的範例設定。 

```json
 {
  "Env": [
    "outbound__clientAuth__clientCert__enabled=false"
  ]
}
 ```
