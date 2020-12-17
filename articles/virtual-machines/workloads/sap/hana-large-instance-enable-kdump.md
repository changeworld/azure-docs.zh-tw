---
title: SAP Hana (大型實例中啟用 Kdump 的腳本) |Microsoft Docs
description: 在 SAP Hana (大型實例中啟用 Kdump 的腳本) ！
services: virtual-machines-linux
documentationcenter: ''
author: prtyag
manager: hrushib
editor: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/30/2020
ms.author: prtyag
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a799242ecaae7b7152d79b7d341a9cb5cc18d7fe
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97654467"
---
# <a name="kdump-for-sap-hana-on-azure-large-instances-hli"></a>Azure 上的 SAP HANA 大型執行個體 (的 Kdump) 

設定和啟用 kdump 是針對沒有明確原因的系統損毀進行疑難排解所需的步驟。
有時候系統會意外損毀，而無法由硬體或基礎結構問題解釋。
在這些情況下，它可能是作業系統或應用程式的問題，而 kdump 會允許 SUSE 判斷系統當機的原因。

## <a name="enable-kdump-service"></a>啟用 Kdump 服務

本檔說明如何在 Azure HANA 大型實例上啟用 Kdump 服務 (**類型 I 和 TYPE II** 的詳細資料) 

## <a name="supported-skus"></a>支援的 SKU

|  Hana 大型實例類型   |  作業系統廠商   |  作業系統套件版本   |  SKU |
|-----------------------------|--------------|-----------------------|-------------|
|   輸入 I                    |  SuSE        |   SLES 12 SP3         |  S224m      |
|   輸入 I                    |  SuSE        |   SLES 12 SP4         |  S224m      |
|   輸入 I                    |  SuSE        |   SLES 12 SP2         |  S72        |
|   輸入 I                    |  SuSE        |   SLES 12 SP2         |  S72m       |
|   輸入 I                    |  SuSE        |   SLES 12 SP3         |  S72m       |
|   輸入 I                    |  SuSE        |   SLES 12 SP2         |  S96        |
|   輸入 I                    |  SuSE        |   SLES 12 SP3         |  S96        |
|   輸入 I                    |  SuSE        |   SLES 12 SP2         |  S192       |
|   輸入 I                    |  SuSE        |   SLES 12 SP3         |  S192       |
|   輸入 I                    |  SuSE        |   SLES 12 SP4         |  S192       |
|   輸入 I                    |  SuSE        |   SLES 12 SP2         |  S192m      |
|   輸入 I                    |  SuSE        |   SLES 12 SP3         |  S192m      |
|   輸入 I                    |  SuSE        |   SLES 12 SP4         |  S192m      |
|   輸入 I                    |  SuSE        |   SLES 12 SP2         |  S144       |
|   輸入 I                    |  SuSE        |   SLES 12 SP3         |  S144       |
|   輸入 I                    |  SuSE        |   SLES 12 SP2         |  S144m      |
|   輸入 I                    |  SuSE        |   SLES 12 SP3         |  S144m      |
|   類型 II                   |  SuSE        |   SLES 12 SP2         |  S384       |
|   類型 II                   |  SuSE        |   SLES 12 SP3         |  S384       |
|   類型 II                   |  SuSE        |   SLES 12 SP4         |  S384       |
|   類型 II                   |  SuSE        |   SLES 12 SP2         |  S384xm     |
|   類型 II                   |  SuSE        |   SLES 12 SP3         |  S384xm     |
|   類型 II                   |  SuSE        |   SLES 12 SP4         |  S384xm     |
|   類型 II                   |  SuSE        |   SLES 12 SP2         |  S576m      |
|   類型 II                   |  SuSE        |   SLES 12 SP3         |  S576m      |
|   類型 II                   |  SuSE        |   SLES 12 SP4         |  S576m      |

## <a name="prerequisites"></a>Prerequisites

- Kdump 服務使用 `/var/crash` 目錄來寫入傾印，請確定對應到此目錄的磁碟分割有足夠的空間可容納傾印。

## <a name="setup-details"></a>設定詳細資料

- 您可以在[這裡](https://github.com/Azure/sap-hana/blob/master/tools/enable-kdump.sh)找到啟用 Kdump 的腳本
> [!NOTE]
> 此腳本是根據我們的實驗室設定來進行，而客戶預期會與 OS 廠商聯繫以進行進一步的調整。
> 將會針對新的和現有的伺服器布建個別的 LUN，以儲存傾印，而腳本將會負責設定 LUN 的檔案系統。
> Microsoft 不會負責分析傾印。 客戶必須向作業系統廠商開啟票證，才能進行分析。

- 使用下列命令，在 HANA 大型實例上執行此腳本

    > [!NOTE]
    > 執行此命令所需的 sudo 許可權。

    ```bash
    sudo bash enable-kdump.sh
    ```

- 如果成功啟用命令輸出 Kdump，請務必重新開機系統，以成功套用變更。

- 如果命令輸出無法進行某些作業，!!!!, 結束，則不會啟用 Kdump 服務。 請參閱 [支援問題](#support-issue)一節。

## <a name="test-kdump"></a>測試 Kdump

> [!NOTE]
>  下列作業會觸發核心損毀和系統重新開機。

- 觸發核心損毀

    ```bash
    echo c > /proc/sysrq-trigger
    ```

- 系統成功重新開機之後，請檢查 `/var/crash` 目錄中是否有核心損毀記錄。

- 如果 `/var/crash` 具有目前日期的目錄，則表示 Kdump 已成功啟用。

## <a name="support-issue"></a>支援問題

如果腳本失敗，並出現錯誤或未啟用 Kdump，請向 Microsoft 支援小組提出下列詳細資料以提出服務要求。

* . 您的訂用帳戶識別碼

* 伺服器名稱

* 作業系統廠商

* OS 版本

* 核心版本

## <a name="related-documents"></a>相關文件
- 若要深入瞭解如何設定 [kdump](https://www.suse.com/support/kb/doc/?id=3374462)
