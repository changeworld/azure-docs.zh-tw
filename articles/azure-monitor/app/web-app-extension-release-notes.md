---
title: Azure web 應用程式擴充功能的版本資訊-Application Insights
description: 使用 Application Insights 的適用于執行時間檢測的 Azure Web Apps 擴充功能版本資訊。
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 06/26/2020
ms.openlocfilehash: b9da3b0647d5abe60dd8f6cb21163ff21f07205b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86539715"
---
# <a name="release-notes-for-azure-web-app-extension-for-application-insights"></a>適用于 Application Insights 的 Azure Web 應用程式擴充功能版本資訊

本文包含適用于執行時間檢測搭配 Application Insights 的 Azure Web Apps 擴充功能版本資訊。 這僅適用于預先安裝的擴充功能。

[深入瞭解](azure-web-apps.md) 適用于 Application Insights 的 Azure Web 應用程式擴充功能。

## <a name="frequently-asked-questions"></a>常見問題集

- 如何找出我目前所在的延伸模組版本為何？
    - 前往 `https://<yoursitename>.scm.azurewebsites.net/ApplicationInsights`。 如需詳細資訊，請流覽 [以擴充/代理程式為基礎的監視逐步疑難排解指南](./azure-web-apps.md?tabs=net#troubleshooting) 。

- 如果我使用的是私用延伸模組，該怎麼辦？
    - 卸載私用網站延伸模組，因為它已不再受支援。

## <a name="release-notes"></a>版本資訊

### <a name="2833"></a>2.8.33

- .NET、.NET core、JAVA 和 Node.js 代理程式，以及 Windows 擴充功能：支援主權雲端。 連接字串可以用來將資料傳送至主權雲端。

### <a name="2831"></a>2.8.31

- ASP.NET Core 代理程式：已修正與其中一個已更新 Application Insights SDK 參考相關的問題 (查看 2.8.26) 的已知問題。 如果執行時間已載入不正確的版本 `System.Diagnostics.DiagnosticSource.dll` ，無程式碼延伸模組現在將不會損毀應用程式並進行備份。 對於受此問題影響的客戶，建議 `System.Diagnostics.DiagnosticSource.dll` 您從 bin 資料夾中移除，或藉由設定 "ApplicationInsightsAgent_EXTENSIONVERSION = 2.8.24" 來使用舊版的延伸模組，否則不會啟用應用程式監視。

### <a name="2826"></a>2.8.26

- ASP.NET Core 代理程式：已修正更新 Application Insights SDK 的相關問題。 `AiHostingStartup`如果 ApplicationInsights.dll 已存在於 bin 資料夾中，則代理程式不會嘗試載入。 這會解決透過元件反映的相關問題 \<AiHostingStartup\> 。Iprovidednamespace.gettypes ( # A1。
- 已知問題： `System.IO.FileLoadException: Could not load file or assembly 'System.Diagnostics.DiagnosticSource, Version=4.0.4.0, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51'` 如果載入其他版本的 dll，則可能會擲回例外狀況 `DiagnosticSource` 。 例如，如果 `System.Diagnostics.DiagnosticSource.dll` 出現在 [發行] 資料夾中，就可能發生這種情況。 若要降低風險，請使用舊版的延伸模組，方法是在應用程式服務中設定應用程式設定： ApplicationInsightsAgent_EXTENSIONVERSION = 2.8.24。

### <a name="2824"></a>2.8.24

- 2.8.21 的重新封裝版本。

### <a name="2823"></a>2.8.23

- 已新增 ASP.NET Core 3.0 無程式碼監視支援。
- 已將 ASP.NET Core SDK 更新為適用于執行階段版本2.1、2.2 和3.0 的 [2.8.0](https://github.com/microsoft/ApplicationInsights-aspnetcore/releases/tag/2.8.0) 。 以 .NET Core 2.0 為目標的應用程式會繼續使用 SDK 2.1.1。

### <a name="2814"></a>2.8.14

- 已將 ASP.NET Core SDK 版本從2.3.0 更新至最新的 (2.6.1) ，適用于以 .NET Core 2.1、2.2 為目標的應用程式。 以 .NET Core 2.0 為目標的應用程式會繼續使用 SDK 2.1.1。

### <a name="2812"></a>2.8.12

- 支援 ASP.NET Core 2.2 應用程式。
- 修正 ASP.NET Core 延伸模組中造成 SDK 插入的 bug，即使已使用 SDK 檢測應用程式也一樣。 若為2.1 和2.2 應用程式，應用程式資料夾中的 ApplicationInsights.dll 存在，現在會導致擴充功能關閉。 針對2.0 應用程式，只有在使用呼叫來啟用 ApplicationInsights 時，才會將擴充功能備份 `UseApplicationInsights()` 。

- 針對 ASP.NET Core 應用程式的不完整 HTML 回應進行永久修正。 此修正現在已擴充為可用於 .NET Core 2.2 應用程式。

- 已新增 ASP.NET Core 應用程式 () 關閉 JavaScript 插入的支援 `APPINSIGHTS_JAVASCRIPT_ENABLED=false appsetting` 。 針對 ASP.NET core，除非明確關閉，否則 JavaScript 插入預設為「退出」模式。  (已完成預設設定以保留目前的行為。 ) 

- 修正了導致插入的 ASP.NET Core 延伸模組 bug，即使 ikey 不存在也一樣。
- 修正在遙測中造成不正確 SDK 版本的 SDK 版本首碼邏輯錯誤。

- 已新增 ASP.NET Core 應用程式的 SDK 版本首碼，以識別如何收集遙測。
- 修正了 SCM ApplicationInsights 頁面，以正確顯示預先安裝的延伸模組版本。

### <a name="2810"></a>2.8.10

- 修正 ASP.NET Core 應用程式未完成的 HTML 回應。

## <a name="next-steps"></a>接下來的步驟

- 如需有關如何設定 Azure App 服務監視的詳細資訊，請流覽 [Azure App Service 檔](azure-web-apps.md) 。 
