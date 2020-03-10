---
title: 使用 SQL Fqdn 設定 Azure 防火牆應用程式規則
description: 在本文中，您將瞭解如何在 Azure 防火牆應用程式規則中設定 SQL Fqdn。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 07/19/2019
ms.author: victorh
ms.openlocfilehash: a42d6bcdcec2a5de7432f11216a4d8dd0c1deef9
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2020
ms.locfileid: "78942561"
---
# <a name="configure-azure-firewall-application-rules-with-sql-fqdns"></a>使用 SQL Fqdn 設定 Azure 防火牆應用程式規則

> [!IMPORTANT]
> 具有 SQL Fqdn 的 Azure 防火牆應用程式規則目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

您現在可以使用 SQL Fqdn 來設定 Azure 防火牆應用程式規則。 這可讓您將虛擬網路的存取限制為僅限指定的 SQL server 實例。

有了 SQL Fqdn，您就可以篩選流量：

- 從您的 Vnet 到 Azure SQL Database 或 Azure SQL 資料倉儲。 例如：僅允許存取*sql-server1.database.windows.net*。
- 從內部部署到 Azure SQL 受控實例或在您的 Vnet 中執行的 SQL IaaS。
- 從輪輻到輪輻，到在您的 Vnet 中執行的 Azure SQL 受控實例或 SQL IaaS。

在公開預覽期間，只支援[proxy 模式](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture#connection-policy)中的 SQL FQDN 篩選（埠1433）。 如果您在預設的重新導向模式中使用 SQL，則可以使用 SQL 服務標籤來篩選存取，做為[網路規則](overview.md#network-traffic-filtering-rules)的一部分。
如果您針對 SQL IaaS 流量使用非預設的埠，您可以在防火牆應用程式規則中設定這些埠。

具有 SQL Fqdn 的應用程式規則目前可透過 Azure 入口網站、Azure CLI、REST 和範本在所有區域中使用。

## <a name="configure-using-azure-cli"></a>使用 Azure CLI 進行設定

1. [使用 Azure CLI 部署 Azure 防火牆](deploy-cli.md)。
2. 如果您篩選到 Azure SQL Database、SQL 資料倉儲或 SQL 受控執行個體的流量，請確定 SQL 連線模式已設定為 [ **Proxy**]。 若要瞭解如何切換 SQL 連線模式，請參閱[AZURE sql 連線能力設定](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-settingse#change-azure-sql-database-connection-policy)。

   > [!NOTE]
   > 相較于重新*導向*，SQL *proxy*模式可能會導致更多的延遲。 如果您想要繼續使用重新導向模式（這是在 Azure 內連線的用戶端預設值），您可以使用防火牆[網路規則](tutorial-firewall-deploy-portal.md#configure-a-network-rule)中的 SQL[服務](service-tags.md)標籤來篩選存取權。

3. 設定具有 SQL FQDN 的應用程式規則，以允許存取 SQL server：

   ```azurecli
   az extension add -n azure-firewall

   az network firewall application-rule create \
   -g FWRG \
   -f azfirewall \
   -c FWAppRules \
   -n srule \
   --protocols mssql=1433 \
   --source-addresses 10.0.0.0/24 \
   --target-fqdns sql-serv1.database.windows.net
   ```

## <a name="configure-using-the-azure-portal"></a>使用 Azure 入口網站進行設定
1. [使用 Azure CLI 部署 Azure 防火牆](deploy-cli.md)。
2. 如果您篩選到 Azure SQL Database、SQL 資料倉儲或 SQL 受控執行個體的流量，請確定 SQL 連線模式已設定為 [ **Proxy**]。 若要瞭解如何切換 SQL 連線模式，請參閱[AZURE sql 連線能力設定](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-settingse#change-azure-sql-database-connection-policy)。  

   > [!NOTE]
   > 相較于重新*導向*，SQL *proxy*模式可能會導致更多的延遲。 如果您想要繼續使用重新導向模式（這是在 Azure 內連線的用戶端預設值），您可以使用防火牆[網路規則](tutorial-firewall-deploy-portal.md#configure-a-network-rule)中的 SQL[服務](service-tags.md)標籤來篩選存取權。
3. 以適當的通訊協定、埠和 SQL FQDN 新增應用程式規則，然後選取 [**儲存**]。
   ![具有 SQL FQDN](media/sql-fqdn-filtering/application-rule-sql.png) 的應用程式規則
4. 從透過防火牆篩選流量的 VNet 中的虛擬機器存取 SQL。 
5. 驗證[Azure 防火牆記錄](log-analytics-samples.md)是否顯示允許的流量。

## <a name="next-steps"></a>後續步驟

若要瞭解 SQL proxy 和重新導向模式，請參閱[AZURE sql database 連線架構](../sql-database/sql-database-connectivity-architecture.md)。