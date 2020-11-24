---
title: Azure 證明審核記錄
description: 描述針對 Azure 證明收集的完整審核記錄
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: reference
ms.date: 11/23/2020
ms.author: mbaldwin
ms.openlocfilehash: 1fa4a458a4e3e1df1d84c343a32e3a41a4a25e75
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "95758975"
---
# <a name="audit-logs-for-azure-attestation"></a>Azure 證明的審核記錄

Audit 記錄是一段時間內發生之離散事件的安全、不變、有時間戳記的記錄。 這些記錄會捕捉可能會影響證明實例功能的重要事件。

Azure 證明會管理證明實例和與其相關聯的原則。 與實例管理和原則變更相關聯的動作會經過審核和記錄。

本文包含有關所記錄的事件、所收集的資訊，以及這些記錄檔位置的資訊。

## <a name="about-audit-logs"></a>關於 Audit 記錄

Azure 證明使用程式碼來產生會影響證明執行方式之事件的審核記錄。 這通常會在您的證明實例以及某些系統管理動作的原則變更時，向下細分。

### <a name="auditable-events"></a>可審核的事件
以下是我們收集的一些審核記錄：

|     事件/API                              |     事件描述                                                                         |
|--------------------------------------------|-----------------------------------------------------------------------------------------------|
|     建立執行個體                        |     建立證明服務的新實例。 |
|     終結實例                       |     終結證明服務的實例。 |
|     新增原則憑證                 |     將憑證新增至目前的原則管理憑證集。 |
|     移除原則憑證              |     從目前的原則管理憑證集合中移除憑證。 |
|     設定目前的原則                     |     為指定的 t 型別設定證明原則。 |
|     重設證明原則               |     針對指定的 t 型別重設證明原則。 |
|     準備更新原則               |     準備為指定的 t 型別更新證明原則。 |
|     在災難後解除凍結租使用者       |     重新密封此證明服務實例上的所有證明租使用者。 這只能由證明服務管理員執行。 |

### <a name="collected--information"></a>收集的資訊
針對上述每個事件，Azure 證明會收集下列資訊：

- 作業名稱
- 作業成功
- 作業呼叫端，可以是下列其中一項：
    - Azure AD UPN
    - 物件識別碼
    - 憑證
    - Azure AD 租用戶識別碼
- 作業目標，可能是下列其中一項：
    - 環境
    - 服務區域
    - 服務角色
    - 服務角色實例
    - 資源識別碼
    - 資源區域

### <a name="sample-audit-log"></a>範例審核記錄

Audit 記錄檔是以 JSON 格式提供。 以下是 audit 記錄檔可能的樣子範例。

```json
{"operationName":"SetCurrentPolicy","resultType":"Success","resultDescription":null,"auditEventCategory":["ApplicationManagement"],"nCloud":null,"requestId":null,"callerIpAddress":null,"callerDisplayName":null,"callerIdentities":[{"callerIdentityType":"ObjectID","callerIdentity":"<some object ID>"},{"callerIdentityType":"TenantId","callerIdentity":"<some tenant ID>"}],"targetResources":[{"targetResourceType":"Environment","targetResourceName":"PublicCloud"},{"targetResourceType":"ServiceRegion","targetResourceName":"EastUS2"},{"targetResourceType":"ServiceRole","targetResourceName":"AttestationRpType"},{"targetResourceType":"ServiceRoleInstance","targetResourceName":"<some service role instance>"},{"targetResourceType":"ResourceId","targetResourceName":"/subscriptions/<some subscription ID>/resourceGroups/<some resource group name>/providers/Microsoft.Attestation/attestationProviders/<some instance name>"},{"targetResourceType":"ResourceRegion","targetResourceName":"EastUS2"}],"ifxAuditFormat":"Json","env_ver":"2.1","env_name":"#Ifx.AuditSchema","env_time":"2020-11-23T18:23:29.9427158Z","env_epoch":"MKZ6G","env_seqNum":1277,"env_popSample":0.0,"env_iKey":null,"env_flags":257,"env_cv":"##00000000-0000-0000-0000-000000000000_00000000-0000-0000-0000-000000000000_00000000-0000-0000-0000-000000000000","env_os":null,"env_osVer":null,"env_appId":null,"env_appVer":null,"env_cloud_ver":"1.0","env_cloud_name":null,"env_cloud_role":null,"env_cloud_roleVer":null,"env_cloud_roleInstance":null,"env_cloud_environment":null,"env_cloud_location":null,"env_cloud_deploymentUnit":null}
```

## <a name="access-audit-logs"></a>存取 Audit 記錄

這些記錄會儲存在 Azure 中，無法直接存取。 如果您需要存取這些記錄檔，請提出支援票證。 如需詳細資訊，請參閱 [連絡 Microsoft 支援服務](https://azure.microsoft.com/support/options/)。 

一旦提出支援票證，Microsoft 將會下載並提供您這些記錄的存取權。
