---
title: 針對適用于 IE 的 Azure 存取面板擴充功能進行疑難排解 |Microsoft Docs
description: 如何使用群組原則針對我的 app 入口網站部署 Internet Explorer 附加元件。
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 16abfbeacd972ee8b0ab55f09945e687c95f0093
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763256"
---
# <a name="troubleshoot-the-access-panel-extension-for-internet-explorer"></a>疑難排解 Internet Explorer 的存取面板延伸模組

這篇文章可協助您疑難排解下列問題：

* 使用 Internet Explorer 時無法透過「我的 app」入口網站存取您的 app。
* 即使您已經安裝軟體，還是看到「安裝軟體」訊息。

如果您是系統管理員，請參閱[如何使用群組原則部署 Internet Explorer 的存取面板延伸](deploy-access-panel-browser-extension.md)模組。

## <a name="run-the-diagnostic-tool"></a>執行診斷工具

您可以下載並執行存取面板診斷工具，以診斷存取面板延伸模組的安裝問題。 

若要下載並安裝診斷工具：

1. [選取此連結以下載診斷工具。](https://account.activedirectory.windowsazure.com/applications/AccessPanelExtensionDiagnosticTool/AccessPanelExtensionDiagnosticTool.zip)
1. 開啟檔案，並將內容解壓縮至您的電腦。
1. 若要執行此工具，請以滑鼠右鍵按一下名為*AccessPanelExtensionDiagnosticTool.js*的檔案，然後選取 [**以**  >  **Microsoft Windows 為基礎的腳本主機**開啟]。

    ![[開啟檔案] > [Microsoft Windows Based Script Host]](./media/manage-access-panel-browser-extension/open-access-panel-extension-diagnostic-tool.png)

1. 查看顯示的診斷結果，然後選取 **[是]** 以修正問題。 [**檢查結果**] 對話方塊隨即出現，其中包含延伸模組無法使用時該怎麼辦的相關資訊。  
1. 閱讀訊息，然後選取 **[確定]**。

## <a name="check-that-the-access-panel-extension-is-enabled"></a>檢查存取面板延伸模組是否已啟用

若要確認您已在 Internet Explorer 中啟用存取面板延伸模組：

1. 在 Internet Explorer 中，選取視窗右上角的**齒輪圖示**，然後選取 [**網際網路選項**]。
1. 移至 [**程式**] 索引標籤，然後選取 [**管理附加**元件]。
1. 在**Microsoft Corporation**區段中選取**存取面板延伸**模組，然後選取 [**啟用**]。
1. 若要儲存變更，請關閉所有已開啟的 Internet Explorer 瀏覽器視窗。 此變更會在您下次開啟 Internet Explorer 時生效。

## <a name="enable-extensions-for-inprivate-browsing"></a>啟用 InPrivate 瀏覽的延伸模組

若要啟用 InPrivate 瀏覽的延伸模組：

1. 在 Internet Explorer 中，選取視窗右上角的**齒輪圖示**，然後選取 [**網際網路選項**]。
1. 移至 [**隱私權**] 索引標籤，並確認已清除 [ **InPrivate 瀏覽啟動時停用工具列和延伸**] 核取方塊。
1. 若要儲存變更，請關閉所有已開啟的 Internet Explorer 瀏覽器視窗。 此變更會在您下次開啟 Internet Explorer 時生效。

## <a name="uninstall-the-access-panel-extension"></a>解除安裝存取面板延伸模組

若要從您的電腦卸載存取面板延伸模組：

1. 在 [控制台] 中，搜尋 [*卸載*]。
1. 在搜尋結果中，選取 [**卸載程式**]。

    ![從 [控制台] 選取 [卸載程式] 選項](./media/manage-access-panel-browser-extension/uninstall-program-control-panel.png)

1. 從清單中選取 [**存取面板延伸**模組]，然後選取 [**卸載**]。

    ![解除安裝存取面板延伸模組](./media/manage-access-panel-browser-extension/uninstall-access-panel-extension.png)

1. 您可以嘗試再安裝一次延伸模組，看看是否已經解決問題。

如果您在卸載延伸模組時遇到問題，也可以使用[Microsoft Fix it](https://go.microsoft.com/?linkid=9779673)工具將它移除。

## <a name="related-articles"></a>相關文章

* [搭配 Azure Active Directory 的應用程式存取和單一登入](what-is-single-sign-on.md)
* [如何使用群組原則部署 Internet Explorer 的存取面板延伸模組](deploy-access-panel-browser-extension.md)
