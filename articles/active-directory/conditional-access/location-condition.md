---
title: Azure Active Directory 條件式存取中的位置條件
description: 了解如何使用位置條件，以根據使用者的網路位置來控制雲端應用程式的存取。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 06/15/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.custom: contperfq4
ms.openlocfilehash: 091555bac780f3abc7089d6214d0f9a8cfbf98cd
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86518431"
---
# <a name="using-the-location-condition-in-a-conditional-access-policy"></a>在條件式存取原則中使用位置條件 

如[總覽一文](overview.md)所述，條件式存取原則是最基本的 if then 語句結合信號、進行決策，以及強制執行組織原則。 其中一個可以併入決策制定流程的信號是網路位置。

![概念性的條件式訊號加上決策，然後強制執行](./media/location-condition/conditional-access-signal-decision-enforcement.png)

組織可以使用此網路位置進行一般工作，例如： 

- 針對從公司網路外存取服務的使用者要求使用多重要素驗證。
- 封鎖從特定國家或地區存取服務之使用者的存取權。

網路位置取決於用戶端提供給 Azure Active Directory 的公用 IP 位址。 條件式存取原則預設適用于所有 IPv4 和 IPv6 位址。 

> [!TIP]
> 只有**[命名位置（預覽）](#preview-features)** 介面支援 IPv6 範圍。 

## <a name="named-locations"></a>具名位置

位置會在 Azure 入口網站的**Azure Active Directory**  >  **安全性**  >  **條件式存取**（  >  **名為位置**）底下指定。 這些名為的網路位置可能包括組織的總部網路範圍、VPN 網路範圍或您想要封鎖的範圍等位置。 

![Azure 入口網站中的命名位置](./media/location-condition/new-named-location.png)

若要設定位置，您必須至少提供一個**名稱**和 IP 範圍。 

您可以設定的具名位置數目受到 Azure AD 中相關物件大小的限制。 您可以根據下列限制設定位置：

- 一個名為的位置，最多1200個 IPv4 範圍。
- 最多 90 個具名位置，每個位置皆指派一個 IP 範圍。

> [!TIP]
> 只有**[命名位置（預覽）](#preview-features)** 介面支援 IPv6 範圍。 

### <a name="trusted-locations"></a>信任的位置

建立網路位置時，系統管理員可以選擇將位置標示為信任的位置。 

![Azure 入口網站中的信任位置](./media/location-condition/new-trusted-location.png)

此選項可納入條件式存取原則，例如，您可能需要從信任的網路位置註冊多重要素驗證。 它也會影響 Azure AD Identity Protection 的風險計算，並在來自標示為信任的位置時，降低使用者的登入風險。

### <a name="countries-and-regions"></a>國家和地區

有些組織可能會選擇將整個國家或地區的 IP 界限定義為條件式存取原則的命名位置。 當封鎖不必要的流量時，他們可能會使用這些位置，因為他們知道有效的使用者永遠都不會來自如北韓國的位置。 這些 IP 位址與國家/地區的對應會定期更新。 

> [!NOTE]
> 國家/地區不包含 IPv6 位址範圍、僅限已知的 IPv4 位址範圍，且無法標示為受信任。

![在 Azure 入口網站中建立新的國家/地區或區域型位置](./media/location-condition/new-named-location-country-region.png)

#### <a name="include-unknown-areas"></a>包含未知區域

某些 IP 位址未對應到特定的國家或地區。 若要捕獲這些 IP 位置，請核取 [定義位置時**包含不明區域**] 方塊。 此選項可讓您選擇這些 IP 位址是否應包含在具名位置中。 當使用具名位置的原則應套用到未知位置時，請使用此設定。

### <a name="configure-mfa-trusted-ips"></a>設定 MFA 信任的 Ip

您也可以在[多重要素驗證服務設定](https://account.activedirectory.windowsazure.com/usermanagement/mfasettings.aspx)中設定代表您組織的近端內部網路的 IP 位址範圍。 這項功能可讓您設定最多 50 個 IP 位址範圍。 IP 位址範圍是 CIDR 格式。 如需詳細資訊，請參閱[信任的 ip](../authentication/howto-mfa-mfasettings.md#trusted-ips)。  

如果您已設定信任的 Ip，它們會在位置條件的位置清單中顯示為**MFA 信任的 ip** 。

### <a name="skipping-multi-factor-authentication"></a>略過多重要素驗證

在多重要素驗證服務設定頁面中，您可以透過選取**針對來自內部網路同盟使用者的要求略過多重要素驗證**來識別公司內部網路使用者。 此設定表示 AD FS 所發出的內部公司網路宣告應受信任，並且應用來識別使用者位於公司網路上。 如需詳細資訊，請參閱[使用條件式存取啟用信任的 ip 功能](../authentication/howto-mfa-mfasettings.md#enable-the-trusted-ips-feature-by-using-conditional-access)。

核取此選項之後（包括已命名位置的**MFA 受信任 ip** ）將會套用至已選取此選項的任何原則。

針對具有長期會話存留期的行動和桌面應用程式，會定期重新評估條件式存取。 預設值是一小時一次。 當只有在初始驗證才會發出位於公司網路內宣告時，Azure AD 可能不會有可信任 IP 範圍清單。 在此情況下，較難判斷使用者是否仍在公司網路上：

1. 檢查使用者的 IP 位址是否在其中一個信任的 IP 範圍內。
1. 檢查使用者 IP 位址的前三個八位是否符合初始驗證 IP 位址的前三個八位。 當原始發出公司網路宣告且使用者位置已通過驗證時，IP 位址會與初始驗證進行比較。

如果這兩個步驟均失敗，會將使用者視為不再位於信任的 IP。

## <a name="preview-features"></a>預覽功能

除了正式推出的「命名位置」功能之外，還有一個命名位置（預覽）。 您可以使用目前 [命名位置] 分頁上方的橫幅來存取命名位置預覽。

![試用命名位置預覽](./media/location-condition/preview-features.png)

有了命名位置預覽，您就能夠

- 設定最多195名的位置
- 針對每個命名位置設定最多2000個 IP 範圍
- 設定 IPv6 位址與 IPv4 位址

我們也已新增一些額外的檢查，以協助減少設定錯誤的變更。

- 無法再設定私人 IP 範圍
- 可以包含在範圍內的 IP 位址數目有限。 設定 IP 範圍時，只會允許大於/8 的 CIDR 遮罩。

在預覽中，現在有兩個建立選項： 

- **國家/地區位置**
- **IP 範圍位置**

> [!NOTE]
> 國家/地區不包含 IPv6 位址範圍、僅限已知的 IPv4 位址範圍，且無法標示為受信任。

![命名位置預覽介面](./media/location-condition/named-location-preview.png)

## <a name="location-condition-in-policy"></a>原則中的位置條件

當您設定位置條件時，您可以區別：

- 任何位置
- 所有信任的位置
- 選取的位置

### <a name="any-location"></a>任何位置

根據預設，選取 [任何位置]**** 會將原則套用到所有 IP 位址，而這意味著網際網路上的所有位址。 這項設定不限於您已設為具名位置的 IP 位址。 當您選取 [任何位置]**** 時，您仍然可以從原則中排除特定位置。 例如，您可以將原則套用到受信任位置以外的所有位置，以將範圍設為公司網路以外的所有位置。

### <a name="all-trusted-locations"></a>所有信任的位置

此選項適用範圍：

- 已標示為信任的位置的所有位置
- **MFA 信任的 IP** (若已設定)

### <a name="selected-locations"></a>選取的位置

使用此選項，您可以選取一或多個具名位置。 將套用的原則若有此設定，使用者必須從任一選取的位置連線。 當您按一下 [選取]**** 時，顯示具名網路清單的具名網路選取控制項隨即開啟。 此清單也會顯示網路位置是否已標示為受信任。 稱為 [MFA 信任的 IP]**** 的具名位置是用來包含可在多重要素驗證服務設定頁面中設定的 IP 設定。

## <a name="ipv6-traffic"></a>IPv6 流量

根據預設，條件式存取原則會套用至所有 IPv6 流量。 使用[命名位置預覽](#preview-features)時，您可以從條件式存取原則中排除特定的 IPv6 位址範圍。 當您不想要針對特定 IPv6 範圍強制執行原則時，此選項非常有用。 例如，如果您不想強制執行公司網路上使用的原則，而且您的公司網路是裝載在公用 IPv6 範圍上。  

### <a name="when-will-my-tenant-have-ipv6-traffic"></a>我的租使用者何時會有 IPv6 流量？

Azure Active Directory （Azure AD）目前不支援使用 IPv6 的直接網路連線。 不過，在某些情況下，驗證流量會透過另一個服務來代理。 在這些情況下，將會在原則評估期間使用 IPv6 位址。

大部分以 proxy 傳送至 Azure AD 的 IPv6 流量都是來自 Microsoft Exchange Online。 當可用時，Exchange 會偏好使用 IPv6 連線。 **因此，如果您有任何 Exchange 的條件式存取原則已針對特定 IPv4 範圍進行設定，您會想要確定您也已新增組織的 IPv6 範圍。** 在下列兩種情況下，不包含 IPv6 範圍會導致非預期的行為：

- 當使用郵件用戶端以舊版驗證連線到 Exchange Online 時，Azure AD 可能會收到 IPv6 位址。 初始驗證要求會前往 Exchange，然後再 proxy 傳送至 Azure AD。
- 在瀏覽器中使用 Outlook Web 存取（OWA）時，它會定期驗證所有條件式存取原則是否繼續滿足。 這種檢查是用來攔截使用者可能已從允許的 IP 位址移至新位置的情況，像是在街道下的咖啡廳。 在此情況下，如果使用 IPv6 位址，而且 IPv6 位址不在設定的範圍內，則使用者可能會中斷其會話，並將其導向回到 Azure AD 重新驗證。 

這些是您在命名位置中設定 IPv6 範圍時可能需要的最常見原因。 此外，如果您使用 Azure Vnet，則會有來自 IPv6 位址的流量。 如果您有條件式存取原則封鎖的 VNet 流量，請檢查您的 Azure AD 登入記錄。 一旦識別出流量之後，您就可以取得使用的 IPv6 位址，並將它從原則中排除。 

> [!NOTE]
> 如果您想要指定單一位址的 IP CIDR 範圍，請套用/32 位元遮罩。 如果您說出 IPv6 位址2607： fb90： b27a：6f69： f8d5： dea0： fb39：74a，而且想要將該單一位址排除成一個範圍，您會使用2607： fb90： b27a：6f69： f8d5： dea0： fb39： 74a/32。

### <a name="identifying-ipv6-traffic-in-the-azure-ad-sign-in-activity-reports"></a>識別 Azure AD 登入活動報告中的 IPv6 流量

您可以藉由將[Azure AD 登入活動報告](../reports-monitoring/concept-sign-ins.md)，探索您租使用者中的 IPv6 流量。 當活動報表開啟之後，請新增 [IP 位址] 資料行。 此欄可讓您識別 IPv6 流量。

您也可以按一下報表中的資料列，然後前往登入活動詳細資料中的 [位置] 索引標籤，來尋找用戶端 IP。 

## <a name="what-you-should-know"></a>您應該知道的事情

### <a name="when-is-a-location-evaluated"></a>何時會評估位置？

條件式存取原則會在下列情況評估：

- 使用者初次登入 Web 應用程式、行動或桌面應用程式。
- 使用新式驗證的行動或桌面應用程式使用重新整理權杖來取得新的存取權杖。 根據預設，這項檢查是一小時一次。

這項檢查代表使用新式驗證的行動和桌面應用程式，在變更網路位置的一小時內會偵測到位置變更。 對於不使用新式驗證的行動和桌面應用程式，此原則會套用至每個權杖要求。 要求頻率會隨應用程式而異。 同樣地，針對 Web 應用程式，此原則會在初始登入時套用，且適用於 Web 應用程式工作階段的存留期。 由於跨應用程式之工作階段存留期的差異，所以原則評估之間的時間也會不同。 每次應用程式要求新的登入權杖時，就會套用此原則。

根據預設，Azure AD 每小時會發出一個權杖。 在移出公司網路後，一小時內就會使用新式驗證針對應用程式強制執行原則。

### <a name="user-ip-address"></a>使用者 IP 位址

使用於原則評估的 IP 位址是使用者的公用 IP 位址。 對於私人網路上的裝置，此 IP 位址不是內部網路上使用者裝置的用戶端 IP，而是網路用來連線到公用網際網路的位址。

### <a name="bulk-uploading-and-downloading-of-named-locations"></a>大量上傳與下載具名位置

當您建立或更新具名位置時，您可以上傳或下載包含 IP 範圍的 CSV 檔案來進行大量更新。 [上傳] 會將清單中的 IP 範圍取代為檔案中的範圍。 檔案的每個資料列均包含一個 CIDR 格式的 IP 位址範圍。

### <a name="cloud-proxies-and-vpns"></a>雲端 Proxy 和 VPN

當您使用雲端託管 Proxy 或 VPN 解決方案時，Azure AD 在評估原則時使用的 IP 位址為 Proxy 的 IP 位址。 不會使用包含使用者公用 IP 位址的 X-Forwarded-For (XFF) 標頭，因為無法驗證它來自受信任的來源，故會提供偽造 IP 位址的方法。

當雲端 Proxy 已就緒時，就能使用用來要求已加入網域之裝置的原則或來自 AD FS 的內部公司網路宣告。

### <a name="api-support-and-powershell"></a>API 支援與 PowerShell

已命名位置的圖形 API 預覽版本可供使用，如需詳細資訊，請參閱[NAMEDLOCATION API](/graph/api/resources/namedlocation?view=graph-rest-beta)。

## <a name="next-steps"></a>後續步驟

- 如果您想要知道如何設定條件式存取原則，請參閱[建立條件式存取原則一](concept-conditional-access-policies.md)文。
- 使用位置條件來尋找範例原則嗎？ 請參閱條件式[存取：依位置封鎖存取](howto-conditional-access-policy-location.md)一文
