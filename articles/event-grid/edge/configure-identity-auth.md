---
title: 設定身分識別-Azure Event Grid IoT Edge |Microsoft Docs
description: 設定事件方格模組的身分識別
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 2418c8518bbf298a102d4f29b4a973f6121de2eb
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2020
ms.locfileid: "86171681"
---
# <a name="configure-identity-for-the-event-grid-module"></a>設定事件方格模組的身分識別

本文說明如何設定 Edge 上格線的識別。 根據預設，事件方格模組會顯示其身分識別憑證，如 IoT 安全性守護程式所設定。 邊緣上的事件方格會在傳遞事件時，以其撥出電話呈現其身分識別憑證。 然後，訂閱者可以驗證它是在接受之前傳送事件的事件方格模組。

如需所有可能的設定，請參閱[安全性和驗證](security-authentication.md)指南。

## <a name="always-present-identity-certificate"></a>一律呈現身分識別憑證
以下是一律在撥出電話上呈現身分識別憑證的範例設定。 

```json
 {
  "Env": [
    "outbound__clientAuth__clientCert__enabled=true",
    "outbound__clientAuth__clientCert__source=IoTEdge"
  ]
}
 ```

## <a name="dont-present-identity-certificate"></a>不要出示身分識別憑證
以下是未在撥出電話上呈現身分識別憑證的範例設定。 

```json
 {
  "Env": [
    "outbound__clientAuth__clientCert__enabled=false"
  ]
}
 ```
