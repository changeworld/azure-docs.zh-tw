---
title: 針對 Azure Active Directory 中的 SAML 型單一登入進行疑難排解
description: 針對針對 SAML 型單一登入設定的 Azure AD 應用程式問題進行疑難排解。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.openlocfilehash: 7f7f999c145903be5db3b20ab60bd0f5a18778ea
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89463449"
---
# <a name="troubleshoot-saml-based-single-sign-on-in-azure-active-directory"></a>針對 Azure Active Directory 中的 SAML 型單一登入進行疑難排解
如果您在設定應用程式時遇到問題。 請確認您已經依照應用程式教學課程中的所有步驟執行。 在應用程式的設定中，您擁有如何設定應用程式的內嵌文件。 此外，您可以存取[如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/)，以取得詳細的逐步指引。

## <a name="cant-add-another-instance-of-the-application"></a>無法新增應用程式的另一個執行個體
若要新增應用程式的第二個執行個體，您需要能夠：
-   設定第二個執行個體的唯一識別碼。 您無法設定已用於第一個執行個體的相同識別碼。
-   設定與第一個執行個體所使用的憑證不同的憑證。

如果應用程式不支援上述任一種方法。 則您將無法設定第二個執行個體。

## <a name="cant-add-the-identifier-or-the-reply-url"></a>無法新增識別碼或回覆 URL
如果您無法設定識別碼或回覆 URL，請確認識別碼和回覆 URL 值符合針對應用程式預先設定的模式。

了解針對應用程式預先設定的模式：
1. 開啟 [**Azure 入口網站**](https://portal.azure.com/) ，然後以 **全域系統管理員** 或共同管理員身分登入 **。** 移至步驟7。 如果您已經有位於 Azure AD 的應用程式設定刀鋒視窗中。
2. 按一下左側主導覽功能表底部的 [所有服務]，以開啟 [Azure Active Directory 延伸模組]。
3. 在篩選搜尋方塊中輸入 **“Azure Active Directory**”，然後選取 [Azure Active Directory] 項目。
4. 在 Azure Active Directory 左側導覽功能表中，按一下 [企業應用程式]。
5. 按一下 [所有應用程式]，以檢視所有應用程式的清單。
   * 若在這裡沒看到您要顯示的應用程式，請使用 [所有應用程式清單] 頂端的 [篩選] 控制項，並將 [顯示] 選項設定為 [所有應用程式]。
6. 選取您要設定單一登入的應用程式。
7. 應用程式載入後，按一下應用程式的左側導覽功能表中的 [單一登入]****。
8. 從 [模式]**** 下拉式清單選取 [SAML 登入]****。
9. 移至 [網域及 URL]**** 區段下方的 [識別碼]**** 或 [回覆 URL]**** 文字方塊。
10. 有三種方式可以知道應用程式支援的模式：
    * 在文字方塊中，您會看到支援的模式是以預留位置的形式出現 (範例︰** <https://contoso.com>)。
    * 如果模式不受支援，則當您嘗試在文字方塊中輸入值時，會看到紅色驚嘆號。 如果您將滑鼠移到紅色驚嘆號上方，就會看到支援的模式。
    * 在應用程式的教學課程中，您也可以取得支援模式的詳細資訊。 在**設定 Azure AD 單一登入**一節中。 移至在 [網域及 URL]**** 區段下方設定值的步驟。

如果值不符合 Azure AD 上預先設定的模式。 您可以：
-   洽詢應用程式廠商，以取得符合 Azure AD 上預先設定之模式的值
-   或者，您也可以連絡 Azure AD 小組 <aadapprequest@microsoft.com> ，或在教學課程中留言，以要求更新應用程式的支援模式

## <a name="where-do-i-set-the-entityid-user-identifier-format"></a>我可以在何處設定 EntityID (使用者識別碼) 格式
在使用者驗證之後，您將無法選取 Azure AD 要在回應中傳送至應用程式的 EntityID (使用者識別碼) 格式。

Azure AD 會根據應用程式在 SAML AuthRequest 中選取的值或要求的格式，來選取 NameID 屬性 (使用者識別碼) 的格式。 如需詳細資訊，請參閱[單一登入 SAML 通訊協定](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest)文章中的＜NameIDPolicy＞一節。

## <a name="cant-find-the-azure-ad-metadata-to-complete-the-configuration-with-the-application"></a>找不到 Azure AD 中繼資料以完成應用程式的設定
若要從 Azure AD 下載應用程式中繼資料或憑證，請遵循下列步驟：
1. 開啟 [**Azure 入口網站**](https://portal.azure.com/) ，然後以 **全域系統管理員** 或共同管理員身分登入 **。**
2. 按一下左側主導覽功能表底部的 [所有服務]，以開啟 [Azure Active Directory 延伸模組]。
3. 在篩選搜尋方塊中輸入 **“Azure Active Directory**”，然後選取 [Azure Active Directory] 項目。
4. 在 Azure Active Directory 左側導覽功能表中，按一下 [企業應用程式]。
5. 按一下 [所有應用程式]，以檢視所有應用程式的清單。
   * 若在這裡沒看到您要顯示的應用程式，請使用 [所有應用程式清單] 頂端的 [篩選] 控制項，並將 [顯示] 選項設定為 [所有應用程式]。
6. 選取您已設定單一登入的應用程式。
7. 應用程式載入後，按一下應用程式的左側導覽功能表中的 [單一登入]****。
8. 移至 [SAML 簽署憑證]**** 區段，然後按一下 [下載]**** 資料行值。 根據應用程式設定單一登入時所需的項目，您會看到下載中繼資料 XML 或憑證的選項。

Azure AD 不提供取得中繼資料的 URL。 只能將中繼資料擷取為 XML 檔案。

## <a name="customize-saml-claims-sent-to-an-application"></a>自訂傳送至應用程式的 SAML 宣告
若要了解如何自訂傳送至應用程式的 SAML 屬性宣告，請參閱 [Azure Active Directory 中的宣告對應](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping)以取得詳細資訊。

## <a name="next-steps"></a>後續步驟
* [應用程式管理快速入門系列](view-applications-portal.md)
