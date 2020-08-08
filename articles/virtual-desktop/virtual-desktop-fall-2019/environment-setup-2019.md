---
title: Windows 虛擬桌面 (傳統) 環境-Azure
description: Windows 虛擬桌面 (傳統) 環境的基本元素。
author: Heidilohr
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 4fa281f6435013e9feb6808aab04ffa69c5f96c4
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2020
ms.locfileid: "88008605"
---
# <a name="windows-virtual-desktop-classic-environment"></a>Windows 虛擬桌面 (傳統) 環境

>[!IMPORTANT]
>此內容適用於不支援 Azure Resource Manager Windows 虛擬桌面物件的 Windows 虛擬桌面 (傳統)。 如果您嘗試管理 Azure Resource Manager Windows 虛擬桌面物件，請參閱[這篇文章](../environment-setup.md)。

Windows 虛擬桌面是一種服務，可讓使用者輕鬆且安全地存取其虛擬化的桌面和 Remoteapp。 本主題將告訴您更多有關 Windows 虛擬桌面環境的一般結構。

## <a name="tenants"></a>租用戶

Windows 虛擬桌面租使用者是用來管理 Windows 虛擬桌面環境的主要介面。 每個 Windows 虛擬桌面的租使用者都必須與包含將登入環境之使用者的 Azure Active Directory 相關聯。 從 Windows 虛擬桌面租使用者中，您可以開始建立主機集區，以執行使用者的工作負載。

## <a name="host-pools"></a>主機集區

主機集區是 Azure 虛擬機器的集合，會在您執行 Windows 虛擬桌面代理程式時，註冊 Windows 虛擬桌面做為工作階段主機。 主機集區中的所有工作階段主機虛擬機器都應該來自相同的映射，以提供一致的使用者體驗。

主機集區可以是下列兩種類型的其中一種：

- Personal，其中每個工作階段主機都會指派給個別使用者。
- 集區，其中工作階段主機可以接受來自主機集區內應用程式群組授權的任何使用者的連接。

您可以在主機集區上設定其他內容，以變更其負載平衡行為、每個工作階段主機可以接受的會話數目，以及使用者在登入其 Windows 虛擬桌面會話時，可以對主機集區中的主機執行哪些動作。 您可以透過應用程式群組控制發佈給使用者的資源。

## <a name="app-groups"></a>應用程式群組

應用程式群組是主機集區中工作階段主機上所安裝應用程式的邏輯群組。 應用程式群組可以是下列兩種類型之一：

- RemoteApp，使用者可在其中存取您個別選取併發布至應用程式群組的 Remoteapp
- 桌面，使用者在其中存取完整桌面

根據預設，每當您建立主機集區時，就會自動建立名為「桌面應用程式群組」 ) 的桌面應用程式群組 (。 您可以隨時移除此應用程式群組。 不過，當桌面應用程式群組存在時，您無法在主機集區中建立另一個桌面應用程式群組。 若要發佈 Remoteapp，您必須建立 RemoteApp 應用程式群組。 您可以建立多個 RemoteApp 應用程式群組來配合不同的背景工作案例。 不同的 RemoteApp 應用程式群組也可以包含重迭的 Remoteapp。

若要將資源發佈給使用者，您必須將它們指派給應用程式群組。 將使用者指派給應用程式群組時，請考慮下列事項：

- 無法將使用者指派至相同主機集區中的桌面應用程式群組和 RemoteApp 應用程式群組。
- 使用者可以指派給相同主機集區中的多個應用程式群組，而其摘要會累積這兩個應用程式群組。

## <a name="tenant-groups"></a>租使用者群組

在 Windows 虛擬桌面中，Windows 虛擬桌面租使用者是大部分的安裝和設定發生所在的位置。 Windows 虛擬桌面租使用者包含主機集區、應用程式群組和應用程式群組使用者指派。 不過，在某些情況下，您可能需要一次管理多個 Windows 虛擬桌面的租使用者，特別是如果您是雲端服務提供者 (CSP) 或主控夥伴。 在這些情況下，您可以使用自訂的 Windows 虛擬桌面租使用者群組，將每個客戶的 Windows 虛擬桌面租使用者，集中管理存取權。 不過，如果您只是管理單一 Windows 虛擬桌面租使用者，則不會套用租使用者群組概念，而且您可以繼續操作及管理存在於預設租使用者群組中的租使用者。

## <a name="end-users"></a>使用者

將使用者指派給其應用程式群組之後，他們就可以使用任何 Windows 虛擬桌面用戶端連線到 Windows 虛擬桌面部署。

## <a name="next-steps"></a>後續步驟

深入瞭解委派的存取權，以及如何[在 Windows 虛擬桌面的委派存取權中](delegated-access-virtual-desktop-2019.md)將角色指派給使用者。

若要瞭解如何設定您的 Windows 虛擬桌面租使用者，請參閱[在 Windows 虛擬桌面中建立租](tenant-setup-azure-active-directory.md)使用者。

若要瞭解如何連接到 Windows 虛擬桌面，請參閱下列其中一篇文章：

- [從 Windows 10 或 Windows 7 連線](connect-windows-7-10-2019.md)
- [從網頁瀏覽器連線](connect-web-2019.md)
