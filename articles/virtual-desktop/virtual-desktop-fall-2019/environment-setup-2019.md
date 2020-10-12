---
title: Windows 虛擬桌面 (傳統) 環境-Azure
description: Windows 虛擬桌面的基本元素 (傳統) 環境。
author: Heidilohr
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 4fa281f6435013e9feb6808aab04ffa69c5f96c4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88008605"
---
# <a name="windows-virtual-desktop-classic-environment"></a>Windows 虛擬桌面 (傳統) 環境

>[!IMPORTANT]
>此內容適用於不支援 Azure Resource Manager Windows 虛擬桌面物件的 Windows 虛擬桌面 (傳統)。 如果您嘗試管理 Azure Resource Manager Windows 虛擬桌面物件，請參閱[這篇文章](../environment-setup.md)。

Windows 虛擬桌面是一項服務，可讓使用者輕鬆且安全地存取其虛擬化桌面和 Remoteapp。 本主題將告訴您更多有關 Windows 虛擬桌面環境一般結構的資訊。

## <a name="tenants"></a>租用戶

Windows 虛擬桌面租使用者是用來管理您的 Windows 虛擬桌面環境的主要介面。 每個 Windows 虛擬桌面租使用者都必須與包含將登入環境之使用者的 Azure Active Directory 相關聯。 您可以從 Windows 虛擬桌面租使用者開始建立主機集區，以執行使用者的工作負載。

## <a name="host-pools"></a>主機集區

主機集區是一組 Azure 虛擬機器，當您執行 Windows 虛擬桌面代理程式時，會以工作階段主機的形式註冊至 Windows 虛擬桌面。 主機集區中的所有工作階段主機虛擬機器都應源自相同的映射，以提供一致的使用者體驗。

主機集區可以是下列兩種類型之一：

- 個人，將每個工作階段主機指派給個別使用者。
- 集區，其中的工作階段主機可以接受從任何授權給主機集區中的應用程式群組之使用者的連接。

您可以設定主機集區上的其他內容，以變更其負載平衡行為、每個工作階段主機可以執行的會話數目，以及使用者可以在登入 Windows 虛擬桌面會話時，對主機集區中的主機進行會話的作業。 您可以透過應用程式群組來控制發佈給使用者的資源。

## <a name="app-groups"></a>應用程式群組

應用程式群組是在主機集區的工作階段主機上安裝之應用程式的邏輯群組。 應用程式群組可以是下列兩種類型之一：

- RemoteApp，讓使用者存取您個別選取的 Remoteapp 併發布至應用程式群組
- 桌上型電腦，讓使用者存取完整桌面

根據預設，每當您建立主機集區時，就會自動建立名為「桌面應用程式群組」 () 的桌面應用程式群組。 您可以隨時移除此應用程式群組。 不過，當桌面應用程式群組存在時，您無法在主機集區中建立另一個桌面應用程式群組。 若要發佈 Remoteapp，您必須建立 RemoteApp 應用程式群組。 您可以建立多個 RemoteApp 應用程式群組來容納不同的背景工作案例。 不同的 RemoteApp 應用程式群組也可以包含重迭的 Remoteapp。

若要將資源發佈給使用者，您必須將資源指派給應用程式群組。 將使用者指派給應用程式群組時，請考慮下列事項：

- 無法將使用者指派給相同主機集區中的桌面應用程式群組和 RemoteApp 應用程式群組。
- 您可以將使用者指派給相同主機集區內的多個應用程式群組，其摘要將會累積兩個應用程式群組。

## <a name="tenant-groups"></a>租使用者群組

在 Windows 虛擬桌面中，Windows 虛擬桌面租使用者是大部分的設定和設定的發生位置。 Windows 虛擬桌面租使用者包含主機集區、應用程式群組和應用程式群組使用者指派。 不過，在某些情況下，您可能需要一次管理多個 Windows 虛擬桌面租使用者，特別是如果您是雲端服務提供者 (CSP) 或主控夥伴。 在這些情況下，您可以使用自訂的 Windows 虛擬桌面租使用者群組來放置每個客戶的 Windows 虛擬桌面租使用者，並集中管理存取權。 不過，如果您只管理單一 Windows 虛擬桌面租使用者，則不會套用租使用者群組概念，而且您可以繼續操作並管理存在於預設租使用者群組中的租使用者。

## <a name="end-users"></a>使用者

當您將使用者指派給其應用程式群組之後，他們就可以使用任何 Windows 虛擬桌面用戶端連接到 Windows 虛擬桌面部署。

## <a name="next-steps"></a>後續步驟

深入瞭解委派存取權，以及如何將角色指派給 [Windows 虛擬桌面中委派存取權](delegated-access-virtual-desktop-2019.md)的使用者。

若要瞭解如何設定您的 Windows 虛擬桌面租使用者，請參閱 [在 Windows 虛擬桌面中建立租](tenant-setup-azure-active-directory.md)使用者。

若要瞭解如何連線到 Windows 虛擬桌面，請參閱下列其中一篇文章：

- [從 Windows 10 或 Windows 7 連線](connect-windows-7-10-2019.md)
- [從網頁瀏覽器連線](connect-web-2019.md)
