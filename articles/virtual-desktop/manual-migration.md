---
title: 從 Windows 虛擬桌面 (傳統) 手動遷移-Azure
description: 如何從 Windows 虛擬桌面 (傳統) 手動遷移至 Windows 虛擬桌面。
author: Heidilohr
ms.topic: how-to
ms.date: 09/11/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: fd292fa6b4f613e0a5f5a80e0cd87675f529baf5
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/24/2020
ms.locfileid: "92516166"
---
# <a name="migrate-manually-from-windows-virtual-desktop-classic"></a>從 Windows 虛擬桌面手動遷移 (傳統) 

Windows 虛擬桌面 (傳統) 使用 PowerShell Cmdlet、REST Api 和服務物件來建立其服務環境。 Windows 虛擬桌面服務環境中的「物件」是 Windows 虛擬桌面建立的一件事。 服務物件包括租使用者、主機集區、應用程式群組和工作階段主機。

不過，Windows 虛擬桌面 (傳統) 不會與 Azure 整合。 如果沒有 Azure 整合，您所建立的任何物件都不會自動受 Azure 入口網站管理，因為它們不會連線到您的 Azure 訂用帳戶。

Windows 虛擬桌面最新的重大更新，會將服務中的轉移標示為完整的 Azure 整合。 您在 Windows 虛擬桌面中建立的物件會由 Azure 入口網站自動管理。

在本文中，我們將說明為什麼您應該考慮遷移至最新版本的 Windows 虛擬桌面。 之後，我們將告訴您如何以手動方式從 Windows 虛擬桌面 (傳統) 遷移至最新的 Windows 虛擬桌面更新。

## <a name="why-migrate"></a>為何要移轉？

重大更新可能不方便，尤其是您必須手動進行的更新。 不過，有一些原因無法自動遷移：

- 使用傳統版本所建立的現有服務物件在 Azure 中沒有任何標記法。 其範圍不會延伸到 Windows 虛擬桌面服務之外。
- 使用最新的更新時，服務的應用程式識別碼已變更為移除應用程式的同意，其方式與 Windows 虛擬桌面 (傳統) 的方式相同。 除非已使用新的應用程式識別碼進行驗證，否則您將無法使用 Windows 虛擬桌面建立新的 Azure 物件。

無論如何，從傳統版本遷移是很重要的。 以下是您遷移後可以執行的動作：

- 透過 Azure 入口網站管理 Windows 虛擬桌面。
- 將 Azure Active Directory (AD) 使用者群組指派給應用程式群組。
- 使用改良的 Log Analytics 功能來疑難排解您的部署。
- 使用 Azure 原生角色型存取控制 (Azure RBAC) 來管理系統管理存取權。

## <a name="when-should-i-migrate"></a>我應該在何時遷移？

當詢問您是否應該遷移時，您也應該考慮部署的目前和未來情況。

在某些案例中，我們建議您手動遷移：

- 您有少數使用者的測試主機集區設定。
- 您的生產主機集區設定有少數幾個使用者，但預計最終可提升至數百名使用者。
- 您可以輕鬆地複寫簡單的設定。 例如，如果您的 Vm 使用資源庫映射。

> [!IMPORTANT]
> 如果您使用的是需要很長時間才能穩定或有很多使用者的 advanced 設定，我們不建議您手動進行遷移。

## <a name="prepare-for-migration"></a>為移轉做準備

開始之前，您必須確定您的環境已準備好遷移。

以下是開始遷移程式所需的內容：

- Azure 訂用帳戶，您將在其中建立新的 Azure 服務物件。
- 請確定您已指派給下列角色：
    
    - 參與者
    - 使用者存取系統管理員
    
    「參與者」角色可讓您在訂用帳戶上建立 Azure 物件，而「使用者存取系統管理員」角色可讓您將使用者指派給應用程式群組。

## <a name="how-to-migrate-manually"></a>如何手動遷移

現在您已準備好進行遷移程式，接下來就是實際遷移的時候了。

若要從 Windows 虛擬桌面手動遷移 (傳統) 至 Windows 虛擬桌面：

1. 遵循 [使用 Azure 入口網站建立主機集](create-host-pools-azure-marketplace.md) 區中的指示，以 Azure 入口網站建立所有高階物件。
2. 如果您想要使用已在使用中的虛擬機器，請依照向 [Windows 虛擬桌面主機集區註冊虛擬機器](create-host-pools-powershell.md#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool) 中的指示，手動將它們註冊到您在步驟1中建立的新主機集區。
3. 建立新的 RemoteApp 應用程式群組。
4. 將使用者或使用者群組發佈到新的桌面和 RemoteApp 應用程式群組。
5. 依照 [設定多重要素驗證](set-up-mfa.md)中的指示，更新您的條件式存取原則，以允許新的物件。

為了避免停機，您應該先將現有的工作階段主機註冊至小型群組中 Azure Resource Manager 整合的主機集區。 之後，將您的使用者慢慢帶到新的 Azure Resource Manager 整合式應用程式群組。

## <a name="next-steps"></a>後續步驟

遷移之後，請查看 [我們的教學](create-host-pools-azure-marketplace.md)課程，瞭解 Windows 虛擬桌面的運作方式。 深入瞭解如何 [展開現有的主機集](expand-existing-host-pool.md) 區和 [自訂 RDP 屬性](customize-rdp-properties.md)，以進行先進的管理功能。

若要深入瞭解服務物件，請參閱 [Windows 虛擬桌面環境](environment-setup.md)。
