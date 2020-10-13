---
title: Azure 應用程式閘道的重新導向概觀
description: 瞭解 Azure 應用程式閘道中的重新導向功能，以將一個接聽程式接收到的流量重新導向至另一個接聽程式或外部網站。
services: application-gateway
author: amsriva
ms.service: application-gateway
ms.topic: conceptual
ms.date: 11/16/2019
ms.author: amsriva
ms.openlocfilehash: fb01d5a4923410f693b682d66be8d5d09f9019d0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90561573"
---
# <a name="application-gateway-redirect-overview"></a>應用程式閘道重新導向概觀

您可以使用應用程式閘道來重新導向流量。  這是泛型重新導向機制，可將在一個接聽程式接收的流量重新導向至另一個接聽程式或外部網站。 這可簡化應用程式組態、將資源使用量最佳化，並支援新的重新導向案例，包括全域和路徑式重新導向。

許多 web 應用程式的常見重新導向案例是支援自動 HTTP 至 HTTPS 的重新導向，以確保應用程式與其使用者之間的所有通訊都是透過加密的路徑進行。 在過去，客戶會使用一些技巧 (例如建立專屬後端集區)，其唯一目的是要將其在 HTTP 上接收的要求重新導向至 HTTPS。 使用應用程式閘道中的重新導向支援時，只要將新的重新導向設定新增至路由規則，並將另一個接聽程式的 HTTPS 通訊協定指定為目標接聽程式，就可以完成這項工作。

支援下列類型的重新導向：

- 301永久重新導向
- 302 已找到
- 303請參閱其他
- 307暫時重新導向

應用程式閘道重新導向提供下列功能：

-  **全域重新導向**

   從閘道上的一個接聽程式到另一個接聽程式的重新導向。 這允許在網站上進行 HTTP 至 HTTPS 重新導向。
- **路徑式重新導向**

   這類型的重新導向只允許在特定網站區域上進行 HTTP 至 HTTPS 重新導向，例如以 /cart/* 表示的購物車區域。
- **重新導向至外部網站**

![圖表會顯示使用者和應用程式閘道以及兩者之間的連線，包括未解除鎖定的 H T P 紅色箭號、不允許的301直接紅色箭號，以及鎖定的 H T T P S a 綠色箭號。](./media/redirect-overview/redirect.png)

有了這項變更，客戶必須建立新的重新導向組態物件，以指定重新導向所需的目標接聽程式或外部網站。 此組態元素也支援能將 URI 路徑和查詢字串附加至重新導向之 URL 的選項。 您也可以選擇重新導向的類型。 一旦建立，此重新導向組態會透過新規則附加至來源接聽程式。 若使用時基本規則，重新導向組態會與來源接聽程式相關聯並為全域重新導向。 使用路徑式規則時，就會在 URL 路徑對應上定義重新導向組態。 因此它只適用於網站的特定路徑區域。

### <a name="next-steps"></a>後續步驟

[在應用程式閘道上設定 URL 重新導向](tutorial-url-redirect-powershell.md)
