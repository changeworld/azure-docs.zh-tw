---
title: Azure Application Insights 中的資源、角色及存取控制 | Microsoft Docs
description: 您的組織詳細資料的擁有者、參與者及讀者。
ms.topic: conceptual
ms.date: 02/14/2019
ms.openlocfilehash: 617134c523148e713f7d34b46e0fbf6ae862591f
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2020
ms.locfileid: "77671523"
---
# <a name="resources-roles-and-access-control-in-application-insights"></a>Application Insights 中的資源、角色及存取控制

您可以使用[Microsoft Azure 中的角色型存取控制](../../role-based-access-control/role-assignments-portal.md)，控制誰具有 Azure [Application Insights][start]中資料的讀取和更新存取權。

> [!IMPORTANT]
> 指派存取權給您的應用程式資源所屬之 **資源群組或訂用帳戶** 中的使用者 - 不在資源本身。 指派 **Application Insights 元件參與者** 角色。 這可確保 Web 測試和警示以及您的應用程式資源的統一存取控制。 [詳細資訊](#access)。


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="resources-groups-and-subscriptions"></a>資源、群組和訂用帳戶

首先是一些定義：

* **資源** - Microsoft Azure 服務的執行個體。 您的 Application Insights 資源會收集、分析及顯示從您的應用程式傳送的遙測資料。  其他類型的 Azure 資源包括 Web 應用程式、資料庫和 VM。
  
    若要查看您的資源，請開啟 [ [Azure 入口網站][portal]]、[登入]，然後按一下 [所有資源]。 若要尋找的資源，請在篩選欄位中輸入名稱的一部分。
  
    ![Azure 資源清單](./media/resources-roles-access-control/10-browse.png)

<a name="resource-group"></a>

* [**資源群組**][group]-每個資源都屬於一個群組。 群組是管理相關資源的便利方式，特別是針對存取控制。 例如，您可以將 Web 應用程式、Application Insights 資源放到一個資源群組，以監視應用程式，以及放到儲存體資源以保存匯出的資料。

* [**訂用帳戶**](https://portal.azure.com) - 若要使用 Application Insights 或其他 Azure 資源，您可以登入 Azure 訂用帳戶。 每個資源群組都屬於一個 Azure 訂用帳戶，其中您選擇價格封裝，如果是組織的訂用帳戶，請選擇成員以及其存取權限。
* [**Microsoft 帳戶**][account]-您用來登入 Microsoft Azure 訂用帳戶、XBox Live、Outlook.com 和其他 Microsoft 服務的使用者名稱和密碼。

## <a name="access"></a> 控制資源群組中的存取

請務必了解除了您為應用程式建立的資源之外，還有警示和 Web 測試的個別隱藏資源。 它們會附加到與您 Application Insights 資源相同的[資源群組](#resource-group)。 您也可以在那裡放置其他 Azure 服務，例如網站或儲存體。

為了控制這些資源的存取，因此建議您：

* 在 **資源群組或訂用帳戶** 層級控制存取。
* 指派 **Application Insights 元件參與者** 角色給使用者。 這可讓他們編輯 Web 測試、警示和 Application Insights 資源，而不用提供群組中任何其他服務的存取權。

## <a name="to-provide-access-to-another-user"></a>若要提供存取權給其他使用者

您必須擁有訂用帳戶或資源群組的擁有者權限。

使用者必須擁有[Microsoft 帳戶][account]，或其[組織的 microsoft 帳戶](../../active-directory/fundamentals/sign-up-organization.md)的存取權。 您可以提供存取權給個人，也可以提供給在 Azure Active Directory 中定義的使用者群組。

#### <a name="navigate-to-resource-group-or-directly-to-the-resource-itself"></a>瀏覽至資源群組，或直接瀏覽資源本身

從左側功能表中選擇 [存取控制 (IAM)]。

![Azure 入口網站中存取控制按鈕的螢幕擷取畫面](./media/resources-roles-access-control/0001-access-control.png)

選取 [新增角色指派]

![此螢幕擷取畫面顯示以紅色醒目提示新增按鈕的存取控制功能表](./media/resources-roles-access-control/0002-add.png)

下方的 [新增權限] 檢視主要是 Application Insights 資源的特定資料，如果您從較高的層級 (例如資源群組) 檢視存取控制權限，您將會看到其他並非以 Application Insights 為主的角色。

若要檢視所有 Azure 角色型存取控制內建角色的資訊，請使用[官方參考內容](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)。

![存取控制使用者角色清單的螢幕擷取畫面](./media/resources-roles-access-control/0003-user-roles.png)

#### <a name="select-a-role"></a>選取角色

我們可連結至相關聯的官方參考文件 (如果有的話)。

| 角色 | 在資源群組中 |
| --- | --- |
| [擁有者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) |可以變更任何項目，包括使用者存取。 |
| [參與者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) |可以編輯任何項目，包括所有資源。 |
| [Application Insights 元件參與者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#application-insights-component-contributor) |可以編輯 Application Insights 資源、Web 測試和警示。 |
| [讀取者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#reader) |可以檢視 (但無法變更) 任何項目。 |
| [Application Insights 快照集偵錯工具](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#application-insights-snapshot-debugger) | 賦予使用者使用「Application Insights 快照集偵錯工具」功能的權限。 請注意，擁有者或參與者角色均不包含這個角色。 |
| Azure 服務部署發行管理參與者 | 透過「Azure 服務部署」進行部署的服務參與者角色。 |
| [資料清除者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#data-purger) | 可清除個人資料的特殊角色。 如需詳細資訊，請參閱我們的[個人資料指引](https://docs.microsoft.com/azure/application-insights/app-insights-customer-data)。   |
| ExpressRoute 管理員 | 可建立、刪除及管理 Express Route。|
| [Log Analytics 參與者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#log-analytics-contributor) | 「Log Analytics 參與者」角色可以讀取所有監視資料和編輯監視設定。 編輯監視設定包括將 VM 延伸模組新增至 VM、讀取儲存體帳戶金鑰以便能夠設定從「Azure 儲存體」收集記錄、建立及設定「自動化」帳戶、新增解決方案，以及設定所有 Azure 資源上的 Azure 診斷。  |
| [Log Analytics 讀者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#log-analytics-reader) | 「Log Analytics 讀者」可以檢視和搜尋所有監視資料，以及檢視監視設定，包括檢視所有 Azure 資源上的 Azure 診斷設定。 |
| masterreader | 可讓使用者檢視所有項目，但是無法進行變更。 |
| [監視參與者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) | 可讀取所有監視資料及更新監視設定。 |
| [監視計量發行者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-metrics-publisher) | 針對 Azure 資源啟用發佈計量。 |
| [監視讀取器](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) | 可讀取所有監視資料。 |
| 資源原則參與者 (預覽) | 從 EA 回填的使用者，有權建立/修改資源原則、建立支援票證及讀取資源/階層。  |
| [使用者存取系統管理員](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#user-access-administrator) | 可讓使用者管理其他使用者對 Azure 資源的存取。|
| [網站參與者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#website-contributor) | 可讓您管理網站 (非 Web 方案)，但無法存取它們。|

「編輯」包括建立、刪除及更新：

* 資源
* Web 測試
* 警示
* 連續匯出

#### <a name="select-the-user"></a>選取使用者

如果您想要的使用者不在目錄中，您可以邀請任何具有 Microsoft 帳戶的使用者。
(如果他們使用 Outlook.com、OneDrive、Windows Phone 或 XBox Live 等服務，他們就會有 Microsoft 帳戶。)

## <a name="related-content"></a>相關內容

* [Azure 中的角色型存取控制](../../role-based-access-control/role-assignments-portal.md)

## <a name="powershell-query-to-determine-role-membership"></a>用於判斷角色成員資格的 PowerShell 查詢

因為特定角色可連結到通知與電子郵件警示，能夠產生屬於給定角色的使用者清單很實用。 為協助產生這些類型的清單，我們提供下列範例查詢供您依據您的特定需求來調整：

### <a name="query-entire-subscription-for-admin-roles--contributor-roles"></a>查詢整個訂用帳戶以尋找系統管理員角色 + 參與者角色

```powershell
(Get-AzRoleAssignment -IncludeClassicAdministrators | Where-Object {$_.RoleDefinitionName -in @('ServiceAdministrator', 'CoAdministrator', 'Owner', 'Contributor') } | Select -ExpandProperty SignInName | Sort-Object -Unique) -Join ", "
```

### <a name="query-within-the-context-of-a-specific-application-insights-resource-for-owners-and-contributors"></a>在特定 Application Insights 資源的範圍中查詢，以尋找擁有者與參與者

```powershell
$resourceGroup = "RGNAME"
$resourceName = "AppInsightsName"
$resourceType = "microsoft.insights/components"
(Get-AzRoleAssignment -ResourceGroup $resourceGroup -ResourceType $resourceType -ResourceName $resourceName | Where-Object {$_.RoleDefinitionName -in @('Owner', 'Contributor') } | Select -ExpandProperty SignInName | Sort-Object -Unique) -Join ", "
```

### <a name="query-within-the-context-of-a-specific-resource-group-for-owners-and-contributors"></a>在特定資源群組的範圍中查詢，以尋找擁有者與參與者

```powershell
$resourceGroup = "RGNAME"
(Get-AzRoleAssignment -ResourceGroup $resourceGroup | Where-Object {$_.RoleDefinitionName -in @('Owner', 'Contributor') } | Select -ExpandProperty SignInName | Sort-Object -Unique) -Join ", "
```

<!--Link references-->

[account]: https://account.microsoft.com
[group]: ../../azure-resource-manager/management/overview.md
[portal]: https://portal.azure.com/
[start]: ../../azure-monitor/app/app-insights-overview.md
