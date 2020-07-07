---
title: X.509 Service Fabric 叢集中以憑證為基礎的驗證
description: 瞭解 Service Fabric 叢集中以憑證為基礎的驗證，以及如何偵測、緩和和修正憑證相關的問題。
ms.topic: conceptual
ms.date: 03/16/2020
ms.custom: sfrev
ms.openlocfilehash: 699015e322c599dea996b3a8b9dbc0a4589440ab
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "81429664"
---
# <a name="x509-certificate-based-authentication-in-service-fabric-clusters"></a>X.509 Service Fabric 叢集中以憑證為基礎的驗證

本文會補充[Service Fabric 叢集安全性](service-fabric-cluster-security.md)的簡介，並深入探討 Service Fabric 叢集內以憑證為基礎的驗證詳細資料。 我們假設讀者熟悉基本的安全性概念，以及 Service Fabric 公開以控制叢集安全性的控制項。  

此標題底下涵蓋的主題如下：

* 以憑證為基礎的驗證基本概念
* 身分識別及其各自的角色
* 憑證設定規則
* 疑難排解和常見問題

## <a name="certificate-based-authentication-basics"></a>以憑證為基礎的驗證基本概念
做為簡短的重新整理程式，在安全性中，憑證是指將實體（主體）的相關資訊系結至其擁有一組非對稱式密碼編譯金鑰的檢測，因此構成公開金鑰加密的核心結構。 憑證所代表的金鑰可用於保護資料，或證明金鑰持有者的身分識別;與公開金鑰基礎結構（PKI）系統搭配使用時，憑證可以代表其主體的其他特性（例如，網際網路網域的擁有權），或憑證簽發者授與的特定許可權（稱為憑證授權單位單位或 CA）。 憑證的常見應用是支援傳輸層安全性（TLS）密碼編譯通訊協定，允許透過電腦網路進行安全通訊。 具體來說，用戶端和伺服器會使用憑證來確保其通訊的隱私權和完整性，也會執行相互驗證。

在 Service Fabric 中，叢集的基本層（同盟）也是以 TLS （在其他通訊協定上）為基礎，以達成可靠且安全的參與節點網路。 透過 Service Fabric 用戶端 Api 連線到叢集時，也會使用 TLS 來保護流量，同時也會建立合作物件的身分識別。 具體而言，當用於 Service Fabric 中的驗證時，憑證可用來證明下列宣告： a）憑證認證的展示者已擁有憑證的私密金鑰 b）憑證的 SHA-1 雜湊（' 指紋 '）符合叢集定義中包含的宣告，或 c）憑證的辨別主體一般名稱符合叢集定義中包含的宣告和憑證的簽發者是已知或受信任的。

在上述清單中，' b ' 堆疊稱為「指紋釘選」;在此情況下，宣告會參考特定的憑證，而驗證配置的強度會放在其所不可行的內部部署上，以偽造憑證，而這會產生與另一個相同的雜湊值，而仍然是所有其他方面的有效、格式正確的物件。 專案 ' c ' 代表宣告憑證的替代形式，其中配置的強度會放在憑證和發行授權單位的組合上。 在此情況下，宣告會參考憑證的類別，任何兩個具有相同特性的憑證都會被視為完全相同。 

下列各節將深入說明 Service Fabric 執行時間如何使用並驗證憑證，以確保叢集安全性。

## <a name="identities-and-their-respective-roles"></a>身分識別及其各自的角色
在深入探討驗證或保護通道的詳細資料之前，請務必列出參與的動作專案以及它們在叢集中扮演的對應角色：
- Service Fabric 執行時間（稱為「系統」）：提供代表叢集之抽象和功能的一組服務。 當參考系統實例之間的叢集間通訊時，我們會使用「叢集身分識別」一詞;當您將叢集視為來自叢集外流量的收件者/目標時，我們會使用「伺服器身分識別」一詞。
- 裝載的應用程式，稱為「應用程式」：叢集擁有者所提供的程式碼，在叢集中協調和執行
- 用戶端：根據叢集設定，允許實體連線並執行叢集中的功能。 我們會分別區分兩種層級的許可權：「使用者」和「admin」。 「使用者」用戶端主要限制為唯讀作業（但不是所有唯讀功能），而「admin 用戶端」則可以不受限制地存取叢集的功能。 （如需詳細資訊，請參閱 Service Fabric 叢集中[的安全性角色](service-fabric-cluster-security-roles.md)）。
- （僅限 Azure） Service Fabric 的服務，可協調及公開 Service Fabric 叢集的作業和管理控制，只稱為「服務」。 視環境而定，「服務」可能會參考 Azure Service Fabric 資源提供者，或由 Service Fabric 團隊所擁有和操作的其他資源提供者。

在安全的叢集中，每個角色都可以使用自己的相異身分識別進行設定，並宣告為配對的預先定義角色名稱與其對應的認證。 Service Fabric 支援將認證宣告為憑證或網域型服務主體。 （也支援 windows/Kerberos-based 身分識別，但不在本文的討論範圍內，請參閱 Service Fabric 叢集的[windows 安全性](service-fabric-windows-cluster-windows-security.md)）。在 Azure 叢集中，可能也會將用戶端角色宣告為以[Azure Active Directory 為基礎的](service-fabric-cluster-creation-setup-aad.md)身分識別。

如同上述提到，Service Fabric 執行時間會在叢集中定義兩種層級的許可權： ' admin ' 和「使用者」。 管理員用戶端和「系統」元件會使用「admin」許可權來運作，因此會彼此 undistinguishable。 當您在叢集中建立連線時，已驗證的呼叫者會被 Service Fabric 執行時間授與，做為後續授權的基礎。 我們將在下列各節中深入探討驗證。

## <a name="certificate-configuration-rules"></a>憑證設定規則
### <a name="validation-rules"></a>驗證規則
Service Fabric 叢集的安全性設定會描述下列層面：
- 驗證類型;這是叢集的建立時間、不可變的特性。 這類設定的範例包括 ' ClusterCredentialType '、' Servercredentialtype 設定 '，而允許的值為 ' none '、' x509 ' 或 ' windows '。 本文著重于 x509 型別驗證。
- （驗證）驗證規則;這些設定是由叢集擁有者所設定，並描述哪些認證應接受給指定的角色。 範例會在下面的深度進行深入探討。
- 用來調整或細微改變驗證結果的設定;這裡的範例包括旗標（取消）限制憑證撤銷清單的強制執行等。

> [!NOTE]
> 下面提供的叢集設定範例是以 XML 格式的叢集資訊清單摘錄，做為最加以訓練的格式，可直接支援本文中所述的 Service Fabric 功能。 您可以直接在叢集定義的 JSON 標記法中表示相同的設定，不論是獨立的 json 叢集資訊清單或 Azure 資源管理範本。

憑證驗證規則包含下列元素：
- 對應的角色：用戶端、系統管理員用戶端（特殊許可權角色）
- 角色已接受的認證，依指紋或主體一般名稱宣告

#### <a name="thumbprint-based-certificate-validation-declarations"></a>以指紋為基礎的憑證驗證宣告
在以指紋為基礎的驗證規則案例中，要求在叢集內連線的呼叫端所呈現的認證將會依照下列方式進行驗證：
  - 認證是有效、格式正確的憑證：其鏈可以建立，簽章必須相符
  - 憑證有效時間（NotBefore <= 現在 < NotAfter）
  - 憑證的 SHA-1 雜湊會符合宣告，做為不區分大小寫的字串比較，但不包括所有的空格

在鏈建立或驗證期間遇到的任何信任錯誤都會針對以指紋為基礎的宣告隱藏，但過期的憑證除外-雖然這種情況下的布建也存在。 具體而言，與下列各項相關的失敗：撤銷狀態為未知或離線、不受信任的根、不正確金鑰使用方式、部分鏈視為非嚴重錯誤;在此情況下，內部部署是指憑證只是金鑰組的信封，安全性在於叢集擁有者已設定為「地點」量值來保護私密金鑰的事實。

下列來自叢集資訊清單的摘錄會」舉例說明一組以指紋為基礎的驗證規則：

```xml
<Section Name="Security">
  <Parameter Name="ClusterCredentialType" Value="X509" />
  <Parameter Name="ServerAuthCredentialType" Value="X509" />
  <Parameter Name="AdminClientCertThumbprints" Value="d5ec...4264" />
  <Parameter Name="ClientCertThumbprints" Value="7c8f...01b0" />
  <Parameter Name="ClusterCertThumbprints" Value="abcd...1234,ef01...5678" />
  <Parameter Name="ServerCertThumbprints" Value="ef01...5678" />
</Section>
```

上述每個專案都會參考先前所述的特定身分識別;每個專案也允許指定多個值，做為以逗號分隔的字串清單。 在此範例中，成功驗證傳入的認證時，具有 SHA-1 指紋的憑證呈現者會 5ec .。。4264 ' 將被授與 ' admin ' 角色;相反地，以憑證 ' 7c8f ' 進行驗證的呼叫端。01b0 ' 將會被授與「使用者」角色，限制為主要的唯讀作業。 輸入呼叫者，其提供指紋為 ' abcd ' 的憑證。1234 ' 或 ' ef01 .。。5678 ' 將接受為叢集中的對等節點。 最後，連接到叢集管理端點的用戶端會預期伺服器憑證的指紋為 ' ef01 .。。5678 '。 

如先前所述，Service Fabric 會針對接受過期的憑證進行布建;原因是憑證具有有限的存留期，且在依指紋宣告（參照特定的憑證實例）時，允許憑證過期會導致無法連線到叢集，或直接折迭叢集。 很容易忘記或忽視輪替指紋固定的憑證，而且很難從這種情況進行復原。

為此，叢集擁有者可以明確陳述指紋所宣告的自我簽署憑證應視為有效，如下所示：

```xml
  <Section Name="Security">
    <Parameter Name="AcceptExpiredPinnedClusterCertificate" Value="true" />
  </Section>
```
此行為不會延伸至 CA 發行的憑證;如果是這種情況，已撤銷、已知遭到盜用的已過期憑證可能會變成「有效」，因為它不會再在 CA 的憑證撤銷清單中顯示，因此會帶來安全性風險。 使用自我簽署憑證時，叢集擁有者會被視為唯一負責保護憑證私密金鑰的當事人，這不是 CA 發行憑證的情況，而叢集擁有者可能不知道其憑證遭到入侵的方式或時機。

#### <a name="common-name-based-certificate-validation-declarations"></a>以名稱為基礎的一般憑證驗證宣告
一般名稱型宣告會採用下列其中一種形式：
- 主體一般名稱（僅限）
- 具有簽發者釘選的主體一般名稱

讓我們先從叢集資訊清單中考慮 exemplifying 這兩種宣告樣式的摘錄：
```xml
    <Section Name="Security/ServerX509Names">
      <Parameter Name="server.demo.system.servicefabric.azure-int" Value="" />
    </Section>
    <Section Name="Security/ClusterX509Names">
      <Parameter Name="cluster.demo.system.servicefabric.azure-int" Value="1b45...844d,d7fe...26c8,3ac7...6960,96ea...fb5e" />
    </Section>
```
宣告會分別參考伺服器和叢集身分識別;請注意，以 CN 為基礎的宣告在叢集資訊清單中有自己的區段，與標準的「安全性」不同。 在這兩個宣告中，「名稱」代表憑證的辨別主體一般名稱，而「值」欄位則代表預期的簽發者，如下所示：

- 在第一個案例中，宣告會指出伺服器憑證之辨別主旨的一般名稱元素應符合字串 "server.demo.system. servicefabric. azure-int";空白的 [值] 欄位表示在驗證伺服器憑證的節點/電腦上，憑證鏈的根目錄是受信任的。在 Windows 上，這表示憑證可以連結到安裝在「信任的根 CA」存放區中的任何憑證;
- 在第二個案例中，如果憑證的一般名稱符合 "cluster.demo.system. servicefabric" 字串，*且*憑證的直接發行者指紋符合 [值] 欄位中的其中一個逗號分隔專案，則宣告會指出憑證的展示者會被視為叢集中的對等節點。 （此規則類型的堆疊稱為「具有簽發者釘選的一般名稱」）。

不論是哪一種情況，都會建立憑證的鏈，而且預期不會發生錯誤;也就是說，撤銷錯誤、部分鏈或時間-不正確信任錯誤會被視為嚴重的，而且憑證驗證會失敗。 釘選簽發者會導致將「不受信任的根」狀態視為非嚴重錯誤;儘管有外觀，這是更嚴格的驗證形式，因為它可讓叢集擁有者將已授權/接受的簽發者限制為自己的 PKI。

建立憑證鏈之後，會使用已宣告的主體做為遠端名稱的標準 TLS/SSL 原則來驗證它。如果憑證的主體一般名稱或其主體替代名稱與叢集資訊清單中的 CN 宣告相符，則會將其視為相符專案。 在此情況下，支援萬用字元，而字串比對不區分大小寫。

（我們應該瞭解，上述的順序可以針對憑證所宣告的每種金鑰使用方式執行; 如果憑證指定用戶端驗證金鑰使用方法，則會先建立鏈，並針對用戶端角色評估。 如果成功，評估會完成，且驗證成功。 如果憑證沒有用戶端驗證使用方式，或驗證失敗，則 Service Fabric 執行時間會建立並評估伺服器驗證的鏈）。

為了完成此範例，下列摘錄說明如何依一般名稱宣告用戶端憑證：
```xml
    <Section Name="Security/AdminClientX509Names">
      <Parameter Name="admin.demo.client.servicefabric.azure-int" Value="1b45...844d,d7fe...26c8,3ac7...6960,96ea...fb5e" />
    </Section>
    <Section Name="Security/ClientX509Names">
      <Parameter Name="user.demo.client.servicefabric.azure-int" Value="1b45...844d,d7fe...26c8,3ac7...6960,96ea...fb5e" />
    </Section>
```

上述宣告分別對應到系統管理員和使用者身分識別。以這種方式宣告的憑證驗證，與先前範例中的叢集和伺服器憑證的描述完全相同。

> [!NOTE]
> 以名稱為基礎的一般宣告是用來簡化迴圈，通常是管理叢集憑證。 不過，建議您遵循下列建議，以確保叢集的持續可用性和安全性：
  * 偏好簽發者釘選以依賴受信任的根
  * 避免混用來自不同 Pki 的簽發者
  * 請確定所有預期的簽發者都列在憑證宣告上;不相符的簽發者會導致驗證失敗
  * 確定 PKI 的憑證原則端點可搜尋、可用且可存取-這表示會在分葉憑證上宣告 AIA、CRL 或 OCSP 端點，而且它們可以存取，以完成憑證鏈建立。

將它全部結合在一起，在 x.509 憑證所保護的叢集中收到連接要求時，Service Fabric 執行時間會使用叢集的安全性設定來驗證遠端合作物件的認證，如上所述。如果成功，則會將呼叫端/遠端方視為已驗證。 如果認證符合多個驗證規則，則執行時間會授與呼叫者任何相符規則的最高許可權角色。 

### <a name="presentation-rules"></a>簡報規則
上一節說明了驗證在憑證保護的叢集中的運作方式;本節將說明 Service Fabric 執行時間本身如何探索並載入它用於叢集通訊的憑證;我們稱之為「簡報」規則。

如同驗證規則，簡報規則會指定角色和相關聯的認證宣告（以指紋或一般名稱表示）。 不同于驗證規則，一般名稱型宣告不會提供簽發者釘選的條款;這可提供更大的彈性，並提升效能。 在叢集資訊清單的 ' NodeType ' 區段中，會針對每個不同的節點類型宣告呈現規則;這些設定會從叢集的安全性區段分割，讓每個節點類型在單一區段中都有完整的設定。 在 Azure Service Fabric 叢集中，節點類型憑證宣告會預設為叢集定義之 [安全性] 區段中的對應設定。

#### <a name="thumbprint-based-certificate-presentation-declarations"></a>以指紋為基礎的憑證呈現宣告
如先前所述，Service Fabric 執行時間會區分其角色，做為叢集中其他節點的對等，以及做為叢集管理作業的伺服器。 就原則而言，這些設定可以明確設定，但實際上，它們通常會對齊。 在本文的其餘部分，我們會假設設定符合以求簡單。

讓我們考慮下列叢集資訊清單的摘錄：
```xml
  <NodeTypes>
    <NodeType Name="nt1vm">
      <Certificates>
        <ClusterCertificate X509FindType="FindByThumbprint" X509FindValue="cc71...1984" X509FindValueSecondary="49e2...19d6" X509StoreName="my" Name="ClusterCertificate" />
        <ServerCertificate X509FindValue="cc71...1984" Name="ServerCertificate" />
        <ClientCertificate X509FindValue="cc71...1984" Name="ClientCertificate" />
      </Certificates>
    </NodeType>
  </NodeTypes>
```
' ClusterCertificate ' 元素會示範完整的架構，包括選擇性參數（' X509FindValueSecondary '）或具有適當預設值的（' X509StoreName '）;其他宣告則顯示縮寫格式。 上述叢集憑證宣告指出類型 ' nt1vm ' 之節點的安全性設定已使用憑證 ' cc71 ' 進行初始化。1984 ' 作為主要，而 ' 49e2.。19d6 ' 憑證作為次要複本;這兩個憑證都應該在 LocalMachine \' my ' 憑證存放區（或 Linux 對等路徑， *var/lib/sfcerts*）中找到。

#### <a name="common-name-based-certificate-presentation-declarations"></a>以名稱為基礎的一般憑證呈現宣告
節點類型憑證也可以依主體一般名稱宣告，如下所示：

```xml
  <NodeTypes>
    <NodeType Name="nt1vm">
      <Certificates>
        <ClusterCertificate X509FindType="FindBySubjectName" X509FindValue="demo.cluster.azuredocpr.system.servicefabric.azure-int" Name="ClusterCertificate" />
      </Certificates>
    </NodeType>
  </NodeTypes>
```

針對任一類型的宣告，Service Fabric 節點會在啟動時讀取設定、尋找並載入指定的憑證，並依其 NotAfter 屬性的遞減順序排序。已忽略過期的憑證，並選取清單中的第一個元素，做為此節點所嘗試之任何 Service Fabric 連接的用戶端認證。 （實際上，Service Fabric 優先于最接近的到期憑證）。

請注意，針對一般名稱的呈現宣告，如果憑證的主體一般名稱等於宣告的 X509FindValue （或 X509FindValueSecondary）欄位為區分大小寫的確切字串比較，就會將其視為相符。 這與驗證規則相比較，其支援萬用字元比對，以及不區分大小寫的字串比較。  

### <a name="miscellaneous-certificate-configuration-settings"></a>其他憑證設定
先前提到的是，Service Fabric 叢集的安全性設定也可以稍微變更驗證碼的行為。 雖然[Service Fabric 叢集設定](service-fabric-cluster-fabric-settings.md)上的文章代表設定的完整和最新清單，但我們將在此展開選取幾個安全性設定的意義，以完成以憑證為基礎之驗證的完整公開。 針對每個設定，我們將說明意圖、預設值/行為、它如何影響驗證，以及哪些值是可接受的。

如前所述，憑證驗證一律意指建立和評估憑證鏈。 對於 CA 發行的憑證，此明顯簡單的 OS API 呼叫通常需要對發行 PKI 的各種端點進行數個輸出呼叫，並快取回應等等。 由於 Service Fabric 叢集中的憑證驗證呼叫普及，因此 PKI 端點中的任何問題都會導致叢集的可用性降低，或直接細分。 雖然無法隱藏輸出呼叫（如需詳細資訊，請參閱常見問題一節中的說明），但您可以使用下列設定來遮罩掉因 CRL 呼叫失敗所造成的驗證錯誤。

  * CrlCheckingFlag-在 [安全性] 區段底下，轉換成 [UINT] 的字串。 Service Fabric 使用此設定的值，藉由變更鏈建築物的行為來遮蔽憑證鏈狀態錯誤;它會以 ' dwFlags ' 參數的形式傳入 Win32 CryptoAPI [CertGetCertificateChain](https://docs.microsoft.com/windows/win32/api/wincrypt/nf-wincrypt-certgetcertificatechain)呼叫，而且可以設定為函式所接受的任何有效旗標組合。 值為0會強制 Service Fabric 執行時間忽略任何信任狀態錯誤，這不是建議的作法，因為它的使用會構成嚴重的安全性風險。 預設值為0x40000000 （CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT）。

  使用時機：用於本機測試，使用自我簽署憑證或開發人員憑證，其格式不完整/沒有適當的公開金鑰基礎結構來支援憑證。 可能也會在在 Pki 之間轉換期間，在空中不具有風險的環境中使用。

  如何使用：我們將會執行強制撤銷檢查的範例，只存取快取的 Url。 假定
  ```C++
  #define CERT_CHAIN_REVOCATION_CHECK_CACHE_ONLY         0x80000000
  ```
  然後，叢集資訊清單中的宣告就會變成：
  ```xml
    <Section Name="Security">
      <Parameter Name="CrlCheckingFlag" Value="0x80000000" />
    </Section>
  ```

  * IgnoreCrlOfflineError-在 [安全性] 區段底下，預設值為 ' false ' 的布林值。 表示隱藏「撤銷離線」鏈建立錯誤狀態（或後續的連鎖原則驗證錯誤狀態）的快捷方式。

  使用時機：本機測試，或使用不受適當 PKI 支援的開發人員憑證。 在空中有風險的環境中，或無法存取 PKI 時，請使用此功能。

  使用方式：
  ```xml
    <Section Name="Security">
      <Parameter Name="IgnoreCrlOfflineError" Value="true" />
    </Section>
  ```

  其他值得注意的設定（全部在「安全性」一節底下）：
  * AcceptExpiredPinnedClusterCertificate-在以指紋為基礎的憑證驗證一節中討論過;允許接受已過期的自我簽署叢集憑證。 
  * CertificateExpirySafetyMargin-間隔（以分鐘為單位），在憑證的 NotAfter 時間戳記之前，以及在這段期間內，憑證會被視為到期的風險。 Service Fabric 監視叢集憑證，並定期發出健全狀況報告的剩餘可用性。 在「安全」間隔內，這些健康情況報告會提高為「警告」狀態。 預設值是 30 天。
  * CertificateHealthReportingInterval-控制與叢集憑證的剩餘時間有效性有關的健康情況報告頻率。 每個間隔只會發出一次報表。 此值以秒為單位表示，預設值為8小時。
  * EnforcePrevalidationOnSecurityChanges-布林值，控制偵測安全性設定的變更時，叢集升級的行為。 如果設定為 'true'，叢集升級會嘗試確保至少其中一個符合任何展示規則的憑證可以通過對應的驗證規則。 預先驗證會先執行，然後才會將新的設定套用至任何節點，但只會在起始升級時裝載叢集管理員服務主要複本的節點上執行。 在撰寫本文時，此設定的預設值為 ' false '，且會針對新的 Azure Service Fabric 叢集設定為 ' true '，且執行時間版本從7.1 開始。
 
### <a name="end-to-end-scenario-examples"></a>端對端案例（範例）
我們探討了簡報規則、驗證規則和調整旗標，但這兩者如何共同運作？ 在本節中，我們將逐步解說兩個端對端範例，示範如何利用安全性設定來進行安全的叢集升級。 請注意，這不是 Service Fabric 的適當憑證管理的完整長篇大論，請參閱該主題的相關文章。

簡報和驗證規則的分隔，會引發其是否可以分離的問題（或問題），以及產生的結果。 事實上，節點選取的驗證憑證不會通過另一個節點的驗證規則。 事實上，這項差異是驗證相關事件的主要原因。 同時，這些規則的分隔可讓叢集在升級期間繼續運作，這會變更叢集的安全性設定。 假設，藉由先擴充第一個步驟的驗證規則，所有叢集的節點都會在新的設定上聚合，同時仍使用目前的認證。 

回想一下，在 Service Fabric 叢集中，升級會進行（最多5個）「升級網域」或 ud。 只有目前 UD 中的節點會在指定時間進行升級/變更，而且只有在叢集的可用性允許時，升級才會繼續到下一個 UD。 （如需詳細資訊，請參閱相同主題的[Service Fabric 叢集升級](service-fabric-cluster-upgrade.md)和其他文章。）憑證/安全性變更特別有風險，因為它們可以從叢集隔離節點，或讓叢集處於仲裁遺失的邊緣。

我們將使用下列標記法來描述節點的安全性設定：

Nk.bin： {P:{TP = A}，V:{TP = A}}，其中：
  - ' Nk.bin ' 代表升級網域*k*中的節點
  - ' P ' 代表節點目前的呈現規則（假設我們只參照叢集憑證）; 
  - ' V ' 代表節點目前的驗證規則（僅限叢集憑證）
  - ' TP = A ' 代表以指紋為基礎的宣告（TP），其中 ' A ' 是憑證指紋
  - ' CN = B ' 代表以名稱為基礎的一般宣告（CN），其中 ' B ' 是憑證的主體一般名稱 

#### <a name="rotating-a-cluster-certificate-declared-by-thumbprint"></a>輪替指紋所宣告的叢集憑證
下列順序說明如何使用2階段升級來安全地引進次要叢集憑證（由指紋所宣告）;第一個階段引進驗證規則中的新憑證宣告，而第二個階段則會在呈現規則仲介紹：
  - 初始狀態： N0 = {P:{TP = A}，V:{TP = A}}，.。。Nk.bin = {P:{TP = A}，V:{TP = A}}-叢集為待用，所有節點都共用一般設定
  - 完成升級時，網域0： N0 = {P:{TP = A}，V:{TP = A，TP = B}}，.。。Nk.bin = {P:{TP = A}，V:{TP = A}}-UD0 中的節點將會顯示憑證 A，並接受憑證 A 或 B;所有其他節點都存在，並只接受憑證 A
  - 完成最後一個升級網域時： N0 = {P:{TP = A}，V:{TP = A，TP = B}}，.。。Nk.bin = {P:{TP = A}，V:{TP = A，TP = B}}-所有節點都有憑證 A，所有節點都會接受憑證 A 或 B
      
此時，叢集會再次均衡，而升級/變更安全性設定的第二個階段可以開始：
  - 完成升級網域0時： N0 = {P:{TP = A，TP = B}，V:{TP = A，TP = B}}，.。。Nk.bin = {P:{TP = A}，V:{TP = A，TP = B}}-UD0 中的節點將會開始呈現 B，而叢集中的任何其他節點都會接受這項功能。
  - 完成最後一個升級網域時： N0 = {P:{TP = A，TP = B}，V:{TP = A，TP = B}}，.。。Nk.bin = {P:{TP = A，TP = B}，V:{TP = A，TP = B}}-所有節點都已切換為出示憑證 B。現在可以從叢集定義淘汰/移除憑證 A，以及後續的一組升級。

#### <a name="converting-a-cluster-from-thumbprint--to-common-name-based-certificate-declarations"></a>將叢集從指紋轉換為一般名稱型憑證宣告
同樣地，將憑證宣告的類型（從 [指紋] 變更為 [一般名稱]）也會遵循與上述相同的模式。 請注意，驗證規則允許在相同的叢集定義中，以指紋和一般名稱宣告指定角色的憑證。 不過，相較之下，呈現規則只允許一種形式的宣告。 順便一提，將叢集憑證從指紋轉換為一般名稱的安全方法，是先依照指紋引進預定的目標憑證，然後再將該宣告變更為通用名稱。 在下列範例中，我們假設指紋 ' A ' 和主體一般名稱 ' B ' 都參考相同的憑證。 

  - 初始狀態： N0 = {P:{TP = A}，V:{TP = A}}，.。。Nk.bin = {P:{TP = A}，V:{TP = A}}-叢集是待用的，所有節點都共用一個通用設定，而且是主要憑證指紋
  - 完成升級網域0時： N0 = {P:{TP = A}，V:{TP = A，CN = B}}，.。。Nk.bin = {P:{TP = A}，V:{TP = A}}-UD0 中的節點會顯示憑證 A，並接受指紋 A 或通用名稱 B 的憑證。所有其他節點都存在，並只接受憑證 A
  - 完成最後一個升級網域時： N0 = {P:{TP = A}，V:{TP = A，CN = B}}，.。。Nk.bin = {P:{TP = A}，V:{TP = A，CN = B}}-所有節點都有憑證 A，所有節點都接受憑證 A （TP）或 B （CN）

到目前為止，我們可以繼續使用後續的升級來變更簡報規則：
  - 完成升級網域0時： N0 = {P:{CN = B}，V:{TP = A，CN = B}}，.。。Nk.bin = {P:{TP = A}，V:{TP = A，CN = B}}-UD0 中的節點會顯示 CN 找到的憑證 B，並接受指紋 A 或通用名稱 B 的憑證。所有其他節點都存在，並只接受憑證 A （依指紋選取）
  - 完成最後一個升級網域時： N0 = {P:{CN = B}，V:{TP = A，CN = B}}，.。。Nk.bin = {P:{CN = B}，V:{TP = A，CN = B}}-所有節點都有 CN 找到的憑證 B，所有節點都會接受憑證 A （TP）或 B （CN）
    
完成階段2也會將叢集的轉換標示為一般名稱型憑證;在後續的叢集升級中，可以移除以指紋為基礎的驗證宣告。

> [!NOTE]
> 在 Azure Service Fabric 叢集中，上述的工作流程是由 Service Fabric 資源提供者協調;叢集擁有者仍須負責根據指定的規則（簡報或驗證）將憑證布建到叢集，並建議您在多個步驟中執行變更。

在另一篇文章中，我們將討論管理和布建憑證到 Service Fabric 叢集中的主題。

## <a name="troubleshooting-and-frequently-asked-questions"></a>疑難排解和常見問題
雖然在 Service Fabric 叢集中調試驗證相關問題並不簡單，但我們 hopeful 下列提示和秘訣可能有所説明。 開始調查最簡單的方式，就是檢查叢集節點上的 Service Fabric 事件記錄檔，而不一定只有顯示徵兆的節點，還有已啟動但無法連接到其中一個鄰近專案的節點。 在 Windows 上，重要性的事件通常會分別記錄在 [應用程式和服務 Logs\Microsoft-ServiceFabric\Admin] 或 [操作] 通道底下。 有時候，[啟用 CAPI2 記錄](https://docs.microsoft.com/archive/blogs/benjaminperkins/enable-capi2-event-logging-to-troubleshoot-pki-and-ssl-certificate-issues)可能會很有説明，可取得有關憑證驗證、CRL/CTL 的抓取等等的更多詳細資料（請記得在完成重現後停用它）。

在遇到驗證問題的叢集中，資訊清單本身的一般徵兆如下： 
  - 節點已關閉/迴圈 
  - 拒絕連線嘗試
  - 連接嘗試已超時

每個徵兆可能是由不同的問題所造成，而相同的根本原因可能會顯示不同的形式分門別類;因此，我們只會列出典型問題的小型範例，並提供修正的建議。 

* 節點可以交換訊息，但無法連接。 連線嘗試終止的可能原因是「憑證不相符」錯誤-Service Fabric 對 Service Fabric 連線中的其中一個合作物件呈現的憑證會使收件者的驗證規則失敗。 可能會伴隨下列其中一個錯誤： 
  ```C++
  0x80071c44    -2147017660 FABRIC_E_SERVER_AUTHENTICATION_FAILED
  ```
  進一步診斷/調查：在每個嘗試連線的節點上，判斷要呈現的憑證;檢查憑證並嘗試模擬驗證規則（檢查指紋或一般名稱是否相等，檢查簽發者指紋（若有指定）。

  另一個常見的錯誤代碼可能是：
  ```C++
  0x800b0109    -2146762487 CERT_E_UNTRUSTEDROOT
  ```
  在此情況下，憑證是由一般名稱宣告，而下列其中一項適用：
    - 簽發者未釘選，且根憑證不受信任，或者
    - 簽發者已釘選，但宣告不包含此憑證的直接發行者指紋

* 節點已啟動，但無法連接到其他節點;其他節點不會從失敗的節點接收輸入流量。 在這種情況下，本機節點上的憑證載入可能會失敗。 尋找下列錯誤：
  - 找不到憑證-請確定 LocalMachine\My （或指定的）憑證存放區的內容可以解析簡報規則中宣告的憑證。 
    失敗的可能原因包括： 
      - 憑證指紋宣告中有不正確字元
      - 未安裝憑證
      - 憑證已過期
      - 一般名稱宣告包含前置詞 ' CN = '
      - 宣告會指定萬用字元，而且憑證存放區中不會有完全相符的內容（宣告： CN = * mydomain，實際憑證： CN = server. mydomain .com）

  - 未知的認證-表示缺少與憑證對應的私密金鑰，通常會伴隨錯誤碼： 
    ```C++ 
    0x8009030d  -2146893043 SEC_E_UNKNOWN_CREDENTIALS
    0x8009030e  -2146893042 SEC_E_NO_CREDENTIALS
    ```
    若要解決此問題，請檢查私密金鑰是否存在;確認 SFAdmins 已授與私密金鑰的「讀取 | 執行」存取權。

  - 錯誤的提供者類型-表示加密新世代（CNG）憑證（「Microsoft 軟體金鑰儲存提供者」）;此時，Service Fabric 只支援 CAPI1 憑證。 通常會伴隨錯誤碼：
    ```C++
    0x80090014  -2146893804 NTE_BAD_PROV_TYPE
    ```
    若要解決此問題，請使用 CAPI1 重新建立叢集憑證（例如「Microsoft 增強型 RSA 和 AES 密碼編譯提供者」）提供者。 如需加密提供者的詳細資訊，請參閱[瞭解密碼編譯提供者](https://docs.microsoft.com/windows/win32/seccertenroll/understanding-cryptographic-providers)

