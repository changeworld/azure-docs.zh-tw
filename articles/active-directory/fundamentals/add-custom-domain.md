---
title: 新增您的自訂網域 - Azure Active Directory | Microsoft Docs
description: 關於如何使用 Azure Active Directory 新增自訂網域的指示。
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: ajburnle
ms.reviewer: elkuzmen
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: de91bd7e1e4c5f9909213f663dd3ede0f979d4de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262148"
---
# <a name="add-your-custom-domain-name-using-the-azure-active-directory-portal"></a>使用 Azure Active Directory 入口網站新增自訂網域名稱

每個新的 Azure AD 租戶都附帶一個初始功能變數名稱，*\<功能變數名稱>.onmicrosoft.com。* 不能更改或刪除初始功能變數名稱，但可以添加組織的名稱。 添加自訂功能變數名稱可説明您創建使用者熟悉的使用者名，例如*alain\@contoso.com*。

## <a name="before-you-begin"></a>開始之前

在添加自訂功能變數名稱之前，請使用域註冊商創建功能變數名稱。 如需公認的網域註冊機構，請參閱 [ICANN 認可的註冊機構](https://www.icann.org/registrar-reports/accredited-list.html)。

## <a name="create-your-directory-in-azure-ad"></a>在 Azure AD 中建立您的目錄

取得您的網域名稱之後，您可以建立您的第一個 Azure AD 目錄。 使用具有訂閱**擁有者**角色的帳戶登錄到目錄的 Azure 門戶。

遵循[為您的組織建立新的租用戶](active-directory-access-create-new-tenant.md#create-a-new-tenant-for-your-organization)中的步驟，以建立新的目錄。

>[!IMPORTANT]
>建立租用戶的人員會自動成為該租用戶的全域管理員。 全域管理員可以將其他系統管理員新增至租用戶。

有關訂閱角色的詳細資訊，請參閱[Azure RBAC 角色](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-rbac-roles)。

>[!TIP]
> 如果計畫將本地 Windows 伺服器 AD 與 Azure AD 聯合使用，則需要選擇"我計畫"在運行 Azure AD 連接工具以同步目錄時 **，將此域配置為與本地活動目錄單一登入**。
>
> 您也需要註冊相同的網域名稱，您選取該名稱以與精靈中 **Azure AD 網域** 步驟中的內部部署目錄同盟。 要查看該設置的外觀，請參閱[驗證為聯合選擇的 Azure AD 域](../hybrid/how-to-connect-install-custom.md#verify-the-azure-ad-domain-selected-for-federation)。 如果沒有 Azure AD 連接工具，可以[在此處下載該工具](https://go.microsoft.com/fwlink/?LinkId=615771)。

## <a name="add-your-custom-domain-name-to-azure-ad"></a>將自訂網域名稱新增至 Azure AD

建立您的目錄之後，您可以新增自訂網域名稱。

1. 使用目錄的全域系統管理員帳戶登入 [Azure 入口網站](https://portal.azure.com/)。

1. 從任何頁面搜索並選擇*Azure 活動目錄*。 然後選擇**自訂功能變數名稱** > **添加自訂域**。

    ![自訂功能變數名稱頁面，顯示"添加自訂域"](media/add-custom-domain/add-custom-domain.png)

1. 在**自訂功能變數名稱**中，輸入組織的新名稱，在此示例中 *，contoso.com*。 選取 [新增網域]****。

    ![自訂功能變數名稱頁面，帶"添加自訂域"頁](media/add-custom-domain/add-custom-domain-blade.png)

    >[!IMPORTANT]
    >您必須包括 *.com* *、.net*或任何其他頂級擴展，才能正常工作。

    將添加未驗證的域。 將顯示**contoso.com**頁，顯示您的 DNS 資訊。 請儲存此資訊。 以後需要它來創建一個 TXT 記錄來配置 DNS。

    ![包含 DNS 項目資訊的 Contoso 頁面](media/add-custom-domain/contoso-blade-with-dns-info.png)

## <a name="add-your-dns-information-to-the-domain-registrar"></a>將您的 DNS 資訊新增至網域註冊機構

將自訂網域名稱新增至 Azure AD 之後，您必須回到您的網域註冊機構，並新增從您複製的 TXT 檔案中的 Azure AD DNS 資訊。 為您的域創建此 TXT 記錄可驗證功能變數名稱的擁有權。

返回域註冊商，並根據複製的 DNS 資訊為您的域創建新的 TXT 記錄。 將存留時間 （TTL） 設置為 3600 秒（60 分鐘），然後保存記錄。

>[!IMPORTANT]
>您可以註冊您想要的多個網域名稱。 不過，每個網域會從 Azure AD 取得自己的 TXT 記錄。 在域註冊商輸入 TXT 檔資訊時要小心。 如果錯誤地輸入了錯誤或重複的資訊，您必須等到 TTL 超時（60 分鐘）後才能重試。

## <a name="verify-your-custom-domain-name"></a>驗證自訂網域名稱

註冊自訂功能變數名稱後，請確保它在 Azure AD 中有效。 從域註冊商到 Azure AD 的傳播可以是即時的，也可以需要幾天時間，具體取決於域註冊商。

要驗證自訂功能變數名稱，請按照以下步驟操作：

1. 使用目錄的全域系統管理員帳戶登入 [Azure 入口網站](https://portal.azure.com/)。

1. 從任何頁面搜索並選擇*Azure 活動目錄*，然後選擇**自訂功能變數名稱**。

1. 在**自訂功能變數名稱**中，選擇自訂功能變數名稱。 在此示例中，選擇**contoso.com**。

    ![[Fabrikam - 自訂網域名稱] 頁面，已醒目提示 contoso](media/add-custom-domain/custom-blade-with-contoso-highlighted.png)

1. 在**contoso.com**頁上，選擇 **"驗證**"以確保自訂域已正確註冊且對 Azure AD 有效。

    ![包含 DNS 項目資訊和 [驗證] 按鈕的 Contoso 頁面](media/add-custom-domain/contoso-blade-with-dns-info-verify.png)

驗證自訂功能變數名稱後，可以刪除驗證 TXT 或 MX 檔。

## <a name="common-verification-issues"></a>常見驗證問題

如果 Azure AD 無法驗證自訂網域名稱，請嘗試下列建議：

- **至少等候一小時，然後再試一次**。 DNS 記錄必須在 Azure AD 驗證網域之後傳播。 此流程可能需要一個小時以上。

- **確定 DNS 記錄正確無誤。** 返回功能變數名稱註冊商網站。 請確保條目在那裡，並且它與 Azure AD 提供的 DNS 條目資訊匹配。

  如果您無法更新註冊商網站上的記錄，請與有權添加該條目並驗證該條目正確的人共用該條目。

- **確定網域名稱並未在另一個目錄中使用。** 功能變數名稱只能在一個目錄中驗證。 如果您的功能變數名稱當前在另一個目錄中已驗證，則無法在新目錄中驗證該功能變數名稱。 若要修正此重複問題，您必須從舊的目錄中刪除網域名稱。 如需如何刪除網域名稱的詳細資訊，請參閱 [管理自訂網域名稱](../users-groups-roles/domains-manage.md)。

- **確定您沒有任何非受控 Power BI 租用戶。** 如果您的使用者已透過自助式註冊啟用 Power BI 並針對貴組織建立非受控租用戶，您就必須使用 PowerShell 以內部或外部管理員身分接管管理。 如需詳細資訊，請參閱[如何以系統管理員身分接管 Azure Active Directory 中非受控目錄](../users-groups-roles/domains-admin-takeover.md)。

## <a name="next-steps"></a>後續步驟

- 將其他全域管理員新增至您的目錄。 如需詳細資訊，請參閱[如何指派角色和系統管理員](active-directory-users-assign-role-azure-portal.md)。

- 將使用者添加到域。 有關詳細資訊，請參閱[如何添加或刪除使用者](add-users-azure-active-directory.md)。

- 在 Azu e AD 中管理網域名稱資訊。 如需詳細資訊，請參閱[管理自訂網域名稱](../users-groups-roles/domains-manage.md)。

- 如果您有想要與 Azure Active Directory 一起使用的內部部署 Windows Server 版本，請參閱[整合內部部署目錄與 Azure Active Directory](../connect/active-directory-aadconnect.md)。
