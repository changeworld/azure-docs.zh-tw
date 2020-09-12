---
title: Azure Key Vault 的威脅防護
description: 本文說明如何設定中 Azure Key Vault 的 advanced 威脅防護 Azure 資訊安全中心
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 7025b091b83a56b7b3df18c3f42cb84b163560bb
ms.sourcegitcommit: c94a177b11a850ab30f406edb233de6923ca742a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/01/2020
ms.locfileid: "89277760"
---
# <a name="threat-protection-for-azure-key-vault-preview"></a>Azure Key Vault (preview) 的威脅防護

Azure Key Vault 的「先進威脅防護」提供額外一層的安全情報。 此工具會偵測可能有害的嘗試存取或惡意探索 Key Vault 帳戶。 使用 Azure 資訊安全中心中的原生 advanced 威脅防護，您可以在不成為安全性專家的情況下解決威脅，而不需要學習額外的安全性監視系統。

當安全性中心偵測到異常活動時，就會顯示警示。 此外，它也會以可疑活動的詳細資料來傳送電子郵件給訂用帳戶管理員，以及如何調查和補救已識別威脅的建議。

## <a name="configure-threat-protection-from-security-center"></a>從安全性中心設定威脅防護

依預設，當您訂閱安全中心的標準定價層時，會為您的所有 Key Vault 帳戶啟用「advanced 威脅防護」。 如需詳細資訊，請參閱[定價](security-center-pricing.md)。

若要啟用或停用特定訂用帳戶的保護：

1. 在 [安全中心] 的左窗格中，選取 [ **定價 & 設定**]。

1. 選取具有您要啟用或停用威脅防護之儲存體帳戶的訂用帳戶。

1. 選取 [定價層]  。

1. 從 [ **依資源類型的選取定價層** ] 群組中，尋找 [ **金鑰保存庫** ] 列，然後選取 [ **啟用** ] 或 [ **停用**]

    [![在 Azure 資訊安全中心中啟用或停用 Key Vault 的 advanced 威脅防護](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png)](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png#lightbox)

1. 選取 [儲存]。


## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解如何啟用和停用 Azure Key Vault 的 advanced 威脅防護。 

如需相關內容，請參閱下列文章：

- [Azure 資訊安全中心中的威脅防護](threat-protection.md)--本文說明 Azure 資訊安全中心中的安全性警示來源。
- [Key Vault 安全性警示](alerts-reference.md#alerts-azurekv)--所有 Azure 資訊安全中心警示的參考資料表 Key Vault 區段