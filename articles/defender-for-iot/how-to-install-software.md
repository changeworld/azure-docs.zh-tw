---
title: 適用于 IoT 的 Defender 安裝
description: 瞭解如何安裝適用于 IoT 的 Azure Defender 的感應器和內部部署管理主控台。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/2/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 49a0129ff26d4a12392066aa6304317d71fdb0f1
ms.sourcegitcommit: 08458f722d77b273fbb6b24a0a7476a5ac8b22e0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/15/2021
ms.locfileid: "98247585"
---
# <a name="defender-for-iot-installation"></a>適用于 IoT 的 Defender 安裝

本文說明如何安裝下列適用于 IoT 的 Azure Defender 元素：

- **感應器**：適用于 IoT 的 Defender 感應器會使用被動 (無代理程式) 監視來收集 ICS 網路流量。 被動和不造成干擾，感應器對 OT 和 IoT 網路和裝置沒有任何影響。 感應器會連接到 SPAN 埠或網路點，並立即開始監視您的網路。 偵測會出現在感應器主控台中。 在那裡，您可以在網路地圖、裝置清查和廣泛的報表範圍內進行查看、調查和分析。 範例包括風險評量報告、資料採礦查詢和攻擊媒介。 請參閱《 [Defender For IoT 感應器」使用者指南 ](./getting-started.md)中的有關感應器功能的詳細資訊， (直接下載) 。

- 內部 **部署管理主控台**：內部部署管理主控台可讓您執行裝置管理、風險管理和弱點管理。 您也可以使用它來執行整個企業的威脅監視和事件回應。 它可讓您統一查看所有網路裝置、金鑰 IoT，以及在部署感應器的設施中偵測到的風險指標和警示。 使用內部部署管理主控台來查看和管理空調網路中的感應器。

本文涵蓋下列安裝資訊：

  - **硬體：** Dell 與 HPE 實體設備詳細資料。

  - **軟體：** 感應器和內部部署管理主控台軟體安裝。

  - **虛擬裝置：** 虛擬機器詳細資料和軟體安裝。

安裝之後，請將您的感應器連線到您的網路。

## <a name="about-defender-for-iot-appliances"></a>關於適用于 IoT 的 Defender 裝置 

下列各節提供適用于 IoT 感應器設備的 Defender 的資訊，以及適用于 Defender for IoT 內部部署管理主控台的設備。

### <a name="physical-appliances"></a>實體設備

適用于 IoT 的 Defender 裝置感應器會連線至 SPAN 埠或網路，並立即開始使用被動 (無代理程式) 監視來收集 ICS 網路流量。 此程式對所有網路和裝置沒有任何影響，因為它不是放在資料路徑中，而且不會主動掃描 OT 裝置。

以下是可用的機架掛接設備：

| **部署類型** | **公司** | **企業** | **SMB** |  |
|--|--|--|--|--|
| **型號** | HPE ProLiant DL360 | Dell PowerEdge R340 XL | HPE ProLiant DL20 | HPE ProLiant DL20 |
| **監視埠** | 最多15個 RJ45 或8個選擇 | 最多9個 RJ45 或6個選擇 | 最多8個 RJ45 或6個選擇 | 4個 RJ45 |
| **最大 \* 頻寬* _ | 3 Gb/秒 | 1 Gb/秒 | 1 Gb/秒 | 100 Mb/秒 |
| _ *受保護的裝置數上限** | 30,000 | 10,000 | 15,000 | 1,000 |

* 頻寬容量上限可能會依通訊協定散發而有所不同。

### <a name="virtual-appliances"></a>虛擬應用裝置

可用的虛擬裝置如下：

| **部署類型** | **企業** | **SMB** | **線條** |
|--|--|--|--|
| **描述** | 適用于企業部署的虛擬裝置 | 適用于 SMB 部署的虛擬裝置 | 適用于程式列部署的虛擬裝置 |
| **最大 \* 頻寬* _ | 150 Mb/秒 | 每秒 15 Mb | 3 Mb/秒 |
| _ *受保護的裝置數上限** | 3,000 | 300 | 100 |
| **部署類型** | Enterprise | SMB | 線條 |
| **描述** | 適用于企業部署的虛擬裝置 | 適用于 SMB 部署的虛擬裝置 | 適用于程式列部署的虛擬裝置 |

* 頻寬容量上限可能會依通訊協定散發而有所不同。

### <a name="hardware-specifications-for-the-on-premises-management-console"></a>內部部署管理主控台的硬體規格

 | Item | 描述 |
 |----|--|
 **說明** | 在多層式架構中，內部部署管理主控台可跨分散各地的網站提供可見度和控制。 它會與 SOC 安全性堆疊整合，包括 Siem、票證系統、下一代防火牆、安全的遠端存取平臺，以及適用于 IoT ICS 惡意程式碼沙箱的 Defender。 |
 **部署類型** | Enterprise |
 **裝置類型**  | Dell R340，VM |
 **受管理的感應器數目** | 無限制 |

## <a name="prepare-for-the-installation"></a>準備安裝

### <a name="access-the-iso-installation-image"></a>存取 ISO 安裝映射

您可以從 Defender for IoT 入口網站存取安裝映射。

若要存取檔案：

1. 登入您的 Defender for IoT 帳戶。

2. 移至 **網路感應器** 或內部 **部署管理主控台** 頁面，並選取要下載的版本。

### <a name="install-from-dvd"></a>從 DVD 安裝

安裝之前，請確定您有：

- 具有 USB 連接器的便攜 DVD 磁片磁碟機。

- ISO 安裝程式映射。

若要安裝：

1. 將映射燒錄至 DVD，或在金鑰上準備磁片。 將便攜 DVD 光碟機連接到您的電腦，以滑鼠右鍵按一下 ISO 映像，然後選取 [ **燒錄到磁片**]。

1. 連接機碼上的 DVD 或磁片，並將設備設定為從 DVD 或磁片上的機碼開機。

### <a name="install-from-disk-on-a-key"></a>從磁片上的機碼安裝

安裝之前，請確定您有：

  - Rufus 已安裝。
  
  - 具有 USB 3.0 版和更新版本的金鑰磁片。 大小下限是 4 GB。

  - ISO 安裝程式影像檔案。

在此程式中，將會清除金鑰上的磁片。

若要在機碼上準備磁片：

1. 執行 Rufus 並選取 [ **感應器 ISO**]。

2. 將機碼上的磁片連接至前端面板。

3. 將伺服器的 BIOS 設定為從 USB 開機。

## <a name="dell-poweredger340xl-installation"></a>Dell PowerEdgeR340XL 安裝

在 Dell 設備上安裝軟體之前，您需要調整設備的 BIOS 設定：

  - [Dell Poweredge R340 前端面板](#dell-poweredge-r340-front-panel) 和 [Dell Poweredge R340 Back 面板](#dell-poweredge-r340-back-panel) 包含前端和後面板的說明，以及安裝所需的資訊，例如驅動程式和埠。

  - [DELL BIOS](#dell-bios-configuration) 設定提供如何連線到 Dell 裝置管理介面及設定 BIOS 的相關資訊。

  - [軟體安裝 (Dell R340) ](#software-installation-dell-r340) 說明為 IoT 感應器軟體安裝 Defender 所需的程式。

### <a name="dell-poweredge-r340xl-requirements"></a>Dell PowerEdge R340XL 需求 

若要安裝 Dell PowerEdge R340XL 設備，您需要：

- 適用于 Dell 遠端存取控制器 (iDrac) 的 Enterprise 授權

- BIOS 設定 XML

- 伺服器固件版本：

  - BIOS 版本2.1。6

  - iDrac 版本3.23.23.23

### <a name="dell-poweredge-r340-front-panel"></a>Dell PowerEdge R340 前端面板

:::image type="content" source="media/tutorial-install-components/view-of-dell-poweredge-r340-front-panel.jpg" alt-text="Dell PowerEdge R340 前端面板。":::

 1. 左方控制台 
 2. 光學磁片磁碟機 (選用)  
 3. 右邊的控制台 
 4. 資訊標記 
 5. 磁碟機  

### <a name="dell-poweredge-r340-back-panel"></a>Dell PowerEdge R340 後面板

:::image type="content" source="media/tutorial-install-components/view-of-dell-poweredge-r340-back-panel.jpg" alt-text="Dell PowerEdge R340 後面板。":::

1. 序列埠 
2. NIC 埠 (Gb 1)  
3. NIC 埠 (Gb 1)  
4. 半高度 PCIe 
5. 全高度 PCIe 擴充卡插槽 
6. 電源供應器單位1 
7. 電源供應器單位2 
8. 系統識別 
9. 系統狀態指示器纜線埠 (CMA) 按鈕 
10. USB 3.0 埠 (2)  
11. iDRAC9 私人網路絡埠 
12. VGA 埠 

### <a name="dell-bios-configuration"></a>Dell BIOS 設定

需要 dell BIOS 設定，才能調整 Dell 設備以使用軟體。

BIOS 設定是透過預先定義的設定來執行。 您可以從說明 [中心](https://help.cyberx-labs.com/)存取該檔案。

將設定檔匯入至 Dell 應用裝置。 使用設定檔之前，您必須建立 Dell 設備與管理電腦之間的通訊。

Dell 設備是由具有生命週期控制器 (LC) 的整合式 iDRAC 所管理。 LC 內嵌在每部 Dell PowerEdge 伺服器，並提供可協助您部署、更新、監視及維護 Dell PowerEdge 設備的功能。

若要建立 Dell 設備與管理電腦之間的通訊，您必須在相同的子網上定義 iDRAC IP 位址和管理電腦的 IP 位址。

建立連線時，可設定 BIOS。

若要設定 Dell BIOS：

1. [設定 iDRAC IP 位址](#configure-idrac-ip-address)

2. [匯入 BIOS 設定檔](#import-the-bios-configuration-file)

#### <a name="configure-idrac-ip-address"></a>設定 iDRAC IP 位址

1. 開啟感應器電源。

2. 如果已安裝作業系統，請選取 F2 鍵以進入 BIOS 設定。

3. 選取 [ **IDRAC 設定**]。

4. 選取 [ **網路**]。

   > [!NOTE]
   > 在安裝期間，您必須設定下列步驟中所述的預設 iDRAC IP 位址和密碼。 安裝之後，您可以變更這些定義。

5. 將靜態 IPv4 位址變更為 **10.100.100.250**。

6. 將靜態子網路遮罩變更為 **255.255.255.0**。

   :::image type="content" source="media/tutorial-install-components/idrac-network-settings-screen-v2.png" alt-text="顯示靜態子網路遮罩的螢幕擷取畫面。":::

7. 選取 [**後端**  >  **]**。

#### <a name="import-the-bios-configuration-file"></a>匯入 BIOS 設定檔

本文說明如何使用設定檔來設定 BIOS。

1. 將靜態預先設定 IP 位址 **10.100.100.200** 的電腦插入 **iDRAC** 埠。

   :::image type="content" source="media/tutorial-install-components/idrac-port.png" alt-text="預先設定之 IP 位址埠的螢幕擷取畫面。":::

2. 開啟瀏覽器並輸入 **10.100.100.250** ，以連接到 iDRAC web 介面。

3. 使用 Dell 預設系統管理員許可權登入：

   - 使用者名稱： **根**

   - 密碼： **《 calvin**

4. 設備的認證如下：

   - 使用者名稱： **XXX**

   - 密碼： **XXX**

     匯入伺服器設定檔作業已啟動。

     > [!NOTE]
     > 匯入檔案之前，請確定：
     > - 您是目前連線到 iDRAC 的唯一使用者。
     > - 系統不在 BIOS 功能表中。

5. 移至 **設定伺服器設定配置**  >  **檔**。 設定下列參數︰

   :::image type="content" source="media/tutorial-install-components/configuration-screen.png" alt-text="顯示伺服器設定檔設定的螢幕擷取畫面。":::

   | 參數 | 組態 |
   |--|--|
   | 位置類型 | 選取 [ **本機**]。 |
   | 檔案路徑 | 選取 **[選擇** 檔案] 並加入設定 XML 檔案。 |
   | 匯入元件 | 選取 [ **BIOS]、[NIC]、[RAID**]。 |
   | 等候時間上限 | 選取 [ **20 分鐘**]。 |

6. 選取 [匯入]。

7. 若要監視此程式，請移至 **維護**  >  **作業佇列**。

   :::image type="content" source="media/tutorial-install-components/view-the-job-queue.png" alt-text="顯示工作佇列的螢幕擷取畫面。":::

#### <a name="manually-configuring-bios"></a>手動設定 BIOS 

如果有下列情況，您需要手動設定設備 BIOS：

- 您未從箭號購買您的設備。

- 您有一個設備，但沒有 XML 設定檔的存取權。

存取 BIOS 之後，請移至 [ **裝置設定**]。

手動設定：

1. 使用鍵盤和螢幕直接存取設備 BIOS，或使用 iDRAC。

   - 如果設備不是適用于 IoT 設備的 Defender，請開啟瀏覽器並移至先前設定的 IP 位址。 使用 Dell 預設系統管理員許可權登入。 使用 **root** 做為密碼的使用者名稱和 **《 calvin** 。

   - 如果設備是適用于 IoT 設備的 Defender，請使用 Xxx 作為使用者名稱的 **xxx** 登入，並以 **xxx** 作為密碼。

2. 存取 BIOS 之後，請移至 [ **裝置設定**]。

3. 選取 [ **整合式 RAID 控制器1： DELL PERC 設定 \<PERC H330 Adapter\> 公用程式**]，以選擇 RAID 控制的設定。

4. 選取 [設定 **管理**]。

5. 選取 [ **建立虛擬磁片**]。

6. 在 [ **選取 RAID 層級** ] 欄位中，選取 [ **RAID5**]。 在 [ **虛擬磁片名稱** ] 欄位中，輸入 **ROOT** ，然後選取 [ **實體磁片**]。

7. 選取 [**全部檢查**]，然後選取 [套用 **變更**]

8. 選取 [確定]  。

9. 向下滾動，然後選取 [ **建立虛擬磁片**]。

10. 選取 [ **確認** ] 核取方塊，然後選取 [ **是]**。

11. 選取 [確定]。

12. 返回主畫面，然後選取 [ **系統 BIOS**]。

13. 選取 [ **開機設定**]。

14. 在 [ **開機模式]** 選項中，選取 [ **BIOS**]。

15. 選取 [ **上一步**]，然後選取 **[完成] 以結束** BIOS 設定。

### <a name="software-installation-dell-r340"></a>軟體安裝 (Dell R340) 

安裝程式大約需要20分鐘的時間。 安裝之後，系統會重新開機數次。

若要安裝：

1. 以下列其中一種方式，確認版本媒體已掛接至設備：

   - 將機碼上的外部 CD 或磁片與版本連接。

   - 使用 iDRAC 掛接 ISO 映像。 登入 iDRAC 之後，請選取虛擬主控台，然後選取 [ **虛擬媒體**]。

2. 在 [ **地圖 CD/DVD** ] 區段中，選取 **[選擇** 檔案]。

3. 從開啟的對話方塊中，選擇這個版本的 ISO 影像檔案。

4. 選取 [ **地圖裝置** ] 按鈕。

   :::image type="content" source="media/tutorial-install-components/mapped-device-on-virtual-media-screen-v2.png" alt-text="顯示對應裝置的螢幕擷取畫面。":::

5. 裝載的媒體。 選取 [關閉]。

6. 啟動設備。 當您使用 iDRAC 時，您可以選取 [ **Consul 控制** ] 按鈕來重新開機伺服器。 然後，在 **鍵盤宏** 上選取 [套用 **] 按鈕，** 這將會啟動 Ctrl + Alt + Delete 序列。

7. 選取 [ **英文**]。

8. 選取 [ **感應器-發行- \<version\> 企業**]。

   :::image type="content" source="media/tutorial-install-components/sensor-version-select-screen-v2.png" alt-text="顯示版本選取專案的螢幕擷取畫面。":::   

9. 定義設備設定檔和網路屬性：

   :::image type="content" source="media/tutorial-install-components/appliance-profile-screen-v2.png" alt-text="顯示裝置設定檔的螢幕擷取畫面。":::   

   | 參數 | 組態 |
   |--|--|
   | **硬體設定檔** | **企業** |
   | **管理介面** | **eno1** |
   | **客戶 (提供的網路參數)** | - |
   |**管理網路 IP 位址：** | - |
   | **子網路遮罩：** | - |
   | **設備主機名稱：** | - |
   | **Dns：** | - |
   | **預設閘道 IP 位址：** | - |
   | **輸入介面：** |  系統會為您產生輸入介面的清單。 若要鏡像輸入介面，請以逗號分隔字元複製清單中顯示的所有專案。 請注意，不需要設定橋接器介面。 此選項僅供特殊使用案例使用。 |

10. 大約10分鐘後，就會出現兩組認證。 其中一個適用于 **CyberX** 使用者，另一個適用于 **支援** 使用者。  

11. 儲存設備識別碼和密碼。 您將需要這些認證，才能在第一次使用時存取平臺。

12. 選取 **Enter 鍵** 以繼續。

## <a name="hpe-proliant-dl20-installation"></a>HPE ProLiant DL20 安裝

本文說明 HPE ProLiant DL20 安裝程式，其中包含下列步驟：

  - 啟用遠端存取並更新預設系統管理員密碼。
  - 設定 BIOS 和 RAID 設定。
  - 安裝軟體。

### <a name="about-the-installation"></a>關於安裝

  - 您可以安裝企業與 SMB 設備。 除了陣列設定以外，這兩個裝置類型的安裝程式都相同。
  - 系統會提供預設的系統管理使用者。 建議您在網路設定過程中變更密碼。
  - 在網路設定過程中，您將在網路埠1設定 iLO 埠。
  - 安裝程式大約需要20分鐘的時間。 安裝之後，系統會重新開機數次。

### <a name="hpe-proliant-dl20-front-panel"></a>HPE ProLiant DL20 前端面板

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl20-front-panel-v2.png" alt-text="HPE ProLiant DL20 前端面板。":::

### <a name="hpe-proliant-dl20-back-panel"></a>HPE ProLiant DL20 上一頁面板

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl20-back-panel-v2.png" alt-text="HPE ProLiant DL20 的 [後端] 面板。":::

### <a name="enable-remote-access-and-update-the-password"></a>啟用遠端存取並更新密碼

您可以使用下列程式來設定網路選項及更新預設密碼。

若要啟用和更新密碼：

1. 將螢幕和鍵盤連接到 HP 設備、開啟設備，然後按 **F9**。

    :::image type="content" source="media/tutorial-install-components/hpe-proliant-screen-v2.png" alt-text="顯示 [HPE ProLiant] 視窗的螢幕擷取畫面。":::

2. 移至 **系統公用事業**  >  **system Configuration**  >  **iLO 5 設定公用程式**  >  **網路選項**。

    :::image type="content" source="media/tutorial-install-components/system-configuration-window-v2.png" alt-text="顯示 [系統組態] 視窗的螢幕擷取畫面。":::

    1.  從 [**網路介面介面卡**] 欄位選取 [**共用網路埠-LOM** ]。
    
    1.  停用 DHCP。
    
    1.  輸入 IP 位址、子網路遮罩和閘道 IP 位址。

3. 選取 **F10：儲存**。

4. 選取 **Esc** 回到 **ILO 5 設定公用程式**，然後選取 [ **使用者管理**]。

5. 選取 [ **編輯/移除使用者**]。 系統管理員是唯一定義的預設使用者。 

6. 變更預設密碼，然後選取 [ **F10：儲存**]。

### <a name="configure-the-hpe-bios"></a>設定 HPE BIOS

下列程式說明如何為企業和 SMB 設備設定 HPE BIOS。

若要設定 HPE BIOS：

1. 選取 [**系統公用事業**  >  **system Configuration**  >  **BIOS/平臺設定] (RBSU)**。

2. 在 [ **BIOS/平臺設定 (RBSU)** ] 表單中，選取 [ **開機選項**]。

3. 將 **開機模式** 變更為 **舊版 BIOS 模式**，然後選取 [ **F10：儲存**]。

4. 選取 **Esc 鍵** 兩次以關閉 **系統組態** 表單。

#### <a name="for-the-enterprise-appliance"></a>企業應用裝置

1. Select **Embedded RAID 1： HPE Smart Array P408i-a SR Gen 10**  >  **Array Configuration**  >  **Create array**。

2. 在 [ **建立陣列** ] 表單中，選取所有選項。 **企業** 應用裝置有三個選項。

#### <a name="for-the-smb-appliance"></a>適用于 SMB 設備

1. Select **Embedded RAID 1： HPE Smart Array P208i-a SR Gen 10**  >  **Array Configuration**  >  **Create array**。

2. 選取 **[繼續至下一個表單]**。

3. 在 [**設定 Raid 層級**] 表單中，將企業部署的 [層級] 設定為 **raid 5** ，並為 [SMB 部署] 設定 **raid 1**

4. 選取 **[繼續至下一個表單]**。

5. 在 [ **邏輯磁碟機標籤** ] 表單中，輸入 **邏輯磁碟機 1**。

6. 選取 [ **提交變更**]。

7. 在 [ **提交** ] 表單中，選取 [ **回到主功能表**]。

8. 選取 **F10：儲存** ，然後按下 **Esc** 鍵兩次。

9. 在 [ **系統公用程式** ] 視窗中，選取 [ **一次開機] 功能表**。

10. 在 [ **一次性開機功能表** ] 表單中，選取 [ **舊版 BIOS] One-Time 開機功能表**。

11. [ **在舊版** 和 **開機覆寫** 中啟動] 視窗隨即出現。 選擇開機覆寫選項;例如，到 CD-ROM、USB、HDD 或 UEFI shell。

    :::image type="content" source="media/tutorial-install-components/boot-override-window-one-v2.png" alt-text="顯示第一個開機覆寫視窗的螢幕擷取畫面。":::

    :::image type="content" source="media/tutorial-install-components/boot-override-window-two-v2.png" alt-text="顯示第二個開機覆寫視窗的螢幕擷取畫面。":::
### <a name="software-installation-hpe-proliant-dl20-appliance"></a>軟體安裝 (HPE ProLiant DL20 設備) 

安裝程式大約需要20分鐘的時間。 安裝之後，系統會重新開機數次。

若要安裝軟體：

1. 將畫面和鍵盤連接到設備，然後連接到 CLI。

2. 使用您從 Defender for IoT 入口網站中的 [ **更新** ] 頁面下載的 ISO 映像，將金鑰上的外部 CD 或磁片連線。

3. 啟動設備。

4. 選取 [ **英文**]。

    :::image type="content" source="media/tutorial-install-components/select-english-screen.png" alt-text="在 CLI 視窗中選取英文。":::

5. 選取 [ **感應器-發行- <version> 企業**]。

    :::image type="content" source="media/tutorial-install-components/sensor-version-select-screen-v2.png" alt-text="選取版本的畫面螢幕擷取畫面。":::

6. 在安裝精靈中，定義設備設定檔和網路屬性：

    :::image type="content" source="media/tutorial-install-components/installation-wizard-screen-v2.png" alt-text="顯示安裝精靈的螢幕擷取畫面。":::

    | 參數 | 組態 |
    | ----------| ------------- |
    | **硬體設定檔** | 選取 [ **企業** ] 或 [ **辦公室** ] 進行 SMB 部署。 |
    | **管理介面** | **eno2** |
    | **預設的網路參數 (通常是由客戶提供參數)** | **管理網路 IP 位址：** <br/> <br/>**設備主機名稱：** <br/>**Dns：** <br/>**預設閘道 IP 位址：**|
    | **輸入介面：** | 系統會為您產生輸入介面的清單。<br/><br/>若要鏡像輸入介面，請使用逗號分隔符號複製清單中顯示的所有專案： **eno5、eno3、eno1、eno6、eno4**<br/><br/>**針對 HPE DL20：請勿列出 eno1、enp1s0f4u4 (iLo 介面)**<br/><br/>**橋接器**：不需要設定橋接器介面。 此選項僅供特殊使用案例使用。 按 **Enter** 以繼續。 |

7. 大約10分鐘後，就會出現兩組認證。 其中一個適用于 **CyberX** 使用者，另一個適用于 **支援** 使用者。

8. 儲存設備的識別碼和密碼。 您第一次需要存取平臺的認證。

9. 選取 **Enter 鍵** 以繼續。

## <a name="hpe-proliant-dl360-installation"></a>HPE ProLiant DL360 安裝

  - 系統會提供預設的系統管理使用者。 建議您在網路設定期間變更密碼。

  - 在網路設定期間，您將設定 iLO 埠。

  - 安裝程式大約需要20分鐘的時間。 安裝之後，系統會重新開機數次。

### <a name="hpe-proliant-dl360-front-panel"></a>HPE ProLiant DL360 前端面板

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl360-front-panel.png" alt-text="HPE ProLiant DL360 前端面板。":::

### <a name="hpe-proliant-dl360-back-panel"></a>HPE ProLiant DL360 上一頁面板

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl360-back-panel.png" alt-text="HPE ProLiant DL360 上一頁面板。":::

### <a name="enable-remote-access-and-update-the-password"></a>啟用遠端存取並更新密碼

請參閱上述各節中的 HPE ProLiant DL20 安裝：

  - 「啟用遠端存取並更新密碼」

  - 「設定 HPE BIOS」

企業設定相同。

> [!Note]
> 在 [陣列] 表單中，確認您選取了所有選項。

### <a name="ilo-remote-installation-from-a-virtual-drive"></a>從虛擬磁片磁碟機) 的 iLO 遠端安裝 (

此程式描述從虛擬磁片磁碟機進行的 iLO 安裝。

若要安裝：

1. 登入 iLO 主控台，然後以滑鼠右鍵按一下 [伺服器] 畫面。

2. 選取 **HTML5 主控台**。

3. 在主控台中，選取 CD 圖示，然後選擇 [CD/DVD] 選項。

4. 選取 [ **本機 ISO** 檔案]。

5. 在對話方塊中，選擇相關的 ISO 檔案。

6. 移至左邊的圖示，選取 [ **電源**]，然後選取 [ **重設**]。

7. 設備將會重新開機並執行感應器安裝程式。

### <a name="software-installation-hpe-dl360"></a>軟體安裝 (HPE DL360) 

安裝程式大約需要20分鐘的時間。 安裝之後，系統會重新開機數次。

若要安裝：

1. 將畫面和鍵盤連接到設備，然後連接到 CLI。

2. 使用您從 Defender for IoT 入口網站中的 [ **更新** ] 頁面下載的 ISO 映像，連接金鑰上的外部 CD 或磁片。

3. 啟動設備。

4. 選取 [ **英文**]。

5. 選取 [ **感應器-發行- <version> 企業**]。

    :::image type="content" source="media/tutorial-install-components/sensor-version-select-screen-v2.png" alt-text="顯示選取版本的螢幕擷取畫面。":::

6. 在安裝精靈中，定義設備設定檔和網路屬性。

    :::image type="content" source="media/tutorial-install-components/installation-wizard-screen-v2.png" alt-text="顯示安裝精靈的螢幕擷取畫面。":::

    | 參數 | 組態 |
    | ----------| ------------- |
    | **硬體設定檔** | 選取 [ **公司**]。 |
    | **管理介面** | **eno2** |
    | **客戶 (提供的預設網路參數)** | **管理網路 IP 位址：** <br/>**子網路遮罩：** <br/>**設備主機名稱：** <br/>**Dns：** <br/>**預設閘道 IP 位址：**|
    | **輸入介面：**  | 系統會為您產生輸入介面的清單。<br/><br/>若要鏡像輸入介面，請以逗號分隔字元複製清單中顯示的所有專案。<br/><br/>請注意，不需要設定橋接器介面。 此選項僅供特殊使用案例使用。 |

7. 大約10分鐘後，就會出現兩組認證。 其中一個適用于 **CyberX** 使用者，另一個適用于 **支援** 使用者。

8. 儲存設備的識別碼和密碼。 您將需要這些認證，才能首次存取平臺。

9. 選取 **Enter 鍵** 以繼續。

## <a name="sensor-installation-for-the-virtual-appliance"></a>虛擬裝置的感應器安裝

您可以在下列架構中部署適用于 Defender for IoT 感應器的虛擬機器：


| 架構 | 規格 | 使用方式 | 註解 |
|---|---|---|---|
| **企業** | CPU：8<br/>記憶體： 32G RAM<br/>HDD： 1800 GB | 生產環境 | 預設和最常見 |
| **小型企業** | CPU：4 <br/>記憶體： 8G RAM<br/>HDD： 500 GB | 測試或小型生產環境 | -  |
| **Office** | CPU：4<br/>記憶體： 8G RAM<br/>HDD： 100 GB | 小型測試環境 | -  |

### <a name="prerequisites"></a>必要條件

內部部署管理主控台支援 VMware 和 Hyper-v 部署選項。 開始安裝之前，請確定您有下列專案：

  - VMware (ESXi 5.5 或更新版本) 或 Hyper-v 虛擬程式 (Windows 10 專業版或 Enterprise) 已安裝且可運作

  - 虛擬機器的可用硬體資源

  - 適用于 IoT 的 Azure Defender 感應器的 ISO 安裝檔案

確定虛擬程式正在執行。

### <a name="create-the-virtual-machine-esxi"></a> (ESXi 建立虛擬機器) 

1. 登入 ESXi，選擇相關的 **資料** 存放區，然後選取 [資料存放區 **瀏覽器**]。

2. **上傳** 影像，然後選取 [ **關閉**]。

3. 移至 [ **虛擬機器**]，然後選取 [ **建立/註冊 VM**]。

4. 選取 [ **建立新的虛擬機器**]，然後選取 **[下一步]**。

5. 新增感應器名稱，然後選擇：

   - 相容性： **&lt; 最新 &gt; 的 ESXi 版本**

   - 來賓 OS 系列： **Linux**

   - 客體作業系統版本： **Ubuntu Linux (64 位)**

6. 選取 [下一步]  。

7. 選擇相關的資料存放區，然後選取 **[下一步]**。

8. 根據所需的架構變更虛擬硬體參數。

9. 若為 **CD/DVD 磁片磁碟機 1**，請選取資料存放區 **ISO** 檔案，然後選擇您先前上傳的 iso 檔案。

10. 選取 [下一步]   > [完成]  。

### <a name="create-the-virtual-machine-hyper-v"></a> (Hyper-v) 建立虛擬機器

此程式描述如何使用 Hyper-v 建立虛擬機器。

若要建立虛擬機器：

1. 在 Hyper-v 管理員中建立虛擬磁片。

2. 選取 **format = VHDX**。

3. 選取 [ **類型] = [動態展開**]。

4. 輸入 VHD 的名稱和位置。

5. 根據架構) 輸入所需的大小 (。   

6. 檢閱摘要，然後選取 [完成]。

7. 在 [ **動作** ] 功能表上，建立新的虛擬機器。

8. 輸入虛擬機器的名稱。

9. 選取 [**指定世代** 層代  >  **1**]。

10. 根據架構) 指定記憶體配置 (，然後選取 [動態記憶體] 的核取方塊。

11. 根據您的伺服器網路拓撲設定網路介面卡。

12. 將先前建立的 VHDX 連接到虛擬機器。

13. 檢閱摘要，然後選取 [完成]。

14. 以滑鼠右鍵按一下新的虛擬機器，然後選取 [ **設定**]。

15. 選取 [ **新增硬體** ]，並新增網路介面卡。

16. 選取將連接至感應器管理網路的虛擬交換器。

17. 根據架構)  (配置 CPU 資源。

18. 將管理主控台的 ISO 映像連接到虛擬 DVD 光碟機。

19. 啟動虛擬機器。

20. 在 [ **動作** ] 功能表上，選取 **[連線]** 以繼續安裝軟體。

### <a name="software-installation-esxi-and-hyper-v"></a>軟體安裝 (ESXi 和 Hyper-v) 

本節說明 ESXi 和 Hyper-v 軟體的安裝。

若要安裝：

1. 開啟虛擬機器主控台。

2. VM 將從 ISO 映像啟動，而且會顯示 [語言選擇] 畫面。 選取 [ **英文**]。

3. 選取所需的架構。

4. 定義設備設定檔和網路屬性：

    | 參數 | 組態 |
    | ----------| ------------- |
    | **硬體設定檔** | &lt;需要的架構&gt; |
    | **管理介面** | **ens192** |
    | **客戶 (提供的網路參數)** | **管理網路 IP 位址：** <br/>**子網路遮罩：** <br/>**設備主機名稱：** <br/>**Dns：** <br/>**預設閘道：** <br/>**輸入介面：**|
    | **橋接器介面：** | 不需要設定橋接器介面。 此選項僅適用于特殊的使用案例。 |

5. 輸入 **Y** 接受設定。

6. 系統會自動產生並顯示登入認證。 將使用者名稱和密碼複製到安全的地方，因為登入和管理都需要這些密碼。

   - **支援**：使用者管理的系統管理使用者。

   - **CyberX**：用來存取設備的根對等專案。

7. 設備重新開機。

8. 透過先前設定的 IP 位址存取管理主控台： `https://ip_address` 。

    :::image type="content" source="media/tutorial-install-components/defender-for-iot-sign-in-screen.png" alt-text="顯示管理主控台存取權的螢幕擷取畫面。":::

## <a name="virtual-appliance-on-premises-management-console-installation"></a>虛擬裝置：內部部署管理主控台安裝

內部部署管理主控台 VM 支援下列架構：

| 架構 | 規格 | 使用方式 | 
|--|--|--|
| Enterprise <br/> (預設和最常見的)  | CPU：8 <br/>記憶體： 32G RAM<br/> HDD： 1.8 TB | 大型生產環境 | 
| Enterprise | CPU：4 <br/> 記憶體： 8G RAM<br/> HDD： 500 GB | 大型生產環境 |
| Enterprise | CPU：4 <br/>記憶體： 8G RAM <br/> HDD： 100 GB | 小型測試環境 | 
   
### <a name="prerequisites"></a>必要條件

內部部署管理主控台支援 VMware 和 Hyper-v 部署選項。 開始安裝之前，請確認下列事項：

- VMware (ESXi 5.5 或更新版本的) 或 Hyper-v 虛擬程式 (Windows 10 專業版或 Enterprise) 已安裝且可運作。

- 虛擬機器可以使用硬體資源。

- 您有內部部署管理主控台的 ISO 安裝檔案。
    
- 虛擬程式正在執行。

### <a name="create-the-virtual-machine-esxi"></a> (ESXi 建立虛擬機器) 

若要建立虛擬機器 (ESXi) ：

1. 登入 ESXi，選擇相關的 **資料** 存放區，然後選取 [資料存放區 **瀏覽器**]。

2. 上傳影像，然後選取 [ **關閉**]。

3. 移至 **虛擬機器**。

4. 選取 [ **建立/註冊 VM**]。

5. 選取 [ **建立新的虛擬機器** ]，然後選取 **[下一步]**。

6. 新增感應器名稱，然後選擇：

   - 相容性： \<latest ESXi version>

   - 來賓 OS 系列： Linux

   - 客體作業系統版本： Ubuntu Linux (64 位) 

7. 選取 [下一步]  。

8. 選擇相關資料存放區，然後選取 **[下一步]**。

9. 根據所需的架構變更虛擬硬體參數。

10. 若為 **CD/DVD 磁片磁碟機 1**，請選取資料存放區 **ISO** 檔案，然後選擇您先前上傳的 iso 檔案。

11. 選取 [下一步]   > [完成]  。

### <a name="create-the-virtual-machine-hyper-v"></a> (Hyper-v) 建立虛擬機器

若要使用 Hyper-v 建立虛擬機器：

1. 在 Hyper-v 管理員中建立虛擬磁片。

2. 選取 [ **VHDX** 格式]。

3. 選取 [下一步]  。

4. 選取 **動態擴充** 的類型。

5. 選取 [下一步]  。

6. 輸入 VHD 的名稱和位置。

7. 選取 [下一步]  。

8. 根據架構) 輸入所需的大小 (。

9. 選取 [下一步]  。

10. 檢閱摘要，然後選取 [完成]。

11. 在 [ **動作** ] 功能表上，建立新的虛擬機器。

12. 選取 [下一步]  。

13. 輸入虛擬機器的名稱。

14. 選取 [下一步]  。

15. 選取 [ **產生** ]，並將它設定為 **第1代**。

16. 選取 [下一步]  。

17. 根據架構) 指定記憶體配置 (，然後選取 [動態記憶體] 的核取方塊。

18. 選取 [下一步]  。

19. 根據您的伺服器網路拓撲設定網路介面卡。

20. 選取 [下一步]  。

21. 將先前建立的 VHDX 連接到虛擬機器。

22. 選取 [下一步]  。

23. 檢閱摘要，然後選取 [完成]。

24. 在新的虛擬機器上按一下滑鼠右鍵，然後選取 [ **設定**]。

25. 選取 [新增 **硬體** ]，並新增 **網路介面卡** 的介面卡。

26. 若為 **虛擬交換器**，請選取將連接至感應器管理網路的交換器。

27. 根據架構)  (配置 CPU 資源。

28. 將管理主控台的 ISO 映像連接到虛擬 DVD 光碟機。

29. 啟動虛擬機器。

30. 在 [ **動作** ] 功能表上，選取 **[連線]** 以繼續安裝軟體。

### <a name="software-installation-esxi-and-hyper-v"></a>軟體安裝 (ESXi 和 Hyper-v) 

啟動虛擬機器將會從 ISO 映像啟動安裝程式。

若要安裝軟體：

1. 選取 [ **英文**]。

2. 為您的部署選取必要的架構。

3. 定義感應器管理網路的網路介面：介面、IP、子網、DNS 伺服器和預設閘道。

4. 系統會自動產生並顯示登入認證。 將這些認證保留在安全的地方，因為它們是登入和管理的必要項。

  - **支援**：使用者管理的系統管理使用者。

  - **CyberX**：用來存取設備的根對等專案。

5. 設備重新開機。

6. 透過先前設定的 IP 位址存取管理主控台： `<https://ip_address>` 。

    :::image type="content" source="media/tutorial-install-components/defender-for-iot-management-console-sign-in-screen.png" alt-text="顯示管理主控台登入畫面的螢幕擷取畫面。":::

## <a name="post-installation-validation"></a>安裝後驗證

若要驗證實體設備的安裝，您必須執行數個測試。 相同的驗證程式會套用至所有裝置類型。

使用 GUI 或 CLI 來執行驗證。 驗證可供使用者 **支援** 和使用者 **CyberX** 使用。

安裝後驗證必須包含下列測試：

  - **健全測試**：確認系統正在執行中。

  - **版本**：確認版本是否正確。

  - **ifconfig**：確認安裝程式期間所設定的所有輸入介面都在執行中。

### <a name="checking-system-health-by-using-the-gui"></a>使用 GUI 檢查系統健康情況

:::image type="content" source="media/tutorial-install-components/system-health-check-screen.png" alt-text="顯示系統健康情況檢查的螢幕擷取畫面。":::

#### <a name="sanity"></a>理智

- **設備**：執行設備的健全檢查。 您可以使用 CLI 命令執行相同的檢查 `system-sanity` 。

- **版本**：顯示裝置版本。

- **網路屬性**：顯示感應器網路參數。

#### <a name="redis"></a>Redis

- **記憶體**：提供記憶體使用量的整體概念，例如使用的記憶體數量，以及保留的數量。

- **最長金鑰**：顯示可能造成大量記憶體使用量的最長索引鍵。

#### <a name="system"></a>系統

- **核心記錄**：提供核心記錄檔的最後500個數據列，讓您可以在不匯出整個系統記錄的情況下，查看最近的記錄資料列。

- **工作管理員**：將進程表格中出現的工作轉譯為下列層級： 
  
  - 持續層 (Redis)  
  -  (SQL) 的現金層

- **網路統計資料**：顯示您的網路統計資料。

- **TOP**：顯示進程的資料表。 它是一種 Linux 命令，可為執行中的系統提供動態即時的觀點。

- **備份記憶體檢查**：提供備份記憶體的狀態，檢查下列各項：
  - 備份檔案夾的位置 
  - 備份檔案夾的大小
  - 備份檔案夾的限制
  - 上次備份發生的時間
  - 額外的備份檔案有多少空間

- **ifconfig**：顯示裝置實體介面的參數。

- **CyberX nload**：使用六秒的測試顯示網路流量和頻寬。

- **來自核心的錯誤，記錄**：顯示核心記錄檔中的錯誤。

若要存取此工具：

1. 使用 **支援** 使用者認證登入感應器。

2. 從 [**系統設定**] 視窗中選取 [**系統統計資料**]。

    :::image type="icon" source="media/tutorial-install-components/system-statistics-icon.png" border="false":::

### <a name="checking-system-health-by-using-the-cli"></a>使用 CLI 檢查系統健康情況

**測試1：健全**

確認系統已啟動且正在執行：

1. 使用 Linux 終端機連接到 CLI (例如，PuTTY) 和使用者 **支援**。

2. 輸入 `system sanity`。

3. 檢查所有服務都是綠色 (執行) 。

    :::image type="content" source="media/tutorial-install-components/support-screen.png" alt-text="顯示正在執行之服務的螢幕擷取畫面。":::

4. 確認 **系統已啟動！ (生產)** 出現在底部。

**測試2：版本檢查**

確認已使用正確的版本：

1. 使用 Linux 終端機連接到 CLI (例如，PuTTY) 和使用者 **支援**。

2. 輸入 `system version`。

3. 檢查是否出現正確的版本。

**測試3：網路驗證**

確認安裝程式期間設定的所有輸入介面都在執行：

1. 使用 Linux 終端機連接到 CLI (例如，PuTTY) 和使用者 **支援**。

2. 輸入 `network list` (Linux 命令) 的對等專案 `ifconfig` 。

3. 驗證所需的輸入介面是否出現。 例如，如果安裝了兩個四顆銅 Nic，清單中應該會有10個介面。

    :::image type="content" source="media/tutorial-install-components/interface-list-screen.png" alt-text="顯示介面清單的螢幕擷取畫面。":::

**測試4：對 UI 的管理存取**

確認您可以存取主控台 web GUI：

1. 將具有乙太網路纜線的膝上型電腦連接到管理埠 (**Gb1**) 。

2. 將膝上型電腦 NIC 位址定義為與設備相同的範圍。

    :::image type="content" source="media/tutorial-install-components/access-to-ui.png" alt-text="顯示 UI 管理存取權的螢幕擷取畫面。":::

3. 從膝上型電腦偵測設備的 IP 位址，以驗證 (預設的連線能力： 10.100.10.1) 。

4. 開啟膝上型電腦上的 Chrome 瀏覽器，然後輸入裝置的 IP 位址。

5. 在 [ **您的連接不是私** 用] 視窗中，選取 [ **Advanced** ] 並繼續。

6. 測試成功時，會出現 [Defender for IoT 登入] 畫面。

   :::image type="content" source="media/tutorial-install-components/defender-for-iot-sign-in-screen.png" alt-text="顯示管理主控台存取權的螢幕擷取畫面。":::

## <a name="troubleshooting"></a>疑難排解

### <a name="you-cant-connect-by-using-a-web-interface"></a>您無法使用 web 介面進行連接

1. 確認您嘗試連線的電腦與設備位於相同的網路上。

2. 確認 GUI 網路已連接到管理埠。

3. 偵測設備的 IP 位址。 如果沒有 ping：

   1. 將監視器和鍵盤連接到設備。

   1. 使用 **支援** 使用者和密碼登入。

   1. 使用命令 `network list` 來查看目前的 IP 位址。

      :::image type="content" source="media/tutorial-install-components/network-list.png" alt-text="顯示 [網路] 清單的螢幕擷取畫面。":::

4. 如果網路參數設定不正確，請使用下列程式來變更它們：

   1. 使用命令 `network edit-settings` 。

   1. 若要變更管理網路的 IP 位址，請選取 [ **Y**]。

   1. 若要變更子網路遮罩，請選取 [ **Y**]。

   1. 若要變更 DNS，請選取 [ **Y**]。

   1. 若要變更預設閘道 IP 位址，請選取 [ **Y**]。

   1. 針對 [輸入介面變更 (感應器僅) ]，選取 [ **N**]。

   1. 若要套用設定，請選取 [ **Y**]。

5. 重新開機之後，請使用支援使用者認證連接，然後使用 `network list` 命令確認參數是否已變更。

6. 再次嘗試從 GUI 進行 ping 並連接。

### <a name="the-appliance-isnt-responding"></a>設備沒有回應

1. 將監視器和鍵盤連接到設備，或使用 PuTTY 從遠端連線至 CLI。

2. 使用 **支援** 使用者的認證來登入。

3. 使用 `system sanity` 命令，並檢查所有進程是否正在執行。

    :::image type="content" source="media/tutorial-install-components/system-sanity-screen.png" alt-text="顯示 [系統健全] 命令的螢幕擷取畫面。":::

如有任何其他問題，請聯絡 [Microsoft 支援服務](https://support.microsoft.com/en-us/supportforbusiness/productselection?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099)。

## <a name="appendix-a-mirroring-port-on-vswitch-esxi"></a>附錄 A： vSwitch 上的鏡像埠 (ESXi) 

### <a name="configure-a-span-port-on-an-existing-vswitch"></a>在現有的 vSwitch 上設定 SPAN 埠

VSwitch 沒有鏡像功能，但您可以使用簡單的因應措施來執行 SPAN 埠。

若要設定 SPAN 埠：

1. 開啟 [vSwitch 屬性]。

2. 選取 [新增]。

3. 接著選取 [**虛擬機器**  >  **]**。

4. 插入網路標籤 **範圍網路**，選取 [ **VLAN ID**  >  **All**]，然後選取 **[下一步]**。

5. 選取 [完成]。

6. 選取 [ **SPAN 網路** ] > **編輯*]。

7. 選取 [ **安全性**]，並確認 [ **混合模式]** 原則設定為 [ **接受** ] 模式。

8. 選取 **[確定]**，然後選取 [ **關閉** ] 以關閉 [vSwitch] 屬性。

9. 開啟 **XSENSE VM** 屬性。

10. 若為 **網路介面卡 2**，請選取 [ **SPAN** 網路]。

11. 選取 [確定]。

12. 連接至感應器，並確認鏡像可正常運作。

## <a name="appendix-b-access-sensors-from-the-on-premises-management-console"></a>附錄 B：從內部部署管理主控台存取感應器

您可以防止使用者直接存取感應器，藉此增強系統安全性。 相反地，請使用 proxy 通道，讓使用者從內部部署管理主控台使用單一防火牆規則來存取感應器。 這項技術可減少未經授權存取感應器之外網路環境的可能性。 使用者登入感應器時的體驗會維持不變。

:::image type="content" source="media/tutorial-install-components/sensor-system-graph.png" alt-text="顯示感應器存取權的螢幕擷取畫面。":::

若要啟用通道：

1. 使用 **CyberX** 登入內部部署管理主控台的 CLI，或 **支援** 使用者認證。

2. 輸入 `sudo cyberx-management-tunnel-enable`。

3. 選取 **Enter**。

4. 輸入 `--port 10000`。

### <a name="next-steps"></a>後續步驟

[設定網路](how-to-set-up-your-network.md)