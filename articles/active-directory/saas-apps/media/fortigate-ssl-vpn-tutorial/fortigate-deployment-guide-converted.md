---
title: FortiGate 部署指南 | Microsoft Docs
description: 設定並使用 Fortinet FortiGate 新一代防火牆產品。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/30/2020
ms.author: jeedes
ms.openlocfilehash: cdaa6a9601452100ab90ef8b0f2191002f256b74
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025428"
---
# <a name="fortigate-azure-virtual-machine-deployment-guide"></a>FortiGate Azure 虛擬機器部署指南

使用本部署指南，您將了解如何設定及使用部署為 Azure 虛擬機器的 Fortinet FortiGate 新一代防火牆產品。 此外，您將設定 FortiGate SSL VPN Azure AD 資源庫應用程式，以透過 Azure Active Directory 提供 VPN 驗證。

## <a name="redeem-the-fortigate-license"></a>兌換 FortiGate 授權

您可以透過 Azure 基礎結構即服務 (IaaS) 中的虛擬機器形式來使用 Fortinet FortiGate 新一代防火牆產品。 此虛擬機器有兩種授權模式：隨用隨付和自備授權 (BYOL)。

如果您已從 Fortinet 購買 FortiGate 授權以用於 BYOL 虛擬機器部署選項，請從 Fortinet 的產品啟用頁面 (https://support.fortinet.com ) 加以兌換。 產生的授權檔案會有 .lic 副檔名。

## <a name="download-firmware"></a>下載韌體

在本文撰寫當下，Fortinet FortiGate Azure VM 並未隨附要進行 SAML 驗證所需的韌體版本。 您必須從 Fortinet 取得最新版本。

1. 在 https://support.fortinet.com/ 登入。
2. 移至 [下載] > [韌體映像]。
3. 在 [版本資訊] 右側，選取 [下載]。
4. 選取 **v6.00** > **6.4** > **6.4.2**。
5. 選取位於同一列的 **HTTPS** 連結，以下載 **FGT_VM64_AZURE-v6-build1723-FORTINET.out**。
6. 儲存檔案供後續使用。

## <a name="deploy-the-fortigate-vm"></a>部署 FortiGate VM

1. 移至 Azure 入口網站，然後登入您要在其中部署 FortiGate 虛擬機器的訂用帳戶。
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

1. 移至 Azure 入口網站，然後開啟 FortiGate VM 的設定。
2. 在 [概觀] 畫面上，選取公用 IP 位址。

    ![Fortigate SSL VPN 的螢幕擷取畫面。](public-ip-address.png)

3. 選取 [靜態] > [儲存]。

針對即將部署 FortiGate VM 的環境，如果您擁有其可公開路由傳送的網域名稱，請為 VM 建立主機 (A) 記錄。 此記錄會對應至先前靜態指派的公用 IP 位址。

### <a name="create-a-new-inbound-network-security-group-rule-for-tcp-port-8443"></a>為 TCP 連接埠 8443 建立新的輸入網路安全性群組規則

1. 移至 Azure 入口網站，然後開啟 FortiGate VM 的設定。
2. 在左側功能表中，選取 [網路]。 此時會列出網路介面，並顯示輸入連接埠規則。
3. 選取 [新增輸入連接埠規則]。
4. 為 TCP 8443 建立新的輸入連接埠規則。

    ![新增輸入安全性規則的螢幕擷取畫面。](port-rule.png)

5. 選取 [新增]。

## <a name="create-a-second-virtual-nic-for-the-vm"></a>為 VM 建立第二個虛擬 NIC

若要讓使用者可以使用內部資源，必須將第二個虛擬 NIC 新增至 FortiGate VM。 在 Azure 中，虛擬 NIC 所在的虛擬網路必須具有對這些內部資源的可路由連線。

1. 移至 Azure 入口網站，然後開啟 FortiGate VM 的設定。
2. 如果 FortiGate VM 尚未停止，請選取 [停止]，並等候 VM 關機。
3. 在左側功能表中，選取 [網路]。
4. 選取 [連結網路介面]。
5. 選取 [建立及連結網路介面]。
6. 設定新網路介面的屬性，然後選取 [建立]。

    ![建立網路介面的螢幕擷取畫面。](new-network-interface.png)

7. 啟動 FortiGate VM。


## <a name="configure-the-fortigate-vm"></a>設定 FortiGate VM

以下各節將逐步引導您了解如何設定 FortiGate VM。

### <a name="install-the-license"></a>安裝授權

1. 前往 `https://<address>`。 在這裡，`<address>` 是指派給 FortiGate VM 的 FQDN 或公用 IP 位址。

2. 繼續跳過任何憑證錯誤。
3. 使用在 FortiGate VM 部署期間提供的管理員認證進行登入。
4. 如果部署使用自備授權模型，您將會看到上傳授權的提示。 選取先前建立的授權檔案，並將加以上傳。 選取 [確定] 並重新啟動 FortiGate VM。

    ![FortiGate VM 授權的螢幕擷取畫面。](license.png)

5. 重新開機後，請使用管理員認證重新登入以驗證授權。

### <a name="update-firmware"></a>更新韌體

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

### <a name="change-the-management-port-to-tcp-8443"></a>將管理連接埠變更為 TCP 8443

1. 前往 `https://<address>`。 在這裡，`<address>` 是指派給 FortiGate VM 的 FQDN 或公用 IP 位址。

2. 繼續跳過任何憑證錯誤。
3. 使用在 FortiGate VM 部署期間提供的管理員認證進行登入。
4. 在左側功能表中，選取 [系統]。
5. 在 [系統管理設定] 底下，將 HTTPS 連接埠變更為 **8443**，然後選取 [套用]。
6. 套用變更後，瀏覽器會嘗試重新載入系統管理頁面，但會失敗。 從現在開始，系統管理頁面的位址將是 `https://<address>:8443`。

    ![[上傳遠端憑證] 的螢幕擷取畫面。](certificate.png)

### <a name="upload-the-azure-ad-saml-signing-certificate"></a>上傳 Azure AD SAML 簽署憑證

1. 前往 `https://<address>:8443`。 在這裡，`<address>` 是指派給 FortiGate VM 的 FQDN 或公用 IP 位址。

2. 繼續跳過任何憑證錯誤。
3. 使用在 FortiGate VM 部署期間提供的管理員認證進行登入。
4. 在左側功能表中，選取 [系統] > [憑證]。
5. 選取 [匯入] > [遠端憑證]。
6. 瀏覽至從 Azure 租用戶中的 FortiGate 自訂應用程式部署下載的憑證。 加以選取，然後選取 [確定]。

### <a name="upload-and-configure-a-custom-ssl-certificate"></a>上傳及設定自訂 SSL 憑證

您可以使用支援您所用 FQDN 的自有 SSL 憑證來設定 FortiGate VM。 如果您可以存取以私密金鑰封裝的 SSL 憑證 (採用 .PFX 格式)，則可將其用於此用途。

1. 前往 `https://<address>:8443`。 在這裡，`<address>` 是指派給 FortiGate VM 的 FQDN 或公用 IP 位址。

2. 繼續跳過任何憑證錯誤。
3. 使用在 FortiGate VM 部署期間提供的管理員認證進行登入。
4. 在左側功能表中，選取 [系統] > [憑證]。
5. 選取 [匯入] > [本機憑證] > [PKCS #12 憑證]。
6. 瀏覽至包含 SSL 憑證和私密金鑰的 .PFX 檔案。
7. 提供 .PFX 密碼，以及有意義的憑證名稱。 然後選取 [確定]。
8. 在左側功能表中，選取 [系統] > [設定]。
9. 在 [系統管理設定] 底下，展開 [HTTPS 伺服器憑證] 旁的清單，然後選取先前匯入的 SSL 憑證。
10. 選取 [套用]。
11. 關閉瀏覽器視窗，並移至 `https://<address>:8443`。
12. 使用 FortiGate 管理員認證登入。 您現在應該會看到正確的 SSL 憑證正在使用中。

### <a name="configure-authentication-timeout"></a>設定驗證逾時

1. 移至 Azure 入口網站，然後開啟 FortiGate VM 的設定。
2. 在左側功能表中，選取 [序列主控台]。
3. 在序列主控台使用 FortiGate VM 管理員認證進行登入。
4. 在序列主控台執行下列命令：

    ```
    config system global
    set remoteauthtimeout 60
    end
    ```

### <a name="ensure-network-interfaces-are-obtaining-ip-addresses"></a>確定網路介面正在取得 IP 位址

1. 前往 `https://<address>:8443`。 在這裡，`<address>` 是指派給 FortiGate VM 的 FQDN 或公用 IP 位址。

2. 使用在 FortiGate VM 部署期間提供的管理員認證進行登入。
3. 在左側功能表中，選取 [網路]。
4. 在 [網路] 底下，選取 [介面]。
5. 檢查 port1 (外部介面) 和 port2 (內部介面)，以確定兩者皆從正確的 Azure 子網路取得 IP 位址。
    a. 如果任一連接埠未從子網路取得 IP 位址 (透過 DHCP)，請以滑鼠右鍵按一下連接埠，然後選取 [編輯]。
    b. 在 [定址模式] 旁，確定已選取 [DHCP]。
    c. 選取 [確定]。

    ![網路介面定址的螢幕擷取畫面。](addressing.png)

### <a name="ensure-fortigate-vm-has-correct-route-to-on-premises-corporate-resources"></a>確定 FortiGate VM 具有對內部部署公司資源的正確路由

多重主目錄 Azure VM 的所有網路介面皆位於相同的虛擬網路上 (但可能在個別的子網路中)。 這通常表示兩個網路介面皆連線至透過 FortiGate 發佈的內部部署公司資源。 因此，您必須建立自訂路由項目，以確保在進行對內部部署公司資源的要求時，流量會從正確的介面連出。

1. 前往 `https://<address>:8443`。 在這裡，`<address>` 是指派給 FortiGate VM 的 FQDN 或公用 IP 位址。

2. 使用 FortiGate VM 部署期間所提供的管理員認證來登入。
3. 在左側功能表中，選取 [網路]。
4. 在 [網路] 底下，選取 [靜態路由]。
5. 選取 [新建]。
6. 在 [目的地] 旁，選取 [子網路]。
7. 在 [子網路] 底下，指定內部部署公司資源所在的子網路資訊 (例如 10.1.0.0/255.255.255.0)
8. 在 [閘道位址] 旁，指定 port2 所連線的 Azure 子網路上的閘道 (例如，這通常會以 1 結尾，像是 10.6.1.1)
9. 在 [介面] 旁，選取內部網路介面 port2
10. 選取 [確定]。

    ![設定路由的螢幕擷取畫面。](route.png)

## <a name="configure-fortigate-ssl-vpn"></a>設定 FortiGate SSL VPN

請依照 https://docs.microsoft.com/azure/active-directory/saas-apps/fortigate-ssl-vpn-tutorial 所列的步驟操作
