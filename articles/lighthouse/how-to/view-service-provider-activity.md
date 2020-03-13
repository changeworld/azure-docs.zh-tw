---
title: 檢視服務提供者活動
description: 客戶可以查看已記錄的活動，以查看服務提供者透過 Azure 委派的資源管理所執行的動作。
ms.date: 01/15/2020
ms.topic: conceptual
ms.openlocfilehash: a923a57ecc94ac15af207c2b8dc8998708b708d4
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2020
ms.locfileid: "77649631"
---
# <a name="view-service-provider-activity"></a>檢視服務提供者活動

對於 Azure 委派資源管理具有委派訂用帳戶的客戶，可以[查看 Azure 活動記錄](../../azure-monitor/platform/platform-logs-overview.md)資料，以查看所採取的所有動作。 這可讓客戶完整看到服務提供者透過 Azure 委派的資源管理執行的作業，以及客戶自己的 Azure Active Directory （Azure AD）租使用者內的使用者所執行的作業。

> [!TIP]
> 我們也會提供 Azure 原則內建原則定義，以將範圍委派審查至管理租使用者。 如需詳細資訊，請參閱[在您的環境中審核委派](view-manage-service-providers.md#audit-delegations-in-your-environment)。

## <a name="view-activity-log-data"></a>查看活動記錄資料

您可以從 Azure 入口網站中的 [**監視**] 功能表[查看活動記錄](../../azure-monitor/platform/activity-log-view.md)。 若要將結果限制為特定的訂用帳戶，請使用篩選準則來選取特定的訂用帳戶。 您也可以透過程式設計方式來[查看和取出活動記錄事件](../../azure-monitor/platform/activity-log-view.md)。

> [!NOTE]
> 如果客戶租使用者中的委派訂用帳戶已被授與[讀取](../../role-based-access-control/built-in-roles.md#reader)者角色（或另一個內建角色，其中包含讀取器存取權），而該訂用帳戶是針對 Azure 委派的資源管理所上架時，則服務提供者租使用者中的使用者可以查看該帳戶的活動記錄結果。

在 [活動記錄檔] 中，您會看到作業的名稱和其狀態，以及執行的日期和時間。 [**由**下列時間起始的事件] 會顯示哪些使用者執行了作業，不論是在服務提供者的租使用者中，是透過 Azure 委派的資源管理，還是客戶自己的租使用者中的使用者。 請注意，會顯示使用者的名稱，而不是租使用者或已指派給該訂用帳戶的角色。

記錄的活動可在過去90天的 Azure 入口網站中使用。 若要瞭解如何儲存此資料超過90天，請參閱[在 Log Analytics 工作區中收集並分析 Azure 活動記錄](../../azure-monitor/platform/activity-log-collect.md)。

> [!NOTE]
> 服務提供者的使用者會出現在活動記錄中，但這些使用者及其角色指派不會顯示在**存取控制（IAM）** 中，或透過 api 來抓取角色指派資訊時。

## <a name="set-alerts-for-critical-operations"></a>設定重大作業的警示

若要留意服務提供者（或您自己的租使用者中的使用者）執行的重要作業，我們建議您建立[活動記錄警示](../../azure-monitor/platform/activity-log-alerts.md)。 例如，您可能想要追蹤訂用帳戶的所有系統管理動作，或在特定資源群組中的任何虛擬機器遭到刪除時收到通知。 當您建立警示時，它們會包含客戶自己的租使用者以及任何管理的租使用者中的使用者所執行的動作。

如需詳細資訊，請參閱[建立和管理活動記錄警示](../../azure-monitor/platform/alerts-activity-log.md)。

## <a name="create-log-queries"></a>建立記錄查詢

您可以建立查詢來分析您的已記錄活動，或將焦點放在特定專案。 例如，可能會有 audit 要求您報告在訂用帳戶上執行的所有系統管理層級動作。 您可以建立查詢，只篩選這些動作，並依使用者、日期或其他值來排序結果。

如需詳細資訊，請參閱[Azure 監視器中記錄查詢的總覽](../../azure-monitor/log-query/log-query-overview.md)。

## <a name="next-steps"></a>後續步驟

- 深入了解 [Azure 監視器](../../azure-monitor/index.yml)。
- 瞭解如何在 Azure 入口網站中[查看及管理服務提供者供應](view-manage-service-providers.md)專案。