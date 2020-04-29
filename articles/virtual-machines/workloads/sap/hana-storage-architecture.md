---
title: SAP HANA on Azure (大型執行個體) 的儲存體架構 | Microsoft Docs
description: 如何部署 SAP HANA on Azure (大型執行個體) 的儲存體架構。
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/20/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a12c454906d6c6ff702b7f635a91361bbe3994c1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77616880"
---
# <a name="sap-hana-large-instances-storage-architecture"></a>SAP HANA (大型執行個體) 儲存體架構

Azure 上的 SAP HANA (大型執行個體) 的儲存體配置是由傳統部署模型根據 SAP 建議的指導方針進行設定。 這些指引列載於 [SAP HANA 儲存體需求](https://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)白皮書中。

類型 I 類別的 HANA 大型執行個體通常隨附存放磁碟區四倍的記憶體磁碟區。 針對類型 II 類別的 HANA 大型執行個體單位，存放裝置將不會超過四倍。 這些單位會隨附一個用來儲存 HANA 交易記錄備份的磁碟區。 如需詳細資訊，請參閱[在 Azure 上安裝和設定 SAP HANA (大型執行個體)](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

關於儲存區配置，請參閱下表。 該表格列出了利用不同 HANA 大型執行個體單位提供之不同磁碟區的粗估容量。

| HANA 大型執行個體 SKU | hana/data | hana/log | hana/shared | hana/logbackups |
| --- | --- | --- | --- | --- |
| S72 | 1,280 GB | 512 GB | 768 GB | 512 GB |
| S72m | 3,328 GB | 768 GB |1,280 GB | 768 GB |
| S96 | 1,280 GB | 512 GB | 768 GB | 512 GB |
| S192 | 4,608 GB | 1,024 GB | 1,536 GB | 1,024 GB |
| S192m | 11,520 GB | 1,536 GB | 1,792 GB | 1,536 GB |
| S192xm |  11,520 GB |  1,536 GB |  1,792 GB |  1,536 GB |
| S224 |  4224 GB |  512 GB |  1,024 GB |  512 GB |
| S224m |  8448 GB |  512 GB |  1,024 GB |  512 GB |
| S384 | 11,520 GB | 1,536 GB | 1,792 GB | 1,536 GB |
| S384m | 12,000 GB | 2,050 GB | 2,050 GB | 2,040 GB |
| S384xm | 16,000 GB | 2,050 GB | 2,050 GB | 2,040 GB |
| S384xxm |  20,000 GB | 3,100 GB | 2,050 GB | 3,100 GB |
| S576m | 20,000 GB | 3,100 GB | 2,050 GB | 3,100 GB |
| S576xm | 31,744 GB | 4,096 GB | 2,048 GB | 4,096 GB |
| S768m | 28,000 GB | 3,100 GB | 2,050 GB | 3,100 GB |
| S768xm | 40,960 GB | 6,144 GB | 4,096 GB | 6,144 GB |
| S960m | 36,000 GB | 4,100 GB | 2,050 GB | 4,100 GB |


實際部署的磁碟區可能會因為部署和用來顯示磁碟區大小的工具而不同。

如果您要細分 HANA 大型執行個體 SKU，以下提供一些可能的劃分片段範例：

| 記憶體磁碟分割，以 GB 為單位 | hana/data | hana/log | hana/shared | hana/log/backup |
| --- | --- | --- | --- | --- |
| 256 | 400 GB | 160 GB | 304 GB | 160 GB |
| 512 | 768 GB | 384 GB | 512 GB | 384 GB |
| 768 | 1,280 GB | 512 GB | 768 GB | 512 GB |
| 1,024 | 1,792 GB | 640 GB | 1,024 GB | 640 GB |
| 1,536 | 3,328 GB | 768 GB | 1,280 GB | 768 GB |


這些大小是粗略的磁碟區數目，會根據部署和用來查看磁碟區的工具而有些微不同。 此外還有其他的磁碟分割大小，例如 2.5 TB。 這些儲存體大小會使用上述磁碟分割所使用的類似公式來計算。 「磁碟分割」一詞不表示作業系統、記憶體或 CPU 資源會以任何方式進行磁碟分割。 它表示適用於您可能想要在單一 HANA 執行個體單位上部署之不同 HANA 執行個體的儲存體磁碟分割。 

您可能需要更多儲存體。 您可以購買額外的儲存體 (以 1 TB 為單位)，以增加儲存體。 此額外儲存體可新增為額外的磁碟區。 它也可用來擴充一或多個現有磁碟區。 您無法將磁碟區的大小降低為原始部署的大小以及上述表格中多數記載的大小。 您也無法變更磁碟區的名稱或掛接的名稱。 上述的存放磁碟區已連接至 HANA 大型執行個體單位以作為 NFS4 磁碟區。

您可以使用儲存體快照來進行備份和還原以及災害復原。 如須詳細資訊，請參閱 [Azure 上的 SAP Hana (大型執行個體) 高可用性和災害復原](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

如需適用於您的案例的儲存配置詳細資料，請參閱 [HLI 支援案例](hana-supported-scenario.md)。

## <a name="run-multiple-sap-hana-instances-on-one-hana-large-instance-unit"></a>在一個 HANA 大型執行個體單位上執行多個 SAP HANA 執行個體

您可以在 HANA 大型執行個體單位上，裝載多個使用中的 SAP HANA 執行個體。 若要提供儲存體快照集和災害復原等功能，這類設定在每個執行個體上都需要一個磁碟區組。 目前可使用下列方式來細分 HANA 大型執行個體單位：

- **S72、S72m、S96、S144、S192**：遞增量為 256 GB，最小起始單位為 256 GB。 不同的遞增量 (例如 256 GB 和 512 GB) 可組合為該單位的記憶體最大值。
- **S144m 和 S192m**：增量為 256 gb，512 GB 為最小單位。 不同的遞增量 (例如 512 GB 和 768 GB) 可組合為該單位的記憶體最大值。
- **類型 II 類別**：遞增量為 512 GB，最小起始單位為 2 TB。 不同的遞增量 (例如 512 GB、1 TB 和 1.5 TB) 可組合為該單位的記憶體最大值。

執行多個 SAP HANA 執行個體的一些範例看起來可能像下面這樣。

| SKU | 記憶體大小 | 儲存體大小 | 具有多個資料庫的大小 |
| --- | --- | --- | --- |
| S72 | 768 GB | 3 TB | 1x768-GB HANA 執行個體<br /> 或 1x512-GB 執行個體 + 1x256-GB 執行個體<br /> 或 3x256-GB 執行個體 | 
| S72m | 1.5 TB | 6 TB | 3x512GB HANA 執行個體<br />或 1x512-GB 執行個體 + 1x1-TB 執行個體<br />或 6x256-GB 執行個體<br />或 1x1.5-TB 執行個體 | 
| S192m | 4 TB | 16 TB | 8x512-GB 執行個體<br />或 4x1-TB 執行個體<br />或 4x512-GB 執行個體 + 2x1-TB 執行個體<br />或 4x768-GB 執行個體 + 2x512-GB 執行個體<br />或 1x4-TB 執行個體 |
| S384xm | 8 TB | 22 TB | 4x2-TB 執行個體<br />或 2x4-TB 執行個體<br />或 2x3-TB 執行個體 + 1x2-TB 執行個體<br />或 2x2.5-TB 執行個體 + 1x3-TB 執行個體<br />或 1x8-TB 執行個體 |


此外還有其他變化形式。 

## <a name="encryption-of-data-at-rest"></a>待用資料加密
用於「HANA 大型實例」的儲存體會針對資料儲存于2018年年底之後，使用透明加密。 在先前的部署中，您可以選擇取得已加密的磁片區。 如果您針對該選項決定，您可以要求讓磁片區在線上加密。 從非加密到已加密磁碟區的轉移過程是透明的，並不需要停機。 

利用類型 I 類別的 SKU，就會將儲存開機 LUN 的磁碟區加密。 在修訂版 3 HANA 大型實例戳記中，使用 HANA 大型實例 Sku 的類型 II 類別，您必須使用 OS 方法來加密開機 LUN。 在修訂 4 HANA 大型實例戳記中，使用類型 II 單位的磁片區會儲存開機 LUN，而且預設也會在靜止時加密。 

## <a name="required-settings-for-larger-hana-instances-on-hana-large-instances"></a>HANA 大型實例上較大 HANA 實例的必要設定
在 HANA 大型實例中使用的儲存體具有檔案大小限制。 [大小限制為](https://docs.netapp.com/ontap-9/index.jsp?topic=%2Fcom.netapp.doc.dot-cm-vsmg%2FGUID-AA1419CF-50AB-41FF-A73C-C401741C847C.html)每個檔案 16 TB。 不同于 EXT3 檔案系統中的檔案大小限制，HANA 不會隱含地感知 HANA 大型實例儲存體所強制執行的儲存體限制。 因此，當達到16TB 的檔案大小限制時，HANA 將不會自動建立新的資料檔案。 當 HANA 嘗試擴大檔案超過 16 TB 時，HANA 會回報錯誤，而且索引伺服器會在結束時損毀。

> [!IMPORTANT]
> 為了防止 HANA 嘗試將資料檔案成長到超過 HANA 大型實例儲存體的 16 TB 檔案大小限制，您必須在 HANA 的全域 .ini 設定檔案中設定下列參數：
> 
> - datavolume_striping = true
> - datavolume_striping_size_gb = 15000
> - 另請參閱 SAP note [#2400005](https://launchpad.support.sap.com/#/notes/2400005)
> - 請留意 SAP note [#2631285](https://launchpad.support.sap.com/#/notes/2631285)




**後續步驟**
- 請參閱 [HANA 大型執行個體支援的案例](hana-supported-scenario.md)