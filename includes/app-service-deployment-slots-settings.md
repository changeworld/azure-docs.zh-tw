---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 09/18/2019
ms.author: cephalin
ms.openlocfilehash: b4234acb2ce32980a268e389cb31de9a57ed18e6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82131702"
---
當您複製其他部署位置的組態時，可以編輯複製的組態。 某些設定元素會遵循交換的內容（非位置特定），而其他設定元素會保留在交換之後的相同位置（位置特定）。 以下清單顯示當您交換位置時會變更的設定。

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

以星號（*）標示的功能會規劃為 unswapped。 

**無法交換的設定**：

* 發行端點
* 自訂網域名稱
* 非公用憑證和 TLS/SSL 設定
* 調整大小設定
* WebJobs 排程器
* IP 限制
* 永遠開啟
* 診斷設定
* 跨原始來源資源分享（CORS）

> [!NOTE]
> 套用至 unswapped 設定的特定應用程式設定也不會進行交換。 例如，由於診斷設定不會交換，因此和等相關應用程式設定 `WEBSITE_HTTPLOGGING_RETENTION_DAYS` `DIAGNOSTICS_AZUREBLOBRETENTIONDAYS` 也不會交換，即使它們未顯示為位置設定也一樣。
>
