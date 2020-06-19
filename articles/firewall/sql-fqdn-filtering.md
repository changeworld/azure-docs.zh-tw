---
title: 使用 SQL FQDN 設定 Azure 防火牆應用程式規則
description: 在此文章中，您將了解如何在 Azure 防火牆應用程式規則中設定 SQL FQDN。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 05/18/2020
ms.author: victorh
ms.openlocfilehash: 8b67574f435681d8071eda1ad954dcafb5124cbf
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83655095"
---
# <a name="configure-azure-firewall-application-rules-with-sql-fqdns"></a>使用 SQL FQDN 設定 Azure 防火牆應用程式規則

您現在可以使用 SQL FQDN 設定 Azure 防火牆應用程式規則。 這可讓您將虛擬網路的存取限制為僅限指定的 SQL Server 執行個體。

利用 SQL FQDN，您可以篩選流量：

- 從您的 VNet 到 Azure SQL Database 或 Azure SQL 資料倉儲。 例如：僅允許存取 *sql-server1.database.windows.net*。
- 從內部部署到 Azure SQL 受控執行個體或在 VNet 中執行的 SQL IaaS。
- 從支點對支點到 Azure SQL 受控執行個體或在 VNet 中執行的 SQL IaaS。

只有 [Proxy 模式](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture#connection-policy)才支援 SQL FQDN 篩選 (連接埠 1433)。 如果您在預設的重新導向模式下使用 SQL，可以使用 SQL 服務標籤篩選存取，作為[網路規則](overview.md#network-traffic-filtering-rules)的一部分。
如果您針對 SQL IaaS 流量使用非預設的連接埠，可以在防火牆應用程式規則中設定那些連接埠。

## <a name="configure-using-azure-cli"></a>使用 Azure CLI 進行設定

1. 部署[使用 Azure CLI 的 Azure 防火牆](deploy-cli.md)。
2. 如果您篩選到 Azure SQL Database、SQL 資料倉儲或 SQL 受控執行個體的流量，請確定 SQL 連線模式已設定為 [Proxy]。 若要了解如何切換 SQL 連線模式，請參閱 [Azure SQL 連線能力設定](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-settings#change-connection-policy-via-azure-cli) \(英文\)。

   > [!NOTE]
   > 相較於*重新導向*，SQL *Proxy* 模式可能會導致更多延遲。 如果您想要繼續使用重新導向模式 (這是用戶端在 Azure 內連線時的預設值)，可以在防火牆[網路規則](tutorial-firewall-deploy-portal.md#configure-a-network-rule)中使用 SQL [服務標籤](service-tags.md)篩選存取。

3. 使用 SQL FQDN 設定應用程式規則以允許存取 SQL Server：

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
1. 部署[使用 Azure CLI 的 Azure 防火牆](deploy-cli.md)。
2. 如果您篩選到 Azure SQL Database、SQL 資料倉儲或 SQL 受控執行個體的流量，請確定 SQL 連線模式已設定為 [Proxy]。 若要了解如何切換 SQL 連線模式，請參閱 [Azure SQL 連線能力設定](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-settings#change-connection-policy-via-azure-cli) \(英文\)。  

   > [!NOTE]
   > 相較於*重新導向*，SQL *Proxy* 模式可能會導致更多延遲。 如果您想要繼續使用重新導向模式 (這是用戶端在 Azure 內連線時的預設值)，可以在防火牆[網路規則](tutorial-firewall-deploy-portal.md#configure-a-network-rule)中使用 SQL [服務標籤](service-tags.md)篩選存取。
3. 利用適當的通訊協定、連接埠和 SQL FQDN，新增應用程式規則，然後選取 [儲存]。
   ![使用 SQL FQDN 的應用程式規則](media/sql-fqdn-filtering/application-rule-sql.png)
4. 在 VNet 中，從透過防火牆篩選流量的虛擬機器存取 SQL。 
5. 驗證 [Azure 防火牆記錄](log-analytics-samples.md)顯示允許流量。

## <a name="next-steps"></a>後續步驟

若要了解 SQL Proxy 和重新導向模式，請參閱 [Azure SQL Database 連線架構](../sql-database/sql-database-connectivity-architecture.md)。