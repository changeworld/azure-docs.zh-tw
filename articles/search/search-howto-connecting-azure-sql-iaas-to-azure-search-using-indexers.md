---
title: 用於搜尋索引的 Azure SQL VM 連線
titleSuffix: Azure Cognitive Search
description: 啟用加密連線並將防火牆設定為允許從 Azure 認知搜尋上的索引子連線到 Azure 虛擬機器（VM）上的 SQL Server。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: bf25c74f0190bc67e7da703e242d5d4bb3e299f5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84020637"
---
# <a name="configure-a-connection-from-an-azure-cognitive-search-indexer-to-sql-server-on-an-azure-vm"></a>設定從 Azure 認知搜尋索引子到 Azure VM 上 SQL Server 的連線

如[使用索引子將 Azure SQL Database 連線至 Azure 認知搜尋](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq)中所述，Azure 認知搜尋支援針對**azure 虛擬機器上的 SQL Server**建立索引子（簡稱**SQL Azure vm** ），但必須先處理幾個安全性相關的必要條件。 

從 Azure 認知搜尋到 VM 上 SQL Server 的連線是公用的網際網路連接。 您通常對這些連線採取的所有安全性措施，在這裡也適用：

+ 向[憑證授權單位提供者](https://en.wikipedia.org/wiki/Certificate_authority#Providers)取得憑證，以取得 Azure VM 上 SQL Server 執行個體的完整網域名稱。
+ 在 VM 上安裝憑證，然後使用本文中的指示在 VM 上啟用並設定加密的連線。

## <a name="enable-encrypted-connections"></a>啟用加密的連線
Azure 認知搜尋需要透過公用網際網路連線的所有索引子要求的加密通道。 本節列出執行這項工作的步驟。

1. 檢查憑證的屬性，以驗證主體名稱為 Azure VM 的完整網域名稱 (FQDN)。 您可以使用 CertUtils 之類的工具或憑證嵌入式管理單元來檢視屬性。 您可以在 [Azure 入口網站](https://portal.azure.com/)的 [公用 IP 位址/DNS 名稱標籤]**** 欄位中，從 VM 服務刀鋒視窗的 [程式集] 區段中取得 FQDN。
   
   * 對於使用較新的 **Resource Manager** 範本建立的 VM，FQDN 的格式為 `<your-VM-name>.<region>.cloudapp.azure.com`
   * 對於建立為**傳統** VM 的較舊 VM，FQDN 的格式為 `<your-cloud-service-name.cloudapp.net>`。

2. 使用登錄編輯器 (regedit) 設定 SQL Server 以使用憑證。 
   
    雖然通常會在這項工作中使用 SQL Server 組態管理員，但您在此案例中無法使用它。 它不會尋找匯入的憑證，因為在 Azure 上 VM 的 FQDN 不符合由 VM 所決定的 FQDN (它會將網域識別為本機電腦或其已加入的網路網域)。 當名稱不相符時，使用 regedit 來指定憑證。
   
   * 在 regedit 中，瀏覽此登錄金鑰︰`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server\[MSSQL13.MSSQLSERVER]\MSSQLServer\SuperSocketNetLib\Certificate`。
     
     `[MSSQL13.MSSQLSERVER]` 部分將視版本和執行個體名稱而有所不同。 
   * 將**憑證**金鑰的值設定為您匯入 VM 的 TLS/SSL 憑證的**指紋**。
     
     有數種方式可以取得指紋，有些方式較其他的更好。 如果您將它從 MMC 中的 **憑證** 嵌入式管理單元進行複製，您可能會挑選不可見的開頭字元 ( [如本支援文章中所述](https://support.microsoft.com/kb/2023869/))，如此當您嘗試連接時便會導致錯誤。 有數個因應措施可修正此問題。 最簡單的方法是按退格鍵，然後重新輸入指紋的第一個字元，在 regedit 中移除金鑰值欄位中的開頭字元。 或者，您可以使用不同的工具來複製指紋。

3. 授與權限給服務帳戶。 
   
    請確定 SQL Server 服務帳戶已被授與 TLS/SSL 憑證之私密金鑰的適當許可權。 如果您忽略此步驟，SQL Server 將會無法啟動。 針對這項工作，您可以使用**憑證**嵌入式管理單元或 **CertUtils**。
    
4. 重新啟動 SQL Server 服務。

## <a name="configure-sql-server-connectivity-in-the-vm"></a>在 VM 中設定 SQL Server 連線
在您設定 Azure 認知搜尋所需的加密連線之後，Azure Vm 上 SQL Server 會有額外的設定步驟。 如果您尚未這麼做，則下一個步驟是使用這些文件其中之一來完成設定︰

* 若為 **Resource Manager** VM，請參閱 [使用 Resource Manager 連接到 Azure 上的 SQL Server 虛擬機器](../azure-sql/virtual-machines/windows/ways-to-connect-to-sql.md)。 
* 若為 **傳統** VM，請參閱 [連線到 Azure 上的 SQL Server 虛擬機器](../virtual-machines/windows/classic/sql-connect.md)。

特別是，檢閱每個文章中的「透過網際網路連接」一節。

## <a name="configure-the-network-security-group-nsg"></a>設定網路安全性群組 (NSG)
設定 NSG 與相對應的 Azure 端點或存取控制清單 (ACL)，讓他人能夠存取您的 Azure VM。 您很可能過去已完成此設定，允許自己的應用程式邏輯連接至您的 SQL Azure VM。 與 SQL Azure VM 的 Azure 認知搜尋連線並無不同。 

下列連結提供 VM 部署的 NSG 組態相關指示。 使用這些指示，根據 Azure 認知搜尋端點的 IP 位址來進行 ACL。

> [!NOTE]
> 若為背景，請參閱 [什麼是網路安全性群組？](../virtual-network/security-overview.md)
> 
> 

* 若為 **Resource Manager** VM，請參閱 [如何針對 ARM 部署建立 NSG](../virtual-network/tutorial-filter-network-traffic.md)。 
* 若為 **傳統** VM，請參閱 [如何針對傳統部署建立 NSG](../virtual-network/virtual-networks-create-nsg-classic-ps.md)。

IP 位址可能會造成一些挑戰，如果您知道問題和可能的因應措施則可輕易克服。 其餘各節提供與 ACL 中的 IP 位址相關的問題處理建議。

#### <a name="restrict-access-to-the-azure-cognitive-search"></a>限制對 Azure 認知搜尋的存取
強烈建議您限制對搜尋服務的 IP 位址，以及 ACL 中服務標籤的 IP 位址範圍的存取， `AzureCognitiveSearch` [service tag](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags)而不是讓您的 SQL Azure vm 開放給所有連線要求。

您可以藉由 ping 搜尋服務的 FQDN （例如，）來找出 IP 位址 `<your-search-service-name>.search.windows.net` 。

您可以 `AzureCognitiveSearch` 使用[可下載的 JSON](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#discover-service-tags-by-using-downloadable-json-files)檔案或透過服務標籤[探索 API](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#use-the-service-tag-discovery-api-public-preview)，來找出[服務](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags)標籤的 IP 位址範圍。 每週會更新 IP 位址範圍。

#### <a name="managing-ip-address-fluctuations"></a>管理 IP 位址的變動
如果您的搜尋服務只有一個搜尋單位 (也就是有一個複本和一個分割區)，在例行服務重新啟動期間，IP 位址會變更，使用您搜尋服務的 IP 位址讓現有的 ACL 失效。

避免後續連線錯誤的方法之一，是在 Azure 認知搜尋中使用一個以上的複本和一個資料分割。 這樣做會增加成本，但它也可以解決 IP 位址的問題。 在 Azure 認知搜尋中，當您有一個以上的搜尋單位時，IP 位址不會變更。

第二個方法是讓連線失敗，然後再重新設定 NSG 中的 ACL。 平均而言，您可以預期 IP 位址每隔幾週會進行變更。 對於頻繁地進行控制編製索引的客戶，此方法可能可行。

第三個可行 (但並不特別安全) 的方法，是針對搜尋服務佈建所在的 Azure 區域，指定 IP 位址範圍。 將公用 IP 位址配置給 Azure 資源的 IP 範圍清單已發佈於 [Azure 資料中心 IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)。 

#### <a name="include-the-azure-cognitive-search-portal-ip-addresses"></a>包含 Azure 認知搜尋入口網站的 IP 位址
如果您使用 Azure 入口網站來建立索引子，Azure 認知搜尋入口網站邏輯也需要在建立期間存取您的 SQL Azure VM。 Ping 可找到 Azure 認知搜尋入口網站的 IP 位址 `stamp2.search.ext.azure.com` 。

## <a name="next-steps"></a>後續步驟
使用設定時，您現在可以在 Azure VM 上指定 SQL Server 做為 Azure 認知搜尋索引子的資料來源。 如需詳細資訊，請參閱[使用索引子將 Azure SQL Database 連接到 Azure 認知搜尋](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)。

