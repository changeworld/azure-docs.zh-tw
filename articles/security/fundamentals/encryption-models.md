---
title: Microsoft Azure 中的資料加密模型
description: 本文提供 Microsoft Azure 中資料加密模型的總覽。
services: security
documentationcenter: na
author: msmbaldwin
manager: rkarlin
ms.assetid: 9dcb190e-e534-4787-bf82-8ce73bf47dba
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mbaldwin
ms.openlocfilehash: 7b92c84234432320aa08017a15fbf8a5a4630eb3
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92019727"
---
# <a name="data-encryption-models"></a>資料加密模型

若要了解 Azure 中的各種資源提供者如何實作待用加密，就務必了解各種加密模式及其優缺點。 這些定義會跨 Azure 中的所有資源提供者進行共用，以確保為通用的語言和分類法。

伺服器端加密有三種情節：

- 使用服務管理之金鑰的伺服器端加密
  - Azure 資源提供者執行加密和解密作業
  - Microsoft 管理金鑰
  - 完整的雲端功能

- 在 Azure Key Vault 中使用客戶管理金鑰的伺服器端加密
  - Azure 資源提供者執行加密和解密作業
  - 客戶透過 Azure Key Vault 控制金鑰
  - 完整的雲端功能

- 在客戶控制的硬體上使用客戶管理金鑰的伺服器端加密
  - Azure 資源提供者執行加密和解密作業
  - 客戶在客戶控制的硬體上控制金鑰
  - 完整的雲端功能

伺服器端加密模型是指 Azure 服務所執行的加密。 在這個模型中，資源提供者會執行加密和解密作業。 例如，Azure 儲存體可能會在純文字作業中接收資料，並在內部執行加密和解密。 資源提供者可能會使用由 Microsoft 或客戶管理的加密金鑰，根據提供的設定而定。

![伺服器](./media/encryption-models/azure-security-encryption-atrest-fig3.png)

每個伺服器端靜態加密模型都表示金鑰管理的特殊特性。 這包括加密金鑰所建立及儲存的位置和方式，以及存取模型和金鑰輪替程序。  

針對用戶端加密，請考慮下列各項：

- Azure 服務無法查看解密的資料
- 客戶在內部部署環境 (或其他安全的儲存區中) 管理或儲存金鑰。 金鑰無法供 Azure 服務使用
- 縮減的雲端功能

Azure 中支援的加密模型會分割成兩個主要群組：如先前所述的「用戶端加密」和「伺服器端加密」。 除了所使用的加密靜態模型之外，Azure 服務一律建議使用諸如 TLS 或 HTTPS 等安全的傳輸。 因此，傳輸中的加密應該由傳輸通訊協定來處理，且不應作為要使用哪個靜態加密模型的主要判斷因素。

## <a name="client-encryption-model"></a>用戶端加密模型

用戶端加密模型是指由服務或呼叫應用程式在資源提供者或 Azure 外部執行的加密。 加密可由 Azure 中的服務應用程式或客戶資料中心內執行的應用程式執行。 在任一案例中，利用此加密模型時，Azure 資源提供者無需以任何方式解密的能力或具有加密金鑰的存取權，即可接收加密的 blob 資料。 在此模型中，金鑰管理是由呼叫服務/應用程式所完成，且對 Azure 服務不透明。

![用戶端](./media/encryption-models/azure-security-encryption-atrest-fig2.png)

## <a name="server-side-encryption-using-service-managed-keys"></a>使用服務管理金鑰的伺服器端加密

對許多客戶而言，基本需求就是確保在靜態時會將資料加密。 使用服務管理之金鑰的伺服器端加密會啟用這個模型，方法是允許客戶標示特定的資源 (儲存體帳戶、SQL DB 等) 以進行加密，並將諸如金鑰發佈、輪替和備份等所有金鑰管理層面保留給 Microsoft。 大部分支援靜態加密的 Azure 服務，通常都支援此模型將加密金鑰的管理卸載至 Azure。 Azure 資源提供者會建立金鑰、將它們放置在安全的儲存體，並在需要時加以擷取。 這表示服務具有金鑰的完整存取權，且服務可完整控制認證生命週期管理。

![受控](./media/encryption-models/azure-security-encryption-atrest-fig4.png)

使用服務管理之金鑰的伺服器端加密因此能快速解決靜態加密的需求，並為客戶提供低額外負荷。 情況允許時，客戶通常會開啟目標訂用帳戶和資源提供者的 Azure 入口網站，並勾選方塊以指示要加密資料。 在部分 Resource Manager 中，使用服務管理金鑰的伺服器端加密預設為開啟。

搭配 Microsoft 管理之金鑰的伺服器端加密確實表示服務具有儲存及管理金鑰的完整存取權。 雖然部分客戶會因為認為可獲得更高的安全性而要管理金鑰，但在評估此模型時，應該考慮自訂金鑰儲存解決方案的相關成本和風險。 在許多情況下，組織可能會判斷資源限制或內部部署解決方案的風險可能會大於在雲端管理靜態加密金鑰的風險。  不過，對於需要控制加密金鑰的建立或生命週期，或是讓不同人員管理服務的加密金鑰（而非管理服務 (也就是從) 服務的整體管理模型隔離金鑰管理）的組織而言，此模型可能不足。

### <a name="key-access"></a>金鑰存取

使用具服務管理之金鑰的伺服器端加密時，金鑰建立、儲存和存取服務都是由服務所管理。 一般而言，基本的 Azure 資源提供者會將資料加密金鑰儲存在接近資料、快速可用且可存取的儲存區中，而金鑰加密金鑰則是儲存在安全的內部儲存區中。

**優點**

- 簡單設定
- Microsoft 會管理金鑰輪替、備份與備援
- 客戶沒有與實作相關聯的成本，或自訂金鑰管理配置的風險。

**缺點**

- 沒有加密金鑰 (金鑰規格、生命週期、撤銷等) 的客戶控制權
- 無法從服務的整體管理模型將金鑰管理隔離

## <a name="server-side-encryption-using-customer-managed-keys-in-azure-key-vault"></a>在 Azure Key Vault 中使用客戶管理金鑰的伺服器端加密

在需要將待用資料加密並控制加密金鑰的情節中，客戶可以使用 Key Vault 中使用客戶管理之金鑰的伺服器端加密。 某些服務可能只會在 Azure Key Vault 中儲存根金鑰加密金鑰，並將加密的資料加密金鑰儲存在較接近資料的內部位置。 在該案例中，客戶可以將自己的金鑰帶到 Key Vault (BYOK – 自備金鑰) 或產生新的金鑰，並使用它們來加密所需的資源。 當資源提供者執行加密和解密作業時，它會使用已設定的金鑰加密金鑰做為所有加密作業的根機碼。

遺失金鑰加密金鑰表示資料遺失。 基於這個理由，不應該刪除金鑰。 每次建立或旋轉時，都應該備份金鑰。 您應該在儲存金鑰加密金鑰的任何保存庫上啟用虛[刪除](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)。 將 [啟用] 設定為 [false] 或 [設定到期日]，而不是刪除金鑰。

### <a name="key-access"></a>金鑰存取

Azure Key Vault 中使用客戶管理之金鑰的伺服器端加密模型，需讓服務視需要存取金鑰來進行加密和解密。 透過存取控制原則，服務可以存取靜態加密金鑰。 此原則授與服務身分識別存取，以接收金鑰。 代表相關聯的訂用帳戶所執行的 Azure 服務，可以使用該訂用帳戶中的身分識別來加以設定。 服務可以執行 Azure Active Directory 驗證，並接收驗證權杖，會將其本身識別為代表訂用帳戶的該服務。 接著會將該權杖提供給 Key Vault，從而取得已獲得存取權的金鑰。

針對使用加密金鑰的作業，可以授與服務識別存取任何下列作業：解密、加密、解除包裝金鑰、包裝金鑰、驗證、簽署、取得、列出、更新、建立、匯入、刪除、備份和還原。

若要取得用於將靜態資料加密或解密的金鑰，Resource Manager 服務執行個體所要執行的服務識別必須擁有「解除包裝金鑰」(可取得解密金鑰) 和「包裝金鑰」(建立新的金鑰時，可將金鑰插入保存庫金鑰)。

>[!NOTE]
>如需關於 Key Vault 授權的詳細資訊，請參閱 [Azure Key Vault 文件](../../key-vault/general/secure-your-key-vault.md)中的「保護您的金鑰保存庫」頁面。

**優點**

- 完整控制所使用的金鑰–加密金鑰會在客戶的 Key Vault 中管理于客戶的控制之下。
- 能夠將多個服務加密到一個主機
- 可從服務的整體管理模型將金鑰管理隔離
- 可跨區域定義服務與金鑰位置

**缺點**

- 客戶對於金鑰存取管理擁有完全責任
- 客戶對於金鑰生命週期管理擁有完全責任
- 安裝與設定的其他額外負荷

## <a name="server-side-encryption-using-customer-managed-keys-in-customer-controlled-hardware"></a>在客戶控制的硬體中使用客戶管理的金鑰進行伺服器端加密

某些 Azure 服務可允許「裝載您自己的金鑰」(HYOK) 金鑰管理模型。 此管理模式適用于需要加密待用資料，以及在 Microsoft 控制之外的專屬存放庫中管理金鑰的案例。 在此模型中，服務必須從外部網站擷取金鑰。 效能和可用性保證會受到影響，且設定會更為複雜。 此外，因為服務在加密和解密作業期間可存取 DEK，此模型的整體安全性保證會與在 Azure Key Vault 中由客戶管理金鑰時類似。  因此，此模型不適用於大部分的組織，除非組織有特定金鑰管理需求。 由於這些限制，大部分的 Azure 服務不支援在客戶控制的硬體中使用伺服器管理金鑰的伺服器端加密。

### <a name="key-access"></a>金鑰存取

使用在客戶控制的硬體中使用服務管理金鑰的伺服器端加密時，金鑰會保留在客戶設定的系統上。 支援這個模型的 Azure 服務可提供方法，來建立與客戶提供之金鑰儲存區的安全連線。

**優點**

- 完整控制所使用的根金鑰 – 金鑰加密會由客戶提供的儲存區進行管理
- 能夠將多個服務加密到一個主機
- 可從服務的整體管理模型將金鑰管理隔離
- 可跨區域定義服務與金鑰位置

**缺點**

- 對於金鑰儲存、安全性、效能和可用性擁有完全責任
- 對於金鑰存取管理擁有完全責任
- 對於金鑰生命週期管理擁有完全責任
- 安裝、設定和持續維護的成本高昂
- 客戶資料中心與 Azure 資料中心之間網路可用性的相依性增加。

## <a name="supporting-services"></a>支援服務
支援每個加密模型的 Azure 服務：

| 產品、功能或服務 | 使用服務管理金鑰的伺服器端   | 使用 Customer-Managed 機碼 Server-Side | 使用 Client-Managed 機碼 Client-Side  |
|----------------------------------|--------------------|-----------------------------------------|--------------------|
| **AI 與機器學習**      |                    |                    |                    |
| Azure 認知搜尋           | 是                | 是                | -                  |
| Azure 認知服務         | 是                | 是                | -                  |
| Azure Machine Learning           | 是                | 是                | -                  |
| Azure Machine Learning Studio (傳統)  | 是         | 預覽，RSA 2048 位元 | -               |
| 內容仲裁者                | 是                | 是                | -                  |
| 臉部                             | 是                | 是                | -                  |
| Language Understanding           | 是                | 是                | -                  |
| 個人化工具                     | 是                | 是                | -                  |
| QnA Maker                        | 是                | 是                | -                  |
| 語音服務                  | 是                | 是                | -                  |
| 翻譯工具文字                  | 是                | 是                | -                  |
| Power BI                         | 是                | 是，RSA 4096 位  | -                  |
| **分析**                    |                    |                    |                    |
| Azure 串流分析           | 是                | N/A\*              | -                  |
| 事件中樞                       | 是                | 是                | -                  |
| 函式                        | 是                | 是                | -                  |
| Azure Analysis Services          | 是                | -                  | -                  |
| Azure 資料目錄               | 是                | -                  | -                  |
| Azure HDInsight                  | 是                | 全部                | -                  |
| Azure 監視器 Application Insights | 是                | 是                | -                  |
| Azure 監視器 Log Analytics      | 是                | 是                | -                  |
| Azure 資料總管              | 是                | 是                | -                  |
| Azure Data Factory               | 是                | 是                | -                  |
| Azure Data Lake Store            | 是                | 是，RSA 2048 位元  | -                  |
| **容器**                   |                    |                    |                    |
| Azure Kubernetes Service         | 是                | 是                | -                  |
| Container Instances              | 是                | 是                | -                  |
| Container Registry               | 是                | 是                | -                  |
| **Compute**                      |                    |                    |                    |
| 虛擬機器                 | 是                | 是                | -                  |
| 虛擬機器擴展集        | 是                | 是                | -                  |
| SAP HANA                         | 是                | 是                | -                  |
| App Service 方案                      | 是                | 是\*\*            | -                  |
| 自動化                       | 是                | 是\*\*            | -                  |
| Azure Functions                  | 是                | 是\*\*            | -                  |
| Azure 入口網站                     | 是                | 是\*\*            | -                  |
| Logic Apps                       | 是                | 是                | -                  |
| Azure 受控應用程式       | 是                | 是\*\*            | -                  |
| 服務匯流排                      | 是                | 是                | -                  |
| Site Recovery                    | 是                | 是                | -                  |
| **資料庫**                    |                    |                    |                    |
| 虛擬機器上的 SQL Server   | 是                | 是                | 是                |
| Azure SQL Database               | 是                | 是，RSA 3072 位  | 是                |
| 適用于適用于 mariadb 的 Azure SQL Database   | 是                | -                  | -                  |
| 適用于 MySQL 的 Azure SQL Database     | 是                | 是                | -                  |
| 適用于于 postgresql 的 Azure SQL Database | 是               | 是                | -                  |
| Azure Synapse Analytics          | 是                | 是，RSA 3072 位  | -                  |
| SQL Server Stretch Database      | 是                | 是，RSA 3072 位  | 是                |
| 表格儲存體                    | 是                | 是                | 是                |
| Azure Cosmos DB                  | 是                | 是                | -                  |
| Azure Databricks                 | 是                | 是                | -                  |
| Azure 資料庫移轉服務 | 是                | N/A\*              | -                  |
| **DevOps**                       |                    |                    |                    |
| Azure DevOps Services            | 是                | -                  | 是                |
| Azure Repos                      | 是                | -                  | 是                |
| **身分識別**                     |                    |                    |                    |
| Azure Active Directory           | 是                | -                  | -                  |
| Azure Active Directory Domain Services | 是          | 是                | -                  |
| **整合**                  |                    |                    |                    |
| 服務匯流排                      | 是                | 是                | 是                |
| Event Grid                       | 是                | -                  | -                  |
| API 管理                   | 是                | -                  | -                  |
| **IoT 服務**                 |                    |                    |                    |
| IoT 中樞                          | 是                | 是                | 是                |
| IoT 中樞裝置布建      | 是                | 是                | -                  |
| **管理和治理**    |                    |                    |                    |
| Azure Site Recovery              | 是                | -                  | -                  |
| Azure Migrate                    | 是                | 是                | -                  |
| **媒體**                        |                    |                    |                    |
| 媒體服務                   | 是                | -                  | 是                |
| **安全性**                     |                    |                    |                    |
| IoT 的 Azure 資訊安全中心    | 是                | 是                | -                  |
| Azure Sentinel                   | 是                | 是                | -                  |
| **Storage**                      |                    |                    |                    |
| Blob 儲存體                     | 是                | 是                | 是                |
| Premium Blob 儲存體             | 是                | 是                | 是                |
| 磁碟儲存體                     | 是                | 是                | -                  |
| Ultra 磁碟儲存體               | 是                | 是                | -                  |
| 受控磁碟儲存體             | 是                | 是                | -                  |
| 檔案儲存體                     | 是                | 是                | -                  |
| 檔案進階儲存體             | 是                | 是                | -                  |
| 檔案同步                        | 是                | 是                | -                  |
| 佇列儲存體                    | 是                | 是                | 是                |
| Avere vFXT                       | 是                | -                  | -                  |
| Azure Cache for Redis            | 是                | N/A\*              | -                  |
| Azure NetApp Files               | 是                | 是                | -                  |
| 封存儲存體                  | 是                | 是                | -                  |
| StorSimple                       | 是                | 是                | 是                |
| Azure 備份                     | 是                | 是                | 是                |
| 資料箱                         | 是                | -                  | 是                |
| 資料箱邊緣                    | 是                | 是                | -                  |

\* 此服務不會保存資料。 暫時性快取（如果有的話）會以 Microsoft 金鑰加密。

\*\* 這項服務支援將資料儲存在您自己的 Key Vault、儲存體帳戶，或其他已支援 Customer-Managed 金鑰 Server-Side 加密的資料保存服務中。

## <a name="next-steps"></a>後續步驟

- 瞭解如何 [在 Azure 中使用加密](encryption-overview.md)。
- 瞭解 Azure 如何使用 [雙重加密](double-encryption.md) 來減輕加密資料所帶來的威脅。
