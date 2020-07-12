---
title: Windows 虛擬桌面環境-Azure
description: Windows 虛擬桌面環境的基本元素。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: d189d5febe54a83cde4fe6cd6aa51e728aa882e6
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2020
ms.locfileid: "86259892"
---
# <a name="windows-virtual-desktop-environment"></a>Windows 虛擬桌面環境

>[!IMPORTANT]
>此內容適用於具有 Azure Resource Manager Windows 虛擬桌面物件的 2020 年春季更新版。 如果您使用不含 Azure Resource Manager 物件的 Windows 虛擬桌面 2019 年秋季版，請參閱[這篇文章](./virtual-desktop-fall-2019/environment-setup-2019.md)。
>
> Windows 虛擬桌面 2020 年春季更新版目前為公開預覽狀態。 此預覽版本是在沒有服務等級協定的情況下提供，不建議您將其用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Windows 虛擬桌面是一種服務，可讓使用者輕鬆且安全地存取其虛擬化的桌面和 Remoteapp。 本主題將告訴您更多有關 Windows 虛擬桌面環境的一般結構。

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

- 使用者可以同時指派至相同主機集區中的桌面應用程式群組和 RemoteApp 應用程式群組。 不過，使用者只能在每個會話啟動一種類型的應用程式群組。 使用者無法同時在單一會話中啟動這兩種類型的應用程式群組。
- 使用者可以指派給相同主機集區中的多個應用程式群組，而其摘要會累積這兩個應用程式群組。

## <a name="workspaces"></a>工作區

工作區是 Windows 虛擬桌面中應用程式群組的邏輯群組。 每個 Windows 虛擬桌面應用程式群組都必須與工作區相關聯，使用者才能看到發佈給他們的遠端應用程式和桌上型電腦。  

## <a name="end-users"></a>使用者

將使用者指派給其應用程式群組之後，他們就可以使用任何 Windows 虛擬桌面用戶端連線到 Windows 虛擬桌面部署。

## <a name="next-steps"></a>後續步驟

深入瞭解委派的存取權，以及如何[在 Windows 虛擬桌面的委派存取權中](delegated-access-virtual-desktop.md)將角色指派給使用者。

若要瞭解如何設定您的 Windows 虛擬桌面主機集區，請參閱[使用 Azure 入口網站建立主機](create-host-pools-azure-marketplace.md)集區。

若要瞭解如何連接到 Windows 虛擬桌面，請參閱下列其中一篇文章：

- [與 Windows 10 或 Windows 7 連接](connect-windows-7-10.md)
- [使用網頁瀏覽器連接](connect-web.md)
- [與 Android 用戶端連線](connect-android.md)
- [與 macOS 用戶端連線](connect-macos.md)
- [與 iOS 用戶端連線](connect-ios.md)