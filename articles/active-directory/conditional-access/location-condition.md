---
title: Azure 活動目錄條件訪問中的位置條件
description: 了解如何使用位置條件，以根據使用者的網路位置來控制雲端應用程式的存取。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.workload: identity
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 915675af1e646f2cb77e36c0018ed372ff9496fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263227"
---
# <a name="what-is-the-location-condition-in-azure-active-directory-conditional-access"></a>Azure 活動目錄條件訪問中的位置條件是什麼？ 

使用[Azure 活動目錄 （Azure AD） 條件訪問](../active-directory-conditional-access-azure-portal.md)，可以控制授權使用者可以如何訪問雲應用。 條件訪問策略的位置條件使您能夠將訪問控制項設置與使用者的網路位置綁定。

本文提供設定位置條件所需的資訊。

## <a name="locations"></a>位置

Azure AD 支援從公共 Internet 上的任意位置對設備、應用和服務進行單一登入。 使用位置條件，您可以根據使用者的網路位置來控制雲端應用程式的存取。 位置條件的常見使用案例如下：

- 針對從公司網路外存取服務的使用者要求使用多重要素驗證。
- 封鎖從特定國家或地區存取服務之使用者的存取權。

位置是表示命名位置或多重要素驗證信任 IP 的網路位置的標籤。

## <a name="named-locations"></a>具名位置

使用命名位置，您可以創建 IP 位址範圍或國家和地區的邏輯分組。

您可以在"條件訪問 **"頁的"管理**"部分中訪問命名位置。

![條件訪問中的命名位置](./media/location-condition/02.png)

具名位置具有下列元件：

![創建新命名位置](./media/location-condition/42.png)

- **名稱** - 具名位置的顯示名稱。
- **IP 範圍** - CIDR 格式的一或多個 IPv4 位址範圍。 不支援指定 IPv6 位址範圍。

   > [!NOTE]
   > IPv6 位址範圍當前不能包含在命名位置。 這意味著無法從條件訪問策略中排除 IPv6 範圍。

- **標記為信任位置** - 您可以為具名位置設定旗標，以指出信任的位置。 一般而言，信任的位置是由您的 IT 部門所控制的網路區域。 除了條件訪問之外，Azure 標識保護和 Azure AD 安全報告還使用受信任的命名位置來減少[誤報](../reports-monitoring/concept-risk-events.md#impossible-travel-to-atypical-locations-1)。
- **國家/地區** - 此選項可讓您選取一或多個國家或地區，以定義具名位置。
- **包括未知區域**- 某些 IP 位址未映射到特定國家或地區。 此選項可讓您選擇這些 IP 位址是否應包含在具名位置中。 當使用具名位置的原則應套用到未知位置時，請使用此設定。

您可以設定的具名位置數目受到 Azure AD 中相關物件大小的限制。 您可以根據以下限制配置位置：

- 一個具名位置最多有 1200 個 IP 範圍。
- 最多 90 個具名位置，每個位置皆指派一個 IP 範圍。

條件訪問策略適用于 IPv4 和 IPv6 流量。 當前命名的位置不允許配置 IPv6 範圍。 此限制導致以下情況：

- 條件訪問策略不能針對特定的 IPv6 範圍
- 條件訪問策略不能排除特定的 IPV6 範圍

如果策略配置為應用於"任何位置"，它將應用於 IPv4 和 IPv6 流量。 為指定國家和地區配置的命名位置僅支援 IPv4 位址。 僅當選擇了"包括未知區域"的選項時，才會包含 IPv6 流量。

## <a name="trusted-ips"></a>信任的 IP

您也可以在[多重要素驗證服務設定](https://account.activedirectory.windowsazure.com/usermanagement/mfasettings.aspx)中設定代表您組織的近端內部網路的 IP 位址範圍。 這項功能可讓您設定最多 50 個 IP 位址範圍。 IP 位址範圍是 CIDR 格式。 有關詳細資訊，請參閱受信任的[IP](../authentication/howto-mfa-mfasettings.md#trusted-ips)。  

如果配置了受信任的 IP，則它們在位置條件的位置清單中顯示為**MFA 受信任的 IPS。**

### <a name="skipping-multi-factor-authentication"></a>略過多重要素驗證

在多重要素驗證服務設定頁面中，您可以透過選取**針對來自內部網路同盟使用者的要求略過多重要素驗證**來識別公司內部網路使用者。 此設定表示 AD FS 所發出的內部公司網路宣告應受信任，並且應用來識別使用者位於公司網路上。 有關詳細資訊，請參閱[使用條件訪問啟用受信任的 IP 功能](../authentication/howto-mfa-mfasettings.md#enable-the-trusted-ips-feature-by-using-conditional-access)。

選中此選項後，包括命名位置**MFA 受信任的 IPS**將應用於選中此選項的任何策略。

對於具有長期會話存留期的移動和桌面應用程式，定期重新評估條件訪問。 預設值是一小時一次。 當只有在初始驗證才會發出位於公司網路內宣告時，Azure AD 可能不會有可信任 IP 範圍清單。 在此情況下，較難判斷使用者是否仍在公司網路上：

1. 檢查使用者的 IP 位址是否在其中一個信任的 IP 範圍內。
2. 檢查使用者 IP 位址的前三個八位位元組是否與初始身份驗證的 IP 位址的前三個八位位元組匹配。 在最初發出內部公司網路聲明並驗證使用者位置時，將 IP 位址與初始身份驗證進行比較。

如果這兩個步驟均失敗，會將使用者視為不再位於信任的 IP。

## <a name="location-condition-configuration"></a>位置條件組態

當您設定位置條件時，您可以區別：

- 任何位置
- 所有信任的位置
- 選取的位置

![位置條件組態](./media/location-condition/01.png)

### <a name="any-location"></a>任何位置

根據預設，選取 [任何位置]**** 會將原則套用到所有 IP 位址，而這意味著網際網路上的所有位址。 這項設定不限於您已設為具名位置的 IP 位址。 當您選取 [任何位置]**** 時，您仍然可以從原則中排除特定位置。 例如，您可以將原則套用到受信任位置以外的所有位置，以將範圍設為公司網路以外的所有位置。

### <a name="all-trusted-locations"></a>所有信任的位置

此選項適用範圍：

- 已標示為信任的位置的所有位置
- **MFA 信任的 IP** (若已設定)

### <a name="selected-locations"></a>選取的位置

使用此選項，您可以選取一或多個具名位置。 將套用的原則若有此設定，使用者必須從任一選取的位置連線。 當您按一下 [選取]**** 時，顯示具名網路清單的具名網路選取控制項隨即開啟。 此清單也會顯示網路位置是否已標示為受信任。 稱為 [MFA 信任的 IP]**** 的具名位置是用來包含可在多重要素驗證服務設定頁面中設定的 IP 設定。

## <a name="what-you-should-know"></a>您應該知道的事情

### <a name="when-is-a-location-evaluated"></a>何時會評估位置？

條件訪問策略在以下時間評估：

- 使用者初次登入 Web 應用程式、行動或桌面應用程式。
- 使用新式驗證的行動或桌面應用程式使用重新整理權杖來取得新的存取權杖。 預設情況下，此檢查是每小時一次。

此檢查意味著對於使用現代身份驗證的移動和桌面應用程式，將在更改網路位置後的一小時內檢測到位置更改。 對於不使用新式驗證的行動和桌面應用程式，此原則會套用至每個權杖要求。 要求頻率會隨應用程式而異。 同樣地，針對 Web 應用程式，此原則會在初始登入時套用，且適用於 Web 應用程式工作階段的存留期。 由於跨應用程式之工作階段存留期的差異，所以原則評估之間的時間也會不同。 每次應用程式要求新的登入權杖時，就會套用此原則。

根據預設，Azure AD 每小時會發出一個權杖。 在移出公司網路後，一小時內就會使用新式驗證針對應用程式強制執行原則。

### <a name="user-ip-address"></a>使用者 IP 位址

使用於原則評估的 IP 位址是使用者的公用 IP 位址。 對於私人網路絡上的設備，此 IP 位址不是 Intranet 上使用者設備的用戶端 IP，它是網路用於連接到公共互聯網的位址。

> [!WARNING]
> 如果您的設備只有 IPv6 位址，則不支援配置位置條件。

### <a name="bulk-uploading-and-downloading-of-named-locations"></a>大量上傳與下載具名位置

當您建立或更新具名位置時，您可以上傳或下載包含 IP 範圍的 CSV 檔案來進行大量更新。 上傳會使用檔案中的 IP 範圍取代清單中的 IP 範圍。 檔案的每個資料列均包含一個 CIDR 格式的 IP 位址範圍。

### <a name="cloud-proxies-and-vpns"></a>雲端 Proxy 和 VPN

當您使用雲端託管 Proxy 或 VPN 解決方案時，Azure AD 在評估原則時使用的 IP 位址為 Proxy 的 IP 位址。 不會使用包含使用者公用 IP 位址的 X-Forwarded-For (XFF) 標頭，因為無法驗證它來自受信任的來源，故會提供偽造 IP 位址的方法。

當雲端 Proxy 已就緒時，就能使用用來要求已加入網域之裝置的原則或來自 AD FS 的內部公司網路宣告。

### <a name="api-support-and-powershell"></a>API 支援與 PowerShell

命名位置或條件訪問策略尚不支援 API 和 PowerShell。

## <a name="next-steps"></a>後續步驟

- 如果想知道如何配置條件訪問策略，請參閱[使用 Azure 活動目錄條件訪問的特定應用需要 MFA。](app-based-mfa.md)
- 如果已準備好為環境配置條件訪問策略，請參閱[Azure 活動目錄中的條件訪問最佳做法](best-practices.md)。
