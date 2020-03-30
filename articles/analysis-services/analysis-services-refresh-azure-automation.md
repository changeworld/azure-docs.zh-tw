---
title: 使用 Azure 自動化刷新 Azure 分析服務模型 |微軟文檔
description: 本文介紹如何使用 Azure 自動化為 Azure 分析服務編寫模型刷新代碼。
author: chrislound
ms.service: analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: chlound
ms.openlocfilehash: a79123d57f80474e1871ef68f9a92ea9417089ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73572361"
---
# <a name="refresh-with-azure-automation"></a>使用 Azure 自動化重新整理

通過使用 Azure 自動化和 PowerShell Runbook，可以在 Azure 分析方格模型上執行自動資料刷新操作。  

本文中的示例使用[PowerShell SqlServer 模組](https://docs.microsoft.com/powershell/module/sqlserver/?view=sqlserver-ps)。

本文稍後將提供一個示例 PowerShell Runbook，該功能演示了刷新模型。  

## <a name="authentication"></a>驗證

所有調用都必須使用有效的 Azure 活動目錄 （OAuth 2） 權杖進行身份驗證。  本文中的示例將使用服務主體 （SPN） 對 Azure 分析服務進行身份驗證。

要瞭解有關創建服務主體的更多內容，請參閱[使用 Azure 門戶創建服務主體](../active-directory/develop/howto-create-service-principal-portal.md)。

## <a name="prerequisites"></a>Prerequisites

> [!IMPORTANT]
> 以下示例假定 Azure 分析服務防火牆已禁用。 如果啟用了防火牆，則請求開始器的公共 IP 位址需要在防火牆中白名單。

### <a name="install-sqlserver-modules-from-powershell-gallery"></a>從 PowerShell 庫中安裝 SqlServer 模組。

1. 在 Azure 自動化帳戶中，按一下 **"模組**"，然後按一下 **"流覽庫**"。

2. 在搜索欄中，搜索**SqlServer**。

    ![搜索模組](./media/analysis-services-refresh-azure-automation/1.png)

3. 選擇 SqlServer，然後按一下 **"導入**"。
 
    ![導入模組](./media/analysis-services-refresh-azure-automation/2.png)

4. 按一下 [確定]****。
 
### <a name="create-a-service-principal-spn"></a>創建服務主體 （SPN）

要瞭解如何創建服務主體，請參閱使用[Azure 門戶創建服務主體](../active-directory/develop/howto-create-service-principal-portal.md)。

### <a name="configure-permissions-in-azure-analysis-services"></a>在 Azure 分析服務中配置許可權
 
您創建的服務主體必須具有伺服器上的伺服器管理員許可權。 若要深入了解，請參閱[將服務主體新增至伺服器管理員角色](analysis-services-addservprinc-admins.md)。

## <a name="design-the-azure-automation-runbook"></a>設計 Azure 自動化運行簿

1. 在自動化帳戶中，創建一個**憑據**資源，用於安全地存儲服務主體。

    ![建立認證](./media/analysis-services-refresh-azure-automation/6.png)

2. 輸入憑據的詳細資訊。  對於**使用者名**，輸入**SPN 用戶端Id**，對於**密碼**，輸入**SPN 金鑰**。

    ![建立認證](./media/analysis-services-refresh-azure-automation/7.png)

3. 導入自動化運行簿

    ![Import Runbook](./media/analysis-services-refresh-azure-automation/8.png)

4. 流覽**刷新模型.ps1**檔，提供**名稱**和**說明**，然後按一下"**創建**"。

    ![Import Runbook](./media/analysis-services-refresh-azure-automation/9.png)

5. 創建 Runbook 後，它將自動進入編輯模式。  選取 [發行]****。

    ![發佈 Runbook](./media/analysis-services-refresh-azure-automation/10.png)

    > [!NOTE]
    > 以前創建的憑據資源由 Runbook 使用**獲取自動化PS憑據**命令檢索。  然後，此命令將傳遞給**調用進程資料庫**PowerShell 命令，以執行對 Azure 分析服務的身份驗證。

6. 按一下"**開始"** 測試 Runbook。

    ![啟動 Runbook](./media/analysis-services-refresh-azure-automation/11.png)

7. 填寫**資料庫名稱**、**分析伺服器**和**REFRESHTYPE**參數，然後按一下"**確定**"。 手動運行 Runbook 時，不需要**WEBHOOKDATA**參數。

    ![啟動 Runbook](./media/analysis-services-refresh-azure-automation/12.png)

如果 Runbook 成功執行，您將收到如下所示的輸出：

![成功運行](./media/analysis-services-refresh-azure-automation/13.png)

## <a name="use-a-self-contained-azure-automation-runbook"></a>使用自包含的 Azure 自動化 Runbook

Runbook 可以配置為按計劃觸發 Azure 分析服務模型刷新。

這可以配置如下：

1. 在"自動化 Runbook"中，按一下 **"計畫**"，然後**添加計畫**。
 
    ![創建計畫](./media/analysis-services-refresh-azure-automation/14.png)

2. 按一下 **"計畫** > **創建新計畫**"，然後填寫詳細資訊。

    ![配置計畫](./media/analysis-services-refresh-azure-automation/15.png)

3. 按一下 **[建立]**。

4. 填寫計畫的參數。 每次 Runbook 觸發時都將使用這些功能。 通過計畫運行時 **，WEBHOOKDATA**參數應留空。

    ![設定參數](./media/analysis-services-refresh-azure-automation/16.png)

5. 按一下 [確定]****。

## <a name="consume-with-data-factory"></a>使用資料工廠

要使用 Azure 資料工廠使用 Runbook，請先為 Runbook 創建**Webhook。** **Webhook**將提供一個 URL，可通過 Azure 資料工廠 Web 活動調用該 URL。

> [!IMPORTANT]
> 要創建**Webhook，** 必須**發佈**Runbook 的狀態。

1. 在自動化 Runbook 中，按一下 **"網頁掛鉤**"，然後按一下"**添加網頁鉤**"。

   ![添加網鉤](./media/analysis-services-refresh-azure-automation/17.png)

2. 為 Webhook 指定名稱和過期。  名稱僅標識自動化 Runbook 中的 Webhook，它不構成 URL 的一部分。

   >[!CAUTION]
   >請確保在關閉嚮導之前複製 URL，因為關閉後無法將其找回。
    
   ![配置 Webhook](./media/analysis-services-refresh-azure-automation/18.png)

    網鉤的參數可以保持空。  配置 Azure 資料工廠 Web 活動時，參數可以傳遞到 Web 調用的主體中。

3. 在資料工廠中，配置**Web 活動**

### <a name="example"></a>範例

   ![示例 Web 活動](./media/analysis-services-refresh-azure-automation/19.png)

**URL**是從 Webhook 創建的 URL。

**正文**是 JSON 文檔，應包含以下屬性：


|屬性  |值  |
|---------|---------|
|**分析服務資料庫**     |Azure 分析服務資料庫的名稱 <br/> 示例：冒險工廠DB         |
|**分析服務伺服器**     |Azure 分析服務伺服器名稱。 <br/> 示例： HTTPs：\//westus.asazure.windows.net/servers/myserver/models/AdventureWorks/         |
|**資料庫刷新類型**     |要執行的刷新類型。 <br/> 示例： 已滿         |

JSON 正文示例：

```json
{
    "AnalysisServicesDatabaseName": "AdventureWorksDB",
    "AnalysisServicesServer": "asazure://westeurope.asazure.windows.net/MyAnalysisServer",
    "DatabaseRefreshType": "Full"
}
```

這些參數在 runbook PowerShell 腳本中定義。  執行 Web 活動時，傳遞的 JSON 有效負載為 WEBHOOKDATA。

這將反序列化並存儲為 PowerShell 參數，然後由 Invoke-ProcesAS 資料庫 PowerShell 命令使用。

![反序列化網路鉤子](./media/analysis-services-refresh-azure-automation/20.png)

## <a name="use-a-hybrid-worker-with-azure-analysis-services"></a>將混合輔助角色與 Azure 分析服務一起使用

具有靜態公共 IP 位址的 Azure 虛擬機器可用作 Azure 自動化混合工作執行緒。  然後，可以將此公共 IP 位址添加到 Azure 分析服務防火牆。

> [!IMPORTANT]
> 確保虛擬機器公共 IP 位址配置為靜態。
>
>要瞭解有關配置 Azure 自動化混合工作執行緒的更多資訊，請參閱[使用混合 Runbook 輔助角色在資料中心或雲中自動執行資源](../automation/automation-hybrid-runbook-worker.md#install-a-hybrid-runbook-worker)。

配置混合輔助角色後，按照"[使用資料工廠"](#consume-with-data-factory)一節中所述創建 Webhook。  此處的唯一區別是在配置 Webhook 時選擇"**在混合輔助角色****上運行** > "選項。

使用混合輔助角色的示例 Webhook：

![混合工作執行緒 Webhook 示例](./media/analysis-services-refresh-azure-automation/21.png)

## <a name="sample-powershell-runbook"></a>示例電源殼運行簿

以下程式碼片段是如何使用 PowerShell Runbook 執行 Azure 分析服務模型刷新的示例。

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

[樣品](analysis-services-samples.md)  
[REST API](https://docs.microsoft.com/rest/api/analysisservices/servers)
