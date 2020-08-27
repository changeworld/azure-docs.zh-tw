---
title: FortiGate 部署指南 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 FortiGate SSL VPN 之間的單一登入。
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
ms.openlocfilehash: 999e19ffad1d18e163881c844cbf30f8b7fef574
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2020
ms.locfileid: "88657053"
---
# <a name="fortigate-deployment-guide"></a>FortiGate 部署指南

## <a name="contents"></a>目錄

- 兌換 FortiGate 授權
- 下載韌體
- 部署 FortiGate VM
   - 設定靜態公用 IP 位址並指派完整網域名稱
   - 為 TCP 連接埠建立新的輸入網路安全性群組規則
- 建立 FortiGate 的自訂 Azure App
- 準備進行群組比對
   - 為使用者建立群組
- 設定 FortiGate VM
   - 安裝授權
   - 更新韌體
   - 將管理連接埠變更為 TCP
   - 上傳 Azure Active Directory SAML 簽署憑證
   - 上傳及設定自訂 SSL 憑證
   - 執行命令列設定
   - 建立 VPN 入口網站和防火牆原則
- 測試使用 Azure 來登入

## <a name="redeeming-the-fortigate-license"></a>兌換 FortiGate 授權

您可以透過 Azure IaaS 中的虛擬機器形式來使用 Fortinet FortiGate 新一代防火牆產品。 此虛擬機器有兩種授權模式 -

- 隨用隨付 (PAYG)
- 自備授權 (BYOL)

在與 Fortinet 合作以提供安全混合式存取 (SHA) 指引的同時，Fortinet 可能會提供授權給「Azure AD Get to Production SHA」小組的成員。 如果未提供任何授權，則也可以使用 PAYG 部署。

如果已發出授權，Fortinet 就會提供必須在線上兌換的註冊碼

![FortiGate SSL VPN](registration-code.png)

1. 在 https://support.fortinet.com/ 進行註冊
2. 註冊後，在 https://support.fortinet.com/ 登入
3. 瀏覽至 [資產] -> [註冊/啟用]
4. 輸入 Fortinet 所提供的註冊碼
5. 指定註冊碼，選取 [產品將會由非政府使用者使用]，然後按 [下一步]
6. 輸入產品描述 (例如 FortiGate)、將 Fortinet 合作夥伴設定為 [其他] -> [Microsoft]，然後按 [下一步]
7. 接受 [Fortinet 產品註冊合約]，然後按 [下一步]
8. 接受 [條款]，然後按一下 [確認]
9. 按一下 [授權檔案下載]，然後儲存授權以供稍後使用


## <a name="download-firmware"></a>下載韌體

在本文撰寫當下，Fortinet FortiGate Azure VM 並未隨附要進行 SAML 驗證所需的韌體版本。 您必須從 Fortinet 取得最新版本。

1. 在 https://support.fortinet.com/ 登入
2. 瀏覽至 [下載] -> [韌體映像]
3. 按一下 [版本資訊] 右邊的 [下載]
4. 按一下 [v6]。
5. 按一下 [6]。
6. 按一下 [6.4]。
7. 按一下位於同一列的 **HTTPS** 連結，以下載 **FGT_VM64_AZURE-v6-build1637-FORTINET.out**
8. 儲存檔案供稍後使用


## <a name="deploy-the-fortigate-vm"></a>部署 FortiGate VM

1. 瀏覽至 https://portal.azure.com ，然後登入您想要作為 FortiGate 虛擬機器部署目的地的訂用帳戶
2. 建立新的資源群組，或開啟您想要作為 FortiGate 虛擬機器部署目的地的資源群組
3. 按一下 [新增]
4. 在 [搜尋 Marketplace] 對話方塊中輸入「Forti」，然後選取 [Fortinet FortiGate 新一代防火牆] 
5. 選取軟體方案 (如果您有授權，就選取 BYOL，否則請選取 PAYG)，然後按一下 [建立]
6. 填入 VM 設定

    ![FortiGate SSL VPN](virtual-machine.png)

7. 將 [驗證類型] 設定為 [密碼]，然後提供 VM 的系統管理認證
8. 按一下 [檢閱 + 建立]
9. 按一下 [建立]
10. 等待 VM 部署完成


### <a name="set-a-statuc-public-ip-address-and-assign-a-fully-qualified-domain-name"></a>設定靜態公用 IP 位址並指派完整網域名稱

為了獲得一致的使用者體驗，建議您將指派給 FortiGate VM 的公用 IP 位址設定為靜態指派。 此外，基於同樣理由，將其對應到完整網域名稱也會有幫助。

_設定靜態公用 IP 位址_

1. 瀏覽至 https://portal.azure.com ，然後開啟 FortiGate VM 的設定
2. 在 [概觀] 畫面上，按一下 [公用 IP 位址]

    ![FortiGate SSL VPN](public-ip-address.png)

3. 按一下 [靜態]，然後按一下 [儲存]

_指派完整網域名稱_

針對即將部署 FortiGate VM 的環境，如果您擁有其可公開路由傳送的網域名稱，請為對應至前面所靜態指派公用 IP 位址的 VM 建立主機 (A) 記錄。

### <a name="create-a-new-inbound-network-security-group-rule-for-tcp-port"></a>為 TCP 連接埠建立新的輸入網路安全性群組規則

1. 瀏覽至 https://portal.azure.com ，然後開啟 FortiGate VM 的設定
2. 按一下左側功能表中的 [網路]。 隨即會列出網路介面，並顯示輸入連接埠規則
3. 按一下 [新增輸入連接埠規則]
4. 為 TCP 8443 建立新的輸入連接埠規則

    ![FortiGate SSL VPN](port-rule.png)

5. 按一下 [新增]


## <a name="create-a-custom-azure-app-for-fortigate"></a>建立 FortiGate 的自訂 Azure App

1. 瀏覽至 https://portal.azure.com ，然後開啟租用戶的 [Azure Active Directory] 刀鋒視窗，其將會提供用於登入 FortiGate 的身分識別
2. 按一下左側功能表中的 [企業應用程式]
3. 按一下 [新增應用程式]
4. 按一下 [不在資源庫內的應用程式]
5. 提供名稱 (例如 FortiGate)，然後按一下 [新增]
6. 按一下左側功能表中的 [使用者和群組]
7. 新增將能夠登入的使用者，然後按一下 [指派]
8. 按一下左側功能表中的 [單一登入]
9. 按一下 [SAML]
10. 在 [基本 SAML 設定] 底下，按一下鉛筆圖示以編輯設定
11. 設定
    - 識別碼 (實體識別碼) 為 `https://<address>/remote/saml/metadata`
    - 回覆 URL (判斷提示取用者服務 URL) 為 `https://<address>/remote/saml/login`
    - 登出 URL 為 `https://<address>/remote/saml/logout`

    其中 `address` 是指派給 FortiGate VM 的 FQDN 或公用 IP 位址

    記錄下面每個 URL 以供稍後使用 -

    - 實體識別碼
    - 回覆 URL
    - 登出 URL
12. 按一下 [儲存] 
13. 關閉 [基本 SAML 設定]
14. 在 [3 - SAML 簽署憑證] 底下下載 [憑證 (Base64)]，並加以儲存以供稍後使用
15. 在 [4 - 設定 (應用程式名稱)] 底下，複製 [Azure 登入 URL]、[Azure AD 識別碼] 和 [Azure 登出 URL]，並加以儲存以供稍後使用
    - Azure 登入 URL
    - Azure AD 識別碼
    - Azure 登出 URL
16. 在 [2 - 使用者屬性和宣告] 底下，按一下鉛筆圖示以編輯設定
17. 按一下 [新增宣告]
18. 將 [名稱] 設定為 [username]
19. 將 [來源屬性] 設定為 [user.userprincipalname]
20. 按一下 [儲存] 
21. 按一下 [新增群組宣告]
22. 選取 [所有群組]
23. 核取 [自訂群組宣告的名稱]
24. 將 [名稱] 設定為 [group]
25. 按一下 [儲存] 


## <a name="prepare-for-group-matching"></a>準備進行群組比對

視群組成員資格而定，FortiGate 允許登入之後有不同的使用者入口網站體驗。 例如，可以讓「行銷」群組有一種體驗，而讓「財務」群組有另一種體驗。

如下所示進行此設定 -

### <a name="create-groups-for-users"></a>為使用者建立群組

1. 瀏覽至 https://portal.azure.com ，然後開啟租用戶的 [Azure Active Directory] 刀鋒視窗，其將會提供用於登入 FortiGate 的身分識別
2. 按一下 [群組]
3. 按一下 [新增群組]
4. 使用下列條件建立群組
    - 群組類型 = 安全性
    - 群組名稱 = `a meaningful name`
    - 群組描述 = `a meaningful description for the group`
    - 成員資格類型 = 已指派
    - 成員 = `users for the user experience that will map to this group`
5. 針對任何其他使用者體驗重複步驟 3 和 4
6. 建立群組之後，請選取每個群組，並記錄其各自的物件識別碼
7. 儲存這些物件識別碼和群組名稱以供稍後使用


## <a name="configure-the-fortigate-vm"></a>設定 FortiGate VM

### <a name="install-the-license"></a>安裝授權

1. 巡覽到 `https://<address>`

    在這裡，`address` 是指派給 FortiGate VM 的 FQDN 或公用 IP 位址

2. 繼續跳過任何憑證錯誤
3. 使用 FortiGate VM 部署期間所提供的管理員認證來登入
4. 如果部署使用 BYOL 模型，則會顯示上傳授權的提示。 選取稍早建立的授權檔案並加以上傳、按一下 [確定]，然後重新啟動 FortiGate VM -

    ![FortiGate SSL VPN](license.png)

5. 重新開機之後，請使用管理員認證重新登入以驗證授權

### <a name="update-firmware"></a>更新韌體

1. 巡覽到 `https://<address>`

    在這裡，`address` 是指派給 FortiGate VM 的 FQDN 或公用 IP 位址

2. 繼續跳過任何憑證錯誤
3. 使用 FortiGate VM 部署期間所提供的管理員認證來登入
4. 在左側功能表中，按一下 [系統]
5. 在 [系統] 底下的左側功能表中，按一下 [韌體]
6. 在 [韌體管理] 頁面中，按一下 [瀏覽] 並選取稍早下載的韌體檔案
7. 忽略警告，然後按一下 [備份設定和升級] -

    ![FortiGate SSL VPN](backup-configure-upgrade.png)

8. 按一下 [繼續]
9. 當系統提示您儲存 FortiGate 設定時 (儲存為 .conf 檔案)，按一下 [儲存]
10. 等待韌體上傳以供套用，並等待 FortiGate VM 重新開機
11. FortiGate VM 重新開機之後，請使用管理員認證重新登入
12. 當系統提示您執行「儀表板設定」時，按一下 [稍後]
13. 當教學課程影片開始時，按一下 [確定]

### <a name="change-the-management-port-to-tcp"></a>將管理連接埠變更為 TCP

1. 巡覽到 `https://<address>`

    在這裡，`address` 是指派給 FortiGate VM 的 FQDN 或公用 IP 位址

2. 繼續跳過任何憑證錯誤
3. 使用 FortiGate VM 部署期間所提供的管理員認證來登入
4. 在左側功能表中，按一下 [系統]
5. 在 [系統管理設定] 底下，將 [HTTPS 連接埠] 變更為 [8443]
6. 按一下 [套用]。
7. 套用變更之後，瀏覽器會嘗試重新載入系統管理頁面，但會失敗。 從現在開始，系統管理頁面的位址將會變成 `https://<address>`

    ![FortiGate SSL VPN](certificate.png)

### <a name="upload-the-azure-active-directory-saml-signing-certificate"></a>上傳 Azure Active Directory SAML 簽署憑證

1. 巡覽到 `https://<address>`

    在這裡，`address` 是指派給 FortiGate VM 的 FQDN 或公用 IP 位址

2. 繼續跳過任何憑證錯誤
3. 使用 FortiGate VM 部署期間所提供的管理員認證來登入
4. 在左側功能表中，按一下 [系統]
5. 在 [系統] 底下，按一下 [憑證]
6. 按一下 [匯入] -> [遠端憑證]
7. 瀏覽至 Azure 租用戶中從 FortiGate 自訂應用程式部署下載的憑證、加以選取，然後按一下 [確定]

### <a name="upload-and-configure-a-custom-ssl-certificate"></a>上傳及設定自訂 SSL 憑證

您可能會想要使用支援您所用 FQDN 的自有 SSL 憑證來設定 FortiGate VM。 如果您可以存取以私密金鑰封裝的 SSL 憑證 (採用 .PFX 格式)，則可將其用於此用途

1. 巡覽到 `https://<address>`

    在這裡，`address` 是指派給 FortiGate VM 的 FQDN 或公用 IP 位址

2. 繼續跳過任何憑證錯誤
3. 使用 FortiGate VM 部署期間所提供的管理員認證來登入
4. 在左側功能表中，按一下 [系統]
5. 在 [系統] 底下，按一下 [憑證]
6. 按一下 [匯入] -> [本機憑證]
7. 按一下 [PKCS #12 憑證]
8. 瀏覽至包含 SSL 憑證和私密金鑰的 .PFX 檔案
9. 提供 .PFX 密碼
10. 為憑證提供有意義的名稱
11. 按一下 [檔案] &gt; [新增] &gt; [專案] 
12. 在左側功能表中，按一下 [系統]
13. 在 [系統] 底下，按一下 [設定]
14. 在 [系統管理設定] 底下，展開 [HTTPS 伺服器憑證] 旁的下拉式清單，然後選取前面匯入的 SSL 憑證
15. 按一下 [套用]。
16. 關閉瀏覽器視窗，然後再次瀏覽至 `https://<address>`
17. 使用 FortiGate 管理員認證來登入，並觀察使用中的正確 SSL 憑證


### <a name="perform-command-line-configuration"></a>執行命令列設定

_執行 SAML 驗證的命令列設定_

1. 瀏覽至 https://portal.azure.com ，然後開啟 FortiGate VM 的設定
2. 在左側功能表中，按一下 [序列主控台]
3. 在序列主控台使用 FortiGate VM 管理員認證來登入

    下一個步驟將需要稍早記錄的 URL。 也就是說 -

    - 實體識別碼
    - 回覆 URL
    - 登出 URL
    - Azure 登入 URL
    - Azure AD 識別碼
    - Azure 登出 URL
4. 在序列主控台執行下列命令 -

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
    > Azure 登出 URL 包含 ? 字元。 這需要特殊的按鍵順序，才能正確提供給 FortiGate 序列主控台。 URL 通常是 -

    `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1`

    若要在序列主控台提供此內容，請輸入下列命令來繼續進行

    ```
    set idp-single-logout-url https://login.microsoftonline.com/common/wsfederation
    ```
    然後輸入 CTRL+V，

    然後貼入 URL 的其餘部分，以完成該行命令

    ```
    set idp-single-logout-url
    https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.
    ```

5. 若要確認設定，請執行 -

    ```
    show user saml
    ```

_執行群組比對的命令列設定_

1. 瀏覽至 https://portal.azure.com ，然後開啟 FortiGate VM 的設定
2. 在左側功能表中，按一下 [序列主控台]
3. 在序列主控台使用 FortiGate VM 管理員認證來登入
4. 在序列主控台執行下列命令 -

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

    從「edit `group 1 name`」重複這些命令，但針對將在 FortiGate 中有不同入口網站體驗的每個其他群組來進行

_執行驗證逾時的命令列設定_

1. 瀏覽至 https://portal.azure.com ，然後開啟 FortiGate VM 的設定
2. 在左側功能表中，按一下 [序列主控台]
3. 在序列主控台使用 FortiGate VM 管理員認證來登入
4. 在序列主控台執行下列命令 -

    ```
    config system global
    set remoteauthtimeout 60
    end
    ```
### <a name="create-vpn-portals-and-firewall-policy"></a>建立 VPN 入口網站和防火牆原則

1. 巡覽到 `https://<address>`

    在這裡，`address` 是指派給 FortiGate VM 的 FQDN 或公用 IP 位址

2. 使用 FortiGate VM 部署期間所提供的管理員認證來登入
3. 在左側功能表中，按一下 [VPN]
4. 在 [VPN] 底下，按一下 [SSL-VPN 入口網站]
5. 按一下 [新建]
6. 提供名稱 (通常會讓其符合用來提供自訂入口網站體驗的 Azure 群組)
7. 按一下 [來源 IP 集區] 旁的加號 ( **+** )、選取 [預設集區]，然後按一下 [關閉]
8. 自訂此群組的體驗。 若要進行測試，您可以自訂入口網站訊息和佈景主題。 您也可以在這裡建立自訂書簽，以將使用者引導至內部資源
9. 按一下 [檔案] &gt; [新增] &gt; [專案] 
10. 針對每個將具有自訂入口網站體驗的 Azure 群組，重複執行步驟 5 到 9
11. 在 [VPN] 底下，按一下 [SSL-VPN 設定]
12. 按一下 [接聽介面] 旁的加號 ( **+** )
13. 選取 [Port1]，然後按一下 [關閉]


14. 如果先前已安裝自訂 SSL 憑證，請變更 [伺服器憑證] 以使用下拉式功能表中的自訂 SSL 憑證
15. 在 [驗證/入口網站對應] 底下，按一下 [新建]
16. 選擇第一個 Azure 群組，並將其與同名的入口網站對應
17. 按一下 [檔案] &gt; [新增] &gt; [專案] 
18. 針對每個 Azure 群組/入口網站配對重複執行步驟 15 到 17
19. 在 [驗證/入口網站對應] 底下，編輯 [所有其他使用者/群組]
20. 將入口網站設定為 [完整存取權]
21. 按一下 [檔案] &gt; [新增] &gt; [專案] 
22. 按一下 [套用]。
23. 捲動至 [SSL-VPN 設定] 頁面的頂端，然後按一下警告 [未存在任何 SSL-VPN 原則。
    請按一下這裡以使用這些設定來建立新的 SSL-VPN 原則]
24. 提供 **VPN Grp** 之類的名稱
25. 將 [傳出介面] 設定為 [連接埠]
26. 按一下 [來源]
27. 在 [位址] 底下，選取 [全部]
28. 在 [使用者] 底下，選取第一個 Azure 群組
29. 請按一下 [關閉]****。
30. 按一下 [目的地]
31. 在 [位址] 底下，這通常是內部網路。 選取 login.microsoft.com 來進行測試
32. 請按一下 [關閉]****。
33. 按一下 [服務]
34. 按一下 [全部]
35. 請按一下 [關閉]****。
36. 按一下 [檔案] &gt; [新增] &gt; [專案] 
37. 在左側功能表中，按一下 [原則和物件]
38. 在 [原則和物件] 底下，按一下 [防火牆原則]
39. 展開 [SSL-VPN 通道介面 (ssl.root)] -> [連接埠]
40. 以滑鼠右鍵按一下稍早建立的 VPN 原則 ( **VPN Grp 1** )，然後選取 [複製]
41. 在 VPN 原則底下按一下滑鼠右鍵，然後選取 [貼上] -> [下方]
42. 編輯新原則、為其提供不同的名稱 (例如 **VPN Grp2** )，然後變更群組適用對象 (另一個 Azure 群組)
43. 以滑鼠右鍵按一下新原則，然後將狀態設定為 [啟用]


## <a name="test-sign-in-using-azure"></a>測試使用 Azure 來登入

1. 使用 InPrivate 瀏覽器工作階段，瀏覽至 `https://<address>` 
2. 登入應該會重新導向至 Azure Active Directory 來進行登入
3. 在為已指派給 Azure 租用戶中 FortiGate 應用程式的使用者提供認證之後，就應該會顯示適當的使用者入口網站

    ![FortiGate SSL VPN](test-sign-in.png)
