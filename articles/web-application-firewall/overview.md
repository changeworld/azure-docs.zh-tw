---
title: Azure Web 應用程式防火牆的簡介
description: 本文提供 Azure Web 應用程式防火牆 (WAF) 的概觀
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 03/18/2020
ms.author: victorh
ms.topic: overview
ms.openlocfilehash: 49024c86d09f5cdd9e8b04d5a49f60021660b0c4
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "79475782"
---
# <a name="what-is-azure-web-application-firewall"></a>什麼是 Azure Web 應用程式防火牆？

Web 應用程式防火牆 (WAF) 可集中保護 Web 應用程式，使其免於遭遇常見的攻擊和弱點。 Web 應用程式已逐漸成為利用常見已知弱點進行惡意攻擊的目標。 在這之中，SQL 插入式和跨網站指令碼攻擊是最常見的攻擊。

![WAF 概觀](media/overview/wafoverview.png)

防止應用程式程式碼發生此類攻擊是一項挑戰。 需要對多層次的應用程式拓撲執行嚴格的維護、修補和監視。 集中式 Web 應用程式防火牆有助於簡化安全性管理。 WAF 也提供應用程式系統管理員更好的保證，以防範威脅和入侵。

相較於保護每個個別的 Web 應用程式，WAF 解決方案可透過集中修補已知弱點，更快地因應安全性威脅。

## <a name="supported-service"></a>支援的服務

您可以使用 Microsoft 的 Azure 應用程式閘道、Azure Front Door 和 Azure 內容傳遞網路 (CDN) 服務來部署 WAF。 Azure CDN 上的 WAF 目前處於公開預覽狀態。  WAF 有適用於每個特定服務自訂的功能。 如需每個服務 WAF 功能的詳細資訊，請參閱每個服務的概觀。

## <a name="next-steps"></a>後續步驟

- 如需應用程式閘道上 Web 應用程式防火牆的詳細資訊，請參閱 [Azure 應用程式閘道上的 Web 應用程式防火牆](./ag/ag-overview.md)。
- 如需 Azure Front Door Service 上 Web 應用程式防火牆的詳細資訊，請參閱 [Azure Front Door Service 上的 Web 應用程式防火牆](./afds/afds-overview.md)。
- 如需 Azure CDN 服務上 Web 應用程式防火牆的詳細資訊，請參閱 [Azure CDN 服務上的 Web 應用程式防火牆](./cdn/cdn-overview.md)
