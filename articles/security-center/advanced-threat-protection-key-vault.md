---
title: Azure 金鑰保存庫的威脅保護
description: 本文介紹如何在 Azure 安全中心中為 Azure 金鑰保存庫設置高級威脅保護
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 449096590df6145c9f80dcf2c97726931909a2ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77914801"
---
# <a name="threat-protection-for-azure-key-vault-preview"></a>Azure 金鑰保存庫的威脅保護（預覽版）

Azure 金鑰保存庫的高級威脅保護提供了額外的安全智慧層。 此工具可檢測訪問或利用金鑰保存庫帳戶的潛在有害嘗試。 在 Azure 安全中心中使用本機高級威脅保護，無需成為安全專家即可應對威脅，無需學習其他安全監控系統。

當安全中心檢測到異常活動時，將顯示警報。 它還向訂閱管理員發送電子郵件，提供可疑活動的詳細資訊以及如何調查和修復已識別的威脅的建議。

## <a name="configuring-threat-protection-from-security-center"></a>配置安全中心的威脅防護

預設情況下，當您訂閱安全中心的標準定價層時，為您的所有金鑰保存庫帳戶啟用高級威脅保護。 如需詳細資訊，請參閱[定價](security-center-pricing.md)。

要啟用或禁用特定訂閱的保護，請：

1. 從安全中心中的左側窗格中，選擇**定價&設置**。

1. 選擇要為其啟用或禁用威脅保護的存儲帳戶的訂閱。

1. 選擇**定價層**。

1. 從 **"按資源類型組選擇定價層**"中，**查找"金鑰保存庫"** 行並選擇 **"已啟用**"或 **"已禁用**"。

    [![在 Azure 安全中心為金鑰保存庫啟用或禁用高級威脅保護](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png)](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png#lightbox)

1. 選取 [儲存]****。


## <a name="next-steps"></a>後續步驟

在本文中，您將瞭解如何為 Azure 金鑰保存庫啟用和禁用高級威脅保護。 

有關相關材料，請參閱以下文章：

- [Azure 安全中心中的威脅保護](threat-protection.md)-- 本文介紹 Azure 安全中心中的安全警報源。
- [金鑰保存庫安全警報](alerts-reference.md#alerts-azurekv)-- 所有 Azure 安全中心警報的參考表的金鑰保存庫部分