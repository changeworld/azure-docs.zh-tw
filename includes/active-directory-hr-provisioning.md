---
author: billmath
ms.service: active-directory
ms.subservice: governance
ms.topic: include
ms.date: 10/16/2019
ms.author: billmath
ms.openlocfilehash: be33e492f44f6926f2ae2d133cf112245fba15f1
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93134907"
---
## <a name="cloud-hr-application-to-azure-active-directory-user-provisioning"></a>雲端 HR 應用程式佈建至 Azure Active Directory 使用者

過去，IT 人員依賴手動方法來建立、更新和刪除員工。 他們使用了上傳 CSV 檔案或自訂指令碼來同步員工資料之類的方法。 這些佈建程序很容易出錯、不安全，且難以管理。

為了管理員工、廠商或臨時工的身分識別生命週期，[Azure Active Directory (Azure AD) 使用者佈建服務](../articles/active-directory/app-provisioning/user-provisioning.md)提供與雲端式人力資源 (HR) 應用程式的整合。 應用程式的範例包括 Workday 或 SuccessFactors。

Azure AD 使用這項整合來啟用下列雲端 HR 應用程式工作流程：

- **將使用者佈建到 Active Directory：** 將選取的使用者集從雲端 HR 應用程式佈建到一或多個 Active Directory 網域。
- **將僅限雲端的使用者佈建到 Azure AD：** 在未使用 Active Directory 的情況下，將使用者直接從雲端 HR 應用程式佈建到 Azure AD。
- **寫回至雲端 HR 應用程式：** 將電子郵件地址和使用者名稱屬性從 Azure AD 寫回至雲端 HR 應用程式。


## <a name="enabled-hr-scenarios"></a>啟用的 HR 案例

Azure AD 使用者佈建服務可使下列以 HR 為基礎的身分識別生命週期管理案例自動化：

- **新員工雇用：** 將新員工新增至雲端 HR 應用程式時，會使用將電子郵件地址和使用者名稱屬性寫回至雲端 HR 應用程式的選項，在 Active Directory 和 Azure AD 中自動建立使用者帳戶。
- **員工屬性和個人檔案更新：** 在雲端 HR 應用程式中更新員工記錄 (例如名字、職稱或經理) 時，其使用者帳戶會在 Active Directory 和 Azure AD 中自動更新。
- **員工終止：** 在雲端 HR 應用程式中終止員工時，其使用者帳戶會在 Active Directory 和 Azure AD 中自動停用。
- **員工重新雇用：** 在雲端 HR 應用程式中重新雇用員工時，其舊帳戶可以自動重新啟用或重新佈建至 Active Directory 和 Azure AD。

## <a name="who-is-this-integration-best-suited-for"></a>此種整合最適合誰？

雲端 HR 應用程式與 Azure AD 使用者佈建的整合非常適合下列組織：

- 想要適用於雲端 HR 使用者佈建的預先建置、雲端式解決方案。
- 需要將使用者直接從雲端 HR 應用程式佈建至 Active Directory 或 Azure AD。
- 需要使用從雲端 HR 應用程式取得的資料來佈建使用者。
- 需要僅根據在雲端 HR 應用程式中偵測到的變更資訊，將加入、調動及離開的使用者同步至一或多個 Active Directory 樹系、網域和 OU。
- 將 Office 365 用於電子郵件。


### <a name="key-benefits"></a>主要權益

此種 HR 驅動 IT 佈建功能提供下列顯著的商業優勢：

- **提高生產力：** 您現在可以將使用者帳戶和 Office 365 授權的指派自動化，並提供金鑰群組的存取權。 自動指派可讓新進員工立即存取其作業工具並提高生產力。
- **管理風險：** 您可以使用從雲端 HR 應用程式流入的資料，根據員工狀態或群組成員資格來自動化變更，以提高安全性。 自動化變更可確保當使用者調職或離開組織時，會自動更新使用者身分識別和金鑰應用程式的存取權。
- **處理合規性與治理：** 對於來源和目標系統的應用程式所執行的使用者佈建要求，Azure AD 支援原生稽核記錄。 透過稽核，您可以從單一畫面追蹤可存取應用程式的人員。
- **管理成本：** 自動佈建可避免與手動佈建相關的效率不彰和人為錯誤，進而降低成本。 藉由使用舊版和過時的平台，可減少隨著時間建置自訂開發使用者佈建解決方案的需求。
