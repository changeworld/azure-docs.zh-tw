---
title: Azure 對等互連服務連線
description: 深入瞭解 Microsoft Azure 對等互連服務連線
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: df337736a676c40f1345310afb9a2e685ddc1d5c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84871318"
---
# <a name="peering-service-connection"></a>對等互連服務連線

連接通常是指識別對等互連服務的邏輯資訊集。 它是藉由指定下列屬性來定義：

- 邏輯名稱
- 連線合作夥伴
- 客戶的實體位置
- IP 首碼

客戶可以根據需求建立單一連線或多個連接。 連接也會用來作為遙測集合的單位。 例如，若要選擇遙測警示，客戶必須定義將受監視的連線。

> [!Note]
> 當您註冊對等互連服務時，我們會分析您的 Windows 和 Office 365 遙測，以便為您所選的前置詞提供延遲測量。
>如需連接遙測的詳細資訊，請參閱對[等互連服務連線遙測](connection-telemetry.md)。
>

>## <a name="how-to-register-a-connection"></a>如何註冊連接？

**案例**-假設分公司散佈在不同的地理位置，如下圖所示。 在此，客戶必須提供邏輯名稱、服務提供者（SP）名稱、客戶的實體位置，以及與單一連線相關聯的 IP 首碼（由客戶所擁有或由服務提供者所配置）。 此程式必須重複，才能針對個別的異地多餘連接註冊對等互連服務。

![異地多餘連接](./media/peering-service-connection/peering-service-connections.png)

> [!Note]
> 狀態層級-當連線地理位置位於美國時，會將篩選視為客戶的實體位置。
>

## <a name="next-steps"></a>後續步驟

若要深入瞭解如何註冊對等互連服務連線的逐步程式，請參閱[使用 Azure 入口網站註冊對等互連服務](azure-portal.md)。

若要深入了解對等互連服務連線遙測，請參閱[對等互連服務連線遙測](connection-telemetry.md)。

若要測量遙測，請參閱[測量連線遙測](measure-connection-telemetry.md)。
