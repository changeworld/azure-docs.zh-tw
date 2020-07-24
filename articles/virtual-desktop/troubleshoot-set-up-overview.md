---
title: Windows 虛擬桌面疑難排解總覽-Azure
description: 在設定 Windows 虛擬桌面環境時，針對問題進行疑難排解的總覽。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 06/05/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 1010f89057d83a11fb2e156f01509e909270b044
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87083567"
---
# <a name="troubleshooting-overview-feedback-and-support-for-windows-virtual-desktop"></a>Windows 虛擬桌面的疑難排解總覽、意見反應和支援

>[!IMPORTANT]
>此內容適用於具有 Azure Resource Manager Windows 虛擬桌面物件的 2020 年春季更新版。 如果您使用不含 Azure Resource Manager 物件的 Windows 虛擬桌面 2019 年秋季版，請參閱[這篇文章](./virtual-desktop-fall-2019/troubleshoot-set-up-overview-2019.md)。
>
> Windows 虛擬桌面 2020 年春季更新版目前為公開預覽狀態。 此預覽版本是在沒有服務等級協定的情況下提供，不建議您將其用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

本文概述您在設定 Windows 虛擬桌面環境時可能會遇到的問題，並提供解決問題的方法。

## <a name="report-issues-during-public-preview"></a>在公開預覽期間報告問題

若要在春季2020版本的公開預覽期間回報問題或建議功能，請造訪[Windows 虛擬桌面技術小組](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)。 您可以使用技術小組來討論最佳作法，或是針對新功能提出建議和投票。 當您報告與公開預覽版本相關的問題時，請務必將問題類型標記為**春季2020更新（預覽）**。

當您提出要求協助或提議新功能的文章時，請務必盡可能詳細地描述您的主題。 詳細資訊可協助其他使用者回答您的問題，或瞭解您想要投票的功能。

## <a name="escalation-tracks"></a>呈報追蹤

在執行任何其他動作之前，請務必檢查[azure 狀態頁面](https://status.azure.com/status)並[Azure 服務健康狀態](https://azure.microsoft.com/features/service-health/)，以確定您的 azure 服務正常運作。

使用下表來找出並解決使用遠端桌面用戶端設定環境時可能會遇到的問題。 設定環境之後，您可以使用新的[診斷服務](diagnostics-role-service.md)來識別常見案例的問題。

| **問題**                                                            | **建議的解決方案**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| 工作階段主機集區 Azure 虛擬網路（VNET）和 Express Route 設定               | [開啟 Azure 支援要求](https://azure.microsoft.com/support/create-ticket/)，然後選取適當的服務（在 [網路] 類別底下）。 |
| 工作階段主機集區虛擬機器（VM）會在未使用 Windows 虛擬桌面提供的 Azure Resource Manager 範本時建立 | [開啟 Azure 支援要求](https://azure.microsoft.com/support/create-ticket/)，然後針對服務選取 [ **Windows 虛擬桌面**]。 <br> <br> 如需 Windows 虛擬桌面隨附之 Azure Resource Manager 範本的問題，請參閱[建立主機集](troubleshoot-set-up-issues.md)區的 Azure Resource Manager 範本錯誤一節。 |
| 從 Azure 入口網站管理 Windows 虛擬桌面工作階段主機環境    | [開啟 Azure 支援要求](https://azure.microsoft.com/support/create-ticket/)。 <br> <br> 如需使用遠端桌面服務/Windows 虛擬桌面 PowerShell 時的管理問題，請參閱[Windows 虛擬桌面 powershell](troubleshoot-powershell.md)或[開啟 Azure 支援要求](https://azure.microsoft.com/support/create-ticket/)，選取 [ **Windows 虛擬桌面**服務]，選取問題類型的 [設定**和管理**]，然後選取 [針對問題子類型**使用 PowerShell 設定環境] 問題**。 |
| 管理系結至主機集區和應用程式群組的 Windows 虛擬桌面設定（應用程式群組）      | 請參閱[Windows 虛擬桌面 PowerShell](troubleshoot-powershell.md)，或[開啟 Azure 支援要求](https://azure.microsoft.com/support/create-ticket/)，選取服務的**Windows 虛擬桌面**，然後選取適當的問題類型。|
| 部署和管理 FSLogix 設定檔容器 | 請參閱[FSLogix 產品的疑難排解指南](/fslogix/fslogix-trouble-shooting-ht/)，如果無法解決問題，請[開啟 Azure 支援要求](https://azure.microsoft.com/support/create-ticket/)，選取服務的**Windows 虛擬桌面**，針對問題類型選取 [ **FSLogix** ]，然後選取適當的問題子類型。 |
| 遠端桌面用戶端在啟動時無法正常運作                                                 | 請參閱[疑難排解遠端桌面用戶端](troubleshoot-client.md)，如果無法解決問題，請[開啟 Azure 支援要求](https://azure.microsoft.com/support/create-ticket/)，選取服務的**Windows 虛擬桌面**，然後針對問題類型選取 [**遠端桌面用戶端**]。  <br> <br> 如果是網路問題，您的使用者必須聯絡其網路系統管理員。 |
| 已連線但沒有摘要                                                                 | 疑難排解使用使用者連接，但[Windows 虛擬桌面服務](troubleshoot-service-connection.md)連線[不會顯示任何內容（沒有摘要）](troubleshoot-service-connection.md#user-connects-but-nothing-is-displayed-no-feed)區段。 <br> <br> 如果您的使用者已指派給應用程式群組，請[開啟 Azure 支援要求](https://azure.microsoft.com/support/create-ticket/)，針對服務選取 [ **Windows 虛擬桌面**]，然後針對 [問題類型] 選取 [**遠端桌面用戶端**]。 |
| 因網路而造成的摘要探索問題                                            | 您的使用者必須聯絡其網路系統管理員。 |
| 連接用戶端                                                                    | 請參閱[Windows 虛擬桌面服務](troubleshoot-service-connection.md)連線，如果無法解決您的問題，請參閱[工作階段主機虛擬機器](troubleshoot-vm-configuration.md)設定。 |
| 遠端應用程式或桌上型電腦的回應能力                                      | 如果問題系結至特定的應用程式或產品，請洽詢負責該產品的小組。 |
| 授權訊息或錯誤                                                          | 如果問題系結至特定的應用程式或產品，請洽詢負責該產品的小組。 |
| 協力廠商驗證方法的問題 | 請確認您的協力廠商提供者支援 Windows 虛擬桌面案例，並針對任何已知問題加以解決。 |
| 使用 Log Analytics 進行 Windows 虛擬桌面的問題 | 對於診斷架構的問題，請[開啟 Azure 支援要求](https://azure.microsoft.com/support/create-ticket/)。<br><br>針對 Log Analytics 中的查詢、視覺效果或其他問題，請在 Log Analytics 底下選取適當的問題類型。 |
| 使用 M365 apps 的問題 | 使用其中一個[M365 系統管理中心說明選項](/microsoft-365/admin/contact-support-for-business-products/)來聯絡 M365 系統管理中心。 |

## <a name="next-steps"></a>後續步驟

- 若要針對在 Windows 虛擬桌面環境中建立主機集區的問題進行疑難排解，請參閱[建立主機集](troubleshoot-set-up-issues.md)區。
- 若要針對在 Windows 虛擬桌面中設定虛擬機器 (VM) 時的問題進行疑難排解，請參閱[工作階段主機虛擬機器設定](troubleshoot-vm-configuration.md)。
- 若要針對 Windows 虛擬桌面用戶端連接的問題進行疑難排解，請參閱[Windows 虛擬桌面服務連接](troubleshoot-service-connection.md)。
- 若要疑難排解遠端桌面用戶端的問題，請參閱針對[遠端桌面用戶端進行疑難排解](troubleshoot-client.md)
- 若要針對使用 PowerShell 搭配 Windows 虛擬桌面時的問題進行疑難排解，請參閱 [Windows 虛擬桌面 PowerShell](troubleshoot-powershell.md)。
- 若要深入瞭解此服務，請參閱[Windows 虛擬桌面環境](environment-setup.md)。
- 若要進行疑難排解教學課程，請參閱[教學課程：針對 Resource Manager 範本部署進行疑難排解](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)。
- 若要了解稽核動作，請參閱 [使用 Resource Manager 來稽核作業](../azure-resource-manager/management/view-activity-logs.md)。
- 若要瞭解在部署期間判斷錯誤的動作，請參閱[查看部署作業](../azure-resource-manager/templates/deployment-history.md)。
