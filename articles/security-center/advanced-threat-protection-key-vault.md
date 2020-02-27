---
title: 設定 Azure Key Vault 的進階威脅防護
description: 本文說明如何在 Azure 資訊安全中心中設定 Azure Key Vault 的先進威脅防護
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 3ca3026a259e7f23bbfa01398a96cbdbfae1321c
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/26/2020
ms.locfileid: "77623990"
---
# <a name="threat-protection-for-azure-key-vault-preview"></a>Azure Key Vault 的威脅防護（預覽）

Azure Key Vault 的先進威脅防護提供一層額外的安全性情報。 此工具會偵測可能有害的嘗試存取或惡意探索 Key Vault 帳戶。 在 Azure 資訊安全中心中使用原生先進的威脅防護，可以解決威脅，而不是安全性專家，也不需要學習額外的安全性監視系統。

當資訊安全中心偵測到異常活動時，它會顯示警示。 它也會以可疑活動的詳細資料，以及如何調查並補救已識別威脅的建議，以電子郵件寄給訂用帳戶管理員。

## <a name="configuring-threat-protection-from-security-center"></a>從資訊安全中心設定威脅防護

根據預設，當您訂閱資訊安全中心的標準定價層時，系統會為您的所有 Key Vault 帳戶啟用 [advanced 威脅防護]。 如需詳細資訊，請參閱[定價](security-center-pricing.md)。

若要啟用或停用特定訂用帳戶的保護：

1. 從資訊安全中心的左窗格中，選取 **定價 & 設定**。

1. 選取您想要啟用或停用威脅防護之儲存體帳戶的訂用帳戶。

1. 選取 [定價層]。

1. 從 [**依資源類型選取定價層**] 群組，尋找 [**金鑰保存庫**] 資料列，然後選取 [**啟用**] 或 [**已停用**]

    [![在 Azure 資訊安全中心中啟用或停用 Key Vault 的 advanced 威脅防護](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png)](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png#lightbox)

1. 選取 [儲存]。


## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解如何啟用和停用 Azure Key Vault 的 [先進的威脅防護]。 

如需相關內容，請參閱下列文章：

- [Azure 資訊安全中心中的威脅防護](threat-protection.md)--這篇文章說明 Azure 資訊安全中心中的安全性警示來源。
- [Key Vault 安全性警示](alerts-reference.md#alerts-azurekv)--所有 Azure 資訊安全中心警示之參考資料表的 Key Vault 區段