---
title: Azure Resource Manager 範本範例
description: 尋找部分常見 App Service 案例的 Azure Resource Manager 範本範例。 了解如何將 App Service 部署或管理工作自動化。
author: tfitzmac
tags: azure-service-management
ms.topic: sample
ms.date: 01/04/2019
ms.author: tomfitz
ms.custom: mvc, fasttrack-edit
ms.openlocfilehash: 2caaadd0da9d62128d04962fa1f2ff7eade907b0
ms.sourcegitcommit: bf99428d2562a70f42b5a04021dde6ef26c3ec3a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/23/2020
ms.locfileid: "85254424"
---
# <a name="azure-resource-manager-templates-for-app-service"></a>Azure Resource Manager 的 App Service 範本

下表包含 Azure Resource Manager 的 Azure App Service 範本連結。 如需有關在建立應用程式範本時避免發生常見錯誤的建議，請參閱[使用 Azure Resource Manager 範本來部署應用程式的指引](deploy-resource-manager-template.md)。

若要深入了解 App Service 資源的 JSON 語法和屬性，請參閱 [Microsoft.Web 資源類型](/azure/templates/microsoft.web/allversions)。

| | |
|-|-|
|**部署應用程式**||
| [App Service 方案和基本 Linux 應用程式](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-basic-linux) \(英文\) | 部署針對 Linux 設定的 App Service 應用程式。 |
| [App Service 方案和基本 Windows 應用程式](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-basic-windows) \(英文\) | 部署針對 Windows 設定的 App Service 應用程式。 |
| [連結至 GitHub 存放庫的應用程式](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-github-deploy) \(英文\)| 部署可從 GitHub 提取程式碼的 App Service 應用程式。 |
| [具有自訂部署位置的應用程式](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-custom-deployment-slots) \(英文\)| 部署具有自訂部署位置/環境的 App Service 應用程式。 |
|**設定應用程式**||
| [來自金鑰保存庫的應用程式憑證](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-certificate-from-key-vault) \(英文\)| 部署來自 Azure Key Vault 祕密的 App Service 應用程式憑證，並使用它來進行 TLS/SSL 繫結。 |
| [具有自訂網域和 SSL 的應用程式](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain-and-ssl) \(英文\)| 部署具有自訂主機名稱的 App Service 應用程式，並從 Key Vault 取得應用程式憑證來進行 TLS/SSL 繫結。 |
| [具有 GoLang 延伸模組的應用程式](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-with-golang) \(英文\)| 部署具有 Golang 網站延伸模組的 App Service 應用程式。 然後，您可以在 Azure 上執行以 Golang 開發的 Web 應用程式。 |
| [採用 Java 8 和 Tomcat 8 的應用程式](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-java-tomcat) \(英文\)| 部署已啟用 Java 8 和 Tomcat 8 的 App Service 應用程式。 然後，您可以在 Azure 中執行 Java 應用程式。 |
| [應用程式與區域 VNet 整合](https://github.com/Azure/azure-quickstart-templates/tree/master/101-app-service-regional-vnet-integration)| 部署已啟用區域 VNet 整合的 App Service 應用程式。 |
|**保護應用程式**||
| [與 Azure 應用程式閘道整合的應用程式](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-app-gateway-v2)| 部署 App Service 應用程式和應用程式閘道，並使用服務端點和存取限制來隔離流量。 |
|**具有已連線資源的 Linux 應用程式**||
| [在採用 MySQL 的 Linux 上部署應用程式](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-mysql) \(英文\) | 在具有「適用於 MySQL 的 Azure 資料庫」的 Linux 上部署 App Service 應用程式。 |
| [在採用 PostgreSQL 的 Linux 上部署應用程式](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-postgresql) \(英文\) | 在具有「適用於 PostgreSQL 的 Azure 資料庫」的 Linux 上部署 App Service 應用程式。 |
|**具有已連線資源的應用程式**||
| [搭配 MySQL 的應用程式](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-mysql) \(英文\)| 在具有「適用於 MySQL 的 Azure 資料庫」的 Windows 上部署 App Service 應用程式。 |
| [搭配 PostgreSQL 的應用程式](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-postgresql) \(英文\)| 在具有「適用於 PostgreSQL 的 Azure 資料庫」的 Windows 上部署 App Service 應用程式。 |
| [在 Azure SQL Database 中具有資料庫的應用程式](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database)| 在「基本」服務層級部署 App Service 應用程式與 Azure SQL Database 中的資料庫。 |
| [具有 Blob 儲存體連線的應用程式](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-blob-connection) \(英文\)| 使用 Azure Blob 儲存體連接字串來部署 App Service 應用程式。 如此，您便可以從該應用程式使用 Blob 儲存體。 |
| [具有 Azure Redis 快取的應用程式](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-redis-cache) \(英文\)| 部署具有「Azure Redis 快取」的 App Service 應用程式。 |
|**App Service 環境**||
| [建立 App Service Environment v2](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-create) | 在虛擬網路中建立 App Service Environment v2。 |
| [建立具有 ILB 位址的 App Service Environment v2](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-ilb-create/) | 在虛擬網路中建立具有私人內部負載平衡位址的 App Service Environment v2。 |
| [設定 ILB App Service Environment 或 ILB App Service Environment v2 的預設 SSL 憑證](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-ase-ilb-configure-default-ssl) | 設定 ILB App Service Environment 或 ILB App Service Environment v2 的預設 TLS/SSL 憑證。 |
| | |
