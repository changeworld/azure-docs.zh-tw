---
title: Windows 虛擬桌面故障排除概述 - Azure
description: 設置 Windows 虛擬桌面租戶環境時疑難排解問題的概述。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 02/07/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 2b9dfb2ee7e74f94bfc3e6d2c679cb7da3d6bc66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127396"
---
# <a name="troubleshooting-overview-feedback-and-support"></a>疑難排解概觀、意見反應和支援

本文概述了設置 Windows 虛擬桌面租戶環境時可能會遇到的問題，並提供瞭解決問題的方法。

## <a name="provide-feedback"></a>提供意見反應

請瀏覽 [Windows 虛擬桌面技術社群](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)，與產品小組和活躍的社群成員一起討論 Windows 虛擬桌面服務。

## <a name="escalation-tracks"></a>升級軌道

使用下表標識並解決使用遠端桌面用戶端設置租戶環境時可能遇到的問題。 設置租戶後，您可以使用我們新的[診斷服務](diagnostics-role-service.md)來識別常見方案的問題。

>[!NOTE]
> 我們有一個技術社區論壇，您可以訪問該論壇，與產品團隊和活躍的社區成員討論您的問題。 訪問[Windows 虛擬桌面技術社區](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)開始討論。

| **問題**                                                            | **建議的解決方案**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| 創建 Windows 虛擬桌面租戶                                                    | 如果存在 Azure 中斷，[請打開 Azure 支援請求](https://azure.microsoft.com/support/create-ticket/)。否則[打開 Azure 支援請求](https://azure.microsoft.com/support/create-ticket/)，為服務選擇**Windows 虛擬桌面**，為問題類型選擇 **"部署"，** 然後為問題子類型選擇**創建 Windows 虛擬桌面租戶的問題**。|
| 訪問 Azure 門戶中的應用商店範本       | 如果存在 Azure 中斷，[請打開 Azure 支援請求](https://azure.microsoft.com/support/create-ticket/)。 <br> <br> Azure 應用商店 Windows 虛擬桌面範本可免費使用。|
| 從 GitHub 訪問 Azure 資源管理器範本                                  | 請參閱[創建"創建 Windows 虛擬桌面"工作階段主機 VM](troubleshoot-set-up-issues.md#creating-windows-virtual-desktop-session-host-vms)部分的[租戶和主機池創建](troubleshoot-set-up-issues.md)。 如果問題仍未解決，請與[GitHub 支援小組](https://github.com/contact)聯繫。 <br> <br> 如果在 GitHub 中訪問範本後發生錯誤，請與[Azure 支援](https://azure.microsoft.com/support/create-ticket/)。|
| 工作階段主機池 Azure 虛擬網路 （VNET） 和快速路由設置               | [打開 Azure 支援請求](https://azure.microsoft.com/support/create-ticket/)，然後選擇相應的服務（在"網路"類別下）。 |
| 未使用 Windows 虛擬桌面提供的 Azure 資源管理器範本時創建工作階段主機池虛擬機器 （VM） | [打開 Azure 支援請求](https://azure.microsoft.com/support/create-ticket/)，然後選擇**運行該服務的 Windows**的虛擬機器。 <br> <br> 有關 Windows 虛擬桌面提供的 Azure 資源管理器範本的問題，請參閱創建租戶的 Windows 虛擬桌面租戶部分[和主機池創建](troubleshoot-set-up-issues.md)。 |
| 從 Azure 門戶管理 Windows 虛擬桌面工作階段主機環境    | [打開 Azure 支援請求](https://azure.microsoft.com/support/create-ticket/)。 <br> <br> 有關使用遠端桌面服務/Windows 虛擬桌面 PowerShell 時的管理問題，請參閱[Windows 虛擬桌面 PowerShell](troubleshoot-powershell.md)或[打開 Azure 支援請求](https://azure.microsoft.com/support/create-ticket/)，為服務選擇**Windows 虛擬桌面**，為問題類型選擇 **"配置和管理**"，然後選擇**使用 PowerShell**配置租戶的問題。 |
| 管理綁定到主機池和應用程式組（應用組）的 Windows 虛擬桌面配置      | 請參閱[Windows 虛擬桌面 PowerShell](troubleshoot-powershell.md)，或[打開 Azure 支援請求](https://azure.microsoft.com/support/create-ticket/)，為服務選擇**Windows 虛擬桌面**，然後選擇適當的問題類型。|
| 部署和管理 FSLogix 設定檔容器 | 請參閱[FSLogix 產品的故障排除指南](/fslogix/fslogix-trouble-shooting-ht/)，如果這不能解決問題，[請打開 Azure 支援請求](https://azure.microsoft.com/support/create-ticket/)，為服務選擇**Windows 虛擬桌面**，為問題類型選擇**FSLogix，** 然後選擇適當的問題子類型。 |
| 遠端桌面用戶端在啟動時出現故障                                                 | 請參閱[對遠端桌面用戶端進行故障排除](troubleshoot-client.md)，如果這不能解決問題，[請打開 Azure 支援請求](https://azure.microsoft.com/support/create-ticket/)，為服務選擇**Windows 虛擬桌面**，然後為問題類型選擇**遠端桌面用戶端**。  <br> <br> 如果是網路問題，則使用者需要與其網路系統管理員聯繫。 |
| 已連接，但沒有源                                                                 | 使用使用者連接進行故障排除，但[Windows 虛擬桌面服務連接](troubleshoot-service-connection.md)[不顯示任何內容（無源）](troubleshoot-service-connection.md#user-connects-but-nothing-is-displayed-no-feed)部分。 <br> <br> 如果使用者已分配到應用組，[請打開 Azure 支援請求](https://azure.microsoft.com/support/create-ticket/)，為服務選擇**Windows 虛擬桌面**，然後為問題類型選擇**遠端桌面用戶端**。 |
| 由於網路而導致的源發現問題                                            | 您的使用者需要與其網路系統管理員聯繫。 |
| 連接用戶端                                                                    | 請參閱[Windows 虛擬桌面服務連接](troubleshoot-service-connection.md)，如果這不能解決您的問題，請參閱[工作階段主機虛擬機器配置](troubleshoot-vm-configuration.md)。 |
| 遠端應用程式或桌面的回應能力                                      | 如果問題與特定應用程式或產品相關，請與負責該產品的團隊聯繫。 |
| 許可消息或錯誤                                                          | 如果問題與特定應用程式或產品相關，請與負責該產品的團隊聯繫。 |
| 在 GitHub 上使用 Windows 虛擬桌面工具時的問題（Azure 資源管理器範本、診斷工具和管理工具） | 請參閱[遠端桌面服務的 Azure 資源管理器範本](https://github.com/Azure/RDS-Templates/blob/master/README.md)以報告問題。 |

## <a name="next-steps"></a>後續步驟

- 要在 Windows 虛擬桌面環境中創建租戶和主機池時解決問題，請參閱[租戶和主機池創建](troubleshoot-set-up-issues.md)。
- 要在 Windows 虛擬桌面中配置虛擬機器 （VM） 時解決問題，請參閱[工作階段主機虛擬機器配置](troubleshoot-vm-configuration.md)。
- 要解決 Windows 虛擬桌面用戶端連接的問題，請參閱[Windows 虛擬桌面服務連接](troubleshoot-service-connection.md)。
- 要解決遠端桌面用戶端的問題，請參閱[排除遠端桌面用戶端的故障](troubleshoot-client.md)
- 要解決在 Windows 虛擬桌面中使用 PowerShell 時的問題，請參閱[Windows 虛擬桌面電源外殼](troubleshoot-powershell.md)。
- 要瞭解有關該服務的更多資訊，請參閱[Windows 虛擬桌面環境](environment-setup.md)。
- 要完成疑難排解教程，請參閱[教程：解決資源管理器範本部署的疑難排解](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)。
- 若要了解稽核動作，請參閱 [使用 Resource Manager 來稽核作業](../azure-resource-manager/management/view-activity-logs.md)。
- 要瞭解在部署過程中確定錯誤的操作，請參閱[查看部署操作](../azure-resource-manager/templates/deployment-history.md)。
