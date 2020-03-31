---
title: 管理 Azure 區塊鏈服務聯盟成員 - PowerShell
description: 瞭解如何使用 Azure PowerShell 管理 Azure 區塊鏈服務聯盟成員。
ms.date: 10/14/2019
ms.topic: article
ms.reviewer: zeyadr
ms.openlocfilehash: e819dd39481b58d446384a5e2253c548ce0c267c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77505974"
---
# <a name="manage-consortium-members-in-azure-blockchain-service-using-powershell"></a>使用 PowerShell 管理 Azure 區塊鏈服務中的聯盟成員

您可以使用 PowerShell 管理 Azure 區塊鏈服務的區塊鏈聯盟成員。 具有管理員許可權的成員可以為區塊鏈聯盟中的所有參與者邀請、添加、刪除和更改角色。 具有使用者許可權的成員可以查看區塊鏈聯盟的所有參與者並更改其成員顯示名稱。

## <a name="prerequisites"></a>Prerequisites

* 使用[Azure 門戶](create-member.md)創建區塊鏈成員。
* 有關聯營、成員和節點的詳細資訊，請參閱[Azure 區塊鏈服務聯合體](consortium.md)。

## <a name="open-azure-cloud-shell"></a>開啟 Azure Cloud Shell

Azure Cloud Shell 是免費的互動式 Shell，可讓您用來執行本文中的步驟。 它具有預先安裝和設定的共用 Azure 工具，可與您的帳戶搭配使用。

您還可以[通過shell.azure.com/powershell在](https://shell.azure.com/powershell)單獨的瀏覽器選項卡中打開雲外殼。 選擇 **"複製"** 以複製代碼塊，將其粘貼到雲殼體中，然後選擇"**輸入**"以運行它。

## <a name="install-the-powershell-module"></a>安裝 PowerShell 模組

從 PowerShell 庫中安裝Microsoft.AzureBlockchainService.ConsortiumManagement.PS包。

```powershell-interactive
Install-Module -Name Microsoft.AzureBlockchainService.ConsortiumManagement.PS -Scope CurrentUser
Import-Module Microsoft.AzureBlockchainService.ConsortiumManagement.PS
```

## <a name="set-the-information-preference"></a>設置資訊首選項

通過設置資訊首選項變數，在執行 Cmdlet 時，可以獲取更多資訊。 預設情況下 *，$InformationPreference*設置為 *"靜默繼續*"。

有關 Cmdlet 的更多詳細資訊，在 PowerShell 中設置首選項，如下所示：

```powershell-interactive
$InformationPreference = 'Continue'
```

## <a name="establish-a-web3-connection"></a>建立 Web3 連接

要管理聯盟成員，請建立與區塊鏈服務成員終結點的 Web3 連接。 您可以使用此腳本設置全域變數來調用聯合體管理 Cmdlet。

```powershell-interactive
$Connection = New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
$MemberAccount = Import-Web3Account -ManagedAccountAddress '<Member account address>' -ManagedAccountPassword '<Member account password>'
$ContractConnection = Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>' -Web3Client $Connection
```

將*\<成員帳戶密碼\>* 替換為創建成員時使用的會員帳戶密碼。

在 Azure 門戶中查找其他值：

1. 登錄到 Azure[門戶](https://portal.azure.com)。
1. 轉到預設區塊鏈服務成員**概述**頁面。

    ![成員概述](./media/manage-consortium-powershell/member-overview.png)

    將*\<成員帳戶\>* 和*\<RootContract\>位址*替換為門戶中的值。

1. 對於終結點位址，選擇**事務節點**，然後選擇**預設事務節點**。 預設節點的名稱與區塊鏈成員相同。
1. 選擇**連接字串**。

    ![連接字串](./media/manage-consortium-powershell/connection-strings.png)

    將*\<終結點位址\>* 替換為**HTTPS（訪問金鑰 1）** 或**HTTPS（訪問金鑰 2）中**的值。

## <a name="manage-the-network-and-smart-contracts"></a>管理網路和智慧合約

使用網路和智慧合約 Cmdlet 建立與負責聯合管理的區塊鏈端點智慧合約的連接。

### <a name="import-consortiummanagementcontracts"></a>進口-聯合管理合同

使用此 Cmdlet 連接到聯合體管理層的智慧合約。 這些合同用於管理和強制執行聯合體內的成員。

`Import-ConsortiumManagementContracts -RootContractAddress <String> -Web3Client <IClient>`

| 參數 | 描述 | 必要 |
|-----------|-------------|:--------:|
| 根合約地址 | 聯營管理智慧合同的根合約地址 | 是 |
| Web3用戶端 | 從新 Web3連接獲取的 Web3用戶端物件 | 是 |

#### <a name="example"></a>範例

```powershell-interactive
Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>'  -Web3Client $Connection
```

### <a name="import-web3account"></a>導入-Web3帳戶

使用此 Cmdlet 創建物件以保存遠端節點的管理帳戶的資訊。

`Import-Web3Account -ManagedAccountAddress <String> -ManagedAccountPassword <String>`

| 參數 | 描述 | 必要 |
|-----------|-------------|:--------:|
| 託管帳戶位址 | 區塊鏈成員帳戶位址 | 是 |
| 託管帳戶密碼 | 帳戶位址密碼 | 是 |

#### <a name="example"></a>範例

```powershell-interactive
Import-Web3Account -ManagedAccountAddress '<Member account address>'  -ManagedAccountPassword '<Member account password>'
```

### <a name="new-web3connection"></a>新-Web3連接

使用此 Cmdlet 建立與事務節點的 RPC 終結點的連接。

`New-Web3Connection [-RemoteRPCEndpoint <String>]`

| 參數 | 描述 | 必要 |
|-----------|-------------|:--------:|
| 遠端RPC端點 | 區塊鏈成員端點位址 | 是 |

#### <a name="example"></a>範例

```powershell-interactive
New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
```

## <a name="manage-the-consortium-members"></a>管理聯營成員

使用聯營體成員管理 Cmdlet 管理聯合體內的成員。 可用的操作取決於您的聯合體角色。

### <a name="get-blockchainmember"></a>獲取區塊鏈成員

使用此 Cmdlet 獲取聯合體的成員詳細資訊或清單成員。

`Get-BlockchainMember [[-Name] <String>] -Members <IContract> -Web3Client <IClient>`

| 參數 | 描述 | 必要 |
|-----------|-------------|:--------:|
| 名稱 | 要檢索詳細資訊的區塊鏈服務成員的姓名。 輸入名稱時，它將返回成員的詳細資訊。 省略名稱時，它將返回所有聯合體成員的清單。 | 否 |
| 成員 | 從導入-聯合管理合同獲取的成員物件 | 是 |
| Web3用戶端 | 從新 Web3連接獲取的 Web3用戶端物件 | 是 |

#### <a name="example"></a>範例

[建立 Web3 連接](#establish-a-web3-connection)以設置$ContractConnection變數。

```powershell-interactive
$ContractConnection | Get-BlockchainMember -Name <Member Name>
```

#### <a name="example-output"></a>範例輸出

```
Name           : myblockchainmember
CorrelationId  : 0
DisplayName    : myCompany
SubscriptionId : <Azure subscription ID>
AccountAddress : 0x85b911c9e103d6405573151258d668479e9ebeef
Role           : ADMIN
```

### <a name="remove-blockchainmember"></a>刪除-區塊鏈成員

使用此 Cmdlet 刪除區塊鏈成員。

`Remove-BlockchainMember -Name <String> -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>`

| 參數 | 描述 | 必要 |
|-----------|-------------|:--------:|
| 名稱 | 要刪除的會員名稱 | 是 |
| 成員 | 從導入-聯合管理合同獲取的成員物件 | 是 |
| Web3帳戶 | 從導入 Web3 帳戶獲取的 Web3帳戶物件 | 是 |
| Web3用戶端 | 從新 Web3連接獲取的 Web3用戶端物件 | 是 |

#### <a name="example"></a>範例

[建立 Web3 連接](#establish-a-web3-connection)以設置$ContractConnection和$MemberAccount變數。

```powershell-interactive
$ContractConnection | Remove-BlockchainMember -Name <Member Name> -Web3Account $MemberAccount
```

### <a name="set-blockchainmember"></a>設置-區塊鏈成員

使用此 Cmdlet 設置區塊鏈成員屬性，包括顯示名稱和聯合體角色。

聯盟管理員可以為所有成員設置 **"顯示名稱**"和 **"角色**"。 具有使用者角色的聯合體成員只能更改其自己的成員的顯示名稱。

```
Set-BlockchainMember -Name <String> [-DisplayName <String>] [-AccountAddress <String>] [-Role <String>]
 -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>
```

| 參數 | 描述 | 必要 |
|-----------|-------------|:--------:|
| 名稱 | 區塊鏈成員名稱 | 是 |
| DisplayName | 新的顯示名稱 | 否 |
| 帳戶位址 | 帳戶位址 | 否 |
| 成員 | 從導入-聯合管理合同獲取的成員物件 | 是 |
| Web3帳戶 | 從導入 Web3 帳戶獲取的 Web3帳戶物件 | 是 |
| Web3用戶端 |  從新 Web3連接獲取的 Web3用戶端物件| 是 |

#### <a name="example"></a>範例

[建立 Web3 連接](#establish-a-web3-connection)以設置$ContractConnection和$MemberAccount變數。

```powershell-interactive
$ContractConnection | Set-BlockchainMember -Name <Member Name> -DisplayName <Display name> -Web3Account $MemberAccount
```

## <a name="manage-the-consortium-members-invitations"></a>管理聯營成員的邀請

使用聯營體成員邀請管理 Cmdlet 管理聯營成員的邀請。 可用的操作取決於您的聯合體角色。

### <a name="new-blockchainmemberinvitation"></a>新區塊鏈會員邀請

使用此 Cmdlet 邀請新成員加入聯合體。

```
New-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>
```

| 參數 | 描述 | 必要 |
|-----------|-------------|:--------:|
| SubscriptionId | 要邀請的成員的 Azure 訂閱 ID | 是 |
| 角色 | 聯合體角色。 值可以是 ADMIN 或 USER。 ADMIN 是聯合管理人角色。 USER 是聯合體成員角色。 | 是 |
| 成員 | 從導入-聯合管理合同獲取的成員物件 | 是 |
| Web3帳戶 | 從導入 Web3 帳戶獲取的 Web3帳戶物件 | 是 |
| Web3用戶端 | 從新 Web3連接獲取的 Web3用戶端物件 | 是 |

#### <a name="example"></a>範例

[建立 Web3 連接](#establish-a-web3-connection)以設置$ContractConnection和$MemberAccount變數。

```powershell-interactive
$ContractConnection | New-BlockchainMemberInvitation -SubscriptionId <Azure Subscription ID> -Role USER -Web3Account $MemberAccount
```

### <a name="get-blockchainmemberinvitation"></a>獲取區塊鏈成員邀請

使用此 Cmdlet 檢索或列出聯合體成員的邀請狀態。

`Get-BlockchainMemberInvitation [[-SubscriptionId] <String>] -Members <IContract> -Web3Client <IClient>`

| 參數 | 描述 | 必要 |
|-----------|-------------|:--------:|
| SubscriptionId | 要邀請的成員的 Azure 訂閱 ID。 如果提供了訂閱 ID，它將返回訂閱 ID 的邀請詳細資訊。 如果省略訂閱 ID，它將返回所有成員邀請的清單。 | 否 |
| 成員 | 從導入-聯合管理合同獲取的成員物件 | 是 |
| Web3用戶端 | 從新 Web3連接獲取的 Web3用戶端物件 | 是 |

#### <a name="example"></a>範例

[建立 Web3 連接](#establish-a-web3-connection)以設置$ContractConnection變數。

```powershell-interactive
$ContractConnection | Get-BlockchainMemberInvitation –SubscriptionId <Azure subscription ID>
```

#### <a name="example-output"></a>範例輸出

```
SubscriptionId                       Role CorrelationId
--------------                       ---- -------------
<Azure subscription ID>              USER             2
```

### <a name="remove-blockchainmemberinvitation"></a>刪除區塊鏈成員邀請

使用此 Cmdlet 撤銷聯合體成員的邀請。

```
Remove-BlockchainMemberInvitation -SubscriptionId <String> -Members <IContract> -Web3Account <IAccount>
 -Web3Client <IClient>
```

| 參數 | 描述 | 必要 |
|-----------|-------------|:--------:|
| SubscriptionId | 要撤銷的成員的 Azure 訂閱 ID | 是 |
| 成員 | 從導入-聯合管理合同獲取的成員物件 | 是 |
| Web3帳戶 | 從導入 Web3 帳戶獲取的 Web3帳戶物件 | 是 |
| Web3用戶端 | 從新 Web3連接獲取的 Web3用戶端物件 | 是 |

#### <a name="example"></a>範例

[建立 Web3 連接](#establish-a-web3-connection)以設置$ContractConnection和$MemberAccount變數。

```powershell-interactive
$ContractConnection | Remove-BlockchainMemberInvitation -SubscriptionId <Subscription ID> -Web3Account $MemberAccount
```

### <a name="set-blockchainmemberinvitation"></a>設置-區塊鏈成員邀請

使用此 Cmdlet 可設置現有邀請**的角色**。 只有聯合管理人才能更改邀請。

```
Set-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>
```

| 參數 | 描述 | 必要 |
|-----------|-------------|:--------:|
| SubscriptionId | 要邀請的成員的 Azure 訂閱 ID | 是 |
| 角色 | 邀請的新聯合體角色。 值可以是**USER**或**ADMIN**。 | 是 |
| 成員 |  從導入-聯合管理合同獲取的成員物件 | 是 |
| Web3帳戶 | 從導入 Web3 帳戶獲取的 Web3帳戶物件 | 是 |
| Web3用戶端 | 從新 Web3連接獲取的 Web3用戶端物件 | 是 |

#### <a name="example"></a>範例

[建立 Web3 連接](#establish-a-web3-connection)以設置$ContractConnection和$MemberAccount變數。

```powershell-interactive
$ContractConnection | Set-BlockchainMemberInvitation -SubscriptionId <Azure subscription ID> -Role USER -Web3Account $MemberAccount
```

## <a name="next-steps"></a>後續步驟

有關聯營、成員和節點的詳細資訊，請參閱[Azure 區塊鏈服務聯盟](consortium.md)
