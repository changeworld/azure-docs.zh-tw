---
title: Azure Key Vault 的虛擬網路服務端點
description: 瞭解 Azure Key Vault 的虛擬網路服務端點如何讓您限制存取指定的虛擬網路，包括使用案例。
services: key-vault
author: amitbapat
ms.author: ambapat
manager: rkarlin
ms.date: 01/02/2019
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.openlocfilehash: 9dcabe10822fd09c8f7a0da6259d81a089c1a042
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97936289"
---
# <a name="virtual-network-service-endpoints-for-azure-key-vault"></a>Azure Key Vault 的虛擬網路服務端點

Azure Key Vault 的虛擬網路服務端點可讓您將存取權限制為指定的虛擬網路。 這些端點也可讓您將存取權限制為 IPv4 (網際網路通訊協定第 4 版) 位址範圍的清單。 任何從這些來源之外連線到金鑰保存庫的使用者都會被拒絕存取。

此限制有一個重要例外狀況。 如果使用者已選擇要允許受信任的 Microsoft 服務，則來自這些服務的連線會通過防火牆。 例如，這些服務包括 Office 365 Exchange Online、Office 365 SharePoint Online、Azure Compute、Azure Resource Manager 和 Azure 備份。 這類使用者仍必須出示有效的 Azure Active Directory 權杖，而且必須具有權限 (如存取原則設定) 才能執行要求的作業。 如需詳細資訊，請參閱[虛擬網路服務端點](../../virtual-network/virtual-network-service-endpoints-overview.md)。

## <a name="usage-scenarios"></a>使用方式情節

根據預設，您可以將 [Key Vault 防火牆和虛擬網路](network-security.md)設定成拒絕存取所有網路流量 (包括網際網路流量)。 您可以授權存取特定 Azure 虛擬網路和公用網際網路 IP 位址範圍的流量，讓您為應用程式建置安全的網路界限。

> [!NOTE]
> Key Vault 防火牆和虛擬網路規則只適用於 Key Vault 的[資料平面](secure-your-key-vault.md#data-plane-access-control)。 Key Vault 控制平面作業 (例如建立、刪除和修改作業、設定存取原則、設定防火牆和虛擬網路規則) 不受防火牆和虛擬網路規則影響。

以下是如何使用服務端點的一些範例：

* 您會使用 Key Vault 來儲存加密金鑰、應用程式祕密和憑證，而且您想要禁止從公用網際網路存取您的金鑰保存庫。
* 您想要鎖定您金鑰保存庫的存取權，以致只有您的應用程式或少數幾個指定的主機可以連線到您的金鑰保存庫。
* 您有在 Azure 虛擬網路中執行的應用程式，而此虛擬網路已針對所有的輸入和輸出流量進行鎖定。 您的應用程式仍然需要連線至 Key Vault，以擷取祕密或憑證，或使用密碼編譯金鑰。

## <a name="trusted-services"></a>信任的服務

以下是已啟用 [允許信任的服務] 選項的情況下，允許存取金鑰保存庫的信任服務清單。

|信任的服務|支援的使用案例|
| --- | --- |
|Azure 虛擬機器部署服務|[Deploy Certificates to VMs from customer-managed Key Vault (將憑證從客戶管理的 Key Vault 部署到 VM)](/archive/blogs/kv/updated-deploy-certificates-to-vms-from-customer-managed-key-vault)。|
|Azure Resource Manager 範本部署服務|[在部署期間傳遞安全的值](../../azure-resource-manager/templates/key-vault-parameter.md)。|
|Azure 應用程式閘道 v2 SKU|[使用 Key Vault 憑證的 TLS 終止](../../application-gateway/key-vault-certs.md)|
|Azure Disk Encryption 磁碟區加密服務|允許在虛擬機器部署期間，存取 BitLocker 金鑰 (Windows VM) 或 DM 複雜密碼 (Linux VM) 和金鑰加密金鑰。 這可啟用 [Azure 磁碟加密](../../security/fundamentals/encryption-overview.md)。|
|Azure 備份|使用 [Azure 備份](../../backup/backup-overview.md)，在 Azure 虛擬機器備份期間，允許相關金鑰和祕密的備份和還原。|
|Exchange Online 和 SharePoint Online|允許使用[客戶金鑰](/microsoft-365/compliance/customer-key-overview)存取 Azure 儲存體服務加密的客戶金鑰。|
|Azure 資訊保護|允許存取 [Azure 資訊保護](/azure/information-protection/what-is-information-protection)的租用戶金鑰|
|Azure App Service|[透過 Key Vault 部署 Azure Web 應用程式憑證](https://azure.github.io/AppService/2016/05/24/Deploying-Azure-Web-App-Certificate-through-Key-Vault.html)。|
|Azure SQL Database|[透明資料加密 Azure SQL Database 和 Azure Synapse Analytics 的攜帶您自己的金鑰支援](../../azure-sql/database/transparent-data-encryption-byok-overview.md?view=sql-server-2017&preserve-view=true&viewFallbackFrom=azuresqldb-current)。|
|Azure 儲存體|[儲存體服務加密在 Azure Key Vault 中使用客戶管理的金鑰](../../storage/common/customer-managed-keys-configure-key-vault.md)。|
|Azure Data Lake Store|使用客戶管理的金鑰[在 Azure Data Lake Store 中加密資料](../../data-lake-store/data-lake-store-encryption.md)。|
|Azure Databricks|[快速、簡單且可共同作業的 Apache Spark 型分析服務](/azure/databricks/scenarios/what-is-azure-databricks)|
|Azure API 管理|[使用 MSI 從 Key Vault 部署自訂網域的憑證](../../api-management/api-management-howto-use-managed-service-identity.md#use-ssl-tls-certificate-from-azure-key-vault)|
|Azure Data Factory|[從 Data Factory 提取 Key Vault 中的資料存放區認證](https://go.microsoft.com/fwlink/?linkid=2109491)|
|Azure 事件中心|[允許存取客戶管理金鑰的金鑰保存庫案例](../../event-hubs/configure-customer-managed-key.md)|
|Azure 服務匯流排|[允許存取客戶管理金鑰的金鑰保存庫案例](../../service-bus-messaging/configure-customer-managed-key.md)|
|Azure 匯入/匯出| [在 Azure Key Vault 中使用客戶管理的金鑰進行匯入/匯出服務](../../storage/common/storage-import-export-encryption-key-portal.md)
|Azure Container Registry|[使用客戶管理的金鑰進行登錄加密](../../container-registry/container-registry-customer-managed-keys.md)

> [!NOTE]
> 您必須將相關 Key Vault 存取原則設定為允許對應的服務取得 Key Vault 的存取權。

## <a name="next-steps"></a>後續步驟

- 如需逐步指示，請參閱 [設定 Azure Key Vault 防火牆和虛擬網路](network-security.md)
- 請參閱 [Azure Key Vault 安全性總覽](security-overview.md)
