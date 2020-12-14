---
title: 包含檔案
description: 包含檔案
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 12/11/2020
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 0056e18b6cb3aad2a4504bbe20b3b3421793489e
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/12/2020
ms.locfileid: "97356257"
---
這些範例示範如何使用 Azure 監視器，針對為 Azure 已委派資源管理上線的訂用帳戶建立警示。

| **範本** | **說明** |
|---------|---------|
| [monitor-delegation-changes](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/tools/monitor-delegation-changes) | 查詢管理租用戶中過去 1 天的活動，並對[任何新增或移除的委派](../articles/lighthouse/how-to/monitor-delegation-changes.md) (或未成功的嘗試) 提出報告。|
| [alert-using-actiongroup](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/alert-using-actiongroup) | 此範本會建立 Azure 警示，並與現有的動作群組連線。|
| [multiple-loganalytics-alerts](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/multiple-loganalytics-alerts) | 建立多個以 Kusto 查詢為基礎的記錄警示。|
| [delegation-alert-for-customer](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegation-alert-for-customer) | 當使用者將訂用帳戶委派給管理租用戶時，在租用戶中部署警示。|
| [workbook-activitylogs-by-domain](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/workbook-activitylogs-by-domain) | 顯示訂用帳戶之間的 Azure 活動記錄，並有選項可讓您依網域名稱篩選這些記錄。 |
