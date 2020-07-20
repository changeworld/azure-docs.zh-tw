---
title: Nsg 的流程記錄簡介
titleSuffix: Azure Network Watcher
description: 本文說明如何使用 Azure 網路監看員的 NSG 流量記錄功能。
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: e59a985f59da1b6a40a6b583d5e2a490611a702c
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2020
ms.locfileid: "86043847"
---
# <a name="introduction-to-flow-logging-for-network-security-groups"></a>網路安全性群組流量記錄簡介

## <a name="introduction"></a>簡介

[網路安全性群組](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules)（NSG）流量記錄是 Azure 網路監看員的一項功能，可讓您記錄流經 NSG 之 IP 流量的相關資訊。 流量資料會傳送至您可以存取的 Azure 儲存體帳戶，以及將其匯出至您選擇的任何視覺效果工具、SIEM 或識別碼。

![流量記錄概觀](./media/network-watcher-nsg-flow-logging-overview/homepage.jpg)

## <a name="why-use-flow-logs"></a>為何要使用流量記錄？

要保有穩固的安全性、合規性和效能，監視、管理及了解您本身的網路，是很重要的。 了解您本身的環境，是保護和最佳化環境的第一要務。 您通常必須了解網路目前的狀態、哪些人正在連線、他們從哪裡連接、對網際網路開啟了哪些連接埠、預期的網路行為、異常的網路行為，以及流量突然上升等。

流量記錄是雲端環境中所有網路活動的真實來源。 無論您是即將推出的新創公司嘗試優化資源或大型企業嘗試偵測入侵，流量記錄都是您的最佳選擇。 您可以使用它來優化網路流量、監視輸送量、驗證合規性、偵測入侵等等。

## <a name="common-use-cases"></a>一般使用案例

**網路監視**：識別不明或不想要的流量。 監視流量層級和頻寬耗用量。 依 IP 和埠篩選流量記錄，以瞭解應用程式的行為。 將流量記錄匯出至您選擇的分析和視覺效果工具，以設定監視儀表板。

**使用狀況監視與優化：** 識別網路中的前幾個發言多者。 結合 GeoIP 資料來識別跨區域的流量。 瞭解容量預測的流量成長。 使用資料來移除 overtly 限制的流量規則。

**合規性**：使用流量資料來驗證網路隔離和與企業存取規則的相容性

**網路辯論 & 安全性分析**：分析來自遭入侵之 ip 和網路介面的網路流量。 將流量記錄匯出至您選擇的任何 SIEM 或 ID 工具。

## <a name="how-logging-works"></a>記錄的運作方式

**索引鍵屬性**

- 流量記錄會在[第4層](https://en.wikipedia.org/wiki/OSI_model#Layer_4:_Transport_Layer)運作，並記錄進出 NSG 的所有 IP 流量
- 記錄是透過 Azure 平臺收集而來，並不會以任何方式影響客戶資源或網路效能。
- 記錄是以 JSON 格式寫入，並顯示每個 NSG 規則的輸出和輸入流程。
- 每一筆記錄都包含套用流量的網路介面（NIC）、5個元組資訊、流量決策 & （僅限第2版）輸送量資訊。 如需完整詳細資料，請參閱下面的_記錄格式_。
- 流量記錄的保留功能可讓您在建立記錄後，自動將它們刪除一年。 

> [!NOTE]
> 只有當您使用[一般用途 V2 儲存體帳戶（GPv2）](https://docs.microsoft.com/azure/storage/common/storage-account-overview#types-of-storage-accounts)時，才會提供保留。 

**核心概念**

- 軟體定義的網路會以虛擬網路（Vnet）和子網來組織。 您可以使用 NSG 來管理這些 Vnet 和子網的安全性。
- 網路安全性群組（NSG）包含_安全性規則_清單，可允許或拒絕其所連線之資源中的網路流量。 Nsg 可以與子網、個別 Vm 或連接至 Vm 的個別網路介面（NIC）相關聯（Resource Manager）。 如需詳細資訊，請參閱[網路安全性群組概觀](https://docs.microsoft.com/azure/virtual-network/security-overview?toc=%2Fazure%2Fnetwork-watcher%2Ftoc.json)。
- 您網路中的所有流量都會使用適用 NSG 中的規則進行評估。
- 這些評估的結果是 NSG 流量記錄。 流量記錄是透過 Azure 平臺收集而來，不需要對客戶資源進行任何變更。
- 注意：規則有兩種類型-終止 & 非終止，每個都有不同的記錄行為。
- - NSG 拒絕規則正在終止。 拒絕流量的 NSG 會將它記錄在流量記錄中，而在此情況下，會在任何 NSG 拒絕流量之後停止處理。 
- - NSG 允許規則不會終止，這表示即使一個 NSG 允許，處理仍會繼續進行下一個 NSG。 允許流量的最後一個 NSG 會將流量記錄到流量記錄。
- NSG 流量記錄會寫入至可供存取的儲存體帳戶。
- 您可以使用 TA、Splunk、Grafana、Stealthwatch 等工具來匯出、處理、分析流量記錄，並將其視覺化。

## <a name="log-format"></a>記錄格式

流量記錄包含下列屬性：

* **time** - 事件的記錄時間
* **systemId** -網路安全性群組系統識別碼。
* **類別** - 事件的類別。 類別一律為 **NetworkSecurityGroupFlowEvent**
* **resourceid** -NSG 的資源識別碼
* **operationName** - 一律是 NetworkSecurityGroupFlowEvents
* **properties** - 流量屬性的集合
    * **Version** - 流量記錄事件結構描述的版本號碼
    * **流程**-流程的集合。 這個屬性有多個適用於不同規則的項目
        * **rule** - 做為流量列出依據的規則
            * **flows** - 流量的集合
                * **mac** - 流量收集所在 VM 之 NIC 的 MAC 位址
                * **flowTuples** - 包含多個流量 tuple 屬性的逗號分隔格式字串
                    * **時間戳記**-此值是以 UNIX epoch 格式出現流程時的時間戳記
                    * **來源 IP** - 來源 IP
                    * **目的地 IP** - 目的地 IP
                    * **來源連接埠** - 來源連接埠
                    * **目的地連接埠** - 目的地連接埠
                    * **通訊協定** - 流量的通訊協定。 有效值為 **T** (若為 TCP) 和 **U** (若為 UDP)
                    * **流量流動** - 流量的流動方向。 有效值為 **I** (若為輸入) 和 **O** (若為輸出)。
                    * **流量判定** - 流量受到允許或拒絕。 有效值為 **A** (若允許) 和 **D** (若拒絕)。
                    * **流量狀態 - 僅限第 2 版** - 擷取流程的狀態。 可能的狀態為 **B**：開始，當流量建立時。 不提供統計資料。 **C**：繼續進行中的流量。 提供 5 分鐘間隔的統計資料。 **E**：結束，當流量終止時。 提供統計資料。
                    * **封包 - 來源到目的地 - 僅限第 2 版** 上次更新之後從來源傳送到目的地的 TCP 或 UDP 封包總數。
                    * **傳送的位元組 - 來源到目的地 - 僅限第 2 版** 上次更新之後從來源傳送到目的地的 TCP 或 UDP 封包位元組總數。 封包位元組包括封包標頭與承載。
                    * **封包 - 目的地到來源 - 僅限第 2 版** 上次更新之後從目的地傳送到來源的 TCP 或 UDP 封包總數。
                    * **傳送的位元組 - 目的地到來源 - 僅限第 2 版** 上次更新之後從目的地傳送到來源的 TCP 與 UDP 封包位元組總數。 封包位元組包括封包標頭與承載。


**NSG 流量記錄第2版（vs 版本1）** 

第2版的記錄引進了流程狀態的概念。 您可以設定您所收到的流量記錄版本。

當起始流量時，會記錄流量狀態 _B_。 流量狀態 _C_ 與流量狀態 _E_ 是分別標記繼續傳送流量與流量終止的狀態。 _C_ 與 _E_ 狀態包含流量頻寬資訊。

### <a name="sample-log-records"></a>範例記錄的記錄

之後的文字是流量記錄範例。 如您所見，有多筆記錄遵循上一節所述的屬性清單。

> [!NOTE]
> *FlowTuples*屬性中的值是以逗號分隔的清單。
 
**第 1 版 NSG 流量記錄格式範例**
```json
{
    "records": [
        {
            "time": "2017-02-16T22:00:32.8950000Z",
            "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 1,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282421,42.119.146.95,10.1.0.4,51529,5358,T,I,D"
                                ]
                            }
                        ]
                    },
                    {
                        "rule": "UserRule_default-allow-rdp",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282370,163.28.66.17,10.1.0.4,61771,3389,T,I,A",
                                    "1487282393,5.39.218.34,10.1.0.4,58596,3389,T,I,A",
                                    "1487282393,91.224.160.154,10.1.0.4,61540,3389,T,I,A",
                                    "1487282423,13.76.89.229,10.1.0.4,53163,3389,T,I,A"
                                ]
                            }
                        ]
                    }
                ]
            }
        },
        {
            "time": "2017-02-16T22:01:32.8960000Z",
            "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 1,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282481,195.78.210.194,10.1.0.4,53,1732,U,I,D"
                                ]
                            }
                        ]
                    },
                    {
                        "rule": "UserRule_default-allow-rdp",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282435,61.129.251.68,10.1.0.4,57776,3389,T,I,A",
                                    "1487282454,84.25.174.170,10.1.0.4,59085,3389,T,I,A",
                                    "1487282477,77.68.9.50,10.1.0.4,65078,3389,T,I,A"
                                ]
                            }
                        ]
                    }
                ]
            }
        },
    "records":
    [
        
        {
             "time": "2017-02-16T22:00:32.8950000Z",
             "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_DenyAllInBound","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282421,42.119.146.95,10.1.0.4,51529,5358,T,I,D"]}]},{"rule":"UserRule_default-allow-rdp","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282370,163.28.66.17,10.1.0.4,61771,3389,T,I,A","1487282393,5.39.218.34,10.1.0.4,58596,3389,T,I,A","1487282393,91.224.160.154,10.1.0.4,61540,3389,T,I,A","1487282423,13.76.89.229,10.1.0.4,53163,3389,T,I,A"]}]}]}
        }
        ,
        {
             "time": "2017-02-16T22:01:32.8960000Z",
             "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_DenyAllInBound","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282481,195.78.210.194,10.1.0.4,53,1732,U,I,D"]}]},{"rule":"UserRule_default-allow-rdp","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282435,61.129.251.68,10.1.0.4,57776,3389,T,I,A","1487282454,84.25.174.170,10.1.0.4,59085,3389,T,I,A","1487282477,77.68.9.50,10.1.0.4,65078,3389,T,I,A"]}]}]}
        }
        ,
        {
             "time": "2017-02-16T22:02:32.9040000Z",
             "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_DenyAllInBound","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282492,175.182.69.29,10.1.0.4,28918,5358,T,I,D","1487282505,71.6.216.55,10.1.0.4,8080,8080,T,I,D"]}]},{"rule":"UserRule_default-allow-rdp","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282512,91.224.160.154,10.1.0.4,59046,3389,T,I,A"]}]}]}
        }
        
        
```
**第 2 版 NSG 流量記錄格式範例**
```json
 {
    "records": [
        {
            "time": "2018-11-13T12:00:35.3899262Z",
            "systemId": "a0fca5ce-022c-47b1-9735-89943b42f2fa",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 2,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF87856",
                                "flowTuples": [
                                    "1542110402,94.102.49.190,10.5.16.4,28746,443,U,I,D,B,,,,",
                                    "1542110424,176.119.4.10,10.5.16.4,56509,59336,T,I,D,B,,,,",
                                    "1542110432,167.99.86.8,10.5.16.4,48495,8088,T,I,D,B,,,,"
                                ]
                            }
                        ]
                    },
                    {
                        "rule": "DefaultRule_AllowInternetOutBound",
                        "flows": [
                            {
                                "mac": "000D3AF87856",
                                "flowTuples": [
                                    "1542110377,10.5.16.4,13.67.143.118,59831,443,T,O,A,B,,,,",
                                    "1542110379,10.5.16.4,13.67.143.117,59932,443,T,O,A,E,1,66,1,66",
                                    "1542110379,10.5.16.4,13.67.143.115,44931,443,T,O,A,C,30,16978,24,14008",
                                    "1542110406,10.5.16.4,40.71.12.225,59929,443,T,O,A,E,15,8489,12,7054"
                                ]
                            }
                        ]
                    }
                ]
            }
        },
        {
            "time": "2018-11-13T12:01:35.3918317Z",
            "systemId": "a0fca5ce-022c-47b1-9735-89943b42f2fa",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 2,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF87856",
                                "flowTuples": [
                                    "1542110437,125.64.94.197,10.5.16.4,59752,18264,T,I,D,B,,,,",
                                    "1542110475,80.211.72.221,10.5.16.4,37433,8088,T,I,D,B,,,,",
                                    "1542110487,46.101.199.124,10.5.16.4,60577,8088,T,I,D,B,,,,",
                                    "1542110490,176.119.4.30,10.5.16.4,57067,52801,T,I,D,B,,,,"
                                ]
                            }
                        ]
                    }
                ]
            }
        }
        
```
**記錄元組說明**

![流量記錄概觀](./media/network-watcher-nsg-flow-logging-overview/tuple.png)

**範例頻寬計算**

來自 185.170.185.105:35370 與 10.2.0.4:23 間之 TCP 對話的流量 Tuple：

"1493763938,185.170.185.105,10.2.0.4,35370,23,T,I,A,B,,,," "1493695838,185.170.185.105,10.2.0.4,35370,23,T,I,A,C,1021,588096,8005,4610880" "1493696138,185.170.185.105,10.2.0.4,35370,23,T,I,A,E,52,29952,47,27072"

針對繼續 _C_ 與結束 _E_ 流量狀態，位元組與封包計數是從上一個流量 Tuple 記錄開始時的彙總計數。 參考上一個範例對話，已傳輸的封包總數是 1021+52+8005+47 = 9125。 已傳輸的位元組總數是 588096+29952+4610880+27072 = 5256000。


## <a name="enabling-nsg-flow-logs"></a>啟用 NSG 流量記錄

請使用下方的相關連結，取得啟用流量記錄的指南。

- [Azure 入口網站](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)
- [PowerShell](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-powershell)
- [CLI](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-cli)
- [REST](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-rest)
- [Azure Resource Manager](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-azure-resource-manager)

## <a name="updating-parameters"></a>正在更新參數

**Azure 入口網站**

在 [Azure 入口網站上，流覽至網路監看員中的 [NSG 流量記錄] 區段。 然後按一下 [NSG] 的名稱。 這會顯示流量記錄的 [設定] 窗格。 變更您想要的參數，並按 [**儲存**] 以部署變更。

**PS/CLI/REST/ARM**

若要透過命令列工具更新參數，請使用用來啟用流量記錄的相同命令（來自上述），但以您想要變更的已更新參數。

## <a name="working-with-flow-logs"></a>使用流量記錄

*讀取和匯出流量記錄*

- [&amp;從入口網站下載視圖流程記錄](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal#download-flow-log)
- [使用 PowerShell 函數讀取流量記錄](https://docs.microsoft.com/azure/network-watcher/network-watcher-read-nsg-flow-logs)
- [將 NSG 流量記錄匯出至 Splunk](https://www.splunk.com/en_us/blog/tips-and-tricks/splunking-microsoft-azure-network-watcher-data.html)

雖然流量記錄是以 NSG 為目標，但其顯示方式與其他記錄不同。 流量記錄只會儲存在儲存體帳戶內，並且會採用以下範例所示的記錄路徑：

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

*將流量記錄視覺化*

- [Azure 流量分析](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)是一種 azure 原生服務，可處理流量記錄、解壓縮見解並將流量記錄視覺化。 
- [教學課程使用 Power BI 將 NSG 流量記錄視覺化](https://docs.microsoft.com/azure/network-watcher/network-watcher-visualize-nsg-flow-logs-power-bi)
- [教學課程使用彈性堆疊將 NSG 流量記錄視覺化](https://docs.microsoft.com/azure/network-watcher/network-watcher-visualize-nsg-flow-logs-open-source-tools)
- [教學課程使用 Grafana 管理和分析 NSG 流量記錄](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-grafana)
- [教學課程使用 Graylog 管理和分析 NSG 流量記錄](https://docs.microsoft.com/azure/network-watcher/network-watcher-analyze-nsg-flow-logs-graylog)


## <a name="nsg-flow-logging-considerations"></a>NSG 流程記錄考慮

**儲存體帳戶考慮**： 

- 位置：使用的儲存體帳戶必須與 NSG 位於相同的區域。
- 自我管理金鑰輪替：如果您變更/輪替儲存體帳戶的存取金鑰，NSG 流量記錄將會停止運作。 若要修正此問題，您必須先停用再重新啟用 NSG 流量記錄。

**流量記錄成本**： NSG 流量記錄是根據所產生的記錄量來計費。 高流量可能會產生大量流量記錄和相關費用。 NSG 流量記錄價格不包括儲存體的基礎結構費用。 搭配 NSG 流量記錄使用保留原則功能，表示在一段時間內會產生個別的儲存成本。 如果您不需要保留原則功能，建議您將此值設為 0。 如需詳細資訊，請參閱[網路監看員定價](https://azure.microsoft.com/pricing/details/network-watcher/)和[Azure 儲存體定價](https://azure.microsoft.com/pricing/details/storage/)，以取得其他詳細資料。

**輸入流量不正確的位元組和封包計數**：[網路安全性群組（nsg）](https://docs.microsoft.com/azure/virtual-network/security-overview)會實作為具[狀態防火牆](https://en.wikipedia.org/wiki/Stateful_firewall?oldformat=true)。 不過，由於平臺限制，控制輸入流量的規則會以無狀態的方式執行。 因為此位元組和封包計數不會針對這些流程進行記錄。 因此，在 NSG 流量記錄（和流量分析）中回報的位元組和封包數目可能與實際數位不同。 此外，輸入流程現在不會終止。 這項限制已排程于2020年12月修正。 

**從網際網路 ip 記錄到沒有公用 ip 的 vm 的輸入流量**：沒有公用 ip 位址的 vm 會透過與 NIC 相關聯的公用 ip 位址指派為實例層級的公用 ip，或屬於基本負載平衡器後端集區的一部分，請使用[預設 SNAT](../load-balancer/load-balancer-outbound-connections.md) ，並具有由 Azure 指派的 IP 位址，以加速輸出連線能力。 因此，如果流程是以指派給 SNAT 的埠範圍內的埠為目的地，您可能會看到來自網際網路 IP 位址之流量的流量記錄專案。 雖然 Azure 不允許這些流量進入 VM，但會記錄嘗試，並依設計出現在網路監看員的 NSG 流量記錄中。 我們建議使用 NSG 明確封鎖不想要的輸入網際網路流量。

## <a name="best-practices"></a>最佳作法

**在重要的 vnet/子網上啟用**：您應該在訂用帳戶中的所有重要 vnet/子網上啟用流量記錄，以作為可審核性和安全性最佳做法。 

**在附加至資源的所有 nsg 上啟用 NSG 流量記錄**： Azure 中的流量記錄設定于 NSG 資源上。 流量只會與一個 NSG 規則相關聯。 在使用多個 Nsg 的情況下，建議您在套用資源的子網或網路介面的所有 Nsg 上啟用 NSG 流量記錄，以確保記錄所有流量。 如需詳細資訊，請參閱網路安全性群組中的[流量評估方式](../virtual-network/security-overview.md#how-traffic-is-evaluated)。

**存放裝置**布建：應該以預期的流量記錄磁片區來調整儲存體。

## <a name="troubleshooting-common-issues"></a>針對常見問題進行疑難排解

**我無法啟用 NSG 流量記錄**

- 未註冊**Microsoft Insights**資源提供者

如果您收到 AuthorizationFailed__ 或 GatewayAuthenticationFailed__ 錯誤，則表示您可能尚未在訂用帳戶上啟用 Microsoft Insights 資源提供者。 [請遵循指示](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal#register-insights-provider)來啟用 Microsoft Insights 提供者。

**我已啟用 NSG 流量記錄，卻未在儲存體帳戶中看到資料**

- **設定時間**

NSG 流量記錄最多可能需要 5 分鐘才會出現在儲存體帳戶中 (在正確設定的情況下)。 此時會出現 PT1H.json，您可依[此處所述](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal#download-flow-log)的資訊存取。

- **您的 NSG 沒有流量**

有時您不會看到記錄，因為您的 VM 未處於作用中狀態，或是應用程式閘道或其他裝置上有上游篩選器封鎖了對 NSG 的流量。

**我想要將 NSG 流量記錄自動化**

透過 ARM 範本的自動化支援目前不適用於 NSG 流量記錄。 如需詳細資訊，請閱讀[功能公告](https://azure.microsoft.com/updates/arm-template-support-for-nsg-flow-logs/)。

## <a name="faq"></a>常見問題集

**NSG 流量記錄有哪些功能？**

您可以透過[網路安全性群組（nsg）](https://docs.microsoft.com/azure/virtual-network/security-overview)結合和管理 Azure 網路資源。 NSG 流量記錄可讓您透過 Nsg 記錄所有流量的5個元組流量資訊。 未經處理的流量記錄會寫入 Azure 儲存體帳戶，讓您可以視需要進一步進行處理、分析、查詢或匯出。

**使用流量記錄是否會影響我的網路延遲或效能？**

流量記錄資料是在網路流量的路徑外收集，因此不會影響網路輸送量或延遲。 您可以建立或刪除流量記錄，而不會影響網路效能的風險。

**如何? 將 NSG 流量記錄與防火牆後方的儲存體帳戶搭配使用？**

若要使用防火牆後方的儲存體帳戶，您必須為受信任的 Microsoft 服務提供例外狀況，才能存取您的儲存體帳戶：

- 在入口網站上的全域搜尋中，或從 [[儲存體帳戶] 頁面](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Storage%2FStorageAccounts)輸入儲存體帳戶的名稱，以流覽至儲存體帳戶。
- 在 [**設定**] 區段下，選取 [**防火牆和虛擬網路**]
- 在 [**允許存取來源**] 中，選取 [**選取的網路**]。 然後，在 [**例外**狀況] 底下，勾選 [允許信任的 Microsoft 服務存取此儲存體帳戶] 旁的方塊 * * * *
- 如果早已選取，則不需要再變更。
- 在 [ [NSG 流量記錄] 總覽頁面](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs)上找出您的目標 NSG，並啟用已選取上述儲存體帳戶的 NSG 流量記錄。

您可以在幾分鐘後檢查儲存體記錄，屆時應該就會看到已更新的時間戳記或新建立的 JSON 檔案。

**如何? 在服務端點後方的儲存體帳戶中使用 NSG 流量記錄嗎？**

NSG 流量記錄與服務端點相容，而不需要任何額外的設定。 請參閱在虛擬網路中[啟用服務端點的教學](https://docs.microsoft.com/azure/virtual-network/tutorial-restrict-network-access-to-resources#enable-a-service-endpoint)課程。

**流量記錄版本 1 & 2 之間的差異為何？**

流量記錄第2版引進了_流程狀態_的概念，& 儲存傳輸位元組和封包的相關資訊。 [閱讀更多內容](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview#log-file)

## <a name="pricing"></a>定價

NSG 流量記錄會依收集的記錄 GB 計費，每個訂用帳戶可提供每月 5 GB 的免費層。 如需您所在地區的目前定價，請參閱[網路監看員定價頁面](https://azure.microsoft.com/pricing/details/network-watcher/)。

記錄的儲存體會分開計費，如需相關價格，請參閱[Azure 儲存體區塊 blob 定價頁面](https://azure.microsoft.com/pricing/details/storage/blobs/)。
 
