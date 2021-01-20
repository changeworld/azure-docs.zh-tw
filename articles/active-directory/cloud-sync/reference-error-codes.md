---
title: Azure AD Connect 雲端同步處理錯誤代碼和描述
description: 雲端同步錯誤代碼的參考文章
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 01/14/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 22e677e5a86f60627552161f7b2115c08695dbf0
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/20/2021
ms.locfileid: "98613093"
---
# <a name="azure-ad-connect-cloud-sync-error-codes-and-descriptions"></a>Azure AD Connect 雲端同步處理錯誤代碼和描述
以下是錯誤碼及其描述的清單


## <a name="error-codes"></a>錯誤碼

|錯誤碼|詳細資料|狀況|解決方案|
|-----|-----|-----|-----|
|TimeOut|錯誤訊息：我們在聯繫內部部署代理程式並同步處理您的設定時，偵測到要求逾時錯誤。 如有其他與您的雲端同步處理代理程式相關的問題，請參閱我們的疑難排解指引。|要求超時。目前的超時值為10分鐘。|請參閱我們的[疑難排解指導](how-to-troubleshoot.md)方針|
|HybridSynchronizationActiveDirectoryInternalServerError|錯誤訊息：我們目前無法處理此要求。 如果此問題持續發生，請洽詢支援人員，並提供下列工作識別碼： AD2AADProvisioning. 30b500eaf9c643b2b78804e80c1421fe. 5c291d3c-d29f-4570-9d6b-f0c2fa3d5926。 其他詳細資料： HTTP 要求的處理導致例外狀況。 |無法處理在 SCIM 要求中收到搜尋要求的參數。|如需詳細資料，請參閱此例外狀況的「回應」屬性傳回的 HTTP 回應。|
|HybridIdentityServiceNoAgentsAssigned|錯誤訊息：找不到您嘗試同步之網域的作用中代理程式。請查看是否已移除代理程式。 若是如此，請重新安裝代理程式。|沒有任何代理程式正在執行。 可能已移除代理程式。 註冊新的代理程式。|「在這種情況下，您將不會在入口網站中看到任何指派給該網域的代理程式。|
|HybridIdentityServiceNoActiveAgents|錯誤訊息：找不到您嘗試同步之網域的作用中代理程式。請確認代理程式是否正在執行，方法是前往已安裝代理程式的伺服器，然後檢查服務是否正在執行「Microsoft Azure AD Cloud Sync Agent」。|「代理程式未接聽匯流排端點。 [代理程式位於不允許連接至服務匯流排的防火牆後方](../../active-directory/manage-apps/application-proxy-configure-connectors-with-proxy-servers.md#use-the-outbound-proxy-server)|
|HybridIdentityServiceInvalidResource|錯誤訊息：我們目前無法處理此要求。 如果此問題持續發生，請洽詢支援人員，並提供下列工作識別碼： AD2AADProvisioning. 3a2a0d8418f34f54a03da5b70b1f7b0c. d583d090-9cd3-4d0a-aee6-8d666658c3e9。 其他詳細資料：您的雲端同步設定似乎發生問題。 請在您的內部內部部署 AD 網域上重新註冊您的雲端同步代理程式，並從 Azure 入口網站重新開機設定。|必須設定資源名稱，讓他知道要聯繫哪個代理程式。|請在您的內部內部部署 AD 網域上重新註冊您的雲端同步代理程式，並從 Azure 入口網站重新開機設定。|
|HybridIdentityServiceAgentSignalingError|錯誤訊息：我們目前無法處理此要求。 如果此問題持續發生，請洽詢支援人員，並提供下列工作識別碼： AD2AADProvisioning. 92d2e8750f37407fa2301c9e52ad7e9b. efb835ef-62e8-42e3-b495-18d5272eb3f9。 其他詳細資料：目前無法處理此要求。 如果此問題持續發生，請從設定) 的狀態窗格中，與作業識別碼 (聯絡支援人員。|服務匯流排無法將訊息傳送給代理程式。 可能是服務匯流排中斷，或代理程式沒有回應。|如果此問題持續發生，請從設定) 的狀態窗格中，與作業識別碼 (聯絡支援人員。|
|AzureDirectoryServiceServerBusy|錯誤訊息：發生錯誤。 錯誤碼：81。 錯誤描述： Azure Active Directory 目前忙碌中。 將會自動重試此操作。 若此問題持續超過24小時，請聯絡技術支援人員。 追蹤識別碼：8a4ab3b5-3664-4278-ab64-9cff37fd3f4f 伺服器名稱：|Azure Active Directory 目前忙碌中。|若此問題持續超過24小時，請聯絡技術支援人員。|
|AzureActiveDirectoryInvalidCredential|錯誤訊息：我們發現用來執行 Azure AD Connect 雲端同步處理的服務帳戶有問題。您可以遵循 [此處](https://go.microsoft.com/fwlink/?linkid=2150988)的指示來修復雲端服務帳戶。 如果錯誤持續發生，請從設定) 的 [狀態] 窗格中，與作業識別碼 (聯絡支援人員。 其他錯誤詳細資料： CredentialsInvalid AADSTS50034： skydrive365.onmicrosoft.com 目錄中不存在使用者帳戶 {EmailHidden}。 若要登入此應用程式，必須將帳戶新增至目錄。 追蹤識別碼：14b63033-3bc9-4bd4-b871-5eb4b3500200 相互關聯識別碼：57d93ed1-be4d-483c-997c-a3b6f03deb00 時間戳記： 2021-01-12 21：08：29Z |當同步處理服務帳戶 ADToAADSyncServiceAccount 不存在於租使用者時，就會擲回此錯誤。 這可能是因為意外刪除帳戶所致。|使用 [修復 AADCloudSyncToolsAccount](reference-powershell.md#repair-aadcloudsynctoolsaccount) 來修正服務帳戶。|
|AzureActiveDirectoryExpiredCredentials|錯誤訊息：我們目前無法處理此要求。 如果此問題持續發生，請從設定) 的狀態窗格中，與作業識別碼 (聯絡支援人員。 其他錯誤詳細資料： CredentialsExpired AADSTS50055：密碼已過期。 追蹤識別碼：989b1841-dbe5-49c9-ab6c-9aa25f7b0e00 相互關聯識別碼： 1c69b196-1c3a-4381-9187-c84747807155 Timestamp： 2021-01-12 20：59：31Z | 回應狀態碼未指出成功： 401 (未授權) 。|AAD 同步服務帳戶認證已過期。|您可以遵循中的指示來修復雲端服務帳戶 https://go.microsoft.com/fwlink/?linkid=2150988 。 如果錯誤持續發生，請從設定) 的 [狀態] 窗格中，與作業識別碼 (聯絡支援人員。  其他錯誤詳細資料：已針對已過期的 OAuth 權杖交換您的系統管理 Azure Active Directory 租使用者認證。」|
|AzureActiveDirectoryAuthenticationFailed|錯誤訊息：我們目前無法處理此要求。 如果此問題持續發生，請洽詢支援人員，並提供下列工作識別碼： AD2AADProvisioning. 60b943e88f234db2b887f8cb91dee87c. 707be0d2-c6a9-405d-a3b9-de87761dc3ac。 其他詳細資料：目前無法處理此要求。 如果此問題持續發生，請從設定) 的狀態窗格中，與作業識別碼 (聯絡支援人員。 其他錯誤詳細資料： UnexpectedError。|未知的錯誤。|如果此問題持續發生，請從設定) 的狀態窗格中，與作業識別碼 (聯絡支援人員。|

## <a name="next-steps"></a>後續步驟 

- [什麼是佈建？](what-is-provisioning.md)
- [什麼是雲端同步 Azure AD Connect？](what-is-cloud-sync.md)
