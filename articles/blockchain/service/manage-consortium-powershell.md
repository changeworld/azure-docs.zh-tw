---
title: 管理 Azure 區塊鏈 Service 聯盟成員-PowerShell
description: 瞭解如何使用 Azure PowerShell 來管理 Azure 區塊鏈 Service 聯盟成員。
ms.date: 10/14/2019
ms.topic: how-to
ms.reviewer: zeyadr
ms.openlocfilehash: d40e55f177bda9edb40383b6e2c61c32633cd005
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85211335"
---
# <a name="manage-consortium-members-in-azure-blockchain-service-using-powershell"></a>使用 PowerShell 管理 Azure 區塊鏈 Service 中的聯盟成員

您可以使用 PowerShell 來管理 Azure 區塊鏈服務的區塊鏈聯盟成員。 具有系統管理員許可權的成員可以邀請、新增、移除及變更區塊鏈聯盟中所有參與者的角色。 具有使用者權限的成員可以查看區塊鏈聯盟中的所有參與者，並變更其成員的顯示名稱。

## <a name="prerequisites"></a>必要條件

* 使用[Azure 入口網站](create-member.md)建立區塊鏈成員。
* 如需有關聯盟、成員和節點的詳細資訊，請參閱[Azure 區塊鏈 Service 聯盟](consortium.md)。

## <a name="open-azure-cloud-shell"></a>開啟 Azure Cloud Shell

Azure Cloud Shell 是免費的互動式 Shell，可讓您用來執行本文中的步驟。 它具有預先安裝和設定的共用 Azure 工具，可與您的帳戶搭配使用。

您也可以前往 [ [shell.azure.com/powershell](https://shell.azure.com/powershell)]，在另一個瀏覽器索引標籤中開啟 Cloud Shell。 選取 [**複製**] 以複製程式碼區塊，並將其貼到 Cloud Shell 中，然後選取**Enter**加以執行。

## <a name="install-the-powershell-module"></a>安裝 PowerShell 模組

從 PowerShell 資源庫安裝 Microsoft.AzureBlockchainService.ConsortiumManagement.PS 套件。

```powershell-interactive
Install-Module -Name Microsoft.AzureBlockchainService.ConsortiumManagement.PS -Scope CurrentUser
Import-Module Microsoft.AzureBlockchainService.ConsortiumManagement.PS
```

## <a name="set-the-information-preference"></a>設定資訊喜好設定

您可以藉由設定資訊喜好設定變數來取得執行 Cmdlet 時的詳細資訊。 根據預設， *$InformationPreference*會設定為*SilentlyContinue*。

如需 Cmdlet 的詳細資訊，請在 PowerShell 中設定喜好設定，如下所示：

```powershell-interactive
$InformationPreference = 'Continue'
```

## <a name="establish-a-web3-connection"></a>建立 Web3 連接

若要管理聯盟成員，請建立區塊鏈服務成員端點的 Web3 連接。 您可以使用此腳本來設定用於呼叫聯盟管理 Cmdlet 的全域變數。

```powershell-interactive
$Connection = New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
$MemberAccount = Import-Web3Account -ManagedAccountAddress '<Member account address>' -ManagedAccountPassword '<Member account password>'
$ContractConnection = Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>' -Web3Client $Connection
```

將取代為您在 *\<Member account password\>* 建立成員時使用的成員帳戶密碼。

在 [Azure 入口網站中尋找其他值：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 移至您的預設區塊鏈服務成員**總覽**頁面。

    ![成員總覽](./media/manage-consortium-powershell/member-overview.png)

    將 *\<Member account\>* 和取代為 *\<RootContract address\>* 入口網站中的值。

1. 針對 [端點位址]，選取 [**交易節點**]，然後選取 [**預設交易] 節點**。 預設節點的名稱與區塊鏈成員相同。
1. 選取 [連接字串]。

    ![連接字串](./media/manage-consortium-powershell/connection-strings.png)

    取代 *\<Endpoint address\>* 為**Https （存取金鑰1）** 或**HTTPs （存取金鑰2）** 的值。

## <a name="manage-the-network-and-smart-contracts"></a>管理網路和智慧合約

使用網路和智慧合約 Cmdlet，建立負責聯盟管理的區塊鏈端點智慧合約連接。

### <a name="import-consortiummanagementcontracts"></a>匯入-ConsortiumManagementContracts

使用此 Cmdlet 來連線到聯盟管理的智慧合約。 這些合約是用來管理和強制執行聯盟中的成員。

`Import-ConsortiumManagementContracts -RootContractAddress <String> -Web3Client <IClient>`

| 參數 | 說明 | 必要 |
|-----------|-------------|:--------:|
| RootContractAddress | 聯盟管理智慧合約的根合約位址 | Yes |
| Web3Client | 從 Web3Connection 取得的 Web3Client 物件 | 是 |

#### <a name="example"></a>範例

```powershell-interactive
Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>'  -Web3Client $Connection
```

### <a name="import-web3account"></a>匯入-Web3Account

使用此 Cmdlet 建立物件，以保存遠端節點管理帳戶的資訊。

`Import-Web3Account -ManagedAccountAddress <String> -ManagedAccountPassword <String>`

| 參數 | 說明 | 必要 |
|-----------|-------------|:--------:|
| ManagedAccountAddress | 區塊鏈成員帳戶位址 | Yes |
| ManagedAccountPassword | 帳戶位址密碼 | 是 |

#### <a name="example"></a>範例

```powershell-interactive
Import-Web3Account -ManagedAccountAddress '<Member account address>'  -ManagedAccountPassword '<Member account password>'
```

### <a name="new-web3connection"></a>新增-Web3Connection

使用此 Cmdlet 來建立與交易節點之 RPC 端點的連接。

`New-Web3Connection [-RemoteRPCEndpoint <String>]`

| 參數 | 說明 | 必要 |
|-----------|-------------|:--------:|
| RemoteRPCEndpoint | 區塊鏈成員端點位址 | 是 |

#### <a name="example"></a>範例

```powershell-interactive
New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
```

## <a name="manage-the-consortium-members"></a>管理聯盟成員

使用聯盟成員管理 Cmdlet 來管理聯盟內的成員。 可用的動作取決於您的聯盟角色。

### <a name="get-blockchainmember"></a>BlockchainMember

使用此 Cmdlet 取得成員詳細資料，或列出聯盟的成員。

`Get-BlockchainMember [[-Name] <String>] -Members <IContract> -Web3Client <IClient>`

| 參數 | 說明 | 必要 |
|-----------|-------------|:--------:|
| Name | 您想要取得詳細資料的區塊鏈服務成員名稱。 輸入名稱時，會傳回成員的詳細資料。 當省略名稱時，它會傳回所有聯盟成員的清單。 | No |
| 成員 | 從匯入-ConsortiumManagementContracts 取得的成員物件 | Yes |
| Web3Client | 從 Web3Connection 取得的 Web3Client 物件 | 是 |

#### <a name="example"></a>範例

[建立 Web3 連接](#establish-a-web3-connection)以設定 $ContractConnection 變數。

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

### <a name="remove-blockchainmember"></a>移除-BlockchainMember

使用此 Cmdlet 來移除區塊鏈成員。

`Remove-BlockchainMember -Name <String> -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>`

| 參數 | 說明 | 必要 |
|-----------|-------------|:--------:|
| Name | 要移除的成員名稱 | Yes |
| 成員 | 從匯入-ConsortiumManagementContracts 取得的成員物件 | Yes |
| Web3Account | 從匯入取得的 Web3Account 物件-Web3Account | Yes |
| Web3Client | 從 Web3Connection 取得的 Web3Client 物件 | 是 |

#### <a name="example"></a>範例

[建立 Web3 連接](#establish-a-web3-connection)，以設定 $ContractConnection 和 $MemberAccount 變數。

```powershell-interactive
$ContractConnection | Remove-BlockchainMember -Name <Member Name> -Web3Account $MemberAccount
```

### <a name="set-blockchainmember"></a>設定-BlockchainMember

使用此 Cmdlet 來設定區塊鏈成員屬性，包括顯示名稱和聯盟角色。

聯盟系統管理員可以設定所有成員的**DisplayName**和**Role** 。 具有使用者角色的聯盟成員只能變更自己成員的顯示名稱。

```
Set-BlockchainMember -Name <String> [-DisplayName <String>] [-AccountAddress <String>] [-Role <String>]
 -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>
```

| 參數 | 說明 | 必要 |
|-----------|-------------|:--------:|
| Name | 區塊鏈成員的名稱 | Yes |
| DisplayName | 新的顯示名稱 | No |
| AccountAddress | 帳戶位址 | No |
| 成員 | 從匯入-ConsortiumManagementContracts 取得的成員物件 | Yes |
| Web3Account | 從匯入取得的 Web3Account 物件-Web3Account | Yes |
| Web3Client |  從 Web3Connection 取得的 Web3Client 物件| 是 |

#### <a name="example"></a>範例

[建立 Web3 連接](#establish-a-web3-connection)，以設定 $ContractConnection 和 $MemberAccount 變數。

```powershell-interactive
$ContractConnection | Set-BlockchainMember -Name <Member Name> -DisplayName <Display name> -Web3Account $MemberAccount
```

## <a name="manage-the-consortium-members-invitations"></a>管理聯盟成員的邀請

使用聯盟成員邀請管理 Cmdlet 來管理聯盟成員的邀請。 可用的動作取決於您的聯盟角色。

### <a name="new-blockchainmemberinvitation"></a>新增-BlockchainMemberInvitation

使用此 Cmdlet 邀請新成員加入聯盟。

```
New-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>
```

| 參數 | 說明 | 必要 |
|-----------|-------------|:--------:|
| SubscriptionId | 要邀請之成員的 Azure 訂用帳戶識別碼 | Yes |
| 角色 | 聯盟角色。 值可以是 [系統管理員] 或 [使用者]。 ADMIN 是聯盟系統管理員角色。 使用者是聯盟成員角色。 | Yes |
| 成員 | 從匯入-ConsortiumManagementContracts 取得的成員物件 | Yes |
| Web3Account | 從匯入取得的 Web3Account 物件-Web3Account | Yes |
| Web3Client | 從 Web3Connection 取得的 Web3Client 物件 | 是 |

#### <a name="example"></a>範例

[建立 Web3 連接](#establish-a-web3-connection)，以設定 $ContractConnection 和 $MemberAccount 變數。

```powershell-interactive
$ContractConnection | New-BlockchainMemberInvitation -SubscriptionId <Azure Subscription ID> -Role USER -Web3Account $MemberAccount
```

### <a name="get-blockchainmemberinvitation"></a>BlockchainMemberInvitation

使用此 Cmdlet 來抓取或列出聯盟成員的邀請狀態。

`Get-BlockchainMemberInvitation [[-SubscriptionId] <String>] -Members <IContract> -Web3Client <IClient>`

| 參數 | 說明 | 必要 |
|-----------|-------------|:--------:|
| SubscriptionId | 要邀請之成員的 Azure 訂用帳戶識別碼。 如果提供訂用帳戶識別碼，它會傳回訂用帳戶識別碼的邀請詳細資料。 如果省略了訂用帳戶識別碼，它會傳回所有成員邀請的清單。 | No |
| 成員 | 從匯入-ConsortiumManagementContracts 取得的成員物件 | Yes |
| Web3Client | 從 Web3Connection 取得的 Web3Client 物件 | 是 |

#### <a name="example"></a>範例

[建立 Web3 連接](#establish-a-web3-connection)以設定 $ContractConnection 變數。

```powershell-interactive
$ContractConnection | Get-BlockchainMemberInvitation –SubscriptionId <Azure subscription ID>
```

#### <a name="example-output"></a>範例輸出

```
SubscriptionId                       Role CorrelationId
--------------                       ---- -------------
<Azure subscription ID>              USER             2
```

### <a name="remove-blockchainmemberinvitation"></a>移除-BlockchainMemberInvitation

使用此 Cmdlet 撤銷聯盟成員的邀請。

```
Remove-BlockchainMemberInvitation -SubscriptionId <String> -Members <IContract> -Web3Account <IAccount>
 -Web3Client <IClient>
```

| 參數 | 說明 | 必要 |
|-----------|-------------|:--------:|
| SubscriptionId | 要撤銷之成員的 Azure 訂用帳戶識別碼 | Yes |
| 成員 | 從匯入-ConsortiumManagementContracts 取得的成員物件 | Yes |
| Web3Account | 從匯入取得的 Web3Account 物件-Web3Account | Yes |
| Web3Client | 從 Web3Connection 取得的 Web3Client 物件 | 是 |

#### <a name="example"></a>範例

[建立 Web3 連接](#establish-a-web3-connection)，以設定 $ContractConnection 和 $MemberAccount 變數。

```powershell-interactive
$ContractConnection | Remove-BlockchainMemberInvitation -SubscriptionId <Subscription ID> -Web3Account $MemberAccount
```

### <a name="set-blockchainmemberinvitation"></a>設定-BlockchainMemberInvitation

使用此 Cmdlet 來設定現有邀請的**角色**。 只有聯盟系統管理員可以變更邀請。

```
Set-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>
```

| 參數 | 說明 | 必要 |
|-----------|-------------|:--------:|
| SubscriptionId | 要邀請之成員的 Azure 訂用帳戶識別碼 | Yes |
| 角色 | 邀請的新聯盟角色。 值可以是 [**使用者**] 或 [系統**管理員**]。 | Yes |
| 成員 |  從匯入-ConsortiumManagementContracts 取得的成員物件 | Yes |
| Web3Account | 從匯入取得的 Web3Account 物件-Web3Account | Yes |
| Web3Client | 從 Web3Connection 取得的 Web3Client 物件 | 是 |

#### <a name="example"></a>範例

[建立 Web3 連接](#establish-a-web3-connection)，以設定 $ContractConnection 和 $MemberAccount 變數。

```powershell-interactive
$ContractConnection | Set-BlockchainMemberInvitation -SubscriptionId <Azure subscription ID> -Role USER -Web3Account $MemberAccount
```

## <a name="next-steps"></a>後續步驟

如需有關聯盟、成員和節點的詳細資訊，請參閱[Azure 區塊鏈 Service 聯盟](consortium.md)
