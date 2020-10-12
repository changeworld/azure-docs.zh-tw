---
title: X.509 Service Fabric 叢集中以憑證為基礎的驗證
description: 瞭解 Service Fabric 叢集中以憑證為基礎的驗證，以及如何偵測、緩和和修正憑證相關問題。
ms.topic: conceptual
ms.date: 03/16/2020
ms.custom: sfrev
ms.openlocfilehash: 36717f526f88af753f3929d62e84ee65be4320e9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86259023"
---
# <a name="x509-certificate-based-authentication-in-service-fabric-clusters"></a>X.509 Service Fabric 叢集中以憑證為基礎的驗證

本文將補充 [Service Fabric 叢集安全性](service-fabric-cluster-security.md)的簡介，並深入探討 Service Fabric 叢集中的憑證型驗證詳細資料。 我們假設讀者已經熟悉基本安全性概念，以及 Service Fabric 公開來控制叢集安全性的控制項。  

此標題底下涵蓋的主題：

* 以憑證為基礎的驗證基本概念
* 身分識別及其各自的角色
* 憑證設定規則
* 疑難排解和常見問題

## <a name="certificate-based-authentication-basics"></a>以憑證為基礎的驗證基本概念
總而言之，在安全性中，憑證是一種工具，用來系結實體的相關資訊 (主體) 擁有一組非對稱式密碼編譯金鑰，因此構成公開金鑰加密的核心結構。 憑證所代表的金鑰可以用來保護資料，或是證明金鑰持有者的身分識別;搭配公開金鑰基礎結構使用 (PKI) 系統時，憑證可以代表其主體的其他特性，例如網際網路網域的擁有權，或憑證 (（稱為憑證授權單位單位）或 CA) 授與的特定許可權。 憑證的常見應用是支援傳輸層安全性 (TLS) 密碼編譯通訊協定，以允許透過電腦網路進行安全通訊。 具體而言，用戶端和伺服器會使用憑證來確保其通訊的隱私權和完整性，也會進行相互驗證。

在 Service Fabric 中，叢集 (同盟) 的基礎層也建基於其他通訊協定的 TLS () ，以達成可靠且安全的參與節點網路。 透過 Service Fabric 用戶端 Api 連接到叢集時，也會使用 TLS 來保護流量，也會建立合作物件的身分識別。 具體而言，在 Service Fabric 中使用驗證時，憑證可以用來證明下列宣告：) 憑證認證的簡報者擁有憑證的私密金鑰 b) 憑證的 SHA-1 雜湊 ( ' 指紋 ' ) 符合叢集定義中包含的宣告，或 c) 憑證的辨別主體一般名稱符合叢集定義中包含的宣告，且憑證的簽發者是已知或受信任的。

在上述清單中，' b ' 堆疊稱為「指紋釘選」;在此情況下，宣告會參考特定的憑證，而驗證配置的強度會在內部部署上，其為不可行的憑證，它會產生與另一個雜湊值相同的雜湊值，而在其他所有方面仍然是有效且格式正確的物件。 專案 ' c ' 代表宣告憑證的替代形式，其中配置的強度會在憑證主體和發行授權單位的組合上。 在此情況下，宣告會參考憑證的類別，任何兩個具有相同特性的憑證都會被視為完全相等。 

下列各節將深入說明 Service Fabric 執行時間如何使用並驗證憑證，以確保叢集安全性。

## <a name="identities-and-their-respective-roles"></a>身分識別及其各自的角色
在深入探討驗證或保護通道的詳細資料之前，請務必列出參與的動作專案以及它們在叢集中扮演的對應角色：
- Service Fabric 執行時間（稱為「系統」）：提供代表叢集之抽象概念和功能的一組服務。 參考系統實例之間的叢集通訊時，我們會使用「叢集身分識別」一詞。將叢集視為來自叢集外部流量的收件者/目標時，我們將使用「伺服器身分識別」一詞。
- 裝載的應用程式，稱為「應用程式」：叢集擁有者所提供的程式碼，該程式碼會在叢集中協調和執行
- 用戶端：根據叢集設定，允許實體連接和執行叢集中的功能。 我們會分別區分兩個層級的許可權： ' user ' 和 ' admin '。 「使用者」用戶端主要是限制為唯讀作業 (但不是所有唯讀功能) ，而「admin 用戶端」則可不受限制地存取叢集的功能。  (需詳細資訊，請參閱 [Service Fabric 叢集中的安全性角色](service-fabric-cluster-security-roles.md)。 ) 
-  (僅限 Azure) Service Fabric 服務，這些服務會協調和公開 Service Fabric 叢集的作業和管理控制項，稱為「服務」。 視環境而定，「服務」可能會參考 Azure Service Fabric 資源提供者，或 Service Fabric 團隊擁有及操作的其他資源提供者。

在安全的叢集中，每個角色都可以使用自己的相異身分識別進行設定，宣告為預先定義之角色名稱與其對應認證的配對。 Service Fabric 支援宣告認證作為憑證或網域型服務主體。 此外，也支援 Windows/Kerberos-based 身分識別，但已超出本文的範圍; (請參閱 [Service Fabric 叢集中的 Windows 安全性](service-fabric-windows-cluster-windows-security.md)。在 Azure 叢集中 ) ，用戶端角色也可以宣告為以 [Azure Active Directory 為基礎的](service-fabric-cluster-creation-setup-aad.md)身分識別。

如同上述提到，Service Fabric 執行時間會在叢集中定義兩個層級的許可權： ' admin ' 和 ' user '。 系統管理員用戶端和「系統」元件都可以使用「系統管理員」許可權來運作，因此會彼此 undistinguishable。 建立與叢集的連線時，會將這兩個角色的 Service Fabric 執行時間授與已驗證的呼叫者，做為後續授權的基底。 我們將在下列各節中深入探討驗證。

## <a name="certificate-configuration-rules"></a>憑證設定規則
### <a name="validation-rules"></a>驗證規則
Service Fabric 叢集的安全性設定會描述下列層面：
- 驗證類型：這是叢集的建立時間且不可變的特性。 這類設定的範例包括 ' >clustercredentialtype '、' ServerCredentialType '，而允許的值為 ' none '、' x509 ' 或 ' windows '。 本文著重于 x509 類型驗證。
-  (authentication) 驗證規則;這些設定是由叢集擁有者所設定，並說明應針對指定角色接受哪些認證。 範例將在下方深入探討。
- 用來調整或細微改變驗證結果的設定;此處的範例包括旗標 (取消 ) 限制憑證撤銷清單的強制執行等。

> [!NOTE]
> 下面提供的叢集設定範例是來自于 XML 格式的叢集資訊清單中的摘錄，其為最子集的格式，可直接支援本文中所述的 Service Fabric 功能。 您可以直接在叢集定義的 JSON 標記法中表示相同的設定，不論是獨立的 json 叢集資訊清單，還是 Azure 資源管理範本。

憑證驗證規則包含下列元素：
- 對應的角色：用戶端、管理員用戶端 (特殊許可權角色) 
- 角色接受的認證，以指紋或主體一般名稱宣告

#### <a name="thumbprint-based-certificate-validation-declarations"></a>以指紋為基礎的憑證驗證聲明
在以指紋為基礎的驗證規則案例中，要求連線到叢集的呼叫端所提供的認證將會依照下列方式進行驗證：
  - 認證是有效且格式正確的憑證：可建立其鏈、簽章相符
  - 憑證的有效時間 (NotBefore <= 現在 < NotAfter) 
  - 憑證的 SHA-1 雜湊符合宣告，因為不區分大小寫的字串比較（排除所有空格）

在連鎖大樓或驗證期間遇到的任何信任錯誤，將會針對以指紋為基礎的宣告隱藏，但過期的憑證除外-雖然該情況下的布建也存在。 具體而言，與下列相關的失敗：撤銷狀態不明或離線、不受信任的根、不正確金鑰使用方式、部分鏈視為非嚴重錯誤;在此案例中，前提是憑證只是金鑰組的信封，安全性是指叢集擁有者已設定 [地點] 量值以保護私密金鑰的事實。

以下是來自叢集資訊清單的摘錄」舉例說明，這是一組以指紋為基礎的驗證規則：

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

上述每個專案都會參考先前所述的特定身分識別;每個專案也允許將多個值指定為以逗號分隔的字串清單。 在此範例中，成功驗證傳入的認證時，具有 SHA-1 指紋的憑證的展示者會 5ec .。。4264 ' 將被授與 ' admin ' 角色;相反地，使用憑證 ' 7c8f ' 進行驗證的呼叫端 .。。01b0 ' 將被授與「使用者」角色，但限制為主要是唯讀作業。 輸入呼叫端，其憑證指紋為 ' abcd .。。1234 ' 或 ' ef01 .。。5678 ' 將接受成為叢集中的對等節點。 最後，連接到叢集管理端點的用戶端會預期伺服器憑證的指紋為 ' ef01 .。。5678 '。 

如先前所述，Service Fabric 會進行接受過期憑證的布建;原因是憑證的存留期有限，而當指紋宣告 (參考特定的憑證實例) ，允許憑證過期將會導致連線到叢集失敗，或直接折迭叢集。 很容易忘記或忽略指紋固定憑證的輪替，但不幸的是，從這類情況的復原很困難。

為此，叢集擁有者可以明確陳述指紋所宣告的自我簽署憑證應視為有效，如下所示：

```xml
  <Section Name="Security">
    <Parameter Name="AcceptExpiredPinnedClusterCertificate" Value="true" />
  </Section>
```
此行為不會延伸至 CA 發行的憑證;如果是這種情況，撤銷的已知已遭盜用的憑證可能會在 CA 的憑證撤銷清單中不再算出，因此會出現安全性風險。 使用自我簽署憑證時，叢集擁有者會被視為唯一負責保護憑證私密金鑰的合作物件，但 CA 發出的憑證則不會發生，而叢集擁有者可能不會察覺其憑證遭到洩漏的方式或時間。

#### <a name="common-name-based-certificate-validation-declarations"></a>以名稱為基礎的一般憑證驗證聲明
一般以名稱為基礎的宣告會採用下列其中一種形式：
- 只 (主體一般名稱) 
- 具有簽發者釘選的主體一般名稱

讓我們先從叢集資訊清單中，考慮 exemplifying 兩種宣告樣式的摘錄：
```xml
    <Section Name="Security/ServerX509Names">
      <Parameter Name="server.demo.system.servicefabric.azure-int" Value="" />
    </Section>
    <Section Name="Security/ClusterX509Names">
      <Parameter Name="cluster.demo.system.servicefabric.azure-int" Value="1b45...844d,d7fe...26c8,3ac7...6960,96ea...fb5e" />
    </Section>
```
宣告分別參考伺服器和叢集身分識別;請注意，以 CN 為基礎的宣告會在叢集資訊清單中有自己的區段，與標準的「安全性」分開。 在這兩個宣告中，' Name ' 代表憑證的辨別主體一般名稱，而 [值] 欄位則代表預期的簽發者，如下所示：

- 在第一個案例中，宣告指出伺服器憑證之辨別主體的一般名稱元素應符合字串 "server.demo.system. servicefabric";空白的 [值] 欄位表示預期憑證鏈的根目錄在驗證伺服器憑證的節點/電腦上是受信任的：在 Windows 上，這表示憑證可以連結至安裝在「信任的根 CA」存放區中的任何憑證;
- 在第二個案例中，如果憑證的一般名稱符合字串 "cluster.demo.system. servicefabric"， *而* 憑證的直接簽發者的指紋符合 [值] 欄位中的其中一個逗號分隔專案，則宣告會指出將憑證的呈現者視為叢集中的對等節點。  (此規則類型的堆疊稱為「具有簽發者釘選的一般名稱」。 ) 

在任一種情況下，都會建立憑證的鏈，而且應該不會有錯誤;也就是說，撤銷錯誤、部分鏈或時間不正確信任錯誤會被視為嚴重，而憑證驗證將會失敗。 釘選簽發者會導致「不受信任的根」狀態視為非嚴重錯誤;雖然外觀，但這是一種更嚴格的驗證形式，因為它可讓叢集擁有者將已授權/接受的簽發者集合限制為自己的 PKI。

建立憑證鏈之後，會使用宣告的主體作為遠端名稱的標準 TLS/SSL 原則進行驗證：如果憑證的主體一般名稱或其任何主體別名與叢集資訊清單中的 CN 宣告相符，就會將憑證視為相符。 在此情況下支援萬用字元，且字串比對不區分大小寫。

 (我們應該說明，您可以針對憑證所宣告的每個金鑰使用類型執行上述的順序;如果憑證指定用戶端驗證金鑰使用方式，則會先針對用戶端角色建立並評估鏈。 在成功的情況下，評估會完成且驗證成功。 如果憑證沒有用戶端驗證使用方式，或驗證失敗，Service Fabric 執行時間將會建立並評估伺服器驗證的鏈。 ) 

為了完成此範例，下列摘錄說明如何依照一般名稱宣告用戶端憑證：
```xml
    <Section Name="Security/AdminClientX509Names">
      <Parameter Name="admin.demo.client.servicefabric.azure-int" Value="1b45...844d,d7fe...26c8,3ac7...6960,96ea...fb5e" />
    </Section>
    <Section Name="Security/ClientX509Names">
      <Parameter Name="user.demo.client.servicefabric.azure-int" Value="1b45...844d,d7fe...26c8,3ac7...6960,96ea...fb5e" />
    </Section>
```

上述宣告分別對應至系統管理員和使用者身分識別;以這種方式宣告的憑證驗證，完全如同先前範例中的叢集和伺服器憑證所述。

> [!NOTE]
> 一般以名稱為基礎的宣告旨在簡化對叢集憑證的管理和一般管理。 不過，建議您遵循下列建議，以確保叢集的持續可用性和安全性：
  * 偏好簽發者釘選以依賴受信任的根目錄
  * 避免混合來自不同 Pki 的簽發者
  * 確認憑證宣告上列出所有預期的簽發者;不相符的簽發者會導致驗證失敗
  * 確定 PKI 的憑證原則端點可供探索、可用且可供存取-這表示 AIA、CRL 或 OCSP 端點是在分葉憑證上宣告，而且可供存取，以完成憑證鏈建立。

將其全部系結在一起，在以 x.509 憑證保護的叢集中收到連接要求時，Service Fabric 執行時間將會使用叢集的安全性設定來驗證遠端合作物件的認證，如上面所述;如果成功，則會將呼叫端/遠端方視為經過驗證。 如果認證符合多個驗證規則，則執行時間會將任何相符規則的最高許可權角色授與呼叫者。 

### <a name="presentation-rules"></a>展示規則
上一節說明了驗證如何在受憑證保護的叢集中運作;本節將說明 Service Fabric runtime 本身如何探索和載入用於叢集內通訊的憑證;我們稱之為「簡報」規則。

如同驗證規則，簡報規則會指定角色和相關聯的認證宣告（以指紋或一般名稱表示）。 不同于驗證規則，一般以名稱為基礎的宣告沒有簽發者釘選的條款;這可提供更大的彈性，並提升效能。 針對每個不同的節點類型，會在叢集資訊清單 (s) 的 ' NodeType ' 區段中宣告展示規則;這些設定會從叢集的安全性區段進行分割，讓每個節點類型在單一區段中具有完整設定。 在 Azure Service Fabric 叢集中，節點類型憑證宣告預設為其在叢集定義之 [安全性] 區段中的對應設定。

#### <a name="thumbprint-based-certificate-presentation-declarations"></a>以指紋為基礎的憑證呈現宣告
如先前所述，Service Fabric 執行時間會區分其角色為叢集中其他節點的對等，以及做為叢集管理作業的伺服器。 基本上，這些設定可以有明顯的設定，但實際上它們可能會對齊。 在本文的其餘部分，我們將假設設定符合簡單的設定。

讓我們從叢集資訊清單考慮下列摘錄：
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
' ClusterCertificate ' 元素示範完整的架構，包括選擇性參數 ( ' X509FindValueSecondary ' ) 或具有適當預設值 ( ' 上 x509storename ' ) ;其他宣告則會顯示縮寫格式。 上述叢集憑證宣告指出型別 ' nt1vm ' 之節點的安全性設定是以憑證 ' cc71 ' 初始化。1984 ' 作為主要和 ' 49e2 .。。19d6 作為次要憑證的憑證;您必須在 LocalMachine \' my ' 憑證存放區 (或 Linux 對等路徑中找到這兩個憑證： *var/lib/sfcerts*) 。

#### <a name="common-name-based-certificate-presentation-declarations"></a>一般以名稱為基礎的憑證呈現宣告
節點類型憑證也可以透過主體一般名稱來宣告，如下所示查詢示：

```xml
  <NodeTypes>
    <NodeType Name="nt1vm">
      <Certificates>
        <ClusterCertificate X509FindType="FindBySubjectName" X509FindValue="demo.cluster.azuredocpr.system.servicefabric.azure-int" Name="ClusterCertificate" />
      </Certificates>
    </NodeType>
  </NodeTypes>
```

針對其中一種類型的宣告，Service Fabric 節點會在啟動時讀取設定、找出並載入指定的憑證，並以其 NotAfter 屬性的遞減順序排序：系統會忽略過期的憑證，並選取清單中的第一個元素作為此節點嘗試之任何 Service Fabric 連接的用戶端認證。  (實際上，Service Fabric 優先于最接近的到期憑證。 ) 

請注意，對於以一般名稱為基礎的呈現宣告，如果其主體一般名稱等於宣告的 X509FindValue (或 X509FindValueSecondary) 欄位，就會將憑證視為相符，精確的字串比較。 這與驗證規則相較之下，其支援萬用字元比對，以及不區分大小寫的字串比較。  

### <a name="miscellaneous-certificate-configuration-settings"></a>其他憑證設定
先前已提及 Service Fabric 叢集的安全性設定也允許稍微變更驗證碼的行為。 當 [Service Fabric 叢集設定](service-fabric-cluster-fabric-settings.md) 的發行項代表最新的設定清單時，我們會在這裡展開一些安全性設定的意義，以完成以憑證為基礎之驗證的完整公開。 針對每個設定，我們將說明意圖、預設值/行為、其如何影響驗證，以及哪些值是可接受的。

如前所述，憑證驗證一律意指建立和評估憑證的鏈。 針對 CA 發出的憑證，這種明顯簡單的作業系統 API 呼叫通常需要對發出 PKI 的各種端點進行數個輸出呼叫、快取回應等等。 由於憑證驗證呼叫在 Service Fabric 叢集中的普及，PKI 端點中的任何問題可能會導致叢集的可用性降低，或可進行徹底的細分。 雖然無法隱藏輸出呼叫 (如需此) 的詳細資訊，請參閱下面的常見問題一節中的詳細資訊，下列設定可用於遮罩因 CRL 呼叫失敗造成的驗證錯誤。

  * CrlCheckingFlag-在 [安全性] 區段下，將字串轉換成 UINT。 Service Fabric 使用此設定的值，藉由變更鏈建築物的行為來遮罩憑證鏈狀態錯誤;它會以 ' dwFlags ' 參數的形式傳遞給 Win32 CryptoAPI [CertGetCertificateChain](/windows/win32/api/wincrypt/nf-wincrypt-certgetcertificatechain) 呼叫，並且可以設定為函式所接受之任何旗標的有效組合。 值為0時，會強制 Service Fabric 執行時間忽略任何信任狀態錯誤-這不是建議的作法，因為它的使用會構成重大的安全性風險。 預設值為 0x40000000 (CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT) 。

  使用時機：進行本機測試時，使用未完整格式的自我簽署憑證或開發人員憑證，或是沒有適當的公開金鑰基礎結構來支援憑證。 在 Pki 之間轉換時，也可能會在發生空調的環境中用來做為緩和措施。

  如何使用：我們將會採用會強制撤銷檢查只存取快取 Url 的範例。 假設：
  ```C++
  #define CERT_CHAIN_REVOCATION_CHECK_CACHE_ONLY         0x80000000
  ```
  然後，叢集資訊清單中的宣告會變成：
  ```xml
    <Section Name="Security">
      <Parameter Name="CrlCheckingFlag" Value="0x80000000" />
    </Section>
  ```

  * IgnoreCrlOfflineError-在 [安全性] 區段下，以預設值為 ' false ' 的布林值。 代表隱藏「撤銷離線」鏈建立錯誤狀態 (或後續連鎖原則驗證錯誤狀態的快捷方式) 。

  使用時機：本機測試，或使用不受適當 PKI 支援的開發人員憑證。 當已知無法存取 PKI 時，請將其當作緩和的環境使用。

  如何使用：
  ```xml
    <Section Name="Security">
      <Parameter Name="IgnoreCrlOfflineError" Value="true" />
    </Section>
  ```

  其他值得注意的設定 (在 [安全性] 區段下) ：
  * AcceptExpiredPinnedClusterCertificate-在專用於以指紋為基礎的憑證驗證一節中討論;允許接受過期的自我簽署叢集憑證。 
  * CertificateExpirySafetyMargin 間隔（以分鐘為單位）（以分鐘為單位）（以分鐘為單位）（以分鐘為單位）（以分鐘為單位），在憑證的 NotAfter 時間 Service Fabric 會監視 () 的叢集憑證，並定期發出健康情況報告的剩餘可用性。 在「安全」的間隔內，這些健康情況報告會提升為「警告」狀態。 預設值是 30 天。
  * CertificateHealthReportingInterval-控制健全狀況報告的頻率，與叢集憑證的剩餘時間有效性有關。 報表只會在每個間隔發出一次。 值以秒為單位，預設值為8小時。
  * EnforcePrevalidationOnSecurityChanges-布林值，控制偵測安全性設定變更時的叢集升級行為。 如果設定為 'true'，叢集升級會嘗試確保至少其中一個符合任何展示規則的憑證可以通過對應的驗證規則。 預先驗證會先執行，然後才會將新的設定套用至任何節點，但只會在起始升級時裝載叢集管理員服務主要複本的節點上執行。 在撰寫本文時，此設定的預設值為 ' false '，且會針對新的 Azure Service Fabric 叢集設定為 ' true '，且執行階段版本的版本是7.1。
 
### <a name="end-to-end-scenario-examples"></a> (範例) 的端對端案例
我們已探討簡報規則、驗證規則和調整旗標，但這兩者如何共同運作？ 在本節中，我們將逐步說明兩個端對端範例，示範如何運用安全性設定來進行安全的叢集升級。 請注意，這並不是在 Service Fabric 的適當憑證管理方面的完整長篇大論，請參閱該主題的相關文章。

呈現和驗證規則的分隔會帶來明顯的問題 (或考慮) 是否可以分離，以及結果為何。 事實上，節點的驗證憑證選擇不會通過另一個節點的驗證規則。 事實上，這項差異是驗證相關事件的主要原因。 同時，這些規則的分隔可讓叢集在升級期間繼續運作，以變更叢集的安全性設定。 請考慮，藉由在第一個步驟中增強第一個驗證規則，所有叢集的節點都會在新的設定上收斂，同時仍會使用目前的認證。 

回想一下，在 Service Fabric 叢集中，升級會透過 (最多5個) 「升級網域」或 ud。 只有目前 UD 中的節點會在指定時間進行升級/變更，而且只有在叢集的可用性允許時，才會繼續升級到下一個 UD。  (請參閱 [Service Fabric 叢集升級](service-fabric-cluster-upgrade.md) 和相同主題的其他文章，以取得更多詳細資料。 ) 憑證/安全性變更特別有風險，因為它們可以從叢集隔離節點，或在仲裁遺失的邊緣保留叢集。

我們將使用下列標記法來描述節點的安全性設定：

Nk.bin： {P:{TP = A}，V:{TP = A}}，其中：
  - ' Nk.bin ' 代表升級網域*k*中的節點
  - ' P ' 代表節點目前的簡報規則 (假設我們只參考叢集憑證) ; 
  - ' V ' 代表節點目前的驗證規則 (叢集憑證) 
  - ' TP = A ' 代表以指紋為基礎的宣告 (TP) ，其中 ' A ' 是憑證指紋
  - ' CN = B ' 代表 (CN) 的一般名稱型宣告，' B ' 是憑證的主體一般名稱 

#### <a name="rotating-a-cluster-certificate-declared-by-thumbprint"></a>輪替指紋所宣告的叢集憑證
下列順序說明如何使用2階段升級安全地引進次要叢集憑證（以指紋宣告）;第一個階段會在驗證規則中引進新的憑證宣告，而第二個階段則是在展示規則仲介紹：
  - 初始狀態： N0 = {P:{TP = A}，V:{TP = A}}，.。。Nk.bin = {P:{TP = A}，V:{TP = A}}-叢集為待用，所有節點共用一般設定
  - 完成升級網域0時： N0 = {P:{TP = A}，V:{TP = A，TP = B}}，.。。Nk.bin = {P:{TP = A}，V:{TP = A}}-UD0 中的節點會呈現憑證 A，並接受憑證 A 或 B;所有其他節點都存在，而且只接受憑證 A
  - 完成最後一個升級網域時： N0 = {P:{TP = A}，V:{TP = A，TP = B}}，.。。Nk.bin = {P:{TP = A}，V:{TP = A，TP = B}}-所有節點都有憑證 A，所有節點都會接受憑證 A 或 B
      
此時，叢集會再次均衡，而且升級/變更安全性設定的第二個階段可以開始：
  - 完成升級網域0時： N0 = {P:{TP = A，TP = B}，V:{TP = A，TP = B}}，.。。Nk.bin = {P:{TP = A}，V:{TP = A，TP = B}}-UD0 中的節點將會開始呈現 B，而叢集中的任何其他節點都會接受這項功能。
  - 完成最後一個升級網域時： N0 = {P:{TP = A，TP = B}，V:{TP = A，TP = B}}，.。。Nk.bin = {P:{TP = A，TP = B}，V:{TP = A，TP = B}}-所有節點都已切換為出示憑證 B。憑證 A 現在可從叢集定義中淘汰/移除，以及後續的一組升級。

#### <a name="converting-a-cluster-from-thumbprint--to-common-name-based-certificate-declarations"></a>將叢集從指紋轉換為一般名稱型的憑證宣告
同樣地，將憑證宣告類型 (從指紋變更為一般名稱) 將遵循與上述相同的模式。 請注意，驗證規則允許在相同的叢集定義中，以指紋和一般名稱宣告指定角色的憑證。 不過，相較之下，表示規則只允許一種形式的宣告。 順便一提，將叢集憑證從指紋轉換為一般名稱的安全方法是先依指紋導入預定的目標憑證，然後將該宣告變更為一般以名稱為基礎的名稱。 在下列範例中，我們會假設指紋 ' A ' 和主體一般名稱 ' B ' 參考相同的憑證。 

  - 初始狀態： N0 = {P:{TP = A}，V:{TP = A}}，.。。Nk.bin = {P:{TP = A}，V:{TP = A}}-叢集為待用，所有節點共用一般設定，並具有成為主要憑證指紋
  - 完成升級網域0時： N0 = {P:{TP = A}，V:{TP = A，CN = B}}，.。。Nk.bin = {P:{TP = A}，V:{TP = A}}-UD0 中的節點會呈現憑證 A，並接受指紋 A 或一般名稱 B 的憑證所有其他節點都存在，而且只接受憑證 A
  - 完成最後一個升級網域時： N0 = {P:{TP = A}，V:{TP = A，CN = B}}，.。。Nk.bin = {P:{TP = A}、V:{TP = A、CN = B}}-所有節點都有憑證 A，所有節點都會接受憑證 A (TP) 或 B (CN) 

現在，我們可以繼續使用後續的升級來變更展示規則：
  - 完成升級網域0時： N0 = {P:{CN = B}，V:{TP = A，CN = B}}，.。。Nk.bin = {P:{TP = A}、V:{TP = A、CN = B}}-UD0 中的節點會顯示 CN 找到的憑證 B，並接受指紋 A 或一般名稱 B 的憑證;所有其他節點都會出現，並只接受憑證 A （依指紋選取）
  - 完成最後一個升級網域時： N0 = {P:{CN = B}，V:{TP = A，CN = B}}，.。。Nk.bin = {P:{CN = B}，V:{TP = A，CN = B}}-所有節點都是由 CN 找到的憑證 B，所有節點都會接受憑證 A (TP) 或 B (CN) 
    
完成階段2也會將叢集的轉換標示為一般以名稱為基礎的憑證;您可以在後續的叢集升級中移除以指紋為基礎的驗證宣告。

> [!NOTE]
> 在 Azure Service Fabric 叢集中，以上顯示的工作流程是由 Service Fabric 資源提供者進行協調;叢集擁有者仍須負責根據指定的規則 (呈現或驗證) ，將憑證布建到叢集，建議您在多個步驟中執行變更。

在另一篇文章中，我們將說明在 Service Fabric 叢集中管理和布建憑證的主題。

## <a name="troubleshooting-and-frequently-asked-questions"></a>疑難排解和常見問題
在 Service Fabric 叢集中偵測與驗證相關的問題並不容易，我們會觀點下列提示，並提示您提供協助。 開始調查最簡單的方式是檢查叢集節點上的 Service Fabric 事件記錄檔，但不一定只是顯示徵兆的事件記錄，也不一定是已啟動但無法連線到其中一個相鄰節點的節點。 在 Windows 中，重要性的事件通常會分別記錄在「應用程式和服務 Logs\Microsoft-ServiceFabric\Admin」或「作業」通道下。 有時候， [啟用 CAPI2 記錄](/archive/blogs/benjaminperkins/enable-capi2-event-logging-to-troubleshoot-pki-and-ssl-certificate-issues)可能會很有説明，以取得更多有關憑證驗證、CRL/CTL 的抓取等詳細資料。 (記得在完成重現之後停用它，這可能相當詳細。 ) 

在發生驗證問題的叢集中，資訊清單本身的一般徵兆如下： 
  - 節點已關閉/迴圈 
  - 連接嘗試遭到拒絕
  - 連接嘗試超時

每個徵兆都可能因為不同的問題而造成，而且相同的根本原因可能會顯示不同的表徵;因此，我們只會列出一般問題的小型範例，以及修正這些問題的建議。 

* 節點可以交換訊息但無法連接。 連接嘗試終止的可能原因是「憑證不相符」錯誤-Service Fabric 對 Service Fabric 連線中的其中一個合作物件正在呈現憑證，而該憑證無法通過收件者的驗證規則。 可能伴隨著下列其中一個錯誤： 
  ```C++
  0x80071c44    -2147017660 FABRIC_E_SERVER_AUTHENTICATION_FAILED
  ```
  若要進一步診斷/調查：在嘗試連線的每個節點上，判斷正在呈現的憑證;檢查憑證，並嘗試並模擬驗證規則 (檢查指紋或一般名稱是否相等，如果指定) ，請檢查簽發者指紋。

  另一個常見的錯誤代碼可能是：
  ```C++
  0x800b0109    -2146762487 CERT_E_UNTRUSTEDROOT
  ```
  在此情況下，憑證是由一般名稱所宣告，且下列其中一項適用：
    - 簽發者未釘選，且根憑證不受信任，或
    - 簽發者已釘選，但宣告不包含此憑證的直接簽發者指紋

* 節點已啟動，但無法連接到其他節點;其他節點不會接收失敗節點的輸入流量。 在此情況下，本機節點上的憑證載入可能會失敗。 尋找下列錯誤：
  - 找不到憑證-請確定 LocalMachine\My (的內容可以解析呈現規則中宣告的憑證，或) 憑證存放區所指定的憑證。 
    可能的失敗原因包括： 
      - 指紋宣告中有不正確字元
      - 未安裝憑證
      - 憑證已過期
      - 一般名稱宣告包含前置詞 ' CN = '
      - 宣告指定萬用字元，但憑證存放區中沒有完全相符的 (宣告： CN = *. mydomain，實際憑證： CN = server. mydomain) 

  - 未知的認證-表示對應到憑證的遺失私密金鑰，通常伴隨錯誤碼： 
    ```C++ 
    0x8009030d  -2146893043 SEC_E_UNKNOWN_CREDENTIALS
    0x8009030e  -2146893042 SEC_E_NO_CREDENTIALS
    ```
    若要解決此情況，請檢查私密金鑰是否存在;確認 SFAdmins 已被授與私密金鑰的「讀取 | 執行」存取權。

  - 錯誤的提供者類型-表示加密新世代 (CNG) 憑證 ( 「Microsoft 軟體金鑰儲存提供者」 ) ;目前，Service Fabric 只支援 CAPI1 憑證。 通常伴隨錯誤碼：
    ```C++
    0x80090014  -2146893804 NTE_BAD_PROV_TYPE
    ```
    若要解決此情況，請使用 CAPI1 重新建立叢集憑證 (例如「Microsoft 增強型 RSA 和 AES 密碼編譯提供者」 ) 提供者。 如需加密提供者的詳細資訊，請參閱 [瞭解密碼編譯提供者](/windows/win32/seccertenroll/understanding-cryptographic-providers)
