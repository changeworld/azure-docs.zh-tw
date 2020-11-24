---
title: 淘汰 Azure 監視器計量和自動調整的部署 Api
description: 計量與自動調整傳統 API 即將停用，此 API 亦稱為「Azure 服務管理」(ASM) 或 RDFE 部署模型
ms.subservice: ''
ms.topic: conceptual
ms.date: 11/19/2018
ms.openlocfilehash: a8900d238e7542864e3dc53f9d192e579e2c96d1
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "95536688"
---
# <a name="azure-monitor-retirement-of-classic-deployment-model-apis-for-metrics-and-autoscale"></a>Azure 監視器停用計量與自動調整的傳統部署模型 API

Azure 監視器 (首次發行時名為 Azure Insights) 目前的工可建立與管理自動調整設定，並可從傳統虛擬機器與傳統雲端服務取用計量。 所有區域中的所有 Azure 公用雲端與私人雲端，將在 **2019 年 6 月 30 日後停用** 以傳統部署模型為基礎的原始 API 集。   

以 Azure Resource Manager 為基礎的 API 集已經支援相同的作業超過一年時間。 Azure 入口網站會在自動調整和計量上使用新的 REST API。 此外也會提供以這些 Resource Manager API 為基礎的新 SDK、PowerShell 和 CLI。 我們合作夥伴的監視服務會取用 Azure 監視器中以新 Resource Manager 為基礎的 REST API。  

## <a name="who-is-not-affected"></a>不會受影響的對象

如果您是透過 Azure 入口網站、[新 Azure 監視器 SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/)、PowerShell、CLI 或 Resource Manager 範本來管理自動調整，則不需要採取任何動作。  

如果您是透過 Azure 入口網站或透過各種[監視合作夥伴服務](./partners.md)取用計量，則不需要採取任何動作。 Microsoft 正在與監視合作夥伴合作，移轉至新 API。

## <a name="who-is-affected"></a>會受影響的對象

如果您使用的是下列元件，這篇文章適合給您閱讀：

- **傳統 Azure Insights SDK** - 如果您使用的是 [傳統 Azure Insights SDK](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/)，請改為使用 [.NET](https://github.com/azure/azure-libraries-for-net#download) 或 [JAVA](https://github.com/azure/azure-libraries-for-java#download) 適用的新 Azure 監視器 SDK。 下載 [Azure 監視器 SDK NuGet 封裝](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/)。

- **傳統自動調整** - 如果您是使用自訂的工具或 [傳統 Azure Insights SDK 來](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/)呼叫 [傳統自動調整設定 API](/previous-versions/azure/reference/mt348562(v=azure.100))，請改用 [Resource Manager Azure 監視器 REST API](/rest/api/monitor/autoscalesettings)。

- **傳統計量** - 如果您是透過自訂的工具使用 [傳統 REST API](/previous-versions/azure/reference/dn510374(v=azure.100)) 或 [傳統 Azure Insights SDK](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/) 來取用計量，請改用 [Resource Manager Azure 監視器 REST API](/rest/api/monitor/autoscalesettings)。 

如果您不確定您的程式碼或自訂工具是否會呼叫傳統 API，請參閱下方：

- 檢視您程式碼或工具中所參考的 URI。 傳統 API 會使用 URI https://management.core.windows.net。 請從 `https://management.azure.com/` 開始，在以 Resource Manager 為基礎的 API 上使用新 URI。

- 比較您電腦上的組件名稱。 舊的傳統組件位於 https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/。

- 如果您使用憑證驗證來存取計量或自動調整 API，則您使用的是傳統端點和程式庫。 新的 Resource Manager API 需要透過服務主體或使用者主體使用 Azure Active Directory。

- 如果您使用任何下列連結文件中所參考的呼叫，則您使用的是舊版傳統 API。

  - [Windows.Azure.Management.Monitoring Class Library](/previous-versions/azure/dn510414(v=azure.100))

  - [Monitoring (classic) .NET](/previous-versions/azure/reference/mt348562(v%3dazure.100))

  - [IMetricOperations 介面](/previous-versions/azure/reference/dn802395(v%3dazure.100))

## <a name="why-you-should-switch"></a>需要切換的原因

自動調整和計量的所有現有功能會透過新的 API 繼續運作。  

遷移至較新的 Api 包含以 Resource Manager 為基礎的功能，例如支援一致的 Azure 角色型存取控制 (Azure RBAC) 所有的監視服務。 您也會得到其他的計量功能： 

- 支援維度
- 所有服務之間有一致的 1 分鐘計量細微性 
- 更好的查詢功能
- 較高的資料保留期 (93 天的計量和30天)  

整體而言，Resource Manager 型的 Azure 監視器 API 與 Azure 中所有其他的服務一樣，效能、延展性及可靠性都更好。 

## <a name="what-happens-if-you-do-not-migrate"></a>未移轉可能發生的情況

### <a name="before-retirement"></a>停用前

對您的 Azure 服務或其工作負載不會有任何直接影響。  

### <a name="after-retirement"></a>停用後

任何對先前所列傳統 API 的呼叫將會失敗，並傳回如下的錯誤訊息：

自動調整： *此 API 已被取代。使用 Azure 入口網站、Azure 監視器 SDK、PowerShell、CLI 或 Resource Manager 範本來管理自動調整設定*。  

針對度量： *這個 API 已被取代。使用 Azure 入口網站、Azure 監視器 SDK、PowerShell、CLI 來查詢計量*。

## <a name="email-notifications"></a>電子郵件通知

停用通知已傳送到下列帳戶角色的電子郵件地址： 

- 帳戶與服務管理員
- 共同管理員  

如有任何疑問，請與我們連絡：MonitorClassicAPIhelp@microsoft.com。  

## <a name="references"></a>參考

- [Azure 監視器的新 REST API](/rest/api/monitor/) (英文) 
- [新 Azure 監視器 SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/) (英文)

