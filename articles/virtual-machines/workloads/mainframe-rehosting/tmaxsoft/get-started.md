---
title: 開始在 Azure 虛擬機器上使用 TmaxSoft OpenFrame
description: 在 (Vm) 的 Azure 虛擬機器上使用 TmaxSoft OpenFrame 環境，以重新裝載您的 IBM z/OS 大型主機工作負載。
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.openlocfilehash: 408e0166e52af9efd3d4c64f1b29bddcfc1cca4c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "61485346"
---
# <a name="get-started-with-tmaxsoft-openframe-on-azure"></a>開始在 Azure 上使用 TmaxSoft OpenFrame

使用 TmaxSoft OpenFrame，將您現有的大型主機資產移至 Microsoft Azure。 這個熱門的重新裝載解決方案可在 Azure 上建立模擬環境，讓您能夠快速地遷移應用程式。 不需要重新格式化。

## <a name="openframe-rehosting-environment"></a>OpenFrame 重新裝載環境

在 Azure 上設定 OpenFrame 環境，以進行開發、示範、測試或生產工作負載。 如下圖所示，OpenFrame 包含可在 Azure 上建立大型主機模擬環境的多個元件。 例如，OpenFrame 線上服務將大型主機中介軟體（例如 IBM 客戶資訊控制系統 (CICS) ）取代。 OpenFrame Batch 及其 TJES 元件，會將 IBM 大型主機的工作輸入子系統取代 (JES) 。 

![OpenFrame 重新裝載流程](media/openframe-01.png)

> [!NOTE]
> 若要在 Azure 上執行 OpenFrame 環境，您必須擁有來自 TmaxSoft 的有效產品授權或試用版授權。

## <a name="openframe-components"></a>OpenFrame 元件

下列元件是 Azure 上 OpenFrame 環境的一部分：

- **遷移工具** （包括 OFMiner）是一種分析大型主機資產，然後將其遷移至 Azure 的解決方案。
- **編譯器**，包括 OFCOBOL，此編譯器會解讀大型主機的 COBOL 程式;OFPLI，它會解釋大型主機的 PL/I 程式;和 OFASM，此編譯器會解釋大型主機的組合器程式。
- **前端** 元件（包括 JAVA Enterprise 使用者解決方案） (JEUS ) 、已通過 JAVA enterprise Edition 6. OFGW 認證的 web 應用程式伺服器，以及提供3270接聽程式的 OpenFrame 閘道元件。
- **應用程式** 環境。 OpenFrame Base 是管理整個系統的中介軟體。 OpenFrame Server Type C (.OSC) 取代大型主機的中介軟體和 IBM CICS。
- **關係資料庫**，例如 Tibero (顯示) 、Oracle Database、MICROSOFT SQL SERVER、IBM Db2 或 MySQL。 OpenFrame 應用程式會使用開放式資料庫連接 (ODBC) 通訊協定與資料庫進行通訊。
- 透過 TACF 的**安全性**，此服務模組可控制使用者對系統和資源的存取。 
- **OFManager** 是一種解決方案，可在 web 環境中提供 OpenFrame 的操作和管理功能。

![OpenFrame 架構](media/openframe-02.png)

## <a name="next-steps"></a>後續步驟

- [在 Azure 上安裝 TmaxSoft OpenFrame](./install-openframe-azure.md)
