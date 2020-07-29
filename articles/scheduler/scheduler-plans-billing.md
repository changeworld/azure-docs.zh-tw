---
title: 方案和計費
description: 了解 Azure 排程器的方案與計費
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: e821036ff4ddb5a9786bc4f4537bb81539ab2c87
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "78898482"
---
# <a name="plans-and-billing-for-azure-scheduler"></a>Azure 排程器的方案與計費

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) 將會取代[即將淘汰的 Azure 排程器](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date)。 若要繼續使用您在排程器中設定的作業，請儘快[遷移至 Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md) 。 
>
> Azure 入口網站中已不再提供排程器，但目前仍然提供 [REST API](/rest/api/scheduler) 和 [Azure 排程器 PowerShell Cmdlets](scheduler-powershell-reference.md)，以便您管理作業和作業集合。

## <a name="job-collection-plans"></a>作業集合方案

在 Azure 排程器中，作業集合會包含特定數目的作業。 作業集合是可計費的實體，有標準、P10 進階和 P20 進階方案，以下將有所說明： 

| 作業集合方案 | 每項集合作業數上限 | 週期上限 | 每個訂用帳戶的作業集合數上限 | 限制 | 
|:--- |:--- |:--- |:--- |:--- |
| **Standard** | 每個集合 50 個作業數 | 每分鐘一次。 每分鐘無法執行一次以上的作業。 | 每個 Azure 訂用帳戶最多都可以有 100 個標準作業集合。 | 存取排程器的完整功能集 | 
| **P10 Premium** | 每個集合 50 個作業數 | 每分鐘一次。 每分鐘無法執行一次以上的作業。 | 每個 Azure 訂用帳戶最多都可以有 10,000 個 P10 的進階作業集合。 如需更多集合數，請<a href="mailto:wapteams@microsoft.com">與我們連絡</a>。 | 存取排程器的完整功能集 |
| **P20 Premium** | 每個集合 1000 個作業數 | 每分鐘一次。 每分鐘無法執行一次以上的作業。 | 每個 Azure 訂用帳戶最多都可以有 5,000 個 P20 的進階作業集合。 如需更多集合數，請<a href="mailto:wapteams@microsoft.com">與我們連絡</a>。 | 存取排程器的完整功能集 |
|||||| 

## <a name="pricing"></a>定價

如需定價詳細資料，請參閱[排程器定價](https://azure.microsoft.com/pricing/details/scheduler/)。

## <a name="upgrade-or-downgrade-plans"></a>升級或降級方案

您可隨時在標準、P10 進階與 P20 進階方案之間，升級或降級作業集合方案。

## <a name="active-status-and-billing"></a>作用中狀態與計費

除非整個 Azure 訂用帳戶因為計費問題而進入暫時停用的狀態，否則，作業集合一律會是作用中。 雖然您可透過單一作業，停用作業集合內的所有作業，但此動作不會變更作業集合的計費狀態，因此該作業集合*仍然*會計費。 空白的作業集合會視為作用中，並將計費。

若要確保作業集合不會計費，您必須刪除該作業集合。

## <a name="standard-billable-units"></a>標準可計費單位

標準可計費單位，最多可有 10 個標準作業集合。 因為標準作業集合可以讓每個集合最多有 50 個作業，所以一個標準計費單位，可讓 Azure 訂用帳戶最多有 500 個作業，或最多每個月幾乎 *2 千 2 百萬*個作業執行。 這份清單說明您的計費方式，取決於各種數目的標準作業集合：

* 若您有 1 到 10 個標準作業集合，則需支付一個標準計費單位。 

* 若您有 11 到 20 個標準作業集合，則需支付兩個標準計費單位。 

* 若您有 21 到 30 個標準作業集合，則需支付三個標準計費單位，依此類推。

## <a name="p10-premium-billable-units"></a>P10 進階可計費單位

一個 P10 進階可計費單位，最多可有 10,000 個 P10 進階作業集合。 因為 P10 進階作業集合可以讓每個集合最多有 50 個作業，所以一個 P10 進階計費單位，可讓 Azure 訂用帳戶最多有 500,000 個作業，或最多每個月幾乎 *2 千 2 百萬*個作業執行。 

P10 進階作業集合提供與標準作業集合相同的功能，但對需要許多作業集合的應用程式，提供量大折扣，同時也有更佳的延展性。 這份清單說明您的計費方式，取決於不同數目的 P10 進階作業集合：

* 若您有 1 到 10,000 個 P10 進階作業集合，則需支付一個 P10 進階計費單位。 

* 若您有 10,001 到 20,000 個 P10 進階作業集合，則需支付兩個 P10 進階計費單位，依此類推。

## <a name="p20-premium-billable-units"></a>P20 進階可計費單位

一個 P20 進階可計費單位，最多可有 5,000 個 P20 進階作業集合。 因為 P20 進階作業集合可以讓每個作業集合最多有 1,000 個作業，所以一個 P20 進階計費單位，可讓 Azure 訂用帳戶最多有 5,000,000 個作業，或最多每個月幾乎 *2 千 2 百億*個作業執行。

P20 進階作業集合提供與 P10 進階作業集合相同的功能，但也對每個集合，支援更多數目的作業，且相較於 P10 進階，提供整體來說總數更多的作業，提供更佳的延展性。

## <a name="plan-comparison"></a>方案比較

* 若您有超過 100 個以上的標準作業集合 (10 個標準計費單位)，則將所有作業集合納入進階方案會是更好的選擇。

* 若您有一個標準作業集合以及一個進階作業集合，則您會支付一個標準計費單位*及*一個進階計費單位。

  排程器服務會根據標準或進階的作用中作業集合數目，加以計費。

## <a name="next-steps"></a>後續步驟

* [Azure 排程器概念、術語及實體階層](scheduler-concepts-terms.md)
* [Azure 排程器限制、預設值和錯誤碼](scheduler-limits-defaults-errors.md)