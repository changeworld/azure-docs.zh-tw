---
title: 如何在 SAP HANA on Azure (大型執行個體) 上安裝 HANA | Microsoft Docs
description: 如何在 SAP HANA on Azure (大型執行個體) 上安裝 HANA。
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: hermannd
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 023f32fce01ffbd974b182fa89fd604e62332936
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/26/2020
ms.locfileid: "77617216"
---
# <a name="install-hana-on-sap-hana-on-azure-large-instances"></a>在 SAP HANA on Azure (大型執行個體) 上安裝 HANA

若要在 SAP HANA on Azure (大型執行個體) 上安裝 HANA，您必須先執行下列作業：
- 向 Microsoft 提供所有資料以部署在 SAP HANA 大型執行個體上。
- 從 Microsoft 收到 SAP HANA 大型執行個體。
- 建立連線至內部部署網路的 Azure 虛擬網路。
- 將 HANA 大型執行個體的 ExpressRotue 線路連線到相同的 Azure 虛擬網路。
- 安裝 Azure 虛擬機器以作為 HANA 大型執行個體的跳躍方塊。
- 確定您可以從跳躍方塊連線到 HANA 大型執行個體單位，反之亦然。
- 確認是否已安裝所有必要的套件及修補程式。
- 閱讀在所用作業系統上安裝 HANA 的相關 SAP 附註和文件。 確定作業系統版本支援所選擇的 HANA 版本。

下一節會舉例說明如何將 HANA 安裝套件下載到跳躍方塊虛擬機器。 在此案例中，作業系統是 Windows。

## <a name="download-the-sap-hana-installation-bits"></a>下載 SAP HANA 安裝位元
HANA 大型執行個體單位不會直接連線到網際網路。 您無法直接從 SAP 將安裝套件下載到 HANA 大型執行個體虛擬機器。 相反地，您必須將套件下載到跳躍方塊虛擬機器。

您需要 SAP S-user 或其他使用者，以便能夠存取 SAP Marketplace。

1. 登入並移至 [SAP Service Marketplace](https://support.sap.com/en/index.html)。 選取 [下載軟體] > [安裝和升級] > [依字母順序排列索引]。 然後選取 [在 H – SAP HANA 平台版本下] > [SAP HANA 平台版本 2.0] > [安裝]。 下載下列螢幕擷取畫面所示的檔案。

   ![螢幕擷取畫面：要下載的檔案](./media/hana-installation/image16_download_hana.PNG)

2. 在此範例中，我們下載了 SAP HANA 2.0 安裝套件。 在 Azure 跳躍方塊虛擬機器中，將自我解壓縮的封存展開到目錄中，如下所示。

   ![螢幕擷取畫面：將封存自我解壓縮](./media/hana-installation/image17_extract_hana.PNG)

3. 當封存解壓縮後，複製解壓縮作業所建立的目錄 (在本例中為 51052030)。 從 HANA 大型執行個體單位的 /hana/shared 磁碟區，將目錄複製到您所建立的目錄中。

   > [!Important]
   > 安裝套件「請勿」複製到根或開機 LUN，因為空間有限，而且必須供其他處理序使用。


## <a name="install-sap-hana-on-the-hana-large-instance-unit"></a>在 HANA 大型執行個體單位上安裝 SAP HANA
若要安裝 SAP HANA，請以根使用者身分登入。 只有根才有足夠的權限安裝 SAP HANA。 在複製到 /hana/shared 的目錄上設定權限。

```
chmod –R 744 <Installation bits folder>
```

如果您想要使用圖形化使用者介面安裝程式來安裝 SAP HANA，則需要將 gtk2 套件安裝在 HANA 大型執行個體上。 若要檢查是否已安裝此套件，請執行下列命令：

```
rpm –qa | grep gtk2
```

(在後續步驟中，我們會示範使用圖形化使用者介面安裝 SAP HANA 的過程)。

移至安裝目錄並瀏覽至子目錄 HDB_LCM_LINUX_X86_64。 

從該目錄啟動：

```
./hdblcmgui 
```
此時，您會經歷一連串的畫面，請在其中提供資料來進行安裝。 在此範例中，我們要安裝 SAP HANA 資料庫伺服器和 SAP HANA 用戶端元件。 因此我們選取 [SAP HANA 資料庫]。

![螢幕擷取畫面：SAP HANA 生命週期管理畫面 (已選取 [SAP HANA 資料庫])](./media/hana-installation/image18_hana_selection.PNG)

在下一個畫面上，選取 [安裝新系統]。

![螢幕擷取畫面：SAP HANA 生命週期管理畫面 (已選取 [安裝新系統])](./media/hana-installation/image19_select_new.PNG)

接下來，選取其他數個可以安裝的元件。

![螢幕擷取畫面：SAP HANA 生命週期管理畫面 (含其他元件的清單)](./media/hana-installation/image20_select_components.PNG)

在此，我們選擇 SAP HANA 用戶端和 SAP HANA Studio。 我們也會安裝相應增加執行個體。 然後，選擇 [單一主機系統]。 

![螢幕擷取畫面：SAP HANA 生命週期管理畫面 (已選取 [單一主機系統])](./media/hana-installation/image21_single_host.PNG)

接下來，提供一些資料。

![螢幕擷取畫面：SAP HANA 生命週期管理畫面 (以及要定義的系統屬性欄位)](./media/hana-installation/image22_provide_sid.PNG)

> [!Important]
> 您需要提供在訂購 HANA 大型執行個體部署時提供給 Microsoft 的相同 SID，當作 HANA 系統識別碼 (SID)。 選擇不同的 SID，安裝會因為不同磁碟區上的存取權限問題而失敗。

使用 /hana/shared 目錄作為安裝路徑。 在下一個步驟中，請提供 HANA 資料檔和 HANA 記錄檔的位置。


![螢幕擷取畫面：SAP HANA 生命週期管理畫面 (含資料與記錄區域欄位)](./media/hana-installation/image23_provide_log.PNG)

> [!Note]
> 定義系統屬性時 (兩個畫面前) 所指定的 SID 應符合掛接點的 SID。 如果不相符，請返回並調整 SID，使其與掛接點上的值相合。

在下一個步驟中，檢閱主機名稱並加以更正。 

![螢幕擷取畫面：SAP HANA 生命週期管理畫面 (含主機名稱)](./media/hana-installation/image24_review_host_name.PNG)

在下一個步驟中，您也需要擷取在訂購 HANA 大型執行個體部署時提供給 Microsoft 的資料。 

![螢幕擷取畫面：SAP HANA 生命週期管理 (以及要定義的系統管理員欄位)](./media/hana-installation/image25_provide_guid.PNG)

> [!Important]
> 請提供在訂購單位部署時提供給 Microsoft 的相同 [系統管理員使用者識別碼] 和 [使用者群組識別碼]。 否則，在 HANA 大型執行個體單位上安裝 SAP HANA 就會失敗。

接下來的兩個畫面不會顯示於此。 這兩個畫面可讓您提供 SAP HANA 資料庫的 SYSTEM 使用者密碼，以及 sapadm 使用者的密碼。 後者會作為 SAP 主機代理程式來安裝為 SAP HANA 資料庫執行個體的一部分。

在定義密碼後，您會看到確認畫面。 請檢查所有列出的資料列，再繼續進行安裝。 您會到達記錄安裝進度的進度畫面，類似下面這個：

![螢幕擷取畫面：SAP HANA 生命週期管理畫面 (含安裝進度列指示器)](./media/hana-installation/image27_show_progress.PNG)

當安裝完成後，您應該會看到類似下圖的畫面：

![螢幕擷取畫面：SAP HANA 生命週期管理畫面 (指出安裝已完成)](./media/hana-installation/image28_install_finished.PNG)

SAP HANA 執行個體現在應該會啟動並執行，且隨時可供使用。 您應該能夠從 SAP HANA Studio 連接到它。 也請務必檢查並套用最新的更新。


## <a name="next-steps"></a>後續步驟

- [Azure 上的 SAP HANA (大型執行個體) 高可用性和災害復原](hana-overview-high-availability-disaster-recovery.md)

