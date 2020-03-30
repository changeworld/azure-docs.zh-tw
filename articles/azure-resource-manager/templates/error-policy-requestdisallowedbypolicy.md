---
title: RequestDisallowedByPolicy 錯誤
description: 描述使用 Azure 資源管理器部署資源時請求禁止 By 策略錯誤的原因。
author: genlin
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 41581ba48da2f2e717c5abf2a749f8fd2b86ac06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75477664"
---
# <a name="requestdisallowedbypolicy-error-with-azure-resource-policy"></a>Azure 資源原則產生的 RequestDisallowedByPolicy 錯誤

本文說明 RequestDisallowedByPolicy 錯誤的原因，其中也會提供此錯誤的解決方案。

## <a name="symptom"></a>徵狀

部署期間，您可能會收到 **RequestDisallowedByPolicy** 錯誤，阻止您建立資源。 下列範例顯示錯誤：

```json
{
  "statusCode": "Forbidden",
  "serviceRequestId": null,
  "statusMessage": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}",
  "responseBody": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}"
}
```

## <a name="troubleshooting"></a>疑難排解

若要擷取有關封鎖了您部署之原則的詳細資訊，請使用下列其中一種方法：

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

在 PowerShell 中，提供該原則識別碼作為 `Id` 參數，以擷取有關封鎖了部署之原則的詳細資料。

```powershell
(Get-AzPolicyDefinition -Id "/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition").Properties.policyRule | ConvertTo-Json
```

### <a name="azure-cli"></a>Azure CLI

在 Azure CLI 中，提供原則定義的名稱︰

```azurecli
az policy definition show --name regionPolicyAssignment
```

## <a name="solution"></a>解決方法

對於安全性或合規性，您的訂用帳戶管理員可能會指派原則來限制資源的部署方式。 例如，您的訂用帳戶可能有一個原則，會阻止建立公用 IP 位址、網路安全性群組、使用者定義的路由或路由表。 **徵狀**一節中的錯誤訊息會顯示原則的名稱。
若要解決這個問題，請檢閱資源原則，並決定如何部署符合那些原則的資源。

如需詳細資訊，請參閱下列文章：

- [什麼是 Azure 原則？](../../governance/policy/overview.md)
- [創建和管理原則以強制實施合規性](../../governance/policy/tutorials/create-and-manage.md)
