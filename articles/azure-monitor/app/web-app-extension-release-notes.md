---
title: Azure web 應用程式擴充功能的版本資訊-Application Insights
description: 使用 Application Insights 來發行適用于執行時間檢測的 Azure Web Apps 擴充功能的相關資訊。
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 06/26/2020
ms.openlocfilehash: b9da3b0647d5abe60dd8f6cb21163ff21f07205b
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86539715"
---
# <a name="release-notes-for-azure-web-app-extension-for-application-insights"></a>適用于 Application Insights 的 Azure Web 應用程式擴充功能版本資訊

本文包含適用于執行時間檢測 Application Insights 的 Azure Web Apps 擴充功能的版本資訊。 這僅適用于預先安裝的延伸模組。

[深入瞭解](azure-web-apps.md)適用于 Application Insights 的 Azure Web 應用程式擴充功能。

## <a name="frequently-asked-questions"></a>常見問題集

- 如何找出我目前所在的延伸模組版本？
    - 前往 `https://<yoursitename>.scm.azurewebsites.net/ApplicationInsights`。 如需詳細資訊，請流覽以[延伸模組/代理程式為基礎的監視的逐步疑難排解指南](./azure-web-apps.md?tabs=net#troubleshooting)。

- 如果我使用私用延伸模組，該怎麼做？
    - 卸載私人網站延伸模組，因為它已不再受到支援。

## <a name="release-notes"></a>版本資訊

### <a name="2833"></a>2.8.33

- .NET、.NET core、JAVA 和 Node.js 代理程式，以及 Windows 擴充功能：主權雲端的支援。 連接字串可以用來將資料傳送至主權雲端。

### <a name="2831"></a>2.8.31

- ASP.NET Core 代理程式：已修正與其中一個已更新的 Application Insights SDK 參考相關的問題（請參閱2.8.26 的已知問題）。 如果執行時間已載入不正確的版本 `System.Diagnostics.DiagnosticSource.dll` ，則無程式碼延伸模組現在不會損毀應用程式並關閉。 對於受到該問題影響的客戶，建議 `System.Diagnostics.DiagnosticSource.dll` 您從 bin 資料夾中移除，或藉由設定 "ApplicationInsightsAgent_EXTENSIONVERSION = 2.8.24" 來使用較舊版本的延伸模組，否則不會啟用應用程式監視。

### <a name="2826"></a>2.8.26

- ASP.NET Core 代理程式：已修正與已更新 Application Insights SDK 相關的問題。 `AiHostingStartup`如果 ApplicationInsights.dll 已經存在於 bin 資料夾中，代理程式將不會嘗試載入。 這會透過元件解決與反映相關的問題 \<AiHostingStartup\> 。Iprovidednamespace.gettypes （）。
- 已知問題： `System.IO.FileLoadException: Could not load file or assembly 'System.Diagnostics.DiagnosticSource, Version=4.0.4.0, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51'` 如果載入另一個版本的 dll，則可能會擲回例外狀況 `DiagnosticSource` 。 例如，如果 `System.Diagnostics.DiagnosticSource.dll` 存在於 publish 資料夾中，就會發生這種情況。 作為緩和措施，請在應用程式服務中設定應用程式設定，以使用舊版的延伸模組： ApplicationInsightsAgent_EXTENSIONVERSION = 2.8.24。

### <a name="2824"></a>2.8.24

- 2.8.21 的重新封裝版本。

### <a name="2823"></a>2.8.23

- 已新增 ASP.NET Core 3.0 無程式碼監視支援。
- 已將 ASP.NET Core SDK 更新為適用于執行階段版本2.1、2.2 和3.0 的[2.8.0](https://github.com/microsoft/ApplicationInsights-aspnetcore/releases/tag/2.8.0) 。 以 .NET Core 2.0 為目標的應用程式會繼續使用 SDK 的2.1.1。

### <a name="2814"></a>2.8.14

- 已針對以 .NET Core 2.1、2.2 為目標的應用程式，將 ASP.NET Core SDK 版本從2.3.0 更新為最新（2.6.1）。 以 .NET Core 2.0 為目標的應用程式會繼續使用 SDK 的2.1.1。

### <a name="2812"></a>2.8.12

- ASP.NET Core 2.2 應用程式的支援。
- 修正 ASP.NET Core 延伸模組中導致 SDK 插入的錯誤，即使應用程式已使用 SDK 進行檢測也一樣。 若是2.1 和2.2 應用程式，應用程式資料夾中的 ApplicationInsights.dll 存在，現在會導致延伸模組回到關閉狀態。 針對2.0 應用程式，只有在使用呼叫來啟用 ApplicationInsights 的情況下，擴充功能才會關閉 `UseApplicationInsights()` 。

- 針對 ASP.NET Core 應用程式的不完整 HTML 回應永久修正。 此修正現在已擴充為適用于 .NET Core 2.2 應用程式。

- 已新增支援以關閉 ASP.NET Core 應用程式的 JavaScript 插入功能（ `APPINSIGHTS_JAVASCRIPT_ENABLED=false appsetting` ）。 針對 ASP.NET core，除非明確關閉，否則 JavaScript 插入預設為「退出」模式。 （會執行預設設定以保留目前的行為）。

- 已修正導致插入的 ASP.NET Core 延伸模組 bug，即使 ikey 不存在也一樣。
- 已修正在遙測中導致 SDK 版本不正確的 SDK 版本首碼邏輯中的 bug。

- 已新增 ASP.NET Core 應用程式的 SDK 版本首碼，以識別遙測的收集方式。
- [已修正 SCM-ApplicationInsights] 頁面，可正確顯示預先安裝的擴充功能版本。

### <a name="2810"></a>2.8.10

- 修正 ASP.NET Core 應用程式不完整的 HTML 回應。

## <a name="next-steps"></a>後續步驟

- 如需如何設定 Azure App 服務監視的詳細資訊，請流覽[Azure App Service 檔](azure-web-apps.md)。 
