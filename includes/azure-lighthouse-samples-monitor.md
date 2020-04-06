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
ms.openlocfilehash: aacf1c287dca01b5993d35b311eed22e88ef7e87
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2020
ms.locfileid: "80421146"
---
這些範例示範如何使用 Azure 監視器，針對為 Azure 已委派資源管理上線的訂用帳戶建立警示。

| **範本** | **說明** |
|---------|---------|
| [monitor-delegation-changes](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/tools/monitor-delegation-changes) | 查詢管理租用戶中過去 1 天的活動，並對[任何新增或移除的委派](../articles/lighthouse/how-to/monitor-delegation-changes.md) (或未成功的嘗試) 提出報告。|
| [alert-using-actiongroup](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/alert-using-actiongroup) | 此範本會建立 Azure 警示，並與現有的動作群組連線。|
| [multiple-loganalytics-alerts](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/multiple-loganalytics-alerts) | 建立多個以 Kusto 查詢為基礎的記錄警示。|
| [delegation-alert-for-customer](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/delegation-alert-for-customer) | 當使用者將訂用帳戶委派給管理租用戶時，在租用戶中部署警示。|
