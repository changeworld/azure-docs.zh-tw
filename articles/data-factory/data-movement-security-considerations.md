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
ms.date: 05/26/2020
ms.openlocfilehash: cefbdce88d49598998e8f985821088778eee3ae8
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2021
ms.locfileid: "98117647"
---
#  <a name="security-considerations-for-data-movement-in-azure-data-factory"></a>在 Azure Data Factory 中資料移動的安全性考量
> [!div class="op_single_selector" title1="選取您目前使用的 Data Factory 服務版本："]
>
> * [第 1 版](v1/data-factory-data-movement-security-considerations.md)
> * [目前的版本](data-movement-security-considerations.md)

 [!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

本文說明 Azure Data Factory 中資料移動服務用來協助保護您資料的基本安全性基礎結構。 Data Factory 管理資源建置在 Azure 安全性基礎結構上，並使用 Azure 提供的所有可能安全性措施。

在 Data Factory 方案中，您可以建立一或多個資料 [管線](concepts-pipelines-activities.md)。 管線是共同執行一項工作的多個活動邏輯群組。 這些管線位於建立 Data Factory 的區域中。 

儘管 Data Factory 僅適用於某些區域，資料移動服務仍[全球適用](concepts-integration-runtime.md#integration-runtime-location)，以確保資料合規、有效率，且網路輸出成本降低。 

Azure Data Factory 包括 Azure Integration Runtime 和自我裝載 Integration Runtime 不會儲存任何暫存資料、快取資料或記錄，但雲端資料存放區的連結服務認證（使用憑證加密）除外。 您可以使用 Data Factory 來建立資料導向工作流程，藉由使用其他區域或內部部署環境中的[計算服務](compute-linked-services.md)，協調[所支援資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)之間的資料移動和資料處理。 您也可以藉由使用 SDK 和 Azure 監視器來監視和管理工作流程。

Data Factory 已通過下列各項規範的認證： 

| **[CSA STAR Certification](https://www.microsoft.com/trustcenter/compliance/csa-star-certification)** |
| :----------------------------------------------------------- |
| **[ISO 20000-1:2011](https://www.microsoft.com/trustcenter/Compliance/ISO-20000-1)** |
| **[ISO 22301:2012](/compliance/regulatory/offering-iso-22301)** |
| **[ISO 27001:2013](https://www.microsoft.com/trustcenter/compliance/iso-iec-27001)** |
| **[ISO 27017:2015](https://www.microsoft.com/trustcenter/compliance/iso-iec-27017)** |
| **[ISO 27018:2014](https://www.microsoft.com/trustcenter/compliance/iso-iec-27018)** |
| **[ISO 9001:2015](https://www.microsoft.com/trustcenter/compliance/iso-9001)** |
| **[SOC 1, 2, 3](https://www.microsoft.com/trustcenter/compliance/soc)** |
| **[HIPAA BAA](/compliance/regulatory/offering-hipaa-hitech)** |

如果您對 Azure 法規遵循以及 Azure 如何保護其專屬基礎結構感興趣，請瀏覽 [Microsoft 信任中心](https://microsoft.com/en-us/trustcenter/default.aspx)。 如需所有 Azure 合規性供應項目的最新清單，請查看 - https://aka.ms/AzureCompliance。

在本文中，我們會檢閱下列兩個資料移動案例中的安全性考量︰ 

- **雲端案例**：在此案例中，可透過網際網路公開存取您的來源和目的地。 這些包括受控雲端儲存體服務，例如 Azure 儲存體、Azure Synapse Analytics、Azure SQL Database、Azure Data Lake Store、Amazon S3、Amazon Redshift、SaaS 服務（例如 Salesforce），以及 web 通訊協定（例如 FTP 和 OData）。 在[支援的資料存放區和格式](copy-activity-overview.md#supported-data-stores-and-formats)中，尋找支援的資料來源完整清單。
- **混合式案例**：在此案例中，您的來源或目的地是位於防火牆後方或在內部部署公司網路內。 或者，資料存放區是在私人網路或虛擬網路 (最常見的來源) ，而且無法公開存取。 裝載在虛擬機器上的資料庫伺服器也屬於此案例的涵蓋範圍。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cloud-scenarios"></a>雲端案例

### <a name="securing-data-store-credentials"></a>保護資料存放區認證

- **在 Azure Data Factory 受控存放區中儲存加密的認證**。 Data Factory 可透過使用受 Microsoft 管理的憑證來加密資料存放區認證，為這些認證提供保護。 這些憑證每隔兩年會輪替一次 (包括憑證更新和憑證移轉)。 如需有關「Azure 儲存體」安全性的詳細資訊，請參閱 [Azure 儲存體安全性概觀](../storage/blobs/security-recommendations.md)。
- **將認證儲存在 Azure Key Vault 中**。 您也可以在 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 中儲存資料存放區的認證。 Data Factory 會在活動執行期間擷取認證。 如需詳細資訊，請參閱 [在 Azure Key Vault 中儲存認證](store-credentials-in-key-vault.md)。

### <a name="data-encryption-in-transit"></a>傳輸中資料加密
如果雲端資料存放區支援 HTTPS 或 TLS，則 Data Factory 中資料移動服務與雲端資料存放區之間的所有資料傳輸，都會透過安全通道 HTTPS 或 TLS。

> [!NOTE]
> 當資料與資料庫之間傳輸時，與 Azure SQL Database 和 Azure Synapse Analytics 的所有連接都需要加密 (SSL/TLS) 。 當您使用 JSON 編輯器來編寫管線時，在連接字串中新增 encryption 屬性，並將它設定為 **true**。 針對 Azure 儲存體，您可以在連接字串中使用 **HTTPS** 。

> [!NOTE]
> 若要在從 Oracle 移動資料時啟用傳輸中加密，請遵循下列其中一個選項：
> 1. 在 Oracle 伺服器上，移至 Oracle 進階安全性 (OAS) 並設定加密設定，其支援三重 DES 加密 (3DES) 和進階加密標準 (AES)，請參閱[這裡](https://docs.oracle.com/cd/E11882_01/network.112/e40393/asointro.htm#i1008759)的詳細資料。 ADF 會自動協商加密方法，以使用建立 Oracle 連線時您在 OAS 中設定的方法。
> 2. 在 ADF 中，您可以在連接字串 (位於連結服務內) 中新增 EncryptionMethod=1。 這將使用 SSL/TLS 作為加密方法。 若要使用這個方法，您需要在 Oracle 伺服器端的 OAS 中停用非 SSL 加密設定，以避免加密衝突。

> [!NOTE]
> 使用的 TLS 版本為 1.2。

### <a name="data-encryption-at-rest"></a>待用資料加密
有些資料存放區支援待用資料加密。 建議您為這些資料存放區啟用資料加密機制。 

#### <a name="azure-synapse-analytics"></a>Azure Synapse Analytics
Azure Synapse Analytics 中的透明資料加密 (TDE) 可針對您待用的資料執行即時加密和解密，協助防止惡意活動的威脅。 用戶端並不會察覺到這個過程。 如需詳細資訊，請參閱 [Azure Synapse Analytics 中的保護資料庫](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-manage-security.md)。

#### <a name="azure-sql-database"></a>Azure SQL Database
Azure SQL Database 也支援透明資料加密 (TDE)，TDE 可在不需變更應用程式的情況下，對資料執行即時加密和解密，協助防止惡意活動的威脅。 用戶端並不會察覺到這個過程。 如需詳細資訊，請參閱 [SQL Database 和資料倉儲的透明資料加密](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)。

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
混合式案例需要您在內部部署網路中或是虛擬網路 (Azure) 或虛擬私人雲端 (Amazon) 內安裝自我裝載整合執行階段。 自我裝載整合執行階段必須能夠存取本機資料存放區。 如需自我裝載整合執行階段的詳細資訊，請參閱[如何建立和設定自我裝載整合執行階段](./create-self-hosted-integration-runtime.md)。 

![自我裝載整合執行階段通道](media/data-movement-security-considerations/data-management-gateway-channels.png)

命令通道可允許 Data Factory 中的資料移動服務與自我裝載整合執行階段之間進行通訊。 此通訊包含活動的相關資訊。 資料通道會用來在內部部署資料存放區與雲端資料存放區之間傳輸資料。    

### <a name="on-premises-data-store-credentials"></a>內部部署資料存放區認證
認證可以儲存在 data factory 中，或 [由 data factory](store-credentials-in-key-vault.md) 在執行時間從 Azure Key Vault 中參考。 如果將認證儲存在 data factory 中，一律會在自我裝載整合執行時間上加密儲存。 
 
- **在本機儲存認證**。 如果您直接使用 **>set-azdatafactoryv2linkedservice 指令程式** 搭配在 JSON 中內嵌的連接字串和認證，連結的服務會進行加密，並儲存在自我裝載整合執行時間上。  在此情況下，認證會流經 Azure 後端服務，這非常安全，對自我裝載的整合機器而言，它最後會進行加密和儲存。 自我裝載整合執行階段會使用 Windows [DPAPI](/previous-versions/ms995355(v=msdn.10)) 加密機密資料和認證資訊。

- **將認證儲存在 Azure Key Vault 中**。 您也可以在 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 中儲存資料存放區的認證。 Data Factory 會在活動執行期間擷取認證。 如需詳細資訊，請參閱 [在 Azure Key Vault 中儲存認證](store-credentials-in-key-vault.md)。

- 將 **認證儲存在本機，而不需要透過 Azure 後端將認證傳送至自我裝載整合運行** 時間。 如果您想要在自我裝載整合執行時間的本機加密和儲存認證，而不需要透過 data factory 後端傳送認證，請依照 Azure Data Factory 中的將內部 [部署資料存放區的認證加密](encrypt-credentials-self-hosted-integration-runtime.md)的步驟進行。 所有連接器皆支援此選項。 自我裝載整合執行階段會使用 Windows [DPAPI](/previous-versions/ms995355(v=msdn.10)) 加密機密資料和認證資訊。 

   使用 **AzDataFactoryV2LinkedServiceEncryptedCredential** Cmdlet 來加密連結服務認證，以及已連結服務中的機密詳細資料。 然後，您可以在連接字串中使用以 **EncryptedCredential** 元素傳回的 JSON () ，以使用 **>set-azdatafactoryv2linkedservice** 指令程式建立連結服務。  


#### <a name="ports-used-when-encrypting-linked-service-on-self-hosted-integration-runtime"></a>在自我裝載整合執行階段上加密連結服務時所使用的連接埠
根據預設，PowerShell 會在具有自我裝載整合執行時間的電腦上使用埠8060來進行安全通訊。 您可以視需要變更此連接埠。  

![閘道的 HTTPS 連接埠](media/data-movement-security-considerations/https-port-for-gateway.png)

 


### <a name="encryption-in-transit"></a>傳輸中加密
與 Azure 服務進行通訊時，所有資料傳輸都會透過安全通道 HTTPS 和 TLS over TCP，以防止攔截式攻擊。

您也可以使用 [IPSec VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md) 或 [Azure ExpressRoute](../expressroute/expressroute-introduction.md) 來進一步保護內部部署網路與 Azure 之間的通訊通道。

Azure 虛擬網路是您網路在雲端的邏輯呈現方式。 您可以透過設定 IPSec VPN (網站間) 或 ExpressRoute (私用對等互連)，將內部部署網路連線到虛擬網路。    

下表根據混合式資料移動的不同來源和目的地位置組合，摘要說明網路和自我裝載整合執行階段組態的建議事項。

| 來源      | Destination                              | 網路組態                    | 整合執行階段設定                |
| ----------- | ---------------------------------------- | ---------------------------------------- | ---------------------------------------- |
| 內部部署 | 部署在虛擬網路中的虛擬機器和雲端服務 | IPSec VPN (點對站或站台對站台) | 自我裝載整合執行時間應該安裝在虛擬網路中的 Azure 虛擬機器上。  |
| 內部部署 | 部署在虛擬網路中的虛擬機器和雲端服務 | ExpressRoute (私用對等互連)           | 自我裝載整合執行時間應該安裝在虛擬網路中的 Azure 虛擬機器上。  |
| 內部部署 | 具有公用端點的 Azure 型服務 | ExpressRoute (Microsoft 對等互連)             | 自我裝載整合執行時間可以安裝在內部部署或 Azure 虛擬機器上。 |

下列各圖說明在使用自我裝載整合執行階段的情況下，利用 ExpressRoute 和 IPSec VPN (搭配 Azure 虛擬網路)，在內部部署資料庫與 Azure 服務之間移動資料：

**ExpressRoute**

![搭配使用 ExpressRoute 與閘道](media/data-movement-security-considerations/express-route-for-gateway.png) 

**IPSec VPN**

![IPSec VPN 搭配閘道](media/data-movement-security-considerations/ipsec-vpn-for-gateway.png)

### <a name="firewall-configurations-and-allow-list-setting-up-for-ip-addresses"></a><a name="firewall-configurations-and-allow-list-setting-up-for-ip-address-of-gateway"></a> IP 位址的防火牆設定和允許清單設定

> [!NOTE] 
> 您可能必須視個別的資料來源需求，在公司防火牆層級管理埠或設定網域的允許清單。 此資料表只會使用 Azure SQL Database、Azure Synapse Analytics 和 Azure Data Lake 存放區作為範例。

> [!NOTE] 
> 如需透過 Azure Data Factory 的資料存取策略的詳細資訊，請參閱 [這篇文章](./data-access-strategies.md#data-access-strategies-through-azure-data-factory)。

#### <a name="firewall-requirements-for-on-premisesprivate-network"></a>內部部署/私人網路的防火牆需求    
在企業中，公司防火牆會在組織的中央路由器上執行。 Windows 防火牆則是在安裝自我裝載整合執行階段的本機電腦上以精靈的形式執行。 

下表提供公司防火牆的輸出連接埠和網域需求：

[!INCLUDE [domain-and-outbound-port-requirements](../../includes/domain-and-outbound-port-requirements.md)]

> [!NOTE] 
> 您可能必須視個別的資料來源需求，在公司防火牆層級管理埠或設定網域的允許清單。 此資料表只會使用 Azure SQL Database、Azure Synapse Analytics 和 Azure Data Lake 存放區作為範例。   

下表提供 Windows 防火牆的輸入連接埠需求：

| 輸入連接埠 | 描述                              |
| ------------- | ---------------------------------------- |
| 8060 (TCP)     | PowerShell 加密 Cmdlet (如[在 Azure Data Factory 中加密內部部署資料存放區的認證](encrypt-credentials-self-hosted-integration-runtime.md)中所述) 和認證管理員應用程式皆需要此連接埠，以便為自我裝載整合執行階段的內部部署資料存放區安全地設定認證。 |

![閘道連接埠需求](media/data-movement-security-considerations/gateway-port-requirements.png) 

#### <a name="ip-configurations-and-allow-list-setting-up-in-data-stores"></a>在資料存放區中設定 IP 設定和允許清單
雲端中的某些資料存放區也會要求您允許存取存放區的電腦 IP 位址。 確定已在防火牆中適當地允許或設定自我裝載整合執行時間電腦的 IP 位址。

下列雲端資料存放區會要求您允許自我裝載整合執行時間電腦的 IP 位址。 這些資料存放區中有一些預設可能不需要允許清單。 

- [Azure SQL Database](../azure-sql/database/firewall-configure.md) 
- [Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/create-data-warehouse-portal.md)
- [Azure Data Lake Store](../data-lake-store/data-lake-store-secure-data.md#set-ip-address-range-for-data-access)
- [Azure Cosmos DB](../cosmos-db/how-to-configure-firewall.md)
- [Amazon Redshift](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) 

## <a name="frequently-asked-questions"></a>常見問題集

**是否可以跨不同的 Data Factory 共用自我裝載整合執行階段？**

是。 更多詳細資料在[這裡](https://azure.microsoft.com/blog/sharing-a-self-hosted-integration-runtime-infrastructure-with-multiple-data-factories/)。

**自我裝載整合執行階段需要什麼連接埠才能運作？**

自我裝載整合執行階段會建立 HTTP 型連線來存取網際網路。 必須開啟輸出連接埠 443，自我裝載整合執行階段才能建立此連線。 僅在電腦層級開啟輸入埠8060， (不是認證管理員應用程式的公司防火牆層級) 。 若 Azure SQL Database 或 Azure Synapse Analytics 作為來源或目的地，您也必須開啟埠1433。 如需詳細資訊，請參閱 [IP 位址的防火牆設定和允許清單設定](#firewall-configurations-and-allow-list-setting-up-for-ip-address-of-gateway) 一節。 


## <a name="next-steps"></a>後續步驟
如需有關 Azure Data Factory 複製活動效能的詳細資訊，請參閱[複製活動的效能及微調指南](copy-activity-performance.md)。

