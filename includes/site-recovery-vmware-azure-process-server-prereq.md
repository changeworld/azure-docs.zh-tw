---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 11/28/2019
ms.author: raynew
ms.openlocfilehash: de15e3028cf22cdd03ce29385278fc5e2babaa9b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "74566227"
---
本文假設：

1. 已經建立內部部署網路與 Azure 虛擬網路之間的**站對站 VPN** 或 **Express Route** 連線。
2. 您的使用者帳戶有權限在虛擬機器容錯移轉至的 Azure 訂用帳戶中建立新的虛擬機器。
3. 您的訂用帳戶至少有8個核心可用來加速新的進程伺服器虛擬機器。
4. 您有**設定伺服器複雜密碼**可用。

> [!TIP]
> 確保您能夠從虛擬機器已容錯移轉至的 Azure 虛擬網路，連接設定伺服器 (在內部部署執行) 的連接埠 443。
