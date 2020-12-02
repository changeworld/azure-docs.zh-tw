---
title: Windows 虛擬桌面 MSIX 應用程式附加常見問題-Azure
description: 適用于 Windows 虛擬桌面的 MSIX 應用程式附加的常見問題。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 73fb9bf436c043e903977fafbb5a502e2edc5488
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96518680"
---
# <a name="msix-app-attach-faq"></a>MSIX 應用程式附加常見問題

本文將回答有關 MSIX Windows 虛擬桌面的應用程式附加的常見問題。

## <a name="whats-the-difference-between-msix-and-msix-app-attach"></a>MSIX 和 MSIX 應用程式附加有何不同？

MSIX 是適用于應用程式的封裝格式，而 MSIX 應用程式附加則是將 MSIX 套件傳遞給您的部署的功能。

## <a name="does-msix-app-attach-use-fslogix"></a>MSIX 應用程式附加使用 FSLogix 嗎？

MSIX 應用程式附加不會使用 FSLogix。 不過，MSIX 應用程式附加和 FSLogix 是設計來一起運作，以提供順暢的使用者體驗。

## <a name="can-i-use-msix-app-attach-outside-of-windows-virtual-desktop"></a>我可以使用 Windows 虛擬桌面以外的 MSIX 應用程式附加嗎？

Power MSIX app 附加的 Api 可用於 Windows 10 企業版。 這些 Api 可以在 Windows 虛擬桌面之外使用。 但是，不會有管理平面可將 MSIX 應用程式附加至 Windows 虛擬桌面以外的地方。

## <a name="how-do-i-get-an-msix-package"></a>如何? 取得 MSIX 套件？

您的軟體廠商會提供 MSIX 套件。 您也可以將非 MSIX 封裝轉換成 MSIX。 深入瞭解 [如何將現有的安裝程式移至 MSIX](/windows/msix/packaging-tool/create-an-msix-overview#how-to-move-your-existing-installers-to-msix)。

## <a name="which-operating-systems-support-msix-app-attach"></a>哪些作業系統支援 MSIX app attach？

Windows 10 企業版和 Windows 10 企業版多會話2004版或更新版本。

## <a name="is-msix-app-attach-currently-generally-available"></a>MSIX 應用程式附加目前是否已正式推出？

MSIX 應用程式附加是 Windows 10 企業版的一部分，Windows 10 企業版多會話2004版或更新版本。 這兩種作業系統目前已正式推出。 

## <a name="can-i-use-msix-app-attach-outside-of-windows-virtual-desktop"></a>我可以使用 Windows 虛擬桌面以外的 MSIX 應用程式附加嗎？

MSIX 和 MSIX app 附加 Api 是 Windows 10 企業版的一部分，且 Windows 10 企業版多會話2004版和更新版本。 我們目前未提供管理軟體，可在 Windows 虛擬桌面以外 MSIX 應用程式附加。

## <a name="can-i-run-two-versions-of-the-same-application-at-the-same-time"></a>我可以同時執行相同應用程式的兩個版本嗎？

若要同時執行兩個相同 MSIX 應用程式的版本，每個應用程式的 appxmanifest.xml 檔案中所定義的 MSIX 套件系列都必須不同。

## <a name="should-i-disable-auto-update-when-using-msix-app-attach"></a>使用 MSIX 應用程式附加時，是否應該停用自動更新？

是。 MSIX 應用程式附加不支援自動更新 MSIX 應用程式。

## <a name="how-do-permissions-work-with-msix-app-attach"></a>MSIX 應用程式附加的許可權如何運作？

在使用 MSIX 應用程式附加的主機集區中，所有 (Vm) 的虛擬機器，都必須具有儲存 MSIX 映射之檔案共用的讀取權限。 如果它也使用 Azure 檔案儲存體，則必須將角色型存取控制授與 (RBAC) 和新技術檔案系統 (NTFS) 許可權。

## <a name="can-i-use-msix-app-attach-for-http-or-https"></a>我可以將 MSIX 應用程式附加用於 HTTP 或 HTTPs 嗎？

屬於使用 MSIX 應用程式附加之主機集區一部分的所有 Vm 都必須具有儲存 MSIX 映射之檔案共用的讀取權限。 如果同時使用 Azure 檔案儲存體，則必須授與 RBAC 和 NTFS 許可權。

## <a name="can-i-restage-the-same-msix-application"></a>我可以重新暫存相同的 MSIX 應用程式嗎？

是。 您可以重新暫存已重新暫存的應用程式，這不會造成任何錯誤。

## <a name="does-msix-app-attach-support-self-signed-certificates"></a>MSIX 應用程式附加是否支援自我簽署憑證？

目前不支援使用 MSIX 應用程式透過 HTTP 或 HTTPs 連接。


## <a name="next-steps"></a>後續步驟

如果您想要深入瞭解 MSIX app 附加，請參閱我們的 [總覽](what-is-app-attach.md) 和 [詞彙](app-attach-glossary.md)。 否則，請開始 [設定應用程式連接](app-attach.md)。
