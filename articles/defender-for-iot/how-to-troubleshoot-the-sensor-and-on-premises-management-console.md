---
title: 對感應器和內部部署管理主控台進行疑難排解
description: 疑難排解您的感應器和內部部署管理主控台，以消除您可能遇到的任何問題。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/3/2021
ms.topic: article
ms.service: azure
ms.openlocfilehash: b91827fc0a6fb8380c9f8aa87a3def3bc1819523
ms.sourcegitcommit: 19ffdad48bc4caca8f93c3b067d1cf29234fef47
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97955428"
---
# <a name="troubleshoot-the-sensor-and-on-premises-management-console"></a>對感應器和內部部署管理主控台進行疑難排解

本文說明感應器和內部部署管理主控台的基本疑難排解工具。 除了此處所述的專案之外，您還可以透過下列方式檢查系統的健康情況：

**警示**：當監視流量的感應器介面關閉時，就會建立警示。 

**Snmp**：透過 snmp 監視感應器健康情況。 適用于 IoT 的 Azure Defender 會回應從授權的監視伺服器傳送的 SNMP 查詢。 

**系統通知**：當管理主控台控制感應器時，您可以轉寄有關失敗的感應器備份和已中斷連線感應器的警示。

## <a name="sensor-troubleshooting-tools"></a>感應器疑難排解工具

### <a name="investigate-password-failure-at-initial-sign-in"></a>調查初始登入時的密碼失敗

當您第一次登入預先設定的箭號感應器時，您必須執行密碼復原。

復原您的密碼：

1. 在 [Defender for IoT 登入] 畫面上，選取 [  **密碼** 復原]。 **密碼修復** 畫面隨即開啟。

1. 選取 [ **CyberX** ] 或 [ **支援**]，然後複製唯一的識別碼。

1. 流覽至 [Azure 入口網站]，然後選取 [ **網站和感應器**]。  

1. 選取 [ **復原內部部署管理主控台密碼** ] 索引標籤。

   :::image type="content" source="media/password-recovery-images/recover-button.png" alt-text="選取 [復原內部部署管理] 按鈕，以下載修復檔案。":::

1. 輸入您在 **密碼修復** 畫面上收到的唯一識別碼，然後選取 [ **復原**]。 `password_recovery.zip`下載檔案。

    > [!NOTE]
    > 請勿改變密碼修復檔。 這是已簽署的檔案，如果您使用它進行篡改，將無法運作。

1. 在 [ **密碼修復** ] 畫面上，選取 **[上傳**]。 **[上傳密碼** 復原檔案] 視窗隨即開啟。

1. 選取 **[流覽]** 以找出您的檔案 `password_recovery.zip` ，或將拖曳 `password_recovery.zip` 至視窗。

1. 選取 **[下一步]**，系統就會顯示您的管理主控台的使用者和系統產生的密碼。

    > [!NOTE]
    > 當您第一次登入感應器或內部部署管理主控台時，它會連結到您所連線的訂用帳戶。 如果您需要重設 CyberX 或支援使用者的密碼，您必須選取該訂用帳戶。 如需復原 CyberX 或支援使用者密碼的詳細資訊，請參閱為 [感應器或內部部署管理主控台重設使用者的密碼](how-to-create-and-manage-users.md#resetting-a-users-password-for-the-sensor-or-on-premises-management-console)

### <a name="investigate-a-lack-of-traffic"></a>調查缺少的流量

當感應器辨識出其中一個設定的埠上沒有流量時，會在主控台的頂端顯示一個指標。 所有使用者都可以看到此指標。

:::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/no-traffic-detected.png" alt-text="未偵測到流量之警示的螢幕擷取畫面。":::
 
當此訊息出現時，您可以調查沒有流量的位置。 請確定已連線到 span 纜線，而且 span 架構沒有任何變更。  

如需支援和疑難排解資訊，請聯絡 [Microsoft 支援服務](https://support.serviceshub.microsoft.com/supportforbusiness/create?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099)。

### <a name="check-system-performance"></a>檢查系統效能 

當部署新感應器或（例如，感應器正在緩慢運作或未顯示任何警示）時，您可以檢查系統效能。

若要檢查系統效能：

1. 在儀表板中，確定已完成 `PPS > 0` 。

   :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/dashboard-view-v2.png" alt-text="範例儀表板的螢幕擷取畫面。"::: 

1. 從側邊功能表選取 [ **裝置**]。

1. 在 [ **裝置** ] 視窗中，確認正在探索裝置。

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/discovered-devices.png" alt-text="確定已探索裝置。":::

1. 從側邊功能表選取 [ **資料採礦**]。

1. 在 [ **資料採礦** ] 視窗中，選取 [ **全部** ] 並產生報表。

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/new-report-generated.png" alt-text="使用資料採礦產生新的報表。":::

1. 請確定報表包含資料。

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/new-report-generated.png" alt-text="確定報表包含資料。":::

1. 從側邊功能表選取 [ **趨勢] & 統計資料**。

1. 在 [ **趨勢 & 統計資料** ] 視窗中，選取 [ **新增 Widget**]。

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/add-widget.png" alt-text="選取 widget 以新增 widget。":::

1. 新增 widget 並確定其顯示資料。

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/widget-data.png" alt-text="確定 widget 顯示資料。":::

1. 從側邊功能表選取 [ **警示**]。 [ **警示** ] 視窗隨即出現。

1. 請確定已建立警示。

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/alerts-created.png" alt-text="確定已建立警示。":::


### <a name="investigate-a-lack-of-expected-alerts"></a>調查缺少預期的警示

如果 [ **警示** ] 視窗未顯示您預期的警示，請確認下列事項：

- 檢查 **警示** 視窗中是否已顯示相同的警示，作為對不同安全性實例的回應。 若為 [是]，而且尚未處理此警示，則感應器主控台不會顯示新的警示。

- 請確定您未使用管理主控台中的 **警示排除** 規則來排除此警示。 

### <a name="investigate-widgets-that-show-no-data"></a>調查未顯示任何資料的小工具

當 [ **趨勢] & [統計** 資料] 視窗中的 widget 未顯示任何資料時，請執行下列動作：

- [檢查系統效能](#check-system-performance)。

- 請確定已正確設定時間和區域設定，且未設定為未來時間。 

### <a name="investigate-a-device-map-that-shows-only-broadcasting-devices"></a>調查只顯示廣播裝置的裝置地圖

當地圖上顯示的裝置未彼此連線時，範圍埠設定可能會發生錯誤。 也就是說，您可能只會看到廣播裝置，而且沒有單播流量。

:::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/broadcasting-devices.png" alt-text="查看您的廣播裝置。":::

在這種情況下，您必須驗證您只能看到廣播流量。 然後，要求網路工程師修正 SPAN 埠設定，讓您可以看到單播流量。

若要驗證您只看到廣播流量：

- 在 [ **資料採礦** ] 畫面上，使用 [ **全部** ] 選項來建立報表。 然後，查看是否只 (廣播和多播流量，且不會) 出現在報表中的單播流量。

或：

- 直接從交換器錄製 PCAP，或使用 Wireshark 來連接膝上型電腦。

### <a name="connect-the-sensor-to-ntp"></a>將感應器連線到 NTP

您可以設定獨立感應器和管理主控台（與其相關的感應器），以連接到 NTP。

若要將獨立感應器連接至 NTP：

- [請洽詢支援小組以取得協助](https://support.microsoft.com/en-us/supportforbusiness/productselection?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099)。

若要將管理主控台所控制的感應器連線到 NTP：

- 與 NTP 的連線是在管理主控台上設定的。 管理主控台控制的所有感應器都會自動取得 NTP 連接。

### <a name="investigate-when-devices-arent-shown-on-the-map-or-you-have-multiple-internet-related-alerts"></a>調查裝置未顯示在地圖上的時間，或您有多個網際網路相關警示

有時 ICS 裝置會以外部 IP 位址設定。 這些 ICS 裝置不會顯示在地圖上。 網際網路雲端會出現在地圖上，而不是裝置。 這些裝置的 IP 位址包含在雲端映射中。

另一個指出相同問題的原因是出現多個與網際網路相關的警示。

:::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/alert-problems.png" alt-text="多個與網際網路相關的警示。":::

若要修正設定：

1. 以滑鼠右鍵按一下裝置對應上的雲端圖示，然後選取 [ **匯出 IP 位址**]。 複製私用的公用範圍，並將其新增至子網清單。 如需詳細資訊，請參閱 [設定子網](how-to-control-what-traffic-is-monitored.md#configure-subnets)。

1. 針對網際網路連接產生新的資料採礦報告。

1. 在資料採礦報表中，選取 :::image type="icon" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/administrator-mode.png" border="false"::: 以進入系統管理員模式，並刪除您 ICS 裝置的 IP 位址。

### <a name="tweak-the-sensors-quality-of-service"></a>調整感應器的服務品質

若要儲存您的網路資源，您可以限制感應器針對日常程式所使用的介面頻寬。

若要限制介面頻寬，請使用 `cyberx-xsense-limit-interface` 需要以 sudo 許可權執行的 CLI 工具。 此工具會取得下列引數：

  - `* -i`：介面 (範例： eth0) 。

  - `* -l`：限制 (範例： 30 kbit/1 mbit) 。 您可以使用下列頻寬單位： kbps、mbps、kbit、mbit 或 bps。

  - `* -c`：清除 (清除介面頻寬限制) 。

若要調整服務品質：

1. 以 Defender for IoT 使用者登入感應器 CLI，然後輸入 `sudo cyberx-xsense-limit-interface-I eth0 -l value` 。

   例如：`sudo cyberx-xsense-limit-interface -i eth0 -l 30kbit`

   > [!NOTE]
   > 若為實體設備，請使用 em1 介面。

1. 若要清除介面限制，請輸入 `sudo cyberx-xsense-limit-interface -i eth0 -l 1mbps -c` 。

## <a name="on-premises-management-console-troubleshooting-tools"></a>內部部署管理主控台疑難排解工具

### <a name="investigate-a-lack-of-expected-alerts"></a>調查缺少預期的警示

如果 [ **警示** ] 視窗中未顯示預期的警示，請確認下列事項：

- 檢查 **警示** 視窗中是否已顯示相同的警示，作為對不同安全性實例的回應。 若為 [是]，而且尚未處理此警示，則不會顯示新的警示。

- 使用內部部署管理主控台中的 **警示排除** 規則，確認您未排除此警示。  

### <a name="tweak-the-quality-of-service"></a>調整服務品質

若要儲存您的網路資源，您可以在設備與內部部署管理主控台之間的一項同步作業中，限制傳送給外部系統的警示數目 (例如電子郵件或 SIEM) 。

預設值為 50。 這表示在設備與內部部署管理主控台之間的一個通訊會話中，外部系統將不會有超過50的警示。 

若要限制警示數目，請使用 `notifications.max_number_to_report` 中可用的屬性 `/var/cyberx/properties/management.properties` 。 變更這個屬性之後不需要重新開機。

若要調整服務品質：

1. 以 Defender for IoT 使用者的身份登入。 

1. 確認預設值：

   ```bash
   grep \"notifications\" /var/cyberx/properties/management.properties
   ```

   會出現下列預設值：

   ```bash
   notifications.max_number_to_report=50
   notifications.max_time_to_report=10 (seconds)
   ```

1. 編輯預設設定：

   ```bash
   sudo nano /var/cyberx/properties/management.properties
   ```

1. 編輯下列各行的設定：

   ```bash
   notifications.max_number_to_report=50
   notifications.max_time_to_report=10 (seconds)
   ```

1. 儲存變更。 不需要重新啟動。

## <a name="export-information-for-troubleshooting"></a>匯出資訊以進行疑難排解

除了用來監視和分析網路的工具之外，您還可以將資訊傳送給支援小組，以進行進一步的調查。 當您匯出記錄時，感應器會在個別的文字檔中自動產生單次密碼 (OTP) （匯出記錄的唯一密碼）。 

若要匯出記錄：

1. 在左窗格中，選取 [ **系統設定**]。

1. 選取 [匯出記錄]。

    :::image type="content" source="media/how-to-export-information-for-troubleshooting/export-a-log.png" alt-text="將記錄匯出至系統支援。":::

1. 在 [ **檔案名** ] 方塊中，輸入要用於記錄匯出的檔案名。 預設值是目前的日期。

1. 若要定義您想要匯出的資料，請選取資料類別：  

    | 匯出分類 | 描述 |
    |--|--|
    | **作業系統記錄檔** | 選取此選項可取得作業系統狀態的相關資訊。 |
    | **安裝/升級記錄檔** | 選取此選項，以調查安裝和升級設定參數。 |
    | **系統健全輸出** | 選取此選項以檢查系統效能。 |
    | **剖析記錄** | 選取此選項以允許進行通訊協定剖析的 advanced 檢查。 |
    | **作業系統核心轉儲** | 選取此選項以匯出您的核心記憶體傾印。 核心記憶體傾印包含核心在此核心發生問題時所使用的所有記憶體。 傾印檔案的大小小於完整的記憶體傾印。 一般而言，傾印檔案大約是系統上的實體記憶體大小的三分之一。 |
    | **轉送記錄** | 選取此選項可調查轉送規則。 |
    | **SNMP 記錄檔** | 選取此選項可接收 SNMP 健康情況檢查資訊。 |
    | **核心應用程式記錄檔** | 選取此選項以匯出有關核心應用程式設定和操作的資料。 |
    | **與 CM 記錄通訊** | 如果與管理主控台的連線持續發生問題或中斷，請選取此選項。 |
    | **Web 應用程式記錄檔** | 選取此選項可取得從應用程式 web 介面傳送之所有要求的相關資訊。 |
    | **系統備份** | 選取此選項以匯出所有系統資料的備份，以調查系統的實際狀態。 |
    | **剖析統計資料** | 選取此選項以允許進行通訊協定統計資料的 advanced 檢查。 |
    | **資料庫記錄** | 選取此選項可從系統資料庫匯出記錄。 調查系統記錄有助於找出系統問題。 |
    | **設定** | 選取此選項以匯出所有可設定參數的相關資訊，以確定一切都已正確設定。 |

1. 若要選取所有選項，請選取 [ **選取所有** ] 旁的 [選取 **類別**]。

1. 選取 [匯出記錄]。

匯出的記錄會新增至 [封存的 **記錄** ] 清單中。 將 OTP 從匯出記錄檔中的個別訊息和媒體傳送給支援小組。 支援小組只能使用用來加密記錄的唯一 OTP 來將匯出的記錄檔解壓縮。

封存的記錄清單最多可包含五個專案。 如果清單中的專案數超過該數位，則會刪除最舊的專案。

## <a name="see-also"></a>請參閱

- [檢視警示](how-to-view-alerts.md)

- [設定 SNMP MIB 監視](how-to-set-up-snmp-mib-monitoring.md)

- [瞭解感應器中斷連接事件](how-to-manage-sensors-from-the-on-premises-management-console.md#understand-sensor-disconnection-events)
