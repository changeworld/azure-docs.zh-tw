---
title: 安全性考量
description: 說明 Azure Data Factory 中資料移動服務用來協助保護您資料的基本安全性基礎結構。
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/11/2020
ms.openlocfilehash: bb3f22223bd64c06cfa4a5f6ffabe7b128dff1d5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416475"
---
#  <a name="security-considerations-for-data-movement-in-azure-data-factory"></a>在 Azure Data Factory 中資料移動的安全性考量
> [!div class="op_single_selector" title1="選取您目前使用的 Data Factory 服務版本："]
>
> * [版本 1](v1/data-factory-data-movement-security-considerations.md)
> * [目前版本](data-movement-security-considerations.md)

 [!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

本文說明 Azure Data Factory 中資料移動服務用來協助保護您資料的基本安全性基礎結構。 Data Factory 管理資源建置在 Azure 安全性基礎結構上，並使用 Azure 提供的所有可能安全性措施。

在 Data Factory 方案中，您可以建立一或多個資料 [管線](concepts-pipelines-activities.md)。 管線是一起執行某個工作的活動所組成的邏輯群組。 這些管線位於建立 Data Factory 的區域中。 

儘管 Data Factory 僅適用於某些區域，資料移動服務仍[全球適用](concepts-integration-runtime.md#integration-runtime-location)，以確保資料合規、有效率，且網路輸出成本降低。 

Azure Data Factory 除了用於雲端資料存放區的連結服務認證 (會使用憑證加密) 之外，並不會儲存任何資料。 您可以使用 Data Factory 來建立資料導向工作流程，藉由使用其他區域或內部部署環境中的[計算服務](compute-linked-services.md)，協調[所支援資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)之間的資料移動和資料處理。 您也可以藉由使用 SDK 和 Azure 監視器來監視和管理工作流程。

Data Factory 已通過下列各項規範的認證：

| **[CSA STAR Certification](https://www.microsoft.com/trustcenter/compliance/csa-star-certification)** |
| :----------------------------------------------------------- |
| **[ISO 20000-1:2011](https://www.microsoft.com/trustcenter/Compliance/ISO-20000-1)** |
| **[ISO 22301:2012](https://www.microsoft.com/trustcenter/compliance/iso-22301)** |
| **[ISO 27001:2013](https://www.microsoft.com/trustcenter/compliance/iso-iec-27001)** |
| **[ISO 27017:2015](https://www.microsoft.com/trustcenter/compliance/iso-iec-27017)** |
| **[ISO 27018:2014](https://www.microsoft.com/trustcenter/compliance/iso-iec-27018)** |
| **[ISO 9001:2015](https://www.microsoft.com/trustcenter/compliance/iso-9001)** |
| **[SOC 1, 2, 3](https://www.microsoft.com/trustcenter/compliance/soc)** |
| **[HIPAA BAA](https://www.microsoft.com/trustcenter/compliance/hipaa)** |

如果您對 Azure 法規遵循以及 Azure 如何保護其專屬基礎結構感興趣，請瀏覽 [Microsoft 信任中心](https://microsoft.com/en-us/trustcenter/default.aspx)。 如需所有 Azure 合規性供應項目的最新清單，請查看 - https://aka.ms/AzureCompliance。

在本文中，我們會檢閱下列兩個資料移動案例中的安全性考量︰ 

- **雲端案例**：在此案例中，可透過網際網路公開存取您的來源和目的地。 這些包括受控雲端儲存體服務 (例如「Azure 儲存體」、「Azure SQL 資料倉儲」、Azure SQL Database、Azure Data Lake Store、Amazon S3、Amazon Redshift)、SaaS 服務 (例如 Salesforce)，以及 Web 通訊協定 (例如 FTP 和 OData)。 在[支援的資料存放區和格式](copy-activity-overview.md#supported-data-stores-and-formats)中，尋找支援的資料來源完整清單。
- **混合式案例**：在此案例中，您的來源或目的地是位於防火牆後方或在內部部署公司網路內。 或者，資料存放區是在私人網路或虛擬網路 (最常見的來源) ，而且無法公開存取。 裝載在虛擬機器上的資料庫伺服器也屬於此案例的涵蓋範圍。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cloud-scenarios"></a>雲端案例

### <a name="securing-data-store-credentials"></a>保護資料存放區認證

- **在 Azure Data Factory 受控存放區中儲存加密的認證**。 Data Factory 可透過使用受 Microsoft 管理的憑證來加密資料存放區認證，為這些認證提供保護。 這些憑證每隔兩年會輪替一次 (包括憑證更新和憑證移轉)。 如需有關「Azure 儲存體」安全性的詳細資訊，請參閱 [Azure 儲存體安全性概觀](../security/fundamentals/storage-overview.md)。
- **在 Azure 金鑰保存的憑證中儲存認證**。 您也可以在 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 中儲存資料存放區的認證。 Data Factory 會在活動執行期間擷取認證。 如需詳細資訊，請參閱 [在 Azure Key Vault 中儲存認證](store-credentials-in-key-vault.md)。

### <a name="data-encryption-in-transit"></a>傳輸中資料加密
如果雲端資料存放區支援 HTTPS 或 TLS，則 Data Factory 中資料移動服務與雲端資料存放區之間的所有資料傳輸，都會透過安全通道 HTTPS 或 TLS。

> [!NOTE]
> 所有連到 Azure SQL Database 和「Azure SQL 資料倉儲」的連線在資料透過傳輸進出資料庫時，需要加密 (SSL/TLS)。 當您使用 JSON 編輯器來編寫管線時，在連接字串中新增 encryption 屬性，並將它設定為 **true**。 對於 Azure 儲存,可以在連接字串中使用**HTTPS。**

> [!NOTE]
> 若要在從 Oracle 移動資料時啟用傳輸中加密，請遵循下列其中一個選項：
> 1. 在 Oracle 伺服器上，移至 Oracle 進階安全性 (OAS) 並設定加密設定，其支援三重 DES 加密 (3DES) 和進階加密標準 (AES)，請參閱[這裡](https://docs.oracle.com/cd/E11882_01/network.112/e40393/asointro.htm#i1008759)的詳細資料。 ADF 會自動協商加密方法，以使用建立 Oracle 連線時您在 OAS 中設定的方法。
> 2. 在 ADF 中，您可以在連接字串 (位於連結服務內) 中新增 EncryptionMethod=1。 這將使用 SSL/TLS 作為加密方法。 若要使用這個方法，您需要在 Oracle 伺服器端的 OAS 中停用非 SSL 加密設定，以避免加密衝突。

> [!NOTE]
> 使用的 TLS 版本為 1.2。

### <a name="data-encryption-at-rest"></a>待用資料加密
有些資料存放區支援待用資料加密。 建議您為這些資料存放區啟用資料加密機制。 

#### <a name="azure-sql-data-warehouse"></a>Azure SQL 資料倉儲
Azure SQL 資料倉儲中的透明資料加密 (TDE) 可以對待用資料執行即時加密和解密，協助防止惡意活動的威脅。 用戶端並不會察覺到這個過程。 如需詳細資訊，請參閱[保護 SQL 資料倉儲中的資料庫](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-manage-security.md)。

#### <a name="azure-sql-database"></a>Azure SQL Database
Azure SQL Database 也支援透明資料加密 (TDE)，TDE 可在不需變更應用程式的情況下，對資料執行即時加密和解密，協助防止惡意活動的威脅。 用戶端並不會察覺到這個過程。 如需詳細資訊，請參閱 [SQL Database 和資料倉儲的透明資料加密](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)。

#### <a name="azure-data-lake-store"></a>Azure Data Lake Store
Azure Data Lake Store 也針對儲存在帳戶中的資料提供加密功能。 啟用加密功能時，Data Lake Store 會在保存資料之前先自動加密資料，並在擷取資料之前先解密資料，因此存取該資料的用戶端並不會察覺這個過程。 如需詳細資訊，請參閱 [Azure Data Lake Store 安全性](../data-lake-store/data-lake-store-security-overview.md)。 

#### <a name="azure-blob-storage-and-azure-table-storage"></a>Azure Blob 儲存體和 Azure 資料表儲存體
「Azure Blob 儲存體」和「Azure 資料表儲存體」支援「儲存體服務加密」(SSE)，此功能會在將資料保存到儲存體之前先自動加密資料，並在擷取資料之前先解密資料。 如需詳細資訊，請參閱[待用資料的 Azure 儲存體服務加密](../storage/common/storage-service-encryption.md)。

#### <a name="amazon-s3"></a>Amazon S3
Amazon S3 同時支援用戶端和伺服器的待用資料加密。 如需詳細資訊，請參閱[使用加密來保護資料 (英文)](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingEncryption.html)。

#### <a name="amazon-redshift"></a>Amazon Redshift
Amazon Redshift 支援叢集待用資料加密。 如需詳細資訊，請參閱 [Amazon Redshift 資料庫加密 (英文)](https://docs.aws.amazon.com/redshift/latest/mgmt/working-with-db-encryption.html)。 

#### <a name="salesforce"></a>Salesforce
Salesforce 支援「Shield 平台加密」，可加密所有檔案、附件和自訂欄位。 如需詳細資訊，請參閱[了解 Web 伺服器 OAuth 驗證流程 (英文)](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/intro_understanding_web_server_oauth_flow.htm)。  

## <a name="hybrid-scenarios"></a>混合式案例
混合式案例需要您在內部部署網路中或是虛擬網路 (Azure) 或虛擬私人雲端 (Amazon) 內安裝自我裝載整合執行階段。 自我裝載整合執行階段必須能夠存取本機資料存放區。 如需自我裝載整合執行階段的詳細資訊，請參閱[如何建立和設定自我裝載整合執行階段](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)。 

![自我裝載整合執行階段通道](media/data-movement-security-considerations/data-management-gateway-channels.png)

命令通道可允許 Data Factory 中的資料移動服務與自我裝載整合執行階段之間進行通訊。 此通訊包含活動的相關資訊。 資料通道會用來在內部部署資料存放區與雲端資料存放區之間傳輸資料。    

### <a name="on-premises-data-store-credentials"></a>內部部署資料存放區認證
認證可以儲存在資料工廠中,也可以在 Azure 金鑰保管庫的執行時[由資料工廠參考](store-credentials-in-key-vault.md)。 如果在數據工廠中存儲憑據,則始終在自託管的集成運行時加密存儲憑據。 
 
- **在本機儲存認證**。 如果您直接使用**集-AzDataFactoryV2LinkService** cmdlet 與 JSON 中內聯的連接字串和憑據,則連結服務將被加密並存儲在自託管集成運行時。  在這種情況下,憑據會通過 Azure 後端服務(非常安全)流向自託管集成計算機,最終對其進行加密和存儲。 自我裝載整合執行階段會使用 Windows [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx) 加密機密資料和認證資訊。

- **在 Azure 金鑰保存的憑證中儲存認證**。 您也可以在 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 中儲存資料存放區的認證。 Data Factory 會在活動執行期間擷取認證。 如需詳細資訊，請參閱 [在 Azure Key Vault 中儲存認證](store-credentials-in-key-vault.md)。

- **在本地儲存認證,而不將認證透過 Azure 後端流向自託管的整合時**。 如果要在自託管的集成運行時本地加密和存儲憑據,而無需通過數據工廠後端流憑據,請按照[Azure 數據工廠 中本地數據存儲的加密憑據](encrypt-credentials-self-hosted-integration-runtime.md)中的步驟操作。 所有連接器皆支援此選項。 自我裝載整合執行階段會使用 Windows [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx) 加密機密資料和認證資訊。 

   使用**New-AzDataFactoryV2LinkServiceEncrypt 認證**連結服務認證和連結服務中的敏感詳細資訊。 然後,可以使用返回的 JSON(連接字串中的**加密認證認證認證**)使用**Set-AzDataFactoryV2LinkService** cmdlet 創建連結服務。  


#### <a name="ports-used-when-encrypting-linked-service-on-self-hosted-integration-runtime"></a>在自我裝載整合執行階段上加密連結服務時所使用的連接埠
默認情況下,PowerShell 在電腦上使用埠 8060,具有自託管整合時,用於安全通信。 您可以視需要變更此連接埠。  

![閘道的 HTTPS 連接埠](media/data-movement-security-considerations/https-port-for-gateway.png)

 


### <a name="encryption-in-transit"></a>傳輸中加密
與 Azure 服務進行通訊時，所有資料傳輸都會透過安全通道 HTTPS 和 TLS over TCP，以防止攔截式攻擊。

您也可以使用 [IPSec VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md) 或 [Azure ExpressRoute](../expressroute/expressroute-introduction.md) 來進一步保護內部部署網路與 Azure 之間的通訊通道。

Azure 虛擬網路是您網路在雲端的邏輯呈現方式。 您可以透過設定 IPSec VPN (網站間) 或 ExpressRoute (私用對等互連)，將內部部署網路連線到虛擬網路。    

下表根據混合式資料移動的不同來源和目的地位置組合，摘要說明網路和自我裝載整合執行階段組態的建議事項。

| 來源      | Destination                              | 網路組態                    | 整合執行階段設定                |
| ----------- | ---------------------------------------- | ---------------------------------------- | ---------------------------------------- |
| 內部部署 | 部署在虛擬網路中的虛擬機器和雲端服務 | IPSec VPN (點對站或站台對站台) | 自託管的整合執行時應安裝在虛擬網路中的 Azure 虛擬機器上。  |
| 內部部署 | 部署在虛擬網路中的虛擬機器和雲端服務 | ExpressRoute (私用對等互連)           | 自託管的整合執行時應安裝在虛擬網路中的 Azure 虛擬機器上。  |
| 內部部署 | 具有公用端點的 Azure 型服務 | 快速路由(微軟對等)            | 可本地或 Azure 虛擬機器上安裝自託管整合時。 |

下列各圖說明在使用自我裝載整合執行階段的情況下，利用 ExpressRoute 和 IPSec VPN (搭配 Azure 虛擬網路)，在內部部署資料庫與 Azure 服務之間移動資料：

**ExpressRoute**

![搭配使用 ExpressRoute 與閘道](media/data-movement-security-considerations/express-route-for-gateway.png) 

**IPSec VPN**

![IPSec VPN 搭配閘道](media/data-movement-security-considerations/ipsec-vpn-for-gateway.png)

### <a name="firewall-configurations-and-allow-list-setting-up-for-ip-addresses"></a><a name="firewall-configurations-and-allow-list-setting-up-for-ip-address-of-gateway"></a>防火牆設定並允許 IP 位址設定清單

#### <a name="firewall-requirements-for-on-premisesprivate-network"></a>內部部署/私人網路的防火牆需求    
在企業中，公司防火牆會在組織的中央路由器上執行。 Windows 防火牆則是在安裝自我裝載整合執行階段的本機電腦上以精靈的形式執行。 

下表提供公司防火牆的輸出連接埠和網域需求：

[!INCLUDE [domain-and-outbound-port-requirements](../../includes/domain-and-outbound-port-requirements.md)]

> [!NOTE] 
> 您可能需要按照相關數據源的要求,管理埠或設置公司防火牆級別的域的允許清單。 此表格僅使用 Azure SQL Database、Azure SQL 資料倉儲和 Azure Data Lake Store 作為範例。   

下表提供 Windows 防火牆的輸入連接埠需求：

| 輸入連接埠 | 描述                              |
| ------------- | ---------------------------------------- |
| 8060 (TCP)    | PowerShell 加密 Cmdlet (如[在 Azure Data Factory 中加密內部部署資料存放區的認證](encrypt-credentials-self-hosted-integration-runtime.md)中所述) 和認證管理員應用程式皆需要此連接埠，以便為自我裝載整合執行階段的內部部署資料存放區安全地設定認證。 |

![閘道連接埠需求](media/data-movement-security-considerations/gateway-port-requirements.png) 

#### <a name="ip-configurations-and-allow-list-setting-up-in-data-stores"></a>IP 設定並允許在資料儲存中設定清單
雲中的某些資料儲存還要求允許存取儲存的電腦的 IP 位址。 確保允許或正確配置防火牆中自託管整合時電腦的 IP 位址。

以下雲資料儲存要求您允許自託管整合時電腦的 IP 位址。 默認情況下,其中一些數據存儲可能不需要允許清單。 

- [Azure SQL Database](../sql-database/sql-database-firewall-configure.md) 
- [Azure SQL 資料倉儲](../sql-data-warehouse/sql-data-warehouse-get-started-provision.md)
- [Azure 資料湖儲存](../data-lake-store/data-lake-store-secure-data.md#set-ip-address-range-for-data-access)
- [Azure Cosmos DB](../cosmos-db/firewall-support.md)
- [Amazon Redshift](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) 

## <a name="frequently-asked-questions"></a>常見問題集

**是否可以跨不同的 Data Factory 共用自我裝載整合執行階段？**

是。 更多詳細資料在[這裡](https://azure.microsoft.com/blog/sharing-a-self-hosted-integration-runtime-infrastructure-with-multiple-data-factories/)。

**自我裝載整合執行階段需要什麼連接埠才能運作？**

自我裝載整合執行階段會建立 HTTP 型連線來存取網際網路。 必須開啟輸出連接埠 443，自我裝載整合執行階段才能建立此連線。 僅在憑據管理器應用程式的機器級別(而不是公司防火牆級別)打開入站埠 8060。 如果使用 Azure SQL Database 或 Azure SQL 資料倉儲作為來源或目的地，則也需要開啟連接埠 1433。 有關詳細資訊,請參閱[防火牆配置並允許設置 IP 位址清單](#firewall-configurations-and-allow-list-setting-up-for-ip-address-of-gateway)部分。 


## <a name="next-steps"></a>後續步驟
如需有關 Azure Data Factory 複製活動效能的詳細資訊，請參閱[複製活動的效能及微調指南](copy-activity-performance.md)。

 
