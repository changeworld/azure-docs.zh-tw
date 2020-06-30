---
title: Microsoft Azure 資料箱閘道概觀 | Microsoft Docs
description: 描述 Azure 資料箱閘道，這是可讓您將資料傳輸到 Azure 的虛擬設備儲存體解決方案
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: overview
ms.date: 08/21/2019
ms.author: alkohli
ms.openlocfilehash: aa68b746ef5c22c97fcfdcca889bf71577c19479
ms.sourcegitcommit: 51718f41d36192b9722e278237617f01da1b9b4e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/19/2020
ms.locfileid: "85100761"
---
# <a name="what-is-azure-data-box-gateway"></a>什麼是 Azure Data Box Gateway？

Azure 資料箱閘道是可讓您順利將資料傳送到 Azure 的儲存體解決方案。 此文章為您提供的概觀涵蓋 Azure 資料箱閘道解決方案、優點、主要功能，以及您可以部署此裝置的案例。

資料箱閘道是虛擬裝置，其基礎是佈建在虛化環境中或 Hypervisor 中的虛擬機器。 虛擬裝置位在您的內部部署環境中，且您使用 NFS 與 SMB 通訊協定將資料寫入其中。 裝置接著將您的資料傳輸到 Azure 區塊 Blob、分頁 Blob 或 Azure 檔案。

## <a name="use-cases"></a>使用案例

需要將資料傳輸到雲端 (以用於雲端封存與災害復原) 或有需要以雲端規模來處理您的資料時，可以使用資料箱閘道。 以下是資料箱閘道可用於資料傳輸的各種案例。

- **雲端封存** - 使用資料箱閘道以安全且有效率的方式將數百 TB 的資料複製到 Azure 儲存體。 針對封存的案例，資料可以一次內嵌或以持續的方式內嵌。

- **連續資料擷取** - 持續將資料嵌入裝置，然後複製到雲端，而且不論資料大小為何。 資料寫入至閘道裝置後，裝置就會將資料上傳至 Azure 儲存體。  

- **初始大量傳輸後接著增量傳輸** - 以離線模式使用資料箱來進行大量傳輸 (初始植入)，然後使用 Data Box Gateway 透過網路進行增量傳輸 (持續饋送)。

如需詳細資訊，請移至 [Azure 資料箱閘道使用案例](data-box-gateway-use-cases.md)。

## <a name="benefits"></a>優點

資料箱閘道具有下列優點：

- **資料傳輸容易** - 使將資料移入及移出 Azure 儲存體和使用區域網路共用一樣容易。  
- **高效能** - 免於網路資料傳輸的麻煩，傳入和傳出 Azure 都具有高效能。
- **營業時間內的快速存取和高資料擷取速率** - 資料箱閘道具有您在佈建虛擬裝置時定義為本機容量大小的本機快取。 資料磁碟大小應依據[虛擬裝置最低需求](data-box-gateway-system-requirements.md#specifications-for-the-virtual-device)指定。 本機快取可提供下列優點：
    - 本機快取允許高速率的資料擷取。 在尖峰營業時間內擷取大量資料時，快取可以保存資料並將它上傳至雲端。
    - 本機快取允許特定臨界值內的快速讀取存取。 在達到裝置容量的 50-60% 之前，來自裝置的所有讀取都是從使其變快速的快取進行存取。 一旦裝置上已用的空間高於此臨界值，裝置就會開始移除本機檔案。
 
- **限制頻寬使用量** - 當系統在尖峰營運時段對網路進行節流以限制使用量時，資料也可以寫入至 Azure。  

## <a name="key-capabilities"></a>主要功能

資料箱閘道具有下列功能：

|功能 |描述  |
|---------|---------|
|速度     | 完全自動化且高度最佳化的資料傳輸與頻寬。|
|支援的通訊協定     | 支援用於資料擷取的標準 SMB 與 NFS 通訊協定。 <br> 如需支援版本的詳細資訊，請移至[資料箱閘道系統需求](data-box-gateway-system-requirements.md)。|
|資料存取     | 裝置所傳送的資料位於雲端後，直接存取雲端 API 即可進一步修改資料。|
|快速存取     | 裝置上的本機快取可讓您快速存取最近使用的檔案。|
|離線上傳     | 中斷連線模式支援離線上傳案例。|
|資料重新整理     | 能夠以來自雲端的最新檔案對本機檔案進行重新整理。|
|災害復原     | 內建的網路災害復原        |


## <a name="specifications"></a>規格

資料箱閘道虛擬裝置具有下列規格：

| 規格                                          | 描述              |
|---------------------------------------------------------|--------------------------|
| 虛擬處理器 (核心)   | 最少 4 個 |
| 記憶體  |最少 8 GB|
| 可用性|單一節點|
| 磁碟|OS 磁碟：250 GB <br> 資料磁碟：最少 2 TB、精簡佈建且必須以 SSD 為後盾|
| 網路介面 |1 或多個虛擬網路介面|
| 原生的檔案共用通訊協定|SMB 和 NFS  |
| 安全性|驗證以解除存取裝置和資料的鎖定 <br> 資料傳輸使用 AES-256 位元加密|
| 管理性|本機 Web UI - 初始設定、診斷及裝置電源管理 <br> Azure 入口網站 - 資料箱閘道裝置的日常管理工作       |

## <a name="components"></a>元件

資料箱閘道解決方案的組成包括資料箱閘道資源、資料箱閘道虛擬裝置和本機 Web UI。

- **資料箱閘道虛擬裝置** - 以您的虛擬化環境或 Hypervisor 中所佈建之虛擬機器為基礎的裝置，可讓您將資料傳送至 Azure。
    
- **資料箱閘道資源** - Azure 入口網站中的資源，可讓您從 Web 介面管理資料箱閘道裝置，且您可以從不同地理位置存取該介面。 使用 Data Box Gateway 資源來檢視及管理資源、共用、使用者和警示。 如需詳細資訊，請參閱如何[使用 Azure 入口網站進行管理](data-box-gateway-manage-shares.md)。

- **資料箱本機 Web UI** - 使用本機 Web UI 來執行診斷、關閉並重新啟動狀、產生支援套件，或連絡 Microsoft 支援服務以提出服務要求。 如需詳細資訊，請參閱如何[使用本機 Web UI 進行管理](data-box-gateway-manage-access-power-connectivity-mode.md)。

## <a name="region-availability"></a>區域可用性

Data Box Gateway 實體裝置、Azure 資源，以及您要將資料轉送至其中的目標儲存體帳戶，並不需要全都位於相同的區域。

- **資源可用性** - 如需 Azure 資料箱閘道資源適用區域的完整清單，請移至[依區域提供的 Azure 產品](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=databox)。 Data Box Gateway 也可部署在 Azure Government 雲端中。 如需詳細資訊，請參閱[何謂 Azure Government？](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)。

- **目的地儲存體帳戶** - 儲存資料的儲存體帳戶在所有 Azure 區域都可用。

    若要獲得最佳效能，儲存資料箱資料的儲存體帳戶應該要位在接近裝置所在區域的區域。 儲存體帳戶的位置若離裝置遙遠，將會導致較長的延遲時間與較緩慢的效能。


## <a name="next-steps"></a>後續步驟

- 檢閱[資料箱閘道的系統需求](data-box-gateway-system-requirements.md)。
- 了解[資料箱閘道的限制](data-box-gateway-limits.md)。
- 在 Azure 入口網站中部署 [Azure 資料箱閘道](data-box-gateway-deploy-prep.md)。

