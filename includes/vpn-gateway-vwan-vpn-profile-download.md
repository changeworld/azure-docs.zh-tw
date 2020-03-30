---
title: 包含檔案
description: 包含檔案
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/17/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c61378510fbfc8bdc13f35ba1063a0d9316d88e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066199"
---
## <a name="1-download-the-file"></a>1. 下載檔案

執行下列命令。 將結果 URL 複製到瀏覽器以下載設定檔 ZIP 檔案。

```azurepowershell-interactive
$profile = New-AzVpnClientConfiguration -ResourceGroupName AADAuth -Name AADauthGW -AuthenticationMethod "EapTls"
   
$PROFILE.VpnProfileSASUrl
```

## <a name="2-extract-the-zip-file"></a>2. 提取 ZIP 檔案

解壓縮 zip 檔案。 該檔包含以下資料夾：

* AzureVPN
* 泛型
* OpenVPN（如果已啟用閘道上的 OpenVPN 和 Azure AD 身份驗證設置。 有關 VPN 閘道，請參閱[創建租戶](../articles/vpn-gateway/openvpn-azure-ad-tenant.md)。 對於虛擬 WAN，請參閱[創建租戶 - VWAN](../articles/virtual-wan/openvpn-azure-ad-tenant.md)。

## <a name="3-retrieve-information"></a>3. 檢索資訊

在**AzureVPN**資料夾中，導航到***azurevpnconfig.xml***檔，然後使用記事本打開該檔。 記下以下標記之間的文本。

```
<audience>          </audience>
<issuer>            </issuer>
<tennant>           </tennant>
<fqdn>              </fqdn>
<serversecret>      </serversecret>
```

## <a name="profile-details"></a>設定檔詳細資訊

添加連接時，請使用上一步中收集的設定檔詳細資訊頁的資訊。 這些欄位對應于以下資訊：

   * **受眾：** 標識權杖的收件者資源
   * **發行人：** 標識發出權杖的安全權杖服務 （STS） 以及 Azure AD 租戶
   * **租戶：** 包含頒發權杖的目錄租戶的不可變的唯一識別碼
   * **FQDN：** Azure VPN 閘道上的完全限定功能變數名稱 （FQDN）
   * **伺服器機密：** VPN 閘道預共用金鑰

## <a name="folder-contents"></a>資料夾內容

* **泛型資料夾**包含公共伺服器憑證和 VpnSettings.xml 檔。 VpnSettings.xml 檔包含配置通用用戶端所需的資訊。

* 下載的 ZIP 檔案可能還包含**WindowsAmd64**和**WindowsX86**資料夾。 這些資料夾包含用於 SSTP 的安裝程式和適用于 Windows 用戶端的 IKEv2。 您需要用戶端上的管理員許可權來安裝它們。