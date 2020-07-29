---
title: 使用 Azure Front 來設定 Web 應用程式防火牆的 bot 保護（預覽）
description: 學習 Web 應用程式防火牆（WAF）。
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: f48b683044bc727cda461fb64a743c055188962a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "76934664"
---
# <a name="configure-bot-protection-for-web-application-firewall-preview"></a>設定 Web 應用程式防火牆的 bot 保護（預覽）
本文說明如何使用 Azure 入口網站在 Azure Web 應用程式防火牆（WAF）中設定 bot 保護規則，以進行 Front 門。 您也可以使用 CLI、Azure PowerShell 或 Azure Resource Manager 範本來設定 Bot 保護規則。

> [!IMPORTANT]
> Bot 保護規則集目前處於公開預覽狀態，並且提供預覽服務等級的協定。 可能不支援特定功能，或可能已經限制功能。  如需詳細資訊，請參閱 [Microsoft Azure 預覽專用的補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>必要條件

遵循[使用 Azure 入口網站建立 Azure front 的 WAF 原則](waf-front-door-create-portal.md)中所述的指示，建立 Front 的基本 WAF 原則。

## <a name="enable-bot-protection-rule-set"></a>啟用 bot 保護規則集

在建立 Web 應用程式防火牆原則時，在 [**受管理的規則**] 頁面中，先尋找 [**受控規則集**] 區段，從下拉式功能表中選取 [規則**Microsoft_BotManager_1 .0** ] 前面的核取方塊，然後選取 [**檢查 + 建立**]。

   ![Bot 保護規則](.././media/waf-front-door-configure-bot-protection/botmanager112019.png)

## <a name="next-steps"></a>後續步驟

- 瞭解如何[監視 WAF](waf-front-door-monitor.md)。
