---
title: Azure 安全性服務和技術 | Microsoft Docs
description: 本文提供 Azure 安全性服務和技術經策劃的清單。
services: security
documentationcenter: na
author: terrylanfear
manager: barbkess
editor: TomSh
ms.assetid: a5a7f60a-97e2-49b4-a8c5-7c010ff27ef8
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/29/2019
ms.author: terrylan
ms.openlocfilehash: 83548063a796401792bd19e7ec27be36a0efa4e4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "80549196"
---
# <a name="security-services-and-technologies-available-on-azure"></a>Azure 可用的安全性服務與技術

在我們與目前和未來的 Azure 客戶的討論中，我們經常被詢問「您有 Azure 提供的所有安全性相關服務和技術的清單嗎？」

在評估雲端服務提供者選項時，擁有此資訊是有幫助的。 所以我們提供了此清單以便您開始操作。

這份清單會隨著時間變更並成長，正如同 Azure。 請務必定期查看此頁面，掌握我們的安全性相關服務和技術。

## <a name="general-azure-security"></a>一般 Azure 安全性
|服務|描述|
|--------|--------|
|[Azure&nbsp;資訊安全&nbsp;中心](/azure/security-center/security-center-intro)| 雲端工作負載保護解決方案，可為混合式雲端工作負載提供安全性管理和進階威脅保護。|
|[Azure 金鑰保存庫](/azure/key-vault/key-vault-overview)| 安全的祕密存放區，可存放密碼、連接字串，以及讓應用程式保持運作所需的其他資訊。 |
|[Azure 監視器記錄](/azure/log-analytics/log-analytics-overview)|監視服務，可收集遙測和其他資料，並提供查詢語言和分析引擎，以提供您應用程式和資源的作業見解。 可以單獨使用，或搭配資訊安全中心等其他服務使用。 |
|[Azure 開發/測試實驗室](/azure/lab-services/devtest-lab-overview)|這項服務可協助開發人員和測試人員在 Azure 中建立快速環境，同時將浪費降至最低並控制成本。  |

<!---|[Azure&nbsp;Disk&nbsp;Encryption](/azure/azure-security-disk-encryption-overview)| THIS WILL GO TO THE NEW OVERVIEW TOPIC MEGHAN STEWART IS WRITING|--->

## <a name="storage-security"></a>儲存體安全性
|服務|描述|
|------|--------|
| [Azure&nbsp;儲存體&nbsp;服務&nbsp;加密](/azure/storage/common/storage-service-encryption)|可自動將 Azure 儲存體中的資料加密的安全性功能。   |
|[StorSimple 加密混合式儲存體](/azure/storsimple/storsimple-ova-overview)| 這是一個整合式儲存體解決方案，可管理內部部署裝置與 Azure 雲端儲存體之間的儲存體工作。|
|[Azure 用戶端加密](/azure/storage/common/storage-client-side-encryption)| 用戶端加密解決方案，可在上傳至 Azure 儲存體之前，將用戶端應用程式內的資料加密，也可在下載時將資料解密。 |
| [Azure 儲存體共用存取簽章](/azure/storage/common/storage-dotnet-shared-access-signature-part-1)|共用存取簽章可提供您儲存體帳戶中資源的委派存取。  |
|[Azure 儲存體帳戶金鑰](/azure/storage/common/storage-create-storage-account)| Azure 儲存體的存取控制方法，用於在存取儲存體帳戶時進行驗證。 |
|[使用 SMB 3.0 加密的 Azure 檔案共用](/azure/storage/files/storage-files-introduction)|網路安全性技術，可啟用伺服器訊息區塊 (SMB) 檔案共用通訊協定的網路自動加密功能。 |
|[Azure 儲存體分析](/rest/api/storageservices/Storage-Analytics)| 記錄和度量產生技術，適用於儲存體帳戶中的資料。 |

<!------>

## <a name="database-security"></a>資料庫安全性
|服務|描述|
|------|--------|
| [Azure&nbsp;SQL&nbsp;防火牆](/azure/sql-database/sql-database-firewall-configure)|網路存取控制功能，可防止對資料庫的網路型攻擊。 |
|[Azure&nbsp;SQL&nbsp;資料格&nbsp;層級加密](https://blogs.msdn.microsoft.com/sqlsecurity/2015/05/12/recommendations-for-using-cell-level-encryption-in-azure-sql-database/)| 可提供細微層級加密的資料庫安全性技術。  |
| [Azure&nbsp;SQL&nbsp;連線加密](/azure/sql-database/sql-database-control-access)|為了提供安全性，SQL Database 會透過以下機制來控制存取：依 IP 位址限制連線的防火牆規則、要求使用者證明其身分識別的驗證機制，以及將使用者限制在特定動作和資料的授權機制。 |
| [Azure SQL 一律加密](/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017)|可保護 Azure SQL Database 或 SQL Server 資料庫中儲存的敏感性資料，像是信用卡號碼或身分證號碼 (例如美國的社會安全號碼)。  |
| [Azure&nbsp;SQL&nbsp;透明資料加密](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql?view=azuresqldb-current)| 可將整個資料庫的儲存體加密的資料庫安全性功能。 |
| [Azure SQL Database 的審核](/azure/sql-database/sql-database-auditing)|資料庫稽核功能，可追蹤資料庫事件，並將它們寫入至您 Azure 儲存體帳戶中的稽核記錄。  |


## <a name="identity-and-access-management"></a>身分識別和存取管理
|服務|描述|
|------|--------|
| [Azure&nbsp;角色&nbsp;型&nbsp;存取控制](/azure/active-directory/role-based-access-control-configure)|存取控制功能，旨在根據使用者在其組織內的角色，只允許他們存取必須存取的資源。  |
| [Azure Active Directory](/azure/active-directory/fundamentals/active-directory-whatis)|雲端式驗證存放庫，可支援多租用戶、雲端式目錄，以及 Azure 內的多項身分識別管理服務。  |
| [Azure Active Directory B2C](/azure/active-directory-b2c/active-directory-b2c-overview)|身分識別管理服務，可控制客戶在使用以 Azure 為基礎的應用程式時如何註冊、登入及管理其設定檔。   |
| [Azure Active Directory 網域服務](/azure/active-directory-domain-services/overview)| Active Directory Domain Services 的雲端式受控版本。 |
| [Azure Multi-Factor Authentication](/azure/active-directory/authentication/multi-factor-authentication)| 安全性佈建，其在允許存取受保護的資訊前，採用數種不同形式的驗證。 |

## <a name="backup-and-disaster-recovery"></a>備份和災害復原
|服務|描述|
|------|--------|
| [Azure &nbsp; 備份](/azure/backup/backup-introduction-to-azure-backup)| 以 Azure 為基礎的服務，用來備份和還原 Azure 雲端中的資料。 |
| [Azure &nbsp; Site &nbsp; Recovery](/azure/site-recovery/site-recovery-overview)|一項線上服務，可將實體和虛擬機器 (VM) 上執行的工作負載從主要網站複寫到次要位置，以便在失敗後復原服務。 |

## <a name="networking"></a>網路功能
|服務|描述|
|------|--------|
| [網路 &nbsp; 安全 &nbsp; 組](/azure/virtual-network/virtual-networks-nsg)| 以網路為基礎的存取控制功能，其使用 5-tuple 來允許或拒絕決策。  |
| [Azure VPN 閘道](/azure/vpn-gateway/vpn-gateway-about-vpngateways)| 作為 VPN 端點使用的網路裝置，可允許跨單位存取 Azure 虛擬網路。  |
| [Azure 應用程式閘道](/azure/application-gateway/application-gateway-introduction)|進階 Web 應用程式負載平衡器，可根據 URL 路由傳送並執行 SSL 卸載。 |
|[Web 應用程式防火牆](/azure/frontdoor/waf-overview) (WAF) |「應用程式閘道」的一項功能，可集中保護您的 Web 應用程式，防止常見惡意探索和弱點危害|
| [Azure Load Balancer](/azure/load-balancer/load-balancer-overview)|TCP/UDP 應用程式網路負載平衡器。 |
| [Azure ExpressRoute](/azure/expressroute/expressroute-introduction)| 內部部署網路與 Azure 虛擬網路之間的專用 WAN 連結。 |
| [Azure 流量管理員](/azure/traffic-manager/traffic-manager-overview)| 全域 DNS 負載平衡器。|
| [Azure 應用程式 Proxy](/azure/active-directory/active-directory-application-proxy-get-started)| 驗證前端，用於保護內部裝載 Web 應用程式的遠端存取。 |
|[Azure 防火牆](/azure/firewall/overview)|一個受控的雲端式網路安全性服務，可保護您的「Azure 虛擬網路」資源。|
|[Azure DDoS 保護](/azure/virtual-network/ddos-protection-overview) \(部分機器翻譯\)|與應用程式設計最佳做法結合，可抵禦 DDoS 攻擊。|
|[虛擬網路服務端點](/azure/virtual-network/virtual-network-service-endpoints-overview)|可透過直接連線，將您的虛擬網路私人位址空間和您的 VNet 身分識別延伸至 Azure 服務。|