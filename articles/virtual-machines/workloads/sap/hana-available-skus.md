---
title: SAP HANA on Azure (大型執行個體) 適用的 SKU | Microsoft Docs
description: SAP HANA on Azure (大型執行個體) 適用的 SKU。
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/10/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d7a487b105c8edc34d9427de3b8ca6738da1855a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84672097"
---
# <a name="available-skus-for-hli"></a>HLI 可用的 SKU

僅以修訂版3戳記為基礎的 SAP Hana on Azure （大型實例）服務，可在 Azure 區域中的數個設定中使用：

- 澳大利亞東部
- 澳大利亞東南部
- 日本東部
- 日本西部

以修訂4戳記為基礎的 SAP Hana on Azure （大型實例）服務可在的 Azure 區域中的數個設定中取得：

- 美國西部 2
- 美國東部
- 美國東部 2
- 美國中南部
- 西歐
- 北歐



[HANA 大型執行個體的 SAP Hana 認證 SKU](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) 清單如下：

| SAP 解決方案 | 型號 | Memory | 儲存體 | 可用性 |
| --- | --- | --- | --- | --- |
| [OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2185)、 [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2265) | SAP HANA on Azure S96<br /> – 2 x Intel®的®處理器 E7-8890 v4 <br /> 48 個 CPU 核心和 96 個 CPU 執行緒 |  768 GB |  3.0 TB | 可用 |
| [OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2186)、 [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2269) | Azure S224 上的 SAP Hana<br /> – 4 x Intel®，®白金8276處理器 <br /> 112 CPU 核心和 224 CPU 執行緒 |  3.0 TB |  6.3 TB | 可用 |
| [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2297) | Azure S224m 上的 SAP Hana<br /> – 4 x Intel®，®白金8276處理器 <br /> 112 CPU 核心和 224 CPU 執行緒 |  6.0 TB |  10.5 TB | 可用 |
| [OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=1983)、 [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2268) | SAP HANA on Azure S384<br /> – 8 x Intel® Xeon® Processor E7-8890 v4<br /> 192 個 CPU 核心和 384 個 CPU 執行緒 |  4.0 TB |  16 TB | 可用 |
|[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2080)| SAP HANA on Azure S384xm<br /> – 8 x Intel® Xeon® Processor E7-8890 v4<br /> 192 個 CPU 核心和 384 個 CPU 執行緒 |  6.0 TB |  18 TB | 可用（僅限修訂4） |
| TDIv5 | SAP HANA on Azure S384m<br /> – 8 x Intel® Xeon® Processor E7-8890 v4<br /> 192 個 CPU 核心和 384 個 CPU 執行緒 |  12.0 TB |  28 TB | 可用 |
|[OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=1984)、 [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2267) | SAP HANA on Azure S384xm<br /> – 8 x Intel® Xeon® Processor E7-8890 v4<br /> 192 個 CPU 核心和 384 個 CPU 執行緒 |  8.0 TB |  22 TB |  可用  |
| [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2049) | SAP HANA on Azure S576m<br /> – 12 x Intel® Xeon® Processor E7-8890 v4<br /> 288 個 CPU 核心和 576 個 CPU 執行緒 |  12.0 TB |  28 TB | 可用（僅限修訂4） |
| TDIv5 | SAP HANA on Azure S576xm<br /> – 12 x Intel® Xeon® Processor E7-8890 v4<br /> 288 個 CPU 核心和 576 個 CPU 執行緒 |  18.0 TB |  41 TB | 可用 |
| [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=1985) | SAP HANA on Azure S768m<br /> – 16 x Intel® Xeon® Processor E7-8890 v4<br /> 384 個 CPU 核心和 768 個 CPU 執行緒 |  16.0 TB |  36 TB | 可用（僅限修訂4） |
| TDIv5 | SAP HANA on Azure S768xm<br /> – 16 x Intel® Xeon® Processor E7-8890 v4<br /> 384 個 CPU 核心和 768 個 CPU 執行緒 |  24.0 TB |  56 TB | 可用 |
| [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=1986) | SAP HANA on Azure S960m<br /> – 20 x Intel® Xeon® Processor E7-8890 v4<br /> 480 個 CPU 核心和 960 個 CPU 執行緒 |  20.0 TB |  46 TB | 可用（僅限修訂4） |
| [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2328) | Azure S896m 上的 SAP Hana<br /> – 16 x Intel®，®白金8276處理器 <br /> 448 CPU 核心和 896 CPU 執行緒 | 24.0 TB | 35.8 TB | 可用（僅限修訂4） |

- CPU 核心 = 伺服器單位處理器總和的非超執行緒 CPU 核心總和。
- CPU 執行緒 = 伺服器單位處理器總和的超執行緒 CPU 核心所提供之計算執行緒的總和。 依預設會將大部分的單位設定為使用超執行緒技術。
- 根據供應商建議，S768m、S768xm 和 S960m 並未設定可使用超執行緒來執行 SAP Hana。


在 SAP Hana TDIv5 下，SAP 允許客戶專屬的調整大小和客戶特定的專案，這可能會導致伺服器設定，而不會在中列示為「已認證」：

- [SAP Hana 認證設備](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/appliances.html)
- [SAP Hana 認證的 IaaS 平臺](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)

在許多情況下，這些客戶專屬的伺服器設定會比使用 SAP 認證的伺服器單位具有更多的記憶體。 使用 SAP 時，客戶可能會取得 SAP 支援，並為其客戶專屬大小的伺服器設定進行認證。 

此外，下列大型實例標準 Sku （雖然尚未經過 SAP 認證）可供使用，並在 Microsoft 價目表中購買：

 | 型號 | 記憶體總數 | 記憶體 DRAM | 記憶體 Optane | 儲存體 | 可用性 |
| --- | --- | --- | --- | --- | --- |
| Azure S224oo 上的 SAP Hana<br /> – 4 x Intel®，®白金8276處理器 <br /> 112 CPU 核心和 224 CPU 執行緒 | 4.5 TB |  1.5 TB |  3.0 TB | 8.4 TB | 可用 |
| Azure S224om 上的 SAP Hana<br /> – 4 x Intel®，®白金8276處理器 <br /> 112 CPU 核心和 224 CPU 執行緒 | 6.0 TB |  3.0 TB |  3.0 TB | 10.5 TB | 可用 |
| Azure S224ooo 上的 SAP Hana<br /> – 4 x Intel®，®白金8276處理器 <br /> 112 CPU 核心和 224 CPU 執行緒 | 7.5 TB |  1.5 TB |  6.0 TB | 12.7 TB | 可用 |
| Azure S224oom 上的 SAP Hana<br /> – 4 x Intel®，®白金8276處理器 <br /> 112 CPU 核心和 224 CPU 執行緒 | 9.0 TB |  3.0 TB |  6.0 TB | 14.8 TB | 可用 |
| Azure S448 上的 SAP Hana<br /> – 8 x Intel®，®白金8276處理器 <br /> 224 CPU 核心和 448 CPU 執行緒 | 6.0 TB |  6.0 TB |  --- | 10.5 TB | 可用（僅限修訂4） |
| Azure S448m 上的 SAP Hana<br /> – 8 x Intel®，®白金8276處理器 <br /> 224 CPU 核心和 448 CPU 執行緒 | 12.0 TB |  12.0 TB |  --- | 18.9 TB | 可用（僅限修訂4） |
| Azure S448oo 上的 SAP Hana<br /> – 8 x Intel®，®白金8276處理器 <br /> 224 CPU 核心和 448 CPU 執行緒 | 9.0 TB |  3.0 TB |  6.0 TB | 14.8 TB  | 可用（僅限修訂4） |
| Azure S448om 上的 SAP Hana<br /> – 8 x Intel®，®白金8276處理器 <br /> 224 CPU 核心和 448 CPU 執行緒 | 12.0 TB |  6.0 TB |  6.0 TB | 18.9 TB  | 可用（僅限修訂4） |
| Azure S448ooo 上的 SAP Hana<br /> – 8 x Intel®，®白金8276處理器 <br /> 224 CPU 核心和 448 CPU 執行緒 | 15.0 TB |  3.0 TB |  12.0 TB | 23.2 TB  | 可用（僅限修訂4） |
| Azure S448oom 上的 SAP Hana<br /> – 8 x Intel®，®白金8276處理器 <br /> 224 CPU 核心和 448 CPU 執行緒 | 18.0 TB |  6.0 TB |  12.0 TB | 27.4 TB  | 可用（僅限修訂4） |
| Azure S672 上的 SAP Hana<br /> – 12 x Intel®，®白金8276處理器 <br /> 336 CPU 核心和 672 CPU 執行緒 | 9.0 TB |  9.0 TB |  --- | 14.7 TB | 可用（僅限修訂4） |
| Azure S672m 上的 SAP Hana<br /> – 12 x Intel®，®白金8276處理器 <br /> 336 CPU 核心和 672 CPU 執行緒 | 18.0 TB |  18.0 TB |  --- | 27.4 TB | 可用（僅限修訂4） |
| Azure S672oo 上的 SAP Hana<br /> – 12 x Intel®，®白金8276處理器 <br /> 336 CPU 核心和 672 CPU 執行緒 | 13.5 TB |  4.5 TB |  9.0 TB | 21.1 TB  | 可用（僅限修訂4） |
| Azure S672om 上的 SAP Hana<br /> – 12 x Intel®，®白金8276處理器 <br /> 336 CPU 核心和 672 CPU 執行緒 | 18.0 TB |  9.0 TB |  9.0 TB | 27.4 TB  | 可用（僅限修訂4） |
| Azure S672ooo 上的 SAP Hana<br /> – 12 x Intel®，®白金8276處理器 <br /> 336 CPU 核心和 672 CPU 執行緒 | 22.5 TB |  4.5 TB |  18.0 TB | 33.7 TB  | 可用（僅限修訂4） |
| Azure S672oom 上的 SAP Hana<br /> – 12 x Intel®，®白金8276處理器 <br /> 336 CPU 核心和 672 CPU 執行緒 | 27.0 TB |  9.0 TB |  18.0 TB | 40.0 TB  | 可用（僅限修訂4） |
| Azure S896 上的 SAP Hana<br /> – 16 x Intel®，®白金8276處理器 <br /> 448 CPU 核心和 896 CPU 執行緒 | 12.0 TB |  12.0 TB |  --- | 18.9 TB | 可用（僅限修訂4） |
| Azure S896oo 上的 SAP Hana<br /> – 16 x Intel®，®白金8276處理器 <br /> 448 CPU 核心和 896 CPU 執行緒 | 18.0 TB |  6.0 TB |  12.0 TB | 27.4 TB  | 可用（僅限修訂4） |
| Azure S896om 上的 SAP Hana<br /> – 16 x Intel®，®白金8276處理器 <br /> 448 CPU 核心和 896 CPU 執行緒 | 24.0 TB |  12.0 TB |  12.0 TB | 35.8 TB  | 可用（僅限修訂4） |
| Azure S896ooo 上的 SAP Hana<br /> – 16 x Intel®，®白金8276處理器 <br /> 448 CPU 核心和 896 CPU 執行緒 | 30.0 TB |  6.0 TB |  24.0 TB | 44.3 TB  | 可用（僅限修訂4） |
| Azure S896oom 上的 SAP Hana<br /> – 16 x Intel®，®白金8276處理器 <br /> 448 CPU 核心和 896 CPU 執行緒 | 36.0 TB |  12.0 TB |  24.0 TB | 52.7 TB  | 可用（僅限修訂4） |


> [!IMPORTANT]
> 雖然仍然支援下列 Sku，但無法再購買： S72、S72m、S144、S144m、S192 和 S192m 

選擇的特定組態取決於工作負載、CPU 資源及所需的記憶體。 OLTP 工作負載可以使用已針對 OLAP 工作負載最佳化的 SKU。 

供應項目的硬體基底 (除了客戶專屬的大小調整專案單位) 已通過 SAP Hana TDI 認證。 兩種不同類別的硬體會將 SKU 分成：

- S72、S72m、S96、S144、S144m、S192、S192m、S192xm、S224 和 S224m、S224oo、S224om、S224ooo、S224oom 稱為 Sku 的「類型 I 類別」。
- 所有其他 Sku 稱為 Sku 的「類型 II 類別」。
- 如果您對尚未列在 SAP 硬體目錄中的 Sku 感興趣，請洽詢您的 Microsoft 帳戶小組以取得詳細資訊。 


完整的 HANA 大型執行個體戳記並非僅配置給單一客戶使用。 這項事實也適用於透過 Azure 中所部署的網路網狀架構連線的計算與儲存資源的機架。 「HANA 大型執行個體」基礎結構和 Azure 一樣，會在下列三個層面部署彼此隔離的不同客戶&quot;租用戶&quot;：

- **網路**：透過 HANA 大型實例戳記內的虛擬網路隔離。
- **儲存體**：透過已指派存放磁片區並隔離租使用者間存放磁片區的儲存體虛擬機器來隔離。
- **計算**：將伺服器單位專用指派給單一租使用者。 不對伺服器單位進行硬體分割或軟體分割。 不在租用戶之間共用單一伺服器或主機單位。 

不同租用戶之間的 HANA 大型執行個體單位部署不會讓彼此看到。 部署在不同租用戶的 HANA 大型執行個體單位無法在 HANA 大型執行個體戳記層級上直接與其他單位進行通訊。 只有同一個租用戶內的 HANA 大型執行個體單位可以在 HANA 大型執行個體戳記層級上與其他單位進行通訊。

為了進行計費，「大型執行個體」戳記中已部署的租用戶會指派給一個 Azure 訂用帳戶。 就網路而言，從同一個 Azure 註冊內的其他 Azure 訂用帳戶的虛擬網路加以存取，是可行的。 如果您使用同一個 Azure 區域中的另一個 Azure 訂用帳戶來部署，則也可以選擇尋求個別的 HANA 大型執行個體租用戶。

在「HANA 大型執行個體」上執行 SAP HANA，與在部署於 Azure 中的 VM 上執行 SAP HANA，有顯著的不同：

- SAP HANA on Azure (大型執行個體) 沒有虛擬化層。 您會獲得基礎裸機硬體的效能。
- 與 Azure 不同，SAP HANA on Azure (大型執行個體) 伺服器是特定客戶專用。 您無法對伺服器單位或主機進行硬體分割或軟體分割。 因此，HANA 大型執行個體單位會整個指派給租用戶，並整體指派給您。 重新啟動或關閉伺服器並不會自動導致作業系統和 SAP HANA 被部署到另一部伺服器。 (針對類型 I 類別的 SKU，唯一的例外狀況，是如果伺服器發生問題而必須在另一部伺服器上執行重新部署的情況)。
- 與 Azure 中配合最佳性價比來選取主機處理器類型的方式不同，為 SAP HANA on Azure (大型執行個體) 選擇的處理器類型是 Intel E7v3 和 E7v4 處理器系列中效能最佳的類型。

**後續步驟**
- 請參閱 [HLI 大小調整](hana-sizing.md)
