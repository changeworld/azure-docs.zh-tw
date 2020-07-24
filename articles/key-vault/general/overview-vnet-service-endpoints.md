---
title: Azure Key Vault 的虛擬網路服務端點 - Azure Key Vault | Microsoft Docs
description: Key Vault 的虛擬網路服務端點概觀
services: key-vault
author: amitbapat
ms.author: ambapat
manager: rkarlin
ms.date: 01/02/2019
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.openlocfilehash: 34e1b7e58a3ceb6c1c2d2b6bc4efd34ee93e9e4e
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87090479"
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

## <a name="configure-key-vault-firewalls-and-virtual-networks"></a>設定 Key Vault 防火牆和虛擬網路

以下是設定防火牆和虛擬網路所需的步驟。 不論您是使用 PowerShell、Azure CLI 或 Azure 入口網站，都適用這些步驟。

1. 啟用[金鑰保存庫記錄](logging.md)，以查看詳細的存取記錄。 當防火牆和虛擬網路規則防止存取金鑰保存庫時，這有助於進行診斷。 (此步驟為選擇性步驟，但強烈建議執行。)
2. 針對目標虛擬網路和子網路中啟用 [金鑰保存庫的服務端點]****。
3. 設定金鑰保存庫的防火牆和虛擬網路規則，以限制從特定的虛擬網路、子網路和 IPv4 位址範圍存取該金鑰保存庫。
4. 如果有任何信任的 Microsoft 服務必須存取此金鑰保存庫，啟用此選項，讓 [信任的 Azure 服務]**** 連線能夠至 Key Vault。

如需詳細資訊，請參閱[設定 Azure Key Vault 防火牆和虛擬網路](network-security.md)。

> [!IMPORTANT]
> 防火牆規則生效後，使用者只能在其要求源自允許的虛擬網路或 IPV4 位址範圍時，才可以執行 Key Vault [資料平面](secure-your-key-vault.md#data-plane-access-control)作業。 這也適用於從 Azure 入口網站存取 Key Vault。 雖然使用者可以從 Azure 入口網站瀏覽金鑰保存庫，但是如果其用戶端電腦不在允許的清單中，他們就無法列出金鑰、祕密或憑證。 這也會影響其他 Azure 服務的 [金鑰保存庫選擇器]。 使用者可以看到金鑰保存庫清單，但是如果防火牆規則阻止其用戶端電腦，則不會列出金鑰。


> [!NOTE]
> 請留意下列組態限制：
> * 最多允許 127 條虛擬網路規則和 127 條 IPv4 規則。 
> * 不支援使用 "/31" 或 "/32" 前置詞大小的小型位址範圍。 而是，使用個別 IP 位址規則設定這些範圍。
> * 只有公用 IP 位址允許使用 IP 網路規則。 IP 規則中不允許保留私人網路的 IP 位址範圍 (如 RFC 1918 中所定義)。 私人網路包括以 **10.** 、**172.16-31** 和 **192.168.** 開頭的位址。 
> * 目前僅支援 IPv4 位址。

## <a name="trusted-services"></a>信任的服務

以下是已啟用 [允許信任的服務]**** 選項的情況下，允許存取金鑰保存庫的信任服務清單。

|信任的服務|支援的使用案例|
| --- | --- |
|Azure 虛擬機器部署服務|[Deploy Certificates to VMs from customer-managed Key Vault (將憑證從客戶管理的 Key Vault 部署到 VM)](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/)。|
|Azure Resource Manager 範本部署服務|[在部署期間傳遞安全值](../../azure-resource-manager/templates/key-vault-parameter.md)。|
|Azure 應用程式 Gateway v2 SKU|[Key Vault 憑證的 TLS 終止](/azure/application-gateway/key-vault-certs)|
|Azure Disk Encryption 磁碟區加密服務|允許在虛擬機器部署期間，存取 BitLocker 金鑰 (Windows VM) 或 DM 複雜密碼 (Linux VM) 和金鑰加密金鑰。 這可啟用 [Azure 磁碟加密](../../security/fundamentals/encryption-overview.md)。|
|Azure 備份|使用 [Azure 備份](../../backup/backup-introduction-to-azure-backup.md)，在 Azure 虛擬機器備份期間，允許相關金鑰和祕密的備份和還原。|
|Exchange Online 和 SharePoint Online|允許使用[客戶金鑰](/microsoft-365/compliance/customer-key-overview)存取 Azure 儲存體服務加密的客戶金鑰。|
|Azure 資訊保護|允許存取 [Azure 資訊保護](https://docs.microsoft.com/azure/information-protection/what-is-information-protection)的租用戶金鑰|
|Azure App Service|[透過 Key Vault 部署 Azure Web 應用程式憑證](https://azure.github.io/AppService/2016/05/24/Deploying-Azure-Web-App-Certificate-through-Key-Vault.html)。|
|Azure SQL Database|[Azure SQL Database 和資料倉儲具有「攜帶您自己的金鑰」支援的的透明資料加密](../../azure-sql/database/transparent-data-encryption-byok-overview.md?view=sql-server-2017&viewFallbackFrom=azuresqldb-current)。|
|Azure 儲存體|[儲存體服務加密在 Azure Key Vault 中使用客戶管理的金鑰](../../storage/common/storage-service-encryption-customer-managed-keys.md)。|
|Azure Data Lake Store|使用客戶管理的金鑰[在 Azure Data Lake Store 中加密資料](../../data-lake-store/data-lake-store-encryption.md)。|
|Azure Databricks|[快速、簡單且可共同作業的 Apache Spark 型分析服務](../../azure-databricks/what-is-azure-databricks.md)|
|Azure API 管理|[使用 MSI 從 Key Vault 部署自訂網域的憑證](../../api-management/api-management-howto-use-managed-service-identity.md#use-ssl-tls-certificate-from-azure-key-vault)|
|Azure Data Factory|[從 Data Factory 的 Key Vault 中提取資料存放區認證](https://go.microsoft.com/fwlink/?linkid=2109491)|
|Azure 事件中心|[針對客戶管理的金鑰案例，允許存取金鑰保存庫](https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key)|
|Azure 服務匯流排|[針對客戶管理的金鑰案例，允許存取金鑰保存庫](https://docs.microsoft.com/azure/service-bus-messaging/configure-customer-managed-key)|
|Azure 匯入/匯出| [在匯入/匯出服務的 Azure Key Vault 中使用客戶管理的金鑰](https://docs.microsoft.com/azure/storage/common/storage-import-export-encryption-key-portal)
|Azure Container Registry|[使用客戶管理的金鑰進行登錄加密](../../container-registry/container-registry-customer-managed-keys.md)<br><br/>[將成品傳輸至另一個登錄](../../container-registry/container-registry-transfer-images.md)

> [!NOTE]
> 您必須將相關 Key Vault 存取原則設定為允許對應的服務取得 Key Vault 的存取權。

## <a name="next-steps"></a>後續步驟

* [保護您的金鑰保存庫](secure-your-key-vault.md))
* [設定 Azure Key Vault 防火牆和虛擬網路](network-security.md)
