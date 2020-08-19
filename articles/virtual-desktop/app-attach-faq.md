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
ms.openlocfilehash: a63d7f067665836910b91b2911db522f0a92bbb1
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2020
ms.locfileid: "88556234"
---
# <a name="msix-app-attach-faq"></a>MSIX 應用程式附加常見問題

本文將回答有關 MSIX Windows 虛擬桌面的應用程式附加的常見問題。

## <a name="does-msix-app-attach-use-fslogix"></a>MSIX 應用程式附加使用 FSLogix 嗎？

MSIX 應用程式附加不會使用 FSLogix。 不過，應用程式附加和 FSLogix 是設計來一起運作，以提供順暢的使用者體驗。

## <a name="can-i-use-msix-app-attach-outside-of-windows-virtual-desktop"></a>我可以使用 Windows 虛擬桌面以外的 MSIX 應用程式附加嗎？

是的，MSIX app attach 是 Windows 10 企業版隨附的功能，可在 Windows 虛擬桌面之外使用。 但是，不會有管理平面可將 MSIX 應用程式附加至 Windows 虛擬桌面以外的地方。

## <a name="how-do-i-get-an-msix-package"></a>如何? 取得 MSIX 套件？

您的軟體廠商會提供 MSIX 套件。 您也可以將非 MSIX 封裝轉換成 MSIX。 深入瞭解 [如何將現有的安裝程式移至 MSIX](/windows/msix/packaging-tool/create-an-msix-overview#how-to-move-your-existing-installers-to-msix)。

## <a name="which-operating-systems-support-msix-app-attach"></a>哪些作業系統支援 MSIX app attach？

Windows 10 企業版和 Windows 10 企業版多會話。

## <a name="next-steps"></a>接下來的步驟

如果您想要深入瞭解 MSIX app 附加，請參閱我們的[總覽](what-is-app-attach.md)[詞彙](app-attach-glossary.md)。 否則，請開始 [設定應用程式連接](app-attach.md)。