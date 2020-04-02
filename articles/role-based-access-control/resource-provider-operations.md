---
title: Azure Resource Manager 資源提供者作業
description: 列出 Microsoft Azure Resource Manager 資源提供者可用的作業
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/22/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: c56e6729307a1cad998a060202f51cb8f29bf94a
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548326"
---
# <a name="azure-resource-manager-resource-provider-operations"></a>Azure Resource Manager 資源提供者作業

本文會列出每個 Azure Resource Manager 資源提供者可用的作業。 這些操作可用於 Azure[自訂角色](custom-roles.md),以便對 Azure 中的資源提供精細的存取控制。 操作字串有以下格式: `{Company}.{ProviderName}/{resourceType}/{action}`。 有關資源提供程式命名空間如何映射到 Azure 服務的清單,請參閱[將資源提供程式符合到服務](../azure-resource-manager/management/azure-services-resource-providers.md)。

資源提供者作業會不斷發展。 若要取得最新的作業，請使用 [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) 或 [az provider operation list](/cli/azure/provider/operation#az-provider-operation-list)。

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="microsoftaad"></a>Microsoft.AAD

Azure 服務[:Azure 活動目錄網域服務](../active-directory-domain-services/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.AAD/unregister/action | 取消註冊網域服務 |
> | 動作 | Microsoft.AAD/register/action | 註冊網域服務 |
> |  | **域服務** |  |
> | 動作 | Microsoft.AAD/domainServices/read | 讀取網域服務 |
> | 動作 | Microsoft.AAD/domainServices/write | 寫入網域服務 |
> | 動作 | Microsoft.AAD/domainServices/delete | 刪除網域服務 |
> |  | **域服務/ou容器** |  |
> | 動作 | Microsoft.AAD/domainServices/oucontainer/read | 讀取 Ou 容器 |
> | 動作 | Microsoft.AAD/domainServices/oucontainer/write | 寫入 Ou 容器 |
> | 動作 | Microsoft.AAD/domainServices/oucontainer/delete | 刪除 Ou 容器 |
> |  | **位置/動作結果** |  |
> | 動作 | Microsoft.AAD/locations/operationresults/read |  |
> |  | **作業** |  |
> | 動作 | Microsoft.AAD/Operations/read |  |

## <a name="microsoftaadiam"></a>microsoft.aadiam

微軟監控洞察

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> |  | **診斷設定** |  |
> | 動作 | microsoft.aadiam/diagnosticsettings/write | 正在寫入診斷設定 |
> | 動作 | microsoft.aadiam/diagnosticsettings/read | 正在讀取診斷設定 |
> | 動作 | microsoft.aadiam/diagnosticsettings/delete | 正在刪除診斷設定 |
> |  | **診斷設定類別** |  |
> | 動作 | microsoft.aadiam/diagnosticsettingscategories/read | 正在讀取診斷設定類別 |
> |  | **指標定義** |  |
> | 動作 | 微軟.aadiam/指標定義/讀 | 讀取租戶級指標定義 |
> |  | **指標** |  |
> | 動作 | 微軟.aadiam/指標/閱讀 | 讀取租戶級指標 |

## <a name="microsoftaddons"></a>Microsoft.Addons

Azure 服務:核心

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.Addons/register/action | 向 Microsoft.Addons 註冊指定的訂用帳戶 |
> |  | **操作** |  |
> | 動作 | Microsoft.Addons/operations/read | 取得支援的 RP 作業。 |
> |  | **supportProviders** |  |
> | 動作 | Microsoft.Addons/supportProviders/listsupportplaninfo/action | 列出指定訂用帳戶目前的支援方案資訊。 |
> |  | **支援提供者/支援計畫類型** |  |
> | 動作 | Microsoft.Addons/supportProviders/supportPlanTypes/read | 取得指定的 Canonical 支援方案狀態。 |
> | 動作 | Microsoft.Addons/supportProviders/supportPlanTypes/write | 新增指定的 Canonical 支援方案類型。 |
> | 動作 | Microsoft.Addons/supportProviders/supportPlanTypes/delete | 移除指定的 Canonical 支援方案 |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

Azure 服務[:Azure 活動目錄](../active-directory/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.ADHybridHealthService/configuration/action | 更新租用戶組態。 |
> | 動作 | Microsoft.ADHybridHealthService/services/action | 更新租用戶中的服務執行個體。 |
> | 動作 | Microsoft.ADHybridHealthService/addsservices/action | 為租用戶建立新的樹系。 |
> | 動作 | Microsoft.ADHybridHealthService/register/action | 註冊 ADHybrid 健康情況服務資源提供者，並允許建立 ADHybrid 健康情況服務資源。 |
> | 動作 | Microsoft.ADHybridHealthService/unregister/action | 將 ADHybrid 健康情況服務資源提供者的訂用帳戶取消註冊。 |
> |  | **addsservices** |  |
> | 動作 | Microsoft.ADHybridHealthService/addsservices/write | 建立或更新租用戶的 ADDomainService 執行個體。 |
> | 動作 | Microsoft.ADHybridHealthService/addsservices/servicemembers/action | 對服務新增伺服器執行個體。 |
> | 動作 | Microsoft.ADHybridHealthService/addsservices/read | 取得指定服務名稱的服務詳細資料。 |
> | 動作 | Microsoft.ADHybridHealthService/addsservices/delete | 刪除服務及其伺服器以及健康情況資料。 |
> |  | **新增服務/廣告網域服務成員** |  |
> | 動作 | Microsoft.ADHybridHealthService/addsservices/addomainservicemembers/read | 取得指定服務名稱的所有伺服器。 |
> |  | **新增服務/警報** |  |
> | 動作 | Microsoft.ADHybridHealthService/addsservices/alerts/read | 取得樹系的警示詳細資料，例如 alertid、警示引發日期、上次偵測到的警示、警示說明、上次更新日期、警示層級、警示狀態、警示疑難排解連結等等。 |
> |  | **新增服務/設定** |  |
> | 動作 | Microsoft.ADHybridHealthService/addsservices/configuration/read | 取得樹系的服務組態。 範例 - 樹系名稱、功能等級、網域命名主機 FSMO 角色、架構主機 FSMO 角色等等。 |
> |  | **新增服務/維度** |  |
> | 動作 | Microsoft.ADHybridHealthService/addsservices/dimensions/read | 取得樹系的網域和網站詳細資料。 範例 - 健康情況狀態、作用中警示、已解決的警示、屬性 (例如，網域功能等級、樹系、基礎結構主機、PDC、RID 主機等等)。  |
> |  | **新增服務/功能/使用者偏好設定** |  |
> | 動作 | Microsoft.ADHybridHealthService/addsservices/features/userpreference/read | 取得樹系的使用者喜好設定。<br>範例 - MetricCounterName，例如 ldapsuccessfulbinds、ntlmauthentications、kerberosauthentications、addsinsightsagentprivatebytes、ldapsearches。<br>UI 圖表等項目的設定。 |
> |  | **新增服務/林摘要** |  |
> | 動作 | Microsoft.ADHybridHealthService/addsservices/forestsummary/read | 取得指定樹系的樹系摘要，例如樹系名稱、此樹系下的網域數目、網站數目和網站詳細資料等。 |
> |  | **新增服務/指標中繼資料** |  |
> | 動作 | Microsoft.ADHybridHealthService/addsservices/metricmetadata/read | 取得指定服務的支援計量清單。<br>例如，ADFS 服務的外部網路帳戶鎖定、失敗要求總數、未處理的權杖要求數 (Proxy)、每秒權杖要求數等等。<br>ADDomainService 的每秒 NTLM 驗證數、每秒 LDAP 成功繫結數、LDAP 繫結時間、LDAP 作用中執行緒、每秒 Kerberos 驗證數、ATQ 執行緒總數等。<br>ADSync 服務的執行設定檔延遲、所建立的 TCP 連線數、Insights 代理程式私用位元組數、將統計資料匯出至 Azure AD。 |
> |  | **新增服務/指標/群組** |  |
> | 動作 | Microsoft.ADHybridHealthService/addsservices/metrics/groups/read | 在指定了服務時，此 API 會取得計量資訊。<br>例如，此 API 可用來取得與下列項目相關的資訊：ADFederation 服務的外部網路帳戶鎖定、失敗要求總數、未處理的權杖要求數 (Proxy)、每秒權杖要求數等等。<br>ADDomain 服務的每秒 NTLM 驗證數、每秒 LDAP 成功繫結數、LDAP 繫結時間、LDAP 作用中執行緒、每秒 Kerberos 驗證數、ATQ 執行緒總數等。<br>Sync 服務的執行設定檔延遲、所建立的 TCP 連線數、Insights 代理程式私用位元組數、將統計資料匯出至 Azure AD。 |
> |  | **新增服務/進階檢查** |  |
> | 動作 | Microsoft.ADHybridHealthService/addsservices/premiumcheck/read | 此 API 會取得進階租用戶所有已上架 ADDomainServices 的清單。 |
> |  | **新增服務/複製詳細資訊** |  |
> | 動作 | Microsoft.ADHybridHealthService/addsservices/replicationdetails/read | 取得指定服務名稱的所有伺服器複寫詳細資料。 |
> |  | **新增服務/複製狀態** |  |
> | 動作 | Microsoft.ADHybridHealthService/addsservices/replicationstatus/read | 取得網域控制站數目和其存在的任何複寫錯誤。 |
> |  | **新增服務/複製摘要** |  |
> | 動作 | Microsoft.ADHybridHealthService/addsservices/replicationsummary/read | 取得完整的網域控制站清單，以及指定樹系的複寫詳細資料。 |
> |  | **新增服務/服務成員** |  |
> | 動作 | Microsoft.ADHybridHealthService/addsservices/servicemembers/delete | 刪除指定服務和租用戶的伺服器。 |
> |  | **新增服務/服務成員/認證** |  |
> | 動作 | Microsoft.ADHybridHealthService/addsservices/servicemembers/credentials/read | 在 ADDomainService 的伺服器註冊期間，會呼叫此 API 來取得用於將新伺服器上架的認證。 |
> |  | **設定** |  |
> | 動作 | Microsoft.ADHybridHealthService/configuration/write | 建立租用戶組態。 |
> | 動作 | Microsoft.ADHybridHealthService/configuration/read | 讀取租用戶組態。 |
> |  | **紀錄** |  |
> | 動作 | Microsoft.ADHybridHealthService/logs/read | 取得租用戶的代理程式安裝和註冊記錄。 |
> |  | **紀錄/內容** |  |
> | 動作 | Microsoft.ADHybridHealthService/logs/contents/read | 取得 Blob 中所儲存的代理程式安裝和註冊記錄內容。 |
> |  | **操作** |  |
> | 動作 | Microsoft.ADHybridHealthService/operations/read | 取得系統所支援作業的清單。 |
> |  | **報告/可用部署** |  |
> | 動作 | Microsoft.ADHybridHealthService/reports/availabledeployments/read | 取得可用區域清單，以供 DevOps 用來支援客戶事件。 |
> |  | **報告/壞密碼** |  |
> | 動作 | Microsoft.ADHybridHealthService/reports/badpassword/read | 取得 Active Directory Federation Service 中所有使用者的錯誤密碼嘗試清單。 |
> |  | **報告/壞密碼使用者頻率** |  |
> | 動作 | Microsoft.ADHybridHealthService/reports/badpassworduseridipfrequency/read | 取得 Blob SAS URI，其包含新近排入佇列的報告作業狀態和最終結果，執行該作業是為了了解指定租用戶每個 UserId、每個 IPAddress、每天發生的錯誤使用者名稱/密碼嘗試頻率。 |
> |  | **報告/同意** |  |
> | 動作 | Microsoft.ADHybridHealthService/reports/consentedtodevopstenants/read | 取得 DevOps 同意的租用戶清單。 通常用於客戶支援。 |
> |  | **報告/isdevops** |  |
> | 動作 | Microsoft.ADHybridHealthService/reports/isdevops/read | 取得可指出租用戶是否已獲得 DevOps 同意的值。 |
> |  | **報告/選擇計劃** |  |
> | 動作 | Microsoft.ADHybridHealthService/reports/selectdevopstenant/read | 更新所選 Dev Ops 租用戶的 userid (objectid)。 |
> |  | **報告/選取的部署** |  |
> | 動作 | Microsoft.ADHybridHealthService/reports/selecteddeployment/read | 取得指定租用戶所選取的部署。 |
> |  | **報告/租戶分配部署** |  |
> | 動作 | Microsoft.ADHybridHealthService/reports/tenantassigneddeployment/read | 在指定了租用戶識別碼時，會取得租用戶儲存位置。 |
> |  | **報告/更新選取的部署** |  |
> | 動作 | Microsoft.ADHybridHealthService/reports/updateselecteddeployment/read | 取得會存取資料的地理位置。 |
> |  | **服務** |  |
> | 動作 | Microsoft.ADHybridHealthService/services/write | 建立租用戶中的服務執行個體。 |
> | 動作 | Microsoft.ADHybridHealthService/services/read | 讀取租用戶中的服務執行個體。 |
> | 動作 | Microsoft.ADHybridHealthService/services/delete | 刪除租用戶中的服務執行個體。 |
> | 動作 | Microsoft.ADHybridHealthService/services/servicemembers/action | 在服務中建立伺服器執行個體。 |
> |  | **服務/警報** |  |
> | 動作 | Microsoft.ADHybridHealthService/services/alerts/read | 讀取服務的警示。 |
> | 動作 | Microsoft.ADHybridHealthService/services/alerts/read | 讀取服務的警示。 |
> |  | **服務/檢查服務功能** |  |
> | 動作 | Microsoft.ADHybridHealthService/services/checkservicefeatureavailibility/read | 在指定了功能名稱時，會確認服務是否已備妥一切所需而可使用該功能。 |
> |  | **服務/匯出錯誤** |  |
> | 動作 | Microsoft.ADHybridHealthService/services/exporterrors/read | 取得指定 Sync 服務的匯出錯誤。 |
> |  | **服務/匯出狀態** |  |
> | 動作 | Microsoft.ADHybridHealthService/services/exportstatus/read | 取得指定服務的匯出狀態。 |
> |  | **服務/回饋類型/回饋** |  |
> | 動作 | Microsoft.ADHybridHealthService/services/feedbacktype/feedback/read | 取得指定服務和伺服器的警示意見反應。 |
> |  | **服務/指標中繼資料** |  |
> | 動作 | Microsoft.ADHybridHealthService/services/metricmetadata/read | 取得指定服務的支援計量清單。<br>例如，ADFS 服務的外部網路帳戶鎖定、失敗要求總數、未處理的權杖要求數 (Proxy)、每秒權杖要求數等等。<br>ADDomainService 的每秒 NTLM 驗證數、每秒 LDAP 成功繫結數、LDAP 繫結時間、LDAP 作用中執行緒、每秒 Kerberos 驗證數、ATQ 執行緒總數等。<br>ADSync 服務的執行設定檔延遲、所建立的 TCP 連線數、Insights 代理程式私用位元組數、將統計資料匯出至 Azure AD。 |
> |  | **服務/指標/組** |  |
> | 動作 | Microsoft.ADHybridHealthService/services/metrics/groups/read | 在指定了服務時，此 API 會取得計量資訊。<br>例如，此 API 可用來取得與下列項目相關的資訊：ADFederation 服務的外部網路帳戶鎖定、失敗要求總數、未處理的權杖要求數 (Proxy)、每秒權杖要求數等等。<br>ADDomain 服務的每秒 NTLM 驗證數、每秒 LDAP 成功繫結數、LDAP 繫結時間、LDAP 作用中執行緒、每秒 Kerberos 驗證數、ATQ 執行緒總數等。<br>Sync 服務的執行設定檔延遲、所建立的 TCP 連線數、Insights 代理程式私用位元組數、將統計資料匯出至 Azure AD。 |
> |  | **服務/指標/組/平均值** |  |
> | 動作 | Microsoft.ADHybridHealthService/services/metrics/groups/average/read | 在指定了服務時，此 API 會取得指定服務的計量平均值。<br>例如，此 API 可用來取得與下列項目相關的資訊：ADFederation 服務的外部網路帳戶鎖定、失敗要求總數、未處理的權杖要求數 (Proxy)、每秒權杖要求數等等。<br>ADDomain 服務的每秒 NTLM 驗證數、每秒 LDAP 成功繫結數、LDAP 繫結時間、LDAP 作用中執行緒、每秒 Kerberos 驗證數、ATQ 執行緒總數等。<br>Sync 服務的執行設定檔延遲、所建立的 TCP 連線數、Insights 代理程式私用位元組數、將統計資料匯出至 Azure AD。 |
> |  | **服務/指標/組/總和** |  |
> | 動作 | Microsoft.ADHybridHealthService/services/metrics/groups/sum/read | 在指定了服務時，此 API 會取得指定服務計量的彙總檢視。<br>例如，此 API 可用來取得與下列項目相關的資訊：ADFederation 服務的外部網路帳戶鎖定、失敗要求總數、未處理的權杖要求數 (Proxy)、每秒權杖要求數等等。<br>ADDomain 服務的每秒 NTLM 驗證數、每秒 LDAP 成功繫結數、LDAP 繫結時間、LDAP 作用中執行緒、每秒 Kerberos 驗證數、ATQ 執行緒總數等。<br>Sync 服務的執行設定檔延遲、所建立的 TCP 連線數、Insights 代理程式私用位元組數、將統計資料匯出至 Azure AD。 |
> |  | **服務/監控組態** |  |
> | 動作 | Microsoft.ADHybridHealthService/services/monitoringconfiguration/write | 新增或更新服務的監視組態。 |
> |  | **服務/監控組態** |  |
> | 動作 | Microsoft.ADHybridHealthService/services/monitoringconfigurations/read | 取得指定服務的監視組態。 |
> | 動作 | Microsoft.ADHybridHealthService/services/monitoringconfigurations/write | 新增或更新服務的監視組態。 |
> |  | **服務/進階檢查** |  |
> | 動作 | Microsoft.ADHybridHealthService/services/premiumcheck/read | 此 API 會取得進階租用戶所有已上架服務的清單。 |
> |  | **服務/報告** |  |
> | 動作 | 微軟.ADHybridHealthService/服務/報告/生成BlobUri/行動 | 生成有風險的IP報告並返回指向它的URI。 |
> |  | **服務/報告/blobUris** |  |
> | 動作 | 微軟.ADHybridHealthService/服務/報告/blobUris/讀取 | 獲取過去 7 天的所有有風險的 IP 報告 URI。 |
> |  | **服務/報告/詳細資訊** |  |
> | 動作 | Microsoft.ADHybridHealthService/services/reports/details/read | 取得過去 7 天排行前 50 名有不正確密碼錯誤的使用者報告 |
> |  | **服務/服務成員** |  |
> | 動作 | Microsoft.ADHybridHealthService/services/servicemembers/read | 讀取服務中的伺服器執行個體。 |
> | 動作 | Microsoft.ADHybridHealthService/services/servicemembers/delete | 刪除服務中的伺服器執行個體。 |
> |  | **服務/服務人員/警報** |  |
> | 動作 | Microsoft.ADHybridHealthService/services/servicemembers/alerts/read | 讀取伺服器的警示。 |
> |  | **服務/服務成員/認證** |  |
> | 動作 | Microsoft.ADHybridHealthService/services/servicemembers/credentials/read | 在伺服器註冊期間，會呼叫此 API 來取得用於將新伺服器上架的認證。 |
> |  | **服務/服務人員/資料新鮮度** |  |
> | 動作 | Microsoft.ADHybridHealthService/services/servicemembers/datafreshness/read | 針對指定的伺服器，此 API 會取得伺服器所要上傳的資料類型清單，以及每個上傳的最新時間。 |
> |  | **服務/服務成員/匯出狀態** |  |
> | 動作 | Microsoft.ADHybridHealthService/services/servicemembers/exportstatus/read | 取得指定 Sync 服務的 Sync 匯出錯誤。 |
> |  | **服務/服務人員/指標** |  |
> | 動作 | 微軟.ADHybridHealthService/服務/服務人員/指標/讀取 | 取得給定服務和服務成員的連接器和運行設定檔名稱的清單。 |
> |  | **服務/服務成員/指標/組** |  |
> | 動作 | Microsoft.ADHybridHealthService/services/servicemembers/metrics/groups/read | 在指定了服務時，此 API 會取得計量資訊。<br>例如，此 API 可用來取得與下列項目相關的資訊：ADFederation 服務的外部網路帳戶鎖定、失敗要求總數、未處理的權杖要求數 (Proxy)、每秒權杖要求數等等。<br>ADDomain 服務的每秒 NTLM 驗證數、每秒 LDAP 成功繫結數、LDAP 繫結時間、LDAP 作用中執行緒、每秒 Kerberos 驗證數、ATQ 執行緒總數等。<br>Sync 服務的執行設定檔延遲、所建立的 TCP 連線數、Insights 代理程式私用位元組數、將統計資料匯出至 Azure AD。 |
> |  | **服務/服務成員/服務設定** |  |
> | 動作 | Microsoft.ADHybridHealthService/services/servicemembers/serviceconfiguration/read | 取得指定租用戶的服務組態。 |
> |  | **服務/租戶白名單** |  |
> | 動作 | Microsoft.ADHybridHealthService/services/tenantwhitelisting/read | 取得指定租用戶的功能允許清單狀態。 |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

Azure 服務[:Azure 顧問](../advisor/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.Advisor/generateRecommendations/action | 取得產生建議狀態 |
> | 動作 | Microsoft.Advisor/register/action | 針對 Microsoft Advisor 註冊訂用帳戶 |
> | 動作 | Microsoft.Advisor/unregister/action | 為 Microsoft Advisor 取消註冊訂用帳戶 |
> |  | **設定** |  |
> | 動作 | Microsoft.Advisor/configurations/read | 取得組態 |
> | 動作 | Microsoft.Advisor/configurations/write | 建立/更新設定 |
> |  | **generateRecommendations** |  |
> | 動作 | Microsoft.Advisor/generateRecommendations/read | 取得產生建議狀態 |
> |  | **元** |  |
> | 動作 | 微軟.顧問/中繼資料/閱讀 | 取得中繼資料 |
> |  | **操作** |  |
> | 動作 | Microsoft.Advisor/operations/read | 取得 Microsoft Advisor 的作業 |
> |  | **建議** |  |
> | 動作 | Microsoft.Advisor/recommendations/read | 讀取建議 |
> | 動作 | Microsoft.Advisor/recommendations/available/action | 新的建議可在微軟顧問 |
> |  | **建議/抑制** |  |
> | 動作 | Microsoft.Advisor/recommendations/suppressions/read | 取得隱藏項目 |
> | 動作 | Microsoft.Advisor/recommendations/suppressions/write | 建立/更新隱藏項目 |
> | 動作 | Microsoft.Advisor/recommendations/suppressions/delete | 刪除隱藏項目 |
> |  | **suppressions** |  |
> | 動作 | Microsoft.Advisor/suppressions/read | 取得隱藏項目 |
> | 動作 | Microsoft.Advisor/suppressions/write | 建立/更新隱藏項目 |
> | 動作 | Microsoft.Advisor/suppressions/delete | 刪除隱藏項目 |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

Azure 服務[:Azure 監視器](../azure-monitor/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.AlertsManagement/register/action | 向 Microsoft 警示管理註冊訂用帳戶 |
> |  | **actionRules** |  |
> | 動作 | Microsoft.AlertsManagement/actionRules/read | 取得輸入篩選的所有動作規則。 |
> | 動作 | Microsoft.AlertsManagement/actionRules/write | 在指定的訂用帳戶中建立或更新動作規則 |
> | 動作 | Microsoft.AlertsManagement/actionRules/delete | 在指定的訂用帳戶中刪除動作規則。 |
> |  | **警示** |  |
> | 動作 | Microsoft.AlertsManagement/alerts/read | 取得輸入篩選的所有警示。 |
> | 動作 | Microsoft.AlertsManagement/alerts/changestate/action | 變更警示的狀態。 |
> |  | **警報/診斷** |  |
> | 動作 | Microsoft.AlertsManagement/alerts/diagnostics/read | 取得警示的所有診斷 |
> |  | **警示/歷史記錄** |  |
> | 動作 | Microsoft.AlertsManagement/alerts/history/read | 取得警示的歷程記錄 |
> |  | **alertsList** |  |
> | 動作 | Microsoft.AlertsManagement/alertsList/read | 取得訂用帳戶之間輸入篩選條件的所有警示 |
> |  | **警示MetaData** |  |
> | 動作 | 微軟.警報管理/警報元數據/讀取 | 獲取輸入參數的警報元數據。 |
> |  | **alertsSummary** |  |
> | 動作 | Microsoft.AlertsManagement/alertsSummary/read | 取得警示的摘要 |
> |  | **alertsSummaryList** |  |
> | 動作 | Microsoft.AlertsManagement/alertsSummaryList/read | 取得訂用帳戶之間警示的摘要 |
> |  | **作業** |  |
> | 動作 | Microsoft.AlertsManagement/Operations/read | 讀取已提供的作業 |
> |  | **smartDetectorAlertRules** |  |
> | 動作 | 微軟.警報管理/智慧探測器警報規則/寫入 | 在給定訂閱中建立或更新智慧偵測器警示規則 |
> | 動作 | 微軟.警報管理/智慧探測器警報規則/讀取 | 取得輸入濾波器的所有智慧偵測器警示規則 |
> | 動作 | 微軟.警報管理/智慧探測器警報規則/刪除 | 移除給定訂閱中的智慧探測器警示規則 |
> |  | **smartGroups** |  |
> | 動作 | Microsoft.AlertsManagement/smartGroups/read | 取得輸入篩選的所有智慧群組 |
> | 動作 | Microsoft.AlertsManagement/smartGroups/changestate/action | 變更智慧群組的狀態 |
> |  | **群組/歷史** |  |
> | 動作 | Microsoft.AlertsManagement/smartGroups/history/read | 取得智慧群組的歷程記錄 |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

Azure 服務[:Azure 分析服務](../analysis-services/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.AnalysisServices/register/action | 註冊 Analysis Services 資源提供者。 |
> |  | **位置** |  |
> | 動作 | Microsoft.AnalysisServices/locations/checkNameAvailability/action | 確認給定的 Analysis Server 名稱有效，且並非使用中。 |
> |  | **位置/動作結果** |  |
> | 動作 | Microsoft.AnalysisServices/locations/operationresults/read | 擷取指定作業結果的資訊。 |
> |  | **位置/動作狀態** |  |
> | 動作 | Microsoft.AnalysisServices/locations/operationstatuses/read | 擷取指定作業狀態的資訊。 |
> |  | **操作** |  |
> | 動作 | Microsoft.AnalysisServices/operations/read | 擷取作業的資訊 |
> |  | **伺服器** |  |
> | 動作 | Microsoft.AnalysisServices/servers/read | 擷取所指定 Analysis Server 的資訊。 |
> | 動作 | Microsoft.AnalysisServices/servers/write | 建立或更新所指定的 Analysis Server。 |
> | 動作 | Microsoft.AnalysisServices/servers/delete | 刪除 Analysis Server。 |
> | 動作 | Microsoft.AnalysisServices/servers/suspend/action | 暫止 Analysis Server。 |
> | 動作 | Microsoft.AnalysisServices/servers/resume/action | 繼續 Analysis Server。 |
> | 動作 | Microsoft.AnalysisServices/servers/listGatewayStatus/action | 列出與伺服器相關聯的閘道狀態。 |
> |  | **伺服器/斯克** |  |
> | 動作 | Microsoft.AnalysisServices/servers/skus/read | 擷取伺服器的可用 SKU 資訊 |
> |  | **斯克庫斯** |  |
> | 動作 | Microsoft.AnalysisServices/skus/read | 擷取 SKU 的資訊 |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

Azure 服務[:API 管理](../api-management/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.ApiManagement/register/action | 針對 Microsoft.ApiManagement 資源提供者註冊訂用帳戶 |
> | 動作 | Microsoft.ApiManagement/unregister/action | 針對 Microsoft.ApiManagement 資源提供者取消註冊訂用帳戶 |
> |  | **檢查名稱可用性** |  |
> | 動作 | Microsoft.ApiManagement/checkNameAvailability/read | 檢查所提供的服務名稱是否可用 |
> |  | **操作** |  |
> | 動作 | Microsoft.ApiManagement/operations/read | 讀取可供 Microsoft.ApiManagement 資源使用的所有 API 作業 |
> |  | **報告** |  |
> | 動作 | Microsoft.ApiManagement/reports/read | 取得依時間週期、地理區域、開發人員、產品、API、作業、訂用帳戶和依需求彙總的報告。 |
> |  | **服務** |  |
> | 動作 | Microsoft.ApiManagement/service/write | 建立或更新 API 管理服務實體 |
> | 動作 | Microsoft.ApiManagement/service/read | 讀取 API 管理服務執行個體的中繼資料 |
> | 動作 | Microsoft.ApiManagement/service/delete | 刪除 API 管理服務執行個體 |
> | 動作 | Microsoft.ApiManagement/service/updatehostname/action | 設定、更新或移除 API 管理服務的自訂網域名稱 |
> | 動作 | Microsoft.ApiManagement/service/updatecertificate/action | 上傳 API 管理服務的 TLS/SSL 憑證 |
> | 動作 | Microsoft.ApiManagement/service/backup/action | 將 API 管理服務備份到使用者所提供之儲存體帳戶中的指定容器 |
> | 動作 | Microsoft.ApiManagement/service/restore/action | 從使用者所提供之儲存體帳戶中的指定容器來還原 API 管理服務 |
> | 動作 | Microsoft.ApiManagement/service/managedeployments/action | 變更 SKU/單位、新增/移除 API 管理服務的區域部署 |
> | 動作 | Microsoft.ApiManagement/service/getssotoken/action | 取得 SSO 權杖，以供用來登入 API 管理服務舊版入口網站 (登入身分為系統管理員) |
> | 動作 | Microsoft.ApiManagement/service/applynetworkconfigurationupdates/action | 更新虛擬網路中執行的 Microsoft.ApiManagement 資源，以挑選更新後的網路設定。 |
> | 動作 | Microsoft.ApiManagement/service/users/action | 註冊新的使用者 |
> | 動作 | Microsoft.ApiManagement/service/notifications/action | 將通知傳送給指定的使用者 |
> | 動作 | 微軟.Api管理/服務/閘道/操作 | 檢索閘道配置。 或更新閘道檢測信號。 |
> |  | **服務/服務** |  |
> | 動作 | Microsoft.ApiManagement/service/apis/read | 列出 API 管理服務實體的所有 API。 或獲取其識別碼指定的 API 的詳細資訊。 |
> | 動作 | Microsoft.ApiManagement/service/apis/write | 創建 API 管理服務實體的新或更新現有的指定 API。 或更新 API 管理服務實體指定 API。 |
> | 動作 | Microsoft.ApiManagement/service/apis/delete | 刪除 API 管理服務實體指定 API。 |
> |  | **服務/應用/診斷** |  |
> | 動作 | Microsoft.ApiManagement/service/apis/diagnostics/read | 列出 API 的所有診斷。 或獲取由其識別碼指定的 API 的診斷的詳細資訊。 |
> | 動作 | Microsoft.ApiManagement/service/apis/diagnostics/write | 為 API 創建新的診斷或更新現有診斷。 或更新由其識別碼指定的 API 的診斷的詳細資訊。 |
> | 動作 | Microsoft.ApiManagement/service/apis/diagnostics/delete | 從 API 中刪除指定的診斷。 |
> |  | **服務/問題** |  |
> | 動作 | Microsoft.ApiManagement/service/apis/issues/read | 列出與指定 API 關聯的所有問題。 或獲取其識別碼指定的 API 的問題詳細資訊。 |
> | 動作 | Microsoft.ApiManagement/service/apis/issues/write | 為 API 創建新問題或更新現有問題。 或更新 API 的現有問題。 |
> | 動作 | Microsoft.ApiManagement/service/apis/issues/delete | 從 API 中刪除指定的問題。 |
> |  | **服務/問題/附件** |  |
> | 動作 | Microsoft.ApiManagement/service/apis/issues/attachments/read | 列出與指定 API 關聯的問題的所有附件。 或獲取由其識別碼指定的 API 的問題附件的詳細資訊。 |
> | 動作 | Microsoft.ApiManagement/service/apis/issues/attachments/write | 在 API 中為問題創建新附件或更新現有附件。 |
> | 動作 | Microsoft.ApiManagement/service/apis/issues/attachments/delete | 從問題中刪除指定的註釋。 |
> |  | **服務/問題/評論** |  |
> | 動作 | Microsoft.ApiManagement/service/apis/issues/comments/read | 列出與指定 API 關聯的問題的所有註釋。 或獲取由其識別碼指定的 API 的問題註釋的詳細資訊。 |
> | 動作 | Microsoft.ApiManagement/service/apis/issues/comments/write | 在 API 中為問題創建新的註釋或更新現有註解。 |
> | 動作 | Microsoft.ApiManagement/service/apis/issues/comments/delete | 從問題中刪除指定的註釋。 |
> |  | **服務/服務/服務/操作** |  |
> | 動作 | Microsoft.ApiManagement/service/apis/operations/read | 列出指定 API 的操作的集合。 或獲取其識別碼指定的 API 操作的詳細資訊。 |
> | 動作 | Microsoft.ApiManagement/service/apis/operations/write | 在 API 中創建新操作或更新現有操作。 或更新其識別碼指定的 API 中操作的詳細資訊。 |
> | 動作 | Microsoft.ApiManagement/service/apis/operations/delete | 刪除 API 中的指定操作。 |
> |  | **服務/服務/服務/操作/策略** |  |
> | 動作 | Microsoft.ApiManagement/service/apis/operations/policies/read | 在 API 操作等級取得策略配置清單。 或在 API 操作等級獲取策略配置。 |
> | 動作 | Microsoft.ApiManagement/service/apis/operations/policies/write | 為 API 操作級別建立或更新策略配置。 |
> | 動作 | Microsoft.ApiManagement/service/apis/operations/policies/delete | 刪除 Api 操作中的策略配置。 |
> |  | **服務/服務/服務/操作/策略** |  |
> | 動作 | Microsoft.ApiManagement/service/apis/operations/policy/read | 在操作層級取得策略設定 |
> | 動作 | Microsoft.ApiManagement/service/apis/operations/policy/write | 在操作層級建立策略設定 |
> | 動作 | Microsoft.ApiManagement/service/apis/operations/policy/delete | 移除操作層級的策略設定 |
> |  | **服務/服務/服務/操作/標籤** |  |
> | 動作 | Microsoft.ApiManagement/service/apis/operations/tags/read | 列出與操作關聯的所有標記。 或獲取與操作關聯的標記。 |
> | 動作 | Microsoft.ApiManagement/service/apis/operations/tags/write | 將標記分配給操作。 |
> | 動作 | Microsoft.ApiManagement/service/apis/operations/tags/delete | 從操作中分離標記。 |
> |  | **服務/apis/操作通過標記** |  |
> | 動作 | Microsoft.ApiManagement/service/apis/operationsByTags/read | 列出與標記關聯的操作的集合。 |
> |  | **服務/服務/服務/策略** |  |
> | 動作 | Microsoft.ApiManagement/service/apis/policies/read | 在 API 級別獲取策略配置。 或在 API 級別獲取策略配置。 |
> | 動作 | Microsoft.ApiManagement/service/apis/policies/write | 為 API 創建或更新策略配置。 |
> | 動作 | Microsoft.ApiManagement/service/apis/policies/delete | 刪除 Api 的策略配置。 |
> |  | **服務/服務/服務/政策** |  |
> | 動作 | Microsoft.ApiManagement/service/apis/policy/read | 在 API 等級取得策略設定 |
> | 動作 | Microsoft.ApiManagement/service/apis/policy/write | 在 API 等級建立策略設定 |
> | 動作 | Microsoft.ApiManagement/service/apis/policy/delete | 移除 API 等級的策略設定 |
> |  | **服務/產品/產品** |  |
> | 動作 | Microsoft.ApiManagement/service/apis/products/read | 列出 API 參與的所有產品。 |
> |  | **服務/服務/服務/發佈** |  |
> | 動作 | Microsoft.ApiManagement/service/apis/releases/read | 列出 API 的所有版本。<br>使 API 修訂為當前時,將創建 API 版本。<br>版本還用於回滾到以前的修訂。<br>結果將被分頁,並可以受$top和$skip參數的約束。<br>或返回 API 版本的詳細資訊。 |
> | 動作 | Microsoft.ApiManagement/service/apis/releases/delete | 刪除 API 的所有版本或刪除 API 中的指定版本。 |
> | 動作 | Microsoft.ApiManagement/service/apis/releases/write | 為 API 創建新版本。 或更新其識別碼指定的 API 發表的詳細資訊。 |
> |  | **服務/服務/修訂** |  |
> | 動作 | Microsoft.ApiManagement/service/apis/revisions/read | 列出 API 的所有修訂。 |
> | 動作 | Microsoft.ApiManagement/service/apis/revisions/delete | 移除 API 的所有修訂 |
> |  | **服務/api/架構** |  |
> | 動作 | Microsoft.ApiManagement/service/apis/schemas/read | 在 API 級別獲取架構配置。 或在 API 級別獲取架構配置。 |
> | 動作 | Microsoft.ApiManagement/service/apis/schemas/write | 為 API 創建或更新架構配置。 |
> | 動作 | Microsoft.ApiManagement/service/apis/schemas/delete | 刪除 Api 的架構配置。 |
> |  | **服務/服務/圖片/標籤描述** |  |
> | 動作 | Microsoft.ApiManagement/service/apis/tagDescriptions/read | 在 API 範圍內列出所有標記說明。 模型類似於揮霍 - 標記描述在 API 級別上定義,但標記可以分配給操作或獲取 API 範圍內的標記描述 |
> | 動作 | Microsoft.ApiManagement/service/apis/tagDescriptions/write | 在 Api 的範圍內創建/更新標記說明。 |
> | 動作 | Microsoft.ApiManagement/service/apis/tagDescriptions/delete | 刪除 Api 的標記說明。 |
> |  | **服務/apis/標籤** |  |
> | 動作 | Microsoft.ApiManagement/service/apis/tags/read | 列出與 API 關聯的所有標記。 或獲取與 API 關聯的標記。 |
> | 動作 | Microsoft.ApiManagement/service/apis/tags/write | 將標記分配給 Api。 |
> | 動作 | Microsoft.ApiManagement/service/apis/tags/delete | 從 Api 分離標記。 |
> |  | **服務/apisByTags** |  |
> | 動作 | Microsoft.ApiManagement/service/apisByTags/read | 列出與標記關聯的 apis 集合。 |
> |  | **服務/apiVersionSet** |  |
> | 動作 | Microsoft.ApiManagement/service/apiVersionSets/read | 在指定的服務實例中列出 API 版本集的集合。 或獲取其識別碼指定的 Api 版本集的詳細資訊。 |
> | 動作 | Microsoft.ApiManagement/service/apiVersionSets/write | 建立或更新 Api 版本集。 或更新其識別碼指定的 Api 版本集的詳細資訊。 |
> | 動作 | Microsoft.ApiManagement/service/apiVersionSets/delete | 刪除特定的 Api 版本集。 |
> |  | **服務/apiVersionSet/版本** |  |
> | 動作 | Microsoft.ApiManagement/service/apiVersionSets/versions/read | 取得版本實體的清單 |
> |  | **服務/授權伺服器** |  |
> | 動作 | Microsoft.ApiManagement/service/authorizationServers/read | 列出在服務實例中定義的授權伺服器的集合。 或獲取授權伺服器的詳細資訊,無機密。 |
> | 動作 | Microsoft.ApiManagement/service/authorizationServers/write | 建立新的授權伺服器或更新現有授權伺服器。 或更新其識別碼指定的授權伺服器的詳細資訊。 |
> | 動作 | Microsoft.ApiManagement/service/authorizationServers/delete | 刪除特定的授權伺服器實例。 |
> | 動作 | 微軟.Api管理/服務/授權伺服器/清單秘密/操作 | 獲取授權伺服器的機密。 |
> |  | **服務/後端介面** |  |
> | 動作 | Microsoft.ApiManagement/service/backends/read | 在指定的服務實例中列出後端的集合。 或獲取其識別碼指定的後端的詳細資訊。 |
> | 動作 | Microsoft.ApiManagement/service/backends/write | 創建或更新後端介面。 或更新現有後端。 |
> | 動作 | Microsoft.ApiManagement/service/backends/delete | 刪除指定的後端介面。 |
> | 動作 | Microsoft.ApiManagement/service/backends/reconnect/action | 通知 APIM 代理在指定超時後創建到後端的新連接。 如果未指定超時,則使用2分鐘的超時。 |
> |  | **服務/快取** |  |
> | 動作 | 微軟.Api管理/服務/緩存/讀取 | 列出指定服務實例中所有外部緩存的集合。 或獲取其識別碼指定的快取的詳細資訊。 |
> | 動作 | 微軟.Api管理/服務/緩存/寫入 | 建立或更新要在 Api 管理實體中使用的外部快取。 或更新其識別碼指定的快取的詳細資訊。 |
> | 動作 | 微軟.Api管理/服務/緩存/刪除 | 刪除特定快取。 |
> |  | **服務/憑證** |  |
> | 動作 | Microsoft.ApiManagement/service/certificates/read | 列出指定服務實例中所有證書的集合。 或獲取其識別碼指定的證書的詳細資訊。 |
> | 動作 | Microsoft.ApiManagement/service/certificates/write | 創建或更新用於後端驗證的憑證。 |
> | 動作 | Microsoft.ApiManagement/service/certificates/delete | 刪除特定證書。 |
> |  | **服務/內容類型** |  |
> | 動作 | Microsoft.ApiManagement/service/contentTypes/read | 傳回內容類型清單 |
> |  | **服務/內容類型/內容項目** |  |
> | 動作 | Microsoft.ApiManagement/service/contentTypes/contentItems/read | 傳回內容項目清單或傳回內容項目詳細資料 |
> | 動作 | Microsoft.ApiManagement/service/contentTypes/contentItems/write | 建立新的內容項目或更新指定的內容項目 |
> | 動作 | Microsoft.ApiManagement/service/contentTypes/contentItems/delete | 移除指定的內容項目。 |
> |  | **服務/診斷** |  |
> | 動作 | Microsoft.ApiManagement/service/diagnostics/read | 列出 API 管理服務實例的所有診斷。 或獲取其識別碼指定的診斷的詳細資訊。 |
> | 動作 | Microsoft.ApiManagement/service/diagnostics/write | 創建新的診斷或更新現有診斷。 或更新其識別碼指定的診斷的詳細資訊。 |
> | 動作 | Microsoft.ApiManagement/service/diagnostics/delete | 刪除指定的診斷。 |
> |  | **服務/閘道** |  |
> | 動作 | 微軟.Api管理/服務/閘道/讀取 | 列出在服務實例中註冊的閘道的集合。 或獲取其識別碼指定的閘道的詳細資訊。 |
> | 動作 | 微軟.Api管理/服務/閘道/寫入 | 建立或更新要在 Api 管理實體中使用的閘道。 或更新其識別碼指定的閘道的詳細資訊。 |
> | 動作 | 微軟.Api管理/服務/閘道/刪除 | 刪除特定閘道。 |
> | 動作 | 微軟.Api管理/服務/閘道/清單鍵/操作 | 檢索閘金鑰。 |
> | 動作 | 微軟.Api管理/服務/閘道/再生鍵/操作 | 重新生成指定的閘道金鑰無效,將驗證使用它創建的任何權杖。 |
> | 動作 | 微軟.Api管理/服務/閘道/生成權杖/操作 | 獲取閘道的共享訪問授權權杖。 |
> |  | **服務/閘道/apis** |  |
> | 動作 | 微軟.Api管理/服務/閘道/圖片/讀取 | 列出與閘道關聯的 API 的集合。 |
> | 動作 | 微軟.Api管理/服務/閘道/api/寫入 | 將 API 添加到指定的閘道。 |
> | 動作 | 微軟.Api管理/服務/閘道/apis/刪除 | 從指定的閘道中刪除指定的 API。 |
> |  | **服務/閘道/主機名稱設定** |  |
> | 動作 | 微軟.Api管理/服務/閘道/主機名配置/讀取 | 列出指定閘道的主機名配置集合。 |
> |  | **服務/組** |  |
> | 動作 | Microsoft.ApiManagement/service/groups/read | 列出在服務實例中定義的組的集合。 或獲取其識別碼指定的組的詳細資訊。 |
> | 動作 | Microsoft.ApiManagement/service/groups/write | 創建或更新組。 或更新其識別碼指定的組的詳細資訊。 |
> | 動作 | Microsoft.ApiManagement/service/groups/delete | 刪除 API 管理服務實體的特定群組。 |
> |  | **服務/群組/使用者** |  |
> | 動作 | Microsoft.ApiManagement/service/groups/users/read | 列出與組關聯的用戶實體的集合。 |
> | 動作 | Microsoft.ApiManagement/service/groups/users/write | 將現有使用者新增至現有群組 |
> | 動作 | Microsoft.ApiManagement/service/groups/users/delete | 從現有組中刪除現有使用者。 |
> |  | **服務/身份提供者** |  |
> | 動作 | Microsoft.ApiManagement/service/identityProviders/read | 列出在指定的服務實例中配置的標識提供程式的集合。 或獲取標識提供程式的配置詳細資訊,而不帶機密。 |
> | 動作 | Microsoft.ApiManagement/service/identityProviders/write | 創建或更新標識提供程式配置。 或更新現有的標識提供程式配置。 |
> | 動作 | Microsoft.ApiManagement/service/identityProviders/delete | 刪除指定的標識提供程式配置。 |
> | 動作 | 微軟.Api管理/服務/身份提供者/清單秘密/操作 | 獲取標識提供程式機密。 |
> |  | **服務/問題** |  |
> | 動作 | 微軟.Api管理/服務/問題/閱讀 | 列出指定服務實例中的問題集合。 或取得 API 管理問題詳細資訊 |
> |  | **服務/位置/網路狀態** |  |
> | 動作 | Microsoft.ApiManagement/service/locations/networkstatus/read | 取得位置中服務相依資源的網路存取狀態。 |
> |  | **服務/記錄器** |  |
> | 動作 | Microsoft.ApiManagement/service/loggers/read | 在指定的服務實例中列出記錄器的集合。 或獲取其識別碼指定的記錄器的詳細資訊。 |
> | 動作 | Microsoft.ApiManagement/service/loggers/write | 創建或更新記錄器。 或更新現有記錄器。 |
> | 動作 | Microsoft.ApiManagement/service/loggers/delete | 刪除指定的記錄器。 |
> |  | **服務/命名值** |  |
> | 動作 | 微軟.Api管理/服務/命名值/讀取 | 列出在服務實例中定義的命名值的集合。 或獲取其識別碼指定的命名值的詳細資訊。 |
> | 動作 | 微軟.Api管理/服務/命名值/寫入 | 建立或更新命名值。 或更新特定的命名值。 |
> | 動作 | 微軟.Api管理/服務/命名值/刪除 | 從 API 管理服務實體中刪除特定的命名值。 |
> | 動作 | 微軟.Api管理/服務/命名值/清單值/操作 | 獲取其識別碼指定的命名值的機密。 |
> |  | **服務/網路狀態** |  |
> | 動作 | Microsoft.ApiManagement/service/networkstatus/read | 取得服務相依資源的網路存取狀態。 |
> |  | **服務/通知** |  |
> | 動作 | Microsoft.ApiManagement/service/notifications/read | 列出在服務實例中定義的屬性的集合。 或獲取其識別碼指定的通知的詳細資訊。 |
> | 動作 | Microsoft.ApiManagement/service/notifications/write | 創建或更新 API 管理發行者通知。 |
> |  | **服務/通知/收件者電子郵件** |  |
> | 動作 | Microsoft.ApiManagement/service/notifications/recipientEmails/read | 獲取訂閱通知的通知收件者電子郵件的清單。 |
> | 動作 | Microsoft.ApiManagement/service/notifications/recipientEmails/write | 將電子郵件位址添加到通知的收件者清單中。 |
> | 動作 | Microsoft.ApiManagement/service/notifications/recipientEmails/delete | 從通知清單中刪除電子郵件。 |
> |  | **服務/通知/收件者使用者** |  |
> | 動作 | Microsoft.ApiManagement/service/notifications/recipientUsers/read | 獲取已訂閱通知的通知收件者使用者的清單。 |
> | 動作 | Microsoft.ApiManagement/service/notifications/recipientUsers/write | 將 API 管理使用者添加到通知的收件者清單中。 |
> | 動作 | Microsoft.ApiManagement/service/notifications/recipientUsers/delete | 從通知清單中刪除 API 管理使用者。 |
> |  | **服務/開放連線提供者** |  |
> | 動作 | Microsoft.ApiManagement/service/openidConnectProviders/read | 所有 OpenId 連線提供者清單。 或獲取特定的 OpenID 連接提供者,無機密。 |
> | 動作 | Microsoft.ApiManagement/service/openidConnectProviders/write | 建立或更新 OpenID 連接提供者。 或更新特定的 OpenID 連接提供者。 |
> | 動作 | Microsoft.ApiManagement/service/openidConnectProviders/delete | 刪除 API 管理服務實體的特定 OpenID 連線提供者。 |
> | 動作 | 微軟.Api管理/服務/開放連接提供者/清單秘密/操作 | 獲取特定的 OpenID 連接提供程式機密。 |
> |  | **服務/動作結果** |  |
> | 動作 | Microsoft.ApiManagement/service/operationresults/read | 取得長時間執行之作業的目前狀態 |
> |  | **服務/原則** |  |
> | 動作 | Microsoft.ApiManagement/service/policies/read | 列出 Api 管理服務的所有全域策略定義。 或獲取 Api 管理服務的全域策略定義。 |
> | 動作 | Microsoft.ApiManagement/service/policies/write | 創建或更新 Api 管理服務的全域策略配置。 |
> | 動作 | Microsoft.ApiManagement/service/policies/delete | 刪除 Api 管理服務的全域策略配置。 |
> |  | **服務/政策** |  |
> | 動作 | 微軟.Api管理/服務/策略/讀取 | 在租戶等級取得策略設定 |
> | 動作 | 微軟.Api管理/服務/策略/寫入 | 在租戶等級建立策略設定 |
> | 動作 | 微軟.Api管理/服務/策略/刪除 | 移除租戶層級的策略設定 |
> |  | **服務/原則描述** |  |
> | 動作 | 微軟.Api管理/服務/策略描述/閱讀 | 列出所有策略說明。 |
> |  | **服務/策略片段** |  |
> | 動作 | Microsoft.ApiManagement/service/policySnippets/read | 列出所有策略代碼段。 |
> |  | **服務/門戶設定** |  |
> | 動作 | Microsoft.ApiManagement/service/portalsettings/read | 列出門戶設置的集合。 或獲取門戶的「登錄設置」或「獲取門戶註冊設置」 或「獲取門戶委派設置」。 |
> | 動作 | Microsoft.ApiManagement/service/portalsettings/write | 更新登錄設置。 或創建或更新登錄設置。 或更新註冊設置或更新註冊設置或更新委派設置。 或創建或更新委派設置。 |
> | 動作 | 微軟.Api管理/服務/門戶設置/清單秘密/操作 | 獲取門戶委派設置的驗證密鑰。 |
> |  | **服務/產品** |  |
> | 動作 | Microsoft.ApiManagement/service/products/read | 列出指定服務實例中的產品集合。 或獲取其識別碼指定的產品的詳細資訊。 |
> | 動作 | Microsoft.ApiManagement/service/products/write | 創建或更新產品。 或更新現有產品詳細資訊。 |
> | 動作 | Microsoft.ApiManagement/service/products/delete | 刪除產品。 |
> |  | **服務/產品/產品** |  |
> | 動作 | Microsoft.ApiManagement/service/products/apis/read | 列出與產品關聯的 API 的集合。 |
> | 動作 | Microsoft.ApiManagement/service/products/apis/write | 將 API 添加到指定的產品。 |
> | 動作 | Microsoft.ApiManagement/service/products/apis/delete | 從指定的產品中刪除指定的 API。 |
> |  | **服務/產品/組** |  |
> | 動作 | Microsoft.ApiManagement/service/products/groups/read | 列出與指定產品關聯的開發人員組的集合。 |
> | 動作 | Microsoft.ApiManagement/service/products/groups/write | 添加指定開發人員組與指定產品之間的關聯。 |
> | 動作 | Microsoft.ApiManagement/service/products/groups/delete | 刪除指定群組和產品之間的關聯。 |
> |  | **服務/產品/政策** |  |
> | 動作 | Microsoft.ApiManagement/service/products/policies/read | 獲取產品級別的策略配置。 或在產品級別獲取策略配置。 |
> | 動作 | Microsoft.ApiManagement/service/products/policies/write | 創建或更新產品的策略配置。 |
> | 動作 | Microsoft.ApiManagement/service/products/policies/delete | 刪除產品的策略配置。 |
> |  | **服務/產品/政策** |  |
> | 動作 | Microsoft.ApiManagement/service/products/policy/read | 在產品等級取得策略設定 |
> | 動作 | Microsoft.ApiManagement/service/products/policy/write | 在產品層級建立策略設定 |
> | 動作 | Microsoft.ApiManagement/service/products/policy/delete | 移除產品層級的策略設定 |
> |  | **服務/產品/訂閱** |  |
> | 動作 | Microsoft.ApiManagement/service/products/subscriptions/read | 列出對指定產品的訂閱集合。 |
> |  | **服務/產品/標籤** |  |
> | 動作 | Microsoft.ApiManagement/service/products/tags/read | 列出與產品關聯的所有標籤。 或獲取與產品關聯的標記。 |
> | 動作 | Microsoft.ApiManagement/service/products/tags/write | 為產品分配標記。 |
> | 動作 | Microsoft.ApiManagement/service/products/tags/delete | 從產品中分離標籤。 |
> |  | **服務/產品 ByTags** |  |
> | 動作 | Microsoft.ApiManagement/service/productsByTags/read | 列出與標記關聯的產品集合。 |
> |  | **服務/屬性** |  |
> | 動作 | Microsoft.ApiManagement/service/properties/read | 列出在服務實例中定義的屬性的集合。 或獲取其識別碼指定的屬性的詳細資訊。 |
> | 動作 | Microsoft.ApiManagement/service/properties/write | 創建或更新屬性。 或更新特定屬性。 |
> | 動作 | Microsoft.ApiManagement/service/properties/delete | 從 API 管理服務實體中刪除特定屬性。 |
> | 動作 | 微軟.Api管理/服務/屬性/清單秘密/操作 | 獲取其識別碼指定的屬性的機密。 |
> |  | **服務/配額** |  |
> | 動作 | Microsoft.ApiManagement/service/quotas/read | 取得配額的值 |
> | 動作 | Microsoft.ApiManagement/service/quotas/write | 設定配額計數器目前的值 |
> |  | **服務/配額/期間** |  |
> | 動作 | Microsoft.ApiManagement/service/quotas/periods/read | 取得期間的配額計數器值 |
> | 動作 | Microsoft.ApiManagement/service/quotas/periods/write | 設定配額計數器目前的值 |
> |  | **服務/地區** |  |
> | 動作 | 微軟.Api管理/服務/區域/讀取 | 列出服務存在的所有 Azure 區域。 |
> |  | **服務/報告** |  |
> | 動作 | Microsoft.ApiManagement/service/reports/read | 取得依時間週期彙總的報告、取得依地理區域彙總的報告，或取得依開發人員彙總的報告。<br>或者，取得依產品彙總的報告。<br>或者，取得依 API 彙總的報告、取得依作業彙總的報告，或取得依訂用帳戶彙總的報告。<br>或者，取得報告資料的要求 |
> |  | **服務/訂閱** |  |
> | 動作 | Microsoft.ApiManagement/service/subscriptions/read | 列出 API 管理服務實例的所有訂閱。 或獲取指定的訂閱實體(不帶密鑰)。 |
> | 動作 | Microsoft.ApiManagement/service/subscriptions/write | 創建或更新指定使用者的訂閱到指定產品。 或更新其識別碼指定的訂閱的詳細資訊。 |
> | 動作 | Microsoft.ApiManagement/service/subscriptions/delete | 刪除指定的訂閱。 |
> | 動作 | Microsoft.ApiManagement/service/subscriptions/regeneratePrimaryKey/action | 重新生成 API 管理服務實體的現有訂閱的主金鑰。 |
> | 動作 | Microsoft.ApiManagement/service/subscriptions/regenerateSecondaryKey/action | 重新生成 API 管理服務實體的現有訂閱的輔助金鑰。 |
> | 動作 | 微軟.Api管理/服務/訂閱/清單秘密/操作 | 獲取指定的訂閱金鑰。 |
> |  | **服務/標籤資源** |  |
> | 動作 | Microsoft.ApiManagement/service/tagResources/read | 列出與標記關聯的資源集合。 |
> |  | **服務/標籤** |  |
> | 動作 | Microsoft.ApiManagement/service/tags/read | 列出在服務實例中定義的標記集合。 或獲取其識別碼指定的標記的詳細資訊。 |
> | 動作 | Microsoft.ApiManagement/service/tags/write | 創建標記。 或更新其識別碼指定的標記的詳細資訊。 |
> | 動作 | Microsoft.ApiManagement/service/tags/delete | 刪除 API 管理服務實體的特定標記。 |
> |  | **服務/樣本** |  |
> | 動作 | Microsoft.ApiManagement/service/templates/read | 取得所有電子郵件範本，或取得 API 管理電子郵件範本詳細資料 |
> | 動作 | Microsoft.ApiManagement/service/templates/write | 建立或更新 API 管理電子郵件範本，或更新 API 管理電子郵件範本 |
> | 動作 | Microsoft.ApiManagement/service/templates/delete | 重設預設的 API 管理電子郵件範本 |
> |  | **服務/租戶** |  |
> | 動作 | Microsoft.ApiManagement/service/tenant/read | 獲取 Api 管理服務的全域策略定義。 或取得租戶存取資訊詳細資訊 |
> | 動作 | Microsoft.ApiManagement/service/tenant/write | 設定租用戶的原則設定，或更新租用戶存取資訊詳細資料 |
> | 動作 | Microsoft.ApiManagement/service/tenant/delete | 移除租用戶的原則組態 |
> | 動作 | 微軟.Api管理/服務/租戶/清單秘密/操作 | 取得租用戶存取資訊的詳細資料 |
> | 動作 | Microsoft.ApiManagement/service/tenant/regeneratePrimaryKey/action | 重新產生主要存取金鑰 |
> | 動作 | Microsoft.ApiManagement/service/tenant/regenerateSecondaryKey/action | 重新產生次要存取金鑰 |
> | 動作 | Microsoft.ApiManagement/service/tenant/deploy/action | 執行部署工作，以將所指定 git 分支的變更套用至資料庫中的組態。 |
> | 動作 | Microsoft.ApiManagement/service/tenant/save/action | 在存放庫的指定分支中建立具有組態快照集的認可 |
> | 動作 | Microsoft.ApiManagement/service/tenant/validate/action | 驗證所指定 git 分支的變更 |
> |  | **服務/租戶/操作結果** |  |
> | 動作 | Microsoft.ApiManagement/service/tenant/operationResults/read | 取得作業結果的清單，或取得特定作業的結果 |
> |  | **服務/租戶/同步狀態** |  |
> | 動作 | Microsoft.ApiManagement/service/tenant/syncState/read | 取得上次 git 同步處理的狀態 |
> |  | **服務/使用者** |  |
> | 動作 | Microsoft.ApiManagement/service/users/read | 列出指定服務實例中的註冊用戶的集合。 或獲取其識別碼指定的使用者的詳細資訊。 |
> | 動作 | Microsoft.ApiManagement/service/users/write | 創建或更新使用者。 或更新其識別碼指定的使用者的詳細資訊。 |
> | 動作 | Microsoft.ApiManagement/service/users/delete | 刪除特定使用者。 |
> | 動作 | Microsoft.ApiManagement/service/users/generateSsoUrl/action | 檢索包含用於將給定使用者登錄到開發人員門戶的身份驗證令牌的重定向 URL。 |
> | 動作 | Microsoft.ApiManagement/service/users/token/action | 獲取用戶的共享訪問授權權杖。 |
> |  | **服務/使用者/確認** |  |
> | 動作 | Microsoft.ApiManagement/service/users/confirmations/send/action | 傳送確認 |
> |  | **服務/使用者/群組** |  |
> | 動作 | Microsoft.ApiManagement/service/users/groups/read | 列出所有使用者組。 |
> |  | **服務/使用者/身份** |  |
> | 動作 | 微軟.Api管理/服務/使用者/身份/讀取 | 所有用戶標識的清單。 |
> |  | **服務/使用者/金鑰** |  |
> | 動作 | Microsoft.ApiManagement/service/users/keys/read | 取得與使用者關聯的金鑰 |
> |  | **服務/使用者/訂閱** |  |
> | 動作 | Microsoft.ApiManagement/service/users/subscriptions/read | 列出指定使用者的訂閱集合。 |

## <a name="microsoftappconfiguration"></a>微軟.應用程式設定

Azure 服務:核心

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | 微軟.應用程式設定/註冊/操作 | 註冊訂閱以使用 Microsoft 應用配置。 |
> |  | **檢查名稱可用性** |  |
> | 動作 | 微軟.應用程式設定/檢查名稱可用性/讀取 | 檢查資源名稱是否可用。 |
> |  | **設定儲存** |  |
> | 動作 | 微軟.應用程式設定/設定儲存/讀取 | 取得指定設定儲存的屬性或列出指定資源群組或訂閱下的所有配置存儲。 |
> | 動作 | 微軟.應用程式設定/設定儲存/寫入 | 使用指定的參數創建或更新配置存儲。 |
> | 動作 | 微軟.應用程式設定/設定儲存/刪除 | 刪除配置存儲。 |
> | 動作 | 微軟.應用程式設定/配置儲存/清單鍵/操作 | 列出指定設定儲存的 API 金鑰。 |
> | 動作 | 微軟.應用程式配置/配置存儲/再生鍵/操作 | 為指定的配置存儲重新生成 API 金鑰。 |
> | 動作 | 微軟.應用程式配置/配置商店/清單鍵值/操作 | 列出指定設定儲存的鍵值。 |
> | 動作 | 微軟.應用配置/配置存儲/私有端點連接審批/操作 | 在指定的配置存儲下自動批准專用終結點連接。 |
> |  | **設定儲存/事件網格篩選器** |  |
> | 動作 | 微軟.應用程式配置/配置商店/事件網格過濾器/讀取 | 取得指定設定儲存事件網格篩選器的屬性,或列出指定配置儲存下的所有配置存儲事件網格篩選器。 |
> | 動作 | 微軟.應用程式設定/配置儲存/事件網格篩選/寫入 | 使用指定的參數創建或更新配置儲存事件網格篩選器。 |
> | 動作 | 微軟.應用程式配置/配置商店/事件網格過濾器/刪除 | 刪除配置儲存事件網格篩選器。 |
> |  | **設定儲存/私有端接連線 Proxies** |  |
> | 動作 | 微軟.App配置/配置商店/私人終端連接Proxies/驗證/操作 | 在指定的配置存儲下驗證專用終結點連接代理。 |
> | 動作 | 微軟.App配置/配置商店/私人端接連接Proxies/讀取 | 在指定的配置存儲下獲取專用終結點連接代理。 |
> | 動作 | 微軟.應用程式配置/配置商店/私人端接連接Proxies/寫 | 在指定的配置儲存下創建或更新專用終結點連接代理。 |
> | 動作 | 微軟.App配置/配置商店/私人終端連接Proxies/刪除 | 在指定的配置儲存下刪除專用終結點連接代理。 |
> |  | **設定儲存/專用端點連接** |  |
> | 動作 | 微軟.應用程式設定/配置儲存/私有端點連接/讀取 | 獲取專用終結點連接或在指定的配置存儲下列出專用終結點連接。 |
> | 動作 | 微軟.應用程式設定/配置儲存/私有端點連接/寫入 | 在指定的配置存儲下批准或拒絕專用終結點連接。 |
> | 動作 | 微軟.應用程式設定/配置儲存/專用端點連接/刪除 | 刪除指定配置存儲下的專用終結點連接。 |
> |  | **設定儲存/私有連結資源** |  |
> | 動作 | 微軟.應用程式配置/配置商店/私有鏈接資源/讀取 | 列出指定配置存儲下的所有專用連結資源。 |
> |  | **配置商店/供應商/微軟.見解/診斷設置** |  |
> | 動作 | 微軟.App配置/配置商店/供應商/微軟.見解/診斷設置/讀取 | 讀取配置存儲的所有診斷設置值。 |
> | 動作 | 微軟.App配置/配置商店/供應商/微軟.見解/診斷設置/寫入 | 為 Microsoft 應用配置編寫/覆蓋診斷設置。 |
> |  | **配置商店/供應商/微軟.見解/指標定義** |  |
> | 動作 | 微軟.App配置/配置商店/供應商/微軟.見解/指標定義/讀取 | 檢索 Microsoft 應用配置的所有指標定義。 |
> |  | **設定儲存/同步工作** |  |
> | 動作 | 微軟.應用程式設定/設定儲存/同步任務/讀取 | 獲取指定設定儲存同步任務的屬性,或列出指定配置儲存下的所有配置存儲同步任務。 |
> | 動作 | 微軟.應用程式設定/設定儲存/同步任務/寫入 | 使用指定的參數創建或更新配置儲存同步任務。 |
> | 動作 | 微軟.應用程式設定/設定儲存/同步任務/刪除 | 刪除設定儲存同步任務。 |
> |  | **位置/動作狀態** |  |
> | 動作 | 微軟.應用程式設定/位置/操作狀態/讀取 | 獲取操作的狀態。 |
> |  | **操作** |  |
> | 動作 | 微軟.應用程式設定/操作/讀取 | 列出 Microsoft 應用配置支援的所有操作。 |
> |  | **設定儲存/鍵值** |  |
> | DataAction | 微軟.應用程式設定/配置儲存/鍵值/讀取 | 從配置存儲讀取鍵值。 |
> | DataAction | 微軟.應用程式設定/設定儲存/鍵值/寫入 | 在配置儲存中建立或更新鍵值。 |
> | DataAction | 微軟.應用程式設定/配置儲存/鍵值/刪除 | 從設定儲存中刪除現有鍵值。 |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

Azure 服務[:Azure 資源管理員](../azure-resource-manager/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.Authorization/elevateAccess/action | 對呼叫者授與租用戶範圍的使用者存取系統管理員存取權 |
> |  | **classicAdministrators** |  |
> | 動作 | Microsoft.Authorization/classicAdministrators/read | 讀取訂用帳戶的系統管理員。 |
> | 動作 | Microsoft.Authorization/classicAdministrators/write | 對訂用帳戶新增或修改系統管理員。 |
> | 動作 | Microsoft.Authorization/classicAdministrators/delete | 從訂用帳戶中移除系統管理員。 |
> |  | **傳統管理員/操作狀態** |  |
> | 動作 | Microsoft.Authorization/classicAdministrators/operationstatuses/read | 取得訂用帳戶的系統管理員作業狀態。 |
> |  | **denyAssignments** |  |
> | 動作 | Microsoft.Authorization/denyAssignments/read | 取得拒絕指派的資訊。 |
> | 動作 | Microsoft.Authorization/denyAssignments/write | 在指定範圍建立拒絕指派。 |
> | 動作 | Microsoft.Authorization/denyAssignments/delete | 在指定範圍刪除拒絕指派。 |
> |  | **鎖** |  |
> | 動作 | Microsoft.Authorization/locks/read | 取得指定範圍的鎖定。 |
> | 動作 | Microsoft.Authorization/locks/write | 新增指定範圍的鎖定。 |
> | 動作 | Microsoft.Authorization/locks/delete | 刪除指定範圍的鎖定。 |
> |  | **操作** |  |
> | 動作 | Microsoft.Authorization/operations/read | 取得作業的清單 |
> |  | **權限** |  |
> | 動作 | Microsoft.Authorization/permissions/read | 列出呼叫者在給定範圍內所具有的所有權限。 |
> |  | **政策** |  |
> | 動作 | 微軟.授權/策略/審計/行動 | 在評估具有「審核」效果的 Azure 策略時執行的操作 |
> | 動作 | 微軟.授權/策略/審核不存在/操作 | 在評估具有「審核不存在」效果的 Azure 策略時執行的操作 |
> | 動作 | 微軟.授權/策略/拒絕/操作 | 在評估具有「拒絕」效果的 Azure 策略時執行的操作 |
> | 動作 | 微軟.授權/策略/部署不存在/操作 | 在評估具有「部署不存在」效果的 Azure 政策時執行的操作 |
> |  | **原則配置** |  |
> | 動作 | Microsoft.Authorization/policyAssignments/read | 取得關於原則指派的資訊。 |
> | 動作 | Microsoft.Authorization/policyAssignments/write | 建立指定範圍的原則指派。 |
> | 動作 | Microsoft.Authorization/policyAssignments/delete | 刪除指定範圍的原則指派。 |
> |  | **policyDefinitions** |  |
> | 動作 | Microsoft.Authorization/policyDefinitions/read | 取得關於原則定義的資訊。 |
> | 動作 | Microsoft.Authorization/policyDefinitions/write | 建立自訂的原則定義。 |
> | 動作 | Microsoft.Authorization/policyDefinitions/delete | 刪除原則定義。 |
> |  | **policySetDefinitions** |  |
> | 動作 | Microsoft.Authorization/policySetDefinitions/read | 取得原則集合定義的相關資訊。 |
> | 動作 | Microsoft.Authorization/policySetDefinitions/write | 建立自訂原則集合定義。 |
> | 動作 | Microsoft.Authorization/policySetDefinitions/delete | 刪除原則集合定義。 |
> |  | **providerOperations** |  |
> | 動作 | Microsoft.Authorization/providerOperations/read | 針對所有資源提供者取得可在角色定義中使用的作業。 |
> |  | **角色分配** |  |
> | 動作 | Microsoft.Authorization/roleAssignments/read | 取得關於角色指派的資訊。 |
> | 動作 | Microsoft.Authorization/roleAssignments/write | 建立指定範圍的角色指派。 |
> | 動作 | Microsoft.Authorization/roleAssignments/delete | 刪除指定範圍內的角色指派。 |
> |  | **roleDefinitions** |  |
> | 動作 | Microsoft.Authorization/roleDefinitions/read | 取得關於角色定義的資訊。 |
> | 動作 | Microsoft.Authorization/roleDefinitions/write | 以指定權限和可指派的範圍來建立或更新自訂角色定義。 |
> | 動作 | Microsoft.Authorization/roleDefinitions/delete | 刪除指定的自訂角色定義。 |

## <a name="microsoftautomation"></a>Microsoft.Automation

Azure 服務:[自動化](../automation/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.Automation/register/action | 對 Azure 自動化註冊訂用帳戶 |
> |  | **自動化帳戶** |  |
> | 動作 | Microsoft.Automation/automationAccounts/webhooks/action | 產生 Azure 自動化 Webhook 的 URI |
> | 動作 | Microsoft.Automation/automationAccounts/read | 取得 Azure 自動化帳戶 |
> | 動作 | Microsoft.Automation/automationAccounts/write | 建立或更新 Azure 自動化帳戶 |
> | 動作 | Microsoft.Automation/automationAccounts/listKeys/action | 讀取自動化帳戶的金鑰 |
> | 動作 | Microsoft.Automation/automationAccounts/delete | 刪除 Azure 自動化帳戶 |
> |  | **自動化帳號/代理註冊資訊** |  |
> | 動作 | Microsoft.Automation/automationAccounts/agentRegistrationInformation/read | 讀取 Azure Automation DSC 的註冊資訊 |
> | 動作 | Microsoft.Automation/automationAccounts/agentRegistrationInformation/regenerateKey/action | 寫入要求以重新產生 Azure Automation DSC 金鑰 |
> |  | **自動化帳戶/憑證** |  |
> | 動作 | Microsoft.Automation/automationAccounts/certificates/getCount/action | 讀取憑證的計數 |
> | 動作 | Microsoft.Automation/automationAccounts/certificates/read | 取得 Azure 自動化憑證資產 |
> | 動作 | Microsoft.Automation/automationAccounts/certificates/write | 建立或更新 Azure 自動化憑證資產 |
> | 動作 | Microsoft.Automation/automationAccounts/certificates/delete | 刪除 Azure 自動化憑證資產 |
> |  | **自動化會計/編譯作業** |  |
> | 動作 | Microsoft.Automation/automationAccounts/compilationjobs/write | 寫入 Azure Automation DSC 的編譯 |
> | 動作 | Microsoft.Automation/automationAccounts/compilationjobs/read | 讀取 Azure Automation DSC 的編譯 |
> | 動作 | Microsoft.Automation/automationAccounts/compilationjobs/write | 寫入 Azure Automation DSC 的編譯 |
> | 動作 | Microsoft.Automation/automationAccounts/compilationjobs/read | 讀取 Azure Automation DSC 的編譯 |
> |  | **automationAccounts/configurations** |  |
> | 動作 | Microsoft.Automation/automationAccounts/configurations/read | 取得 Azure Automation DSC 的內容 |
> | 動作 | Microsoft.Automation/automationAccounts/configurations/getCount/action | 讀取 Azure Automation DSC 內容的計數 |
> | 動作 | Microsoft.Automation/automationAccounts/configurations/write | 寫入 Azure Automation DSC 的內容 |
> | 動作 | Microsoft.Automation/automationAccounts/configurations/delete | 刪除 Azure Automation DSC 的內容 |
> |  | **自動化會計/設定/內容** |  |
> | 動作 | Microsoft.Automation/automationAccounts/configurations/content/read | 讀取組態媒體內容 |
> |  | **自動化帳戶/連線** |  |
> | 動作 | Microsoft.Automation/automationAccounts/connections/read | 取得 Azure 自動化連線資產 |
> | 動作 | Microsoft.Automation/automationAccounts/connections/getCount/action | 讀取連線的計數 |
> | 動作 | Microsoft.Automation/automationAccounts/connections/write | 建立或更新 Azure 自動化連線資產 |
> | 動作 | Microsoft.Automation/automationAccounts/connections/delete | 刪除 Azure 自動化連線資產 |
> |  | **自動化帳戶/連線類型** |  |
> | 動作 | Microsoft.Automation/automationAccounts/connectionTypes/read | 取得 Azure 自動化連線類型資產 |
> | 動作 | Microsoft.Automation/automationAccounts/connectionTypes/write | 建立 Azure 自動化連線類型資產 |
> | 動作 | Microsoft.Automation/automationAccounts/connectionTypes/delete | 刪除 Azure 自動化連線類型資產 |
> |  | **自動化帳戶/認證** |  |
> | 動作 | Microsoft.Automation/automationAccounts/credentials/read | 取得 Azure 自動化認證資產 |
> | 動作 | Microsoft.Automation/automationAccounts/credentials/getCount/action | 讀取認證的計數 |
> | 動作 | Microsoft.Automation/automationAccounts/credentials/write | 建立或更新 Azure 自動化認證資產 |
> | 動作 | Microsoft.Automation/automationAccounts/credentials/delete | 刪除 Azure 自動化認證資產 |
> |  | **自動化帳戶/混合 Runbook 工人組** |  |
> | 動作 | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | 讀取混合式 Runbook 背景工作角色資源 |
> | 動作 | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/delete | 刪除混合式 Runbook 背景工作角色資源 |
> |  | **automationAccounts/jobs** |  |
> | 動作 | Microsoft.Automation/automationAccounts/jobs/runbookContent/action | 在作業執行時取得 Azure 自動化 Runbook 的內容 |
> | 動作 | Microsoft.Automation/automationAccounts/jobs/read | 取得 Azure 自動化作業 |
> | 動作 | Microsoft.Automation/automationAccounts/jobs/write | 建立 Azure 自動化作業 |
> | 動作 | Microsoft.Automation/automationAccounts/jobs/stop/action | 停止 Azure 自動化作業 |
> | 動作 | Microsoft.Automation/automationAccounts/jobs/suspend/action | 暫止 Azure 自動化作業 |
> | 動作 | Microsoft.Automation/automationAccounts/jobs/resume/action | 繼續 Azure 自動化作業 |
> |  | **自動化會計/工作/輸出** |  |
> | 動作 | Microsoft.Automation/automationAccounts/jobs/output/read | 取得作業的輸出 |
> |  | **自動化會計/作業/流** |  |
> | 動作 | Microsoft.Automation/automationAccounts/jobs/streams/read | 取得 Azure 自動化作業串流 |
> | 動作 | Microsoft.Automation/automationAccounts/jobs/streams/read | 取得 Azure 自動化作業串流 |
> |  | **自動化會計或作業計劃** |  |
> | 動作 | Microsoft.Automation/automationAccounts/jobSchedules/read | 取得 Azure 自動化作業排程 |
> | 動作 | Microsoft.Automation/automationAccounts/jobSchedules/write | 建立 Azure 自動化作業排程 |
> | 動作 | Microsoft.Automation/automationAccounts/jobSchedules/delete | 刪除 Azure 自動化作業排程 |
> |  | **自動化帳戶/連結工作區** |  |
> | 動作 | Microsoft.Automation/automationAccounts/linkedWorkspace/read | 取得連結至自動化帳戶的工作區 |
> |  | **自動化帳戶/模組** |  |
> | 動作 | Microsoft.Automation/automationAccounts/modules/read | 取得 Azure 自動化 Powershell 模組 |
> | 動作 | Microsoft.Automation/automationAccounts/modules/getCount/action | 取得自動化帳戶內的 Powershell 模組計數 |
> | 動作 | Microsoft.Automation/automationAccounts/modules/write | 建立或更新 Azure 自動化 Powershell 模組 |
> | 動作 | Microsoft.Automation/automationAccounts/modules/delete | 刪除 Azure 自動化 Powershell 模組 |
> |  | **自動化帳號/模組/活動** |  |
> | 動作 | Microsoft.Automation/automationAccounts/modules/activities/read | 取得 Azure 自動化活動 |
> |  | **自動化會計/節點設定** |  |
> | 動作 | Microsoft.Automation/automationAccounts/nodeConfigurations/rawContent/action | 讀取 Azure Automation DSC 的節點設定內容 |
> | 動作 | Microsoft.Automation/automationAccounts/nodeConfigurations/read | 讀取 Azure Automation DSC 的節點設定 |
> | 動作 | Microsoft.Automation/automationAccounts/nodeConfigurations/write | 寫入 Azure Automation DSC 的節點設定 |
> | 動作 | Microsoft.Automation/automationAccounts/nodeConfigurations/delete | 刪除 Azure Automation DSC 的節點設定 |
> |  | **自動化帳目/節點計數** |  |
> | 動作 | Microsoft.Automation/automationAccounts/nodecounts/read | 讀取指定類型的節點計數摘要 |
> |  | **自動化帳戶/節點** |  |
> | 動作 | Microsoft.Automation/automationAccounts/nodes/read | 讀取 Azure Automation DSC 節點 |
> | 動作 | Microsoft.Automation/automationAccounts/nodes/write | 建立或更新 Azure Automation DSC 節點 |
> | 動作 | Microsoft.Automation/automationAccounts/nodes/delete | 刪除 Azure Automation DSC 節點 |
> |  | **自動化帳戶/節點/報告** |  |
> | 動作 | Microsoft.Automation/automationAccounts/nodes/reports/read | 讀取 Azure Automation DSC 報告 |
> |  | **自動化帳戶/節點/報告/內容** |  |
> | 動作 | Microsoft.Automation/automationAccounts/nodes/reports/content/read | 讀取 Azure Automation DSC 報告內容 |
> |  | **自動化會計/物件資料型態/欄位** |  |
> | 動作 | Microsoft.Automation/automationAccounts/objectDataTypes/fields/read | 取得 Azure 自動化 TypeField |
> |  | **自動化帳戶/私有端點連接Proxies** |  |
> | 動作 | 微軟.自動化/自動化帳戶/私人端接連接Proxies/讀取 | 讀取 Azure 自動化專屬的主效 |
> | 動作 | 微軟.自動化/自動化帳戶/私人端接連接Proxies/寫 | 建立 Azure 自動化專用終結點連線代理 |
> | 動作 | 微軟.自動化/自動化帳戶/私人端接連接Proxies/驗證/操作 | 驗證專用終結點連線要求(組 Id 驗證) |
> | 動作 | 微軟.自動化/自動化帳戶/私人端接連接Proxies/刪除 | 移除 Azure 自動化專用終結點連線代理 |
> |  | **自動化帳目/專用端接策略/操作結果** |  |
> | 動作 | 微軟.自動化/自動化帳戶/私人端接連接Proxies/操作結果/讀取 | 獲取 Azure 自動化專用終結點代理操作結果。 |
> |  | **自動化帳戶/專用端點連接** |  |
> | 動作 | 微軟.自動化/自動化帳戶/專用端點連接/讀取 | 取得 Azure 自動化專用終結點連線狀態 |
> | 動作 | 微軟.自動化/自動化帳戶/專用端點連接/寫入 | 批准或拒絕 Azure 自動化專用終結點連接 |
> |  | **自動化帳號/私有連結資源** |  |
> | 動作 | 微軟.自動化/自動化帳戶/私人連結資源/閱讀 | 讀取專用終結點的組資訊 |
> |  | **自動化帳戶/python2包** |  |
> | 動作 | Microsoft.Automation/automationAccounts/python2Packages/read | 取得 Azure 自動化 Python 2 套件 |
> | 動作 | Microsoft.Automation/automationAccounts/python2Packages/write | 建立或更新 Azure 自動化 Python 2 套件 |
> | 動作 | Microsoft.Automation/automationAccounts/python2Packages/delete | 刪除 Azure 自動化 Python 2 套件 |
> |  | **自動化帳戶/python3套件** |  |
> | 動作 | Microsoft.Automation/automationAccounts/python3Packages/read | 取得 Azure 自動化 Python 3 套件 |
> | 動作 | Microsoft.Automation/automationAccounts/python3Packages/write | 建立或更新 Azure 自動化 Python 3 套件 |
> | 動作 | Microsoft.Automation/automationAccounts/python3Packages/delete | 刪除 Azure 自動化 Python 3 套件 |
> |  | **自動化帳目/執行簿** |  |
> | 動作 | Microsoft.Automation/automationAccounts/runbooks/read | 取得 Azure 自動化 Runbook |
> | 動作 | Microsoft.Automation/automationAccounts/runbooks/getCount/action | 取得 Azure 自動化 Runbook 的計數 |
> | 動作 | Microsoft.Automation/automationAccounts/runbooks/write | 建立或更新 Azure 自動化 Runbook |
> | 動作 | Microsoft.Automation/automationAccounts/runbooks/delete | 刪除 Azure 自動化 Runbook |
> | 動作 | Microsoft.Automation/automationAccounts/runbooks/publish/action | 發佈 Azure 自動化 Runbook 草稿 |
> |  | **自動化帳目/執行簿/內容** |  |
> | 動作 | Microsoft.Automation/automationAccounts/runbooks/content/read | 取得 Azure 自動化 Runbook 的內容 |
> |  | **自動化帳目/執行簿/草稿** |  |
> | 動作 | Microsoft.Automation/automationAccounts/runbooks/draft/read | 取得 Azure 自動化 Runbook 草稿 |
> | 動作 | Microsoft.Automation/automationAccounts/runbooks/draft/undoEdit/action | 復原對 Azure 自動化 Runbook 草稿所做的編輯 |
> |  | **自動化帳目/執行簿/草稿/內容** |  |
> | 動作 | Microsoft.Automation/automationAccounts/runbooks/draft/content/write | 建立 Azure 自動化 Runbook 草稿的內容 |
> |  | **自動化帳目/執行簿/草稿/操作結果** |  |
> | 動作 | Microsoft.Automation/automationAccounts/runbooks/draft/operationResults/read | 取得 Azure 自動化 Runbook 草稿作業結果 |
> |  | **自動化帳簿/執行簿/草稿/測試作業** |  |
> | 動作 | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/read | 取得 Azure 自動化 Runbook 草稿測試作業 |
> | 動作 | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/write | 建立 Azure 自動化 Runbook 草稿測試作業 |
> | 動作 | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/stop/action | 停止 Azure 自動化 Runbook 草稿測試作業 |
> | 動作 | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/suspend/action | 暫止 Azure 自動化 Runbook 草稿測試作業 |
> | 動作 | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/resume/action | 繼續 Azure 自動化 Runbook 草稿測試作業 |
> |  | **自動化會計/計畫** |  |
> | 動作 | Microsoft.Automation/automationAccounts/schedules/read | 取得 Azure 自動化排程資產 |
> | 動作 | Microsoft.Automation/automationAccounts/schedules/getCount/action | 取得 Azure 自動化排程的計數 |
> | 動作 | Microsoft.Automation/automationAccounts/schedules/write | 建立或更新 Azure 自動化排程資產 |
> | 動作 | Microsoft.Automation/automationAccounts/schedules/delete | 刪除 Azure 自動化排程資產 |
> |  | **自動化帳戶/軟體更新配置機器執行** |  |
> | 動作 | 微軟.自動化/自動化帳戶/軟體更新配置機運行/讀取 | 取得 Azure 自動化軟體更新設定電腦執行 |
> |  | **自動化帳戶/軟體更新配置執行** |  |
> | 動作 | 微軟.自動化/自動化帳戶/軟體更新配置運行/讀取 | 取得 Azure 自動化軟體更新配置執行 |
> |  | **automationAccounts/softwareUpdateConfigurations** |  |
> | 動作 | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/write | 建立或更新 Azure 自動化軟體更新設定 |
> | 動作 | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/read | 取得 Azure 自動化軟體更新設定 |
> | 動作 | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/delete | 刪除 Azure 自動化軟體更新設定 |
> | 動作 | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/write | 建立或更新 Azure 自動化軟體更新設定 |
> | 動作 | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/read | 取得 Azure 自動化軟體更新設定 |
> | 動作 | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/delete | 刪除 Azure 自動化軟體更新設定 |
> |  | **自動化會計/統計** |  |
> | 動作 | Microsoft.Automation/automationAccounts/statistics/read | 取得 Azure 自動化統計資料 |
> |  | **自動化帳戶/更新部署電腦執行** |  |
> | 動作 | Microsoft.Automation/automationAccounts/updateDeploymentMachineRuns/read | 取得 Azure 自動化更新部署機器 |
> |  | **自動化帳戶/更新管理修補程式作業** |  |
> | 動作 | Microsoft.Automation/automationAccounts/updateManagementPatchJob/read | 取得 Azure 自動化更新管理修補作業 |
> |  | **自動化帳戶/用法** |  |
> | 動作 | Microsoft.Automation/automationAccounts/usages/read | 取得 Azure 自動化使用方式 |
> |  | **自動化帳戶/變數** |  |
> | 動作 | Microsoft.Automation/automationAccounts/variables/read | 讀取 Azure 自動化變數資產 |
> | 動作 | Microsoft.Automation/automationAccounts/variables/write | 建立或更新 Azure 自動化變數資產 |
> | 動作 | Microsoft.Automation/automationAccounts/variables/delete | 刪除 Azure 自動化變數資產 |
> |  | **自動化帳戶/觀察程式** |  |
> | 動作 | Microsoft.Automation/automationAccounts/watchers/write | 建立 Azure 自動化監看員作業 |
> | 動作 | Microsoft.Automation/automationAccounts/watchers/read | 取得 Azure 自動化監看員作業 |
> | 動作 | Microsoft.Automation/automationAccounts/watchers/delete | 刪除 Azure 自動化監看員作業 |
> | 動作 | Microsoft.Automation/automationAccounts/watchers/start/action | 啟動 Azure 自動化監看員作業 |
> | 動作 | Microsoft.Automation/automationAccounts/watchers/stop/action | 停止 Azure 自動化監看員作業 |
> |  | **自動化帳戶/觀察程式/流** |  |
> | 動作 | Microsoft.Automation/automationAccounts/watchers/streams/read | 取得 Azure 自動化監看員作業串流 |
> |  | **自動化帳戶/觀察程式/觀察程式** |  |
> | 動作 | Microsoft.Automation/automationAccounts/watchers/watcherActions/write | 建立 Azure 自動化監看員作業動作 |
> | 動作 | Microsoft.Automation/automationAccounts/watchers/watcherActions/read | 取得 Azure 自動化監看員作業動作 |
> | 動作 | Microsoft.Automation/automationAccounts/watchers/watcherActions/delete | 刪除 Azure 自動化監看員作業動作 |
> |  | **automationAccounts/webhooks** |  |
> | 動作 | Microsoft.Automation/automationAccounts/webhooks/read | 讀取 Azure 自動化 Webhook |
> | 動作 | Microsoft.Automation/automationAccounts/webhooks/write | 建立或更新 Azure 自動化 Webhook |
> | 動作 | Microsoft.Automation/automationAccounts/webhooks/delete | 刪除 Azure 自動化 Webhook  |
> |  | **操作** |  |
> | 動作 | Microsoft.Automation/operations/read | 取得 Azure 自動化資源的可用作業 |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

Azure 服務[:Azure 活動目錄 B2C](../active-directory-b2c/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.AzureActiveDirectory/register/action | 為 Microsoft.AzureActiveDirectory 資源提供者註冊訂用帳戶 |
> |  | **b2c 目錄** |  |
> | 動作 | Microsoft.AzureActiveDirectory/b2cDirectories/write | 建立或更新 B2C 目錄資源 |
> | 動作 | Microsoft.AzureActiveDirectory/b2cDirectories/read | 檢視 B2C 目錄資源 |
> | 動作 | Microsoft.AzureActiveDirectory/b2cDirectories/delete | 刪除 B2C 目錄資源 |
> |  | **b2c租戶** |  |
> | 動作 | 微軟.AzureActiveDirectory/b2c租戶/讀取 | 列出使用者是成員的所有 B2C 租戶 |
> |  | **操作** |  |
> | 動作 | Microsoft.AzureActiveDirectory/operations/read | 讀取可供 Microsoft.AzureActiveDirectory 資源提供者使用的所有 API 作業 |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

Azure 服務:核心

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.AzureStack/register/action | 向 Microsoft.AzureStack 資源提供者註冊訂用帳戶 |
> |  | **雲清單檔** |  |
> | 動作 | 微軟.AzureStack/雲清單檔/讀取 | 取得資料檔案 |
> |  | **作業** |  |
> | 動作 | Microsoft.AzureStack/Operations/read | 取得資源提供者作業的屬性 |
> |  | **註冊** |  |
> | 動作 | Microsoft.AzureStack/registrations/read | 取得 Azure Stack 註冊的屬性 |
> | 動作 | Microsoft.AzureStack/registrations/write | 建立或更新 Azure Stack 註冊 |
> | 動作 | Microsoft.AzureStack/registrations/delete | 刪除 Azure Stack 註冊 |
> | 動作 | Microsoft.AzureStack/registrations/getActivationKey/action | 取得最新的 Azure Stack 啟用金鑰 |
> |  | **registrations/customerSubscriptions** |  |
> | 動作 | Microsoft.AzureStack/registrations/customerSubscriptions/read | 取得 Azure Stack 客戶訂用帳戶的屬性 |
> | 動作 | Microsoft.AzureStack/registrations/customerSubscriptions/write | 建立或更新 Azure Stack 客戶訂用帳戶 |
> | 動作 | Microsoft.AzureStack/registrations/customerSubscriptions/delete | 刪除 Azure Stack 客戶訂用帳戶 |
> |  | **registrations/products** |  |
> | 動作 | Microsoft.AzureStack/registrations/products/read | 取得 Azure Stack Marketplace 產品的屬性 |
> | 動作 | Microsoft.AzureStack/registrations/products/listDetails/action | 擷取 Azure Stack Marketplace 產品的延伸詳細資料 |
> | 動作 | 微軟.AzureStack/註冊/產品/獲取產品/操作 | 檢索 Azure 堆疊應用商店產品的清單 |
> | 動作 | 微軟.AzureStack/註冊/產品/獲取產品/操作 | 檢索 Azure 堆疊應用商店產品 |
> | 動作 | 微軟.AzureStack/註冊/產品/上傳產品日誌/操作 | 記錄 Azure 堆疊應用商店產品操作狀態和時間戳 |

## <a name="microsoftbatch"></a>Microsoft.Batch

Azure 服務:[批次處理](../batch/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.Batch/register/action | 針對 Batch 資源提供者註冊訂用帳戶，並讓您能夠建立 Batch 帳戶 |
> | 動作 | Microsoft.Batch/unregister/action | 為 Batch 資源提供者取消註冊訂用帳戶，以防止建立 Batch 帳戶 |
> |  | **批次處理帳戶** |  |
> | 動作 | Microsoft.Batch/batchAccounts/read | 列出 Batch 帳戶，或取得 Batch 帳戶的屬性 |
> | 動作 | Microsoft.Batch/batchAccounts/write | 建立新的 Batch 帳戶，或更新現有的 Batch 帳戶 |
> | 動作 | Microsoft.Batch/batchAccounts/delete | 刪除 Batch 帳戶 |
> | 動作 | Microsoft.Batch/batchAccounts/listkeys/action | 列出 Batch 帳戶的存取金鑰 |
> | 動作 | Microsoft.Batch/batchAccounts/regeneratekeys/action | 重新產生 Batch 帳戶的存取金鑰 |
> | 動作 | Microsoft.Batch/batchAccounts/syncAutoStorageKeys/action | 針對為 Batch 帳戶所設定的自動儲存體帳戶同步處理存取金鑰 |
> |  | **批次處理帳戶/應用程式** |  |
> | 動作 | Microsoft.Batch/batchAccounts/applications/read | 列出應用程式，或取得應用程式的屬性 |
> | 動作 | Microsoft.Batch/batchAccounts/applications/write | 建立新的應用程式，或更新現有應用程式 |
> | 動作 | Microsoft.Batch/batchAccounts/applications/delete | 刪除應用程式 |
> |  | **批次處理帳號/應用程式/版本** |  |
> | 動作 | Microsoft.Batch/batchAccounts/applications/versions/read | 取得應用程式套件的屬性 |
> | 動作 | Microsoft.Batch/batchAccounts/applications/versions/write | 建立新的應用程式套件，或更新現有的應用程式套件 |
> | 動作 | Microsoft.Batch/batchAccounts/applications/versions/delete | 刪除應用程式套件 |
> | 動作 | Microsoft.Batch/batchAccounts/applications/versions/activate/action | 啟動應用程式套件 |
> |  | **批次處理帳目/憑證操作結果** |  |
> | 動作 | Microsoft.Batch/batchAccounts/certificateOperationResults/read | 取得 Batch 帳戶上長時間執行憑證作業的結果 |
> |  | **批次處理帳戶/憑證** |  |
> | 動作 | Microsoft.Batch/batchAccounts/certificates/read | 列出批 Batch 帳戶上的憑證，或取得憑證的屬性 |
> | 動作 | Microsoft.Batch/batchAccounts/certificates/write | 在 Batch 帳戶上建立新的憑證，或更新現有憑證 |
> | 動作 | Microsoft.Batch/batchAccounts/certificates/delete | 從 Batch 帳戶刪除憑證 |
> | 動作 | Microsoft.Batch/batchAccounts/certificates/cancelDelete/action | 取消 Batch 帳戶上無法刪除憑證的作業 |
> |  | **批次處理帳目/操作結果** |  |
> | 動作 | Microsoft.Batch/batchAccounts/operationResults/read | 取得長時間執行 Batch 帳戶作業的結果 |
> |  | **批次處理帳目/池操作結果** |  |
> | 動作 | Microsoft.Batch/batchAccounts/poolOperationResults/read | 取得 Batch 帳戶上長時間執行集區作業的結果 |
> |  | **批次處理帳戶/池** |  |
> | 動作 | Microsoft.Batch/batchAccounts/pools/read | 列出 Batch 帳戶上的集區，或取得集區的屬性 |
> | 動作 | Microsoft.Batch/batchAccounts/pools/write | 在 Batch 帳戶上建立新的集區，或更新現有的集區 |
> | 動作 | Microsoft.Batch/batchAccounts/pools/delete | 從 Batch 帳戶刪除集區 |
> | 動作 | Microsoft.Batch/batchAccounts/pools/stopResize/action | 停止 Batch 帳戶集區上正在進行的調整大小作業 |
> | 動作 | Microsoft.Batch/batchAccounts/pools/disableAutoscale/action | 停用 Batch 帳戶集區的自動縮放比例 |
> |  | **批次處理帳戶/私有終結點連接結果** |  |
> | 動作 | 微軟.批量/批處理帳戶/私有終結點連接結果/讀取 | 取得長時間執行的 Batch 帳號專用終結點連線操作的結果 |
> |  | **批次處理帳戶/專用終結點連接** |  |
> | 動作 | 微軟.批處理/批處理帳戶/私有終結點連接/寫入 | 更新批次處理帳戶上的現有專用終結點連接 |
> | 動作 | 微軟.批處理/批處理帳戶/私有終結點連接/讀取 | 取得專用終結點連線或列出批次處理帳戶上的專用終結點連接 |
> |  | **批次處理帳戶/私有連結資源** |  |
> | 動作 | 微軟.批量/批處理帳戶/私有鏈接資源/讀取 | 取得專用連結資源屬性或列出批次處理帳戶上的專用連結資源 |
> |  | **位置** |  |
> | 動作 | Microsoft.Batch/locations/checkNameAvailability/action | 檢查帳戶名稱有效，且並非使用中。 |
> |  | **位置/帳戶操作結果** |  |
> | 動作 | Microsoft.Batch/locations/accountOperationResults/read | 取得長時間執行 Batch 帳戶作業的結果 |
> |  | **位置/配額** |  |
> | 動作 | Microsoft.Batch/locations/quotas/read | 取得指定訂用帳戶在指定 Azure 區域內的 Batch 配額 |
> |  | **操作** |  |
> | 動作 | Microsoft.Batch/operations/read | 列出可對 Microsoft.Batch 資源提供者進行的作業 |
> |  | **批次處理帳戶/工作** |  |
> | DataAction | 微軟.批次/批次帳戶/作業/讀取 | 列出 Batch 帳號上的工作或抓取工作屬性 |
> | DataAction | 微軟.批量/批量帳戶/作業/寫入 | 在 Batch 帳號建立新的作業或更新現有作業 |
> | DataAction | 微軟.批量/批量帳戶/作業/刪除 | 從批次處理帳戶中移除工作 |
> |  | **批次處理科目/作業計劃** |  |
> | DataAction | 微軟.批量/批次帳戶/作業計劃/讀取 | 列出 Batch 帳號上的作業計劃或取得作業計劃屬性 |
> | DataAction | 微軟.批量/批次帳戶/作業計劃/寫入 | 在 Batch 帳號建立新的作業計畫或更新現有作業計劃 |
> | DataAction | 微軟.批量/批次帳戶/作業計劃/刪除 | 從批次處理帳戶中移除工作計劃 |

## <a name="microsoftbilling"></a>Microsoft.Billing

Azure 服務:[成本管理 + 計費](../cost-management-billing/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | 微軟.計費/驗證位址/操作 |  |
> | 動作 | Microsoft.Billing/register/action |  |
> |  | **billingAccounts** |  |
> | 動作 | Microsoft.Billing/billingAccounts/read |  |
> | 動作 | 微軟.計費/計費帳戶/清單發票節創建訂閱許可權/操作 |  |
> | 動作 | 微軟.計費/計費帳戶/寫入 |  |
> |  | **帳單帳戶/協定** |  |
> | 動作 | 微軟.計費/計費帳戶/協定/讀取 |  |
> |  | **計費帳戶/計費許可權** |  |
> | 動作 | 微軟.計費/計費帳戶/計費許可權/讀取 |  |
> |  | **billingAccounts/billingProfiles** |  |
> | 動作 | 微軟.計費/計費帳戶/計費配置檔/寫入 |  |
> | 動作 | 微軟.計費/計費帳戶/計費配置檔/讀取 |  |
> | 動作 | 微軟.計費/計費帳戶/計費配置檔/寫入 |  |
> | 動作 | 微軟.計費/計費帳戶/計費配置檔/寫入 |  |
> |  | **帳號/帳戶/帳戶/帳單設定檔/計費權限** |  |
> | 動作 | 微軟.計費/計費帳戶/計費配置檔/計費許可權/讀取 |  |
> |  | **帳單帳號/帳單設定檔/客戶** |  |
> | 動作 | 微軟.計費/計費帳戶/計費配置檔/客戶/閱讀 |  |
> |  | **billingAccounts/billingProfiles/invoices/pricesheet** |  |
> | 動作 | 微軟.計費/計費帳戶/帳單配置檔/發票/價格表/下載/操作 |  |
> |  | **帳單科目/帳單配置檔/發票部分** |  |
> | 動作 | 微軟.計費/計費帳戶/計費配置檔/發票節/寫 |  |
> | 動作 | 微軟.計費/計費帳戶/帳單配置檔/發票節/讀 |  |
> | 動作 | 微軟.計費/計費帳戶/計費配置檔/發票節/寫 |  |
> |  | **帳單科目/帳單配置檔/發票節/計費許可權** |  |
> | 動作 | 微軟.計費/計費帳戶/帳單配置檔/發票節/計費許可權/讀取 |  |
> |  | **帳單科目/帳單配置檔/發票節/計費訂閱** |  |
> | 動作 | 微軟.計費/計費帳戶/帳單配置檔/發票節/計費訂閱/轉移/操作 |  |
> | 動作 | 微軟.計費/計費帳戶/帳單配置檔/發票節/計費訂閱/移動/操作 |  |
> | 動作 | 微軟.計費/計費帳戶/帳單配置檔/發票節/計費訂閱/驗證移動資格/操作 |  |
> |  | **帳單科目/帳單配置檔/發票部分/產品** |  |
> | 動作 | 微軟.計費/計費帳戶/帳單配置檔/發票部分/產品/轉移/操作 |  |
> | 動作 | 微軟.計費/計費帳戶/帳單配置檔/發票節/產品/移動/操作 |  |
> | 動作 | 微軟.計費/計費帳戶/帳單配置檔/發票部分/產品/驗證移動資格/操作 |  |
> |  | **billingAccounts/billingProfiles/pricesheet** |  |
> | 動作 | 微軟.計費/計費帳戶/帳單配置檔/價格表/下載/操作 |  |
> |  | **billingAccounts/billingSubscriptions** |  |
> | 動作 | 微軟.計費/計費帳戶/計費訂閱/讀取 |  |
> |  | **計費帳戶/客戶** |  |
> | 動作 | 微軟.計費/計費帳戶/客戶/閱讀 |  |
> |  | **計費帳戶/客戶/計費許可權** |  |
> | 動作 | 微軟.計費/計費帳戶/客戶/計費許可權/讀取 |  |
> |  | **billingAccounts/departments** |  |
> | 動作 | Microsoft.Billing/billingAccounts/departments/read |  |
> |  | **billingAccounts/enrollmentAccounts** |  |
> | 動作 | Microsoft.Billing/billingAccounts/enrollmentAccounts/read |  |
> |  | **計費帳戶/註冊帳戶/計費許可權** |  |
> | 動作 | 微軟.計費/計費帳戶/註冊帳戶/計費許可權/讀取 |  |
> |  | **計費帳戶/註冊部門/計費許可權** |  |
> | 動作 | 微軟.計費/計費帳戶/註冊部門/計費許可權/讀取 |  |
> |  | **billingAccounts/products** |  |
> | 動作 | 微軟.計費/計費帳戶/產品/閱讀 |  |
> |  | **部門** |  |
> | 動作 | Microsoft.Billing/departments/read |  |
> |  | **發票** |  |
> | 動作 | 微軟.計費/發票/下載/操作 | 使用清單中下載連結下載發票 |
> | 動作 | 微軟.計費/發票/下載/操作 | 使用清單中下載連結下載發票 |
> | 動作 | Microsoft.Billing/invoices/read |  |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

Azure 服務:[必要地圖](https://docs.microsoft.com/BingMaps/)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | 微軟.BingMaps/更新通訊首選項/操作 | 更新 Microsoft 擁有者的通信首選項。 |
> | 動作 | 微軟.BingMaps/清單通信首選項/操作 | 獲取 Microsoft 擁有者的通信首選項。 |
> |  | **mapApis** |  |
> | 動作 | Microsoft.BingMaps/mapApis/Read | 獲取 Microsoft 的資源。 |
> | 動作 | Microsoft.BingMaps/mapApis/Write | 更新微軟的資源。 |
> | 動作 | Microsoft.BingMaps/mapApis/Delete | 刪除 Microsoft 的資源。 |
> | 動作 | Microsoft.BingMaps/mapApis/regenerateKey/action | 為微軟重新生成金鑰。 |
> | 動作 | Microsoft.BingMaps/mapApis/listSecrets/action | 列出微軟的秘密. BingMaps/mapApis |
> | 動作 | 微軟.BingMaps/mapApis/清單使用指標/操作 | 列出微軟的指標。 |
> |  | **作業** |  |
> | 動作 | Microsoft.BingMaps/Operations/read | 列出微軟的操作。 |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

Azure 服務[:Azure 區塊鏈服務](../blockchain/workbench/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | 微軟.區塊鏈/註冊/操作 | 註冊區塊鏈資源供應商的訂閱。 |
> |  | **區塊鏈成員** |  |
> | 動作 | 微軟.區塊鏈/區塊鏈成員/閱讀 | 獲取或列出現有的區塊鏈成員。 |
> | 動作 | 微軟.區塊鏈/區塊鏈成員/寫入 | 創建或更新區塊鏈成員。 |
> | 動作 | 微軟.區塊鏈/區塊鏈成員/刪除 | 刪除現有的區塊鏈成員。 |
> | 動作 | 微軟.區塊鏈/區塊鏈成員/清單ApiKeys/行動 | 獲取或列出現有的區塊鏈成員 API 密鑰。 |
> |  | **區塊鏈成員/交易節點** |  |
> | 動作 | 微軟.區塊鏈/區塊鏈成員/交易節點/閱讀 | 獲取或列出現有的區塊鏈會員交易節點。 |
> | 動作 | 微軟.區塊鏈/區塊鏈成員/交易節點/寫入 | 創建或更新區塊鏈會員交易節點。 |
> | 動作 | 微軟.區塊鏈/區塊鏈成員/交易節點/刪除 | 刪除現有的區塊鏈會員交易節點。 |
> | 動作 | 微軟.區塊鏈/區塊鏈成員/交易節點/清單ApiKeys/操作 | 獲取或列出現有的區塊鏈會員事務節點 API 密鑰。 |
> |  | **臍帶成員** |  |
> | 動作 | 微軟.區塊鏈/有線會員/閱讀 | 獲取或列出現有的區塊鏈 Corda 會員。 |
> | 動作 | 微軟.區塊鏈/有線會員/寫入 | 創建或更新區塊鏈 Corda 會員。 |
> | 動作 | 微軟.區塊鏈/有線會員/刪除 | 刪除現有的區塊鏈 Corda 會員。 |
> |  | **位置** |  |
> | 動作 | 微軟.區塊鏈/位置/檢查名稱可用性/操作 | 檢查資源名稱是否有效且未使用。 |
> |  | **位置/區塊鏈成員操作結果** |  |
> | 動作 | 微軟.區塊鏈/位置/區塊鏈會員操作結果/閱讀 | 獲取區塊鏈成員的操作結果。 |
> |  | **操作** |  |
> | 動作 | 微軟.區塊鏈/操作/閱讀 | 列出微軟區塊鏈資源供應商的所有操作。 |
> |  | **區塊鏈成員/交易節點** |  |
> | DataAction | 微軟.區塊鏈/區塊鏈成員/交易節點/連接/操作 | 連接到區塊鏈會員交易節點。 |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

Azure 服務[:Azure 藍圖](../governance/blueprints/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.Blueprint/register/action | 註冊 Azure 藍圖資源提供者 |
> |  | **藍圖配置** |  |
> | 動作 | Microsoft.Blueprint/blueprintAssignments/read | 讀取任何藍圖成品 |
> | 動作 | Microsoft.Blueprint/blueprintAssignments/write | 建立或更新任何藍圖成品 |
> | 動作 | Microsoft.Blueprint/blueprintAssignments/delete | 刪除任何藍圖成品 |
> | 動作 | 微軟.藍圖/藍圖分配/誰的藍圖/行動 | 獲取 Azure 藍圖服務主體物件 ID。 |
> |  | **blueprintAssignments/assignmentOperations** |  |
> | 動作 | Microsoft.Blueprint/blueprintAssignments/assignmentOperations/read | 讀取任何藍圖成品 |
> |  | **藍圖** |  |
> | 動作 | Microsoft.Blueprint/blueprints/read | 讀取任何藍圖 |
> | 動作 | Microsoft.Blueprint/blueprints/write | 建立或更新任何藍圖 |
> | 動作 | Microsoft.Blueprint/blueprints/delete | 刪除任何藍圖 |
> |  | **blueprints/artifacts** |  |
> | 動作 | Microsoft.Blueprint/blueprints/artifacts/read | 讀取任何藍圖成品 |
> | 動作 | Microsoft.Blueprint/blueprints/artifacts/write | 建立或更新任何藍圖成品 |
> | 動作 | Microsoft.Blueprint/blueprints/artifacts/delete | 刪除任何藍圖成品 |
> |  | **blueprints/versions** |  |
> | 動作 | Microsoft.Blueprint/blueprints/versions/read | 讀取任何藍圖 |
> | 動作 | Microsoft.Blueprint/blueprints/versions/write | 建立或更新任何藍圖 |
> | 動作 | Microsoft.Blueprint/blueprints/versions/delete | 刪除任何藍圖 |
> |  | **blueprints/versions/artifacts** |  |
> | 動作 | Microsoft.Blueprint/blueprints/versions/artifacts/read | 讀取任何藍圖成品 |

## <a name="microsoftbotservice"></a>Microsoft.BotService

Azure 服務[:Azure 機器人服務](https://docs.microsoft.com/azure/bot-service/)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | 微軟.Bot服務/校名可用性/操作 | 檢查自動程式的名稱可用性 |
> | 動作 | 微軟.Bot服務/listauth服務提供者/行動 | 列出身分驗證服務提供者 |
> |  | **機器人服務** |  |
> | 動作 | Microsoft.BotService/botServices/read | 讀取 Bot 服務 |
> | 動作 | Microsoft.BotService/botServices/write | 寫入 Bot 服務 |
> | 動作 | Microsoft.BotService/botServices/delete | 刪除 Bot 服務 |
> |  | **botServices/channels** |  |
> | 動作 | Microsoft.BotService/botServices/channels/read | 閱讀機器人服務管道 |
> | 動作 | Microsoft.BotService/botServices/channels/write | 編寫機器人服務頻道 |
> | 動作 | Microsoft.BotService/botServices/channels/delete | 移除機器人服務通路 |
> | 動作 | 微軟.Bot服務/自動服務/頻道/清單通道與密鑰/操作 | 列出帶機密的自動服務通道 |
> |  | **botServices/connections** |  |
> | 動作 | Microsoft.BotService/botServices/connections/read | 讀取機器人服務連線 |
> | 動作 | Microsoft.BotService/botServices/connections/write | 編寫機器人服務連線 |
> | 動作 | Microsoft.BotService/botServices/connections/delete | 移除機器人服務連線 |
> |  | **機器人服務/連線/清單與機密** |  |
> | 動作 | 微軟.Bot服務/自動服務/連接/清單與秘密/寫入 | 編寫機器人服務連線清單  |
> |  | **位置/動作結果** |  |
> | 動作 | Microsoft.BotService/locations/operationresults/read | 讀取非同步作業的狀態 |
> |  | **作業** |  |
> | 動作 | Microsoft.BotService/Operations/read | 讀取所有資源類型的作業 |

## <a name="microsoftcache"></a>Microsoft.Cache

Azure 服務:[用於雷瑞斯的 Azure 緩存](../azure-cache-for-redis/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.Cache/checknameavailability/action | 檢查名稱是否可用於新的 Redis 快取 |
> | 動作 | Microsoft.Cache/register/action | 向訂用帳戶註冊 'Microsoft.Cache' 資源提供者 |
> | 動作 | Microsoft.Cache/unregister/action | 向訂用帳戶取消註冊 'Microsoft.Cache' 資源提供者 |
> |  | **位置/動作結果** |  |
> | 動作 | Microsoft.Cache/locations/operationresults/read | 取得先前已將 'Location' 標頭傳回用戶端之長時間執行作業的結果 |
> |  | **操作** |  |
> | 動作 | Microsoft.Cache/operations/read | 列出 'Microsoft.Cache' 提供者支援的作業。 |
> |  | **雷裡斯** |  |
> | 動作 | Microsoft.Cache/redis/write | 在管理入口網站中修改 Redis 快取的設定和組態 |
> | 動作 | Microsoft.Cache/redis/read | 在管理入口網站中檢視 Redis 快取的設定和組態 |
> | 動作 | Microsoft.Cache/redis/delete | 刪除整個 Redis 快取 |
> | 動作 | Microsoft.Cache/redis/listKeys/action | 在管理入口網站中檢視 Redis 快取存取金鑰的值 |
> | 動作 | Microsoft.Cache/redis/regenerateKey/action | 在管理入口網站中變更 Redis 快取存取金鑰的值 |
> | 動作 | Microsoft.Cache/redis/import/action | 從多個 Blob 將指定格式的資料匯入 Redis |
> | 動作 | Microsoft.Cache/redis/export/action | 以指定格式將 Redis 資料匯出至有前置詞的儲存體 Blob |
> | 動作 | Microsoft.Cache/redis/forceReboot/action | 強制重新啟動快取執行個體，但可能會造成資料遺失。 |
> | 動作 | Microsoft.Cache/redis/stop/action | 停止快取執行個體。 |
> | 動作 | Microsoft.Cache/redis/start/action | 啟動快取執行個體。 |
> |  | **瑞瑞斯/防火牆規則** |  |
> | 動作 | Microsoft.Cache/redis/firewallRules/read | 取得 Redis 快取的 IP 防火牆規則 |
> | 動作 | Microsoft.Cache/redis/firewallRules/write | 編輯 Redis 快取的 IP 防火牆規則 |
> | 動作 | Microsoft.Cache/redis/firewallRules/delete | 刪除 Redis 快取的 IP 防火牆規則 |
> |  | **redis/連結伺服器** |  |
> | 動作 | Microsoft.Cache/redis/linkedservers/read | 取得與 Redis 快取相關聯的連結伺服器。 |
> | 動作 | Microsoft.Cache/redis/linkedservers/write | 對 Redis 快取新增連結伺服器 |
> | 動作 | Microsoft.Cache/redis/linkedservers/delete | 從 Redis 快取中刪除連結伺服器 |
> |  | **重寫/指標定義** |  |
> | 動作 | Microsoft.Cache/redis/metricDefinitions/read | 取得 Redis 快取可用的計量 |
> |  | **瑞瑞斯/補丁計劃** |  |
> | 動作 | Microsoft.Cache/redis/patchSchedules/read | 取得 Redis 快取的修補排程 |
> | 動作 | Microsoft.Cache/redis/patchSchedules/write | 修改 Redis 快取的修補排程 |
> | 動作 | Microsoft.Cache/redis/patchSchedules/delete | 刪除 Redis 快取的修補排程 |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

Azure 服務:核心

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.Capacity/calculateprice/action | 計算任何保留價格 |
> | 動作 | Microsoft.Capacity/checkoffers/action | 檢查任何訂用帳戶供應項目 |
> | 動作 | Microsoft.Capacity/checkscopes/action | 檢查任何訂用帳戶 |
> | 動作 | Microsoft.Capacity/validatereservationorder/action | 驗證任何保留項目 |
> | 動作 | Microsoft.Capacity/reservationorders/action | 更新任何保留項目 |
> | 動作 | Microsoft.Capacity/register/action | 註冊容量資源提供者，並讓您能夠建立容量資源。 |
> | 動作 | Microsoft.Capacity/unregister/action | 取消註冊任何租用戶 |
> | 動作 | 微軟.容量/計算交換/操作 | 計算新購買和退貨策略錯誤的交換金額和價格。 |
> | 動作 | 微軟.容量/交換/行動 | 交換任何預訂 |
> |  | **申請保留** |  |
> | 動作 | Microsoft.Capacity/appliedreservations/read | 讀取所有保留項目 |
> |  | **catalogs** |  |
> | 動作 | Microsoft.Capacity/catalogs/read | 讀取保留目錄 |
> |  | **商業預訂單** |  |
> | 動作 | Microsoft.Capacity/commercialreservationorders/read | 取得在任何租用戶中建立的保留順序 |
> |  | **操作** |  |
> | 動作 | Microsoft.Capacity/operations/read | 讀取任何作業 |
> |  | **預訂訂單** |  |
> | 動作 | Microsoft.Capacity/reservationorders/availablescopes/action | 尋找任何可用的範圍 |
> | 動作 | Microsoft.Capacity/reservationorders/read | 讀取所有保留項目 |
> | 動作 | Microsoft.Capacity/reservationorders/write | 建立任何保留項目 |
> | 動作 | Microsoft.Capacity/reservationorders/delete | 刪除任何保留項目 |
> | 動作 | Microsoft.Capacity/reservationorders/reservations/action | 更新任何保留項目 |
> | 動作 | Microsoft.Capacity/reservationorders/return/action | 傳回任何保留項目 |
> | 動作 | Microsoft.Capacity/reservationorders/swap/action | 交換任何保留項目 |
> | 動作 | Microsoft.Capacity/reservationorders/split/action | 分割任何保留項目 |
> | 動作 | Microsoft.Capacity/reservationorders/merge/action | 合併任何保留項目 |
> | 動作 | 微軟.容量/預訂單/計算退款/行動 | 計算新購買和退貨策略錯誤的退款金額和價格。 |
> |  | **預留單/合併操作結果** |  |
> | 動作 | 微軟.容量/預訂單/合併操作結果/讀取 | 輪詢任何合併操作 |
> |  | **預訂單/預訂** |  |
> | 動作 | 微軟.容量/預訂單/預訂/可用示波器/操作 | 尋找任何可用的範圍 |
> | 動作 | Microsoft.Capacity/reservationorders/reservations/read | 讀取所有保留項目 |
> | 動作 | Microsoft.Capacity/reservationorders/reservations/write | 建立任何保留項目 |
> | 動作 | Microsoft.Capacity/reservationorders/reservations/delete | 刪除任何保留項目 |
> |  | **預訂單/預訂/修訂** |  |
> | 動作 | Microsoft.Capacity/reservationorders/reservations/revisions/read | 讀取所有保留項目 |
> |  | **預留單/分割操作結果** |  |
> | 動作 | 微軟.容量/預訂單/拆分操作結果/讀取 | 輪詢任何分割操作 |
> |  | **租戶** |  |
> | 動作 | Microsoft.Capacity/tenants/register/action | 註冊任何租用戶 |

## <a name="microsoftcdn"></a>Microsoft.Cdn

Azure 服務:[內容傳遞網路](../cdn/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.Cdn/register/action | 為 CDN 資源提供者註冊訂用帳戶，並讓您能夠建立 CDN 設定檔。 |
> | 動作 | Microsoft.Cdn/CheckNameAvailability/action |  |
> | 動作 | Microsoft.Cdn/ValidateProbe/action |  |
> | 動作 | Microsoft.Cdn/CheckResourceUsage/action |  |
> |  | **edgenodes** |  |
> | 動作 | Microsoft.Cdn/edgenodes/read |  |
> | 動作 | Microsoft.Cdn/edgenodes/write |  |
> | 動作 | Microsoft.Cdn/edgenodes/delete |  |
> |  | **動作結果** |  |
> | 動作 | Microsoft.Cdn/operationresults/read |  |
> | 動作 | Microsoft.Cdn/operationresults/write |  |
> | 動作 | Microsoft.Cdn/operationresults/delete |  |
> |  | **操作結果/設定檔結果** |  |
> | 動作 | Microsoft.Cdn/operationresults/profileresults/read |  |
> | 動作 | Microsoft.Cdn/operationresults/profileresults/write |  |
> | 動作 | Microsoft.Cdn/operationresults/profileresults/delete |  |
> | 動作 | Microsoft.Cdn/operationresults/profileresults/CheckResourceUsage/action |  |
> | 動作 | Microsoft.Cdn/operationresults/profileresults/GenerateSsoUri/action |  |
> | 動作 | Microsoft.Cdn/operationresults/profileresults/GetSupportedOptimizationTypes/action |  |
> |  | **操作結果/設定檔結果/終結點結果** |  |
> | 動作 | Microsoft.Cdn/operationresults/profileresults/endpointresults/read |  |
> | 動作 | Microsoft.Cdn/operationresults/profileresults/endpointresults/write |  |
> | 動作 | Microsoft.Cdn/operationresults/profileresults/endpointresults/delete |  |
> | 動作 | Microsoft.Cdn/operationresults/profileresults/endpointresults/CheckResourceUsage/action |  |
> | 動作 | Microsoft.Cdn/operationresults/profileresults/endpointresults/Start/action |  |
> | 動作 | Microsoft.Cdn/operationresults/profileresults/endpointresults/Stop/action |  |
> | 動作 | Microsoft.Cdn/operationresults/profileresults/endpointresults/Purge/action |  |
> | 動作 | Microsoft.Cdn/operationresults/profileresults/endpointresults/Load/action |  |
> | 動作 | Microsoft.Cdn/operationresults/profileresults/endpointresults/ValidateCustomDomain/action |  |
> |  | **操作結果/設定檔結果/終結點結果/自訂網域結果** |  |
> | 動作 | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/read |  |
> | 動作 | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/write |  |
> | 動作 | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/delete |  |
> | 動作 | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/DisableCustomHttps/action |  |
> | 動作 | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/EnableCustomHttps/action |  |
> |  | **操作結果/設定檔結果/終結點結果/原點組結果** |  |
> | 動作 | Microsoft.Cdn/操作結果/設定檔結果/終結點結果/原點組結果/讀取 |  |
> | 動作 | Microsoft.Cdn/操作結果/設定檔結果/終結點結果/原點組結果/寫入 |  |
> | 動作 | Microsoft.Cdn/操作結果/設定檔結果/終結點結果/原點組結果/刪除 |  |
> |  | **操作結果/設定檔結果/端點結果/原點結果** |  |
> | 動作 | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/read |  |
> | 動作 | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/write |  |
> | 動作 | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/delete |  |
> |  | **操作** |  |
> | 動作 | Microsoft.Cdn/operations/read |  |
> |  | **設定檔** |  |
> | 動作 | Microsoft.Cdn/profiles/read |  |
> | 動作 | Microsoft.Cdn/profiles/write |  |
> | 動作 | Microsoft.Cdn/profiles/delete |  |
> | 動作 | Microsoft.Cdn/profiles/CheckResourceUsage/action |  |
> | 動作 | Microsoft.Cdn/profiles/GenerateSsoUri/action |  |
> | 動作 | Microsoft.Cdn/profiles/GetSupportedOptimizationTypes/action |  |
> |  | **profiles/endpoints** |  |
> | 動作 | Microsoft.Cdn/profiles/endpoints/read |  |
> | 動作 | Microsoft.Cdn/profiles/endpoints/write |  |
> | 動作 | Microsoft.Cdn/profiles/endpoints/delete |  |
> | 動作 | Microsoft.Cdn/profiles/endpoints/CheckResourceUsage/action |  |
> | 動作 | Microsoft.Cdn/profiles/endpoints/Start/action |  |
> | 動作 | Microsoft.Cdn/profiles/endpoints/Stop/action |  |
> | 動作 | Microsoft.Cdn/profiles/endpoints/Purge/action |  |
> | 動作 | Microsoft.Cdn/profiles/endpoints/Load/action |  |
> | 動作 | Microsoft.Cdn/profiles/endpoints/ValidateCustomDomain/action |  |
> |  | **profiles/endpoints/customdomains** |  |
> | 動作 | Microsoft.Cdn/profiles/endpoints/customdomains/read |  |
> | 動作 | Microsoft.Cdn/profiles/endpoints/customdomains/write |  |
> | 動作 | Microsoft.Cdn/profiles/endpoints/customdomains/delete |  |
> | 動作 | Microsoft.Cdn/profiles/endpoints/customdomains/DisableCustomHttps/action |  |
> | 動作 | Microsoft.Cdn/profiles/endpoints/customdomains/EnableCustomHttps/action |  |
> |  | **設定檔/終結點/原點組** |  |
> | 動作 | 微軟.Cdn/設定檔/終結點/原始檔組/讀取 |  |
> | 動作 | 微軟.Cdn/設定檔/終結點/源組/寫入 |  |
> | 動作 | 微軟.Cdn/設定檔/終結點/原點組/刪除 |  |
> |  | **profiles/endpoints/origins** |  |
> | 動作 | Microsoft.Cdn/profiles/endpoints/origins/read |  |
> | 動作 | Microsoft.Cdn/profiles/endpoints/origins/write |  |
> | 動作 | Microsoft.Cdn/profiles/endpoints/origins/delete |  |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

Azure 服務:[應用服務憑證](../app-service/configure-ssl-certificate.md#import-an-app-service-certificate)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.CertificateRegistration/provisionGlobalAppServicePrincipalInUserTenant/Action | 為服務應用程式主體佈建服務主體 |
> | 動作 | Microsoft.CertificateRegistration/validateCertificateRegistrationInformation/Action | 驗證憑證購買物件，但不將其提交出去 |
> | 動作 | Microsoft.CertificateRegistration/register/action | 針對訂用帳戶註冊 Microsoft 憑證資源提供者 |
> |  | **憑證訂單** |  |
> | 動作 | Microsoft.CertificateRegistration/certificateOrders/Write | 新增 certificateOrder 或更新現有 certificateOrder |
> | 動作 | Microsoft.CertificateRegistration/certificateOrders/Delete | 刪除現有的 AppServiceCertificate |
> | 動作 | Microsoft.CertificateRegistration/certificateOrders/Read | 取得 CertificateOrders 的清單 |
> | 動作 | Microsoft.CertificateRegistration/certificateOrders/reissue/Action | 重新發行現有的 certificateorder |
> | 動作 | Microsoft.CertificateRegistration/certificateOrders/renew/Action | 更新現有的 certificateorder |
> | 動作 | Microsoft.CertificateRegistration/certificateOrders/retrieveCertificateActions/Action | 擷取憑證動作的清單 |
> | 動作 | Microsoft.CertificateRegistration/certificateOrders/retrieveEmailHistory/Action | 擷取憑證電子郵件的歷程記錄 |
> | 動作 | Microsoft.CertificateRegistration/certificateOrders/resendEmail/Action | 重新傳送憑證電子郵件 |
> | 動作 | Microsoft.CertificateRegistration/certificateOrders/verifyDomainOwnership/Action | 確認網域擁有權 |
> | 動作 | Microsoft.CertificateRegistration/certificateOrders/resendRequestEmails/Action | 將要求電子郵件重新傳送至其他電子郵件地址 |
> | 動作 | Microsoft.CertificateRegistration/certificateOrders/resendRequestEmails/Action | 擷取已發行之 App Service 憑證的網站密封 |
> |  | **certificateOrders/certificates** |  |
> | 動作 | Microsoft.CertificateRegistration/certificateOrders/certificates/Write | 新增憑證或更新現有憑證 |
> | 動作 | Microsoft.CertificateRegistration/certificateOrders/certificates/Delete | 刪除現有憑證 |
> | 動作 | Microsoft.CertificateRegistration/certificateOrders/certificates/Read | 取得憑證清單 |
> |  | **操作** |  |
> | 動作 | Microsoft.CertificateRegistration/operations/Read | 列出 App Service 憑證註冊的所有作業 |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

Azure 服務:經典部署模型虛擬機器

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.ClassicCompute/register/action | 傳統計算的暫存器 |
> | 動作 | Microsoft.ClassicCompute/checkDomainNameAvailability/action | 檢查給定網域名稱的可用性。 |
> | 動作 | Microsoft.ClassicCompute/moveSubscriptionResources/action | 將所有傳統資源移動到不同訂用帳戶。 |
> | 動作 | Microsoft.ClassicCompute/validateSubscriptionMoveAvailability/action | 驗證訂用帳戶是否可進行傳統移動作業。 |
> |  | **能力** |  |
> | 動作 | Microsoft.ClassicCompute/capabilities/read | 顯示功能 |
> |  | **檢查網域名稱可用性** |  |
> | 動作 | Microsoft.ClassicCompute/checkDomainNameAvailability/read | 取得指定網域名稱的可用性。 |
> |  | **domainNames** |  |
> | 動作 | Microsoft.ClassicCompute/domainNames/read | 傳回資源的網域名稱。 |
> | 動作 | Microsoft.ClassicCompute/domainNames/write | 新增或修改資源的網域名稱。 |
> | 動作 | Microsoft.ClassicCompute/domainNames/delete | 移除資源的網域名稱。 |
> | 動作 | Microsoft.ClassicCompute/domainNames/swap/action | 將預備位置切換到生產位置。 |
> |  | **功能變數名稱/活動** |  |
> | 動作 | Microsoft.ClassicCompute/domainNames/active/write | 設定作用中的網域名稱。 |
> |  | **功能變數名稱/可用性集** |  |
> | 動作 | Microsoft.ClassicCompute/domainNames/availabilitySets/read | 顯示資源的可用性設定組。 |
> |  | **domainNames/capabilities** |  |
> | 動作 | Microsoft.ClassicCompute/domainNames/capabilities/read | 顯示網域名稱功能 |
> |  | **功能變數名稱/部署槽** |  |
> | 動作 | Microsoft.ClassicCompute/domainNames/deploymentslots/read | 顯示部署位置。 |
> | 動作 | Microsoft.ClassicCompute/domainNames/deploymentslots/write | 建立或更新部署。 |
> |  | **功能變數名稱/部署槽/角色** |  |
> | 動作 | Microsoft.ClassicCompute/domainNames/deploymentslots/roles/read | 取得網域名稱之部署位置上的角色 |
> |  | **功能變數名稱/部署槽/角色/角色實例** |  |
> | 動作 | Microsoft.ClassicCompute/domainNames/deploymentslots/roles/roleinstances/read | 取得網域名稱之部署位置上的角色執行個體 |
> |  | **功能變數名稱/部署槽/狀態** |  |
> | 動作 | Microsoft.ClassicCompute/domainNames/deploymentslots/state/read | 取得部署位置狀態。 |
> | 動作 | Microsoft.ClassicCompute/domainNames/deploymentslots/state/write | 新增部署位置狀態。 |
> |  | **功能變數名稱/部署槽/升級域** |  |
> | 動作 | Microsoft.ClassicCompute/domainNames/deploymentslots/upgradedomain/read | 取得網域名稱上之部署位置的升級網域 |
> | 動作 | Microsoft.ClassicCompute/domainNames/deploymentslots/upgradedomain/write | 更新網域名稱上之部署位置的升級網域 |
> |  | **網域名稱/延伸** |  |
> | 動作 | Microsoft.ClassicCompute/domainNames/extensions/read | 傳回網域名稱副檔名。 |
> | 動作 | Microsoft.ClassicCompute/domainNames/extensions/write | 新增網域名稱副檔名。 |
> | 動作 | Microsoft.ClassicCompute/domainNames/extensions/delete | 移除網域名稱副檔名。 |
> |  | **網域名稱/延伸/操作狀態** |  |
> | 動作 | Microsoft.ClassicCompute/domainNames/extensions/operationStatuses/read | 讀取網域名稱副檔名的作業狀態。 |
> |  | **domainNames/internalLoadBalancers** |  |
> | 動作 | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/read | 取得內部負載平衡器。 |
> | 動作 | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/write | 建立新的內部負載平衡。 |
> | 動作 | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/delete | 移除新的內部負載平衡。 |
> |  | **網域名稱/內部負載平衡器/操作狀態** |  |
> | 動作 | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/operationStatuses/read | 讀取網域名稱內部負載平衡器的作業狀態。 |
> |  | **網域名稱/載入平衡終結點集** |  |
> | 動作 | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/read | 取得已負載平衡的端點集。 |
> | 動作 | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/write | 新增已負載平衡的端點集。 |
> |  | **網域名稱/載入平衡終結點集/操作狀態** |  |
> | 動作 | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/operationStatuses/read | 讀取網域名稱之已負載平衡端點集的作業狀態。 |
> |  | **網域名稱/操作狀態** |  |
> | 動作 | Microsoft.ClassicCompute/domainNames/operationstatuses/read | 取得網域名稱的作業狀態。 |
> | 動作 | Microsoft.ClassicCompute/domainNames/operationStatuses/read | 讀取網域名稱副檔名的作業狀態。 |
> |  | **domainNames/serviceCertificates** |  |
> | 動作 | Microsoft.ClassicCompute/domainNames/serviceCertificates/read | 傳回所使用的服務憑證。 |
> | 動作 | Microsoft.ClassicCompute/domainNames/serviceCertificates/write | 新增或修改所使用的服務憑證。 |
> | 動作 | Microsoft.ClassicCompute/domainNames/serviceCertificates/delete | 刪除所使用的服務憑證。 |
> |  | **網域名稱/服務憑證/操作狀態** |  |
> | 動作 | Microsoft.ClassicCompute/domainNames/serviceCertificates/operationStatuses/read | 讀取網域名稱服務憑證的作業狀態。 |
> |  | **domainNames/slots** |  |
> | 動作 | Microsoft.ClassicCompute/domainNames/slots/read | 顯示部署位置。 |
> | 動作 | Microsoft.ClassicCompute/domainNames/slots/write | 建立或更新部署。 |
> | 動作 | Microsoft.ClassicCompute/domainNames/slots/delete | 刪除給定的部署位置。 |
> | 動作 | Microsoft.ClassicCompute/domainNames/slots/start/action | 啟動部署位置。 |
> | 動作 | Microsoft.ClassicCompute/domainNames/slots/stop/action | 暫止部署位置。 |
> | 動作 | Microsoft.ClassicCompute/domainNames/slots/validateMigration/action | 驗證部署位置的移轉。 |
> | 動作 | Microsoft.ClassicCompute/domainNames/slots/prepareMigration/action | 準備部署位置的移轉。 |
> | 動作 | Microsoft.ClassicCompute/domainNames/slots/commitMigration/action | 認可部署位置的移轉。 |
> | 動作 | Microsoft.ClassicCompute/domainNames/slots/abortMigration/action | 中止部署位置的移轉。 |
> |  | **網域名稱/插槽/操作狀態** |  |
> | 動作 | Microsoft.ClassicCompute/domainNames/slots/operationStatuses/read | 讀取網域名稱位置的作業狀態。 |
> |  | **domainNames/slots/roles** |  |
> | 動作 | Microsoft.ClassicCompute/domainNames/slots/roles/read | 取得部署位置的角色。 |
> | 動作 | Microsoft.ClassicCompute/domainNames/slots/roles/write | 新增部署位置的角色。 |
> |  | **網域名稱/插槽/角色/擴展引用** |  |
> | 動作 | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/read | 傳回部署位置角色的擴充功能參考。 |
> | 動作 | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/write | 新增或修改部署位置角色的擴充功能參考。 |
> | 動作 | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/delete | 移除部署位置角色的擴充功能參考。 |
> |  | **網域名稱/插槽/角色/擴展引用/操作狀態** |  |
> | 動作 | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/operationStatuses/read | 讀取網域名稱位置角色擴充功能參考的作業狀態。 |
> |  | **功能變數名稱/插槽/角色/指標定義** |  |
> | 動作 | Microsoft.ClassicCompute/domainNames/slots/roles/metricdefinitions/read | 取得網域名稱的角色計量定義。 |
> |  | **功能變數名稱/插槽/角色/指標** |  |
> | 動作 | Microsoft.ClassicCompute/domainNames/slots/roles/metrics/read | 取得網域名稱的角色計量。 |
> |  | **網域名稱/插槽/角色/操作狀態** |  |
> | 動作 | Microsoft.ClassicCompute/domainNames/slots/roles/operationstatuses/read | 取得網域名稱位置角色的作業狀態。 |
> |  | **功能變數名稱/插槽/角色/供應商/微軟.見解/診斷設置** |  |
> | 動作 | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/read | 取得診斷設定。 |
> | 動作 | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/write | 新增或修改診斷設定。 |
> |  | **功能變數名稱/插槽/角色/供應商/微軟.見解/指標定義** |  |
> | 動作 | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/metricDefinitions/read | 取得計量定義。 |
> |  | **功能變數名稱/插槽/角色/角色實例** |  |
> | 動作 | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/downloadremotedesktopconnectionfile/action | 下載網域名稱位置角色上角色執行個體的遠端桌面連線檔案。 |
> | 動作 | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/read | 取得角色執行個體。 |
> | 動作 | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/restart/action | 重新啟動角色執行個體。 |
> | 動作 | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/reimage/action | 針對角色執行個體重新安裝映像。 |
> | 動作 | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/rebuild/action | 重建角色執行個體。 |
> |  | **網域名稱/插槽/角色/角色實體/操作狀態** |  |
> | 動作 | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/operationStatuses/read | 取得網域名稱位置角色上角色執行個體的作業狀態。 |
> |  | **功能變數名稱/插槽/角色/斯克** |  |
> | 動作 | Microsoft.ClassicCompute/domainNames/slots/roles/skus/read | 取得部署位置的角色 SKU。 |
> |  | **功能變數名稱/插槽/狀態/開始** |  |
> | 動作 | Microsoft.ClassicCompute/domainNames/slots/state/start/write | 將部署位置狀態變更為停止。 |
> |  | **功能變數名稱/插槽/狀態/停止** |  |
> | 動作 | Microsoft.ClassicCompute/domainNames/slots/state/stop/write | 將部署位置狀態變更為啟動。 |
> |  | **功能變數名稱/插槽/升級域** |  |
> | 動作 | Microsoft.ClassicCompute/domainNames/slots/upgradeDomain/write | 逐步升級網域。 |
> |  | **operatingSystemFamilies** |  |
> | 動作 | Microsoft.ClassicCompute/operatingSystemFamilies/read | 列出可在 Microsoft Azure 使用的客體作業系統系列，同時也會列出各系列適用的作業系統版本。 |
> |  | **operatingSystems** |  |
> | 動作 | Microsoft.ClassicCompute/operatingSystems/read | 列出 Microsoft Azure 中目前可用的客體作業系統版本。 |
> |  | **操作** |  |
> | 動作 | Microsoft.ClassicCompute/operations/read | 取得作業的清單。 |
> |  | **操作 狀態** |  |
> | 動作 | Microsoft.ClassicCompute/operationStatuses/read | 讀取資源的作業狀態。 |
> |  | **quotas** |  |
> | 動作 | Microsoft.ClassicCompute/quotas/read | 取得訂用帳戶配額。 |
> |  | **資源類型/skus** |  |
> | 動作 | Microsoft.ClassicCompute/resourceTypes/skus/read | 取得受支援之資源類型的 SKU 清單。 |
> |  | **虛擬機器** |  |
> | 動作 | Microsoft.ClassicCompute/virtualMachines/read | 擷取虛擬機器清單。 |
> | 動作 | Microsoft.ClassicCompute/virtualMachines/write | 新增或修改虛擬機器。 |
> | 動作 | Microsoft.ClassicCompute/virtualMachines/delete | 移除虛擬機器。 |
> | 動作 | Microsoft.ClassicCompute/virtualMachines/capture/action | 擷取虛擬機器。 |
> | 動作 | Microsoft.ClassicCompute/virtualMachines/start/action | 啟動虛擬機器。 |
> | 動作 | Microsoft.ClassicCompute/virtualMachines/redeploy/action | 重新部署虛擬機器。 |
> | 動作 | Microsoft.ClassicCompute/virtualMachines/performMaintenance/action | 執行虛擬機器的維護。 |
> | 動作 | Microsoft.ClassicCompute/virtualMachines/restart/action | 重新啟動虛擬機器。 |
> | 動作 | Microsoft.ClassicCompute/virtualMachines/stop/action | 停止虛擬機器。 |
> | 動作 | Microsoft.ClassicCompute/virtualMachines/shutdown/action | 關閉虛擬機器。 |
> | 動作 | Microsoft.ClassicCompute/virtualMachines/attachDisk/action | 將資料磁碟連結至虛擬機器。 |
> | 動作 | Microsoft.ClassicCompute/virtualMachines/detachDisk/action | 中斷資料磁碟對虛擬機器的連結。 |
> | 動作 | Microsoft.ClassicCompute/virtualMachines/downloadRemoteDesktopConnectionFile/action | 下載虛擬機器的 RDP 檔案。 |
> |  | **虛擬機器/關聯的網路安全群組** |  |
> | 動作 | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/read | 取得與虛擬機器相關聯的網路安全性群組。 |
> | 動作 | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/write | 新增與虛擬機器相關聯的網路安全性群組。 |
> | 動作 | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/delete | 刪除與虛擬機器相關聯的網路安全性群組。 |
> |  | **虛擬機器/關聯的網路安全群組/操作狀態** |  |
> | 動作 | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/operationStatuses/read | 讀取與網路安全性群組相關聯之虛擬機器的作業狀態。 |
> |  | **虛擬機器/非同步操作** |  |
> | 動作 | Microsoft.ClassicCompute/virtualMachines/asyncOperations/read | 取得可能的非同步作業 |
> |  | **虛擬機器/診斷設定** |  |
> | 動作 | Microsoft.ClassicCompute/virtualMachines/diagnosticsettings/read | 取得虛擬機器診斷設定。 |
> |  | **虛擬機器/磁碟** |  |
> | 動作 | Microsoft.ClassicCompute/virtualMachines/disks/read | 擷取資料磁碟清單 |
> |  | **虛擬機器/擴展** |  |
> | 動作 | Microsoft.ClassicCompute/virtualMachines/extensions/read | 取得虛擬機器擴充功能。 |
> | 動作 | Microsoft.ClassicCompute/virtualMachines/extensions/write | 放置虛擬機器擴充功能。 |
> |  | **虛擬機器/延伸/操作狀態** |  |
> | 動作 | Microsoft.ClassicCompute/virtualMachines/extensions/operationStatuses/read | 讀取虛擬機器擴充功能的作業狀態。 |
> |  | **虛擬機器/指標定義** |  |
> | 動作 | Microsoft.ClassicCompute/virtualMachines/metricdefinitions/read | 取得虛擬機器計量定義。 |
> |  | **virtualMachines/metrics** |  |
> | 動作 | Microsoft.ClassicCompute/virtualMachines/metrics/read | 取得計量。 |
> |  | **虛擬機器/網路介面/關聯的網路安全群組** |  |
> | 動作 | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/read | 取得與網路介面相關聯的網路安全性群組。 |
> | 動作 | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/write | 新增與網路介面相關聯的網路安全性群組。 |
> | 動作 | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/delete | 刪除與網路介面相關聯的網路安全性群組。 |
> |  | **虛擬機器/網路介面/關聯的網路安全組/操作狀態** |  |
> | 動作 | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/operationStatuses/read | 讀取與網路安全性群組相關聯之虛擬機器的作業狀態。 |
> |  | **虛擬機器/操作狀態** |  |
> | 動作 | Microsoft.ClassicCompute/virtualMachines/operationStatuses/read | 讀取虛擬機器的作業狀態。 |
> |  | **虛擬機/供應商/微軟.見解/診斷設置** |  |
> | 動作 | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/read | 取得診斷設定。 |
> | 動作 | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/write | 新增或修改診斷設定。 |
> |  | **虛擬機/供應商/微軟.見解/指標定義** |  |
> | 動作 | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read | 取得計量定義。 |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

Azure 服務:經典部署模型虛擬網路

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.ClassicNetwork/register/action | 傳統網路的暫存器 |
> |  | **expressroutecrossconnections** |  |
> | 動作 | Microsoft.ClassicNetwork/expressroutecrossconnections/read | 取得 Express Route 交叉連線。 |
> | 動作 | Microsoft.ClassicNetwork/expressroutecrossconnections/write | 新增 Express Route 交叉連線。 |
> |  | **快速路由交叉連接/操作狀態** |  |
> | 動作 | Microsoft.ClassicNetwork/expressroutecrossconnections/operationstatuses/read | 取得 Express Route 交叉連線作業狀態。 |
> |  | **快速路由交叉連接/對等互連** |  |
> | 動作 | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/read | 取得 Express Route 交叉連線對等互連。 |
> | 動作 | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/write | 新增 Express Route 交叉連線對等互連。 |
> | 動作 | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/delete | 刪除 Express Route 交叉連線對等互連。 |
> |  | **快速路由交叉連接/對等互連/操作狀態** |  |
> | 動作 | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/operationstatuses/read | 取得 Express Route 交叉連線對等互連作業狀態。 |
> |  | **gatewaySupportedDevices** |  |
> | 動作 | Microsoft.ClassicNetwork/gatewaySupportedDevices/read | 擷取支援裝置清單。 |
> |  | **networkSecurityGroups** |  |
> | 動作 | Microsoft.ClassicNetwork/networkSecurityGroups/read | 取得網路安全性群組。 |
> | 動作 | Microsoft.ClassicNetwork/networkSecurityGroups/write | 新增網路安全性群組。 |
> | 動作 | Microsoft.ClassicNetwork/networkSecurityGroups/delete | 刪除網路安全性群組。 |
> |  | **網路安全群組/操作狀態** |  |
> | 動作 | Microsoft.ClassicNetwork/networkSecurityGroups/operationStatuses/read | 讀取網路安全性群組的作業狀態。 |
> |  | **網路安全組/供應商/微軟.見解/診斷設置** |  |
> | 動作 | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/read | 取得網路安全性群組診斷設定 |
> | 動作 | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/write | 建立或更新網路安全性群組診斷設定，此作業會由 Insights 資源提供者來補充。 |
> |  | **網路安全組/供應商/微軟.見解/日誌定義** |  |
> | 動作 | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/logDefinitions/read | 取得網路安全性群組的事件 |
> |  | **網路安全群組/安全規則** |  |
> | 動作 | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/read | 取得安全性規則。 |
> | 動作 | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/write | 新增或更新安全性規則。 |
> | 動作 | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/delete | 刪除安全性規則。 |
> |  | **網路安全群組/安全規則/操作狀態** |  |
> | 動作 | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/operationStatuses/read | 讀取網路安全性群組安全性規則的作業狀態。 |
> |  | **操作** |  |
> | 動作 | Microsoft.ClassicNetwork/operations/read | 取得傳統網路作業。 |
> |  | **quotas** |  |
> | 動作 | Microsoft.ClassicNetwork/quotas/read | 取得訂用帳戶配額。 |
> |  | **保留 Ip** |  |
> | 動作 | Microsoft.ClassicNetwork/reservedIps/read | 取得保留的 IP |
> | 動作 | Microsoft.ClassicNetwork/reservedIps/write | 新增保留的 IP |
> | 動作 | Microsoft.ClassicNetwork/reservedIps/delete | 刪除保留的 IP。 |
> | 動作 | Microsoft.ClassicNetwork/reservedIps/link/action | 連結保留的 IP |
> | 動作 | Microsoft.ClassicNetwork/reservedIps/join/action | 加入保留的 IP |
> |  | **保留 Ip/作業狀態** |  |
> | 動作 | Microsoft.ClassicNetwork/reservedIps/operationStatuses/read | 讀取保留之 IP 的作業狀態。 |
> |  | **virtualNetworks** |  |
> | 動作 | Microsoft.ClassicNetwork/virtualNetworks/read | 取得虛擬網路。 |
> | 動作 | Microsoft.ClassicNetwork/virtualNetworks/write | 新增虛擬網路。 |
> | 動作 | Microsoft.ClassicNetwork/virtualNetworks/delete | 刪除虛擬網路。 |
> | 動作 | Microsoft.ClassicNetwork/virtualNetworks/peer/action | 讓某個虛擬網路與另一個虛擬網路對等互連。 |
> | 動作 | Microsoft.ClassicNetwork/virtualNetworks/join/action | 加入虛擬網路。 |
> | 動作 | Microsoft.ClassicNetwork/virtualNetworks/checkIPAddressAvailability/action | 檢查虛擬網路中給定 IP 位址的可用性。 |
> | 動作 | Microsoft.ClassicNetwork/virtualNetworks/validateMigration/action | 驗證虛擬網路移轉 |
> | 動作 | Microsoft.ClassicNetwork/virtualNetworks/prepareMigration/action | 準備虛擬網路移轉 |
> | 動作 | Microsoft.ClassicNetwork/virtualNetworks/commitMigration/action | 認可虛擬網路移轉 |
> | 動作 | Microsoft.ClassicNetwork/virtualNetworks/abortMigration/action | 中止虛擬網路移轉 |
> |  | **虛擬網路/功能** |  |
> | 動作 | Microsoft.ClassicNetwork/virtualNetworks/capabilities/read | 顯示功能 |
> |  | **虛擬網路/閘道** |  |
> | 動作 | Microsoft.ClassicNetwork/virtualNetworks/gateways/read | 取得虛擬網路閘道。 |
> | 動作 | Microsoft.ClassicNetwork/virtualNetworks/gateways/write | 新增虛擬網路閘道。 |
> | 動作 | Microsoft.ClassicNetwork/virtualNetworks/gateways/delete | 刪除虛擬網路閘道。 |
> | 動作 | Microsoft.ClassicNetwork/virtualNetworks/gateways/startDiagnostics/action | 啟動虛擬網路閘道的診斷。 |
> | 動作 | Microsoft.ClassicNetwork/virtualNetworks/gateways/stopDiagnostics/action | 停止虛擬網路閘道的診斷。 |
> | 動作 | Microsoft.ClassicNetwork/virtualNetworks/gateways/downloadDiagnostics/action | 下載閘道診斷。 |
> | 動作 | Microsoft.ClassicNetwork/virtualNetworks/gateways/listCircuitServiceKey/action | 擷取迴路服務金鑰。 |
> | 動作 | Microsoft.ClassicNetwork/virtualNetworks/gateways/downloadDeviceConfigurationScript/action | 下載裝置組態指令碼。 |
> | 動作 | Microsoft.ClassicNetwork/virtualNetworks/gateways/listPackage/action | 列出虛擬網路閘道套件。 |
> |  | **虛擬網路/閘道/客戶端撤銷憑證** |  |
> | 動作 | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/read | 讀取已撤銷的用戶端憑證。 |
> | 動作 | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/write | 撤銷用戶端憑證。 |
> | 動作 | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/delete | 取消撤銷用戶端憑證。 |
> |  | **虛擬網路/閘道/用戶端根憑證** |  |
> | 動作 | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/read | 尋找用戶端根憑證。 |
> | 動作 | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/write | 上傳新的用戶端根憑證。 |
> | 動作 | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/delete | 刪除虛擬網路閘道用戶端憑證。 |
> | 動作 | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/download/action | 依指紋下載憑證。 |
> | 動作 | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/listPackage/action | 列出虛擬網路閘道憑證套件。 |
> |  | **虛擬網路/閘道/連線** |  |
> | 動作 | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/read | 擷取連線清單。 |
> | 動作 | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/connect/action | 建立網站對網站閘道連線。 |
> | 動作 | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/disconnect/action | 中斷網站對網站閘道連線。 |
> | 動作 | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/test/action | 測試網站對網站閘道連線。 |
> |  | **虛擬網路/閘道/操作狀態** |  |
> | 動作 | Microsoft.ClassicNetwork/virtualNetworks/gateways/operationStatuses/read | 讀取虛擬網路閘道的作業狀態。 |
> |  | **虛擬網路/閘道/包** |  |
> | 動作 | Microsoft.ClassicNetwork/virtualNetworks/gateways/packages/read | 取得虛擬網路閘道套件。 |
> |  | **虛擬網路/操作狀態** |  |
> | 動作 | Microsoft.ClassicNetwork/virtualNetworks/operationStatuses/read | 讀取虛擬網路的作業狀態。 |
> |  | **virtualNetworks/remoteVirtualNetworkPeeringProxies** |  |
> | 動作 | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/read | 取得遠端虛擬網路對等互連 Proxy。 |
> | 動作 | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/write | 新增或更新遠端虛擬網路對等互連 Proxy。 |
> | 動作 | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/delete | 刪除遠端虛擬網路對等互連 Proxy。 |
> |  | **虛擬網路/子網/關聯的網路安全組** |  |
> | 動作 | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/read | 取得與子網路相關聯的網路安全性群組。 |
> | 動作 | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/write | 新增與子網路相關聯的網路安全性群組。 |
> | 動作 | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/delete | 刪除與子網路相關聯的網路安全性群組。 |
> |  | **虛擬網路/子網/關聯的網路安全組/操作狀態** |  |
> | 動作 | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/operationStatuses/read | 讀取與網路安全性群組相關聯之虛擬網路子網路的作業狀態。 |
> |  | **virtualNetworks/virtualNetworkPeerings** |  |
> | 動作 | Microsoft.ClassicNetwork/virtualNetworks/virtualNetworkPeerings/read | 取得虛擬網路對等互連。 |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

Azure 服務:經典部署模型存儲

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.ClassicStorage/register/action | 傳統儲存體的暫存器 |
> | 動作 | Microsoft.ClassicStorage/checkStorageAccountAvailability/action | 檢查儲存體帳戶的可用性。 |
> |  | **能力** |  |
> | 動作 | Microsoft.ClassicStorage/capabilities/read | 顯示功能 |
> |  | **檢查儲存帳戶可用性** |  |
> | 動作 | Microsoft.ClassicStorage/checkStorageAccountAvailability/read | 取得儲存體帳戶的可用性。 |
> |  | **磁碟** |  |
> | 動作 | Microsoft.ClassicStorage/disks/read | 傳回儲存體帳戶磁碟。 |
> |  | **影像** |  |
> | 動作 | Microsoft.ClassicStorage/images/read | 傳回映像。 |
> |  | **影像/操作狀態** |  |
> | 動作 | Microsoft.ClassicStorage/images/operationstatuses/read | 取得映像作業狀態。 |
> |  | **操作** |  |
> | 動作 | Microsoft.ClassicStorage/operations/read | 取得傳統儲存體作業 |
> |  | **osImages** |  |
> | 動作 | Microsoft.ClassicStorage/osImages/read | 傳回作業系統映像。 |
> |  | **osPlatformImages** |  |
> | 動作 | Microsoft.ClassicStorage/osPlatformImages/read | 取得作業系統平台映像。 |
> |  | **publicImages** |  |
> | 動作 | Microsoft.ClassicStorage/publicImages/read | 取得公用虛擬機器映像。 |
> |  | **quotas** |  |
> | 動作 | Microsoft.ClassicStorage/quotas/read | 取得訂用帳戶配額。 |
> |  | **storageAccounts** |  |
> | 動作 | Microsoft.ClassicStorage/storageAccounts/read | 傳回具有給定帳戶的儲存體帳戶。 |
> | 動作 | Microsoft.ClassicStorage/storageAccounts/write | 新增儲存體帳戶。 |
> | 動作 | Microsoft.ClassicStorage/storageAccounts/delete | 刪除儲存體帳戶。 |
> | 動作 | Microsoft.ClassicStorage/storageAccounts/listKeys/action | 列出儲存體帳戶的存取金鑰。 |
> | 動作 | Microsoft.ClassicStorage/storageAccounts/regenerateKey/action | 重新產生儲存體帳戶的現有存取金鑰。 |
> | 動作 | Microsoft.ClassicStorage/storageAccounts/validateMigration/action | 驗證儲存體帳戶的移轉。 |
> | 動作 | Microsoft.ClassicStorage/storageAccounts/prepareMigration/action | 準備儲存體帳戶的移轉。 |
> | 動作 | Microsoft.ClassicStorage/storageAccounts/commitMigration/action | 認可儲存體帳戶的移轉。 |
> | 動作 | Microsoft.ClassicStorage/storageAccounts/abortMigration/action | 中止儲存體帳戶的移轉。 |
> |  | **儲存帳戶/blob 服務/提供者/微軟.見解/診斷設置** |  |
> | 動作 | 微軟.經典存儲/存儲帳戶/blob服務/提供者/微軟.見解/診斷設置/讀取 | 取得診斷設定。 |
> | 動作 | 微軟.經典存儲/存儲帳戶/blob服務/提供者/微軟.見解/診斷設置/寫入 | 新增或修改診斷設定。 |
> |  | **儲存帳戶/blob 服務/提供者/微軟.見解/指標定義** |  |
> | 動作 | 微軟.經典存儲/存儲帳戶/blob服務/供應商/微軟.見解/指標定義/讀取 | 取得計量定義。 |
> |  | **儲存帳號/磁碟** |  |
> | 動作 | Microsoft.ClassicStorage/storageAccounts/disks/read | 傳回儲存體帳戶磁碟。 |
> | 動作 | Microsoft.ClassicStorage/storageAccounts/disks/write | 新增儲存體帳戶磁碟。 |
> | 動作 | Microsoft.ClassicStorage/storageAccounts/disks/delete | 刪除給定的儲存體帳戶磁碟。 |
> |  | **儲存帳號/磁碟/操作狀態** |  |
> | 動作 | Microsoft.ClassicStorage/storageAccounts/disks/operationStatuses/read | 讀取資源的作業狀態。 |
> |  | **儲存帳戶/檔案服務/供應商/微軟.見解/診斷設置** |  |
> | 動作 | 微軟.經典存儲/存儲帳戶/檔案服務/供應商/微軟.見解/診斷設置/讀取 | 取得診斷設定。 |
> | 動作 | 微軟.經典存儲/存儲帳戶/檔案服務/供應商/微軟.見解/診斷設置/寫入 | 新增或修改診斷設定。 |
> |  | **儲存帳戶/檔案服務/供應商/微軟.見解/指標定義** |  |
> | 動作 | 微軟.經典存儲/存儲帳戶/檔案服務/供應商/微軟.見解/指標定義/讀取 | 取得計量定義。 |
> |  | **儲存帳號/影像** |  |
> | 動作 | Microsoft.ClassicStorage/storageAccounts/images/read | 傳回儲存體帳戶映像。 (已淘汰。 使用 'Microsoft.ClassicStorage/storageAccounts/vmImages') |
> | 動作 | Microsoft.ClassicStorage/storageAccounts/images/delete | 刪除給定的儲存體帳戶映像。 (已淘汰。 使用 'Microsoft.ClassicStorage/storageAccounts/vmImages') |
> |  | **儲存帳號/映像/操作狀態** |  |
> | 動作 | Microsoft.ClassicStorage/storageAccounts/images/operationstatuses/read | 傳回儲存體帳戶映像作業狀態。 |
> |  | **儲存帳戶/操作狀態** |  |
> | 動作 | Microsoft.ClassicStorage/storageAccounts/operationStatuses/read | 讀取資源的作業狀態。 |
> |  | **儲存帳號/osImage** |  |
> | 動作 | Microsoft.ClassicStorage/storageAccounts/osImages/read | 傳回儲存體帳戶的作業系統映像。 |
> | 動作 | Microsoft.ClassicStorage/storageAccounts/osImages/write | 新增給定儲存體帳戶作業系統映像。 |
> | 動作 | Microsoft.ClassicStorage/storageAccounts/osImages/delete | 刪除給定之儲存體帳戶的作業系統映像。 |
> |  | **儲存帳戶/供應商/微軟.見解/診斷設置** |  |
> | 動作 | 微軟.經典存儲/存儲帳戶/供應商/微軟.見解/診斷設置/讀取 | 取得診斷設定。 |
> | 動作 | 微軟.經典存儲/存儲帳戶/供應商/微軟.見解/診斷設置/寫入 | 新增或修改診斷設定。 |
> |  | **儲存帳戶/供應商/微軟.見解/指標定義** |  |
> | 動作 | 微軟.經典存儲/存儲帳戶/供應商/微軟.見解/指標定義/讀取 | 取得計量定義。 |
> |  | **儲存帳戶/佇列服務/供應商/微軟.見解/診斷設置** |  |
> | 動作 | 微軟.經典存儲/存儲帳戶/佇列服務/供應商/微軟.見解/診斷設置/讀取 | 取得診斷設定。 |
> | 動作 | 微軟.經典存儲/存儲帳戶/佇列服務/供應商/微軟.見解/診斷設置/寫入 | 新增或修改診斷設定。 |
> |  | **儲存帳戶/佇列服務/供應商/微軟.見解/指標定義** |  |
> | 動作 | 微軟.經典存儲/存儲帳戶/佇列服務/供應商/微軟.見解/指標定義/讀取 | 取得計量定義。 |
> |  | **storageAccounts/services** |  |
> | 動作 | Microsoft.ClassicStorage/storageAccounts/services/read | 取得可用服務。 |
> |  | **storageAccounts/services/diagnosticSettings** |  |
> | 動作 | Microsoft.ClassicStorage/storageAccounts/services/diagnosticSettings/read | 取得診斷設定。 |
> | 動作 | Microsoft.ClassicStorage/storageAccounts/services/diagnosticSettings/write | 新增或修改診斷設定。 |
> |  | **storageAccounts/services/metricDefinitions** |  |
> | 動作 | Microsoft.ClassicStorage/storageAccounts/services/metricDefinitions/read | 取得計量定義。 |
> |  | **儲存帳戶/服務/指標** |  |
> | 動作 | Microsoft.ClassicStorage/storageAccounts/services/metrics/read | 取得計量。 |
> |  | **儲存帳戶/表服務/供應商/微軟.見解/診斷設置** |  |
> | 動作 | 微軟.經典存儲/存儲帳戶/表服務/供應商/微軟.見解/診斷設置/讀取 | 取得診斷設定。 |
> | 動作 | 微軟.經典存儲/存儲帳戶/表服務/供應商/微軟.見解/診斷設置/寫入 | 新增或修改診斷設定。 |
> |  | **儲存帳戶/表服務/供應商/微軟.見解/指標定義** |  |
> | 動作 | 微軟.經典存儲/存儲帳戶/表服務/供應商/微軟.見解/指標定義/讀取 | 取得計量定義。 |
> |  | **storageAccounts/vmImages** |  |
> | 動作 | Microsoft.ClassicStorage/storageAccounts/vmImages/read | 傳回虛擬機器映像。 |
> | 動作 | Microsoft.ClassicStorage/storageAccounts/vmImages/write | 新增指定的虛擬機器映像。 |
> | 動作 | Microsoft.ClassicStorage/storageAccounts/vmImages/delete | 刪除指定的虛擬機器映像。 |
> |  | **儲存帳號/vmImage/操作狀態** |  |
> | 動作 | Microsoft.ClassicStorage/storageAccounts/vmImages/operationstatuses/read | 取得指定的虛擬機器映像作業狀態。 |
> |  | **vmImages** |  |
> | 動作 | Microsoft.ClassicStorage/vmImages/read | 列出虛擬機器映像。 |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

Azure 服務:[認知服務](../cognitive-services/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.CognitiveServices/register/action | 訂閱註冊操作 |
> | 動作 | Microsoft.CognitiveServices/register/action | 為認知服務註冊訂用帳戶 |
> | 動作 | 微軟.認知服務/檢查域可用性/操作 | 讀取訂閱的可用 SKU。 |
> | 動作 | Microsoft.CognitiveServices/register/action | 為認知服務註冊訂用帳戶 |
> |  | **帳戶** |  |
> | 動作 | Microsoft.CognitiveServices/accounts/read | 讀取 API 帳戶。 |
> | 動作 | Microsoft.CognitiveServices/accounts/write | 寫入 API 帳戶。 |
> | 動作 | Microsoft.CognitiveServices/accounts/delete | 刪除 API 帳戶 |
> | 動作 | Microsoft.CognitiveServices/accounts/listKeys/action | 列出金鑰 |
> | 動作 | Microsoft.CognitiveServices/accounts/regenerateKey/action | 重新產生金鑰 |
> |  | **帳戶/斯克** |  |
> | 動作 | Microsoft.CognitiveServices/accounts/skus/read | 讀取現有資源的可用 SKU。 |
> |  | **帳戶/使用方式** |  |
> | 動作 | Microsoft.CognitiveServices/accounts/usages/read | 取得現有資源的配額使用情況。 |
> |  | **位置** |  |
> | 動作 | Microsoft.CognitiveServices/locations/checkSkuAvailability/action | 讀取訂閱的可用 SKU。 |
> | 動作 | 微軟.認知服務/位置/刪除虛擬網路或子網/操作 | 從 Microsoft.網路中刪除虛擬網路或子網的通知。 |
> | 動作 | Microsoft.CognitiveServices/locations/checkSkuAvailability/action | 讀取訂閱的可用 SKU。 |
> |  | **位置/動作結果** |  |
> | 動作 | 微軟.認知服務/位置/操作結果/讀取 | 讀取非同步作業的狀態。 |
> |  | **作業** |  |
> | 動作 | Microsoft.CognitiveServices/Operations/read | 列出所有可用的作業 |
> |  | **帳號/Anomaly 檢測器/時間序列/整個** |  |
> | DataAction | 微軟.認知服務/帳戶/Anomaly檢測器/時間序列/完整/檢測/操作 | 此操作使用整個系列生成模型,使用同一模型檢測每個點。<br>使用此方法,使用特定點之前和之後的點來確定它是否是異常。<br>整個檢測可以為使用者提供時間序列的總體狀態。 |
> |  | **帳號/Anomaly 檢測器/時間序列/最後一個** |  |
> | DataAction | 微軟.認知服務/帳戶/AnomalyDetector/時間序列/最後/檢測/操作 | 此操作使用最新點之前的點生成模型。 使用此方法,僅使用歷史點來確定目標點是否為異常。 最新的檢測點與即時監視業務指標的方案相匹配。 |
> |  | **帳號/自動建議** |  |
> | DataAction | 微軟.認知服務/帳戶/自動建議/搜索/操作 | 此操作為給定查詢或部分查詢提供建議。 |
> |  | **帳號/電腦視覺** |  |
> | DataAction | 微軟.認知服務/帳戶/計算機視覺/分析/行動 | 此操作根據圖像內容提取一組豐富的可視功能。  |
> | DataAction | 微軟.認知服務/帳戶/計算機視覺/描述/操作 | 此操作以具有完整句子的人類可讀語言生成圖像的描述。<br> 描述基於內容標記的集合,該標記也會由操作返回。<br>可以為每個圖像生成多個描述。<br> 描述按其置信度分數排序。<br>所有描述都是英文的。 |
> | DataAction | 微軟.認知服務/帳戶/計算機視覺/生成縮略圖/操作 | 此操作將生成具有使用者指定的寬度和高度的縮圖圖像。<br> 根據預設，此服務會分析影像、識別相關區域 (ROI)，並根據 ROI 產生智慧裁剪的座標。<br> 當您指定與輸入影像不同的縱橫比時,智慧裁剪會有所説明 |
> | DataAction | 微軟.認知服務/帳戶/計算機視覺/ocr/行動 | 光學字元識別 (OCR) 檢測影像中的文本,並將識別的字元提取到機器可用的字元流中。    |
> | DataAction | 微軟.認知服務/帳戶/計算機視覺/識別文本/操作 | 使用此介面可以獲取識別文本操作的結果。 使用"識別文字"介面時,回應包含名為"操作-位置"的欄位。 '操作位置"欄位包含必須用於獲取識別文本操作結果操作的 URL。 |
> | DataAction | 微軟.認知服務/帳戶/計算機視覺/標籤/操作 | 此操作將生成與所提供圖像的內容相關的單詞或標記的清單。<br>計算機視覺 API 可以基於圖像中找到的物件、生物、風景或操作返回標記。<br>與類別不同,標記不是根據分層分類系統組織的,而是對應於圖像內容。<br>標籤可能包含提示以避免歧義或提供上下文,例如標籤「大提琴」可能伴隨著提示「樂器」。。<br>所有標籤均以英語為名。 |
> | DataAction | 微軟.認知服務/帳戶/計算機視覺/興趣領域/行動 | 此操作返回圖像最重要區域周圍的邊界框。 |
> | DataAction | 微軟.認知服務/帳戶/計算機視覺/檢測/操作 | 此操作對指定的映射執行物件檢測。  |
> |  | **帳號/電腦視覺/模型** |  |
> | DataAction | 微軟.認知服務/帳戶/計算機視覺/模型/閱讀 | 此操作返回電腦視覺 API 支援的特定於網域的模型的清單。  目前,API 支援以下特定於域的模型:名人識別器、地標識別器。 |
> | DataAction | 微軟.認知服務/帳戶/計算機視覺/模型/分析/操作 | 此操作通過應用特定於域的模型來識別圖像中的內容。<br> 可以使用 /model GET 請求檢索計算機視覺 API 支援的特定於域的模型清單。<br> 目前,API 提供以下特定於域的模型:名人、地標。 |
> |  | **帳號/電腦視覺/讀取** |  |
> | DataAction | 微軟.認知服務/帳戶/計算機視覺/閱讀/分析/操作 | 使用此介面執行讀取操作,採用針對文本重文檔優化的最先進的光學字元識別 (OCR) 演演演算法。<br>它可以處理手寫、列印或混合文檔。<br>使用 Read 介面時,回應包含名為"操作-位置"的標頭。<br>"操作位置"標頭包含必須用於獲取讀取結果操作以造訪OCR結果的 URL。 |
> |  | **帳號/電腦視覺/讀取/分析結果** |  |
> | DataAction | 微軟.認知服務/帳戶/計算機視覺/閱讀/分析結果/閱讀 | 使用此介面可以檢索讀取操作的狀態和 OCR 結果。  包含「操作 Id」的 URL 將在讀取操作「操作-位置」回應標頭中返回。 |
> |  | **帳號/電腦視覺/讀取/核心** |  |
> | DataAction | 微軟.認知服務/帳戶/計算機視覺/讀取/核心/非同步批次處理分析/操作 | 使用此介面抓取批次讀取檔案操作的結果,以最先進的光學字元 |
> |  | **帳號/電腦視覺/讀取/操作** |  |
> | DataAction | 微軟.認知服務/帳戶/計算機視覺/讀取/操作/讀取 | 此介面用於獲取讀取操作的OCR結果。 應從批次處理讀取檔介面傳<b>回的操作-位置欄</b>位檢索此介面的網址。 |
> |  | **帳號/電腦視覺/文字操作** |  |
> | DataAction | 微軟.認知服務/帳戶/計算機視覺/文本操作/讀取 | 此介面用於獲取識別文本操作結果。 應從「識別文字」 的介面傳回<b>的操作-位置「</b>欄位中檢索此介面的 URL。 |
> |  | **帳戶/內容管理員** |  |
> | DataAction | 微軟.認知服務/帳戶/內容管理員/圖像清單/操作 | 創建圖像清單。 |
> | DataAction | 微軟.認知服務/帳戶/內容管理員/術語清單/行動 | 建立術語清單。 |
> |  | **帳號/內容管理員/影像清單** |  |
> | DataAction | 微軟.認知服務/帳戶/內容管理員/圖像列表/閱讀 | 影像清單 ─ 取得詳細資訊 - 影像清單 - 抓取所有 |
> | DataAction | 微軟.認知服務/帳戶/內容管理員/圖像列表/刪除 | 影像清單 - 移除 |
> | DataAction | 微軟.認知服務/帳戶/內容管理員/圖像清單/刷新索引/操作 | 影像清單 - 刷新搜尋索引 |
> | DataAction | 微軟.認知服務/帳戶/內容管理員/圖像清單/寫入 | 影像清單 ─ 更新詳細資訊 |
> |  | **帳號/內容管理員/影像清單/影像** |  |
> | DataAction | 微軟.認知服務/帳戶/內容管理員/圖像列表/圖像/寫入 | 將圖像添加到圖像清單中。 使用圖像/匹配 API 時,圖像清單可用於對其他圖像進行模糊匹配。 |
> | DataAction | 微軟.認知服務/帳戶/內容管理員/圖像列表/圖像/刪除 | 從影像清單中刪除圖像。 使用圖像/匹配 API 時,圖像清單可用於對其他圖像進行模糊匹配。 從清單中刪除所有圖像。 使用圖像/匹配 API 時,圖像清單可用於對其他圖像進行模糊匹配。 |
> | DataAction | 微軟.認知服務/帳戶/內容管理員/圖像列表/圖像/閱讀 | 影像 -抓取所有影像代碼 |
> |  | **帳戶/內容管理員/流程影像** |  |
> | DataAction | 微軟.認知服務/帳戶/內容管理員/進程圖像/評估/行動 | 返回包含淫穢內容或成人內容的圖像的概率。 |
> | DataAction | 微軟.認知服務/帳戶/內容管理員/進程圖像/查找面/操作 | 在圖像中查找人臉。 |
> | DataAction | 微軟.認知服務/帳戶/內容管理員/進程圖像/匹配/操作 | 模糊地將圖像與自定義圖像清單之一匹配。 您可以使用此 API 建立及管理自訂映像清單。  |
> | DataAction | 微軟.認知服務/帳戶/內容管理員/進程圖像/ocr/操作 | 傳回影像中為指定語言找到的任何文本。 如果在輸入中未指定任何語言,則檢測預設為英語。 |
> |  | **帳號/內容管理員/程序文字** |  |
> | DataAction | 微軟.認知服務/帳戶/內容管理員/進程文本/檢測語言/操作 | 此操作將檢測給定輸入內容的語言。 返回包含提交文本的主要語言的 ISO 639-3 代碼。 支援超過 110 種語言。 |
> | DataAction | 微軟.認知服務/帳戶/內容管理員/進程文本/螢幕/操作 | 該操作檢測 100 多種語言的褻瀆行為,並匹配自定義和共用黑名單。 |
> |  | **帳戶/內容管理員/團隊** |  |
> | DataAction | 微軟.認知服務/帳戶/內容管理員/團隊/工作/行動 | 將為此終結點上發佈的影像內容返回作業 ID。  |
> | DataAction | 微軟.認知服務/帳戶/內容管理員/團隊/評論/行動 | 創建的評論將顯示給團隊中的審閱者。 當審閱者完成審核時,審核結果將在指定的呼叫返回端點上發佈(即 HTTP POST)。 |
> |  | **帳戶/內容管理員/團隊/工作** |  |
> | DataAction | 微軟.認知服務/帳戶/內容管理員/團隊/工作/閱讀 | 獲取作業 ID 的工作詳細資訊。 |
> |  | **帳戶/內容管理員/團隊/評論** |  |
> | DataAction | 微軟.認知服務/帳戶/內容管理員/團隊/評論/閱讀 | 返回已通過的審核ID的審核詳細資訊。 |
> | DataAction | 微軟.認知服務/帳戶/內容管理員/團隊/評論/發佈/行動 | 視頻評論最初創建狀態為未發佈狀態,這意味著團隊中的審閱者還不能進行審閱。 |
> | DataAction | 微軟.認知服務/帳戶/內容管理員/團隊/評論/成績單/行動 | 此 API 將文本檔(視頻中所有單詞的文本版本)添加到視頻審閱中。 該檔應為有效的 WebVTT 格式。 |
> | DataAction | 微軟.認知服務/帳戶/內容管理員/團隊/評論/成績單/行動 | 此 API 添加用於影片審閱的文本螢幕文字結果檔。 文本螢幕文字結果檔是螢幕文字 API 的結果。 為了生成文本螢幕文本結果檔,必須使用螢幕文本 API 篩選腳本檔以進行褻瀆。 |
> |  | **帳號/內容管理員/團隊/評論/存取鍵** |  |
> | DataAction | 微軟.認知服務/帳戶/內容管理員/團隊/評論/訪問鍵/閱讀 | 獲取團隊的審閱內容訪問密鑰。 |
> |  | **帳戶/內容管理員/團隊/評論/框架** |  |
> | DataAction | 微軟.認知服務/帳戶/內容管理員/團隊/評論/框架/寫入 | 使用此方法可添加視頻審閱的幀。 |
> | DataAction | 微軟.認知服務/帳戶/內容管理員/團隊/評論/框架/閱讀 | *未定義* |
> |  | **帳號/內容管理員/團隊/設定/範本** |  |
> | DataAction | 微軟.認知服務/帳戶/內容管理員/團隊/設置/範本/寫入 | 建立或更新指定的樣本 |
> | DataAction | 微軟.認知服務/帳戶/內容管理員/團隊/設置/範本/刪除 | 移除團隊中的樣本 |
> | DataAction | 微軟.認知服務/帳戶/內容管理員/團隊/設置/範本/閱讀 | 返回此團隊中預配的審閱範本陣列。 |
> |  | **帳戶/內容管理員/團隊/工作流** |  |
> | DataAction | 微軟.認知服務/帳戶/內容管理員/團隊/工作流/寫入 | 創建新工作流或更新現有工作流。 |
> | DataAction | 微軟.認知服務/帳戶/內容管理員/團隊/工作流/閱讀 | 取得團隊中特定工作流的詳細資訊,取得團隊的所有可用工作流* |
> |  | **帳戶/內容管理員/術語清單** |  |
> | DataAction | 微軟.認知服務/帳戶/內容管理員/術語清單/批量更新/操作 | 字語清單 ─ 大量更新 |
> | DataAction | 微軟.認知服務/帳戶/內容管理員/術語清單/刪除 | 字語清單 - 移除 |
> | DataAction | 微軟.認知服務/帳戶/內容管理員/術語清單/閱讀 | 字列清單 ─抓取所有 - 字詞清單 - 取得詳細資訊 |
> | DataAction | 微軟.認知服務/帳戶/內容管理員/術語清單/刷新索引/操作 | 字語清單 - 刷新搜尋索引 |
> | DataAction | 微軟.認知服務/帳戶/內容管理員/術語清單/寫入 | 字語清單 ─更新詳細資訊 |
> |  | **帳戶/內容管理員/術語表/術語** |  |
> | DataAction | 微軟.認知服務/帳戶/內容管理員/術語清單/術語/寫入 | 字語 - 新增期限 |
> | DataAction | 微軟.認知服務/帳戶/內容管理員/術語清單/術語/刪除 | 期限 - 移除 - 字語 - 刪除所有條款 |
> | DataAction | 微軟.認知服務/帳戶/內容管理員/術語清單/術語/閱讀 | 字語 - 取得所有條款 |
> |  | **帳戶/自訂視覺.預測** |  |
> | DataAction | 微軟.認知服務/帳戶/自定義視覺.預測/專案/行動 | 建立專案。 |
> | DataAction | 微軟.認知服務/帳戶/自定義視覺.預測/用戶/行動 | *未定義* |
> | DataAction | 微軟.認知服務/帳戶/自定義視覺.預測/配額/行動 | *未定義* |
> |  | **帳戶/自定義視覺.預測/分類/反覆運算** |  |
> | DataAction | 微軟.認知服務/帳戶/自定義視覺.預測/分類/反覆運算/圖像/操作 | 對圖像進行分類並保存結果。 |
> | DataAction | 微軟.認知服務/帳戶/自定義視覺.預測/分類/反覆運算/url/操作 | 對影像 URL 進行分類並保存結果。 |
> |  | **帳號/自訂視覺.預測/分類/反覆運算/影像** |  |
> | DataAction | 微軟.認知服務/帳戶/自定義視覺.預測/分類/反覆運算/圖像/無店/操作 | 在不保存結果的情況下對圖像進行分類。 |
> |  | **帳號/自訂視覺.預測/分類/反覆運算/url** |  |
> | DataAction | 微軟.認知服務/帳戶/自定義視覺.預測/分類/反覆運算/url/無庫/操作 | 在不保存結果的情況下對圖像 URL 進行分類。 |
> |  | **帳戶/自定義視覺.預測/檢測/反覆運算** |  |
> | DataAction | 微軟.認知服務/帳戶/自定義視覺.預測/檢測/反覆運算/圖像/操作 | 檢測圖像中的物件並保存結果。 |
> | DataAction | 微軟.認知服務/帳戶/自定義視覺.預測/檢測/反覆運算/url/操作 | 檢測影像 URL 中的物件並保存結果。 |
> |  | **帳號/自訂視覺.預測/檢測/反覆運算/圖像** |  |
> | DataAction | 微軟.認知服務/帳戶/自定義視覺.預測/檢測/反覆運算/圖像/無商店/操作 | 檢測圖像中的物件,而不保存結果。 |
> |  | **帳號/自訂視覺.預測/檢測/反覆運算/url** |  |
> | DataAction | 微軟.認知服務/帳戶/自定義視覺.預測/檢測/反覆運算/url/無存儲/操作 | 檢測圖像 URL 中的物件,而不保存結果。 |
> |  | **帳號/自訂視覺.預測/域** |  |
> | DataAction | 微軟.認知服務/帳戶/自定義視覺.預測/域/閱讀 | 獲取有關特定域的資訊。 取得可用網域清單。 |
> |  | **帳號/自訂視覺.預測/標籤建議** |  |
> | DataAction | 微軟.認知服務/帳戶/自定義視覺.預測/標籤建議/設置/操作 | 設置標籤建議的池大小。 |
> |  | **帳號/自訂視覺.預測/標籤建議/設置** |  |
> | DataAction | 微軟.認知服務/帳戶/自定義視覺.預測/標籤建議/設置/閱讀 | 獲取此項目的標籤方案的池大小。 |
> |  | **帳號/自訂視覺.預測/專案** |  |
> | DataAction | 微軟.認知服務/帳戶/自定義視覺.預測/專案/遷移/操作 | *未定義* |
> |  | **帳號/自訂視覺.預測/專案** |  |
> | DataAction | 微軟.認知服務/帳戶/自定義視覺.預測/專案/圖像/行動 | 此 API 將接受正文內容為多部分/窗體數據和應用程式/八進位流。 使用多部份時 |
> | DataAction | 微軟.認知服務/帳戶/自定義視覺.預測/項目/標籤/操作 | 為專案創建標記。 |
> | DataAction | 微軟.認知服務/帳戶/自定義視覺.預測/專案/刪除 | 刪除特定專案。 |
> | DataAction | 微軟.認知服務/帳戶/自定義視覺.預測/專案/閱讀 | 獲取特定專案。 獲取您的專案。 |
> | DataAction | 微軟.認知服務/帳戶/自定義視覺.預測/專案/培訓/行動 | 用於培訓的佇列專案。 |
> | DataAction | 微軟.認知服務/帳戶/自定義視覺.預測/專案/寫入 | 更新特定專案。 |
> | DataAction | 微軟.認知服務/帳戶/自定義視覺.預測/專案/導入/操作 | 導入專案。 |
> |  | **帳號/自訂視覺.預測/專案/匯出** |  |
> | DataAction | 微軟.認知服務/帳戶/自定義視覺.預測/專案/匯出/讀取 | 匯出專案。 |
> |  | **帳號/自訂視覺.預測/專案/影像** |  |
> | DataAction | 微軟.認知服務/帳戶/自定義視覺.預測/專案/圖像/區域/行動 | 此 API 接受一批圖像區域和可選標記,以便使用區域資訊更新現有圖像。 |
> | DataAction | 微軟.認知服務/帳戶/自定義視覺.預測/專案/圖像/檔/操作 | 此 API 接受一批檔(可選標記)來創建映射。 其限制為 64 個影像和 20 個標記。 |
> | DataAction | 微軟.認知服務/帳戶/自定義視覺.預測/專案/圖像/預測/行動 | 此 API 從指定的預測圖像創建一批圖像。 其限制為 64 個影像和 20 個標記。 |
> | DataAction | 微軟.認知服務/帳戶/自定義視覺.預測/專案/圖像/url/行動 | 此 API 接受一批 URL 和可選標記來創建映射。 其限制為 64 個影像和 20 個標記。 |
> | DataAction | 微軟.認知服務/帳戶/自定義視覺.預測/專案/圖像/標籤/操作 | 將一組圖像與一組標記相關聯。 |
> | DataAction | 微軟.認知服務/帳戶/自定義視覺.預測/專案/圖像/刪除 | 從訓練圖像集中刪除圖像。 |
> | DataAction | 微軟.認知服務/帳戶/自定義視覺.預測/專案/圖像/區域建議/行動 | 此 API 將獲得圖像的區域建議以及該區域的置信。 如果未找到任何建議,它將返回一個空陣組。 |
> | DataAction | 微軟.認知服務/帳戶/自定義視覺.預測/專案/圖像/建議/操作 | 此 API 將獲取由建議標記 Id 篩選的未標記圖像。 如果未找到圖像,它將返回一個空陣組。 |
> |  | **帳號/自訂視覺.預測/專案/影像/id** |  |
> | DataAction | 微軟.認知服務/帳戶/自定義視覺.預測/專案/圖像/id/讀取 | 此 API 將返回指定標記和可選反覆運算的一組圖像。 如果未指定反覆運算, |
> |  | **帳號/自訂視覺.預測/專案/影像/區域** |  |
> | DataAction | 微軟.認知服務/帳戶/自定義視覺.預測/專案/圖像/區域/刪除 | 刪除一組圖像區域。 |
> |  | **帳號/自訂視覺.預測/專案/影像/建議** |  |
> | DataAction | 微軟.認知服務/帳戶/自定義視覺.預測/專案/圖像/建議/計數/操作 | 此 API 採用 tagIds 來獲取給定閾值每個建議標記的未標記圖像計數。 |
> |  | **帳號/自訂視覺.預測/專案/影像/標記** |  |
> | DataAction | 微軟.認知服務/帳戶/自定義視覺.預測/專案/圖像/標記/閱讀 | 此 API 支援批次處理和範圍選擇。 默認情況下,它只會返回前 50 個匹配圖像的圖像。 |
> |  | **帳號/自訂視覺.預測/專案/影像/標記/計數** |  |
> | DataAction | 微軟.認知服務/帳戶/自定義視覺.預測/專案/圖像/標記/計數/閱讀 | 篩選位於和/或關係上。 例如,如果提供的標記 ID 用於「狗」和 |
> |  | **帳號/自訂視覺.預測/專案/影像/標籤** |  |
> | DataAction | 微軟.認知服務/帳戶/自定義視覺.預測/專案/圖像/標籤/刪除 | 從一組圖像中刪除一組標記。 |
> |  | **帳號/自訂視覺.預測/專案/影像/未標記** |  |
> | DataAction | 微軟.認知服務/帳戶/自定義視覺.預測/專案/圖像/未標記/讀取 | 此 API 支援批次處理和範圍選擇。 默認情況下,它只會返回前 50 個匹配圖像的圖像。 |
> |  | **帳號/自訂視覺.預測/專案/影像/未標記/計數** |  |
> | DataAction | 微軟.認知服務/帳戶/自定義視覺.預測/專案/圖像/未標記/計數/讀取 | 此 API 返回沒有給定項目標記的圖像,並選擇性地返回反覆運算。 如果未指定反覆運算, |
> |  | **帳號/自訂視覺.預測/專案/反覆運算** |  |
> | DataAction | 微軟.認知服務/帳戶/自定義視覺.預測/專案/反覆運算/刪除 | 刪除專案的特定反覆運算。 |
> | DataAction | 微軟.認知服務/帳戶/自定義視覺.預測/專案/反覆運算/匯出/操作 | 匯出經過訓練的反覆運算。 |
> | DataAction | 微軟.認知服務/帳戶/自定義視覺.預測/專案/反覆運算/閱讀 | 獲取特定反覆運算。 獲取專案的反覆運算。 |
> | DataAction | 微軟.認知服務/帳戶/自定義視覺.預測/專案/反覆運算/發佈/操作 | 發佈特定反覆運算。 |
> | DataAction | 微軟.認知服務/帳戶/自定義視覺.預測/專案/反覆運算/寫入 | 更新特定反覆運算。 |
> |  | **帳號/自定義視覺.預測/專案/反覆運算/匯出** |  |
> | DataAction | 微軟.認知服務/帳戶/自定義視覺.預測/專案/反覆運算/匯出/讀取 | 獲取特定反覆運算的匯出清單。 |
> |  | **帳號/自訂視覺.預測/專案/反覆運算/性能** |  |
> | DataAction | 微軟.認知服務/帳戶/自定義視覺.預測/專案/反覆運算/性能/閱讀 | 獲取有關反覆運算的詳細性能資訊。 |
> |  | **帳號/自訂視覺.預測/專案/反覆運算/性能/影像** |  |
> | DataAction | 微軟.認知服務/帳戶/自定義視覺.預測/專案/反覆運算/性能/圖像/閱讀 | 此 API 支援批次處理和範圍選擇。 默認情況下,它只會返回前 50 個匹配圖像的圖像。 |
> |  | **帳號/自訂視覺.預測/專案/反覆運算/性能/影像/計數** |  |
> | DataAction | 微軟.認知服務/帳戶/自定義視覺.預測/專案/反覆運算/性能/圖像/計數/讀取 | 篩選位於和/或關係上。 例如,如果提供的標記 ID 用於「狗」和 |
> |  | **帳戶/自定義視覺.預測/專案/反覆運算/發佈** |  |
> | DataAction | 微軟.認知服務/帳戶/自定義視覺.預測/專案/反覆運算/發佈/刪除 | 取消發佈特定反覆運算。 |
> |  | **帳號/自訂視覺.預測/項目/預測** |  |
> | DataAction | 微軟.認知服務/帳戶/自定義視覺.預測/項目/預測/刪除 | 刪除一組預測圖像及其關聯的預測結果。 |
> | DataAction | 微軟.認知服務/帳戶/自定義視覺.預測/項目/預測/查詢/操作 | 獲取發送到預測終結點的圖像。 |
> |  | **帳號/自訂視覺.預測/專案/快速測試** |  |
> | DataAction | 微軟.認知服務/帳戶/自定義視覺.預測/專案/快速測試/圖像/操作 | 快速測試圖像。 |
> | DataAction | 微軟.認知服務/帳戶/自定義視覺.預測/專案/快速測試/url/行動 | 快速測試圖像 URL。 |
> |  | **帳號/自訂視覺.預測/項目/標籤** |  |
> | DataAction | 微軟.認知服務/帳戶/自定義視覺.預測/項目/標籤/刪除 | 從項目中刪除標記。 |
> | DataAction | 微軟.認知服務/帳戶/自定義視覺.預測/項目/標籤/讀取 | 獲取有關特定標記的資訊。 獲取給定專案和反覆運算的標記。 |
> | DataAction | 微軟.認知服務/帳戶/自定義視覺.預測/項目/標籤/寫入 | 更新標記。 |
> |  | **帳號/自訂視覺.預測/項目/標籤和區域** |  |
> | DataAction | 微軟.認知服務/帳戶/自定義視覺.預測/項目/標籤和區域/建議/行動 | 此 API 將獲得陣列/批未標記圖像的建議標記和區域以及標記的置信度。 如果未找到標記,它將返回空陣組。 |
> |  | **帳戶/自定義視覺.預測/專案/培訓** |  |
> | DataAction | 微軟.認知服務/帳戶/自定義視覺.預測/專案/培訓/高級/行動 | 使用管道配置和培訓類型進行訓練的佇列專案。 |
> |  | **帳戶/自定義視覺.預測/配額** |  |
> | DataAction | 微軟.認知服務/帳戶/自定義視覺.預測/配額/刪除 | *未定義* |
> |  | **帳戶/自定義視覺.預測/配額/刷新** |  |
> | DataAction | 微軟.認知服務/帳戶/自定義視覺.預測/配額/刷新/寫入 | *未定義* |
> |  | **帳號/自訂視覺.預測/使用/預測/使用者** |  |
> | DataAction | 微軟.認知服務/帳戶/自定義視覺.預測/使用/預測/使用者/閱讀 | 取得牛津使用者預測資源的使用方式 |
> |  | **帳戶/自定義視覺.預測/使用/培訓/資源/層** |  |
> | DataAction | 微軟.認知服務/帳戶/自定義視覺.預測/使用/培訓/資源/層/閱讀 | 取得 Azure 使用者訓練資源的使用方式 |
> |  | **帳戶/自定義視覺.預測/使用/培訓/使用者** |  |
> | DataAction | 微軟.認知服務/帳戶/自定義視覺.預測/使用/培訓/用戶/閱讀 | 取得牛津使用者培訓資源的用法 |
> |  | **帳號/自訂視覺.預測/使用者** |  |
> | DataAction | 微軟.認知服務/帳戶/自定義視覺.預測/使用者/刪除 | *未定義* |
> |  | **帳號/自訂視覺.預測/用戶/狀態** |  |
> | DataAction | 微軟.認知服務/帳戶/自定義視覺.預測/用戶/狀態/寫入 | 更新使用者狀態 |
> |  | **帳號/自訂視覺.預測/用戶/層** |  |
> | DataAction | 微軟.認知服務/帳戶/自定義視覺.預測/使用者/層/寫入 | *未定義* |
> |  | **帳號/自訂視覺.預測/使用者** |  |
> | DataAction | 微軟.認知服務/帳戶/自定義視覺.預測/用戶/閱讀 | *未定義* |
> |  | **帳戶/自定義視覺.預測/白名單** |  |
> | DataAction | 微軟.認知服務/帳戶/自定義視覺.預測/白名單/刪除 | 移除有特定功能的白名單使用者 |
> | DataAction | 微軟.認知服務/帳戶/自定義視覺.預測/白名單/閱讀 | 取得有特定功能的白名單使用者清單 |
> | DataAction | 微軟.認知服務/帳戶/自定義視覺.預測/白名單/寫入 | 更新或建立有特定功能的白名單中的使用者 |
> |  | **帳號/實體搜尋** |  |
> | DataAction | 微軟.認知服務/帳戶/實體搜索/搜索/操作 | 獲取實體並放置給定查詢的結果。 |
> |  | **帳戶/面** |  |
> | DataAction | 微軟.認知服務/帳戶/人臉/檢測/操作 | 檢測圖像中的人臉、返回面矩形,以及可選使用面 ID、地標和屬性。 |
> | DataAction | 微軟.認知服務/帳戶/臉/查找類似/操作 | 給定查詢面的 faceId,以從 faceId 陣列、面清單或大型麵清單中搜索相似外觀的面。 faceId |
> | DataAction | 微軟.認知服務/帳戶/臉/組/行動 | 根據人臉相似性將候選面劃分為多個組。 |
> | DataAction | 微軟.認知服務/帳戶/臉/識別/行動 | 1 對多標識,從人員組或大型人員組查找特定查詢人面的最近匹配項。 |
> | DataAction | 微軟.認知服務/帳戶/臉/驗證/操作 | 驗證兩張臉是否屬於同一個人,或者一張臉是否屬於一個人。 |
> |  | **帳號/面/分麵清單** |  |
> | DataAction | 微軟.認知服務/帳戶/臉/臉列表/寫 | 使用使用者指定的麵列表、名稱和可選用戶數據創建空麵清單。 最多允許 64 個麵清單 更新人臉清單的資訊,包括姓名和用戶數據。 |
> | DataAction | 微軟.認知服務/帳戶/臉/麵列表/刪除 | 刪除指定的麵清單。 人臉清單中的相關人臉圖像也將被刪除。 |
> | DataAction | 微軟.認知服務/帳戶/臉/臉列表/閱讀 | 檢索人臉清單的人臉清單Id、名稱、用戶數據和人臉清單中的人臉清單。 列出麵清單的麵清單Id、名稱和用戶數據。 |
> |  | **帳號/面/麵清單/已保留面** |  |
> | DataAction | 微軟.認知服務/帳戶/臉/臉列表/持久面/寫入 | 將面添加到指定的麵清單中,最多 1,000 張面。 |
> | DataAction | 微軟.認知服務/帳戶/臉/麵清單/持久面/刪除 | 通過指定的面清單 Id 和滲透面 Id 從麵清單中刪除面。 相關人臉圖像也將被刪除。 |
> |  | **帳號/面/大面清單** |  |
> | DataAction | 微軟.認知服務/帳戶/臉/大面表/寫 | 使用使用者指定的大 FaceListId、名稱和可選用戶數據創建一個空的大面清單。 更新大型麵清單的資訊,包括名稱和用戶數據。 |
> | DataAction | 微軟.認知服務/帳戶/臉/大面清單/刪除 | 刪除指定的大面清單。 大面清單中的相關人臉圖像也將被刪除。 |
> | DataAction | 微軟.認知服務/帳戶/臉/大面列表/閱讀 | 檢索大型麵清單的大 FaceListId、名稱和用戶數據。 列出大型麵清單的大 FaceListId、名稱和用戶數據的資訊。 |
> | DataAction | 微軟.認知服務/帳戶/臉/大面清單/訓練/行動 | 提交大面清單訓練任務。 培訓是只有經過訓練的大臉表才能使用的關鍵步驟。 |
> |  | **帳戶/面/大面清單/持久面** |  |
> | DataAction | 微軟.認知服務/帳戶/臉/大面清單/持久面/寫入 | 將面添加到指定的大面清單中,最多 1,000,000 張面。 通過保留的 FaceId 更新大面清單中指定面的用戶數據欄位。 |
> | DataAction | 微軟.認知服務/帳戶/臉/大面清單/持久面/刪除 | 通過指定的大 FaceListId 和滲透面 Id 從大面清單中刪除面。 相關人臉圖像也將被刪除。 |
> | DataAction | 微軟.認知服務/帳戶/臉/大面清單/持久面/閱讀 | 通過大 FaceListId 和持久面 Id 在大型麵列表中檢索持久面。 列表面的持久存在 FaceId 和用戶數據位於指定的大面清單中。 |
> |  | **帳戶/臉/大面列表/培訓** |  |
> | DataAction | 微軟.認知服務/帳戶/臉/大面表/培訓/閱讀 | 檢查大面清單訓練狀態已完成或仍在進行中。 大型FaceList培訓是非同步操作 |
> |  | **帳戶/臉/大人組** |  |
> | DataAction | 微軟.認知服務/帳戶/臉/大人組/寫 | 使用使用者指定的大人組 Id、名稱和可選用戶數據創建新的大型企業組。 更新現有大型人員組的名稱和用戶數據。 如果屬性不在請求正文中,則屬性保持不變。 |
> | DataAction | 微軟.認知服務/帳戶/臉/大人組/刪除 | 刪除具有指定人員 GroupId 的現有大型人員組。 此大型人員組中的持久數據將被刪除。 |
> | DataAction | 微軟.認知服務/帳戶/臉/大人組/閱讀 | 檢索大型人員組的資訊,包括其名稱和用戶數據。 此 API 返回大型人員組資訊清單所有現有大型人員組的大型 PesonGroupId、名稱和用戶數據。 |
> | DataAction | 微軟.認知服務/帳戶/臉/大人組/火車/行動 | 提交大型人員組培訓任務。 培訓是只有經過培訓的大型企業群體才能使用的關鍵步驟。 |
> | DataAction | 微軟.認知服務/帳戶/臉/大人組/人/行動 | 在指定的大型人員組中創建新人員。 要向此人添加面部,請致電 |
> |  | **帳號/臉/大人組/人** |  |
> | DataAction | 微軟.認知服務/帳戶/臉/大人組/人/刪除 | 從大型人員組中刪除現有人員。 所有存儲的人員數據以及人員條目中的人臉圖像都將被刪除。 |
> | DataAction | 微軟.認知服務/帳戶/臉/大人組/人/閱讀 | 檢索某人的姓名和用戶數據,以及表示註冊人面部圖像的持久化人臉 Id。 列出指定大型人員組中的所有人資訊,包括人員 Id、姓名、用戶數據和持久化 FaceId。 |
> | DataAction | 微軟.認知服務/帳戶/臉/大人組/人/寫 | 更新人員的姓名或用戶數據。 |
> |  | **帳戶/臉/大人組/人/堅持面** |  |
> | DataAction | 微軟.認知服務/帳戶/臉/大人組/人/堅持面孔/寫 | 將人臉圖像添加到大型人員組中,以便進行人臉識別或驗證。 要處理"更新"圖像,使用者保留人臉的用戶數據欄位。 |
> | DataAction | 微軟.認知服務/帳戶/臉/大人組/人/堅持臉/刪除 | 從大型人員組中的人員中刪除人臉。 與此人臉條目相關的人臉數據和圖像也將被刪除。 |
> | DataAction | 微軟.認知服務/帳戶/臉/大人組/人/堅持面孔/閱讀 | 檢索人臉資訊。 堅持者面由其大型 PersonGroupId、人 Id 和持久化 FaceId 指定。 |
> |  | **帳號/臉/大人組/培訓** |  |
> | DataAction | 微軟.認知服務/帳戶/臉/大人組/培訓/閱讀 | 檢查大型人員組培訓狀態已完成或仍在進行中。 大型PersonGroup培訓是非同步操作 |
> |  | **帳戶/臉/人組** |  |
> | DataAction | 微軟.認知服務/帳戶/臉/人組/寫入 | 使用指定人員 GroupId、名稱和使用者提供的使用者數據創建新的人員組。 更新現有人員組的名稱和用戶數據。 如果屬性不在請求正文中,則屬性保持不變。 |
> | DataAction | 微軟.認知服務/帳戶/臉/人組/刪除 | 刪除具有指定人員 GroupId 的現有人員組。 此人員組中的持久數據將被刪除。 |
> | DataAction | 微軟.認知服務/帳戶/臉/人組/閱讀 | 檢索人員組名稱和用戶數據。 要取得此人群組下的人員資訊,請使用列表人員組的 pesonGroupId、名稱和用戶數據。 |
> | DataAction | 微軟.認知服務/帳戶/臉/人組/火車/行動 | 提交人員組培訓任務。 培訓是只有經過培訓的人員群體才能使用的關鍵步驟。 |
> | DataAction | 微軟.認知服務/帳戶/臉/人組/人/行動 | 在指定人員組中創建新人員。 要向此人添加面部,請致電 |
> |  | **帳號/臉/人組/人** |  |
> | DataAction | 微軟.認知服務/帳戶/臉/人組/人/刪除 | 從人員組中刪除現有人員。 所有存儲的人員數據以及人員條目中的人臉圖像都將被刪除。 |
> | DataAction | 微軟.認知服務/帳戶/臉/人組/人/閱讀 | 檢索某人的姓名和用戶數據,以及表示註冊人面部圖像的持久化人臉 Id。 列出指定人員組中的所有人資訊,包括已登記的人員 Id、姓名、用戶數據和已保留的 FaceId。 |
> | DataAction | 微軟.認知服務/帳戶/臉/人組/人/寫 | 更新人員的姓名或用戶數據。 |
> |  | **帳戶/臉/人組/人/已保留的面孔** |  |
> | DataAction | 微軟.認知服務/帳戶/臉/人組/人/堅持面孔/寫 | 將人臉圖像添加到人員組中,以便進行人臉識別或驗證。 要處理多個更新的圖像,使用者用戶數據欄位是使用者保留的。 |
> | DataAction | 微軟.認知服務/帳戶/臉/人組/人/堅持面/刪除 | 從人員組中的人員中刪除人臉。 與此人臉條目相關的人臉數據和圖像也將被刪除。 |
> | DataAction | 微軟.認知服務/帳戶/臉/人組/人/堅持面孔/閱讀 | 檢索人臉資訊。 堅持的人臉由其人組Id、人 Id 和持久化 FaceId 指定。 |
> |  | **帳號/臉/人組/培訓** |  |
> | DataAction | 微軟.認知服務/帳戶/臉/人組/培訓/閱讀 | 檢查人員組培訓狀態已完成或仍在進行中。 人員組培訓是觸發的非同步操作 |
> |  | **帳戶/臉/快照** |  |
> | DataAction | 微軟.認知服務/帳戶/臉/快照/拍攝/行動 | 為物件拍攝快照。 |
> | DataAction | 微軟.認知服務/帳戶/臉/快照/閱讀 | 獲取快照操作的狀態。 |
> | DataAction | 微軟.認知服務/帳戶/臉/快照/應用/操作 | 應用快照,提供使用者指定的對象 ID。 |
> | DataAction | 微軟.認知服務/帳戶/臉/快照/刪除 | 刪除快照。 |
> | DataAction | 微軟.認知服務/帳戶/臉/快照/寫入 | 更新快照的屬性。 |
> |  | **帳戶/人臉/快照** |  |
> | DataAction | 微軟.認知服務/帳戶/臉/快照/閱讀 | 列出使用者所有可訪問的快照資訊。 |
> |  | **帳號/表單識別器/自訂** |  |
> | DataAction | 微軟.認知服務/帳戶/表單識別器/自定義/培訓/操作 | 創建並訓練自定義模型。<br>訓練請求必須包括一個源參數,該參數是外部可訪問的 Azure 儲存 Blob 容器 Uri(最好是共享存取簽名 Uri)或本地裝載驅動器中資料資料夾的有效路徑。<br>指定本地路徑時,它們必須遵循 Linux/Unix 路徑格式,並且是植根於輸入載入設定的絕對路徑 |
> |  | **帳號/表單識別器/自訂/模型** |  |
> | DataAction | 微軟.認知服務/帳戶/表單識別器/自定義/模型/分析/操作 | 從給定文檔中提取鍵值對。 輸入文件必須為支援的內容類型之一 - "應用程式/pdf"、"圖像/jpeg"或"圖像/png"。 成功的回應會以 JSON 的形式傳回。 |
> | DataAction | 微軟.認知服務/帳戶/表單識別器/自定義/模型/刪除 | 刪除模型專案。 |
> | DataAction | 微軟.認知服務/帳戶/表單識別器/自定義/模型/讀取 | 獲取有關模型的資訊。 獲取有關所有經過培訓的自定義模型的資訊* |
> |  | **帳號/表單識別器/自訂/模型/金鑰** |  |
> | DataAction | 微軟.認知服務/帳戶/表單識別器/自定義/型號/密鑰/讀取 | 檢索模型的鍵。 |
> |  | **帳號/表單識別器/預建構/接收** |  |
> | DataAction | 微軟.認知服務/帳戶/表單識別器/預構建/接收/非同步批次處理分析/操作 | 從給定的接收文檔中提取欄位文本和語義值。 輸入影像文件必須是支援的內容類型之一 - JPEG、PNG、BMP、PDF 或 TIFF。 成功回應是包含名為"操作-位置"的欄位的 JSON,其中包含獲取接收結果操作的 URL 以非同步檢索結果。 |
> | DataAction | 微軟.認知服務/帳戶/表單識別器/預構建/接收/操作/操作 | 查詢狀態並檢索分析收貨操作的結果。 此介面的網址 可以從分析收據回應中的"操作位置"標頭獲取。 |
> |  | **帳號/圖片搜尋** |  |
> | DataAction | 微軟.認知服務/帳戶/圖像搜索/詳細資訊/操作 | 返回有關圖像的見解,例如包含圖像的網頁。 |
> | DataAction | 微軟.認知服務/帳戶/圖像搜索/搜索/操作 | 獲取給定查詢的相關圖像。 |
> | DataAction | 微軟.認知服務/帳戶/圖像搜索/趨勢/操作 | 獲取當前趨勢圖像。 |
> |  | **帳號/沉浸式閱讀器** |  |
> | DataAction | 微軟.認知服務/帳戶/ImmersiveReader/獲取讀者/操作內容模型 | 建立沉浸式讀取器工作階段 |
> |  | **帳號/墨蹟識別器** |  |
> | DataAction | 微軟.認知服務/帳戶/墨水識別器/識別/操作 | 給定一組描邊數據會分析內容並生成包含已識別文本的可識別實體的清單。 |
> |  | **帳號/LUIS** |  |
> | DataAction | 微軟.認知服務/帳戶/LUIS/預測/行動 | 獲取給定查詢的已發佈終結點預測。 |
> |  | **帳號/新聞搜尋** |  |
> | DataAction | 微軟.認知服務/帳戶/新聞搜索/類別搜索/操作 | 返回提供的類別的新聞。 |
> | DataAction | 微軟.認知服務/帳戶/新聞搜索/搜索/操作 | 獲取與給定查詢相關的新聞文章。 |
> | DataAction | 微軟.認知服務/帳戶/新聞搜索/熱門話題/行動 | 獲取必應識別的趨勢主題。 這些主題與必應主頁底部的橫幅中顯示的主題相同。 |
> |  | **帳號/QnAMaker** |  |
> | DataAction | 微軟.認知服務/帳戶/QnAMaker/根/行動 | QnA Maker |
> |  | **帳號/QnAMaker/改變** |  |
> | DataAction | 微軟.認知服務/帳戶/QnAMaker/改變/閱讀 | 從運行時下載更改。 |
> | DataAction | 微軟.認知服務/帳戶/QnAMaker/改變/寫入 | 替換更改數據。 |
> |  | **帳號/QnAMaker/終結點鍵** |  |
> | DataAction | 微軟.認知服務/帳戶/QnAMaker/終結點鍵/讀取 | 取得終結點的終結點鍵 |
> | DataAction | 微軟.認知服務/帳戶/QnAMaker/終結點鍵/刷新鍵/操作 | 重新生成終結點金鑰。 |
> |  | **帳號/QnAMaker/端點設定** |  |
> | DataAction | 微軟.認知服務/帳戶/QnAMaker/端點設置/讀取 | 取得終結點的終結點設定 |
> | DataAction | 微軟.認知服務/帳戶/QnAMaker/端點設置/寫入 | 更新終結點的終結點滲出。 |
> |  | **帳號/QnAMaker/知識庫** |  |
> | DataAction | 微軟.認知服務/帳戶/QnAMaker/知識庫/發佈/行動 | 將知識庫測試索引中的所有更改發佈到其 prod 索引。 |
> | DataAction | 微軟.認知服務/帳戶/QnAMaker/知識庫/刪除 | 刪除知識庫及其所有數據。 |
> | DataAction | 微軟.認知服務/帳戶/QnAMaker/知識庫/閱讀 | 獲取知識庫清單或特定知識庫的詳細資訊。 |
> | DataAction | 微軟.認知服務/帳戶/QnAMaker/知識庫/寫入 | 非同步操作以修改知識庫或替換知識庫內容。 |
> | DataAction | 微軟.認知服務/帳戶/QnAMaker/知識庫/生成答案/行動 | 生成應答調用以查詢知識庫。 |
> | DataAction | 微軟.認知服務/帳戶/QnAMaker/知識庫/培訓/行動 | 訓練呼叫以向知識庫添加建議。 |
> |  | **帳號/QnAMaker/知識庫/創建** |  |
> | DataAction | 微軟.認知服務/帳戶/QnAMaker/知識庫/創建/寫入 | 非同步操作以創建新的知識庫。 |
> |  | **帳號/QnAMaker/知識庫/下載** |  |
> | DataAction | 微軟.認知服務/帳戶/QnAMaker/知識庫/下載/閱讀 | 下載知識庫。 |
> |  | **帳號/QnAMaker/運營** |  |
> | DataAction | 微軟.認知服務/帳戶/QnAMaker/操作/讀取 | 取得特定長時間執行作業的詳細資料。 |
> |  | **帳號/拼字檢查** |  |
> | DataAction | 微軟.認知服務/帳戶/拼寫檢查/拼寫檢查/操作 | 通過 GET 或 POST 獲取拼寫檢查查詢的結果。 |
> |  | **帳號/文字分析** |  |
> | DataAction | 微軟.認知服務/帳戶/文本分析/語言/行動 | API 傳回檢測到的語言和介於 0 和 1 之間的數值分數。 接近 1 的分數表示 100% 確定已識別的語言為真實。 總共支援 120 種語言。 |
> | DataAction | 微軟.認知服務/帳戶/文本分析/實體/行動 | API 傳回給定文件中已知實體和一般命名\"實體\"(\"\"人員\"、\"位置、 組織等)的清單。 |
> | DataAction | 微軟.認知服務/帳戶/文本分析/關鍵詞/操作 | API 會傳回輸入文字中代表說話重點的字串清單。 |
> | DataAction | 微軟.認知服務/帳戶/文本分析/情緒/行動 | 此 API 會傳回 0 到 1 之間的數值分數。<br>接近 1 的分數表示正面的情感，而接近 0 的分數則表示負面的情感。<br>分數 0.5 表示缺乏情緒(例如,<br>事實陳述)。 |
> |  | **帳號/視訊搜尋** |  |
> | DataAction | 微軟.認知服務/帳戶/視頻搜索/趨勢/操作 | 獲取當前熱門視頻。 |
> | DataAction | 微軟.認知服務/帳戶/視頻搜索/詳細資訊/操作 | 獲取有關視頻(如相關視頻)的見解。 |
> | DataAction | 微軟.認知服務/帳戶/視頻搜索/搜索/操作 | 獲取與給定查詢相關的視頻。 |
> |  | **帳號/視覺搜尋** |  |
> | DataAction | 微軟.認知服務/帳戶/視覺搜索/搜索/操作 | 傳回與提供的影像相關的標籤清單 |
> |  | **帳號/網路搜尋** |  |
> | DataAction | 微軟.認知服務/帳戶/網路搜索/搜索/操作 | 獲取給定查詢的網路、圖像、新聞、&视频结果。 |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

Azure 服務:核心

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | 微軟.商業/註冊/行動 | 註冊 Microsoft 商務使用集合訂閱 |
> | 動作 | 微軟.商業/取消註冊/行動 | 取消註冊 Microsoft 商務使用集合訂閱 |
> |  | **RateCard** |  |
> | 動作 | Microsoft.Commerce/RateCard/read | 傳回給定訂用帳戶的供應項目資料、資源/計量中繼資料和速率。 |
> |  | **使用方式彙總** |  |
> | 動作 | Microsoft.Commerce/UsageAggregates/read | 按訂閱檢索 Microsoft Azure 的消耗。 結果會包含特定時間範圍內的彙總使用量資料、訂用帳戶和資源相關資訊。 |

## <a name="microsoftcompute"></a>Microsoft.Compute

Azure 服務:[虛擬機器](../virtual-machines/index.yml),[虛擬機器縮放集](../virtual-machine-scale-sets/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.Compute/register/action | 向 Microsoft.Compute 資源提供者註冊訂用帳戶 |
> | 動作 | 微軟.計算/取消註冊/操作 | 取消註冊 Microsoft 訂閱。計算資源提供者 |
> |  | **availabilitySets** |  |
> | 動作 | Microsoft.Compute/availabilitySets/read | 取得可用性設定組的屬性 |
> | 動作 | Microsoft.Compute/availabilitySets/write | 建立新的可用性設定組，或更新現有的可用性設定組 |
> | 動作 | Microsoft.Compute/availabilitySets/delete | 刪除可用性設定組 |
> |  | **可用性設定/vmSizes** |  |
> | 動作 | Microsoft.Compute/availabilitySets/vmSizes/read | 列出在可用性設定組中建立或更新虛擬機器時可以使用的大小 |
> |  | **磁碟加密集** |  |
> | 動作 | 微軟.計算/磁碟加密集/讀取 | 取得磁碟加密集屬性 |
> | 動作 | 微軟.計算/磁碟加密集/寫入 | 建立新的磁碟加密集或更新現有磁碟加密集 |
> | 動作 | 微軟.計算/磁碟加密集/刪除 | 移除磁碟加密集 |
> |  | **磁碟** |  |
> | 動作 | Microsoft.Compute/disks/read | 取得磁碟的屬性 |
> | 動作 | Microsoft.Compute/disks/write | 建立新的磁碟，或更新現有磁碟 |
> | 動作 | Microsoft.Compute/disks/delete | 刪除磁碟 |
> | 動作 | Microsoft.Compute/disks/beginGetAccess/action | 取得磁碟用於 Blob 存取的 SAS URI |
> | 動作 | Microsoft.Compute/disks/endGetAccess/action | 撤銷磁碟的 SAS URI |
> |  | **galleries** |  |
> | 動作 | Microsoft.Compute/galleries/read | 取得資源庫的屬性 |
> | 動作 | Microsoft.Compute/galleries/write | 建立新的資源庫或更新現有的資源庫 |
> | 動作 | Microsoft.Compute/galleries/delete | 刪除資源庫 |
> |  | **畫廊/應用程式** |  |
> | 動作 | 微軟.計算/畫廊/應用程式/讀取 | 取得函式庫應用程式屬性 |
> | 動作 | 微軟.計算/畫廊/應用程式/寫入 | 建立新的函式庫應用程式或更新現有函式庫應用程式 |
> | 動作 | 微軟.計算/畫廊/應用程式/刪除 | 移除函式庫應用程式 |
> |  | **畫廊/應用程式/版本** |  |
> | 動作 | 微軟.計算/畫廊/應用程式/版本/讀取 | 取得函式庫應用程式版本的屬性 |
> | 動作 | 微軟.計算/畫廊/應用程式/版本/寫入 | 建立新的函式庫應用程式版本或更新現有函式庫應用程式版本 |
> | 動作 | 微軟.計算/畫廊/應用程式/版本/刪除 | 移除函式庫應用程式版本 |
> |  | **galleries/images** |  |
> | 動作 | Microsoft.Compute/galleries/images/read | 取得資源庫映像的屬性 |
> | 動作 | Microsoft.Compute/galleries/images/write | 建立新的資源庫映像或更新現有的資源庫映像 |
> | 動作 | Microsoft.Compute/galleries/images/delete | 刪除資源庫映像 |
> |  | **galleries/images/versions** |  |
> | 動作 | Microsoft.Compute/galleries/images/versions/read | 取得資源庫映像版本的屬性 |
> | 動作 | Microsoft.Compute/galleries/images/versions/write | 建立新的資源庫映像版本或更新現有的資源庫映像版本 |
> | 動作 | Microsoft.Compute/galleries/images/versions/delete | 刪除資源庫映像版本 |
> |  | **主機組** |  |
> | 動作 | 微軟.計算/主機組/讀取 | 取得主機組屬性 |
> | 動作 | 微軟.計算/主機組/寫入 | 建立新主機組或更新現有主機組 |
> | 動作 | 微軟.計算/主機組/刪除 | 移除主機組 |
> |  | **主機組/主機** |  |
> | 動作 | 微軟.計算/主機組/主機/讀取 | 取得主機屬性 |
> | 動作 | 微軟.計算/主機組/主機/寫入 | 建立新主機或更新現有主機 |
> | 動作 | 微軟.計算/主機組/主機/刪除 | 移除主機 |
> |  | **影像** |  |
> | 動作 | Microsoft.Compute/images/read | 取得映像的屬性 |
> | 動作 | Microsoft.Compute/images/write | 建立新的映像，或更新現有映像 |
> | 動作 | Microsoft.Compute/images/delete | 刪除映像 |
> |  | **位置/上限 操作** |  |
> | 動作 | Microsoft.Compute/locations/capsOperations/read | 取得非同步 Caps 作業的狀態 |
> |  | **位置/磁碟操作** |  |
> | 動作 | Microsoft.Compute/locations/diskOperations/read | 取得非同步磁碟作業的狀態 |
> |  | **位置/紀錄分析** |  |
> | 動作 | Microsoft.Compute/locations/logAnalytics/getRequestRateByInterval/action | 建立記錄以依時間間隔顯示要求總數以協助進行節流診斷。 |
> | 動作 | Microsoft.Compute/locations/logAnalytics/getThrottledRequests/action | 建立記錄以依 ResourceName、OperationName 或已套用的節流原則顯示已節流要求彙總。 |
> |  | **位置/操作** |  |
> | 動作 | Microsoft.Compute/locations/operations/read | 取得非同步作業的狀態 |
> |  | **位置/發行者** |  |
> | 動作 | Microsoft.Compute/locations/publishers/read | 取得發行者的屬性 |
> |  | **位置/發行者/工件類型/優惠** |  |
> | 動作 | Microsoft.Compute/locations/publishers/artifacttypes/offers/read | 取得平台映像提供的屬性 |
> |  | **位置/發行者/工件類型/優惠/斯克** |  |
> | 動作 | Microsoft.Compute/locations/publishers/artifacttypes/offers/skus/read | 取得平台映像 SKU 的屬性 |
> |  | **位置/發行者/工件類型/優惠/斯克/版本** |  |
> | 動作 | Microsoft.Compute/locations/publishers/artifacttypes/offers/skus/versions/read | 取得平台映像版本的屬性 |
> |  | **位置/發行者/工件類型/類型** |  |
> | 動作 | Microsoft.Compute/locations/publishers/artifacttypes/types/read | 取得 VMExtension 類型的屬性 |
> |  | **位置/發行者/工件類型/類型/版本** |  |
> | 動作 | Microsoft.Compute/locations/publishers/artifacttypes/types/versions/read | 取得 VMExtension 版本的屬性 |
> |  | **位置/執行命令** |  |
> | 動作 | Microsoft.Compute/locations/runCommands/read | 列出位置中可用的執行命令 |
> |  | **位置/用法** |  |
> | 動作 | Microsoft.Compute/locations/usages/read | 取得訂用帳戶在某個位置之計算資源的服務限制和目前的使用量 |
> |  | **位置/vmSizes** |  |
> | 動作 | Microsoft.Compute/locations/vmSizes/read | 列出某個位置可用的虛擬機器大小 |
> |  | **位置/vsm 操作** |  |
> | 動作 | 微軟.計算/位置/vsm 操作/讀取 | 使用虛擬機器執行時服務延伸取得虛擬機器延伸的非同步作業狀態 |
> |  | **操作** |  |
> | 動作 | Microsoft.Compute/operations/read | 列出 Microsoft.Compute 資源提供者的可用作業 |
> |  | **鄰近放置群組** |  |
> | 動作 | 微軟.計算/鄰近放置組/讀取 | 取得鄰近放置群組的屬性 |
> | 動作 | 微軟.計算/鄰近放置組/寫入 | 建立新的鄰近放置群組或更新現有 |
> | 動作 | 微軟.計算/鄰近放置群組/刪除 | 移除鄰近放置群組 |
> |  | **restorePointCollections** |  |
> | 動作 | Microsoft.Compute/restorePointCollections/read | 取得還原點集合的屬性 |
> | 動作 | Microsoft.Compute/restorePointCollections/write | 建立新的還原點集合，或更新現有還原點集合 |
> | 動作 | Microsoft.Compute/restorePointCollections/delete | 刪除還原點集合和內含的還原點 |
> |  | **restorePointCollections/restorePoints** |  |
> | 動作 | Microsoft.Compute/restorePointCollections/restorePoints/read | 取得還原點的屬性 |
> | 動作 | Microsoft.Compute/restorePointCollections/restorePoints/write | 建立新的還原點 |
> | 動作 | Microsoft.Compute/restorePointCollections/restorePoints/delete | 刪除還原點 |
> | 動作 | Microsoft.Compute/restorePointCollections/restorePoints/retrieveSasUris/action | 取得還原點屬性以及 Blob SAS URI |
> |  | **共用VM圖像** |  |
> | 動作 | Microsoft.Compute/sharedVMImages/read | 取得 SharedVMImage 的屬性 |
> | 動作 | Microsoft.Compute/sharedVMImages/write | 建立新的 SharedVMImage，或更新現有的 SharedVMImage |
> | 動作 | Microsoft.Compute/sharedVMImages/delete | 刪除 SharedVMImage |
> |  | **sharedVMImages/versions** |  |
> | 動作 | Microsoft.Compute/sharedVMImages/versions/read | 取得 SharedVMImageVersion 的屬性 |
> | 動作 | Microsoft.Compute/sharedVMImages/versions/write | 建立新的 SharedVMImageVersion，或更新現有的 SharedVMImageVersion |
> | 動作 | Microsoft.Compute/sharedVMImages/versions/delete | 刪除 SharedVMImageVersion |
> | 動作 | Microsoft.Compute/sharedVMImages/versions/replicate/action | 將 SharedVMImageVersion 複寫至目標區域 |
> |  | **斯克庫斯** |  |
> | 動作 | Microsoft.Compute/skus/read | 取得可供您的訂用帳戶使用的 Microsoft.Compute SKU 清單 |
> |  | **快照** |  |
> | 動作 | Microsoft.Compute/snapshots/read | 取得快照集的屬性 |
> | 動作 | Microsoft.Compute/snapshots/write | 建立新的快照集，或更新現有快照集 |
> | 動作 | Microsoft.Compute/snapshots/delete | 刪除快照集 |
> | 動作 | Microsoft.Compute/snapshots/beginGetAccess/action | 取得快照集的 SAS URI 以用於 Blob 存取 |
> | 動作 | Microsoft.Compute/snapshots/endGetAccess/action | 撤銷快照集的 SAS URI |
> |  | **虛擬機器** |  |
> | 動作 | Microsoft.Compute/virtualMachines/read | 取得虛擬機器的屬性 |
> | 動作 | Microsoft.Compute/virtualMachines/write | 建立新的虛擬機器，或更新現有虛擬機器 |
> | 動作 | Microsoft.Compute/virtualMachines/delete | 刪除虛擬機器 |
> | 動作 | Microsoft.Compute/virtualMachines/start/action | 啟動虛擬機器 |
> | 動作 | Microsoft.Compute/virtualMachines/powerOff/action | 關閉虛擬機器的電源。 請注意，您會繼續收到虛擬機器的帳單。 |
> | 動作 | Microsoft.Compute/virtualMachines/redeploy/action | 重新部署虛擬機器 |
> | 動作 | Microsoft.Compute/virtualMachines/restart/action | 重新啟動虛擬機器 |
> | 動作 | Microsoft.Compute/virtualMachines/deallocate/action | 關閉虛擬機器的電源，並將計算資源釋出 |
> | 動作 | Microsoft.Compute/virtualMachines/generalize/action | 將虛擬機器的狀態設定為「已一般化」，並準備虛擬機器以供擷取 |
> | 動作 | Microsoft.Compute/virtualMachines/capture/action | 藉由複製虛擬硬碟來擷取虛擬機器，並產生可用來建立類似虛擬機器的範本 |
> | 動作 | Microsoft.Compute/virtualMachines/runCommand/action | 在虛擬機器上執行預先定義的指令碼 |
> | 動作 | Microsoft.Compute/virtualMachines/convertToManagedDisks/action | 將虛擬機器的 Blob 型磁碟轉換為受控磁碟 |
> | 動作 | Microsoft.Compute/virtualMachines/performMaintenance/action | 在 VM 上執行維護作業。 |
> | 動作 | Microsoft.Compute/virtualMachines/reimage/action | 為正在使用差異磁碟的虛擬機器重新安裝映像。 |
> | 動作 | 微軟.計算/虛擬機器/安裝補丁/操作 | 根據使用者提供的參數在虛擬機器上安裝可用的作業系統更新修補程式。 包含可用修補程式清單的評估結果也將作為其中的一部分刷新。 |
> | 動作 | 微軟.計算/虛擬機器/評估補丁/操作 | 評估虛擬機並查找可用的作業系統更新修補程式的清單。 |
> | 動作 | 微軟.計算/虛擬機器/取消補丁安裝/操作 | 取消虛擬機上正在進行的安裝 OS 更新修補程式操作。 |
> |  | **虛擬機器/擴展** |  |
> | 動作 | Microsoft.Compute/virtualMachines/extensions/read | 取得虛擬機器擴充功能的屬性 |
> | 動作 | Microsoft.Compute/virtualMachines/extensions/write | 建立新的虛擬機器擴充功能，或更新現有的虛擬機器擴充功能 |
> | 動作 | Microsoft.Compute/virtualMachines/extensions/delete | 刪除虛擬機器擴充功能 |
> |  | **虛擬機器/實體檢視** |  |
> | 動作 | Microsoft.Compute/virtualMachines/instanceView/read | 取得虛擬機器和其資源的詳細執行階段狀態 |
> |  | **虛擬機器/虛擬機器大小** |  |
> | 動作 | Microsoft.Compute/virtualMachines/vmSizes/read | 列出虛擬機器所能更新成的大小 |
> |  | **虛擬機器規模集** |  |
> | 動作 | Microsoft.Compute/virtualMachineScaleSets/read | 取得虛擬機器擴展集的屬性 |
> | 動作 | Microsoft.Compute/virtualMachineScaleSets/write | 建立新的虛擬機器擴展集或更新現有的 |
> | 動作 | Microsoft.Compute/virtualMachineScaleSets/delete | 刪除虛擬機器擴展集 |
> | 動作 | Microsoft.Compute/virtualMachineScaleSets/delete/action | 刪除虛擬機器擴展集的執行個體 |
> | 動作 | Microsoft.Compute/virtualMachineScaleSets/start/action | 啟動虛擬機器擴展集的執行個體 |
> | 動作 | Microsoft.Compute/virtualMachineScaleSets/powerOff/action | 關閉虛擬機器擴展集執行個體的電源 |
> | 動作 | Microsoft.Compute/virtualMachineScaleSets/restart/action | 重新啟動虛擬機器擴展集的執行個體 |
> | 動作 | Microsoft.Compute/virtualMachineScaleSets/deallocate/action | 關閉虛擬機器擴展集之執行個體的電源，並將其計算資源釋出  |
> | 動作 | Microsoft.Compute/virtualMachineScaleSets/manualUpgrade/action | 將虛擬機器擴展集的執行個體手動更新至最新模型 |
> | 動作 | Microsoft.Compute/virtualMachineScaleSets/reimage/action | 重新安裝虛擬機器擴展集執行個體的映像 |
> | 動作 | Microsoft.Compute/virtualMachineScaleSets/reimageAll/action | 為虛擬機器擴展集之執行個體的所有磁碟重新安裝映像 (OS 磁碟和資料磁碟)  |
> | 動作 | Microsoft.Compute/virtualMachineScaleSets/redeploy/action | 重新部署虛擬機器擴展集的執行個體 |
> | 動作 | Microsoft.Compute/virtualMachineScaleSets/performMaintenance/action | 在虛擬機器擴展集的執行個體上執行規劃的維護 |
> | 動作 | Microsoft.Compute/virtualMachineScaleSets/scale/action | 驗證現有的虛擬機器擴展集是否可以相應縮小/相應放大到指定的執行個體計數 |
> | 動作 | Microsoft.Compute/virtualMachineScaleSets/forceRecoveryServiceFabricPlatformUpdateDomainWalk/action | 手動徹查 Service Fabric 虛擬機器擴展集的平台更新網域，以完成停滯的暫止更新 |
> | 動作 | Microsoft.Compute/virtualMachineScaleSets/osRollingUpgrade/action | 開始輪流升級，以將所有虛擬機器擴展集執行個體移至最新可用的平台映像作業系統版本。 |
> | 動作 | 微軟.計算/虛擬機器表集/滾動升級/操作 | 取消虛擬機器擴展集的輪流升級 |
> |  | **virtualMachineScaleSets/extensions** |  |
> | 動作 | Microsoft.Compute/virtualMachineScaleSets/extensions/read | 取得虛擬機器擴展集延伸模組的屬性 |
> | 動作 | Microsoft.Compute/virtualMachineScaleSets/extensions/write | 建立新的虛擬機器擴展集延伸模組或更新現有的 |
> | 動作 | Microsoft.Compute/virtualMachineScaleSets/extensions/delete | 刪除虛擬機器擴展集延伸模組 |
> |  | **虛擬機器級集/擴充/角色** |  |
> | 動作 | 微軟.計算/虛擬機器表集/擴展/角色/讀取 | 使用虛擬機器執行時服務延伸取得虛擬機器延伸集中角色的屬性 |
> | 動作 | 微軟.計算/虛擬機器表集/擴展/角色/寫入 | 使用虛擬機器執行時服務延伸更新虛擬機器延伸集中的現有角色的屬性 |
> |  | **虛擬機器表集/實體檢視** |  |
> | 動作 | Microsoft.Compute/virtualMachineScaleSets/instanceView/read | 取得虛擬機器擴展集的執行個體檢視 |
> |  | **virtualMachineScaleSets/networkInterfaces** |  |
> | 動作 | Microsoft.Compute/virtualMachineScaleSets/networkInterfaces/read | 取得虛擬機器擴展集之所有網路介面的屬性 |
> |  | **虛擬機器表集/升級歷史記錄** |  |
> | 動作 | Microsoft.Compute/virtualMachineScaleSets/osUpgradeHistory/read | 取得虛擬機器擴展集的 OS 升級歷程記錄 |
> |  | **virtualMachineScaleSets/publicIPAddresses** |  |
> | 動作 | Microsoft.Compute/virtualMachineScaleSets/publicIPAddresses/read | 取得虛擬機器擴展集之所有公用 IP 位址的屬性 |
> |  | **虛擬機器表集/滾動升級** |  |
> | 動作 | Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades/read | 取得虛擬機器擴展集最新的輪流升級狀態 |
> |  | **虛擬機器表集/sus** |  |
> | 動作 | Microsoft.Compute/virtualMachineScaleSets/skus/read | 列出現有虛擬機器擴展集的有效 SKU |
> |  | **virtualMachineScaleSets/virtualMachines** |  |
> | 動作 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/read | 擷取 VM 擴展集內的虛擬機器屬性 |
> | 動作 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/write | 更新 VM 擴展集中虛擬機器的屬性 |
> | 動作 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/delete | 刪除 VM 擴展集內的特定虛擬機器。 |
> | 動作 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/start/action | 啟動 VM 擴展集內的虛擬機器執行個體。 |
> | 動作 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/powerOff/action | 將 VM 擴展集內的虛擬機器執行個體關閉電源。 |
> | 動作 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/restart/action | 重新啟動 VM 擴展集內的虛擬機器執行個體。 |
> | 動作 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/deallocate/action | 關閉 VM 擴展集之虛擬機器的電源，並將其計算資源釋出。 |
> | 動作 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/reimage/action | 重新安裝虛擬機器擴展集中的虛擬機器執行個體映像。 |
> | 動作 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/reimageAll/action | 為虛擬機器擴展集中虛擬機器執行個體的所有磁碟 (OS 磁碟和資料磁碟) 重新安裝映像。 |
> | 動作 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/redeploy/action | 重新部署虛擬機器擴展集中的虛擬機器執行個體 |
> | 動作 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/performMaintenance/action | 在虛擬機器擴展集的虛擬機器執行個體上執行規劃的維護 |
> | 動作 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/runCommand/action | 在虛擬機器擴展集中的虛擬機器執行個體上執行預先定義的指令碼。 |
> |  | **虛擬機器表集/虛擬機器/實體檢視** |  |
> | 動作 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/instanceView/read | 擷取 VM 擴展集內的虛擬機器執行個體檢視。 |
> |  | **virtualMachineScaleSets/virtualMachines/networkInterfaces** |  |
> | 動作 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/read | 取得使用虛擬機器擴展集建立的虛擬機器其中一個或所有網路介面屬性 |
> |  | **虛擬機器級集/虛擬機器/網路介面/ip設定** |  |
> | 動作 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/read | 取得使用虛擬機器擴展集建立的網路介面其中一個或所有 IP 設定屬性。 IP 設定代表私人 IP |
> |  | **虛擬機器設定/虛擬機器/網路介面/ip 設定/公共 IP 位址** |  |
> | 動作 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/publicIPAddresses/read | 取得使用虛擬機器擴展集建立的公用 IP 位址屬性。 虛擬機器擴展集最多可以在每個 ipconfiguration (私人 IP) 建立一個公用 IP |
> |  | **虛擬機器表集/vmSizes** |  |
> | 動作 | 微軟.計算/虛擬機器規模集/vmSizes/讀取 | 列出用於在虛擬機器縮放集中建立或更新虛擬機器的大小 |
> |  | **虛擬機器設定虛擬機器/擴充** |  |
> | 動作 | 微軟.計算/虛擬機器規模設定虛擬機器/擴展/讀取 | 在虛擬機器縮放集中取得虛擬機器擴充的屬性 |
> | 動作 | 微軟.計算/虛擬機器規模設定虛擬機器/擴展/寫入 | 在虛擬機器縮放集中為虛擬機器建立新延伸或更新現有擴充 |
> | 動作 | 微軟.計算/虛擬機器規模設定虛擬機器/擴展/刪除 | 移除虛擬機器縮放集中虛擬機器的延伸 |
> |  | **虛擬機器** |  |
> | DataAction | Microsoft.Compute/virtualMachines/login/action | 以一般使用者身分登入虛擬機器 |
> | DataAction | Microsoft.Compute/virtualMachines/loginAsAdmin/action | 以 Windows 系統管理員或 Linux 根使用者權限登入虛擬機器 |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

Azure 服務:[成本管理](../cost-management-billing/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.Consumption/register/action | 向 Consumption RP 註冊 |
> |  | **彙總成本** |  |
> | 動作 | 微軟.消費/聚合成本/讀取 | 列出管理組的聚合成本。 |
> |  | **平衡** |  |
> | 動作 | Microsoft.Consumption/balances/read | 針對管理群組列出計費期的使用率摘要。 |
> |  | **預算** |  |
> | 動作 | Microsoft.Consumption/budgets/read | 依訂用帳戶或管理群組列出預算。 |
> | 動作 | Microsoft.Consumption/budgets/write | 依訂用帳戶或管理群組建立和更新預算。 |
> | 動作 | Microsoft.Consumption/budgets/delete | 依訂用帳戶或管理群組刪除預算。 |
> |  | **收費** |  |
> | 動作 | Microsoft.Consumption/charges/read | 列出費用 |
> |  | **學分** |  |
> | 動作 | Microsoft.Consumption/credits/read | 列出信用額度 |
> |  | **事件** |  |
> | 動作 | Microsoft.Consumption/events/read | 列出事件 |
> |  | **外部計費帳戶/標籤** |  |
> | 動作 | 微軟.消費/外部計費帳戶/標籤/讀取 | 列出 EA 和訂用帳戶的標記。 |
> |  | **外部訂閱/標籤** |  |
> | 動作 | 微軟.消費/外部訂閱/標籤/讀取 | 列出 EA 和訂用帳戶的標記。 |
> |  | **預測** |  |
> | 動作 | Microsoft.Consumption/forecasts/read | 列出預測 |
> |  | **lots** |  |
> | 動作 | Microsoft.Consumption/lots/read | 列出位置 |
> |  | **市場** |  |
> | 動作 | Microsoft.Consumption/marketplaces/read | 針對適用於 EA 與 WebDirect 訂用帳戶的範圍列出 Marketplace 資源使用狀況詳細資料。 |
> |  | **動作結果** |  |
> | 動作 | Microsoft.Consumption/operationresults/read | 列出作業結果 |
> |  | **操作** |  |
> | 動作 | Microsoft.Consumption/operations/read | 列出 Microsoft.Consumption 資源提供者支援的所有作業。 |
> |  | **操作狀態** |  |
> | 動作 | Microsoft.Consumption/operationstatus/read | 列出作業狀態 |
> |  | **價格表** |  |
> | 動作 | Microsoft.Consumption/pricesheets/read | 列出適用於訂用帳戶或管理群組的價位表資料。 |
> |  | **預訂詳情** |  |
> | 動作 | Microsoft.Consumption/reservationDetails/read | 依保留順序或管理群組列出保留執行個體的使用率詳細資料。 詳細資料屬於每日層級的每個執行個體。 |
> |  | **預訂建議** |  |
> | 動作 | Microsoft.Consumption/reservationRecommendations/read | 列出訂用帳戶保留執行個體的單一或共用建議。 |
> |  | **預訂摘要** |  |
> | 動作 | Microsoft.Consumption/reservationSummaries/read | 依保留順序或管理群組列出保留執行個體的使用率摘要。 摘要資料位於每月或每日層級。 |
> |  | **預訂交易** |  |
> | 動作 | Microsoft.Consumption/reservationTransactions/read | 依管理群組列出保留執行個體的交易記錄。 |
> |  | **「標記」** |  |
> | 動作 | Microsoft.Consumption/tags/read | 列出 EA 和訂用帳戶的標記。 |
> |  | **租戶** |  |
> | 動作 | Microsoft.Consumption/tenants/register/action | 依某個租用戶註冊 Microsoft.Consumption 範圍的動作。 |
> | 動作 | 微軟.消費/租戶/閱讀 | 列出租戶 |
> |  | **條款** |  |
> | 動作 | Microsoft.Consumption/terms/read | 列出適用於訂用帳戶或管理群組的條款。 |
> |  | **usageDetails** |  |
> | 動作 | Microsoft.Consumption/usageDetails/read | 針對適用於 EA 與 WebDirect 訂用帳戶的範圍列出使用狀況詳細資料。 |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

Azure 服務:[容器實體](../container-instances/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.ContainerInstance/register/action | 為容器執行個體資源提供者註冊訂用帳戶，並允許建立容器群組。 |
> |  | **containerGroups** |  |
> | 動作 | Microsoft.ContainerInstance/containerGroups/read | 取得所有容器群組。 |
> | 動作 | Microsoft.ContainerInstance/containerGroups/write | 建立或更新特定容器群組。 |
> | 動作 | Microsoft.ContainerInstance/containerGroups/delete | 刪除特定容器群組。 |
> | 動作 | Microsoft.ContainerInstance/containerGroups/restart/action | 重新啟動特定容器群組。 |
> | 動作 | Microsoft.ContainerInstance/containerGroups/stop/action | 停止特定容器群組。 計算資源將會取消配置，計費也會停止。 |
> | 動作 | Microsoft.ContainerInstance/containerGroups/start/action | 啟動特定容器群組。 |
> |  | **容器群組/容器** |  |
> | 動作 | Microsoft.ContainerInstance/containerGroups/containers/exec/action | 執行至特定容器內。 |
> |  | **容器群組/容器/編譯紀錄** |  |
> | 動作 | 微軟.容器實例/容器組/容器/構建日誌/讀取 | 獲取特定容器的生成日誌。 |
> |  | **容器群組/容器/紀錄** |  |
> | 動作 | Microsoft.ContainerInstance/containerGroups/containers/logs/read | 取得特定容器的記錄。 |
> |  | **容器群組/操作結果** |  |
> | 動作 | 微軟.容器實例/容器組/操作結果/讀取 | 取得非同步操作結果 |
> |  | **容器組/供應商/微軟.見解/診斷設置** |  |
> | 動作 | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/diagnosticSettings/read | 取得容器群組的診斷設定。 |
> | 動作 | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/diagnosticSettings/write | 建立或更新容器群組的診斷設定。 |
> |  | **容器組/供應商/微軟.見解/指標定義** |  |
> | 動作 | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/metricDefinitions/read | 取得容器群組的可用計量。 |
> |  | **位置** |  |
> | 動作 | Microsoft.ContainerInstance/locations/deleteVirtualNetworkOrSubnets/action | 通知 Microsoft.ContainerInstance 正在刪除虛擬網路或子網路。 |
> |  | **位置/快取影像** |  |
> | 動作 | Microsoft.ContainerInstance/locations/cachedImages/read | 取得區域中訂用帳戶的快取映像。 |
> |  | **位置/功能** |  |
> | 動作 | Microsoft.ContainerInstance/locations/capabilities/read | 取得區域的功能。 |
> |  | **位置/操作** |  |
> | 動作 | 微軟.容器實例/位置/操作/讀取 | 列出 Azure 容器實例服務的操作。 |
> |  | **位置/用法** |  |
> | 動作 | Microsoft.ContainerInstance/locations/usages/read | 取得特定區域的使用量。 |
> |  | **操作** |  |
> | 動作 | 微軟.容器實例/操作/讀取 | 列出 Azure 容器實例服務的操作。 |
> |  | **服務關聯連結** |  |
> | 動作 | 微軟.容器實例/服務關聯連結/刪除 | 刪除由子網上的 Azure 容器實例資源提供程式創建的服務關聯連結。 |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

Azure 服務:[容器註冊表](../container-registry/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.ContainerRegistry/register/action | 針對容器登錄資源提供者註冊訂用帳戶，並讓您能夠建立容器登錄。 |
> |  | **檢查名稱可用性** |  |
> | 動作 | Microsoft.ContainerRegistry/checkNameAvailability/read | 檢查容器登錄名稱是否可供使用。 |
> |  | **位置** |  |
> | 動作 | Microsoft.ContainerRegistry/locations/deleteVirtualNetworkOrSubnets/action | 讓 Microsoft.ContainerRegistry 知道虛擬網路或子網路即將刪除 |
> |  | **位置/動作結果** |  |
> | 動作 | Microsoft.ContainerRegistry/locations/operationResults/read | 取得非同步作業結果 |
> |  | **操作** |  |
> | 動作 | Microsoft.ContainerRegistry/operations/read | 列出所有可用的 Azure Container Registry REST API 作業 |
> |  | **registries** |  |
> | 動作 | Microsoft.ContainerRegistry/registries/read | 取得指定容器登錄的屬性，或列出指定資源群組或訂用帳戶下的所有容器登錄。 |
> | 動作 | Microsoft.ContainerRegistry/registries/write | 使用指定參數，建立或更新容器登錄。 |
> | 動作 | Microsoft.ContainerRegistry/registries/delete | 刪除容器登錄。 |
> | 動作 | Microsoft.ContainerRegistry/registries/listCredentials/action | 列出指定容器登錄的登入認證。 |
> | 動作 | Microsoft.ContainerRegistry/registries/regenerateCredential/action | 針對指定容器登錄重新產生一個登入認證。 |
> | 動作 | 微軟.集裝箱註冊/註冊/生成憑據/操作 | 為指定容器註冊表的權杖生成金鑰。 |
> | 動作 | Microsoft.ContainerRegistry/registries/importImage/action | 使用指定參數將映像匯入到容器登錄。 |
> | 動作 | Microsoft.ContainerRegistry/registries/getBuildSourceUploadUrl/action | 取得上傳位置，讓使用者可上傳來源。 |
> | 動作 | Microsoft.ContainerRegistry/registries/queueBuild/action | 根據要求參數建立新的組建，並將它新增至組建佇列。 |
> | 動作 | Microsoft.ContainerRegistry/registries/listBuildSourceUploadUrl/action | 取得容器登錄的來源上傳 URL 位置。 |
> | 動作 | Microsoft.ContainerRegistry/registries/scheduleRun/action | 排程對容器登錄的流程執行。 |
> |  | **註冊表/代理池** |  |
> | 動作 | 微軟.容器註冊/註冊表/代理池/清單佇列狀態/操作 | 列出容器註冊表的代理池的所有佇列狀態。 |
> |  | **註冊表/工件** |  |
> | 動作 | 微軟.集裝箱註冊/註冊/專案/刪除 | 刪除容器註冊表中的專案。 |
> |  | **registries/builds** |  |
> | 動作 | Microsoft.ContainerRegistry/registries/builds/read | 取得指定組建的屬性，或列出指定容器登錄的所有組建。 |
> | 動作 | Microsoft.ContainerRegistry/registries/builds/write | 使用指定參數更新容器登錄的組建。 |
> | 動作 | Microsoft.ContainerRegistry/registries/builds/getLogLink/action | 取得下載組建記錄的連結。 |
> | 動作 | Microsoft.ContainerRegistry/registries/builds/cancel/action | 取消現有組建。 |
> |  | **註冊表/產生工作** |  |
> | 動作 | Microsoft.ContainerRegistry/registries/buildTasks/read | 取得指定組建工作的屬性，或列出指定容器登錄的所有組建工作。 |
> | 動作 | Microsoft.ContainerRegistry/registries/buildTasks/write | 使用指定參數建立或更新容器登錄的組建工作。 |
> | 動作 | Microsoft.ContainerRegistry/registries/buildTasks/delete | 刪除容器登錄中的組建工作。 |
> | 動作 | Microsoft.ContainerRegistry/registries/buildTasks/listSourceRepositoryProperties/action | 列出組建工作的原始檔控制屬性。 |
> |  | **registries/buildTasks/steps** |  |
> | 動作 | Microsoft.ContainerRegistry/registries/buildTasks/steps/read | 取得指定組建步驟的屬性，或列出指定組建工作的所有組建步驟。 |
> | 動作 | Microsoft.ContainerRegistry/registries/buildTasks/steps/write | 使用指定參數建立或更新組建工作的組建步驟。 |
> | 動作 | Microsoft.ContainerRegistry/registries/buildTasks/steps/delete | 從組建工作中刪除組建步驟。 |
> | 動作 | Microsoft.ContainerRegistry/registries/buildTasks/steps/listBuildArguments/action | 列出組建步驟的組建引數，包括祕密引數。 |
> |  | **registries/eventGridFilters** |  |
> | 動作 | Microsoft.ContainerRegistry/registries/eventGridFilters/read | 取得指定事件格線篩選的屬性，或列出指定容器登錄的所有事件格線篩選。 |
> | 動作 | Microsoft.ContainerRegistry/registries/eventGridFilters/write | 使用指定參數，建立或更新容器登錄的事件格線篩選。 |
> | 動作 | Microsoft.ContainerRegistry/registries/eventGridFilters/delete | 從容器登錄中刪除事件格線篩選。 |
> |  | **登錄表/清單政策** |  |
> | 動作 | Microsoft.ContainerRegistry/registries/listPolicies/read | 列出指定容器登錄的原則 |
> |  | **註冊表/清單使用** |  |
> | 動作 | Microsoft.ContainerRegistry/registries/listUsages/read | 列出指定容器登錄的配額使用量。 |
> |  | **登錄表/中繼資料** |  |
> | 動作 | 微軟.集裝箱註冊/註冊/元數據/讀取 | 取得容器註冊表的儲存函式庫的中繼資料 |
> | 動作 | 微軟.集裝箱註冊/註冊/元數據/寫入 | 更新容器註冊表儲存庫的中繼資料 |
> |  | **註冊表/操作狀態** |  |
> | 動作 | Microsoft.ContainerRegistry/registries/operationStatuses/read | 取得登錄非同步作業狀態 |
> |  | **註冊表/私有端接連接 Proxies** |  |
> | 動作 | 微軟.集裝箱註冊/註冊/私人終端連接Proxies/驗證/操作 | 驗證專用終結點連線代理 (僅限 NRP) |
> | 動作 | 微軟.集裝箱註冊/註冊/私人端接連接Proxies/讀取 | 取得專用終結點連線代理 (僅限 NRP) |
> | 動作 | 微軟.集裝箱註冊/註冊/私人終端連接Proxies/寫 | 建立專用的終結點連線代理(僅限 NRP) |
> | 動作 | 微軟.集裝箱註冊/註冊/私人終端連接Proxies/刪除 | 移除專用的終結點連線代理(僅限 NRP) |
> |  | **註冊表/私有端接連接狀態/操作狀態** |  |
> | 動作 | 微軟.集裝箱註冊/註冊/私人端接連接Proxies/操作狀態/讀取 | 取得專用終結點連線代理非同步作業狀態 |
> |  | **註冊表/拉取** |  |
> | 動作 | Microsoft.ContainerRegistry/registries/pull/read | 從容器登錄中提取或取得映像。 |
> |  | **註冊表/推送** |  |
> | 動作 | Microsoft.ContainerRegistry/registries/push/write | 將映像推送或寫入至容器登錄。 |
> |  | **註冊/檢疫** |  |
> | 動作 | 微軟.集裝箱註冊/註冊/檢疫/讀 | 從容器登錄中提取或取得隔離的映像 |
> | 動作 | 微軟.集裝箱註冊/註冊/檢疫/寫入 | 寫入/修改已隔離映像的隔離狀態 |
> |  | **registries/replications** |  |
> | 動作 | Microsoft.ContainerRegistry/registries/replications/read | 取得指定複寫的屬性，或列出指定容器登錄的所有複寫。 |
> | 動作 | Microsoft.ContainerRegistry/registries/replications/write | 使用指定參數，建立或更新容器登錄的複寫。 |
> | 動作 | Microsoft.ContainerRegistry/registries/replications/delete | 刪除容器登錄中的複寫。 |
> |  | **註冊表/複製/操作狀態** |  |
> | 動作 | Microsoft.ContainerRegistry/registries/replications/operationStatuses/read | 取得複寫非同步作業狀態 |
> |  | **registries/runs** |  |
> | 動作 | Microsoft.ContainerRegistry/registries/runs/read | 對容器登錄或清單流程執行取得流程執行的屬性。 |
> | 動作 | Microsoft.ContainerRegistry/registries/runs/write | 更新流程執行。 |
> | 動作 | Microsoft.ContainerRegistry/registries/runs/listLogSasUrl/action | 取得流程執行的記錄檔 SAS URL。 |
> | 動作 | Microsoft.ContainerRegistry/registries/runs/cancel/action | 取消現有的流程執行。 |
> |  | **註冊表/範圍圖** |  |
> | 動作 | 微軟.集裝箱註冊/註冊/範圍地圖/閱讀 | 獲取指定作用域映射的屬性或列出指定容器註冊表的所有作用域映射。 |
> | 動作 | 微軟.集裝箱註冊/註冊/範圍圖/寫入 | 使用指定的參數創建或更新容器註冊表的範圍映射。 |
> | 動作 | 微軟.集裝箱註冊/註冊/範圍映射/刪除 | 從容器註冊表中刪除範圍映射。 |
> |  | **註冊表/範圍對應/操作狀態** |  |
> | 動作 | 微軟.集裝箱註冊/註冊/範圍地圖/操作狀態/讀取 | 獲取範圍映射非同步操作狀態。 |
> |  | **註冊/簽名** |  |
> | 動作 | Microsoft.ContainerRegistry/registries/sign/write | 推送/提取容器登錄的內容信任中繼資料。 |
> |  | **登錄表格表** |  |
> | 動作 | 微軟.集裝箱註冊/註冊表/任務運行/清單詳細資訊/操作 | 列出容器註冊表的任務運行的所有詳細資訊。 |
> |  | **registries/tasks** |  |
> | 動作 | Microsoft.ContainerRegistry/registries/tasks/read | 取得容器登錄的工作或列出所有工作。 |
> | 動作 | Microsoft.ContainerRegistry/registries/tasks/write | 建立或更新容器登錄的工作。 |
> | 動作 | Microsoft.ContainerRegistry/registries/tasks/delete | 刪除容器登錄的工作。 |
> | 動作 | Microsoft.ContainerRegistry/registries/tasks/listDetails/action | 列出容器登錄工作的所有詳細資料。 |
> |  | **註冊表/權杖** |  |
> | 動作 | 微軟.集裝箱註冊/註冊/令牌/讀取 | 獲取指定權杖的屬性或列出指定容器註冊表的所有權杖。 |
> | 動作 | 微軟.集裝箱註冊/註冊/權杖/寫入 | 使用指定的參數創建或更新容器註冊表的權杖。 |
> | 動作 | 微軟.集裝箱註冊/註冊/權杖/刪除 | 從容器註冊表中刪除權杖。 |
> |  | **註冊表/權杖/操作狀態** |  |
> | 動作 | 微軟.集裝箱註冊/註冊/權杖/操作狀態/讀取 | 獲取權杖非同步操作狀態。 |
> |  | **registries/updatePolicies** |  |
> | 動作 | Microsoft.ContainerRegistry/registries/updatePolicies/write | 更新指定容器登錄的原則 |
> |  | **registries/webhooks** |  |
> | 動作 | Microsoft.ContainerRegistry/registries/webhooks/read | 取得指定 Webhook 的屬性，或列出指定容器登錄的所有 Webhook。 |
> | 動作 | Microsoft.ContainerRegistry/registries/webhooks/write | 使用指定參數，建立或更新容器登錄的 Webhook。 |
> | 動作 | Microsoft.ContainerRegistry/registries/webhooks/delete | 刪除容器登錄中的 Webhook。 |
> | 動作 | Microsoft.ContainerRegistry/registries/webhooks/getCallbackConfig/action | 針對 Webhook 取得服務 URI 的設定和自訂標頭。 |
> | 動作 | Microsoft.ContainerRegistry/registries/webhooks/ping/action | 觸發要傳送到 Webhook 的 Ping 事件。 |
> | 動作 | Microsoft.ContainerRegistry/registries/webhooks/listEvents/action | 針對指定的 Webhook 列出最近的事件。 |
> |  | **註冊表/網鉤/操作狀態** |  |
> | 動作 | Microsoft.ContainerRegistry/registries/webhooks/operationStatuses/read | 取得 Webhook 非同步作業狀態 |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

Azure 服務[:Azure 庫伯奈斯服務 (AKS)](../aks/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.ContainerService/register/action | 向 Microsoft.ContainerService 資源提供者註冊訂用帳戶 |
> | 動作 | Microsoft.ContainerService/unregister/action | 向 Microsoft.ContainerService 資源提供者取消註冊訂用帳戶 |
> |  | **containerServices** |  |
> | 動作 | Microsoft.ContainerService/containerServices/read | 取得容器服務 |
> | 動作 | Microsoft.ContainerService/containerServices/write | 建立新的容器服務，或更新現有的容器服務 |
> | 動作 | Microsoft.ContainerService/containerServices/delete | 刪除容器服務 |
> |  | **位置/動作結果** |  |
> | 動作 | Microsoft.ContainerService/locations/operationresults/read | 取得非同步作業結果的狀態 |
> |  | **位置/操作** |  |
> | 動作 | Microsoft.ContainerService/locations/operations/read | 取得非同步作業的狀態 |
> |  | **位置/協調器** |  |
> | 動作 | Microsoft.ContainerService/locations/orchestrators/read | 列出支援的協調器 |
> |  | **託管叢集** |  |
> | 動作 | Microsoft.ContainerService/managedClusters/read | 取得受控叢集 |
> | 動作 | Microsoft.ContainerService/managedClusters/write | 建立新的受控叢集，或更新現有的受控叢集 |
> | 動作 | Microsoft.ContainerService/managedClusters/delete | 刪除受控叢集 |
> | 動作 | Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action | 列出受控叢集的 clusterAdmin 認證 |
> | 動作 | Microsoft.ContainerService/managedClusters/listClusterUserCredential/action | 列出受控叢集的 clusterUser 認證 |
> | 動作 | 微軟.容器服務/託管群集/清單群集監視使用者憑據/操作 | 列出託管群集的群集監視用戶憑據 |
> | 動作 | Microsoft.ContainerService/managedClusters/resetServicePrincipalProfile/action | 重設受控叢集的服務主體 |
> | 動作 | Microsoft.ContainerService/managedClusters/resetAADProfile/action | 重設受控叢集的 AAD 設定檔 |
> | 動作 | 微軟.容器服務/託管群集/旋轉群集證書/操作 | 旋轉託管叢集的憑證 |
> | 動作 | 微軟.容器服務/託管群集/專用端點連接審批/操作 | 確定是否允許使用者批准專用終結點連線 |
> |  | **託管叢集/存取設定檔** |  |
> | 動作 | Microsoft.ContainerService/managedClusters/accessProfiles/read | 依角色名稱取得受控叢集存取設定檔 |
> | 動作 | Microsoft.ContainerService/managedClusters/accessProfiles/listCredential/action | 使用清單認證依角色名稱取得受控叢集存取設定檔 |
> |  | **託管群集/代理池** |  |
> | 動作 | 微軟.容器服務/託管集群/代理池/讀取 | 取得代理池 |
> | 動作 | 微軟.容器服務/託管集群/代理池/寫入 | 建立新代理池或更新現有代理池 |
> | 動作 | 微軟.容器服務/託管集群/代理池/刪除 | 移除代理池 |
> |  | **託管叢集/代理池/升級配置檔** |  |
> | 動作 | 微軟.容器服務/託管集群/代理池/升級配置檔/讀取 | 取得代理池的升級設定檔 |
> |  | **託管叢集/可用代理池版本** |  |
> | 動作 | 微軟.容器服務/託管群集/可用代理池版本/讀取 | 取得叢集可用代理池版本 |
> |  | **託管群集/探測器** |  |
> | 動作 | 微軟.集裝箱服務/託管集群/探測器/讀取 | 取得託管叢集偵測器 |
> |  | **託管群集/診斷狀態** |  |
> | 動作 | 微軟.容器服務/託管群集/診斷狀態/讀取 | 取得叢集的診斷狀態 |
> |  | **託管叢集/升級設定檔** |  |
> | 動作 | 微軟.容器服務/託管集群/升級配置檔/讀取 | 取得叢集的升級設定檔 |
> |  | **開啟移位叢集** |  |
> | 動作 | Microsoft.ContainerService/openShiftClusters/read | 取得開放式班次群集 |
> | 動作 | Microsoft.ContainerService/openShiftClusters/write | 建立新的或更新現有的 Open Shift 叢集 |
> | 動作 | Microsoft.ContainerService/openShiftClusters/delete | 移除開放式班次群集 |
> |  | **開放式轉移託管集群** |  |
> | 動作 | Microsoft.ContainerService/openShiftManagedClusters/read | 取得開放式班次託管群集 |
> | 動作 | Microsoft.ContainerService/openShiftManagedClusters/write | 建立新的或更新現有的 Open Shift 受控叢集 |
> | 動作 | Microsoft.ContainerService/openShiftManagedClusters/delete | 移除開放式班次託管群集 |
> |  | **操作** |  |
> | 動作 | Microsoft.ContainerService/operations/read | 列出 Microsoft.ContainerService 資源提供者的可用作業 |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

Azure 服務:[成本管理](../cost-management-billing/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.CostManagement/query/action | 依範圍查使用狀況資料。 |
> | 動作 | Microsoft.CostManagement/reports/action | 依範圍排定使用狀況資料報告。 |
> | 動作 | Microsoft.CostManagement/exports/action | 執行指定的匯出。 |
> | 動作 | 微軟.成本管理/註冊/操作 | 註冊 Microsoft.Cost 管理範圍的操作。 |
> | 動作 | 微軟.成本管理/觀點/行動 | 創建視圖。 |
> | 動作 | 微軟.成本管理/預測/行動 | 按作用域預測使用方式數據。 |
> |  | **警示** |  |
> | 動作 | 微軟.成本管理/警報/寫入 | 更新警示。 |
> |  | **雲連接器** |  |
> | 動作 | 微軟.成本管理/雲連接器/讀取 | 列出經過身份驗證使用者的雲端連接器。 |
> | 動作 | 微軟.成本管理/雲連接器/寫入 | 建立或更新指定的雲端連接器。 |
> | 動作 | 微軟.成本管理/雲連接器/刪除 | 刪除指定的雲端連接器。 |
> |  | **尺寸** |  |
> | 動作 | Microsoft.CostManagement/dimensions/read | 依範圍列出所有支援的維度。 |
> |  | **出口** |  |
> | 動作 | Microsoft.CostManagement/exports/read | 依範圍列出匯出。 |
> | 動作 | Microsoft.CostManagement/exports/write | 建立或更新指定的匯出。 |
> | 動作 | Microsoft.CostManagement/exports/delete | 刪除指定的匯出。 |
> | 動作 | 微軟.成本管理/出口/運行/操作 | 運行匯出。 |
> |  | **外部結算帳戶** |  |
> | 動作 | 微軟.成本管理/外部計費帳戶/讀取 | 列出已身份驗證使用者的外部計費帳戶。 |
> | 動作 | 微軟.成本管理/外部計費帳戶/查詢/操作 | 查詢外部計費科目的使用數據。 |
> | 動作 | 微軟.成本管理/外部計費帳戶/預測/行動 | 外部計費科目的預測使用方式數據。 |
> |  | **外部計費帳戶/維度** |  |
> | 動作 | 微軟.成本管理/外部計費帳戶/維度/讀取 | 列出外部計費帳戶的所有受支持維度。 |
> |  | **外部計費帳戶/外部訂閱** |  |
> | 動作 | 微軟.成本管理/外部計費帳戶/外部訂閱/讀取 | 列出經過身份驗證的使用者的外部 BillingACCOUNT 中的外部訂閱。 |
> |  | **外部計費帳戶/預測** |  |
> | 動作 | 微軟.成本管理/外部計費帳戶/預測/閱讀 | 外部計費科目的預測使用方式數據。 |
> |  | **外部計費帳戶/查詢** |  |
> | 動作 | 微軟.成本管理/外部計費帳戶/查詢/讀取 | 查詢外部計費科目的使用數據。 |
> |  | **外部訂閱** |  |
> | 動作 | 微軟.成本管理/外部訂閱/讀取 | 列出經過身份驗證的使用者的外部訂閱。 |
> | 動作 | 微軟.成本管理/外部訂閱/寫入 | 更新外部訂閱的關聯管理群組 |
> | 動作 | 微軟.成本管理/外部訂閱/查詢/操作 | 查詢外部訂閱的使用數據。 |
> | 動作 | 微軟.成本管理/外部訂閱/預測/行動 | 外部計費科目的預測使用方式數據。 |
> |  | **外部訂閱/維度** |  |
> | 動作 | 微軟.成本管理/外部訂閱/維度/讀取 | 列出外部訂閱的所有受支援維度。 |
> |  | **外部訂閱/預測** |  |
> | 動作 | 微軟.成本管理/外部訂閱/預測/閱讀 | 外部計費科目的預測使用方式數據。 |
> |  | **外部訂閱/查詢** |  |
> | 動作 | 微軟.成本管理/外部訂閱/查詢/讀取 | 查詢外部訂閱的使用數據。 |
> |  | **預測** |  |
> | 動作 | 微軟.成本管理/預測/閱讀 | 按作用域預測使用方式數據。 |
> |  | **操作** |  |
> | 動作 | 微軟.成本管理/操作/讀取 | 列出 Microsoft.CostManagement 資源提供程式支援的所有操作。 |
> |  | **查詢** |  |
> | 動作 | Microsoft.CostManagement/query/read | 依範圍查使用狀況資料。 |
> |  | **報告** |  |
> | 動作 | Microsoft.CostManagement/reports/read | 依範圍排定使用狀況資料報告。 |
> |  | **租戶** |  |
> | 動作 | 微軟.成本管理/租戶/註冊/行動 | 註冊操作以進行 Microsoft.成本管理的範圍。 |
> |  | **檢視** |  |
> | 動作 | 微軟.成本管理/觀點/閱讀 | 列出所有保存的視圖。 |
> | 動作 | 微軟.成本管理/檢視/刪除 | 刪除保存的檢視。 |
> | 動作 | 微軟.成本管理/觀點/寫入 | 更新視圖。 |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

Azure 服務[:Azure 資料框](../databox-family/index.md)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.DataBox/register/action | 註冊提供者 Microsoft.Databox |
> | 動作 | 微軟.DataBox/取消註冊/操作 | 取消註冊供應商微軟.資料箱 |
> |  | **工作** |  |
> | 動作 | Microsoft.DataBox/jobs/cancel/action | 取消進行中的訂單。 |
> | 動作 | Microsoft.DataBox/jobs/bookShipmentPickUp/action | 允許預約退貨商品的取貨。 |
> | 動作 | Microsoft.DataBox/jobs/read | 列出或取得訂單 |
> | 動作 | Microsoft.DataBox/jobs/delete | 刪除訂單 |
> | 動作 | Microsoft.DataBox/jobs/write | 建立或更新訂單 |
> | 動作 | Microsoft.DataBox/jobs/listCredentials/action | 列出與訂單相關的未加密認證。 |
> |  | **位置** |  |
> | 動作 | 微軟.DataBox/位置/驗證輸入/操作 | 此方法執行所有類型的驗證。 |
> | 動作 | Microsoft.DataBox/locations/validateAddress/action | 驗證出貨地址，並提供備用的地址 (若有的話)。 |
> | 動作 | Microsoft.DataBox/locations/availableSkus/action | 此方法會傳回可用的 SKU 清單。 |
> | 動作 | 微軟.DataBox/位置/區域配置/操作 | 此方法返回該區域的配置。 |
> |  | **位置/可用** |  |
> | 動作 | 微軟.DataBox/位置/可用Skus/讀 | 列出或取得 Skus |
> |  | **位置/動作結果** |  |
> | 動作 | Microsoft.DataBox/locations/operationResults/read | 列出或取得作業結果 |
> |  | **操作** |  |
> | 動作 | 微軟.DataBox/操作/讀取 | 列出或取得操作 |
> |  | **subscriptions/resourceGroups** |  |
> | 動作 | 微軟.DataBox/訂閱/資源組/移動資源/操作 | 此方法執行資源移動。 |
> | 動作 | 微軟.DataBox/訂閱/資源組/驗證移動資源/操作 | 此方法驗證是否允許資源移動。 |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

Azure 服務[:Azure 資料框邊緣](../databox-online/data-box-edge-overview.md)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> |  | **資料盒邊緣裝置** |  |
> | 動作 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/uploadCertificate/action | 上傳裝置註冊的憑證 |
> | 動作 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/write | 建立或更新 Data Box Edge 裝置 |
> | 動作 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | 列出或取得 Data Box Edge 裝置 |
> | 動作 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/delete | 刪除 Data Box Edge 裝置 |
> | 動作 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | 列出或取得 Data Box Edge 裝置 |
> | 動作 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | 列出或取得 Data Box Edge 裝置 |
> | 動作 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/write | 建立或更新 Data Box Edge 裝置 |
> | 動作 | 微軟.DataBoxEdge/DataBoxEdge設備/獲取擴展資訊/操作 | 擷取資源延伸資訊 |
> | 動作 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/scanForUpdates/action | 掃描更新 |
> | 動作 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/downloadUpdates/action | 在裝置中下載更新 |
> | 動作 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/installUpdates/action | 在裝置上安裝更新 |
> |  | **資料盒邊緣裝置/警報** |  |
> | 動作 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/alerts/read | 列出或取得警示 |
> | 動作 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/alerts/read | 列出或取得警示 |
> |  | **資料盒邊緣裝置/頻寬計劃** |  |
> | 動作 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/read | 列出或取得頻寬排程 |
> | 動作 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/read | 列出或取得頻寬排程 |
> | 動作 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/write | 建立或更新頻寬排程 |
> | 動作 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/delete | 刪除頻寬排程 |
> |  | **資料BoxEdge裝置/頻寬計劃/操作結果** |  |
> | 動作 | 微軟.DataBoxEdge/DataBoxEdge設備/带寬計劃/操作結果/閱讀 | 列出或取得操作結果 |
> |  | **資料盒邊緣裝置/工作** |  |
> | 動作 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/jobs/read | 列出或取得作業 |
> |  | **資料盒邊緣裝置/網路設定** |  |
> | 動作 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/networkSettings/read | 列出或取得裝置網路設定 |
> |  | **資料盒邊緣裝置/節點** |  |
> | 動作 | 微軟.DataBoxEdge/DataBox邊緣設備/節點/讀取 | 列出或取得節點 |
> |  | **資料盒邊緣裝置/操作結果** |  |
> | 動作 | 微軟.DataBoxEdge/DataBoxEdge 設備/操作結果/讀取 | 列出或取得操作結果 |
> |  | **資料盒邊緣裝置/操作狀態** |  |
> | 動作 | 微軟.DataBoxEdge/DataBox邊緣設備/操作狀態/讀取 | 列出或抓取作業狀態 |
> |  | **資料盒邊緣裝置/訂單** |  |
> | 動作 | 微軟.DataBoxEdge/DataBox邊緣設備/訂單/閱讀 | 列出或取得訂單 |
> | 動作 | 微軟.DataBoxEdge/DataBox邊緣設備/訂單/閱讀 | 列出或取得訂單 |
> | 動作 | 微軟.DataBoxEdge/DataBox邊緣設備/訂單/寫 | 建立或更新訂單 |
> | 動作 | 微軟.DataBoxEdge/DataBox邊緣設備/訂單/刪除 | 刪除訂單 |
> |  | **資料BoxEdge裝置/訂單/操作結果** |  |
> | 動作 | 微軟.DataBoxEdge/DataBox邊緣設備/訂單/操作結果/閱讀 | 列出或取得操作結果 |
> |  | **資料盒邊緣裝置/角色** |  |
> | 動作 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/read | 列出或抓取角色 |
> | 動作 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/read | 列出或抓取角色 |
> | 動作 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/write | 建立或更新角色 |
> | 動作 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/delete | 移除角色 |
> |  | **資料BoxEdge裝置/角色/操作結果** |  |
> | 動作 | 微軟.DataBoxEdge/DataBoxEdge 設備/角色/操作結果/閱讀 | 列出或取得操作結果 |
> |  | **資料盒邊緣裝置/安全設定** |  |
> | 動作 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/securitySettings/update/action | 更新安全性設定 |
> |  | **資料盒邊緣裝置/安全設定/操作結果** |  |
> | 動作 | 微軟.DataBoxEdge/DataBoxEdge 設備/安全設置/操作結果/讀取 | 列出或取得操作結果 |
> |  | **資料盒邊緣裝置/共用** |  |
> | 動作 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/read | 列出或取得共用 |
> | 動作 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/read | 列出或取得共用 |
> | 動作 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/write | 建立或更新共用 |
> | 動作 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/refresh/action | 使用雲端資料刷新共享中繼資料 |
> | 動作 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/delete | 刪除共用 |
> |  | **資料BoxEdge裝置/共用/操作結果** |  |
> | 動作 | 微軟.DataBoxEdge/DataBoxEdge設備/共用/操作結果/閱讀 | 列出或取得操作結果 |
> |  | **資料盒邊緣裝置/儲存帳戶認證** |  |
> | 動作 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/write | 建立或更新儲存體帳戶認證 |
> | 動作 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/read | 列出或取得儲存體帳戶認證 |
> | 動作 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/read | 列出或取得儲存體帳戶認證 |
> | 動作 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/delete | 刪除儲存體帳戶認證 |
> |  | **資料盒邊緣裝置/儲存帳戶認證/操作結果** |  |
> | 動作 | 微軟.DataBoxEdge/DataBoxEdge設備/存儲帳戶憑據/操作結果/讀取 | 列出或取得操作結果 |
> |  | **資料盒邊緣裝置/儲存帳戶** |  |
> | 動作 | 微軟.DataBoxEdge/DataBox邊緣設備/儲存帳戶/讀取 | 列出或取得儲存帳戶 |
> | 動作 | 微軟.DataBoxEdge/DataBox邊緣設備/儲存帳戶/讀取 | 列出或取得儲存帳戶 |
> | 動作 | 微軟.DataBoxEdge/DataBox邊緣設備/儲存帳戶/寫 | 建立或更新儲存帳戶 |
> | 動作 | 微軟.DataBoxEdge/DataBox邊緣設備/儲存帳戶/刪除 | 移除儲存帳戶 |
> |  | **資料盒邊緣裝置/儲存帳戶/容器** |  |
> | 動作 | 微軟.DataBoxEdge/DataBox邊緣設備/儲存帳戶/容器/讀取 | 列出或抓取容器 |
> | 動作 | 微軟.DataBoxEdge/DataBox邊緣設備/儲存帳戶/容器/讀取 | 列出或抓取容器 |
> | 動作 | 微軟.DataBoxEdge/DataBox邊緣設備/儲存帳戶/容器/寫入 | 建立或更新容器 |
> | 動作 | 微軟.DataBoxEdge/DataBox邊緣設備/儲存帳戶/容器/刪除 | 移除容器 |
> | 動作 | 微軟.DataBoxEdge/DataBoxEdge設備/儲存帳戶/容器/刷新/操作 | 使用雲端資料刷新容器中繼資料 |
> |  | **資料BoxEdge裝置/儲存帳戶/容器/操作結果** |  |
> | 動作 | 微軟.DataBoxEdge/DataBoxEdge設備/儲存帳戶/容器/操作結果/讀取 | 列出或取得操作結果 |
> |  | **資料盒邊緣裝置/儲存帳戶/操作結果** |  |
> | 動作 | 微軟.DataBoxEdge/DataBoxEdge 設備/儲存帳戶/操作結果/讀取 | 列出或取得操作結果 |
> |  | **資料盒邊緣裝置/觸發器** |  |
> | 動作 | 微軟.DataBoxEdge/DataBox邊緣設備/觸發器/讀取 | 列出或取得觸發器 |
> | 動作 | 微軟.DataBoxEdge/DataBox邊緣設備/觸發器/讀取 | 列出或取得觸發器 |
> | 動作 | 微軟.DataBoxEdge/DataBox邊緣設備/觸發器/寫入 | 建立或更新觸發器 |
> | 動作 | 微軟.DataBoxEdge/DataBox邊緣設備/觸發器/刪除 | 刪除觸發器 |
> |  | **資料盒邊緣裝置/觸發器/操作結果** |  |
> | 動作 | 微軟.DataBoxEdge/DataBoxEdge設備/觸發器/操作結果/閱讀 | 列出或取得操作結果 |
> |  | **資料盒邊緣裝置/更新摘要** |  |
> | 動作 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/updateSummary/read | 列出或取得更新摘要 |
> |  | **資料盒邊緣裝置/使用者** |  |
> | 動作 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/read | 列出或取得共用使用者 |
> | 動作 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/read | 列出或取得共用使用者 |
> | 動作 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/write | 建立或更新共用使用者 |
> | 動作 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/delete | 刪除共用使用者 |
> |  | **資料BoxEdge裝置/使用者/操作結果** |  |
> | 動作 | 微軟.DataBoxEdge/DataBoxEdge 設備/使用者/操作結果/閱讀 | 列出或取得操作結果 |
> |  | **斯克庫斯** |  |
> | 動作 | 微軟.DataBoxEdge/skus/讀取 | 列出或取得 SKU |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

Azure 服務[:Azure 資料塊](../azure-databricks/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.Databricks/register/action | 註冊 Databricks。 |
> |  | **位置** |  |
> | 動作 | 微軟.資料磚/位置/獲取網路策略/操作 | 根據 NRP 使用的位置抓取子網路的網路意圖政策 |
> |  | **位置/動作狀態** |  |
> | 動作 | 微軟.資料磚/位置/操作狀態/讀取 | 讀取資源的作業狀態。 |
> |  | **操作** |  |
> | 動作 | 微軟.資料磚/操作/讀取 | 取得作業的清單。 |
> |  | **workspaces** |  |
> | 動作 | Microsoft.Databricks/workspaces/read | 擷取 Databricks 工作區的清單。 |
> | 動作 | Microsoft.Databricks/workspaces/write | 建立 Databricks 工作區。 |
> | 動作 | Microsoft.Databricks/workspaces/delete | 移除 Databricks 工作區。 |
> | 動作 | 微軟.數據磚/工作區/刷新許可權/操作 | 工作區的刷新權限 |
> | 動作 | 微軟.數據磚/工作區/更新拒絕分配/操作 | 更新工作區的託管資源群組的拒絕分配而不是操作 |
> | 動作 | 微軟.數據磚/工作區/刷新工作區/操作 | 使用網址等新詳細資訊刷新工作區 |
> |  | **工作區/資料庫工作區** |  |
> | 動作 | 微軟.數據磚/工作區/db工作區/寫入 | 初始化資料磚塊工作區(僅限內部) |
> |  | **工作區/供應商/微軟.見解/診斷設置** |  |
> | 動作 | Microsoft.Databricks/workspaces/providers/Microsoft.Insights/diagnosticSettings/read | 設定 Databricks 工作區的可用診斷設定 |
> | 動作 | Microsoft.Databricks/workspaces/providers/Microsoft.Insights/diagnosticSettings/write | 新增或修改診斷設定。 |
> |  | **工作區/提供者/微軟.見解/日誌定義** |  |
> | 動作 | Microsoft.Databricks/workspaces/providers/Microsoft.Insights/logDefinitions/read | 取得 Databricks 工作區的可用記錄定義 |
> |  | **工作區/儲存加密** |  |
> | 動作 | 微軟.資料磚/工作空間/儲存加密/寫入 | 在 Databricks 工作區的託管儲存帳戶上啟用客戶託管金鑰加密 |
> | 動作 | 微軟.資料磚/工作區/儲存加密/刪除 | 關閉 Databricks 工作區的託管儲存帳戶上的客戶託管金鑰加密 |
> |  | **workspaces/virtualNetworkPeerings** |  |
> | 動作 | 微軟.數據磚/工作區/虛擬網路對等值/讀取 | 取得虛擬網路對等互連。 |
> | 動作 | 微軟.數據磚/工作區/虛擬網路對等互連/寫入 | 新增或修改虛擬網路對等互連 |
> | 動作 | 微軟.資料磚/工作區/虛擬網路對等/刪除 | 刪除虛擬網路對等互連 |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

Azure 服務:[資料目錄](../data-catalog/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.DataCatalog/register/action | 註冊資料目錄資源提供者的訂閱 |
> | 動作 | Microsoft.DataCatalog/unregister/action | 取消註冊資料目錄資源提供者的訂閱 |
> |  | **catalogs** |  |
> | 動作 | Microsoft.DataCatalog/catalogs/read | 讀取資料目錄資源提供程式的目錄資源。 |
> | 動作 | Microsoft.DataCatalog/catalogs/write | 為數據目錄資源提供程式編寫目錄資源。 |
> | 動作 | Microsoft.DataCatalog/catalogs/delete | 刪除資料目錄資源提供程式的目錄資源。 |
> |  | **檢查名稱可用性** |  |
> | 動作 | 微軟.資料目錄/檢查名稱可用性/讀取 | 檢查資料目錄資源提供程式的目錄名稱可用性。 |
> |  | **資料目錄** |  |
> | 動作 | 微軟資料目錄/資料目錄/讀取 | 讀取資料目錄資源提供程式的數據目錄資源。 |
> | 動作 | 微軟資料目錄/資料目錄/寫入 | 為數據目錄資源提供程式編寫數據目錄資源。 |
> | 動作 | 微軟資料目錄/資料目錄/刪除 | 刪除資料目錄資源提供程式的資料目錄資源。 |
> |  | **操作** |  |
> | 動作 | Microsoft.DataCatalog/operations/read | 讀取資料目錄資源提供程式中的所有可用操作。 |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

Azure 服務:[資料工廠](../data-factory/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.DataFactory/register/action | 為 Data Factory 資源提供者註冊訂用帳戶。 |
> | 動作 | Microsoft.DataFactory/unregister/action | 為 Data Factory 資源提供者取消註冊訂用帳戶。 |
> |  | **檢查azure資料工廠名稱可用性** |  |
> | 動作 | Microsoft.DataFactory/checkazuredatafactorynameavailability/read | 檢查 Data Factory 名稱是否可供使用。 |
> |  | **datafactories** |  |
> | 動作 | Microsoft.DataFactory/datafactories/read | 讀取 Data Factory。 |
> | 動作 | Microsoft.DataFactory/datafactories/write | 建立或更新 Data Factory。 |
> | 動作 | Microsoft.DataFactory/datafactories/delete | 刪除 Data Factory。 |
> |  | **資料工廠/活動視窗** |  |
> | 動作 | Microsoft.DataFactory/datafactories/activitywindows/read | 使用指定參數讀取 Data Factory 中的活動視窗。 |
> |  | **資料工廠/資料管道** |  |
> | 動作 | Microsoft.DataFactory/datafactories/datapipelines/read | 讀取任何管線。 |
> | 動作 | Microsoft.DataFactory/datafactories/datapipelines/delete | 刪除任何管線。 |
> | 動作 | Microsoft.DataFactory/datafactories/datapipelines/pause/action | 暫停任何管線。 |
> | 動作 | Microsoft.DataFactory/datafactories/datapipelines/resume/action | 繼續任何管線。 |
> | 動作 | Microsoft.DataFactory/datafactories/datapipelines/update/action | 更新任何管線。 |
> | 動作 | Microsoft.DataFactory/datafactories/datapipelines/write | 建立或更新任何管線。 |
> |  | **資料工廠/資料導管/活動/活動視窗** |  |
> | 動作 | Microsoft.DataFactory/datafactories/datapipelines/activities/activitywindows/read | 使用指定參數讀取管線活動的活動視窗。 |
> |  | **資料工廠/資料導管/活動視窗** |  |
> | 動作 | Microsoft.DataFactory/datafactories/datapipelines/activitywindows/read | 使用指定參數讀取管線的活動視窗。 |
> |  | **資料工廠/資料集** |  |
> | 動作 | Microsoft.DataFactory/datafactories/datasets/read | 讀取任何資料集。 |
> | 動作 | Microsoft.DataFactory/datafactories/datasets/delete | 刪除任何資料集。 |
> | 動作 | Microsoft.DataFactory/datafactories/datasets/write | 建立或更新任何資料集。 |
> |  | **資料工廠/資料集/活動視窗** |  |
> | 動作 | Microsoft.DataFactory/datafactories/datasets/activitywindows/read | 使用指定參數讀取資料集的活動視窗。 |
> |  | **資料工廠/資料集/切片執行** |  |
> | 動作 | Microsoft.DataFactory/datafactories/datasets/sliceruns/read | 針對具有指定開始時間的指定資料集讀取資料配量執行。 |
> |  | **資料工廠/資料集/切片** |  |
> | 動作 | Microsoft.DataFactory/datafactories/datasets/slices/read | 取得指定期間內的資料配量。 |
> | 動作 | Microsoft.DataFactory/datafactories/datasets/slices/write | 更新資料配量的狀態。 |
> |  | **資料工廠/閘道** |  |
> | 動作 | Microsoft.DataFactory/datafactories/gateways/read | 讀取任何閘道。 |
> | 動作 | Microsoft.DataFactory/datafactories/gateways/write | 建立或更新任何閘道。 |
> | 動作 | Microsoft.DataFactory/datafactories/gateways/delete | 刪除任何閘道。 |
> | 動作 | Microsoft.DataFactory/datafactories/gateways/connectioninfo/action | 讀取任何閘道的連線資訊。 |
> | 動作 | Microsoft.DataFactory/datafactories/gateways/listauthkeys/action | 列出任何閘道的驗證金鑰。 |
> | 動作 | Microsoft.DataFactory/datafactories/gateways/regenerateauthkey/action | 重新產生任何閘道的驗證金鑰。 |
> |  | **資料工廠/連結服務** |  |
> | 動作 | Microsoft.DataFactory/datafactories/linkedServices/read | 讀取任何連結服務。 |
> | 動作 | Microsoft.DataFactory/datafactories/linkedServices/delete | 刪除任何連結服務。 |
> | 動作 | Microsoft.DataFactory/datafactories/linkedServices/write | 建立或更新任何連結服務。 |
> |  | **資料工廠/執行/記錄資訊** |  |
> | 動作 | Microsoft.DataFactory/datafactories/runs/loginfo/read | 讀取包含記錄的 Blob 容器 SAS URI。 |
> |  | **資料工廠/表** |  |
> | 動作 | Microsoft.DataFactory/datafactories/tables/read | 讀取任何資料集。 |
> | 動作 | Microsoft.DataFactory/datafactories/tables/delete | 刪除任何資料集。 |
> | 動作 | Microsoft.DataFactory/datafactories/tables/write | 建立或更新任何資料集。 |
> |  | **factories** |  |
> | 動作 | Microsoft.DataFactory/factories/read | 讀取 Data Factory。 |
> | 動作 | Microsoft.DataFactory/factories/write | 建立或更新 Data Factory |
> | 動作 | Microsoft.DataFactory/factories/delete | 刪除 Data Factory。 |
> | 動作 | 微軟.資料工廠/工廠/創建資料流調試會話/操作 | 建立資料流調試工作階段。 |
> | 動作 | 微軟.資料工廠/工廠/啟動數據流調試會話/操作 | 啟動數據流調試會話。 |
> | 動作 | 微軟.資料工廠/工廠/添加資料流到調試會話/操作 | 將數據流添加到調試會話以進行預覽。 |
> | 動作 | 微軟.資料工廠/工廠/執行資料流調試命令/操作 | 執行數據流調試命令。 |
> | 動作 | 微軟.資料工廠/工廠/刪除資料流調試會話/操作 | 刪除資料流程除錯工作階段。 |
> | 動作 | 微軟.資料工廠/工廠/查詢數據流調試/操作 | 查詢數據流調試會話。 |
> | 動作 | Microsoft.DataFactory/factories/cancelpipelinerun/action | 取消執行識別碼所指定的管線執行。 |
> | 動作 | 微軟.資料工廠/工廠/取消沙箱管道運行/操作 | 取消管道的調試運行。 |
> | 動作 | 微軟.資料工廠/工廠/沙箱管道運行/操作 | 查詢偵錯管線執行。 |
> | 動作 | 微軟.資料工廠/工廠/查詢觸發器/操作 | 查詢觸發器。 |
> | 動作 | 微軟.資料工廠/工廠/獲取功能價值/操作 | 取得特定位置的暴露風險控制特徵值。 |
> | 動作 | 微軟.資料工廠/工廠/獲取數據平面訪問/操作 | 取得 ADF DataPlane 服務的存取權。 |
> | 動作 | Microsoft.DataFactory/factories/getGitHubAccessToken/action | 取得 GitHub 存取權杖。 |
> | 動作 | Microsoft.DataFactory/factories/querytriggerruns/action | 查詢觸發程序執行。 |
> | 動作 | Microsoft.DataFactory/factories/querypipelineruns/action | 查詢管線執行。 |
> | 動作 | Microsoft.DataFactory/factories/querydebugpipelineruns/action | 查詢偵錯管線執行。 |
> |  | **工廠/資料流程** |  |
> | 動作 | 微軟.資料工廠/工廠/資料流/讀取 | 讀取數據流。 |
> | 動作 | 微軟.資料工廠/工廠/資料流/刪除 | 刪除資料流。 |
> | 動作 | 微軟.資料工廠/工廠/資料流/寫入 | 建立或更新資料串流 |
> |  | **工廠/資料集** |  |
> | 動作 | Microsoft.DataFactory/factories/datasets/read | 讀取任何資料集。 |
> | 動作 | Microsoft.DataFactory/factories/datasets/delete | 刪除任何資料集。 |
> | 動作 | Microsoft.DataFactory/factories/datasets/write | 建立或更新任何資料集。 |
> |  | **工廠/除錯導管執行** |  |
> | 動作 | 微軟.資料工廠/工廠/調試管道運行/取消/操作 | 取消管道的調試運行。 |
> |  | **工廠/取得資料平面存取** |  |
> | 動作 | 微軟.資料工廠/工廠/獲取數據平面訪問/讀取 | 讀取 ADF DataPlane 服務的存取權。 |
> |  | **工廠/取得功能價值** |  |
> | 動作 | 微軟.資料工廠/工廠/獲取功能價值/讀取 | 讀取特定位置的暴露風險控制特徵值。 |
> |  | **工廠/整合執行時間** |  |
> | 動作 | Microsoft.DataFactory/factories/integrationruntimes/read | 讀取任何 Integration Runtime。 |
> | 動作 | Microsoft.DataFactory/factories/integrationruntimes/write | 建立或更新任何 Integration Runtime。 |
> | 動作 | Microsoft.DataFactory/factories/integrationruntimes/delete | 刪除任何 Integration Runtime。 |
> | 動作 | Microsoft.DataFactory/factories/integrationruntimes/start/action | 取動任何 Integration Runtime。 |
> | 動作 | Microsoft.DataFactory/factories/integrationruntimes/stop/action | 停止任何 Integration Runtime。 |
> | 動作 | Microsoft.DataFactory/factories/integrationruntimes/synccredentials/action | 同步處理所指定 Integration Runtime 的認證。 |
> | 動作 | Microsoft.DataFactory/factories/integrationruntimes/upgrade/action | 將指定的 Integration Runtime 升級。 |
> | 動作 | Microsoft.DataFactory/factories/integrationruntimes/regenerateauthkey/action | 重新產生所指定 Integration Runtime 的驗證金鑰。 |
> | 動作 | Microsoft.DataFactory/factories/integrationruntimes/removelinks/action | 從指定的 Integration Runtime 中移除連結的 Integration Runtime 參考。 |
> | 動作 | Microsoft.DataFactory/factories/integrationruntimes/linkedIntegrationRuntime/action | 在指定的共用 Integration Runtime 上建立連結的 Integration Runtime 參考。 |
> | 動作 | Microsoft.DataFactory/factories/integrationruntimes/getObjectMetadata/action | 取得所指定 Integration Runtime 的 SSIS Integration Runtime 中繼資料。 |
> | 動作 | Microsoft.DataFactory/factories/integrationruntimes/refreshObjectMetadata/action | 重新整理所指定 Integration Runtime 的 SSIS Integration Runtime 中繼資料。 |
> |  | **工廠/整合執行時間/取得連接資訊** |  |
> | 動作 | Microsoft.DataFactory/factories/integrationruntimes/getconnectioninfo/read | 讀取 Integration Runtime 連線資訊。 |
> |  | **工廠/整合執行時間/取得狀態** |  |
> | 動作 | Microsoft.DataFactory/factories/integrationruntimes/getstatus/read | 讀取 Integration Runtime 狀態。 |
> |  | **工廠/整合時間/清單鍵** |  |
> | 動作 | Microsoft.DataFactory/factories/integrationruntimes/listauthkeys/read | 列出任何 Integration Runtime 的驗證金鑰。 |
> |  | **工廠/整合執行時間/監控資料** |  |
> | 動作 | Microsoft.DataFactory/factories/integrationruntimes/monitoringdata/read | 取得任何 Integration Runtime 的監視資料。 |
> |  | **工廠/整合執行時間/節點** |  |
> | 動作 | Microsoft.DataFactory/factories/integrationruntimes/nodes/read | 讀取所指定 Integration Runtime 的節點。 |
> | 動作 | Microsoft.DataFactory/factories/integrationruntimes/nodes/delete | 刪除所指定 Integration Runtime 的節點。 |
> | 動作 | Microsoft.DataFactory/factories/integrationruntimes/nodes/write | 更新自我裝載的 Integration Runtime 節點。 |
> | 動作 | Microsoft.DataFactory/factories/integrationruntimes/nodes/ipAddress/action | 傳回 Integration Runtime 所指定節點的 IP 位址。 |
> |  | **工廠/連結服務** |  |
> | 動作 | Microsoft.DataFactory/factories/linkedServices/read | 讀取連結服務。 |
> | 動作 | Microsoft.DataFactory/factories/linkedServices/delete | 刪除連結服務。 |
> | 動作 | Microsoft.DataFactory/factories/linkedServices/write | 建立或更新連結服務 |
> |  | **工廠/運營結果** |  |
> | 動作 | 微軟.資料工廠/工廠/運營結果/閱讀 | 獲取操作結果。 |
> |  | **工廠/導管執行** |  |
> | 動作 | Microsoft.DataFactory/factories/pipelineruns/read | 讀取管線執行。 |
> | 動作 | Microsoft.DataFactory/factories/pipelineruns/cancel/action | 取消執行識別碼所指定的管線執行。 |
> | 動作 | Microsoft.DataFactory/factories/pipelineruns/queryactivityruns/action | 查詢所指定管線執行識別碼的活動執行。 |
> |  | **工廠/導管執行/活動執行** |  |
> | 動作 | Microsoft.DataFactory/factories/pipelineruns/activityruns/read | 讀取所指定管線執行識別碼的活動執行。 |
> |  | **工廠/導管執行/查詢活動執行** |  |
> | 動作 | Microsoft.DataFactory/factories/pipelineruns/queryactivityruns/read | 讀取所指定管線執行識別碼的查詢活動執行結果。 |
> |  | **工廠/導管** |  |
> | 動作 | Microsoft.DataFactory/factories/pipelines/read | 讀取管線。 |
> | 動作 | Microsoft.DataFactory/factories/pipelines/delete | 刪除管線。 |
> | 動作 | Microsoft.DataFactory/factories/pipelines/write | 建立或更新管線 |
> | 動作 | Microsoft.DataFactory/factories/pipelines/createrun/action | 建立管線的執行。 |
> | 動作 | 微軟.數據工廠/工廠/管道/沙箱/行動 | 為管道創建調試運行環境。 |
> |  | **工廠/導管/導管執行** |  |
> | 動作 | Microsoft.DataFactory/factories/pipelines/pipelineruns/read | 讀取管線執行。 |
> |  | **工廠/導管/導管執行/活動執行/進度** |  |
> | 動作 | Microsoft.DataFactory/factories/pipelines/pipelineruns/activityruns/progress/read | 取得活動執行的進度。 |
> |  | **工廠/導管/沙箱** |  |
> | 動作 | 微軟.資料工廠/工廠/管道/沙箱/創建/操作 | 為管道創建調試運行環境。 |
> | 動作 | 微軟.資料工廠/工廠/管道/沙箱/運行/操作 | 為管道創建調試運行。 |
> |  | **工廠/查詢管道執行** |  |
> | 動作 | Microsoft.DataFactory/factories/querypipelineruns/read | 讀取查詢管線執行的結果。 |
> |  | **工廠/查詢觸發器** |  |
> | 動作 | Microsoft.DataFactory/factories/querytriggerruns/read | 讀取觸發程序執行的結果。 |
> |  | **工廠/沙箱管道執行** |  |
> | 動作 | 微軟.資料工廠/工廠/沙箱管道運行/讀取 | 獲取管道的調試運行資訊。 |
> |  | **工廠/沙箱管道運行/沙箱活動運行** |  |
> | 動作 | 微軟.資料工廠/工廠/沙箱管道運行/沙盒活動運行/讀取 | 獲取活動的調試運行資訊。 |
> |  | **工廠/觸發器** |  |
> | 動作 | Microsoft.DataFactory/factories/triggerruns/read | 讀取觸發程序執行。 |
> |  | **工廠/觸發器** |  |
> | 動作 | Microsoft.DataFactory/factories/triggers/read | 讀取任何觸發程序。 |
> | 動作 | Microsoft.DataFactory/factories/triggers/write | 建立或更新任何觸發程序。 |
> | 動作 | Microsoft.DataFactory/factories/triggers/delete | 刪除任何觸發程序。 |
> | 動作 | 微軟.資料工廠/工廠/觸發器/訂閱事件/操作 | 訂閱活動。 |
> | 動作 | 微軟.資料工廠/工廠/觸發器/獲取事件訂閱狀態/操作 | 事件訂閱狀態。 |
> | 動作 | 微軟.資料工廠/工廠/觸發器/取消訂閱事件/操作 | 取消訂閱事件。 |
> | 動作 | Microsoft.DataFactory/factories/triggers/start/action | 啟動任何觸發程序。 |
> | 動作 | Microsoft.DataFactory/factories/triggers/stop/action | 停止任何觸發程序。 |
> |  | **工廠/觸發器/觸發器** |  |
> | 動作 | Microsoft.DataFactory/factories/triggers/triggerruns/read | 讀取觸發程序執行。 |
> |  | **位置** |  |
> | 動作 | Microsoft.DataFactory/locations/configureFactoryRepo/action | 設定中心的存放庫。 |
> | 動作 | Microsoft.DataFactory/locations/getFeatureValue/action | 取得特定位置的暴露風險控制特徵值。 |
> |  | **位置/取得功能值** |  |
> | 動作 | Microsoft.DataFactory/locations/getFeatureValue/read | 讀取特定位置的暴露風險控制特徵值。 |
> |  | **操作** |  |
> | 動作 | Microsoft.DataFactory/operations/read | 讀取 Microsoft Data Factory 提供者中的所有作業。 |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

Azure 服務:[資料湖分析](../data-lake-analytics/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.DataLakeAnalytics/register/action | 向 DataLakeAnalytics 註冊訂用帳戶。 |
> |  | **帳戶** |  |
> | 動作 | Microsoft.DataLakeAnalytics/accounts/read | 取得現有 DataLakeAnalytics 帳戶的相關資訊。 |
> | 動作 | Microsoft.DataLakeAnalytics/accounts/write | 建立或更新 DataLakeAnalytics 帳戶。 |
> | 動作 | Microsoft.DataLakeAnalytics/accounts/delete | 刪除 DataLakeAnalytics 帳戶。 |
> | 動作 | Microsoft.DataLakeAnalytics/accounts/transferAnalyticsUnits/action | 在 DataLakeAnalytics 帳戶之間轉送 SystemMaxAnalyticsUnits。 |
> | 動作 | Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action | 授與權限以取消其他使用者所提交的作業。 |
> |  | **帳號/計算原則** |  |
> | 動作 | Microsoft.DataLakeAnalytics/accounts/computePolicies/read | 取得計算原則的相關資訊。 |
> | 動作 | Microsoft.DataLakeAnalytics/accounts/computePolicies/write | 建立或更新計算原則。 |
> | 動作 | Microsoft.DataLakeAnalytics/accounts/computePolicies/delete | 刪除計算原則。 |
> |  | **accounts/dataLakeStoreAccounts** |  |
> | 動作 | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/read | 取得 DataLakeAnalytics 帳戶所連結之 DataLakeStore 帳戶的相關資訊。 |
> | 動作 | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/write | 建立或更新 DataLakeAnalytics 帳戶所連結的 DataLakeStore 帳戶。 |
> | 動作 | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/delete | 取消 DataLakeStore 帳戶與 DataLakeAnalytics 帳戶的連結。 |
> |  | **accounts/firewallRules** |  |
> | 動作 | Microsoft.DataLakeAnalytics/accounts/firewallRules/read | 取得防火牆規則的相關資訊。 |
> | 動作 | Microsoft.DataLakeAnalytics/accounts/firewallRules/write | 建立或更新防火牆規則。 |
> | 動作 | Microsoft.DataLakeAnalytics/accounts/firewallRules/delete | 刪除防火牆規則。 |
> |  | **帳號/動作結果** |  |
> | 動作 | Microsoft.DataLakeAnalytics/accounts/operationResults/read | 取得 DataLakeAnalytics 帳戶作業的結果。 |
> |  | **accounts/storageAccounts** |  |
> | 動作 | Microsoft.DataLakeAnalytics/accounts/storageAccounts/read | 取得 DataLakeAnalytics 帳戶所連結之儲存體帳戶的相關資訊。 |
> | 動作 | Microsoft.DataLakeAnalytics/accounts/storageAccounts/write | 建立或更新 DataLakeAnalytics 帳戶所連結的儲存體帳戶。 |
> | 動作 | Microsoft.DataLakeAnalytics/accounts/storageAccounts/delete | 取消儲存體帳戶與 DataLakeAnalytics 帳戶的連結。 |
> |  | **帳戶/儲存帳戶/容器** |  |
> | 動作 | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers/read | 取得 DataLakeAnalytics 帳戶所連結之儲存體帳戶的容器。 |
> | 動作 | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers/listSasTokens/action | 針對 DataLakeAnalytics 帳戶所連結之儲存體帳戶的儲存體容器列出 SAS 權杖。 |
> |  | **帳號/虛擬網路規則** |  |
> | 動作 | 微軟.DataLake分析/帳戶/虛擬網路規則/閱讀 | 獲取有關虛擬網路規則的資訊。 |
> | 動作 | 微軟.DataLake分析/帳戶/虛擬網路規則/寫入 | 創建或更新虛擬網路規則。 |
> | 動作 | 微軟.DataLake分析/帳戶/虛擬網路規則/刪除 | 刪除虛擬網路規則。 |
> |  | **位置** |  |
> | 動作 | Microsoft.DataLakeAnalytics/locations/checkNameAvailability/action | 檢查 DataLakeAnalytics 帳戶名稱的可用性。 |
> |  | **位置/功能** |  |
> | 動作 | Microsoft.DataLakeAnalytics/locations/capability/read | 取得訂用帳戶中關於使用 DataLakeAnalytics 的功能資訊。 |
> |  | **位置/動作結果** |  |
> | 動作 | Microsoft.DataLakeAnalytics/locations/operationResults/read | 取得 DataLakeAnalytics 帳戶作業的結果。 |
> |  | **位置/用法** |  |
> | 動作 | Microsoft.DataLakeAnalytics/locations/usages/read | 取得訂用帳戶中關於使用 DataLakeAnalytics 的配額使用量資訊。 |
> |  | **操作** |  |
> | 動作 | Microsoft.DataLakeAnalytics/operations/read | 取得 DataLakeAnalytics 的可用作業。 |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

Azure 服務[:Azure 資料湖儲存](../storage/blobs/data-lake-storage-introduction.md)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.DataLakeStore/register/action | 向 DataLakeStore 註冊訂用帳戶。 |
> |  | **帳戶** |  |
> | 動作 | Microsoft.DataLakeStore/accounts/read | 取得現有 DataLakeStore 帳戶的相關資訊。 |
> | 動作 | Microsoft.DataLakeStore/accounts/write | 建立或更新 DataLakeStore 帳戶。 |
> | 動作 | Microsoft.DataLakeStore/accounts/delete | 刪除 DataLakeStore 帳戶。 |
> | 動作 | Microsoft.DataLakeStore/accounts/enableKeyVault/action | 針對 DataLakeStore 帳戶啟用 KeyVault。 |
> | 動作 | Microsoft.DataLakeStore/accounts/Superuser/action | 使用 Microsoft.Authorization/roleAssignments/write 授與時，授與 Data Lake Store 的超級使用者。 |
> |  | **accounts/eventGridFilters** |  |
> | 動作 | Microsoft.DataLakeStore/accounts/eventGridFilters/read | 取得 EventGrid 篩選。 |
> | 動作 | Microsoft.DataLakeStore/accounts/eventGridFilters/write | 建立或更新 EventGrid 篩選。 |
> | 動作 | Microsoft.DataLakeStore/accounts/eventGridFilters/delete | 刪除 EventGrid 篩選。 |
> |  | **accounts/firewallRules** |  |
> | 動作 | Microsoft.DataLakeStore/accounts/firewallRules/read | 取得防火牆規則的相關資訊。 |
> | 動作 | Microsoft.DataLakeStore/accounts/firewallRules/write | 建立或更新防火牆規則。 |
> | 動作 | Microsoft.DataLakeStore/accounts/firewallRules/delete | 刪除防火牆規則。 |
> |  | **帳號/動作結果** |  |
> | 動作 | Microsoft.DataLakeStore/accounts/operationResults/read | 取得 DataLakeStore 帳戶作業的結果。 |
> |  | **帳戶/股份** |  |
> | 動作 | 微軟.DataLakeStore/帳戶/共用/讀取 | 獲取有關共享的資訊。 |
> | 動作 | 微軟.DataLakeStore/帳戶/股票/寫入 | 創建或更新共用。 |
> | 動作 | 微軟.DataLakeStore/帳戶/共用/刪除 | 刪除共用。 |
> |  | **帳號/受信任的 IdProvider** |  |
> | 動作 | Microsoft.DataLakeStore/accounts/trustedIdProviders/read | 取得所信任識別提供者的相關資訊。 |
> | 動作 | Microsoft.DataLakeStore/accounts/trustedIdProviders/write | 建立或更新所信任的識別提供者。 |
> | 動作 | Microsoft.DataLakeStore/accounts/trustedIdProviders/delete | 刪除所信任的識別提供者。 |
> |  | **帳號/虛擬網路規則** |  |
> | 動作 | 微軟.DataLakeStore/帳戶/虛擬網路規則/讀取 | 獲取有關虛擬網路規則的資訊。 |
> | 動作 | 微軟.DataLakeStore/帳戶/虛擬網路規則/寫入 | 創建或更新虛擬網路規則。 |
> | 動作 | 微軟.DataLakeStore/帳戶/虛擬網路規則/刪除 | 刪除虛擬網路規則。 |
> |  | **位置** |  |
> | 動作 | Microsoft.DataLakeStore/locations/checkNameAvailability/action | 檢查 DataLakeStore 帳戶名稱的可用性。 |
> |  | **位置/功能** |  |
> | 動作 | Microsoft.DataLakeStore/locations/capability/read | 取得訂用帳戶中關於使用 DataLakeStore 的功能資訊。 |
> |  | **位置/動作結果** |  |
> | 動作 | Microsoft.DataLakeStore/locations/operationResults/read | 取得 DataLakeStore 帳戶作業的結果。 |
> |  | **位置/用法** |  |
> | 動作 | Microsoft.DataLakeStore/locations/usages/read | 取得訂用帳戶中關於使用 DataLakeStore 的配額使用量資訊。 |
> |  | **操作** |  |
> | 動作 | Microsoft.DataLakeStore/operations/read | 取得 DataLakeStore 的可用作業。 |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

Azure 服務[:Azure 資料庫遷移服務](../dms/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.DataMigration/register/action | 向「Azure 資料庫移轉服務」提供者註冊訂用帳戶 |
> |  | **位置/動作結果** |  |
> | 動作 | Microsoft.DataMigration/locations/operationResults/read | 取得與「202 已接受」回應相關且長時間執行之作業的狀態 |
> |  | **位置/動作狀態** |  |
> | 動作 | Microsoft.DataMigration/locations/operationStatuses/read | 取得與「202 已接受」回應相關且長時間執行之作業的狀態 |
> |  | **服務** |  |
> | 動作 | Microsoft.DataMigration/services/read | 閱讀資源的相關資訊 |
> | 動作 | Microsoft.DataMigration/services/write | 建立或更新資源及其屬性 |
> | 動作 | Microsoft.DataMigration/services/delete | 刪除資源及其所有子系 |
> | 動作 | Microsoft.DataMigration/services/stop/action | 停止 DMS 服務以將其成本降到最低 |
> | 動作 | Microsoft.DataMigration/services/start/action | 啟動 DMS 服務以讓它再次處理移轉 |
> | 動作 | Microsoft.DataMigration/services/checkStatus/action | 檢查服務是否已部署且在執行中 |
> | 動作 | 微軟.資料移轉/服務/設定輔助角色/操作 | 將 DMS 工作人員設定為服務的可用工作人員 |
> | 動作 | 微軟.資料遷移/服務/添加輔助角色/操作 | 將 DMS 工作人員新增到服務中可用的工作人員 |
> | 動作 | 微軟.資料遷移/服務/刪除工作人員/操作 | 將 DMS 工作人員移除給服務可用的工作人員 |
> | 動作 | 微軟.資料移轉/服務/更新代理設定/操作 | 使用提供的值更新 DMS 代理配置。 |
> | 動作 | 微軟.資料遷移/服務/獲取混合下載連結/操作 | 從 RP Blob 儲存獲取 DMS 工作程式包下載連結。 |
> |  | **services/projects** |  |
> | 動作 | Microsoft.DataMigration/services/projects/read | 閱讀資源的相關資訊 |
> | 動作 | Microsoft.DataMigration/services/projects/write | 執行工作「Azure 資料庫移轉服務」工作 |
> | 動作 | Microsoft.DataMigration/services/projects/delete | 刪除資源及其所有子系 |
> | 動作 | Microsoft.DataMigration/services/projects/accessArtifacts/action | 產生可用來對專案成品進行 GET 或 PUT 的 URL |
> |  | **服務/專案/工作** |  |
> | 動作 | Microsoft.DataMigration/services/projects/tasks/read | 閱讀資源的相關資訊 |
> | 動作 | Microsoft.DataMigration/services/projects/tasks/write | 執行工作「Azure 資料庫移轉服務」工作 |
> | 動作 | Microsoft.DataMigration/services/projects/tasks/delete | 刪除資源及其所有子系 |
> | 動作 | Microsoft.DataMigration/services/projects/tasks/cancel/action | 取消目前正在執行的工作 |
> |  | **服務/服務工作** |  |
> | 動作 | 微軟.資料遷移/服務/服務任務/讀取 | 閱讀資源的相關資訊 |
> | 動作 | 微軟.資料遷移/服務/服務任務/寫入 | 執行工作「Azure 資料庫移轉服務」工作 |
> | 動作 | 微軟.資料遷移/服務/服務任務/刪除 | 刪除資源及其所有子系 |
> | 動作 | 微軟.資料遷移/服務/服務任務/取消/操作 | 取消目前正在執行的工作 |
> |  | **服務/ 插槽** |  |
> | 動作 | Microsoft.DataMigration/services/slots/read | 閱讀資源的相關資訊 |
> | 動作 | Microsoft.DataMigration/services/slots/write | 建立或更新資源及其屬性 |
> | 動作 | Microsoft.DataMigration/services/slots/delete | 刪除資源及其所有子系 |
> |  | **斯克庫斯** |  |
> | 動作 | Microsoft.DataMigration/skus/read | 取得 DMS 資源所支援的 SKU 清單。 |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

Azure 服務[:MariaDB 的 Azure 資料庫](../mariadb/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | 微軟.DBforMariaDB/註冊/行動 | 註冊 MariaDB 資源提供者 |
> | 動作 | 微軟.DBforMariaDB/檢查名稱可用性/操作 | 確認指定的伺服器名稱是否可用來針對指定的訂用帳戶進行全球佈建。 |
> |  | **位置/azure 同步操作** |  |
> | 動作 | 微軟.DBforMariaDB/位置/azureAsync操作/讀取 | 傳回 MariaDB 伺服器動作結果 |
> |  | **位置/動作結果** |  |
> | 動作 | 微軟.DBforMariaDB/位置/操作結果/閱讀 | 傳回基於資源群組的 MariaDB 伺服器操作結果 |
> | 動作 | 微軟.DBforMariaDB/位置/操作結果/閱讀 | 傳回 MariaDB 伺服器動作結果 |
> |  | **位置/效能層** |  |
> | 動作 | Microsoft.DBforMariaDB/locations/performanceTiers/read | 傳回可用的效能層級清單。 |
> |  | **位置/專用端點連接Azure同步操作** |  |
> | 動作 | 微軟.DBforMariaDB/位置/私人端接Azure同步操作/讀取 | 取得專用終結點連線操作的結果 |
> |  | **位置/專用端點連線操作結果** |  |
> | 動作 | 微軟.DBforMariaDB/位置/私人端點連接操作結果/讀取 | 取得專用終結點連線操作的結果 |
> |  | **位置/專用端點連線代理Azure同步操作** |  |
> | 動作 | 微軟.DBforMariaDB/位置/專用端接代理Azure同步操作/讀取 | 取得專用終結點連線代理操作的結果 |
> |  | **位置/專用端接連線操作結果** |  |
> | 動作 | 微軟.DBforMariaDB/位置/私人端接連接操作結果/讀取 | 取得專用終結點連線代理操作的結果 |
> |  | **位置/安全警報策略Azure同步操作** |  |
> | 動作 | 微軟.DBforMariaDB/位置/安全警報策略Azure同步操作/讀取 | 返回伺服器威脅檢測操作結果的清單。 |
> |  | **位置/安全警報策略操作結果** |  |
> | 動作 | 微軟.DBforMariaDB/位置/安全警報策略操作結果/讀取 | 返回伺服器威脅檢測操作結果的清單。 |
> |  | **位置/伺服器鍵Azure同步操作** |  |
> | 動作 | 微軟.DBforMariaDB/位置/伺服器鍵Azure同步操作/讀取 | 取得對透明資料加密伺服器金鑰的正在進行的作業 |
> |  | **位置/伺服器鍵操作結果** |  |
> | 動作 | 微軟.DBforMariaDB/位置/伺服器鍵操作結果/讀取 | 取得對透明資料加密伺服器金鑰的正在進行的作業 |
> |  | **操作** |  |
> | 動作 | 微軟.DBforMariaDB/操作/讀 | 返回 MariaDB 操作的清單。 |
> |  | **效能等級** |  |
> | 動作 | Microsoft.DBforMariaDB/performanceTiers/read | 傳回可用的效能層級清單。 |
> |  | **伺服器** |  |
> | 動作 | 微軟.DBforMariaDB/伺服器/查詢文本/操作 | 回覆查詢清單的文字 |
> | 動作 | 微軟.DBforMariaDB/伺服器/查詢文本/操作 | 傳回查詢的文字 |
> | 動作 | Microsoft.DBforMariaDB/servers/read | 傳回伺服器清單，或取得指定伺服器的屬性。 |
> | 動作 | Microsoft.DBforMariaDB/servers/write | 使用指定參數建立伺服器，或更新指定伺服器的屬性或標記。 |
> | 動作 | Microsoft.DBforMariaDB/servers/delete | 刪除現有伺服器。 |
> | 動作 | 微軟.DBforMariaDB/伺服器/重新啟動/操作 | 重新啟動特定伺服器。 |
> | 動作 | Microsoft.DBforMariaDB/servers/updateConfigurations/action | 更新指定伺服器的組態 |
> |  | **servers/administrators** |  |
> | 動作 | 微軟.DBforMariaDB/伺服器/管理員/閱讀 | 取得 MariaDB 伺服器管理員的清單。 |
> | 動作 | 微軟.DBforMariaDB/伺服器/管理員/寫入 | 使用指定的參數創建或更新 MariaDB 伺服器管理員。 |
> | 動作 | 微軟.DBforMariaDB/伺服器/管理員/刪除 | 刪除 MariaDB 伺服器的現有管理員。 |
> |  | **servers/advisors** |  |
> | 動作 | 微軟.DBforMariaDB/伺服器/顧問/閱讀 | 傳回建議程式清單 |
> | 動作 | 微軟.DBforMariaDB/伺服器/顧問/閱讀 | 返回顧問 |
> | 動作 | 微軟.DBforMariaDB/伺服器/顧問/創建推薦行動會話/行動 | 建立新的建議操作工作階段 |
> |  | **伺服器/顧問/建議的操作** |  |
> | 動作 | 微軟.DBforMariaDB/伺服器/顧問/推薦操作/閱讀 | 傳回建議的動作清單 |
> | 動作 | 微軟.DBforMariaDB/伺服器/顧問/推薦操作/閱讀 | 傳回建議的動作清單 |
> | 動作 | 微軟.DBforMariaDB/伺服器/顧問/推薦操作/閱讀 | 回傳回建議的作業 |
> |  | **servers/configurations** |  |
> | 動作 | Microsoft.DBforMariaDB/servers/configurations/read | 傳回伺服器的組態清單，或取得指定組態的屬性。 |
> | 動作 | Microsoft.DBforMariaDB/servers/configurations/write | 更新指定組態的值 |
> |  | **servers/databases** |  |
> | 動作 | 微軟.DBforMariaDB/伺服器/資料庫/讀取 | 返回 MariaDB 資料庫的清單或獲取指定資料庫的屬性。 |
> | 動作 | 微軟.DBforMariaDB/伺服器/資料庫/寫入 | 使用指定的參數創建 MariaDB 資料庫或更新指定資料庫的屬性。 |
> | 動作 | 微軟.DBforMariaDB/伺服器/資料庫/刪除 | 刪除現有的 MariaDB 資料庫。 |
> |  | **servers/firewallRules** |  |
> | 動作 | Microsoft.DBforMariaDB/servers/firewallRules/read | 傳回伺服器的防火牆規則清單，或取得指定防火牆規則的屬性。 |
> | 動作 | Microsoft.DBforMariaDB/servers/firewallRules/write | 使用指定參數建立防火牆規則，或更新現有的規則。 |
> | 動作 | Microsoft.DBforMariaDB/servers/firewallRules/delete | 刪除現有防火牆規則。 |
> |  | **servers/keys** |  |
> | 動作 | 微軟.DBforMariaDB/伺服器/金鑰/讀取 | 傳回伺服器金鑰的清單，或取得指定伺服器金鑰的屬性。 |
> | 動作 | 微軟.DBforMariaDB/伺服器/金鑰/寫入 | 使用指定參數建立金鑰，或更新指定伺服器金鑰的屬性或標記。 |
> | 動作 | 微軟.DBforMariaDB/伺服器/金鑰/刪除 | 刪除現有的伺服器金鑰。 |
> |  | **伺服器/記錄檔案** |  |
> | 動作 | 微軟.DBforMariaDB/伺服器/記錄檔/讀取 | 返回 MariaDB 紀錄檔的清單。 |
> |  | **伺服器/專用端接連線Proxies** |  |
> | 動作 | 微軟.DBforMariaDB/伺服器/私人端接連接Proxies/驗證/操作 | 驗證來自 NRP 連接的專屬的檢查點 |
> | 動作 | 微軟.DBforMariaDB/伺服器/私人端接連接Proxies/讀取 | 返回私有終結點連接代理的清單或獲取指定專用終結點連接代理的屬性。 |
> | 動作 | 微軟.DBforMariaDB/伺服器/私人端接連接Proxies/寫 | 使用指定的參數創建專用終結點連接代理,或更新指定專用終結點連接代理的屬性或標記。 |
> | 動作 | 微軟.DBforMariaDB/伺服器/私人端接連接Proxies/刪除 | 移除現有專用終結點連線代理 |
> |  | **伺服器/專用端點連接** |  |
> | 動作 | 微軟.DBforMariaDB/伺服器/專用端點連接/讀取 | 返回專用終結點連接的清單或獲取指定專用終結點連接的屬性。 |
> | 動作 | 微軟.DBforMariaDB/伺服器/專用端點連接/刪除 | 移除現有的專用終結點連線 |
> | 動作 | 微軟.DBforMariaDB/伺服器/私有端點連接/寫入 | 批准或拒絕現有專用終結點連線 |
> |  | **伺服器/專用連結資源** |  |
> | 動作 | 微軟.DBforMariaDB/伺服器/私有連結資源/讀取 | 取得檔案 MariaDB 伺服器的專用連結資源 |
> |  | **伺服器/供應商/微軟.見解/診斷設置** |  |
> | 動作 | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/diagnosticSettings/read | 取得資源的診斷設定 |
> | 動作 | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/diagnosticSettings/write | 建立或更新資源的診斷設定 |
> |  | **伺服器/供應商/微軟.見解/日誌定義** |  |
> | 動作 | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/logDefinitions/read | 取得 MariaDB 伺服器的可用記錄 |
> |  | **伺服器/供應商/微軟.見解/指標定義** |  |
> | 動作 | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/metricDefinitions/read | 傳回可供資料庫使用之計量的類型 |
> |  | **servers/recoverableServers** |  |
> | 動作 | Microsoft.DBforMariaDB/servers/recoverableServers/read | 傳回可復原的 MariaDB 伺服器資訊 |
> |  | **伺服器/複本** |  |
> | 動作 | 微軟.DBforMariaDB/伺服器/複本/讀取 | 取得 MariaDB 伺服器讀取複本 |
> |  | **servers/securityAlertPolicies** |  |
> | 動作 | Microsoft.DBforMariaDB/servers/securityAlertPolicies/read | 擷取給定伺服器上所設定的伺服器威脅偵測原則詳細資料 |
> | 動作 | Microsoft.DBforMariaDB/servers/securityAlertPolicies/write | 變更指定伺服器的伺服器威脅偵測原則 |
> |  | **servers/topQueryStatistics** |  |
> | 動作 | 微軟.DBforMariaDB/伺服器/頂級查詢統計/讀取 | 傳回最高排名查詢的查詢統計資料清單。 |
> | 動作 | 微軟.DBforMariaDB/伺服器/頂級查詢統計/讀取 | 回查詢統計資訊 |
> |  | **servers/virtualNetworkRules** |  |
> | 動作 | Microsoft.DBforMariaDB/servers/virtualNetworkRules/read | 傳回虛擬網路規則的清單，或取得指定虛擬網路規則的屬性。 |
> | 動作 | Microsoft.DBforMariaDB/servers/virtualNetworkRules/write | 使用指定參數建立虛擬網路規則，或更新指定虛擬網路規則的屬性或標記。 |
> | 動作 | Microsoft.DBforMariaDB/servers/virtualNetworkRules/delete | 刪除現有虛擬網路規則 |
> |  | **servers/waitStatistics** |  |
> | 動作 | 微軟.DBforMariaDB/伺服器/等待統計/閱讀 | 傳回執行個體的等候統計資料 |
> | 動作 | 微軟.DBforMariaDB/伺服器/等待統計/閱讀 | 返回等待統計資訊 |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

Azure 服務[:MySQL 的 Azure 資料庫](../mysql/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | 微軟.DBforMySQL/註冊/操作 | 註冊 MySQL 資源提供者 |
> | 動作 | 微軟.DBforMySQL/檢查名稱可用性/操作 | 確認指定的伺服器名稱是否可用來針對指定的訂用帳戶進行全球佈建。 |
> |  | **位置/azure 同步操作** |  |
> | 動作 | 微軟.DBforMySQL/位置/azureAsync操作/讀取 | 傳回 MySQL 伺服器操作結果 |
> |  | **位置/動作結果** |  |
> | 動作 | 微軟.DBforMySQL/位置/操作結果/讀取 | 傳回資源群組的 MySQL 伺服器操作結果 |
> | 動作 | 微軟.DBforMySQL/位置/操作結果/讀取 | 傳回 MySQL 伺服器操作結果 |
> |  | **位置/效能層** |  |
> | 動作 | Microsoft.DBforMySQL/locations/performanceTiers/read | 傳回可用的效能層級清單。 |
> |  | **位置/專用端點連接Azure同步操作** |  |
> | 動作 | 微軟.DBforMySQL/位置/專用端接Azure同步操作/讀取 | 取得專用終結點連線操作的結果 |
> |  | **位置/專用端點連線操作結果** |  |
> | 動作 | 微軟.DBforMySQL/位置/私人端點連接操作結果/讀取 | 取得專用終結點連線操作的結果 |
> |  | **位置/專用端點連線代理Azure同步操作** |  |
> | 動作 | 微軟.DBforMySQL/位置/專用端點連接代理Azure同步操作/讀取 | 取得專用終結點連線代理操作的結果 |
> |  | **位置/專用端接連線操作結果** |  |
> | 動作 | 微軟.DBforMySQL/位置/私有端接連接代理操作結果/讀取 | 取得專用終結點連線代理操作的結果 |
> |  | **位置/安全警報策略Azure同步操作** |  |
> | 動作 | 微軟.DBforMySQL/位置/安全警報策略Azure同步操作/讀取 | 返回伺服器威脅檢測操作結果的清單。 |
> |  | **位置/安全警報策略操作結果** |  |
> | 動作 | 微軟.DBforMySQL/位置/安全警報策略操作結果/讀取 | 返回伺服器威脅檢測操作結果的清單。 |
> |  | **位置/伺服器鍵Azure同步操作** |  |
> | 動作 | 微軟.DBforMySQL/位置/伺服器鍵Azure同步操作/讀取 | 取得對透明資料加密伺服器金鑰的正在進行的作業 |
> |  | **位置/伺服器鍵操作結果** |  |
> | 動作 | 微軟.DBforMySQL/位置/伺服器鍵操作結果/讀取 | 取得對透明資料加密伺服器金鑰的正在進行的作業 |
> |  | **操作** |  |
> | 動作 | 微軟.DBforMySQL/操作/讀取 | 返回 MySQL 操作的清單。 |
> |  | **效能等級** |  |
> | 動作 | Microsoft.DBforMySQL/performanceTiers/read | 傳回可用的效能層級清單。 |
> |  | **伺服器** |  |
> | 動作 | 微軟.DBforMySQL/伺服器/查詢文本/操作 | 回覆查詢清單的文字 |
> | 動作 | 微軟.DBforMySQL/伺服器/查詢文本/操作 | 傳回查詢的文字 |
> | 動作 | Microsoft.DBforMySQL/servers/read | 傳回伺服器清單，或取得指定伺服器的屬性。 |
> | 動作 | Microsoft.DBforMySQL/servers/write | 使用指定參數建立伺服器，或更新指定伺服器的屬性或標記。 |
> | 動作 | Microsoft.DBforMySQL/servers/delete | 刪除現有伺服器。 |
> | 動作 | 微軟.DBforMySQL/伺服器/重新啟動/操作 | 重新啟動特定伺服器。 |
> | 動作 | Microsoft.DBforMySQL/servers/updateConfigurations/action | 更新指定伺服器的組態 |
> |  | **servers/administrators** |  |
> | 動作 | 微軟.DBforMySQL/伺服器/管理員/讀取 | 獲取 MySQL 伺服器管理員的清單。 |
> | 動作 | 微軟.DBforMySQL/伺服器/管理員/寫入 | 使用指定的參數創建或更新 MySQL 伺服器管理員。 |
> | 動作 | 微軟.DBforMySQL/伺服器/管理員/刪除 | 刪除 MySQL 伺服器的現有管理員。 |
> |  | **servers/advisors** |  |
> | 動作 | 微軟.DBforMySQL/伺服器/顧問/閱讀 | 傳回建議程式清單 |
> | 動作 | 微軟.DBforMySQL/伺服器/顧問/閱讀 | 返回顧問 |
> | 動作 | 微軟.DBforMySQL/伺服器/顧問/創建推薦操作會話/操作 | 建立新的建議操作工作階段 |
> |  | **伺服器/顧問/建議的操作** |  |
> | 動作 | 微軟.DBforMySQL/伺服器/顧問/建議的操作/讀取 | 傳回建議的動作清單 |
> | 動作 | 微軟.DBforMySQL/伺服器/顧問/建議的操作/讀取 | 傳回建議的動作清單 |
> | 動作 | 微軟.DBforMySQL/伺服器/顧問/建議的操作/讀取 | 回傳回建議的作業 |
> |  | **servers/configurations** |  |
> | 動作 | Microsoft.DBforMySQL/servers/configurations/read | 傳回伺服器的組態清單，或取得指定組態的屬性。 |
> | 動作 | Microsoft.DBforMySQL/servers/configurations/write | 更新指定組態的值 |
> |  | **servers/databases** |  |
> | 動作 | 微軟.DBforMySQL/伺服器/資料庫/讀取 | 返回 MySQL 資料庫的清單或獲取指定資料庫的屬性。 |
> | 動作 | 微軟.DBforMySQL/伺服器/資料庫/寫入 | 使用指定的參數創建 MySQL 資料庫或更新指定資料庫的屬性。 |
> | 動作 | 微軟.DBforMySQL/伺服器/資料庫/刪除 | 刪除現有的 MySQL 資料庫。 |
> |  | **servers/firewallRules** |  |
> | 動作 | Microsoft.DBforMySQL/servers/firewallRules/read | 傳回伺服器的防火牆規則清單，或取得指定防火牆規則的屬性。 |
> | 動作 | Microsoft.DBforMySQL/servers/firewallRules/write | 使用指定參數建立防火牆規則，或更新現有的規則。 |
> | 動作 | Microsoft.DBforMySQL/servers/firewallRules/delete | 刪除現有防火牆規則。 |
> |  | **servers/keys** |  |
> | 動作 | 微軟.DBforMySQL/伺服器/金鑰/讀取 | 傳回伺服器金鑰的清單，或取得指定伺服器金鑰的屬性。 |
> | 動作 | 微軟.DBforMySQL/伺服器/金鑰/寫入 | 使用指定參數建立金鑰，或更新指定伺服器金鑰的屬性或標記。 |
> | 動作 | 微軟.DBforMySQL/伺服器/金鑰/刪除 | 刪除現有的伺服器金鑰。 |
> |  | **伺服器/記錄檔案** |  |
> | 動作 | 微軟.DBforMySQL/伺服器/日誌檔/讀取 | 傳回 MySQL 紀錄檔的清單。 |
> |  | **伺服器/專用端接連線Proxies** |  |
> | 動作 | 微軟.DBforMySQL/伺服器/私人端接連接Proxies/驗證/操作 | 驗證來自 NRP 連接的專屬的檢查點 |
> | 動作 | 微軟.DBforMySQL/伺服器/私人端接連接Proxies/讀取 | 返回私有終結點連接代理的清單或獲取指定專用終結點連接代理的屬性。 |
> | 動作 | 微軟.DBforMySQL/伺服器/私人端接連接Proxies/寫 | 使用指定的參數創建專用終結點連接代理,或更新指定專用終結點連接代理的屬性或標記。 |
> | 動作 | 微軟.DBforMySQL/伺服器/私人端接連接Proxies/刪除 | 移除現有專用終結點連線代理 |
> |  | **伺服器/專用端點連接** |  |
> | 動作 | 微軟.DBforMySQL/伺服器/私有端點連接/讀取 | 返回專用終結點連接的清單或獲取指定專用終結點連接的屬性。 |
> | 動作 | 微軟.DBforMySQL/伺服器/專用端點連接/刪除 | 移除現有的專用終結點連線 |
> | 動作 | 微軟.DBforMySQL/伺服器/私有端點連接/寫入 | 批准或拒絕現有專用終結點連線 |
> |  | **伺服器/專用連結資源** |  |
> | 動作 | 微軟.DBforMySQL/伺服器/私有連結資源/讀取 | 取得相對 MySQL 伺服器的專用連結資源 |
> |  | **伺服器/供應商/微軟.見解/診斷設置** |  |
> | 動作 | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/diagnosticSettings/read | 取得資源的診斷設定 |
> | 動作 | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/diagnosticSettings/write | 建立或更新資源的診斷設定 |
> |  | **伺服器/供應商/微軟.見解/日誌定義** |  |
> | 動作 | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/logDefinitions/read | 取得 MySQL 伺服器的可用記錄 |
> |  | **伺服器/供應商/微軟.見解/指標定義** |  |
> | 動作 | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/metricDefinitions/read | 傳回可供資料庫使用之計量的類型 |
> |  | **servers/recoverableServers** |  |
> | 動作 | Microsoft.DBforMySQL/servers/recoverableServers/read | 傳回可復原的 MySQL 伺服器資訊 |
> |  | **伺服器/複本** |  |
> | 動作 | 微軟.DBforMySQL/伺服器/副本/讀取 | 取得 MySQL 伺服器讀取複本 |
> |  | **servers/securityAlertPolicies** |  |
> | 動作 | Microsoft.DBforMySQL/servers/securityAlertPolicies/read | 擷取給定伺服器上所設定的伺服器威脅偵測原則詳細資料 |
> | 動作 | Microsoft.DBforMySQL/servers/securityAlertPolicies/write | 變更指定伺服器的伺服器威脅偵測原則 |
> |  | **servers/topQueryStatistics** |  |
> | 動作 | 微軟.DBforMySQL/伺服器/頂級查詢統計/讀取 | 傳回最高排名查詢的查詢統計資料清單。 |
> | 動作 | 微軟.DBforMySQL/伺服器/頂級查詢統計/讀取 | 回查詢統計資訊 |
> |  | **servers/virtualNetworkRules** |  |
> | 動作 | Microsoft.DBforMySQL/servers/virtualNetworkRules/read | 傳回虛擬網路規則的清單，或取得指定虛擬網路規則的屬性。 |
> | 動作 | Microsoft.DBforMySQL/servers/virtualNetworkRules/write | 使用指定參數建立虛擬網路規則，或更新指定虛擬網路規則的屬性或標記。 |
> | 動作 | Microsoft.DBforMySQL/servers/virtualNetworkRules/delete | 刪除現有虛擬網路規則 |
> |  | **servers/waitStatistics** |  |
> | 動作 | 微軟.DBforMySQL/伺服器/等待統計/讀取 | 傳回執行個體的等候統計資料 |
> | 動作 | 微軟.DBforMySQL/伺服器/等待統計/讀取 | 返回等待統計資訊 |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

Azure 服務:[用於 PostgreSQL 的 Azure 資料庫](../postgresql/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | 微軟.DBforPostsql/註冊/行動 | 註冊 PostgreSQL 資源提供者 |
> | 動作 | 微軟.DBforPostgreSQL/檢查名稱可用性/操作 | 確認指定的伺服器名稱是否可用來針對指定的訂用帳戶進行全球佈建。 |
> |  | **位置/azure 同步操作** |  |
> | 動作 | 微軟.DBforPostgreSQL/位置/azureAsync操作/讀取 | 傳回後格雷SQL伺服器操作結果 |
> |  | **位置/動作結果** |  |
> | 動作 | 微軟.DBforPostgreSQL/位置/操作結果/閱讀 | 傳回基於資源群組的後格雷SQL伺服器操作結果 |
> | 動作 | 微軟.DBforPostgreSQL/位置/操作結果/閱讀 | 傳回後格雷SQL伺服器操作結果 |
> |  | **位置/效能層** |  |
> | 動作 | Microsoft.DBforPostgreSQL/locations/performanceTiers/read | 傳回可用的效能層級清單。 |
> |  | **位置/專用端點連接Azure同步操作** |  |
> | 動作 | 微軟.DBforPostgreSQL/位置/私人端接Azure同步操作/讀取 | 取得專用終結點連線操作的結果 |
> |  | **位置/專用端點連線操作結果** |  |
> | 動作 | 微軟.DBforPostgreSQL/位置/私人端點連接操作結果/讀取 | 取得專用終結點連線操作的結果 |
> |  | **位置/專用端點連線代理Azure同步操作** |  |
> | 動作 | 微軟.DBforPostgreSQL/位置/私有端點連接代理Azure同步操作/讀取 | 取得專用終結點連線代理操作的結果 |
> |  | **位置/專用端接連線操作結果** |  |
> | 動作 | 微軟.DBforPostgreSQL/位置/私人端接連接代理操作結果/讀取 | 取得專用終結點連線代理操作的結果 |
> |  | **位置/安全警報策略Azure同步操作** |  |
> | 動作 | 微軟.DBforPostgreSQL/位置/安全警報策略Azure同步操作/讀取 | 返回伺服器威脅檢測操作結果的清單。 |
> |  | **位置/安全警報策略操作結果** |  |
> | 動作 | 微軟.DBforPostgreSQL/位置/安全警報策略操作結果/讀取 | 返回伺服器威脅檢測操作結果的清單。 |
> |  | **位置/伺服器鍵Azure同步操作** |  |
> | 動作 | 微軟.DBforPostgreSQL/位置/伺服器鍵Azure同步操作/讀取 | 取得對透明資料加密伺服器金鑰的正在進行的作業 |
> |  | **位置/伺服器鍵操作結果** |  |
> | 動作 | 微軟.DBforPostgreSQL/位置/伺服器鍵操作結果/讀取 | 取得對透明資料加密伺服器金鑰的正在進行的作業 |
> |  | **操作** |  |
> | 動作 | 微軟.DBforPostgreSQL/操作/讀 | 傳回 PostgreSQL 操作的清單。 |
> |  | **效能等級** |  |
> | 動作 | Microsoft.DBforPostgreSQL/performanceTiers/read | 傳回可用的效能層級清單。 |
> |  | **伺服器** |  |
> | 動作 | Microsoft.DBforPostgreSQL/servers/queryTexts/action | 傳回查詢的文字 |
> | 動作 | Microsoft.DBforPostgreSQL/servers/read | 傳回伺服器清單，或取得指定伺服器的屬性。 |
> | 動作 | Microsoft.DBforPostgreSQL/servers/write | 使用指定參數建立伺服器，或更新指定伺服器的屬性或標記。 |
> | 動作 | Microsoft.DBforPostgreSQL/servers/delete | 刪除現有伺服器。 |
> | 動作 | 微軟.DBforPostgreSQL/伺服器/重新啟動/操作 | 重新啟動特定伺服器。 |
> | 動作 | Microsoft.DBforPostgreSQL/servers/updateConfigurations/action | 更新指定伺服器的組態 |
> |  | **servers/administrators** |  |
> | 動作 | 微軟.DBforPostgreSQL/伺服器/管理員/讀取 | 獲取 PostgreSQL 伺服器管理員的清單。 |
> | 動作 | 微軟.DBforPostgreSQL/伺服器/管理員/寫入 | 使用指定的參數創建或更新 PostgreSQL 伺服器管理員。 |
> | 動作 | 微軟.DBforPostgreSQL/伺服器/管理員/刪除 | 刪除 PostgreSQL 伺服器的現有管理員。 |
> |  | **servers/advisors** |  |
> | 動作 | Microsoft.DBforPostgreSQL/servers/advisors/read | 傳回建議程式清單 |
> | 動作 | Microsoft.DBforPostgreSQL/servers/advisors/recommendedActionSessions/action | 提出建議 |
> |  | **伺服器/顧問/建議的操作** |  |
> | 動作 | Microsoft.DBforPostgreSQL/servers/advisors/recommendedActions/read | 傳回建議的動作清單 |
> |  | **servers/configurations** |  |
> | 動作 | Microsoft.DBforPostgreSQL/servers/configurations/read | 傳回伺服器的組態清單，或取得指定組態的屬性。 |
> | 動作 | Microsoft.DBforPostgreSQL/servers/configurations/write | 更新指定組態的值 |
> |  | **servers/databases** |  |
> | 動作 | 微軟.DBforPostgreSQL/伺服器/資料庫/讀取 | 傳回 PostgreSQL 資料庫的清單或獲取指定資料庫的屬性。 |
> | 動作 | 微軟.DBforPostgreSQL/伺服器/資料庫/寫入 | 使用指定的參數創建 PostgreSQL 資料庫或更新指定資料庫的屬性。 |
> | 動作 | 微軟.DBforPostgreSQL/伺服器/資料庫/刪除 | 刪除現有的 PostgreSQL 資料庫。 |
> |  | **servers/firewallRules** |  |
> | 動作 | Microsoft.DBforPostgreSQL/servers/firewallRules/read | 傳回伺服器的防火牆規則清單，或取得指定防火牆規則的屬性。 |
> | 動作 | Microsoft.DBforPostgreSQL/servers/firewallRules/write | 使用指定參數建立防火牆規則，或更新現有的規則。 |
> | 動作 | Microsoft.DBforPostgreSQL/servers/firewallRules/delete | 刪除現有防火牆規則。 |
> |  | **servers/keys** |  |
> | 動作 | 微軟.DBforPostgreSQL/伺服器/金鑰/讀取 | 傳回伺服器金鑰的清單，或取得指定伺服器金鑰的屬性。 |
> | 動作 | 微軟.DBforPostgreSQL/伺服器/金鑰/寫入 | 使用指定參數建立金鑰，或更新指定伺服器金鑰的屬性或標記。 |
> | 動作 | 微軟.DBforPostgreSQL/伺服器/密鑰/刪除 | 刪除現有的伺服器金鑰。 |
> |  | **伺服器/記錄檔案** |  |
> | 動作 | 微軟.DBforPostgreSQL/伺服器/日誌檔/讀取 | 傳回 PostgreSQL 紀錄檔的清單。 |
> |  | **伺服器/專用端接連線Proxies** |  |
> | 動作 | 微軟.DBforPostgreSQL/伺服器/私人端接連接Proxies/驗證/操作 | 驗證來自 NRP 連接的專屬的檢查點 |
> | 動作 | 微軟.DBforPostgreSQL/伺服器/私人端接連接Proxies/讀取 | 返回私有終結點連接代理的清單或獲取指定專用終結點連接代理的屬性。 |
> | 動作 | 微軟.DBforPostgreSQL/伺服器/私人端接連接Proxies/寫 | 使用指定的參數創建專用終結點連接代理,或更新指定專用終結點連接代理的屬性或標記。 |
> | 動作 | 微軟.DBforPostgreSQL/伺服器/私人端接連接Proxies/刪除 | 移除現有專用終結點連線代理 |
> |  | **伺服器/專用端點連接** |  |
> | 動作 | 微軟.DBforPostgreSQL/伺服器/私人端點連接/讀取 | 返回專用終結點連接的清單或獲取指定專用終結點連接的屬性。 |
> | 動作 | 微軟.DBforPostgreSQL/伺服器/私人端點連接/刪除 | 移除現有的專用終結點連線 |
> | 動作 | 微軟.DBforPostgreSQL/伺服器/私有端點連接/寫入 | 批准或拒絕現有專用終結點連線 |
> |  | **伺服器/專用連結資源** |  |
> | 動作 | 微軟.DBforPostgreSQL/伺服器/私有連結資源/讀取 | 取得一個無 PostgreSQL 伺服器的專用連結資源 |
> |  | **伺服器/供應商/微軟.見解/診斷設置** |  |
> | 動作 | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/diagnosticSettings/read | 取得資源的診斷設定 |
> | 動作 | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/diagnosticSettings/write | 建立或更新資源的診斷設定 |
> |  | **伺服器/供應商/微軟.見解/日誌定義** |  |
> | 動作 | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/logDefinitions/read | 取得 PostgreSQL 伺服器的可用紀錄 |
> |  | **伺服器/供應商/微軟.見解/指標定義** |  |
> | 動作 | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/metricDefinitions/read | 傳回可供資料庫使用之計量的類型 |
> |  | **servers/queryTexts** |  |
> | 動作 | 微軟.DBforPostgreSQL/伺服器/查詢文本/閱讀 | 回覆查詢清單的文字 |
> |  | **servers/recoverableServers** |  |
> | 動作 | Microsoft.DBforPostgreSQL/servers/recoverableServers/read | 傳回可復原的 PostgreSQL 伺服器資訊 |
> |  | **伺服器/複本** |  |
> | 動作 | 微軟.DBforPostgreSQL/伺服器/副本/讀取 | 取得 PostgreSQL 伺服器讀取複本 |
> |  | **servers/securityAlertPolicies** |  |
> | 動作 | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/read | 擷取給定伺服器上所設定的伺服器威脅偵測原則詳細資料 |
> | 動作 | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/write | 變更指定伺服器的伺服器威脅偵測原則 |
> |  | **servers/topQueryStatistics** |  |
> | 動作 | Microsoft.DBforPostgreSQL/servers/topQueryStatistics/read | 傳回最高排名查詢的查詢統計資料清單。 |
> |  | **servers/virtualNetworkRules** |  |
> | 動作 | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/read | 傳回虛擬網路規則的清單，或取得指定虛擬網路規則的屬性。 |
> | 動作 | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/write | 使用指定參數建立虛擬網路規則，或更新指定虛擬網路規則的屬性或標記。 |
> | 動作 | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/delete | 刪除現有虛擬網路規則 |
> |  | **servers/waitStatistics** |  |
> | 動作 | Microsoft.DBforPostgreSQL/servers/waitStatistics/read | 傳回執行個體的等候統計資料 |
> |  | **伺服器v2** |  |
> | 動作 | 微軟.DBforPostsql/伺服器2/讀 | 傳回伺服器清單，或取得指定伺服器的屬性。 |
> | 動作 | 微軟.DBforPostgreSQL/伺服器v2/寫 | 使用指定參數建立伺服器，或更新指定伺服器的屬性或標記。 |
> | 動作 | 微軟.DBforPostsql/伺服器2/刪除 | 刪除現有伺服器。 |
> | 動作 | 微軟.DBforPostgreSQL/伺服器v2/更新配置/操作 | 更新指定伺服器的組態 |
> |  | **伺服器v2/設定** |  |
> | 動作 | 微軟.DBforPostgreSQL/伺服器v2/配置/讀取 | 傳回伺服器的組態清單，或取得指定組態的屬性。 |
> | 動作 | 微軟.DBforPostgreSQL/伺服器2/配置/寫入 | 更新指定組態的值 |
> |  | **伺服器v2/防火牆規則** |  |
> | 動作 | 微軟.DBforPostgreSQL/伺服器v2/防火牆規則/讀取 | 傳回伺服器的防火牆規則清單，或取得指定防火牆規則的屬性。 |
> | 動作 | 微軟.DBforPostgreSQL/伺服器v2/防火牆規則/寫入 | 使用指定參數建立防火牆規則，或更新現有的規則。 |
> | 動作 | 微軟.DBforPostgreSQL/伺服器v2/防火牆規則/刪除 | 刪除現有防火牆規則。 |
> |  | **伺服器2/供應商/微軟.見解/診斷設置** |  |
> | 動作 | 微軟.DBforPostgreSQL/伺服器2/供應商/微軟.見解/診斷設置/讀 | 取得資源的診斷設定 |
> | 動作 | 微軟.DBforPostgreSQL/伺服器2/供應商/微軟.見解/診斷設置/寫入 | 建立或更新資源的診斷設定 |
> |  | **伺服器2/提供程式/微軟.Insights/日誌定義** |  |
> | 動作 | 微軟.DBforPostgreSQL/伺服器2/供應商/微軟.見解/日誌定義/讀取 | 取得 PostgreSQL 伺服器的可用紀錄 |
> |  | **伺服器2/供應商/微軟.見解/指標定義** |  |
> | 動作 | 微軟.DBforPostgreSQL/伺服器2/供應商/微軟.見解/指標定義/讀 | 傳回可供資料庫使用之計量的類型 |
> |  | **單伺服器** |  |
> | 動作 | 微軟.DBforPostgreSQL/單伺服器/讀取 | 傳回伺服器清單，或取得指定伺服器的屬性。 |
> | 動作 | 微軟.DBforPostgreSQL/單伺服器/寫入 | 使用指定參數建立伺服器，或更新指定伺服器的屬性或標記。 |
> | 動作 | 微軟.DBforPostgreSQL/單伺服器/刪除 | 刪除現有伺服器。 |
> |  | **單伺服器/供應商/微軟.見解/診斷設置** |  |
> | 動作 | 微軟.DBforPostgreSQL/單伺服器/供應商/微軟.見解/診斷設置/讀取 | 取得資源的診斷設定 |
> | 動作 | 微軟.DBforPostgreSQL/單伺服器/供應商/微軟.見解/診斷設置/寫入 | 建立或更新資源的診斷設定 |
> |  | **單一伺服器/供應商/微軟.見解/日誌定義** |  |
> | 動作 | 微軟.DBforPostgreSQL/單伺服器/供應商/微軟.見解/日誌定義/讀取 | 取得 PostgreSQL 伺服器的可用紀錄 |
> |  | **單伺服器/供應商/微軟.見解/指標定義** |  |
> | 動作 | 微軟.DBforPostgreSQL/單伺服器/供應商/微軟.見解/指標定義/讀取 | 傳回可供資料庫使用之計量的類型 |

## <a name="microsoftdevices"></a>Microsoft.Devices

Azure 服務[:IoT 中心](../iot-hub/index.yml)[,IoT 中心裝置預配服務](../iot-dps/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.Devices/register/action | 針對 IotHub 資源提供者註冊訂用帳戶，並讓您能夠建立 IotHub 資源 |
> | 動作 | Microsoft.Devices/checkNameAvailability/Action | 檢查 IotHub 名稱是否可供使用 |
> | 動作 | Microsoft.Devices/checkProvisioningServiceNameAvailability/Action | 確認佈建服務名稱是否可用 |
> | 動作 | Microsoft.Devices/register/action | 針對 IotHub 資源提供者註冊訂用帳戶，並讓您能夠建立 IotHub 資源 |
> | 動作 | Microsoft.Devices/checkNameAvailability/Action | 檢查 IotHub 名稱是否可供使用 |
> | 動作 | Microsoft.Devices/checkProvisioningServiceNameAvailability/Action | 確認佈建服務名稱是否可用 |
> | 動作 | Microsoft.Devices/register/action | 針對 IotHub 資源提供者註冊訂用帳戶，並讓您能夠建立 IotHub 資源 |
> |  | **帳戶/診斷設定** |  |
> | 動作 | 微軟.設備/帳戶/診斷設置/讀取 | 取得資源的診斷設定 |
> | 動作 | 微軟.設備/帳戶/診斷設置/寫入 | 建立或更新資源的診斷設定 |
> |  | **帳號/記錄定義** |  |
> | 動作 | 微軟.設備/帳戶/日誌定義/讀取 | 取得 IotHub 服務的可用記錄定義 |
> |  | **帳戶/指標定義** |  |
> | 動作 | 微軟.設備/帳戶/指標定義/讀取 | 取得 IotHub 服務的可用計量 |
> |  | **數位雙胞胎** |  |
> | 動作 | 微軟.設備/數位雙胞胎/閱讀 | 取得與訂閱關聯的數位孿生帳戶的清單 |
> | 動作 | 微軟.設備/數位雙胞胎/寫入 | 建立新的數位孿生帳戶 |
> | 動作 | 微軟.設備/數位雙胞胎/刪除 | 刪除現有的數位孿生帳戶及其所有子帳戶 |
> |  | **數位雙胞胎/操作結果** |  |
> | 動作 | 微軟.設備/數位雙胞胎/操作結果/閱讀 | 取得對數位孿生帳戶的操作狀態 |
> |  | **數位雙胞胎/斯克烏斯** |  |
> | 動作 | 微軟.設備/數位雙胞胎/skus/閱讀 | 取得數字雙胞胎帳戶的有效 SKU 清單 |
> |  | **彈性池/診斷設置** |  |
> | 動作 | Microsoft.Devices/ElasticPools/diagnosticSettings/read | 取得資源的診斷設定 |
> | 動作 | Microsoft.Devices/ElasticPools/diagnosticSettings/write | 建立或更新資源的診斷設定 |
> |  | **彈性池/事件網格篩選器** |  |
> | 動作 | 微軟.設備/彈性池/事件網格過濾器/寫入 | 建立新或更新現有彈性池事件網格篩選器 |
> | 動作 | 微軟.設備/彈性池/事件網格過濾器/讀取 | 取得彈性池事件網格篩選器 |
> | 動作 | 微軟.裝置/彈性池/事件網格過濾器/刪除 | 移除彈性池事件網格篩選器 |
> |  | **彈性池/iotHub租戶** |  |
> | 動作 | Microsoft.Devices/elasticPools/iotHubTenants/Write | 建立或更新 IotHub 租用戶資源 |
> | 動作 | Microsoft.Devices/elasticPools/iotHubTenants/Read | 取得 IotHub 租用戶資源 |
> | 動作 | Microsoft.Devices/elasticPools/iotHubTenants/Delete | 刪除 IotHub 租用戶資源 |
> | 動作 | Microsoft.Devices/elasticPools/iotHubTenants/listKeys/Action | 取得 IotHub 租用戶金鑰 |
> | 動作 | Microsoft.Devices/elasticPools/iotHubTenants/exportDevices/Action | 匯出裝置 |
> | 動作 | Microsoft.Devices/elasticPools/iotHubTenants/importDevices/Action | 匯入裝置 |
> |  | **彈性池/iotHub租戶/證書** |  |
> | 動作 | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Read | 取得憑證 |
> | 動作 | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Write | 建立或更新憑證 |
> | 動作 | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Delete | 刪除憑證 |
> | 動作 | Microsoft.Devices/elasticPools/iotHubTenants/certificates/generateVerificationCode/Action | 產生驗證碼 |
> | 動作 | Microsoft.Devices/elasticPools/iotHubTenants/certificates/verify/Action | 驗證憑證資源 |
> |  | **彈性池/IotHub租戶/診斷設置** |  |
> | 動作 | Microsoft.Devices/ElasticPools/IotHubTenants/diagnosticSettings/read | 取得資源的診斷設定 |
> | 動作 | Microsoft.Devices/ElasticPools/IotHubTenants/diagnosticSettings/write | 建立或更新資源的診斷設定 |
> |  | **彈性池/iotHub租戶/事件HubEndpoints/消費者組** |  |
> | 動作 | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Write | 建立 EventHub 取用者群組 |
> | 動作 | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Read | 取得 EventHub 取用者群組 |
> | 動作 | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Delete | 刪除 EventHub 取用者群組 |
> |  | **彈性池/iotHub租戶/getStats** |  |
> | 動作 | Microsoft.Devices/elasticPools/iotHubTenants/getStats/Read | 取得 IotHub 租用戶統計資料資源 |
> |  | **彈性池/iotHub租戶/iotHubKeys** |  |
> | 動作 | Microsoft.Devices/elasticPools/iotHubTenants/iotHubKeys/listkeys/Action | 取得 IotHub 租用戶金鑰 |
> |  | **彈性池/iotHub租戶/作業** |  |
> | 動作 | Microsoft.Devices/elasticPools/iotHubTenants/jobs/Read | 取得在給定的 IotHub 上所提交的作業詳細資料 |
> |  | **彈性池/IotHub租戶/日誌定義** |  |
> | 動作 | Microsoft.Devices/ElasticPools/IotHubTenants/logDefinitions/read | 取得 IotHub 服務的可用記錄定義 |
> |  | **彈性池/IotHub租戶/指標定義** |  |
> | 動作 | Microsoft.Devices/ElasticPools/IotHubTenants/metricDefinitions/read | 取得 IotHub 服務的可用計量 |
> |  | **彈性池/iotHub租戶/配額指標** |  |
> | 動作 | Microsoft.Devices/elasticPools/iotHubTenants/quotaMetrics/Read | 取得配額計量 |
> |  | **彈性池/iotHub租戶/路由/路由** |  |
> | 動作 | Microsoft.Devices/elasticPools/iotHubTenants/routing/routes/$testall/Action | 針對所有現有路由來測試訊息 |
> | 動作 | Microsoft.Devices/elasticPools/iotHubTenants/routing/routes/$testnew/Action | 針對所提供的測試路由來測試訊息 |
> |  | **彈性池/iotHub租戶/路由終結點運行狀況** |  |
> | 動作 | Microsoft.Devices/elasticPools/iotHubTenants/routingEndpointsHealth/Read | 取得 IotHub 之所有路由端點的健康狀態 |
> |  | **彈性池/iotHub租戶/安全設置** |  |
> | 動作 | 微軟.設備/彈性池/iotHub租戶/安全設置/寫入 | 更新 Iot 租戶中心的 Azure 安全中心設定 |
> | 動作 | 微軟.設備/彈性池/iotHub租戶/安全設置/讀取 | 取得 Iot 租戶中心的 Azure 安全中心設定 |
> |  | **彈性池/iotHub租戶/安全設置/操作結果** |  |
> | 動作 | 微軟.設備/彈性池/iotHub租戶/安全設置/操作結果/閱讀 | 取得 Iot 租戶中心安全設定的「非同步放置」操作的結果 |
> |  | **彈性池/指標定義** |  |
> | 動作 | Microsoft.Devices/ElasticPools/metricDefinitions/read | 取得 IotHub 服務的可用計量 |
> |  | **iotHubs** |  |
> | 動作 | Microsoft.Devices/iotHubs/Read | 取得 IotHub 資源 |
> | 動作 | Microsoft.Devices/iotHubs/Write | 建立或更新 IotHub 資源 |
> | 動作 | Microsoft.Devices/iotHubs/Delete | 刪除 IotHub 資源 |
> | 動作 | Microsoft.Devices/iotHubs/listkeys/Action | 取得所有 IotHub 金鑰 |
> | 動作 | Microsoft.Devices/iotHubs/exportDevices/Action | 匯出裝置 |
> | 動作 | Microsoft.Devices/iotHubs/importDevices/Action | 匯入裝置 |
> | 動作 | 微軟.裝置/iotHubs/私人端點連接審批/操作 | 批准或拒絕專用終結點連線 |
> |  | **iotHubs/憑證** |  |
> | 動作 | Microsoft.Devices/iotHubs/certificates/Read | 取得憑證 |
> | 動作 | Microsoft.Devices/iotHubs/certificates/Write | 建立或更新憑證 |
> | 動作 | Microsoft.Devices/iotHubs/certificates/Delete | 刪除憑證 |
> | 動作 | Microsoft.Devices/iotHubs/certificates/generateVerificationCode/Action | 產生驗證碼 |
> | 動作 | Microsoft.Devices/iotHubs/certificates/verify/Action | 驗證憑證資源 |
> | 動作 | Microsoft.Devices/iotHubs/certificates/Read | 取得憑證 |
> | 動作 | Microsoft.Devices/iotHubs/certificates/Write | 建立或更新憑證 |
> | 動作 | Microsoft.Devices/iotHubs/certificates/Delete | 刪除憑證 |
> | 動作 | Microsoft.Devices/iotHubs/certificates/generateVerificationCode/Action | 產生驗證碼 |
> | 動作 | Microsoft.Devices/iotHubs/certificates/verify/Action | 驗證憑證資源 |
> |  | **IotHubs/診斷設定** |  |
> | 動作 | Microsoft.Devices/IotHubs/diagnosticSettings/read | 取得資源的診斷設定 |
> | 動作 | Microsoft.Devices/IotHubs/diagnosticSettings/write | 建立或更新資源的診斷設定 |
> |  | **iotHubs/數字雙星連結** |  |
> | 動作 | 微軟.設備/電圖中心/數位雙星連結/寫入 |  |
> | 動作 | 微軟.裝置/iotHubs/數位雙星連結/閱讀 |  |
> | 動作 | 微軟.裝置/iotHubs/數位雙星連結/刪除 |  |
> |  | **iotHubs/事件網格篩選器** |  |
> | 動作 | Microsoft.Devices/iotHubs/eventGridFilters/Write | 建立新的或更新現有的事件格線篩選 |
> | 動作 | Microsoft.Devices/iotHubs/eventGridFilters/Read | 取得事件格線篩選 |
> | 動作 | Microsoft.Devices/iotHubs/eventGridFilters/Delete | 刪除事件格線篩選 |
> |  | **iotHubs/事件HubEndpoints/消費者組** |  |
> | 動作 | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Write | 建立 EventHub 取用者群組 |
> | 動作 | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Read | 取得 EventHub 取用者群組 |
> | 動作 | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Delete | 刪除 EventHub 取用者群組 |
> |  | **iotHubs/iotHubKeys** |  |
> | 動作 | Microsoft.Devices/iotHubs/iotHubKeys/listkeys/Action | 取得給定名稱的 IotHub 金鑰 |
> |  | **iotHubs/iotHubStats** |  |
> | 動作 | Microsoft.Devices/iotHubs/iotHubStats/Read | 取得 IotHub 統計資料 |
> |  | **iotHubs/工作** |  |
> | 動作 | Microsoft.Devices/iotHubs/jobs/Read | 取得在給定的 IotHub 上所提交的作業詳細資料 |
> |  | **IotHubs/紀錄定義** |  |
> | 動作 | Microsoft.Devices/IotHubs/logDefinitions/read | 取得 IotHub 服務的可用記錄定義 |
> |  | **IotHubs/指標定義** |  |
> | 動作 | Microsoft.Devices/IotHubs/metricDefinitions/read | 取得 IotHub 服務的可用計量 |
> |  | **iotHubs/操作結果** |  |
> | 動作 | Microsoft.Devices/iotHubs/operationresults/Read | 取得作業結果 (過時的 API) |
> |  | **iotHubs/私有端點連接Proxies** |  |
> | 動作 | 微軟.裝置/iotHubs/私人終端連線Proxies/驗證/操作 | 在建立期間驗證專用終結點連線代理輸入 |
> | 動作 | 微軟.裝置/iotHubs/私人終端連接Proxies/閱讀 | 取得指定專用的終結點連線代理的屬性 |
> | 動作 | 微軟.裝置/iotHubs/私人終端連線Proxies/寫 | 建立或更新專用終結點連線代理 |
> | 動作 | 微軟.裝置/iotHubs/私人終端連接Proxies/刪除 | 移除現有專用終結點連線代理 |
> |  | **iotHubs/私有端點連接Proxies/操作結果** |  |
> | 動作 | 微軟.設備/iotHubs/私人終端連接Proxies/操作結果/閱讀 | 取得私有終結點連線代理上的非同步操作的結果 |
> |  | **iotHubs/私有端點連接** |  |
> | 動作 | 微軟.裝置/iotHubs/私人端點連接/讀取 | 取得指定專用的終結點連線的屬性 |
> | 動作 | 微軟.裝置/iotHubs/私人端點連接/刪除 | 移除現有的專用終結點連線 |
> | 動作 | 微軟.裝置/iotHubs/私有端點連接/寫入 | 建立或更新專用終結點連線 |
> |  | **iotHubs/專用端點連接/操作結果** |  |
> | 動作 | 微軟.裝置/iotHubs/私有端點連接/操作結果/閱讀 | 取得私有終結點連接上非同步操作的結果 |
> |  | **iotHubs/私有連結資源** |  |
> | 動作 | 微軟.裝置/iotHubs/私人連結資源/閱讀 | 取得 IotHub 的私人連結資源 |
> |  | **iotHubs/配額指標** |  |
> | 動作 | Microsoft.Devices/iotHubs/quotaMetrics/Read | 取得配額計量 |
> |  | **iotHubs/路由** |  |
> | 動作 | Microsoft.Devices/iotHubs/routing/$testall/Action | 針對所有現有路由來測試訊息 |
> | 動作 | Microsoft.Devices/iotHubs/routing/$testnew/Action | 針對所提供的測試路由來測試訊息 |
> |  | **iotHubs/路由終結點執行狀況** |  |
> | 動作 | Microsoft.Devices/iotHubs/routingEndpointsHealth/Read | 取得 IotHub 之所有路由端點的健康狀態 |
> |  | **iotHubs/安全設定** |  |
> | 動作 | 微軟.裝置/iotHubs/安全設置/寫入 | 更新 Iot 中心上的 Azure 安全中心設定 |
> | 動作 | 微軟.裝置/iotHubs/安全設置/閱讀 | 取得 Iot 中心上的 Azure 安全中心設定 |
> |  | **iotHubs/安全設定/操作結果** |  |
> | 動作 | 微軟.設備/iotHubs/安全設置/操作結果/閱讀 | 取得 Iot 中心安全設定的「非同步放置」動作的結果 |
> |  | **iotHubs/skus** |  |
> | 動作 | Microsoft.Devices/iotHubs/skus/Read | 取得有效的 IotHub SKU |
> |  | **位置/動作結果** |  |
> | 動作 | Microsoft.Devices/locations/operationresults/Read | 取得以位置為基礎的作業結果 |
> | 動作 | Microsoft.Devices/locations/operationresults/Read | 取得以位置為基礎的作業結果 |
> |  | **動作結果** |  |
> | 動作 | Microsoft.Devices/operationresults/Read | 取得作業結果 |
> | 動作 | Microsoft.Devices/operationresults/Read | 取得作業結果 |
> |  | **操作** |  |
> | 動作 | Microsoft.Devices/operations/Read | 取得所有 ResourceProvider 作業 |
> | 動作 | Microsoft.Devices/operations/Read | 取得所有 ResourceProvider 作業 |
> |  | **預配服務** |  |
> | 動作 | Microsoft.Devices/provisioningServices/Read | 取得 IotDps 資源 |
> | 動作 | Microsoft.Devices/provisioningServices/Write | 建立 IotDps 資源 |
> | 動作 | Microsoft.Devices/provisioningServices/Delete | 刪除 IotDps 資源 |
> | 動作 | Microsoft.Devices/provisioningServices/listkeys/Action | 取得所有 IotDps 金鑰 |
> |  | **預先定義服務憑證** |  |
> | 動作 | Microsoft.Devices/provisioningServices/certificates/Read | 取得憑證 |
> | 動作 | Microsoft.Devices/provisioningServices/certificates/Write | 建立或更新憑證 |
> | 動作 | Microsoft.Devices/provisioningServices/certificates/Delete | 刪除憑證 |
> | 動作 | Microsoft.Devices/provisioningServices/certificates/generateVerificationCode/Action | 產生驗證碼 |
> | 動作 | Microsoft.Devices/provisioningServices/certificates/verify/Action | 驗證憑證資源 |
> |  | **預配服務/診斷設定** |  |
> | 動作 | Microsoft.Devices/provisioningServices/diagnosticSettings/read | 取得資源的診斷設定 |
> | 動作 | Microsoft.Devices/provisioningServices/diagnosticSettings/write | 建立或更新資源的診斷設定 |
> |  | **預先用服務金鑰** |  |
> | 動作 | Microsoft.Devices/provisioningServices/keys/listkeys/Action | 取得金鑰名稱的 IotDps 金鑰 |
> |  | **預先定義服務/記錄定義** |  |
> | 動作 | Microsoft.Devices/provisioningServices/logDefinitions/read | 取得佈建服務的可用記錄定義 |
> |  | **預先定義服務/指標定義** |  |
> | 動作 | Microsoft.Devices/provisioningServices/metricDefinitions/read | 取得佈建服務的可用計量 |
> |  | **預先定義服務或操作結果** |  |
> | 動作 | Microsoft.Devices/provisioningServices/operationresults/Read | 取得 DPS 作業結果 |
> |  | **預配服務/skus** |  |
> | 動作 | Microsoft.Devices/provisioningServices/skus/Read | 取得有效的 IotDps SKU |
> |  | **usages** |  |
> | 動作 | Microsoft.Devices/usages/Read | 取得此提供者的訂用帳戶使用量詳細資料。 |
> | 動作 | Microsoft.Devices/usages/Read | 取得此提供者的訂用帳戶使用量詳細資料。 |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

Azure 服務[:Azure 開發空間](../dev-spaces/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.DevSpaces/register/action | 向訂用帳戶註冊 Microsoft Dev Spaces 資源提供者 |
> |  | **controllers** |  |
> | 動作 | Microsoft.DevSpaces/controllers/read | 讀取 Azure Dev Spaces 控制器屬性 |
> | 動作 | Microsoft.DevSpaces/controllers/write | 建立或更新 Azure Dev Spaces 控制器屬性 |
> | 動作 | Microsoft.DevSpaces/controllers/delete | 刪除 Azure Dev Spaces 控制器與資料平面服務 |
> | 動作 | Microsoft.DevSpaces/controllers/listConnectionDetails/action | 列出 Azure Dev Spaces 控制器基礎結構的連接詳細資料 |
> |  | **位置** |  |
> | 動作 | 微軟.開發空間/位置/檢查容器主機映射/操作 | 檢查容器主機的現有控制器對應 |
> |  | **位置/檢查容器主機對應** |  |
> | 動作 | 微軟.開發空間/位置/檢查容器主機映射/讀取 | 檢查容器主機的現有控制器對應 |
> |  | **位置/動作結果** |  |
> | 動作 | 微軟.開發空間/位置/操作結果/讀取 | 非同步操作的讀取狀態 |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

Azure 服務[:Azure 實驗室服務](../lab-services/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.DevTestLab/register/action | 註冊訂用帳戶 |
> |  | **實驗室中心** |  |
> | 動作 | Microsoft.DevTestLab/labCenters/delete | 刪除實驗室中心。 |
> | 動作 | Microsoft.DevTestLab/labCenters/read | 讀取實驗室中心。 |
> | 動作 | Microsoft.DevTestLab/labCenters/write | 新增或修改實驗室中心。 |
> |  | **實驗室** |  |
> | 動作 | Microsoft.DevTestLab/labs/delete | 刪除實驗室。 |
> | 動作 | Microsoft.DevTestLab/labs/read | 讀取實驗室。 |
> | 動作 | Microsoft.DevTestLab/labs/write | 新增或修改實驗室。 |
> | 動作 | Microsoft.DevTestLab/labs/ListVhds/action | 列出可供建立自訂映像的磁碟映像。 |
> | 動作 | Microsoft.DevTestLab/labs/GenerateUploadUri/action | 產生 URI 以用來將自訂磁碟映像上傳到實驗室。 |
> | 動作 | Microsoft.DevTestLab/labs/CreateEnvironment/action | 在實驗室中建立虛擬機器。 |
> | 動作 | Microsoft.DevTestLab/labs/ClaimAnyVm/action | 在實驗室中宣告隨機的可宣告虛擬機器。 |
> | 動作 | Microsoft.DevTestLab/labs/ExportResourceUsage/action | 將實驗室資源使用狀況匯出到儲存體帳戶 |
> | 動作 | Microsoft.DevTestLab/labs/ImportVirtualMachine/action | 將虛擬機器匯入不同的實驗室。 |
> | 動作 | 微軟.開發測試實驗室/實驗室/確保當前使用者配置檔/操作 | 確保當前用戶在實驗室中具有有效的配置檔。 |
> |  | **實驗室/文物來源** |  |
> | 動作 | Microsoft.DevTestLab/labs/artifactSources/delete | 刪除構件來源。 |
> | 動作 | Microsoft.DevTestLab/labs/artifactSources/read | 讀取構件來源。 |
> | 動作 | Microsoft.DevTestLab/labs/artifactSources/write | 新增或修改構件來源。 |
> |  | **實驗室/工件源/手臂範本** |  |
> | 動作 | Microsoft.DevTestLab/labs/artifactSources/armTemplates/read | 讀取 Azure Resource Manager 範本。 |
> |  | **實驗室/工件來源/工件** |  |
> | 動作 | Microsoft.DevTestLab/labs/artifactSources/artifacts/read | 讀取構件。 |
> | 動作 | Microsoft.DevTestLab/labs/artifactSources/artifacts/GenerateArmTemplate/action | 為給定專案生成 Azure 資源管理員範本,將所需檔上載到存儲帳戶,並驗證生成的專案。 |
> |  | **labs/costs** |  |
> | 動作 | Microsoft.DevTestLab/labs/costs/read | 讀取成本。 |
> | 動作 | Microsoft.DevTestLab/labs/costs/write | 新增或修改成本。 |
> |  | **實驗室/自訂影像** |  |
> | 動作 | Microsoft.DevTestLab/labs/customImages/delete | 刪除自訂映像。 |
> | 動作 | Microsoft.DevTestLab/labs/customImages/read | 讀取自訂映像。 |
> | 動作 | Microsoft.DevTestLab/labs/customImages/write | 新增或修改自訂映像。 |
> |  | **labs/formulas** |  |
> | 動作 | Microsoft.DevTestLab/labs/formulas/delete | 刪除公式。 |
> | 動作 | Microsoft.DevTestLab/labs/formulas/read | 讀取公式。 |
> | 動作 | Microsoft.DevTestLab/labs/formulas/write | 新增或修改公式。 |
> |  | **實驗室/畫廊圖片** |  |
> | 動作 | Microsoft.DevTestLab/labs/galleryImages/read | 讀取資源庫映像。 |
> |  | **實驗室/通知通道** |  |
> | 動作 | Microsoft.DevTestLab/labs/notificationChannels/delete | 刪除通知通道。 |
> | 動作 | Microsoft.DevTestLab/labs/notificationChannels/read | 讀取通知通道。 |
> | 動作 | Microsoft.DevTestLab/labs/notificationChannels/write | 添加或修改通知通道。 |
> | 動作 | Microsoft.DevTestLab/labs/notificationChannels/Notify/action | 對所提供的通道傳送通知。 |
> |  | **實驗室/策略集** |  |
> | 動作 | Microsoft.DevTestLab/labs/policySets/read | 讀取原則集合。 |
> | 動作 | Microsoft.DevTestLab/labs/policySets/EvaluatePolicies/action | 評估實驗室原則。 |
> |  | **實驗室/策略集/策略** |  |
> | 動作 | Microsoft.DevTestLab/labs/policySets/policies/delete | 刪除原則。 |
> | 動作 | Microsoft.DevTestLab/labs/policySets/policies/read | 讀取原則。 |
> | 動作 | Microsoft.DevTestLab/labs/policySets/policies/write | 新增或修改原則。 |
> |  | **labs/schedules** |  |
> | 動作 | Microsoft.DevTestLab/labs/schedules/delete | 刪除排程。 |
> | 動作 | Microsoft.DevTestLab/labs/schedules/read | 讀取排程。 |
> | 動作 | Microsoft.DevTestLab/labs/schedules/write | 新增或修改排程。 |
> | 動作 | Microsoft.DevTestLab/labs/schedules/Execute/action | 執行排程。 |
> | 動作 | Microsoft.DevTestLab/labs/schedules/ListApplicable/action | 列出所有適用排程 |
> |  | **labs/serviceRunners** |  |
> | 動作 | Microsoft.DevTestLab/labs/serviceRunners/delete | 刪除服務執行器。 |
> | 動作 | Microsoft.DevTestLab/labs/serviceRunners/read | 讀取服務執行器。 |
> | 動作 | Microsoft.DevTestLab/labs/serviceRunners/write | 新增或修改服務執行器。 |
> |  | **實驗室/共用庫** |  |
> | 動作 | 微軟.開發測試實驗室/實驗室/共用庫/刪除 | 刪除共用的庫。 |
> | 動作 | 微軟.開發測試實驗室/實驗室/共用畫廊/閱讀 | 閱讀共用的圖庫。 |
> | 動作 | 微軟.開發測試實驗室/實驗室/共用畫廊/寫入 | 添加或修改共用庫。 |
> |  | **實驗室/共用庫/共用影像** |  |
> | 動作 | 微軟.DevTestLab/實驗室/共用庫/共用圖像/刪除 | 刪除共用的圖像。 |
> | 動作 | 微軟.DevTestLab/實驗室/共用庫/共用圖像/閱讀 | 讀取共用的圖像。 |
> | 動作 | 微軟.DevTestLab/實驗室/共用庫/共用圖像/寫入 | 添加或修改共用圖像。 |
> |  | **labs/users** |  |
> | 動作 | Microsoft.DevTestLab/labs/users/delete | 刪除使用者設定檔。 |
> | 動作 | Microsoft.DevTestLab/labs/users/read | 讀取使用者設定檔。 |
> | 動作 | Microsoft.DevTestLab/labs/users/write | 新增或修改使用者設定檔。 |
> |  | **labs/users/disks** |  |
> | 動作 | Microsoft.DevTestLab/labs/users/disks/delete | 刪除磁碟。 |
> | 動作 | Microsoft.DevTestLab/labs/users/disks/read | 讀取磁碟。 |
> | 動作 | Microsoft.DevTestLab/labs/users/disks/write | 新增或修改磁碟。 |
> | 動作 | Microsoft.DevTestLab/labs/users/disks/Attach/action | 將磁碟連結至虛擬機器並建立磁碟租用。 |
> | 動作 | Microsoft.DevTestLab/labs/users/disks/Detach/action | 將連結至虛擬機器的磁碟中斷連結並中斷磁碟租用。 |
> |  | **labs/users/environments** |  |
> | 動作 | Microsoft.DevTestLab/labs/users/environments/delete | 刪除環境。 |
> | 動作 | Microsoft.DevTestLab/labs/users/environments/read | 讀取環境。 |
> | 動作 | Microsoft.DevTestLab/labs/users/environments/write | 新增或修改環境。 |
> |  | **labs/users/secrets** |  |
> | 動作 | Microsoft.DevTestLab/labs/users/secrets/delete | 刪除祕密。 |
> | 動作 | Microsoft.DevTestLab/labs/users/secrets/read | 讀取祕密。 |
> | 動作 | Microsoft.DevTestLab/labs/users/secrets/write | 新增或修改祕密。 |
> |  | **實驗室/使用者/服務結構** |  |
> | 動作 | Microsoft.DevTestLab/labs/users/serviceFabrics/delete | 刪除 Service Fabric。 |
> | 動作 | Microsoft.DevTestLab/labs/users/serviceFabrics/read | 讀取 Service Fabric。 |
> | 動作 | Microsoft.DevTestLab/labs/users/serviceFabrics/write | 新增或修改 Service Fabric。 |
> | 動作 | Microsoft.DevTestLab/labs/users/serviceFabrics/Start/action | 啟動 Service Fabric。 |
> | 動作 | Microsoft.DevTestLab/labs/users/serviceFabrics/Stop/action | 停止 Service Fabric |
> | 動作 | Microsoft.DevTestLab/labs/users/serviceFabrics/ListApplicableSchedules/action | 列出適用的啟動/停止排程 (若有的話)。 |
> |  | **實驗室/使用者/服務結構/時程表** |  |
> | 動作 | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/delete | 刪除排程。 |
> | 動作 | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/read | 讀取排程。 |
> | 動作 | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/write | 新增或修改排程。 |
> | 動作 | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/Execute/action | 執行排程。 |
> |  | **實驗室/虛擬機器** |  |
> | 動作 | Microsoft.DevTestLab/labs/virtualMachines/delete | 刪除虛擬機器。 |
> | 動作 | Microsoft.DevTestLab/labs/virtualMachines/read | 讀取虛擬機器。 |
> | 動作 | Microsoft.DevTestLab/labs/virtualMachines/write | 新增或修改虛擬機器。 |
> | 動作 | Microsoft.DevTestLab/labs/virtualMachines/AddDataDisk/action | 將新的或現有的資料磁碟連結至虛擬機器。 |
> | 動作 | Microsoft.DevTestLab/labs/virtualMachines/ApplyArtifacts/action | 將構件套用至虛擬機器。 |
> | 動作 | Microsoft.DevTestLab/labs/virtualMachines/Claim/action | 取得現有虛擬機器的擁有權 |
> | 動作 | 微軟.開發測試實驗室/實驗室/虛擬機/清除結果/操作 | 清除虛擬機的項目結果。 |
> | 動作 | Microsoft.DevTestLab/labs/virtualMachines/DetachDataDisk/action | 中斷指定磁碟與虛擬機器的連結。 |
> | 動作 | Microsoft.DevTestLab/labs/virtualMachines/GetRdpFileContents/action | 取得代表虛擬機器 RDP 檔案內容的字串 |
> | 動作 | Microsoft.DevTestLab/labs/virtualMachines/ListApplicableSchedules/action | 列出適用的啟動/停止排程 (若有的話)。 |
> | 動作 | Microsoft.DevTestLab/labs/virtualMachines/Redeploy/action | 重新部署虛擬機器 |
> | 動作 | Microsoft.DevTestLab/labs/virtualMachines/Resize/action | 調整虛擬機器的大小。 |
> | 動作 | Microsoft.DevTestLab/labs/virtualMachines/Restart/action | 重新啟動虛擬機器。 |
> | 動作 | Microsoft.DevTestLab/labs/virtualMachines/Start/action | 啟動虛擬機器。 |
> | 動作 | Microsoft.DevTestLab/labs/virtualMachines/Stop/action | 停止虛擬機器 |
> | 動作 | Microsoft.DevTestLab/labs/virtualMachines/TransferDisks/action | 傳送所有連結至虛擬機器的資料磁碟，由目前的使用者擁有。 |
> | 動作 | Microsoft.DevTestLab/labs/virtualMachines/UnClaim/action | 釋放現有虛擬機器的擁有權 |
> |  | **實驗室/虛擬機器/時程表** |  |
> | 動作 | Microsoft.DevTestLab/labs/virtualMachines/schedules/delete | 刪除排程。 |
> | 動作 | Microsoft.DevTestLab/labs/virtualMachines/schedules/read | 讀取排程。 |
> | 動作 | Microsoft.DevTestLab/labs/virtualMachines/schedules/write | 新增或修改排程。 |
> | 動作 | Microsoft.DevTestLab/labs/virtualMachines/schedules/Execute/action | 執行排程。 |
> |  | **實驗室/虛擬網路** |  |
> | 動作 | Microsoft.DevTestLab/labs/virtualNetworks/delete | 刪除虛擬網路。 |
> | 動作 | Microsoft.DevTestLab/labs/virtualNetworks/read | 讀取虛擬網路。 |
> | 動作 | Microsoft.DevTestLab/labs/virtualNetworks/write | 新增或修改虛擬網路。 |
> |  | **實驗室/虛擬網路/堡壘主機** |  |
> | 動作 | 微軟.開發測試實驗室/實驗室/虛擬網路/堡壘主機/刪除 | 刪除堡壘主機。 |
> | 動作 | 微軟.開發測試實驗室/實驗室/虛擬網路/堡壘主機/讀取 | 讀取堡壘主機。 |
> | 動作 | 微軟.開發測試實驗室/實驗室/虛擬網路/堡壘主機/寫入 | 添加或修改堡壘主機。 |
> |  | **實驗室/vmPools** |  |
> | 動作 | Microsoft.DevTestLab/labs/vmPools/delete | 刪除虛擬機器集區。 |
> | 動作 | Microsoft.DevTestLab/labs/vmPools/read | 讀取虛擬機器集區。 |
> | 動作 | Microsoft.DevTestLab/labs/vmPools/write | 新增或修改虛擬機器集區。 |
> |  | **位置/操作** |  |
> | 動作 | Microsoft.DevTestLab/locations/operations/read | 讀取作業。 |
> |  | **附表** |  |
> | 動作 | Microsoft.DevTestLab/schedules/delete | 刪除排程。 |
> | 動作 | Microsoft.DevTestLab/schedules/read | 讀取排程。 |
> | 動作 | Microsoft.DevTestLab/schedules/write | 新增或修改排程。 |
> | 動作 | Microsoft.DevTestLab/schedules/Execute/action | 執行排程。 |
> | 動作 | Microsoft.DevTestLab/schedules/Retarget/action | 更新排程的目標資源識別碼。 |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

Azure 服務[:Azure 宇宙資料庫](../cosmos-db/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.DocumentDB/register/action |  為訂用帳戶註冊 Microsoft DocumentDB 資源提供者 |
> |  | **databaseAccountNames** |  |
> | 動作 | Microsoft.DocumentDB/databaseAccountNames/read | 檢查名稱可用性。 |
> |  | **databaseAccounts** |  |
> | 動作 | Microsoft.DocumentDB/databaseAccounts/read | 讀取資料庫帳戶。 |
> | 動作 | Microsoft.DocumentDB/databaseAccounts/write | 更新資料庫帳戶。 |
> | 動作 | Microsoft.DocumentDB/databaseAccounts/listKeys/action | 列出資料庫帳戶的金鑰 |
> | 動作 | Microsoft.DocumentDB/databaseAccounts/readonlykeys/action | 讀取資料庫帳戶的唯讀金鑰。 |
> | 動作 | Microsoft.DocumentDB/databaseAccounts/regenerateKey/action | 輪替資料庫帳戶的金鑰 |
> | 動作 | Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/action | 取得資料庫帳戶的連接字串 |
> | 動作 | Microsoft.DocumentDB/databaseAccounts/changeResourceGroup/action | 變更資料庫帳戶的資源群組 |
> | 動作 | Microsoft.DocumentDB/databaseAccounts/failoverPriorityChange/action | 變更資料庫帳戶區域的容錯移轉優先順序。 這會用來執行手動容錯移轉作業 |
> | 動作 | Microsoft.DocumentDB/databaseAccounts/offlineRegion/action | 讓資料庫帳戶的某個區域離線。  |
> | 動作 | Microsoft.DocumentDB/databaseAccounts/onlineRegion/action | 讓資料庫帳戶的某個區域上線。 |
> | 動作 | Microsoft.DocumentDB/databaseAccounts/delete | 刪除資料庫帳戶。 |
> | 動作 | 微軟.文件資料庫/資料庫帳戶/獲取備份策略/操作 | 取得資料庫帳戶的備份原則 |
> | 動作 | Microsoft.DocumentDB/databaseAccounts/restore/action | 提交還原要求 |
> | 動作 | Microsoft.DocumentDB/databaseAccounts/backup/action | 提交要求以設定備份 |
> |  | **資料庫帳戶/卡桑德拉關鍵空間** |  |
> | 動作 | 微軟.文檔資料庫/資料庫帳戶/卡桑德拉關鍵空間/寫入 | 創建卡桑德拉鍵空間。 |
> | 動作 | 微軟.文檔資料庫/資料庫帳戶/卡桑德拉關鍵空間/讀取 | 閱讀卡桑德拉鍵空間或列出所有卡桑德拉鍵空間。 |
> | 動作 | 微軟.文檔資料庫/資料庫帳戶/卡桑德拉關鍵空間/刪除 | 刪除卡桑德拉鍵空間。 |
> |  | **資料庫帳戶/卡桑德拉關鍵空間/操作結果** |  |
> | 動作 | 微軟.文檔資料庫/資料庫帳戶/卡桑德拉關鍵空間/操作結果/讀取 | 讀取非同步操作的狀態。 |
> |  | **資料庫帳戶/卡桑德拉關鍵空間/表** |  |
> | 動作 | 微軟.文檔資料庫/資料庫帳戶/卡桑德拉關鍵空間/表/寫入 | 創建或更新卡桑德拉錶。 |
> | 動作 | 微軟.文檔資料庫/資料庫帳戶/卡桑德拉關鍵空間/表/讀取 | 閱讀卡桑德拉錶或列出所有卡桑德拉錶。 |
> | 動作 | 微軟.文檔資料庫/資料庫帳戶/卡桑德拉關鍵空間/表/刪除 | 刪除卡桑德拉錶。 |
> |  | **資料庫帳戶/卡桑德拉關鍵空間/表/操作結果** |  |
> | 動作 | 微軟.文檔資料庫/資料庫帳戶/卡桑德拉關鍵空間/表/操作結果/讀取 | 讀取非同步操作的狀態。 |
> |  | **資料庫帳戶/卡桑德拉關鍵空間/表/輸送量設置** |  |
> | 動作 | 微軟.文檔資料庫/資料庫帳戶/卡桑德拉關鍵空間/表/輸送量設置/寫入 | 更新卡桑德拉錶輸送量。 |
> | 動作 | 微軟.文檔資料庫/資料庫帳戶/卡桑德拉關鍵空間/表/輸送量設置/讀取 | 讀取卡桑德拉錶輸送量。 |
> |  | **資料庫帳戶/卡桑德拉關鍵空間/表/輸送量設置/操作結果** |  |
> | 動作 | 微軟.文檔資料庫/資料庫帳戶/卡桑德拉關鍵空間/表/輸送量設置/操作結果/讀取 | 讀取非同步操作的狀態。 |
> |  | **資料庫帳戶/卡桑德拉關鍵空間/輸送量設置** |  |
> | 動作 | 微軟.文檔資料庫/資料庫帳戶/卡桑德拉關鍵空間/輸送量設置/寫入 | 更新卡桑德拉金鑰空間輸送量。 |
> | 動作 | 微軟.文檔資料庫/資料庫帳戶/卡桑德拉關鍵空間/輸送量設置/讀取 | 讀取卡桑德拉鍵空間輸送量。 |
> |  | **資料庫帳戶/卡桑德拉關鍵空間/輸送量設置/操作結果** |  |
> | 動作 | 微軟.文檔資料庫/資料庫帳戶/卡桑德拉關鍵空間/輸送量設置/操作結果/讀取 | 讀取非同步操作的狀態。 |
> |  | **資料庫帳戶/資料庫/集合/指標定義** |  |
> | 動作 | Microsoft.DocumentDB/databaseAccounts/databases/collections/metricDefinitions/read | 讀取集合計量定義。 |
> |  | **資料庫帳戶/資料庫/集合/指標** |  |
> | 動作 | Microsoft.DocumentDB/databaseAccounts/databases/collections/metrics/read | 讀取集合計量。 |
> |  | **資料庫帳戶/資料庫/集合/分區KeyRangeId/指標** |  |
> | 動作 | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitionKeyRangeId/metrics/read | 讀取資料庫帳戶分割區索引鍵層級計量 |
> |  | **資料庫帳戶/資料庫/集合/分區** |  |
> | 動作 | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/read | 讀取集合中的資料庫帳戶分割區 |
> |  | **資料庫帳戶/資料庫/集合/分區/指標** |  |
> | 動作 | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/metrics/read | 讀取資料庫帳戶分割區層級計量 |
> |  | **資料庫帳戶/資料庫/集合/分區/用法** |  |
> | 動作 | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/usages/read | 讀取資料庫帳戶分割區層級使用量 |
> |  | **資料庫帳戶/資料庫/集合/使用方式** |  |
> | 動作 | Microsoft.DocumentDB/databaseAccounts/databases/collections/usages/read | 讀取集合使用量。 |
> |  | **資料庫帳戶/資料庫/指標定義** |  |
> | 動作 | Microsoft.DocumentDB/databaseAccounts/databases/metricDefinitions/read | 讀取資料庫計量定義 |
> |  | **資料庫帳戶/資料庫/指標** |  |
> | 動作 | Microsoft.DocumentDB/databaseAccounts/databases/metrics/read | 讀取資料庫計量。 |
> |  | **資料庫帳戶/資料庫/使用方式** |  |
> | 動作 | Microsoft.DocumentDB/databaseAccounts/databases/usages/read | 讀取資料庫使用量。 |
> |  | **資料庫帳戶/格林林資料庫** |  |
> | 動作 | 微軟.文檔資料庫/資料庫帳戶/格林林資料庫/寫入 | 創建格雷姆林資料庫。 |
> | 動作 | 微軟.文檔資料庫/資料庫帳戶/格林林資料庫/讀取 | 讀取格雷姆林資料庫或列出所有格雷姆林資料庫。 |
> | 動作 | 微軟.文檔資料庫/資料庫帳戶/格林林資料庫/刪除 | 刪除格雷姆林資料庫。 |
> |  | **資料庫帳戶/格林林資料庫/圖形** |  |
> | 動作 | 微軟.文檔資料庫/資料庫帳戶/格林林資料庫/圖形/寫入 | 創建或更新格雷姆林圖。 |
> | 動作 | 微軟.文檔資料庫/資料庫帳戶/格林林資料庫/圖形/讀取 | 閱讀格雷姆林圖或列出所有格雷姆林圖。 |
> | 動作 | 微軟.文檔資料庫/資料庫帳戶/格林林資料庫/圖形/刪除 | 刪除格雷姆林圖。 |
> |  | **資料庫帳戶/格林林資料庫/圖形/操作結果** |  |
> | 動作 | 微軟.文檔資料庫/資料庫帳戶/格林林資料庫/圖形/操作結果/讀取 | 讀取非同步操作的狀態。 |
> |  | **資料庫帳戶/格林林資料庫/圖形/輸送量設置** |  |
> | 動作 | 微軟.文檔資料庫/資料庫帳戶/格林林資料庫/圖形/輸送量設置/寫入 | 更新格雷姆林圖形輸送量。 |
> | 動作 | 微軟.文檔資料庫/資料庫帳戶/格林林資料庫/圖形/輸送量設置/讀取 | 讀取格雷姆林圖形輸送量。 |
> |  | **資料庫帳戶/格林林資料庫/圖形/輸送量設置/操作結果** |  |
> | 動作 | 微軟.文檔資料庫/資料庫帳戶/格林林資料庫/圖形/輸送量設置/操作結果/讀取 | 讀取非同步操作的狀態。 |
> |  | **資料庫帳戶/格林林資料庫/操作結果** |  |
> | 動作 | 微軟.文檔資料庫/資料庫帳戶/格林林資料庫/操作結果/讀取 | 讀取非同步操作的狀態。 |
> |  | **資料庫帳戶/格林林資料庫/輸送量設置** |  |
> | 動作 | 微軟.文檔資料庫/資料庫帳戶/格林林資料庫/輸送量設置/寫入 | 更新格雷姆林資料庫輸送量。 |
> | 動作 | 微軟.文檔資料庫/資料庫帳戶/格雷姆林資料庫/輸送量設置/讀取 | 讀取格雷姆林資料庫輸送量。 |
> |  | **資料庫帳戶/格林林資料庫/輸送量設定/操作結果** |  |
> | 動作 | 微軟.文檔資料庫/資料庫帳戶/格雷姆林資料庫/輸送量設置/操作結果/讀取 | 讀取非同步操作的狀態。 |
> |  | **資料庫帳戶/指標定義** |  |
> | 動作 | Microsoft.DocumentDB/databaseAccounts/metricDefinitions/read | 讀取資料庫帳戶的計量定義。 |
> |  | **資料庫會計/指標** |  |
> | 動作 | Microsoft.DocumentDB/databaseAccounts/metrics/read | 讀取資料庫帳戶的計量。 |
> |  | **資料庫帳戶/蒙戈布資料庫** |  |
> | 動作 | 微軟.文檔資料庫/資料庫帳戶/蒙戈布資料庫/寫入 | 創建蒙戈DB資料庫。 |
> | 動作 | 微軟.文檔資料庫/資料庫帳戶/蒙戈布資料庫/讀取 | 讀取 MongoDB 資料庫或列出所有蒙戈DB資料庫。 |
> | 動作 | 微軟.文檔資料庫/資料庫帳戶/蒙戈布資料庫/刪除 | 刪除蒙戈DB資料庫。 |
> |  | **資料庫帳戶/mongodb 資料庫/集合** |  |
> | 動作 | 微軟.文檔資料庫/資料庫帳戶/蒙戈布資料庫/集合/寫入 | 創建或更新 MongoDB 集合。 |
> | 動作 | 微軟.文檔資料庫/資料庫帳戶/蒙戈布資料庫/集合/讀取 | 讀取 MongoDB 集合或列出所有蒙戈DB集合。 |
> | 動作 | 微軟.文檔資料庫/資料庫帳戶/蒙戈布資料庫/集合/刪除 | 刪除 MongoDB 集合。 |
> |  | **資料庫帳戶/mongodb 資料庫/集合/操作結果** |  |
> | 動作 | 微軟.文檔資料庫/資料庫帳戶/蒙戈布資料庫/集合/操作結果/讀取 | 讀取非同步操作的狀態。 |
> |  | **資料庫帳戶/mongodb 資料庫/集合/輸送量設置** |  |
> | 動作 | 微軟.文檔資料庫/資料庫帳戶/蒙戈布資料庫/集合/輸送量設置/寫入 | 更新 MongoDB 集合輸送量。 |
> | 動作 | 微軟.文檔資料庫/資料庫帳戶/蒙戈布資料庫/集合/輸送量設置/讀取 | 讀取蒙戈DB集合輸送量。 |
> |  | **資料庫帳戶/mongodb 資料庫/集合/輸送量設置/操作結果** |  |
> | 動作 | 微軟.文檔資料庫/資料庫帳戶/蒙戈布資料庫/集合/輸送量設置/操作結果/讀取 | 讀取非同步操作的狀態。 |
> |  | **資料庫帳戶/mongodb 資料庫/操作結果** |  |
> | 動作 | 微軟.文檔資料庫/資料庫帳戶/蒙戈布資料庫/操作結果/讀取 | 讀取非同步操作的狀態。 |
> |  | **資料庫帳戶/mongodb 資料庫/輸送量設置** |  |
> | 動作 | 微軟.文檔資料庫/資料庫帳戶/蒙戈布資料庫/輸送量設置/寫入 | 更新 MongoDB 資料庫輸送量。 |
> | 動作 | 微軟.文檔資料庫/資料庫帳戶/蒙戈布資料庫/輸送量設置/讀取 | 讀取蒙戈DB資料庫輸送量。 |
> |  | **資料庫帳戶/mongodb 資料庫/輸送量設定/操作結果** |  |
> | 動作 | 微軟.文檔資料庫/資料庫帳戶/蒙戈布資料庫/輸送量設置/操作結果/讀取 | 讀取非同步操作的狀態。 |
> |  | **資料庫帳戶/筆記本工作區** |  |
> | 動作 | 微軟.文檔資料庫/資料庫帳戶/筆記本工作區/寫入 | 建立或更新筆記本工作區 |
> | 動作 | 微軟.文檔資料庫/資料庫帳戶/筆記本工作區/讀取 | 閱讀筆記本工作區 |
> | 動作 | 微軟.文檔資料庫/資料庫帳戶/筆記本工作區/刪除 | 刪除筆記本工作區 |
> | 動作 | 微軟.文件資料庫/資料庫帳戶/筆記本工作區/清單連接資訊/操作 | 列出筆記本工作區的連線資訊 |
> |  | **資料庫帳戶/筆記本工作區/操作結果** |  |
> | 動作 | 微軟.文檔資料庫/資料庫帳戶/筆記本工作/操作結果/讀取 | 讀取筆記本工作區上非同步操作的狀態 |
> |  | **資料庫帳戶/動作結果** |  |
> | 動作 | Microsoft.DocumentDB/databaseAccounts/operationResults/read | 讀取非同步作業的狀態 |
> |  | **資料庫帳戶/百分位數** |  |
> | 動作 | Microsoft.DocumentDB/databaseAccounts/percentile/read | 讀取複寫延遲的百分位數 |
> |  | **資料庫帳戶/百分位數/指標** |  |
> | 動作 | Microsoft.DocumentDB/databaseAccounts/percentile/metrics/read | 讀取延遲計量 |
> |  | **資料庫帳戶/百分比/源區域/目標區域/指標** |  |
> | 動作 | Microsoft.DocumentDB/databaseAccounts/percentile/sourceRegion/targetRegion/metrics/read | 讀取指定來源和目標區域的延遲計量 |
> |  | **資料庫帳戶/百分位數/目標區域/指標** |  |
> | 動作 | Microsoft.DocumentDB/databaseAccounts/percentile/targetRegion/metrics/read | 讀取指定目標區域的延遲計量 |
> |  | **資料庫帳戶/私有端點連接Proxies** |  |
> | 動作 | 微軟.文檔資料庫/資料庫帳戶/私有端點連接Proxies/讀取 | 讀取資料庫帳戶的專用終結點連線代理 |
> | 動作 | 微軟.文檔資料庫/資料庫帳戶/私有端點連接Proxies/寫入 | 建立或更新資料庫帳戶的專用終結點連線代理 |
> | 動作 | 微軟.文件資料庫/資料庫帳戶/私人端接連接Proxies/刪除 | 刪除資料庫帳戶的專用終結點連線代理 |
> | 動作 | 微軟.文檔資料庫/資料庫帳戶/私有端點連接Proxies/驗證/操作 | 驗證資料庫帳戶的專用終結點連線代理 |
> |  | **資料庫帳戶/私有端接連線Proxies/操作結果** |  |
> | 動作 | 微軟.文檔資料庫/資料庫帳戶/私有端點連接Proxies/操作結果/讀取 | 讀取專用終結點連線代理非同步操作的狀態 |
> |  | **資料庫帳戶/專用終結點連接** |  |
> | 動作 | 微軟.文件資料庫/資料庫帳戶/私有端點連接/讀取 | 讀取專用終結點連線或列出資料庫帳戶的所有專用終結點連接 |
> | 動作 | 微軟.文件資料庫/資料庫帳戶/私有端點連接/寫入 | 建立或更新資料庫帳戶的專用終結點連線 |
> | 動作 | 微軟.文件資料庫/資料庫帳戶/專用端點連接/刪除 | 刪除資料庫帳戶的專用終結點連線 |
> |  | **資料庫帳戶/私有終結點連接/操作結果** |  |
> | 動作 | 微軟.文檔資料庫/資料庫帳戶/私有終結點連接/操作結果/讀取 | 讀取私有終結點進行非同步操作的狀態 |
> |  | **資料庫帳戶/私有連結資源** |  |
> | 動作 | 微軟.文檔資料庫/資料庫帳戶/私有連結資源/讀取 | 讀取專用連結資源或列出資料庫帳戶的所有專用連結資源 |
> |  | **資料庫帳戶/唯讀鍵** |  |
> | 動作 | Microsoft.DocumentDB/databaseAccounts/readonlykeys/read | 讀取資料庫帳戶的唯讀金鑰。 |
> |  | **資料庫帳戶/區域/資料庫/集合/指標** |  |
> | 動作 | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/metrics/read | 讀取區域集合計量。 |
> |  | **資料庫帳戶/區域/資料庫/集合/分區KeyRangeId/指標** |  |
> | 動作 | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitionKeyRangeId/metrics/read | 讀取區域資料庫帳戶分割區索引鍵層級計量 |
> |  | **資料庫帳戶/區域/資料庫/集合/分區** |  |
> | 動作 | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitions/read | 讀取集合中的區域資料庫帳戶分割區 |
> |  | **資料庫帳戶/區域/資料庫/集合/分區/指標** |  |
> | 動作 | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitions/metrics/read | 讀取區域資料庫帳戶分割區層級計量 |
> |  | **資料庫會計/區域/指標** |  |
> | 動作 | Microsoft.DocumentDB/databaseAccounts/region/metrics/read | 讀取區域和資料庫帳戶計量。 |
> |  | **資料庫帳戶/sql資料庫** |  |
> | 動作 | 微軟.文件資料庫/資料庫帳戶/sql資料庫/寫入 | 建立 SQL 資料庫。 |
> | 動作 | 微軟.文件資料庫/資料庫帳戶/sql資料庫/讀取 | 讀取 SQL 資料庫或列出所有 SQL 資料庫。 |
> | 動作 | 微軟.文件資料庫/資料庫帳戶/sql資料庫/刪除 | 刪除 SQL 資料庫。 |
> |  | **資料庫帳戶/sql資料庫/容器** |  |
> | 動作 | 微軟.文件資料庫/資料庫帳戶/sql資料庫/容器/寫入 | 建立或更新 SQL 容器。 |
> | 動作 | 微軟.文件資料庫/資料庫帳戶/sql資料庫/容器/讀取 | 讀取 SQL 容器或列出所有 SQL 容器。 |
> | 動作 | 微軟.文件資料庫/資料庫帳戶/sql資料庫/容器/刪除 | 刪除 SQL 容器。 |
> |  | **資料庫帳號/sql資料庫/容器/操作結果** |  |
> | 動作 | 微軟.文件資料庫/資料庫帳戶/sql資料庫/容器/操作結果/讀取 | 讀取非同步操作的狀態。 |
> |  | **資料庫帳號/sql資料庫/容器/儲存程式** |  |
> | 動作 | 微軟.文件資料庫/資料庫帳戶/sql資料庫/容器/儲存程式/寫入 | 創建或更新 SQL 儲存過程。 |
> | 動作 | 微軟.文件資料庫/資料庫帳戶/sql資料庫/容器/儲存程式/讀取 | 讀取 SQL 儲存過程或列出所有 SQL 儲存過程。 |
> | 動作 | 微軟.文件資料庫/資料庫帳戶/sql資料庫/容器/儲存程式/刪除 | 刪除 SQL 儲存過程。 |
> |  | **資料庫帳號/sql資料庫/容器/儲存程式/操作結果** |  |
> | 動作 | 微軟.文件資料庫/資料庫帳戶/sql資料庫/容器/儲存程式/操作結果/讀取 | 讀取非同步操作的狀態。 |
> |  | **資料庫帳號/sql資料庫/容器/輸送量設定** |  |
> | 動作 | 微軟.文件資料庫/資料庫帳戶/sql資料庫/容器/輸送量設置/寫入 | 更新 SQL 容器輸送量。 |
> | 動作 | 微軟.文件資料庫/資料庫帳戶/sql資料庫/容器/輸送量設置/讀取 | 讀取 SQL 容器輸送量。 |
> |  | **資料庫帳號/sql資料庫/容器/輸送量設定/操作結果** |  |
> | 動作 | 微軟.文件資料庫/資料庫帳戶/sql資料庫/容器/輸送量設置/操作結果/讀取 | 讀取非同步操作的狀態。 |
> |  | **資料庫帳戶/sql資料庫/容器/觸發器** |  |
> | 動作 | 微軟.文件資料庫/資料庫帳戶/sql資料庫/容器/觸發器/寫入 | 創建或更新 SQL 觸發器。 |
> | 動作 | 微軟.文件資料庫/資料庫帳戶/sql資料庫/容器/觸發器/讀取 | 讀取 SQL 觸發器或列出所有 SQL 觸發器。 |
> | 動作 | 微軟.文件資料庫/資料庫帳戶/sql資料庫/容器/觸發器/刪除 | 刪除 SQL 觸發器。 |
> |  | **資料庫帳號/sql資料庫/容器/觸發器/操作結果** |  |
> | 動作 | 微軟.文件資料庫/資料庫帳戶/sql資料庫/容器/觸發器/操作結果/讀取 | 讀取非同步操作的狀態。 |
> |  | **資料庫帳戶/sql資料庫/容器/使用者定義函數** |  |
> | 動作 | 微軟.文件資料庫/資料庫帳戶/sql資料庫/容器/使用者定義功能/寫入 | 創建或更新 SQL 使用者定義的函數。 |
> | 動作 | 微軟.文件資料庫/資料庫帳戶/sql資料庫/容器/使用者定義功能/讀取 | 讀取 SQL 使用者定義的函數或列出所有 SQL 使用者定義的函數。 |
> | 動作 | 微軟.文件資料庫/資料庫帳戶/sql資料庫/容器/使用者定義功能/刪除 | 刪除 SQL 使用者定義的函數。 |
> |  | **資料庫帳號/sql資料庫/容器/使用者定義的函數/操作結果** |  |
> | 動作 | Microsoft.文檔資料庫/資料庫帳戶/sql資料庫/容器/使用者定義的功能/操作結果/讀取 | 讀取非同步操作的狀態。 |
> |  | **資料庫帳號/sql資料庫/動作結果** |  |
> | 動作 | 微軟.文件資料庫/資料庫帳戶/sql資料庫/操作結果/讀取 | 讀取非同步操作的狀態。 |
> |  | **資料庫帳號/sql資料庫/輸送量設定** |  |
> | 動作 | 微軟.文件資料庫/資料庫帳戶/sql資料庫/輸送量設置/寫入 | 更新 SQL 資料庫輸送量。 |
> | 動作 | 微軟.文檔資料庫/資料庫帳戶/sql資料庫/輸送量設置/讀取 | 讀取 SQL 資料庫輸送量。 |
> |  | **資料庫帳號/sql資料庫/輸送量設定/操作結果** |  |
> | 動作 | 微軟.文件資料庫/資料庫帳戶/sql資料庫/輸送量設置/操作結果/讀取 | 讀取非同步操作的狀態。 |
> |  | **資料庫帳戶/表** |  |
> | 動作 | 微軟.文件資料庫/資料庫帳戶/表/寫入 | 創建或更新表。 |
> | 動作 | 微軟.文件資料庫/資料庫帳戶/表/讀取 | 閱讀一個表或列出所有表。 |
> | 動作 | 微軟.文件資料庫/資料庫帳戶/表/刪除 | 刪除表。 |
> |  | **資料庫會計/表/動作結果** |  |
> | 動作 | 微軟.文檔資料庫/資料庫帳戶/表/操作結果/讀取 | 讀取非同步操作的狀態。 |
> |  | **資料庫帳戶/表/輸送量設定** |  |
> | 動作 | 微軟.文檔資料庫/資料庫帳戶/表/輸送量設置/寫入 | 更新表輸送量。 |
> | 動作 | 微軟.文檔資料庫/資料庫帳戶/表/輸送量設置/讀取 | 讀取表輸送量。 |
> |  | **資料庫帳戶/表/輸送量設定/操作結果** |  |
> | 動作 | 微軟.文檔資料庫/資料庫帳戶/表/輸送量設置/操作結果/讀取 | 讀取非同步操作的狀態。 |
> |  | **資料庫帳戶/用法** |  |
> | 動作 | Microsoft.DocumentDB/databaseAccounts/usages/read | 讀取資料庫帳戶的使用量。 |
> |  | **位置** |  |
> | 動作 | Microsoft.DocumentDB/locations/deleteVirtualNetworkOrSubnets/action | 通知 Microsoft.DocumentDB 正在刪除虛擬網路或子網路 |
> |  | **位置/移除虛擬網路或子網路/操作結果** |  |
> | 動作 | Microsoft.DocumentDB/locations/deleteVirtualNetworkOrSubnets/operationResults/read | 讀取 deleteVirtualNetworkOrSubnets 非同步作業的狀態 |
> |  | **位置/動作狀態** |  |
> | 動作 | 微軟.文件資料庫/位置/操作狀態/讀取 | 讀取非同步作業的狀態 |
> |  | **動作結果** |  |
> | 動作 | Microsoft.DocumentDB/operationResults/read | 讀取非同步作業的狀態 |
> |  | **操作** |  |
> | 動作 | Microsoft.DocumentDB/operations/read | 讀取可供 Microsoft DocumentDB 使用的作業  |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

Azure 服務:[應用服務](../app-service/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.DomainRegistration/generateSsoRequest/Action | 產生要求以便登入網域控制中心。 |
> | 動作 | Microsoft.DomainRegistration/validateDomainRegistrationInformation/Action | 驗證網域購買物件，但不將其提交出去 |
> | 動作 | Microsoft.DomainRegistration/checkDomainAvailability/Action | 檢查網域是否可供購買 |
> | 動作 | Microsoft.DomainRegistration/listDomainRecommendations/Action | 根據關鍵字來擷取清單網域建議 |
> | 動作 | Microsoft.DomainRegistration/register/action | 針對訂用帳戶註冊 Microsoft 網域資源提供者 |
> |  | **網域** |  |
> | 動作 | Microsoft.DomainRegistration/domains/Read | 取得網域清單 |
> | 動作 | Microsoft.DomainRegistration/domains/Read | 取得網域 |
> | 動作 | Microsoft.DomainRegistration/domains/Write | 新增網域或更新現有網域 |
> | 動作 | Microsoft.DomainRegistration/domains/Delete | 刪除現有網域。 |
> | 動作 | Microsoft.DomainRegistration/domains/renew/Action | 更新現有網域。 |
> |  | **網域/網域擁有權識別碼** |  |
> | 動作 | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Read | 列出擁有權識別碼 |
> | 動作 | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Read | 取得擁有權識別碼 |
> | 動作 | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Write | 建立或更新識別碼 |
> | 動作 | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Delete | 刪除擁有權識別碼 |
> |  | **網域/動作結果** |  |
> | 動作 | Microsoft.DomainRegistration/domains/operationresults/Read | 取得網域作業 |
> |  | **操作** |  |
> | 動作 | Microsoft.DomainRegistration/operations/Read | 列出 App Service 網域註冊的所有作業 |
> |  | **topLevelDomains** |  |
> | 動作 | Microsoft.DomainRegistration/topLevelDomains/Read | 取得最上層網域 |
> | 動作 | Microsoft.DomainRegistration/topLevelDomains/Read | 取得最上層網域 |
> | 動作 | Microsoft.DomainRegistration/topLevelDomains/listAgreements/Action | 列出合約動作 |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

Azure 服務:[事件網格](../event-grid/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.EventGrid/register/action | 為 EventGrid 資源提供者註冊訂用帳戶。 |
> | 動作 | Microsoft.EventGrid/unregister/action | 為 EventGrid 資源提供者取消註冊訂用帳戶。 |
> |  | **網域** |  |
> | 動作 | Microsoft.EventGrid/domains/write | 建立或更新網域 |
> | 動作 | Microsoft.EventGrid/domains/read | 讀取網域 |
> | 動作 | Microsoft.EventGrid/domains/delete | 刪除網域 |
> | 動作 | Microsoft.EventGrid/domains/listKeys/action | 列出網域的金鑰 |
> | 動作 | Microsoft.EventGrid/domains/regenerateKey/action | 重新產生網域的金鑰 |
> |  | **網域/專用端接連線 Proxies** |  |
> | 動作 | 微軟.事件網格/域/私人端接連接Proxies/驗證/操作 | 驗證網域的專用端接連線 Proxie |
> | 動作 | 微軟.事件網格/域/私人端接連接Proxies/讀取 | 讀取網域的專用端接連線 Proxie |
> | 動作 | 微軟.事件網格/域/私人端接連接Proxies/寫入 | 為網域編寫專用的終結點連接 Proxie |
> | 動作 | 微軟.事件網格/域/私人端接連接Proxies/刪除 | 刪除網域的專用終結點連線 Proxie |
> |  | **網域/專用端點連接** |  |
> | 動作 | 微軟.事件網格/域/私有端點連接/讀取 | 讀取網域的專用終結點連線 |
> | 動作 | 微軟.事件網格/域/私有端點連接/寫入 | 為網域寫入專用的終結點 |
> | 動作 | 微軟.事件網格/域/私人端點連接/刪除 | 刪除網域的專用終結點連線 |
> |  | **網域/私有連結資源** |  |
> | 動作 | 微軟.事件網格/域/私有鏈接資源/讀取 | 取得或列出網域的私有連結資源 |
> |  | **網域/供應商/微軟.見解/日誌定義** |  |
> | 動作 | 微軟.eventGrid/域/提供者/微軟.見解/日誌定義/讀取 | 允許存取診斷紀錄 |
> |  | **網域/供應商/微軟.見解/指標定義** |  |
> | 動作 | Microsoft.EventGrid/domains/providers/Microsoft.Insights/metricDefinitions/read | 取得網域的可用計量 |
> |  | **domains/topics** |  |
> | 動作 | Microsoft.EventGrid/domains/topics/read | 讀取網域主題 |
> | 動作 | 微軟.事件網格/域/主題/寫入 | 建立或更新網域主題 |
> | 動作 | 微軟.事件網格/域/主題/刪除 | 刪除網域主題 |
> |  | **eventSubscriptions** |  |
> | 動作 | Microsoft.EventGrid/eventSubscriptions/write | 建立或更新事件訂閱 |
> | 動作 | Microsoft.EventGrid/eventSubscriptions/read | 閱讀事件訂閱 |
> | 動作 | Microsoft.EventGrid/eventSubscriptions/delete | 移除事件訂閱 |
> | 動作 | Microsoft.EventGrid/eventSubscriptions/getFullUrl/action | 取得事件訂閱的完整 URL |
> |  | **事件訂閱/供應商/微軟.見解/診斷設置** |  |
> | 動作 | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/read | 取得事件訂閱的診斷設定 |
> | 動作 | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/write | 建立或更新事件訂閱的診斷設定 |
> |  | **事件訂閱/提供者/微軟.見解/指標定義** |  |
> | 動作 | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/metricDefinitions/read | 取得 eventSubscriptions 的可用計量 |
> |  | **extensionTopics** |  |
> | 動作 | Microsoft.EventGrid/extensionTopics/read | 度取 extensionTopic。 |
> |  | **延伸主題/供應商/微軟.見解/診斷設置** |  |
> | 動作 | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/read | 取得主題的診斷設定 |
> | 動作 | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/write | 建立或更新主題的診斷設定 |
> |  | **延伸主題/供應商/微軟.見解/指標定義** |  |
> | 動作 | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/metricDefinitions/read | 取得主題的可用計量 |
> |  | **位置/事件訂閱** |  |
> | 動作 | Microsoft.EventGrid/locations/eventSubscriptions/read | 列出區域事件訂用帳戶 |
> |  | **位置/動作結果** |  |
> | 動作 | Microsoft.EventGrid/locations/operationResults/read | 讀取區域作業的結果 |
> |  | **位置/動作狀態** |  |
> | 動作 | Microsoft.EventGrid/locations/operationsStatus/read | 讀取區域作業的狀態 |
> |  | **位置/主題類型/事件訂閱** |  |
> | 動作 | Microsoft.EventGrid/locations/topictypes/eventSubscriptions/read | 依主題類型列出區域事件訂用帳戶 |
> |  | **動作結果** |  |
> | 動作 | Microsoft.EventGrid/operationResults/read | 讀取作業的結果 |
> |  | **操作** |  |
> | 動作 | Microsoft.EventGrid/operations/read | 列出 EventGrid 作業。 |
> |  | **操作狀態** |  |
> | 動作 | Microsoft.EventGrid/operationsStatus/read | 讀取作業的狀態 |
> |  | **合作夥伴命名空間** |  |
> | 動作 | 微軟.事件網格/合作夥伴命名空間/寫入 | 建立或更新合作夥伴命名空間 |
> | 動作 | 微軟.事件網格/合作夥伴命名空間/讀取 | 讀取合作夥伴命名空間 |
> | 動作 | 微軟.事件網格/合作夥伴命名空間/刪除 | 移除合作夥伴命名空間 |
> | 動作 | 微軟.事件網格/合作夥伴命名空間/清單鍵/操作 | 列出合作夥伴命名空間的鍵 |
> | 動作 | 微軟.事件網格/合作夥伴命名空間/重新生成鍵/操作 | 重新產生合作夥伴命名空間的鍵 |
> |  | **合作夥伴命名空間/事件通道** |  |
> | 動作 | 微軟.事件網格/合作夥伴命名空間/事件通道/讀取 | 閱讀事件頻道 |
> | 動作 | 微軟.事件網格/合作夥伴命名空間/事件通道/寫入 | 建立或更新事件頻道 |
> | 動作 | 微軟.事件網格/合作夥伴命名空間/事件通道/刪除 | 移除事件頻道 |
> |  | **合作夥伴註冊** |  |
> | 動作 | 微軟.事件網格/合作夥伴註冊/寫入 | 建立或更新合作夥伴註冊 |
> | 動作 | 微軟.事件網格/合作夥伴註冊/閱讀 | 閱讀合作夥伴註冊 |
> | 動作 | 微軟.事件網格/合作夥伴註冊/刪除 | 移除合作夥伴註冊 |
> |  | **合作夥伴主題** |  |
> | 動作 | 微軟.事件網格/合作夥伴主題/閱讀 | 閱讀合作夥伴主題 |
> | 動作 | 微軟.事件網格/合作夥伴主題/寫入 | 建立或更新合作夥伴主題 |
> | 動作 | 微軟.事件網格/合作夥伴主題/刪除 | 移除合作夥伴主題 |
> | 動作 | 微軟.事件網格/合作夥伴主題/啟動/操作 | 啟用合作夥伴主題 |
> | 動作 | 微軟.事件網格/合作夥伴主題/停用/操作 | 停用合作夥伴主題 |
> |  | **Sku** |  |
> | 動作 | 微軟.事件網格/sku/讀取 | 讀取事件網格資源的可用 SKU 定義 |
> |  | **系統主題** |  |
> | 動作 | 微軟.事件網格/系統主題/閱讀 | 閱讀系統主題 |
> | 動作 | 微軟.事件網格/系統主題/寫入 | 建立或更新系統主題 |
> | 動作 | 微軟.事件網格/系統主題/刪除 | 移除系統主題 |
> |  | **系統主題/供應商/微軟.見解/診斷設置** |  |
> | 動作 | 微軟.事件網格/系統主題/供應商/微軟.見解/診斷設置/讀取 | 取得系統主題的診斷設定 |
> | 動作 | 微軟.事件網格/系統主題/供應商/微軟.見解/診斷設置/寫入 | 建立或更新系統主題的診斷設定 |
> |  | **系統主題/供應商/微軟.見解/日誌定義** |  |
> | 動作 | 微軟.事件網格/系統主題/供應商/微軟.見解/日誌定義/讀取 | 允許存取診斷紀錄 |
> |  | **系統主題/供應商/微軟.見解/指標定義** |  |
> | 動作 | 微軟.事件網格/系統主題/供應商/微軟.見解/指標定義/讀取 | 取得系統主題的可用指標 |
> |  | **主題** |  |
> | 動作 | Microsoft.EventGrid/topics/write | 建立或更新主題 |
> | 動作 | Microsoft.EventGrid/topics/read | 讀取主題 |
> | 動作 | Microsoft.EventGrid/topics/delete | 刪除主題 |
> | 動作 | Microsoft.EventGrid/topics/listKeys/action | 列出主題的金鑰 |
> | 動作 | Microsoft.EventGrid/topics/regenerateKey/action | 重新產生主題的金鑰 |
> |  | **主題/私有端點連接Proxies** |  |
> | 動作 | 微軟.事件網格/主題/私人端接連接Proxies/驗證/操作 | 驗證主題的專用端接連線 Proxie |
> | 動作 | 微軟.事件網格/主題/私人端接連接Proxies/閱讀 | 閱讀私有端點連線 Proxie 主題 |
> | 動作 | 微軟.事件網格/主題/私人端接連接Proxies/寫 | 為主題編寫私有終結點連接 Proxie |
> | 動作 | 微軟.事件網格/主題/私人端接連接Proxies/刪除 | 移除主題的專用終結點連線 Proxie |
> |  | **主題/專用端點連接** |  |
> | 動作 | 微軟.事件網格/主題/私人端點連接/讀取 | 閱讀主題的專用終結點連線 |
> | 動作 | 微軟.事件網格/主題/私有端點連接/寫入 | 為主題編寫專用終結點連接 |
> | 動作 | 微軟.事件網格/主題/私人端點連接/刪除 | 移除主題的專用終結點連線 |
> |  | **主題/私有連結資源** |  |
> | 動作 | 微軟.事件網格/主題/私人連結資源/閱讀 | 閱讀私有連結資源主題 |
> |  | **主題/供應商/微軟.見解/診斷設置** |  |
> | 動作 | Microsoft.EventGrid/topics/providers/Microsoft.Insights/diagnosticSettings/read | 取得主題的診斷設定 |
> | 動作 | Microsoft.EventGrid/topics/providers/Microsoft.Insights/diagnosticSettings/write | 建立或更新主題的診斷設定 |
> |  | **主題/供應商/微軟.見解/日誌定義** |  |
> | 動作 | 微軟.事件網格/主題/供應商/微軟.見解/日誌定義/閱讀 | 允許存取診斷紀錄 |
> |  | **主題/供應商/微軟.見解/指標定義** |  |
> | 動作 | Microsoft.EventGrid/topics/providers/Microsoft.Insights/metricDefinitions/read | 取得主題的可用計量 |
> |  | **主題類型** |  |
> | 動作 | Microsoft.EventGrid/topictypes/read | 讀取 topictype |
> |  | **主題類型/事件訂閱** |  |
> | 動作 | Microsoft.EventGrid/topictypes/eventSubscriptions/read | 依主題類型列出全域事件訂用帳戶 |
> |  | **主題類型/事件型態** |  |
> | 動作 | Microsoft.EventGrid/topictypes/eventtypes/read | 讀取 topictype 支援的 eventtypes |

## <a name="microsofteventhub"></a>Microsoft.EventHub

Azure 服務:[事件中心](../event-hubs/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.EventHub/checkNamespaceAvailability/action | 檢查給定訂用帳戶下的命名空間是否可用。 此 API 即將淘汰。請改用 CheckNameAvailabiltiy。 |
> | 動作 | Microsoft.EventHub/checkNameAvailability/action | 檢查給定訂用帳戶下的命名空間是否可用。 |
> | 動作 | Microsoft.EventHub/register/action | 針對 EventHub 資源提供者註冊訂用帳戶，並讓您能夠建立 EventHub 資源 |
> | 動作 | Microsoft.EventHub/unregister/action | 註冊 EventHub 資源提供者 |
> |  | **可用叢集區域** |  |
> | 動作 | 微軟.事件中心/可用群集區域/讀取 | 讀取操作以按 Azure 區域列出可用的預配置群集。 |
> |  | **集群** |  |
> | 動作 | Microsoft.EventHub/clusters/read | 取得叢集資源描述 |
> | 動作 | Microsoft.EventHub/clusters/write | 創建或修改現有群集資源。 |
> | 動作 | 微軟.事件中心/群集/刪除 | 刪除現有的群集資源。 |
> |  | **叢集/命名空間** |  |
> | 動作 | 微軟.事件中心/集群/命名空間/讀取 | 列出群集中命名空間的 Azure 資源管理器標識。 |
> |  | **叢集/操作結果** |  |
> | 動作 | 微軟.事件中心/集群/操作結果/讀取 | 獲取非同步群集操作的狀態。 |
> |  | **叢集/供應商/微軟.見解/指標定義** |  |
> | 動作 | Microsoft.EventHub/clusters/providers/Microsoft.Insights/metricDefinitions/read | 取得叢集計量資源描述的清單 |
> |  | **位置** |  |
> | 動作 | Microsoft.EventHub/locations/deleteVirtualNetworkOrSubnets/action | 針對指定的 VNet，刪除 EventHub 資源提供者中的 VNet 規則 |
> |  | **namespaces** |  |
> | 動作 | Microsoft.EventHub/namespaces/write | 建立命名空間資源，並更新其屬性。 命名空間的標記和容量是可以更新的屬性。 |
> | 動作 | Microsoft.EventHub/namespaces/read | 取得命名空間資源描述的清單 |
> | 動作 | Microsoft.EventHub/namespaces/Delete | 刪除命名空間資源 |
> | 動作 | Microsoft.EventHub/namespaces/authorizationRules/action | 更新命名空間授權規則。 此 API 即將淘汰。 請改用 PUT 呼叫來更新命名空間授權規則。 API 版本 2017-04-01 不支援此作業。 |
> | 動作 | Microsoft.EventHub/namespaces/removeAcsNamepsace/action | 移除 ACS 命名空間 |
> |  | **命名空間/授權規則** |  |
> | 動作 | Microsoft.EventHub/namespaces/authorizationRules/read | 取得命名空間授權規則描述的清單。 |
> | 動作 | Microsoft.EventHub/namespaces/authorizationRules/write | 建立命名空間層級的授權規則，並更新其屬性。 您可以更新授權規則的存取權限、主要金鑰和次要金鑰。 |
> | 動作 | Microsoft.EventHub/namespaces/authorizationRules/delete | 刪除命名空間授權規則。 您無法刪除預設的命名空間授權規則。  |
> | 動作 | Microsoft.EventHub/namespaces/authorizationRules/listkeys/action | 取得命名空間的連接字串 |
> | 動作 | Microsoft.EventHub/namespaces/authorizationRules/regenerateKeys/action | 對資源重新產生主要或次要金鑰 |
> |  | **命名空間/災難修復設定** |  |
> | 動作 | Microsoft.EventHub/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | 檢查指定訂用帳戶下命名空間別名的可用性。 |
> | 動作 | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/write | 建立或更新與命名空間相關聯的災害復原設定。 |
> | 動作 | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/read | 取得與命名空間相關聯的災害復原設定。 |
> | 動作 | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/delete | 刪除與命名空間相關聯的災害復原設定。 此作業只能透過主要命名空間叫用。 |
> | 動作 | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/breakPairing/action | 停用災害復原，並停止將變更從主要命名空間複寫至次要命名空間。 |
> | 動作 | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/failover/action | 叫用異地災害復原容錯移轉，並將命名空間別名重新設定為指向次要命名空間。 |
> |  | **命名空間/災難修復設定/授權規則** |  |
> | 動作 | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/read | 取得災害復原主要命名空間的授權規則 |
> | 動作 | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | 取得災害復原主要命名空間的授權規則金鑰 |
> |  | **namespaces/eventhubs** |  |
> | 動作 | Microsoft.EventHub/namespaces/eventhubs/write | 建立或更新 EventHub 屬性。 |
> | 動作 | Microsoft.EventHub/namespaces/eventhubs/read | 取得 EventHub 資源描述的清單 |
> | 動作 | Microsoft.EventHub/namespaces/eventhubs/Delete | 用來刪除 EventHub 資源的作業 |
> | 動作 | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/action | 更新 EventHub 的作業。 API 版本 2017-04-01 不支援此作業。 授權規則。 請使用 PUT 呼叫來更新授權規則。 |
> |  | **namespaces/eventhubs/authorizationRules** |  |
> | 動作 | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/read |  取得 EventHub 授權規則的清單 |
> | 動作 | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/write | 建立 EventHub 授權規則，並更新其屬性。 您可以更新授權規則存取權限。 |
> | 動作 | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/delete | 用來刪除 EventHub 授權規則的作業 |
> | 動作 | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/listkeys/action | 取得 EventHub 的連接字串 |
> | 動作 | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/regenerateKeys/action | 對資源重新產生主要或次要金鑰 |
> |  | **命名空間/事件中心/消費組** |  |
> | 動作 | Microsoft.EventHub/namespaces/eventHubs/consumergroups/write | 建立或更新 ConsumerGroup 屬性。 |
> | 動作 | Microsoft.EventHub/namespaces/eventHubs/consumergroups/read | 取得 ConsumerGroup 資源描述的清單 |
> | 動作 | Microsoft.EventHub/namespaces/eventHubs/consumergroups/Delete | 用來刪除 ConsumerGroup 資源的作業 |
> |  | **命名空間/ipFilter規則** |  |
> | 動作 | Microsoft.EventHub/namespaces/ipFilterRules/read | 取得 IP 篩選資源 |
> | 動作 | Microsoft.EventHub/namespaces/ipFilterRules/write | 建立 IP 篩選資源 |
> | 動作 | Microsoft.EventHub/namespaces/ipFilterRules/delete | 刪除 IP 篩選資源 |
> |  | **命名空間/訊息計劃** |  |
> | 動作 | Microsoft.EventHub/namespaces/messagingPlan/read | 取得命名空間的傳訊方案。<br>此 API 即將淘汰。<br>透過 MessagingPlan 資源公開的屬性，已移至新版 API 的 (父系) 命名空間資源。<br>API 版本 2017-04-01 不支援此作業。 |
> | 動作 | Microsoft.EventHub/namespaces/messagingPlan/write | 更新命名空間的傳訊方案。<br>此 API 即將淘汰。<br>透過 MessagingPlan 資源公開的屬性，已移至新版 API 的 (父系) 命名空間資源。<br>API 版本 2017-04-01 不支援此作業。 |
> |  | **命名空間/網路規則集** |  |
> | 動作 | 微軟.EventHub/命名空間/網路規則集/讀取 | 取得網路規則集資源 |
> | 動作 | 微軟.事件Hub/命名空間/網路規則集/寫入 | 建立 VNET 規則資源 |
> | 動作 | 微軟.EventHub/命名空間/網路規則集/刪除 | 刪除 VNET 規則資源 |
> |  | **命名空間/網路規則集** |  |
> | 動作 | 微軟.EventHub/命名空間/網路規則集/讀取 | 取得網路規則集資源 |
> | 動作 | 微軟.事件Hub/命名空間/網路規則集/寫入 | 建立 VNET 規則資源 |
> | 動作 | 微軟.EventHub/命名空間/網路規則集/刪除 | 刪除 VNET 規則資源 |
> |  | **命名空間/動作結果** |  |
> | 動作 | Microsoft.EventHub/namespaces/operationresults/read | 取得命名空間作業的狀態 |
> |  | **命名空間/專用端接Proxies** |  |
> | 動作 | 微軟.事件Hub/命名空間/私人端接連接Proxies/驗證/操作 | 驗證專用終結點連線代理 |
> | 動作 | 微軟.事件Hub/命名空間/私人端接連接Proxies/讀取 | 取得專用終結點連線代理 |
> | 動作 | 微軟.事件Hub/命名空間/私人端接連接Proxies/寫入 | 建立專用終結點連線代理 |
> | 動作 | 微軟.事件Hub/命名空間/私人端接連接Proxies/刪除 | 移除專用終結點連線代理 |
> |  | **命名空間/供應商/微軟.見解/診斷設置** |  |
> | 動作 | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/diagnosticSettings/read | 取得命名空間診斷設定資源描述的清單 |
> | 動作 | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/diagnosticSettings/write | 取得命名空間診斷設定資源描述的清單 |
> |  | **命名空間/提供者/微軟.見解/日誌定義** |  |
> | 動作 | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/logDefinitions/read | 取得命名空間記錄資源描述的清單 |
> |  | **命名空間/提供者/微軟.見解/指標定義** |  |
> | 動作 | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/metricDefinitions/read | 取得命名空間計量資源描述的清單 |
> |  | **命名空間/虛擬網路規則** |  |
> | 動作 | Microsoft.EventHub/namespaces/virtualNetworkRules/read | 取得 VNET 規則資源 |
> | 動作 | Microsoft.EventHub/namespaces/virtualNetworkRules/write | 建立 VNET 規則資源 |
> | 動作 | Microsoft.EventHub/namespaces/virtualNetworkRules/delete | 刪除 VNET 規則資源 |
> |  | **操作** |  |
> | 動作 | Microsoft.EventHub/operations/read | 取得作業 |
> |  | **Sku** |  |
> | 動作 | Microsoft.EventHub/sku/read | 取得 SKU 資源描述的清單 |
> |  | **sKU/地區** |  |
> | 動作 | Microsoft.EventHub/sku/regions/read | 取得 SkuRegions 資源描述的清單 |
> |  | **命名空間/訊息** |  |
> | DataAction | 微軟.EventHub/命名空間/消息/發送/操作 | 傳送訊息 |
> | DataAction | 微軟.EventHub/命名空間/消息/接收/操作 | 接收訊息 |

## <a name="microsoftfeatures"></a>Microsoft.Features

Azure 服務[:Azure 資源管理員](../azure-resource-manager/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.Features/register/action | 註冊訂用帳戶的功能。 |
> |  | **特徵** |  |
> | 動作 | Microsoft.Features/features/read | 取得訂用帳戶的功能。 |
> |  | **操作** |  |
> | 動作 | Microsoft.Features/operations/read | 取得作業的清單。 |
> |  | **提供程式/功能** |  |
> | 動作 | Microsoft.Features/providers/features/read | 取得給定資源提供者中某個訂用帳戶的功能。 |
> | 動作 | Microsoft.Features/providers/features/register/action | 註冊給定資源提供者中某個訂用帳戶的功能。 |
> | 動作 | Microsoft.Features/providers/features/unregister/action | 在指定的資源提供者內取消註冊訂用帳戶的功能。 |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

Azure 服務[:Azure 策略](../governance/policy/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | 微軟.來賓配置/註冊/操作 | 註冊 Microsoft.Guest 配置資源提供程式的訂閱。 |
> |  | **guestConfigurationAssignments** |  |
> | 動作 | Microsoft.GuestConfiguration/guestConfigurationAssignments/write | 取得新的來賓組態指派。 |
> | 動作 | Microsoft.GuestConfiguration/guestConfigurationAssignments/read | 取得來賓組態指派。 |
> | 動作 | 微軟.來賓配置/來賓配置分配/刪除 | 刪除來賓配置分配。 |
> |  | **來賓配置配置/報告** |  |
> | 動作 | Microsoft.GuestConfiguration/guestConfigurationAssignments/reports/read | 取得來賓組態指派報告。 |
> |  | **操作** |  |
> | 動作 | 微軟.來賓配置/操作/讀取 | 取得 Microsoft.Guest 設定資源提供者的作業 |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

Azure 服務[:HDInsight](../hdinsight/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | 微軟.HDInsight/註冊/操作 | 註冊訂閱的 HDInsight 資源提供者 |
> | 動作 | 微軟.HDInsight/取消註冊/操作 | 取消註冊訂閱的 HDInsight 資源提供者 |
> |  | **集群** |  |
> | 動作 | Microsoft.HDInsight/clusters/write | 建立或更新 HDInsight 叢集 |
> | 動作 | Microsoft.HDInsight/clusters/read | 取得 HDInsight 叢集的相關詳細資料 |
> | 動作 | Microsoft.HDInsight/clusters/delete | 刪除 HDInsight 叢集 |
> | 動作 | Microsoft.HDInsight/clusters/changerdpsetting/action | 變更 HDInsight 叢集的 RDP 設定 |
> | 動作 | 微軟.HDInsight/集群/獲取閘道設定/操作 | 取得 HDInsight 叢集的閘道設定 |
> | 動作 | 微軟.HDInsight/集群/更新閘道設定/操作 | 更新 HDInsight 叢集的閘道設定 |
> | 動作 | Microsoft.HDInsight/clusters/configurations/action | 取得 HDInsight 叢集組態 |
> |  | **clusters/applications** |  |
> | 動作 | Microsoft.HDInsight/clusters/applications/read | 取得 HDInsight 叢集的應用程式 |
> | 動作 | Microsoft.HDInsight/clusters/applications/write | 建立或更新 HDInsight 叢集的應用程式 |
> | 動作 | Microsoft.HDInsight/clusters/applications/delete | 刪除 HDInsight 叢集的應用程式 |
> |  | **叢集/組態** |  |
> | 動作 | Microsoft.HDInsight/clusters/configurations/read | 取得 HDInsight 叢集組態 |
> |  | **叢集/擴展** |  |
> | 動作 | 微軟.HDInsight/集群/擴展/寫入 | 為 HDInsight 叢集建立叢集延伸 |
> | 動作 | 微軟.HDInsight/集群/擴展/讀取 | 取得 HDInsight 叢集的叢集延伸 |
> | 動作 | 微軟.HDInsight/集群/擴展/刪除 | 刪除 HDInsight 叢集的叢集延伸 |
> |  | **叢集/供應商/微軟.見解/診斷設置** |  |
> | 動作 | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/diagnosticSettings/read | 取得資源 HDInsight 叢集的診斷設定 |
> | 動作 | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/diagnosticSettings/write | 建立或更新資源 HDInsight 叢集的診斷設定 |
> |  | **叢集/供應商/微軟.見解/指標定義** |  |
> | 動作 | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/metricDefinitions/read | 取得 HDInsight 叢集的可用計量 |
> |  | **叢集/角色** |  |
> | 動作 | Microsoft.HDInsight/clusters/roles/resize/action | 調整 HDInsight 叢集的大小 |
> |  | **位置/功能** |  |
> | 動作 | Microsoft.HDInsight/locations/capabilities/read | 取得訂用帳戶功能 |
> |  | **位置/檢查名稱可用性** |  |
> | 動作 | Microsoft.HDInsight/locations/checkNameAvailability/read | 檢查名稱可用性 |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

Azure 服務[:Azure 弧形](../azure-arc/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | 微軟.混合計算/註冊/操作 | 註冊 Microsoft.混合計算資源提供程式的訂閱 |
> | 動作 | 微軟.混合計算/取消註冊/操作 | 取消註冊 Microsoft 的訂閱。 |
> |  | **位置/動作結果** |  |
> | 動作 | 微軟.混合計算/位置/操作結果/讀取 | 讀取 Microsoft 上操作的狀態。 |
> |  | **機器** |  |
> | 動作 | 微軟.混合計算/機器/讀取 | 讀取任何 Azure 弧形電腦 |
> | 動作 | 微軟.混合計算/機器/寫入 | 寫入 Azure 弧形電腦 |
> | 動作 | 微軟.混合計算/機器/刪除 | 移除 Azure 弧形電腦 |
> | 動作 | 微軟.混合計算/機器/重新連接/操作 | 重新連接 Azure 弧形電腦 |
> |  | **機器/擴展** |  |
> | 動作 | 微軟.混合計算/機器/擴展/讀取 | 讀取任何 Azure 電弧延伸 |
> | 動作 | 微軟.混合計算/機器/擴展/寫入 | 安裝或更新 Azure 電弧延伸 |
> | 動作 | 微軟.混合計算/機器/擴展/刪除 | 移除 Azure 電弧延伸 |
> |  | **操作** |  |
> | 動作 | 微軟.混合計算/操作/讀取 | 讀取伺服器 Azure 弧的所有操作 |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

Azure 服務[:Azure 匯入/匯出](../storage/common/storage-import-export-service.md)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.ImportExport/register/action | 針對匯入/匯出資源提供者註冊訂用帳戶，並讓您能夠建立匯入/匯出作業。 |
> |  | **工作** |  |
> | 動作 | Microsoft.ImportExport/jobs/write | 使用指定參數建立作業，或更新指定作業的屬性或標記。 |
> | 動作 | Microsoft.ImportExport/jobs/read | 取得指定作業的屬性，或傳回作業清單。 |
> | 動作 | Microsoft.ImportExport/jobs/listBitLockerKeys/action | 取得指定作業的 BitLocker 金鑰。 |
> | 動作 | Microsoft.ImportExport/jobs/delete | 刪除現有作業。 |
> |  | **位置** |  |
> | 動作 | Microsoft.ImportExport/locations/read | 取得指定位置的屬性，或傳回位置清單。 |
> |  | **操作** |  |
> | 動作 | 微軟.進口出口/操作/讀取 | 獲取資源提供程式支援的操作。 |

## <a name="microsoftinsights"></a>Microsoft.Insights

Azure 服務[:Azure 監視器](../azure-monitor/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.Insights/Metrics/Action | 計量動作 |
> | 動作 | Microsoft.Insights/Register/Action | 註冊 Microsoft Insights 提供者 |
> | 動作 | Microsoft.Insights/Unregister/Action | 註冊 Microsoft Insights 提供者 |
> | 動作 | Microsoft.Insights/ListMigrationDate/Action | 取回訂用帳戶移轉日期 |
> | 動作 | Microsoft.Insights/MigrateToNewpricingModel/Action | 將訂用帳戶移轉至新的計價模式 |
> | 動作 | Microsoft.Insights/RollbackToLegacyPricingModel/Action | 將訂用帳戶復原至舊版計價模式 |
> |  | **行動組** |  |
> | 動作 | Microsoft.Insights/ActionGroups/Write | 建立或更新動作群組 |
> | 動作 | Microsoft.Insights/ActionGroups/Delete | 刪除動作群組 |
> | 動作 | Microsoft.Insights/ActionGroups/Read | 讀取動作群組 |
> |  | **活動記錄警報** |  |
> | 動作 | Microsoft.Insights/ActivityLogAlerts/Write | 建立或更新活動記錄警示 |
> | 動作 | Microsoft.Insights/ActivityLogAlerts/Delete | 刪除活動記錄警示 |
> | 動作 | Microsoft.Insights/ActivityLogAlerts/Read | 讀取活動記錄警示 |
> | 動作 | Microsoft.Insights/ActivityLogAlerts/Activated/Action | 活動記錄警示已啟用 |
> |  | **警示規則** |  |
> | 動作 | Microsoft.Insights/AlertRules/Write | 建立或更新傳統計量警示 |
> | 動作 | Microsoft.Insights/AlertRules/Delete | 刪除傳統計量警示 |
> | 動作 | Microsoft.Insights/AlertRules/Read | 讀取傳統計量警示 |
> | 動作 | Microsoft.Insights/AlertRules/Activated/Action | 傳統計量警示已啟用 |
> | 動作 | Microsoft.Insights/AlertRules/Resolved/Action | 傳統計量警示已解決 |
> | 動作 | Microsoft.Insights/AlertRules/Throttled/Action | 傳統計量警示規則已節流 |
> |  | **警示規則/事件** |  |
> | 動作 | Microsoft.Insights/AlertRules/Incidents/Read | 讀取傳統計量警示事件 |
> |  | **自動縮放設定** |  |
> | 動作 | Microsoft.Insights/AutoscaleSettings/Write | 建立或更新自動調整設定 |
> | 動作 | Microsoft.Insights/AutoscaleSettings/Delete | 刪除自動調整設定 |
> | 動作 | Microsoft.Insights/AutoscaleSettings/Read | 讀取自動調整設定 |
> | 動作 | Microsoft.Insights/AutoscaleSettings/Scaleup/Action | 自動調整相應增加已起始 |
> | 動作 | Microsoft.Insights/AutoscaleSettings/Scaledown/Action | 自動調整相應減少已起始 |
> | 動作 | Microsoft.Insights/AutoscaleSettings/ScaleupResult/Action | 自動調整相應增加已完成 |
> | 動作 | Microsoft.Insights/AutoscaleSettings/ScaledownResult/Action | 自動調整相應減少已完成 |
> |  | **自動縮放設定/供應商/微軟.見解/診斷設置** |  |
> | 動作 | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/diagnosticSettings/Read | 讀取資源診斷設定 |
> | 動作 | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/diagnosticSettings/Write | 建立或更新資源診斷設定 |
> |  | **自動縮放設定/供應商/微軟.見解/日誌定義** |  |
> | 動作 | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/logDefinitions/Read | 讀取記錄定義 |
> |  | **自動縮放設定/供應商/微軟.見解/指標定義** |  |
> | 動作 | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/MetricDefinitions/Read | 讀取計量定義 |
> |  | **基線** |  |
> | 動作 | 微軟.見解/基線/閱讀 | 讀取指標基線(預覽) |
> |  | **計算基線** |  |
> | 動作 | 微軟.見解/計算基線/閱讀 | 計算指標值的基線(預覽) |
> |  | **元件** |  |
> | 動作 | Microsoft.Insights/Components/AnalyticsTables/Action | Application Insights 分析資料表動作 |
> | 動作 | Microsoft.Insights/Components/ApiKeys/Action | 產生 Application Insights API 金鑰 |
> | 動作 | Microsoft.Insights/Components/Purge/Action | 從 Application Insights 清除資料 |
> | 動作 | 微軟.見解/元件/每日Cap達到/操作 | 達到應用見解元件的每日上限 |
> | 動作 | 微軟.見解/元件/每日Cap警告閾值達到/操作 | 達到應用程式見解元件的每日上限警告閾值 |
> | 動作 | Microsoft.Insights/Components/Write | 寫入 Application Insights 元件設定 |
> | 動作 | Microsoft.Insights/Components/Delete | 刪除 Application Insights 元件設定 |
> | 動作 | Microsoft.Insights/Components/Read | 讀取 Application Insights 元件設定 |
> | 動作 | Microsoft.Insights/Components/ExportConfiguration/Action | Application Insights 匯出設定動作 |
> | 動作 | Microsoft.Insights/Components/Move/Action | 將 Application Insights 元件移到另一個資源群組或訂用帳戶 |
> |  | **元件/分析專案** |  |
> | 動作 | Microsoft.Insights/Components/AnalyticsItems/Delete | 刪除 Application Insights 分析項目 |
> | 動作 | Microsoft.Insights/Components/AnalyticsItems/Read | 讀取 Application Insights 分析項目 |
> | 動作 | Microsoft.Insights/Components/AnalyticsItems/Write | 寫入 Application Insights 分析項目 |
> |  | **元件/分析表** |  |
> | 動作 | Microsoft.Insights/Components/AnalyticsTables/Delete | 刪除 Application Insights 分析資料表結構描述 |
> | 動作 | Microsoft.Insights/Components/AnalyticsTables/Read | 讀取 Application Insights 分析資料表結構描述 |
> | 動作 | Microsoft.Insights/Components/AnalyticsTables/Write | 寫入 Application Insights 分析資料表結構描述 |
> |  | **元件/註解** |  |
> | 動作 | Microsoft.Insights/Components/Annotations/Delete | 刪除 Application Insights 註釋 |
> | 動作 | Microsoft.Insights/Components/Annotations/Read | 讀取 Application Insights 註釋 |
> | 動作 | Microsoft.Insights/Components/Annotations/Write | 寫入 Application Insights 註釋 |
> |  | **元件/Api** |  |
> | 動作 | Microsoft.Insights/Components/Api/Read | 讀取 Application Insights 元件資料 API |
> |  | **元件/ApiKeys** |  |
> | 動作 | Microsoft.Insights/Components/ApiKeys/Delete | 刪除 Application Insights API 金鑰 |
> | 動作 | Microsoft.Insights/Components/ApiKeys/Read | 讀取 Application Insights API 金鑰 |
> |  | **元件/計費計劃元件** |  |
> | 動作 | Microsoft.Insights/Components/BillingPlanForComponent/Read | 讀取 Application Insights 元件的計費方案 |
> |  | **元件/目前計費功能** |  |
> | 動作 | Microsoft.Insights/Components/CurrentBillingFeatures/Read | 在讀取 Application Insights 元件目前的計費功能 |
> | 動作 | Microsoft.Insights/Components/CurrentBillingFeatures/Write | 寫入 Application Insights 元件目前的計費功能 |
> |  | **元件/預設工作項目設定** |  |
> | 動作 | Microsoft.Insights/Components/DefaultWorkItemConfig/Read | 讀取 Application Insights 預設的 ALM 整合設定 |
> |  | **元件/事件** |  |
> | 動作 | Microsoft.Insights/Components/Events/Read | 使用 OData 查詢格式從 Application Insights 取得記錄 |
> |  | **元件/匯出設定** |  |
> | 動作 | Microsoft.Insights/Components/ExportConfiguration/Delete | 刪除 Application Insights 匯出設定 |
> | 動作 | Microsoft.Insights/Components/ExportConfiguration/Read | 讀取 Application Insights 匯出設定 |
> | 動作 | Microsoft.Insights/Components/ExportConfiguration/Write | 寫入 Application Insights 匯出設定 |
> |  | **元件/延伸查詢** |  |
> | 動作 | Microsoft.Insights/Components/ExtendQueries/Read | 讀取 Application Insights 元件擴充的查詢結果 |
> |  | **元件/我的最愛** |  |
> | 動作 | Microsoft.Insights/Components/Favorites/Delete | 刪除 Application Insights 我的最愛 |
> | 動作 | Microsoft.Insights/Components/Favorites/Read | 讀取 Application Insights 我的最愛 |
> | 動作 | Microsoft.Insights/Components/Favorites/Write | 寫入 Application Insights 我的最愛 |
> |  | **元件/功能功能** |  |
> | 動作 | Microsoft.Insights/Components/FeatureCapabilities/Read | 讀取 Application Insights 元件功能的能力 |
> |  | **元件/取得可用的計費功能** |  |
> | 動作 | Microsoft.Insights/Components/GetAvailableBillingFeatures/Read | 讀取 Application Insights 元件可用的計費功能 |
> |  | **元件/取得權杖** |  |
> | 動作 | Microsoft.Insights/Components/GetToken/Read | 讀取 Application Insights 元件權杖 |
> |  | **元件/指標定義** |  |
> | 動作 | Microsoft.Insights/Components/MetricDefinitions/Read | 讀取 Application Insights 元件計量定義 |
> |  | **元件/指標** |  |
> | 動作 | Microsoft.Insights/Components/Metrics/Read | 讀取 Application Insights 元件計量 |
> |  | **元件/我的分析專案** |  |
> | 動作 | Microsoft.Insights/Components/MyAnalyticsItems/Delete | 刪除 Application Insights 個人分析項目 |
> | 動作 | Microsoft.Insights/Components/MyAnalyticsItems/Write | 寫入 Application Insights 個人分析項目 |
> | 動作 | Microsoft.Insights/Components/MyAnalyticsItems/Read | 讀取 Application Insights 個人分析項目 |
> |  | **元件/我的最愛** |  |
> | 動作 | Microsoft.Insights/Components/MyFavorites/Read | 讀取 Application Insights 個人我的最愛 |
> |  | **元件/操作** |  |
> | 動作 | Microsoft.Insights/Components/Operations/Read | 取得 Application Insights 中長時間執行之作業的狀態 |
> |  | **元件/定價計劃** |  |
> | 動作 | Microsoft.Insights/Components/PricingPlans/Read | 讀取 Application Insights 元件定價方案 |
> | 動作 | Microsoft.Insights/Components/PricingPlans/Write | 寫入 Application Insights 元件定價方案 |
> |  | **元件/主動偵測設定** |  |
> | 動作 | Microsoft.Insights/Components/ProactiveDetectionConfigs/Read | 讀取 Application Insights 主動式偵測設定 |
> | 動作 | Microsoft.Insights/Components/ProactiveDetectionConfigs/Write | 寫入 Application Insights 主動式偵測設定 |
> |  | **元件/提供者/微軟.見解/診斷設置** |  |
> | 動作 | 微軟.見解/元件/供應商/微軟.見解/診斷設置/閱讀 | 讀取資源診斷設定 |
> | 動作 | 微軟.見解/元件/供應商/微軟.見解/診斷設置/寫入 | 建立或更新資源診斷設定 |
> |  | **元件/提供者/微軟.見解/日誌定義** |  |
> | 動作 | 微軟.見解/元件/供應商/微軟.見解/日誌定義/閱讀 | 讀取記錄定義 |
> |  | **元件/提供者/微軟.見解/指標定義** |  |
> | 動作 | Microsoft.Insights/Components/providers/Microsoft.Insights/MetricDefinitions/Read | 讀取計量定義 |
> |  | **元件/查詢** |  |
> | 動作 | Microsoft.Insights/Components/Query/Read | 針對 Application Insights 記錄執行查詢 |
> |  | **元件/配額狀態** |  |
> | 動作 | Microsoft.Insights/Components/QuotaStatus/Read | 讀取 Application Insights 元件配額狀態 |
> |  | **元件/合成監視器位置** |  |
> | 動作 | Microsoft.Insights/Components/SyntheticMonitorLocations/Read | 讀取 Application Insights webtest 位置 |
> |  | **元件/網路測試** |  |
> | 動作 | Microsoft.Insights/Components/Webtests/Read | 讀取 Webtest 設定 |
> |  | **元件/工作項目設定** |  |
> | 動作 | Microsoft.Insights/Components/WorkItemConfigs/Delete | 刪除 Application Insights ALM 整合設定 |
> | 動作 | Microsoft.Insights/Components/WorkItemConfigs/Read | 讀取 Application Insights ALM 整合設定 |
> | 動作 | Microsoft.Insights/Components/WorkItemConfigs/Write | 寫入 Application Insights ALM 整合設定 |
> |  | **資料收集規則關聯** |  |
> | 動作 | 微軟.見解/資料收集規則關聯/閱讀 | 讀取資源與資料收集規則的關聯 |
> | 動作 | 微軟.見解/資料收集規則關聯/寫入 | 建立或更新資源與資料收集規則的關聯 |
> | 動作 | 微軟.見解/資料收集規則關聯/刪除 | 刪除資源與資料收集規則的關聯 |
> |  | **資料收集規則** |  |
> | 動作 | 微軟.見解/資料收集規則/閱讀 | 讀取資料收集規則 |
> | 動作 | 微軟.見解/資料收集規則/寫入 | 建立或更新資料收集規則 |
> | 動作 | 微軟.見解/資料收集規則/刪除 | 刪除資料收集規則 |
> |  | **診斷設定** |  |
> | 動作 | Microsoft.Insights/DiagnosticSettings/Write | 建立或更新資源診斷設定 |
> | 動作 | Microsoft.Insights/DiagnosticSettings/Delete | 刪除資源診斷設定 |
> | 動作 | Microsoft.Insights/DiagnosticSettings/Read | 讀取資源診斷設定 |
> |  | **活動類別** |  |
> | 動作 | Microsoft.Insights/EventCategories/Read | 讀取可用的活動記錄事件類別 |
> |  | **事件類型/摘要事件** |  |
> | 動作 | Microsoft.Insights/eventtypes/digestevents/Read | 讀取管理事件類型摘要 |
> |  | **事件類型/值** |  |
> | 動作 | Microsoft.Insights/eventtypes/values/Read | 讀取活動記錄事件 |
> |  | **延伸診斷設定** |  |
> | 動作 | Microsoft.Insights/ExtendedDiagnosticSettings/Write | 建立或更新網路流量記錄診斷設定 |
> | 動作 | Microsoft.Insights/ExtendedDiagnosticSettings/Delete | 刪除網路流量記錄診斷設定 |
> | 動作 | Microsoft.Insights/ExtendedDiagnosticSettings/Read | 讀取網路流量記錄診斷設定 |
> |  | **清單移轉日期** |  |
> | 動作 | Microsoft.Insights/ListMigrationDate/Read | 取回訂用帳戶移轉日期 |
> |  | **紀錄定義** |  |
> | 動作 | Microsoft.Insights/LogDefinitions/Read | 讀取記錄定義 |
> |  | **紀錄設定檔** |  |
> | 動作 | Microsoft.Insights/LogProfiles/Write | 建立或更新活動記錄的記錄設定檔 |
> | 動作 | Microsoft.Insights/LogProfiles/Delete | 刪除活動記錄的記錄設定檔 |
> | 動作 | Microsoft.Insights/LogProfiles/Read | 讀取活動記錄的記錄設定檔 |
> |  | **記錄** |  |
> | 動作 | Microsoft.Insights/Logs/Read | 從您的所有記錄讀取資料 |
> |  | **紀錄/AD評看建議** |  |
> | 動作 | Microsoft.Insights/Logs/ADAssessmentRecommendation/Read | 從 ADAssessmentRecommendation 資料表讀取資料 |
> |  | **紀錄/AD 複製結果** |  |
> | 動作 | Microsoft.Insights/Logs/ADReplicationResult/Read | 從 ADReplicationResult 資料表讀取資料 |
> |  | **紀錄/AD 安全評估建議** |  |
> | 動作 | Microsoft.Insights/Logs/ADSecurityAssessmentRecommendation/Read | 從 ADSecurityAssessmentRecommendation 資料表讀取資料 |
> |  | **記錄/警示** |  |
> | 動作 | Microsoft.Insights/Logs/Alert/Read | 從 Alert 資料表讀取資料 |
> |  | **記錄/警示記錄** |  |
> | 動作 | Microsoft.Insights/Logs/AlertHistory/Read | 從 AlertHistory 資料表讀取資料 |
> |  | **紀錄/應用程式見解** |  |
> | 動作 | Microsoft.Insights/Logs/ApplicationInsights/Read | 從 ApplicationInsights 資料表讀取資料 |
> |  | **紀錄/Azure 活動** |  |
> | 動作 | Microsoft.Insights/Logs/AzureActivity/Read | 從 AzureActivity 資料表讀取資料 |
> |  | **紀錄/Azure 指標** |  |
> | 動作 | Microsoft.Insights/Logs/AzureMetrics/Read | 從 AzureMetrics 資料表讀取資料 |
> |  | **日誌/結合埠** |  |
> | 動作 | Microsoft.Insights/Logs/BoundPort/Read | 從 BoundPort 資料表讀取資料 |
> |  | **紀錄/通用安全紀錄** |  |
> | 動作 | Microsoft.Insights/Logs/CommonSecurityLog/Read | 從 CommonSecurityLog 資料表讀取資料 |
> |  | **紀錄/電腦群組** |  |
> | 動作 | Microsoft.Insights/Logs/ComputerGroup/Read | 從 ComputerGroup 資料表讀取資料 |
> |  | **紀錄/設定變更** |  |
> | 動作 | Microsoft.Insights/Logs/ConfigurationChange/Read | 從 ConfigurationChange 資料表讀取資料 |
> |  | **紀錄/設定資料** |  |
> | 動作 | Microsoft.Insights/Logs/ConfigurationData/Read | 從 ConfigurationData 資料表讀取資料 |
> |  | **紀錄/容器映像清單** |  |
> | 動作 | Microsoft.Insights/Logs/ContainerImageInventory/Read | 從 ContainerImageInventory 資料表讀取資料 |
> |  | **紀錄/容器清單** |  |
> | 動作 | Microsoft.Insights/Logs/ContainerInventory/Read | 從 ContainerInventory 資料表讀取資料 |
> |  | **紀錄/容器紀錄** |  |
> | 動作 | Microsoft.Insights/Logs/ContainerLog/Read | 從 ContainerLog 資料表讀取資料 |
> |  | **紀錄/容器服務紀錄** |  |
> | 動作 | Microsoft.Insights/Logs/ContainerServiceLog/Read | 從 ContainerServiceLog 資料表讀取資料 |
> |  | **日誌/裝置AppCrash** |  |
> | 動作 | Microsoft.Insights/Logs/DeviceAppCrash/Read | 從 DeviceAppCrash 資料表讀取資料 |
> |  | **紀錄/裝置App啟動** |  |
> | 動作 | Microsoft.Insights/Logs/DeviceAppLaunch/Read | 從 DeviceAppLaunch 資料表讀取資料 |
> |  | **記錄/裝置行事曆** |  |
> | 動作 | Microsoft.Insights/Logs/DeviceCalendar/Read | 從 DeviceCalendar 資料表讀取資料 |
> |  | **紀錄/裝置清理** |  |
> | 動作 | Microsoft.Insights/Logs/DeviceCleanup/Read | 從 DeviceCleanup 資料表讀取資料 |
> |  | **紀錄/裝置連線工作階段** |  |
> | 動作 | Microsoft.Insights/Logs/DeviceConnectSession/Read | 從 DeviceConnectSession 資料表讀取資料 |
> |  | **記錄/裝置** |  |
> | 動作 | Microsoft.Insights/Logs/DeviceEtw/Read | 從 DeviceEtw 資料表讀取資料 |
> |  | **紀錄/裝置硬體執行狀況** |  |
> | 動作 | Microsoft.Insights/Logs/DeviceHardwareHealth/Read | 從 DeviceHardwareHealth 資料表讀取資料 |
> |  | **紀錄/裝置執行狀況** |  |
> | 動作 | Microsoft.Insights/Logs/DeviceHealth/Read | 從 DeviceHealth 資料表讀取資料 |
> |  | **紀錄/裝置偵測訊號** |  |
> | 動作 | Microsoft.Insights/Logs/DeviceHeartbeat/Read | 從 DeviceHeartbeat 資料表讀取資料 |
> |  | **日誌/裝置Skype檢測** |  |
> | 動作 | Microsoft.Insights/Logs/DeviceSkypeHeartbeat/Read | 從 DeviceSkypeHeartbeat 資料表讀取資料 |
> |  | **紀錄/裝置Skypesignin** |  |
> | 動作 | Microsoft.Insights/Logs/DeviceSkypeSignIn/Read | 從 DeviceSkypeSignIn 資料表讀取資料 |
> |  | **紀錄/裝置睡眠狀態** |  |
> | 動作 | Microsoft.Insights/Logs/DeviceSleepState/Read | 從 DeviceSleepState 資料表讀取資料 |
> |  | **紀錄/DHApp 失敗** |  |
> | 動作 | Microsoft.Insights/Logs/DHAppFailure/Read | 從 DHAppFailure 資料表讀取資料 |
> |  | **日誌/DHApp 可靠性** |  |
> | 動作 | Microsoft.Insights/Logs/DHAppReliability/Read | 從 DHAppReliability 資料表讀取資料 |
> |  | **紀錄/DH驅動程式可靠性** |  |
> | 動作 | Microsoft.Insights/Logs/DHDriverReliability/Read | 從 DHDriverReliability 資料表讀取資料 |
> |  | **日誌/DHLogon 失敗** |  |
> | 動作 | Microsoft.Insights/Logs/DHLogonFailures/Read | 從 DHLogonFailures 資料表讀取資料 |
> |  | **日誌/DHLogon 指標** |  |
> | 動作 | Microsoft.Insights/Logs/DHLogonMetrics/Read | 從 DHLogonMetrics 資料表讀取資料 |
> |  | **紀錄/DHOSCrash資料** |  |
> | 動作 | Microsoft.Insights/Logs/DHOSCrashData/Read | 從 DHOSCrashData 資料表讀取資料 |
> |  | **紀錄/DHOS 可靠性** |  |
> | 動作 | Microsoft.Insights/Logs/DHOSReliability/Read | 從 DHOSReliability 資料表讀取資料 |
> |  | **紀錄/DHWipApp學習** |  |
> | 動作 | Microsoft.Insights/Logs/DHWipAppLearning/Read | 從 DHWipAppLearning 資料表讀取資料 |
> |  | **紀錄/Dns事件** |  |
> | 動作 | Microsoft.Insights/Logs/DnsEvents/Read | 從 DnsEvents 資料表讀取資料 |
> |  | **紀錄/Dns 清單** |  |
> | 動作 | Microsoft.Insights/Logs/DnsInventory/Read | 從 DnsInventory 資料表讀取資料 |
> |  | **紀錄/ETW事件** |  |
> | 動作 | Microsoft.Insights/Logs/ETWEvent/Read | 從 ETWEvent 資料表讀取資料 |
> |  | **紀錄/事件** |  |
> | 動作 | Microsoft.Insights/Logs/Event/Read | 從 Event 資料表讀取資料 |
> |  | **紀錄/交換評估建議** |  |
> | 動作 | Microsoft.Insights/Logs/ExchangeAssessmentRecommendation/Read | 從 ExchangeAssessmentRecommendation 資料表讀取資料 |
> |  | **紀錄/交換線上評估建議** |  |
> | 動作 | Microsoft.Insights/Logs/ExchangeOnlineAssessmentRecommendation/Read | 從 ExchangeOnlineAssessmentRecommendation 資料表讀取資料 |
> |  | **紀錄/偵測訊號** |  |
> | 動作 | Microsoft.Insights/Logs/Heartbeat/Read | 從 Heartbeat 資料表讀取資料 |
> |  | **紀錄/IIS評估建議** |  |
> | 動作 | Microsoft.Insights/Logs/IISAssessmentRecommendation/Read | 從 IISAssessmentRecommendation 資料表讀取資料 |
> |  | **紀錄/入站連線** |  |
> | 動作 | Microsoft.Insights/Logs/InboundConnection/Read | 從 InboundConnection 資料表讀取資料 |
> |  | **日誌/庫貝諾德清單** |  |
> | 動作 | Microsoft.Insights/Logs/KubeNodeInventory/Read | 從 KubeNodeInventory 資料表讀取資料 |
> |  | **日誌/庫貝波德庫存** |  |
> | 動作 | Microsoft.Insights/Logs/KubePodInventory/Read | 從 KubePodInventory 資料表讀取資料 |
> |  | **記錄/Linux稽核紀錄** |  |
> | 動作 | Microsoft.Insights/Logs/LinuxAuditLog/Read | 從 LinuxAuditLog 資料表讀取資料 |
> |  | **紀錄/MA 應用程式** |  |
> | 動作 | Microsoft.Insights/Logs/MAApplication/Read | 從 MAApplication 資料表讀取資料 |
> |  | **紀錄/MA 應用程式執行狀況** |  |
> | 動作 | Microsoft.Insights/Logs/MAApplicationHealth/Read | 從 MAApplicationHealth 資料表讀取資料 |
> |  | **紀錄/MA 應用程式執行狀況替代版本** |  |
> | 動作 | Microsoft.Insights/Logs/MAApplicationHealthAlternativeVersions/Read | 從 MAApplicationHealthAlternativeVersions 資料表讀取資料 |
> |  | **紀錄/MA 應用程式健康問題** |  |
> | 動作 | Microsoft.Insights/Logs/MAApplicationHealthIssues/Read | 從 MAApplicationHealthIssues 資料表讀取資料 |
> |  | **紀錄/MA 應用程式實體** |  |
> | 動作 | Microsoft.Insights/Logs/MAApplicationInstance/Read | 從 MAApplicationInstance 資料表讀取資料 |
> |  | **紀錄/MA 應用程式實體就緒性** |  |
> | 動作 | Microsoft.Insights/Logs/MAApplicationInstanceReadiness/Read | 從 MAApplicationInstanceReadiness 資料表讀取資料 |
> |  | **紀錄/MA應用程式就緒** |  |
> | 動作 | Microsoft.Insights/Logs/MAApplicationReadiness/Read | 從 MAApplicationReadiness 資料表讀取資料 |
> |  | **紀錄/MA 部署計劃** |  |
> | 動作 | Microsoft.Insights/Logs/MADeploymentPlan/Read | 從 MADeploymentPlan 資料表讀取資料 |
> |  | **紀錄/MADevice** |  |
> | 動作 | Microsoft.Insights/Logs/MADevice/Read | 從 MADevice 資料表讀取資料 |
> |  | **紀錄/MADevicePnP 執行狀況** |  |
> | 動作 | Microsoft.Insights/Logs/MADevicePnPHealth/Read | 從 MADevicePnPHealth 資料表讀取資料 |
> |  | **紀錄/MADevicePnP健康替代版本** |  |
> | 動作 | Microsoft.Insights/Logs/MADevicePnPHealthAlternativeVersions/Read | 從 MADevicePnPHealthAlternativeVersions 資料表讀取資料 |
> |  | **日誌/MADevicePnP健康問題** |  |
> | 動作 | Microsoft.Insights/Logs/MADevicePnPHealthIssues/Read | 從 MADevicePnPHealthIssues 資料表讀取資料 |
> |  | **紀錄/MADevice 就緒** |  |
> | 動作 | Microsoft.Insights/Logs/MADeviceReadiness/Read | 從 MADeviceReadiness 資料表讀取資料 |
> |  | **紀錄/MADriver 實體就緒性** |  |
> | 動作 | Microsoft.Insights/Logs/MADriverInstanceReadiness/Read | 從 MADriverInstanceReadiness 資料表讀取資料 |
> |  | **紀錄/MADriver 就緒** |  |
> | 動作 | Microsoft.Insights/Logs/MADriverReadiness/Read | 從 MADriverReadiness 資料表讀取資料 |
> |  | **日誌/MAOfficeAddin** |  |
> | 動作 | Microsoft.Insights/Logs/MAOfficeAddin/Read | 從 MAOfficeAddin 資料表讀取資料 |
> |  | **日誌/MAOfficeAddin 健康** |  |
> | 動作 | Microsoft.Insights/Logs/MAOfficeAddinHealth/Read | 從 MAOfficeAddinHealth 資料表讀取資料 |
> |  | **日誌/MAOfficeAddin 健康問題** |  |
> | 動作 | Microsoft.Insights/Logs/MAOfficeAddinHealthIssues/Read | 從 MAOfficeAddinHealthIssues 資料表讀取資料 |
> |  | **日誌/MAOfficeAddin實例** |  |
> | 動作 | Microsoft.Insights/Logs/MAOfficeAddinInstance/Read | 從 MAOfficeAddinInstance 資料表讀取資料 |
> |  | **日誌/MAOfficeAddin實例就緒性** |  |
> | 動作 | Microsoft.Insights/Logs/MAOfficeAddinInstanceReadiness/Read | 從 MAOfficeAddinInstanceReadiness 資料表讀取資料 |
> |  | **日誌/MAOfficeAddin 準備** |  |
> | 動作 | Microsoft.Insights/Logs/MAOfficeAddinReadiness/Read | 從 MAOfficeAddinReadiness 資料表讀取資料 |
> |  | **日誌/MAOfficeApp** |  |
> | 動作 | Microsoft.Insights/Logs/MAOfficeApp/Read | 從 MAOfficeApp 資料表讀取資料 |
> |  | **日誌/MAOfficeApp運行狀況** |  |
> | 動作 | Microsoft.Insights/Logs/MAOfficeAppHealth/Read | 從 MAOfficeAppHealth 資料表讀取資料 |
> |  | **日誌/MAOfficeApp實例** |  |
> | 動作 | Microsoft.Insights/Logs/MAOfficeAppInstance/Read | 從 MAOfficeAppInstance 資料表讀取資料 |
> |  | **紀錄/MAOffice 應用程式就緒** |  |
> | 動作 | Microsoft.Insights/Logs/MAOfficeAppReadiness/Read | 從 MAOfficeAppReadiness 資料表讀取資料 |
> |  | **紀錄/MAOffice 編譯資訊** |  |
> | 動作 | Microsoft.Insights/Logs/MAOfficeBuildInfo/Read | 從 MAOfficeBuildInfo 資料表讀取資料 |
> |  | **日誌/MAOffice 貨幣評估** |  |
> | 動作 | Microsoft.Insights/Logs/MAOfficeCurrencyAssessment/Read | 從 MAOfficeCurrencyAssessment 資料表讀取資料 |
> |  | **日誌/MAOffice 貨幣評估每日計數** |  |
> | 動作 | Microsoft.Insights/Logs/MAOfficeCurrencyAssessmentDailyCounts/Read | 從 MAOfficeCurrencyAssessmentDailyCounts 資料表讀取資料 |
> |  | **紀錄/MAOffice 部署狀態** |  |
> | 動作 | Microsoft.Insights/Logs/MAOfficeDeploymentStatus/Read | 從 MAOfficeDeploymentStatus 資料表讀取資料 |
> |  | **日誌/MAOfficeMacroHealth** |  |
> | 動作 | Microsoft.Insights/Logs/MAOfficeMacroHealth/Read | 從 MAOfficeMacroHealth 資料表讀取資料 |
> |  | **日誌/MAOffice 宏觀健康問題** |  |
> | 動作 | Microsoft.Insights/Logs/MAOfficeMacroHealthIssues/Read | 從 MAOfficeMacroHealthIssues 資料表讀取資料 |
> |  | **日誌/MAOfficeMacro 發佈實例就緒性** |  |
> | 動作 | Microsoft.Insights/Logs/MAOfficeMacroIssueInstanceReadiness/Read | 從 MAOfficeMacroIssueInstanceReadiness 資料表讀取資料 |
> |  | **日誌/MAOfficeMacro 問題就緒** |  |
> | 動作 | Microsoft.Insights/Logs/MAOfficeMacroIssueReadiness/Read | 從 MAOfficeMacroIssueReadiness 資料表讀取資料 |
> |  | **日誌/MAOfficeMacro 摘要** |  |
> | 動作 | Microsoft.Insights/Logs/MAOfficeMacroSummary/Read | 從 MAOfficeMacroSummary 資料表讀取資料 |
> |  | **日誌/MAOfficeSuite** |  |
> | 動作 | Microsoft.Insights/Logs/MAOfficeSuite/Read | 從 MAOfficeSuite 資料表讀取資料 |
> |  | **記錄/MAOfficeSuite實例** |  |
> | 動作 | Microsoft.Insights/Logs/MAOfficeSuiteInstance/Read | 從 MAOfficeSuiteInstance 資料表讀取資料 |
> |  | **紀錄/MA 建議導機裝置** |  |
> | 動作 | Microsoft.Insights/Logs/MAProposedPilotDevices/Read | 從 MAProposedPilotDevices 資料表讀取資料 |
> |  | **紀錄/MAWindows 建構資訊** |  |
> | 動作 | Microsoft.Insights/Logs/MAWindowsBuildInfo/Read | 從 MAWindowsBuildInfo 資料表讀取資料 |
> |  | **日誌/MAWindows貨幣評估** |  |
> | 動作 | Microsoft.Insights/Logs/MAWindowsCurrencyAssessment/Read | 從 MAWindowsCurrencyAssessment 資料表讀取資料 |
> |  | **日誌/MAWindows貨幣評估每日計數** |  |
> | 動作 | Microsoft.Insights/Logs/MAWindowsCurrencyAssessmentDailyCounts/Read | 從 MAWindowsCurrencyAssessmentDailyCounts 資料表讀取資料 |
> |  | **紀錄/MAWindows 部署狀態** |  |
> | 動作 | Microsoft.Insights/Logs/MAWindowsDeploymentStatus/Read | 從 MAWindowsDeploymentStatus 資料表讀取資料 |
> |  | **紀錄/MAWindowsSysReq實體就緒性** |  |
> | 動作 | Microsoft.Insights/Logs/MAWindowsSysReqInstanceReadiness/Read | 從 MAWindowsSysReqInstanceReadiness 資料表讀取資料 |
> |  | **紀錄/網路監控** |  |
> | 動作 | Microsoft.Insights/Logs/NetworkMonitoring/Read | 從 NetworkMonitoring 資料表讀取資料 |
> |  | **記錄/辦公室活動** |  |
> | 動作 | Microsoft.Insights/Logs/OfficeActivity/Read | 從 OfficeActivity 資料表讀取資料 |
> |  | **紀錄/操作** |  |
> | 動作 | Microsoft.Insights/Logs/Operation/Read | 從 Operation 資料表讀取資料 |
> |  | **紀錄/出站連線** |  |
> | 動作 | Microsoft.Insights/Logs/OutboundConnection/Read | 從 OutboundConnection 資料表讀取資料 |
> |  | **紀錄/Perf** |  |
> | 動作 | Microsoft.Insights/Logs/Perf/Read | 從 Perf 資料表讀取資料 |
> |  | **紀錄/保護狀態** |  |
> | 動作 | Microsoft.Insights/Logs/ProtectionStatus/Read | 從 ProtectionStatus 資料表讀取資料 |
> |  | **紀錄/保留 Azure 公共欄位** |  |
> | 動作 | Microsoft.Insights/Logs/ReservedAzureCommonFields/Read | 從 ReservedAzureCommonFields 資料表讀取資料 |
> |  | **紀錄/保留公共欄位** |  |
> | 動作 | Microsoft.Insights/Logs/ReservedCommonFields/Read | 從 ReservedCommonFields 資料表讀取資料 |
> |  | **紀錄/SCCM 評估建議** |  |
> | 動作 | Microsoft.Insights/Logs/SCCMAssessmentRecommendation/Read | 從 SCCMAssessmentRecommendation 資料表讀取資料 |
> |  | **紀錄/SCOM 評估建議** |  |
> | 動作 | Microsoft.Insights/Logs/SCOMAssessmentRecommendation/Read | 從 SCOMAssessmentRecommendation 資料表讀取資料 |
> |  | **日誌/安全警報** |  |
> | 動作 | Microsoft.Insights/Logs/SecurityAlert/Read | 從 SecurityAlert 資料表讀取資料 |
> |  | **日誌/安全基線** |  |
> | 動作 | Microsoft.Insights/Logs/SecurityBaseline/Read | 從 SecurityBaseline 資料表讀取資料 |
> |  | **紀錄/安全基線摘要** |  |
> | 動作 | Microsoft.Insights/Logs/SecurityBaselineSummary/Read | 從 SecurityBaselineSummary 資料表讀取資料 |
> |  | **紀錄/安全偵測** |  |
> | 動作 | Microsoft.Insights/Logs/SecurityDetection/Read | 從 SecurityDetection 資料表讀取資料 |
> |  | **紀錄/安全事件** |  |
> | 動作 | Microsoft.Insights/Logs/SecurityEvent/Read | 從 SecurityEvent 資料表讀取資料 |
> |  | **紀錄/服務結構操作事件** |  |
> | 動作 | Microsoft.Insights/Logs/ServiceFabricOperationalEvent/Read | 從 ServiceFabricOperationalEvent 資料表讀取資料 |
> |  | **紀錄/服務結構可靠參與者事件** |  |
> | 動作 | Microsoft.Insights/Logs/ServiceFabricReliableActorEvent/Read | 從 ServiceFabricReliableActorEvent 資料表讀取資料 |
> |  | **紀錄/服務結構可靠服務事件** |  |
> | 動作 | Microsoft.Insights/Logs/ServiceFabricReliableServiceEvent/Read | 從 ServiceFabricReliableServiceEvent 資料表讀取資料 |
> |  | **紀錄/SfB評估建議** |  |
> | 動作 | Microsoft.Insights/Logs/SfBAssessmentRecommendation/Read | 從 SfBAssessmentRecommendation 資料表讀取資料 |
> |  | **紀錄/SfB線上評看建議** |  |
> | 動作 | Microsoft.Insights/Logs/SfBOnlineAssessmentRecommendation/Read | 從 SfBOnlineAssessmentRecommendation 資料表讀取資料 |
> |  | **紀錄/共享點線上評估建議** |  |
> | 動作 | Microsoft.Insights/Logs/SharePointOnlineAssessmentRecommendation/Read | 從 SharePointOnlineAssessmentRecommendation 資料表讀取資料 |
> |  | **紀錄/評估建議** |  |
> | 動作 | Microsoft.Insights/Logs/SPAssessmentRecommendation/Read | 從 SPAssessmentRecommendation 資料表讀取資料 |
> |  | **紀錄/SQL評估建議** |  |
> | 動作 | Microsoft.Insights/Logs/SQLAssessmentRecommendation/Read | 從 SQLAssessmentRecommendation 資料表讀取資料 |
> |  | **紀錄/SQLQuery 效能** |  |
> | 動作 | Microsoft.Insights/Logs/SQLQueryPerformance/Read | 從 SQLQueryPerformance 資料表讀取資料 |
> |  | **紀錄/系統紀錄** |  |
> | 動作 | Microsoft.Insights/Logs/Syslog/Read | 從 Syslog 資料表讀取資料 |
> |  | **紀錄/系統事件** |  |
> | 動作 | Microsoft.Insights/Logs/SysmonEvent/Read | 從 SysmonEvent 資料表讀取資料 |
> |  | **記錄/表.自訂** |  |
> | 動作 | 微軟.見解/日誌/表格.自定義/閱讀 | 從任何自訂記錄檔讀取資料 |
> |  | **日誌/UAApp** |  |
> | 動作 | Microsoft.Insights/Logs/UAApp/Read | 從 UAApp 資料表讀取資料 |
> |  | **紀錄/UA 電腦** |  |
> | 動作 | Microsoft.Insights/Logs/UAComputer/Read | 從 UAComputer 資料表讀取資料 |
> |  | **紀錄/UA 電腦排名** |  |
> | 動作 | Microsoft.Insights/Logs/UAComputerRank/Read | 從 UAComputerRank 資料表讀取資料 |
> |  | **紀錄/UADriver** |  |
> | 動作 | Microsoft.Insights/Logs/UADriver/Read | 從 UADriver 資料表讀取資料 |
> |  | **紀錄/UADriver問題代碼** |  |
> | 動作 | Microsoft.Insights/Logs/UADriverProblemCodes/Read | 從 UADriverProblemCodes 資料表讀取資料 |
> |  | **紀錄/UA回饋** |  |
> | 動作 | Microsoft.Insights/Logs/UAFeedback/Read | 從 UAFeedback 資料表讀取資料 |
> |  | **紀錄/UA 硬體安全性** |  |
> | 動作 | Microsoft.Insights/Logs/UAHardwareSecurity/Read | 從 UAHardwareSecurity 資料表讀取資料 |
> |  | **日誌/UAIESite 發現** |  |
> | 動作 | Microsoft.Insights/Logs/UAIESiteDiscovery/Read | 從 UAIESiteDiscovery 資料表讀取資料 |
> |  | **紀錄/UAOfficeADDIn** |  |
> | 動作 | Microsoft.Insights/Logs/UAOfficeAddIn/Read | 從 UAOfficeAddIn 資料表讀取資料 |
> |  | **紀錄/UA 建議行動計劃** |  |
> | 動作 | Microsoft.Insights/Logs/UAProposedActionPlan/Read | 從 UAProposedActionPlan 資料表讀取資料 |
> |  | **紀錄/UASysReq 問題** |  |
> | 動作 | Microsoft.Insights/Logs/UASysReqIssue/Read | 從 UASysReqIssue 資料表讀取資料 |
> |  | **紀錄/UA 升級電腦** |  |
> | 動作 | Microsoft.Insights/Logs/UAUpgradedComputer/Read | 從 UAUpgradedComputer 資料表讀取資料 |
> |  | **紀錄/更新** |  |
> | 動作 | Microsoft.Insights/Logs/Update/Read | 從 Update 資料表讀取資料 |
> |  | **紀錄/更新執行進度** |  |
> | 動作 | Microsoft.Insights/Logs/UpdateRunProgress/Read | 從 UpdateRunProgress 資料表讀取資料 |
> |  | **紀錄/更新摘要** |  |
> | 動作 | Microsoft.Insights/Logs/UpdateSummary/Read | 從 UpdateSummary 資料表讀取資料 |
> |  | **紀錄/使用方式** |  |
> | 動作 | Microsoft.Insights/Logs/Usage/Read | 從 Usage 資料表讀取資料 |
> |  | **日誌/W3CIIS日誌** |  |
> | 動作 | Microsoft.Insights/Logs/W3CIISLog/Read | 從 W3CIISLog 資料表讀取資料 |
> |  | **紀錄/WaaS 部署狀態** |  |
> | 動作 | Microsoft.Insights/Logs/WaaSDeploymentStatus/Read | 從 WaaSDeploymentStatus 資料表讀取資料 |
> |  | **紀錄/WaaS內幕狀態** |  |
> | 動作 | Microsoft.Insights/Logs/WaaSInsiderStatus/Read | 從 WaaSInsiderStatus 資料表讀取資料 |
> |  | **紀錄/WaaS 更新狀態** |  |
> | 動作 | Microsoft.Insights/Logs/WaaSUpdateStatus/Read | 從 WaaSUpdateStatus 資料表讀取資料 |
> |  | **紀錄/WDAV 狀態** |  |
> | 動作 | Microsoft.Insights/Logs/WDAVStatus/Read | 從 WDAVStatus 資料表讀取資料 |
> |  | **日誌/WDAV威脅** |  |
> | 動作 | Microsoft.Insights/Logs/WDAVThreat/Read | 從 WDAVThreat 資料表讀取資料 |
> |  | **紀錄/Windows 客戶端評估建議** |  |
> | 動作 | Microsoft.Insights/Logs/WindowsClientAssessmentRecommendation/Read | 從 WindowsClientAssessmentRecommendation 資料表讀取資料 |
> |  | **紀錄/視窗防火牆** |  |
> | 動作 | Microsoft.Insights/Logs/WindowsFirewall/Read | 從 WindowsFirewall 資料表讀取資料 |
> |  | **紀錄/Windows 伺服器評估建議** |  |
> | 動作 | Microsoft.Insights/Logs/WindowsServerAssessmentRecommendation/Read | 從 WindowsServerAssessmentRecommendation 資料表讀取資料 |
> |  | **紀錄/線資料** |  |
> | 動作 | Microsoft.Insights/Logs/WireData/Read | 從 WireData 資料表讀取資料 |
> |  | **日誌/WUDO聚合狀態** |  |
> | 動作 | Microsoft.Insights/Logs/WUDOAggregatedStatus/Read | 從 WUDOAggregatedStatus 資料表讀取資料 |
> |  | **紀錄/WUDO 狀態** |  |
> | 動作 | Microsoft.Insights/Logs/WUDOStatus/Read | 從 WUDOStatus 資料表讀取資料 |
> |  | **指標警報** |  |
> | 動作 | Microsoft.Insights/MetricAlerts/Write | 建立或更新計量警示 |
> | 動作 | Microsoft.Insights/MetricAlerts/Delete | 刪除計量警示 |
> | 動作 | Microsoft.Insights/MetricAlerts/Read | 讀取計量警示 |
> |  | **指標警示/狀態** |  |
> | 動作 | Microsoft.Insights/MetricAlerts/Status/Read | 讀取計量警示狀態 |
> |  | **指標基線** |  |
> | 動作 | 微軟.見解/指標基線/閱讀 | 讀取指標基線 |
> |  | **指標定義** |  |
> | 動作 | Microsoft.Insights/MetricDefinitions/Read | 讀取計量定義 |
> |  | **指標定義/微軟.見解** |  |
> | 動作 | Microsoft.Insights/MetricDefinitions/Microsoft.Insights/Read | 讀取計量定義 |
> |  | **指標定義/供應商/微軟.Insights** |  |
> | 動作 | Microsoft.Insights/MetricDefinitions/providers/Microsoft.Insights/Read | 讀取計量定義 |
> |  | **指標命名空間** |  |
> | 動作 | 微軟.見解/指標命名空間/閱讀 | 讀取指標命名空間 |
> |  | **計量** |  |
> | 動作 | Microsoft.Insights/Metrics/Read | 讀取計量 |
> |  | **指標/微軟.見解** |  |
> | 動作 | 微軟.見解/指標/微軟.見解/閱讀 | 讀取計量 |
> |  | **指標/提供程式/指標** |  |
> | 動作 | Microsoft.Insights/Metrics/providers/Metrics/Read | 讀取計量 |
> |  | **myWorkbooks** |  |
> | 動作 | 微軟.見解/我的工作簿/寫作 | 建立或更新專用工作簿 |
> | 動作 | 微軟.見解/我的工作簿/閱讀 | 閱讀私人工作簿 |
> |  | **作業** |  |
> | 動作 | Microsoft.Insights/Operations/Read | 讀取作業 |
> |  | **私有連結範圍操作狀態** |  |
> | 動作 | 微軟.見解/私人連結範圍操作狀態/閱讀 | 讀取專用連結的功能狀態 |
> |  | **私人連結範圍** |  |
> | 動作 | 微軟.見解/私人連結範圍/閱讀 | 讀取專用連結的功能 |
> | 動作 | 微軟.見解/私人連結範圍/寫入 | 建立或更新專用連結的範圍 |
> | 動作 | 微軟.見解/私人連結範圍/刪除 | 移除專用連結範圍 |
> |  | **私有連結範圍/私有端點連接 Proxies** |  |
> | 動作 | 微軟.Insights/私人連結範圍/私人端接連結Proxies/閱讀 | 讀取專用終結點連線代理 |
> | 動作 | 微軟.見解/私人連結範圍/私人端接連結Proxies/寫入 | 建立或更新專用終結點連線代理 |
> | 動作 | 微軟.Insights/私人連結範圍/私人端接連結Proxies/刪除 | 移除專用終結點連線代理 |
> | 動作 | 微軟.Insights/私人連結範圍/私人端接連結Proxies/驗證/操作 | 驗證專用終結點連線代理 |
> |  | **私有連結範圍/專用端點連接** |  |
> | 動作 | 微軟.Insights/私人連結範圍/私人端點連接/閱讀 | 讀取專用終結點連線 |
> | 動作 | 微軟.Insights/私人連結範圍/私人端點連接/寫入 | 建立或更新專用終結點連線 |
> | 動作 | 微軟.Insights/私人連結範圍/私人端點連接/刪除 | 移除專用終結點連線 |
> |  | **私有連結範圍/私人連結資源** |  |
> | 動作 | 微軟.見解/私人連結範圍/私人連結資源/閱讀 | 讀取專用連結資源 |
> |  | **私有連結範圍/範圍資源** |  |
> | 動作 | 微軟.見解/私人連結範圍/範圍資源/閱讀 | 讀取專用連結的功能 |
> | 動作 | 微軟.見解/私人連結範圍/範圍資源/寫入 | 建立或更新專用連結的功能 |
> | 動作 | 微軟.見解/私人連結範圍/範圍資源/刪除 | 移除私人連結範圍資源 |
> |  | **排程查詢規則** |  |
> | 動作 | Microsoft.Insights/ScheduledQueryRules/Write | 寫入排程的查詢規則 |
> | 動作 | Microsoft.Insights/ScheduledQueryRules/Read | 讀取排程的查詢規則 |
> | 動作 | Microsoft.Insights/ScheduledQueryRules/Delete | 刪除排程的查詢規則 |
> |  | **租用戶** |  |
> | 動作 | Microsoft.Insights/Tenants/Register/Action | 將 Microsoft Insights 提供者初始化 |
> |  | **網路測試** |  |
> | 動作 | Microsoft.Insights/Webtests/Write | 寫入至 Webtest 設定 |
> | 動作 | Microsoft.Insights/Webtests/Delete | 刪除 Webtest 設定 |
> | 動作 | Microsoft.Insights/Webtests/Read | 讀取 Webtest 設定 |
> |  | **網路測試/取得權杖** |  |
> | 動作 | Microsoft.Insights/Webtests/GetToken/Read | 讀取 Webtest 權杖 |
> |  | **網路測試/指標定義** |  |
> | 動作 | Microsoft.Insights/Webtests/MetricDefinitions/Read | 讀取 Webtest 計量定義 |
> |  | **網路測試/指標** |  |
> | 動作 | Microsoft.Insights/Webtests/Metrics/Read | 讀取 Webtest 計量 |
> |  | **活頁簿** |  |
> | 動作 | 微軟.見解/工作簿/寫入 | 建立或更新工作簿 |
> | 動作 | 微軟.見解/工作簿/刪除 | 刪除活頁簿 |
> | 動作 | 微軟.見解/工作簿/閱讀 | 閱讀工作簿 |
> |  | **資料收集規則/資料** |  |
> | DataAction | 微軟.見解/資料收集規則/資料/寫入 | 將資料傳送到資料收集規則 |
> |  | **計量** |  |
> | DataAction | Microsoft.Insights/Metrics/Write | 寫入計量 |

## <a name="microsoftintune"></a>Microsoft.Intune

微軟監控洞察

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> |  | **診斷設定** |  |
> | 動作 | Microsoft.Intune/diagnosticsettings/write | 正在寫入診斷設定 |
> | 動作 | Microsoft.Intune/diagnosticsettings/read | 正在讀取診斷設定 |
> | 動作 | Microsoft.Intune/diagnosticsettings/delete | 正在刪除診斷設定 |
> |  | **診斷設定類別** |  |
> | 動作 | Microsoft.Intune/diagnosticsettingscategories/read | 正在讀取診斷設定類別 |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

Azure 服務[:IoT 中心](../iot-central/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.IoTCentral/checkNameAvailability/action | 檢查 IoT Central 應用程式名稱是否可用 |
> | 動作 | Microsoft.IoTCentral/checkSubdomainAvailability/action | 檢查 IoT Central 應用程式子網域是否可用 |
> | 動作 | 微軟.IoTCentral/應用程式範本/操作 | 取得 Azure IoT 中央的所有可用應用程式樣本 |
> | 動作 | Microsoft.IoTCentral/register/action | 針對 Azure IoT Central 資源提供者註冊訂用帳戶 |
> |  | **物聯網應用** |  |
> | 動作 | Microsoft.IoTCentral/IoTApps/read | 取得單一 IoT Central 應用程式 |
> | 動作 | Microsoft.IoTCentral/IoTApps/write | 建立或更新 IoT Central 應用程式 |
> | 動作 | Microsoft.IoTCentral/IoTApps/delete | 刪除 IoT Central 應用程式 |
> |  | **操作** |  |
> | 動作 | Microsoft.IoTCentral/operations/read | 取得 IoT Central 應用程式上所有可用的作業 |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

Azure 服務[:Azure 數位孿生](../digital-twins/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.IoTSpaces/register/action | 為 Microsoft.IoTSpaces Graph 資源提供者註冊訂用帳戶以啟用資源的建立作業 |
> |  | **圖形** |  |
> | 動作 | Microsoft.IoTSpaces/Graph/write | 建立 Microsoft.IoTSpaces Graph 資源 |
> | 動作 | Microsoft.IoTSpaces/Graph/read | 取得 Microsoft.IoTSpaces Graph 資源 |
> | 動作 | Microsoft.IoTSpaces/Graph/delete | 刪除 Microsoft.IoTSpaces Graph 資源 |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

Azure 服務:[金鑰保存庫](../key-vault/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.KeyVault/register/action | 註冊訂用帳戶 |
> | 動作 | Microsoft.KeyVault/unregister/action | 取消註冊訂用帳戶 |
> |  | **檢查名稱可用性** |  |
> | 動作 | Microsoft.KeyVault/checkNameAvailability/read | 確認 Key Vault 名稱有效，且並非使用中 |
> |  | **deletedVaults** |  |
> | 動作 | Microsoft.KeyVault/deletedVaults/read | 檢視虛刪除之 Key Vault 的屬性 |
> |  | **hsmPools** |  |
> | 動作 | Microsoft.KeyVault/hsmPools/read | 檢視 HSM 集區的屬性 |
> | 動作 | Microsoft.KeyVault/hsmPools/write | 建立新的 HSM 集區，或更新現有 HSM 集區的屬性 |
> | 動作 | Microsoft.KeyVault/hsmPools/delete | 刪除 HSM 集區 |
> | 動作 | Microsoft.KeyVault/hsmPools/joinVault/action | 將金鑰保存庫加入至 HSM 集區 |
> |  | **位置** |  |
> | 動作 | Microsoft.KeyVault/locations/deleteVirtualNetworkOrSubnets/action | 通知 Microsoft.KeyVault 正在刪除虛擬網路或子網路 |
> |  | **位置/已移除的 Vault** |  |
> | 動作 | Microsoft.KeyVault/locations/deletedVaults/read | 檢視虛刪除之 Key Vault 的屬性 |
> | 動作 | Microsoft.KeyVault/locations/deletedVaults/purge/action | 清除虛刪除的 Key Vault |
> |  | **位置/動作結果** |  |
> | 動作 | Microsoft.KeyVault/locations/operationResults/read | 檢查長時間執行之作業的結果 |
> |  | **操作** |  |
> | 動作 | Microsoft.KeyVault/operations/read | 列出可以對 Microsoft.KeyVault 資源提供者執行的作業 |
> |  | **金庫** |  |
> | 動作 | Microsoft.KeyVault/vaults/read | 檢視 Key Vault 的屬性 |
> | 動作 | Microsoft.KeyVault/vaults/write | 建立新的 Key Vault，或更新現有 Key Vault 的屬性 |
> | 動作 | Microsoft.KeyVault/vaults/delete | 刪除 Key Vault |
> | 動作 | Microsoft.KeyVault/vaults/deploy/action | 在部署 Azure 資源時啟用 Key Vault 中祕密的存取權 |
> |  | **vaults/accessPolicies** |  |
> | 動作 | Microsoft.KeyVault/vaults/accessPolicies/write | 藉由合併或取代來更新現有存取原則，或在保存庫中新增存取原則。 |
> |  | **保存庫/事件網格篩選器** |  |
> | 動作 | 微軟.金鑰庫/保管庫/事件網格篩選器/讀取 | 通知 Microsoft.KeyVault 正在檢視金鑰保管庫的事件網格訂閱 |
> | 動作 | 微軟.金鑰庫/保管庫/事件網格篩選器/寫入 | 通知 Microsoft.KeyVault 正在建立金鑰保管庫的新事件網格訂閱 |
> | 動作 | 微軟.金鑰庫/保管庫/事件網格篩選器/刪除 | 通知 Microsoft.KeyVault 金鑰保管庫的事件網格訂閱正在被刪除 |
> |  | **vaults/secrets** |  |
> | 動作 | Microsoft.KeyVault/vaults/secrets/read | 查看機密的屬性,但不能查看其值。 |
> | 動作 | Microsoft.KeyVault/vaults/secrets/write | 創建新機密或更新現有機密的值。 |
> |  | **保存庫/憑證庫** |  |
> | DataAction | 微軟.密鑰庫/保管庫/證書庫/刪除 | 移除憑證 Issuser |
> | DataAction | 微軟.金鑰庫/保管庫/證書庫/讀取 | 讀取憑證 Issuser |
> | DataAction | 微軟.金鑰庫/保管庫/證書庫/寫入 | 寫入憑證 Issuser |
> |  | **保存庫/憑證連絡人** |  |
> | DataAction | 微軟.密鑰庫/保管庫/證書連絡人/寫入 | 管理憑證連絡人 |
> |  | **保存庫/憑證** |  |
> | DataAction | 微軟.金鑰庫/保管庫/證書/刪除 | 刪除證書。 |
> | DataAction | 微軟.金鑰庫/保管庫/證書/讀取 | 在指定的金鑰保管庫中列出證書,或獲取有關證書的資訊。 |
> | DataAction | 微軟.金鑰庫/保管庫/證書/備份/操作 | 建立證書的備份檔。 該檔可用於還原同一訂閱的密鑰保管庫中的證書。 可能會施加限制。 |
> | DataAction | 微軟.金鑰庫/保管庫/證書/清除/操作 | 清除證書,使其無法恢復。 |
> | DataAction | 微軟.金鑰庫/保管庫/證書/更新/操作 | 更新與給定證書關聯的指定屬性。 |
> | DataAction | 微軟.金鑰庫/保管庫/證書/創建/操作 | 創建新證書。 |
> | DataAction | 微軟.金鑰庫/保管庫/證書/匯入/操作 | 將包含私鑰的現有有效證書導入 Azure 密鑰保管庫。 要導入的證書可以是 PFX 或 PEM 格式。 |
> | DataAction | 微軟.金鑰庫/保管庫/證書/恢復/操作 | 恢復已刪除的證書。 該操作執行"刪除"操作的反轉。 該操作適用於啟用軟刪除的保管庫,必須在保留間隔期間頒發。 |
> | DataAction | 微軟.金鑰庫/保管庫/證書/恢復/操作 | 將備份的證書及其所有版本還原到保管庫。 |
> |  | **保存庫/金鑰** |  |
> | DataAction | 微軟.鑰匙庫/保管庫/金鑰/讀取 | 列出指定保管庫中的鍵,或讀取密鑰的屬性和公共材料。<br>對於非對稱密鑰,此操作公開公鑰,包括執行公鑰演演演算法(如加密和驗證簽名)的能力。<br>私鑰和對稱鍵永遠不會公開。 |
> | DataAction | 微軟.金鑰庫/保管庫/金鑰/更新/操作 | 更新與給定金鑰關聯的指定屬性。 |
> | DataAction | 微軟.金鑰庫/保管庫/金鑰/創建/操作 | 創建新鍵。 |
> | DataAction | 微軟.金鑰庫/保管庫/金鑰/匯入/操作 | 匯出外部建立的金鑰、加以儲存，然後將金鑰參數和屬性傳回至用戶端。 |
> | DataAction | 微軟.金鑰庫/保管庫/金鑰/恢復/操作 | 回復已刪除的金鑰。 該操作執行"刪除"操作的反轉。 該操作適用於啟用軟刪除的保管庫,必須在保留間隔期間頒發。 |
> | DataAction | 微軟.金鑰庫/保管庫/金鑰/恢復/操作 | 將備份的密鑰及其所有版本還原到保管庫。 |
> | DataAction | 微軟.金鑰庫/保管庫/金鑰/刪除 | 刪除金鑰。 |
> | DataAction | 微軟.金鑰庫/保管庫/金鑰/備份/操作 | 建立金鑰的備份檔。 該檔可用於還原同一訂閱的密鑰保管庫中的密鑰。 可能會施加限制。 |
> | DataAction | 微軟.金鑰庫/保管庫/金鑰/清除/操作 | 清除金鑰,使其無法恢復。 |
> | DataAction | 微軟.金鑰庫/保管庫/金鑰/加密/操作 | 使用金鑰加密純文字。 請注意,如果密鑰是非對稱的,則此操作可以由具有讀取訪問許可權的主體執行。 |
> | DataAction | 微軟.金鑰庫/保管庫/金鑰/解密/操作 | 使用金鑰解密密碼文字。 |
> | DataAction | 微軟.鑰匙庫/保管庫/密鑰/包裝/操作 | 使用金鑰保管庫密鑰包裝對稱密鑰。 請注意,如果金鑰保管庫密鑰是不對稱的,則可以使用讀取訪問執行此操作。 |
> | DataAction | 微軟.金鑰庫/保管庫/金鑰/解包/操作 | 使用金鑰保管庫密鑰取消包裝對稱密鑰。 |
> | DataAction | 微軟.鑰匙庫/保管庫/鑰匙/簽名/操作 | 用鍵簽名哈希。 |
> | DataAction | 微軟.金鑰庫/保管庫/金鑰/驗證/操作 | 驗證哈希。 請注意,如果密鑰是非對稱的,則此操作可以由具有讀取訪問許可權的主體執行。 |
> |  | **vaults/secrets** |  |
> | DataAction | 微軟.密鑰庫/保管庫/機密/刪除 | 刪除機密。 |
> | DataAction | 微軟.鑰匙庫/保管庫/機密/備份/操作 | 建立機密的備份檔。 該檔可用於還原同一訂閱的密鑰保管庫中的機密。 可能會施加限制。 |
> | DataAction | 微軟.鑰匙庫/保管庫/機密/清除/操作 | 清除一個秘密,使其無法恢復。 |
> | DataAction | 微軟.金鑰庫/保管庫/機密/更新/操作 | 更新與給定機密關聯的指定屬性。 |
> | DataAction | 微軟.密鑰庫/保管庫/機密/恢復/操作 | 恢復已刪除的機密。 該操作執行"刪除"操作的反轉。 該操作適用於啟用軟刪除的保管庫,必須在保留間隔期間頒發。 |
> | DataAction | 微軟.密鑰庫/保管庫/機密/恢復/操作 | 將備份的機密及其所有版本還原到保管庫。 |
> | DataAction | 微軟.金鑰庫/保管庫/機密/讀取元數據/操作 | 列出或查看機密的屬性,但不列出其值。 |
> | DataAction | 微軟.密鑰庫/保管庫/機密/獲取機密/操作 | 獲取機密的價值。 |
> | DataAction | 微軟.金鑰庫/保管庫/機密/設置機密/操作 | 創建新機密。 |

## <a name="microsoftkusto"></a>Microsoft.Kusto

Azure 服務[:Azure 資料資源管理員](../data-explorer/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | 微軟.庫斯托/註冊/行動 | 訂閱註冊操作 |
> | 動作 | 微軟.庫斯托/註冊/行動 | 將訂閱註冊到庫斯托資源提供程式。 |
> | 動作 | 微軟.庫斯托/取消註冊/行動 | 取消註冊庫斯托資源提供程式的訂閱。 |
> |  | **叢集** |  |
> | 動作 | Microsoft.Kusto/Clusters/read | 讀取叢集資源。 |
> | 動作 | Microsoft.Kusto/Clusters/write | 寫入叢集資源。 |
> | 動作 | Microsoft.Kusto/Clusters/delete | 刪除叢集資源。 |
> | 動作 | 微軟.庫斯托/集群/開始/行動 | 啟動群集。 |
> | 動作 | 微軟.庫斯托/集群/停止/行動 | 停止群集。 |
> | 動作 | 微軟.庫斯托/集群/啟動/操作 | 啟動群集。 |
> | 動作 | 微軟.庫斯托/集群/停用/操作 | 停止群集。 |
> | 動作 | 微軟.庫斯托/集群/檢查名稱可用性/操作 | 檢查群集名稱可用性。 |
> | 動作 | 微軟.庫斯特托/集群/分離追蹤器資料庫/操作 | 分離跟隨器的資料庫。 |
> | 動作 | 微軟.庫斯特托/集群/清單關注者資料庫/操作 | 列出跟隨者的資料庫。 |
> | 動作 | 微軟.庫斯托/集群/診斷虛擬網路/操作 | 診斷服務所依賴的外部資源的網路連接狀態。 |
> | 動作 | 微軟.庫斯特/集群/列表語言擴展/操作 | 列出語言擴展。 |
> | 動作 | 微軟.庫斯托/集群/添加語言擴展/操作 | 添加語言副檔名。 |
> | 動作 | 微軟.庫斯托/集群/刪除語言擴展/操作 | 刪除語言副檔名。 |
> |  | **叢集/附加資料庫設定** |  |
> | 動作 | 微軟.庫斯托/集群/附加資料庫配置/讀取 | 讀取附加的資料庫配置資源。 |
> | 動作 | 微軟.庫斯托/集群/附加資料庫配置/寫入 | 寫入附加的資料庫配置資源。 |
> | 動作 | 微軟.庫斯托/集群/附加資料庫配置/刪除 | 刪除附加的資料庫配置資源。 |
> |  | **叢集/資料庫** |  |
> | 動作 | Microsoft.Kusto/Clusters/Databases/read | 讀取資料庫資源。 |
> | 動作 | Microsoft.Kusto/Clusters/Databases/write | 寫入資料庫資源。 |
> | 動作 | Microsoft.Kusto/Clusters/Databases/delete | 刪除資料庫資源。 |
> | 動作 | 微軟.庫斯特托/集群/資料庫/列表主體/操作 | 列出資料庫主體。 |
> | 動作 | 微軟.庫斯托/集群/資料庫/添加主體/操作 | 添加資料庫主體。 |
> | 動作 | 微軟.庫斯托/集群/資料庫/刪除主體/操作 | 刪除資料庫主體。 |
> | 動作 | 微軟.庫斯托/集群/資料庫/資料連接驗證/操作 | 驗證資料庫數據連接。 |
> | 動作 | 微軟.庫斯托/集群/資料庫/檢查名稱可用性/操作 | 檢查給定類型的名稱可用性。 |
> | 動作 | 微軟.庫斯托/集群/資料庫/事件Hub連接驗證/操作 | 驗證資料庫事件中心連接。 |
> |  | **叢集/資料庫/資料連線** |  |
> | 動作 | Microsoft.Kusto/Clusters/Databases/DataConnections/read | 讀取資料連線資源。 |
> | 動作 | Microsoft.Kusto/Clusters/Databases/DataConnections/write | 寫入數據連接資源。 |
> | 動作 | Microsoft.Kusto/Clusters/Databases/DataConnections/delete | 刪除資料連線資源。 |
> |  | **叢集/資料庫/事件連線** |  |
> | 動作 | Microsoft.Kusto/Clusters/Databases/EventHubConnections/read | 讀取事件中心連接資源。 |
> | 動作 | Microsoft.Kusto/Clusters/Databases/EventHubConnections/write | 寫入事件中心連接資源。 |
> | 動作 | Microsoft.Kusto/Clusters/Databases/EventHubConnections/delete | 刪除事件中心連接資源。 |
> |  | **叢集/資料庫/主體分配** |  |
> | 動作 | 微軟.庫斯托/集群/資料庫/主體分配/讀取 | 讀取資料庫主體分配資源。 |
> | 動作 | 微軟.庫斯托/集群/資料庫/主體分配/寫入 | 寫入資料庫主體分配資源。 |
> | 動作 | 微軟.庫斯托/集群/資料庫/主體分配/刪除 | 刪除資料庫主體分配資源。 |
> |  | **叢集/資料連線** |  |
> | 動作 | 微軟.庫斯托/集群/數據連接/讀取 | 讀取群集的數據連接資源。 |
> | 動作 | 微軟.庫斯托/集群/資料連接/寫入 | 寫入群集的數據連接資源。 |
> | 動作 | 微軟.庫斯托/集群/資料連接/刪除 | 刪除群集的資料連接資源。 |
> |  | **叢集/主體分配** |  |
> | 動作 | 微軟.庫斯托/集群/主體分配/讀取 | 讀取群集主體分配資源。 |
> | 動作 | 微軟.庫斯托/集群/主體分配/寫入 | 寫入群集主體分配資源。 |
> | 動作 | 微軟.庫斯托/集群/主體分配/刪除 | 刪除群集主體分配資源。 |
> |  | **叢集/SKU** |  |
> | 動作 | 微軟.庫斯托/集群/SKU/讀取 | 讀取群集 SKU 資源。 |
> |  | **位置** |  |
> | 動作 | 微軟.庫斯托/位置/檢查名稱可用性/操作 | 檢查資源名稱可用性。 |
> | 動作 | 微軟.庫斯托/位置/獲取網路策略/操作 | 取得網路意圖原則 |
> |  | **位置/動作結果** |  |
> | 動作 | Microsoft.Kusto/locations/operationresults/read | 讀取作業資源 |
> |  | **作業** |  |
> | 動作 | Microsoft.Kusto/Operations/read | 讀取作業資源 |
> |  | **SKU** |  |
> | 動作 | 微軟.庫什托/SKU/讀 | 讀取 SKU 資源。 |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

Azure 服務[:Azure 實驗室服務](../lab-services/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.LabServices/register/action | 註冊訂用帳戶 |
> |  | **實驗室會計** |  |
> | 動作 | Microsoft.LabServices/labAccounts/delete | 刪除實驗室帳戶。 |
> | 動作 | Microsoft.LabServices/labAccounts/read | 讀取實驗室帳戶。 |
> | 動作 | Microsoft.LabServices/labAccounts/write | 新增或修改實驗室帳戶。 |
> | 動作 | Microsoft.LabServices/labAccounts/CreateLab/action | 在實驗室帳戶中建立實驗室。 |
> | 動作 | Microsoft.LabServices/labAccounts/GetRegionalAvailability/action | 取得在實驗室帳戶下設定的每個大小類別的區域可用性資訊 |
> | 動作 | 微軟.實驗室服務/實驗室帳戶/獲取定價和可用性/操作 | 獲取實驗室帳戶的大小、地理位置和作業系統組合的定價和可用性。 |
> | 動作 | 微軟.實驗室服務/實驗室帳戶/獲取限制和使用/操作 | 取得此訂閱的核心限制與使用方式 |
> |  | **實驗室帳戶/函式庫影像** |  |
> | 動作 | Microsoft.LabServices/labAccounts/galleryImages/delete | 刪除資源庫映像。 |
> | 動作 | Microsoft.LabServices/labAccounts/galleryImages/read | 讀取資源庫映像。 |
> | 動作 | Microsoft.LabServices/labAccounts/galleryImages/write | 新增或修改資源庫映像。 |
> |  | **實驗室帳戶/實驗室** |  |
> | 動作 | Microsoft.LabServices/labAccounts/labs/delete | 刪除實驗室。 |
> | 動作 | Microsoft.LabServices/labAccounts/labs/read | 讀取實驗室。 |
> | 動作 | Microsoft.LabServices/labAccounts/labs/write | 新增或修改實驗室。 |
> | 動作 | Microsoft.LabServices/labAccounts/labs/AddUsers/action | 將使用者新增至實驗室 |
> | 動作 | Microsoft.LabServices/labAccounts/labs/SendEmail/action | 傳送附有註冊連結的電子郵件給實驗室 |
> | 動作 | 微軟.實驗室服務/實驗室帳戶/實驗室/獲取實驗室定價和可用性/操作 | 獲取此實驗室每個實驗室單元的定價以及指示此實驗室是否可以擴展的可用性。 |
> |  | **實驗室帳戶/實驗室/環境設定** |  |
> | 動作 | Microsoft.LabServices/labAccounts/labs/environmentSettings/delete | 刪除環境設定。 |
> | 動作 | Microsoft.LabServices/labAccounts/labs/environmentSettings/read | 讀取環境設定。 |
> | 動作 | Microsoft.LabServices/labAccounts/labs/environmentSettings/write | 新增或修改環境設定。 |
> | 動作 | Microsoft.LabServices/labAccounts/labs/environmentSettings/Publish/action | 根據實驗室/環境設定的目前狀態，佈建/取消佈建環境設定所需的資源。 |
> | 動作 | Microsoft.LabServices/labAccounts/labs/environmentSettings/Start/action | 啟動範本內的所有資源以啟動範本。 |
> | 動作 | Microsoft.LabServices/labAccounts/labs/environmentSettings/Stop/action | 透過停止範本中的所有資源來停止範本。 |
> | 動作 | 微軟.實驗室服務/實驗室帳戶/實驗室/環境設置/保存圖像/操作 | 將目前樣本映像儲存到實驗室帳戶中的分享函式庫 |
> | 動作 | 微軟.實驗室服務/實驗室帳戶/實驗室/環境設置/重置密碼/操作 | 重置範本虛擬機器上的密碼。 |
> |  | **實驗室帳戶/實驗室/環境設置/環境** |  |
> | 動作 | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/delete | 刪除環境。 |
> | 動作 | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/read | 讀取環境。 |
> | 動作 | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Start/action | 啟動環境內的所有資源以啟動環境。 |
> | 動作 | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Stop/action | 停止環境內的所有資源以停止環境 |
> | 動作 | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/ResetPassword/action | 重設環境的使用者密碼 |
> |  | **實驗室帳戶/實驗室/環境設置/計劃** |  |
> | 動作 | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/delete | 刪除排程。 |
> | 動作 | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/read | 讀取排程。 |
> | 動作 | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/write | 新增或修改排程。 |
> |  | **實驗室帳戶/實驗室/使用者** |  |
> | 動作 | Microsoft.LabServices/labAccounts/labs/users/delete | 刪除使用者。 |
> | 動作 | Microsoft.LabServices/labAccounts/labs/users/read | 讀取使用者。 |
> | 動作 | Microsoft.LabServices/labAccounts/labs/users/write | 新增或修改使用者。 |
> |  | **實驗室帳戶/共用庫** |  |
> | 動作 | Microsoft.LabServices/labAccounts/sharedGalleries/delete | 刪除共用資源庫。 |
> | 動作 | Microsoft.LabServices/labAccounts/sharedGalleries/read | 讀取共用資源庫。 |
> | 動作 | Microsoft.LabServices/labAccounts/sharedGalleries/write | 新增或修改共用資源庫。 |
> |  | **實驗室帳戶/共用影像** |  |
> | 動作 | Microsoft.LabServices/labAccounts/sharedImages/delete | 刪除共用影像。 |
> | 動作 | Microsoft.LabServices/labAccounts/sharedImages/read | 讀取共用影像。 |
> | 動作 | Microsoft.LabServices/labAccounts/sharedImages/write | 新增或修改共用影像。 |
> |  | **位置/操作** |  |
> | 動作 | Microsoft.LabServices/locations/operations/read | 讀取作業。 |
> |  | **使用者** |  |
> | 動作 | Microsoft.LabServices/users/Register/action | 向受控實驗室註冊使用者 |
> | 動作 | 微軟.實驗室服務/使用者/清單所有環境/操作 | 列出使用者的所有環境 |
> | 動作 | Microsoft.LabServices/users/StartEnvironment/action | 啟動環境內的所有資源以啟動環境。 |
> | 動作 | Microsoft.LabServices/users/StopEnvironment/action | 停止環境內的所有資源以停止環境 |
> | 動作 | Microsoft.LabServices/users/ResetPassword/action | 重設環境的使用者密碼 |
> | 動作 | 微軟.實驗室服務/使用者/使用者設置/操作 | 更新並返回個人用戶設置。 |

## <a name="microsoftlogic"></a>Microsoft.Logic

Azure 服務:[邏輯應用](../logic-apps/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.Logic/register/action | 為指定的訂用帳戶註冊 Microsoft.Logic 資源提供者。 |
> |  | **集成帳戶** |  |
> | 動作 | Microsoft.Logic/integrationAccounts/read | 讀取整合帳戶。 |
> | 動作 | Microsoft.Logic/integrationAccounts/write | 建立或更新整合帳戶。 |
> | 動作 | Microsoft.Logic/integrationAccounts/delete | 刪除整合帳戶。 |
> | 動作 | Microsoft.Logic/integrationAccounts/regenerateAccessKey/action | 重新產生存取金鑰祕密。 |
> | 動作 | Microsoft.Logic/integrationAccounts/listCallbackUrl/action | 取得整合帳戶的回呼 URL。 |
> | 動作 | Microsoft.Logic/integrationAccounts/listKeyVaultKeys/action | 取得金鑰保存庫中的金鑰。 |
> | 動作 | Microsoft.Logic/integrationAccounts/logTrackingEvents/action | 記錄整合帳戶中的追蹤事件。 |
> | 動作 | Microsoft.Logic/integrationAccounts/join/action | 加入整合帳戶。 |
> |  | **整合帳號/協定** |  |
> | 動作 | Microsoft.Logic/integrationAccounts/agreements/read | 讀取整合帳戶中的合約。 |
> | 動作 | Microsoft.Logic/integrationAccounts/agreements/write | 建立或更新整合帳戶中的合約。 |
> | 動作 | Microsoft.Logic/integrationAccounts/agreements/delete | 刪除整合帳戶中的合約。 |
> | 動作 | Microsoft.Logic/integrationAccounts/agreements/listContentCallbackUrl/action | 取得整合帳戶中合約內容的回呼 URL。 |
> |  | **整合帳號/程式集** |  |
> | 動作 | Microsoft.Logic/integrationAccounts/assemblies/read | 讀取整合帳戶中的組件。 |
> | 動作 | Microsoft.Logic/integrationAccounts/assemblies/write | 建立或更新整合帳戶中的組件。 |
> | 動作 | Microsoft.Logic/integrationAccounts/assemblies/delete | 刪除整合帳戶中的組件。 |
> | 動作 | Microsoft.Logic/integrationAccounts/assemblies/listContentCallbackUrl/action | 取得整合帳戶中組件內容的回呼 URL。 |
> |  | **整合帳號/批次處理設定** |  |
> | 動作 | Microsoft.Logic/integrationAccounts/batchConfigurations/read | 讀取整合帳戶中的批次設定。 |
> | 動作 | Microsoft.Logic/integrationAccounts/batchConfigurations/write | 建立或更新整合帳戶中的批次設定。 |
> | 動作 | Microsoft.Logic/integrationAccounts/batchConfigurations/delete | 刪除整合帳戶中的批次設定。 |
> |  | **整合帳號/憑證** |  |
> | 動作 | Microsoft.Logic/integrationAccounts/certificates/read | 讀取整合帳戶中的憑證。 |
> | 動作 | Microsoft.Logic/integrationAccounts/certificates/write | 建立或更新整合帳戶中的憑證。 |
> | 動作 | Microsoft.Logic/integrationAccounts/certificates/delete | 刪除整合帳戶中的憑證。 |
> |  | **整合帳號/群組** |  |
> | 動作 | 微軟.邏輯/整合帳戶/組/讀取 | 在集成帳戶中讀取組。 |
> | 動作 | 微軟.邏輯/整合帳戶/組/寫入 | 在整合帳戶中建立或更新組。 |
> | 動作 | 微軟.邏輯/整合帳戶/群組/刪除 | 刪除整合帳號中的群組。 |
> |  | **整合帳號/地圖** |  |
> | 動作 | Microsoft.Logic/integrationAccounts/maps/read | 讀取整合帳戶中的對應。 |
> | 動作 | Microsoft.Logic/integrationAccounts/maps/write | 建立或更新整合帳戶中的對應。 |
> | 動作 | Microsoft.Logic/integrationAccounts/maps/delete | 刪除整合帳戶中的對應。 |
> | 動作 | Microsoft.Logic/integrationAccounts/maps/listContentCallbackUrl/action | 取得整合帳戶中對應內容的回呼 URL。 |
> |  | **整合帳號/合作夥伴** |  |
> | 動作 | Microsoft.Logic/integrationAccounts/partners/read | 讀取整合帳戶中的合作夥伴。 |
> | 動作 | Microsoft.Logic/integrationAccounts/partners/write | 建立或更新整合帳戶中的合作夥伴。 |
> | 動作 | Microsoft.Logic/integrationAccounts/partners/delete | 刪除整合帳戶中的合作夥伴。 |
> | 動作 | Microsoft.Logic/integrationAccounts/partners/listContentCallbackUrl/action | 取得整合帳戶中合作夥伴內容的回呼 URL。 |
> |  | **整合帳戶/供應商/微軟.見解/日誌定義** |  |
> | 動作 | Microsoft.Logic/integrationAccounts/providers/Microsoft.Insights/logDefinitions/read | 讀取整合帳戶記錄定義。 |
> |  | **整合帳號/羅塞塔網路流程配置** |  |
> | 動作 | 微軟.邏輯/集成帳戶/羅塞塔網路流程配置/讀取 | 在集成帳戶中讀取羅塞塔網流程配置。 |
> | 動作 | 微軟.邏輯/整合帳戶/羅塞塔網路流程配置/寫入 | 在整合帳戶中創建或更新羅塞塔網流程配置。 |
> | 動作 | 微軟.邏輯/整合帳戶/羅塞塔NetProcess配置/刪除 | 刪除集成帳戶中的羅塞塔網流程配置。 |
> |  | **整合帳號/計劃** |  |
> | 動作 | 微軟.邏輯/整合帳戶/計畫/讀取 | 在集成帳戶中讀取計劃。 |
> | 動作 | 微軟.邏輯/整合帳戶/計劃/寫入 | 在整合帳戶中創建或更新計畫。 |
> | 動作 | 微軟.邏輯/整合帳戶/計劃/刪除 | 刪除集成帳戶中的計劃。 |
> |  | **整合帳號/架構** |  |
> | 動作 | Microsoft.Logic/integrationAccounts/schemas/read | 讀取整合帳戶中的結構描述。 |
> | 動作 | Microsoft.Logic/integrationAccounts/schemas/write | 建立或更新整合帳戶中的結構描述。 |
> | 動作 | Microsoft.Logic/integrationAccounts/schemas/delete | 刪除整合帳戶中的結構描述。 |
> | 動作 | Microsoft.Logic/integrationAccounts/schemas/listContentCallbackUrl/action | 取得整合帳戶中結構描述內容的回呼 URL。 |
> |  | **集成帳戶/工作階段** |  |
> | 動作 | Microsoft.Logic/integrationAccounts/sessions/read | 在集成帳戶中讀取會話。 |
> | 動作 | Microsoft.Logic/integrationAccounts/sessions/write | 建立或更新整合帳戶中的工作階段。 |
> | 動作 | Microsoft.Logic/integrationAccounts/sessions/delete | 刪除整合帳戶中的工作階段。 |
> |  | **整合服務環境** |  |
> | 動作 | Microsoft.Logic/integrationServiceEnvironments/read | 讀取整合服務環境。 |
> | 動作 | Microsoft.Logic/integrationServiceEnvironments/write | 建立或更新整合服務環境。 |
> | 動作 | Microsoft.Logic/integrationServiceEnvironments/delete | 刪除整合服務環境。 |
> | 動作 | Microsoft.Logic/integrationServiceEnvironments/join/action | 聯結整合服務環境。 |
> |  | **整合服務環境/可用託管 Apis** |  |
> | 動作 | 微軟.邏輯/整合服務環境/可用託管Apis/讀取 | 讀取可用的託管 API 的整合服務環境。 |
> |  | **整合服務環境/託管 Apis** |  |
> | 動作 | Microsoft.Logic/integrationServiceEnvironments/managedApis/read | 讀取整合服務環境受控 API。 |
> | 動作 | 微軟.邏輯/整合服務環境/託管Apis/寫 | 創建或更新整合服務環境託管 API。 |
> | 動作 | 微軟.邏輯/整合服務環境/託管Apis/聯接/操作 | 加入整合服務環境託管 API。 |
> |  | **整合服務環境/託管 Apis/api 操作** |  |
> | 動作 | Microsoft.Logic/integrationServiceEnvironments/managedApis/apiOperations/read | 讀取整合服務環境受控 API 作業。 |
> |  | **整合服務環境/託管 Apis/操作狀態** |  |
> | 動作 | 微軟.邏輯/整合服務環境/託管Apis/操作狀態/讀取 | 讀取整合服務環境託管 API 操作狀態。 |
> |  | **整合服務環境/操作狀態** |  |
> | 動作 | Microsoft.Logic/integrationServiceEnvironments/operationStatuses/read | 讀取整合服務環境作業狀態。 |
> |  | **整合服務環境/供應商/微軟.見解/指標定義** |  |
> | 動作 | Microsoft.Logic/integrationServiceEnvironments/providers/Microsoft.Insights/metricDefinitions/read | 讀取整合服務環境計量定義。 |
> |  | **位置/工作流** |  |
> | 動作 | Microsoft.Logic/locations/workflows/validate/action | 驗證工作流程。 |
> | 動作 | Microsoft.Logic/locations/workflows/recommendOperationGroups/action | 取得工作流程建議的作業群組。 |
> |  | **操作** |  |
> | 動作 | Microsoft.Logic/operations/read | 取得作業。 |
> |  | **workflows** |  |
> | 動作 | Microsoft.Logic/workflows/read | 讀取工作流程。 |
> | 動作 | Microsoft.Logic/workflows/write | 建立或更新工作流程。 |
> | 動作 | Microsoft.Logic/workflows/delete | 刪除工作流程。 |
> | 動作 | Microsoft.Logic/workflows/run/action | 啟動工作流程的執行。 |
> | 動作 | Microsoft.Logic/workflows/disable/action | 停用工作流程。 |
> | 動作 | Microsoft.Logic/workflows/enable/action | 啟用工作流程。 |
> | 動作 | Microsoft.Logic/workflows/suspend/action | 暫止工作流程。 |
> | 動作 | Microsoft.Logic/workflows/validate/action | 驗證工作流程。 |
> | 動作 | Microsoft.Logic/workflows/move/action | 將工作流程從其現有的訂用帳戶識別碼、資源群組和/或名稱改為不同的訂用帳戶識別碼、資源群組和/或名稱。 |
> | 動作 | Microsoft.Logic/workflows/listSwagger/action | 取得工作流程 Swagger 定義。 |
> | 動作 | Microsoft.Logic/workflows/regenerateAccessKey/action | 重新產生存取金鑰祕密。 |
> | 動作 | Microsoft.Logic/workflows/listCallbackUrl/action | 取得工作流程的回呼 URL。 |
> |  | **工作流/存取金鑰** |  |
> | 動作 | Microsoft.Logic/workflows/accessKeys/read | 讀取存取金鑰。 |
> | 動作 | Microsoft.Logic/workflows/accessKeys/write | 建立或更新存取金鑰。 |
> | 動作 | Microsoft.Logic/workflows/accessKeys/delete | 刪除存取金鑰。 |
> | 動作 | Microsoft.Logic/workflows/accessKeys/list/action | 列出存取金鑰祕密。 |
> | 動作 | Microsoft.Logic/workflows/accessKeys/regenerate/action | 重新產生存取金鑰祕密。 |
> |  | **工作流/探測器** |  |
> | 動作 | 微軟.邏輯/工作流/探測器/讀取 | 讀取工作流檢測器。 |
> |  | **工作流/供應商/微軟.見解/診斷設置** |  |
> | 動作 | Microsoft.Logic/workflows/providers/Microsoft.Insights/diagnosticSettings/read | 讀取工作流程診斷設定。 |
> | 動作 | Microsoft.Logic/workflows/providers/Microsoft.Insights/diagnosticSettings/write | 建立或更新工作流程診斷設定。 |
> |  | **工作流/提供程式/微軟.見解/日誌定義** |  |
> | 動作 | Microsoft.Logic/workflows/providers/Microsoft.Insights/logDefinitions/read | 讀取工作流程記錄定義。 |
> |  | **工作流/供應商/微軟.見解/指標定義** |  |
> | 動作 | Microsoft.Logic/workflows/providers/Microsoft.Insights/metricDefinitions/read | 讀取工作流程計量定義。 |
> |  | **工作流/執行** |  |
> | 動作 | Microsoft.Logic/workflows/runs/read | 讀取工作流程的執行。 |
> | 動作 | 微軟.邏輯/工作流/運行/刪除 | 刪除工作流的運行。 |
> | 動作 | Microsoft.Logic/workflows/runs/cancel/action | 取消工作流程的執行。 |
> |  | **工作流/執行/操作** |  |
> | 動作 | Microsoft.Logic/workflows/runs/actions/read | 讀取工作流程的執行動作。 |
> | 動作 | Microsoft.Logic/workflows/runs/actions/listExpressionTraces/action | 取得工作流程執行動作運算式追蹤。 |
> |  | **工作流/執行/操作/重複** |  |
> | 動作 | Microsoft.Logic/workflows/runs/actions/repetitions/read | 讀取工作流程執行動作重複作業。 |
> | 動作 | Microsoft.Logic/workflows/runs/actions/repetitions/listExpressionTraces/action | 取得工作流程執行動作重複作業運算式追蹤。 |
> |  | **工作流/執行/操作/重複/請求歷史記錄** |  |
> | 動作 | Microsoft.Logic/workflows/runs/actions/repetitions/requestHistories/read | 讀取工作流程執行重複動作要求歷程記錄。 |
> |  | **工作流/執行/操作/請求歷史記錄** |  |
> | 動作 | Microsoft.Logic/workflows/runs/actions/requestHistories/read | 讀取工作流程執行動作要求歷程記錄。 |
> |  | **工作流/執行/操作/範圍重複** |  |
> | 動作 | Microsoft.Logic/workflows/runs/actions/scoperepetitions/read | 讀取工作流程執行動作範圍重複作業。 |
> |  | **工作流/執行/操作** |  |
> | 動作 | Microsoft.Logic/workflows/runs/operations/read | 讀取工作流程的執行作業狀態。 |
> |  | **工作流/觸發器** |  |
> | 動作 | Microsoft.Logic/workflows/triggers/read | 讀取觸發程序。 |
> | 動作 | Microsoft.Logic/workflows/triggers/run/action | 執行觸發程序。 |
> | 動作 | Microsoft.Logic/workflows/triggers/reset/action | 重設觸發程序。 |
> | 動作 | Microsoft.Logic/workflows/triggers/setState/action | 設定觸發程序狀態。 |
> | 動作 | Microsoft.Logic/workflows/triggers/listCallbackUrl/action | 取得觸發程序的回呼 URL。 |
> |  | **工作流/觸發器/歷史記錄** |  |
> | 動作 | Microsoft.Logic/workflows/triggers/histories/read | 讀取觸發程序歷程記錄。 |
> | 動作 | Microsoft.Logic/workflows/triggers/histories/resubmit/action | 重新提交工作流程的觸發程序。 |
> |  | **工作流/版本** |  |
> | 動作 | Microsoft.Logic/workflows/versions/read | 讀取工作流程版本。 |
> |  | **工作流/版本/觸發器** |  |
> | 動作 | Microsoft.Logic/workflows/versions/triggers/listCallbackUrl/action | 取得觸發程序的回呼 URL。 |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

Azure 服務:[機器學習工作室](../machine-learning/studio/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.MachineLearning/register/action | 針對 Machine Learning Web 服務資源提供者註冊訂用帳戶，並讓您能夠建立 Web 服務。 |
> | 動作 | Microsoft.MachineLearning/webServices/action | 建立所支援區域的區域性 Web 服務屬性 |
> |  | **承諾計劃** |  |
> | 動作 | Microsoft.MachineLearning/commitmentPlans/read | 讀取任何 Machine Learning 承諾用量方案 |
> | 動作 | Microsoft.MachineLearning/commitmentPlans/write | 建立或更新任何 Machine Learning 承諾用量方案 |
> | 動作 | Microsoft.MachineLearning/commitmentPlans/delete | 刪除任何 Machine Learning 承諾用量方案 |
> | 動作 | Microsoft.MachineLearning/commitmentPlans/join/action | 加入任何 Machine Learning 承諾用量方案 |
> |  | **承諾計劃/承諾協會** |  |
> | 動作 | Microsoft.MachineLearning/commitmentPlans/commitmentAssociations/read | 讀取任何 Machine Learning 承諾用量方案關聯 |
> | 動作 | Microsoft.MachineLearning/commitmentPlans/commitmentAssociations/move/action | 移動任何 Machine Learning 承諾用量方案關聯 |
> |  | **位置/動作結果** |  |
> | 動作 | Microsoft.MachineLearning/locations/operationresults/read | 取得 Machine Learning 作業的結果 |
> |  | **位置/動作狀態** |  |
> | 動作 | Microsoft.MachineLearning/locations/operationsstatus/read | 取得執行中 Machine Learning 作業的狀態 |
> |  | **操作** |  |
> | 動作 | Microsoft.MachineLearning/operations/read | 取得 Machine Learning 作業 |
> |  | **斯克庫斯** |  |
> | 動作 | Microsoft.MachineLearning/skus/read | 取得 Machine Learning 承諾用量方案 SKU |
> |  | **網路服務** |  |
> | 動作 | Microsoft.MachineLearning/webServices/read | 讀取任何 Machine Learning Web 服務 |
> | 動作 | Microsoft.MachineLearning/webServices/write | 建立或更新任何 Machine Learning Web 服務 |
> | 動作 | Microsoft.MachineLearning/webServices/delete | 刪除任何 Machine Learning Web 服務 |
> |  | **Web服務/清單鍵** |  |
> | 動作 | Microsoft.MachineLearning/webServices/listkeys/read | 取得 Machine Learning Web 服務的金鑰 |
> |  | **工作區** |  |
> | 動作 | Microsoft.MachineLearning/Workspaces/read | 讀取任何 Machine Learning 工作區 |
> | 動作 | Microsoft.MachineLearning/Workspaces/write | 建立或更新任何 Machine Learning 工作區 |
> | 動作 | Microsoft.MachineLearning/Workspaces/delete | 刪除任何 Machine Learning 工作區 |
> | 動作 | Microsoft.MachineLearning/Workspaces/listworkspacekeys/action | 列出 Machine Learning 工作區的金鑰 |
> | 動作 | Microsoft.MachineLearning/Workspaces/resyncstoragekeys/action | 重新同步處理針對 Machine Learning 工作區所設定之儲存體帳戶的金鑰 |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

Azure 服務:[機器學習服務](../machine-learning/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.MachineLearningServices/register/action | 為機器學習服務資源提供者註冊訂用帳戶 |
> |  | **位置** |  |
> | 動作 | 微軟.機器學習服務/位置/更新配額/操作 | 更新工作區中每個 VM 系列的配額。 |
> |  | **位置/計算操作狀態** |  |
> | 動作 | 微軟.機器學習服務/位置/計算操作狀態/讀取 | 取得特定計算作業的狀態 |
> |  | **位置/配額** |  |
> | 動作 | 微軟.機器學習服務/位置/配額/讀取 | 根據 VM 系列獲取當前分配的工作區配額。 |
> |  | **位置/用法** |  |
> | 動作 | Microsoft.MachineLearningServices/locations/usages/read | 訂用帳戶中的 aml 計算資源的使用量報告 |
> |  | **位置/vmsizes** |  |
> | 動作 | Microsoft.MachineLearningServices/locations/vmsizes/read | 取得支援的 VM 大小 |
> |  | **位置/工作區操作狀態** |  |
> | 動作 | Microsoft.MachineLearningServices/locations/workspaceOperationsStatus/read | 取得特定工作區作業的狀態 |
> |  | **workspaces** |  |
> | 動作 | Microsoft.MachineLearningServices/workspaces/read | 取得機器學習服務工作區 |
> | 動作 | Microsoft.MachineLearningServices/workspaces/write | 建立或更新機器學習服務工作區 |
> | 動作 | Microsoft.MachineLearningServices/workspaces/delete | 刪除機器學習服務工作區 |
> | 動作 | Microsoft.MachineLearningServices/workspaces/listKeys/action | 列出機器學習服務工作區的祕密 |
> | 動作 | 微軟.機器學習服務/工作區/私有端點連接審批/操作 | 批准或拒絕與 Microsoft.網路供應商的專用終結點資源的連接 |
> |  | **workspaces/computes** |  |
> | 動作 | Microsoft.MachineLearningServices/workspaces/computes/read | 取得機器學習服務工作區中的計算資源 |
> | 動作 | Microsoft.MachineLearningServices/workspaces/computes/write | 在機器學習服務工作區中建立或更新計算資源 |
> | 動作 | Microsoft.MachineLearningServices/workspaces/computes/delete | 刪除機器學習服務工作區中的計算資源 |
> | 動作 | Microsoft.MachineLearningServices/workspaces/computes/listKeys/action | 列出機器學習服務工作區中的計算資源祕密 |
> | 動作 | Microsoft.MachineLearningServices/workspaces/computes/listNodes/action | 列出機器學習服務工作區中的計算資源節點 |
> | 動作 | 微軟.機器學習服務/工作區/計算/啟動/操作 | 在機器學習服務工作區中啟動計算資源 |
> | 動作 | 微軟.機器學習服務/工作空間/計算/停止/操作 | 停止機器學習服務工作區中的計算資源 |
> | 動作 | 微軟.機器學習服務/工作區/計算/重新啟動/操作 | 重新啟動機器學習服務工作區中的計算資源 |
> |  | **工作區/事件網格篩選器** |  |
> | 動作 | 微軟.機器學習服務/工作區/事件網格篩選器/讀取 | 取得特定工作區的事件網格篩選器 |
> |  | **工作區/功能** |  |
> | 動作 | 微軟.機器學習服務/工作空間/功能/閱讀 | 取得機器學習服務工作區的所有已啟用功能 |
> |  | **工作區/專用端接連線 Proxies** |  |
> | 動作 | 微軟.機器學習服務/工作空間/私有端點連接Proxies/讀取 | 查看連線到 Microsoft.網路提供者專用終結點資源的狀態 |
> | 動作 | 微軟.機器學習服務/工作空間/私有端點連接Proxies/寫入 | 將連線代理的狀態更改為 Microsoft.Network 提供者的專用終結點資源 |
> | 動作 | 微軟.機器學習服務/工作空間/私人端接連接Proxies/刪除 | 移除與 Microsoft.網路提供者的專用終結點資源的連線代理 |
> | 動作 | 微軟.機器學習服務/工作空間/專用端接連接Proxies/驗證/操作 | 認證與 Microsoft.網路提供者的專用終結點資源的連線代理 |
> |  | **工作區/專用端點連接** |  |
> | 動作 | 微軟.機器學習服務/工作區/專用端點連接/讀取 | 查看連線到 Microsoft.網路提供者的專用終結點資源的狀態 |
> | 動作 | 微軟.機器學習服務/工作區/私有端點連接/寫入 | 變更連線到 Microsoft.網路提供者的專用終結點資源的狀態 |
> | 動作 | 微軟.機器學習服務/工作區/專用端點連接/刪除 | 移除與 Microsoft.網路提供者的專用終結點資源的連線 |
> |  | **工作區/私有連結資源** |  |
> | 動作 | 微軟.機器學習服務/工作空間/私有鏈接資源/讀取 | 取得機器學習服務工作區指定實體可用專用連結資源 |
> |  | **工作空間/資料漂移探測器** |  |
> | DataAction | 微軟.機器學習服務/工作空間/數據漂移探測器/讀取 | 在機器學習服務工作區中取得資料漂移檢測器 |
> | DataAction | 微軟.機器學習服務/工作空間/數據漂移探測器/寫入 | 在機器學習服務工作區中建立或更新資料漂移檢測器 |
> |  | **工作區/資料集/已註冊** |  |
> | DataAction | 微軟.機器學習服務/工作空間/數據集/註冊/讀取 | 在機器學習服務工作區中獲取已註冊的數據集 |
> | DataAction | 微軟.機器學習服務/工作空間/數據集/註冊/寫入 | 在機器學習服務工作區中建立或更新已註冊的資料集 |
> | DataAction | 微軟.機器學習服務/工作空間/數據集/註冊/刪除 | 移除機器學習服務工作區中的已註冊資料集 |
> |  | **工作區/資料集/註冊/預覽** |  |
> | DataAction | 微軟.機器學習服務/工作空間/數據集/註冊/預覽/讀取 | 取得機器學習服務工作區中已註冊資料集的資料集預覽 |
> |  | **工作區/資料集/註冊/設定檔** |  |
> | DataAction | 微軟.機器學習服務/工作空間/數據集/註冊/配置檔/讀取 | 取得機器學習服務工作區中已註冊資料集的資料集設定檔 |
> | DataAction | 微軟.機器學習服務/工作空間/數據集/註冊/配置檔/寫入 | 在機器學習服務工作區中建立或更新已註冊資料集的資料集設定檔 |
> |  | **工作區/資料集/註冊/架構** |  |
> | DataAction | 微軟.機器學習服務/工作空間/數據集/註冊/架構/讀取 | 取得機器學習服務工作區中已註冊資料集的資料集架構 |
> |  | **工作區/資料集/未註冊** |  |
> | DataAction | 微軟.機器學習服務/工作空間/數據集/未註冊/讀取 | 在機器學習服務工作區中獲取未註冊的數據集 |
> | DataAction | 微軟.機器學習服務/工作空間/數據集/未註冊/寫入 | 在機器學習服務工作區中建立或更新未註冊的資料集 |
> | DataAction | 微軟.機器學習服務/工作區/數據集/未註冊/刪除 | 刪除機器學習服務工作區中未註冊的資料集 |
> |  | **工作區/資料集/未註冊/預覽** |  |
> | DataAction | 微軟.機器學習服務/工作空間/數據集/未註冊/預覽/讀取 | 取得機器學習服務工作區中未註冊資料集的資料集預覽 |
> |  | **工作區/資料集/未註冊/設定檔** |  |
> | DataAction | 微軟.機器學習服務/工作空間/數據集/未註冊/配置檔/讀取 | 取得機器學習服務工作區中未註冊資料集的資料集設定檔 |
> | DataAction | 微軟.機器學習服務/工作空間/數據集/未註冊/配置檔/寫入 | 在機器學習服務工作區中為未註冊的資料集建立或更新資料集設定檔 |
> |  | **工作區/資料集/未註冊/架構** |  |
> | DataAction | 微軟.機器學習服務/工作空間/數據集/未註冊/架構/讀取 | 取得機器學習服務工作區中未註冊資料集的資料集架構 |
> |  | **工作區/資料儲存** |  |
> | DataAction | 微軟.機器學習服務/工作空間/數據存儲/讀取 | 取得機器學習服務工作區中的資料儲存 |
> | DataAction | 微軟.機器學習服務/工作空間/數據存儲/寫入 | 在機器學習服務工作區中建立或更新資料儲存 |
> | DataAction | 微軟.機器學習服務/工作區/資料存儲/刪除 | 移除機器學習服務工作區中的資料儲存 |
> |  | **工作區/終結點/管道** |  |
> | DataAction | 微軟.機器學習服務/工作區/終結點/管道/讀取 | 在機器學習服務工作區中獲取已發布的管道和管道終結點 |
> | DataAction | 微軟.機器學習服務/工作區/終結點/管道/寫入 | 在機器學習服務工作區中建立或更新已發布的管道和管道終結點 |
> |  | **工作區/環境** |  |
> | DataAction | 微軟.機器學習服務/工作空間/環境/讀取 | 取得機器學習服務工作區中的環境 |
> | DataAction | 微軟.機器學習服務/工作空間/環境/讀取秘密/操作 | 取得機器學習服務工作區中具有機密的環境 |
> | DataAction | 微軟.機器學習服務/工作空間/環境/寫入 | 在機器學習服務工作區中建立或更新環境 |
> | DataAction | 微軟.機器學習服務/工作空間/環境/構建/操作 | 在機器學習服務工作區中建構環境 |
> |  | **workspaces/experiments** |  |
> | DataAction | 微軟.機器學習服務/工作空間/實驗/閱讀 | 取得機器學習服務工作區中的實驗 |
> | DataAction | 微軟.機器學習服務/工作空間/實驗/寫入 | 在機器學習服務工作區中建立或更新實驗 |
> | DataAction | 微軟.機器學習服務/工作空間/實驗/刪除 | 移除機器學習服務工作區中的實驗 |
> |  | **工作區/實驗/執行** |  |
> | DataAction | 微軟.機器學習服務/工作空間/實驗/運行/讀取 | 取得機器學習服務工作區執行 |
> | DataAction | 微軟.機器學習服務/工作空間/實驗/運行/寫入 | 在機器學習服務工作區中建立或更新 |
> |  | **工作區/實驗/執行/文稿執行** |  |
> | DataAction | 微軟.機器學習服務/工作空間/實驗/運行/腳本運行/提交/操作 | 在機器學習服務工作區中建立或更新文稿執行 |
> |  | **工作區/標籤** |  |
> | DataAction | 微軟.機器學習服務/工作空間/標籤/匯出/操作 | 機器學習服務工作區中分頁項目的匯出標籤 |
> |  | **工作區/標籤/標籤** |  |
> | DataAction | 微軟.機器學習服務/工作空間/標籤/標籤/讀取 | 取得機器學習服務工作區中標記項目的標籤 |
> | DataAction | 微軟.機器學習服務/工作空間/標籤/標籤/寫入 | 在機器學習服務工作區中建立標記項目的標籤 |
> | DataAction | 微軟.機器學習服務/工作空間/標籤/標籤/拒絕/操作 | 拒絕機器學習服務工作區中標記項目的標籤 |
> |  | **工作區/標籤/專案** |  |
> | DataAction | 微軟.機器學習服務/工作空間/標籤/專案/讀取 | 取得機器學習服務工作區中的分頁項目 |
> | DataAction | 微軟.機器學習服務/工作空間/標籤/專案/寫入 | 在機器學習服務工作區中建立或更新標記專案 |
> | DataAction | 微軟.機器學習服務/工作空間/標籤/專案/刪除 | 移除機器學習服務工作區中的分頁項目 |
> |  | **工作區/標籤/專案/摘要** |  |
> | DataAction | 微軟.機器學習服務/工作空間/標籤/專案/摘要/閱讀 | 取得機器學習服務工作區中的標籤專案摘要 |
> |  | **工作區/中繼資料/專案** |  |
> | DataAction | 微軟.機器學習服務/工作空間/元數據/專案/讀取 | 取得機器學習服務工作區中的工件 |
> | DataAction | 微軟.機器學習服務/工作空間/元數據/專案/寫入 | 在機器學習服務工作區中建立或更新專案 |
> | DataAction | 微軟.機器學習服務/工作區/元數據/專案/刪除 | 移除機器學習服務工作區中的項目 |
> |  | **工作區/中繼資料/機密** |  |
> | DataAction | 微軟.機器學習服務/工作空間/元數據/機密/讀取 | 取得機器學習服務工作區中的秘密 |
> | DataAction | 微軟.機器學習服務/工作空間/元數據/機密/寫入 | 在機器學習服務工作區中建立或更新機密 |
> | DataAction | 微軟.機器學習服務/工作區/元數據/機密/刪除 | 移除機器學習服務工作區中的秘密 |
> |  | **工作區/中繼資料/快照** |  |
> | DataAction | 微軟.機器學習服務/工作區/元數據/快照/讀取 | 抓取機器學習服務工作區中的快照 |
> | DataAction | 微軟.機器學習服務/工作區/元數據/快照/寫入 | 在機器學習服務工作區中建立或更新快照 |
> | DataAction | 微軟.機器學習服務/工作區/元數據/快照/刪除 | 移除機器學習服務工作區中的快照 |
> |  | **工作區/模型** |  |
> | DataAction | 微軟.機器學習服務/工作空間/模型/讀取 | 取得機器學習服務工作區中的模型 |
> | DataAction | 微軟.機器學習服務/工作空間/模型/寫入 | 在機器學習服務工作區中建立或更新模型 |
> | DataAction | 微軟.機器學習服務/工作空間/模型/刪除 | 移除機器學習服務工作區中的模型 |
> | DataAction | 微軟.機器學習服務/工作空間/模型/包/操作 | 機器學習服務工作區中的套件模型 |
> |  | **工作區/模組** |  |
> | DataAction | 微軟.機器學習服務/工作空間/模組/讀取 | 取得機器學習服務工作區中的模組 |
> | DataAction | 微軟.機器學習服務/工作空間/模組/寫入 | 在機器學習服務工作區中建立或更新模組 |
> |  | **工作區/導管草稿** |  |
> | DataAction | 微軟.機器學習服務/工作空間/管道草稿/讀取 | 在機器學習服務工作區中獲取管道草稿 |
> | DataAction | 微軟.機器學習服務/工作空間/管道草稿/寫入 | 在機器學習服務工作區中建立或更新管道草稿 |
> | DataAction | 微軟.機器學習服務/工作區/管道草稿/刪除 | 移除機器學習服務工作區中的導管草稿 |
> |  | **工作空間/服務** |  |
> | DataAction | 微軟.機器學習服務/工作空間/服務/讀取 | 取得機器學習服務工作區中的服務 |
> |  | **工作空間/服務/服務** |  |
> | DataAction | 微軟.機器學習服務/工作空間/服務/aci/寫入 | 在機器學習服務工作區中建立或更新 ACI 服務 |
> | DataAction | 微軟.機器學習服務/工作空間/服務/aci/清單鍵/操作 | 在機器學習服務工作區中列出 ACI 服務的鍵 |
> | DataAction | 微軟.機器學習服務/工作空間/服務/aci/刪除 | 移除機器學習服務工作區中的 ACI 服務 |
> |  | **工作空間/服務/aks/devtest** |  |
> | DataAction | 微軟.機器學習服務/工作空間/服務/aks/測試/寫入 | 在機器學習服務工作區中建立或更新開發性 AKS 服務 |
> | DataAction | 微軟.機器學習服務/工作空間/服務/aks/devtest/清單鍵/操作 | 列出機器學習服務工作區中開發 AKS 服務的金鑰 |
> | DataAction | 微軟.機器學習服務/工作空間/服務/aks/devtest/刪除 | 移除機器學習服務工作區中最開發 AKS 服務 |
> | DataAction | 微軟.機器學習服務/工作空間/服務/aks/devtest/分數/操作 | 在機器學習服務工作區中建立最優 AKS 服務的分數 |
> |  | **工作空間/服務/aks/prod** |  |
> | DataAction | 微軟.機器學習服務/工作空間/服務/aks/prod/write/write | 在機器學習服務工作區中建立或更新 prod AKS 服務 |
> | DataAction | 微軟.機器學習服務/工作空間/服務/aks/prod/listkey/操作 | 在機器學習服務工作區中列出 prod AKS 服務的鍵 |
> | DataAction | 微軟.機器學習服務/工作空間/服務/aks/prod/刪除 | 移除機器學習服務工作區中的 PROAKS 服務 |
> | DataAction | 微軟.機器學習服務/工作空間/服務/aks/prod/分數/操作 | 機器學習服務工作區中推動 AKS 服務的分數 |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

Azure 服務[:Azure 資源的託管識別](../active-directory/managed-identities-azure-resources/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.ManagedIdentity/register/action | 為受控識別資源提供者註冊訂用帳戶 |
> |  | **身份** |  |
> | 動作 | 微軟.託管身份/身份/讀取 | 取得現有系統配置的識別 |
> |  | **操作** |  |
> | 動作 | 微軟.託管身份/操作/讀取 | 列出 Microsoft.託管身份資源提供者上可用的作業 |
> |  | **userAssignedIdentities** |  |
> | 動作 | Microsoft.ManagedIdentity/userAssignedIdentities/read | 取得現有已指派使用者的身分識別 |
> | 動作 | Microsoft.ManagedIdentity/userAssignedIdentities/write | 建立新的已指派使用者的身分識別，或更新與現有已指派使用者之身分識別相關聯的標記 |
> | 動作 | Microsoft.ManagedIdentity/userAssignedIdentities/delete | 刪除現有已指派使用者的身分識別 |
> | 動作 | Microsoft.ManagedIdentity/userAssignedIdentities/assign/action | 用來將現有已指派使用者的身分識別指派給資源的 RBAC 動作 |

## <a name="microsoftmanagedservices"></a>微軟.託管服務

Azure 服務[:Azure 燈塔](../lighthouse/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | 微軟.託管服務/註冊/操作 | 註冊到託管服務。 |
> | 動作 | 微軟.託管服務/取消註冊/操作 | 從託管服務取消註冊。 |
> |  | **市場註冊定義** |  |
> | 動作 | 微軟.託管服務/市場註冊定義/讀取 | 檢索託管服務註冊定義的清單。 |
> |  | **操作** |  |
> | 動作 | 微軟.託管服務/操作/讀取 | 檢索託管服務操作的清單。 |
> |  | **操作 狀態** |  |
> | 動作 | 微軟.託管服務/操作 狀態/讀取 | 讀取資源的作業狀態。 |
> |  | **註冊配置** |  |
> | 動作 | 微軟.託管服務/註冊分配/讀取 | 檢索託管服務註冊分配的清單。 |
> | 動作 | 微軟.託管服務/註冊分配/寫入 | 添加或修改託管服務註冊分配。 |
> | 動作 | 微軟.託管服務/註冊分配/刪除 | 刪除託管服務註冊分配。 |
> |  | **註冊定義** |  |
> | 動作 | 微軟.託管服務/註冊定義/讀取 | 檢索託管服務註冊定義的清單。 |
> | 動作 | 微軟.託管服務/註冊定義/寫入 | 添加或修改託管服務註冊定義。 |
> | 動作 | 微軟.託管服務/註冊定義/刪除 | 刪除託管服務註冊定義。 |

## <a name="microsoftmanagement"></a>Microsoft.Management

Azure 服務:[管理群組](../governance/management-groups/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.Management/checkNameAvailability/action | 檢查指定的管理群組名稱是否有效且唯一。 |
> | 動作 | Microsoft.Management/getEntities/action | 列出已驗證之使用者的所有實體 (管理群組、訂用帳戶等)。 |
> | 動作 | Microsoft.Management/register/action | 向 Microsoft.Management 註冊指定的訂用帳戶 |
> |  | **管理群組** |  |
> | 動作 | Microsoft.Management/managementGroups/read | 列出已驗證之使用者的管理群組。 |
> | 動作 | Microsoft.Management/managementGroups/write | 建立或更新管理群組。 |
> | 動作 | Microsoft.Management/managementGroups/delete | 刪除管理群組。 |
> |  | **管理群組/後代** |  |
> | 動作 | 微軟.管理/管理組/後代/閱讀 | 獲取管理組的所有後代(管理組、訂閱)。 |
> |  | **管理群組/設定** |  |
> | 動作 | 微軟.管理/管理組/設置/讀取 | 列出現有的管理組層次結構設置。 |
> | 動作 | 微軟.管理/管理群組/設定/寫入 | 創建或更新管理組層次結構設置。 |
> | 動作 | 微軟管理/管理群組/設定/刪除 | 刪除管理組層次結構設置。 |
> |  | **管理群組/訂閱** |  |
> | 動作 | Microsoft.Management/managementGroups/subscriptions/write | 將現有的訂用帳戶關聯至管理群組。 |
> | 動作 | Microsoft.Management/managementGroups/subscriptions/delete | 從管理群組中取消訂用帳戶的關聯。 |

## <a name="microsoftmaps"></a>Microsoft.Maps

Azure 服務[:Azure 對應](../azure-maps/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.Maps/register/action | 註冊提供者 |
> |  | **帳戶** |  |
> | 動作 | Microsoft.Maps/accounts/write | 建立或更新地圖服務帳戶。 |
> | 動作 | Microsoft.Maps/accounts/read | 取得地圖服務帳戶。 |
> | 動作 | Microsoft.Maps/accounts/delete | 刪除地圖服務帳戶。 |
> | 動作 | Microsoft.Maps/accounts/listKeys/action | 列出地圖服務帳戶金鑰 |
> | 動作 | Microsoft.Maps/accounts/regenerateKey/action | 產生新地圖服務帳戶的主要或次要金鑰 |
> |  | **accounts/eventGridFilters** |  |
> | 動作 | Microsoft.Maps/accounts/eventGridFilters/delete | 刪除事件格線篩選 |
> | 動作 | Microsoft.Maps/accounts/eventGridFilters/read | 取得事件格線篩選 |
> | 動作 | Microsoft.Maps/accounts/eventGridFilters/write | 建立或更新事件格線篩選 |
> |  | **操作** |  |
> | 動作 | 微軟.地圖/操作/閱讀 | 閱讀提供者操作 |
> |  | **帳號/資料** |  |
> | DataAction | Microsoft.Maps/accounts/data/read | 將資料讀取權限授與地圖服務帳戶。 |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

Azure 服務:核心

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.Marketplace/register/action | 在訂用帳戶中註冊 Microsoft.Marketplace 資源提供者。 |
> | 動作 | 微軟.市場/私人商店/行動 | 更新專用應用商店。 |
> |  | **offerTypes/publishers/offers/plans/agreements** |  |
> | 動作 | Microsoft.Marketplace/offerTypes/publishers/offers/plans/agreements/read | 傳回協議。 |
> | 動作 | Microsoft.Marketplace/offerTypes/publishers/offers/plans/agreements/write | 接受已簽署的協議。 |
> |  | **offerTypes/publishers/offers/plans/configs** |  |
> | 動作 | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/read | 傳回組態。 |
> | 動作 | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/write | 儲存組態。 |
> | 動作 | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/importImage/action | 將映像匯入至終端使用者的 ACR。 |
> |  | **私人商店** |  |
> | 動作 | 微軟.市場/私人商店/寫入 | 創建專用商店。 |
> | 動作 | 微軟.市場/私人商店/刪除 | 刪除專用商店。 |
> | 動作 | 微軟.市場/私人商店/優惠/行動 | 私人商店中的更新產品/服務。 |
> | 動作 | 微軟.市場/私人商店/閱讀 | 讀取私人商店。 |
> |  | **私人商店/優惠** |  |
> | 動作 | 微軟.市場/私人商店/優惠/寫入 | 在私人商店中創建產品/服務。 |
> | 動作 | 微軟.市場/私人商店/優惠/刪除 | 從私人商店中刪除產品/服務。 |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

Azure 服務:核心

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> |  | **經典開發服務** |  |
> | 動作 | Microsoft.MarketplaceApps/ClassicDevServices/read | 在傳統開發服務上進行 GET 作業。 |
> | 動作 | Microsoft.MarketplaceApps/ClassicDevServices/delete | 在傳統開發服務資源上進行 DELETE 作業。 |
> | 動作 | Microsoft.MarketplaceApps/ClassicDevServices/listSingleSignOnToken/action | 取得傳統開發服務的單一登入 URL。 |
> | 動作 | Microsoft.MarketplaceApps/ClassicDevServices/listSecrets/action | 取得傳統開發服務資源管理金鑰。 |
> | 動作 | Microsoft.MarketplaceApps/ClassicDevServices/regenerateKey/action | 產生傳統開發服務資源管理金鑰。 |
> |  | **作業** |  |
> | 動作 | Microsoft.MarketplaceApps/Operations/read | 讀取所有資源類型的作業。 |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

Azure 服務:核心

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> |  | **協定** |  |
> | 動作 | Microsoft.MarketplaceOrdering/agreements/read | 傳回指定訂用帳戶下的所有合約 |
> |  | **協定/報價/計劃** |  |
> | 動作 | Microsoft.MarketplaceOrdering/agreements/offers/plans/read | 傳回給定 Marketplace 項目的合約 |
> | 動作 | Microsoft.MarketplaceOrdering/agreements/offers/plans/sign/action | 簽署給定 Marketplace 項目的合約 |
> | 動作 | Microsoft.MarketplaceOrdering/agreements/offers/plans/cancel/action | 取消給定 Marketplace 項目的合約 |
> |  | **報價類型/發行者/優惠/計劃/協定** |  |
> | 動作 | Microsoft.MarketplaceOrdering/offertypes/publishers/offers/plans/agreements/read | 取得指定 Marketplace 虛擬機器項目的合約 |
> | 動作 | Microsoft.MarketplaceOrdering/offertypes/publishers/offers/plans/agreements/write | 簽署或取消指定 Marketplace 虛擬機器項目的合約 |
> |  | **操作** |  |
> | 動作 | 微軟.市場訂購/操作/讀取 | 在 API 中列出所有可能的操作 |

## <a name="microsoftmedia"></a>Microsoft.Media

Azure 服務:[媒體服務](../media-services/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.Media/register/action | 為媒體服務資源提供者註冊訂用帳戶，並讓您能夠建立媒體服務帳戶 |
> | 動作 | Microsoft.Media/unregister/action | 為媒體服務資源提供者取消註冊訂用帳戶 |
> | 動作 | Microsoft.Media/checknameavailability/action | 檢查媒體服務帳戶名稱是否可供使用 |
> |  | **mediaservices** |  |
> | 動作 | Microsoft.Media/mediaservices/read | 讀取任何媒體服務帳戶 |
> | 動作 | Microsoft.Media/mediaservices/write | 建立或更新任何媒體服務帳戶 |
> | 動作 | Microsoft.Media/mediaservices/delete | 刪除任何媒體服務帳戶 |
> | 動作 | Microsoft.Media/mediaservices/syncStorageKeys/action | 同步處理已連結之 Azure 儲存體帳戶的儲存體金鑰 |
> |  | **媒體服務/帳戶篩選器** |  |
> | 動作 | Microsoft.Media/mediaservices/accountfilters/read | 讀取任何帳戶篩選條件 |
> | 動作 | Microsoft.Media/mediaservices/accountfilters/write | 建立或更新任何帳戶篩選條件 |
> | 動作 | Microsoft.Media/mediaservices/accountfilters/delete | 刪除任何帳戶篩選條件 |
> |  | **mediaservices/assets** |  |
> | 動作 | Microsoft.Media/mediaservices/assets/read | 讀取任何資產 |
> | 動作 | Microsoft.Media/mediaservices/assets/write | 建立或更新任何資產 |
> | 動作 | Microsoft.Media/mediaservices/assets/delete | 刪除任何資產 |
> | 動作 | Microsoft.Media/mediaservices/assets/listContainerSas/action | 列出資產容器 SAS URL |
> | 動作 | Microsoft.Media/mediaservices/assets/getEncryptionKey/action | 取得資產加密金鑰 |
> | 動作 | Microsoft.Media/mediaservices/assets/listStreamingLocators/action | 列出資產的串流定位器 |
> |  | **媒體服務/資產/資產過濾器** |  |
> | 動作 | Microsoft.Media/mediaservices/assets/assetfilters/read | 讀取任何資產篩選條件 |
> | 動作 | Microsoft.Media/mediaservices/assets/assetfilters/write | 建立或更新任何資產篩選條件 |
> | 動作 | Microsoft.Media/mediaservices/assets/assetfilters/delete | 刪除任何資產篩選條件 |
> |  | **mediaservices/contentKeyPolicies** |  |
> | 動作 | Microsoft.Media/mediaservices/contentKeyPolicies/read | 讀取任何內容金鑰原則 |
> | 動作 | Microsoft.Media/mediaservices/contentKeyPolicies/write | 建立或更新任何內容金鑰原則 |
> | 動作 | Microsoft.Media/mediaservices/contentKeyPolicies/delete | 刪除任何內容金鑰原則 |
> | 動作 | Microsoft.Media/mediaservices/contentKeyPolicies/getPolicyPropertiesWithSecrets/action | 取得含有祕密的原則屬性 |
> |  | **mediaservices/eventGridFilters** |  |
> | 動作 | Microsoft.Media/mediaservices/eventGridFilters/read | 讀取任何事件方格篩選 |
> | 動作 | Microsoft.Media/mediaservices/eventGridFilters/write | 建立或更新任何事件方格篩選 |
> | 動作 | Microsoft.Media/mediaservices/eventGridFilters/delete | 刪除任何事件方格篩選 |
> |  | **mediaservices/liveEventOperations** |  |
> | 動作 | Microsoft.Media/mediaservices/liveEventOperations/read | 讀取任何即時事件作業 |
> |  | **mediaservices/liveEvents** |  |
> | 動作 | Microsoft.Media/mediaservices/liveEvents/read | 讀取任何即時事件 |
> | 動作 | Microsoft.Media/mediaservices/liveEvents/write | 建立或更新任何即時事件 |
> | 動作 | Microsoft.Media/mediaservices/liveEvents/delete | 刪除任何即時事件 |
> | 動作 | Microsoft.Media/mediaservices/liveEvents/start/action | 啟動任何即時事件作業 |
> | 動作 | Microsoft.Media/mediaservices/liveEvents/stop/action | 停止任何即時事件作業 |
> | 動作 | Microsoft.Media/mediaservices/liveEvents/reset/action | 重設任何即時事件作業 |
> |  | **mediaservices/liveEvents/liveOutputs** |  |
> | 動作 | Microsoft.Media/mediaservices/liveEvents/liveOutputs/read | 讀取任何即時輸出 |
> | 動作 | Microsoft.Media/mediaservices/liveEvents/liveOutputs/write | 建立或更新任何即時輸出 |
> | 動作 | Microsoft.Media/mediaservices/liveEvents/liveOutputs/delete | 刪除任何即時輸出 |
> |  | **mediaservices/liveOutputOperations** |  |
> | 動作 | Microsoft.Media/mediaservices/liveOutputOperations/read | 讀取任何即時輸出作業 |
> |  | **mediaservices/streamingEndpointOperations** |  |
> | 動作 | Microsoft.Media/mediaservices/streamingEndpointOperations/read | 讀取任何串流端點作業 |
> |  | **mediaservices/streamingEndpoints** |  |
> | 動作 | Microsoft.Media/mediaservices/streamingEndpoints/read | 讀取任何串流端點 |
> | 動作 | Microsoft.Media/mediaservices/streamingEndpoints/write | 建立或更新任何串流端點 |
> | 動作 | Microsoft.Media/mediaservices/streamingEndpoints/delete | 刪除任何串流端點 |
> | 動作 | Microsoft.Media/mediaservices/streamingEndpoints/start/action | 啟動任何串流端點作業 |
> | 動作 | Microsoft.Media/mediaservices/streamingEndpoints/stop/action | 停止任何串流端點作業 |
> | 動作 | Microsoft.Media/mediaservices/streamingEndpoints/scale/action | 調整任何串流端點作業 |
> |  | **mediaservices/streamingLocators** |  |
> | 動作 | Microsoft.Media/mediaservices/streamingLocators/read | 讀取任何串流定位器 |
> | 動作 | Microsoft.Media/mediaservices/streamingLocators/write | 建立或更新任何串流定位器 |
> | 動作 | Microsoft.Media/mediaservices/streamingLocators/delete | 刪除任何串流定位器 |
> | 動作 | Microsoft.Media/mediaservices/streamingLocators/listContentKeys/action | 列出內容金鑰 |
> | 動作 | Microsoft.Media/mediaservices/streamingLocators/listPaths/action | 列出路徑 |
> |  | **mediaservices/streamingPolicies** |  |
> | 動作 | Microsoft.Media/mediaservices/streamingPolicies/read | 讀取任何串流原則 |
> | 動作 | Microsoft.Media/mediaservices/streamingPolicies/write | 建立或更新任何串流原則 |
> | 動作 | Microsoft.Media/mediaservices/streamingPolicies/delete | 刪除任何串流原則 |
> |  | **mediaservices/transforms** |  |
> | 動作 | Microsoft.Media/mediaservices/transforms/read | 讀取任何轉換 |
> | 動作 | Microsoft.Media/mediaservices/transforms/write | 建立或更新任何轉換 |
> | 動作 | Microsoft.Media/mediaservices/transforms/delete | 刪除任何轉換 |
> |  | **mediaservices/transforms/jobs** |  |
> | 動作 | Microsoft.Media/mediaservices/transforms/jobs/read | 讀取任何作業 |
> | 動作 | Microsoft.Media/mediaservices/transforms/jobs/write | 建立或更新任何作業 |
> | 動作 | Microsoft.Media/mediaservices/transforms/jobs/delete | 刪除任何作業 |
> | 動作 | Microsoft.Media/mediaservices/transforms/jobs/cancelJob/action | 取消作業 |
> |  | **操作** |  |
> | 動作 | Microsoft.Media/operations/read | 取得可用的作業 |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

Azure 服務[:Azure 移轉](../migrate/migrate-services-overview.md)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.Migrate/register/action | 向 Microsoft.Migrate 資源提供者註冊訂用帳戶 |
> |  | **assessmentprojects** |  |
> | 動作 | 微軟.遷移/評估專案/閱讀 | 取得評估項目的屬性 |
> | 動作 | 微軟.遷移/評估專案/寫入 | 建立新的評估項目或更新現有評估專案 |
> | 動作 | 微軟.移轉/評估項目/刪除 | 刪除評項項目 |
> |  | **評估項目/評估選項** |  |
> | 動作 | 微軟.遷移/評估專案/評估選項/讀取 | 取得可在指定位置使用的評定選項 |
> |  | **評估項目/評估** |  |
> | 動作 | 微軟.遷移/評估專案/評估/讀取 | 列出專案內的評定 |
> |  | **評估項目/群組** |  |
> | 動作 | 微軟.遷移/評估專案/組/讀取 | 取得群組的屬性 |
> | 動作 | 微軟.遷移/評估專案/組/寫入 | 建立新的群組或更新現有的群組 |
> | 動作 | 微軟.遷移/評估專案/組/刪除 | 刪除群組 |
> | 動作 | 微軟.遷移/評估專案/組/更新機器/操作 | 以新增或移除電腦更新群組 |
> |  | **評估專案/小組/評估** |  |
> | 動作 | 微軟.遷移/評估專案/組/評估/讀取 | 取得評定的屬性 |
> | 動作 | 微軟.遷移/評估專案/組/評估/寫入 | 建立新的評定或更新現有的評定 |
> | 動作 | 微軟.遷移/評估專案/組/評估/刪除 | 刪除評定 |
> | 動作 | 微軟.遷移/評估專案/組/評估/下載/操作 | 下載評定報告的 URL |
> |  | **評估專案/小組/評估/評估機** |  |
> | 動作 | 微軟.遷移/評估專案/組/評估/評估機/讀 | 取得已評定電腦的屬性 |
> |  | **評估專案/超收器** |  |
> | 動作 | 微軟.遷移/評估專案/超收器/閱讀 | 取得超V收集器屬性 |
> | 動作 | 微軟.遷移/評估專案/超收器/寫入 | 建立新的 HyperV 收集器或更新現有的 HyperV 收集器 |
> | 動作 | 微軟.遷移/評估專案/超收器/刪除 | 移除超V收集器 |
> |  | **評估項目/進口收集器** |  |
> | 動作 | 微軟.遷移/評估專案/導入者/讀取 | 取得匯入收集器屬性 |
> | 動作 | 微軟.遷移/評估專案/導入者/寫入 | 建立新的匯入收集器或更新現有匯入收集器 |
> | 動作 | 微軟.遷移/評估專案/導入者/刪除 | 移除匯入收集器 |
> |  | **評估專案/機器** |  |
> | 動作 | 微軟.遷移/評估專案/機器/讀取 | 取得電腦的屬性 |
> |  | **評估專案/伺服器收集器** |  |
> | 動作 | 微軟.遷移/評估專案/伺服器收集器/讀取 | 取得伺服器收集器屬性 |
> | 動作 | 微軟.遷移/評估專案/伺服器收集器/寫入 | 建立新的伺服器收集器或更新現有伺服器收集器 |
> |  | **評估專案/vmware 收集器** |  |
> | 動作 | 微軟.遷移/評估專案/vmware收集器/閱讀 | 取得 VMware 收集器屬性 |
> | 動作 | 微軟.遷移/評估專案/vmware收集器/寫入 | 建立新的 VMware 收集器或更新現有的 VMware 收集器 |
> | 動作 | 微軟.遷移/評估專案/vmware收集器/刪除 | 移除 VMware 收集器 |
> |  | **位置** |  |
> | 動作 | Microsoft.Migrate/locations/checknameavailability/action | 檢查指定位置中，指定訂用帳戶的資源名稱可用性 |
> |  | **位置/評估選項** |  |
> | 動作 | Microsoft.Migrate/locations/assessmentOptions/read | 取得可在指定位置使用的評定選項 |
> |  | **migrateprojects** |  |
> | 動作 | Microsoft.Migrate/migrateprojects/read | 取得移轉項目屬性 |
> | 動作 | 微軟.遷移/遷移專案/寫入 | 建立新的移轉專案或更新現有遷移專案 |
> | 動作 | 微軟.移移/移轉專案/刪除 | 移除移轉項目 |
> | 動作 | 微軟.遷移/遷移專案/註冊工具/操作 | 將工具註冊到移轉項目 |
> | 動作 | 微軟.遷移/遷移專案/刷新摘要/操作 | 更新移轉專案摘要 |
> |  | **移轉項目/資料庫實體** |  |
> | 動作 | 微軟.遷移/遷移專案/資料庫實例/讀取 | 取得資料庫實體屬性 |
> |  | **移轉項目/資料庫** |  |
> | 動作 | 微軟.遷移/遷移專案/資料庫/讀取 | 取得資料庫屬性 |
> |  | **移轉項目/電腦** |  |
> | 動作 | 微軟.遷移/遷移專案/計算機/讀取 | 取得電腦的屬性 |
> |  | **移轉項目/移轉事件** |  |
> | 動作 | 微軟.遷移/遷移專案/遷移事件/讀取 | 獲取遷移事件的屬性。 |
> | 動作 | 微軟.遷移/遷移專案/遷移事件/刪除 | 移除移轉事件 |
> |  | **移轉專案/解決方案** |  |
> | 動作 | Microsoft.Migrate/migrateprojects/solutions/read | 取得移轉專案解決方案的屬性 |
> | 動作 | 微軟.遷移/遷移專案/解決方案/寫入 | 建立新的遷移專案解決方案或更新現有遷移專案解決方案 |
> | 動作 | 微軟.遷移/遷移專案/解決方案/刪除 | 移除移轉專案解決方案 |
> | 動作 | Microsoft.Migrate/migrateprojects/solutions/getconfig/action | 取得遷移專案解決方案組態 |
> | 動作 | 微軟.遷移/遷移專案/解決方案/清理資料/操作 | 清理移轉的項目解決方案資料 |
> |  | **作業** |  |
> | 動作 | Microsoft.Migrate/Operations/read | 列出可對 Microsoft.Migrate 資源提供者進行的作業 |
> |  | **專案** |  |
> | 動作 | Microsoft.Migrate/projects/read | 取得專案的屬性 |
> | 動作 | Microsoft.Migrate/projects/write | 建立新的專案或更新現有的專案 |
> | 動作 | Microsoft.Migrate/projects/delete | 刪除專案 |
> | 動作 | Microsoft.Migrate/projects/keys/action | 取得專案的共用金鑰 |
> |  | **專案/評估** |  |
> | 動作 | Microsoft.Migrate/projects/assessments/read | 列出專案內的評定 |
> |  | **專案/組** |  |
> | 動作 | Microsoft.Migrate/projects/groups/read | 取得群組的屬性 |
> | 動作 | Microsoft.Migrate/projects/groups/write | 建立新的群組或更新現有的群組 |
> | 動作 | Microsoft.Migrate/projects/groups/delete | 刪除群組 |
> |  | **專案/小組/評估** |  |
> | 動作 | Microsoft.Migrate/projects/groups/assessments/read | 取得評定的屬性 |
> | 動作 | Microsoft.Migrate/projects/groups/assessments/write | 建立新的評定或更新現有的評定 |
> | 動作 | Microsoft.Migrate/projects/groups/assessments/delete | 刪除評定 |
> | 動作 | Microsoft.Migrate/projects/groups/assessments/downloadurl/action | 下載評定報告的 URL |
> |  | **專案/小組/評估/評估機** |  |
> | 動作 | Microsoft.Migrate/projects/groups/assessments/assessedmachines/read | 取得已評定電腦的屬性 |
> |  | **專案/機器** |  |
> | 動作 | Microsoft.Migrate/projects/machines/read | 取得電腦的屬性 |

## <a name="microsoftmixedreality"></a>微軟.混合實境

Azure 服務[:Azure 空間錨點](../spatial-anchors/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | 微軟.混合現實/註冊/行動 | 註冊混合現實資源提供程式的訂閱。 |
> |  | **遠端呈現帳戶/提供程式/微軟.見解/指標定義** |  |
> | 動作 | 微軟.混合現實/遠端呈現帳戶/供應商/微軟.見解/指標定義/讀取 | 獲取 Microsoft 的可用指標。 |
> |  | **空間錨定帳戶/提供者/微軟.見解/診斷設置** |  |
> | 動作 | 微軟.混合現實/空間錨定帳戶/提供者/微軟.見解/診斷設置/閱讀 | 獲取 Microsoft 的診斷設置。 |
> | 動作 | 微軟.混合現實/空間錨定帳戶/提供者/微軟.見解/診斷設置/寫入 | 創建或更新 Microsoft 的診斷設置。 |
> |  | **空間錨定帳戶/供應商/微軟.見解/指標定義** |  |
> | 動作 | 微軟.混合現實/空間錨定帳戶/供應商/微軟.見解/指標定義/閱讀 | 獲取 Microsoft 的可用指標。 |
> |  | **遠端成像帳戶** |  |
> | DataAction | 微軟.混合實境/遠端呈現帳戶/轉換/操作 | 開始資產轉換 |
> | DataAction | 微軟.混合實境/遠端呈現帳戶/管理會話/操作 | 開始工作階段 |
> |  | **遠端成像帳號/轉換** |  |
> | DataAction | 微軟.混合實境/遠端呈現帳戶/轉換/讀取 | 取得資產轉換屬性 |
> | DataAction | 微軟.混合實境/遠端呈現帳戶/轉換/刪除 | 停止資產轉換 |
> |  | **遠端渲染帳號/診斷** |  |
> | DataAction | 微軟.混合現實/遠程呈現帳戶/診斷/讀取 | 連接到遠端的渲染檢查器 |
> |  | **遠端渲染帳戶/管理工作階段** |  |
> | DataAction | 微軟.混合現實/遠程呈現帳戶/管理會話/讀取 | 取得工作階段屬性 |
> | DataAction | 微軟.混合實境/遠端呈現帳戶/管理會話/刪除 | 停止工作階段 |
> |  | **遠端成像帳號/成像** |  |
> | DataAction | 微軟.混合實境/遠端呈現帳戶/渲染/讀取 | 連線到工作階段 |
> |  | **空間錨點帳戶** |  |
> | DataAction | 微軟.混合現實/空間錨定帳戶/創建/操作 | 建立空間錨點 |
> | DataAction | 微軟.混合現實/空間錨定帳戶/刪除 | 刪除空間錨點 |
> | DataAction | 微軟.混合現實/空間錨定帳戶/寫入 | 更新空間錨點屬性 |
> |  | **空間錨點帳戶/發現** |  |
> | DataAction | 微軟.混合現實/空間錨定帳戶/發現/閱讀 | 發現附近的空間錨點 |
> |  | **空間錨點帳戶/屬性** |  |
> | DataAction | 微軟.混合現實/空間錨定帳戶/屬性/讀取 | 取得空間錨點的屬性 |
> |  | **空間錨點帳戶/查詢** |  |
> | DataAction | 微軟.混合現實/空間錨定帳戶/查詢/閱讀 | 定位空間錨點 |
> |  | **空間錨點帳戶/提交** |  |
> | DataAction | 微軟.混合現實/空間錨定帳戶/提交/閱讀 | 提交診斷資料以説明提高 Azure 空間錨點服務的品質 |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

Azure 服務[:Azure NetApp 檔案](../azure-netapp-files/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | 微軟.NetApp/註冊/操作 | 訂閱註冊操作 |
> | 動作 | 微軟.NetApp/取消註冊/操作 | 取消註冊訂閱到 Microsoft.NetApp 資源提供者 |
> |  | **位置** |  |
> | 動作 | Microsoft.NetApp/locations/read | 讀取可用性檢查資源。 |
> | 動作 | 微軟.NetApp/位置/校名可用性/操作 | 檢查資源名稱是否可用 |
> | 動作 | 微軟.NetApp/位置/檢查檔案路徑可用性/操作 | 檢查檔案路徑是否可使用 |
> |  | **位置/動作結果** |  |
> | 動作 | Microsoft.NetApp/locations/operationresults/read | 讀取作業結果資源。 |
> |  | **淨應用帳戶** |  |
> | 動作 | Microsoft.NetApp/netAppAccounts/read | 讀取帳戶資源。 |
> | 動作 | Microsoft.NetApp/netAppAccounts/write | 寫入帳戶資源。 |
> | 動作 | Microsoft.NetApp/netAppAccounts/delete | 刪除帳戶資源。 |
> |  | **淨應用帳戶/帳戶備份** |  |
> | 動作 | 微軟.NetApp/netApp帳戶/帳戶備份/讀取 | 讀取帳戶備份資源。 |
> | 動作 | 微軟.NetApp/netApp帳戶/帳戶備份/寫入 | 寫入帳戶備份資源。 |
> | 動作 | 微軟.NetApp/NetApp帳戶/帳戶備份/刪除 | 刪除帳戶備份資源。 |
> |  | **淨應用帳戶/備份原則** |  |
> | 動作 | 微軟.NetApp/NetApp帳戶/備份策略/讀取 | 讀取備份策略資源。 |
> | 動作 | 微軟.NetApp/NetApp帳戶/備份策略/寫入 | 寫入備份策略資源。 |
> | 動作 | 微軟.NetApp/NetApp帳戶/備份策略/刪除 | 刪除備份策略資源。 |
> |  | **淨應用帳號/容量池** |  |
> | 動作 | Microsoft.NetApp/netAppAccounts/capacityPools/read | 讀取集區資源。 |
> | 動作 | Microsoft.NetApp/netAppAccounts/capacityPools/write | 寫入集區資源。 |
> | 動作 | Microsoft.NetApp/netAppAccounts/capacityPools/delete | 刪除集區資源。 |
> |  | **淨應用帳號/容量池/卷** |  |
> | 動作 | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/read | 讀取磁碟區資源。 |
> | 動作 | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/write | 寫入磁碟區資源。 |
> | 動作 | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/delete | 刪除磁碟區資源。 |
> | 動作 | 微軟.NetApp/NetApp帳戶/容量池/卷/恢復/操作 | 將捲還原到特定快照 |
> | 動作 | 微軟.NetApp/NetApp帳戶/容量池/卷/中斷複製/操作 | 中斷卷複製關係 |
> | 動作 | 微軟.NetApp/NetApp 帳戶/容量池/卷/複製狀態/操作 | 讀取卷複製的狀態。 |
> | 動作 | 微軟.NetApp/NetApp帳戶/容量池/卷/授權複製/操作 | 授權來源複製 |
> | 動作 | 微軟.NetApp/NetApp帳戶/容量池/卷/重新同步複製/操作 | 在目標捲上重新同步複製 |
> | 動作 | 微軟.NetApp/NetApp 帳戶/容量池/卷/刪除複製/操作 | 刪除目標卷中的複製 |
> |  | **淨應用帳號/容量池/卷/備份** |  |
> | 動作 | 微軟.NetApp/NetApp帳戶/容量池/卷/備份/讀取 | 讀取備份資源。 |
> | 動作 | 微軟.NetApp/NetApp帳戶/容量池/卷/備份/寫入 | 寫入備份資源。 |
> | 動作 | 微軟.NetApp/NetApp帳戶/容量池/卷/備份/刪除 | 刪除備份資源。 |
> |  | **淨應用帳戶/容量池/卷/快照** |  |
> | 動作 | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/read | 讀取快照集資源。 |
> | 動作 | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/write | 寫入快照集資源。 |
> | 動作 | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/delete | 刪除快照集資源。 |
> |  | **淨應用帳戶/快照策略** |  |
> | 動作 | 微軟.NetApp/NetApp帳戶/快照策略/讀取 | 讀取快照策略資源。 |
> | 動作 | 微軟.NetApp/NetApp帳戶/快照策略/寫入 | 寫入快照策略資源。 |
> | 動作 | 微軟.NetApp/NetApp帳戶/快照策略/刪除 | 刪除快照策略資源。 |
> | 動作 | 微軟.NetApp/NetApp帳戶/快照策略/清單卷/操作 | 清單連線到快照政策的卷 |
> |  | **淨應用帳戶/保管庫** |  |
> | 動作 | 微軟.NetApp/netApp帳戶/保管庫/讀取 | 讀取保管庫資源。 |
> |  | **作業** |  |
> | 動作 | Microsoft.NetApp/Operations/read | 讀取作業資源。 |

## <a name="microsoftnetwork"></a>Microsoft.Network

Azure 服務:[虛擬網路](../virtual-network/index.yml),[負載均衡器](../load-balancer/index.yml),[應用程式閘道](../application-gateway/index.yml), [Azure DNS](../dns/index.yml),[快速路由](../expressroute/index.yml), [VPN 閘道](../vpn-gateway/index.yml),[流量管理員](../traffic-manager/index.yml),[網路觀察程式](../network-watcher/index.yml), [Azure 防火牆](../firewall/index.yml)Azure[前門服務](../frontdoor/index.yml), [Azure 堡壘](../bastion/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.Network/register/action | 註冊訂用帳戶 |
> | 動作 | Microsoft.Network/unregister/action | 取消註冊訂用帳戶 |
> | 動作 | Microsoft.Network/checkTrafficManagerNameAvailability/action | 檢查流量管理員相對 DNS 名稱的可用性。 |
> | 動作 | Microsoft.Network/checkFrontDoorNameAvailability/action | 檢查 Front Door 名稱是否可用 |
> |  | **應用程式閘道可用要求標題** |  |
> | 動作 | Microsoft.Network/applicationGatewayAvailableRequestHeaders/read | 取得應用程式閘道可用的要求標頭 |
> |  | **應用程式閘道可用回應標頭** |  |
> | 動作 | Microsoft.Network/applicationGatewayAvailableResponseHeaders/read | 取得應用程式閘道可用的回應標頭 |
> |  | **應用程式閘道執行伺服器變數** |  |
> | 動作 | Microsoft.Network/applicationGatewayAvailableServerVariables/read | 取得應用程式閘道可用的伺服器變數 |
> |  | **應用程式閘道可取得 Ssl 選項** |  |
> | 動作 | Microsoft.Network/applicationGatewayAvailableSslOptions/read | 應用程式閘道可用的 SSL 選項 |
> |  | **應用程式閘道可用 Ssl 選項/ 預先定義政策** |  |
> | 動作 | Microsoft.Network/applicationGatewayAvailableSslOptions/predefinedPolicies/read | 應用程式閘道 SSL 預先定義的原則 |
> |  | **應用程式閘道可取得 WafRuleSet** |  |
> | 動作 | Microsoft.Network/applicationGatewayAvailableWafRuleSets/read | 取得應用程式閘道可用的 WAF 規則集 |
> |  | **應用程式閘道** |  |
> | 動作 | Microsoft.Network/applicationGateways/read | 取得應用程式閘道 |
> | 動作 | Microsoft.Network/applicationGateways/write | 建立或更新應用程式閘道 |
> | 動作 | Microsoft.Network/applicationGateways/delete | 刪除應用程式閘道 |
> | 動作 | Microsoft.Network/applicationGateways/backendhealth/action | 取得應用程式閘道後端的健康狀態 |
> | 動作 | 微軟.網路/應用程式閘道/獲取後端健康需求/操作 | 依欲取得給定 HTTP 設定與後端介面的應用程式閘道後端執行狀況 |
> | 動作 | Microsoft.Network/applicationGateways/start/action | 啟動應用程式閘道 |
> | 動作 | Microsoft.Network/applicationGateways/stop/action | 停止應用程式閘道 |
> |  | **應用程式閘道/後端位址池** |  |
> | 動作 | Microsoft.Network/applicationGateways/backendAddressPools/join/action | 加入應用程式閘道後端位址池。 無法報警。 |
> |  | **應用程式閘道Web應用程式防火牆原則** |  |
> | 動作 | 微軟.網路/應用程式閘道Web應用程式防火牆策略/讀取 | 取得應用程式閘道 WAF 政策 |
> | 動作 | 微軟.網路/應用程式閘道Web應用程式防火牆策略/寫入 | 建立應用程式閘道 WAF 政策或更新應用程式閘道 WAF 政策 |
> | 動作 | 微軟.網路/應用程式閘道Web應用程式防火牆策略/刪除 | 移除應用程式閘道 WAF 政策 |
> |  | **應用程式規則集合** |  |
> | 動作 | 微軟.網路/應用程式規則集合/讀取 | 取得 Azure 防火牆應用程式規則集合 |
> | 動作 | 微軟.網路/應用程式規則集合/寫入 | 建立或更新 Azure 防火牆應用程式規則集合 |
> | 動作 | 微軟.網路/應用程式規則集合/刪除 | 移除 Azure 防火牆應用程式規則集合 |
> |  | **applicationSecurityGroups** |  |
> | 動作 | Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action | 將 IP 設定加入至應用程式安全性群組。 不可報警。 |
> | 動作 | Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | 將安全性規則加入至應用程式安全性群組。 不可報警。 |
> | 動作 | Microsoft.Network/applicationSecurityGroups/read | 取得應用程式安全性群組識別碼。 |
> | 動作 | Microsoft.Network/applicationSecurityGroups/write | 建立應用程式安全性群組，或更新現有的應用程式安全性群組。 |
> | 動作 | Microsoft.Network/applicationSecurityGroups/delete | 刪除應用程式安全性群組 |
> | 動作 | Microsoft.Network/applicationSecurityGroups/listIpConfigurations/action | 在 ApplicationSecurityGroup 中列出 IP 組態 |
> |  | **azureFirewallFqdnTags** |  |
> | 動作 | Microsoft.Network/azureFirewallFqdnTags/read | 取得 Azure 防火牆 FQDN 標記 |
> |  | **Azure 防火牆** |  |
> | 動作 | Microsoft.Network/azurefirewalls/read | 取得 Azure 防火牆 |
> | 動作 | Microsoft.Network/azurefirewalls/write | 建立或更新 Azure 防火牆 |
> | 動作 | Microsoft.Network/azurefirewalls/delete | 刪除 Azure 防火牆 |
> |  | **堡壘主機** |  |
> | 動作 | Microsoft.Network/bastionHosts/read | 取得堡壘主機 |
> | 動作 | Microsoft.Network/bastionHosts/write | 建立或更新堡壘主機 |
> | 動作 | Microsoft.Network/bastionHosts/delete | 刪除堡壘主機 |
> | 動作 | 微軟.網路/堡壘主機/獲取活動會話/操作 | 在堡壘主機中獲取活動會話 |
> | 動作 | 微軟.網路/堡壘主機/斷開連接活動會話/操作 | 斷開堡壘主機中給定的活動會話 |
> | 動作 | 微軟.網路/堡壘主機/獲取可共用連結/操作 | 傳回 Bastion 子網中指定 VM 的可分享網址,前提是其網址已建立 |
> | 動作 | 微軟.網路/堡壘主機/創建可共用連結/操作 | 在堡壘下為 VM 建立可分享的網址 並傳回網址 |
> | 動作 | 微軟.網路/堡壘主機/可共用連結/操作 | 刪除堡壘下提供的 VM 的可分享網址 |
> |  | **bgpServiceCommunities** |  |
> | 動作 | Microsoft.Network/bgpServiceCommunities/read | 取得 BGP 服務社群 |
> |  | **連線** |  |
> | 動作 | Microsoft.Network/connections/read | 取得 VirtualNetworkGatewayConnection |
> | 動作 | Microsoft.Network/connections/write | 建立或更新現有的 VirtualNetworkGatewayConnection |
> | 動作 | Microsoft.Network/connections/delete | 刪除 VirtualNetworkGatewayConnection |
> | 動作 | Microsoft.Network/connections/sharedkey/action | 取得 VirtualNetworkGatewayConnection SharedKey |
> | 動作 | Microsoft.Network/connections/vpndeviceconfigurationscript/action | 取得 VirtualNetworkGatewayConnection 的 VPN 裝置設定 |
> | 動作 | Microsoft.Network/connections/revoke/action | 將 Express Route 連線狀態標示為已撤銷 |
> | 動作 | 微軟.網路/連接/啟動封包擷取/操作 | 啟動虛擬網路閘道連接數據包捕獲。 |
> | 動作 | 微軟.網路/連接/停止封包擷取/操作 | 停止虛擬網路閘道連接數據包捕獲。 |
> |  | **連線/分享金鑰** |  |
> | 動作 | Microsoft.Network/connections/sharedKey/read | 取得 VirtualNetworkGatewayConnection SharedKey |
> | 動作 | Microsoft.Network/connections/sharedKey/write | 建立或更新現有的 VirtualNetworkGatewayConnection SharedKey |
> |  | **ddosCustomPolicies** |  |
> | 動作 | Microsoft.Network/ddosCustomPolicies/read | 取得 DDoS 自訂原則定義 |
> | 動作 | Microsoft.Network/ddosCustomPolicies/write | 建立 DDoS 自訂原則或更新現有的 DDoS 自訂原則 |
> | 動作 | Microsoft.Network/ddosCustomPolicies/delete | 刪除 DDoS 自訂原則 |
> |  | **ddosProtectionPlans** |  |
> | 動作 | Microsoft.Network/ddosProtectionPlans/read | 取得 DDoS 保護計劃 |
> | 動作 | Microsoft.Network/ddosProtectionPlans/write | 建立 DDoS 保護計劃，或更新 DDoS 保護計劃  |
> | 動作 | Microsoft.Network/ddosProtectionPlans/delete | 刪除 DDoS 保護計劃 |
> | 動作 | Microsoft.Network/ddosProtectionPlans/join/action | 加入 DDoS 保護計劃。 不可報警。 |
> |  | **dns 動作結果** |  |
> | 動作 | Microsoft.Network/dnsoperationresults/read | 取得 DNS 作業的結果 |
> |  | **dns 操作狀態** |  |
> | 動作 | Microsoft.Network/dnsoperationstatuses/read | 取得 DNS 作業的狀態  |
> |  | **dns 區域** |  |
> | 動作 | Microsoft.Network/dnszones/read | 取得 JSON 格式的 DNS 區域。 區域屬性包含標記、etag、numberOfRecordSets 和 maxNumberOfRecordSets。 請注意，此命令不會擷取區域內所包含的記錄集。 |
> | 動作 | Microsoft.Network/dnszones/write | 在資源群組內建立或更新 DNS 區域。  用來更新 DNS 區域資源上的標記。 請注意，此命令無法用來在區域內建立或更新記錄集。 |
> | 動作 | Microsoft.Network/dnszones/delete | 刪除 JSON 格式的 DNS 區域。 區域屬性包含標記、etag、numberOfRecordSets 和 maxNumberOfRecordSets。 |
> |  | **dns 區域/A** |  |
> | 動作 | Microsoft.Network/dnszones/A/read | 獲取類型為"A"的記錄集,採用 JSON 格式。 記錄集包含記錄清單以及 TTL、標記和 etag。 |
> | 動作 | Microsoft.Network/dnszones/A/write | 在 DNS 區域中建立或更新類型為「A」的記錄集。 指定的記錄將會取代記錄集內的目前記錄。 |
> | 動作 | Microsoft.Network/dnszones/A/delete | 從 DNS 區域中刪除給定名稱的記錄集並鍵入" A" |
> |  | **dns區域/AAA** |  |
> | 動作 | Microsoft.Network/dnszones/AAAA/read | 獲取以 JSON 格式的類型"AAAA"的記錄集。 記錄集包含記錄清單以及 TTL、標記和 etag。 |
> | 動作 | Microsoft.Network/dnszones/AAAA/write | 在 DNS 區域中建立或更新類型為「AAAA」的記錄集。 指定的記錄將會取代記錄集內的目前記錄。 |
> | 動作 | Microsoft.Network/dnszones/AAAA/delete | 從 DNS 區域中刪除給定名稱的記錄集並鍵入「AAAA」。 |
> |  | **dnszones/all** |  |
> | 動作 | Microsoft.Network/dnszones/all/read | 取得跨類型的 DNS 記錄集 |
> |  | **dnszones/CAA** |  |
> | 動作 | Microsoft.Network/dnszones/CAA/read | 獲取以 JSON 格式的類型"CAA"的記錄集。 記錄集包含 TTL、標記和 etag。 |
> | 動作 | Microsoft.Network/dnszones/CAA/write | 在 DNS 區域中建立或更新類型為「CAA」的記錄集。 指定的記錄將會取代記錄集內的目前記錄。 |
> | 動作 | Microsoft.Network/dnszones/CAA/delete | 從 DNS 區域中刪除給定名稱的記錄集並鍵入「CAA」。 |
> |  | **dns 區域/CNAME** |  |
> | 動作 | Microsoft.Network/dnszones/CNAME/read | 獲取以 JSON 格式的類型「CNAME」的記錄集。 記錄集包含 TTL、標記和 etag。 |
> | 動作 | Microsoft.Network/dnszones/CNAME/write | 在 DNS 區域中建立或更新類型為「CNAME」的記錄集。 指定的記錄將會取代記錄集內的目前記錄。 |
> | 動作 | Microsoft.Network/dnszones/CNAME/delete | 從 DNS 區域中刪除給定名稱的記錄集並鍵入「CNAME」。。 |
> |  | **dns 區域/MX** |  |
> | 動作 | Microsoft.Network/dnszones/MX/read | 獲取類型為"MX"的記錄集,其格式為 JSON 格式。 記錄集包含記錄清單以及 TTL、標記和 etag。 |
> | 動作 | Microsoft.Network/dnszones/MX/write | 在 DNS 區域中建立或更新類型為"MX"的記錄集。 指定的記錄將會取代記錄集內的目前記錄。 |
> | 動作 | Microsoft.Network/dnszones/MX/delete | 從 DNS 區域中刪除給定名稱的記錄集並鍵入「MX」。 |
> |  | **dnszones/NS** |  |
> | 動作 | Microsoft.Network/dnszones/NS/read | 取得 NS 類型的 DNS 記錄集 |
> | 動作 | Microsoft.Network/dnszones/NS/write | 建立或更新 NS 類型的 DNS 記錄集 |
> | 動作 | Microsoft.Network/dnszones/NS/delete | 刪除 NS 類型的 DNS 記錄集 |
> |  | **dns 區域/PTR** |  |
> | 動作 | Microsoft.Network/dnszones/PTR/read | 獲取以 JSON 格式的類型"PTR"的記錄集。 記錄集包含記錄清單以及 TTL、標記和 etag。 |
> | 動作 | Microsoft.Network/dnszones/PTR/write | 在 DNS 區域中建立或更新類型為「PTR」的記錄集。 指定的記錄將會取代記錄集內的目前記錄。 |
> | 動作 | Microsoft.Network/dnszones/PTR/delete | 從 DNS 區域中刪除給定名稱的記錄集並鍵入"PTR" |
> |  | **dnszones/recordsets** |  |
> | 動作 | Microsoft.Network/dnszones/recordsets/read | 取得跨類型的 DNS 記錄集 |
> |  | **dnszones/SOA** |  |
> | 動作 | Microsoft.Network/dnszones/SOA/read | 取得 SOA 類型的 DNS 記錄集 |
> | 動作 | Microsoft.Network/dnszones/SOA/write | 建立或更新 SOA 類型的 DNS 記錄集 |
> |  | **dns 區域/SRV** |  |
> | 動作 | Microsoft.Network/dnszones/SRV/read | 獲取類型為"SRV"的記錄集,採用 JSON 格式。 記錄集包含記錄清單以及 TTL、標記和 etag。 |
> | 動作 | Microsoft.Network/dnszones/SRV/write | 建立或更新 SRV 類型的記錄集 |
> | 動作 | Microsoft.Network/dnszones/SRV/delete | 從 DNS 區域中刪除給定名稱的記錄集並鍵入「SRV」。 |
> |  | **dns 區域/TXT** |  |
> | 動作 | Microsoft.Network/dnszones/TXT/read | 獲取以 JSON 格式的類型"TXT"的記錄集。 記錄集包含記錄清單以及 TTL、標記和 etag。 |
> | 動作 | Microsoft.Network/dnszones/TXT/write | 在 DNS 區域中建立或更新類型為「TXT」的記錄集。 指定的記錄將會取代記錄集內的目前記錄。 |
> | 動作 | Microsoft.Network/dnszones/TXT/delete | 從 DNS 區域中刪除給定名稱的記錄集並鍵入「TXT」。。 |
> |  | **expressRouteCircuits** |  |
> | 動作 | Microsoft.Network/expressRouteCircuits/read | 取得 ExpressRouteCircuit |
> | 動作 | Microsoft.Network/expressRouteCircuits/write | 建立或更新現有的 ExpressRouteCircuit |
> | 動作 | Microsoft.Network/expressRouteCircuits/join/action | 加入快速路由電路。 不可報警。 |
> | 動作 | Microsoft.Network/expressRouteCircuits/delete | 刪除 ExpressRouteCircuit |
> |  | **快遞電路/授權** |  |
> | 動作 | Microsoft.Network/expressRouteCircuits/authorizations/read | 取得 ExpressRouteCircuit 授權 |
> | 動作 | Microsoft.Network/expressRouteCircuits/authorizations/write | 建立或更新現有的 ExpressRouteCircuit 授權 |
> | 動作 | Microsoft.Network/expressRouteCircuits/authorizations/delete | 刪除 ExpressRouteCircuit 授權 |
> |  | **快速路由電路/對等** |  |
> | 動作 | Microsoft.Network/expressRouteCircuits/peerings/read | 取得 ExpressRouteCircuit 對等互連 |
> | 動作 | Microsoft.Network/expressRouteCircuits/peerings/write | 建立或更新現有的 ExpressRouteCircuit 對等互連 |
> | 動作 | Microsoft.Network/expressRouteCircuits/peerings/delete | 刪除 ExpressRouteCircuit 對等互連 |
> |  | **快速路由電路/對等器/arpTables** |  |
> | 動作 | 微軟.網路/快速路由電路/對等互連/rpTables/讀取 | 取得 ExpressRouteCircuit 對等互連 ArpTable |
> |  | **快速路由電路/對等互連/連接** |  |
> | 動作 | Microsoft.Network/expressRouteCircuits/peerings/connections/read | 取得 ExpressRouteCircuit 連線 |
> | 動作 | Microsoft.Network/expressRouteCircuits/peerings/connections/write | 建立或更新現有的 ExpressRouteCircuit 連線資源 |
> | 動作 | Microsoft.Network/expressRouteCircuits/peerings/connections/delete | 刪除 ExpressRouteCircuit 連線 |
> |  | **快速路由電路/對等互連/對等連接** |  |
> | 動作 | Microsoft.Network/expressRouteCircuits/peerings/peerConnections/read | 取得對等 Express Route 線路連線 |
> |  | **快速路由電路/對等/路由表** |  |
> | 動作 | 微軟.網路/快速路由電路/對等互連/路由表/讀取 | 取得 ExpressRouteCircuit 對等互連 RouteTable |
> |  | **快速路由電路/對等/路由表摘要** |  |
> | 動作 | 微軟.網路/快速路由電路/對等互連/路由表摘要/讀取 | 取得 ExpressRouteCircuit 對等互連 RouteTable 摘要 |
> |  | **快速路由電路/對等/統計** |  |
> | 動作 | Microsoft.Network/expressRouteCircuits/peerings/stats/read | 取得 ExpressRouteCircuit 對等互連統計資料 |
> |  | **快速路由電路/統計** |  |
> | 動作 | Microsoft.Network/expressRouteCircuits/stats/read | 取得 ExpressRouteCircuit 統計資料 |
> |  | **快速路由交叉連線** |  |
> | 動作 | Microsoft.Network/expressRouteCrossConnections/read | 取得快速路由交叉連線 |
> | 動作 | Microsoft.Network/expressRouteCrossConnections/join/action | 加入快速路由交叉連接。 不可報警。 |
> |  | **expressRouteCrossConnections/peerings** |  |
> | 動作 | Microsoft.Network/expressRouteCrossConnections/peerings/read | 取得快速路由交叉連線對等互連 |
> | 動作 | Microsoft.Network/expressRouteCrossConnections/peerings/write | 建立快速路由交叉連線對等互連，或更新現有的快速路由交叉連線對等互連 |
> | 動作 | Microsoft.Network/expressRouteCrossConnections/peerings/delete | 刪除快速路由交叉連線對等互連 |
> |  | **快速路由交叉連接/對等互連/rpTables** |  |
> | 動作 | 微軟.網路/快速路由交叉連接/對等互連/arpTables/讀取 | 取得快速路由交叉連線對等互連 ARP 資料表 |
> |  | **快速路由交叉連接/對等互連/路由表** |  |
> | 動作 | 微軟.網路/快速路由交叉連接/對等互連/路由表/讀取 | 取得快速路由交叉連線對等互連路由表 |
> |  | **快速路由交叉連接/對等互連/路由表摘要** |  |
> | 動作 | 微軟.網路/快速路由交叉連接/對等互連/路由表摘要/讀取 | 取得快速路由交叉連線對等互連路由表摘要 |
> |  | **快遞閘道** |  |
> | 動作 | Microsoft.Network/expressRouteGateways/read | 取得 ExpressRoute 閘道 |
> | 動作 | Microsoft.Network/expressRouteGateways/join/action | 加入快速路由閘道。 不可報警。 |
> |  | **快速路由閘道/快速路由連接** |  |
> | 動作 | Microsoft.Network/expressRouteGateways/expressRouteConnections/read | 取得 ExpressRoute 連線 |
> | 動作 | Microsoft.Network/expressRouteGateways/expressRouteConnections/write | 建立 ExpressRoute 連線，或更新現有的 ExpressRoute 連線 |
> | 動作 | Microsoft.Network/expressRouteGateways/expressRouteConnections/delete | 刪除 ExpressRoute 連線 |
> |  | **快速路由連接埠** |  |
> | 動作 | Microsoft.Network/expressRoutePorts/read | 取得 ExpressRoutePorts |
> | 動作 | Microsoft.Network/expressRoutePorts/write | 建立或更新 ExpressRoutePorts |
> | 動作 | Microsoft.Network/expressRoutePorts/join/action | 聯接快速路由埠。 不可報警。 |
> | 動作 | Microsoft.Network/expressRoutePorts/delete | 刪除 ExpressRoutePorts |
> |  | **快速路由連接埠/連結** |  |
> | 動作 | Microsoft.Network/expressRoutePorts/links/read | 取得 ExpressRouteLink |
> |  | **快速路由連接埠位置** |  |
> | 動作 | Microsoft.Network/expressRoutePortsLocations/read | 取得 Express Route 連接埠位置 |
> |  | **expressRouteServiceProviders** |  |
> | 動作 | Microsoft.Network/expressRouteServiceProviders/read | 取得 ExpressRoute 服務提供者 |
> |  | **防火牆策略** |  |
> | 動作 | 微軟.網路/防火牆策略/讀取 | 取得防火牆原則 |
> | 動作 | 微軟.網路/防火牆策略/寫入 | 建立防火牆原則或更新現有防火牆策略 |
> | 動作 | 微軟.網路/防火牆策略/加入/操作 | 加入防火牆策略。 不可報警。 |
> | 動作 | 微軟.網路/防火牆策略/刪除 | 移除防火牆原則 |
> |  | **防火牆原則/ 規則群組** |  |
> | 動作 | 微軟.網路/防火牆策略/規則組/讀取 | 取得防火牆原則規則群組 |
> | 動作 | 微軟.網路/防火牆策略/規則組/寫入 | 建立防火牆原則規則群組或更新現有的防火牆策略規則群組 |
> | 動作 | 微軟.網路/防火牆策略/規則組/刪除 | 移除防火牆原則規則群組 |
> |  | **前門** |  |
> | 動作 | Microsoft.Network/frontDoors/read | 取得 Front Door |
> | 動作 | Microsoft.Network/frontDoors/write | 建立或更新 Front Door |
> | 動作 | Microsoft.Network/frontDoors/delete | 刪除 Front Door |
> | 動作 | Microsoft.Network/frontDoors/purge/action | 從前端清除快取的內容 |
> | 動作 | Microsoft.Network/frontDoors/validateCustomDomain/action | 驗證 Front Door 的前端端點 |
> |  | **前門/後端池** |  |
> | 動作 | Microsoft.Network/frontDoors/backendPools/read | 取得後端集區 |
> | 動作 | Microsoft.Network/frontDoors/backendPools/write | 建立或更新後端集區 |
> | 動作 | Microsoft.Network/frontDoors/backendPools/delete | 刪除後端集區 |
> |  | **前門/前端點** |  |
> | 動作 | Microsoft.Network/frontDoors/frontendEndpoints/read | 取得前端端點 |
> | 動作 | Microsoft.Network/frontDoors/frontendEndpoints/write | 建立或更新前端端點 |
> | 動作 | Microsoft.Network/frontDoors/frontendEndpoints/delete | 刪除前端端點 |
> | 動作 | Microsoft.Network/frontDoors/frontendEndpoints/enableHttps/action | 啟用前端端點上的 HTTPS |
> | 動作 | Microsoft.Network/frontDoors/frontendEndpoints/disableHttps/action | 停用前端端點上的 HTTPS |
> |  | **前門/健康探測設置** |  |
> | 動作 | Microsoft.Network/frontDoors/healthProbeSettings/read | 取得健康情況探查設定 |
> | 動作 | Microsoft.Network/frontDoors/healthProbeSettings/write | 建立或更新健康情況探查設定 |
> | 動作 | Microsoft.Network/frontDoors/healthProbeSettings/delete | 刪除健康情況探查設定 |
> |  | **前門/負載平衡設置** |  |
> | 動作 | Microsoft.Network/frontDoors/loadBalancingSettings/read | 取得負載平衡設定 |
> | 動作 | Microsoft.Network/frontDoors/loadBalancingSettings/write | 建立或更新負載平衡設定 |
> | 動作 | Microsoft.Network/frontDoors/loadBalancingSettings/delete | 建立或更新負載平衡設定 |
> |  | **前門/路由規則** |  |
> | 動作 | Microsoft.Network/frontDoors/routingRules/read | 取得路由規則 |
> | 動作 | Microsoft.Network/frontDoors/routingRules/write | 建立或更新路由規則 |
> | 動作 | Microsoft.Network/frontDoors/routingRules/delete | 刪除路由規則 |
> |  | **前門Web應用程式防火牆管理規則集** |  |
> | 動作 | 微軟.網路/前門網應用防火牆管理規則集/讀取 | 取得 Web 應用程式防火牆託管規則集 |
> |  | **前門網應用防火牆策略** |  |
> | 動作 | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/read | 取得 Web 應用程式防火牆原則 |
> | 動作 | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/write | 建立或更新 Web 應用程式防火牆原則 |
> | 動作 | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/delete | 刪除 Web 應用程式防火牆原則 |
> | 動作 | 微軟.網路/前門Web應用程式防火牆策略/加入/行動 | 加入 Web 應用程式防火牆策略。 無法報警。 |
> |  | **IP 配置** |  |
> | 動作 | 微軟.網路/ip分配/讀取 | 取得 Ip 配置 |
> | 動作 | 微軟.網路/ip分配/寫入 | 建立 Ip 分配或更新現有 Ip 分配 |
> | 動作 | 微軟.網路/ip分配/刪除 | 移除 Ip 配置 |
> |  | **IP群組** |  |
> | 動作 | 微軟.網路/ip組/讀 | 取得 IpGroup |
> | 動作 | 微軟.網路/ip組/寫 | 建立 IpGroup 或更新現有 IpGroup |
> | 動作 | 微軟.網路/ip組/驗證/操作 | 驗證 IpGroup |
> | 動作 | 微軟.網路/ip組/更新參考/操作 | 更新 IpGroup 中參考 |
> | 動作 | 微軟.網路/ip組/加入/行動 | 加入IpGroup。 不可報警。 |
> | 動作 | 微軟.網路/ip組/刪除 | 移除 IpGroup |
> |  | **loadBalancers** |  |
> | 動作 | Microsoft.Network/loadBalancers/read | 取得負載平衡器定義 |
> | 動作 | Microsoft.Network/loadBalancers/write | 建立負載平衡器，或更新現有的負載平衡器 |
> | 動作 | Microsoft.Network/loadBalancers/delete | 刪除負載平衡器 |
> |  | **負載平衡器/後端位址池** |  |
> | 動作 | Microsoft.Network/loadBalancers/backendAddressPools/read | 取得負載平衡器的後端位址集區定義 |
> | 動作 | Microsoft.Network/loadBalancers/backendAddressPools/join/action | 加入負載均衡器後端位址池。 無法報警。 |
> |  | **負載平衡器/前端IP設定** |  |
> | 動作 | Microsoft.Network/loadBalancers/frontendIPConfigurations/read | 取得負載平衡器的前端 IP 組態定義 |
> | 動作 | Microsoft.Network/loadBalancers/frontendIPConfigurations/join/action | 加入 Load Balancer Frontend IP 設定。 不可報警。 |
> |  | **負載平衡器/入站NatPools** |  |
> | 動作 | Microsoft.Network/loadBalancers/inboundNatPools/read | 取得負載平衡器的輸入 NAT 集區定義 |
> | 動作 | Microsoft.Network/loadBalancers/inboundNatPools/join/action | 加入負載均衡器入站 NAT 池。 不可報警。 |
> |  | **負載平衡器/入站Nat規則** |  |
> | 動作 | Microsoft.Network/loadBalancers/inboundNatRules/read | 取得負載平衡器的輸入 NAT 規則定義 |
> | 動作 | Microsoft.Network/loadBalancers/inboundNatRules/write | 建立負載平衡器的輸入 NAT 規則，或更新現有的負載平衡器輸入 NAT 規則 |
> | 動作 | Microsoft.Network/loadBalancers/inboundNatRules/delete | 刪除負載平衡器的輸入 NAT 規則 |
> | 動作 | Microsoft.Network/loadBalancers/inboundNatRules/join/action | 加入負載均衡器入站 nat 規則。 無法報警。 |
> |  | **負載平衡器/負載平衡規則** |  |
> | 動作 | Microsoft.Network/loadBalancers/loadBalancingRules/read | 取得負載平衡器的負載平衡規則定義 |
> |  | **負載平衡器/網路介面** |  |
> | 動作 | Microsoft.Network/loadBalancers/networkInterfaces/read | 取得負載平衡器下所有網路介面的參考 |
> |  | **負載平衡器/出站規則** |  |
> | 動作 | Microsoft.Network/loadBalancers/outboundRules/read | 取得負載平衡器的輸出規則定義 |
> |  | **負載平衡器/探頭** |  |
> | 動作 | Microsoft.Network/loadBalancers/probes/read | 取得負載平衡器探查 |
> | 動作 | Microsoft.Network/loadBalancers/probes/join/action | 允許使用負載平衡器的探查。 例如，使用此權限，VM 擴展集的 healthProbe 屬性就可以參考探查。 不可報警。 |
> |  | **負載平衡器/虛擬機器** |  |
> | 動作 | Microsoft.Network/loadBalancers/virtualMachines/read | 取得負載平衡器下所有虛擬機器的參考 |
> |  | **本地網路閘道** |  |
> | 動作 | Microsoft.Network/localnetworkgateways/read | 取得 LocalNetworkGateway |
> | 動作 | Microsoft.Network/localnetworkgateways/write | 建立或更新現有的 LocalNetworkGateway |
> | 動作 | Microsoft.Network/localnetworkgateways/delete | 刪除 LocalNetworkGateway |
> |  | **位置** |  |
> | 動作 | Microsoft.Network/locations/checkAcceleratedNetworkingSupport/action | 檢查加速網路支援 |
> | 動作 | 微軟.網路/位置/批量通知私人終結點資源移動/操作 | 批量通知專用終結點資源移動。 |
> | 動作 | 微軟.網路/位置/檢查私人連結服務可見性/操作 | 檢查專用連結服務可見性 |
> | 動作 | Microsoft.Network/locations/bareMetalTenants/action | 配置或驗證裸機租用戶 |
> |  | **位置/自動批准私人連結服務** |  |
> | 動作 | 微軟.網路/位置/自動批准私人連結服務/讀取 | 取得自動批准的專用連結服務 |
> |  | **地點/可用代表團** |  |
> | 動作 | Microsoft.Network/locations/availableDelegations/read | 取得可用的委派 |
> |  | **位置/可用的私人終結點型態** |  |
> | 動作 | 微軟.網路/位置/可用私人終結點類型/讀取 | 取得可用的專用終結點資源 |
> |  | **位置/可用的服務別名** |  |
> | 動作 | 微軟.網路/位置/可用服務別名/讀取 | 取得可用服務別名 |
> |  | **位置/檢查Dns名稱可用性** |  |
> | 動作 | Microsoft.Network/locations/checkDnsNameAvailability/read | 檢查指定的位置是否有 DNS 標籤 |
> |  | **位置/動作結果** |  |
> | 動作 | Microsoft.Network/locations/operationResults/read | 取得非同步 POST 或 DELETE 作業的作業結果 |
> |  | **位置/操作** |  |
> | 動作 | Microsoft.Network/locations/operations/read | 取得代表非同步作業狀態的作業資源 |
> |  | **位置/服務標籤** |  |
> | 動作 | 微軟.網路/位置/服務標籤/讀取 | 取得服務標記 |
> |  | **位置/支援的虛擬電腦大小** |  |
> | 動作 | Microsoft.Network/locations/supportedVirtualMachineSizes/read | 取得支援的虛擬機器大小 |
> |  | **位置/用法** |  |
> | 動作 | Microsoft.Network/locations/usages/read | 取得資源使用量計量 |
> |  | **位置/虛擬網路可用終結點服務** |  |
> | 動作 | Microsoft.Network/locations/virtualNetworkAvailableEndpointServices/read | 取得可用虛擬網路端點服務的清單 |
> |  | **natGateways** |  |
> | 動作 | 微軟.網路/natGateways/加入/行動 | 加入 NAT 閘道 |
> |  | **納特魯爾收集** |  |
> | 動作 | 微軟.網路/natRule集合/讀取 | 取得 Azure 防火牆 NatRule 集合 |
> | 動作 | 微軟.網路/natRule集合/寫入 | 建立或更新 Azure 防火牆 NatRule 收集 |
> | 動作 | 微軟.網路/natRule集合/刪除 | 移除 Azure 防火牆 NatRule 集合 |
> |  | **網路實驗設定檔** |  |
> | 動作 | 微軟.網路/網路實驗配置檔/讀取 | 取得網際網路分析器設定檔 |
> | 動作 | 微軟.網路/網路實驗配置檔/寫入 | 建立或更新網際網路分析器設定檔 |
> | 動作 | 微軟.網路/網路實驗配置檔/刪除 | 刪除網際網路分析器設定檔 |
> |  | **網路實驗設定檔、實驗** |  |
> | 動作 | 微軟.網路/網路實驗配置檔/實驗/閱讀 | 取得網際網路分析儀測試 |
> | 動作 | 微軟.網路/網路實驗配置檔/實驗/寫入 | 建立或更新網際網路分析器測試 |
> | 動作 | 微軟.網路/網路實驗配置檔/實驗/刪除 | 移除網際網路分析器測試 |
> | 動作 | 微軟.網路/網路實驗配置檔/實驗/時間序列/操作 | 取得網際網路分析儀測試的時間序列 |
> | 動作 | 微軟.網路/網路實驗配置檔/實驗/延遲計分卡/操作 | 取得網際網路分析器測試的延遲計分卡 |
> |  | **網路實驗設定檔/預先設定的終結點** |  |
> | 動作 | 微軟.網路/網路實驗配置檔/預先設定的終結點/讀取 | 取得網際網路分析器設定檔的預先設定終結點 |
> |  | **networkIntentPolicies** |  |
> | 動作 | Microsoft.Network/networkIntentPolicies/read | 取得網路意圖原則描述 |
> | 動作 | Microsoft.Network/networkIntentPolicies/write | 建立網路意圖原則或更新現有的網路意圖原則 |
> | 動作 | Microsoft.Network/networkIntentPolicies/delete | 刪除網路意圖原則 |
> |  | **networkInterfaces** |  |
> | 動作 | Microsoft.Network/networkInterfaces/read | 取得網路介面定義。  |
> | 動作 | Microsoft.Network/networkInterfaces/write | 建立網路介面，或更新現有的網路介面。  |
> | 動作 | Microsoft.Network/networkInterfaces/join/action | 將虛擬機加入網路介面。 無法報警。 |
> | 動作 | Microsoft.Network/networkInterfaces/delete | 刪除網路介面 |
> | 動作 | Microsoft.Network/networkInterfaces/effectiveRouteTable/action | 取得 VM 網路介面上所設定的路由表 |
> | 動作 | Microsoft.Network/networkInterfaces/effectiveNetworkSecurityGroups/action | 取得 VM 網路介面上所設定的網路安全性群組 |
> | 動作 | 微軟.網路/網路介面/更新家長附件上彈性/操作 | 更新與彈性 NIC 關聯的父 NIC |
> |  | **網路介面/ip 設定** |  |
> | 動作 | Microsoft.Network/networkInterfaces/ipconfigurations/read | 取得網路介面 IP 組態定義。  |
> | 動作 | Microsoft.Network/networkInterfaces/ipconfigurations/join/action | 加入網路介面 IP 設定。 不可報警。 |
> |  | **網路介面/負載平衡器** |  |
> | 動作 | Microsoft.Network/networkInterfaces/loadBalancers/read | 取得網路介面所屬的所有負載平衡器 |
> |  | **網路介面/點擊設定** |  |
> | 動作 | Microsoft.Network/networkInterfaces/tapConfigurations/read | 取得網路介面 Tap 設定。 |
> | 動作 | Microsoft.Network/networkInterfaces/tapConfigurations/write | 建立網路介面 Tap 設定，或更新現有的網路介面 Tap 設定。 |
> | 動作 | Microsoft.Network/networkInterfaces/tapConfigurations/delete | 刪除網路介面 Tap 設定。 |
> |  | **networkProfiles** |  |
> | 動作 | Microsoft.Network/networkProfiles/read | 取得網路設定檔 |
> | 動作 | Microsoft.Network/networkProfiles/write | 建立或更新網路設定檔 |
> | 動作 | Microsoft.Network/networkProfiles/delete | 刪除網路設定檔 |
> | 動作 | Microsoft.Network/networkProfiles/setContainers/action | 設定容器 |
> | 動作 | Microsoft.Network/networkProfiles/removeContainers/action | 移除容器 |
> | 動作 | Microsoft.Network/networkProfiles/setNetworkInterfaces/action | 設定容器網路介面 |
> |  | **網路規則集合** |  |
> | 動作 | 微軟.網路/網路規則集合/讀取 | 取得 Azure 防火牆網路規則集合 |
> | 動作 | 微軟.網路/網路規則收集/寫入 | 建立或更新 Azure 防火牆網路規則集合 |
> | 動作 | 微軟.網路/網路規則收集/刪除 | 移除 Azure 防火牆網路規則集合 |
> |  | **networkSecurityGroups** |  |
> | 動作 | Microsoft.Network/networkSecurityGroups/read | 取得網路安全性群組定義 |
> | 動作 | Microsoft.Network/networkSecurityGroups/write | 建立網路安全性群組，或更新現有的網路安全性群組 |
> | 動作 | Microsoft.Network/networkSecurityGroups/delete | 刪除網路安全性群組 |
> | 動作 | Microsoft.Network/networkSecurityGroups/join/action | 加入網路安全組。 無法報警。 |
> |  | **網路安全組/預設安全規則** |  |
> | 動作 | Microsoft.Network/networkSecurityGroups/defaultSecurityRules/read | 取得預設的安全性規則定義 |
> |  | **網路安全群組/安全規則** |  |
> | 動作 | Microsoft.Network/networkSecurityGroups/securityRules/read | 取得安全性規則定義 |
> | 動作 | Microsoft.Network/networkSecurityGroups/securityRules/write | 建立安全性規則，或更新現有的安全性規則 |
> | 動作 | Microsoft.Network/networkSecurityGroups/securityRules/delete | 刪除安全性規則 |
> |  | **networkWatchers** |  |
> | 動作 | Microsoft.Network/networkWatchers/read | 取得網路監看員定義 |
> | 動作 | Microsoft.Network/networkWatchers/write | 建立網路監看員，或更新現有的網路監看員 |
> | 動作 | Microsoft.Network/networkWatchers/delete | 刪除網路監看員 |
> | 動作 | Microsoft.Network/networkWatchers/configureFlowLog/action | 設定目標資源的流程記錄。 |
> | 動作 | Microsoft.Network/networkWatchers/ipFlowVerify/action | 傳回是要允許還是拒絕特定目的地的往返封包。 |
> | 動作 | Microsoft.Network/networkWatchers/nextHop/action | 針對指定的目標和目的地 IP 位址，傳回下一個躍點的類型和 IP 位址。 |
> | 動作 | Microsoft.Network/networkWatchers/queryFlowLogStatus/action | 取得資源的流程記錄狀態。 |
> | 動作 | Microsoft.Network/networkWatchers/queryTroubleshootResult/action | 取得上一次執行的疑難排解結果或目前執行的疑難排解作業。 |
> | 動作 | Microsoft.Network/networkWatchers/securityGroupView/action | 檢視對 VM 套用之已設定且生效的網路安全性群組規則。 |
> | 動作 | Microsoft.Network/networkWatchers/networkConfigurationDiagnostic/action | 網路組態的診斷。 |
> | 動作 | Microsoft.Network/networkWatchers/queryConnectionMonitors/action | 批次查詢監視指定端點之間的連線能力 |
> | 動作 | Microsoft.Network/networkWatchers/topology/action | 取得資源的網路層級檢視和資源在資源群組中的關聯性。 |
> | 動作 | Microsoft.Network/networkWatchers/troubleshoot/action | 開始針對 Azure 中的網路資源進行疑難排解。 |
> | 動作 | Microsoft.Network/networkWatchers/connectivityCheck/action | 確認是否可以建立從虛擬機器到包含另一台 VM 或任意遠端伺服器之指定端點的直接 TCP 連線。 |
> | 動作 | Microsoft.Network/networkWatchers/azureReachabilityReport/action | 傳回從指定位置到 Azure 區域之網際網路服務提供者的相對延遲分數。 |
> | 動作 | Microsoft.Network/networkWatchers/availableProvidersList/action | 針對指定的 Azure 區域傳回所有可用的網際網路服務提供者。 |
> |  | **網路觀察器/連接監視器** |  |
> | 動作 | Microsoft.Network/networkWatchers/connectionMonitors/start/action | 開始監視指定端點之間的連線能力 |
> | 動作 | Microsoft.Network/networkWatchers/connectionMonitors/stop/action | 停止/暫停監視指定端點之間的連線能力 |
> | 動作 | Microsoft.Network/networkWatchers/connectionMonitors/query/action | 查詢監視指定端點之間的連線能力 |
> | 動作 | Microsoft.Network/networkWatchers/connectionMonitors/read | 取得連線監視詳細資料 |
> | 動作 | Microsoft.Network/networkWatchers/connectionMonitors/write | 建立連線監視 |
> | 動作 | Microsoft.Network/networkWatchers/connectionMonitors/delete | 刪除連線監視 |
> |  | **網路觀察器/串流紀錄** |  |
> | 動作 | 微軟.網路/網路觀察者/流日誌/讀取 | 取得串流紀錄詳細資訊 |
> | 動作 | 微軟.網路/網路觀察者/流日誌/寫入 | 建立串流紀錄 |
> | 動作 | 微軟.網路/網路觀察者/流日誌/刪除 | 刪除串流紀錄 |
> |  | **networkWatchers/lenses** |  |
> | 動作 | Microsoft.Network/networkWatchers/lenses/start/action | 開始監視指定端點上的網路流量 |
> | 動作 | Microsoft.Network/networkWatchers/lenses/stop/action | 停止/暫停監視指定端點上的網路流量 |
> | 動作 | Microsoft.Network/networkWatchers/lenses/query/action | 查詢監視指定端點上的網路流量 |
> | 動作 | Microsoft.Network/networkWatchers/lenses/read | 取得功能濾鏡詳細資料 |
> | 動作 | Microsoft.Network/networkWatchers/lenses/write | 建立功能濾鏡 |
> | 動作 | Microsoft.Network/networkWatchers/lenses/delete | 刪除功能濾鏡 |
> |  | **網路觀察器/封包擷取** |  |
> | 動作 | Microsoft.Network/networkWatchers/packetCaptures/queryStatus/action | 取得屬性的相關資訊和封包擷取資源的狀態。 |
> | 動作 | Microsoft.Network/networkWatchers/packetCaptures/stop/action | 停止正在執行的封包擷取工作階段。 |
> | 動作 | Microsoft.Network/networkWatchers/packetCaptures/read | 取得封包擷取定義 |
> | 動作 | Microsoft.Network/networkWatchers/packetCaptures/write | 建立封包擷取 |
> | 動作 | Microsoft.Network/networkWatchers/packetCaptures/delete | 刪除封包擷取 |
> |  | **networkWatchers/pingMeshes** |  |
> | 動作 | Microsoft.Network/networkWatchers/pingMeshes/start/action | 在指定的 VM 之間啟動 PingMesh |
> | 動作 | Microsoft.Network/networkWatchers/pingMeshes/stop/action | 在指定的 VM 之間停止 PingMesh |
> | 動作 | Microsoft.Network/networkWatchers/pingMeshes/read | 取得 PingMesh 詳細資料 |
> | 動作 | Microsoft.Network/networkWatchers/pingMeshes/write | 建立 PingMesh |
> | 動作 | Microsoft.Network/networkWatchers/pingMeshes/delete | 刪除 PingMesh |
> |  | **操作** |  |
> | 動作 | Microsoft.Network/operations/read | 取得可用的作業 |
> |  | **p2sVpn 閘道** |  |
> | 動作 | Microsoft.Network/p2sVpnGateways/read | 取得 P2SVpnGateway。 |
> | 動作 | Microsoft.Network/p2sVpnGateways/write | 放置 P2SVpnGateway。 |
> | 動作 | Microsoft.Network/p2sVpnGateways/delete | 刪除 P2SVpnGateway。 |
> | 動作 | Microsoft.Network/p2sVpnGateways/generatevpnprofile/action | 產生 P2SVpnGateway 的 Vpn 設定檔 |
> | 動作 | Microsoft.Network/p2sVpnGateways/getp2svpnconnectionhealth/action | 取得 P2SVpnGateway 的 P2S Vpn 連線健康情況 |
> | 動作 | 微軟.網路/p2sVpn閘道/getp2svpn連接健康詳細/操作 | 取得 P2S Vpn 閘道的詳細 P2S Vpn 連線執行狀況 |
> | 動作 | 微軟.網路/p2sVpn閘道/斷開p2svpn連接/操作 | 斷線 s vpn 連線 |
> |  | **私有Dns操作結果** |  |
> | 動作 | 微軟.網路/私人Dns操作結果/讀取 | 取得專用 DNS 操作的結果 |
> |  | **私有Dns操作狀態** |  |
> | 動作 | 微軟.網路/私人Dns操作狀態/讀取 | 取得專用 DNS 操作的狀態 |
> |  | **私有 Dns 區塊** |  |
> | 動作 | 微軟.網路/私人Dns區/讀取 | 獲取專用 DNS 區域屬性,以 JSON 格式。 請注意,此命令不會檢索專用 DNS 區域連結到的虛擬網路或該區域中包含的記錄集。 |
> | 動作 | 微軟.網路/私有Dns區/寫入 | 在資源組中創建或更新專用 DNS 區域。 請注意,此命令不能用於在區域內創建或更新虛擬網路連結或記錄集。 |
> | 動作 | 微軟.網路/私人Dns區/刪除 | 刪除專用 DNS 區域。 |
> | 動作 | 微軟.網路/專用 Dns 區域/加入/操作 | 加入專用 DNS 區域 |
> |  | **私有 Dns 區域/A** |  |
> | 動作 | 微軟.網路/私有Dns區/A/讀取 | 獲取專用 DNS 區域中鍵入「A」的記錄集,其格式為 JSON 格式。 記錄集包含記錄清單以及 TTL、標記和 etag。 |
> | 動作 | 微軟.網路/私有Dns區/A/寫 | 在專用 DNS 區域中創建或更新類型為「A」的記錄集。 指定的記錄將會取代記錄集內的目前記錄。 |
> | 動作 | 微軟.網路/專用Dns區/A/刪除 | 從專用 DNS 區域中刪除給定名稱的記錄集並鍵入"A" |
> |  | **私有 Dns 區域/AAA** |  |
> | 動作 | 微軟.網路/專用Dns區/AAA/讀取 | 在專用 DNS 區域中以 JSON 格式獲取類型為「AAAA」的記錄集。 記錄集包含記錄清單以及 TTL、標記和 etag。 |
> | 動作 | 微軟.網路/私有Dns區/AAAA/寫 | 在專用 DNS 區域中創建或更新類型為「AAAA」的記錄集。 指定的記錄將會取代記錄集內的目前記錄。 |
> | 動作 | 微軟.網路/專用Dns區/AAA/刪除 | 從專用 DNS 區域中刪除給定名稱的記錄集並鍵入「AAAA」。 |
> |  | **私有 Dns 區域/ALL** |  |
> | 動作 | 微軟.網路/專用 Dns 區域/ALL/讀取 | 跨類型取得專用 DNS 記錄集 |
> |  | **私有 Dns 區域/CNAME** |  |
> | 動作 | 微軟.網路/私人Dns區/CNAME/讀取 | 以 JSON 格式在專用 DNS 區域中獲取類型「CNAME」的記錄集。 |
> | 動作 | 微軟.網路/私人Dns區/CNAME/寫入 | 在專用 DNS 區域中建立或更新類型為「CNAME」的記錄集。 |
> | 動作 | 微軟.網路/私人Dns區/CNAME/刪除 | 從專用 DNS 區域中刪除給定名稱的記錄集並鍵入「CNAME」。。 |
> |  | **專用 Dns 區域/MX** |  |
> | 動作 | 微軟.網路/專用Dns區/MX/讀取 | 獲取專用 DNS 區域內的「MX」類型的記錄集,其格式為 JSON 格式。 記錄集包含記錄清單以及 TTL、標記和 etag。 |
> | 動作 | 微軟.網路/專用Dns區/MX/寫入 | 在專用 DNS 區域中創建或更新類型為"MX"的記錄集。 指定的記錄將會取代記錄集內的目前記錄。 |
> | 動作 | 微軟.網路/專用 Dns 區域/MX/刪除 | 從專用 DNS 區域中刪除給定名稱的記錄集並鍵入"MX"。 |
> |  | **私人 Dns 區域/PTR** |  |
> | 動作 | 微軟.網路/專用Dns區/PTR/讀取 | 獲取專用 DNS 區域內以 JSON 格式的類型"PTR"的記錄集。 記錄集包含記錄清單以及 TTL、標記和 etag。 |
> | 動作 | 微軟.網路/專用Dns區/PTR/寫入 | 在專用 DNS 區域中建立或更新類型為"PTR"的記錄集。 指定的記錄將會取代記錄集內的目前記錄。 |
> | 動作 | 微軟.網路/專用Dns區/PTR/刪除 | 從專用 DNS 區域中刪除給定名稱的記錄集並鍵入"PTR"。 |
> |  | **私有 Dns 區域/記錄集** |  |
> | 動作 | 微軟.網路/專用 Dns 區域/記錄集/讀取 | 跨類型取得專用 DNS 記錄集 |
> |  | **私有 Dns 區域/SOA** |  |
> | 動作 | 微軟.網路/專用Dns區/SOA/讀取 | 以 JSON 格式在專用 DNS 區域中獲取類型為「SOA」的記錄集。 |
> | 動作 | 微軟.網路/專用Dns區/SOA/寫入 | 在專用 DNS 區域中更新類型為"SOA"的記錄集。 |
> |  | **專用 Dns 區/SRV** |  |
> | 動作 | 微軟.網路/專用Dns區/SRV/讀取 | 獲取專用 DNS 區域內的「SRV」類型的記錄集,其格式為 JSON 格式。 記錄集包含記錄清單以及 TTL、標記和 etag。 |
> | 動作 | 微軟.網路/專用Dns區/SRV/寫入 | 在專用 DNS 區域中創建或更新類型為「SRV」的記錄集。 指定的記錄將會取代記錄集內的目前記錄。 |
> | 動作 | 微軟.網路/專用Dns區/SRV/刪除 | 從專用 DNS 區域中刪除給定名稱的記錄集並鍵入「SRV」。 |
> |  | **專用 Dns 區域/TXT** |  |
> | 動作 | 微軟.網路/專用Dns區/TXT/讀取 | 獲取專用 DNS 區域內的「TXT」類型的記錄集,其格式為 JSON 格式。 記錄集包含記錄清單以及 TTL、標記和 etag。 |
> | 動作 | 微軟.網路/專用Dns區/TXT/寫 | 在專用 DNS 區域中創建或更新類型為「TXT」的記錄集。 指定的記錄將會取代記錄集內的目前記錄。 |
> | 動作 | 微軟.網路/專用Dns區/TXT/刪除 | 從專用 DNS 區域中刪除給定名稱的記錄集並鍵入「TXT」。。 |
> |  | **專用 Dns 區域/虛擬網路連結** |  |
> | 動作 | 微軟.網路/專用網路區/虛擬網路鏈接/讀取 | 獲取以 JSON 格式連接到虛擬網路屬性的專用 DNS 區域連結。 |
> | 動作 | 微軟.網路/私人網路區/虛擬網路連結/寫入 | 創建或更新專用 DNS 區域連結到虛擬網路。 |
> | 動作 | 微軟.網路/私人Dns區/虛擬網路連結/刪除 | 刪除指向虛擬網路的專用 DNS 區域連結。 |
> |  | **私有端點重定向圖** |  |
> | 動作 | 微軟.網路/私人端點重定向地圖/閱讀 | 取得專用終結點重定向對應 |
> | 動作 | 微軟.網路/私人端點重定向地圖/寫入 | 建立專用終結點重定向映射或更新現有專用終結點重定向映射 |
> |  | **私有終結點** |  |
> | 動作 | 微軟.網路/私人終端/讀取 | 獲取專用終結點資源。 |
> | 動作 | 微軟.網路/私人終端/寫入 | 創建新的專用終結點,或更新現有專用終結點。 |
> | 動作 | 微軟.網路/私人終結點/刪除 | 刪除專用終結點資源。 |
> |  | **私有終結點/私有 DnsZone 設定** |  |
> | 動作 | 微軟.網路/私人終端/私人DnsZone配置/寫入 | 放置專用 DNS 區域設定 |
> |  | **privateLinkServices** |  |
> | 動作 | Microsoft.Network/privateLinkServices/read | 取得私人連結服務資源。 |
> | 動作 | Microsoft.Network/privateLinkServices/write | 建立新的私人連結服務或更新現有的私人連結服務。 |
> | 動作 | Microsoft.Network/privateLinkServices/delete | 刪除私人連結服務資源。 |
> |  | **專用連結服務/專用端點連接** |  |
> | 動作 | 微軟.網路/私人連結服務/私人端點連接/讀取 | 獲取專用終結點連接定義。 |
> | 動作 | 微軟.網路/私人連結服務/私人端點連接/寫入 | 創建新的專用終結點連接,或更新現有專用終結點連接。 |
> | 動作 | 微軟.網路/私人連結服務/私人端點連接/刪除 | 刪除專用終結點連接。 |
> |  | **公共 IP 位址** |  |
> | 動作 | Microsoft.Network/publicIPAddresses/read | 取得公用 IP 位址定義。 |
> | 動作 | Microsoft.Network/publicIPAddresses/write | 建立公用 IP 位址，或更新現有的公用 IP 位址。  |
> | 動作 | Microsoft.Network/publicIPAddresses/delete | 刪除公用 IP 位址。 |
> | 動作 | Microsoft.Network/publicIPAddresses/join/action | 加入公共 ip 位址。 無法報警。 |
> |  | **publicIPPrefixes** |  |
> | 動作 | Microsoft.Network/publicIPPrefixes/read | 取得公用 IP 首碼定義 |
> | 動作 | Microsoft.Network/publicIPPrefixes/write | 建立公用 IP 首碼，或更新現有的公用 IP 首碼 |
> | 動作 | Microsoft.Network/publicIPPrefixes/delete | 刪除公用 IP 首碼 |
> | 動作 | Microsoft.Network/publicIPPrefixes/join/action | 加入公共 IpPrefix。 不可報警。 |
> |  | **routeFilters** |  |
> | 動作 | Microsoft.Network/routeFilters/read | 取得路由篩選的定義 |
> | 動作 | Microsoft.Network/routeFilters/join/action | 加入路由篩選器。 無法報警。 |
> | 動作 | Microsoft.Network/routeFilters/delete | 刪除路由篩選的定義 |
> | 動作 | Microsoft.Network/routeFilters/write | 建立路由篩選條件，或更新現有的路由篩選條件 |
> |  | **路由篩選器/路由篩選器規則** |  |
> | 動作 | Microsoft.Network/routeFilters/routeFilterRules/read | 取得路由篩選規則的定義 |
> | 動作 | Microsoft.Network/routeFilters/routeFilterRules/write | 建立路由篩選規則，或更新現有的路由篩選規則 |
> | 動作 | Microsoft.Network/routeFilters/routeFilterRules/delete | 刪除路由篩選規則的定義 |
> |  | **routeTables** |  |
> | 動作 | Microsoft.Network/routeTables/read | 取得路由表定義 |
> | 動作 | Microsoft.Network/routeTables/write | 建立路由表或更新現有的路由表 |
> | 動作 | Microsoft.Network/routeTables/delete | 刪除路由表定義 |
> | 動作 | Microsoft.Network/routeTables/join/action | 加入路由表。 無法報警。 |
> |  | **路由表/路由** |  |
> | 動作 | Microsoft.Network/routeTables/routes/read | 取得路由定義 |
> | 動作 | Microsoft.Network/routeTables/routes/write | 建立路由，或更新現有路由 |
> | 動作 | Microsoft.Network/routeTables/routes/delete | 刪除路由定義 |
> |  | **serviceEndpointPolicies** |  |
> | 動作 | Microsoft.Network/serviceEndpointPolicies/read | 取得服務端點原則描述 |
> | 動作 | Microsoft.Network/serviceEndpointPolicies/write | 建立服務端點原則，或更新現有的服務端點原則 |
> | 動作 | Microsoft.Network/serviceEndpointPolicies/delete | 刪除服務端點原則 |
> | 動作 | Microsoft.Network/serviceEndpointPolicies/join/action | 加入服務終結點策略。 不可報警。 |
> | 動作 | Microsoft.Network/serviceEndpointPolicies/joinSubnet/action | 加入子網到服務終結點策略。 不可報警。 |
> |  | **服務端點原則/服務端點原則定義** |  |
> | 動作 | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/read | 取得服務端點原則定義描述 |
> | 動作 | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/write | 建立服務端點原則定義，或更新現有的服務端點原則定義 |
> | 動作 | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/delete | 刪除服務端點原則定義 |
> |  | **trafficManagerGeographicHierarchies** |  |
> | 動作 | Microsoft.Network/trafficManagerGeographicHierarchies/read | 取得流量管理員的地理階層，其所含的區域可用在地理流量路由方法中 |
> |  | **流量管理員設定檔** |  |
> | 動作 | Microsoft.Network/trafficManagerProfiles/read | 取得流量管理員設定檔組態。 這包括 DNS 設定、流量路由設定、端點監視設定，以及此流量管理員設定檔所路由之端點的清單。 |
> | 動作 | Microsoft.Network/trafficManagerProfiles/write | 建立流量管理員設定檔，或修改現有流量管理員設定檔的組態。<br>這包括啟用或停用設定檔以及修改 DNS 設定、流量路由設定或端點監視設定。<br>您可以新增、移除、啟用或停用由流量管理員設定檔所路由傳送的端點。 |
> | 動作 | Microsoft.Network/trafficManagerProfiles/delete | 刪除流量管理員設定檔。 與流量管理員設定檔相關聯的所有設定都會遺失，而且您無法再使用設定檔來路由傳送流量。 |
> |  | **流量管理員設定檔/azure 終結點** |  |
> | 動作 | Microsoft.Network/trafficManagerProfiles/azureEndpoints/read | 取得屬於流量管理員設定檔的 Azure 端點，其中包含該 Azure 端點的所有屬性。 |
> | 動作 | Microsoft.Network/trafficManagerProfiles/azureEndpoints/write | 在現有流量管理員設定檔中新增 Azure 端點，或在該流量管理員設定檔中更新現有 Azure 端點的屬性。 |
> | 動作 | Microsoft.Network/trafficManagerProfiles/azureEndpoints/delete | 從現有的流量管理員設定檔刪除 Azure 端點。 流量管理員將會停止將流量路由傳送到已刪除的 Azure 端點。 |
> |  | **流量管理員設定檔/外部終結點** |  |
> | 動作 | Microsoft.Network/trafficManagerProfiles/externalEndpoints/read | 取得屬於流量管理員設定檔的外部端點，其中包含該外部端點的所有屬性。 |
> | 動作 | Microsoft.Network/trafficManagerProfiles/externalEndpoints/write | 在現有流量管理員設定檔中新增外部端點，或在該流量管理員設定檔中更新現有外部端點的屬性。 |
> | 動作 | Microsoft.Network/trafficManagerProfiles/externalEndpoints/delete | 從現有的流量管理員設定檔刪除外部端點。 流量管理員將會停止將流量路由傳送到已刪除的外部端點。 |
> |  | **交通經理檔案/熱圖** |  |
> | 動作 | Microsoft.Network/trafficManagerProfiles/heatMaps/read | 取得指定流量管理員設定檔的流量管理員熱度圖，其中包含依位置和來源 IP 的查詢計數和延遲資料。 |
> |  | **流量管理員設定檔/嵌套終結點** |  |
> | 動作 | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/read | 取得屬於流量管理員設定檔的巢狀端點，其中包含該巢狀端點的所有屬性。 |
> | 動作 | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/write | 在現有流量管理員設定檔中新增巢狀端點，或在該流量管理員設定檔中更新現有巢狀端點的屬性。 |
> | 動作 | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/delete | 從現有的流量管理員設定檔刪除巢狀端點。 流量管理員將會停止將流量路由傳送到已刪除的巢狀端點。 |
> |  | **流量管理員使用者指標鍵** |  |
> | 動作 | Microsoft.Network/trafficManagerUserMetricsKeys/read | 取得針對即時使用者計量集合使用的訂用帳戶層級金鑰。 |
> | 動作 | Microsoft.Network/trafficManagerUserMetricsKeys/write | 建立要針對即時使用者計量集合使用的新訂用帳戶層級金鑰。 |
> | 動作 | Microsoft.Network/trafficManagerUserMetricsKeys/delete | 刪除針對即時使用者計量集合使用的訂用帳戶層級金鑰。 |
> |  | **virtualHubs** |  |
> | 動作 | Microsoft.Network/virtualHubs/delete | 刪除虛擬中樞 |
> | 動作 | Microsoft.Network/virtualHubs/read | 取得虛擬中樞 |
> | 動作 | Microsoft.Network/virtualHubs/write | 建立或更新虛擬中樞 |
> | 動作 | 微軟.網路/虛擬中心/有效路由/操作 | 取得在虛擬中心設定的有效路由 |
> |  | **虛擬中心/中心虛擬網路連線** |  |
> | 動作 | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/read | 取得 HubVirtualNetworkConnection |
> | 動作 | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/write | 建立或更新 HubVirtualNetworkConnection |
> | 動作 | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/delete | 刪除 HubVirtualNetworkConnection |
> |  | **虛擬Hubs/路由表** |  |
> | 動作 | 微軟.網路/虛擬中心/路由表/讀取 | 取得虛擬HubRouteTableV2 |
> | 動作 | 微軟.網路/虛擬中心/路由表/寫入 | 建立或更新虛擬HubRouteTableV2 |
> | 動作 | 微軟.網路/虛擬中心/路由表/刪除 | 移除虛擬HubRouteTableV2 |
> |  | **virtualnetworkgateways** |  |
> | 動作 | Microsoft.Network/virtualnetworkgateways/supportedvpndevices/action | 列出支援的 VPN 裝置 |
> | 動作 | Microsoft.Network/virtualNetworkGateways/read | 取得 VirtualNetworkGateway |
> | 動作 | Microsoft.Network/virtualNetworkGateways/write | 建立或更新 VirtualNetworkGateway |
> | 動作 | Microsoft.Network/virtualNetworkGateways/delete | 刪除 virtualNetworkGateway |
> | 動作 | microsoft.network/virtualnetworkgateways/generatevpnclientpackage/action | 針對 virtualNetworkGateway 產生 VpnClient 套件 |
> | 動作 | microsoft.network/virtualnetworkgateways/generatevpnprofile/action | 針對 VirtualNetworkGateway 產生 VpnProfile 套件 |
> | 動作 | microsoft.network/virtualnetworkgateways/getvpnclientconnectionhealth/action | 針對 VirtualNetworkGateway 取得每個 VPN 用戶端連線健康情況 |
> | 動作 | 微軟.網路/虛擬網路閘道/斷開虛擬網路閘道連接/操作 | 斷開虛擬網路閘道 vpn 連線 |
> | 動作 | microsoft.network/virtualnetworkgateways/getvpnprofilepackageurl/action | 取得預先產生之 VPN 用戶端設定檔套件的 URL |
> | 動作 | microsoft.network/virtualnetworkgateways/setvpnclientipsecparameters/action | 設定適用於 VirtualNetworkGateway P2S 用戶端的 Vpnclient Ipsec 參數。 |
> | 動作 | microsoft.network/virtualnetworkgateways/getvpnclientipsecparameters/action | 取得適用於 VirtualNetworkGateway P2S 用戶端的 Vpnclient Ipsec 參數。 |
> | 動作 | microsoft.network/virtualnetworkgateways/resetvpnclientsharedkey/action | 重設適用於 VirtualNetworkGateway P2S 用戶端的 Vpnclient 共用金鑰。 |
> | 動作 | microsoft.network/virtualnetworkgateways/reset/action | 重設 virtualNetworkGateway |
> | 動作 | microsoft.network/virtualnetworkgateways/getadvertisedroutes/action | 取得 virtualNetworkGateway 通告路由 |
> | 動作 | microsoft.network/virtualnetworkgateways/getbgppeerstatus/action | 取得 virtualNetworkGateway bgp 對等狀態 |
> | 動作 | microsoft.network/virtualnetworkgateways/getlearnedroutes/action | 取得 virtualnetworkgateway 學習到的路由 |
> | 動作 | 微軟.網路/虛擬網路閘道/啟動封包擷取/操作 | 啟動虛擬網路閘道包捕獲。 |
> | 動作 | 微軟.網路/虛擬網路閘道/停止資料包擷取/操作 | 停止虛擬網路閘道包捕獲。 |
> |  | **虛擬網路閘道/連線** |  |
> | 動作 | microsoft.network/virtualnetworkgateways/connections/read | 取得 VirtualNetworkGatewayConnection |
> |  | **virtualNetworks** |  |
> | 動作 | Microsoft.Network/virtualNetworks/read | 取得虛擬網路定義 |
> | 動作 | Microsoft.Network/virtualNetworks/write | 建立虛擬網路，或更新現有的虛擬網路 |
> | 動作 | Microsoft.Network/virtualNetworks/delete | 刪除虛擬網路 |
> | 動作 | Microsoft.Network/virtualNetworks/peer/action | 讓某個虛擬網路與另一個虛擬網路對等互連 |
> | 動作 | 微軟.網路/虛擬網路/加入/行動 | 加入虛擬網路。 無法報警。 |
> | 動作 | Microsoft.Network/virtualNetworks/BastionHosts/action | 取得虛擬網路中的堡壘主機參考。 |
> |  | **虛擬網路/堡壘主機** |  |
> | 動作 | 微軟.網路/虛擬網路/堡壘主機/預設/操作 | 取得虛擬網路中的堡壘主機參考。 |
> |  | **虛擬網路/檢查 Ip 位址可用性** |  |
> | 動作 | Microsoft.Network/virtualNetworks/checkIpAddressAvailability/read | 檢查指定的虛擬網路中是否有 IP 位址 |
> |  | **虛擬網路/子網** |  |
> | 動作 | Microsoft.Network/virtualNetworks/subnets/read | 取得虛擬網路子網路定義 |
> | 動作 | Microsoft.Network/virtualNetworks/subnets/write | 建立虛擬網路子網路，或更新現有的虛擬網路子網路 |
> | 動作 | Microsoft.Network/virtualNetworks/subnets/delete | 刪除虛擬網路子網路 |
> | 動作 | Microsoft.Network/virtualNetworks/subnets/join/action | 加入虛擬網路。 無法報警。 |
> | 動作 | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | 將資源 (例如，儲存體帳戶或 SQL Database) 加入至子網路。 不可報警。 |
> | 動作 | Microsoft.Network/virtualNetworks/subnets/prepareNetworkPolicies/action | 藉由套用必要的網路原則來準備子網路 |
> | 動作 | 微軟.網路/虛擬網路/子網/未準備網路策略/操作 | 使用移除應用程式的網路原則取消子網 |
> |  | **虛擬網路/子網/上下文服務終結點策略** |  |
> | 動作 | 微軟.網路/虛擬網路/子網/上下文服務終結點策略/讀取 | 取得上下文服務終結點原則 |
> | 動作 | 微軟.網路/虛擬網路/子網/上下文服務終結點策略/寫入 | 建立上下文服務終結點原則或更新現有的上下文服務終結點策略 |
> | 動作 | 微軟.網路/虛擬網路/子網/上下文服務終結點策略/刪除 | 刪除內容服務終結點原則 |
> |  | **虛擬網路/子網/虛擬機器** |  |
> | 動作 | Microsoft.Network/virtualNetworks/subnets/virtualMachines/read | 取得虛擬網路子網路中所有虛擬機器的參考 |
> |  | **虛擬網路/使用方式** |  |
> | 動作 | Microsoft.Network/virtualNetworks/usages/read | 取得虛擬網路中每個子網路的 IP 使用方式 |
> |  | **虛擬網路/虛擬機器** |  |
> | 動作 | Microsoft.Network/virtualNetworks/virtualMachines/read | 取得虛擬網路中所有虛擬機器的參考 |
> |  | **virtualNetworks/virtualNetworkPeerings** |  |
> | 動作 | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/read | 取得虛擬網路對等互連定義 |
> | 動作 | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write | 建立虛擬網路對等互連，或更新現有的虛擬網路對等互連 |
> | 動作 | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/delete | 刪除虛擬網路對等互連 |
> |  | **虛擬網路點擊** |  |
> | 動作 | Microsoft.Network/virtualNetworkTaps/read | 取得 Virtual Network Tap |
> | 動作 | Microsoft.Network/virtualNetworkTaps/join/action | 加入虛擬網路點擊。 無法報警。 |
> | 動作 | Microsoft.Network/virtualNetworkTaps/delete | 刪除Virtual Network Tap |
> | 動作 | Microsoft.Network/virtualNetworkTaps/write | 建立或更新 Virtual Network Tap |
> |  | **虛擬路由器** |  |
> | 動作 | 微軟.網路/虛擬路由器/讀取 | 取得虛擬路由器 |
> | 動作 | 微軟.網路/虛擬路由器/寫入 | 建立虛擬路由器或更新現有虛擬路由器 |
> | 動作 | 微軟.網路/虛擬路由器/刪除 | 移除虛擬路由器 |
> | 動作 | 微軟.網路/虛擬路由器/加入/操作 | 加入虛擬路由器。 不可報警。 |
> |  | **虛擬路由器/對等互連** |  |
> | 動作 | 微軟.網路/虛擬路由器/對等機/讀取 | 取得虛擬路由器對等互連 |
> | 動作 | 微軟.網路/虛擬路由器/對等機/寫入 | 建立虛擬路由器對等互連或更新現有虛擬路由器對等 |
> | 動作 | 微軟.網路/虛擬路由器/對等機/刪除 | 移除虛擬路由器對等互連 |
> |  | **virtualWans** |  |
> | 動作 | Microsoft.Network/virtualWans/delete | 刪除虛擬 WAN |
> | 動作 | Microsoft.Network/virtualWans/read | 取得虛擬 WAN |
> | 動作 | Microsoft.Network/virtualWans/write | 建立或更新虛擬 WAN |
> | 動作 | Microsoft.Network/virtualwans/vpnconfiguration/action | 取得 VPN 設定 |
> | 動作 | 微軟.網路/虛擬萬域/vpn伺服器配置/操作 | 取得虛擬萬Vpn伺服器設定 |
> | 動作 | 微軟.網路/虛擬萬域/生成Vpn配置檔/操作 | 產生虛擬萬Vpn伺服器設定Vpn設定檔 |
> |  | **虛擬萬網域/p2sVpn伺服器設定** |  |
> | 動作 | Microsoft.Network/virtualWans/p2sVpnServerConfigurations/read | 取得虛擬 Wan P2SVpnServerConfiguration |
> | 動作 | Microsoft.network/virtualWans/p2sVpnServerConfigurations/write | 建立虛擬 Wan P2SVpnServerConfiguration 或更新現有的虛擬 Wan P2SVpnServerConfiguration |
> | 動作 | Microsoft.network/virtualWans/p2sVpnServerConfigurations/delete | 刪除虛擬 Wan P2SVpnServerConfiguration |
> |  | **虛擬萬網域/受支援的安全提供者** |  |
> | 動作 | Microsoft.Network/virtualwans/supportedSecurityProviders/read | 取得支援的 VirtualWan 安全性提供者。 |
> |  | **虛擬萬網域/虛擬中心** |  |
> | 動作 | Microsoft.Network/virtualWans/virtualHubs/read | 取得所有參考虛擬 WAN 的虛擬中樞。 |
> |  | **虛擬萬網域/vpnSites** |  |
> | 動作 | Microsoft.Network/virtualWans/vpnSites/read | 取得所有參考虛擬 WAN 的 VPN 網站。 |
> |  | **vpn閘道** |  |
> | 動作 | Microsoft.Network/vpnGateways/read | 取得 VpnGateway。 |
> | 動作 | Microsoft.Network/vpnGateways/write | 放置 VpnGateway。 |
> | 動作 | Microsoft.Network/vpnGateways/delete | 刪除 VpnGateway。 |
> | 動作 | microsoft.network/vpngateways/reset/action | 重設 VpnGateway |
> | 動作 | microsoft.network/vpngateways/listvpnconnectionshealth/action | 取得 VpnGateway 上所有或部分連線的連線健康情況 |
> |  | **vpn閘道/vpn連接** |  |
> | 動作 | microsoft.network/vpnGateways/vpnConnections/read | 取得 VpnConnection。 |
> | 動作 | microsoft.network/vpnGateways/vpnConnections/write | 放置 VpnConnection。 |
> | 動作 | microsoft.network/vpnGateways/vpnConnections/delete | 刪除 VpnConnection。 |
> |  | **vpn閘道/vpn連接/vpn連結連接** |  |
> | 動作 | 微軟.網路/vpn閘道/vpn連接/vpn連結連接/讀取 | 取得 Vpn 連結連線 |
> |  | **vpnServer 設定** |  |
> | 動作 | 微軟.網路/vpn伺服器設定/讀取 | 取得 VpnServer 設定 |
> | 動作 | 微軟.網路/vpn伺服器設定/寫入 | 建立或更新 VpnServer 設定 |
> | 動作 | 微軟.網路/vpn伺服器設定/刪除 | 移除 VpnServer 設定 |
> |  | **vpnsites** |  |
> | 動作 | Microsoft.Network/vpnsites/read | 取得 VPN 網站資源。 |
> | 動作 | Microsoft.Network/vpnsites/write | 建立或更新 VPN 網站資源。 |
> | 動作 | Microsoft.Network/vpnsites/delete | 刪除 VPN 網站資源。 |
> |  | **vpn網站/vpn網站連結** |  |
> | 動作 | 微軟.網路/vpnSites/vpnSite鏈接/閱讀 | 取得 Vpn 網站連結 |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

Azure 服務:[通知中心](../notification-hubs/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.NotificationHubs/register/action | 針對 NotifciationHubs 資源提供者註冊訂用帳戶，並讓您能夠建立命名空間和 NotificationHubs |
> | 動作 | Microsoft.NotificationHubs/unregister/action | 針對 NotifciationHubs 資源提供者取消註冊訂用帳戶，並讓您能夠建立命名空間和 NotificationHubs |
> | 動作 | Microsoft.NotificationHubs/CheckNamespaceAvailability/action | 檢查在 NotificationHub 服務內，給定的命名空間資源名稱是否可供使用。 |
> |  | **命名空間** |  |
> | 動作 | Microsoft.NotificationHubs/Namespaces/write | 建立命名空間資源，並更新其屬性。 命名空間的標記和容量是可以更新的屬性。 |
> | 動作 | Microsoft.NotificationHubs/Namespaces/read | 取得命名空間資源描述的清單 |
> | 動作 | Microsoft.NotificationHubs/Namespaces/Delete | 刪除命名空間資源 |
> | 動作 | Microsoft.NotificationHubs/Namespaces/authorizationRules/action | 取得命名空間授權規則描述的清單。 |
> | 動作 | Microsoft.NotificationHubs/Namespaces/CheckNotificationHubAvailability/action | 檢查在命名空間內，給定的 NotificationHub 名稱是否可供使用。 |
> |  | **命名空間/授權規則** |  |
> | 動作 | Microsoft.NotificationHubs/Namespaces/authorizationRules/write | 建立命名空間層級的授權規則，並更新其屬性。 您可以更新授權規則的存取權限、主要金鑰和次要金鑰。 |
> | 動作 | Microsoft.NotificationHubs/Namespaces/authorizationRules/read | 取得命名空間授權規則描述的清單。 |
> | 動作 | Microsoft.NotificationHubs/Namespaces/authorizationRules/delete | 刪除命名空間授權規則。 您無法刪除預設的命名空間授權規則。  |
> | 動作 | Microsoft.NotificationHubs/Namespaces/authorizationRules/listkeys/action | 取得命名空間的連接字串 |
> | 動作 | Microsoft.NotificationHubs/Namespaces/authorizationRules/regenerateKeys/action | 命名空間授權規則會重新產生主要/次要金鑰，指定需要重新產生的金鑰 |
> |  | **命名空間/通知中心** |  |
> | 動作 | Microsoft.NotificationHubs/Namespaces/NotificationHubs/write | 建立通知中樞，並更新其屬性。 其屬性主要包括 PNS 認證。 授權規則與 TTL |
> | 動作 | Microsoft.NotificationHubs/Namespaces/NotificationHubs/read | 取得通知中樞資源描述的清單 |
> | 動作 | Microsoft.NotificationHubs/Namespaces/NotificationHubs/Delete | 刪除通知中樞資源 |
> | 動作 | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/action | 取得通知中樞授權規則的清單 |
> | 動作 | Microsoft.NotificationHubs/Namespaces/NotificationHubs/pnsCredentials/action | 取得所有通知中樞的 PNS 認證。 這包括 WNS、MPNS、APNS、GCM 和百度認證 |
> | 動作 | Microsoft.NotificationHubs/Namespaces/NotificationHubs/debugSend/action | 傳送測試推播通知。 |
> |  | **命名空間/通知中心/授權規則** |  |
> | 動作 | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/write | 建立通知中樞授權規則，並更新其屬性。 您可以更新授權規則的存取權限、主要金鑰和次要金鑰。 |
> | 動作 | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/read | 取得通知中樞授權規則的清單 |
> | 動作 | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/delete | 刪除通知中樞的授權規則 |
> | 動作 | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/listkeys/action | 取得通知中樞的連接字串 |
> | 動作 | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/regenerateKeys/action | 通知中樞授權規則會重新產生主要/次要金鑰，指定需要重新產生的金鑰 |
> |  | **命名空間/通知中心/指標定義** |  |
> | 動作 | Microsoft.NotificationHubs/Namespaces/NotificationHubs/metricDefinitions/read | 取得命名空間計量資源描述的清單 |
> |  | **動作結果** |  |
> | 動作 | Microsoft.NotificationHubs/operationResults/read | 傳回通知中樞提供者的作業結果 |
> |  | **操作** |  |
> | 動作 | Microsoft.NotificationHubs/operations/read | 傳回通知中樞提供者支援的作業清單 |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

Azure 服務[:Azure 移轉](../migrate/migrate-services-overview.md)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.OffAzure/register/action | 向 Microsoft.OffAzure 資源提供者註冊訂用帳戶 |
> | 動作 | Microsoft.OffAzure/register/action | 向 Microsoft.OffAzure 資源提供者註冊訂用帳戶 |
> | 動作 | Microsoft.OffAzure/register/action | 向 Microsoft.OffAzure 資源提供者註冊訂用帳戶 |
> | 動作 | Microsoft.OffAzure/register/action | 向 Microsoft.OffAzure 資源提供者註冊訂用帳戶 |
> |  | **超級網站** |  |
> | 動作 | Microsoft.OffAzure/HyperVSites/read | 取得 Hyper-V 站台的屬性 |
> | 動作 | Microsoft.OffAzure/HyperVSites/write | 建立或更新 Hyper-V 站台 |
> | 動作 | Microsoft.OffAzure/HyperVSites/delete | 刪除 Hyper-V 站台 |
> | 動作 | Microsoft.OffAzure/HyperVSites/refresh/action | 重新整理 Hyper-V 站台內的物件 |
> | 動作 | Microsoft.OffAzure/HyperVSites/read | 取得 Hyper-V 站台的屬性 |
> | 動作 | Microsoft.OffAzure/HyperVSites/write | 建立或更新 Hyper-V 站台 |
> | 動作 | Microsoft.OffAzure/HyperVSites/delete | 刪除 Hyper-V 站台 |
> | 動作 | Microsoft.OffAzure/HyperVSites/refresh/action | 重新整理 Hyper-V 站台內的物件 |
> | 動作 | Microsoft.OffAzure/HyperVSites/read | 取得 Hyper-V 站台的屬性 |
> | 動作 | Microsoft.OffAzure/HyperVSites/write | 建立或更新 Hyper-V 站台 |
> | 動作 | Microsoft.OffAzure/HyperVSites/delete | 刪除 Hyper-V 站台 |
> | 動作 | Microsoft.OffAzure/HyperVSites/refresh/action | 重新整理 Hyper-V 站台內的物件 |
> | 動作 | Microsoft.OffAzure/HyperVSites/read | 取得 Hyper-V 站台的屬性 |
> | 動作 | Microsoft.OffAzure/HyperVSites/write | 建立或更新 Hyper-V 站台 |
> | 動作 | Microsoft.OffAzure/HyperVSites/delete | 刪除 Hyper-V 站台 |
> | 動作 | Microsoft.OffAzure/HyperVSites/refresh/action | 重新整理 Hyper-V 站台內的物件 |
> |  | **超級網站/群集** |  |
> | 動作 | Microsoft.OffAzure/HyperVSites/clusters/read | 取得 Hyper-V 叢集的屬性 |
> | 動作 | Microsoft.OffAzure/HyperVSites/clusters/write | 建立或更新 Hyper-V 叢集 |
> | 動作 | Microsoft.OffAzure/HyperVSites/clusters/read | 取得 Hyper-V 叢集的屬性 |
> | 動作 | Microsoft.OffAzure/HyperVSites/clusters/write | 建立或更新 Hyper-V 叢集 |
> | 動作 | Microsoft.OffAzure/HyperVSites/clusters/read | 取得 Hyper-V 叢集的屬性 |
> | 動作 | Microsoft.OffAzure/HyperVSites/clusters/write | 建立或更新 Hyper-V 叢集 |
> | 動作 | Microsoft.OffAzure/HyperVSites/clusters/read | 取得 Hyper-V 叢集的屬性 |
> | 動作 | Microsoft.OffAzure/HyperVSites/clusters/write | 建立或更新 Hyper-V 叢集 |
> |  | **超V網站/運行狀況摘要** |  |
> | 動作 | 微軟.關閉Azure/超網站/健康摘要/閱讀 | 取得超 V 資源的執行狀況摘要 |
> | 動作 | 微軟.關閉Azure/超網站/健康摘要/閱讀 | 取得超 V 資源的執行狀況摘要 |
> | 動作 | 微軟.關閉Azure/超網站/健康摘要/閱讀 | 取得超 V 資源的執行狀況摘要 |
> | 動作 | 微軟.關閉Azure/超網站/健康摘要/閱讀 | 取得超 V 資源的執行狀況摘要 |
> |  | **超級網站/主機** |  |
> | 動作 | Microsoft.OffAzure/HyperVSites/hosts/read | 取得 Hyper-V 主機的屬性 |
> | 動作 | Microsoft.OffAzure/HyperVSites/hosts/write | 建立或更新 Hyper-V 主機 |
> | 動作 | Microsoft.OffAzure/HyperVSites/hosts/read | 取得 Hyper-V 主機的屬性 |
> | 動作 | Microsoft.OffAzure/HyperVSites/hosts/write | 建立或更新 Hyper-V 主機 |
> | 動作 | Microsoft.OffAzure/HyperVSites/hosts/read | 取得 Hyper-V 主機的屬性 |
> | 動作 | Microsoft.OffAzure/HyperVSites/hosts/write | 建立或更新 Hyper-V 主機 |
> | 動作 | Microsoft.OffAzure/HyperVSites/hosts/read | 取得 Hyper-V 主機的屬性 |
> | 動作 | Microsoft.OffAzure/HyperVSites/hosts/write | 建立或更新 Hyper-V 主機 |
> |  | **超級網站/工作** |  |
> | 動作 | Microsoft.OffAzure/HyperVSites/jobs/read | 取得 Hyper-V 作業的屬性 |
> | 動作 | Microsoft.OffAzure/HyperVSites/jobs/read | 取得 Hyper-V 作業的屬性 |
> | 動作 | Microsoft.OffAzure/HyperVSites/jobs/read | 取得 Hyper-V 作業的屬性 |
> | 動作 | Microsoft.OffAzure/HyperVSites/jobs/read | 取得 Hyper-V 作業的屬性 |
> |  | **超級網站/機器** |  |
> | 動作 | Microsoft.OffAzure/HyperVSites/machines/read | 取得 Hyper-V 機器的屬性 |
> | 動作 | Microsoft.OffAzure/HyperVSites/machines/read | 取得 Hyper-V 機器的屬性 |
> | 動作 | Microsoft.OffAzure/HyperVSites/machines/read | 取得 Hyper-V 機器的屬性 |
> | 動作 | Microsoft.OffAzure/HyperVSites/machines/read | 取得 Hyper-V 機器的屬性 |
> |  | **超級網站/操作狀態** |  |
> | 動作 | Microsoft.OffAzure/HyperVSites/operationsstatus/read | 取得 Hyper-V 作業狀態的屬性 |
> | 動作 | Microsoft.OffAzure/HyperVSites/operationsstatus/read | 取得 Hyper-V 作業狀態的屬性 |
> | 動作 | Microsoft.OffAzure/HyperVSites/operationsstatus/read | 取得 Hyper-V 作業狀態的屬性 |
> | 動作 | Microsoft.OffAzure/HyperVSites/operationsstatus/read | 取得 Hyper-V 作業狀態的屬性 |
> |  | **超級VSites/執行帳戶** |  |
> | 動作 | Microsoft.OffAzure/HyperVSites/runasaccounts/read | 取得 Hyper-V 執行身分帳戶的屬性 |
> | 動作 | Microsoft.OffAzure/HyperVSites/runasaccounts/read | 取得 Hyper-V 執行身分帳戶的屬性 |
> | 動作 | Microsoft.OffAzure/HyperVSites/runasaccounts/read | 取得 Hyper-V 執行身分帳戶的屬性 |
> | 動作 | Microsoft.OffAzure/HyperVSites/runasaccounts/read | 取得 Hyper-V 執行身分帳戶的屬性 |
> |  | **超級網站/摘要** |  |
> | 動作 | 微軟.關閉Azure/超網站/摘要/閱讀 | 取得超 V 網站的摘要 |
> | 動作 | 微軟.關閉Azure/超網站/摘要/閱讀 | 取得超 V 網站的摘要 |
> | 動作 | 微軟.關閉Azure/超網站/摘要/閱讀 | 取得超 V 網站的摘要 |
> | 動作 | 微軟.關閉Azure/超網站/摘要/閱讀 | 取得超 V 網站的摘要 |
> |  | **超級網站/使用** |  |
> | 動作 | Microsoft.OffAzure/HyperVSites/usage/read | 取得 Hyper-V 站台的使用情形 |
> | 動作 | Microsoft.OffAzure/HyperVSites/usage/read | 取得 Hyper-V 站台的使用情形 |
> | 動作 | Microsoft.OffAzure/HyperVSites/usage/read | 取得 Hyper-V 站台的使用情形 |
> | 動作 | Microsoft.OffAzure/HyperVSites/usage/read | 取得 Hyper-V 站台的使用情形 |
> |  | **匯入網站** |  |
> | 動作 | 微軟.關閉Azure/導入網站/讀取 | 取得匯入網站屬性 |
> | 動作 | 微軟.關閉Azure/導入網站/寫入 | 建立或更新匯入網站 |
> | 動作 | 微軟.關閉Azure/導入網站/刪除 | 移除匯入網站 |
> | 動作 | 微軟.關閉Azure/導入網站/導入/操作 | 獲取用於導入電腦 CSV 檔案的 SAS uri。 |
> | 動作 | 微軟.關閉Azure/導入網站/匯出/操作 | 獲取用於匯出電腦 CSV 檔案的 SAS uri。 |
> | 動作 | 微軟.關閉Azure/導入網站/讀取 | 取得匯入網站屬性 |
> | 動作 | 微軟.關閉Azure/導入網站/寫入 | 建立或更新匯入網站 |
> | 動作 | 微軟.關閉Azure/導入網站/刪除 | 移除匯入網站 |
> | 動作 | 微軟.關閉Azure/導入網站/導入/操作 | 獲取用於導入電腦 CSV 檔案的 SAS uri。 |
> | 動作 | 微軟.關閉Azure/導入網站/匯出/操作 | 獲取用於匯出電腦 CSV 檔案的 SAS uri。 |
> | 動作 | 微軟.關閉Azure/導入網站/讀取 | 取得匯入網站屬性 |
> | 動作 | 微軟.關閉Azure/導入網站/寫入 | 建立或更新匯入網站 |
> | 動作 | 微軟.關閉Azure/導入網站/刪除 | 移除匯入網站 |
> | 動作 | 微軟.關閉Azure/導入網站/導入/操作 | 獲取用於導入電腦 CSV 檔案的 SAS uri。 |
> | 動作 | 微軟.關閉Azure/導入網站/匯出/操作 | 獲取用於匯出電腦 CSV 檔案的 SAS uri。 |
> | 動作 | 微軟.關閉Azure/導入網站/讀取 | 取得匯入網站屬性 |
> | 動作 | 微軟.關閉Azure/導入網站/寫入 | 建立或更新匯入網站 |
> | 動作 | 微軟.關閉Azure/導入網站/刪除 | 移除匯入網站 |
> | 動作 | 微軟.關閉Azure/導入網站/導入/操作 | 獲取用於導入電腦 CSV 檔案的 SAS uri。 |
> | 動作 | 微軟.關閉Azure/導入網站/匯出/操作 | 獲取用於匯出電腦 CSV 檔案的 SAS uri。 |
> |  | **匯入網站/工作** |  |
> | 動作 | 微軟.關閉Azure/導入網站/作業/讀取 | 取得匯入工作屬性 |
> | 動作 | 微軟.關閉Azure/導入網站/作業/讀取 | 取得匯入工作屬性 |
> | 動作 | 微軟.關閉Azure/導入網站/作業/讀取 | 取得匯入工作屬性 |
> | 動作 | 微軟.關閉Azure/導入網站/作業/讀取 | 取得匯入工作屬性 |
> |  | **匯入網站/電腦** |  |
> | 動作 | 微軟.關閉Azure/導入網站/計算機/讀取 | 取得匯入電腦屬性 |
> | 動作 | 微軟.關閉Azure/導入網站/電腦/刪除 | 移除匯入電腦 |
> | 動作 | 微軟.關閉Azure/導入網站/計算機/讀取 | 取得匯入電腦屬性 |
> | 動作 | 微軟.關閉Azure/導入網站/電腦/刪除 | 移除匯入電腦 |
> | 動作 | 微軟.關閉Azure/導入網站/計算機/讀取 | 取得匯入電腦屬性 |
> | 動作 | 微軟.關閉Azure/導入網站/電腦/刪除 | 移除匯入電腦 |
> | 動作 | 微軟.關閉Azure/導入網站/計算機/讀取 | 取得匯入電腦屬性 |
> | 動作 | 微軟.關閉Azure/導入網站/電腦/刪除 | 移除匯入電腦 |
> |  | **作業** |  |
> | 動作 | Microsoft.OffAzure/Operations/read | 讀取公開的作業 |
> |  | **伺服器網站** |  |
> | 動作 | 微軟.關閉Azure/伺服器網站/讀取 | 取得伺服器站台的屬性 |
> | 動作 | 微軟.關閉Azure/伺服器網站/寫入 | 建立或更新伺服器站台 |
> | 動作 | 微軟.關閉Azure/伺服器網站/刪除 | 移除伺服器站台 |
> | 動作 | 微軟.關閉Azure/伺服器網站/刷新/操作 | 重新整理伺服器站台的物件 |
> | 動作 | 微軟.關閉Azure/伺服器網站/讀取 | 取得伺服器站台的屬性 |
> | 動作 | 微軟.關閉Azure/伺服器網站/寫入 | 建立或更新伺服器站台 |
> | 動作 | 微軟.關閉Azure/伺服器網站/刪除 | 移除伺服器站台 |
> | 動作 | 微軟.關閉Azure/伺服器網站/刷新/操作 | 重新整理伺服器站台的物件 |
> | 動作 | 微軟.關閉Azure/伺服器網站/讀取 | 取得伺服器站台的屬性 |
> | 動作 | 微軟.關閉Azure/伺服器網站/寫入 | 建立或更新伺服器站台 |
> | 動作 | 微軟.關閉Azure/伺服器網站/刪除 | 移除伺服器站台 |
> | 動作 | 微軟.關閉Azure/伺服器網站/刷新/操作 | 重新整理伺服器站台的物件 |
> | 動作 | 微軟.關閉Azure/伺服器網站/讀取 | 取得伺服器站台的屬性 |
> | 動作 | 微軟.關閉Azure/伺服器網站/寫入 | 建立或更新伺服器站台 |
> | 動作 | 微軟.關閉Azure/伺服器網站/刪除 | 移除伺服器站台 |
> | 動作 | 微軟.關閉Azure/伺服器網站/刷新/操作 | 重新整理伺服器站台的物件 |
> |  | **伺服器網站/工作** |  |
> | 動作 | 微軟.關閉Azure/伺服器網站/作業/閱讀 | 取得伺服器工作的屬性 |
> | 動作 | 微軟.關閉Azure/伺服器網站/作業/閱讀 | 取得伺服器工作的屬性 |
> | 動作 | 微軟.關閉Azure/伺服器網站/作業/閱讀 | 取得伺服器工作的屬性 |
> | 動作 | 微軟.關閉Azure/伺服器網站/作業/閱讀 | 取得伺服器工作的屬性 |
> |  | **伺服器網站/電腦** |  |
> | 動作 | 微軟.關閉Azure/伺服器網站/電腦/讀取 | 取得伺服器電腦屬性 |
> | 動作 | 微軟.關閉Azure/伺服器網站/計算機/寫入 | 編寫伺服器電腦的屬性 |
> | 動作 | 微軟.關閉Azure/伺服器網站/電腦/讀取 | 取得伺服器電腦屬性 |
> | 動作 | 微軟.關閉Azure/伺服器網站/計算機/寫入 | 編寫伺服器電腦的屬性 |
> | 動作 | 微軟.關閉Azure/伺服器網站/電腦/讀取 | 取得伺服器電腦屬性 |
> | 動作 | 微軟.關閉Azure/伺服器網站/計算機/寫入 | 編寫伺服器電腦的屬性 |
> | 動作 | 微軟.關閉Azure/伺服器網站/電腦/讀取 | 取得伺服器電腦屬性 |
> | 動作 | 微軟.關閉Azure/伺服器網站/計算機/寫入 | 編寫伺服器電腦的屬性 |
> |  | **伺服器網站/操作狀態** |  |
> | 動作 | 微軟.關閉Azure/伺服器網站/操作狀態/讀取 | 取得伺服器操作狀態的屬性 |
> | 動作 | 微軟.關閉Azure/伺服器網站/操作狀態/讀取 | 取得伺服器操作狀態的屬性 |
> | 動作 | 微軟.關閉Azure/伺服器網站/操作狀態/讀取 | 取得伺服器操作狀態的屬性 |
> | 動作 | 微軟.關閉Azure/伺服器網站/操作狀態/讀取 | 取得伺服器操作狀態的屬性 |
> |  | **伺服器網站/執行帳戶** |  |
> | 動作 | 微軟.關閉Azure/伺服器網站/運行帳戶/讀取 | 取得伺服器作為帳戶執行的屬性 |
> | 動作 | 微軟.關閉Azure/伺服器網站/運行帳戶/讀取 | 取得伺服器作為帳戶執行的屬性 |
> | 動作 | 微軟.關閉Azure/伺服器網站/運行帳戶/讀取 | 取得伺服器作為帳戶執行的屬性 |
> | 動作 | 微軟.關閉Azure/伺服器網站/運行帳戶/讀取 | 取得伺服器作為帳戶執行的屬性 |
> |  | **伺服器網站/摘要** |  |
> | 動作 | 微軟.關閉Azure/伺服器網站/摘要/閱讀 | 取得伺服器網站的摘要 |
> | 動作 | 微軟.關閉Azure/伺服器網站/摘要/閱讀 | 取得伺服器網站的摘要 |
> | 動作 | 微軟.關閉Azure/伺服器網站/摘要/閱讀 | 取得伺服器網站的摘要 |
> | 動作 | 微軟.關閉Azure/伺服器網站/摘要/閱讀 | 取得伺服器網站的摘要 |
> |  | **伺服器網站/使用方式** |  |
> | 動作 | 微軟.關閉Azure/伺服器網站/使用/讀取 | 取得伺服器站台的用法 |
> | 動作 | 微軟.關閉Azure/伺服器網站/使用/讀取 | 取得伺服器站台的用法 |
> | 動作 | 微軟.關閉Azure/伺服器網站/使用/讀取 | 取得伺服器站台的用法 |
> | 動作 | 微軟.關閉Azure/伺服器網站/使用/讀取 | 取得伺服器站台的用法 |
> |  | **VMwareSites** |  |
> | 動作 | Microsoft.OffAzure/VMwareSites/read | 取得 VMware 站台的屬性 |
> | 動作 | Microsoft.OffAzure/VMwareSites/write | 建立或更新 VMware 站台 |
> | 動作 | Microsoft.OffAzure/VMwareSites/delete | 刪除 VMware 站台 |
> | 動作 | Microsoft.OffAzure/VMwareSites/refresh/action | 重新整理 VMware 站台內的物件 |
> | 動作 | 微軟.關閉Azure/VMware網站/匯出應用程式/操作 | 將 VMware 應用程式和角色資料匯出到 xls |
> | 動作 | 微軟.關閉Azure/VMware網站/更新屬性/操作 | 更新網站中電腦的屬性 |
> | 動作 | 微軟.關閉Azure/VMware網站/生成粗糙映射/操作 | 為電腦清單產生粗映射 |
> | 動作 | 微軟.關閉Azure/VMware網站/生成詳細地圖/操作 | 產生詳細的 VMware 粗糙地圖 |
> | 動作 | 微軟.關閉Azure/VMware網站/用戶端組成員/操作 | 列出所選客戶端組的客戶端組成員。 |
> | 動作 | 微軟.關閉Azure/VMware網站/伺服器組成員/操作 | 列出所選伺服器組的伺服器組成員。 |
> | 動作 | 微軟.關閉Azure/VMware網站/獲取應用程式/操作 | 取得選取的電腦應用程式資訊 |
> | 動作 | Microsoft.OffAzure/VMwareSites/read | 取得 VMware 站台的屬性 |
> | 動作 | Microsoft.OffAzure/VMwareSites/write | 建立或更新 VMware 站台 |
> | 動作 | Microsoft.OffAzure/VMwareSites/delete | 刪除 VMware 站台 |
> | 動作 | Microsoft.OffAzure/VMwareSites/refresh/action | 重新整理 VMware 站台內的物件 |
> | 動作 | 微軟.關閉Azure/VMware網站/匯出應用程式/操作 | 將 VMware 應用程式和角色資料匯出到 xls |
> | 動作 | 微軟.關閉Azure/VMware網站/更新屬性/操作 | 更新網站中電腦的屬性 |
> | 動作 | 微軟.關閉Azure/VMware網站/生成粗糙映射/操作 | 為電腦清單產生粗映射 |
> | 動作 | 微軟.關閉Azure/VMware網站/生成詳細地圖/操作 | 產生詳細的 VMware 粗糙地圖 |
> | 動作 | 微軟.關閉Azure/VMware網站/用戶端組成員/操作 | 列出所選客戶端組的客戶端組成員。 |
> | 動作 | 微軟.關閉Azure/VMware網站/伺服器組成員/操作 | 列出所選伺服器組的伺服器組成員。 |
> | 動作 | 微軟.關閉Azure/VMware網站/獲取應用程式/操作 | 取得選取的電腦應用程式資訊 |
> | 動作 | Microsoft.OffAzure/VMwareSites/read | 取得 VMware 站台的屬性 |
> | 動作 | Microsoft.OffAzure/VMwareSites/write | 建立或更新 VMware 站台 |
> | 動作 | Microsoft.OffAzure/VMwareSites/delete | 刪除 VMware 站台 |
> | 動作 | Microsoft.OffAzure/VMwareSites/refresh/action | 重新整理 VMware 站台內的物件 |
> | 動作 | 微軟.關閉Azure/VMware網站/匯出應用程式/操作 | 將 VMware 應用程式和角色資料匯出到 xls |
> | 動作 | 微軟.關閉Azure/VMware網站/更新屬性/操作 | 更新網站中電腦的屬性 |
> | 動作 | 微軟.關閉Azure/VMware網站/生成粗糙映射/操作 | 為電腦清單產生粗映射 |
> | 動作 | 微軟.關閉Azure/VMware網站/生成詳細地圖/操作 | 產生詳細的 VMware 粗糙地圖 |
> | 動作 | 微軟.關閉Azure/VMware網站/用戶端組成員/操作 | 列出所選客戶端組的客戶端組成員。 |
> | 動作 | 微軟.關閉Azure/VMware網站/伺服器組成員/操作 | 列出所選伺服器組的伺服器組成員。 |
> | 動作 | 微軟.關閉Azure/VMware網站/獲取應用程式/操作 | 取得選取的電腦應用程式資訊 |
> | 動作 | Microsoft.OffAzure/VMwareSites/read | 取得 VMware 站台的屬性 |
> | 動作 | Microsoft.OffAzure/VMwareSites/write | 建立或更新 VMware 站台 |
> | 動作 | Microsoft.OffAzure/VMwareSites/delete | 刪除 VMware 站台 |
> | 動作 | Microsoft.OffAzure/VMwareSites/refresh/action | 重新整理 VMware 站台內的物件 |
> | 動作 | 微軟.關閉Azure/VMware網站/匯出應用程式/操作 | 將 VMware 應用程式和角色資料匯出到 xls |
> | 動作 | 微軟.關閉Azure/VMware網站/更新屬性/操作 | 更新網站中電腦的屬性 |
> | 動作 | 微軟.關閉Azure/VMware網站/生成粗糙映射/操作 | 為電腦清單產生粗映射 |
> | 動作 | 微軟.關閉Azure/VMware網站/生成詳細地圖/操作 | 產生詳細的 VMware 粗糙地圖 |
> | 動作 | 微軟.關閉Azure/VMware網站/用戶端組成員/操作 | 列出所選客戶端組的客戶端組成員。 |
> | 動作 | 微軟.關閉Azure/VMware網站/伺服器組成員/操作 | 列出所選伺服器組的伺服器組成員。 |
> | 動作 | 微軟.關閉Azure/VMware網站/獲取應用程式/操作 | 取得選取的電腦應用程式資訊 |
> |  | **VMwareSites/健康摘要** |  |
> | 動作 | 微軟.關閉Azure/VMwareSites/健康摘要/閱讀 | 取得 VMware 資源的執行狀況摘要 |
> | 動作 | 微軟.關閉Azure/VMwareSites/健康摘要/閱讀 | 取得 VMware 資源的執行狀況摘要 |
> | 動作 | 微軟.關閉Azure/VMwareSites/健康摘要/閱讀 | 取得 VMware 資源的執行狀況摘要 |
> | 動作 | 微軟.關閉Azure/VMwareSites/健康摘要/閱讀 | 取得 VMware 資源的執行狀況摘要 |
> |  | **VMware 網站/作業** |  |
> | 動作 | Microsoft.OffAzure/VMwareSites/jobs/read | 取得 VMware 作業的屬性 |
> | 動作 | Microsoft.OffAzure/VMwareSites/jobs/read | 取得 VMware 作業的屬性 |
> | 動作 | Microsoft.OffAzure/VMwareSites/jobs/read | 取得 VMware 作業的屬性 |
> | 動作 | Microsoft.OffAzure/VMwareSites/jobs/read | 取得 VMware 作業的屬性 |
> |  | **VMwareSites/機器** |  |
> | 動作 | Microsoft.OffAzure/VMwareSites/machines/read | 取得 VMware 機器的屬性 |
> | 動作 | Microsoft.OffAzure/VMwareSites/machines/stop/action | 停止 VMware 機器 |
> | 動作 | Microsoft.OffAzure/VMwareSites/machines/start/action | 啟動 VMware 機器 |
> | 動作 | Microsoft.OffAzure/VMwareSites/machines/read | 取得 VMware 機器的屬性 |
> | 動作 | Microsoft.OffAzure/VMwareSites/machines/stop/action | 停止 VMware 機器 |
> | 動作 | Microsoft.OffAzure/VMwareSites/machines/start/action | 啟動 VMware 機器 |
> | 動作 | Microsoft.OffAzure/VMwareSites/machines/read | 取得 VMware 機器的屬性 |
> | 動作 | Microsoft.OffAzure/VMwareSites/machines/stop/action | 停止 VMware 機器 |
> | 動作 | Microsoft.OffAzure/VMwareSites/machines/start/action | 啟動 VMware 機器 |
> | 動作 | Microsoft.OffAzure/VMwareSites/machines/read | 取得 VMware 機器的屬性 |
> | 動作 | Microsoft.OffAzure/VMwareSites/machines/stop/action | 停止 VMware 機器 |
> | 動作 | Microsoft.OffAzure/VMwareSites/machines/start/action | 啟動 VMware 機器 |
> |  | **VMwareSites/機器/應用程式** |  |
> | 動作 | 微軟.關閉Azure/VMwareSites/機器/應用程式/讀取 | 取得 VMware 電腦應用程式屬性 |
> | 動作 | 微軟.關閉Azure/VMwareSites/機器/應用程式/讀取 | 取得 VMware 電腦應用程式屬性 |
> | 動作 | 微軟.關閉Azure/VMwareSites/機器/應用程式/讀取 | 取得 VMware 電腦應用程式屬性 |
> | 動作 | 微軟.關閉Azure/VMwareSites/機器/應用程式/讀取 | 取得 VMware 電腦應用程式屬性 |
> |  | **VMware 網站/操作狀態** |  |
> | 動作 | Microsoft.OffAzure/VMwareSites/operationsstatus/read | 取得 VMware 作業狀態的屬性 |
> | 動作 | Microsoft.OffAzure/VMwareSites/operationsstatus/read | 取得 VMware 作業狀態的屬性 |
> | 動作 | Microsoft.OffAzure/VMwareSites/operationsstatus/read | 取得 VMware 作業狀態的屬性 |
> | 動作 | Microsoft.OffAzure/VMwareSites/operationsstatus/read | 取得 VMware 作業狀態的屬性 |
> |  | **VMwareSites/執行帳戶** |  |
> | 動作 | Microsoft.OffAzure/VMwareSites/runasaccounts/read | 取得 VMware 執行身分帳戶的屬性 |
> | 動作 | Microsoft.OffAzure/VMwareSites/runasaccounts/read | 取得 VMware 執行身分帳戶的屬性 |
> | 動作 | Microsoft.OffAzure/VMwareSites/runasaccounts/read | 取得 VMware 執行身分帳戶的屬性 |
> | 動作 | Microsoft.OffAzure/VMwareSites/runasaccounts/read | 取得 VMware 執行身分帳戶的屬性 |
> |  | **VMware 網站/摘要** |  |
> | 動作 | 微軟.關閉Azure/VMware網站/摘要/閱讀 | 取得 VMware 網站的摘要 |
> | 動作 | 微軟.關閉Azure/VMware網站/摘要/閱讀 | 取得 VMware 網站的摘要 |
> | 動作 | 微軟.關閉Azure/VMware網站/摘要/閱讀 | 取得 VMware 網站的摘要 |
> | 動作 | 微軟.關閉Azure/VMware網站/摘要/閱讀 | 取得 VMware 網站的摘要 |
> |  | **VMware 網站/使用方式** |  |
> | 動作 | Microsoft.OffAzure/VMwareSites/usage/read | 取得 VMware 站台的使用情形 |
> | 動作 | Microsoft.OffAzure/VMwareSites/usage/read | 取得 VMware 站台的使用情形 |
> | 動作 | Microsoft.OffAzure/VMwareSites/usage/read | 取得 VMware 站台的使用情形 |
> | 動作 | Microsoft.OffAzure/VMwareSites/usage/read | 取得 VMware 站台的使用情形 |
> |  | **VMwareSites/vcenter** |  |
> | 動作 | Microsoft.OffAzure/VMwareSites/vcenters/read | 取得 VMware vCenter 的屬性 |
> | 動作 | Microsoft.OffAzure/VMwareSites/vcenters/write | 建立或更新 VMware vCenter |
> | 動作 | Microsoft.OffAzure/VMwareSites/vcenters/read | 取得 VMware vCenter 的屬性 |
> | 動作 | Microsoft.OffAzure/VMwareSites/vcenters/write | 建立或更新 VMware vCenter |
> | 動作 | Microsoft.OffAzure/VMwareSites/vcenters/read | 取得 VMware vCenter 的屬性 |
> | 動作 | Microsoft.OffAzure/VMwareSites/vcenters/write | 建立或更新 VMware vCenter |
> | 動作 | Microsoft.OffAzure/VMwareSites/vcenters/read | 取得 VMware vCenter 的屬性 |
> | 動作 | Microsoft.OffAzure/VMwareSites/vcenters/write | 建立或更新 VMware vCenter |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

Azure 服務[:Azure 監視器](../azure-monitor/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.OperationalInsights/register/action | 向資源提供者註冊訂用帳戶。 |
> | 動作 | 微軟.運營見解/註冊/行動 | 重新調整訂閱。 |
> | 動作 | 微軟.操作見解/未註冊/操作 | 取消註冊訂閱。 |
> |  | **可用的服務層** |  |
> | 動作 | microsoft.運營見解/可用服務層/讀取 | 獲取可用的服務層。 |
> |  | **集群** |  |
> | 動作 | 微軟.操作見解/集群/閱讀 | 取得叢集 |
> | 動作 | 微軟.操作見解/集群/寫入 | 建立或更新叢集 |
> | 動作 | 微軟.操作見解/集群/刪除 | 刪除叢集 |
> |  | **linkTargets** |  |
> | 動作 | Microsoft.OperationalInsights/linkTargets/read | 列出未與 Azure 訂用帳戶相關聯的現有帳戶。 若要將此 Azure 訂用帳戶連結到工作區，請在「建立工作區」作業的客戶識別碼屬性中，使用這項作業所傳回的客戶識別碼。 |
> |  | **位置/動作狀態** |  |
> | 動作 | 微軟.操作見解/位置/操作 狀態/讀取 | 獲取日誌分析 Azure 非同步操作狀態。 |
> |  | **操作** |  |
> | 動作 | microsoft.operationalinsights/operations/read | 列出所有可用的 OperationalInsights Rest API 作業。 |
> |  | **workspaces** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/write | 建立新的工作區，或藉由提供來自現有工作區的客戶識別碼來連結至現有工作區。 |
> | 動作 | Microsoft.OperationalInsights/workspaces/read | 取得現有工作區 |
> | 動作 | Microsoft.OperationalInsights/workspaces/delete | 刪除工作區。 如果工作區在建立時連結到現有工作區，則系統不會刪除其所連結的工作區。 |
> | 動作 | Microsoft.OperationalInsights/workspaces/generateregistrationcertificate/action | 為工作區產生註冊憑證。 此憑證可用來將 Microsoft System Center Operation Manager 連線到工作區。 |
> | 動作 | Microsoft.OperationalInsights/workspaces/sharedKeys/action | 擷取工作區的共用金鑰。 這些金鑰可用來將 Microsoft Operational Insights 代理程式連線到工作區。 |
> | 動作 | Microsoft.OperationalInsights/workspaces/listKeys/action | 擷取工作區的清單金鑰。 這些金鑰可用來將 Microsoft Operational Insights 代理程式連線到工作區。 |
> | 動作 | Microsoft.OperationalInsights/workspaces/search/action | 執行搜尋查詢 |
> | 動作 | Microsoft.OperationalInsights/workspaces/purge/action | 從工作區刪除指定的資料 |
> | 動作 | Microsoft.OperationalInsights/workspaces/regeneratesharedkey/action | 重新產生指定的工作區共用金鑰 |
> |  | **工作空間/分析** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/analytics/query/action | 使用新的引擎進行搜尋。 |
> |  | **工作區/分析/查詢/架構** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/analytics/query/schema/read | 取得搜尋結構描述 V2。 |
> |  | **工作區/api** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/api/query/action | 使用新的引擎進行搜尋。 |
> |  | **工作區/api/查詢/架構** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/api/query/schema/read | 取得搜尋結構描述 V2。 |
> |  | **工作區/設定範圍** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/configurationScopes/read | 取得組態範圍 |
> | 動作 | Microsoft.OperationalInsights/workspaces/configurationScopes/write | 設定組態範圍 |
> | 動作 | Microsoft.OperationalInsights/workspaces/configurationScopes/delete | 刪除組態範圍 |
> |  | **工作區/資料匯出** |  |
> | 動作 | microsoft.運營見解/工作空間/數據匯出/讀取 | 獲取特定數據匯出。 |
> | 動作 | microsoft.運營見解/工作空間/數據匯出/寫入 | 創建或更新資料匯出。 |
> | 動作 | microsoft.運營見解/工作空間/數據匯出/刪除 | 刪除特定數據匯出。 |
> |  | **工作區/資料來源** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/datasources/read | 取得工作區下的資料來源。 |
> | 動作 | Microsoft.OperationalInsights/workspaces/datasources/write | 在工作區下建立/更新資料來源。 |
> | 動作 | Microsoft.OperationalInsights/workspaces/datasources/delete | 刪除工作區下的資料來源。 |
> |  | **工作區/閘道** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/gateways/delete | 移除針對工作區設定的閘道。 |
> |  | **工作空間/智慧包** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/intelligencepacks/read | 列出給定工作區可見的所有智慧套件，並且也會列出該工作區會啟用還是停用套件。 |
> | 動作 | Microsoft.OperationalInsights/workspaces/intelligencepacks/enable/action | 為給定工作區啟用智慧套件。 |
> | 動作 | Microsoft.OperationalInsights/workspaces/intelligencepacks/disable/action | 為給定工作區停用智慧套件。 |
> |  | **workspaces/linkedServices** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/linkedServices/read | 取得指定工作區下已連結的服務。 |
> | 動作 | Microsoft.OperationalInsights/workspaces/linkedServices/write | 建立/更新指定工作區下已連結的服務。 |
> | 動作 | Microsoft.OperationalInsights/workspaces/linkedServices/delete | 刪除指定工作區下已連結的服務。 |
> |  | **工作區/清單鍵** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/listKeys/read | 擷取工作區的清單金鑰。 這些金鑰可用來將 Microsoft Operational Insights 代理程式連線到工作區。 |
> |  | **工作區/管理群組** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/managementGroups/read | 取得與此工作區連線之 System Center Operations Manager 管理群組的名稱和中繼資料。 |
> |  | **工作區/指標定義** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/metricDefinitions/read | 取得工作區下的計量定義 |
> |  | **工作區/通知設定** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/notificationSettings/read | 針對工作區取得使用者的通知設定。 |
> | 動作 | Microsoft.OperationalInsights/workspaces/notificationSettings/write | 針對工作區設定使用者的通知設定。 |
> | 動作 | Microsoft.OperationalInsights/workspaces/notificationSettings/delete | 針對工作區刪除使用者的通知設定。 |
> |  | **工作區/操作** |  |
> | 動作 | microsoft.運營見解/工作空間/操作/讀取 | 獲取操作見解工作區操作的狀態。 |
> |  | **workspaces/query** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/read | 針對工作區中的資料執行查詢 |
> |  | **工作區/查詢/AdDomain服務帳號日誌** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/AdDomain服務帳戶登錄/讀取 | 從 AADDomain 服務帳號紀錄表中讀取資料 |
> |  | **工作區/查詢/AAD域服務帳戶管理** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/AADDomain服務帳戶管理/讀取 | 從 AADDomain 服務帳號管理表中讀取資料 |
> |  | **工作區/查詢/AAD網域服務目錄服務存取** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/AdDomain服務目錄服務存取/讀取 | 從 AADDomain 服務目錄服務存取表中讀取資料 |
> |  | **工作區/查詢/AdDomain服務登入日誌關閉** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/AdDomain服務登錄/讀取 | 從 AADDomain 服務紀錄讀取資料 |
> |  | **工作區/查詢/AAD網域服務策略變更** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/AAD域服務策略更改/讀取 | 從 AADDomain 服務政策變更表中讀取資料 |
> |  | **工作區/查詢/AAD網域服務許可權使用** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/AAD域服務特權使用/讀取 | 從 AADDomain 服務權限使用表中讀取資料 |
> |  | **工作區/查詢/AAD域服務系統安全性** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/AAD域服務系統安全/讀取 | 從 AADDomain 服務系統安全表中讀取資料 |
> |  | **工作區/查詢/AD評估建議** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/ADAssessmentRecommendation/read | 從 ADAssessmentRecommendation 資料表讀取資料 |
> |  | **工作區/查詢/附加Azure備份警報** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/附加Azure備份警報/讀取 | 從「載入項 Azure 備份警示」表中讀取資料 |
> |  | **工作區/查詢/附加Azure備份作業** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/附加Azure備份作業/讀取 | 從「載入項目 Azure 備份工作」表中讀取資料 |
> |  | **工作區/查詢/附加Azure備份策略** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/附加Azure備份策略/讀取 | 從載入項 Azure 備份策略表中讀取資料 |
> |  | **工作區/查詢/新增 Azure 備份保護實體** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/添加 Azure 備份保護實例/讀取 | 從載入項 Azure 備份保護實體表中讀取資料 |
> |  | **工作區/查詢/附加Azure備份儲存** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/附加Azure備份存儲/讀取 | 從載入項目 Azure 備份儲存表中讀取資料 |
> |  | **工作區/查詢/ADF活動執行** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/ADF活動運行/讀取 | 從 ADF 執行時讀取資料 |
> |  | **工作區/查詢/ADF 導管執行** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/ADF管道運行/讀取 | 從 ADFPipelineRun 表中讀取資料 |
> |  | **工作區/查詢/ADFSSIS整合執行時日誌** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/ADFSSIS整合執行時日誌/讀取 | 從 ADFSIS 整合執行時紀錄表中讀取資料 |
> |  | **工作區/查詢/ADFSSIS 套件事件訊息內容** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/ADFSSIS包事件訊息上下文/讀取 | 從 ADFSSIS 套件事件訊息內容中讀取資料 |
> |  | **工作區/查詢/ADFSSIS 套件事件訊息** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/ADFSSIS包事件消息/讀取 | 從 ADFSSIS 封包事件訊息表中讀取資料 |
> |  | **工作區/查詢/ADFSSIS 套件可執行統計資訊** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/ADFSSIS包可執行統計/讀取 | 從 ADFSSIS 套件可執行統計資訊表中讀取資料 |
> |  | **工作區/查詢/ADFSSIS 套件執行元件階段** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/ADFSSIS包執行元件階段/讀取 | 從 ADFSSIS 套件執行元件階段表中讀取資料 |
> |  | **工作區/查詢/ADFSSIS套件執行資料統計** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/ADFSSIS包執行數據統計/讀取 | 從 ADFSSIS 套件執行資料統計資訊表中讀取資料 |
> |  | **工作區/查詢/ADF觸發器執行** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/ADF觸發器運行/讀取 | 從 ADF 觸發器執行表中讀取資料 |
> |  | **工作區/查詢/AD 複製結果** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/ADReplicationResult/read | 從 ADReplicationResult 資料表讀取資料 |
> |  | **工作區/查詢/AD安全評估建議** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/ADSecurityAssessmentRecommendation/read | 從 ADSecurityAssessmentRecommendation 資料表讀取資料 |
> |  | **工作區/查詢/Aeg交付失敗日誌** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/Aeg交付失敗日誌/讀取 | 從 Aeg 交付失敗紀錄中讀取資料 |
> |  | **工作區/查詢/Aeg發佈失敗日誌** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/Aeg發佈失敗日誌/讀取 | 從 AegPublish 失敗紀錄中讀取資料 |
> |  | **工作區/查詢/警報** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/Alert/read | 從 Alert 資料表讀取資料 |
> |  | **工作區/查詢/警報歷史記錄** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/AlertHistory/read | 從 AlertHistory 資料表讀取資料 |
> |  | **工作區/查詢/Aml 計算叢集事件** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/Aml計算集群事件/讀取 | 從 Aml 計算叢集事件的資料 |
> |  | **工作區/查詢/Aml 計算叢集節點事件** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/Aml計算集群節點事件/讀取 | 從 Aml 計算叢集節點事件的資料 |
> |  | **工作區/查詢/計算作業事件** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/Aml計算作業事件/讀取 | 從 Aml 計算工作表讀取資料 |
> |  | **工作區/查詢/Api管理閘道紀錄** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/Api管理閘道誌/讀取 | 從 Api管理閘道紀錄表中讀取資料 |
> |  | **工作區/查詢/應用程式可用性結果** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/應用程式可用性結果/讀取 | 從應用程式可用性結果表中讀取資料 |
> |  | **工作區/查詢/應用程式瀏覽器計時** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/應用程式瀏覽器計時/閱讀 | 從應用程式來計時表讀取資料 |
> |  | **工作區/查詢/應用中心錯誤** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/AppCenterError/read | 從 AppCenterError 資料表讀取資料 |
> |  | **工作區/查詢/應用程式依賴性** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/應用程式依賴性/讀取 | 從應用相依性表讀取資料 |
> |  | **工作區/查詢/應用程式事件** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/應用程式事件/讀取 | 從應用程式事件表中讀取資料 |
> |  | **工作區/查詢/應用程式見解** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/ApplicationInsights/read | 從 ApplicationInsights 資料表讀取資料 |
> |  | **工作區/查詢/應用指標** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/應用指標/閱讀 | 從應用程式指標表中讀取資料 |
> |  | **工作區/查詢/應用頁面檢視** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/應用程式頁面視圖/閱讀 | 從 AppPageViews 表中讀取資料 |
> |  | **工作區/查詢/應用程式效能計數器** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/應用程式性能計數器/讀取 | 從應用效能計數器表中讀取資料 |
> |  | **工作區/查詢/應用程式平台日誌用於春天** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/AppPlatformLogs 用於Spring/閱讀 | 從 AppPlatformLogsforSpring 表讀取資料 |
> |  | **工作區/查詢/AppPlatform系統日誌** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/AppPlatform系統日誌/閱讀 | 從 AppPlatform 系統紀錄中讀取資料 |
> |  | **工作區/查詢/應用程式要求** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/應用程式請求/讀取 | 從應用程式要求表中讀取資料 |
> |  | **工作區/查詢/應用程式應用日誌** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/AppServiceAppLogs/閱讀 | 從 AppServiceAppLogs 表中讀取資料 |
> |  | **工作區/查詢/應用程式服務稽核紀錄** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/應用程式服務審核日誌/讀取 | 從應用程式服務稽核紀錄中讀取資料 |
> |  | **工作區/查詢/應用程式服務主控台日誌** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/AppServiceConsoleLogs/閱讀 | 從 AppServiceConsoleLogs 表中讀取資料 |
> |  | **工作區/查詢/應用程式服務環境平台日誌** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/應用程式服務環境平臺日誌/閱讀 | 從應用服務環境平台紀錄表中讀取資料 |
> |  | **工作區/查詢/應用程式服務檔稽核紀錄** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/應用程式服務檔審核日誌/讀取 | 從應用程式服務檔案稽核紀錄中讀取資料 |
> |  | **工作區/查詢/應用程式服務HTTPLogs** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/應用程式服務HTTPLogs/讀取 | 從 AppServiceHTTPLogs 表中讀取資料 |
> |  | **工作區/查詢/應用程式系統事件** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/應用程式系統事件/讀取 | 從 AppSystem 事件表中讀取資料 |
> |  | **工作區/查詢/應用追蹤** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/應用程式追蹤/閱讀 | 從 AppTraces 表讀取資料 |
> |  | **工作區/查詢/審核日誌** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/AuditLogs/read | 從 AuditLogs 資料表讀取資料 |
> |  | **工作區/查詢/自動縮放評估日誌** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/自動縮放評估日誌/讀取 | 從自動縮放評估紀錄表中讀取資料 |
> |  | **工作區/查詢/自動縮放操作紀錄** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/自動縮放操作日誌/讀取 | 從自動縮放操作紀錄表中讀取資料 |
> |  | **工作區/查詢/AWSCloudTrail** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/AWSCloudTrail/閱讀 | 從 AWSCloudTrail 表中讀取資料 |
> |  | **工作區/查詢/Azure活動** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/AzureActivity/read | 從 AzureActivity 資料表讀取資料 |
> |  | **工作區/查詢/Azure 評估建議** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/Azure 評估建議/讀取 | 從 Azure 評估建議表中讀取資料 |
> |  | **工作區/查詢/AzureDevOps審核** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/AzureDevOps審核/讀取 | 從 AzureDevOps 稽核表中讀取資料 |
> |  | **工作區/查詢/Azure 診斷** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/Azure診斷/讀取 | 從 Azure 診斷表中讀取資料 |
> |  | **工作區/查詢/Azure指標** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/AzureMetrics/read | 從 AzureMetrics 資料表讀取資料 |
> |  | **工作區/查詢/百色事件** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/百色事件/閱讀 | 從白集束事件表中讀取資料 |
> |  | **工作區/查詢/百色節點事件** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/百家星節點事件/閱讀 | 從白叢集節點事件的讀取資料 |
> |  | **工作區/查詢/白作業事件** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/白作業事件/閱讀 | 從白工作表讀取資料 |
> |  | **工作區/查詢/區塊鏈應用程式日誌** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/區塊鏈應用程式日誌/閱讀 | 從區塊鏈應用程式紀錄表中讀取資料 |
> |  | **工作區/查詢/區塊鏈代理日誌** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/區塊鏈代理日誌/閱讀 | 從區塊鏈代理紀錄表中讀取資料 |
> |  | **工作區/查詢/綁定埠** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/BoundPort/read | 從 BoundPort 資料表讀取資料 |
> |  | **工作區/查詢/通用安全日誌** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/CommonSecurityLog/read | 從 CommonSecurityLog 資料表讀取資料 |
> |  | **工作區/查詢/電腦群組** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/ComputerGroup/read | 從 ComputerGroup 資料表讀取資料 |
> |  | **工作區/查詢/設定變更** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/ConfigurationChange/read | 從 ConfigurationChange 資料表讀取資料 |
> |  | **工作區/查詢/設定資料** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/ConfigurationData/read | 從 ConfigurationData 資料表讀取資料 |
> |  | **工作區/查詢/容器映像清單** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/ContainerImageInventory/read | 從 ContainerImageInventory 資料表讀取資料 |
> |  | **工作區/查詢/容器清單** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/ContainerInventory/read | 從 ContainerInventory 資料表讀取資料 |
> |  | **工作區/查詢/容器紀錄** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/ContainerLog/read | 從 ContainerLog 資料表讀取資料 |
> |  | **工作區/查詢/容器節點清單** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/容器節點庫存/讀取 | 從容器節點清單表中讀取資料 |
> |  | **工作區/查詢/容器註冊登錄事件** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/容器註冊登錄事件/讀取 | 從容器註冊登入事件表中讀取資料 |
> |  | **工作區/查詢/容器註冊表儲存庫事件** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/容器註冊表庫事件/讀取 | 從容器註冊表儲存函式庫事件表中讀取資料 |
> |  | **工作區/查詢/容器服務日誌** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/ContainerServiceLog/read | 從 ContainerServiceLog 資料表讀取資料 |
> |  | **工作區/查詢/核心Azure備份** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/核心Azure備份/讀取 | 從 CoreAzure 備份表中讀取資料 |
> |  | **工作區/查詢/資料磚塊帳戶** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/數據磚帳戶/讀取 | 從資料磚塊會計表讀取資料 |
> |  | **工作區/查詢/資料磚群集** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/數據磚集群/讀取 | 從資料磚塊叢集表中讀取資料 |
> |  | **工作區/查詢/資料磚塊DBFS** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/數據磚塊DBFS/讀取 | 從資料磚塊 DBFS 表中讀取資料 |
> |  | **工作區/查詢/資料磚實例池** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/數據磚實例池/讀取 | 從資料磚塊實體的池表中讀取資料 |
> |  | **工作區/查詢/資料磚塊作業** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/數據磚作業/讀取 | 從資料磚區的工作表中讀取資料 |
> |  | **工作區/查詢/資料磚塊筆記本** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/數據磚筆記本/閱讀 | 從資料磚區的錶中讀取資料 |
> |  | **工作區/查詢/資料磚秘密** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/數據磚秘密/讀取 | 從資料磚塊機密表中讀取資料 |
> |  | **工作區/查詢/資料磚塊SQL許可權** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/數據磚塊SQL許可權/讀取 | 從資料磚塊 SQL 權限表中讀取資料 |
> |  | **工作區/查詢/資料磚塊SSH** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/數據磚塊SSH/讀取 | 從資料磚塊SSH表中讀取資料 |
> |  | **工作區/查詢/資料磚塊表** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/數據磚塊表/讀取 | 從資料磚塊表讀取資料 |
> |  | **工作區/查詢/資料磚工作區** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/數據磚工作空間/讀取 | 從資料磚區的工作區表中讀取資料 |
> |  | **工作區/查詢/依賴項** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/依賴/閱讀 | 從相依項目的表格讀取資料 |
> |  | **工作區/查詢/裝置AppCrash** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/DeviceAppCrash/read | 從 DeviceAppCrash 資料表讀取資料 |
> |  | **工作區/查詢/設備App啟動** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/DeviceAppLaunch/read | 從 DeviceAppLaunch 資料表讀取資料 |
> |  | **工作區/查詢/裝置行事曆** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/DeviceCalendar/read | 從 DeviceCalendar 資料表讀取資料 |
> |  | **工作區/查詢/裝置清理** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/DeviceCleanup/read | 從 DeviceCleanup 資料表讀取資料 |
> |  | **工作區/查詢/裝置連線工作階段** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/DeviceConnectSession/read | 從 DeviceConnectSession 資料表讀取資料 |
> |  | **工作區/查詢/裝置** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/DeviceEtw/read | 從 DeviceEtw 資料表讀取資料 |
> |  | **工作區/查詢/裝置硬體執行狀況** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/DeviceHardwareHealth/read | 從 DeviceHardwareHealth 資料表讀取資料 |
> |  | **工作區/查詢/裝置執行狀況** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/DeviceHealth/read | 從 DeviceHealth 資料表讀取資料 |
> |  | **工作區/查詢/裝置偵測** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/DeviceHeartbeat/read | 從 DeviceHeartbeat 資料表讀取資料 |
> |  | **工作區/查詢/設備Skype檢測** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/DeviceSkypeHeartbeat/read | 從 DeviceSkypeHeartbeat 資料表讀取資料 |
> |  | **工作區/查詢/裝置SkypeSignIn** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/DeviceSkypeSignIn/read | 從 DeviceSkypeSignIn 資料表讀取資料 |
> |  | **工作區/查詢/裝置睡眠狀態** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/DeviceSleepState/read | 從 DeviceSleepState 資料表讀取資料 |
> |  | **工作區/查詢/DHApp失敗** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/DHAppFailure/read | 從 DHAppFailure 資料表讀取資料 |
> |  | **工作區/查詢/DHApp 可靠性** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/DHAppReliability/read | 從 DHAppReliability 資料表讀取資料 |
> |  | **工作區/查詢/DHCP活動** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/DHCP活動/讀取 | 從 DHCPActivity 表中讀取資料 |
> |  | **工作區/查詢/DH驅動程式可靠性** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/DHDriverReliability/read | 從 DHDriverReliability 資料表讀取資料 |
> |  | **工作區/查詢/DHLogon 失敗** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/DHLogonFailures/read | 從 DHLogonFailures 資料表讀取資料 |
> |  | **工作區/查詢/DHLogon指標** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/DHLogonMetrics/read | 從 DHLogonMetrics 資料表讀取資料 |
> |  | **工作區/查詢/DHOSCrash資料** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/DHOSCrashData/read | 從 DHOSCrashData 資料表讀取資料 |
> |  | **工作區/查詢/DHOS 可靠性** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/DHOSReliability/read | 從 DHOSReliability 資料表讀取資料 |
> |  | **工作區/查詢/DHWipApp學習** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/DHWipAppLearning/read | 從 DHWipAppLearning 資料表讀取資料 |
> |  | **工作區/查詢/Dns事件** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/DnsEvents/read | 從 DnsEvents 資料表讀取資料 |
> |  | **工作區/查詢/Dns清單** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/DnsInventory/read | 從 DnsInventory 資料表讀取資料 |
> |  | **工作區/查詢/ETW事件** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/ETWEvent/read | 從 ETWEvent 資料表讀取資料 |
> |  | **工作區/查詢/事件** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/Event/read | 從 Event 資料表讀取資料 |
> |  | **工作區/查詢/交換評估建議** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/ExchangeAssessmentRecommendation/read | 從 ExchangeAssessmentRecommendation 資料表讀取資料 |
> |  | **工作區/查詢/交換線上評估建議** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/ExchangeOnlineAssessmentRecommendation/read | 從 ExchangeOnlineAssessmentRecommendation 資料表讀取資料 |
> |  | **工作區/查詢/失敗** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/失敗/讀取 | 從失敗化表讀取資料 |
> |  | **工作區/查詢/功能應用日誌** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/功能應用日誌/讀取 | 從函數應用紀錄表中讀取資料 |
> |  | **工作區/查詢/檢測信號** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/Heartbeat/read | 從 Heartbeat 資料表讀取資料 |
> |  | **工作區/查詢/狩獵書籤** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/狩獵書簽/閱讀 | 從「狩獵書籤」表中讀取數據 |
> |  | **工作區/查詢/IIS評估建議** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/IISAssessmentRecommendation/read | 從 IISAssessmentRecommendation 資料表讀取資料 |
> |  | **工作區/查詢/入站連線** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/InboundConnection/read | 從 InboundConnection 資料表讀取資料 |
> |  | **工作區/查詢/見解指標** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/見解指標/閱讀 | 從「洞察指標」表中讀取資料 |
> |  | **工作區/查詢/調諧審核日誌** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/IntuneAuditLogs/read | 從 IntuneAuditLogs 資料表讀取資料 |
> |  | **工作區/查詢/調諧設備合規性組織** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/Intune設備合規性組織/讀取 | 從 IntuneDevice 合規組織表中讀取資料 |
> |  | **工作區/查詢/Intune 操作紀錄** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/IntuneOperationalLogs/read | 從 IntuneOperationalLogs 資料表讀取資料 |
> |  | **工作區/查詢/IoTHub 分散式追蹤** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/IoTHub分散式跟蹤/讀取 | 從 IoTHub 分散式追蹤表中讀取資料 |
> |  | **工作區/查詢/庫貝事件** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/KubeEvents/read | 從 KubeEvents 資料表讀取資料 |
> |  | **工作區/查詢/庫貝健康** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/庫貝健康/閱讀 | 從庫貝健康表中讀取資料 |
> |  | **工作區/查詢/庫貝單活動** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/庫貝蒙代理事件/讀取 | 從 KubeMonAgent 事件表中讀取資料 |
> |  | **工作區/查詢/庫貝節點清單** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/KubeNodeInventory/read | 從 KubeNodeInventory 資料表讀取資料 |
> |  | **工作區/查詢/庫貝波德庫存** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/KubePodInventory/read | 從 KubePodInventory 資料表讀取資料 |
> |  | **工作區/查詢/庫貝服務** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/KubeServices/read | 從 KubeServices 資料表讀取資料 |
> |  | **工作區/查詢/LinuxAuditLog** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/LinuxAuditLog/read | 從 LinuxAuditLog 資料表讀取資料 |
> |  | **工作區/查詢/MA應用程式** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MAApplication/read | 從 MAApplication 資料表讀取資料 |
> |  | **工作區/查詢/MA應用程式執行狀況** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealth/read | 從 MAApplicationHealth 資料表讀取資料 |
> |  | **工作區/查詢/MA應用程式執行狀況替代版本** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealthAlternativeVersions/read | 從 MAApplicationHealthAlternativeVersions 資料表讀取資料 |
> |  | **工作區/查詢/MA應用程式健康問題** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealthIssues/read | 從 MAApplicationHealthIssues 資料表讀取資料 |
> |  | **工作區/查詢/MA應用程式實體** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MAApplicationInstance/read | 從 MAApplicationInstance 資料表讀取資料 |
> |  | **工作區/查詢/MA應用程式實例就緒性** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MAApplicationInstanceReadiness/read | 從 MAApplicationInstanceReadiness 資料表讀取資料 |
> |  | **工作區/查詢/MA應用程式就緒** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MAApplicationReadiness/read | 從 MAApplicationReadiness 資料表讀取資料 |
> |  | **工作區/查詢/MA部署計劃** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MADeploymentPlan/read | 從 MADeploymentPlan 資料表讀取資料 |
> |  | **工作區/查詢/MADevice** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MADevice/read | 從 MADevice 資料表讀取資料 |
> |  | **工作區/查詢/MADevice 未註冊** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MADeviceNotEnrolled/read | 從 MADeviceNotEnrolled 資料表讀取資料 |
> |  | **工作區/查詢/MADeviceNRT** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/MADeviceNRT/讀取 | 從 MADeviceNRT 表中讀取資料 |
> |  | **工作區/查詢/MADevicePnP執行狀況** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealth/read | 從 MADevicePnPHealth 資料表讀取資料 |
> |  | **工作區/查詢/MADevicePnP執行狀況替代版本** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealthAlternativeVersions/read | 從 MADevicePnPHealthAlternativeVersions 資料表讀取資料 |
> |  | **工作區/查詢/MADevicePnP健康問題** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealthIssues/read | 從 MADevicePnPHealthIssues 資料表讀取資料 |
> |  | **工作區/查詢/MADevice 就緒** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MADeviceReadiness/read | 從 MADeviceReadiness 資料表讀取資料 |
> |  | **工作區/查詢/MADriver實例就緒性** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MADriverInstanceReadiness/read | 從 MADriverInstanceReadiness 資料表讀取資料 |
> |  | **工作區/查詢/MADriver 就緒** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MADriverReadiness/read | 從 MADriverReadiness 資料表讀取資料 |
> |  | **工作區/查詢/MAOfficeAddin** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddin/read | 從 MAOfficeAddin 資料表讀取資料 |
> |  | **工作區/查詢/MAOfficeAddinEntity運行狀況** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/MAOfficeAddinEntity運行狀況/讀取 | 從 MAOfficeAddinEntity 執行狀況表中讀取資料 |
> |  | **工作區/查詢/MAOfficeAddin 健康** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinHealth/read | 從 MAOfficeAddinHealth 資料表讀取資料 |
> |  | **工作區/查詢/MAOfficeAddinHealthEventNRT** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/MAOfficeAddinHealthEventNRT/閱讀 | 從 MAOfficeAddinHealthEventNRT 表讀取資料 |
> |  | **工作區/查詢/MAOfficeAddin 健康問題** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinHealthIssues/read | 從 MAOfficeAddinHealthIssues 資料表讀取資料 |
> |  | **工作區/查詢/MAOfficeAddin實例** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinInstance/read | 從 MAOfficeAddinInstance 資料表讀取資料 |
> |  | **工作區/查詢/MAOfficeAddin實例就緒性** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinInstanceReadiness/read | 從 MAOfficeAddinInstanceReadiness 資料表讀取資料 |
> |  | **工作區/查詢/MAOfficeAddin 準備** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinReadiness/read | 從 MAOfficeAddinReadiness 資料表讀取資料 |
> |  | **工作區/查詢/MAOffice應用程式** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MAOfficeApp/read | 從 MAOfficeApp 資料表讀取資料 |
> |  | **工作區/查詢/MAOfficeApp崩潰NRT** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/MAOfficeApp崩潰NRT/讀取 | 從 MAOfficeAppCrashesNRT 表讀取資料 |
> |  | **工作區/查詢/MAOfficeAppHealth** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppHealth/read | 從 MAOfficeAppHealth 資料表讀取資料 |
> |  | **工作區/查詢/MAOfficeApp實例** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppInstance/read | 從 MAOfficeAppInstance 資料表讀取資料 |
> |  | **工作區/查詢/MAOfficeApp實例運行狀況** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/MAOfficeApp實例運行狀況/讀取 | 從 MAOfficeApp 實例執行狀況表中讀取資料 |
> |  | **工作區/查詢/MAOffice 應用程式就緒** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppReadiness/read | 從 MAOfficeAppReadiness 資料表讀取資料 |
> |  | **工作區/查詢/MAOfficeAppSessionsNRT** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/MAOfficeAppSessionsNRT/讀取 | 從 MAOfficeAppSessionsNRT 讀取資料 |
> |  | **工作區/查詢/MAOffice 建譯資訊** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MAOfficeBuildInfo/read | 從 MAOfficeBuildInfo 資料表讀取資料 |
> |  | **工作區/查詢/MAOffice 貨幣評估** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MAOfficeCurrencyAssessment/read | 從 MAOfficeCurrencyAssessment 資料表讀取資料 |
> |  | **工作區/查詢/MAOffice 貨幣評估日計數** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MAOfficeCurrencyAssessmentDailyCounts/read | 從 MAOfficeCurrencyAssessmentDailyCounts 資料表讀取資料 |
> |  | **工作區/查詢/MAOffice 部署狀態** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MAOfficeDeploymentStatus/read | 從 MAOfficeDeploymentStatus 資料表讀取資料 |
> |  | **工作區/查詢/MAOffice 部署狀態NRT** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/MAOffice 部署狀態NRT/讀取 | 從 MAOffice 部署狀態 NRT 表中讀取資料 |
> |  | **工作區/查詢/MAOfficeMacro錯誤NRT** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/MAOfficeMacro錯誤NRT/讀取 | 從 MAOfficeMacro 錯誤 NRT 的資料 |
> |  | **工作區/查詢/MAOfficeMacro 全球運行狀況** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/MAOfficeMacro全球健康/讀取 | 從 MAOfficeMacro 全球執行狀況表中讀取資料 |
> |  | **工作區/查詢/MAOfficeMacroHealth** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroHealth/read | 從 MAOfficeMacroHealth 資料表讀取資料 |
> |  | **工作區/查詢/MAOfficeMacroHealth 問題** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroHealthIssues/read | 從 MAOfficeMacroHealthIssues 資料表讀取資料 |
> |  | **工作區/查詢/MAOfficeMacro 問題實例就緒** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroIssueInstanceReadiness/read | 從 MAOfficeMacroIssueInstanceReadiness 資料表讀取資料 |
> |  | **工作區/查詢/MAOfficeMacro 問題就緒** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroIssueReadiness/read | 從 MAOfficeMacroIssueReadiness 資料表讀取資料 |
> |  | **工作區/查詢/MAOfficeMacro 摘要** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroSummary/read | 從 MAOfficeMacroSummary 資料表讀取資料 |
> |  | **工作區/查詢/MAOfficeSuite** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MAOfficeSuite/read | 從 MAOfficeSuite 資料表讀取資料 |
> |  | **工作區/查詢/MAOfficeSuite實例** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MAOfficeSuiteInstance/read | 從 MAOfficeSuiteInstance 資料表讀取資料 |
> |  | **工作區/查詢/MA 建議引導裝置** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MAProposedPilotDevices/read | 從 MAProposedPilotDevices 資料表讀取資料 |
> |  | **工作區/查詢/MAWindows建構資訊** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MAWindowsBuildInfo/read | 從 MAWindowsBuildInfo 資料表讀取資料 |
> |  | **工作區/查詢/MAWindows貨幣評估** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MAWindowsCurrencyAssessment/read | 從 MAWindowsCurrencyAssessment 資料表讀取資料 |
> |  | **工作區/查詢/MAWindows貨幣評估日計數** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MAWindowsCurrencyAssessmentDailyCounts/read | 從 MAWindowsCurrencyAssessmentDailyCounts 資料表讀取資料 |
> |  | **工作區/查詢/MAWindows部署狀態** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MAWindowsDeploymentStatus/read | 從 MAWindowsDeploymentStatus 資料表讀取資料 |
> |  | **工作區/查詢/MAWindows 部署狀態NRT** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/MAWindows部署狀態NRT/讀取 | 從 MAWindows 部署狀態 NRT 表中讀取資料 |
> |  | **工作區/查詢/MAWindowsSysReq實例就緒性** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MAWindowsSysReqInstanceReadiness/read | 從 MAWindowsSysReqInstanceReadiness 資料表讀取資料 |
> |  | **工作區/查詢/麥克斯影子報告** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/McasShadowIt報告/讀取 | 從 McasShadowIt 報告表中讀取資料 |
> |  | **工作區/查詢/微軟Azure堡壘審核日誌** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/微軟Azure堡壘審核日誌/讀取 | 從 MicrosoftAzureBastion 稽核紀錄中讀取資料 |
> |  | **工作區/查詢/微軟數據共享接收快照日誌** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/微軟數據共享接收快照日誌/讀取 | 從 Microsoft 資料分享接收快照紀錄中讀取資料 |
> |  | **工作區/查詢/微軟數據共享快照日誌** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/微軟數據共享共用快照日誌/讀取 | 從 Microsoft 資料共享分享快照日誌表中讀取資料 |
> |  | **工作區/查詢/微軟資料共享日誌** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/微軟數據共享日誌/閱讀 | 從 Microsoft 資料分享紀錄表中讀取資料 |
> |  | **工作區/查詢/微軟動態遙測性能日誌** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/微軟動態遙測性能日誌/讀取 | 從 Microsoft 動態遙測性能紀錄表中讀取資料 |
> |  | **工作區/查詢/微軟動力學遙測系統指標日誌** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/微軟動力學遙測系統指標日誌/讀取 | 從 Microsoft 動態遙測系統指標紀錄表中讀取資料 |
> |  | **工作區/查詢/微軟醫療審核日誌** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/微軟醫療審核日誌/讀取 | 從 Microsoft 醫療稽核紀錄中讀取資料 |
> |  | **工作區/查詢/網路監控** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/NetworkMonitoring/read | 從 NetworkMonitoring 資料表讀取資料 |
> |  | **工作區/查詢/辦公室活動** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/OfficeActivity/read | 從 OfficeActivity 資料表讀取資料 |
> |  | **工作區/查詢/操作** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/Operation/read | 從 Operation 資料表讀取資料 |
> |  | **工作區/查詢/出站連線** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/OutboundConnection/read | 從 OutboundConnection 資料表讀取資料 |
> |  | **工作區/查詢/Perf** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/Perf/read | 從 Perf 資料表讀取資料 |
> |  | **工作區/查詢/保護狀態** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/ProtectionStatus/read | 從 ProtectionStatus 資料表讀取資料 |
> |  | **工作區/查詢/請求** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/請求/讀取 | 從要求表中讀取資料 |
> |  | **工作區/查詢/預留公共欄位** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/ReservedCommonFields/read | 從 ReservedCommonFields 資料表讀取資料 |
> |  | **工作區/查詢/SCCM評估建議** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/SCCMAssessmentRecommendation/read | 從 SCCMAssessmentRecommendation 資料表讀取資料 |
> |  | **工作區/查詢/SCOM評估建議** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/SCOMAssessmentRecommendation/read | 從 SCOMAssessmentRecommendation 資料表讀取資料 |
> |  | **工作區/查詢/安全警報** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/SecurityAlert/read | 從 SecurityAlert 資料表讀取資料 |
> |  | **工作區/查詢/安全基線** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/SecurityBaseline/read | 從 SecurityBaseline 資料表讀取資料 |
> |  | **工作區/查詢/安全基線摘要** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/SecurityBaselineSummary/read | 從 SecurityBaselineSummary 資料表讀取資料 |
> |  | **工作區/查詢/安全檢測** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/SecurityDetection/read | 從 SecurityDetection 資料表讀取資料 |
> |  | **工作區/查詢/安全事件** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/SecurityEvent/read | 從 SecurityEvent 資料表讀取資料 |
> |  | **工作區/查詢/安全IoTRaw事件** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/安全IoTRawEvent/讀取 | 從安全IoTRawEvent表讀取資料 |
> |  | **工作區/查詢/安全建議** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/安全推薦/閱讀 | 從安全建議表中讀取資料 |
> |  | **工作區/查詢/服務結構操作事件** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/ServiceFabricOperationalEvent/read | 從 ServiceFabricOperationalEvent 資料表讀取資料 |
> |  | **工作區/查詢/服務結構可靠參與者事件** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/ServiceFabricReliableActorEvent/read | 從 ServiceFabricReliableActorEvent 資料表讀取資料 |
> |  | **工作區/查詢/服務結構可靠服務事件** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/ServiceFabricReliableServiceEvent/read | 從 ServiceFabricReliableServiceEvent 資料表讀取資料 |
> |  | **工作區/查詢/SfB評估建議** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/SfBAssessmentRecommendation/read | 從 SfBAssessmentRecommendation 資料表讀取資料 |
> |  | **工作區/查詢/SfB線上評估建議** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/SfBOnlineAssessmentRecommendation/read | 從 SfBOnlineAssessmentRecommendation 資料表讀取資料 |
> |  | **工作區/查詢/共用點線上評估建議** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/SharePointOnlineAssessmentRecommendation/read | 從 SharePointOnlineAssessmentRecommendation 資料表讀取資料 |
> |  | **工作區/查詢/信號R服務診斷日誌** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/信號R服務診斷日誌/讀取 | 從訊號 R 服務診斷紀錄中讀取資料 |
> |  | **工作區/查詢/登錄日誌** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/SigninLogs/read | 從 SigninLogs 資料表讀取資料 |
> |  | **工作區/查詢/評估建議** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/SPAssessmentRecommendation/read | 從 SPAssessmentRecommendation 資料表讀取資料 |
> |  | **工作區/查詢/SQL評估建議** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/SQLAssessmentRecommendation/read | 從 SQLAssessmentRecommendation 資料表讀取資料 |
> |  | **工作區/查詢/SqlData 分類** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/SqlData 分類/讀取 | 從 SqlData 分類表中讀取資料 |
> |  | **工作區/查詢/SQLQuery性能** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/SQLQueryPerformance/read | 從 SQLQueryPerformance 資料表讀取資料 |
> |  | **工作區/查詢/Sql威脅保護登錄審核** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/SqlThreat保護登錄審核/讀取 | 從 SqlThreat 保護登入稽核表中讀取資料 |
> |  | **工作區/查詢/Sql脆弱評估結果** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/Sql脆弱評估結果/讀取 | 從 SqlVulnerability 評估結果表中讀取資料 |
> |  | **工作區/查詢/儲存BlobLogs** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/存儲BlobLogs/讀取 | 從儲存BlobLogs表讀取資料 |
> |  | **工作區/查詢/儲存檔案紀錄** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/存儲檔案日誌/讀取 | 從儲存檔案紀錄表中讀取資料 |
> |  | **工作區/查詢/儲存佇列日誌** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/存儲佇列日誌/讀取 | 從儲存佇列紀錄表中讀取資料 |
> |  | **工作區/查詢/儲存表日誌** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/存儲表日誌/讀取 | 從儲存表格曆讀取資料 |
> |  | **工作區/查詢/成功** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/成功/閱讀 | 從成功化表讀取資料 |
> |  | **工作區/查詢/系統紀錄** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/Syslog/read | 從 Syslog 資料表讀取資料 |
> |  | **工作區/查詢/系統事件** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/SysmonEvent/read | 從 SysmonEvent 資料表讀取資料 |
> |  | **工作區/查詢/表.自訂** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read | 從任何自訂記錄檔讀取資料 |
> |  | **工作區/查詢/威脅情報指標** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/威脅情報指示器/讀取 | 從威脅智慧指示器表中讀取資料 |
> |  | **工作區/查詢/UAApp** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/UAApp/read | 從 UAApp 資料表讀取資料 |
> |  | **工作區/查詢/UA 電腦** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/UAComputer/read | 從 UAComputer 資料表讀取資料 |
> |  | **工作區/查詢/UA 電腦排名** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/UAComputer/read | 從 UAComputerRank 資料表讀取資料 |
> |  | **工作區/查詢/UADriver** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/UADriver/read | 從 UADriver 資料表讀取資料 |
> |  | **工作區/查詢/UADriver問題代碼** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/UADriverProblemCodes/read | 從 UADriverProblemCodes 資料表讀取資料 |
> |  | **工作區/查詢/UA回饋** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/UAFeedback/read | 從 UAFeedback 資料表讀取資料 |
> |  | **工作區/查詢/UA 硬體安全** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/UAHardwareSecurity/read | 從 UAHardwareSecurity 資料表讀取資料 |
> |  | **工作區/查詢/UAIE 網站發現** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/UAIESiteDiscovery/read | 從 UAIESiteDiscovery 資料表讀取資料 |
> |  | **工作區/查詢/UAOfficeAddIn** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/UAOfficeAddIn/read | 從 UAOfficeAddIn 資料表讀取資料 |
> |  | **工作區/查詢/UA 建議行動計劃** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/UAProposedActionPlan/read | 從 UAProposedActionPlan 資料表讀取資料 |
> |  | **工作區/查詢/UASysReq問題** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/UASysReqIssue/read | 從 UASysReqIssue 資料表讀取資料 |
> |  | **工作區/查詢/UA 升級電腦** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/UAUpgradedComputer/read | 從 UAUpgradedComputer 資料表讀取資料 |
> |  | **工作區/查詢/更新** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/Update/read | 從 Update 資料表讀取資料 |
> |  | **工作區/查詢/更新執行進度** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/UpdateRunProgress/read | 從 UpdateRunProgress 資料表讀取資料 |
> |  | **工作區/查詢/更新摘要** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/UpdateSummary/read | 從 UpdateSummary 資料表讀取資料 |
> |  | **工作區/查詢/使用** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/Usage/read | 從 Usage 資料表讀取資料 |
> |  | **工作區/查詢/VMBundPort** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/VMBoundPort/read | 從 VMBoundPort 資料表讀取資料 |
> |  | **工作區/查詢/虛擬機器** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/VM計算機/讀取 | 從 VM 電腦表中讀取資料 |
> |  | **工作區/查詢/VM連接** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/VMConnection/read | 從 VMConnection 資料表讀取資料 |
> |  | **工作區/查詢/VMProcess** |  |
> | 動作 | 微軟.操作見解/工作空間/查詢/VMProcess/讀取 | 從 VMProcess 表中讀取資料 |
> |  | **工作區/查詢/W3CIIS日誌** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/W3CIISLog/read | 從 W3CIISLog 資料表讀取資料 |
> |  | **工作區/查詢/WaaS部署狀態** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/WaaSDeploymentStatus/read | 從 WaaSDeploymentStatus 資料表讀取資料 |
> |  | **工作區/查詢/WaaS內幕狀態** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/WaaSInsiderStatus/read | 從 WaaSInsiderStatus 資料表讀取資料 |
> |  | **工作區/查詢/瓦塞更新狀態** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/WaaSUpdateStatus/read | 從 WaaSUpdateStatus 資料表讀取資料 |
> |  | **工作區/查詢/WDAV 狀態** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/WDAVStatus/read | 從 WDAVStatus 資料表讀取資料 |
> |  | **工作區/查詢/WDAV威脅** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/WDAVThreat/read | 從 WDAVThreat 資料表讀取資料 |
> |  | **工作區/查詢/Windows客戶端評估建議** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/WindowsClientAssessmentRecommendation/read | 從 WindowsClientAssessmentRecommendation 資料表讀取資料 |
> |  | **工作區/查詢/視窗事件** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/WindowsEvent/read | 從 WindowsEvent 資料表讀取資料 |
> |  | **工作區/查詢/Windows 防火牆** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/WindowsFirewall/read | 從 WindowsFirewall 資料表讀取資料 |
> |  | **工作區/查詢/WindowsServer評估建議** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/WindowsServerAssessmentRecommendation/read | 從 WindowsServerAssessmentRecommendation 資料表讀取資料 |
> |  | **工作區/查詢/線資料** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/WireData/read | 從 WireData 資料表讀取資料 |
> |  | **工作區/查詢/工作負載監視 Perf** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/WorkloadMonitoringPerf/read | 從 WorkloadMonitoringPerf 資料表中讀取資料 |
> |  | **工作區/查詢/WUDO聚合狀態** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/WUDOAggregatedStatus/read | 從 WUDOAggregatedStatus 資料表讀取資料 |
> |  | **工作區/查詢/WUDO狀態** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/WUDOStatus/read | 從 WUDOStatus 資料表讀取資料 |
> |  | **工作區/規則** |  |
> | 動作 | microsoft.operationalinsights/workspaces/rules/read | 取得所有警示規則。 |
> |  | **workspaces/savedSearches** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/savedSearches/read | 取得已儲存的搜尋查詢 |
> | 動作 | Microsoft.OperationalInsights/workspaces/savedSearches/write | 建立已儲存的搜尋查詢 |
> | 動作 | Microsoft.OperationalInsights/workspaces/savedSearches/delete | 刪除已儲存的搜尋查詢 |
> |  | **工作區/儲存的搜尋/結果** |  |
> | 動作 | microsoft.operationalinsights/workspaces/savedsearches/results/read | 取得已儲存的搜尋結果。 已被取代 |
> |  | **工作區/儲存的搜尋/排程** |  |
> | 動作 | microsoft.operationalinsights/workspaces/savedsearches/schedules/read | 取得已排程的搜尋。 |
> | 動作 | microsoft.operationalinsights/workspaces/savedsearches/schedules/delete | 刪除已排程的搜尋。 |
> | 動作 | microsoft.operationalinsights/workspaces/savedsearches/schedules/write | 建立或更新已排程的搜尋。 |
> |  | **工作區/儲存的搜尋/排程/操作** |  |
> | 動作 | microsoft.operationalinsights/workspaces/savedsearches/schedules/actions/read | 取得已排程的搜尋動作。 |
> | 動作 | microsoft.operationalinsights/workspaces/savedsearches/schedules/actions/delete | 刪除已排程的搜尋動作。 |
> | 動作 | microsoft.operationalinsights/workspaces/savedsearches/schedules/actions/write | 建立或更新已排程的搜尋動作。 |
> |  | **工作區/架構** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/schema/read | 取得工作區的搜尋結構描述。  搜尋結構描述包括公開的欄位和其類型。 |
> |  | **工作區/作用域私人連結 Proxies** |  |
> | 動作 | microsoft.操作見解/工作空間/範圍PrivateLinkProxies/讀取 | 獲取作用域專用連結代理。 |
> | 動作 | microsoft.操作見解/工作空間/範圍私人鏈接普洛西/寫入 | 放置作用域專用連結代理。 |
> | 動作 | microsoft.操作見解/工作區/範圍PrivateLinkProxies/刪除 | 刪除作用域專用連結代理。 |
> |  | **工作區/搜尋** |  |
> | 動作 | microsoft.operationalinsights/workspaces/search/read | 取得搜尋結果。 已被取代。 |
> |  | **工作區/共享金鑰** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/sharedKeys/read | 擷取工作區的共用金鑰。 這些金鑰可用來將 Microsoft Operational Insights 代理程式連線到工作區。 |
> |  | **工作空間/儲存洞察設定** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/write | 建立新的儲存體組態。 這些組態可用來提取現有儲存體帳戶中來自某個位置的資料。 |
> | 動作 | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/read | 取得儲存體組態。 |
> | 動作 | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/delete | 刪除儲存體組態。 這會讓 Microsoft Operational Insights 停止從儲存體帳戶讀取資料。 |
> |  | **工作區/升級翻譯失敗** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/upgradetranslationfailures/read | 取得工作區的搜尋升級翻譯失敗記錄 |
> |  | **工作區/用法** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/usages/read | 取得工作區的使用量資料，包括工作區所讀取的資料量。 |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

Azure 服務[:Azure 監視器](../azure-monitor/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.OperationsManagement/register/action | 向資源提供者註冊訂用帳戶。 |
> |  | **管理協會** |  |
> | 動作 | Microsoft.OperationsManagement/managementAssociations/write | 建立新的管理關聯 |
> | 動作 | Microsoft.OperationsManagement/managementAssociations/read | 取得現有的管理關聯 |
> | 動作 | Microsoft.OperationsManagement/managementAssociations/delete | 刪除現有的管理關聯 |
> |  | **管理設定** |  |
> | 動作 | Microsoft.OperationsManagement/managementConfigurations/write | 建立新的管理設定 |
> | 動作 | Microsoft.OperationsManagement/managementConfigurations/read | 取得現有的管理設定 |
> | 動作 | Microsoft.OperationsManagement/managementConfigurations/delete | 刪除現有的管理組態 |
> |  | **解決機制** |  |
> | 動作 | Microsoft.OperationsManagement/solutions/write | 建立新的 OMS 解決方案 |
> | 動作 | Microsoft.OperationsManagement/solutions/read | 取得現有的 OMS 解決方案 |
> | 動作 | Microsoft.OperationsManagement/solutions/delete | 刪除現有的 OMS 解決方案 |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

Azure 服務[:Azure 策略](../governance/policy/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.PolicyInsights/register/action | 註冊 Microsoft 策略見解資源提供程式,並啟用其操作。 |
> | 動作 | 微軟.政策見解/取消註冊/操作 | 取消註冊 Microsoft 策略見解資源提供程式。 |
> |  | **非同步操作結果** |  |
> | 動作 | Microsoft.PolicyInsights/asyncOperationResults/read | 取得非同步作業結果。 |
> |  | **操作** |  |
> | 動作 | 微軟.政策見解/操作/閱讀 | 獲取 Microsoft 上支援的操作。 |
> |  | **policyEvents** |  |
> | 動作 | Microsoft.PolicyInsights/policyEvents/queryResults/action | 查詢原則事件的相關資訊。 |
> |  | **原則事件/查詢結果** |  |
> | 動作 | Microsoft.PolicyInsights/policyEvents/queryResults/read | 查詢原則事件的相關資訊。 |
> |  | **原則中繼資料** |  |
> | 動作 | 微軟.政策見解/政策元數據/閱讀 | 獲取策略元數據資源。 |
> |  | **policyStates** |  |
> | 動作 | Microsoft.PolicyInsights/policyStates/queryResults/action | 查詢原則狀態的相關資訊。 |
> | 動作 | Microsoft.PolicyInsights/policyStates/summarize/action | 查詢原則最新狀態的摘要資訊。 |
> | 動作 | Microsoft.PolicyInsights/policyStates/triggerEvaluation/action | 對選取的範圍觸發新的合規性評估。 |
> |  | **原則狀態/查詢結果** |  |
> | 動作 | Microsoft.PolicyInsights/policyStates/queryResults/read | 查詢原則狀態的相關資訊。 |
> |  | **政策狀態/摘要** |  |
> | 動作 | Microsoft.PolicyInsights/policyStates/summarize/read | 查詢原則最新狀態的摘要資訊。 |
> |  | **原則追蹤資源/查詢結果** |  |
> | 動作 | Microsoft.PolicyInsights/policyTrackedResources/queryResults/read | 查詢 DeployIfNotExists 原則所需之資源的相關資訊。 |
> |  | **補救** |  |
> | 動作 | Microsoft.PolicyInsights/remediations/read | 取得原則補救。 |
> | 動作 | Microsoft.PolicyInsights/remediations/write | 創建或更新 Microsoft 策略修正。 |
> | 動作 | Microsoft.PolicyInsights/remediations/delete | 刪除原則補救。 |
> | 動作 | Microsoft.PolicyInsights/remediations/cancel/action | 取消正在進行的 Microsoft 策略修正。 |
> |  | **修正/清單部署** |  |
> | 動作 | Microsoft.PolicyInsights/remediations/listDeployments/read | 列出原則補救所需的部署。 |
> | DataAction | 微軟.政策見解/檢查數據策略合規性/行動 | 根據數據策略檢查給定元件的符合性狀態。 |
> |  | **policyEvents** |  |
> | DataAction | 微軟.政策見解/策略事件/日誌數據事件/操作 | 記錄資源元件策略事件。 |

## <a name="microsoftportal"></a>Microsoft.Portal

Azure 服務[:Azure 門戶](../azure-portal/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.Portal/register/action | 註冊入口網站 |
> |  | **consoles** |  |
> | 動作 | Microsoft.Portal/consoles/delete | 刪除雲外殼實例。 |
> | 動作 | Microsoft.Portal/consoles/write | 創建或更新雲外殼實例。 |
> | 動作 | 微軟.門戶/控制台/讀取 | 讀取雲外殼實例。 |
> |  | **儀錶 板** |  |
> | 動作 | Microsoft.Portal/dashboards/read | 讀取訂用帳戶的儀表板。 |
> | 動作 | Microsoft.Portal/dashboards/write | 新增或修改訂用帳戶的儀表板。 |
> | 動作 | Microsoft.Portal/dashboards/delete | 從訂用帳戶移除儀表板。 |
> |  | **使用者設定** |  |
> | 動作 | Microsoft.Portal/usersettings/delete | 刪除雲殼用戶設置。 |
> | 動作 | Microsoft.Portal/usersettings/write | 創建或更新雲外殼用戶設置。 |
> | 動作 | Microsoft.Portal/usersettings/read | 讀取雲殼用戶設置。 |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

Azure 服務:[電源 BI 嵌入式](https://docs.microsoft.com/azure/power-bi-embedded/)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.PowerBIDedicated/register/action | 註冊 Power BI 專用資源提供者。 |
> |  | **capacities** |  |
> | 動作 | Microsoft.PowerBIDedicated/capacities/read | 擷取指定 Power BI 專用容量的資訊。 |
> | 動作 | Microsoft.PowerBIDedicated/capacities/write | 建立或更新指定的 Power BI 專用容量。 |
> | 動作 | Microsoft.PowerBIDedicated/capacities/delete | 刪除 Power BI 專用容量。 |
> | 動作 | Microsoft.PowerBIDedicated/capacities/suspend/action | 暫止容量。 |
> | 動作 | Microsoft.PowerBIDedicated/capacities/resume/action | 繼續容量。 |
> |  | **容量/容量** |  |
> | 動作 | Microsoft.PowerBIDedicated/capacities/skus/read | 擷取容量的可用 SKU 資訊 |
> |  | **位置** |  |
> | 動作 | 微軟.PowerBI專用/位置/檢查名稱可用性/操作 | 檢查指定的 Power BI 專用容量名稱有效且未在使用中。 |
> |  | **位置/動作結果** |  |
> | 動作 | Microsoft.PowerBIDedicated/locations/operationresults/read | 擷取指定作業結果的資訊。 |
> |  | **位置/動作狀態** |  |
> | 動作 | Microsoft.PowerBIDedicated/locations/operationstatuses/read | 擷取指定作業狀態的資訊。 |
> |  | **操作** |  |
> | 動作 | Microsoft.PowerBIDedicated/operations/read | 擷取作業的資訊 |
> |  | **斯克庫斯** |  |
> | 動作 | Microsoft.PowerBIDedicated/skus/read | 擷取 SKU 的資訊 |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

Azure 服務:[站台復原](../site-recovery/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.RecoveryServices/register/action | 為指定的資源提供者註冊訂用帳戶 |
> |  | **位置** |  |
> | 動作 | Microsoft.RecoveryServices/Locations/backupPreValidateProtection/action |  |
> | 動作 | Microsoft.RecoveryServices/Locations/backupStatus/action | 檢查復原服務保存庫的備份狀態 |
> | 動作 | Microsoft.RecoveryServices/Locations/backupValidateFeatures/action | 驗證功能 |
> | 動作 | Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp 是服務所使用的內部作業 |
> | 動作 | Microsoft.RecoveryServices/locations/checkNameAvailability/action | 檢查資源名稱可用性是 API，以檢查是否有可用的資源名稱 |
> |  | **位置/已配置戳** |  |
> | 動作 | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp 是服務所使用的內部作業 |
> |  | **位置/備份保護專案** |  |
> | 動作 | 微軟.恢復服務/位置/備份保護專案/寫入 | 建立備用的受保護項目 |
> |  | **位置/備份保護專案** |  |
> | 動作 | 微軟.恢復服務/位置/備份保護專案/讀取 | 傳回所有受保護項目的清單。 |
> |  | **位置/動作狀態** |  |
> | 動作 | Microsoft.RecoveryServices/locations/operationStatus/read | 取得給定作業的作業狀態 |
> |  | **操作** |  |
> | 動作 | Microsoft.RecoveryServices/operations/read | 作業會傳回資源提供者的作業清單 |
> |  | **金庫** |  |
> | 動作 | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | 匯出作業 |
> | 動作 | Microsoft.RecoveryServices/Vaults/backupSecurityPIN/action | 傳回復原服務保存庫的安全性 PIN 碼資訊。 |
> | 動作 | Microsoft.RecoveryServices/Vaults/backupValidateOperation/action | 驗證受保護項目上的作業 |
> | 動作 | Microsoft.RecoveryServices/Vaults/write | 「建立保存庫」作業會建立 'vault' 類型的 Azure 資源 |
> | 動作 | Microsoft.RecoveryServices/Vaults/read | 「取得保存庫」作業會取得物件，此物件代表 'vault' 類型的 Azure 資源 |
> | 動作 | Microsoft.RecoveryServices/Vaults/delete | 「刪除保存庫」作業會刪除 'vault' 類型的指定 Azure 資源 |
> |  | **保險庫/備份設定** |  |
> | 動作 | Microsoft.RecoveryServices/Vaults/backupconfig/read | 傳回復原服務保存庫的組態。 |
> | 動作 | Microsoft.RecoveryServices/Vaults/backupconfig/write | 更新復原服務保存庫的組態。 |
> |  | **保存庫/備份加密設定** |  |
> | 動作 | 微軟.恢復服務/Vault/備份加密配置/讀取 | 獲取備份資源加密配置。 |
> | 動作 | 微軟.恢復服務/Vault/備份加密配置/寫入 | 更新備份資源加密設定 |
> |  | **保險庫/備用引擎** |  |
> | 動作 | Microsoft.RecoveryServices/Vaults/backupEngines/read | 傳回已向保存庫註冊的所有備份管理伺服器。 |
> |  | **保存結構** |  |
> | 動作 | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | 重新整理容器清單 |
> |  | **保險庫/備份結構/備份保護意圖** |  |
> | 動作 | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/delete | 刪除備份保護用途 |
> | 動作 | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read | 取得備份保護用途 |
> | 動作 | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | 建立備份保護用途 |
> |  | **保存庫/備份結構/操作結果** |  |
> | 動作 | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | 傳回作業的狀態 |
> |  | **保存庫/備份結構/操作狀態** |  |
> | 動作 | 微軟.恢復服務/Vault/備份結構/操作狀態/讀取 | 傳回作業的狀態 |
> |  | **保險庫/備用結構/可保護容器** |  |
> | 動作 | Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read | 取得所有可保護的容器 |
> |  | **保險庫/備用結構/保護容器** |  |
> | 動作 | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/delete | 刪除已註冊的容器 |
> | 動作 | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/inquire/action | 執行容器內工作負載的查詢 |
> | 動作 | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | 傳回所有已註冊的容器 |
> | 動作 | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/write | 建立已註冊的容器 |
> |  | **保存結構/保護容器/專案** |  |
> | 動作 | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | 取得容器中的所有項目 |
> |  | **保存結構/保護容器/操作結果** |  |
> | 動作 | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | 取得對保護容器執行之作業的結果。 |
> |  | **保存結構/保護容器/操作狀態** |  |
> | 動作 | 微軟.恢復服務/Vault/備份結構/保護容器/操作狀態/讀取 | 獲取在保護容器上執行的操作狀態。 |
> |  | **保存庫/備份結構/保護容器/受保護專案** |  |
> | 動作 | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | 對受保護的項目執行備份。 |
> | 動作 | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/delete | 刪除受保護的項目 |
> | 動作 | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | 傳回受保護項目的物件詳細資料 |
> | 動作 | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | 建立備用的受保護項目 |
> |  | **保存庫/備份結構/保護容器/受保護專案/操作結果** |  |
> | 動作 | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | 取得對受保護項目執行之作業的結果。 |
> |  | **保存庫/備份結構/保護容器/受保護專案/操作狀態** |  |
> | 動作 | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | 傳回對受保護項目執行之作業的狀態。 |
> |  | **保存庫/備份結構/保護容器/受保護項目/恢復點** |  |
> | 動作 | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | 為受保護的項目佈建即時項目復原 |
> | 動作 | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | 取得受保護項目的復原點。 |
> | 動作 | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | 還原受保護項目的復原點。 |
> | 動作 | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | 為受保護的項目撤銷即時項目復原 |
> |  | **保存庫/備份作業** |  |
> | 動作 | Microsoft.RecoveryServices/Vaults/backupJobs/cancel/action | 取消作業 |
> | 動作 | Microsoft.RecoveryServices/Vaults/backupJobs/read | 傳回所有作業物件 |
> |  | **保存庫/備份作業/操作結果** |  |
> | 動作 | Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | 傳回作業的作業結果。 |
> |  | **保存庫/備份作業/操作狀態** |  |
> | 動作 | 微軟.恢復服務/Vault/備份作業/操作狀態/讀取 | 返回作業操作的狀態。 |
> |  | **保存庫/備份操作結果** |  |
> | 動作 | Microsoft.RecoveryServices/Vaults/backupOperationResults/read | 傳回復原服務保存庫的備份作業結果。 |
> |  | **保存庫/備份操作** |  |
> | 動作 | Microsoft.RecoveryServices/Vaults/backupOperations/read | 傳回復原服務保存庫的備份作業狀態。 |
> |  | **保存庫/備份原則** |  |
> | 動作 | Microsoft.RecoveryServices/Vaults/backupPolicies/delete | 刪除保護原則 |
> | 動作 | Microsoft.RecoveryServices/Vaults/backupPolicies/read | 傳回所有保護原則 |
> | 動作 | Microsoft.RecoveryServices/Vaults/backupPolicies/write | 建立保護原則 |
> |  | **保存庫/備份政策/操作結果** |  |
> | 動作 | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | 取得原則作業的結果。 |
> |  | **保存庫/備份政策/操作** |  |
> | 動作 | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | 取得原則作業的狀態。 |
> |  | **保存庫/備份可保護專案** |  |
> | 動作 | Microsoft.RecoveryServices/Vaults/backupProtectableItems/read | 傳回所有可保護項目的清單。 |
> |  | **保存庫/備份保護專案** |  |
> | 動作 | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | 傳回所有受保護項目的清單。 |
> |  | **保存管庫/備用保護容器** |  |
> | 動作 | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | 傳回屬於訂用帳戶的所有容器 |
> |  | **保管庫/備份保護意圖** |  |
> | 動作 | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | 列出所有的備份保護用途 |
> |  | **保存庫/備份儲存配置** |  |
> | 動作 | Microsoft.RecoveryServices/Vaults/backupstorageconfig/read | 傳回復原服務保存庫的儲存體組態。 |
> | 動作 | Microsoft.RecoveryServices/Vaults/backupstorageconfig/write | 更新復原服務保存庫的儲存體組態。 |
> |  | **保存庫/備份使用摘要** |  |
> | 動作 | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | 傳回復原服務之受保護項目和受保護伺服器的摘要。 |
> |  | **保存庫/憑證** |  |
> | 動作 | Microsoft.RecoveryServices/Vaults/certificates/write | 「更新資源憑證」作業會更新資源/保存庫的認證憑證。 |
> |  | **保險庫/擴展資訊** |  |
> | 動作 | Microsoft.RecoveryServices/Vaults/extendedInformation/read | 「取得延伸資訊」作業會取得物件的延伸資訊，此延伸資訊代表 'vault' 類型的 Azure 資源 |
> | 動作 | Microsoft.RecoveryServices/Vaults/extendedInformation/write | 「取得延伸資訊」作業會取得物件的延伸資訊，此延伸資訊代表 'vault' 類型的 Azure 資源 |
> | 動作 | Microsoft.RecoveryServices/Vaults/extendedInformation/delete | 「取得延伸資訊」作業會取得物件的延伸資訊，此延伸資訊代表 'vault' 類型的 Azure 資源 |
> |  | **保管庫/監控警報** |  |
> | 動作 | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | 取得復原服務保存庫的警示。 |
> | 動作 | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | 解決警示。 |
> |  | **保存庫/監控組態** |  |
> | 動作 | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/read | 取得復原服務保存庫通知組態。 |
> | 動作 | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/write | 設定復原服務保存庫的電子郵件通知。 |
> |  | **保存庫/專用端接連線 Proxies** |  |
> | 動作 | 微軟.恢復服務/Vault/私人終端連接Proxies/刪除 | 等候幾分鐘的時間，然後再次嘗試操作。 如果問題持續發生， 請連絡 Microsoft 支援服務。 |
> | 動作 | 微軟.恢復服務/Vaults/私人端接連接Proxies/讀取 | 取得所有可保護的容器 |
> | 動作 | 微軟.恢復服務/Vault/私人終端連接Proxies/驗證/操作 | 取得所有可保護的容器 |
> | 動作 | 微軟.恢復服務/Vaults/私有端接連接Proxies/寫入 | 取得所有可保護的容器 |
> |  | **保存庫/專用端接連線Proxies/操作狀態** |  |
> | 動作 | 微軟.恢復服務/Vault/私人終端連接Proxies/操作狀態/讀取 | 取得所有可保護的容器 |
> |  | **保存庫/專用端點連接** |  |
> | 動作 | 微軟.恢復服務/Vault/私有端點連接/刪除 | 刪除專用終結點請求。 此調用由備份管理員進行。 |
> | 動作 | 微軟.恢復服務/Vault/私有端點連接/寫入 | 批准或拒絕專用終結點請求。 此調用由備份管理員進行。 |
> |  | **保存庫/專用端點連接/操作狀態** |  |
> | 動作 | 微軟.恢復服務/Vault/私有端點連接/操作狀態/讀取 | 返回專用終結點連接的操作狀態。 |
> |  | **保管庫/註冊標識** |  |
> | 動作 | Microsoft.RecoveryServices/Vaults/registeredIdentities/write | 「註冊服務容器」作業可用來向復原服務註冊容器。 |
> | 動作 | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | 「取得容器」作業可用來取得為資源註冊的容器。 |
> | 動作 | Microsoft.RecoveryServices/Vaults/registeredIdentities/delete | 「取消註冊容器」作業可用來取消註冊容器。 |
> |  | **保管庫/註冊標識/操作結果** |  |
> | 動作 | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | 「取得作業結果」作業可用來取得以非同步方式提交之作業的作業狀態和結果 |
> |  | **保存庫/複製警示設定** |  |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | 讀取任何警示設定 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationAlertSettings/write | 建立或更新任何警示設定 |
> |  | **保存庫/複製事件** |  |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationEvents/read | 讀取任何事件 |
> |  | **保存結構** |  |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/read | 讀取任何網狀架構 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/write | 建立或更新任何網狀架構 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/remove/action | 移除網狀架構 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | 檢查網狀架構的一致性 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/delete | 刪除任何網狀架構 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | 更新網狀架構的憑證 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/deployProcessServerImage/action | 部署處理序伺服器的映像 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | 重新關聯閘道 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/migratetoaad/action | 將網狀架構遷移至 AAD |
> |  | **保存結構/動作結果** |  |
> | 動作 | 微軟.恢復服務/保管庫/複製結構/操作結果/讀取 | 追蹤資源結構上的非同步操作的結果 |
> |  | **保存邏輯網路** |  |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationLogicalNetworks/read | 讀取任何邏輯網路 |
> |  | **保存式庫/複製結構/複製網路** |  |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | 讀取任何網路 |
> |  | **保存網路/複製結構/複製網路/複製網路映射** |  |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | 讀取任何網路對應 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/write | 建立或更新任何網路對應 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/delete | 刪除任何網路對應 |
> |  | **保存式庫/複製結構/ 複製保護容器** |  |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | 讀取任何保護容器 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/discoverProtectableItem/action | 探索可保護的項目 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/write | 建立或更新任何保護容器 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/remove/action | 移除保護容器 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action | 切換保護容器 |
> |  | **保存管庫/複製結構/ 複製保護容器/ 動作結果** |  |
> | 動作 | 微軟.恢復服務/保管庫/複製結構/複製保護容器/操作結果/讀取 | 追蹤資源保護容器上的非同步操作的結果 |
> |  | **保存管理的庫/複製結構/ 複製保護容器/ 複製移至項目** |  |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/read | 讀取任何移轉項目 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/write | 建立或更新任何移轉項目 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/delete | 刪除任何移轉項目 |
> | 動作 | Microsoft.恢復服務/保管庫/複製結構/複製保護容器/複製遷移專案/重新同步/操作 | 重新同步處理 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/migrate/action | 遷移資料 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/testMigrate/action | 測試遷移 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/testMigrateCleanup/action | 測試遷移清除 |
> |  | **保存管庫/複製結構/ 複製保護容器/ 複製移至項目或移至分割** |  |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/migrationRecoveryPoints/read | 讀取任何移轉復原點 |
> |  | **保存管理容器/複製結構/ 複製保護容器/ 複製移至專案/操作結果** |  |
> | 動作 | Microsoft.恢復服務/保管庫/複製結構/複製保護容器/複製遷移專案/操作結果/讀取 | 追蹤資源移轉項目上的非同步操作的結果 |
> |  | **保存式庫/複製結構/ 複製保護容器/ 可保護專案** |  |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | 讀取任何可保護的項目 |
> |  | **保存式庫/複製結構/ 複製保護容器/ 複製保護專案** |  |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | 讀取任何受保護的項目 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/write | 建立或更新任何受保護的項目 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/delete | 刪除任何受保護的項目 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/remove/action | 移除受保護的項目 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | 計劃性容錯移轉 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | 容錯移轉 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | Test Failover |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | 測試容錯移轉清理 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | 容錯移轉認可 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | 重新保護受保護的項目 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | 更新行動服務 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | 修復複寫 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | 套用復原點 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/submitFeedback/action | 提交意見 |
> | 動作 | Microsoft.恢復服務/保管庫/複製結構/複製保護容器/複製保護專案/添加磁碟/操作 | 新增磁碟 |
> | 動作 | Microsoft.恢復服務/保管庫/複製結構/複製保護容器/複製保護專案/刪除磁碟/操作 | 移除磁碟 |
> | 動作 | Microsoft.恢復服務/保管庫/複製結構/複製保護容器/複製保護專案/解決運行狀況錯誤/操作 |  |
> |  | **保存管理的庫/複製結構/ 複製保護容器/ 複製保護專案/ 操作結果** |  |
> | 動作 | Microsoft.恢復服務/保管庫/複製結構/複製保護容器/複製保護專案/操作結果/讀取 | 追蹤資源受保護項上的非同步操作的結果 |
> |  | **保存管理的庫/複製結構/ 複製保護容器/ 複製保護項目/ 復原點** |  |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | 讀取任何複寫復原點 |
> |  | **保存式庫/複製結構/ 複製保護容器/ 複製保護項目/ 目標計算大小** |  |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/targetComputeSizes/read | 讀取任何目標計算大小 |
> |  | **保存式庫/複製結構/ 複製保護容器/ 複製保護容器映射** |  |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | 讀取任何保護容器對應 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/write | 建立或更新任何保護容器對應 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/remove/action | 移除保護容器對應 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/delete | 刪除任何保護容器對應 |
> |  | **保存式庫/複製結構/ 複製保護容器/ 複製保護容器映射/動作結果** |  |
> | 動作 | Microsoft.恢復服務/保管庫/複製結構/複製保護容器/複製保護容器映射/操作結果/讀取 | 追蹤資源保護容器映射上的非同步動作的結果 |
> |  | **保存庫/複製結構/複製回復服務提供者** |  |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | 讀取任何復原服務提供者 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/write | 建立或更新任何復原服務提供者 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/remove/action | 移除復原服務提供者 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/delete | 刪除任何復原服務提供者 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action | 重新整理提供者 |
> |  | **保存庫/複製結構/複製回復服務供應商/操作結果** |  |
> | 動作 | 微軟.恢復服務/保管庫/複製結構/複製恢復服務提供者/操作結果/讀取 | 追蹤資源復原服務提供者上的非同步操作的結果 |
> |  | **保存庫/複製結構/複製儲存類別** |  |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | 讀取任何存放裝置分類 |
> |  | **保存庫/複製結構/複製儲存類別/複製儲存分類映射** |  |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | 讀取任何存放裝置分類對應 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/write | 建立或更新任何存放裝置分類對應 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/delete | 刪除任何存放裝置分類對應 |
> |  | **保存庫/複製結構/複製儲存分類/複製儲存分類映射/動作結果** |  |
> | 動作 | Microsoft.恢復服務/保管庫/複製結構/複製存儲分類/複製存儲分類映射/操作結果/讀取 | 追蹤資源儲存類別映射上的非同步操作的結果 |
> |  | **保存結構/複製中心** |  |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | 讀取任何 vCenter |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/write | 建立或更新任何 vCenter |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/delete | 刪除任何 vCenter |
> |  | **保存中心/動作結果** |  |
> | 動作 | 微軟.恢復服務/保管庫/複製結構/複製中心/操作結果/讀取 | 追蹤資源 vCenter 上的非同步操作的結果 |
> |  | **保存工作** |  |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationJobs/read | 讀取任何作業 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationJobs/cancel/action | 取消作業 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationJobs/restart/action | 重新啟動作業 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationJobs/resume/action | 繼續作業 |
> |  | **保存工作或管理程式** |  |
> | 動作 | 微軟.恢復服務/保管庫/複製作業/操作結果/讀取 | 追蹤資源作業上的非同步操作的結果 |
> |  | **保存庫/複製移至項目** |  |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationMigrationItems/read | 讀取任何移轉項目 |
> |  | **保存網路映射** |  |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationNetworkMappings/read | 讀取任何網路對應 |
> |  | **保存網路** |  |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationNetworks/read | 讀取任何網路 |
> |  | **保存管庫/複製操作狀態** |  |
> | 動作 | 微軟.恢復服務/保管庫/複製操作狀態/讀取 | 讀取任何保管庫複製操作狀態 |
> |  | **保存庫/複製原則** |  |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationPolicies/read | 讀取任何原則 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationPolicies/write | 建立或更新任何原則 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationPolicies/delete | 刪除任何原則 |
> |  | **保存檔/複製原則/動作結果** |  |
> | 動作 | 微軟.恢復服務/保管庫/複製策略/操作結果/讀取 | 追蹤資源政策上的非同步操作的結果 |
> |  | **保存分割項目** |  |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationProtectedItems/read | 讀取任何受保護的項目 |
> |  | **保存式容器映射** |  |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationProtectionContainerMappings/read | 讀取任何保護容器對應 |
> |  | **保存管理容器** |  |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationProtectionContainers/read | 讀取任何保護容器 |
> |  | **保存庫/複製復原計劃** |  |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | 讀取任何復原方案 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/write | 建立或更新任何復原方案 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/delete | 刪除任何復原方案 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action | 計劃性容錯移轉復原方案 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action | 容錯移轉復原方案 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | 測試容錯移轉復原方案 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action | 測試容錯移轉清理復原方案 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action | 容錯移轉認可復原方案 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | 重新保護復原方案 |
> |  | **保存庫/複製復原計劃/操作結果** |  |
> | 動作 | 微軟.恢復服務/保管庫/複製恢復計劃/操作結果/讀取 | 追蹤資源復原排程的非同步操作的結果 |
> |  | **保存管理員服務提供者** |  |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationRecoveryServicesProviders/read | 讀取任何復原服務提供者 |
> |  | **保存儲存類別映射** |  |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationStorageClassificationMappings/read | 讀取任何存放裝置分類對應 |
> |  | **保存庫碼庫** |  |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationStorageClassifications/read | 讀取任何存放裝置分類 |
> |  | **保存支援的作業系統** |  |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationSupportedOperatingSystems/read | 讀取任何  |
> |  | **保存庫/複製法** |  |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationUsages/read | 讀取任何保存庫複寫使用量 |
> |  | **保存庫狀態** |  |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationVaultHealth/read | 讀取任何保存庫複寫健康情況 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationVaultHealth/refresh/action | 重新整理保存庫健康情況 |
> |  | **保存庫/複製Vault執行狀況/動作結果** |  |
> | 動作 | 微軟.恢復服務/保管庫/複製Vault運行狀況/操作結果/讀取 | 追蹤資源保存式管庫複製執行狀況上的非同步的動作的結果 |
> |  | **保存庫名設定** |  |
> | 動作 | 微軟.恢復服務/保管庫/複製Vault設置/讀取 | 讀取任何  |
> | 動作 | 微軟.恢復服務/保管庫/複製Vault設置/寫入 | 建立或更新任何  |
> |  | **保存中心** |  |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationvCenters/read | 讀取任何 vCenter |
> |  | **保存庫/使用方式** |  |
> | 動作 | Microsoft.RecoveryServices/Vaults/usages/read | 傳回復原服務保存庫的使用量詳細資料。 |
> | 動作 | Microsoft.RecoveryServices/vaults/usages/read | 讀取任何保存庫使用量 |
> |  | **保存庫/保管庫權杖** |  |
> | 動作 | Microsoft.RecoveryServices/Vaults/vaultTokens/read | 「保存庫權杖」作業可用來取得保存庫層級後端作業的保存庫權杖。 |

## <a name="microsoftrelay"></a>Microsoft.Relay

Azure 服務[:Azure 中繼](../service-bus-relay/relay-what-is-it.md)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.Relay/checkNamespaceAvailability/action | 檢查給定訂用帳戶下的命名空間是否可用。 此 API 即將淘汰。請改用 CheckNameAvailabiltiy。 |
> | 動作 | Microsoft.Relay/checkNameAvailability/action | 檢查給定訂用帳戶下的命名空間是否可用。 |
> | 動作 | Microsoft.Relay/register/action | 針對轉送資源提供者註冊訂用帳戶，並讓您能夠建立轉送資源 |
> | 動作 | Microsoft.Relay/unregister/action | 針對轉送資源提供者註冊訂用帳戶，並讓您能夠建立轉送資源 |
> |  | **namespaces** |  |
> | 動作 | Microsoft.Relay/namespaces/write | 建立命名空間資源，並更新其屬性。 命名空間的標記和容量是可以更新的屬性。 |
> | 動作 | Microsoft.Relay/namespaces/read | 取得命名空間資源描述的清單 |
> | 動作 | Microsoft.Relay/namespaces/Delete | 刪除命名空間資源 |
> | 動作 | Microsoft.Relay/namespaces/authorizationRules/action | 更新命名空間授權規則。 此 API 即將淘汰。 請改用 PUT 呼叫來更新命名空間授權規則。 API 版本 2017-04-01 不支援此作業。 |
> | 動作 | Microsoft.Relay/namespaces/removeAcsNamepsace/action | 移除 ACS 命名空間 |
> |  | **命名空間/授權規則** |  |
> | 動作 | Microsoft.Relay/namespaces/authorizationRules/read | 取得命名空間授權規則描述的清單。 |
> | 動作 | Microsoft.Relay/namespaces/authorizationRules/write | 建立命名空間層級的授權規則，並更新其屬性。 您可以更新授權規則的存取權限、主要金鑰和次要金鑰。 |
> | 動作 | Microsoft.Relay/namespaces/authorizationRules/delete | 刪除命名空間授權規則。 您無法刪除預設的命名空間授權規則。  |
> | 動作 | Microsoft.Relay/namespaces/authorizationRules/listkeys/action | 取得命名空間的連接字串 |
> | 動作 | Microsoft.Relay/namespaces/authorizationRules/regenerateKeys/action | 對資源重新產生主要或次要金鑰 |
> |  | **命名空間/災難修復設定** |  |
> | 動作 | Microsoft.Relay/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | 檢查指定訂用帳戶下命名空間別名的可用性。 |
> | 動作 | Microsoft.Relay/namespaces/disasterRecoveryConfigs/write | 建立或更新與命名空間相關聯的災害復原設定。 |
> | 動作 | Microsoft.Relay/namespaces/disasterRecoveryConfigs/read | 取得與命名空間相關聯的災害復原設定。 |
> | 動作 | Microsoft.Relay/namespaces/disasterRecoveryConfigs/delete | 刪除與命名空間相關聯的災害復原設定。 此作業只能透過主要命名空間叫用。 |
> | 動作 | Microsoft.Relay/namespaces/disasterRecoveryConfigs/breakPairing/action | 停用災害復原，並停止將變更從主要命名空間複寫至次要命名空間。 |
> | 動作 | Microsoft.Relay/namespaces/disasterRecoveryConfigs/failover/action | 叫用異地災害復原容錯移轉，並將命名空間別名重新設定為指向次要命名空間。 |
> |  | **命名空間/災難修復設定/授權規則** |  |
> | 動作 | Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules/read | 取得災害復原主要命名空間的授權規則 |
> | 動作 | Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | 取得災害復原主要命名空間的授權規則金鑰 |
> |  | **命名空間/混合連線** |  |
> | 動作 | Microsoft.Relay/namespaces/HybridConnections/write | 建立或更新 HybridConnection 屬性。 |
> | 動作 | Microsoft.Relay/namespaces/HybridConnections/read | 取得 HybridConnection 資源描述的清單 |
> | 動作 | Microsoft.Relay/namespaces/HybridConnections/Delete | 用來刪除 HybridConnection 資源的作業 |
> | 動作 | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/action | 更新 HybridConnection 的作業。 API 版本 2017-04-01 不支援此作業。 授權規則。 請使用 PUT 呼叫來更新授權規則。 |
> |  | **命名空間/混合連線/授權規則** |  |
> | 動作 | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/read |  取得 HybridConnection 授權規則的清單 |
> | 動作 | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/write | 建立 HybridConnection 授權規則，並更新其屬性。 您可以更新授權規則存取權限。 |
> | 動作 | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/delete | 用來刪除 HybridConnection 授權規則的作業 |
> | 動作 | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/listkeys/action | 取得 HybridConnection 的連接字串 |
> | 動作 | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/regeneratekeys/action | 對資源重新產生主要或次要金鑰 |
> |  | **命名空間/訊息計劃** |  |
> | 動作 | Microsoft.Relay/namespaces/messagingPlan/read | 取得命名空間的傳訊方案。<br>此 API 即將淘汰。<br>透過 MessagingPlan 資源公開的屬性，已移至新版 API 的 (父系) 命名空間資源。<br>API 版本 2017-04-01 不支援此作業。 |
> | 動作 | Microsoft.Relay/namespaces/messagingPlan/write | 更新命名空間的傳訊方案。<br>此 API 即將淘汰。<br>透過 MessagingPlan 資源公開的屬性，已移至新版 API 的 (父系) 命名空間資源。<br>API 版本 2017-04-01 不支援此作業。 |
> |  | **命名空間/網路規則集** |  |
> | 動作 | 微軟.中繼/命名空間/網路規則集/讀取 | 取得網路規則集資源 |
> | 動作 | 微軟.中繼/命名空間/網路規則集/寫入 | 建立 VNET 規則資源 |
> | 動作 | 微軟.中繼/命名空間/網路規則集/刪除 | 刪除 VNET 規則資源 |
> |  | **命名空間/動作結果** |  |
> | 動作 | Microsoft.Relay/namespaces/operationresults/read | 取得命名空間作業的狀態 |
> |  | **命名空間/專用端接Proxies** |  |
> | 動作 | 微軟.中繼/命名空間/私人端接連接Proxies/驗證/操作 | 驗證專用終結點連線代理 |
> | 動作 | 微軟.中繼/命名空間/私人端接連接Proxies/讀取 | 取得專用終結點連線代理 |
> | 動作 | 微軟.中繼/命名空間/私人端接連接Proxies/寫入 | 建立專用終結點連線代理 |
> | 動作 | 微軟.中繼/命名空間/私人端接連接Proxies/刪除 | 移除專用終結點連線代理 |
> |  | **命名空間/供應商/微軟.見解/診斷設置** |  |
> | 動作 | 微軟.中繼/命名空間/供應商/微軟.見解/診斷設置/讀取 | 取得命名空間診斷設定資源描述的清單 |
> | 動作 | 微軟.中繼/命名空間/供應商/微軟.見解/診斷設置/寫入 | 取得命名空間診斷設定資源描述的清單 |
> |  | **命名空間/提供者/微軟.見解/日誌定義** |  |
> | 動作 | 微軟.中繼/命名空間/供應商/微軟.見解/日誌定義/讀取 | 取得命名空間記錄資源描述的清單 |
> |  | **命名空間/提供者/微軟.見解/指標定義** |  |
> | 動作 | Microsoft.Relay/namespaces/providers/Microsoft.Insights/metricDefinitions/read | 取得命名空間計量資源描述的清單 |
> |  | **命名空間/WcfRelays** |  |
> | 動作 | Microsoft.Relay/namespaces/WcfRelays/write | 建立或更新 WcfRelay 屬性。 |
> | 動作 | Microsoft.Relay/namespaces/WcfRelays/read | 取得 WcfRelay 資源描述的清單 |
> | 動作 | Microsoft.Relay/namespaces/WcfRelays/Delete | 用來刪除 WcfRelay 資源的作業 |
> | 動作 | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/action | 更新 WcfRelay 的作業。 API 版本 2017-04-01 不支援此作業。 授權規則。 請使用 PUT 呼叫來更新授權規則。 |
> |  | **命名空間/WcfRelay/授權規則** |  |
> | 動作 | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/read |  取得 WcfRelay 授權規則的清單 |
> | 動作 | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/write | 建立 WcfRelay 授權規則，並更新其屬性。 您可以更新授權規則存取權限。 |
> | 動作 | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/delete | 用來刪除 WcfRelay 授權規則的作業 |
> | 動作 | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/listkeys/action | 取得 WcfRelay 的連接字串 |
> | 動作 | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/regeneratekeys/action | 對資源重新產生主要或次要金鑰 |
> |  | **操作** |  |
> | 動作 | Microsoft.Relay/operations/read | 取得作業 |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

Azure 服務[:Azure 服務執行狀況](../service-health/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.ResourceHealth/register/action | 為 Microsoft ResourceHealth 註冊訂用帳戶 |
> | 動作 | Microsoft.ResourceHealth/unregister/action | 取消註冊 Microsoft ResourceHealth 的訂用帳戶 |
> | 動作 | Microsoft.Resourcehealth/healthevent/action | 代表指定資源健康狀態的變更 |
> |  | **可用性狀態** |  |
> | 動作 | Microsoft.ResourceHealth/AvailabilityStatuses/read | 取得指定範圍中所有資源的可用性狀態 |
> |  | **可用性狀態/目前** |  |
> | 動作 | Microsoft.ResourceHealth/AvailabilityStatuses/current/read | 取得指定資源的可用性狀態 |
> |  | **新興問題** |  |
> | 動作 | 微軟.資源健康/新興問題/閱讀 | 取得 Azure 服務的新興問題 |
> |  | **事件** |  |
> | 動作 | Microsoft.ResourceHealth/events/read | 取得指定訂用帳戶的服務健康狀態事件 |
> |  | **健康事件** |  |
> | 動作 | Microsoft.Resourcehealth/healthevent/Activated/action | 代表指定資源健康狀態的變更 |
> | 動作 | Microsoft.Resourcehealth/healthevent/Updated/action | 代表指定資源健康狀態的變更 |
> | 動作 | Microsoft.Resourcehealth/healthevent/Resolved/action | 代表指定資源健康狀態的變更 |
> | 動作 | Microsoft.Resourcehealth/healthevent/InProgress/action | 代表指定資源健康狀態的變更 |
> | 動作 | Microsoft.Resourcehealth/healthevent/Pending/action | 代表指定資源健康狀態的變更 |
> |  | **impactedResources** |  |
> | 動作 | Microsoft.ResourceHealth/impactedResources/read | 取得指定訂用帳戶的受影響資源 |
> |  | **元** |  |
> | 動作 | 微軟.資源健康/中繼資料/讀取 | 取得中繼資料 |
> |  | **通知** |  |
> | 動作 | 微軟.資源運行狀況/通知/讀取 | 接收 Azure 資源管理員通知 |
> |  | **作業** |  |
> | 動作 | Microsoft.ResourceHealth/Operations/read | 取得 Microsoft ResourceHealth 可用的作業 |

## <a name="microsoftresources"></a>Microsoft.Resources

Azure 服務[:Azure 資源管理員](../azure-resource-manager/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.Resources/checkResourceName/action | 檢查資源名稱是否有效。 |
> | 動作 | 微軟.資源/計算範本哈希/操作 | 計算提供的範本的哈希。 |
> |  | **檢查政策合規性** |  |
> | 動作 | 微軟.資源/檢查政策合規性/閱讀 | 檢查指定的資源是否符合資源原則，以了解合規性狀態。 |
> |  | **部署** |  |
> | 動作 | Microsoft.Resources/deployments/read | 取得或列出部署。 |
> | 動作 | Microsoft.Resources/deployments/write | 建立或更新部署。 |
> | 動作 | Microsoft.Resources/deployments/delete | 刪除部署。 |
> | 動作 | Microsoft.Resources/deployments/cancel/action | 取消部署。 |
> | 動作 | Microsoft.Resources/deployments/validate/action | 驗證部署。 |
> | 動作 | 微軟.資源/部署/什麼如果/操作 | 預測範本部署更改。 |
> | 動作 | 微軟.資源/部署/匯出範本/操作 | 部署匯出樣本 |
> |  | **部署/操作** |  |
> | 動作 | Microsoft.Resources/deployments/operations/read | 取得或列出部署作業。 |
> |  | **部署/操作狀態** |  |
> | 動作 | Microsoft.資源/部署/操作狀態/讀取 | 取得或列出部署作業狀態。 |
> |  | **部署文稿** |  |
> | 動作 | 微軟.資源/部署腳本/讀取 | 取得或列出部署文稿 |
> | 動作 | 微軟.資源/部署文本/寫入 | 建立或更新部署文稿 |
> | 動作 | 微軟.資源/部署文稿/刪除 | 刪除部署文稿 |
> |  | **部署文稿/紀錄** |  |
> | 動作 | 微軟.資源/部署腳本/日誌/讀取 | 取得或列出部署文稿紀錄 |
> |  | **連結** |  |
> | 動作 | Microsoft.Resources/links/read | 取得或列出資源連結。 |
> | 動作 | Microsoft.Resources/links/write | 建立或更新資源連結。 |
> | 動作 | Microsoft.Resources/links/delete | 刪除資源連結。 |
> |  | **marketplace** |  |
> | 動作 | Microsoft.Resources/marketplace/purchase/action | 從 Marketplace 購買資源。 |
> |  | **供應商** |  |
> | 動作 | Microsoft.Resources/providers/read | 取得提供者清單。 |
> |  | **資源** |  |
> | 動作 | Microsoft.Resources/resources/read | 根據篩選來取得資源清單。 |
> |  | **訂閱** |  |
> | 動作 | Microsoft.Resources/subscriptions/read | 取得訂用帳戶清單。 |
> |  | **訂閱/位置** |  |
> | 動作 | Microsoft.Resources/subscriptions/locations/read | 取得所支援位置的清單。 |
> |  | **訂閱/動作結果** |  |
> | 動作 | Microsoft.Resources/subscriptions/operationresults/read | 取得訂用帳戶作業結果。 |
> |  | **訂閱/提供者** |  |
> | 動作 | Microsoft.Resources/subscriptions/providers/read | 取得或列出資源提供者。 |
> |  | **subscriptions/resourceGroups** |  |
> | 動作 | Microsoft.Resources/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | 動作 | Microsoft.Resources/subscriptions/resourceGroups/write | 建立或更新資源群組。 |
> | 動作 | Microsoft.Resources/subscriptions/resourceGroups/delete | 刪除資源群組和其所有資源。 |
> | 動作 | Microsoft.Resources/subscriptions/resourceGroups/moveResources/action | 將資源從某個資源群組移到另一個資源群組。 |
> | 動作 | Microsoft.Resources/subscriptions/resourceGroups/validateMoveResources/action | 驗證資源從某個資源群組移到另一個資源群組的動作。 |
> |  | **訂閱/資源群組/部署** |  |
> | 動作 | Microsoft.Resources/subscriptions/resourcegroups/deployments/read | 取得或列出部署。 |
> | 動作 | Microsoft.Resources/subscriptions/resourcegroups/deployments/write | 建立或更新部署。 |
> |  | **訂閱/資源組/部署/操作** |  |
> | 動作 | Microsoft.Resources/subscriptions/resourcegroups/deployments/operations/read | 取得或列出部署作業。 |
> |  | **訂閱/資源群組/部署/操作狀態** |  |
> | 動作 | Microsoft.Resources/subscriptions/resourcegroups/deployments/operationstatuses/read | 取得或列出部署作業狀態。 |
> |  | **訂閱/資源群組/資源** |  |
> | 動作 | Microsoft.Resources/subscriptions/resourcegroups/resources/read | 取得資源群組的資源。 |
> |  | **訂閱/資源** |  |
> | 動作 | Microsoft.Resources/subscriptions/resources/read | 取得訂用帳戶的資源。 |
> |  | **訂閱/標籤名稱** |  |
> | 動作 | Microsoft.Resources/subscriptions/tagNames/read | 取得或列出訂用帳戶標記。 |
> | 動作 | Microsoft.Resources/subscriptions/tagNames/write | 新增訂用帳戶標記。 |
> | 動作 | Microsoft.Resources/subscriptions/tagNames/delete | 刪除訂用帳戶標記。 |
> |  | **訂閱/標籤名稱/標籤值** |  |
> | 動作 | Microsoft.Resources/subscriptions/tagNames/tagValues/read | 取得或列出訂用帳戶標記值。 |
> | 動作 | Microsoft.Resources/subscriptions/tagNames/tagValues/write | 新增訂用帳戶標記值。 |
> | 動作 | Microsoft.Resources/subscriptions/tagNames/tagValues/delete | 刪除訂用帳戶標記值。 |
> |  | **「標記」** |  |
> | 動作 | 微軟.資源/標籤/讀取 | 獲取資源上的所有標記。 |
> | 動作 | 微軟.資源/標籤/寫入 | 通過將現有標記替換為一組新的標記或刪除現有標記,更新資源上的標記。 |
> | 動作 | 微軟.資源/標籤/刪除 | 刪除資源上的所有標記。 |
> |  | **租戶** |  |
> | 動作 | Microsoft.Resources/tenants/read | 取得租用戶清單。 |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

Azure 服務:[排程](../scheduler/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> |  | **jobcollections** |  |
> | 動作 | Microsoft.Scheduler/jobcollections/read | 取得作業集合 |
> | 動作 | Microsoft.Scheduler/jobcollections/write | 建立或更新作業集合。 |
> | 動作 | Microsoft.Scheduler/jobcollections/delete | 刪除作業集合。 |
> | 動作 | Microsoft.Scheduler/jobcollections/enable/action | 啟用作業集合。 |
> | 動作 | Microsoft.Scheduler/jobcollections/disable/action | 停用作業集合。 |
> |  | **工作收集/作業** |  |
> | 動作 | Microsoft.Scheduler/jobcollections/jobs/read | 取得作業。 |
> | 動作 | Microsoft.Scheduler/jobcollections/jobs/write | 建立或更新作業。 |
> | 動作 | Microsoft.Scheduler/jobcollections/jobs/delete | 刪除作業。 |
> | 動作 | Microsoft.Scheduler/jobcollections/jobs/run/action | 執行作業。 |
> | 動作 | Microsoft.Scheduler/jobcollections/jobs/generateLogicAppDefinition/action | 根據排程器作業來產生邏輯應用程式定義。 |
> |  | **工作收集/工作/工作記錄** |  |
> | 動作 | Microsoft.Scheduler/jobcollections/jobs/jobhistories/read | 取得工作歷程記錄。 |

## <a name="microsoftsearch"></a>Microsoft.Search

Azure 服務[:Azure 搜尋](../search/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.Search/register/action | 針對搜尋資源提供者註冊訂用帳戶，並讓您能夠建立搜尋服務。 |
> | 動作 | Microsoft.Search/checkNameAvailability/action | 檢查服務名稱的可用性。 |
> |  | **操作** |  |
> | 動作 | Microsoft.Search/operations/read | 列出 Microsoft.Search 提供者的所有可用作業。 |
> |  | **搜尋服務** |  |
> | 動作 | Microsoft.Search/searchServices/write | 建立或更新搜尋服務。 |
> | 動作 | Microsoft.Search/searchServices/read | 讀取搜尋服務。 |
> | 動作 | Microsoft.Search/searchServices/delete | 刪除搜尋服務。 |
> | 動作 | Microsoft.Search/searchServices/start/action | 啟動搜尋服務。 |
> | 動作 | Microsoft.Search/searchServices/stop/action | 停止搜尋服務。 |
> | 動作 | Microsoft.Search/searchServices/listAdminKeys/action | 讀取系統管理金鑰。 |
> | 動作 | Microsoft.Search/searchServices/regenerateAdminKey/action | 重新產生系統管理金鑰。 |
> | 動作 | 微軟.搜尋/搜尋服務/清單查詢鍵/操作 | 傳回指定 Azure 搜尋服務的查詢 API 金鑰清單。 |
> | 動作 | Microsoft.Search/searchServices/createQueryKey/action | 建立查詢金鑰。 |
> |  | **搜尋服務/刪除查詢鍵** |  |
> | 動作 | Microsoft.Search/searchServices/deleteQueryKey/delete | 刪除查詢金鑰。 |
> |  | **搜尋服務/專用端接Proxies** |  |
> | 動作 | 微軟.搜尋/搜尋服務/私人端接連接Proxies/驗證/操作 | 驗證來自 NRP 連接的專屬的檢查點 |
> | 動作 | 微軟.搜尋/搜尋服務/私人端接連接Proxies/寫入 | 使用指定參數建立專用終結點連線代理或更新指定專用終結點連線代理的屬性或標記 |
> | 動作 | 微軟.搜尋/搜尋服務/私人端接連接Proxies/讀取 | 傳回專用的清單或取得指定專用終結點連線代理的屬性 |
> | 動作 | 微軟.搜索/搜尋服務/私人終端連接Proxies/刪除 | 移除現有專用終結點連線代理 |

## <a name="microsoftsecurity"></a>Microsoft.Security

Azure 服務:[安全中心](../security-center/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.Security/register/action | 為 Azure 資訊安全中心註冊訂用帳戶 |
> | 動作 | Microsoft.Security/unregister/action | 從 Azure 資訊安全中心取消註冊訂用帳戶 |
> |  | **自我適應網路強化** |  |
> | 動作 | 微軟.安全/自適應網路強化/讀取 | 取得 Azure 受保護資源的自適應網路強化建議 |
> | 動作 | 微軟.安全/自適應網路強化/強制執行/操作 | 通過在給定的網路安全組上建立匹配的安全規則來強制實施給定的流量強化規則 |
> |  | **advancedThreatProtectionSettings** |  |
> | 動作 | Microsoft.Security/advancedThreatProtectionSettings/read | 取得資源的進階威脅防護設定 |
> | 動作 | Microsoft.Security/advancedThreatProtectionSettings/write | 更新資源的進階威脅防護設定 |
> |  | **警示** |  |
> | 動作 | Microsoft.Security/alerts/read | 取得所有可用的安全性警訊 |
> |  | **applicationWhitelistings** |  |
> | 動作 | Microsoft.Security/applicationWhitelistings/read | 取得應用程式允許清單 |
> | 動作 | Microsoft.Security/applicationWhitelistings/write | 建立新的應用程式允許清單，或更新現有的應用程式允許清單 |
> |  | **評估中繼資料** |  |
> | 動作 | 微軟.安全/評估元數據/讀取 | 取得訂閱上的可用安全評估中繼資料 |
> | 動作 | 微軟.安全/評估元資料/寫入 | 建立或更新安全評估中繼資料 |
> |  | **評估** |  |
> | 動作 | 微軟.安全/評估/讀取 | 取得訂閱的安全評估 |
> | 動作 | 微軟.安全/評估/寫入 | 建立或更新訂閱的安全評估 |
> |  | **自動預先設定** |  |
> | 動作 | 微軟.安全/自動預先設定/讀取 | 取得訂閱的安全自動預先設定 |
> | 動作 | 微軟.安全/自動預先設定/寫入 | 為訂閱建立或更新安全自動預先設定 |
> |  | **合規性結果** |  |
> | 動作 | Microsoft.Security/complianceResults/read | 取得資源的合規性結果 |
> |  | **資訊保護政策** |  |
> | 動作 | Microsoft.Security/informationProtectionPolicies/read | 取得資源的資訊保護原則 |
> | 動作 | Microsoft.Security/informationProtectionPolicies/write | 更新資源的資訊保護原則 |
> |  | **位置** |  |
> | 動作 | Microsoft.Security/locations/read | 取得安全性資料位置 |
> |  | **位置/警報** |  |
> | 動作 | Microsoft.Security/locations/alerts/read | 取得所有可用的安全性警訊 |
> | 動作 | Microsoft.Security/locations/alerts/dismiss/action | 關閉安全性警示 |
> | 動作 | Microsoft.Security/locations/alerts/activate/action | 啟動安全性警訊 |
> |  | **位置/jit網路存取原則** |  |
> | 動作 | Microsoft.Security/locations/jitNetworkAccessPolicies/read | 取得 Just-in-Time 網路存取原則 |
> | 動作 | Microsoft.Security/locations/jitNetworkAccessPolicies/write | 建立新的 Just-in-Time 網路存取原則，或更新現有的 Just-in-Time 網路存取原則 |
> | 動作 | Microsoft.Security/locations/jitNetworkAccessPolicies/delete | 刪除 Just-in-Time 網路存取原則 |
> | 動作 | Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action | 起始 Just-in-Time 網路存取原則要求 |
> |  | **位置/工作** |  |
> | 動作 | Microsoft.Security/locations/tasks/read | 取得所有可用的安全性建議 |
> | 動作 | Microsoft.Security/locations/tasks/start/action | 啟動安全性建議 |
> | 動作 | Microsoft.Security/locations/tasks/resolve/action | 解析安全性建議 |
> | 動作 | Microsoft.Security/locations/tasks/activate/action | 啟用安全性建議 |
> | 動作 | Microsoft.Security/locations/tasks/dismiss/action | 關閉安全性建議 |
> |  | **政策** |  |
> | 動作 | Microsoft.Security/policies/read | 取得安全性原則 |
> | 動作 | Microsoft.Security/policies/write | 更新安全性原則 |
> |  | **pricings** |  |
> | 動作 | Microsoft.Security/pricings/read | 取得該範圍的定價設定 |
> | 動作 | Microsoft.Security/pricings/write | 更新該範圍的定價設定 |
> | 動作 | Microsoft.Security/pricings/delete | 刪除該範圍的定價設定 |
> |  | **securityContacts** |  |
> | 動作 | Microsoft.Security/securityContacts/read | 取得安全性連絡人 |
> | 動作 | Microsoft.Security/securityContacts/write | 更新安全性連絡人 |
> | 動作 | Microsoft.Security/securityContacts/delete | 刪除安全性連絡人 |
> |  | **securitySolutions** |  |
> | 動作 | Microsoft.Security/securitySolutions/read | 取得安全性解決方案 |
> | 動作 | Microsoft.Security/securitySolutions/write | 建立新的安全性解決方案，或更新現有的安全性解決方案 |
> | 動作 | Microsoft.Security/securitySolutions/delete | 刪除安全性解決方案 |
> |  | **securitySolutionsReferenceData** |  |
> | 動作 | Microsoft.Security/securitySolutionsReferenceData/read | 取得安全性解決方案參考資料 |
> |  | **securityStatuses** |  |
> | 動作 | Microsoft.Security/securityStatuses/read | 取得 Azure 資源的安全性健康狀態 |
> |  | **securityStatusesSummaries** |  |
> | 動作 | Microsoft.Security/securityStatusesSummaries/read | 取得該範圍的安全性狀態摘要 |
> |  | **設定** |  |
> | 動作 | Microsoft.Security/settings/read | 取得範圍的設定 |
> | 動作 | Microsoft.Security/settings/write | 更新範圍的設定 |
> |  | **工作** |  |
> | 動作 | Microsoft.Security/tasks/read | 取得所有可用的安全性建議 |
> |  | **web 應用程式防火牆** |  |
> | 動作 | Microsoft.Security/webApplicationFirewalls/read | 取得 Web 應用程式防火牆 |
> | 動作 | Microsoft.Security/webApplicationFirewalls/write | 建立新的 Web 應用程式防火牆，或更新現有的 Web 應用程式防火牆 |
> | 動作 | Microsoft.Security/webApplicationFirewalls/delete | 刪除 Web 應用程式防火牆 |
> |  | **workspaceSettings** |  |
> | 動作 | Microsoft.Security/workspaceSettings/read | 取得工作區設定 |
> | 動作 | Microsoft.Security/workspaceSettings/write | 更新工作區設定 |
> | 動作 | Microsoft.Security/workspaceSettings/delete | 刪除工作區設定 |
> | 動作 | Microsoft.Security/workspaceSettings/connect/action | 變更工作區設定的重新連線設定 |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

微軟監控洞察

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> |  | **診斷設定** |  |
> | 動作 | Microsoft.SecurityGraph/diagnosticsettings/write | 正在寫入診斷設定 |
> | 動作 | Microsoft.SecurityGraph/diagnosticsettings/read | 正在讀取診斷設定 |
> | 動作 | Microsoft.SecurityGraph/diagnosticsettings/delete | 正在刪除診斷設定 |
> |  | **診斷設定類別** |  |
> | 動作 | Microsoft.SecurityGraph/diagnosticsettingscategories/read | 正在讀取診斷設定類別 |

## <a name="microsoftsecurityinsights"></a>微軟.安全洞察

Azure 服務[:Azure 哨兵](../sentinel/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | 微軟.安全見解/註冊/行動 | 註冊 Azure 哨兵的訂閱 |
> | 動作 | 微軟安全見解/取消註冊/操作 | 從 Azure 哨兵取消註冊訂閱 |
> |  | **聚合** |  |
> | 動作 | 微軟.安全見解/聚合/閱讀 | 取得集合資訊 |
> |  | **警示規則** |  |
> | 動作 | 微軟安全見解/警報規則/讀取 | 取得警示規則 |
> | 動作 | 微軟.安全見解/警報規則/寫入 | 更新警示規則 |
> | 動作 | 微軟.安全見解/警報規則/刪除 | 刪除警示規則 |
> |  | **警示規則/操作** |  |
> | 動作 | 微軟安全見解/警報規則/操作/讀取 | 取得警示規則的回應操作 |
> | 動作 | 微軟.安全見解/警報規則/操作/寫入 | 更新警示規則的回應操作 |
> | 動作 | 微軟.安全見解/警報規則/操作/刪除 | 刪除警示規則的回應操作 |
> |  | **書籤** |  |
> | 動作 | 微軟.安全見解/書籤/閱讀 | 取得書籤 |
> | 動作 | 微軟.安全見解/書籤/寫入 | 更新書籤 |
> | 動作 | 微軟.安全見解/書籤/刪除 | 刪除書籤 |
> | 動作 | 微軟.安全見解/書籤/擴展/操作 | 透過特定擴充取得實體的相關實體 |
> |  | **例** |  |
> | 動作 | 微軟.安全見解/案例/閱讀 | 獲取案例 |
> | 動作 | 微軟.安全見解/案例/寫入 | 更新案例 |
> | 動作 | 微軟.安全見解/案例/刪除 | 刪除案例 |
> |  | **案例/評論** |  |
> | 動作 | 微軟.安全見解/案例/評論/閱讀 | 取得案例註解 |
> | 動作 | 微軟.安全見解/案例/評論/寫入 | 建立案例註解 |
> |  | **案件/調查** |  |
> | 動作 | 微軟安全見解/案例/調查/閱讀 | 獲取案例調查 |
> | 動作 | 微軟安全見解/案例/調查/寫入 | 更新案例的中繼資料 |
> |  | **資料連接器** |  |
> | 動作 | 微軟.安全見解/資料連接器/讀取 | 取得資料連線器 |
> | 動作 | 微軟.安全見解/資料連接器/寫入 | 更新資料連接器 |
> | 動作 | 微軟.安全見解/資料連接器/刪除 | 刪除資料連線器 |
> |  | **事件** |  |
> | 動作 | 微軟.安全見解/事件/讀取 | 取得事件 |
> | 動作 | 微軟.安全見解/事件/寫入 | 更新事件 |
> | 動作 | 微軟.安全見解/事件/刪除 | 移除事件 |
> |  | **事件/評論** |  |
> | 動作 | 微軟.安全見解/事件/評論/閱讀 | 取得事件註解 |
> | 動作 | 微軟.安全見解/事件/評論/寫入 | 建立事件註解 |
> |  | **事件/關聯** |  |
> | 動作 | 微軟.安全見解/事件/關係/閱讀 | 取得事件與資源之間的關係 |
> | 動作 | 微軟.安全見解/事件/關係/寫入 | 更新事件與相關資源之間的關係 |
> | 動作 | 微軟.安全見解/事件/關係/刪除 | 移除事件與資源之間的關係 |
> |  | **設定** |  |
> | 動作 | 微軟.安全見解/設置/閱讀 | 取得設定 |
> | 動作 | 微軟.安全見解/設置/寫入 | 更新設定 |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

Azure 服務:[服務匯流排](../service-bus/index.md)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.ServiceBus/checkNamespaceAvailability/action | 檢查給定訂用帳戶下的命名空間是否可用。 此 API 即將淘汰。請改用 CheckNameAvailabiltiy。 |
> | 動作 | Microsoft.ServiceBus/checkNameAvailability/action | 檢查給定訂用帳戶下的命名空間是否可用。 |
> | 動作 | Microsoft.ServiceBus/register/action | 針對 ServiceBus 資源提供者註冊訂用帳戶，並讓您能夠建立 ServiceBus 資源 |
> | 動作 | Microsoft.ServiceBus/unregister/action | 針對 ServiceBus 資源提供者註冊訂用帳戶，並讓您能夠建立 ServiceBus 資源 |
> |  | **位置** |  |
> | 動作 | Microsoft.ServiceBus/locations/deleteVirtualNetworkOrSubnets/action | 針對指定的 VNet，刪除 ServiceBus 資源提供者中的 VNet 規則 |
> |  | **namespaces** |  |
> | 動作 | Microsoft.ServiceBus/namespaces/write | 建立命名空間資源，並更新其屬性。 命名空間的標記和容量是可以更新的屬性。 |
> | 動作 | Microsoft.ServiceBus/namespaces/read | 取得命名空間資源描述的清單 |
> | 動作 | Microsoft.ServiceBus/namespaces/Delete | 刪除命名空間資源 |
> | 動作 | Microsoft.ServiceBus/namespaces/authorizationRules/action | 更新命名空間授權規則。 此 API 即將淘汰。 請改用 PUT 呼叫來更新命名空間授權規則。 API 版本 2017-04-01 不支援此作業。 |
> | 動作 | Microsoft.ServiceBus/namespaces/migrate/action | 移轉命名空間作業 |
> | 動作 | Microsoft.ServiceBus/namespaces/removeAcsNamepsace/action | 移除 ACS 命名空間 |
> |  | **命名空間/授權規則** |  |
> | 動作 | Microsoft.ServiceBus/namespaces/authorizationRules/write | 建立命名空間層級的授權規則，並更新其屬性。 您可以更新授權規則的存取權限、主要金鑰和次要金鑰。 |
> | 動作 | Microsoft.ServiceBus/namespaces/authorizationRules/read | 取得命名空間授權規則描述的清單。 |
> | 動作 | Microsoft.ServiceBus/namespaces/authorizationRules/delete | 刪除命名空間授權規則。 您無法刪除預設的命名空間授權規則。  |
> | 動作 | Microsoft.ServiceBus/namespaces/authorizationRules/listkeys/action | 取得命名空間的連接字串 |
> | 動作 | Microsoft.ServiceBus/namespaces/authorizationRules/regenerateKeys/action | 對資源重新產生主要或次要金鑰 |
> |  | **命名空間/災難修復設定** |  |
> | 動作 | Microsoft.ServiceBus/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | 檢查指定訂用帳戶下命名空間別名的可用性。 |
> | 動作 | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/write | 建立或更新與命名空間相關聯的災害復原設定。 |
> | 動作 | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/read | 取得與命名空間相關聯的災害復原設定。 |
> | 動作 | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/delete | 刪除與命名空間相關聯的災害復原設定。 此作業只能透過主要命名空間叫用。 |
> | 動作 | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/breakPairing/action | 停用災害復原，並停止將變更從主要命名空間複寫至次要命名空間。 |
> | 動作 | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/failover/action | 叫用異地災害復原容錯移轉，並將命名空間別名重新設定為指向次要命名空間。 |
> |  | **命名空間/災難修復設定/授權規則** |  |
> | 動作 | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/read | 取得災害復原主要命名空間的授權規則 |
> | 動作 | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | 取得災害復原主要命名空間的授權規則金鑰 |
> |  | **命名空間/事件網格篩選器** |  |
> | 動作 | Microsoft.ServiceBus/namespaces/eventGridFilters/write | 建立或更新與命名空間相關聯的事件格線篩選。 |
> | 動作 | Microsoft.ServiceBus/namespaces/eventGridFilters/read | 取得與命名空間相關聯的事件格線篩選。 |
> | 動作 | Microsoft.ServiceBus/namespaces/eventGridFilters/delete | 刪除與命名空間相關聯的事件格線篩選。 |
> |  | **namespaces/eventhubs** |  |
> | 動作 | Microsoft.ServiceBus/namespaces/eventhubs/read | 取得 EventHub 資源描述的清單 |
> |  | **命名空間/ipFilter規則** |  |
> | 動作 | Microsoft.ServiceBus/namespaces/ipFilterRules/read | 取得 IP 篩選資源 |
> | 動作 | Microsoft.ServiceBus/namespaces/ipFilterRules/write | 建立 IP 篩選資源 |
> | 動作 | Microsoft.ServiceBus/namespaces/ipFilterRules/delete | 刪除 IP 篩選資源 |
> |  | **命名空間/訊息計劃** |  |
> | 動作 | Microsoft.ServiceBus/namespaces/messagingPlan/read | 取得命名空間的傳訊方案。<br>此 API 即將淘汰。<br>透過 MessagingPlan 資源公開的屬性，已移至新版 API 的 (父系) 命名空間資源。<br>API 版本 2017-04-01 不支援此作業。 |
> | 動作 | Microsoft.ServiceBus/namespaces/messagingPlan/write | 更新命名空間的傳訊方案。<br>此 API 即將淘汰。<br>透過 MessagingPlan 資源公開的屬性，已移至新版 API 的 (父系) 命名空間資源。<br>API 版本 2017-04-01 不支援此作業。 |
> |  | **命名空間/移轉設定** |  |
> | 動作 | Microsoft.ServiceBus/namespaces/migrationConfigurations/write | 建立或更新移轉設定。 這會開始將資源從標準命名空間同步至進階命名空間 |
> | 動作 | Microsoft.ServiceBus/namespaces/migrationConfigurations/read | 取得指出移轉和暫止複寫作業狀態的移轉設定 |
> | 動作 | Microsoft.ServiceBus/namespaces/migrationConfigurations/delete | 刪除移轉設定。 |
> | 動作 | Microsoft.ServiceBus/namespaces/migrationConfigurations/revert/action | 還原從標準到進階命名空間的移轉 |
> | 動作 | Microsoft.ServiceBus/namespaces/migrationConfigurations/upgrade/action | 將與標準命名空間建立關聯的 DNS 指派給進階命名空間，這會完成移轉，並停止將資源從標準命名空間同步至進階命名空間 |
> |  | **命名空間/網路規則集** |  |
> | 動作 | 微軟.服務總線/命名空間/網路規則集/讀取 | 取得網路規則集資源 |
> | 動作 | 微軟.服務總線/命名空間/網路規則集/寫入 | 建立 VNET 規則資源 |
> | 動作 | 微軟.服務總線/命名空間/網路規則集/刪除 | 刪除 VNET 規則資源 |
> |  | **命名空間/網路規則集** |  |
> | 動作 | 微軟.服務總線/命名空間/網路規則集/讀取 | 取得網路規則集資源 |
> | 動作 | 微軟.服務總線/命名空間/網路規則集/寫入 | 建立 VNET 規則資源 |
> | 動作 | 微軟.服務總線/命名空間/網路規則集/刪除 | 刪除 VNET 規則資源 |
> |  | **命名空間/動作結果** |  |
> | 動作 | Microsoft.ServiceBus/namespaces/operationresults/read | 取得命名空間作業的狀態 |
> |  | **命名空間/專用端接Proxies** |  |
> | 動作 | 微軟.服務總線/命名空間/私人端接連接Proxies/驗證/操作 | 驗證專用終結點連線代理 |
> | 動作 | 微軟.服務總線/命名空間/私人端接連接Proxies/讀取 | 取得專用終結點連線代理 |
> | 動作 | 微軟.服務總線/命名空間/私人端接連接Proxies/寫入 | 建立專用終結點連線代理 |
> | 動作 | 微軟.服務總線/命名空間/私人端接連接Proxies/刪除 | 移除專用終結點連線代理 |
> |  | **命名空間/供應商/微軟.見解/診斷設置** |  |
> | 動作 | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/diagnosticSettings/read | 取得命名空間診斷設定資源描述的清單 |
> | 動作 | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/diagnosticSettings/write | 取得命名空間診斷設定資源描述的清單 |
> |  | **命名空間/提供者/微軟.見解/日誌定義** |  |
> | 動作 | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/logDefinitions/read | 取得命名空間記錄資源描述的清單 |
> |  | **命名空間/提供者/微軟.見解/指標定義** |  |
> | 動作 | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/metricDefinitions/read | 取得命名空間計量資源描述的清單 |
> |  | **namespaces/queues** |  |
> | 動作 | Microsoft.ServiceBus/namespaces/queues/write | 建立或更新佇列屬性。 |
> | 動作 | Microsoft.ServiceBus/namespaces/queues/read | 取得佇列資源描述的清單 |
> | 動作 | Microsoft.ServiceBus/namespaces/queues/Delete | 用來刪除佇列資源的作業 |
> | 動作 | Microsoft.ServiceBus/namespaces/queues/authorizationRules/action | 更新佇列的作業。 API 版本 2017-04-01 不支援此作業。 授權規則。 請使用 PUT 呼叫來更新授權規則。 |
> |  | **命名空間/佇列/授權規則** |  |
> | 動作 | Microsoft.ServiceBus/namespaces/queues/authorizationRules/write | 建立佇列授權規則，並更新其屬性。 您可以更新授權規則存取權限。 |
> | 動作 | Microsoft.ServiceBus/namespaces/queues/authorizationRules/read |  取得佇列授權規則的清單 |
> | 動作 | Microsoft.ServiceBus/namespaces/queues/authorizationRules/delete | 用來刪除佇列授權規則的作業 |
> | 動作 | Microsoft.ServiceBus/namespaces/queues/authorizationRules/listkeys/action | 取得佇列的連接字串 |
> | 動作 | Microsoft.ServiceBus/namespaces/queues/authorizationRules/regenerateKeys/action | 對資源重新產生主要或次要金鑰 |
> |  | **namespaces/topics** |  |
> | 動作 | Microsoft.ServiceBus/namespaces/topics/write | 建立或更新主題屬性。 |
> | 動作 | Microsoft.ServiceBus/namespaces/topics/read | 取得主題資源描述的清單 |
> | 動作 | Microsoft.ServiceBus/namespaces/topics/Delete | 用來刪除主題資源的作業 |
> | 動作 | Microsoft.ServiceBus/namespaces/topics/authorizationRules/action | 更新主題的作業。 API 版本 2017-04-01 不支援此作業。 授權規則。 請使用 PUT 呼叫來更新授權規則。 |
> |  | **命名空間/主題/授權規則** |  |
> | 動作 | Microsoft.ServiceBus/namespaces/topics/authorizationRules/write | 建立主題授權規則，並更新其屬性。 您可以更新授權規則存取權限。 |
> | 動作 | Microsoft.ServiceBus/namespaces/topics/authorizationRules/read |  取得主題授權規則的清單 |
> | 動作 | Microsoft.ServiceBus/namespaces/topics/authorizationRules/delete | 用來刪除主題授權規則的作業 |
> | 動作 | Microsoft.ServiceBus/namespaces/topics/authorizationRules/listkeys/action | 取得主題的連接字串 |
> | 動作 | Microsoft.ServiceBus/namespaces/topics/authorizationRules/regenerateKeys/action | 對資源重新產生主要或次要金鑰 |
> |  | **namespaces/topics/subscriptions** |  |
> | 動作 | Microsoft.ServiceBus/namespaces/topics/subscriptions/write | 建立或更新 TopicSubscription 屬性。 |
> | 動作 | Microsoft.ServiceBus/namespaces/topics/subscriptions/read | 取得 TopicSubscription 資源描述的清單 |
> | 動作 | Microsoft.ServiceBus/namespaces/topics/subscriptions/Delete | 用來刪除 TopicSubscription 資源的作業 |
> |  | **namespaces/topics/subscriptions/rules** |  |
> | 動作 | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/write | 建立或更新規則屬性。 |
> | 動作 | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/read | 取得規則資源描述的清單 |
> | 動作 | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/Delete | 用來刪除規則資源的作業 |
> |  | **命名空間/虛擬網路規則** |  |
> | 動作 | Microsoft.ServiceBus/namespaces/virtualNetworkRules/read | 取得 VNET 規則資源 |
> | 動作 | Microsoft.ServiceBus/namespaces/virtualNetworkRules/write | 建立 VNET 規則資源 |
> | 動作 | Microsoft.ServiceBus/namespaces/virtualNetworkRules/delete | 刪除 VNET 規則資源 |
> |  | **操作** |  |
> | 動作 | Microsoft.ServiceBus/operations/read | 取得作業 |
> |  | **Sku** |  |
> | 動作 | Microsoft.ServiceBus/sku/read | 取得 SKU 資源描述的清單 |
> |  | **sKU/地區** |  |
> | 動作 | Microsoft.ServiceBus/sku/regions/read | 取得 SkuRegions 資源描述的清單 |
> |  | **命名空間/訊息** |  |
> | DataAction | 微軟.服務總線/命名空間/消息/發送/操作 | 傳送訊息 |
> | DataAction | 微軟.服務總線/命名空間/消息/接收/操作 | 接收訊息 |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

Azure 服務:[服務結構](../service-fabric/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.ServiceFabric/register/action | 註冊任何動作 |
> |  | **集群** |  |
> | 動作 | Microsoft.ServiceFabric/clusters/read | 讀取任何叢集 |
> | 動作 | Microsoft.ServiceFabric/clusters/write | 建立或更新任何叢集 |
> | 動作 | Microsoft.ServiceFabric/clusters/delete | 刪除任何叢集 |
> |  | **clusters/applications** |  |
> | 動作 | Microsoft.ServiceFabric/clusters/applications/read | 讀取任何應用程式 |
> | 動作 | Microsoft.ServiceFabric/clusters/applications/write | 建立或更新任何應用程式 |
> | 動作 | Microsoft.ServiceFabric/clusters/applications/delete | 刪除任何應用程式 |
> |  | **集群/應用程式/服務** |  |
> | 動作 | Microsoft.ServiceFabric/clusters/applications/services/read | 讀取任何服務 |
> | 動作 | Microsoft.ServiceFabric/clusters/applications/services/write | 建立或更新任何服務 |
> | 動作 | Microsoft.ServiceFabric/clusters/applications/services/delete | 刪除任何服務 |
> |  | **叢集/應用程式/服務/分割區** |  |
> | 動作 | Microsoft.ServiceFabric/clusters/applications/services/partitions/read | 讀取任何分割區 |
> |  | **叢集/應用程式/服務/分區/副本** |  |
> | 動作 | Microsoft.ServiceFabric/clusters/applications/services/partitions/replicas/read | 讀取任何複本 |
> |  | **叢集/應用程式/服務/狀態** |  |
> | 動作 | Microsoft.ServiceFabric/clusters/applications/services/statuses/read | 讀取任何服務狀態 |
> |  | **叢集/應用程式類型** |  |
> | 動作 | Microsoft.ServiceFabric/clusters/applicationTypes/read | 讀取任何應用程式類型 |
> | 動作 | Microsoft.ServiceFabric/clusters/applicationTypes/write | 建立或更新任何應用程式類型 |
> | 動作 | Microsoft.ServiceFabric/clusters/applicationTypes/delete | 刪除任何應用程式類型 |
> |  | **叢集/應用程式類型/版本** |  |
> | 動作 | Microsoft.ServiceFabric/clusters/applicationTypes/versions/read | 讀取任何應用程式類型版本 |
> | 動作 | Microsoft.ServiceFabric/clusters/applicationTypes/versions/write | 建立或更新任何應用程式類型版本 |
> | 動作 | Microsoft.ServiceFabric/clusters/applicationTypes/versions/delete | 刪除任何應用程式類型版本 |
> |  | **叢集/節點** |  |
> | 動作 | Microsoft.ServiceFabric/clusters/nodes/read | 讀取任何節點 |
> |  | **叢集/狀態** |  |
> | 動作 | Microsoft.ServiceFabric/clusters/statuses/read | 讀取任何叢集狀態 |
> |  | **位置/叢集版本** |  |
> | 動作 | Microsoft.ServiceFabric/locations/clusterVersions/read | 讀取任何叢集版本 |
> |  | **位置/環境/叢集版本** |  |
> | 動作 | Microsoft.ServiceFabric/locations/environments/clusterVersions/read | 讀取特定環境的任何叢集版本 |
> |  | **位置/動作結果** |  |
> | 動作 | Microsoft.ServiceFabric/locations/operationresults/read | 讀取任何作業結果 |
> |  | **位置/操作** |  |
> | 動作 | Microsoft.ServiceFabric/locations/operations/read | 依位置讀取任何作業 |
> |  | **操作** |  |
> | 動作 | Microsoft.ServiceFabric/operations/read | 讀取任何可用的作業 |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

Azure 服務[:Azure 信號器服務](../azure-signalr/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.SignalRService/register/action | 向訂用帳戶註冊 'Microsoft.SignalRService' 資源提供者 |
> | 動作 | Microsoft.SignalRService/unregister/action | 向訂用帳戶取消註冊 'Microsoft.SignalRService' 資源提供者 |
> |  | **位置** |  |
> | 動作 | Microsoft.SignalRService/locations/checknameavailability/action | 檢查名稱是否可用於新的 SignalR 服務 |
> |  | **位置/動作結果/信號器** |  |
> | 動作 | Microsoft.SignalRService/locations/operationresults/signalr/read | 查詢基於位置的非同步操作的結果 |
> |  | **位置/操作 狀態/操作 Id** |  |
> | 動作 | 微軟.SignalR服務/位置/操作 狀態/操作 Id/讀取 | 查詢基於位置的非同步操作的狀態 |
> |  | **位置/用法** |  |
> | 動作 | Microsoft.SignalRService/locations/usages/read | 取得 Azure SignalR Service 的配額使用量 |
> |  | **動作結果** |  |
> | 動作 | Microsoft.SignalRService/operationresults/read | 查詢提供程式級非同步操作的結果 |
> |  | **操作** |  |
> | 動作 | 微軟.SignalR服務/操作/讀取 | 列出 Azure SignalR 服務的操作。 |
> |  | **操作狀態** |  |
> | 動作 | 微軟.SignalR服務/操作狀態/讀取 | 查詢提供者級非同步操作的狀態 |
> |  | **SignalR** |  |
> | 動作 | Microsoft.SignalRService/SignalR/read | 在管理入口網站中或透過 API 檢視 SignalR 的設定和組態 |
> | 動作 | Microsoft.SignalRService/SignalR/write | 在管理入口網站中或透過 API 修改 SignalR 的設定和組態 |
> | 動作 | Microsoft.SignalRService/SignalR/delete | 刪除整個 SignalR Service |
> | 動作 | Microsoft.SignalRService/SignalR/listkeys/action | 在管理入口網站中或透過 API 檢視 SignalR 存取金鑰 |
> | 動作 | Microsoft.SignalRService/SignalR/regeneratekey/action | 在管理入口網站中或透過 API 變更 SignalR 存取金鑰 |
> | 動作 | Microsoft.SignalRService/SignalR/restart/action | 在管理入口網站中或透過 API 重新啟動 Azure SignalR Service。 會有一些停機時間。 |
> |  | **訊號R/事件網格濾波器** |  |
> | 動作 | 微軟.SignalR服務/信號R/事件網格過濾器/讀取 | 獲取指定事件網格篩選器的屬性或列出指定 SignalR 的所有事件網格篩選器。 |
> | 動作 | 微軟.SignalR服務/信號R/事件網格過濾器/寫入 | 使用指定的參數創建或更新 SignalR 的事件網格篩選器。 |
> | 動作 | 微軟.SignalR服務/信號R/事件網格過濾器/刪除 | 從訊號 R 中刪除事件網格篩選器。 |
> |  | **訊號R/私有端點連接Proxies** |  |
> | 動作 | 微軟.SignalR服務/信號R/私有端點連接Proxies/驗證/操作 | 驗證專用終結點連線代理 |
> | 動作 | 微軟.SignalR服務/信號R/私有端點連接Proxies/寫 | 建立專用終結點連線代理 |
> | 動作 | 微軟.SignalR服務/信號R/私有端點連接Proxies/讀取 | 讀取專用終結點連線代理 |
> | 動作 | 微軟.SignalR服務/信號R/私人端接連接Proxies/刪除 | 移除專用終結點連線代理 |
> |  | **訊號R/私有端點連接** |  |
> | 動作 | 微軟.SignalR服務/信號R/私有端點連接/寫入 | 批准或拒絕專用終結點連線 |
> | 動作 | 微軟.SignalR服務/信號R/私有端點連接/讀取 | 讀取專用終結點連線 |
> |  | **訊號R/私有連結資源** |  |
> | 動作 | 微軟.SignalR服務/信號R/私有連結資源/讀取 | 列出所有 SignalR 專用鍊路資源 |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

Azure 服務[:Azure 託管應用程式](../azure-resource-manager/managed-applications/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.Solutions/register/action | 向 Solutions 註冊。 |
> | 動作 | 微軟.解決方案/取消註冊/操作 | 從解決方案取消註冊。 |
> |  | **applicationDefinitions** |  |
> | 動作 | Microsoft.Solutions/applicationDefinitions/read | 擷取應用程式定義清單。 |
> | 動作 | Microsoft.Solutions/applicationDefinitions/write | 新增或修改應用程式定義。 |
> | 動作 | Microsoft.Solutions/applicationDefinitions/delete | 移除應用程式定義。 |
> |  | **應用程式定義/應用程式應用程式** |  |
> | 動作 | 微軟.解決方案/應用程式定義/應用程式應用/讀取 | 列出應用程式定義的應用程式專案。 |
> |  | **套用** |  |
> | 動作 | Microsoft.Solutions/applications/read | 擷取應用程式清單。 |
> | 動作 | Microsoft.Solutions/applications/write | 建立應用程式。 |
> | 動作 | Microsoft.Solutions/applications/delete | 移除應用程式。 |
> | 動作 | 微軟.解決方案/應用程式/刷新權限/操作 | 刷新應用程式許可權。 |
> | 動作 | 微軟.解決方案/應用程式/更新存取/操作 | 更新應用程序訪問。 |
> |  | **應用程式/應用程式工件** |  |
> | 動作 | 微軟.解決方案/應用程式/應用程式 | 列出應用程式專案。 |
> |  | **jitRequests** |  |
> | 動作 | Microsoft.Solutions/jitRequests/read | 擷取 JitRequests 清單 |
> | 動作 | Microsoft.Solutions/jitRequests/write | 建立 JitRequest |
> | 動作 | Microsoft.Solutions/jitRequests/delete | 移除 JitRequest |
> |  | **位置/動作狀態** |  |
> | 動作 | Microsoft.Solutions/locations/operationStatuses/read | 讀取資源的作業狀態。 |
> |  | **操作** |  |
> | 動作 | 微軟.解決方案/操作/讀取 | 取得作業的清單。 |

## <a name="microsoftsql"></a>Microsoft.Sql

Azure 服務[:Azure SQL 資料庫](../sql-database/index.yml)[、SQL 資料倉儲](../synapse-analytics/sql-data-warehouse/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.Sql/checkNameAvailability/action | 確認指定的伺服器名稱是否可用來針對指定的訂用帳戶進行全球佈建。 |
> | 動作 | Microsoft.Sql/register/action | 為 Microsoft SQL Database 資源提供者註冊訂用帳戶，並讓您能夠建立 Microsoft SQL Database。 |
> | 動作 | Microsoft.Sql/unregister/action | 為 Microsoft SQL Database 資源提供者取消註冊訂用帳戶，並讓您能夠建立 Microsoft SQL Database。 |
> | 動作 | 微軟.Sql/私有端點連接審批/操作 | 確定是否允許使用者批准專用終結點連線 |
> |  | **實例池** |  |
> | 動作 | Microsoft.Sql/instancePools/read | 取得執行個體集區 |
> | 動作 | Microsoft.Sql/instancePools/write | 建立或更新執行個體集區 |
> | 動作 | Microsoft.Sql/instancePools/delete | 刪除執行個體集區 |
> |  | **實體池/用法** |  |
> | 動作 | 微軟.Sql/實例池/用法/讀取 | 取得實體池的使用資訊 |
> |  | **位置** |  |
> | 動作 | Microsoft.Sql/locations/read | 取得指定訂用帳戶的可用位置 |
> |  | **位置/稽核設定Azure同步操作** |  |
> | 動作 | Microsoft.Sql/locations/auditingSettingsAzureAsyncOperation/read | 擷取擴充伺服器的 Blob 稽核原則設定作業結果 |
> |  | **位置/稽核設定操作結果** |  |
> | 動作 | Microsoft.Sql/locations/auditingSettingsOperationResults/read | 擷取伺服器的「Blob 稽核原則設定」作業結果 |
> |  | **位置/功能** |  |
> | 動作 | Microsoft.Sql/locations/capabilities/read | 在指定位置取得此訂用帳戶的功能 |
> |  | **位置/資料庫 Azure 同步操作** |  |
> | 動作 | Microsoft.Sql/locations/databaseAzureAsyncOperation/read | 取得資料庫作業的狀態。 |
> |  | **位置/資料庫操作結果** |  |
> | 動作 | Microsoft.Sql/locations/databaseOperationResults/read | 取得資料庫作業的狀態。 |
> |  | **位置/已移除伺服器同步操作** |  |
> | 動作 | Microsoft.Sql/locations/deletedServerAsyncOperation/read | 取得已刪除伺服器上進行中的作業 |
> |  | **位置/已移除伺服器操作結果** |  |
> | 動作 | Microsoft.Sql/locations/deletedServerOperationResults/read | 取得已刪除伺服器上進行中的作業 |
> |  | **位置/已移除的伺服器** |  |
> | 動作 | Microsoft.Sql/locations/deletedServers/read | 傳回已刪除伺服器的清單，或取得指定已刪除伺服器的屬性。 |
> | 動作 | Microsoft.Sql/locations/deletedServers/recover/action | 復原已刪除的伺服器 |
> |  | **位置/彈性池Azure同步操作** |  |
> | 動作 | Microsoft.Sql/locations/elasticPoolAzureAsyncOperation/read | 取得彈性集區非同步作業的 Aure 非同步作業 |
> |  | **位置/彈性池操作結果** |  |
> | 動作 | Microsoft.Sql/locations/elasticPoolOperationResults/read | 取得彈性集區作業的結果。 |
> |  | **位置/加密保護器Azure同步操作** |  |
> | 動作 | 微軟.Sql/位置/加密保護器Azure同步操作/讀取 | 取得對透明資料加密加密保護器的正在進行的作業 |
> |  | **位置/加密保護器操作結果** |  |
> | 動作 | 微軟.Sql/位置/加密保護器操作結果/讀取 | 取得對透明資料加密加密保護器的正在進行的作業 |
> |  | **位置/延伸稽核設定Azure同步操作** |  |
> | 動作 | Microsoft.Sql/locations/extendedAuditingSettingsAzureAsyncOperation/read | 擷取擴充伺服器的 Blob 稽核原則設定作業結果 |
> |  | **位置/延伸稽核設定操作結果** |  |
> | 動作 | Microsoft.Sql/locations/extendedAuditingSettingsOperationResults/read | 擷取擴充伺服器的 Blob 稽核原則設定作業結果 |
> |  | **位置/防火牆規則Azure同步操作** |  |
> | 動作 | Microsoft.Sql/locations/firewallRulesAzureAsyncOperation/read | 取得防火牆規則作業的狀態。 |
> |  | **位置/防火牆規則操作結果** |  |
> | 動作 | Microsoft.Sql/locations/firewallRulesOperationResults/read | 取得防火牆規則作業的狀態。 |
> |  | **locations/instanceFailoverGroups** |  |
> | 動作 | Microsoft.Sql/locations/instanceFailoverGroups/read | 傳回執行個體容錯移轉群組的清單，或取得指定執行個體容錯移轉群組的屬性。 |
> | 動作 | Microsoft.Sql/locations/instanceFailoverGroups/write | 使用指定參數創建實例故障轉移組或更新指定實例故障轉移組的屬性或標記。 |
> | 動作 | Microsoft.Sql/locations/instanceFailoverGroups/delete | 刪除現有的執行個體容錯移轉群組。 |
> | 動作 | Microsoft.Sql/locations/instanceFailoverGroups/failover/action | 在現有的執行個體容錯移轉群組中執行規劃的容錯移轉。 |
> | 動作 | Microsoft.Sql/locations/instanceFailoverGroups/forceFailoverAllowDataLoss/action | 在現有的執行個體容錯移轉群組中執行強制容錯移轉。 |
> |  | **位置/實體池Azure同步操作** |  |
> | 動作 | Microsoft.Sql/locations/instancePoolAzureAsyncOperation/read | 取得實體池操作的狀態 |
> |  | **位置/實體音數** |  |
> | 動作 | Microsoft.Sql/locations/instancePoolOperationResults/read | 取得實體池操作的結果 |
> |  | **位置/介面終結點設定檔Azure同步操作** |  |
> | 動作 | Microsoft.Sql/locations/interfaceEndpointProfileAzureAsyncOperation/read | 傳回特定介面端點 Azure 非同步作業的詳細資料 |
> |  | **位置/介面終結點設定檔操作結果** |  |
> | 動作 | Microsoft.Sql/locations/interfaceEndpointProfileOperationResults/read | 傳回特定介面端點 設定檔作業的詳細資料 |
> |  | **位置/作業代理Azure同步操作** |  |
> | 動作 | Microsoft.Sql/locations/jobAgentAzureAsyncOperation/read | 取得工作代理程式作業的狀態。 |
> |  | **位置/工作操作結果** |  |
> | 動作 | Microsoft.Sql/locations/jobAgentOperationResults/read | 取得工作代理程式作業的結果。 |
> |  | **位置/長期保留備份** |  |
> | 動作 | Microsoft.Sql/locations/longTermRetentionBackups/read | 列出位置中每個伺服器上每個資料庫的長期保留備份 |
> |  | **位置/長期保留管理實體備份** |  |
> | 動作 | 微軟.Sql/位置/長期保留管理實例備份/讀取 | 傳回特定位置的託管實例 LTR 備份清單  |
> |  | **位置/長期保留管理實例/長期保留資料庫/長期保留管理實例備份** |  |
> | 動作 | Microsoft.Sql/位置/長期保留管理實例/長期保留数据庫/長期保留管理實例備份/讀取 | 傳回託管實體的 LTR 備份清單 |
> | 動作 | Microsoft.Sql/位置/長期保留管理實例/長期保留数据庫/長期保留管理實例備份/刪除 | 移除託管實體的樣本的 LTR 備份 |
> |  | **位置/長期保留管理實體/長期保留管理實體備份** |  |
> | 動作 | 微軟.Sql/位置/長期保留管理實例/長期保留管理實例備份/讀取 | 傳回特定託管實體的管管實體 LTR 備份清單 |
> |  | **位置/長期保留伺服器/長期保留備份** |  |
> | 動作 | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionBackups/read | 列出伺服器上每個資料庫的長期保留備份 |
> |  | **位置/長期保留伺服器/長期保留資料庫/長期保留備份** |  |
> | 動作 | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/read | 列出資料庫的長期保留備份 |
> | 動作 | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete | 刪除長期保留備份 |
> |  | **位置/託管資料庫還原Azure同步操作** |  |
> | 動作 | Microsoft.Sql/locations/managedDatabaseRestoreAzureAsyncOperation/completeRestore/action | 完成受控資料庫還原作業 |
> |  | **位置/託管實例加密保護器 Azure 同步操作** |  |
> | 動作 | 微軟.Sql/位置/託管實例加密保護器Azure同步操作/讀取 | 取得對透明資料加密管理方式加密保護器的正在進行的操作 |
> |  | **位置/託管實例加密保護器操作結果** |  |
> | 動作 | 微軟.Sql/位置/託管實例加密保護器操作結果/讀取 | 取得對透明資料加密管理方式加密保護器的正在進行的操作 |
> |  | **位置/託管實例鍵 Azure 同步操作** |  |
> | 動作 | 微軟.Sql/位置/託管實例鍵Azure同步操作/讀取 | 取得對透明資料加密管理資訊資訊 |
> |  | **位置/託管實例鍵操作結果** |  |
> | 動作 | 微軟.Sql/位置/託管實例鍵操作結果/讀取 | 取得對透明資料加密管理資訊資訊 |
> |  | **位置/託管實例長期保留策略 Azure 同步操作** |  |
> | 動作 | 微軟.Sql/位置/託管實例長期保留策略Azure同步操作/讀取 | 取得託管資料庫的長期保留策略操作的狀態 |
> |  | **位置/託管實例長期保留政策操作結果** |  |
> | 動作 | 微軟.Sql/位置/託管實例長期保留策略操作結果/讀取 | 取得託管資料庫的長期保留策略操作的狀態 |
> |  | **位置/託管短期保留政策操作結果** |  |
> | 動作 | 微軟.Sql/位置/託管短期保留策略操作結果/讀取 | 取得短期保留原則操作的狀態 |
> |  | **位置/託管透明資料加密 Azure 同步操作** |  |
> | 動作 | Microsoft.Sql/locations/managedTransparentDataEncryptionAzureAsyncOperation/read | 取得有關受控資料庫之透明資料加密的進行中作業 |
> |  | **位置/託管透明資料加密操作結果** |  |
> | 動作 | Microsoft.Sql/locations/managedTransparentDataEncryptionOperationResults/read | 取得有關受控資料庫之透明資料加密的進行中作業 |
> |  | **位置/專用端點連接Azure同步操作** |  |
> | 動作 | 微軟.Sql/位置/私有端點連接Azure同步操作/讀取 | 取得專用終結點連線操作的結果 |
> |  | **位置/專用端點連線操作結果** |  |
> | 動作 | 微軟.Sql/位置/私有終結點連接操作結果/讀取 | 取得專用終結點連線操作的結果 |
> |  | **位置/專用端點連線代理Azure同步操作** |  |
> | 動作 | 微軟.Sql/位置/私有端點連接代理Azure同步操作/讀取 | 取得專用終結點連線代理操作的結果 |
> |  | **位置/專用端接連線操作結果** |  |
> | 動作 | 微軟.Sql/位置/私有端點連接代理操作結果/讀取 | 取得專用終結點連線代理操作的結果 |
> |  | **位置/伺服器管理員 Azure 同步操作** |  |
> | 動作 | 微軟.Sql/位置/伺服器管理員Azure同步操作/讀取 | 伺服器 Azure 活動目錄管理員同步操作結果 |
> |  | **位置/伺服器管理員操作結果** |  |
> | 動作 | 微軟.Sql/位置/伺服器管理員操作結果/讀取 | 伺服器 Azure 活動目錄管理員操作結果 |
> |  | **位置/伺服器鍵Azure同步操作** |  |
> | 動作 | 微軟.Sql/位置/伺服器鍵Azure同步操作/讀取 | 取得對透明資料加密伺服器金鑰的正在進行的作業 |
> |  | **位置/伺服器鍵操作結果** |  |
> | 動作 | 微軟.Sql/位置/伺服器鍵操作結果/讀取 | 取得對透明資料加密伺服器金鑰的正在進行的作業 |
> |  | **地點/短期保留政策操作結果** |  |
> | 動作 | 微軟.Sql/位置/短期保留策略操作結果/讀取 | 取得短期保留原則操作的狀態 |
> |  | **位置/同步操作結果** |  |
> | 動作 | Microsoft.Sql/locations/syncAgentOperationResults/read | 擷取同步代理程式資源作業的結果 |
> |  | **位置/同步資料庫 Id** |  |
> | 動作 | Microsoft.Sql/locations/syncDatabaseIds/read | 擷取特定區域和訂用帳戶的同步資料庫識別碼 |
> |  | **位置/同步組動作結果** |  |
> | 動作 | Microsoft.Sql/locations/syncGroupOperationResults/read | 擷取同步群組資源作業的結果 |
> |  | **位置/同步成員操作結果** |  |
> | 動作 | Microsoft.Sql/locations/syncMemberOperationResults/read | 擷取同步成員資源作業的結果 |
> |  | **位置/時區** |  |
> | 動作 | 微軟.Sql/位置/時區/讀取 | 按位置返回託管實例時區的清單。 |
> |  | **位置/透明資料加密Azure同步操作** |  |
> | 動作 | 微軟.Sql/位置/透明資料加密Azure同步操作/讀取 | 取得邏輯資料庫透明資料加密的正在進行的操作 |
> |  | **位置/透明資料加密操作結果** |  |
> | 動作 | 微軟.Sql/位置/透明資料加密操作結果/讀取 | 取得邏輯資料庫透明資料加密的正在進行的操作 |
> |  | **位置/用法** |  |
> | 動作 | Microsoft.Sql/locations/usages/read | 在位置中取得此訂用帳戶的使用計量集合 |
> |  | **位置/虛擬網路規則Azure同步操作** |  |
> | 動作 | Microsoft.Sql/locations/virtualNetworkRulesAzureAsyncOperation/read | 傳回指定虛擬網路規則 Aure 非同步作業的詳細資料  |
> |  | **位置/虛擬網路規則操作結果** |  |
> | 動作 | Microsoft.Sql/locations/virtualNetworkRulesOperationResults/read | 傳回指定虛擬網路規則作業的詳細資料  |
> |  | **託管實例** |  |
> | 動作 | Microsoft.Sql/managedInstances/tdeCertificates/action | 建立/更新 TDE 憑證 |
> | 動作 | Microsoft.Sql/managedInstances/read | 傳回受控執行個體的清單，或取得指定受控執行個體的屬性。 |
> | 動作 | Microsoft.Sql/managedInstances/write | 使用指定參數建立受控執行個體，或更新指定受控執行個體的屬性或標記。 |
> | 動作 | Microsoft.Sql/managedInstances/delete | 刪除現有的受控執行個體。 |
> |  | **託管實例/管理員** |  |
> | 動作 | Microsoft.Sql/managedInstances/administrators/read | 取得受控執行個體系統管理員的清單。 |
> | 動作 | Microsoft.Sql/managedInstances/administrators/write | 使用指定參數，建立或更新受控執行個體的系統管理員。 |
> | 動作 | Microsoft.Sql/managedInstances/administrators/delete | 刪除受控執行個體的現有系統管理員。 |
> |  | **託管實例/資料庫** |  |
> | 動作 | Microsoft.Sql/managedInstances/databases/read | 取得現有的受控資料庫 |
> | 動作 | Microsoft.Sql/managedInstances/databases/delete | 刪除現有的受控資料庫 |
> | 動作 | Microsoft.Sql/managedInstances/databases/write | 建立新的資料庫或更新現有資料庫。 |
> | 動作 | 微軟.Sql/託管實例/資料庫/完成還原/操作 | 完成受控資料庫還原作業 |
> |  | **託管實例/資料庫/備份長期保留策略** |  |
> | 動作 | 微軟.Sql/託管實例/資料庫/備份長期保留策略/寫入 | 更新託管資料庫的長期保留原則 |
> | 動作 | 微軟.Sql/託管實例/資料庫/備份長期保留策略/讀取 | 取得託管資料庫的長期保留原則 |
> |  | **managedInstances/databases/backupShortTermRetentionPolicies** |  |
> | 動作 | Microsoft.Sql/managedInstances/databases/backupShortTermRetentionPolicies/read | 取得受控資料庫中的短期保留原則 |
> | 動作 | Microsoft.Sql/managedInstances/databases/backupShortTermRetentionPolicies/write | 更新受控資料庫中的短期保留原則 |
> |  | **託管實例/資料庫/欄** |  |
> | 動作 | 微軟.Sql/託管實例/資料庫/列/讀取 | 傳回託管資料庫的列清單 |
> |  | **託管實例/資料庫/當前敏感標籤** |  |
> | 動作 | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/read | 列出指定資料庫的敏感度標籤 |
> | 動作 | 微軟.Sql/託管實例/資料庫/當前敏感標籤/寫入 | 批次更新敏感度標籤 |
> |  | **託管實例/資料庫/提供程式/微軟.見解/診斷設置** |  |
> | 動作 | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/diagnosticSettings/read | 取得資源的診斷設定 |
> | 動作 | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/diagnosticSettings/write | 建立或更新資源的診斷設定 |
> |  | **託管實例/資料庫/提供程式/微軟.見解/日誌定義** |  |
> | 動作 | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/logDefinitions/read | 取得受控執行個體資料庫的可用記錄 |
> |  | **託管實例/資料庫/推薦敏感標籤** |  |
> | 動作 | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/read | 列出指定資料庫的敏感度標籤 |
> | 動作 | 微軟.Sql/託管實例/資料庫/推薦敏感標籤/寫入 | 大量更新建議敏感度標籤 |
> |  | **託管實例/資料庫/還原詳細資訊** |  |
> | 動作 | 微軟.Sql/託管實例/資料庫/還原詳細資訊/讀取 | 在還原正在進行時返回託管資料庫還原詳細資訊。 |
> |  | **託管實例/資料庫/架構** |  |
> | 動作 | 微軟.Sql/託管實例/資料庫/架構/讀取 | 獲取託管資料庫架構。 |
> |  | **託管實例/資料庫/架構/表** |  |
> | 動作 | 微軟.Sql/託管實例/資料庫/架構/表/讀取 | 取得託管資料庫表 |
> |  | **託管實例/資料庫/架構/表/欄** |  |
> | 動作 | 微軟.Sql/託管實例/資料庫/架構/表/列/讀取 | 取得託管資料庫欄位 |
> |  | **managedInstances/databases/schemas/tables/columns/sensitivityLabels** |  |
> | 動作 | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/read | 取得指定資料行的敏感度標籤 |
> | 動作 | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/write | 建立或更新指定資料行的敏感度標籤 |
> | 動作 | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/delete | 刪除指定資料行的敏感度標籤 |
> | 動作 | 微軟.Sql/託管實例/資料庫/架構/表/列/敏感度標籤/禁用/操作 | 關閉給定列的敏感度建議 |
> | 動作 | 微軟.Sql/託管實例/資料庫/架構/表/列/敏感度標籤/啟用/操作 | 在給定欄上開啟敏感性建議 |
> |  | **託管實例/資料庫/安全警報策略** |  |
> | 動作 | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/write | 變更指定受控資料庫的資料庫威脅偵測原則 |
> | 動作 | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/read | 檢索為給定伺服器設定的託管資料庫威脅偵測原則的清單 |
> |  | **託管實例/資料庫/安全事件** |  |
> | 動作 | Microsoft.Sql/managedInstances/databases/securityEvents/read | 擷取受控資料庫的安全性事件 |
> |  | **託管實例/資料庫/敏感性標籤** |  |
> | 動作 | Microsoft.Sql/managedInstances/databases/sensitivityLabels/read | 列出指定資料庫的敏感度標籤 |
> |  | **託管實例/資料庫/透明資料加密** |  |
> | 動作 | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/read | 擷取指定受控資料庫上資料庫透明資料加密的詳細資料 |
> | 動作 | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/write | 變更指定受控資料庫的資料庫透明資料加密 |
> |  | **managedInstances/databases/vulnerabilityAssessments** |  |
> | 動作 | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/write | 變更指定資料庫的弱點評量 |
> | 動作 | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/delete | 移除指定資料庫的弱點評量 |
> | 動作 | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/read | 擷取指定資料庫上的弱點評量原則 |
> |  | **managedInstances/databases/vulnerabilityAssessments/rules/baselines** |  |
> | 動作 | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/delete | 移除指定資料庫的弱點評量規則基準 |
> | 動作 | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/write | 變更指定資料庫的弱點評量規則基準 |
> | 動作 | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/read | 取得指定資料庫的弱點評量規則基準 |
> |  | **託管實例/資料庫/漏洞評估/掃描** |  |
> | 動作 | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/initiateScan/action | 執行弱點評估資料庫掃描。 |
> | 動作 | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/export/action | 將現有的掃描結果轉換成人類可讀取的格式。 如果已存在，則不會執行任何動作 |
> | 動作 | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/read | 傳回資料庫弱點評量掃描記錄的清單，或取得指定掃描識別碼的掃描記錄。 |
> |  | **managedInstances/encryptionProtector** |  |
> | 動作 | 微軟.Sql/託管實例/加密保護器/重新驗證/操作 | 更新指定伺服器加密保護裝置的屬性。 |
> | 動作 | Microsoft.Sql/managedInstances/encryptionProtector/read | 傳回伺服器加密保護裝置的清單，或取得指定伺服器加密保護裝置的屬性。 |
> | 動作 | Microsoft.Sql/managedInstances/encryptionProtector/write | 更新指定伺服器加密保護裝置的屬性。 |
> |  | **託管實例/無法存取的託管資料庫** |  |
> | 動作 | 微軟.Sql/託管實例/無法訪問的託管資料庫/讀取 | 取得託管實例中無法存取的託管資料庫的清單 |
> |  | **managedInstances/keys** |  |
> | 動作 | Microsoft.Sql/managedInstances/keys/read | 傳回受控執行個體金鑰的清單，或取得指定受控執行個體金鑰的屬性。 |
> | 動作 | Microsoft.Sql/managedInstances/keys/write | 使用指定參數建立金鑰，或更新指定受控執行個體金鑰的屬性或標記。 |
> | 動作 | Microsoft.Sql/managedInstances/keys/delete | 刪除現有的 Azure SQL 受控執行個體金鑰。 |
> |  | **託管實例/指標定義** |  |
> | 動作 | Microsoft.Sql/managedInstances/metricDefinitions/read | 取得受控執行個體的計量定義 |
> |  | **託管實例/指標** |  |
> | 動作 | Microsoft.Sql/managedInstances/metrics/read | 取得受控執行個體計量 |
> |  | **託管實例/操作** |  |
> | 動作 | 微軟.Sql/託管實例/操作/讀取 | 取得託管實例操作 |
> | 動作 | 微軟.Sql/託管實例/操作/取消/操作 | 取消尚未完成的 Azure SQL 託管實例掛起的非同步操作。 |
> |  | **託管實例/提供程式/微軟.見解/診斷設置** |  |
> | 動作 | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/diagnosticSettings/read | 取得資源的診斷設定 |
> | 動作 | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/diagnosticSettings/write | 建立或更新資源的診斷設定 |
> |  | **託管實例/提供程式/微軟.見解/日誌定義** |  |
> | 動作 | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/logDefinitions/read | 取得受控執行個體的可用記錄 |
> |  | **託管實例/供應商/微軟.見解/指標定義** |  |
> | 動作 | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/metricDefinitions/read | 傳回可供受控執行個體使用的計量類型 |
> |  | **託管實例/可復原資料庫** |  |
> | 動作 | Microsoft.Sql/managedInstances/recoverableDatabases/read | 傳回可還原的受控資料庫清單 |
> |  | **託管實例/可恢復丟棄的資料庫** |  |
> | 動作 | Microsoft.Sql/managedInstances/restorableDroppedDatabases/read | 傳回可還原的已卸除受控資料庫清單。 |
> |  | **managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies** |  |
> | 動作 | Microsoft.Sql/managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies/read | 取得已卸除之受控資料庫中的短期保留原則 |
> | 動作 | Microsoft.Sql/managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies/write | 更新已卸除之受控資料庫中的短期保留原則 |
> |  | **託管實例/安全警報策略** |  |
> | 動作 | Microsoft.Sql/managedInstances/securityAlertPolicies/write | 變更指定受控伺服器的受控伺服器威脅偵測原則 |
> | 動作 | Microsoft.Sql/managedInstances/securityAlertPolicies/read | 檢索為給定伺服器設定的託管伺服器威脅偵測原則的清單 |
> |  | **managedInstances/vulnerabilityAssessments** |  |
> | 動作 | Microsoft.Sql/managedInstances/vulnerabilityAssessments/write | 變更指定受控執行個體的弱點評量 |
> | 動作 | Microsoft.Sql/managedInstances/vulnerabilityAssessments/delete | 移除指定受控執行個體的弱點評量 |
> | 動作 | Microsoft.Sql/managedInstances/vulnerabilityAssessments/read | 擷取指定受控執行個體上的弱點評量原則 |
> |  | **操作** |  |
> | 動作 | Microsoft.Sql/operations/read | 取得可用的 REST 作業 |
> |  | **伺服器** |  |
> | 動作 | Microsoft.Sql/servers/tdeCertificates/action | 建立/更新 TDE 憑證 |
> | 動作 | 微軟.Sql/伺服器/禁用 AzureAD 唯一身份驗證/操作 | 關閉邏輯伺服器時只能關閉 Azure 的目錄身分驗證 |
> | 動作 | Microsoft.Sql/servers/read | 傳回伺服器清單，或取得指定伺服器的屬性。 |
> | 動作 | Microsoft.Sql/servers/write | 使用指定參數建立伺服器，或更新指定伺服器的屬性或標記。 |
> | 動作 | Microsoft.Sql/servers/delete | 刪除現有伺服器。 |
> | 動作 | 微軟.Sql/伺服器/私有端點連接審批/操作 | 確定是否允許使用者批准專用終結點連線 |
> | 動作 | Microsoft.Sql/servers/import/action | 在伺服器上建立新的資料庫，並部署來自 DacPac 套件的結構描述和資料 |
> |  | **伺服器/管理員操作結果** |  |
> | 動作 | Microsoft.Sql/servers/administratorOperationResults/read | 取得關於伺服器系統管理員的進行中作業 |
> |  | **servers/administrators** |  |
> | 動作 | Microsoft.Sql/servers/administrators/read | 取得特定的 Azure 活動目錄管理員物件 |
> | 動作 | Microsoft.Sql/servers/administrators/write | 新增或更新特定的 Azure 活動目錄管理員物件 |
> | 動作 | Microsoft.Sql/servers/administrators/delete | 移除特定的 Azure 活動目錄管理員物件 |
> |  | **servers/advisors** |  |
> | 動作 | Microsoft.Sql/servers/advisors/read | 傳回伺服器可用 Advisor 的清單 |
> | 動作 | Microsoft.Sql/servers/advisors/write | 更新伺服器層級上 Advisor 的自動執行狀態。 |
> |  | **伺服器/顧問/建議的操作** |  |
> | 動作 | Microsoft.Sql/servers/advisors/recommendedActions/read | 傳回伺服器之指定 Advisor 的建議動作清單 |
> | 動作 | Microsoft.Sql/servers/advisors/recommendedActions/write | 對伺服器套用建議動作 |
> |  | **伺服器/稽核原則** |  |
> | 動作 | Microsoft.Sql/servers/auditingPolicies/read | 擷取給定伺服器上所設定之預設伺服器資料表稽核原則的詳細資料 |
> | 動作 | Microsoft.Sql/servers/auditingPolicies/write | 變更給定伺服器的預設伺服器資料表稽核 |
> |  | **servers/auditingSettings** |  |
> | 動作 | Microsoft.Sql/servers/auditingSettings/read | 擷取給定伺服器上所設定之伺服器 Blob 稽核原則的詳細資料 |
> | 動作 | Microsoft.Sql/servers/auditingSettings/write | 變更給定伺服器的伺服器 Blob 稽核 |
> |  | **伺服器/稽核設定/操作結果** |  |
> | 動作 | Microsoft.Sql/servers/auditingSettings/operationResults/read | 擷取伺服器的「Blob 稽核原則設定」作業結果 |
> |  | **伺服器/自動調諧** |  |
> | 動作 | Microsoft.Sql/servers/automaticTuning/read | 傳回伺服器的自動調整設定 |
> | 動作 | Microsoft.Sql/servers/automaticTuning/write | 更新伺服器的自動調整設定，並傳回更新的設定 |
> |  | **servers/backupLongTermRetentionVaults** |  |
> | 動作 | Microsoft.Sql/servers/backupLongTermRetentionVaults/read | 這項作業可用來取得備用的長期保留保存庫。 它會傳回向此伺服器註冊之保存庫的相關資訊 |
> | 動作 | Microsoft.Sql/servers/backupLongTermRetentionVaults/write | 此作業可用來向伺服器註冊備份的長期保留保存庫 |
> | 動作 | Microsoft.Sql/servers/backupLongTermRetentionVaults/delete | 刪除現有的備份封存保存庫。 |
> |  | **servers/communicationLinks** |  |
> | 動作 | Microsoft.Sql/servers/communicationLinks/read | 傳回指定伺服器的通訊連結清單。 |
> | 動作 | Microsoft.Sql/servers/communicationLinks/write | 建立或更新伺服器通訊連結。 |
> | 動作 | Microsoft.Sql/servers/communicationLinks/delete | 刪除現有的伺服器通訊連結。 |
> |  | **servers/connectionPolicies** |  |
> | 動作 | Microsoft.Sql/servers/connectionPolicies/read | 傳回指定伺服器的伺服器連線原則清單。 |
> | 動作 | Microsoft.Sql/servers/connectionPolicies/write | 建立或更新伺服器連線原則。 |
> |  | **servers/databases** |  |
> | 動作 | Microsoft.Sql/servers/databases/read | 傳回資料庫清單，或取得指定資料庫的屬性。 |
> | 動作 | Microsoft.Sql/servers/databases/write | 使用指定參數建立資料庫，或更新指定資料庫的屬性或標記。 |
> | 動作 | Microsoft.Sql/servers/databases/delete | 刪除現有的資料庫。 |
> | 動作 | Microsoft.Sql/servers/databases/pause/action | 暫停 Azure SQL 資料倉儲資料庫 |
> | 動作 | Microsoft.Sql/servers/databases/resume/action | 繼續 Azure SQL 資料倉儲資料庫 |
> | 動作 | Microsoft.Sql/servers/databases/export/action | 匯出 Azure SQL Database |
> | 動作 | Microsoft.Sql/servers/databases/upgradeDataWarehouse/action | 將 Azure SQL 資料倉儲資料庫升級 |
> | 動作 | Microsoft.Sql/servers/databases/move/action | 更改現有資料庫的名稱。 |
> | 動作 | Microsoft.Sql/servers/databases/restorePoints/action | 建立新的還原點 |
> | 動作 | 微軟.Sql/伺服器/資料庫/故障轉移/操作 | 客戶啟動的資料庫故障轉移。 |
> | 動作 | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/action | 執行弱點評估資料庫掃描。 |
> |  | **servers/databases/advisors** |  |
> | 動作 | Microsoft.Sql/servers/databases/advisors/read | 傳回資料庫可用 Advisor 的清單 |
> | 動作 | Microsoft.Sql/servers/databases/advisors/write | 更新資料庫層級上 Advisor 的自動執行狀態。 |
> |  | **伺服器/資料庫/顧問/建議操作** |  |
> | 動作 | Microsoft.Sql/servers/databases/advisors/recommendedActions/read | 傳回資料庫之指定 Advisor 的建議動作清單 |
> | 動作 | Microsoft.Sql/servers/databases/advisors/recommendedActions/write | 對資料庫套用建議動作 |
> |  | **伺服器/資料庫/稽核原則** |  |
> | 動作 | Microsoft.Sql/servers/databases/auditingPolicies/read | 擷取給定資料庫上所設定之資料表稽核原則的詳細資料 |
> | 動作 | Microsoft.Sql/servers/databases/auditingPolicies/write | 變更給定資料庫的資料表稽核原則 |
> |  | **servers/databases/auditingSettings** |  |
> | 動作 | Microsoft.Sql/servers/databases/auditingSettings/read | 擷取給定資料庫上所設定之 Blob 稽核原則的詳細資料 |
> | 動作 | Microsoft.Sql/servers/databases/auditingSettings/write | 變更給定資料庫的 Blob 稽核原則 |
> |  | **伺服器/資料庫/稽核記錄** |  |
> | 動作 | Microsoft.Sql/servers/databases/auditRecords/read | 擷取資料庫 Blob 稽核記錄 |
> |  | **伺服器/資料庫/自動調諧** |  |
> | 動作 | Microsoft.Sql/servers/databases/automaticTuning/read | 傳回資料庫的自動調整設定 |
> | 動作 | Microsoft.Sql/servers/databases/automaticTuning/write | 更新資料庫的自動調整設定，並傳回更新的設定 |
> |  | **伺服器/資料庫/azure 同步操作** |  |
> | 動作 | Microsoft.Sql/servers/databases/azureAsyncOperation/read | 取得資料庫作業的狀態。 |
> |  | **servers/databases/backupLongTermRetentionPolicies** |  |
> | 動作 | Microsoft.Sql/servers/databases/backupLongTermRetentionPolicies/write | 建立或更新資料庫備份封存原則。 |
> | 動作 | Microsoft.Sql/servers/databases/backupLongTermRetentionPolicies/read | 傳回指定資料庫的備份封存原則清單。 |
> |  | **servers/databases/backupShortTermRetentionPolicies** |  |
> | 動作 | 微軟.Sql/伺服器/資料庫/備份短期保留策略/讀取 | 取得資料庫的短期保留原則 |
> | 動作 | 微軟.Sql/伺服器/資料庫/備份短期保留策略/寫入 | 更新資料庫的短期保留原則 |
> |  | **伺服器/資料庫/欄** |  |
> | 動作 | 微軟.Sql/伺服器/資料庫/列/讀取 | 傳回資料庫的欄清單 |
> |  | **servers/databases/connectionPolicies** |  |
> | 動作 | Microsoft.Sql/servers/databases/connectionPolicies/read | 擷取給定資料庫上所設定的連線原則詳細資料 |
> | 動作 | Microsoft.Sql/servers/databases/connectionPolicies/write | 變更給定資料庫的連線原則 |
> |  | **伺服器/資料庫/目前敏感標籤** |  |
> | 動作 | Microsoft.Sql/servers/databases/currentSensitivityLabels/read | 列出指定資料庫的敏感度標籤 |
> | 動作 | 微軟.Sql/伺服器/資料庫/當前敏感標籤/寫入 | 批次更新敏感度標籤 |
> |  | **servers/databases/dataMaskingPolicies** |  |
> | 動作 | Microsoft.Sql/servers/databases/dataMaskingPolicies/read | 傳回資料庫資料遮罩原則的清單。 |
> | 動作 | Microsoft.Sql/servers/databases/dataMaskingPolicies/write | 變更給定伺服器的資料遮罩原則 |
> |  | **servers/databases/dataMaskingPolicies/rules** |  |
> | 動作 | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/read | 擷取給定資料庫上所設定之資料遮罩原則規則的詳細資料 |
> | 動作 | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/write | 變更給定伺服器的資料遮罩原則規則 |
> | 動作 | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/delete | 刪除指定資料庫的資料遮罩原則規則 |
> |  | **伺服器/資料庫/資料倉儲查詢** |  |
> | 動作 | Microsoft.Sql/servers/databases/dataWarehouseQueries/read | 傳回所選查詢識別碼的資料倉儲散發查詢資訊 |
> |  | **伺服器/資料庫/資料倉儲查詢/資料倉儲查詢步驟** |  |
> | 動作 | Microsoft.Sql/servers/databases/dataWarehouseQueries/dataWarehouseQuerySteps/read | 傳回所選步驟識別碼之資料倉儲查詢的分散式查詢步驟資訊 |
> |  | **伺服器/資料庫/資料倉儲用戶活動** |  |
> | 動作 | Microsoft.Sql/servers/databases/dataWarehouseUserActivities/read | 擷取 SQL 資料倉儲執行個體的使用者活動，其中包含執行中和暫止的查詢 |
> |  | **servers/databases/extendedAuditingSettings** |  |
> | 動作 | Microsoft.Sql/servers/databases/extendedAuditingSettings/read | 擷取指定資料庫上所設定之擴充 Blob 稽核原則的詳細資料 |
> | 動作 | Microsoft.Sql/servers/databases/extendedAuditingSettings/write | 變更指定資料庫的擴充 Blob 稽核原則 |
> |  | **servers/databases/extensions** |  |
> | 動作 | Microsoft.Sql/servers/databases/extensions/read | 取得資料庫的擴充功能集合。 |
> | 動作 | Microsoft.Sql/servers/databases/extensions/write | 變更指定資料庫的擴充功能 |
> |  | **伺服器/資料庫/延伸/匯入延伸操作結果** |  |
> | 動作 | 微軟.Sql/伺服器/資料庫/擴展/匯入擴展操作結果/讀取 | 取得正在進行的匯入操作 |
> |  | **servers/databases/geoBackupPolicies** |  |
> | 動作 | Microsoft.Sql/servers/databases/geoBackupPolicies/read | 擷取指定資料庫的異地備份原則 |
> | 動作 | Microsoft.Sql/servers/databases/geoBackupPolicies/write | 建立或更新資料庫異地備份原則 |
> |  | **伺服器/資料庫/匯入匯出操作結果** |  |
> | 動作 | Microsoft.Sql/servers/databases/importExportOperationResults/read | 取得進行中的匯入/匯出作業 |
> |  | **伺服器/資料庫/維護視窗選項** |  |
> | 動作 | Microsoft.Sql/servers/databases/maintenanceWindowOptions/read | 取得所選資料庫的可用維護時間範圍清單。 |
> |  | **伺服器/資料庫/維護視窗** |  |
> | 動作 | Microsoft.Sql/servers/databases/maintenanceWindows/read | 取得所選資料庫的維護時間範圍設定。 |
> | 動作 | Microsoft.Sql/servers/databases/maintenanceWindows/write | 設定所選資料庫的維護時間範圍設定。 |
> |  | **伺服器/資料庫/指標定義** |  |
> | 動作 | Microsoft.Sql/servers/databases/metricDefinitions/read | 傳回可供資料庫使用之計量的類型 |
> |  | **伺服器/資料庫/指標** |  |
> | 動作 | Microsoft.Sql/servers/databases/metrics/read | 傳回資料庫的計量 |
> |  | **伺服器/資料庫/操作結果** |  |
> | 動作 | Microsoft.Sql/servers/databases/operationResults/read | 取得資料庫作業的狀態。 |
> |  | **伺服器/資料庫/操作** |  |
> | 動作 | Microsoft.Sql/servers/databases/operations/cancel/action | 取消尚未完成的 Azure SQL Database 暫止非同步作業。 |
> | 動作 | Microsoft.Sql/servers/databases/operations/read | 傳回在資料庫上執行的作業清單 |
> |  | **伺服器/資料庫/供應商/微軟.見解/診斷設置** |  |
> | 動作 | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/diagnosticSettings/read | 取得資源的診斷設定 |
> | 動作 | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/diagnosticSettings/write | 建立或更新資源的診斷設定 |
> |  | **伺服器/資料庫/供應商/微軟.見解/日誌定義** |  |
> | 動作 | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/logDefinitions/read | 取得資料庫的可用記錄 |
> |  | **伺服器/資料庫/供應商/微軟.見解/指標定義** |  |
> | 動作 | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/metricDefinitions/read | 傳回可供資料庫使用之計量的類型 |
> |  | **伺服器/資料庫/查詢儲存** |  |
> | 動作 | Microsoft.Sql/servers/databases/queryStore/read | 傳回資料庫之查詢存放區設定目前的值。 |
> | 動作 | Microsoft.Sql/servers/databases/queryStore/write | 更新資料庫的查詢存放區設定 |
> |  | **伺服器/資料庫/查詢儲存/查詢文字** |  |
> | 動作 | Microsoft.Sql/servers/databases/queryStore/queryTexts/read | 傳回對應至指定參數的查詢文字集合。 |
> |  | **伺服器/資料庫/推薦敏感標籤** |  |
> | 動作 | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/read | 列出指定資料庫的敏感度標籤 |
> | 動作 | 微軟.Sql/伺服器/資料庫/推薦敏感標籤/寫入 | 大量更新建議敏感度標籤 |
> |  | **伺服器/資料庫/複製連結** |  |
> | 動作 | Microsoft.Sql/servers/databases/replicationLinks/read | 傳回複寫連結的清單，或取得指定複寫連結的屬性。 |
> | 動作 | Microsoft.Sql/servers/databases/replicationLinks/delete | 強制終止複寫關係，但可能遺失資料 |
> | 動作 | Microsoft.Sql/servers/databases/replicationLinks/failover/action | 從主要資料庫同步處理所有變更後進行容錯移轉，讓此資料庫變成複寫關係的主要資料庫，並讓遠端的主要資料庫變成次要資料庫 |
> | 動作 | Microsoft.Sql/servers/databases/replicationLinks/forceFailoverAllowDataLoss/action | 立即進行容錯移轉 (但可能遺失資料)，讓此資料庫變成複寫關係的主要資料庫，並讓遠端的主要資料庫變成次要資料庫 |
> | 動作 | Microsoft.Sql/servers/databases/replicationLinks/updateReplicationMode/action | 將連結的複寫模式更新為同步或非同步模式 |
> | 動作 | Microsoft.Sql/servers/databases/replicationLinks/unlink/action | 強制終止複寫關係，或在與合作夥伴進行同步處理之後終止 |
> |  | **伺服器/資料庫/復原點** |  |
> | 動作 | Microsoft.Sql/servers/databases/restorePoints/read | 傳回資料庫的還原點。 |
> | 動作 | Microsoft.Sql/servers/databases/restorePoints/delete | 刪除資料庫的還原點。 |
> |  | **伺服器/資料庫/架構** |  |
> | 動作 | Microsoft.Sql/servers/databases/schemas/read | 獲取資料庫架構。 |
> |  | **伺服器/資料庫/架構/表** |  |
> | 動作 | Microsoft.Sql/servers/databases/schemas/tables/read | 獲取資料庫表。 |
> |  | **伺服器/資料庫/架構/表/欄** |  |
> | 動作 | Microsoft.Sql/servers/databases/schemas/tables/columns/read | 獲取資料庫列。 |
> |  | **servers/databases/schemas/tables/columns/sensitivityLabels** |  |
> | 動作 | 微軟.Sql/伺服器/資料庫/架構/表/列/敏感度標籤/啟用/操作 | 在給定欄上開啟敏感性建議 |
> | 動作 | 微軟.Sql/伺服器/資料庫/架構/表/列/敏感度標籤/禁用/操作 | 關閉給定列的敏感度建議 |
> | 動作 | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/read | 取得指定資料行的敏感度標籤 |
> | 動作 | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/write | 建立或更新指定資料行的敏感度標籤 |
> | 動作 | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/delete | 刪除指定資料行的敏感度標籤 |
> |  | **伺服器/資料庫/架構/表/建議索引** |  |
> | 動作 | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/read | 擷取資料庫上的索引建議清單 |
> | 動作 | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/write | 套用索引建議 |
> |  | **servers/databases/securityAlertPolicies** |  |
> | 動作 | Microsoft.Sql/servers/databases/securityAlertPolicies/write | 變更指定資料庫的資料庫威脅偵測原則 |
> | 動作 | Microsoft.Sql/servers/databases/securityAlertPolicies/read | 檢索為給定伺服器設定的資料庫威脅偵測原則的清單 |
> |  | **伺服器/資料庫/安全指標** |  |
> | 動作 | Microsoft.Sql/servers/databases/securityMetrics/read | 取得資料庫安全性計量的集合 |
> |  | **伺服器/資料庫/敏感度標籤** |  |
> | 動作 | Microsoft.Sql/servers/databases/sensitivityLabels/read | 列出指定資料庫的敏感度標籤 |
> |  | **伺服器/資料庫/服務級顧問** |  |
> | 動作 | Microsoft.Sql/servers/databases/serviceTierAdvisors/read | 傳回相關建議，以讓您根據查詢執行統計資料來相應增加或減少資料庫，以提升效能或降低成本 |
> |  | **伺服器/資料庫/skus** |  |
> | 動作 | Microsoft.Sql/servers/databases/skus/read | 取得可供資料庫使用的 SKU 集合 |
> |  | **servers/databases/syncGroups** |  |
> | 動作 | Microsoft.Sql/servers/databases/syncGroups/refreshHubSchema/action | 重新整理同步中樞資料庫結構描述 |
> | 動作 | Microsoft.Sql/servers/databases/syncGroups/cancelSync/action | 取消同步群組同步處理 |
> | 動作 | Microsoft.Sql/servers/databases/syncGroups/triggerSync/action | 觸發同步群組同步處理 |
> | 動作 | Microsoft.Sql/servers/databases/syncGroups/read | 傳回同步群組的清單，或取得指定同步群組的屬性。 |
> | 動作 | Microsoft.Sql/servers/databases/syncGroups/write | 使用指定參數建立同步群組，或更新指定同步群組的屬性。 |
> | 動作 | Microsoft.Sql/servers/databases/syncGroups/delete | 刪除現有的同步群組。 |
> |  | **伺服器/資料庫/同步群組/中心架構** |  |
> | 動作 | Microsoft.Sql/servers/databases/syncGroups/hubSchemas/read | 傳回同步中樞資料庫結構描述的清單 |
> |  | **伺服器/資料庫/同步群組/紀錄** |  |
> | 動作 | Microsoft.Sql/servers/databases/syncGroups/logs/read | 傳回同步群組記錄的清單 |
> |  | **伺服器/資料庫/同步群組/刷新Hub架構操作結果** |  |
> | 動作 | Microsoft.Sql/servers/databases/syncGroups/refreshHubSchemaOperationResults/read | 擷取同步中樞結構描述重新整理作業的結果 |
> |  | **servers/databases/syncGroups/syncMembers** |  |
> | 動作 | Microsoft.Sql/servers/databases/syncGroups/syncMembers/read | 傳回同步成員的清單，或取得指定同步成員的屬性。 |
> | 動作 | Microsoft.Sql/servers/databases/syncGroups/syncMembers/write | 使用指定參數建立同步成員，或更新指定同步成員的屬性。 |
> | 動作 | Microsoft.Sql/servers/databases/syncGroups/syncMembers/delete | 刪除現有的同步成員。 |
> | 動作 | Microsoft.Sql/servers/databases/syncGroups/syncMembers/refreshSchema/action | 重新整理同步成員結構描述 |
> |  | **伺服器/資料庫/同步群組/同步成員/刷新架構操作結果** |  |
> | 動作 | Microsoft.Sql/servers/databases/syncGroups/syncMembers/refreshSchemaOperationResults/read | 擷取同步成員結構描述重新整理作業的結果 |
> |  | **伺服器/資料庫/同步群組/同步成員/架構** |  |
> | 動作 | Microsoft.Sql/servers/databases/syncGroups/syncMembers/schemas/read | 傳回同步成員資料庫結構描述的清單 |
> |  | **伺服器/資料庫/頂層查詢** |  |
> | 動作 | Microsoft.Sql/servers/databases/topQueries/queryText/action | 傳回所選查詢識別碼的 Transact-SQL 文字 |
> | 動作 | Microsoft.Sql/servers/databases/topQueries/read | 傳回所選查詢在所選時段內彙總的執行階段統計資料 |
> |  | **伺服器/資料庫/頂層查詢/統計資訊** |  |
> | 動作 | Microsoft.Sql/servers/databases/topQueries/statistics/read | 傳回所選查詢在所選時段內彙總的執行階段統計資料 |
> |  | **servers/databases/transparentDataEncryption** |  |
> | 動作 | Microsoft.Sql/servers/databases/transparentDataEncryption/read | 檢索給定託管資料庫上邏輯資料庫透明資料加密的詳細資訊 |
> | 動作 | Microsoft.Sql/servers/databases/transparentDataEncryption/write | 變更給定邏輯資料庫的資料庫的透明度資料加密 |
> |  | **伺服器/資料庫/透明資料加密/操作結果** |  |
> | 動作 | Microsoft.Sql/servers/databases/transparentDataEncryption/operationResults/read | 取得有關透明資料加密的進行中作業 |
> |  | **伺服器/資料庫/使用方式** |  |
> | 動作 | Microsoft.Sql/servers/databases/usages/read | 取得 Azure SQL Database 的使用方式資訊 |
> |  | **servers/databases/vulnerabilityAssessments** |  |
> | 動作 | Microsoft.Sql/servers/databases/vulnerabilityAssessments/write | 變更指定資料庫的弱點評量 |
> | 動作 | Microsoft.Sql/servers/databases/vulnerabilityAssessments/delete | 移除指定資料庫的弱點評量 |
> | 動作 | Microsoft.Sql/servers/databases/vulnerabilityAssessments/read | 擷取指定資料庫上的弱點評量原則 |
> |  | **servers/databases/vulnerabilityAssessments/rules/baselines** |  |
> | 動作 | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/delete | 移除指定資料庫的弱點評量規則基準 |
> | 動作 | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/write | 變更指定資料庫的弱點評量規則基準 |
> | 動作 | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/read | 取得指定資料庫的弱點評量規則基準 |
> |  | **伺服器/資料庫/漏洞評估/掃描** |  |
> | 動作 | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/initiateScan/action | 執行弱點評估資料庫掃描。 |
> | 動作 | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/read | 傳回資料庫弱點評量掃描記錄的清單，或取得指定掃描識別碼的掃描記錄。 |
> | 動作 | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/export/action | 將現有的掃描結果轉換成人類可讀取的格式。 如果已存在，則不會執行任何動作 |
> |  | **伺服器/資料庫/漏洞評估掃描/操作結果** |  |
> | 動作 | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/operationResults/read | 擷取資料庫弱點評量掃描執行作業的結果 |
> |  | **伺服器/資料庫/漏洞評估設定** |  |
> | 動作 | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/read | 擷取指定資料庫上所設定之弱點評量的詳細資料 |
> | 動作 | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/write | 變更指定資料庫的弱點評量 |
> |  | **伺服器/資料庫/工作負載器** |  |
> | 動作 | 微軟.Sql/伺服器/資料庫/工作負載組/讀取 | 列出所選資料庫的工作負載組。 |
> | 動作 | 微軟.Sql/伺服器/資料庫/工作負載組/寫入 | 設置特定工作負荷組的屬性。 |
> | 動作 | 微軟.Sql/伺服器/資料庫/工作負載組/刪除 | 刪除特定的工作負載組。 |
> |  | **伺服器/資料庫/工作負載組/工作負載分類器** |  |
> | 動作 | 微軟.Sql/伺服器/資料庫/工作負載組/工作負載分類器/讀取 | 列出所選資料庫的工作負載分類器。 |
> | 動作 | 微軟.Sql/伺服器/資料庫/工作負載組/工作負載分類器/寫入 | 設置特定工作負載分類器的屬性。 |
> | 動作 | 微軟.Sql/伺服器/資料庫/工作負載組/工作負載分類器/刪除 | 丟棄特定的工作負載分類器。 |
> |  | **servers/disasterRecoveryConfiguration** |  |
> | 動作 | Microsoft.Sql/servers/disasterRecoveryConfiguration/read | 取得包括此伺服器在內的災害復原設定集合 |
> | 動作 | Microsoft.Sql/servers/disasterRecoveryConfiguration/write | 變更伺服器的災害復原設定 |
> | 動作 | Microsoft.Sql/servers/disasterRecoveryConfiguration/delete | 刪除指定伺服器的現有災害復原設定 |
> | 動作 | Microsoft.Sql/servers/disasterRecoveryConfiguration/failover/action | 進行 DisasterRecoveryConfiguration 的容錯移轉 |
> | 動作 | Microsoft.Sql/servers/disasterRecoveryConfiguration/forceFailoverAllowDataLoss/action | 強制進行 DisasterRecoveryConfiguration 的容錯移轉 |
> |  | **伺服器/彈性池估計** |  |
> | 動作 | Microsoft.Sql/servers/elasticPoolEstimates/read | 傳回已針對此伺服器建立之彈性集區估計值的清單 |
> | 動作 | Microsoft.Sql/servers/elasticPoolEstimates/write | 針對所提供的資料庫清單建立新的彈性集區估計值 |
> |  | **servers/elasticPools** |  |
> | 動作 | Microsoft.Sql/servers/elasticPools/read | 擷取指定伺服器上彈性集區的詳細資料 |
> | 動作 | Microsoft.Sql/servers/elasticPools/write | 建立新的彈性集區，或變更現有彈性集區的屬性 |
> | 動作 | Microsoft.Sql/servers/elasticPools/delete | 刪除現有彈性集區 |
> | 動作 | 微軟.Sql/伺服器/彈性池/故障轉移/操作 | 客戶啟動的彈性池故障轉移。 |
> |  | **伺服器/彈性池/顧問** |  |
> | 動作 | Microsoft.Sql/servers/elasticPools/advisors/read | 傳回彈性集區可用 Advisor 的清單 |
> | 動作 | Microsoft.Sql/servers/elasticPools/advisors/write | 更新彈性集區層級上 Advisor 的自動執行狀態。 |
> |  | **伺服器/彈性池/顧問/建議操作** |  |
> | 動作 | Microsoft.Sql/servers/elasticPools/advisors/recommendedActions/read | 傳回彈性集區之指定 Advisor 的建議動作清單 |
> | 動作 | Microsoft.Sql/servers/elasticPools/advisors/recommendedActions/write | 對彈性集區套用建議動作 |
> |  | **伺服器/彈性池/資料庫** |  |
> | 動作 | Microsoft.Sql/servers/elasticPools/databases/read | 取得彈性集區的資料庫清單 |
> |  | **伺服器/彈性池/彈性池活動** |  |
> | 動作 | Microsoft.Sql/servers/elasticPools/elasticPoolActivity/read | 擷取給定彈性資料庫集區的活動和詳細資料 |
> |  | **伺服器/彈性池/彈性池資料庫活動** |  |
> | 動作 | Microsoft.Sql/servers/elasticPools/elasticPoolDatabaseActivity/read | 擷取屬於彈性資料庫集區之給定資料庫的活動和詳細資料 |
> |  | **伺服器/彈性池/指標定義** |  |
> | 動作 | Microsoft.Sql/servers/elasticPools/metricDefinitions/read | 傳回可供彈性資料庫集區使用之計量的類型 |
> |  | **伺服器/彈性池/指標** |  |
> | 動作 | Microsoft.Sql/servers/elasticPools/metrics/read | 傳回彈性資料庫集區的計量 |
> |  | **伺服器/彈性池/操作** |  |
> | 動作 | Microsoft.Sql/servers/elasticPools/operations/cancel/action | 取消尚未完成的 Azure SQL 彈性集區暫止非同步作業。 |
> | 動作 | Microsoft.Sql/servers/elasticPools/operations/read | 傳回在彈性集區上執行的作業清單 |
> |  | **伺服器/彈性池/供應商/微軟.見解/診斷設置** |  |
> | 動作 | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/read | 取得資源的診斷設定 |
> | 動作 | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/write | 建立或更新資源的診斷設定 |
> |  | **伺服器/彈性池/提供程式/微軟.見解/指標定義** |  |
> | 動作 | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/metricDefinitions/read | 傳回可供彈性資料庫集區使用之計量的類型 |
> |  | **伺服器/彈性池/滑閥** |  |
> | 動作 | Microsoft.Sql/servers/elasticPools/skus/read | 取得可供彈性集區使用的 SKU 集合 |
> |  | **servers/encryptionProtector** |  |
> | 動作 | 微軟.Sql/伺服器/加密保護器/重新驗證/操作 | 更新指定伺服器加密保護裝置的屬性。 |
> | 動作 | Microsoft.Sql/servers/encryptionProtector/read | 傳回伺服器加密保護裝置的清單，或取得指定伺服器加密保護裝置的屬性。 |
> | 動作 | Microsoft.Sql/servers/encryptionProtector/write | 更新指定伺服器加密保護裝置的屬性。 |
> |  | **servers/extendedAuditingSettings** |  |
> | 動作 | Microsoft.Sql/servers/extendedAuditingSettings/read | 擷取指定伺服器上所設定之擴充伺服器 Blob 稽核原則的詳細資料 |
> | 動作 | Microsoft.Sql/servers/extendedAuditingSettings/write | 變更指定伺服器的擴充伺服器 Blob 稽核 |
> |  | **servers/failoverGroups** |  |
> | 動作 | Microsoft.Sql/servers/failoverGroups/read | 傳回容錯移轉群組的清單，或取得指定容錯移轉群組的屬性。 |
> | 動作 | Microsoft.Sql/servers/failoverGroups/write | 使用指定參數建立容錯移轉群組，或更新指定容錯移轉的屬性或標記。 |
> | 動作 | Microsoft.Sql/servers/failoverGroups/delete | 刪除現有的容錯移轉群組。 |
> | 動作 | Microsoft.Sql/servers/failoverGroups/failover/action | 在現有的容錯移轉群組中執行規劃的容錯移轉。 |
> | 動作 | Microsoft.Sql/servers/failoverGroups/forceFailoverAllowDataLoss/action | 在現有的容錯移轉群組中執行強制容錯移轉。 |
> |  | **servers/firewallRules** |  |
> | 動作 | Microsoft.Sql/servers/firewallRules/write | 使用指定參數建立伺服器防火牆規則、更新指定規則的屬性，或使用新的伺服器防火牆規則覆寫所有現有的規則。 |
> | 動作 | Microsoft.Sql/servers/firewallRules/read | 傳回伺服器防火牆規則的清單，或取得指定伺服器防火牆規則的屬性。 |
> | 動作 | Microsoft.Sql/servers/firewallRules/delete | 刪除現有的伺服器防火牆規則。 |
> |  | **伺服器/匯入匯出操作結果** |  |
> | 動作 | Microsoft.Sql/servers/importExportOperationResults/read | 取得進行中的匯入/匯出作業 |
> |  | **伺服器/無法存取的資料庫** |  |
> | 動作 | 微軟.Sql/伺服器/無法存取的資料庫/讀取 | 返回邏輯伺服器中無法訪問的資料庫的清單。 |
> |  | **伺服器/介面端點設定檔** |  |
> | 動作 | Microsoft.Sql/servers/interfaceEndpointProfiles/write | 使用指定參數建立介面端點設定檔，或更新指定介面端點的屬性或標記 |
> | 動作 | Microsoft.Sql/servers/interfaceEndpointProfiles/read | 傳回指定介面端點設定檔的屬性 |
> | 動作 | Microsoft.Sql/servers/interfaceEndpointProfiles/delete | 刪除指定的介面端點設定檔 |
> |  | **伺服器/工作代理** |  |
> | 動作 | Microsoft.Sql/servers/jobAgents/read | 取得 Azure SQL DB 工作代理程式 |
> | 動作 | Microsoft.Sql/servers/jobAgents/write | 建立或更新 Azure SQL DB 工作代理程式 |
> | 動作 | Microsoft.Sql/servers/jobAgents/delete | 刪除 Azure SQL DB 工作代理程式 |
> |  | **servers/keys** |  |
> | 動作 | Microsoft.Sql/servers/keys/read | 傳回伺服器金鑰的清單，或取得指定伺服器金鑰的屬性。 |
> | 動作 | Microsoft.Sql/servers/keys/write | 使用指定參數建立金鑰，或更新指定伺服器金鑰的屬性或標記。 |
> | 動作 | Microsoft.Sql/servers/keys/delete | 刪除現有的伺服器金鑰。 |
> |  | **伺服器/動作結果** |  |
> | 動作 | Microsoft.Sql/servers/operationResults/read | 取得進行中的伺服器作業 |
> |  | **伺服器/操作** |  |
> | 動作 | 微軟.Sql/伺服器/操作/讀取 | 傳回在伺服器上執行的操作清單 |
> |  | **伺服器/專用端接連線Proxies** |  |
> | 動作 | 微軟.Sql/伺服器/私人端接連接Proxies/驗證/操作 | 驗證來自 NRP 連接的專屬的檢查點 |
> | 動作 | 微軟.Sql/伺服器/私人端接連接Proxies/讀取 | 返回私有終結點連接代理的清單或獲取指定專用終結點連接代理的屬性。 |
> | 動作 | 微軟.Sql/伺服器/私人端接連接Proxies/寫入 | 使用指定的參數創建專用終結點連接代理,或更新指定專用終結點連接代理的屬性或標記。 |
> | 動作 | 微軟.Sql/伺服器/私人端接連接Proxies/刪除 | 移除現有專用終結點連線代理 |
> |  | **伺服器/專用端點連接** |  |
> | 動作 | 微軟.Sql/伺服器/私有端點連接/讀取 | 返回專用終結點連接的清單或獲取指定專用終結點連接的屬性。 |
> | 動作 | 微軟.Sql/伺服器/專用端接連接/刪除 | 移除現有的專用終結點連線 |
> | 動作 | 微軟.Sql/伺服器/私有端點連接/寫入 | 批准或拒絕現有專用終結點連線 |
> |  | **伺服器/專用連結資源** |  |
> | 動作 | 微軟.Sql/伺服器/私有連結資源/讀取 | 取得此檔案的網串資源 |
> |  | **伺服器/供應商/微軟.見解/指標定義** |  |
> | 動作 | Microsoft.Sql/servers/providers/Microsoft.Insights/metricDefinitions/read | 傳回可供伺服器使用的計量類型 |
> |  | **伺服器/推薦的彈性池** |  |
> | 動作 | Microsoft.Sql/servers/recommendedElasticPools/read | 擷取彈性資料庫集區的建議，以根據資源的歷史使用量來降低成本或改善效能 |
> |  | **伺服器/推薦的彈性池/資料庫** |  |
> | 動作 | Microsoft.Sql/servers/recommendedElasticPools/databases/read | 擷取給定伺服器之建議彈性資料庫集區的計量 |
> |  | **伺服器/可修復資料庫** |  |
> | 動作 | Microsoft.Sql/servers/recoverableDatabases/read | 這項作業可用於即時資料庫的災害復原，以將資料庫還原至最後一個已知良好的備份點。 它會傳回最後一個良好備份的相關資訊，但它實際上並不會還原資料庫。 |
> |  | **伺服器/複製連結** |  |
> | 動作 | Microsoft.Sql/servers/replicationLinks/read | 傳回複寫連結的清單，或取得指定複寫連結的屬性。 |
> |  | **servers/restorableDroppedDatabases** |  |
> | 動作 | Microsoft.Sql/servers/restorableDroppedDatabases/read | 取得指定伺服器上雖已捨棄但仍留在保留原則內的資料庫清單。 |
> |  | **servers/securityAlertPolicies** |  |
> | 動作 | Microsoft.Sql/servers/securityAlertPolicies/write | 變更指定伺服器的伺服器威脅偵測原則 |
> | 動作 | Microsoft.Sql/servers/securityAlertPolicies/read | 檢索為給定伺服器設定的伺服器威脅偵測原則清單 |
> |  | **伺服器/安全警報策略/操作結果** |  |
> | 動作 | Microsoft.Sql/servers/securityAlertPolicies/operationResults/read | 擷取伺服器威脅偵測原則寫入作業的結果 |
> |  | **伺服器/服務目標** |  |
> | 動作 | Microsoft.Sql/servers/serviceObjectives/read | 擷取給定伺服器上可用的服務等級目標 (也稱為效能層級) 清單 |
> |  | **servers/syncAgents** |  |
> | 動作 | Microsoft.Sql/servers/syncAgents/read | 傳回同步代理程式的清單，或取得指定同步代理程式的屬性。 |
> | 動作 | Microsoft.Sql/servers/syncAgents/write | 使用指定參數建立同步代理程式，或更新指定同步代理程式的屬性。 |
> | 動作 | Microsoft.Sql/servers/syncAgents/delete | 刪除現有的同步代理程式。 |
> | 動作 | Microsoft.Sql/servers/syncAgents/generateKey/action | 產生同步代理程式登錄金鑰 |
> |  | **伺服器/同步代理/連結資料庫** |  |
> | 動作 | Microsoft.Sql/servers/syncAgents/linkedDatabases/read | 傳回已連結同步代理程式的資料庫清單 |
> |  | **伺服器/使用方式** |  |
> | 動作 | Microsoft.Sql/servers/usages/read | 傳回伺服器 DTU 配額和伺服器內所有資料庫的目前 DTU 使用 |
> |  | **servers/virtualNetworkRules** |  |
> | 動作 | Microsoft.Sql/servers/virtualNetworkRules/read | 傳回虛擬網路規則的清單，或取得指定虛擬網路規則的屬性。 |
> | 動作 | Microsoft.Sql/servers/virtualNetworkRules/write | 使用指定參數建立虛擬網路規則，或更新指定虛擬網路規則的屬性或標記。 |
> | 動作 | Microsoft.Sql/servers/virtualNetworkRules/delete | 刪除現有虛擬網路規則 |
> |  | **servers/vulnerabilityAssessments** |  |
> | 動作 | Microsoft.Sql/servers/vulnerabilityAssessments/write | 變更指定伺服器的弱點評量 |
> | 動作 | Microsoft.Sql/servers/vulnerabilityAssessments/delete | 移除指定伺服器的弱點評量 |
> | 動作 | Microsoft.Sql/servers/vulnerabilityAssessments/read | 擷取指定伺服器上的弱點評量原則 |
> |  | **虛擬叢集** |  |
> | 動作 | Microsoft.Sql/virtualClusters/read | 傳回虛擬叢集清單，或取得指定虛擬叢集的屬性。 |
> | 動作 | Microsoft.Sql/virtualClusters/write | 更新虛擬叢集標記。 |
> | 動作 | 微軟.Sql/虛擬群集/刪除 | 刪除現有虛擬群集。 |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

Azure 服務[:Azure 虛擬機器上的 SQL 伺服器](../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | 微軟.Sql虛擬電腦/註冊/操作 | 向 Microsoft.Sql 虛擬電腦資源提供程式註冊訂閱 |
> | 動作 | 微軟.Sql虛擬電腦/取消註冊/操作 | 向 Microsoft.Sql 虛擬電腦資源提供程式取消註冊訂閱 |
> |  | **位置** |  |
> | 動作 | 微軟.Sql虛擬電腦/位置/寄存器SqlVm候選/操作 | 註冊 SQL Vm 候選 |
> |  | **位置/可用性組偵聽操作結果** |  |
> | 動作 | 微軟.Sql虛擬電腦/位置/可用性組偵聽器操作結果/讀取 | 取得可用性組偵聽器操作的結果 |
> |  | **位置/sql虛擬電腦組動作結果** |  |
> | 動作 | 微軟.Sql虛擬電腦/位置/sql虛擬電腦組操作結果/讀取 | 取得 SQL 虛擬機組操作的結果 |
> |  | **位置/sql虛擬電腦操作結果** |  |
> | 動作 | 微軟.Sql虛擬電腦/位置/sql虛擬電腦操作結果/讀取 | 取得 SQL 虛擬機器操作的結果 |
> |  | **操作** |  |
> | 動作 | 微軟.Sql虛擬機器/操作/讀取 |  |
> |  | **sql 虛擬電腦群組** |  |
> | 動作 | 微軟.Sql虛擬電腦/sql虛擬電腦組/讀取 | 重新介紹 SQL 虛擬機組的詳細資訊 |
> | 動作 | 微軟.Sql虛擬電腦/sql虛擬電腦組/寫入 | 建立新或變更現有 SQL 虛擬機組的屬性 |
> | 動作 | 微軟.Sql虛擬電腦/sql虛擬電腦組/刪除 | 移除現有的 SQL 虛擬機組 |
> |  | **sql 虛擬電腦群組/可用性組偵聽器** |  |
> | 動作 | 微軟.Sql虛擬電腦/sql虛擬電腦組/可用性組偵聽器/讀取 | 檢索給定 SQL 虛擬機組上的 SQL 可用性組偵聽器的詳細資訊 |
> | 動作 | 微軟.Sql虛擬電腦/sql虛擬電腦組/可用性組偵聽器/寫入 | 建立新的 SQL 可用性組偵聽器的屬性或變更屬性 |
> | 動作 | 微軟.Sql虛擬電腦/sql虛擬電腦組/可用性組偵聽器/刪除 | 移除現有可用性組偵聽器 |
> |  | **sql 虛擬電腦群組/sql 虛擬機器** |  |
> | 動作 | 微軟.Sql虛擬電腦/sql虛擬電腦組/sql虛擬電腦/讀取 | 依特定 sql 虛擬虛擬機組列出 Sql 虛擬機器 |
> |  | **sql 虛擬機器** |  |
> | 動作 | 微軟.Sql虛擬電腦/sql虛擬電腦/讀取 | 檢索 SQL 虛擬機器的詳細資訊 |
> | 動作 | 微軟.Sql虛擬電腦/sql虛擬電腦/寫入 | 建立新或變更現有 SQL 虛擬機器屬性 |
> | 動作 | 微軟.Sql虛擬電腦/sql虛擬電腦/刪除 | 移除現有 SQL 虛擬機器 |

## <a name="microsoftstorage"></a>Microsoft.Storage

Azure 服務:[儲存](../storage/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.Storage/register/action | 針對儲存體資源提供者註冊訂用帳戶，並讓您能夠建立儲存體帳戶。 |
> |  | **checknameavailability** |  |
> | 動作 | Microsoft.Storage/checknameavailability/read | 確認帳戶名稱有效，且並非使用中。 |
> |  | **位置** |  |
> | 動作 | Microsoft.Storage/locations/deleteVirtualNetworkOrSubnets/action | 讓 Microsoft.Storage 知道虛擬網路或子網路即將刪除 |
> |  | **位置/檢查名稱可用性** |  |
> | 動作 | 微軟儲存/位置/檢查名稱可用性/讀取 | 確認帳戶名稱有效，且並非使用中。 |
> |  | **位置/用法** |  |
> | 動作 | Microsoft.Storage/locations/usages/read | 傳回指定訂用帳戶資源的限制和目前的使用量計數 |
> |  | **操作** |  |
> | 動作 | Microsoft.Storage/operations/read | 輪詢非同步作業的狀態。 |
> |  | **斯克庫斯** |  |
> | 動作 | Microsoft.Storage/skus/read | 列出 Microsoft.Storage 所支援的 SKU。 |
> |  | **storageAccounts** |  |
> | 動作 | 微軟.存儲/儲存帳戶/恢復Blob範圍/操作 | 將 Blob 範圍回復到指定時間的狀態 |
> | 動作 | 微軟.存儲/存儲帳戶/專用端接連接審批/操作 | 批准專用終結點連線 |
> | 動作 | 微軟.存儲/儲存帳戶/故障轉移/操作 | 客戶能夠在出現可用性問題時控制故障轉移 |
> | 動作 | Microsoft.Storage/storageAccounts/listkeys/action | 傳回指定儲存體帳戶的存取金鑰。 |
> | 動作 | Microsoft.Storage/storageAccounts/regeneratekey/action | 重新產生指定儲存體帳戶的存取金鑰。 |
> | 動作 | Microsoft.Storage/storageAccounts/revokeUserDelegationKeys/action | 撤銷所指定儲存體帳戶的所有使用者委派金鑰。 |
> | 動作 | Microsoft.Storage/storageAccounts/delete | 刪除現有的儲存體帳戶。 |
> | 動作 | Microsoft.Storage/storageAccounts/read | 傳回儲存體帳戶清單，或取得指定儲存體帳戶的屬性。 |
> | 動作 | Microsoft.Storage/storageAccounts/listAccountSas/action | 傳回指定儲存體帳戶的帳戶 SAS 權杖。 |
> | 動作 | Microsoft.Storage/storageAccounts/listServiceSas/action | 傳回指定儲存體帳戶的服務 SAS 權杖。 |
> | 動作 | Microsoft.Storage/storageAccounts/write | 使用指定參數來建立儲存體帳戶、更新指定儲存體帳戶的屬性或標記，或新增指定儲存體帳戶的自訂網域。 |
> |  | **storageAccounts/blobServices** |  |
> | 動作 | Microsoft.Storage/storageAccounts/blobServices/read |  |
> | 動作 | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | 傳回 Blob 服務的使用者委派金鑰 |
> | 動作 | Microsoft.Storage/storageAccounts/blobServices/write | 傳回放置 Blob 服務屬性的結果 |
> | 動作 | Microsoft.Storage/storageAccounts/blobServices/read | 傳回 Blob 服務屬性或統計資料 |
> |  | **儲存帳號/blob 服務/容器** |  |
> | 動作 | Microsoft.Storage/storageAccounts/blobServices/containers/write | 傳回修補 Blob 容器的結果 |
> | 動作 | Microsoft.Storage/storageAccounts/blobServices/containers/delete | 傳回刪除容器的結果 |
> | 動作 | Microsoft.Storage/storageAccounts/blobServices/containers/read | 傳回容器 |
> | 動作 | Microsoft.Storage/storageAccounts/blobServices/containers/read | 傳回容器的清單 |
> | 動作 | Microsoft.Storage/storageAccounts/blobServices/containers/lease/action | 傳回租用 Blob 容器的結果 |
> | 動作 | Microsoft.Storage/storageAccounts/blobServices/containers/write | 傳回放置 Blob 容器的結果 |
> | 動作 | Microsoft.Storage/storageAccounts/blobServices/containers/clearLegalHold/action | 清除 Blob 容器法務保存措施 |
> | 動作 | Microsoft.Storage/storageAccounts/blobServices/containers/setLegalHold/action | 設定 Blob 容器法務保存措施 |
> |  | **儲存帳號/blob 服務/容器/不可變性策略** |  |
> | 動作 | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/extend/action | 擴充 Blob 容器不變性原則 |
> | 動作 | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/delete | 刪除 Blob 容器不變性原則 |
> | 動作 | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/write | 放置 Blob 容器不變性原則 |
> | 動作 | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/lock/action | 鎖定 Blob 容器不變性原則 |
> | 動作 | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/read | 取得 Blob 容器不變性原則 |
> |  | **儲存帳戶/資料共享原則** |  |
> | 動作 | 微軟.儲存/儲存帳戶/資料共享策略/刪除 |  |
> | 動作 | 微軟.存儲/儲存帳戶/數據共享策略/讀取 |  |
> | 動作 | 微軟.存儲/儲存帳戶/數據共享策略/讀取 |  |
> | 動作 | 微軟.存儲/儲存帳戶/數據共享策略/寫入 |  |
> |  | **儲存帳號/加密範圍** |  |
> | 動作 | 微軟儲存記憶體 |  |
> | 動作 | 微軟儲存記憶體 |  |
> | 動作 | 微軟儲存記憶體/儲存帳戶/加密範圍/寫入 |  |
> | 動作 | 微軟儲存記憶體/儲存帳戶/加密範圍/寫入 |  |
> |  | **storageAccounts/fileServices** |  |
> | 動作 | 微軟.儲存/儲存帳戶/檔案服務/共用/操作 |  |
> | 動作 | 微軟.儲存/儲存帳戶/檔案服務/讀取 |  |
> | 動作 | 微軟儲存記憶體/儲存帳戶/檔案服務/寫入 |  |
> | 動作 | 微軟.儲存/儲存帳戶/檔案服務/讀取 | 取得檔案服務屬性 |
> |  | **儲存帳號/檔案服務/共用** |  |
> | 動作 | 微軟儲存記憶體/儲存帳戶/檔案服務/共用/刪除 |  |
> | 動作 | 微軟.存儲/儲存帳戶/檔案服務/共用/讀取 |  |
> | 動作 | 微軟.存儲/儲存帳戶/檔案服務/共用/讀取 |  |
> | 動作 | 微軟.儲存/儲存帳戶/檔案服務/共用/寫入 |  |
> |  | **儲存帳號/本地端使用者** |  |
> | 動作 | 微軟儲存記憶體/儲存帳戶/本地使用者/刪除 |  |
> | 動作 | 微軟儲存記憶體/儲存帳戶/本地使用者/清單鍵/操作 |  |
> | 動作 | 微軟.儲存/儲存帳戶/本地使用者/讀取 |  |
> | 動作 | 微軟.儲存/儲存帳戶/本地使用者/讀取 |  |
> | 動作 | 微軟儲存記憶體/儲存帳戶/本地使用者/寫入 |  |
> |  | **儲存帳戶/管理原則** |  |
> | 動作 | 微軟.儲存/儲存帳戶/管理原則/刪除 | 移除儲存帳戶管理原則 |
> | 動作 | 微軟.儲存/儲存帳戶/管理原則/讀取 | 取得儲存管理帳戶原則 |
> | 動作 | 微軟.儲存/儲存帳戶/管理原則/寫入 | 放置儲存帳戶管理原則 |
> |  | **儲存帳號/物件複製原則** |  |
> | 動作 | 微軟儲存記憶體檔/儲存帳戶/物件複製策略/刪除 |  |
> | 動作 | 微軟.儲存/儲存帳戶/物件複製策略/讀取 |  |
> | 動作 | 微軟.儲存/儲存帳戶/物件複製策略/讀取 |  |
> | 動作 | 微軟.儲存/儲存帳戶/物件複製策略/寫入 |  |
> |  | **儲存帳號/專用端接連線 Proxies** |  |
> | 動作 | 微軟.存儲/存儲帳戶/私有端接連接Proxies/讀取 | 取得專用終結點連線代理 |
> | 動作 | 微軟.儲存/儲存帳戶/私人端接連接Proxies/刪除 | 移除專用終結點連線代理 |
> | 動作 | 微軟.存儲/存儲帳戶/私有端接連接Proxies/寫入 | 放置專用終結點連線代理 |
> |  | **儲存帳戶/專用終結點連線** |  |
> | 動作 | 微軟.儲存/儲存帳戶/專用端點連接/刪除 | 移除專用終結點連線 |
> | 動作 | 微軟.儲存/儲存帳戶/專用端點連接/讀取 | 取得專用終結點連線 |
> | 動作 | 微軟.存儲/儲存帳戶/私有端點連接/寫入 | 放置專用終結點連線 |
> |  | **儲存帳戶/私有連結資源** |  |
> | 動作 | 微軟.存儲/存儲帳戶/私有鏈接資源/讀取 | 取得儲存帳戶群組 ID |
> |  | **storageAccounts/queueServices** |  |
> | 動作 | Microsoft.Storage/storageAccounts/queueServices/read | 取得佇列服務屬性 |
> | 動作 | Microsoft.Storage/storageAccounts/queueServices/read | 傳回佇列服務屬性或統計資料。 |
> | 動作 | Microsoft.Storage/storageAccounts/queueServices/write | 傳回設定佇列服務屬性的結果 |
> |  | **儲存帳戶/佇列服務/佇列** |  |
> | 動作 | Microsoft.Storage/storageAccounts/queueServices/queues/read | 傳回佇列或佇列清單。 |
> | 動作 | Microsoft.Storage/storageAccounts/queueServices/queues/write | 傳回寫入佇列的結果 |
> | 動作 | Microsoft.Storage/storageAccounts/queueServices/queues/delete | 傳回刪除佇列的結果 |
> |  | **storageAccounts/services/diagnosticSettings** |  |
> | 動作 | Microsoft.Storage/storageAccounts/services/diagnosticSettings/write | 建立/更新儲存體帳戶的診斷設定。 |
> |  | **storageAccounts/tableServices** |  |
> | 動作 | 微軟.存儲/存儲帳戶/表服務/讀取 | 取得表服務屬性 |
> |  | **usages** |  |
> | 動作 | Microsoft.Storage/usages/read | 傳回指定訂用帳戶資源的限制和目前的使用量計數 |
> |  | **儲存帳號/blob 服務/容器/blob** |  |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | 傳回 Blob 或 Blob 清單 |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | 傳回寫入 Blob 的結果 |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | 傳回刪除 Blob 的結果 |
> | DataAction | 微軟儲存或儲存的記憶體/儲存帳戶/blob服務/容器/blob/刪除 Blob 版本/操作 | 傳回刪除 Blob 版本的結果 |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action | 傳回新增 Blob 內容的結果 |
> | DataAction | 微軟.儲存/儲存帳戶/blob服務/容器/blob/過濾器/操作 | 傳回符合的標籤篩選器的帳號下的 blob 清單 |
> | DataAction | 微軟.儲存/儲存帳戶/blob服務/容器/blob/移動/操作 | 將 Blob 從路徑移至另一個路徑 |
> | DataAction | 微軟.儲存/儲存帳戶/blob服務/容器/blob/管理所有權/操作 | 變更 Blob 的擁有權 |
> | DataAction | 微軟.儲存/儲存帳戶/blob服務/容器/blob/修改許可權/操作 | 變更 Blob 的權限 |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/runAsSuperUser/action | 傳回 blob 命令的結果 |
> |  | **儲存帳號/blob 服務/容器/blob/標記** |  |
> | DataAction | 微軟.儲存/儲存帳戶/blob服務/容器/blob/標籤/讀取 | 回讀取 blob 標籤的結果 |
> | DataAction | 微軟.儲存/儲存帳戶/blob服務/容器/blob/標籤/寫入 | 傳回寫入 blob 標籤的結果 |
> |  | **儲存帳號/檔案服務/檔案分享/檔案** |  |
> | DataAction | 微軟儲存檔案/儲存帳戶/檔案服務/檔案分享/檔案/讀取 | 傳回檔案/資料夾或檔案/資料夾清單 |
> | DataAction | 微軟儲存檔案、儲存帳戶/檔案服務/檔案分享/檔案/寫入 | 傳回寫檔案或建立資料夾的結果 |
> | DataAction | 微軟儲存檔案、儲存帳戶/檔案服務/檔案分享/檔案/刪除 | 傳回刪除檔案/資料夾的結果 |
> | DataAction | 微軟儲存檔案/儲存帳戶/檔案服務/檔案分享/檔案/修改權限/操作 | 傳回修改檔案/資料夾權限的結果 |
> | DataAction | 微軟.儲存/儲存帳戶/檔案服務/檔案共用/檔案/行動超級使用者/操作 | 取得檔案管理員權限 |
> |  | **儲存帳戶/佇列服務/佇列/消息** |  |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | 傳回訊息 |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/write | 傳回寫入訊息的結果 |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete | 傳回刪除訊息的結果 |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action | 傳回新增訊息的結果 |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action | 傳回處理訊息的結果 |

## <a name="microsoftstoragesync"></a>微軟儲存同步

Azure 服務:[儲存](../storage/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | 微軟.儲存同步/註冊/操作 | 註冊儲存同步提供者的訂閱 |
> | 動作 | 微軟.儲存同步/取消註冊/操作 | 取消註冊儲存同步提供者的訂閱 |
> |  | **位置** |  |
> | 動作 | microsoft.storagesync/locations/checkNameAvailability/action | 確認儲存體同步服務名稱有效，且並非使用中。 |
> |  | **位置/工作流/操作** |  |
> | 動作 | microsoft.storagesync/locations/workflows/operations/read | 取得非同步作業的狀態 |
> |  | **操作** |  |
> | 動作 | 微軟.儲存同步/操作/讀取 | 取得受支援操作清單 |
> |  | **儲存同步服務** |  |
> | 動作 | microsoft.storagesync/storageSyncServices/read | 讀取任何儲存體同步服務 |
> | 動作 | microsoft.storagesync/storageSyncServices/write | 建立或更新任何儲存體同步服務 |
> | 動作 | microsoft.storagesync/storageSyncServices/delete | 刪除任何儲存體同步服務 |
> |  | **儲存同步服務/供應商/微軟.見解/指標定義** |  |
> | 動作 | microsoft.storagesync/storageSyncServices/providers/Microsoft.Insights/metricDefinitions/read | 取得儲存體同步服務的可用計量 |
> |  | **storageSyncServices/registeredServers** |  |
> | 動作 | microsoft.storagesync/storageSyncServices/registeredServers/read | 讀取任何已註冊的伺服器 |
> | 動作 | microsoft.storagesync/storageSyncServices/registeredServers/write | 建立或更新任何已註冊的伺服器 |
> | 動作 | microsoft.storagesync/storageSyncServices/registeredServers/delete | 刪除任何已註冊的伺服器 |
> |  | **儲存同步服務/註冊伺服器/供應商/微軟.見解/指標定義** |  |
> | 動作 | microsoft.storagesync/storageSyncServices/registeredServers/providers/Microsoft.Insights/metricDefinitions/read | 取得已註冊的伺服器可用的計量 |
> |  | **storageSyncServices/syncGroups** |  |
> | 動作 | microsoft.storagesync/storageSyncServices/syncGroups/read | 讀取任何同步群組 |
> | 動作 | microsoft.storagesync/storageSyncServices/syncGroups/write | 建立或更新任何同步群組 |
> | 動作 | microsoft.storagesync/storageSyncServices/syncGroups/delete | 刪除任何同步群組 |
> |  | **storageSyncServices/syncGroups/cloudEndpoints** |  |
> | 動作 | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/read | 讀取任何雲端端點 |
> | 動作 | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/write | 建立或更新任何雲端端點 |
> | 動作 | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/delete | 刪除任何雲端端點 |
> | 動作 | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/prebackup/action | 在備份之前呼叫此動作 |
> | 動作 | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/postbackup/action | 在備份之後呼叫此動作 |
> | 動作 | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/prerestore/action | 在還原之前呼叫此動作 |
> | 動作 | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/postrestore/action | 在還原之後呼叫此動作 |
> | 動作 | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/restoreheartbeat/action | 還原活動訊號 |
> | 動作 | 微軟.儲存同步/儲存同步服務/同步組/雲端點/觸發器更改檢測/操作 | 呼叫此操作可觸發雲終結點檔案分享上的變更檢測 |
> |  | **儲存同步服務/同步群組/雲端點/操作結果** |  |
> | 動作 | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/operationresults/read | 取得非同步備份/還原作業的狀態 |
> |  | **儲存同步服務/同步組/供應商/微軟.見解/指標定義** |  |
> | 動作 | microsoft.storagesync/storageSyncServices/syncGroups/providers/Microsoft.Insights/metricDefinitions/read | 取得同步群組的可用計量 |
> |  | **storageSyncServices/syncGroups/serverEndpoints** |  |
> | 動作 | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/read | 讀取任何伺服器端點 |
> | 動作 | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/write | 建立或更新任何伺服器端點 |
> | 動作 | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/delete | 刪除任何伺服器端點 |
> | 動作 | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/recallAction/action | 呼叫此動作以將檔案回復到伺服器 |
> |  | **儲存同步服務/同步組/伺服器終結點/供應商/微軟.見解/指標定義** |  |
> | 動作 | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/providers/Microsoft.Insights/metricDefinitions/read | 取得伺服器端點的可用計量 |
> |  | **storageSyncServices/workflows** |  |
> | 動作 | microsoft.storagesync/storageSyncServices/workflows/read | 讀取工作流程 |
> |  | **儲存同步服務/工作流/運營結果** |  |
> | 動作 | microsoft.storagesync/storageSyncServices/workflows/operationresults/read | 取得非同步作業的狀態 |
> |  | **儲存同步服務/工作流/操作** |  |
> | 動作 | microsoft.storagesync/storageSyncServices/workflows/operations/read | 取得非同步作業的狀態 |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

Azure 服務:[簡單](../storsimple/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.StorSimple/register/action | 註冊提供者 Microsoft.StorSimple |
> |  | **managers** |  |
> | 動作 | Microsoft.StorSimple/managers/clearAlerts/action | 清除與裝置管理員相關聯的所有警示。 |
> | 動作 | Microsoft.StorSimple/managers/getEncryptionKey/action | 取得裝置管理員的加密金鑰。 |
> | 動作 | Microsoft.StorSimple/managers/read | 列出或取得裝置管理員 |
> | 動作 | Microsoft.StorSimple/managers/delete | 刪除裝置管理員 |
> | 動作 | Microsoft.StorSimple/managers/write | 建立或更新裝置管理員 |
> | 動作 | Microsoft.StorSimple/managers/configureDevice/action | 設定裝置 |
> | 動作 | Microsoft.StorSimple/managers/migrateClassicToResourceManager/action | 從傳統移轉至 Resource Manager |
> | 動作 | Microsoft.StorSimple/managers/listActivationKey/action | 取得 StorSimple 裝置管理員的啟用金鑰。 |
> | 動作 | Microsoft.StorSimple/managers/regenerateActivationKey/action | 重新產生現有 StorSimple 裝置管理員的啟用金鑰。 |
> | 動作 | Microsoft.StorSimple/managers/listPublicEncryptionKey/action | 列出 StorSimple 裝置管理員的公用加密金鑰。 |
> | 動作 | Microsoft.StorSimple/managers/provisionCloudAppliance/action | 建立新的雲端應用裝置。 |
> | 動作 | Microsoft.StorSimple/Managers/write | 「建立保存庫」作業會建立 'vault' 類型的 Azure 資源 |
> | 動作 | Microsoft.StorSimple/Managers/read | 「取得保存庫」作業會取得物件，此物件代表 'vault' 類型的 Azure 資源 |
> | 動作 | Microsoft.StorSimple/Managers/delete | 「刪除保存庫」作業會刪除 'vault' 類型的指定 Azure 資源 |
> |  | **經理/存取控制記錄** |  |
> | 動作 | Microsoft.StorSimple/managers/accessControlRecords/read | 列出或取得存取控制記錄 |
> | 動作 | Microsoft.StorSimple/managers/accessControlRecords/write | 建立或更新存取控制記錄 |
> | 動作 | Microsoft.StorSimple/managers/accessControlRecords/delete | 刪除存取控制記錄 |
> |  | **經理/存取控制記錄/操作結果** |  |
> | 動作 | Microsoft.StorSimple/managers/accessControlRecords/operationResults/read | 列出或取得作業結果 |
> |  | **經理/警報** |  |
> | 動作 | Microsoft.StorSimple/managers/alerts/read | 列出或取得警示 |
> |  | **經理/備份** |  |
> | 動作 | Microsoft.StorSimple/managers/backups/read | 列出或取得備份組 |
> |  | **管理員/頻寬設定** |  |
> | 動作 | Microsoft.StorSimple/managers/bandwidthSettings/read | 列出頻寬設定 (僅限 8000 系列) |
> | 動作 | Microsoft.StorSimple/managers/bandwidthSettings/write | 新建或更新頻寬設定 (僅限 8000 系列) |
> | 動作 | Microsoft.StorSimple/managers/bandwidthSettings/delete | 刪除現有頻寬設定 (僅限 8000 系列) |
> |  | **經理/頻寬設定/操作結果** |  |
> | 動作 | Microsoft.StorSimple/managers/bandwidthSettings/operationResults/read | 列出作業結果 |
> |  | **經理/憑證** |  |
> | 動作 | Microsoft.StorSimple/managers/certificates/write | 建立或更新憑證 |
> | 動作 | Microsoft.StorSimple/Managers/certificates/write | 「更新資源憑證」作業會更新資源/保存庫的認證憑證。 |
> |  | **經理/雲裝置設定** |  |
> | 動作 | Microsoft.StorSimple/managers/cloudApplianceConfigurations/read | 列出雲端應用裝置所支援的組態 |
> |  | **經理/裝置** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/sendTestAlertEmail/action | 傳送測試警示電子郵件給所設定的電子郵件收件者。 |
> | 動作 | Microsoft.StorSimple/managers/devices/scanForUpdates/action | 掃描裝置的更新。 |
> | 動作 | Microsoft.StorSimple/managers/devices/download/action | 下載設備的更新。 |
> | 動作 | Microsoft.StorSimple/managers/devices/install/action | 在裝置上安裝更新。 |
> | 動作 | Microsoft.StorSimple/managers/devices/read | 列出或取得裝置 |
> | 動作 | Microsoft.StorSimple/managers/devices/write | 建立或更新裝置 |
> | 動作 | Microsoft.StorSimple/managers/devices/delete | 刪除裝置 |
> | 動作 | Microsoft.StorSimple/managers/devices/deactivate/action | 停用裝置。 |
> | 動作 | Microsoft.StorSimple/managers/devices/failover/action | 裝置的容錯移轉。 |
> | 動作 | Microsoft.StorSimple/managers/devices/publishSupportPackage/action | 發佈現有裝置的支援套件。 StorSimple 支援封裝是一種簡便的機制，可收集所有相關的記錄以協助 Microsoft 支援服務針對任何 StorSimple 裝置問題進行疑難排解。 |
> | 動作 | Microsoft.StorSimple/managers/devices/authorizeForServiceEncryptionKeyRollover/action | 授權裝置的服務加密金鑰變換 |
> | 動作 | Microsoft.StorSimple/managers/devices/installUpdates/action | 在裝置 (僅限 8000 系列) 上安裝更新。 |
> | 動作 | Microsoft.StorSimple/managers/devices/listFailoverSets/action | 列出現有裝置 (僅限 8000 系列) 的容錯移轉集。 |
> | 動作 | Microsoft.StorSimple/managers/devices/listFailoverTargets/action | 列出裝置 (僅限 8000 系列) 的容錯移轉目標。 |
> | 動作 | Microsoft.StorSimple/managers/devices/publicEncryptionKey/action | 列出裝置管理員的公用加密金鑰 |
> |  | **管理員/裝置/警示設定** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/alertSettings/read | 列出或取得警示設定 |
> | 動作 | Microsoft.StorSimple/managers/devices/alertSettings/write | 建立或更新警示設定 |
> |  | **經理/裝置/警示設定/操作結果** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/alertSettings/operationResults/read | 列出或取得作業結果 |
> |  | **經理/裝置/備份原則** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/backupPolicies/write | 新建或更新備份原則 (僅限 8000 系列) |
> | 動作 | Microsoft.StorSimple/managers/devices/backupPolicies/read | 列出備份原則 (僅限 8000 系列) |
> | 動作 | Microsoft.StorSimple/managers/devices/backupPolicies/delete | 刪除現有的備份原則 (僅限 8000 系列) |
> | 動作 | Microsoft.StorSimple/managers/devices/backupPolicies/backup/action | 製作手動備份，以針對原則所保護的所有磁碟區建立隨選備份。 |
> |  | **經理/裝置/備份策略/操作結果** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/backupPolicies/operationResults/read | 列出作業結果 |
> |  | **經理/裝置/備份策略/計劃** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/write | 新建或更新排程 |
> | 動作 | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/read | 列出排程 |
> | 動作 | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/delete | 刪除現有排程 |
> |  | **經理/裝置/備份策略/計畫/操作結果** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/operationResults/read | 列出作業結果 |
> |  | **經理/裝置/備份** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/backups/read | 列出或取得備份組 |
> | 動作 | Microsoft.StorSimple/managers/devices/backups/delete | 刪除備份組 |
> | 動作 | Microsoft.StorSimple/managers/devices/backups/restore/action | 從備份組還原所有磁碟區。 |
> |  | **經理/裝置/備份/元素** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/backups/elements/clone/action | 使用備份元素來複製共用或磁碟區。 |
> |  | **經理/裝置/備份/元素/操作結果** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/backups/elements/operationResults/read | 列出或取得作業結果 |
> |  | **經理/裝置/備份/操作結果** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/backups/operationResults/read | 列出或取得作業結果 |
> |  | **經理/裝置/備份計劃組** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/backupScheduleGroups/read | 列出或取得備份排程群組 |
> | 動作 | Microsoft.StorSimple/managers/devices/backupScheduleGroups/write | 建立或更新備份排程群組 |
> | 動作 | Microsoft.StorSimple/managers/devices/backupScheduleGroups/delete | 刪除備份排程群組 |
> |  | **經理/裝置/備份計畫組/操作結果** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/backupScheduleGroups/operationResults/read | 列出或取得作業結果 |
> |  | **經理/裝置/章設定** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/chapSettings/write | 建立或更新 Chap 設定 |
> | 動作 | Microsoft.StorSimple/managers/devices/chapSettings/read | 列出或取得 Chap 設定 |
> | 動作 | Microsoft.StorSimple/managers/devices/chapSettings/delete | 刪除 Chap 設定 |
> |  | **經理/裝置/章設定/操作結果** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/chapSettings/operationResults/read | 列出或取得作業結果 |
> |  | **經理/裝置/磁碟** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/disks/read | 列出或取得磁碟 |
> |  | **經理/裝置/故障轉移/操作結果** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/failover/operationResults/read | 列出或取得作業結果 |
> |  | **管理員/裝置/容錯移轉目標** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/failoverTargets/read | 列出或取得裝置的容錯移轉目標 |
> |  | **經理/裝置/檔案伺服器** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/fileservers/read | 列出或取得檔案伺服器 |
> | 動作 | Microsoft.StorSimple/managers/devices/fileservers/write | 建立或更新檔案伺服器 |
> | 動作 | Microsoft.StorSimple/managers/devices/fileservers/delete | 刪除檔案伺服器 |
> | 動作 | Microsoft.StorSimple/managers/devices/fileservers/backup/action | 製作檔案伺服器的備份。 |
> |  | **經理/裝置/檔案伺服器/指標** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/fileservers/metrics/read | 列出或取得計量 |
> |  | **管理員/裝置/檔案伺服器/指標定義** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/fileservers/metricsDefinitions/read | 列出或取得計量定義 |
> |  | **管理員/裝置/檔案伺服器/操作結果** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/fileservers/operationResults/read | 列出或取得作業結果 |
> |  | **經理/裝置/檔案伺服器/共用** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/fileservers/shares/write | 建立或更新共用 |
> | 動作 | Microsoft.StorSimple/managers/devices/fileservers/shares/read | 列出或取得共用 |
> | 動作 | Microsoft.StorSimple/managers/devices/fileservers/shares/delete | 刪除共用 |
> |  | **經理/裝置/檔案伺服器/共用/指標** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/fileservers/shares/metrics/read | 列出或取得計量 |
> |  | **經理/裝置/檔案伺服器/共用/指標定義** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/fileservers/shares/metricsDefinitions/read | 列出或取得計量定義 |
> |  | **經理/裝置/檔案伺服器/共用/操作結果** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/fileservers/shares/operationResults/read | 列出或取得作業結果 |
> |  | **經理/裝置/硬體元件元件** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/read | 列出硬體元件群組 |
> | 動作 | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/changeControllerPowerState/action | 變更硬體元件群組的控制器電源狀態 |
> |  | **經理/裝置/硬體元件元件元件/操作結果** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/operationResults/read | 列出作業結果 |
> |  | **經理/裝置/伺服器** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/iscsiservers/read | 列出或取得 iSCSI 伺服器 |
> | 動作 | Microsoft.StorSimple/managers/devices/iscsiservers/write | 建立或更新 iSCSI 伺服器 |
> | 動作 | Microsoft.StorSimple/managers/devices/iscsiservers/delete | 刪除 iSCSI 伺服器 |
> | 動作 | Microsoft.StorSimple/managers/devices/iscsiservers/backup/action | 製作 iSCSI 伺服器的備份。 |
> |  | **經理/裝置/伺服器/磁碟** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/iscsiservers/disks/read | 列出或取得磁碟 |
> | 動作 | Microsoft.StorSimple/managers/devices/iscsiservers/disks/write | 建立或更新磁碟 |
> | 動作 | Microsoft.StorSimple/managers/devices/iscsiservers/disks/delete | 刪除磁碟 |
> |  | **經理/裝置/伺服器/磁碟/指標** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/iscsiservers/disks/metrics/read | 列出或取得計量 |
> |  | **經理/裝置/伺服器/磁碟/指標定義** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/iscsiservers/disks/metricsDefinitions/read | 列出或取得計量定義 |
> |  | **經理/裝置/伺服器/磁碟/操作結果** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/iscsiservers/disks/operationResults/read | 列出或取得作業結果 |
> |  | **經理/裝置/伺服器/指標** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/iscsiservers/metrics/read | 列出或取得計量 |
> |  | **經理/裝置/伺服器/指標定義** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/iscsiservers/metricsDefinitions/read | 列出或取得計量定義 |
> |  | **經理/裝置/伺服器/操作結果** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/iscsiservers/operationResults/read | 列出或取得作業結果 |
> |  | **經理/裝置/工作** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/jobs/read | 列出或取得作業 |
> | 動作 | Microsoft.StorSimple/managers/devices/jobs/cancel/action | 取消正在執行的作業 |
> |  | **經理/裝置/作業/操作結果** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/jobs/operationResults/read | 列出作業結果 |
> |  | **經理/裝置/指標** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/metrics/read | 列出或取得計量 |
> |  | **經理/裝置/指標定義** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/metricsDefinitions/read | 列出或取得計量定義 |
> |  | **經理/裝置/移轉來源配置** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/import/action | 匯入用於移轉的來源組態 |
> | 動作 | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/startMigrationEstimate/action | 啟動作業來預估移轉程序的持續時間。 |
> | 動作 | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/startMigration/action | 使用來源組態來開始移轉 |
> | 動作 | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/confirmMigration/action | 確認移轉成功，並加以認可。 |
> | 動作 | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchMigrationEstimate/action | 提取移轉估計作業的狀態。 |
> | 動作 | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchMigrationStatus/action | 提取移轉狀態。 |
> | 動作 | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchConfirmMigrationStatus/action | 提取移轉的確認狀態。 |
> |  | **經理/裝置/遷移來源配置/確認遷移狀態** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/confirmMigrationStatus/read | 列出確認移轉狀態 |
> |  | **經理/裝置/遷移來源配置/移轉估計** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/migrationEstimate/read | 列出移轉估計 |
> |  | **經理/裝置/遷移來源配置/遷移狀態** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/migrationStatus/read | 列出移轉狀態 |
> |  | **經理/裝置/遷移來源配置/操作結果** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/operationResults/read | 列出作業結果 |
> |  | **經理/裝置/網路設定** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/networkSettings/read | 列出或取得網路設定 |
> | 動作 | Microsoft.StorSimple/managers/devices/networkSettings/write | 新建或更新網路設定 |
> |  | **經理/裝置/網路設定/操作結果** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/networkSettings/operationResults/read | 列出作業結果 |
> |  | **經理/裝置/操作結果** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/operationResults/read | 列出或取得作業結果 |
> |  | **經理/裝置/安全設定** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/securitySettings/update/action | 更新安全性設定。 |
> | 動作 | Microsoft.StorSimple/managers/devices/securitySettings/read | 列出安全性設定 |
> | 動作 | Microsoft.StorSimple/managers/devices/securitySettings/syncRemoteManagementCertificate/action | 同步處理裝置的遠端管理憑證。 |
> | 動作 | Microsoft.StorSimple/managers/devices/securitySettings/write | 新建或更新安全性設定 |
> |  | **經理/裝置/安全設定/操作結果** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/securitySettings/operationResults/read | 列出或取得作業結果 |
> |  | **經理/裝置/共用** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/shares/read | 列出或取得共用 |
> |  | **經理/裝置/時間設定** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/timeSettings/read | 列出或取得時間設定 |
> | 動作 | Microsoft.StorSimple/managers/devices/timeSettings/write | 新建或更新時間設定 |
> |  | **經理/裝置/時間設定/操作結果** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/timeSettings/operationResults/read | 列出作業結果 |
> |  | **經理/裝置/更新/操作結果** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/updates/operationResults/read | 列出或取得作業結果 |
> |  | **經理/裝置/更新摘要** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/updateSummary/read | 列出或取得更新摘要 |
> |  | **經理/裝置/卷容器** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/volumeContainers/write | 新建或更新磁碟區容器 (僅限 8000 系列) |
> | 動作 | Microsoft.StorSimple/managers/devices/volumeContainers/read | 列出磁碟區容器 (僅限 8000 系列) |
> | 動作 | Microsoft.StorSimple/managers/devices/volumeContainers/delete | 刪除現有的磁碟區容器 (僅限 8000 系列) |
> |  | **經理/裝置/大量容器/指標** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/volumeContainers/metrics/read | 列出計量 |
> |  | **經理/裝置/大量容器/指標定義** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/volumeContainers/metricsDefinitions/read | 列出計量定義 |
> |  | **經理/裝置/大量容器/操作結果** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/volumeContainers/operationResults/read | 列出作業結果 |
> |  | **經理/裝置/卷容器/卷** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/read | 列出磁碟區 |
> | 動作 | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/write | 新建或更新磁碟區 |
> | 動作 | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/delete | 刪除現有磁碟區 |
> |  | **經理/裝置/大量容器/卷/指標** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/metrics/read | 列出計量 |
> |  | **經理/裝置/卷容器/卷/指標定義** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/metricsDefinitions/read | 列出計量定義 |
> |  | **經理/裝置/大量容器/捲/操作結果** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/operationResults/read | 列出作業結果 |
> |  | **經理/裝置/卷** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/volumes/read | 列出磁碟區 |
> |  | **管理員/加密設定** |  |
> | 動作 | Microsoft.StorSimple/managers/encryptionSettings/read | 列出或取得加密設定 |
> |  | **經理/延伸資訊** |  |
> | 動作 | Microsoft.StorSimple/managers/extendedInformation/read | 列出或取得延伸的保存庫資訊 |
> | 動作 | Microsoft.StorSimple/managers/extendedInformation/write | 建立或更新延伸的保存庫資訊 |
> | 動作 | Microsoft.StorSimple/managers/extendedInformation/delete | 刪除擴充的保存庫資訊 |
> | 動作 | Microsoft.StorSimple/Managers/extendedInformation/read | 「取得延伸資訊」作業會取得物件的延伸資訊，此延伸資訊代表 'vault' 類型的 Azure 資源 |
> | 動作 | Microsoft.StorSimple/Managers/extendedInformation/write | 「取得延伸資訊」作業會取得物件的延伸資訊，此延伸資訊代表 'vault' 類型的 Azure 資源 |
> | 動作 | Microsoft.StorSimple/Managers/extendedInformation/delete | 「取得延伸資訊」作業會取得物件的延伸資訊，此延伸資訊代表 'vault' 類型的 Azure 資源 |
> |  | **經理/功能** |  |
> | 動作 | Microsoft.StorSimple/managers/features/read | 列出功能 |
> |  | **經理/檔案伺服器** |  |
> | 動作 | Microsoft.StorSimple/managers/fileservers/read | 列出或取得檔案伺服器 |
> |  | **經理/伺服器** |  |
> | 動作 | Microsoft.StorSimple/managers/iscsiservers/read | 列出或取得 iSCSI 伺服器 |
> |  | **經理/工作** |  |
> | 動作 | Microsoft.StorSimple/managers/jobs/read | 列出或取得作業 |
> |  | **經理/指標** |  |
> | 動作 | Microsoft.StorSimple/managers/metrics/read | 列出或取得計量 |
> |  | **經理/指標定義** |  |
> | 動作 | Microsoft.StorSimple/managers/metricsDefinitions/read | 列出或取得計量定義 |
> |  | **經理/移轉來源設定** |  |
> | 動作 | Microsoft.StorSimple/managers/migrationSourceConfigurations/read | 列出移轉來源設定 (僅限 8000 系列) |
> |  | **經理/運營結果** |  |
> | 動作 | Microsoft.StorSimple/managers/operationResults/read | 列出或取得作業結果 |
> |  | **經理/存儲帳戶認證** |  |
> | 動作 | Microsoft.StorSimple/managers/storageAccountCredentials/write | 建立或更新儲存體帳戶認證 |
> | 動作 | Microsoft.StorSimple/managers/storageAccountCredentials/read | 列出或取得儲存體帳戶認證 |
> | 動作 | Microsoft.StorSimple/managers/storageAccountCredentials/delete | 刪除儲存體帳戶認證 |
> |  | **經理/存儲帳戶認證/操作結果** |  |
> | 動作 | Microsoft.StorSimple/managers/storageAccountCredentials/operationResults/read | 列出或取得作業結果 |
> |  | **經理/儲存網域** |  |
> | 動作 | Microsoft.StorSimple/managers/storageDomains/read | 列出或取得儲存體網域 |
> | 動作 | Microsoft.StorSimple/managers/storageDomains/write | 建立或更新儲存體網域 |
> | 動作 | Microsoft.StorSimple/managers/storageDomains/delete | 刪除儲存體網域 |
> |  | **經理/儲存網域/操作結果** |  |
> | 動作 | Microsoft.StorSimple/managers/storageDomains/operationResults/read | 列出或取得作業結果 |
> |  | **操作** |  |
> | 動作 | Microsoft.StorSimple/operations/read | 列出或取得作業 |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

Azure 服務:[串流分析](../stream-analytics/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.StreamAnalytics/Register/action | 向串流分析資源提供者註冊訂用帳戶 |
> |  | **位置/配額** |  |
> | 動作 | Microsoft.StreamAnalytics/locations/quotas/Read | 讀取串流分析訂用帳戶配額 |
> |  | **操作** |  |
> | 動作 | Microsoft.StreamAnalytics/operations/Read | 讀取串流分析作業 |
> |  | **streamingjobs** |  |
> | 動作 | Microsoft.StreamAnalytics/streamingjobs/Delete | 刪除串流分析作業 |
> | 動作 | 微軟.流分析/流工作/發佈邊緣包/操作 | 發佈流分析工作的邊緣套件 |
> | 動作 | Microsoft.StreamAnalytics/streamingjobs/Read | 讀取串流分析作業 |
> | 動作 | 微軟.流分析/流式處理/縮放/操作 | 調整流的分析工作 |
> | 動作 | Microsoft.StreamAnalytics/streamingjobs/Start/action | 啟動串流分析作業 |
> | 動作 | Microsoft.StreamAnalytics/streamingjobs/Stop/action | 停止串流分析作業 |
> | 動作 | Microsoft.StreamAnalytics/streamingjobs/Write | 寫入串流分析作業 |
> |  | **流程式處理工作/功能** |  |
> | 動作 | Microsoft.StreamAnalytics/streamingjobs/functions/Delete | 刪除串流分析作業函式 |
> | 動作 | Microsoft.StreamAnalytics/streamingjobs/functions/Read | 讀取串流分析作業函式 |
> | 動作 | Microsoft.StreamAnalytics/streamingjobs/functions/RetrieveDefaultDefinition/action | 擷取串流分析作業函式的預設定義 |
> | 動作 | Microsoft.StreamAnalytics/streamingjobs/functions/Test/action | 測試串流分析作業函式 |
> | 動作 | Microsoft.StreamAnalytics/streamingjobs/functions/Write | 寫入串流分析作業函式 |
> |  | **流式處理工作/函數/操作結果** |  |
> | 動作 | Microsoft.StreamAnalytics/streamingjobs/functions/operationresults/Read | 讀取串流分析作業函式的作業結果 |
> |  | **流程式處理工作/輸入** |  |
> | 動作 | Microsoft.StreamAnalytics/streamingjobs/inputs/Delete | 刪除串流分析作業輸入 |
> | 動作 | Microsoft.StreamAnalytics/streamingjobs/inputs/Read | 讀取串流分析作業輸入 |
> | 動作 | Microsoft.StreamAnalytics/streamingjobs/inputs/Sample/action | 進行串流分析作業輸入的取樣 |
> | 動作 | Microsoft.StreamAnalytics/streamingjobs/inputs/Test/action | 測試串流分析作業輸入 |
> | 動作 | Microsoft.StreamAnalytics/streamingjobs/inputs/Write | 寫入串流分析作業輸入 |
> |  | **流式處理工作/輸入/操作結果** |  |
> | 動作 | Microsoft.StreamAnalytics/streamingjobs/inputs/operationresults/Read | 讀取串流分析作業輸入的作業結果 |
> |  | **流式處理工作/指標定義** |  |
> | 動作 | Microsoft.StreamAnalytics/streamingjobs/metricdefinitions/Read | 讀取計量定義 |
> |  | **流式處理工作/操作結果** |  |
> | 動作 | Microsoft.StreamAnalytics/streamingjobs/operationresults/Read | 讀取串流分析作業的作業結果 |
> |  | **流式處理工作/輸出** |  |
> | 動作 | Microsoft.StreamAnalytics/streamingjobs/outputs/Delete | 刪除串流分析作業輸出 |
> | 動作 | Microsoft.StreamAnalytics/streamingjobs/outputs/Read | 讀取串流分析作業輸出 |
> | 動作 | Microsoft.StreamAnalytics/streamingjobs/outputs/Test/action | 測試串流分析作業輸出 |
> | 動作 | Microsoft.StreamAnalytics/streamingjobs/outputs/Write | 寫入串流分析作業輸出 |
> |  | **流式處理工作/輸出/操作結果** |  |
> | 動作 | Microsoft.StreamAnalytics/streamingjobs/outputs/operationresults/Read | 讀取串流分析作業輸出的作業結果 |
> |  | **流式處理/供應商/微軟.見解/診斷設置** |  |
> | 動作 | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/read | 讀取診斷設定。 |
> | 動作 | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/write | 寫入診斷設定。 |
> |  | **串流式處理/提供者/微軟.見解/日誌定義** |  |
> | 動作 | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/logDefinitions/read | 取得 streamingjobs 的可用記錄 |
> |  | **流式處理/供應商/微軟.見解/指標定義** |  |
> | 動作 | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/metricDefinitions/read | 取得 streamingjobs 的可用計量 |
> |  | **流式處理/轉換** |  |
> | 動作 | Microsoft.StreamAnalytics/streamingjobs/transformations/Delete | 刪除串流分析作業轉換 |
> | 動作 | Microsoft.StreamAnalytics/streamingjobs/transformations/Read | 讀取串流分析作業轉換 |
> | 動作 | Microsoft.StreamAnalytics/streamingjobs/transformations/Write | 寫入串流分析作業轉換 |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

Azure 服務:核心

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.Subscription/CreateSubscription/action | 建立 Azure 訂用帳戶 |
> | 動作 | Microsoft.Subscription/register/action | 向 Microsoft.Subscription 資源提供者註冊訂用帳戶 |
> | 動作 | 微軟.訂閱/取消/操作 | 取消訂閱 |
> | 動作 | 微軟.訂閱/重新命名/操作 | 重新命名訂閱 |
> |  | **SubscriptionDefinitions** |  |
> | 動作 | Microsoft.Subscription/SubscriptionDefinitions/read | 取得管理群組中的 Azure 訂用帳戶定義。 |
> | 動作 | Microsoft.Subscription/SubscriptionDefinitions/write | 建立 Azure 訂用帳戶定義 |

## <a name="microsoftsupport"></a>Microsoft.Support

Azure 服務:核心

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.Support/register/action | 註冊支援資源提供者 |
> | 動作 | 微軟.支援/檢查名稱可用性/操作 | 檢查此名稱有效或未用於資源型態 |
> |  | **動作結果** |  |
> | 動作 | 微軟.支援/操作結果/閱讀 | 取得作業結果 |
> |  | **操作** |  |
> | 動作 | 微軟支援/操作/讀取 | 列出 Microsoft. 支援資源提供者上可用的作業 |
> |  | **操作狀態** |  |
> | 動作 | 微軟.支援/操作狀態/讀取 | 取得作業狀態 |
> |  | **服務** |  |
> | 動作 | 微軟.支援/服務/閱讀 | 取得可支援的 Azure 服務清單 |
> |  | **服務/問題分類** |  |
> | 動作 | 微軟.支援/服務/問題分類/閱讀 | 取得適用於 Azure 服務的問題類別清單 |
> |  | **支援門票** |  |
> | 動作 | Microsoft.Support/supportTickets/read | 獲取支援票證清單。 |
> | 動作 | Microsoft.Support/supportTickets/write | 非同步創建支援票證或更新它。 您可以建立技術、帳單、配額或訂用帳戶管理相關問題的支援票證。 您可以更新現有支援票證的嚴重性和聯繫人詳細資訊。 |
> |  | **支援門票/通訊** |  |
> | 動作 | 微軟.支援/支持門票/通信/閱讀 | 取得支援票證通訊清單 |
> | 動作 | 微軟.支援/支援門票/通信/寫入 | 建立支援票證通訊 |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

Azure 服務:[時間序列的見解](../time-series-insights/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.TimeSeriesInsights/register/action | 為時間序列深入解析資源提供者註冊訂用帳戶，並讓您能夠建立時間序列深入解析環境。 |
> |  | **環境** |  |
> | 動作 | Microsoft.TimeSeriesInsights/environments/read | 取得環境的屬性。 |
> | 動作 | Microsoft.TimeSeriesInsights/environments/write | 建立新的環境，或更新現有的環境。 |
> | 動作 | Microsoft.TimeSeriesInsights/environments/delete | 刪除環境。 |
> |  | **環境/存取原則** |  |
> | 動作 | Microsoft.TimeSeriesInsights/environments/accesspolicies/read | 取得存取原則的屬性。 |
> | 動作 | Microsoft.TimeSeriesInsights/environments/accesspolicies/write | 針對環境建立新的存取原則，或更新現有的存取原則。 |
> | 動作 | Microsoft.TimeSeriesInsights/environments/accesspolicies/delete | 刪除存取原則。 |
> |  | **environments/eventsources** |  |
> | 動作 | Microsoft.TimeSeriesInsights/environments/eventsources/read | 取得事件來源的屬性。 |
> | 動作 | Microsoft.TimeSeriesInsights/environments/eventsources/write | 針對環境建立新的事件來源，或更新現有的事件來源。 |
> | 動作 | Microsoft.TimeSeriesInsights/environments/eventsources/delete | 刪除事件來源。 |
> |  | **environments/referencedatasets** |  |
> | 動作 | Microsoft.TimeSeriesInsights/environments/referencedatasets/read | 取得參考資料集的屬性。 |
> | 動作 | Microsoft.TimeSeriesInsights/environments/referencedatasets/write | 針對環境建立新的參考資料集，或更新現有的參考資料集。 |
> | 動作 | Microsoft.TimeSeriesInsights/environments/referencedatasets/delete | 刪除參考資料集。 |
> |  | **環境/狀態** |  |
> | 動作 | Microsoft.TimeSeriesInsights/environments/status/read | 取得環境的狀態，其相關聯作業 (例如輸入) 的狀態。 |

## <a name="microsoftvisualstudio"></a>Microsoft.VisualStudio

Azure 服務[:Azure DevOps](https://docs.microsoft.com/azure/devops/)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.VisualStudio/Register/Action | 向 Microsoft.VisualStudio 提供者註冊 Azure 訂用帳戶 |
> |  | **帳戶** |  |
> | 動作 | Microsoft.VisualStudio/Account/Write | 設定帳戶 |
> | 動作 | Microsoft.VisualStudio/Account/Delete | 刪除帳戶 |
> | 動作 | Microsoft.VisualStudio/Account/Read | 讀取帳戶 |
> |  | **帳號/擴展** |  |
> | 動作 | Microsoft.VisualStudio/Account/Extension/Read | 讀取帳戶/擴充功能 |
> |  | **帳號/專案** |  |
> | 動作 | Microsoft.VisualStudio/Account/Project/Read | 讀取帳戶/專案 |
> | 動作 | Microsoft.VisualStudio/Account/Project/Write | 讀取帳戶/專案 |
> |  | **延伸** |  |
> | 動作 | Microsoft.VisualStudio/Extension/Write | 設定擴充功能 |
> | 動作 | Microsoft.VisualStudio/Extension/Delete | 刪除擴充功能 |
> | 動作 | Microsoft.VisualStudio/Extension/Read | 讀取擴充功能 |
> |  | **專案** |  |
> | 動作 | Microsoft.VisualStudio/Project/Write | 設定專案 |
> | 動作 | Microsoft.VisualStudio/Project/Delete | 刪除專案 |
> | 動作 | Microsoft.VisualStudio/Project/Read | 讀取專案 |

## <a name="microsoftweb"></a>microsoft.web

Azure 服務:[應用服務](../app-service/index.yml),[功能](../azure-functions/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | microsoft.web/unregister/action | 針對訂用帳戶取消註冊 Microsoft.Web 資源提供者。 |
> | 動作 | microsoft.web/validate/action | 驗證。 |
> | 動作 | microsoft.web/register/action | 針對訂用帳戶註冊 Microsoft.Web 資源提供者。 |
> | 動作 | microsoft.web/verifyhostingenvironmentvnet/action | 確認裝載環境 Vnet。 |
> |  | **api管理帳戶/apiacls** |  |
> | 動作 | microsoft.web/apimanagementaccounts/apiacls/read | 取得 API 管理帳戶 Apiacl。 |
> |  | **api管理帳戶/api** |  |
> | 動作 | microsoft.web/apimanagementaccounts/apis/read | 取得 API 管理帳戶 API。 |
> | 動作 | microsoft.web/apimanagementaccounts/apis/delete | 刪除 API 管理帳戶 API。 |
> | 動作 | microsoft.web/apimanagementaccounts/apis/write | 更新 API 管理帳戶 API。 |
> |  | **api管理帳戶/apis/apiacls** |  |
> | 動作 | microsoft.web/apimanagementaccounts/apis/apiacls/delete | 刪除 API 管理帳戶 API Apiacl。 |
> | 動作 | microsoft.web/apimanagementaccounts/apis/apiacls/read | 讀取 API 管理帳戶 API Apiacl。 |
> | 動作 | microsoft.web/apimanagementaccounts/apis/apiacls/write | 更新 API 管理帳戶 API Apiacl。 |
> |  | **api 管理帳戶/api/連接acl** |  |
> | 動作 | microsoft.web/apimanagementaccounts/apis/connectionacls/read | 取得 API 管理帳戶 API Connectionacl。 |
> |  | **api管理帳戶/api/連接** |  |
> | 動作 | microsoft.web/apimanagementaccounts/apis/connections/read | 取得 API 管理帳戶 API 連線。 |
> | 動作 | microsoft.web/apimanagementaccounts/apis/connections/confirmconsentcode/action | 確認同意碼 API 管理帳戶 API 連線。 |
> | 動作 | microsoft.web/apimanagementaccounts/apis/connections/delete | 刪除 API 管理帳戶 API 連線。 |
> | 動作 | microsoft.web/apimanagementaccounts/apis/connections/getconsentlinks/action | 取得 API 管理帳戶 API 連線的同意連結。 |
> | 動作 | microsoft.web/apimanagementaccounts/apis/connections/write | 更新 API 管理帳戶 API 連線。 |
> | 動作 | microsoft.web/apimanagementaccounts/apis/connections/listconnectionkeys/action | 列出 API 管理帳戶 API 連線的連線金鑰。 |
> | 動作 | microsoft.web/apimanagementaccounts/apis/connections/listsecrets/action | 列出 API 管理帳戶 API 連線的祕密。 |
> |  | **api管理帳戶/api/連接/連接** |  |
> | 動作 | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/delete | 刪除 API 管理帳戶 API 連線 Connectionacl。 |
> | 動作 | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/read | 取得 API 管理帳戶 API 連線 Connectionacl。 |
> | 動作 | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/write | 更新 API 管理帳戶 API 連線 Connectionacl。 |
> |  | **api 管理帳戶/api/當地語系化定義** |  |
> | 動作 | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/delete | 刪除 API 管理帳戶 API 已當地語系化的定義。 |
> | 動作 | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/read | 取得 API 管理帳戶 API 已當地語系化的定義。 |
> | 動作 | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/write | 更新 API 管理帳戶 API 已當地語系化的定義。 |
> |  | **api 管理帳戶/連線 acl** |  |
> | 動作 | microsoft.web/apimanagementaccounts/connectionacls/read | 取得 API 管理帳戶 Connectionacl。 |
> |  | **可用堆疊** |  |
> | 動作 | microsoft.web/availablestacks/read | 取得可用堆疊。 |
> |  | **憑證** |  |
> | 動作 | Microsoft.Web/certificates/Read | 取得憑證清單。 |
> | 動作 | Microsoft.Web/certificates/Write | 新增憑證或更新現有憑證。 |
> | 動作 | Microsoft.Web/certificates/Delete | 刪除現有憑證。 |
> |  | **checknameavailability** |  |
> | 動作 | microsoft.web/checknameavailability/read | 檢查資源名稱是否可供使用。 |
> |  | **classicmobileservices** |  |
> | 動作 | microsoft.web/classicmobileservices/read | 取得傳統的行動服務。 |
> |  | **連接閘道** |  |
> | 動作 | Microsoft.Web/connectionGateways/Read | 取得連線閘道器的清單。 |
> | 動作 | Microsoft.Web/connectionGateways/Write | 建立或更新連線閘道器。 |
> | 動作 | Microsoft.Web/connectionGateways/Delete | 刪除連接閘道器。 |
> | 動作 | Microsoft.Web/connectionGateways/Move/Action | 移動連線閘道。 |
> | 動作 | Microsoft.Web/connectionGateways/Join/Action | 加入連線閘道器。 |
> | 動作 | Microsoft.Web/connectionGateways/ListStatus/Action | 列出連線閘道的狀態。 |
> |  | **連線** |  |
> | 動作 | Microsoft.Web/connections/Read | 取得連線清單。 |
> | 動作 | Microsoft.Web/connections/Write | 建立或更新連線。 |
> | 動作 | Microsoft.Web/connections/Delete | 刪除連線。 |
> | 動作 | Microsoft.Web/connections/Move/Action | 移動連線。 |
> | 動作 | Microsoft.Web/connections/Join/Action | 加入連線。 |
> | 動作 | microsoft.web/connections/confirmconsentcode/action | 確認連線同意程式碼。 |
> | 動作 | microsoft.web/connections/listconsentlinks/action | 列出連線的同意連結。 |
> |  | **自訂 Apis** |  |
> | 動作 | Microsoft.Web/customApis/Read | 取得自訂 API 的清單。 |
> | 動作 | Microsoft.Web/customApis/Write | 建立或更新自訂 API。 |
> | 動作 | Microsoft.Web/customApis/Delete | 刪除自訂 API。 |
> | 動作 | Microsoft.Web/customApis/Move/Action | 移動自訂 API。 |
> | 動作 | Microsoft.Web/customApis/Join/Action | 加入自訂 API。 |
> | 動作 | Microsoft.Web/customApis/extractApiDefinitionFromWsdl/Action | 從 WSDL 中擷取 API 定義。 |
> | 動作 | Microsoft.Web/customApis/listWsdlInterfaces/Action | 列出自訂 API 的 WSDL 介面。 |
> |  | **deletedSites** |  |
> | 動作 | Microsoft.Web/deletedSites/Read | 取得所刪除 Web 應用程式的屬性 |
> |  | **部署位置** |  |
> | 動作 | microsoft.web/deploymentlocations/read | 取得部署位置。 |
> |  | **地理區域** |  |
> | 動作 | Microsoft.Web/geoRegions/Read | 取得地理區域清單。 |
> |  | **hostingEnvironments** |  |
> | 動作 | Microsoft.Web/hostingEnvironments/Read | 取得 App Service 環境的屬性 |
> | 動作 | Microsoft.Web/hostingEnvironments/Write | 建立新的 App Service 環境，或更新現有的 App Service 環境 |
> | 動作 | Microsoft.Web/hostingEnvironments/Delete | 刪除 App Service 環境 |
> | 動作 | 微軟.Web/託管環境/加入/操作 | 加入應用服務環境 |
> | 動作 | Microsoft.Web/hostingEnvironments/reboot/Action | 將 App Service 環境中的所有機器重新開機 |
> | 動作 | 微軟.Web/託管環境/私有端點連接審批/操作 | 批准專用終結點連線 |
> | 動作 | microsoft.web/hostingenvironments/resume/action | 繼續裝載環境。 |
> | 動作 | microsoft.web/hostingenvironments/suspend/action | 暫止裝載環境。 |
> |  | **託管環境/容量** |  |
> | 動作 | microsoft.web/hostingenvironments/capacities/read | 取得裝載環境容量。 |
> |  | **託管環境/探測器** |  |
> | 動作 | microsoft.web/hostingenvironments/detectors/read | 取得裝載環境偵測器。 |
> |  | **託管環境/診斷** |  |
> | 動作 | microsoft.web/hostingenvironments/diagnostics/read | 取得裝載環境診斷。 |
> |  | **託管環境/事件網格篩選器** |  |
> | 動作 | 微軟.Web/託管環境/事件網格篩選/刪除 | 移除託管環境中的事件網格篩選器。 |
> | 動作 | 微軟.Web/託管環境/事件網格過濾器/讀取 | 取得託管環境中的事件網格篩選器。 |
> | 動作 | 微軟.Web/託管環境/事件網格篩選/寫入 | 將事件網格篩選器放在託管環境中。 |
> |  | **託管環境/入站網路依賴點** |  |
> | 動作 | microsoft.web/hostingenvironments/inboundnetworkdependenciesendpoints/read | 取得所有輸入相依性的網路端點。 |
> |  | **託管環境/指標定義** |  |
> | 動作 | microsoft.web/hostingenvironments/metricdefinitions/read | 取得裝載環境的計量定義。 |
> |  | **hostingEnvironments/multiRolePools** |  |
> | 動作 | Microsoft.Web/hostingEnvironments/multiRolePools/Read | 取得 App Service 環境中前端集區的屬性 |
> | 動作 | Microsoft.Web/hostingEnvironments/multiRolePools/Write | 在 App Service 環境中建立新的前端集區，或更新 App Service 環境中現有的前端集區 |
> |  | **託管環境/多角色池/指標定義** |  |
> | 動作 | microsoft.web/hostingenvironments/multirolepools/metricdefinitions/read | 取得裝載環境 MultiRole 集區計量定義。 |
> |  | **託管環境/多角色池/指標** |  |
> | 動作 | microsoft.web/hostingenvironments/multirolepools/metrics/read | 取得裝載環境 MultiRole 集區計量。 |
> |  | **託管環境/多角色池/sus** |  |
> | 動作 | microsoft.web/hostingenvironments/multirolepools/skus/read | 取得裝載環境 MultiRole 集區 SKU。 |
> |  | **託管環境/多角色池/使用方式** |  |
> | 動作 | microsoft.web/hostingenvironments/multirolepools/usages/read | 取得裝載環境 MultiRole 集區使用量。 |
> |  | **託管環境、操作** |  |
> | 動作 | microsoft.web/hostingenvironments/operations/read | 取得裝載環境作業。 |
> |  | **託管環境/出站網路依賴點** |  |
> | 動作 | microsoft.web/hostingenvironments/outboundnetworkdependenciesendpoints/read | 取得所有輸出相依性的網路端點。 |
> |  | **託管環境/伺服器場** |  |
> | 動作 | microsoft.web/hostingenvironments/serverfarms/read | 取得裝載環境 App Service 方案。 |
> |  | **託管環境/站台** |  |
> | 動作 | microsoft.web/hostingenvironments/sites/read | 取得裝載環境 Web Apps。 |
> |  | **託管環境/使用方式** |  |
> | 動作 | microsoft.web/hostingenvironments/usages/read | 取得裝載環境使用量。 |
> |  | **hostingEnvironments/workerPools** |  |
> | 動作 | Microsoft.Web/hostingEnvironments/workerPools/Read | 取得 App Service 環境中背景工作集區的屬性 |
> | 動作 | Microsoft.Web/hostingEnvironments/workerPools/Write | 在 App Service 環境中建立新的背景工作集區，或更新 App Service 環境中現有的背景工作集區 |
> |  | **託管環境/工作池/指標定義** |  |
> | 動作 | microsoft.web/hostingenvironments/workerpools/metricdefinitions/read | 取得裝載環境 Workerpools 計量定義。 |
> |  | **託管環境/工作池/指標** |  |
> | 動作 | microsoft.web/hostingenvironments/workerpools/metrics/read | 取得裝載環境 Workerpools 計量。 |
> |  | **託管環境/工人池/sus** |  |
> | 動作 | microsoft.web/hostingenvironments/workerpools/skus/read | 取得裝載環境 Workerpools SKU。 |
> |  | **託管環境/工作池/使用方式** |  |
> | 動作 | microsoft.web/hostingenvironments/workerpools/usages/read | 取得裝載環境 Workerpools 使用量。 |
> |  | **託管環境名稱可用** |  |
> | 動作 | microsoft.web/ishostingenvironmentnameavailable/read | 取得裝載環境名稱是否可供使用。 |
> |  | **主機名稱可用** |  |
> | 動作 | microsoft.web/ishostnameavailable/read | 檢查主機名稱是否可供使用。 |
> |  | **是使用者名稱可用** |  |
> | 動作 | microsoft.web/isusernameavailable/read | 檢查使用者名稱是否可供使用。 |
> |  | **配置給主機名稱的清單** |  |
> | 動作 | Microsoft.Web/listSitesAssignedToHostName/Read | 取得指派給主機名稱之網站的名稱。 |
> |  | **位置** |  |
> | 動作 | microsoft.web/locations/extractapidefinitionfromwsdl/action | 針對位置從 WSDL 中擷取 API 定義。 |
> | 動作 | microsoft.web/locations/listwsdlinterfaces/action | 列出適用於位置的 WSDL 介面。 |
> | 動作 | microsoft.web/locations/deleteVirtualNetworkOrSubnets/action | 位置的虛擬網路或子網路刪除通知。 |
> |  | **位置/api 操作** |  |
> | 動作 | microsoft.web/locations/apioperations/read | 取得位置 API 作業。 |
> |  | **位置/連線閘道安裝** |  |
> | 動作 | microsoft.web/locations/connectiongatewayinstallations/read | 取得位置連線閘道器安裝。 |
> |  | **位置/託管** |  |
> | 動作 | microsoft.web/locations/managedapis/read | 取得位置管理的 API。 |
> | 動作 | Microsoft.Web/locations/managedapis/Join/Action | 加入受控 API。 |
> |  | **位置/託管/aapi 操作** |  |
> | 動作 | microsoft.web/locations/managedapis/apioperations/read | 取得位置受控 API 作業。 |
> |  | **位置/動作結果** |  |
> | 動作 | 微軟.Web/位置/操作結果/讀取 | 取得作業。 |
> |  | **位置/操作** |  |
> | 動作 | 微軟.web/位置/操作/讀取 | 取得作業。 |
> |  | **操作** |  |
> | 動作 | microsoft.web/operations/read | 取得作業。 |
> |  | **發佈使用者** |  |
> | 動作 | microsoft.web/publishingusers/read | 取得發佈使用者。 |
> | 動作 | microsoft.web/publishingusers/write | 更新發佈使用者。 |
> |  | **建議** |  |
> | 動作 | Microsoft.Web/recommendations/Read | 取得訂用帳戶的建議清單。 |
> |  | **資源健康中繼資料** |  |
> | 動作 | microsoft.web/resourcehealthmetadata/read | 取得資源健康狀態中繼資料。 |
> |  | **伺服器場** |  |
> | 動作 | Microsoft.Web/serverfarms/Read | 取得 App Service 方案的屬性 |
> | 動作 | Microsoft.Web/serverfarms/Write | 建立新的 App Service 方案，或更新現有的 App Service 方案 |
> | 動作 | Microsoft.Web/serverfarms/Delete | 刪除現有的 App Service 方案 |
> | 動作 | Microsoft.Web/serverfarms/restartSites/Action | 重新啟動 App Service 方案中的所有 Web Apps |
> |  | **伺服器場/功能** |  |
> | 動作 | microsoft.web/serverfarms/capabilities/read | 取得 App Service 方案的容量。 |
> |  | **伺服器場/事件網格篩選器** |  |
> | 動作 | 微軟.Web/伺服器場/事件網格過濾器/刪除 | 刪除伺服器場上的事件網格篩選器。 |
> | 動作 | 微軟.Web/伺服器場/事件網格過濾器/讀取 | 在伺服器場上獲取事件網格篩選器。 |
> | 動作 | 微軟.Web/伺服器場/事件網格過濾器/寫入 | 將事件網格篩選器放在伺服器場上。 |
> |  | **伺服器場/第一方應用/設定** |  |
> | 動作 | microsoft.web/serverfarms/firstpartyapps/settings/delete | 刪除 App Service 方案的第一方應用程式設定。 |
> | 動作 | microsoft.web/serverfarms/firstpartyapps/settings/read | 取得 App Service 方案的第一方應用程式設定。 |
> | 動作 | microsoft.web/serverfarms/firstpartyapps/settings/write | 更新 App Service 方案的第一方應用程式設定。 |
> |  | **伺服器場/混合連線命名空間/中繼** |  |
> | 動作 | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/read | 取得 App Service 方案的混合式連線命名空間轉送。 |
> | 動作 | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/delete | 刪除 App Service 方案的混合式連線命名空間轉送。 |
> |  | **伺服器場/混合連接命名空間/中繼/網站** |  |
> | 動作 | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/sites/read | 取得 App Service 方案的混合式連線命名空間轉送 Web Apps。 |
> |  | **伺服器場/混合連線計劃限制** |  |
> | 動作 | microsoft.web/serverfarms/hybridconnectionplanlimits/read | 取得 App Service 方案的混合式連線方案限制。 |
> |  | **伺服器場/混合連接中繼** |  |
> | 動作 | microsoft.web/serverfarms/hybridconnectionrelays/read | 取得 App Service 方案的混合式連線轉送。 |
> |  | **伺服器場/指標定義** |  |
> | 動作 | microsoft.web/serverfarms/metricdefinitions/read | 取得 App Service 方案的計量定義。 |
> |  | **伺服器場/指標** |  |
> | 動作 | microsoft.web/serverfarms/metrics/read | 取得 App Service 方案的計量。 |
> |  | **伺服器場/運營結果** |  |
> | 動作 | microsoft.web/serverfarms/operationresults/read | 取得 App Service 方案的作業結果。 |
> |  | **伺服器場/網站** |  |
> | 動作 | microsoft.web/serverfarms/sites/read | 取得 App Service 方案的 Web Apps。 |
> |  | **伺服器場/斯克** |  |
> | 動作 | microsoft.web/serverfarms/skus/read | 取得 App Service 方案 SKU。 |
> |  | **伺服器場/使用方式** |  |
> | 動作 | microsoft.web/serverfarms/usages/read | 取得 App Service 方案的使用量。 |
> |  | **伺服器場/虛擬網路連線** |  |
> | 動作 | microsoft.web/serverfarms/virtualnetworkconnections/read | 取得 App Service 方案的虛擬網路連線。 |
> |  | **伺服器場/虛擬網路連線/閘道** |  |
> | 動作 | microsoft.web/serverfarms/virtualnetworkconnections/gateways/write | 更新 App Service 方案的虛擬網路連線閘道器。 |
> |  | **伺服器場/虛擬網路連接/路由** |  |
> | 動作 | microsoft.web/serverfarms/virtualnetworkconnections/routes/delete | 刪除 App Service 方案的虛擬網路連線路由。 |
> | 動作 | microsoft.web/serverfarms/virtualnetworkconnections/routes/read | 取得 App Service 方案的虛擬網路連線路由。 |
> | 動作 | microsoft.web/serverfarms/virtualnetworkconnections/routes/write | 更新 App Service 方案的虛擬網路連線路由。 |
> |  | **伺服器場/工作人員** |  |
> | 動作 | microsoft.web/serverfarms/workers/reboot/action | 重新啟動 App Service 方案的背景工作角色。 |
> |  | **sites** |  |
> | 動作 | Microsoft.Web/sites/Read | 取得 Web 應用程式的屬性 |
> | 動作 | Microsoft.Web/sites/Write | 建立新的 Web 應用程式，或更新現有 Web 應用程式 |
> | 動作 | Microsoft.Web/sites/Delete | 刪除現有的 Web 應用程式 |
> | 動作 | Microsoft.Web/sites/backup/Action | 建立新的 Web 應用程式備份 |
> | 動作 | Microsoft.Web/sites/publishxml/Action | 取得 Web 應用程式的發行設定檔 xml |
> | 動作 | Microsoft.Web/sites/publish/Action | 發佈 Web 應用程式 |
> | 動作 | Microsoft.Web/sites/restart/Action | 重新啟動 Web 應用程式 |
> | 動作 | Microsoft.Web/sites/start/Action | 啟動 Web 應用程式 |
> | 動作 | Microsoft.Web/sites/stop/Action | 停止 Web 應用程式 |
> | 動作 | Microsoft.Web/sites/slotsswap/Action | 交換 Web 應用程式部署位置 |
> | 動作 | Microsoft.Web/sites/slotsdiffs/Action | 取得 Web 應用程式和位置之間的組態差異 |
> | 動作 | Microsoft.Web/sites/applySlotConfig/Action | 將目標位置的 Web 應用程式位置組態套用到目前的 Web 應用程式 |
> | 動作 | Microsoft.Web/sites/resetSlotConfig/Action | 重設 Web 應用程式組態 |
> | 動作 | 微軟.Web/網站/私人端點連接審批/操作 | 批准專用終結點連線 |
> | 動作 | microsoft.web/sites/functions/action | 函式 Web Apps。 |
> | 動作 | microsoft.web/sites/listsyncfunctiontriggerstatus/action | 清單同步功能觸發器狀態。 |
> | 動作 | microsoft.web/sites/networktrace/action | 網路追蹤 Web Apps。 |
> | 動作 | microsoft.web/sites/newpassword/action | Newpassword Web Apps。 |
> | 動作 | microsoft.web/sites/sync/action | 同步處理 Web Apps。 |
> | 動作 | microsoft.web/sites/migratemysql/action | 移轉 MySql Web Apps。 |
> | 動作 | microsoft.web/sites/recover/action | 復原 Web Apps。 |
> | 動作 | microsoft.web/sites/restoresnapshot/action | 還原 Web Apps 快照集。 |
> | 動作 | 微軟.web/網站/從刪除應用/操作中恢復 | 從已刪除的應用程式還原 Web Apps。 |
> | 動作 | microsoft.web/sites/syncfunctiontriggers/action | 同步函數觸發器。 |
> | 動作 | microsoft.web/sites/backups/action | 探索可從 Azure 儲存體中 Blob 還原的現有應用程式備份。 |
> | 動作 | microsoft.web/sites/containerlogs/action | 取得 Web 應用程式的壓縮容器記錄。 |
> | 動作 | microsoft.web/sites/restorefrombackupblob/action | 從備份 Blob 還原 Web 應用程式。 |
> |  | **網站/分析自訂主機名稱** |  |
> | 動作 | microsoft.web/sites/analyzecustomhostname/read | 分析自訂主機名稱。 |
> |  | **網站/備份** |  |
> | 動作 | microsoft.web/sites/backup/read | 取得 Web Apps 備份。 |
> | 動作 | microsoft.web/sites/backup/write | 更新 Web Apps 備份。 |
> |  | **網站/備份** |  |
> | 動作 | Microsoft.Web/sites/backups/Read | 取得 Web 應用程式備份的屬性 |
> | 動作 | microsoft.web/sites/backups/list/action | 列出 Web Apps 備份。 |
> | 動作 | microsoft.web/sites/backups/restore/action | 還原 Web Apps 備份。 |
> | 動作 | microsoft.web/sites/backups/delete | 刪除 Web Apps 備份。 |
> | 動作 | microsoft.web/sites/backups/write | 更新 Web Apps 備份。 |
> |  | **網站/設定** |  |
> | 動作 | Microsoft.Web/sites/config/Read | 取得 Web 應用程式的組態設定 |
> | 動作 | Microsoft.Web/sites/config/list/Action | 列出 Web 應用程式的安全性機密設定，例如發佈認證、應用程式設定和連接字串 |
> | 動作 | Microsoft.Web/sites/config/Write | 更新 Web 應用程式的組態設定 |
> | 動作 | microsoft.web/sites/config/delete | 刪除 Web Apps 設定。 |
> |  | **網站/設定/快照** |  |
> | 動作 | microsoft.web/sites/config/snapshots/read | 取得 Web Apps 組態快照集。 |
> |  | **網站/容器紀錄** |  |
> | 動作 | microsoft.web/sites/containerlogs/download/action | 下載 Web Apps 容器記錄。 |
> |  | **網站/連續網路工作** |  |
> | 動作 | microsoft.web/sites/continuouswebjobs/delete | 刪除 Web Apps 的連續 Web 作業。 |
> | 動作 | microsoft.web/sites/continuouswebjobs/read | 取得 Web Apps 的連續 Web 作業。 |
> | 動作 | microsoft.web/sites/continuouswebjobs/start/action | 啟動 Web Apps 的連續 Web 作業。 |
> | 動作 | microsoft.web/sites/continuouswebjobs/stop/action | 停止 Web Apps 的連續 Web 作業。 |
> |  | **網站/部署** |  |
> | 動作 | microsoft.web/sites/deployments/delete | 刪除 Web Apps 部署。 |
> | 動作 | microsoft.web/sites/deployments/read | 取得 Web Apps 部署。 |
> | 動作 | microsoft.web/sites/deployments/write | 更新 Web Apps 部署。 |
> |  | **網站/部署/紀錄** |  |
> | 動作 | microsoft.web/sites/deployments/log/read | 取得 Web Apps 部署記錄。 |
> |  | **場地/探測器** |  |
> | 動作 | microsoft.web/sites/detectors/read | 取得 Web Apps 偵測器。 |
> |  | **網站/診斷** |  |
> | 動作 | microsoft.web/sites/diagnostics/read | 取得 Web Apps 診斷類別。 |
> |  | **網站/診斷/分析** |  |
> | 動作 | microsoft.web/sites/diagnostics/analyses/read | 取得 Web Apps 診斷分析。 |
> | 動作 | microsoft.web/sites/diagnostics/analyses/execute/Action | 執行 Web Apps 診斷分析。 |
> |  | **網站/診斷/aspnetcore** |  |
> | 動作 | microsoft.web/sites/diagnostics/aspnetcore/read | 取得 ASP.NET Core 應用程式的 Web Apps 診斷。 |
> |  | **網站/診斷/自動修復** |  |
> | 動作 | microsoft.web/sites/diagnostics/autoheal/read | 取得 Web Apps 診斷 Autoheal。 |
> |  | **網站/診斷/部署** |  |
> | 動作 | microsoft.web/sites/diagnostics/deployment/read | 取得 Web Apps 診斷部署。 |
> |  | **網站/診斷/部署** |  |
> | 動作 | microsoft.web/sites/diagnostics/deployments/read | 取得 Web Apps 診斷部署。 |
> |  | **網站/診斷/探測器** |  |
> | 動作 | microsoft.web/sites/diagnostics/detectors/read | 取得 Web Apps 診斷偵測器。 |
> | 動作 | microsoft.web/sites/diagnostics/detectors/execute/Action | 執行 Web Apps 診斷偵測器。 |
> |  | **網站/診斷/失敗請求 peruri** |  |
> | 動作 | microsoft.web/sites/diagnostics/failedrequestsperuri/read | 取得 Web Apps 診斷對於每個 URI 的失敗要求。 |
> |  | **網站/診斷/分析** |  |
> | 動作 | microsoft.web/sites/diagnostics/frebanalysis/read | 取得 Web Apps 診斷的 FREB 分析。 |
> |  | **網站/診斷/記錄分析器** |  |
> | 動作 | microsoft.web/sites/diagnostics/loganalyzer/read | 取得 Web Apps 診斷的記錄分析器。 |
> |  | **網站/診斷/執行時可用性** |  |
> | 動作 | microsoft.web/sites/diagnostics/runtimeavailability/read | 取得 Web Apps 診斷的執行階段可用性。 |
> |  | **網站/診斷/服務執行狀況** |  |
> | 動作 | microsoft.web/sites/diagnostics/servicehealth/read | 取得 Web Apps 診斷的服務健康狀態。 |
> |  | **網站/診斷/站台分析** |  |
> | 動作 | microsoft.web/sites/diagnostics/sitecpuanalysis/read | 取得 Web Apps 診斷的網站 CPU 分析。 |
> |  | **網站/診斷/站點崩潰** |  |
> | 動作 | microsoft.web/sites/diagnostics/sitecrashes/read | 取得 Web Apps 診斷的網站損毀。 |
> |  | **網站/診斷/站台延遲** |  |
> | 動作 | microsoft.web/sites/diagnostics/sitelatency/read | 取得 Web Apps 診斷的網站延遲。 |
> |  | **網站/診斷/網站記憶體分析** |  |
> | 動作 | microsoft.web/sites/diagnostics/sitememoryanalysis/read | 取得 Web Apps 診斷的網站記憶體分析。 |
> |  | **網站/診斷/站台重新啟動設定更新** |  |
> | 動作 | microsoft.web/sites/diagnostics/siterestartsettingupdate/read | 取得 Web Apps 診斷的網站重新啟動設定更新。 |
> |  | **網站/診斷/站台重新啟動使用者啟動** |  |
> | 動作 | microsoft.web/sites/diagnostics/siterestartuserinitiated/read | 取得 Web Apps 診斷中使用者初始的網站重新啟動。 |
> |  | **網站/診斷/站台交換** |  |
> | 動作 | microsoft.web/sites/diagnostics/siteswap/read | 取得 Web Apps 診斷的網站交換。 |
> |  | **網站/診斷/執行緒數** |  |
> | 動作 | microsoft.web/sites/diagnostics/threadcount/read | 取得 Web Apps 診斷的執行緒計數。 |
> |  | **網站/診斷/工作人員可用性** |  |
> | 動作 | microsoft.web/sites/diagnostics/workeravailability/read | 取得 Web Apps 診斷的 Workeravailability。 |
> |  | **網站/診斷/工人流程回收** |  |
> | 動作 | microsoft.web/sites/diagnostics/workerprocessrecycle/read | 取得 Web Apps 診斷的背景工作處理序回收。 |
> |  | **網站/網域擁有權識別碼** |  |
> | 動作 | microsoft.web/sites/domainownershipidentifiers/read | 取得 Web Apps 網域擁有權識別碼。 |
> | 動作 | microsoft.web/sites/domainownershipidentifiers/write | 更新 Web Apps 網域擁有權識別碼。 |
> |  | **網站/事件網格篩選器** |  |
> | 動作 | 微軟.網站/網站/事件網格過濾器/刪除 | 移除 Web 應用程式上的事件網格篩選器。 |
> | 動作 | 微軟.網站/網站/事件網格過濾器/讀取 | 在 Web 應用上獲取事件網格篩選器。 |
> | 動作 | 微軟.網站/網站/事件網格過濾器/寫入 | 將事件網格篩選器放在 Web 應用上。 |
> |  | **網站/延伸** |  |
> | 動作 | 微軟.web/網站/擴展/刪除 | 刪除 Web Apps 的網站擴充功能。 |
> | 動作 | 微軟.web/網站/擴展/讀取 | 取得 Web Apps 的網站擴充功能。 |
> | 動作 | 微軟.web/網站/擴展/寫入 | 更新 Web Apps 的網站擴充功能。 |
> |  | **網站/功能** |  |
> | 動作 | microsoft.web/sites/functions/delete | 刪除 Web Apps 函式。 |
> | 動作 | microsoft.web/sites/functions/listsecrets/action | 列出函數機密。 |
> | 動作 | 微軟.web/網站/功能/清單鍵/操作 | 列出函數鍵。 |
> | 動作 | microsoft.web/sites/functions/read | 取得 Web Apps 函式。 |
> | 動作 | microsoft.web/sites/functions/write | 更新 Web Apps 函式。 |
> |  | **網站/功能/鍵** |  |
> | 動作 | 微軟.web/網站/功能/鍵/寫入 | 更新功能鍵。 |
> | 動作 | 微軟.web/網站/功能/鍵/刪除 | 刪除功能鍵。 |
> |  | **網站/功能/主鍵** |  |
> | 動作 | microsoft.web/sites/functions/masterkey/read | 取得 Web Apps 函式的主要金鑰。 |
> |  | **網站/功能/屬性** |  |
> | 動作 | 微軟.web/網站/功能/屬性/讀取 | 讀取 Web 應用功能屬性。 |
> | 動作 | 微軟.web/網站/功能/屬性/寫入 | 更新 Web 應用功能屬性。 |
> |  | **網站/功能/權杖** |  |
> | 動作 | microsoft.web/sites/functions/token/read | 取得 Web Apps 函式的權杖。 |
> |  | **網站/主機** |  |
> | 動作 | 微軟.web/網站/主機/清單鍵/操作 | 列出函數主機鍵。 |
> | 動作 | 微軟.web/網站/主機/同步/操作 | 同步函數觸發器。 |
> | 動作 | 微軟.web/網站/主機/清單同步狀態/操作 | 清單同步函數觸發狀態。 |
> |  | **網站/主機/功能鍵** |  |
> | 動作 | 微軟.web/網站/主機/功能鍵/寫入 | 更新函數主機功能鍵。 |
> | 動作 | 微軟.web/網站/主機/功能鍵/刪除 | 刪除函數主機功能鍵。 |
> |  | **網站/主機/屬性** |  |
> | 動作 | 微軟.web/網站/主機/屬性/讀取 | 讀取 Web 應用功能主機屬性。 |
> |  | **網站/主機/系統鍵** |  |
> | 動作 | 微軟.web/網站/主機/系統鍵/寫入 | 更新功能主機系統金鑰。 |
> | 動作 | 微軟.web/網站/主機/系統鍵/刪除 | 刪除功能主機系統金鑰。 |
> |  | **網站/主機名綁定** |  |
> | 動作 | microsoft.web/sites/hostnamebindings/delete | 刪除 Web Apps 的主機名稱繫結。 |
> | 動作 | microsoft.web/sites/hostnamebindings/read | 取得 Web Apps 的主機名稱繫結。 |
> | 動作 | microsoft.web/sites/hostnamebindings/write | 更新 Web Apps 的主機名稱繫結。 |
> |  | **網站/主機執行時間** |  |
> | 動作 | Microsoft.Web/sites/hostruntime/host/action | 執行函式應用程式的執行階段動作，例如同步觸發程序、新增函式、叫用函式、刪除函式等動作。 |
> |  | **網站/主機執行時間/函數/鍵** |  |
> | 動作 | microsoft.web/sites/hostruntime/functions/keys/read | 取得 Web Apps Hostruntime 函式金鑰。 |
> |  | **網站/主機執行時間/主機** |  |
> | 動作 | microsoft.web/sites/hostruntime/host/read | 取得 Web Apps Hostruntime 主機。 |
> |  | **網站/主機運行時間/主機/_master** |  |
> | 動作 | Microsoft.Web/sites/hostruntime/host/_master/read | 取得系統管理作業的函式應用程式主要金鑰 |
> |  | **站台/混合連接** |  |
> | 動作 | microsoft.web/sites/hybridconnection/delete | 刪除 Web Apps 的混合式連線。 |
> | 動作 | microsoft.web/sites/hybridconnection/read | 取得 Web Apps 的混合式連線。 |
> | 動作 | microsoft.web/sites/hybridconnection/write | 更新 Web Apps 的混合式連線。 |
> |  | **網站/混合連線命名空間/中繼** |  |
> | 動作 | microsoft.web/sites/hybridconnectionnamespaces/relays/delete | 刪除 Web Apps 的混合式連線命名空間轉送。 |
> | 動作 | microsoft.web/sites/hybridconnectionnamespaces/relays/listkeys/action | 列出 Web Apps 的混合式連線命名空間轉送。 |
> | 動作 | microsoft.web/sites/hybridconnectionnamespaces/relays/write | 更新 Web Apps 的混合式連線命名空間轉送。 |
> | 動作 | microsoft.web/sites/hybridconnectionnamespaces/relays/read | 取得 Web Apps 的混合式連線命名空間轉送。 |
> |  | **站台/混合連接中繼** |  |
> | 動作 | microsoft.web/sites/hybridconnectionrelays/read | 取得 Web Apps 的混合式連線轉送。 |
> |  | **sites/instances** |  |
> | 動作 | microsoft.web/sites/instances/read | 取得 Web Apps 執行個體。 |
> |  | **站台/實體/部署** |  |
> | 動作 | microsoft.web/sites/instances/deployments/read | 取得 Web Apps 的執行個體部署。 |
> | 動作 | microsoft.web/sites/instances/deployments/delete | 刪除 Web Apps 的執行個體部署。 |
> |  | **sites/instances/extensions** |  |
> | 動作 | microsoft.web/sites/instances/extensions/read | 取得 Web Apps 的執行個體擴充功能。 |
> |  | **網站/實體/擴展/日誌** |  |
> | 動作 | microsoft.web/sites/instances/extensions/log/read | 取得 Web Apps 的執行個體擴充功能記錄。 |
> |  | **網站/實體/擴展/行程** |  |
> | 動作 | microsoft.web/sites/instances/extensions/processes/read | 取得 Web Apps 執行個體擴充功能程序。 |
> |  | **網站/實體/行程** |  |
> | 動作 | microsoft.web/sites/instances/processes/delete | 刪除 Web Apps 的執行個體處理序。 |
> | 動作 | microsoft.web/sites/instances/processes/read | 取得 Web Apps 的執行個體處理序。 |
> |  | **網站/實體/行程/模組** |  |
> | 動作 | microsoft.web/sites/instances/processes/modules/read | 取得 Web Apps 執行個體處理程序模組。 |
> |  | **網站/實體/行程/執行緒** |  |
> | 動作 | microsoft.web/sites/instances/processes/threads/read | 取得 Web Apps 執行個體處理程序的執行緒。 |
> |  | **網站/指標定義** |  |
> | 動作 | microsoft.web/sites/metricdefinitions/read | 取得 Web Apps 計量定義。 |
> |  | **sites/metrics** |  |
> | 動作 | microsoft.web/sites/metrics/read | 取得 Web Apps 計量。 |
> |  | **網站/指標定義** |  |
> | 動作 | microsoft.web/sites/metricsdefinitions/read | 取得 Web Apps 計量定義。 |
> |  | **網站/移轉** |  |
> | 動作 | microsoft.web/sites/migratemysql/read | 取得 Web Apps 的移轉 MySql。 |
> |  | **網站/網路追蹤/操作結果** |  |
> | 動作 | 微軟.web/網站/網路追蹤/操作結果/讀取 | 獲取 Web 應用網路追蹤操作結果。 |
> |  | **網站/動作結果** |  |
> | 動作 | microsoft.web/sites/operationresults/read | 取得 Web Apps 的作業結果。 |
> |  | **網站/操作** |  |
> | 動作 | microsoft.web/sites/operations/read | 取得 Web Apps 作業。 |
> |  | **網站/服務櫃** |  |
> | 動作 | microsoft.web/sites/perfcounters/read | 取得 Web Apps 的效能計數器。 |
> |  | **sites/premieraddons** |  |
> | 動作 | microsoft.web/sites/premieraddons/delete | 刪除 Web Apps 的頂級附加元件。 |
> | 動作 | microsoft.web/sites/premieraddons/read | 取得 Web Apps 的頂級附加元件。 |
> | 動作 | microsoft.web/sites/premieraddons/write | 更新 Web Apps 的頂級附加元件。 |
> |  | **網站/私人存取** |  |
> | 動作 | microsoft.web/sites/privateaccess/read | 取得私人網站存取啟用和已授權虛擬網路 (可存取該網站) 的資料。 |
> |  | **網站/流程** |  |
> | 動作 | microsoft.web/sites/processes/read | 取得 Web Apps 處理序。 |
> |  | **網站/流程/模組** |  |
> | 動作 | 微軟.web/網站/行程/模組/讀取 | 獲取 Web 應用處理模組。 |
> |  | **站台/行程/執行緒** |  |
> | 動作 | 微軟.web/網站/行程/線程/讀取 | 獲取 Web 應用進程線程。 |
> |  | **網站/公共證書** |  |
> | 動作 | microsoft.web/sites/publiccertificates/delete | 刪除 Web Apps 的公開憑證。 |
> | 動作 | microsoft.web/sites/publiccertificates/read | 取得 Web Apps 的公開憑證。 |
> | 動作 | microsoft.web/sites/publiccertificates/write | 更新 Web Apps 的公開憑證。 |
> |  | **網站/發佈xml** |  |
> | 動作 | microsoft.web/sites/publishxml/read | 取得 Web Apps 的發佈 XML。 |
> |  | **網站/推薦歷史記錄** |  |
> | 動作 | microsoft.web/sites/recommendationhistory/read | 取得 Web Apps 的建議歷程記錄。 |
> |  | **sites/recommendations** |  |
> | 動作 | Microsoft.Web/sites/recommendations/Read | 取得 Web 應用程式的建議清單。 |
> | 動作 | microsoft.web/sites/recommendations/disable/action | 停用 Web Apps 建議。 |
> |  | **網站/資源執行狀況中繼資料** |  |
> | 動作 | microsoft.web/sites/resourcehealthmetadata/read | 取得 Web Apps 的資源健康狀態中繼資料。 |
> |  | **網站/恢復** |  |
> | 動作 | microsoft.web/sites/restore/read | 取得 Web Apps 還原。 |
> | 動作 | microsoft.web/sites/restore/write | 還原 Web Apps。 |
> |  | **網站/站台擴展** |  |
> | 動作 | microsoft.web/sites/siteextensions/delete | 刪除 Web Apps 的網站擴充功能。 |
> | 動作 | microsoft.web/sites/siteextensions/read | 取得 Web Apps 的網站擴充功能。 |
> | 動作 | microsoft.web/sites/siteextensions/write | 更新 Web Apps 的網站擴充功能。 |
> |  | **sites/slots** |  |
> | 動作 | Microsoft.Web/sites/slots/Write | 建立新的 Web 應用程式位置，或更新現有的 Web 應用程式位置 |
> | 動作 | Microsoft.Web/sites/slots/Delete | 刪除現有的 Web 應用程式位置 |
> | 動作 | Microsoft.Web/sites/slots/backup/Action | 建立新的 Web 應用程式位置備份。 |
> | 動作 | Microsoft.Web/sites/slots/publishxml/Action | 取得 Web 應用程式位置的發行設定檔 xml |
> | 動作 | Microsoft.Web/sites/slots/publish/Action | 發佈 Web 應用程式位置 |
> | 動作 | Microsoft.Web/sites/slots/restart/Action | 重新啟動 Web 應用程式位置 |
> | 動作 | Microsoft.Web/sites/slots/start/Action | 啟動 Web 應用程式位置 |
> | 動作 | Microsoft.Web/sites/slots/stop/Action | 停止 Web 應用程式位置 |
> | 動作 | Microsoft.Web/sites/slots/slotsswap/Action | 交換 Web 應用程式部署位置 |
> | 動作 | Microsoft.Web/sites/slots/slotsdiffs/Action | 取得 Web 應用程式和位置之間的組態差異 |
> | 動作 | Microsoft.Web/sites/slots/applySlotConfig/Action | 將目標位置的 Web 應用程式位置組態套用到目前的位置。 |
> | 動作 | Microsoft.Web/sites/slots/resetSlotConfig/Action | 重設 Web 應用程式位置組態 |
> | 動作 | Microsoft.Web/sites/slots/Read | 取得 Web 應用程式部署位置的屬性 |
> | 動作 | microsoft.web/sites/slots/newpassword/action | Newpassword Web Apps 位置。 |
> | 動作 | microsoft.web/sites/slots/sync/action | 同步處理 Web Apps 的位置。 |
> | 動作 | microsoft.web/sites/slots/networktrace/action | 網路追蹤 Web Apps 位置。 |
> | 動作 | microsoft.web/sites/slots/recover/action | 復原 Web Apps 位置。 |
> | 動作 | microsoft.web/sites/slots/restoresnapshot/action | 還原 Web Apps 位置快照集。 |
> | 動作 | 微軟.web/網站/插槽/從刪除的刪除/操作恢復 | 從已刪除的應用程式還原 Web 應用程式位置。 |
> | 動作 | microsoft.web/sites/slots/backups/action | 探索 Web Apps 位置備份。 |
> | 動作 | microsoft.web/sites/slots/containerlogs/action | 取得 Web 應用程式位置的壓縮容器記錄。 |
> | 動作 | microsoft.web/sites/slots/restorefrombackupblob/action | 從備份 Blob 還原 Web 應用程式位置。 |
> |  | **網站/插槽/分析自定義主機名** |  |
> | 動作 | microsoft.web/sites/slots/analyzecustomhostname/read | 取得 Web Apps 位置的分析自訂主機名稱。 |
> |  | **網站/插槽/備份** |  |
> | 動作 | microsoft.web/sites/slots/backup/write | 更新 Web Apps 位置的備份。 |
> | 動作 | microsoft.web/sites/slots/backup/read | 取得 Web Apps 位置備份。 |
> |  | **網站/插槽/備份** |  |
> | 動作 | Microsoft.Web/sites/slots/backups/Read | 取得 Web 應用程式位置備份的屬性 |
> | 動作 | microsoft.web/sites/slots/backups/list/action | 列出 Web Apps 位置的備份。 |
> | 動作 | microsoft.web/sites/slots/backups/restore/action | 還原 Web Apps 位置的備份。 |
> | 動作 | microsoft.web/sites/slots/backups/delete | 刪除 Web Apps 位置備份。 |
> |  | **網站/插槽/配置** |  |
> | 動作 | Microsoft.Web/sites/slots/config/Read | 取得 Web 應用程式位置的組態設定 |
> | 動作 | Microsoft.Web/sites/slots/config/list/Action | 列出 Web 應用程式位置的安全性機密設定，例如發佈認證、應用程式設定和連接字串 |
> | 動作 | Microsoft.Web/sites/slots/config/Write | 更新 Web 應用程式位置的組態設定 |
> | 動作 | microsoft.web/sites/slots/config/delete | 刪除 Web Apps 的位置設定。 |
> |  | **網站/插槽/容器日誌** |  |
> | 動作 | microsoft.web/sites/slots/containerlogs/download/action | 下載 Web Apps 位置容器記錄。 |
> |  | **網站/插槽/連續網路作業** |  |
> | 動作 | microsoft.web/sites/slots/continuouswebjobs/delete | 刪除 Web Apps 位置的連續 Web 作業。 |
> | 動作 | microsoft.web/sites/slots/continuouswebjobs/read | 取得 Web Apps 位置的連續 Web 作業。 |
> | 動作 | microsoft.web/sites/slots/continuouswebjobs/start/action | 啟動 Web Apps 位置的連續 Web 作業。 |
> | 動作 | microsoft.web/sites/slots/continuouswebjobs/stop/action | 停止 Web Apps 位置的連續 Web 作業。 |
> |  | **網站/插槽/部署** |  |
> | 動作 | microsoft.web/sites/slots/deployments/delete | 刪除 Web Apps 的位置部署。 |
> | 動作 | microsoft.web/sites/slots/deployments/read | 取得 Web Apps 的位置部署。 |
> | 動作 | microsoft.web/sites/slots/deployments/write | 更新 Web Apps 的位置部署。 |
> |  | **網站/插槽/部署/日誌** |  |
> | 動作 | microsoft.web/sites/slots/deployments/log/read | 取得 Web Apps 的位置部署記錄。 |
> |  | **網站/插槽/探測器** |  |
> | 動作 | microsoft.web/sites/slots/detectors/read | 取得 Web Apps 位置偵測器。 |
> |  | **網站/插槽/診斷** |  |
> | 動作 | microsoft.web/sites/slots/diagnostics/read | 取得 Web Apps 位置診斷。 |
> |  | **網站/插槽/診斷/分析** |  |
> | 動作 | microsoft.web/sites/slots/diagnostics/analyses/read | 取得 Web Apps 位置診斷的分析。 |
> | 動作 | microsoft.web/sites/slots/diagnostics/analyses/execute/Action | 執行 Web Apps 位置診斷的分析。 |
> |  | **網站/插槽/診斷/aspnetcore** |  |
> | 動作 | microsoft.web/sites/slots/diagnostics/aspnetcore/read | 取得 ASP.NET Core 應用程式的 Web Apps 位置診斷。 |
> |  | **網站/插槽/診斷/自動修復** |  |
> | 動作 | microsoft.web/sites/slots/diagnostics/autoheal/read | 取得 Web Apps 位置診斷的 Autoheal。 |
> |  | **網站/插槽/診斷/部署** |  |
> | 動作 | microsoft.web/sites/slots/diagnostics/deployment/read | 取得 Web Apps 位置診斷的部署。 |
> |  | **網站/插槽/診斷/部署** |  |
> | 動作 | microsoft.web/sites/slots/diagnostics/deployments/read | 取得 Web Apps 位置診斷的部署。 |
> |  | **網站/插槽/診斷/檢測器** |  |
> | 動作 | microsoft.web/sites/slots/diagnostics/detectors/read | 取得 Web Apps 位置診斷的偵測器。 |
> | 動作 | microsoft.web/sites/slots/diagnostics/detectors/execute/Action | 執行 Web Apps 位置診斷的偵測器。 |
> |  | **網站/插槽/診斷/分析** |  |
> | 動作 | microsoft.web/sites/slots/diagnostics/frebanalysis/read | 取得 Web Apps 位置診斷的 FREB 分析。 |
> |  | **網站/插槽/診斷/日誌分析器** |  |
> | 動作 | microsoft.web/sites/slots/diagnostics/loganalyzer/read | 取得 Web Apps 位置診斷的記錄分析器。 |
> |  | **網站/插槽/診斷/運行時可用性** |  |
> | 動作 | microsoft.web/sites/slots/diagnostics/runtimeavailability/read | 取得 Web Apps 位置診斷的執行階段可用性。 |
> |  | **網站/插槽/診斷/服務運行狀況** |  |
> | 動作 | microsoft.web/sites/slots/diagnostics/servicehealth/read | 取得 Web Apps 位置診斷的服務健康狀態。 |
> |  | **網站/插槽/診斷/網站分析** |  |
> | 動作 | microsoft.web/sites/slots/diagnostics/sitecpuanalysis/read | 取得 Web Apps 位置診斷的網站 CPU 分析。 |
> |  | **網站/插槽/診斷/站點崩潰** |  |
> | 動作 | microsoft.web/sites/slots/diagnostics/sitecrashes/read | 取得 Web Apps 位置診斷的網站損毀。 |
> |  | **網站/插槽/診斷/站點延遲** |  |
> | 動作 | microsoft.web/sites/slots/diagnostics/sitelatency/read | 取得 Web Apps 位置診斷的網站延遲。 |
> |  | **網站/插槽/診斷/網站記憶體分析** |  |
> | 動作 | microsoft.web/sites/slots/diagnostics/sitememoryanalysis/read | 取得 Web Apps 位置診斷的網站記憶體分析。 |
> |  | **網站/插槽/診斷/站點重新啟動設置更新** |  |
> | 動作 | microsoft.web/sites/slots/diagnostics/siterestartsettingupdate/read | 取得 Web Apps 位置診斷的網站重新啟動設定更新。 |
> |  | **網站/插槽/診斷/站點重新啟動用戶啟動** |  |
> | 動作 | microsoft.web/sites/slots/diagnostics/siterestartuserinitiated/read | 取得 Web Apps 位置診斷中使用者初始的網站重新啟動。 |
> |  | **網站/插槽/診斷/站點交換** |  |
> | 動作 | microsoft.web/sites/slots/diagnostics/siteswap/read | 取得 Web Apps 位置診斷的網站交換。 |
> |  | **網站/插槽/診斷/線程計數** |  |
> | 動作 | microsoft.web/sites/slots/diagnostics/threadcount/read | 取得 Web Apps 位置診斷的執行緒計數。 |
> |  | **網站/插槽/診斷/工作人員可用性** |  |
> | 動作 | microsoft.web/sites/slots/diagnostics/workeravailability/read | 取得 Web Apps 位置診斷的 Workeravailability。 |
> |  | **網站/插槽/診斷/工人過程回收** |  |
> | 動作 | microsoft.web/sites/slots/diagnostics/workerprocessrecycle/read | 取得 Web Apps 位置診斷的背景工作處理序回收。 |
> |  | **網站/插槽/網域擁有權識別碼** |  |
> | 動作 | microsoft.web/sites/slots/domainownershipidentifiers/read | 取得 Web Apps 位置的網域擁有權識別碼。 |
> |  | **網站/插槽/擴展** |  |
> | 動作 | 微軟.web/網站/插槽/擴展/讀取 | 獲取 Web 應用插槽擴展。 |
> | 動作 | 微軟.web/網站/插槽/擴展/寫入 | 更新 Web 應用插槽擴展。 |
> |  | **網站/插槽/功能** |  |
> | 動作 | microsoft.web/sites/slots/functions/read | 取得 Web Apps 位置函式。 |
> | 動作 | microsoft.web/sites/slots/functions/listsecrets/action | 列出祕密 Web Apps 位置函式。 |
> |  | **網站/插槽/主機名綁定** |  |
> | 動作 | microsoft.web/sites/slots/hostnamebindings/delete | 刪除 Web Apps 位置的主機名稱繫結。 |
> | 動作 | microsoft.web/sites/slots/hostnamebindings/read | 取得 Web Apps 位置的主機名稱繫結。 |
> | 動作 | microsoft.web/sites/slots/hostnamebindings/write | 更新 Web Apps 位置的主機名稱繫結。 |
> |  | **網站/插槽/混合連接** |  |
> | 動作 | microsoft.web/sites/slots/hybridconnection/delete | 刪除 Web Apps 位置的混合式連線。 |
> | 動作 | microsoft.web/sites/slots/hybridconnection/read | 取得 Web Apps 位置的混合式連線。 |
> | 動作 | microsoft.web/sites/slots/hybridconnection/write | 更新 Web Apps 位置的混合式連線。 |
> |  | **網站/插槽/混合連接命名空間/中繼** |  |
> | 動作 | microsoft.web/sites/slots/hybridconnectionnamespaces/relays/delete | 刪除 Web Apps 位置的混合式連線命名空間轉送。 |
> | 動作 | microsoft.web/sites/slots/hybridconnectionnamespaces/relays/write | 更新 Web Apps 位置的混合式連線命名空間轉送。 |
> |  | **網站/插槽/混合連接中繼** |  |
> | 動作 | microsoft.web/sites/slots/hybridconnectionrelays/read | 取得 Web Apps 位置的混合式連線轉送。 |
> |  | **sites/slots/instances** |  |
> | 動作 | microsoft.web/sites/slots/instances/read | 取得 Web Apps 位置的執行個體。 |
> |  | **網站/插槽/實例/部署** |  |
> | 動作 | microsoft.web/sites/slots/instances/deployments/read | 取得 Web Apps 位置的執行個體部署。 |
> |  | **網站/插槽/實例/進程** |  |
> | 動作 | microsoft.web/sites/slots/instances/processes/read | 取得 Web Apps 位置的執行個體處理序。 |
> | 動作 | microsoft.web/sites/slots/instances/processes/delete | 刪除 Web Apps 位置的執行個體處理序。 |
> |  | **網站/插槽/指標定義** |  |
> | 動作 | microsoft.web/sites/slots/metricdefinitions/read | 取得 Web Apps 位置的計量定義。 |
> |  | **sites/slots/metrics** |  |
> | 動作 | microsoft.web/sites/slots/metrics/read | 取得 Web Apps 位置的計量。 |
> |  | **網站/插槽/遷移** |  |
> | 動作 | microsoft.web/sites/slots/migratemysql/read | 取得 Web Apps 位置的移轉 MySql。 |
> |  | **網站/插槽/網路追蹤/操作結果** |  |
> | 動作 | 微軟.Web/網站/插槽/網路跟蹤/操作結果/讀取 | 獲取 Web 應用插槽網路跟蹤操作結果。 |
> |  | **網站/插槽/操作結果** |  |
> | 動作 | microsoft.web/sites/slots/operationresults/read | 取得 Web Apps 位置的作業結果。 |
> |  | **網站/插槽/操作** |  |
> | 動作 | microsoft.web/sites/slots/operations/read | 取得 Web Apps 位置的作業。 |
> |  | **網站/插槽/計數器** |  |
> | 動作 | microsoft.web/sites/slots/perfcounters/read | 取得 Web Apps 位置的效能計數器。 |
> |  | **網站/插槽/php 紀錄記錄** |  |
> | 動作 | microsoft.web/sites/slots/phplogging/read | 取得 Web Apps 位置的 Phplogging。 |
> |  | **網站/插槽/進階元件** |  |
> | 動作 | microsoft.web/sites/slots/premieraddons/delete | 刪除 Web Apps 位置的頂級附加元件。 |
> | 動作 | microsoft.web/sites/slots/premieraddons/read | 取得 Web Apps 位置的頂級附加元件。 |
> | 動作 | microsoft.web/sites/slots/premieraddons/write | 更新 Web Apps 位置的頂級附加元件。 |
> |  | **網站/插槽/流程** |  |
> | 動作 | microsoft.web/sites/slots/processes/read | 取得 Web Apps 位置的程序。 |
> |  | **網站/插槽/公共證書** |  |
> | 動作 | microsoft.web/sites/slots/publiccertificates/read | 取得 Web Apps 位置的公開憑證。 |
> | 動作 | microsoft.web/sites/slots/publiccertificates/write | 建立或更新 Web Apps 位置的公開憑證。 |
> | 動作 | microsoft.web/sites/slots/publiccertificates/delete | 刪除 Web Apps 位置的公開憑證。 |
> |  | **網站/插槽/資源運行狀況中繼資料** |  |
> | 動作 | microsoft.web/sites/slots/resourcehealthmetadata/read | 取得 Web Apps 位置的資源健康狀態中繼資料。 |
> |  | **網站/插槽/恢復** |  |
> | 動作 | microsoft.web/sites/slots/restore/read | 取得 Web Apps 位置的還原。 |
> | 動作 | microsoft.web/sites/slots/restore/write | 還原 Web Apps 位置。 |
> |  | **網站/插槽/站點擴展** |  |
> | 動作 | microsoft.web/sites/slots/siteextensions/delete | 刪除 Web Apps 位置的網站擴充功能。 |
> | 動作 | microsoft.web/sites/slots/siteextensions/read | 取得 Web Apps 位置的網站擴充功能。 |
> | 動作 | microsoft.web/sites/slots/siteextensions/write | 更新 Web Apps 位置的網站擴充功能。 |
> |  | **網站/插槽/快照** |  |
> | 動作 | microsoft.web/sites/slots/snapshots/read | 取得 Web Apps 位置的快照集。 |
> |  | **網站/插槽/源控制項** |  |
> | 動作 | Microsoft.Web/sites/slots/sourcecontrols/Read | 取得 Web 應用程式位置的原始檔控制組態設定 |
> | 動作 | Microsoft.Web/sites/slots/sourcecontrols/Write | 更新 Web 應用程式位置的原始檔控制組態設定 |
> | 動作 | Microsoft.Web/sites/slots/sourcecontrols/Delete | 刪除 Web 應用程式位置的原始檔控制組態設定 |
> |  | **網站/插槽/觸發網路作業** |  |
> | 動作 | microsoft.web/sites/slots/triggeredwebjobs/delete | 刪除 Web Apps 位置所觸發的 WebJobs。 |
> | 動作 | microsoft.web/sites/slots/triggeredwebjobs/read | 取得 Web Apps 位置所觸發的 WebJobs。 |
> | 動作 | microsoft.web/sites/slots/triggeredwebjobs/run/action | 執行 Web Apps 位置所觸發的 WebJobs。 |
> |  | **網站/插槽/用法** |  |
> | 動作 | microsoft.web/sites/slots/usages/read | 取得 Web Apps 位置的使用量。 |
> |  | **網站/插槽/虛擬網路連接** |  |
> | 動作 | microsoft.web/sites/slots/virtualnetworkconnections/delete | 刪除 Web Apps 位置的虛擬網路連線。 |
> | 動作 | microsoft.web/sites/slots/virtualnetworkconnections/read | 取得 Web Apps 位置的虛擬網路連線。 |
> | 動作 | microsoft.web/sites/slots/virtualnetworkconnections/write | 更新 Web Apps 位置的虛擬網路連線。 |
> |  | **網站/插槽/虛擬網路連接/閘道** |  |
> | 動作 | microsoft.web/sites/slots/virtualnetworkconnections/gateways/write | 更新 Web Apps 位置的虛擬網路連線閘道器。 |
> |  | **網站/插槽/網路作業** |  |
> | 動作 | microsoft.web/sites/slots/webjobs/read | 取得 Web Apps 位置的 WebJobs。 |
> |  | **網站/快照** |  |
> | 動作 | microsoft.web/sites/snapshots/read | 取得 Web Apps 快照集。 |
> |  | **網站/來源控制項** |  |
> | 動作 | Microsoft.Web/sites/sourcecontrols/Read | 取得 Web 應用程式的原始檔控制組態設定 |
> | 動作 | Microsoft.Web/sites/sourcecontrols/Write | 更新 Web 應用程式的原始檔控制組態設定 |
> | 動作 | Microsoft.Web/sites/sourcecontrols/Delete | 刪除 Web 應用程式的原始檔控制組態設定 |
> |  | **網站/觸發網路作業** |  |
> | 動作 | microsoft.web/sites/triggeredwebjobs/delete | 刪除 Web Apps 所觸發的 WebJobs。 |
> | 動作 | microsoft.web/sites/triggeredwebjobs/read | 取得 Web Apps 所觸發的 WebJobs。 |
> | 動作 | microsoft.web/sites/triggeredwebjobs/run/action | 執行 Web Apps 所觸發的 WebJobs。 |
> |  | **網站/觸發網路作業/歷史記錄** |  |
> | 動作 | microsoft.web/sites/triggeredwebjobs/history/read | 取得 Web Apps 所觸發的 WebJobs 歷程記錄。 |
> |  | **網站/用法** |  |
> | 動作 | microsoft.web/sites/usages/read | 取得 Web Apps 使用量。 |
> |  | **站台/虛擬網路連線** |  |
> | 動作 | microsoft.web/sites/virtualnetworkconnections/delete | 刪除 Web Apps 的虛擬網路連線。 |
> | 動作 | microsoft.web/sites/virtualnetworkconnections/read | 取得 Web Apps 的虛擬網路連線。 |
> | 動作 | microsoft.web/sites/virtualnetworkconnections/write | 更新 Web Apps 的虛擬網路連線。 |
> |  | **網站/虛擬網路連線/閘道** |  |
> | 動作 | microsoft.web/sites/virtualnetworkconnections/gateways/read | 取得 Web Apps 的虛擬網路連線閘道器。 |
> | 動作 | microsoft.web/sites/virtualnetworkconnections/gateways/write | 更新 Web Apps 的虛擬網路連線閘道器。 |
> |  | **網站/網站工作** |  |
> | 動作 | microsoft.web/sites/webjobs/read | 取得 Web Apps WebJobs。 |
> |  | **斯克庫斯** |  |
> | 動作 | microsoft.web/skus/read | 取得 SKU。 |
> |  | **來源控制** |  |
> | 動作 | microsoft.web/sourcecontrols/read | 取得原始檔控制。 |
> | 動作 | microsoft.web/sourcecontrols/write | 更新原始檔控制。 |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

Azure 服務[:Azure 監視器](../azure-monitor/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> |  | **元件** |  |
> | 動作 | Microsoft.WorkloadMonitor/components/read | 取得資源的元件 |
> |  | **componentsSummary** |  |
> | 動作 | Microsoft.WorkloadMonitor/componentsSummary/read | 取得元件的摘要 |
> |  | **monitorInstances** |  |
> | 動作 | Microsoft.WorkloadMonitor/monitorInstances/read | 取得資源的監視器執行個體 |
> |  | **monitorInstancesSummary** |  |
> | 動作 | Microsoft.WorkloadMonitor/monitorInstancesSummary/read | 取得監視器執行個體的摘要 |
> |  | **顯示器** |  |
> | 動作 | Microsoft.WorkloadMonitor/monitors/read | 取得資源的監視器 |
> | 動作 | Microsoft.WorkloadMonitor/monitors/write | 設定資源的監視器 |
> |  | **notificationSettings** |  |
> | 動作 | Microsoft.WorkloadMonitor/notificationSettings/read | 取得資源的通知設定 |
> | 動作 | Microsoft.WorkloadMonitor/notificationSettings/write | 設定資源的通知設定 |
> |  | **操作** |  |
> | 動作 | Microsoft.WorkloadMonitor/operations/read | 取得支援的作業 |

## <a name="next-steps"></a>後續步驟

- [將資源提供者與服務比對](../azure-resource-manager/management/azure-services-resource-providers.md)
- [Azure 自訂角色](custom-roles.md)
- [Azure 內建角色](built-in-roles.md)
