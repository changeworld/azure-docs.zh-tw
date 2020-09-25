---
title: '使用 Azure Front Door (Preview 設定 Web 應用程式防火牆的 bot 保護) '
description: 瞭解如何使用 Azure 入口網站在 Azure Web 應用程式防火牆中設定 bot 保護規則 (WAF) 的 Front Door。
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: 2357c51f47bcb9bd8bbc6c408cb6d8edbab4d10e
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91267001"
---
# <a name="configure-bot-protection-for-web-application-firewall-preview"></a>設定 Web 應用程式防火牆的 bot 保護 (預覽) 
本文說明如何使用 Azure 入口網站，在 Azure Web 應用程式防火牆中設定 bot 保護規則 (WAF) 的 Front Door。 您也可以使用 CLI、Azure PowerShell 或 Azure Resource Manager 範本來設定 Bot 保護規則。

> [!IMPORTANT]
> Bot 保護規則集目前處於公開預覽狀態，並且提供預覽服務等級的協定。 可能不支援特定功能，或可能已經限制功能。  如需詳細資訊，請參閱 [Microsoft Azure 預覽專用的補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>Prerequisites

遵循 [使用 Azure 入口網站建立 Azure Front Door 的 WAF 原則](waf-front-door-create-portal.md)中所述的指示，建立 Front Door 的基本 WAF 原則。

## <a name="enable-bot-protection-rule-set"></a>啟用 bot 保護規則集

在 [ **受控規則** ] 頁面中建立 Web 應用程式防火牆原則時，先尋找 [ **受控規則集** ] 區段，從下拉式功能表中選取規則 **Microsoft_BotManager_1 .0** 前面的核取方塊，然後選取 [ **審核 + 建立**]。

   ![Bot 保護規則](.././media/waf-front-door-configure-bot-protection/botmanager112019.png)

## <a name="next-steps"></a>後續步驟

- 瞭解如何 [監視 WAF](waf-front-door-monitor.md)。
