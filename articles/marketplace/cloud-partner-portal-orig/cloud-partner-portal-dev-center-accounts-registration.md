---
title: 建立 Microsoft 開發人員帳戶 |Azure Marketplace
description: 建立 Microsoft 開發人員帳戶的需求與步驟。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 161abde1ef0dfd86842fb56afe699fa632e0d9ba
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80280366"
---
<a name="create-a-microsoft-developer-account"></a>建立 Microsoft 開發人員帳戶
====================================

此文章說明如何成為核准的 Microsoft 開發人員以進行 Azure Marketplace 發行。

## <a name="create-a-microsoft-account"></a>建立 Microsoft 帳戶

若要開始執行發行程序，您將需要完成 **Microsoft 開發人員中心**註冊。 您將使用在 **[Cloud Partner 入口網站](https://cloudpartner.azure.com/)** 上註冊的帳戶來開始執行發行程序。

### <a name="general-account-guidelines"></a>一般帳戶指導方針

我們建議您只為您的 Azure Marketplace 發行使用一個 Microsoft 帳戶。 此帳戶不應該限定於特定服務或供應項目。

構成使用者名稱的電子郵件地址應該位於您的網域中，並由您的 IT 小組所控制。 所有和發佈相關的活動都應透過此帳戶完成。

>[!WARNING]
>Microsoft 帳戶註冊不支援使用 "Azure" 與 "Microsoft" 之類的單字。 請避免使用這些字，以完成帳戶建立及註冊程序。

### <a name="company-account-guidelines"></a>公司帳戶指導方針

如果有多人需要以開啟該帳戶的 Microsoft 帳戶進行登入來存取該帳戶，請依照這些指導方針執行。

>[!IMPORTANT]
>若要允許多個使用者存取您的開發人員中心帳戶，建議使用 Azure Active Directory 將角色指派給個別使用者。 他們可以使用其個別 Azure AD 認證進行登入，以存取該帳戶。 如需詳細資訊，請參閱[管理帳戶使用者](https://docs.microsoft.com/windows/uwp/publish/manage-account-users)。

-   使用屬於貴公司網域，但不屬於單一個人的電子郵件地址，建立您的 Microsoft 帳戶。 例如，windowsapps\@fabrikam.com。
-   將此 Microsoft 帳戶的存取限制為可能最少的開發人員。
-   設定公司電子郵件通訊群組清單，其中包含每一位需要存取開發人員帳戶的使用者，並將此電子郵件地址新增至您的安全性資訊。 這可讓清單上的所有員工在必要時接收安全碼，以及管理您的 Microsoft 帳戶安全性資訊。 如果設定通訊群組清單不可行，則個人電子郵件帳戶的擁有者必須能在出現提示時存取及共用安全碼 (例如將新的安全性資訊新增至帳戶時，或是必須從新裝置存取安全性資訊時)。
-   新增重要小組成員可以存取的公司電話號碼 (不需要分機號碼)。
-   一般而言，讓程式開發人員使用受信任的裝置來登入公司的開發人員帳戶。 所有重要小組成員應該可以存取這些受信任的裝置。 這會降低存取帳戶時傳送安全碼的需求。
-   如果您需要允許從非信任的電腦帳戶存取，請將該存取限制為最多五個開發人員。 在理想情況下，這些開發人員應該從共用相同地理和網路位置的電腦存取該帳戶。
-   經常檢閱[貴公司的安全性資訊](https://account.live.com/proofs/Manage)，以確保資訊是最新的。

>[!IMPORTANT]
>您的開發人員帳戶主要應從受信任的電腦存取。 這點很重要，因為每個帳戶每週產生的安全碼數目有所限制。 此外，也可提供最順暢的登入體驗。
>
>如需詳細資訊，請參閱[其他開發人員帳戶指導方針與安全性](https://msdn.microsoft.com/windows/uwp/publish/opening-a-developer-account#additional-guidelines-for-company-accounts)。

### <a name="to-create-a-microsoft-account"></a>建立 Microsoft 帳戶

1.  開啟新的 Chrome Incognito 或 Internet Explorer InPrivate 瀏覽工作階段，確定您未登入現有帳戶。
2.  使用此[連結](https://signup.live.com/signup.aspx)將電子郵件 (使用先前的指導方針) 註冊為 Microsoft 帳戶。 完成下列註冊指示：

    - 將您的帳戶註冊為 Microsoft 帳戶時，您必須提供有效的電話號碼，讓系統能以簡訊或自動撥號傳送帳戶驗證碼。
    - 將您的帳戶註冊為 Microsoft 帳戶時，您必須提供有效的電子郵件識別碼來接收進行帳戶驗證的自動發送電子郵件。
    - 請確認傳送至 DL 的電子郵件地址。

    您現在可以在「Microsoft 開發人員中心」中開始使用新的 Microsoft 帳戶。

## <a name="register-your-account-in-microsoft-developer-center"></a>在 Microsoft 開發人員中心註冊您的帳戶

Microsoft 開發人員中心用於註冊一次公司資訊。 註冊者必須是公司的有效代表，而且必須提供個人資訊用來驗證其身分識別。 註冊的人員必須使用公司內共用的 Microsoft 帳戶，**而且在 Cloud Partner 入口網站中必須使用同一個帳戶。**  當您嘗試建立帳戶之前，應該先檢查公司是否確實未曾擁有 Microsoft 開發人員中心帳戶。 在這個程序期間，我們會收集公司地址資訊、銀行帳戶資訊和稅務資訊。 這些資訊通常可以從金融或商務連絡人處取得。

>[!IMPORTANT]
>您必須完成下列開發人員設定檔元件，才能進行供應項目建立和部署程序的各個階段。

| 開發人員設定檔     | 開始草擬    | 執行       | 免費發佈與解決方案範本   | 商業發佈   |
|---------------------- |----------------   |-----------    |-------------------------------------  |---------------------  |
| 公司註冊  | 必備         | 必備     | 必備                             | 必備             |
| 稅務設定檔識別碼        | 選用          | 選用      | 選用                              | 必備             |
| 銀行帳戶          | 選用          | 選用      | 選用                              | 必備             |

>[!NOTE]
>虛擬機器僅支援自備授權 (BYOL)，並將它視為免費供應項目。

### <a name="register-your-company-account"></a>註冊您的公司帳戶

1. 開啟新的 Internet Explorer InPrivate 或 Chrome Incognito 瀏覽工作階段，確定您未登入個人帳戶。

2. 移至[Windows 開發人員中心](https://dev.windows.com/registration?accountprogram=azure)，將您自己註冊為賣方。 在您繼續之前，請先閱讀下列重要事項。

   ![Microsoft 帳戶驗證](./media/cloud-partner-portal-create-dev-center-registration/seller-dashboard-verify.jpg)

    >[!IMPORTANT]
    >確定將在開發人員中心註冊的電子郵件識別碼或通訊群組清單 (為了不受限於個人使用，建議使用通訊群組清單) 已註冊為 Microsoft 帳戶。 如果還未註冊為 Microsoft 帳戶，請使用此連結註冊。 此外，不得使用隸屬於 Microsoft 公司網域的任何電子郵件識別碼在開發人員中心註冊。

   ![開發人員中心登入](./media/cloud-partner-portal-create-dev-center-registration/seller-dashboard-login.jpg)

3. 執行 [協助我們保護您的帳戶] 精靈，以使用電話號碼或電子郵件地址驗證您的身分識別。

4. 在 [註冊帳戶資訊] 中，從下拉式功能表選取您的 [帳戶國家/地區] ****，然後選取 [下一步]****。

   ![選取國家/地區](./media/cloud-partner-portal-create-dev-center-registration/imgRegisterCo_04.png)

    >[!WARNING]
    >「銷售來源」國家/地區：若要在 Azure Marketplace 上銷售您的服務，您的已註冊實體必須是下拉式清單中所顯示的其中一個已核准的「銷售來源」國家/地區。 這項限制是基於付款和稅務理由。 如需詳細資訊，請參閱 Marketplace 參與原則。

5. 針對您的 [帳戶類型] 選取 [公司]****，然後選取 [下一步]****。

    >[!IMPORTANT]
    >若要深入了解帳戶類型並判斷哪種類行最適合您，請檢閱下一個螢幕擷取畫面中的頁面 [帳戶類型]、位置與費用。

    ![賣方的帳戶類型](./media/cloud-partner-portal-create-dev-center-registration/imgRegisterCo_05.png)

6. 輸入 [發行者顯示名稱]****。 這通常是您的公司名稱。

    >[!NOTE]
    >您的供應項目列出後，在開發人員中心輸入的發行者顯示名稱並不會顯示在 Azure Marketplace 中。 但是需要此資訊才能完成註冊程序。

7. 輸入 **連絡資訊** 以進行帳戶驗證。

    >[!IMPORTANT]
    >您必須提供精確的連絡資訊，因為它將用於我們的驗證程序，讓您的公司在開發人員中心獲得核准。

8. 輸入 **公司核准者**的連絡資訊。 公司核准者是可以驗證您有權代表您的組織在開發人員中心建立帳戶的人員。 提供此資訊之後，請選取 [下一步]**** 以移至 [付款區段]****。

    ![識別公司核准者](./media/cloud-partner-portal-create-dev-center-registration/imgRegisterCo_08.png)

9. 輸入您帳戶的付款資訊。 如果您有涵蓋註冊成本的促銷代碼，您可以在此處輸入。 否則，請提供您的信用卡資訊 (或受支援市場的 PayPal)。 選取 [下一步]**** 以移至最終 [檢閱]****。

   ![付款註冊](./media/cloud-partner-portal-create-dev-center-registration/imgRegisterCo_09.png)

10. 檢閱您的帳戶資訊，並確認所有項目都正確。 閱讀並接受 [Microsoft Azure Marketplace 發佈者合約](https://go.microsoft.com/fwlink/?LinkID=699560)的條款及條件。 勾選方塊以表示您已閱讀並接受這些條款。

11. 選取 [完成]**** 以確認您的註冊。 我們將會傳送一則確認訊息到您的電子郵件地址。

12. 若您計畫只發行免費供應項目，請選取 [移至 Cloud Partner 入口網站](https://cloudpartner.azure.com/) 並跳到此文章中的「在 Cloud Partner 入口網站中註冊您的帳戶」。

### <a name="commercial-offers"></a>商業供應項目

若您計畫發行商業供應項目 (例如使用每小時計費模型的虛擬機器供應項目)，您必須提供稅務與銀行資訊。 若要這樣做，請登入您的開發人員中心帳戶並選取 [更新您的帳戶資訊]****。 依照下一節＜新增銀行與稅務資訊＞中的指示執行。

>[!IMPORTANT]
>若未提供銀行帳戶與稅務資訊，您無法將商業供應項目推送到生產環境。

若稍後才要更新您的銀行與稅務資訊，您可以跳到此文章中的「在 Cloud Partner 入口網站中註冊您的帳戶」。

>[!NOTE]
>我們建議您儘快提供銀行帳戶與稅務資訊，因未驗證稅務資訊需要時間。

### <a name="add-banking-and-tax-information"></a>新增銀行與稅務資訊

若要發佈商業供應項目供購買，您必須新增付款與稅務資訊，然後在開發人員中心中提交以供驗證。

**提供銀行資訊**

1.  使用您的 Microsoft 帳戶登入 [Microsoft 開發人員中心](https://dev.windows.com/registration?accountprogram=azure) 。
2.  選取左側功能表中的 [支付帳戶]****，在 [選擇付款方式]**** 下，選取 [銀行帳戶]**** 或 [PayPal]****。

    >[!NOTE]
    >如果您商業供應項目可供客戶在 Marketplace 購買，則這是您將收到購物付款金額的帳戶。
3.  輸入付款資訊，然後選取 [儲存]****。

    >[!IMPORTANT]
    >如果您需要更新或變更您的支付帳戶，請依照上面的步驟執行，但需以新資訊取代目前的資訊。
    >
    >變更您的付款帳戶會延遲您的付款最多一次付款週期。 會發生此延遲是因為我們必須確認帳戶變更，就像我們在您第一次設定付款帳戶時所做的一樣。 您仍然會在帳戶確認之後收到全額款項。當期付款週期未付的款項會新增至下一期。

4.  選取 [下一步]  。

**提供稅務資訊**

1.  使用您的 Microsoft 帳戶登入 [Microsoft 開發人員中心](https://dev.windows.com/registration?accountprogram=azure) (視需要)。
2.  在左側功能表上，選取 [稅務設定檔]****。
3.  在 [ 設定您的稅單]**** 頁面上：
    - 選取您永久居住的國家或地區。
    - 選取您持有主要公民身分的國家或地區。
    - 選取 [下一步]  。
4.  輸入稅務詳細資料，然後選取 [下一步]****。

>[!WARNING]
>如果沒有在 Microsoft 開發人員中心帳戶中提供銀行帳戶與稅務資訊，則您無法將商業供應項目推送到生產環境。

### <a name="developer-center-registration-issues"></a>開發人員中心註冊問題

如果在開發人員中心註冊時發生問題，請使用下列步驟建立支援票證。

1.  移至[支援連結](https://developer.microsoft.com/windows/support)。
2.  在 [與我們連絡]**** 下，選取 [提交事件]**** 按鈕。
    ![開啟票證](./media/cloud-partner-portal-create-dev-center-registration/imgAddTax_02.png)
3.  針對 [問題類型]**** 選取 [開發人員中心協助]，並針對 [類別]**** 選取 [發行和管理應用程式]。 選取 [開始電子郵件]****。

    ![識別您的問題類型](./media/cloud-partner-portal-create-dev-center-registration/imgAddTax_03.png)

4.  會為您提供登入頁面。 使用任一 Microsoft 帳戶登入。 如果您沒有 Microsoft 帳戶，則請[建立一個](https://signup.live.com/signup?uaid=0089f09ccae94043a0f07c2aaf928831&lic=1)。

5.  提供問題的相關詳細資訊，然後選取 [提交]**** 以傳送票證。

    ![提交票證](./media/cloud-partner-portal-create-dev-center-registration/imgAddTax_05.png)

## <a name="register-your-account-in-the-cloud-partner-portal"></a>在 Cloud Partner 入口網站中註冊您的帳戶

您可以使用 [Cloud Partner 入口網站](https://cloudpartner.azure.com/)用來發佈及管理您的供應項目。

1.  開啟新的 Chrome Incognito 或 Internet Explorer InPrivate 瀏覽工作階段，確定您尚未登入個人帳戶。
2.  移至 [Cloud Partner 入口網站](https://cloudpartner.azure.com/)。
3.  如果您是第一次登入 [Cloud Partner 入口網站](https://cloudpartner.azure.com/)的新使用者，您必須使用註冊開發人員中心帳戶所用的相同電子郵件識別碼登入。 這樣可確保您的開發人員中心帳戶與 Cloud Partner 入口網站帳戶彼此連結。

稍後您可以新增公司中使用該應用程式的其他成員。 您可以依照下一節中的步驟，在 Cloud Partner 入口網站中將他們指派為參與者或擁有者。

如果已將您新增成為 Cloud Partner 入口網站中的參與者/擁有者，您便可以使用自己的帳戶登入。

>[!TIP]
>您可以在 Azure 網站上找到有關參與原則的說明。

## <a name="manage-users-as-owners-or-contributors-in-the-cloud-partner-portal"></a>以 Cloud Partner 入口網站中的擁有者或參與者角色管理使用者

[在 Cloud Partner 入口網站上管理使用者的步驟](./cloud-partner-portal-manage-users.md)


## <a name="next-steps"></a>後續步驟

現在，您的帳戶已建立並註冊完成，您可以啟動 Azure Marketplace 發佈程序。
