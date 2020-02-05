---
title: Azure 應用程式閘道 bot 保護總覽上的 WAF
titleSuffix: Azure Web Application Firewall
description: 本文提供應用程式閘道 bot 保護上的 web 應用程式防火牆（WAF）總覽
services: web-application-firewall
author: winthrop28
ms.service: web-application-firewall
ms.date: 02/04/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 3bc481cfc35ac94699d2795862f1fe8e4decf875
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/05/2020
ms.locfileid: "77027091"
---
# <a name="azure-web-application-firewall-on-azure-application-gateway-bot-protection-overview"></a>Azure 應用程式閘道 bot 保護的 Azure Web 應用程式防火牆總覽

大約有20% 的網際網路流量來自不良的 bot。 它們會進行抓取、掃描和尋找 web 應用程式中的弱點等動作。 當這些 bot 在 Web 應用程式防火牆（WAF）停止時，就無法攻擊您。 它們也不能使用您的資源和服務，例如您的後端和其他基礎結構。

您可以為 WAF 啟用受管理的 bot 保護規則集，以封鎖或記錄來自已知惡意 IP 位址的要求。 這些 IP 位址皆來自 Microsoft 威脅情報摘要。 包括 Azure 資訊安全中心在內的多項服務皆使用 Intelligent Security Graph，其同時也支援 Microsoft 威脅情報的運作。

> [!IMPORTANT]
> Bot 保護規則集目前處於公開預覽狀態，且提供預覽服務等級協定。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽的補充使用](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)規定 。

## <a name="use-with-owasp-rulesets"></a>使用 with OWASP 規則集

您可以使用 Bot 保護規則集與任何 OWASP 規則集（2.2.9、3.0 和3.1）。 在任何指定的時間都只能使用一個 OWASP 規則集。 Bot 保護規則集包含一個額外的規則，它會出現在本身的規則集內。 其標題為**Microsoft_BotManagerRuleSet_0 .1**，您可以像其他 OWASP 規則一樣啟用或停用它。

![Bot 規則集](../media/bot-protection-overview/bot-ruleset.png)

## <a name="ruleset-update"></a>規則集更新

已知錯誤 IP 位址的 bot 緩和規則集清單會每天更新一次，來自 Microsoft 威脅情報摘要，以維持與 bot 的同步。 即使 bot 攻擊向量變更，您的 web 應用程式仍會持續受到保護。

## <a name="next-steps"></a>後續步驟

- [在 Azure 應用程式閘道上設定 Web 應用程式防火牆的 bot 保護（預覽）](bot-protection.md)
