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
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720243"
---
# <a name="peering-service-partner-walkthrough"></a>對等服務夥伴逐步解說

本節說明提供者在啟用對等互連服務的直接對等互連時所需遵循的步驟。

## <a name="create-direct-peering-connection-for-peering-service"></a>建立對等互連服務的直接對等互連連線
服務提供者可以藉由建立支援對等互連服務的新直接對等互連，來擴充其地理範圍。 若要完成此工作，
1. 成為對等服務合作夥伴（如果尚未這麼做）
1. 遵循指示，[使用入口網站建立或修改直接對等互連](howto-direct-portal.md)。 請確定它符合高可用性需求。
1. 然後，依照下列步驟，[使用入口網站在直接對等互連上啟用對等互連服務](howto-peering-service-portal.md)。

## <a name="use-legacy-direct-peering-connection-for-peering-service"></a>對等互連服務使用舊版直接對等互連連線
如果您有想要用來支援對等互連服務的舊版直接對等互連，
1. 成為對等服務夥伴（如果尚未這麼做）。
1. 遵循指示，[使用入口網站將舊版直接對等互連轉換至 Azure 資源](howto-legacy-direct-portal.md)。 如有需要，請訂購額外的線路以符合高可用性需求。
1. 然後，依照下列步驟，[使用入口網站在直接對等互連上啟用對等互連服務](howto-peering-service-portal.md)。

## <a name="next-steps"></a>後續步驟

* 深入瞭解對[等互連原則](https://peering.azurewebsites.net/peering)。
* 若要瞭解設定與 Microsoft 直接對等互連的步驟，請遵循[直接對等的逐步](walkthrough-direct-all.md)解說。
* 若要瞭解設定與 Microsoft 交換對等互連的步驟，請遵循[Exchange 對等互連逐步](walkthrough-exchange-all.md)解說。