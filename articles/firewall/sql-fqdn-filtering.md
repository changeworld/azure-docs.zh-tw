---
title: 使用 SQL FQDN 配置 Azure 防火牆應用程式規則
description: 在本文中，您將瞭解如何在 Azure 防火牆應用程式規則中配置 SQL FQDN。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 07/19/2019
ms.author: victorh
ms.openlocfilehash: 858cfc9a8c15f1e33e688bb5086a58f194e7173f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501507"
---
# <a name="configure-azure-firewall-application-rules-with-sql-fqdns"></a>使用 SQL FQDN 配置 Azure 防火牆應用程式規則

> [!IMPORTANT]
> 具有 SQL FQDN 的 Azure 防火牆應用程式規則當前處於公共預覽版中。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

現在，您可以使用 SQL FQDN 配置 Azure 防火牆應用程式規則。 這允許您將虛擬網路的訪問限制為僅指定的 SQL 伺服器實例。

使用 SQL FQDN，您可以篩選流量：

- 從 VNet 到 Azure SQL 資料庫或 Azure SQL 資料倉儲。 例如：只允許訪問*sql-server1.database.windows.net。*
- 從本地到 Azure SQL 託管實例或 VNet 中運行的 SQL IaaS。
- 從分輻到 Azure SQL 託管實例或 VNet 中運行的 SQL IaaS。

在公共預覽期間，僅[代理模式](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture#connection-policy)（埠 1433）支援 SQL FQDN 篩選。 如果在預設重定向模式下使用 SQL，則可以使用 SQL 服務標記篩選訪問，作為[網路規則的](overview.md#network-traffic-filtering-rules)一部分。
如果對 SQL IaaS 流量使用非預設埠，則可以在防火牆應用程式規則中配置這些埠。

具有 SQL FQDN 的應用程式規則當前通過 Azure 門戶、Azure CLI、REST 和範本在所有區域都可用。

## <a name="configure-using-azure-cli"></a>使用 Azure CLI 進行配置

1. 使用[Azure CLI](deploy-cli.md)部署 Azure 防火牆 。
2. 如果篩選到 Azure SQL 資料庫、SQL 資料倉儲或 SQL 託管實例的流量，請確保 SQL 連接模式設置為**代理**。 要瞭解如何切換 SQL 連接模式，請參閱[Azure SQL 連接設置](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-settings#change-connection-policy-via-azure-cli)。

   > [!NOTE]
   > 與*重定向*相比，SQL*代理*模式會導致更多的延遲。 如果要繼續使用重定向模式（這是在 Azure 中連接的用戶端的預設值），可以使用防火牆[網路規則](tutorial-firewall-deploy-portal.md#configure-a-network-rule)中的 SQL[服務標記](service-tags.md)篩選訪問。

3. 使用 SQL FQDN 配置應用程式規則以允許訪問 SQL 伺服器：

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
1. 使用[Azure CLI](deploy-cli.md)部署 Azure 防火牆 。
2. 如果篩選到 Azure SQL 資料庫、SQL 資料倉儲或 SQL 託管實例的流量，請確保 SQL 連接模式設置為**代理**。 要瞭解如何切換 SQL 連接模式，請參閱[Azure SQL 連接設置](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-settings#change-connection-policy-via-azure-cli)。  

   > [!NOTE]
   > 與*重定向*相比，SQL*代理*模式會導致更多的延遲。 如果要繼續使用重定向模式（這是在 Azure 中連接的用戶端的預設值），可以使用防火牆[網路規則](tutorial-firewall-deploy-portal.md#configure-a-network-rule)中的 SQL[服務標記](service-tags.md)篩選訪問。
3. 使用適當的協定、埠和 SQL FQDN 添加應用程式規則，然後選擇 **"保存**"。
   ![使用 SQL FQDN 的應用程式規則](media/sql-fqdn-filtering/application-rule-sql.png)
4. 從 VNet 中的虛擬機器訪問 SQL，該虛擬機器通過防火牆篩選流量。 
5. 驗證[Azure 防火牆日誌](log-analytics-samples.md)是否顯示允許流量。

## <a name="next-steps"></a>後續步驟

要瞭解 SQL 代理和重定向模式，請參閱[Azure SQL 資料庫連接體系結構](../sql-database/sql-database-connectivity-architecture.md)。