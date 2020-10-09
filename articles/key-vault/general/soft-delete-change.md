---
title: 所有 Azure Key Vault 都將會啟用虛刪除 | Microsoft Docs
description: 請使用這份文件來為所有金鑰保存庫採用虛刪除。
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 07/27/2020
ms.author: sudbalas
ms.openlocfilehash: 0e811cc219002c034afb968be760ce2c249b08f3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91825258"
---
# <a name="soft-delete-will-be-enabled-on-all-key-vaults"></a>所有金鑰保存庫都將會啟用虛刪除

> [!WARNING]
> **重大變更**：退出虛刪除的功能將會在年底淘汰，所有金鑰保存庫都將會自動開啟虛刪除保護。  Azure Key Vault 的使用者和管理員應該立即在其金鑰保存庫上啟用虛刪除。
>
> 針對受控 HSM，預設會啟用虛刪除，且無法停用。

如果沒有虛刪除保護，從金鑰保存庫刪除祕密時，該祕密就會永久遭到刪除。 使用者目前可以在建立金鑰保存庫時退出虛刪除，但為了防止秘密遭到使用者意外或惡意地刪除，Microsoft 很快地將會在**所有**金鑰保存庫上啟用虛刪除保護，使用者將無法再選擇退出或關閉虛刪除。

:::image type="content" source="../media/softdeletediagram.png" alt-text="<替代文字>":::

如需虛刪除功能的完整詳細資料，請參閱 [Azure Key Vault 虛刪除概觀](soft-delete-overview.md)。

## <a name="how-do-i-respond-to-breaking-changes"></a>如何回應重大變更

當金鑰保存庫物件處於虛刪除狀態時，就無法使用相同的名稱來建立金鑰保存庫物件。  例如，如果您刪除金鑰保存庫 A 中名為 `test key` 的金鑰，您就無法在金鑰保存庫 A 中建立名為 `test key` 的新金鑰，要等到清除虛刪除的 `test key` 物件後才能刪除。

### <a name="application-changes"></a>應用程式變更

如果您的應用程式假設虛刪除並未啟用，而且預期已刪除的秘密或金鑰保存庫名稱可供立即重複使用，則應用程式邏輯必須進行下列變更，才能採用這項變更。

1. 刪除原始的金鑰保存庫或秘密
2. 清除處於虛刪除狀態的金鑰保存庫或祕密。
3. 等待 - 立即重新建立可能會導致衝突發生。
4. 使用相同的名稱重新建立金鑰保存庫。
5. 如果建立作業仍導致名稱衝突錯誤，請進行重試，最糟糕的情況下，可能需要長達 10 分鐘的時間，DNS 記錄才會更新。

### <a name="administration-changes"></a>系統管理變更

需要有權永久刪除秘密的安全性主體必須額外獲得存取原則權限，才能清除這些秘密和金鑰保存庫。

如果您的金鑰保存庫上有 Azure 原則規定必須關閉虛刪除，則必須停用此原則。  您可能需要將此問題呈報給管理員，以控制套用於您環境的 Azure 原則。 如果未停用此原則，您可能會無法在所套用原則的範圍內建立新的金鑰保存庫。

如果您的組織受限於法律合規性需求，且無法允許已刪除的金鑰保存庫和秘密長時間保持可復原狀態，則您必須調整虛刪除保留期間 (可設定的範圍是 7 到 90 天)，以符合貴組織的標準。

## <a name="procedures"></a>程序

### <a name="audit-your-key-vaults-to-check-if-soft-delete-is-enabled"></a>稽核您的金鑰保存庫以檢查虛刪除是否已啟用

1. 登入 Azure 管理入口網站。
2. 搜尋「Azure 原則」。
3. 選取 [定義]。
4. 在 [類別] 下，於篩選中選取 [Key Vault]。
5. 選取 [Key Vault 物件應該要可復原] 原則。
6. 按一下 [指派]。
7. 將範圍設定為您的訂用帳戶。
8. 選取 [檢閱 + 建立]。
9. 最多可能需要 24 小時的時間，才能對您的環境完成完整掃描。
10. 在 [Azure 原則] 刀鋒視窗中，按一下 [合規性]。
11. 選取您套用的原則。

您現在應該能夠篩選並查看哪些金鑰保存庫已啟用虛刪除 (符合規範的資源)，以及哪些金鑰保存庫未啟用虛刪除 (不符合規範的資源)。

### <a name="turn-on-soft-delete-for-an-existing-key-vault"></a>針對現有的金鑰保存庫開啟虛刪除

1. 登入 Azure 管理入口網站。
2. 搜尋您的 Key Vault。
3. 選取 [設定] 底下的 [屬性]。
4. 在 [虛刪除] 底下，選取對應至 [啟用此保存庫及其物件的復原] 選項按鈕。
5. 設定虛刪除的保留期間。
6. 選取 [儲存]。

### <a name="grant-purge-access-policy-permissions-to-a-security-principal"></a>將清除存取原則的權限授與給安全性主體

1. 登入 Azure 管理入口網站。
2. 搜尋您的 Key Vault。
3. 選取 [設定] 底下的 [存取原則]。
4. 選取您想要向其授與存取權的服務主體。
5. 針對 [金鑰]、[祕密] 和 [憑證權限] 底下的每個下拉式清單，向下捲動至 [特殊權限作業]，然後選取 [清除] 權限。

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="does-this-change-affect-me"></a>這項變更是否會對我造成影響？

如果您已經開啟虛刪除，或未先刪除金鑰保存庫物件再以相同名稱重新建立，應該就不會注意到金鑰保存庫的行為有任何變更。

如果您的應用程式經常先刪除金鑰保存庫物件再以相同的命名慣例來重新建立，您就必須在應用程式邏輯中進行變更，才能維持預期的行為。 請參閱上面的＜如何回應重大變更？＞ 一節。

### <a name="how-do-i-benefit-from-this-change"></a>我能從這項變更獲得什麼好處？

虛刪除保護會為您的組織提供一層額外的保護，以防止意外或惡意刪除。 身為金鑰保存庫管理員，您可以同時限制對復原權限和清除權限的存取。

如果使用者不小心刪除了金鑰保存庫或秘密，您可以向其授與存取權限讓其自行復原秘密，而不會有其永久刪除秘密或金鑰保存庫的風險。 此自助式程序會讓您環境的停機時間降到最低，並保證祕密的可用性。

### <a name="how-do-i-find-out-if-i-need-to-take-action"></a>如何了解是否需要採取行動？

請遵循前面的＜用來稽核 Key Vault 以檢查虛刪除是否已開啟的程序＞一節中的步驟來進行。 任何未開啟虛刪除的金鑰保存庫都會因這項變更而受到影響。 其他有助於稽核的工具很快就會推出，且這份文件也會更新。

### <a name="what-action-do-i-need-to-take"></a>我需要採取什麼動作？

確定您不需要變更應用程式邏輯。 確認之後，在所有金鑰保存庫上開啟虛刪除。 這可確保當年底所有金鑰保存庫都開啟虛刪除時，您不會受到重大變更的影響。

### <a name="by-when-do-i-need-to-take-action"></a>我最慢必須何時採取行動？

年底時，所有金鑰保存庫都會開啟虛刪除。 為確保您的應用程式不會受到影響，請盡快在金鑰保存庫上開啟虛刪除。

## <a name="what-will-happen-if-i-dont-take-any-action"></a>如果我沒有採取任何動作，會發生什麼事？

如果您未採取任何動作，所有金鑰保存庫會在年底時自動開啟虛刪除。 如果您嘗試在刪除金鑰保存庫物件後，未先將其從虛刪除狀態中清除就以相同名稱重新建立，則可能會導致衝突錯誤。 這可能會導致您的應用程式或自動化失敗。

## <a name="next-steps"></a>後續步驟

- 如有關於此變更的任何問題，請至 [akvsoftdelete@microsoft.com](mailto:akvsoftdelete@microsoft.com) 與我們連絡。
- 閱讀[虛刪除概觀](soft-delete-overview.md)
