---
title: 使用 Azure Pipelines 部署自訂原則
titleSuffix: Azure AD B2C
description: 瞭解如何在 Azure DevOps Services 中使用 Azure Pipelines，在 CI/CD 管線中部署 Azure AD B2C 自訂原則。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 411fa207323a9bff6cfcc3b17769203c444dd844
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85388675"
---
# <a name="deploy-custom-policies-with-azure-pipelines"></a>使用 Azure Pipelines 部署自訂原則

藉由使用持續整合和傳遞 (您在 [Azure Pipelines][devops-pipelines]中設定的 CI/CD) 管線，您可以在軟體傳遞和程式碼控制自動化中包含 Azure AD B2C 自訂原則。 當您部署至不同的 Azure AD B2C 環境（例如開發、測試和生產環境）時，建議您移除手動程式，並使用 Azure Pipelines 來執行自動化測試。

啟用 Azure Pipelines 來管理 Azure AD B2C 內的自訂原則需要三個主要步驟：

1. 在您的 Azure AD B2C 租使用者中建立 web 應用程式註冊
1. 設定 Azure 存放庫
1. 設定 Azure 管線

> [!IMPORTANT]
> 使用 Azure 管線管理 Azure AD B2C 自訂原則目前使用 Microsoft Graph API 端點上提供的 **預覽** 作業 `/beta` 。 不支援在生產應用程式中使用這些 API。 如需詳細資訊，請參閱 [Microsoft Graph REST API Beta 端點參考](https://docs.microsoft.com/graph/api/overview?toc=./ref/toc.json&view=graph-rest-beta)。

## <a name="prerequisites"></a>必要條件

* [Azure AD B2C 租](tutorial-create-tenant.md)使用者，以及具有 [B2C IEF 原則系統管理員](../active-directory/users-groups-roles/directory-assign-admin-roles.md#b2c-ief-policy-administrator) 角色之目錄中的使用者認證
* 已上傳至您租使用者的[自訂原則](custom-policy-get-started.md)
* 已在您的租使用者中註冊使用 Microsoft Graph API 許可權原則的[管理應用程式](microsoft-graph-get-started.md) *。 TrustFramework*
* [Azure 管線](https://azure.microsoft.com/services/devops/pipelines/)和[Azure DevOps Services 專案][devops-create-project]的存取權

## <a name="client-credentials-grant-flow"></a>用戶端認證授與流程

此處所述的案例會使用 OAuth 2.0 [用戶端認證授與流程](../active-directory/develop/v1-oauth2-client-creds-grant-flow.md)，在 Azure Pipelines 和 Azure AD B2C 之間使用服務對服務呼叫。 此授與流程可允許 Azure Pipelines (機密) 用戶端的 web 服務使用自己的認證，而不是在 Microsoft Graph API （在此案例中) ）呼叫另一個 web (服務時，模擬使用者進行驗證。 Azure Pipelines 以非互動方式取得權杖，然後對 Microsoft Graph API 提出要求。

## <a name="register-an-application-for-management-tasks"></a>註冊管理工作的應用程式

如 [必要條件](#prerequisites)中所述，您需要應用程式註冊，您的 PowerShell 腳本（由 Azure Pipelines 執行）可以用來存取您租使用者中的資源。

如果您已經有用於自動化工作的應用程式註冊，請確定其已被授與應用**Microsoft Graph**程式註冊之  >  **Policy**  >  **API 許可權**內的 Microsoft Graph 原則**TrustFramework**許可權。

如需註冊管理應用程式的指示，請參閱 [使用 Microsoft Graph 管理 Azure AD B2C](microsoft-graph-get-started.md)。

## <a name="configure-an-azure-repo"></a>設定 Azure 存放庫

註冊管理應用程式之後，您就可以設定原則檔案的存放庫。

1. 登入您的 Azure DevOps Services 組織。
1. [建立新的專案][devops-create-project] ，或選取現有的專案。
1. 在您的專案中，流覽至 **存放庫** ，然後 **選取 [檔案** ] 頁面。 選取現有的存放庫，或在此練習中建立一個。
1. 建立名為 *B2CAssets*的資料夾。 將必要的預留位置檔案命名為 *README.md* ，並 **認可** 檔案。 您可以稍後視需要移除此檔案。
1. 將 Azure AD B2C 原則檔新增至 *B2CAssets* 資料夾。 這包括 *TrustFrameworkBase.xml*、 *TrustFrameWorkExtensions.xml*、 *SignUpOrSignin.xml*、 *ProfileEdit.xml*、 *PasswordReset.xml*，以及您已建立的任何其他原則。 記錄每個 Azure AD B2C 原則檔案的檔案名，以便在稍後的步驟中使用 (它們作為 PowerShell 腳本引數) 。
1. 在存放庫的根目錄中建立名為 *Scripts* 的資料夾，並將預留位置檔案命名為 *DeployToB2c.ps1*。 請勿在此時認可檔案，您將在稍後的步驟中執行此動作。
1. 將下列 PowerShell 腳本貼入 *DeployToB2c.ps1*，然後 **認可** 檔案。 此腳本會從 Azure AD 取得權杖，並呼叫 Microsoft Graph API，以將 *B2CAssets* 資料夾內的原則上傳至您的 Azure AD B2C 租使用者。

    ```PowerShell
    [Cmdletbinding()]
    Param(
        [Parameter(Mandatory = $true)][string]$ClientID,
        [Parameter(Mandatory = $true)][string]$ClientSecret,
        [Parameter(Mandatory = $true)][string]$TenantId,
        [Parameter(Mandatory = $true)][string]$PolicyId,
        [Parameter(Mandatory = $true)][string]$PathToFile
    )

    try {
        $body = @{grant_type = "client_credentials"; scope = "https://graph.microsoft.com/.default"; client_id = $ClientID; client_secret = $ClientSecret }

        $response = Invoke-RestMethod -Uri https://login.microsoftonline.com/$TenantId/oauth2/v2.0/token -Method Post -Body $body
        $token = $response.access_token

        $headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
        $headers.Add("Content-Type", 'application/xml')
        $headers.Add("Authorization", 'Bearer ' + $token)

        $graphuri = 'https://graph.microsoft.com/beta/trustframework/policies/' + $PolicyId + '/$value'
        $policycontent = Get-Content $PathToFile
        $response = Invoke-RestMethod -Uri $graphuri -Method Put -Body $policycontent -Headers $headers

        Write-Host "Policy" $PolicyId "uploaded successfully."
    }
    catch {
        Write-Host "StatusCode:" $_.Exception.Response.StatusCode.value__

        $_

        $streamReader = [System.IO.StreamReader]::new($_.Exception.Response.GetResponseStream())
        $streamReader.BaseStream.Position = 0
        $streamReader.DiscardBufferedData()
        $errResp = $streamReader.ReadToEnd()
        $streamReader.Close()

        $ErrResp

        exit 1
    }

    exit 0
    ```

## <a name="configure-your-azure-pipeline"></a>設定您的 Azure 管線

當您的存放庫初始化並填入您的自訂原則檔案時，就可以開始設定發行管線。

### <a name="create-pipeline"></a>建立管線

1. 登入您的 Azure DevOps Services 組織，然後流覽至您的專案。
1. 在您的專案中，選取 [**管線**  >  **釋放**  >  **新的管線**]。
1. 在 [ **選取範本**] 下，選取 [ **空白作業**]。
1. 輸入 **階段名稱**（例如 *DeployCustomPolicies*），然後關閉窗格。
1. 選取 [ **新增**成品]，然後在 [ **來源類型**] 下選取 [ **Azure 儲存**機制]。
    1. 選擇包含您以 PowerShell 腳本填入的 *腳本* 資料夾的來源存放庫。
    1. 選擇 **預設分支**。 如果您在上一節中建立了新的存放庫，則預設分支為 *master*。
    1. 保留預設分支的 **預設版本** 設定為 [ *最新*]。
    1. 輸入存放庫的 **來源別名** 。 例如， *policyRepo*。 請勿在別名名稱中包含任何空格。
1. 選取 [新增]
1. 重新命名管線以反映其意圖。 例如， *部署自訂原則管線*。
1. 選取 [ **儲存** ] 以儲存管線設定。

### <a name="configure-pipeline-variables"></a>設定管線變數

1. 選取 [ **變數** ] 索引標籤。
1. 在 **管線變數** 底下新增下列變數，並依照指定的方式設定其值：

    | 名稱 | 值 |
    | ---- | ----- |
    | `clientId` | **應用程式 (用戶端) ** 您稍早註冊之應用程式的識別碼。 |
    | `clientSecret` | 您稍早建立的 **用戶端密碼** 的值。 <br /> 將變數類型變更為 **秘密** (選取鎖定圖示) 。 |
    | `tenantId` | `your-b2c-tenant.onmicrosoft.com`，其中 *您的 b2c 租使用者* 是 Azure AD B2C 租使用者的名稱。 |

1. 選取 [ **儲存** ] 以儲存變數。

### <a name="add-pipeline-tasks"></a>新增管線工作

接下來，新增工作以部署原則檔。

1. 選取 [ **工作** ] 索引標籤。
1. 選取 [ **代理程式作業**]，然後選取加號 (**+**) 將工作新增至代理程式作業。
1. 搜尋並選取 [ **PowerShell**]。 請勿選取「Azure PowerShell」、「目的電腦上的 PowerShell」或另一個 PowerShell 專案。
1. 選取新增的 **PowerShell 腳本** 工作。
1. 針對 PowerShell 腳本工作輸入下列值：
    * 工作**版本**： 2. *
    * **顯示名稱**：此工作應該上傳的原則名稱。 例如， *B2C_1A_TrustFrameworkBase*。
    * **類型**：檔案路徑
    * **腳本路徑**：選取省略號 (***...***) ，流覽至 [ *腳本* ] 資料夾，然後選取 *DeployToB2C.ps1* 的檔案。
    * **引數:**

        輸入 **引數**的下列值。 取代 `{alias-name}` 為您在上一節中指定的別名。

        ```PowerShell
        # Before
        -ClientID $(clientId) -ClientSecret $(clientSecret) -TenantId $(tenantId) -PolicyId B2C_1A_TrustFrameworkBase -PathToFile $(System.DefaultWorkingDirectory)/{alias-name}/B2CAssets/TrustFrameworkBase.xml
        ```

        例如，如果您指定的別名是 *policyRepo*，則引數行應該是：

        ```PowerShell
        # After
        -ClientID $(clientId) -ClientSecret $(clientSecret) -TenantId $(tenantId) -PolicyId B2C_1A_TrustFrameworkBase -PathToFile $(System.DefaultWorkingDirectory)/policyRepo/B2CAssets/TrustFrameworkBase.xml
        ```

1. 選取 [ **儲存** ] 以儲存代理程式作業。

您剛剛新增的工作會將 *一個* 原則檔上傳至 Azure AD B2C。 在繼續之前，請手動觸發工作 (**建立版本**) ，以確保在建立其他工作之前成功完成。

如果工作成功完成，請針對每個自訂原則檔執行上述步驟來新增部署工作。 修改 `-PolicyId` `-PathToFile` 每個原則的和引數值。

`PolicyId`是在 TrustFrameworkPolicy 節點內的 XML 原則檔開頭找到的值。 例如， `PolicyId` 下列原則 XML 中的 *B2C_1A_TrustFrameworkBase*：

```xml
<TrustFrameworkPolicy
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xmlns:xsd="http://www.w3.org/2001/XMLSchema"
xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
PolicySchemaVersion="0.3.0.0"
TenantId="contoso.onmicrosoft.com"
PolicyId= "B2C_1A_TrustFrameworkBase"
PublicPolicyUri="http://contoso.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
```

執行代理程式並上傳原則檔案時，請確定這些檔案是依下列順序上傳：

1. *TrustFrameworkBase.xml*
1. *TrustFrameworkExtensions.xml*
1. *SignUpOrSignin.xml*
1. *ProfileEdit.xml*
1. *PasswordReset.xml*

Identity Experience Framework 會強制執行此順序，因為檔案結構是以階層式鏈為基礎。

## <a name="test-your-pipeline"></a>測試您的管線

若要測試您的發行管線：

1. 選取 [ **管線** ]，然後再 **放開**。
1. 選取您稍早建立的管線，例如 *DeployCustomPolicies*。
1. 選取 [ **建立發行**]，然後選取 [ **建立** ] 以將發行排入佇列。

您應該會看到通知橫幅，指出發行已排入佇列。 若要查看其狀態，請選取通知橫幅中的連結，或在 [ **發行** ] 索引標籤上的清單中選取該連結。

## <a name="next-steps"></a>後續步驟

深入了解：

* [使用用戶端認證的服務對服務呼叫](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)
* [Azure DevOps Services](https://docs.microsoft.com/azure/devops/user-guide/?view=azure-devops)

<!-- LINKS - External -->
[devops]: https://docs.microsoft.com/azure/devops/?view=azure-devops
[devops-create-project]:  https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops
[devops-pipelines]: https://docs.microsoft.com/azure/devops/pipelines
