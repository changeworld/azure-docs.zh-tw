---
title: 支援受控識別的 Azure 服務-Azure AD
description: 支援 Azure 資源和 Azure AD 驗證受控識別的服務清單
services: active-directory
author: MarkusVi
ms.author: markvi
ms.date: 04/09/2020
ms.topic: conceptual
ms.service: active-directory
ms.subservice: msi
manager: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45a248300cd61a0e57b9f35c8905d7c335069a27
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80991274"
---
# <a name="services-that-support-managed-identities-for-azure-resources"></a>支援適用於 Azure 資源的受控識別服務

Azure 資源受控識別會在 Azure Active Directory 中為 Azure 服務提供自動受控識別。 您可以使用受控身分識別，向任何支援 Azure AD 驗證的服務進行驗證，而不需要您程式碼中的任何認證。 我們正在努力對於 Azure 的 Azure 資源和 Azure AD 驗證整合受控識別。 請經常回來檢查更新。

> [!NOTE]
> 先前稱為「受控服務識別」(MSI) 的服務，其新名稱為「Azure 資源適用受控識別」。

## <a name="azure-services-that-support-managed-identities-for-azure-resources"></a>支援適用於 Azure 資源的受控識別 Azure 服務

下列 Azure 服務支援 Azure 資源的受控識別：

### <a name="azure-virtual-machines"></a>Azure 虛擬機器

| 受控識別類型 | 全部正式推出<br>全域 Azure 區域 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| 系統指派 | ![可用][check] | ![可用][check] | 預覽 | 預覽 | 
| 使用者指派 | ![可用][check] | ![可用][check] | 預覽 | 預覽 |

請參閱下列清單來設定 Azure 虛擬機器的受控識別 (若區域提供)：

- [Azure 入口網站](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Azure 資源管理員範本](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-virtual-machine-scale-sets"></a>Azure 虛擬機器擴展集

|受控識別類型 | 全部正式推出<br>全域 Azure 區域 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| 系統指派 | ![可用][check] | 預覽 | 預覽 | 預覽 |
| 使用者指派 | ![可用][check] | 預覽 | 預覽 | 預覽 |

請參閱下列清單來設定 Azure 虛擬機器擴展集的受控識別 (若區域提供)：

- [Azure 入口網站](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Azure 資源管理員範本](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-app-service"></a>Azure App Service

| 受控識別類型 | 全部正式推出<br>全域 Azure 區域 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| 系統指派 | ![可用][check] | ![可用][check] | ![可用][check] | ![可用][check] |
| 使用者指派 | ![可用][check] | ![可用][check]  | ![可用][check]  | ![可用][check] |

請參閱下列清單來設定 Azure App Service 的受控識別 (若區域提供)：

- [Azure 入口網站](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Azure CLI](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Azure Resource Manager 範本](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-blueprints"></a>Azure 藍圖

|受控識別類型 | 全部正式推出<br>全域 Azure 區域 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| 系統指派 | ![可用][check] | ![可用][check] | 無法使用 | 無法使用 |
| 使用者指派 | ![可用][check] | ![可用][check] | 無法使用 | 無法使用 |

請參閱下列清單以使用受控識別搭配[Azure 藍圖](../../governance/blueprints/overview.md)：

- [Azure 入口網站-藍圖指派](../../governance/blueprints/create-blueprint-portal.md#assign-a-blueprint)
- [REST API-藍圖指派](../../governance/blueprints/create-blueprint-rest-api.md#assign-a-blueprint)

### <a name="azure-functions"></a>Azure Functions

受控識別類型 |全部正式推出<br>全域 Azure 區域 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| 系統指派 | ![可用][check] | ![可用][check] | ![可用][check] | ![可用][check] |
| 使用者指派 | ![可用][check] | ![可用][check]  | ![可用][check]  | ![可用][check]  |

請參閱下列清單來設定 Azure Functions 的受控識別 (若區域提供)：

- [Azure 入口網站](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Azure CLI](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Azure Resource Manager 範本](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-logic-apps"></a>Azure Logic Apps

受控識別類型 | 全部正式推出<br>全域 Azure 區域 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| 系統指派 | ![可用][check] | ![可用][check] | 無法使用 | ![可用][check] |
| 使用者指派 | ![可用][check] | ![可用][check] | 無法使用 | ![可用][check] |


請參閱下列清單來設定 Azure Logic Apps 的受控識別 (若區域提供)：

- [Azure 入口網站](/azure/logic-apps/create-managed-service-identity#enable-system-assigned-identity-in-azure-portal)
- [Azure Resource Manager 範本](https://docs.microsoft.com/azure/logic-apps/logic-apps-azure-resource-manager-templates-overview)

### <a name="azure-data-factory-v2"></a>Azure Data Factory V2

受控識別類型 | 全部正式推出<br>全域 Azure 區域 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| 系統指派 | ![可用][check] | ![可用][check] | 無法使用 | ![可用][check] |
| 使用者指派 | 無法使用 | 無法使用 | 無法使用 | 無法使用 |

請參閱下列清單來設定 Azure Data Factory V2 的受控識別 (若區域提供)：

- [Azure 入口網站](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity)
- [PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-powershell)
- [REST](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-rest-api)
- [SDK](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-sdk)

### <a name="azure-api-management"></a>Azure API 管理

受控識別類型 | 全部正式推出<br>全域 Azure 區域 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| 系統指派 | ![可用][check] | ![可用][check] | 無法使用 | ![可用][check] |
| 使用者指派 | 預覽 | 預覽 | 無法使用 | 預覽 |

請參閱下列清單來設定 Azure APIM 的受控識別 (若區域提供)：

- [Azure Resource Manager 範本](/azure/api-management/api-management-howto-use-managed-service-identity)

### <a name="azure-container-instances"></a>Azure Container Instances

受控識別類型 | 全部正式推出<br>全域 Azure 區域 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| 系統指派 | Linux：預覽<br>Windows：無法使用 | 無法使用 | 無法使用 | 無法使用 |
| 使用者指派 | Linux：預覽<br>Windows：無法使用 | 無法使用 | 無法使用 | 無法使用 |

請參閱下列清單來設定 Azure 容器執行個體的受控識別 (若區域提供)：

- [Azure CLI](~/articles/container-instances/container-instances-managed-identity.md)
- [Azure Resource Manager 範本](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-resource-manager-template)
- [YAML](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-yaml-file)

### <a name="azure-container-registry-tasks"></a>Azure Container Registry 工作

受控識別類型 | 全部正式推出<br>全域 Azure 區域 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| 系統指派 | ![可用][check] | 無法使用 | 無法使用 | 無法使用 |
| 使用者指派 | 預覽 | 無法使用 | 無法使用 | 無法使用 |

請參閱下列清單以設定 Azure Container Registry 工作的受控識別（在可用區域中）：

- [Azure CLI](~/articles/container-registry/container-registry-tasks-authentication-managed-identity.md)

### <a name="azure-service-fabric"></a>Azure Service Fabric
適用于[Service Fabric 應用程式的受控識別](https://docs.microsoft.com/azure/service-fabric/concepts-managed-identity)處於預覽狀態，而且可在所有區域使用。

受控識別類型 | 全部正式推出<br>全域 Azure 區域 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| 系統指派 | ![可用][check] | 無法使用 | 無法使用 | 無法使用 |
| 使用者指派 | ![可用][check] | 無法使用 | 無法使用 |無法使用 |

請參閱下列清單，以在所有區域中設定 Azure Service Fabric 應用程式的受控識別：
- [Azure Resource Manager 範本](https://github.com/Azure-Samples/service-fabric-managed-identity/tree/anmenard-docs)

## <a name="azure-services-that-support-azure-ad-authentication"></a>支援 Azure AD 驗證的 Azure 服務

下列服務支援 Azure AD 驗證，並已經過使用 Azure 資源受控識別的用戶端服務進行的測試。

### <a name="azure-resource-manager"></a>Azure Resource Manager

請參閱下列清單，以設定 Azure Resource Manager 的存取權：

- [透過 Azure 入口網站指派存取權](howto-assign-access-portal.md)
- [透過 PowerShell 指派存取權](howto-assign-access-powershell.md)
- [透過 Azure CLI 指派存取權](howto-assign-access-CLI.md)
- [透過 Azure Resource Manager 範本指派存取權](../../role-based-access-control/role-assignments-template.md)

| 雲端 | 資源識別碼 | 狀態 |
|--------|------------|:-:|
| Azure 全域 | `https://management.azure.com/`| ![可用][check] |
| Azure Government | `https://management.usgovcloudapi.net/` | ![可用][check] |
| Azure Germany | `https://management.microsoftazure.de/` | ![可用][check] |
| Azure China 21Vianet | `https://management.chinacloudapi.cn` | ![可用][check] |

### <a name="azure-key-vault"></a>Azure 金鑰保存庫

| 雲端 | 資源識別碼 | 狀態 |
|--------|------------|:-:|
| Azure 全域 | `https://vault.azure.net`| ![可用][check] |
| Azure Government | `https://vault.usgovcloudapi.net` | ![可用][check] |
| Azure Germany |  `https://vault.microsoftazure.de` | ![可用][check] |
| Azure China 21Vianet | `https://vault.azure.cn` | ![可用][check] |

### <a name="azure-data-lake"></a>Azure Data Lake 

| 雲端 | 資源識別碼 | 狀態 |
|--------|------------|:-:|
| Azure 全域 | `https://datalake.azure.net/` | ![可用][check] |
| Azure Government |  | 無法使用 |
| Azure Germany |   | 無法使用 |
| Azure China 21Vianet |  | 無法使用 |

### <a name="azure-sql"></a>Azure SQL 

| 雲端 | 資源識別碼 | 狀態 |
|--------|------------|:-:|
| Azure 全域 | `https://database.windows.net/` | ![可用][check] |
| Azure Government | `https://database.usgovcloudapi.net/` | ![可用][check] |
| Azure Germany | `https://database.cloudapi.de/` | ![可用][check] |
| Azure China 21Vianet | `https://database.chinacloudapi.cn/` | ![可用][check] |

### <a name="azure-event-hubs"></a>Azure 事件中心

| 雲端 | 資源識別碼 | 狀態 |
|--------|------------|:-:|
| Azure 全域 | `https://eventhubs.azure.net` | ![可用][check] |
| Azure Government |  | 無法使用 |
| Azure Germany |   | 無法使用 |
| Azure China 21Vianet |  | 無法使用 |

### <a name="azure-service-bus"></a>Azure 服務匯流排

| 雲端 | 資源識別碼 | 狀態 |
|--------|------------|:-:|
| Azure 全域 | `https://servicebus.azure.net`  | ![可用][check] |
| Azure Government |  | ![可用][check] |
| Azure Germany |   | 無法使用 |
| Azure China 21Vianet |  | 無法使用 |









### <a name="azure-storage-blobs-and-queues"></a>Azure 儲存體 blob 和佇列

| 雲端 | 資源識別碼 | 狀態 |
|--------|------------|:-:|
| Azure 全域 | `https://storage.azure.com/` <br /><br />`https://<account>.blob.core.windows.net` <br /><br />`https://<account>.queue.core.windows.net` | ![可用][check] |
| Azure Government | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.usgovcloudapi.net` <br /><br />`https://<account>.queue.core.usgovcloudapi.net` | ![可用][check] |
| Azure Germany | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.cloudapi.de` <br /><br />`https://<account>.queue.core.cloudapi.de` | ![可用][check] |
| Azure China 21Vianet | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.chinacloudapi.cn` <br /><br />`https://<account>.queue.core.chinacloudapi.cn` | ![可用][check] |










### <a name="azure-analysis-services"></a>Azure Analysis Services

| 雲端 | 資源識別碼 | 狀態 |
|--------|------------|:-:|
| Azure 全域 | `https://*.asazure.windows.net` | ![可用][check] |
| Azure Government | `https://*.asazure.usgovcloudapi.net` | ![可用][check] |
| Azure Germany | `https://*.asazure.cloudapi.de` | ![可用][check] |
| Azure China 21Vianet | `https://*.asazure.chinacloudapi.cn` | ![可用][check] |

> [!Note]
> Microsoft Power BI 也[支援受控](https://docs.microsoft.com/azure/stream-analytics/powerbi-output-managed-identity)識別。


[check]: media/services-support-managed-identities/check.png "只有"
