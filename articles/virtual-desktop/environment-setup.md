---
title: Windows 虛擬桌面環境-Azure
description: 深入瞭解 Windows 虛擬桌面環境的基本元素，例如主機集區和應用程式群組。
author: Heidilohr
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 99a07dd1791b539ea44fcbab250aa9c227ee1705
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88002611"
---
# <a name="windows-virtual-desktop-environment"></a>Windows 虛擬桌面環境

>[!IMPORTANT]
>此內容適用於具有 Azure Resource Manager Windows 虛擬桌面物件的 Windows 虛擬桌面。 如果您使用不含 Azure Resource Manager 物件的 Windows 虛擬桌面 (傳統版)，請參閱[這篇文章](./virtual-desktop-fall-2019/environment-setup-2019.md)。

Windows 虛擬桌面是一項服務，可讓使用者輕鬆且安全地存取其虛擬化桌面和 Remoteapp。 本主題將告訴您更多有關 Windows 虛擬桌面環境一般結構的資訊。

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

- 您可以將使用者指派給相同主機集區中的桌面應用程式群組和 RemoteApp 應用程式群組。 不過，使用者只能針對每個會話啟動一個類型的應用程式群組。 使用者無法在單一會話中同時啟動這兩種類型的應用程式群組。
- 您可以將使用者指派給相同主機集區內的多個應用程式群組，其摘要將會累積兩個應用程式群組。

## <a name="workspaces"></a>工作區

工作區是 Windows 虛擬桌面中的應用程式群組邏輯群組。 每個 Windows 虛擬桌面應用程式群組都必須與工作區相關聯，才能讓使用者看到遠端應用程式和桌上型電腦發佈給他們。

## <a name="end-users"></a>使用者

當您將使用者指派給其應用程式群組之後，他們就可以使用任何 Windows 虛擬桌面用戶端連接到 Windows 虛擬桌面部署。

## <a name="next-steps"></a>後續步驟

深入瞭解委派存取權，以及如何將角色指派給 [Windows 虛擬桌面中委派存取權](delegated-access-virtual-desktop.md)的使用者。

若要瞭解如何設定您的 Windows 虛擬桌面主機集區，請參閱 [使用 Azure 入口網站建立主機集](create-host-pools-azure-marketplace.md)區。

若要瞭解如何連線到 Windows 虛擬桌面，請參閱下列其中一篇文章：

- [使用 Windows 10 或 Windows 7 連接](connect-windows-7-10.md)
- [使用網頁瀏覽器連接](connect-web.md)
- [與 Android 用戶端連線](connect-android.md)
- [與 macOS 用戶端連線](connect-macos.md)
- [與 iOS 用戶端連線](connect-ios.md)