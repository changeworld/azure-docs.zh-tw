---
title: 支援受控識別的 Azure 服務 - Azure AD
description: 支援 Azure 資源和 Azure AD 驗證受控識別的服務清單
services: active-directory
author: barclayn
ms.author: barclayn
ms.date: 10/07/2020
ms.topic: conceptual
ms.service: active-directory
ms.subservice: msi
manager: daveba
ms.collection: M365-identity-device-management
ms.custom: references_regions
ms.openlocfilehash: e875eda2a59841d3238a13389768c6fa37cef774
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91843170"
---
# <a name="services-that-support-managed-identities-for-azure-resources"></a>支援適用於 Azure 資源的受控識別服務

Azure 資源受控識別會在 Azure Active Directory 中為 Azure 服務提供自動受控識別。 您可以使用受控身分識別，向任何支援 Azure AD 驗證的服務進行驗證，而不需要您程式碼中的任何認證。 我們正在努力對於 Azure 的 Azure 資源和 Azure AD 驗證整合受控識別。 請經常回來檢查更新。

> [!NOTE]
> 先前稱為「受控服務識別」(MSI) 的服務，其新名稱為「Azure 資源適用受控識別」。

## <a name="azure-services-that-support-managed-identities-for-azure-resources"></a>支援適用於 Azure 資源的受控識別 Azure 服務

下列 Azure 服務支援 Azure 資源的受控識別：


### <a name="azure-api-management"></a>Azure API 管理

受控識別類型 | 全部正式推出<br>全域 Azure 區域 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| 系統指派 | ![可用][check] | ![可用][check] | 無法使用 | ![可用][check] |
| 使用者指派 | 預覽 | 預覽 | 無法使用 | 預覽 |

請參閱下列清單來設定 Azure APIM 的受控識別 (若區域提供)：

- [Azure Resource Manager 範本](../../api-management/api-management-howto-use-managed-service-identity.md)


### <a name="azure-app-service"></a>Azure App Service

| 受控識別類型 | 全部正式推出<br>全域 Azure 區域 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| 系統指派 | ![可用][check] | ![可用][check] | ![可用][check] | ![可用][check] |
| 使用者指派 | ![可用][check] | ![可用][check]  | ![可用][check]  | ![可用][check] |

請參閱下列清單來設定 Azure App Service 的受控識別 (若區域提供)：

- [Azure 入口網站](../../app-service/overview-managed-identity.md#using-the-azure-portal)
- [Azure CLI](../../app-service/overview-managed-identity.md#using-the-azure-cli)
- [Azure PowerShell](../../app-service/overview-managed-identity.md#using-azure-powershell)
- [Azure Resource Manager 範本](../../app-service/overview-managed-identity.md#using-an-azure-resource-manager-template)

### <a name="azure-arc-enabled-kubernetes"></a>已啟用 Azure Arc 的 Kubernetes

| 受控識別類型 | 全部正式推出<br>全域 Azure 區域 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| 系統指派 | 預覽 | 無法使用 | 無法使用 | 無法使用 | 
| 使用者指派 | 無法使用 | 無法使用 | 無法使用 | 無法使用 |

Azure Arc 啟用的 Kubernetes 目前 [支援系統指派](../../azure-arc/kubernetes/connect-cluster.md#azure-arc-agents-for-kubernetes)的身分識別。 所有 Azure Arc 啟用的 Kubernetes 代理程式都會使用受控服務識別憑證來與 Azure 進行通訊。

### <a name="azure-blueprints"></a>Azure 藍圖

|受控識別類型 | 全部正式推出<br>全域 Azure 區域 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| 系統指派 | ![可用][check] | ![可用][check] | 無法使用 | 無法使用 |
| 使用者指派 | ![可用][check] | ![可用][check] | 無法使用 | 無法使用 |

請參閱下列清單，以搭配 [Azure 藍圖](../../governance/blueprints/overview.md)使用受控識別：

- [Azure 入口網站 - 藍圖指派](../../governance/blueprints/create-blueprint-portal.md#assign-a-blueprint)
- [REST API - 藍圖指派](../../governance/blueprints/create-blueprint-rest-api.md#assign-a-blueprint)


### <a name="azure-cognitive-search"></a>Azue 認知搜尋

受控識別類型 | 全部正式推出<br>全域 Azure 區域 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| 系統指派 | ![可用][check] | ![可用][check] | 無法使用 | ![可用][check] |
| 使用者指派 | 無法使用 | 無法使用 | 無法使用 | 無法使用 |

### <a name="azure-cognitive-services"></a>Azure 認知服務

受控識別類型 | 全部正式推出<br>全域 Azure 區域 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| 系統指派 | ![可用][check] | ![可用][check] | 無法使用 | ![可用][check] |
| 使用者指派 | 無法使用 | 無法使用 | 無法使用 | 無法使用 |


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

請參閱下列清單來設定 Azure Container Registry 工作的受控識別 (若區域提供)：

- [Azure CLI](~/articles/container-registry/container-registry-tasks-authentication-managed-identity.md)

### <a name="azure-data-explorer"></a>Azure 資料總管

受控識別類型 | 全部正式推出<br>全域 Azure 區域 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| 系統指派 | ![可用][check] | ![可用][check] | 無法使用 | ![可用][check] |
| 使用者指派 | 無法使用 | 無法使用 | 無法使用 | 無法使用 |

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



### <a name="azure-event-grid"></a>Azure Event Grid 

受控識別類型 |全部正式推出<br>全域 Azure 區域 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| 系統指派 | 預覽 | 預覽 | 無法使用 | 預覽 |
| 使用者指派 | 無法使用 | 無法使用  | 無法使用  | 無法使用 |









### <a name="azure-functions"></a>Azure Functions

受控識別類型 |全部正式推出<br>全域 Azure 區域 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| 系統指派 | ![可用][check] | ![可用][check] | ![可用][check] | ![可用][check] |
| 使用者指派 | ![可用][check] | ![可用][check]  | ![可用][check]  | ![可用][check]  |

請參閱下列清單來設定 Azure Functions 的受控識別 (若區域提供)：

- [Azure 入口網站](../../app-service/overview-managed-identity.md#using-the-azure-portal)
- [Azure CLI](../../app-service/overview-managed-identity.md#using-the-azure-cli)
- [Azure PowerShell](../../app-service/overview-managed-identity.md#using-azure-powershell)
- [Azure Resource Manager 範本](../../app-service/overview-managed-identity.md#using-an-azure-resource-manager-template)

### <a name="azure-iot-hub"></a>Azure IoT 中樞

受控識別類型 | 全部正式推出<br>全域 Azure 區域 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| 系統指派 | ![可用][check] | ![可用][check] | 無法使用 | ![可用][check] |
| 使用者指派 | 無法使用 | 無法使用 | 無法使用 | 無法使用 |

請參閱下列清單來設定 Azure Data Factory V2 的受控識別 (若區域提供)：

- [Azure 入口網站](../../iot-hub/virtual-network-support.md#turn-on-managed-identity-for-iot-hub)

### <a name="azure-importexport"></a>Azure 匯入/匯出

受控識別類型 | 全部正式推出<br>全域 Azure 區域 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| 系統指派 | 可在 Azure 匯入匯出服務的可用區域中使用 | 預覽 | 可用 | 可用 |
| 使用者指派 | 無法使用 | 無法使用 | 無法使用 | 無法使用 |

### <a name="azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS)

| 受控識別類型 | 全部正式推出<br>全域 Azure 區域 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| 系統指派 | ![可用][check] | ![可用][check] | 無法使用 | ![可用][check] | 
| 使用者指派 | ![可用][check] | ![可用][check] | 無法使用 | ![可用][check] |


如需詳細資訊，請參閱[在 Azure Kubernetes Service 中使用受控識別](../../aks/use-managed-identity.md) \(部分機器翻譯\)。


### <a name="azure-logic-apps"></a>Azure Logic Apps

受控識別類型 | 全部正式推出<br>全域 Azure 區域 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| 系統指派 | ![可用][check] | ![可用][check] | 無法使用 | ![可用][check] |
| 使用者指派 | ![可用][check] | ![可用][check] | 無法使用 | ![可用][check] |


請參閱下列清單來設定 Azure Logic Apps 的受控識別 (若區域提供)：

- [Azure 入口網站](../../logic-apps/create-managed-service-identity.md#enable-system-assigned-identity-in-azure-portal)
- [Azure Resource Manager 範本](../../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)


### <a name="azure-policy"></a>Azure 原則

|受控識別類型 | 全部正式推出<br>全域 Azure 區域 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| 系統指派 | ![可用][check] | ![可用][check] | ![可用][check] | ![可用][check] |
| 使用者指派 | 無法使用 | 無法使用 | 無法使用 | 無法使用 |

請參閱下列清單，以在可用) 的區域中設定 Azure 原則 (的受控識別：

- [Azure 入口網站](../../governance/policy/tutorials/create-and-manage.md#assign-a-policy)
- [PowerShell](../../governance/policy/how-to/remediate-resources.md#create-managed-identity-with-powershell)
- [Azure CLI](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-create)
- [Azure 資源管理員範本](/azure/templates/microsoft.authorization/policyassignments)
- [REST](/rest/api/resources/policyassignments/create)


### <a name="azure-service-fabric"></a>Azure Service Fabric

適用于[Service Fabric 應用程式的受控識別](../../service-fabric/concepts-managed-identity.md)可在所有區域中使用。

受控識別類型 | 全部正式推出<br>全域 Azure 區域 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| 系統指派 | ![可用][check] | 無法使用 | 無法使用 | 無法使用 |
| 使用者指派 | ![可用][check] | 無法使用 | 無法使用 |無法使用 |

請參閱下列清單來在所有區域設定 Azure Service Fabric 應用程式的受控識別：

- [Azure Resource Manager 範本](https://github.com/Azure-Samples/service-fabric-managed-identity/tree/anmenard-docs)

### <a name="azure-spring-cloud"></a>Azure Spring Cloud

| 受控識別類型 | 全部正式推出<br>全域 Azure 區域 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| 系統指派 | ![可用][check] | 無法使用 | 無法使用 | 無法使用 | 
| 使用者指派 | 無法使用 | 無法使用 | 無法使用 | 無法使用 |


如需詳細資訊，請參閱 [如何為 Azure 春季 Cloud 應用程式啟用系統指派的受控識別](~/articles/spring-cloud/spring-cloud-howto-enable-system-assigned-managed-identity.md)。

### <a name="azure-stack-edge"></a>Azure Stack Edge

受控識別類型 | 全部正式推出<br>全域 Azure 區域 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| 系統指派 | 可在 Azure Stack Edge 服務可用的區域中使用 | 無法使用 | 無法使用 | 無法使用 |
| 使用者指派 | 無法使用 | 無法使用 | 無法使用 | 無法使用 |

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
- [Azure SDK](qs-configure-sdk-windows-vm.md)


### <a name="azure-vm-image-builder"></a>Azure VM 映射產生器

| 受控識別類型 | 全部正式推出<br>全域 Azure 區域 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| 系統指派 | 無法使用 | 無法使用 | 無法使用 | 無法使用 | 
| 使用者指派 | [可在支援的區域中使用](../../virtual-machines/windows/image-builder-overview.md#regions) | 無法使用 | 無法使用 | 無法使用 |

若要瞭解如何為 Azure VM 映射產生器設定受控識別 (在可用) 的區域中，請參閱影像產生器 [總覽](../../virtual-machines/windows/image-builder-overview.md#permissions)。
### <a name="azure-signalr-service"></a>Azure SignalR 服務

受控識別類型 | 全部正式推出<br>全域 Azure 區域 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| 系統指派 | 預覽 | 預覽 | 無法使用 | 預覽 |
| 使用者指派 | 預覽 | 預覽 | 無法使用 | 預覽 |

請參閱下列清單，以在可用) 的區域中設定 Azure SignalR Service (的受控識別：

- [Azure Resource Manager 範本](../../azure-signalr/howto-use-managed-identity.md)

## <a name="azure-services-that-support-azure-ad-authentication"></a>支援 Azure AD 驗證的 Azure 服務

下列服務支援 Azure AD 驗證，並已經過使用 Azure 資源受控識別的用戶端服務進行的測試。

### <a name="azure-resource-manager"></a>Azure Resource Manager

請參閱下列清單，以設定 Azure Resource Manager 的存取權：

- [透過 Azure 入口網站指派存取權](howto-assign-access-portal.md)
- [透過 PowerShell 指派存取權](howto-assign-access-powershell.md)
- [透過 Azure CLI 指派存取權](howto-assign-access-CLI.md)
- [透過 Azure Resource Manager 範本指派存取權](../../role-based-access-control/role-assignments-template.md)

| Cloud | 資源識別碼 | 狀態 |
|--------|------------|:-:|
| Azure 全域 | `https://management.azure.com/`| ![可用][check] |
| Azure Government | `https://management.usgovcloudapi.net/` | ![可用][check] |
| Azure Germany | `https://management.microsoftazure.de/` | ![可用][check] |
| Azure China 21Vianet | `https://management.chinacloudapi.cn` | ![可用][check] |

### <a name="azure-key-vault"></a>Azure 金鑰保存庫

| Cloud | 資源識別碼 | 狀態 |
|--------|------------|:-:|
| Azure 全域 | `https://vault.azure.net`| ![可用][check] |
| Azure Government | `https://vault.usgovcloudapi.net` | ![可用][check] |
| Azure Germany |  `https://vault.microsoftazure.de` | ![可用][check] |
| Azure China 21Vianet | `https://vault.azure.cn` | ![可用][check] |

### <a name="azure-data-lake"></a>Azure Data Lake

| Cloud | 資源識別碼 | 狀態 |
|--------|------------|:-:|
| Azure 全域 | `https://datalake.azure.net/` | ![可用][check] |
| Azure Government |  | 無法使用 |
| Azure Germany |   | 無法使用 |
| Azure China 21Vianet |  | 無法使用 |

### <a name="azure-sql"></a>Azure SQL

| Cloud | 資源識別碼 | 狀態 |
|--------|------------|:-:|
| Azure 全域 | `https://database.windows.net/` | ![可用][check] |
| Azure Government | `https://database.usgovcloudapi.net/` | ![可用][check] |
| Azure Germany | `https://database.cloudapi.de/` | ![可用][check] |
| Azure China 21Vianet | `https://database.chinacloudapi.cn/` | ![可用][check] |

### <a name="azure-event-hubs"></a>Azure 事件中心

| Cloud | 資源識別碼 | 狀態 |
|--------|------------|:-:|
| Azure 全域 | `https://eventhubs.azure.net` | ![可用][check] |
| Azure Government |  | 無法使用 |
| Azure Germany |   | 無法使用 |
| Azure China 21Vianet |  | 無法使用 |

### <a name="azure-service-bus"></a>Azure 服務匯流排

| Cloud | 資源識別碼 | 狀態 |
|--------|------------|:-:|
| Azure 全域 | `https://servicebus.azure.net`  | ![可用][check] |
| Azure Government |  | ![可用][check] |
| Azure Germany |   | 無法使用 |
| Azure China 21Vianet |  | 無法使用 |









### <a name="azure-storage-blobs-and-queues"></a>Azure 儲存體 Blob 和佇列

| Cloud | 資源識別碼 | 狀態 |
|--------|------------|:-:|
| Azure 全域 | `https://storage.azure.com/` <br /><br />`https://<account>.blob.core.windows.net` <br /><br />`https://<account>.queue.core.windows.net` | ![可用][check] |
| Azure Government | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.usgovcloudapi.net` <br /><br />`https://<account>.queue.core.usgovcloudapi.net` | ![可用][check] |
| Azure Germany | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.cloudapi.de` <br /><br />`https://<account>.queue.core.cloudapi.de` | ![可用][check] |
| Azure China 21Vianet | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.chinacloudapi.cn` <br /><br />`https://<account>.queue.core.chinacloudapi.cn` | ![可用][check] |

### <a name="azure-analysis-services"></a>Azure Analysis Services

| Cloud | 資源識別碼 | 狀態 |
|--------|------------|:-:|
| Azure 全域 | `https://*.asazure.windows.net` | ![可用][check] |
| Azure Government | `https://*.asazure.usgovcloudapi.net` | ![可用][check] |
| Azure Germany | `https://*.asazure.cloudapi.de` | ![可用][check] |
| Azure China 21Vianet | `https://*.asazure.chinacloudapi.cn` | ![可用][check] |

> [!Note]
> Microsoft Power BI 也[支援受控識別](../../stream-analytics/powerbi-output-managed-identity.md) \(部分機器翻譯\)。


[check]: media/services-support-managed-identities/check.png "可用"
