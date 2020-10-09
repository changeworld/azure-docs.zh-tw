---
title: ISO 27001 ASE/SQL 工作負載藍圖範例概觀
description: ISO 27001 App Service 環境/SQL Database 工作負載藍圖範例的概觀和架構。
ms.date: 07/13/2020
ms.topic: sample
ms.openlocfilehash: 76177efcac8b32907c60cecac41404a3834d0fb8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87926088"
---
# <a name="overview-of-the-iso-27001-app-service-environmentsql-database-workload-blueprint-sample"></a>ISO 27001 App Service 環境/SQL Database 工作負載藍圖範例概觀

ISO 27001 App Service 環境/SQL Database 工作負載藍圖範例可為 [ISO 27001 共用服務](../iso27001-shared/index.md)藍圖範例提供額外的基礎結構。
此藍圖可協助客戶部署雲端式架構，進而將解決方案提供給有認證或合規性需求的案例。

ISO 27001 藍圖範例有兩個，此範例和 [ISO 27001 共用服務](../iso27001-shared/index.md)藍圖範例。

> [!IMPORTANT]
> 此範例相依於 [ISO 27001 共用服務](../iso27001-shared/index.md)藍圖範例所部署的基礎結構。 必須先部署該基礎結構。

## <a name="architecture"></a>架構

ISO 27001 App Service 環境/SQL Database 工作負載藍圖範例會部署平台即服務類型的 Web 環境。 該環境可用來裝載多個遵循 ISO 27001 標準的 Web 應用程式、Web API 及 SQL Database 執行個體。 此藍圖範例相依於 [ISO 27001 共用服務](../iso27001-shared/index.md)藍圖範例。

:::image type="content" source="../../media/sample-iso27001-ase-sql-workload/iso27001-ase-sql-workload-blueprint-sample-design.png" alt-text="ISO 27001 ASE/SQL 工作負載藍圖範例設計" border="false":::

此環境包含數個 Azure 服務，用來提供以 ISO 27001 標準為基礎且完全受到監視的企業級安全工作負載基礎結構。 此環境包含：

- 名為 DevOps 的[Azure 角色](../../../../role-based-access-control/overview.md)，其有權部署和管理 [Azure App Service 環境](../../../../app-service/environment/intro.md) (由藍圖範例所部署) 中的資源
- [Azure 原則](../../../policy/overview.md)，用於鎖定可部署到環境的服務，以及拒絕建立任何公用 IP 位址 (PIP) 資源
- 包含單一子網路且會對等互連回既有[共用服務](../iso27001-shared/index.md)環境的虛擬網路，並且會強迫所有流量通過[共用服務](../iso27001-shared/index.md)防火牆。 虛擬網路會裝載下列資源：
  - [Azure App Service 環境](../../../../app-service/environment/intro.md)，可用來裝載一個或多個 Web 應用程式、Web API 或函式
  - 使用 VNet 服務端點的 [Azure Key Vault](../../../../key-vault/general/overview.md) 執行個體，用來儲存應用程式在工作負載環境中執行時使用的密碼
  - 使用 VNet 服務端點的 [Azure SQL Database](../../../../azure-sql/database/sql-database-paas-overview.md) 伺服器執行個體，用來裝載應用程式在工作負載環境中使用的資料庫

## <a name="next-steps"></a>後續步驟

您已檢閱 ISO 27001 App Service 環境/SQL Database 工作負載藍圖範例的概觀和架構。 接下來，請瀏覽下列文章，以深入了解控制項對應及部署此範例的方法：

> [!div class="nextstepaction"]
> [ISO 27001 App Service 環境/SQL Database 工作負載藍圖 - 控制項對應](./control-mapping.md)
> [ISO 27001 App Service 環境/SQL Database 工作負載藍圖 - 部署步驟](./deploy.md)

有關藍圖及其使用方式的其他文件：

- 了解[藍圖生命週期](../../concepts/lifecycle.md)。
- 了解如何使用[靜態與動態參數](../../concepts/parameters.md)。
- 了解如何自訂[藍圖排序順序](../../concepts/sequencing-order.md)。
- 了解如何使用[藍圖資源鎖定](../../concepts/resource-locking.md)。
- 了解如何[更新現有的指派](../../how-to/update-existing-assignments.md)。
