---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 04/15/2020
ms.author: cephalin
ms.openlocfilehash: 0fccb1ed092190f88d197c9bf2cd17678a4d1731
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83649038"
---
App Service 的平台元件 (包括 Azure VM、儲存體、網路連線、Web 架構、管理和整合功能) 會主動受到保護與強化。 App Service 會持續經歷加強的合規性檢查，以確保：

- 您的應用程式資源[受到保護](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)免於遭受其他客戶的 Azure 資源威脅。
- [VM 執行個體和執行階段軟體會定期更新](../articles/app-service/overview-patch-os-runtime.md)，以因應新發現的弱點。 
- 您的應用程式與其他 Azure 資源 (例如 [SQL Database](https://azure.microsoft.com/services/sql-database/)) 之間的祕密通訊 (例如連接字串) 仍在 Azure 內，不會跨越任何網路界限。 祕密會在儲存時一律加密。
- 透過 App Service 連線功能 (例如[混合式連線](../articles/app-service/app-service-hybrid-connections.md)) 的所有通訊都會加密。 
- 透過 Azure PowerShell、Azure CLI、Azure SDK、REST API 等遠端管理工具進行的連線全都會加密。
- 全天候威脅管理會保護基礎結構和平台，免於遭受惡意程式碼、分散式拒絕服務 (DDoS)、攔截式 (MITM) 和其他威脅。

如需在 Azure 中的基礎結構與平台安全性的詳細資訊，請參閱 [Azure 信任中心](https://azure.microsoft.com/overview/trusted-cloud/)。