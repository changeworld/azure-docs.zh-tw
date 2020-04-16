---
title: X.509 服務結構群集中基於證書的身份驗證
description: 瞭解 Service Fabric 群集中基於證書的身份驗證,以及如何檢測、緩解和修復與證書相關的問題。
ms.topic: conceptual
ms.date: 03/16/2020
ms.custom: sfrev
ms.openlocfilehash: 699015e322c599dea996b3a8b9dbc0a4589440ab
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429664"
---
# <a name="x509-certificate-based-authentication-in-service-fabric-clusters"></a>X.509 服務結構群集中基於證書的身份驗證

本文補充了[Service Fabric 群集安全性](service-fabric-cluster-security.md)的介紹,並詳細介紹了 Service Fabric 群集中基於證書的身份驗證的詳細資訊。 我們假設讀者熟悉基本的安全概念,以及 Service Fabric 公開控制群集安全性的控制項。  

此標題涵蓋的主題:

* 根據憑證的身份驗證基礎知識
* 身份及其各自角色
* 憑證設定規則
* 容損損和常見問題

## <a name="certificate-based-authentication-basics"></a>根據憑證的身份驗證基礎知識
作為一個簡短的複習,在安全性中,證書是一種工具,旨在將有關實體(主體)的資訊綁定到他們擁有一對非對稱加密密鑰,因此構成了公鑰加密的核心構造。 證書表示的密鑰可用於保護數據或證明金鑰持有者的身份;當與公鑰基礎結構 (PKI) 系統結合使用時,證書可以表示其主體的其他特徵,例如互聯網域的所有權或證書頒發者(稱為證書頒發機構或 CA)授予它的某些特權。 證書的常見應用支援傳輸層安全 (TLS) 加密協議,允許透過電腦網路進行安全通訊。 具體來說,用戶端和伺服器使用證書來確保其通信的隱私和完整性,並進行相互身份驗證。

在 Service Fabric 中,群集(聯合)的基本層也基於 TLS(以及其他協定)構建,以實現參與節點的可靠、安全網路。 通過 Service Fabric 用戶端 API 連接到群集時,還可以使用 TLS 來保護流量,以及建立各方的身份。 具體而言,在 Service Fabric 中用於身份驗證時,證書可用於證明以下聲明:a) 證書認證的示範者擁有證書的私密金鑰 b)證書的 SHA-1 哈希(「指紋」)與群集定義中包含的聲明匹配,或者 c) 證書的區分主題通用名稱與群集定義中包含的聲明匹配和證書的頒發者已知或受信任。

在上面的清單中,「b」被通俗地稱為「指紋固定」;在這種情況下,聲明是指特定的證書,身份驗證方案的強度取決於在計算上不可行的證書,該證書產生與另一個相同的哈希值,同時在所有其他方面仍然是一個有效、格式良好的物件。 專案"c"是宣佈證書的替代方法,其中方案的強度取決於證書主體和頒發機構的組合。 在這種情況下,聲明引用一類證書 - 任何具有相同特徵的兩個證書都被視為完全等效。 

以下各節將深入解釋 Service Fabric 運行時如何使用和驗證證書以確保群集安全性。

## <a name="identities-and-their-respective-roles"></a>身份及其各自角色
在深入瞭解身份驗證或保護通信通道的詳細資訊之前,必須列出參與的參與者及其在群集中扮演的相應角色:
- Service Fabric 運行時,稱為"系統":提供表示群集的抽象和功能的一組服務。 當提到系統實例之間的群集內通信時,我們將使用術語「群集標識」;當將群集稱為群集外部流量的接收者/目標時,我們將使用術語"伺服器標識"。
- 託管應用程式,稱為「應用程式」:由群集擁有者提供的代碼,該代碼在群集中進行編排和執行
- 用戶端:允許根據群集配置連接到群集中並執行功能的實體。 我們分別區分兩個級別的許可權 - 「使用者」和「管理員」。 "使用者"用戶端主要限於唯讀操作(但不是所有唯讀功能),而"管理員"用戶端可以不受限制地存取群集的功能。 (有關詳細資訊,請參閱[服務結構群集中的安全角色](service-fabric-cluster-security-roles.md)。
- (僅限 Azure)服務結構服務,用於協調並公開用於操作和管理服務結構群集的控制項,稱為"服務"。 根據環境,「服務」可能引用 Azure 服務結構資源提供程式或服務結構團隊擁有和操作的其他資源提供程式。

在安全群集中,每個角色都可以配置它們自己的不同標識,聲明為預定義角色名稱及其相應憑據的配對。 Service Fabric 支援將憑據聲明為證書或基於域的服務主體。 (支援基於 Windows/Kerberos 的識別,但超出了本文的範圍;請參閱[服務結構群集中基於 Windows 的安全性](service-fabric-windows-cluster-windows-security.md)。在 Azure 群組,用戶端角色也可以宣告為[Azure 活動目錄的識別](service-fabric-cluster-creation-setup-aad.md)。

如上所述,Service Fabric 運行時定義了群集中的兩個特權級別:"管理員"和"使用者"。 管理員用戶端和"系統"元件都將使用"管理員"許可權運行,因此無法相互區分。 在群集中建立連接時,Service Fabric 運行時將授予經過身份驗證的調用方作為後續授權的基點之一。 我們將在以下各節中深入檢查身份驗證。

## <a name="certificate-configuration-rules"></a>憑證設定規則
### <a name="validation-rules"></a>驗證規則
Service Fabric 群集的安全設置原則上描述了以下幾個方面:
- 身份驗證類型;這是群集的創建時間、不可變特性。 此類設置的範例包括"群集認證類型"、"伺服器認證類型",允許的值為"無"、"x509"或"視窗"。 本文重點介紹 x509 類型的身份驗證。
- (身份驗證)驗證規則;這些設置由群集擁有者設置,並描述給定角色應接受哪些憑據。 下面將深入研究示例。
- 用於調整或巧妙地更改身份驗證結果的設置;此處的範例包括限制執行證書吊銷清單的標誌(取消)等。

> [!NOTE]
> 下面提供的群集配置範例是 XML 格式的群集清單的摘錄,作為直接支援本文中描述的 Service Fabric 功能的最消化格式。 相同的設置可以直接在群集定義的 JSON 表示形式中表示,無論是獨立的 json 群集清單還是 Azure 資源管理範本。

憑證驗證規則包含以下元素:
- 相應的角色:用戶端、管理員用戶端(特權角色)
- 為角色接受的憑據,通過指紋或主題通用名稱聲明

#### <a name="thumbprint-based-certificate-validation-declarations"></a>基於指紋的憑證驗證聲明
對於基於指紋的驗證規則,請求連接到群集的調用方提供的憑據將驗證如下:
  - 認證是一個有效的、格式良好的證書:可以建構其鏈,簽名匹配
  - 憑證是時間有效的(在<之前 = 現在<不是)
  - 憑證的 SHA-1 哈希與聲明匹配,作為不區分大小寫的字串比較,不包括所有空格

鏈構建或驗證期間遇到的任何信任錯誤都將針對基於指紋的聲明進行抑制,但過期證書除外,儘管該案例也確實存在規定。 具體來說,與:吊銷狀態未知或脫機、不受信任的根、無效的密鑰使用、部分鏈相關的失敗被視為非致命錯誤;在這種情況下,前提是證書只是密鑰對的信封 - 安全性在於群集擁有者已設置位置度量值來保護私鑰。

以下來自群集清單的摘錄體現了這樣一組基於指紋的驗證規則:

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

上述每個項目都引用前面描述的特定標識;每個項目還允許指定多個值,作為逗號分隔的字串清單。 在此範例中,在成功驗證傳入憑據後,帶有 SHA-1 指紋的證書的演示者"d5ec...4264'將被授予"管理員"角色;相反,使用憑證「7c8f...01b0 將被授予"使用者"角色,僅限於唯讀操作。 顯示指紋為"abcd...1234" 或"ef01"5678' 將作為群集中的對等節點被接受。 最後,連線到叢集的管理終結點的用戶端將期望伺服器憑證的指紋為「ef01...5678'. 

如前所述,Service Fabric 確實為接受過期證書提供了規定;原因是證書的生存期有限,並且,當通過指紋聲明(指特定的證書實例)時,允許證書過期將導致無法連接到群集,或群集完全崩潰。 很容易忘記或忽略旋轉指紋固定證書,不幸的是,從這種情況恢復是困難的。

為此,群集擁有者可以明確說明,由指紋聲明的自簽名證書應視為有效,如下所示:

```xml
  <Section Name="Security">
    <Parameter Name="AcceptExpiredPinnedClusterCertificate" Value="true" />
  </Section>
```
此行為不擴展到 CA 頒發的證書;因此,此行為不會擴展到 CA 頒發的證書。如果是這樣的話,一個被吊銷的、已知待洩露的過期證書,只要它不再出現在 CA 的證書吊銷清單中,就會變得"有效",從而帶來安全風險。 使用自簽名證書時,群集擁有者被視為負責保護證書私鑰的唯一一方,而 CA 頒發的證書則不是這種情況 - 群集擁有者可能不知道其證書被聲明如何或何時被宣布洩露。

#### <a name="common-name-based-certificate-validation-declarations"></a>常見的基於名稱的憑證驗證聲明
基於名稱的常見聲明採用以下形式之一:
- 主題通用名稱(僅)
- 主題通用名稱與發行者固定

讓我們首先考慮一個類清單的摘錄,該清單體現了兩種聲明樣式:
```xml
    <Section Name="Security/ServerX509Names">
      <Parameter Name="server.demo.system.servicefabric.azure-int" Value="" />
    </Section>
    <Section Name="Security/ClusterX509Names">
      <Parameter Name="cluster.demo.system.servicefabric.azure-int" Value="1b45...844d,d7fe...26c8,3ac7...6960,96ea...fb5e" />
    </Section>
```
聲明分別引用伺服器和群集標識;請注意,基於 CN 的聲明在群集清單中有自己的部分,與標準「安全性」分開。 在這兩個聲明中,「Name」表示憑證的可區分主題通用名稱,而「價值」欄位表示預期的頒發者,如下所示:

- 在第一種情況下,聲明指出伺服器證書的可分辨主題的通用名稱元素應與字串"server.demo.system.system.servicefabric.azure-int"匹配;空「值」欄位表示期望證書鏈的根在正在驗證伺服器證書的節點/電腦上受信任;在 Windows 上,這意味著證書可以連結到「受信任的根 CA」存儲中安裝的任何證書;
- 在第二種情況下,聲明指出,如果證書的通用名稱與字串"cluster.demo.system.system.servicefabric.azure-int"匹配,則證書的演示者作為群集中的對等節點被接受,*並且*證書的直接頒發者與"值"欄位中的逗號分隔條目之一匹配。 (此規則類型被通俗地稱為"具有頒發者固定的通用名稱"。

在這兩種情況下,證書的鏈都是構建的,並且預期沒有錯誤;即,吊銷錯誤、部分鏈或時間無效信任錯誤被視為致命錯誤,並且證書驗證將失敗。 固定頒發者將導致將「不受信任的根」狀態視為非致命錯誤;儘管出現,這是一種更嚴格的驗證形式,因為它允許群集擁有者將授權/接受的頒發器集約束到自己的 PKI。

構建憑證鏈後,根據標準 TLS/SSL 策略驗證它,聲明的主題為遠端名稱;如果證書的主題通用名稱或其任何主題替代名稱與群集清單中的 CN 聲明匹配,則證書將被視為匹配項。 在這種情況下,支援通配符,並且字串匹配不區分大小寫。

(我們應該明確,上述序列可以針對證書聲明的每種類型的密鑰用法執行;如果證書指定用戶端身份驗證密鑰用法,則首先為用戶端角色構建並計算鏈。 如果成功,評估完成並驗證成功。 如果證書沒有用戶端身份驗證用法,或者驗證失敗,則 Service Fabric 運行時將生成和評估伺服器身份驗證鏈。

為了完成該範例,以下摘錄說明了按通用名稱聲明客戶端證書:
```xml
    <Section Name="Security/AdminClientX509Names">
      <Parameter Name="admin.demo.client.servicefabric.azure-int" Value="1b45...844d,d7fe...26c8,3ac7...6960,96ea...fb5e" />
    </Section>
    <Section Name="Security/ClientX509Names">
      <Parameter Name="user.demo.client.servicefabric.azure-int" Value="1b45...844d,d7fe...26c8,3ac7...6960,96ea...fb5e" />
    </Section>
```

上述聲明分別對應於管理員和用戶標識;以這種方式聲明的證書的驗證與前面的示例群集和伺服器證書的描述完全一致。

> [!NOTE]
> 基於名稱的常見聲明旨在簡化群集證書的輪換,以及一般管理群集證書。 但是,建議遵守以下建議,以確保群集的持續可用性和安全性:
  * 更喜歡頒發者固定而不是依賴受信任的根
  * 避免混合來自不同 PKI 的發行人
  * 確保所有預期的發行人都列在證書聲明中;不符合的簽發者會導致驗證失敗
  * 確保 PKI 的憑證策略終結點是可發現、可用和可訪問的 - 這意味著 AIA、CRL 或 OCSP 終結點在葉證書上聲明,並且它們可以訪問,以便證書鏈構建可以完成。

將所有這些內容捆綁在一起,在收到使用 X.509 證書保護的群集中的連接請求時,Service Fabric 運行時將使用群集的安全設置來驗證上述遠端方的認證;如果成功,則調用方/遠端方被視為經過身份驗證。 如果憑據與多個驗證規則匹配,運行時將授予調用方任何匹配規則的最高特權角色。 

### <a name="presentation-rules"></a>示範規則
上一節介紹身份驗證在證書保護群集中的工作原理;本節將解釋 Service Fabric 運行時本身如何發現和載入它用於群集內通信的證書;我們稱這些為"演示"規則。

與驗證規則一樣,表示規則指定角色和關聯的憑據聲明,這些聲明由指紋或通用名稱表示。 與驗證規則不同,基於名稱的通用聲明沒有用於頒發者固定的規定;這提供了更大的靈活性,以及更高的性能。 表示規則在群集清單的「NodeType」部分(s)中聲明,每個不同的節點類型;設置從群集的安全部分拆分,以允許每個節點類型在單個部分中具有其完整配置。 在 Azure 服務結構群集中,節點類型證書聲明預設為群集定義的安全部分中的相應設置。

#### <a name="thumbprint-based-certificate-presentation-declarations"></a>基於指紋的憑證表示聲明
如前所述,Service Fabric 運行時區分其作為群集中其他節點的對等體的角色和群集管理操作的伺服器。 原則上,這些設置可以明顯配置,但實際上它們傾向於對齊。 對於本文的其餘部分,我們將假定設置匹配,以便簡單。

讓我們考慮以下群集清單的摘錄:
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
"群集證書"元素演示完整架構,包括可選參數("X509FindValue")或具有適當預設值的參數("X509StoreName");其他聲明顯示縮寫形式。 上面的群集證書聲明指出,類型為"nt1vm"的節點的安全設置使用證書"cc71"進行初始化。1984年'為主,和'49e2.。19d6' 證書作為輔助證書;這兩個證書都應在本地電腦\'My 的憑證儲存(或 Linux 等效路徑 *、var/lib/sfcerts)* 中找到。

#### <a name="common-name-based-certificate-presentation-declarations"></a>常見的基於名稱的憑證表示聲明
節點類型證書也可以按主題通用名稱聲明,如下圖所示:

```xml
  <NodeTypes>
    <NodeType Name="nt1vm">
      <Certificates>
        <ClusterCertificate X509FindType="FindBySubjectName" X509FindValue="demo.cluster.azuredocpr.system.servicefabric.azure-int" Name="ClusterCertificate" />
      </Certificates>
    </NodeType>
  </NodeTypes>
```

對於任一類型的聲明,Service Fabric 節點將在啟動時讀取配置,查找並載入指定的證書,並按其 NotAfter 屬性的降序排序它們;將忽略過期的證書,並且選擇清單的第一個元素作為此節點嘗試的任何 Service Fabric 連接的用戶端憑據。 (實際上,Service Fabric 傾向於最遠的過期證書。

請注意,對於基於通用名稱的表示聲明,如果證書的主題通用名稱等於聲明的 X509FindValue(或 X509FindValue)欄位,則證書被視為匹配項,作為區分大小寫的、精確的字元串比較。 這與驗證規則(它確實支援通配符匹配)以及不區分大小寫的字串比較形成鮮明對比。  

### <a name="miscellaneous-certificate-configuration-settings"></a>其他憑證設定設定
前面提到過,Service Fabric 群集的安全設置還允許巧妙地更改身份驗證代碼的行為。 雖然有關[Service Fabric 群集設置](service-fabric-cluster-fabric-settings.md)的文章表示全面和最新的設置清單,但我們將在此處擴展某些安全設置的含義,以完成基於證書的身份驗證的完整公開。 對於每個設置,我們將解釋意圖、預設值/行為、它如何影響身份驗證以及哪些值是可以接受的。

如前所述,證書驗證始終意味著構建和評估證書的鏈。 對於 CA 頒發的證書,這種看似簡單的 OS API 調用通常需要對頒發 PKI 的各個終結點進行多個出站調用、緩存回應等。 鑒於 Service Fabric 群集中證書驗證調用的流行程度,PKI 終結點中的任何問題都可能導致群集的可用性降低或徹底崩潰。 雖然無法禁止外出呼叫(請參閱下面的常見問題部分,瞭解有關此內容的更多內容),但以下設置可用於遮罩CRL呼叫失敗引起的驗證錯誤。

  * CrlCheckingFlag - 在"安全"部分下,字串轉換為 UINT。 Service Fabric 使用此設置的值通過更改鏈構建的行為來遮罩證書鏈狀態錯誤;它作為「dwFlags」參數傳遞到 Win32 CryptoAPI [CertGet 證書鏈](https://docs.microsoft.com/windows/win32/api/wincrypt/nf-wincrypt-certgetcertificatechain)調用,並可以設置為函數接受的任何有效標誌組合。 值 0 強制 Service Fabric 執行時忽略任何信任狀態錯誤 - 不建議這樣做,因為它的使用將構成重大的安全暴露。 默認值為 0x4000000(CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT)。

  使用時間:對於本地測試,具有未完全形成/沒有適當的公鑰基礎結構來支援證書的自簽名證書或開發人員證書。 在 PKI 之間的過渡期間,也可以在空氣間隙環境中用作緩解。

  如何使用:我們將舉一個示例,強制吊銷檢查以僅訪問緩存的 URL。 假設:
  ```C++
  #define CERT_CHAIN_REVOCATION_CHECK_CACHE_ONLY         0x80000000
  ```
  然後群集清單中的聲明變為:
  ```xml
    <Section Name="Security">
      <Parameter Name="CrlCheckingFlag" Value="0x80000000" />
    </Section>
  ```

  * 忽略CrlOfflineError - 在"安全"部分下,默認值為"false"的布爾。 表示禁止「離線吊銷」鏈構建錯誤狀態(或後續鏈策略驗證錯誤狀態)的快捷方式。

  何時使用:本地測試,或沒有適當的 PKI 支援的開發人員證書。 在空氣間隙環境中或已知無法訪問 PKI 時用作緩解措施。

  如何使用:
  ```xml
    <Section Name="Security">
      <Parameter Name="IgnoreCrlOfflineError" Value="true" />
    </Section>
  ```

  其他值得注意的設置(全部在"安全"部分下):
  * 接受過期固定群集證書 - 在專用於基於指紋的證書驗證部分中討論;允許接受過期的自簽名群集證書。 
  * 證書到期安全保證金 - 間隔,在證書的"非過期時間戳"之前幾分鐘表示,在此期間,證書被視為有過期風險。 Service Fabric 監視群集證書,並定期發佈有關其剩餘可用性的運行狀況報告。 在「安全」間隔內,這些運行狀況報告將提升為"警告"狀態。 預設值是 30 天。
  * 憑證執行狀況報告間隔 - 控制有關群集證書剩餘時間有效性的運行狀況報告的頻率。 報告將僅在此間隔發出一次。 該值以秒為單位表示,預設值為 8 小時。
  * 強制預驗證安全更改 - 布爾,在檢測到安全設置更改時控制群集升級的行為。 如果設置為"true",群集升級將嘗試確保至少一個與任何表示規則匹配的證書可以傳遞相應的驗證規則。 在將新設置應用於任何節點之前執行預驗證,但在啟動升級時僅在承載群集管理器服務主副本的節點上運行。 在撰寫本文時,該設置的預設值為"false",並且對於以 7.1 開頭的運行時版本的新 Azure 服務結構群集,該設置將設置為"true"。
 
### <a name="end-to-end-scenario-examples"></a>端到端介面(範例)
我們已經查看了表示規則、驗證規則和調整標誌,但這一切如何協同工作? 在本節中,我們將介紹兩個端到端示例,演示如何利用安全設置進行安全群集升級。 請注意,這不是關於 Service Fabric 中正確證書管理的全面論文,要查找有關該主題的配套文章。

列報規則和驗證規則的分離提出了一個明顯的問題(或擔心),即這些規則能否產生分歧,以及後果如何。 事實上,節點選擇的身份驗證證書可能無法通過另一個節點的驗證規則。 事實上,這種差異是導致與身份驗證相關的事件的主要原因。 同時,這些規則的分離允許群集在升級期間繼續運行,從而更改群集的安全設置。 請考慮,首先將驗證規則作為第一步進行增強,群集的所有節點都將在使用當前憑據時收斂到新設置上。 

回想一下,在 Service Fabric 群集中,升級通過(最多 5 個)「升級域」或「U」進行。 僅在給定時間升級/更改當前 UD 中的節點,並且只有在群集的可用性允許時,升級才會繼續到下一個 UD。 (有關更多詳細資訊,請參閱[服務結構群集升級](service-fabric-cluster-upgrade.md)和其他有關同一主題的文章。證書/安全更改的風險特別大,因為它們可以將節點與群集隔離,或使群集處於仲裁丟失的邊緣。

我們將使用以下表示法來描述節點的安全設定:

Nk: [P:[TP]A],V:[TP]A],其中:
  - 「Nk」表示升級網*域 k*的節點
  - "P"表示節點的當前表示規則(假設我們僅指群集證書); 
  - 「V」表示節點的認證規則(僅限群集憑證)
  - "TP_A"表示基於指紋的聲明 (TP),其中"A"表示證書指紋
  - "CN_B"表示基於名稱的通用聲明 (CN),其中"B"是證書的主題通用名稱 

#### <a name="rotating-a-cluster-certificate-declared-by-thumbprint"></a>旋轉由指紋聲明的叢集憑證
以下序列描述如何使用 2 階段升級安全地引入輔助群集證書(由指紋聲明);第一階段在驗證規則中引入了新的證書聲明,第二階段在表示規則中引入了它:
  - 初始狀態:N0 = [P:[TP]A},V:[TP]A},...Nk = [P:[TP]A},V:[TP]A}- 叢集處於靜止狀態,所有節點共用通用配置
  - 完成升級域 0: N0 = [P:[TP]A],V:[TP_A,TP=B],...Nk = [P:TP]A、V:[TP]A}- UD0 中的節點將呈現證書 A,並接受證書 A 或 B;所有其他節點僅存在並接受憑證 A
  - 完成上次升級域時:N0 = [P:[TP]A},V:[TP_A,TP=B],...Nk = [P:TP]A、V:[TP_A,TP_B]- 所有存在證書 A 的節點,所有節點都將接受證書 A 或 B
      
此時,群集再次處於平衡狀態,升級/更改安全設置的第二階段可以開始:
  - 完成升級域 0: N0 = [P:TP_A, TP=B], V:[TP_A, TP=B], ...Nk = [P:TP]A、V:[TP_A,TP_B]- UD0 中的節點將開始呈現 B,群集中的任何其他節點都接受 B。
  - 完成上次升級域時:N0 = [P:[TP]A,TP=B=,V:[TP_A,TP=B],...Nk = {P:[TP}A,TP=B],V:[TP]A,TP=B}- 所有節點都已切換到證書 B。 證書 A 現在可以通過一組後續升級從群集定義中停用/刪除。

#### <a name="converting-a-cluster-from-thumbprint--to-common-name-based-certificate-declarations"></a>將叢集從指紋轉換為基於通用名稱的憑證聲明
同樣,更改證書聲明的類型(從指紋到通用名稱)將遵循與上述相同的模式。 請注意,驗證規則允許通過指紋和同一群集定義中的通用名稱聲明給定角色的證書。 相比之下,表示規則只允許一種形式的聲明。 順便說一句,將群集證書從指紋轉換為通用名稱的安全方法是首先通過指紋引入預期的目標證書,然後將該聲明更改為基於名稱的通用證書。 在下面的示例中,我們將假設指紋"A"和主題通用名稱"B"引用同一證書。 

  - 初始狀態:N0 = [P:[TP]A},V:[TP]A},...Nk = [P:[TP]A、V:[TP]A}- 叢集處於靜止狀態,所有節點共用通用配置,A 是主證書指紋
  - 完成升級域 0: N0 = [P:[TP]A],V:[TP_A,CN=B],...Nk = [P:TP]A、V:[TP]A}- UD0 中的節點將呈現證書 A,並接受指紋 A 或通用名稱 B 的證書;所有其他節點僅存在並接受憑證 A
  - 完成上次升級域時:N0 = [P:[TP]A},V:[TP_A,CN=B],...Nk = [P:TP]A、V:[TP_A,CN_B]- 所有節點都存在證書 A,所有節點將接受證書 A (TP) 或 B (CN)

此時,我們可以通過後續升級繼續更改表示規則:
  - 完成升級域 0: N0 = [P:CN]B],V:[TP_A,CN=B],...Nk = [P:TP_A], V:[TP_A, CN_B] - UD0 中的節點將呈現 CN 找到的證書 B,並接受帶有指紋 A 或通用名稱 B 的證書;所有其他節點都存在並接受證書 A,由指紋選擇
  - 完成上次升級域時:N0 = [P:CN]B},V:[TP_A,CN=B],...Nk = [P:CN]B、V:[TP_A,CN]- 所有節點都提供 CN 找到的證書 B,所有節點將接受證書 A (TP) 或 B (CN)
    
第 2 階段完成的還標誌著群集轉換為基於名稱的通用證書;在後續的群集升級中,可以刪除基於指紋的驗證聲明。

> [!NOTE]
> 在 Azure 服務結構群集中,上面顯示的工作流由服務結構資源提供程式協調;在 Azure 服務結構結構群集中,上述工作流由服務結構資源提供程式協調。群集擁有者仍負責根據指示的規則(表示或驗證)將證書預配到群集中,並鼓勵他們執行多個步驟的更改。

在單獨的文章中,我們將討論將證書管理和預配到 Service Fabric 群集中的主題。

## <a name="troubleshooting-and-frequently-asked-questions"></a>容損損和常見問題
雖然在 Service Fabric 群集中調試與身份驗證相關的問題並不容易,但我們希望以下提示和提示會有所説明。 開始調查的最簡單方法是檢查群集節點上的 Service Fabric 事件日誌 - 不僅一定顯示症狀,而且檢查已啟動但無法連接到其鄰居之一的節點。 在 Windows 上,重要的事件通常分別記錄在"應用程式和服務日誌_Microsoft-ServiceFabric_Admin"或"操作"通道下。 有時,[啟用 CAPI2 日誌記錄](https://docs.microsoft.com/archive/blogs/benjaminperkins/enable-capi2-event-logging-to-troubleshoot-pki-and-ssl-certificate-issues)可能會有所説明,以獲取有關證書驗證、CRL/CTL 等檢索的更多詳細資訊(請記住在完成重新專業後禁用它,它可以相當詳細。

在遇到身份驗證問題的群集中表現出的典型癥狀包括: 
  - 節點已關閉/循環 
  - 連線嘗試被拒絕
  - 連線嘗試逾時

每個癥狀可能由不同的問題引起,並且相同的根本原因可能表現出不同的表現;因此,我們將只列出一個典型問題的一個小樣本,並推薦它們來修復它們。 

* 節點可以交換消息,但不能連接。 連接嘗試終止的可能原因是「證書不匹配」錯誤 - Service Fabric 到 Service Fabric 連接中的一方正在提交證書,該證書未通過收件人的驗證規則。 可能伴隨著以下任一錯誤: 
  ```C++
  0x80071c44    -2147017660 FABRIC_E_SERVER_AUTHENTICATION_FAILED
  ```
  要進一步診斷/調查:在嘗試連接的每個節點上,確定正在顯示的證書;檢查證書並嘗試類比驗證規則(檢查指紋或通用名稱相等性,如果指定,請檢查頒發者指紋)。

  另一個常見的伴隨錯誤代碼可能是:
  ```C++
  0x800b0109    -2146762487 CERT_E_UNTRUSTEDROOT
  ```
  在這種情況下,證書按通用名稱聲明,以下任一項適用:
    - 頒發者未固定,並且根證書不受信任,或者
    - 頒發者被固定,但聲明不包括此證書的直接頒發者的經驗

* 節點已打開,但無法連接到其他節點;其他節點不會從故障節點接收入站流量。 在這種情況下,證書載入可能在本地節點上失敗。 尋找以下錯誤:
  - 找不到憑證 - 確保表示規則中聲明的證書可以通過 LocalMachine_My(或指定)證書存儲的內容解析。 
    容錯的可能原因可能包括: 
      - 指紋宣告的不合法字元
      - 未安裝憑證
      - 憑證已過期
      - 通用名稱聲明包括首碼"CN+"
      - 宣告指定通配符,憑證儲存中不存在完全符合項(聲明:CN_.mydomain.com,實際證書:CN_server.mydomain.com)

  - 未知的憑證對應的找不到私密金鑰,通常附帶錯誤程式碼: 
    ```C++ 
    0x8009030d  -2146893043 SEC_E_UNKNOWN_CREDENTIALS
    0x8009030e  -2146893042 SEC_E_NO_CREDENTIALS
    ```
    要進行補救,檢查私鑰的存在;驗證 SFAdmins 被授予對私鑰的「讀取」訪問許可權。

  - 錯誤提供程式類型 - 指示加密新一代 (CNG) 證書(「微軟軟體密鑰存儲提供者」);此時,服務交換矩陣僅支援 CAPI1 證書。 通常附帶錯誤代碼:
    ```C++
    0x80090014  -2146893804 NTE_BAD_PROV_TYPE
    ```
    若要進行補救,請使用 CAPI1(例如"Microsoft 增強 RSA 和 AES 加密提供者")提供程式重新創建群集證書。 有關加密提供者的詳細資訊,請參閱[瞭解加密提供者](https://docs.microsoft.com/windows/win32/seccertenroll/understanding-cryptographic-providers)

