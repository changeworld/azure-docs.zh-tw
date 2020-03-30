---
title: 配置出站網路流量限制 - Azure HDInsight
description: 瞭解如何為 Azure HDInsight 群集配置出站網路流量限制。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: 6e0c98cffef06fb6d6345fc2b23bbc22715909b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370180"
---
# <a name="configure-outbound-network-traffic-for-azure-hdinsight-clusters-using-firewall"></a>使用防火牆為 Azure HDInsight 群集配置出站網路流量

本文提供了使用 Azure 防火牆保護 HDInsight 群集中的出站流量的步驟。 以下步驟假定您正在為現有群集配置 Azure 防火牆。 如果要部署新群集並在防火牆後面部署，請先創建 HDInsight 群集和子網，然後按照本指南中的步驟操作。

## <a name="background"></a>背景

Azure HDInsight 群集通常部署在您自己的虛擬網路中。 群集依賴于該虛擬網路外部需要網路訪問才能正常運行的服務。

有幾個依賴項需要入站流量。 無法通過防火牆設備發送入站管理流量。 此流量的源位址已知，並[在此處](hdinsight-management-ip-addresses.md)發佈。 您還可以使用此資訊創建網路安全性群組 （NSG） 規則，以保護群集的入站流量。

HDInsight 出站流量依賴項幾乎完全由 FQDN 定義，它們後面沒有靜態 IP 位址。 缺少靜態位址意味著網路安全性群組 （NSG） 不能用於鎖定群集中的出站流量。 位址更改的頻率足以導致無法根據當前名稱解析設置規則，並且無法使用它來設置 NSG 規則。

保護出站位址的解決方案是使用防火牆設備，該設備可以基於功能變數名稱控制出站流量。 Azure 防火牆可以基於目標或[FQDN 標記](../firewall/fqdn-tags.md)的 FQDN 限制出站 HTTP 和 HTTPS 流量。

## <a name="configuring-azure-firewall-with-hdinsight"></a>使用 HDInsight 配置 Azure 防火牆

使用 Azure 防火牆鎖定現有 HDInsight 出口的步驟摘要包括：

1. 建立子網路。
1. 創建防火牆。
1. 將應用程式規則添加到防火牆
1. 將網路規則添加到防火牆。
1. 建立路由表。

### <a name="create-new-subnet"></a>創建新子網

在群集所在的虛擬網路中創建名為**Azure 防火牆子網**的子網。

### <a name="create-a-new-firewall-for-your-cluster"></a>為群集創建新防火牆

使用 **"從教程部署防火牆**[：使用 Azure 門戶部署和配置 Azure 防火牆](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall)"中的步驟創建名為**Test-FW01**的防火牆。

### <a name="configure-the-firewall-with-application-rules"></a>使用應用程式規則配置防火牆

創建允許群集發送和接收重要通信的應用程式規則集合。

1. 從 Azure 門戶中選擇新的防火牆**測試-FW01。**

1. 導航到**設置** > **規則** > **應用程式規則集合** > **+ 添加應用程式規則集合**。

    ![標題：添加應用程式規則集合](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection.png)

1. 在 **"添加應用程式規則集合"** 螢幕上，提供以下資訊：

    **頂部部分**

    | 屬性|  值|
    |---|---|
    |名稱| FwAppRule|
    |優先順序|200|
    |動作|Allow|

    **FQDN 標籤部分**

    | 名稱 | 來源地址 | FQDN 標籤 | 注意 |
    | --- | --- | --- | --- |
    | Rule_1 | * | Windows 更新和高清洞察 | HDI 服務所需的 |

    **目標 FQDN 部分**

    | 名稱 | 源位址 | 協定：埠 | 目標 FQDNS | 注意 |
    | --- | --- | --- | --- | --- |
    | Rule_2 | * | HTTPs：443 | login.windows.net | 允許 Windows 登錄活動 |
    | Rule_3 | * | HTTPs：443 | login.microsoftonline.com | 允許 Windows 登錄活動 |
    | Rule_4 | * | HTTPs：443，HTTP：80 | storage_account_name.blob.core.windows.net | 替換為`storage_account_name`實際存儲帳戶名稱。 如果您的群集由 WASB 支援，則為 WASB 添加規則。 要僅使用 HTTPs 連接，請確保在存儲帳戶上啟用[了"需要安全傳輸"。](../storage/common/storage-require-secure-transfer.md) |

   ![標題：輸入應用程式規則集合詳細資訊](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection-details.png)

1. 選取 [加入]****。

### <a name="configure-the-firewall-with-network-rules"></a>使用網路規則配置防火牆

創建網路規則以正確配置 HDInsight 群集。

1. 繼續從上一步，導航到**網路規則集合** > **+ 添加網路規則集合**。

1. 在 **"添加網路規則集合"** 螢幕上，提供以下資訊：

    **頂部部分**

    | 屬性|  值|
    |---|---|
    |名稱| FwNetRule|
    |優先順序|200|
    |動作|Allow|

    **IP 位址部分**

    | 名稱 | 通訊協定 | 源位址 | 目標位址 | 目的地連接埠 | 注意 |
    | --- | --- | --- | --- | --- | --- |
    | Rule_1 | UDP | * | * | 123 | 時間服務 |
    | Rule_2 | 任意 | * | DC_IP_Address_1，DC_IP_Address_2 | * | 如果使用企業安全包 （ESP），則在 IP 位址部分中添加網路規則，允許與 ESP 群集的 AAD-DS 通信。 您可以在門戶中的 AAD-DS 部分找到網域控制站的 IP 位址 |
    | Rule_3 | TCP | * | 資料湖存儲帳戶的 IP 位址 | * | 如果使用 Azure 資料湖存儲，則可以在 IP 位址部分添加網路規則，以解決 ADLS Gen1 和 Gen2 的 SNI 問題。 此選項將流量路由到防火牆，這可能導致大型資料載入的成本增加，但流量將在防火牆日誌中記錄和審核。 確定資料湖存儲帳戶的 IP 位址。 可以使用電源殼命令，例如`[System.Net.DNS]::GetHostAddresses("STORAGEACCOUNTNAME.blob.core.windows.net")`將 FQDN 解析為 IP 位址。|
    | Rule_4 | TCP | * | * | 12000 | （可選）如果使用日誌分析，則在 IP 位址部分中創建網路規則，以啟用與日誌分析工作區的通信。 |

    **服務標籤部分**

    | 名稱 | 通訊協定 | 來源位址 | 服務標籤 | 目標埠 | 注意 |
    | --- | --- | --- | --- | --- | --- |
    | Rule_7 | TCP | * | SQL | 1433 | 在 SQL 的服務標記部分中配置網路規則，該規則允許您記錄和審核 SQL 流量，除非您在 HDInsight 子網上為 SQL Server 配置了服務終結點，這將繞過防火牆。 |

   ![標題：輸入應用程式規則集合](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-network-rule-collection.png)

1. 選取 [加入]****。

### <a name="create-and-configure-a-route-table"></a>創建和配置路由表

創建包含以下條目的路由表：

* [來自運行狀況和管理服務](../hdinsight/hdinsight-management-ip-addresses.md#health-and-management-services-all-regions)的所有 IP 位址：具有下一個躍點類型的**Internet**的所有區域。

* 從[運行狀況和管理服務](../hdinsight/hdinsight-management-ip-addresses.md#health-and-management-services-specific-regions)創建群集的區域的兩個 IP 位址：具有下一個躍點類型的**Internet**的特定區域。

* IP 位址的一個虛擬裝置路由 0.0.0.0/0，下一個躍點是 Azure 防火牆私人 IP 位址。

例如，要為在美國"美國東部"區域創建的群集配置路由表，請使用以下步驟：

1. 選擇 Azure 防火牆**測試-FW01**。 複製 **"概述"** 頁上列出的**私人 IP 位址**。 在此示例中，我們將使用**10.0.2.4 的示例位址**。

1. 然後導航到**所有服務** > **網路** > **路由表**並**創建路由表**。

1. 從新路由導航到**設置** > **路由** > **和添加**。 添加以下路由：

| 路由名稱 | 位址首碼 | 下一個躍點類型 | 下一個躍點位址 |
|---|---|---|---|
| 168.61.49.99 | 168.61.49.99/32 | Internet | NA |
| 23.99.5.239 | 23.99.5.239/32 | Internet | NA |
| 168.61.48.131 | 168.61.48.131/32 | Internet | NA |
| 138.91.141.162 | 138.91.141.162/32 | Internet | NA |
| 13.82.225.233 | 13.82.225.233/32 | Internet | NA |
| 40.71.175.99 | 40.71.175.99/32 | Internet | NA |
| 0.0.0.0 | 0.0.0.0/0 | 虛擬設備 | 10.0.2.4 |

完成路由表配置：

1. 通過在 **"設置**"下選擇**子網**，將您創建的路由表分配給 HDInsight 子網。

1. 選擇 **= 關聯**。

1. 在 **"關聯子網"** 螢幕上，選擇群集創建的虛擬網路和用於 HDInsight 群集的**子網**。

1. 選取 [確定]****。

## <a name="edge-node-or-custom-application-traffic"></a>邊緣節點或自訂應用程式流量

上述步驟將允許群集在沒有問題的情況下運行。 如果適用，您仍然需要配置依賴項以適應在邊緣節點上運行的自訂應用程式。

必須標識應用程式依賴項並將其添加到 Azure 防火牆或路由表中。

必須為應用程式流量創建路由以避免非對稱路由問題。

如果應用程式具有其他依賴項，則需要將它們添加到 Azure 防火牆中。 建立應用程式規則來允許 HTTP/HTTPS 流量，以及建立網路規則來允許其他一切流量。

## <a name="logging-and-scale"></a>日誌記錄和縮放

Azure 防火牆可以將日誌發送到幾個不同的存儲系統。 有關為防火牆配置日誌記錄的說明，請按照教程中的步驟[：監視 Azure 防火牆日誌和指標](../firewall/tutorial-diagnostics.md)。

完成日誌記錄設置後，如果將資料記錄到日誌分析，可以使用以下查詢查看被阻止的流量：

```Kusto
AzureDiagnostics | where msg_s contains "Deny" | where TimeGenerated >= ago(1h)
```

當您不知道所有應用程式依賴項時，首次讓應用程式正常工作時，將 Azure 防火牆與 Azure 監視器日誌集成非常有用。 您可以從[分析 Azure 監視器中的記錄資料](../azure-monitor/log-query/log-query-overview.md)深入了解 Azure 監視器記錄

要瞭解 Azure 防火牆和請求增加的規模限制，請參閱[本文檔](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-firewall-limits)或參閱[常見問題解答](../firewall/firewall-faq.md)。

## <a name="access-to-the-cluster"></a>訪問群集

成功設置防火牆後，可以使用內部終結點 （`https://CLUSTERNAME-int.azurehdinsight.net`） 從虛擬網路內部訪問 Ambari。

要使用公共終結點 （`https://CLUSTERNAME.azurehdinsight.net`） 或 ssh 終結點 （），`CLUSTERNAME-ssh.azurehdinsight.net`請確保路由表和 NSG 規則中具有正確的路由，以避免[此處](../firewall/integrate-lb.md)介紹的不對稱路由問題。 具體在這種情況下，您需要允許入站 NSG 規則中的用戶端 IP 位址，並將其添加到使用者定義的路由表中，下一個躍點設置為`internet`。 如果未正確設置，您將看到逾時錯誤。

## <a name="configure-another-network-virtual-appliance"></a>配置另一個網路虛擬裝置

> [!Important]
> 僅當希望配置 Azure 防火牆以外的網路虛擬裝置 （NVA） 時，**才**需要以下資訊。

前面的說明可説明您配置 Azure 防火牆，以限制來自 HDInsight 群集的出站流量。 Azure 防火牆會自動設定為允許流量用於許多常見重要方案。 如果要使用其他網路虛擬裝置，則需要手動設定許多附加功能。 在配置網路虛擬裝置時，請記住以下事項：

* 應使用服務端點來設定支援的服務端點服務。
* IP 位址依賴項適用于非 HTTP/S 流量（TCP 和 UDP 流量）。
* FQDN HTTP/HTTPS 終結點可以放置在您的 NVA 設備中。
* 萬用字元 HTTP/HTTPS 終結點是依賴項，可能因許多限定詞而異。
* 將創建的路由表分配給 HDInsight 子網。

### <a name="service-endpoint-capable-dependencies"></a>支援服務終結點的依賴項

| **端點** |
|---|
| Azure SQL |
| Azure 儲存體 |
| Azure Active Directory |

#### <a name="ip-address-dependencies"></a>IP 位址相依性

| **端點** | **詳細資料** |
|---|---|
| \*:123 | NTP 時鐘檢查。 在連接埠 123 上的多個端點檢查流量 |
| [此處](hdinsight-management-ip-addresses.md)發佈的 IP | 這些是 HDInsight 服務 |
| 用於 ESP 群集的 AAD-DS 專用 IP |
| \*：16800 用於 KMS 視窗啟動 |
| \*12000 用於日誌分析 |

#### <a name="fqdn-httphttps-dependencies"></a>FQDN HTTP/HTTPS 相依性

> [!Important]
> 下面的清單僅提供了一些最重要的 FQDN。 您可以獲取用於[在此檔中](https://github.com/Azure-Samples/hdinsight-fqdn-lists/blob/master/HDInsightFQDNTags.json)配置 NVA 的 FQDN 的完整清單。

| **端點**                                                          |
|---|
| azure.archive.ubuntu.com:80                                           |
| security.ubuntu.com:80                                                |
| ocsp.msocsp.com:80                                                    |
| ocsp.digicert.com:80                                                  |
| wawsinfraprodbay063.blob.core.windows.net:443                         |
| registry-1.docker.io:443                                              |
| auth.docker.io:443                                                    |
| production.cloudflare.docker.com:443                                  |
| download.docker.com:443                                               |
| us.archive.ubuntu.com:80                                              |
| download.mono-project.com:80                                          |
| packages.treasuredata.com:80                                          |
| security.ubuntu.com:80                                                |
| azure.archive.ubuntu.com:80                                           |
| ocsp.msocsp.com:80                                                    |
| ocsp.digicert.com:80                                                  |

## <a name="next-steps"></a>後續步驟

* [Azure HDInsight 虛擬網路架構](hdinsight-virtual-network-architecture.md)
