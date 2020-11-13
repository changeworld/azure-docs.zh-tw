---
title: 固件安全開機-Azure 安全性
description: Azure 固件安全開機的技術總覽。
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/10/2020
ms.openlocfilehash: feb28b1d448d0146046ed789d1389a3a42f344de
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2020
ms.locfileid: "94557563"
---
# <a name="secure-boot"></a>安全開機

安全開機是 [整合可延伸韌體介面](https://en.wikipedia.org/wiki/Unified_Extensible_Firmware_Interface) (UEFI) 的功能，需要在載入前先驗證所有低層級的固件和軟體元件。 在開機期間，UEFI 安全開機會檢查每一項開機軟體的簽章，包括 UEFI 固件驅動程式 (也稱為選項 Rom) 、可延伸的固件介面 (EFI) 應用程式，以及作業系統驅動程式和二進位檔。 如果這些簽章是由原始設備製造商 (OEM) 的有效或信任，則電腦會開機，而且此固件可讓您控制作業系統。

## <a name="components-and-process"></a>元件和流程

安全開機依賴下列重要元件：

- 平臺金鑰 (PK) -在平臺擁有者 (Microsoft) 和固件之間建立信任。 Public 一半是 PKpub，私用一半是 PKpriv。
- 金鑰註冊金鑰資料庫 (KEK) -在作業系統和平臺固件之間建立信任。 Public 一半是 KEKpub，私用一半是 KEKpriv。
- 簽章資料庫 (db) -保存受信任簽署者的摘要 (公開金鑰和憑證) 已授權與平臺固件互動的固件和軟體程式碼模組。
- 已撤銷簽章資料庫 (.dbx) –保存已被識別為惡意、易受攻擊、遭盜用或未受信任的程式碼模組的摘要。 如果雜湊是在簽章 db 和撤銷的簽章 db 中，則撤銷的簽章資料庫會採用引用項。

下圖和程式說明如何更新這些元件：

![顯示安全開機元件的圖表。](./media/secure-boot/secure-boot.png)

OEM 會將安全開機摘要儲存在機器的非靜態 RAM 上， (NV-RAM) 在製造時。

1.  (db) 的簽章資料庫會填入 UEFI 應用程式的簽署者或映射雜湊、作業系統載入器 (，例如 Microsoft 作業系統載入器或開機管理程式) ，以及受信任的 UEFI 驅動程式。
2. 撤銷的簽章資料庫 (的 .dbx) 會填入不再受信任的模組摘要。
3. 金鑰註冊金鑰 (KEK) 資料庫會填入可用於更新簽章資料庫和撤銷簽章資料庫的簽署金鑰。 您可以透過使用正確金鑰簽署的更新，或使用 [固件] 功能表實際顯示的授權使用者，透過更新來編輯資料庫。
4. 新增 db、的 .dbx 和 KEK 資料庫，並完成最後的固件驗證和測試之後，OEM 會將該固件鎖在編輯中，並產生 (PK) 的平臺金鑰。 PK 可以用來簽署 KEK 的更新，或關閉安全開機。

在開機程式的每個階段中，會計算出固件、開機載入器、作業系統、核心驅動程式和其他開機鏈成品的摘要，並與可接受的值進行比較。 系統不允許載入未受信任的已探索到的固件和軟體。 因此，低層級的惡意程式碼插入或預先開機惡意程式碼攻擊可能會遭到封鎖。

## <a name="secure-boot-on-the-azure-fleet"></a>Azure 車隊上的安全開機
現今，上線並部署至 Azure 計算車隊以裝載客戶工作負載的每一部電腦，都是從啟用安全開機的工廠樓層。 目標工具和程式在硬體增建和整合管線中的每個階段都已備妥，以確保安全開機啟用不會被意外或惡意意圖還原。

驗證 db 和 .dbx 摘要正確無誤，可確保：

- 開機載入器出現在其中一個 db 專案中
- 開機載入器的簽章有效
- 使用受信任軟體的主機開機

 藉由驗證 KEKpub 和 PKpub 的簽章，我們可以確認只有受信任的方有權修改哪些軟體被視為受信任的定義。 最後，藉由確保安全開機處於作用中狀態，我們可以驗證是否已強制執行這些定義。

## <a name="next-steps"></a>後續步驟
若要深入瞭解我們要如何驅動平臺完整性和安全性，請參閱：

- [固件安全性](firmware.md)
- [測量開機和主機證明](measured-boot-host-attestation.md)
- [專案 Cerberus](project-cerberus.md)
- [待用加密](encryption-atrest.md)
- [虛擬程式安全性](hypervisor.md)