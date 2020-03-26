---
title: Microsoft Azure Data Box Heavy 概觀 | Microsoft Docs
description: 說明 Azure 資料箱，這是可讓您將大量資料傳輸至 Azure 的混合式解決方案
services: databox
documentationcenter: NA
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: overview
ms.date: 08/28/2019
ms.author: alkohli
ms.openlocfilehash: 362b7457538a16d389c3cc40fc44da19b073c0b0
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "70142921"
---
# <a name="what-is-azure-data-box-heavy"></a>什麼是 Azure Data Box Heavy？

Azure Data Box Heavy 可讓您以快速、實惠和可靠的方式，將數百 TB 的資料寄送到 Azure。 您可以將資料儲存在具備 1-PB 儲存容量的 Data Box Heavy 裝置中，然後將裝置寄回給 Microsoft，就能將資料轉送至 Azure。 該裝置具有堅固的外殼，可在運輸過程中保護裝置與資料安全。

當資料中心收到裝置之後，請您使用本機 Web UI 來設定它。 將您伺服器的資料複製到裝置上，然後將裝置寄回給 Azure。 在 Azure 資料中心內，您的資料會自動上傳到您的 Azure 儲存體帳戶。 您可以在 Azure 入口網站追蹤整個端對端程序。


> [!IMPORTANT]
> - 若要申請裝置，請在 [Azure 入口網站](https://portal.azure.com)中註冊。


## <a name="use-cases"></a>使用案例

Data Box Heavy 最適合資料大小達到數百個 TB，而網路連線能力不足以將資料上傳至 Azure 的案例採用。 資料移動可以是單次或定期的，或一開始是大量資料傳輸，然後繼之以定期傳輸。 以下是適合將 Data Box Heavy 用於資料傳輸的各種案例。

 - **單次移轉** - 有大量的內部部署資料移至 Azure 時。
     - 將媒體櫃從離線磁帶移至 Azure 以建立線上媒體櫃。
     - 將 VM 陣列、SQL 伺服器與應用程式移轉到 Azure。
     - 將歷程記錄資料移至 Azure 以使用 HDInsight 進行深入分析和報告。

 - **初始大量傳輸** - 使用 Data Box Heavy (種子) 完成初始大量傳輸，然後透過網路進行累加式傳輸。
     - 例如，使用 Data Box Heavy 與備份解決方案合作夥伴，將初始的大量歷程記錄備份移到 Azure。 完成後，繼續透過網路將累加資料傳輸到 Azure 儲存體。

 - **定期上傳** - 定期產生大量資料而需要移至 Azure 時。 例如，在進行能源探勘時，鑽井平台和風車農場會產生視訊內容。

## <a name="benefits"></a>優點

Data Box Heavy 的設計可讓它在幾乎不對網路產生影響的情況下，將大量資料移至 Azure。 此解決方案有下列優點：

- **速度** - Data Box Heavy 使用的是高效能的 40 Gbps 網路介面。

- **安全性** - Data Box Heavy 具有適用於裝置、資料與服務的內建安全性保護機制。
    - 裝置有堅固的裝置外殼，並以防拆螺絲和易碎貼紙加以保護。
    - 裝置上的資料會全程受到 AES 256 位元加密的保護。
    - 裝置只能使用 Azure 入口網站中提供的密碼來解除鎖定。
    - 此服務受到 Azure 安全功能的保護。
    - 在您的資料上傳至 Azure 後，裝置上的磁碟即會根據美國國家標準與技術局 (NIST) 800-88r1 標準進行抹除。


## <a name="features-and-specifications"></a>功能和規格

此版本的 Data Box Heavy 裝置具備下列功能。

| 規格                                          | 描述              |
|---------------------------------------------------------|--------------------------|
| Weight                                                  | ~ 500 磅。 <br>用於傳輸的鎖定輪上的裝置|
| 維度                                              | 寬度：26 英吋高：28 英吋長：48 英吋 |
| 機架空間                                              | 不能安裝在機架上|
| 必要纜線                                         | 內含 4 條接地 120 V/10 A 電源線 (NEMA 5-15) <br> 裝置支援最多 240 V 電源，並配備 C-13 電源箱 <br> 使用與 [Mellanox MCX314A BCCT](https://store.mellanox.com/products/mellanox-mcx314a-bcct-connectx-3-pro-en-network-interface-card-40-56gbe-dual-port-qsfp-pcie3-0-x8-8gt-s-rohs-r6.html) \(英文\) 相容的網路纜線  |
| Power                                                    | 跨兩個裝置節點共用的 4 個內建電源供應器單位 (PSU) <br> 1,200 瓦特一般電力消耗|
| 儲存體容量                                        | ~ 1-PB 未經處理、70 個 14 TB 磁碟 <br> 770-TB 可用容量|
| 節點數目                                          | 每個裝置 2 個獨立的節點 (各 500 TB) |
| 每個節點的網路介面                             | 每個節點 4 個網路介面 <br><br> MGMT, DATA3 <ul><li> 2 個 1-GbE 介面 </li><li> MGMT 用於管理和初始設定，使用者無法設定 </li><li> DATA3 是使用者可設定的，預設使用動態主機設定通訊協定 (DHCP)</li></ul>DATA1、DATA2 資料介面 <ul><li>2 個 40-GbE 介面 </li><li> 使用者可設定的 DHCP (預設) 或靜態</li></ul>|


## <a name="components"></a>元件

Data Box Heavy 包括下列元件：

* **Data Box Heavy 裝置** - 具有堅固外殼並能安全儲存資料的實體裝置。 此裝置的可用儲存體容量為 770 TB。
    
* **資料箱服務** - Azure 入口網站的延伸模組，可讓您從不同的地理位置存取 Web 介面來管理 Data Box Heavy 裝置。 請使用資料箱服務管理 Data Box Heavy 裝置。 服務工作包括如何建立和管理訂單、檢視及管理警示，以及管理共用。  

* **本機 Web 使用者介面** - 此 Web 型 UI 可用來設定裝置以使其可以連線到區域網路，然後再向資料箱服務註冊該裝置。 您也可以使用本機 Web UI 來將裝置關機及重新啟動、檢視複製記錄，以及連絡 Microsoft 支援服務以提出服務要求。


## <a name="the-workflow"></a>工作流程

典型的流程包含下列步驟：

1. **訂購** - 在 Azure 入口網站中建立訂單，提供運送資訊，以及資料的目的地 Azure 儲存體帳戶。 如果裝置可供使用，Azure 便會準備並寄出裝置，並附上寄送追蹤識別碼。

2. **接收** - 在裝置成功遞送之後，請使用指定的纜線將裝置接上網路及電源。 開啟電源並連線到裝置。 設定裝置網路，並在您想要複製資料的主機電腦上掛接共用。

3. **複製資料** - 將資料複製到 Data Box Heavy 共用。

4. **寄回** - 準備好裝置並將它關機，然後將裝置寄回 Azure 資料中心。

5. **上傳** - 資料會自動從裝置複製到 Azure。 裝置磁碟會根據美國國家標準與技術局 (National Institute of Standards and Technology，NIST) 的指導方針安全地抹除。

在這整個程序中，您將會透過電子郵件獲知所有狀態變更。

## <a name="region-availability"></a>區域可用性

Data Box Heavy 可以根據其服務所部署的區域、裝置所寄送的國家/地區，以及您將資料傳輸至其中的目標 Azure 儲存體帳戶來傳輸資料。

- **服務可用性** - 針對此版本，下列區域提供 Data Box Heavy：
    - 位於美國的所有公用雲端區域 - 美國中西部、美國西部 2、美國西部、美國中南部、美國中部、美國中北部、美國東部，以及美國東部 2。
    - 歐盟 - 西歐與北歐。
    - 英國 - 英國南部與英國西部。
    - 法國 - 法國中部與法國南部。

- **目的地儲存體帳戶** - 儲存資料的儲存體帳戶在服務可用的所有 Azure 區域都可用。

如需 Data Box Heavy 區域可用性的最新資訊，請移至[依區域提供的 Azure 產品](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all) \(英文\)。

## <a name="sign-up"></a>註冊

請採取下列步驟以註冊 Data Box Heavy︰

1. 登入 Azure 入口網站： https://portal.azure.com 。
2. 按一下 [+ 建立資源]  以建立新的資源。 搜尋 **Azure 資料箱**。 選取 [Azure 資料箱]  服務。
3. 按一下頁面底部的 [新增]  。
4. 挑選您要用於 Data Box Heavy 的訂用帳戶。 選取您要部署 Data Box Heavy 資源的區域。 在 [Data Box Heavy]  選項中，按一下 [註冊]  。
5. 回答關於資料所在國家/地區、時間範圍、資料轉送的目標 Azure 服務、網路頻寬，以及資料傳輸頻率的問題。 檢閱 [隱私權與條款]，然後選取 [Microsoft 可以使用您的電子郵件地址連絡您] 核取方塊。

完成註冊後，您即可訂購 Data Box Heavy。

    
