---
title: 對 IE 的 Azure 訪問面板擴展進行故障排除 |微軟文檔
description: 如何使用群組原則針對我的 app 入口網站部署 Internet Explorer 附加元件。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: mimart
ms.reviewer: asteen
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: a0269c87572e2a9242a54491103ae0fcc3637518
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "67723915"
---
# <a name="troubleshoot-the-access-panel-extension-for-internet-explorer"></a>排除 Internet 資源管理器的訪問面板擴展故障

這篇文章可協助您疑難排解下列問題：

* 使用 Internet Explorer 時無法透過「我的 app」入口網站存取您的 app。
* 即使您已經安裝軟體，還是看到「安裝軟體」訊息。

如果您是管理員，請參閱[如何使用群組原則部署 Internet 資源管理器的訪問面板擴展](deploy-access-panel-browser-extension.md)。

## <a name="run-the-diagnostic-tool"></a>運行診斷工具

您可以通過下載並運行訪問面板診斷工具來診斷"訪問面板擴展"的安裝問題。 

要下載並安裝診斷工具：

1. [選擇此連結可下載診斷工具。](https://account.activedirectory.windowsazure.com/applications/AccessPanelExtensionDiagnosticTool/AccessPanelExtensionDiagnosticTool.zip)
1. 打開檔並將內容提取到電腦。
1. 要運行該工具，請按右鍵名為*AccessPanel 擴展診斷工具.js*的檔，然後選擇 **"使用** > **基於微軟 Windows 的腳本主機**打開"。

    ![[開啟檔案] > [Microsoft Windows Based Script Host]](./media/manage-access-panel-browser-extension/open-access-panel-extension-diagnostic-tool.png)

1. 查看顯示的診斷結果，然後選擇 **"是**"以解決問題。 "**檢查結果"** 對話方塊將顯示有關擴展不起作用時應該怎麼做的資訊。  
1. 閱讀郵件並選擇 **"確定**"。

## <a name="check-that-the-access-panel-extension-is-enabled"></a>檢查存取面板延伸模組是否已啟用

要驗證您是否啟用了 Internet 資源管理器中的訪問面板擴展：

1. 在 Internet 資源管理器中，選擇視窗右上角的**齒輪圖示**，然後選擇 Internet**選項**。
1. 轉到"**程式**"選項卡並選擇 **"管理載入項**"。
1. 在**Microsoft 公司**部分中選擇 **"訪問面板擴展****"，** 然後選擇"啟用"。
1. 要保存更改，關閉已打開的所有 Internet Explorer 瀏覽器視窗。 下次打開 Internet 資源管理器時，更改將生效。

## <a name="enable-extensions-for-inprivate-browsing"></a>為 InPrivate 瀏覽啟用擴展

要啟用 InPrivate 瀏覽的擴展：

1. 在 Internet 資源管理器中，選擇視窗右上角的**齒輪圖示**，然後選擇 Internet**選項**。
1. 轉到 **"隱私"** 選項卡，並驗證 **"在 InPrivate 瀏覽啟動時禁用工具列和擴展**"核取方塊是否清晰。
1. 要保存更改，關閉已打開的所有 Internet Explorer 瀏覽器視窗。 下次打開 Internet 資源管理器時，更改將生效。

## <a name="uninstall-the-access-panel-extension"></a>解除安裝存取面板延伸模組

要從電腦卸載訪問面板擴展：

1. 在控制台中，搜索*卸載*。
1. 在搜尋結果中，選擇 **"卸載程式**"。

    ![從"控制台"中選擇"卸載程式"選項](./media/manage-access-panel-browser-extension/uninstall-program-control-panel.png)

1. 從清單中選擇 **"訪問面板擴展**"並選擇 **"卸載**"。

    ![解除安裝存取面板延伸模組](./media/manage-access-panel-browser-extension/uninstall-access-panel-extension.png)

1. 您可以嘗試再安裝一次延伸模組，看看是否已經解決問題。

如果卸載擴展遇到問題，也可以使用[Microsoft 修復 It](https://go.microsoft.com/?linkid=9779673)工具將其刪除。

## <a name="related-articles"></a>相關文章

* [搭配 Azure Active Directory 的應用程式存取和單一登入](what-is-single-sign-on.md)
* [如何使用群組原則部署 Internet 資源管理器的訪問面板擴展](deploy-access-panel-browser-extension.md)
