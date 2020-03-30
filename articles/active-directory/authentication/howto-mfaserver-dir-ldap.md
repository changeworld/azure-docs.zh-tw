---
title: LDAP 身份驗證和 Azure MFA 伺服器 - Azure 活動目錄
description: 部署 LDAP 驗證與 Azure Multi-Factor Authentication Server。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf7a5400fce2fce1b75cbd579830f929fcc0d0dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79454460"
---
# <a name="ldap-authentication-and-azure-multi-factor-authentication-server"></a>LDAP 驗證和 Azure Multi-Factor Authentication Server

根據預設，Azure Multi-Factor Authentication Server 會設定為從 Active Directory 匯入或同步處理使用者。 不過，它可以設定為繫結至不同 LDAP 目錄 (例如 ADAM 目錄) 或特定的 Active Directory 網域控制站。 在透過 LDAP 來連線到目錄時，Azure Multi-Factor Authentication Server 可作為 LDAP Proxy 來執行驗證。 它也支援使用 LDAP 繫結做為 RADIUS 目標、使用 IIS 驗證預先驗證使用者，或做為 Azure MFA 使用者入口網站中的主要驗證。

若要使用 Azure Multi-Factor Authentication 作為 LDAP Proxy，請在 LDAP 用戶端 (例如 VPN 應用裝置、應用程式) 和 LDAP 目錄伺服器之間插入 Azure Multi-Factor Authentication Server。 Azure Multi-Factor Authentication Server 必須設定為能夠與用戶端伺服器和 LDAP 目錄進行通訊。 在此組態中，Azure Multi-Factor Authentication Server 接受來自用戶端伺服器和應用程式的 LDAP 要求，然後轉送至目標 LDAP 目錄伺服器來驗證主要認證。 如果 LDAP 目錄認為主要認證有效，Azure Multi-Factor Authentication 會執行第二步身分識別驗證，然後將回應傳回給 LDAP 用戶端。 只有在 LDAP 伺服器驗證和第二步驗證皆成功時，整個驗證才會成功。

> [!IMPORTANT]
> 自 2019 年 7 月 1 日起，Microsoft 將不再為新部署提供 MFA 伺服器。 希望使用者進行多重要素驗證的新客戶應使用基於雲的 Azure 多重要素驗證。 在 7 月 1 日之前啟動 MFA 伺服器的現有客戶將能夠像往常一樣下載最新版本、將來的更新並生成啟動憑據。

## <a name="configure-ldap-authentication"></a>設定 LDAP 驗證

若要設定 LDAP 驗證，請在 Windows 伺服器上安裝 Azure Multi-Factor Authentication Server。 請使用下列程序：

### <a name="add-an-ldap-client"></a>新增 LDAP 用戶端

1. 在 Azure Multi-Factor Authentication Server 中，選取左功能表中的 [LDAP 驗證] 圖示。
2. 核取 [啟用 LDAP 驗證]**** 核取方塊。

   ![MFA 伺服器中的 LDAP 身份驗證](./media/howto-mfaserver-dir-ldap/ldap2.png)

3. 在"用戶端"選項卡上，如果 Azure 多重要素驗證 LDAP 服務應綁定到非標準埠以偵聽 LDAP 請求，請更改 TCP 埠和 SSL （TLS） 埠。
4. 如果計畫使用從用戶端到 Azure 多重要素驗證伺服器的 LDAPS，則必須在與 MFA 伺服器相同的伺服器上安裝 TLS/SSL 憑證。 按一下 **"流覽**"SSL （TLS） 證書框旁邊，然後選擇用於安全連線的證書。
5. 按一下 **[新增]**。
6. 在 [新增 LDAP 用戶端] 對話方塊中，輸入要向「伺服器」驗證的應用裝置、伺服器或應用程式的 IP 位址，以及應用程式名稱 (選擇性)。 應用程式名稱會出現在 Azure Multi-Factor Authentication 報表中，而且可能顯示在簡訊或行動應用程式驗證訊息內。
7. 如果所有使用者都已經或將要匯入到「伺服器」，且必須接受雙步驟驗證，請核取 [需要進行 Azure Multi-Factor Authentication 使用者比對]**** 方塊。 如果有大量使用者尚未匯入伺服器及/或將免除雙步驟驗證，請勿核取此方塊。 如需此功能的其他資訊，請參閱 MFA 伺服器說明檔。

重複上述步驟來新增其他 LDAP 用戶端。

### <a name="configure-the-ldap-directory-connection"></a>設定 LDAP 目錄連線

當 Azure Multi-Factor Authentication 設定為接受 LDAP 驗證時，它必須將這些驗證交由 LDAP 目錄代理。 因此，[目標] 索引標籤只會顯示灰色的單一選項來使用 LDAP 目標。

> [!NOTE]
> 目錄集成不保證與活動目錄域服務以外的目錄一起工作。

1. 若要設定 LDAP 目錄連線，請按一下 [目錄整合]**** 圖示。
2. 在 [設定] 索引標籤上，選取 [使用特定 LDAP 設定]**** 選項按鈕。
3. 選取 [編輯...]****
4. 在 [編輯 LDAP 設定] 對話方塊的欄位中，填入連接到 AD 目錄所需的資訊。 欄位說明包含在 Azure Multi-Factor Authentication Server 說明檔中。

    ![目錄集成 LDAP 配置](./media/howto-mfaserver-dir-ldap/ldap.png)

5. 按一下 [測試]**** 按鈕來測試 LDAP 連接。
6. 如果 LDAP 連接測試成功，請按一下 [確定]**** 按鈕。
7. 按一下 **"篩選器"** 選項卡。伺服器已預先配置為從活動目錄載入容器、安全性群組和使用者。 如果繫結至不同 LDAP 目錄，您可能需要編輯顯示的篩選。 如需篩選的詳細資訊，請按一下 [說明]**** 連結。
8. 按一下"**屬性"** 選項卡。伺服器已預先配置為從活動目錄映射屬性。
9. 如果要繫結至不同 LDAP 目錄，或要變更預先設定的屬性對應，請按一下 [編輯...]****
10. 在 [編輯屬性] 對話方塊中，修改您的目錄的 LDAP 屬性對應。 您可以輸入屬性名稱，或按一下每個欄位旁邊的 [...]**** 按鈕來選取。 如需屬性的詳細資訊，請按一下 [說明]**** 連結。
11. 按一下 [確定]**** 按鈕。
12. 按一下 [公司設定]**** 圖示，然後選取 [使用者名稱解析]**** 索引標籤。
13. 如果要從加入網域的伺服器連線到 Active Directory，請將 [使用 Windows 安全性識別碼 (SID) 來比對使用者名稱]**** 選項按鈕保持選取狀態。 否則，請選取 [使用 LDAP 唯一識別碼屬性來比對使用者名稱]**** 選項按鈕。

已選取 [使用 LDAP 唯一識別碼屬性來比對使用者名稱]**** 選項按鈕時，Azure Multi-factor Authentication Server 會嘗試將每個使用者名稱解析為 LDAP 目錄中的唯一識別碼。 LDAP 搜索在目錄集成 -> 屬性選項卡中定義的使用者名屬性上執行。當使用者進行身份驗證時，使用者名將解析為 LDAP 目錄中的唯一識別碼。 唯一識別碼可用來比對 Azure Multi-Factor Authentication 資料檔中的使用者。 這可進行不區分大小寫的比較，且允許完整或簡短的使用者名稱格式。

在完成這些步驟後，MFA Server 會在設定的連接埠上接聽來自所設定用戶端的 LDAP 存取要求，並且會作為 Proxy 將這些要求交由 LDAP 目錄進行驗證。

## <a name="configure-ldap-client"></a>設定 LDAP 用戶端

若要設定 LDAP 用戶端，請遵循下列指導方針：

* 將您的應用裝置、伺服器或應用程式設定為透過 LDAP 向 Azure Multi-Factor Authentication Server 驗證，就好像您的 LDAP 目錄一樣。 使用您平常直接連接到您的 LDAP 目錄時所用的相同設定，唯一的差別是使用 Azure Multi-Factor Authentication Server 的伺服器名稱或 IP 位址。
* 將 LDAP 逾時設定為 30 至 60 秒，以保留足夠的時間向 LDAP 目錄驗證使用者的認證、執行第二步驗證、接收回應，然後回應 LDAP 存取要求。
* 如果使用 LDAPS，則執行 LDAP 查詢的設備或伺服器必須信任 Azure 多重要素驗證伺服器上安裝的 TLS/SSL 憑證。
