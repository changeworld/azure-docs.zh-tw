---
title: 使用 Azure 自動化重新整理 Azure Analysis Services 模型 |Microsoft Docs
description: 本文說明如何使用 Azure 自動化，針對 Azure Analysis Services 的程式碼模型重新整理。
author: chrislound
ms.service: analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: chlound
ms.openlocfilehash: 7908b168adcaa00819990d7ced04ba4888d67d63
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2020
ms.locfileid: "82871312"
---
# <a name="refresh-with-azure-automation"></a>使用 Azure 自動化重新整理

藉由使用 Azure 自動化和 PowerShell Runbook，您可以在 Azure 分析方格式模型上執行自動化的資料重新整理作業。  

本文中的範例會使用[PowerShell SqlServer 模組](https://docs.microsoft.com/powershell/module/sqlserver/?view=sqlserver-ps)。

本文稍後會提供範例 PowerShell Runbook，其中示範重新整理模型。  

## <a name="authentication"></a>驗證

所有呼叫都必須使用有效的 Azure Active Directory （OAuth 2）權杖進行驗證。  本文中的範例將使用服務主體（SPN）向 Azure Analysis Services 進行驗證。

若要深入瞭解如何建立服務主體，請參閱[使用 Azure 入口網站建立服務主體](../active-directory/develop/howto-create-service-principal-portal.md)。

## <a name="prerequisites"></a>Prerequisites

> [!IMPORTANT]
> 下列範例假設 Azure Analysis Services 防火牆已停用。 如果已啟用防火牆，則要求啟動器的公用 IP 位址必須在防火牆的白名單中。

### <a name="install-sqlserver-modules-from-powershell-gallery"></a>從 PowerShell 資源庫安裝 SqlServer 模組。

1. 在您的 Azure 自動化帳戶中，依序按一下 [**模組** **] 和 [流覽資源庫]**。

2. 在搜尋列中，搜尋**SqlServer**。

    ![搜尋模組](./media/analysis-services-refresh-azure-automation/1.png)

3. 選取 [SqlServer]，然後按一下 [匯**入**]。
 
    ![匯入模組](./media/analysis-services-refresh-azure-automation/2.png)

4. 按一下 [確定]  。
 
### <a name="create-a-service-principal-spn"></a>建立服務主體（SPN）

若要瞭解如何建立服務主體，請參閱[使用 Azure 入口網站建立服務主體](../active-directory/develop/howto-create-service-principal-portal.md)。

### <a name="configure-permissions-in-azure-analysis-services"></a>在 Azure Analysis Services 中設定許可權
 
您所建立的服務主體必須具有伺服器的伺服器管理員許可權。 若要深入了解，請參閱[將服務主體新增至伺服器管理員角色](analysis-services-addservprinc-admins.md)。

## <a name="design-the-azure-automation-runbook"></a>設計 Azure 自動化 Runbook

1. 在自動化帳戶中，建立將用來安全地儲存服務主體的**認證**資源。

    ![建立認證](./media/analysis-services-refresh-azure-automation/6.png)

2. 輸入認證的詳細資料。  針對 [**使用者名稱**]，輸入**spn ClientId**，針對 [**密碼**] 輸入**spn 密碼**。

    ![建立認證](./media/analysis-services-refresh-azure-automation/7.png)

3. 匯入自動化 Runbook

    ![Import Runbook](./media/analysis-services-refresh-azure-automation/8.png)

4. 流覽**Refresh-Model**檔案、提供**名稱**和**描述**，然後按一下 [**建立**]。

    ![Import Runbook](./media/analysis-services-refresh-azure-automation/9.png)

5. 建立 Runbook 之後，它會自動進入編輯模式。  選取 [發佈]  。

    ![發佈 Runbook](./media/analysis-services-refresh-azure-automation/10.png)

    > [!NOTE]
    > 先前建立的認證資源是由 runbook 藉由使用 AutomationPSCredential 命令來**取得**。  此命令接著會傳遞至**ProcessASADatabase** PowerShell 命令，以對 Azure Analysis Services 執行驗證。

6. 按一下 [**啟動**] 來測試 runbook。

    ![啟動 Runbook](./media/analysis-services-refresh-azure-automation/11.png)

7. 填寫 [ **DATABASENAME**]、[ **ANALYSISSERVER**] 和 [ **REFRESHTYPE** ] 參數，然後按一下 **[確定]**。 手動執行 Runbook 時，不需要**WEBHOOKDATA**參數。

    ![啟動 Runbook](./media/analysis-services-refresh-azure-automation/12.png)

如果 Runbook 執行成功，您將會收到如下所示的輸出：

![成功執行](./media/analysis-services-refresh-azure-automation/13.png)

## <a name="use-a-self-contained-azure-automation-runbook"></a>使用獨立的 Azure 自動化 Runbook

Runbook 可以設定為根據排程觸發 Azure Analysis Services 模型重新整理。

這可設定如下：

1. 在自動化 Runbook 中 **，按一下 [** 排程]，然後按一下 [**新增排程**]。
 
    ![建立排程](./media/analysis-services-refresh-azure-automation/14.png)

2. 按一下 [**排程** > ] [**建立新的排程**]，然後填入詳細資料。

    ![設定排程](./media/analysis-services-refresh-azure-automation/15.png)

3. 按一下 [建立]  。

4. 填入排程的參數。 這會在每次 Runbook 觸發時使用。 透過排程執行時， **WEBHOOKDATA**參數應保留空白。

    ![設定參數](./media/analysis-services-refresh-azure-automation/16.png)

5. 按一下 [確定]  。

## <a name="consume-with-data-factory"></a>使用 Data Factory

若要使用 Azure Data Factory 來取用 runbook，請先建立 runbook 的**Webhook** 。 **Webhook**會提供可透過 Azure Data Factory web 活動呼叫的 URL。

> [!IMPORTANT]
> 若要建立**Webhook**，Runbook 的狀態必須是 [**已發佈**]。

1. 在您的自動化 Runbook 中，按一下 [ **webhook**]，然後按一下 [**新增 Webhook**]。

   ![新增 Webhook](./media/analysis-services-refresh-azure-automation/17.png)

2. 提供 Webhook 的名稱和到期日。  此名稱只會識別自動化 Runbook 內的 Webhook，而不會形成 URL 的一部分。

   >[!CAUTION]
   >請確定您在關閉嚮導之前複製 URL，因為您無法在關閉後重新取得。
    
   ![設定 Webhook](./media/analysis-services-refresh-azure-automation/18.png)

    Webhook 的參數可以保留空白。  設定 Azure Data Factory web 活動時，可以將參數傳遞至 web 呼叫的主體。

3. 在 Data Factory 中，設定**web 活動**

### <a name="example"></a>範例

   ![範例 Web 活動](./media/analysis-services-refresh-azure-automation/19.png)

**Url**是從 Webhook 建立的 url。

本文**是一**份 JSON 檔，其中應包含下列屬性：


|屬性  |值  |
|---------|---------|
|**AnalysisServicesDatabase**     |Azure Analysis Services 資料庫的名稱 <br/> 範例： AdventureWorksDB         |
|**AnalysisServicesServer**     |Azure Analysis Services 伺服器名稱。 <br/> 範例： HTTPs：\//westus.asazure.windows.net/servers/myserver/models/AdventureWorks/         |
|**DatabaseRefreshType**     |要執行的重新整理類型。 <br/> 範例： Full         |

範例 JSON 主體：

```json
{
    "AnalysisServicesDatabaseName": "AdventureWorksDB",
    "AnalysisServicesServer": "asazure://westeurope.asazure.windows.net/MyAnalysisServer",
    "DatabaseRefreshType": "Full"
}
```

這些參數是在 runbook PowerShell 腳本中定義。  執行 web 活動時，傳遞的 JSON 承載是 WEBHOOKDATA。

這會還原序列化並儲存為 PowerShell 參數，然後由 ProcesASDatabase PowerShell 命令使用。

![已還原序列化的 Webhook](./media/analysis-services-refresh-azure-automation/20.png)

## <a name="use-a-hybrid-worker-with-azure-analysis-services"></a>搭配 Azure Analysis Services 使用混合式背景工作角色

具有靜態公用 IP 位址的 Azure 虛擬機器可用來做為 Azure 自動化混合式背景工作角色。  然後，可以將此公用 IP 位址新增至 Azure Analysis Services 防火牆。

> [!IMPORTANT]
> 確定虛擬機器公用 IP 位址已設定為 [靜態]。
>
>若要深入瞭解如何設定 Azure 自動化混合式背景工作角色，請參閱[混合式 Runbook 背景工作安裝](../automation/automation-hybrid-runbook-worker.md#hybrid-runbook-worker-installation)。

設定混合式背景工作角色之後，請[依照使用與 Data Factory](#consume-with-data-factory)一節中所述的方式建立 Webhook。  這裡唯一的差異是在設定 Webhook 時，選取 [在混合式背景 > **工作角色****上執行**] 選項。

使用混合式背景工作角色的範例 webhook：

![範例混合式背景工作角色 Webhook](./media/analysis-services-refresh-azure-automation/21.png)

## <a name="sample-powershell-runbook"></a>PowerShell Runbook 範例

下列程式碼片段是如何使用 PowerShell Runbook 執行 Azure Analysis Services 模型重新整理的範例。

```powershell
param
(
    [Parameter (Mandatory = $false)]
    [object] $WebhookData,

    [Parameter (Mandatory = $false)]
    [String] $DatabaseName,
    [Parameter (Mandatory = $false)]
    [String] $AnalysisServer,
    [Parameter (Mandatory = $false)]
    [String] $RefreshType
)

$_Credential = Get-AutomationPSCredential -Name "ServicePrincipal"

# If runbook was called from Webhook, WebhookData will not be null.
if ($WebhookData)
{ 
    # Retrieve AAS details from Webhook request body
    $atmParameters = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)
    Write-Output "CredentialName: $($atmParameters.CredentialName)"
    Write-Output "AnalysisServicesDatabaseName: $($atmParameters.AnalysisServicesDatabaseName)"
    Write-Output "AnalysisServicesServer: $($atmParameters.AnalysisServicesServer)"
    Write-Output "DatabaseRefreshType: $($atmParameters.DatabaseRefreshType)"
    
    $_databaseName = $atmParameters.AnalysisServicesDatabaseName
    $_analysisServer = $atmParameters.AnalysisServicesServer
    $_refreshType = $atmParameters.DatabaseRefreshType
 
    Invoke-ProcessASDatabase -DatabaseName $_databaseName -RefreshType $_refreshType -Server $_analysisServer -ServicePrincipal -Credential $_credential
}
else 
{
    Invoke-ProcessASDatabase -DatabaseName $DatabaseName -RefreshType $RefreshType -Server $AnalysisServer -ServicePrincipal -Credential $_Credential
}
```


## <a name="next-steps"></a>後續步驟

[範例](analysis-services-samples.md)  
[REST API](https://docs.microsoft.com/rest/api/analysisservices/servers)
