---
title: 重新啟用已停用的 Azure 存取控制服務（ACS）命名空間
description: 尋找並啟用您的 Azure 存取控制服務（ACS）命名空間，並要求延伸模組以保持啟用，直到2019年2月4日為止。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 01/21/2019
ms.author: ryanwi
ms.reviewer: jlu
ms.custom: aaddev
ms.openlocfilehash: ad908b814a7c2b4dce9d15153b0991f51f623e8b
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2020
ms.locfileid: "76697093"
---
# <a name="how-to-reactivate-disabled-access-control-service-namespaces"></a>操作說明：重新啟用已停用的存取控制服務命名空間

我們在 2017 年 11 月宣布 Microsoft Azure 存取控制服務 (ACS) 這項 Azure Active Directory (Azure AD) 服務，將於 2018 年 11 月 7 日淘汰。

從那時開始，我們已在 ACS 2018 年 11 月 7 日淘汰日期的 12 個月、9 個月、6 個月、3 個月、1個月、2 週、1 週和 1 天之前，傳送電子郵件至 ACS 訂用帳戶的系統管理員電子郵件地址。

在 2018 年 10 月 3 日時，我們宣布 (透過電子郵件和[部落格文章](https://azure.microsoft.com/blog/one-month-retirement-notice-access-control-service/)) 為無法在 2018 年 11 月 7 日之前完成遷移的客戶提供延長時間。 公告也包含要求延長遷移時間的指示。

## <a name="why-your-namespace-is-disabled"></a>為何您的命名空間遭到停用

如果您還沒有選擇加入延長時間，我們將從 2018 年 11 月 7 日開始停用 ACS 命名空間。 您必須已要求延長到 2019 年 2 月 4 日；否則您將無法透過 PowerShell 啟用命名空間。

> [!NOTE]
> 您必須是訂用帳戶的服務管理員或共同管理員，才能執行 PowerShell 命令並要求延長。

## <a name="find-and-enable-your-acs-namespaces"></a>請尋找並啟用您的 ACS 命名空間

您可以使用 ACS PowerShell 列出您所有的 ACS 命名空間，並重新啟用已停用的命名空間。

1. 下載並安裝 ACS PowerShell：
    1. 移至 PowerShell 資源庫並下載 [Acs.Namespaces](https://www.powershellgallery.com/packages/Acs.Namespaces/1.0.2)。
    1. 安裝模組：

        ```powershell
        Install-Module -Name Acs.Namespaces
        ```

    1. 取得所有可能命令的清單：

        ```powershell
        Get-Command -Module Acs.Namespaces
        ```

        若要取得特定命令的說明，請執行：

        ```powershell
        Get-Help [Command-Name] -Full
        ```
    
        其中 `[Command-Name]` 是 ACS 命令的名稱。
1. 使用 **Connect-AcsAccount** Cmdlet 連線至 ACS。 

    您可能需要先執行 **Set-ExecutionPolicy** 來變更執行原則，才能執行命令。
1. 使用 **Get-AcsSubscription** Cmdlet 列出可用的 Azure 訂用帳戶。
1. 使用 **Get-AcsNamespace** Cmdlet 列出您的 ACS 命名空間。
1. 透過確認 `State` 為 `Disabled` 以確認已停用的命名空間。

    [![確認已停用的命名空間](./media/howto-reactivate-disabled-acs-namespaces/confirm-disabled-namespace.png)](./media/howto-reactivate-disabled-acs-namespaces/confirm-disabled-namespace.png#lightbox)

    您也可以使用 `nslookup {your-namespace}.accesscontrol.windows.net` 確認網域是否仍在作用中。

1. 使用 **Enable-AcsNamespace** Cmdlet 啟用您的 ACS 命名空間。

    一旦您啟用命名空間之後，就可以要求延長，命名空間就不會在 2019 年 2 月 4 日之前再次停用。 從該日期開始，對 ACS 提出的所有要求都會失敗。

## <a name="request-an-extension"></a>要求延長

我們將於 2019 年 1 月 21 日開始採用新的延長要求。

我們將開始對已要求延長至 2019 年 2 月 4 日的客戶停用命名空間。 您仍然可以透過 PowerShell 重新啟用命名空間，但命名空間將會在 48 小時之後再次停用。

2019 年 3 月 4 日之後，客戶將無法再透過 PowerShell 重新啟用任何命名空間。

系統將不會再自動核准更久的延長時間。 如果您需要額外的時間來進行遷移，請連絡 [Azure 支援](https://portal.azure.com/#create/Microsoft.Support)來提供詳細的移轉時間軸。

### <a name="to-request-an-extension"></a>要求延長

1. 登入 Azure 入口網站，並建立新的[支援要求](https://portal.azure.com/#create/Microsoft.Support)。
1. 填入新的支援要求表單，如下列範例所示。

    | 支援要求欄位 | 值 |
    |-----------------------|--------------------|
    | **問題類型** | `Technical` |
    | **訂用帳戶** | 設為您的訂用帳戶 |
    | **服務** | `All services` |
    | **Resource** | `General question/Resource not available` |
    | **問題類型** | `ACS to SAS Migration` |
    | **主旨** | 描述問題 |

   ![顯示新技術支援要求的範例](./media/howto-reactivate-disabled-acs-namespaces/new-technical-support-request.png)

<!--

1. Navigate to your ACS namespace's management portal by going to `https://{your-namespace}.accesscontrol.windows.net`.
1. Select the **Read Terms** button to read the [updated Terms of Use](https://azure.microsoft.com/support/legal/access-control/), which will direct you to a page with the updated Terms of Use.

    [![Select the Read Terms button](./media/howto-reactivate-disabled-acs-namespaces/read-terms-button-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/read-terms-button-expanded.png#lightbox)

1. Select **Request Extension** on the banner at the top of the page. The button will only be enabled after you read the [updated Terms of Use](https://azure.microsoft.com/support/legal/access-control/).

    [![Select the Request Extension button](./media/howto-reactivate-disabled-acs-namespaces/request-extension-button-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/request-extension-button-expanded.png#lightbox)

1. After the extension request is registered, the page will refresh with a new banner at the top of the page.

    [![Updated page with refreshed banner](./media/howto-reactivate-disabled-acs-namespaces/updated-banner-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/updated-banner-expanded.png#lightbox)
-->

## <a name="help-and-support"></a>說明及支援

- 如果您遵循此操作說明之後遇到任何問題，請連絡 [Azure 支援](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)。
- 如果您有和 ACS 淘汰有關的問題或意見反應，請透過 acsfeedback@microsoft.com 與我們聯絡。

## <a name="next-steps"></a>後續步驟

- 檢閱[操作說明：從 Azure 存取控制服務遷移](active-directory-acs-migration.md)中的 ACS 淘汰相關資訊。
