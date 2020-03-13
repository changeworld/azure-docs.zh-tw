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
ms.date: 03/12/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 6b0d967dad250b06988865cde2f4ce11367f8acb
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79245768"
---
# <a name="azure-resource-manager-resource-provider-operations"></a>Azure Resource Manager 資源提供者作業

本文會列出每個 Azure Resource Manager 資源提供者可用的作業。 這些作業可用於[azure 自訂角色](custom-roles.md)，以對 azure 中的資源提供細微的存取控制。 作業字串的格式如下： `{Company}.{ProviderName}/{resourceType}/{action}`。 如需資源提供者命名空間如何對應至 Azure 服務的清單，請參閱[將資源提供者與服務比對](../azure-resource-manager/management/azure-services-resource-providers.md)。

資源提供者作業會不斷發展。 若要取得最新的作業，請使用 [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) 或 [az provider operation list](/cli/azure/provider/operation#az-provider-operation-list)。

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="microsoftaad"></a>Microsoft.AAD

Azure 服務： [Azure Active Directory Domain Services](../active-directory-domain-services/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.AAD/unregister/action | 取消註冊網域服務 |
> | 動作 | Microsoft.AAD/register/action | 註冊網域服務 |
> |  | **domainServices** |  |
> | 動作 | Microsoft.AAD/domainServices/read | 讀取網域服務 |
> | 動作 | Microsoft.AAD/domainServices/write | 寫入網域服務 |
> | 動作 | Microsoft.AAD/domainServices/delete | 刪除網域服務 |
> |  | **domainServices/oucontainer** |  |
> | 動作 | Microsoft.AAD/domainServices/oucontainer/read | 讀取 Ou 容器 |
> | 動作 | Microsoft.AAD/domainServices/oucontainer/write | 寫入 Ou 容器 |
> | 動作 | Microsoft.AAD/domainServices/oucontainer/delete | 刪除 Ou 容器 |
> |  | **位置/operationresults** |  |
> | 動作 | Microsoft.AAD/locations/operationresults/read |  |
> |  | **作業** |  |
> | 動作 | Microsoft.AAD/Operations/read |  |

## <a name="microsoftaadiam"></a>microsoft.aadiam

Microsoft Monitoring Insights

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> |  | **diagnosticsettings** |  |
> | 動作 | microsoft.aadiam/diagnosticsettings/write | 正在寫入診斷設定 |
> | 動作 | microsoft.aadiam/diagnosticsettings/read | 正在讀取診斷設定 |
> | 動作 | microsoft.aadiam/diagnosticsettings/delete | 正在刪除診斷設定 |
> |  | **diagnosticsettingscategories** |  |
> | 動作 | microsoft.aadiam/diagnosticsettingscategories/read | 正在讀取診斷設定類別 |
> |  | **Metricdefinitions.listasync** |  |
> | 動作 | aadiam/Metricdefinitions.listasync/read | 讀取租使用者層級的度量定義 |
> |  | **計量** |  |
> | 動作 | aadiam/計量/讀取 | 讀取租使用者層級計量 |

## <a name="microsoftaddons"></a>Microsoft.Addons

Azure 服務：核心

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.Addons/register/action | 向 Microsoft.Addons 註冊指定的訂用帳戶 |
> |  | **業務** |  |
> | 動作 | Microsoft.Addons/operations/read | 取得支援的 RP 作業。 |
> |  | **supportProviders** |  |
> | 動作 | Microsoft.Addons/supportProviders/listsupportplaninfo/action | 列出指定訂用帳戶目前的支援方案資訊。 |
> |  | **supportProviders/supportPlanTypes** |  |
> | 動作 | Microsoft.Addons/supportProviders/supportPlanTypes/read | 取得指定的 Canonical 支援方案狀態。 |
> | 動作 | Microsoft.Addons/supportProviders/supportPlanTypes/write | 新增指定的 Canonical 支援方案類型。 |
> | 動作 | Microsoft.Addons/supportProviders/supportPlanTypes/delete | 移除指定的 Canonical 支援方案 |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

Azure 服務： [Azure Active Directory](../active-directory/index.yml)

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
> |  | **addsservices/addomainservicemembers** |  |
> | 動作 | Microsoft.ADHybridHealthService/addsservices/addomainservicemembers/read | 取得指定服務名稱的所有伺服器。 |
> |  | **addsservices/警示** |  |
> | 動作 | Microsoft.ADHybridHealthService/addsservices/alerts/read | 取得樹系的警示詳細資料，例如 alertid、警示引發日期、上次偵測到的警示、警示說明、上次更新日期、警示層級、警示狀態、警示疑難排解連結等等。 |
> |  | **addsservices/設定** |  |
> | 動作 | Microsoft.ADHybridHealthService/addsservices/configuration/read | 取得樹系的服務組態。 範例 - 樹系名稱、功能等級、網域命名主機 FSMO 角色、架構主機 FSMO 角色等等。 |
> |  | **addsservices/維度** |  |
> | 動作 | Microsoft.ADHybridHealthService/addsservices/dimensions/read | 取得樹系的網域和網站詳細資料。 範例 - 健康情況狀態、作用中警示、已解決的警示、屬性 (例如，網域功能等級、樹系、基礎結構主機、PDC、RID 主機等等)。  |
> |  | **addsservices/features/userpreference** |  |
> | 動作 | Microsoft.ADHybridHealthService/addsservices/features/userpreference/read | 取得樹系的使用者喜好設定。<br>範例 - MetricCounterName，例如 ldapsuccessfulbinds、ntlmauthentications、kerberosauthentications、addsinsightsagentprivatebytes、ldapsearches。<br>UI 圖表等項目的設定。 |
> |  | **addsservices/forestsummary** |  |
> | 動作 | Microsoft.ADHybridHealthService/addsservices/forestsummary/read | 取得指定樹系的樹系摘要，例如樹系名稱、此樹系下的網域數目、網站數目和網站詳細資料等。 |
> |  | **addsservices/metricmetadata** |  |
> | 動作 | Microsoft.ADHybridHealthService/addsservices/metricmetadata/read | 取得指定服務的支援計量清單。<br>例如，ADFS 服務的外部網路帳戶鎖定、失敗要求總數、未處理的權杖要求數 (Proxy)、每秒權杖要求數等等。<br>ADDomainService 的每秒 NTLM 驗證數、每秒 LDAP 成功繫結數、LDAP 繫結時間、LDAP 作用中執行緒、每秒 Kerberos 驗證數、ATQ 執行緒總數等。<br>ADSync 服務的執行設定檔延遲、所建立的 TCP 連線數、Insights 代理程式私用位元組數、將統計資料匯出至 Azure AD。 |
> |  | **addsservices/計量/群組** |  |
> | 動作 | Microsoft.ADHybridHealthService/addsservices/metrics/groups/read | 在指定了服務時，此 API 會取得計量資訊。<br>例如，此 API 可用來取得與下列項目相關的資訊：ADFederation 服務的外部網路帳戶鎖定、失敗要求總數、未處理的權杖要求數 (Proxy)、每秒權杖要求數等等。<br>ADDomain 服務的每秒 NTLM 驗證數、每秒 LDAP 成功繫結數、LDAP 繫結時間、LDAP 作用中執行緒、每秒 Kerberos 驗證數、ATQ 執行緒總數等。<br>Sync 服務的執行設定檔延遲、所建立的 TCP 連線數、Insights 代理程式私用位元組數、將統計資料匯出至 Azure AD。 |
> |  | **addsservices/premiumcheck** |  |
> | 動作 | Microsoft.ADHybridHealthService/addsservices/premiumcheck/read | 此 API 會取得進階租用戶所有已上架 ADDomainServices 的清單。 |
> |  | **addsservices/replicationdetails** |  |
> | 動作 | Microsoft.ADHybridHealthService/addsservices/replicationdetails/read | 取得指定服務名稱的所有伺服器複寫詳細資料。 |
> |  | **addsservices/replicationstatus** |  |
> | 動作 | Microsoft.ADHybridHealthService/addsservices/replicationstatus/read | 取得網域控制站數目和其存在的任何複寫錯誤。 |
> |  | **addsservices/replicationsummary** |  |
> | 動作 | Microsoft.ADHybridHealthService/addsservices/replicationsummary/read | 取得完整的網域控制站清單，以及指定樹系的複寫詳細資料。 |
> |  | **addsservices/servicemembers** |  |
> | 動作 | Microsoft.ADHybridHealthService/addsservices/servicemembers/delete | 刪除指定服務和租用戶的伺服器。 |
> |  | **addsservices/servicemembers/認證** |  |
> | 動作 | Microsoft.ADHybridHealthService/addsservices/servicemembers/credentials/read | 在 ADDomainService 的伺服器註冊期間，會呼叫此 API 來取得用於將新伺服器上架的認證。 |
> |  | **配置** |  |
> | 動作 | Microsoft.ADHybridHealthService/configuration/write | 建立租用戶組態。 |
> | 動作 | Microsoft.ADHybridHealthService/configuration/read | 讀取租用戶組態。 |
> |  | **退出** |  |
> | 動作 | Microsoft.ADHybridHealthService/logs/read | 取得租用戶的代理程式安裝和註冊記錄。 |
> |  | **記錄/內容** |  |
> | 動作 | Microsoft.ADHybridHealthService/logs/contents/read | 取得 Blob 中所儲存的代理程式安裝和註冊記錄內容。 |
> |  | **業務** |  |
> | 動作 | Microsoft.ADHybridHealthService/operations/read | 取得系統所支援作業的清單。 |
> |  | **報表/availabledeployments** |  |
> | 動作 | Microsoft.ADHybridHealthService/reports/availabledeployments/read | 取得可用區域清單，以供 DevOps 用來支援客戶事件。 |
> |  | **報表/badpassword** |  |
> | 動作 | Microsoft.ADHybridHealthService/reports/badpassword/read | 取得 Active Directory Federation Service 中所有使用者的錯誤密碼嘗試清單。 |
> |  | **報表/badpassworduseridipfrequency** |  |
> | 動作 | Microsoft.ADHybridHealthService/reports/badpassworduseridipfrequency/read | 取得 Blob SAS URI，其包含新近排入佇列的報告作業狀態和最終結果，執行該作業是為了了解指定租用戶每個 UserId、每個 IPAddress、每天發生的錯誤使用者名稱/密碼嘗試頻率。 |
> |  | **報表/consentedtodevopstenants** |  |
> | 動作 | Microsoft.ADHybridHealthService/reports/consentedtodevopstenants/read | 取得 DevOps 同意的租用戶清單。 通常用於客戶支援。 |
> |  | **報表/isdevops** |  |
> | 動作 | Microsoft.ADHybridHealthService/reports/isdevops/read | 取得可指出租用戶是否已獲得 DevOps 同意的值。 |
> |  | **報表/selectdevopstenant** |  |
> | 動作 | Microsoft.ADHybridHealthService/reports/selectdevopstenant/read | 更新所選 Dev Ops 租用戶的 userid (objectid)。 |
> |  | **報表/selecteddeployment** |  |
> | 動作 | Microsoft.ADHybridHealthService/reports/selecteddeployment/read | 取得指定租用戶所選取的部署。 |
> |  | **報表/tenantassigneddeployment** |  |
> | 動作 | Microsoft.ADHybridHealthService/reports/tenantassigneddeployment/read | 在指定了租用戶識別碼時，會取得租用戶儲存位置。 |
> |  | **報表/updateselecteddeployment** |  |
> | 動作 | Microsoft.ADHybridHealthService/reports/updateselecteddeployment/read | 取得會存取資料的地理位置。 |
> |  | **services** |  |
> | 動作 | Microsoft.ADHybridHealthService/services/write | 建立租用戶中的服務執行個體。 |
> | 動作 | Microsoft.ADHybridHealthService/services/read | 讀取租用戶中的服務執行個體。 |
> | 動作 | Microsoft.ADHybridHealthService/services/delete | 刪除租用戶中的服務執行個體。 |
> | 動作 | Microsoft.ADHybridHealthService/services/servicemembers/action | 在服務中建立伺服器執行個體。 |
> |  | **服務/警示** |  |
> | 動作 | Microsoft.ADHybridHealthService/services/alerts/read | 讀取服務的警示。 |
> | 動作 | Microsoft.ADHybridHealthService/services/alerts/read | 讀取服務的警示。 |
> |  | **服務/checkservicefeatureavailibility** |  |
> | 動作 | Microsoft.ADHybridHealthService/services/checkservicefeatureavailibility/read | 在指定了功能名稱時，會確認服務是否已備妥一切所需而可使用該功能。 |
> |  | **服務/exporterrors** |  |
> | 動作 | Microsoft.ADHybridHealthService/services/exporterrors/read | 取得指定 Sync 服務的匯出錯誤。 |
> |  | **服務/exportstatus** |  |
> | 動作 | Microsoft.ADHybridHealthService/services/exportstatus/read | 取得指定服務的匯出狀態。 |
> |  | **服務/feedbacktype/意見反應** |  |
> | 動作 | Microsoft.ADHybridHealthService/services/feedbacktype/feedback/read | 取得指定服務和伺服器的警示意見反應。 |
> |  | **服務/metricmetadata** |  |
> | 動作 | Microsoft.ADHybridHealthService/services/metricmetadata/read | 取得指定服務的支援計量清單。<br>例如，ADFS 服務的外部網路帳戶鎖定、失敗要求總數、未處理的權杖要求數 (Proxy)、每秒權杖要求數等等。<br>ADDomainService 的每秒 NTLM 驗證數、每秒 LDAP 成功繫結數、LDAP 繫結時間、LDAP 作用中執行緒、每秒 Kerberos 驗證數、ATQ 執行緒總數等。<br>ADSync 服務的執行設定檔延遲、所建立的 TCP 連線數、Insights 代理程式私用位元組數、將統計資料匯出至 Azure AD。 |
> |  | **服務/計量/群組** |  |
> | 動作 | Microsoft.ADHybridHealthService/services/metrics/groups/read | 在指定了服務時，此 API 會取得計量資訊。<br>例如，此 API 可用來取得與下列項目相關的資訊：ADFederation 服務的外部網路帳戶鎖定、失敗要求總數、未處理的權杖要求數 (Proxy)、每秒權杖要求數等等。<br>ADDomain 服務的每秒 NTLM 驗證數、每秒 LDAP 成功繫結數、LDAP 繫結時間、LDAP 作用中執行緒、每秒 Kerberos 驗證數、ATQ 執行緒總數等。<br>Sync 服務的執行設定檔延遲、所建立的 TCP 連線數、Insights 代理程式私用位元組數、將統計資料匯出至 Azure AD。 |
> |  | **服務/計量/群組/平均** |  |
> | 動作 | Microsoft.ADHybridHealthService/services/metrics/groups/average/read | 在指定了服務時，此 API 會取得指定服務的計量平均值。<br>例如，此 API 可用來取得與下列項目相關的資訊：ADFederation 服務的外部網路帳戶鎖定、失敗要求總數、未處理的權杖要求數 (Proxy)、每秒權杖要求數等等。<br>ADDomain 服務的每秒 NTLM 驗證數、每秒 LDAP 成功繫結數、LDAP 繫結時間、LDAP 作用中執行緒、每秒 Kerberos 驗證數、ATQ 執行緒總數等。<br>Sync 服務的執行設定檔延遲、所建立的 TCP 連線數、Insights 代理程式私用位元組數、將統計資料匯出至 Azure AD。 |
> |  | **服務/計量/群組/總和** |  |
> | 動作 | Microsoft.ADHybridHealthService/services/metrics/groups/sum/read | 在指定了服務時，此 API 會取得指定服務計量的彙總檢視。<br>例如，此 API 可用來取得與下列項目相關的資訊：ADFederation 服務的外部網路帳戶鎖定、失敗要求總數、未處理的權杖要求數 (Proxy)、每秒權杖要求數等等。<br>ADDomain 服務的每秒 NTLM 驗證數、每秒 LDAP 成功繫結數、LDAP 繫結時間、LDAP 作用中執行緒、每秒 Kerberos 驗證數、ATQ 執行緒總數等。<br>Sync 服務的執行設定檔延遲、所建立的 TCP 連線數、Insights 代理程式私用位元組數、將統計資料匯出至 Azure AD。 |
> |  | **服務/monitoringconfiguration** |  |
> | 動作 | Microsoft.ADHybridHealthService/services/monitoringconfiguration/write | 新增或更新服務的監視組態。 |
> |  | **服務/monitoringconfigurations** |  |
> | 動作 | Microsoft.ADHybridHealthService/services/monitoringconfigurations/read | 取得指定服務的監視組態。 |
> | 動作 | Microsoft.ADHybridHealthService/services/monitoringconfigurations/write | 新增或更新服務的監視組態。 |
> |  | **服務/premiumcheck** |  |
> | 動作 | Microsoft.ADHybridHealthService/services/premiumcheck/read | 此 API 會取得進階租用戶所有已上架服務的清單。 |
> |  | **服務/報表** |  |
> | 動作 | ADHybridHealthService/services/reports/generateBlobUri/action | 產生具風險的 IP 報告，並傳回指向它的 URI。 |
> |  | **服務/報表/blobUris** |  |
> | 動作 | ADHybridHealthService/services/reports/blobUris/read | 取得過去7天內所有具風險的 IP 報告 Uri。 |
> |  | **服務/報表/詳細資料** |  |
> | 動作 | Microsoft.ADHybridHealthService/services/reports/details/read | 取得過去 7 天排行前 50 名有不正確密碼錯誤的使用者報告 |
> |  | **服務/servicemembers** |  |
> | 動作 | Microsoft.ADHybridHealthService/services/servicemembers/read | 讀取服務中的伺服器執行個體。 |
> | 動作 | Microsoft.ADHybridHealthService/services/servicemembers/delete | 刪除服務中的伺服器執行個體。 |
> |  | **服務/servicemembers/警示** |  |
> | 動作 | Microsoft.ADHybridHealthService/services/servicemembers/alerts/read | 讀取伺服器的警示。 |
> |  | **服務/servicemembers/認證** |  |
> | 動作 | Microsoft.ADHybridHealthService/services/servicemembers/credentials/read | 在伺服器註冊期間，會呼叫此 API 來取得用於將新伺服器上架的認證。 |
> |  | **services/servicemembers/datafreshness** |  |
> | 動作 | Microsoft.ADHybridHealthService/services/servicemembers/datafreshness/read | 針對指定的伺服器，此 API 會取得伺服器所要上傳的資料類型清單，以及每個上傳的最新時間。 |
> |  | **services/servicemembers/exportstatus** |  |
> | 動作 | Microsoft.ADHybridHealthService/services/servicemembers/exportstatus/read | 取得指定 Sync 服務的 Sync 匯出錯誤。 |
> |  | **服務/servicemembers/計量** |  |
> | 動作 | ADHybridHealthService/services/servicemembers/計量/讀取 | 取得連接器的清單，以及指定服務和服務成員的執行設定檔名稱。 |
> |  | **服務/servicemembers/計量/群組** |  |
> | 動作 | Microsoft.ADHybridHealthService/services/servicemembers/metrics/groups/read | 在指定了服務時，此 API 會取得計量資訊。<br>例如，此 API 可用來取得與下列項目相關的資訊：ADFederation 服務的外部網路帳戶鎖定、失敗要求總數、未處理的權杖要求數 (Proxy)、每秒權杖要求數等等。<br>ADDomain 服務的每秒 NTLM 驗證數、每秒 LDAP 成功繫結數、LDAP 繫結時間、LDAP 作用中執行緒、每秒 Kerberos 驗證數、ATQ 執行緒總數等。<br>Sync 服務的執行設定檔延遲、所建立的 TCP 連線數、Insights 代理程式私用位元組數、將統計資料匯出至 Azure AD。 |
> |  | **services/servicemembers/serviceconfiguration.cscfg** |  |
> | 動作 | Microsoft.ADHybridHealthService/services/servicemembers/serviceconfiguration/read | 取得指定租用戶的服務組態。 |
> |  | **服務/tenantwhitelisting** |  |
> | 動作 | Microsoft.ADHybridHealthService/services/tenantwhitelisting/read | 取得指定租用戶的功能允許清單狀態。 |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

Azure 服務： [Azure Advisor](../advisor/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.Advisor/generateRecommendations/action | 取得產生建議狀態 |
> | 動作 | Microsoft.Advisor/register/action | 針對 Microsoft Advisor 註冊訂用帳戶 |
> | 動作 | Microsoft.Advisor/unregister/action | 為 Microsoft Advisor 取消註冊訂用帳戶 |
> |  | **配置** |  |
> | 動作 | Microsoft.Advisor/configurations/read | 取得組態 |
> | 動作 | Microsoft.Advisor/configurations/write | 建立/更新設定 |
> |  | **generateRecommendations** |  |
> | 動作 | Microsoft.Advisor/generateRecommendations/read | 取得產生建議狀態 |
> |  | **metadata** |  |
> | 動作 | Microsoft Advisor/中繼資料/讀取 | 取得中繼資料 |
> |  | **業務** |  |
> | 動作 | Microsoft.Advisor/operations/read | 取得 Microsoft Advisor 的作業 |
> |  | **相關** |  |
> | 動作 | Microsoft.Advisor/recommendations/read | 讀取建議 |
> | 動作 | Microsoft.Advisor/recommendations/available/action | Microsoft Advisor 提供新的建議 |
> |  | **建議/隱藏** |  |
> | 動作 | Microsoft.Advisor/recommendations/suppressions/read | 取得隱藏項目 |
> | 動作 | Microsoft.Advisor/recommendations/suppressions/write | 建立/更新隱藏項目 |
> | 動作 | Microsoft.Advisor/recommendations/suppressions/delete | 刪除隱藏項目 |
> |  | **行為** |  |
> | 動作 | Microsoft.Advisor/suppressions/read | 取得隱藏項目 |
> | 動作 | Microsoft.Advisor/suppressions/write | 建立/更新隱藏項目 |
> | 動作 | Microsoft.Advisor/suppressions/delete | 刪除隱藏項目 |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

Azure 服務： [Azure 監視器](../azure-monitor/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.AlertsManagement/register/action | 向 Microsoft 警示管理註冊訂用帳戶 |
> |  | **actionRules** |  |
> | 動作 | Microsoft.AlertsManagement/actionRules/read | 取得輸入篩選的所有動作規則。 |
> | 動作 | Microsoft.AlertsManagement/actionRules/write | 在指定的訂用帳戶中建立或更新動作規則 |
> | 動作 | Microsoft.AlertsManagement/actionRules/delete | 在指定的訂用帳戶中刪除動作規則。 |
> |  | **alerts** |  |
> | 動作 | Microsoft.AlertsManagement/alerts/read | 取得輸入篩選的所有警示。 |
> | 動作 | Microsoft.AlertsManagement/alerts/changestate/action | 變更警示的狀態。 |
> |  | **警示/診斷** |  |
> | 動作 | Microsoft.AlertsManagement/alerts/diagnostics/read | 取得警示的所有診斷 |
> |  | **警示/歷程記錄** |  |
> | 動作 | Microsoft.AlertsManagement/alerts/history/read | 取得警示的歷程記錄 |
> |  | **alertsList** |  |
> | 動作 | Microsoft.AlertsManagement/alertsList/read | 取得訂用帳戶之間輸入篩選條件的所有警示 |
> |  | **alertsMetaData** |  |
> | 動作 | Microsoft.alertsmanagement/alertsMetaData/read | 取得輸入參數的警示中繼資料。 |
> |  | **alertsSummary** |  |
> | 動作 | Microsoft.AlertsManagement/alertsSummary/read | 取得警示的摘要 |
> |  | **alertsSummaryList** |  |
> | 動作 | Microsoft.AlertsManagement/alertsSummaryList/read | 取得訂用帳戶之間警示的摘要 |
> |  | **作業** |  |
> | 動作 | Microsoft.AlertsManagement/Operations/read | 讀取已提供的作業 |
> |  | **smartDetectorAlertRules** |  |
> | 動作 | Microsoft.alertsmanagement/smartDetectorAlertRules/write | 在指定的訂用帳戶中建立或更新智慧偵測器警示規則 |
> | 動作 | Microsoft.alertsmanagement/smartDetectorAlertRules/read | 取得輸入篩選的所有智慧偵測器警示規則 |
> | 動作 | Microsoft.alertsmanagement/smartDetectorAlertRules/delete | 刪除指定訂用帳戶中的智慧型偵測器警示規則 |
> |  | **smartGroups** |  |
> | 動作 | Microsoft.AlertsManagement/smartGroups/read | 取得輸入篩選的所有智慧群組 |
> | 動作 | Microsoft.AlertsManagement/smartGroups/changestate/action | 變更智慧群組的狀態 |
> |  | **smartGroups/歷程記錄** |  |
> | 動作 | Microsoft.AlertsManagement/smartGroups/history/read | 取得智慧群組的歷程記錄 |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

Azure 服務： [azure Analysis Services](../analysis-services/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.AnalysisServices/register/action | 註冊 Analysis Services 資源提供者。 |
> |  | **堆放** |  |
> | 動作 | Microsoft.AnalysisServices/locations/checkNameAvailability/action | 確認給定的 Analysis Server 名稱有效，且並非使用中。 |
> |  | **位置/operationresults** |  |
> | 動作 | Microsoft.AnalysisServices/locations/operationresults/read | 擷取指定作業結果的資訊。 |
> |  | **位置/operationstatuses** |  |
> | 動作 | Microsoft.AnalysisServices/locations/operationstatuses/read | 擷取指定作業狀態的資訊。 |
> |  | **業務** |  |
> | 動作 | Microsoft.AnalysisServices/operations/read | 擷取作業的資訊 |
> |  | **伺服器** |  |
> | 動作 | Microsoft.AnalysisServices/servers/read | 擷取所指定 Analysis Server 的資訊。 |
> | 動作 | Microsoft.AnalysisServices/servers/write | 建立或更新所指定的 Analysis Server。 |
> | 動作 | Microsoft.AnalysisServices/servers/delete | 刪除 Analysis Server。 |
> | 動作 | Microsoft.AnalysisServices/servers/suspend/action | 暫止 Analysis Server。 |
> | 動作 | Microsoft.AnalysisServices/servers/resume/action | 繼續 Analysis Server。 |
> | 動作 | Microsoft.AnalysisServices/servers/listGatewayStatus/action | 列出與伺服器相關聯的閘道狀態。 |
> |  | **伺服器/sku** |  |
> | 動作 | Microsoft.AnalysisServices/servers/skus/read | 擷取伺服器的可用 SKU 資訊 |
> |  | **sku** |  |
> | 動作 | Microsoft.AnalysisServices/skus/read | 擷取 SKU 的資訊 |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

Azure 服務： [API 管理](../api-management/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.ApiManagement/register/action | 針對 Microsoft.ApiManagement 資源提供者註冊訂用帳戶 |
> | 動作 | Microsoft.ApiManagement/unregister/action | 針對 Microsoft.ApiManagement 資源提供者取消註冊訂用帳戶 |
> |  | **checkNameAvailability** |  |
> | 動作 | Microsoft.ApiManagement/checkNameAvailability/read | 檢查所提供的服務名稱是否可用 |
> |  | **業務** |  |
> | 動作 | Microsoft.ApiManagement/operations/read | 讀取可供 Microsoft.ApiManagement 資源使用的所有 API 作業 |
> |  | **透視** |  |
> | 動作 | Microsoft.ApiManagement/reports/read | 取得依時間週期、地理區域、開發人員、產品、API、作業、訂用帳戶和依需求彙總的報告。 |
> |  | **service** |  |
> | 動作 | Microsoft.ApiManagement/service/write | 建立或更新 API 管理服務實例 |
> | 動作 | Microsoft.ApiManagement/service/read | 讀取 API 管理服務執行個體的中繼資料 |
> | 動作 | Microsoft.ApiManagement/service/delete | 刪除 API 管理服務執行個體 |
> | 動作 | Microsoft.ApiManagement/service/updatehostname/action | 設定、更新或移除 API 管理服務的自訂網域名稱 |
> | 動作 | Microsoft.ApiManagement/service/updatecertificate/action | 上傳 API 管理服務的 SSL 憑證 |
> | 動作 | Microsoft.ApiManagement/service/backup/action | 將 API 管理服務備份到使用者所提供之儲存體帳戶中的指定容器 |
> | 動作 | Microsoft.ApiManagement/service/restore/action | 從使用者所提供之儲存體帳戶中的指定容器來還原 API 管理服務 |
> | 動作 | Microsoft.ApiManagement/service/managedeployments/action | 變更 SKU/單位、新增/移除 API 管理服務的區域部署 |
> | 動作 | Microsoft.ApiManagement/service/getssotoken/action | 取得 SSO 權杖，以供用來登入 API 管理服務舊版入口網站 (登入身分為系統管理員) |
> | 動作 | Microsoft.ApiManagement/service/applynetworkconfigurationupdates/action | 更新虛擬網路中執行的 Microsoft.ApiManagement 資源，以挑選更新後的網路設定。 |
> | 動作 | Microsoft.ApiManagement/service/users/action | 註冊新的使用者 |
> | 動作 | Microsoft.ApiManagement/service/notifications/action | 將通知傳送給指定的使用者 |
> | 動作 | ApiManagement/服務/閘道/動作 | 抓取閘道設定。 或更新閘道的心跳。 |
> |  | **服務/api** |  |
> | 動作 | Microsoft.ApiManagement/service/apis/read | 列出 API 管理服務實例的所有 Api。 或取得其識別碼所指定之 API 的詳細資料。 |
> | 動作 | Microsoft.ApiManagement/service/apis/write | 建立新的或更新 API 管理服務實例的現有指定 API。 或會更新 API 管理服務實例的指定 API。 |
> | 動作 | Microsoft.ApiManagement/service/apis/delete | 刪除 API 管理服務實例的指定 API。 |
> |  | **服務/api/診斷** |  |
> | 動作 | Microsoft.ApiManagement/service/apis/diagnostics/read | 列出 API 的所有診斷。 或取得其識別碼所指定之 API 的診斷詳細資料。 |
> | 動作 | Microsoft.ApiManagement/service/apis/diagnostics/write | 為 API 建立新的診斷，或更新現有的診斷。 或更新其識別碼所指定之 API 的診斷詳細資料。 |
> | 動作 | Microsoft.ApiManagement/service/apis/diagnostics/delete | 從 API 刪除指定的診斷。 |
> |  | **服務/api/問題** |  |
> | 動作 | Microsoft.ApiManagement/service/apis/issues/read | 列出與指定之 API 相關聯的所有問題。 或取得其識別碼所指定之 API 的問題詳細資料。 |
> | 動作 | Microsoft.ApiManagement/service/apis/issues/write | 為 API 建立新的問題，或更新現有的問題。 或更新 API 的現有問題。 |
> | 動作 | Microsoft.ApiManagement/service/apis/issues/delete | 從 API 刪除指定的問題。 |
> |  | **服務/api/問題/附件** |  |
> | 動作 | Microsoft.ApiManagement/service/apis/issues/attachments/read | 列出與指定之 API 相關聯之問題的所有附件。 或取得其識別碼所指定之 API 的問題附件詳細資料。 |
> | 動作 | Microsoft.ApiManagement/service/apis/issues/attachments/write | 為 API 中的問題建立新的附件，或更新現有的附加檔案。 |
> | 動作 | Microsoft.ApiManagement/service/apis/issues/attachments/delete | 從問題中刪除指定的批註。 |
> |  | **服務/api/問題/批註** |  |
> | 動作 | Microsoft.ApiManagement/service/apis/issues/comments/read | 列出與指定之 API 相關聯之問題的所有批註。 或取得其識別碼所指定之 API 的問題批註詳細資料。 |
> | 動作 | Microsoft.ApiManagement/service/apis/issues/comments/write | 為 API 中的問題建立新的批註，或更新現有的批註。 |
> | 動作 | Microsoft.ApiManagement/service/apis/issues/comments/delete | 從問題中刪除指定的批註。 |
> |  | **服務/api/作業** |  |
> | 動作 | Microsoft.ApiManagement/service/apis/operations/read | 列出指定之 API 的作業集合。 或取得其識別碼所指定之 API 作業的詳細資料。 |
> | 動作 | Microsoft.ApiManagement/service/apis/operations/write | 在 API 中建立新的作業，或更新現有的作業。 或會更新其識別碼所指定之 API 中的作業詳細資料。 |
> | 動作 | Microsoft.ApiManagement/service/apis/operations/delete | 在 API 中刪除指定的作業。 |
> |  | **服務/api/作業/原則** |  |
> | 動作 | Microsoft.ApiManagement/service/apis/operations/policies/read | 取得 API 作業層級的原則設定清單。 或取得 API 作業層級的原則設定。 |
> | 動作 | Microsoft.ApiManagement/service/apis/operations/policies/write | 建立或更新 API 作業層級的原則設定。 |
> | 動作 | Microsoft.ApiManagement/service/apis/operations/policies/delete | 刪除 Api 作業的原則設定。 |
> |  | **服務/api/作業/原則** |  |
> | 動作 | Microsoft.ApiManagement/service/apis/operations/policy/read | 取得作業層級的原則設定 |
> | 動作 | Microsoft.ApiManagement/service/apis/operations/policy/write | 在作業層級建立原則設定 |
> | 動作 | Microsoft.ApiManagement/service/apis/operations/policy/delete | 刪除作業層級的原則設定 |
> |  | **服務/api/作業/標記** |  |
> | 動作 | Microsoft.ApiManagement/service/apis/operations/tags/read | 列出與作業相關聯的所有標記。 或取得與作業相關聯的標記。 |
> | 動作 | Microsoft.ApiManagement/service/apis/operations/tags/write | 將標記指派給作業。 |
> | 動作 | Microsoft.ApiManagement/service/apis/operations/tags/delete | 卸離作業的標記。 |
> |  | **服務/api/operationsByTags** |  |
> | 動作 | Microsoft.ApiManagement/service/apis/operationsByTags/read | 列出與標記相關聯的作業集合。 |
> |  | **服務/api/原則** |  |
> | 動作 | Microsoft.ApiManagement/service/apis/policies/read | 取得 API 層級的原則設定。 或取得 API 層級的原則設定。 |
> | 動作 | Microsoft.ApiManagement/service/apis/policies/write | 建立或更新 API 的原則設定。 |
> | 動作 | Microsoft.ApiManagement/service/apis/policies/delete | 刪除 Api 的原則設定。 |
> |  | **服務/api/原則** |  |
> | 動作 | Microsoft.ApiManagement/service/apis/policy/read | 取得 API 層級的原則設定 |
> | 動作 | Microsoft.ApiManagement/service/apis/policy/write | 在 API 層級建立原則設定 |
> | 動作 | Microsoft.ApiManagement/service/apis/policy/delete | 刪除 API 層級的原則設定 |
> |  | **服務/api/產品** |  |
> | 動作 | Microsoft.ApiManagement/service/apis/products/read | 列出 API 所屬的所有產品。 |
> |  | **服務/api/版本** |  |
> | 動作 | Microsoft.ApiManagement/service/apis/releases/read | 列出 API 的所有版本。<br>API 版本會在建立 API 的最新版本時建立。<br>發行也用來回複先前的修訂版本。<br>結果將會分頁，並可由 $top 和 $skip 參數加以限制。<br>或傳回 API 版本的詳細資料。 |
> | 動作 | Microsoft.ApiManagement/service/apis/releases/delete | 移除 API 的所有版本，或刪除 API 中的指定版本。 |
> | 動作 | Microsoft.ApiManagement/service/apis/releases/write | 為 API 建立新的版本。 或會更新其識別碼所指定之 API 版本的詳細資料。 |
> |  | **服務/api/修訂** |  |
> | 動作 | Microsoft.ApiManagement/service/apis/revisions/read | 列出 API 的所有修訂。 |
> | 動作 | Microsoft.ApiManagement/service/apis/revisions/delete | 移除 API 的所有修訂 |
> |  | **服務/api/架構** |  |
> | 動作 | Microsoft.ApiManagement/service/apis/schemas/read | 取得 API 層級的架構設定。 或取得 API 層級的架構設定。 |
> | 動作 | Microsoft.ApiManagement/service/apis/schemas/write | 建立或更新 API 的架構設定。 |
> | 動作 | Microsoft.ApiManagement/service/apis/schemas/delete | 刪除 Api 的架構設定。 |
> |  | **服務/api/tagDescriptions** |  |
> | 動作 | Microsoft.ApiManagement/service/apis/tagDescriptions/read | 列出 API 範圍內的所有標記描述。 類似于 swagger-tagDescription 的模型是在 API 層級上定義，但是標籤可能會指派給作業，或在 API 範圍中取得標記描述 |
> | 動作 | Microsoft.ApiManagement/service/apis/tagDescriptions/write | 建立/更新 Api 範圍內的標記描述。 |
> | 動作 | Microsoft.ApiManagement/service/apis/tagDescriptions/delete | 刪除 Api 的標記描述。 |
> |  | **服務/api/標記** |  |
> | 動作 | Microsoft.ApiManagement/service/apis/tags/read | 列出與 API 相關聯的所有標記。 或取得與 API 相關聯的標記。 |
> | 動作 | Microsoft.ApiManagement/service/apis/tags/write | 將標記指派給 Api。 |
> | 動作 | Microsoft.ApiManagement/service/apis/tags/delete | 從 Api 卸離標記。 |
> |  | **服務/apisByTags** |  |
> | 動作 | Microsoft.ApiManagement/service/apisByTags/read | 列出與標記相關聯的 api 集合。 |
> |  | **服務/apiVersionSets** |  |
> | 動作 | Microsoft.ApiManagement/service/apiVersionSets/read | 列出指定之服務實例中的 API 版本集集合。 或取得其識別碼所指定之 Api 版本集的詳細資料。 |
> | 動作 | Microsoft.ApiManagement/service/apiVersionSets/write | 建立或更新 Api 版本集。 或會更新其識別碼所指定之 Api VersionSet 的詳細資料。 |
> | 動作 | Microsoft.ApiManagement/service/apiVersionSets/delete | 刪除特定的 Api 版本集。 |
> |  | **服務/apiVersionSets/版本** |  |
> | 動作 | Microsoft.ApiManagement/service/apiVersionSets/versions/read | 取得版本實體的清單 |
> |  | **服務/authorizationServers** |  |
> | 動作 | Microsoft.ApiManagement/service/authorizationServers/read | 列出在服務實例內定義的授權伺服器集合。 或取得沒有密碼之授權伺服器的詳細資料。 |
> | 動作 | Microsoft.ApiManagement/service/authorizationServers/write | 建立新的授權伺服器，或更新現有的授權伺服器。 或會更新其識別碼所指定之授權伺服器的詳細資料。 |
> | 動作 | Microsoft.ApiManagement/service/authorizationServers/delete | 刪除特定的授權伺服器實例。 |
> | 動作 | ApiManagement/service/authorizationServers/listSecrets/action | 取得授權伺服器的密碼。 |
> |  | **服務/後端** |  |
> | 動作 | Microsoft.ApiManagement/service/backends/read | 列出指定之服務實例中的後端集合。 或取得其識別碼所指定後端的詳細資料。 |
> | 動作 | Microsoft.ApiManagement/service/backends/write | 建立或更新後端。 或更新現有的後端。 |
> | 動作 | Microsoft.ApiManagement/service/backends/delete | 刪除指定的後端。 |
> | 動作 | Microsoft.ApiManagement/service/backends/reconnect/action | 通知 APIM proxy 在指定的超時時間之後，建立與後端的新連接。 如果沒有指定 timeout，則會使用2分鐘的 timeout。 |
> |  | **服務/快取** |  |
> | 動作 | ApiManagement/服務/快取/讀取 | 列出指定服務實例中所有外部快取的集合。 或取得其識別碼所指定之快取的詳細資料。 |
> | 動作 | ApiManagement/service/cache/write | 建立或更新要在 Api 管理實例中使用的外部快取。 或會更新其識別碼所指定之快取的詳細資料。 |
> | 動作 | ApiManagement/service/cache/delete | 刪除特定的快取。 |
> |  | **服務/憑證** |  |
> | 動作 | Microsoft.ApiManagement/service/certificates/read | 列出指定服務實例中所有憑證的集合。 或取得其識別碼所指定之憑證的詳細資料。 |
> | 動作 | Microsoft.ApiManagement/service/certificates/write | 建立或更新用來向後端驗證的憑證。 |
> | 動作 | Microsoft.ApiManagement/service/certificates/delete | 刪除特定的憑證。 |
> |  | **服務/contentTypes** |  |
> | 動作 | Microsoft.ApiManagement/service/contentTypes/read | 傳回內容類型清單 |
> |  | **service/contentTypes/contentItems** |  |
> | 動作 | Microsoft.ApiManagement/service/contentTypes/contentItems/read | 傳回內容項目清單或傳回內容項目詳細資料 |
> | 動作 | Microsoft.ApiManagement/service/contentTypes/contentItems/write | 建立新的內容項目或更新指定的內容項目 |
> | 動作 | Microsoft.ApiManagement/service/contentTypes/contentItems/delete | 移除指定的內容項目。 |
> |  | **服務/診斷** |  |
> | 動作 | Microsoft.ApiManagement/service/diagnostics/read | 列出 API 管理服務實例的所有診斷。 或取得其識別碼所指定之診斷的詳細資料。 |
> | 動作 | Microsoft.ApiManagement/service/diagnostics/write | 建立新的診斷，或更新現有的診斷。 或會更新其識別碼所指定之診斷的詳細資料。 |
> | 動作 | Microsoft.ApiManagement/service/diagnostics/delete | 刪除指定的診斷。 |
> |  | **服務/閘道** |  |
> | 動作 | ApiManagement/服務/閘道/讀取 | 列出已向服務實例註冊的閘道集合。 或取得其識別碼所指定之閘道的詳細資料。 |
> | 動作 | ApiManagement/服務/閘道/寫入 | 建立或更新要在 Api 管理實例中使用的閘道。 或會更新其識別碼所指定之閘道的詳細資料。 |
> | 動作 | ApiManagement/服務/閘道/刪除 | 刪除特定的閘道。 |
> | 動作 | ApiManagement/服務/閘道/listKeys/動作 | 抓取閘道金鑰。 |
> | 動作 | ApiManagement/服務/閘道/regenerateKey/動作 | 重新產生指定的閘道金鑰 invalidationg 使用它建立的任何權杖。 |
> | 動作 | ApiManagement/服務/閘道/generateToken/動作 | 取得閘道的共用存取授權權杖。 |
> |  | **服務/閘道/api** |  |
> | 動作 | ApiManagement/服務/閘道/api/讀取 | 列出與閘道相關聯的 Api 集合。 |
> | 動作 | ApiManagement/服務/閘道/api/寫入 | 將 API 新增至指定的閘道。 |
> | 動作 | ApiManagement/服務/閘道/api/刪除 | 從指定的閘道刪除指定的 API。 |
> |  | **服務/閘道/hostnameConfigurations** |  |
> | 動作 | ApiManagement/服務/閘道/hostnameConfigurations/讀取 | 列出指定之閘道的主機名稱設定集合。 |
> |  | **服務/群組** |  |
> | 動作 | Microsoft.ApiManagement/service/groups/read | 列出服務實例中定義的群組集合。 或取得其識別碼所指定之群組的詳細資料。 |
> | 動作 | Microsoft.ApiManagement/service/groups/write | 建立或更新群組。 或會更新其識別碼所指定之群組的詳細資料。 |
> | 動作 | Microsoft.ApiManagement/service/groups/delete | 刪除 API 管理服務實例的特定群組。 |
> |  | **服務/群組/使用者** |  |
> | 動作 | Microsoft.ApiManagement/service/groups/users/read | 列出與群組相關聯之使用者實體的集合。 |
> | 動作 | Microsoft.ApiManagement/service/groups/users/write | 將現有使用者新增至現有群組 |
> | 動作 | Microsoft.ApiManagement/service/groups/users/delete | 從現有的群組移除現有的使用者。 |
> |  | **服務/identityProviders** |  |
> | 動作 | Microsoft.ApiManagement/service/identityProviders/read | 列出在指定的服務實例中設定的識別提供者集合。 或取得身分識別提供者的設定詳細資料，而不需要秘密。 |
> | 動作 | Microsoft.ApiManagement/service/identityProviders/write | 建立或更新 IdentityProvider 設定。 或更新現有的 IdentityProvider 設定。 |
> | 動作 | Microsoft.ApiManagement/service/identityProviders/delete | 刪除指定的識別提供者設定。 |
> | 動作 | ApiManagement/service/identityProviders/listSecrets/action | 取得識別提供者密碼。 |
> |  | **服務/問題** |  |
> | 動作 | ApiManagement/service/問題/讀取 | 列出指定的服務實例中的問題集合。 或取得 API 管理問題詳細資料 |
> |  | **服務/位置/networkstatus** |  |
> | 動作 | Microsoft.ApiManagement/service/locations/networkstatus/read | 取得位置中服務相依資源的網路存取狀態。 |
> |  | **服務/記錄器** |  |
> | 動作 | Microsoft.ApiManagement/service/loggers/read | 列出指定之服務實例中的記錄器集合。 或取得其識別碼所指定之記錄器的詳細資料。 |
> | 動作 | Microsoft.ApiManagement/service/loggers/write | 建立或更新記錄器。 或更新現有的記錄器。 |
> | 動作 | Microsoft.ApiManagement/service/loggers/delete | 刪除指定的記錄器。 |
> |  | **服務/namedValues** |  |
> | 動作 | ApiManagement/service/namedValues/read | 列出在服務實例內定義的已命名值集合。 或取得其識別碼所指定之已命名值的詳細資料。 |
> | 動作 | ApiManagement/service/namedValues/write | 建立或更新名為的值。 或會更新特定的已命名值。 |
> | 動作 | ApiManagement/service/namedValues/delete | 從 API 管理服務實例中刪除特定的已命名值。 |
> | 動作 | ApiManagement/service/namedValues/listValue/action | 取得其識別碼所指定之已命名值的密碼。 |
> |  | **服務/networkstatus** |  |
> | 動作 | Microsoft.ApiManagement/service/networkstatus/read | 取得服務相依資源的網路存取狀態。 |
> |  | **服務/通知** |  |
> | 動作 | Microsoft.ApiManagement/service/notifications/read | 列出服務實例中定義的屬性集合。 或取得其識別碼所指定之通知的詳細資料。 |
> | 動作 | Microsoft.ApiManagement/service/notifications/write | 建立或更新 API 管理發行者通知。 |
> |  | **服務/通知/recipientEmails** |  |
> | 動作 | Microsoft.ApiManagement/service/notifications/recipientEmails/read | 取得訂閱通知的通知收件者電子郵件清單。 |
> | 動作 | Microsoft.ApiManagement/service/notifications/recipientEmails/write | 將電子郵件地址新增至通知的收件者清單。 |
> | 動作 | Microsoft.ApiManagement/service/notifications/recipientEmails/delete | 從通知清單中移除電子郵件。 |
> |  | **服務/通知/recipientUsers** |  |
> | 動作 | Microsoft.ApiManagement/service/notifications/recipientUsers/read | 取得訂閱通知的通知收件者使用者清單。 |
> | 動作 | Microsoft.ApiManagement/service/notifications/recipientUsers/write | 將 [API 管理] 使用者新增至通知的收件者清單。 |
> | 動作 | Microsoft.ApiManagement/service/notifications/recipientUsers/delete | 從通知清單中移除 [API 管理] 使用者。 |
> |  | **服務/openidConnectProviders** |  |
> | 動作 | Microsoft.ApiManagement/service/openidConnectProviders/read | 所有 OpenId Connect 提供者的清單。 或取得沒有秘密的特定 OpenID Connect 提供者。 |
> | 動作 | Microsoft.ApiManagement/service/openidConnectProviders/write | 建立或更新 OpenID Connect 提供者。 或會更新特定的 OpenID Connect 提供者。 |
> | 動作 | Microsoft.ApiManagement/service/openidConnectProviders/delete | 刪除 API 管理服務實例的特定 OpenID Connect 提供者。 |
> | 動作 | ApiManagement/service/openidConnectProviders/listSecrets/action | 取得特定的 OpenID Connect 提供者密碼。 |
> |  | **服務/operationresults** |  |
> | 動作 | Microsoft.ApiManagement/service/operationresults/read | 取得長時間執行之作業的目前狀態 |
> |  | **服務/原則** |  |
> | 動作 | Microsoft.ApiManagement/service/policies/read | 列出 Api 管理服務的所有全域原則定義。 或取得 Api 管理服務的全域原則定義。 |
> | 動作 | Microsoft.ApiManagement/service/policies/write | 建立或更新 Api 管理服務的全域原則設定。 |
> | 動作 | Microsoft.ApiManagement/service/policies/delete | 刪除 Api 管理服務的全域原則設定。 |
> |  | **服務/原則** |  |
> | 動作 | ApiManagement/服務/原則/讀取 | 取得租使用者層級的原則設定 |
> | 動作 | ApiManagement/服務/原則/寫入 | 在租使用者層級建立原則設定 |
> | 動作 | ApiManagement/服務/原則/刪除 | 刪除租使用者層級的原則設定 |
> |  | **服務/policyDescriptions** |  |
> | 動作 | ApiManagement/service/policyDescriptions/read | 列出所有原則描述。 |
> |  | **服務/policySnippets** |  |
> | 動作 | Microsoft.ApiManagement/service/policySnippets/read | 列出所有原則程式碼片段。 |
> |  | **服務/portalsettings** |  |
> | 動作 | Microsoft.ApiManagement/service/portalsettings/read | 列出入口網站設定的集合。 或取得入口網站的登入設定，或取得入口網站的 [註冊設定] 或 [取得入口網站的委派設定]。 |
> | 動作 | Microsoft.ApiManagement/service/portalsettings/write | 更新登入設定。 或建立或更新登入設定。 或更新 [註冊設定] 或 [更新註冊設定] 或 [更新委派設定]。 或建立或更新委派設定。 |
> | 動作 | ApiManagement/service/portalsettings/listSecrets/action | 取得入口網站委派設定的驗證金鑰。 |
> |  | **服務/產品** |  |
> | 動作 | Microsoft.ApiManagement/service/products/read | 列出指定之服務實例中的產品集合。 或取得其識別碼所指定之產品的詳細資料。 |
> | 動作 | Microsoft.ApiManagement/service/products/write | 建立或更新產品。 或更新現有的產品詳細資料。 |
> | 動作 | Microsoft.ApiManagement/service/products/delete | 刪除產品。 |
> |  | **服務/產品/api** |  |
> | 動作 | Microsoft.ApiManagement/service/products/apis/read | 列出與產品相關聯的 Api 集合。 |
> | 動作 | Microsoft.ApiManagement/service/products/apis/write | 將 API 新增至指定的產品。 |
> | 動作 | Microsoft.ApiManagement/service/products/apis/delete | 從指定的產品刪除指定的 API。 |
> |  | **服務/產品/群組** |  |
> | 動作 | Microsoft.ApiManagement/service/products/groups/read | 列出與指定之產品相關聯的開發人員群組集合。 |
> | 動作 | Microsoft.ApiManagement/service/products/groups/write | 在指定的開發人員群組與指定的產品之間加入關聯。 |
> | 動作 | Microsoft.ApiManagement/service/products/groups/delete | 刪除指定之群組和產品之間的關聯。 |
> |  | **服務/產品/原則** |  |
> | 動作 | Microsoft.ApiManagement/service/products/policies/read | 取得產品層級的原則設定。 或取得產品層級的原則設定。 |
> | 動作 | Microsoft.ApiManagement/service/products/policies/write | 建立或更新產品的原則設定。 |
> | 動作 | Microsoft.ApiManagement/service/products/policies/delete | 刪除產品的原則設定。 |
> |  | **服務/產品/原則** |  |
> | 動作 | Microsoft.ApiManagement/service/products/policy/read | 取得產品層級的原則設定 |
> | 動作 | Microsoft.ApiManagement/service/products/policy/write | 在產品層級建立原則設定 |
> | 動作 | Microsoft.ApiManagement/service/products/policy/delete | 刪除產品層級的原則設定 |
> |  | **服務/產品/訂用帳戶** |  |
> | 動作 | Microsoft.ApiManagement/service/products/subscriptions/read | 列出指定之產品的訂閱集合。 |
> |  | **服務/產品/標記** |  |
> | 動作 | Microsoft.ApiManagement/service/products/tags/read | 列出與產品相關聯的所有標記。 或取得與產品相關聯的標記。 |
> | 動作 | Microsoft.ApiManagement/service/products/tags/write | 將標記指派給產品。 |
> | 動作 | Microsoft.ApiManagement/service/products/tags/delete | 卸離產品的標記。 |
> |  | **服務/productsByTags** |  |
> | 動作 | Microsoft.ApiManagement/service/productsByTags/read | 列出與標記相關聯的產品集合。 |
> |  | **服務/屬性** |  |
> | 動作 | Microsoft.ApiManagement/service/properties/read | 列出服務實例中定義的屬性集合。 或取得其識別碼所指定之屬性的詳細資料。 |
> | 動作 | Microsoft.ApiManagement/service/properties/write | 建立或更新屬性。 或會更新特定屬性。 |
> | 動作 | Microsoft.ApiManagement/service/properties/delete | 從 API 管理服務實例中刪除特定屬性。 |
> | 動作 | ApiManagement/service/properties/listSecrets/action | 取得其識別碼所指定之屬性的密碼。 |
> |  | **服務/配額** |  |
> | 動作 | Microsoft.ApiManagement/service/quotas/read | 取得配額的值 |
> | 動作 | Microsoft.ApiManagement/service/quotas/write | 設定配額計數器目前的值 |
> |  | **服務/配額/期間** |  |
> | 動作 | Microsoft.ApiManagement/service/quotas/periods/read | 取得期間的配額計數器值 |
> | 動作 | Microsoft.ApiManagement/service/quotas/periods/write | 設定配額計數器目前的值 |
> |  | **服務/區域** |  |
> | 動作 | ApiManagement/服務/區域/讀取 | 列出服務所在的所有 azure 區域。 |
> |  | **服務/報表** |  |
> | 動作 | Microsoft.ApiManagement/service/reports/read | 取得依時間週期彙總的報告、取得依地理區域彙總的報告，或取得依開發人員彙總的報告。<br>或者，取得依產品彙總的報告。<br>或者，取得依 API 彙總的報告、取得依作業彙總的報告，或取得依訂用帳戶彙總的報告。<br>或者，取得報告資料的要求 |
> |  | **服務/訂用帳戶** |  |
> | 動作 | Microsoft.ApiManagement/service/subscriptions/read | 列出 API 管理服務實例的所有訂用帳戶。 或取得指定的訂用帳戶實體（不含索引鍵）。 |
> | 動作 | Microsoft.ApiManagement/service/subscriptions/write | 建立或更新指定產品的指定使用者訂閱。 或會更新其識別碼所指定之訂用帳戶的詳細資料。 |
> | 動作 | Microsoft.ApiManagement/service/subscriptions/delete | 刪除指定的訂用帳戶。 |
> | 動作 | Microsoft.ApiManagement/service/subscriptions/regeneratePrimaryKey/action | 重新產生 API 管理服務實例現有訂用帳戶的主要金鑰。 |
> | 動作 | Microsoft.ApiManagement/service/subscriptions/regenerateSecondaryKey/action | 重新產生 API 管理服務實例之現有訂用帳戶的次要金鑰。 |
> | 動作 | ApiManagement/service/訂用帳戶/listSecrets/動作 | 取得指定的訂用帳戶金鑰。 |
> |  | **服務/tagResources** |  |
> | 動作 | Microsoft.ApiManagement/service/tagResources/read | 列出與標記相關聯的資源集合。 |
> |  | **服務/標記** |  |
> | 動作 | Microsoft.ApiManagement/service/tags/read | 列出服務實例中定義的標記集合。 或取得其識別碼所指定之標記的詳細資料。 |
> | 動作 | Microsoft.ApiManagement/service/tags/write | 建立標記。 或會更新其識別碼所指定之標記的詳細資料。 |
> | 動作 | Microsoft.ApiManagement/service/tags/delete | 刪除 API 管理服務實例的特定標記。 |
> |  | **服務/範本** |  |
> | 動作 | Microsoft.ApiManagement/service/templates/read | 取得所有電子郵件範本，或取得 API 管理電子郵件範本詳細資料 |
> | 動作 | Microsoft.ApiManagement/service/templates/write | 建立或更新 API 管理電子郵件範本，或更新 API 管理電子郵件範本 |
> | 動作 | Microsoft.ApiManagement/service/templates/delete | 重設預設的 API 管理電子郵件範本 |
> |  | **服務/租使用者** |  |
> | 動作 | Microsoft.ApiManagement/service/tenant/read | 取得 Api 管理服務的全域原則定義。 或取得租使用者存取訊號詳細資料 |
> | 動作 | Microsoft.ApiManagement/service/tenant/write | 設定租用戶的原則設定，或更新租用戶存取資訊詳細資料 |
> | 動作 | Microsoft.ApiManagement/service/tenant/delete | 移除租用戶的原則組態 |
> | 動作 | ApiManagement/服務/租使用者/listSecrets/動作 | 取得租用戶存取資訊的詳細資料 |
> | 動作 | Microsoft.ApiManagement/service/tenant/regeneratePrimaryKey/action | 重新產生主要存取金鑰 |
> | 動作 | Microsoft.ApiManagement/service/tenant/regenerateSecondaryKey/action | 重新產生次要存取金鑰 |
> | 動作 | Microsoft.ApiManagement/service/tenant/deploy/action | 執行部署工作，以將所指定 git 分支的變更套用至資料庫中的組態。 |
> | 動作 | Microsoft.ApiManagement/service/tenant/save/action | 在存放庫的指定分支中建立具有組態快照集的認可 |
> | 動作 | Microsoft.ApiManagement/service/tenant/validate/action | 驗證所指定 git 分支的變更 |
> |  | **服務/租使用者/operationResults** |  |
> | 動作 | Microsoft.ApiManagement/service/tenant/operationResults/read | 取得作業結果的清單，或取得特定作業的結果 |
> |  | **服務/租使用者/syncState** |  |
> | 動作 | Microsoft.ApiManagement/service/tenant/syncState/read | 取得上次 git 同步處理的狀態 |
> |  | **服務/使用者** |  |
> | 動作 | Microsoft.ApiManagement/service/users/read | 列出指定的服務實例中已註冊的使用者集合。 或取得其識別碼所指定之使用者的詳細資料。 |
> | 動作 | Microsoft.ApiManagement/service/users/write | 建立或更新使用者。 或會更新其識別碼所指定之使用者的詳細資料。 |
> | 動作 | Microsoft.ApiManagement/service/users/delete | 刪除特定的使用者。 |
> | 動作 | Microsoft.ApiManagement/service/users/generateSsoUrl/action | 抓取包含驗證權杖的重新導向 URL，以便將指定的使用者簽署到開發人員入口網站。 |
> | 動作 | Microsoft.ApiManagement/service/users/token/action | 取得使用者的共用存取授權 Token。 |
> |  | **服務/使用者/確認** |  |
> | 動作 | Microsoft.ApiManagement/service/users/confirmations/send/action | 傳送確認 |
> |  | **服務/使用者/群組** |  |
> | 動作 | Microsoft.ApiManagement/service/users/groups/read | 列出所有使用者群組。 |
> |  | **服務/使用者/身分識別** |  |
> | 動作 | ApiManagement/服務/使用者/身分識別/讀取 | 所有使用者身分識別的清單。 |
> |  | **服務/使用者/金鑰** |  |
> | 動作 | Microsoft.ApiManagement/service/users/keys/read | 取得與使用者相關聯的金鑰 |
> |  | **服務/使用者/訂用帳戶** |  |
> | 動作 | Microsoft.ApiManagement/service/users/subscriptions/read | 列出指定之使用者的訂閱集合。 |

## <a name="microsoftappconfiguration"></a>AppConfiguration

Azure 服務：核心

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | AppConfiguration/register/action | 註冊訂用帳戶以使用 Microsoft 應用程式組態。 |
> |  | **checkNameAvailability** |  |
> | 動作 | AppConfiguration/checkNameAvailability/read | 檢查資源名稱是否可供使用。 |
> |  | **configurationStores** |  |
> | 動作 | AppConfiguration/configurationStores/read | 取得指定之設定存放區的屬性，或列出指定的資源群組或訂用帳戶下的所有設定存放區。 |
> | 動作 | AppConfiguration/configurationStores/write | 使用指定的參數建立或更新設定存放區。 |
> | 動作 | AppConfiguration/configurationStores/delete | 刪除設定存放區。 |
> | 動作 | AppConfiguration/configurationStores/ListKeys/action | 列出指定之設定存放區的 API 金鑰。 |
> | 動作 | AppConfiguration/configurationStores/RegenerateKey/action | 重新產生指定之設定存放區的 API 金鑰。 |
> | 動作 | AppConfiguration/configurationStores/ListKeyValue/action | 列出指定之設定存放區的索引鍵/值。 |
> | 動作 | AppConfiguration/configurationStores/PrivateEndpointConnectionsApproval/action | 自動核准指定之設定存放區下的私人端點連線。 |
> |  | **configurationStores/eventGridFilters** |  |
> | 動作 | AppConfiguration/configurationStores/eventGridFilters/read | 取得指定設定存放區事件方格篩選器的屬性，或列出指定之設定存放區下的所有設定存放區事件方格篩選器。 |
> | 動作 | AppConfiguration/configurationStores/eventGridFilters/write | 使用指定的參數，建立或更新設定存放區事件方格篩選準則。 |
> | 動作 | AppConfiguration/configurationStores/eventGridFilters/delete | 刪除設定存放區事件方格篩選。 |
> |  | **configurationStores/privateEndpointConnectionProxies** |  |
> | 動作 | AppConfiguration/configurationStores/privateEndpointConnectionProxies/validate/action | 驗證指定之設定存放區下的私用端點連接 proxy。 |
> | 動作 | AppConfiguration/configurationStores/privateEndpointConnectionProxies/read | 取得指定設定存放區下的私人端點連線 proxy。 |
> | 動作 | AppConfiguration/configurationStores/privateEndpointConnectionProxies/write | 建立或更新指定之設定存放區下的私人端點連接 proxy。 |
> | 動作 | AppConfiguration/configurationStores/privateEndpointConnectionProxies/delete | 刪除指定之設定存放區下的私人端點連線 proxy。 |
> |  | **configurationStores/privateEndpointConnections** |  |
> | 動作 | AppConfiguration/configurationStores/privateEndpointConnections/read | 取得私人端點連線，或列出指定之設定存放區下的私人端點連線。 |
> | 動作 | AppConfiguration/configurationStores/privateEndpointConnections/write | 核准或拒絕指定之設定存放區下的私人端點連線。 |
> | 動作 | AppConfiguration/configurationStores/privateEndpointConnections/delete | 刪除指定之設定存放區下的私人端點連線。 |
> |  | **configurationStores/privateLinkResources** |  |
> | 動作 | AppConfiguration/configurationStores/privateLinkResources/read | 列出指定之設定存放區下的所有私人連結資源。 |
> |  | **configurationStores/providers/Microsoft Insights/diagnosticSettings** |  |
> | 動作 | AppConfiguration/configurationStores/provider/Microsoft Insights/diagnosticSettings/read | 讀取設定存放區的所有診斷設定值。 |
> | 動作 | AppConfiguration/configurationStores/provider/Microsoft Insights/diagnosticSettings/write | 適用于 Microsoft 應用程式組態的寫入/覆寫診斷設定。 |
> |  | **configurationStores/providers/Microsoft Insights/Metricdefinitions.listasync** |  |
> | 動作 | AppConfiguration/configurationStores/provider/Microsoft Insights/Metricdefinitions.listasync/read | 取得 Microsoft 應用程式組態的所有度量定義。 |
> |  | **configurationStores/syncTasks** |  |
> | 動作 | AppConfiguration/configurationStores/syncTasks/read | 取得指定設定存放區同步工作的屬性，或列出指定之設定存放區下的所有設定存放區同步處理工作。 |
> | 動作 | AppConfiguration/configurationStores/syncTasks/write | 使用指定的參數建立或更新設定存放區同步工作。 |
> | 動作 | AppConfiguration/configurationStores/syncTasks/delete | 刪除設定存放區同步工作。 |
> |  | **位置/operationsStatus** |  |
> | 動作 | AppConfiguration/位置/operationsStatus/讀取 | 取得作業的狀態。 |
> |  | **業務** |  |
> | 動作 | AppConfiguration/作業/讀取 | 列出 Microsoft 應用程式組態支援的所有作業。 |
> |  | **configurationStores/配置** |  |
> | DataAction | AppConfiguration/configurationStores/配置/read | 從設定存放區讀取索引鍵/值。 |
> | DataAction | AppConfiguration/configurationStores/配置/write | 建立或更新設定存放區中的索引鍵/值。 |
> | DataAction | AppConfiguration/configurationStores/配置/delete | 從設定存放區刪除現有的索引鍵/值。 |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

Azure 服務： [Azure Resource Manager](../azure-resource-manager/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.Authorization/elevateAccess/action | 對呼叫者授與租用戶範圍的使用者存取系統管理員存取權 |
> |  | **classicAdministrators** |  |
> | 動作 | Microsoft.Authorization/classicAdministrators/read | 讀取訂用帳戶的系統管理員。 |
> | 動作 | Microsoft.Authorization/classicAdministrators/write | 對訂用帳戶新增或修改系統管理員。 |
> | 動作 | Microsoft.Authorization/classicAdministrators/delete | 從訂用帳戶中移除系統管理員。 |
> |  | **classicAdministrators/operationstatuses** |  |
> | 動作 | Microsoft.Authorization/classicAdministrators/operationstatuses/read | 取得訂用帳戶的系統管理員作業狀態。 |
> |  | **denyAssignments** |  |
> | 動作 | Microsoft.Authorization/denyAssignments/read | 取得拒絕指派的資訊。 |
> | 動作 | Microsoft.Authorization/denyAssignments/write | 在指定範圍建立拒絕指派。 |
> | 動作 | Microsoft.Authorization/denyAssignments/delete | 在指定範圍刪除拒絕指派。 |
> |  | **鎖定** |  |
> | 動作 | Microsoft.Authorization/locks/read | 取得指定範圍的鎖定。 |
> | 動作 | Microsoft.Authorization/locks/write | 新增指定範圍的鎖定。 |
> | 動作 | Microsoft.Authorization/locks/delete | 刪除指定範圍的鎖定。 |
> |  | **業務** |  |
> | 動作 | Microsoft.Authorization/operations/read | 取得作業的清單 |
> |  | **權限** |  |
> | 動作 | Microsoft.Authorization/permissions/read | 列出呼叫者在給定範圍內所具有的所有權限。 |
> |  | **策略** |  |
> | 動作 | Microsoft。授權/原則/audit/action | 以 ' audit ' 效果評估 Azure 原則的結果所採取的動作 |
> | 動作 | Microsoft. Authorization/原則/auditIfNotExists/action | 以 ' auditIfNotExists ' 效果評估 Azure 原則的結果所採取的動作 |
> | 動作 | Microsoft。授權/原則/拒絕/動作 | 以 ' deny ' 效果評估 Azure 原則的結果所採取的動作 |
> | 動作 | Microsoft. Authorization/原則/deployIfNotExists/action | 以 ' deployIfNotExists ' 效果評估 Azure 原則的結果所採取的動作 |
> |  | **policyAssignments** |  |
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
> |  | **roleAssignments** |  |
> | 動作 | Microsoft.Authorization/roleAssignments/read | 取得關於角色指派的資訊。 |
> | 動作 | Microsoft.Authorization/roleAssignments/write | 建立指定範圍的角色指派。 |
> | 動作 | Microsoft.Authorization/roleAssignments/delete | 刪除指定範圍內的角色指派。 |
> |  | **roleDefinitions** |  |
> | 動作 | Microsoft.Authorization/roleDefinitions/read | 取得關於角色定義的資訊。 |
> | 動作 | Microsoft.Authorization/roleDefinitions/write | 以指定權限和可指派的範圍來建立或更新自訂角色定義。 |
> | 動作 | Microsoft.Authorization/roleDefinitions/delete | 刪除指定的自訂角色定義。 |

## <a name="microsoftautomation"></a>Microsoft.Automation

Azure 服務：[自動化](../automation/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.Automation/register/action | 對 Azure 自動化註冊訂用帳戶 |
> |  | **automationAccounts** |  |
> | 動作 | Microsoft.Automation/automationAccounts/webhooks/action | 產生 Azure 自動化 Webhook 的 URI |
> | 動作 | Microsoft.Automation/automationAccounts/read | 取得 Azure 自動化帳戶 |
> | 動作 | Microsoft.Automation/automationAccounts/write | 建立或更新 Azure 自動化帳戶 |
> | 動作 | Microsoft.Automation/automationAccounts/listKeys/action | 讀取自動化帳戶的金鑰 |
> | 動作 | Microsoft.Automation/automationAccounts/delete | 刪除 Azure 自動化帳戶 |
> |  | **automationAccounts/agentRegistrationInformation** |  |
> | 動作 | Microsoft.Automation/automationAccounts/agentRegistrationInformation/read | 讀取 Azure Automation DSC 的註冊資訊 |
> | 動作 | Microsoft.Automation/automationAccounts/agentRegistrationInformation/regenerateKey/action | 寫入要求以重新產生 Azure Automation DSC 金鑰 |
> |  | **automationAccounts/憑證** |  |
> | 動作 | Microsoft.Automation/automationAccounts/certificates/getCount/action | 讀取憑證的計數 |
> | 動作 | Microsoft.Automation/automationAccounts/certificates/read | 取得 Azure 自動化憑證資產 |
> | 動作 | Microsoft.Automation/automationAccounts/certificates/write | 建立或更新 Azure 自動化憑證資產 |
> | 動作 | Microsoft.Automation/automationAccounts/certificates/delete | 刪除 Azure 自動化憑證資產 |
> |  | **automationAccounts/compilationjobs** |  |
> | 動作 | Microsoft.Automation/automationAccounts/compilationjobs/write | 寫入 Azure Automation DSC 的編譯 |
> | 動作 | Microsoft.Automation/automationAccounts/compilationjobs/read | 讀取 Azure Automation DSC 的編譯 |
> | 動作 | Microsoft.Automation/automationAccounts/compilationjobs/write | 寫入 Azure Automation DSC 的編譯 |
> | 動作 | Microsoft.Automation/automationAccounts/compilationjobs/read | 讀取 Azure Automation DSC 的編譯 |
> |  | **automationAccounts/設定** |  |
> | 動作 | Microsoft.Automation/automationAccounts/configurations/read | 取得 Azure Automation DSC 的內容 |
> | 動作 | Microsoft.Automation/automationAccounts/configurations/getCount/action | 讀取 Azure Automation DSC 內容的計數 |
> | 動作 | Microsoft.Automation/automationAccounts/configurations/write | 寫入 Azure Automation DSC 的內容 |
> | 動作 | Microsoft.Automation/automationAccounts/configurations/delete | 刪除 Azure Automation DSC 的內容 |
> |  | **automationAccounts/設定/內容** |  |
> | 動作 | Microsoft.Automation/automationAccounts/configurations/content/read | 讀取組態媒體內容 |
> |  | **automationAccounts/connections** |  |
> | 動作 | Microsoft.Automation/automationAccounts/connections/read | 取得 Azure 自動化連線資產 |
> | 動作 | Microsoft.Automation/automationAccounts/connections/getCount/action | 讀取連線的計數 |
> | 動作 | Microsoft.Automation/automationAccounts/connections/write | 建立或更新 Azure 自動化連線資產 |
> | 動作 | Microsoft.Automation/automationAccounts/connections/delete | 刪除 Azure 自動化連線資產 |
> |  | **automationAccounts/connectionTypes** |  |
> | 動作 | Microsoft.Automation/automationAccounts/connectionTypes/read | 取得 Azure 自動化連線類型資產 |
> | 動作 | Microsoft.Automation/automationAccounts/connectionTypes/write | 建立 Azure 自動化連線類型資產 |
> | 動作 | Microsoft.Automation/automationAccounts/connectionTypes/delete | 刪除 Azure 自動化連線類型資產 |
> |  | **automationAccounts/認證** |  |
> | 動作 | Microsoft.Automation/automationAccounts/credentials/read | 取得 Azure 自動化認證資產 |
> | 動作 | Microsoft.Automation/automationAccounts/credentials/getCount/action | 讀取認證的計數 |
> | 動作 | Microsoft.Automation/automationAccounts/credentials/write | 建立或更新 Azure 自動化認證資產 |
> | 動作 | Microsoft.Automation/automationAccounts/credentials/delete | 刪除 Azure 自動化認證資產 |
> |  | **automationAccounts/hybridRunbookWorkerGroups** |  |
> | 動作 | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | 讀取混合式 Runbook 背景工作角色資源 |
> | 動作 | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/delete | 刪除混合式 Runbook 背景工作角色資源 |
> |  | **automationAccounts/作業** |  |
> | 動作 | Microsoft.Automation/automationAccounts/jobs/runbookContent/action | 在作業執行時取得 Azure 自動化 Runbook 的內容 |
> | 動作 | Microsoft.Automation/automationAccounts/jobs/read | 取得 Azure 自動化作業 |
> | 動作 | Microsoft.Automation/automationAccounts/jobs/write | 建立 Azure 自動化作業 |
> | 動作 | Microsoft.Automation/automationAccounts/jobs/stop/action | 停止 Azure 自動化作業 |
> | 動作 | Microsoft.Automation/automationAccounts/jobs/suspend/action | 暫止 Azure 自動化作業 |
> | 動作 | Microsoft.Automation/automationAccounts/jobs/resume/action | 繼續 Azure 自動化作業 |
> |  | **automationAccounts/作業/輸出** |  |
> | 動作 | Microsoft.Automation/automationAccounts/jobs/output/read | 取得作業的輸出 |
> |  | **automationAccounts/作業/資料流程** |  |
> | 動作 | Microsoft.Automation/automationAccounts/jobs/streams/read | 取得 Azure 自動化作業串流 |
> | 動作 | Microsoft.Automation/automationAccounts/jobs/streams/read | 取得 Azure 自動化作業串流 |
> |  | **automationAccounts/jobSchedules** |  |
> | 動作 | Microsoft.Automation/automationAccounts/jobSchedules/read | 取得 Azure 自動化作業排程 |
> | 動作 | Microsoft.Automation/automationAccounts/jobSchedules/write | 建立 Azure 自動化作業排程 |
> | 動作 | Microsoft.Automation/automationAccounts/jobSchedules/delete | 刪除 Azure 自動化作業排程 |
> |  | **automationAccounts/linkedWorkspace** |  |
> | 動作 | Microsoft.Automation/automationAccounts/linkedWorkspace/read | 取得連結至自動化帳戶的工作區 |
> |  | **automationAccounts/模組** |  |
> | 動作 | Microsoft.Automation/automationAccounts/modules/read | 取得 Azure 自動化 Powershell 模組 |
> | 動作 | Microsoft.Automation/automationAccounts/modules/getCount/action | 取得自動化帳戶內的 Powershell 模組計數 |
> | 動作 | Microsoft.Automation/automationAccounts/modules/write | 建立或更新 Azure 自動化 Powershell 模組 |
> | 動作 | Microsoft.Automation/automationAccounts/modules/delete | 刪除 Azure 自動化 Powershell 模組 |
> |  | **automationAccounts/模組/活動** |  |
> | 動作 | Microsoft.Automation/automationAccounts/modules/activities/read | 取得 Azure 自動化活動 |
> |  | **automationAccounts/nodeConfigurations** |  |
> | 動作 | Microsoft.Automation/automationAccounts/nodeConfigurations/rawContent/action | 讀取 Azure Automation DSC 的節點設定內容 |
> | 動作 | Microsoft.Automation/automationAccounts/nodeConfigurations/read | 讀取 Azure Automation DSC 的節點設定 |
> | 動作 | Microsoft.Automation/automationAccounts/nodeConfigurations/write | 寫入 Azure Automation DSC 的節點設定 |
> | 動作 | Microsoft.Automation/automationAccounts/nodeConfigurations/delete | 刪除 Azure Automation DSC 的節點設定 |
> |  | **automationAccounts/nodecounts** |  |
> | 動作 | Microsoft.Automation/automationAccounts/nodecounts/read | 讀取指定類型的節點計數摘要 |
> |  | **automationAccounts/節點** |  |
> | 動作 | Microsoft.Automation/automationAccounts/nodes/read | 讀取 Azure Automation DSC 節點 |
> | 動作 | Microsoft.Automation/automationAccounts/nodes/write | 建立或更新 Azure Automation DSC 節點 |
> | 動作 | Microsoft.Automation/automationAccounts/nodes/delete | 刪除 Azure Automation DSC 節點 |
> |  | **automationAccounts/節點/報表** |  |
> | 動作 | Microsoft.Automation/automationAccounts/nodes/reports/read | 讀取 Azure Automation DSC 報告 |
> |  | **automationAccounts/節點/報表/內容** |  |
> | 動作 | Microsoft.Automation/automationAccounts/nodes/reports/content/read | 讀取 Azure Automation DSC 報告內容 |
> |  | **automationAccounts/objectDataTypes/fields** |  |
> | 動作 | Microsoft.Automation/automationAccounts/objectDataTypes/fields/read | 取得 Azure 自動化 TypeField |
> |  | **automationAccounts/privateEndpointConnectionProxies** |  |
> | 動作 | Microsoft. Automation/automationAccounts/privateEndpointConnectionProxies/read | 讀取 Azure 自動化私用端點連接 Proxy |
> | 動作 | AutomationAccounts/privateEndpointConnectionProxies/write | 建立 Azure 自動化私用端點連接 Proxy |
> | 動作 | Microsoft. Automation/automationAccounts/privateEndpointConnectionProxies/validate/action | 驗證私用端點連接要求（groupId 驗證） |
> | 動作 | AutomationAccounts/privateEndpointConnectionProxies/delete | 刪除 Azure 自動化私用端點連接 Proxy |
> |  | **automationAccounts/privateEndpointConnectionProxies/operationResults** |  |
> | 動作 | Microsoft. Automation/automationAccounts/privateEndpointConnectionProxies/operationResults/read | 取得 Azure 自動化私用端點 proxy 操作結果。 |
> |  | **automationAccounts/privateEndpointConnections** |  |
> | 動作 | Microsoft. Automation/automationAccounts/privateEndpointConnections/read | 取得 Azure 自動化私人端點線上狀態 |
> | 動作 | AutomationAccounts/privateEndpointConnections/write | 核准或拒絕 Azure 自動化的私用端點連接 |
> |  | **automationAccounts/privateLinkResources** |  |
> | 動作 | Microsoft. Automation/automationAccounts/privateLinkResources/read | 讀取私人端點的群組資訊 |
> |  | **automationAccounts/python2Packages** |  |
> | 動作 | Microsoft.Automation/automationAccounts/python2Packages/read | 取得 Azure 自動化 Python 2 套件 |
> | 動作 | Microsoft.Automation/automationAccounts/python2Packages/write | 建立或更新 Azure 自動化 Python 2 套件 |
> | 動作 | Microsoft.Automation/automationAccounts/python2Packages/delete | 刪除 Azure 自動化 Python 2 套件 |
> |  | **automationAccounts/python3Packages** |  |
> | 動作 | Microsoft.Automation/automationAccounts/python3Packages/read | 取得 Azure 自動化 Python 3 套件 |
> | 動作 | Microsoft.Automation/automationAccounts/python3Packages/write | 建立或更新 Azure 自動化 Python 3 套件 |
> | 動作 | Microsoft.Automation/automationAccounts/python3Packages/delete | 刪除 Azure 自動化 Python 3 套件 |
> |  | **automationAccounts/runbook** |  |
> | 動作 | Microsoft.Automation/automationAccounts/runbooks/read | 取得 Azure 自動化 Runbook |
> | 動作 | Microsoft.Automation/automationAccounts/runbooks/getCount/action | 取得 Azure 自動化 Runbook 的計數 |
> | 動作 | Microsoft.Automation/automationAccounts/runbooks/write | 建立或更新 Azure 自動化 Runbook |
> | 動作 | Microsoft.Automation/automationAccounts/runbooks/delete | 刪除 Azure 自動化 Runbook |
> | 動作 | Microsoft.Automation/automationAccounts/runbooks/publish/action | 發佈 Azure 自動化 Runbook 草稿 |
> |  | **automationAccounts/runbook/內容** |  |
> | 動作 | Microsoft.Automation/automationAccounts/runbooks/content/read | 取得 Azure 自動化 Runbook 的內容 |
> |  | **automationAccounts/runbook/草稿** |  |
> | 動作 | Microsoft.Automation/automationAccounts/runbooks/draft/read | 取得 Azure 自動化 Runbook 草稿 |
> | 動作 | Microsoft.Automation/automationAccounts/runbooks/draft/undoEdit/action | 復原對 Azure 自動化 Runbook 草稿所做的編輯 |
> |  | **automationAccounts/runbook/草稿/內容** |  |
> | 動作 | Microsoft.Automation/automationAccounts/runbooks/draft/content/write | 建立 Azure 自動化 Runbook 草稿的內容 |
> |  | **automationAccounts/runbook/draft/operationResults** |  |
> | 動作 | Microsoft.Automation/automationAccounts/runbooks/draft/operationResults/read | 取得 Azure 自動化 Runbook 草稿作業結果 |
> |  | **automationAccounts/runbook/draft/testJob** |  |
> | 動作 | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/read | 取得 Azure 自動化 Runbook 草稿測試作業 |
> | 動作 | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/write | 建立 Azure 自動化 Runbook 草稿測試作業 |
> | 動作 | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/stop/action | 停止 Azure 自動化 Runbook 草稿測試作業 |
> | 動作 | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/suspend/action | 暫止 Azure 自動化 Runbook 草稿測試作業 |
> | 動作 | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/resume/action | 繼續 Azure 自動化 Runbook 草稿測試作業 |
> |  | **automationAccounts/排程** |  |
> | 動作 | Microsoft.Automation/automationAccounts/schedules/read | 取得 Azure 自動化排程資產 |
> | 動作 | Microsoft.Automation/automationAccounts/schedules/getCount/action | 取得 Azure 自動化排程的計數 |
> | 動作 | Microsoft.Automation/automationAccounts/schedules/write | 建立或更新 Azure 自動化排程資產 |
> | 動作 | Microsoft.Automation/automationAccounts/schedules/delete | 刪除 Azure 自動化排程資產 |
> |  | **automationAccounts/softwareUpdateConfigurationMachineRuns** |  |
> | 動作 | Microsoft. Automation/automationAccounts/softwareUpdateConfigurationMachineRuns/read | 取得 Azure 自動化軟體更新設定機器執行 |
> |  | **automationAccounts/softwareUpdateConfigurationRuns** |  |
> | 動作 | Microsoft. Automation/automationAccounts/softwareUpdateConfigurationRuns/read | 取得 Azure 自動化軟體更新設定執行 |
> |  | **automationAccounts/softwareUpdateConfigurations** |  |
> | 動作 | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/write | 建立或更新 Azure 自動化軟體更新設定 |
> | 動作 | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/read | 取得 Azure 自動化軟體更新設定 |
> | 動作 | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/delete | 刪除 Azure 自動化軟體更新設定 |
> | 動作 | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/write | 建立或更新 Azure 自動化軟體更新設定 |
> | 動作 | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/read | 取得 Azure 自動化軟體更新設定 |
> | 動作 | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/delete | 刪除 Azure 自動化軟體更新設定 |
> |  | **automationAccounts/statistics** |  |
> | 動作 | Microsoft.Automation/automationAccounts/statistics/read | 取得 Azure 自動化統計資料 |
> |  | **automationAccounts/updateDeploymentMachineRuns** |  |
> | 動作 | Microsoft.Automation/automationAccounts/updateDeploymentMachineRuns/read | 取得 Azure 自動化更新部署機器 |
> |  | **automationAccounts/updateManagementPatchJob** |  |
> | 動作 | Microsoft.Automation/automationAccounts/updateManagementPatchJob/read | 取得 Azure 自動化更新管理修補作業 |
> |  | **automationAccounts/使用方式** |  |
> | 動作 | Microsoft.Automation/automationAccounts/usages/read | 取得 Azure 自動化使用方式 |
> |  | **automationAccounts/變數** |  |
> | 動作 | Microsoft.Automation/automationAccounts/variables/read | 讀取 Azure 自動化變數資產 |
> | 動作 | Microsoft.Automation/automationAccounts/variables/write | 建立或更新 Azure 自動化變數資產 |
> | 動作 | Microsoft.Automation/automationAccounts/variables/delete | 刪除 Azure 自動化變數資產 |
> |  | **automationAccounts/監看員** |  |
> | 動作 | Microsoft.Automation/automationAccounts/watchers/write | 建立 Azure 自動化監看員作業 |
> | 動作 | Microsoft.Automation/automationAccounts/watchers/read | 取得 Azure 自動化監看員作業 |
> | 動作 | Microsoft.Automation/automationAccounts/watchers/delete | 刪除 Azure 自動化監看員作業 |
> | 動作 | Microsoft.Automation/automationAccounts/watchers/start/action | 啟動 Azure 自動化監看員作業 |
> | 動作 | Microsoft.Automation/automationAccounts/watchers/stop/action | 停止 Azure 自動化監看員作業 |
> |  | **automationAccounts/監看員/資料流程** |  |
> | 動作 | Microsoft.Automation/automationAccounts/watchers/streams/read | 取得 Azure 自動化監看員作業串流 |
> |  | **automationAccounts/監看員/watcherActions** |  |
> | 動作 | Microsoft.Automation/automationAccounts/watchers/watcherActions/write | 建立 Azure 自動化監看員作業動作 |
> | 動作 | Microsoft.Automation/automationAccounts/watchers/watcherActions/read | 取得 Azure 自動化監看員作業動作 |
> | 動作 | Microsoft.Automation/automationAccounts/watchers/watcherActions/delete | 刪除 Azure 自動化監看員作業動作 |
> |  | **automationAccounts/webhook** |  |
> | 動作 | Microsoft.Automation/automationAccounts/webhooks/read | 讀取 Azure 自動化 Webhook |
> | 動作 | Microsoft.Automation/automationAccounts/webhooks/write | 建立或更新 Azure 自動化 Webhook |
> | 動作 | Microsoft.Automation/automationAccounts/webhooks/delete | 刪除 Azure 自動化 Webhook  |
> |  | **業務** |  |
> | 動作 | Microsoft.Automation/operations/read | 取得 Azure 自動化資源的可用作業 |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

Azure 服務： [Azure Active Directory B2C](../active-directory-b2c/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.AzureActiveDirectory/register/action | 為 Microsoft.AzureActiveDirectory 資源提供者註冊訂用帳戶 |
> |  | **b2cDirectories** |  |
> | 動作 | Microsoft.AzureActiveDirectory/b2cDirectories/write | 建立或更新 B2C 目錄資源 |
> | 動作 | Microsoft.AzureActiveDirectory/b2cDirectories/read | 檢視 B2C 目錄資源 |
> | 動作 | Microsoft.AzureActiveDirectory/b2cDirectories/delete | 刪除 B2C 目錄資源 |
> |  | **b2ctenants** |  |
> | 動作 | AzureActiveDirectory/b2ctenants/read | 列出使用者為其成員的所有 B2C 租使用者 |
> |  | **業務** |  |
> | 動作 | Microsoft.AzureActiveDirectory/operations/read | 讀取可供 Microsoft.AzureActiveDirectory 資源提供者使用的所有 API 作業 |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

Azure 服務：核心

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.AzureStack/register/action | 向 Microsoft.AzureStack 資源提供者註冊訂用帳戶 |
> |  | **cloudManifestFiles** |  |
> | 動作 | AzureStack/cloudManifestFiles/read | 取得雲端資訊清單檔案 |
> |  | **作業** |  |
> | 動作 | Microsoft.AzureStack/Operations/read | 取得資源提供者作業的屬性 |
> |  | **登記** |  |
> | 動作 | Microsoft.AzureStack/registrations/read | 取得 Azure Stack 註冊的屬性 |
> | 動作 | Microsoft.AzureStack/registrations/write | 建立或更新 Azure Stack 註冊 |
> | 動作 | Microsoft.AzureStack/registrations/delete | 刪除 Azure Stack 註冊 |
> | 動作 | Microsoft.AzureStack/registrations/getActivationKey/action | 取得最新的 Azure Stack 啟用金鑰 |
> |  | **註冊/customerSubscriptions** |  |
> | 動作 | Microsoft.AzureStack/registrations/customerSubscriptions/read | 取得 Azure Stack 客戶訂用帳戶的屬性 |
> | 動作 | Microsoft.AzureStack/registrations/customerSubscriptions/write | 建立或更新 Azure Stack 客戶訂用帳戶 |
> | 動作 | Microsoft.AzureStack/registrations/customerSubscriptions/delete | 刪除 Azure Stack 客戶訂用帳戶 |
> |  | **註冊/產品** |  |
> | 動作 | Microsoft.AzureStack/registrations/products/read | 取得 Azure Stack Marketplace 產品的屬性 |
> | 動作 | Microsoft.AzureStack/registrations/products/listDetails/action | 擷取 Azure Stack Marketplace 產品的延伸詳細資料 |
> | 動作 | AzureStack/註冊/產品/getProducts/動作 | 抓取 Azure Stack Marketplace 產品的清單 |
> | 動作 | AzureStack/註冊/產品/getProduct/動作 | 抓取 Azure Stack Marketplace 產品 |
> | 動作 | AzureStack/註冊/產品/uploadProductLog/動作 | 記錄 Azure Stack Marketplace 產品作業狀態和時間戳記 |

## <a name="microsoftbatch"></a>Microsoft.Batch

Azure 服務： [Batch](../batch/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.Batch/register/action | 針對 Batch 資源提供者註冊訂用帳戶，並讓您能夠建立 Batch 帳戶 |
> | 動作 | Microsoft.Batch/unregister/action | 為 Batch 資源提供者取消註冊訂用帳戶，以防止建立 Batch 帳戶 |
> |  | **batchAccounts** |  |
> | 動作 | Microsoft.Batch/batchAccounts/read | 列出 Batch 帳戶，或取得 Batch 帳戶的屬性 |
> | 動作 | Microsoft.Batch/batchAccounts/write | 建立新的 Batch 帳戶，或更新現有的 Batch 帳戶 |
> | 動作 | Microsoft.Batch/batchAccounts/delete | 刪除 Batch 帳戶 |
> | 動作 | Microsoft.Batch/batchAccounts/listkeys/action | 列出 Batch 帳戶的存取金鑰 |
> | 動作 | Microsoft.Batch/batchAccounts/regeneratekeys/action | 重新產生 Batch 帳戶的存取金鑰 |
> | 動作 | Microsoft.Batch/batchAccounts/syncAutoStorageKeys/action | 針對為 Batch 帳戶所設定的自動儲存體帳戶同步處理存取金鑰 |
> |  | **batchAccounts/應用程式** |  |
> | 動作 | Microsoft.Batch/batchAccounts/applications/read | 列出應用程式，或取得應用程式的屬性 |
> | 動作 | Microsoft.Batch/batchAccounts/applications/write | 建立新的應用程式，或更新現有應用程式 |
> | 動作 | Microsoft.Batch/batchAccounts/applications/delete | 刪除應用程式 |
> |  | **batchAccounts/應用程式/版本** |  |
> | 動作 | Microsoft.Batch/batchAccounts/applications/versions/read | 取得應用程式套件的屬性 |
> | 動作 | Microsoft.Batch/batchAccounts/applications/versions/write | 建立新的應用程式套件，或更新現有的應用程式套件 |
> | 動作 | Microsoft.Batch/batchAccounts/applications/versions/delete | 刪除應用程式套件 |
> | 動作 | Microsoft.Batch/batchAccounts/applications/versions/activate/action | 啟動應用程式套件 |
> |  | **batchAccounts/certificateOperationResults** |  |
> | 動作 | Microsoft.Batch/batchAccounts/certificateOperationResults/read | 取得 Batch 帳戶上長時間執行憑證作業的結果 |
> |  | **batchAccounts/憑證** |  |
> | 動作 | Microsoft.Batch/batchAccounts/certificates/read | 列出批 Batch 帳戶上的憑證，或取得憑證的屬性 |
> | 動作 | Microsoft.Batch/batchAccounts/certificates/write | 在 Batch 帳戶上建立新的憑證，或更新現有憑證 |
> | 動作 | Microsoft.Batch/batchAccounts/certificates/delete | 從 Batch 帳戶刪除憑證 |
> | 動作 | Microsoft.Batch/batchAccounts/certificates/cancelDelete/action | 取消 Batch 帳戶上無法刪除憑證的作業 |
> |  | **batchAccounts/operationResults** |  |
> | 動作 | Microsoft.Batch/batchAccounts/operationResults/read | 取得長時間執行 Batch 帳戶作業的結果 |
> |  | **batchAccounts/poolOperationResults** |  |
> | 動作 | Microsoft.Batch/batchAccounts/poolOperationResults/read | 取得 Batch 帳戶上長時間執行集區作業的結果 |
> |  | **batchAccounts/集區** |  |
> | 動作 | Microsoft.Batch/batchAccounts/pools/read | 列出 Batch 帳戶上的集區，或取得集區的屬性 |
> | 動作 | Microsoft.Batch/batchAccounts/pools/write | 在 Batch 帳戶上建立新的集區，或更新現有的集區 |
> | 動作 | Microsoft.Batch/batchAccounts/pools/delete | 從 Batch 帳戶刪除集區 |
> | 動作 | Microsoft.Batch/batchAccounts/pools/stopResize/action | 停止 Batch 帳戶集區上正在進行的調整大小作業 |
> | 動作 | Microsoft.Batch/batchAccounts/pools/disableAutoscale/action | 停用 Batch 帳戶集區的自動縮放比例 |
> |  | **堆放** |  |
> | 動作 | Microsoft.Batch/locations/checkNameAvailability/action | 檢查帳戶名稱有效，且並非使用中。 |
> |  | **位置/accountOperationResults** |  |
> | 動作 | Microsoft.Batch/locations/accountOperationResults/read | 取得長時間執行 Batch 帳戶作業的結果 |
> |  | **位置/配額** |  |
> | 動作 | Microsoft.Batch/locations/quotas/read | 取得指定訂用帳戶在指定 Azure 區域內的 Batch 配額 |
> |  | **業務** |  |
> | 動作 | Microsoft.Batch/operations/read | 列出可對 Microsoft.Batch 資源提供者進行的作業 |
> |  | **batchAccounts/作業** |  |
> | DataAction | Microsoft。 Batch/batchAccounts/作業/讀取 | 列出 Batch 帳戶上的作業，或取得作業的屬性 |
> | DataAction | Microsoft。 Batch/batchAccounts/作業/寫入 | 在 Batch 帳戶上建立新作業，或更新現有的作業 |
> | DataAction | Microsoft Batch/batchAccounts/job/delete | 從 Batch 帳戶刪除作業 |
> |  | **batchAccounts/jobSchedules** |  |
> | DataAction | BatchAccounts/jobSchedules/read | 列出 Batch 帳戶上的作業排程，或取得作業排程的屬性 |
> | DataAction | BatchAccounts/jobSchedules/write | 在 Batch 帳戶上建立新的作業排程，或更新現有的作業排程 |
> | DataAction | BatchAccounts/jobSchedules/delete | 從 Batch 帳戶刪除作業排程 |

## <a name="microsoftbilling"></a>Microsoft.Billing

Azure 服務：[成本管理 + 帳單](../cost-management-billing/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft 帳單/validateAddress/動作 |  |
> | 動作 | Microsoft.Billing/register/action |  |
> |  | **billingAccounts** |  |
> | 動作 | Microsoft.Billing/billingAccounts/read |  |
> | 動作 | Microsoft 帳單/billingAccounts/listInvoiceSectionsWithCreateSubscriptionPermission/動作 |  |
> | 動作 | Microsoft 帳單/billingAccounts/寫入 |  |
> |  | **billingAccounts/合約** |  |
> | 動作 | Microsoft 帳單/billingAccounts/合約/讀取 |  |
> |  | **billingAccounts/billingPermissions** |  |
> | 動作 | Microsoft 帳單/billingAccounts/billingPermissions/read |  |
> |  | **billingAccounts/billingProfiles** |  |
> | 動作 | Microsoft 帳單/billingAccounts/billingProfiles/write |  |
> | 動作 | Microsoft 帳單/billingAccounts/billingProfiles/read |  |
> | 動作 | Microsoft 帳單/billingAccounts/billingProfiles/write |  |
> | 動作 | Microsoft 帳單/billingAccounts/billingProfiles/write |  |
> |  | **billingAccounts/billingProfiles/billingPermissions** |  |
> | 動作 | Microsoft 帳單/billingAccounts/billingProfiles/billingPermissions/read |  |
> |  | **billingAccounts/billingProfiles/客戶** |  |
> | 動作 | Microsoft 帳單/billingAccounts/billingProfiles/客戶/讀取 |  |
> |  | **billingAccounts/billingProfiles/發票/pricesheet** |  |
> | 動作 | Microsoft 帳單/billingAccounts/billingProfiles/發票/pricesheet/下載/動作 |  |
> |  | **billingAccounts/billingProfiles/invoiceSections** |  |
> | 動作 | Microsoft 帳單/billingAccounts/billingProfiles/invoiceSections/write |  |
> | 動作 | Microsoft 帳單/billingAccounts/billingProfiles/invoiceSections/read |  |
> | 動作 | Microsoft 帳單/billingAccounts/billingProfiles/invoiceSections/write |  |
> |  | **billingAccounts/billingProfiles/invoiceSections/billingPermissions** |  |
> | 動作 | Microsoft 帳單/billingAccounts/billingProfiles/invoiceSections/billingPermissions/read |  |
> |  | **billingAccounts/billingProfiles/invoiceSections/billingSubscriptions** |  |
> | 動作 | Microsoft 帳單/billingAccounts/billingProfiles/invoiceSections/billingSubscriptions/transfer/action |  |
> | 動作 | Microsoft 帳單/billingAccounts/billingProfiles/invoiceSections/billingSubscriptions/move/action |  |
> | 動作 | Microsoft 帳單/billingAccounts/billingProfiles/invoiceSections/billingSubscriptions/validateMoveEligibility/action |  |
> |  | **billingAccounts/billingProfiles/invoiceSections/products** |  |
> | 動作 | Microsoft 帳單/billingAccounts/billingProfiles/invoiceSections/產品/傳輸/動作 |  |
> | 動作 | Microsoft 帳單/billingAccounts/billingProfiles/invoiceSections/products/move/action |  |
> | 動作 | Microsoft 帳單/billingAccounts/billingProfiles/invoiceSections/products/validateMoveEligibility/action |  |
> |  | **billingAccounts/billingProfiles/pricesheet** |  |
> | 動作 | Microsoft 帳單/billingAccounts/billingProfiles/pricesheet/下載/動作 |  |
> |  | **billingAccounts/billingSubscriptions** |  |
> | 動作 | Microsoft 帳單/billingAccounts/billingSubscriptions/read |  |
> |  | **billingAccounts/客戶** |  |
> | 動作 | Microsoft 帳單/billingAccounts/客戶/讀取 |  |
> |  | **billingAccounts/customers/billingPermissions** |  |
> | 動作 | Microsoft 帳單/billingAccounts/customers/billingPermissions/read |  |
> |  | **billingAccounts/部門** |  |
> | 動作 | Microsoft.Billing/billingAccounts/departments/read |  |
> |  | **billingAccounts/enrollmentAccounts** |  |
> | 動作 | Microsoft.Billing/billingAccounts/enrollmentAccounts/read |  |
> |  | **billingAccounts/enrollmentAccounts/billingPermissions** |  |
> | 動作 | Microsoft 帳單/billingAccounts/enrollmentAccounts/billingPermissions/read |  |
> |  | **billingAccounts/enrollmentDepartments/billingPermissions** |  |
> | 動作 | Microsoft 帳單/billingAccounts/enrollmentDepartments/billingPermissions/read |  |
> |  | **billingAccounts/products** |  |
> | 動作 | Microsoft 帳單/billingAccounts/產品/讀取 |  |
> |  | **部門** |  |
> | 動作 | Microsoft.Billing/departments/read |  |
> |  | **而後** |  |
> | 動作 | Microsoft 帳單/發票/下載/動作 | 使用來自清單的下載連結來下載發票 |
> | 動作 | Microsoft 帳單/發票/下載/動作 | 使用來自清單的下載連結來下載發票 |
> | 動作 | Microsoft.Billing/invoices/read |  |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

Azure 服務： [Bing Maps](https://docs.microsoft.com/BingMaps/)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | BingMaps/updateCommunicationPreference/action | 更新 BingMaps 擁有者的通訊喜好設定。 |
> | 動作 | BingMaps/listCommunicationPreference/action | 取得 BingMaps 擁有者的通訊喜好設定。 |
> |  | **mapApis** |  |
> | 動作 | Microsoft.BingMaps/mapApis/Read | 取得 BingMaps/mapApis 的資源 |
> | 動作 | Microsoft.BingMaps/mapApis/Write | 更新 BingMaps/mapApis 的資源 |
> | 動作 | Microsoft.BingMaps/mapApis/Delete | 刪除 BingMaps/mapApis 的資源 |
> | 動作 | Microsoft.BingMaps/mapApis/regenerateKey/action | 重新產生 BingMaps/mapApis 的金鑰 |
> | 動作 | Microsoft.BingMaps/mapApis/listSecrets/action | 列出 BingMaps/mapApis 的秘密 |
> | 動作 | BingMaps/mapApis/listUsageMetrics/action | 列出 BingMaps/mapApis 的計量 |
> |  | **作業** |  |
> | 動作 | Microsoft.BingMaps/Operations/read | 列出 BingMaps 的作業 |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

Azure 服務： [Azure 區塊鏈服務](../blockchain/workbench/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | 區塊鏈/register/action | 為區塊鏈資源提供者註冊訂用帳戶。 |
> |  | **blockchainMembers** |  |
> | 動作 | 區塊鏈/blockchainMembers/read | 取得或列出現有的區塊鏈成員。 |
> | 動作 | 區塊鏈/blockchainMembers/write | 建立或更新區塊鏈成員。 |
> | 動作 | 區塊鏈/blockchainMembers/delete | 刪除現有的區塊鏈成員。 |
> | 動作 | 區塊鏈/blockchainMembers/listApiKeys/action | 取得或列出現有的區塊鏈成員 API 金鑰。 |
> |  | **blockchainMembers/transactionNodes** |  |
> | 動作 | 區塊鏈/blockchainMembers/transactionNodes/read | 取得或列出現有的區塊鏈成員交易節點。 |
> | 動作 | 區塊鏈/blockchainMembers/transactionNodes/write | 建立或更新區塊鏈成員交易節點。 |
> | 動作 | 區塊鏈/blockchainMembers/transactionNodes/delete | 刪除現有的區塊鏈成員交易節點。 |
> | 動作 | 區塊鏈/blockchainMembers/transactionNodes/listApiKeys/action | 取得或列出現有的區塊鏈成員交易節點 API 金鑰。 |
> |  | **cordaMembers** |  |
> | 動作 | 區塊鏈/cordaMembers/read | 取得或列出現有的區塊鏈 Corda 成員。 |
> | 動作 | 區塊鏈/cordaMembers/write | 建立或更新區塊鏈 Corda 成員。 |
> | 動作 | 區塊鏈/cordaMembers/delete | 刪除現有的區塊鏈 Corda 成員。 |
> |  | **堆放** |  |
> | 動作 | 區塊鏈/位置/checkNameAvailability/動作 | 檢查資源名稱是否有效，且不在使用中。 |
> |  | **位置/blockchainMemberOperationResults** |  |
> | 動作 | 區塊鏈/位置/blockchainMemberOperationResults/讀取 | 取得區塊鏈成員的作業結果。 |
> |  | **業務** |  |
> | 動作 | 區塊鏈/作業/讀取 | 列出 Microsoft 區塊鏈資源提供者中的所有作業。 |
> |  | **blockchainMembers/transactionNodes** |  |
> | DataAction | 區塊鏈/blockchainMembers/transactionNodes/connect/action | 連接到區塊鏈成員交易節點。 |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

Azure 服務： [Azure 藍圖](../governance/blueprints/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.Blueprint/register/action | 註冊 Azure 藍圖資源提供者 |
> |  | **blueprintAssignments** |  |
> | 動作 | Microsoft.Blueprint/blueprintAssignments/read | 讀取任何藍圖成品 |
> | 動作 | Microsoft.Blueprint/blueprintAssignments/write | 建立或更新任何藍圖成品 |
> | 動作 | Microsoft.Blueprint/blueprintAssignments/delete | 刪除任何藍圖成品 |
> | 動作 | Microsoft. 藍圖/blueprintAssignments/Whoisblueprint/action | 取得 Azure 藍圖服務主體物件識別碼。 |
> |  | **blueprintAssignments/assignmentOperations** |  |
> | 動作 | Microsoft.Blueprint/blueprintAssignments/assignmentOperations/read | 讀取任何藍圖成品 |
> |  | **藍圖** |  |
> | 動作 | Microsoft.Blueprint/blueprints/read | 讀取任何藍圖 |
> | 動作 | Microsoft.Blueprint/blueprints/write | 建立或更新任何藍圖 |
> | 動作 | Microsoft.Blueprint/blueprints/delete | 刪除任何藍圖 |
> |  | **藍圖/成品** |  |
> | 動作 | Microsoft.Blueprint/blueprints/artifacts/read | 讀取任何藍圖成品 |
> | 動作 | Microsoft.Blueprint/blueprints/artifacts/write | 建立或更新任何藍圖成品 |
> | 動作 | Microsoft.Blueprint/blueprints/artifacts/delete | 刪除任何藍圖成品 |
> |  | **藍圖/版本** |  |
> | 動作 | Microsoft.Blueprint/blueprints/versions/read | 讀取任何藍圖 |
> | 動作 | Microsoft.Blueprint/blueprints/versions/write | 建立或更新任何藍圖 |
> | 動作 | Microsoft.Blueprint/blueprints/versions/delete | 刪除任何藍圖 |
> |  | **藍圖/版本/構件** |  |
> | 動作 | Microsoft.Blueprint/blueprints/versions/artifacts/read | 讀取任何藍圖成品 |

## <a name="microsoftbotservice"></a>Microsoft.BotService

Azure 服務： [Azure Bot service](https://docs.microsoft.com/azure/bot-service/)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | BotService/checknameavailability/action | 檢查 Bot 的名稱可用性 |
> | 動作 | BotService/listauthserviceproviders/action | 列出驗證服務提供者 |
> |  | **botServices** |  |
> | 動作 | Microsoft.BotService/botServices/read | 讀取 Bot 服務 |
> | 動作 | Microsoft.BotService/botServices/write | 寫入 Bot 服務 |
> | 動作 | Microsoft.BotService/botServices/delete | 刪除 Bot 服務 |
> |  | **botServices/通道** |  |
> | 動作 | Microsoft.BotService/botServices/channels/read | 讀取 Bot 服務通道 |
> | 動作 | Microsoft.BotService/botServices/channels/write | 撰寫 Bot 服務通道 |
> | 動作 | Microsoft.BotService/botServices/channels/delete | 刪除 Bot 服務通道 |
> | 動作 | BotService/botServices/頻道/listchannelwithkeys/action | 列出具有秘密的 Botservice 頻道 |
> |  | **botServices/connections** |  |
> | 動作 | Microsoft.BotService/botServices/connections/read | 讀取 Bot 服務連線 |
> | 動作 | Microsoft.BotService/botServices/connections/write | 撰寫 Bot 服務連線 |
> | 動作 | Microsoft.BotService/botServices/connections/delete | 刪除 Bot 服務連線 |
> |  | **botServices/connections/listwithsecrets** |  |
> | 動作 | BotService/botServices/connections/listwithsecrets/write | 撰寫 Bot 服務連接清單  |
> |  | **位置/operationresults** |  |
> | 動作 | Microsoft.BotService/locations/operationresults/read | 讀取非同步作業的狀態 |
> |  | **作業** |  |
> | 動作 | Microsoft.BotService/Operations/read | 讀取所有資源類型的作業 |

## <a name="microsoftcache"></a>Microsoft.Cache

Azure 服務： [Azure Cache For Redis](../azure-cache-for-redis/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.Cache/checknameavailability/action | 檢查名稱是否可用於新的 Redis 快取 |
> | 動作 | Microsoft.Cache/register/action | 向訂用帳戶註冊 'Microsoft.Cache' 資源提供者 |
> | 動作 | Microsoft.Cache/unregister/action | 向訂用帳戶取消註冊 'Microsoft.Cache' 資源提供者 |
> |  | **位置/operationresults** |  |
> | 動作 | Microsoft.Cache/locations/operationresults/read | 取得先前已將 'Location' 標頭傳回用戶端之長時間執行作業的結果 |
> |  | **業務** |  |
> | 動作 | Microsoft.Cache/operations/read | 列出 'Microsoft.Cache' 提供者支援的作業。 |
> |  | **redis** |  |
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
> |  | **redis/firewallRules** |  |
> | 動作 | Microsoft.Cache/redis/firewallRules/read | 取得 Redis 快取的 IP 防火牆規則 |
> | 動作 | Microsoft.Cache/redis/firewallRules/write | 編輯 Redis 快取的 IP 防火牆規則 |
> | 動作 | Microsoft.Cache/redis/firewallRules/delete | 刪除 Redis 快取的 IP 防火牆規則 |
> |  | **redis/linkedservers** |  |
> | 動作 | Microsoft.Cache/redis/linkedservers/read | 取得與 Redis 快取相關聯的連結伺服器。 |
> | 動作 | Microsoft.Cache/redis/linkedservers/write | 對 Redis 快取新增連結伺服器 |
> | 動作 | Microsoft.Cache/redis/linkedservers/delete | 從 Redis 快取中刪除連結伺服器 |
> |  | **redis/Metricdefinitions.listasync** |  |
> | 動作 | Microsoft.Cache/redis/metricDefinitions/read | 取得 Redis 快取可用的計量 |
> |  | **redis/patchSchedules** |  |
> | 動作 | Microsoft.Cache/redis/patchSchedules/read | 取得 Redis 快取的修補排程 |
> | 動作 | Microsoft.Cache/redis/patchSchedules/write | 修改 Redis 快取的修補排程 |
> | 動作 | Microsoft.Cache/redis/patchSchedules/delete | 刪除 Redis 快取的修補排程 |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

Azure 服務：核心

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
> | 動作 | Microsoft。容量/calculateexchange/動作 | 計算新購買的交換金額和價格，並傳回原則錯誤。 |
> | 動作 | Microsoft。容量/exchange/動作 | 交換任何保留 |
> |  | **appliedreservations** |  |
> | 動作 | Microsoft.Capacity/appliedreservations/read | 讀取所有保留項目 |
> |  | **庫** |  |
> | 動作 | Microsoft.Capacity/catalogs/read | 讀取保留目錄 |
> |  | **commercialreservationorders** |  |
> | 動作 | Microsoft.Capacity/commercialreservationorders/read | 取得在任何租用戶中建立的保留順序 |
> |  | **業務** |  |
> | 動作 | Microsoft.Capacity/operations/read | 讀取任何作業 |
> |  | **reservationorders** |  |
> | 動作 | Microsoft.Capacity/reservationorders/availablescopes/action | 尋找任何可用的範圍 |
> | 動作 | Microsoft.Capacity/reservationorders/read | 讀取所有保留項目 |
> | 動作 | Microsoft.Capacity/reservationorders/write | 建立任何保留項目 |
> | 動作 | Microsoft.Capacity/reservationorders/delete | 刪除任何保留項目 |
> | 動作 | Microsoft.Capacity/reservationorders/reservations/action | 更新任何保留項目 |
> | 動作 | Microsoft.Capacity/reservationorders/return/action | 傳回任何保留項目 |
> | 動作 | Microsoft.Capacity/reservationorders/swap/action | 交換任何保留項目 |
> | 動作 | Microsoft.Capacity/reservationorders/split/action | 分割任何保留項目 |
> | 動作 | Microsoft.Capacity/reservationorders/merge/action | 合併任何保留項目 |
> | 動作 | Microsoft. 容量/reservationorders/calculaterefund/action | 計算新購買的退款金額和價格，並傳回原則錯誤。 |
> |  | **reservationorders/mergeoperationresults** |  |
> | 動作 | Microsoft. 容量/reservationorders/mergeoperationresults/read | 輪詢任何合併作業 |
> |  | **reservationorders/保留** |  |
> | 動作 | Microsoft. 容量/reservationorders/保留/availablescopes/動作 | 尋找任何可用的範圍 |
> | 動作 | Microsoft.Capacity/reservationorders/reservations/read | 讀取所有保留項目 |
> | 動作 | Microsoft.Capacity/reservationorders/reservations/write | 建立任何保留項目 |
> | 動作 | Microsoft.Capacity/reservationorders/reservations/delete | 刪除任何保留項目 |
> |  | **reservationorders/保留/修訂** |  |
> | 動作 | Microsoft.Capacity/reservationorders/reservations/revisions/read | 讀取所有保留項目 |
> |  | **reservationorders/splitoperationresults** |  |
> | 動作 | Microsoft. 容量/reservationorders/splitoperationresults/read | 輪詢任何分割作業 |
> |  | **租戶** |  |
> | 動作 | Microsoft.Capacity/tenants/register/action | 註冊任何租用戶 |

## <a name="microsoftcdn"></a>Microsoft.Cdn

Azure 服務：[內容傳遞網路](../cdn/index.yml)

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
> |  | **operationresults** |  |
> | 動作 | Microsoft.Cdn/operationresults/read |  |
> | 動作 | Microsoft.Cdn/operationresults/write |  |
> | 動作 | Microsoft.Cdn/operationresults/delete |  |
> |  | **operationresults/profileresults** |  |
> | 動作 | Microsoft.Cdn/operationresults/profileresults/read |  |
> | 動作 | Microsoft.Cdn/operationresults/profileresults/write |  |
> | 動作 | Microsoft.Cdn/operationresults/profileresults/delete |  |
> | 動作 | Microsoft.Cdn/operationresults/profileresults/CheckResourceUsage/action |  |
> | 動作 | Microsoft.Cdn/operationresults/profileresults/GenerateSsoUri/action |  |
> | 動作 | Microsoft.Cdn/operationresults/profileresults/GetSupportedOptimizationTypes/action |  |
> |  | **operationresults/profileresults/endpointresults** |  |
> | 動作 | Microsoft.Cdn/operationresults/profileresults/endpointresults/read |  |
> | 動作 | Microsoft.Cdn/operationresults/profileresults/endpointresults/write |  |
> | 動作 | Microsoft.Cdn/operationresults/profileresults/endpointresults/delete |  |
> | 動作 | Microsoft.Cdn/operationresults/profileresults/endpointresults/CheckResourceUsage/action |  |
> | 動作 | Microsoft.Cdn/operationresults/profileresults/endpointresults/Start/action |  |
> | 動作 | Microsoft.Cdn/operationresults/profileresults/endpointresults/Stop/action |  |
> | 動作 | Microsoft.Cdn/operationresults/profileresults/endpointresults/Purge/action |  |
> | 動作 | Microsoft.Cdn/operationresults/profileresults/endpointresults/Load/action |  |
> | 動作 | Microsoft.Cdn/operationresults/profileresults/endpointresults/ValidateCustomDomain/action |  |
> |  | **operationresults/profileresults/endpointresults/customdomainresults** |  |
> | 動作 | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/read |  |
> | 動作 | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/write |  |
> | 動作 | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/delete |  |
> | 動作 | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/DisableCustomHttps/action |  |
> | 動作 | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/EnableCustomHttps/action |  |
> |  | **operationresults/profileresults/endpointresults/origingroupresults** |  |
> | 動作 | Microsoft Cdn/operationresults/profileresults/endpointresults/origingroupresults/read |  |
> | 動作 | Microsoft Cdn/operationresults/profileresults/endpointresults/origingroupresults/write |  |
> | 動作 | Microsoft Cdn/operationresults/profileresults/endpointresults/origingroupresults/delete |  |
> |  | **operationresults/profileresults/endpointresults/originresults** |  |
> | 動作 | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/read |  |
> | 動作 | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/write |  |
> | 動作 | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/delete |  |
> |  | **業務** |  |
> | 動作 | Microsoft.Cdn/operations/read |  |
> |  | **資料** |  |
> | 動作 | Microsoft.Cdn/profiles/read |  |
> | 動作 | Microsoft.Cdn/profiles/write |  |
> | 動作 | Microsoft.Cdn/profiles/delete |  |
> | 動作 | Microsoft.Cdn/profiles/CheckResourceUsage/action |  |
> | 動作 | Microsoft.Cdn/profiles/GenerateSsoUri/action |  |
> | 動作 | Microsoft.Cdn/profiles/GetSupportedOptimizationTypes/action |  |
> |  | **設定檔/端點** |  |
> | 動作 | Microsoft.Cdn/profiles/endpoints/read |  |
> | 動作 | Microsoft.Cdn/profiles/endpoints/write |  |
> | 動作 | Microsoft.Cdn/profiles/endpoints/delete |  |
> | 動作 | Microsoft.Cdn/profiles/endpoints/CheckResourceUsage/action |  |
> | 動作 | Microsoft.Cdn/profiles/endpoints/Start/action |  |
> | 動作 | Microsoft.Cdn/profiles/endpoints/Stop/action |  |
> | 動作 | Microsoft.Cdn/profiles/endpoints/Purge/action |  |
> | 動作 | Microsoft.Cdn/profiles/endpoints/Load/action |  |
> | 動作 | Microsoft.Cdn/profiles/endpoints/ValidateCustomDomain/action |  |
> |  | **設定檔/端點/customdomains** |  |
> | 動作 | Microsoft.Cdn/profiles/endpoints/customdomains/read |  |
> | 動作 | Microsoft.Cdn/profiles/endpoints/customdomains/write |  |
> | 動作 | Microsoft.Cdn/profiles/endpoints/customdomains/delete |  |
> | 動作 | Microsoft.Cdn/profiles/endpoints/customdomains/DisableCustomHttps/action |  |
> | 動作 | Microsoft.Cdn/profiles/endpoints/customdomains/EnableCustomHttps/action |  |
> |  | **設定檔/端點/origingroups** |  |
> | 動作 | Microsoft. Cdn/設定檔/端點/origingroups/讀取 |  |
> | 動作 | Microsoft Cdn/設定檔/端點/origingroups/寫入 |  |
> | 動作 | Microsoft Cdn/設定檔/端點/origingroups/刪除 |  |
> |  | **設定檔/端點/來源** |  |
> | 動作 | Microsoft.Cdn/profiles/endpoints/origins/read |  |
> | 動作 | Microsoft.Cdn/profiles/endpoints/origins/write |  |
> | 動作 | Microsoft.Cdn/profiles/endpoints/origins/delete |  |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

Azure 服務： [App Service 憑證](../app-service/configure-ssl-certificate.md#import-an-app-service-certificate)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.CertificateRegistration/provisionGlobalAppServicePrincipalInUserTenant/Action | 為服務應用程式主體佈建服務主體 |
> | 動作 | Microsoft.CertificateRegistration/validateCertificateRegistrationInformation/Action | 驗證憑證購買物件，但不將其提交出去 |
> | 動作 | Microsoft.CertificateRegistration/register/action | 針對訂用帳戶註冊 Microsoft 憑證資源提供者 |
> |  | **certificateOrders** |  |
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
> |  | **certificateOrders/憑證** |  |
> | 動作 | Microsoft.CertificateRegistration/certificateOrders/certificates/Write | 新增憑證或更新現有憑證 |
> | 動作 | Microsoft.CertificateRegistration/certificateOrders/certificates/Delete | 刪除現有憑證 |
> | 動作 | Microsoft.CertificateRegistration/certificateOrders/certificates/Read | 取得憑證清單 |
> |  | **業務** |  |
> | 動作 | Microsoft.CertificateRegistration/operations/Read | 列出 App Service 憑證註冊的所有作業 |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

Azure 服務：傳統部署模型虛擬機器

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.ClassicCompute/register/action | 傳統計算的暫存器 |
> | 動作 | Microsoft.ClassicCompute/checkDomainNameAvailability/action | 檢查給定網域名稱的可用性。 |
> | 動作 | Microsoft.ClassicCompute/moveSubscriptionResources/action | 將所有傳統資源移動到不同訂用帳戶。 |
> | 動作 | Microsoft.ClassicCompute/validateSubscriptionMoveAvailability/action | 驗證訂用帳戶是否可進行傳統移動作業。 |
> |  | **功能** |  |
> | 動作 | Microsoft.ClassicCompute/capabilities/read | 顯示功能 |
> |  | **checkDomainNameAvailability** |  |
> | 動作 | Microsoft.ClassicCompute/checkDomainNameAvailability/read | 取得指定網域名稱的可用性。 |
> |  | **domainNames** |  |
> | 動作 | Microsoft.ClassicCompute/domainNames/read | 傳回資源的網域名稱。 |
> | 動作 | Microsoft.ClassicCompute/domainNames/write | 新增或修改資源的網域名稱。 |
> | 動作 | Microsoft.ClassicCompute/domainNames/delete | 移除資源的網域名稱。 |
> | 動作 | Microsoft.ClassicCompute/domainNames/swap/action | 將預備位置切換到生產位置。 |
> |  | **domainNames/active** |  |
> | 動作 | Microsoft.ClassicCompute/domainNames/active/write | 設定作用中的網域名稱。 |
> |  | **domainNames/availabilitySets** |  |
> | 動作 | Microsoft.ClassicCompute/domainNames/availabilitySets/read | 顯示資源的可用性設定組。 |
> |  | **domainNames/功能** |  |
> | 動作 | Microsoft.ClassicCompute/domainNames/capabilities/read | 顯示網域名稱功能 |
> |  | **domainNames/deploymentslots** |  |
> | 動作 | Microsoft.ClassicCompute/domainNames/deploymentslots/read | 顯示部署位置。 |
> | 動作 | Microsoft.ClassicCompute/domainNames/deploymentslots/write | 建立或更新部署。 |
> |  | **domainNames/deploymentslots/角色** |  |
> | 動作 | Microsoft.ClassicCompute/domainNames/deploymentslots/roles/read | 取得網域名稱之部署位置上的角色 |
> |  | **domainNames/deploymentslots/roles/roleinstances** |  |
> | 動作 | Microsoft.ClassicCompute/domainNames/deploymentslots/roles/roleinstances/read | 取得網域名稱之部署位置上的角色執行個體 |
> |  | **domainNames/deploymentslots/state** |  |
> | 動作 | Microsoft.ClassicCompute/domainNames/deploymentslots/state/read | 取得部署位置狀態。 |
> | 動作 | Microsoft.ClassicCompute/domainNames/deploymentslots/state/write | 新增部署位置狀態。 |
> |  | **domainNames/deploymentslots/upgradedomain** |  |
> | 動作 | Microsoft.ClassicCompute/domainNames/deploymentslots/upgradedomain/read | 取得網域名稱上之部署位置的升級網域 |
> | 動作 | Microsoft.ClassicCompute/domainNames/deploymentslots/upgradedomain/write | 更新網域名稱上之部署位置的升級網域 |
> |  | **domainNames/extensions** |  |
> | 動作 | Microsoft.ClassicCompute/domainNames/extensions/read | 傳回網域名稱副檔名。 |
> | 動作 | Microsoft.ClassicCompute/domainNames/extensions/write | 新增網域名稱副檔名。 |
> | 動作 | Microsoft.ClassicCompute/domainNames/extensions/delete | 移除網域名稱副檔名。 |
> |  | **domainNames/extensions/operationStatuses** |  |
> | 動作 | Microsoft.ClassicCompute/domainNames/extensions/operationStatuses/read | 讀取網域名稱副檔名的作業狀態。 |
> |  | **domainNames/internalLoadBalancers** |  |
> | 動作 | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/read | 取得內部負載平衡器。 |
> | 動作 | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/write | 建立新的內部負載平衡。 |
> | 動作 | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/delete | 移除新的內部負載平衡。 |
> |  | **domainNames/internalLoadBalancers/operationStatuses** |  |
> | 動作 | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/operationStatuses/read | 讀取網域名稱內部負載平衡器的作業狀態。 |
> |  | **domainNames/loadBalancedEndpointSets** |  |
> | 動作 | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/read | 取得已負載平衡的端點集。 |
> | 動作 | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/write | 新增已負載平衡的端點集。 |
> |  | **domainNames/loadBalancedEndpointSets/operationStatuses** |  |
> | 動作 | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/operationStatuses/read | 讀取網域名稱之已負載平衡端點集的作業狀態。 |
> |  | **domainNames/operationstatuses** |  |
> | 動作 | Microsoft.ClassicCompute/domainNames/operationstatuses/read | 取得網域名稱的作業狀態。 |
> | 動作 | Microsoft.ClassicCompute/domainNames/operationStatuses/read | 讀取網域名稱副檔名的作業狀態。 |
> |  | **domainNames/serviceCertificates** |  |
> | 動作 | Microsoft.ClassicCompute/domainNames/serviceCertificates/read | 傳回所使用的服務憑證。 |
> | 動作 | Microsoft.ClassicCompute/domainNames/serviceCertificates/write | 新增或修改所使用的服務憑證。 |
> | 動作 | Microsoft.ClassicCompute/domainNames/serviceCertificates/delete | 刪除所使用的服務憑證。 |
> |  | **domainNames/serviceCertificates/operationStatuses** |  |
> | 動作 | Microsoft.ClassicCompute/domainNames/serviceCertificates/operationStatuses/read | 讀取網域名稱服務憑證的作業狀態。 |
> |  | **domainNames/插槽** |  |
> | 動作 | Microsoft.ClassicCompute/domainNames/slots/read | 顯示部署位置。 |
> | 動作 | Microsoft.ClassicCompute/domainNames/slots/write | 建立或更新部署。 |
> | 動作 | Microsoft.ClassicCompute/domainNames/slots/delete | 刪除給定的部署位置。 |
> | 動作 | Microsoft.ClassicCompute/domainNames/slots/start/action | 啟動部署位置。 |
> | 動作 | Microsoft.ClassicCompute/domainNames/slots/stop/action | 暫止部署位置。 |
> | 動作 | Microsoft.ClassicCompute/domainNames/slots/validateMigration/action | 驗證部署位置的移轉。 |
> | 動作 | Microsoft.ClassicCompute/domainNames/slots/prepareMigration/action | 準備部署位置的移轉。 |
> | 動作 | Microsoft.ClassicCompute/domainNames/slots/commitMigration/action | 認可部署位置的移轉。 |
> | 動作 | Microsoft.ClassicCompute/domainNames/slots/abortMigration/action | 中止部署位置的移轉。 |
> |  | **domainNames/插槽/operationStatuses** |  |
> | 動作 | Microsoft.ClassicCompute/domainNames/slots/operationStatuses/read | 讀取網域名稱位置的作業狀態。 |
> |  | **domainNames/位置/角色** |  |
> | 動作 | Microsoft.ClassicCompute/domainNames/slots/roles/read | 取得部署位置的角色。 |
> | 動作 | Microsoft.ClassicCompute/domainNames/slots/roles/write | 新增部署位置的角色。 |
> |  | **domainNames/位置/角色/extensionReferences** |  |
> | 動作 | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/read | 傳回部署位置角色的擴充功能參考。 |
> | 動作 | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/write | 新增或修改部署位置角色的擴充功能參考。 |
> | 動作 | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/delete | 移除部署位置角色的擴充功能參考。 |
> |  | **domainNames/位置/角色/extensionReferences/operationStatuses** |  |
> | 動作 | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/operationStatuses/read | 讀取網域名稱位置角色擴充功能參考的作業狀態。 |
> |  | **domainNames/位置/角色/metricdefinitions.listasync** |  |
> | 動作 | Microsoft.ClassicCompute/domainNames/slots/roles/metricdefinitions/read | 取得網域名稱的角色計量定義。 |
> |  | **domainNames/位置/角色/計量** |  |
> | 動作 | Microsoft.ClassicCompute/domainNames/slots/roles/metrics/read | 取得網域名稱的角色計量。 |
> |  | **domainNames/位置/角色/operationstatuses** |  |
> | 動作 | Microsoft.ClassicCompute/domainNames/slots/roles/operationstatuses/read | 取得網域名稱位置角色的作業狀態。 |
> |  | **domainNames/位置/角色/提供者/Microsoft Insights/diagnosticSettings** |  |
> | 動作 | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/read | 取得診斷設定。 |
> | 動作 | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/write | 新增或修改診斷設定。 |
> |  | **domainNames/位置/角色/提供者/Microsoft Insights/Metricdefinitions.listasync** |  |
> | 動作 | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/metricDefinitions/read | 取得計量定義。 |
> |  | **domainNames/位置/角色/roleInstances** |  |
> | 動作 | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/downloadremotedesktopconnectionfile/action | 下載網域名稱位置角色上角色執行個體的遠端桌面連線檔案。 |
> | 動作 | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/read | 取得角色執行個體。 |
> | 動作 | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/restart/action | 重新啟動角色執行個體。 |
> | 動作 | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/reimage/action | 針對角色執行個體重新安裝映像。 |
> | 動作 | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/rebuild/action | 重建角色執行個體。 |
> |  | **domainNames/位置/角色/roleInstances/operationStatuses** |  |
> | 動作 | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/operationStatuses/read | 取得網域名稱位置角色上角色執行個體的作業狀態。 |
> |  | **domainNames/位置/角色/sku** |  |
> | 動作 | Microsoft.ClassicCompute/domainNames/slots/roles/skus/read | 取得部署位置的角色 SKU。 |
> |  | **domainNames/插槽/狀態/啟動** |  |
> | 動作 | Microsoft.ClassicCompute/domainNames/slots/state/start/write | 將部署位置狀態變更為停止。 |
> |  | **domainNames/插槽/狀態/停止** |  |
> | 動作 | Microsoft.ClassicCompute/domainNames/slots/state/stop/write | 將部署位置狀態變更為啟動。 |
> |  | **domainNames/插槽/upgradeDomain** |  |
> | 動作 | Microsoft.ClassicCompute/domainNames/slots/upgradeDomain/write | 逐步升級網域。 |
> |  | **operatingSystemFamilies** |  |
> | 動作 | Microsoft.ClassicCompute/operatingSystemFamilies/read | 列出可在 Microsoft Azure 使用的客體作業系統系列，同時也會列出各系列適用的作業系統版本。 |
> |  | **operatingSystems** |  |
> | 動作 | Microsoft.ClassicCompute/operatingSystems/read | 列出 Microsoft Azure 中目前可用的客體作業系統版本。 |
> |  | **業務** |  |
> | 動作 | Microsoft.ClassicCompute/operations/read | 取得作業的清單。 |
> |  | **operationStatuses** |  |
> | 動作 | Microsoft.ClassicCompute/operationStatuses/read | 讀取資源的作業狀態。 |
> |  | **quotas** |  |
> | 動作 | Microsoft.ClassicCompute/quotas/read | 取得訂用帳戶配額。 |
> |  | **resourceTypes/sku** |  |
> | 動作 | Microsoft.ClassicCompute/resourceTypes/skus/read | 取得受支援之資源類型的 SKU 清單。 |
> |  | **virtualMachines** |  |
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
> |  | **virtualMachines/associatedNetworkSecurityGroups** |  |
> | 動作 | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/read | 取得與虛擬機器相關聯的網路安全性群組。 |
> | 動作 | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/write | 新增與虛擬機器相關聯的網路安全性群組。 |
> | 動作 | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/delete | 刪除與虛擬機器相關聯的網路安全性群組。 |
> |  | **virtualMachines/associatedNetworkSecurityGroups/operationStatuses** |  |
> | 動作 | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/operationStatuses/read | 讀取與網路安全性群組相關聯之虛擬機器的作業狀態。 |
> |  | **virtualMachines/asyncOperations** |  |
> | 動作 | Microsoft.ClassicCompute/virtualMachines/asyncOperations/read | 取得可能的非同步作業 |
> |  | **virtualMachines/diagnosticsettings** |  |
> | 動作 | Microsoft.ClassicCompute/virtualMachines/diagnosticsettings/read | 取得虛擬機器診斷設定。 |
> |  | **virtualMachines/磁片** |  |
> | 動作 | Microsoft.ClassicCompute/virtualMachines/disks/read | 擷取資料磁碟清單 |
> |  | **virtualMachines/extensions** |  |
> | 動作 | Microsoft.ClassicCompute/virtualMachines/extensions/read | 取得虛擬機器擴充功能。 |
> | 動作 | Microsoft.ClassicCompute/virtualMachines/extensions/write | 放置虛擬機器擴充功能。 |
> |  | **virtualMachines/extensions/operationStatuses** |  |
> | 動作 | Microsoft.ClassicCompute/virtualMachines/extensions/operationStatuses/read | 讀取虛擬機器擴充功能的作業狀態。 |
> |  | **virtualMachines/metricdefinitions.listasync** |  |
> | 動作 | Microsoft.ClassicCompute/virtualMachines/metricdefinitions/read | 取得虛擬機器計量定義。 |
> |  | **virtualMachines/計量** |  |
> | 動作 | Microsoft.ClassicCompute/virtualMachines/metrics/read | 取得計量。 |
> |  | **virtualMachines/networkInterfaces/associatedNetworkSecurityGroups** |  |
> | 動作 | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/read | 取得與網路介面相關聯的網路安全性群組。 |
> | 動作 | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/write | 新增與網路介面相關聯的網路安全性群組。 |
> | 動作 | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/delete | 刪除與網路介面相關聯的網路安全性群組。 |
> |  | **virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/operationStatuses** |  |
> | 動作 | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/operationStatuses/read | 讀取與網路安全性群組相關聯之虛擬機器的作業狀態。 |
> |  | **virtualMachines/operationStatuses** |  |
> | 動作 | Microsoft.ClassicCompute/virtualMachines/operationStatuses/read | 讀取虛擬機器的作業狀態。 |
> |  | **virtualMachines/providers/Microsoft Insights/diagnosticSettings** |  |
> | 動作 | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/read | 取得診斷設定。 |
> | 動作 | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/write | 新增或修改診斷設定。 |
> |  | **virtualMachines/providers/Microsoft Insights/Metricdefinitions.listasync** |  |
> | 動作 | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read | 取得計量定義。 |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

Azure 服務：傳統部署模型虛擬網路

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.ClassicNetwork/register/action | 傳統網路的暫存器 |
> |  | **expressroutecrossconnections** |  |
> | 動作 | Microsoft.ClassicNetwork/expressroutecrossconnections/read | 取得 Express Route 交叉連線。 |
> | 動作 | Microsoft.ClassicNetwork/expressroutecrossconnections/write | 新增 Express Route 交叉連線。 |
> |  | **expressroutecrossconnections/operationstatuses** |  |
> | 動作 | Microsoft.ClassicNetwork/expressroutecrossconnections/operationstatuses/read | 取得 Express Route 交叉連線作業狀態。 |
> |  | **expressroutecrossconnections/對等互連** |  |
> | 動作 | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/read | 取得 Express Route 交叉連線對等互連。 |
> | 動作 | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/write | 新增 Express Route 交叉連線對等互連。 |
> | 動作 | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/delete | 刪除 Express Route 交叉連線對等互連。 |
> |  | **expressroutecrossconnections/對等互連/operationstatuses** |  |
> | 動作 | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/operationstatuses/read | 取得 Express Route 交叉連線對等互連作業狀態。 |
> |  | **gatewaySupportedDevices** |  |
> | 動作 | Microsoft.ClassicNetwork/gatewaySupportedDevices/read | 擷取支援裝置清單。 |
> |  | **networkSecurityGroups** |  |
> | 動作 | Microsoft.ClassicNetwork/networkSecurityGroups/read | 取得網路安全性群組。 |
> | 動作 | Microsoft.ClassicNetwork/networkSecurityGroups/write | 新增網路安全性群組。 |
> | 動作 | Microsoft.ClassicNetwork/networkSecurityGroups/delete | 刪除網路安全性群組。 |
> |  | **networkSecurityGroups/operationStatuses** |  |
> | 動作 | Microsoft.ClassicNetwork/networkSecurityGroups/operationStatuses/read | 讀取網路安全性群組的作業狀態。 |
> |  | **networksecuritygroups/providers/Microsoft Insights/diagnosticSettings** |  |
> | 動作 | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/read | 取得網路安全性群組診斷設定 |
> | 動作 | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/write | 建立或更新網路安全性群組診斷設定，此作業會由 Insights 資源提供者來補充。 |
> |  | **networksecuritygroups/providers/Microsoft Insights/logDefinitions** |  |
> | 動作 | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/logDefinitions/read | 取得網路安全性群組的事件 |
> |  | **networkSecurityGroups/securityRules** |  |
> | 動作 | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/read | 取得安全性規則。 |
> | 動作 | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/write | 新增或更新安全性規則。 |
> | 動作 | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/delete | 刪除安全性規則。 |
> |  | **networkSecurityGroups/securityRules/operationStatuses** |  |
> | 動作 | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/operationStatuses/read | 讀取網路安全性群組安全性規則的作業狀態。 |
> |  | **業務** |  |
> | 動作 | Microsoft.ClassicNetwork/operations/read | 取得傳統網路作業。 |
> |  | **quotas** |  |
> | 動作 | Microsoft.ClassicNetwork/quotas/read | 取得訂用帳戶配額。 |
> |  | **reservedIps** |  |
> | 動作 | Microsoft.ClassicNetwork/reservedIps/read | 取得保留的 IP |
> | 動作 | Microsoft.ClassicNetwork/reservedIps/write | 新增保留的 IP |
> | 動作 | Microsoft.ClassicNetwork/reservedIps/delete | 刪除保留的 IP。 |
> | 動作 | Microsoft.ClassicNetwork/reservedIps/link/action | 連結保留的 IP |
> | 動作 | Microsoft.ClassicNetwork/reservedIps/join/action | 加入保留的 IP |
> |  | **reservedIps/operationStatuses** |  |
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
> |  | **virtualNetworks/功能** |  |
> | 動作 | Microsoft.ClassicNetwork/virtualNetworks/capabilities/read | 顯示功能 |
> |  | **virtualNetworks/閘道** |  |
> | 動作 | Microsoft.ClassicNetwork/virtualNetworks/gateways/read | 取得虛擬網路閘道。 |
> | 動作 | Microsoft.ClassicNetwork/virtualNetworks/gateways/write | 新增虛擬網路閘道。 |
> | 動作 | Microsoft.ClassicNetwork/virtualNetworks/gateways/delete | 刪除虛擬網路閘道。 |
> | 動作 | Microsoft.ClassicNetwork/virtualNetworks/gateways/startDiagnostics/action | 啟動虛擬網路閘道的診斷。 |
> | 動作 | Microsoft.ClassicNetwork/virtualNetworks/gateways/stopDiagnostics/action | 停止虛擬網路閘道的診斷。 |
> | 動作 | Microsoft.ClassicNetwork/virtualNetworks/gateways/downloadDiagnostics/action | 下載閘道診斷。 |
> | 動作 | Microsoft.ClassicNetwork/virtualNetworks/gateways/listCircuitServiceKey/action | 擷取迴路服務金鑰。 |
> | 動作 | Microsoft.ClassicNetwork/virtualNetworks/gateways/downloadDeviceConfigurationScript/action | 下載裝置組態指令碼。 |
> | 動作 | Microsoft.ClassicNetwork/virtualNetworks/gateways/listPackage/action | 列出虛擬網路閘道套件。 |
> |  | **virtualNetworks/閘道/clientRevokedCertificates** |  |
> | 動作 | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/read | 讀取已撤銷的用戶端憑證。 |
> | 動作 | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/write | 撤銷用戶端憑證。 |
> | 動作 | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/delete | 取消撤銷用戶端憑證。 |
> |  | **virtualNetworks/閘道/clientRootCertificates** |  |
> | 動作 | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/read | 尋找用戶端根憑證。 |
> | 動作 | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/write | 上傳新的用戶端根憑證。 |
> | 動作 | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/delete | 刪除虛擬網路閘道用戶端憑證。 |
> | 動作 | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/download/action | 依指紋下載憑證。 |
> | 動作 | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/listPackage/action | 列出虛擬網路閘道憑證套件。 |
> |  | **virtualNetworks/閘道/連線** |  |
> | 動作 | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/read | 擷取連線清單。 |
> | 動作 | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/connect/action | 建立網站對網站閘道連線。 |
> | 動作 | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/disconnect/action | 中斷網站對網站閘道連線。 |
> | 動作 | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/test/action | 測試網站對網站閘道連線。 |
> |  | **virtualNetworks/閘道/operationStatuses** |  |
> | 動作 | Microsoft.ClassicNetwork/virtualNetworks/gateways/operationStatuses/read | 讀取虛擬網路閘道的作業狀態。 |
> |  | **virtualNetworks/閘道/套件** |  |
> | 動作 | Microsoft.ClassicNetwork/virtualNetworks/gateways/packages/read | 取得虛擬網路閘道套件。 |
> |  | **virtualNetworks/operationStatuses** |  |
> | 動作 | Microsoft.ClassicNetwork/virtualNetworks/operationStatuses/read | 讀取虛擬網路的作業狀態。 |
> |  | **virtualNetworks/remoteVirtualNetworkPeeringProxies** |  |
> | 動作 | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/read | 取得遠端虛擬網路對等互連 Proxy。 |
> | 動作 | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/write | 新增或更新遠端虛擬網路對等互連 Proxy。 |
> | 動作 | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/delete | 刪除遠端虛擬網路對等互連 Proxy。 |
> |  | **virtualNetworks/子網/associatedNetworkSecurityGroups** |  |
> | 動作 | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/read | 取得與子網路相關聯的網路安全性群組。 |
> | 動作 | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/write | 新增與子網路相關聯的網路安全性群組。 |
> | 動作 | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/delete | 刪除與子網路相關聯的網路安全性群組。 |
> |  | **virtualNetworks/subnet/associatedNetworkSecurityGroups/operationStatuses** |  |
> | 動作 | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/operationStatuses/read | 讀取與網路安全性群組相關聯之虛擬網路子網路的作業狀態。 |
> |  | **virtualNetworks/virtualNetworkPeerings** |  |
> | 動作 | Microsoft.ClassicNetwork/virtualNetworks/virtualNetworkPeerings/read | 取得虛擬網路對等互連。 |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

Azure 服務：傳統部署模型儲存體

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.ClassicStorage/register/action | 傳統儲存體的暫存器 |
> | 動作 | Microsoft.ClassicStorage/checkStorageAccountAvailability/action | 檢查儲存體帳戶的可用性。 |
> |  | **功能** |  |
> | 動作 | Microsoft.ClassicStorage/capabilities/read | 顯示功能 |
> |  | **checkStorageAccountAvailability** |  |
> | 動作 | Microsoft.ClassicStorage/checkStorageAccountAvailability/read | 取得儲存體帳戶的可用性。 |
> |  | **盤** |  |
> | 動作 | Microsoft.ClassicStorage/disks/read | 傳回儲存體帳戶磁碟。 |
> |  | **images** |  |
> | 動作 | Microsoft.ClassicStorage/images/read | 傳回映像。 |
> |  | **images/operationstatuses** |  |
> | 動作 | Microsoft.ClassicStorage/images/operationstatuses/read | 取得映像作業狀態。 |
> |  | **業務** |  |
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
> |  | **storageAccounts/blobServices/providers/Microsoft Insights/diagnosticSettings** |  |
> | 動作 | Microsoft.classicstorage/storageAccounts/blobServices/provider/Microsoft Insights/diagnosticSettings/read | 取得診斷設定。 |
> | 動作 | Microsoft.classicstorage/storageAccounts/blobServices/provider/Microsoft Insights/diagnosticSettings/write | 新增或修改診斷設定。 |
> |  | **storageAccounts/blobServices/providers/Microsoft Insights/Metricdefinitions.listasync** |  |
> | 動作 | Microsoft.classicstorage/storageAccounts/blobServices/provider/Microsoft Insights/Metricdefinitions.listasync/read | 取得計量定義。 |
> |  | **storageAccounts/磁片** |  |
> | 動作 | Microsoft.ClassicStorage/storageAccounts/disks/read | 傳回儲存體帳戶磁碟。 |
> | 動作 | Microsoft.ClassicStorage/storageAccounts/disks/write | 新增儲存體帳戶磁碟。 |
> | 動作 | Microsoft.ClassicStorage/storageAccounts/disks/delete | 刪除給定的儲存體帳戶磁碟。 |
> |  | **storageAccounts/磁片/operationStatuses** |  |
> | 動作 | Microsoft.ClassicStorage/storageAccounts/disks/operationStatuses/read | 讀取資源的作業狀態。 |
> |  | **storageAccounts/fileServices/providers/Microsoft Insights/diagnosticSettings** |  |
> | 動作 | Microsoft.classicstorage/storageAccounts/fileServices/provider/Microsoft Insights/diagnosticSettings/read | 取得診斷設定。 |
> | 動作 | Microsoft.classicstorage/storageAccounts/fileServices/provider/Microsoft Insights/diagnosticSettings/write | 新增或修改診斷設定。 |
> |  | **storageAccounts/fileServices/providers/Microsoft Insights/Metricdefinitions.listasync** |  |
> | 動作 | Microsoft.classicstorage/storageAccounts/fileServices/provider/Microsoft Insights/Metricdefinitions.listasync/read | 取得計量定義。 |
> |  | **storageAccounts/images** |  |
> | 動作 | Microsoft.ClassicStorage/storageAccounts/images/read | 傳回儲存體帳戶映像。 (已淘汰。 使用 'Microsoft.ClassicStorage/storageAccounts/vmImages') |
> | 動作 | Microsoft.ClassicStorage/storageAccounts/images/delete | 刪除給定的儲存體帳戶映像。 (已淘汰。 使用 'Microsoft.ClassicStorage/storageAccounts/vmImages') |
> |  | **storageAccounts/images/operationstatuses** |  |
> | 動作 | Microsoft.ClassicStorage/storageAccounts/images/operationstatuses/read | 傳回儲存體帳戶映像作業狀態。 |
> |  | **storageAccounts/operationStatuses** |  |
> | 動作 | Microsoft.ClassicStorage/storageAccounts/operationStatuses/read | 讀取資源的作業狀態。 |
> |  | **storageAccounts/osImages** |  |
> | 動作 | Microsoft.ClassicStorage/storageAccounts/osImages/read | 傳回儲存體帳戶的作業系統映像。 |
> | 動作 | Microsoft.ClassicStorage/storageAccounts/osImages/write | 新增給定儲存體帳戶作業系統映像。 |
> | 動作 | Microsoft.ClassicStorage/storageAccounts/osImages/delete | 刪除給定之儲存體帳戶的作業系統映像。 |
> |  | **storageAccounts/providers/Microsoft Insights/diagnosticSettings** |  |
> | 動作 | Microsoft.classicstorage/storageAccounts/provider/Microsoft Insights/diagnosticSettings/read | 取得診斷設定。 |
> | 動作 | Microsoft.classicstorage/storageAccounts/provider/Microsoft Insights/diagnosticSettings/write | 新增或修改診斷設定。 |
> |  | **storageAccounts/providers/Microsoft Insights/Metricdefinitions.listasync** |  |
> | 動作 | Microsoft.classicstorage/storageAccounts/provider/Microsoft Insights/Metricdefinitions.listasync/read | 取得計量定義。 |
> |  | **storageAccounts/queueServices/providers/Microsoft Insights/diagnosticSettings** |  |
> | 動作 | Microsoft.classicstorage/storageAccounts/queueServices/provider/Microsoft Insights/diagnosticSettings/read | 取得診斷設定。 |
> | 動作 | Microsoft.classicstorage/storageAccounts/queueServices/provider/Microsoft Insights/diagnosticSettings/write | 新增或修改診斷設定。 |
> |  | **storageAccounts/queueServices/providers/Microsoft Insights/Metricdefinitions.listasync** |  |
> | 動作 | Microsoft.classicstorage/storageAccounts/queueServices/provider/Microsoft Insights/Metricdefinitions.listasync/read | 取得計量定義。 |
> |  | **storageAccounts/服務** |  |
> | 動作 | Microsoft.ClassicStorage/storageAccounts/services/read | 取得可用服務。 |
> |  | **storageAccounts/services/diagnosticSettings** |  |
> | 動作 | Microsoft.ClassicStorage/storageAccounts/services/diagnosticSettings/read | 取得診斷設定。 |
> | 動作 | Microsoft.ClassicStorage/storageAccounts/services/diagnosticSettings/write | 新增或修改診斷設定。 |
> |  | **storageAccounts/services/Metricdefinitions.listasync** |  |
> | 動作 | Microsoft.ClassicStorage/storageAccounts/services/metricDefinitions/read | 取得計量定義。 |
> |  | **storageAccounts/服務/計量** |  |
> | 動作 | Microsoft.ClassicStorage/storageAccounts/services/metrics/read | 取得計量。 |
> |  | **storageAccounts/tableServices/providers/Microsoft Insights/diagnosticSettings** |  |
> | 動作 | Microsoft.classicstorage/storageAccounts/tableServices/provider/Microsoft Insights/diagnosticSettings/read | 取得診斷設定。 |
> | 動作 | Microsoft.classicstorage/storageAccounts/tableServices/provider/Microsoft Insights/diagnosticSettings/write | 新增或修改診斷設定。 |
> |  | **storageAccounts/tableServices/providers/Microsoft Insights/Metricdefinitions.listasync** |  |
> | 動作 | Microsoft.classicstorage/storageAccounts/tableServices/provider/Microsoft Insights/Metricdefinitions.listasync/read | 取得計量定義。 |
> |  | **storageAccounts/vmImages** |  |
> | 動作 | Microsoft.ClassicStorage/storageAccounts/vmImages/read | 傳回虛擬機器映像。 |
> | 動作 | Microsoft.ClassicStorage/storageAccounts/vmImages/write | 新增指定的虛擬機器映像。 |
> | 動作 | Microsoft.ClassicStorage/storageAccounts/vmImages/delete | 刪除指定的虛擬機器映像。 |
> |  | **storageAccounts/vmImages/operationstatuses** |  |
> | 動作 | Microsoft.ClassicStorage/storageAccounts/vmImages/operationstatuses/read | 取得指定的虛擬機器映像作業狀態。 |
> |  | **vmImages** |  |
> | 動作 | Microsoft.ClassicStorage/vmImages/read | 列出虛擬機器映像。 |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

Azure 服務：[認知服務](../cognitive-services/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.CognitiveServices/register/action | 訂用帳戶註冊動作 |
> | 動作 | Microsoft.CognitiveServices/register/action | 為認知服務註冊訂用帳戶 |
> | 動作 | CognitiveServices/checkDomainAvailability/action | 讀取訂用帳戶的可用 Sku。 |
> | 動作 | Microsoft.CognitiveServices/register/action | 為認知服務註冊訂用帳戶 |
> |  | **帳目** |  |
> | 動作 | Microsoft.CognitiveServices/accounts/read | 讀取 API 帳戶。 |
> | 動作 | Microsoft.CognitiveServices/accounts/write | 寫入 API 帳戶。 |
> | 動作 | Microsoft.CognitiveServices/accounts/delete | 刪除 API 帳戶 |
> | 動作 | Microsoft.CognitiveServices/accounts/listKeys/action | 列出金鑰 |
> | 動作 | Microsoft.CognitiveServices/accounts/regenerateKey/action | 重新產生金鑰 |
> |  | **帳戶/sku** |  |
> | 動作 | Microsoft.CognitiveServices/accounts/skus/read | 讀取現有資源的可用 SKU。 |
> |  | **帳戶/使用方式** |  |
> | 動作 | Microsoft.CognitiveServices/accounts/usages/read | 取得現有資源的配額使用情況。 |
> |  | **堆放** |  |
> | 動作 | Microsoft.CognitiveServices/locations/checkSkuAvailability/action | 讀取訂用帳戶的可用 Sku。 |
> | 動作 | CognitiveServices/位置/deleteVirtualNetworkOrSubnets/動作 | 來自 Microsoft 的通知正在刪除 VirtualNetworks 或子網。 |
> | 動作 | Microsoft.CognitiveServices/locations/checkSkuAvailability/action | 讀取訂用帳戶的可用 Sku。 |
> |  | **位置/operationresults** |  |
> | 動作 | CognitiveServices/位置/operationresults/讀取 | 讀取非同步作業的狀態。 |
> |  | **作業** |  |
> | 動作 | Microsoft.CognitiveServices/Operations/read | 列出所有可用的作業 |
> |  | **accounts/AnomalyDetector/時間序列/整個** |  |
> | DataAction | CognitiveServices/accounts/AnomalyDetector/時間序列/整個/偵測/動作 | 這項作業會使用整個數列來產生模型，並使用相同的模型偵測到每個點。<br>使用這個方法時，會使用特定點前後的點來判斷它是否為異常。<br>整個偵測可為使用者提供時間序列的整體狀態。 |
> |  | **accounts/AnomalyDetector/時間序列/last** |  |
> | DataAction | CognitiveServices/accounts/AnomalyDetector/時間序列/last/偵測/action | 這項作業會使用最新的點來產生模型。 使用此方法時，只會使用歷程記錄點來判斷目標點是否為異常。 偵測到的最新點符合商務計量的即時監視案例。 |
> |  | **帳戶/自動建議** |  |
> | DataAction | CognitiveServices/帳戶/自動建議/搜尋/動作 | 這種作業會提供給定查詢或部分查詢的建議。 |
> |  | **帳戶/ComputerVision** |  |
> | DataAction | CognitiveServices/accounts/ComputerVision/分析/動作 | 這項作業會根據影像內容來解壓縮一組豐富的視覺功能。  |
> | DataAction | CognitiveServices/accounts/ComputerVision/說明/動作 | 這項作業會使用完整的句子，以人類看得懂的語言來產生影像的描述。<br> 這項描述是以內容標記的集合為基礎，這也會由作業傳回。<br>可以為每個影像產生一個以上的描述。<br> 描述會依其信賴分數排序。<br>所有描述都是英文。 |
> | DataAction | CognitiveServices/accounts/ComputerVision/generatethumbnail/action | 此作業會產生具有使用者指定寬度和高度的縮圖影像。<br> 根據預設，服務會分析影像、識別感利率的區域（ROI），並依據 ROI 產生智慧裁剪座標。<br> 當您指定與輸入影像不同的外觀比例時，智慧型裁剪會有所説明 |
> | DataAction | CognitiveServices/accounts/ComputerVision/ocr/action | 光學字元辨識（OCR）會偵測影像中的文字，並將已辨識的字元解壓縮至電腦可用的字元資料流。    |
> | DataAction | CognitiveServices/accounts/ComputerVision/recognizetext/action | 使用此介面來取得辨識文字作業的結果。 當您使用辨識文字介面時，回應會包含名為 "Operation-Location" 的欄位。 [作業-位置] 欄位包含您必須用於「取得辨識文字」作業結果作業的 URL。 |
> | DataAction | CognitiveServices/accounts/ComputerVision/tag/action | 這項作業會產生與所提供影像內容相關的單字或標籤清單。<br>電腦視覺 API 可以根據在影像中找到的物件、景象或動作傳回標記。<br>不同于類別，標記不會根據階層式分類系統來組織，而是對應至影像內容。<br>標記可能包含避免不明確或提供內容的提示，例如，標記 "cello" 可能會伴隨提示 "樂器"。<br>所有標記都是英文。 |
> | DataAction | CognitiveServices/accounts/ComputerVision/areaofinterest/action | 這項作業會傳回影像中最重要區域周圍的周框方塊。 |
> | DataAction | CognitiveServices/accounts/ComputerVision/偵測/action | 此作業會在指定的映射上執行物件偵測。  |
> |  | **帳戶/ComputerVision/模型** |  |
> | DataAction | CognitiveServices/accounts/ComputerVision/型號/read | 這項作業會傳回電腦視覺 API 支援的特定領域模型清單。  目前，此 API 支援下列特定領域模型：名人辨識器、地標辨識器。 |
> | DataAction | CognitiveServices/accounts/ComputerVision/模型/分析/動作 | 這項作業會藉由套用特定領域模型來辨識影像中的內容。<br> 您可以使用/models GET 要求來抓取電腦視覺 API 所支援的特定領域模型清單。<br> 目前，API 提供下列特定領域模型：名人、地標。 |
> |  | **帳戶/ComputerVision/讀取** |  |
> | DataAction | CognitiveServices/accounts/ComputerVision/讀取/分析/動作 | 使用此介面來執行讀取作業，並採用針對大量文字檔優化的最先進光學字元辨識（OCR）演算法。<br>它可以處理右手寫、列印或混合檔。<br>當您使用讀取介面時，回應會包含名為「作業-位置」的標頭。<br>「作業-位置」標頭包含您必須用於「取得讀取結果」作業以存取 OCR 結果的 URL。 |
> |  | **帳戶/ComputerVision/讀取/analyzeresults** |  |
> | DataAction | CognitiveServices/accounts/ComputerVision/read/analyzeresults/read | 使用此介面來取出讀取作業的狀態和 OCR 結果。  在讀取作業的「作業-位置」回應標頭中，會傳回包含 ' operationId ' 的 URL。 |
> |  | **帳戶/ComputerVision/讀取/核心** |  |
> | DataAction | CognitiveServices/accounts/ComputerVision/讀取/核心/asyncbatchanalyze/動作 | 使用此介面來取得批次讀取檔案作業的結果，並採用最先進的光學字元 |
> |  | **帳戶/ComputerVision/讀取/作業** |  |
> | DataAction | CognitiveServices/accounts/ComputerVision/讀取/作業/讀取 | 這個介面是用來取得讀取作業的 OCR 結果。 應從批次讀取檔案介面傳回的「作業<b>-位置</b>」欄位，抓取此介面的 URL。 |
> |  | **accounts/ComputerVision/textoperations** |  |
> | DataAction | CognitiveServices/accounts/ComputerVision/textoperations/read | 這個介面是用來取得辨識文字作業結果。 應從辨識文字介面傳回的「作業<b>-位置</b>」欄位，抓取此介面的 URL。 |
> |  | **帳戶/ContentModerator** |  |
> | DataAction | CognitiveServices/accounts/ContentModerator/imagelists/action | 建立影像清單。 |
> | DataAction | CognitiveServices/accounts/ContentModerator/termlists、program/action | 建立字詞清單。 |
> |  | **accounts/ContentModerator/imagelists** |  |
> | DataAction | CognitiveServices/accounts/ContentModerator/imagelists/read | 影像清單-取得詳細資料-影像清單-全部取得 |
> | DataAction | CognitiveServices/accounts/ContentModerator/imagelists/delete | 影像清單-刪除 |
> | DataAction | CognitiveServices/accounts/ContentModerator/imagelists/refreshindex/action | 影像清單-重新整理搜尋索引 |
> | DataAction | CognitiveServices/accounts/ContentModerator/imagelists/write | 影像清單-更新詳細資料 |
> |  | **accounts/ContentModerator/imagelists/images** |  |
> | DataAction | CognitiveServices/accounts/ContentModerator/imagelists/images/write | 將影像新增至您的影像清單。 使用影像/比對 API 時，影像清單可以用來對其他映射執行模糊比對。 |
> | DataAction | CognitiveServices/accounts/ContentModerator/imagelists/images/delete | 刪除影像清單中的影像。 使用影像/比對 API 時，影像清單可以用來對其他映射執行模糊比對。 刪除清單中的所有映射。 使用影像/比對 API 時，影像清單可以用來對其他映射執行模糊比對。 * |
> | DataAction | CognitiveServices/accounts/ContentModerator/imagelists/images/read | 影像-取得所有映射識別碼 |
> |  | **accounts/ContentModerator/processimage** |  |
> | DataAction | CognitiveServices/accounts/ContentModerator/processimage/評估/動作 | 傳回包含猥褻或成人內容之影像的機率。 |
> | DataAction | CognitiveServices/accounts/ContentModerator/processimage/findfaces/action | 尋找影像中的臉部。 |
> | DataAction | CognitiveServices/accounts/ContentModerator/processimage/match/action | Fuzzily 會比對影像與您的其中一個自訂影像清單。 您可以使用此 API 來建立及管理您的自訂映射清單。  |
> | DataAction | CognitiveServices/accounts/ContentModerator/processimage/ocr/action | 針對指定的語言，傳回在影像中找到的任何文字。 如果輸入中未指定任何語言，則偵測會預設為英文。 |
> |  | **accounts/ContentModerator/processtext** |  |
> | DataAction | CognitiveServices/accounts/ContentModerator/processtext/detectlanguage/action | 這項作業會偵測指定輸入內容的語言。 傳回組成提交文字之主要語言的 ISO 639-3 代碼。 支援110以上的語言。 |
> | DataAction | CognitiveServices/accounts/ContentModerator/processtext/螢幕/動作 | 作業會偵測超過100語言的不雅內容，並符合自訂和共用的黑名單。 |
> |  | **帳戶/ContentModerator/小組** |  |
> | DataAction | CognitiveServices/accounts/ContentModerator/小組/作業/動作 | 將會針對在此端點上公佈的影像內容傳回作業識別碼。  |
> | DataAction | CognitiveServices/accounts/ContentModerator/小組/評論/動作 | 所建立的審查會針對小組中的審核者顯示。 當審核者完成審核時，會在指定的 CallBackEndpoint 上張貼評論的結果（亦即 HTTP POST）。 |
> |  | **帳戶/ContentModerator/小組/作業** |  |
> | DataAction | CognitiveServices/accounts/ContentModerator/小組/工作/讀取 | 取得作業識別碼的作業詳細資料。 |
> |  | **帳戶/ContentModerator/小組/評論** |  |
> | DataAction | CognitiveServices/accounts/ContentModerator/小組/評論/閱讀 | 傳回通過審查識別碼的審查詳細資料。 |
> | DataAction | CognitiveServices/accounts/ContentModerator/小組/評論/發行/動作 | 影片評論最初是以未發行的狀態建立，這表示您的小組的審核者無法進行審核。 |
> | DataAction | CognitiveServices/accounts/ContentModerator/小組/評論/文字記錄/動作 | 此 API 會將文字記錄檔案（影片中所說的所有單字的文字版本）新增至影片審核。 檔案必須是有效的 WebVTT 格式。 |
> | DataAction | CognitiveServices/accounts/ContentModerator/小組/評論/transcriptmoderationresult/動作 | 此 API 會新增影片評論的文字記錄螢幕文字結果檔案。 文字記錄螢幕文字結果檔案是螢幕文字 API 的結果。 若要產生文字記錄畫面文字結果檔案，必須使用螢幕文字 API，將文字記錄檔篩選成不雅內容。 |
> |  | **accounts/ContentModerator/小組/評論/accesskey** |  |
> | DataAction | CognitiveServices/accounts/ContentModerator/小組/評論/accesskey/讀取 | 取得小組的審查內容存取金鑰。 |
> |  | **帳戶/ContentModerator/小組/評論/畫面格** |  |
> | DataAction | CognitiveServices/accounts/ContentModerator/小組/評論/框架/寫入 | 使用此方法來新增影片審核畫面。 |
> | DataAction | CognitiveServices/accounts/ContentModerator/小組/評論/畫面/讀取 | *NotDefined* |
> |  | **accounts/ContentModerator/小組/設定/範本** |  |
> | DataAction | CognitiveServices/accounts/ContentModerator/小組/設定/範本/寫入 | 建立或更新指定的範本 |
> | DataAction | CognitiveServices/accounts/ContentModerator/小組/設定/範本/刪除 | 刪除小組中的範本 |
> | DataAction | CognitiveServices/accounts/ContentModerator/小組/設定/範本/讀取 | 傳回此小組所布建的審查範本陣列。 |
> |  | **帳戶/ContentModerator/小組/工作流程** |  |
> | DataAction | CognitiveServices/accounts/ContentModerator/小組/工作流程/寫入 | 建立新的工作流程，或更新現有的。 |
> | DataAction | CognitiveServices/accounts/ContentModerator/小組/工作流程/讀取 | 取得小組中特定工作流程的詳細資料取得您的小組所提供的所有工作流程 * |
> |  | **accounts/ContentModerator/termlists、program** |  |
> | DataAction | CognitiveServices/accounts/ContentModerator/termlists、program/bulkupdate/action | 詞彙清單-大量更新 |
> | DataAction | CognitiveServices/accounts/ContentModerator/termlists、program/delete | 詞彙清單-刪除 |
> | DataAction | CognitiveServices/accounts/ContentModerator/termlists、program/read | 詞彙清單-取得所有詞彙清單-取得詳細資料 |
> | DataAction | CognitiveServices/accounts/ContentModerator/termlists、program/refreshindex/action | 詞彙清單-重新整理搜尋索引 |
> | DataAction | CognitiveServices/accounts/ContentModerator/termlists、program/write | 詞彙清單-更新詳細資料 |
> |  | **accounts/ContentModerator/termlists、program/詞彙** |  |
> | DataAction | CognitiveServices/accounts/ContentModerator/termlists、program/詞彙/寫入 | 詞彙-新增詞彙 |
> | DataAction | CognitiveServices/accounts/ContentModerator/termlists、program/詞彙/刪除 | 詞彙-刪除詞彙-刪除所有詞彙 |
> | DataAction | CognitiveServices/accounts/ContentModerator/termlists、program/條款/讀取 | 詞彙-取得所有詞彙 |
> |  | **帳戶/CustomVision。預測** |  |
> | DataAction | CognitiveServices/accounts/CustomVision。預測/專案/動作 | 建立專案。 |
> | DataAction | CognitiveServices/accounts/CustomVision。預測/使用者/動作 | *NotDefined* |
> | DataAction | CognitiveServices/accounts/CustomVision。預測/配額/動作 | *NotDefined* |
> |  | **accounts/CustomVision。預測/分類/反復專案** |  |
> | DataAction | CognitiveServices/accounts/CustomVision。預測/分類/反復專案/影像/動作 | 將影像分類並儲存結果。 |
> | DataAction | CognitiveServices/accounts/CustomVision。預測/分類/反復專案/url/動作 | 將影像 url 分類並儲存結果。 |
> |  | **accounts/CustomVision. 預測/分類/反覆運算/影像** |  |
> | DataAction | CognitiveServices/accounts/CustomVision。預測/分類/反復專案/影像/nostore/動作 | 分類映射而不儲存結果。 |
> |  | **accounts/CustomVision。預測/分類/反覆運算/url** |  |
> | DataAction | CognitiveServices/accounts/CustomVision。預測/分類/反復專案/url/nostore/動作 | 分類影像 url，而不儲存結果。 |
> |  | **accounts/CustomVision。預測/偵測/反復專案** |  |
> | DataAction | CognitiveServices/accounts/CustomVision。預測/偵測/反復專案/影像/動作 | 偵測影像中的物件並儲存結果。 |
> | DataAction | CognitiveServices/accounts/CustomVision。預測/偵測/反復專案/url/動作 | 偵測影像 url 中的物件並儲存結果。 |
> |  | **accounts/CustomVision. 預測/偵測/反覆運算/影像** |  |
> | DataAction | CognitiveServices/accounts/CustomVision。預測/偵測/反復專案/影像/nostore/動作 | 在不儲存結果的情況下，偵測影像中的物件。 |
> |  | **accounts/CustomVision。預測/偵測/反覆運算/url** |  |
> | DataAction | CognitiveServices/accounts/CustomVision。預測/偵測/反覆運算/url/nostore/動作 | 偵測影像 url 中的物件，而不儲存結果。 |
> |  | **accounts/CustomVision。預測/網域** |  |
> | DataAction | CognitiveServices/accounts/CustomVision。預測/網域/讀取 | 取得特定網域的相關資訊。 取得可用網域的清單。 * |
> |  | **accounts/CustomVision. 預測/labelproposals** |  |
> | DataAction | CognitiveServices/accounts/CustomVision。預測/labelproposals/設定/動作 | 設定標籤提案的集區大小。 |
> |  | **accounts/CustomVision. 預測/labelproposals/設定** |  |
> | DataAction | CognitiveServices/accounts/CustomVision。預測/labelproposals/設定/讀取 | 取得此專案的標籤的集區大小建議。 |
> |  | **accounts/CustomVision. 預測/專案** |  |
> | DataAction | CognitiveServices/accounts/CustomVision。預測/專案/遷移/動作 | *NotDefined* |
> |  | **accounts/CustomVision. 預測/專案** |  |
> | DataAction | CognitiveServices/accounts/CustomVision。預測/專案/影像/動作 | 此 API 會以多部分/表單資料和應用程式/八位串流的形式接受本文內容。 使用多部分時 |
> | DataAction | CognitiveServices/accounts/CustomVision。預測/專案/標記/動作 | 建立專案的標記。 |
> | DataAction | CognitiveServices/accounts/CustomVision。預測/專案/刪除 | 刪除特定專案。 |
> | DataAction | CognitiveServices/accounts/CustomVision。預測/專案/讀取 | 取得特定專案。 取得您的專案。 * |
> | DataAction | CognitiveServices/accounts/CustomVision。預測/專案/訓練/動作 | 將專案排入佇列以進行定型。 |
> | DataAction | CognitiveServices/accounts/CustomVision。預測/專案/寫入 | 更新特定專案。 |
> |  | **accounts/CustomVision. 預測/專案/影像** |  |
> | DataAction | CognitiveServices/accounts/CustomVision。預測/專案/影像/區域/動作 | 此 API 會接受一批影像區域和選擇性標記，以使用區域資訊來更新現有的映射。 |
> | DataAction | CognitiveServices/accounts/CustomVision。預測/專案/影像/檔案/動作 | 此 API 會接受一批檔案和選擇性標記，以建立映射。 有64個影像和20個標記的限制。 |
> | DataAction | CognitiveServices/accounts/CustomVision。預測/專案/影像/預測/動作 | 此 API 會從指定的預測影像建立一批映射。 有64個影像和20個標記的限制。 |
> | DataAction | CognitiveServices/accounts/CustomVision。預測/專案/影像/url/動作 | 此 API 會接受一批 url，並選擇性地使用標記來建立映射。 有64個影像和20個標記的限制。 |
> | DataAction | CognitiveServices/accounts/CustomVision。預測/專案/影像/標記/動作 | 將一組影像與一組標記產生關聯。 |
> | DataAction | CognitiveServices/accounts/CustomVision。預測/專案/影像/刪除 | 從一組定型影像中刪除影像。 |
> | DataAction | CognitiveServices/accounts/CustomVision。預測/專案/影像/regionproposals/動作 | 此 API 會針對該地區的 confidences 取得影像的區域提案。 如果找不到任何提案，它會傳回空陣列。 |
> | DataAction | CognitiveServices/accounts/CustomVision。預測/專案/影像/建議/動作 | 此 API 會提取依建議的標記識別項篩選的未標記影像。 如果找不到任何影像，則會傳回空陣列。 |
> |  | **accounts/CustomVision. 預測/專案/影像/識別碼** |  |
> | DataAction | CognitiveServices/accounts/CustomVision。預測/專案/影像/識別碼/讀取 | 這個 API 會針對指定的標記和選擇性的反復專案傳回一組影像。 如果未指定反復專案， |
> |  | **accounts/CustomVision. 預測/專案/影像/區域** |  |
> | DataAction | CognitiveServices/accounts/CustomVision。預測/專案/影像/區域/刪除 | 刪除一組影像區域。 |
> |  | **accounts/CustomVision. 預測/專案/影像/建議** |  |
> | DataAction | CognitiveServices/accounts/CustomVision。預測/專案/影像/建議/計數/動作 | 此 API 會採用 Tagid 來取得每個建議標籤的未標記影像計數，以取得指定的閾值。 |
> |  | **accounts/CustomVision. 預測/專案/影像/已標記** |  |
> | DataAction | CognitiveServices/accounts/CustomVision。預測/專案/影像/標記/讀取 | 此 API 支援批次處理和範圍選取。 根據預設，它只會傳回符合映射的前50個影像。 |
> |  | **accounts/CustomVision. 預測/專案/影像/標記/計數** |  |
> | DataAction | CognitiveServices/accounts/CustomVision。預測/專案/影像/標記/計數/讀取 | 篩選是在和/或關聯性上。 例如，如果提供的標記識別項適用于 "Dog"，而 |
> |  | **accounts/CustomVision. 預測/專案/影像/標記** |  |
> | DataAction | CognitiveServices/accounts/CustomVision。預測/專案/影像/標記/刪除 | 從一組影像中移除一組標記。 |
> |  | **accounts/CustomVision. 預測/專案/影像/未標記** |  |
> | DataAction | CognitiveServices/accounts/CustomVision。預測/專案/影像/未標記/已讀取 | 此 API 支援批次處理和範圍選取。 根據預設，它只會傳回符合映射的前50個影像。 |
> |  | **帳戶/CustomVision。預測/專案/影像/未標記/計數** |  |
> | DataAction | CognitiveServices/accounts/CustomVision。預測/專案/影像/未標記/計數/讀取 | 此 API 會傳回沒有指定專案標記的影像，以及選擇性地反復專案。 如果未指定反復專案， |
> |  | **accounts/CustomVision. 預測/專案/反覆運算** |  |
> | DataAction | CognitiveServices/accounts/CustomVision。預測/專案/反覆運算/刪除 | 刪除專案的特定反復專案。 |
> | DataAction | CognitiveServices/accounts/CustomVision。預測/專案/反覆運算/匯出/動作 | 匯出定型的反復專案。 |
> | DataAction | CognitiveServices/accounts/CustomVision。預測/專案/反覆運算/讀取 | 取得特定的反復專案。 取得專案的反覆運算。 * |
> | DataAction | CognitiveServices/accounts/CustomVision。預測/專案/反覆運算/發行/動作 | 發行特定的反復專案。 |
> | DataAction | CognitiveServices/accounts/CustomVision。預測/專案/反覆運算/寫入 | 更新特定的反復專案。 |
> |  | **accounts/CustomVision. 預測/專案/反覆運算/匯出** |  |
> | DataAction | CognitiveServices/accounts/CustomVision。預測/專案/反覆運算/匯出/讀取 | 取得特定反復專案的匯出清單。 |
> |  | **帳戶/CustomVision。預測/專案/反覆運算/效能** |  |
> | DataAction | CognitiveServices/accounts/CustomVision。預測/專案/反覆運算/效能/讀取 | 取得反復專案的詳細效能資訊。 |
> |  | **accounts/CustomVision. 預測/專案/反覆運算/效能/影像** |  |
> | DataAction | CognitiveServices/accounts/CustomVision。預測/專案/反覆運算/效能/影像/讀取 | 此 API 支援批次處理和範圍選取。 根據預設，它只會傳回符合映射的前50個影像。 |
> |  | **accounts/CustomVision. 預測/專案/反覆運算/效能/影像/計數** |  |
> | DataAction | CognitiveServices/accounts/CustomVision。預測/專案/反覆運算/效能/影像/計數/讀取 | 篩選是在和/或關聯性上。 例如，如果提供的標記識別項適用于 "Dog"，而 |
> |  | **accounts/CustomVision. 預測/專案/反覆運算/發行** |  |
> | DataAction | CognitiveServices/accounts/CustomVision。預測/專案/反覆運算/發行/刪除 | 解除發佈特定的反復專案。 |
> |  | **accounts/CustomVision. 預測/專案/預測** |  |
> | DataAction | CognitiveServices/accounts/CustomVision。預測/專案/預測/刪除 | 刪除一組預測的影像及其相關聯的預測結果。 |
> | DataAction | CognitiveServices/accounts/CustomVision。預測/專案/預測/查詢/動作 | 取得已傳送至預測端點的影像。 |
> |  | **accounts/CustomVision. 預測/專案/quicktest** |  |
> | DataAction | CognitiveServices/accounts/CustomVision。預測/專案/quicktest/影像/動作 | 快速測試影像。 |
> | DataAction | CognitiveServices/accounts/CustomVision。預測/專案/quicktest/url/動作 | 快速測試影像 url。 |
> |  | **accounts/CustomVision. 預測/專案/標記** |  |
> | DataAction | CognitiveServices/accounts/CustomVision。預測/專案/標記/刪除 | 從專案中刪除標記。 |
> | DataAction | CognitiveServices/accounts/CustomVision。預測/專案/標記/讀取 | 取得特定標記的相關資訊。 取得指定專案和反復專案的標記。 * |
> | DataAction | CognitiveServices/accounts/CustomVision/的預測/專案/標記/寫入 | 更新標記。 |
> |  | **accounts/CustomVision. 預測/專案/tagsandregions** |  |
> | DataAction | CognitiveServices/accounts/CustomVision。預測/專案/tagsandregions/建議/動作 | 此 API 會針對未標記影像的陣列/批次取得建議的標籤和區域，以及 confidences 標籤。 如果找不到標記，則會傳回空陣列。 |
> |  | **帳戶/CustomVision。預測/配額** |  |
> | DataAction | CognitiveServices/accounts/CustomVision。預測/配額/刪除 | *NotDefined* |
> |  | **帳戶/CustomVision。預測/配額/重新整理** |  |
> | DataAction | CognitiveServices/accounts/CustomVision。預測/配額/重新整理/寫入 | *NotDefined* |
> |  | **accounts/CustomVision。預測/使用量/預測/使用者** |  |
> | DataAction | CognitiveServices/accounts/CustomVision。預測/使用量/預測/使用者/讀取 | 取得 Oxford 使用者的預測資源使用量 |
> |  | **帳戶/CustomVision。預測/使用量/訓練/資源/層** |  |
> | DataAction | CognitiveServices/accounts/CustomVision。預測/使用量/訓練/資源/層/讀取 | 取得 Azure 使用者訓練資源的使用量 |
> |  | **accounts/CustomVision。預測/使用量/訓練/使用者** |  |
> | DataAction | CognitiveServices/accounts/CustomVision。預測/使用量/訓練/使用者/讀取 | 取得 Oxford 使用者訓練資源的使用方式 |
> |  | **accounts/CustomVision。預測/使用者** |  |
> | DataAction | CognitiveServices/accounts/CustomVision。預測/使用者/刪除 | *NotDefined* |
> |  | **帳戶/CustomVision。預測/使用者/狀態** |  |
> | DataAction | CognitiveServices/accounts/CustomVision。預測/使用者/狀態/寫入 | 更新使用者狀態 |
> |  | **帳戶/CustomVision。預測/使用者/層** |  |
> | DataAction | CognitiveServices/accounts/CustomVision。預測/使用者/層級/寫入 | *NotDefined* |
> |  | **accounts/CustomVision。預測/使用者** |  |
> | DataAction | CognitiveServices/accounts/CustomVision。預測/使用者/讀取 | *NotDefined* |
> |  | **帳戶/CustomVision。預測/白名單** |  |
> | DataAction | CognitiveServices/accounts/CustomVision。預測/帳戶白名單/刪除 | 刪除具有特定功能的白名單使用者 |
> | DataAction | CognitiveServices/accounts/CustomVision。預測/帳戶白名單/讀取 | 取得具有特定功能的白名單使用者清單 |
> | DataAction | CognitiveServices/accounts/CustomVision。預測/列入允許清單/寫入 | 在具有特定功能的白名單中更新或建立使用者 |
> |  | **帳戶/EntitySearch** |  |
> | DataAction | CognitiveServices/帳戶/EntitySearch/搜尋/動作 | 取得給定查詢的實體和位置結果。 |
> |  | **帳戶/臉部** |  |
> | DataAction | CognitiveServices/帳戶/臉部/偵測/動作 | 偵測影像中的人臉、傳回臉部矩形，並選擇性地使用 faceIds、地標和屬性。 |
> | DataAction | CognitiveServices/accounts/臉部/findsimilars/action | 指定查詢臉部的 faceId，以便從 faceId 陣列、臉部清單或大型臉部清單中搜尋外觀相似的臉部。 faceId |
> | DataAction | CognitiveServices/accounts/臉部/群組/動作 | 根據臉部相似性將候選臉部分割成群組。 |
> | DataAction | CognitiveServices/accounts/臉部/識別/動作 | 1對多的識別，以尋找與人員群組或大型人員群組中的特定查詢人臉最接近的相符專案。 |
> | DataAction | CognitiveServices/帳戶/臉部/驗證/動作 | 確認兩個臉部是否屬於同一人，或某個臉部是否屬於某個人。 |
> |  | **帳戶/臉部/facelist** |  |
> | DataAction | CognitiveServices/accounts/臉部/facelist/write | 使用使用者指定的 faceListId、名稱和選擇性的 userData 來建立空白的臉部清單。 最多64個臉部清單可更新臉部清單的資訊，包括 name 和 userData。 |
> | DataAction | CognitiveServices/accounts/臉部/facelist/delete | 刪除指定的臉部清單。 臉部清單中的相關臉部影像也會一併刪除。 |
> | DataAction | CognitiveServices/accounts/臉部/facelist/read | 在臉部清單中取出臉部清單的 faceListId、姓名、userData 和臉部。 列出臉部清單的 faceListId、name 和 userData。 |
> |  | **accounts/臉部/facelist/persistedfaces** |  |
> | DataAction | CognitiveServices/accounts/臉部/facelist/persistedfaces/write | 將臉部新增至指定的臉部清單，最多可達1000人臉。 |
> | DataAction | CognitiveServices/accounts/臉部/facelist/persistedfaces/delete | 藉由指定的 faceListId 和 persisitedFaceId，從臉部清單中刪除臉部。 也會刪除相關的臉部影像。 |
> |  | **帳戶/臉部/largefacelists** |  |
> | DataAction | CognitiveServices/accounts/臉部/largefacelists/write | 使用使用者指定的 largeFaceListId、名稱和選擇性的 userData 來建立空白的大型臉部清單。 更新大型臉部清單的資訊，包括 name 和 userData。 |
> | DataAction | CognitiveServices/accounts/臉部/largefacelists/delete | 刪除指定的大型臉部清單。 也會刪除大型臉部清單中的相關臉部影像。 |
> | DataAction | CognitiveServices/accounts/臉部/largefacelists/read | 取出大型臉部清單的 largeFaceListId、name、userData。 列出 largeFaceListId、name 和 userData 等大型臉部清單的資訊。 |
> | DataAction | CognitiveServices/accounts/臉部/largefacelists/訓練/動作 | 提交大型的臉部清單定型工作。 定型是只有經過訓練的大型臉部清單可以使用的重要步驟。 |
> |  | **accounts/臉部/largefacelists/persistedfaces** |  |
> | DataAction | CognitiveServices/accounts/臉部/largefacelists/persistedfaces/write | 將臉部新增到指定的大型臉部清單中，最多可達1000000人臉。 依 persistedFaceId，更新大型臉部清單中指定臉部的 userData 欄位。 |
> | DataAction | CognitiveServices/accounts/臉部/largefacelists/persistedfaces/delete | 藉由指定的 largeFaceListId 和 persisitedFaceId，從大型臉部清單中刪除臉部。 也會刪除相關的臉部影像。 |
> | DataAction | CognitiveServices/accounts/臉部/largefacelists/persistedfaces/read | 依 largeFaceListId 和 persistedFaceId 取出大型臉部清單中的保存臉部。 在指定的大型臉部清單中列出臉部的 persistedFaceId 和 userData。 |
> |  | **帳戶/臉部/largefacelists/訓練** |  |
> | DataAction | CognitiveServices/accounts/臉部/largefacelists/訓練/閱讀 | 檢查大型臉部清單定型狀態是否已完成或仍在進行中。 LargeFaceList 訓練是非同步作業 |
> |  | **帳戶/臉部/largepersongroups** |  |
> | DataAction | CognitiveServices/accounts/臉部/largepersongroups/write | 使用使用者指定的 largePersonGroupId、名稱和選擇性的 userData，建立新的大型人員群組。 更新現有的大型人員群組名稱和 userData。 如果內容不在要求本文中，屬性會保持不變。 |
> | DataAction | CognitiveServices/accounts/臉部/largepersongroups/delete | 使用指定的 personGroupId 刪除現有的大型人員群組。 將會刪除此大型人員群組中的保存資料。 |
> | DataAction | CognitiveServices/accounts/臉部/largepersongroups/read | 取出大型人員群組的資訊，包括其名稱和 userData。 此 API 會傳回大型人員群組資訊清單，列出所有現有的大型人員群組的 largePesonGroupId、name 和 userData。 |
> | DataAction | CognitiveServices/accounts/臉部/largepersongroups/訓練/動作 | 提交大型人員群組訓練工作。 定型是只有經過訓練的大型人員群組可以使用的重要步驟。 |
> | DataAction | CognitiveServices/accounts/臉部/largepersongroups/人員/動作 | 在指定的大型人員群組中建立新的人員。 若要將臉部加入此人員，請致電 |
> |  | **帳戶/臉部/largepersongroups/人員** |  |
> | DataAction | CognitiveServices/accounts/臉部/largepersongroups/人員/刪除 | 從大型人員群組中刪除現有的人員。 所有儲存的人員資料和 person 專案中的臉部影像都會被刪除。 |
> | DataAction | CognitiveServices/accounts/臉部/largepersongroups/人員/閱讀 | 抓取人員的名稱和 userData，而保存的 faceIds 則代表已註冊的人員臉部影像。 列出指定的大型人員群組中的所有人員資訊，包括 personId、name、userData 和 persistedFaceIds。 |
> | DataAction | CognitiveServices/accounts/臉部/largepersongroups/人員/寫入 | 更新個人的姓名或 userData。 |
> |  | **帳戶/臉部/largepersongroups/人員/persistedfaces** |  |
> | DataAction | CognitiveServices/accounts/臉部/largepersongroups/人員/persistedfaces/寫入 | 針對臉部識別或驗證，將臉部影像新增到大型人員群組中。 若要處理更新人員保存臉部的 userData 欄位的影像。 |
> | DataAction | CognitiveServices/accounts/臉部/largepersongroups/人員/persistedfaces/刪除 | 從大型人員群組中的人員刪除臉部。 臉部資料和與此臉部專案相關的影像也會一併刪除。 |
> | DataAction | CognitiveServices/accounts/臉部/largepersongroups/人員/persistedfaces/閱讀 | 取出人員臉部資訊。 保存的人臉是由其 largePersonGroupId、personId 和 persistedFaceId 所指定。 |
> |  | **帳戶/臉部/largepersongroups/訓練** |  |
> | DataAction | CognitiveServices/accounts/臉部/largepersongroups/訓練/閱讀 | 檢查大型人員群組訓練狀態已完成或仍在進行中。 LargePersonGroup 訓練是非同步作業 |
> |  | **帳戶/臉部/persongroup** |  |
> | DataAction | CognitiveServices/accounts/臉部/persongroup/write | 使用指定的 personGroupId、名稱和使用者提供的 userData，建立新的人員群組。 更新現有人員群組的名稱和 userData。 如果內容不在要求本文中，屬性會保持不變。 |
> | DataAction | CognitiveServices/accounts/臉部/persongroup/delete | 使用指定的 personGroupId 刪除現有的人員群組。 將會刪除此 person 群組中的保存資料。 |
> | DataAction | CognitiveServices/accounts/臉部/persongroup/read | 取出人員組名和 userData。 若要取得此 personGroup 下的人員資訊，請使用 List person 群組的 pesonGroupId、name 及 userData。 |
> | DataAction | CognitiveServices/accounts/臉部/persongroup/訓練/動作 | 提交人員群組訓練工作。 定型是只有定型的人員群組可以使用的重要步驟。 |
> | DataAction | CognitiveServices/accounts/臉部/persongroup/人員/動作 | 在指定的人員群組中建立新的人員。 若要將臉部加入此人員，請致電 |
> |  | **帳戶/臉部/persongroup/人員** |  |
> | DataAction | CognitiveServices/accounts/臉部/persongroup/人員/刪除 | 刪除人員群組中的現有人員。 所有儲存的人員資料和 person 專案中的臉部影像都會被刪除。 |
> | DataAction | CognitiveServices/accounts/臉部/persongroup/人員/閱讀 | 抓取人員的名稱和 userData，而保存的 faceIds 則代表已註冊的人員臉部影像。 列出指定之 person 群組中的所有人員資訊，包括已註冊的 personId、name、userData 和 persistedFaceIds。 |
> | DataAction | CognitiveServices/accounts/臉部/persongroup/人員/寫入 | 更新個人的姓名或 userData。 |
> |  | **帳戶/臉部/persongroup/人員/persistedfaces** |  |
> | DataAction | CognitiveServices/accounts/臉部/persongroup/人員/persistedfaces/寫入 | 將臉部影像新增至人員群組以進行臉部識別或驗證。 若要處理多個更新的影像，個人會保存臉部的 userData 欄位。 |
> | DataAction | CognitiveServices/accounts/臉部/persongroup/人員/persistedfaces/刪除 | 刪除人員群組中的人臉。 臉部資料和與此臉部專案相關的影像也會一併刪除。 |
> | DataAction | CognitiveServices/accounts/臉部/persongroup/人員/persistedfaces/閱讀 | 取出人員臉部資訊。 保存的人臉是由其 personGroupId、personId 和 persistedFaceId 所指定。 |
> |  | **帳戶/臉部/persongroup/訓練** |  |
> | DataAction | CognitiveServices/accounts/臉部/persongroup/訓練/閱讀 | 檢查人員群組訓練狀態已完成或仍在進行中。 PersonGroup 訓練是觸發的非同步作業 |
> |  | **帳戶/臉部/快照集** |  |
> | DataAction | CognitiveServices/accounts/臉部/快照/採取動作 | 取得物件的快照集。 |
> | DataAction | CognitiveServices/帳戶/臉部/快照/讀取 | 取得快照集作業的狀態。 |
> | DataAction | CognitiveServices/帳戶/臉部/快照/套用/動作 | 套用快照集，並提供使用者指定的物件識別碼。 |
> | DataAction | CognitiveServices/accounts/臉部/快照/刪除 | 刪除快照集。 |
> | DataAction | CognitiveServices/帳戶/臉部/快照/寫入 | 更新快照集的屬性。 |
> |  | **帳戶/臉部/快照集** |  |
> | DataAction | CognitiveServices/帳戶/臉部/快照/讀取 | 列出所有使用者可存取的快照集，並提供資訊。 * |
> |  | **帳戶/FormRecognizer/自訂** |  |
> | DataAction | CognitiveServices/accounts/FormRecognizer/custom/訓練/action | 建立和定型自訂模型。<br>定型要求必須包含來源參數，這是可從外部存取的 Azure 儲存體 blob 容器 Uri （最好是共用存取簽章 Uri），或是在本機掛接的磁片磁碟機中，指向資料檔案夾的有效路徑。<br>指定本機路徑時，它們必須遵循 Linux/Unix 路徑格式，而且必須是根目錄為輸入裝載設定的絕對路徑 |
> |  | **帳戶/FormRecognizer/自訂/模型** |  |
> | DataAction | CognitiveServices/accounts/FormRecognizer/自訂/模型/分析/動作 | 從指定的檔中解壓縮索引鍵/值組。 輸入檔必須是其中一種支援的內容類型-「應用程式/pdf」、「影像/jpeg」或「影像/png」。 成功的回應會以 JSON 的形式傳回。 |
> | DataAction | CognitiveServices/accounts/FormRecognizer/custom/型號/delete | 刪除模型成品。 |
> | DataAction | CognitiveServices/accounts/FormRecognizer/自訂/模型/讀取 | 取得模型的相關資訊。 取得所有定型自訂模型的相關資訊 * |
> |  | **帳戶/FormRecognizer/自訂/模型/金鑰** |  |
> | DataAction | CognitiveServices/accounts/FormRecognizer/自訂/模型/金鑰/讀取 | 取得模型的索引鍵。 |
> |  | **帳戶/FormRecognizer/預先建立/接收** |  |
> | DataAction | CognitiveServices/accounts/FormRecognizer/預建/接收/asyncbatchanalyze/動作 | 從指定的收據檔中，將欄位文字和語義值解壓縮。 輸入影像檔必須是其中一種支援的內容類型： JPEG、PNG、BMP、PDF 或 TIFF。 成功回應是包含稱為「作業-位置」之欄位的 JSON，其中包含取得接收結果作業的 URL 以非同步方式抓取結果。 |
> | DataAction | CognitiveServices/accounts/FormRecognizer/預先建立/接收/作業/動作 | 查詢狀態，並取出分析回條作業的結果。 此介面的 URL 可以從「分析回條」回應中的「作業-位置」標頭取得。 |
> |  | **帳戶/ImageSearch** |  |
> | DataAction | CognitiveServices/accounts/ImageSearch/details/action | 傳回影像的相關見解，例如包含影像的網頁。 |
> | DataAction | CognitiveServices/帳戶/ImageSearch/搜尋/動作 | 取得給定查詢的相關影像。 |
> | DataAction | CognitiveServices/accounts/ImageSearch/趨勢/動作 | 取得目前的趨勢影像。 |
> |  | **帳戶/ImmersiveReader** |  |
> | DataAction | CognitiveServices/accounts/ImmersiveReader/getcontentmodelforreader/action | 建立沉浸式讀取器會話 |
> |  | **帳戶/InkRecognizer** |  |
> | DataAction | CognitiveServices/accounts/InkRecognizer/辨識/動作 | 假設有一組筆劃資料會分析內容，並產生可辨識的實體清單，包括已辨識的文字。 |
> |  | **帳戶/LUIS** |  |
> | DataAction | CognitiveServices/accounts/LUIS/predict/action | 取得給定查詢的已發行端點預測。 |
> |  | **帳戶/NewsSearch** |  |
> | DataAction | CognitiveServices/accounts/NewsSearch/categorysearch/action | 傳回所提供分類的新聞。 |
> | DataAction | CognitiveServices/帳戶/NewsSearch/搜尋/動作 | 取得與給定查詢相關的新聞文章。 |
> | DataAction | CognitiveServices/accounts/NewsSearch/trendingtopics/action | 取得 Bing 所識別的趨勢主題。 這些是 Bing 首頁底部橫幅中所顯示的相同主題。 |
> |  | **帳戶/QnAMaker** |  |
> | DataAction | CognitiveServices/accounts/QnAMaker/root/action | QnA Maker |
> |  | **帳戶/QnAMaker/變更** |  |
> | DataAction | CognitiveServices/帳戶/QnAMaker/變更/讀取 | 從執行時間下載變更。 |
> | DataAction | CognitiveServices/accounts/QnAMaker/更改/寫入 | 取代變更資料。 |
> |  | **accounts/QnAMaker/endpointkeys** |  |
> | DataAction | CognitiveServices/accounts/QnAMaker/endpointkeys/read | 取得端點的端點金鑰 |
> | DataAction | CognitiveServices/accounts/QnAMaker/endpointkeys/refreshkeys/action | 重新產生端點金鑰。 |
> |  | **accounts/QnAMaker/endpointsettings** |  |
> | DataAction | CognitiveServices/accounts/QnAMaker/endpointsettings/read | 取得端點的端點設定 |
> | DataAction | CognitiveServices/accounts/QnAMaker/endpointsettings/write | 更新端點的端點 seettings。 |
> |  | **accounts/QnAMaker/知識庫** |  |
> | DataAction | CognitiveServices/accounts/QnAMaker/知識庫/publish/action | 將知識庫的測試索引中的所有變更發佈至其生產索引。 |
> | DataAction | CognitiveServices/accounts/QnAMaker/知識庫/delete | 刪除知識庫及其所有資料。 |
> | DataAction | CognitiveServices/accounts/QnAMaker/知識庫/read | 取得特定 knowledgebaser 的知識庫或詳細資料清單。 |
> | DataAction | CognitiveServices/accounts/QnAMaker/知識庫/write | 修改知識庫或取代知識庫內容的非同步作業。 |
> | DataAction | CognitiveServices/accounts/QnAMaker/知識庫/generateanswer/action | GenerateAnswer 呼叫來查詢知識庫。 |
> | DataAction | CognitiveServices/accounts/QnAMaker/知識庫/訓練/動作 | 訓練呼叫以將建議新增至知識庫。 |
> |  | **accounts/QnAMaker/知識庫/create** |  |
> | DataAction | CognitiveServices/accounts/QnAMaker/知識庫/create/write | 非同步作業以建立新的知識庫。 |
> |  | **帳戶/QnAMaker/知識庫/下載** |  |
> | DataAction | CognitiveServices/accounts/QnAMaker/知識庫/下載/讀取 | 下載知識庫。 |
> |  | **帳戶/QnAMaker/作業** |  |
> | DataAction | CognitiveServices/accounts/QnAMaker/operations/read | 取得特定長時間執行作業的詳細資料。 |
> |  | **帳戶/拼字檢查** |  |
> | DataAction | CognitiveServices/accounts/拼字檢查/拼字檢查/action | 透過 GET 或 POST 取得拼寫檢查查詢的結果。 |
> |  | **帳戶/Microsoft.azure.cognitiveservices.language.textanalytics** |  |
> | DataAction | CognitiveServices/accounts/Microsoft.azure.cognitiveservices.language.textanalytics/語言/動作 | 此 API 會傳回偵測到的語言，以及介於0和1之間的數值分數。 接近 1 的分數表示 100% 確定已識別的語言為真實。 總共支援 120 種語言。 |
> | DataAction | CognitiveServices/accounts/Microsoft.azure.cognitiveservices.language.textanalytics/實體/動作 | API 會傳回給定檔中已知實體和一般命名實體（\"Person\"、\"位置\"、\"組織\" 等）的清單。 |
> | DataAction | CognitiveServices/accounts/Microsoft.azure.cognitiveservices.language.textanalytics/keyphrases/action | API 會傳回輸入文字中代表說話重點的字串清單。 |
> | DataAction | CognitiveServices/accounts/Microsoft.azure.cognitiveservices.language.textanalytics/情感/action | 此 API 會傳回 0 到 1 之間的數值分數。<br>接近 1 的分數表示正面的情感，而接近 0 的分數則表示負面的情感。<br>分數0.5 表示缺少情感（例如<br>模擬語句）。 |
> |  | **帳戶/VideoSearch** |  |
> | DataAction | CognitiveServices/accounts/VideoSearch/趨勢/動作 | 取得目前的發燒影片。 |
> | DataAction | CognitiveServices/accounts/VideoSearch/details/action | 取得影片的深入解析，例如相關影片。 |
> | DataAction | CognitiveServices/帳戶/VideoSearch/搜尋/動作 | 取得與指定查詢相關的影片。 |
> |  | **帳戶/VisualSearch** |  |
> | DataAction | CognitiveServices/帳戶/VisualSearch/搜尋/動作 | 傳回與所提供影像相關的標記清單 |
> |  | **帳戶/WebSearch** |  |
> | DataAction | CognitiveServices/帳戶/WebSearch/搜尋/動作 | 取得給定查詢的 web、影像、新聞、& 影片結果。 |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

Azure 服務：核心

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft Commerce/註冊/動作 | 註冊 Microsoft Commerce UsageAggregate 的訂用帳戶 |
> | 動作 | Microsoft Commerce/取消註冊/動作 | 取消註冊 Microsoft Commerce UsageAggregate 的訂用帳戶 |
> |  | **RateCard** |  |
> | 動作 | Microsoft.Commerce/RateCard/read | 傳回給定訂用帳戶的供應項目資料、資源/計量中繼資料和速率。 |
> |  | **UsageAggregates** |  |
> | 動作 | Microsoft.Commerce/UsageAggregates/read | 抓取訂用帳戶 Microsoft Azure 的耗用量。 結果會包含特定時間範圍內的彙總使用量資料、訂用帳戶和資源相關資訊。 |

## <a name="microsoftcompute"></a>Microsoft.Compute

Azure 服務：[虛擬機器](../virtual-machines/index.yml)、[虛擬機器擴展集](../virtual-machine-scale-sets/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.Compute/register/action | 向 Microsoft.Compute 資源提供者註冊訂用帳戶 |
> | 動作 | Microsoft。計算/取消註冊/動作 | 向 Microsoft 取消註冊訂用帳戶。計算資源提供者 |
> |  | **availabilitySets** |  |
> | 動作 | Microsoft.Compute/availabilitySets/read | 取得可用性設定組的屬性 |
> | 動作 | Microsoft.Compute/availabilitySets/write | 建立新的可用性設定組，或更新現有的可用性設定組 |
> | 動作 | Microsoft.Compute/availabilitySets/delete | 刪除可用性設定組 |
> |  | **availabilitySets/的 vmsizes** |  |
> | 動作 | Microsoft.Compute/availabilitySets/vmSizes/read | 列出在可用性設定組中建立或更新虛擬機器時可以使用的大小 |
> |  | **diskEncryptionSets** |  |
> | 動作 | Microsoft。計算/diskEncryptionSets/讀取 | 取得磁片加密集的屬性 |
> | 動作 | Microsoft。計算/diskEncryptionSets/寫入 | 建立新的磁片加密集或更新現有的 |
> | 動作 | Microsoft。計算/diskEncryptionSets/刪除 | 刪除磁片加密集 |
> |  | **盤** |  |
> | 動作 | Microsoft.Compute/disks/read | 取得磁碟的屬性 |
> | 動作 | Microsoft.Compute/disks/write | 建立新的磁碟，或更新現有磁碟 |
> | 動作 | Microsoft.Compute/disks/delete | 刪除磁碟 |
> | 動作 | Microsoft.Compute/disks/beginGetAccess/action | 取得磁碟用於 Blob 存取的 SAS URI |
> | 動作 | Microsoft.Compute/disks/endGetAccess/action | 撤銷磁碟的 SAS URI |
> |  | **圖片** |  |
> | 動作 | Microsoft.Compute/galleries/read | 取得資源庫的屬性 |
> | 動作 | Microsoft.Compute/galleries/write | 建立新的資源庫或更新現有的資源庫 |
> | 動作 | Microsoft.Compute/galleries/delete | 刪除資源庫 |
> |  | **資源庫/應用程式** |  |
> | 動作 | Microsoft。計算/資源庫/應用程式/讀取 | 取得圖庫應用程式的屬性 |
> | 動作 | Microsoft. 計算/資源庫/應用程式/寫入 | 建立新的資源庫應用程式，或更新現有的 |
> | 動作 | Microsoft。計算/資源庫/應用程式/刪除 | 刪除資源庫應用程式 |
> |  | **資源庫/應用程式/版本** |  |
> | 動作 | Microsoft。計算/資源庫/應用程式/版本/讀取 | 取得圖庫應用程式版本的屬性 |
> | 動作 | Microsoft。計算/資源庫/應用程式/版本/寫入 | 建立新的資源庫應用程式版本，或更新現有的 |
> | 動作 | Microsoft. 計算/資源庫/應用程式/版本/刪除 | 刪除圖庫應用程式版本 |
> |  | **資源庫/影像** |  |
> | 動作 | Microsoft.Compute/galleries/images/read | 取得資源庫映像的屬性 |
> | 動作 | Microsoft.Compute/galleries/images/write | 建立新的資源庫映像或更新現有的資源庫映像 |
> | 動作 | Microsoft.Compute/galleries/images/delete | 刪除資源庫映像 |
> |  | **資源庫/影像/版本** |  |
> | 動作 | Microsoft.Compute/galleries/images/versions/read | 取得資源庫映像版本的屬性 |
> | 動作 | Microsoft.Compute/galleries/images/versions/write | 建立新的資源庫映像版本或更新現有的資源庫映像版本 |
> | 動作 | Microsoft.Compute/galleries/images/versions/delete | 刪除資源庫映像版本 |
> |  | **hostGroups** |  |
> | 動作 | Microsoft。計算/hostGroups/讀取 | 取得主機群組的屬性 |
> | 動作 | Microsoft。計算/hostGroups/寫入 | 建立新的主機群組，或更新現有的主機群組 |
> | 動作 | Microsoft。計算/hostGroups/刪除 | 刪除主機群組 |
> |  | **hostGroups/主機** |  |
> | 動作 | Microsoft。計算/hostGroups/主機/讀取 | 取得主機的屬性 |
> | 動作 | Microsoft。 Compute/hostGroups/hosts/write | 建立新的主機，或更新現有的主機 |
> | 動作 | Microsoft。 Compute/hostGroups/hosts/delete | 刪除主機 |
> |  | **images** |  |
> | 動作 | Microsoft.Compute/images/read | 取得映像的屬性 |
> | 動作 | Microsoft.Compute/images/write | 建立新的映像，或更新現有映像 |
> | 動作 | Microsoft.Compute/images/delete | 刪除映像 |
> |  | **位置/capsOperations** |  |
> | 動作 | Microsoft.Compute/locations/capsOperations/read | 取得非同步 Caps 作業的狀態 |
> |  | **位置/diskOperations** |  |
> | 動作 | Microsoft.Compute/locations/diskOperations/read | 取得非同步磁碟作業的狀態 |
> |  | **位置/logAnalytics** |  |
> | 動作 | Microsoft.Compute/locations/logAnalytics/getRequestRateByInterval/action | 建立記錄以依時間間隔顯示要求總數以協助進行節流診斷。 |
> | 動作 | Microsoft.Compute/locations/logAnalytics/getThrottledRequests/action | 建立記錄以依 ResourceName、OperationName 或已套用的節流原則顯示已節流要求彙總。 |
> |  | **位置/作業** |  |
> | 動作 | Microsoft.Compute/locations/operations/read | 取得非同步作業的狀態 |
> |  | **位置/發行者** |  |
> | 動作 | Microsoft.Compute/locations/publishers/read | 取得發行者的屬性 |
> |  | **位置/發行者/artifacttypes/優惠** |  |
> | 動作 | Microsoft.Compute/locations/publishers/artifacttypes/offers/read | 取得平台映像提供的屬性 |
> |  | **位置/發行者/artifacttypes/供應專案/sku** |  |
> | 動作 | Microsoft.Compute/locations/publishers/artifacttypes/offers/skus/read | 取得平台映像 SKU 的屬性 |
> |  | **位置/發行者/artifacttypes/供應專案/sku/版本** |  |
> | 動作 | Microsoft.Compute/locations/publishers/artifacttypes/offers/skus/versions/read | 取得平台映像版本的屬性 |
> |  | **位置/發行者/artifacttypes/類型** |  |
> | 動作 | Microsoft.Compute/locations/publishers/artifacttypes/types/read | 取得 VMExtension 類型的屬性 |
> |  | **位置/發行者/artifacttypes/類型/版本** |  |
> | 動作 | Microsoft.Compute/locations/publishers/artifacttypes/types/versions/read | 取得 VMExtension 版本的屬性 |
> |  | **位置/runCommands** |  |
> | 動作 | Microsoft.Compute/locations/runCommands/read | 列出位置中可用的執行命令 |
> |  | **位置/使用方式** |  |
> | 動作 | Microsoft.Compute/locations/usages/read | 取得訂用帳戶在某個位置之計算資源的服務限制和目前的使用量 |
> |  | **位置/的 vmsizes** |  |
> | 動作 | Microsoft.Compute/locations/vmSizes/read | 列出某個位置可用的虛擬機器大小 |
> |  | **位置/vsmOperations** |  |
> | 動作 | Microsoft。計算/位置/vsmOperations/讀取 | 使用虛擬機器執行時間服務延伸模組，取得虛擬機器擴展集的非同步作業狀態 |
> |  | **業務** |  |
> | 動作 | Microsoft.Compute/operations/read | 列出 Microsoft.Compute 資源提供者的可用作業 |
> |  | **proximityPlacementGroups** |  |
> | 動作 | Microsoft。計算/proximityPlacementGroups/讀取 | 取得鄰近放置群組的屬性 |
> | 動作 | Microsoft。計算/proximityPlacementGroups/寫入 | 建立新的鄰近放置群組，或更新現有的 |
> | 動作 | Microsoft。計算/proximityPlacementGroups/刪除 | 刪除鄰近位置群組 |
> |  | **restorePointCollections** |  |
> | 動作 | Microsoft.Compute/restorePointCollections/read | 取得還原點集合的屬性 |
> | 動作 | Microsoft.Compute/restorePointCollections/write | 建立新的還原點集合，或更新現有還原點集合 |
> | 動作 | Microsoft.Compute/restorePointCollections/delete | 刪除還原點集合和內含的還原點 |
> |  | **restorePointCollections/restorePoints** |  |
> | 動作 | Microsoft.Compute/restorePointCollections/restorePoints/read | 取得還原點的屬性 |
> | 動作 | Microsoft.Compute/restorePointCollections/restorePoints/write | 建立新的還原點 |
> | 動作 | Microsoft.Compute/restorePointCollections/restorePoints/delete | 刪除還原點 |
> | 動作 | Microsoft.Compute/restorePointCollections/restorePoints/retrieveSasUris/action | 取得還原點屬性以及 Blob SAS URI |
> |  | **sharedVMImages** |  |
> | 動作 | Microsoft.Compute/sharedVMImages/read | 取得 SharedVMImage 的屬性 |
> | 動作 | Microsoft.Compute/sharedVMImages/write | 建立新的 SharedVMImage，或更新現有的 SharedVMImage |
> | 動作 | Microsoft.Compute/sharedVMImages/delete | 刪除 SharedVMImage |
> |  | **sharedVMImages/版本** |  |
> | 動作 | Microsoft.Compute/sharedVMImages/versions/read | 取得 SharedVMImageVersion 的屬性 |
> | 動作 | Microsoft.Compute/sharedVMImages/versions/write | 建立新的 SharedVMImageVersion，或更新現有的 SharedVMImageVersion |
> | 動作 | Microsoft.Compute/sharedVMImages/versions/delete | 刪除 SharedVMImageVersion |
> | 動作 | Microsoft.Compute/sharedVMImages/versions/replicate/action | 將 SharedVMImageVersion 複寫至目標區域 |
> |  | **sku** |  |
> | 動作 | Microsoft.Compute/skus/read | 取得可供您的訂用帳戶使用的 Microsoft.Compute SKU 清單 |
> |  | **快照集** |  |
> | 動作 | Microsoft.Compute/snapshots/read | 取得快照集的屬性 |
> | 動作 | Microsoft.Compute/snapshots/write | 建立新的快照集，或更新現有快照集 |
> | 動作 | Microsoft.Compute/snapshots/delete | 刪除快照集 |
> | 動作 | Microsoft.Compute/snapshots/beginGetAccess/action | 取得快照集的 SAS URI 以用於 Blob 存取 |
> | 動作 | Microsoft.Compute/snapshots/endGetAccess/action | 撤銷快照集的 SAS URI |
> |  | **virtualMachines** |  |
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
> | 動作 | Microsoft。 Compute/virtualMachines/installPatches/action | 會根據使用者提供的參數，在虛擬機器上安裝可用的 OS 更新修補程式。 包含可用修補程式清單的評量結果也會在此過程中重新整理。 |
> | 動作 | Microsoft。 Compute/virtualMachines/assessPatches/action | 評估虛擬機器並尋找其可用的 OS 更新修補程式清單。 |
> | 動作 | Microsoft。 Compute/virtualMachines/cancelPatchInstallation/action | 取消虛擬機器上進行中的安裝 OS 更新修補作業。 |
> |  | **virtualMachines/extensions** |  |
> | 動作 | Microsoft.Compute/virtualMachines/extensions/read | 取得虛擬機器擴充功能的屬性 |
> | 動作 | Microsoft.Compute/virtualMachines/extensions/write | 建立新的虛擬機器擴充功能，或更新現有的虛擬機器擴充功能 |
> | 動作 | Microsoft.Compute/virtualMachines/extensions/delete | 刪除虛擬機器擴充功能 |
> |  | **virtualMachines/instanceView** |  |
> | 動作 | Microsoft.Compute/virtualMachines/instanceView/read | 取得虛擬機器和其資源的詳細執行階段狀態 |
> |  | **virtualMachines/的 vmsizes** |  |
> | 動作 | Microsoft.Compute/virtualMachines/vmSizes/read | 列出虛擬機器所能更新成的大小 |
> |  | **virtualMachineScaleSets** |  |
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
> | 動作 | Microsoft。 Compute/virtualMachineScaleSets/rollingUpgrades/action | 取消虛擬機器擴展集的輪流升級 |
> |  | **virtualMachineScaleSets/extensions** |  |
> | 動作 | Microsoft.Compute/virtualMachineScaleSets/extensions/read | 取得虛擬機器擴展集延伸模組的屬性 |
> | 動作 | Microsoft.Compute/virtualMachineScaleSets/extensions/write | 建立新的虛擬機器擴展集延伸模組或更新現有的 |
> | 動作 | Microsoft.Compute/virtualMachineScaleSets/extensions/delete | 刪除虛擬機器擴展集延伸模組 |
> |  | **virtualMachineScaleSets/擴充功能/角色** |  |
> | 動作 | Microsoft。計算/virtualMachineScaleSets/延伸模組/角色/讀取 | 使用虛擬機器執行時間服務延伸模組，取得虛擬機器擴展集中角色的屬性 |
> | 動作 | Microsoft。計算/virtualMachineScaleSets/延伸模組/角色/寫入 | 使用虛擬機器執行時間服務延伸模組來更新虛擬機器擴展集中現有角色的屬性 |
> |  | **virtualMachineScaleSets/instanceView** |  |
> | 動作 | Microsoft.Compute/virtualMachineScaleSets/instanceView/read | 取得虛擬機器擴展集的執行個體檢視 |
> |  | **virtualMachineScaleSets/networkInterfaces** |  |
> | 動作 | Microsoft.Compute/virtualMachineScaleSets/networkInterfaces/read | 取得虛擬機器擴展集之所有網路介面的屬性 |
> |  | **virtualMachineScaleSets/osUpgradeHistory** |  |
> | 動作 | Microsoft.Compute/virtualMachineScaleSets/osUpgradeHistory/read | 取得虛擬機器擴展集的 OS 升級歷程記錄 |
> |  | **virtualMachineScaleSets/publicIPAddresses** |  |
> | 動作 | Microsoft.Compute/virtualMachineScaleSets/publicIPAddresses/read | 取得虛擬機器擴展集之所有公用 IP 位址的屬性 |
> |  | **virtualMachineScaleSets/rollingUpgrades** |  |
> | 動作 | Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades/read | 取得虛擬機器擴展集最新的輪流升級狀態 |
> |  | **virtualMachineScaleSets/sku** |  |
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
> |  | **virtualMachineScaleSets/virtualMachines/instanceView** |  |
> | 動作 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/instanceView/read | 擷取 VM 擴展集內的虛擬機器執行個體檢視。 |
> |  | **virtualMachineScaleSets/virtualMachines/networkInterfaces** |  |
> | 動作 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/read | 取得使用虛擬機器擴展集建立的虛擬機器其中一個或所有網路介面屬性 |
> |  | **virtualMachineScaleSets/virtualMachines/networkInterfaces/Ipconfiguration** |  |
> | 動作 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/read | 取得使用虛擬機器擴展集建立的網路介面其中一個或所有 IP 設定屬性。 IP 設定代表私人 IP |
> |  | **virtualMachineScaleSets/virtualMachines/networkInterfaces/Ipconfiguration/publicIPAddresses** |  |
> | 動作 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/publicIPAddresses/read | 取得使用虛擬機器擴展集建立的公用 IP 位址屬性。 虛擬機器擴展集最多可以在每個 ipconfiguration (私人 IP) 建立一個公用 IP |
> |  | **virtualMachineScaleSets/的 vmsizes** |  |
> | 動作 | Microsoft。 Compute/virtualMachineScaleSets/的 vmsizes/read | 列出在虛擬機器擴展集中建立或更新虛擬機器的可用大小 |
> |  | **virtualMachines** |  |
> | DataAction | Microsoft.Compute/virtualMachines/login/action | 以一般使用者身分登入虛擬機器 |
> | DataAction | Microsoft.Compute/virtualMachines/loginAsAdmin/action | 以 Windows 系統管理員或 Linux 根使用者權限登入虛擬機器 |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

Azure 服務：[成本管理](../cost-management-billing/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.Consumption/register/action | 向 Consumption RP 註冊 |
> |  | **aggregatedcost** |  |
> | 動作 | Microsoft. 耗用量/aggregatedcost/讀取 | 列出管理群組的 AggregatedCost。 |
> |  | **餘額** |  |
> | 動作 | Microsoft.Consumption/balances/read | 針對管理群組列出計費期的使用率摘要。 |
> |  | **對應** |  |
> | 動作 | Microsoft.Consumption/budgets/read | 依訂用帳戶或管理群組列出預算。 |
> | 動作 | Microsoft.Consumption/budgets/write | 依訂用帳戶或管理群組建立和更新預算。 |
> | 動作 | Microsoft.Consumption/budgets/delete | 依訂用帳戶或管理群組刪除預算。 |
> |  | **費用** |  |
> | 動作 | Microsoft.Consumption/charges/read | 列出費用 |
> |  | **信用** |  |
> | 動作 | Microsoft.Consumption/credits/read | 列出信用額度 |
> |  | **events** |  |
> | 動作 | Microsoft.Consumption/events/read | 列出事件 |
> |  | **externalBillingAccounts/標記** |  |
> | 動作 | Microsoft. 耗用量/externalBillingAccounts/標記/讀取 | 列出 EA 和訂用帳戶的標記。 |
> |  | **externalSubscriptions/標記** |  |
> | 動作 | Microsoft. 耗用量/externalSubscriptions/標記/讀取 | 列出 EA 和訂用帳戶的標記。 |
> |  | **天氣預報** |  |
> | 動作 | Microsoft.Consumption/forecasts/read | 列出預測 |
> |  | **許多** |  |
> | 動作 | Microsoft.Consumption/lots/read | 列出位置 |
> |  | **市場** |  |
> | 動作 | Microsoft.Consumption/marketplaces/read | 針對適用於 EA 與 WebDirect 訂用帳戶的範圍列出 Marketplace 資源使用狀況詳細資料。 |
> |  | **operationresults** |  |
> | 動作 | Microsoft.Consumption/operationresults/read | 列出作業結果 |
> |  | **業務** |  |
> | 動作 | Microsoft.Consumption/operations/read | 列出 Microsoft.Consumption 資源提供者支援的所有作業。 |
> |  | **operationstatus** |  |
> | 動作 | Microsoft.Consumption/operationstatus/read | 列出作業狀態 |
> |  | **pricesheets** |  |
> | 動作 | Microsoft.Consumption/pricesheets/read | 列出適用於訂用帳戶或管理群組的價位表資料。 |
> |  | **reservationDetails** |  |
> | 動作 | Microsoft.Consumption/reservationDetails/read | 依保留順序或管理群組列出保留執行個體的使用率詳細資料。 詳細資料屬於每日層級的每個執行個體。 |
> |  | **reservationRecommendations** |  |
> | 動作 | Microsoft.Consumption/reservationRecommendations/read | 列出訂用帳戶保留執行個體的單一或共用建議。 |
> |  | **reservationSummaries** |  |
> | 動作 | Microsoft.Consumption/reservationSummaries/read | 依保留順序或管理群組列出保留執行個體的使用率摘要。 摘要資料位於每月或每日層級。 |
> |  | **reservationTransactions** |  |
> | 動作 | Microsoft.Consumption/reservationTransactions/read | 依管理群組列出保留執行個體的交易記錄。 |
> |  | **「標記」** |  |
> | 動作 | Microsoft.Consumption/tags/read | 列出 EA 和訂用帳戶的標記。 |
> |  | **租戶** |  |
> | 動作 | Microsoft.Consumption/tenants/register/action | 依某個租用戶註冊 Microsoft.Consumption 範圍的動作。 |
> | 動作 | Microsoft. 耗用量/租使用者/讀取 | 列出租使用者 |
> |  | **條款** |  |
> | 動作 | Microsoft.Consumption/terms/read | 列出適用於訂用帳戶或管理群組的條款。 |
> |  | **usageDetails** |  |
> | 動作 | Microsoft.Consumption/usageDetails/read | 針對適用於 EA 與 WebDirect 訂用帳戶的範圍列出使用狀況詳細資料。 |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

Azure 服務：[容器實例](../container-instances/index.yml)

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
> |  | **containerGroups/容器** |  |
> | 動作 | Microsoft.ContainerInstance/containerGroups/containers/exec/action | 執行至特定容器內。 |
> |  | **containerGroups/容器/buildlogs** |  |
> | 動作 | Microsoft.containerinstance/containerGroups/容器/buildlogs/read | 取得特定容器的組建記錄檔。 |
> |  | **containerGroups/容器/記錄** |  |
> | 動作 | Microsoft.ContainerInstance/containerGroups/containers/logs/read | 取得特定容器的記錄。 |
> |  | **containerGroups/operationResults** |  |
> | 動作 | Microsoft.containerinstance/containerGroups/operationResults/read | 取得非同步作業結果 |
> |  | **containerGroups/providers/Microsoft Insights/diagnosticSettings** |  |
> | 動作 | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/diagnosticSettings/read | 取得容器群組的診斷設定。 |
> | 動作 | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/diagnosticSettings/write | 建立或更新容器群組的診斷設定。 |
> |  | **containerGroups/providers/Microsoft Insights/Metricdefinitions.listasync** |  |
> | 動作 | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/metricDefinitions/read | 取得容器群組的可用計量。 |
> |  | **堆放** |  |
> | 動作 | Microsoft.ContainerInstance/locations/deleteVirtualNetworkOrSubnets/action | 通知 Microsoft.ContainerInstance 正在刪除虛擬網路或子網路。 |
> |  | **位置/cachedImages** |  |
> | 動作 | Microsoft.ContainerInstance/locations/cachedImages/read | 取得區域中訂用帳戶的快取映像。 |
> |  | **位置/功能** |  |
> | 動作 | Microsoft.ContainerInstance/locations/capabilities/read | 取得區域的功能。 |
> |  | **位置/作業** |  |
> | 動作 | Microsoft.containerinstance/位置/作業/讀取 | 列出 Azure 容器實例服務的作業。 |
> |  | **位置/使用方式** |  |
> | 動作 | Microsoft.ContainerInstance/locations/usages/read | 取得特定區域的使用量。 |
> |  | **業務** |  |
> | 動作 | Microsoft.containerinstance/作業/讀取 | 列出 Azure 容器實例服務的作業。 |
> |  | **serviceassociationlinks** |  |
> | 動作 | Microsoft.containerinstance/serviceassociationlinks/delete | 刪除子網上的 azure 容器實例資源提供者所建立的服務關聯連結。 |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

Azure 服務： [Container Registry](../container-registry/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.ContainerRegistry/register/action | 針對容器登錄資源提供者註冊訂用帳戶，並讓您能夠建立容器登錄。 |
> |  | **checkNameAvailability** |  |
> | 動作 | Microsoft.ContainerRegistry/checkNameAvailability/read | 檢查容器登錄名稱是否可供使用。 |
> |  | **堆放** |  |
> | 動作 | Microsoft.ContainerRegistry/locations/deleteVirtualNetworkOrSubnets/action | 讓 Microsoft.ContainerRegistry 知道虛擬網路或子網路即將刪除 |
> |  | **位置/operationResults** |  |
> | 動作 | Microsoft.ContainerRegistry/locations/operationResults/read | 取得非同步作業結果 |
> |  | **業務** |  |
> | 動作 | Microsoft.ContainerRegistry/operations/read | 列出所有可用的 Azure Container Registry REST API 作業 |
> |  | **機構** |  |
> | 動作 | Microsoft.ContainerRegistry/registries/read | 取得指定容器登錄的屬性，或列出指定資源群組或訂用帳戶下的所有容器登錄。 |
> | 動作 | Microsoft.ContainerRegistry/registries/write | 使用指定參數，建立或更新容器登錄。 |
> | 動作 | Microsoft.ContainerRegistry/registries/delete | 刪除容器登錄。 |
> | 動作 | Microsoft.ContainerRegistry/registries/listCredentials/action | 列出指定容器登錄的登入認證。 |
> | 動作 | Microsoft.ContainerRegistry/registries/regenerateCredential/action | 針對指定容器登錄重新產生一個登入認證。 |
> | 動作 | ContainerRegistry/registry/generateCredentials/action | 為指定容器登錄的權杖產生金鑰。 |
> | 動作 | Microsoft.ContainerRegistry/registries/importImage/action | 使用指定參數將映像匯入到容器登錄。 |
> | 動作 | Microsoft.ContainerRegistry/registries/getBuildSourceUploadUrl/action | 取得上傳位置，讓使用者可上傳來源。 |
> | 動作 | Microsoft.ContainerRegistry/registries/queueBuild/action | 根據要求參數建立新的組建，並將它新增至組建佇列。 |
> | 動作 | Microsoft.ContainerRegistry/registries/listBuildSourceUploadUrl/action | 取得容器登錄的來源上傳 URL 位置。 |
> | 動作 | Microsoft.ContainerRegistry/registries/scheduleRun/action | 排程對容器登錄的流程執行。 |
> |  | **登錄/agentpools** |  |
> | 動作 | ContainerRegistry/registry/agentpools/listQueueStatus/action | 列出容器登錄之 agentpool 的所有佇列狀態。 |
> |  | **登錄/構件** |  |
> | 動作 | ContainerRegistry/registry/構件/delete | 刪除容器登錄中的成品。 |
> |  | **登錄/組建** |  |
> | 動作 | Microsoft.ContainerRegistry/registries/builds/read | 取得指定組建的屬性，或列出指定容器登錄的所有組建。 |
> | 動作 | Microsoft.ContainerRegistry/registries/builds/write | 使用指定參數更新容器登錄的組建。 |
> | 動作 | Microsoft.ContainerRegistry/registries/builds/getLogLink/action | 取得下載組建記錄的連結。 |
> | 動作 | Microsoft.ContainerRegistry/registries/builds/cancel/action | 取消現有組建。 |
> |  | **登錄/buildTasks** |  |
> | 動作 | Microsoft.ContainerRegistry/registries/buildTasks/read | 取得指定組建工作的屬性，或列出指定容器登錄的所有組建工作。 |
> | 動作 | Microsoft.ContainerRegistry/registries/buildTasks/write | 使用指定參數建立或更新容器登錄的組建工作。 |
> | 動作 | Microsoft.ContainerRegistry/registries/buildTasks/delete | 刪除容器登錄中的組建工作。 |
> | 動作 | Microsoft.ContainerRegistry/registries/buildTasks/listSourceRepositoryProperties/action | 列出組建工作的原始檔控制屬性。 |
> |  | **登錄/buildTasks/步驟** |  |
> | 動作 | Microsoft.ContainerRegistry/registries/buildTasks/steps/read | 取得指定組建步驟的屬性，或列出指定組建工作的所有組建步驟。 |
> | 動作 | Microsoft.ContainerRegistry/registries/buildTasks/steps/write | 使用指定參數建立或更新組建工作的組建步驟。 |
> | 動作 | Microsoft.ContainerRegistry/registries/buildTasks/steps/delete | 從組建工作中刪除組建步驟。 |
> | 動作 | Microsoft.ContainerRegistry/registries/buildTasks/steps/listBuildArguments/action | 列出組建步驟的組建引數，包括祕密引數。 |
> |  | **登錄/eventGridFilters** |  |
> | 動作 | Microsoft.ContainerRegistry/registries/eventGridFilters/read | 取得指定事件格線篩選的屬性，或列出指定容器登錄的所有事件格線篩選。 |
> | 動作 | Microsoft.ContainerRegistry/registries/eventGridFilters/write | 使用指定參數，建立或更新容器登錄的事件格線篩選。 |
> | 動作 | Microsoft.ContainerRegistry/registries/eventGridFilters/delete | 從容器登錄中刪除事件格線篩選。 |
> |  | **登錄/listPolicies** |  |
> | 動作 | Microsoft.ContainerRegistry/registries/listPolicies/read | 列出指定容器登錄的原則 |
> |  | **登錄/listUsages** |  |
> | 動作 | Microsoft.ContainerRegistry/registries/listUsages/read | 列出指定容器登錄的配額使用量。 |
> |  | **登錄/中繼資料** |  |
> | 動作 | ContainerRegistry/登錄/中繼資料/讀取 | 取得容器登錄之特定存放庫的中繼資料 |
> | 動作 | ContainerRegistry/登錄/中繼資料/寫入 | 更新容器登錄庫的中繼資料 |
> |  | **登錄/operationStatuses** |  |
> | 動作 | Microsoft.ContainerRegistry/registries/operationStatuses/read | 取得登錄非同步作業狀態 |
> |  | **登錄/privateEndpointConnectionProxies** |  |
> | 動作 | ContainerRegistry/registry/privateEndpointConnectionProxies/validate/action | 驗證私用端點連接 Proxy （僅限 NRP） |
> | 動作 | ContainerRegistry/registry/privateEndpointConnectionProxies/read | 取得私人端點連接 Proxy （僅限 NRP） |
> | 動作 | ContainerRegistry/registry/privateEndpointConnectionProxies/write | 建立私用端點連接 Proxy （僅限 NRP） |
> | 動作 | ContainerRegistry/registry/privateEndpointConnectionProxies/delete | 刪除私人端點連接 Proxy （僅限 NRP） |
> |  | **登錄/privateEndpointConnectionProxies/operationStatuses** |  |
> | 動作 | ContainerRegistry/registry/privateEndpointConnectionProxies/operationStatuses/read | 取得私用端點連接 Proxy 非同步作業狀態 |
> |  | **登錄/提取** |  |
> | 動作 | Microsoft.ContainerRegistry/registries/pull/read | 從容器登錄中提取或取得映像。 |
> |  | **登錄/推播** |  |
> | 動作 | Microsoft.ContainerRegistry/registries/push/write | 將映像推送或寫入至容器登錄。 |
> |  | **登錄/隔離** |  |
> | 動作 | ContainerRegistry/登錄/隔離/讀取 | 從容器登錄中提取或取得隔離的映像 |
> | 動作 | ContainerRegistry/登錄/隔離/寫入 | 寫入/修改已隔離映像的隔離狀態 |
> |  | **登錄/複寫** |  |
> | 動作 | Microsoft.ContainerRegistry/registries/replications/read | 取得指定複寫的屬性，或列出指定容器登錄的所有複寫。 |
> | 動作 | Microsoft.ContainerRegistry/registries/replications/write | 使用指定參數，建立或更新容器登錄的複寫。 |
> | 動作 | Microsoft.ContainerRegistry/registries/replications/delete | 刪除容器登錄中的複寫。 |
> |  | **登錄/複寫/operationStatuses** |  |
> | 動作 | Microsoft.ContainerRegistry/registries/replications/operationStatuses/read | 取得複寫非同步作業狀態 |
> |  | **登錄/執行** |  |
> | 動作 | Microsoft.ContainerRegistry/registries/runs/read | 對容器登錄或清單流程執行取得流程執行的屬性。 |
> | 動作 | Microsoft.ContainerRegistry/registries/runs/write | 更新流程執行。 |
> | 動作 | Microsoft.ContainerRegistry/registries/runs/listLogSasUrl/action | 取得流程執行的記錄檔 SAS URL。 |
> | 動作 | Microsoft.ContainerRegistry/registries/runs/cancel/action | 取消現有的流程執行。 |
> |  | **登錄/scopeMaps** |  |
> | 動作 | ContainerRegistry/registry/scopeMaps/read | 取得指定之範圍對應的屬性，或列出指定容器登錄的所有範圍對應。 |
> | 動作 | ContainerRegistry/registry/scopeMaps/write | 使用指定的參數，建立或更新容器登錄的範圍對應。 |
> | 動作 | ContainerRegistry/registry/scopeMaps/delete | 從容器登錄中刪除範圍對應。 |
> |  | **登錄/scopeMaps/operationStatuses** |  |
> | 動作 | ContainerRegistry/registry/scopeMaps/operationStatuses/read | 取得範圍對應非同步作業狀態。 |
> |  | **登錄/簽署** |  |
> | 動作 | Microsoft.ContainerRegistry/registries/sign/write | 推送/提取容器登錄的內容信任中繼資料。 |
> |  | **登錄/taskruns** |  |
> | 動作 | ContainerRegistry/registry/taskruns/listDetails/action | 列出容器登錄之/tr u n 的所有詳細資料。 |
> |  | **登錄/工作** |  |
> | 動作 | Microsoft.ContainerRegistry/registries/tasks/read | 取得容器登錄的工作或列出所有工作。 |
> | 動作 | Microsoft.ContainerRegistry/registries/tasks/write | 建立或更新容器登錄的工作。 |
> | 動作 | Microsoft.ContainerRegistry/registries/tasks/delete | 刪除容器登錄的工作。 |
> | 動作 | Microsoft.ContainerRegistry/registries/tasks/listDetails/action | 列出容器登錄工作的所有詳細資料。 |
> |  | **登錄/權杖** |  |
> | 動作 | ContainerRegistry/登錄/權杖/讀取 | 取得指定之權杖的屬性，或列出指定容器登錄的所有權杖。 |
> | 動作 | ContainerRegistry/登錄/權杖/寫入 | 使用指定的參數，建立或更新容器登錄的權杖。 |
> | 動作 | ContainerRegistry/registry/token/delete | 從容器登錄中刪除權杖。 |
> |  | **登錄/權杖/operationStatuses** |  |
> | 動作 | ContainerRegistry/registry/token/operationStatuses/read | 取得權杖非同步作業狀態。 |
> |  | **登錄/updatePolicies** |  |
> | 動作 | Microsoft.ContainerRegistry/registries/updatePolicies/write | 更新指定容器登錄的原則 |
> |  | **登錄/webhook** |  |
> | 動作 | Microsoft.ContainerRegistry/registries/webhooks/read | 取得指定 Webhook 的屬性，或列出指定容器登錄的所有 Webhook。 |
> | 動作 | Microsoft.ContainerRegistry/registries/webhooks/write | 使用指定參數，建立或更新容器登錄的 Webhook。 |
> | 動作 | Microsoft.ContainerRegistry/registries/webhooks/delete | 刪除容器登錄中的 Webhook。 |
> | 動作 | Microsoft.ContainerRegistry/registries/webhooks/getCallbackConfig/action | 針對 Webhook 取得服務 URI 的設定和自訂標頭。 |
> | 動作 | Microsoft.ContainerRegistry/registries/webhooks/ping/action | 觸發要傳送到 Webhook 的 Ping 事件。 |
> | 動作 | Microsoft.ContainerRegistry/registries/webhooks/listEvents/action | 針對指定的 Webhook 列出最近的事件。 |
> |  | **登錄/webhook/operationStatuses** |  |
> | 動作 | Microsoft.ContainerRegistry/registries/webhooks/operationStatuses/read | 取得 Webhook 非同步作業狀態 |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

Azure 服務： [Azure Kubernetes Service （AKS）](../aks/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.ContainerService/register/action | 向 Microsoft.ContainerService 資源提供者註冊訂用帳戶 |
> | 動作 | Microsoft.ContainerService/unregister/action | 向 Microsoft.ContainerService 資源提供者取消註冊訂用帳戶 |
> |  | **containerServices** |  |
> | 動作 | Microsoft.ContainerService/containerServices/read | 取得容器服務 |
> | 動作 | Microsoft.ContainerService/containerServices/write | 建立新的容器服務，或更新現有的容器服務 |
> | 動作 | Microsoft.ContainerService/containerServices/delete | 刪除容器服務 |
> |  | **位置/operationresults** |  |
> | 動作 | Microsoft.ContainerService/locations/operationresults/read | 取得非同步作業結果的狀態 |
> |  | **位置/作業** |  |
> | 動作 | Microsoft.ContainerService/locations/operations/read | 取得非同步作業的狀態 |
> |  | **位置/協調器** |  |
> | 動作 | Microsoft.ContainerService/locations/orchestrators/read | 列出支援的協調器 |
> |  | **managedClusters** |  |
> | 動作 | Microsoft.ContainerService/managedClusters/read | 取得受控叢集 |
> | 動作 | Microsoft.ContainerService/managedClusters/write | 建立新的受控叢集，或更新現有的受控叢集 |
> | 動作 | Microsoft.ContainerService/managedClusters/delete | 刪除受控叢集 |
> | 動作 | Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action | 列出受控叢集的 clusterAdmin 認證 |
> | 動作 | Microsoft.ContainerService/managedClusters/listClusterUserCredential/action | 列出受控叢集的 clusterUser 認證 |
> | 動作 | Microsoft.containerservice/managedClusters/listClusterMonitoringUserCredential/action | 列出受控叢集的 clusterMonitoringUser 認證 |
> | 動作 | Microsoft.ContainerService/managedClusters/resetServicePrincipalProfile/action | 重設受控叢集的服務主體 |
> | 動作 | Microsoft.ContainerService/managedClusters/resetAADProfile/action | 重設受控叢集的 AAD 設定檔 |
> | 動作 | Microsoft.containerservice/managedClusters/rotateClusterCertificates/action | 輪替受控叢集的憑證 |
> | 動作 | Microsoft.containerservice/managedClusters/privateEndpointConnectionsApproval/action | 決定是否允許使用者核准私用端點連接 |
> |  | **managedClusters/accessProfiles** |  |
> | 動作 | Microsoft.ContainerService/managedClusters/accessProfiles/read | 依角色名稱取得受控叢集存取設定檔 |
> | 動作 | Microsoft.ContainerService/managedClusters/accessProfiles/listCredential/action | 使用清單認證依角色名稱取得受控叢集存取設定檔 |
> |  | **managedClusters/agentPools** |  |
> | 動作 | Microsoft.containerservice/managedClusters/agentPools/read | 取得代理程式組件區 |
> | 動作 | Microsoft.containerservice/managedClusters/agentPools/write | 建立新的代理程式組件區，或更新現有的 |
> | 動作 | Microsoft.containerservice/managedClusters/agentPools/delete | 刪除代理程式組件區 |
> |  | **managedClusters/agentPools/upgradeProfiles** |  |
> | 動作 | Microsoft.containerservice/managedClusters/agentPools/upgradeProfiles/read | 取得代理程式組件區的升級設定檔 |
> |  | **managedClusters/availableAgentPoolVersions** |  |
> | 動作 | Microsoft.containerservice/managedClusters/availableAgentPoolVersions/read | 取得叢集的可用代理程式組件區版本 |
> |  | **managedClusters/偵測器** |  |
> | 動作 | Microsoft.containerservice/managedClusters/偵測器/read | 取得受控叢集偵測器 |
> |  | **managedClusters/diagnosticsState** |  |
> | 動作 | Microsoft.containerservice/managedClusters/diagnosticsState/read | 取得叢集的診斷狀態 |
> |  | **managedClusters/upgradeProfiles** |  |
> | 動作 | Microsoft.containerservice/managedClusters/upgradeProfiles/read | 取得叢集的升級設定檔 |
> |  | **openShiftClusters** |  |
> | 動作 | Microsoft.ContainerService/openShiftClusters/read | 取得開啟的轉移叢集 |
> | 動作 | Microsoft.ContainerService/openShiftClusters/write | 建立新的或更新現有的 Open Shift 叢集 |
> | 動作 | Microsoft.ContainerService/openShiftClusters/delete | 刪除開啟的轉移叢集 |
> |  | **openShiftManagedClusters** |  |
> | 動作 | Microsoft.ContainerService/openShiftManagedClusters/read | 取得 Open Shift 受控叢集 |
> | 動作 | Microsoft.ContainerService/openShiftManagedClusters/write | 建立新的或更新現有的 Open Shift 受控叢集 |
> | 動作 | Microsoft.ContainerService/openShiftManagedClusters/delete | 刪除 Open Shift 受控叢集 |
> |  | **業務** |  |
> | 動作 | Microsoft.ContainerService/operations/read | 列出 Microsoft.ContainerService 資源提供者的可用作業 |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

Azure 服務：[成本管理](../cost-management-billing/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.CostManagement/query/action | 依範圍查使用狀況資料。 |
> | 動作 | Microsoft.CostManagement/reports/action | 依範圍排定使用狀況資料報告。 |
> | 動作 | Microsoft.CostManagement/exports/action | 執行指定的匯出。 |
> | 動作 | CostManagement/register/action | 為訂用帳戶 CostManagement 的範圍註冊動作。 |
> | 動作 | CostManagement/views/action | 建立 view。 |
> | 動作 | CostManagement/預測/動作 | 依範圍預測使用量資料。 |
> |  | **alerts** |  |
> | 動作 | CostManagement/警示/寫入 | 更新警示。 |
> |  | **cloudConnectors** |  |
> | 動作 | CostManagement/cloudConnectors/read | 列出已驗證之使用者的 cloudConnectors。 |
> | 動作 | CostManagement/cloudConnectors/write | 建立或更新指定的 Cloudconnector 為必要。 |
> | 動作 | CostManagement/cloudConnectors/delete | 刪除指定的 Cloudconnector 為必要。 |
> |  | **維** |  |
> | 動作 | Microsoft.CostManagement/dimensions/read | 依範圍列出所有支援的維度。 |
> |  | **多餘** |  |
> | 動作 | Microsoft.CostManagement/exports/read | 依範圍列出匯出。 |
> | 動作 | Microsoft.CostManagement/exports/write | 建立或更新指定的匯出。 |
> | 動作 | Microsoft.CostManagement/exports/delete | 刪除指定的匯出。 |
> | 動作 | CostManagement/匯出/執行/動作 | 執行匯出。 |
> |  | **externalBillingAccounts** |  |
> | 動作 | CostManagement/externalBillingAccounts/read | 列出已驗證之使用者的 externalBillingAccounts。 |
> | 動作 | CostManagement/externalBillingAccounts/查詢/動作 | 查詢外部 BillingAccounts 的使用量資料。 |
> | 動作 | CostManagement/externalBillingAccounts/預測/動作 | 預測外部 BillingAccounts 的使用量資料。 |
> |  | **externalBillingAccounts/維度** |  |
> | 動作 | CostManagement/externalBillingAccounts/dimension/read | 列出外部 BillingAccounts 的所有支援維度。 |
> |  | **externalBillingAccounts/externalSubscriptions** |  |
> | 動作 | CostManagement/externalBillingAccounts/externalSubscriptions/read | 列出已驗證使用者的 externalBillingAccount 內的 externalSubscriptions。 |
> |  | **externalBillingAccounts/預測** |  |
> | 動作 | CostManagement/externalBillingAccounts/預測/讀取 | 預測外部 BillingAccounts 的使用量資料。 |
> |  | **externalBillingAccounts/查詢** |  |
> | 動作 | CostManagement/externalBillingAccounts/查詢/讀取 | 查詢外部 BillingAccounts 的使用量資料。 |
> |  | **externalSubscriptions** |  |
> | 動作 | CostManagement/externalSubscriptions/read | 列出已驗證之使用者的 externalSubscriptions。 |
> | 動作 | CostManagement/externalSubscriptions/write | 更新 externalSubscription 的相關管理群組 |
> | 動作 | CostManagement/externalSubscriptions/查詢/動作 | 查詢外部訂用帳戶的使用量資料。 |
> | 動作 | CostManagement/externalSubscriptions/預測/動作 | 預測外部 BillingAccounts 的使用量資料。 |
> |  | **externalSubscriptions/維度** |  |
> | 動作 | CostManagement/externalSubscriptions/dimension/read | 列出外部訂用帳戶的所有支援維度。 |
> |  | **externalSubscriptions/預測** |  |
> | 動作 | CostManagement/externalSubscriptions/預測/讀取 | 預測外部 BillingAccounts 的使用量資料。 |
> |  | **externalSubscriptions/查詢** |  |
> | 動作 | CostManagement/externalSubscriptions/查詢/讀取 | 查詢外部訂用帳戶的使用量資料。 |
> |  | **預見性** |  |
> | 動作 | CostManagement/預測/讀取 | 依範圍預測使用量資料。 |
> |  | **業務** |  |
> | 動作 | CostManagement/作業/讀取 | 列出 CostManagement 資源提供者所支援的所有作業。 |
> |  | **query** |  |
> | 動作 | Microsoft.CostManagement/query/read | 依範圍查使用狀況資料。 |
> |  | **透視** |  |
> | 動作 | Microsoft.CostManagement/reports/read | 依範圍排定使用狀況資料報告。 |
> |  | **租戶** |  |
> | 動作 | CostManagement/tenant/register/action | 向租使用者註冊 CostManagement 範圍的動作。 |
> |  | **檢視** |  |
> | 動作 | CostManagement/views/read | 列出所有已儲存的視圖。 |
> | 動作 | CostManagement/views/delete | 刪除已儲存的視圖。 |
> | 動作 | CostManagement/views/write | 更新 view。 |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

Azure 服務： [Azure 資料箱](../databox-family/index.md)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.DataBox/register/action | 註冊提供者 Microsoft.Databox |
> | 動作 | DataBox/取消註冊/動作 | 取消註冊提供者 Databox |
> |  | **jobs** |  |
> | 動作 | Microsoft.DataBox/jobs/cancel/action | 取消進行中的訂單。 |
> | 動作 | Microsoft.DataBox/jobs/bookShipmentPickUp/action | 允許預約退貨商品的取貨。 |
> | 動作 | Microsoft.DataBox/jobs/read | 列出或取得訂單 |
> | 動作 | Microsoft.DataBox/jobs/delete | 刪除訂單 |
> | 動作 | Microsoft.DataBox/jobs/write | 建立或更新訂單 |
> | 動作 | Microsoft.DataBox/jobs/listCredentials/action | 列出與訂單相關的未加密認證。 |
> |  | **堆放** |  |
> | 動作 | DataBox/位置/validateInputs/動作 | 這個方法會執行所有類型的驗證。 |
> | 動作 | Microsoft.DataBox/locations/validateAddress/action | 驗證出貨地址，並提供備用的地址 (若有的話)。 |
> | 動作 | Microsoft.DataBox/locations/availableSkus/action | 此方法會傳回可用的 SKU 清單。 |
> | 動作 | DataBox/位置/regionConfiguration/動作 | 這個方法會傳回區域的設定。 |
> |  | **位置/availableSkus** |  |
> | 動作 | DataBox/位置/availableSkus/讀取 | 列出或取得可用的 Sku |
> |  | **位置/operationResults** |  |
> | 動作 | Microsoft.DataBox/locations/operationResults/read | 列出或取得作業結果 |
> |  | **業務** |  |
> | 動作 | DataBox/作業/讀取 | 列出或取得作業 |
> |  | **訂閱/resourceGroups** |  |
> | 動作 | DataBox/訂用帳戶/resourceGroups/moveResources/action | 這個方法會執行資源移動。 |
> | 動作 | DataBox/訂用帳戶/resourceGroups/validateMoveResources/action | 這個方法會驗證是否允許移動資源。 |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

Azure 服務： [Azure Data Box Edge](../databox-online/data-box-edge-overview.md)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> |  | **dataBoxEdgeDevices** |  |
> | 動作 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/uploadCertificate/action | 上傳裝置註冊的憑證 |
> | 動作 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/write | 建立或更新 Data Box Edge 裝置 |
> | 動作 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | 列出或取得 Data Box Edge 裝置 |
> | 動作 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/delete | 刪除 Data Box Edge 裝置 |
> | 動作 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | 列出或取得 Data Box Edge 裝置 |
> | 動作 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | 列出或取得 Data Box Edge 裝置 |
> | 動作 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/write | 建立或更新 Data Box Edge 裝置 |
> | 動作 | DataBoxEdge/dataBoxEdgeDevices/getExtendedInformation/action | 擷取資源延伸資訊 |
> | 動作 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/scanForUpdates/action | 掃描更新 |
> | 動作 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/downloadUpdates/action | 在裝置中下載更新 |
> | 動作 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/installUpdates/action | 在裝置上安裝更新 |
> |  | **dataBoxEdgeDevices/警示** |  |
> | 動作 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/alerts/read | 列出或取得警示 |
> | 動作 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/alerts/read | 列出或取得警示 |
> |  | **dataBoxEdgeDevices/bandwidthSchedules** |  |
> | 動作 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/read | 列出或取得頻寬排程 |
> | 動作 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/read | 列出或取得頻寬排程 |
> | 動作 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/write | 建立或更新頻寬排程 |
> | 動作 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/delete | 刪除頻寬排程 |
> |  | **dataBoxEdgeDevices/bandwidthSchedules/operationResults** |  |
> | 動作 | DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/operationResults/read | 列出或取得作業結果 |
> |  | **dataBoxEdgeDevices/作業** |  |
> | 動作 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/jobs/read | 列出或取得作業 |
> |  | **dataBoxEdgeDevices/networkSettings** |  |
> | 動作 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/networkSettings/read | 列出或取得裝置網路設定 |
> |  | **dataBoxEdgeDevices/節點** |  |
> | 動作 | DataBoxEdge/dataBoxEdgeDevices/節點/讀取 | 列出或取得節點 |
> |  | **dataBoxEdgeDevices/operationResults** |  |
> | 動作 | DataBoxEdge/dataBoxEdgeDevices/operationResults/read | 列出或取得作業結果 |
> |  | **dataBoxEdgeDevices/operationsStatus** |  |
> | 動作 | DataBoxEdge/dataBoxEdgeDevices/operationsStatus/read | 列出或取得作業狀態 |
> |  | **dataBoxEdgeDevices/orders** |  |
> | 動作 | DataBoxEdge/dataBoxEdgeDevices/orders/read | 列出或取得訂單 |
> | 動作 | DataBoxEdge/dataBoxEdgeDevices/orders/read | 列出或取得訂單 |
> | 動作 | DataBoxEdge/dataBoxEdgeDevices/orders/write | 建立或更新訂單 |
> | 動作 | DataBoxEdge/dataBoxEdgeDevices/orders/delete | 刪除訂單 |
> |  | **dataBoxEdgeDevices/orders/operationResults** |  |
> | 動作 | DataBoxEdge/dataBoxEdgeDevices/orders/operationResults/read | 列出或取得作業結果 |
> |  | **dataBoxEdgeDevices/角色** |  |
> | 動作 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/read | 列出或取得角色 |
> | 動作 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/read | 列出或取得角色 |
> | 動作 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/write | 建立或更新角色 |
> | 動作 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/delete | 刪除角色 |
> |  | **dataBoxEdgeDevices/roles/operationResults** |  |
> | 動作 | DataBoxEdge/dataBoxEdgeDevices/roles/operationResults/read | 列出或取得作業結果 |
> |  | **dataBoxEdgeDevices/securitySettings** |  |
> | 動作 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/securitySettings/update/action | 更新安全性設定 |
> |  | **dataBoxEdgeDevices/securitySettings/operationResults** |  |
> | 動作 | DataBoxEdge/dataBoxEdgeDevices/securitySettings/operationResults/read | 列出或取得作業結果 |
> |  | **dataBoxEdgeDevices/共用** |  |
> | 動作 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/read | 列出或取得共用 |
> | 動作 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/read | 列出或取得共用 |
> | 動作 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/write | 建立或更新共用 |
> | 動作 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/refresh/action | 以雲端中的資料重新整理共用中繼資料 |
> | 動作 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/delete | 刪除共用 |
> |  | **dataBoxEdgeDevices/共用/operationResults** |  |
> | 動作 | DataBoxEdge/dataBoxEdgeDevices/共用/operationResults/read | 列出或取得作業結果 |
> |  | **dataBoxEdgeDevices/storageAccountCredentials** |  |
> | 動作 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/write | 建立或更新儲存體帳戶認證 |
> | 動作 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/read | 列出或取得儲存體帳戶認證 |
> | 動作 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/read | 列出或取得儲存體帳戶認證 |
> | 動作 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/delete | 刪除儲存體帳戶認證 |
> |  | **dataBoxEdgeDevices/storageAccountCredentials/operationResults** |  |
> | 動作 | DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/operationResults/read | 列出或取得作業結果 |
> |  | **dataBoxEdgeDevices/storageAccounts** |  |
> | 動作 | DataBoxEdge/dataBoxEdgeDevices/storageAccounts/read | 列出或取得儲存體帳戶 |
> | 動作 | DataBoxEdge/dataBoxEdgeDevices/storageAccounts/read | 列出或取得儲存體帳戶 |
> | 動作 | DataBoxEdge/dataBoxEdgeDevices/storageAccounts/write | 建立或更新儲存體帳戶 |
> | 動作 | DataBoxEdge/dataBoxEdgeDevices/storageAccounts/delete | 刪除儲存體帳戶 |
> |  | **dataBoxEdgeDevices/storageAccounts/容器** |  |
> | 動作 | DataBoxEdge/dataBoxEdgeDevices/storageAccounts/容器/讀取 | 列出或取得容器 |
> | 動作 | DataBoxEdge/dataBoxEdgeDevices/storageAccounts/容器/讀取 | 列出或取得容器 |
> | 動作 | DataBoxEdge/dataBoxEdgeDevices/storageAccounts/容器/寫入 | 建立或更新容器 |
> | 動作 | DataBoxEdge/dataBoxEdgeDevices/storageAccounts/容器/刪除 | 刪除容器 |
> | 動作 | DataBoxEdge/dataBoxEdgeDevices/storageAccounts/容器/重新整理/動作 | 以雲端中的資料重新整理容器中繼資料 |
> |  | **dataBoxEdgeDevices/storageAccounts/容器/operationResults** |  |
> | 動作 | DataBoxEdge/dataBoxEdgeDevices/storageAccounts/container/operationResults/read | 列出或取得作業結果 |
> |  | **dataBoxEdgeDevices/storageAccounts/operationResults** |  |
> | 動作 | DataBoxEdge/dataBoxEdgeDevices/storageAccounts/operationResults/read | 列出或取得作業結果 |
> |  | **dataBoxEdgeDevices/觸發程式** |  |
> | 動作 | DataBoxEdge/dataBoxEdgeDevices/觸發程式/讀取 | 列出或取得觸發程式 |
> | 動作 | DataBoxEdge/dataBoxEdgeDevices/觸發程式/讀取 | 列出或取得觸發程式 |
> | 動作 | DataBoxEdge/dataBoxEdgeDevices/觸發程式/寫入 | 建立或更新觸發程式 |
> | 動作 | DataBoxEdge/dataBoxEdgeDevices/trigger/delete | 刪除觸發程式 |
> |  | **dataBoxEdgeDevices/trigger/operationResults** |  |
> | 動作 | DataBoxEdge/dataBoxEdgeDevices/trigger/operationResults/read | 列出或取得作業結果 |
> |  | **dataBoxEdgeDevices/updateSummary** |  |
> | 動作 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/updateSummary/read | 列出或取得更新摘要 |
> |  | **dataBoxEdgeDevices/使用者** |  |
> | 動作 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/read | 列出或取得共用使用者 |
> | 動作 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/read | 列出或取得共用使用者 |
> | 動作 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/write | 建立或更新共用使用者 |
> | 動作 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/delete | 刪除共用使用者 |
> |  | **dataBoxEdgeDevices/users/operationResults** |  |
> | 動作 | DataBoxEdge/dataBoxEdgeDevices/users/operationResults/read | 列出或取得作業結果 |
> |  | **sku** |  |
> | 動作 | DataBoxEdge/sku/讀取 | 列出或取得 Sku |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

Azure 服務： [Azure Databricks](../azure-databricks/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.Databricks/register/action | 註冊 Databricks。 |
> |  | **堆放** |  |
> | 動作 | Databricks/位置/getNetworkPolicies/動作 | 根據 NRP 所使用的位置，取得子網的網路意圖原則 |
> |  | **位置/operationstatuses** |  |
> | 動作 | Databricks/位置/operationstatuses/讀取 | 讀取資源的作業狀態。 |
> |  | **業務** |  |
> | 動作 | Databricks/作業/讀取 | 取得作業的清單。 |
> |  | **環境** |  |
> | 動作 | Microsoft.Databricks/workspaces/read | 擷取 Databricks 工作區的清單。 |
> | 動作 | Microsoft.Databricks/workspaces/write | 建立 Databricks 工作區。 |
> | 動作 | Microsoft.Databricks/workspaces/delete | 移除 Databricks 工作區。 |
> | 動作 | Databricks/workspace/refreshPermissions/action | 重新整理工作區的許可權 |
> | 動作 | Databricks/workspace/updateDenyAssignment/action | 針對工作區的受控資源群組更新拒絕指派而非動作 |
> | 動作 | Databricks/workspace/refreshWorkspaces/action | 使用新的詳細資料（例如 URL）重新整理工作區 |
> |  | **工作區/dbWorkspaces** |  |
> | 動作 | Databricks/workspace/dbWorkspaces/write | 初始化 Databricks 工作區（僅限內部） |
> |  | **工作區/提供者/Microsoft Insights/diagnosticSettings** |  |
> | 動作 | Microsoft.Databricks/workspaces/providers/Microsoft.Insights/diagnosticSettings/read | 設定 Databricks 工作區的可用診斷設定 |
> | 動作 | Microsoft.Databricks/workspaces/providers/Microsoft.Insights/diagnosticSettings/write | 新增或修改診斷設定。 |
> |  | **工作區/提供者/Microsoft Insights/logDefinitions** |  |
> | 動作 | Microsoft.Databricks/workspaces/providers/Microsoft.Insights/logDefinitions/read | 取得 Databricks 工作區的可用記錄定義 |
> |  | **工作區/storageEncryption** |  |
> | 動作 | Databricks/workspace/storageEncryption/write | 在 Databricks 工作區的受控儲存體帳戶上啟用客戶管理的金鑰加密 |
> | 動作 | Databricks/workspace/storageEncryption/delete | 在 Databricks 工作區的受控儲存體帳戶上停用客戶管理的金鑰加密 |
> |  | **工作區/virtualNetworkPeerings** |  |
> | 動作 | Databricks/workspace/virtualNetworkPeerings/read | 取得虛擬網路對等互連。 |
> | 動作 | Databricks/workspace/virtualNetworkPeerings/write | 新增或修改虛擬網路對等互連 |
> | 動作 | Databricks/workspace/virtualNetworkPeerings/delete | 刪除虛擬網路對等互連 |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

Azure 服務：[資料目錄](../data-catalog/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.DataCatalog/register/action | 為資料目錄資源提供者註冊訂用帳戶 |
> | 動作 | Microsoft.DataCatalog/unregister/action | 取消註冊資料目錄資源提供者的訂用帳戶 |
> |  | **庫** |  |
> | 動作 | Microsoft.DataCatalog/catalogs/read | 讀取資料目錄資源提供者的目錄資源。 |
> | 動作 | Microsoft.DataCatalog/catalogs/write | 為資料目錄資源提供者撰寫目錄資源。 |
> | 動作 | Microsoft.DataCatalog/catalogs/delete | 刪除資料目錄資源提供者的目錄資源。 |
> |  | **checkNameAvailability** |  |
> | 動作 | Microsoft.datacatalog/checkNameAvailability/read | 檢查資料目錄資源提供者的目錄名稱可用性。 |
> |  | **datacatalogs** |  |
> | 動作 | Microsoft.datacatalog/datacatalogs/read | 讀取資料目錄資源提供者的 Microsoft.datacatalog 資源。 |
> | 動作 | Microsoft.datacatalog/datacatalogs/write | 寫入資料目錄資源提供者的 Microsoft.datacatalog 資源。 |
> | 動作 | Microsoft.datacatalog/datacatalogs/delete | 刪除資料目錄資源提供者的 Microsoft.datacatalog 資源。 |
> |  | **業務** |  |
> | 動作 | Microsoft.DataCatalog/operations/read | 讀取資料目錄資源提供者中的所有可用作業。 |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

Azure 服務： [Data Factory](../data-factory/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.DataFactory/register/action | 為 Data Factory 資源提供者註冊訂用帳戶。 |
> | 動作 | Microsoft.DataFactory/unregister/action | 為 Data Factory 資源提供者取消註冊訂用帳戶。 |
> |  | **checkazuredatafactorynameavailability** |  |
> | 動作 | Microsoft.DataFactory/checkazuredatafactorynameavailability/read | 檢查 Data Factory 名稱是否可供使用。 |
> |  | **microsoft.azure.management.datafactories** |  |
> | 動作 | Microsoft.DataFactory/datafactories/read | 讀取 Data Factory。 |
> | 動作 | Microsoft.DataFactory/datafactories/write | 建立或更新 Data Factory。 |
> | 動作 | Microsoft.DataFactory/datafactories/delete | 刪除 Data Factory。 |
> |  | **microsoft.azure.management.datafactories/activitywindows** |  |
> | 動作 | Microsoft.DataFactory/datafactories/activitywindows/read | 使用指定參數讀取 Data Factory 中的活動視窗。 |
> |  | **microsoft.azure.management.datafactories/datapipelines** |  |
> | 動作 | Microsoft.DataFactory/datafactories/datapipelines/read | 讀取任何管線。 |
> | 動作 | Microsoft.DataFactory/datafactories/datapipelines/delete | 刪除任何管線。 |
> | 動作 | Microsoft.DataFactory/datafactories/datapipelines/pause/action | 暫停任何管線。 |
> | 動作 | Microsoft.DataFactory/datafactories/datapipelines/resume/action | 繼續任何管線。 |
> | 動作 | Microsoft.DataFactory/datafactories/datapipelines/update/action | 更新任何管線。 |
> | 動作 | Microsoft.DataFactory/datafactories/datapipelines/write | 建立或更新任何管線。 |
> |  | **microsoft.azure.management.datafactories/datapipelines/activity/activitywindows** |  |
> | 動作 | Microsoft.DataFactory/datafactories/datapipelines/activities/activitywindows/read | 使用指定參數讀取管線活動的活動視窗。 |
> |  | **microsoft.azure.management.datafactories/datapipelines/activitywindows** |  |
> | 動作 | Microsoft.DataFactory/datafactories/datapipelines/activitywindows/read | 使用指定參數讀取管線的活動視窗。 |
> |  | **microsoft.azure.management.datafactories/資料集** |  |
> | 動作 | Microsoft.DataFactory/datafactories/datasets/read | 讀取任何資料集。 |
> | 動作 | Microsoft.DataFactory/datafactories/datasets/delete | 刪除任何資料集。 |
> | 動作 | Microsoft.DataFactory/datafactories/datasets/write | 建立或更新任何資料集。 |
> |  | **microsoft.azure.management.datafactories/資料集/activitywindows** |  |
> | 動作 | Microsoft.DataFactory/datafactories/datasets/activitywindows/read | 使用指定參數讀取資料集的活動視窗。 |
> |  | **microsoft.azure.management.datafactories/資料集/sliceruns** |  |
> | 動作 | Microsoft.DataFactory/datafactories/datasets/sliceruns/read | 針對具有指定開始時間的指定資料集讀取資料配量執行。 |
> |  | **microsoft.azure.management.datafactories/資料集/配量** |  |
> | 動作 | Microsoft.DataFactory/datafactories/datasets/slices/read | 取得指定期間內的資料配量。 |
> | 動作 | Microsoft.DataFactory/datafactories/datasets/slices/write | 更新資料配量的狀態。 |
> |  | **microsoft.azure.management.datafactories/閘道** |  |
> | 動作 | Microsoft.DataFactory/datafactories/gateways/read | 讀取任何閘道。 |
> | 動作 | Microsoft.DataFactory/datafactories/gateways/write | 建立或更新任何閘道。 |
> | 動作 | Microsoft.DataFactory/datafactories/gateways/delete | 刪除任何閘道。 |
> | 動作 | Microsoft.DataFactory/datafactories/gateways/connectioninfo/action | 讀取任何閘道的連線資訊。 |
> | 動作 | Microsoft.DataFactory/datafactories/gateways/listauthkeys/action | 列出任何閘道的驗證金鑰。 |
> | 動作 | Microsoft.DataFactory/datafactories/gateways/regenerateauthkey/action | 重新產生任何閘道的驗證金鑰。 |
> |  | **microsoft.azure.management.datafactories/Linkedservices.json 和 datasets.json** |  |
> | 動作 | Microsoft.DataFactory/datafactories/linkedServices/read | 讀取任何連結服務。 |
> | 動作 | Microsoft.DataFactory/datafactories/linkedServices/delete | 刪除任何連結服務。 |
> | 動作 | Microsoft.DataFactory/datafactories/linkedServices/write | 建立或更新任何連結服務。 |
> |  | **microsoft.azure.management.datafactories/執行/loginfo** |  |
> | 動作 | Microsoft.DataFactory/datafactories/runs/loginfo/read | 讀取包含記錄的 Blob 容器 SAS URI。 |
> |  | **microsoft.azure.management.datafactories/tables** |  |
> | 動作 | Microsoft.DataFactory/datafactories/tables/read | 讀取任何資料集。 |
> | 動作 | Microsoft.DataFactory/datafactories/tables/delete | 刪除任何資料集。 |
> | 動作 | Microsoft.DataFactory/datafactories/tables/write | 建立或更新任何資料集。 |
> |  | **處理站** |  |
> | 動作 | Microsoft.DataFactory/factories/read | 讀取 Data Factory。 |
> | 動作 | Microsoft.DataFactory/factories/write | 建立或更新 Data Factory |
> | 動作 | Microsoft.DataFactory/factories/delete | 刪除 Data Factory。 |
> | 動作 | DataFactory/factory/createdataflowdebugsession/action | 建立資料流程的「資料流程」（debug）會話。 |
> | 動作 | DataFactory/factory/startdataflowdebugsession/action | 啟動資料流程的「資料流程」（debug）會話。 |
> | 動作 | DataFactory/factory/addDataFlowToDebugSession/action | 將資料流程新增至用於預覽的 debug 會話。 |
> | 動作 | DataFactory/factory/executeDataFlowDebugCommand/action | 執行資料流程的調試命令。 |
> | 動作 | DataFactory/factory/deletedataflowdebugsession/action | 刪除資料流程的「資料流程」（debug）會話。 |
> | 動作 | DataFactory/factory/querydataflowdebugsessions/action | 查詢資料流程的「資料流程」（debug）會話。 |
> | 動作 | Microsoft.DataFactory/factories/cancelpipelinerun/action | 取消執行識別碼所指定的管線執行。 |
> | 動作 | DataFactory/factory/cancelSandboxPipelineRun/action | 取消管線的偵錯工具執行。 |
> | 動作 | DataFactory/factory/sandboxpipelineruns/action | 查詢偵錯管線執行。 |
> | 動作 | DataFactory/factory/querytriggers/action | 查詢觸發程式。 |
> | 動作 | DataFactory/factory/getFeatureValue/action | 取得特定位置的暴露風險控制特徵值。 |
> | 動作 | DataFactory/factory/getDataPlaneAccess/action | 取得 ADF DataPlane 服務的存取權。 |
> | 動作 | Microsoft.DataFactory/factories/getGitHubAccessToken/action | 取得 GitHub 存取權杖。 |
> | 動作 | Microsoft.DataFactory/factories/querytriggerruns/action | 查詢觸發程序執行。 |
> | 動作 | Microsoft.DataFactory/factories/querypipelineruns/action | 查詢管線執行。 |
> | 動作 | Microsoft.DataFactory/factories/querydebugpipelineruns/action | 查詢偵錯管線執行。 |
> |  | **factory/資料流程** |  |
> | 動作 | DataFactory/factory/資料流程/read | 讀取資料流程。 |
> | 動作 | DataFactory/factory/資料流程/delete | 刪除資料流程。 |
> | 動作 | DataFactory/factory/資料流程/write | 建立或更新資料流程 |
> |  | **factory/資料集** |  |
> | 動作 | Microsoft.DataFactory/factories/datasets/read | 讀取任何資料集。 |
> | 動作 | Microsoft.DataFactory/factories/datasets/delete | 刪除任何資料集。 |
> | 動作 | Microsoft.DataFactory/factories/datasets/write | 建立或更新任何資料集。 |
> |  | **factory/debugpipelineruns** |  |
> | 動作 | DataFactory/factory/debugpipelineruns/取消/動作 | 取消管線的偵錯工具執行。 |
> |  | **factory/getDataPlaneAccess** |  |
> | 動作 | DataFactory/factory/getDataPlaneAccess/read | 讀取 ADF DataPlane 服務的存取權。 |
> |  | **factory/getFeatureValue** |  |
> | 動作 | DataFactory/factory/getFeatureValue/read | 讀取特定位置的暴露風險控制特徵值。 |
> |  | **factory/integrationruntimes** |  |
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
> |  | **factory/integrationruntimes/getconnectioninfo** |  |
> | 動作 | Microsoft.DataFactory/factories/integrationruntimes/getconnectioninfo/read | 讀取 Integration Runtime 連線資訊。 |
> |  | **factory/integrationruntimes/getstatus** |  |
> | 動作 | Microsoft.DataFactory/factories/integrationruntimes/getstatus/read | 讀取 Integration Runtime 狀態。 |
> |  | **factory/integrationruntimes/listauthkeys** |  |
> | 動作 | Microsoft.DataFactory/factories/integrationruntimes/listauthkeys/read | 列出任何 Integration Runtime 的驗證金鑰。 |
> |  | **factory/integrationruntimes/monitoringdata** |  |
> | 動作 | Microsoft.DataFactory/factories/integrationruntimes/monitoringdata/read | 取得任何 Integration Runtime 的監視資料。 |
> |  | **factory/integrationruntimes/節點** |  |
> | 動作 | Microsoft.DataFactory/factories/integrationruntimes/nodes/read | 讀取所指定 Integration Runtime 的節點。 |
> | 動作 | Microsoft.DataFactory/factories/integrationruntimes/nodes/delete | 刪除所指定 Integration Runtime 的節點。 |
> | 動作 | Microsoft.DataFactory/factories/integrationruntimes/nodes/write | 更新自我裝載的 Integration Runtime 節點。 |
> | 動作 | Microsoft.DataFactory/factories/integrationruntimes/nodes/ipAddress/action | 傳回 Integration Runtime 所指定節點的 IP 位址。 |
> |  | **factory/Linkedservices.json 和 datasets.json** |  |
> | 動作 | Microsoft.DataFactory/factories/linkedServices/read | 讀取連結服務。 |
> | 動作 | Microsoft.DataFactory/factories/linkedServices/delete | 刪除連結服務。 |
> | 動作 | Microsoft.DataFactory/factories/linkedServices/write | 建立或更新連結服務 |
> |  | **factory/operationResults** |  |
> | 動作 | DataFactory/factory/operationResults/read | 取得作業結果。 |
> |  | **factory/pipelineruns** |  |
> | 動作 | Microsoft.DataFactory/factories/pipelineruns/read | 讀取管線執行。 |
> | 動作 | Microsoft.DataFactory/factories/pipelineruns/cancel/action | 取消執行識別碼所指定的管線執行。 |
> | 動作 | Microsoft.DataFactory/factories/pipelineruns/queryactivityruns/action | 查詢所指定管線執行識別碼的活動執行。 |
> |  | **factory/pipelineruns/activityruns** |  |
> | 動作 | Microsoft.DataFactory/factories/pipelineruns/activityruns/read | 讀取所指定管線執行識別碼的活動執行。 |
> |  | **factory/pipelineruns/queryactivityruns** |  |
> | 動作 | Microsoft.DataFactory/factories/pipelineruns/queryactivityruns/read | 讀取所指定管線執行識別碼的查詢活動執行結果。 |
> |  | **工廠/管線** |  |
> | 動作 | Microsoft.DataFactory/factories/pipelines/read | 讀取管線。 |
> | 動作 | Microsoft.DataFactory/factories/pipelines/delete | 刪除管線。 |
> | 動作 | Microsoft.DataFactory/factories/pipelines/write | 建立或更新管線 |
> | 動作 | Microsoft.DataFactory/factories/pipelines/createrun/action | 建立管線的執行。 |
> | 動作 | DataFactory/factory/管線/沙箱/動作 | 建立管線的「偵錯工具」執行環境。 |
> |  | **工廠/管線/pipelineruns** |  |
> | 動作 | Microsoft.DataFactory/factories/pipelines/pipelineruns/read | 讀取管線執行。 |
> |  | **factory/管線/pipelineruns/activityruns/進度** |  |
> | 動作 | Microsoft.DataFactory/factories/pipelines/pipelineruns/activityruns/progress/read | 取得活動執行的進度。 |
> |  | **工廠/管線/沙箱** |  |
> | 動作 | DataFactory/factory/管線/沙箱/建立/動作 | 建立管線的「偵錯工具」執行環境。 |
> | 動作 | DataFactory/factory/管線/沙箱/執行/動作 | 建立管線的偵錯工具執行。 |
> |  | **factory/querypipelineruns** |  |
> | 動作 | Microsoft.DataFactory/factories/querypipelineruns/read | 讀取查詢管線執行的結果。 |
> |  | **factory/querytriggerruns** |  |
> | 動作 | Microsoft.DataFactory/factories/querytriggerruns/read | 讀取觸發程序執行的結果。 |
> |  | **factory/sandboxpipelineruns** |  |
> | 動作 | DataFactory/factory/sandboxpipelineruns/read | 取得管線的偵錯工具執行資訊。 |
> |  | **factory/sandboxpipelineruns/sandboxActivityRuns** |  |
> | 動作 | DataFactory/factory/sandboxpipelineruns/sandboxActivityRuns/read | 取得活動的偵錯工具執行資訊。 |
> |  | **factory/triggerruns** |  |
> | 動作 | Microsoft.DataFactory/factories/triggerruns/read | 讀取觸發程序執行。 |
> |  | **factory/觸發程式** |  |
> | 動作 | Microsoft.DataFactory/factories/triggers/read | 讀取任何觸發程序。 |
> | 動作 | Microsoft.DataFactory/factories/triggers/write | 建立或更新任何觸發程序。 |
> | 動作 | Microsoft.DataFactory/factories/triggers/delete | 刪除任何觸發程序。 |
> | 動作 | DataFactory/factory/觸發程式/subscribetoevents/action | 訂閱事件。 |
> | 動作 | DataFactory/factory/觸發程式/geteventsubscriptionstatus/action | 事件訂閱狀態。 |
> | 動作 | DataFactory/factory/觸發程式/unsubscribefromevents/action | 取消訂閱事件。 |
> | 動作 | Microsoft.DataFactory/factories/triggers/start/action | 啟動任何觸發程序。 |
> | 動作 | Microsoft.DataFactory/factories/triggers/stop/action | 停止任何觸發程序。 |
> |  | **factory/觸發程式/triggerruns** |  |
> | 動作 | Microsoft.DataFactory/factories/triggers/triggerruns/read | 讀取觸發程序執行。 |
> |  | **堆放** |  |
> | 動作 | Microsoft.DataFactory/locations/configureFactoryRepo/action | 設定中心的存放庫。 |
> | 動作 | Microsoft.DataFactory/locations/getFeatureValue/action | 取得特定位置的暴露風險控制特徵值。 |
> |  | **位置/getFeatureValue** |  |
> | 動作 | Microsoft.DataFactory/locations/getFeatureValue/read | 讀取特定位置的暴露風險控制特徵值。 |
> |  | **業務** |  |
> | 動作 | Microsoft.DataFactory/operations/read | 讀取 Microsoft Data Factory 提供者中的所有作業。 |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

Azure 服務： [Data Lake Analytics](../data-lake-analytics/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.DataLakeAnalytics/register/action | 向 DataLakeAnalytics 註冊訂用帳戶。 |
> |  | **帳目** |  |
> | 動作 | Microsoft.DataLakeAnalytics/accounts/read | 取得現有 DataLakeAnalytics 帳戶的相關資訊。 |
> | 動作 | Microsoft.DataLakeAnalytics/accounts/write | 建立或更新 DataLakeAnalytics 帳戶。 |
> | 動作 | Microsoft.DataLakeAnalytics/accounts/delete | 刪除 DataLakeAnalytics 帳戶。 |
> | 動作 | Microsoft.DataLakeAnalytics/accounts/transferAnalyticsUnits/action | 在 DataLakeAnalytics 帳戶之間轉送 SystemMaxAnalyticsUnits。 |
> | 動作 | Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action | 授與權限以取消其他使用者所提交的作業。 |
> |  | **帳戶/computePolicies** |  |
> | 動作 | Microsoft.DataLakeAnalytics/accounts/computePolicies/read | 取得計算原則的相關資訊。 |
> | 動作 | Microsoft.DataLakeAnalytics/accounts/computePolicies/write | 建立或更新計算原則。 |
> | 動作 | Microsoft.DataLakeAnalytics/accounts/computePolicies/delete | 刪除計算原則。 |
> |  | **帳戶/dataLakeStoreAccounts** |  |
> | 動作 | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/read | 取得 DataLakeAnalytics 帳戶所連結之 DataLakeStore 帳戶的相關資訊。 |
> | 動作 | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/write | 建立或更新 DataLakeAnalytics 帳戶所連結的 DataLakeStore 帳戶。 |
> | 動作 | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/delete | 取消 DataLakeStore 帳戶與 DataLakeAnalytics 帳戶的連結。 |
> |  | **帳戶/firewallRules** |  |
> | 動作 | Microsoft.DataLakeAnalytics/accounts/firewallRules/read | 取得防火牆規則的相關資訊。 |
> | 動作 | Microsoft.DataLakeAnalytics/accounts/firewallRules/write | 建立或更新防火牆規則。 |
> | 動作 | Microsoft.DataLakeAnalytics/accounts/firewallRules/delete | 刪除防火牆規則。 |
> |  | **帳戶/operationResults** |  |
> | 動作 | Microsoft.DataLakeAnalytics/accounts/operationResults/read | 取得 DataLakeAnalytics 帳戶作業的結果。 |
> |  | **帳戶/storageAccounts** |  |
> | 動作 | Microsoft.DataLakeAnalytics/accounts/storageAccounts/read | 取得 DataLakeAnalytics 帳戶所連結之儲存體帳戶的相關資訊。 |
> | 動作 | Microsoft.DataLakeAnalytics/accounts/storageAccounts/write | 建立或更新 DataLakeAnalytics 帳戶所連結的儲存體帳戶。 |
> | 動作 | Microsoft.DataLakeAnalytics/accounts/storageAccounts/delete | 取消儲存體帳戶與 DataLakeAnalytics 帳戶的連結。 |
> |  | **帳戶/storageAccounts/容器** |  |
> | 動作 | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers/read | 取得 DataLakeAnalytics 帳戶所連結之儲存體帳戶的容器。 |
> | 動作 | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers/listSasTokens/action | 針對 DataLakeAnalytics 帳戶所連結之儲存體帳戶的儲存體容器列出 SAS 權杖。 |
> |  | **帳戶/virtualNetworkRules** |  |
> | 動作 | DataLakeAnalytics/accounts/virtualNetworkRules/read | 取得虛擬網路規則的相關資訊。 |
> | 動作 | DataLakeAnalytics/accounts/virtualNetworkRules/write | 建立或更新虛擬網路規則。 |
> | 動作 | DataLakeAnalytics/accounts/virtualNetworkRules/delete | 刪除虛擬網路規則。 |
> |  | **堆放** |  |
> | 動作 | Microsoft.DataLakeAnalytics/locations/checkNameAvailability/action | 檢查 DataLakeAnalytics 帳戶名稱的可用性。 |
> |  | **位置/功能** |  |
> | 動作 | Microsoft.DataLakeAnalytics/locations/capability/read | 取得訂用帳戶中關於使用 DataLakeAnalytics 的功能資訊。 |
> |  | **位置/operationResults** |  |
> | 動作 | Microsoft.DataLakeAnalytics/locations/operationResults/read | 取得 DataLakeAnalytics 帳戶作業的結果。 |
> |  | **位置/使用方式** |  |
> | 動作 | Microsoft.DataLakeAnalytics/locations/usages/read | 取得訂用帳戶中關於使用 DataLakeAnalytics 的配額使用量資訊。 |
> |  | **業務** |  |
> | 動作 | Microsoft.DataLakeAnalytics/operations/read | 取得 DataLakeAnalytics 的可用作業。 |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

Azure 服務： [Azure Data Lake 存放區](../storage/blobs/data-lake-storage-introduction.md)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.DataLakeStore/register/action | 向 DataLakeStore 註冊訂用帳戶。 |
> |  | **帳目** |  |
> | 動作 | Microsoft.DataLakeStore/accounts/read | 取得現有 DataLakeStore 帳戶的相關資訊。 |
> | 動作 | Microsoft.DataLakeStore/accounts/write | 建立或更新 DataLakeStore 帳戶。 |
> | 動作 | Microsoft.DataLakeStore/accounts/delete | 刪除 DataLakeStore 帳戶。 |
> | 動作 | Microsoft.DataLakeStore/accounts/enableKeyVault/action | 針對 DataLakeStore 帳戶啟用 KeyVault。 |
> | 動作 | Microsoft.DataLakeStore/accounts/Superuser/action | 使用 Microsoft.Authorization/roleAssignments/write 授與時，授與 Data Lake Store 的超級使用者。 |
> |  | **帳戶/eventGridFilters** |  |
> | 動作 | Microsoft.DataLakeStore/accounts/eventGridFilters/read | 取得 EventGrid 篩選。 |
> | 動作 | Microsoft.DataLakeStore/accounts/eventGridFilters/write | 建立或更新 EventGrid 篩選。 |
> | 動作 | Microsoft.DataLakeStore/accounts/eventGridFilters/delete | 刪除 EventGrid 篩選。 |
> |  | **帳戶/firewallRules** |  |
> | 動作 | Microsoft.DataLakeStore/accounts/firewallRules/read | 取得防火牆規則的相關資訊。 |
> | 動作 | Microsoft.DataLakeStore/accounts/firewallRules/write | 建立或更新防火牆規則。 |
> | 動作 | Microsoft.DataLakeStore/accounts/firewallRules/delete | 刪除防火牆規則。 |
> |  | **帳戶/operationResults** |  |
> | 動作 | Microsoft.DataLakeStore/accounts/operationResults/read | 取得 DataLakeStore 帳戶作業的結果。 |
> |  | **帳戶/共用** |  |
> | 動作 | DataLakeStore/accounts/共用/讀取 | 取得共用的相關資訊。 |
> | 動作 | DataLakeStore/accounts/共用/寫入 | 建立或更新共用。 |
> | 動作 | DataLakeStore/accounts/共用/刪除 | 刪除共用。 |
> |  | **帳戶/trustedIdProviders** |  |
> | 動作 | Microsoft.DataLakeStore/accounts/trustedIdProviders/read | 取得所信任識別提供者的相關資訊。 |
> | 動作 | Microsoft.DataLakeStore/accounts/trustedIdProviders/write | 建立或更新所信任的識別提供者。 |
> | 動作 | Microsoft.DataLakeStore/accounts/trustedIdProviders/delete | 刪除所信任的識別提供者。 |
> |  | **帳戶/virtualNetworkRules** |  |
> | 動作 | DataLakeStore/accounts/virtualNetworkRules/read | 取得虛擬網路規則的相關資訊。 |
> | 動作 | DataLakeStore/accounts/virtualNetworkRules/write | 建立或更新虛擬網路規則。 |
> | 動作 | DataLakeStore/accounts/virtualNetworkRules/delete | 刪除虛擬網路規則。 |
> |  | **堆放** |  |
> | 動作 | Microsoft.DataLakeStore/locations/checkNameAvailability/action | 檢查 DataLakeStore 帳戶名稱的可用性。 |
> |  | **位置/功能** |  |
> | 動作 | Microsoft.DataLakeStore/locations/capability/read | 取得訂用帳戶中關於使用 DataLakeStore 的功能資訊。 |
> |  | **位置/operationResults** |  |
> | 動作 | Microsoft.DataLakeStore/locations/operationResults/read | 取得 DataLakeStore 帳戶作業的結果。 |
> |  | **位置/使用方式** |  |
> | 動作 | Microsoft.DataLakeStore/locations/usages/read | 取得訂用帳戶中關於使用 DataLakeStore 的配額使用量資訊。 |
> |  | **業務** |  |
> | 動作 | Microsoft.DataLakeStore/operations/read | 取得 DataLakeStore 的可用作業。 |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

Azure 服務： [Azure 資料庫移轉服務](../dms/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.DataMigration/register/action | 向「Azure 資料庫移轉服務」提供者註冊訂用帳戶 |
> |  | **位置/operationResults** |  |
> | 動作 | Microsoft.DataMigration/locations/operationResults/read | 取得與「202 已接受」回應相關且長時間執行之作業的狀態 |
> |  | **位置/operationStatuses** |  |
> | 動作 | Microsoft.DataMigration/locations/operationStatuses/read | 取得與「202 已接受」回應相關且長時間執行之作業的狀態 |
> |  | **services** |  |
> | 動作 | Microsoft.DataMigration/services/read | 閱讀資源的相關資訊 |
> | 動作 | Microsoft.DataMigration/services/write | 建立或更新資源及其屬性 |
> | 動作 | Microsoft.DataMigration/services/delete | 刪除資源及其所有子系 |
> | 動作 | Microsoft.DataMigration/services/stop/action | 停止 DMS 服務以將其成本降到最低 |
> | 動作 | Microsoft.DataMigration/services/start/action | 啟動 DMS 服務以讓它再次處理移轉 |
> | 動作 | Microsoft.DataMigration/services/checkStatus/action | 檢查服務是否已部署且在執行中 |
> | 動作 | Microsoft.datamigration/services/configureWorker/action | 將 DMS 背景工作角色設定為服務的可用背景工作 |
> | 動作 | Microsoft.datamigration/services/addWorker/action | 將 DMS 背景工作角色新增至服務的可用背景工作角色 |
> | 動作 | Microsoft.datamigration/services/removeWorker/action | 將 DMS 背景工作角色移除至服務的可用背景工作 |
> | 動作 | Microsoft.datamigration/services/updateAgentConfig/action | 以提供的值更新 DMS 代理程式設定。 |
> | 動作 | Microsoft.datamigration/services/getHybridDownloadLink/action | 從 RP Blob 儲存體取得 DMS 背景工作角色套件下載連結。 |
> |  | **服務/專案** |  |
> | 動作 | Microsoft.DataMigration/services/projects/read | 閱讀資源的相關資訊 |
> | 動作 | Microsoft.DataMigration/services/projects/write | 執行工作「Azure 資料庫移轉服務」工作 |
> | 動作 | Microsoft.DataMigration/services/projects/delete | 刪除資源及其所有子系 |
> | 動作 | Microsoft.DataMigration/services/projects/accessArtifacts/action | 產生可用來對專案成品進行 GET 或 PUT 的 URL |
> |  | **服務/專案/檔案** |  |
> | 動作 | Microsoft.DataMigration/services/projects/files/read | 閱讀資源的相關資訊 |
> | 動作 | Microsoft.DataMigration/services/projects/files/write | 建立或更新資源及其屬性 |
> | 動作 | Microsoft.DataMigration/services/projects/files/delete | 刪除資源及其所有子系 |
> | 動作 | Microsoft.DataMigration/services/projects/files/read/action | 取得可用來讀取檔案內容的 URL |
> | 動作 | Microsoft.DataMigration/services/projects/files/readWrite/action | 取得可用來讀取或寫入檔案內容的 URL |
> |  | **服務/專案/工作** |  |
> | 動作 | Microsoft.DataMigration/services/projects/tasks/read | 閱讀資源的相關資訊 |
> | 動作 | Microsoft.DataMigration/services/projects/tasks/write | 執行工作「Azure 資料庫移轉服務」工作 |
> | 動作 | Microsoft.DataMigration/services/projects/tasks/delete | 刪除資源及其所有子系 |
> | 動作 | Microsoft.DataMigration/services/projects/tasks/cancel/action | 取消目前正在執行的工作 |
> |  | **服務/serviceTasks** |  |
> | 動作 | Microsoft.datamigration/services/serviceTasks/read | 閱讀資源的相關資訊 |
> | 動作 | Microsoft.datamigration/services/serviceTasks/write | 執行工作「Azure 資料庫移轉服務」工作 |
> | 動作 | Microsoft.datamigration/services/serviceTasks/delete | 刪除資源及其所有子系 |
> | 動作 | Microsoft.datamigration/services/serviceTasks/取消/動作 | 取消目前正在執行的工作 |
> |  | **服務/位置** |  |
> | 動作 | Microsoft.DataMigration/services/slots/read | 閱讀資源的相關資訊 |
> | 動作 | Microsoft.DataMigration/services/slots/write | 建立或更新資源及其屬性 |
> | 動作 | Microsoft.DataMigration/services/slots/delete | 刪除資源及其所有子系 |
> |  | **sku** |  |
> | 動作 | Microsoft.DataMigration/skus/read | 取得 DMS 資源所支援的 SKU 清單。 |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

Azure 服務：[適用於 MariaDB 的 Azure 資料庫](../mariadb/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | DBforMariaDB/register/action | 註冊適用于 mariadb 資源提供者 |
> | 動作 | DBforMariaDB/checkNameAvailability/action | 確認指定的伺服器名稱是否可用來針對指定的訂用帳戶進行全球佈建。 |
> |  | **位置/azureAsyncOperation** |  |
> | 動作 | DBforMariaDB/位置/azureAsyncOperation/讀取 | 傳回適用于 mariadb 伺服器操作結果 |
> |  | **位置/operationResults** |  |
> | 動作 | DBforMariaDB/位置/operationResults/讀取 | 傳回以 ResourceGroup 為基礎的適用于 mariadb 伺服器作業結果 |
> | 動作 | DBforMariaDB/位置/operationResults/讀取 | 傳回適用于 mariadb 伺服器操作結果 |
> |  | **位置/performanceTiers** |  |
> | 動作 | Microsoft.DBforMariaDB/locations/performanceTiers/read | 傳回可用的效能層級清單。 |
> |  | **位置/privateEndpointConnectionAzureAsyncOperation** |  |
> | 動作 | DBforMariaDB/位置/privateEndpointConnectionAzureAsyncOperation/讀取 | 取得私用端點連接作業的結果 |
> |  | **位置/privateEndpointConnectionOperationResults** |  |
> | 動作 | DBforMariaDB/位置/privateEndpointConnectionOperationResults/讀取 | 取得私用端點連接作業的結果 |
> |  | **位置/privateEndpointConnectionProxyAzureAsyncOperation** |  |
> | 動作 | DBforMariaDB/位置/privateEndpointConnectionProxyAzureAsyncOperation/讀取 | 取得私用端點連接 proxy 作業的結果 |
> |  | **位置/privateEndpointConnectionProxyOperationResults** |  |
> | 動作 | DBforMariaDB/位置/privateEndpointConnectionProxyOperationResults/讀取 | 取得私用端點連接 proxy 作業的結果 |
> |  | **位置/securityAlertPoliciesAzureAsyncOperation** |  |
> | 動作 | DBforMariaDB/位置/securityAlertPoliciesAzureAsyncOperation/讀取 | 傳回伺服器威脅偵測操作結果的清單。 |
> |  | **位置/securityAlertPoliciesOperationResults** |  |
> | 動作 | DBforMariaDB/位置/securityAlertPoliciesOperationResults/讀取 | 傳回伺服器威脅偵測操作結果的清單。 |
> |  | **位置/serverKeyAzureAsyncOperation** |  |
> | 動作 | DBforMariaDB/位置/serverKeyAzureAsyncOperation/讀取 | 取得透明資料加密伺服器金鑰的進行中作業 |
> |  | **位置/serverKeyOperationResults** |  |
> | 動作 | DBforMariaDB/位置/serverKeyOperationResults/讀取 | 取得透明資料加密伺服器金鑰的進行中作業 |
> |  | **業務** |  |
> | 動作 | DBforMariaDB/作業/讀取 | 傳回適用于 mariadb 作業的清單。 |
> |  | **performanceTiers** |  |
> | 動作 | Microsoft.DBforMariaDB/performanceTiers/read | 傳回可用的效能層級清單。 |
> |  | **伺服器** |  |
> | 動作 | DBforMariaDB/servers/queryTexts/action | 傳回查詢清單的文字 |
> | 動作 | DBforMariaDB/servers/queryTexts/action | 傳回查詢的文字 |
> | 動作 | Microsoft.DBforMariaDB/servers/read | 傳回伺服器清單，或取得指定伺服器的屬性。 |
> | 動作 | Microsoft.DBforMariaDB/servers/write | 使用指定參數建立伺服器，或更新指定伺服器的屬性或標記。 |
> | 動作 | Microsoft.DBforMariaDB/servers/delete | 刪除現有伺服器。 |
> | 動作 | DBforMariaDB/伺服器/重新開機/動作 | 重新開機特定的伺服器。 |
> | 動作 | Microsoft.DBforMariaDB/servers/updateConfigurations/action | 更新指定伺服器的組態 |
> |  | **伺服器/系統管理員** |  |
> | 動作 | DBforMariaDB/伺服器/系統管理員/讀取 | 取得適用于 mariadb 伺服器系統管理員的清單。 |
> | 動作 | DBforMariaDB/servers/administrator/write | 使用指定的參數建立或更新適用于 mariadb 伺服器系統管理員。 |
> | 動作 | DBforMariaDB/伺服器/系統管理員/刪除 | 刪除適用于 mariadb 伺服器的現有系統管理員。 |
> |  | **伺服器/顧問** |  |
> | 動作 | DBforMariaDB/servers/顧問/read | 傳回建議程式清單 |
> | 動作 | DBforMariaDB/servers/顧問/read | 傳回 advisor |
> | 動作 | DBforMariaDB/servers/顧問/createRecommendedActionSession/action | 建立新的建議動作會話 |
> |  | **伺服器/顧問/recommendedActions** |  |
> | 動作 | DBforMariaDB/servers/顧問/recommendedActions/read | 傳回建議的動作清單 |
> | 動作 | DBforMariaDB/servers/顧問/recommendedActions/read | 傳回建議的動作清單 |
> | 動作 | DBforMariaDB/servers/顧問/recommendedActions/read | 傳回建議的動作 |
> |  | **伺服器/設定** |  |
> | 動作 | Microsoft.DBforMariaDB/servers/configurations/read | 傳回伺服器的組態清單，或取得指定組態的屬性。 |
> | 動作 | Microsoft.DBforMariaDB/servers/configurations/write | 更新指定組態的值 |
> |  | **伺服器/資料庫** |  |
> | 動作 | DBforMariaDB/servers/資料庫/讀取 | 傳回適用于 mariadb 資料庫的清單，或取得指定之資料庫的屬性。 |
> | 動作 | DBforMariaDB/servers/資料庫/寫入 | 使用指定的參數建立適用于 mariadb 資料庫，或更新指定之資料庫的屬性。 |
> | 動作 | DBforMariaDB/伺服器/資料庫/刪除 | 刪除現有的適用于 mariadb 資料庫。 |
> |  | **伺服器/firewallRules** |  |
> | 動作 | Microsoft.DBforMariaDB/servers/firewallRules/read | 傳回伺服器的防火牆規則清單，或取得指定防火牆規則的屬性。 |
> | 動作 | Microsoft.DBforMariaDB/servers/firewallRules/write | 使用指定參數建立防火牆規則，或更新現有的規則。 |
> | 動作 | Microsoft.DBforMariaDB/servers/firewallRules/delete | 刪除現有防火牆規則。 |
> |  | **伺服器/金鑰** |  |
> | 動作 | DBforMariaDB/servers/keys/read | 傳回伺服器金鑰的清單，或取得指定伺服器金鑰的屬性。 |
> | 動作 | DBforMariaDB/servers/keys/write | 使用指定參數建立金鑰，或更新指定伺服器金鑰的屬性或標記。 |
> | 動作 | DBforMariaDB/servers/keys/delete | 刪除現有的伺服器金鑰。 |
> |  | **伺服器/記錄檔** |  |
> | 動作 | DBforMariaDB/servers/記錄檔/讀取 | 傳回適用于 mariadb 記錄檔的清單。 |
> |  | **伺服器/privateEndpointConnectionProxies** |  |
> | 動作 | DBforMariaDB/servers/privateEndpointConnectionProxies/validate/action | 驗證從 NRP 端的私用端點連接建立呼叫 |
> | 動作 | DBforMariaDB/servers/privateEndpointConnectionProxies/read | 傳回私用端點連接 proxy 的清單，或取得指定之私用端點連接 proxy 的屬性。 |
> | 動作 | DBforMariaDB/servers/privateEndpointConnectionProxies/write | 使用指定的參數建立私用端點連接 proxy，或更新指定之私用端點連接 proxy 的屬性或標記。 |
> | 動作 | DBforMariaDB/servers/privateEndpointConnectionProxies/delete | 刪除現有的私用端點連接 proxy |
> |  | **伺服器/privateEndpointConnections** |  |
> | 動作 | DBforMariaDB/servers/privateEndpointConnections/read | 傳回私用端點連接的清單，或取得指定之私用端點連接的屬性。 |
> | 動作 | DBforMariaDB/servers/privateEndpointConnections/delete | 刪除現有的私用端點連接 |
> | 動作 | DBforMariaDB/servers/privateEndpointConnections/write | 核准或拒絕現有的私用端點連接 |
> |  | **伺服器/privateLinkResources** |  |
> | 動作 | DBforMariaDB/servers/privateLinkResources/read | 取得對應適用于 mariadb 伺服器的私人連結資源 |
> |  | **伺服器/提供者/Microsoft Insights/diagnosticSettings** |  |
> | 動作 | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/diagnosticSettings/read | 取得資源的診斷設定 |
> | 動作 | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/diagnosticSettings/write | 建立或更新資源的診斷設定 |
> |  | **伺服器/提供者/Microsoft Insights/logDefinitions** |  |
> | 動作 | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/logDefinitions/read | 取得 MariaDB 伺服器的可用記錄 |
> |  | **伺服器/提供者/Microsoft Insights/Metricdefinitions.listasync** |  |
> | 動作 | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/metricDefinitions/read | 傳回可供資料庫使用之計量的類型 |
> |  | **伺服器/recoverableServers** |  |
> | 動作 | Microsoft.DBforMariaDB/servers/recoverableServers/read | 傳回可復原的 MariaDB 伺服器資訊 |
> |  | **伺服器/複本** |  |
> | 動作 | DBforMariaDB/servers/複本/讀取 | 取得適用于 mariadb 伺服器的讀取複本 |
> |  | **伺服器/securityAlertPolicies** |  |
> | 動作 | Microsoft.DBforMariaDB/servers/securityAlertPolicies/read | 擷取給定伺服器上所設定的伺服器威脅偵測原則詳細資料 |
> | 動作 | Microsoft.DBforMariaDB/servers/securityAlertPolicies/write | 變更指定伺服器的伺服器威脅偵測原則 |
> |  | **伺服器/topQueryStatistics** |  |
> | 動作 | DBforMariaDB/servers/topQueryStatistics/read | 傳回最高排名查詢的查詢統計資料清單。 |
> | 動作 | DBforMariaDB/servers/topQueryStatistics/read | 傳回查詢統計資料 |
> |  | **伺服器/virtualNetworkRules** |  |
> | 動作 | Microsoft.DBforMariaDB/servers/virtualNetworkRules/read | 傳回虛擬網路規則的清單，或取得指定虛擬網路規則的屬性。 |
> | 動作 | Microsoft.DBforMariaDB/servers/virtualNetworkRules/write | 使用指定參數建立虛擬網路規則，或更新指定虛擬網路規則的屬性或標記。 |
> | 動作 | Microsoft.DBforMariaDB/servers/virtualNetworkRules/delete | 刪除現有虛擬網路規則 |
> |  | **伺服器/waitStatistics** |  |
> | 動作 | DBforMariaDB/servers/waitStatistics/read | 傳回執行個體的等候統計資料 |
> | 動作 | DBforMariaDB/servers/waitStatistics/read | 傳回等候統計資料 |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

Azure 服務：[適用於 MySQL 的 Azure 資料庫](../mysql/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.dbformysql/register/action | 註冊 MySQL 資源提供者 |
> | 動作 | Microsoft.dbformysql/checkNameAvailability/action | 確認指定的伺服器名稱是否可用來針對指定的訂用帳戶進行全球佈建。 |
> |  | **位置/azureAsyncOperation** |  |
> | 動作 | Microsoft.dbformysql/位置/azureAsyncOperation/讀取 | 傳回 MySQL 伺服器操作結果 |
> |  | **位置/operationResults** |  |
> | 動作 | Microsoft.dbformysql/位置/operationResults/讀取 | 傳回以 ResourceGroup 為基礎的 MySQL 伺服器操作結果 |
> | 動作 | Microsoft.dbformysql/位置/operationResults/讀取 | 傳回 MySQL 伺服器操作結果 |
> |  | **位置/performanceTiers** |  |
> | 動作 | Microsoft.DBforMySQL/locations/performanceTiers/read | 傳回可用的效能層級清單。 |
> |  | **位置/privateEndpointConnectionAzureAsyncOperation** |  |
> | 動作 | Microsoft.dbformysql/位置/privateEndpointConnectionAzureAsyncOperation/讀取 | 取得私用端點連接作業的結果 |
> |  | **位置/privateEndpointConnectionOperationResults** |  |
> | 動作 | Microsoft.dbformysql/位置/privateEndpointConnectionOperationResults/讀取 | 取得私用端點連接作業的結果 |
> |  | **位置/privateEndpointConnectionProxyAzureAsyncOperation** |  |
> | 動作 | Microsoft.dbformysql/位置/privateEndpointConnectionProxyAzureAsyncOperation/讀取 | 取得私用端點連接 proxy 作業的結果 |
> |  | **位置/privateEndpointConnectionProxyOperationResults** |  |
> | 動作 | Microsoft.dbformysql/位置/privateEndpointConnectionProxyOperationResults/讀取 | 取得私用端點連接 proxy 作業的結果 |
> |  | **位置/securityAlertPoliciesAzureAsyncOperation** |  |
> | 動作 | Microsoft.dbformysql/位置/securityAlertPoliciesAzureAsyncOperation/讀取 | 傳回伺服器威脅偵測操作結果的清單。 |
> |  | **位置/securityAlertPoliciesOperationResults** |  |
> | 動作 | Microsoft.dbformysql/位置/securityAlertPoliciesOperationResults/讀取 | 傳回伺服器威脅偵測操作結果的清單。 |
> |  | **位置/serverKeyAzureAsyncOperation** |  |
> | 動作 | Microsoft.dbformysql/位置/serverKeyAzureAsyncOperation/讀取 | 取得透明資料加密伺服器金鑰的進行中作業 |
> |  | **位置/serverKeyOperationResults** |  |
> | 動作 | Microsoft.dbformysql/位置/serverKeyOperationResults/讀取 | 取得透明資料加密伺服器金鑰的進行中作業 |
> |  | **業務** |  |
> | 動作 | Microsoft.dbformysql/作業/讀取 | 傳回 MySQL 作業的清單。 |
> |  | **performanceTiers** |  |
> | 動作 | Microsoft.DBforMySQL/performanceTiers/read | 傳回可用的效能層級清單。 |
> |  | **伺服器** |  |
> | 動作 | Microsoft.dbformysql/servers/queryTexts/action | 傳回查詢清單的文字 |
> | 動作 | Microsoft.dbformysql/servers/queryTexts/action | 傳回查詢的文字 |
> | 動作 | Microsoft.DBforMySQL/servers/read | 傳回伺服器清單，或取得指定伺服器的屬性。 |
> | 動作 | Microsoft.DBforMySQL/servers/write | 使用指定參數建立伺服器，或更新指定伺服器的屬性或標記。 |
> | 動作 | Microsoft.DBforMySQL/servers/delete | 刪除現有伺服器。 |
> | 動作 | Microsoft.dbformysql/伺服器/重新開機/動作 | 重新開機特定的伺服器。 |
> | 動作 | Microsoft.DBforMySQL/servers/updateConfigurations/action | 更新指定伺服器的組態 |
> |  | **伺服器/系統管理員** |  |
> | 動作 | Microsoft.dbformysql/伺服器/系統管理員/讀取 | 取得 MySQL server 系統管理員的清單。 |
> | 動作 | Microsoft.dbformysql/servers/administrator/write | 使用指定的參數建立或更新 MySQL server 系統管理員。 |
> | 動作 | Microsoft.dbformysql/伺服器/系統管理員/刪除 | 刪除現有的 MySQL server 系統管理員。 |
> |  | **伺服器/顧問** |  |
> | 動作 | Microsoft.dbformysql/servers/顧問/read | 傳回建議程式清單 |
> | 動作 | Microsoft.dbformysql/servers/顧問/read | 傳回 advisor |
> | 動作 | Microsoft.dbformysql/servers/顧問/createRecommendedActionSession/action | 建立新的建議動作會話 |
> |  | **伺服器/顧問/recommendedActions** |  |
> | 動作 | Microsoft.dbformysql/servers/顧問/recommendedActions/read | 傳回建議的動作清單 |
> | 動作 | Microsoft.dbformysql/servers/顧問/recommendedActions/read | 傳回建議的動作清單 |
> | 動作 | Microsoft.dbformysql/servers/顧問/recommendedActions/read | 傳回建議的動作 |
> |  | **伺服器/設定** |  |
> | 動作 | Microsoft.DBforMySQL/servers/configurations/read | 傳回伺服器的組態清單，或取得指定組態的屬性。 |
> | 動作 | Microsoft.DBforMySQL/servers/configurations/write | 更新指定組態的值 |
> |  | **伺服器/資料庫** |  |
> | 動作 | Microsoft.dbformysql/servers/資料庫/讀取 | 傳回 MySQL 資料庫的清單，或取得指定之資料庫的屬性。 |
> | 動作 | Microsoft.dbformysql/servers/資料庫/寫入 | 使用指定的參數建立 MySQL 資料庫，或更新指定之資料庫的屬性。 |
> | 動作 | Microsoft.dbformysql/伺服器/資料庫/刪除 | 刪除現有的 MySQL 資料庫。 |
> |  | **伺服器/firewallRules** |  |
> | 動作 | Microsoft.DBforMySQL/servers/firewallRules/read | 傳回伺服器的防火牆規則清單，或取得指定防火牆規則的屬性。 |
> | 動作 | Microsoft.DBforMySQL/servers/firewallRules/write | 使用指定參數建立防火牆規則，或更新現有的規則。 |
> | 動作 | Microsoft.DBforMySQL/servers/firewallRules/delete | 刪除現有防火牆規則。 |
> |  | **伺服器/金鑰** |  |
> | 動作 | Microsoft.dbformysql/servers/keys/read | 傳回伺服器金鑰的清單，或取得指定伺服器金鑰的屬性。 |
> | 動作 | Microsoft.dbformysql/servers/keys/write | 使用指定參數建立金鑰，或更新指定伺服器金鑰的屬性或標記。 |
> | 動作 | Microsoft.dbformysql/servers/keys/delete | 刪除現有的伺服器金鑰。 |
> |  | **伺服器/記錄檔** |  |
> | 動作 | Microsoft.dbformysql/servers/記錄檔/讀取 | 傳回 MySQL 記錄檔的清單。 |
> |  | **伺服器/privateEndpointConnectionProxies** |  |
> | 動作 | Microsoft.dbformysql/servers/privateEndpointConnectionProxies/validate/action | 驗證從 NRP 端的私用端點連接建立呼叫 |
> | 動作 | Microsoft.dbformysql/servers/privateEndpointConnectionProxies/read | 傳回私用端點連接 proxy 的清單，或取得指定之私用端點連接 proxy 的屬性。 |
> | 動作 | Microsoft.dbformysql/servers/privateEndpointConnectionProxies/write | 使用指定的參數建立私用端點連接 proxy，或更新指定之私用端點連接 proxy 的屬性或標記。 |
> | 動作 | Microsoft.dbformysql/servers/privateEndpointConnectionProxies/delete | 刪除現有的私用端點連接 proxy |
> |  | **伺服器/privateEndpointConnections** |  |
> | 動作 | Microsoft.dbformysql/servers/privateEndpointConnections/read | 傳回私用端點連接的清單，或取得指定之私用端點連接的屬性。 |
> | 動作 | Microsoft.dbformysql/servers/privateEndpointConnections/delete | 刪除現有的私用端點連接 |
> | 動作 | Microsoft.dbformysql/servers/privateEndpointConnections/write | 核准或拒絕現有的私用端點連接 |
> |  | **伺服器/privateLinkResources** |  |
> | 動作 | Microsoft.dbformysql/servers/privateLinkResources/read | 取得對應 MySQL 伺服器的私人連結資源 |
> |  | **伺服器/提供者/Microsoft Insights/diagnosticSettings** |  |
> | 動作 | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/diagnosticSettings/read | 取得資源的診斷設定 |
> | 動作 | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/diagnosticSettings/write | 建立或更新資源的診斷設定 |
> |  | **伺服器/提供者/Microsoft Insights/logDefinitions** |  |
> | 動作 | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/logDefinitions/read | 取得 MySQL 伺服器的可用記錄 |
> |  | **伺服器/提供者/Microsoft Insights/Metricdefinitions.listasync** |  |
> | 動作 | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/metricDefinitions/read | 傳回可供資料庫使用之計量的類型 |
> |  | **伺服器/recoverableServers** |  |
> | 動作 | Microsoft.DBforMySQL/servers/recoverableServers/read | 傳回可復原的 MySQL 伺服器資訊 |
> |  | **伺服器/複本** |  |
> | 動作 | Microsoft.dbformysql/servers/複本/讀取 | 取得 MySQL 伺服器的讀取複本 |
> |  | **伺服器/securityAlertPolicies** |  |
> | 動作 | Microsoft.DBforMySQL/servers/securityAlertPolicies/read | 擷取給定伺服器上所設定的伺服器威脅偵測原則詳細資料 |
> | 動作 | Microsoft.DBforMySQL/servers/securityAlertPolicies/write | 變更指定伺服器的伺服器威脅偵測原則 |
> |  | **伺服器/topQueryStatistics** |  |
> | 動作 | Microsoft.dbformysql/servers/topQueryStatistics/read | 傳回最高排名查詢的查詢統計資料清單。 |
> | 動作 | Microsoft.dbformysql/servers/topQueryStatistics/read | 傳回查詢統計資料 |
> |  | **伺服器/virtualNetworkRules** |  |
> | 動作 | Microsoft.DBforMySQL/servers/virtualNetworkRules/read | 傳回虛擬網路規則的清單，或取得指定虛擬網路規則的屬性。 |
> | 動作 | Microsoft.DBforMySQL/servers/virtualNetworkRules/write | 使用指定參數建立虛擬網路規則，或更新指定虛擬網路規則的屬性或標記。 |
> | 動作 | Microsoft.DBforMySQL/servers/virtualNetworkRules/delete | 刪除現有虛擬網路規則 |
> |  | **伺服器/waitStatistics** |  |
> | 動作 | Microsoft.dbformysql/servers/waitStatistics/read | 傳回執行個體的等候統計資料 |
> | 動作 | Microsoft.dbformysql/servers/waitStatistics/read | 傳回等候統計資料 |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

Azure 服務：[適用於 PostgreSQL 的 Azure 資料庫](../postgresql/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | DBforPostgreSQL/register/action | 註冊于 postgresql 資源提供者 |
> | 動作 | DBforPostgreSQL/checkNameAvailability/action | 確認指定的伺服器名稱是否可用來針對指定的訂用帳戶進行全球佈建。 |
> |  | **位置/azureAsyncOperation** |  |
> | 動作 | DBforPostgreSQL/位置/azureAsyncOperation/讀取 | 傳回于 postgresql 伺服器操作結果 |
> |  | **位置/operationResults** |  |
> | 動作 | DBforPostgreSQL/位置/operationResults/讀取 | 傳回以 ResourceGroup 為基礎的于 postgresql 伺服器作業結果 |
> | 動作 | DBforPostgreSQL/位置/operationResults/讀取 | 傳回于 postgresql 伺服器操作結果 |
> |  | **位置/performanceTiers** |  |
> | 動作 | Microsoft.DBforPostgreSQL/locations/performanceTiers/read | 傳回可用的效能層級清單。 |
> |  | **位置/privateEndpointConnectionAzureAsyncOperation** |  |
> | 動作 | DBforPostgreSQL/位置/privateEndpointConnectionAzureAsyncOperation/讀取 | 取得私用端點連接作業的結果 |
> |  | **位置/privateEndpointConnectionOperationResults** |  |
> | 動作 | DBforPostgreSQL/位置/privateEndpointConnectionOperationResults/讀取 | 取得私用端點連接作業的結果 |
> |  | **位置/privateEndpointConnectionProxyAzureAsyncOperation** |  |
> | 動作 | DBforPostgreSQL/位置/privateEndpointConnectionProxyAzureAsyncOperation/讀取 | 取得私用端點連接 proxy 作業的結果 |
> |  | **位置/privateEndpointConnectionProxyOperationResults** |  |
> | 動作 | DBforPostgreSQL/位置/privateEndpointConnectionProxyOperationResults/讀取 | 取得私用端點連接 proxy 作業的結果 |
> |  | **位置/securityAlertPoliciesAzureAsyncOperation** |  |
> | 動作 | DBforPostgreSQL/位置/securityAlertPoliciesAzureAsyncOperation/讀取 | 傳回伺服器威脅偵測操作結果的清單。 |
> |  | **位置/securityAlertPoliciesOperationResults** |  |
> | 動作 | DBforPostgreSQL/位置/securityAlertPoliciesOperationResults/讀取 | 傳回伺服器威脅偵測操作結果的清單。 |
> |  | **位置/serverKeyAzureAsyncOperation** |  |
> | 動作 | DBforPostgreSQL/位置/serverKeyAzureAsyncOperation/讀取 | 取得透明資料加密伺服器金鑰的進行中作業 |
> |  | **位置/serverKeyOperationResults** |  |
> | 動作 | DBforPostgreSQL/位置/serverKeyOperationResults/讀取 | 取得透明資料加密伺服器金鑰的進行中作業 |
> |  | **業務** |  |
> | 動作 | DBforPostgreSQL/作業/讀取 | 傳回于 postgresql 作業的清單。 |
> |  | **performanceTiers** |  |
> | 動作 | Microsoft.DBforPostgreSQL/performanceTiers/read | 傳回可用的效能層級清單。 |
> |  | **伺服器** |  |
> | 動作 | Microsoft.DBforPostgreSQL/servers/queryTexts/action | 傳回查詢的文字 |
> | 動作 | Microsoft.DBforPostgreSQL/servers/read | 傳回伺服器清單，或取得指定伺服器的屬性。 |
> | 動作 | Microsoft.DBforPostgreSQL/servers/write | 使用指定參數建立伺服器，或更新指定伺服器的屬性或標記。 |
> | 動作 | Microsoft.DBforPostgreSQL/servers/delete | 刪除現有伺服器。 |
> | 動作 | DBforPostgreSQL/伺服器/重新開機/動作 | 重新開機特定的伺服器。 |
> | 動作 | Microsoft.DBforPostgreSQL/servers/updateConfigurations/action | 更新指定伺服器的組態 |
> |  | **伺服器/系統管理員** |  |
> | 動作 | DBforPostgreSQL/伺服器/系統管理員/讀取 | 取得于 postgresql 伺服器系統管理員的清單。 |
> | 動作 | DBforPostgreSQL/servers/administrator/write | 使用指定的參數建立或更新于 postgresql 伺服器系統管理員。 |
> | 動作 | DBforPostgreSQL/伺服器/系統管理員/刪除 | 刪除于 postgresql 伺服器的現有系統管理員。 |
> |  | **伺服器/顧問** |  |
> | 動作 | Microsoft.DBforPostgreSQL/servers/advisors/read | 傳回建議程式清單 |
> | 動作 | Microsoft.DBforPostgreSQL/servers/advisors/recommendedActionSessions/action | 提出建議 |
> |  | **伺服器/顧問/recommendedActions** |  |
> | 動作 | Microsoft.DBforPostgreSQL/servers/advisors/recommendedActions/read | 傳回建議的動作清單 |
> |  | **伺服器/設定** |  |
> | 動作 | Microsoft.DBforPostgreSQL/servers/configurations/read | 傳回伺服器的組態清單，或取得指定組態的屬性。 |
> | 動作 | Microsoft.DBforPostgreSQL/servers/configurations/write | 更新指定組態的值 |
> |  | **伺服器/資料庫** |  |
> | 動作 | DBforPostgreSQL/servers/資料庫/讀取 | 傳回于 postgresql 資料庫的清單，或取得指定之資料庫的屬性。 |
> | 動作 | DBforPostgreSQL/servers/資料庫/寫入 | 使用指定的參數建立于 postgresql 資料庫，或更新指定之資料庫的屬性。 |
> | 動作 | DBforPostgreSQL/伺服器/資料庫/刪除 | 刪除現有的于 postgresql 資料庫。 |
> |  | **伺服器/firewallRules** |  |
> | 動作 | Microsoft.DBforPostgreSQL/servers/firewallRules/read | 傳回伺服器的防火牆規則清單，或取得指定防火牆規則的屬性。 |
> | 動作 | Microsoft.DBforPostgreSQL/servers/firewallRules/write | 使用指定參數建立防火牆規則，或更新現有的規則。 |
> | 動作 | Microsoft.DBforPostgreSQL/servers/firewallRules/delete | 刪除現有防火牆規則。 |
> |  | **伺服器/金鑰** |  |
> | 動作 | DBforPostgreSQL/servers/keys/read | 傳回伺服器金鑰的清單，或取得指定伺服器金鑰的屬性。 |
> | 動作 | DBforPostgreSQL/servers/keys/write | 使用指定參數建立金鑰，或更新指定伺服器金鑰的屬性或標記。 |
> | 動作 | DBforPostgreSQL/servers/keys/delete | 刪除現有的伺服器金鑰。 |
> |  | **伺服器/記錄檔** |  |
> | 動作 | DBforPostgreSQL/servers/記錄檔/讀取 | 傳回于 postgresql 記錄檔的清單。 |
> |  | **伺服器/privateEndpointConnectionProxies** |  |
> | 動作 | DBforPostgreSQL/servers/privateEndpointConnectionProxies/validate/action | 驗證從 NRP 端的私用端點連接建立呼叫 |
> | 動作 | DBforPostgreSQL/servers/privateEndpointConnectionProxies/read | 傳回私用端點連接 proxy 的清單，或取得指定之私用端點連接 proxy 的屬性。 |
> | 動作 | DBforPostgreSQL/servers/privateEndpointConnectionProxies/write | 使用指定的參數建立私用端點連接 proxy，或更新指定之私用端點連接 proxy 的屬性或標記。 |
> | 動作 | DBforPostgreSQL/servers/privateEndpointConnectionProxies/delete | 刪除現有的私用端點連接 proxy |
> |  | **伺服器/privateEndpointConnections** |  |
> | 動作 | DBforPostgreSQL/servers/privateEndpointConnections/read | 傳回私用端點連接的清單，或取得指定之私用端點連接的屬性。 |
> | 動作 | DBforPostgreSQL/servers/privateEndpointConnections/delete | 刪除現有的私用端點連接 |
> | 動作 | DBforPostgreSQL/servers/privateEndpointConnections/write | 核准或拒絕現有的私用端點連接 |
> |  | **伺服器/privateLinkResources** |  |
> | 動作 | DBforPostgreSQL/servers/privateLinkResources/read | 取得對應于 postgresql 伺服器的私人連結資源 |
> |  | **伺服器/提供者/Microsoft Insights/diagnosticSettings** |  |
> | 動作 | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/diagnosticSettings/read | 取得資源的診斷設定 |
> | 動作 | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/diagnosticSettings/write | 建立或更新資源的診斷設定 |
> |  | **伺服器/提供者/Microsoft Insights/logDefinitions** |  |
> | 動作 | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/logDefinitions/read | 取得于 postgresql 伺服器的可用記錄 |
> |  | **伺服器/提供者/Microsoft Insights/Metricdefinitions.listasync** |  |
> | 動作 | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/metricDefinitions/read | 傳回可供資料庫使用之計量的類型 |
> |  | **伺服器/queryTexts** |  |
> | 動作 | DBforPostgreSQL/servers/queryTexts/read | 傳回查詢清單的文字 |
> |  | **伺服器/recoverableServers** |  |
> | 動作 | Microsoft.DBforPostgreSQL/servers/recoverableServers/read | 傳回可復原的 PostgreSQL 伺服器資訊 |
> |  | **伺服器/複本** |  |
> | 動作 | DBforPostgreSQL/servers/複本/讀取 | 取得于 postgresql 伺服器的讀取複本 |
> |  | **伺服器/securityAlertPolicies** |  |
> | 動作 | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/read | 擷取給定伺服器上所設定的伺服器威脅偵測原則詳細資料 |
> | 動作 | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/write | 變更指定伺服器的伺服器威脅偵測原則 |
> |  | **伺服器/topQueryStatistics** |  |
> | 動作 | Microsoft.DBforPostgreSQL/servers/topQueryStatistics/read | 傳回最高排名查詢的查詢統計資料清單。 |
> |  | **伺服器/virtualNetworkRules** |  |
> | 動作 | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/read | 傳回虛擬網路規則的清單，或取得指定虛擬網路規則的屬性。 |
> | 動作 | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/write | 使用指定參數建立虛擬網路規則，或更新指定虛擬網路規則的屬性或標記。 |
> | 動作 | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/delete | 刪除現有虛擬網路規則 |
> |  | **伺服器/waitStatistics** |  |
> | 動作 | Microsoft.DBforPostgreSQL/servers/waitStatistics/read | 傳回執行個體的等候統計資料 |
> |  | **serversv2** |  |
> | 動作 | DBforPostgreSQL/serversv2/read | 傳回伺服器清單，或取得指定伺服器的屬性。 |
> | 動作 | DBforPostgreSQL/serversv2/write | 使用指定參數建立伺服器，或更新指定伺服器的屬性或標記。 |
> | 動作 | DBforPostgreSQL/serversv2/delete | 刪除現有伺服器。 |
> | 動作 | DBforPostgreSQL/serversv2/updateConfigurations/action | 更新指定伺服器的組態 |
> |  | **serversv2/設定** |  |
> | 動作 | DBforPostgreSQL/serversv2/設定/讀取 | 傳回伺服器的組態清單，或取得指定組態的屬性。 |
> | 動作 | DBforPostgreSQL/serversv2/設定/寫入 | 更新指定組態的值 |
> |  | **serversv2/firewallRules** |  |
> | 動作 | DBforPostgreSQL/serversv2/firewallRules/read | 傳回伺服器的防火牆規則清單，或取得指定防火牆規則的屬性。 |
> | 動作 | DBforPostgreSQL/serversv2/firewallRules/write | 使用指定參數建立防火牆規則，或更新現有的規則。 |
> | 動作 | DBforPostgreSQL/serversv2/firewallRules/delete | 刪除現有防火牆規則。 |
> |  | **serversv2/providers/Microsoft Insights/diagnosticSettings** |  |
> | 動作 | DBforPostgreSQL/serversv2/provider/Microsoft Insights/diagnosticSettings/read | 取得資源的診斷設定 |
> | 動作 | DBforPostgreSQL/serversv2/provider/Microsoft Insights/diagnosticSettings/write | 建立或更新資源的診斷設定 |
> |  | **serversv2/providers/Microsoft Insights/logDefinitions** |  |
> | 動作 | DBforPostgreSQL/serversv2/provider/Microsoft Insights/logDefinitions/read | 取得于 postgresql 伺服器的可用記錄 |
> |  | **serversv2/providers/Microsoft Insights/Metricdefinitions.listasync** |  |
> | 動作 | DBforPostgreSQL/serversv2/provider/Microsoft Insights/Metricdefinitions.listasync/read | 傳回可供資料庫使用之計量的類型 |

## <a name="microsoftdevices"></a>Microsoft.Devices

Azure 服務： [IoT 中樞](../iot-hub/index.yml)、 [IoT 中樞裝置佈建服務](../iot-dps/index.yml)

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
> |  | **帳戶/diagnosticSettings** |  |
> | 動作 | Microsoft. 裝置/帳戶/diagnosticSettings/讀取 | 取得資源的診斷設定 |
> | 動作 | Microsoft。裝置/帳戶/diagnosticSettings/寫入 | 建立或更新資源的診斷設定 |
> |  | **帳戶/logDefinitions** |  |
> | 動作 | Microsoft. 裝置/帳戶/logDefinitions/讀取 | 取得 IotHub 服務的可用記錄定義 |
> |  | **帳戶/Metricdefinitions.listasync** |  |
> | 動作 | Microsoft. 裝置/帳戶/Metricdefinitions.listasync/讀取 | 取得 IotHub 服務的可用計量 |
> |  | **選取** |  |
> | 動作 | Microsoft. Devices/選取/Read | 取得與訂用帳戶相關聯的數位 Twins 帳戶清單 |
> | 動作 | Microsoft. Devices/選取/Write | 建立新的 Digitial Twins 帳戶 |
> | 動作 | Microsoft. Devices/選取/Delete | 刪除現有的數位 Twins 帳戶及其所有子系 |
> |  | **選取/operationresults** |  |
> | 動作 | Microsoft. Devices/選取/operationresults/Read | 針對數位 Twins 帳戶取得操作的狀態 |
> |  | **選取/sku** |  |
> | 動作 | Microsoft. Devices/選取/sku/Read | 取得數位 Twins 帳戶的有效 Sku 清單 |
> |  | **ElasticPools/diagnosticSettings** |  |
> | 動作 | Microsoft.Devices/ElasticPools/diagnosticSettings/read | 取得資源的診斷設定 |
> | 動作 | Microsoft.Devices/ElasticPools/diagnosticSettings/write | 建立或更新資源的診斷設定 |
> |  | **elasticPools/eventGridFilters** |  |
> | 動作 | Microsoft. Devices/elasticPools/eventGridFilters/Write | 建立新的或更新現有的彈性集區事件方格篩選 |
> | 動作 | Microsoft. Devices/elasticPools/eventGridFilters/Read | 取得彈性集區事件方格篩選 |
> | 動作 | Microsoft. Devices/elasticPools/eventGridFilters/Delete | 刪除彈性集區事件方格篩選 |
> |  | **elasticPools/iotHubTenants** |  |
> | 動作 | Microsoft.Devices/elasticPools/iotHubTenants/Write | 建立或更新 IotHub 租用戶資源 |
> | 動作 | Microsoft.Devices/elasticPools/iotHubTenants/Read | 取得 IotHub 租用戶資源 |
> | 動作 | Microsoft.Devices/elasticPools/iotHubTenants/Delete | 刪除 IotHub 租用戶資源 |
> | 動作 | Microsoft.Devices/elasticPools/iotHubTenants/listKeys/Action | 取得 IotHub 租用戶金鑰 |
> | 動作 | Microsoft.Devices/elasticPools/iotHubTenants/exportDevices/Action | 匯出裝置 |
> | 動作 | Microsoft.Devices/elasticPools/iotHubTenants/importDevices/Action | 匯入裝置 |
> |  | **elasticPools/iotHubTenants/憑證** |  |
> | 動作 | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Read | 取得憑證 |
> | 動作 | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Write | 建立或更新憑證 |
> | 動作 | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Delete | 刪除憑證 |
> | 動作 | Microsoft.Devices/elasticPools/iotHubTenants/certificates/generateVerificationCode/Action | 產生驗證碼 |
> | 動作 | Microsoft.Devices/elasticPools/iotHubTenants/certificates/verify/Action | 驗證憑證資源 |
> |  | **ElasticPools/IotHubTenants/diagnosticSettings** |  |
> | 動作 | Microsoft.Devices/ElasticPools/IotHubTenants/diagnosticSettings/read | 取得資源的診斷設定 |
> | 動作 | Microsoft.Devices/ElasticPools/IotHubTenants/diagnosticSettings/write | 建立或更新資源的診斷設定 |
> |  | **elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups** |  |
> | 動作 | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Write | 建立 EventHub 取用者群組 |
> | 動作 | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Read | 取得 EventHub 取用者群組 |
> | 動作 | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Delete | 刪除 EventHub 取用者群組 |
> |  | **elasticPools/iotHubTenants/getStats** |  |
> | 動作 | Microsoft.Devices/elasticPools/iotHubTenants/getStats/Read | 取得 IotHub 租用戶統計資料資源 |
> |  | **elasticPools/iotHubTenants/iotHubKeys** |  |
> | 動作 | Microsoft.Devices/elasticPools/iotHubTenants/iotHubKeys/listkeys/Action | 取得 IotHub 租用戶金鑰 |
> |  | **elasticPools/iotHubTenants/作業** |  |
> | 動作 | Microsoft.Devices/elasticPools/iotHubTenants/jobs/Read | 取得在給定的 IotHub 上所提交的作業詳細資料 |
> |  | **ElasticPools/IotHubTenants/logDefinitions** |  |
> | 動作 | Microsoft.Devices/ElasticPools/IotHubTenants/logDefinitions/read | 取得 IotHub 服務的可用記錄定義 |
> |  | **ElasticPools/IotHubTenants/Metricdefinitions.listasync** |  |
> | 動作 | Microsoft.Devices/ElasticPools/IotHubTenants/metricDefinitions/read | 取得 IotHub 服務的可用計量 |
> |  | **elasticPools/iotHubTenants/quotaMetrics** |  |
> | 動作 | Microsoft.Devices/elasticPools/iotHubTenants/quotaMetrics/Read | 取得配額計量 |
> |  | **elasticPools/iotHubTenants/路由/路由** |  |
> | 動作 | Microsoft.Devices/elasticPools/iotHubTenants/routing/routes/$testall/Action | 針對所有現有路由來測試訊息 |
> | 動作 | Microsoft.Devices/elasticPools/iotHubTenants/routing/routes/$testnew/Action | 針對所提供的測試路由來測試訊息 |
> |  | **elasticPools/iotHubTenants/routingEndpointsHealth** |  |
> | 動作 | Microsoft.Devices/elasticPools/iotHubTenants/routingEndpointsHealth/Read | 取得 IotHub 之所有路由端點的健康狀態 |
> |  | **elasticPools/iotHubTenants/securitySettings** |  |
> | 動作 | Microsoft. Devices/elasticPools/iotHubTenants/securitySettings/Write | 更新 Iot 租使用者中樞上的 Azure 資訊安全中心設定 |
> | 動作 | Microsoft. Devices/elasticPools/iotHubTenants/securitySettings/Read | 取得 Iot 租使用者中樞上的 Azure 資訊安全中心設定 |
> |  | **elasticPools/iotHubTenants/securitySettings/operationResults** |  |
> | 動作 | Microsoft. Devices/elasticPools/iotHubTenants/securitySettings/operationResults/Read | 取得 Iot 租使用者中樞 SecuritySettings 的非同步 Put 作業結果 |
> |  | **ElasticPools/Metricdefinitions.listasync** |  |
> | 動作 | Microsoft.Devices/ElasticPools/metricDefinitions/read | 取得 IotHub 服務的可用計量 |
> |  | **iotHubs** |  |
> | 動作 | Microsoft.Devices/iotHubs/Read | 取得 IotHub 資源 |
> | 動作 | Microsoft.Devices/iotHubs/Write | 建立或更新 IotHub 資源 |
> | 動作 | Microsoft.Devices/iotHubs/Delete | 刪除 IotHub 資源 |
> | 動作 | Microsoft.Devices/iotHubs/listkeys/Action | 取得所有 IotHub 金鑰 |
> | 動作 | Microsoft.Devices/iotHubs/exportDevices/Action | 匯出裝置 |
> | 動作 | Microsoft.Devices/iotHubs/importDevices/Action | 匯入裝置 |
> | 動作 | Microsoft. Devices/iotHubs/privateEndpointConnectionsApproval/Action | 核准或拒絕私人端點連接 |
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
> |  | **IotHubs/diagnosticSettings** |  |
> | 動作 | Microsoft.Devices/IotHubs/diagnosticSettings/read | 取得資源的診斷設定 |
> | 動作 | Microsoft.Devices/IotHubs/diagnosticSettings/write | 建立或更新資源的診斷設定 |
> |  | **iotHubs/digitalTwinsLinks** |  |
> | 動作 | Microsoft. Devices/iotHubs/digitalTwinsLinks/Write |  |
> | 動作 | Microsoft. Devices/iotHubs/digitalTwinsLinks/Read |  |
> | 動作 | Microsoft. Devices/iotHubs/digitalTwinsLinks/Delete |  |
> |  | **iotHubs/eventGridFilters** |  |
> | 動作 | Microsoft.Devices/iotHubs/eventGridFilters/Write | 建立新的或更新現有的事件格線篩選 |
> | 動作 | Microsoft.Devices/iotHubs/eventGridFilters/Read | 取得事件格線篩選 |
> | 動作 | Microsoft.Devices/iotHubs/eventGridFilters/Delete | 刪除事件格線篩選 |
> |  | **iotHubs/eventHubEndpoints/consumerGroups** |  |
> | 動作 | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Write | 建立 EventHub 取用者群組 |
> | 動作 | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Read | 取得 EventHub 取用者群組 |
> | 動作 | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Delete | 刪除 EventHub 取用者群組 |
> |  | **iotHubs/iotHubKeys** |  |
> | 動作 | Microsoft.Devices/iotHubs/iotHubKeys/listkeys/Action | 取得給定名稱的 IotHub 金鑰 |
> |  | **iotHubs/iotHubStats** |  |
> | 動作 | Microsoft.Devices/iotHubs/iotHubStats/Read | 取得 IotHub 統計資料 |
> |  | **iotHubs/作業** |  |
> | 動作 | Microsoft.Devices/iotHubs/jobs/Read | 取得在給定的 IotHub 上所提交的作業詳細資料 |
> |  | **IotHubs/logDefinitions** |  |
> | 動作 | Microsoft.Devices/IotHubs/logDefinitions/read | 取得 IotHub 服務的可用記錄定義 |
> |  | **IotHubs/Metricdefinitions.listasync** |  |
> | 動作 | Microsoft.Devices/IotHubs/metricDefinitions/read | 取得 IotHub 服務的可用計量 |
> |  | **iotHubs/operationresults** |  |
> | 動作 | Microsoft.Devices/iotHubs/operationresults/Read | 取得作業結果 (過時的 API) |
> |  | **iotHubs/privateEndpointConnectionProxies** |  |
> | 動作 | Microsoft. Devices/iotHubs/privateEndpointConnectionProxies/validate/Action | 在建立期間驗證私用端點連接 proxy 輸入 |
> | 動作 | Microsoft. Devices/iotHubs/privateEndpointConnectionProxies/Read | 取得指定之私用端點連接 proxy 的屬性 |
> | 動作 | Microsoft. Devices/iotHubs/privateEndpointConnectionProxies/Write | 建立或更新私用端點連接 proxy |
> | 動作 | Microsoft. Devices/iotHubs/privateEndpointConnectionProxies/Delete | 刪除現有的私用端點連接 proxy |
> |  | **iotHubs/privateEndpointConnectionProxies/operationResults** |  |
> | 動作 | Microsoft. Devices/iotHubs/privateEndpointConnectionProxies/operationResults/Read | 取得私用端點連接 proxy 上非同步作業的結果 |
> |  | **iotHubs/privateEndpointConnections** |  |
> | 動作 | Microsoft. Devices/iotHubs/privateEndpointConnections/Read | 取得指定之私用端點連接的屬性 |
> | 動作 | Microsoft. Devices/iotHubs/privateEndpointConnections/Delete | 刪除現有的私用端點連接 |
> | 動作 | Microsoft. Devices/iotHubs/privateEndpointConnections/Write | 建立或更新私用端點連接 |
> |  | **iotHubs/privateEndpointConnections/operationResults** |  |
> | 動作 | Microsoft. Devices/iotHubs/privateEndpointConnections/operationResults/Read | 取得私用端點連接上非同步作業的結果 |
> |  | **iotHubs/privateLinkResources** |  |
> | 動作 | Microsoft. Devices/iotHubs/privateLinkResources/Read | 取得 IotHub 的私人連結資源 |
> |  | **iotHubs/quotaMetrics** |  |
> | 動作 | Microsoft.Devices/iotHubs/quotaMetrics/Read | 取得配額計量 |
> |  | **iotHubs/路由** |  |
> | 動作 | Microsoft.Devices/iotHubs/routing/$testall/Action | 針對所有現有路由來測試訊息 |
> | 動作 | Microsoft.Devices/iotHubs/routing/$testnew/Action | 針對所提供的測試路由來測試訊息 |
> |  | **iotHubs/routingEndpointsHealth** |  |
> | 動作 | Microsoft.Devices/iotHubs/routingEndpointsHealth/Read | 取得 IotHub 之所有路由端點的健康狀態 |
> |  | **iotHubs/securitySettings** |  |
> | 動作 | Microsoft. Devices/iotHubs/securitySettings/Write | 更新 Iot 中樞上的 Azure 資訊安全中心設定 |
> | 動作 | Microsoft. Devices/iotHubs/securitySettings/Read | 取得 Iot 中樞上的 Azure 資訊安全中心設定 |
> |  | **iotHubs/securitySettings/operationResults** |  |
> | 動作 | Microsoft. Devices/iotHubs/securitySettings/operationResults/Read | 取得 Iot 中樞 SecuritySettings 的非同步 Put 作業結果 |
> |  | **iotHubs/sku** |  |
> | 動作 | Microsoft.Devices/iotHubs/skus/Read | 取得有效的 IotHub SKU |
> |  | **位置/operationresults** |  |
> | 動作 | Microsoft.Devices/locations/operationresults/Read | 取得以位置為基礎的作業結果 |
> | 動作 | Microsoft.Devices/locations/operationresults/Read | 取得以位置為基礎的作業結果 |
> |  | **operationresults** |  |
> | 動作 | Microsoft.Devices/operationresults/Read | 取得作業結果 |
> | 動作 | Microsoft.Devices/operationresults/Read | 取得作業結果 |
> |  | **業務** |  |
> | 動作 | Microsoft.Devices/operations/Read | 取得所有 ResourceProvider 作業 |
> | 動作 | Microsoft.Devices/operations/Read | 取得所有 ResourceProvider 作業 |
> |  | **provisioningServices** |  |
> | 動作 | Microsoft.Devices/provisioningServices/Read | 取得 IotDps 資源 |
> | 動作 | Microsoft.Devices/provisioningServices/Write | 建立 IotDps 資源 |
> | 動作 | Microsoft.Devices/provisioningServices/Delete | 刪除 IotDps 資源 |
> | 動作 | Microsoft.Devices/provisioningServices/listkeys/Action | 取得所有 IotDps 金鑰 |
> |  | **provisioningServices/憑證** |  |
> | 動作 | Microsoft.Devices/provisioningServices/certificates/Read | 取得憑證 |
> | 動作 | Microsoft.Devices/provisioningServices/certificates/Write | 建立或更新憑證 |
> | 動作 | Microsoft.Devices/provisioningServices/certificates/Delete | 刪除憑證 |
> | 動作 | Microsoft.Devices/provisioningServices/certificates/generateVerificationCode/Action | 產生驗證碼 |
> | 動作 | Microsoft.Devices/provisioningServices/certificates/verify/Action | 驗證憑證資源 |
> |  | **provisioningServices/diagnosticSettings** |  |
> | 動作 | Microsoft.Devices/provisioningServices/diagnosticSettings/read | 取得資源的診斷設定 |
> | 動作 | Microsoft.Devices/provisioningServices/diagnosticSettings/write | 建立或更新資源的診斷設定 |
> |  | **provisioningServices/金鑰** |  |
> | 動作 | Microsoft.Devices/provisioningServices/keys/listkeys/Action | 取得金鑰名稱的 IotDps 金鑰 |
> |  | **provisioningServices/logDefinitions** |  |
> | 動作 | Microsoft.Devices/provisioningServices/logDefinitions/read | 取得佈建服務的可用記錄定義 |
> |  | **provisioningServices/Metricdefinitions.listasync** |  |
> | 動作 | Microsoft.Devices/provisioningServices/metricDefinitions/read | 取得佈建服務的可用計量 |
> |  | **provisioningServices/operationresults** |  |
> | 動作 | Microsoft.Devices/provisioningServices/operationresults/Read | 取得 DPS 作業結果 |
> |  | **provisioningServices/sku** |  |
> | 動作 | Microsoft.Devices/provisioningServices/skus/Read | 取得有效的 IotDps SKU |
> |  | **實例** |  |
> | 動作 | Microsoft.Devices/usages/Read | 取得此提供者的訂用帳戶使用量詳細資料。 |
> | 動作 | Microsoft.Devices/usages/Read | 取得此提供者的訂用帳戶使用量詳細資料。 |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

Azure 服務： [Azure Dev Spaces](../dev-spaces/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.DevSpaces/register/action | 向訂用帳戶註冊 Microsoft Dev Spaces 資源提供者 |
> |  | **控制器** |  |
> | 動作 | Microsoft.DevSpaces/controllers/read | 讀取 Azure Dev Spaces 控制器屬性 |
> | 動作 | Microsoft.DevSpaces/controllers/write | 建立或更新 Azure Dev Spaces 控制器屬性 |
> | 動作 | Microsoft.DevSpaces/controllers/delete | 刪除 Azure Dev Spaces 控制器與資料平面服務 |
> | 動作 | Microsoft.DevSpaces/controllers/listConnectionDetails/action | 列出 Azure Dev Spaces 控制器基礎結構的連接詳細資料 |
> |  | **堆放** |  |
> | 動作 | DevSpaces/位置/checkContainerHostMapping/動作 | 檢查容器主機的現有控制器對應 |
> |  | **位置/checkContainerHostMapping** |  |
> | 動作 | DevSpaces/位置/checkContainerHostMapping/讀取 | 檢查容器主機的現有控制器對應 |
> |  | **位置/operationresults** |  |
> | 動作 | DevSpaces/位置/operationresults/讀取 | 讀取非同步作業的狀態 |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

Azure 服務： [Azure 實驗室服務](../lab-services/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.DevTestLab/register/action | 註冊訂用帳戶 |
> |  | **labCenters** |  |
> | 動作 | Microsoft.DevTestLab/labCenters/delete | 刪除實驗室中心。 |
> | 動作 | Microsoft.DevTestLab/labCenters/read | 讀取實驗室中心。 |
> | 動作 | Microsoft.DevTestLab/labCenters/write | 新增或修改實驗室中心。 |
> |  | **檢驗** |  |
> | 動作 | Microsoft.DevTestLab/labs/delete | 刪除實驗室。 |
> | 動作 | Microsoft.DevTestLab/labs/read | 讀取實驗室。 |
> | 動作 | Microsoft.DevTestLab/labs/write | 新增或修改實驗室。 |
> | 動作 | Microsoft.DevTestLab/labs/ListVhds/action | 列出可供建立自訂映像的磁碟映像。 |
> | 動作 | Microsoft.DevTestLab/labs/GenerateUploadUri/action | 產生 URI 以用來將自訂磁碟映像上傳到實驗室。 |
> | 動作 | Microsoft.DevTestLab/labs/CreateEnvironment/action | 在實驗室中建立虛擬機器。 |
> | 動作 | Microsoft.DevTestLab/labs/ClaimAnyVm/action | 在實驗室中宣告隨機的可宣告虛擬機器。 |
> | 動作 | Microsoft.DevTestLab/labs/ExportResourceUsage/action | 將實驗室資源使用狀況匯出到儲存體帳戶 |
> | 動作 | Microsoft.DevTestLab/labs/ImportVirtualMachine/action | 將虛擬機器匯入不同的實驗室。 |
> | 動作 | Microsoft.devtestlab/labs/EnsureCurrentUserProfile/action | 請確定目前的使用者在實驗室中具有有效的設定檔。 |
> |  | **實驗室/artifactSources** |  |
> | 動作 | Microsoft.DevTestLab/labs/artifactSources/delete | 刪除構件來源。 |
> | 動作 | Microsoft.DevTestLab/labs/artifactSources/read | 讀取構件來源。 |
> | 動作 | Microsoft.DevTestLab/labs/artifactSources/write | 新增或修改構件來源。 |
> |  | **labs/artifactSources/armTemplates** |  |
> | 動作 | Microsoft.DevTestLab/labs/artifactSources/armTemplates/read | 讀取 Azure Resource Manager 範本。 |
> |  | **labs/artifactSources/構件** |  |
> | 動作 | Microsoft.DevTestLab/labs/artifactSources/artifacts/read | 讀取構件。 |
> | 動作 | Microsoft.DevTestLab/labs/artifactSources/artifacts/GenerateArmTemplate/action | 為指定的成品產生 Azure Resource Manager 範本、將所需的檔案上傳至儲存體帳戶，並驗證產生的成品。 |
> |  | **實驗室/成本** |  |
> | 動作 | Microsoft.DevTestLab/labs/costs/read | 讀取成本。 |
> | 動作 | Microsoft.DevTestLab/labs/costs/write | 新增或修改成本。 |
> |  | **實驗室/customImages** |  |
> | 動作 | Microsoft.DevTestLab/labs/customImages/delete | 刪除自訂映像。 |
> | 動作 | Microsoft.DevTestLab/labs/customImages/read | 讀取自訂映像。 |
> | 動作 | Microsoft.DevTestLab/labs/customImages/write | 新增或修改自訂映像。 |
> |  | **實驗室/公式** |  |
> | 動作 | Microsoft.DevTestLab/labs/formulas/delete | 刪除公式。 |
> | 動作 | Microsoft.DevTestLab/labs/formulas/read | 讀取公式。 |
> | 動作 | Microsoft.DevTestLab/labs/formulas/write | 新增或修改公式。 |
> |  | **實驗室/galleryImages** |  |
> | 動作 | Microsoft.DevTestLab/labs/galleryImages/read | 讀取資源庫映像。 |
> |  | **實驗室/notificationChannels** |  |
> | 動作 | Microsoft.DevTestLab/labs/notificationChannels/delete | 刪除通知通道。 |
> | 動作 | Microsoft.DevTestLab/labs/notificationChannels/read | 讀取通知通道。 |
> | 動作 | Microsoft.DevTestLab/labs/notificationChannels/write | 新增或修改通知通道。 |
> | 動作 | Microsoft.DevTestLab/labs/notificationChannels/Notify/action | 對所提供的通道傳送通知。 |
> |  | **實驗室/policySets** |  |
> | 動作 | Microsoft.DevTestLab/labs/policySets/read | 讀取原則集合。 |
> | 動作 | Microsoft.DevTestLab/labs/policySets/EvaluatePolicies/action | 評估實驗室原則。 |
> |  | **實驗室/policySets/原則** |  |
> | 動作 | Microsoft.DevTestLab/labs/policySets/policies/delete | 刪除原則。 |
> | 動作 | Microsoft.DevTestLab/labs/policySets/policies/read | 讀取原則。 |
> | 動作 | Microsoft.DevTestLab/labs/policySets/policies/write | 新增或修改原則。 |
> |  | **實驗室/排程** |  |
> | 動作 | Microsoft.DevTestLab/labs/schedules/delete | 刪除排程。 |
> | 動作 | Microsoft.DevTestLab/labs/schedules/read | 讀取排程。 |
> | 動作 | Microsoft.DevTestLab/labs/schedules/write | 新增或修改排程。 |
> | 動作 | Microsoft.DevTestLab/labs/schedules/Execute/action | 執行排程。 |
> | 動作 | Microsoft.DevTestLab/labs/schedules/ListApplicable/action | 列出所有適用排程 |
> |  | **實驗室/serviceRunners** |  |
> | 動作 | Microsoft.DevTestLab/labs/serviceRunners/delete | 刪除服務執行器。 |
> | 動作 | Microsoft.DevTestLab/labs/serviceRunners/read | 讀取服務執行器。 |
> | 動作 | Microsoft.DevTestLab/labs/serviceRunners/write | 新增或修改服務執行器。 |
> |  | **實驗室/共用資源庫** |  |
> | 動作 | Microsoft.devtestlab/labs/共用資源庫/delete | 刪除共用的資源庫。 |
> | 動作 | Microsoft.DevTestLab/labs/sharedGalleries/read | 讀取共用的資源庫。 |
> | 動作 | Microsoft.devtestlab/labs/共用資源庫/write | 新增或修改共用資源庫。 |
> |  | **labs/共用資源庫/共用影像** |  |
> | 動作 | Microsoft.devtestlab/labs/共用資源庫/共用影像/delete | 刪除共用映射。 |
> | 動作 | Microsoft.devtestlab/labs/共用資源庫/共用影像/read | 讀取共用映射。 |
> | 動作 | Microsoft.devtestlab/labs/共用資源庫/共用影像/write | 新增或修改共用映射。 |
> |  | **實驗室/使用者** |  |
> | 動作 | Microsoft.DevTestLab/labs/users/delete | 刪除使用者設定檔。 |
> | 動作 | Microsoft.DevTestLab/labs/users/read | 讀取使用者設定檔。 |
> | 動作 | Microsoft.DevTestLab/labs/users/write | 新增或修改使用者設定檔。 |
> |  | **實驗室/使用者/磁片** |  |
> | 動作 | Microsoft.DevTestLab/labs/users/disks/delete | 刪除磁碟。 |
> | 動作 | Microsoft.DevTestLab/labs/users/disks/read | 讀取磁碟。 |
> | 動作 | Microsoft.DevTestLab/labs/users/disks/write | 新增或修改磁碟。 |
> | 動作 | Microsoft.DevTestLab/labs/users/disks/Attach/action | 將磁碟連結至虛擬機器並建立磁碟租用。 |
> | 動作 | Microsoft.DevTestLab/labs/users/disks/Detach/action | 將連結至虛擬機器的磁碟中斷連結並中斷磁碟租用。 |
> |  | **實驗室/使用者/環境** |  |
> | 動作 | Microsoft.DevTestLab/labs/users/environments/delete | 刪除環境。 |
> | 動作 | Microsoft.DevTestLab/labs/users/environments/read | 讀取環境。 |
> | 動作 | Microsoft.DevTestLab/labs/users/environments/write | 新增或修改環境。 |
> |  | **實驗室/使用者/秘密** |  |
> | 動作 | Microsoft.DevTestLab/labs/users/secrets/delete | 刪除祕密。 |
> | 動作 | Microsoft.DevTestLab/labs/users/secrets/read | 讀取祕密。 |
> | 動作 | Microsoft.DevTestLab/labs/users/secrets/write | 新增或修改祕密。 |
> |  | **實驗室/使用者/serviceFabrics** |  |
> | 動作 | Microsoft.DevTestLab/labs/users/serviceFabrics/delete | 刪除 Service Fabric。 |
> | 動作 | Microsoft.DevTestLab/labs/users/serviceFabrics/read | 讀取 Service Fabric。 |
> | 動作 | Microsoft.DevTestLab/labs/users/serviceFabrics/write | 新增或修改 Service Fabric。 |
> | 動作 | Microsoft.DevTestLab/labs/users/serviceFabrics/Start/action | 啟動 Service Fabric。 |
> | 動作 | Microsoft.DevTestLab/labs/users/serviceFabrics/Stop/action | 停止 Service Fabric |
> | 動作 | Microsoft.DevTestLab/labs/users/serviceFabrics/ListApplicableSchedules/action | 列出適用的啟動/停止排程 (若有的話)。 |
> |  | **實驗室/使用者/serviceFabrics/排程** |  |
> | 動作 | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/delete | 刪除排程。 |
> | 動作 | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/read | 讀取排程。 |
> | 動作 | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/write | 新增或修改排程。 |
> | 動作 | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/Execute/action | 執行排程。 |
> |  | **實驗室/virtualMachines** |  |
> | 動作 | Microsoft.DevTestLab/labs/virtualMachines/delete | 刪除虛擬機器。 |
> | 動作 | Microsoft.DevTestLab/labs/virtualMachines/read | 讀取虛擬機器。 |
> | 動作 | Microsoft.DevTestLab/labs/virtualMachines/write | 新增或修改虛擬機器。 |
> | 動作 | Microsoft.DevTestLab/labs/virtualMachines/AddDataDisk/action | 將新的或現有的資料磁碟連結至虛擬機器。 |
> | 動作 | Microsoft.DevTestLab/labs/virtualMachines/ApplyArtifacts/action | 將構件套用至虛擬機器。 |
> | 動作 | Microsoft.DevTestLab/labs/virtualMachines/Claim/action | 取得現有虛擬機器的擁有權 |
> | 動作 | Microsoft.devtestlab/labs/virtualMachines/ClearArtifactResults/action | 清除虛擬機器的成品結果。 |
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
> |  | **實驗室/virtualMachines/排程** |  |
> | 動作 | Microsoft.DevTestLab/labs/virtualMachines/schedules/delete | 刪除排程。 |
> | 動作 | Microsoft.DevTestLab/labs/virtualMachines/schedules/read | 讀取排程。 |
> | 動作 | Microsoft.DevTestLab/labs/virtualMachines/schedules/write | 新增或修改排程。 |
> | 動作 | Microsoft.DevTestLab/labs/virtualMachines/schedules/Execute/action | 執行排程。 |
> |  | **實驗室/virtualNetworks** |  |
> | 動作 | Microsoft.DevTestLab/labs/virtualNetworks/delete | 刪除虛擬網路。 |
> | 動作 | Microsoft.DevTestLab/labs/virtualNetworks/read | 讀取虛擬網路。 |
> | 動作 | Microsoft.DevTestLab/labs/virtualNetworks/write | 新增或修改虛擬網路。 |
> |  | **labs/virtualNetworks/bastionHosts** |  |
> | 動作 | Microsoft.devtestlab/labs/virtualNetworks/bastionHosts/delete | 刪除 bastionhosts。 |
> | 動作 | Microsoft.devtestlab/labs/virtualNetworks/bastionHosts/read | 閱讀 bastionhosts。 |
> | 動作 | Microsoft.devtestlab/labs/virtualNetworks/bastionHosts/write | 新增或修改 bastionhosts。 |
> |  | **實驗室/vmPools** |  |
> | 動作 | Microsoft.DevTestLab/labs/vmPools/delete | 刪除虛擬機器集區。 |
> | 動作 | Microsoft.DevTestLab/labs/vmPools/read | 讀取虛擬機器集區。 |
> | 動作 | Microsoft.DevTestLab/labs/vmPools/write | 新增或修改虛擬機器集區。 |
> |  | **位置/作業** |  |
> | 動作 | Microsoft.DevTestLab/locations/operations/read | 讀取作業。 |
> |  | **預定** |  |
> | 動作 | Microsoft.DevTestLab/schedules/delete | 刪除排程。 |
> | 動作 | Microsoft.DevTestLab/schedules/read | 讀取排程。 |
> | 動作 | Microsoft.DevTestLab/schedules/write | 新增或修改排程。 |
> | 動作 | Microsoft.DevTestLab/schedules/Execute/action | 執行排程。 |
> | 動作 | Microsoft.DevTestLab/schedules/Retarget/action | 更新排程的目標資源識別碼。 |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

Azure 服務： [Azure Cosmos DB](../cosmos-db/index.yml)

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
> | 動作 | Microsoft DocumentDB/databaseAccounts/getBackupPolicy/action | 取得資料庫帳戶的備份原則 |
> | 動作 | Microsoft.DocumentDB/databaseAccounts/restore/action | 提交還原要求 |
> | 動作 | Microsoft.DocumentDB/databaseAccounts/backup/action | 提交要求以設定備份 |
> |  | **databaseAccounts/cassandraKeyspaces** |  |
> | 動作 | Microsoft DocumentDB/databaseAccounts/cassandraKeyspaces/write | 建立 Cassandra keyspace。 |
> | 動作 | Microsoft DocumentDB/databaseAccounts/cassandraKeyspaces/read | 閱讀 Cassandra keyspace 或列出所有 Cassandra keyspace。 |
> | 動作 | Microsoft DocumentDB/databaseAccounts/cassandraKeyspaces/delete | 刪除 Cassandra keyspace。 |
> |  | **databaseAccounts/cassandraKeyspaces/operationResults** |  |
> | 動作 | Microsoft DocumentDB/databaseAccounts/cassandraKeyspaces/operationResults/read | 讀取非同步作業的狀態。 |
> |  | **databaseAccounts/cassandraKeyspaces/tables** |  |
> | 動作 | Microsoft DocumentDB/databaseAccounts/cassandraKeyspaces/tables/write | 建立或更新 Cassandra 資料表。 |
> | 動作 | Microsoft DocumentDB/databaseAccounts/cassandraKeyspaces/資料表/讀取 | 讀取 Cassandra 資料表或列出所有 Cassandra 資料表。 |
> | 動作 | Microsoft DocumentDB/databaseAccounts/cassandraKeyspaces/tables/delete | 刪除 Cassandra 資料表。 |
> |  | **databaseAccounts/cassandraKeyspaces/tables/operationResults** |  |
> | 動作 | Microsoft DocumentDB/databaseAccounts/cassandraKeyspaces/tables/operationResults/read | 讀取非同步作業的狀態。 |
> |  | **databaseAccounts/cassandraKeyspaces/tables/throughputSettings** |  |
> | 動作 | Microsoft DocumentDB/databaseAccounts/cassandraKeyspaces/tables/throughputSettings/write | 更新 Cassandra 資料表的輸送量。 |
> | 動作 | Microsoft DocumentDB/databaseAccounts/cassandraKeyspaces/tables/throughputSettings/read | 讀取 Cassandra 資料表的輸送量。 |
> |  | **databaseAccounts/cassandraKeyspaces/tables/throughputSettings/operationResults** |  |
> | 動作 | Microsoft DocumentDB/databaseAccounts/cassandraKeyspaces/tables/throughputSettings/operationResults/read | 讀取非同步作業的狀態。 |
> |  | **databaseAccounts/cassandraKeyspaces/throughputSettings** |  |
> | 動作 | Microsoft DocumentDB/databaseAccounts/cassandraKeyspaces/throughputSettings/write | 更新 Cassandra keyspace 輸送量。 |
> | 動作 | Microsoft DocumentDB/databaseAccounts/cassandraKeyspaces/throughputSettings/read | 閱讀 Cassandra keyspace 輸送量。 |
> |  | **databaseAccounts/cassandraKeyspaces/throughputSettings/operationResults** |  |
> | 動作 | Microsoft DocumentDB/databaseAccounts/cassandraKeyspaces/throughputSettings/operationResults/read | 讀取非同步作業的狀態。 |
> |  | **databaseAccounts/資料庫/集合/Metricdefinitions.listasync** |  |
> | 動作 | Microsoft.DocumentDB/databaseAccounts/databases/collections/metricDefinitions/read | 讀取集合計量定義。 |
> |  | **databaseAccounts/資料庫/集合/計量** |  |
> | 動作 | Microsoft.DocumentDB/databaseAccounts/databases/collections/metrics/read | 讀取集合計量。 |
> |  | **databaseAccounts/資料庫/集合/partitionKeyRangeId/計量** |  |
> | 動作 | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitionKeyRangeId/metrics/read | 讀取資料庫帳戶分割區索引鍵層級計量 |
> |  | **databaseAccounts/資料庫/集合/資料分割** |  |
> | 動作 | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/read | 讀取集合中的資料庫帳戶分割區 |
> |  | **databaseAccounts/資料庫/集合/資料分割/計量** |  |
> | 動作 | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/metrics/read | 讀取資料庫帳戶分割區層級計量 |
> |  | **databaseAccounts/資料庫/集合/資料分割/使用量** |  |
> | 動作 | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/usages/read | 讀取資料庫帳戶分割區層級使用量 |
> |  | **databaseAccounts/資料庫/集合/使用方式** |  |
> | 動作 | Microsoft.DocumentDB/databaseAccounts/databases/collections/usages/read | 讀取集合使用量。 |
> |  | **databaseAccounts/資料庫/Metricdefinitions.listasync** |  |
> | 動作 | Microsoft.DocumentDB/databaseAccounts/databases/metricDefinitions/read | 讀取資料庫計量定義 |
> |  | **databaseAccounts/資料庫/計量** |  |
> | 動作 | Microsoft.DocumentDB/databaseAccounts/databases/metrics/read | 讀取資料庫計量。 |
> |  | **databaseAccounts/資料庫/使用方式** |  |
> | 動作 | Microsoft.DocumentDB/databaseAccounts/databases/usages/read | 讀取資料庫使用量。 |
> |  | **databaseAccounts/gremlinDatabases** |  |
> | 動作 | Microsoft DocumentDB/databaseAccounts/gremlinDatabases/write | 建立 Gremlin 資料庫。 |
> | 動作 | Microsoft DocumentDB/databaseAccounts/gremlinDatabases/read | 讀取 Gremlin 資料庫或列出所有的 Gremlin 資料庫。 |
> | 動作 | Microsoft DocumentDB/databaseAccounts/gremlinDatabases/delete | 刪除 Gremlin 資料庫。 |
> |  | **databaseAccounts/gremlinDatabases/圖形** |  |
> | 動作 | Microsoft DocumentDB/databaseAccounts/gremlinDatabases/圖形/寫入 | 建立或更新 Gremlin 圖形。 |
> | 動作 | Microsoft DocumentDB/databaseAccounts/gremlinDatabases/圖形/讀取 | 讀取 Gremlin 圖形或列出所有 Gremlin 圖形。 |
> | 動作 | Microsoft DocumentDB/databaseAccounts/gremlinDatabases/圖形/刪除 | 刪除 Gremlin 圖形。 |
> |  | **databaseAccounts/gremlinDatabases/圖形/operationResults** |  |
> | 動作 | Microsoft DocumentDB/databaseAccounts/gremlinDatabases/圖形/operationResults/read | 讀取非同步作業的狀態。 |
> |  | **databaseAccounts/gremlinDatabases/圖形/throughputSettings** |  |
> | 動作 | Microsoft DocumentDB/databaseAccounts/gremlinDatabases/圖形/throughputSettings/write | 更新 Gremlin 圖形輸送量。 |
> | 動作 | Microsoft DocumentDB/databaseAccounts/gremlinDatabases/圖形/throughputSettings/read | 讀取 Gremlin 圖形輸送量。 |
> |  | **databaseAccounts/gremlinDatabases/圖形/throughputSettings/operationResults** |  |
> | 動作 | Microsoft DocumentDB/databaseAccounts/gremlinDatabases/圖形/throughputSettings/operationResults/read | 讀取非同步作業的狀態。 |
> |  | **databaseAccounts/gremlinDatabases/operationResults** |  |
> | 動作 | Microsoft DocumentDB/databaseAccounts/gremlinDatabases/operationResults/read | 讀取非同步作業的狀態。 |
> |  | **databaseAccounts/gremlinDatabases/throughputSettings** |  |
> | 動作 | Microsoft DocumentDB/databaseAccounts/gremlinDatabases/throughputSettings/write | 更新 Gremlin 資料庫輸送量。 |
> | 動作 | Microsoft DocumentDB/databaseAccounts/gremlinDatabases/throughputSettings/read | 讀取 Gremlin 資料庫輸送量。 |
> |  | **databaseAccounts/gremlinDatabases/throughputSettings/operationResults** |  |
> | 動作 | Microsoft DocumentDB/databaseAccounts/gremlinDatabases/throughputSettings/operationResults/read | 讀取非同步作業的狀態。 |
> |  | **databaseAccounts/Metricdefinitions.listasync** |  |
> | 動作 | Microsoft.DocumentDB/databaseAccounts/metricDefinitions/read | 讀取資料庫帳戶的計量定義。 |
> |  | **databaseAccounts/計量** |  |
> | 動作 | Microsoft.DocumentDB/databaseAccounts/metrics/read | 讀取資料庫帳戶的計量。 |
> |  | **databaseAccounts/mongodbDatabases** |  |
> | 動作 | Microsoft DocumentDB/databaseAccounts/mongodbDatabases/write | 建立 MongoDB 資料庫。 |
> | 動作 | Microsoft DocumentDB/databaseAccounts/mongodbDatabases/read | 讀取 MongoDB 資料庫或列出所有 MongoDB 資料庫。 |
> | 動作 | Microsoft DocumentDB/databaseAccounts/mongodbDatabases/delete | 刪除 MongoDB 資料庫。 |
> |  | **databaseAccounts/mongodbDatabases/集合** |  |
> | 動作 | Microsoft DocumentDB/databaseAccounts/mongodbDatabases/集合/寫入 | 建立或更新 MongoDB 集合。 |
> | 動作 | Microsoft DocumentDB/databaseAccounts/mongodbDatabases/集合/讀取 | 讀取 MongoDB 集合或列出所有 MongoDB 集合。 |
> | 動作 | Microsoft DocumentDB/databaseAccounts/mongodbDatabases/集合/刪除 | 刪除 MongoDB 集合。 |
> |  | **databaseAccounts/mongodbDatabases/collection/operationResults** |  |
> | 動作 | Microsoft DocumentDB/databaseAccounts/mongodbDatabases/collection/operationResults/read | 讀取非同步作業的狀態。 |
> |  | **databaseAccounts/mongodbDatabases/collection/throughputSettings** |  |
> | 動作 | Microsoft DocumentDB/databaseAccounts/mongodbDatabases/集合/throughputSettings/write | 更新 MongoDB 集合輸送量。 |
> | 動作 | Microsoft DocumentDB/databaseAccounts/mongodbDatabases/collection/throughputSettings/read | 讀取 MongoDB 集合輸送量。 |
> |  | **databaseAccounts/mongodbDatabases/collection/throughputSettings/operationResults** |  |
> | 動作 | Microsoft DocumentDB/databaseAccounts/mongodbDatabases/collection/throughputSettings/operationResults/read | 讀取非同步作業的狀態。 |
> |  | **databaseAccounts/mongodbDatabases/operationResults** |  |
> | 動作 | Microsoft DocumentDB/databaseAccounts/mongodbDatabases/operationResults/read | 讀取非同步作業的狀態。 |
> |  | **databaseAccounts/mongodbDatabases/throughputSettings** |  |
> | 動作 | Microsoft DocumentDB/databaseAccounts/mongodbDatabases/throughputSettings/write | 更新 MongoDB 資料庫輸送量。 |
> | 動作 | Microsoft DocumentDB/databaseAccounts/mongodbDatabases/throughputSettings/read | 讀取 MongoDB 資料庫輸送量。 |
> |  | **databaseAccounts/mongodbDatabases/throughputSettings/operationResults** |  |
> | 動作 | Microsoft DocumentDB/databaseAccounts/mongodbDatabases/throughputSettings/operationResults/read | 讀取非同步作業的狀態。 |
> |  | **databaseAccounts/notebookWorkspaces** |  |
> | 動作 | Microsoft DocumentDB/databaseAccounts/notebookWorkspaces/write | 建立或更新筆記本工作區 |
> | 動作 | Microsoft DocumentDB/databaseAccounts/notebookWorkspaces/read | 讀取筆記本工作區 |
> | 動作 | Microsoft DocumentDB/databaseAccounts/notebookWorkspaces/delete | 刪除筆記本工作區 |
> | 動作 | Microsoft DocumentDB/databaseAccounts/notebookWorkspaces/listConnectionInfo/action | 列出筆記本工作區的連線資訊 |
> |  | **databaseAccounts/notebookWorkspaces/operationResults** |  |
> | 動作 | Microsoft DocumentDB/databaseAccounts/notebookWorkspaces/operationResults/read | 讀取筆記本工作區上非同步作業的狀態 |
> |  | **databaseAccounts/operationResults** |  |
> | 動作 | Microsoft.DocumentDB/databaseAccounts/operationResults/read | 讀取非同步作業的狀態 |
> |  | **databaseAccounts/個百分位數** |  |
> | 動作 | Microsoft.DocumentDB/databaseAccounts/percentile/read | 讀取複寫延遲的百分位數 |
> |  | **databaseAccounts/百分位數/計量** |  |
> | 動作 | Microsoft.DocumentDB/databaseAccounts/percentile/metrics/read | 讀取延遲計量 |
> |  | **databaseAccounts/百分位數/sourceRegion/targetRegion/計量** |  |
> | 動作 | Microsoft.DocumentDB/databaseAccounts/percentile/sourceRegion/targetRegion/metrics/read | 讀取指定來源和目標區域的延遲計量 |
> |  | **databaseAccounts/百分位數/targetRegion/計量** |  |
> | 動作 | Microsoft.DocumentDB/databaseAccounts/percentile/targetRegion/metrics/read | 讀取指定目標區域的延遲計量 |
> |  | **databaseAccounts/privateEndpointConnectionProxies** |  |
> | 動作 | Microsoft DocumentDB/databaseAccounts/privateEndpointConnectionProxies/read | 讀取資料庫帳戶的私人端點連接 proxy |
> | 動作 | Microsoft DocumentDB/databaseAccounts/privateEndpointConnectionProxies/write | 建立或更新資料庫帳戶的私人端點連接 proxy |
> | 動作 | Microsoft DocumentDB/databaseAccounts/privateEndpointConnectionProxies/delete | 刪除資料庫帳戶的私人端點連接 proxy |
> | 動作 | Microsoft DocumentDB/databaseAccounts/privateEndpointConnectionProxies/validate/action | 驗證資料庫帳戶的私用端點連接 proxy |
> |  | **databaseAccounts/privateEndpointConnectionProxies/operationResults** |  |
> | 動作 | Microsoft DocumentDB/databaseAccounts/privateEndpointConnectionProxies/operationResults/read | 讀取私人端點連接 proxy 非同步作業的狀態 |
> |  | **databaseAccounts/privateEndpointConnections** |  |
> | 動作 | Microsoft DocumentDB/databaseAccounts/privateEndpointConnections/read | 讀取私人端點連接，或列出資料庫帳戶的所有私人端點連接 |
> | 動作 | Microsoft DocumentDB/databaseAccounts/privateEndpointConnections/write | 建立或更新資料庫帳戶的私人端點連接 |
> | 動作 | Microsoft DocumentDB/databaseAccounts/privateEndpointConnections/delete | 刪除資料庫帳戶的私人端點連接 |
> |  | **databaseAccounts/privateEndpointConnections/operationResults** |  |
> | 動作 | Microsoft DocumentDB/databaseAccounts/privateEndpointConnections/operationResults/read | 讀取 privateEndpointConnenctions 非同步作業的狀態 |
> |  | **databaseAccounts/privateLinkResources** |  |
> | 動作 | Microsoft DocumentDB/databaseAccounts/privateLinkResources/read | 讀取私人連結資源，或列出資料庫帳戶的所有私人連結資源 |
> |  | **databaseAccounts/readonlykeys** |  |
> | 動作 | Microsoft.DocumentDB/databaseAccounts/readonlykeys/read | 讀取資料庫帳戶的唯讀金鑰。 |
> |  | **databaseAccounts/region/資料庫/集合/計量** |  |
> | 動作 | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/metrics/read | 讀取區域集合計量。 |
> |  | **databaseAccounts/region/資料庫/集合/partitionKeyRangeId/計量** |  |
> | 動作 | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitionKeyRangeId/metrics/read | 讀取區域資料庫帳戶分割區索引鍵層級計量 |
> |  | **databaseAccounts/region/資料庫/集合/資料分割** |  |
> | 動作 | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitions/read | 讀取集合中的區域資料庫帳戶分割區 |
> |  | **databaseAccounts/region/資料庫/集合/資料分割/計量** |  |
> | 動作 | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitions/metrics/read | 讀取區域資料庫帳戶分割區層級計量 |
> |  | **databaseAccounts/region/公制** |  |
> | 動作 | Microsoft.DocumentDB/databaseAccounts/region/metrics/read | 讀取區域和資料庫帳戶計量。 |
> |  | **databaseAccounts/sqlDatabases** |  |
> | 動作 | Microsoft DocumentDB/databaseAccounts/sqlDatabases/write | 建立 SQL 資料庫。 |
> | 動作 | Microsoft DocumentDB/databaseAccounts/sqlDatabases/read | 讀取 SQL 資料庫或列出所有 SQL 資料庫。 |
> | 動作 | Microsoft DocumentDB/databaseAccounts/sqlDatabases/delete | 刪除 SQL 資料庫。 |
> |  | **databaseAccounts/sqlDatabases/容器** |  |
> | 動作 | Microsoft DocumentDB/databaseAccounts/sqlDatabases/容器/寫入 | 建立或更新 SQL 容器。 |
> | 動作 | Microsoft DocumentDB/databaseAccounts/sqlDatabases/容器/讀取 | 讀取 SQL 容器或列出所有 SQL 容器。 |
> | 動作 | Microsoft DocumentDB/databaseAccounts/sqlDatabases/容器/刪除 | 刪除 SQL 容器。 |
> |  | **databaseAccounts/sqlDatabases/容器/operationResults** |  |
> | 動作 | Microsoft DocumentDB/databaseAccounts/sqlDatabases/容器/operationResults/讀取 | 讀取非同步作業的狀態。 |
> |  | **databaseAccounts/sqlDatabases/容器/storedProcedures** |  |
> | 動作 | Microsoft DocumentDB/databaseAccounts/sqlDatabases/容器/storedProcedures/write | 建立或更新 SQL 預存程式。 |
> | 動作 | Microsoft DocumentDB/databaseAccounts/sqlDatabases/容器/storedProcedures/讀取 | 讀取 SQL 預存程式或列出所有 SQL 預存程式。 |
> | 動作 | Microsoft DocumentDB/databaseAccounts/sqlDatabases/容器/storedProcedures/delete | 刪除 SQL 預存程式。 |
> |  | **databaseAccounts/sqlDatabases/容器/storedProcedures/operationResults** |  |
> | 動作 | Microsoft DocumentDB/databaseAccounts/sqlDatabases/容器/storedProcedures/operationResults/read | 讀取非同步作業的狀態。 |
> |  | **databaseAccounts/sqlDatabases/容器/throughputSettings** |  |
> | 動作 | Microsoft DocumentDB/databaseAccounts/sqlDatabases/容器/throughputSettings/write | 更新 SQL 容器輸送量。 |
> | 動作 | Microsoft DocumentDB/databaseAccounts/sqlDatabases/容器/throughputSettings/讀取 | 讀取 SQL 容器輸送量。 |
> |  | **databaseAccounts/sqlDatabases/容器/throughputSettings/operationResults** |  |
> | 動作 | Microsoft DocumentDB/databaseAccounts/sqlDatabases/容器/throughputSettings/operationResults/read | 讀取非同步作業的狀態。 |
> |  | **databaseAccounts/sqlDatabases/容器/觸發程式** |  |
> | 動作 | Microsoft DocumentDB/databaseAccounts/sqlDatabases/容器/觸發程式/寫入 | 建立或更新 SQL 觸發程式。 |
> | 動作 | Microsoft DocumentDB/databaseAccounts/sqlDatabases/容器/觸發程式/讀取 | 讀取 SQL 觸發程式或列出所有 SQL 觸發程式。 |
> | 動作 | Microsoft DocumentDB/databaseAccounts/sqlDatabases/容器/觸發程式/刪除 | 刪除 SQL 觸發程式。 |
> |  | **databaseAccounts/sqlDatabases/容器/觸發程式/operationResults** |  |
> | 動作 | Microsoft DocumentDB/databaseAccounts/sqlDatabases/容器/觸發程式/operationResults/讀取 | 讀取非同步作業的狀態。 |
> |  | **databaseAccounts/sqlDatabases/容器/userDefinedFunctions** |  |
> | 動作 | Microsoft DocumentDB/databaseAccounts/sqlDatabases/容器/userDefinedFunctions/write | 建立或更新 SQL 使用者定義函數。 |
> | 動作 | Microsoft DocumentDB/databaseAccounts/sqlDatabases/容器/userDefinedFunctions/讀取 | 讀取 SQL 使用者定義函數，或列出所有 SQL 使用者定義函數。 |
> | 動作 | Microsoft DocumentDB/databaseAccounts/sqlDatabases/容器/userDefinedFunctions/delete | 刪除 SQL 使用者定義函數。 |
> |  | **databaseAccounts/sqlDatabases/容器/userDefinedFunctions/operationResults** |  |
> | 動作 | Microsoft DocumentDB/databaseAccounts/sqlDatabases/容器/userDefinedFunctions/operationResults/read | 讀取非同步作業的狀態。 |
> |  | **databaseAccounts/sqlDatabases/operationResults** |  |
> | 動作 | Microsoft DocumentDB/databaseAccounts/sqlDatabases/operationResults/read | 讀取非同步作業的狀態。 |
> |  | **databaseAccounts/sqlDatabases/throughputSettings** |  |
> | 動作 | Microsoft DocumentDB/databaseAccounts/sqlDatabases/throughputSettings/write | 更新 SQL database 輸送量。 |
> | 動作 | Microsoft DocumentDB/databaseAccounts/sqlDatabases/throughputSettings/read | 讀取 SQL database 輸送量。 |
> |  | **databaseAccounts/sqlDatabases/throughputSettings/operationResults** |  |
> | 動作 | Microsoft DocumentDB/databaseAccounts/sqlDatabases/throughputSettings/operationResults/read | 讀取非同步作業的狀態。 |
> |  | **databaseAccounts/tables** |  |
> | 動作 | Microsoft DocumentDB/databaseAccounts/資料表/寫入 | 建立或更新資料表。 |
> | 動作 | Microsoft DocumentDB/databaseAccounts/資料表/讀取 | 讀取資料表或列出所有資料表。 |
> | 動作 | Microsoft DocumentDB/databaseAccounts/資料表/刪除 | 刪除資料表。 |
> |  | **databaseAccounts/tables/operationResults** |  |
> | 動作 | Microsoft DocumentDB/databaseAccounts/tables/operationResults/read | 讀取非同步作業的狀態。 |
> |  | **databaseAccounts/tables/throughputSettings** |  |
> | 動作 | Microsoft DocumentDB/databaseAccounts/資料表/throughputSettings/write | 更新資料表輸送量。 |
> | 動作 | Microsoft DocumentDB/databaseAccounts/tables/throughputSettings/read | 讀取資料表輸送量。 |
> |  | **databaseAccounts/tables/throughputSettings/operationResults** |  |
> | 動作 | Microsoft DocumentDB/databaseAccounts/tables/throughputSettings/operationResults/read | 讀取非同步作業的狀態。 |
> |  | **databaseAccounts/使用方式** |  |
> | 動作 | Microsoft.DocumentDB/databaseAccounts/usages/read | 讀取資料庫帳戶的使用量。 |
> |  | **堆放** |  |
> | 動作 | Microsoft.DocumentDB/locations/deleteVirtualNetworkOrSubnets/action | 通知 Microsoft.DocumentDB 正在刪除虛擬網路或子網路 |
> |  | **位置/deleteVirtualNetworkOrSubnets/operationResults** |  |
> | 動作 | Microsoft.DocumentDB/locations/deleteVirtualNetworkOrSubnets/operationResults/read | 讀取 deleteVirtualNetworkOrSubnets 非同步作業的狀態 |
> |  | **位置/operationsStatus** |  |
> | 動作 | Microsoft DocumentDB/位置/operationsStatus/讀取 | 讀取非同步作業的狀態 |
> |  | **operationResults** |  |
> | 動作 | Microsoft.DocumentDB/operationResults/read | 讀取非同步作業的狀態 |
> |  | **業務** |  |
> | 動作 | Microsoft.DocumentDB/operations/read | 讀取可供 Microsoft DocumentDB 使用的作業  |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

Azure 服務： [App Service](../app-service/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.DomainRegistration/generateSsoRequest/Action | 產生要求以便登入網域控制中心。 |
> | 動作 | Microsoft.DomainRegistration/validateDomainRegistrationInformation/Action | 驗證網域購買物件，但不將其提交出去 |
> | 動作 | Microsoft.DomainRegistration/checkDomainAvailability/Action | 檢查網域是否可供購買 |
> | 動作 | Microsoft.DomainRegistration/listDomainRecommendations/Action | 根據關鍵字來擷取清單網域建議 |
> | 動作 | Microsoft.DomainRegistration/register/action | 針對訂用帳戶註冊 Microsoft 網域資源提供者 |
> |  | **林中** |  |
> | 動作 | Microsoft.DomainRegistration/domains/Read | 取得網域清單 |
> | 動作 | Microsoft.DomainRegistration/domains/Read | 取得網域 |
> | 動作 | Microsoft.DomainRegistration/domains/Write | 新增網域或更新現有網域 |
> | 動作 | Microsoft.DomainRegistration/domains/Delete | 刪除現有網域。 |
> | 動作 | Microsoft.DomainRegistration/domains/renew/Action | 更新現有網域。 |
> |  | **網域/domainownershipidentifiers** |  |
> | 動作 | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Read | 列出擁有權識別碼 |
> | 動作 | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Read | 取得擁有權識別碼 |
> | 動作 | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Write | 建立或更新識別碼 |
> | 動作 | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Delete | 刪除擁有權識別碼 |
> |  | **網域/operationresults** |  |
> | 動作 | Microsoft.DomainRegistration/domains/operationresults/Read | 取得網域作業 |
> |  | **業務** |  |
> | 動作 | Microsoft.DomainRegistration/operations/Read | 列出 App Service 網域註冊的所有作業 |
> |  | **topLevelDomains** |  |
> | 動作 | Microsoft.DomainRegistration/topLevelDomains/Read | 取得最上層網域 |
> | 動作 | Microsoft.DomainRegistration/topLevelDomains/Read | 取得最上層網域 |
> | 動作 | Microsoft.DomainRegistration/topLevelDomains/listAgreements/Action | 列出合約動作 |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

Azure 服務：[事件方格](../event-grid/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.EventGrid/register/action | 為 EventGrid 資源提供者註冊訂用帳戶。 |
> | 動作 | Microsoft.EventGrid/unregister/action | 為 EventGrid 資源提供者取消註冊訂用帳戶。 |
> |  | **林中** |  |
> | 動作 | Microsoft.EventGrid/domains/write | 建立或更新網域 |
> | 動作 | Microsoft.EventGrid/domains/read | 讀取網域 |
> | 動作 | Microsoft.EventGrid/domains/delete | 刪除網域 |
> | 動作 | Microsoft.EventGrid/domains/listKeys/action | 列出網域的金鑰 |
> | 動作 | Microsoft.EventGrid/domains/regenerateKey/action | 重新產生網域的金鑰 |
> |  | **網域/privateEndpointConnectionProxies** |  |
> | 動作 | EventGrid/網域/privateEndpointConnectionProxies/驗證/動作 | 驗證網域的 PrivateEndpointConnectionProxies |
> | 動作 | EventGrid/網域/privateEndpointConnectionProxies/read | 讀取網域的 PrivateEndpointConnectionProxies |
> | 動作 | EventGrid/網域/privateEndpointConnectionProxies/write | 寫入網域的 PrivateEndpointConnectionProxies |
> | 動作 | EventGrid/網域/privateEndpointConnectionProxies/delete | 刪除網域的 PrivateEndpointConnectionProxies |
> |  | **網域/privateEndpointConnections** |  |
> | 動作 | EventGrid/網域/privateEndpointConnections/read | 讀取網域的 PrivateEndpointConnections |
> | 動作 | EventGrid/網域/privateEndpointConnections/write | 寫入網域的 PrivateEndpointConnections |
> | 動作 | EventGrid/網域/privateEndpointConnections/delete | 刪除網域的 PrivateEndpointConnections |
> |  | **網域/privateLinkResources** |  |
> | 動作 | EventGrid/網域/privateLinkResources/read | 取得或列出網域的 PrivateLinkResources |
> |  | **網域/提供者/Microsoft Insights/logDefinitions** |  |
> | 動作 | EventGrid/網域/提供者/Microsoft Insights/logDefinitions/read | 允許存取診斷記錄 |
> |  | **網域/提供者/Microsoft Insights/Metricdefinitions.listasync** |  |
> | 動作 | Microsoft.EventGrid/domains/providers/Microsoft.Insights/metricDefinitions/read | 取得網域的可用計量 |
> |  | **網域/主題** |  |
> | 動作 | Microsoft.EventGrid/domains/topics/read | 讀取網域主題 |
> | 動作 | EventGrid/網域/主題/寫入 | 建立或更新網域主題 |
> | 動作 | EventGrid/網域/主題/delete | 刪除網域主題 |
> |  | **eventSubscriptions** |  |
> | 動作 | Microsoft.EventGrid/eventSubscriptions/write | 建立或更新 eventSubscription |
> | 動作 | Microsoft.EventGrid/eventSubscriptions/read | 閱讀 eventSubscription |
> | 動作 | Microsoft.EventGrid/eventSubscriptions/delete | 刪除 eventSubscription |
> | 動作 | Microsoft.EventGrid/eventSubscriptions/getFullUrl/action | 取得事件訂閱的完整 URL |
> |  | **eventSubscriptions/providers/Microsoft Insights/diagnosticSettings** |  |
> | 動作 | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/read | 取得事件訂閱的診斷設定 |
> | 動作 | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/write | 建立或更新事件訂閱的診斷設定 |
> |  | **eventSubscriptions/providers/Microsoft Insights/Metricdefinitions.listasync** |  |
> | 動作 | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/metricDefinitions/read | 取得 eventSubscriptions 的可用計量 |
> |  | **extensionTopics** |  |
> | 動作 | Microsoft.EventGrid/extensionTopics/read | 度取 extensionTopic。 |
> |  | **extensionTopics/providers/Microsoft Insights/diagnosticSettings** |  |
> | 動作 | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/read | 取得主題的診斷設定 |
> | 動作 | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/write | 建立或更新主題的診斷設定 |
> |  | **extensionTopics/providers/Microsoft Insights/Metricdefinitions.listasync** |  |
> | 動作 | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/metricDefinitions/read | 取得主題的可用計量 |
> |  | **位置/eventSubscriptions** |  |
> | 動作 | Microsoft.EventGrid/locations/eventSubscriptions/read | 列出區域事件訂用帳戶 |
> |  | **位置/operationResults** |  |
> | 動作 | Microsoft.EventGrid/locations/operationResults/read | 讀取區域作業的結果 |
> |  | **位置/operationsStatus** |  |
> | 動作 | Microsoft.EventGrid/locations/operationsStatus/read | 讀取區域作業的狀態 |
> |  | **位置/topictypes/eventSubscriptions** |  |
> | 動作 | Microsoft.EventGrid/locations/topictypes/eventSubscriptions/read | 依主題類型列出區域事件訂用帳戶 |
> |  | **operationResults** |  |
> | 動作 | Microsoft.EventGrid/operationResults/read | 讀取作業的結果 |
> |  | **業務** |  |
> | 動作 | Microsoft.EventGrid/operations/read | 列出 EventGrid 作業。 |
> |  | **operationsStatus** |  |
> | 動作 | Microsoft.EventGrid/operationsStatus/read | 讀取作業的狀態 |
> |  | **partnerNamespaces** |  |
> | 動作 | EventGrid/partnerNamespaces/write | 建立或更新合作夥伴命名空間 |
> | 動作 | EventGrid/partnerNamespaces/read | 讀取合作夥伴命名空間 |
> | 動作 | EventGrid/partnerNamespaces/delete | 刪除合作夥伴命名空間 |
> | 動作 | EventGrid/partnerNamespaces/listKeys/action | 列出合作夥伴命名空間的金鑰 |
> | 動作 | EventGrid/partnerNamespaces/regenerateKey/action | 重新產生夥伴命名空間的金鑰 |
> |  | **partnerNamespaces/eventChannels** |  |
> | 動作 | EventGrid/partnerNamespaces/eventChannels/read | 讀取事件通道 |
> | 動作 | EventGrid/partnerNamespaces/eventChannels/write | 建立或更新事件通道 |
> | 動作 | EventGrid/partnerNamespaces/eventChannels/delete | 刪除事件通道 |
> |  | **partnerRegistrations** |  |
> | 動作 | EventGrid/partnerRegistrations/write | 建立或更新合作夥伴註冊 |
> | 動作 | EventGrid/partnerRegistrations/read | 閱讀合作夥伴註冊 |
> | 動作 | EventGrid/partnerRegistrations/delete | 刪除合作夥伴註冊 |
> |  | **partnerTopics** |  |
> | 動作 | EventGrid/partnerTopics/read | 閱讀合作夥伴主題 |
> | 動作 | EventGrid/partnerTopics/write | 建立或更新合作夥伴主題 |
> | 動作 | EventGrid/partnerTopics/delete | 刪除合作夥伴主題 |
> | 動作 | EventGrid/partnerTopics/啟動/動作 | 啟用合作夥伴主題 |
> | 動作 | EventGrid/partnerTopics/停用/動作 | 停用合作夥伴主題 |
> |  | **sku** |  |
> | 動作 | EventGrid/sku/讀取 | 讀取事件方格資源的可用 Sku 定義 |
> |  | **systemTopics** |  |
> | 動作 | EventGrid/systemTopics/read | 閱讀系統主題 |
> | 動作 | EventGrid/systemTopics/write | 建立或更新系統主題 |
> | 動作 | EventGrid/systemTopics/delete | 刪除系統主題 |
> |  | **systemTopics/providers/Microsoft Insights/diagnosticSettings** |  |
> | 動作 | EventGrid/systemTopics/provider/Microsoft Insights/diagnosticSettings/read | 取得系統主題的診斷設定 |
> | 動作 | EventGrid/systemTopics/provider/Microsoft Insights/diagnosticSettings/write | 建立或更新系統主題的診斷設定 |
> |  | **systemTopics/providers/Microsoft Insights/logDefinitions** |  |
> | 動作 | EventGrid/systemTopics/provider/Microsoft Insights/logDefinitions/read | 允許存取診斷記錄 |
> |  | **systemTopics/providers/Microsoft Insights/Metricdefinitions.listasync** |  |
> | 動作 | EventGrid/systemTopics/provider/Microsoft Insights/Metricdefinitions.listasync/read | 取得系統主題的可用計量 |
> |  | **主題** |  |
> | 動作 | Microsoft.EventGrid/topics/write | 建立或更新主題 |
> | 動作 | Microsoft.EventGrid/topics/read | 讀取主題 |
> | 動作 | Microsoft.EventGrid/topics/delete | 刪除主題 |
> | 動作 | Microsoft.EventGrid/topics/listKeys/action | 列出主題的金鑰 |
> | 動作 | Microsoft.EventGrid/topics/regenerateKey/action | 重新產生主題的金鑰 |
> |  | **主題/privateEndpointConnectionProxies** |  |
> | 動作 | EventGrid/主題/privateEndpointConnectionProxies/validate/action | 驗證主題的 PrivateEndpointConnectionProxies |
> | 動作 | EventGrid/主題/privateEndpointConnectionProxies/read | 閱讀主題的 PrivateEndpointConnectionProxies |
> | 動作 | EventGrid/主題/privateEndpointConnectionProxies/write | 撰寫主題的 PrivateEndpointConnectionProxies |
> | 動作 | EventGrid/主題/privateEndpointConnectionProxies/delete | 刪除主題的 PrivateEndpointConnectionProxies |
> |  | **主題/privateEndpointConnections** |  |
> | 動作 | EventGrid/主題/privateEndpointConnections/read | 閱讀主題的 PrivateEndpointConnections |
> | 動作 | EventGrid/主題/privateEndpointConnections/write | 撰寫主題的 PrivateEndpointConnections |
> | 動作 | EventGrid/主題/privateEndpointConnections/delete | 刪除主題的 PrivateEndpointConnections |
> |  | **主題/privateLinkResources** |  |
> | 動作 | EventGrid/主題/privateLinkResources/read | 閱讀主題的 PrivateLinkResources |
> |  | **主題/提供者/Microsoft Insights/diagnosticSettings** |  |
> | 動作 | Microsoft.EventGrid/topics/providers/Microsoft.Insights/diagnosticSettings/read | 取得主題的診斷設定 |
> | 動作 | Microsoft.EventGrid/topics/providers/Microsoft.Insights/diagnosticSettings/write | 建立或更新主題的診斷設定 |
> |  | **主題/提供者/Microsoft Insights/logDefinitions** |  |
> | 動作 | EventGrid/主題/提供者/Microsoft Insights/logDefinitions/read | 允許存取診斷記錄 |
> |  | **主題/提供者/Microsoft Insights/Metricdefinitions.listasync** |  |
> | 動作 | Microsoft.EventGrid/topics/providers/Microsoft.Insights/metricDefinitions/read | 取得主題的可用計量 |
> |  | **topictypes** |  |
> | 動作 | Microsoft.EventGrid/topictypes/read | 讀取 topictype |
> |  | **topictypes/eventSubscriptions** |  |
> | 動作 | Microsoft.EventGrid/topictypes/eventSubscriptions/read | 依主題類型列出全域事件訂用帳戶 |
> |  | **topictypes/eventtypes** |  |
> | 動作 | Microsoft.EventGrid/topictypes/eventtypes/read | 讀取 topictype 支援的 eventtypes |

## <a name="microsofteventhub"></a>Microsoft.EventHub

Azure 服務：[事件中樞](../event-hubs/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.EventHub/checkNamespaceAvailability/action | 檢查給定訂用帳戶下的命名空間是否可用。 此 API 即將淘汰。請改用 CheckNameAvailabiltiy。 |
> | 動作 | Microsoft.EventHub/checkNameAvailability/action | 檢查給定訂用帳戶下的命名空間是否可用。 |
> | 動作 | Microsoft.EventHub/register/action | 針對 EventHub 資源提供者註冊訂用帳戶，並讓您能夠建立 EventHub 資源 |
> | 動作 | Microsoft.EventHub/unregister/action | 註冊 EventHub 資源提供者 |
> |  | **availableClusterRegions** |  |
> | 動作 | Microsoft EventHub/availableClusterRegions/read | 讀取作業，以列出 Azure 區域可用的預先布建叢集。 |
> |  | **叢集** |  |
> | 動作 | Microsoft.EventHub/clusters/read | 取得叢集資源描述 |
> | 動作 | Microsoft.EventHub/clusters/write | 建立或修改現有的叢集資源。 |
> | 動作 | Microsoft EventHub/叢集/刪除 | 刪除現有的叢集資源。 |
> |  | **群集/命名空間** |  |
> | 動作 | Microsoft EventHub/叢集/命名空間/讀取 | 列出叢集中的命名空間 Azure Resource Manager 識別碼。 |
> |  | **叢集/operationresults** |  |
> | 動作 | Microsoft EventHub/叢集/operationresults/read | 取得非同步叢集操作的狀態。 |
> |  | **叢集/提供者/Microsoft Insights/Metricdefinitions.listasync** |  |
> | 動作 | Microsoft.EventHub/clusters/providers/Microsoft.Insights/metricDefinitions/read | 取得叢集計量資源描述的清單 |
> |  | **堆放** |  |
> | 動作 | Microsoft.EventHub/locations/deleteVirtualNetworkOrSubnets/action | 針對指定的 VNet，刪除 EventHub 資源提供者中的 VNet 規則 |
> |  | **命名空間** |  |
> | 動作 | Microsoft.EventHub/namespaces/write | 建立命名空間資源，並更新其屬性。 命名空間的標記和容量是可以更新的屬性。 |
> | 動作 | Microsoft.EventHub/namespaces/read | 取得命名空間資源描述的清單 |
> | 動作 | Microsoft.EventHub/namespaces/Delete | 刪除命名空間資源 |
> | 動作 | Microsoft.EventHub/namespaces/authorizationRules/action | 更新命名空間授權規則。 此 API 即將淘汰。 請改用 PUT 呼叫來更新命名空間授權規則。 API 版本 2017-04-01 不支援此作業。 |
> | 動作 | Microsoft.EventHub/namespaces/removeAcsNamepsace/action | 移除 ACS 命名空間 |
> |  | **命名空間/authorizationRules** |  |
> | 動作 | Microsoft.EventHub/namespaces/authorizationRules/read | 取得命名空間授權規則描述的清單。 |
> | 動作 | Microsoft.EventHub/namespaces/authorizationRules/write | 建立命名空間層級的授權規則，並更新其屬性。 您可以更新授權規則的存取權限、主要金鑰和次要金鑰。 |
> | 動作 | Microsoft.EventHub/namespaces/authorizationRules/delete | 刪除命名空間授權規則。 您無法刪除預設的命名空間授權規則。  |
> | 動作 | Microsoft.EventHub/namespaces/authorizationRules/listkeys/action | 取得命名空間的連接字串 |
> | 動作 | Microsoft.EventHub/namespaces/authorizationRules/regenerateKeys/action | 對資源重新產生主要或次要金鑰 |
> |  | **命名空間/disasterrecoveryconfigs** |  |
> | 動作 | Microsoft.EventHub/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | 檢查指定訂用帳戶下命名空間別名的可用性。 |
> | 動作 | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/write | 建立或更新與命名空間相關聯的災害復原設定。 |
> | 動作 | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/read | 取得與命名空間相關聯的災害復原設定。 |
> | 動作 | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/delete | 刪除與命名空間相關聯的災害復原設定。 此作業只能透過主要命名空間叫用。 |
> | 動作 | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/breakPairing/action | 停用災害復原，並停止將變更從主要命名空間複寫至次要命名空間。 |
> | 動作 | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/failover/action | 叫用異地災害復原容錯移轉，並將命名空間別名重新設定為指向次要命名空間。 |
> |  | **命名空間/disasterRecoveryConfigs/authorizationRules** |  |
> | 動作 | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/read | 取得災害復原主要命名空間的授權規則 |
> | 動作 | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | 取得災害復原主要命名空間的授權規則金鑰 |
> |  | **命名空間/eventhubs** |  |
> | 動作 | Microsoft.EventHub/namespaces/eventhubs/write | 建立或更新 EventHub 屬性。 |
> | 動作 | Microsoft.EventHub/namespaces/eventhubs/read | 取得 EventHub 資源描述的清單 |
> | 動作 | Microsoft.EventHub/namespaces/eventhubs/Delete | 用來刪除 EventHub 資源的作業 |
> | 動作 | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/action | 更新 EventHub 的作業。 API 版本 2017-04-01 不支援此作業。 授權規則。 請使用 PUT 呼叫來更新授權規則。 |
> |  | **命名空間/eventhubs/authorizationRules** |  |
> | 動作 | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/read |  取得 EventHub 授權規則的清單 |
> | 動作 | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/write | 建立 EventHub 授權規則，並更新其屬性。 您可以更新授權規則存取權限。 |
> | 動作 | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/delete | 用來刪除 EventHub 授權規則的作業 |
> | 動作 | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/listkeys/action | 取得 EventHub 的連接字串 |
> | 動作 | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/regenerateKeys/action | 對資源重新產生主要或次要金鑰 |
> |  | **命名空間/eventHubs/consumergroups** |  |
> | 動作 | Microsoft.EventHub/namespaces/eventHubs/consumergroups/write | 建立或更新 ConsumerGroup 屬性。 |
> | 動作 | Microsoft.EventHub/namespaces/eventHubs/consumergroups/read | 取得 ConsumerGroup 資源描述的清單 |
> | 動作 | Microsoft.EventHub/namespaces/eventHubs/consumergroups/Delete | 用來刪除 ConsumerGroup 資源的作業 |
> |  | **命名空間/ipFilterRules** |  |
> | 動作 | Microsoft.EventHub/namespaces/ipFilterRules/read | 取得 IP 篩選資源 |
> | 動作 | Microsoft.EventHub/namespaces/ipFilterRules/write | 建立 IP 篩選資源 |
> | 動作 | Microsoft.EventHub/namespaces/ipFilterRules/delete | 刪除 IP 篩選資源 |
> |  | **命名空間/messagingPlan** |  |
> | 動作 | Microsoft.EventHub/namespaces/messagingPlan/read | 取得命名空間的傳訊方案。<br>此 API 即將淘汰。<br>透過 MessagingPlan 資源公開的屬性，已移至新版 API 的 (父系) 命名空間資源。<br>API 版本 2017-04-01 不支援此作業。 |
> | 動作 | Microsoft.EventHub/namespaces/messagingPlan/write | 更新命名空間的傳訊方案。<br>此 API 即將淘汰。<br>透過 MessagingPlan 資源公開的屬性，已移至新版 API 的 (父系) 命名空間資源。<br>API 版本 2017-04-01 不支援此作業。 |
> |  | **命名空間/networkruleset** |  |
> | 動作 | Microsoft. EventHub/命名空間/networkruleset/read | 取得 NetworkRuleSet 資源 |
> | 動作 | Microsoft. EventHub/命名空間/networkruleset/write | 建立 VNET 規則資源 |
> | 動作 | Microsoft. EventHub/命名空間/networkruleset/delete | 刪除 VNET 規則資源 |
> |  | **命名空間/networkrulesets** |  |
> | 動作 | Microsoft. EventHub/命名空間/networkrulesets/read | 取得 NetworkRuleSet 資源 |
> | 動作 | Microsoft. EventHub/命名空間/networkrulesets/write | 建立 VNET 規則資源 |
> | 動作 | Microsoft. EventHub/命名空間/networkrulesets/delete | 刪除 VNET 規則資源 |
> |  | **命名空間/operationresults** |  |
> | 動作 | Microsoft.EventHub/namespaces/operationresults/read | 取得命名空間作業的狀態 |
> |  | **命名空間/privateEndpointConnectionProxies** |  |
> | 動作 | Microsoft. EventHub/命名空間/privateEndpointConnectionProxies/驗證/動作 | 驗證私用端點連接 Proxy |
> | 動作 | Microsoft. EventHub/命名空間/privateEndpointConnectionProxies/read | 取得私人端點連接 Proxy |
> | 動作 | Microsoft. EventHub/命名空間/privateEndpointConnectionProxies/write | 建立私用端點連接 Proxy |
> | 動作 | Microsoft. EventHub/命名空間/privateEndpointConnectionProxies/delete | 刪除私人端點連接 Proxy |
> |  | **命名空間/提供者/Microsoft Insights/diagnosticSettings** |  |
> | 動作 | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/diagnosticSettings/read | 取得命名空間診斷設定資源描述的清單 |
> | 動作 | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/diagnosticSettings/write | 取得命名空間診斷設定資源描述的清單 |
> |  | **命名空間/提供者/Microsoft Insights/logDefinitions** |  |
> | 動作 | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/logDefinitions/read | 取得命名空間記錄資源描述的清單 |
> |  | **命名空間/提供者/Microsoft Insights/Metricdefinitions.listasync** |  |
> | 動作 | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/metricDefinitions/read | 取得命名空間計量資源描述的清單 |
> |  | **命名空間/virtualNetworkRules** |  |
> | 動作 | Microsoft.EventHub/namespaces/virtualNetworkRules/read | 取得 VNET 規則資源 |
> | 動作 | Microsoft.EventHub/namespaces/virtualNetworkRules/write | 建立 VNET 規則資源 |
> | 動作 | Microsoft.EventHub/namespaces/virtualNetworkRules/delete | 刪除 VNET 規則資源 |
> |  | **業務** |  |
> | 動作 | Microsoft.EventHub/operations/read | 取得作業 |
> |  | **sku** |  |
> | 動作 | Microsoft.EventHub/sku/read | 取得 SKU 資源描述的清單 |
> |  | **sku/區域** |  |
> | 動作 | Microsoft.EventHub/sku/regions/read | 取得 SkuRegions 資源描述的清單 |
> |  | **命名空間/訊息** |  |
> | DataAction | Microsoft. EventHub/命名空間/訊息/傳送/動作 | 傳送訊息 |
> | DataAction | Microsoft. EventHub/命名空間/訊息/接收/動作 | 接收訊息 |

## <a name="microsoftfeatures"></a>Microsoft.Features

Azure 服務： [Azure Resource Manager](../azure-resource-manager/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.Features/register/action | 註冊訂用帳戶的功能。 |
> |  | **features** |  |
> | 動作 | Microsoft.Features/features/read | 取得訂用帳戶的功能。 |
> |  | **業務** |  |
> | 動作 | Microsoft.Features/operations/read | 取得作業的清單。 |
> |  | **提供者/功能** |  |
> | 動作 | Microsoft.Features/providers/features/read | 取得給定資源提供者中某個訂用帳戶的功能。 |
> | 動作 | Microsoft.Features/providers/features/register/action | 註冊給定資源提供者中某個訂用帳戶的功能。 |
> | 動作 | Microsoft.Features/providers/features/unregister/action | 在指定的資源提供者內取消註冊訂用帳戶的功能。 |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

Azure 服務： [Azure 原則](../governance/policy/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | GuestConfiguration/register/action | 為 GuestConfiguration 資源提供者註冊訂用帳戶。 |
> |  | **guestConfigurationAssignments** |  |
> | 動作 | Microsoft.GuestConfiguration/guestConfigurationAssignments/write | 取得新的來賓組態指派。 |
> | 動作 | Microsoft.GuestConfiguration/guestConfigurationAssignments/read | 取得來賓組態指派。 |
> | 動作 | GuestConfiguration/guestConfigurationAssignments/delete | 刪除來賓設定指派。 |
> |  | **guestConfigurationAssignments/報表** |  |
> | 動作 | Microsoft.GuestConfiguration/guestConfigurationAssignments/reports/read | 取得來賓組態指派報告。 |
> |  | **業務** |  |
> | 動作 | GuestConfiguration/作業/讀取 | 取得 GuestConfiguration 資源提供者的作業 |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

Azure 服務： [HDInsight](../hdinsight/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft HDInsight/註冊/動作 | 為訂用帳戶註冊 HDInsight 資源提供者 |
> | 動作 | Microsoft HDInsight/取消註冊/動作 | 取消註冊訂用帳戶的 HDInsight 資源提供者 |
> |  | **叢集** |  |
> | 動作 | Microsoft.HDInsight/clusters/write | 建立或更新 HDInsight 叢集 |
> | 動作 | Microsoft.HDInsight/clusters/read | 取得 HDInsight 叢集的相關詳細資料 |
> | 動作 | Microsoft.HDInsight/clusters/delete | 刪除 HDInsight 叢集 |
> | 動作 | Microsoft.HDInsight/clusters/changerdpsetting/action | 變更 HDInsight 叢集的 RDP 設定 |
> | 動作 | Microsoft HDInsight/叢集/getGatewaySettings/動作 | 取得 HDInsight 叢集的閘道設定 |
> | 動作 | Microsoft HDInsight/叢集/updateGatewaySettings/動作 | 更新 HDInsight 叢集的閘道設定 |
> | 動作 | Microsoft.HDInsight/clusters/configurations/action | 取得 HDInsight 叢集組態 |
> |  | **叢集/應用程式** |  |
> | 動作 | Microsoft.HDInsight/clusters/applications/read | 取得 HDInsight 叢集的應用程式 |
> | 動作 | Microsoft.HDInsight/clusters/applications/write | 建立或更新 HDInsight 叢集的應用程式 |
> | 動作 | Microsoft.HDInsight/clusters/applications/delete | 刪除 HDInsight 叢集的應用程式 |
> |  | **叢集/設定** |  |
> | 動作 | Microsoft.HDInsight/clusters/configurations/read | 取得 HDInsight 叢集組態 |
> |  | **群集/擴充功能** |  |
> | 動作 | Microsoft HDInsight/叢集/擴充功能/寫入 | 建立適用于 HDInsight 叢集的叢集擴充功能 |
> | 動作 | Microsoft HDInsight/叢集/擴充功能/讀取 | 取得 HDInsight 叢集的叢集擴充功能 |
> | 動作 | Microsoft HDInsight/叢集/擴充功能/刪除 | 刪除 HDInsight 叢集的叢集擴充功能 |
> |  | **叢集/提供者/Microsoft Insights/diagnosticSettings** |  |
> | 動作 | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/diagnosticSettings/read | 取得資源 HDInsight 叢集的診斷設定 |
> | 動作 | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/diagnosticSettings/write | 建立或更新資源 HDInsight 叢集的診斷設定 |
> |  | **叢集/提供者/Microsoft Insights/Metricdefinitions.listasync** |  |
> | 動作 | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/metricDefinitions/read | 取得 HDInsight 叢集的可用計量 |
> |  | **群集/角色** |  |
> | 動作 | Microsoft.HDInsight/clusters/roles/resize/action | 調整 HDInsight 叢集的大小 |
> |  | **位置/功能** |  |
> | 動作 | Microsoft.HDInsight/locations/capabilities/read | 取得訂用帳戶功能 |
> |  | **位置/checkNameAvailability** |  |
> | 動作 | Microsoft.HDInsight/locations/checkNameAvailability/read | 檢查名稱可用性 |

## <a name="microsofthybridcompute"></a>HybridCompute

Azure 服務： [Azure Arc](../azure-arc/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | HybridCompute/register/action | 為 HybridCompute 資源提供者註冊訂用帳戶 |
> | 動作 | HybridCompute/取消註冊/動作 | 取消註冊 HybridCompute 資源提供者的訂用帳戶 |
> |  | **位置/operationresults** |  |
> | 動作 | HybridCompute/位置/operationresults/讀取 | 讀取 HybridCompute 資源提供者上的作業狀態 |
> |  | **機時** |  |
> | 動作 | HybridCompute/機器/讀取 | 讀取任何 Azure Arc 機器 |
> | 動作 | HybridCompute/機器/寫入 | 寫入 Azure Arc 機器 |
> | 動作 | HybridCompute/機器/刪除 | 刪除 Azure Arc 機器 |
> | 動作 | HybridCompute/電腦/重新連接/動作 | 重新連接 Azure Arc 機器 |
> |  | **機器/擴充功能** |  |
> | 動作 | HybridCompute/電腦/擴充功能/讀取 | 讀取任何 Azure Arc 延伸模組 |
> | 動作 | HybridCompute/機器/擴充功能/寫入 | 安裝或更新 Azure Arc 延伸模組 |
> | 動作 | HybridCompute/電腦/擴充功能/刪除 | 刪除 Azure Arc 延伸模組 |
> |  | **業務** |  |
> | 動作 | HybridCompute/作業/讀取 | 讀取適用于伺服器的 Azure Arc 所有作業 |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

Azure 服務： [azure 匯入/匯出](../storage/common/storage-import-export-service.md)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.ImportExport/register/action | 針對匯入/匯出資源提供者註冊訂用帳戶，並讓您能夠建立匯入/匯出作業。 |
> |  | **jobs** |  |
> | 動作 | Microsoft.ImportExport/jobs/write | 使用指定參數建立作業，或更新指定作業的屬性或標記。 |
> | 動作 | Microsoft.ImportExport/jobs/read | 取得指定作業的屬性，或傳回作業清單。 |
> | 動作 | Microsoft.ImportExport/jobs/listBitLockerKeys/action | 取得指定作業的 BitLocker 金鑰。 |
> | 動作 | Microsoft.ImportExport/jobs/delete | 刪除現有作業。 |
> |  | **堆放** |  |
> | 動作 | Microsoft.ImportExport/locations/read | 取得指定位置的屬性，或傳回位置清單。 |
> |  | **業務** |  |
> | 動作 | ImportExport/作業/讀取 | 取得資源提供者所支援的作業。 |

## <a name="microsoftinsights"></a>Microsoft.Insights

Azure 服務： [Azure 監視器](../azure-monitor/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.Insights/Metrics/Action | 計量動作 |
> | 動作 | Microsoft.Insights/Register/Action | 註冊 Microsoft Insights 提供者 |
> | 動作 | Microsoft.Insights/Unregister/Action | 註冊 Microsoft Insights 提供者 |
> | 動作 | Microsoft.Insights/ListMigrationDate/Action | 取回訂用帳戶移轉日期 |
> | 動作 | Microsoft.Insights/MigrateToNewpricingModel/Action | 將訂用帳戶移轉至新的計價模式 |
> | 動作 | Microsoft.Insights/RollbackToLegacyPricingModel/Action | 將訂用帳戶復原至舊版計價模式 |
> |  | **ActionGroups** |  |
> | 動作 | Microsoft.Insights/ActionGroups/Write | 建立或更新動作群組 |
> | 動作 | Microsoft.Insights/ActionGroups/Delete | 刪除動作群組 |
> | 動作 | Microsoft.Insights/ActionGroups/Read | 讀取動作群組 |
> |  | **ActivityLogAlerts** |  |
> | 動作 | Microsoft.Insights/ActivityLogAlerts/Write | 建立或更新活動記錄警示 |
> | 動作 | Microsoft.Insights/ActivityLogAlerts/Delete | 刪除活動記錄警示 |
> | 動作 | Microsoft.Insights/ActivityLogAlerts/Read | 讀取活動記錄警示 |
> | 動作 | Microsoft.Insights/ActivityLogAlerts/Activated/Action | 活動記錄警示已啟用 |
> |  | **AlertRules** |  |
> | 動作 | Microsoft.Insights/AlertRules/Write | 建立或更新傳統計量警示 |
> | 動作 | Microsoft.Insights/AlertRules/Delete | 刪除傳統計量警示 |
> | 動作 | Microsoft.Insights/AlertRules/Read | 讀取傳統計量警示 |
> | 動作 | Microsoft.Insights/AlertRules/Activated/Action | 傳統計量警示已啟用 |
> | 動作 | Microsoft.Insights/AlertRules/Resolved/Action | 傳統計量警示已解決 |
> | 動作 | Microsoft.Insights/AlertRules/Throttled/Action | 傳統計量警示規則已節流 |
> |  | **AlertRules/事件** |  |
> | 動作 | Microsoft.Insights/AlertRules/Incidents/Read | 讀取傳統計量警示事件 |
> |  | **AutoscaleSettings** |  |
> | 動作 | Microsoft.Insights/AutoscaleSettings/Write | 建立或更新自動調整設定 |
> | 動作 | Microsoft.Insights/AutoscaleSettings/Delete | 刪除自動調整設定 |
> | 動作 | Microsoft.Insights/AutoscaleSettings/Read | 讀取自動調整設定 |
> | 動作 | Microsoft.Insights/AutoscaleSettings/Scaleup/Action | 自動調整相應增加已起始 |
> | 動作 | Microsoft.Insights/AutoscaleSettings/Scaledown/Action | 自動調整相應減少已起始 |
> | 動作 | Microsoft.Insights/AutoscaleSettings/ScaleupResult/Action | 自動調整相應增加已完成 |
> | 動作 | Microsoft.Insights/AutoscaleSettings/ScaledownResult/Action | 自動調整相應減少已完成 |
> |  | **AutoscaleSettings/providers/Microsoft Insights/diagnosticSettings** |  |
> | 動作 | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/diagnosticSettings/Read | 讀取資源診斷設定 |
> | 動作 | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/diagnosticSettings/Write | 建立或更新資源診斷設定 |
> |  | **AutoscaleSettings/providers/Microsoft Insights/logDefinitions** |  |
> | 動作 | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/logDefinitions/Read | 讀取記錄定義 |
> |  | **AutoscaleSettings/providers/Microsoft Insights/Metricdefinitions.listasync** |  |
> | 動作 | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/MetricDefinitions/Read | 讀取計量定義 |
> |  | **底線** |  |
> | 動作 | Microsoft Insights/基準/讀取 | 讀取度量基準（預覽） |
> |  | **CalculateBaseline** |  |
> | 動作 | Microsoft Insights/CalculateBaseline/Read | 計算度量值的基準（預覽） |
> |  | **Components** |  |
> | 動作 | Microsoft.Insights/Components/AnalyticsTables/Action | Application Insights 分析資料表動作 |
> | 動作 | Microsoft.Insights/Components/ApiKeys/Action | 產生 Application Insights API 金鑰 |
> | 動作 | Microsoft.Insights/Components/Purge/Action | 從 Application Insights 清除資料 |
> | 動作 | Microsoft Insights/Components/DailyCapReached/Action | 已達到 Application Insights 元件的每日上限 |
> | 動作 | Microsoft Insights/Components/DailyCapWarningThresholdReached/Action | 已達到 Application Insights 元件的每日上限警告閾值 |
> | 動作 | Microsoft.Insights/Components/Write | 寫入 Application Insights 元件設定 |
> | 動作 | Microsoft.Insights/Components/Delete | 刪除 Application Insights 元件設定 |
> | 動作 | Microsoft.Insights/Components/Read | 讀取 Application Insights 元件設定 |
> | 動作 | Microsoft.Insights/Components/ExportConfiguration/Action | Application Insights 匯出設定動作 |
> | 動作 | Microsoft.Insights/Components/Move/Action | 將 Application Insights 元件移到另一個資源群組或訂用帳戶 |
> |  | **元件/AnalyticsItems** |  |
> | 動作 | Microsoft.Insights/Components/AnalyticsItems/Delete | 刪除 Application Insights 分析項目 |
> | 動作 | Microsoft.Insights/Components/AnalyticsItems/Read | 讀取 Application Insights 分析項目 |
> | 動作 | Microsoft.Insights/Components/AnalyticsItems/Write | 寫入 Application Insights 分析項目 |
> |  | **元件/AnalyticsTables** |  |
> | 動作 | Microsoft.Insights/Components/AnalyticsTables/Delete | 刪除 Application Insights 分析資料表結構描述 |
> | 動作 | Microsoft.Insights/Components/AnalyticsTables/Read | 讀取 Application Insights 分析資料表結構描述 |
> | 動作 | Microsoft.Insights/Components/AnalyticsTables/Write | 寫入 Application Insights 分析資料表結構描述 |
> |  | **元件/注釋** |  |
> | 動作 | Microsoft.Insights/Components/Annotations/Delete | 刪除 Application Insights 註釋 |
> | 動作 | Microsoft.Insights/Components/Annotations/Read | 讀取 Application Insights 註釋 |
> | 動作 | Microsoft.Insights/Components/Annotations/Write | 寫入 Application Insights 註釋 |
> |  | **元件/Api** |  |
> | 動作 | Microsoft.Insights/Components/Api/Read | 讀取 Application Insights 元件資料 API |
> |  | **元件/ApiKeys** |  |
> | 動作 | Microsoft.Insights/Components/ApiKeys/Delete | 刪除 Application Insights API 金鑰 |
> | 動作 | Microsoft.Insights/Components/ApiKeys/Read | 讀取 Application Insights API 金鑰 |
> |  | **元件/BillingPlanForComponent** |  |
> | 動作 | Microsoft.Insights/Components/BillingPlanForComponent/Read | 讀取 Application Insights 元件的計費方案 |
> |  | **元件/CurrentBillingFeatures** |  |
> | 動作 | Microsoft.Insights/Components/CurrentBillingFeatures/Read | 在讀取 Application Insights 元件目前的計費功能 |
> | 動作 | Microsoft.Insights/Components/CurrentBillingFeatures/Write | 寫入 Application Insights 元件目前的計費功能 |
> |  | **元件/DefaultWorkItemConfig** |  |
> | 動作 | Microsoft.Insights/Components/DefaultWorkItemConfig/Read | 讀取 Application Insights 預設的 ALM 整合設定 |
> |  | **元件/事件** |  |
> | 動作 | Microsoft.Insights/Components/Events/Read | 使用 OData 查詢格式從 Application Insights 取得記錄 |
> |  | **元件/ExportConfiguration** |  |
> | 動作 | Microsoft.Insights/Components/ExportConfiguration/Delete | 刪除 Application Insights 匯出設定 |
> | 動作 | Microsoft.Insights/Components/ExportConfiguration/Read | 讀取 Application Insights 匯出設定 |
> | 動作 | Microsoft.Insights/Components/ExportConfiguration/Write | 寫入 Application Insights 匯出設定 |
> |  | **元件/ExtendQueries** |  |
> | 動作 | Microsoft.Insights/Components/ExtendQueries/Read | 讀取 Application Insights 元件擴充的查詢結果 |
> |  | **元件/我的最愛** |  |
> | 動作 | Microsoft.Insights/Components/Favorites/Delete | 刪除 Application Insights 我的最愛 |
> | 動作 | Microsoft.Insights/Components/Favorites/Read | 讀取 Application Insights 我的最愛 |
> | 動作 | Microsoft.Insights/Components/Favorites/Write | 寫入 Application Insights 我的最愛 |
> |  | **元件/FeatureCapabilities** |  |
> | 動作 | Microsoft.Insights/Components/FeatureCapabilities/Read | 讀取 Application Insights 元件功能的能力 |
> |  | **元件/GetAvailableBillingFeatures** |  |
> | 動作 | Microsoft.Insights/Components/GetAvailableBillingFeatures/Read | 讀取 Application Insights 元件可用的計費功能 |
> |  | **元件/GetToken** |  |
> | 動作 | Microsoft.Insights/Components/GetToken/Read | 讀取 Application Insights 元件權杖 |
> |  | **元件/Metricdefinitions.listasync** |  |
> | 動作 | Microsoft.Insights/Components/MetricDefinitions/Read | 讀取 Application Insights 元件計量定義 |
> |  | **元件/計量** |  |
> | 動作 | Microsoft.Insights/Components/Metrics/Read | 讀取 Application Insights 元件計量 |
> |  | **元件/MyAnalyticsItems** |  |
> | 動作 | Microsoft.Insights/Components/MyAnalyticsItems/Delete | 刪除 Application Insights 個人分析項目 |
> | 動作 | Microsoft.Insights/Components/MyAnalyticsItems/Write | 寫入 Application Insights 個人分析項目 |
> | 動作 | Microsoft.Insights/Components/MyAnalyticsItems/Read | 讀取 Application Insights 個人分析項目 |
> |  | **元件/MyFavorites** |  |
> | 動作 | Microsoft.Insights/Components/MyFavorites/Read | 讀取 Application Insights 個人我的最愛 |
> |  | **元件/作業** |  |
> | 動作 | Microsoft.Insights/Components/Operations/Read | 取得 Application Insights 中長時間執行之作業的狀態 |
> |  | **元件/PricingPlans** |  |
> | 動作 | Microsoft.Insights/Components/PricingPlans/Read | 讀取 Application Insights 元件定價方案 |
> | 動作 | Microsoft.Insights/Components/PricingPlans/Write | 寫入 Application Insights 元件定價方案 |
> |  | **元件/ProactiveDetectionConfigs** |  |
> | 動作 | Microsoft.Insights/Components/ProactiveDetectionConfigs/Read | 讀取 Application Insights 主動式偵測設定 |
> | 動作 | Microsoft.Insights/Components/ProactiveDetectionConfigs/Write | 寫入 Application Insights 主動式偵測設定 |
> |  | **元件/提供者/Microsoft Insights/diagnosticSettings** |  |
> | 動作 | Microsoft Insights/Components/provider/Microsoft Insights/diagnosticSettings/Read | 讀取資源診斷設定 |
> | 動作 | Microsoft Insights/Components/provider/Microsoft Insights/diagnosticSettings/Write | 建立或更新資源診斷設定 |
> |  | **元件/提供者/Microsoft Insights/logDefinitions** |  |
> | 動作 | Microsoft Insights/Components/provider/Microsoft Insights/logDefinitions/Read | 讀取記錄定義 |
> |  | **元件/提供者/Microsoft Insights/Metricdefinitions.listasync** |  |
> | 動作 | Microsoft.Insights/Components/providers/Microsoft.Insights/MetricDefinitions/Read | 讀取計量定義 |
> |  | **元件/查詢** |  |
> | 動作 | Microsoft.Insights/Components/Query/Read | 針對 Application Insights 記錄執行查詢 |
> |  | **元件/QuotaStatus** |  |
> | 動作 | Microsoft.Insights/Components/QuotaStatus/Read | 讀取 Application Insights 元件配額狀態 |
> |  | **元件/SyntheticMonitorLocations** |  |
> | 動作 | Microsoft.Insights/Components/SyntheticMonitorLocations/Read | 讀取 Application Insights webtest 位置 |
> |  | **元件/Webtest** |  |
> | 動作 | Microsoft.Insights/Components/Webtests/Read | 讀取 Webtest 設定 |
> |  | **元件/WorkItemConfigs** |  |
> | 動作 | Microsoft.Insights/Components/WorkItemConfigs/Delete | 刪除 Application Insights ALM 整合設定 |
> | 動作 | Microsoft.Insights/Components/WorkItemConfigs/Read | 讀取 Application Insights ALM 整合設定 |
> | 動作 | Microsoft.Insights/Components/WorkItemConfigs/Write | 寫入 Application Insights ALM 整合設定 |
> |  | **DataCollectionRuleAssociations** |  |
> | 動作 | Microsoft Insights/DataCollectionRuleAssociations/Read | 讀取資源與資料集合規則的關聯 |
> | 動作 | Microsoft Insights/DataCollectionRuleAssociations/Write | 建立或更新資源與資料集合規則的關聯 |
> | 動作 | Microsoft Insights/DataCollectionRuleAssociations/Delete | 刪除資源與資料集合規則的關聯 |
> |  | **DataCollectionRules** |  |
> | 動作 | Microsoft Insights/DataCollectionRules/Read | 讀取資料收集規則 |
> | 動作 | Microsoft Insights/DataCollectionRules/Write | 建立或更新資料收集規則 |
> | 動作 | Microsoft Insights/DataCollectionRules/Delete | 刪除資料收集規則 |
> |  | **DiagnosticSettings** |  |
> | 動作 | Microsoft.Insights/DiagnosticSettings/Write | 建立或更新資源診斷設定 |
> | 動作 | Microsoft.Insights/DiagnosticSettings/Delete | 刪除資源診斷設定 |
> | 動作 | Microsoft.Insights/DiagnosticSettings/Read | 讀取資源診斷設定 |
> |  | **EventCategories** |  |
> | 動作 | Microsoft.Insights/EventCategories/Read | 讀取可用的活動記錄事件類別 |
> |  | **eventtypes/digestevents** |  |
> | 動作 | Microsoft.Insights/eventtypes/digestevents/Read | 讀取管理事件類型摘要 |
> |  | **eventtypes/值** |  |
> | 動作 | Microsoft.Insights/eventtypes/values/Read | 讀取活動記錄事件 |
> |  | **ExtendedDiagnosticSettings** |  |
> | 動作 | Microsoft.Insights/ExtendedDiagnosticSettings/Write | 建立或更新網路流量記錄診斷設定 |
> | 動作 | Microsoft.Insights/ExtendedDiagnosticSettings/Delete | 刪除網路流量記錄診斷設定 |
> | 動作 | Microsoft.Insights/ExtendedDiagnosticSettings/Read | 讀取網路流量記錄診斷設定 |
> |  | **ListMigrationDate** |  |
> | 動作 | Microsoft.Insights/ListMigrationDate/Read | 取回訂用帳戶移轉日期 |
> |  | **LogDefinitions** |  |
> | 動作 | Microsoft.Insights/LogDefinitions/Read | 讀取記錄定義 |
> |  | **LogProfiles** |  |
> | 動作 | Microsoft.Insights/LogProfiles/Write | 建立或更新活動記錄的記錄設定檔 |
> | 動作 | Microsoft.Insights/LogProfiles/Delete | 刪除活動記錄的記錄設定檔 |
> | 動作 | Microsoft.Insights/LogProfiles/Read | 讀取活動記錄的記錄設定檔 |
> |  | **記錄** |  |
> | 動作 | Microsoft.Insights/Logs/Read | 從您的所有記錄讀取資料 |
> |  | **Logs/ADAssessmentRecommendation** |  |
> | 動作 | Microsoft.Insights/Logs/ADAssessmentRecommendation/Read | 從 ADAssessmentRecommendation 資料表讀取資料 |
> |  | **Logs/ADReplicationResult** |  |
> | 動作 | Microsoft.Insights/Logs/ADReplicationResult/Read | 從 ADReplicationResult 資料表讀取資料 |
> |  | **Logs/ADSecurityAssessmentRecommendation** |  |
> | 動作 | Microsoft.Insights/Logs/ADSecurityAssessmentRecommendation/Read | 從 ADSecurityAssessmentRecommendation 資料表讀取資料 |
> |  | **記錄/警示** |  |
> | 動作 | Microsoft.Insights/Logs/Alert/Read | 從 Alert 資料表讀取資料 |
> |  | **Logs/AlertHistory** |  |
> | 動作 | Microsoft.Insights/Logs/AlertHistory/Read | 從 AlertHistory 資料表讀取資料 |
> |  | **Logs/ApplicationInsights** |  |
> | 動作 | Microsoft.Insights/Logs/ApplicationInsights/Read | 從 ApplicationInsights 資料表讀取資料 |
> |  | **Logs/AzureActivity** |  |
> | 動作 | Microsoft.Insights/Logs/AzureActivity/Read | 從 AzureActivity 資料表讀取資料 |
> |  | **Logs/AzureMetrics** |  |
> | 動作 | Microsoft.Insights/Logs/AzureMetrics/Read | 從 AzureMetrics 資料表讀取資料 |
> |  | **Logs/BoundPort** |  |
> | 動作 | Microsoft.Insights/Logs/BoundPort/Read | 從 BoundPort 資料表讀取資料 |
> |  | **Logs/CommonSecurityLog** |  |
> | 動作 | Microsoft.Insights/Logs/CommonSecurityLog/Read | 從 CommonSecurityLog 資料表讀取資料 |
> |  | **Logs/ComputerGroup** |  |
> | 動作 | Microsoft.Insights/Logs/ComputerGroup/Read | 從 ComputerGroup 資料表讀取資料 |
> |  | **Logs/ConfigurationChange** |  |
> | 動作 | Microsoft.Insights/Logs/ConfigurationChange/Read | 從 ConfigurationChange 資料表讀取資料 |
> |  | **Logs/ConfigurationData** |  |
> | 動作 | Microsoft.Insights/Logs/ConfigurationData/Read | 從 ConfigurationData 資料表讀取資料 |
> |  | **Logs/ContainerImageInventory** |  |
> | 動作 | Microsoft.Insights/Logs/ContainerImageInventory/Read | 從 ContainerImageInventory 資料表讀取資料 |
> |  | **Logs/ContainerInventory** |  |
> | 動作 | Microsoft.Insights/Logs/ContainerInventory/Read | 從 ContainerInventory 資料表讀取資料 |
> |  | **Logs/ContainerLog** |  |
> | 動作 | Microsoft.Insights/Logs/ContainerLog/Read | 從 ContainerLog 資料表讀取資料 |
> |  | **Logs/ContainerServiceLog** |  |
> | 動作 | Microsoft.Insights/Logs/ContainerServiceLog/Read | 從 ContainerServiceLog 資料表讀取資料 |
> |  | **Logs/DeviceAppCrash** |  |
> | 動作 | Microsoft.Insights/Logs/DeviceAppCrash/Read | 從 DeviceAppCrash 資料表讀取資料 |
> |  | **Logs/DeviceAppLaunch** |  |
> | 動作 | Microsoft.Insights/Logs/DeviceAppLaunch/Read | 從 DeviceAppLaunch 資料表讀取資料 |
> |  | **Logs/DeviceCalendar** |  |
> | 動作 | Microsoft.Insights/Logs/DeviceCalendar/Read | 從 DeviceCalendar 資料表讀取資料 |
> |  | **Logs/DeviceCleanup** |  |
> | 動作 | Microsoft.Insights/Logs/DeviceCleanup/Read | 從 DeviceCleanup 資料表讀取資料 |
> |  | **Logs/DeviceConnectSession** |  |
> | 動作 | Microsoft.Insights/Logs/DeviceConnectSession/Read | 從 DeviceConnectSession 資料表讀取資料 |
> |  | **Logs/DeviceEtw** |  |
> | 動作 | Microsoft.Insights/Logs/DeviceEtw/Read | 從 DeviceEtw 資料表讀取資料 |
> |  | **Logs/DeviceHardwareHealth** |  |
> | 動作 | Microsoft.Insights/Logs/DeviceHardwareHealth/Read | 從 DeviceHardwareHealth 資料表讀取資料 |
> |  | **Logs/DeviceHealth** |  |
> | 動作 | Microsoft.Insights/Logs/DeviceHealth/Read | 從 DeviceHealth 資料表讀取資料 |
> |  | **Logs/DeviceHeartbeat** |  |
> | 動作 | Microsoft.Insights/Logs/DeviceHeartbeat/Read | 從 DeviceHeartbeat 資料表讀取資料 |
> |  | **Logs/DeviceSkypeHeartbeat** |  |
> | 動作 | Microsoft.Insights/Logs/DeviceSkypeHeartbeat/Read | 從 DeviceSkypeHeartbeat 資料表讀取資料 |
> |  | **Logs/DeviceSkypeSignIn** |  |
> | 動作 | Microsoft.Insights/Logs/DeviceSkypeSignIn/Read | 從 DeviceSkypeSignIn 資料表讀取資料 |
> |  | **Logs/DeviceSleepState** |  |
> | 動作 | Microsoft.Insights/Logs/DeviceSleepState/Read | 從 DeviceSleepState 資料表讀取資料 |
> |  | **Logs/DHAppFailure** |  |
> | 動作 | Microsoft.Insights/Logs/DHAppFailure/Read | 從 DHAppFailure 資料表讀取資料 |
> |  | **Logs/DHAppReliability** |  |
> | 動作 | Microsoft.Insights/Logs/DHAppReliability/Read | 從 DHAppReliability 資料表讀取資料 |
> |  | **Logs/DHDriverReliability** |  |
> | 動作 | Microsoft.Insights/Logs/DHDriverReliability/Read | 從 DHDriverReliability 資料表讀取資料 |
> |  | **Logs/DHLogonFailures** |  |
> | 動作 | Microsoft.Insights/Logs/DHLogonFailures/Read | 從 DHLogonFailures 資料表讀取資料 |
> |  | **Logs/DHLogonMetrics** |  |
> | 動作 | Microsoft.Insights/Logs/DHLogonMetrics/Read | 從 DHLogonMetrics 資料表讀取資料 |
> |  | **Logs/DHOSCrashData** |  |
> | 動作 | Microsoft.Insights/Logs/DHOSCrashData/Read | 從 DHOSCrashData 資料表讀取資料 |
> |  | **Logs/DHOSReliability** |  |
> | 動作 | Microsoft.Insights/Logs/DHOSReliability/Read | 從 DHOSReliability 資料表讀取資料 |
> |  | **Logs/DHWipAppLearning** |  |
> | 動作 | Microsoft.Insights/Logs/DHWipAppLearning/Read | 從 DHWipAppLearning 資料表讀取資料 |
> |  | **Logs/DnsEvents** |  |
> | 動作 | Microsoft.Insights/Logs/DnsEvents/Read | 從 DnsEvents 資料表讀取資料 |
> |  | **Logs/DnsInventory** |  |
> | 動作 | Microsoft.Insights/Logs/DnsInventory/Read | 從 DnsInventory 資料表讀取資料 |
> |  | **Logs/ETWEvent** |  |
> | 動作 | Microsoft.Insights/Logs/ETWEvent/Read | 從 ETWEvent 資料表讀取資料 |
> |  | **記錄/事件** |  |
> | 動作 | Microsoft.Insights/Logs/Event/Read | 從 Event 資料表讀取資料 |
> |  | **Logs/ExchangeAssessmentRecommendation** |  |
> | 動作 | Microsoft.Insights/Logs/ExchangeAssessmentRecommendation/Read | 從 ExchangeAssessmentRecommendation 資料表讀取資料 |
> |  | **Logs/ExchangeOnlineAssessmentRecommendation** |  |
> | 動作 | Microsoft.Insights/Logs/ExchangeOnlineAssessmentRecommendation/Read | 從 ExchangeOnlineAssessmentRecommendation 資料表讀取資料 |
> |  | **記錄/心跳** |  |
> | 動作 | Microsoft.Insights/Logs/Heartbeat/Read | 從 Heartbeat 資料表讀取資料 |
> |  | **Logs/IISAssessmentRecommendation** |  |
> | 動作 | Microsoft.Insights/Logs/IISAssessmentRecommendation/Read | 從 IISAssessmentRecommendation 資料表讀取資料 |
> |  | **Logs/InboundConnection** |  |
> | 動作 | Microsoft.Insights/Logs/InboundConnection/Read | 從 InboundConnection 資料表讀取資料 |
> |  | **Logs/KubeNodeInventory** |  |
> | 動作 | Microsoft.Insights/Logs/KubeNodeInventory/Read | 從 KubeNodeInventory 資料表讀取資料 |
> |  | **Logs/KubePodInventory** |  |
> | 動作 | Microsoft.Insights/Logs/KubePodInventory/Read | 從 KubePodInventory 資料表讀取資料 |
> |  | **Logs/LinuxAuditLog** |  |
> | 動作 | Microsoft.Insights/Logs/LinuxAuditLog/Read | 從 LinuxAuditLog 資料表讀取資料 |
> |  | **Logs/MAApplication** |  |
> | 動作 | Microsoft.Insights/Logs/MAApplication/Read | 從 MAApplication 資料表讀取資料 |
> |  | **Logs/MAApplicationHealth** |  |
> | 動作 | Microsoft.Insights/Logs/MAApplicationHealth/Read | 從 MAApplicationHealth 資料表讀取資料 |
> |  | **Logs/MAApplicationHealthAlternativeVersions** |  |
> | 動作 | Microsoft.Insights/Logs/MAApplicationHealthAlternativeVersions/Read | 從 MAApplicationHealthAlternativeVersions 資料表讀取資料 |
> |  | **Logs/MAApplicationHealthIssues** |  |
> | 動作 | Microsoft.Insights/Logs/MAApplicationHealthIssues/Read | 從 MAApplicationHealthIssues 資料表讀取資料 |
> |  | **Logs/MAApplicationInstance** |  |
> | 動作 | Microsoft.Insights/Logs/MAApplicationInstance/Read | 從 MAApplicationInstance 資料表讀取資料 |
> |  | **Logs/MAApplicationInstanceReadiness** |  |
> | 動作 | Microsoft.Insights/Logs/MAApplicationInstanceReadiness/Read | 從 MAApplicationInstanceReadiness 資料表讀取資料 |
> |  | **Logs/MAApplicationReadiness** |  |
> | 動作 | Microsoft.Insights/Logs/MAApplicationReadiness/Read | 從 MAApplicationReadiness 資料表讀取資料 |
> |  | **Logs/MADeploymentPlan** |  |
> | 動作 | Microsoft.Insights/Logs/MADeploymentPlan/Read | 從 MADeploymentPlan 資料表讀取資料 |
> |  | **Logs/MADevice** |  |
> | 動作 | Microsoft.Insights/Logs/MADevice/Read | 從 MADevice 資料表讀取資料 |
> |  | **Logs/MADevicePnPHealth** |  |
> | 動作 | Microsoft.Insights/Logs/MADevicePnPHealth/Read | 從 MADevicePnPHealth 資料表讀取資料 |
> |  | **Logs/MADevicePnPHealthAlternativeVersions** |  |
> | 動作 | Microsoft.Insights/Logs/MADevicePnPHealthAlternativeVersions/Read | 從 MADevicePnPHealthAlternativeVersions 資料表讀取資料 |
> |  | **Logs/MADevicePnPHealthIssues** |  |
> | 動作 | Microsoft.Insights/Logs/MADevicePnPHealthIssues/Read | 從 MADevicePnPHealthIssues 資料表讀取資料 |
> |  | **Logs/MADeviceReadiness** |  |
> | 動作 | Microsoft.Insights/Logs/MADeviceReadiness/Read | 從 MADeviceReadiness 資料表讀取資料 |
> |  | **Logs/MADriverInstanceReadiness** |  |
> | 動作 | Microsoft.Insights/Logs/MADriverInstanceReadiness/Read | 從 MADriverInstanceReadiness 資料表讀取資料 |
> |  | **Logs/MADriverReadiness** |  |
> | 動作 | Microsoft.Insights/Logs/MADriverReadiness/Read | 從 MADriverReadiness 資料表讀取資料 |
> |  | **Logs/MAOfficeAddin** |  |
> | 動作 | Microsoft.Insights/Logs/MAOfficeAddin/Read | 從 MAOfficeAddin 資料表讀取資料 |
> |  | **Logs/MAOfficeAddinHealth** |  |
> | 動作 | Microsoft.Insights/Logs/MAOfficeAddinHealth/Read | 從 MAOfficeAddinHealth 資料表讀取資料 |
> |  | **Logs/MAOfficeAddinHealthIssues** |  |
> | 動作 | Microsoft.Insights/Logs/MAOfficeAddinHealthIssues/Read | 從 MAOfficeAddinHealthIssues 資料表讀取資料 |
> |  | **Logs/MAOfficeAddinInstance** |  |
> | 動作 | Microsoft.Insights/Logs/MAOfficeAddinInstance/Read | 從 MAOfficeAddinInstance 資料表讀取資料 |
> |  | **Logs/MAOfficeAddinInstanceReadiness** |  |
> | 動作 | Microsoft.Insights/Logs/MAOfficeAddinInstanceReadiness/Read | 從 MAOfficeAddinInstanceReadiness 資料表讀取資料 |
> |  | **Logs/MAOfficeAddinReadiness** |  |
> | 動作 | Microsoft.Insights/Logs/MAOfficeAddinReadiness/Read | 從 MAOfficeAddinReadiness 資料表讀取資料 |
> |  | **Logs/MAOfficeApp** |  |
> | 動作 | Microsoft.Insights/Logs/MAOfficeApp/Read | 從 MAOfficeApp 資料表讀取資料 |
> |  | **Logs/MAOfficeAppHealth** |  |
> | 動作 | Microsoft.Insights/Logs/MAOfficeAppHealth/Read | 從 MAOfficeAppHealth 資料表讀取資料 |
> |  | **Logs/MAOfficeAppInstance** |  |
> | 動作 | Microsoft.Insights/Logs/MAOfficeAppInstance/Read | 從 MAOfficeAppInstance 資料表讀取資料 |
> |  | **Logs/MAOfficeAppReadiness** |  |
> | 動作 | Microsoft.Insights/Logs/MAOfficeAppReadiness/Read | 從 MAOfficeAppReadiness 資料表讀取資料 |
> |  | **Logs/MAOfficeBuildInfo** |  |
> | 動作 | Microsoft.Insights/Logs/MAOfficeBuildInfo/Read | 從 MAOfficeBuildInfo 資料表讀取資料 |
> |  | **Logs/MAOfficeCurrencyAssessment** |  |
> | 動作 | Microsoft.Insights/Logs/MAOfficeCurrencyAssessment/Read | 從 MAOfficeCurrencyAssessment 資料表讀取資料 |
> |  | **Logs/MAOfficeCurrencyAssessmentDailyCounts** |  |
> | 動作 | Microsoft.Insights/Logs/MAOfficeCurrencyAssessmentDailyCounts/Read | 從 MAOfficeCurrencyAssessmentDailyCounts 資料表讀取資料 |
> |  | **Logs/MAOfficeDeploymentStatus** |  |
> | 動作 | Microsoft.Insights/Logs/MAOfficeDeploymentStatus/Read | 從 MAOfficeDeploymentStatus 資料表讀取資料 |
> |  | **Logs/MAOfficeMacroHealth** |  |
> | 動作 | Microsoft.Insights/Logs/MAOfficeMacroHealth/Read | 從 MAOfficeMacroHealth 資料表讀取資料 |
> |  | **Logs/MAOfficeMacroHealthIssues** |  |
> | 動作 | Microsoft.Insights/Logs/MAOfficeMacroHealthIssues/Read | 從 MAOfficeMacroHealthIssues 資料表讀取資料 |
> |  | **Logs/MAOfficeMacroIssueInstanceReadiness** |  |
> | 動作 | Microsoft.Insights/Logs/MAOfficeMacroIssueInstanceReadiness/Read | 從 MAOfficeMacroIssueInstanceReadiness 資料表讀取資料 |
> |  | **Logs/MAOfficeMacroIssueReadiness** |  |
> | 動作 | Microsoft.Insights/Logs/MAOfficeMacroIssueReadiness/Read | 從 MAOfficeMacroIssueReadiness 資料表讀取資料 |
> |  | **Logs/MAOfficeMacroSummary** |  |
> | 動作 | Microsoft.Insights/Logs/MAOfficeMacroSummary/Read | 從 MAOfficeMacroSummary 資料表讀取資料 |
> |  | **Logs/MAOfficeSuite** |  |
> | 動作 | Microsoft.Insights/Logs/MAOfficeSuite/Read | 從 MAOfficeSuite 資料表讀取資料 |
> |  | **Logs/MAOfficeSuiteInstance** |  |
> | 動作 | Microsoft.Insights/Logs/MAOfficeSuiteInstance/Read | 從 MAOfficeSuiteInstance 資料表讀取資料 |
> |  | **Logs/MAProposedPilotDevices** |  |
> | 動作 | Microsoft.Insights/Logs/MAProposedPilotDevices/Read | 從 MAProposedPilotDevices 資料表讀取資料 |
> |  | **Logs/MAWindowsBuildInfo** |  |
> | 動作 | Microsoft.Insights/Logs/MAWindowsBuildInfo/Read | 從 MAWindowsBuildInfo 資料表讀取資料 |
> |  | **Logs/MAWindowsCurrencyAssessment** |  |
> | 動作 | Microsoft.Insights/Logs/MAWindowsCurrencyAssessment/Read | 從 MAWindowsCurrencyAssessment 資料表讀取資料 |
> |  | **Logs/MAWindowsCurrencyAssessmentDailyCounts** |  |
> | 動作 | Microsoft.Insights/Logs/MAWindowsCurrencyAssessmentDailyCounts/Read | 從 MAWindowsCurrencyAssessmentDailyCounts 資料表讀取資料 |
> |  | **Logs/MAWindowsDeploymentStatus** |  |
> | 動作 | Microsoft.Insights/Logs/MAWindowsDeploymentStatus/Read | 從 MAWindowsDeploymentStatus 資料表讀取資料 |
> |  | **Logs/MAWindowsSysReqInstanceReadiness** |  |
> | 動作 | Microsoft.Insights/Logs/MAWindowsSysReqInstanceReadiness/Read | 從 MAWindowsSysReqInstanceReadiness 資料表讀取資料 |
> |  | **Logs/NetworkMonitoring** |  |
> | 動作 | Microsoft.Insights/Logs/NetworkMonitoring/Read | 從 NetworkMonitoring 資料表讀取資料 |
> |  | **Logs/OfficeActivity** |  |
> | 動作 | Microsoft.Insights/Logs/OfficeActivity/Read | 從 OfficeActivity 資料表讀取資料 |
> |  | **記錄/作業** |  |
> | 動作 | Microsoft.Insights/Logs/Operation/Read | 從 Operation 資料表讀取資料 |
> |  | **Logs/OutboundConnection** |  |
> | 動作 | Microsoft.Insights/Logs/OutboundConnection/Read | 從 OutboundConnection 資料表讀取資料 |
> |  | **記錄/效能** |  |
> | 動作 | Microsoft.Insights/Logs/Perf/Read | 從 Perf 資料表讀取資料 |
> |  | **Logs/ProtectionStatus** |  |
> | 動作 | Microsoft.Insights/Logs/ProtectionStatus/Read | 從 ProtectionStatus 資料表讀取資料 |
> |  | **Logs/ReservedAzureCommonFields** |  |
> | 動作 | Microsoft.Insights/Logs/ReservedAzureCommonFields/Read | 從 ReservedAzureCommonFields 資料表讀取資料 |
> |  | **Logs/ReservedCommonFields** |  |
> | 動作 | Microsoft.Insights/Logs/ReservedCommonFields/Read | 從 ReservedCommonFields 資料表讀取資料 |
> |  | **Logs/SCCMAssessmentRecommendation** |  |
> | 動作 | Microsoft.Insights/Logs/SCCMAssessmentRecommendation/Read | 從 SCCMAssessmentRecommendation 資料表讀取資料 |
> |  | **Logs/SCOMAssessmentRecommendation** |  |
> | 動作 | Microsoft.Insights/Logs/SCOMAssessmentRecommendation/Read | 從 SCOMAssessmentRecommendation 資料表讀取資料 |
> |  | **Logs/SecurityAlert** |  |
> | 動作 | Microsoft.Insights/Logs/SecurityAlert/Read | 從 SecurityAlert 資料表讀取資料 |
> |  | **Logs/SecurityBaseline** |  |
> | 動作 | Microsoft.Insights/Logs/SecurityBaseline/Read | 從 SecurityBaseline 資料表讀取資料 |
> |  | **Logs/SecurityBaselineSummary** |  |
> | 動作 | Microsoft.Insights/Logs/SecurityBaselineSummary/Read | 從 SecurityBaselineSummary 資料表讀取資料 |
> |  | **Logs/SecurityDetection** |  |
> | 動作 | Microsoft.Insights/Logs/SecurityDetection/Read | 從 SecurityDetection 資料表讀取資料 |
> |  | **Logs/SecurityEvent** |  |
> | 動作 | Microsoft.Insights/Logs/SecurityEvent/Read | 從 SecurityEvent 資料表讀取資料 |
> |  | **Logs/ServiceFabricOperationalEvent** |  |
> | 動作 | Microsoft.Insights/Logs/ServiceFabricOperationalEvent/Read | 從 ServiceFabricOperationalEvent 資料表讀取資料 |
> |  | **Logs/ServiceFabricReliableActorEvent** |  |
> | 動作 | Microsoft.Insights/Logs/ServiceFabricReliableActorEvent/Read | 從 ServiceFabricReliableActorEvent 資料表讀取資料 |
> |  | **Logs/ServiceFabricReliableServiceEvent** |  |
> | 動作 | Microsoft.Insights/Logs/ServiceFabricReliableServiceEvent/Read | 從 ServiceFabricReliableServiceEvent 資料表讀取資料 |
> |  | **Logs/SfBAssessmentRecommendation** |  |
> | 動作 | Microsoft.Insights/Logs/SfBAssessmentRecommendation/Read | 從 SfBAssessmentRecommendation 資料表讀取資料 |
> |  | **Logs/SfBOnlineAssessmentRecommendation** |  |
> | 動作 | Microsoft.Insights/Logs/SfBOnlineAssessmentRecommendation/Read | 從 SfBOnlineAssessmentRecommendation 資料表讀取資料 |
> |  | **Logs/SharePointOnlineAssessmentRecommendation** |  |
> | 動作 | Microsoft.Insights/Logs/SharePointOnlineAssessmentRecommendation/Read | 從 SharePointOnlineAssessmentRecommendation 資料表讀取資料 |
> |  | **Logs/SPAssessmentRecommendation** |  |
> | 動作 | Microsoft.Insights/Logs/SPAssessmentRecommendation/Read | 從 SPAssessmentRecommendation 資料表讀取資料 |
> |  | **Logs/SQLAssessmentRecommendation** |  |
> | 動作 | Microsoft.Insights/Logs/SQLAssessmentRecommendation/Read | 從 SQLAssessmentRecommendation 資料表讀取資料 |
> |  | **Logs/SQLQueryPerformance** |  |
> | 動作 | Microsoft.Insights/Logs/SQLQueryPerformance/Read | 從 SQLQueryPerformance 資料表讀取資料 |
> |  | **記錄/Syslog** |  |
> | 動作 | Microsoft.Insights/Logs/Syslog/Read | 從 Syslog 資料表讀取資料 |
> |  | **Logs/SysmonEvent** |  |
> | 動作 | Microsoft.Insights/Logs/SysmonEvent/Read | 從 SysmonEvent 資料表讀取資料 |
> |  | **記錄/資料表。自訂** |  |
> | 動作 | Microsoft Insights/Logs/Tables。自訂/讀取 | 從任何自訂記錄檔讀取資料 |
> |  | **Logs/UAApp** |  |
> | 動作 | Microsoft.Insights/Logs/UAApp/Read | 從 UAApp 資料表讀取資料 |
> |  | **Logs/UAComputer** |  |
> | 動作 | Microsoft.Insights/Logs/UAComputer/Read | 從 UAComputer 資料表讀取資料 |
> |  | **Logs/UAComputerRank** |  |
> | 動作 | Microsoft.Insights/Logs/UAComputerRank/Read | 從 UAComputerRank 資料表讀取資料 |
> |  | **Logs/UADriver** |  |
> | 動作 | Microsoft.Insights/Logs/UADriver/Read | 從 UADriver 資料表讀取資料 |
> |  | **Logs/UADriverProblemCodes** |  |
> | 動作 | Microsoft.Insights/Logs/UADriverProblemCodes/Read | 從 UADriverProblemCodes 資料表讀取資料 |
> |  | **Logs/UAFeedback** |  |
> | 動作 | Microsoft.Insights/Logs/UAFeedback/Read | 從 UAFeedback 資料表讀取資料 |
> |  | **Logs/UAHardwareSecurity** |  |
> | 動作 | Microsoft.Insights/Logs/UAHardwareSecurity/Read | 從 UAHardwareSecurity 資料表讀取資料 |
> |  | **Logs/UAIESiteDiscovery** |  |
> | 動作 | Microsoft.Insights/Logs/UAIESiteDiscovery/Read | 從 UAIESiteDiscovery 資料表讀取資料 |
> |  | **Logs/UAOfficeAddIn** |  |
> | 動作 | Microsoft.Insights/Logs/UAOfficeAddIn/Read | 從 UAOfficeAddIn 資料表讀取資料 |
> |  | **Logs/UAProposedActionPlan** |  |
> | 動作 | Microsoft.Insights/Logs/UAProposedActionPlan/Read | 從 UAProposedActionPlan 資料表讀取資料 |
> |  | **Logs/UASysReqIssue** |  |
> | 動作 | Microsoft.Insights/Logs/UASysReqIssue/Read | 從 UASysReqIssue 資料表讀取資料 |
> |  | **Logs/UAUpgradedComputer** |  |
> | 動作 | Microsoft.Insights/Logs/UAUpgradedComputer/Read | 從 UAUpgradedComputer 資料表讀取資料 |
> |  | **記錄/更新** |  |
> | 動作 | Microsoft.Insights/Logs/Update/Read | 從 Update 資料表讀取資料 |
> |  | **Logs/UpdateRunProgress** |  |
> | 動作 | Microsoft.Insights/Logs/UpdateRunProgress/Read | 從 UpdateRunProgress 資料表讀取資料 |
> |  | **Logs/UpdateSummary** |  |
> | 動作 | Microsoft.Insights/Logs/UpdateSummary/Read | 從 UpdateSummary 資料表讀取資料 |
> |  | **記錄/使用量** |  |
> | 動作 | Microsoft.Insights/Logs/Usage/Read | 從 Usage 資料表讀取資料 |
> |  | **Logs/W3CIISLog** |  |
> | 動作 | Microsoft.Insights/Logs/W3CIISLog/Read | 從 W3CIISLog 資料表讀取資料 |
> |  | **Logs/WaaSDeploymentStatus** |  |
> | 動作 | Microsoft.Insights/Logs/WaaSDeploymentStatus/Read | 從 WaaSDeploymentStatus 資料表讀取資料 |
> |  | **Logs/WaaSInsiderStatus** |  |
> | 動作 | Microsoft.Insights/Logs/WaaSInsiderStatus/Read | 從 WaaSInsiderStatus 資料表讀取資料 |
> |  | **Logs/WaaSUpdateStatus** |  |
> | 動作 | Microsoft.Insights/Logs/WaaSUpdateStatus/Read | 從 WaaSUpdateStatus 資料表讀取資料 |
> |  | **Logs/WDAVStatus** |  |
> | 動作 | Microsoft.Insights/Logs/WDAVStatus/Read | 從 WDAVStatus 資料表讀取資料 |
> |  | **Logs/WDAVThreat** |  |
> | 動作 | Microsoft.Insights/Logs/WDAVThreat/Read | 從 WDAVThreat 資料表讀取資料 |
> |  | **Logs/WindowsClientAssessmentRecommendation** |  |
> | 動作 | Microsoft.Insights/Logs/WindowsClientAssessmentRecommendation/Read | 從 WindowsClientAssessmentRecommendation 資料表讀取資料 |
> |  | **Logs/Windows 防火牆** |  |
> | 動作 | Microsoft.Insights/Logs/WindowsFirewall/Read | 從 WindowsFirewall 資料表讀取資料 |
> |  | **Logs/WindowsServerAssessmentRecommendation** |  |
> | 動作 | Microsoft.Insights/Logs/WindowsServerAssessmentRecommendation/Read | 從 WindowsServerAssessmentRecommendation 資料表讀取資料 |
> |  | **Logs/WireData** |  |
> | 動作 | Microsoft.Insights/Logs/WireData/Read | 從 WireData 資料表讀取資料 |
> |  | **Logs/WUDOAggregatedStatus** |  |
> | 動作 | Microsoft.Insights/Logs/WUDOAggregatedStatus/Read | 從 WUDOAggregatedStatus 資料表讀取資料 |
> |  | **Logs/WUDOStatus** |  |
> | 動作 | Microsoft.Insights/Logs/WUDOStatus/Read | 從 WUDOStatus 資料表讀取資料 |
> |  | **MetricAlerts** |  |
> | 動作 | Microsoft.Insights/MetricAlerts/Write | 建立或更新計量警示 |
> | 動作 | Microsoft.Insights/MetricAlerts/Delete | 刪除計量警示 |
> | 動作 | Microsoft.Insights/MetricAlerts/Read | 讀取計量警示 |
> |  | **MetricAlerts/Status** |  |
> | 動作 | Microsoft.Insights/MetricAlerts/Status/Read | 讀取計量警示狀態 |
> |  | **MetricBaselines** |  |
> | 動作 | Microsoft Insights/MetricBaselines/Read | 讀取度量基準 |
> |  | **Metricdefinitions.listasync** |  |
> | 動作 | Microsoft.Insights/MetricDefinitions/Read | 讀取計量定義 |
> |  | **Metricdefinitions.listasync/Microsoft Insights** |  |
> | 動作 | Microsoft.Insights/MetricDefinitions/Microsoft.Insights/Read | 讀取計量定義 |
> |  | **Metricdefinitions.listasync/providers/Microsoft Insights** |  |
> | 動作 | Microsoft.Insights/MetricDefinitions/providers/Microsoft.Insights/Read | 讀取計量定義 |
> |  | **Metricnamespaces** |  |
> | 動作 | Microsoft Insights/Metricnamespaces/Read | 讀取計量命名空間 |
> |  | **計量** |  |
> | 動作 | Microsoft.Insights/Metrics/Read | 讀取計量 |
> |  | **計量/Microsoft Insights** |  |
> | 動作 | Microsoft Insights/計量/Microsoft Insights/讀取 | 讀取計量 |
> |  | **計量/提供者/計量** |  |
> | 動作 | Microsoft.Insights/Metrics/providers/Metrics/Read | 讀取計量 |
> |  | **myWorkbooks** |  |
> | 動作 | Microsoft Insights/myWorkbooks/Write | 建立或更新私用活頁簿 |
> | 動作 | Microsoft Insights/myWorkbooks/Read | 讀取私人活頁簿 |
> |  | **作業** |  |
> | 動作 | Microsoft.Insights/Operations/Read | 讀取作業 |
> |  | **PrivateLinkScopeOperationStatuses** |  |
> | 動作 | Microsoft Insights/PrivateLinkScopeOperationStatuses/Read | 讀取已設定範圍的私人連結操作狀態 |
> |  | **PrivateLinkScopes** |  |
> | 動作 | Microsoft Insights/PrivateLinkScopes/Read | 讀取私人連結範圍 |
> | 動作 | Microsoft Insights/PrivateLinkScopes/Write | 建立或更新私人連結範圍 |
> | 動作 | Microsoft Insights/PrivateLinkScopes/Delete | 刪除私人連結範圍 |
> |  | **PrivateLinkScopes/PrivateEndpointConnectionProxies** |  |
> | 動作 | Microsoft Insights/PrivateLinkScopes/PrivateEndpointConnectionProxies/Read | 讀取私人端點連接 proxy |
> | 動作 | Microsoft Insights/PrivateLinkScopes/PrivateEndpointConnectionProxies/Write | 建立或更新私人端點連接 proxy |
> | 動作 | Microsoft Insights/PrivateLinkScopes/PrivateEndpointConnectionProxies/Delete | 刪除私人端點連接 proxy |
> | 動作 | Microsoft Insights/PrivateLinkScopes/PrivateEndpointConnectionProxies/Validate/Action | 驗證私用端點連接 proxy |
> |  | **PrivateLinkScopes/PrivateEndpointConnections** |  |
> | 動作 | Microsoft Insights/PrivateLinkScopes/PrivateEndpointConnections/Read | 讀取私人端點連接 |
> | 動作 | Microsoft Insights/PrivateLinkScopes/PrivateEndpointConnections/Write | 建立或更新私人端點連接 |
> | 動作 | Microsoft Insights/PrivateLinkScopes/PrivateEndpointConnections/Delete | 刪除私人端點連接 |
> |  | **PrivateLinkScopes/PrivateLinkResources** |  |
> | 動作 | Microsoft Insights/PrivateLinkScopes/PrivateLinkResources/Read | 讀取私人連結資源 |
> |  | **PrivateLinkScopes/ScopedResources** |  |
> | 動作 | Microsoft Insights/PrivateLinkScopes/ScopedResources/Read | 讀取已設定範圍的私人連結資源 |
> | 動作 | Microsoft Insights/PrivateLinkScopes/ScopedResources/Write | 建立或更新已設定範圍的私人連結資源 |
> | 動作 | Microsoft Insights/PrivateLinkScopes/ScopedResources/Delete | 刪除已設定範圍的私人連結資源 |
> |  | **ScheduledQueryRules** |  |
> | 動作 | Microsoft.Insights/ScheduledQueryRules/Write | 寫入排程的查詢規則 |
> | 動作 | Microsoft.Insights/ScheduledQueryRules/Read | 讀取排程的查詢規則 |
> | 動作 | Microsoft.Insights/ScheduledQueryRules/Delete | 刪除排程的查詢規則 |
> |  | **租用戶** |  |
> | 動作 | Microsoft.Insights/Tenants/Register/Action | 將 Microsoft Insights 提供者初始化 |
> |  | **Webtest** |  |
> | 動作 | Microsoft.Insights/Webtests/Write | 寫入至 Webtest 設定 |
> | 動作 | Microsoft.Insights/Webtests/Delete | 刪除 Webtest 設定 |
> | 動作 | Microsoft.Insights/Webtests/Read | 讀取 Webtest 設定 |
> |  | **Webtest/GetToken** |  |
> | 動作 | Microsoft.Insights/Webtests/GetToken/Read | 讀取 Webtest 權杖 |
> |  | **Webtest/Metricdefinitions.listasync** |  |
> | 動作 | Microsoft.Insights/Webtests/MetricDefinitions/Read | 讀取 Webtest 計量定義 |
> |  | **Webtest/計量** |  |
> | 動作 | Microsoft.Insights/Webtests/Metrics/Read | 讀取 Webtest 計量 |
> |  | **活頁簿** |  |
> | 動作 | Microsoft Insights/活頁簿/寫入 | 建立或更新活頁簿 |
> | 動作 | Microsoft Insights/活頁簿/刪除 | 刪除活頁簿 |
> | 動作 | Microsoft Insights/活頁簿/讀取 | 讀取活頁簿 |
> |  | **DataCollectionRules/資料** |  |
> | DataAction | Microsoft Insights/DataCollectionRules/資料/寫入 | 將資料傳送至資料收集規則 |
> |  | **計量** |  |
> | DataAction | Microsoft.Insights/Metrics/Write | 寫入計量 |

## <a name="microsoftintune"></a>Microsoft.Intune

Microsoft Monitoring Insights

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> |  | **diagnosticsettings** |  |
> | 動作 | Microsoft.Intune/diagnosticsettings/write | 正在寫入診斷設定 |
> | 動作 | Microsoft.Intune/diagnosticsettings/read | 正在讀取診斷設定 |
> | 動作 | Microsoft.Intune/diagnosticsettings/delete | 正在刪除診斷設定 |
> |  | **diagnosticsettingscategories** |  |
> | 動作 | Microsoft.Intune/diagnosticsettingscategories/read | 正在讀取診斷設定類別 |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

Azure 服務： [IoT Central](../iot-central/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.IoTCentral/checkNameAvailability/action | 檢查 IoT Central 應用程式名稱是否可用 |
> | 動作 | Microsoft.IoTCentral/checkSubdomainAvailability/action | 檢查 IoT Central 應用程式子網域是否可用 |
> | 動作 | IoTCentral/appTemplates/action | 取得 Azure 上所有可用的應用程式範本 IoT Central |
> | 動作 | Microsoft.IoTCentral/register/action | 針對 Azure IoT Central 資源提供者註冊訂用帳戶 |
> |  | **IoTApps** |  |
> | 動作 | Microsoft.IoTCentral/IoTApps/read | 取得單一 IoT Central 應用程式 |
> | 動作 | Microsoft.IoTCentral/IoTApps/write | 建立或更新 IoT Central 應用程式 |
> | 動作 | Microsoft.IoTCentral/IoTApps/delete | 刪除 IoT Central 應用程式 |
> |  | **業務** |  |
> | 動作 | Microsoft.IoTCentral/operations/read | 取得 IoT Central 應用程式上所有可用的作業 |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

Azure 服務： [Azure 數位 Twins](../digital-twins/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.IoTSpaces/register/action | 為 Microsoft.IoTSpaces Graph 資源提供者註冊訂用帳戶以啟用資源的建立作業 |
> |  | **Graph** |  |
> | 動作 | Microsoft.IoTSpaces/Graph/write | 建立 Microsoft.IoTSpaces Graph 資源 |
> | 動作 | Microsoft.IoTSpaces/Graph/read | 取得 Microsoft.IoTSpaces Graph 資源 |
> | 動作 | Microsoft.IoTSpaces/Graph/delete | 刪除 Microsoft.IoTSpaces Graph 資源 |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

Azure 服務： [Key Vault](../key-vault/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.KeyVault/register/action | 註冊訂用帳戶 |
> | 動作 | Microsoft.KeyVault/unregister/action | 取消註冊訂用帳戶 |
> |  | **checkNameAvailability** |  |
> | 動作 | Microsoft.KeyVault/checkNameAvailability/read | 確認 Key Vault 名稱有效，且並非使用中 |
> |  | **deletedVaults** |  |
> | 動作 | Microsoft.KeyVault/deletedVaults/read | 檢視虛刪除之 Key Vault 的屬性 |
> |  | **hsmPools** |  |
> | 動作 | Microsoft.KeyVault/hsmPools/read | 檢視 HSM 集區的屬性 |
> | 動作 | Microsoft.KeyVault/hsmPools/write | 建立新的 HSM 集區，或更新現有 HSM 集區的屬性 |
> | 動作 | Microsoft.KeyVault/hsmPools/delete | 刪除 HSM 集區 |
> | 動作 | Microsoft.KeyVault/hsmPools/joinVault/action | 將金鑰保存庫加入至 HSM 集區 |
> |  | **堆放** |  |
> | 動作 | Microsoft.KeyVault/locations/deleteVirtualNetworkOrSubnets/action | 通知 Microsoft.KeyVault 正在刪除虛擬網路或子網路 |
> |  | **位置/deletedVaults** |  |
> | 動作 | Microsoft.KeyVault/locations/deletedVaults/read | 檢視虛刪除之 Key Vault 的屬性 |
> | 動作 | Microsoft.KeyVault/locations/deletedVaults/purge/action | 清除虛刪除的 Key Vault |
> |  | **位置/operationResults** |  |
> | 動作 | Microsoft.KeyVault/locations/operationResults/read | 檢查長時間執行之作業的結果 |
> |  | **業務** |  |
> | 動作 | Microsoft.KeyVault/operations/read | 列出可以對 Microsoft.KeyVault 資源提供者執行的作業 |
> |  | **庫** |  |
> | 動作 | Microsoft.KeyVault/vaults/read | 檢視 Key Vault 的屬性 |
> | 動作 | Microsoft.KeyVault/vaults/write | 建立新的 Key Vault，或更新現有 Key Vault 的屬性 |
> | 動作 | Microsoft.KeyVault/vaults/delete | 刪除 Key Vault |
> | 動作 | Microsoft.KeyVault/vaults/deploy/action | 在部署 Azure 資源時啟用 Key Vault 中祕密的存取權 |
> |  | **保存庫/accessPolicies** |  |
> | 動作 | Microsoft.KeyVault/vaults/accessPolicies/write | 藉由合併或取代來更新現有存取原則，或在保存庫中新增存取原則。 |
> |  | **保存庫/eventGridFilters** |  |
> | 動作 | KeyVault/vault/eventGridFilters/read | 通知 KeyVault，正在查看 Key Vault 的 EventGrid 訂用帳戶 |
> | 動作 | KeyVault/vault/eventGridFilters/write | 通知 KeyVault，正在建立 Key Vault 的新 EventGrid 訂用帳戶 |
> | 動作 | KeyVault/vault/eventGridFilters/delete | 通知 KeyVault，正在刪除 Key Vault 的 EventGrid 訂用帳戶 |
> |  | **保存庫/秘密** |  |
> | 動作 | Microsoft.KeyVault/vaults/secrets/read | 查看密碼的內容，而不是其值。 |
> | 動作 | Microsoft.KeyVault/vaults/secrets/write | 建立新的密碼，或更新現有密碼的值。 |
> |  | **保存庫/certificatecas** |  |
> | DataAction | KeyVault/vault/certificatecas/delete | 刪除憑證 Issuser |
> | DataAction | KeyVault/vault/certificatecas/read | 讀取憑證 Issuser |
> | DataAction | KeyVault/vault/certificatecas/write | 寫入憑證 Issuser |
> |  | **保存庫/certificatecontacts** |  |
> | DataAction | KeyVault/vault/certificatecontacts/write | 管理憑證連絡人 |
> |  | **保存庫/憑證** |  |
> | DataAction | KeyVault/保存庫/憑證/刪除 | 刪除憑證。 |
> | DataAction | KeyVault/保存庫/憑證/讀取 | 列出指定金鑰保存庫中的憑證，或取得憑證的相關資訊。 |
> | DataAction | KeyVault/保存庫/憑證/備份/動作 | 建立憑證的備份檔案。 檔案可以用來還原相同訂用帳戶 Key Vault 中的憑證。 可能適用限制。 |
> | DataAction | KeyVault/保存庫/憑證/清除/動作 | 清除憑證，使其無法復原。 |
> | DataAction | KeyVault/保存庫/憑證/更新/動作 | 更新與給定憑證相關聯的指定屬性。 |
> | DataAction | KeyVault/保存庫/憑證/建立/動作 | 建立新的憑證。 |
> | DataAction | KeyVault/保存庫/憑證/匯入/動作 | 將現有的有效憑證（包含私密金鑰）匯入 Azure Key Vault。 要匯入的憑證可以是 PFX 或 PEM 格式。 |
> | DataAction | KeyVault/保存庫/憑證/復原/動作 | 復原已刪除的憑證。 作業會執行刪除作業的反轉。 此作業適用于已啟用虛刪除的保存庫，而且必須在保留間隔期間發出。 |
> | DataAction | KeyVault/保存庫/憑證/還原/動作 | 將備份的憑證及其所有版本還原到保存庫。 |
> |  | **保存庫/金鑰** |  |
> | DataAction | KeyVault/保存庫/金鑰/讀取 | 列出指定之保存庫中的金鑰，或讀取索引鍵的屬性和公開材料。<br>對於非對稱金鑰，這項作業會公開公開金鑰，並包含執行公開金鑰演算法的能力，例如加密和驗證簽章。<br>私密金鑰和對稱金鑰永遠不會公開。 |
> | DataAction | KeyVault/保存庫/金鑰/更新/動作 | 更新與指定索引鍵相關聯的指定屬性。 |
> | DataAction | KeyVault/保存庫/金鑰/建立/動作 | 建立新的金鑰。 |
> | DataAction | KeyVault/保存庫/金鑰/匯入/動作 | 匯出外部建立的金鑰、加以儲存，然後將金鑰參數和屬性傳回至用戶端。 |
> | DataAction | KeyVault/保存庫/金鑰/復原/動作 | 復原已刪除的金鑰。 作業會執行刪除作業的反轉。 此作業適用于已啟用虛刪除的保存庫，而且必須在保留間隔期間發出。 |
> | DataAction | KeyVault/保存庫/金鑰/還原/動作 | 將備份的金鑰及其所有版本還原到保存庫。 |
> | DataAction | KeyVault/保存庫/金鑰/刪除 | 刪除金鑰。 |
> | DataAction | KeyVault/保存庫/金鑰/備份/動作 | 建立金鑰的備份檔案。 檔案可以用來還原相同訂用帳戶 Key Vault 中的金鑰。 可能適用限制。 |
> | DataAction | KeyVault/保存庫/金鑰/清除/動作 | 清除金鑰，使其無法復原。 |
> | DataAction | KeyVault/保存庫/金鑰/加密/動作 | 使用金鑰加密純文字。 請注意，如果金鑰是非對稱的，這項作業可以由具有讀取權限的主體來執行。 |
> | DataAction | KeyVault/保存庫/金鑰/解密/動作 | 使用金鑰將加密文字解密。 |
> | DataAction | KeyVault/保存庫/金鑰/包裝/動作 | 包裝具有 Key Vault 金鑰的對稱金鑰。 請注意，如果 Key Vault 金鑰是非對稱的，則可以使用讀取權限來執行此作業。 |
> | DataAction | KeyVault/保存庫/金鑰/解除包裝/動作 | 使用 Key Vault 金鑰將對稱金鑰解除包裝。 |
> | DataAction | KeyVault/保存庫/金鑰/正負號/動作 | 使用金鑰簽署雜湊。 |
> | DataAction | KeyVault/保存庫/金鑰/驗證/動作 | 驗證雜湊。 請注意，如果金鑰是非對稱的，這項作業可以由具有讀取權限的主體來執行。 |
> |  | **保存庫/秘密** |  |
> | DataAction | KeyVault/保存庫/秘密/刪除 | 刪除秘密。 |
> | DataAction | KeyVault/保存庫/秘密/備份/動作 | 建立密碼的備份檔案。 檔案可以用來還原相同訂用帳戶 Key Vault 中的密碼。 可能適用限制。 |
> | DataAction | KeyVault/保存庫/秘密/清除/動作 | 清除秘密，使其無法復原。 |
> | DataAction | KeyVault/保存庫/秘密/更新/動作 | 更新與指定的密碼相關聯的指定屬性。 |
> | DataAction | KeyVault/保存庫/秘密/復原/動作 | 復原已刪除的秘密。 作業會執行刪除作業的反轉。 此作業適用于已啟用虛刪除的保存庫，而且必須在保留間隔期間發出。 |
> | DataAction | KeyVault/保存庫/秘密/還原/動作 | 將備份的密碼及其所有版本還原到保存庫。 |
> | DataAction | KeyVault/保存庫/秘密/readMetadata/action | 列出或查看秘密的內容，而不是其值。 |
> | DataAction | KeyVault/保存庫/秘密/getSecret/action | 取得密碼的值。 |
> | DataAction | KeyVault/保存庫/秘密/setSecret/action | 建立新的秘密。 |

## <a name="microsoftkusto"></a>Microsoft.Kusto

Azure 服務： [azure 資料總管](../data-explorer/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Kusto/register/action | 訂用帳戶註冊動作 |
> | 動作 | Kusto/Register/action | 向 Kusto 資源提供者註冊訂用帳戶。 |
> | 動作 | Kusto/取消註冊/動作 | 取消註冊 Kusto 資源提供者的訂用帳戶。 |
> |  | **叢集** |  |
> | 動作 | Microsoft.Kusto/Clusters/read | 讀取叢集資源。 |
> | 動作 | Microsoft.Kusto/Clusters/write | 寫入叢集資源。 |
> | 動作 | Microsoft.Kusto/Clusters/delete | 刪除叢集資源。 |
> | 動作 | Kusto/叢集/啟動/動作 | 啟動叢集。 |
> | 動作 | Kusto/叢集/停止/動作 | 停止叢集。 |
> | 動作 | Kusto/叢集/啟動/動作 | 啟動叢集。 |
> | 動作 | Kusto/叢集/停用/動作 | 停止叢集。 |
> | 動作 | Kusto/叢集/CheckNameAvailability/action | 檢查叢集名稱可用性。 |
> | 動作 | Kusto/叢集/DetachFollowerDatabases/action | 卸離進行中的資料庫。 |
> | 動作 | Kusto/叢集/ListFollowerDatabases/action | 列出進行中的資料庫。 |
> | 動作 | Kusto/叢集/DiagnoseVirtualNetwork/action | 針對服務所依存的外部資源，診斷其網路線上狀態。 |
> |  | **叢集/AttachedDatabaseConfigurations** |  |
> | 動作 | Kusto/叢集/AttachedDatabaseConfigurations/read | 讀取附加的資料庫設定資源。 |
> | 動作 | Kusto/叢集/AttachedDatabaseConfigurations/write | 寫入附加的資料庫設定資源。 |
> | 動作 | Kusto/叢集/AttachedDatabaseConfigurations/delete | 刪除附加的資料庫設定資源。 |
> |  | **叢集/資料庫** |  |
> | 動作 | Microsoft.Kusto/Clusters/Databases/read | 讀取資料庫資源。 |
> | 動作 | Microsoft.Kusto/Clusters/Databases/write | 寫入資料庫資源。 |
> | 動作 | Microsoft.Kusto/Clusters/Databases/delete | 刪除資料庫資源。 |
> | 動作 | Kusto/叢集/資料庫/ListPrincipals/action | 列出資料庫主體。 |
> | 動作 | Kusto/叢集/資料庫/AddPrincipals/action | 加入資料庫主體。 |
> | 動作 | Kusto/叢集/資料庫/RemovePrincipals/action | 移除資料庫主體。 |
> | 動作 | Kusto/叢集/資料庫/DataConnectionValidation/action | 驗證資料庫資料連線。 |
> | 動作 | Kusto/叢集/資料庫/CheckNameAvailability/action | 檢查給定類型的名稱可用性。 |
> | 動作 | Kusto/叢集/資料庫/EventHubConnectionValidation/action | 驗證資料庫事件中樞連接。 |
> |  | **叢集/資料庫/DataConnections** |  |
> | 動作 | Microsoft.Kusto/Clusters/Databases/DataConnections/read | 讀取資料連線資源。 |
> | 動作 | Microsoft.Kusto/Clusters/Databases/DataConnections/write | 寫入資料連線資源。 |
> | 動作 | Microsoft.Kusto/Clusters/Databases/DataConnections/delete | 刪除資料連線資源。 |
> |  | **叢集/資料庫/EventHubConnections** |  |
> | 動作 | Microsoft.Kusto/Clusters/Databases/EventHubConnections/read | 讀取事件中樞連接資源。 |
> | 動作 | Microsoft.Kusto/Clusters/Databases/EventHubConnections/write | 寫入事件中樞連接資源。 |
> | 動作 | Microsoft.Kusto/Clusters/Databases/EventHubConnections/delete | 刪除事件中樞連接資源。 |
> |  | **叢集/資料庫/PrincipalAssignments** |  |
> | 動作 | Kusto/叢集/資料庫/PrincipalAssignments/read | 讀取資料庫主體指派資源。 |
> | 動作 | Kusto/叢集/資料庫/PrincipalAssignments/write | 寫入資料庫主體指派資源。 |
> | 動作 | Kusto/叢集/資料庫/PrincipalAssignments/delete | 刪除資料庫主體指派資源。 |
> |  | **叢集/DataConnections** |  |
> | 動作 | Kusto/叢集/DataConnections/read | 讀取叢集的資料連線資源。 |
> | 動作 | Kusto/叢集/DataConnections/write | 寫入叢集的資料連線資源。 |
> | 動作 | Kusto/叢集/DataConnections/delete | 刪除叢集的資料連線資源。 |
> |  | **叢集/PrincipalAssignments** |  |
> | 動作 | Kusto/叢集/PrincipalAssignments/read | 讀取叢集主體指派資源。 |
> | 動作 | Kusto/叢集/PrincipalAssignments/write | 寫入叢集主體指派資源。 |
> | 動作 | Kusto/叢集/PrincipalAssignments/delete | 刪除叢集主體指派資源。 |
> |  | **群集/Sku** |  |
> | 動作 | Kusto/叢集/Sku/讀取 | 讀取叢集 SKU 資源。 |
> |  | **位置** |  |
> | 動作 | Kusto/位置/CheckNameAvailability/動作 | 檢查資源名稱可用性。 |
> | 動作 | Kusto/位置/GetNetworkPolicies/動作 | 取得網路意圖原則 |
> |  | **位置/operationresults** |  |
> | 動作 | Microsoft.Kusto/locations/operationresults/read | 讀取作業資源 |
> |  | **作業** |  |
> | 動作 | Microsoft.Kusto/Operations/read | 讀取作業資源 |
> |  | **SKU** |  |
> | 動作 | Kusto/Sku/讀取 | 讀取 SKU 資源。 |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

Azure 服務： [Azure 實驗室服務](../lab-services/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.LabServices/register/action | 註冊訂用帳戶 |
> |  | **labAccounts** |  |
> | 動作 | Microsoft.LabServices/labAccounts/delete | 刪除實驗室帳戶。 |
> | 動作 | Microsoft.LabServices/labAccounts/read | 讀取實驗室帳戶。 |
> | 動作 | Microsoft.LabServices/labAccounts/write | 新增或修改實驗室帳戶。 |
> | 動作 | Microsoft.LabServices/labAccounts/CreateLab/action | 在實驗室帳戶中建立實驗室。 |
> | 動作 | Microsoft.LabServices/labAccounts/GetRegionalAvailability/action | 取得在實驗室帳戶下設定的每個大小類別的區域可用性資訊 |
> | 動作 | LabServices/labAccounts/GetPricingAndAvailability/action | 取得實驗室帳戶的大小、地理位置和作業系統組合的價格和可用性。 |
> | 動作 | LabServices/labAccounts/GetRestrictionsAndUsage/action | 取得此訂用帳戶的核心限制和使用方式 |
> |  | **labAccounts/galleryImages** |  |
> | 動作 | Microsoft.LabServices/labAccounts/galleryImages/delete | 刪除資源庫映像。 |
> | 動作 | Microsoft.LabServices/labAccounts/galleryImages/read | 讀取資源庫映像。 |
> | 動作 | Microsoft.LabServices/labAccounts/galleryImages/write | 新增或修改資源庫映像。 |
> |  | **labAccounts/labs** |  |
> | 動作 | Microsoft.LabServices/labAccounts/labs/delete | 刪除實驗室。 |
> | 動作 | Microsoft.LabServices/labAccounts/labs/read | 讀取實驗室。 |
> | 動作 | Microsoft.LabServices/labAccounts/labs/write | 新增或修改實驗室。 |
> | 動作 | Microsoft.LabServices/labAccounts/labs/AddUsers/action | 將使用者新增至實驗室 |
> | 動作 | Microsoft.LabServices/labAccounts/labs/SendEmail/action | 傳送附有註冊連結的電子郵件給實驗室 |
> | 動作 | LabServices/labAccounts/labs/GetLabPricingAndAvailability/action | 取得此實驗室的每個實驗室單位定價，以及指出此實驗室是否可以相應增加的可用性。 |
> |  | **labAccounts/labs/Cloudtask.environmentsettings** |  |
> | 動作 | Microsoft.LabServices/labAccounts/labs/environmentSettings/delete | 刪除環境設定。 |
> | 動作 | Microsoft.LabServices/labAccounts/labs/environmentSettings/read | 讀取環境設定。 |
> | 動作 | Microsoft.LabServices/labAccounts/labs/environmentSettings/write | 新增或修改環境設定。 |
> | 動作 | Microsoft.LabServices/labAccounts/labs/environmentSettings/Publish/action | 根據實驗室/環境設定的目前狀態，佈建/取消佈建環境設定所需的資源。 |
> | 動作 | Microsoft.LabServices/labAccounts/labs/environmentSettings/Start/action | 啟動範本內的所有資源以啟動範本。 |
> | 動作 | Microsoft.LabServices/labAccounts/labs/environmentSettings/Stop/action | 透過停止範本中的所有資源來停止範本。 |
> | 動作 | LabServices/labAccounts/labs/Cloudtask.environmentsettings/SaveImage/action | 將目前的範本映射儲存至實驗室帳戶中的共用資源庫 |
> | 動作 | LabServices/labAccounts/labs/Cloudtask.environmentsettings/ResetPassword/action | 重設範本虛擬機器上的密碼。 |
> |  | **labAccounts/labs/Cloudtask.environmentsettings/環境** |  |
> | 動作 | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/delete | 刪除環境。 |
> | 動作 | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/read | 讀取環境。 |
> | 動作 | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Start/action | 啟動環境內的所有資源以啟動環境。 |
> | 動作 | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Stop/action | 停止環境內的所有資源以停止環境 |
> | 動作 | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/ResetPassword/action | 重設環境的使用者密碼 |
> |  | **labAccounts/labs/Cloudtask.environmentsettings/排程** |  |
> | 動作 | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/delete | 刪除排程。 |
> | 動作 | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/read | 讀取排程。 |
> | 動作 | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/write | 新增或修改排程。 |
> |  | **labAccounts/labs/使用者** |  |
> | 動作 | Microsoft.LabServices/labAccounts/labs/users/delete | 刪除使用者。 |
> | 動作 | Microsoft.LabServices/labAccounts/labs/users/read | 讀取使用者。 |
> | 動作 | Microsoft.LabServices/labAccounts/labs/users/write | 新增或修改使用者。 |
> |  | **labAccounts/共用資源庫** |  |
> | 動作 | Microsoft.LabServices/labAccounts/sharedGalleries/delete | 刪除共用資源庫。 |
> | 動作 | Microsoft.LabServices/labAccounts/sharedGalleries/read | 讀取共用資源庫。 |
> | 動作 | Microsoft.LabServices/labAccounts/sharedGalleries/write | 新增或修改共用資源庫。 |
> |  | **labAccounts/共用影像** |  |
> | 動作 | Microsoft.LabServices/labAccounts/sharedImages/delete | 刪除共用影像。 |
> | 動作 | Microsoft.LabServices/labAccounts/sharedImages/read | 讀取共用影像。 |
> | 動作 | Microsoft.LabServices/labAccounts/sharedImages/write | 新增或修改共用影像。 |
> |  | **位置/作業** |  |
> | 動作 | Microsoft.LabServices/locations/operations/read | 讀取作業。 |
> |  | **使用者** |  |
> | 動作 | Microsoft.LabServices/users/Register/action | 向受控實驗室註冊使用者 |
> | 動作 | LabServices/users/ListAllEnvironments/action | 列出使用者的所有環境 |
> | 動作 | Microsoft.LabServices/users/StartEnvironment/action | 啟動環境內的所有資源以啟動環境。 |
> | 動作 | Microsoft.LabServices/users/StopEnvironment/action | 停止環境內的所有資源以停止環境 |
> | 動作 | Microsoft.LabServices/users/ResetPassword/action | 重設環境的使用者密碼 |
> | 動作 | LabServices/users/UserSettings/action | 更新並傳回個人使用者設定。 |

## <a name="microsoftlogic"></a>Microsoft.Logic

Azure 服務： [Logic Apps](../logic-apps/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.Logic/register/action | 為指定的訂用帳戶註冊 Microsoft.Logic 資源提供者。 |
> |  | **integrationAccounts** |  |
> | 動作 | Microsoft.Logic/integrationAccounts/read | 讀取整合帳戶。 |
> | 動作 | Microsoft.Logic/integrationAccounts/write | 建立或更新整合帳戶。 |
> | 動作 | Microsoft.Logic/integrationAccounts/delete | 刪除整合帳戶。 |
> | 動作 | Microsoft.Logic/integrationAccounts/regenerateAccessKey/action | 重新產生存取金鑰祕密。 |
> | 動作 | Microsoft.Logic/integrationAccounts/listCallbackUrl/action | 取得整合帳戶的回呼 URL。 |
> | 動作 | Microsoft.Logic/integrationAccounts/listKeyVaultKeys/action | 取得金鑰保存庫中的金鑰。 |
> | 動作 | Microsoft.Logic/integrationAccounts/logTrackingEvents/action | 記錄整合帳戶中的追蹤事件。 |
> | 動作 | Microsoft.Logic/integrationAccounts/join/action | 加入整合帳戶。 |
> |  | **integrationAccounts/合約** |  |
> | 動作 | Microsoft.Logic/integrationAccounts/agreements/read | 讀取整合帳戶中的合約。 |
> | 動作 | Microsoft.Logic/integrationAccounts/agreements/write | 建立或更新整合帳戶中的合約。 |
> | 動作 | Microsoft.Logic/integrationAccounts/agreements/delete | 刪除整合帳戶中的合約。 |
> | 動作 | Microsoft.Logic/integrationAccounts/agreements/listContentCallbackUrl/action | 取得整合帳戶中合約內容的回呼 URL。 |
> |  | **integrationAccounts/元件** |  |
> | 動作 | Microsoft.Logic/integrationAccounts/assemblies/read | 讀取整合帳戶中的組件。 |
> | 動作 | Microsoft.Logic/integrationAccounts/assemblies/write | 建立或更新整合帳戶中的組件。 |
> | 動作 | Microsoft.Logic/integrationAccounts/assemblies/delete | 刪除整合帳戶中的組件。 |
> | 動作 | Microsoft.Logic/integrationAccounts/assemblies/listContentCallbackUrl/action | 取得整合帳戶中組件內容的回呼 URL。 |
> |  | **integrationAccounts/batchConfigurations** |  |
> | 動作 | Microsoft.Logic/integrationAccounts/batchConfigurations/read | 讀取整合帳戶中的批次設定。 |
> | 動作 | Microsoft.Logic/integrationAccounts/batchConfigurations/write | 建立或更新整合帳戶中的批次設定。 |
> | 動作 | Microsoft.Logic/integrationAccounts/batchConfigurations/delete | 刪除整合帳戶中的批次設定。 |
> |  | **integrationAccounts/憑證** |  |
> | 動作 | Microsoft.Logic/integrationAccounts/certificates/read | 讀取整合帳戶中的憑證。 |
> | 動作 | Microsoft.Logic/integrationAccounts/certificates/write | 建立或更新整合帳戶中的憑證。 |
> | 動作 | Microsoft.Logic/integrationAccounts/certificates/delete | 刪除整合帳戶中的憑證。 |
> |  | **integrationAccounts/群組** |  |
> | 動作 | Microsoft. 邏輯/integrationAccounts/群組/讀取 | 讀取整合帳戶中的群組。 |
> | 動作 | Microsoft. 邏輯/integrationAccounts/群組/寫入 | 建立或更新整合帳戶中的群組。 |
> | 動作 | Microsoft. 邏輯/integrationAccounts/群組/刪除 | 刪除整合帳戶中的群組。 |
> |  | **integrationAccounts/maps** |  |
> | 動作 | Microsoft.Logic/integrationAccounts/maps/read | 讀取整合帳戶中的對應。 |
> | 動作 | Microsoft.Logic/integrationAccounts/maps/write | 建立或更新整合帳戶中的對應。 |
> | 動作 | Microsoft.Logic/integrationAccounts/maps/delete | 刪除整合帳戶中的對應。 |
> | 動作 | Microsoft.Logic/integrationAccounts/maps/listContentCallbackUrl/action | 取得整合帳戶中對應內容的回呼 URL。 |
> |  | **integrationAccounts/合作夥伴** |  |
> | 動作 | Microsoft.Logic/integrationAccounts/partners/read | 讀取整合帳戶中的合作夥伴。 |
> | 動作 | Microsoft.Logic/integrationAccounts/partners/write | 建立或更新整合帳戶中的合作夥伴。 |
> | 動作 | Microsoft.Logic/integrationAccounts/partners/delete | 刪除整合帳戶中的合作夥伴。 |
> | 動作 | Microsoft.Logic/integrationAccounts/partners/listContentCallbackUrl/action | 取得整合帳戶中合作夥伴內容的回呼 URL。 |
> |  | **integrationAccounts/providers/Microsoft Insights/logDefinitions** |  |
> | 動作 | Microsoft.Logic/integrationAccounts/providers/Microsoft.Insights/logDefinitions/read | 讀取整合帳戶記錄定義。 |
> |  | **integrationAccounts/rosettaNetProcessConfigurations** |  |
> | 動作 | Microsoft. 邏輯/integrationAccounts/rosettaNetProcessConfigurations/read | 讀取整合帳戶中的 RosettaNet 進程設定。 |
> | 動作 | Microsoft. 邏輯/integrationAccounts/rosettaNetProcessConfigurations/write | 建立或更新整合帳戶中的 RosettaNet 進程設定。 |
> | 動作 | Microsoft. 邏輯/integrationAccounts/rosettaNetProcessConfigurations/delete | 刪除整合帳戶中的 RosettaNet 進程設定。 |
> |  | **integrationAccounts/排程** |  |
> | 動作 | Microsoft. 邏輯/integrationAccounts/排程/讀取 | 讀取整合帳戶中的排程。 |
> | 動作 | Microsoft. 邏輯/integrationAccounts/排程/寫入 | 建立或更新整合帳戶中的排程。 |
> | 動作 | Microsoft. 邏輯/integrationAccounts/排程/刪除 | 刪除整合帳戶中的排程。 |
> |  | **integrationAccounts/架構** |  |
> | 動作 | Microsoft.Logic/integrationAccounts/schemas/read | 讀取整合帳戶中的結構描述。 |
> | 動作 | Microsoft.Logic/integrationAccounts/schemas/write | 建立或更新整合帳戶中的結構描述。 |
> | 動作 | Microsoft.Logic/integrationAccounts/schemas/delete | 刪除整合帳戶中的結構描述。 |
> | 動作 | Microsoft.Logic/integrationAccounts/schemas/listContentCallbackUrl/action | 取得整合帳戶中結構描述內容的回呼 URL。 |
> |  | **integrationAccounts/會話** |  |
> | 動作 | Microsoft.Logic/integrationAccounts/sessions/read | 讀取整合帳戶中的會話。 |
> | 動作 | Microsoft.Logic/integrationAccounts/sessions/write | 建立或更新整合帳戶中的工作階段。 |
> | 動作 | Microsoft.Logic/integrationAccounts/sessions/delete | 刪除整合帳戶中的工作階段。 |
> |  | **integrationServiceEnvironments** |  |
> | 動作 | Microsoft.Logic/integrationServiceEnvironments/read | 讀取整合服務環境。 |
> | 動作 | Microsoft.Logic/integrationServiceEnvironments/write | 建立或更新整合服務環境。 |
> | 動作 | Microsoft.Logic/integrationServiceEnvironments/delete | 刪除整合服務環境。 |
> | 動作 | Microsoft.Logic/integrationServiceEnvironments/join/action | 聯結整合服務環境。 |
> |  | **integrationServiceEnvironments/availableManagedApis** |  |
> | 動作 | Microsoft. 邏輯/integrationServiceEnvironments/availableManagedApis/read | 讀取整合服務環境可用的受控 Api。 |
> |  | **integrationServiceEnvironments/managedApis** |  |
> | 動作 | Microsoft.Logic/integrationServiceEnvironments/managedApis/read | 讀取整合服務環境受控 API。 |
> | 動作 | Microsoft. 邏輯/integrationServiceEnvironments/managedApis/write | 建立或更新整合服務環境受控 API。 |
> | 動作 | Microsoft. 邏輯/integrationServiceEnvironments/managedApis/join/action | 加入整合服務環境受控 API。 |
> |  | **integrationServiceEnvironments/managedApis/apiOperations** |  |
> | 動作 | Microsoft.Logic/integrationServiceEnvironments/managedApis/apiOperations/read | 讀取整合服務環境受控 API 作業。 |
> |  | **integrationServiceEnvironments/managedApis/operationStatuses** |  |
> | 動作 | Microsoft. 邏輯/integrationServiceEnvironments/managedApis/operationStatuses/read | 讀取整合服務環境受控 API 作業狀態。 |
> |  | **integrationServiceEnvironments/operationStatuses** |  |
> | 動作 | Microsoft.Logic/integrationServiceEnvironments/operationStatuses/read | 讀取整合服務環境作業狀態。 |
> |  | **integrationServiceEnvironments/providers/Microsoft Insights/Metricdefinitions.listasync** |  |
> | 動作 | Microsoft.Logic/integrationServiceEnvironments/providers/Microsoft.Insights/metricDefinitions/read | 讀取整合服務環境計量定義。 |
> |  | **位置/工作流程** |  |
> | 動作 | Microsoft.Logic/locations/workflows/validate/action | 驗證工作流程。 |
> | 動作 | Microsoft.Logic/locations/workflows/recommendOperationGroups/action | 取得工作流程建議的作業群組。 |
> |  | **業務** |  |
> | 動作 | Microsoft.Logic/operations/read | 取得作業。 |
> |  | **工作流程** |  |
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
> |  | **工作流程/accessKeys** |  |
> | 動作 | Microsoft.Logic/workflows/accessKeys/read | 讀取存取金鑰。 |
> | 動作 | Microsoft.Logic/workflows/accessKeys/write | 建立或更新存取金鑰。 |
> | 動作 | Microsoft.Logic/workflows/accessKeys/delete | 刪除存取金鑰。 |
> | 動作 | Microsoft.Logic/workflows/accessKeys/list/action | 列出存取金鑰祕密。 |
> | 動作 | Microsoft.Logic/workflows/accessKeys/regenerate/action | 重新產生存取金鑰祕密。 |
> |  | **工作流程/偵測器** |  |
> | 動作 | Microsoft. 邏輯/工作流程/偵測器/讀取 | 讀取工作流程偵測器。 |
> |  | **工作流程/提供者/Microsoft Insights/diagnosticSettings** |  |
> | 動作 | Microsoft.Logic/workflows/providers/Microsoft.Insights/diagnosticSettings/read | 讀取工作流程診斷設定。 |
> | 動作 | Microsoft.Logic/workflows/providers/Microsoft.Insights/diagnosticSettings/write | 建立或更新工作流程診斷設定。 |
> |  | **工作流程/提供者/Microsoft Insights/logDefinitions** |  |
> | 動作 | Microsoft.Logic/workflows/providers/Microsoft.Insights/logDefinitions/read | 讀取工作流程記錄定義。 |
> |  | **工作流程/提供者/Microsoft Insights/Metricdefinitions.listasync** |  |
> | 動作 | Microsoft.Logic/workflows/providers/Microsoft.Insights/metricDefinitions/read | 讀取工作流程計量定義。 |
> |  | **工作流程/執行** |  |
> | 動作 | Microsoft.Logic/workflows/runs/read | 讀取工作流程的執行。 |
> | 動作 | Microsoft. 邏輯/工作流程/執行/刪除 | 刪除工作流程的執行。 |
> | 動作 | Microsoft.Logic/workflows/runs/cancel/action | 取消工作流程的執行。 |
> |  | **工作流程/執行/動作** |  |
> | 動作 | Microsoft.Logic/workflows/runs/actions/read | 讀取工作流程的執行動作。 |
> | 動作 | Microsoft.Logic/workflows/runs/actions/listExpressionTraces/action | 取得工作流程執行動作運算式追蹤。 |
> |  | **工作流程/執行/動作/重複** |  |
> | 動作 | Microsoft.Logic/workflows/runs/actions/repetitions/read | 讀取工作流程執行動作重複作業。 |
> | 動作 | Microsoft.Logic/workflows/runs/actions/repetitions/listExpressionTraces/action | 取得工作流程執行動作重複作業運算式追蹤。 |
> |  | **工作流程/執行/動作/重複/requestHistories** |  |
> | 動作 | Microsoft.Logic/workflows/runs/actions/repetitions/requestHistories/read | 讀取工作流程執行重複動作要求歷程記錄。 |
> |  | **工作流程/執行/動作/requestHistories** |  |
> | 動作 | Microsoft.Logic/workflows/runs/actions/requestHistories/read | 讀取工作流程執行動作要求歷程記錄。 |
> |  | **工作流程/執行/動作/scoperepetitions** |  |
> | 動作 | Microsoft.Logic/workflows/runs/actions/scoperepetitions/read | 讀取工作流程執行動作範圍重複作業。 |
> |  | **工作流程/執行/作業** |  |
> | 動作 | Microsoft.Logic/workflows/runs/operations/read | 讀取工作流程的執行作業狀態。 |
> |  | **工作流程/觸發程式** |  |
> | 動作 | Microsoft.Logic/workflows/triggers/read | 讀取觸發程序。 |
> | 動作 | Microsoft.Logic/workflows/triggers/run/action | 執行觸發程序。 |
> | 動作 | Microsoft.Logic/workflows/triggers/reset/action | 重設觸發程序。 |
> | 動作 | Microsoft.Logic/workflows/triggers/setState/action | 設定觸發程序狀態。 |
> | 動作 | Microsoft.Logic/workflows/triggers/listCallbackUrl/action | 取得觸發程序的回呼 URL。 |
> |  | **工作流程/觸發程式/歷程記錄** |  |
> | 動作 | Microsoft.Logic/workflows/triggers/histories/read | 讀取觸發程序歷程記錄。 |
> | 動作 | Microsoft.Logic/workflows/triggers/histories/resubmit/action | 重新提交工作流程的觸發程序。 |
> |  | **工作流程/版本** |  |
> | 動作 | Microsoft.Logic/workflows/versions/read | 讀取工作流程版本。 |
> |  | **工作流程/版本/觸發程式** |  |
> | 動作 | Microsoft.Logic/workflows/versions/triggers/listCallbackUrl/action | 取得觸發程序的回呼 URL。 |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

Azure 服務： [Machine Learning Studio](../machine-learning/studio/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.MachineLearning/register/action | 針對 Machine Learning Web 服務資源提供者註冊訂用帳戶，並讓您能夠建立 Web 服務。 |
> | 動作 | Microsoft.MachineLearning/webServices/action | 建立所支援區域的區域性 Web 服務屬性 |
> |  | **commitmentPlans** |  |
> | 動作 | Microsoft.MachineLearning/commitmentPlans/read | 讀取任何 Machine Learning 承諾用量方案 |
> | 動作 | Microsoft.MachineLearning/commitmentPlans/write | 建立或更新任何 Machine Learning 承諾用量方案 |
> | 動作 | Microsoft.MachineLearning/commitmentPlans/delete | 刪除任何 Machine Learning 承諾用量方案 |
> | 動作 | Microsoft.MachineLearning/commitmentPlans/join/action | 加入任何 Machine Learning 承諾用量方案 |
> |  | **commitmentPlans/commitmentAssociations** |  |
> | 動作 | Microsoft.MachineLearning/commitmentPlans/commitmentAssociations/read | 讀取任何 Machine Learning 承諾用量方案關聯 |
> | 動作 | Microsoft.MachineLearning/commitmentPlans/commitmentAssociations/move/action | 移動任何 Machine Learning 承諾用量方案關聯 |
> |  | **位置/operationresults** |  |
> | 動作 | Microsoft.MachineLearning/locations/operationresults/read | 取得 Machine Learning 作業的結果 |
> |  | **位置/operationsstatus** |  |
> | 動作 | Microsoft.MachineLearning/locations/operationsstatus/read | 取得執行中 Machine Learning 作業的狀態 |
> |  | **業務** |  |
> | 動作 | Microsoft.MachineLearning/operations/read | 取得 Machine Learning 作業 |
> |  | **sku** |  |
> | 動作 | Microsoft.MachineLearning/skus/read | 取得 Machine Learning 承諾用量方案 SKU |
> |  | **webServices** |  |
> | 動作 | Microsoft.MachineLearning/webServices/read | 讀取任何 Machine Learning Web 服務 |
> | 動作 | Microsoft.MachineLearning/webServices/write | 建立或更新任何 Machine Learning Web 服務 |
> | 動作 | Microsoft.MachineLearning/webServices/delete | 刪除任何 Machine Learning Web 服務 |
> |  | **webServices/listkeys** |  |
> | 動作 | Microsoft.MachineLearning/webServices/listkeys/read | 取得 Machine Learning Web 服務的金鑰 |
> |  | **環境** |  |
> | 動作 | Microsoft.MachineLearning/Workspaces/read | 讀取任何 Machine Learning 工作區 |
> | 動作 | Microsoft.MachineLearning/Workspaces/write | 建立或更新任何 Machine Learning 工作區 |
> | 動作 | Microsoft.MachineLearning/Workspaces/delete | 刪除任何 Machine Learning 工作區 |
> | 動作 | Microsoft.MachineLearning/Workspaces/listworkspacekeys/action | 列出 Machine Learning 工作區的金鑰 |
> | 動作 | Microsoft.MachineLearning/Workspaces/resyncstoragekeys/action | 重新同步處理針對 Machine Learning 工作區所設定之儲存體帳戶的金鑰 |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

Azure 服務： [Machine Learning 服務](../machine-learning/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.MachineLearningServices/register/action | 為機器學習服務資源提供者註冊訂用帳戶 |
> |  | **堆放** |  |
> | 動作 | Microsoft.machinelearningservices/位置/updateQuotas/動作 | 更新工作區中每個 VM 系列的配額。 |
> |  | **位置/computeoperationsstatus** |  |
> | 動作 | Microsoft.machinelearningservices/位置/computeoperationsstatus/讀取 | 取得特定計算作業的狀態 |
> |  | **位置/配額** |  |
> | 動作 | Microsoft.machinelearningservices/位置/配額/讀取 | 根據 VMFamily 取得目前指派的工作區配額。 |
> |  | **位置/使用方式** |  |
> | 動作 | Microsoft.MachineLearningServices/locations/usages/read | 訂用帳戶中的 aml 計算資源的使用量報告 |
> |  | **位置/的 vmsizes** |  |
> | 動作 | Microsoft.MachineLearningServices/locations/vmsizes/read | 取得支援的 VM 大小 |
> |  | **位置/workspaceOperationsStatus** |  |
> | 動作 | Microsoft.MachineLearningServices/locations/workspaceOperationsStatus/read | 取得特定工作區作業的狀態 |
> |  | **環境** |  |
> | 動作 | Microsoft.MachineLearningServices/workspaces/read | 取得機器學習服務工作區 |
> | 動作 | Microsoft.MachineLearningServices/workspaces/write | 建立或更新機器學習服務工作區 |
> | 動作 | Microsoft.MachineLearningServices/workspaces/delete | 刪除機器學習服務工作區 |
> | 動作 | Microsoft.MachineLearningServices/workspaces/listKeys/action | 列出機器學習服務工作區的祕密 |
> | 動作 | Microsoft.machinelearningservices/workspace/PrivateEndpointConnectionsApproval/action | 核准或拒絕連線至 Microsoft 的私人端點資源。網路提供者的連接 |
> |  | **工作區/計算** |  |
> | 動作 | Microsoft.MachineLearningServices/workspaces/computes/read | 取得機器學習服務工作區中的計算資源 |
> | 動作 | Microsoft.MachineLearningServices/workspaces/computes/write | 在機器學習服務工作區中建立或更新計算資源 |
> | 動作 | Microsoft.MachineLearningServices/workspaces/computes/delete | 刪除機器學習服務工作區中的計算資源 |
> | 動作 | Microsoft.MachineLearningServices/workspaces/computes/listKeys/action | 列出機器學習服務工作區中的計算資源祕密 |
> | 動作 | Microsoft.MachineLearningServices/workspaces/computes/listNodes/action | 列出機器學習服務工作區中的計算資源節點 |
> | 動作 | Microsoft.machinelearningservices/工作區/計算/開始/動作 | 在 Machine Learning 服務工作區中啟動計算資源 |
> | 動作 | Microsoft.machinelearningservices/工作區/計算/停止/動作 | 停止 Machine Learning 服務工作區中的計算資源 |
> | 動作 | Microsoft.machinelearningservices/工作區/計算/重新開機/動作 | 重新開機 Machine Learning Services 工作區中的計算資源 |
> |  | **工作區/eventGridFilters** |  |
> | 動作 | Microsoft.machinelearningservices/workspace/eventGridFilters/read | 取得特定工作區的事件方格篩選 |
> |  | **工作區/功能** |  |
> | 動作 | Microsoft.machinelearningservices/工作區/功能/讀取 | 取得 Machine Learning 服務工作區的所有已啟用功能 |
> |  | **工作區/privateEndpointConnectionProxies** |  |
> | 動作 | Microsoft.machinelearningservices/workspace/privateEndpointConnectionProxies/read | 將連線 proxy 的狀態，流覽至 Microsoft 的私人端點資源。網路提供者 |
> | 動作 | Microsoft.machinelearningservices/workspace/privateEndpointConnectionProxies/write | 將連接 proxy 的狀態變更為 Microsoft 的私人端點資源。網路提供者 |
> | 動作 | Microsoft.machinelearningservices/workspace/privateEndpointConnectionProxies/delete | 將連線 proxy 刪除至 Microsoft 的私人端點資源。網路提供者 |
> | 動作 | Microsoft.machinelearningservices/workspace/privateEndpointConnectionProxies/validate/action | 驗證連線 proxy 是否為 Microsoft 的私人端點資源。網路提供者 |
> |  | **工作區/privateEndpointConnections** |  |
> | 動作 | Microsoft.machinelearningservices/workspace/privateEndpointConnections/read | 查看與 Microsoft 的私人端點資源的線上狀態。網路提供者 |
> | 動作 | Microsoft.machinelearningservices/workspace/privateEndpointConnections/write | 將連接的狀態變更為 Microsoft 的私人端點資源。網路提供者 |
> | 動作 | Microsoft.machinelearningservices/workspace/privateEndpointConnections/delete | 刪除與 Microsoft 的私人端點資源的連接。網路提供者 |
> |  | **工作區/privateLinkResources** |  |
> | 動作 | Microsoft.machinelearningservices/workspace/privateLinkResources/read | 取得 Machine Learning 服務工作區的指定實例可用的私用連結資源 |
> |  | **工作區/datadriftdetectors** |  |
> | DataAction | Microsoft.machinelearningservices/workspace/datadriftdetectors/read | 取得 Machine Learning Services 工作區中的資料漂移偵測器 |
> | DataAction | Microsoft.machinelearningservices/workspace/datadriftdetectors/write | 在 Machine Learning Services 工作區中建立或更新資料漂移偵測器 |
> |  | **工作區/資料集/已註冊** |  |
> | DataAction | Microsoft.machinelearningservices/工作區/資料集/已註冊/已讀取 | 在 Machine Learning Services 工作區中取得已註冊的資料集 |
> | DataAction | Microsoft.machinelearningservices/工作區/資料集/已註冊/寫入 | 在 Machine Learning Services 工作區中建立或更新已註冊的資料集 |
> | DataAction | Microsoft.machinelearningservices/工作區/資料集/已註冊/刪除 | 刪除 Machine Learning Services 工作區中已註冊的資料集 |
> |  | **工作區/資料集/已註冊/預覽** |  |
> | DataAction | Microsoft.machinelearningservices/工作區/資料集/已註冊/預覽/已讀取 | 取得 Machine Learning Services 工作區中已註冊資料集的資料集預覽 |
> |  | **工作區/資料集/已註冊/設定檔** |  |
> | DataAction | Microsoft.machinelearningservices/工作區/資料集/已註冊/設定檔/讀取 | 取得 Machine Learning Services 工作區中已註冊資料集的資料集設定檔 |
> | DataAction | Microsoft.machinelearningservices/工作區/資料集/已註冊/設定檔/寫入 | 在 Machine Learning Services 工作區中，建立或更新已註冊資料集的資料集設定檔 |
> |  | **工作區/資料集/已註冊/架構** |  |
> | DataAction | Microsoft.machinelearningservices/工作區/資料集/已註冊/架構/讀取 | 取得 Machine Learning Services 工作區中已註冊之資料集的資料集架構 |
> |  | **工作區/資料集/已取消註冊** |  |
> | DataAction | Microsoft.machinelearningservices/工作區/資料集/未註冊/已讀取 | 在 Machine Learning Services 工作區中取得未註冊的資料集 |
> | DataAction | Microsoft.machinelearningservices/工作區/資料集/未註冊/寫入 | 在 Machine Learning Services 工作區中建立或更新未註冊的資料集 |
> | DataAction | Microsoft.machinelearningservices/工作區/資料集/未註冊/刪除 | 在 Machine Learning Services 工作區中刪除未註冊的資料集 |
> |  | **工作區/資料集/未註冊/預覽** |  |
> | DataAction | Microsoft.machinelearningservices/工作區/資料集/未註冊/預覽/讀取 | 取得 Machine Learning Services 工作區中未註冊資料集的資料集預覽 |
> |  | **工作區/資料集/未註冊/設定檔** |  |
> | DataAction | Microsoft.machinelearningservices/工作區/資料集/未註冊/設定檔/讀取 | 取得 Machine Learning Services 工作區中未註冊資料集的資料集設定檔 |
> | DataAction | Microsoft.machinelearningservices/工作區/資料集/未註冊/設定檔/寫入 | 在 Machine Learning Services 工作區中，建立或更新未註冊資料集的資料集設定檔 |
> |  | **工作區/資料集/未註冊/架構** |  |
> | DataAction | Microsoft.machinelearningservices/工作區/資料集/未註冊/架構/讀取 | 在 Machine Learning Services 工作區中，取得未註冊資料集的資料集架構 |
> |  | **工作區/資料存放區** |  |
> | DataAction | Microsoft.machinelearningservices/workspace/資料存放區/read | 取得 Machine Learning Services 工作區中的資料存放區 |
> | DataAction | Microsoft.machinelearningservices/workspace/資料存放區/write | 在 Machine Learning Services 工作區中建立或更新資料存放區 |
> | DataAction | Microsoft.machinelearningservices/workspace/資料存放區/delete | 刪除 Machine Learning Services 工作區中的資料存放區 |
> |  | **工作區/端點/管線** |  |
> | DataAction | Microsoft.machinelearningservices/工作區/端點/管線/讀取 | 取得 Machine Learning 服務工作區中的已發佈管線和管線端點 |
> | DataAction | Microsoft.machinelearningservices/工作區/端點/管線/寫入 | 在 Machine Learning Services 工作區中建立或更新已發佈的管線和管線端點 |
> |  | **工作區/環境** |  |
> | DataAction | Microsoft.machinelearningservices/工作區/環境/讀取 | 取得 Machine Learning Services 工作區中的環境 |
> | DataAction | Microsoft.machinelearningservices/工作區/環境/readSecrets/action | 在 Machine Learning Services 工作區中取得具有秘密的環境 |
> | DataAction | Microsoft.machinelearningservices/工作區/環境/寫入 | 在 Machine Learning Services 工作區中建立或更新環境 |
> | DataAction | Microsoft.machinelearningservices/工作區/環境/組建/動作 | 在 Machine Learning Services 工作區中建立環境 |
> |  | **工作區/實驗** |  |
> | DataAction | Microsoft.machinelearningservices/工作區/實驗/讀取 | 取得 Machine Learning Services 工作區中的實驗 |
> | DataAction | Microsoft.machinelearningservices/工作區/實驗/寫入 | 在 Machine Learning Services 工作區中建立或更新實驗 |
> | DataAction | Microsoft.machinelearningservices/工作區/實驗/刪除 | 刪除 Machine Learning Services 工作區中的實驗 |
> |  | **工作區/實驗/執行** |  |
> | DataAction | Microsoft.machinelearningservices/工作區/實驗/執行/讀取 | 取得在 Machine Learning Services 工作區中執行 |
> | DataAction | Microsoft.machinelearningservices/工作區/實驗/執行/寫入 | 在 Machine Learning Services 工作區中建立或更新執行 |
> |  | **工作區/實驗/執行/scriptRun** |  |
> | DataAction | Microsoft.machinelearningservices/工作區/實驗/執行/scriptRun/提交/動作 | 在 Machine Learning Services 工作區中建立或更新腳本執行 |
> |  | **工作區/標籤** |  |
> | DataAction | Microsoft.machinelearningservices/工作區/標記/匯出/動作 | 在 Machine Learning Services 工作區中匯出標記專案的標籤 |
> |  | **工作區/標記/標籤** |  |
> | DataAction | Microsoft.machinelearningservices/工作區/標記/標籤/讀取 | 取得 Machine Learning Services 工作區中標記專案的標籤 |
> | DataAction | Microsoft.machinelearningservices/工作區/標記/標籤/寫入 | 在 Machine Learning Services 工作區中建立標記專案的標籤 |
> | DataAction | Microsoft.machinelearningservices/工作區/標記/標籤/拒絕/動作 | 拒絕 Machine Learning Services 工作區中標記專案的標籤 |
> |  | **工作區/標籤/專案** |  |
> | DataAction | Microsoft.machinelearningservices/工作區/標籤/專案/讀取 | 在 Machine Learning Services 工作區中取得標籤專案 |
> | DataAction | Microsoft.machinelearningservices/工作區/標籤/專案/寫入 | 在 Machine Learning Services 工作區中建立或更新標籤專案 |
> | DataAction | Microsoft.machinelearningservices/工作區/標籤/專案/刪除 | 刪除 Machine Learning Services 工作區中的標籤專案 |
> |  | **工作區/標記/專案/摘要** |  |
> | DataAction | Microsoft.machinelearningservices/工作區/標記/專案/摘要/讀取 | 在 Machine Learning Services 工作區中取得標籤專案摘要 |
> |  | **工作區/中繼資料/構件** |  |
> | DataAction | Microsoft.machinelearningservices/工作區/中繼資料/構件/讀取 | 取得 Machine Learning Services 工作區中的成品 |
> | DataAction | Microsoft.machinelearningservices/工作區/中繼資料/構件/寫入 | 在 Machine Learning Services 工作區中建立或更新構件 |
> | DataAction | Microsoft.machinelearningservices/工作區/中繼資料/構件/delete | 刪除 Machine Learning Services 工作區中的成品 |
> |  | **工作區/中繼資料/秘密** |  |
> | DataAction | Microsoft.machinelearningservices/工作區/中繼資料/秘密/讀取 | 取得 Machine Learning Services 工作區中的秘密 |
> | DataAction | Microsoft.machinelearningservices/工作區/中繼資料/秘密/寫入 | 在 Machine Learning Services 工作區中建立或更新秘密 |
> | DataAction | Microsoft.machinelearningservices/工作區/中繼資料/秘密/刪除 | 刪除 Machine Learning Services 工作區中的秘密 |
> |  | **工作區/中繼資料/快照集** |  |
> | DataAction | Microsoft.machinelearningservices/工作區/中繼資料/快照/讀取 | 取得 Machine Learning Services 工作區中的快照集 |
> | DataAction | Microsoft.machinelearningservices/工作區/中繼資料/快照/寫入 | 在 Machine Learning Services 工作區中建立或更新快照集 |
> | DataAction | Microsoft.machinelearningservices/工作區/中繼資料/快照/刪除 | 刪除 Machine Learning Services 工作區中的快照集 |
> |  | **工作區/模型** |  |
> | DataAction | Microsoft.machinelearningservices/工作區/模型/讀取 | 取得 Machine Learning Services 工作區中的模型 |
> | DataAction | Microsoft.machinelearningservices/工作區/模型/寫入 | 在 Machine Learning Services 工作區中建立或更新模型 |
> | DataAction | Microsoft.machinelearningservices/工作區/模型/刪除 | 刪除 Machine Learning Services 工作區中的模型 |
> | DataAction | Microsoft.machinelearningservices/工作區/模型/套件/動作 | 封裝 Machine Learning Services 工作區中的模型 |
> |  | **工作區/模組** |  |
> | DataAction | Microsoft.machinelearningservices/工作區/模組/讀取 | 取得 Machine Learning Services 工作區中的模組 |
> | DataAction | Microsoft.machinelearningservices/工作區/模組/寫入 | 在 Machine Learning Services 工作區中建立或更新模組 |
> |  | **工作區/pipelinedrafts** |  |
> | DataAction | Microsoft.machinelearningservices/workspace/pipelinedrafts/read | 取得 Machine Learning Services 工作區中的管線草稿 |
> | DataAction | Microsoft.machinelearningservices/workspace/pipelinedrafts/write | 在 Machine Learning Services 工作區中建立或更新管線草稿 |
> | DataAction | Microsoft.machinelearningservices/workspace/pipelinedrafts/delete | 刪除 Machine Learning Services 工作區中的管線草稿 |
> |  | **工作區/服務** |  |
> | DataAction | Microsoft.machinelearningservices/工作區/服務/讀取 | 取得 Machine Learning Services 工作區中的服務 |
> |  | **工作區/服務/aci** |  |
> | DataAction | Microsoft.machinelearningservices/工作區/服務/aci/寫入 | 建立或更新 Machine Learning Services 工作區中的 ACI 服務 |
> | DataAction | Microsoft.machinelearningservices/workspace/services/aci/listkeys/action | 列出 Machine Learning Services 工作區中 ACI 服務的索引鍵 |
> | DataAction | Microsoft.machinelearningservices/工作區/服務/aci/刪除 | 刪除 Machine Learning Services 工作區中的 ACI 服務 |
> |  | **工作區/服務/aks/devtest** |  |
> | DataAction | Microsoft.machinelearningservices/workspace/services/aks/devtest/write | 在 Machine Learning Services 工作區中建立或更新 devtest AKS services |
> | DataAction | Microsoft.machinelearningservices/workspace/services/aks/devtest/listkeys/action | 列出 Machine Learning Services 工作區中 devtest AKS services 的金鑰 |
> | DataAction | Microsoft.machinelearningservices/workspace/services/aks/devtest/delete | 刪除 Machine Learning Services 工作區中的 devtest AKS services |
> | DataAction | Microsoft.machinelearningservices/workspace/services/aks/devtest/分數/動作 | Machine Learning 服務工作區中的分數 devtest AKS services |
> |  | **工作區/服務/aks/生產** |  |
> | DataAction | Microsoft.machinelearningservices/workspace/services/aks/生產/寫入 | 在 Machine Learning Services 工作區中建立或更新生產 AKS 服務 |
> | DataAction | Microsoft.machinelearningservices/workspace/services/aks/生產/listkeys/action | 列出 Machine Learning Services 工作區中生產 AKS 服務的金鑰 |
> | DataAction | Microsoft.machinelearningservices/workspace/services/aks/生產/刪除 | 刪除 Machine Learning Services 工作區中的生產 AKS 服務 |
> | DataAction | Microsoft.machinelearningservices/工作區/服務/aks/生產/分數/動作 | 在 Machine Learning Services 工作區中評分生產 AKS 服務 |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

Azure 服務：[適用于 azure 資源的受控](../active-directory/managed-identities-azure-resources/index.yml)識別

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.ManagedIdentity/register/action | 為受控識別資源提供者註冊訂用帳戶 |
> |  | **那麼** |  |
> | 動作 | Microsoft.managedidentity/身分識別/讀取 | 取得現有系統指派的身分識別 |
> |  | **業務** |  |
> | 動作 | Microsoft.managedidentity/作業/讀取 | 列出 Microsoft.managedidentity 資源提供者上可用的作業 |
> |  | **userAssignedIdentities** |  |
> | 動作 | Microsoft.ManagedIdentity/userAssignedIdentities/read | 取得現有已指派使用者的身分識別 |
> | 動作 | Microsoft.ManagedIdentity/userAssignedIdentities/write | 建立新的已指派使用者的身分識別，或更新與現有已指派使用者之身分識別相關聯的標記 |
> | 動作 | Microsoft.ManagedIdentity/userAssignedIdentities/delete | 刪除現有已指派使用者的身分識別 |
> | 動作 | Microsoft.ManagedIdentity/userAssignedIdentities/assign/action | 用來將現有已指派使用者的身分識別指派給資源的 RBAC 動作 |

## <a name="microsoftmanagedservices"></a>ManagedServices

Azure 服務： [Azure 燈塔](../lighthouse/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | ManagedServices/register/action | 註冊至受管理的服務。 |
> | 動作 | ManagedServices/取消註冊/動作 | 從受管理的服務取消註冊。 |
> |  | **marketplaceRegistrationDefinitions** |  |
> | 動作 | ManagedServices/marketplaceRegistrationDefinitions/read | 抓取受控服務註冊定義的清單。 |
> |  | **業務** |  |
> | 動作 | ManagedServices/作業/讀取 | 抓取受控服務作業的清單。 |
> |  | **operationStatuses** |  |
> | 動作 | ManagedServices/operationStatuses/read | 讀取資源的作業狀態。 |
> |  | **registrationAssignments** |  |
> | 動作 | ManagedServices/registrationAssignments/read | 抓取受控服務註冊指派的清單。 |
> | 動作 | ManagedServices/registrationAssignments/write | 新增或修改受控服務註冊指派。 |
> | 動作 | ManagedServices/registrationAssignments/delete | 移除受控服務註冊指派。 |
> |  | **registrationDefinitions** |  |
> | 動作 | ManagedServices/registrationDefinitions/read | 抓取受控服務註冊定義的清單。 |
> | 動作 | ManagedServices/registrationDefinitions/write | 新增或修改受控服務註冊定義。 |
> | 動作 | ManagedServices/registrationDefinitions/delete | 移除受控服務註冊定義。 |

## <a name="microsoftmanagement"></a>Microsoft.Management

Azure 服務：[管理群組](../governance/management-groups/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.Management/checkNameAvailability/action | 檢查指定的管理群組名稱是否有效且唯一。 |
> | 動作 | Microsoft.Management/getEntities/action | 列出已驗證之使用者的所有實體 (管理群組、訂用帳戶等)。 |
> | 動作 | Microsoft.Management/register/action | 向 Microsoft.Management 註冊指定的訂用帳戶 |
> |  | **managementGroups** |  |
> | 動作 | Microsoft.Management/managementGroups/read | 列出已驗證之使用者的管理群組。 |
> | 動作 | Microsoft.Management/managementGroups/write | 建立或更新管理群組。 |
> | 動作 | Microsoft.Management/managementGroups/delete | 刪除管理群組。 |
> |  | **managementGroups/子代** |  |
> | 動作 | Microsoft 管理/managementGroups/下階/讀取 | 取得管理群組的所有子系（管理群組、訂閱）。 |
> |  | **managementGroups/設定** |  |
> | 動作 | Microsoft 管理/managementGroups/設定/讀取 | 列出現有的管理群組階層設定。 |
> | 動作 | Microsoft 管理/managementGroups/設定/寫入 | 建立或更新管理群組階層設定。 |
> | 動作 | Microsoft 管理/managementGroups/設定/刪除 | 刪除管理群組階層設定。 |
> |  | **managementGroups/訂用帳戶** |  |
> | 動作 | Microsoft.Management/managementGroups/subscriptions/write | 將現有的訂用帳戶關聯至管理群組。 |
> | 動作 | Microsoft.Management/managementGroups/subscriptions/delete | 從管理群組中取消訂用帳戶的關聯。 |

## <a name="microsoftmaps"></a>Microsoft.Maps

Azure 服務： [Azure 地圖服務](../azure-maps/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.Maps/register/action | 註冊提供者 |
> |  | **帳目** |  |
> | 動作 | Microsoft.Maps/accounts/write | 建立或更新地圖服務帳戶。 |
> | 動作 | Microsoft.Maps/accounts/read | 取得地圖服務帳戶。 |
> | 動作 | Microsoft.Maps/accounts/delete | 刪除地圖服務帳戶。 |
> | 動作 | Microsoft.Maps/accounts/listKeys/action | 列出地圖服務帳戶金鑰 |
> | 動作 | Microsoft.Maps/accounts/regenerateKey/action | 產生新地圖服務帳戶的主要或次要金鑰 |
> |  | **帳戶/eventGridFilters** |  |
> | 動作 | Microsoft.Maps/accounts/eventGridFilters/delete | 刪除事件格線篩選 |
> | 動作 | Microsoft.Maps/accounts/eventGridFilters/read | 取得事件格線篩選 |
> | 動作 | Microsoft.Maps/accounts/eventGridFilters/write | 建立或更新事件格線篩選 |
> |  | **業務** |  |
> | 動作 | Microsoft。地圖/作業/讀取 | 讀取提供者作業 |
> |  | **帳戶/資料** |  |
> | DataAction | Microsoft.Maps/accounts/data/read | 將資料讀取權限授與地圖服務帳戶。 |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

Azure 服務：核心

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.Marketplace/register/action | 在訂用帳戶中註冊 Microsoft.Marketplace 資源提供者。 |
> | 動作 | Microsoft Marketplace/privateStores/動作 | 更新 PrivateStore。 |
> |  | **offerTypes/發行者/優惠/方案/合約** |  |
> | 動作 | Microsoft.Marketplace/offerTypes/publishers/offers/plans/agreements/read | 傳回協議。 |
> | 動作 | Microsoft.Marketplace/offerTypes/publishers/offers/plans/agreements/write | 接受已簽署的協議。 |
> |  | **offerTypes/發行者/供應專案/方案/專案** |  |
> | 動作 | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/read | 傳回組態。 |
> | 動作 | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/write | 儲存組態。 |
> | 動作 | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/importImage/action | 將映像匯入至終端使用者的 ACR。 |
> |  | **privateStores** |  |
> | 動作 | Microsoft Marketplace/privateStores/write | 建立 PrivateStore。 |
> | 動作 | Microsoft Marketplace/privateStores/delete | 刪除 PrivateStore。 |
> | 動作 | Microsoft Marketplace/privateStores/優惠/動作 | PrivateStore 中的更新供應專案。 |
> |  | **privateStores/優惠** |  |
> | 動作 | Microsoft Marketplace/privateStores/提供/寫入 | 在 PrivateStore 中建立供應專案。 |
> | 動作 | Microsoft Marketplace/privateStores/優惠/刪除 | 從 PrivateStore 中刪除供應專案。 |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

Azure 服務：核心

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> |  | **ClassicDevServices** |  |
> | 動作 | Microsoft.MarketplaceApps/ClassicDevServices/read | 在傳統開發服務上進行 GET 作業。 |
> | 動作 | Microsoft.MarketplaceApps/ClassicDevServices/delete | 在傳統開發服務資源上進行 DELETE 作業。 |
> | 動作 | Microsoft.MarketplaceApps/ClassicDevServices/listSingleSignOnToken/action | 取得傳統開發服務的單一登入 URL。 |
> | 動作 | Microsoft.MarketplaceApps/ClassicDevServices/listSecrets/action | 取得傳統開發服務資源管理金鑰。 |
> | 動作 | Microsoft.MarketplaceApps/ClassicDevServices/regenerateKey/action | 產生傳統開發服務資源管理金鑰。 |
> |  | **作業** |  |
> | 動作 | Microsoft.MarketplaceApps/Operations/read | 讀取所有資源類型的作業。 |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

Azure 服務：核心

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> |  | **保密協定** |  |
> | 動作 | Microsoft.MarketplaceOrdering/agreements/read | 傳回指定訂用帳戶下的所有合約 |
> |  | **合約/優惠/方案** |  |
> | 動作 | Microsoft.MarketplaceOrdering/agreements/offers/plans/read | 傳回給定 Marketplace 項目的合約 |
> | 動作 | Microsoft.MarketplaceOrdering/agreements/offers/plans/sign/action | 簽署給定 Marketplace 項目的合約 |
> | 動作 | Microsoft.MarketplaceOrdering/agreements/offers/plans/cancel/action | 取消給定 Marketplace 項目的合約 |
> |  | **offertypes/發行者/優惠/方案/合約** |  |
> | 動作 | Microsoft.MarketplaceOrdering/offertypes/publishers/offers/plans/agreements/read | 取得指定 Marketplace 虛擬機器項目的合約 |
> | 動作 | Microsoft.MarketplaceOrdering/offertypes/publishers/offers/plans/agreements/write | 簽署或取消指定 Marketplace 虛擬機器項目的合約 |
> |  | **業務** |  |
> | 動作 | MarketplaceOrdering/作業/讀取 | 列出 API 中所有可能的作業 |

## <a name="microsoftmedia"></a>Microsoft.Media

Azure 服務：[媒體服務](../media-services/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.Media/register/action | 為媒體服務資源提供者註冊訂用帳戶，並讓您能夠建立媒體服務帳戶 |
> | 動作 | Microsoft.Media/unregister/action | 為媒體服務資源提供者取消註冊訂用帳戶 |
> | 動作 | Microsoft.Media/checknameavailability/action | 檢查媒體服務帳戶名稱是否可供使用 |
> |  | **windowsazure.mediaservices.extensions** |  |
> | 動作 | Microsoft.Media/mediaservices/read | 讀取任何媒體服務帳戶 |
> | 動作 | Microsoft.Media/mediaservices/write | 建立或更新任何媒體服務帳戶 |
> | 動作 | Microsoft.Media/mediaservices/delete | 刪除任何媒體服務帳戶 |
> | 動作 | Microsoft.Media/mediaservices/syncStorageKeys/action | 同步處理已連結之 Azure 儲存體帳戶的儲存體金鑰 |
> |  | **windowsazure.mediaservices.extensions/accountfilters** |  |
> | 動作 | Microsoft.Media/mediaservices/accountfilters/read | 讀取任何帳戶篩選條件 |
> | 動作 | Microsoft.Media/mediaservices/accountfilters/write | 建立或更新任何帳戶篩選條件 |
> | 動作 | Microsoft.Media/mediaservices/accountfilters/delete | 刪除任何帳戶篩選條件 |
> |  | **windowsazure.mediaservices.extensions/資產** |  |
> | 動作 | Microsoft.Media/mediaservices/assets/read | 讀取任何資產 |
> | 動作 | Microsoft.Media/mediaservices/assets/write | 建立或更新任何資產 |
> | 動作 | Microsoft.Media/mediaservices/assets/delete | 刪除任何資產 |
> | 動作 | Microsoft.Media/mediaservices/assets/listContainerSas/action | 列出資產容器 SAS URL |
> | 動作 | Microsoft.Media/mediaservices/assets/getEncryptionKey/action | 取得資產加密金鑰 |
> | 動作 | Microsoft.Media/mediaservices/assets/listStreamingLocators/action | 列出資產的串流定位器 |
> |  | **windowsazure.mediaservices.extensions/資產/assetfilter** |  |
> | 動作 | Microsoft.Media/mediaservices/assets/assetfilters/read | 讀取任何資產篩選條件 |
> | 動作 | Microsoft.Media/mediaservices/assets/assetfilters/write | 建立或更新任何資產篩選條件 |
> | 動作 | Microsoft.Media/mediaservices/assets/assetfilters/delete | 刪除任何資產篩選條件 |
> |  | **windowsazure.mediaservices.extensions/contentKeyPolicies** |  |
> | 動作 | Microsoft.Media/mediaservices/contentKeyPolicies/read | 讀取任何內容金鑰原則 |
> | 動作 | Microsoft.Media/mediaservices/contentKeyPolicies/write | 建立或更新任何內容金鑰原則 |
> | 動作 | Microsoft.Media/mediaservices/contentKeyPolicies/delete | 刪除任何內容金鑰原則 |
> | 動作 | Microsoft.Media/mediaservices/contentKeyPolicies/getPolicyPropertiesWithSecrets/action | 取得含有祕密的原則屬性 |
> |  | **windowsazure.mediaservices.extensions/eventGridFilters** |  |
> | 動作 | Microsoft.Media/mediaservices/eventGridFilters/read | 讀取任何事件方格篩選 |
> | 動作 | Microsoft.Media/mediaservices/eventGridFilters/write | 建立或更新任何事件方格篩選 |
> | 動作 | Microsoft.Media/mediaservices/eventGridFilters/delete | 刪除任何事件方格篩選 |
> |  | **windowsazure.mediaservices.extensions/liveEventOperations** |  |
> | 動作 | Microsoft.Media/mediaservices/liveEventOperations/read | 讀取任何即時事件作業 |
> |  | **windowsazure.mediaservices.extensions/Liveevent** |  |
> | 動作 | Microsoft.Media/mediaservices/liveEvents/read | 讀取任何即時事件 |
> | 動作 | Microsoft.Media/mediaservices/liveEvents/write | 建立或更新任何即時事件 |
> | 動作 | Microsoft.Media/mediaservices/liveEvents/delete | 刪除任何即時事件 |
> | 動作 | Microsoft.Media/mediaservices/liveEvents/start/action | 啟動任何即時事件作業 |
> | 動作 | Microsoft.Media/mediaservices/liveEvents/stop/action | 停止任何即時事件作業 |
> | 動作 | Microsoft.Media/mediaservices/liveEvents/reset/action | 重設任何即時事件作業 |
> |  | **windowsazure.mediaservices.extensions/Liveevent/Liveoutput** |  |
> | 動作 | Microsoft.Media/mediaservices/liveEvents/liveOutputs/read | 讀取任何即時輸出 |
> | 動作 | Microsoft.Media/mediaservices/liveEvents/liveOutputs/write | 建立或更新任何即時輸出 |
> | 動作 | Microsoft.Media/mediaservices/liveEvents/liveOutputs/delete | 刪除任何即時輸出 |
> |  | **windowsazure.mediaservices.extensions/liveOutputOperations** |  |
> | 動作 | Microsoft.Media/mediaservices/liveOutputOperations/read | 讀取任何即時輸出作業 |
> |  | **windowsazure.mediaservices.extensions/streamingEndpointOperations** |  |
> | 動作 | Microsoft.Media/mediaservices/streamingEndpointOperations/read | 讀取任何串流端點作業 |
> |  | **windowsazure.mediaservices.extensions/Streamingendpoint** |  |
> | 動作 | Microsoft.Media/mediaservices/streamingEndpoints/read | 讀取任何串流端點 |
> | 動作 | Microsoft.Media/mediaservices/streamingEndpoints/write | 建立或更新任何串流端點 |
> | 動作 | Microsoft.Media/mediaservices/streamingEndpoints/delete | 刪除任何串流端點 |
> | 動作 | Microsoft.Media/mediaservices/streamingEndpoints/start/action | 啟動任何串流端點作業 |
> | 動作 | Microsoft.Media/mediaservices/streamingEndpoints/stop/action | 停止任何串流端點作業 |
> | 動作 | Microsoft.Media/mediaservices/streamingEndpoints/scale/action | 調整任何串流端點作業 |
> |  | **windowsazure.mediaservices.extensions/Streaminglocator** |  |
> | 動作 | Microsoft.Media/mediaservices/streamingLocators/read | 讀取任何串流定位器 |
> | 動作 | Microsoft.Media/mediaservices/streamingLocators/write | 建立或更新任何串流定位器 |
> | 動作 | Microsoft.Media/mediaservices/streamingLocators/delete | 刪除任何串流定位器 |
> | 動作 | Microsoft.Media/mediaservices/streamingLocators/listContentKeys/action | 列出內容金鑰 |
> | 動作 | Microsoft.Media/mediaservices/streamingLocators/listPaths/action | 列出路徑 |
> |  | **windowsazure.mediaservices.extensions/streamingPolicies** |  |
> | 動作 | Microsoft.Media/mediaservices/streamingPolicies/read | 讀取任何串流原則 |
> | 動作 | Microsoft.Media/mediaservices/streamingPolicies/write | 建立或更新任何串流原則 |
> | 動作 | Microsoft.Media/mediaservices/streamingPolicies/delete | 刪除任何串流原則 |
> |  | **windowsazure.mediaservices.extensions/轉換** |  |
> | 動作 | Microsoft.Media/mediaservices/transforms/read | 讀取任何轉換 |
> | 動作 | Microsoft.Media/mediaservices/transforms/write | 建立或更新任何轉換 |
> | 動作 | Microsoft.Media/mediaservices/transforms/delete | 刪除任何轉換 |
> |  | **windowsazure.mediaservices.extensions/轉換/作業** |  |
> | 動作 | Microsoft.Media/mediaservices/transforms/jobs/read | 讀取任何作業 |
> | 動作 | Microsoft.Media/mediaservices/transforms/jobs/write | 建立或更新任何作業 |
> | 動作 | Microsoft.Media/mediaservices/transforms/jobs/delete | 刪除任何作業 |
> | 動作 | Microsoft.Media/mediaservices/transforms/jobs/cancelJob/action | 取消作業 |
> |  | **業務** |  |
> | 動作 | Microsoft.Media/operations/read | 取得可用的作業 |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

Azure 服務： [Azure Migrate](../migrate/migrate-services-overview.md)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.Migrate/register/action | 向 Microsoft.Migrate 資源提供者註冊訂用帳戶 |
> |  | **assessmentprojects** |  |
> | 動作 | Microsoft. 遷移/assessmentprojects/讀取 | 取得評量專案的屬性 |
> | 動作 | Microsoft. 遷移/assessmentprojects/寫入 | 建立新的評估專案，或更新現有的評估專案 |
> | 動作 | Microsoft. 遷移/assessmentprojects/刪除 | 刪除評估專案 |
> |  | **assessmentprojects/assessmentOptions** |  |
> | 動作 | Microsoft. 遷移/assessmentprojects/assessmentOptions/讀取 | 取得可在指定位置使用的評定選項 |
> |  | **assessmentprojects/評量** |  |
> | 動作 | Microsoft. 遷移/assessmentprojects/評量/讀取 | 列出專案內的評定 |
> |  | **assessmentprojects/群組** |  |
> | 動作 | Microsoft. 遷移/assessmentprojects/群組/讀取 | 取得群組的屬性 |
> | 動作 | Microsoft. 遷移/assessmentprojects/群組/寫入 | 建立新的群組或更新現有的群組 |
> | 動作 | Microsoft. 遷移/assessmentprojects/群組/刪除 | 刪除群組 |
> | 動作 | Microsoft. 遷移/assessmentprojects/群組/updateMachines/動作 | 藉由新增或移除電腦來更新群組 |
> |  | **assessmentprojects/群組/評量** |  |
> | 動作 | Microsoft. 遷移/assessmentprojects/群組/評量/讀取 | 取得評定的屬性 |
> | 動作 | Microsoft. 遷移/assessmentprojects/群組/評量/寫入 | 建立新的評定或更新現有的評定 |
> | 動作 | Microsoft. 遷移/assessmentprojects/群組/評量/刪除 | 刪除評定 |
> | 動作 | Microsoft. 遷移/assessmentprojects/群組/評量/downloadurl/動作 | 下載評定報告的 URL |
> |  | **assessmentprojects/群組/評量/assessedmachines** |  |
> | 動作 | Microsoft. 遷移/assessmentprojects/群組/評量/assessedmachines/讀取 | 取得已評定電腦的屬性 |
> |  | **assessmentprojects/hypervcollectors** |  |
> | 動作 | Microsoft. 遷移/assessmentprojects/hypervcollectors/讀取 | 取得 HyperV 收集器的屬性 |
> | 動作 | Microsoft. 遷移/assessmentprojects/hypervcollectors/寫入 | 建立新的 HyperV 收集器，或更新現有的 HyperV 收集器 |
> | 動作 | Microsoft. 遷移/assessmentprojects/hypervcollectors/刪除 | 刪除 HyperV 收集器 |
> |  | **assessmentprojects/importcollectors** |  |
> | 動作 | Microsoft. 遷移/assessmentprojects/importcollectors/讀取 | 取得匯入收集器的屬性 |
> | 動作 | Microsoft. 遷移/assessmentprojects/importcollectors/寫入 | 建立新的匯入收集器，或更新現有的匯入收集器 |
> | 動作 | Microsoft. 遷移/assessmentprojects/importcollectors/刪除 | 刪除匯入收集器 |
> |  | **assessmentprojects/機器** |  |
> | 動作 | Microsoft. 遷移/assessmentprojects/機器/讀取 | 取得電腦的屬性 |
> |  | **assessmentprojects/servercollectors** |  |
> | 動作 | Microsoft. 遷移/assessmentprojects/servercollectors/讀取 | 取得伺服器收集器的屬性 |
> | 動作 | Microsoft. 遷移/assessmentprojects/servercollectors/寫入 | 建立新的伺服器收集器，或更新現有的伺服器收集器 |
> |  | **assessmentprojects/vmwarecollectors** |  |
> | 動作 | Microsoft. 遷移/assessmentprojects/vmwarecollectors/讀取 | 取得 VMware 收集器的屬性 |
> | 動作 | Microsoft. 遷移/assessmentprojects/vmwarecollectors/寫入 | 建立新的 VMware 收集器，或更新現有的 VMware 收集器 |
> | 動作 | Microsoft. 遷移/assessmentprojects/vmwarecollectors/刪除 | 刪除 VMware 收集器 |
> |  | **堆放** |  |
> | 動作 | Microsoft.Migrate/locations/checknameavailability/action | 檢查指定位置中，指定訂用帳戶的資源名稱可用性 |
> |  | **位置/assessmentOptions** |  |
> | 動作 | Microsoft.Migrate/locations/assessmentOptions/read | 取得可在指定位置使用的評定選項 |
> |  | **migrateprojects** |  |
> | 動作 | Microsoft.Migrate/migrateprojects/read | 取得遷移專案的屬性 |
> | 動作 | Microsoft. 遷移/migrateprojects/寫入 | 建立新的遷移專案，或更新現有的遷移專案 |
> | 動作 | Microsoft. 遷移/migrateprojects/刪除 | 刪除遷移專案 |
> | 動作 | Microsoft. 遷移/migrateprojects/registerTool/動作 | 將工具註冊到遷移專案 |
> | 動作 | Microsoft. 遷移/migrateprojects/RefreshSummary/動作 | 重新整理遷移專案摘要 |
> |  | **migrateprojects/DatabaseInstances** |  |
> | 動作 | Microsoft. 遷移/migrateprojects/DatabaseInstances/讀取 | 取得資料庫實例的屬性。 |
> |  | **migrateprojects/資料庫** |  |
> | 動作 | Microsoft. 遷移/migrateprojects/資料庫/讀取 | 取得資料庫的屬性 |
> |  | **migrateprojects/機器** |  |
> | 動作 | Microsoft. 遷移/migrateprojects/機器/讀取 | 取得電腦的屬性 |
> |  | **migrateprojects/MigrateEvents** |  |
> | 動作 | Microsoft. 遷移/migrateprojects/MigrateEvents/讀取 | 取得遷移事件的屬性。 |
> | 動作 | Microsoft. 遷移/migrateprojects/MigrateEvents/刪除 | 刪除遷移事件 |
> |  | **migrateprojects/解決方案** |  |
> | 動作 | Microsoft.Migrate/migrateprojects/solutions/read | 取得遷移專案方案的屬性 |
> | 動作 | Microsoft. 遷移/migrateprojects/解決方案/寫入 | 建立新的遷移專案解決方案，或更新現有的遷移專案解決方案 |
> | 動作 | Microsoft. 遷移/migrateprojects/解決方案/刪除 | 刪除遷移專案解決方案 |
> | 動作 | Microsoft.Migrate/migrateprojects/solutions/getconfig/action | 取得遷移專案解決方案組態 |
> | 動作 | Microsoft. 遷移/migrateprojects/解決方案/cleanupData/動作 | 清除遷移專案解決方案資料 |
> |  | **作業** |  |
> | 動作 | Microsoft.Migrate/Operations/read | 列出可對 Microsoft.Migrate 資源提供者進行的作業 |
> |  | **投影** |  |
> | 動作 | Microsoft.Migrate/projects/read | 取得專案的屬性 |
> | 動作 | Microsoft.Migrate/projects/write | 建立新的專案或更新現有的專案 |
> | 動作 | Microsoft.Migrate/projects/delete | 刪除專案 |
> | 動作 | Microsoft.Migrate/projects/keys/action | 取得專案的共用金鑰 |
> |  | **專案/評量** |  |
> | 動作 | Microsoft.Migrate/projects/assessments/read | 列出專案內的評定 |
> |  | **專案/群組** |  |
> | 動作 | Microsoft.Migrate/projects/groups/read | 取得群組的屬性 |
> | 動作 | Microsoft.Migrate/projects/groups/write | 建立新的群組或更新現有的群組 |
> | 動作 | Microsoft.Migrate/projects/groups/delete | 刪除群組 |
> |  | **專案/群組/評量** |  |
> | 動作 | Microsoft.Migrate/projects/groups/assessments/read | 取得評定的屬性 |
> | 動作 | Microsoft.Migrate/projects/groups/assessments/write | 建立新的評定或更新現有的評定 |
> | 動作 | Microsoft.Migrate/projects/groups/assessments/delete | 刪除評定 |
> | 動作 | Microsoft.Migrate/projects/groups/assessments/downloadurl/action | 下載評定報告的 URL |
> |  | **專案/群組/評量/assessedmachines** |  |
> | 動作 | Microsoft.Migrate/projects/groups/assessments/assessedmachines/read | 取得已評定電腦的屬性 |
> |  | **專案/機器** |  |
> | 動作 | Microsoft.Migrate/projects/machines/read | 取得電腦的屬性 |

## <a name="microsoftmixedreality"></a>MixedReality

Azure 服務： [Azure 空間錨點](../spatial-anchors/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | MixedReality/register/action | 為混合現實資源提供者註冊訂用帳戶。 |
> |  | **remoteRenderingAccounts/providers/Microsoft Insights/Metricdefinitions.listasync** |  |
> | 動作 | MixedReality/remoteRenderingAccounts/provider/Microsoft Insights/Metricdefinitions.listasync/read | 取得 MixedReality/remoteRenderingAccounts 的可用計量 |
> |  | **spatialAnchorsAccounts/providers/Microsoft Insights/diagnosticSettings** |  |
> | 動作 | MixedReality/spatialAnchorsAccounts/provider/Microsoft Insights/diagnosticSettings/read | 取得 MixedReality/spatialAnchorsAccounts 的診斷設定 |
> | 動作 | MixedReality/spatialAnchorsAccounts/provider/Microsoft Insights/diagnosticSettings/write | 建立或更新 MixedReality/spatialAnchorsAccounts 的診斷設定 |
> |  | **spatialAnchorsAccounts/providers/Microsoft Insights/Metricdefinitions.listasync** |  |
> | 動作 | MixedReality/spatialAnchorsAccounts/provider/Microsoft Insights/Metricdefinitions.listasync/read | 取得 MixedReality/spatialAnchorsAccounts 的可用計量 |
> |  | **RemoteRenderingAccounts** |  |
> | DataAction | MixedReality/RemoteRenderingAccounts/convert/action | 開始資產轉換 |
> | DataAction | MixedReality/RemoteRenderingAccounts/managesessions/action | 啟動會話 |
> |  | **RemoteRenderingAccounts/轉換** |  |
> | DataAction | MixedReality/RemoteRenderingAccounts/convert/read | 取得資產轉換屬性 |
> | DataAction | MixedReality/RemoteRenderingAccounts/convert/delete | 停止資產轉換 |
> |  | **RemoteRenderingAccounts/診斷** |  |
> | DataAction | MixedReality/RemoteRenderingAccounts/診斷/讀取 | 連接到遠端轉譯檢查器 |
> |  | **RemoteRenderingAccounts/managesessions** |  |
> | DataAction | MixedReality/RemoteRenderingAccounts/managesessions/read | 取得會話屬性 |
> | DataAction | MixedReality/RemoteRenderingAccounts/managesessions/delete | 停止會話 |
> |  | **RemoteRenderingAccounts/render** |  |
> | DataAction | MixedReality/RemoteRenderingAccounts/render/read | 連接到會話 |
> |  | **SpatialAnchorsAccounts** |  |
> | DataAction | MixedReality/SpatialAnchorsAccounts/create/action | 建立空間錨點 |
> | DataAction | MixedReality/SpatialAnchorsAccounts/delete | 刪除空間錨點 |
> | DataAction | MixedReality/SpatialAnchorsAccounts/write | 更新空間錨點屬性 |
> |  | **SpatialAnchorsAccounts/探索** |  |
> | DataAction | MixedReality/SpatialAnchorsAccounts/探索/讀取 | 探索附近的空間錨點 |
> |  | **SpatialAnchorsAccounts/屬性** |  |
> | DataAction | MixedReality/SpatialAnchorsAccounts/properties/read | 取得空間錨點的屬性 |
> |  | **SpatialAnchorsAccounts/查詢** |  |
> | DataAction | MixedReality/SpatialAnchorsAccounts/查詢/讀取 | 找出空間錨點 |
> |  | **SpatialAnchorsAccounts/submitdiag** |  |
> | DataAction | MixedReality/SpatialAnchorsAccounts/submitdiag/read | 提交診斷資料，以協助改善 Azure 空間錨點服務的品質 |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

Azure 服務： [Azure NetApp Files](../azure-netapp-files/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft。 NetApp/register/action | 訂用帳戶註冊動作 |
> | 動作 | Microsoft。 NetApp/取消註冊/動作 | 向 Microsoft. NetApp 資源提供者取消註冊訂用帳戶 |
> |  | **堆放** |  |
> | 動作 | Microsoft.NetApp/locations/read | 讀取可用性檢查資源。 |
> | 動作 | Microsoft NetApp/位置/checknameavailability/動作 | 檢查資源名稱是否可用 |
> | 動作 | Microsoft NetApp/位置/checkfilepathavailability/動作 | 檢查檔案路徑是否可用 |
> |  | **位置/operationresults** |  |
> | 動作 | Microsoft.NetApp/locations/operationresults/read | 讀取作業結果資源。 |
> |  | **netAppAccounts** |  |
> | 動作 | Microsoft.NetApp/netAppAccounts/read | 讀取帳戶資源。 |
> | 動作 | Microsoft.NetApp/netAppAccounts/write | 寫入帳戶資源。 |
> | 動作 | Microsoft.NetApp/netAppAccounts/delete | 刪除帳戶資源。 |
> |  | **netAppAccounts/accountBackups** |  |
> | 動作 | Microsoft NetApp/netAppAccounts/accountBackups/read | 讀取帳戶備份資源。 |
> | 動作 | Microsoft NetApp/netAppAccounts/accountBackups/write | 寫入帳戶備份資源。 |
> | 動作 | Microsoft NetApp/netAppAccounts/accountBackups/delete | 刪除帳戶備份資源。 |
> |  | **netAppAccounts/backupPolicies** |  |
> | 動作 | Microsoft NetApp/netAppAccounts/backupPolicies/read | 讀取備份原則資源。 |
> | 動作 | Microsoft NetApp/netAppAccounts/backupPolicies/write | 寫入備份原則資源。 |
> | 動作 | Microsoft NetApp/netAppAccounts/backupPolicies/delete | 刪除備份原則資源。 |
> |  | **netAppAccounts/capacityPools** |  |
> | 動作 | Microsoft.NetApp/netAppAccounts/capacityPools/read | 讀取集區資源。 |
> | 動作 | Microsoft.NetApp/netAppAccounts/capacityPools/write | 寫入集區資源。 |
> | 動作 | Microsoft.NetApp/netAppAccounts/capacityPools/delete | 刪除集區資源。 |
> |  | **netAppAccounts/capacityPools/磁片區** |  |
> | 動作 | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/read | 讀取磁碟區資源。 |
> | 動作 | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/write | 寫入磁碟區資源。 |
> | 動作 | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/delete | 刪除磁碟區資源。 |
> | 動作 | Microsoft NetApp/netAppAccounts/capacityPools/磁片區/還原/動作 | 將磁片區還原到特定快照集 |
> | 動作 | Microsoft NetApp/netAppAccounts/capacityPools/磁片區/中斷/動作 | 中斷磁片區複寫關係 |
> | 動作 | Microsoft NetApp/netAppAccounts/capacityPools/磁片區/ReplicationStatus/action | 讀取磁片區複寫的狀態。 |
> | 動作 | Microsoft NetApp/netAppAccounts/capacityPools/磁片區/AuthorizeReplication/action | 授權來源磁片區複寫 |
> | 動作 | Microsoft NetApp/netAppAccounts/capacityPools/磁片區/ResyncReplication/action | 重新同步處理目的地磁片區上的複寫 |
> | 動作 | Microsoft NetApp/netAppAccounts/capacityPools/磁片區/DeleteReplication/action | 刪除目的地磁片區上的複寫 |
> |  | **netAppAccounts/capacityPools/磁片區/備份** |  |
> | 動作 | Microsoft NetApp/netAppAccounts/capacityPools/磁片區/備份/讀取 | 讀取備份資源。 |
> | 動作 | Microsoft NetApp/netAppAccounts/capacityPools/磁片區/備份/寫入 | 寫入備份資源。 |
> | 動作 | Microsoft NetApp/netAppAccounts/capacityPools/磁片區/備份/刪除 | 刪除備份資源。 |
> |  | **netAppAccounts/capacityPools/磁片區/快照集** |  |
> | 動作 | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/read | 讀取快照集資源。 |
> | 動作 | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/write | 寫入快照集資源。 |
> | 動作 | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/delete | 刪除快照集資源。 |
> |  | **netAppAccounts/snapshotPolicies** |  |
> | 動作 | Microsoft NetApp/netAppAccounts/snapshotPolicies/read | 讀取快照集原則資源。 |
> | 動作 | Microsoft NetApp/netAppAccounts/snapshotPolicies/write | 寫入快照集原則資源。 |
> | 動作 | Microsoft NetApp/netAppAccounts/snapshotPolicies/delete | 刪除快照集原則資源。 |
> | 動作 | Microsoft NetApp/netAppAccounts/snapshotPolicies/ListVolumes/action | 列出已連線至快照集原則的磁片區 |
> |  | **netAppAccounts/保存庫** |  |
> | 動作 | Microsoft NetApp/netAppAccounts/vault/讀取 | 讀取保存庫資源。 |
> |  | **作業** |  |
> | 動作 | Microsoft.NetApp/Operations/read | 讀取作業資源。 |

## <a name="microsoftnetwork"></a>Microsoft.Network

Azure 服務：[虛擬網路](../virtual-network/index.yml)、 [Load Balancer](../load-balancer/index.yml)、[應用程式閘道](../application-gateway/index.yml)、 [Azure DNS](../dns/index.yml)、 [ExpressRoute](../expressroute/index.yml)、 [VPN 閘道](../vpn-gateway/index.yml)、[流量管理員](../traffic-manager/index.yml)、[網路監看員](../network-watcher/index.yml)、 [azure 防火牆](../firewall/index.yml) [azure Front 門板服務](../frontdoor/index.yml)、 [azure](../bastion/index.yml)防禦

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.Network/register/action | 註冊訂用帳戶 |
> | 動作 | Microsoft.Network/unregister/action | 取消註冊訂用帳戶 |
> | 動作 | Microsoft.Network/checkTrafficManagerNameAvailability/action | 檢查流量管理員相對 DNS 名稱的可用性。 |
> | 動作 | Microsoft.Network/checkFrontDoorNameAvailability/action | 檢查 Front Door 名稱是否可用 |
> |  | **applicationGatewayAvailableRequestHeaders** |  |
> | 動作 | Microsoft.Network/applicationGatewayAvailableRequestHeaders/read | 取得應用程式閘道可用的要求標頭 |
> |  | **applicationGatewayAvailableResponseHeaders** |  |
> | 動作 | Microsoft.Network/applicationGatewayAvailableResponseHeaders/read | 取得應用程式閘道可用的回應標頭 |
> |  | **applicationGatewayAvailableServerVariables** |  |
> | 動作 | Microsoft.Network/applicationGatewayAvailableServerVariables/read | 取得應用程式閘道可用的伺服器變數 |
> |  | **applicationGatewayAvailableSslOptions** |  |
> | 動作 | Microsoft.Network/applicationGatewayAvailableSslOptions/read | 應用程式閘道可用的 SSL 選項 |
> |  | **applicationGatewayAvailableSslOptions/predefinedPolicies** |  |
> | 動作 | Microsoft.Network/applicationGatewayAvailableSslOptions/predefinedPolicies/read | 應用程式閘道 SSL 預先定義的原則 |
> |  | **applicationGatewayAvailableWafRuleSets** |  |
> | 動作 | Microsoft.Network/applicationGatewayAvailableWafRuleSets/read | 取得應用程式閘道可用的 WAF 規則集 |
> |  | **applicationGateways** |  |
> | 動作 | Microsoft.Network/applicationGateways/read | 取得應用程式閘道 |
> | 動作 | Microsoft.Network/applicationGateways/write | 建立或更新應用程式閘道 |
> | 動作 | Microsoft.Network/applicationGateways/delete | 刪除應用程式閘道 |
> | 動作 | Microsoft.Network/applicationGateways/backendhealth/action | 取得應用程式閘道後端的健康狀態 |
> | 動作 | Microsoft. Network/applicationGateways/getBackendHealthOnDemand/action | 取得給定 HTTP 設定和後端集區的應用程式閘道後端健全狀況需求 |
> | 動作 | Microsoft.Network/applicationGateways/start/action | 啟動應用程式閘道 |
> | 動作 | Microsoft.Network/applicationGateways/stop/action | 停止應用程式閘道 |
> |  | **applicationGateways/Loadbalancer.backendaddresspools** |  |
> | 動作 | Microsoft.Network/applicationGateways/backendAddressPools/join/action | 加入應用程式閘道後端位址集區。 未打斷。 |
> |  | **ApplicationGatewayWebApplicationFirewallPolicies** |  |
> | 動作 | Microsoft 網路/ApplicationGatewayWebApplicationFirewallPolicies/讀取 | 取得應用程式閘道 WAF 原則 |
> | 動作 | Microsoft 網路/ApplicationGatewayWebApplicationFirewallPolicies/寫入 | 建立應用程式閘道 WAF 原則，或更新應用程式閘道 WAF 原則 |
> | 動作 | Microsoft 網路/ApplicationGatewayWebApplicationFirewallPolicies/刪除 | 刪除應用程式閘道 WAF 原則 |
> |  | **applicationRuleCollections** |  |
> | 動作 | Microsoft 網路/applicationRuleCollections/讀取 | 取得 Azure 防火牆 ApplicationRuleCollection |
> | 動作 | Microsoft 網路/applicationRuleCollections/寫入 | CreatesOrUpdates Azure 防火牆 ApplicationRuleCollection |
> | 動作 | Microsoft 網路/applicationRuleCollections/刪除 | 刪除 Azure 防火牆 ApplicationRuleCollection |
> |  | **applicationSecurityGroups** |  |
> | 動作 | Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action | 將 IP 設定加入至應用程式安全性群組。 未打斷。 |
> | 動作 | Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | 將安全性規則加入至應用程式安全性群組。 未打斷。 |
> | 動作 | Microsoft.Network/applicationSecurityGroups/read | 取得應用程式安全性群組識別碼。 |
> | 動作 | Microsoft.Network/applicationSecurityGroups/write | 建立應用程式安全性群組，或更新現有的應用程式安全性群組。 |
> | 動作 | Microsoft.Network/applicationSecurityGroups/delete | 刪除應用程式安全性群組 |
> | 動作 | Microsoft.Network/applicationSecurityGroups/listIpConfigurations/action | 在 ApplicationSecurityGroup 中列出 IP 組態 |
> |  | **azureFirewallFqdnTags** |  |
> | 動作 | Microsoft.Network/azureFirewallFqdnTags/read | 取得 Azure 防火牆 FQDN 標記 |
> |  | **azurefirewalls** |  |
> | 動作 | Microsoft.Network/azurefirewalls/read | 取得 Azure 防火牆 |
> | 動作 | Microsoft.Network/azurefirewalls/write | 建立或更新 Azure 防火牆 |
> | 動作 | Microsoft.Network/azurefirewalls/delete | 刪除 Azure 防火牆 |
> |  | **bastionHosts** |  |
> | 動作 | Microsoft.Network/bastionHosts/read | 取得堡壘主機 |
> | 動作 | Microsoft.Network/bastionHosts/write | 建立或更新堡壘主機 |
> | 動作 | Microsoft.Network/bastionHosts/delete | 刪除堡壘主機 |
> | 動作 | Microsoft. Network/bastionHosts/getactivesessions/action | 取得防禦主機中的作用中會話 |
> | 動作 | Microsoft. Network/bastionHosts/disconnectactivesessions/action | 將防禦主機中指定的作用中會話中斷連線 |
> | 動作 | Microsoft. Network/bastionHosts/getShareableLinks/action | 為防禦子網中指定的 Vm 傳回可共用的 url，前提是其 url 已建立 |
> | 動作 | Microsoft. Network/bastionHosts/createShareableLinks/action | 為防禦下的 Vm 建立可共用的 url，並傳回 url |
> | 動作 | Microsoft. Network/bastionHosts/deleteShareableLinks/action | 在防禦下刪除所提供 Vm 的可共用 url |
> |  | **bgpServiceCommunities** |  |
> | 動作 | Microsoft.Network/bgpServiceCommunities/read | 取得 BGP 服務社群 |
> |  | **連線** |  |
> | 動作 | Microsoft.Network/connections/read | 取得 VirtualNetworkGatewayConnection |
> | 動作 | Microsoft.Network/connections/write | 建立或更新現有的 VirtualNetworkGatewayConnection |
> | 動作 | Microsoft.Network/connections/delete | 刪除 VirtualNetworkGatewayConnection |
> | 動作 | Microsoft.Network/connections/sharedkey/action | 取得 VirtualNetworkGatewayConnection SharedKey |
> | 動作 | Microsoft.Network/connections/vpndeviceconfigurationscript/action | 取得 VirtualNetworkGatewayConnection 的 VPN 裝置設定 |
> | 動作 | Microsoft.Network/connections/revoke/action | 將 Express Route 連線狀態標示為已撤銷 |
> | 動作 | Microsoft 網路/連線/startpacketcapture/動作 | 啟動虛擬網路的閘道連線封包捕獲。 |
> | 動作 | Microsoft 網路/連線/stoppacketcapture/動作 | 停止虛擬網路的閘道連線封包捕獲。 |
> |  | **connections/sharedKey** |  |
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
> | 動作 | Microsoft.Network/ddosProtectionPlans/join/action | 加入 DDoS 保護計劃。 未打斷。 |
> |  | **dnsoperationresults** |  |
> | 動作 | Microsoft.Network/dnsoperationresults/read | 取得 DNS 作業的結果 |
> |  | **dnsoperationstatuses** |  |
> | 動作 | Microsoft.Network/dnsoperationstatuses/read | 取得 DNS 作業的狀態  |
> |  | **dnszones** |  |
> | 動作 | Microsoft.Network/dnszones/read | 取得 JSON 格式的 DNS 區域。 區域屬性包含標記、etag、numberOfRecordSets 和 maxNumberOfRecordSets。 請注意，此命令不會擷取區域內所包含的記錄集。 |
> | 動作 | Microsoft.Network/dnszones/write | 在資源群組內建立或更新 DNS 區域。  用來更新 DNS 區域資源上的標記。 請注意，此命令無法用來在區域內建立或更新記錄集。 |
> | 動作 | Microsoft.Network/dnszones/delete | 刪除 JSON 格式的 DNS 區域。 區域屬性包含標記、etag、numberOfRecordSets 和 maxNumberOfRecordSets。 |
> |  | **dnszones/A** |  |
> | 動作 | Microsoft.Network/dnszones/A/read | 取得 JSON 格式的 ' A ' 類型記錄集。 記錄集包含記錄清單以及 TTL、標記和 etag。 |
> | 動作 | Microsoft.Network/dnszones/A/write | 在 DNS 區域內建立或更新 ' A ' 類型的記錄集。 指定的記錄將會取代記錄集內的目前記錄。 |
> | 動作 | Microsoft.Network/dnszones/A/delete | 從 DNS 區域中移除指定名稱和類型 ' A ' 的記錄集。 |
> |  | **dnszones/AAAA** |  |
> | 動作 | Microsoft.Network/dnszones/AAAA/read | 取得 JSON 格式的 ' AAAA ' 類型記錄集。 記錄集包含記錄清單以及 TTL、標記和 etag。 |
> | 動作 | Microsoft.Network/dnszones/AAAA/write | 在 DNS 區域內建立或更新 ' AAAA ' 類型的記錄集。 指定的記錄將會取代記錄集內的目前記錄。 |
> | 動作 | Microsoft.Network/dnszones/AAAA/delete | 從 DNS 區域中移除指定名稱和類型 ' AAAA ' 的記錄集。 |
> |  | **dnszones/全部** |  |
> | 動作 | Microsoft.Network/dnszones/all/read | 取得跨類型的 DNS 記錄集 |
> |  | **dnszones/CAA** |  |
> | 動作 | Microsoft.Network/dnszones/CAA/read | 取得 JSON 格式的 ' CAA ' 類型記錄集。 記錄集包含 TTL、標記和 etag。 |
> | 動作 | Microsoft.Network/dnszones/CAA/write | 在 DNS 區域內建立或更新 ' CAA ' 類型的記錄集。 指定的記錄將會取代記錄集內的目前記錄。 |
> | 動作 | Microsoft.Network/dnszones/CAA/delete | 從 DNS 區域中移除指定名稱的記錄集，並輸入 ' CAA '。 |
> |  | **dnszones/CNAME** |  |
> | 動作 | Microsoft.Network/dnszones/CNAME/read | 取得 JSON 格式的 ' CNAME ' 類型記錄集。 記錄集包含 TTL、標記和 etag。 |
> | 動作 | Microsoft.Network/dnszones/CNAME/write | 在 DNS 區域內建立或更新 ' CNAME ' 類型的記錄集。 指定的記錄將會取代記錄集內的目前記錄。 |
> | 動作 | Microsoft.Network/dnszones/CNAME/delete | 從 DNS 區域中移除指定名稱和類型為 ' CNAME ' 的記錄集。 |
> |  | **dnszones/MX** |  |
> | 動作 | Microsoft.Network/dnszones/MX/read | 取得 JSON 格式的 ' MX ' 類型記錄集。 記錄集包含記錄清單以及 TTL、標記和 etag。 |
> | 動作 | Microsoft.Network/dnszones/MX/write | 在 DNS 區域內建立或更新 ' MX ' 類型的記錄集。 指定的記錄將會取代記錄集內的目前記錄。 |
> | 動作 | Microsoft.Network/dnszones/MX/delete | 從 DNS 區域中移除指定名稱和類型 ' MX ' 的記錄集。 |
> |  | **dnszones/NS** |  |
> | 動作 | Microsoft.Network/dnszones/NS/read | 取得 NS 類型的 DNS 記錄集 |
> | 動作 | Microsoft.Network/dnszones/NS/write | 建立或更新 NS 類型的 DNS 記錄集 |
> | 動作 | Microsoft.Network/dnszones/NS/delete | 刪除 NS 類型的 DNS 記錄集 |
> |  | **dnszones/PTR** |  |
> | 動作 | Microsoft.Network/dnszones/PTR/read | 取得 JSON 格式的 ' PTR ' 類型記錄集。 記錄集包含記錄清單以及 TTL、標記和 etag。 |
> | 動作 | Microsoft.Network/dnszones/PTR/write | 在 DNS 區域內建立或更新 ' PTR ' 類型的記錄集。 指定的記錄將會取代記錄集內的目前記錄。 |
> | 動作 | Microsoft.Network/dnszones/PTR/delete | 從 DNS 區域中移除指定名稱和類型 ' PTR ' 的記錄集。 |
> |  | **dnszones/記錄集** |  |
> | 動作 | Microsoft.Network/dnszones/recordsets/read | 取得跨類型的 DNS 記錄集 |
> |  | **dnszones/SOA** |  |
> | 動作 | Microsoft.Network/dnszones/SOA/read | 取得 SOA 類型的 DNS 記錄集 |
> | 動作 | Microsoft.Network/dnszones/SOA/write | 建立或更新 SOA 類型的 DNS 記錄集 |
> |  | **dnszones/SRV** |  |
> | 動作 | Microsoft.Network/dnszones/SRV/read | 取得 JSON 格式的 ' SRV ' 類型記錄集。 記錄集包含記錄清單以及 TTL、標記和 etag。 |
> | 動作 | Microsoft.Network/dnszones/SRV/write | 建立或更新 SRV 類型的記錄集 |
> | 動作 | Microsoft.Network/dnszones/SRV/delete | 從 DNS 區域中移除指定名稱和類型為 ' SRV ' 的記錄集。 |
> |  | **dnszones/TXT** |  |
> | 動作 | Microsoft.Network/dnszones/TXT/read | 取得 JSON 格式的 ' TXT ' 類型記錄集。 記錄集包含記錄清單以及 TTL、標記和 etag。 |
> | 動作 | Microsoft.Network/dnszones/TXT/write | 在 DNS 區域內建立或更新 ' TXT ' 類型的記錄集。 指定的記錄將會取代記錄集內的目前記錄。 |
> | 動作 | Microsoft.Network/dnszones/TXT/delete | 從 DNS 區域中移除指定名稱和類型 ' TXT ' 的記錄集。 |
> |  | **expressRouteCircuits** |  |
> | 動作 | Microsoft.Network/expressRouteCircuits/read | 取得 ExpressRouteCircuit |
> | 動作 | Microsoft.Network/expressRouteCircuits/write | 建立或更新現有的 ExpressRouteCircuit |
> | 動作 | Microsoft.Network/expressRouteCircuits/join/action | 加入 Express Route 線路。 未打斷。 |
> | 動作 | Microsoft.Network/expressRouteCircuits/delete | 刪除 ExpressRouteCircuit |
> |  | **expressRouteCircuits/授權** |  |
> | 動作 | Microsoft.Network/expressRouteCircuits/authorizations/read | 取得 ExpressRouteCircuit 授權 |
> | 動作 | Microsoft.Network/expressRouteCircuits/authorizations/write | 建立或更新現有的 ExpressRouteCircuit 授權 |
> | 動作 | Microsoft.Network/expressRouteCircuits/authorizations/delete | 刪除 ExpressRouteCircuit 授權 |
> |  | **expressRouteCircuits/對等互連** |  |
> | 動作 | Microsoft.Network/expressRouteCircuits/peerings/read | 取得 ExpressRouteCircuit 對等互連 |
> | 動作 | Microsoft.Network/expressRouteCircuits/peerings/write | 建立或更新現有的 ExpressRouteCircuit 對等互連 |
> | 動作 | Microsoft.Network/expressRouteCircuits/peerings/delete | 刪除 ExpressRouteCircuit 對等互連 |
> |  | **expressRouteCircuits/對等互連/arpTables** |  |
> | 動作 | Microsoft. Network/expressRouteCircuits/對等互連/arpTables/read | 取得 ExpressRouteCircuit 對等互連 ArpTable |
> |  | **expressRouteCircuits/對等互連/connections** |  |
> | 動作 | Microsoft.Network/expressRouteCircuits/peerings/connections/read | 取得 ExpressRouteCircuit 連線 |
> | 動作 | Microsoft.Network/expressRouteCircuits/peerings/connections/write | 建立或更新現有的 ExpressRouteCircuit 連線資源 |
> | 動作 | Microsoft.Network/expressRouteCircuits/peerings/connections/delete | 刪除 ExpressRouteCircuit 連線 |
> |  | **expressRouteCircuits/對等互連/peerConnections** |  |
> | 動作 | Microsoft.Network/expressRouteCircuits/peerings/peerConnections/read | 取得對等 Express Route 線路連線 |
> |  | **expressRouteCircuits/對等互連/routeTables** |  |
> | 動作 | Microsoft. Network/expressRouteCircuits/對等互連/routeTables/read | 取得 ExpressRouteCircuit 對等互連 RouteTable |
> |  | **expressRouteCircuits/對等互連/routeTablesSummary** |  |
> | 動作 | Microsoft. Network/expressRouteCircuits/對等互連/routeTablesSummary/read | 取得 ExpressRouteCircuit 對等互連 RouteTable 摘要 |
> |  | **expressRouteCircuits/對等互連/stats** |  |
> | 動作 | Microsoft.Network/expressRouteCircuits/peerings/stats/read | 取得 ExpressRouteCircuit 對等互連統計資料 |
> |  | **expressRouteCircuits/stats** |  |
> | 動作 | Microsoft.Network/expressRouteCircuits/stats/read | 取得 ExpressRouteCircuit 統計資料 |
> |  | **expressRouteCrossConnections** |  |
> | 動作 | Microsoft.Network/expressRouteCrossConnections/read | 取得快速路由交叉連線 |
> | 動作 | Microsoft.Network/expressRouteCrossConnections/join/action | 加入 Express Route 交叉連線。 未打斷。 |
> |  | **expressRouteCrossConnections/對等互連** |  |
> | 動作 | Microsoft.Network/expressRouteCrossConnections/peerings/read | 取得快速路由交叉連線對等互連 |
> | 動作 | Microsoft.Network/expressRouteCrossConnections/peerings/write | 建立快速路由交叉連線對等互連，或更新現有的快速路由交叉連線對等互連 |
> | 動作 | Microsoft.Network/expressRouteCrossConnections/peerings/delete | 刪除快速路由交叉連線對等互連 |
> |  | **expressRouteCrossConnections/對等互連/arpTables** |  |
> | 動作 | Microsoft. Network/expressRouteCrossConnections/對等互連/arpTables/read | 取得快速路由交叉連線對等互連 ARP 資料表 |
> |  | **expressRouteCrossConnections/對等互連/routeTables** |  |
> | 動作 | Microsoft. Network/expressRouteCrossConnections/對等互連/routeTables/read | 取得快速路由交叉連線對等互連路由表 |
> |  | **expressRouteCrossConnections/對等互連/routeTableSummary** |  |
> | 動作 | Microsoft. Network/expressRouteCrossConnections/對等互連/routeTableSummary/read | 取得快速路由交叉連線對等互連路由表摘要 |
> |  | **expressRouteGateways** |  |
> | 動作 | Microsoft.Network/expressRouteGateways/read | 取得 ExpressRoute 閘道 |
> | 動作 | Microsoft.Network/expressRouteGateways/join/action | 加入 Express Route 閘道。 未打斷。 |
> |  | **expressRouteGateways/expressRouteConnections** |  |
> | 動作 | Microsoft.Network/expressRouteGateways/expressRouteConnections/read | 取得 ExpressRoute 連線 |
> | 動作 | Microsoft.Network/expressRouteGateways/expressRouteConnections/write | 建立 ExpressRoute 連線，或更新現有的 ExpressRoute 連線 |
> | 動作 | Microsoft.Network/expressRouteGateways/expressRouteConnections/delete | 刪除 ExpressRoute 連線 |
> |  | **expressRoutePorts** |  |
> | 動作 | Microsoft.Network/expressRoutePorts/read | 取得 ExpressRoutePorts |
> | 動作 | Microsoft.Network/expressRoutePorts/write | 建立或更新 ExpressRoutePorts |
> | 動作 | Microsoft.Network/expressRoutePorts/join/action | 加入 Express Route 埠。 未打斷。 |
> | 動作 | Microsoft.Network/expressRoutePorts/delete | 刪除 ExpressRoutePorts |
> |  | **expressRoutePorts/links** |  |
> | 動作 | Microsoft.Network/expressRoutePorts/links/read | 取得 ExpressRouteLink |
> |  | **expressRoutePortsLocations** |  |
> | 動作 | Microsoft.Network/expressRoutePortsLocations/read | 取得 Express Route 連接埠位置 |
> |  | **expressRouteServiceProviders** |  |
> | 動作 | Microsoft.Network/expressRouteServiceProviders/read | 取得 ExpressRoute 服務提供者 |
> |  | **firewallPolicies** |  |
> | 動作 | Microsoft 網路/firewallPolicies/讀取 | 取得防火牆原則 |
> | 動作 | Microsoft 網路/firewallPolicies/寫入 | 建立防火牆原則，或更新現有的防火牆原則 |
> | 動作 | Microsoft 網路/firewallPolicies/聯結/動作 | 加入防火牆原則。 未打斷。 |
> | 動作 | Microsoft 網路/firewallPolicies/刪除 | 刪除防火牆原則 |
> |  | **firewallPolicies/ruleGroups** |  |
> | 動作 | Microsoft. Network/firewallPolicies/ruleGroups/read | 取得防火牆原則規則群組 |
> | 動作 | FirewallPolicies/ruleGroups/write | 建立防火牆原則規則群組，或更新現有的防火牆原則規則群組 |
> | 動作 | Microsoft. Network/firewallPolicies/ruleGroups/delete | 刪除防火牆原則規則群組 |
> |  | **frontDoors** |  |
> | 動作 | Microsoft.Network/frontDoors/read | 取得 Front Door |
> | 動作 | Microsoft.Network/frontDoors/write | 建立或更新 Front Door |
> | 動作 | Microsoft.Network/frontDoors/delete | 刪除 Front Door |
> | 動作 | Microsoft.Network/frontDoors/purge/action | 從前端清除快取的內容 |
> | 動作 | Microsoft.Network/frontDoors/validateCustomDomain/action | 驗證 Front Door 的前端端點 |
> |  | **frontDoors/backendPools** |  |
> | 動作 | Microsoft.Network/frontDoors/backendPools/read | 取得後端集區 |
> | 動作 | Microsoft.Network/frontDoors/backendPools/write | 建立或更新後端集區 |
> | 動作 | Microsoft.Network/frontDoors/backendPools/delete | 刪除後端集區 |
> |  | **frontDoors/frontendEndpoints** |  |
> | 動作 | Microsoft.Network/frontDoors/frontendEndpoints/read | 取得前端端點 |
> | 動作 | Microsoft.Network/frontDoors/frontendEndpoints/write | 建立或更新前端端點 |
> | 動作 | Microsoft.Network/frontDoors/frontendEndpoints/delete | 刪除前端端點 |
> | 動作 | Microsoft.Network/frontDoors/frontendEndpoints/enableHttps/action | 啟用前端端點上的 HTTPS |
> | 動作 | Microsoft.Network/frontDoors/frontendEndpoints/disableHttps/action | 停用前端端點上的 HTTPS |
> |  | **frontDoors/healthProbeSettings** |  |
> | 動作 | Microsoft.Network/frontDoors/healthProbeSettings/read | 取得健康情況探查設定 |
> | 動作 | Microsoft.Network/frontDoors/healthProbeSettings/write | 建立或更新健康情況探查設定 |
> | 動作 | Microsoft.Network/frontDoors/healthProbeSettings/delete | 刪除健康情況探查設定 |
> |  | **frontDoors/loadBalancingSettings** |  |
> | 動作 | Microsoft.Network/frontDoors/loadBalancingSettings/read | 取得負載平衡設定 |
> | 動作 | Microsoft.Network/frontDoors/loadBalancingSettings/write | 建立或更新負載平衡設定 |
> | 動作 | Microsoft.Network/frontDoors/loadBalancingSettings/delete | 建立或更新負載平衡設定 |
> |  | **frontDoors/routingRules** |  |
> | 動作 | Microsoft.Network/frontDoors/routingRules/read | 取得路由規則 |
> | 動作 | Microsoft.Network/frontDoors/routingRules/write | 建立或更新路由規則 |
> | 動作 | Microsoft.Network/frontDoors/routingRules/delete | 刪除路由規則 |
> |  | **frontDoorWebApplicationFirewallManagedRuleSets** |  |
> | 動作 | Microsoft 網路/frontDoorWebApplicationFirewallManagedRuleSets/讀取 | 取得 Web 應用程式防火牆受控規則集 |
> |  | **frontDoorWebApplicationFirewallPolicies** |  |
> | 動作 | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/read | 取得 Web 應用程式防火牆原則 |
> | 動作 | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/write | 建立或更新 Web 應用程式防火牆原則 |
> | 動作 | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/delete | 刪除 Web 應用程式防火牆原則 |
> | 動作 | Microsoft 網路/frontDoorWebApplicationFirewallPolicies/聯結/動作 | 加入 Web 應用程式防火牆原則。 未打斷。 |
> |  | **ipAllocations** |  |
> | 動作 | Microsoft 網路/ipAllocations/讀取 | 取得 IpAllocation |
> | 動作 | Microsoft 網路/ipAllocations/寫入 | 建立 IpAllocation 或更新現有的 IpAllocation |
> | 動作 | Microsoft 網路/ipAllocations/刪除 | 刪除 IpAllocation |
> |  | **ipGroups** |  |
> | 動作 | Microsoft 網路/ipGroups/讀取 | 取得 IpGroup |
> | 動作 | Microsoft 網路/ipGroups/寫入 | 建立 IpGroup 或更新現有的 IpGroups |
> | 動作 | Microsoft 網路/ipGroups/驗證/動作 | 驗證 IpGroup |
> | 動作 | Microsoft. Network/ipGroups/updateReferences/action | 更新 IpGroup 中的參考 |
> | 動作 | Microsoft 網路/ipGroups/聯結/動作 | 加入 IpGroup。 未打斷。 |
> | 動作 | Microsoft 網路/ipGroups/刪除 | 刪除 IpGroup |
> |  | **loadBalancers** |  |
> | 動作 | Microsoft.Network/loadBalancers/read | 取得負載平衡器定義 |
> | 動作 | Microsoft.Network/loadBalancers/write | 建立負載平衡器，或更新現有的負載平衡器 |
> | 動作 | Microsoft.Network/loadBalancers/delete | 刪除負載平衡器 |
> |  | **loadBalancers/Loadbalancer.backendaddresspools** |  |
> | 動作 | Microsoft.Network/loadBalancers/backendAddressPools/read | 取得負載平衡器的後端位址集區定義 |
> | 動作 | Microsoft.Network/loadBalancers/backendAddressPools/join/action | 加入負載平衡器後端位址集區。 未打斷。 |
> |  | **loadBalancers/frontendIPConfigurations** |  |
> | 動作 | Microsoft.Network/loadBalancers/frontendIPConfigurations/read | 取得負載平衡器的前端 IP 組態定義 |
> | 動作 | Microsoft.Network/loadBalancers/frontendIPConfigurations/join/action | 加入 Load Balancer Frontend IP 設定。 未打斷。 |
> |  | **loadBalancers/inboundNatPools** |  |
> | 動作 | Microsoft.Network/loadBalancers/inboundNatPools/read | 取得負載平衡器的輸入 NAT 集區定義 |
> | 動作 | Microsoft.Network/loadBalancers/inboundNatPools/join/action | 加入負載平衡器輸入 NAT 集區。 未打斷。 |
> |  | **loadBalancers/Loadbalancer.inboundnatrules** |  |
> | 動作 | Microsoft.Network/loadBalancers/inboundNatRules/read | 取得負載平衡器的輸入 NAT 規則定義 |
> | 動作 | Microsoft.Network/loadBalancers/inboundNatRules/write | 建立負載平衡器的輸入 NAT 規則，或更新現有的負載平衡器輸入 NAT 規則 |
> | 動作 | Microsoft.Network/loadBalancers/inboundNatRules/delete | 刪除負載平衡器的輸入 NAT 規則 |
> | 動作 | Microsoft.Network/loadBalancers/inboundNatRules/join/action | 加入負載平衡器輸入 nat 規則。 未打斷。 |
> |  | **loadBalancers/loadBalancingRules** |  |
> | 動作 | Microsoft.Network/loadBalancers/loadBalancingRules/read | 取得負載平衡器的負載平衡規則定義 |
> |  | **loadBalancers/networkInterfaces** |  |
> | 動作 | Microsoft.Network/loadBalancers/networkInterfaces/read | 取得負載平衡器下所有網路介面的參考 |
> |  | **loadBalancers/outboundRules** |  |
> | 動作 | Microsoft.Network/loadBalancers/outboundRules/read | 取得負載平衡器的輸出規則定義 |
> |  | **loadBalancers/探查** |  |
> | 動作 | Microsoft.Network/loadBalancers/probes/read | 取得負載平衡器探查 |
> | 動作 | Microsoft.Network/loadBalancers/probes/join/action | 允許使用負載平衡器的探查。 例如，使用此權限，VM 擴展集的 healthProbe 屬性就可以參考探查。 未打斷。 |
> |  | **loadBalancers/virtualMachines** |  |
> | 動作 | Microsoft.Network/loadBalancers/virtualMachines/read | 取得負載平衡器下所有虛擬機器的參考 |
> |  | **localnetworkgateways** |  |
> | 動作 | Microsoft.Network/localnetworkgateways/read | 取得 LocalNetworkGateway |
> | 動作 | Microsoft.Network/localnetworkgateways/write | 建立或更新現有的 LocalNetworkGateway |
> | 動作 | Microsoft.Network/localnetworkgateways/delete | 刪除 LocalNetworkGateway |
> |  | **堆放** |  |
> | 動作 | Microsoft.Network/locations/checkAcceleratedNetworkingSupport/action | 檢查加速網路支援 |
> | 動作 | Microsoft. Network/位置/batchNotifyPrivateEndpointsForResourceMove/action | 以批次方式通知私人端點，以進行資源移動。 |
> | 動作 | Microsoft. Network/位置/checkPrivateLinkServiceVisibility/action | 檢查私人連結服務可見度 |
> | 動作 | Microsoft.Network/locations/bareMetalTenants/action | 配置或驗證裸機租用戶 |
> |  | **位置/autoApprovedPrivateLinkServices** |  |
> | 動作 | Microsoft. 網路/位置/autoApprovedPrivateLinkServices/讀取 | 取得自動核准的私用連結服務 |
> |  | **位置/availableDelegations** |  |
> | 動作 | Microsoft.Network/locations/availableDelegations/read | 取得可用的委派 |
> |  | **位置/availablePrivateEndpointTypes** |  |
> | 動作 | Microsoft. 網路/位置/availablePrivateEndpointTypes/讀取 | 取得可用的私用端點資源 |
> |  | **位置/availableServiceAliases** |  |
> | 動作 | Microsoft. 網路/位置/availableServiceAliases/讀取 | 取得可用的服務別名 |
> |  | **位置/checkDnsNameAvailability** |  |
> | 動作 | Microsoft.Network/locations/checkDnsNameAvailability/read | 檢查指定的位置是否有 DNS 標籤 |
> |  | **位置/operationResults** |  |
> | 動作 | Microsoft.Network/locations/operationResults/read | 取得非同步 POST 或 DELETE 作業的作業結果 |
> |  | **位置/作業** |  |
> | 動作 | Microsoft.Network/locations/operations/read | 取得代表非同步作業狀態的作業資源 |
> |  | **位置/serviceTags** |  |
> | 動作 | Microsoft. 網路/位置/serviceTags/讀取 | 取得服務標記 |
> |  | **位置/supportedVirtualMachineSizes** |  |
> | 動作 | Microsoft.Network/locations/supportedVirtualMachineSizes/read | 取得支援的虛擬機器大小 |
> |  | **位置/使用方式** |  |
> | 動作 | Microsoft.Network/locations/usages/read | 取得資源使用量計量 |
> |  | **位置/virtualNetworkAvailableEndpointServices** |  |
> | 動作 | Microsoft.Network/locations/virtualNetworkAvailableEndpointServices/read | 取得可用虛擬網路端點服務的清單 |
> |  | **natRuleCollections** |  |
> | 動作 | Microsoft 網路/natRuleCollections/讀取 | 取得 Azure 防火牆 NatRuleCollection |
> | 動作 | Microsoft 網路/natRuleCollections/寫入 | CreatesOrUpdates Azure 防火牆 NatRuleCollection |
> | 動作 | Microsoft 網路/natRuleCollections/刪除 | 刪除 Azure 防火牆 NatRuleCollection |
> |  | **networkExperimentProfiles** |  |
> | 動作 | Microsoft 網路/networkExperimentProfiles/讀取 | 取得網際網路分析器設定檔 |
> | 動作 | Microsoft 網路/networkExperimentProfiles/寫入 | 建立或更新網際網路分析器設定檔 |
> | 動作 | Microsoft 網路/networkExperimentProfiles/刪除 | 刪除網際網路分析器設定檔 |
> |  | **networkExperimentProfiles/實驗** |  |
> | 動作 | Microsoft. Network/networkExperimentProfiles/實驗/讀取 | 取得網際網路分析器測試 |
> | 動作 | Microsoft. Network/networkExperimentProfiles/實驗/寫入 | 建立或更新網際網路分析器測試 |
> | 動作 | Microsoft 網路/networkExperimentProfiles/實驗/刪除 | 刪除網際網路分析器測試 |
> | 動作 | Microsoft. Network/networkExperimentProfiles/實驗/時間序列/action | 取得網際網路分析器測試的時間序列 |
> | 動作 | Microsoft. Network/networkExperimentProfiles/實驗/latencyScorecard/action | 取得網際網路分析器測試的延遲計分卡 |
> |  | **networkExperimentProfiles/preconfiguredEndpoints** |  |
> | 動作 | Microsoft. Network/networkExperimentProfiles/preconfiguredEndpoints/read | 取得網際網路分析器設定檔的預先設定端點 |
> |  | **networkIntentPolicies** |  |
> | 動作 | Microsoft.Network/networkIntentPolicies/read | 取得網路意圖原則描述 |
> | 動作 | Microsoft.Network/networkIntentPolicies/write | 建立網路意圖原則或更新現有的網路意圖原則 |
> | 動作 | Microsoft.Network/networkIntentPolicies/delete | 刪除網路意圖原則 |
> |  | **networkInterfaces** |  |
> | 動作 | Microsoft.Network/networkInterfaces/read | 取得網路介面定義。  |
> | 動作 | Microsoft.Network/networkInterfaces/write | 建立網路介面，或更新現有的網路介面。  |
> | 動作 | Microsoft.Network/networkInterfaces/join/action | 將虛擬機器加入網路介面。 未打斷。 |
> | 動作 | Microsoft.Network/networkInterfaces/delete | 刪除網路介面 |
> | 動作 | Microsoft.Network/networkInterfaces/effectiveRouteTable/action | 取得 VM 網路介面上所設定的路由表 |
> | 動作 | Microsoft.Network/networkInterfaces/effectiveNetworkSecurityGroups/action | 取得 VM 網路介面上所設定的網路安全性群組 |
> | 動作 | Microsoft. Network/networkInterfaces/UpdateParentNicAttachmentOnElasticNic/action | 更新與彈性 NIC 相關聯的父 NIC |
> |  | **networkInterfaces/ipconfiguration** |  |
> | 動作 | Microsoft.Network/networkInterfaces/ipconfigurations/read | 取得網路介面 IP 組態定義。  |
> | 動作 | Microsoft.Network/networkInterfaces/ipconfigurations/join/action | 加入網路介面 IP 設定。 未打斷。 |
> |  | **networkInterfaces/loadBalancers** |  |
> | 動作 | Microsoft.Network/networkInterfaces/loadBalancers/read | 取得網路介面所屬的所有負載平衡器 |
> |  | **networkInterfaces/tapConfigurations** |  |
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
> |  | **networkRuleCollections** |  |
> | 動作 | Microsoft 網路/networkRuleCollections/讀取 | 取得 Azure 防火牆 NetworkRuleCollection |
> | 動作 | Microsoft 網路/networkRuleCollections/寫入 | CreatesOrUpdates Azure 防火牆 NetworkRuleCollection |
> | 動作 | Microsoft 網路/networkRuleCollections/刪除 | 刪除 Azure 防火牆 NetworkRuleCollection |
> |  | **networkSecurityGroups** |  |
> | 動作 | Microsoft.Network/networkSecurityGroups/read | 取得網路安全性群組定義 |
> | 動作 | Microsoft.Network/networkSecurityGroups/write | 建立網路安全性群組，或更新現有的網路安全性群組 |
> | 動作 | Microsoft.Network/networkSecurityGroups/delete | 刪除網路安全性群組 |
> | 動作 | Microsoft.Network/networkSecurityGroups/join/action | 加入網路安全性群組。 未打斷。 |
> |  | **networkSecurityGroups/defaultSecurityRules** |  |
> | 動作 | Microsoft.Network/networkSecurityGroups/defaultSecurityRules/read | 取得預設的安全性規則定義 |
> |  | **networkSecurityGroups/securityRules** |  |
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
> |  | **networkWatchers/connectionMonitors** |  |
> | 動作 | Microsoft.Network/networkWatchers/connectionMonitors/start/action | 開始監視指定端點之間的連線能力 |
> | 動作 | Microsoft.Network/networkWatchers/connectionMonitors/stop/action | 停止/暫停監視指定端點之間的連線能力 |
> | 動作 | Microsoft.Network/networkWatchers/connectionMonitors/query/action | 查詢監視指定端點之間的連線能力 |
> | 動作 | Microsoft.Network/networkWatchers/connectionMonitors/read | 取得連線監視詳細資料 |
> | 動作 | Microsoft.Network/networkWatchers/connectionMonitors/write | 建立連線監視 |
> | 動作 | Microsoft.Network/networkWatchers/connectionMonitors/delete | 刪除連線監視 |
> |  | **networkWatchers/flowLogs** |  |
> | 動作 | Microsoft. Network/networkWatchers/flowLogs/read | 取得流程記錄詳細資料 |
> | 動作 | NetworkWatchers/flowLogs/write | 建立流量記錄 |
> | 動作 | Microsoft. Network/networkWatchers/flowLogs/delete | 刪除流量記錄 |
> |  | **networkWatchers/鏡頭** |  |
> | 動作 | Microsoft.Network/networkWatchers/lenses/start/action | 開始監視指定端點上的網路流量 |
> | 動作 | Microsoft.Network/networkWatchers/lenses/stop/action | 停止/暫停監視指定端點上的網路流量 |
> | 動作 | Microsoft.Network/networkWatchers/lenses/query/action | 查詢監視指定端點上的網路流量 |
> | 動作 | Microsoft.Network/networkWatchers/lenses/read | 取得功能濾鏡詳細資料 |
> | 動作 | Microsoft.Network/networkWatchers/lenses/write | 建立功能濾鏡 |
> | 動作 | Microsoft.Network/networkWatchers/lenses/delete | 刪除功能濾鏡 |
> |  | **networkWatchers/packetCaptures** |  |
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
> |  | **業務** |  |
> | 動作 | Microsoft.Network/operations/read | 取得可用的作業 |
> |  | **p2sVpnGateways** |  |
> | 動作 | Microsoft.Network/p2sVpnGateways/read | 取得 P2SVpnGateway。 |
> | 動作 | Microsoft.Network/p2sVpnGateways/write | 放置 P2SVpnGateway。 |
> | 動作 | Microsoft.Network/p2sVpnGateways/delete | 刪除 P2SVpnGateway。 |
> | 動作 | Microsoft.Network/p2sVpnGateways/generatevpnprofile/action | 產生 P2SVpnGateway 的 Vpn 設定檔 |
> | 動作 | Microsoft.Network/p2sVpnGateways/getp2svpnconnectionhealth/action | 取得 P2SVpnGateway 的 P2S Vpn 連線健康情況 |
> | 動作 | Microsoft. Network/p2sVpnGateways/getp2svpnconnectionhealthdetailed/action | 取得 P2SVpnGateway 的詳細 P2S Vpn 連線健全狀況 |
> | 動作 | Microsoft. Network/p2sVpnGateways/disconnectp2svpnconnections/action | 中斷 p2s vpn 連線的連線 |
> |  | **privateDnsOperationResults** |  |
> | 動作 | Microsoft 網路/privateDnsOperationResults/讀取 | 取得私人 DNS 操作的結果 |
> |  | **privateDnsOperationStatuses** |  |
> | 動作 | Microsoft 網路/privateDnsOperationStatuses/讀取 | 取得私人 DNS 操作的狀態 |
> |  | **privateDnsZones** |  |
> | 動作 | Microsoft 網路/privateDnsZones/讀取 | 取得 JSON 格式的私人 DNS 區域屬性。 請注意，此命令不會抓取私人 DNS 區域所連結的虛擬網路，或是包含在該區域內的記錄集。 |
> | 動作 | Microsoft 網路/privateDnsZones/寫入 | 建立或更新資源群組內的私人 DNS 區域。 請注意，此命令無法用來建立或更新區域內的虛擬網路連結或記錄集。 |
> | 動作 | Microsoft 網路/privateDnsZones/刪除 | 刪除私人 DNS 區域。 |
> | 動作 | Microsoft 網路/privateDnsZones/聯結/動作 | 加入私人 DNS 區域 |
> |  | **privateDnsZones/A** |  |
> | 動作 | Microsoft 網路/privateDnsZones/A/read | 以 JSON 格式取得私人 DNS 區域內 ' A ' 類型的記錄集。 記錄集包含記錄清單以及 TTL、標記和 etag。 |
> | 動作 | Microsoft 網路/privateDnsZones/A/write | 在私人 DNS 區域內建立或更新 ' A ' 類型的記錄集。 指定的記錄將會取代記錄集內的目前記錄。 |
> | 動作 | Microsoft 網路/privateDnsZones/A/delete | 從私人 DNS 區域中移除指定名稱的記錄集，並輸入 ' A '。 |
> |  | **privateDnsZones/AAAA** |  |
> | 動作 | Microsoft. Network/privateDnsZones/AAAA/read | 以 JSON 格式取得私人 DNS 區域內 ' AAAA ' 類型的記錄集。 記錄集包含記錄清單以及 TTL、標記和 etag。 |
> | 動作 | Microsoft 網路/privateDnsZones/AAAA/write | 在私人 DNS 區域內建立或更新 ' AAAA ' 類型的記錄集。 指定的記錄將會取代記錄集內的目前記錄。 |
> | 動作 | Microsoft 網路/privateDnsZones/AAAA/delete | 從私人 DNS 區域中移除指定名稱的記錄集，並輸入 ' AAAA '。 |
> |  | **privateDnsZones/全部** |  |
> | 動作 | Microsoft 網路/privateDnsZones/全部/讀取 | 取得跨類型的私人 DNS 記錄集 |
> |  | **privateDnsZones/CNAME** |  |
> | 動作 | Microsoft. Network/privateDnsZones/CNAME/read | 在私人 DNS 區域內，以 JSON 格式取得 ' CNAME ' 類型的記錄集。 |
> | 動作 | Microsoft 網路/privateDnsZones/CNAME/write | 在私人 DNS 區域內建立或更新 ' CNAME ' 類型的記錄集。 |
> | 動作 | Microsoft 網路/privateDnsZones/CNAME/delete | 從私人 DNS 區域中移除指定名稱的記錄集，並輸入 ' CNAME '。 |
> |  | **privateDnsZones/MX** |  |
> | 動作 | Microsoft. Network/privateDnsZones/MX/read | 在私人 DNS 區域內，以 JSON 格式取得 ' MX ' 類型的記錄集。 記錄集包含記錄清單以及 TTL、標記和 etag。 |
> | 動作 | Microsoft 網路/privateDnsZones/MX/write | 在私人 DNS 區域內建立或更新 ' MX ' 類型的記錄集。 指定的記錄將會取代記錄集內的目前記錄。 |
> | 動作 | Microsoft 網路/privateDnsZones/MX/delete | 從私人 DNS 區域中移除指定名稱和類型 ' MX ' 的記錄集。 |
> |  | **privateDnsZones/PTR** |  |
> | 動作 | Microsoft 網路/privateDnsZones/PTR/讀取 | 以 JSON 格式取得私人 DNS 區域內 ' PTR ' 類型的記錄集。 記錄集包含記錄清單以及 TTL、標記和 etag。 |
> | 動作 | Microsoft 網路/privateDnsZones/PTR/寫入 | 在私人 DNS 區域內建立或更新 ' PTR ' 類型的記錄集。 指定的記錄將會取代記錄集內的目前記錄。 |
> | 動作 | Microsoft 網路/privateDnsZones/PTR/刪除 | 從私人 DNS 區域中移除指定名稱和類型 ' PTR ' 的記錄集。 |
> |  | **privateDnsZones/記錄集** |  |
> | 動作 | Microsoft. Network/privateDnsZones/recordset/read | 取得跨類型的私人 DNS 記錄集 |
> |  | **privateDnsZones/SOA** |  |
> | 動作 | Microsoft. Network/privateDnsZones/SOA/read | 在私人 DNS 區域內，以 JSON 格式取得類型 ' SOA ' 的記錄集。 |
> | 動作 | Microsoft. Network/privateDnsZones/SOA/write | 在私人 DNS 區域內更新 ' SOA ' 類型的記錄集。 |
> |  | **privateDnsZones/SRV** |  |
> | 動作 | Microsoft. Network/privateDnsZones/SRV/read | 在私人 DNS 區域內，以 JSON 格式取得 ' SRV ' 類型的記錄集。 記錄集包含記錄清單以及 TTL、標記和 etag。 |
> | 動作 | Microsoft. Network/privateDnsZones/SRV/write | 在私人 DNS 區域內建立或更新 ' SRV ' 類型的記錄集。 指定的記錄將會取代記錄集內的目前記錄。 |
> | 動作 | Microsoft. Network/privateDnsZones/SRV/delete | 從私人 DNS 區域中，移除指定名稱和類型 ' SRV ' 的記錄集。 |
> |  | **privateDnsZones/TXT** |  |
> | 動作 | Microsoft. Network/privateDnsZones/TXT/read | 以 JSON 格式取得私人 DNS 區域內 ' TXT ' 類型的記錄集。 記錄集包含記錄清單以及 TTL、標記和 etag。 |
> | 動作 | Microsoft. Network/privateDnsZones/TXT/write | 在私人 DNS 區域內建立或更新 ' TXT ' 類型的記錄集。 指定的記錄將會取代記錄集內的目前記錄。 |
> | 動作 | Microsoft 網路/privateDnsZones/TXT/delete | 從私人 DNS 區域中移除指定名稱和類型 ' TXT ' 的記錄集。 |
> |  | **privateDnsZones/virtualNetworkLinks** |  |
> | 動作 | Microsoft. Network/privateDnsZones/virtualNetworkLinks/read | 以 JSON 格式取得虛擬網路屬性的私人 DNS 區域連結。 |
> | 動作 | PrivateDnsZones/virtualNetworkLinks/write | 建立或更新虛擬網路的私人 DNS 區域連結。 |
> | 動作 | Microsoft. Network/privateDnsZones/virtualNetworkLinks/delete | 刪除虛擬網路的私人 DNS 區域連結。 |
> |  | **privateEndpointRedirectMaps** |  |
> | 動作 | Microsoft 網路/privateEndpointRedirectMaps/讀取 | 取得私用端點 RedirectMap |
> | 動作 | Microsoft 網路/privateEndpointRedirectMaps/寫入 | 建立私用端點 RedirectMap 或更新現有的私用端點 RedirectMap |
> |  | **privateEndpoints** |  |
> | 動作 | Microsoft 網路/privateEndpoints/讀取 | 取得私用端點資源。 |
> | 動作 | Microsoft 網路/privateEndpoints/寫入 | 建立新的私用端點，或更新現有的私用端點。 |
> | 動作 | Microsoft 網路/privateEndpoints/刪除 | 刪除私用端點資源。 |
> |  | **privateEndpoints/privateDnsZoneConfigs** |  |
> | 動作 | PrivateEndpoints/privateDnsZoneConfigs/write | 放置私人 DNS 區域設定 |
> |  | **privateLinkServices** |  |
> | 動作 | Microsoft.Network/privateLinkServices/read | 取得私人連結服務資源。 |
> | 動作 | Microsoft.Network/privateLinkServices/write | 建立新的私人連結服務或更新現有的私人連結服務。 |
> | 動作 | Microsoft.Network/privateLinkServices/delete | 刪除私人連結服務資源。 |
> |  | **privateLinkServices/privateEndpointConnections** |  |
> | 動作 | Microsoft. Network/privateLinkServices/privateEndpointConnections/read | 取得私用端點連接定義。 |
> | 動作 | PrivateLinkServices/privateEndpointConnections/write | 建立新的私用端點連接，或更新現有的私用端點連線。 |
> | 動作 | Microsoft. Network/privateLinkServices/privateEndpointConnections/delete | 刪除私用端點連接。 |
> |  | **publicIPAddresses** |  |
> | 動作 | Microsoft.Network/publicIPAddresses/read | 取得公用 IP 位址定義。 |
> | 動作 | Microsoft.Network/publicIPAddresses/write | 建立公用 IP 位址，或更新現有的公用 IP 位址。  |
> | 動作 | Microsoft.Network/publicIPAddresses/delete | 刪除公用 IP 位址。 |
> | 動作 | Microsoft.Network/publicIPAddresses/join/action | 加入公用 ip 位址。 未打斷。 |
> |  | **publicIPPrefixes** |  |
> | 動作 | Microsoft.Network/publicIPPrefixes/read | 取得公用 IP 首碼定義 |
> | 動作 | Microsoft.Network/publicIPPrefixes/write | 建立公用 IP 首碼，或更新現有的公用 IP 首碼 |
> | 動作 | Microsoft.Network/publicIPPrefixes/delete | 刪除公用 IP 首碼 |
> | 動作 | Microsoft.Network/publicIPPrefixes/join/action | 加入 PublicIPPrefix。 未打斷。 |
> |  | **routeFilters** |  |
> | 動作 | Microsoft.Network/routeFilters/read | 取得路由篩選的定義 |
> | 動作 | Microsoft.Network/routeFilters/join/action | 加入路由篩選。 未打斷。 |
> | 動作 | Microsoft.Network/routeFilters/delete | 刪除路由篩選的定義 |
> | 動作 | Microsoft.Network/routeFilters/write | 建立路由篩選條件，或更新現有的路由篩選條件 |
> |  | **routeFilters/routeFilterRules** |  |
> | 動作 | Microsoft.Network/routeFilters/routeFilterRules/read | 取得路由篩選規則的定義 |
> | 動作 | Microsoft.Network/routeFilters/routeFilterRules/write | 建立路由篩選規則，或更新現有的路由篩選規則 |
> | 動作 | Microsoft.Network/routeFilters/routeFilterRules/delete | 刪除路由篩選規則的定義 |
> |  | **routeTables** |  |
> | 動作 | Microsoft.Network/routeTables/read | 取得路由表定義 |
> | 動作 | Microsoft.Network/routeTables/write | 建立路由表或更新現有的路由表 |
> | 動作 | Microsoft.Network/routeTables/delete | 刪除路由表定義 |
> | 動作 | Microsoft.Network/routeTables/join/action | 加入路由表。 未打斷。 |
> |  | **routeTables/路由** |  |
> | 動作 | Microsoft.Network/routeTables/routes/read | 取得路由定義 |
> | 動作 | Microsoft.Network/routeTables/routes/write | 建立路由，或更新現有路由 |
> | 動作 | Microsoft.Network/routeTables/routes/delete | 刪除路由定義 |
> |  | **serviceEndpointPolicies** |  |
> | 動作 | Microsoft.Network/serviceEndpointPolicies/read | 取得服務端點原則描述 |
> | 動作 | Microsoft.Network/serviceEndpointPolicies/write | 建立服務端點原則，或更新現有的服務端點原則 |
> | 動作 | Microsoft.Network/serviceEndpointPolicies/delete | 刪除服務端點原則 |
> | 動作 | Microsoft.Network/serviceEndpointPolicies/join/action | 加入服務端點原則。 未打斷。 |
> | 動作 | Microsoft.Network/serviceEndpointPolicies/joinSubnet/action | 將子網加入服務端點原則。 未打斷。 |
> |  | **serviceEndpointPolicies/serviceEndpointPolicyDefinitions** |  |
> | 動作 | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/read | 取得服務端點原則定義描述 |
> | 動作 | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/write | 建立服務端點原則定義，或更新現有的服務端點原則定義 |
> | 動作 | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/delete | 刪除服務端點原則定義 |
> |  | **trafficManagerGeographicHierarchies** |  |
> | 動作 | Microsoft.Network/trafficManagerGeographicHierarchies/read | 取得流量管理員的地理階層，其所含的區域可用在地理流量路由方法中 |
> |  | **trafficManagerProfiles** |  |
> | 動作 | Microsoft.Network/trafficManagerProfiles/read | 取得流量管理員設定檔組態。 這包括 DNS 設定、流量路由設定、端點監視設定，以及此流量管理員設定檔所路由之端點的清單。 |
> | 動作 | Microsoft.Network/trafficManagerProfiles/write | 建立流量管理員設定檔，或修改現有流量管理員設定檔的組態。<br>這包括啟用或停用設定檔以及修改 DNS 設定、流量路由設定或端點監視設定。<br>您可以新增、移除、啟用或停用由流量管理員設定檔所路由傳送的端點。 |
> | 動作 | Microsoft.Network/trafficManagerProfiles/delete | 刪除流量管理員設定檔。 與流量管理員設定檔相關聯的所有設定都會遺失，而且您無法再使用設定檔來路由傳送流量。 |
> |  | **trafficManagerProfiles/azureEndpoints** |  |
> | 動作 | Microsoft.Network/trafficManagerProfiles/azureEndpoints/read | 取得屬於流量管理員設定檔的 Azure 端點，其中包含該 Azure 端點的所有屬性。 |
> | 動作 | Microsoft.Network/trafficManagerProfiles/azureEndpoints/write | 在現有流量管理員設定檔中新增 Azure 端點，或在該流量管理員設定檔中更新現有 Azure 端點的屬性。 |
> | 動作 | Microsoft.Network/trafficManagerProfiles/azureEndpoints/delete | 從現有的流量管理員設定檔刪除 Azure 端點。 流量管理員將會停止將流量路由傳送到已刪除的 Azure 端點。 |
> |  | **trafficManagerProfiles/externalEndpoints** |  |
> | 動作 | Microsoft.Network/trafficManagerProfiles/externalEndpoints/read | 取得屬於流量管理員設定檔的外部端點，其中包含該外部端點的所有屬性。 |
> | 動作 | Microsoft.Network/trafficManagerProfiles/externalEndpoints/write | 在現有流量管理員設定檔中新增外部端點，或在該流量管理員設定檔中更新現有外部端點的屬性。 |
> | 動作 | Microsoft.Network/trafficManagerProfiles/externalEndpoints/delete | 從現有的流量管理員設定檔刪除外部端點。 流量管理員將會停止將流量路由傳送到已刪除的外部端點。 |
> |  | **trafficManagerProfiles/熱度圖** |  |
> | 動作 | Microsoft.Network/trafficManagerProfiles/heatMaps/read | 取得指定流量管理員設定檔的流量管理員熱度圖，其中包含依位置和來源 IP 的查詢計數和延遲資料。 |
> |  | **trafficManagerProfiles/nestedEndpoints** |  |
> | 動作 | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/read | 取得屬於流量管理員設定檔的巢狀端點，其中包含該巢狀端點的所有屬性。 |
> | 動作 | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/write | 在現有流量管理員設定檔中新增巢狀端點，或在該流量管理員設定檔中更新現有巢狀端點的屬性。 |
> | 動作 | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/delete | 從現有的流量管理員設定檔刪除巢狀端點。 流量管理員將會停止將流量路由傳送到已刪除的巢狀端點。 |
> |  | **trafficManagerUserMetricsKeys** |  |
> | 動作 | Microsoft.Network/trafficManagerUserMetricsKeys/read | 取得針對即時使用者計量集合使用的訂用帳戶層級金鑰。 |
> | 動作 | Microsoft.Network/trafficManagerUserMetricsKeys/write | 建立要針對即時使用者計量集合使用的新訂用帳戶層級金鑰。 |
> | 動作 | Microsoft.Network/trafficManagerUserMetricsKeys/delete | 刪除針對即時使用者計量集合使用的訂用帳戶層級金鑰。 |
> |  | **virtualHubs** |  |
> | 動作 | Microsoft.Network/virtualHubs/delete | 刪除虛擬中樞 |
> | 動作 | Microsoft.Network/virtualHubs/read | 取得虛擬中樞 |
> | 動作 | Microsoft.Network/virtualHubs/write | 建立或更新虛擬中樞 |
> | 動作 | Microsoft. Network/virtualHubs/effectiveRoutes/action | 取得在虛擬中樞上設定的有效路由 |
> |  | **virtualHubs/hubVirtualNetworkConnections** |  |
> | 動作 | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/read | 取得 HubVirtualNetworkConnection |
> | 動作 | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/write | 建立或更新 HubVirtualNetworkConnection |
> | 動作 | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/delete | 刪除 HubVirtualNetworkConnection |
> |  | **virtualHubs/routeTables** |  |
> | 動作 | Microsoft. Network/virtualHubs/routeTables/read | 取得 VirtualHubRouteTableV2 |
> | 動作 | VirtualHubs/routeTables/write | 建立或更新 VirtualHubRouteTableV2 |
> | 動作 | Microsoft. Network/virtualHubs/routeTables/delete | 刪除 VirtualHubRouteTableV2 |
> |  | **virtualnetworkgateways** |  |
> | 動作 | Microsoft.Network/virtualnetworkgateways/supportedvpndevices/action | 列出支援的 VPN 裝置 |
> | 動作 | Microsoft.Network/virtualNetworkGateways/read | 取得 VirtualNetworkGateway |
> | 動作 | Microsoft.Network/virtualNetworkGateways/write | 建立或更新 VirtualNetworkGateway |
> | 動作 | Microsoft.Network/virtualNetworkGateways/delete | 刪除 virtualNetworkGateway |
> | 動作 | microsoft.network/virtualnetworkgateways/generatevpnclientpackage/action | 針對 virtualNetworkGateway 產生 VpnClient 套件 |
> | 動作 | microsoft.network/virtualnetworkgateways/generatevpnprofile/action | 針對 VirtualNetworkGateway 產生 VpnProfile 套件 |
> | 動作 | microsoft.network/virtualnetworkgateways/getvpnclientconnectionhealth/action | 針對 VirtualNetworkGateway 取得每個 VPN 用戶端連線健康情況 |
> | 動作 | microsoft. network/virtualnetworkgateways/disconnectvirtualnetworkgatewayvpnconnections/action | 中斷虛擬網路閘道 vpn 連線的連線 |
> | 動作 | microsoft.network/virtualnetworkgateways/getvpnprofilepackageurl/action | 取得預先產生之 VPN 用戶端設定檔套件的 URL |
> | 動作 | microsoft.network/virtualnetworkgateways/setvpnclientipsecparameters/action | 設定適用於 VirtualNetworkGateway P2S 用戶端的 Vpnclient Ipsec 參數。 |
> | 動作 | microsoft.network/virtualnetworkgateways/getvpnclientipsecparameters/action | 取得適用於 VirtualNetworkGateway P2S 用戶端的 Vpnclient Ipsec 參數。 |
> | 動作 | microsoft.network/virtualnetworkgateways/resetvpnclientsharedkey/action | 重設適用於 VirtualNetworkGateway P2S 用戶端的 Vpnclient 共用金鑰。 |
> | 動作 | microsoft.network/virtualnetworkgateways/reset/action | 重設 virtualNetworkGateway |
> | 動作 | microsoft.network/virtualnetworkgateways/getadvertisedroutes/action | 取得 virtualNetworkGateway 通告路由 |
> | 動作 | microsoft.network/virtualnetworkgateways/getbgppeerstatus/action | 取得 virtualNetworkGateway bgp 對等狀態 |
> | 動作 | microsoft.network/virtualnetworkgateways/getlearnedroutes/action | 取得 virtualnetworkgateway 學習到的路由 |
> | 動作 | microsoft. network/virtualnetworkgateways/startpacketcapture/action | 啟動虛擬網路閘道封包捕獲。 |
> | 動作 | microsoft. network/virtualnetworkgateways/stoppacketcapture/action | 停止虛擬網路閘道封包捕獲。 |
> |  | **virtualnetworkgateways/connections** |  |
> | 動作 | microsoft.network/virtualnetworkgateways/connections/read | 取得 VirtualNetworkGatewayConnection |
> |  | **virtualNetworks** |  |
> | 動作 | Microsoft.Network/virtualNetworks/read | 取得虛擬網路定義 |
> | 動作 | Microsoft.Network/virtualNetworks/write | 建立虛擬網路，或更新現有的虛擬網路 |
> | 動作 | Microsoft.Network/virtualNetworks/delete | 刪除虛擬網路 |
> | 動作 | Microsoft.Network/virtualNetworks/peer/action | 讓某個虛擬網路與另一個虛擬網路對等互連 |
> | 動作 | Microsoft 網路/virtualNetworks/聯結/動作 | 加入虛擬網路。 未打斷。 |
> | 動作 | Microsoft.Network/virtualNetworks/BastionHosts/action | 取得虛擬網路中的堡壘主機參考。 |
> |  | **virtualNetworks/bastionHosts** |  |
> | 動作 | Microsoft 網路/virtualNetworks/bastionHosts/預設/動作 | 取得虛擬網路中的堡壘主機參考。 |
> |  | **virtualNetworks/checkIpAddressAvailability** |  |
> | 動作 | Microsoft.Network/virtualNetworks/checkIpAddressAvailability/read | 檢查指定的虛擬網路中是否有 IP 位址 |
> |  | **virtualNetworks/子網** |  |
> | 動作 | Microsoft.Network/virtualNetworks/subnets/read | 取得虛擬網路子網路定義 |
> | 動作 | Microsoft.Network/virtualNetworks/subnets/write | 建立虛擬網路子網路，或更新現有的虛擬網路子網路 |
> | 動作 | Microsoft.Network/virtualNetworks/subnets/delete | 刪除虛擬網路子網路 |
> | 動作 | Microsoft.Network/virtualNetworks/subnets/join/action | 加入虛擬網路。 未打斷。 |
> | 動作 | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | 將資源 (例如，儲存體帳戶或 SQL Database) 加入至子網路。 未打斷。 |
> | 動作 | Microsoft.Network/virtualNetworks/subnets/prepareNetworkPolicies/action | 藉由套用必要的網路原則來準備子網路 |
> | 動作 | Microsoft 網路/virtualNetworks/子網/unprepareNetworkPolicies/action | 藉由移除已套用的網路原則來 Unprepare 子網 |
> |  | **virtualNetworks/子網/coNtextualServiceEndpointPolicies** |  |
> | 動作 | Microsoft 網路/virtualNetworks/子網/coNtextualServiceEndpointPolicies/read | 取得內容相關服務端點原則 |
> | 動作 | Microsoft 網路/virtualNetworks/子網/coNtextualServiceEndpointPolicies/write | 建立內容相關服務端點原則，或更新現有的內容服務端點原則 |
> | 動作 | Microsoft 網路/virtualNetworks/子網/coNtextualServiceEndpointPolicies/delete | 刪除內容相關的服務端點原則 |
> |  | **virtualNetworks/子網/virtualMachines** |  |
> | 動作 | Microsoft.Network/virtualNetworks/subnets/virtualMachines/read | 取得虛擬網路子網路中所有虛擬機器的參考 |
> |  | **virtualNetworks/使用方式** |  |
> | 動作 | Microsoft.Network/virtualNetworks/usages/read | 取得虛擬網路中每個子網路的 IP 使用方式 |
> |  | **virtualNetworks/virtualMachines** |  |
> | 動作 | Microsoft.Network/virtualNetworks/virtualMachines/read | 取得虛擬網路中所有虛擬機器的參考 |
> |  | **virtualNetworks/virtualNetworkPeerings** |  |
> | 動作 | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/read | 取得虛擬網路對等互連定義 |
> | 動作 | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write | 建立虛擬網路對等互連，或更新現有的虛擬網路對等互連 |
> | 動作 | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/delete | 刪除虛擬網路對等互連 |
> |  | **virtualNetworkTaps** |  |
> | 動作 | Microsoft.Network/virtualNetworkTaps/read | 取得 Virtual Network Tap |
> | 動作 | Microsoft.Network/virtualNetworkTaps/join/action | 加入虛擬網路點路。 未打斷。 |
> | 動作 | Microsoft.Network/virtualNetworkTaps/delete | 刪除Virtual Network Tap |
> | 動作 | Microsoft.Network/virtualNetworkTaps/write | 建立或更新 Virtual Network Tap |
> |  | **virtualRouters** |  |
> | 動作 | Microsoft 網路/virtualRouters/讀取 | 取得 VirtualRouter |
> | 動作 | Microsoft 網路/virtualRouters/寫入 | 建立 VirtualRouter 或更新現有的 VirtualRouter |
> | 動作 | Microsoft 網路/virtualRouters/刪除 | 刪除 VirtualRouter |
> | 動作 | Microsoft 網路/virtualRouters/聯結/動作 | 加入 VirtualRouter。 未打斷。 |
> |  | **virtualRouters/對等互連** |  |
> | 動作 | Microsoft. Network/virtualRouters/對等互連/read | 取得 VirtualRouterPeering |
> | 動作 | VirtualRouters/對等互連/write | 建立 VirtualRouterPeering 或更新現有的 VirtualRouterPeering |
> | 動作 | Microsoft. Network/virtualRouters/對等互連/delete | 刪除 VirtualRouterPeering |
> |  | **virtualWans** |  |
> | 動作 | Microsoft.Network/virtualWans/delete | 刪除虛擬 WAN |
> | 動作 | Microsoft.Network/virtualWans/read | 取得虛擬 WAN |
> | 動作 | Microsoft.Network/virtualWans/write | 建立或更新虛擬 WAN |
> | 動作 | Microsoft.Network/virtualwans/vpnconfiguration/action | 取得 VPN 設定 |
> | 動作 | Microsoft. Network/virtualwans/vpnServerConfigurations/action | 取得 VirtualWanVpnServerConfigurations |
> | 動作 | Microsoft. Network/virtualwans/generateVpnProfile/action | 產生 VirtualWanVpnServerConfiguration VpnProfile |
> |  | **virtualWans/p2sVpnServerConfigurations** |  |
> | 動作 | Microsoft.Network/virtualWans/p2sVpnServerConfigurations/read | 取得虛擬 Wan P2SVpnServerConfiguration |
> | 動作 | Microsoft.network/virtualWans/p2sVpnServerConfigurations/write | 建立虛擬 Wan P2SVpnServerConfiguration 或更新現有的虛擬 Wan P2SVpnServerConfiguration |
> | 動作 | Microsoft.network/virtualWans/p2sVpnServerConfigurations/delete | 刪除虛擬 Wan P2SVpnServerConfiguration |
> |  | **virtualwans/supportedSecurityProviders** |  |
> | 動作 | Microsoft.Network/virtualwans/supportedSecurityProviders/read | 取得支援的 VirtualWan 安全性提供者。 |
> |  | **virtualWans/virtualHubs** |  |
> | 動作 | Microsoft.Network/virtualWans/virtualHubs/read | 取得所有參考虛擬 WAN 的虛擬中樞。 |
> |  | **virtualWans/vpnSites** |  |
> | 動作 | Microsoft.Network/virtualWans/vpnSites/read | 取得所有參考虛擬 WAN 的 VPN 網站。 |
> |  | **vpnGateways** |  |
> | 動作 | Microsoft.Network/vpnGateways/read | 取得 VpnGateway。 |
> | 動作 | Microsoft.Network/vpnGateways/write | 放置 VpnGateway。 |
> | 動作 | Microsoft.Network/vpnGateways/delete | 刪除 VpnGateway。 |
> | 動作 | microsoft.network/vpngateways/reset/action | 重設 VpnGateway |
> | 動作 | microsoft.network/vpngateways/listvpnconnectionshealth/action | 取得 VpnGateway 上所有或部分連線的連線健康情況 |
> |  | **vpnGateways/vpnConnections** |  |
> | 動作 | microsoft.network/vpnGateways/vpnConnections/read | 取得 VpnConnection。 |
> | 動作 | microsoft.network/vpnGateways/vpnConnections/write | 放置 VpnConnection。 |
> | 動作 | microsoft.network/vpnGateways/vpnConnections/delete | 刪除 VpnConnection。 |
> |  | **vpnGateways/vpnConnections/vpnLinkConnections** |  |
> | 動作 | microsoft. network/vpnGateways/vpnConnections/vpnLinkConnections/read | 取得 Vpn 連結連線 |
> |  | **vpnServerConfigurations** |  |
> | 動作 | Microsoft 網路/vpnServerConfigurations/讀取 | 取得 VpnServerConfiguration |
> | 動作 | Microsoft 網路/vpnServerConfigurations/寫入 | 建立或更新 VpnServerConfiguration |
> | 動作 | Microsoft 網路/vpnServerConfigurations/刪除 | 刪除 VpnServerConfiguration |
> |  | **vpnsites** |  |
> | 動作 | Microsoft.Network/vpnsites/read | 取得 VPN 網站資源。 |
> | 動作 | Microsoft.Network/vpnsites/write | 建立或更新 VPN 網站資源。 |
> | 動作 | Microsoft.Network/vpnsites/delete | 刪除 VPN 網站資源。 |
> |  | **vpnSites/vpnSiteLinks** |  |
> | 動作 | microsoft. network/vpnSites/vpnSiteLinks/read | 取得 Vpn 站台連結 |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

Azure 服務：[通知中樞](../notification-hubs/index.yml)

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
> |  | **命名空間/authorizationRules** |  |
> | 動作 | Microsoft.NotificationHubs/Namespaces/authorizationRules/write | 建立命名空間層級的授權規則，並更新其屬性。 您可以更新授權規則的存取權限、主要金鑰和次要金鑰。 |
> | 動作 | Microsoft.NotificationHubs/Namespaces/authorizationRules/read | 取得命名空間授權規則描述的清單。 |
> | 動作 | Microsoft.NotificationHubs/Namespaces/authorizationRules/delete | 刪除命名空間授權規則。 您無法刪除預設的命名空間授權規則。  |
> | 動作 | Microsoft.NotificationHubs/Namespaces/authorizationRules/listkeys/action | 取得命名空間的連接字串 |
> | 動作 | Microsoft.NotificationHubs/Namespaces/authorizationRules/regenerateKeys/action | 命名空間授權規則會重新產生主要/次要金鑰，指定需要重新產生的金鑰 |
> |  | **命名空間/NotificationHubs** |  |
> | 動作 | Microsoft.NotificationHubs/Namespaces/NotificationHubs/write | 建立通知中樞，並更新其屬性。 其屬性主要包括 PNS 認證。 授權規則與 TTL |
> | 動作 | Microsoft.NotificationHubs/Namespaces/NotificationHubs/read | 取得通知中樞資源描述的清單 |
> | 動作 | Microsoft.NotificationHubs/Namespaces/NotificationHubs/Delete | 刪除通知中樞資源 |
> | 動作 | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/action | 取得通知中樞授權規則的清單 |
> | 動作 | Microsoft.NotificationHubs/Namespaces/NotificationHubs/pnsCredentials/action | 取得所有通知中樞的 PNS 認證。 這包括 WNS、MPNS、APNS、GCM 和百度認證 |
> | 動作 | Microsoft.NotificationHubs/Namespaces/NotificationHubs/debugSend/action | 傳送測試推播通知。 |
> |  | **命名空間/NotificationHubs/authorizationRules** |  |
> | 動作 | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/write | 建立通知中樞授權規則，並更新其屬性。 您可以更新授權規則的存取權限、主要金鑰和次要金鑰。 |
> | 動作 | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/read | 取得通知中樞授權規則的清單 |
> | 動作 | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/delete | 刪除通知中樞的授權規則 |
> | 動作 | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/listkeys/action | 取得通知中樞的連接字串 |
> | 動作 | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/regenerateKeys/action | 通知中樞授權規則會重新產生主要/次要金鑰，指定需要重新產生的金鑰 |
> |  | **命名空間/NotificationHubs/Metricdefinitions.listasync** |  |
> | 動作 | Microsoft.NotificationHubs/Namespaces/NotificationHubs/metricDefinitions/read | 取得命名空間計量資源描述的清單 |
> |  | **operationResults** |  |
> | 動作 | Microsoft.NotificationHubs/operationResults/read | 傳回通知中樞提供者的作業結果 |
> |  | **業務** |  |
> | 動作 | Microsoft.NotificationHubs/operations/read | 傳回通知中樞提供者支援的作業清單 |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

Azure 服務： [Azure Migrate](../migrate/migrate-services-overview.md)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.OffAzure/register/action | 向 Microsoft.OffAzure 資源提供者註冊訂用帳戶 |
> | 動作 | Microsoft.OffAzure/register/action | 向 Microsoft.OffAzure 資源提供者註冊訂用帳戶 |
> | 動作 | Microsoft.OffAzure/register/action | 向 Microsoft.OffAzure 資源提供者註冊訂用帳戶 |
> | 動作 | Microsoft.OffAzure/register/action | 向 Microsoft.OffAzure 資源提供者註冊訂用帳戶 |
> |  | **HyperVSites** |  |
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
> |  | **HyperVSites/叢集** |  |
> | 動作 | Microsoft.OffAzure/HyperVSites/clusters/read | 取得 Hyper-V 叢集的屬性 |
> | 動作 | Microsoft.OffAzure/HyperVSites/clusters/write | 建立或更新 Hyper-V 叢集 |
> | 動作 | Microsoft.OffAzure/HyperVSites/clusters/read | 取得 Hyper-V 叢集的屬性 |
> | 動作 | Microsoft.OffAzure/HyperVSites/clusters/write | 建立或更新 Hyper-V 叢集 |
> | 動作 | Microsoft.OffAzure/HyperVSites/clusters/read | 取得 Hyper-V 叢集的屬性 |
> | 動作 | Microsoft.OffAzure/HyperVSites/clusters/write | 建立或更新 Hyper-V 叢集 |
> | 動作 | Microsoft.OffAzure/HyperVSites/clusters/read | 取得 Hyper-V 叢集的屬性 |
> | 動作 | Microsoft.OffAzure/HyperVSites/clusters/write | 建立或更新 Hyper-V 叢集 |
> |  | **HyperVSites/healthsummary** |  |
> | 動作 | OffAzure/HyperVSites/healthsummary/read | 取得 Hyper-v 資源的健全狀況摘要 |
> | 動作 | OffAzure/HyperVSites/healthsummary/read | 取得 Hyper-v 資源的健全狀況摘要 |
> | 動作 | OffAzure/HyperVSites/healthsummary/read | 取得 Hyper-v 資源的健全狀況摘要 |
> | 動作 | OffAzure/HyperVSites/healthsummary/read | 取得 Hyper-v 資源的健全狀況摘要 |
> |  | **HyperVSites/主機** |  |
> | 動作 | Microsoft.OffAzure/HyperVSites/hosts/read | 取得 Hyper-V 主機的屬性 |
> | 動作 | Microsoft.OffAzure/HyperVSites/hosts/write | 建立或更新 Hyper-V 主機 |
> | 動作 | Microsoft.OffAzure/HyperVSites/hosts/read | 取得 Hyper-V 主機的屬性 |
> | 動作 | Microsoft.OffAzure/HyperVSites/hosts/write | 建立或更新 Hyper-V 主機 |
> | 動作 | Microsoft.OffAzure/HyperVSites/hosts/read | 取得 Hyper-V 主機的屬性 |
> | 動作 | Microsoft.OffAzure/HyperVSites/hosts/write | 建立或更新 Hyper-V 主機 |
> | 動作 | Microsoft.OffAzure/HyperVSites/hosts/read | 取得 Hyper-V 主機的屬性 |
> | 動作 | Microsoft.OffAzure/HyperVSites/hosts/write | 建立或更新 Hyper-V 主機 |
> |  | **HyperVSites/作業** |  |
> | 動作 | Microsoft.OffAzure/HyperVSites/jobs/read | 取得 Hyper-V 作業的屬性 |
> | 動作 | Microsoft.OffAzure/HyperVSites/jobs/read | 取得 Hyper-V 作業的屬性 |
> | 動作 | Microsoft.OffAzure/HyperVSites/jobs/read | 取得 Hyper-V 作業的屬性 |
> | 動作 | Microsoft.OffAzure/HyperVSites/jobs/read | 取得 Hyper-V 作業的屬性 |
> |  | **HyperVSites/機器** |  |
> | 動作 | Microsoft.OffAzure/HyperVSites/machines/read | 取得 Hyper-V 機器的屬性 |
> | 動作 | Microsoft.OffAzure/HyperVSites/machines/read | 取得 Hyper-V 機器的屬性 |
> | 動作 | Microsoft.OffAzure/HyperVSites/machines/read | 取得 Hyper-V 機器的屬性 |
> | 動作 | Microsoft.OffAzure/HyperVSites/machines/read | 取得 Hyper-V 機器的屬性 |
> |  | **HyperVSites/operationsstatus** |  |
> | 動作 | Microsoft.OffAzure/HyperVSites/operationsstatus/read | 取得 Hyper-V 作業狀態的屬性 |
> | 動作 | Microsoft.OffAzure/HyperVSites/operationsstatus/read | 取得 Hyper-V 作業狀態的屬性 |
> | 動作 | Microsoft.OffAzure/HyperVSites/operationsstatus/read | 取得 Hyper-V 作業狀態的屬性 |
> | 動作 | Microsoft.OffAzure/HyperVSites/operationsstatus/read | 取得 Hyper-V 作業狀態的屬性 |
> |  | **HyperVSites/執行身分帳戶** |  |
> | 動作 | Microsoft.OffAzure/HyperVSites/runasaccounts/read | 取得 Hyper-V 執行身分帳戶的屬性 |
> | 動作 | Microsoft.OffAzure/HyperVSites/runasaccounts/read | 取得 Hyper-V 執行身分帳戶的屬性 |
> | 動作 | Microsoft.OffAzure/HyperVSites/runasaccounts/read | 取得 Hyper-V 執行身分帳戶的屬性 |
> | 動作 | Microsoft.OffAzure/HyperVSites/runasaccounts/read | 取得 Hyper-V 執行身分帳戶的屬性 |
> |  | **HyperVSites/摘要** |  |
> | 動作 | OffAzure/HyperVSites/summary/read | 取得 Hyper-v 網站的摘要 |
> | 動作 | OffAzure/HyperVSites/summary/read | 取得 Hyper-v 網站的摘要 |
> | 動作 | OffAzure/HyperVSites/summary/read | 取得 Hyper-v 網站的摘要 |
> | 動作 | OffAzure/HyperVSites/summary/read | 取得 Hyper-v 網站的摘要 |
> |  | **HyperVSites/使用量** |  |
> | 動作 | Microsoft.OffAzure/HyperVSites/usage/read | 取得 Hyper-V 站台的使用情形 |
> | 動作 | Microsoft.OffAzure/HyperVSites/usage/read | 取得 Hyper-V 站台的使用情形 |
> | 動作 | Microsoft.OffAzure/HyperVSites/usage/read | 取得 Hyper-V 站台的使用情形 |
> | 動作 | Microsoft.OffAzure/HyperVSites/usage/read | 取得 Hyper-V 站台的使用情形 |
> |  | **ImportSites** |  |
> | 動作 | OffAzure/ImportSites/read | 取得匯入網站的屬性 |
> | 動作 | OffAzure/ImportSites/write | 建立或更新匯入網站 |
> | 動作 | OffAzure/ImportSites/delete | 刪除匯入網站 |
> | 動作 | OffAzure/ImportSites/importuri/action | 取得用於匯入電腦 CSV 檔案的 SAS uri。 |
> | 動作 | OffAzure/ImportSites/exporturi/action | 取得用於匯出電腦 CSV 檔案的 SAS uri。 |
> | 動作 | OffAzure/ImportSites/read | 取得匯入網站的屬性 |
> | 動作 | OffAzure/ImportSites/write | 建立或更新匯入網站 |
> | 動作 | OffAzure/ImportSites/delete | 刪除匯入網站 |
> | 動作 | OffAzure/ImportSites/importuri/action | 取得用於匯入電腦 CSV 檔案的 SAS uri。 |
> | 動作 | OffAzure/ImportSites/exporturi/action | 取得用於匯出電腦 CSV 檔案的 SAS uri。 |
> | 動作 | OffAzure/ImportSites/read | 取得匯入網站的屬性 |
> | 動作 | OffAzure/ImportSites/write | 建立或更新匯入網站 |
> | 動作 | OffAzure/ImportSites/delete | 刪除匯入網站 |
> | 動作 | OffAzure/ImportSites/importuri/action | 取得用於匯入電腦 CSV 檔案的 SAS uri。 |
> | 動作 | OffAzure/ImportSites/exporturi/action | 取得用於匯出電腦 CSV 檔案的 SAS uri。 |
> | 動作 | OffAzure/ImportSites/read | 取得匯入網站的屬性 |
> | 動作 | OffAzure/ImportSites/write | 建立或更新匯入網站 |
> | 動作 | OffAzure/ImportSites/delete | 刪除匯入網站 |
> | 動作 | OffAzure/ImportSites/importuri/action | 取得用於匯入電腦 CSV 檔案的 SAS uri。 |
> | 動作 | OffAzure/ImportSites/exporturi/action | 取得用於匯出電腦 CSV 檔案的 SAS uri。 |
> |  | **ImportSites/作業** |  |
> | 動作 | OffAzure/ImportSites/作業/讀取 | 取得匯入作業的屬性 |
> | 動作 | OffAzure/ImportSites/作業/讀取 | 取得匯入作業的屬性 |
> | 動作 | OffAzure/ImportSites/作業/讀取 | 取得匯入作業的屬性 |
> | 動作 | OffAzure/ImportSites/作業/讀取 | 取得匯入作業的屬性 |
> |  | **ImportSites/機器** |  |
> | 動作 | OffAzure/ImportSites/機器/讀取 | 取得匯入電腦的屬性 |
> | 動作 | OffAzure/ImportSites/機器/刪除 | 刪除匯入電腦 |
> | 動作 | OffAzure/ImportSites/機器/讀取 | 取得匯入電腦的屬性 |
> | 動作 | OffAzure/ImportSites/機器/刪除 | 刪除匯入電腦 |
> | 動作 | OffAzure/ImportSites/機器/讀取 | 取得匯入電腦的屬性 |
> | 動作 | OffAzure/ImportSites/機器/刪除 | 刪除匯入電腦 |
> | 動作 | OffAzure/ImportSites/機器/讀取 | 取得匯入電腦的屬性 |
> | 動作 | OffAzure/ImportSites/機器/刪除 | 刪除匯入電腦 |
> |  | **作業** |  |
> | 動作 | Microsoft.OffAzure/Operations/read | 讀取公開的作業 |
> |  | **ServerSites** |  |
> | 動作 | OffAzure/ServerSites/read | 取得伺服器網站的屬性 |
> | 動作 | OffAzure/ServerSites/write | 建立或補救伺服器網站 |
> | 動作 | OffAzure/ServerSites/delete | 刪除伺服器網站 |
> | 動作 | OffAzure/ServerSites/refresh/action | 重新整理伺服器網站內的物件 |
> | 動作 | OffAzure/ServerSites/read | 取得伺服器網站的屬性 |
> | 動作 | OffAzure/ServerSites/write | 建立或補救伺服器網站 |
> | 動作 | OffAzure/ServerSites/delete | 刪除伺服器網站 |
> | 動作 | OffAzure/ServerSites/refresh/action | 重新整理伺服器網站內的物件 |
> | 動作 | OffAzure/ServerSites/read | 取得伺服器網站的屬性 |
> | 動作 | OffAzure/ServerSites/write | 建立或補救伺服器網站 |
> | 動作 | OffAzure/ServerSites/delete | 刪除伺服器網站 |
> | 動作 | OffAzure/ServerSites/refresh/action | 重新整理伺服器網站內的物件 |
> | 動作 | OffAzure/ServerSites/read | 取得伺服器網站的屬性 |
> | 動作 | OffAzure/ServerSites/write | 建立或補救伺服器網站 |
> | 動作 | OffAzure/ServerSites/delete | 刪除伺服器網站 |
> | 動作 | OffAzure/ServerSites/refresh/action | 重新整理伺服器網站內的物件 |
> |  | **ServerSites/作業** |  |
> | 動作 | OffAzure/ServerSites/作業/讀取 | 取得伺服器作業的屬性 |
> | 動作 | OffAzure/ServerSites/作業/讀取 | 取得伺服器作業的屬性 |
> | 動作 | OffAzure/ServerSites/作業/讀取 | 取得伺服器作業的屬性 |
> | 動作 | OffAzure/ServerSites/作業/讀取 | 取得伺服器作業的屬性 |
> |  | **ServerSites/機器** |  |
> | 動作 | OffAzure/ServerSites/機器/讀取 | 取得伺服器電腦的屬性 |
> | 動作 | OffAzure/ServerSites/機器/寫入 | 寫入伺服器電腦的屬性 |
> | 動作 | OffAzure/ServerSites/機器/讀取 | 取得伺服器電腦的屬性 |
> | 動作 | OffAzure/ServerSites/機器/寫入 | 寫入伺服器電腦的屬性 |
> | 動作 | OffAzure/ServerSites/機器/讀取 | 取得伺服器電腦的屬性 |
> | 動作 | OffAzure/ServerSites/機器/寫入 | 寫入伺服器電腦的屬性 |
> | 動作 | OffAzure/ServerSites/機器/讀取 | 取得伺服器電腦的屬性 |
> | 動作 | OffAzure/ServerSites/機器/寫入 | 寫入伺服器電腦的屬性 |
> |  | **ServerSites/operationsstatus** |  |
> | 動作 | OffAzure/ServerSites/operationsstatus/read | 取得伺服器作業狀態的屬性 |
> | 動作 | OffAzure/ServerSites/operationsstatus/read | 取得伺服器作業狀態的屬性 |
> | 動作 | OffAzure/ServerSites/operationsstatus/read | 取得伺服器作業狀態的屬性 |
> | 動作 | OffAzure/ServerSites/operationsstatus/read | 取得伺服器作業狀態的屬性 |
> |  | **ServerSites/執行身分帳戶** |  |
> | 動作 | OffAzure/ServerSites/執行身分帳戶/read | 取得伺服器執行身分帳戶的屬性 |
> | 動作 | OffAzure/ServerSites/執行身分帳戶/read | 取得伺服器執行身分帳戶的屬性 |
> | 動作 | OffAzure/ServerSites/執行身分帳戶/read | 取得伺服器執行身分帳戶的屬性 |
> | 動作 | OffAzure/ServerSites/執行身分帳戶/read | 取得伺服器執行身分帳戶的屬性 |
> |  | **ServerSites/摘要** |  |
> | 動作 | OffAzure/ServerSites/summary/read | 取得伺服器網站的摘要 |
> | 動作 | OffAzure/ServerSites/summary/read | 取得伺服器網站的摘要 |
> | 動作 | OffAzure/ServerSites/summary/read | 取得伺服器網站的摘要 |
> | 動作 | OffAzure/ServerSites/summary/read | 取得伺服器網站的摘要 |
> |  | **ServerSites/使用量** |  |
> | 動作 | OffAzure/ServerSites/使用量/讀取 | 取得伺服器網站的使用方式 |
> | 動作 | OffAzure/ServerSites/使用量/讀取 | 取得伺服器網站的使用方式 |
> | 動作 | OffAzure/ServerSites/使用量/讀取 | 取得伺服器網站的使用方式 |
> | 動作 | OffAzure/ServerSites/使用量/讀取 | 取得伺服器網站的使用方式 |
> |  | **VMwareSites** |  |
> | 動作 | Microsoft.OffAzure/VMwareSites/read | 取得 VMware 站台的屬性 |
> | 動作 | Microsoft.OffAzure/VMwareSites/write | 建立或更新 VMware 站台 |
> | 動作 | Microsoft.OffAzure/VMwareSites/delete | 刪除 VMware 站台 |
> | 動作 | Microsoft.OffAzure/VMwareSites/refresh/action | 重新整理 VMware 站台內的物件 |
> | 動作 | OffAzure/VMwareSites/exportapplications/action | 將 VMware 應用程式和角色資料匯出至 xls |
> | 動作 | OffAzure/VMwareSites/updateProperties/action | 更新網站中電腦的屬性 |
> | 動作 | OffAzure/VMwareSites/generateCoarseMap/action | 產生電腦清單的粗略對應 |
> | 動作 | OffAzure/VMwareSites/generateDetailedMap/action | 產生詳細的 VMware 粗略地圖 |
> | 動作 | OffAzure/VMwareSites/clientGroupMembers/action | 列出所選取用戶端群組的用戶端群組成員。 |
> | 動作 | OffAzure/VMwareSites/serverGroupMembers/action | 列出所選伺服器群組的伺服器群組成員。 |
> | 動作 | OffAzure/VMwareSites/getApplications/action | 取得所選電腦的清單應用程式資訊 |
> | 動作 | Microsoft.OffAzure/VMwareSites/read | 取得 VMware 站台的屬性 |
> | 動作 | Microsoft.OffAzure/VMwareSites/write | 建立或更新 VMware 站台 |
> | 動作 | Microsoft.OffAzure/VMwareSites/delete | 刪除 VMware 站台 |
> | 動作 | Microsoft.OffAzure/VMwareSites/refresh/action | 重新整理 VMware 站台內的物件 |
> | 動作 | OffAzure/VMwareSites/exportapplications/action | 將 VMware 應用程式和角色資料匯出至 xls |
> | 動作 | OffAzure/VMwareSites/updateProperties/action | 更新網站中電腦的屬性 |
> | 動作 | OffAzure/VMwareSites/generateCoarseMap/action | 產生電腦清單的粗略對應 |
> | 動作 | OffAzure/VMwareSites/generateDetailedMap/action | 產生詳細的 VMware 粗略地圖 |
> | 動作 | OffAzure/VMwareSites/clientGroupMembers/action | 列出所選取用戶端群組的用戶端群組成員。 |
> | 動作 | OffAzure/VMwareSites/serverGroupMembers/action | 列出所選伺服器群組的伺服器群組成員。 |
> | 動作 | OffAzure/VMwareSites/getApplications/action | 取得所選電腦的清單應用程式資訊 |
> | 動作 | Microsoft.OffAzure/VMwareSites/read | 取得 VMware 站台的屬性 |
> | 動作 | Microsoft.OffAzure/VMwareSites/write | 建立或更新 VMware 站台 |
> | 動作 | Microsoft.OffAzure/VMwareSites/delete | 刪除 VMware 站台 |
> | 動作 | Microsoft.OffAzure/VMwareSites/refresh/action | 重新整理 VMware 站台內的物件 |
> | 動作 | OffAzure/VMwareSites/exportapplications/action | 將 VMware 應用程式和角色資料匯出至 xls |
> | 動作 | OffAzure/VMwareSites/updateProperties/action | 更新網站中電腦的屬性 |
> | 動作 | OffAzure/VMwareSites/generateCoarseMap/action | 產生電腦清單的粗略對應 |
> | 動作 | OffAzure/VMwareSites/generateDetailedMap/action | 產生詳細的 VMware 粗略地圖 |
> | 動作 | OffAzure/VMwareSites/clientGroupMembers/action | 列出所選取用戶端群組的用戶端群組成員。 |
> | 動作 | OffAzure/VMwareSites/serverGroupMembers/action | 列出所選伺服器群組的伺服器群組成員。 |
> | 動作 | OffAzure/VMwareSites/getApplications/action | 取得所選電腦的清單應用程式資訊 |
> | 動作 | Microsoft.OffAzure/VMwareSites/read | 取得 VMware 站台的屬性 |
> | 動作 | Microsoft.OffAzure/VMwareSites/write | 建立或更新 VMware 站台 |
> | 動作 | Microsoft.OffAzure/VMwareSites/delete | 刪除 VMware 站台 |
> | 動作 | Microsoft.OffAzure/VMwareSites/refresh/action | 重新整理 VMware 站台內的物件 |
> | 動作 | OffAzure/VMwareSites/exportapplications/action | 將 VMware 應用程式和角色資料匯出至 xls |
> | 動作 | OffAzure/VMwareSites/updateProperties/action | 更新網站中電腦的屬性 |
> | 動作 | OffAzure/VMwareSites/generateCoarseMap/action | 產生電腦清單的粗略對應 |
> | 動作 | OffAzure/VMwareSites/generateDetailedMap/action | 產生詳細的 VMware 粗略地圖 |
> | 動作 | OffAzure/VMwareSites/clientGroupMembers/action | 列出所選取用戶端群組的用戶端群組成員。 |
> | 動作 | OffAzure/VMwareSites/serverGroupMembers/action | 列出所選伺服器群組的伺服器群組成員。 |
> | 動作 | OffAzure/VMwareSites/getApplications/action | 取得所選電腦的清單應用程式資訊 |
> |  | **VMwareSites/healthsummary** |  |
> | 動作 | OffAzure/VMwareSites/healthsummary/read | 取得 VMware 資源的健全狀況摘要 |
> | 動作 | OffAzure/VMwareSites/healthsummary/read | 取得 VMware 資源的健全狀況摘要 |
> | 動作 | OffAzure/VMwareSites/healthsummary/read | 取得 VMware 資源的健全狀況摘要 |
> | 動作 | OffAzure/VMwareSites/healthsummary/read | 取得 VMware 資源的健全狀況摘要 |
> |  | **VMwareSites/作業** |  |
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
> | 動作 | OffAzure/VMwareSites/機器/應用程式/讀取 | 取得 VMware 機器應用程式的屬性 |
> | 動作 | OffAzure/VMwareSites/機器/應用程式/讀取 | 取得 VMware 機器應用程式的屬性 |
> | 動作 | OffAzure/VMwareSites/機器/應用程式/讀取 | 取得 VMware 機器應用程式的屬性 |
> | 動作 | OffAzure/VMwareSites/機器/應用程式/讀取 | 取得 VMware 機器應用程式的屬性 |
> |  | **VMwareSites/operationsstatus** |  |
> | 動作 | Microsoft.OffAzure/VMwareSites/operationsstatus/read | 取得 VMware 作業狀態的屬性 |
> | 動作 | Microsoft.OffAzure/VMwareSites/operationsstatus/read | 取得 VMware 作業狀態的屬性 |
> | 動作 | Microsoft.OffAzure/VMwareSites/operationsstatus/read | 取得 VMware 作業狀態的屬性 |
> | 動作 | Microsoft.OffAzure/VMwareSites/operationsstatus/read | 取得 VMware 作業狀態的屬性 |
> |  | **VMwareSites/執行身分帳戶** |  |
> | 動作 | Microsoft.OffAzure/VMwareSites/runasaccounts/read | 取得 VMware 執行身分帳戶的屬性 |
> | 動作 | Microsoft.OffAzure/VMwareSites/runasaccounts/read | 取得 VMware 執行身分帳戶的屬性 |
> | 動作 | Microsoft.OffAzure/VMwareSites/runasaccounts/read | 取得 VMware 執行身分帳戶的屬性 |
> | 動作 | Microsoft.OffAzure/VMwareSites/runasaccounts/read | 取得 VMware 執行身分帳戶的屬性 |
> |  | **VMwareSites/摘要** |  |
> | 動作 | OffAzure/VMwareSites/summary/read | 取得 VMware 網站的摘要 |
> | 動作 | OffAzure/VMwareSites/summary/read | 取得 VMware 網站的摘要 |
> | 動作 | OffAzure/VMwareSites/summary/read | 取得 VMware 網站的摘要 |
> | 動作 | OffAzure/VMwareSites/summary/read | 取得 VMware 網站的摘要 |
> |  | **VMwareSites/使用量** |  |
> | 動作 | Microsoft.OffAzure/VMwareSites/usage/read | 取得 VMware 站台的使用情形 |
> | 動作 | Microsoft.OffAzure/VMwareSites/usage/read | 取得 VMware 站台的使用情形 |
> | 動作 | Microsoft.OffAzure/VMwareSites/usage/read | 取得 VMware 站台的使用情形 |
> | 動作 | Microsoft.OffAzure/VMwareSites/usage/read | 取得 VMware 站台的使用情形 |
> |  | **VMwareSites/vcenters** |  |
> | 動作 | Microsoft.OffAzure/VMwareSites/vcenters/read | 取得 VMware vCenter 的屬性 |
> | 動作 | Microsoft.OffAzure/VMwareSites/vcenters/write | 建立或更新 VMware vCenter |
> | 動作 | Microsoft.OffAzure/VMwareSites/vcenters/read | 取得 VMware vCenter 的屬性 |
> | 動作 | Microsoft.OffAzure/VMwareSites/vcenters/write | 建立或更新 VMware vCenter |
> | 動作 | Microsoft.OffAzure/VMwareSites/vcenters/read | 取得 VMware vCenter 的屬性 |
> | 動作 | Microsoft.OffAzure/VMwareSites/vcenters/write | 建立或更新 VMware vCenter |
> | 動作 | Microsoft.OffAzure/VMwareSites/vcenters/read | 取得 VMware vCenter 的屬性 |
> | 動作 | Microsoft.OffAzure/VMwareSites/vcenters/write | 建立或更新 VMware vCenter |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

Azure 服務： [Azure 監視器](../azure-monitor/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.OperationalInsights/register/action | 向資源提供者註冊訂用帳戶。 |
> | 動作 | microsoft.operationalinsights/register/action | Rergisters 訂用帳戶。 |
> | 動作 | microsoft.operationalinsights/取消註冊/動作 | 取消註冊訂用帳戶。 |
> |  | **availableservicetiers** |  |
> | 動作 | microsoft.operationalinsights/availableservicetiers/read | 取得可用的服務層級。 |
> |  | **linkTargets** |  |
> | 動作 | Microsoft.OperationalInsights/linkTargets/read | 列出未與 Azure 訂用帳戶相關聯的現有帳戶。 若要將此 Azure 訂用帳戶連結到工作區，請在「建立工作區」作業的客戶識別碼屬性中，使用這項作業所傳回的客戶識別碼。 |
> |  | **位置/operationStatuses** |  |
> | 動作 | microsoft.operationalinsights/位置/operationStatuses/讀取 | 取得 Log Analytics Azure 非同步作業狀態。 |
> |  | **業務** |  |
> | 動作 | microsoft.operationalinsights/operations/read | 列出所有可用的 OperationalInsights Rest API 作業。 |
> |  | **環境** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/write | 建立新的工作區，或藉由提供來自現有工作區的客戶識別碼來連結至現有工作區。 |
> | 動作 | Microsoft.OperationalInsights/workspaces/read | 取得現有工作區 |
> | 動作 | Microsoft.OperationalInsights/workspaces/delete | 刪除工作區。 如果工作區在建立時連結到現有工作區，則系統不會刪除其所連結的工作區。 |
> | 動作 | Microsoft.OperationalInsights/workspaces/generateregistrationcertificate/action | 為工作區產生註冊憑證。 此憑證可用來將 Microsoft System Center Operation Manager 連線到工作區。 |
> | 動作 | Microsoft.OperationalInsights/workspaces/sharedKeys/action | 擷取工作區的共用金鑰。 這些金鑰可用來將 Microsoft Operational Insights 代理程式連線到工作區。 |
> | 動作 | Microsoft.OperationalInsights/workspaces/listKeys/action | 擷取工作區的清單金鑰。 這些金鑰可用來將 Microsoft Operational Insights 代理程式連線到工作區。 |
> | 動作 | Microsoft.OperationalInsights/workspaces/search/action | 執行搜尋查詢 |
> | 動作 | Microsoft.OperationalInsights/workspaces/purge/action | 從工作區刪除指定的資料 |
> | 動作 | Microsoft.OperationalInsights/workspaces/regeneratesharedkey/action | 重新產生指定的工作區共用金鑰 |
> |  | **工作區/分析** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/analytics/query/action | 使用新的引擎進行搜尋。 |
> |  | **工作區/分析/查詢/架構** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/analytics/query/schema/read | 取得搜尋結構描述 V2。 |
> |  | **工作區/api** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/api/query/action | 使用新的引擎進行搜尋。 |
> |  | **工作區/api/查詢/架構** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/api/query/schema/read | 取得搜尋結構描述 V2。 |
> |  | **工作區/configurationScopes** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/configurationScopes/read | 取得組態範圍 |
> | 動作 | Microsoft.OperationalInsights/workspaces/configurationScopes/write | 設定組態範圍 |
> | 動作 | Microsoft.OperationalInsights/workspaces/configurationScopes/delete | 刪除組態範圍 |
> |  | **工作區/dataexport** |  |
> | 動作 | microsoft.operationalinsights/workspace/dataexport/read | 取得特定的資料匯出。 |
> | 動作 | microsoft.operationalinsights/workspace/dataexport/write | 建立或更新資料匯出。 |
> | 動作 | microsoft.operationalinsights/workspace/dataexport/delete | 刪除特定的資料匯出。 |
> |  | **工作區/資料來源** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/datasources/read | 取得工作區下的資料來源。 |
> | 動作 | Microsoft.OperationalInsights/workspaces/datasources/write | 在工作區下建立/更新資料來源。 |
> | 動作 | Microsoft.OperationalInsights/workspaces/datasources/delete | 刪除工作區下的資料來源。 |
> |  | **工作區/閘道** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/gateways/delete | 移除針對工作區設定的閘道。 |
> |  | **工作區/microsoft.intelligencepacks.applicationdependencymonitor** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/intelligencepacks/read | 列出給定工作區可見的所有智慧套件，並且也會列出該工作區會啟用還是停用套件。 |
> | 動作 | Microsoft.OperationalInsights/workspaces/intelligencepacks/enable/action | 為給定工作區啟用智慧套件。 |
> | 動作 | Microsoft.OperationalInsights/workspaces/intelligencepacks/disable/action | 為給定工作區停用智慧套件。 |
> |  | **工作區/Linkedservices.json 和 datasets.json** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/linkedServices/read | 取得指定工作區下已連結的服務。 |
> | 動作 | Microsoft.OperationalInsights/workspaces/linkedServices/write | 建立/更新指定工作區下已連結的服務。 |
> | 動作 | Microsoft.OperationalInsights/workspaces/linkedServices/delete | 刪除指定工作區下已連結的服務。 |
> |  | **工作區/listKeys** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/listKeys/read | 擷取工作區的清單金鑰。 這些金鑰可用來將 Microsoft Operational Insights 代理程式連線到工作區。 |
> |  | **工作區/managementGroups** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/managementGroups/read | 取得與此工作區連線之 System Center Operations Manager 管理群組的名稱和中繼資料。 |
> |  | **工作區/Metricdefinitions.listasync** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/metricDefinitions/read | 取得工作區下的計量定義 |
> |  | **工作區/notificationSettings** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/notificationSettings/read | 針對工作區取得使用者的通知設定。 |
> | 動作 | Microsoft.OperationalInsights/workspaces/notificationSettings/write | 針對工作區設定使用者的通知設定。 |
> | 動作 | Microsoft.OperationalInsights/workspaces/notificationSettings/delete | 針對工作區刪除使用者的通知設定。 |
> |  | **工作區/作業** |  |
> | 動作 | microsoft.operationalinsights/工作區/作業/讀取 | 取得 Microsoft.operationalinsights 工作區作業的狀態。 |
> |  | **工作區/privateEndpointConnectionProxies** |  |
> | 動作 | microsoft.operationalinsights/workspace/privateEndpointConnectionProxies/read | 取得私用端點連接 Proxy。 |
> | 動作 | microsoft.operationalinsights/workspace/privateEndpointConnectionProxies/write | 放置私人端點連接 Proxy。 |
> | 動作 | microsoft.operationalinsights/workspace/privateEndpointConnectionProxies/delete | 刪除私人端點連接 Proxy。 |
> | 動作 | microsoft.operationalinsights/workspace/privateEndpointConnectionProxies/validate/action | 驗證私用端點連接 Proxy。 |
> |  | **工作區/privateEndpointConnections** |  |
> | 動作 | microsoft.operationalinsights/workspace/privateEndpointConnections/read | 取得私用端點連接 Proxy。 |
> | 動作 | microsoft.operationalinsights/workspace/privateEndpointConnections/write | 放置私人端點連接 Proxy。 |
> | 動作 | microsoft.operationalinsights/workspace/privateEndpointConnections/delete | 刪除私人端點連接 Proxy。 |
> |  | **工作區/privateLinkResources** |  |
> | 動作 | microsoft.operationalinsights/workspace/privateLinkResources/read | 取得 Log Analytics 私人連結資源。 |
> |  | **工作區/查詢** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/read | 針對工作區中的資料執行查詢 |
> |  | **工作區/查詢/AADDomainServicesAccountLogon** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/AADDomainServicesAccountLogon/讀取 | 讀取來自 AADDomainServicesAccountLogon 資料表的資料 |
> |  | **工作區/查詢/AADDomainServicesAccountManagement** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/AADDomainServicesAccountManagement/讀取 | 讀取來自 AADDomainServicesAccountManagement 資料表的資料 |
> |  | **工作區/查詢/AADDomainServicesDirectoryServiceAccess** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/AADDomainServicesDirectoryServiceAccess/讀取 | 讀取來自 AADDomainServicesDirectoryServiceAccess 資料表的資料 |
> |  | **工作區/查詢/AADDomainServicesLogonLogoff** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/AADDomainServicesLogonLogoff/讀取 | 讀取來自 AADDomainServicesLogonLogoff 資料表的資料 |
> |  | **工作區/查詢/AADDomainServicesPolicyChange** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/AADDomainServicesPolicyChange/讀取 | 讀取來自 AADDomainServicesPolicyChange 資料表的資料 |
> |  | **工作區/查詢/AADDomainServicesPrivilegeUse** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/AADDomainServicesPrivilegeUse/讀取 | 讀取來自 AADDomainServicesPrivilegeUse 資料表的資料 |
> |  | **工作區/查詢/AADDomainServicesSystemSecurity** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/AADDomainServicesSystemSecurity/讀取 | 讀取來自 AADDomainServicesSystemSecurity 資料表的資料 |
> |  | **工作區/查詢/ADAssessmentRecommendation** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/ADAssessmentRecommendation/read | 從 ADAssessmentRecommendation 資料表讀取資料 |
> |  | **工作區/查詢/AddonAzureBackupAlerts** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/AddonAzureBackupAlerts/讀取 | 讀取來自 AddonAzureBackupAlerts 資料表的資料 |
> |  | **工作區/查詢/AddonAzureBackupJobs** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/AddonAzureBackupJobs/讀取 | 讀取來自 AddonAzureBackupJobs 資料表的資料 |
> |  | **工作區/查詢/AddonAzureBackupPolicy** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/AddonAzureBackupPolicy/讀取 | 讀取來自 AddonAzureBackupPolicy 資料表的資料 |
> |  | **工作區/查詢/AddonAzureBackupProtectedInstance** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/AddonAzureBackupProtectedInstance/讀取 | 讀取來自 AddonAzureBackupProtectedInstance 資料表的資料 |
> |  | **工作區/查詢/AddonAzureBackupStorage** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/AddonAzureBackupStorage/讀取 | 讀取來自 AddonAzureBackupStorage 資料表的資料 |
> |  | **工作區/查詢/ADFActivityRun** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/ADFActivityRun/讀取 | 讀取來自 ADFActivityRun 資料表的資料 |
> |  | **工作區/查詢/ADFPipelineRun** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/ADFPipelineRun/讀取 | 讀取來自 ADFPipelineRun 資料表的資料 |
> |  | **工作區/查詢/ADFSSISIntegrationRuntimeLogs** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/ADFSSISIntegrationRuntimeLogs/讀取 | 讀取來自 ADFSSISIntegrationRuntimeLogs 資料表的資料 |
> |  | **工作區/查詢/ADFSSISPackageEventMessageCoNtext** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/ADFSSISPackageEventMessageCoNtext/讀取 | 讀取來自 ADFSSISPackageEventMessageCoNtext 資料表的資料 |
> |  | **工作區/查詢/ADFSSISPackageEventMessages** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/ADFSSISPackageEventMessages/讀取 | 讀取來自 ADFSSISPackageEventMessages 資料表的資料 |
> |  | **工作區/查詢/ADFSSISPackageExecutableStatistics** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/ADFSSISPackageExecutableStatistics/讀取 | 讀取來自 ADFSSISPackageExecutableStatistics 資料表的資料 |
> |  | **工作區/查詢/ADFSSISPackageExecutionComponentPhases** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/ADFSSISPackageExecutionComponentPhases/讀取 | 讀取來自 ADFSSISPackageExecutionComponentPhases 資料表的資料 |
> |  | **工作區/查詢/ADFSSISPackageExecutionDataStatistics** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/ADFSSISPackageExecutionDataStatistics/讀取 | 讀取來自 ADFSSISPackageExecutionDataStatistics 資料表的資料 |
> |  | **工作區/查詢/ADFTriggerRun** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/ADFTriggerRun/讀取 | 讀取來自 ADFTriggerRun 資料表的資料 |
> |  | **工作區/查詢/ADReplicationResult** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/ADReplicationResult/read | 從 ADReplicationResult 資料表讀取資料 |
> |  | **工作區/查詢/ADSecurityAssessmentRecommendation** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/ADSecurityAssessmentRecommendation/read | 從 ADSecurityAssessmentRecommendation 資料表讀取資料 |
> |  | **工作區/查詢/AegDeliveryFailureLogs** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/AegDeliveryFailureLogs/讀取 | 讀取來自 AegDeliveryFailureLogs 資料表的資料 |
> |  | **工作區/查詢/AegPublishFailureLogs** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/AegPublishFailureLogs/讀取 | 讀取來自 AegPublishFailureLogs 資料表的資料 |
> |  | **工作區/查詢/警示** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/Alert/read | 從 Alert 資料表讀取資料 |
> |  | **工作區/查詢/AlertHistory** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/AlertHistory/read | 從 AlertHistory 資料表讀取資料 |
> |  | **工作區/查詢/AmlComputeClusterEvent** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/AmlComputeClusterEvent/讀取 | 讀取來自 AmlComputeClusterEvent 資料表的資料 |
> |  | **工作區/查詢/AmlComputeClusterNodeEvent** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/AmlComputeClusterNodeEvent/讀取 | 讀取來自 AmlComputeClusterNodeEvent 資料表的資料 |
> |  | **工作區/查詢/AmlComputeJobEvent** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/AmlComputeJobEvent/讀取 | 讀取來自 AmlComputeJobEvent 資料表的資料 |
> |  | **工作區/查詢/ApiManagementGatewayLogs** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/ApiManagementGatewayLogs/讀取 | 讀取來自 ApiManagementGatewayLogs 資料表的資料 |
> |  | **工作區/查詢/AppAvailabilityResults** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/AppAvailabilityResults/讀取 | 讀取來自 AppAvailabilityResults 資料表的資料 |
> |  | **工作區/查詢/AppBrowserTimings** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/AppBrowserTimings/讀取 | 讀取來自 AppBrowserTimings 資料表的資料 |
> |  | **工作區/查詢/AppCenterError** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/AppCenterError/read | 從 AppCenterError 資料表讀取資料 |
> |  | **工作區/查詢/AppDependencies** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/AppDependencies/讀取 | 讀取來自 AppDependencies 資料表的資料 |
> |  | **工作區/查詢/AppEvents** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/AppEvents/讀取 | 讀取來自 AppEvents 資料表的資料 |
> |  | **工作區/查詢/ApplicationInsights** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/ApplicationInsights/read | 從 ApplicationInsights 資料表讀取資料 |
> |  | **工作區/查詢/AppMetrics** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/AppMetrics/讀取 | 讀取來自 AppMetrics 資料表的資料 |
> |  | **工作區/查詢/AppPageViews** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/AppPageViews/讀取 | 讀取來自 AppPageViews 資料表的資料 |
> |  | **工作區/查詢/AppPerformanceCounters** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/AppPerformanceCounters/讀取 | 讀取來自 AppPerformanceCounters 資料表的資料 |
> |  | **工作區/查詢/AppPlatformLogsforSpring** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/AppPlatformLogsforSpring/讀取 | 讀取來自 AppPlatformLogsforSpring 資料表的資料 |
> |  | **工作區/查詢/AppPlatformSystemLogs** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/AppPlatformSystemLogs/讀取 | 讀取來自 AppPlatformSystemLogs 資料表的資料 |
> |  | **工作區/查詢/AppRequests** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/AppRequests/讀取 | 讀取來自 AppRequests 資料表的資料 |
> |  | **工作區/查詢/AppServiceAppLogs** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/AppServiceAppLogs/讀取 | 讀取來自 AppServiceAppLogs 資料表的資料 |
> |  | **工作區/查詢/AppServiceAuditLogs** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/AppServiceAuditLogs/讀取 | 讀取來自 AppServiceAuditLogs 資料表的資料 |
> |  | **工作區/查詢/AppServiceConsoleLogs** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/AppServiceConsoleLogs/讀取 | 讀取來自 AppServiceConsoleLogs 資料表的資料 |
> |  | **工作區/查詢/AppServiceEnvironmentPlatformLogs** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/AppServiceEnvironmentPlatformLogs/讀取 | 讀取來自 AppServiceEnvironmentPlatformLogs 資料表的資料 |
> |  | **工作區/查詢/AppServiceFileAuditLogs** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/AppServiceFileAuditLogs/讀取 | 讀取來自 AppServiceFileAuditLogs 資料表的資料 |
> |  | **工作區/查詢/AppServiceHTTPLogs** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/AppServiceHTTPLogs/讀取 | 讀取來自 AppServiceHTTPLogs 資料表的資料 |
> |  | **工作區/查詢/AppSystemEvents** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/AppSystemEvents/讀取 | 讀取來自 AppSystemEvents 資料表的資料 |
> |  | **工作區/查詢/AppTraces** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/AppTraces/讀取 | 讀取來自 AppTraces 資料表的資料 |
> |  | **工作區/查詢/AuditLogs** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/AuditLogs/read | 從 AuditLogs 資料表讀取資料 |
> |  | **工作區/查詢/AutoscaleEvaluationsLog** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/AutoscaleEvaluationsLog/讀取 | 讀取來自 AutoscaleEvaluationsLog 資料表的資料 |
> |  | **工作區/查詢/AutoscaleScaleActionsLog** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/AutoscaleScaleActionsLog/讀取 | 讀取來自 AutoscaleScaleActionsLog 資料表的資料 |
> |  | **工作區/查詢/availabilityResults** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/availabilityResults/讀取 | 讀取來自 availabilityResults 資料表的資料 |
> |  | **工作區/查詢/AWSCloudTrail** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/AWSCloudTrail/讀取 | 讀取來自 AWSCloudTrail 資料表的資料 |
> |  | **工作區/查詢/AzureActivity** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/AzureActivity/read | 從 AzureActivity 資料表讀取資料 |
> |  | **工作區/查詢/AzureAssessmentRecommendation** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/AzureAssessmentRecommendation/讀取 | 讀取來自 AzureAssessmentRecommendation 資料表的資料 |
> |  | **工作區/查詢/AzureDiagnostics** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/AzureDiagnostics/讀取 | 讀取來自 AzureDiagnostics 資料表的資料 |
> |  | **工作區/查詢/AzureMetrics** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/AzureMetrics/read | 從 AzureMetrics 資料表讀取資料 |
> |  | **工作區/查詢/BaiClusterEvent** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/BaiClusterEvent/讀取 | 讀取來自 BaiClusterEvent 資料表的資料 |
> |  | **工作區/查詢/BaiClusterNodeEvent** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/BaiClusterNodeEvent/讀取 | 讀取來自 BaiClusterNodeEvent 資料表的資料 |
> |  | **工作區/查詢/BaiJobEvent** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/BaiJobEvent/讀取 | 讀取來自 BaiJobEvent 資料表的資料 |
> |  | **工作區/查詢/BlockchainApplicationLog** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/BlockchainApplicationLog/讀取 | 讀取來自 BlockchainApplicationLog 資料表的資料 |
> |  | **工作區/查詢/BlockchainProxyLog** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/BlockchainProxyLog/讀取 | 讀取來自 BlockchainProxyLog 資料表的資料 |
> |  | **工作區/查詢/BoundPort** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/BoundPort/read | 從 BoundPort 資料表讀取資料 |
> |  | **工作區/查詢/browserTimings** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/browserTimings/讀取 | 讀取來自 browserTimings 資料表的資料 |
> |  | **工作區/查詢/CommonSecurityLog** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/CommonSecurityLog/read | 從 CommonSecurityLog 資料表讀取資料 |
> |  | **工作區/查詢/ComputerGroup** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/ComputerGroup/read | 從 ComputerGroup 資料表讀取資料 |
> |  | **工作區/查詢/ConfigurationChange** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/ConfigurationChange/read | 從 ConfigurationChange 資料表讀取資料 |
> |  | **工作區/查詢/ConfigurationData** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/ConfigurationData/read | 從 ConfigurationData 資料表讀取資料 |
> |  | **工作區/查詢/ContainerImageInventory** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/ContainerImageInventory/read | 從 ContainerImageInventory 資料表讀取資料 |
> |  | **工作區/查詢/ContainerInventory** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/ContainerInventory/read | 從 ContainerInventory 資料表讀取資料 |
> |  | **工作區/查詢/ContainerLog** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/ContainerLog/read | 從 ContainerLog 資料表讀取資料 |
> |  | **工作區/查詢/ContainerNodeInventory** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/ContainerNodeInventory/讀取 | 讀取來自 ContainerNodeInventory 資料表的資料 |
> |  | **工作區/查詢/ContainerRegistryLoginEvents** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/ContainerRegistryLoginEvents/讀取 | 讀取來自 ContainerRegistryLoginEvents 資料表的資料 |
> |  | **工作區/查詢/ContainerRegistryRepositoryEvents** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/ContainerRegistryRepositoryEvents/讀取 | 讀取來自 ContainerRegistryRepositoryEvents 資料表的資料 |
> |  | **工作區/查詢/ContainerServiceLog** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/ContainerServiceLog/read | 從 ContainerServiceLog 資料表讀取資料 |
> |  | **工作區/查詢/CoreAzureBackup** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/CoreAzureBackup/讀取 | 讀取來自 CoreAzureBackup 資料表的資料 |
> |  | **工作區/查詢/customEvents** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/customEvents/讀取 | 讀取來自 customEvents 資料表的資料 |
> |  | **工作區/查詢/customMetrics** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/customMetrics/讀取 | 讀取來自 customMetrics 資料表的資料 |
> |  | **工作區/查詢/DatabricksAccounts** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/DatabricksAccounts/讀取 | 讀取來自 DatabricksAccounts 資料表的資料 |
> |  | **工作區/查詢/DatabricksClusters** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/DatabricksClusters/讀取 | 讀取來自 DatabricksClusters 資料表的資料 |
> |  | **工作區/查詢/DatabricksDBFS** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/DatabricksDBFS/讀取 | 讀取來自 DatabricksDBFS 資料表的資料 |
> |  | **工作區/查詢/DatabricksInstancePools** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/DatabricksInstancePools/讀取 | 讀取來自 DatabricksInstancePools 資料表的資料 |
> |  | **工作區/查詢/DatabricksJobs** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/DatabricksJobs/讀取 | 讀取來自 DatabricksJobs 資料表的資料 |
> |  | **工作區/查詢/DatabricksNotebook** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/DatabricksNotebook/讀取 | 讀取來自 DatabricksNotebook 資料表的資料 |
> |  | **工作區/查詢/DatabricksSecrets** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/DatabricksSecrets/讀取 | 讀取來自 DatabricksSecrets 資料表的資料 |
> |  | **工作區/查詢/DatabricksSQLPermissions** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/DatabricksSQLPermissions/讀取 | 讀取來自 DatabricksSQLPermissions 資料表的資料 |
> |  | **工作區/查詢/DatabricksSSH** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/DatabricksSSH/讀取 | 讀取來自 DatabricksSSH 資料表的資料 |
> |  | **工作區/查詢/DatabricksTables** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/DatabricksTables/讀取 | 讀取來自 DatabricksTables 資料表的資料 |
> |  | **工作區/查詢/DatabricksWorkspace** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/DatabricksWorkspace/讀取 | 讀取來自 DatabricksWorkspace 資料表的資料 |
> |  | **工作區/查詢/相依性** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/相依性/讀取 | 從相依性資料表讀取資料 |
> |  | **工作區/查詢/DeviceAppCrash** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/DeviceAppCrash/read | 從 DeviceAppCrash 資料表讀取資料 |
> |  | **工作區/查詢/DeviceAppLaunch** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/DeviceAppLaunch/read | 從 DeviceAppLaunch 資料表讀取資料 |
> |  | **工作區/查詢/DeviceCalendar** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/DeviceCalendar/read | 從 DeviceCalendar 資料表讀取資料 |
> |  | **工作區/查詢/DeviceCleanup** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/DeviceCleanup/read | 從 DeviceCleanup 資料表讀取資料 |
> |  | **工作區/查詢/DeviceConnectSession** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/DeviceConnectSession/read | 從 DeviceConnectSession 資料表讀取資料 |
> |  | **工作區/查詢/DeviceEtw** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/DeviceEtw/read | 從 DeviceEtw 資料表讀取資料 |
> |  | **工作區/查詢/DeviceHardwareHealth** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/DeviceHardwareHealth/read | 從 DeviceHardwareHealth 資料表讀取資料 |
> |  | **工作區/查詢/DeviceHealth** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/DeviceHealth/read | 從 DeviceHealth 資料表讀取資料 |
> |  | **工作區/查詢/DeviceHeartbeat** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/DeviceHeartbeat/read | 從 DeviceHeartbeat 資料表讀取資料 |
> |  | **工作區/查詢/DeviceSkypeHeartbeat** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/DeviceSkypeHeartbeat/read | 從 DeviceSkypeHeartbeat 資料表讀取資料 |
> |  | **工作區/查詢/DeviceSkypeSignIn** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/DeviceSkypeSignIn/read | 從 DeviceSkypeSignIn 資料表讀取資料 |
> |  | **工作區/查詢/DeviceSleepState** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/DeviceSleepState/read | 從 DeviceSleepState 資料表讀取資料 |
> |  | **工作區/查詢/DHAppFailure** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/DHAppFailure/read | 從 DHAppFailure 資料表讀取資料 |
> |  | **工作區/查詢/DHAppReliability** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/DHAppReliability/read | 從 DHAppReliability 資料表讀取資料 |
> |  | **工作區/查詢/DHCPActivity** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/DHCPActivity/讀取 | 讀取來自 DHCPActivity 資料表的資料 |
> |  | **工作區/查詢/DHDriverReliability** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/DHDriverReliability/read | 從 DHDriverReliability 資料表讀取資料 |
> |  | **工作區/查詢/DHLogonFailures** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/DHLogonFailures/read | 從 DHLogonFailures 資料表讀取資料 |
> |  | **工作區/查詢/DHLogonMetrics** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/DHLogonMetrics/read | 從 DHLogonMetrics 資料表讀取資料 |
> |  | **工作區/查詢/DHOSCrashData** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/DHOSCrashData/read | 從 DHOSCrashData 資料表讀取資料 |
> |  | **工作區/查詢/DHOSReliability** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/DHOSReliability/read | 從 DHOSReliability 資料表讀取資料 |
> |  | **工作區/查詢/DHWipAppLearning** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/DHWipAppLearning/read | 從 DHWipAppLearning 資料表讀取資料 |
> |  | **工作區/查詢/DnsEvents** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/DnsEvents/read | 從 DnsEvents 資料表讀取資料 |
> |  | **工作區/查詢/DnsInventory** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/DnsInventory/read | 從 DnsInventory 資料表讀取資料 |
> |  | **工作區/查詢/ETWEvent** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/ETWEvent/read | 從 ETWEvent 資料表讀取資料 |
> |  | **工作區/查詢/事件** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/Event/read | 從 Event 資料表讀取資料 |
> |  | **工作區/查詢/ExchangeAssessmentRecommendation** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/ExchangeAssessmentRecommendation/read | 從 ExchangeAssessmentRecommendation 資料表讀取資料 |
> |  | **工作區/查詢/ExchangeOnlineAssessmentRecommendation** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/ExchangeOnlineAssessmentRecommendation/read | 從 ExchangeOnlineAssessmentRecommendation 資料表讀取資料 |
> |  | **工作區/查詢/FailedIngestion** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/FailedIngestion/讀取 | 讀取來自 FailedIngestion 資料表的資料 |
> |  | **工作區/查詢/FunctionAppLogs** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/FunctionAppLogs/讀取 | 讀取來自 FunctionAppLogs 資料表的資料 |
> |  | **工作區/查詢/心跳** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/Heartbeat/read | 從 Heartbeat 資料表讀取資料 |
> |  | **工作區/查詢/HuntingBookmark** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/HuntingBookmark/讀取 | 讀取來自 HuntingBookmark 資料表的資料 |
> |  | **工作區/查詢/IISAssessmentRecommendation** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/IISAssessmentRecommendation/read | 從 IISAssessmentRecommendation 資料表讀取資料 |
> |  | **工作區/查詢/InboundConnection** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/InboundConnection/read | 從 InboundConnection 資料表讀取資料 |
> |  | **工作區/查詢/InsightsMetrics** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/InsightsMetrics/讀取 | 讀取來自 InsightsMetrics 資料表的資料 |
> |  | **工作區/查詢/IntuneAuditLogs** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/IntuneAuditLogs/read | 從 IntuneAuditLogs 資料表讀取資料 |
> |  | **工作區/查詢/IntuneDeviceComplianceOrg** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/IntuneDeviceComplianceOrg/讀取 | 讀取來自 IntuneDeviceComplianceOrg 資料表的資料 |
> |  | **工作區/查詢/IntuneOperationalLogs** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/IntuneOperationalLogs/read | 從 IntuneOperationalLogs 資料表讀取資料 |
> |  | **工作區/查詢/IoTHubDistributedTracing** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/IoTHubDistributedTracing/讀取 | 讀取來自 IoTHubDistributedTracing 資料表的資料 |
> |  | **工作區/查詢/KubeEvents** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/KubeEvents/read | 從 KubeEvents 資料表讀取資料 |
> |  | **工作區/查詢/KubeHealth** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/KubeHealth/讀取 | 讀取來自 KubeHealth 資料表的資料 |
> |  | **工作區/查詢/KubeMonAgentEvents** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/KubeMonAgentEvents/讀取 | 讀取來自 KubeMonAgentEvents 資料表的資料 |
> |  | **工作區/查詢/KubeNodeInventory** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/KubeNodeInventory/read | 從 KubeNodeInventory 資料表讀取資料 |
> |  | **工作區/查詢/KubePodInventory** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/KubePodInventory/read | 從 KubePodInventory 資料表讀取資料 |
> |  | **工作區/查詢/KubeServices** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/KubeServices/read | 從 KubeServices 資料表讀取資料 |
> |  | **工作區/查詢/LinuxAuditLog** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/LinuxAuditLog/read | 從 LinuxAuditLog 資料表讀取資料 |
> |  | **工作區/查詢/MAApplication** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MAApplication/read | 從 MAApplication 資料表讀取資料 |
> |  | **工作區/查詢/MAApplicationHealth** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealth/read | 從 MAApplicationHealth 資料表讀取資料 |
> |  | **工作區/查詢/MAApplicationHealthAlternativeVersions** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealthAlternativeVersions/read | 從 MAApplicationHealthAlternativeVersions 資料表讀取資料 |
> |  | **工作區/查詢/MAApplicationHealthIssues** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealthIssues/read | 從 MAApplicationHealthIssues 資料表讀取資料 |
> |  | **工作區/查詢/MAApplicationInstance** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MAApplicationInstance/read | 從 MAApplicationInstance 資料表讀取資料 |
> |  | **工作區/查詢/MAApplicationInstanceReadiness** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MAApplicationInstanceReadiness/read | 從 MAApplicationInstanceReadiness 資料表讀取資料 |
> |  | **工作區/查詢/MAApplicationReadiness** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MAApplicationReadiness/read | 從 MAApplicationReadiness 資料表讀取資料 |
> |  | **工作區/查詢/MADeploymentPlan** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MADeploymentPlan/read | 從 MADeploymentPlan 資料表讀取資料 |
> |  | **工作區/查詢/MADevice** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MADevice/read | 從 MADevice 資料表讀取資料 |
> |  | **工作區/查詢/MADeviceNotEnrolled** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MADeviceNotEnrolled/read | 從 MADeviceNotEnrolled 資料表讀取資料 |
> |  | **工作區/查詢/MADeviceNRT** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/MADeviceNRT/讀取 | 讀取來自 MADeviceNRT 資料表的資料 |
> |  | **工作區/查詢/MADevicePnPHealth** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealth/read | 從 MADevicePnPHealth 資料表讀取資料 |
> |  | **工作區/查詢/MADevicePnPHealthAlternativeVersions** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealthAlternativeVersions/read | 從 MADevicePnPHealthAlternativeVersions 資料表讀取資料 |
> |  | **工作區/查詢/MADevicePnPHealthIssues** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealthIssues/read | 從 MADevicePnPHealthIssues 資料表讀取資料 |
> |  | **工作區/查詢/MADeviceReadiness** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MADeviceReadiness/read | 從 MADeviceReadiness 資料表讀取資料 |
> |  | **工作區/查詢/MADriverInstanceReadiness** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MADriverInstanceReadiness/read | 從 MADriverInstanceReadiness 資料表讀取資料 |
> |  | **工作區/查詢/MADriverReadiness** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MADriverReadiness/read | 從 MADriverReadiness 資料表讀取資料 |
> |  | **工作區/查詢/MAOfficeAddin** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddin/read | 從 MAOfficeAddin 資料表讀取資料 |
> |  | **工作區/查詢/MAOfficeAddinEntityHealth** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/MAOfficeAddinEntityHealth/讀取 | 讀取來自 MAOfficeAddinEntityHealth 資料表的資料 |
> |  | **工作區/查詢/MAOfficeAddinHealth** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinHealth/read | 從 MAOfficeAddinHealth 資料表讀取資料 |
> |  | **工作區/查詢/MAOfficeAddinHealthEventNRT** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/MAOfficeAddinHealthEventNRT/讀取 | 讀取來自 MAOfficeAddinHealthEventNRT 資料表的資料 |
> |  | **工作區/查詢/MAOfficeAddinHealthIssues** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinHealthIssues/read | 從 MAOfficeAddinHealthIssues 資料表讀取資料 |
> |  | **工作區/查詢/MAOfficeAddinInstance** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinInstance/read | 從 MAOfficeAddinInstance 資料表讀取資料 |
> |  | **工作區/查詢/MAOfficeAddinInstanceReadiness** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinInstanceReadiness/read | 從 MAOfficeAddinInstanceReadiness 資料表讀取資料 |
> |  | **工作區/查詢/MAOfficeAddinReadiness** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinReadiness/read | 從 MAOfficeAddinReadiness 資料表讀取資料 |
> |  | **工作區/查詢/MAOfficeApp** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MAOfficeApp/read | 從 MAOfficeApp 資料表讀取資料 |
> |  | **工作區/查詢/MAOfficeAppCrashesNRT** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/MAOfficeAppCrashesNRT/讀取 | 讀取來自 MAOfficeAppCrashesNRT 資料表的資料 |
> |  | **工作區/查詢/MAOfficeAppHealth** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppHealth/read | 從 MAOfficeAppHealth 資料表讀取資料 |
> |  | **工作區/查詢/MAOfficeAppInstance** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppInstance/read | 從 MAOfficeAppInstance 資料表讀取資料 |
> |  | **工作區/查詢/MAOfficeAppInstanceHealth** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/MAOfficeAppInstanceHealth/讀取 | 讀取來自 MAOfficeAppInstanceHealth 資料表的資料 |
> |  | **工作區/查詢/MAOfficeAppReadiness** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppReadiness/read | 從 MAOfficeAppReadiness 資料表讀取資料 |
> |  | **工作區/查詢/MAOfficeAppSessionsNRT** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/MAOfficeAppSessionsNRT/讀取 | 讀取來自 MAOfficeAppSessionsNRT 資料表的資料 |
> |  | **工作區/查詢/MAOfficeBuildInfo** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MAOfficeBuildInfo/read | 從 MAOfficeBuildInfo 資料表讀取資料 |
> |  | **工作區/查詢/MAOfficeCurrencyAssessment** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MAOfficeCurrencyAssessment/read | 從 MAOfficeCurrencyAssessment 資料表讀取資料 |
> |  | **工作區/查詢/MAOfficeCurrencyAssessmentDailyCounts** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MAOfficeCurrencyAssessmentDailyCounts/read | 從 MAOfficeCurrencyAssessmentDailyCounts 資料表讀取資料 |
> |  | **工作區/查詢/MAOfficeDeploymentStatus** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MAOfficeDeploymentStatus/read | 從 MAOfficeDeploymentStatus 資料表讀取資料 |
> |  | **工作區/查詢/MAOfficeDeploymentStatusNRT** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/MAOfficeDeploymentStatusNRT/讀取 | 讀取來自 MAOfficeDeploymentStatusNRT 資料表的資料 |
> |  | **工作區/查詢/MAOfficeMacroErrorNRT** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/MAOfficeMacroErrorNRT/讀取 | 讀取來自 MAOfficeMacroErrorNRT 資料表的資料 |
> |  | **工作區/查詢/MAOfficeMacroGlobalHealth** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/MAOfficeMacroGlobalHealth/讀取 | 讀取來自 MAOfficeMacroGlobalHealth 資料表的資料 |
> |  | **工作區/查詢/MAOfficeMacroHealth** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroHealth/read | 從 MAOfficeMacroHealth 資料表讀取資料 |
> |  | **工作區/查詢/MAOfficeMacroHealthIssues** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroHealthIssues/read | 從 MAOfficeMacroHealthIssues 資料表讀取資料 |
> |  | **工作區/查詢/MAOfficeMacroIssueInstanceReadiness** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroIssueInstanceReadiness/read | 從 MAOfficeMacroIssueInstanceReadiness 資料表讀取資料 |
> |  | **工作區/查詢/MAOfficeMacroIssueReadiness** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroIssueReadiness/read | 從 MAOfficeMacroIssueReadiness 資料表讀取資料 |
> |  | **工作區/查詢/MAOfficeMacroSummary** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroSummary/read | 從 MAOfficeMacroSummary 資料表讀取資料 |
> |  | **工作區/查詢/MAOfficeSuite** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MAOfficeSuite/read | 從 MAOfficeSuite 資料表讀取資料 |
> |  | **工作區/查詢/MAOfficeSuiteInstance** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MAOfficeSuiteInstance/read | 從 MAOfficeSuiteInstance 資料表讀取資料 |
> |  | **工作區/查詢/MAProposedPilotDevices** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MAProposedPilotDevices/read | 從 MAProposedPilotDevices 資料表讀取資料 |
> |  | **工作區/查詢/MAWindowsBuildInfo** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MAWindowsBuildInfo/read | 從 MAWindowsBuildInfo 資料表讀取資料 |
> |  | **工作區/查詢/MAWindowsCurrencyAssessment** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MAWindowsCurrencyAssessment/read | 從 MAWindowsCurrencyAssessment 資料表讀取資料 |
> |  | **工作區/查詢/MAWindowsCurrencyAssessmentDailyCounts** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MAWindowsCurrencyAssessmentDailyCounts/read | 從 MAWindowsCurrencyAssessmentDailyCounts 資料表讀取資料 |
> |  | **工作區/查詢/MAWindowsDeploymentStatus** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MAWindowsDeploymentStatus/read | 從 MAWindowsDeploymentStatus 資料表讀取資料 |
> |  | **工作區/查詢/MAWindowsDeploymentStatusNRT** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/MAWindowsDeploymentStatusNRT/讀取 | 讀取來自 MAWindowsDeploymentStatusNRT 資料表的資料 |
> |  | **工作區/查詢/MAWindowsSysReqInstanceReadiness** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/MAWindowsSysReqInstanceReadiness/read | 從 MAWindowsSysReqInstanceReadiness 資料表讀取資料 |
> |  | **工作區/查詢/McasShadowItReporting** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/McasShadowItReporting/讀取 | 讀取來自 McasShadowItReporting 資料表的資料 |
> |  | **工作區/查詢/MicrosoftAzureBastionAuditLogs** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/MicrosoftAzureBastionAuditLogs/讀取 | 讀取來自 MicrosoftAzureBastionAuditLogs 資料表的資料 |
> |  | **工作區/查詢/MicrosoftDataShareReceivedSnapshotLog** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/MicrosoftDataShareReceivedSnapshotLog/讀取 | 讀取來自 MicrosoftDataShareReceivedSnapshotLog 資料表的資料 |
> |  | **工作區/查詢/MicrosoftDataShareSentSnapshotLog** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/MicrosoftDataShareSentSnapshotLog/讀取 | 讀取來自 MicrosoftDataShareSentSnapshotLog 資料表的資料 |
> |  | **工作區/查詢/MicrosoftDataShareShareLog** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/MicrosoftDataShareShareLog/讀取 | 讀取來自 MicrosoftDataShareShareLog 資料表的資料 |
> |  | **工作區/查詢/MicrosoftDynamicsTelemetryPerformanceLogs** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/MicrosoftDynamicsTelemetryPerformanceLogs/讀取 | 讀取來自 MicrosoftDynamicsTelemetryPerformanceLogs 資料表的資料 |
> |  | **工作區/查詢/MicrosoftDynamicsTelemetrySystemMetricsLogs** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/MicrosoftDynamicsTelemetrySystemMetricsLogs/讀取 | 讀取來自 MicrosoftDynamicsTelemetrySystemMetricsLogs 資料表的資料 |
> |  | **工作區/查詢/MicrosoftHealthcareApisAuditLogs** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/MicrosoftHealthcareApisAuditLogs/讀取 | 讀取來自 MicrosoftHealthcareApisAuditLogs 資料表的資料 |
> |  | **工作區/查詢/NetworkMonitoring** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/NetworkMonitoring/read | 從 NetworkMonitoring 資料表讀取資料 |
> |  | **工作區/查詢/OfficeActivity** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/OfficeActivity/read | 從 OfficeActivity 資料表讀取資料 |
> |  | **工作區/查詢/作業** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/Operation/read | 從 Operation 資料表讀取資料 |
> |  | **工作區/查詢/OutboundConnection** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/OutboundConnection/read | 從 OutboundConnection 資料表讀取資料 |
> |  | **工作區/查詢/pageViews** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/pageViews/讀取 | 讀取來自 pageViews 資料表的資料 |
> |  | **工作區/查詢/效能** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/Perf/read | 從 Perf 資料表讀取資料 |
> |  | **工作區/查詢/performanceCounters** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/performanceCounters/讀取 | 讀取來自 performanceCounters 資料表的資料 |
> |  | **工作區/查詢/ProtectionStatus** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/ProtectionStatus/read | 從 ProtectionStatus 資料表讀取資料 |
> |  | **工作區/查詢/要求** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/要求/讀取 | 從要求資料表讀取資料 |
> |  | **工作區/查詢/ReservedCommonFields** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/ReservedCommonFields/read | 從 ReservedCommonFields 資料表讀取資料 |
> |  | **工作區/查詢/SCCMAssessmentRecommendation** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/SCCMAssessmentRecommendation/read | 從 SCCMAssessmentRecommendation 資料表讀取資料 |
> |  | **工作區/查詢/SCOMAssessmentRecommendation** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/SCOMAssessmentRecommendation/read | 從 SCOMAssessmentRecommendation 資料表讀取資料 |
> |  | **工作區/查詢/SecurityAlert** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/SecurityAlert/read | 從 SecurityAlert 資料表讀取資料 |
> |  | **工作區/查詢/SecurityBaseline** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/SecurityBaseline/read | 從 SecurityBaseline 資料表讀取資料 |
> |  | **工作區/查詢/SecurityBaselineSummary** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/SecurityBaselineSummary/read | 從 SecurityBaselineSummary 資料表讀取資料 |
> |  | **工作區/查詢/SecurityDetection** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/SecurityDetection/read | 從 SecurityDetection 資料表讀取資料 |
> |  | **工作區/查詢/SecurityEvent** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/SecurityEvent/read | 從 SecurityEvent 資料表讀取資料 |
> |  | **工作區/查詢/SecurityIoTRawEvent** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/SecurityIoTRawEvent/讀取 | 讀取來自 SecurityIoTRawEvent 資料表的資料 |
> |  | **工作區/查詢/SecurityRecommendation** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/SecurityRecommendation/讀取 | 讀取來自 SecurityRecommendation 資料表的資料 |
> |  | **工作區/查詢/ServiceFabricOperationalEvent** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/ServiceFabricOperationalEvent/read | 從 ServiceFabricOperationalEvent 資料表讀取資料 |
> |  | **工作區/查詢/ServiceFabricReliableActorEvent** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/ServiceFabricReliableActorEvent/read | 從 ServiceFabricReliableActorEvent 資料表讀取資料 |
> |  | **工作區/查詢/ServiceFabricReliableServiceEvent** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/ServiceFabricReliableServiceEvent/read | 從 ServiceFabricReliableServiceEvent 資料表讀取資料 |
> |  | **工作區/查詢/SfBAssessmentRecommendation** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/SfBAssessmentRecommendation/read | 從 SfBAssessmentRecommendation 資料表讀取資料 |
> |  | **工作區/查詢/SfBOnlineAssessmentRecommendation** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/SfBOnlineAssessmentRecommendation/read | 從 SfBOnlineAssessmentRecommendation 資料表讀取資料 |
> |  | **工作區/查詢/SharePointOnlineAssessmentRecommendation** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/SharePointOnlineAssessmentRecommendation/read | 從 SharePointOnlineAssessmentRecommendation 資料表讀取資料 |
> |  | **工作區/查詢/SignalRServiceDiagnosticLogs** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/SignalRServiceDiagnosticLogs/讀取 | 讀取來自 SignalRServiceDiagnosticLogs 資料表的資料 |
> |  | **工作區/查詢/SigninLogs** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/SigninLogs/read | 從 SigninLogs 資料表讀取資料 |
> |  | **工作區/查詢/SPAssessmentRecommendation** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/SPAssessmentRecommendation/read | 從 SPAssessmentRecommendation 資料表讀取資料 |
> |  | **工作區/查詢/SQLAssessmentRecommendation** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/SQLAssessmentRecommendation/read | 從 SQLAssessmentRecommendation 資料表讀取資料 |
> |  | **工作區/查詢/SqlDataClassification** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/SqlDataClassification/讀取 | 讀取來自 SqlDataClassification 資料表的資料 |
> |  | **工作區/查詢/SQLQueryPerformance** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/SQLQueryPerformance/read | 從 SQLQueryPerformance 資料表讀取資料 |
> |  | **工作區/查詢/SqlThreatProtectionLoginAudits** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/SqlThreatProtectionLoginAudits/讀取 | 讀取來自 SqlThreatProtectionLoginAudits 資料表的資料 |
> |  | **工作區/查詢/SqlVulnerabilityAssessmentResult** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/SqlVulnerabilityAssessmentResult/讀取 | 讀取來自 SqlVulnerabilityAssessmentResult 資料表的資料 |
> |  | **工作區/查詢/StorageBlobLogs** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/StorageBlobLogs/讀取 | 讀取來自 StorageBlobLogs 資料表的資料 |
> |  | **工作區/查詢/StorageFileLogs** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/StorageFileLogs/讀取 | 讀取來自 StorageFileLogs 資料表的資料 |
> |  | **工作區/查詢/StorageQueueLogs** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/StorageQueueLogs/讀取 | 讀取來自 StorageQueueLogs 資料表的資料 |
> |  | **工作區/查詢/StorageTableLogs** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/StorageTableLogs/讀取 | 讀取來自 StorageTableLogs 資料表的資料 |
> |  | **工作區/查詢/SucceededIngestion** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/SucceededIngestion/讀取 | 讀取來自 SucceededIngestion 資料表的資料 |
> |  | **工作區/查詢/Syslog** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/Syslog/read | 從 Syslog 資料表讀取資料 |
> |  | **工作區/查詢/SysmonEvent** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/SysmonEvent/read | 從 SysmonEvent 資料表讀取資料 |
> |  | **工作區/查詢/systemEvents** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/systemEvents/讀取 | 讀取來自 systemEvents 資料表的資料 |
> |  | **工作區/查詢/資料表。自訂** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read | 從任何自訂記錄檔讀取資料 |
> |  | **工作區/查詢/ThreatIntelligenceIndicator** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/ThreatIntelligenceIndicator/讀取 | 讀取來自 ThreatIntelligenceIndicator 資料表的資料 |
> |  | **工作區/查詢/追蹤** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/追蹤/讀取 | 讀取追蹤資料表中的資料 |
> |  | **工作區/查詢/UAApp** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/UAApp/read | 從 UAApp 資料表讀取資料 |
> |  | **工作區/查詢/UAComputer** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/UAComputer/read | 從 UAComputer 資料表讀取資料 |
> |  | **工作區/查詢/UAComputerRank** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/UAComputer/read | 從 UAComputerRank 資料表讀取資料 |
> |  | **工作區/查詢/UADriver** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/UADriver/read | 從 UADriver 資料表讀取資料 |
> |  | **工作區/查詢/UADriverProblemCodes** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/UADriverProblemCodes/read | 從 UADriverProblemCodes 資料表讀取資料 |
> |  | **工作區/查詢/UAFeedback** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/UAFeedback/read | 從 UAFeedback 資料表讀取資料 |
> |  | **工作區/查詢/UAHardwareSecurity** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/UAHardwareSecurity/read | 從 UAHardwareSecurity 資料表讀取資料 |
> |  | **工作區/查詢/UAIESiteDiscovery** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/UAIESiteDiscovery/read | 從 UAIESiteDiscovery 資料表讀取資料 |
> |  | **工作區/查詢/UAOfficeAddIn** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/UAOfficeAddIn/read | 從 UAOfficeAddIn 資料表讀取資料 |
> |  | **工作區/查詢/UAProposedActionPlan** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/UAProposedActionPlan/read | 從 UAProposedActionPlan 資料表讀取資料 |
> |  | **工作區/查詢/UASysReqIssue** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/UASysReqIssue/read | 從 UASysReqIssue 資料表讀取資料 |
> |  | **工作區/查詢/UAUpgradedComputer** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/UAUpgradedComputer/read | 從 UAUpgradedComputer 資料表讀取資料 |
> |  | **工作區/查詢/更新** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/Update/read | 從 Update 資料表讀取資料 |
> |  | **工作區/查詢/UpdateRunProgress** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/UpdateRunProgress/read | 從 UpdateRunProgress 資料表讀取資料 |
> |  | **工作區/查詢/UpdateSummary** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/UpdateSummary/read | 從 UpdateSummary 資料表讀取資料 |
> |  | **工作區/查詢/使用量** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/Usage/read | 從 Usage 資料表讀取資料 |
> |  | **工作區/查詢/VMBoundPort** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/VMBoundPort/read | 從 VMBoundPort 資料表讀取資料 |
> |  | **工作區/查詢/VMComputer** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/VMComputer/讀取 | 讀取來自 VMComputer 資料表的資料 |
> |  | **工作區/查詢/VMConnection** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/VMConnection/read | 從 VMConnection 資料表讀取資料 |
> |  | **工作區/查詢/VMProcess** |  |
> | 動作 | Microsoft.operationalinsights/工作區/查詢/VMProcess/讀取 | 讀取來自 VMProcess 資料表的資料 |
> |  | **工作區/查詢/W3CIISLog** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/W3CIISLog/read | 從 W3CIISLog 資料表讀取資料 |
> |  | **工作區/查詢/WaaSDeploymentStatus** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/WaaSDeploymentStatus/read | 從 WaaSDeploymentStatus 資料表讀取資料 |
> |  | **工作區/查詢/WaaSInsiderStatus** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/WaaSInsiderStatus/read | 從 WaaSInsiderStatus 資料表讀取資料 |
> |  | **工作區/查詢/WaaSUpdateStatus** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/WaaSUpdateStatus/read | 從 WaaSUpdateStatus 資料表讀取資料 |
> |  | **工作區/查詢/WDAVStatus** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/WDAVStatus/read | 從 WDAVStatus 資料表讀取資料 |
> |  | **工作區/查詢/WDAVThreat** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/WDAVThreat/read | 從 WDAVThreat 資料表讀取資料 |
> |  | **工作區/查詢/WindowsClientAssessmentRecommendation** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/WindowsClientAssessmentRecommendation/read | 從 WindowsClientAssessmentRecommendation 資料表讀取資料 |
> |  | **工作區/查詢/WindowsEvent** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/WindowsEvent/read | 從 WindowsEvent 資料表讀取資料 |
> |  | **工作區/查詢/Windows 防火牆** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/WindowsFirewall/read | 從 WindowsFirewall 資料表讀取資料 |
> |  | **工作區/查詢/WindowsServerAssessmentRecommendation** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/WindowsServerAssessmentRecommendation/read | 從 WindowsServerAssessmentRecommendation 資料表讀取資料 |
> |  | **工作區/查詢/WireData** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/WireData/read | 從 WireData 資料表讀取資料 |
> |  | **工作區/查詢/WorkloadMonitoringPerf** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/WorkloadMonitoringPerf/read | 從 WorkloadMonitoringPerf 資料表中讀取資料 |
> |  | **工作區/查詢/WUDOAggregatedStatus** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/WUDOAggregatedStatus/read | 從 WUDOAggregatedStatus 資料表讀取資料 |
> |  | **工作區/查詢/WUDOStatus** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/query/WUDOStatus/read | 從 WUDOStatus 資料表讀取資料 |
> |  | **工作區/規則** |  |
> | 動作 | microsoft.operationalinsights/workspaces/rules/read | 取得所有警示規則。 |
> |  | **工作區/savedSearches** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/savedSearches/read | 取得已儲存的搜尋查詢 |
> | 動作 | Microsoft.OperationalInsights/workspaces/savedSearches/write | 建立已儲存的搜尋查詢 |
> | 動作 | Microsoft.OperationalInsights/workspaces/savedSearches/delete | 刪除已儲存的搜尋查詢 |
> |  | **工作區/savedsearches/結果** |  |
> | 動作 | microsoft.operationalinsights/workspaces/savedsearches/results/read | 取得已儲存的搜尋結果。 已被取代 |
> |  | **工作區/savedsearches/排程** |  |
> | 動作 | microsoft.operationalinsights/workspaces/savedsearches/schedules/read | 取得已排程的搜尋。 |
> | 動作 | microsoft.operationalinsights/workspaces/savedsearches/schedules/delete | 刪除已排程的搜尋。 |
> | 動作 | microsoft.operationalinsights/workspaces/savedsearches/schedules/write | 建立或更新已排程的搜尋。 |
> |  | **工作區/savedsearches/排程/動作** |  |
> | 動作 | microsoft.operationalinsights/workspaces/savedsearches/schedules/actions/read | 取得已排程的搜尋動作。 |
> | 動作 | microsoft.operationalinsights/workspaces/savedsearches/schedules/actions/delete | 刪除已排程的搜尋動作。 |
> | 動作 | microsoft.operationalinsights/workspaces/savedsearches/schedules/actions/write | 建立或更新已排程的搜尋動作。 |
> |  | **工作區/架構** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/schema/read | 取得工作區的搜尋結構描述。  搜尋結構描述包括公開的欄位和其類型。 |
> |  | **工作區/scopedPrivateLinkProxies** |  |
> | 動作 | microsoft.operationalinsights/workspace/scopedPrivateLinkProxies/read | 取得限定範圍的私用連結 Proxy。 |
> | 動作 | microsoft.operationalinsights/workspace/scopedPrivateLinkProxies/write | 放置範圍私人連結 Proxy。 |
> | 動作 | microsoft.operationalinsights/workspace/scopedPrivateLinkProxies/delete | 刪除已設定範圍的私用連結 Proxy。 |
> |  | **工作區/搜尋** |  |
> | 動作 | microsoft.operationalinsights/workspaces/search/read | 取得搜尋結果。 已被取代。 |
> |  | **工作區/sharedKeys** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/sharedKeys/read | 擷取工作區的共用金鑰。 這些金鑰可用來將 Microsoft Operational Insights 代理程式連線到工作區。 |
> |  | **工作區/storageinsightconfigs** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/write | 建立新的儲存體組態。 這些組態可用來提取現有儲存體帳戶中來自某個位置的資料。 |
> | 動作 | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/read | 取得儲存體組態。 |
> | 動作 | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/delete | 刪除儲存體組態。 這會讓 Microsoft Operational Insights 停止從儲存體帳戶讀取資料。 |
> |  | **工作區/upgradetranslationfailures** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/upgradetranslationfailures/read | 取得工作區的搜尋升級翻譯失敗記錄 |
> |  | **工作區/使用方式** |  |
> | 動作 | Microsoft.OperationalInsights/workspaces/usages/read | 取得工作區的使用量資料，包括工作區所讀取的資料量。 |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

Azure 服務： [Azure 監視器](../azure-monitor/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.OperationsManagement/register/action | 向資源提供者註冊訂用帳戶。 |
> |  | **managementAssociations** |  |
> | 動作 | Microsoft.OperationsManagement/managementAssociations/write | 建立新的管理關聯 |
> | 動作 | Microsoft.OperationsManagement/managementAssociations/read | 取得現有的管理關聯 |
> | 動作 | Microsoft.OperationsManagement/managementAssociations/delete | 刪除現有的管理關聯 |
> |  | **managementConfigurations** |  |
> | 動作 | Microsoft.OperationsManagement/managementConfigurations/write | 建立新的管理設定 |
> | 動作 | Microsoft.OperationsManagement/managementConfigurations/read | 取得現有的管理設定 |
> | 動作 | Microsoft.OperationsManagement/managementConfigurations/delete | 刪除現有的管理組態 |
> |  | **解決方案** |  |
> | 動作 | Microsoft.OperationsManagement/solutions/write | 建立新的 OMS 解決方案 |
> | 動作 | Microsoft.OperationsManagement/solutions/read | 取得現有的 OMS 解決方案 |
> | 動作 | Microsoft.OperationsManagement/solutions/delete | 刪除現有的 OMS 解決方案 |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

Azure 服務： [Azure 原則](../governance/policy/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.PolicyInsights/register/action | 註冊 Microsoft Policy Insights 資源提供者，並對其啟用動作。 |
> | 動作 | Microsoft.policyinsights/取消註冊/動作 | 取消註冊 Microsoft Policy Insights 資源提供者。 |
> |  | **asyncOperationResults** |  |
> | 動作 | Microsoft.PolicyInsights/asyncOperationResults/read | 取得非同步作業結果。 |
> |  | **業務** |  |
> | 動作 | Microsoft.policyinsights/作業/讀取 | 取得 Microsoft.policyinsights 命名空間上支援的作業 |
> |  | **policyEvents** |  |
> | 動作 | Microsoft.PolicyInsights/policyEvents/queryResults/action | 查詢原則事件的相關資訊。 |
> |  | **policyEvents/queryResults** |  |
> | 動作 | Microsoft.PolicyInsights/policyEvents/queryResults/read | 查詢原則事件的相關資訊。 |
> |  | **policyMetadata** |  |
> | 動作 | Microsoft.policyinsights/policyMetadata/read | 取得原則中繼資料資源。 |
> |  | **policyStates** |  |
> | 動作 | Microsoft.PolicyInsights/policyStates/queryResults/action | 查詢原則狀態的相關資訊。 |
> | 動作 | Microsoft.PolicyInsights/policyStates/summarize/action | 查詢原則最新狀態的摘要資訊。 |
> | 動作 | Microsoft.PolicyInsights/policyStates/triggerEvaluation/action | 對選取的範圍觸發新的合規性評估。 |
> |  | **policyStates/queryResults** |  |
> | 動作 | Microsoft.PolicyInsights/policyStates/queryResults/read | 查詢原則狀態的相關資訊。 |
> |  | **policyStates/摘要** |  |
> | 動作 | Microsoft.PolicyInsights/policyStates/summarize/read | 查詢原則最新狀態的摘要資訊。 |
> |  | **policyTrackedResources/queryResults** |  |
> | 動作 | Microsoft.PolicyInsights/policyTrackedResources/queryResults/read | 查詢 DeployIfNotExists 原則所需之資源的相關資訊。 |
> |  | **補救** |  |
> | 動作 | Microsoft.PolicyInsights/remediations/read | 取得原則補救。 |
> | 動作 | Microsoft.PolicyInsights/remediations/write | 建立或更新 Microsoft Policy 補救。 |
> | 動作 | Microsoft.PolicyInsights/remediations/delete | 刪除原則補救。 |
> | 動作 | Microsoft.PolicyInsights/remediations/cancel/action | 取消進行中的 Microsoft 原則補救。 |
> |  | **補救/listDeployments** |  |
> | 動作 | Microsoft.PolicyInsights/remediations/listDeployments/read | 列出原則補救所需的部署。 |
> | DataAction | Microsoft.policyinsights/checkDataPolicyCompliance/action | 根據資料原則檢查給定元件的相容性狀態。 |
> |  | **policyEvents** |  |
> | DataAction | Microsoft.policyinsights/policyEvents/logDataEvents/action | 記錄資源元件原則事件。 |

## <a name="microsoftportal"></a>Microsoft.Portal

Azure 服務： [Azure 入口網站](../azure-portal/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.Portal/register/action | 註冊入口網站 |
> |  | **板** |  |
> | 動作 | Microsoft.Portal/consoles/delete | 移除 Cloud Shell 實例。 |
> | 動作 | Microsoft.Portal/consoles/write | 建立或更新 Cloud Shell 實例。 |
> | 動作 | Microsoft 入口網站/主控台/讀取 | 讀取 Cloud Shell 實例。 |
> |  | **儀錶** |  |
> | 動作 | Microsoft.Portal/dashboards/read | 讀取訂用帳戶的儀表板。 |
> | 動作 | Microsoft.Portal/dashboards/write | 新增或修改訂用帳戶的儀表板。 |
> | 動作 | Microsoft.Portal/dashboards/delete | 從訂用帳戶移除儀表板。 |
> |  | **usersettings** |  |
> | 動作 | Microsoft.Portal/usersettings/delete | 移除 Cloud Shell 的使用者設定。 |
> | 動作 | Microsoft.Portal/usersettings/write | 建立或更新 Cloud Shell 使用者設定。 |
> | 動作 | Microsoft.Portal/usersettings/read | 讀取 Cloud Shell 的使用者設定。 |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

Azure 服務： [Power BI Embedded](https://docs.microsoft.com/azure/power-bi-embedded/)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.PowerBIDedicated/register/action | 註冊 Power BI 專用資源提供者。 |
> |  | **容量** |  |
> | 動作 | Microsoft.PowerBIDedicated/capacities/read | 擷取指定 Power BI 專用容量的資訊。 |
> | 動作 | Microsoft.PowerBIDedicated/capacities/write | 建立或更新指定的 Power BI 專用容量。 |
> | 動作 | Microsoft.PowerBIDedicated/capacities/delete | 刪除 Power BI 專用容量。 |
> | 動作 | Microsoft.PowerBIDedicated/capacities/suspend/action | 暫止容量。 |
> | 動作 | Microsoft.PowerBIDedicated/capacities/resume/action | 繼續容量。 |
> |  | **容量/sku** |  |
> | 動作 | Microsoft.PowerBIDedicated/capacities/skus/read | 擷取容量的可用 SKU 資訊 |
> |  | **堆放** |  |
> | 動作 | PowerBIDedicated/位置/checkNameAvailability/動作 | 檢查指定的 Power BI 專用容量名稱有效且未在使用中。 |
> |  | **位置/operationresults** |  |
> | 動作 | Microsoft.PowerBIDedicated/locations/operationresults/read | 擷取指定作業結果的資訊。 |
> |  | **位置/operationstatuses** |  |
> | 動作 | Microsoft.PowerBIDedicated/locations/operationstatuses/read | 擷取指定作業狀態的資訊。 |
> |  | **業務** |  |
> | 動作 | Microsoft.PowerBIDedicated/operations/read | 擷取作業的資訊 |
> |  | **sku** |  |
> | 動作 | Microsoft.PowerBIDedicated/skus/read | 擷取 SKU 的資訊 |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

Azure 服務： [Site Recovery](../site-recovery/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.RecoveryServices/register/action | 為指定的資源提供者註冊訂用帳戶 |
> |  | **位置** |  |
> | 動作 | azurerm.recoveryservices/位置/backupPreValidateProtection/動作 |  |
> | 動作 | azurerm.recoveryservices/位置/backupStatus/動作 | 檢查復原服務保存庫的備份狀態 |
> | 動作 | azurerm.recoveryservices/位置/backupValidateFeatures/動作 | 驗證功能 |
> | 動作 | Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp 是服務所使用的內部作業 |
> | 動作 | Microsoft.RecoveryServices/locations/checkNameAvailability/action | 檢查資源名稱可用性是 API，以檢查是否有可用的資源名稱 |
> |  | **位置/allocatedStamp** |  |
> | 動作 | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp 是服務所使用的內部作業 |
> |  | **位置/backupProtectedItem** |  |
> | 動作 | azurerm.recoveryservices/位置/backupProtectedItem/寫入 | 建立備用的受保護項目 |
> |  | **位置/backupProtectedItems** |  |
> | 動作 | azurerm.recoveryservices/位置/backupProtectedItems/讀取 | 傳回所有受保護項目的清單。 |
> |  | **位置/operationStatus** |  |
> | 動作 | Microsoft.RecoveryServices/locations/operationStatus/read | 取得給定作業的作業狀態 |
> |  | **業務** |  |
> | 動作 | Microsoft.RecoveryServices/operations/read | 作業會傳回資源提供者的作業清單 |
> |  | **保存庫** |  |
> | 動作 | azurerm.recoveryservices/vault/backupJobsExport/action | 匯出作業 |
> | 動作 | azurerm.recoveryservices/vault/backupSecurityPIN/action | 傳回復原服務保存庫的安全性 PIN 碼資訊。 |
> | 動作 | azurerm.recoveryservices/vault/backupValidateOperation/action | 驗證受保護項目上的作業 |
> | 動作 | Microsoft.RecoveryServices/Vaults/write | 「建立保存庫」作業會建立 'vault' 類型的 Azure 資源 |
> | 動作 | Microsoft.RecoveryServices/Vaults/read | 「取得保存庫」作業會取得物件，此物件代表 'vault' 類型的 Azure 資源 |
> | 動作 | Microsoft.RecoveryServices/Vaults/delete | 「刪除保存庫」作業會刪除 'vault' 類型的指定 Azure 資源 |
> |  | **保存庫/backupconfig** |  |
> | 動作 | azurerm.recoveryservices/vault/backupconfig/read | 傳回復原服務保存庫的組態。 |
> | 動作 | azurerm.recoveryservices/vault/backupconfig/write | 更新復原服務保存庫的組態。 |
> |  | **保存庫/backupEncryptionConfigs** |  |
> | 動作 | azurerm.recoveryservices/vault/backupEncryptionConfigs/read | 取得備份資源加密設定。 |
> | 動作 | azurerm.recoveryservices/vault/backupEncryptionConfigs/write | 更新備份資源加密設定 |
> |  | **保存庫/backupEngines** |  |
> | 動作 | azurerm.recoveryservices/vault/backupEngines/read | 傳回已向保存庫註冊的所有備份管理伺服器。 |
> |  | **保存庫/backupFabrics** |  |
> | 動作 | azurerm.recoveryservices/vault/backupFabrics/refreshContainers/action | 重新整理容器清單 |
> |  | **保存庫/backupFabrics/backupProtectionIntent** |  |
> | 動作 | azurerm.recoveryservices/vault/backupFabrics/backupProtectionIntent/delete | 刪除備份保護用途 |
> | 動作 | azurerm.recoveryservices/vault/backupFabrics/backupProtectionIntent/read | 取得備份保護用途 |
> | 動作 | azurerm.recoveryservices/vault/backupFabrics/backupProtectionIntent/write | 建立備份保護用途 |
> |  | **保存庫/backupFabrics/operationResults** |  |
> | 動作 | azurerm.recoveryservices/vault/backupFabrics/operationResults/read | 傳回作業的狀態 |
> |  | **保存庫/backupFabrics/operationsStatus** |  |
> | 動作 | azurerm.recoveryservices/vault/backupFabrics/operationsStatus/read | 傳回作業的狀態 |
> |  | **保存庫/backupFabrics/protectableContainers** |  |
> | 動作 | azurerm.recoveryservices/vault/backupFabrics/protectableContainers/read | 取得所有可保護的容器 |
> |  | **保存庫/backupFabrics/protectionContainers** |  |
> | 動作 | azurerm.recoveryservices/vault/backupFabrics/protectionContainers/delete | 刪除已註冊的容器 |
> | 動作 | azurerm.recoveryservices/vault/backupFabrics/protectionContainers/inquire/action | 執行容器內工作負載的查詢 |
> | 動作 | azurerm.recoveryservices/vault/backupFabrics/protectionContainers/read | 傳回所有已註冊的容器 |
> | 動作 | azurerm.recoveryservices/vault/backupFabrics/protectionContainers/write | 建立已註冊的容器 |
> |  | **保存庫/backupFabrics/protectionContainers/個專案** |  |
> | 動作 | azurerm.recoveryservices/vault/backupFabrics/protectionContainers/items/read | 取得容器中的所有項目 |
> |  | **保存庫/backupFabrics/protectionContainers/operationResults** |  |
> | 動作 | azurerm.recoveryservices/vault/backupFabrics/protectionContainers/operationResults/read | 取得對保護容器執行之作業的結果。 |
> |  | **保存庫/backupFabrics/protectionContainers/operationsStatus** |  |
> | 動作 | azurerm.recoveryservices/vault/backupFabrics/protectionContainers/operationsStatus/read | 取得保護容器上執行之作業的狀態。 |
> |  | **保存庫/backupFabrics/protectionContainers/protectedItems** |  |
> | 動作 | azurerm.recoveryservices/vault/backupFabrics/protectionContainers/protectedItems/backup/action | 對受保護的項目執行備份。 |
> | 動作 | azurerm.recoveryservices/vault/backupFabrics/protectionContainers/protectedItems/delete | 刪除受保護的項目 |
> | 動作 | azurerm.recoveryservices/vault/backupFabrics/protectionContainers/protectedItems/read | 傳回受保護項目的物件詳細資料 |
> | 動作 | azurerm.recoveryservices/vault/backupFabrics/protectionContainers/protectedItems/write | 建立備用的受保護項目 |
> |  | **保存庫/backupFabrics/protectionContainers/protectedItems/operationResults** |  |
> | 動作 | azurerm.recoveryservices/vault/backupFabrics/protectionContainers/protectedItems/operationResults/read | 取得對受保護項目執行之作業的結果。 |
> |  | **保存庫/backupFabrics/protectionContainers/protectedItems/operationsStatus** |  |
> | 動作 | azurerm.recoveryservices/vault/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | 傳回對受保護項目執行之作業的狀態。 |
> |  | **保存庫/backupFabrics/protectionContainers/protectedItems/recoveryPoints** |  |
> | 動作 | azurerm.recoveryservices/vault/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | 為受保護的項目佈建即時項目復原 |
> | 動作 | azurerm.recoveryservices/vault/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | 取得受保護項目的復原點。 |
> | 動作 | azurerm.recoveryservices/vault/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | 還原受保護項目的復原點。 |
> | 動作 | azurerm.recoveryservices/vault/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | 為受保護的項目撤銷即時項目復原 |
> |  | **保存庫/backupJobs** |  |
> | 動作 | azurerm.recoveryservices/保存庫/backupJobs/取消/動作 | 取消作業 |
> | 動作 | azurerm.recoveryservices/vault/backupJobs/read | 傳回所有作業物件 |
> |  | **保存庫/backupJobs/operationResults** |  |
> | 動作 | azurerm.recoveryservices/vault/backupJobs/operationResults/read | 傳回作業的作業結果。 |
> |  | **保存庫/backupJobs/operationsStatus** |  |
> | 動作 | azurerm.recoveryservices/vault/backupJobs/operationsStatus/read | 傳回作業操作的狀態。 |
> |  | **保存庫/backupOperationResults** |  |
> | 動作 | azurerm.recoveryservices/vault/backupOperationResults/read | 傳回復原服務保存庫的備份作業結果。 |
> |  | **保存庫/backupOperations** |  |
> | 動作 | azurerm.recoveryservices/vault/backupOperations/read | 傳回復原服務保存庫的備份作業狀態。 |
> |  | **保存庫/backupPolicies** |  |
> | 動作 | azurerm.recoveryservices/vault/backupPolicies/delete | 刪除保護原則 |
> | 動作 | azurerm.recoveryservices/vault/backupPolicies/read | 傳回所有保護原則 |
> | 動作 | azurerm.recoveryservices/vault/backupPolicies/write | 建立保護原則 |
> |  | **保存庫/backupPolicies/operationResults** |  |
> | 動作 | azurerm.recoveryservices/vault/backupPolicies/operationResults/read | 取得原則作業的結果。 |
> |  | **保存庫/backupPolicies/作業** |  |
> | 動作 | azurerm.recoveryservices/vault/backupPolicies/作業/讀取 | 取得原則作業的狀態。 |
> |  | **保存庫/backupProtectableItems** |  |
> | 動作 | azurerm.recoveryservices/vault/backupProtectableItems/read | 傳回所有可保護項目的清單。 |
> |  | **保存庫/backupProtectedItems** |  |
> | 動作 | azurerm.recoveryservices/vault/backupProtectedItems/read | 傳回所有受保護項目的清單。 |
> |  | **保存庫/backupProtectionContainers** |  |
> | 動作 | azurerm.recoveryservices/vault/backupProtectionContainers/read | 傳回屬於訂用帳戶的所有容器 |
> |  | **保存庫/backupProtectionIntents** |  |
> | 動作 | azurerm.recoveryservices/vault/backupProtectionIntents/read | 列出所有的備份保護用途 |
> |  | **保存庫/backupstorageconfig** |  |
> | 動作 | azurerm.recoveryservices/vault/backupstorageconfig/read | 傳回復原服務保存庫的儲存體組態。 |
> | 動作 | azurerm.recoveryservices/vault/backupstorageconfig/write | 更新復原服務保存庫的儲存體組態。 |
> |  | **保存庫/backupUsageSummaries** |  |
> | 動作 | azurerm.recoveryservices/vault/backupUsageSummaries/read | 傳回復原服務之受保護項目和受保護伺服器的摘要。 |
> |  | **保存庫/憑證** |  |
> | 動作 | Microsoft.RecoveryServices/Vaults/certificates/write | 「更新資源憑證」作業會更新資源/保存庫的認證憑證。 |
> |  | **保存庫/extendedInformation** |  |
> | 動作 | Microsoft.RecoveryServices/Vaults/extendedInformation/read | 「取得延伸資訊」作業會取得物件的延伸資訊，此延伸資訊代表 'vault' 類型的 Azure 資源 |
> | 動作 | Microsoft.RecoveryServices/Vaults/extendedInformation/write | 「取得延伸資訊」作業會取得物件的延伸資訊，此延伸資訊代表 'vault' 類型的 Azure 資源 |
> | 動作 | Microsoft.RecoveryServices/Vaults/extendedInformation/delete | 「取得延伸資訊」作業會取得物件的延伸資訊，此延伸資訊代表 'vault' 類型的 Azure 資源 |
> |  | **保存庫/monitoringAlerts** |  |
> | 動作 | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | 取得復原服務保存庫的警示。 |
> | 動作 | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | 解決警示。 |
> |  | **保存庫/monitoringConfigurations** |  |
> | 動作 | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/read | 取得復原服務保存庫通知組態。 |
> | 動作 | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/write | 設定復原服務保存庫的電子郵件通知。 |
> |  | **保存庫/privateEndpointConnectionProxies** |  |
> | 動作 | azurerm.recoveryservices/vault/privateEndpointConnectionProxies/delete | 取得所有可保護的容器 |
> | 動作 | azurerm.recoveryservices/vault/privateEndpointConnectionProxies/read | 取得所有可保護的容器 |
> | 動作 | azurerm.recoveryservices/保存庫/privateEndpointConnectionProxies/驗證/動作 | 取得所有可保護的容器 |
> | 動作 | azurerm.recoveryservices/vault/privateEndpointConnectionProxies/write | 取得所有可保護的容器 |
> |  | **保存庫/privateEndpointConnectionProxies/operationResults** |  |
> | 動作 | azurerm.recoveryservices/vault/privateEndpointConnectionProxies/operationResults/read | 取得所有可保護的容器 |
> |  | **保存庫/privateEndpointConnectionProxies/operationsStatus** |  |
> | 動作 | azurerm.recoveryservices/vault/privateEndpointConnectionProxies/operationsStatus/read | 取得所有可保護的容器 |
> |  | **保存庫/privateEndpointConnections** |  |
> | 動作 | azurerm.recoveryservices/vault/privateEndpointConnections/write | 取得所有可保護的容器 |
> |  | **保存庫/privateEndpointConnections/operationResults** |  |
> | 動作 | azurerm.recoveryservices/vault/privateEndpointConnections/operationResults/read | 取得所有可保護的容器 |
> |  | **保存庫/privateEndpointConnections/operationsStatus** |  |
> | 動作 | azurerm.recoveryservices/vault/privateEndpointConnections/operationsStatus/read | 取得所有可保護的容器 |
> |  | **保存庫/registeredIdentities** |  |
> | 動作 | Microsoft.RecoveryServices/Vaults/registeredIdentities/write | 「註冊服務容器」作業可用來向復原服務註冊容器。 |
> | 動作 | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | 「取得容器」作業可用來取得為資源註冊的容器。 |
> | 動作 | Microsoft.RecoveryServices/Vaults/registeredIdentities/delete | 「取消註冊容器」作業可用來取消註冊容器。 |
> |  | **保存庫/registeredIdentities/operationResults** |  |
> | 動作 | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | 「取得作業結果」作業可用來取得以非同步方式提交之作業的作業狀態和結果 |
> |  | **保存庫/replicationAlertSettings** |  |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | 讀取任何警示設定 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationAlertSettings/write | 建立或更新任何警示設定 |
> |  | **保存庫/replicationEvents** |  |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationEvents/read | 讀取任何事件 |
> |  | **保存庫/replicationFabrics** |  |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/read | 讀取任何網狀架構 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/write | 建立或更新任何網狀架構 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/remove/action | 移除網狀架構 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | 檢查網狀架構的一致性 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/delete | 刪除任何網狀架構 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | 更新網狀架構的憑證 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/deployProcessServerImage/action | 部署處理序伺服器的映像 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | 重新關聯閘道 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/migratetoaad/action | 將網狀架構遷移至 AAD |
> |  | **保存庫/replicationFabrics/operationresults** |  |
> | 動作 | Azurerm.recoveryservices/vault/replicationFabrics/operationresults/read | 在資源網狀架構上追蹤非同步作業的結果 |
> |  | **保存庫/replicationFabrics/replicationLogicalNetworks** |  |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationLogicalNetworks/read | 讀取任何邏輯網路 |
> |  | **保存庫/replicationFabrics/replicationNetworks** |  |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | 讀取任何網路 |
> |  | **保存庫/replicationFabrics/replicationNetworks/replicationNetworkMappings** |  |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | 讀取任何網路對應 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/write | 建立或更新任何網路對應 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/delete | 刪除任何網路對應 |
> |  | **保存庫/replicationFabrics/replicationProtectionContainers** |  |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | 讀取任何保護容器 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/discoverProtectableItem/action | 探索可保護的項目 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/write | 建立或更新任何保護容器 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/remove/action | 移除保護容器 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action | 切換保護容器 |
> |  | **保存庫/replicationFabrics/replicationProtectionContainers/operationresults** |  |
> | 動作 | Azurerm.recoveryservices/vault/replicationFabrics/replicationProtectionContainers/operationresults/read | 追蹤資源保護容器上非同步作業的結果 |
> |  | **保存庫/replicationFabrics/replicationProtectionContainers/replicationMigrationItems** |  |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/read | 讀取任何移轉項目 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/write | 建立或更新任何移轉項目 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/delete | 刪除任何移轉項目 |
> | 動作 | Azurerm.recoveryservices/vault/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/resync/action | 同步 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/migrate/action | 遷移資料 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/testMigrate/action | 測試遷移 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/testMigrateCleanup/action | 測試遷移清除 |
> |  | **保存庫/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/migrationRecoveryPoints** |  |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/migrationRecoveryPoints/read | 讀取任何移轉復原點 |
> |  | **保存庫/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/operationresults** |  |
> | 動作 | Azurerm.recoveryservices/vault/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/operationresults/read | 追蹤資源遷移專案上非同步作業的結果 |
> |  | **保存庫/replicationFabrics/replicationProtectionContainers/replicationProtectableItems** |  |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | 讀取任何可保護的項目 |
> |  | **保存庫/replicationFabrics/replicationProtectionContainers/replicationProtectedItems** |  |
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
> | 動作 | Azurerm.recoveryservices/vault/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/addDisks/action | 新增磁片 |
> | 動作 | Azurerm.recoveryservices/vault/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/removeDisks/action | 移除磁片 |
> | 動作 | Azurerm.recoveryservices/vault/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/ResolveHealthErrors/action |  |
> |  | **保存庫/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/operationresults** |  |
> | 動作 | Azurerm.recoveryservices/vault/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/operationresults/read | 在受資源保護的專案上追蹤非同步作業的結果 |
> |  | **保存庫/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints** |  |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | 讀取任何複寫復原點 |
> |  | **保存庫/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/targetComputeSizes** |  |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/targetComputeSizes/read | 讀取任何目標計算大小 |
> |  | **保存庫/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings** |  |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | 讀取任何保護容器對應 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/write | 建立或更新任何保護容器對應 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/remove/action | 移除保護容器對應 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/delete | 刪除任何保護容器對應 |
> |  | **保存庫/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/operationresults** |  |
> | 動作 | Azurerm.recoveryservices/vault/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/operationresults/read | 在資源保護容器對應上追蹤非同步作業的結果 |
> |  | **保存庫/replicationFabrics/replicationRecoveryServicesProviders** |  |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | 讀取任何復原服務提供者 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/write | 建立或更新任何復原服務提供者 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/remove/action | 移除復原服務提供者 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/delete | 刪除任何復原服務提供者 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action | 重新整理提供者 |
> |  | **保存庫/replicationFabrics/replicationRecoveryServicesProviders/operationresults** |  |
> | 動作 | Azurerm.recoveryservices/vault/replicationFabrics/replicationRecoveryServicesProviders/operationresults/read | 在資源復原服務提供者上追蹤非同步作業的結果 |
> |  | **保存庫/replicationFabrics/replicationStorageClassifications** |  |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | 讀取任何存放裝置分類 |
> |  | **保存庫/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings** |  |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | 讀取任何存放裝置分類對應 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/write | 建立或更新任何存放裝置分類對應 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/delete | 刪除任何存放裝置分類對應 |
> |  | **保存庫/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/operationresults** |  |
> | 動作 | Azurerm.recoveryservices/vault/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/operationresults/read | 在資源儲存體分類對應上追蹤非同步作業的結果 |
> |  | **保存庫/replicationFabrics/replicationvCenters** |  |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | 讀取任何 vCenter |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/write | 建立或更新任何 vCenter |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/delete | 刪除任何 vCenter |
> |  | **保存庫/replicationFabrics/replicationvCenters/operationresults** |  |
> | 動作 | Azurerm.recoveryservices/vault/replicationFabrics/replicationvCenters/operationresults/read | 在資源 vCenters 上追蹤非同步作業的結果 |
> |  | **保存庫/replicationJobs** |  |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationJobs/read | 讀取任何作業 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationJobs/cancel/action | 取消作業 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationJobs/restart/action | 重新啟動作業 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationJobs/resume/action | 繼續作業 |
> |  | **保存庫/replicationJobs/operationresults** |  |
> | 動作 | Azurerm.recoveryservices/vault/replicationJobs/operationresults/read | 在資源工作上追蹤非同步作業的結果 |
> |  | **保存庫/replicationMigrationItems** |  |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationMigrationItems/read | 讀取任何移轉項目 |
> |  | **保存庫/replicationNetworkMappings** |  |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationNetworkMappings/read | 讀取任何網路對應 |
> |  | **保存庫/replicationNetworks** |  |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationNetworks/read | 讀取任何網路 |
> |  | **保存庫/replicationOperationStatus** |  |
> | 動作 | Azurerm.recoveryservices/vault/replicationOperationStatus/read | 讀取任何保存庫複寫作業狀態 |
> |  | **保存庫/replicationPolicies** |  |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationPolicies/read | 讀取任何原則 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationPolicies/write | 建立或更新任何原則 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationPolicies/delete | 刪除任何原則 |
> |  | **保存庫/replicationPolicies/operationresults** |  |
> | 動作 | Azurerm.recoveryservices/vault/replicationPolicies/operationresults/read | 追蹤對資源原則進行非同步作業的結果 |
> |  | **保存庫/replicationProtectedItems** |  |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationProtectedItems/read | 讀取任何受保護的項目 |
> |  | **保存庫/replicationProtectionContainerMappings** |  |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationProtectionContainerMappings/read | 讀取任何保護容器對應 |
> |  | **保存庫/replicationProtectionContainers** |  |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationProtectionContainers/read | 讀取任何保護容器 |
> |  | **保存庫/replicationRecoveryPlans** |  |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | 讀取任何復原方案 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/write | 建立或更新任何復原方案 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/delete | 刪除任何復原方案 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action | 計劃性容錯移轉復原方案 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action | 容錯移轉復原方案 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | 測試容錯移轉復原方案 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action | 測試容錯移轉清理復原方案 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action | 容錯移轉認可復原方案 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | 重新保護復原方案 |
> |  | **保存庫/replicationRecoveryPlans/operationresults** |  |
> | 動作 | Azurerm.recoveryservices/vault/replicationRecoveryPlans/operationresults/read | 在資源復原計畫上追蹤非同步作業的結果 |
> |  | **保存庫/replicationRecoveryServicesProviders** |  |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationRecoveryServicesProviders/read | 讀取任何復原服務提供者 |
> |  | **保存庫/replicationStorageClassificationMappings** |  |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationStorageClassificationMappings/read | 讀取任何存放裝置分類對應 |
> |  | **保存庫/replicationStorageClassifications** |  |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationStorageClassifications/read | 讀取任何存放裝置分類 |
> |  | **保存庫/replicationSupportedOperatingSystems** |  |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationSupportedOperatingSystems/read | 讀取任何  |
> |  | **保存庫/replicationUsages** |  |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationUsages/read | 讀取任何保存庫複寫使用量 |
> |  | **保存庫/replicationVaultHealth** |  |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationVaultHealth/read | 讀取任何保存庫複寫健康情況 |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationVaultHealth/refresh/action | 重新整理保存庫健康情況 |
> |  | **保存庫/replicationVaultHealth/operationresults** |  |
> | 動作 | Azurerm.recoveryservices/vault/replicationVaultHealth/operationresults/read | 追蹤資源保存庫複寫健全狀況上非同步作業的結果 |
> |  | **保存庫/replicationVaultSettings** |  |
> | 動作 | Azurerm.recoveryservices/vault/replicationVaultSettings/read | 讀取任何  |
> | 動作 | Azurerm.recoveryservices/vault/replicationVaultSettings/write | 建立或更新任何  |
> |  | **保存庫/replicationvCenters** |  |
> | 動作 | Microsoft.RecoveryServices/vaults/replicationvCenters/read | 讀取任何 vCenter |
> |  | **保存庫/使用方式** |  |
> | 動作 | azurerm.recoveryservices/保存庫/使用方式/讀取 | 傳回復原服務保存庫的使用量詳細資料。 |
> | 動作 | Microsoft.RecoveryServices/vaults/usages/read | 讀取任何保存庫使用量 |
> |  | **保存庫/vaultTokens** |  |
> | 動作 | Microsoft.RecoveryServices/Vaults/vaultTokens/read | 「保存庫權杖」作業可用來取得保存庫層級後端作業的保存庫權杖。 |

## <a name="microsoftrelay"></a>Microsoft.Relay

Azure 服務： [Azure 轉送](../service-bus-relay/relay-what-is-it.md)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.Relay/checkNamespaceAvailability/action | 檢查給定訂用帳戶下的命名空間是否可用。 此 API 即將淘汰。請改用 CheckNameAvailabiltiy。 |
> | 動作 | Microsoft.Relay/checkNameAvailability/action | 檢查給定訂用帳戶下的命名空間是否可用。 |
> | 動作 | Microsoft.Relay/register/action | 針對轉送資源提供者註冊訂用帳戶，並讓您能夠建立轉送資源 |
> | 動作 | Microsoft.Relay/unregister/action | 針對轉送資源提供者註冊訂用帳戶，並讓您能夠建立轉送資源 |
> |  | **命名空間** |  |
> | 動作 | Microsoft.Relay/namespaces/write | 建立命名空間資源，並更新其屬性。 命名空間的標記和容量是可以更新的屬性。 |
> | 動作 | Microsoft.Relay/namespaces/read | 取得命名空間資源描述的清單 |
> | 動作 | Microsoft.Relay/namespaces/Delete | 刪除命名空間資源 |
> | 動作 | Microsoft.Relay/namespaces/authorizationRules/action | 更新命名空間授權規則。 此 API 即將淘汰。 請改用 PUT 呼叫來更新命名空間授權規則。 API 版本 2017-04-01 不支援此作業。 |
> | 動作 | Microsoft.Relay/namespaces/removeAcsNamepsace/action | 移除 ACS 命名空間 |
> |  | **命名空間/authorizationRules** |  |
> | 動作 | Microsoft.Relay/namespaces/authorizationRules/read | 取得命名空間授權規則描述的清單。 |
> | 動作 | Microsoft.Relay/namespaces/authorizationRules/write | 建立命名空間層級的授權規則，並更新其屬性。 您可以更新授權規則的存取權限、主要金鑰和次要金鑰。 |
> | 動作 | Microsoft.Relay/namespaces/authorizationRules/delete | 刪除命名空間授權規則。 您無法刪除預設的命名空間授權規則。  |
> | 動作 | Microsoft.Relay/namespaces/authorizationRules/listkeys/action | 取得命名空間的連接字串 |
> | 動作 | Microsoft.Relay/namespaces/authorizationRules/regenerateKeys/action | 對資源重新產生主要或次要金鑰 |
> |  | **命名空間/disasterrecoveryconfigs** |  |
> | 動作 | Microsoft.Relay/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | 檢查指定訂用帳戶下命名空間別名的可用性。 |
> | 動作 | Microsoft.Relay/namespaces/disasterRecoveryConfigs/write | 建立或更新與命名空間相關聯的災害復原設定。 |
> | 動作 | Microsoft.Relay/namespaces/disasterRecoveryConfigs/read | 取得與命名空間相關聯的災害復原設定。 |
> | 動作 | Microsoft.Relay/namespaces/disasterRecoveryConfigs/delete | 刪除與命名空間相關聯的災害復原設定。 此作業只能透過主要命名空間叫用。 |
> | 動作 | Microsoft.Relay/namespaces/disasterRecoveryConfigs/breakPairing/action | 停用災害復原，並停止將變更從主要命名空間複寫至次要命名空間。 |
> | 動作 | Microsoft.Relay/namespaces/disasterRecoveryConfigs/failover/action | 叫用異地災害復原容錯移轉，並將命名空間別名重新設定為指向次要命名空間。 |
> |  | **命名空間/disasterRecoveryConfigs/authorizationRules** |  |
> | 動作 | Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules/read | 取得災害復原主要命名空間的授權規則 |
> | 動作 | Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | 取得災害復原主要命名空間的授權規則金鑰 |
> |  | **命名空間/HybridConnections** |  |
> | 動作 | Microsoft.Relay/namespaces/HybridConnections/write | 建立或更新 HybridConnection 屬性。 |
> | 動作 | Microsoft.Relay/namespaces/HybridConnections/read | 取得 HybridConnection 資源描述的清單 |
> | 動作 | Microsoft.Relay/namespaces/HybridConnections/Delete | 用來刪除 HybridConnection 資源的作業 |
> | 動作 | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/action | 更新 HybridConnection 的作業。 API 版本 2017-04-01 不支援此作業。 授權規則。 請使用 PUT 呼叫來更新授權規則。 |
> |  | **命名空間/HybridConnections/authorizationRules** |  |
> | 動作 | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/read |  取得 HybridConnection 授權規則的清單 |
> | 動作 | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/write | 建立 HybridConnection 授權規則，並更新其屬性。 您可以更新授權規則存取權限。 |
> | 動作 | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/delete | 用來刪除 HybridConnection 授權規則的作業 |
> | 動作 | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/listkeys/action | 取得 HybridConnection 的連接字串 |
> | 動作 | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/regeneratekeys/action | 對資源重新產生主要或次要金鑰 |
> |  | **命名空間/messagingPlan** |  |
> | 動作 | Microsoft.Relay/namespaces/messagingPlan/read | 取得命名空間的傳訊方案。<br>此 API 即將淘汰。<br>透過 MessagingPlan 資源公開的屬性，已移至新版 API 的 (父系) 命名空間資源。<br>API 版本 2017-04-01 不支援此作業。 |
> | 動作 | Microsoft.Relay/namespaces/messagingPlan/write | 更新命名空間的傳訊方案。<br>此 API 即將淘汰。<br>透過 MessagingPlan 資源公開的屬性，已移至新版 API 的 (父系) 命名空間資源。<br>API 版本 2017-04-01 不支援此作業。 |
> |  | **命名空間/networkrulesets** |  |
> | 動作 | Microsoft 轉送/命名空間/networkrulesets/讀取 | 取得 NetworkRuleSet 資源 |
> | 動作 | Microsoft 轉送/命名空間/networkrulesets/寫入 | 建立 VNET 規則資源 |
> | 動作 | Microsoft 轉送/命名空間/networkrulesets/delete | 刪除 VNET 規則資源 |
> |  | **命名空間/operationresults** |  |
> | 動作 | Microsoft.Relay/namespaces/operationresults/read | 取得命名空間作業的狀態 |
> |  | **命名空間/privateEndpointConnectionProxies** |  |
> | 動作 | Microsoft 轉送/命名空間/privateEndpointConnectionProxies/驗證/動作 | 驗證私用端點連接 Proxy |
> | 動作 | Microsoft 轉送/命名空間/privateEndpointConnectionProxies/讀取 | 取得私人端點連接 Proxy |
> | 動作 | Microsoft 轉送/命名空間/privateEndpointConnectionProxies/寫入 | 建立私用端點連接 Proxy |
> | 動作 | Microsoft 轉送/命名空間/privateEndpointConnectionProxies/delete | 刪除私人端點連接 Proxy |
> |  | **命名空間/提供者/Microsoft Insights/diagnosticSettings** |  |
> | 動作 | Microsoft 轉送/命名空間/提供者/Microsoft Insights/diagnosticSettings/read | 取得命名空間診斷設定資源描述的清單 |
> | 動作 | Microsoft 轉送/命名空間/提供者/Microsoft Insights/diagnosticSettings/write | 取得命名空間診斷設定資源描述的清單 |
> |  | **命名空間/提供者/Microsoft Insights/logDefinitions** |  |
> | 動作 | Microsoft 轉送/命名空間/提供者/Microsoft Insights/logDefinitions/read | 取得命名空間記錄資源描述的清單 |
> |  | **命名空間/提供者/Microsoft Insights/Metricdefinitions.listasync** |  |
> | 動作 | Microsoft.Relay/namespaces/providers/Microsoft.Insights/metricDefinitions/read | 取得命名空間計量資源描述的清單 |
> |  | **命名空間/WcfRelays** |  |
> | 動作 | Microsoft.Relay/namespaces/WcfRelays/write | 建立或更新 WcfRelay 屬性。 |
> | 動作 | Microsoft.Relay/namespaces/WcfRelays/read | 取得 WcfRelay 資源描述的清單 |
> | 動作 | Microsoft.Relay/namespaces/WcfRelays/Delete | 用來刪除 WcfRelay 資源的作業 |
> | 動作 | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/action | 更新 WcfRelay 的作業。 API 版本 2017-04-01 不支援此作業。 授權規則。 請使用 PUT 呼叫來更新授權規則。 |
> |  | **命名空間/WcfRelays/authorizationRules** |  |
> | 動作 | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/read |  取得 WcfRelay 授權規則的清單 |
> | 動作 | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/write | 建立 WcfRelay 授權規則，並更新其屬性。 您可以更新授權規則存取權限。 |
> | 動作 | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/delete | 用來刪除 WcfRelay 授權規則的作業 |
> | 動作 | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/listkeys/action | 取得 WcfRelay 的連接字串 |
> | 動作 | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/regeneratekeys/action | 對資源重新產生主要或次要金鑰 |
> |  | **業務** |  |
> | 動作 | Microsoft.Relay/operations/read | 取得作業 |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

Azure 服務： [Azure 服務健康狀態](../service-health/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.ResourceHealth/register/action | 為 Microsoft ResourceHealth 註冊訂用帳戶 |
> | 動作 | Microsoft.ResourceHealth/unregister/action | 取消註冊 Microsoft ResourceHealth 的訂用帳戶 |
> | 動作 | Microsoft.Resourcehealth/healthevent/action | 代表指定資源健康狀態的變更 |
> |  | **AvailabilityStatuses** |  |
> | 動作 | Microsoft.ResourceHealth/AvailabilityStatuses/read | 取得指定範圍中所有資源的可用性狀態 |
> |  | **AvailabilityStatuses/目前** |  |
> | 動作 | Microsoft.ResourceHealth/AvailabilityStatuses/current/read | 取得指定資源的可用性狀態 |
> |  | **emergingissues** |  |
> | 動作 | ResourceHealth/emergingissues/read | 取得 Azure 服務的新興問題 |
> |  | **events** |  |
> | 動作 | Microsoft.ResourceHealth/events/read | 取得指定訂用帳戶的服務健康狀態事件 |
> |  | **healthevent** |  |
> | 動作 | Microsoft.Resourcehealth/healthevent/Activated/action | 代表指定資源健康狀態的變更 |
> | 動作 | Microsoft.Resourcehealth/healthevent/Updated/action | 代表指定資源健康狀態的變更 |
> | 動作 | Microsoft.Resourcehealth/healthevent/Resolved/action | 代表指定資源健康狀態的變更 |
> | 動作 | Microsoft.Resourcehealth/healthevent/InProgress/action | 代表指定資源健康狀態的變更 |
> | 動作 | Microsoft.Resourcehealth/healthevent/Pending/action | 代表指定資源健康狀態的變更 |
> |  | **impactedResources** |  |
> | 動作 | Microsoft.ResourceHealth/impactedResources/read | 取得指定訂用帳戶的受影響資源 |
> |  | **metadata** |  |
> | 動作 | ResourceHealth/中繼資料/讀取 | 取得中繼資料 |
> |  | **通知** |  |
> | 動作 | ResourceHealth/通知/讀取 | 接收 Azure Resource Manager 通知 |
> |  | **作業** |  |
> | 動作 | Microsoft.ResourceHealth/Operations/read | 取得 Microsoft ResourceHealth 可用的作業 |

## <a name="microsoftresources"></a>Microsoft.Resources

Azure 服務： [Azure Resource Manager](../azure-resource-manager/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.Resources/checkResourceName/action | 檢查資源名稱是否有效。 |
> | 動作 | Microsoft .Resources/calculateTemplateHash/action | 計算提供的範本雜湊。 |
> |  | **checkPolicyCompliance** |  |
> | 動作 | Microsoft .Resources/checkPolicyCompliance/read | 檢查指定的資源是否符合資源原則，以了解合規性狀態。 |
> |  | **部署** |  |
> | 動作 | Microsoft.Resources/deployments/read | 取得或列出部署。 |
> | 動作 | Microsoft.Resources/deployments/write | 建立或更新部署。 |
> | 動作 | Microsoft.Resources/deployments/delete | 刪除部署。 |
> | 動作 | Microsoft.Resources/deployments/cancel/action | 取消部署。 |
> | 動作 | Microsoft.Resources/deployments/validate/action | 驗證部署。 |
> | 動作 | Microsoft .Resources/部署/whatIf/action | 預測範本部署變更。 |
> | 動作 | Microsoft .Resources/部署/exportTemplate/動作 | 匯出部署的範本 |
> |  | **部署/作業** |  |
> | 動作 | Microsoft.Resources/deployments/operations/read | 取得或列出部署作業。 |
> |  | **部署/operationstatuses** |  |
> | 動作 | Microsoft .Resources/部署/operationstatuses/讀取 | 取得或列出部署作業狀態。 |
> |  | **deploymentScripts** |  |
> | 動作 | Microsoft .Resources/deploymentScripts/read | 取得或列出部署腳本 |
> | 動作 | Microsoft .Resources/deploymentScripts/write | 建立或更新部署腳本 |
> | 動作 | Microsoft .Resources/deploymentScripts/delete | 刪除部署腳本 |
> |  | **deploymentScripts/記錄** |  |
> | 動作 | Microsoft .Resources/deploymentScripts/logs/read | 取得或列出部署腳本記錄 |
> |  | **連結** |  |
> | 動作 | Microsoft.Resources/links/read | 取得或列出資源連結。 |
> | 動作 | Microsoft.Resources/links/write | 建立或更新資源連結。 |
> | 動作 | Microsoft.Resources/links/delete | 刪除資源連結。 |
> |  | **marketplace** |  |
> | 動作 | Microsoft.Resources/marketplace/purchase/action | 從 Marketplace 購買資源。 |
> |  | **提供者** |  |
> | 動作 | Microsoft.Resources/providers/read | 取得提供者清單。 |
> |  | **resources** |  |
> | 動作 | Microsoft.Resources/resources/read | 根據篩選來取得資源清單。 |
> |  | **訂戶** |  |
> | 動作 | Microsoft.Resources/subscriptions/read | 取得訂用帳戶清單。 |
> |  | **訂用帳戶/位置** |  |
> | 動作 | Microsoft.Resources/subscriptions/locations/read | 取得所支援位置的清單。 |
> |  | **訂閱/operationresults** |  |
> | 動作 | Microsoft.Resources/subscriptions/operationresults/read | 取得訂用帳戶作業結果。 |
> |  | **訂閱/提供者** |  |
> | 動作 | Microsoft.Resources/subscriptions/providers/read | 取得或列出資源提供者。 |
> |  | **訂閱/resourceGroups** |  |
> | 動作 | Microsoft.Resources/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | 動作 | Microsoft.Resources/subscriptions/resourceGroups/write | 建立或更新資源群組。 |
> | 動作 | Microsoft.Resources/subscriptions/resourceGroups/delete | 刪除資源群組和其所有資源。 |
> | 動作 | Microsoft.Resources/subscriptions/resourceGroups/moveResources/action | 將資源從某個資源群組移到另一個資源群組。 |
> | 動作 | Microsoft.Resources/subscriptions/resourceGroups/validateMoveResources/action | 驗證資源從某個資源群組移到另一個資源群組的動作。 |
> |  | **訂用帳戶/resourcegroups/部署** |  |
> | 動作 | Microsoft.Resources/subscriptions/resourcegroups/deployments/read | 取得或列出部署。 |
> | 動作 | Microsoft.Resources/subscriptions/resourcegroups/deployments/write | 建立或更新部署。 |
> |  | **訂用帳戶/resourcegroups/部署/作業** |  |
> | 動作 | Microsoft.Resources/subscriptions/resourcegroups/deployments/operations/read | 取得或列出部署作業。 |
> |  | **訂閱/resourcegroups/部署/operationstatuses** |  |
> | 動作 | Microsoft.Resources/subscriptions/resourcegroups/deployments/operationstatuses/read | 取得或列出部署作業狀態。 |
> |  | **訂用帳戶/resourcegroups/資源** |  |
> | 動作 | Microsoft.Resources/subscriptions/resourcegroups/resources/read | 取得資源群組的資源。 |
> |  | **訂用帳戶/資源** |  |
> | 動作 | Microsoft.Resources/subscriptions/resources/read | 取得訂用帳戶的資源。 |
> |  | **訂閱/tagNames** |  |
> | 動作 | Microsoft.Resources/subscriptions/tagNames/read | 取得或列出訂用帳戶標記。 |
> | 動作 | Microsoft.Resources/subscriptions/tagNames/write | 新增訂用帳戶標記。 |
> | 動作 | Microsoft.Resources/subscriptions/tagNames/delete | 刪除訂用帳戶標記。 |
> |  | **訂閱/tagNames/tagValues** |  |
> | 動作 | Microsoft.Resources/subscriptions/tagNames/tagValues/read | 取得或列出訂用帳戶標記值。 |
> | 動作 | Microsoft.Resources/subscriptions/tagNames/tagValues/write | 新增訂用帳戶標記值。 |
> | 動作 | Microsoft.Resources/subscriptions/tagNames/tagValues/delete | 刪除訂用帳戶標記值。 |
> |  | **「標記」** |  |
> | 動作 | Microsoft .Resources/標記/讀取 | 取得資源上的所有標記。 |
> | 動作 | Microsoft .Resources/tags/write | 藉由以一組新的標記取代或合併現有標記，或移除現有的標記，更新資源上的標記。 |
> | 動作 | Microsoft .Resources/tags/delete | 移除資源上的所有標記。 |
> |  | **租戶** |  |
> | 動作 | Microsoft.Resources/tenants/read | 取得租用戶清單。 |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

Azure[服務：排程](../scheduler/index.md)器

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> |  | **jobcollections** |  |
> | 動作 | Microsoft.Scheduler/jobcollections/read | 取得作業集合 |
> | 動作 | Microsoft.Scheduler/jobcollections/write | 建立或更新作業集合。 |
> | 動作 | Microsoft.Scheduler/jobcollections/delete | 刪除作業集合。 |
> | 動作 | Microsoft.Scheduler/jobcollections/enable/action | 啟用作業集合。 |
> | 動作 | Microsoft.Scheduler/jobcollections/disable/action | 停用作業集合。 |
> |  | **jobcollections/作業** |  |
> | 動作 | Microsoft.Scheduler/jobcollections/jobs/read | 取得作業。 |
> | 動作 | Microsoft.Scheduler/jobcollections/jobs/write | 建立或更新作業。 |
> | 動作 | Microsoft.Scheduler/jobcollections/jobs/delete | 刪除作業。 |
> | 動作 | Microsoft.Scheduler/jobcollections/jobs/run/action | 執行作業。 |
> | 動作 | Microsoft.Scheduler/jobcollections/jobs/generateLogicAppDefinition/action | 根據排程器作業來產生邏輯應用程式定義。 |
> |  | **jobcollections/jobs/jobhistories** |  |
> | 動作 | Microsoft.Scheduler/jobcollections/jobs/jobhistories/read | 取得工作歷程記錄。 |

## <a name="microsoftsearch"></a>Microsoft.Search

Azure 服務： [Azure 搜尋服務](../search/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.Search/register/action | 針對搜尋資源提供者註冊訂用帳戶，並讓您能夠建立搜尋服務。 |
> | 動作 | Microsoft.Search/checkNameAvailability/action | 檢查服務名稱的可用性。 |
> |  | **業務** |  |
> | 動作 | Microsoft.Search/operations/read | 列出 Microsoft.Search 提供者的所有可用作業。 |
> |  | **searchServices** |  |
> | 動作 | Microsoft.Search/searchServices/write | 建立或更新搜尋服務。 |
> | 動作 | Microsoft.Search/searchServices/read | 讀取搜尋服務。 |
> | 動作 | Microsoft.Search/searchServices/delete | 刪除搜尋服務。 |
> | 動作 | Microsoft.Search/searchServices/start/action | 啟動搜尋服務。 |
> | 動作 | Microsoft.Search/searchServices/stop/action | 停止搜尋服務。 |
> | 動作 | Microsoft.Search/searchServices/listAdminKeys/action | 讀取系統管理金鑰。 |
> | 動作 | Microsoft.Search/searchServices/regenerateAdminKey/action | 重新產生系統管理金鑰。 |
> | 動作 | Microsoft 搜尋/searchServices/listQueryKeys/action | 傳回指定 Azure 搜尋服務的查詢 API 金鑰清單。 |
> | 動作 | Microsoft.Search/searchServices/createQueryKey/action | 建立查詢金鑰。 |
> |  | **searchServices/deleteQueryKey** |  |
> | 動作 | Microsoft.Search/searchServices/deleteQueryKey/delete | 刪除查詢金鑰。 |
> |  | **searchServices/privateEndpointConnectionProxies** |  |
> | 動作 | Microsoft 搜尋/searchServices/privateEndpointConnectionProxies/驗證/動作 | 驗證從 NRP 端的私用端點連接建立呼叫 |
> | 動作 | SearchServices/privateEndpointConnectionProxies/write | 建立具有指定參數的私用端點連接 proxy，或更新指定之私用端點連接 proxy 的屬性或標記 |
> | 動作 | Microsoft 搜尋/searchServices/privateEndpointConnectionProxies/read | 傳回私用端點連接 proxy 的清單，或取得指定之私用端點連接 proxy 的屬性。 |
> | 動作 | Microsoft 搜尋/searchServices/privateEndpointConnectionProxies/delete | 刪除現有的私用端點連接 proxy |

## <a name="microsoftsecurity"></a>Microsoft.Security

Azure 服務：[資訊安全中心](../security-center/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.Security/register/action | 為 Azure 資訊安全中心註冊訂用帳戶 |
> | 動作 | Microsoft.Security/unregister/action | 從 Azure 資訊安全中心取消註冊訂用帳戶 |
> |  | **adaptiveNetworkHardenings** |  |
> | 動作 | Microsoft. Security/adaptiveNetworkHardenings/read | 取得 Azure 受保護資源的彈性網路強化建議 |
> | 動作 | Microsoft. Security/adaptiveNetworkHardenings/強制執行/動作 | 藉由在指定的網路安全性群組上建立相符的安全性規則，強制執行指定的流量強化規則 |
> |  | **advancedThreatProtectionSettings** |  |
> | 動作 | Microsoft.Security/advancedThreatProtectionSettings/read | 取得資源的進階威脅防護設定 |
> | 動作 | Microsoft.Security/advancedThreatProtectionSettings/write | 更新資源的進階威脅防護設定 |
> |  | **alerts** |  |
> | 動作 | Microsoft.Security/alerts/read | 取得所有可用的安全性警訊 |
> |  | **applicationWhitelistings** |  |
> | 動作 | Microsoft.Security/applicationWhitelistings/read | 取得應用程式允許清單 |
> | 動作 | Microsoft.Security/applicationWhitelistings/write | 建立新的應用程式允許清單，或更新現有的應用程式允許清單 |
> |  | **assessmentMetadata** |  |
> | 動作 | Microsoft. Security/assessmentMetadata/read | 取得您訂用帳戶的可用安全性評量中繼資料 |
> | 動作 | Microsoft. Security/assessmentMetadata/write | 建立或更新安全性評量中繼資料 |
> |  | **評估** |  |
> | 動作 | Microsoft. 安全性/評量/讀取 | 取得訂用帳戶的安全性評量 |
> | 動作 | Microsoft. 安全性/評量/寫入 | 在您的訂用帳戶上建立或更新安全性評量 |
> |  | **autoProvisioningSettings** |  |
> | 動作 | Microsoft. Security/autoProvisioningSettings/read | 取得訂用帳戶的安全性自動布建設定 |
> | 動作 | Microsoft. Security/autoProvisioningSettings/write | 建立或更新訂用帳戶的安全性自動布建設定 |
> |  | **complianceResults** |  |
> | 動作 | Microsoft.Security/complianceResults/read | 取得資源的合規性結果 |
> |  | **informationProtectionPolicies** |  |
> | 動作 | Microsoft.Security/informationProtectionPolicies/read | 取得資源的資訊保護原則 |
> | 動作 | Microsoft.Security/informationProtectionPolicies/write | 更新資源的資訊保護原則 |
> |  | **堆放** |  |
> | 動作 | Microsoft.Security/locations/read | 取得安全性資料位置 |
> |  | **位置/警示** |  |
> | 動作 | Microsoft.Security/locations/alerts/read | 取得所有可用的安全性警訊 |
> | 動作 | Microsoft.Security/locations/alerts/dismiss/action | 關閉安全性警示 |
> | 動作 | Microsoft.Security/locations/alerts/activate/action | 啟動安全性警訊 |
> |  | **位置/jitNetworkAccessPolicies** |  |
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
> |  | **策略** |  |
> | 動作 | Microsoft.Security/policies/read | 取得安全性原則 |
> | 動作 | Microsoft.Security/policies/write | 更新安全性原則 |
> |  | **定價** |  |
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
> |  | **任務** |  |
> | 動作 | Microsoft.Security/tasks/read | 取得所有可用的安全性建議 |
> |  | **webApplicationFirewalls** |  |
> | 動作 | Microsoft.Security/webApplicationFirewalls/read | 取得 Web 應用程式防火牆 |
> | 動作 | Microsoft.Security/webApplicationFirewalls/write | 建立新的 Web 應用程式防火牆，或更新現有的 Web 應用程式防火牆 |
> | 動作 | Microsoft.Security/webApplicationFirewalls/delete | 刪除 Web 應用程式防火牆 |
> |  | **workspaceSettings** |  |
> | 動作 | Microsoft.Security/workspaceSettings/read | 取得工作區設定 |
> | 動作 | Microsoft.Security/workspaceSettings/write | 更新工作區設定 |
> | 動作 | Microsoft.Security/workspaceSettings/delete | 刪除工作區設定 |
> | 動作 | Microsoft.Security/workspaceSettings/connect/action | 變更工作區設定的重新連線設定 |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

Microsoft Monitoring Insights

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> |  | **diagnosticsettings** |  |
> | 動作 | Microsoft.SecurityGraph/diagnosticsettings/write | 正在寫入診斷設定 |
> | 動作 | Microsoft.SecurityGraph/diagnosticsettings/read | 正在讀取診斷設定 |
> | 動作 | Microsoft.SecurityGraph/diagnosticsettings/delete | 正在刪除診斷設定 |
> |  | **diagnosticsettingscategories** |  |
> | 動作 | Microsoft.SecurityGraph/diagnosticsettingscategories/read | 正在讀取診斷設定類別 |

## <a name="microsoftsecurityinsights"></a>SecurityInsights

Azure 服務： [Azure Sentinel](../sentinel/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | SecurityInsights/register/action | 註冊訂用帳戶以 Azure Sentinel |
> | 動作 | SecurityInsights/取消註冊/動作 | 從 Azure Sentinel 取消註冊訂用帳戶 |
> |  | **彙總** |  |
> | 動作 | SecurityInsights/匯總/讀取 | 取得匯總資訊 |
> |  | **alertRules** |  |
> | 動作 | SecurityInsights/alertRules/read | 取得警示規則 |
> | 動作 | SecurityInsights/alertRules/write | 更新警示規則 |
> | 動作 | SecurityInsights/alertRules/delete | 刪除警示規則 |
> |  | **alertRules/actions** |  |
> | 動作 | SecurityInsights/alertRules/actions/read | 取得警示規則的回應動作 |
> | 動作 | SecurityInsights/alertRules/actions/write | 更新警示規則的回應動作 |
> | 動作 | SecurityInsights/alertRules/actions/delete | 刪除警示規則的回應動作 |
> |  | **書籤** |  |
> | 動作 | SecurityInsights/書簽/讀取 | 取得書簽 |
> | 動作 | SecurityInsights/書簽/寫入 | 更新書簽 |
> | 動作 | SecurityInsights/書簽/刪除 | 刪除書簽 |
> | 動作 | SecurityInsights/書簽/展開/動作 | 依特定的擴充取得實體的相關實體 |
> |  | **種** |  |
> | 動作 | SecurityInsights/案例/讀取 | 取得案例 |
> | 動作 | SecurityInsights/案例/寫入 | 更新案例 |
> | 動作 | SecurityInsights/案例/刪除 | 刪除案例 |
> |  | **案例/批註** |  |
> | 動作 | SecurityInsights/案例/留言/讀取 | 取得案例批註 |
> | 動作 | SecurityInsights/案例/留言/寫入 | 建立案例批註 |
> |  | **案例/調查** |  |
> | 動作 | SecurityInsights/案例/調查/讀取 | 取得案例調查 |
> | 動作 | SecurityInsights/案例/調查/寫入 | 更新案例的中繼資料 |
> |  | **dataConnectors** |  |
> | 動作 | SecurityInsights/dataConnectors/read | 取得資料連線器 |
> | 動作 | SecurityInsights/dataConnectors/write | 更新資料連線器 |
> | 動作 | SecurityInsights/dataConnectors/delete | 刪除資料連線器 |
> |  | **次數** |  |
> | 動作 | SecurityInsights/事件/讀取 | 取得事件 |
> | 動作 | SecurityInsights/事件/寫入 | 更新事件 |
> | 動作 | SecurityInsights/事件/刪除 | 刪除事件 |
> |  | **事件/留言** |  |
> | 動作 | SecurityInsights/事件/留言/讀取 | 取得事件批註 |
> | 動作 | SecurityInsights/事件/留言/寫入 | 在事件上建立批註 |
> |  | **事件/關係** |  |
> | 動作 | SecurityInsights/事件/關係/讀取 | 取得事件和相關資源之間的關聯性 |
> | 動作 | SecurityInsights/事件/關係/寫入 | 更新事件和相關資源之間的關聯性 |
> | 動作 | SecurityInsights/事件/關係/刪除 | 刪除事件與相關資源之間的關聯性 |
> |  | **設定** |  |
> | 動作 | SecurityInsights/設定/讀取 | 取得設定 |
> | 動作 | SecurityInsights/settings/write | 更新設定 |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

Azure 服務：[服務匯流排](../service-bus/index.md)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.ServiceBus/checkNamespaceAvailability/action | 檢查給定訂用帳戶下的命名空間是否可用。 此 API 即將淘汰。請改用 CheckNameAvailabiltiy。 |
> | 動作 | Microsoft.ServiceBus/checkNameAvailability/action | 檢查給定訂用帳戶下的命名空間是否可用。 |
> | 動作 | Microsoft.ServiceBus/register/action | 針對 ServiceBus 資源提供者註冊訂用帳戶，並讓您能夠建立 ServiceBus 資源 |
> | 動作 | Microsoft.ServiceBus/unregister/action | 針對 ServiceBus 資源提供者註冊訂用帳戶，並讓您能夠建立 ServiceBus 資源 |
> |  | **堆放** |  |
> | 動作 | Microsoft.ServiceBus/locations/deleteVirtualNetworkOrSubnets/action | 針對指定的 VNet，刪除 ServiceBus 資源提供者中的 VNet 規則 |
> |  | **命名空間** |  |
> | 動作 | Microsoft.ServiceBus/namespaces/write | 建立命名空間資源，並更新其屬性。 命名空間的標記和容量是可以更新的屬性。 |
> | 動作 | Microsoft.ServiceBus/namespaces/read | 取得命名空間資源描述的清單 |
> | 動作 | Microsoft.ServiceBus/namespaces/Delete | 刪除命名空間資源 |
> | 動作 | Microsoft.ServiceBus/namespaces/authorizationRules/action | 更新命名空間授權規則。 此 API 即將淘汰。 請改用 PUT 呼叫來更新命名空間授權規則。 API 版本 2017-04-01 不支援此作業。 |
> | 動作 | Microsoft.ServiceBus/namespaces/migrate/action | 移轉命名空間作業 |
> | 動作 | Microsoft.ServiceBus/namespaces/removeAcsNamepsace/action | 移除 ACS 命名空間 |
> |  | **命名空間/authorizationRules** |  |
> | 動作 | Microsoft.ServiceBus/namespaces/authorizationRules/write | 建立命名空間層級的授權規則，並更新其屬性。 您可以更新授權規則的存取權限、主要金鑰和次要金鑰。 |
> | 動作 | Microsoft.ServiceBus/namespaces/authorizationRules/read | 取得命名空間授權規則描述的清單。 |
> | 動作 | Microsoft.ServiceBus/namespaces/authorizationRules/delete | 刪除命名空間授權規則。 您無法刪除預設的命名空間授權規則。  |
> | 動作 | Microsoft.ServiceBus/namespaces/authorizationRules/listkeys/action | 取得命名空間的連接字串 |
> | 動作 | Microsoft.ServiceBus/namespaces/authorizationRules/regenerateKeys/action | 對資源重新產生主要或次要金鑰 |
> |  | **命名空間/disasterrecoveryconfigs** |  |
> | 動作 | Microsoft.ServiceBus/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | 檢查指定訂用帳戶下命名空間別名的可用性。 |
> | 動作 | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/write | 建立或更新與命名空間相關聯的災害復原設定。 |
> | 動作 | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/read | 取得與命名空間相關聯的災害復原設定。 |
> | 動作 | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/delete | 刪除與命名空間相關聯的災害復原設定。 此作業只能透過主要命名空間叫用。 |
> | 動作 | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/breakPairing/action | 停用災害復原，並停止將變更從主要命名空間複寫至次要命名空間。 |
> | 動作 | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/failover/action | 叫用異地災害復原容錯移轉，並將命名空間別名重新設定為指向次要命名空間。 |
> |  | **命名空間/disasterRecoveryConfigs/authorizationRules** |  |
> | 動作 | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/read | 取得災害復原主要命名空間的授權規則 |
> | 動作 | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | 取得災害復原主要命名空間的授權規則金鑰 |
> |  | **命名空間/eventGridFilters** |  |
> | 動作 | Microsoft.ServiceBus/namespaces/eventGridFilters/write | 建立或更新與命名空間相關聯的事件格線篩選。 |
> | 動作 | Microsoft.ServiceBus/namespaces/eventGridFilters/read | 取得與命名空間相關聯的事件格線篩選。 |
> | 動作 | Microsoft.ServiceBus/namespaces/eventGridFilters/delete | 刪除與命名空間相關聯的事件格線篩選。 |
> |  | **命名空間/eventhubs** |  |
> | 動作 | Microsoft.ServiceBus/namespaces/eventhubs/read | 取得 EventHub 資源描述的清單 |
> |  | **命名空間/ipFilterRules** |  |
> | 動作 | Microsoft.ServiceBus/namespaces/ipFilterRules/read | 取得 IP 篩選資源 |
> | 動作 | Microsoft.ServiceBus/namespaces/ipFilterRules/write | 建立 IP 篩選資源 |
> | 動作 | Microsoft.ServiceBus/namespaces/ipFilterRules/delete | 刪除 IP 篩選資源 |
> |  | **命名空間/messagingPlan** |  |
> | 動作 | Microsoft.ServiceBus/namespaces/messagingPlan/read | 取得命名空間的傳訊方案。<br>此 API 即將淘汰。<br>透過 MessagingPlan 資源公開的屬性，已移至新版 API 的 (父系) 命名空間資源。<br>API 版本 2017-04-01 不支援此作業。 |
> | 動作 | Microsoft.ServiceBus/namespaces/messagingPlan/write | 更新命名空間的傳訊方案。<br>此 API 即將淘汰。<br>透過 MessagingPlan 資源公開的屬性，已移至新版 API 的 (父系) 命名空間資源。<br>API 版本 2017-04-01 不支援此作業。 |
> |  | **命名空間/migrationConfigurations** |  |
> | 動作 | Microsoft.ServiceBus/namespaces/migrationConfigurations/write | 建立或更新移轉設定。 這會開始將資源從標準命名空間同步至進階命名空間 |
> | 動作 | Microsoft.ServiceBus/namespaces/migrationConfigurations/read | 取得指出移轉和暫止複寫作業狀態的移轉設定 |
> | 動作 | Microsoft.ServiceBus/namespaces/migrationConfigurations/delete | 刪除移轉設定。 |
> | 動作 | Microsoft.ServiceBus/namespaces/migrationConfigurations/revert/action | 還原從標準到進階命名空間的移轉 |
> | 動作 | Microsoft.ServiceBus/namespaces/migrationConfigurations/upgrade/action | 將與標準命名空間建立關聯的 DNS 指派給進階命名空間，這會完成移轉，並停止將資源從標準命名空間同步至進階命名空間 |
> |  | **命名空間/networkruleset** |  |
> | 動作 | Microsoft. 匯流排/命名空間/networkruleset/read | 取得 NetworkRuleSet 資源 |
> | 動作 | Microsoft. 匯流排/命名空間/networkruleset/write | 建立 VNET 規則資源 |
> | 動作 | Microsoft. 匯流排/命名空間/networkruleset/delete | 刪除 VNET 規則資源 |
> |  | **命名空間/networkrulesets** |  |
> | 動作 | Microsoft. 匯流排/命名空間/networkrulesets/read | 取得 NetworkRuleSet 資源 |
> | 動作 | Microsoft. 匯流排/命名空間/networkrulesets/write | 建立 VNET 規則資源 |
> | 動作 | Microsoft. 匯流排/命名空間/networkrulesets/delete | 刪除 VNET 規則資源 |
> |  | **命名空間/operationresults** |  |
> | 動作 | Microsoft.ServiceBus/namespaces/operationresults/read | 取得命名空間作業的狀態 |
> |  | **命名空間/privateEndpointConnectionProxies** |  |
> | 動作 | Microsoft。執行匯流排/命名空間/privateEndpointConnectionProxies/驗證/動作 | 驗證私用端點連接 Proxy |
> | 動作 | Microsoft. 匯流排/命名空間/privateEndpointConnectionProxies/read | 取得私人端點連接 Proxy |
> | 動作 | Microsoft. 匯流排/命名空間/privateEndpointConnectionProxies/write | 建立私用端點連接 Proxy |
> | 動作 | Microsoft. 匯流排/命名空間/privateEndpointConnectionProxies/delete | 刪除私人端點連接 Proxy |
> |  | **命名空間/提供者/Microsoft Insights/diagnosticSettings** |  |
> | 動作 | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/diagnosticSettings/read | 取得命名空間診斷設定資源描述的清單 |
> | 動作 | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/diagnosticSettings/write | 取得命名空間診斷設定資源描述的清單 |
> |  | **命名空間/提供者/Microsoft Insights/logDefinitions** |  |
> | 動作 | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/logDefinitions/read | 取得命名空間記錄資源描述的清單 |
> |  | **命名空間/提供者/Microsoft Insights/Metricdefinitions.listasync** |  |
> | 動作 | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/metricDefinitions/read | 取得命名空間計量資源描述的清單 |
> |  | **命名空間/佇列** |  |
> | 動作 | Microsoft.ServiceBus/namespaces/queues/write | 建立或更新佇列屬性。 |
> | 動作 | Microsoft.ServiceBus/namespaces/queues/read | 取得佇列資源描述的清單 |
> | 動作 | Microsoft.ServiceBus/namespaces/queues/Delete | 用來刪除佇列資源的作業 |
> | 動作 | Microsoft.ServiceBus/namespaces/queues/authorizationRules/action | 更新佇列的作業。 API 版本 2017-04-01 不支援此作業。 授權規則。 請使用 PUT 呼叫來更新授權規則。 |
> |  | **命名空間/佇列/authorizationRules** |  |
> | 動作 | Microsoft.ServiceBus/namespaces/queues/authorizationRules/write | 建立佇列授權規則，並更新其屬性。 您可以更新授權規則存取權限。 |
> | 動作 | Microsoft.ServiceBus/namespaces/queues/authorizationRules/read |  取得佇列授權規則的清單 |
> | 動作 | Microsoft.ServiceBus/namespaces/queues/authorizationRules/delete | 用來刪除佇列授權規則的作業 |
> | 動作 | Microsoft.ServiceBus/namespaces/queues/authorizationRules/listkeys/action | 取得佇列的連接字串 |
> | 動作 | Microsoft.ServiceBus/namespaces/queues/authorizationRules/regenerateKeys/action | 對資源重新產生主要或次要金鑰 |
> |  | **命名空間/主題** |  |
> | 動作 | Microsoft.ServiceBus/namespaces/topics/write | 建立或更新主題屬性。 |
> | 動作 | Microsoft.ServiceBus/namespaces/topics/read | 取得主題資源描述的清單 |
> | 動作 | Microsoft.ServiceBus/namespaces/topics/Delete | 用來刪除主題資源的作業 |
> | 動作 | Microsoft.ServiceBus/namespaces/topics/authorizationRules/action | 更新主題的作業。 API 版本 2017-04-01 不支援此作業。 授權規則。 請使用 PUT 呼叫來更新授權規則。 |
> |  | **命名空間/主題/authorizationRules** |  |
> | 動作 | Microsoft.ServiceBus/namespaces/topics/authorizationRules/write | 建立主題授權規則，並更新其屬性。 您可以更新授權規則存取權限。 |
> | 動作 | Microsoft.ServiceBus/namespaces/topics/authorizationRules/read |  取得主題授權規則的清單 |
> | 動作 | Microsoft.ServiceBus/namespaces/topics/authorizationRules/delete | 用來刪除主題授權規則的作業 |
> | 動作 | Microsoft.ServiceBus/namespaces/topics/authorizationRules/listkeys/action | 取得主題的連接字串 |
> | 動作 | Microsoft.ServiceBus/namespaces/topics/authorizationRules/regenerateKeys/action | 對資源重新產生主要或次要金鑰 |
> |  | **命名空間/主題/訂用帳戶** |  |
> | 動作 | Microsoft.ServiceBus/namespaces/topics/subscriptions/write | 建立或更新 TopicSubscription 屬性。 |
> | 動作 | Microsoft.ServiceBus/namespaces/topics/subscriptions/read | 取得 TopicSubscription 資源描述的清單 |
> | 動作 | Microsoft.ServiceBus/namespaces/topics/subscriptions/Delete | 用來刪除 TopicSubscription 資源的作業 |
> |  | **命名空間/主題/訂用帳戶/規則** |  |
> | 動作 | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/write | 建立或更新規則屬性。 |
> | 動作 | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/read | 取得規則資源描述的清單 |
> | 動作 | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/Delete | 用來刪除規則資源的作業 |
> |  | **命名空間/virtualNetworkRules** |  |
> | 動作 | Microsoft.ServiceBus/namespaces/virtualNetworkRules/read | 取得 VNET 規則資源 |
> | 動作 | Microsoft.ServiceBus/namespaces/virtualNetworkRules/write | 建立 VNET 規則資源 |
> | 動作 | Microsoft.ServiceBus/namespaces/virtualNetworkRules/delete | 刪除 VNET 規則資源 |
> |  | **業務** |  |
> | 動作 | Microsoft.ServiceBus/operations/read | 取得作業 |
> |  | **sku** |  |
> | 動作 | Microsoft.ServiceBus/sku/read | 取得 SKU 資源描述的清單 |
> |  | **sku/區域** |  |
> | 動作 | Microsoft.ServiceBus/sku/regions/read | 取得 SkuRegions 資源描述的清單 |
> |  | **命名空間/訊息** |  |
> | DataAction | Microsoft. 執行匯流排/命名空間/訊息/傳送/動作 | 傳送訊息 |
> | DataAction | Microsoft. 執行匯流排/命名空間/訊息/接收/動作 | 接收訊息 |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

Azure 服務： [Service Fabric](../service-fabric/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.ServiceFabric/register/action | 註冊任何動作 |
> |  | **叢集** |  |
> | 動作 | Microsoft.ServiceFabric/clusters/read | 讀取任何叢集 |
> | 動作 | Microsoft.ServiceFabric/clusters/write | 建立或更新任何叢集 |
> | 動作 | Microsoft.ServiceFabric/clusters/delete | 刪除任何叢集 |
> |  | **叢集/應用程式** |  |
> | 動作 | Microsoft.ServiceFabric/clusters/applications/read | 讀取任何應用程式 |
> | 動作 | Microsoft.ServiceFabric/clusters/applications/write | 建立或更新任何應用程式 |
> | 動作 | Microsoft.ServiceFabric/clusters/applications/delete | 刪除任何應用程式 |
> |  | **叢集/應用程式/服務** |  |
> | 動作 | Microsoft.ServiceFabric/clusters/applications/services/read | 讀取任何服務 |
> | 動作 | Microsoft.ServiceFabric/clusters/applications/services/write | 建立或更新任何服務 |
> | 動作 | Microsoft.ServiceFabric/clusters/applications/services/delete | 刪除任何服務 |
> |  | **叢集/應用程式/服務/磁碟分割** |  |
> | 動作 | Microsoft.ServiceFabric/clusters/applications/services/partitions/read | 讀取任何分割區 |
> |  | **叢集/應用程式/服務/磁碟分割/複本** |  |
> | 動作 | Microsoft.ServiceFabric/clusters/applications/services/partitions/replicas/read | 讀取任何複本 |
> |  | **叢集/應用程式/服務/狀態** |  |
> | 動作 | Microsoft.ServiceFabric/clusters/applications/services/statuses/read | 讀取任何服務狀態 |
> |  | **叢集/applicationTypes** |  |
> | 動作 | Microsoft.ServiceFabric/clusters/applicationTypes/read | 讀取任何應用程式類型 |
> | 動作 | Microsoft.ServiceFabric/clusters/applicationTypes/write | 建立或更新任何應用程式類型 |
> | 動作 | Microsoft.ServiceFabric/clusters/applicationTypes/delete | 刪除任何應用程式類型 |
> |  | **叢集/applicationTypes/版本** |  |
> | 動作 | Microsoft.ServiceFabric/clusters/applicationTypes/versions/read | 讀取任何應用程式類型版本 |
> | 動作 | Microsoft.ServiceFabric/clusters/applicationTypes/versions/write | 建立或更新任何應用程式類型版本 |
> | 動作 | Microsoft.ServiceFabric/clusters/applicationTypes/versions/delete | 刪除任何應用程式類型版本 |
> |  | **群集/節點** |  |
> | 動作 | Microsoft.ServiceFabric/clusters/nodes/read | 讀取任何節點 |
> |  | **群集/狀態** |  |
> | 動作 | Microsoft.ServiceFabric/clusters/statuses/read | 讀取任何叢集狀態 |
> |  | **位置/clusterVersions** |  |
> | 動作 | Microsoft.ServiceFabric/locations/clusterVersions/read | 讀取任何叢集版本 |
> |  | **位置/環境/clusterVersions** |  |
> | 動作 | Microsoft.ServiceFabric/locations/environments/clusterVersions/read | 讀取特定環境的任何叢集版本 |
> |  | **位置/operationresults** |  |
> | 動作 | Microsoft.ServiceFabric/locations/operationresults/read | 讀取任何作業結果 |
> |  | **位置/作業** |  |
> | 動作 | Microsoft.ServiceFabric/locations/operations/read | 依位置讀取任何作業 |
> |  | **業務** |  |
> | 動作 | Microsoft.ServiceFabric/operations/read | 讀取任何可用的作業 |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

Azure 服務： [Azure SignalR Service](../azure-signalr/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.SignalRService/register/action | 向訂用帳戶註冊 'Microsoft.SignalRService' 資源提供者 |
> | 動作 | Microsoft.SignalRService/unregister/action | 向訂用帳戶取消註冊 'Microsoft.SignalRService' 資源提供者 |
> |  | **堆放** |  |
> | 動作 | Microsoft.SignalRService/locations/checknameavailability/action | 檢查名稱是否可用於新的 SignalR 服務 |
> |  | **位置/operationresults/signalr** |  |
> | 動作 | Microsoft.SignalRService/locations/operationresults/signalr/read | 查詢以位置為基礎的非同步作業結果 |
> |  | **位置/operationStatuses/operationId** |  |
> | 動作 | Microsoft.signalrservice/位置/operationStatuses/operationId/讀取 | 查詢以位置為基礎的非同步作業狀態 |
> |  | **位置/使用方式** |  |
> | 動作 | Microsoft.SignalRService/locations/usages/read | 取得 Azure SignalR Service 的配額使用量 |
> |  | **operationresults** |  |
> | 動作 | Microsoft.SignalRService/operationresults/read | 查詢提供者層級非同步作業的結果 |
> |  | **業務** |  |
> | 動作 | Microsoft.signalrservice/作業/讀取 | 列出 Azure SignalR 服務的作業。 |
> |  | **operationstatus** |  |
> | 動作 | Microsoft.signalrservice/operationstatus/read | 查詢提供者層級非同步作業的狀態 |
> |  | **SignalR** |  |
> | 動作 | Microsoft.SignalRService/SignalR/read | 在管理入口網站中或透過 API 檢視 SignalR 的設定和組態 |
> | 動作 | Microsoft.SignalRService/SignalR/write | 在管理入口網站中或透過 API 修改 SignalR 的設定和組態 |
> | 動作 | Microsoft.SignalRService/SignalR/delete | 刪除整個 SignalR Service |
> | 動作 | Microsoft.SignalRService/SignalR/listkeys/action | 在管理入口網站中或透過 API 檢視 SignalR 存取金鑰 |
> | 動作 | Microsoft.SignalRService/SignalR/regeneratekey/action | 在管理入口網站中或透過 API 變更 SignalR 存取金鑰 |
> | 動作 | Microsoft.SignalRService/SignalR/restart/action | 在管理入口網站中或透過 API 重新啟動 Azure SignalR Service。 會有一些停機時間。 |
> |  | **SignalR/eventGridFilters** |  |
> | 動作 | Microsoft.signalrservice/SignalR/eventGridFilters/read | 取得指定之事件方格篩選器的屬性，或列出指定之 SignalR 的所有事件方格篩選準則。 |
> | 動作 | Microsoft.signalrservice/SignalR/eventGridFilters/write | 針對具有指定參數的 SignalR，建立或更新事件方格篩選準則。 |
> | 動作 | Microsoft.signalrservice/SignalR/eventGridFilters/delete | 從 SignalR 中刪除事件方格篩選準則。 |
> |  | **SignalR/privateEndpointConnectionProxies** |  |
> | 動作 | Microsoft.signalrservice/SignalR/privateEndpointConnectionProxies/validate/action | 驗證私用端點連接 Proxy |
> | 動作 | Microsoft.signalrservice/SignalR/privateEndpointConnectionProxies/write | 建立私用端點連接 Proxy |
> | 動作 | Microsoft.signalrservice/SignalR/privateEndpointConnectionProxies/read | 讀取私人端點連接 Proxy |
> | 動作 | Microsoft.signalrservice/SignalR/privateEndpointConnectionProxies/delete | 刪除私人端點連接 Proxy |
> |  | **SignalR/privateEndpointConnections** |  |
> | 動作 | Microsoft.signalrservice/SignalR/privateEndpointConnections/write | 核准或拒絕私人端點連接 |
> | 動作 | Microsoft.signalrservice/SignalR/privateEndpointConnections/read | 讀取私人端點連接 |
> |  | **SignalR/privateLinkResources** |  |
> | 動作 | Microsoft.signalrservice/SignalR/privateLinkResources/read | 列出所有 SignalR 的私人連結資源 |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

Azure 服務： [Azure 受控應用程式](../azure-resource-manager/managed-applications/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.Solutions/register/action | 向 Solutions 註冊。 |
> | 動作 | Microsoft. 解決方案/取消註冊/動作 | 從解決方案中取消註冊。 |
> |  | **applicationDefinitions** |  |
> | 動作 | Microsoft.Solutions/applicationDefinitions/read | 擷取應用程式定義清單。 |
> | 動作 | Microsoft.Solutions/applicationDefinitions/write | 新增或修改應用程式定義。 |
> | 動作 | Microsoft.Solutions/applicationDefinitions/delete | 移除應用程式定義。 |
> |  | **applicationDefinitions/applicationArtifacts** |  |
> | 動作 | Microsoft 解決方案/applicationDefinitions/applicationArtifacts/read | 列出應用程式定義的應用程式構件。 |
> |  | **應用程式** |  |
> | 動作 | Microsoft.Solutions/applications/read | 擷取應用程式清單。 |
> | 動作 | Microsoft.Solutions/applications/write | 建立應用程式。 |
> | 動作 | Microsoft.Solutions/applications/delete | 移除應用程式。 |
> | 動作 | Microsoft. 解決方案/應用程式/refreshPermissions/動作 | 重新整理應用程式許可權。 |
> | 動作 | Microsoft. 解決方案/應用程式/updateAccess/動作 | 更新應用程式存取。 |
> |  | **應用程式/applicationArtifacts** |  |
> | 動作 | Microsoft. 解決方案/應用程式/applicationArtifacts/讀取 | 列出應用程式成品。 |
> |  | **Jitrequest** |  |
> | 動作 | Microsoft.Solutions/jitRequests/read | 擷取 JitRequests 清單 |
> | 動作 | Microsoft.Solutions/jitRequests/write | 建立 JitRequest |
> | 動作 | Microsoft.Solutions/jitRequests/delete | 移除 JitRequest |
> |  | **位置/operationStatuses** |  |
> | 動作 | Microsoft.Solutions/locations/operationStatuses/read | 讀取資源的作業狀態。 |
> |  | **業務** |  |
> | 動作 | Microsoft。解決方案/作業/讀取 | 取得作業的清單。 |

## <a name="microsoftsql"></a>Microsoft.Sql

Azure 服務： [Azure SQL Database](../sql-database/index.yml)、 [SQL 資料倉儲](../sql-data-warehouse/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.Sql/checkNameAvailability/action | 確認指定的伺服器名稱是否可用來針對指定的訂用帳戶進行全球佈建。 |
> | 動作 | Microsoft.Sql/register/action | 為 Microsoft SQL Database 資源提供者註冊訂用帳戶，並讓您能夠建立 Microsoft SQL Database。 |
> | 動作 | Microsoft.Sql/unregister/action | 為 Microsoft SQL Database 資源提供者取消註冊訂用帳戶，並讓您能夠建立 Microsoft SQL Database。 |
> | 動作 | Microsoft .Sql/privateEndpointConnectionsApproval/action | 決定是否允許使用者核准私用端點連接 |
> |  | **instancePools** |  |
> | 動作 | Microsoft.Sql/instancePools/read | 取得執行個體集區 |
> | 動作 | Microsoft.Sql/instancePools/write | 建立或更新執行個體集區 |
> | 動作 | Microsoft.Sql/instancePools/delete | 刪除執行個體集區 |
> |  | **instancePools/使用方式** |  |
> | 動作 | Microsoft .Sql/instancePools/使用方式/讀取 | 取得實例集區的使用方式資訊 |
> |  | **堆放** |  |
> | 動作 | Microsoft.Sql/locations/read | 取得指定訂用帳戶的可用位置 |
> |  | **位置/auditingSettingsAzureAsyncOperation** |  |
> | 動作 | Microsoft.Sql/locations/auditingSettingsAzureAsyncOperation/read | 擷取擴充伺服器的 Blob 稽核原則設定作業結果 |
> |  | **位置/auditingSettingsOperationResults** |  |
> | 動作 | Microsoft.Sql/locations/auditingSettingsOperationResults/read | 擷取伺服器的「Blob 稽核原則設定」作業結果 |
> |  | **位置/功能** |  |
> | 動作 | Microsoft.Sql/locations/capabilities/read | 在指定位置取得此訂用帳戶的功能 |
> |  | **位置/databaseAzureAsyncOperation** |  |
> | 動作 | Microsoft.Sql/locations/databaseAzureAsyncOperation/read | 取得資料庫作業的狀態。 |
> |  | **位置/databaseOperationResults** |  |
> | 動作 | Microsoft.Sql/locations/databaseOperationResults/read | 取得資料庫作業的狀態。 |
> |  | **位置/deletedServerAsyncOperation** |  |
> | 動作 | Microsoft.Sql/locations/deletedServerAsyncOperation/read | 取得已刪除伺服器上進行中的作業 |
> |  | **位置/deletedServerOperationResults** |  |
> | 動作 | Microsoft.Sql/locations/deletedServerOperationResults/read | 取得已刪除伺服器上進行中的作業 |
> |  | **位置/deletedServers** |  |
> | 動作 | Microsoft.Sql/locations/deletedServers/read | 傳回已刪除伺服器的清單，或取得指定已刪除伺服器的屬性。 |
> | 動作 | Microsoft.Sql/locations/deletedServers/recover/action | 復原已刪除的伺服器 |
> |  | **位置/elasticPoolAzureAsyncOperation** |  |
> | 動作 | Microsoft.Sql/locations/elasticPoolAzureAsyncOperation/read | 取得彈性集區非同步作業的 Aure 非同步作業 |
> |  | **位置/elasticPoolOperationResults** |  |
> | 動作 | Microsoft.Sql/locations/elasticPoolOperationResults/read | 取得彈性集區作業的結果。 |
> |  | **位置/encryptionProtectorAzureAsyncOperation** |  |
> | 動作 | Microsoft .Sql/位置/encryptionProtectorAzureAsyncOperation/讀取 | 取得透明資料加密加密保護裝置上的進行中作業 |
> |  | **位置/encryptionProtectorOperationResults** |  |
> | 動作 | Microsoft .Sql/位置/encryptionProtectorOperationResults/讀取 | 取得透明資料加密加密保護裝置上的進行中作業 |
> |  | **位置/extendedAuditingSettingsAzureAsyncOperation** |  |
> | 動作 | Microsoft.Sql/locations/extendedAuditingSettingsAzureAsyncOperation/read | 擷取擴充伺服器的 Blob 稽核原則設定作業結果 |
> |  | **位置/extendedAuditingSettingsOperationResults** |  |
> | 動作 | Microsoft.Sql/locations/extendedAuditingSettingsOperationResults/read | 擷取擴充伺服器的 Blob 稽核原則設定作業結果 |
> |  | **位置/firewallRulesAzureAsyncOperation** |  |
> | 動作 | Microsoft.Sql/locations/firewallRulesAzureAsyncOperation/read | 取得防火牆規則作業的狀態。 |
> |  | **位置/firewallRulesOperationResults** |  |
> | 動作 | Microsoft.Sql/locations/firewallRulesOperationResults/read | 取得防火牆規則作業的狀態。 |
> |  | **位置/instanceFailoverGroups** |  |
> | 動作 | Microsoft.Sql/locations/instanceFailoverGroups/read | 傳回執行個體容錯移轉群組的清單，或取得指定執行個體容錯移轉群組的屬性。 |
> | 動作 | Microsoft.Sql/locations/instanceFailoverGroups/write | 建立具有指定參數的實例容錯移轉群組，或更新指定之實例容錯移轉群組的屬性或標記。 |
> | 動作 | Microsoft.Sql/locations/instanceFailoverGroups/delete | 刪除現有的執行個體容錯移轉群組。 |
> | 動作 | Microsoft.Sql/locations/instanceFailoverGroups/failover/action | 在現有的執行個體容錯移轉群組中執行規劃的容錯移轉。 |
> | 動作 | Microsoft.Sql/locations/instanceFailoverGroups/forceFailoverAllowDataLoss/action | 在現有的執行個體容錯移轉群組中執行強制容錯移轉。 |
> |  | **位置/instancePoolAzureAsyncOperation** |  |
> | 動作 | Microsoft.Sql/locations/instancePoolAzureAsyncOperation/read | 取得實例集區作業的狀態 |
> |  | **位置/instancePoolOperationResults** |  |
> | 動作 | Microsoft.Sql/locations/instancePoolOperationResults/read | 取得實例集區作業的結果 |
> |  | **位置/interfaceEndpointProfileAzureAsyncOperation** |  |
> | 動作 | Microsoft.Sql/locations/interfaceEndpointProfileAzureAsyncOperation/read | 傳回特定介面端點 Azure 非同步作業的詳細資料 |
> |  | **位置/interfaceEndpointProfileOperationResults** |  |
> | 動作 | Microsoft.Sql/locations/interfaceEndpointProfileOperationResults/read | 傳回特定介面端點 設定檔作業的詳細資料 |
> |  | **位置/jobAgentAzureAsyncOperation** |  |
> | 動作 | Microsoft.Sql/locations/jobAgentAzureAsyncOperation/read | 取得工作代理程式作業的狀態。 |
> |  | **位置/jobAgentOperationResults** |  |
> | 動作 | Microsoft.Sql/locations/jobAgentOperationResults/read | 取得工作代理程式作業的結果。 |
> |  | **位置/longTermRetentionBackups** |  |
> | 動作 | Microsoft.Sql/locations/longTermRetentionBackups/read | 列出位置中每個伺服器上每個資料庫的長期保留備份 |
> |  | **位置/longTermRetentionManagedInstanceBackups** |  |
> | 動作 | Microsoft .Sql/位置/longTermRetentionManagedInstanceBackups/讀取 | 傳回特定位置的受控實例 LTR 備份清單  |
> |  | **位置/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups** |  |
> | 動作 | Microsoft .Sql/位置/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/read | 傳回受控實例資料庫的 LTR 備份清單 |
> | 動作 | Microsoft .Sql/位置/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/delete | 刪除受控實例資料庫的 LTR 備份 |
> |  | **位置/longTermRetentionManagedInstances/longTermRetentionManagedInstanceBackups** |  |
> | 動作 | Microsoft .Sql/位置/longTermRetentionManagedInstances/longTermRetentionManagedInstanceBackups/read | 傳回特定受控實例的受控實例 LTR 備份清單 |
> |  | **位置/longTermRetentionServers/longTermRetentionBackups** |  |
> | 動作 | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionBackups/read | 列出伺服器上每個資料庫的長期保留備份 |
> |  | **位置/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups** |  |
> | 動作 | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/read | 列出資料庫的長期保留備份 |
> | 動作 | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete | 刪除長期保留備份 |
> |  | **位置/managedDatabaseRestoreAzureAsyncOperation** |  |
> | 動作 | Microsoft.Sql/locations/managedDatabaseRestoreAzureAsyncOperation/completeRestore/action | 完成受控資料庫還原作業 |
> |  | **位置/managedInstanceEncryptionProtectorAzureAsyncOperation** |  |
> | 動作 | Microsoft .Sql/位置/managedInstanceEncryptionProtectorAzureAsyncOperation/讀取 | 取得透明資料加密受控實例加密保護裝置上的進行中作業 |
> |  | **位置/managedInstanceEncryptionProtectorOperationResults** |  |
> | 動作 | Microsoft .Sql/位置/managedInstanceEncryptionProtectorOperationResults/讀取 | 取得透明資料加密受控實例加密保護裝置上的進行中作業 |
> |  | **位置/managedInstanceKeyAzureAsyncOperation** |  |
> | 動作 | Microsoft .Sql/位置/managedInstanceKeyAzureAsyncOperation/讀取 | 取得透明資料加密受控實例金鑰的進行中作業 |
> |  | **位置/managedInstanceKeyOperationResults** |  |
> | 動作 | Microsoft .Sql/位置/managedInstanceKeyOperationResults/讀取 | 取得透明資料加密受控實例金鑰的進行中作業 |
> |  | **位置/managedInstanceLongTermRetentionPolicyAzureAsyncOperation** |  |
> | 動作 | Microsoft .Sql/位置/managedInstanceLongTermRetentionPolicyAzureAsyncOperation/讀取 | 取得受管理資料庫長期保留原則作業的狀態 |
> |  | **位置/managedInstanceLongTermRetentionPolicyOperationResults** |  |
> | 動作 | Microsoft .Sql/位置/managedInstanceLongTermRetentionPolicyOperationResults/讀取 | 取得受管理資料庫長期保留原則作業的狀態 |
> |  | **位置/managedShortTermRetentionPolicyOperationResults** |  |
> | 動作 | Microsoft .Sql/位置/managedShortTermRetentionPolicyOperationResults/讀取 | 取得短期保留原則操作的狀態 |
> |  | **位置/managedTransparentDataEncryptionAzureAsyncOperation** |  |
> | 動作 | Microsoft.Sql/locations/managedTransparentDataEncryptionAzureAsyncOperation/read | 取得有關受控資料庫之透明資料加密的進行中作業 |
> |  | **位置/managedTransparentDataEncryptionOperationResults** |  |
> | 動作 | Microsoft.Sql/locations/managedTransparentDataEncryptionOperationResults/read | 取得有關受控資料庫之透明資料加密的進行中作業 |
> |  | **位置/privateEndpointConnectionAzureAsyncOperation** |  |
> | 動作 | Microsoft .Sql/位置/privateEndpointConnectionAzureAsyncOperation/讀取 | 取得私用端點連接作業的結果 |
> |  | **位置/privateEndpointConnectionOperationResults** |  |
> | 動作 | Microsoft .Sql/位置/privateEndpointConnectionOperationResults/讀取 | 取得私用端點連接作業的結果 |
> |  | **位置/privateEndpointConnectionProxyAzureAsyncOperation** |  |
> | 動作 | Microsoft .Sql/位置/privateEndpointConnectionProxyAzureAsyncOperation/讀取 | 取得私用端點連接 proxy 作業的結果 |
> |  | **位置/privateEndpointConnectionProxyOperationResults** |  |
> | 動作 | Microsoft .Sql/位置/privateEndpointConnectionProxyOperationResults/讀取 | 取得私用端點連接 proxy 作業的結果 |
> |  | **位置/serverAdministratorAzureAsyncOperation** |  |
> | 動作 | Microsoft .Sql/位置/serverAdministratorAzureAsyncOperation/讀取 | 伺服器 Azure Active Directory 系統管理員非同步作業結果 |
> |  | **位置/serverAdministratorOperationResults** |  |
> | 動作 | Microsoft .Sql/位置/serverAdministratorOperationResults/讀取 | 伺服器 Azure Active Directory 系統管理員操作結果 |
> |  | **位置/serverKeyAzureAsyncOperation** |  |
> | 動作 | Microsoft .Sql/位置/serverKeyAzureAsyncOperation/讀取 | 取得透明資料加密伺服器金鑰的進行中作業 |
> |  | **位置/serverKeyOperationResults** |  |
> | 動作 | Microsoft .Sql/位置/serverKeyOperationResults/讀取 | 取得透明資料加密伺服器金鑰的進行中作業 |
> |  | **位置/shortTermRetentionPolicyOperationResults** |  |
> | 動作 | Microsoft .Sql/位置/shortTermRetentionPolicyOperationResults/讀取 | 取得短期保留原則操作的狀態 |
> |  | **位置/syncAgentOperationResults** |  |
> | 動作 | Microsoft.Sql/locations/syncAgentOperationResults/read | 擷取同步代理程式資源作業的結果 |
> |  | **位置/syncDatabaseIds** |  |
> | 動作 | Microsoft.Sql/locations/syncDatabaseIds/read | 擷取特定區域和訂用帳戶的同步資料庫識別碼 |
> |  | **位置/syncGroupOperationResults** |  |
> | 動作 | Microsoft.Sql/locations/syncGroupOperationResults/read | 擷取同步群組資源作業的結果 |
> |  | **位置/syncMemberOperationResults** |  |
> | 動作 | Microsoft.Sql/locations/syncMemberOperationResults/read | 擷取同步成員資源作業的結果 |
> |  | **位置/transparentDataEncryptionAzureAsyncOperation** |  |
> | 動作 | Microsoft .Sql/位置/transparentDataEncryptionAzureAsyncOperation/讀取 | 取得邏輯資料庫透明資料加密的進行中作業 |
> |  | **位置/transparentDataEncryptionOperationResults** |  |
> | 動作 | Microsoft .Sql/位置/transparentDataEncryptionOperationResults/讀取 | 取得邏輯資料庫透明資料加密的進行中作業 |
> |  | **位置/使用方式** |  |
> | 動作 | Microsoft.Sql/locations/usages/read | 在位置中取得此訂用帳戶的使用計量集合 |
> |  | **位置/virtualNetworkRulesAzureAsyncOperation** |  |
> | 動作 | Microsoft.Sql/locations/virtualNetworkRulesAzureAsyncOperation/read | 傳回指定虛擬網路規則 Aure 非同步作業的詳細資料  |
> |  | **位置/virtualNetworkRulesOperationResults** |  |
> | 動作 | Microsoft.Sql/locations/virtualNetworkRulesOperationResults/read | 傳回指定虛擬網路規則作業的詳細資料  |
> |  | **managedInstances** |  |
> | 動作 | Microsoft.Sql/managedInstances/tdeCertificates/action | 建立/更新 TDE 憑證 |
> | 動作 | Microsoft.Sql/managedInstances/read | 傳回受控執行個體的清單，或取得指定受控執行個體的屬性。 |
> | 動作 | Microsoft.Sql/managedInstances/write | 使用指定參數建立受控執行個體，或更新指定受控執行個體的屬性或標記。 |
> | 動作 | Microsoft.Sql/managedInstances/delete | 刪除現有的受控執行個體。 |
> |  | **managedInstances/系統管理員** |  |
> | 動作 | Microsoft.Sql/managedInstances/administrators/read | 取得受控執行個體系統管理員的清單。 |
> | 動作 | Microsoft.Sql/managedInstances/administrators/write | 使用指定參數，建立或更新受控執行個體的系統管理員。 |
> | 動作 | Microsoft.Sql/managedInstances/administrators/delete | 刪除受控執行個體的現有系統管理員。 |
> |  | **managedInstances/資料庫** |  |
> | 動作 | Microsoft.Sql/managedInstances/databases/read | 取得現有的受控資料庫 |
> | 動作 | Microsoft.Sql/managedInstances/databases/delete | 刪除現有的受控資料庫 |
> | 動作 | Microsoft.Sql/managedInstances/databases/write | 建立新的資料庫或更新現有資料庫。 |
> | 動作 | Microsoft .Sql/managedInstances/資料庫/completeRestore/action | 完成受控資料庫還原作業 |
> |  | **managedInstances/資料庫/backupLongTermRetentionPolicies** |  |
> | 動作 | Microsoft .Sql/managedInstances/資料庫/backupLongTermRetentionPolicies/write | 更新受控資料庫的長期保留原則 |
> | 動作 | Microsoft .Sql/managedInstances/資料庫/backupLongTermRetentionPolicies/read | 取得受控資料庫的長期保留原則 |
> |  | **managedInstances/資料庫/backupShortTermRetentionPolicies** |  |
> | 動作 | Microsoft.Sql/managedInstances/databases/backupShortTermRetentionPolicies/read | 取得受控資料庫中的短期保留原則 |
> | 動作 | Microsoft.Sql/managedInstances/databases/backupShortTermRetentionPolicies/write | 更新受控資料庫中的短期保留原則 |
> |  | **managedInstances/資料庫/資料行** |  |
> | 動作 | Microsoft .Sql/managedInstances/資料庫/資料行/讀取 | 傳回受控資料庫的資料行清單 |
> |  | **managedInstances/資料庫/currentSensitivityLabels** |  |
> | 動作 | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/read | 列出指定資料庫的敏感度標籤 |
> | 動作 | Microsoft .Sql/managedInstances/資料庫/currentSensitivityLabels/write | 批次更新敏感度標籤 |
> |  | **managedInstances/資料庫/提供者/Microsoft Insights/diagnosticSettings** |  |
> | 動作 | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/diagnosticSettings/read | 取得資源的診斷設定 |
> | 動作 | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/diagnosticSettings/write | 建立或更新資源的診斷設定 |
> |  | **managedInstances/資料庫/提供者/Microsoft Insights/logDefinitions** |  |
> | 動作 | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/logDefinitions/read | 取得受控執行個體資料庫的可用記錄 |
> |  | **managedInstances/資料庫/recommendedSensitivityLabels** |  |
> | 動作 | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/read | 列出指定資料庫的敏感度標籤 |
> | 動作 | Microsoft .Sql/managedInstances/資料庫/recommendedSensitivityLabels/write | 批次更新建議的敏感度標籤 |
> |  | **managedInstances/資料庫/restoreDetails** |  |
> | 動作 | Microsoft .Sql/managedInstances/資料庫/restoreDetails/read | 還原進行時傳回受控資料庫還原詳細資料。 |
> |  | **managedInstances/資料庫/架構** |  |
> | 動作 | Microsoft .Sql/managedInstances/資料庫/架構/讀取 | 取得受控資料庫架構。 |
> |  | **managedInstances/資料庫/架構/資料表** |  |
> | 動作 | Microsoft .Sql/managedInstances/資料庫/架構/資料表/讀取 | 取得受控資料庫資料表 |
> |  | **managedInstances/資料庫/架構/資料表/資料行** |  |
> | 動作 | Microsoft .Sql/managedInstances/資料庫/架構/資料表/資料行/讀取 | 取得受控資料庫資料行 |
> |  | **managedInstances/資料庫/架構/資料表/資料行/sensitivityLabels** |  |
> | 動作 | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/read | 取得指定資料行的敏感度標籤 |
> | 動作 | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/write | 建立或更新指定資料行的敏感度標籤 |
> | 動作 | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/delete | 刪除指定資料行的敏感度標籤 |
> | 動作 | Microsoft .Sql/managedInstances/資料庫/架構/資料表/資料行/sensitivityLabels/停用/動作 | 停用指定資料行的敏感度建議 |
> | 動作 | Microsoft .Sql/managedInstances/資料庫/架構/資料表/資料行/sensitivityLabels/啟用/動作 | 在指定的資料行上啟用敏感性建議 |
> |  | **managedInstances/資料庫/securityAlertPolicies** |  |
> | 動作 | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/write | 變更指定受控資料庫的資料庫威脅偵測原則 |
> | 動作 | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/read | 抓取為給定伺服器設定的受控資料庫威脅偵測原則清單 |
> |  | **managedInstances/資料庫/securityEvents** |  |
> | 動作 | Microsoft.Sql/managedInstances/databases/securityEvents/read | 擷取受控資料庫的安全性事件 |
> |  | **managedInstances/資料庫/sensitivityLabels** |  |
> | 動作 | Microsoft.Sql/managedInstances/databases/sensitivityLabels/read | 列出指定資料庫的敏感度標籤 |
> |  | **managedInstances/資料庫/transparentDataEncryption** |  |
> | 動作 | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/read | 擷取指定受控資料庫上資料庫透明資料加密的詳細資料 |
> | 動作 | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/write | 變更指定受控資料庫的資料庫透明資料加密 |
> |  | **managedInstances/資料庫/vulnerabilityAssessments** |  |
> | 動作 | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/write | 變更指定資料庫的弱點評量 |
> | 動作 | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/delete | 移除指定資料庫的弱點評量 |
> | 動作 | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/read | 擷取指定資料庫上的弱點評量原則 |
> |  | **managedInstances/資料庫/vulnerabilityAssessments/規則/基準** |  |
> | 動作 | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/delete | 移除指定資料庫的弱點評量規則基準 |
> | 動作 | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/write | 變更指定資料庫的弱點評量規則基準 |
> | 動作 | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/read | 取得指定資料庫的弱點評量規則基準 |
> |  | **managedInstances/資料庫/vulnerabilityAssessments/掃描** |  |
> | 動作 | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/initiateScan/action | 執行弱點評估資料庫掃描。 |
> | 動作 | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/export/action | 將現有的掃描結果轉換成人類可讀取的格式。 如果已存在，則不會執行任何動作 |
> | 動作 | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/read | 傳回資料庫弱點評量掃描記錄的清單，或取得指定掃描識別碼的掃描記錄。 |
> |  | **managedInstances/encryptionProtector** |  |
> | 動作 | Microsoft .Sql/managedInstances/encryptionProtector/重新驗證/動作 | 更新指定伺服器加密保護裝置的屬性。 |
> | 動作 | Microsoft.Sql/managedInstances/encryptionProtector/read | 傳回伺服器加密保護裝置的清單，或取得指定伺服器加密保護裝置的屬性。 |
> | 動作 | Microsoft.Sql/managedInstances/encryptionProtector/write | 更新指定伺服器加密保護裝置的屬性。 |
> |  | **managedInstances/inaccessibleManagedDatabases** |  |
> | 動作 | ManagedInstances/inaccessibleManagedDatabases/read | 取得受控實例中無法存取的受控資料庫清單 |
> |  | **managedInstances/金鑰** |  |
> | 動作 | Microsoft.Sql/managedInstances/keys/read | 傳回受控執行個體金鑰的清單，或取得指定受控執行個體金鑰的屬性。 |
> | 動作 | Microsoft.Sql/managedInstances/keys/write | 使用指定參數建立金鑰，或更新指定受控執行個體金鑰的屬性或標記。 |
> | 動作 | Microsoft.Sql/managedInstances/keys/delete | 刪除現有的 Azure SQL 受控執行個體金鑰。 |
> |  | **managedInstances/Metricdefinitions.listasync** |  |
> | 動作 | Microsoft.Sql/managedInstances/metricDefinitions/read | 取得受控執行個體的計量定義 |
> |  | **managedInstances/計量** |  |
> | 動作 | Microsoft.Sql/managedInstances/metrics/read | 取得受控執行個體計量 |
> |  | **managedInstances/作業** |  |
> | 動作 | Microsoft .Sql/managedInstances/作業/讀取 | 取得受控實例作業 |
> | 動作 | Microsoft .Sql/managedInstances/作業/取消/動作 | 取消尚未完成的 Azure SQL 受控執行個體暫止非同步作業。 |
> |  | **managedInstances/providers/Microsoft Insights/diagnosticSettings** |  |
> | 動作 | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/diagnosticSettings/read | 取得資源的診斷設定 |
> | 動作 | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/diagnosticSettings/write | 建立或更新資源的診斷設定 |
> |  | **managedInstances/providers/Microsoft Insights/logDefinitions** |  |
> | 動作 | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/logDefinitions/read | 取得受控執行個體的可用記錄 |
> |  | **managedInstances/providers/Microsoft Insights/Metricdefinitions.listasync** |  |
> | 動作 | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/metricDefinitions/read | 傳回可供受控執行個體使用的計量類型 |
> |  | **managedInstances/recoverableDatabases** |  |
> | 動作 | Microsoft.Sql/managedInstances/recoverableDatabases/read | 傳回可還原的受控資料庫清單 |
> |  | **managedInstances/restorableDroppedDatabases** |  |
> | 動作 | Microsoft.Sql/managedInstances/restorableDroppedDatabases/read | 傳回可還原的已卸除受控資料庫清單。 |
> |  | **managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies** |  |
> | 動作 | Microsoft.Sql/managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies/read | 取得已卸除之受控資料庫中的短期保留原則 |
> | 動作 | Microsoft.Sql/managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies/write | 更新已卸除之受控資料庫中的短期保留原則 |
> |  | **managedInstances/securityAlertPolicies** |  |
> | 動作 | Microsoft.Sql/managedInstances/securityAlertPolicies/write | 變更指定受控伺服器的受控伺服器威脅偵測原則 |
> | 動作 | Microsoft.Sql/managedInstances/securityAlertPolicies/read | 抓取為給定伺服器設定的受管理伺服器威脅偵測原則清單 |
> |  | **managedInstances/vulnerabilityAssessments** |  |
> | 動作 | Microsoft.Sql/managedInstances/vulnerabilityAssessments/write | 變更指定受控執行個體的弱點評量 |
> | 動作 | Microsoft.Sql/managedInstances/vulnerabilityAssessments/delete | 移除指定受控執行個體的弱點評量 |
> | 動作 | Microsoft.Sql/managedInstances/vulnerabilityAssessments/read | 擷取指定受控執行個體上的弱點評量原則 |
> |  | **業務** |  |
> | 動作 | Microsoft.Sql/operations/read | 取得可用的 REST 作業 |
> |  | **伺服器** |  |
> | 動作 | Microsoft.Sql/servers/tdeCertificates/action | 建立/更新 TDE 憑證 |
> | 動作 | Microsoft .Sql/servers/disableAzureADOnlyAuthentication/action | 停用只 Azure Active Directory 邏輯伺服器上的驗證 |
> | 動作 | Microsoft.Sql/servers/read | 傳回伺服器清單，或取得指定伺服器的屬性。 |
> | 動作 | Microsoft.Sql/servers/write | 使用指定參數建立伺服器，或更新指定伺服器的屬性或標記。 |
> | 動作 | Microsoft.Sql/servers/delete | 刪除現有伺服器。 |
> | 動作 | Microsoft .Sql/servers/privateEndpointConnectionsApproval/action | 決定是否允許使用者核准私用端點連接 |
> | 動作 | Microsoft.Sql/servers/import/action | 在伺服器上建立新的資料庫，並部署來自 DacPac 套件的結構描述和資料 |
> |  | **伺服器/administratorOperationResults** |  |
> | 動作 | Microsoft.Sql/servers/administratorOperationResults/read | 取得關於伺服器系統管理員的進行中作業 |
> |  | **伺服器/系統管理員** |  |
> | 動作 | Microsoft.Sql/servers/administrators/read | 取得特定 Azure Active Directory 系統管理員物件 |
> | 動作 | Microsoft.Sql/servers/administrators/write | 新增或更新特定 Azure Active Directory 系統管理員物件 |
> | 動作 | Microsoft.Sql/servers/administrators/delete | 刪除特定的 Azure Active Directory 系統管理員物件 |
> |  | **伺服器/顧問** |  |
> | 動作 | Microsoft.Sql/servers/advisors/read | 傳回伺服器可用 Advisor 的清單 |
> | 動作 | Microsoft.Sql/servers/advisors/write | 更新伺服器層級上 Advisor 的自動執行狀態。 |
> |  | **伺服器/顧問/recommendedActions** |  |
> | 動作 | Microsoft.Sql/servers/advisors/recommendedActions/read | 傳回伺服器之指定 Advisor 的建議動作清單 |
> | 動作 | Microsoft.Sql/servers/advisors/recommendedActions/write | 對伺服器套用建議動作 |
> |  | **伺服器/auditingPolicies** |  |
> | 動作 | Microsoft.Sql/servers/auditingPolicies/read | 擷取給定伺服器上所設定之預設伺服器資料表稽核原則的詳細資料 |
> | 動作 | Microsoft.Sql/servers/auditingPolicies/write | 變更給定伺服器的預設伺服器資料表稽核 |
> |  | **伺服器/auditingSettings** |  |
> | 動作 | Microsoft.Sql/servers/auditingSettings/read | 擷取給定伺服器上所設定之伺服器 Blob 稽核原則的詳細資料 |
> | 動作 | Microsoft.Sql/servers/auditingSettings/write | 變更給定伺服器的伺服器 Blob 稽核 |
> |  | **servers/auditingSettings/operationResults** |  |
> | 動作 | Microsoft.Sql/servers/auditingSettings/operationResults/read | 擷取伺服器的「Blob 稽核原則設定」作業結果 |
> |  | **伺服器/automaticTuning** |  |
> | 動作 | Microsoft.Sql/servers/automaticTuning/read | 傳回伺服器的自動調整設定 |
> | 動作 | Microsoft.Sql/servers/automaticTuning/write | 更新伺服器的自動調整設定，並傳回更新的設定 |
> |  | **伺服器/backupLongTermRetentionVaults** |  |
> | 動作 | Microsoft.Sql/servers/backupLongTermRetentionVaults/read | 這項作業可用來取得備用的長期保留保存庫。 它會傳回向此伺服器註冊之保存庫的相關資訊 |
> | 動作 | Microsoft.Sql/servers/backupLongTermRetentionVaults/write | 此作業可用來向伺服器註冊備份的長期保留保存庫 |
> | 動作 | Microsoft.Sql/servers/backupLongTermRetentionVaults/delete | 刪除現有的備份封存保存庫。 |
> |  | **伺服器/communicationLinks** |  |
> | 動作 | Microsoft.Sql/servers/communicationLinks/read | 傳回指定伺服器的通訊連結清單。 |
> | 動作 | Microsoft.Sql/servers/communicationLinks/write | 建立或更新伺服器通訊連結。 |
> | 動作 | Microsoft.Sql/servers/communicationLinks/delete | 刪除現有的伺服器通訊連結。 |
> |  | **伺服器/connectionPolicies** |  |
> | 動作 | Microsoft.Sql/servers/connectionPolicies/read | 傳回指定伺服器的伺服器連線原則清單。 |
> | 動作 | Microsoft.Sql/servers/connectionPolicies/write | 建立或更新伺服器連線原則。 |
> |  | **伺服器/資料庫** |  |
> | 動作 | Microsoft.Sql/servers/databases/read | 傳回資料庫清單，或取得指定資料庫的屬性。 |
> | 動作 | Microsoft.Sql/servers/databases/write | 使用指定參數建立資料庫，或更新指定資料庫的屬性或標記。 |
> | 動作 | Microsoft.Sql/servers/databases/delete | 刪除現有的資料庫。 |
> | 動作 | Microsoft.Sql/servers/databases/pause/action | 暫停 Azure SQL 資料倉儲資料庫 |
> | 動作 | Microsoft.Sql/servers/databases/resume/action | 繼續 Azure SQL 資料倉儲資料庫 |
> | 動作 | Microsoft.Sql/servers/databases/export/action | 匯出 Azure SQL Database |
> | 動作 | Microsoft.Sql/servers/databases/upgradeDataWarehouse/action | 將 Azure SQL 資料倉儲資料庫升級 |
> | 動作 | Microsoft.Sql/servers/databases/move/action | 變更現有資料庫的名稱。 |
> | 動作 | Microsoft.Sql/servers/databases/restorePoints/action | 建立新的還原點 |
> | 動作 | Microsoft .Sql/伺服器/資料庫/容錯移轉/動作 | 客戶起始的資料庫容錯移轉。 |
> | 動作 | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/action | 執行弱點評估資料庫掃描。 |
> |  | **伺服器/資料庫/顧問** |  |
> | 動作 | Microsoft.Sql/servers/databases/advisors/read | 傳回資料庫可用 Advisor 的清單 |
> | 動作 | Microsoft.Sql/servers/databases/advisors/write | 更新資料庫層級上 Advisor 的自動執行狀態。 |
> |  | **伺服器/資料庫/顧問/recommendedActions** |  |
> | 動作 | Microsoft.Sql/servers/databases/advisors/recommendedActions/read | 傳回資料庫之指定 Advisor 的建議動作清單 |
> | 動作 | Microsoft.Sql/servers/databases/advisors/recommendedActions/write | 對資料庫套用建議動作 |
> |  | **伺服器/資料庫/auditingPolicies** |  |
> | 動作 | Microsoft.Sql/servers/databases/auditingPolicies/read | 擷取給定資料庫上所設定之資料表稽核原則的詳細資料 |
> | 動作 | Microsoft.Sql/servers/databases/auditingPolicies/write | 變更給定資料庫的資料表稽核原則 |
> |  | **伺服器/資料庫/auditingSettings** |  |
> | 動作 | Microsoft.Sql/servers/databases/auditingSettings/read | 擷取給定資料庫上所設定之 Blob 稽核原則的詳細資料 |
> | 動作 | Microsoft.Sql/servers/databases/auditingSettings/write | 變更給定資料庫的 Blob 稽核原則 |
> |  | **伺服器/資料庫/auditRecords** |  |
> | 動作 | Microsoft.Sql/servers/databases/auditRecords/read | 擷取資料庫 Blob 稽核記錄 |
> |  | **伺服器/資料庫/automaticTuning** |  |
> | 動作 | Microsoft.Sql/servers/databases/automaticTuning/read | 傳回資料庫的自動調整設定 |
> | 動作 | Microsoft.Sql/servers/databases/automaticTuning/write | 更新資料庫的自動調整設定，並傳回更新的設定 |
> |  | **伺服器/資料庫/azureAsyncOperation** |  |
> | 動作 | Microsoft.Sql/servers/databases/azureAsyncOperation/read | 取得資料庫作業的狀態。 |
> |  | **伺服器/資料庫/backupLongTermRetentionPolicies** |  |
> | 動作 | Microsoft.Sql/servers/databases/backupLongTermRetentionPolicies/write | 建立或更新資料庫備份封存原則。 |
> | 動作 | Microsoft.Sql/servers/databases/backupLongTermRetentionPolicies/read | 傳回指定資料庫的備份封存原則清單。 |
> |  | **伺服器/資料庫/backupShortTermRetentionPolicies** |  |
> | 動作 | Microsoft .Sql/servers/資料庫/backupShortTermRetentionPolicies/read | 取得資料庫的短期保留原則 |
> | 動作 | Microsoft .Sql/servers/資料庫/backupShortTermRetentionPolicies/write | 更新資料庫的短期保留原則 |
> |  | **伺服器/資料庫/資料行** |  |
> | 動作 | Microsoft .Sql/伺服器/資料庫/資料行/讀取 | 傳回資料庫的資料行清單 |
> |  | **伺服器/資料庫/connectionPolicies** |  |
> | 動作 | Microsoft.Sql/servers/databases/connectionPolicies/read | 擷取給定資料庫上所設定的連線原則詳細資料 |
> | 動作 | Microsoft.Sql/servers/databases/connectionPolicies/write | 變更給定資料庫的連線原則 |
> |  | **伺服器/資料庫/currentSensitivityLabels** |  |
> | 動作 | Microsoft.Sql/servers/databases/currentSensitivityLabels/read | 列出指定資料庫的敏感度標籤 |
> | 動作 | Microsoft .Sql/servers/資料庫/currentSensitivityLabels/write | 批次更新敏感度標籤 |
> |  | **伺服器/資料庫/dataMaskingPolicies** |  |
> | 動作 | Microsoft.Sql/servers/databases/dataMaskingPolicies/read | 傳回資料庫資料遮罩原則的清單。 |
> | 動作 | Microsoft.Sql/servers/databases/dataMaskingPolicies/write | 變更給定伺服器的資料遮罩原則 |
> |  | **伺服器/資料庫/dataMaskingPolicies/規則** |  |
> | 動作 | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/read | 擷取給定資料庫上所設定之資料遮罩原則規則的詳細資料 |
> | 動作 | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/write | 變更給定伺服器的資料遮罩原則規則 |
> | 動作 | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/delete | 刪除指定資料庫的資料遮罩原則規則 |
> |  | **伺服器/資料庫/dataWarehouseQueries** |  |
> | 動作 | Microsoft.Sql/servers/databases/dataWarehouseQueries/read | 傳回所選查詢識別碼的資料倉儲散發查詢資訊 |
> |  | **伺服器/資料庫/dataWarehouseQueries/dataWarehouseQuerySteps** |  |
> | 動作 | Microsoft.Sql/servers/databases/dataWarehouseQueries/dataWarehouseQuerySteps/read | 傳回所選步驟識別碼之資料倉儲查詢的分散式查詢步驟資訊 |
> |  | **伺服器/資料庫/dataWarehouseUserActivities** |  |
> | 動作 | Microsoft.Sql/servers/databases/dataWarehouseUserActivities/read | 擷取 SQL 資料倉儲執行個體的使用者活動，其中包含執行中和暫止的查詢 |
> |  | **伺服器/資料庫/extendedAuditingSettings** |  |
> | 動作 | Microsoft.Sql/servers/databases/extendedAuditingSettings/read | 擷取指定資料庫上所設定之擴充 Blob 稽核原則的詳細資料 |
> | 動作 | Microsoft.Sql/servers/databases/extendedAuditingSettings/write | 變更指定資料庫的擴充 Blob 稽核原則 |
> |  | **伺服器/資料庫/擴充功能** |  |
> | 動作 | Microsoft.Sql/servers/databases/extensions/read | 取得資料庫的擴充功能集合。 |
> | 動作 | Microsoft.Sql/servers/databases/extensions/write | 變更指定資料庫的擴充功能 |
> |  | **伺服器/資料庫/擴充功能/importExtensionOperationResults** |  |
> | 動作 | Microsoft .Sql/伺服器/資料庫/擴充功能/importExtensionOperationResults/讀取 | 取得進行中的匯入作業 |
> |  | **伺服器/資料庫/geoBackupPolicies** |  |
> | 動作 | Microsoft.Sql/servers/databases/geoBackupPolicies/read | 擷取指定資料庫的異地備份原則 |
> | 動作 | Microsoft.Sql/servers/databases/geoBackupPolicies/write | 建立或更新資料庫異地備份原則 |
> |  | **伺服器/資料庫/importExportOperationResults** |  |
> | 動作 | Microsoft.Sql/servers/databases/importExportOperationResults/read | 取得進行中的匯入/匯出作業 |
> |  | **伺服器/資料庫/maintenanceWindowOptions** |  |
> | 動作 | Microsoft.Sql/servers/databases/maintenanceWindowOptions/read | 取得所選資料庫的可用維護時間範圍清單。 |
> |  | **伺服器/資料庫/maintenanceWindows** |  |
> | 動作 | Microsoft.Sql/servers/databases/maintenanceWindows/read | 取得所選資料庫的維護時間範圍設定。 |
> | 動作 | Microsoft.Sql/servers/databases/maintenanceWindows/write | 設定所選資料庫的維護時間範圍設定。 |
> |  | **伺服器/資料庫/Metricdefinitions.listasync** |  |
> | 動作 | Microsoft.Sql/servers/databases/metricDefinitions/read | 傳回可供資料庫使用之計量的類型 |
> |  | **伺服器/資料庫/計量** |  |
> | 動作 | Microsoft.Sql/servers/databases/metrics/read | 傳回資料庫的計量 |
> |  | **伺服器/資料庫/operationResults** |  |
> | 動作 | Microsoft.Sql/servers/databases/operationResults/read | 取得資料庫作業的狀態。 |
> |  | **伺服器/資料庫/作業** |  |
> | 動作 | Microsoft.Sql/servers/databases/operations/cancel/action | 取消尚未完成的 Azure SQL Database 暫止非同步作業。 |
> | 動作 | Microsoft.Sql/servers/databases/operations/read | 傳回在資料庫上執行的作業清單 |
> |  | **伺服器/資料庫/提供者/Microsoft Insights/diagnosticSettings** |  |
> | 動作 | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/diagnosticSettings/read | 取得資源的診斷設定 |
> | 動作 | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/diagnosticSettings/write | 建立或更新資源的診斷設定 |
> |  | **伺服器/資料庫/提供者/Microsoft Insights/logDefinitions** |  |
> | 動作 | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/logDefinitions/read | 取得資料庫的可用記錄 |
> |  | **伺服器/資料庫/提供者/Microsoft Insights/Metricdefinitions.listasync** |  |
> | 動作 | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/metricDefinitions/read | 傳回可供資料庫使用之計量的類型 |
> |  | **伺服器/資料庫/queryStore** |  |
> | 動作 | Microsoft.Sql/servers/databases/queryStore/read | 傳回資料庫之查詢存放區設定目前的值。 |
> | 動作 | Microsoft.Sql/servers/databases/queryStore/write | 更新資料庫的查詢存放區設定 |
> |  | **伺服器/資料庫/queryStore/queryTexts** |  |
> | 動作 | Microsoft.Sql/servers/databases/queryStore/queryTexts/read | 傳回對應至指定參數的查詢文字集合。 |
> |  | **伺服器/資料庫/recommendedSensitivityLabels** |  |
> | 動作 | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/read | 列出指定資料庫的敏感度標籤 |
> | 動作 | Microsoft .Sql/servers/資料庫/recommendedSensitivityLabels/write | 批次更新建議的敏感度標籤 |
> |  | **伺服器/資料庫/replicationLinks** |  |
> | 動作 | Microsoft.Sql/servers/databases/replicationLinks/read | 傳回複寫連結的清單，或取得指定複寫連結的屬性。 |
> | 動作 | Microsoft.Sql/servers/databases/replicationLinks/delete | 強制終止複寫關係，但可能遺失資料 |
> | 動作 | Microsoft.Sql/servers/databases/replicationLinks/failover/action | 從主要資料庫同步處理所有變更後進行容錯移轉，讓此資料庫變成複寫關係的主要資料庫，並讓遠端的主要資料庫變成次要資料庫 |
> | 動作 | Microsoft.Sql/servers/databases/replicationLinks/forceFailoverAllowDataLoss/action | 立即進行容錯移轉 (但可能遺失資料)，讓此資料庫變成複寫關係的主要資料庫，並讓遠端的主要資料庫變成次要資料庫 |
> | 動作 | Microsoft.Sql/servers/databases/replicationLinks/updateReplicationMode/action | 將連結的複寫模式更新為同步或非同步模式 |
> | 動作 | Microsoft.Sql/servers/databases/replicationLinks/unlink/action | 強制終止複寫關係，或在與合作夥伴進行同步處理之後終止 |
> |  | **伺服器/資料庫/restorePoints** |  |
> | 動作 | Microsoft.Sql/servers/databases/restorePoints/read | 傳回資料庫的還原點。 |
> | 動作 | Microsoft.Sql/servers/databases/restorePoints/delete | 刪除資料庫的還原點。 |
> |  | **伺服器/資料庫/架構** |  |
> | 動作 | Microsoft.Sql/servers/databases/schemas/read | 取得資料庫架構。 |
> |  | **伺服器/資料庫/架構/資料表** |  |
> | 動作 | Microsoft.Sql/servers/databases/schemas/tables/read | 取得資料庫資料表。 |
> |  | **伺服器/資料庫/架構/資料表/資料行** |  |
> | 動作 | Microsoft.Sql/servers/databases/schemas/tables/columns/read | 取得資料庫資料行。 |
> |  | **伺服器/資料庫/架構/資料表/資料行/sensitivityLabels** |  |
> | 動作 | Microsoft .Sql/servers/資料庫/架構/資料表/資料行/sensitivityLabels/啟用/動作 | 在指定的資料行上啟用敏感性建議 |
> | 動作 | Microsoft .Sql/servers/資料庫/架構/資料表/資料行/sensitivityLabels/停用/動作 | 停用指定資料行的敏感度建議 |
> | 動作 | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/read | 取得指定資料行的敏感度標籤 |
> | 動作 | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/write | 建立或更新指定資料行的敏感度標籤 |
> | 動作 | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/delete | 刪除指定資料行的敏感度標籤 |
> |  | **伺服器/資料庫/架構/資料表/recommendedIndexes** |  |
> | 動作 | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/read | 擷取資料庫上的索引建議清單 |
> | 動作 | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/write | 套用索引建議 |
> |  | **伺服器/資料庫/securityAlertPolicies** |  |
> | 動作 | Microsoft.Sql/servers/databases/securityAlertPolicies/write | 變更指定資料庫的資料庫威脅偵測原則 |
> | 動作 | Microsoft.Sql/servers/databases/securityAlertPolicies/read | 抓取為給定伺服器設定的資料庫威脅偵測原則清單 |
> |  | **伺服器/資料庫/securityMetrics** |  |
> | 動作 | Microsoft.Sql/servers/databases/securityMetrics/read | 取得資料庫安全性計量的集合 |
> |  | **伺服器/資料庫/sensitivityLabels** |  |
> | 動作 | Microsoft.Sql/servers/databases/sensitivityLabels/read | 列出指定資料庫的敏感度標籤 |
> |  | **伺服器/資料庫/serviceTierAdvisors** |  |
> | 動作 | Microsoft.Sql/servers/databases/serviceTierAdvisors/read | 傳回相關建議，以讓您根據查詢執行統計資料來相應增加或減少資料庫，以提升效能或降低成本 |
> |  | **伺服器/資料庫/sku** |  |
> | 動作 | Microsoft.Sql/servers/databases/skus/read | 取得可供資料庫使用的 SKU 集合 |
> |  | **伺服器/資料庫/syncGroups** |  |
> | 動作 | Microsoft.Sql/servers/databases/syncGroups/refreshHubSchema/action | 重新整理同步中樞資料庫結構描述 |
> | 動作 | Microsoft.Sql/servers/databases/syncGroups/cancelSync/action | 取消同步群組同步處理 |
> | 動作 | Microsoft.Sql/servers/databases/syncGroups/triggerSync/action | 觸發同步群組同步處理 |
> | 動作 | Microsoft.Sql/servers/databases/syncGroups/read | 傳回同步群組的清單，或取得指定同步群組的屬性。 |
> | 動作 | Microsoft.Sql/servers/databases/syncGroups/write | 使用指定參數建立同步群組，或更新指定同步群組的屬性。 |
> | 動作 | Microsoft.Sql/servers/databases/syncGroups/delete | 刪除現有的同步群組。 |
> |  | **伺服器/資料庫/syncGroups/hubSchemas** |  |
> | 動作 | Microsoft.Sql/servers/databases/syncGroups/hubSchemas/read | 傳回同步中樞資料庫結構描述的清單 |
> |  | **伺服器/資料庫/syncGroups/記錄** |  |
> | 動作 | Microsoft.Sql/servers/databases/syncGroups/logs/read | 傳回同步群組記錄的清單 |
> |  | **伺服器/資料庫/syncGroups/refreshHubSchemaOperationResults** |  |
> | 動作 | Microsoft.Sql/servers/databases/syncGroups/refreshHubSchemaOperationResults/read | 擷取同步中樞結構描述重新整理作業的結果 |
> |  | **伺服器/資料庫/syncGroups/syncMembers** |  |
> | 動作 | Microsoft.Sql/servers/databases/syncGroups/syncMembers/read | 傳回同步成員的清單，或取得指定同步成員的屬性。 |
> | 動作 | Microsoft.Sql/servers/databases/syncGroups/syncMembers/write | 使用指定參數建立同步成員，或更新指定同步成員的屬性。 |
> | 動作 | Microsoft.Sql/servers/databases/syncGroups/syncMembers/delete | 刪除現有的同步成員。 |
> | 動作 | Microsoft.Sql/servers/databases/syncGroups/syncMembers/refreshSchema/action | 重新整理同步成員結構描述 |
> |  | **伺服器/資料庫/syncGroups/syncMembers/refreshSchemaOperationResults** |  |
> | 動作 | Microsoft.Sql/servers/databases/syncGroups/syncMembers/refreshSchemaOperationResults/read | 擷取同步成員結構描述重新整理作業的結果 |
> |  | **伺服器/資料庫/syncGroups/syncMembers/架構** |  |
> | 動作 | Microsoft.Sql/servers/databases/syncGroups/syncMembers/schemas/read | 傳回同步成員資料庫結構描述的清單 |
> |  | **伺服器/資料庫/topQueries** |  |
> | 動作 | Microsoft.Sql/servers/databases/topQueries/queryText/action | 傳回所選查詢識別碼的 Transact-SQL 文字 |
> | 動作 | Microsoft.Sql/servers/databases/topQueries/read | 傳回所選查詢在所選時段內彙總的執行階段統計資料 |
> |  | **伺服器/資料庫/topQueries/統計資料** |  |
> | 動作 | Microsoft.Sql/servers/databases/topQueries/statistics/read | 傳回所選查詢在所選時段內彙總的執行階段統計資料 |
> |  | **伺服器/資料庫/transparentDataEncryption** |  |
> | 動作 | Microsoft.Sql/servers/databases/transparentDataEncryption/read | 在指定的受控資料庫上，取得邏輯資料庫透明資料加密的詳細資料 |
> | 動作 | Microsoft.Sql/servers/databases/transparentDataEncryption/write | 變更給定邏輯資料庫的資料庫透明資料加密 |
> |  | **伺服器/資料庫/transparentDataEncryption/operationResults** |  |
> | 動作 | Microsoft.Sql/servers/databases/transparentDataEncryption/operationResults/read | 取得有關透明資料加密的進行中作業 |
> |  | **伺服器/資料庫/使用方式** |  |
> | 動作 | Microsoft.Sql/servers/databases/usages/read | 取得 Azure SQL Database 的使用方式資訊 |
> |  | **伺服器/資料庫/vulnerabilityAssessments** |  |
> | 動作 | Microsoft.Sql/servers/databases/vulnerabilityAssessments/write | 變更指定資料庫的弱點評量 |
> | 動作 | Microsoft.Sql/servers/databases/vulnerabilityAssessments/delete | 移除指定資料庫的弱點評量 |
> | 動作 | Microsoft.Sql/servers/databases/vulnerabilityAssessments/read | 擷取指定資料庫上的弱點評量原則 |
> |  | **伺服器/資料庫/vulnerabilityAssessments/規則/基準** |  |
> | 動作 | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/delete | 移除指定資料庫的弱點評量規則基準 |
> | 動作 | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/write | 變更指定資料庫的弱點評量規則基準 |
> | 動作 | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/read | 取得指定資料庫的弱點評量規則基準 |
> |  | **伺服器/資料庫/vulnerabilityAssessments/掃描** |  |
> | 動作 | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/initiateScan/action | 執行弱點評估資料庫掃描。 |
> | 動作 | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/read | 傳回資料庫弱點評量掃描記錄的清單，或取得指定掃描識別碼的掃描記錄。 |
> | 動作 | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/export/action | 將現有的掃描結果轉換成人類可讀取的格式。 如果已存在，則不會執行任何動作 |
> |  | **伺服器/資料庫/vulnerabilityAssessmentScans/operationResults** |  |
> | 動作 | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/operationResults/read | 擷取資料庫弱點評量掃描執行作業的結果 |
> |  | **伺服器/資料庫/vulnerabilityAssessmentSettings** |  |
> | 動作 | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/read | 擷取指定資料庫上所設定之弱點評量的詳細資料 |
> | 動作 | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/write | 變更指定資料庫的弱點評量 |
> |  | **伺服器/資料庫/workloadGroups** |  |
> | 動作 | Microsoft .Sql/servers/資料庫/workloadGroups/read | 列出所選資料庫的工作負載群組。 |
> | 動作 | Microsoft .Sql/servers/資料庫/workloadGroups/write | 設定特定工作負載群組的屬性。 |
> | 動作 | Microsoft .Sql/servers/資料庫/workloadGroups/delete | 卸載特定的工作負載群組。 |
> |  | **伺服器/資料庫/workloadGroups/workloadClassifiers** |  |
> | 動作 | Microsoft .Sql/servers/資料庫/workloadGroups/workloadClassifiers/read | 列出所選取資料庫的工作負載分類器。 |
> | 動作 | Microsoft .Sql/servers/資料庫/workloadGroups/workloadClassifiers/write | 設定特定工作負載分類器的屬性。 |
> | 動作 | Microsoft .Sql/servers/資料庫/workloadGroups/workloadClassifiers/delete | 卸載特定的工作負載分類器。 |
> |  | **伺服器/進行 disasterrecoveryconfiguration** |  |
> | 動作 | Microsoft.Sql/servers/disasterRecoveryConfiguration/read | 取得包括此伺服器在內的災害復原設定集合 |
> | 動作 | Microsoft.Sql/servers/disasterRecoveryConfiguration/write | 變更伺服器的災害復原設定 |
> | 動作 | Microsoft.Sql/servers/disasterRecoveryConfiguration/delete | 刪除指定伺服器的現有災害復原設定 |
> | 動作 | Microsoft.Sql/servers/disasterRecoveryConfiguration/failover/action | 進行 DisasterRecoveryConfiguration 的容錯移轉 |
> | 動作 | Microsoft.Sql/servers/disasterRecoveryConfiguration/forceFailoverAllowDataLoss/action | 強制進行 DisasterRecoveryConfiguration 的容錯移轉 |
> |  | **伺服器/elasticPoolEstimates** |  |
> | 動作 | Microsoft.Sql/servers/elasticPoolEstimates/read | 傳回已針對此伺服器建立之彈性集區估計值的清單 |
> | 動作 | Microsoft.Sql/servers/elasticPoolEstimates/write | 針對所提供的資料庫清單建立新的彈性集區估計值 |
> |  | **伺服器/elasticPools** |  |
> | 動作 | Microsoft.Sql/servers/elasticPools/read | 擷取指定伺服器上彈性集區的詳細資料 |
> | 動作 | Microsoft.Sql/servers/elasticPools/write | 建立新的彈性集區，或變更現有彈性集區的屬性 |
> | 動作 | Microsoft.Sql/servers/elasticPools/delete | 刪除現有彈性集區 |
> | 動作 | Microsoft .Sql/servers/elasticPools/容錯移轉/動作 | 客戶已起始彈性集區容錯移轉。 |
> |  | **伺服器/elasticPools/顧問** |  |
> | 動作 | Microsoft.Sql/servers/elasticPools/advisors/read | 傳回彈性集區可用 Advisor 的清單 |
> | 動作 | Microsoft.Sql/servers/elasticPools/advisors/write | 更新彈性集區層級上 Advisor 的自動執行狀態。 |
> |  | **伺服器/elasticPools/顧問/recommendedActions** |  |
> | 動作 | Microsoft.Sql/servers/elasticPools/advisors/recommendedActions/read | 傳回彈性集區之指定 Advisor 的建議動作清單 |
> | 動作 | Microsoft.Sql/servers/elasticPools/advisors/recommendedActions/write | 對彈性集區套用建議動作 |
> |  | **伺服器/elasticPools/資料庫** |  |
> | 動作 | Microsoft.Sql/servers/elasticPools/databases/read | 取得彈性集區的資料庫清單 |
> |  | **servers/elasticPools/elasticPoolActivity** |  |
> | 動作 | Microsoft.Sql/servers/elasticPools/elasticPoolActivity/read | 擷取給定彈性資料庫集區的活動和詳細資料 |
> |  | **servers/elasticPools/elasticPoolDatabaseActivity** |  |
> | 動作 | Microsoft.Sql/servers/elasticPools/elasticPoolDatabaseActivity/read | 擷取屬於彈性資料庫集區之給定資料庫的活動和詳細資料 |
> |  | **servers/elasticPools/Metricdefinitions.listasync** |  |
> | 動作 | Microsoft.Sql/servers/elasticPools/metricDefinitions/read | 傳回可供彈性資料庫集區使用之計量的類型 |
> |  | **伺服器/elasticPools/計量** |  |
> | 動作 | Microsoft.Sql/servers/elasticPools/metrics/read | 傳回彈性資料庫集區的計量 |
> |  | **伺服器/elasticPools/作業** |  |
> | 動作 | Microsoft.Sql/servers/elasticPools/operations/cancel/action | 取消尚未完成的 Azure SQL 彈性集區暫止非同步作業。 |
> | 動作 | Microsoft.Sql/servers/elasticPools/operations/read | 傳回在彈性集區上執行的作業清單 |
> |  | **servers/elasticPools/provider/Microsoft Insights/diagnosticSettings** |  |
> | 動作 | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/read | 取得資源的診斷設定 |
> | 動作 | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/write | 建立或更新資源的診斷設定 |
> |  | **servers/elasticPools/provider/Microsoft Insights/Metricdefinitions.listasync** |  |
> | 動作 | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/metricDefinitions/read | 傳回可供彈性資料庫集區使用之計量的類型 |
> |  | **伺服器/elasticPools/sku** |  |
> | 動作 | Microsoft.Sql/servers/elasticPools/skus/read | 取得可供彈性集區使用的 SKU 集合 |
> |  | **伺服器/encryptionProtector** |  |
> | 動作 | Microsoft .Sql/伺服器/encryptionProtector/重新驗證/動作 | 更新指定伺服器加密保護裝置的屬性。 |
> | 動作 | Microsoft.Sql/servers/encryptionProtector/read | 傳回伺服器加密保護裝置的清單，或取得指定伺服器加密保護裝置的屬性。 |
> | 動作 | Microsoft.Sql/servers/encryptionProtector/write | 更新指定伺服器加密保護裝置的屬性。 |
> |  | **伺服器/extendedAuditingSettings** |  |
> | 動作 | Microsoft.Sql/servers/extendedAuditingSettings/read | 擷取指定伺服器上所設定之擴充伺服器 Blob 稽核原則的詳細資料 |
> | 動作 | Microsoft.Sql/servers/extendedAuditingSettings/write | 變更指定伺服器的擴充伺服器 Blob 稽核 |
> |  | **伺服器/failoverGroups** |  |
> | 動作 | Microsoft.Sql/servers/failoverGroups/read | 傳回容錯移轉群組的清單，或取得指定容錯移轉群組的屬性。 |
> | 動作 | Microsoft.Sql/servers/failoverGroups/write | 使用指定參數建立容錯移轉群組，或更新指定容錯移轉的屬性或標記。 |
> | 動作 | Microsoft.Sql/servers/failoverGroups/delete | 刪除現有的容錯移轉群組。 |
> | 動作 | Microsoft.Sql/servers/failoverGroups/failover/action | 在現有的容錯移轉群組中執行規劃的容錯移轉。 |
> | 動作 | Microsoft.Sql/servers/failoverGroups/forceFailoverAllowDataLoss/action | 在現有的容錯移轉群組中執行強制容錯移轉。 |
> |  | **伺服器/firewallRules** |  |
> | 動作 | Microsoft.Sql/servers/firewallRules/write | 使用指定參數建立伺服器防火牆規則、更新指定規則的屬性，或使用新的伺服器防火牆規則覆寫所有現有的規則。 |
> | 動作 | Microsoft.Sql/servers/firewallRules/read | 傳回伺服器防火牆規則的清單，或取得指定伺服器防火牆規則的屬性。 |
> | 動作 | Microsoft.Sql/servers/firewallRules/delete | 刪除現有的伺服器防火牆規則。 |
> |  | **伺服器/importExportOperationResults** |  |
> | 動作 | Microsoft.Sql/servers/importExportOperationResults/read | 取得進行中的匯入/匯出作業 |
> |  | **伺服器/inaccessibleDatabases** |  |
> | 動作 | Microsoft .Sql/servers/inaccessibleDatabases/read | 傳回邏輯伺服器中無法存取的資料庫清單。 |
> |  | **伺服器/interfaceEndpointProfiles** |  |
> | 動作 | Microsoft.Sql/servers/interfaceEndpointProfiles/write | 使用指定參數建立介面端點設定檔，或更新指定介面端點的屬性或標記 |
> | 動作 | Microsoft.Sql/servers/interfaceEndpointProfiles/read | 傳回指定介面端點設定檔的屬性 |
> | 動作 | Microsoft.Sql/servers/interfaceEndpointProfiles/delete | 刪除指定的介面端點設定檔 |
> |  | **伺服器/jobAgents** |  |
> | 動作 | Microsoft.Sql/servers/jobAgents/read | 取得 Azure SQL DB 工作代理程式 |
> | 動作 | Microsoft.Sql/servers/jobAgents/write | 建立或更新 Azure SQL DB 工作代理程式 |
> | 動作 | Microsoft.Sql/servers/jobAgents/delete | 刪除 Azure SQL DB 工作代理程式 |
> |  | **伺服器/金鑰** |  |
> | 動作 | Microsoft.Sql/servers/keys/read | 傳回伺服器金鑰的清單，或取得指定伺服器金鑰的屬性。 |
> | 動作 | Microsoft.Sql/servers/keys/write | 使用指定參數建立金鑰，或更新指定伺服器金鑰的屬性或標記。 |
> | 動作 | Microsoft.Sql/servers/keys/delete | 刪除現有的伺服器金鑰。 |
> |  | **伺服器/operationResults** |  |
> | 動作 | Microsoft.Sql/servers/operationResults/read | 取得進行中的伺服器作業 |
> |  | **伺服器/作業** |  |
> | 動作 | Microsoft .Sql/伺服器/作業/讀取 | 傳回伺服器上執行的作業清單 |
> |  | **伺服器/privateEndpointConnectionProxies** |  |
> | 動作 | Microsoft .Sql/伺服器/privateEndpointConnectionProxies/驗證/動作 | 驗證從 NRP 端的私用端點連接建立呼叫 |
> | 動作 | Microsoft .Sql/servers/privateEndpointConnectionProxies/read | 傳回私用端點連接 proxy 的清單，或取得指定之私用端點連接 proxy 的屬性。 |
> | 動作 | Microsoft .Sql/servers/privateEndpointConnectionProxies/write | 使用指定的參數建立私用端點連接 proxy，或更新指定之私用端點連接 proxy 的屬性或標記。 |
> | 動作 | Microsoft .Sql/servers/privateEndpointConnectionProxies/delete | 刪除現有的私用端點連接 proxy |
> |  | **伺服器/privateEndpointConnections** |  |
> | 動作 | Microsoft .Sql/servers/privateEndpointConnections/read | 傳回私用端點連接的清單，或取得指定之私用端點連接的屬性。 |
> | 動作 | Microsoft .Sql/servers/privateEndpointConnections/delete | 刪除現有的私用端點連接 |
> | 動作 | Microsoft .Sql/servers/privateEndpointConnections/write | 核准或拒絕現有的私用端點連接 |
> |  | **伺服器/privateLinkResources** |  |
> | 動作 | Microsoft .Sql/servers/privateLinkResources/read | 取得對應 sql server 的私用連結資源 |
> |  | **伺服器/提供者/Microsoft Insights/Metricdefinitions.listasync** |  |
> | 動作 | Microsoft.Sql/servers/providers/Microsoft.Insights/metricDefinitions/read | 傳回可供伺服器使用的計量類型 |
> |  | **伺服器/recommendedElasticPools** |  |
> | 動作 | Microsoft.Sql/servers/recommendedElasticPools/read | 擷取彈性資料庫集區的建議，以根據資源的歷史使用量來降低成本或改善效能 |
> |  | **伺服器/recommendedElasticPools/資料庫** |  |
> | 動作 | Microsoft.Sql/servers/recommendedElasticPools/databases/read | 擷取給定伺服器之建議彈性資料庫集區的計量 |
> |  | **伺服器/recoverableDatabases** |  |
> | 動作 | Microsoft.Sql/servers/recoverableDatabases/read | 這項作業可用於即時資料庫的災害復原，以將資料庫還原至最後一個已知良好的備份點。 它會傳回最後一個良好備份的相關資訊，但它實際上並不會還原資料庫。 |
> |  | **伺服器/replicationLinks** |  |
> | 動作 | Microsoft.Sql/servers/replicationLinks/read | 傳回複寫連結的清單，或取得指定複寫連結的屬性。 |
> |  | **伺服器/restorableDroppedDatabases** |  |
> | 動作 | Microsoft.Sql/servers/restorableDroppedDatabases/read | 取得指定伺服器上雖已捨棄但仍留在保留原則內的資料庫清單。 |
> |  | **伺服器/securityAlertPolicies** |  |
> | 動作 | Microsoft.Sql/servers/securityAlertPolicies/write | 變更指定伺服器的伺服器威脅偵測原則 |
> | 動作 | Microsoft.Sql/servers/securityAlertPolicies/read | 抓取為指定伺服器設定的伺服器威脅偵測原則清單 |
> |  | **servers/securityAlertPolicies/operationResults** |  |
> | 動作 | Microsoft.Sql/servers/securityAlertPolicies/operationResults/read | 擷取伺服器威脅偵測原則寫入作業的結果 |
> |  | **伺服器/serviceObjectives** |  |
> | 動作 | Microsoft.Sql/servers/serviceObjectives/read | 擷取給定伺服器上可用的服務等級目標 (也稱為效能層級) 清單 |
> |  | **伺服器/syncAgents** |  |
> | 動作 | Microsoft.Sql/servers/syncAgents/read | 傳回同步代理程式的清單，或取得指定同步代理程式的屬性。 |
> | 動作 | Microsoft.Sql/servers/syncAgents/write | 使用指定參數建立同步代理程式，或更新指定同步代理程式的屬性。 |
> | 動作 | Microsoft.Sql/servers/syncAgents/delete | 刪除現有的同步代理程式。 |
> | 動作 | Microsoft.Sql/servers/syncAgents/generateKey/action | 產生同步代理程式登錄金鑰 |
> |  | **servers/syncAgents/linkedDatabases** |  |
> | 動作 | Microsoft.Sql/servers/syncAgents/linkedDatabases/read | 傳回已連結同步代理程式的資料庫清單 |
> |  | **伺服器/使用方式** |  |
> | 動作 | Microsoft.Sql/servers/usages/read | 傳回伺服器中所有資料庫的伺服器 DTU 配額和目前 DTU 耗用量 |
> |  | **伺服器/virtualNetworkRules** |  |
> | 動作 | Microsoft.Sql/servers/virtualNetworkRules/read | 傳回虛擬網路規則的清單，或取得指定虛擬網路規則的屬性。 |
> | 動作 | Microsoft.Sql/servers/virtualNetworkRules/write | 使用指定參數建立虛擬網路規則，或更新指定虛擬網路規則的屬性或標記。 |
> | 動作 | Microsoft.Sql/servers/virtualNetworkRules/delete | 刪除現有虛擬網路規則 |
> |  | **伺服器/vulnerabilityAssessments** |  |
> | 動作 | Microsoft.Sql/servers/vulnerabilityAssessments/write | 變更指定伺服器的弱點評量 |
> | 動作 | Microsoft.Sql/servers/vulnerabilityAssessments/delete | 移除指定伺服器的弱點評量 |
> | 動作 | Microsoft.Sql/servers/vulnerabilityAssessments/read | 擷取指定伺服器上的弱點評量原則 |
> |  | **virtualClusters** |  |
> | 動作 | Microsoft.Sql/virtualClusters/read | 傳回虛擬叢集清單，或取得指定虛擬叢集的屬性。 |
> | 動作 | Microsoft.Sql/virtualClusters/write | 更新虛擬叢集標記。 |
> | 動作 | Microsoft .Sql/virtualClusters/delete | 刪除現有的虛擬叢集。 |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

Azure 服務： [azure 上的 SQL Server 虛擬機器](../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.sqlvirtualmachine/register/action | 向 Microsoft.sqlvirtualmachine 資源提供者註冊訂用帳戶 |
> | 動作 | Microsoft.sqlvirtualmachine/取消註冊/動作 | 向 Microsoft.sqlvirtualmachine 資源提供者取消註冊訂用帳戶 |
> |  | **位置/availabilityGroupListenerOperationResults** |  |
> | 動作 | Microsoft.sqlvirtualmachine/位置/availabilityGroupListenerOperationResults/讀取 | 取得可用性群組接聽程式作業的結果 |
> |  | **位置/sqlVirtualMachineGroupOperationResults** |  |
> | 動作 | Microsoft.sqlvirtualmachine/位置/sqlVirtualMachineGroupOperationResults/讀取 | 取得 SQL 虛擬機器群組作業的結果 |
> |  | **位置/sqlVirtualMachineOperationResults** |  |
> | 動作 | Microsoft.sqlvirtualmachine/位置/sqlVirtualMachineOperationResults/讀取 | 取得 SQL 虛擬機器作業的結果 |
> |  | **業務** |  |
> | 動作 | Microsoft.sqlvirtualmachine/作業/讀取 |  |
> |  | **sqlVirtualMachineGroups** |  |
> | 動作 | Microsoft.sqlvirtualmachine/sqlVirtualMachineGroups/read | 檢索 SQL 虛擬機器群組的詳細資料 |
> | 動作 | Microsoft.sqlvirtualmachine/sqlVirtualMachineGroups/write | 建立新的或變更現有 SQL 虛擬機器群組的屬性 |
> | 動作 | Microsoft.sqlvirtualmachine/sqlVirtualMachineGroups/delete | 刪除現有的 SQL 虛擬機器群組 |
> |  | **sqlVirtualMachineGroups/availabilityGroupListeners** |  |
> | 動作 | Microsoft.sqlvirtualmachine/sqlVirtualMachineGroups/availabilityGroupListeners/read | 在指定的 SQL 虛擬機器群組上，取得 SQL 可用性群組接聽程式的詳細資料 |
> | 動作 | Microsoft.sqlvirtualmachine/sqlVirtualMachineGroups/availabilityGroupListeners/write | 建立新的或變更現有 SQL 可用性群組接聽程式的屬性 |
> | 動作 | Microsoft.sqlvirtualmachine/sqlVirtualMachineGroups/availabilityGroupListeners/delete | 刪除現有的可用性群組接聽程式 |
> |  | **sqlVirtualMachineGroups/sqlVirtualMachines** |  |
> | 動作 | Microsoft.sqlvirtualmachine/sqlVirtualMachineGroups/sqlVirtualMachines/read | 依特定 sql 虛擬機器群組列出 Sql 虛擬機器 |
> |  | **sqlVirtualMachines** |  |
> | 動作 | Microsoft.sqlvirtualmachine/sqlVirtualMachines/read | 取得 SQL 虛擬機器的詳細資料 |
> | 動作 | Microsoft.sqlvirtualmachine/sqlVirtualMachines/write | 建立新的或變更現有 SQL 虛擬機器的屬性 |
> | 動作 | Microsoft.sqlvirtualmachine/sqlVirtualMachines/delete | 刪除現有的 SQL 虛擬機器 |

## <a name="microsoftstorage"></a>Microsoft.Storage

Azure 服務：[儲存體](../storage/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.Storage/register/action | 針對儲存體資源提供者註冊訂用帳戶，並讓您能夠建立儲存體帳戶。 |
> |  | **checknameavailability** |  |
> | 動作 | Microsoft.Storage/checknameavailability/read | 確認帳戶名稱有效，且並非使用中。 |
> |  | **堆放** |  |
> | 動作 | Microsoft.Storage/locations/deleteVirtualNetworkOrSubnets/action | 讓 Microsoft.Storage 知道虛擬網路或子網路即將刪除 |
> |  | **位置/checknameavailability** |  |
> | 動作 | Microsoft. Storage/位置/checknameavailability/read | 確認帳戶名稱有效，且並非使用中。 |
> |  | **位置/使用方式** |  |
> | 動作 | Microsoft.Storage/locations/usages/read | 傳回指定訂用帳戶資源的限制和目前的使用量計數 |
> |  | **業務** |  |
> | 動作 | Microsoft.Storage/operations/read | 輪詢非同步作業的狀態。 |
> |  | **sku** |  |
> | 動作 | Microsoft.Storage/skus/read | 列出 Microsoft.Storage 所支援的 SKU。 |
> |  | **storageAccounts** |  |
> | 動作 | Microsoft. Storage/storageAccounts/restoreBlobRanges/action | 將 blob 範圍還原至指定時間的狀態 |
> | 動作 | Microsoft. Storage/storageAccounts/PrivateEndpointConnectionsApproval/action | 核准私人端點連接 |
> | 動作 | Microsoft. Storage/storageAccounts/容錯移轉/動作 | 客戶能夠在發生可用性問題時控制容錯移轉 |
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
> |  | **storageAccounts/blobServices/容器** |  |
> | 動作 | Microsoft.Storage/storageAccounts/blobServices/containers/write | 傳回修補 Blob 容器的結果 |
> | 動作 | Microsoft.Storage/storageAccounts/blobServices/containers/delete | 傳回刪除容器的結果 |
> | 動作 | Microsoft.Storage/storageAccounts/blobServices/containers/read | 傳回容器 |
> | 動作 | Microsoft.Storage/storageAccounts/blobServices/containers/read | 傳回容器的清單 |
> | 動作 | Microsoft.Storage/storageAccounts/blobServices/containers/lease/action | 傳回租用 Blob 容器的結果 |
> | 動作 | Microsoft.Storage/storageAccounts/blobServices/containers/write | 傳回放置 Blob 容器的結果 |
> | 動作 | Microsoft.Storage/storageAccounts/blobServices/containers/clearLegalHold/action | 清除 Blob 容器法務保存措施 |
> | 動作 | Microsoft.Storage/storageAccounts/blobServices/containers/setLegalHold/action | 設定 Blob 容器法務保存措施 |
> |  | **storageAccounts/blobServices/容器/immutabilityPolicies** |  |
> | 動作 | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/extend/action | 擴充 Blob 容器不變性原則 |
> | 動作 | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/delete | 刪除 Blob 容器不變性原則 |
> | 動作 | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/write | 放置 Blob 容器不變性原則 |
> | 動作 | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/lock/action | 鎖定 Blob 容器不變性原則 |
> | 動作 | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/read | 取得 Blob 容器不變性原則 |
> |  | **storageAccounts/dataSharePolicies** |  |
> | 動作 | Microsoft. Storage/storageAccounts/dataSharePolicies/delete |  |
> | 動作 | Microsoft. Storage/storageAccounts/dataSharePolicies/read |  |
> | 動作 | Microsoft. Storage/storageAccounts/dataSharePolicies/read |  |
> | 動作 | Microsoft. Storage/storageAccounts/dataSharePolicies/write |  |
> |  | **storageAccounts/encryptionScopes** |  |
> | 動作 | Microsoft. Storage/storageAccounts/encryptionScopes/read |  |
> | 動作 | Microsoft. Storage/storageAccounts/encryptionScopes/read |  |
> | 動作 | Microsoft. Storage/storageAccounts/encryptionScopes/write |  |
> | 動作 | Microsoft. Storage/storageAccounts/encryptionScopes/write |  |
> |  | **storageAccounts/fileServices** |  |
> | 動作 | Microsoft. Storage/storageAccounts/fileServices/共用/動作 |  |
> | 動作 | Microsoft. Storage/storageAccounts/fileServices/read |  |
> | 動作 | Microsoft. Storage/storageAccounts/fileServices/write |  |
> | 動作 | Microsoft. Storage/storageAccounts/fileServices/read | 取得檔案服務屬性 |
> |  | **storageAccounts/fileServices/共用** |  |
> | 動作 | Microsoft. Storage/storageAccounts/fileServices/共用/刪除 |  |
> | 動作 | Microsoft. Storage/storageAccounts/fileServices/共用/讀取 |  |
> | 動作 | Microsoft. Storage/storageAccounts/fileServices/共用/讀取 |  |
> | 動作 | Microsoft. Storage/storageAccounts/fileServices/共用/寫入 |  |
> |  | **storageAccounts/managementPolicies** |  |
> | 動作 | Microsoft. Storage/storageAccounts/managementPolicies/delete | 刪除儲存體帳戶管理原則 |
> | 動作 | Microsoft. Storage/storageAccounts/managementPolicies/read | 取得儲存體管理帳戶原則 |
> | 動作 | Microsoft. Storage/storageAccounts/managementPolicies/write | 放置儲存體帳戶管理原則 |
> |  | **storageAccounts/objectReplicationPolicies** |  |
> | 動作 | Microsoft. Storage/storageAccounts/objectReplicationPolicies/delete |  |
> | 動作 | Microsoft. Storage/storageAccounts/objectReplicationPolicies/read |  |
> | 動作 | Microsoft. Storage/storageAccounts/objectReplicationPolicies/read |  |
> | 動作 | Microsoft. Storage/storageAccounts/objectReplicationPolicies/write |  |
> |  | **storageAccounts/privateEndpointConnectionProxies** |  |
> | 動作 | Microsoft. Storage/storageAccounts/privateEndpointConnectionProxies/read | 取得私人端點連接 Proxy |
> | 動作 | Microsoft. Storage/storageAccounts/privateEndpointConnectionProxies/delete | 刪除私人端點連接 proxy |
> | 動作 | Microsoft. Storage/storageAccounts/privateEndpointConnectionProxies/write | 放置私人端點連接 proxy |
> |  | **storageAccounts/privateEndpointConnections** |  |
> | 動作 | Microsoft. Storage/storageAccounts/privateEndpointConnections/delete | 刪除私人端點連接 |
> | 動作 | Microsoft. Storage/storageAccounts/privateEndpointConnections/read | 取得私人端點連接 |
> | 動作 | Microsoft. Storage/storageAccounts/privateEndpointConnections/write | Put 私用端點連接 |
> |  | **storageAccounts/privateLinkResources** |  |
> | 動作 | Microsoft. Storage/storageAccounts/privateLinkResources/read | 取得 StorageAccount groupids |
> |  | **storageAccounts/queueServices** |  |
> | 動作 | Microsoft.Storage/storageAccounts/queueServices/read | 取得佇列服務屬性 |
> | 動作 | Microsoft.Storage/storageAccounts/queueServices/read | 傳回佇列服務屬性或統計資料。 |
> | 動作 | Microsoft.Storage/storageAccounts/queueServices/write | 傳回設定佇列服務屬性的結果 |
> |  | **storageAccounts/queueServices/佇列** |  |
> | 動作 | Microsoft.Storage/storageAccounts/queueServices/queues/read | 傳回佇列或佇列清單。 |
> | 動作 | Microsoft.Storage/storageAccounts/queueServices/queues/write | 傳回寫入佇列的結果 |
> | 動作 | Microsoft.Storage/storageAccounts/queueServices/queues/delete | 傳回刪除佇列的結果 |
> |  | **storageAccounts/services/diagnosticSettings** |  |
> | 動作 | Microsoft.Storage/storageAccounts/services/diagnosticSettings/write | 建立/更新儲存體帳戶的診斷設定。 |
> |  | **storageAccounts/tableServices** |  |
> | 動作 | Microsoft. Storage/storageAccounts/tableServices/read | 取得表格服務屬性 |
> |  | **實例** |  |
> | 動作 | Microsoft.Storage/usages/read | 傳回指定訂用帳戶資源的限制和目前的使用量計數 |
> |  | **storageAccounts/blobServices/容器/blob** |  |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | 傳回 Blob 或 Blob 清單 |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | 傳回寫入 Blob 的結果 |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | 傳回刪除 Blob 的結果 |
> | DataAction | Microsoft. Storage/storageAccounts/blobServices/container/blob/deleteBlobVersion/action | 傳回刪除 blob 版本的結果 |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action | 傳回新增 Blob 內容的結果 |
> | DataAction | Microsoft. Storage/storageAccounts/blobServices/container/blob/filter/action | 傳回具有相符標記篩選之帳戶底下的 blob 清單 |
> | DataAction | Microsoft 儲存體/storageAccounts/blobServices/容器/blob/移動/動作 | 將 blob 從一個路徑移到另一個 |
> | DataAction | Microsoft. Storage/storageAccounts/blobServices/container/blob/manageOwnership/action | 變更 blob 的擁有權 |
> | DataAction | Microsoft. Storage/storageAccounts/blobServices/container/blob/modifyPermissions/action | 修改 blob 的許可權 |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/runAsSuperUser/action | 傳回 blob 命令的結果 |
> |  | **storageAccounts/blobServices/容器/blob/標記** |  |
> | DataAction | Microsoft 儲存體/storageAccounts/blobServices/容器/blob/標記/讀取 | 傳回讀取 blob 標記的結果 |
> | DataAction | Microsoft 儲存體/storageAccounts/blobServices/容器/blob/標記/寫入 | 傳回寫入 blob 標記的結果 |
> |  | **storageAccounts/fileServices/檔案共用/files** |  |
> | DataAction | Microsoft. Storage/storageAccounts/fileServices/檔案共用/files/read | 傳回檔案/資料夾或檔案/資料夾的清單 |
> | DataAction | Microsoft. Storage/storageAccounts/fileServices/檔案共用/files/write | 傳回寫入檔案或建立資料夾的結果 |
> | DataAction | Microsoft. Storage/storageAccounts/fileServices/檔案共用/files/delete | 傳回刪除檔案/資料夾的結果 |
> | DataAction | Microsoft. Storage/storageAccounts/fileServices/檔案共用/files/modifypermissions/action | 傳回檔案/資料夾上修改許可權的結果 |
> | DataAction | Microsoft. Storage/storageAccounts/fileServices/檔案共用/files/actassuperuser/action | 取得檔案系統管理員許可權 |
> |  | **storageAccounts/queueServices/佇列/訊息** |  |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | 傳回訊息 |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/write | 傳回寫入訊息的結果 |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete | 傳回刪除訊息的結果 |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action | 傳回新增訊息的結果 |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action | 傳回處理訊息的結果 |

## <a name="microsoftstoragesync"></a>microsoft.storagesync

Azure 服務：[儲存體](../storage/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | microsoft.storagesync/register/action | 為儲存體同步處理提供者註冊訂用帳戶 |
> | 動作 | microsoft.storagesync/取消註冊/動作 | 取消註冊儲存體同步處理提供者的訂用帳戶 |
> |  | **堆放** |  |
> | 動作 | microsoft.storagesync/locations/checkNameAvailability/action | 確認儲存體同步服務名稱有效，且並非使用中。 |
> |  | **位置/工作流程/作業** |  |
> | 動作 | microsoft.storagesync/locations/workflows/operations/read | 取得非同步作業的狀態 |
> |  | **業務** |  |
> | 動作 | microsoft.storagesync/作業/讀取 | 取得支援的作業清單 |
> |  | **storageSyncServices** |  |
> | 動作 | microsoft.storagesync/storageSyncServices/read | 讀取任何儲存體同步服務 |
> | 動作 | microsoft.storagesync/storageSyncServices/write | 建立或更新任何儲存體同步服務 |
> | 動作 | microsoft.storagesync/storageSyncServices/delete | 刪除任何儲存體同步服務 |
> |  | **storageSyncServices/providers/Microsoft Insights/Metricdefinitions.listasync** |  |
> | 動作 | microsoft.storagesync/storageSyncServices/providers/Microsoft.Insights/metricDefinitions/read | 取得儲存體同步服務的可用計量 |
> |  | **storageSyncServices/registeredServers** |  |
> | 動作 | microsoft.storagesync/storageSyncServices/registeredServers/read | 讀取任何已註冊的伺服器 |
> | 動作 | microsoft.storagesync/storageSyncServices/registeredServers/write | 建立或更新任何已註冊的伺服器 |
> | 動作 | microsoft.storagesync/storageSyncServices/registeredServers/delete | 刪除任何已註冊的伺服器 |
> |  | **storageSyncServices/registeredServers/providers/Microsoft Insights/Metricdefinitions.listasync** |  |
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
> | 動作 | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/triggerChangeDetection/action | 呼叫此動作以在雲端端點的檔案共用上觸發變更的偵測 |
> |  | **storageSyncServices/syncGroups/cloudEndpoints/operationresults** |  |
> | 動作 | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/operationresults/read | 取得非同步備份/還原作業的狀態 |
> |  | **storageSyncServices/syncGroups/providers/Microsoft Insights/Metricdefinitions.listasync** |  |
> | 動作 | microsoft.storagesync/storageSyncServices/syncGroups/providers/Microsoft.Insights/metricDefinitions/read | 取得同步群組的可用計量 |
> |  | **storageSyncServices/syncGroups/serverEndpoints** |  |
> | 動作 | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/read | 讀取任何伺服器端點 |
> | 動作 | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/write | 建立或更新任何伺服器端點 |
> | 動作 | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/delete | 刪除任何伺服器端點 |
> | 動作 | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/recallAction/action | 呼叫此動作以將檔案回復到伺服器 |
> |  | **storageSyncServices/syncGroups/serverEndpoints/providers/Microsoft Insights/Metricdefinitions.listasync** |  |
> | 動作 | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/providers/Microsoft.Insights/metricDefinitions/read | 取得伺服器端點的可用計量 |
> |  | **storageSyncServices/工作流程** |  |
> | 動作 | microsoft.storagesync/storageSyncServices/workflows/read | 讀取工作流程 |
> |  | **storageSyncServices/workflow/operationresults** |  |
> | 動作 | microsoft.storagesync/storageSyncServices/workflows/operationresults/read | 取得非同步作業的狀態 |
> |  | **storageSyncServices/工作流程/作業** |  |
> | 動作 | microsoft.storagesync/storageSyncServices/workflows/operations/read | 取得非同步作業的狀態 |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

Azure 服務： [StorSimple](../storsimple/index.md)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.StorSimple/register/action | 註冊提供者 Microsoft.StorSimple |
> |  | **器** |  |
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
> |  | **管理員/accessControlRecords** |  |
> | 動作 | Microsoft.StorSimple/managers/accessControlRecords/read | 列出或取得存取控制記錄 |
> | 動作 | Microsoft.StorSimple/managers/accessControlRecords/write | 建立或更新存取控制記錄 |
> | 動作 | Microsoft.StorSimple/managers/accessControlRecords/delete | 刪除存取控制記錄 |
> |  | **管理員/accessControlRecords/operationResults** |  |
> | 動作 | Microsoft.StorSimple/managers/accessControlRecords/operationResults/read | 列出或取得作業結果 |
> |  | **管理員/警示** |  |
> | 動作 | Microsoft.StorSimple/managers/alerts/read | 列出或取得警示 |
> |  | **管理員/備份** |  |
> | 動作 | Microsoft.StorSimple/managers/backups/read | 列出或取得備份組 |
> |  | **管理員/bandwidthSettings** |  |
> | 動作 | Microsoft.StorSimple/managers/bandwidthSettings/read | 列出頻寬設定 (僅限 8000 系列) |
> | 動作 | Microsoft.StorSimple/managers/bandwidthSettings/write | 新建或更新頻寬設定 (僅限 8000 系列) |
> | 動作 | Microsoft.StorSimple/managers/bandwidthSettings/delete | 刪除現有頻寬設定 (僅限 8000 系列) |
> |  | **管理員/bandwidthSettings/operationResults** |  |
> | 動作 | Microsoft.StorSimple/managers/bandwidthSettings/operationResults/read | 列出作業結果 |
> |  | **管理員/憑證** |  |
> | 動作 | Microsoft.StorSimple/managers/certificates/write | 建立或更新憑證 |
> | 動作 | Microsoft.StorSimple/Managers/certificates/write | 「更新資源憑證」作業會更新資源/保存庫的認證憑證。 |
> |  | **管理員/cloudApplianceConfigurations** |  |
> | 動作 | Microsoft.StorSimple/managers/cloudApplianceConfigurations/read | 列出雲端應用裝置所支援的組態 |
> |  | **管理員/裝置** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/sendTestAlertEmail/action | 傳送測試警示電子郵件給所設定的電子郵件收件者。 |
> | 動作 | Microsoft.StorSimple/managers/devices/scanForUpdates/action | 掃描裝置的更新。 |
> | 動作 | Microsoft.StorSimple/managers/devices/download/action | 下載裝置的更新。 |
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
> |  | **管理員/裝置/alertSettings** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/alertSettings/read | 列出或取得警示設定 |
> | 動作 | Microsoft.StorSimple/managers/devices/alertSettings/write | 建立或更新警示設定 |
> |  | **管理員/裝置/alertSettings/operationResults** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/alertSettings/operationResults/read | 列出或取得作業結果 |
> |  | **管理員/裝置/backupPolicies** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/backupPolicies/write | 新建或更新備份原則 (僅限 8000 系列) |
> | 動作 | Microsoft.StorSimple/managers/devices/backupPolicies/read | 列出備份原則 (僅限 8000 系列) |
> | 動作 | Microsoft.StorSimple/managers/devices/backupPolicies/delete | 刪除現有的備份原則 (僅限 8000 系列) |
> | 動作 | Microsoft.StorSimple/managers/devices/backupPolicies/backup/action | 製作手動備份，以針對原則所保護的所有磁碟區建立隨選備份。 |
> |  | **管理員/裝置/backupPolicies/operationResults** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/backupPolicies/operationResults/read | 列出作業結果 |
> |  | **管理員/裝置/backupPolicies/排程** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/write | 新建或更新排程 |
> | 動作 | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/read | 列出排程 |
> | 動作 | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/delete | 刪除現有排程 |
> |  | **管理員/裝置/backupPolicies/排程/operationResults** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/operationResults/read | 列出作業結果 |
> |  | **管理員/裝置/備份** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/backups/read | 列出或取得備份組 |
> | 動作 | Microsoft.StorSimple/managers/devices/backups/delete | 刪除備份組 |
> | 動作 | Microsoft.StorSimple/managers/devices/backups/restore/action | 從備份組還原所有磁碟區。 |
> |  | **管理員/裝置/備份/元素** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/backups/elements/clone/action | 使用備份元素來複製共用或磁碟區。 |
> |  | **管理員/裝置/備份/元素/operationResults** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/backups/elements/operationResults/read | 列出或取得作業結果 |
> |  | **管理員/裝置/備份/operationResults** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/backups/operationResults/read | 列出或取得作業結果 |
> |  | **管理員/裝置/backupScheduleGroups** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/backupScheduleGroups/read | 列出或取得備份排程群組 |
> | 動作 | Microsoft.StorSimple/managers/devices/backupScheduleGroups/write | 建立或更新備份排程群組 |
> | 動作 | Microsoft.StorSimple/managers/devices/backupScheduleGroups/delete | 刪除備份排程群組 |
> |  | **管理員/裝置/backupScheduleGroups/operationResults** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/backupScheduleGroups/operationResults/read | 列出或取得作業結果 |
> |  | **管理員/裝置/chapSettings** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/chapSettings/write | 建立或更新 Chap 設定 |
> | 動作 | Microsoft.StorSimple/managers/devices/chapSettings/read | 列出或取得 Chap 設定 |
> | 動作 | Microsoft.StorSimple/managers/devices/chapSettings/delete | 刪除 Chap 設定 |
> |  | **管理員/裝置/chapSettings/operationResults** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/chapSettings/operationResults/read | 列出或取得作業結果 |
> |  | **管理員/裝置/磁片** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/disks/read | 列出或取得磁碟 |
> |  | **管理員/裝置/容錯移轉/operationResults** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/failover/operationResults/read | 列出或取得作業結果 |
> |  | **管理員/裝置/failoverTargets** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/failoverTargets/read | 列出或取得裝置的容錯移轉目標 |
> |  | **管理員/裝置/** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/fileservers/read | 列出或取得檔案伺服器 |
> | 動作 | Microsoft.StorSimple/managers/devices/fileservers/write | 建立或更新檔案伺服器 |
> | 動作 | Microsoft.StorSimple/managers/devices/fileservers/delete | 刪除檔案伺服器 |
> | 動作 | Microsoft.StorSimple/managers/devices/fileservers/backup/action | 製作檔案伺服器的備份。 |
> |  | **管理員/裝置/擁有者/計量** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/fileservers/metrics/read | 列出或取得計量 |
> |  | **管理員/裝置/擁有者/metricsDefinitions** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/fileservers/metricsDefinitions/read | 列出或取得計量定義 |
> |  | **管理員/裝置/擁有者/operationResults** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/fileservers/operationResults/read | 列出或取得作業結果 |
> |  | **管理員/裝置/擁有者/共用** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/fileservers/shares/write | 建立或更新共用 |
> | 動作 | Microsoft.StorSimple/managers/devices/fileservers/shares/read | 列出或取得共用 |
> | 動作 | Microsoft.StorSimple/managers/devices/fileservers/shares/delete | 刪除共用 |
> |  | **管理員/裝置/擁有者/共用/計量** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/fileservers/shares/metrics/read | 列出或取得計量 |
> |  | **管理員/裝置/擁有者/共用/metricsDefinitions** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/fileservers/shares/metricsDefinitions/read | 列出或取得計量定義 |
> |  | **管理員/裝置/擁有者/共用/operationResults** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/fileservers/shares/operationResults/read | 列出或取得作業結果 |
> |  | **管理員/裝置/hardwareComponentGroups** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/read | 列出硬體元件群組 |
> | 動作 | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/changeControllerPowerState/action | 變更硬體元件群組的控制器電源狀態 |
> |  | **管理員/裝置/hardwareComponentGroups/operationResults** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/operationResults/read | 列出作業結果 |
> |  | **管理員/裝置/iscsiservers** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/iscsiservers/read | 列出或取得 iSCSI 伺服器 |
> | 動作 | Microsoft.StorSimple/managers/devices/iscsiservers/write | 建立或更新 iSCSI 伺服器 |
> | 動作 | Microsoft.StorSimple/managers/devices/iscsiservers/delete | 刪除 iSCSI 伺服器 |
> | 動作 | Microsoft.StorSimple/managers/devices/iscsiservers/backup/action | 製作 iSCSI 伺服器的備份。 |
> |  | **管理員/裝置/iscsiservers/磁片** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/iscsiservers/disks/read | 列出或取得磁碟 |
> | 動作 | Microsoft.StorSimple/managers/devices/iscsiservers/disks/write | 建立或更新磁碟 |
> | 動作 | Microsoft.StorSimple/managers/devices/iscsiservers/disks/delete | 刪除磁碟 |
> |  | **管理員/裝置/iscsiservers/磁片/計量** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/iscsiservers/disks/metrics/read | 列出或取得計量 |
> |  | **管理員/裝置/iscsiservers/磁片/metricsDefinitions** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/iscsiservers/disks/metricsDefinitions/read | 列出或取得計量定義 |
> |  | **管理員/裝置/iscsiservers/磁片/operationResults** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/iscsiservers/disks/operationResults/read | 列出或取得作業結果 |
> |  | **管理員/裝置/iscsiservers/計量** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/iscsiservers/metrics/read | 列出或取得計量 |
> |  | **管理員/裝置/iscsiservers/metricsDefinitions** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/iscsiservers/metricsDefinitions/read | 列出或取得計量定義 |
> |  | **管理員/裝置/iscsiservers/operationResults** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/iscsiservers/operationResults/read | 列出或取得作業結果 |
> |  | **管理員/裝置/作業** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/jobs/read | 列出或取得作業 |
> | 動作 | Microsoft.StorSimple/managers/devices/jobs/cancel/action | 取消正在執行的作業 |
> |  | **管理員/裝置/作業/operationResults** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/jobs/operationResults/read | 列出作業結果 |
> |  | **管理員/裝置/計量** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/metrics/read | 列出或取得計量 |
> |  | **管理員/裝置/metricsDefinitions** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/metricsDefinitions/read | 列出或取得計量定義 |
> |  | **管理員/裝置/migrationSourceConfigurations** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/import/action | 匯入用於移轉的來源組態 |
> | 動作 | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/startMigrationEstimate/action | 啟動作業來預估移轉程序的持續時間。 |
> | 動作 | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/startMigration/action | 使用來源組態來開始移轉 |
> | 動作 | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/confirmMigration/action | 確認移轉成功，並加以認可。 |
> | 動作 | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchMigrationEstimate/action | 提取移轉估計作業的狀態。 |
> | 動作 | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchMigrationStatus/action | 提取移轉狀態。 |
> | 動作 | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchConfirmMigrationStatus/action | 提取移轉的確認狀態。 |
> |  | **管理員/裝置/migrationSourceConfigurations/confirmMigrationStatus** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/confirmMigrationStatus/read | 列出確認移轉狀態 |
> |  | **管理員/裝置/migrationSourceConfigurations/migrationEstimate** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/migrationEstimate/read | 列出移轉估計 |
> |  | **管理員/裝置/migrationSourceConfigurations/migrationStatus** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/migrationStatus/read | 列出移轉狀態 |
> |  | **管理員/裝置/migrationSourceConfigurations/operationResults** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/operationResults/read | 列出作業結果 |
> |  | **管理員/裝置/networkSettings** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/networkSettings/read | 列出或取得網路設定 |
> | 動作 | Microsoft.StorSimple/managers/devices/networkSettings/write | 新建或更新網路設定 |
> |  | **管理員/裝置/networkSettings/operationResults** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/networkSettings/operationResults/read | 列出作業結果 |
> |  | **管理員/裝置/operationResults** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/operationResults/read | 列出或取得作業結果 |
> |  | **管理員/裝置/securitySettings** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/securitySettings/update/action | 更新安全性設定。 |
> | 動作 | Microsoft.StorSimple/managers/devices/securitySettings/read | 列出安全性設定 |
> | 動作 | Microsoft.StorSimple/managers/devices/securitySettings/syncRemoteManagementCertificate/action | 同步處理裝置的遠端管理憑證。 |
> | 動作 | Microsoft.StorSimple/managers/devices/securitySettings/write | 新建或更新安全性設定 |
> |  | **管理員/裝置/securitySettings/operationResults** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/securitySettings/operationResults/read | 列出或取得作業結果 |
> |  | **管理員/裝置/共用** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/shares/read | 列出或取得共用 |
> |  | **管理員/裝置/timeSettings** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/timeSettings/read | 列出或取得時間設定 |
> | 動作 | Microsoft.StorSimple/managers/devices/timeSettings/write | 新建或更新時間設定 |
> |  | **管理員/裝置/timeSettings/operationResults** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/timeSettings/operationResults/read | 列出作業結果 |
> |  | **管理員/裝置/更新/operationResults** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/updates/operationResults/read | 列出或取得作業結果 |
> |  | **管理員/裝置/updateSummary** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/updateSummary/read | 列出或取得更新摘要 |
> |  | **管理員/裝置/volumeContainers** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/volumeContainers/write | 新建或更新磁碟區容器 (僅限 8000 系列) |
> | 動作 | Microsoft.StorSimple/managers/devices/volumeContainers/read | 列出磁碟區容器 (僅限 8000 系列) |
> | 動作 | Microsoft.StorSimple/managers/devices/volumeContainers/delete | 刪除現有的磁碟區容器 (僅限 8000 系列) |
> |  | **管理員/裝置/volumeContainers/計量** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/volumeContainers/metrics/read | 列出計量 |
> |  | **管理員/裝置/volumeContainers/metricsDefinitions** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/volumeContainers/metricsDefinitions/read | 列出計量定義 |
> |  | **管理員/裝置/volumeContainers/operationResults** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/volumeContainers/operationResults/read | 列出作業結果 |
> |  | **管理員/裝置/volumeContainers/磁片區** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/read | 列出磁碟區 |
> | 動作 | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/write | 新建或更新磁碟區 |
> | 動作 | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/delete | 刪除現有磁碟區 |
> |  | **管理員/裝置/volumeContainers/磁片區/計量** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/metrics/read | 列出計量 |
> |  | **管理員/裝置/volumeContainers/磁片區/metricsDefinitions** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/metricsDefinitions/read | 列出計量定義 |
> |  | **管理員/裝置/volumeContainers/磁片區/operationResults** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/operationResults/read | 列出作業結果 |
> |  | **管理員/裝置/磁片區** |  |
> | 動作 | Microsoft.StorSimple/managers/devices/volumes/read | 列出磁碟區 |
> |  | **管理員/encryptionSettings** |  |
> | 動作 | Microsoft.StorSimple/managers/encryptionSettings/read | 列出或取得加密設定 |
> |  | **管理員/extendedInformation** |  |
> | 動作 | Microsoft.StorSimple/managers/extendedInformation/read | 列出或取得延伸的保存庫資訊 |
> | 動作 | Microsoft.StorSimple/managers/extendedInformation/write | 建立或更新延伸的保存庫資訊 |
> | 動作 | Microsoft.StorSimple/managers/extendedInformation/delete | 刪除擴充的保存庫資訊 |
> | 動作 | Microsoft.StorSimple/Managers/extendedInformation/read | 「取得延伸資訊」作業會取得物件的延伸資訊，此延伸資訊代表 'vault' 類型的 Azure 資源 |
> | 動作 | Microsoft.StorSimple/Managers/extendedInformation/write | 「取得延伸資訊」作業會取得物件的延伸資訊，此延伸資訊代表 'vault' 類型的 Azure 資源 |
> | 動作 | Microsoft.StorSimple/Managers/extendedInformation/delete | 「取得延伸資訊」作業會取得物件的延伸資訊，此延伸資訊代表 'vault' 類型的 Azure 資源 |
> |  | **管理員/功能** |  |
> | 動作 | Microsoft.StorSimple/managers/features/read | 列出功能 |
> |  | **管理員/擁有者** |  |
> | 動作 | Microsoft.StorSimple/managers/fileservers/read | 列出或取得檔案伺服器 |
> |  | **管理員/iscsiservers** |  |
> | 動作 | Microsoft.StorSimple/managers/iscsiservers/read | 列出或取得 iSCSI 伺服器 |
> |  | **管理員/作業** |  |
> | 動作 | Microsoft.StorSimple/managers/jobs/read | 列出或取得作業 |
> |  | **管理員/計量** |  |
> | 動作 | Microsoft.StorSimple/managers/metrics/read | 列出或取得計量 |
> |  | **管理員/metricsDefinitions** |  |
> | 動作 | Microsoft.StorSimple/managers/metricsDefinitions/read | 列出或取得計量定義 |
> |  | **管理員/migrationSourceConfigurations** |  |
> | 動作 | Microsoft.StorSimple/managers/migrationSourceConfigurations/read | 列出移轉來源設定 (僅限 8000 系列) |
> |  | **管理員/operationResults** |  |
> | 動作 | Microsoft.StorSimple/managers/operationResults/read | 列出或取得作業結果 |
> |  | **管理員/storageAccountCredentials** |  |
> | 動作 | Microsoft.StorSimple/managers/storageAccountCredentials/write | 建立或更新儲存體帳戶認證 |
> | 動作 | Microsoft.StorSimple/managers/storageAccountCredentials/read | 列出或取得儲存體帳戶認證 |
> | 動作 | Microsoft.StorSimple/managers/storageAccountCredentials/delete | 刪除儲存體帳戶認證 |
> |  | **管理員/storageAccountCredentials/operationResults** |  |
> | 動作 | Microsoft.StorSimple/managers/storageAccountCredentials/operationResults/read | 列出或取得作業結果 |
> |  | **管理員/storageDomains** |  |
> | 動作 | Microsoft.StorSimple/managers/storageDomains/read | 列出或取得儲存體網域 |
> | 動作 | Microsoft.StorSimple/managers/storageDomains/write | 建立或更新儲存體網域 |
> | 動作 | Microsoft.StorSimple/managers/storageDomains/delete | 刪除儲存體網域 |
> |  | **管理員/storageDomains/operationResults** |  |
> | 動作 | Microsoft.StorSimple/managers/storageDomains/operationResults/read | 列出或取得作業結果 |
> |  | **業務** |  |
> | 動作 | Microsoft.StorSimple/operations/read | 列出或取得作業 |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

Azure 服務：[串流分析](../stream-analytics/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.StreamAnalytics/Register/action | 向串流分析資源提供者註冊訂用帳戶 |
> |  | **位置/配額** |  |
> | 動作 | Microsoft.StreamAnalytics/locations/quotas/Read | 讀取串流分析訂用帳戶配額 |
> |  | **業務** |  |
> | 動作 | Microsoft.StreamAnalytics/operations/Read | 讀取串流分析作業 |
> |  | **streamingjobs** |  |
> | 動作 | Microsoft.StreamAnalytics/streamingjobs/Delete | 刪除串流分析作業 |
> | 動作 | Mslearn-streamanalytics/streamingjobs/PublishEdgePackage/action | 發佈串流分析作業的 edge 套件 |
> | 動作 | Microsoft.StreamAnalytics/streamingjobs/Read | 讀取串流分析作業 |
> | 動作 | Mslearn-streamanalytics/streamingjobs/調整/動作 | 調整串流分析作業 |
> | 動作 | Microsoft.StreamAnalytics/streamingjobs/Start/action | 啟動串流分析作業 |
> | 動作 | Microsoft.StreamAnalytics/streamingjobs/Stop/action | 停止串流分析作業 |
> | 動作 | Microsoft.StreamAnalytics/streamingjobs/Write | 寫入串流分析作業 |
> |  | **streamingjobs/函數** |  |
> | 動作 | Microsoft.StreamAnalytics/streamingjobs/functions/Delete | 刪除串流分析作業函式 |
> | 動作 | Microsoft.StreamAnalytics/streamingjobs/functions/Read | 讀取串流分析作業函式 |
> | 動作 | Microsoft.StreamAnalytics/streamingjobs/functions/RetrieveDefaultDefinition/action | 擷取串流分析作業函式的預設定義 |
> | 動作 | Microsoft.StreamAnalytics/streamingjobs/functions/Test/action | 測試串流分析作業函式 |
> | 動作 | Microsoft.StreamAnalytics/streamingjobs/functions/Write | 寫入串流分析作業函式 |
> |  | **streamingjobs/函數/operationresults** |  |
> | 動作 | Microsoft.StreamAnalytics/streamingjobs/functions/operationresults/Read | 讀取串流分析作業函式的作業結果 |
> |  | **streamingjobs/輸入** |  |
> | 動作 | Microsoft.StreamAnalytics/streamingjobs/inputs/Delete | 刪除串流分析作業輸入 |
> | 動作 | Microsoft.StreamAnalytics/streamingjobs/inputs/Read | 讀取串流分析作業輸入 |
> | 動作 | Microsoft.StreamAnalytics/streamingjobs/inputs/Sample/action | 進行串流分析作業輸入的取樣 |
> | 動作 | Microsoft.StreamAnalytics/streamingjobs/inputs/Test/action | 測試串流分析作業輸入 |
> | 動作 | Microsoft.StreamAnalytics/streamingjobs/inputs/Write | 寫入串流分析作業輸入 |
> |  | **streamingjobs/輸入/operationresults** |  |
> | 動作 | Microsoft.StreamAnalytics/streamingjobs/inputs/operationresults/Read | 讀取串流分析作業輸入的作業結果 |
> |  | **streamingjobs/metricdefinitions.listasync** |  |
> | 動作 | Microsoft.StreamAnalytics/streamingjobs/metricdefinitions/Read | 讀取計量定義 |
> |  | **streamingjobs/operationresults** |  |
> | 動作 | Microsoft.StreamAnalytics/streamingjobs/operationresults/Read | 讀取串流分析作業的作業結果 |
> |  | **streamingjobs/輸出** |  |
> | 動作 | Microsoft.StreamAnalytics/streamingjobs/outputs/Delete | 刪除串流分析作業輸出 |
> | 動作 | Microsoft.StreamAnalytics/streamingjobs/outputs/Read | 讀取串流分析作業輸出 |
> | 動作 | Microsoft.StreamAnalytics/streamingjobs/outputs/Test/action | 測試串流分析作業輸出 |
> | 動作 | Microsoft.StreamAnalytics/streamingjobs/outputs/Write | 寫入串流分析作業輸出 |
> |  | **streamingjobs/輸出/operationresults** |  |
> | 動作 | Microsoft.StreamAnalytics/streamingjobs/outputs/operationresults/Read | 讀取串流分析作業輸出的作業結果 |
> |  | **streamingjobs/providers/Microsoft Insights/diagnosticSettings** |  |
> | 動作 | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/read | 讀取診斷設定。 |
> | 動作 | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/write | 寫入診斷設定。 |
> |  | **streamingjobs/providers/Microsoft Insights/logDefinitions** |  |
> | 動作 | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/logDefinitions/read | 取得 streamingjobs 的可用記錄 |
> |  | **streamingjobs/providers/Microsoft Insights/Metricdefinitions.listasync** |  |
> | 動作 | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/metricDefinitions/read | 取得 streamingjobs 的可用計量 |
> |  | **streamingjobs/轉換** |  |
> | 動作 | Microsoft.StreamAnalytics/streamingjobs/transformations/Delete | 刪除串流分析作業轉換 |
> | 動作 | Microsoft.StreamAnalytics/streamingjobs/transformations/Read | 讀取串流分析作業轉換 |
> | 動作 | Microsoft.StreamAnalytics/streamingjobs/transformations/Write | 寫入串流分析作業轉換 |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

Azure 服務：核心

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.Subscription/CreateSubscription/action | 建立 Azure 訂用帳戶 |
> | 動作 | Microsoft.Subscription/register/action | 向 Microsoft.Subscription 資源提供者註冊訂用帳戶 |
> | 動作 | Microsoft 訂用帳戶/取消/動作 | 取消訂用帳戶 |
> | 動作 | Microsoft 訂用帳戶/重新命名/動作 | 重新命名訂用帳戶 |
> |  | **SubscriptionDefinitions** |  |
> | 動作 | Microsoft.Subscription/SubscriptionDefinitions/read | 取得管理群組中的 Azure 訂用帳戶定義。 |
> | 動作 | Microsoft.Subscription/SubscriptionDefinitions/write | 建立 Azure 訂用帳戶定義 |

## <a name="microsoftsupport"></a>Microsoft.Support

Azure 服務：核心

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.Support/register/action | 註冊支援資源提供者 |
> | 動作 | Microsoft 支援/checkNameAvailability/動作 | 檢查名稱是否有效，且不會用於資源類型 |
> |  | **operationresults** |  |
> | 動作 | Microsoft 支援/operationresults/讀取 | 取得作業結果 |
> |  | **業務** |  |
> | 動作 | Microsoft. 支援/作業/讀取 | 列出 Microsoft 提供的作業。支援資源提供者 |
> |  | **operationsstatus** |  |
> | 動作 | Microsoft 支援/operationsstatus/讀取 | 取得作業狀態 |
> |  | **services** |  |
> | 動作 | Microsoft 支援/服務/讀取 | 取得可支援的 Azure 服務清單 |
> |  | **服務/problemClassifications** |  |
> | 動作 | Microsoft 支援/服務/problemClassifications/讀取 | 取得適用于 Azure 服務的問題分類清單 |
> |  | **supportTickets** |  |
> | 動作 | Microsoft.Support/supportTickets/read | 取得支援票證的清單。 |
> | 動作 | Microsoft.Support/supportTickets/write | 以非同步方式建立支援票證，或加以更新。 您可以建立技術、帳單、配額或訂用帳戶管理相關問題的支援票證。 您可以更新現有支援票證的嚴重性和連絡人詳細資料。 |
> |  | **supportTickets/通訊** |  |
> | 動作 | Microsoft 支援/supportTickets/通訊/讀取 | 取得支援票證通訊的清單 |
> | 動作 | Microsoft 支援/supportTickets/通訊/寫入 | 建立支援票證通訊 |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

Azure 服務：[時間序列深入解析](../time-series-insights/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.TimeSeriesInsights/register/action | 為時間序列深入解析資源提供者註冊訂用帳戶，並讓您能夠建立時間序列深入解析環境。 |
> |  | **環境** |  |
> | 動作 | Microsoft.TimeSeriesInsights/environments/read | 取得環境的屬性。 |
> | 動作 | Microsoft.TimeSeriesInsights/environments/write | 建立新的環境，或更新現有的環境。 |
> | 動作 | Microsoft.TimeSeriesInsights/environments/delete | 刪除環境。 |
> |  | **環境/accesspolicies** |  |
> | 動作 | Microsoft.TimeSeriesInsights/environments/accesspolicies/read | 取得存取原則的屬性。 |
> | 動作 | Microsoft.TimeSeriesInsights/environments/accesspolicies/write | 針對環境建立新的存取原則，或更新現有的存取原則。 |
> | 動作 | Microsoft.TimeSeriesInsights/environments/accesspolicies/delete | 刪除存取原則。 |
> |  | **環境/eventsources** |  |
> | 動作 | Microsoft.TimeSeriesInsights/environments/eventsources/read | 取得事件來源的屬性。 |
> | 動作 | Microsoft.TimeSeriesInsights/environments/eventsources/write | 針對環境建立新的事件來源，或更新現有的事件來源。 |
> | 動作 | Microsoft.TimeSeriesInsights/environments/eventsources/delete | 刪除事件來源。 |
> |  | **環境/referencedatasets** |  |
> | 動作 | Microsoft.TimeSeriesInsights/environments/referencedatasets/read | 取得參考資料集的屬性。 |
> | 動作 | Microsoft.TimeSeriesInsights/environments/referencedatasets/write | 針對環境建立新的參考資料集，或更新現有的參考資料集。 |
> | 動作 | Microsoft.TimeSeriesInsights/environments/referencedatasets/delete | 刪除參考資料集。 |
> |  | **環境/狀態** |  |
> | 動作 | Microsoft.TimeSeriesInsights/environments/status/read | 取得環境的狀態，其相關聯作業 (例如輸入) 的狀態。 |

## <a name="microsoftvisualstudio"></a>Microsoft.VisualStudio

Azure 服務： [Azure DevOps](https://docs.microsoft.com/azure/devops/)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | Microsoft.VisualStudio/Register/Action | 向 Microsoft.VisualStudio 提供者註冊 Azure 訂用帳戶 |
> |  | **帳戶** |  |
> | 動作 | Microsoft.VisualStudio/Account/Write | 設定帳戶 |
> | 動作 | Microsoft.VisualStudio/Account/Delete | 刪除帳戶 |
> | 動作 | Microsoft.VisualStudio/Account/Read | 讀取帳戶 |
> |  | **帳戶/擴充功能** |  |
> | 動作 | Microsoft.VisualStudio/Account/Extension/Read | 讀取帳戶/擴充功能 |
> |  | **帳戶/專案** |  |
> | 動作 | Microsoft.VisualStudio/Account/Project/Read | 讀取帳戶/專案 |
> | 動作 | Microsoft.VisualStudio/Account/Project/Write | 讀取帳戶/專案 |
> |  | **副檔名** |  |
> | 動作 | Microsoft.VisualStudio/Extension/Write | 設定擴充功能 |
> | 動作 | Microsoft.VisualStudio/Extension/Delete | 刪除擴充功能 |
> | 動作 | Microsoft.VisualStudio/Extension/Read | 讀取擴充功能 |
> |  | **專案** |  |
> | 動作 | Microsoft.VisualStudio/Project/Write | 設定專案 |
> | 動作 | Microsoft.VisualStudio/Project/Delete | 刪除專案 |
> | 動作 | Microsoft.VisualStudio/Project/Read | 讀取專案 |

## <a name="microsoftweb"></a>microsoft.web

Azure 服務： [App Service](../app-service/index.yml)、[函數](../azure-functions/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> | 動作 | microsoft.web/unregister/action | 針對訂用帳戶取消註冊 Microsoft.Web 資源提供者。 |
> | 動作 | microsoft.web/validate/action | 驗證。 |
> | 動作 | microsoft.web/register/action | 針對訂用帳戶註冊 Microsoft.Web 資源提供者。 |
> | 動作 | microsoft.web/verifyhostingenvironmentvnet/action | 確認裝載環境 Vnet。 |
> |  | **apimanagementaccounts/apiacl** |  |
> | 動作 | microsoft.web/apimanagementaccounts/apiacls/read | 取得 API 管理帳戶 Apiacl。 |
> |  | **apimanagementaccounts/api** |  |
> | 動作 | microsoft.web/apimanagementaccounts/apis/read | 取得 API 管理帳戶 API。 |
> | 動作 | microsoft.web/apimanagementaccounts/apis/delete | 刪除 API 管理帳戶 API。 |
> | 動作 | microsoft.web/apimanagementaccounts/apis/write | 更新 API 管理帳戶 API。 |
> |  | **apimanagementaccounts/api/apiacl** |  |
> | 動作 | microsoft.web/apimanagementaccounts/apis/apiacls/delete | 刪除 API 管理帳戶 API Apiacl。 |
> | 動作 | microsoft.web/apimanagementaccounts/apis/apiacls/read | 讀取 API 管理帳戶 API Apiacl。 |
> | 動作 | microsoft.web/apimanagementaccounts/apis/apiacls/write | 更新 API 管理帳戶 API Apiacl。 |
> |  | **apimanagementaccounts/api/connectionacl** |  |
> | 動作 | microsoft.web/apimanagementaccounts/apis/connectionacls/read | 取得 API 管理帳戶 API Connectionacl。 |
> |  | **apimanagementaccounts/api/連線** |  |
> | 動作 | microsoft.web/apimanagementaccounts/apis/connections/read | 取得 API 管理帳戶 API 連線。 |
> | 動作 | microsoft.web/apimanagementaccounts/apis/connections/confirmconsentcode/action | 確認同意碼 API 管理帳戶 API 連線。 |
> | 動作 | microsoft.web/apimanagementaccounts/apis/connections/delete | 刪除 API 管理帳戶 API 連線。 |
> | 動作 | microsoft.web/apimanagementaccounts/apis/connections/getconsentlinks/action | 取得 API 管理帳戶 API 連線的同意連結。 |
> | 動作 | microsoft.web/apimanagementaccounts/apis/connections/write | 更新 API 管理帳戶 API 連線。 |
> | 動作 | microsoft.web/apimanagementaccounts/apis/connections/listconnectionkeys/action | 列出 API 管理帳戶 API 連線的連線金鑰。 |
> | 動作 | microsoft.web/apimanagementaccounts/apis/connections/listsecrets/action | 列出 API 管理帳戶 API 連線的祕密。 |
> |  | **apimanagementaccounts/api/connections/connectionacl** |  |
> | 動作 | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/delete | 刪除 API 管理帳戶 API 連線 Connectionacl。 |
> | 動作 | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/read | 取得 API 管理帳戶 API 連線 Connectionacl。 |
> | 動作 | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/write | 更新 API 管理帳戶 API 連線 Connectionacl。 |
> |  | **apimanagementaccounts/api/localizeddefinitions** |  |
> | 動作 | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/delete | 刪除 API 管理帳戶 API 已當地語系化的定義。 |
> | 動作 | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/read | 取得 API 管理帳戶 API 已當地語系化的定義。 |
> | 動作 | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/write | 更新 API 管理帳戶 API 已當地語系化的定義。 |
> |  | **apimanagementaccounts/connectionacl** |  |
> | 動作 | microsoft.web/apimanagementaccounts/connectionacls/read | 取得 API 管理帳戶 Connectionacl。 |
> |  | **availablestacks** |  |
> | 動作 | microsoft.web/availablestacks/read | 取得可用堆疊。 |
> |  | **數位憑證** |  |
> | 動作 | Microsoft.Web/certificates/Read | 取得憑證清單。 |
> | 動作 | Microsoft.Web/certificates/Write | 新增憑證或更新現有憑證。 |
> | 動作 | Microsoft.Web/certificates/Delete | 刪除現有憑證。 |
> |  | **checknameavailability** |  |
> | 動作 | microsoft.web/checknameavailability/read | 檢查資源名稱是否可供使用。 |
> |  | **classicmobileservices** |  |
> | 動作 | microsoft.web/classicmobileservices/read | 取得傳統的行動服務。 |
> |  | **connectionGateways** |  |
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
> |  | **customApis** |  |
> | 動作 | Microsoft.Web/customApis/Read | 取得自訂 API 的清單。 |
> | 動作 | Microsoft.Web/customApis/Write | 建立或更新自訂 API。 |
> | 動作 | Microsoft.Web/customApis/Delete | 刪除自訂 API。 |
> | 動作 | Microsoft.Web/customApis/Move/Action | 移動自訂 API。 |
> | 動作 | Microsoft.Web/customApis/Join/Action | 加入自訂 API。 |
> | 動作 | Microsoft.Web/customApis/extractApiDefinitionFromWsdl/Action | 從 WSDL 中擷取 API 定義。 |
> | 動作 | Microsoft.Web/customApis/listWsdlInterfaces/Action | 列出自訂 API 的 WSDL 介面。 |
> |  | **deletedSites** |  |
> | 動作 | Microsoft.Web/deletedSites/Read | 取得所刪除 Web 應用程式的屬性 |
> |  | **deploymentlocations** |  |
> | 動作 | microsoft.web/deploymentlocations/read | 取得部署位置。 |
> |  | **geoRegions** |  |
> | 動作 | Microsoft.Web/geoRegions/Read | 取得地理區域清單。 |
> |  | **hostingEnvironments** |  |
> | 動作 | Microsoft.Web/hostingEnvironments/Read | 取得 App Service 環境的屬性 |
> | 動作 | Microsoft.Web/hostingEnvironments/Write | 建立新的 App Service 環境，或更新現有的 App Service 環境 |
> | 動作 | Microsoft.Web/hostingEnvironments/Delete | 刪除 App Service 環境 |
> | 動作 | Microsoft Web/hostingEnvironments/Join/Action | 加入 App Service 環境 |
> | 動作 | Microsoft.Web/hostingEnvironments/reboot/Action | 將 App Service 環境中的所有機器重新開機 |
> | 動作 | Microsoft Web/hostingEnvironments/PrivateEndpointConnectionsApproval/action | 核准私人端點連接 |
> | 動作 | microsoft.web/hostingenvironments/resume/action | 繼續裝載環境。 |
> | 動作 | microsoft.web/hostingenvironments/suspend/action | 暫止裝載環境。 |
> |  | **hostingenvironments/容量** |  |
> | 動作 | microsoft.web/hostingenvironments/capacities/read | 取得裝載環境容量。 |
> |  | **hostingenvironments/偵測器** |  |
> | 動作 | microsoft.web/hostingenvironments/detectors/read | 取得裝載環境偵測器。 |
> |  | **hostingenvironments/診斷** |  |
> | 動作 | microsoft.web/hostingenvironments/diagnostics/read | 取得裝載環境診斷。 |
> |  | **hostingEnvironments/eventGridFilters** |  |
> | 動作 | Microsoft Web/hostingEnvironments/eventGridFilters/delete | 在主控環境中刪除事件方格篩選。 |
> | 動作 | Microsoft Web/hostingEnvironments/eventGridFilters/read | 取得裝載環境的事件格線篩選。 |
> | 動作 | Microsoft Web/hostingEnvironments/eventGridFilters/write | 將事件方格篩選放在主控環境中。 |
> |  | **hostingenvironments/inboundnetworkdependenciesendpoints** |  |
> | 動作 | microsoft.web/hostingenvironments/inboundnetworkdependenciesendpoints/read | 取得所有輸入相依性的網路端點。 |
> |  | **hostingenvironments/metricdefinitions.listasync** |  |
> | 動作 | microsoft.web/hostingenvironments/metricdefinitions/read | 取得裝載環境的計量定義。 |
> |  | **hostingEnvironments/multiRolePools** |  |
> | 動作 | Microsoft.Web/hostingEnvironments/multiRolePools/Read | 取得 App Service 環境中前端集區的屬性 |
> | 動作 | Microsoft.Web/hostingEnvironments/multiRolePools/Write | 在 App Service 環境中建立新的前端集區，或更新 App Service 環境中現有的前端集區 |
> |  | **hostingenvironments/multirolepools/metricdefinitions.listasync** |  |
> | 動作 | microsoft.web/hostingenvironments/multirolepools/metricdefinitions/read | 取得裝載環境 MultiRole 集區計量定義。 |
> |  | **hostingenvironments/multirolepools/計量** |  |
> | 動作 | microsoft.web/hostingenvironments/multirolepools/metrics/read | 取得裝載環境 MultiRole 集區計量。 |
> |  | **hostingenvironments/multirolepools/sku** |  |
> | 動作 | microsoft.web/hostingenvironments/multirolepools/skus/read | 取得裝載環境 MultiRole 集區 SKU。 |
> |  | **hostingenvironments/multirolepools/使用方式** |  |
> | 動作 | microsoft.web/hostingenvironments/multirolepools/usages/read | 取得裝載環境 MultiRole 集區使用量。 |
> |  | **hostingenvironments/作業** |  |
> | 動作 | microsoft.web/hostingenvironments/operations/read | 取得裝載環境作業。 |
> |  | **hostingenvironments/outboundnetworkdependenciesendpoints** |  |
> | 動作 | microsoft.web/hostingenvironments/outboundnetworkdependenciesendpoints/read | 取得所有輸出相依性的網路端點。 |
> |  | **hostingenvironments/serverfarms** |  |
> | 動作 | microsoft.web/hostingenvironments/serverfarms/read | 取得裝載環境 App Service 方案。 |
> |  | **hostingenvironments/sites** |  |
> | 動作 | microsoft.web/hostingenvironments/sites/read | 取得裝載環境 Web Apps。 |
> |  | **hostingenvironments/使用方式** |  |
> | 動作 | microsoft.web/hostingenvironments/usages/read | 取得裝載環境使用量。 |
> |  | **hostingEnvironments/workerPools** |  |
> | 動作 | Microsoft.Web/hostingEnvironments/workerPools/Read | 取得 App Service 環境中背景工作集區的屬性 |
> | 動作 | Microsoft.Web/hostingEnvironments/workerPools/Write | 在 App Service 環境中建立新的背景工作集區，或更新 App Service 環境中現有的背景工作集區 |
> |  | **hostingenvironments/workerpools/metricdefinitions.listasync** |  |
> | 動作 | microsoft.web/hostingenvironments/workerpools/metricdefinitions/read | 取得裝載環境 Workerpools 計量定義。 |
> |  | **hostingenvironments/workerpools/計量** |  |
> | 動作 | microsoft.web/hostingenvironments/workerpools/metrics/read | 取得裝載環境 Workerpools 計量。 |
> |  | **hostingenvironments/workerpools/sku** |  |
> | 動作 | microsoft.web/hostingenvironments/workerpools/skus/read | 取得裝載環境 Workerpools SKU。 |
> |  | **hostingenvironments/workerpools/使用方式** |  |
> | 動作 | microsoft.web/hostingenvironments/workerpools/usages/read | 取得裝載環境 Workerpools 使用量。 |
> |  | **ishostingenvironmentnameavailable** |  |
> | 動作 | microsoft.web/ishostingenvironmentnameavailable/read | 取得裝載環境名稱是否可供使用。 |
> |  | **ishostnameavailable** |  |
> | 動作 | microsoft.web/ishostnameavailable/read | 檢查主機名稱是否可供使用。 |
> |  | **isusernameavailable** |  |
> | 動作 | microsoft.web/isusernameavailable/read | 檢查使用者名稱是否可供使用。 |
> |  | **listSitesAssignedToHostName** |  |
> | 動作 | Microsoft.Web/listSitesAssignedToHostName/Read | 取得指派給主機名稱之網站的名稱。 |
> |  | **堆放** |  |
> | 動作 | microsoft.web/locations/extractapidefinitionfromwsdl/action | 針對位置從 WSDL 中擷取 API 定義。 |
> | 動作 | microsoft.web/locations/listwsdlinterfaces/action | 列出適用於位置的 WSDL 介面。 |
> | 動作 | microsoft.web/locations/deleteVirtualNetworkOrSubnets/action | 位置的虛擬網路或子網路刪除通知。 |
> |  | **位置/apioperations** |  |
> | 動作 | microsoft.web/locations/apioperations/read | 取得位置 API 作業。 |
> |  | **位置/connectiongatewayinstallations** |  |
> | 動作 | microsoft.web/locations/connectiongatewayinstallations/read | 取得位置連線閘道器安裝。 |
> |  | **位置/managedapis** |  |
> | 動作 | microsoft.web/locations/managedapis/read | 取得位置管理的 API。 |
> | 動作 | Microsoft.Web/locations/managedapis/Join/Action | 加入受控 API。 |
> |  | **位置/managedapis/apioperations** |  |
> | 動作 | microsoft.web/locations/managedapis/apioperations/read | 取得位置受控 API 作業。 |
> |  | **位置/operationResults** |  |
> | 動作 | microsoft. web/位置/operationResults/讀取 | 取得作業。 |
> |  | **位置/作業** |  |
> | 動作 | microsoft. web/位置/作業/讀取 | 取得作業。 |
> |  | **業務** |  |
> | 動作 | microsoft.web/operations/read | 取得作業。 |
> |  | **publishingusers** |  |
> | 動作 | microsoft.web/publishingusers/read | 取得發佈使用者。 |
> | 動作 | microsoft.web/publishingusers/write | 更新發佈使用者。 |
> |  | **相關** |  |
> | 動作 | Microsoft.Web/recommendations/Read | 取得訂用帳戶的建議清單。 |
> |  | **resourcehealthmetadata** |  |
> | 動作 | microsoft.web/resourcehealthmetadata/read | 取得資源健康狀態中繼資料。 |
> |  | **serverfarms** |  |
> | 動作 | Microsoft.Web/serverfarms/Read | 取得 App Service 方案的屬性 |
> | 動作 | Microsoft.Web/serverfarms/Write | 建立新的 App Service 方案，或更新現有的 App Service 方案 |
> | 動作 | Microsoft.Web/serverfarms/Delete | 刪除現有的 App Service 方案 |
> | 動作 | Microsoft.Web/serverfarms/restartSites/Action | 重新啟動 App Service 方案中的所有 Web Apps |
> |  | **serverfarms/功能** |  |
> | 動作 | microsoft.web/serverfarms/capabilities/read | 取得 App Service 方案的容量。 |
> |  | **serverfarms/eventGridFilters** |  |
> | 動作 | Microsoft Web/serverfarms/eventGridFilters/delete | 刪除伺服器陣列上的事件格線篩選。 |
> | 動作 | Microsoft Web/serverfarms/eventGridFilters/read | 取得伺服器陣列上的事件格線篩選。 |
> | 動作 | Microsoft Web/serverfarms/eventGridFilters/write | 將事件方格篩選放在伺服器陣列上。 |
> |  | **serverfarms/firstpartyapps/settings** |  |
> | 動作 | microsoft.web/serverfarms/firstpartyapps/settings/delete | 刪除 App Service 方案的第一方應用程式設定。 |
> | 動作 | microsoft.web/serverfarms/firstpartyapps/settings/read | 取得 App Service 方案的第一方應用程式設定。 |
> | 動作 | microsoft.web/serverfarms/firstpartyapps/settings/write | 更新 App Service 方案的第一方應用程式設定。 |
> |  | **serverfarms/hybridconnectionnamespaces/轉送** |  |
> | 動作 | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/read | 取得 App Service 方案的混合式連線命名空間轉送。 |
> | 動作 | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/delete | 刪除 App Service 方案的混合式連線命名空間轉送。 |
> |  | **serverfarms/hybridconnectionnamespaces/轉送/網站** |  |
> | 動作 | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/sites/read | 取得 App Service 方案的混合式連線命名空間轉送 Web Apps。 |
> |  | **serverfarms/hybridconnectionplanlimits** |  |
> | 動作 | microsoft.web/serverfarms/hybridconnectionplanlimits/read | 取得 App Service 方案的混合式連線方案限制。 |
> |  | **serverfarms/hybridconnectionrelays** |  |
> | 動作 | microsoft.web/serverfarms/hybridconnectionrelays/read | 取得 App Service 方案的混合式連線轉送。 |
> |  | **serverfarms/metricdefinitions.listasync** |  |
> | 動作 | microsoft.web/serverfarms/metricdefinitions/read | 取得 App Service 方案的計量定義。 |
> |  | **serverfarms/計量** |  |
> | 動作 | microsoft.web/serverfarms/metrics/read | 取得 App Service 方案的計量。 |
> |  | **serverfarms/operationresults** |  |
> | 動作 | microsoft.web/serverfarms/operationresults/read | 取得 App Service 方案的作業結果。 |
> |  | **serverfarms/sites** |  |
> | 動作 | microsoft.web/serverfarms/sites/read | 取得 App Service 方案的 Web Apps。 |
> |  | **serverfarms/sku** |  |
> | 動作 | microsoft.web/serverfarms/skus/read | 取得 App Service 方案 SKU。 |
> |  | **serverfarms/使用方式** |  |
> | 動作 | microsoft.web/serverfarms/usages/read | 取得 App Service 方案的使用量。 |
> |  | **serverfarms/virtualnetworkconnections** |  |
> | 動作 | microsoft.web/serverfarms/virtualnetworkconnections/read | 取得 App Service 方案的虛擬網路連線。 |
> |  | **serverfarms/virtualnetworkconnections/閘道** |  |
> | 動作 | microsoft.web/serverfarms/virtualnetworkconnections/gateways/write | 更新 App Service 方案的虛擬網路連線閘道器。 |
> |  | **serverfarms/virtualnetworkconnections/route** |  |
> | 動作 | microsoft.web/serverfarms/virtualnetworkconnections/routes/delete | 刪除 App Service 方案的虛擬網路連線路由。 |
> | 動作 | microsoft.web/serverfarms/virtualnetworkconnections/routes/read | 取得 App Service 方案的虛擬網路連線路由。 |
> | 動作 | microsoft.web/serverfarms/virtualnetworkconnections/routes/write | 更新 App Service 方案的虛擬網路連線路由。 |
> |  | **serverfarms/背景工作** |  |
> | 動作 | microsoft.web/serverfarms/workers/reboot/action | 重新啟動 App Service 方案的背景工作角色。 |
> |  | **網站** |  |
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
> | 動作 | Microsoft Web/sites/PrivateEndpointConnectionsApproval/action | 核准私人端點連接 |
> | 動作 | microsoft.web/sites/functions/action | 函式 Web Apps。 |
> | 動作 | microsoft.web/sites/listsyncfunctiontriggerstatus/action | 列出同步處理函數觸發程式狀態。 |
> | 動作 | microsoft.web/sites/networktrace/action | 網路追蹤 Web Apps。 |
> | 動作 | microsoft.web/sites/newpassword/action | Newpassword Web Apps。 |
> | 動作 | microsoft.web/sites/sync/action | 同步處理 Web Apps。 |
> | 動作 | microsoft.web/sites/migratemysql/action | 移轉 MySql Web Apps。 |
> | 動作 | microsoft.web/sites/recover/action | 復原 Web Apps。 |
> | 動作 | microsoft.web/sites/restoresnapshot/action | 還原 Web Apps 快照集。 |
> | 動作 | microsoft web/sites/restorefromdeletedapp/action | 從已刪除的應用程式還原 Web Apps。 |
> | 動作 | microsoft.web/sites/syncfunctiontriggers/action | 同步處理函數觸發程式。 |
> | 動作 | microsoft.web/sites/backups/action | 探索可從 Azure 儲存體中 Blob 還原的現有應用程式備份。 |
> | 動作 | microsoft.web/sites/containerlogs/action | 取得 Web 應用程式的壓縮容器記錄。 |
> | 動作 | microsoft.web/sites/restorefrombackupblob/action | 從備份 Blob 還原 Web 應用程式。 |
> |  | **sites/analyzecustomhostname** |  |
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
> |  | **sites/config** |  |
> | 動作 | Microsoft.Web/sites/config/Read | 取得 Web 應用程式的組態設定 |
> | 動作 | Microsoft.Web/sites/config/list/Action | 列出 Web 應用程式的安全性機密設定，例如發佈認證、應用程式設定和連接字串 |
> | 動作 | Microsoft.Web/sites/config/Write | 更新 Web 應用程式的組態設定 |
> | 動作 | microsoft.web/sites/config/delete | 刪除 Web Apps 設定。 |
> |  | **網站/設定/快照集** |  |
> | 動作 | microsoft.web/sites/config/snapshots/read | 取得 Web Apps 組態快照集。 |
> |  | **sites/containerlogs** |  |
> | 動作 | microsoft.web/sites/containerlogs/download/action | 下載 Web Apps 容器記錄。 |
> |  | **sites/continuouswebjobs** |  |
> | 動作 | microsoft.web/sites/continuouswebjobs/delete | 刪除 Web Apps 的連續 Web 作業。 |
> | 動作 | microsoft.web/sites/continuouswebjobs/read | 取得 Web Apps 的連續 Web 作業。 |
> | 動作 | microsoft.web/sites/continuouswebjobs/start/action | 啟動 Web Apps 的連續 Web 作業。 |
> | 動作 | microsoft.web/sites/continuouswebjobs/stop/action | 停止 Web Apps 的連續 Web 作業。 |
> |  | **網站/部署** |  |
> | 動作 | microsoft.web/sites/deployments/delete | 刪除 Web Apps 部署。 |
> | 動作 | microsoft.web/sites/deployments/read | 取得 Web Apps 部署。 |
> | 動作 | microsoft.web/sites/deployments/write | 更新 Web Apps 部署。 |
> |  | **網站/部署/記錄** |  |
> | 動作 | microsoft.web/sites/deployments/log/read | 取得 Web Apps 部署記錄。 |
> |  | **sites/偵測器** |  |
> | 動作 | microsoft.web/sites/detectors/read | 取得 Web Apps 偵測器。 |
> |  | **網站/診斷** |  |
> | 動作 | microsoft.web/sites/diagnostics/read | 取得 Web Apps 診斷類別。 |
> |  | **網站/診斷/分析** |  |
> | 動作 | microsoft.web/sites/diagnostics/analyses/read | 取得 Web Apps 診斷分析。 |
> | 動作 | microsoft.web/sites/diagnostics/analyses/execute/Action | 執行 Web Apps 診斷分析。 |
> |  | **網站/診斷/aspnetcore** |  |
> | 動作 | microsoft.web/sites/diagnostics/aspnetcore/read | 取得 ASP.NET Core 應用程式的 Web Apps 診斷。 |
> |  | **網站/診斷/autoheal** |  |
> | 動作 | microsoft.web/sites/diagnostics/autoheal/read | 取得 Web Apps 診斷 Autoheal。 |
> |  | **網站/診斷/部署** |  |
> | 動作 | microsoft.web/sites/diagnostics/deployment/read | 取得 Web Apps 診斷部署。 |
> |  | **網站/診斷/部署** |  |
> | 動作 | microsoft.web/sites/diagnostics/deployments/read | 取得 Web Apps 診斷部署。 |
> |  | **網站/診斷/偵測器** |  |
> | 動作 | microsoft.web/sites/diagnostics/detectors/read | 取得 Web Apps 診斷偵測器。 |
> | 動作 | microsoft.web/sites/diagnostics/detectors/execute/Action | 執行 Web Apps 診斷偵測器。 |
> |  | **網站/診斷/failedrequestsperuri** |  |
> | 動作 | microsoft.web/sites/diagnostics/failedrequestsperuri/read | 取得 Web Apps 診斷對於每個 URI 的失敗要求。 |
> |  | **網站/診斷/frebanalysis** |  |
> | 動作 | microsoft.web/sites/diagnostics/frebanalysis/read | 取得 Web Apps 診斷的 FREB 分析。 |
> |  | **網站/診斷/loganalyzer** |  |
> | 動作 | microsoft.web/sites/diagnostics/loganalyzer/read | 取得 Web Apps 診斷的記錄分析器。 |
> |  | **網站/診斷/runtimeavailability** |  |
> | 動作 | microsoft.web/sites/diagnostics/runtimeavailability/read | 取得 Web Apps 診斷的執行階段可用性。 |
> |  | **網站/診斷/servicehealth** |  |
> | 動作 | microsoft.web/sites/diagnostics/servicehealth/read | 取得 Web Apps 診斷的服務健康狀態。 |
> |  | **網站/診斷/sitecpuanalysis** |  |
> | 動作 | microsoft.web/sites/diagnostics/sitecpuanalysis/read | 取得 Web Apps 診斷的網站 CPU 分析。 |
> |  | **網站/診斷/sitecrashes** |  |
> | 動作 | microsoft.web/sites/diagnostics/sitecrashes/read | 取得 Web Apps 診斷的網站損毀。 |
> |  | **網站/診斷/sitelatency** |  |
> | 動作 | microsoft.web/sites/diagnostics/sitelatency/read | 取得 Web Apps 診斷的網站延遲。 |
> |  | **網站/診斷/sitememoryanalysis** |  |
> | 動作 | microsoft.web/sites/diagnostics/sitememoryanalysis/read | 取得 Web Apps 診斷的網站記憶體分析。 |
> |  | **網站/診斷/siterestartsettingupdate** |  |
> | 動作 | microsoft.web/sites/diagnostics/siterestartsettingupdate/read | 取得 Web Apps 診斷的網站重新啟動設定更新。 |
> |  | **網站/診斷/siterestartuserinitiated** |  |
> | 動作 | microsoft.web/sites/diagnostics/siterestartuserinitiated/read | 取得 Web Apps 診斷中使用者初始的網站重新啟動。 |
> |  | **網站/診斷/siteswap** |  |
> | 動作 | microsoft.web/sites/diagnostics/siteswap/read | 取得 Web Apps 診斷的網站交換。 |
> |  | **網站/診斷/threadcount** |  |
> | 動作 | microsoft.web/sites/diagnostics/threadcount/read | 取得 Web Apps 診斷的執行緒計數。 |
> |  | **網站/診斷/workeravailability** |  |
> | 動作 | microsoft.web/sites/diagnostics/workeravailability/read | 取得 Web Apps 診斷的 Workeravailability。 |
> |  | **網站/診斷/workerprocessrecycle** |  |
> | 動作 | microsoft.web/sites/diagnostics/workerprocessrecycle/read | 取得 Web Apps 診斷的背景工作處理序回收。 |
> |  | **sites/domainownershipidentifiers** |  |
> | 動作 | microsoft.web/sites/domainownershipidentifiers/read | 取得 Web Apps 網域擁有權識別碼。 |
> | 動作 | microsoft.web/sites/domainownershipidentifiers/write | 更新 Web Apps 網域擁有權識別碼。 |
> |  | **sites/eventGridFilters** |  |
> | 動作 | Microsoft Web/sites/eventGridFilters/delete | 刪除 web 應用程式上的事件格線篩選。 |
> | 動作 | Microsoft Web/sites/eventGridFilters/read | 取得 web 應用程式的事件格線篩選。 |
> | 動作 | Microsoft Web/sites/eventGridFilters/write | 將事件方格篩選放在 web 應用程式上。 |
> |  | **網站/擴充功能** |  |
> | 動作 | microsoft web/sites/extensions/delete | 刪除 Web Apps 的網站擴充功能。 |
> | 動作 | microsoft 網站/網站/延伸模組/讀取 | 取得 Web Apps 的網站擴充功能。 |
> | 動作 | microsoft web/sites/extensions/write | 更新 Web Apps 的網站擴充功能。 |
> |  | **網站/功能** |  |
> | 動作 | microsoft.web/sites/functions/delete | 刪除 Web Apps 函式。 |
> | 動作 | microsoft.web/sites/functions/listsecrets/action | 列出函數秘密。 |
> | 動作 | microsoft web/sites/函數/listkeys/action | 列出功能鍵。 |
> | 動作 | microsoft.web/sites/functions/read | 取得 Web Apps 函式。 |
> | 動作 | microsoft.web/sites/functions/write | 更新 Web Apps 函式。 |
> |  | **sites/函數/金鑰** |  |
> | 動作 | microsoft web/sites/函數/金鑰/寫入 | 更新功能鍵。 |
> | 動作 | microsoft web/sites/函數/金鑰/刪除 | 刪除功能鍵。 |
> |  | **sites/函數/masterkey** |  |
> | 動作 | microsoft.web/sites/functions/masterkey/read | 取得 Web Apps 函式的主要金鑰。 |
> |  | **網站/函數/屬性** |  |
> | 動作 | microsoft web/sites/函數/屬性/讀取 | 讀取 Web Apps 函數屬性。 |
> | 動作 | microsoft web/sites/函數/屬性/寫入 | 更新 Web Apps 函數屬性。 |
> |  | **網站/功能/權杖** |  |
> | 動作 | microsoft.web/sites/functions/token/read | 取得 Web Apps 函式的權杖。 |
> |  | **網站/主機** |  |
> | 動作 | microsoft web/sites/host/listkeys/action | 列出函式主機金鑰。 |
> | 動作 | microsoft 網站/網站/主機/同步處理/動作 | 同步處理函數觸發程式。 |
> | 動作 | microsoft web/sites/host/listsyncstatus/action | 列出同步處理函數觸發程式狀態。 |
> |  | **sites/主機/functionkeys** |  |
> | 動作 | microsoft 網站/網站/主機/functionkeys/寫入 | Update 函式主控功能鍵。 |
> | 動作 | microsoft web/sites/host/functionkeys/delete | Delete 函式主機功能鍵。 |
> |  | **網站/主機/屬性** |  |
> | 動作 | microsoft 網站/網站/主機/屬性/讀取 | 讀取 Web Apps 函式主機屬性。 |
> |  | **sites/主機/systemkeys** |  |
> | 動作 | microsoft 網站/網站/主機/systemkeys/寫入 | 更新函式主機系統金鑰。 |
> | 動作 | microsoft web/sites/host/systemkeys/delete | Delete 函數主機系統金鑰。 |
> |  | **sites/hostnamebindings** |  |
> | 動作 | microsoft.web/sites/hostnamebindings/delete | 刪除 Web Apps 的主機名稱繫結。 |
> | 動作 | microsoft.web/sites/hostnamebindings/read | 取得 Web Apps 的主機名稱繫結。 |
> | 動作 | microsoft.web/sites/hostnamebindings/write | 更新 Web Apps 的主機名稱繫結。 |
> |  | **sites/hostruntime** |  |
> | 動作 | Microsoft.Web/sites/hostruntime/host/action | 執行函式應用程式的執行階段動作，例如同步觸發程序、新增函式、叫用函式、刪除函式等動作。 |
> |  | **sites/hostruntime/函數/金鑰** |  |
> | 動作 | microsoft.web/sites/hostruntime/functions/keys/read | 取得 Web Apps Hostruntime 函式金鑰。 |
> |  | **sites/hostruntime/主機** |  |
> | 動作 | microsoft.web/sites/hostruntime/host/read | 取得 Web Apps Hostruntime 主機。 |
> |  | **sites/hostruntime/主機/_master** |  |
> | 動作 | Microsoft.Web/sites/hostruntime/host/_master/read | 取得系統管理作業的函式應用程式主要金鑰 |
> |  | **sites/hybridconnection** |  |
> | 動作 | microsoft.web/sites/hybridconnection/delete | 刪除 Web Apps 的混合式連線。 |
> | 動作 | microsoft.web/sites/hybridconnection/read | 取得 Web Apps 的混合式連線。 |
> | 動作 | microsoft.web/sites/hybridconnection/write | 更新 Web Apps 的混合式連線。 |
> |  | **sites/hybridconnectionnamespaces/轉送** |  |
> | 動作 | microsoft.web/sites/hybridconnectionnamespaces/relays/delete | 刪除 Web Apps 的混合式連線命名空間轉送。 |
> | 動作 | microsoft.web/sites/hybridconnectionnamespaces/relays/listkeys/action | 列出 Web Apps 的混合式連線命名空間轉送。 |
> | 動作 | microsoft.web/sites/hybridconnectionnamespaces/relays/write | 更新 Web Apps 的混合式連線命名空間轉送。 |
> | 動作 | microsoft.web/sites/hybridconnectionnamespaces/relays/read | 取得 Web Apps 的混合式連線命名空間轉送。 |
> |  | **sites/hybridconnectionrelays** |  |
> | 動作 | microsoft.web/sites/hybridconnectionrelays/read | 取得 Web Apps 的混合式連線轉送。 |
> |  | **網站/實例** |  |
> | 動作 | microsoft.web/sites/instances/read | 取得 Web Apps 執行個體。 |
> |  | **網站/實例/部署** |  |
> | 動作 | microsoft.web/sites/instances/deployments/read | 取得 Web Apps 的執行個體部署。 |
> | 動作 | microsoft.web/sites/instances/deployments/delete | 刪除 Web Apps 的執行個體部署。 |
> |  | **網站/實例/擴充功能** |  |
> | 動作 | microsoft.web/sites/instances/extensions/read | 取得 Web Apps 的執行個體擴充功能。 |
> |  | **網站/實例/延伸模組/記錄** |  |
> | 動作 | microsoft.web/sites/instances/extensions/log/read | 取得 Web Apps 的執行個體擴充功能記錄。 |
> |  | **網站/實例/擴充功能/處理常式** |  |
> | 動作 | microsoft.web/sites/instances/extensions/processes/read | 取得 Web Apps 執行個體擴充功能程序。 |
> |  | **網站/實例/進程** |  |
> | 動作 | microsoft.web/sites/instances/processes/delete | 刪除 Web Apps 的執行個體處理序。 |
> | 動作 | microsoft.web/sites/instances/processes/read | 取得 Web Apps 的執行個體處理序。 |
> |  | **網站/實例/進程/模組** |  |
> | 動作 | microsoft.web/sites/instances/processes/modules/read | 取得 Web Apps 執行個體處理程序模組。 |
> |  | **網站/實例/進程/執行緒** |  |
> | 動作 | microsoft.web/sites/instances/processes/threads/read | 取得 Web Apps 執行個體處理程序的執行緒。 |
> |  | **sites/metricdefinitions.listasync** |  |
> | 動作 | microsoft.web/sites/metricdefinitions/read | 取得 Web Apps 計量定義。 |
> |  | **網站/計量** |  |
> | 動作 | microsoft.web/sites/metrics/read | 取得 Web Apps 計量。 |
> |  | **sites/metricsdefinitions** |  |
> | 動作 | microsoft.web/sites/metricsdefinitions/read | 取得 Web Apps 計量定義。 |
> |  | **sites/migratemysql** |  |
> | 動作 | microsoft.web/sites/migratemysql/read | 取得 Web Apps 的移轉 MySql。 |
> |  | **sites/networktraces/operationresults** |  |
> | 動作 | microsoft web/sites/networktraces/operationresults/read | 取得 Web Apps 網路追蹤作業結果。 |
> |  | **sites/operationresults** |  |
> | 動作 | microsoft.web/sites/operationresults/read | 取得 Web Apps 的作業結果。 |
> |  | **網站/作業** |  |
> | 動作 | microsoft.web/sites/operations/read | 取得 Web Apps 作業。 |
> |  | **sites/perfcounters** |  |
> | 動作 | microsoft.web/sites/perfcounters/read | 取得 Web Apps 的效能計數器。 |
> |  | **sites/premieraddons** |  |
> | 動作 | microsoft.web/sites/premieraddons/delete | 刪除 Web Apps 的頂級附加元件。 |
> | 動作 | microsoft.web/sites/premieraddons/read | 取得 Web Apps 的頂級附加元件。 |
> | 動作 | microsoft.web/sites/premieraddons/write | 更新 Web Apps 的頂級附加元件。 |
> |  | **sites/privateaccess** |  |
> | 動作 | microsoft.web/sites/privateaccess/read | 取得私人網站存取啟用和已授權虛擬網路 (可存取該網站) 的資料。 |
> |  | **網站/處理常式** |  |
> | 動作 | microsoft.web/sites/processes/read | 取得 Web Apps 處理序。 |
> |  | **網站/進程/模組** |  |
> | 動作 | microsoft web/sites/進程/模組/讀取 | 取得 Web Apps 處理模組。 |
> |  | **網站/進程/執行緒** |  |
> | 動作 | microsoft web/sites/進程/執行緒/讀取 | 取得 Web Apps 處理執行緒。 |
> |  | **sites/publiccertificates** |  |
> | 動作 | microsoft.web/sites/publiccertificates/delete | 刪除 Web Apps 的公開憑證。 |
> | 動作 | microsoft.web/sites/publiccertificates/read | 取得 Web Apps 的公開憑證。 |
> | 動作 | microsoft.web/sites/publiccertificates/write | 更新 Web Apps 的公開憑證。 |
> |  | **sites/publishxml** |  |
> | 動作 | microsoft.web/sites/publishxml/read | 取得 Web Apps 的發佈 XML。 |
> |  | **sites/recommendationhistory** |  |
> | 動作 | microsoft.web/sites/recommendationhistory/read | 取得 Web Apps 的建議歷程記錄。 |
> |  | **網站/建議** |  |
> | 動作 | Microsoft.Web/sites/recommendations/Read | 取得 Web 應用程式的建議清單。 |
> | 動作 | microsoft.web/sites/recommendations/disable/action | 停用 Web Apps 建議。 |
> |  | **sites/resourcehealthmetadata** |  |
> | 動作 | microsoft.web/sites/resourcehealthmetadata/read | 取得 Web Apps 的資源健康狀態中繼資料。 |
> |  | **網站/還原** |  |
> | 動作 | microsoft.web/sites/restore/read | 取得 Web Apps 還原。 |
> | 動作 | microsoft.web/sites/restore/write | 還原 Web Apps。 |
> |  | **sites/siteextensions** |  |
> | 動作 | microsoft.web/sites/siteextensions/delete | 刪除 Web Apps 的網站擴充功能。 |
> | 動作 | microsoft.web/sites/siteextensions/read | 取得 Web Apps 的網站擴充功能。 |
> | 動作 | microsoft.web/sites/siteextensions/write | 更新 Web Apps 的網站擴充功能。 |
> |  | **網站/位置** |  |
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
> | 動作 | microsoft web/sites/位置/restorefromdeletedapp/動作 | 從已刪除的應用程式還原 Web 應用程式位置。 |
> | 動作 | microsoft.web/sites/slots/backups/action | 探索 Web Apps 位置備份。 |
> | 動作 | microsoft.web/sites/slots/containerlogs/action | 取得 Web 應用程式位置的壓縮容器記錄。 |
> | 動作 | microsoft.web/sites/slots/restorefrombackupblob/action | 從備份 Blob 還原 Web 應用程式位置。 |
> |  | **sites/位置/analyzecustomhostname** |  |
> | 動作 | microsoft.web/sites/slots/analyzecustomhostname/read | 取得 Web Apps 位置的分析自訂主機名稱。 |
> |  | **網站/位置/備份** |  |
> | 動作 | microsoft.web/sites/slots/backup/write | 更新 Web Apps 位置的備份。 |
> | 動作 | microsoft.web/sites/slots/backup/read | 取得 Web Apps 位置備份。 |
> |  | **網站/位置/備份** |  |
> | 動作 | Microsoft.Web/sites/slots/backups/Read | 取得 Web 應用程式位置備份的屬性 |
> | 動作 | microsoft.web/sites/slots/backups/list/action | 列出 Web Apps 位置的備份。 |
> | 動作 | microsoft.web/sites/slots/backups/restore/action | 還原 Web Apps 位置的備份。 |
> | 動作 | microsoft.web/sites/slots/backups/delete | 刪除 Web Apps 位置備份。 |
> |  | **網站/位置/設定** |  |
> | 動作 | Microsoft.Web/sites/slots/config/Read | 取得 Web 應用程式位置的組態設定 |
> | 動作 | Microsoft.Web/sites/slots/config/list/Action | 列出 Web 應用程式位置的安全性機密設定，例如發佈認證、應用程式設定和連接字串 |
> | 動作 | Microsoft.Web/sites/slots/config/Write | 更新 Web 應用程式位置的組態設定 |
> | 動作 | microsoft.web/sites/slots/config/delete | 刪除 Web Apps 的位置設定。 |
> |  | **sites/位置/containerlogs** |  |
> | 動作 | microsoft.web/sites/slots/containerlogs/download/action | 下載 Web Apps 位置容器記錄。 |
> |  | **sites/位置/continuouswebjobs** |  |
> | 動作 | microsoft.web/sites/slots/continuouswebjobs/delete | 刪除 Web Apps 位置的連續 Web 作業。 |
> | 動作 | microsoft.web/sites/slots/continuouswebjobs/read | 取得 Web Apps 位置的連續 Web 作業。 |
> | 動作 | microsoft.web/sites/slots/continuouswebjobs/start/action | 啟動 Web Apps 位置的連續 Web 作業。 |
> | 動作 | microsoft.web/sites/slots/continuouswebjobs/stop/action | 停止 Web Apps 位置的連續 Web 作業。 |
> |  | **網站/位置/部署** |  |
> | 動作 | microsoft.web/sites/slots/deployments/delete | 刪除 Web Apps 的位置部署。 |
> | 動作 | microsoft.web/sites/slots/deployments/read | 取得 Web Apps 的位置部署。 |
> | 動作 | microsoft.web/sites/slots/deployments/write | 更新 Web Apps 的位置部署。 |
> |  | **網站/位置/部署/記錄** |  |
> | 動作 | microsoft.web/sites/slots/deployments/log/read | 取得 Web Apps 的位置部署記錄。 |
> |  | **sites/位置/偵測器** |  |
> | 動作 | microsoft.web/sites/slots/detectors/read | 取得 Web Apps 位置偵測器。 |
> |  | **網站/位置/診斷** |  |
> | 動作 | microsoft.web/sites/slots/diagnostics/read | 取得 Web Apps 位置診斷。 |
> |  | **網站/位置/診斷/分析** |  |
> | 動作 | microsoft.web/sites/slots/diagnostics/analyses/read | 取得 Web Apps 位置診斷的分析。 |
> | 動作 | microsoft.web/sites/slots/diagnostics/analyses/execute/Action | 執行 Web Apps 位置診斷的分析。 |
> |  | **sites/插槽/診斷/aspnetcore** |  |
> | 動作 | microsoft.web/sites/slots/diagnostics/aspnetcore/read | 取得 ASP.NET Core 應用程式的 Web Apps 位置診斷。 |
> |  | **sites/插槽/診斷/autoheal** |  |
> | 動作 | microsoft.web/sites/slots/diagnostics/autoheal/read | 取得 Web Apps 位置診斷的 Autoheal。 |
> |  | **網站/位置/診斷/部署** |  |
> | 動作 | microsoft.web/sites/slots/diagnostics/deployment/read | 取得 Web Apps 位置診斷的部署。 |
> |  | **網站/位置/診斷/部署** |  |
> | 動作 | microsoft.web/sites/slots/diagnostics/deployments/read | 取得 Web Apps 位置診斷的部署。 |
> |  | **sites/插槽/診斷/偵測器** |  |
> | 動作 | microsoft.web/sites/slots/diagnostics/detectors/read | 取得 Web Apps 位置診斷的偵測器。 |
> | 動作 | microsoft.web/sites/slots/diagnostics/detectors/execute/Action | 執行 Web Apps 位置診斷的偵測器。 |
> |  | **sites/插槽/診斷/frebanalysis** |  |
> | 動作 | microsoft.web/sites/slots/diagnostics/frebanalysis/read | 取得 Web Apps 位置診斷的 FREB 分析。 |
> |  | **sites/插槽/診斷/loganalyzer** |  |
> | 動作 | microsoft.web/sites/slots/diagnostics/loganalyzer/read | 取得 Web Apps 位置診斷的記錄分析器。 |
> |  | **sites/插槽/診斷/runtimeavailability** |  |
> | 動作 | microsoft.web/sites/slots/diagnostics/runtimeavailability/read | 取得 Web Apps 位置診斷的執行階段可用性。 |
> |  | **sites/插槽/診斷/servicehealth** |  |
> | 動作 | microsoft.web/sites/slots/diagnostics/servicehealth/read | 取得 Web Apps 位置診斷的服務健康狀態。 |
> |  | **sites/插槽/診斷/sitecpuanalysis** |  |
> | 動作 | microsoft.web/sites/slots/diagnostics/sitecpuanalysis/read | 取得 Web Apps 位置診斷的網站 CPU 分析。 |
> |  | **sites/插槽/診斷/sitecrashes** |  |
> | 動作 | microsoft.web/sites/slots/diagnostics/sitecrashes/read | 取得 Web Apps 位置診斷的網站損毀。 |
> |  | **sites/插槽/診斷/sitelatency** |  |
> | 動作 | microsoft.web/sites/slots/diagnostics/sitelatency/read | 取得 Web Apps 位置診斷的網站延遲。 |
> |  | **sites/插槽/診斷/sitememoryanalysis** |  |
> | 動作 | microsoft.web/sites/slots/diagnostics/sitememoryanalysis/read | 取得 Web Apps 位置診斷的網站記憶體分析。 |
> |  | **sites/插槽/診斷/siterestartsettingupdate** |  |
> | 動作 | microsoft.web/sites/slots/diagnostics/siterestartsettingupdate/read | 取得 Web Apps 位置診斷的網站重新啟動設定更新。 |
> |  | **sites/插槽/診斷/siterestartuserinitiated** |  |
> | 動作 | microsoft.web/sites/slots/diagnostics/siterestartuserinitiated/read | 取得 Web Apps 位置診斷中使用者初始的網站重新啟動。 |
> |  | **sites/插槽/診斷/siteswap** |  |
> | 動作 | microsoft.web/sites/slots/diagnostics/siteswap/read | 取得 Web Apps 位置診斷的網站交換。 |
> |  | **sites/插槽/診斷/threadcount** |  |
> | 動作 | microsoft.web/sites/slots/diagnostics/threadcount/read | 取得 Web Apps 位置診斷的執行緒計數。 |
> |  | **sites/插槽/診斷/workeravailability** |  |
> | 動作 | microsoft.web/sites/slots/diagnostics/workeravailability/read | 取得 Web Apps 位置診斷的 Workeravailability。 |
> |  | **sites/插槽/診斷/workerprocessrecycle** |  |
> | 動作 | microsoft.web/sites/slots/diagnostics/workerprocessrecycle/read | 取得 Web Apps 位置診斷的背景工作處理序回收。 |
> |  | **sites/位置/domainownershipidentifiers** |  |
> | 動作 | microsoft.web/sites/slots/domainownershipidentifiers/read | 取得 Web Apps 位置的網域擁有權識別碼。 |
> |  | **網站/位置/擴充功能** |  |
> | 動作 | microsoft web/sites/位置/分機/讀取 | 取得 Web Apps 插槽延伸模組。 |
> | 動作 | microsoft web/sites/位置/擴充功能/寫入 | 更新 Web Apps 位置延伸模組。 |
> |  | **網站/位置/功能** |  |
> | 動作 | microsoft.web/sites/slots/functions/read | 取得 Web Apps 位置函式。 |
> | 動作 | microsoft.web/sites/slots/functions/listsecrets/action | 列出祕密 Web Apps 位置函式。 |
> |  | **sites/位置/hostnamebindings** |  |
> | 動作 | microsoft.web/sites/slots/hostnamebindings/delete | 刪除 Web Apps 位置的主機名稱繫結。 |
> | 動作 | microsoft.web/sites/slots/hostnamebindings/read | 取得 Web Apps 位置的主機名稱繫結。 |
> | 動作 | microsoft.web/sites/slots/hostnamebindings/write | 更新 Web Apps 位置的主機名稱繫結。 |
> |  | **sites/位置/hybridconnection** |  |
> | 動作 | microsoft.web/sites/slots/hybridconnection/delete | 刪除 Web Apps 位置的混合式連線。 |
> | 動作 | microsoft.web/sites/slots/hybridconnection/read | 取得 Web Apps 位置的混合式連線。 |
> | 動作 | microsoft.web/sites/slots/hybridconnection/write | 更新 Web Apps 位置的混合式連線。 |
> |  | **sites/插槽/hybridconnectionnamespaces/轉送** |  |
> | 動作 | microsoft.web/sites/slots/hybridconnectionnamespaces/relays/delete | 刪除 Web Apps 位置的混合式連線命名空間轉送。 |
> | 動作 | microsoft.web/sites/slots/hybridconnectionnamespaces/relays/write | 更新 Web Apps 位置的混合式連線命名空間轉送。 |
> |  | **sites/位置/hybridconnectionrelays** |  |
> | 動作 | microsoft.web/sites/slots/hybridconnectionrelays/read | 取得 Web Apps 位置的混合式連線轉送。 |
> |  | **網站/位置/實例** |  |
> | 動作 | microsoft.web/sites/slots/instances/read | 取得 Web Apps 位置的執行個體。 |
> |  | **網站/位置/實例/部署** |  |
> | 動作 | microsoft.web/sites/slots/instances/deployments/read | 取得 Web Apps 位置的執行個體部署。 |
> |  | **網站/位置/實例/進程** |  |
> | 動作 | microsoft.web/sites/slots/instances/processes/read | 取得 Web Apps 位置的執行個體處理序。 |
> | 動作 | microsoft.web/sites/slots/instances/processes/delete | 刪除 Web Apps 位置的執行個體處理序。 |
> |  | **sites/位置/metricdefinitions.listasync** |  |
> | 動作 | microsoft.web/sites/slots/metricdefinitions/read | 取得 Web Apps 位置的計量定義。 |
> |  | **網站/位置/計量** |  |
> | 動作 | microsoft.web/sites/slots/metrics/read | 取得 Web Apps 位置的計量。 |
> |  | **sites/位置/migratemysql** |  |
> | 動作 | microsoft.web/sites/slots/migratemysql/read | 取得 Web Apps 位置的移轉 MySql。 |
> |  | **sites/插槽/networktraces/operationresults** |  |
> | 動作 | microsoft web/sites/位置/networktraces/operationresults/read | 取得 Web Apps 位置的網路追蹤操作結果。 |
> |  | **sites/位置/operationresults** |  |
> | 動作 | microsoft.web/sites/slots/operationresults/read | 取得 Web Apps 位置的作業結果。 |
> |  | **網站/位置/作業** |  |
> | 動作 | microsoft.web/sites/slots/operations/read | 取得 Web Apps 位置的作業。 |
> |  | **sites/位置/perfcounters** |  |
> | 動作 | microsoft.web/sites/slots/perfcounters/read | 取得 Web Apps 位置的效能計數器。 |
> |  | **sites/位置/phplogging** |  |
> | 動作 | microsoft.web/sites/slots/phplogging/read | 取得 Web Apps 位置的 Phplogging。 |
> |  | **sites/位置/premieraddons** |  |
> | 動作 | microsoft.web/sites/slots/premieraddons/delete | 刪除 Web Apps 位置的頂級附加元件。 |
> | 動作 | microsoft.web/sites/slots/premieraddons/read | 取得 Web Apps 位置的頂級附加元件。 |
> | 動作 | microsoft.web/sites/slots/premieraddons/write | 更新 Web Apps 位置的頂級附加元件。 |
> |  | **網站/位置/進程** |  |
> | 動作 | microsoft.web/sites/slots/processes/read | 取得 Web Apps 位置的程序。 |
> |  | **sites/位置/publiccertificates** |  |
> | 動作 | microsoft.web/sites/slots/publiccertificates/read | 取得 Web Apps 位置的公開憑證。 |
> | 動作 | microsoft.web/sites/slots/publiccertificates/write | 建立或更新 Web Apps 位置的公開憑證。 |
> | 動作 | microsoft.web/sites/slots/publiccertificates/delete | 刪除 Web Apps 位置的公開憑證。 |
> |  | **sites/位置/resourcehealthmetadata** |  |
> | 動作 | microsoft.web/sites/slots/resourcehealthmetadata/read | 取得 Web Apps 位置的資源健康狀態中繼資料。 |
> |  | **網站/位置/還原** |  |
> | 動作 | microsoft.web/sites/slots/restore/read | 取得 Web Apps 位置的還原。 |
> | 動作 | microsoft.web/sites/slots/restore/write | 還原 Web Apps 位置。 |
> |  | **sites/位置/siteextensions** |  |
> | 動作 | microsoft.web/sites/slots/siteextensions/delete | 刪除 Web Apps 位置的網站擴充功能。 |
> | 動作 | microsoft.web/sites/slots/siteextensions/read | 取得 Web Apps 位置的網站擴充功能。 |
> | 動作 | microsoft.web/sites/slots/siteextensions/write | 更新 Web Apps 位置的網站擴充功能。 |
> |  | **網站/位置/快照集** |  |
> | 動作 | microsoft.web/sites/slots/snapshots/read | 取得 Web Apps 位置的快照集。 |
> |  | **sites/位置/sourcecontrols** |  |
> | 動作 | Microsoft.Web/sites/slots/sourcecontrols/Read | 取得 Web 應用程式位置的原始檔控制組態設定 |
> | 動作 | Microsoft.Web/sites/slots/sourcecontrols/Write | 更新 Web 應用程式位置的原始檔控制組態設定 |
> | 動作 | Microsoft.Web/sites/slots/sourcecontrols/Delete | 刪除 Web 應用程式位置的原始檔控制組態設定 |
> |  | **sites/位置/triggeredwebjobs** |  |
> | 動作 | microsoft.web/sites/slots/triggeredwebjobs/delete | 刪除 Web Apps 位置所觸發的 WebJobs。 |
> | 動作 | microsoft.web/sites/slots/triggeredwebjobs/read | 取得 Web Apps 位置所觸發的 WebJobs。 |
> | 動作 | microsoft.web/sites/slots/triggeredwebjobs/run/action | 執行 Web Apps 位置所觸發的 WebJobs。 |
> |  | **網站/位置/使用量** |  |
> | 動作 | microsoft.web/sites/slots/usages/read | 取得 Web Apps 位置的使用量。 |
> |  | **sites/位置/virtualnetworkconnections** |  |
> | 動作 | microsoft.web/sites/slots/virtualnetworkconnections/delete | 刪除 Web Apps 位置的虛擬網路連線。 |
> | 動作 | microsoft.web/sites/slots/virtualnetworkconnections/read | 取得 Web Apps 位置的虛擬網路連線。 |
> | 動作 | microsoft.web/sites/slots/virtualnetworkconnections/write | 更新 Web Apps 位置的虛擬網路連線。 |
> |  | **sites/插槽/virtualnetworkconnections/閘道** |  |
> | 動作 | microsoft.web/sites/slots/virtualnetworkconnections/gateways/write | 更新 Web Apps 位置的虛擬網路連線閘道器。 |
> |  | **網站/位置/webjob** |  |
> | 動作 | microsoft.web/sites/slots/webjobs/read | 取得 Web Apps 位置的 WebJobs。 |
> |  | **網站/快照集** |  |
> | 動作 | microsoft.web/sites/snapshots/read | 取得 Web Apps 快照集。 |
> |  | **sites/sourcecontrols** |  |
> | 動作 | Microsoft.Web/sites/sourcecontrols/Read | 取得 Web 應用程式的原始檔控制組態設定 |
> | 動作 | Microsoft.Web/sites/sourcecontrols/Write | 更新 Web 應用程式的原始檔控制組態設定 |
> | 動作 | Microsoft.Web/sites/sourcecontrols/Delete | 刪除 Web 應用程式的原始檔控制組態設定 |
> |  | **sites/triggeredwebjobs** |  |
> | 動作 | microsoft.web/sites/triggeredwebjobs/delete | 刪除 Web Apps 所觸發的 WebJobs。 |
> | 動作 | microsoft.web/sites/triggeredwebjobs/read | 取得 Web Apps 所觸發的 WebJobs。 |
> | 動作 | microsoft.web/sites/triggeredwebjobs/run/action | 執行 Web Apps 所觸發的 WebJobs。 |
> |  | **sites/triggeredwebjobs/歷程記錄** |  |
> | 動作 | microsoft.web/sites/triggeredwebjobs/history/read | 取得 Web Apps 所觸發的 WebJobs 歷程記錄。 |
> |  | **網站/使用方式** |  |
> | 動作 | microsoft.web/sites/usages/read | 取得 Web Apps 使用量。 |
> |  | **sites/virtualnetworkconnections** |  |
> | 動作 | microsoft.web/sites/virtualnetworkconnections/delete | 刪除 Web Apps 的虛擬網路連線。 |
> | 動作 | microsoft.web/sites/virtualnetworkconnections/read | 取得 Web Apps 的虛擬網路連線。 |
> | 動作 | microsoft.web/sites/virtualnetworkconnections/write | 更新 Web Apps 的虛擬網路連線。 |
> |  | **網站/virtualnetworkconnections/閘道** |  |
> | 動作 | microsoft.web/sites/virtualnetworkconnections/gateways/read | 取得 Web Apps 的虛擬網路連線閘道器。 |
> | 動作 | microsoft.web/sites/virtualnetworkconnections/gateways/write | 更新 Web Apps 的虛擬網路連線閘道器。 |
> |  | **網站/webjob** |  |
> | 動作 | microsoft.web/sites/webjobs/read | 取得 Web Apps WebJobs。 |
> |  | **sku** |  |
> | 動作 | microsoft.web/skus/read | 取得 SKU。 |
> |  | **sourcecontrols** |  |
> | 動作 | microsoft.web/sourcecontrols/read | 取得原始檔控制。 |
> | 動作 | microsoft.web/sourcecontrols/write | 更新原始檔控制。 |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

Azure 服務： [Azure 監視器](../azure-monitor/index.yml)

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 作業 | 描述 |
> | --- | --- | --- |
> |  | **要素** |  |
> | 動作 | Microsoft.WorkloadMonitor/components/read | 取得資源的元件 |
> |  | **componentsSummary** |  |
> | 動作 | Microsoft.WorkloadMonitor/componentsSummary/read | 取得元件的摘要 |
> |  | **monitorInstances** |  |
> | 動作 | Microsoft.WorkloadMonitor/monitorInstances/read | 取得資源的監視器執行個體 |
> |  | **monitorInstancesSummary** |  |
> | 動作 | Microsoft.WorkloadMonitor/monitorInstancesSummary/read | 取得監視器執行個體的摘要 |
> |  | **監督** |  |
> | 動作 | Microsoft.WorkloadMonitor/monitors/read | 取得資源的監視器 |
> | 動作 | Microsoft.WorkloadMonitor/monitors/write | 設定資源的監視器 |
> |  | **notificationSettings** |  |
> | 動作 | Microsoft.WorkloadMonitor/notificationSettings/read | 取得資源的通知設定 |
> | 動作 | Microsoft.WorkloadMonitor/notificationSettings/write | 設定資源的通知設定 |
> |  | **業務** |  |
> | 動作 | Microsoft.WorkloadMonitor/operations/read | 取得支援的作業 |

## <a name="next-steps"></a>後續步驟

- [將資源提供者與服務比對](../azure-resource-manager/management/azure-services-resource-providers.md)
- [Azure 自訂角色](custom-roles.md)
- [Azure 內建角色](built-in-roles.md)
