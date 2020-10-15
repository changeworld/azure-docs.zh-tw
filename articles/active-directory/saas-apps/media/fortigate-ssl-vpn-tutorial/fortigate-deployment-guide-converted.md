---
title: FortiGate 部署指南 | Microsoft Docs
description: 設定並使用 Fortinet FortiGate 新一代防火牆產品。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 18a3d9d5-d81c-478c-be7e-ef38b574cb88
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 08/11/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 357eb0a60e6246996de9ab75337ecc213d845ae7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91273325"
---
# <a name="fortigate-deployment-guide"></a>FortiGate 部署指南

使用本部署指南，您將了解如何設定並使用 Fortinet FortiGate 新一代防火牆產品。

## <a name="redeem-the-fortigate-license"></a>兌換 FortiGate 授權

您可以透過 Azure 基礎結構即服務 (IaaS) 中的虛擬機器形式來使用 Fortinet FortiGate 新一代防火牆產品。 此虛擬機器有兩種授權模式：隨用隨付和自備授權。

Fortinet 可能會為 Azure Active Directory (Azure AD) 的「生產安全混合式存取 (SHA)」小組成員提供授權。 如果未提供任何授權，則也可以使用隨用隨付部署。

如果已發出授權，Fortinet 就會提供必須在線上兌換的註冊碼。

![FortiGate SSL VPN 註冊碼的螢幕擷取畫面。](registration-code.png)

1. 在 https://support.fortinet.com/ 進行註冊。
2. 註冊後，請在 https://support.fortinet.com/ 登入。
3. 移至 [資產] > [註冊/啟用]。
4. 輸入 Fortinet 所提供的註冊碼。
5. 指定註冊碼，選取 [產品將會由非政府使用者使用]，然後選取 [下一步]。
6. 輸入產品說明 (例如 FortiGate)，將 Fortinet 合作夥伴設定為 [其他] > [Microsoft]，然後選取 [下一步]。
7. 接受 [Fortinet 產品註冊合約]，然後選取 [下一步]。
8. 接受 [條款]，然後選取 [確認]。
9. 選取 [授權檔案下載]，然後儲存授權以供後續使用。


## <a name="download-firmware"></a>下載韌體

目前，Fortinet FortiGate Azure VM 並未隨附進行 SAML 驗證所需的韌體版本。 您必須從 Fortinet 取得最新版本。

1. 在 https://support.fortinet.com/ 登入。
2. 移至 [下載] > [韌體映像]。
3. 在 [版本資訊] 右側，選取 [下載]。
4. 選取 **v6.** > **6.** > **6.4.** 。
5. 選取位於同一列的 **HTTPS** 連結，以下載 **FGT_VM64_AZURE-v6-build1637-FORTINET.out**。
6. 儲存檔案供後續使用。


## <a name="deploy-the-fortigate-vm"></a>部署 FortiGate VM

1. 移至 https://portal.azure.com ，然後登入您要在其中部署 FortiGate 虛擬機器的訂用帳戶。
2. 建立新的資源群組，或開啟您要在其中部署 FortiGate 虛擬機器的資源群組。
3. 選取 [新增]。
4. 在 [搜尋 Marketplace]，輸入 *Forti*。 選取 [Fortinet FortiGate 新一代防火牆]。
5. 選取軟體方案 (若有授權請選取自備授權，若沒有則選取隨用隨付)。 選取 [建立]  。
6. 填入 VM 設定。

    ![建立虛擬機器的螢幕擷取畫面。](virtual-machine.png)

7. 將 [驗證類型] 設定為 [密碼]，然後提供 VM 的系統管理認證。
8. 選取 [檢閱 + 建立] > [建立]。
9. 等待 VM 部署完成。


### <a name="set-a-static-public-ip-address-and-assign-a-fully-qualified-domain-name"></a>設定靜態公用 IP 位址並指派完整網域名稱

為了獲得一致的使用者體驗，請將指派給 FortiGate VM 的公用 IP 位址設定為靜態指派。 此外，請將其對應至完整網域名稱 (FQDN)。

1. 移至 https://portal.azure.com ，然後開啟 FortiGate VM 的設定。
2. 在 [概觀] 畫面上，選取公用 IP 位址。

    ![Fortigate SSL VPN 的螢幕擷取畫面。](public-ip-address.png)

3. 選取 [靜態] > [儲存]。

針對即將部署 FortiGate VM 的環境，如果您擁有其可公開路由傳送的網域名稱，請為 VM 建立主機 (A) 記錄。 此記錄會對應至先前靜態指派的公用 IP 位址。

### <a name="create-a-new-inbound-network-security-group-rule-for-tcp-port"></a>為 TCP 連接埠建立新的輸入網路安全性群組規則

1. 移至 https://portal.azure.com ，然後開啟 FortiGate VM 的設定。
2. 在左側功能表中，選取 [網路]。 此時會列出網路介面，並顯示輸入連接埠規則。
3. 選取 [新增輸入連接埠規則]。
4. 為 TCP 8443 建立新的輸入連接埠規則。

    ![新增輸入安全性規則的螢幕擷取畫面。](port-rule.png)

5. 選取 [新增]。


## <a name="create-a-custom-azure-app-for-fortigate"></a>建立 FortiGate 的自訂 Azure App

1. 移至 https://portal.azure.com ，然後開啟租用戶的 [Azure AD] 窗格，此窗格會提供用於 FortiGate 登入的身分識別。
2. 在左側功能表中，選取 [企業應用程式]。
3. 選取 [新增應用程式] > [非資源庫應用程式]。
4. 輸入名稱 (例如 FortiGate)，然後選取 [新增]。
5. 在左側功能表中，選取 [使用者和群組]****。
6. 新增將能夠登入的使用者，然後選取 [指派]。
7. 在左側功能表中，選取 [單一登入]。
8. 選取 [SAML]。
9. 在 [基本 SAML 設定] 底下，選取鉛筆圖示以編輯設定。
10. 設定下列各項：
    - [識別碼 (實體識別碼)] 為 `https://<address>/remote/saml/metadata`。
    - [回覆 URL (判斷提示取用者服務 URL)] 為 `https://<address>/remote/saml/login`。
    - [登出 URL] 為 `https://<address>/remote/saml/logout`。

    `<address>` 是指派給 FortiGate VM 的 FQDN 或公用 IP 位址。

11. 記錄以下每個 URL 以供後續使用：[實體識別碼]、[回覆 URL] 和 [登出 URL]。
12. 選取 [儲存]，然後關閉 [基本 SAML 設定]。
13. 在 [3 - SAML 簽署憑證] 底下，下載 [憑證 (Base64)] 並儲存，以供後續使用。
14. 在 [4 - 設定 (應用程式名稱)] 底下，複製 [Azure 登入 URL]、[Azure AD 識別碼] 和 [Azure 登出 URL]，並儲存以供後續使用。
15. 在 [2 - 使用者屬性和宣告] 底下，選取鉛筆圖示以編輯設定。
16. 選取 [新增宣告]，並將名稱設定為 **username**。
17. 將來源屬性設定為 [user.userprincipalname]。
18. 選取 [儲存] > [新增群組宣告] > [所有群組]。
19. 選取 [自訂群組宣告的名稱]，並將名稱設定為 **group**。
20. 選取 [儲存]。


## <a name="prepare-for-group-matching"></a>準備進行群組比對

視群組成員資格而定，FortiGate 允許登入之後有不同的使用者入口網站體驗。 例如，可以讓行銷群組有一種體驗，而讓財務群組有另一種體驗。 以下是建立使用者群組的方式：

1. 移至 https://portal.azure.com ，然後開啟租用戶的 [Azure AD] 窗格，此窗格會提供用於 FortiGate 登入的身分識別。
2. 選取 [群組] > [新增群組]。
3. 建立具有下列詳細資料的群組：
    - 群組類型 = 安全性
    - 群組名稱 = `a meaningful name`
    - 群組描述 = `a meaningful description for the group`
    - 成員資格類型 = 已指派
    - 成員 = `users for the user experience that will map to this group`
4. 針對任何其他使用者體驗重複步驟 3 和 4。
5. 在建立群組之後，選取每個群組，並記錄其各自的 [物件識別碼]。
6. 儲存這些物件識別碼和群組名稱以供後續使用。


## <a name="configure-the-fortigate-vm"></a>設定 FortiGate VM

以下各節將逐步引導您了解如何設定 FortiGate VM。

### <a name="install-the-license"></a>安裝授權

1. 前往 `https://<address>`。 在這裡，`<address>` 是指派給 FortiGate VM 的 FQDN 或公用 IP 位址。

2. 繼續跳過任何憑證錯誤。
3. 使用在 FortiGate VM 部署期間提供的管理員認證進行登入。
4. 如果部署使用自備授權模型，您將會看到上傳授權的提示。 選取先前建立的授權檔案，並將加以上傳。 選取 [確定] 並重新啟動 FortiGate VM。

    ![FortiGate VM 授權的螢幕擷取畫面。](license.png)

5. 重新開機後，請使用管理員認證重新登入以驗證授權。

### <a name="update-firmware"></a>Update firmware

1. 前往 `https://<address>`。 在這裡，`<address>` 是指派給 FortiGate VM 的 FQDN 或公用 IP 位址。

2. 繼續跳過任何憑證錯誤。
3. 使用在 FortiGate VM 部署期間提供的管理員認證進行登入。
4. 在左側功能表中，選取 [系統] > [韌體]。
5. 在 [韌體管理] 中選取 [瀏覽]，然後選取先前下載的韌體檔案。
6. 忽略警告，然後選取 [備份設定和升級]。

    ![[韌體管理] 的螢幕擷取畫面。](backup-configure-upgrade.png)

7. 選取 [繼續]。
8. 在系統提示您儲存 FortiGate 設定時 (儲存為 .conf 檔案)，選取 [儲存]。
9. 等候韌體上傳並套用。 等候 FortiGate VM 重新開機。
10. FortiGate VM 重新開機後，請使用管理員認證重新登入。
11. 在系統提示您設定儀表板時，選取 [稍後]。
12. 在教學課程影片開始時，選取 [確定]。

### <a name="change-the-management-port-to-tcp"></a>將管理連接埠變更為 TCP

1. 前往 `https://<address>`。 在這裡，`<address>` 是指派給 FortiGate VM 的 FQDN 或公用 IP 位址。

2. 繼續跳過任何憑證錯誤。
3. 使用在 FortiGate VM 部署期間提供的管理員認證進行登入。
4. 在左側功能表中，選取 [系統]。
5. 在 [系統管理設定] 底下，將 HTTPS 連接埠變更為 **8443**，然後選取 [套用]。
6. 套用變更後，瀏覽器會嘗試重新載入系統管理頁面，但會失敗。 從現在開始，系統管理頁面的位址將是 `https://<address>`。

    ![[上傳遠端憑證] 的螢幕擷取畫面。](certificate.png)

### <a name="upload-the-azure-ad-saml-signing-certificate"></a>上傳 Azure AD SAML 簽署憑證

1. 前往 `https://<address>`。 在這裡，`<address>` 是指派給 FortiGate VM 的 FQDN 或公用 IP 位址。

2. 繼續跳過任何憑證錯誤。
3. 使用在 FortiGate VM 部署期間提供的管理員認證進行登入。
4. 在左側功能表中，選取 [系統] > [憑證]。
5. 選取 [匯入] > [遠端憑證]。
6. 瀏覽至從 Azure 租用戶中的 FortiGate 自訂應用程式部署下載的憑證。 加以選取，然後選取 [確定]。

### <a name="upload-and-configure-a-custom-ssl-certificate"></a>上傳及設定自訂 SSL 憑證

您可以使用支援您所用 FQDN 的自有 SSL 憑證來設定 FortiGate VM。 如果您可以存取以私密金鑰封裝的 SSL 憑證 (採用 .PFX 格式)，則可將其用於此用途。

1. 前往 `https://<address>`。 在這裡，`<address>` 是指派給 FortiGate VM 的 FQDN 或公用 IP 位址。

2. 繼續跳過任何憑證錯誤。
3. 使用在 FortiGate VM 部署期間提供的管理員認證進行登入。
4. 在左側功能表中，選取 [系統] > [憑證]。
5. 選取 [匯入] > [本機憑證] > [PKCS #12 憑證]。
6. 瀏覽至包含 SSL 憑證和私密金鑰的 .PFX 檔案。
7. 提供 .PFX 密碼，以及有意義的憑證名稱。 然後選取 [確定]。
8. 在左側功能表中，選取 [系統] > [設定]。
9. 在 [系統管理設定] 底下，展開 [HTTPS 伺服器憑證] 旁的清單，然後選取先前匯入的 SSL 憑證。
10. 選取 [套用]。
11. 關閉瀏覽器視窗，並移至 `https://<address>`。
12. 使用 FortiGate 管理員認證登入。 您現在應該會看到正確的 SSL 憑證正在使用中。


### <a name="perform-command-line-configuration"></a>執行命令列設定

以下各節將提供使用命令列進行各種設定的步驟。

#### <a name="for-saml-authentication"></a>針對 SAML 驗證

1. 移至 https://portal.azure.com ，然後開啟 FortiGate VM 的設定。
2. 在左側功能表中，選取 [序列主控台]。
3. 在序列主控台使用 FortiGate VM 管理員認證進行登入。 下一個步驟將需要您先前記錄的 URL：
    - 實體識別碼
    - 回覆 URL
    - 登出 URL
    - Azure 登入 URL
    - Azure AD 識別碼
    - Azure 登出 URL
4. 在序列主控台執行下列命令：

    ```
    config user saml
    edit azure
    set entity-id <Entity ID>
    set single-sign-on-url <Reply URL>
    set single-logout-url <Logout URL>
    set idp-single-sign-on-url <Azure Login URL>
    set idp-entity-id <Azure AD Identifier>
    set idp-single-logout-url <Azure Logout URL>
    set idp-cert REMOTE_Cert_
    set user-name username
    set group-name group
    end
    ```
    > [!NOTE]
    > Azure 登出 URL 包含 `?` 字元。 這需要特殊的按鍵順序，才能正確提供給 FortiGate 序列主控台。 URL 通常是 `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1`。 若要在序列主控台中提供這項資料，請輸入：
        ```
        set idp-single-logout-url https://login.microsoftonline.com/common/wsfederation
        ```.
    然後，輸入 CTRL+V 並貼入 URL 的其餘部分，以完成以下這一行： 
        ```
        set idp-single-logout-url
        https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.
        ```

5. 若要確認設定，請執行下列動作：

    ```
    show user saml
    ```

#### <a name="for-group-matching"></a>針對群組比對

1. 移至 https://portal.azure.com ，然後開啟 FortiGate VM 的設定。
2. 在左側功能表中，選取 [序列主控台]。
3. 在序列主控台使用 FortiGate VM 管理員認證進行登入。
4. 在序列主控台執行下列命令：

    ```
    config user group
    edit <group 1 name>
    set member azure
    config match
    edit 1
    set server-name azure
    set group-name <group 1 Object Id>
    next
    end
    next
    ```

    針對在 FortiGate 中會有不同入口網站體驗的每個其他群組，請重複執行這些命令 (從程式碼的第二行開始)。

#### <a name="for-authentication-timeout"></a>針對驗證逾時

1. 移至 https://portal.azure.com ，然後開啟 FortiGate VM 的設定。
2. 在左側功能表中，選取 [序列主控台]。
3. 在序列主控台使用 FortiGate VM 管理員認證進行登入。
4. 在序列主控台執行下列命令：

    ```
    config system global
    set remoteauthtimeout 60
    end
    ```
### <a name="create-vpn-portals-and-firewall-policy"></a>建立 VPN 入口網站和防火牆原則

1. 前往 `https://<address>`。 在這裡，`<address>` 是指派給 FortiGate VM 的 FQDN 或公用 IP 位址。

2. 使用在 FortiGate VM 部署期間提供的管理員認證進行登入。
3. 在左側功能表中，選取 [VPN] > [SSL-VPN 入口網站] > [新建]。
6. 提供名稱 (通常會讓其符合用來提供自訂入口網站體驗的 Azure 群組)。
7. 選取 [來源 IP 集區] 旁的加號 ( **+** )，再選取預設集區，然後選取 [關閉]。
8. 自訂此群組的體驗。 若要進行測試，您可以自訂入口網站訊息和佈景主題。 您也可以在這裡建立自訂書籤，以將使用者導向至內部資源。
9. 選取 [確定]。
10. 針對每個將具有自訂入口網站體驗的 Azure 群組，重複執行步驟 5-9。
11. 在 [VPN] 底下，選取 [SSL-VPN 設定]。
12. 選取 [接聽介面] 旁的加號 ( **+** )，再選取 **Port1**，然後選取 [關閉]。
14. 如果您先前已安裝自訂 SSL 憑證，請變更 [伺服器憑證] 以使用下拉式功能表中的自訂 SSL 憑證。
15. 在 [驗證/入口網站對應] 底下，選取 [新建]。 選擇第一個 Azure 群組，並將其與同名的入口網站對應。 然後選取 [確定]。
18. 針對 Azure 群組和入口網站的每個配對，重複執行步驟 15-17。
19. 在 [驗證/入口網站對應] 底下，編輯 [所有其他使用者/群組]。
20. 將入口網站設定為 [完整存取]，然後選取 [確定] > [套用]。
23. 捲動至 [SSL-VPN 設定] 頁面頂端，然後選取 [未存在任何 SSL-VPN 原則。請按一下這裡以使用這些設定來建立新的 SSL-VPN 原則]。
24. 提供 **VPN Grp** 之類的名稱。 接著，將 [輸出介面] 設定為 [連接埠]，然後選取 [來源]。
27. 在 [位址] 底下，選取 [全部]。
28. 在 [使用者] 底下，選取第一個 Azure 群組。
29. 選取 [關閉] > [目的地]。 在 [位址] 底下，這通常是內部網路。 選取 [login.microsoft.com] 以進行測試。
32. 選取 [關閉] > [服務] > [全部]。 然後，選取 [關閉] > [確定]。
37. 在左側功能表中，選取 [原則和物件] > [防火牆原則]。
39. 展開 [SSL-VPN 通道介面 (ssl.root)] > [連接埠]。
40. 以滑鼠右鍵按一下先前建立的 VPN 原則 (**VPN Grp 1**)，然後選取 [複製]。
41. 在 VPN 原則底下按一下滑鼠右鍵，然後選取 [貼上] > [下方]。
42. 編輯新的原則，並為其提供不同的名稱 (例如 **VPN Grp2**)。 同時請變更其適用的群組 (變更為另一個 Azure 群組)。
43. 以滑鼠右鍵按一下新原則，然後將狀態設定為 [啟用]。


## <a name="test-sign-in-by-using-azure"></a>使用 Azure 測試登入

1. 使用 InPrivate 瀏覽器工作階段，移至 `https://<address>`。  
2. 登入應該會重新導向至 Azure AD 以進行登入。
3. 在為已指派給 Azure 租用戶中 FortiGate 應用程式的使用者提供認證之後，就應該會出現適當的使用者入口網站。

    ![FortiGate SSL VPN 的螢幕擷取畫面](test-sign-in.png)
