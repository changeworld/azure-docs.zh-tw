---
title: Azure 應用程式閘道的重新導向概觀
description: 瞭解 Azure 應用程式閘道中的重定向功能，將一個攔截器上接收的流量重定向到另一個攔截器或外部網站。
services: application-gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
ms.date: 11/16/2019
ms.author: amsriva
ms.openlocfilehash: 5943d8aad4d5dd0d981fae9b2325dd3fc75b31e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74129877"
---
# <a name="application-gateway-redirect-overview"></a>應用程式閘道重新導向概觀

您可以使用應用程式閘道重定向流量。  這是泛型重新導向機制，可將在一個接聽程式接收的流量重新導向至另一個接聽程式或外部網站。 這可簡化應用程式組態、將資源使用量最佳化，並支援新的重新導向案例，包括全域和路徑式重新導向。

對於許多 Web 應用程式，常見的重定向方案是支援自動 HTTP 到 HTTPS 重定向，以確保應用程式與其使用者之間的所有通信都通過加密路徑進行。 在過去，客戶會使用一些技巧 (例如建立專屬後端集區)，其唯一目的是要將其在 HTTP 上接收的要求重新導向至 HTTPS。 借助應用程式閘道中的重定向支援，只需向路由規則添加新重定向配置，並將另一個具有 HTTPS 協定攔截器的攔截器指定為目標攔截器即可實現此目的。

支援以下類型的重定向：

- 301 永久重定向
- 302 已找到
- 303 請參閱其他
- 307 臨時重定向

應用程式閘道重新導向提供下列功能：

-  **全域重新導向**

   從閘道上的一個接聽程式到另一個接聽程式的重新導向。 這允許在網站上進行 HTTP 至 HTTPS 重新導向。
- **路徑式重新導向**

   這類型的重新導向只允許在特定網站區域上進行 HTTP 至 HTTPS 重新導向，例如以 /cart/* 表示的購物車區域。
- **重新導向至外部網站**

![重新導向](./media/redirect-overview/redirect.png)

有了這項變更，客戶必須建立新的重新導向組態物件，以指定重新導向所需的目標接聽程式或外部網站。 此組態元素也支援能將 URI 路徑和查詢字串附加至重新導向之 URL 的選項。 您還可以選擇重定向的類型。 一旦建立，此重新導向組態會透過新規則附加至來源接聽程式。 若使用時基本規則，重新導向組態會與來源接聽程式相關聯並為全域重新導向。 使用路徑式規則時，就會在 URL 路徑對應上定義重新導向組態。 因此它只適用於網站的特定路徑區域。

### <a name="next-steps"></a>後續步驟

[在應用程式閘道上設定 URL 重新導向](tutorial-url-redirect-powershell.md)
