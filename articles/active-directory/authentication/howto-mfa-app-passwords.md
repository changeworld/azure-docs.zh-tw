---
title: 設定 Azure 多重要素驗證的應用程式密碼-Azure Active Directory
description: 瞭解如何在 Azure 多因素驗證中設定和使用繼承應用程式的應用程式密碼
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 06/05/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b247b64d563bc2b12c5bffff6a460d77cb96207
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84485481"
---
# <a name="enable-and-use-azure-multi-factor-authentication-with-legacy-applications-using-app-passwords"></a>透過使用應用程式密碼的繼承應用程式來啟用及使用 Azure 多重要素驗證

某些應用程式（例如 Office 2010 或更早版本，以及 iOS 11 之前的 Apple Mail）不支援多重要素驗證。 應用程式未設定為接受次要形式的驗證或提示。 若要以安全的方式使用這些應用程式，並針對使用者帳戶啟用 Azure 多重要素驗證，您可以使用應用程式密碼。 這些應用程式密碼會取代您的傳統密碼，讓應用程式略過多重要素驗證並正常運作。

Microsoft Office 2013 用戶端和更新版本支援新式驗證。 Office 2013 用戶端（包括 Outlook）支援新式驗證通訊協定，而且可以啟用以使用雙步驟驗證。 啟用用戶端之後，用戶端並不需要應用程式密碼。

本文說明如何針對不支援多重要素驗證提示的繼承應用程式啟用及使用應用程式密碼。

>[!NOTE]
> 應用程式密碼不適用於以條件式存取為基礎的多重要素驗證原則和新式驗證。

## <a name="overview-and-considerations"></a>總覽和考慮

當使用者帳戶啟用 Azure 多重要素驗證時，一般登入提示會被要求進行額外的驗證中斷。 某些較舊的應用程式不了解登入過程中的這項中斷，因此驗證會失敗。 若要維護使用者帳戶安全性，並讓 Azure 多重要素驗證保持啟用狀態，您可以使用應用程式密碼，而不是使用者的一般使用者名稱和密碼。 在登入期間使用應用程式密碼時，不會有額外的驗證提示，因此驗證成功。

應用程式密碼會自動產生，而不是由使用者指定。 這個自動產生的密碼會使攻擊者更難猜到，因此更安全。 使用者每次只輸入一次應用程式密碼，就不需要追蹤密碼或輸入密碼。

當您使用應用程式密碼時，適用下列考慮事項：

* 每位使用者的應用程式密碼限制為40。
* 快取密碼並用於內部部署案例的應用程式可能會失敗，因為在工作或學校帳戶以外的應用程式密碼不是已知的。 將 Exchange 電子郵件存放在內部部署設施，但是將封存郵件存放在雲端即是此案例的一個範例。 在此案例中，同樣的密碼無法適用於兩者。
* 在使用者帳戶上啟用 Azure 多重要素驗證之後，應用程式密碼可用於大部分的非瀏覽器用戶端，例如 Outlook 和 Microsoft 商務用 Skype。 不過，您無法透過非瀏覽器應用程式（例如 Windows PowerShell）使用應用程式密碼來執行系統管理動作。 即使使用者具有系統管理員帳戶，仍無法執行動作。
    * 若要執行 PowerShell 指令碼，請使用強式密碼建立服務帳戶，且請勿為該帳戶啟用雙步驟驗證。

>[!WARNING]
> 應用程式密碼無法在用戶端會同時與內部部署及雲端自動探索端點通訊的混合環境作用。 需要網域密碼才能驗證內部部署。 需要應用程式密碼才能向雲端驗證。

### <a name="app-password-names"></a>應用程式密碼名稱

應用程式密碼名稱應該反映出它們用在哪個裝置。 如果膝上型電腦有 Outlook、Word 及 Excel 之類的非瀏覽器應用程式，請針對這些應用程式建立一個名為 **Laptop** 的應用程式密碼。 接著，為在桌上型電腦上執行的同樣應用程式建立另一個應用程式密碼，名為 **Desktop**。

建議每個裝置建立一個應用程式密碼，而不是每個應用程式的一個應用程式密碼。

## <a name="federated-or-single-sign-on-app-passwords"></a>同盟或單一登入應用程式密碼

Azure AD 支援使用內部部署 Active Directory Domain Services （AD DS）的同盟或單一登入（SSO）。 如果您的組織與 Azure AD 同盟，而且您使用的是 Azure 多因素驗證，則適用下列應用程式密碼考慮：

>[!NOTE]
> 下列各點僅適用於同盟 (SSO) 客戶。

* 應用程式密碼由 Azure AD 驗證，因此會略過同盟。 唯有在設定應用程式密碼時才會主動使用同盟。
* 對於同盟 (SSO) 使用者，不會像被動流程一樣連絡識別提供者 (IdP)。 應用程式密碼會儲存在公司或學校帳戶中。 如果使用者離開公司，使用者的資訊就會即時使用 **DirSync** 流向公司或學校帳戶。 停用/刪除帳戶最多可能需要三個小時才能同步處理，這可能會延遲 Azure AD 中的應用程式密碼停用/刪除。
* 應用程式密碼功能不會遵守內部部署用戶端存取控制設定。
* 應用程式密碼功能不提供內部部署驗證記錄或審核功能。

某些先進的架構需要與用戶端進行多重要素驗證的認證組合。 這些認證可能包含公司或學校帳戶使用者名稱和密碼，以及應用程式密碼。 這些需求取決於執行驗證的方式。 對於根據內部部署基礎結構進行驗證的用戶端，需要公司或學校帳戶的使用者名稱和密碼。 對於根據 Azure AD 進行驗證的用戶端，需要應用程式密碼。

例如，假設您有下列架構：

* 您的內部部署 Active Directory 執行個體會與 Azure AD 同盟。
* 您使用 Exchange online。
* 您在內部部署使用商務用 Skype。
* 您會使用 Azure 多重要素驗證。

在此案例中，您要使用下列認證：

* 若要登入商務用 Skype，請使用您公司或學校帳戶的使用者名稱和密碼。
* 若要從連線至 Exchange Online 的 Outlook 用戶端存取通訊錄，請使用應用程式密碼。

## <a name="allow-users-to-create-app-passwords"></a>允許使用者建立應用程式密碼

根據預設，使用者無法建立應用程式密碼。 必須先啟用 [應用程式密碼] 功能，使用者才能使用它們。 若要讓使用者能夠建立應用程式密碼，請完成下列步驟：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 搜尋並選取 [ **Azure Active Directory**]，然後選擇 [**使用者**]。
3. 從 [*使用者*] 視窗頂端的導覽列中，選取 [**多重要素驗證**]。
4. 在 [Multi-Factor Authentication] 下，選取 [服務設定]。
5. 在 [服務設定] 頁面上，選取 [允許使用者建立應用程式密碼以登入非瀏覽器應用程式] 選項。

    ![Azure 入口網站的螢幕擷取畫面，其中顯示多重要素驗證的服務設定，以允許使用者建立應用程式密碼](media/concept-authentication-methods/app-password-authentication-method.png)

## <a name="create-an-app-password"></a>建立應用程式密碼

當使用者完成其 Azure 多重要素驗證的初始註冊時，可以選擇在註冊程式結束時建立應用程式密碼。

使用者也可以在註冊後建立應用程式密碼。 如需使用者的詳細資訊和詳細步驟，請參閱[什麼是 Azure Multi-Factor Authentication 中的應用程式密碼？](../user-help/multi-factor-authentication-end-user-app-passwords.md)

## <a name="next-steps"></a>後續步驟

如需有關如何允許使用者快速註冊 Azure 多重要素驗證的詳細資訊，請參閱[結合的安全性資訊註冊總覽](concept-registration-mfa-sspr-combined.md)。
