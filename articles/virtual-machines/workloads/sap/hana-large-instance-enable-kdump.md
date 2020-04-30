---
title: 在 SAP Hana （大型實例）中啟用 Kdump 的腳本 |Microsoft Docs
description: 用來在 SAP Hana （大型實例）的 Kdump 中啟用的腳本-類型 I、的類型 II
services: virtual-machines-linux
documentationcenter: ''
author: prtyag
manager: hrushib
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/30/2020
ms.author: prtyag
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 16dc15b4369904643d0138a4b8e5b94c47868d31
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82204932"
---
# <a name="enable-kdump-service"></a>啟用 Kdump 服務

本檔說明如何在 Azure HANA 大型實例（**類型 I 和類型 II**）上啟用 Kdump 服務的詳細資料

## <a name="supported-skus"></a>支援的 SKU

|  Hana 大型實例類型   |  作業系統廠商   |  OS 套件版本   |  SKU        |
|-----------------------------|--------------|-----------------------|-------------|
|   類型 I                    |  SuSE        |   SLES 12 SP3         |  S224m      |
|   類型 I                    |  SuSE        |   SLES 12 SP4         |  S224m      |
|   類型 I                    |  SuSE        |   SLES 12 SP2         |  S72m       |
|   類型 I                    |  SuSE        |   SLES 12 SP3         |  S72m       |
|   類型 I                    |  SuSE        |   SLES 12 SP2         |  S96        |
|   類型 I                    |  SuSE        |   SLES 12 SP3         |  S96        |
|   類型 II                   |  SuSE        |   SLES 12 SP3         |  S384       |
|   類型 II                   |  SuSE        |   SLES 12 SP3         |  S576m      |
|   類型 II                   |  SuSE        |   SLES 12 SP3         |  S384xm     |
|   類型 II                   |  SuSE        |   SLES 12 SP4         |  S384xm     |

## <a name="prerequisites"></a>先決條件

- Kdump 服務會`/var/crash`使用目錄來寫入傾印，請確定磁碟分割對應到此目錄有足夠的空間可容納傾印。

## <a name="setup-details"></a>設定詳細資料

- 您可以在[這裡](https://github.com/Azure/sap-hana/blob/master/tools/enable-kdump.sh)找到啟用 Kdump 的腳本

- 使用下列命令在 HANA 大型實例上執行此腳本

    > [!NOTE]
    > 執行此命令所需的 sudo 許可權。

    ```bash
    sudo bash enable-kdump.sh
    ```

- 如果已成功啟用命令輸出 Kdump，請將系統重新開機以套用變更，然後 Kdump 就會成功啟用。 將系統重新開機以套用變更。

- 如果命令輸出無法執行特定作業，則結束!!!!, 則不會啟用 Kdump 服務。 請參閱[支援問題](#support-issue)一節。

## <a name="test-kdump"></a>測試 Kdump

> [!NOTE]
>  下列作業將會觸發核心損毀和系統重新開機。

- 觸發核心損毀

    ```bash
    echo 1 > /proc/sys/kernel/sysrq
    echo c > /proc/sysrq-trigger
    ```

- 在系統順利重新開機之後，請`/var/crash`檢查核心損毀記錄檔的目錄。

- 如果`/var/crash`具有目前日期的目錄，則會成功啟用 Kdump。

## <a name="support-issue"></a>支援問題

如果腳本因錯誤而失敗，或未啟用 Kdump，請使用下列詳細資料向 Microsoft 支援小組提出服務要求。

* 的訂用帳戶識別碼

* 伺服器名稱

* 作業系統廠商

* OS 版本

* 核心版本
