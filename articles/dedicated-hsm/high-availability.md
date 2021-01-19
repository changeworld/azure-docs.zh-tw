---
title: 高可用性 - Azure 專用 HSM | Microsoft Docs
description: 瞭解 Azure 專用 HSM 高可用性的基本考慮。 本文包含範例。
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/15/2021
ms.author: mbaldwin
ms.openlocfilehash: 85aa9d355b2440535ea13c8c15e95500bac22352
ms.sourcegitcommit: ca215fa220b924f19f56513fc810c8c728dff420
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/19/2021
ms.locfileid: "98567253"
---
# <a name="azure-dedicated-hsm-high-availability"></a>Azure 專用 HSM 高可用性

Azure 專用 HSM 的基礎是由 Microsoft 高可用性資料中心所構成。 但是，任何高可用性的資料中心都很容易受到當地故障的影響，而且在極端情況下，可能會出現區域層級的故障。 Microsoft 會將 HSM 裝置部署到同個區域內的不同資料中心，以確保不會因為佈建了多個裝置，而導致裝置共用到單一機架。 您可以使用 Thales HA 群組功能，將這些 Hsm 與區域中的資料中心配對，以達成更高的高可用性層級。 也可以跨區域配對裝置，以便在需要災害復原的情況下，解決區域容錯移轉問題。 使用這種多層高可用性設定，即可自動應對任何裝置故障情形，保持應用程式正常運作。 所有資料中心在現場也會備有備用裝置和元件，因此可以及時更換任何故障的裝置。

## <a name="high-availability-example"></a>高可用性範例

如需有關如何在軟體層級設定 HSM 裝置以獲得高可用性的資訊，請參閱「Thales Luna 7 HSM 系統管理指南」。 您可以在  [THALES HSM 頁面](https://thalesdocs.com/gphsm/Content/luna/network/luna_network_releases.htm)上取得這份檔。

下圖顯示的是一個高可用性的架構。 此架構在同個區域中使用了多台裝置，且在獨立區域中有多台配對裝置。 此架構最少使用了四個 HSM 裝置和虛擬網路元件。

![高可用性圖表](media/high-availability/high-availability.png)

## <a name="next-steps"></a>後續步驟

建議在裝置佈建和應用程式設計或部署之前，先充分了解服務的所有重要概念，例如高可用性和安全性。
更多概念層級的主題：

* [部署架構](deployment-architecture.md)
* [實體安全性](physical-security.md)
* [網路功能](networking.md)
* [支援能力](supportability.md)
* [監視](monitoring.md)

如需有關為 HSM 裝置設定高可用性的特定詳細資訊，請參閱 Thales 客戶支援入口網站以取得系統管理員指南，請參閱第6節。
