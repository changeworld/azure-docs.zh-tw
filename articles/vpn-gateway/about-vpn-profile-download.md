---
title: Azure VPN 閘道：關於 P2S VPN 用戶端設定檔
description: 這可協助您使用用戶端設定檔
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 03/11/2020
ms.author: cherylmc
ms.openlocfilehash: 762f62fa0901672c447da42f416e5b003e7419b2
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/11/2020
ms.locfileid: "79127304"
---
# <a name="about-p2s-vpn-client-profiles"></a>關於 P2S VPN 用戶端設定檔

下載的設定檔包含設定 VPN 連線所需的資訊。 本文將協助您取得並瞭解 VPN 用戶端設定檔所需的資訊。

## <a name="1-download-the-file"></a>1. 下載檔案

執行下列命令。 將結果 URL 複製到您的瀏覽器，以便下載設定檔 zip 檔案。

```azurepowershell-interactive
$profile = New-AzVpnClientConfiguration -ResourceGroupName AADAuth -Name AADauthGW -AuthenticationMethod "EapTls"
   
$PROFILE.VpnProfileSASUrl
```

## <a name="2-extract-the-zip-file"></a>2. 解壓縮 zip 檔案

解壓縮 zip 檔案。 檔案包含下列資料夾：

* AzureVPN
* 一般
* OpenVPN （如果您已在閘道上啟用 OpenVPN 和 Azure AD 驗證設定。 請參閱[建立租](openvpn-azure-ad-tenant.md)使用者。）

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

* **OpenVPN 資料夾**包含需要修改的*vpnconfig.ovpn*設定檔，以包含金鑰和憑證。 如需詳細資訊，請參閱[設定 Azure VPN 閘道的 OpenVPN 用戶端](vpn-gateway-howto-openvpn-clients.md#windows)。 如果在 VPN 閘道上選取了 Azure AD authentication，此資料夾就不會出現在 zip 檔案中。 相反地，azurevpnconfig 會在 AzureVPN 資料夾中。

* **Generic 資料夾**包含公用伺服器憑證和 vpnsettings.xml 檔。 Vpnsettings.xml 包含設定一般用戶端所需的資訊。

* 下載的 zip 檔案可能也包含**WindowsAmd64**和**WindowsX86**資料夾。 這些資料夾包含適用于 Windows 用戶端的 SSTP 和 IKEv2 安裝程式。 您需要用戶端上的系統管理員許可權才能安裝它們。

## <a name="next-steps"></a>後續步驟

如需有關點對站的詳細資訊，請參閱[關於點對站](point-to-site-about.md)。
