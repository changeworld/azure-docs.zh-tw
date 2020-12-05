---
title: Windows 虛擬桌面疑難排解總覽-Azure
description: 針對設定 Windows 虛擬桌面環境時的問題進行疑難排解的總覽。
author: Heidilohr
ms.topic: troubleshooting
ms.date: 12/04/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: d051594572f742b290867d12e0fb148f2abbf370
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2020
ms.locfileid: "96621309"
---
# <a name="troubleshooting-overview-feedback-and-support-for-windows-virtual-desktop"></a>Windows 虛擬桌面的疑難排解總覽、意見反應和支援

>[!IMPORTANT]
>此內容適用於具有 Azure Resource Manager Windows 虛擬桌面物件的 Windows 虛擬桌面。 如果您使用不含 Azure Resource Manager 物件的 Windows 虛擬桌面 (傳統)，請參閱[此文章](./virtual-desktop-fall-2019/troubleshoot-set-up-overview-2019.md)。

本文概述您在設定 Windows 虛擬桌面環境時可能會遇到的問題，並提供解決問題的方法。

## <a name="report-issues"></a>回報問題

若要使用 Azure Resource Manager 整合來回報適用于 Windows 虛擬桌面的問題或建議功能，請造訪 [Windows 虛擬桌面技術社區](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)。 您可以使用技術小組討論最佳做法，或建議和投票新功能。

當您提出要求協助或提議新功能的文章時，請務必盡可能詳細地描述您的主題。 詳細資訊可協助其他使用者回答您的問題，或瞭解您所建議的投票功能。

## <a name="escalation-tracks"></a>呈報追蹤

在進行任何其他作業之前，請務必檢查 [azure 狀態頁面](https://status.azure.com/status) 並 [Azure 服務健康狀態](https://azure.microsoft.com/features/service-health/) ，以確定您的 azure 服務執行正常。

使用下表來找出並解決您在使用遠端桌面用戶端設定環境時可能會遇到的問題。 設定好環境之後，您就可以使用新的 [診斷服務](diagnostics-role-service.md) 來找出常見案例的問題。

| **問題**                                                            | **建議的解決方案**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| 工作階段主機集區 Azure 虛擬網路 (VNET) 和 Express Route 設定               | [開啟 Azure 支援要求](https://azure.microsoft.com/support/create-ticket/)，然後在 [網路] 類別下選取適當的服務 () 。 |
| 當未使用 Windows 虛擬桌面提供的 Azure Resource Manager 範本時，工作階段主機集區虛擬機器 (VM) 建立 | [開啟 Azure 支援要求](https://azure.microsoft.com/support/create-ticket/)，然後選取 [ **Windows 虛擬桌面** ] 作為服務。 <br> <br> 如有 Windows 虛擬桌面提供 Azure Resource Manager 範本的問題，請參閱 [建立主機集](troubleshoot-set-up-issues.md)區 Azure Resource Manager 範本錯誤一節。 |
| 從 Azure 入口網站管理 Windows 虛擬桌面工作階段主機環境    | [開啟 Azure 支援要求](https://azure.microsoft.com/support/create-ticket/)。 <br> <br> 針對使用遠端桌面服務/Windows 虛擬桌面 PowerShell 時發生的管理問題，請參閱 [Windows 虛擬桌面 powershell](troubleshoot-powershell.md) 或 [開啟 Azure 支援要求](https://azure.microsoft.com/support/create-ticket/)、為服務選取 [ **windows 虛擬桌面** ]、針對問題類型選取 [設定 **和管理** ]，然後選取 [ **使用 PowerShell** 針對問題子類型設定環境] 的問題。 |
| 管理系結至主機集區和應用程式群組的 Windows 虛擬桌面設定 (應用程式群組)       | 請參閱 [Windows 虛擬桌面 PowerShell](troubleshoot-powershell.md)，或 [開啟 Azure 支援要求](https://azure.microsoft.com/support/create-ticket/)，選取服務的 **windows 虛擬桌面** ，然後選取適當的問題類型。|
| 部署及管理 FSLogix 設定檔容器 | 請參閱 [FSLogix 產品的疑難排解指南](/fslogix/fslogix-trouble-shooting-ht/) ，如果無法解決問題，請 [開啟 Azure 支援要求](https://azure.microsoft.com/support/create-ticket/)，選取服務的 **Windows 虛擬桌面** ，針對問題類型選取 [ **FSLogix** ]，然後選取適當的問題子類型。 |
| 遠端桌面用戶端在啟動時無法正常運作                                                 | 請參閱 [疑難排解遠端桌面用戶端](troubleshoot-client.md) ，如果無法解決問題，請  [開啟 Azure 支援要求](https://azure.microsoft.com/support/create-ticket/)，選取服務的 **Windows 虛擬桌面** ，然後針對問題類型選取 [ **遠端桌面用戶端** ]。  <br> <br> 如果這是網路問題，您的使用者必須與網路系統管理員聯絡。 |
| 已連線但未饋送                                                                 | 使用使用者連線進行疑難排解 [，但不顯示任何 (](troubleshoot-service-connection.md#user-connects-but-nothing-is-displayed-no-feed) [Windows 虛擬桌面服務](troubleshoot-service-connection.md)連線) 區段的內容。 <br> <br> 如果您的使用者已指派至應用程式群組，請  [開啟 Azure 支援要求](https://azure.microsoft.com/support/create-ticket/)，並選取服務的 [ **Windows 虛擬桌面** ]，然後針對問題類型選取 [ **遠端桌面用戶端** ]。 |
| 由於網路的摘要探索問題                                            | 您的使用者需要聯繫其網路系統管理員。 |
| 連接用戶端                                                                    | 請參閱 [Windows 虛擬桌面服務](troubleshoot-service-connection.md) 連線，如果無法解決您的問題，請參閱 [工作階段主機虛擬機器](troubleshoot-vm-configuration.md)設定。 |
| 遠端應用程式或桌面的回應                                      | 如果問題系結至特定的應用程式或產品，請洽詢負責該產品的小組。 |
| 授權訊息或錯誤                                                          | 如果問題系結至特定的應用程式或產品，請洽詢負責該產品的小組。 |
| 協力廠商驗證方法或工具的問題 | 確認協力廠商提供者支援 Windows 虛擬桌面案例，並將其與任何已知問題相關。 |
| 使用適用于 Windows 虛擬桌面的 Log Analytics 的問題 | 針對診斷架構的問題，請 [開啟 Azure 支援要求](https://azure.microsoft.com/support/create-ticket/)。<br><br>針對 Log Analytics 中的查詢、視覺效果或其他問題，請在 Log Analytics 底下選取適當的問題類型。 |
| 使用 M365 apps 的問題 | 使用其中一個 [M365 系統管理中心](/microsoft-365/admin/contact-support-for-business-products/)的 [說明] 選項來聯絡 M365 系統管理中心。 |

## <a name="next-steps"></a>後續步驟

- 若要針對在 Windows 虛擬桌面環境中建立主機集區時所發生的問題進行疑難排解，請參閱 [主機集區建立](troubleshoot-set-up-issues.md)。
- 若要針對在 Windows 虛擬桌面中設定虛擬機器 (VM) 時的問題進行疑難排解，請參閱[工作階段主機虛擬機器設定](troubleshoot-vm-configuration.md)。
- 若要對 Windows 虛擬桌面用戶端連線問題進行疑難排解，請參閱 [Windows 虛擬桌面服務連接](troubleshoot-service-connection.md)。
- 若要疑難排解遠端桌面用戶端的問題，請參閱 [遠端桌面用戶端疑難排解](troubleshoot-client.md)
- 若要針對使用 PowerShell 搭配 Windows 虛擬桌面時的問題進行疑難排解，請參閱 [Windows 虛擬桌面 PowerShell](troubleshoot-powershell.md)。
- 若要深入瞭解此服務，請參閱 [Windows 虛擬桌面環境](environment-setup.md)。
- 若要進行疑難排解教學課程，請參閱[教學課程：針對 Resource Manager 範本部署進行疑難排解](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)。
- 若要了解稽核動作，請參閱 [使用 Resource Manager 來稽核作業](../azure-resource-manager/management/view-activity-logs.md)。
- 若要瞭解在部署期間判斷錯誤的動作，請參閱 [查看部署作業](../azure-resource-manager/templates/deployment-history.md)。
