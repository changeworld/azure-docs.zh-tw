---
title: 使用 REST API 創建和編輯 Azure 策略安全性原則
description: 通過 REST API 瞭解 Azure 策略策略管理。
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: c218b5dc8ca3bfa0358a9b6a0d4867696762a8d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77430936"
---
# <a name="configure-a-security-policy-in-azure-policy-using-the-rest-api"></a>使用 REST API 在 Azure 策略中配置安全性原則

Azure 資訊安全中心與 Azure 原則的原生整合可讓您充分利用 Azure 原則的 REST API 來建立原則指派。 下列指示會引導您完成建立原則指派，以及自訂現有的指派。 

Azure 原則中的重要概念： 

- **策略定義**是一條規則 

- **計畫是**策略定義（規則）的集合 

- **分配**是計畫或策略對特定範圍（管理組、訂閱等）的應用 

資訊安全中心有內建的方案，其中包含所有其安全性原則。 要評估安全中心對 Azure 資源的策略，應在管理組或要評估的訂閱上創建分配。

內建的方案都有預設啟用的所有資訊安全中心原則。 您可以選擇從內置計畫中禁用某些策略。 例如，要應用除**Web 應用程式防火牆**之外的所有安全中心策略，請將策略的效果參數的值更改為 **"已禁用**"。 

## <a name="api-examples"></a>API 範例

在下列範例中，更換這些變數：

- **{scope}** 輸入要向其應用策略的管理組或訂閱的名稱。
- **{policyAssignmentName}** 輸入[相關原則指派的名稱](#policy-names)。
- **{name}** 輸入您的名稱，或已核准原則變更的系統管理員名稱。

此範例將示範如何對於訂用帳戶或管理群組指派內建的資訊安全中心方案
 
 ```
    PUT  
    https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 

    Request Body (JSON) 

    { 

      "properties":{ 

    "displayName":"Enable Monitoring in Azure Security Center", 

    "metadata":{ 

    "assignedBy":"{Name}" 

    }, 

    "policyDefinitionId":"/providers/Microsoft.Authorization/policySetDefinitions/1f3afdf9-d0c9-4c3d-847f-89da613e70a8", 

    "parameters":{}, 

    } 

    } 
 ```

此範例將示範如何對於已停用下列原則的訂用帳戶，指派內建的資訊安全中心方案： 

- 系統更新 (「systemUpdatesMonitoringEffect」) 

- 安全性設定 (「systemConfigurationsMonitoringEffect」) 

- 端點保護 (「endpointProtectionMonitoringEffect」) 

 ```
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 
    
    Request Body (JSON) 
    
    { 
    
      "properties":{ 
    
    "displayName":"Enable Monitoring in Azure Security Center", 
    
    "metadata":{ 
    
    "assignedBy":"{Name}" 
    
    }, 
    
    "policyDefinitionId":"/providers/Microsoft.Authorization/policySetDefinitions/1f3afdf9-d0c9-4c3d-847f-89da613e70a8", 
    
    "parameters":{ 
    
    "systemUpdatesMonitoringEffect":{"value":"Disabled"}, 
    
    "systemConfigurationsMonitoringEffect":{"value":"Disabled"}, 
    
    "endpointProtectionMonitoringEffect":{"value":"Disabled"}, 
    
    }, 
    
     } 
    
    } 
 ```
此範例會示範如何移除指派：
 ```
    DELETE   
    https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 
 ```

## <a name="policy-names-reference"></a>原則名稱參考 <a name="policy-names"></a>

|資訊安全中心中的原則名稱|Azure 原則中顯示的原則名稱 |原則效果參數名稱|
|----|----|----|
|SQL 加密 |在 Azure 資訊安全中心中監視未加密的 SQL 資料庫 |sqlEncryptionMonitoringEffect| 
|SQL 稽核 |在 Azure 資訊安全中心中監視未稽核的 SQL 資料庫 |sqlAuditingMonitoringEffect|
|系統更新 |在 Azure 資訊安全中心中監視缺少的系統更新 |systemUpdatesMonitoringEffect|
|儲存體加密 |稽核 Blob 未加密的儲存體帳戶 |storageEncryptionMonitoringEffect|
|JIT 網路存取 |監視 Azure 安全中心中可能的即時網路 （JIT） 訪問 |jitNetworkAccessMonitoringEffect |
|自適性應用程式控制 |在 Azure 資訊安全中心中監視可能的應用程式允許清單 |adaptiveApplicationControlsMonitoringEffect|
|網路安全性群組 |在 Azure 資訊安全中心中監視寬鬆的網路存取 |networkSecurityGroupsMonitoringEffect| 
|安全性設定 |在 Azure 資訊安全中心中監視 OS 弱點 |systemConfigurationsMonitoringEffect| 
|端點保護 |在 Azure 資訊安全中心中監視缺少的 Endpoint Protection |endpointProtectionMonitoringEffect |
|磁碟加密 |在 Azure 資訊安全中心中監視未加密的 VM 磁碟 |diskEncryptionMonitoringEffect|
|弱點評估 |在 Azure 資訊安全中心中監視 VM 的弱點 |vulnerabilityAssessmentMonitoringEffect|
|Web 應用程式防火牆 |在 Azure 資訊安全中心中監視未受保護的 Web 應用程式 |webApplicationFirewallMonitoringEffect |
|新一代防火牆 |在 Azure 資訊安全中心中監視未保護的網路端點| |


## <a name="next-steps"></a>後續步驟

有關其他相關材料，請參閱以下文章： 

- [自訂安全性原則](custom-security-policies.md)
- [安全性原則概述](tutorial-security-policy.md)