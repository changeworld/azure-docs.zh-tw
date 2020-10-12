---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 09/18/2019
ms.author: cephalin
ms.openlocfilehash: b4234acb2ce32980a268e389cb31de9a57ed18e6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "82131702"
---
當您複製其他部署位置的組態時，可以編輯複製的組態。 某些設定元素會在交換 (不是特定位置的) 上執行內容，而其他設定元素則會在交換 (位置特定) 之後停留在相同的位置。 以下清單顯示當您交換位置時會變更的設定。

**交換的設定**：

* 一般設定，例如 framework 版本、32/64 位、web 通訊端
* 應用程式設定 (可以設定為停在某一個位置)
* 連接字串 (可以設定為停在某一個位置)
* 處理常式對應
* 公開憑證
* WebJobs 內容
* 混合式連接 *
* 虛擬網路整合 *
* 服務端點 *
* Azure 內容傳遞網路 *

以星號 ( * ) 標記的功能預計會 unswapped。 

**無法交換的設定**：

* 發行端點
* 自訂網域名稱
* 非公用憑證與 TLS/SSL 設定
* 調整大小設定
* WebJobs 排程器
* IP 限制
* 永遠開啟
* 診斷設定
* 跨原始資源分享 (CORS) 

> [!NOTE]
> 套用至 unswapped 設定的某些應用程式設定也不會交換。 例如，因為診斷設定不會交換，所以和等相關的應用程式設定 `WEBSITE_HTTPLOGGING_RETENTION_DAYS` `DIAGNOSTICS_AZUREBLOBRETENTIONDAYS` 也不會交換，即使它們沒有顯示為位置設定亦同。
>
