---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 09/18/2019
ms.author: cephalin
ms.openlocfilehash: e00db06346b19ef85eb77626eb2ed169d2224b6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "71129699"
---
當您複製其他部署位置的組態時，可以編輯複製的組態。 某些配置元素在交換後跟蹤內容（不是特定于插槽），而其他配置元素在交換後保留在同一插槽中（特定于插槽）。 以下清單顯示當您交換位置時會變更的設定。

**交換的設定**：

* 常規設置，如框架版本、32/64 位、Web 通訊端
* 應用程式設定 (可以設定為停在某一個位置)
* 連接字串 (可以設定為停在某一個位置)
* 處理常式對應
* 公開憑證
* WebJobs 內容
* 混合連接 |
* 虛擬網路集成 |
* 服務端點 |
* Azure 內容交付網路 |

標有星號 （*） 的功能計畫取消交換。 

**無法交換的設定**：

* 發行端點
* 自訂網域名稱
* 非公共證書和 TLS/SSL 設置
* 調整大小設定
* WebJobs 排程器
* IP 限制
* 永遠開啟
* 診斷日誌設置
* 跨源資源分享 （CORS）

> [!NOTE]
> 某些應用於未交換設置的應用設置也不會交換。 例如，由於未交換診斷日誌設置，因此相關應用設置（如`WEBSITE_HTTPLOGGING_RETENTION_DAYS`和`DIAGNOSTICS_AZUREBLOBRETENTIONDAYS`也不會交換）即使它們未顯示為插槽設置也是如此。
>
