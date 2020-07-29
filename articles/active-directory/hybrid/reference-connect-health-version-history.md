---
title: Azure AD Connect Health 版本歷程記錄
description: 本文件說明 Azure AD Connect Health 的版本和已包含在這些版本中的功能。
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: daveba
editor: curtand
ms.assetid: 8dd4e998-747b-4c52-b8d3-3900fe77d88f
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 03/20/2019
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ace8d170a5ba48c00775c3b376df8bb70a337d5d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "76897045"
---
# <a name="azure-ad-connect-health-version-release-history"></a>Azure AD Connect Health︰版本發行歷程記錄
Azure Active Directory 團隊會定期以新的特性和功能更新 Azure AD Connect Health。 本文列出已發行的版本和功能。  

> [!NOTE]
> 發行新版本時，Connect Health 代理程式會自動更新。 請確定已從 Azure 入口網站啟用自動升級設定。
>

Azure AD Connect Health for Sync 會與 Azure AD Connect Health 安裝整合。 深入了解 [Azure AD Connect 版本歷程記錄](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history) 如需功能意見反應，請在 [Connect Health User Voice 頻道](https://feedback.azure.com/forums/169401-azure-active-directory/filters/new?category_id=165591)投票

## <a name="july-2019"></a>2019 年 7 月
**代理程式更新**
* AD FS 的 Azure AD Connect Health 代理程式（版本3.1.59.0） 
   1. TestWindowsTransport 中的文字變更
   2. AD FS RP 上傳的變更
   
* AD FS 的 Azure AD Connect Health 代理程式（版本3.1.56.0） 
   1. 新增 TestWindowsTransport 測試和移除 CheckOffice365Endpoints 測試中的 WsTrust 端點檢查
   2. 記錄 OS 和 .NET 資訊
   3. 將 RP 設定訊息上傳大小增加至 1 MB。
   4. Bug 修正
   
* AD DS 的 Azure AD Connect Health 代理程式（版本3.1.56.0） 
   1. 記錄 OS 和 .NET 資訊 
   2. Bug 修正

## <a name="may-2019"></a>2019 年 5 月
**代理程式更新：** 
* AD FS 的 Azure AD Connect Health 代理程式（版本3.1.51.0） 
   1. Bug 修正，以區別共用同一個用戶端要求識別碼的多個登入。
   2. Bug 修正，以剖析語言當地語系化伺服器上的錯誤使用者名稱/密碼錯誤。   

## <a name="april-2019"></a>2019 年 4 月
**代理程式更新：** 
* AD FS 的 Azure AD Connect Health 代理程式（版本3.1.46.0） 
   1. 修正檢查 ADFS 的重複 SPN 警示進程

## <a name="march-2019"></a>2019 年 3 月
**代理程式更新：** 
* AD DS 的 Azure AD Connect Health 代理程式（版本3.1.41.0）  
   1. .NET 版本集合
   2. 當遺失特定類別時，效能計數器集合的改進
   3. 錯誤修正，以防止產生多個監視代理程式實例

* AD FS 的 Azure AD Connect Health 代理程式（版本3.1.41.0） 
   1. 使用 ADFSToolBox 整合和升級 AD FS 測試腳本
   2. 執行 .NET 版本集合
   3. 當遺失特定類別時，效能計數器集合的改進
   4. 錯誤修正，以防止產生多個監視代理程式實例


## <a name="november-2018"></a>2018 年 11 月
**新的 GA 功能：** 
* Azure AD Connect Health for Sync - 診斷及修復入口網站中重複的屬性同步處理錯誤

**代理程式更新：** 
* 適用於 AD DS 的 Azure AD Connect Health 代理程式 (3.1.24.0 版) 
   1. 傳輸層安全性 (TLS) 通訊協定 1.2 版合規性和強制執行
   2. 減少通用類別警示雜訊
   3. 健康情況代理程式註冊錯誤 (bug) 修正

* 適用於 AD FS 的 Azure AD Connect Health 代理程式 (3.1.24.0 版)  
   1. 傳輸層安全性 (TLS) 通訊協定 1.2 版合規性和強制執行
   2. 對當地語系化的作業系統支援 Test-ADFSRequestToken
   3. 已解決診斷代理程式 EventHandler 鎖定問題
   4. 健康情況代理程式註冊錯誤 (bug) 修正

## <a name="august-2018"></a>2018 年 8 月 
*  隨著 Azure AD Connect 1.1.880.0 版發行的 Azure AD Connect Health Agent for Sync (3.1.7.0 版)    
   1. [具有 .NET FRAMEWORK KB 版本的監視代理程式高 CPU 問題的](https://support.microsoft.com/help/4346822/high-cpu-issue-in-azure-active-directory-connect-health-for-sync)修正程式

## <a name="june-2018"></a>2018 年 6 月 
**新的預覽功能：** 
* Azure AD Connect Health for Sync - 診斷及修復入口網站中重複的屬性同步處理錯誤 

**代理程式更新：** 
* 適用於 AD DS 的 Azure AD Connect Health 代理程式 (3.1.7.0 版)    
  1. [具有 .NET FRAMEWORK KB 版本的監視代理程式高 CPU 問題的](https://support.microsoft.com/help/4346822/high-cpu-issue-in-azure-active-directory-connect-health-for-sync)修正程式
   
* 適用於 AD FS 的 Azure AD Connect Health 代理程式 (3.1.7.0 版)  
  1. [具有 .NET FRAMEWORK KB 版本的監視代理程式高 CPU 問題的](https://support.microsoft.com/help/4346822/high-cpu-issue-in-azure-active-directory-connect-health-for-sync)修正程式
  2. ADFS Server 2016 次要伺服器上的測試結果修正
   
* 適用於 AD FS 的 Azure AD Connect Health 代理程式 (3.1.2.0 版)  
  1. 專用於 3.0.244.0 版之憶體管理和相關警示的 Hotfix


## <a name="may-2018"></a>2018 年 5 月
**代理程式更新：**
* 適用於 AD DS 的 Azure AD Connect Health 代理程式 (3.0.244.0 版)
  1. 代理程式隱私權改進  
  2. Bug 修正和一般改善

* 適用於 AD FS 的 Azure AD Connect Health 代理程式 (3.0.244.0 版)
  1. 代理程式診斷服務和相關的 PowerShell 模組改進
  2. 代理程式隱私權改進  
  3. Bug 修正和一般改善

* 隨著 Azure AD Connect 1.1.819.0 版發行的 Azure AD Connect Health Agent for Sync (3.0.164.0 版) 
  1. 代理程式隱私權改進  
  2. Bug 修正和一般改善


## <a name="march-2018"></a>2018 年 3 月
**新的預覽功能：**
* 適用於 AD FS 的 Azure AD Connect Health - 具風險的 IP 報告和警示。

**代理程式更新：**

* 適用於 AD DS 的 Azure AD Connect Health 代理程式 (3.0.176.0 版)
  1. 代理程式可用性改善 
  2. Bug 修正和一般改善
* 適用於 AD FS 的 Azure AD Connect Health 代理程式 (3.0.176.0 版)
  1. 代理程式可用性改善 
  2. Bug 修正和一般改善
* 隨著 Azure AD Connect 1.1.750.0 版發行的 Azure AD Connect Health Agent for Sync (3.0.129.0 版)  
  1. 代理程式可用性改善 
  2. Bug 修正和一般改善

## <a name="december-2017"></a>2017 年 12 月
**代理程式更新：**

* 適用於 AD DS 的 Azure AD Connect Health 代理程式 (3.0.145.0 版)
  1. 代理程式可用性改善 
  2. 已新增新的代理程式疑難排解命令
  3. Bug 修正和一般改善
* 適用於 AD FS 的 Azure AD Connect Health 代理程式 (3.0.145.0 版)
  1. 已新增新的代理程式疑難排解命令
  2. 代理程式可用性改善 
  3. Bug 修正和一般改善
  
## <a name="october-2017"></a>2017年 10 月
**代理程式更新：**

 * 隨著 Azure AD Connect 1.1.649.0 版發行的 Azure AD Connect Health Agent for Sync (3.0.129.0 版)
<br></br> 已修正 Azure AD Connect 與 Azure AD Connect Health 代理程式之間的版本相容性問題，以進行同步處理。此問題會影響正在執行 Azure AD Connect 就地升級至版本1.1.647.0 版的客戶，但目前有健康情況代理程式版本3.0.127.0。 升級之後，健康情況代理程式就不會再將有關 Azure AD Connect 同步處理服務的健康情況資料傳送至 Azure AD 健康情況服務。 透過此修正，就會在 Azure AD Connect 就地升級期間安裝健康情況代理程式 3.0.129.0 版。 健康情況代理程式 3.0.129.0 版與 Azure AD Connect 1.1.649.0 版 沒有相容性問題。

## <a name="july-2017"></a>2017 年 7 月
**代理程式更新：**

* 適用於 AD DS 的 Azure AD Connect Health 代理程式 (3.0.68.0 版)
  1. Bug 修正和一般改善
  2. Sovereign 雲端支援
* 適用於 AD FS 的 Azure AD Connect Health 代理程式 (3.0.68.0 版)
  1. Bug 修正和一般改善
  2. Sovereign 雲端支援
* 隨著 Azure AD Connect 1.1.614.0 版發行的 Azure AD Connect Health Agent for Sync (3.0.68.0 版)
  1. Microsoft Azure Government Cloud 和 Microsoft Cloud Germany 的支援

## <a name="april-2017"></a>2017 年 4 月      
**代理程式更新：**

* 適用於 AD FS 的 Azure AD Connect Health 代理程式 (3.0.12.0 版)
  1. Bug 修正和一般改善
* 適用於 AD DS 的 Azure AD Connect Health 代理程式 (3.0.12.0 版)
  1. 效能計數器上傳改善
  2. Bug 修正和一般改善

## <a name="october-2016"></a>2016 年 10 月
**代理程式更新：**

* 適用於 AD FS 的 Azure AD Connect Health 代理程式 (2.6.408.0 版)
* 改進在驗證要求中偵測用戶端 IP 位址
* 與警示相關的錯誤修正
* 適用於 AD FS 的 Azure AD Connect Health 代理程式 (2.6.408.0 版)
* 與警示相關的錯誤修正。
* 隨著 Azure AD Connect 1.1.281.0 版發行的 Azure AD Connect Health Agent for Sync (2.6.353.0 版)
* 提供同步處理錯誤報告所需的資料
* 與警示相關的錯誤修正

**新的預覽功能：**

* Azure AD Connect 的同步處理錯誤報告

**新功能︰**

* 適用於 AD FS 的 Azure AD Connect Health - IP 位址欄位可用於報告中大約前 50 位使用者名稱/密碼不正確的使用者。

## <a name="july-2016"></a>2016 年 7 月
**新的預覽功能：**

* [AD DS 的 Azure AD Connect Health](how-to-connect-health-adds.md)。

## <a name="january-2016"></a>2016 年 1 月
**代理程式更新：**

* 適用於 AD FS 的 Azure AD Connect Health 代理程式 (2.6.91.1512 版)

**新功能︰**

* [Azure AD Connect Health 代理程式的測試連線工具](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service)

## <a name="november-2015"></a>2015 年 11 月
**新功能︰**

* 支援 [角色型存取控制](how-to-connect-health-operations.md#manage-access-with-role-based-access-control)

**新的預覽功能：**

* [適用於同步的 Azure AD Connect Health](how-to-connect-health-sync.md)。

**已修正的問題：**

* 針對代理程式註冊期間看到的錯誤進行修正。

## <a name="september-2015"></a>2015 年 9 月
**新功能︰**

* AD FS 的錯誤使用者名稱密碼報告
* 支援設定未經驗證的 HTTP Proxy
* 支援在伺服器核心上設定代理程式
* 改善 AD FS 的警示
* 在適用於 AD FS 的 Azure AD Connect Health 代理程式中改善連線和資料上傳。

**已修正的問題：**

* AD FS 使用量詳細資料錯誤類型的錯誤修正。

## <a name="june-2015"></a>2015 年 6 月
**適用於 AD FS 和 AD FS Proxy 的 Azure AD Connect Health 初始版本。**

**新功能︰**

* 監視 AD FS 與 AD FS Proxy 伺服器時以電子郵件通知發出警示。
* 在 AD FS 效能計數器中輕鬆存取 AD FS 拓撲和模式。
* 依應用程式、驗證方法、要求網路位置等分組，顯示 AD FS 伺服器上成功權杖要求的趨勢。
* 依應用程式、錯誤類型等分組，顯示 AD FS 伺服器上失敗要求的趨勢。
* 使用 Azure AD 全域管理員認證更輕鬆部署代理程式。  

## <a name="next-steps"></a>後續步驟
深入了解 [在雲端中監視內部部署身分識別基礎結構和同步處理服務](whatis-hybrid-identity-health.md)。

