---
title: Microsoft Threat Modeling Tool 版本9/12/2018
titleSuffix: Azure
description: 閱讀9/12/2018 版所發行 Microsoft Threat Modeling Tool 的版本資訊。 附注包括功能變更和 bug 修正。
author: jegeib
ms.author: jegeib
ms.service: security
ms.subservice: security-develop
ms.topic: article
ms.date: 01/15/2019
ms.openlocfilehash: 780ddc614feef492dafa54862abbea9552496274
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87539810"
---
# <a name="threat-modeling-tool-ga-release-71509112---9122018"></a>Threat Modeling Tool GA 版本 7.1.50911.2 - 9/12/2018

我們很興奮地宣布，Microsoft Threat Modeling Tool 現在已是支援的正式上市 (GA) 版本，並且可供下載。 此版本包含重要的隱私權和安全性更新，以及 Bug 修正、功能更新，和穩定性方面的改進。 在開啟用戶端時，系統將提示 2017 預覽版本的現有使用者透過 ClickOnce 技術更新至最新版本。 針對工具的新使用者，您可以 [下載用戶端](https://aka.ms/threatmodelingtool)。

此版本推出後，我們將結束對 2017 預覽版的支援，並建議所有預覽版使用者更新至 GA 版本。 我們將在 2018 年 10 月 15 日或之後，為 Threat Modeling Tool 設定符合最低需求的 ClickOnce 版本，且所有預覽版用戶端都必須升級。

可從 [Microsoft 下載中心](https://www.microsoft.com/en-us/download/details.aspx?id=49168)取得的 Microsoft Threat Modeling Tool 2016 將持續支援到 2019 年 10 月 1 日，但僅限於重大安全性修正。

## <a name="feature-changes"></a>功能變更

### <a name="azure-stencil-updates"></a>Azure 樣板更新

此版本隨附的樣板集已新增更多 Azure 樣板及其相關聯的威脅和緩和措施。 進行重大變更的主要領域為「Azure 應用程式服務」、「Azure 資料庫供應項目」和「Azure 儲存體」。

![Azure 樣板更新](./media/threat-modeling-tool-releases-71509112/tmt_azure_stencil_update-300x70.png)

### <a name="onedrive-integration-feature-removed"></a>已移除 OneDrive 整合功能

預覽版的「儲存至 OneDrive」、「從 OneDrive 開啟」和「共用連結」功能均已移除。 我們建議 OneDrive 的使用者使用 Microsoft 的 [Windows 版 OneDrive](https://onedrive.live.com/about/en-us/download/) 用戶端，透過標準的檔案儲存和開啟對話方塊存取他們在 OneDrive 上儲存的檔案。

## <a name="notable-fixed-bugs-reported-by-customers"></a>客戶回報 Bug 的重要修正

### <a name="in-tmt-preview-the-tool-crashes-when-using-the-standard-template"></a>在 TMT 預覽版中，工具在使用標準範本時會當機

- 在繪圖介面中新增一般樣板 (例如「一般資料流程」)，並產生威脅時，此工具可能會當機。 已修正此問題。

### <a name="in-tmt-preview-when-i-save-a-report-or-copy-the-threats-the-risk-levels-are-incorrect"></a>在 TMT 預覽版中，當我儲存報告或複製威脅時，風險層級會不正確

- 如果使用者修改了特定威脅的風險層級，然後儲存報告或複製風險，風險層級可能會還原為「高」。 已修正此問題。

## <a name="known-issues-and-faq"></a>已知問題和常見問題集

### <a name="users-of-high-resolution-screens-may-experience-small-text-in-the-threat-properties"></a>使用者在使用高解析度螢幕時，威脅屬性中的文字可能會顯得太小

#### <a name="issue"></a>問題

在此工具的 [分析] 檢視中，如果使用者使用高解析度螢幕，並且依預設放大以利在 Windows 中閱讀，威脅的 [可能的緩和措施] 區段可能會以小型文字呈現。

![高解析度螢幕的已知問題](./media/threat-modeling-tool-releases-71509112/tmt_screen_resolution-300x153.png)

#### <a name="workaround"></a>因應措施

使用者可以按一下緩和措施文字，並使用標準 Windows 縮放控制 (Crtl+滑鼠滾輪向上) 放大該區段。

### <a name="files-in-the-recently-opened-models-section-of-the-main-window-may-fail-to-open"></a>主視窗的 [最近開啟的模型] 區段中包含的檔案可能無法開啟

#### <a name="issue"></a>問題

預覽版本的「從 OneDrive 開啟」功能已移除。 使用者若有已儲存至 OneDrive 的「最近開啟的模型」，將會看到下列錯誤。

![已移除 OneDrive 功能](./media/threat-modeling-tool-releases-71509112/tmt_save_error-300x131.png)

#### <a name="workaround"></a>因應措施

我們建議 OneDrive 的使用者使用 Microsoft 的 [Windows 版 OneDrive](https://onedrive.live.com/about/en-us/download/) 用戶端，透過標準的 [開啟模型] 對話方塊存取他們在 OneDrive 上儲存的檔案。

![已移除 OneDrive 功能](./media/threat-modeling-tool-releases-71509112/tmt_save_onedrive-300x149.png)

### <a name="my-organization-uses-the-2016-version-of-the-tool-can-i-use-the-azure-stencil-set"></a>組織使用 2016 版的工具，我可以使用 Azure 樣板集嗎？

是，您可以！ [Azure 樣板集可在 GitHub 上](https://github.com/Microsoft/threat-modeling-templates/)取得，並可在2016版的工具中載入。 若要使用 Azure 樣板集建立新模型，請使用主功能表畫面上的 [新模型的範本] 對話方塊。 TMT 2016 無法轉譯 Azure 樣板集的 [可能的緩和措施] 欄位中包含的連結，因此您可能會看到顯示為 HTML 標記的連結。

![2016 用戶端中的 Azure 樣板更新](./media/threat-modeling-tool-releases-71509112/tmt_azure_stencils-300x212.png)

## <a name="system-requirements"></a>系統需求

- 支援的作業系統
  - Microsoft Windows 10
- 必要的 .NET 版本
  - .NET 3.5。2
- 其他需求
  - 必須有網際網路連線可接收工具和範本的更新。

## <a name="documentation-and-feedback"></a>文件和意見反應

- Threat Modeling Tool 的文件位於 [docs.microsoft.com](threat-modeling-tool.md)，其中包含[關於使用工具](threat-modeling-tool-getting-started.md)的資訊。

## <a name="next-steps"></a>後續步驟

下載最新版的 [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool)。
