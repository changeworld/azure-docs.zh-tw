---
title: Azure 證明疑難排解指南
description: 常見問題的疑難排解指南
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: reference
ms.date: 07/20/2020
ms.author: mbaldwin
ms.openlocfilehash: 3ae3e12c11f194b3efcc149382dc952bd74d38b5
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/20/2020
ms.locfileid: "97704311"
---
# <a name="microsoft-azure-attestation-troubleshooting-guide"></a>Microsoft Azure 證明疑難排解指南

Azure 證明中的錯誤處理是遵循 [Microsoft REST API 指導方針](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#7102-error-condition-responses)來實行。 Azure 證明 Api 所傳回的錯誤回應包含 HTTP 狀態碼，以及名稱/值組的名稱為 "code" 和 "message"。 "Code" 的值是人類看得懂的，且是錯誤類型的指標。 "Message" 的值旨在協助使用者，並提供錯誤詳細資料。

如果本文未討論您的問題，您也可以在 [ [Azure 支援] 頁面](https://azure.microsoft.com/support/options/)上提交 Azure 支援要求。

以下是 Azure 證明所傳回錯誤的一些範例：

## <a name="1-http401--unauthorized-exception"></a>1. HTTP –401：未經授權的例外狀況

### <a name="http-status-code"></a>HTTP 狀態碼
401

**錯誤碼** 未經 授權

**案例範例**
  - 如果未指派證明讀者角色給使用者，則證明失敗
  - 因為未指派適當的角色給使用者，所以無法管理證明原則
  - 因為未指派適當的角色給使用者，所以無法管理證明原則簽署者

具有讀取者角色的使用者嘗試在 PowerShell 中編輯證明原則 

  ```powershell
  Set-AzAttestationPolicy : Operation returned HTTP Status Code 401
At line:1 char:1
+ Set-AzAttestationPolicy -Name $attestationProvider -ResourceGroupName ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzAttestationPolicy], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.SetAzureAttestationPolicy
  ```

**疑難排解步驟**

為了查看證明原則/原則簽署者，Azure AD 使用者需要「動作」的許可權：
- Microsoft.Attestation/attestationProviders/attestation/read

  您可以透過「擁有者」之類的角色，將此許可權指派給 AD 使用者 (萬用字元許可權) 或「讀者」 (萬用字元許可權) 或「證明讀者」 (僅) Azure 證明的特定許可權。

為了新增/刪除原則簽署者或設定原則，Azure AD 使用者需要下列「動作」許可權：
- Microsoft.Attestation/attestationProviders/attestation/write
- Microsoft.Attestation/attestationProviders/attestation/delete

  您可以透過「擁有者」之類的角色將這些許可權指派給 AD 使用者 (萬用字元許可權) 、「參與者」 (萬用字元許可權) 或「證明參與者」 (僅) Azure 證明的特定許可權。

客戶可以選擇使用預設提供者進行證明，或利用自訂原則建立自己的提供者。 若要將證明要求傳送給自訂證明提供者，使用者必須擁有「擁有者」 (萬用字元許可權) 或「讀者」 (萬用字元許可權) 或「證明讀者」角色。 任何 Azure AD 使用者都可以存取預設提供者。

若要在 PowerShell 中驗證角色，請執行下列動作：

a. 透過 "Disconnect-azaccount" Cmdlet 啟動 PowerShell 並登入 Azure

b. 驗證您的 Azure 角色指派設定


  ```powershell
  $c = Get-AzContext
  Get-AzRoleAssignment -ResourceGroupName $attestationResourceGroup -ResourceName $attestationProvider -ResourceType Microsoft.Attestation/attestationProviders -SignInName $c.Account.Id
  ```

  您應該會看到如下內容：

  ```
  RoleAssignmentId   :/subscriptions/subscriptionId/providers/Microsoft.Authorization/roleAssignments/roleAssignmentId
  
  Scope              : /subscriptions/subscriptionId
  
  DisplayName        : displayName
  
  SignInName         : signInName
  
  RoleDefinitionName : Reader
  
  RoleDefinitionId   : roleDefinitionId
  
  ObjectId           : objectid
  
  ObjectType         : User
  
  CanDelegate        : False
 
  ```

c. 如果您在清單中找不到適當的角色指派，請遵循[此處](../role-based-access-control/role-assignments-powershell.md)的指示。

## <a name="2-http--400-errors"></a>2. HTTP-400 錯誤

### <a name="http-status-code"></a>HTTP 狀態碼
400

要求可能會傳回400的原因有很多種。 以下是 Azure 證明 Api 傳回的一些錯誤範例：

### <a name="21-attestation-failure-due-to-policy-evaluation-errors"></a>2.1. 證明因為原則評估錯誤而失敗

證明原則包含授權規則和發佈規則。 記憶體保護區辨識項是根據授權規則進行評估。 發行規則會定義要包含在證明權杖中的宣告。 如果記憶體保護區證據中的宣告不符合授權規則，證明呼叫會傳回原則評估錯誤。 

**錯誤碼** PolicyEvaluationError

**案例範例** 當記憶體保護區報價中的宣告與證明原則的授權規則不相符時

```
Native operation failed with 65518: G:\Az\security\Attestation\src\AttestationServices\Instance\NativePolicyWrapper\NativePolicyEngine.cpp(168)\(null)!00007FF801762308: (caller: 00007FF80143DCC8) Exception(0) 83FFFFEE Policy Evaluation Error has occurred Msg:[Policy Engine Exception: A Deny claim was issued, authorization failed.]

G:\Az\security\Attestation\src\AttestationServices\Instance\Enclave\api.cpp(840)\(null)!00007FF801739FF3: (caller: 00007FF801232801) LogHr(0) 83FFFFEE Policy Evaluation Error has occurred Msg:[Unhandled Enclave Exception: "Policy Evaluation Error has occurred"]

```

**疑難排解步驟** 在設定相同之前，使用者可以針對 SGX 證明原則評估記憶體保護區辨識項。

在 "draftPolicyForAttestation" 參數中提供原則文字，以傳送證明 API 的要求。 AttestSgxEnclave API 會在證明通話期間使用此原則檔，而這可在取用證明原則之前用來進行測試。 當這個欄位存在時所產生的證明權杖將會是不安全的。

請參閱 [證明原則範例](./policy-examples.md)

### <a name="22-attestation-failure-due-to-invalid-input"></a>2.2. 因為輸入無效，所以證明失敗

**錯誤碼** InvalidParameter

**案例範例** 因為輸入無效，導致 SGX 證明失敗。 以下是一些錯誤訊息的範例：
- 因為報價的附屬品有錯誤，所以指定的報價無效 
- 指定的報價無效，因為產生報價的裝置不符合 Azure 基準需求
- 指定的報價無效，因為 PCK 快取服務提供的 TCBInfo 或 QEID 無效

**疑難排解步驟**

Microsoft Azure 證明可支援 Intel SDK 和 Open 記憶體保護區 SDK 所產生的 SGX 報價證明。

請參閱使用 Open 記憶體保護區 SDK/Intel SDK 執行證明的程式[代碼範例](/samples/browse/?expanded=azure&terms=attestation)

### <a name="23-invalid-certificate-chain-error-while-uploading-policypolicy-signer"></a>2.3. 上傳原則/原則簽署者時發生不正確憑證鏈錯誤

**錯誤碼** InvalidParameter

**案例範例** 設定已簽署的原則或新增/刪除原則簽署者（使用不正確憑證鏈簽署） (例如，當根憑證的基本限制延伸未設定為主體類型 = CA) 

```
Native operation failed with 65529: C:\source\src\AttestationServices\Instance\SgxPal\sgxcert.cpp(1074)\(null)!00007FFA285CDAED: (caller: 00007FFA285C36E8) Exception(0) 83FFFFF9 The requested item is not found    Msg:[Unable to find issuer certificate CN=attestationsigningcert]
C:\source\src\AttestationServices\Instance\Enclave\api.cpp(618)\(null)!00007FFA286DCBF8: (caller: 00007FFA285860D3) LogHr(0) 83FFFFF9 The requested item is not found    Msg:[Unhandled Enclave Exception: "The requested item is not found"]
At line:1 char:1
+ Set-AzAttestationPolicy -Name "testpolicy1" -ResourceGroupName "BugBa ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzAttestationPolicy], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.SetAzureAttestationPolicy

```

**疑難排解步驟** 根憑證必須標示為由 CA 發出 (x.509 基本限制) ，否則不會被視為有效的憑證。 

確認根憑證的基本限制延伸已設定為指出主體類型 = CA

否則，憑證鏈會視為無效。

請參閱 [原則簽署者](./policy-signer-examples.md) 和 [原則](./policy-examples.md) 範例 

### <a name="24-adddelete-policy-signer-failure"></a>2.4. 新增/刪除原則簽署人失敗

**錯誤碼** InvalidOperation

**案例範例**

當使用者上傳不含 "maa-policyCertificate" 宣告的 JWS 時

```
Add-AzAttestationPolicySigner : Operation returned HTTP Status Code 400
Code: InvalidOperation
Message: Native operation failed with 74: ..\Enclave\enclave.cpp(2213)\(null)!: (caller: ) Exception(0) 83FF004A Bad
message    Msg:[Could not find "maa-policyCertificate" claim in policy token]
..\Enclave\api.cpp(496)\(null)!: (caller: ) LogHr(0) 83FF004A Bad message    Msg:[Unhandled Enclave Exception: "Bad
message"]
At line:1 char:1
+ Add-AzAttestationPolicySigner -Name $attestationProvider -ResourceGro ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Add-AzAttestationPolicySigner], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.AddAzureAttestationPolicySigner

```

當使用者未上傳 JWS 格式的憑證時

```
Add-AzAttestationPolicySigner : Operation returned HTTP Status Code 400
Code: InvalidOperation
Message: Native operation failed with 74: ..\JsonWebToken\jsonwebtoken.cpp(375)\(null)!: (caller: ) LogHr(0) 83FF004A
Bad message    Msg:[RETURN_IF_TRUE('(firstPeriod == std::string::npos)') failed with 0x4a: Malformed JWT: Could not
find first period in the token.]
..\Enclave\enclave.cpp(2106)\(null)!: (caller: ) LogHr(0) 83FF004A Bad message
Msg:[THROW_IF_ERROR('DecomposeJsonWebSignature(&policyJws, encodedJoseHeader, encodedJwsBody, jwsSignature)') failed
with 0x4a: 'Bad message']
..\Enclave\enclave.cpp(2106)\(null)!: (caller: ) Exception(0) 83FF004A Bad message
..\Enclave\api.cpp(496)\(null)!: (caller: ) LogHr(0) 83FF004A Bad message    Msg:[Unhandled Enclave Exception: "Bad
message"]
At line:1 char:1
+ Add-AzAttestationPolicySigner -Name $attestationProvider -ResourceGro ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Add-AzAttestationPolicySigner], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.AddAzureAttestationPolicySigner
```

**疑難排解步驟** 若要新增/刪除新的原則簽署者憑證，請使用 RFC7519 JSON Web 權杖 (JWT) 搭配名為 "x-policyCertificate" 的宣告。 宣告的值是 RFC7517 JSON Web 金鑰，其中包含要新增的憑證。 JWT 必須使用任何與提供者相關聯的有效原則簽署者憑證的私密金鑰進行簽署。 請參閱 [原則簽署者範例](./policy-signer-examples.md)。

### <a name="25-attestation-policy-configuration-failure"></a>2.5. 證明原則設定失敗

**錯誤碼** PolicyParsingError

**案例範例** 提供語法不正確的原則 (例如，遺漏分號) /valid JWT 原則) 

```
Native operation failed with 65526: ..\NativePolicyWrapper\NativePolicyEngine.cpp(31)\(null)!: (caller: ) Exception(0) 83FFFFF6 Invalid policy was specified    Msg:[Policy Parser Exception Thrown: Offending
symbol: '['
Line: '2', Column: '1'
Failure message: 'mismatched input '[' expecting ';''
Failing rule: 'policy > versionInfo']
..\Enclave\api.cpp(618)\(null)!: (caller: ) LogHr(0) 83FFFFF6 Invalid policy was specified    Msg:[Unhandled Enclave Exception: "Invalid policy was specified"]
At line:1 char:1
+ set-AzAttestationPolicy -Name $attestationProvider -ResourceGroupName ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzAttestationPolicy], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.SetAzureAttestationPolicy
```

**錯誤碼** InvalidOperation

**案例範例** 提供的內容無效 (例如，需要原則簽署時，上傳原則/不帶正負號的原則) 

```
Native operation failed with 74: ..\Shared\base64url.h(226)\(null)!: (caller: ) Exception(0) 83FF004A Bad message    Msg:[Unknown base64 character: 41 (')')]
..\Enclave\api.cpp(618)\(null)!: (caller: ) LogHr(0) 83FF004A Bad message    Msg:[Unhandled Enclave Exception: "Bad message"]
At line:1 char:1
+ set-AzAttestationPolicy -Name $attestationProvider -ResourceGroupName ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzAttestationPolicy], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.SetAzureAttestationPolicy
```

**疑難排解步驟** 確定文字格式的原則為 UTF-8 編碼。

如果需要原則簽署，則必須只在 RFC7519 JSON Web 權杖中設定證明原則 (JWT) 格式。 如果不需要原則簽署，可以設定文字或 JWT 格式的原則。

若要設定 JWT 格式的原則，請使用 JWT 和名為 "AttestationPolicy" 的宣告。 宣告的值是原則文字的 Base64URL 編碼版本。 如果證明提供者已設定原則簽署者憑證，則必須使用與提供者相關聯之任何有效原則簽署者憑證的私密金鑰來簽署 JWT。 

若要以文字格式設定原則，請直接指定原則文字。

在 PowerShell 中，將 PolicyFormat 指定為 JWT 以設定 JWT 格式的原則。 預設原則格式為 Text。

請參閱證明 [原則範例](./policy-examples.md) 及 [如何撰寫證明原則](./author-sign-policy.md) 

## <a name="3-azattestation-installation-issues-in-powershell"></a>3. 將 PowerShell 中的證明安裝問題

無法在 PowerShell 中安裝 Az 或 Az. 證明模組

### <a name="error"></a>[錯誤]

警告：無法解析套件來源 ' https://www.powershellgallery.com/api/v2 ' PackageManagement\Install-Package：找不到符合指定搜尋準則和模組名稱的相符項

### <a name="troubleshooting-steps"></a>疑難排解步驟

PowerShell 資源庫已淘汰傳輸層安全性 (TLS) 1.0 和1.1 版。 

建議使用 TLS 1.2 或更新版本。 

若要繼續與 PowerShell 資源庫互動，請在 Install-Module 命令之前執行下列命令

**[ServicePointManager]：： SecurityProtocol = [Net. SecurityProtocolType]：： Tls12**

## <a name="4-policy-accessconfiguration-issues-in-powershell"></a>4. PowerShell 中的原則存取/設定問題

已指派適當角色的使用者。 但透過 PowerShell 管理證明原則時遇到授權問題。

### <a name="error"></a>[錯誤]
物件識別碼物件識別碼的用戶端沒有 &lt; &gt;  授權，無法執行 Microsoft 的動作。授權/roleassignments/寫入範圍 ' 經由 open 訂閱/ &lt; subscriptionId &gt; resourcegroups/secure_enclave_poc/providers/microsoft.authorization/roleassignments/ &lt; role assignmentId &gt; ' 或範圍無效。 如果最近才授與存取權，請重新整理您的認證

### <a name="troubleshooting-steps"></a>疑難排解步驟

支援證明作業所需的 Az 模組最小版本如下： 

 **Az 4.5.0** 
 
 **Az.Accounts 1.9.2**
 
 **Az.Attestation 0.1.8** 

執行下列命令來確認已安裝的所有 Az 模組版本 

```powershell
Get-InstalledModule 
```

如果版本不符合最低需求，請執行 Update-Module 命令

例如-Update-Module-名稱 Az. 證明
