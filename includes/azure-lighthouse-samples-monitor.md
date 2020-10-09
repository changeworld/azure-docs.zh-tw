---
title: 包含檔案
description: 包含檔案
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 03/30/2020
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: be8aae6308e712449402b002576974743bc125ef
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "80986741"
---
這些範例示範如何使用 Azure 監視器，針對為 Azure 已委派資源管理上線的訂用帳戶建立警示。

| **範本** | **說明** |
|---------|---------|
| [monitor-delegation-changes](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/tools/monitor-delegation-changes) | 查詢管理租用戶中過去 1 天的活動，並對[任何新增或移除的委派](../articles/lighthouse/how-to/monitor-delegation-changes.md) (或未成功的嘗試) 提出報告。|
| [alert-using-actiongroup](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/alert-using-actiongroup) | 此範本會建立 Azure 警示，並與現有的動作群組連線。|
| [multiple-loganalytics-alerts](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/multiple-loganalytics-alerts) | 建立多個以 Kusto 查詢為基礎的記錄警示。|
| [delegation-alert-for-customer](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegation-alert-for-customer) | 當使用者將訂用帳戶委派給管理租用戶時，在租用戶中部署警示。|
