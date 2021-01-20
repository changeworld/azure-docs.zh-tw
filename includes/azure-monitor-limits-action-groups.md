---
title: 包含檔案
description: 包含檔案
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 10/01/2020
ms.author: robb
ms.custom: include file
ms.openlocfilehash: 963e49ea0e5536be0fca6d565b439aef4a08793d
ms.sourcegitcommit: 65cef6e5d7c2827cf1194451c8f26a3458bc310a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/19/2021
ms.locfileid: "98605076"
---
| 資源 | 預設限制 | 上限 |
| --- | --- | --- |
| Azure 應用程式推送 | 每個動作群組有 10 個 Azure 應用程式動作。 | 與預設值相同 |
| 電子郵件 | 一個動作群組中有 1000 個電子郵件動作。<br>一小時內不超過 100 個電子郵件。<br>另請參閱[速率限制資訊](../articles/azure-monitor/platform/alerts-rate-limiting.md)。 | 與預設值相同 |
| ITSM | 一個動作群組中有 10 個 ITSM 動作。 | 與預設值相同 | 
| 邏輯應用程式 | 一個動作群組中有 10 個邏輯應用程式動作。 | 與預設值相同 |
| Runbook | 一個動作群組中有 10 個 Runbook 動作。 | 與預設值相同 |
| SMS | 一個動作群組中有 10 個 SMS 動作。<br>每 5 分鐘不超過 1 個 SMS 訊息。<br>另請參閱[速率限制資訊](../articles/azure-monitor/platform/alerts-rate-limiting.md)。 | 與預設值相同 |
| 語音 | 一個動作群組中有 10 個語音動作。<br>每 5 分鐘不超過 1 則語音電話。<br>另請參閱[速率限制資訊](../articles/azure-monitor/platform/alerts-rate-limiting.md)。 | 與預設值相同 |
| Webhook | 一個動作群組中有 10 個 Webhook 動作。  每個訂用帳戶的 Webhook 呼叫數目上限為每分鐘 1500 次。 其他限制可在[動作特定資訊](../articles/azure-monitor/platform/action-groups.md#action-specific-information)中取得。  | 與預設值相同 |
