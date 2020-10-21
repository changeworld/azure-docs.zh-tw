---
title: 使用 Azure 防火牆原則來定義規則階層
description: 瞭解如何使用 Azure 防火牆原則來定義規則階層，並強制執行合規性。
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: how-to
ms.date: 08/26/2020
ms.author: victorh
ms.openlocfilehash: 1ba683e3d616f52854f1055dab9b9fe2d389116a
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92331731"
---
# <a name="use-azure-firewall-policy-to-define-a-rule-hierarchy"></a>使用 Azure 防火牆原則來定義規則階層

安全性系統管理員需要管理防火牆，以確保內部部署和雲端部署之間的合規性。 關鍵元件是能夠讓應用程式小組彈性地執行 CI/CD 管線，以自動化的方式建立防火牆規則。

Azure 防火牆原則可讓您定義規則階層並強制執行合規性：

- 提供階層式結構，以在子應用程式小組原則之上覆迭中央基底原則。 基底原則的優先順序較高，而且會在子原則之前執行。
- 使用 Azure 自訂角色定義，以防止意外移除基底原則，並在訂用帳戶或資源群組內提供選擇性存取規則集合群組。 

## <a name="solution-overview"></a>解決方案概觀

此範例的高階步驟如下：

1. 在安全性小組資源群組中建立基本防火牆原則。 
3. 在基底原則中定義 IT 安全性特定的規則。 這會新增一組常用的規則，以允許/拒絕流量。
4. 建立繼承基底原則的應用程式小組原則。 
5. 定義原則中的應用程式小組特定規則。 您也可以從預先存在的防火牆遷移規則。
6. 建立 Azure Active Directory 自訂角色，以提供更細微的規則集合群組存取權，並在防火牆原則範圍新增角色。 在下列範例中，銷售團隊成員可以編輯「銷售小組」防火牆原則的規則集合群組。 這同樣適用于資料庫和工程團隊。
7. 將原則與對應的防火牆產生關聯。 Azure 防火牆只能有一個指派的原則。 這需要每個應用程式小組都有自己的防火牆。



:::image type="content" source="media/rule-hierarchy/contoso-teams.png" alt-text="小組和需求" border="false":::

### <a name="create-the-firewall-policies"></a>建立防火牆原則

- 基本防火牆原則。

為每個應用程式小組建立原則：

- 銷售防火牆原則。 銷售防火牆原則會繼承基本防火牆原則。
- 資料庫防火牆原則。 資料庫防火牆原則會繼承基本防火牆原則。
- 工程防火牆原則。 工程防火牆原則也會繼承基本防火牆原則。

:::image type="content" source="media/rule-hierarchy/policy-hierarchy.png" alt-text="小組和需求" border="false":::

### <a name="create-custom-roles-to-access-the-rule-collection-groups"></a>建立自訂角色以存取規則集合群組 

定義每個應用程式小組的自訂角色。 角色會定義作業與範圍。 應用程式小組可以編輯其個別應用程式的規則集合群組。

使用下列高階程式來定義自訂角色：

1. 取得訂用帳戶：

   `Select-AzSubscription -SubscriptionId xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxxx`
2. 執行下列命令：

   `Get-AzProviderOperation "Microsoft.Support/*" | FT Operation, Description -AutoSize`
3. 使用 Get-AzRoleDefinition 命令來輸出 JSON 格式的讀取者角色。 

   `Get-AzRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\CustomRoles\ReaderSupportRole.json`
4. 在編輯器中開啟檔案 ReaderSupportRole.js。

   下圖顯示了 JSON 輸出。 如需不同屬性的詳細資訊，請參閱 [Azure 自訂角色](../role-based-access-control/custom-roles.md)。

```json
   { 
     "Name": "Reader", 
     "Id": "acdd72a7-3385-48ef-bd42-f606fba81ae7", 
     "IsCustom": false, 
     "Description": "Lets you view everything, but not make any changes.", 
     "Actions": [ 
      "*/read" 
     ], 
     "NotActions": [], 
     "DataActions": [], 
     "NotDataActions": [], 
     "AssignableScopes": [ 
       "/" 
     ] 
   } 
```
5. 編輯 JSON 檔案以新增 

   `*/read", "Microsoft.Network/*/read", "Microsoft.Network/firewallPolicies/ruleCollectionGroups/write` 

    **動作**   屬性的操作。 請務必在讀取作業之後包含逗號。 此動作可讓使用者建立和更新規則集合群組。
6. 在 **>assignablescopes**中，新增具有下列格式的訂用帳戶識別碼： 

   `/subscriptions/xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxxx`

   您必須新增明確的訂用帳戶識別碼，否則無法將角色匯入您的訂用帳戶。
7. 刪除 [ **識別碼**]   屬性行，並將 [ **IsCustom**] 屬性變更為 [   true]。
8. 將 [ **名稱**]   和 [ **描述**]   屬性變更為*AZFM 規則集合群組作者*，而*此角色中的使用者可以編輯防火牆原則規則集合群組*

您的 JSON 檔案看起來應該類似下列範例：

```
{ 

    "Name":  "AZFM Rule Collection Group Author", 
    "IsCustom":  true, 
    "Description":  "Users in this role can edit Firewall Policy rule collection groups", 
    "Actions":  [ 
                    "*/read", 
                    "Microsoft.Network/*/read", 
                     "Microsoft.Network/firewallPolicies/ruleCollectionGroups/write" 
                ], 
    "NotActions":  [ 
                   ], 
    "DataActions":  [ 
                    ], 
    "NotDataActions":  [ 
                       ], 
    "AssignableScopes":  [ 
                             "/subscriptions/xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxxx"] 
} 
```
9. 若要建立新的自訂角色，請使用 New-AzRoleDefinition 命令，並指定 JSON 角色定義檔。 

   `New-AzRoleDefinition -InputFile "C:\CustomRoles\RuleCollectionGroupRole.json`

### <a name="list-custom-roles"></a>列出自訂角色

若要列出所有自訂角色，您可以使用 Get-AzRoleDefinition 命令：

   `Get-AzRoleDefinition | ? {$_.IsCustom -eq $true} | FT Name, IsCustom`

您也可以在 Azure 入口網站中看到自訂角色。 移至您的訂用帳戶，選取 [ **存取控制] (IAM) **、[ **角色**]。

:::image type="content" source="media/rule-hierarchy/sales-app-policy.png" alt-text="小組和需求":::

:::image type="content" source="media/rule-hierarchy/sales-app-policy-read.png" alt-text="小組和需求":::

如需詳細資訊，請參閱 [教學課程：使用 Azure PowerShell 建立 Azure 自訂角色](../role-based-access-control/tutorial-custom-role-powershell.md)。

### <a name="add-users-to-the-custom-role"></a>將使用者新增至自訂角色

在入口網站上，您可以將使用者新增至 AZFM 規則集合群組作者角色，並提供防火牆原則的存取權。

1. 從入口網站中，選取應用程式小組防火牆原則 (例如，SalesAppPolicy) 。
2. 選取 [ **存取控制**]。
3. 選取 [新增角色指派]。
4. 新增使用者/使用者群組 (例如，銷售小組) 角色。

針對其他防火牆原則重複此程式。

### <a name="summary"></a>摘要

具有自訂角色的防火牆原則現在可讓您選擇性地存取防火牆原則規則集合群組。

使用者沒有許可權可執行下列動作：
- 刪除 Azure 防火牆或防火牆原則。
- 更新防火牆原則階層或 DNS 設定或威脅情報。
- 更新防火牆原則，其中不是 AZFM 規則集合群組作者群組的成員。

安全性系統管理員可以使用基本原則來強制執行護欄，並封鎖特定類型的流量 (例如，其企業要求的 ICMP) 。 

## <a name="next-steps"></a>後續步驟

深入瞭解 [Azure 防火牆原則](policy-overview.md)。

