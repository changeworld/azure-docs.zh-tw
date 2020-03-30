---
title: Azure 活動目錄標識和訪問管理操作參考指南
description: 此操作參考指南描述為確保標識和訪問管理操作而應採取的檢查和操作
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: 5653fa7c67d36dbf2ee71f51f182168bccb69105
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79298609"
---
# <a name="azure-active-directory-identity-and-access-management-operations-reference-guide"></a>Azure 活動目錄標識和訪問管理操作參考指南

Azure AD[操作參考指南](active-directory-ops-guide-intro.md)的這一部分介紹了保護和管理標識及其分配的生命週期時應考慮的檢查和操作。

> [!NOTE]
> 這些建議自發布之日起是最新的，但可能會隨時間而變化。 隨著 Microsoft 產品和服務隨時間推移而變化，組織應持續評估其身份實踐。

## <a name="key-operational-processes"></a>關鍵操作流程

### <a name="assign-owners-to-key-tasks"></a>將擁有者分配給要徑任務

管理 Azure 活動目錄需要持續執行可能不是推出專案一部分的關鍵操作任務和進程。 設置這些任務以維護環境仍然很重要。 要徑任務及其推薦的擁有者包括：

| Task | 擁有者 |
| :- | :- |
| 定義如何創建 Azure 訂閱的過程 | 因組織而異 |
| 決定誰獲得企業移動性和安全許可證 | IAM 運營團隊 |
| 決定誰獲得 Office 365 許可證 | 生產力團隊 |
| 決定誰獲得其他許可證，例如，動態，VSO | 應用程式擁有者 |
| 指派授權 | IAM 運營團隊 |
| 排除和修復許可證分配錯誤 | IAM 運營團隊 |
| 預配 Azure AD 中的應用程式標識 | IAM 運營團隊 |

在查看清單時，您可能會發現需要為缺少擁有者的任務分配擁有者，或者調整擁有者與上述建議不一致的任務的擁有權。

#### <a name="assigning-owners-recommended-reading"></a>分配擁有者建議閱讀

- [在 Azure Active Directory 中指派系統管理員角色](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)
- [Azure 中的治理](https://docs.microsoft.com/azure/security/governance-in-azure)

## <a name="on-premises-identity-synchronization"></a>本地標識同步

### <a name="identify-and-resolve-synchronization-issues"></a>識別和解決同步問題

Microsoft 建議您對本地環境中可能導致雲同步問題的問題有良好的基線和理解。 由於[IdFix](https://docs.microsoft.com/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix)和 Azure [AD 連接運行狀況](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-azure-ad-connect#why-use-azure-ad-connect-health)等自動化工具可以生成大量誤報，因此我們建議您通過清除錯誤物件來識別 100 天內未解決的同步錯誤。 長期未解決的同步錯誤可能會生成支援事件。 [同步期間的故障排除錯誤](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sync-errors)概述了不同類型的同步錯誤、導致這些錯誤的一些可能方案以及修復錯誤的潛在方法。

### <a name="azure-ad-connect-sync-configuration"></a>Azure AD 連接同步配置

要啟用所有混合體驗、基於設備的安全狀態以及與 Azure AD 集成，需要同步員工用來登錄到其桌面的使用者帳戶。

如果不同步林使用者登錄，則應將同步更改為來自正確的林。

#### <a name="synchronization-scope-and-object-filtering"></a>同步範圍和物件篩選

刪除不需要同步的已知物件存儲桶具有以下操作優勢：

- 同步錯誤源減少
- 更快的同步週期
- 減少從本地轉發的"垃圾"，例如，雲中不相關的本地服務帳戶的全域通訊清單污染

> [!NOTE]
> 如果您發現正在導入許多未匯出到雲的物件，則應按 OU 或特定屬性進行篩選。

要排除的物件的示例包括：

- 不用於雲應用程式的服務帳戶
- 不應用於雲方案（如用於授予資源存取權限）的組
- 旨在表示 Azure AD B2B 協作的外部標識的使用者或連絡人
- 員工不應從伺服器（例如伺服器）訪問雲應用程式的電腦帳戶

> [!NOTE]
> 如果單個人的身份具有從舊域遷移、合併或獲取等內容預配的多個帳戶，則僅應同步使用者日常使用的帳戶，例如，他們用於登錄到其電腦的內容.

理想情況下，您希望在減少要同步的物件數和規則中的複雜性之間取得平衡。 通常，OU/容器[篩選](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-configure-filtering)與與雲篩選屬性的簡單屬性對應的組合是一種有效的篩選組合。

> [!IMPORTANT]
> 如果在生產中使用組篩選，則應過渡到其他篩選方法。

#### <a name="sync-failover-or-disaster-recovery"></a>同步容錯移轉或災害復原

Azure AD 連接在預配過程中扮演著關鍵角色。 如果同步伺服器由於任何原因離線，則無法在雲中更新對本地的更改，並可能導致使用者訪問問題。 因此，請務必定義容錯移轉策略，允許管理員在同步伺服器離線後快速恢復同步。 這種戰略可分為以下幾類：

- **在暫存模式下部署 Azure AD 連接伺服器**- 允許管理員通過簡單的配置開關將暫存伺服器"提升"到生產。
- **使用虛擬化**- 如果 Azure AD 連接部署在虛擬機器 （VM） 中，則管理員可以利用其虛擬化堆疊進行即時移轉或快速重新部署 VM，從而恢復同步。

如果您的組織缺少 Sync 的災害復原和容錯移轉策略，則應毫不猶豫地在暫存模式下部署 Azure AD 連接。 同樣，如果生產和暫存配置不匹配，則應重新基線 Azure AD Connect 暫存模式以匹配生產配置，包括軟體版本和配置。

![Azure AD 連接暫存模式配置的螢幕截圖](./media/active-directory-ops-guide/active-directory-ops-img1.png)

#### <a name="stay-current"></a>保持最新狀態

微軟定期更新 Azure AD 連接。 保持最新狀態，利用每個新版本提供的性能改進、錯誤修復和新功能。

如果 Azure AD Connect 版本落後六個月以上，則應升級到最新版本。

#### <a name="source-anchor"></a>源錨點

使用**ms-DS 一致性作為**[源錨點](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-design-concepts)可以更輕鬆地跨林和域遷移物件，這在 AD 域整合/清理、合併、收購和剝離中很常見。

如果您當前使用**ObjectGuid**作為源錨點，我們建議您切換到使用**ms-DS-一致性 Guid**。

#### <a name="custom-rules"></a>自訂規則

Azure AD Connect 自訂規則提供了控制本機物件和雲物件之間屬性流的能力。 但是，過度使用或誤用自訂規則可能會帶來以下風險：

- 故障排除複雜性
- 跨物件執行複雜操作時性能下降
- 生產伺服器和暫存伺服器之間配置差異的可能性更高
- 如果在優先順序大於 100 的範圍內創建自訂規則（由內置規則使用）則升級 Azure AD Connect 時的額外開銷

如果使用過於複雜的規則，則應調查複雜性的原因，並找到簡化的機會。 同樣，如果您創建了優先順序值超過 100 的自訂規則，則應修復這些規則，以便這些規則不會面臨風險或與預設集衝突。

濫用自訂規則的示例包括：

- **補償目錄中的髒資料**- 在這種情況下，建議與 AD 團隊的擁有者合作，將目錄中的資料清理為修正任務，並調整進程以避免重新引入錯誤資料。
- **單個使用者的一次性修正**- 通常查找異常情況的規則，通常是由於特定使用者的問題。
- **"雲篩選"過於複雜**- 雖然減少物件數量是一種好的做法，但使用許多同步規則創建和過度複雜的同步範圍存在風險。 如果存在複雜的邏輯來包括/排除 OU 篩選之外的物件，建議在同步之外處理此邏輯，並將物件標記為具有簡單"雲篩選"屬性，該屬性可以使用簡單的同步規則流動。

#### <a name="azure-ad-connect-configuration-documenter"></a>Azure AD 連接配置文檔

[Azure AD 連接配置文檔是](https://github.com/Microsoft/AADConnectConfigDocumenter)一種工具，可用於生成 Azure AD Connect 安裝的文檔，以便更好地瞭解同步配置、建立正確操作的信心，並瞭解在應用 Azure AD Connect 的新生成或配置或添加或更新自訂同步規則時發生了哪些更改。 該工具的當前功能包括：

- Azure AD 連接同步的完整配置的文檔。
- 記錄兩個 Azure AD Connect 配置中的任何更改同步伺服器或從給定配置基線更改。
- 生成 PowerShell 部署腳本，以將同步規則差異或自訂項從一台伺服器遷移到另一台伺服器。

## <a name="assignment-to-apps-and-resources"></a>分配給應用和資源

### <a name="group-based-licensing-for-microsoft-cloud-services"></a>基於組的許可，適用于 Microsoft 雲服務

Azure 活動目錄通過 Microsoft 雲服務[基於組的許可](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-licensing-whatis-azure-portal)簡化授權管理。 這樣，IAM 會將這些組基礎結構和委派管理委託給組織中的適當團隊。 在 Azure AD 中設置組的成員身份有多種，包括：

- **從本地同步**- 組可能來自本地目錄，這非常適合已建立的組管理流程的組織，這些流程可以擴展以在 Office 365 中分配許可證。

- **基於屬性/動態**- 可以基於基於使用者屬性的運算式在雲中創建組，例如，部門等於"銷售"。 Azure AD 維護組的成員，使其與定義的運算式保持一致。 將此類組用於許可證分配可啟用基於屬性的許可證分配，這非常適合其目錄中具有高資料品質的組織。

- **委派的擁有權**- 可以在雲中創建組，也可以指定擁有者。 這樣，您可以授權業務擁有者（例如協作團隊或 BI 團隊）定義誰應該有權訪問。

如果您當前使用手動流程向使用者分配許可證和元件，我們建議您實施基於組的許可。 如果當前流程不監視許可錯誤或"分配"與"可用"，則應定義流程的改進，以解決許可錯誤並監視許可分配。

授權管理的另一個方面是服務方案（許可證元件）的定義，應基於組織中的作業功能啟用。 授予對不需要的服務方案的許可權，可能會導致使用者在 Office 門戶中看到尚未培訓或不應使用的工具。 它可以推動額外的説明台容量、不必要的預配，並危及合規性和治理，例如，在為可能不允許共用內容的個人預配 OneDrive 時。

使用以下準則向使用者定義服務方案：

- 管理員應根據使用者的角色（例如白領員工與地工）定義要提供給使用者的"捆綁"服務方案。
- 按群集創建組，並通過服務方案分配許可證。
- 或者，可以定義屬性以為使用者保留包。

> [!IMPORTANT]
> Azure AD 中基於組的許可引入了處於許可錯誤狀態的使用者的概念。 如果您發現任何許可錯誤，則應立即[識別和解決](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-resolve-problems)任何許可證分配問題。

![自動生成的電腦螢幕的螢幕截圖 描述](./media/active-directory-ops-guide/active-directory-ops-img2.png)

#### <a name="lifecycle-management"></a>生命週期管理

如果您當前使用依賴本地基礎結構的工具（如[Microsoft 標識管理器](https://docs.microsoft.com/microsoft-identity-manager/)或協力廠商系統），我們建議您從現有工具卸載分配，實現基於組的許可，並根據[組](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-group-advanced#use-group-based-licensing-with-dynamic-groups)定義組生命週期管理。 同樣，如果您的現有流程不考慮離開組織的新員工或員工，則應基於動態組部署基於組的許可，並定義組成員生命週期。 最後，如果針對缺少生命週期管理的本機群組部署了基於組的許可，請考慮使用雲組啟用委派擁有權或基於屬性的動態成員身份等功能。

### <a name="assignment-of-apps-with-all-users-group"></a>具有"所有使用者"組的應用分配

資源擁有者可能認為，**所有使用者**組僅包含**企業員工**，而實際上可能同時包含**企業員工**和**來賓**。 因此，在使用 **"所有使用者**"組進行應用程式分配和授予對資源（如 SharePoint 內容或應用程式）的存取權限時，應特別注意。

> [!IMPORTANT]
> 如果啟用了 **"所有使用者**"組並用於條件訪問策略、應用或資源配置，請確保在不希望[該組](https://docs.microsoft.com/azure/active-directory/b2b/use-dynamic-groups)包含來賓使用者時保護該組。 此外，還應通過創建和分配給僅包含**企業員工的**組來修復許可分配。 另一方面，如果您發現 **"所有使用者**"組已啟用，但未用於授予對資源的存取權限，請確保組織的運營指導是有意使用該組（包括**企業員工**和**來賓**）。

### <a name="automated-user-provisioning-to-apps"></a>自動使用者預配到應用

[自動使用者預配](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)到應用程式是跨多個系統創建一致配置、取消預配和生命週期的最佳方式。

如果您當前正在以臨時方式預配應用，或者使用 CSV 檔、JIT 或不涉及生命週期管理的本地解決方案等內容，我們建議您為受支援的應用程式實現 Azure AD[的應用程式預配](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning#how-do-i-set-up-automatic-provisioning-to-an-application)，並為 Azure AD 尚不支援的應用程式定義一致的模式。

![Azure AD 預配服務](./media/active-directory-ops-guide/active-directory-ops-img3.png)

### <a name="azure-ad-connect-delta-sync-cycle-baseline"></a>Azure AD 連接增量同步週期基線

瞭解組織中更改的數量並確保不會花太長的時間進行可預測的同步時間，這一點非常重要。

[預設增量同步](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-feature-scheduler)頻率為 30 分鐘。 如果增量同步持續時間超過 30 分鐘，或者暫存和生產中的增量同步性能之間存在顯著差異，則應調查和查看[影響 Azure AD 連線性能的因素](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-performance-factors)。

#### <a name="azure-ad-connect-troubleshooting-recommended-reading"></a>Azure AD 連接故障排除建議讀取

- [使用 IdFix 工具 （ Office 365）準備目錄屬性，以便與 Office 365 同步](https://docs.microsoft.com/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix)
- [Azure AD 連接：在同步期間排除錯誤](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-sync-errors)

## <a name="summary"></a>總結

安全標識基礎結構有五個方面。 此清單將説明您快速查找並在必要時操作，以確保和管理組織中標識及其權利的生命週期。

- 將擁有者分配給要徑任務。
- 查找並解決同步問題。
- 定義災害復原的容錯移轉策略。
- 簡化授權管理和應用程式分配。
- 自動向應用預配使用者。

## <a name="next-steps"></a>後續步驟

開始[使用身份驗證管理檢查和操作](active-directory-ops-guide-auth.md)。
