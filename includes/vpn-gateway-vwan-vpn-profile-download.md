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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80066199"
---
## <a name="1-download-the-file"></a>1. 下載檔案

執行下列命令。 將結果 URL 複製到您的瀏覽器，以便下載設定檔 zip 檔案。

```azurepowershell-interactive
$profile = New-AzVpnClientConfiguration -ResourceGroupName AADAuth -Name AADauthGW -AuthenticationMethod "EapTls"
   
$PROFILE.VpnProfileSASUrl
```

## <a name="2-extract-the-zip-file"></a>2. 解壓縮 zip 檔案

解壓縮 zip 檔案。 檔案包含下列資料夾：

* AzureVPN
* 泛型
* OpenVPN （如果您已在閘道上啟用 OpenVPN 和 Azure AD 驗證設定。 如 VPN 閘道，請參閱[建立租](../articles/vpn-gateway/openvpn-azure-ad-tenant.md)使用者。 如需虛擬 WAN，請參閱[建立租使用者-VWAN](../articles/virtual-wan/openvpn-azure-ad-tenant.md)。

## <a name="3-retrieve-information"></a>3. 取出資訊

在**AzureVPN**資料夾中，流覽至***azurevpnconfig***檔案，並使用 [記事本] 開啟它。 記下下列標記之間的文字。

```
<audience>          </audience>
<issuer>            </issuer>
<tennant>           </tennant>
<fqdn>              </fqdn>
<serversecret>      </serversecret>
```

## <a name="profile-details"></a>設定檔詳細資料

當您新增連接時，請使用您在上一個步驟中針對設定檔詳細資料頁面所收集的資訊。 欄位會對應至下列資訊：

   * **物件：** 識別權杖適用的收件者資源
   * **簽發者：** 識別發出權杖的安全性權杖服務（STS），以及 Azure AD 租使用者
   * **租使用者：** 包含發出權杖之目錄租使用者的不可變、唯一識別碼
   * **FQDN：** Azure VPN 閘道上的完整功能變數名稱（FQDN）
   * **ServerSecret：** VPN 閘道預先共用金鑰

## <a name="folder-contents"></a>資料夾內容

* **Generic 資料夾**包含公用伺服器憑證和 vpnsettings.xml 檔。 Vpnsettings.xml 包含設定一般用戶端所需的資訊。

* 下載的 zip 檔案可能也包含**WindowsAmd64**和**WindowsX86**資料夾。 這些資料夾包含適用于 Windows 用戶端的 SSTP 和 IKEv2 安裝程式。 您需要用戶端上的系統管理員許可權才能安裝它們。