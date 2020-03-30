---
title: 使用 Azure 管道部署自訂策略
titleSuffix: Azure AD B2C
description: 瞭解如何在 CI/CD 管道中使用 Azure DevOps 服務中的 Azure 管道在 CI/CD 管道中部署 Azure AD B2C 自訂策略。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b23b60ae49a4973fa04e6fa5f795f99536e32e7f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188744"
---
# <a name="deploy-custom-policies-with-azure-pipelines"></a>使用 Azure 管道部署自訂策略

通過使用在[Azure 管道][devops-pipelines]中設置的連續集成和傳遞 （CI/CD） 管道，可以在軟體交付和代碼控制自動化中包括 Azure AD B2C 自訂策略。 當您部署到不同的 Azure AD B2C 環境（例如開發、測試和生產）時，我們建議您刪除手動進程，並使用 Azure 管道執行自動測試。

要使 Azure 管道在 Azure AD B2C 中管理自訂策略，需要三個主要步驟：

1. 在 Azure AD B2C 租戶中創建 Web 應用程式註冊
1. 配置 Azure 回購
1. 配置 Azure 管道

> [!IMPORTANT]
> 使用 Azure 管道管理 Azure AD B2C**preview**自訂策略當前使用 Microsoft`/beta`圖形 API 終結點上可用的預覽操作。 不支援在生產應用程式中使用這些 API。 有關詳細資訊，請參閱 Microsoft[圖形 REST API 測試版終結點引用](https://docs.microsoft.com/graph/api/overview?toc=./ref/toc.json&view=graph-rest-beta)。

## <a name="prerequisites"></a>Prerequisites

* [Azure AD B2C 租戶](tutorial-create-tenant.md)，以及具有[B2C IEF 策略管理員](../active-directory/users-groups-roles/directory-assign-admin-roles.md#b2c-ief-policy-administrator)角色的目錄中使用者的憑據
* 上載到租戶的[自訂策略](custom-policy-get-started.md)
* 使用 Microsoft 圖形 API 權限原則在租戶中註冊[的管理應用](microsoft-graph-get-started.md)*。ReadWrite.TrustFramework*
* [Azure 管道](https://azure.microsoft.com/services/devops/pipelines/)和對[Azure DevOps 服務專案][devops-create-project]的訪問

## <a name="client-credentials-grant-flow"></a>用戶端憑據授予流

此處描述的方案使用 Azure 管道和 Azure AD B2C 之間的服務到服務調用，使用 OAuth 2.0[用戶端憑據授予流](../active-directory/develop/v1-oauth2-client-creds-grant-flow.md)。 此授予流允許 Azure 管道（機密用戶端）等 Web 服務使用自己的憑據，而不是在調用另一個 Web 服務（本例中為 Microsoft 圖形 API）時類比使用者進行身份驗證。 Azure 管道以非對話模式獲取權杖，然後向 Microsoft 圖形 API 發出請求。

## <a name="register-an-application-for-management-tasks"></a>註冊管理工作的應用程式

如[先決條件](#prerequisites)中所述，您需要一個應用程式註冊，即 PowerShell 腳本（由 Azure 管道執行）可用於訪問租戶中的資源。

如果您已有用於自動化任務的應用程式註冊，請確保已被授予**Microsoft 圖形** > **策略** > **策略.ReadWrite.TrustFramework**許可權，屬於應用註冊的**API 許可權**。

有關註冊管理應用程式的說明，請參閱使用 Microsoft[圖形管理 Azure AD B2C](microsoft-graph-get-started.md)。

## <a name="configure-an-azure-repo"></a>配置 Azure 回購

註冊管理應用程式後，即可為策略檔配置存儲庫。

1. 登錄到 Azure DevOps 服務組織。
1. [創建新專案][devops-create-project]或選擇現有專案。
1. 在專案中，導航到 **"存儲庫"** 並選擇"**檔**"頁。 選擇現有存儲庫或為此練習創建一個存儲庫。
1. 創建名為*B2CAssets 的*資料夾。 README.md命名所需的預留位置*檔*並**提交**該檔。 如果您願意，以後可以刪除此檔。
1. 將 Azure AD B2C 策略檔添加到*B2CAssets*資料夾。 這包括*TrustFrameworkBase.xml* *、TrustFrameWork 擴展*.xml、SignUpOrSignin.xml 、*設定檔編輯.xml、PasswordReset.xml*以及您創建的任何其他策略。 *SignUpOrSignin.xml* *PasswordReset.xml* 記錄每個 Azure AD B2C 策略檔的檔案名，以便在後面的步驟中使用（它們用作 PowerShell 腳本參數）。
1. 在存儲庫的根目錄中創建名為*腳本*的資料夾，命名預留位置檔*DeployToB2c.ps1*。 此時不要提交檔，您將在後面的步驟中提交該檔。
1. 將以下 PowerShell 腳本粘貼到*DeployToB2c.ps1*中，然後**提交**檔。 該腳本從 Azure AD 獲取權杖，並調用 Microsoft 圖形 API 將*B2CAssets*資料夾中的策略上載到 Azure AD B2C 租戶。

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

## <a name="configure-your-azure-pipeline"></a>配置 Azure 管道

通過使用自訂策略檔初始化並填充存儲庫，即可設置發佈管道。

### <a name="create-pipeline"></a>建立管線

1. 登錄到 Azure DevOps 服務組織並導航到專案。
1. 在專案中，選擇 **"** > 管道**釋放** > **新管道**"。
1. 在 **"選擇範本**"下，選擇 **"空作業**"。
1. 輸入**階段名稱**，例如 *"部署自訂策略*"，然後關閉窗格。
1. 選擇 **"添加專案**"，並在 **"源類型**"下選擇**Azure 存儲庫**。
    1. 選擇包含使用 PowerShell 腳本填充的*腳本*資料夾的源存儲庫。
    1. 選擇**預設分支**。 如果在上一節中創建了一個新存儲庫，則預設分支是*主*分支。
    1. *從預設分支保留"最新"的***預設版本**設置。
    1. 輸入存儲庫的**源別名**。 例如，*策略 Repo*。 請勿在別名名稱中包含任何空格。
1. 選擇 **"添加"**
1. 重具名管道以反映其意圖。 例如，*部署自訂策略管道*。
1. 選擇 **"保存**"以保存管道配置。

### <a name="configure-pipeline-variables"></a>配置管道變數

1. 選擇"**變數"** 選項卡。
1. 在**管道變數**下添加以下變數，並按照指定設置其值：

    | 名稱 | 值 |
    | ---- | ----- |
    | `clientId` | 您之前註冊的應用程式**的應用程式的應用程式（用戶端）ID。** |
    | `clientSecret` | 您之前創建的**用戶端機密**的值。 <br /> 將變數類型更改為**機密**（選擇鎖定圖示）。 |
    | `tenantId` | `your-b2c-tenant.onmicrosoft.com`，其中*b2c 租戶*是 Azure AD B2C 租戶的名稱。 |

1. 選擇 **"保存"** 以保存變數。

### <a name="add-pipeline-tasks"></a>添加管道任務

接下來，添加任務以部署策略檔。

1. 選擇"**任務**"選項卡。
1. 選擇 **"代理"作業**，然後選擇加號**+**（ ） 以將任務添加到"代理"作業。
1. 搜索並選擇**PowerShell**。 不要選擇"Azure PowerShell"、"目的電腦上的 PowerShell"或其他 PowerShell 條目。
1. 選擇新添加**的 PowerShell 腳本**任務。
1. 輸入 PowerShell 腳本任務的以下值：
    * **任務版本**： 2.*
    * **顯示名稱**：此任務應上載的策略的名稱。 例如 *，B2C_1A_TrustFrameworkBase*。
    * **類型**： 檔路徑
    * **腳本路徑**：選擇省略號 ***（...），*** 導航到*腳本*資料夾，然後選擇*DeployToB2C.ps1*檔。
    * **引數:**

        輸入參數**的**以下值。 替換為`{alias-name}`您在上一節中指定的別名。

        ```PowerShell
        # Before
        -ClientID $(clientId) -ClientSecret $(clientSecret) -TenantId $(tenantId) -PolicyId B2C_1A_TrustFrameworkBase -PathToFile $(System.DefaultWorkingDirectory)/{alias-name}/B2CAssets/TrustFrameworkBase.xml
        ```

        例如，如果您指定的別名是*策略 Repo，* 則參數行應為：

        ```PowerShell
        # After
        -ClientID $(clientId) -ClientSecret $(clientSecret) -TenantId $(tenantId) -PolicyId B2C_1A_TrustFrameworkBase -PathToFile $(System.DefaultWorkingDirectory)/policyRepo/B2CAssets/TrustFrameworkBase.xml
        ```

1. 選擇 **"保存**"以保存代理作業。

剛剛添加的任務將*一個*策略檔上載到 Azure AD B2C。 在繼續操作之前，手動觸發作業 （**創建版本**），以確保在創建其他任務之前成功完成。

如果任務成功完成，則通過為每個自訂策略檔執行上述步驟來添加部署任務。 修改每個`-PolicyId`策略`-PathToFile`的 和 參數值。

是在`PolicyId`TrustFrameworkPolicy 節點中的 XML 策略檔開頭找到的值。 例如，`PolicyId`以下策略 XML 中*B2C_1A_TrustFrameworkBase*：

```XML
<TrustFrameworkPolicy
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xmlns:xsd="http://www.w3.org/2001/XMLSchema"
xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
PolicySchemaVersion="0.3.0.0"
TenantId="contoso.onmicrosoft.com"
PolicyId= "B2C_1A_TrustFrameworkBase"
PublicPolicyUri="http://contoso.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
```

運行代理並上載策略檔時，請確保按以下順序上載它們：

1. *TrustFrameworkBase.xml*
1. *TrustFrameworkExtensions.xml*
1. *註冊或Signin.xml*
1. *設定檔編輯.xml*
1. *密碼重設.xml*

當檔結構構建在分層鏈上時，標識體驗框架強制執行此順序。

## <a name="test-your-pipeline"></a>測試您的管線

要測試發佈管道，

1. 選擇**管道**，然後**釋放**。
1. 選擇您之前創建的管道，例如*部署自訂策略*。
1. 選擇 **"創建發佈**"，然後選擇 **"創建"** 以對發佈進行排隊。

您應該會看到一個通知橫幅，該橫幅顯示發佈已排隊。 要查看其狀態，請在通知橫幅中選擇連結，或在 **"發佈"** 選項卡的清單中選擇該連結。

## <a name="next-steps"></a>後續步驟

深入了解：

* [使用用戶端憑據的服務到服務調用](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)
* [Azure 開發人員服務](https://docs.microsoft.com/azure/devops/user-guide/?view=azure-devops)

<!-- LINKS - External -->
[devops]: https://docs.microsoft.com/azure/devops/?view=azure-devops
[devops-create-project]:  https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops
[devops-pipelines]: https://docs.microsoft.com/azure/devops/pipelines
