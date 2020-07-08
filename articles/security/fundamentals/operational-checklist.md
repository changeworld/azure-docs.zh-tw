---
title: Azure 作業安全性檢查清單 | Microsoft Docs
description: 本文提供一組 Azure 作業安全性的檢查清單。
services: security
documentationcenter: na
author: unifycloud
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: tomsh
ms.openlocfilehash: fb940857a0f88590cb9bbbf56b9e6a791299309f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "75980952"
---
# <a name="azure-operational-security-checklist"></a>Azure 作業安全性檢查清單
在 Azure 上部署應用程式很快速、輕鬆且符合成本效益。 在生產環境中部署雲端應用程式之前，針對您需要考慮的重要和建議作業安全性動作清單，取得檢查清單以協助評估應用程式是很有幫助的。

## <a name="introduction"></a>簡介

Azure 提供的基礎結構服務套件，可讓您用來部署應用程式。 Azure 作業安全性是指使用者可在 Microsoft Azure 中用來保護其資料、應用程式和其他資產的服務、控制及功能。

-   若要發揮雲端平台的最大效益，建議您運用 Azure 服務並遵循下列檢查清單。
-   在啟動之前投入時間和資源評估其應用程式之作業整備狀態的組織，會比未這麼做的組織擁有更高的滿意度。 執行這項工作時，檢查清單可用來作為一項重要機制，確保會以一致且完整的方式來評估應用程式。
-   營運評估層級會根據組織的雲端成熟度層級和應用程式的開發階段、可用性需求和資料敏感度需求而有所不同。

## <a name="checklist"></a>檢查清單

這份檢查清單旨在協助企業在 Azure 上部署複雜的企業應用程式之際，思考各種作業的安全性考量。 它也可以用來協助您建置組織的安全雲端移轉和作業策略。

|檢查清單類別| Description|
| ------------ | -------- |
| [<br>& 存取控制的安全性角色](../../security-center/security-center-planning-and-operations-guide.md)|<ul><li>使用 [角色型存取控制 (RBAC)](../../role-based-access-control/role-assignments-portal.md)可提供用來指派權限給特定範圍的使用者、群組和應用程式的使用者特定。</li></ul> |
| [<br>資料收集 & 儲存體](../../storage/blobs/security-recommendations.md)|<ul><li>透過[角色型存取控制 (RBAC)](../../role-based-access-control/role-assignments-portal.md)，使用管理平面安全性來保護您的儲存體帳戶。</li><li>資料平面安全性可使用[共用存取簽章 (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md) 和儲存的存取原則來保護您的資料存取。</li><li>使用傳輸等級加密 – 使用 HTTPS 和適用於 [Azure 檔案共用](../../storage/files/storage-dotnet-how-to-use-files.md)的 [SMB (伺服器訊息區塊通訊協定) 3.0](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) 所使用的加密。</li><li>使用[用戶端加密](../../storage/common/storage-client-side-encryption.md)可保護您需要加密金鑰的唯一控制權時，傳送給儲存體帳戶的資料。 </li><li>使用[儲存體服務加密 (SSE)](../../storage/common/storage-service-encryption.md)可將 Azure 儲存體中的資料自動加密，而使用 [Azure 磁碟加密](../azure-security-disk-encryption-overview.md)可將 OS 和資料磁碟的虛擬機器磁碟檔案進行加密。</li><li>使用 Azure [儲存體分析](https://docs.microsoft.com/rest/api/storageservices/storage-analytics)可監視授權類型；如同使用 Blob 儲存體，您可以看到使用者是否已使用共用存取簽章還是儲存體帳戶金鑰。</li><li>使用[跨原始資源共用 (CORS)](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) 可存取來自不同網域的儲存區資源。</li></ul> |
|[<br>& 建議的安全性原則](../../security-center/security-center-planning-and-operations-guide.md)|<ul><li>使用 [Azure 資訊安全中心](../../security-center/security-center-install-endpoint-protection.md)來部署端點解決方案。</li><li>新增 [Web 應用程式防火牆 (WAF)](../../application-gateway/waf-overview.md) 來保護 Web 應用程式。</li><li>   使用來自 Microsoft 合作夥伴的[防火牆](../../sentinel/connect-data-sources.md)來增加您的安全性保護。 </li><li>對您的 Azure 訂用帳戶套用安全性連絡人詳細資料;這是[Microsoft 安全性回應中心（MSRC）](https://technet.microsoft.com/security/dn528958.aspx)在發現您的客戶資料已被非法或未經授權的合作物件存取時，會與您聯繫。</li></ul> |
| [<br>身分識別 & 存取管理](identity-management-best-practices.md)|<ul><li>[使用 Azure AD 同步處理內部部署目錄與雲端目錄](../../active-directory/hybrid/whatis-hybrid-identity.md)。</li><li>使用[單一登入](https://azure.microsoft.com/resources/videos/overview-of-single-sign-on/)可讓使用者以其在 Azure AD 中的組織帳戶作為基礎，存取其 SaaS 應用程式。</li><li>使用[密碼重設登錄活動](../../active-directory/active-directory-passwords-reporting.md)報告可監視進行註冊的使用者。</li><li>對使用者啟用 [Multi-Factor Authentication (MFA)](../../active-directory/authentication/multi-factor-authentication.md)。</li><li>開發人員可以針對應用程式使用安全的身分識別功能，例如 [Microsoft 安全性開發週期 (SDL)](https://www.microsoft.com/download/details.aspx?id=12379)。</li><li>主動監視可疑的活動，方法是使用 Azure AD Premium 異常報告和 [Azure AD Identity Protection 功能](../../active-directory/identity-protection/overview.md)。</li></ul> |
|[<br>持續的安全性監視](../../security-center/security-center-planning-and-operations-guide.md)|<ul><li>使用惡意程式碼評定解決方案[Azure 監視器記錄](../../log-analytics/log-analytics-queries.md)來報告您基礎結構中的反惡意程式碼保護狀態。</li><li>使用[更新評量](../../automation/automation-update-management.md)判斷潛在安全性問題的整體風險，以及這些更新是否或如何適用於您的環境。</li><li>[身分識別和存取](../../security-center/security-center-monitoring.md)提供您的使用者概觀 </li><ul><li>使用者身分識別狀態、</li><li>嘗試登入失敗的次數，</li><li> 這些嘗試期間所使用的使用者帳戶、已鎖定的帳戶</li> <li>密碼已變更或重設的帳戶 </li><li>目前已登入的帳戶數目。</li></ul></ul> |
| [<br>Azure 資訊安全中心的偵測功能](../../security-center/security-center-alerts-overview.md#detect-threats)|<ul><li>使用[偵測功能](../../security-center/security-center-alerts-overview.md#detect-threats)，可識別目標為 Microsoft Azure 資源的作用中威脅。</li><li>使用[整合性威脅情報](https://blogs.msdn.microsoft.com/azuresecurity/2016/12/19/get-threat-intelligence-reports-with-azure-security-center/)，可利用 Microsoft 產品和服務、[Microsoft 數位犯罪防治中心 (DCU)](https://www.microsoft.com/trustcenter/security/cybercrime)、[Microsoft Security Response Center (MSRC)](response-center.md) 以及外部摘要的全域威脅情報，尋找已知的不良執行者。</li><li>使用[行為分析](https://blogs.technet.microsoft.com/enterprisemobility/2016/06/30/ata-behavior-analysis-monitoring/)，可套用已知模式來探索惡意行為。 </li><li>使用[異常偵測](https://msdn.microsoft.com/library/azure/dn913096.aspx)，可使用統計分析來建置歷程基準。</li></ul> |
| [<br>開發人員作業（DevOps）](https://docs.microsoft.com/azure/architecture/checklist/dev-ops)|<ul><li>[基礎結構即程式碼 (IaC)](https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/)是一種做法，可用來將建立及卸除網路和虛擬機器加以自動化並驗證，從而協助提供安全且穩定的應用程式裝載平台。</li><li>[持續整合及部署](/visualstudio/containers/overview#continuous-delivery-and-continuous-integration-cicd)會驅動進行中的合併和程式碼測試，以便及早找出缺失。 </li><li>[Release Management](https://msdn.microsoft.com/library/vs/alm/release/overview) 可透過您管線的每個階段管理自動化的部署。</li><li>執行中應用程式的[應用程式效能監視](https://azure.microsoft.com/documentation/articles/app-insights-start-monitoring-app-health-usage/)，包括應用程式健康情況和客戶使用方式的生產環境，協助組織形成假設並快速驗證或反駁策略。</li><li>使用[負載測試和自動縮放](https://www.visualstudio.com/docs/test/performance-testing/getting-started/getting-started-with-performance-testing)，就可以在我們的應用程式中找出效能問題，從而改善部署品質，並確保我們的應用程式一律保持最新狀態，或使用符合商務需求。</li></ul> |


## <a name="conclusion"></a>結論
許多組織已成功在 Azure 上部署和操作其雲端應用程式。 所提供的檢查清單會反白顯示幾個重要的檢查清單，協助您提高部署成功和避免免費作業的可能性。 強烈建議您在 Azure 上進行現有和新的應用程式部署時，使用這些作業和策略考量。

## <a name="next-steps"></a>後續步驟
若要深入了解安全性，請參閱下列文章：

- [設計和營運安全性](https://www.microsoft.com/trustcenter/security/designopsecurity)。
- [Azure 資訊安全中心規劃和作業](../../security-center/security-center-planning-and-operations-guide.md)。
