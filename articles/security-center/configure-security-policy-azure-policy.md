---
title: 使用 REST API 建立和編輯 Azure 原則安全性原則
description: 瞭解如何透過 REST API Azure 原則原則管理。
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: c218b5dc8ca3bfa0358a9b6a0d4867696762a8d4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "77430936"
---
# <a name="configure-a-security-policy-in-azure-policy-using-the-rest-api"></a>使用 REST API 在 Azure 原則中設定安全性原則

Azure 資訊安全中心與 Azure 原則的原生整合可讓您充分利用 Azure 原則的 REST API 來建立原則指派。 下列指示會引導您完成建立原則指派，以及自訂現有的指派。 

Azure 原則中的重要概念： 

- **原則定義**是一種規則 

- **方案**是原則定義的集合（規則） 

- **指派**是將方案或原則應用至特定範圍（管理群組、訂用帳戶等） 

資訊安全中心有內建的方案，其中包含所有其安全性原則。 若要評估 Azure 資源上資訊安全中心的原則，您應該在管理群組或您想要評估的訂用帳戶上建立指派。

內建的方案都有預設啟用的所有資訊安全中心原則。 您可以選擇停用內建方案中的特定原則。 例如，若要套用「 **web 應用程式防火牆**」以外的所有資訊安全中心原則，請將原則的效果參數值變更為「**已停用**」。 

## <a name="api-examples"></a>API 範例

在下列範例中，更換這些變數：

- **{scope}** 輸入您要套用原則的管理群組或訂用帳戶的名稱。
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
|JIT 網路存取 |在 Azure 資訊安全中心中監視可能的網路即時（JIT）存取 |jitNetworkAccessMonitoringEffect |
|自適性應用程式控制 |在 Azure 資訊安全中心中監視可能的應用程式允許清單 |adaptiveApplicationControlsMonitoringEffect|
|網路安全性群組 |在 Azure 資訊安全中心中監視寬鬆的網路存取 |networkSecurityGroupsMonitoringEffect| 
|安全性設定 |在 Azure 資訊安全中心中監視 OS 弱點 |systemConfigurationsMonitoringEffect| 
|Endpoint Protection |在 Azure 資訊安全中心中監視缺少的 Endpoint Protection |endpointProtectionMonitoringEffect |
|磁碟加密 |在 Azure 資訊安全中心中監視未加密的 VM 磁碟 |diskEncryptionMonitoringEffect|
|弱點評估 |在 Azure 資訊安全中心中監視 VM 的弱點 |vulnerabilityAssessmentMonitoringEffect|
|Web 應用程式防火牆 |在 Azure 資訊安全中心中監視未受保護的 Web 應用程式 |webApplicationFirewallMonitoringEffect |
|新一代防火牆 |在 Azure 資訊安全中心中監視未保護的網路端點| |


## <a name="next-steps"></a>後續步驟

如需其他相關資料，請參閱下列文章： 

- [自訂安全性原則](custom-security-policies.md)
- [安全性原則總覽](tutorial-security-policy.md)