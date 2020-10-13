---
title: Windows 虛擬桌面 MSIX 應用程式附加總覽-Azure
description: 什麼是 MSIX 應用程式附加？ 請在本文中瞭解。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: c6bf296b5173a662b1e9dd7b025648e3f16d23c8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88556238"
---
# <a name="what-is-msix-app-attach"></a>什麼是 MSIX 應用程式附加？

MSIX 是一種新的封裝格式，提供許多旨在改善所有 Windows 應用程式封裝體驗的功能。 若要深入瞭解 MSIX，請參閱 [MSIX 總覽](/windows/msix/overview)。

MSIX 應用程式附加是一種將 MSIX 應用程式傳遞至實體和虛擬機器的方式。 不過，MSIX app 附加與一般 MSIX 不同，因為它特別適用于 Windows 虛擬桌面。 本文將說明 MSIX 應用程式的附加專案，以及它可以為您做什麼。

## <a name="application-delivery-options-in-windows-virtual-desktop"></a>Windows 虛擬桌面中的應用程式傳遞選項

您可以透過下列其中一種方法，在 Windows 虛擬桌面提供應用程式：

- 將應用程式放在主要映射中
- 使用 SCCM 或 Intune 等工具進行集中管理
- 動態應用程式布建 (AppV、VMWare AppVolumes 或 Citrix AppLayering) 
- 使用 Microsoft 和協力廠商工具建立自訂工具或腳本

## <a name="what-does-msix-app-attach-do"></a>MSIX app 附加的功能為何？

在 Windows 虛擬桌面部署中，MSIX 應用程式附加可以：

- 使用 [MSIX 容器](/windows/msix/msix-container)來建立使用者資料、作業系統和應用程式之間的分隔。
- 當您以動態方式傳遞應用程式時，請移除重新封裝的需求。
- 縮短使用者登入所需的時間。
- 降低基礎結構需求和成本。

MSIX 應用程式附加必須適用于 VDI 或 SBC 以外的範圍。

## <a name="traditional-app-layering-compared-to-msix-app-attach"></a>相較于 MSIX 應用程式附加的傳統應用程式分層

下表比較 MSIX app 附加和應用程式階層式主要功能。

| 功能 | 傳統的應用程式分層  | MSIX 應用程式附加  |
|-----|-----------------------------|--------------------|
| [格式]               | 不同的應用程式分層技術需要不同的專屬格式。 | 適用于原生 MSIX 封裝格式。        |
| 重新包裝額外負荷 | 專屬格式需要針對每個更新進行排序和重新封裝。         | 發佈為 MSIX 的應用程式不需要重新封裝。 但是，如果無法使用 MSIX 套件，則仍會套用重新封裝額外負荷。 |
| 生態系統            | N/A (例如，廠商未隨附 App-v)   | MSIX 是 Microsoft 的主要技術，可供主要 ISV 合作夥伴和內部應用程式（例如 Office）採用。 您可以在虛擬桌面電腦和實體 Windows 電腦上使用 MSIX。 |
| 基礎結構       | 需要額外的基礎結構 (伺服器、用戶端等等)  | 僅限儲存體   |
| 系統管理       | 需要維護和更新   | 簡化應用程式更新 |
| 使用者體驗      | 影響使用者登入時間。 界限存在於作業系統狀態、應用程式狀態和使用者資料之間。  | 提供的應用程式與本機安裝的應用程式不區分。 |

## <a name="next-steps"></a>後續步驟

如果您想要深入瞭解 MSIX app 附加，請參閱我們的 [詞彙](app-attach-glossary.md) 和 [常見問題](app-attach-faq.md)。 否則，請開始 [設定應用程式連接](app-attach.md)。
