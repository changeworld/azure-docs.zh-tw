---
title: 包含檔案
description: 包含檔案
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 04/24/2020
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 4a6d9ef04a33c84d68dff1429fb39a193a249280
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82204605"
---
這些範例說明可在跨租使用者管理案例中執行的各種工作。

| **範本** | **說明** |
|---------|---------|
| [create-keyvault-secret](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/create-keyvault-secret) | 在客戶的租用戶中建立金鑰保存庫並建立存取原則。
| [cross-rg-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/cross-rg-deployment) | 將儲存體帳戶部署到兩個不同的資源群組中。|
| [deploy-azure-mgmt-services](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/deploy-azure-mgmt-services) | 建立 Azure 管理服務、將其連結在一起並部署其他解決方案。 針對端對端部署，請使用 **rgWithAzureMgmt.json** 範本。 |
| [deploy-azure-security-center](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/deploy-azure-security-center) | 在目標 Azure 訂用帳戶中啟用和設定 Azure 資訊安全中心。 |
| [deploy-azure-sentinel](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/deploy-azure-sentinel) | 在委派訂用帳戶中的現有 Log Analytics 工作區上部署並啟用 Azure Sentinel。 |
| [deploy-log-analytics-vm-extensions](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/deploy-log-analytics-vm-extensions) | 這些範本可讓您將 Log Analytics VM 擴充功能部署到您的 Windows 和 Linux VM，並與指定的 Log Analytics 工作區連線 |
