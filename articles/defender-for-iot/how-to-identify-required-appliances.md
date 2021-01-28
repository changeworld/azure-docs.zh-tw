---
title: 識別所需的設備
description: 瞭解適用于 IoT 感應器和內部部署管理主控台之已認證 Defender 的硬體和虛擬裝置。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 01/13/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 92bf066c9769cc4b2525923b9e18ed3c0e9c577a
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98937140"
---
# <a name="identify-required-appliances"></a>識別所需的設備

本文提供適用于 IoT 感應器設備的已認證 Defender 的相關資訊。 Defender fort IoT 可以部署在實體和虛擬裝置上。

這包括經過認證的 *預先設定* 設備、已安裝的軟體，以及您可以下載並安裝所需軟體的未設定認證設備。

本文也會提供內部部署管理主控台設備的規格。 內部部署管理主控台無法作為預先設定的設備使用。

- 如果您想要購買預先設定的感應器，請檢查 [ [感應器設備](#sensor-appliances) ] 區段中可用的模型，然後繼續進行購買。

- 如果您想要購買自己的設備，請在 [ [感應器設備](#sensor-appliances) ] 區段和 [ [其他認證的設備](#additional-certified-appliances) ] 區段中，查看可用的模型。 取得設備之後，您可以下載並安裝軟體。

- 如果您想要購買內部部署管理主控台，請參閱內部 [部署管理主控台設備](#on-premises-management-console-appliance) 一節中的資訊。 取得裝置之後，您就可以下載並安裝軟體。

在這裡完成工作之後，您可以安裝軟體並設定您的網路。

## <a name="sensor-appliances"></a>感應器設備

適用于 IoT 的 Defender 支援實體與虛擬部署。

### <a name="physical-sensors"></a>實體感應器

本節提供可用實體感應器模型的總覽。 您可以購買具有預先設定軟體的感應器，或購買未預先設定的感應器。

| 部署類型 | 公司 | Enterprise | SMB |
|--|--|--|--|
| 映像 | :::image type="content" source="media/how-to-prepare-your-network/corporate-hpe-proliant-dl360-v2.png" alt-text="公司層級模型。"::: | :::image type="content" source="media/how-to-prepare-your-network/enterprise-and-smb-hpe-proliant-dl20-v2.png" alt-text="企業層級模型。"::: | :::image type="content" source="media/how-to-prepare-your-network/enterprise-and-smb-hpe-proliant-dl20-v2.png" alt-text="SMB 層級模型。"::: |
| 型號 | HPE ProLiant DL360 | HPE ProLiant DL20 | HPE ProLiant DL20 |
| 監視埠 | 最多15個 RJ45 或8個選擇 | 最多8個 RJ45 或6個選擇 | 4個 RJ45 |
| 最大頻寬 [1](#anchortext) | 每秒 3 Gb | 每秒 1 Gb | 每秒 200 Mb |
| 受保護的裝置上限 | 30,000 | 15,000 | 1,000 |

請參閱廠商詳細資料的 [設備規格](#appliance-specifications) 。

關於預先設定的感應器： Microsoft 已與箭號合作，以提供預先設定的感應器。 若要購買預先設定的感應器，請使用下列位址的 contact 箭號： <hardware.sales@arrow.com>

關於攜帶您自己的設備：請參閱此處所述的支援模型。 取得設備之後，請移至適用于 **IoT**  >  **網路感應器 ISO**  >  **安裝** 的 Defender 以下載軟體。

:::image type="content" source="media/how-to-prepare-your-network/azure-defender-for-iot-sensor-download-software-screen.png" alt-text="網路感應器 ISO。":::

<a id="anchortext">1</a> 個頻寬容量可能會有所不同，取決於通訊協定的散發。

### <a name="virtual-sensors"></a>虛擬感應器

本節提供可用虛擬感應器的總覽。

| 部署類型 | 公司 | Enterprise | SMB |
|--|--|--|--|
| 最大頻寬 | 2.5 Gb/秒 | 800 Mb/秒 | 160 Mb/秒 |
| 受保護的裝置上限 | 30,000 | 10,000 | 2,500 |

## <a name="on-premises-management-console-appliance"></a>內部部署管理主控台設備

管理主控台是以虛擬部署的形式提供。

| 部署類型 | Enterprise |
|--|--|
| 裝置類型 | HPE DL20，VM |
| 受管理的感應器數目 | 最多 300 |

取得內部部署管理主控台之後，請移至適用于 **IoT** 內部  >  **部署管理主控台**  >  **iso 安裝** 的 Defender 以下載 ISO。

:::image type="content" source="media/how-to-prepare-your-network/azure-defender-for-iot-iso-download-screen.png" alt-text="內部部署管理主控台。":::

## <a name="appliance-specifications"></a>設備規格

本節說明下列設備的硬體規格：

- 公司部署： HPE ProLiant DL360

- 企業部署： HPE ProLiant DL20

- SMB 部署： HPE ProLiant DL20

- 虛擬裝置規格

## <a name="corporate-deployment-hpe-proliant-dl360"></a>公司部署： HPE ProLiant DL360

| 元件 | 技術規格 |
|--|--|
| 底座 | 1U 機架伺服器 |
| 維度 | 42.9 x 43.46 x 70.7 (cm) /1.69 "x 17.11" x 27.83 " ()  |
| Weight | 最大16.27 公斤 (35.86 lb)  |
| 處理器 | Intel 以上的銀級 4215 R 3.2 GHz、>11m cache、8c/16T、130 W |
| 晶片組 | Intel C621 |
| 記憶體 | 32 GB = 2 x 16 GB 2666MT/s DDR4 ECC UDIMM |
| 儲存體 | 6 x 1.2-TB SAS 12G Enterprise 10K SFF (2.5 in) 的 Hot-Plug 硬碟-RAID 5 |
| 網路控制卡 | 在面板上： 2 x 1 Gb 的 Broadcom BCM5720<br>板載 LOM： iDRAC 埠卡 1-Gb 的 Broadcom BCM5720<br><br>External： 1 x Intel Ethernet i350 QP 1-Gb 的伺服器配接器，低設定檔 |
| 管理性 | HPE iLO Advanced |
| 裝置存取 | 兩個後置 USB 3。0<br>一個前端 USB 2。0<br>一個內部 USB 3。0 |
| 電源 | 2 x HPE 500 W 彈性位置白金級熱插即插低 Halogen 電源供應套件 |
| 機架支援 | HPE 1U Gen10 SFF Easy Install 鐵路套件 |

### <a name="appliance-bom"></a>設備 BOM

| Pn | 描述 | 數量 |
|--|--|--|
| P19766-B21 | HPE DL360 Gen10 8SFF NC CTO Server | 1 |
| P19766-B21 | 歐洲-多語系當地語系化 | 1 |
| P24479-L21 | 適用于 DL360 G10 的 Intel 4215 R FIO 套件 | 1 |
| P24479-B21 | 適用于 DL360 Gen10 的 Intel 4215 R 套件 R 套件 | 1 |
| P00922-B21 | HPE 16-GB 2Rx8 PC4-2933Y-R 智慧套件 | 2 |
| 872479-B21 | HPE 1.2-TB SAS 10K SFF SC DS HDD | 6 |
| 811546-B21 | HPE 1-GbE 4-p BASE-T I350 Adapter | 1 |
| P02377-B21 | HPE 智慧型混合式電容器 w \_ 145 Mm 纜線 | 1 |
| 804331-B21 | HPE 智慧型陣列 P408i-一個 SR Gen10 控制器 | 1 |
| 665240-B21 | HPE 1-GbE 4-p FLR-T I350 Adapter | 1 |
| 871244-B21 | HPE DL360 Gen10 High Performance 風機套件 | 1 |
| 865408-B21 | HPE 500-W FS 平臺熱外掛程式 LH 電源供應器套件 | 2 |
| 512485-B21 | HPE iLO 進階 1-伺服器授權1年支援 | 1 |
| 874543-B21 | HPE 1U Gen10 SFF Easy Install 鐵路套件 | 1 |

## <a name="enterprise-deployment-hpe-proliant-dl20"></a>企業部署： HPE ProLiant DL20

| 元件 | 技術規格 |
|--|--|
| 底座 | 1U 機架伺服器 |
| 維度 (高度 x 寬度 x 深度)  | 4.32 x 43.46 x 38.22 cm/1.70 x 17.11 x 15.05 英寸 |
| Weight | 7.9 公斤/17.41 lb |
| 處理器 | Intel 2234、3.6 GHz、4C/8T、71 W |
| 晶片組 | Intel C242 |
| 記憶體 | 2 x 16 GB 雙排名 x8 DDR4-2666 |
| 儲存體 | 3 x 1 TB SATA 6G 中線 7.2 K SFF (2.5 in) –具有智慧型陣列 P408i 的 RAID 5--SR 控制器 |
| 網路控制卡 | 板載： 2 x 1 Gb <br>板載： iLO 埠卡 1 Gb <br>External： 1 x HPE Ethernet 1-g 4-port 366FLR Adapter |
| 管理性 | HPE iLO Advanced |
| 裝置存取 | Front： 1 x USB 3.0，1 x USB iLO 服務埠 <br>後方： 2 x USB 3。0 <br>內部： 1 x USB 3。0 |
| 電源 | 雙熱插即用電源供應器 500 W |
| 機架支援 | HPE 1U 的短暫摩擦軌套件 |

### <a name="appliance-bom"></a>設備 BOM

| Pn | 描述：高階端 | 數量 |
|--|--|--|
| P06963-B21 | HPE DL20 Gen10 4SFF CTO Server | 1 |
| P06963-B21 | HPE DL20 Gen10 4SFF CTO Server | 1 |
| P17104-L21 | HPE DL20 Gen10 E-2234 FIO 套件 | 1 |
| 879507-B21 | HPE 16-GB 2Rx8 PC4-2666V-E STND 套件 | 2 |
| 655710-B21 | HPE 1-TB SATA 7.2 K SFF SC DS HDD | 3 |
| P06667-B21 | HPE DL20 Gen10 x8x16 FLOM Riser 套件 | 1 |
| 665240-B21 | HPE Ethernet 1-g 4-port 366FLR Adapter | 1 |
| 782961-B21 | HPE 12-W 智慧型存放裝置電池 | 1 |
| 869081-B21 | HPE 智慧型陣列 P408i-SR G10 LH 控制器 | 1 |
| 865408-B21 | HPE 500-W FS 平臺熱外掛程式 LH 電源供應器套件 | 2 |
| 512485-B21 | HPE iLO 進階 1-伺服器授權1年支援 | 1 |
| P06722-B21 | HPE DL20 Gen10 RPS 啟用 FIO 套件 | 1 |
| 775612-B21 | HPE 1U 的短暫摩擦軌套件 | 1 |

## <a name="smb-deployment-hpe-proliant-dl20"></a>SMB 部署： HPE ProLiant DL20

| 元件 | 技術規格 |
|--|--|
| 底座 | 1U 機架伺服器 |
| 維度 (高度 x 寬度 x 深度)  | 4.32 x 43.46 x 38.22 cm/1.70 x 17.11 x 15.05 英寸 |
| Weight | 7.88 公斤/17.37 lb |
| 處理器 | Intel 2224、3.4 GHz、4C、71 W |
| 晶片組 | Intel C242 |
| 記憶體 | 1 x 8 GB 雙排名 x8 DDR4-2666 |
| 儲存體 | 2 x 1 TB SATA 6G 中線 7.2 K SFF (2.5 in) –具有智慧型陣列 P208i 的 RAID 1-a |
| 網路控制卡 | 板載： 2 x 1 Gb <br>板載： iLO 埠卡 1 Gb <br>External： 1 x HPE Ethernet 1-g 4-port 366FLR Adapter |
| 管理性 | HPE iLO Advanced |
| 裝置存取 | Front： 1 x USB 3.0，1 x USB iLO 服務埠 <br>後方： 2 x USB 3。0 <br>內部： 1 x USB 3。0 |
| 電源 | 熱插即用電源供應器 290 W |
| 機架支援 | HPE 1U 的短暫摩擦軌套件 |

### <a name="appliance-bom"></a>設備 BOM

| Pn | 描述 | 數量 |
|--|--|--|
| P06961-B21 | HPE DL20 Gen10 NHP 2LFF CTO Server | 1 |
| P06961-B21 | HPE DL20 Gen10 NHP 2LFF CTO Server | 1 |
| P17102-L21 | HPE DL20 Gen10 E-2224 FIO 套件 | 1 |
| 879505-B21 | HPE 8-GB 1Rx8 PC4-2666V-E STND 套件 | 1 |
| 801882-B21 | HPE 1-TB SATA 7.2 K LFF RW HDD | 2 |
| P06667-B21 | HPE DL20 Gen10 x8x16 FLOM Riser 套件 | 1 |
| 665240-B21 | HPE Ethernet 1-g 4-port 366FLR Adapter | 1 |
| 869079-B21 | HPE 智慧型陣列 E208i-SR G10 LH 控制器 | 1 |
| P21649-B21 | HPE DL20 Gen10 平臺 290 W FIO PSU 套件 | 1 |
| P06683-B21 | HPE DL20 Gen10 M. 2 SATA/LFF AROC 纜線套件 | 1 |
| 512485-B21 | HPE iLO 進階 1-伺服器授權1年支援 | 1 |
| 775612-B21 | HPE 1U 的短暫摩擦軌套件 | 1 |

## <a name="virtual-appliance-specifications"></a>虛擬裝置規格

### <a name="sensors"></a>感應器

| 類型 | 公司 | Enterprise | SMB |
|--|--|--|--|
| vCPU | 32 | 8 | 4 |
| 記憶體 | 32 GB | 32 GB | 8 GB |
| 儲存體 | 5.6 TB | 1.8 TB | 500 GB |

### <a name="on-premises-management-console-appliance"></a>內部部署管理主控台設備

| 類型 | Enterprise |
|--|--|
| 描述 | 適用于企業部署類型的虛擬裝置 |
| vCPU | 8 |
| 記憶體 | 32 GB |
| 儲存體 | 1.8 TB |

支援的虛擬機器： VMware ESXi 5.0 版和更新版本、Hyper-v

## <a name="additional-certified-appliances"></a>其他認證的設備

本節將詳細說明 Microsoft 認證但未提供為預先設定設備的其他設備。

| 部署類型 | Enterprise |
|--|--|
| 映像 | :::image type="content" source="media/how-to-prepare-your-network/deployment-type-enterprise-for-azure-defender-for-iot-v2.png" alt-text="企業部署類型。"::: |
| 型號 | Dell PowerEdge R340 XL |
| 監視埠 | 最多9個 RJ45 或六個選擇 |
| 最大頻寬 [1](#anchortext2)| 1 Gb/秒 |
| 受保護的裝置數上限 | 10,000 |

<a id="anchortext2">一個</a> 頻寬容量可能會有所不同，取決於通訊協定的散發。

購買設備之後，請移至 **適用于 IoT**  >  **網路感應器 ISO**  >  **安裝** 的 Defender 以下載軟體。

:::image type="content" source="media/how-to-prepare-your-network/azure-defender-for-iot-sensor-download-software-screen.png" alt-text="網路感應器 ISO。":::

## <a name="enterprise-deployment-dell-poweredge-r340-xl"></a>企業部署： Dell PowerEdge R340 XL

| 元件 | 技術規格 |
|--|--|
| 底座 | 1U 機架伺服器 |
| 維度 | 42.8 x 434.0 x 596 (mm) /1.67 "x 17.09" x 23.5 " ()  |
| Weight | 最大 29.98 lb/13.6 公斤 |
| 處理器 | Intel 2144G 3.6 GHz、>8m cache、4C/8T、turbo (71 W)  |
| 晶片組 | Intel C246 |
| 記憶體 | 32 GB = 2 x 16 GB 2666MT/s DDR4 ECC UDIMM |
| 儲存體 | 3 x 2 TB 7.2 K RPM SATA 6-Gbps 512n 3.5-in Hot-Plug 硬碟-RAID 5 |
| 網路控制卡 | 在面板上： 2 x 1 Gb 的 Broadcom BCM5720<br>板載 LOM： iDRAC 埠卡 1-Gb 的 Broadcom BCM5720 <br><br>External： 1 x Intel Ethernet i350 QP 1-Gb 的伺服器配接器，低設定檔 |
| 管理性 | iDRAC 九企業版 |
| 裝置存取 | 兩個後置 USB 3。0 <br> 一個前端 USB 3。0 |
| 電源 | 雙熱插即用電源供應器 350 W |
| 機架支援 | ReadyRails II 滑滑軌，可在4個 post 機架中使用正方形或 unthreaded 圓形孔或工具式掛接的4個後置機架，並支援選擇性無工具纜線管理 arm。 |

## <a name="dell-r340-bom"></a>Dell R340 BOM

:::image type="content" source="media/how-to-prepare-your-network/enterprise-deployment-for-azure-defender-for-iot-dell-r340-bom.png" alt-text="Dell R340 BOM。":::

## <a name="next-steps"></a>後續步驟

[關於適用于 IoT 的 Azure Defender 安裝](how-to-install-software.md)

[關於適用於 IoT 的 Azure Defender 網路設定](how-to-set-up-your-network.md)
