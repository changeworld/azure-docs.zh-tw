---
title: Azure 應用程式閘道自動程式保護概述上的 WAF
titleSuffix: Azure Web Application Firewall
description: 本文概述了應用程式閘道機器人保護方面的 Web 應用程式防火牆 （WAF）
services: web-application-firewall
author: winthrop28
ms.service: web-application-firewall
ms.date: 02/04/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 3bc481cfc35ac94699d2795862f1fe8e4decf875
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77027091"
---
# <a name="azure-web-application-firewall-on-azure-application-gateway-bot-protection-overview"></a>Azure 應用程式防火牆上的 Azure 應用程式閘道自動程式保護概述

大約20%的互聯網流量來自不良機器人。 他們執行刮擦、掃描和查找 Web 應用程式中的漏洞等操作。 當這些自動程式在 Web 應用程式防火牆 （WAF） 停止時，它們不能攻擊您。 它們也不能佔用您的資源和服務，例如後端和其他底層基礎結構。

您可以為 WAF 啟用託管自動程式保護規則集，以阻止或記錄來自已知惡意 IP 位址的請求。 這些 IP 位址皆來自 Microsoft 威脅情報摘要。 包括 Azure 資訊安全中心在內的多項服務皆使用 Intelligent Security Graph，其同時也支援 Microsoft 威脅情報的運作。

> [!IMPORTANT]
> 自動程式保護規則集當前處於公共預覽版中，並且提供預覽服務等級協定。 可能不支援特定功能，或可能已經限制功能。 有關詳細資訊，請參閱 Microsoft Azure  [預覽的補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="use-with-owasp-rulesets"></a>與 OWASP 規則集一起使用

您可以將 Bot 保護規則集與任何 OWASP 規則集（2.2.9、3.0 和 3.1）一起使用。 在任何給定時間只能使用一個 OWASP 規則集。 自動程式保護規則集包含一個出現在其自己的規則集中的其他規則。 它的標題是**Microsoft_BotManagerRuleSet_0.1，** 你可以像其他OWASP規則一樣啟用或禁用它。

![機器人規則集](../media/bot-protection-overview/bot-ruleset.png)

## <a name="ruleset-update"></a>規則集更新

已知不良 IP 位址的自動程式緩解規則集清單每天從 Microsoft 威脅智慧源更新多次，以便與自動程式保持同步。 即使自動攻擊媒介發生變化，Web 應用程式也會不斷受到保護。

## <a name="next-steps"></a>後續步驟

- [為 Azure 應用程式閘道上的 Web 應用程式防火牆配置自動程式保護（預覽）](bot-protection.md)
