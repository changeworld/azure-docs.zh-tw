---
title: 如何回應 Azure Defender 的 Key Vault 警示
description: 瞭解從 Azure Defender 針對 Key Vault 回應警示所需的步驟。
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: e3eb6b750c805951a67a2e869c862f1285a342a8
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91448325"
---
# <a name="respond-to-azure-defender-for-key-vault-alerts"></a>回應適用於 Key Vault 的 Azure Defender 警示
當您從 Azure Defender 針對 Key Vault 收到警示時，建議您調查並回應警示，如下所述。 適用于 Key Vault 的 Azure Defender 可保護應用程式和認證，因此即使您熟悉觸發警示的應用程式或使用者，也請務必確認每個警示周圍的狀況。  

Azure Defender for Key Vault 的每個警示都包含下列元素：

- 物件識別碼
- 可疑資源的使用者主體名稱或 IP 位址

> [!TIP]
> 根據發生的存取 *類型* 而定，有些欄位可能無法使用。 例如，如果您的金鑰保存庫是由應用程式存取，您就不會看到相關聯的使用者主體名稱。 如果流量源自于 Azure 外部，您將不會看到物件識別碼。

## <a name="step-1-contact"></a>步驟 1： Contact

1. 確認流量是否源自您的 Azure 租使用者。 如果金鑰保存庫防火牆已啟用，您可能會提供觸發此警示之使用者或應用程式的存取權。
1. 如果您無法確認流量的來源，請繼續進行 [步驟2。立即緩和](#step-2-immediate-mitigation)。
1. 如果您可以識別租使用者中的流量來源，請洽詢該應用程式的使用者或擁有者。 

> [!CAUTION]
> 適用于 Key Vault 的 Azure Defender 是設計用來協助識別遭竊認證所造成的可疑活動。 因為您辨識使用者或應用程式，所以**請勿**關閉警示。 請聯繫應用程式或使用者的擁有者，並確認活動是否合法。 如有必要，您可以建立隱藏專案規則來消除雜訊。 深入瞭解如何 [從 Azure Defender 隱藏警示](alerts-suppression-rules.md)。


## <a name="step-2-immediate-mitigation"></a>步驟 2： 立即緩和 
如果您無法辨識使用者或應用程式，或您認為該存取權不應獲得授權：

- 如果流量來自無法辨識的 IP 位址：
    1. 啟用 Azure Key Vault 防火牆，如 [設定 Azure Key Vault 防火牆和虛擬網路](../key-vault/general/network-security.md)中所述。
    1. 使用受信任的資源和虛擬網路來設定防火牆。

- 如果警示的來源是未經授權的應用程式或可疑的使用者：
    1. 開啟金鑰保存庫的存取原則設定。
    1. 移除對應的安全性主體，或限制安全性主體可執行檔作業。  

- 如果警示來源的租使用者中有 Azure Active Directory 角色：
    1. 請連絡系統管理員。
    1. 判斷是否需要減少或撤銷 Azure Active Directory 許可權。

## <a name="step-3-identify-impact"></a>步驟 3： 識別影響 
當影響緩和之後，請調查金鑰保存庫中受影響的秘密：
1. 開啟您 Azure Key Vault 上的 [安全性] 頁面，並查看觸發的警示。
1. 選取已觸發的特定警示。
    檢查已存取的秘密清單以及時間戳記。
1. （選擇性）如果您已啟用金鑰保存庫診斷記錄，請檢查對應的呼叫端 IP、使用者主體或物件識別碼先前的作業。  

## <a name="step-4-take-action"></a>步驟 4： 採取動作 
當您編譯了可疑使用者或應用程式所存取的秘密、金鑰和憑證清單之後，您應該立即輪替這些物件。

1. 應該停用或刪除您的金鑰保存庫中受影響的秘密。
1. 如果是針對特定應用程式使用認證：
    1. 請洽詢應用程式的系統管理員，並要求他們審核其環境，以使用遭盜用的認證，因為它們遭到盜用。
    1. 如果使用了遭盜用的認證，應用程式擁有者應該識別已存取的資訊並減輕影響。


## <a name="next-steps"></a>後續步驟

此頁面說明從 Azure Defender 針對 Key Vault 回應警示的流程。 如需相關資訊，請參閱下列頁面：

- [適用于 Key Vault 的 Azure Defender 簡介](defender-for-key-vault-introduction.md)
- [隱藏 Azure Defender 的警示](alerts-suppression-rules.md)
- [匯出安全性警示](continuous-export.md)