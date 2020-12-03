---
title: '使用 PowerShell 來註冊和掃描 Power BI (預覽) '
description: 瞭解如何使用 PowerShell 在 Azure 範疇中註冊和掃描 Power BI 租使用者。
author: darrenparker
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/12/2020
ms.openlocfilehash: f0b541baf49307006c18f07d92bd409763a29e3a
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2020
ms.locfileid: "96551900"
---
# <a name="use-powershell-to-register-and-scan-power-bi-in-azure-purview-preview"></a>使用 PowerShell 在 Azure 範疇 (preview 中註冊和掃描 Power BI)  

本文說明如何使用 PowerShell 在 Azure 範疇目錄中設定 Power BI 租使用者的掃描。

## <a name="power-bi-authentication-background"></a>Power BI authentication 背景

範疇類別目錄必須連線至 Power BI 系統管理員 API，才能掃描 Power BI 租使用者中的構件。 Power BI 管理 API 目前支援兩種類型的驗證：

- 受控識別 (MSI) 。
- 委派的使用者驗證。

> [!Note]
> 除非需要委派的使用者驗證，否則建議使用 MSI。

## <a name="create-a-security-group"></a>建立安全性群組

每個範疇目錄都有自己的系統指派受控識別，必須獲得 Power BI 租使用者的存取權，才可啟用掃描。 目錄名稱可以用來在 Azure 入口網站上尋找其身分識別。

1. 在 [Azure 入口網站](https://portal.azure.com)中，搜尋 Azure Active Directory。
1. 在您的 Azure Active Directory 中建立新的安全性群組，方法如下： [建立基本的群組，並使用 Azure Active Directory 來新增成員](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal)。

    > [!Tip]
    > 如果您已經有要使用的安全性群組，則可以略過此步驟。

1. 請務必選取 [安全性] 作為 **群組類型**。

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/security-group.png" alt-text="安全性群組類型":::

1. 依序選取 [成員] 和 [ **新增成員**]，將目錄的受控識別新增至此安全性群組。

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-group-member.png" alt-text="將目錄的受控實例新增至群組":::

1. 搜尋您的目錄，然後選取它。

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-catalog-to-group-by-search.png" alt-text="藉由搜尋來新增類別目錄":::

1. 您應該會看到成功通知，告訴您已新增它。

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/success-add-catalog-msi.png" alt-text="新增類別目錄 MSI 成功":::

## <a name="associate-the-security-group-with-power-bi"></a>將安全性群組與 Power BI 建立關聯

1. 登入 [Power BI 系統管理員入口網站](https://app.powerbi.com/admin-portal/tenantSettings?allowServicePrincipalsUseReadAdminAPIsUI=1)。 附加此功能旗標：  `allowServicePrincipalsUseReadAdminAPIsUI=1` 。 此旗標可讓您建立安全性群組的關聯功能。 例如

    ```http
    https://app.powerbi.com/admin-portal/tenantSettings?allowServicePrincipalsUseReadAdminAPIsUI=1`
    ```

    > [!Important]
    > 您必須是 Power BI 系統管理員，才能看到 [租使用者設定] 頁面。

1. 選取 [**開發人員設定**]  >  **可讓服務主體使用唯讀 Power BI api (Preview)**。
1. 選取 **特定的安全性群組**。

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/allow-service-principals-power-bi-admin.png" alt-text="顯示如何允許服務主體取得唯讀 Power BI 管理員 API 許可權的影像 ":::

    > [!Caution]
    > 當您允許您建立的安全性群組 (以您的資料目錄受控識別作為成員) 以使用唯讀 Power BI 系統管理員 Api 時，您也會允許它存取此租使用者中所有 ) 成品的中繼資料 (例如儀表板和報表名稱、擁有者、描述等 Power BI。 一旦將中繼資料提取到 Azure 範疇中，範疇的許可權，不 Power BI 許可權，就可以決定誰可以看到該中繼資料。

    > [!Note]
    > 您可以從開發人員設定中移除安全性群組，但先前解壓縮的中繼資料不會從範疇帳戶中移除。 您可以另外刪除它。

## <a name="register-power-bi-and-set-up-a-scan"></a>註冊 Power BI 並設定掃描

既然您已將目錄許可權提供給 Power BI 租使用者的系統管理員 API，您必須在目錄中設定您的掃描。 若要這樣做，請設定並執行 PowerShell 腳本。

1. 下載並解壓縮 ADC PowerShell Cmdlet。
1. 在腳本頂端提供指派值，以設定您的腳本。

    ```PowerShell
    #Purview Account Info
    $azuretenantId = '<Tenant Id>'
    $azuresubscriptionId = '<Tenant Id>'
    $azureResourceGroupName = '<Resource Group Name>'
    $azurePurviewAccountName = '<Catalog Name>'
    $createCatalog = $false #Change to true if you need a new catalog to be created
    $azureCatalogLocation = 'East Us' #The region of your account
    $dataSourceName = 'pbi-msi-datasource01' #provide a unique data source name
    $dataScanName = 'scan01' #provide a unique scan name

    #PowerShell Command Module Path
    $ModulePath = '<Full path to where you extracted the PS zip files>\Microsoft.DataCatalog.Management.Commands.dll'
    Import-Module $ModulePath

    If ($createCatalog -eq $true)
    {
      Connect-AzAccount -Tenant $azuretenantId -SubscriptionId $azuresubscriptionId
      Set-AzDataCatalog -ResourceGroupName $azureResourceGroupName -Name $azurePurviewAccountName -Location $azureCatalogLocation
    }

    Set-AzDataCatalogSessionSettings -DataCatalogSession -UserAuthentication -TenantId $azuretenantId  -DataCatalogAccountName $azurePurviewAccountName

    Set-AzDataCatalogDataSource -Name $dataSourceName -AccountType PowerBI  -Tenant $azuretenantId

    Set-AzDataCatalogScan -DataSourceName $dataSourceName -ScanName $dataScanName -PowerBIManagedInstanceMsi
    ```

    > [!Note]
    > 您必須是用來執行命令之訂用帳戶的參與者或擁有者。

1. 執行下列腳本來開始掃描：

    ```PowerShell
    Start-AzDataCatalogScan -DataSourceName $dataSourceName -Name $dataScanName -AsJob
    ```

## <a name="register-and-scan-power-bi"></a>註冊並掃描 Power BI

建議的驗證方法是 MSI。 不過，若要掃描與您的目錄不同的 Azure 租使用者中的 Power BI 租使用者，您可以使用委派的驗證。

若要進行委派的驗證，您必須擁有系統管理員使用者認證，以及 Power BI 系統管理員認證。 您也必須建立 Azure 應用程式，並將其授與 Power BI ReadAll 許可權。

1. 流覽至 [Azure 入口網站](https://portal.azure.com) 並搜尋 **應用程式註冊**。

1. 從 **應用程式註冊** 選取 [ **+ 新增註冊**]。

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/new-app-registration.png" alt-text="顯示如何建立新的 Azure 應用程式註冊的影像":::

1. 輸入應用程式的名稱。

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/register-new-app.png" alt-text="註冊新的應用程式":::

1. 一旦建立您的應用程式，請選取 [ **API 許可權**]，然後再按 [ **新增許可權**]。

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/app-permissions.png" alt-text="顯示如何將許可權新增至應用程式的影像":::

1. 選取 [**要求 API 許可權** **Power BI 服務**]。

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/select-power-bi-service.png" alt-text="顯示如何選取 PBI 服務的影像":::

1. 選取 [ **委派的許可權** ] 和 [ **租** 使用者]。 然後選取 [新增權限]。

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/request-api-permissions.png" alt-text="顯示如何要求 API 許可權的影像":::

1. 選取 [授與管理員同意]。

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/grant-admin-perms.png" alt-text="顯示如何授與系統管理員同意的影像":::

1. 將 **應用程式 (用戶端) 識別碼** 和 **目錄 (租使用者) 識別碼** 值。  當您設定掃描時，將會使用這些值。

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/copy-client-and-tenantid.png" alt-text="顯示覆制用戶端和租使用者識別碼的影像":::

1. 在 PowerShell 中設定您的掃描。 腳本會提示您輸入認證。 您必須至少擁有您要使用之 Azure 訂用帳戶的「參與者」角色和「範疇資料來源管理員」角色。

    ```PowerShell
    # Purview Account Info
    $azuretenantId = '<Tenant Id>'
    $azuresubscriptionId = '<Tenant Id>'
    $azureResourceGroupName = '<Resource Group Name>'
    $azurePurviewAccountName = '<Catalog Name>'
    $createCatalog = $false
    $azureCatalogLocation = 'East Us' #The region of your account
    $dataSourceName = 'pbi-delegated-datasource01' #provide a unique data source name
    $dataScanName = 'scan01' #provide a unique scan name

    # Power BI Tenant Info
    $powerBITenantIdToScan = '<Power BI Tenant ID copied from above steps>'
    $ServicePrincipalId = '<Client ID copied from above steps>'
    $UserName = '<Power BI Admin emil ex: admin@firsttomarket.onmicrosoft.com>'
    $Password = '<Power BI Admin Password>'

    #PowerShell Command Module Path
    $ModulePath = '<Full path to where you extracted the PS zip files>\Microsoft.DataCatalog.Management.Commands.dll'
    Import-Module $ModulePath

    #Commands To Create catalog, Create DataSource, Create Datascan, Start Scan
    If($createCatalog -eq $true)
    {
      Connect-AzAccount -Tenant $azuretenantId -SubscriptionId $azuresubscriptionId
      Set-AzDataCatalog -ResourceGroupName $azureResourceGroupName -Name $azurePurviewAccountName -Location $azureCatalogLocation
    }

    Set-AzDataCatalogSessionSettings -DataCatalogSession -UserAuthentication -TenantId $azuretenantId   -DataCatalogAccountName $azurePurviewAccountName

    Set-AzDataCatalogDataSource -Name $dataSourceName -AccountType PowerBI -Tenant $powerBITenantIdToScan

    Set-AzDataCatalogScan -DataSourceName $dataSourceName -ScanName $dataScanName -PowerBIDelegated -ServicePrincipalId $ServicePrincipalId -UserName $UserName -Password $Password
    ```

1. 執行您的掃描。

      ```PowerShell
      Start-AzDataCatalogScan -DataSourceName $dataSourceName -Name $dataScanName -AsJob
      ```

## <a name="next-steps"></a>後續步驟

若要開始使用 Azure 範疇，請參閱 [快速入門：建立 Azure 範疇帳戶](create-catalog-portal.md)。
