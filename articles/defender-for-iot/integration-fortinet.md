---
title: 關於 Fortinet 整合
titleSuffix: Azure Defender for IoT
description: 適用于 IoT 和 Fortinet 的 Defender 已建立技術合作關係，以偵測及停止 IoT 和 ICS 網路的攻擊。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/17/2021
ms.topic: article
ms.service: azure
ms.openlocfilehash: 313db90d4c9be30ef588b00caf1d6e4ce32b113b
ms.sourcegitcommit: 6628bce68a5a99f451417a115be4b21d49878bb2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/18/2021
ms.locfileid: "98557577"
---
# <a name="defender-for-iot-and-fortinet-iiot-and-ics-threat-detection--prevention"></a>Defender for IoT 和 Fortinet IIoT 和 ICS 威脅偵測 & 防護

適用于 IoT 的 Defender 可透過已獲專利、可感知 ICS 的自我學習引擎，在不到一小時內提供有關 ICS 裝置、弱點和威脅的立即深入解析，而不需要依賴代理程式、規則或簽章、特製化技能或事先知道環境。

適用于 IoT 和 Fortinet 的 Defender 已建立技術合作關係，以偵測及停止 IoT 和 ICS 網路的攻擊。

適用于 IoT 的 Fortinet 和 Defender 會一起防止：

- 可程式化邏輯控制器的未經授權變更。

- 透過其原生通訊協定操作 ICS 和 IoT 裝置的惡意程式碼。
- 收集資料的偵察工具。
- 因錯誤或惡意攻擊者造成的通訊協定違規。

## <a name="defender-for-iot-and-fortigate-joint-solution"></a>適用于 IoT 和 FortiGate 聯合解決方案的 Defender

Defender for IoT 會偵測 IoT 和 ICS 網路中的異常行為，並將該資訊傳遞至 FortiGate 和 FortiSIEM，如下所示：

- **可見度：** Defender 針對 IoT 提供的資訊可讓 FortiSIEM 系統管理員看到先前不可見的 IoT 和 ICS 網路。

- **封鎖惡意攻擊：** FortiGate 系統管理員可以使用 Defender for IoT 所探索到的資訊來建立規則，以停止異常行為，不論該行為是由混亂動作專案造成，還是裝置設定錯誤，在導致生產、收益或人員受損之前。

## <a name="the-defender-for-iot-platform"></a>適用于 IoT 的 Defender 平臺

Defender for IoT 平臺會 autodiscovers 並為任何非受控 IoT 和 ICS 裝置提供指紋，同時持續監視目標攻擊和惡意程式碼。 風險和弱點管理功能包括自動化的威脅分析模型，以及有關端點和網路弱點的完整報告，以及風險優先的風險降低建議。  

適用于 IoT 的 Defender 平臺是無代理程式、非侵入式且易於部署，可在連線到網路後的一小時內提供可操作的見解。

## <a name="fortinet-fortisiem"></a>Fortinet FortiSIEM

有效的安全性需要所有裝置、所有基礎結構的可見度，以及內容。 什麼是代表威脅的裝置，您可以使用哪些功能來管理企業所面臨的威脅，而不是多個安全性工具所建立的雜訊。

端點、IoT、基礎結構、安全性工具、應用程式、VM 和雲端是系統管理員需要保護的一些專案，並且會不斷地監視。

FortiSIEM 和 Fortinet 的多廠商安全性事件，以及事件管理解決方案，可在單一、可調整的解決方案中整合、可見度、相互關聯、自動回應及補救。

使用商務服務觀點，管理網路和安全性作業的複雜度會降低、釋出資源、改善缺口偵測。 FortiSIEM 可在套用機器學習時提供交叉相互關聯，而 UEBA 則可改善回應，以在發生缺口之前停止缺口。

## <a name="fortinet-fortigate-next-generation-firewalls"></a>Fortinet FortiGate 下一代防火牆

FortiGate 的下一代防火牆 (Ngfw) 利用 AI 技術 FortiGuard labs 的特殊用途安全性處理器和威脅情報安全性服務，提供最高評等的保護、高效能的清楚簡訊傳送和加密流量檢查。

新一代防火牆可降低成本和複雜度，並完全掌握應用程式、使用者和網路，並提供最佳的安全性。 這是 Fortinet Security Fabric 新一代防火牆不可或缺的一部分，可在 Fortinet 的全方位安全性組合和合作夥伴安全性解決方案中，于多廠商環境內進行通訊，以共用威脅情報並改善安全性狀態。

## <a name="use-cases"></a>使用案例

### <a name="prevent-unauthorized-changes-to-programmable-logic-controllers"></a>防止未獲授權的程式化邏輯控制器變更

組織會使用 (Plc) 的可程式化邏輯控制器來管理實體進程，例如工廠中的機器人、在伺服器陣列中旋轉渦輪機，以及 centrifuges 的實體電源工廠。

針對 PLC 的階梯邏輯或固件的更新，可能代表合法的活動，或是藉由插入惡意程式碼來嘗試危害裝置。  適用于 IoT 的 Defender 可以偵測到 Plc 的未經授權變更，然後將該變更的相關資訊傳遞給 FortiSIEM 和 FortiGate。 FortSIEM 系統管理員可以利用這些資訊來決定如何最好地緩和解決方案。 其中一個緩和選項是在 FortiGate 中建立規則，以停止對受影響裝置的進一步通訊。

### <a name="stop-ransomware-before-it-damages-iot-and-ics-networks"></a>在軟體損毀 IoT 和 ICS 網路之前停止勒索軟體

適用于 IoT 的 Defender 會持續監視 IoT 和 ICS 網路，以瞭解勒索軟體所造成的行為，例如 LockerGoga、WannaCry 和 NotPetya。 與 FortiSIEM 和 FortiGate 整合時，適用于 IoT 的 Defender 可以傳遞這些類型勒索軟體的相關資訊，讓 ForiSIEM 操作員可以看到惡意程式碼的位置，而 FortiGate 系統管理員可以阻止勒索軟體散佈和肆意更多破壞。

## <a name="set-sending-defender-for-iot-alerts-to-fortisiem"></a>設定將 Defender for IoT 警示傳送至 FortiSIEM

IoT 警示的防禦者提供各種安全性事件的相關資訊，包括：

- 與已學習的基準網路活動偏差
- 惡意程式碼偵測
- 根據可疑操作變更的偵測
- 網路異常
- 通訊協定與通訊協定規格的偏差

:::image type="content" source="media/integration-fortinet/address-scan.png" alt-text="偵測到位址掃描視窗的螢幕擷取畫面。":::

您可以設定 Defender for IoT 將警示傳送至 FortiSIEM 伺服器，警示資訊會顯示在 [分析] 視窗中：

:::image type="content" source="media/integration-fortinet/analytics.png" alt-text="分析視窗的螢幕擷取畫面。":::

系統會剖析每個 Defender for IoT 警示，而不會在 FortiSIEM 端進行任何其他設定，並在 FortiSIEM 中顯示為安全性事件。 預設會顯示下列事件詳細資料：

:::image type="content" source="media/integration-fortinet/event-detail.png" alt-text="在 [事件詳細資料] 視窗中，查看您的事件詳細資料。":::

## <a name="define-alert-forwarding-rules"></a>定義警示轉送規則

使用 Defender 作為 IoT 的轉送規則，將警示資訊傳送至 FortiSIEM。

您可以根據下列選項自訂警示規則：

- 偵測到特定的通訊協定。

- 事件的嚴重性。

- 偵測事件的 Defender for IoT 引擎。

若要建立轉送規則

1. 從 [感應器] 或 [內部部署管理主控台] 的左窗格中，選取 [ **轉送**]。

    [:::image type="content" source="media/integration-fortinet/forwarding-view.png" alt-text="在 [轉送] 視窗中，查看您的轉送規則。":::](media/integration-fortinet/forwarding-view.png#lightbox)

2. 選取 [ **建立轉送規則**]。 在 [ **建立轉送規則** ] 視窗中，定義規則的參數。

    :::image type="content" source="media/integration-fortinet/new-rule.png" alt-text="建立新的轉送規則視窗。":::

    | 參數 | 描述 |
    |--|--|
    | **名稱** | 轉送規則名稱。 |
    | **選取嚴重性** | 要轉寄的最小安全性層級事件。 例如，如果選取 [ **次要** ]，將會轉送 [次要警示] 和高於此嚴重性層級的任何警示。 |
    | **通訊協定** | 預設會選取所有的通訊協定。<br><br>若要選取特定的通訊協定，請選取 [ **特定** ]，然後選取要套用此規則的通訊協定。 |
    | **引擎** | 依預設，會牽涉到所有的安全性引擎。<br><br>若要選取套用此規則的特定安全性引擎，請選取 [ **特定** ]，然後選取引擎。 |
    | **系統通知** | 轉寄感應器上線/離線狀態。 只有當您已登入內部部署管理主控台時，才能使用此選項。 |

3. 若要指示 Defender 讓 IoT 傳送、將警示資訊傳送至 FortiSIEM，請選取 [ **動作** ]，然後選取 [ **傳送至 FortiSIEM**]。

    :::image type="content" source="media/integration-fortinet/forward-rule.png" alt-text="建立轉送規則，然後選取 [傳送至 Fortinet]。":::

4. 輸入 FortiSIEM 伺服器詳細資料。

    :::image type="content" source="media/integration-fortinet/details.png" alt-text="將 FortiSIEm 詳細資料新增至轉送規則":::

    | 參數 | 描述 |
    | --------- | ----------- |
    | **主控件** | FortiSIEM 伺服器位址。 |
    | **通訊埠** | FortiSIEM 伺服器埠。 |
    | **時區** | 警示偵測的時間戳記。 |

5. 選取 [提交]  。

## <a name="set-blocking-suspected-traffic-using-fortigate-firewall"></a>使用 Fortigate 防火牆設定封鎖可疑的流量

您可以根據適用于 IoT 的 Defender 警示，在 FortiGate 防火牆中自動設定封鎖原則。

:::image type="content" source="media/integration-fortinet/firewall.png" alt-text="FortiGate 防火牆視窗視圖的視圖。":::

例如，下列警示可能會封鎖惡意來源：

:::image type="content" source="media/integration-fortinet/suspicion.png" alt-text="NotPetya 惡意程式碼可疑的視窗":::

若要設定封鎖此惡意來源的 FortiGate 防火牆規則：

1. 在 FortiGate 中，建立 Defender for IoT 轉送規則所需的 API 金鑰。 如需詳細資訊，請參閱 [在 FortiGate 中建立 API 金鑰](#create-an-api-key-in-fortigate)。

1. 在 Defender for IoT **轉送** 中，設定會封鎖惡意程式碼相關警示的轉送規則。 如需詳細資訊，請參閱 [使用 FortiGate 防火牆封鎖疑似的流量](#block-suspected-traffic-using-the-fortigate-firewall)。

1. 在適用于 IoT 的 Defender 中， **警示** 會封鎖惡意來源。 如需詳細資訊，請參閱 [封鎖可疑來源](#block-the-suspicious-source)。

    惡意來源位址會出現在 [FortiGage **管理員** ] 視窗中。

   :::image type="content" source="media/integration-fortinet/administrator.png" alt-text="FortiGate 系統管理員視窗視圖。":::

   封鎖原則會自動建立，並出現在 [FortiGate **IPv4 原則** ] 視窗中。

   :::image type="content" source="media/integration-fortinet/policy.png" alt-text="[FortiGate IPv4 原則] 視窗視圖。":::

   :::image type="content" source="media/integration-fortinet/edit.png" alt-text="FortiGate IPv4 原則編輯檢視。":::

## <a name="create-an-api-key-in-fortigate"></a>在 FortiGate 中建立 API 金鑰

1. 在 FortiGate 中，選取 [**系統**  >  **管理設定檔**]。

1. 建立具有下列許可權的設定檔：

    :::image type="content" source="media/integration-fortinet/admin-profile.png" alt-text="自動產生電腦描述":::

1. 選取 [**系統**  >  **管理員**] 並建立新的 **REST API** 系統管理員。

    :::image type="content" source="media/integration-fortinet/cellphone.png" alt-text="自動產生行動電話描述":::

    | 參數 | 描述 |
    | --------- | ----------- |
    | **使用者名稱** | 轉送規則名稱。 |
    | **註解** | 要轉寄的最基本安全性層級事件。 例如，如果選取 [次要]，將會轉送 [次要警示] 和高於此嚴重性層級的任何警示。 |
    | **系統管理員設定檔** | 從下拉式清單中，選取您在上一個步驟中定義的設定檔名稱。 |
    | **PKI 群組** | 停用。 |
    | **CORS 允許來源** | 啟用。 |
    | **限制登入受信任的主機** | 新增將連接至 FortiGate 之感應器和 CMs 的 IP 位址。 |

1. 儲存 API 金鑰。

    :::image type="content" source="media/integration-fortinet/api-key.png" alt-text="行動電話描述會自動產生新的 API 金鑰":::

## <a name="block-suspected-traffic-using-the-fortigate-firewall"></a>使用 FortiGate 防火牆封鎖疑似的流量

1. 在左窗格中，選取 [ **轉送**]。

    [:::image type="content" source="media/integration-fortinet/forwarding-view.png" alt-text="感應器中的 [轉送視窗] 選項。":::](media/integration-fortinet/forwarding-view.png#lightbox)

1. 選取 [ **建立轉送規則** ] 並定義規則參數。

    :::image type="content" source="media/integration-fortinet/new-rule.png" alt-text="[建立轉送規則] 視窗的螢幕擷取畫面":::

    | 參數 | 描述 |
    | --------- | ----------- |
    | **名稱** | 轉送規則名稱。 |
    | **選取嚴重性** | 要轉寄的最基本安全性層級事件。 例如，如果選取 [ **次要** ]，將會轉送 [次要警示] 和高於此嚴重性層級的任何警示。 |
    | **通訊協定** | 預設會選取所有的通訊協定。<br><br>若要選取特定的通訊協定，請選取 [ **特定** ]，然後選取要套用此規則的通訊協定。 |
    | **引擎** | 依預設，會牽涉到所有的安全性引擎。<br><br>若要選取套用此規則的特定安全性引擎，請選取 [ **特定** ]，然後選取引擎。 |
    | **系統通知** | 轉寄感應器 *連線和離線* 狀態。 只有當您已登入內部部署管理主控台時，才能使用此選項。 |

1. 若要指示 Defender 讓 IoT 將防火牆規則傳送至 FortiGate，請選取 [ **動作** ]，然後選取 [ **傳送至 FortiGate**]。

    :::image type="content" source="media/integration-fortinet/fortigate.png" alt-text="[建立轉送規則] 視窗並選取 [傳送至 FortiGate":::

1. 若要設定 FortiGate 轉送規則：

    :::image type="content" source="media/integration-fortinet/configure.png" alt-text="設定建立轉送規則視窗":::

1. 在 [ **動作** ] 窗格中，設定下列參數：

    | 參數 | 描述 |
    |--|--|
    | 主機 | FortiGate 伺服器 IP 位址類型。 |
    | 連接埠 | FortiGate 伺服器埠類型。 |
    | 使用者名稱 | FortiGate 伺服器使用者名稱類型。 |
    | API 金鑰 | 輸入您在 FortiGate 中建立的 API 金鑰。 |
    | 傳入介面| 定義如何執行封鎖：<br /><br />**依 Ip 位址**：一律會根據 ip 位址在全景上建立封鎖原則。<br /><br />**依 fqdn 或 Ip 位址**：根據 Fqdn 在全景上建立封鎖原則（如果有的話），否則為 ip 位址。| 
    | 輸出介面 |設定原則通知電子郵件的電子郵件地址。 <br /><br /> **注意**：請確定您已在 XSense 中設定郵件伺服器。 如果未輸入任何電子郵件地址，XSense 就不會傳送通知電子郵件。| 
    |設定| 設定下列選項，以允許 FortiGate 防火牆封鎖可疑來源： <br /><br />**封鎖不合法** 的函式代碼：通訊協定違規-不合法的域值違反 ICS 通訊協定規格 (潛在的惡意探索) <br /><br />**封鎖未經授權的 plc 程式設計/固件更新**：未經授權的 plc 變更<br /><br />**封鎖未授權的 PLC/停止**： plc 停止 (停機) <br /><br />**封鎖惡意程式碼相關警示**：封鎖產業惡意程式碼嘗試 (TRITON、NotPetya 等 ) 。 您可以選取 **自動封鎖** 的選項。 在此情況下，封鎖會自動且立即執行。<br /><br />*封鎖未經授權的掃描*：未經授權的掃描 (可能的偵察) 

1. 選取 [提交]  。

## <a name="block-the-suspicious-source"></a>封鎖可疑來源

1. 在 [ **警示** ] 窗格中，選取與 Fortinet 整合相關的警示。

    :::image type="content" source="media/integration-fortinet/unauthorized.png" alt-text="未經授權的 PLC 程式設計視窗":::

1. 若要自動封鎖可疑來源，請選取 [ **區塊來源**]。

    :::image type="content" source="media/integration-fortinet/confirm.png" alt-text="確認視窗。":::

1. 在 [ **請確認** ] 對話方塊中，選取 **[確定**]。

## <a name="next-steps"></a>後續步驟

瞭解如何 [轉寄警示資訊](how-to-forward-alert-information-to-partners.md)。
