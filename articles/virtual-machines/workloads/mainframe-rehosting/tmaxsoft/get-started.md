---
title: 開始在 Azure 虛擬機器上使用 TmaxSoft 開放幀
description: 使用 Azure 虛擬機器 （VM） 上的 TmaxSoft OpenFrame 環境重新託管 IBM z/OS 主機工作負載。
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.openlocfilehash: 408e0166e52af9efd3d4c64f1b29bddcfc1cca4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "61485346"
---
# <a name="get-started-with-tmaxsoft-openframe-on-azure"></a>在 Azure 上使用 TmaxSoft 開放幀開始

獲取現有大型機資源，並使用 TmaxSoft OpenFrame 將其移動到 Microsoft Azure。 此流行的重新託管解決方案在 Azure 上創建模擬環境，使您能夠快速遷移應用程式。 無需重新格式化。

## <a name="openframe-rehosting-environment"></a>打開幀重新託管環境

在 Azure 上設置 OpenFrame 環境，用於開發、演示、測試或生產工作負荷。 如下圖所示，OpenFrame 包括多個元件，這些元件在 Azure 上創建大型機模擬環境。 例如，OpenFrame 線上服務取代了大型機中介軟體，如 IBM 客戶資訊控制系統 （CICS）。 OpenFrame Batch 及其 TJES 元件取代了 IBM 主機的作業輸入子系統 （JES）。 

![打開幀重新託管過程](media/openframe-01.png)

> [!NOTE]
> 要在 Azure 上運行 OpenFrame 環境，必須擁有 TmaxSoft 的有效產品許可證或試用許可證。

## <a name="openframe-components"></a>開放框架元件

以下元件是 Azure 上的 OpenFrame 環境的一部分：

- **遷移工具**，包括 OFMiner，一種分析大型機資產然後將其遷移到 Azure 的解決方案。
- **編譯器**，包括OFCOBOL，一個解釋大型機COBOL程式的編譯器;OFPLI，它解釋大型機的PL/I程式;和 OFASM，一個解釋大型機的組合語言程式的編譯器。
- **前端**元件，包括 JAVA 企業使用者解決方案 （JEUS），一個經過 JAVA 企業版 6.OFGW 認證的 Web 應用程式伺服器，以及提供 3270 攔截器的 OpenFrame 閘道元件。
- **應用程式**環境。 OpenFrame Base 是管理整個系統的中介軟體。 OpenFrame 伺服器類型 C （OSC） 取代了大型機的中介軟體和 IBM CICS。
- **關係資料庫**，如 Tibero（如圖所示）、Oracle 資料庫、微軟 SQL 伺服器、IBM Db2 或 MySQL。 OpenFrame 應用程式使用開放資料庫連接 （ODBC） 協定與資料庫通信。
- **通過**TACF 的安全性，TACF 是一個服務模組，用於控制使用者對系統和資源的訪問。 
- **OFManager**是一種在 Web 環境中提供 OpenFrame 的操作和管理功能的解決方案。

![開放框架架構](media/openframe-02.png)

## <a name="next-steps"></a>後續步驟

- [在 Azure 上安裝 TmaxSoft 開放幀](./install-openframe-azure.md)
