---
title: Windows 虛擬桌面環境 - Azure
description: Windows 虛擬桌面環境的基本元素。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 33d058f028b7032f296ffcf82f0e5fe2c993e6fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127912"
---
# <a name="windows-virtual-desktop-environment"></a>Windows 虛擬桌面環境

Windows 虛擬桌面是一項服務，使使用者能夠輕鬆安全地訪問其虛擬化桌面和遠端應用。 本主題將告訴您有關 Windows 虛擬桌面環境的一般結構的更多資訊。

## <a name="tenants"></a>租用戶

Windows 虛擬桌面租戶是管理 Windows 虛擬桌面環境的主要介面。 每個 Windows 虛擬桌面租戶都必須與 Azure 活動目錄相關聯，該目錄包含將登錄到環境的使用者。 從 Windows 虛擬桌面租戶中，您可以開始創建主機池以運行使用者的工作負載。

## <a name="host-pools"></a>主機池

主機池是 Azure 虛擬機器的集合，在運行 Windows 虛擬桌面代理時，這些虛擬機器會註冊到 Windows 虛擬桌面作為工作階段主機。 主機池中的所有工作階段主機虛擬機器都應來自同一映射，以便獲得一致的使用者體驗。

主機池可以是兩種類型之一：

- 個人，其中每個工作階段主機分配給單個使用者。
- 池，其中工作階段主機可以接受從任何使用者授權到主機池中的應用組的連接。

您可以在主機池上設置其他屬性，以更改其負載平衡行為、每個工作階段主機可以執行多少會話，以及使用者可以在登錄到其 Windows 虛擬桌面會話時對主機池中的工作階段主機執行哪些操作。 通過應用組控制發佈到使用者的資源。

## <a name="app-groups"></a>應用程式群組

應用組是安裝在主機池中的工作階段主機上的應用程式的邏輯分組。 應用組可以是兩種類型之一：

- 遠端應用，使用者訪問您單獨選擇的遠端應用並將其發佈到應用組
- 桌面，使用者訪問整個桌面

預設情況下，每當創建主機池時，都會自動創建桌面應用組（名為"桌面應用程式組"）。 您可以隨時刪除此應用組。 但是，當桌面應用組存在時，無法在主機池中創建另一個桌面應用組。 要發佈遠端應用，必須創建遠端應用應用組。 您可以創建多個 RemoteApp 應用組，以適應不同的輔助方案。 不同的 RemoteApp 應用組還可以包含重疊的遠端應用。

要向使用者發佈資源，必須將它們分配給應用組。 將使用者分配給應用組時，請考慮以下事項：

- 無法將使用者分配給同一主機池中的桌面應用組和 RemoteApp 應用組。
- 可以將使用者分配給同一主機池中的多個應用組，並且其源將是兩個應用組的累積。

## <a name="tenant-groups"></a>租戶組

在 Windows 虛擬桌面中，Windows 虛擬桌面租戶是大多數設置和配置發生的位置。 Windows 虛擬桌面租戶包含主機池、應用組和應用組使用者分配。 但是，在某些情況下，可能需要同時管理多個 Windows 虛擬桌面租戶，尤其是在您是雲服務提供者 （CSP） 或託管合作夥伴的情況下。 在這些情況下，可以使用自訂 Windows 虛擬桌面租戶組放置每個客戶的 Windows 虛擬桌面租戶並集中管理存取權限。 但是，如果您只管理單個 Windows 虛擬桌面租戶，則租戶組概念不適用，您可以繼續操作和管理預設租戶組中存在的租戶。

## <a name="end-users"></a>使用者

將使用者分配到其應用組後，他們可以使用任何 Windows 虛擬桌面用戶端連接到 Windows 虛擬桌面部署。

## <a name="next-steps"></a>後續步驟

詳細瞭解委派訪問以及如何[在 Windows 虛擬桌面中委派訪問](delegated-access-virtual-desktop.md)的使用者分配角色。

要瞭解如何設置 Windows 虛擬桌面租戶，請參閱在[Windows 虛擬桌面中創建租戶](tenant-setup-azure-active-directory.md)。

要瞭解如何連接到 Windows 虛擬桌面，請參閱以下文章之一：

- [從 Windows 10 或 Windows 7 連線](connect-windows-7-and-10.md)
- [從網頁瀏覽器連線](connect-web.md)
