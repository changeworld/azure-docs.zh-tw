---
title: 智慧型偵測通知變更-Azure 應用程式見解
description: 從智慧型偵測變更為預設通知收件者。 智慧型偵測可讓您在追蹤遙測中使用 Azure 應用程式深入解析的不尋常模式來監視應用程式追蹤。
ms.topic: conceptual
author: harelbr
ms.author: harelbr
ms.date: 03/13/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 8e2bf4e451ebc3c9ebba2c01dae6703fc79aa606
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87324126"
---
# <a name="smart-detection-e-mail-notification-change"></a>智慧偵測電子郵件通知變更

基於客戶的意見反應，將於 2019 年 4 月 1 日變更接收智慧偵測服務電子郵件通知的預設角色。

## <a name="what-is-changing"></a>變更內容為何？

智慧偵測電子郵件通知目前預設傳送至訂用帳戶擁有者__、訂用帳戶參與者__ 和訂用帳戶讀者__ 角色。 這類角色中常含有並非主動參與監視的使用者，而導致許多使用者收到不必要的通知。 為了改善使用體驗，即將進行變更，僅將電子郵件通知寄給[監視讀者](../../role-based-access-control/built-in-roles.md#monitoring-reader)和[監視參與者](../../role-based-access-control/built-in-roles.md#monitoring-contributor)的預設角色。

## <a name="scope-of-this-change"></a>變更範圍

本次變更會影響所有的智慧偵測規則，但不包括下列項目：

* 標示為預覽版的智慧偵測規則。 這類智慧偵測規則目前不支援電子郵件通知功能。

* 失敗異常規則。 此規則一旦從傳統警示功能移轉至整合警示平台，就會開始以新的預設角色為目標 (如需詳細資訊，請至[此處](../platform/monitoring-classic-retirement.md)。)

## <a name="how-to-prepare-for-this-change"></a>如何針對這項變更做準備？

為了確保來自智慧型偵測的電子郵件通知會傳送給相關使用者，必須將這些使用者指派給訂用帳戶的 [監視讀取](../../role-based-access-control/built-in-roles.md#monitoring-reader) 者或 [監視參與者](../../role-based-access-control/built-in-roles.md#monitoring-contributor) 角色。

若要透過 Azure 入口網站指派使用者為監視讀者或監視參與者，請按照[新增角色指派](../../role-based-access-control/role-assignments-portal.md#add-a-role-assignment)文章所述的步驟進行。 請務必選取監視讀者__ 或監視參與者__ 做為使用者指派的角色。

> [!NOTE]
> 若使用規則設定中的 [新增電子郵件收件者] __ 選項來設定智慧偵測通知的特定收件者，則不會受到這項變更影響。 這些收件者會繼續接收到電子郵件通知。

如果您對這項變更有任何問題或疑慮，請盡速[與我們連絡](mailto:smart-alert-feedback@microsoft.com)。

## <a name="next-steps"></a>接下來的步驟

深入了解智慧偵測：

- [失敗的異常](./proactive-failure-diagnostics.md)
- [記憶體流失](./proactive-potential-memory-leak.md)
- [效能異常](./proactive-performance-diagnostics.md)

