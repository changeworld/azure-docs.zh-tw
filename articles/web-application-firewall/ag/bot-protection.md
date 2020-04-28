---
title: 設定 Azure Web 應用程式防火牆的 bot 保護（WAF）
description: 瞭解如何在 Azure 應用程式閘道上設定 Web 應用程式防火牆（WAF）的 bot 保護。
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 10/31/2019
ms.author: victorh
ms.openlocfilehash: 89c863e85d9eab27a47bc1bf7b98cd1c8d89e900
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "73516860"
---
# <a name="configure-bot-protection-for-web-application-firewall-on-azure-application-gateway-preview"></a>在 Azure 應用程式閘道上設定 Web 應用程式防火牆的 bot 保護（預覽）

本文說明如何在 Azure Web 應用程式防火牆（WAF）中為使用 Azure 入口網站的應用程式閘道設定 bot 保護規則。 

您可以為 WAF 啟用受管理的 bot 保護規則集，以封鎖或記錄來自已知惡意 IP 位址的要求。 這些 IP 位址皆來自 Microsoft 威脅情報摘要。 包括 Azure 資訊安全中心在內的多項服務皆使用 Intelligent Security Graph，其同時也支援 Microsoft 威脅情報的運作。

> [!NOTE]
> Bot 保護規則集目前處於公開預覽狀態，且提供預覽服務等級協定。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 的補充使用規定。

## <a name="prerequisites"></a>Prerequisites

遵循 [建立適用于應用程式閘道的 Web 應用程式防火牆原則](create-waf-policy-ag.md)中所述的指示，建立應用程式閘道的基本 WAF 原則。

## <a name="enable-bot-protection-rule-set"></a>啟用 bot 保護規則集

1. 在您先前建立的 [**基本**原則] 頁面中，選取 [**設定**] 底下的 [**規則**]。  

2. 在 [詳細資料] 頁面的 [ **管理規則** ] 區段下，從下拉式功能表中選取 bot 保護規則的核取方塊，然後選取 [**儲存**]。

> [!div class="mx-imgBorder"]
> ![Bot 保護](../media/bot-protection/bot-protection.png)

## <a name="next-steps"></a>後續步驟

如需自訂規則的詳細資訊，請參閱[Azure 應用程式閘道上的 Web 應用程式防火牆 v2 的自訂規則](custom-waf-rules-overview.md)。