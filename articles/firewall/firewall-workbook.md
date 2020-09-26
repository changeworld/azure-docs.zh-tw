---
title: 使用 Azure 防火牆活頁簿監視記錄
description: Azure 防火牆活頁簿可為 Azure 防火牆資料分析提供彈性的畫布，並在 Azure 入口網站中建立豐富的視覺效果報表。
services: firewall
author: gopimsft
ms.service: firewall
ms.topic: how-to
ms.date: 09/22/2020
ms.author: victorh
ms.openlocfilehash: dabe154535d2bd3ae5550d4bbbafa75adc5ae2c0
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91342649"
---
# <a name="monitor-logs-using-azure-firewall-workbook"></a>使用 Azure 防火牆活頁簿監視記錄

Azure 防火牆活頁簿為 Azure 防火牆資料分析提供了彈性的畫布。 您可以使用它，在 Azure 入口網站中建立豐富的視覺效果報表。 您可以利用多個在 Azure 中部署的防火牆，並將它們結合成整合的互動式體驗。

您可以深入瞭解 Azure 防火牆事件、瞭解您的應用程式和網路規則，以及查看 Url、埠和位址之間的防火牆活動統計資料。 Azure 防火牆活頁簿可讓您篩選防火牆和資源群組，並在調查記錄中的問題時，以可輕鬆讀取的資料集，以動態方式篩選每個類別。 

## <a name="prerequisites"></a>Prerequisites

開始之前，您應該透過 Azure 入口網站 [啟用診斷記錄](firewall-diagnostics.md#enable-diagnostic-logging-through-the-azure-portal) 。 此外，請閱讀 [Azure 防火牆記錄和計量](logs-and-metrics.md) ，以瞭解適用于 azure 防火牆的診斷記錄和計量。

## <a name="get-started"></a>開始使用

若要部署活頁簿，請移至 [適用于 Azure 防火牆的 Azure 監視器活頁簿](https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20Firewall/Azure%20Monitor%20Workbook) ，並遵循頁面上的指示。 Azure 防火牆活頁簿是設計來跨多租使用者、多個訂用帳戶運作，而且可篩選為多個防火牆。

## <a name="overview-page"></a>概觀分頁

[總覽] 頁面可讓您在工作區、時間和防火牆之間進行篩選。 它會依時間顯示各種防火牆和記錄類型的事件 (應用程式、網路、威脅 intel、DNS proxy) 。

:::image type="content" source="./media/firewall-workbook/firewall-workbook-overview.png" alt-text="Azure 防火牆活頁簿總覽":::

## <a name="application-rule-log-statistics"></a>應用程式規則記錄統計資料

此頁面會顯示一段時間內 IP 位址的唯一來源、應用程式規則計數使用量、拒絕/允許一段時間的 FQDN，以及篩選過的資料。 您可以根據 IP 位址篩選資料。

:::image type="content" source="./media/firewall-workbook/firewall-workbook-application-rule.png" alt-text="Azure 防火牆活頁簿應用程式規則記錄檔":::

## <a name="network-rule-log-statistics"></a>網路規則記錄統計資料

此頁面提供依規則動作（允許/拒絕）、依 IP 和 DNAT 一段時間的目標埠來查看。 您也可以依動作、埠和目的地類型進行篩選。

:::image type="content" source="./media/firewall-workbook/firewall-workbook-network-rule.png" alt-text="Azure 防火牆活頁簿網路規則記錄檔":::

您也可以根據時間範圍來篩選記錄：

:::image type="content" source="./media/firewall-workbook/firewall-workbook-network-rule-time.png" alt-text="Azure 防火牆活頁簿網路規則記錄時間範圍":::

## <a name="investigations"></a>調查

您可以查看記錄，並根據來源 IP 位址深入瞭解資源。 您可以取得虛擬機器名稱和網路介面名稱等資訊。 從記錄篩選至資源很簡單。

:::image type="content" source="./media/firewall-workbook/firewall-workbook-investigation.png" alt-text="Azure 防火牆活頁簿調查":::

## <a name="next-steps"></a>後續步驟

- 深入瞭解 [Azure 防火牆診斷](firewall-diagnostics.md)