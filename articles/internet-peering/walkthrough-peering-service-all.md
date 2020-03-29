---
title: 對等服務夥伴逐步解說
titleSuffix: Azure
description: 對等服務夥伴逐步解說
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: d626d98613bd5d988b599d0980c885d7f73bb958
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720243"
---
# <a name="peering-service-partner-walkthrough"></a>對等服務夥伴逐步解說

本節介紹提供程式為啟用對等互連服務啟用直接對等互連所需的步驟。

## <a name="create-direct-peering-connection-for-peering-service"></a>為對等服務創建直接對等互連連接
服務提供者可以通過創建新的直接對等互連來擴展其地理覆蓋範圍，以支援對等互連服務。 為此，
1. 成為對等服務合作夥伴（如果尚未）
1. 按照說明使用[門戶創建或修改直接對等互連](howto-direct-portal.md)。 確保它滿足高可用性要求。
1. 然後，按照以下步驟[使用門戶 在直接對等互連上啟用對等互連服務](howto-peering-service-portal.md)。

## <a name="use-legacy-direct-peering-connection-for-peering-service"></a>使用舊版直接對等互連連接進行對等互連服務
如果您有要用於支援對等互連服務的舊版直接對等互連，
1. 如果尚未成為對等服務合作夥伴，則成為對等服務合作夥伴。
1. 按照說明使用[門戶將舊版直接對等互連轉換為 Azure 資源](howto-legacy-direct-portal.md)。 如果需要，訂購其他電路以滿足高可用性要求。
1. 然後，按照以下步驟[使用門戶 在直接對等互連上啟用對等互連服務](howto-peering-service-portal.md)。

## <a name="next-steps"></a>後續步驟

* 瞭解[對等策略](https://peering.azurewebsites.net/peering)。
* 要瞭解設置與 Microsoft 的直接對等互連的步驟，請按照[直接對等演練進行](walkthrough-direct-all.md)。
* 要瞭解與 Microsoft 設置 Exchange 對等互連的步驟，請按照[Exchange 對等演練進行](walkthrough-exchange-all.md)。