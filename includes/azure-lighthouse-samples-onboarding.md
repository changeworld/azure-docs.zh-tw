---
title: 包含檔案
description: 包含檔案
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 12/19/2019
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: e32b55ed655b1e47f85640eb7f494a89f3274667
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/25/2020
ms.locfileid: "75456798"
---
我們提供不同的範本來解決特定的上線案例。 選擇最適合的選項，並務必修改參數檔案以反映您的環境。 如需如何在部署中使用這些檔案的詳細資訊，請參閱[讓客戶在 Azure 委派的資源管理中上線](../articles/lighthouse/how-to/onboard-customer.md)。

| **範本** | **說明** |
|---------|---------|
| [delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management) | 讓客戶的訂用帳戶在 Azure 委派的資源管理中上線。 您必須針對每個訂用帳戶個別執行部署。 |
| [rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management) | 讓一或多個客戶的資源群組在 Azure 委派的資源管理中上線。 單一資源群組請使用 **rgDelegatedResourceManagement**，或使用 **multipleRgDelegatedResourceManagement** 將相同訂用帳戶中的多個資源群組上線。 |
| [marketplace-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management) | 如果您已[將受控服務發佈到 Azure Marketplace](../articles/lighthouse/how-to/publish-managed-services-offers.md)，可以選擇性地使用此範本來為已接受供應項目的客戶將資源上線。 參數檔案中的 marketplace 值必須符合您在發佈供應項目時所使用的值。 |

一般來說，每個要上線的訂用帳戶都需要個別部署，但您也可以跨多個訂用帳戶部署範本。

| **範本** | **說明** |
|---------|---------|
| [cross-subscription-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/cross-subscription-deployment) | 跨多個訂用帳戶部署 Azure Resource Manager 範本。 |
