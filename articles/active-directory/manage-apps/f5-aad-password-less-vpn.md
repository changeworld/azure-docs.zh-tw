---
title: 使用 F5 VPN Azure AD 安全的混合式存取 |Microsoft Docs
description: Azure Active Directory 單一登入 (SSO) 與無密碼 VPN 的 F5 大型 IP 整合的教學課程
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 10/12/2020
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2961f3f01f6ea4398fab6144b34fcb4409cdd96f
ms.sourcegitcommit: e5f9126c1b04ffe55a2e0eb04b043e2c9e895e48
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96317957"
---
# <a name="tutorial-for-azure-active-directory-single-sign-on-integration-with-f5-big-ip-for-password-less-vpn"></a>適用于無密碼 VPN 的 Azure Active Directory 單一登入與 F5 BIG IP 整合的教學課程

在本教學課程中，您將瞭解如何整合 F5 大型 IP 型安全通訊端層虛擬私人網路 (SSL-VPN) 解決方案，以及 Azure Active Directory (AD) ，以取得安全的混合式存取 (SHA) 。

將 BIG IP SSL-VPN 與 Azure AD 整合提供 [許多重要的優點](f5-aad-integration.md)，包括：

- 透過[Azure AD 預先驗證與授權，](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization)改進了零信任治理

- [對 VPN 服務進行無密碼驗證](https://www.microsoft.com/security/business/identity/passwordless)

- 從單一控制平面管理身分識別和存取- [Azure 入口網站](https://portal.azure.com/#home)

儘管新增了這些絕佳的價值，但傳統的 VPN 仍會前提網路周邊的概念，其中受信任的位於內部且不受信任的外部。 此模型在達成真正的零信任狀態時已不再有效，因為公司資產不再局限于企業資料中心的牆，而是在不含固定界限的多雲端環境中。 基於這個理由，我們鼓勵客戶考慮採用更多身分識別導向的方法來管理 [每個應用程式的存取權](https://docs.microsoft.com/azure/active-directory/fundamentals/five-steps-to-full-application-integration-with-azure-ad)。

## <a name="scenario-description"></a>案例描述

在此案例中，SSL VPN 服務的大型 IP APM 實例將會設定為 SAML 服務提供者 (SP) ，而 Azure AD 會成為受信任的 SAML IDP，提供預先驗證。 單一登入 (來自 Azure AD 的 SSO) 接著會透過以宣告為基礎的驗證方式提供給大型 IP APM，提供順暢的 VPN 存取體驗。

![影像顯示 ssl-vpn 架構](media/f5-sso-vpn/ssl-vpn-architecture.png)

>[!NOTE]
>本指南中所參考的所有範例字串或值都應取代為實際環境的字串或值。

## <a name="prerequisites"></a>必要條件

不過，您不需要對 F5 BIG IP 進行事先經驗或知識，您需要：

- Azure AD [免費訂](https://azure.microsoft.com/trial/get-started-active-directory/) 用帳戶或更高版本

- 使用者身分識別應該 [從其內部部署目錄同步](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis) 處理至 Azure AD。

- 具有 Azure AD 應用程式系統管理員[許可權](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#application-administrator)的帳戶

- 現有的大型 IP 基礎結構，可將用戶端流量路由傳送至大型 IP，或將 [大型 Ip 虛擬版本部署至 Azure](f5-bigip-deployment-guide.md)。

- 大型 IP 已發佈 VPN 服務的記錄必須存在於公用 DNS 中，或是測試用戶端的 localhost 檔案（測試時）

- 大型 IP 應使用必要的 SSL 憑證來布建，以便透過 HTTPS 發行服務。

熟悉您自己的 [F5 大型 IP 術語](https://www.f5.com/services/resources/glossary) 也將有助於瞭解整個教學課程中所參考的各種元件。

>[!NOTE]
>如果您在本指南中的指示和您在 Azure 入口網站中看到的內容有任何差異，則 Azure 不斷演進，因此不會感到驚訝。 不過，螢幕擷取畫面來自于大型 IP v15，與從 v5.2 保持相當類似。

## <a name="add-f5-big-ip-from-the-azure-ad-gallery"></a>從 Azure AD 資源庫新增 F5 BIG IP

在大 IP 間設定 SAML 同盟信任，可讓 Azure AD 的大型 IP 將預先驗證及 [條件式存取](https://docs.microsoft.com/azure/active-directory/conditional-access/overview) 交給 Azure AD，然後再授與已發佈 VPN 服務的存取權。

1. 使用具有應用程式系統管理員許可權的帳戶登入 Azure AD 入口網站

2. 從左方的流覽窗格中，選取 **Azure Active Directory 服務**

3. 移至 [ **企業應用程式** ]，然後從頂端功能區選取 [ **新增應用程式**]。

4. 在資源庫中搜尋 F5，然後選取 [ **F5 BIG-IP APM Azure AD 整合**]。

5. 提供應用程式的名稱，然後使用 [ **新增/建立** ]，將它新增至您的租使用者。 使用者可以在 Azure 和 Office 365 應用程式入口網站中看到名稱為圖示。 名稱應該會反映該特定服務。 例如，VPN。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

1. 在 view 中使用新的 F5 應用程式屬性，移至 [**管理**  >  **單一登入**]

2. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。 選取 [ **否，稍後再儲存]，** 略過提示以儲存單一登入設定。

3. 在 [ **以 SAML 設定單一登入** ] 功能表上，選取 [ **基本 SAML** 設定] 的 [畫筆] 圖示，以提供下列詳細資料：

   - 以您的大型 IP 已發佈服務的 URL 取代預先定義的 **識別碼 url** 。 例如， `https://ssl-vpn.contoso.com`

   - 在 [ **回復 URL** ] 文字方塊中進行相同的動作，包括 SAML 端點路徑。 例如， `https://ssl-vpn.contoso.com/saml/sp/profile/post/acs`

   - 在這項設定中，應用程式會以 IDP 起始模式運作，其中 Azure AD 會在重新導向至大型 IP SAML 服務之前，先使用 SAML 判斷提示來發出使用者。 針對不支援 IDP 起始模式的應用程式，請指定大型 IP SAML 服務的登 **入 URL** 。 例如： `https://ssl-vpn.contoso.com` 。

   - 在 [登出 Url] 中，輸入要發行之服務的主機標頭預先擱置的大型 IP APM 單一登出 (SLO) 端點。 例如， `https://ssl-vpn.contoso.com/saml/sp/profile/redirect/slr`

   提供 SLO URL 可確保使用者在使用者登出之後，會在兩端、大 IP 和 Azure AD 終止使用者會話。大型 IP APM 也提供在呼叫特定應用程式 URL 時終止所有會話的 [選項](https://support.f5.com/csp/article/K12056) 。

![顯示基本 saml 設定的影像](media/f5-sso-vpn/basic-saml-configuration.png).

>[!NOTE]
>從 TMOS v16，SAML SLO 端點已變更為/saml/sp/profile/redirect/slo

4. 選取 [ **儲存** ]，然後結束 [SAML 設定] 功能表，並略過 SSO 測試提示。

觀察 & 宣告] 區段的 [ **使用者屬性** ] 的屬性，因為 Azure AD 會將這些內容發行給使用者進行大型 IP APM 驗證。

![顯示使用者屬性宣告的影像](media/f5-sso-vpn/user-attributes-claims.png)

您可以視需要新增您的大型 IP 已發佈服務所預期的任何其他特定宣告，但請注意，除了預設設定以外，任何定義的宣告都只會在 Azure AD 中，以填入的屬性來發出。 同樣地，目錄 [角色或群組](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-fed-group-claims) 成員資格也需要針對 Azure AD 中的使用者物件進行定義，才能將它們當作宣告來發出。

![影像顯示同盟中繼資料下載連結](media/f5-sso-vpn/saml-signing-certificate.png)

Azure AD 所建立的 SAML 簽署憑證有三年的存留期，因此需要使用 Azure AD 發佈的指導方針來管理。

### <a name="azure-ad-authorization"></a>Azure AD 授權

根據預設，Azure AD 只會將權杖發行給已獲得服務存取權的使用者。

1. 仍在應用程式的 [設定] 視圖中，選取 [**使用者和群組**]

2. 選取 [ **+ 新增使用者**]，然後在 [新增指派] 功能表中選取 [**使用者和群組**]

3. 在 [**使用者和群組**] 對話方塊中，新增已獲授權可存取 VPN 的使用者群組，然後 **選取**[  >  **指派**]。

![顯示新增使用者連結的影像 ](media/f5-sso-vpn/add-user-link.png)

4. 這會完成 SAML 同盟信任的 Azure AD 部分。 您現在可以設定大型 IP APM 以發佈 SSL VPN 服務，並使用一組對應的屬性設定來完成 SAML 預先驗證的信任。

## <a name="big-ip-apm-configuration"></a>大型 IP APM 設定

### <a name="saml-federation"></a>SAML 同盟

下一節會建立大型 IP SAML 服務提供者，以及將 VPN 服務與 Azure AD 完成同盟所需的對應 SAML IDP 物件。

1. 移至 [**存取**  >  **同盟**  >  **SAML 服務提供者**  >  **本機 SP 服務**]，然後選取 [**建立**]

![顯示大型 IP SAML 設定的影像](media/f5-sso-vpn/bigip-saml-configuration.png)

2. 輸入您稍早在 Azure AD 中定義的 **名稱** 和相同的 **實體識別碼** 值，以及將用來連線到應用程式的主機 FQDN

![顯示如何建立新的 SAML SP 服務的影像](media/f5-sso-vpn/create-new-saml-sp.png)

   只有當實體識別碼與已發佈 URL 的主機名稱部分完全相符，或不是以主機名稱為基礎的 URL 格式時，才需要 SP **名稱** 設定。 如果實體識別碼為，請提供要發佈之應用程式的外部配置和主機名稱 `urn:ssl-vpn:contosoonline` 。

3. 向下滾動以選取新的 **SAML SP 物件** ，並選取 [系結 **/解除系結 IDP 連接器**]。

![影像顯示如何建立與本機 SP 服務的同盟](media/f5-sso-vpn/federation-local-sp-service.png)

4. 選取 [**建立新的 IDP 連接器**]，然後從下拉式功能表中選取 [**從中繼資料**]

![影像顯示建立新的 IDP 連接器](media/f5-sso-vpn/create-new-idp-connector.png)

5. 流覽至您稍早下載的同盟中繼資料 XML 檔案，並針對將代表外部 SAML 的 APM 物件提供身分 **識別提供者名稱** IDP

6. 選取 [ **加入新** 的資料列]，以選取新的 AZURE AD external IDP connector。

![顯示外部 IDP 連接器的影像](media/f5-sso-vpn/external-idp-connector.png)

7. 選取 [ **更新** ] 以將 saml SP 物件系結至 saml IDP 物件，然後選取 **[確定]**。

![影像顯示使用 SP 的 SAML IDP](media/f5-sso-vpn/saml-idp-using-sp.png)

### <a name="webtop-configuration"></a>Webtop 設定

下列步驟可讓您透過大型 IP 的專屬入口網站提供 SSL VPN 給使用者。

1. 移至 **Access**  >  **Webtops**  >  **Webtop 清單**，然後選取 [**建立**]。

2. 提供入口網站的名稱，並將類型設定為 [ **完整**]。 例如： `Contoso_webtop` 。

3. 調整其餘的喜好設定，然後選取 [ **完成**]。

![顯示 webtop 設定的影像](media/f5-sso-vpn/webtop-configuration.png)

### <a name="vpn-configuration"></a>VPN 設定

VPN 功能是由數個元素所組成，每個元素都控制整體服務的不同層面。

1. 移至 [**存取** 連線  >  **能力/vpn**  >  **網路存取 (VPN)**  >  **IPV4 租用** 集區，然後選取 [**建立**]。

2. 針對要配置給 VPN 用戶端的 IP 位址集區，提供名稱。 例如，Contoso_vpn_pool

3. 將 [類型] 設定為 [ **IP 位址範圍** ]，然後提供 [開始] 和 [結束 IP]，接著 **新增** 和 **完成**。

![影像顯示 vpn 設定](media/f5-sso-vpn/vpn-configuration.png)

網路存取清單會使用 VPN 集區中的 IP 和 DNS 設定來布建服務、使用者路由許可權，也可以視需要啟動應用程式。

1. 移至 **存取** 連線  >  **能力/vpn：網路存取 (VPN)**  >  **網路存取清單**，然後選取 [**建立**]。

2. 提供 VPN 存取清單的名稱和標題，例如 Contoso-VPN，然後是 [ **已完成**]。

![映射顯示網路存取清單中的 vpn 設定](media/f5-sso-vpn/vpn-configuration-network-access-list.png)

3. 從頂端功能區選取 [ **網路設定** ]，並新增下列設定：

   • **支援的 IP 版本**： IPV4

   • **IPV4 租用集** 區：選取稍早建立的 VPN 集區，例如 Contoso_vpn_pool

![影像顯示 contoso vpn 集區](media/f5-sso-vpn/contoso-vpn-pool.png)

   用戶端設定選項可用來在建立 VPN 時，對用戶端流量的路由方式強制執行限制。

4. 選取 [ **完成** ] 並移至 [DNS/主機] 索引標籤，以新增設定：

   • **IPV4 主要名稱伺服器**：環境 DNS 伺服器的 IP

   • **DNS 預設網域尾碼**：此特定 VPN 連接的網域尾碼。 例如，contoso.com

![影像顯示預設網域尾碼](media/f5-sso-vpn/domain-suffix.png)

[F5 文章](https://techdocs.f5.com/kb/en-us/products/big-ip_apm/manuals/product/apm-network-access-11-5-0/2.html) 提供根據您的喜好調整其餘設定的詳細資料。

現在需要大型 IP 連線設定檔，才能針對 VPN 服務需要支援的每個 VPN 用戶端類型設定設定。 例如，Windows、OSX 和 Android。

1. 移至 [**存取** 連線  >  **能力/VPN** 連線  >  **能力**  >  **設定檔**]，然後選取 [**新增**]。

2. 提供設定檔名稱，並將父設定檔設定為 **/Common/connectivity**，例如 Contoso_VPN_Profile。

![影像顯示建立新的連線設定檔](media/f5-sso-vpn/create-connectivity-profile.png)

F5 [檔](https://techdocs.f5.com/kb/en-us/bigip-edge-apps.html) 提供用戶端支援的詳細資料。

## <a name="access-profile-configuration"></a>存取設定檔設定

設定 VPN 物件之後，需要有存取原則，才能啟用服務進行 SAML 驗證。

1. 前往 **存取**  >  **設定檔/原則**  >  **存取設定檔 (每個會話原則)** 然後選取 [**建立**]

2. 提供設定檔名稱，並針對配置檔案類型選取 [ **全部**]，例如 Contoso_network_access

3. 向下滾動以將至少一個語言新增至 [**接受的語言**] 清單，然後選取 [**完成**]。

![影像顯示一般屬性](media/f5-sso-vpn/general-properties.png)

4. 在新存取設定檔的 Per-Session 原則] 欄位上選取 [ **編輯** ]，讓視覺化原則編輯器在不同的瀏覽器索引標籤中啟動。

![顯示每個會話原則的影像](media/f5-sso-vpn/per-session-policy.png)

5. 選取 [ **+** 簽署]，然後在快顯的 [選取 **驗證**  >  **SAML** 驗證]  >  **新增專案** 中選取。

6. 在 [SAML 驗證 SP 設定] 中，選取您稍早建立的 VPN SAML SP 物件，然後按一下 [ **儲存**]。

![顯示 saml 驗證的影像](media/f5-sso-vpn/saml-authentication.png)

7. **+** 針對 SAML 驗證的成功分支進行選取。

8. 從 [指派] 索引標籤中選取 [ **Advanced Resource Assign** ]，然後選取 [**新增專案**]

![影像顯示預先指派的資源](media/f5-sso-vpn/advance-resource-assign.png)

9. 在快顯視窗中，選取 [ **新增專案** ]，然後按一下 [新增 **/刪除**]。

10. 在子視窗中，選取 [ **網路存取** ]，然後選取稍早建立的網路存取設定檔

![影像顯示加入新的網路存取專案](media/f5-sso-vpn/add-new-entry.png)

11. 切換至 [ **webtop** ] 索引標籤，然後新增先前建立的 webtop 物件。

![顯示新增 webtop 物件的影像](media/f5-sso-vpn/add-webtop-object.png)

12. 選取 [ **更新** ]，然後按一下 [ **儲存**]。

13. 選取 [上方拒絕] 方塊中的連結，將成功的分支變更為 [ **允許** ]，然後 **儲存**。

![影像顯示新的視覺效果原則編輯器](media/f5-sso-vpn/vizual-policy-editor.png)

14. 選取 [套用 **存取原則** ]，然後關閉 [視覺效果原則編輯器] 索引標籤，以認可這些設定。

![顯示新的存取原則管理員的影像](media/f5-sso-vpn/access-policy-manager.png)

## <a name="publish-the-vpn-service"></a>發佈 VPN 服務

設定好之後，APM 現在需要前端虛擬伺服器來接聽連接到 VPN 的用戶端。

1. 選取 [**本機流量**  >  **虛擬伺服器**  >  **虛擬伺服器] 清單**，然後選取 [**建立**]。

2. 提供 VPN 虛擬伺服器的 **名稱** ，例如 **VPN_Listener**。

3. 提供具有未使用 **IP 目的地位址** 的虛擬伺服器，以接收用戶端流量進行路由

4. 將服務埠設定為 **443 HTTPS** ，並確定狀態顯示為 **已啟用**

![顯示新虛擬伺服器的影像](media/f5-sso-vpn/new-virtual-server.png)

5. 將 **Http 設定檔** 設定為 HTTP，然後將 SSL 設定檔新增至您在必要條件中布建的公用 SSL 憑證 (用戶端) 。

![顯示 ssl 設定檔的影像](media/f5-sso-vpn/ssl-profile.png)

6. 在 [存取原則] 底下，將 **存取設定檔** 和連線 **設定檔** 設定為使用已建立的 VPN 物件。

![影像顯示存取原則](media/f5-sso-vpn/access-policy.png)

7. 完成時選取 [ **完成** ]。

8.  您的 SSL VPN 服務現在已發佈並可透過 SHA 存取，可直接透過其 URL 或透過 Microsoft 的應用程式入口網站來存取。

## <a name="additional-resources"></a>其他資源

- [密碼結束時，請前往無密碼](https://www.microsoft.com/security/business/identity/passwordless)

- [何謂條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Microsoft 零信任 framework 來啟用遠端工作](https://www.microsoft.com/security/blog/2020/04/02/announcing-microsoft-zero-trust-assessment-tool/)

- [完整應用程式與 Azure AD 整合的五個步驟](https://docs.microsoft.com/azure/active-directory/fundamentals/five-steps-to-full-application-integration-with-azure-ad)

## <a name="next-steps"></a>後續步驟

在遠端 Windows 用戶端上開啟瀏覽器，並流覽至 **大型 IP VPN 服務** 的 url。 向 Azure AD 驗證之後，您會看到大型 IP 的 webtop 入口網站和 VPN 啟動器。

![顯示 vpn 啟動器的影像](media/f5-sso-vpn/vpn-launcher.png)

選取 [VPN] 磚將會安裝大型 IP Edge 用戶端，並建立為 SHA 設定的 VPN 連線。
您也應該在 Azure AD 條件式存取中，以目標資源的形式顯示 F5 VPN 應用程式。 請參閱我們的 [指導](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policies) 方針來建立條件式存取原則，也可讓使用者 Azure AD 無 [密碼驗證](https://www.microsoft.com/security/business/identity/passwordless)。