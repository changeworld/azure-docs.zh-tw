---
title: 使用 Azure 前門配置 Web 應用程式防火牆的自動程式保護（預覽版）
description: 瞭解 Web 應用程式防火牆 （WAF）。
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: f48b683044bc727cda461fb64a743c055188962a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76934664"
---
# <a name="configure-bot-protection-for-web-application-firewall-preview"></a>為 Web 應用程式防火牆配置自動程式保護（預覽）
本文介紹如何使用 Azure 門戶在 Azure Web 應用程式防火牆 （WAF） 中為前門配置自動程式保護規則。 還可以使用 CLI、Azure PowerShell 或 Azure 資源管理器範本配置自動程式保護規則。

> [!IMPORTANT]
> Bot 保護規則集目前處於公開預覽狀態，並且提供預覽服務等級的協定。 可能不支援特定功能，或可能已經限制功能。  如需詳細資訊，請參閱 [Microsoft Azure 預覽專用的補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>Prerequisites

使用 Azure 門戶，按照 Azure[前門創建 WAF 策略](waf-front-door-create-portal.md)中描述的說明，為前門創建基本 WAF 策略。

## <a name="enable-bot-protection-rule-set"></a>啟用自動程式保護規則集

在創建 Web 應用程式防火牆策略時的 **"託管規則"** 頁中，首先查找**託管規則集**部分，從下拉式功能表中選擇規則**Microsoft_BotManager_1.0**前面的核取方塊，然後選擇"審閱 **+ 創建**"。

   ![機器人保護規則](.././media/waf-front-door-configure-bot-protection/botmanager112019.png)

## <a name="next-steps"></a>後續步驟

- 瞭解如何監視[WAF](waf-front-door-monitor.md)。
