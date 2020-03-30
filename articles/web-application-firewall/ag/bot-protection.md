---
title: 為 Azure Web 應用程式防火牆 （WAF） 配置自動程式保護
description: 瞭解如何在 Azure 應用程式閘道上為 Web 應用程式防火牆 （WAF） 配置自動程式保護。
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 10/31/2019
ms.author: victorh
ms.openlocfilehash: 89c863e85d9eab27a47bc1bf7b98cd1c8d89e900
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73516860"
---
# <a name="configure-bot-protection-for-web-application-firewall-on-azure-application-gateway-preview"></a>為 Azure 應用程式閘道上的 Web 應用程式防火牆配置自動程式保護（預覽）

本文介紹如何使用 Azure 門戶為應用程式閘道配置 Azure Web 應用程式防火牆 （WAF） 中的自動程式保護規則。 

您可以為 WAF 啟用託管自動程式保護規則集，以阻止或記錄來自已知惡意 IP 位址的請求。 這些 IP 位址皆來自 Microsoft 威脅情報摘要。 包括 Azure 資訊安全中心在內的多項服務皆使用 Intelligent Security Graph，其同時也支援 Microsoft 威脅情報的運作。

> [!NOTE]
> 自動程式保護規則集當前處於公共預覽版中，並且提供預覽服務等級協定。 可能不支援特定功能，或可能已經限制功能。 有關詳細資訊，請參閱 Microsoft Azure  [預覽的補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>Prerequisites

按照為 [應用程式閘道創建 Web 應用程式防火牆策略](create-waf-policy-ag.md)中描述的說明，為應用程式閘道創建基本的 WAF 策略。

## <a name="enable-bot-protection-rule-set"></a>啟用自動程式保護規則集

1. 在以前創建**的基本**策略頁中，在 **"設置"** 下，選擇 **"規則**"。  

2. 在詳細資訊頁中，在 **"管理規則"** 部分下，從下拉式功能表中選擇"自動保護"規則的核取方塊，然後選擇"**保存**"。

> [!div class="mx-imgBorder"]
> ![Bot 保護](../media/bot-protection/bot-protection.png)

## <a name="next-steps"></a>後續步驟

有關自訂規則的詳細資訊，請參閱[Azure 應用程式閘道上的 Web 應用程式防火牆 v2 的自訂規則](custom-waf-rules-overview.md)。