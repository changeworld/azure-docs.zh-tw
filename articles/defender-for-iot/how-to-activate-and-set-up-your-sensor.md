---
title: 啟用和設定感應器
description: 本文說明如何登入並啟動感應器主控台。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/12/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 908460bd0a034e21524b6ea6d3042f362cc810d4
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98623584"
---
# <a name="activate-and-set-up-your-sensor"></a>啟用和設定感應器

本文說明如何啟用感應器並執行初始設定。

系統管理員使用者第一次登入時，以及需要啟用管理時，會執行啟用。 安裝程式可確保感應器已設定為以最佳方式偵測和警示。

安全性分析師和唯讀使用者無法啟動感應器或產生新的密碼。

## <a name="sign-in-and-activation-for-administrator-users"></a>系統管理員使用者的登入和啟用

第一次登入的系統管理員應該確認他們可以存取在感應器上線期間下載的啟用和密碼修復檔案。 如果沒有，則需要 Azure 安全性系統管理員、訂用帳戶參與者或訂用帳戶擁有者許可權，才能在適用于 IoT 的 Azure Defender 入口網站上產生這些檔案。

### <a name="first-time-sign-in-and-activation-checklist"></a>首次登入和啟用檢查清單

在登入感應器主控台之前，系統管理員使用者必須有下列許可權：

- 安裝期間所定義的感應器 IP 位址。

- 感應器的使用者登入認證。 如果您下載了感應器的 ISO，請使用您在安裝期間收到的預設認證。 建議您在啟用之後建立新的 *系統管理員* 使用者。

- 初始密碼。 如果您是從箭號購買預先設定的感應器，您需要在第一次登入時產生密碼。

- 與此感應器相關聯的啟用檔。 在 IoT 入口網站的 Defender 上架感應器時，會產生並下載該檔案。

- 您公司所需的 SSL/TLS CA 簽署憑證。

### <a name="about-activation-files"></a>關於啟用檔案

您的感應器已在特定管理模式中上線至適用于 IoT 的 Azure Defender：

| 模式類型 | 描述 |
|--|--|
| **雲端連線模式** | 感應器偵測的資訊會顯示在感應器主控台中。 警示資訊也會透過 IoT 中樞傳遞，並可與其他 Azure 服務共用，例如 Azure Sentinel。 |
| **本機連線模式** | 感應器偵測的資訊會顯示在感應器主控台中。 如果感應器已連線，偵測資訊也會與內部部署管理主控台共用。 |

在上線期間，會產生並下載此感應器的本機連線或已連線到雲端的啟用檔。 啟用檔案包含感應器管理模式的指示。 *必須將唯一的啟用檔案上傳至您所部署的每個感應器。*  當您第一次登入時，必須上傳此感應器的相關啟用檔案。

:::image type="content" source="media/how-to-activate-and-set-up-your-sensor/azure-defender-for-iot-activation-file-download-button.png" alt-text="適用于 IoT 的 Azure Defender 入口網站，上架感應器。":::

### <a name="about-certificates"></a>關於憑證

在感應器安裝之後，會產生本機自我簽署憑證，並使用此憑證來存取感應器主控台。 當系統管理員第一次登入主控台時，系統會提示該使用者將 SSL/TLS 憑證上架。

有兩種安全性層級可用：

- 上傳 CA 簽署的憑證，以符合您組織所要求的特定憑證和加密需求。
- 允許在管理主控台和連線感應器之間進行驗證。 驗證會針對憑證撤銷清單和憑證到期日進行評估。 *如果驗證失敗，管理主控台和感應器之間的通訊會停止，並在主控台中顯示驗證錯誤。* 安裝之後，預設會啟用此選項。  

主控台支援下列憑證類型：

- 私用和企業金鑰基礎結構 (私用 PKI) 

- 公開金鑰基礎結構 (公開 PKI) 

- 在本機產生的設備 (本機自我簽署)  

  > [!IMPORTANT]
  > 我們建議您不要使用預設的自我簽署憑證。 憑證並非安全的，且只能用於測試環境。 憑證的擁有者無法驗證，而且無法維護系統的安全性。 請勿將此選項用於生產網路。

### <a name="sign-in-and-activate-the-sensor"></a>登入並啟動感應器

若要登入並啟動：

1. 使用安裝期間所定義的 IP，從瀏覽器移至感應器主控台。 [登入] 對話方塊隨即開啟。

    :::image type="content" source="media/how-to-activate-and-set-up-your-sensor/azure-defender-for-iot-sensor-log-in-screen.png" alt-text="適用于 IoT 的 Azure Defender 感應器。":::

1. 輸入在感應器安裝期間定義的認證。 如果您已從箭號購買預先設定的感應器，請先產生密碼。 如需有關密碼復原的詳細資訊，請參閱 [在初始登入時調查密碼失敗](how-to-troubleshoot-the-sensor-and-on-premises-management-console.md#investigate-password-failure-at-initial-sign-in)。

1. 登入之後，[ **啟用** ] 對話方塊隨即開啟。 選取 **[上傳** ]，然後移至您在感應器上線時下載的啟用檔案。

   :::image type="content" source="media/how-to-activate-and-set-up-your-sensor/activation-upload-screen-with-upload-button.png" alt-text="選取 [上傳] 並移至啟用檔。":::

1. 如果您想要在啟用之前變更感應器網路設定，請選取 [ **感應器網路** 設定] 連結。 請參閱 [啟用之前更新感應器網路](#update-sensor-network-configuration-before-activation)設定。

1. 接受條款及條件。

1. 選取 [啟用]  。 [SSL/TLS 憑證] 對話方塊隨即開啟。

1. 定義憑證名稱。
1. 上傳 CRT 和金鑰檔。
1. 輸入複雜密碼，然後視需要上傳 PEM 檔案。
1. 選取 [下一步]  。 [驗證] 畫面隨即開啟。 依預設，會啟用管理主控台和連線感應器之間的驗證。
1. 關閉 [ **啟用全系統驗證** ] 切換，以停用驗證。 建議您啟用驗證。
1. 選取 [儲存]。  

上傳 CA 簽署的憑證之後，您可能需要重新整理畫面。

如需上傳新憑證、支援的憑證參數，以及使用 CLI 憑證命令的詳細資訊，請參閱 [管理個別感應器](how-to-manage-individual-sensors.md)。

#### <a name="update-sensor-network-configuration-before-activation"></a>在啟用前更新感應器網路設定  

感應器網路設定參數是在軟體安裝期間定義，或在您購買預先設定的感應器時定義。 所定義的參數如下：

- IP 位址
- DNS
- 預設閘道
- 子網路遮罩
- 主機名稱

您可能會想要在啟用感應器之前更新此資訊。 例如，您可能需要變更以箭號定義的預先設定參數。 您也可以在啟用感應器之前定義 proxy 設定。

若要更新感應器網路設定參數：

1. 選取 [**啟用**] 對話方塊中的 [**感應器網路** 設定] 連結。

   :::image type="content" source="media/how-to-activate-and-set-up-your-sensor/editable-network-configuration-screen-v2.png" alt-text="感應器網路設定。":::

2. 隨即會顯示安裝期間所定義的參數。 也可以使用定義 proxy 的選項。 視需要更新任何設定，然後選取 [ **儲存**]。

### <a name="subsequent-sign-ins"></a>後續登入

第一次啟用之後，Azure Defender for IoT 感應器主控台會在登入之後開啟，而不需要啟用檔案。 您只需要登入認證。

登入之後，Azure Defender for IoT 主控台隨即開啟。

:::image type="content" source="media/how-to-activate-and-set-up-your-sensor/azure-defender-for-iot-log-in-screen-dashboard-v2.png" alt-text="適用于 IoT 的 Azure Defender 主控台。":::

## <a name="initial-setup-and-learning-for-administrators"></a>系統管理員) 的初始安裝和學習 (

在您第一次登入之後，適用于 IoT 的 Azure Defender 感應器會開始自動監視您的網路。 網路裝置將會出現在裝置對應和裝置清查區段中。 適用于 IoT 的 Azure Defender 將開始偵測並警示您在網路中發生的所有安全性和操作事件。 然後，您可以根據偵測到的資訊來建立報表和查詢。

此活動一開始會在學習模式中執行，這會指示您的感應器瞭解您網路的一般活動。 例如，感應器會學習網路中探索到的裝置、在網路中偵測到的通訊協定，以及在特定裝置之間進行的檔案傳輸。 此活動會成為您網路的基準活動。

### <a name="review-and-update-basic-system-settings"></a>檢查和更新基本系統設定

請檢查感應器的系統設定，以確定感應器已設定為以最佳方式偵測和警示。

定義感應器的系統設定。 例如：

- 定義 ICS (或 IoT) 和隔離的子網。

- 定義適用于網站專屬通訊協定的埠別名。

- 定義使用中的 Vlan 和名稱。

- 如果 DHCP 正在使用中，請定義合法的 DHCP 範圍。

- 定義與 Active Directory 和郵件伺服器的整合。

### <a name="disable-learning-mode"></a>停用學習模式

調整系統設定之後，您可以讓適用于 IoT 的 Azure Defender 感應器在學習模式中執行，直到您覺得系統偵測精確地反映您的網路活動為止。

根據您的網路大小和複雜度而定，學習模式應該會執行約2到6周。 停用學習模式之後，任何與您的基準活動不同的活動都會觸發警示。

若要停用學習模式：

- 選取 [ **系統設定** ]，並關閉 **學習** 選項。

## <a name="first-time-sign-in-for-security-analysts-and-read-only-users"></a>安全性分析師和唯讀使用者的第一次登入

登入之前，請確認您有：

- 感應器 IP 位址。
- 您系統管理員提供的登入認證。

## <a name="console-tools-overview"></a>主控台工具：總覽

您可以從側邊功能表存取主控台工具。

**導覽** 

| 時間範圍 | 圖示 | 描述 |
| -----------|--|--|
| 儀表板 | :::image type="icon" source="media/concept-sensor-console-overview/dashboard-icon-azure.png" border="false"::: | 查看網路安全性狀態的直覺快照。 |
| 裝置對應 | :::image type="icon" source="media/concept-sensor-console-overview/asset-map-icon-azure.png" border="false"::: | 查看地圖中的網路裝置、裝置連線和裝置屬性。 您可以使用各種縮放、反白顯示和篩選選項來顯示您的網路。 |
| 裝置清查 | :::image type="icon" source="media/concept-sensor-console-overview/asset-inventory-icon-azure.png" border="false":::  | 裝置清查會顯示此感應器偵測到的廣泛裝置屬性範圍。 您可以使用下列選項： <br /> -根據資料表欄位篩選資訊，並查看顯示的篩選資訊。 <br /> -將資訊匯出至 CSV 檔案。 <br /> -匯入 Windows 登錄詳細資料。|
| 警示 | :::image type="icon" source="media/concept-sensor-console-overview/alerts-icon-azure.png" border="false"::: | 當發生原則違規、發生與基準行為的偏差，或偵測到網路中的任何可疑活動類型時，就會顯示警示。 |
| 報表 | :::image type="icon" source="media/concept-sensor-console-overview/reports-icon-azure.png" border="false"::: | 根據資料採礦查詢來查看報表。 |

**分析**

| 時間範圍| 圖示 | 描述 |
|---|---|---|
| 事件時間軸 | :::image type="icon" source="media/concept-sensor-console-overview/event-timeline-icon-azure.png" border="false"::: | 使用警示、網路事件 (資訊) 和使用者作業的相關資訊（例如使用者登入和使用者刪除）來查看時間軸。|

**導覽**

| 時間範圍 | 圖示 | 描述 |
|---|---|---|
| 資料採礦 | :::image type="icon" source="media/concept-sensor-console-overview/data-mining-icon-azure.png" border="false"::: | 在各種層級產生網路裝置的完整和細微資訊。 |
| 趨勢和統計資料 | :::image type="icon" source="media/concept-sensor-console-overview/trends-and-statistics-icon-azure.jpg" border="false"::: | 查看多個小工具範圍中的趨勢和統計資料。 |
| 風險評估 | :::image type="icon" source="media/concept-sensor-console-overview/vulnerabilities-icon-azure.png" border="false"::: | 顯示 [ **弱點** ] 視窗。 |

**管理員**

| 時間範圍 | 圖示 | 描述 |
|---|---|---|
| 使用者 | :::image type="icon" source="media/concept-sensor-console-overview/users-icon-azure.png" border="false"::: | 使用各種存取層級來定義使用者和角色。 |
| 轉發 | :::image type="icon" source="media/concept-sensor-console-overview/forwarding-icon-azure.png" border="false"::: | 將警示資訊轉寄給與適用于 IoT 的 Defender、電子郵件地址、webhook 伺服器等整合的合作夥伴。 <br /> 如需詳細資料，請參閱 [轉寄警示資訊](how-to-forward-alert-information-to-partners.md) 。 |
| 系統設定 | :::image type="icon" source="media/concept-sensor-console-overview/system-settings-icon-azure.png" border="false"::: | 設定系統設定。 例如，定義 DHCP 設定、提供郵件伺服器詳細資料，或建立埠別名。 |
| 匯入設定 | :::image type="icon" source="media/concept-sensor-console-overview/import-settings-icon-azure.png" border="false"::: | 顯示 [匯 **入設定** ] 視窗。 您可以在裝置的資訊中執行手動變更。<br /> 如需詳細資料，請參閱匯 [入裝置資訊](how-to-import-device-information.md) 。 |

**支援**

| 時間範圍| 圖示 | 描述 |
|----|---|---|
| 支援 | :::image type="icon" source="media/concept-sensor-console-overview/support-icon-azure.png" border="false"::: | 請聯絡 [Microsoft 支援服務](https://support.microsoft.com/) 尋求協助。 |

### <a name="see-also"></a>另請參閱

[將感應器上線](getting-started.md#4-onboard-a-sensor)

[管理感應器啟用檔案](how-to-manage-individual-sensors.md#manage-sensor-activation-files)

[控制監視到的流量](how-to-control-what-traffic-is-monitored.md)
