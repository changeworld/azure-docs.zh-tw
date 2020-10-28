---
title: Windows 虛擬桌面 MSIX 應用程式附加詞彙-Azure
description: MSIX 應用程式附加詞彙和概念的詞彙。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 6107ffea4fe4d615a42973ab1b231ca9f6b5241f
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92674956"
---
# <a name="msix-app-attach-glossary"></a>MSIX 應用程式附加詞彙

本文是與 MSIX app attach 相關的重要詞彙和概念的定義清單。

## <a name="msix-container"></a>MSIX 容器

MSIX 容器是 MSIX apps 的執行位置。 若要深入瞭解，請參閱 [MSIX 容器](/windows/msix/msix-container)。

## <a name="msix-application"></a>MSIX 應用程式 

儲存在中的應用程式。MSIX 檔案。

## <a name="msix-package"></a>MSIX 套件 

MSIX 套件是 MSIX 檔案或應用程式。

## <a name="msix-share"></a>MSIX 共用

MSIX 共用是保存擴充的 MSIX 套件的網路共用。 MSIX 共用支援 SMB 3 或更新版本。 應用程式會從此 MSIX 共用暫存，而不需要將應用程式檔移至系統磁片磁碟機。

## <a name="msix-image"></a>MSIX 影像

MSIX 映射是包含一或多個 MSIX 封裝應用程式的 VHD、VHDx 或 CIM 檔案。 每個應用程式都會使用 MSIXMGR 工具在 MSIX 映射中傳遞。

## <a name="repackage"></a>重新封裝

重新封裝會採用非 MSIX 的應用程式，並使用 MSIX 封裝工具 (的) 來將它轉換成 MSIX。 如需詳細資訊，請參閱 [MSIX 封裝工具總覽](/windows/msix/packaging-tool/tool-overview)。

## <a name="expand-an-msix-package"></a>展開 MSIX 套件

擴充 MSIX 套件是一個多步驟的程式。 展開會採用 MSIX 檔案，並將其內容放入 VHD (x) 或 CIM 檔案中。 

若要展開 MSIX 套件：

1. 取得 MSIX 套件 (MSIX 檔) 。
2. 將 MSIX 檔案重新命名為 .zip 檔案。
3. 將產生的 .zip 檔案解壓縮到資料夾中。
4. 建立與資料夾大小相同的 VHD。
5. 掛接 VHD。
6. 將磁片初始化。
7. 建立資料分割。
8. 格式化磁碟分割。
9. 將解壓縮的內容複寫到 VHD。
10. 使用 MSIXMGR 工具，在 VHD 的內容上套用 Acl。
11. 將 VHD (x) 或 [CIM](#cim)卸載。

## <a name="upload-an-msix-package"></a>上傳 MSIX 套件 

上傳 MSIX 套件牽涉到將 VHD (x) 或包含擴充的 MSIX 套件的 [CIM](#cim) 上傳至 MSIX 共用。

在 Windows 虛擬桌面中，每個 MSIX 共用會進行上傳一次。 當您上傳封裝之後，相同訂用帳戶中的所有主機集區都可以參考它。

## <a name="add-an-msix-package"></a>新增 MSIX 套件

在 Windows 虛擬桌面中，新增 MSIX 套件會將它連結至主機集區。

## <a name="publish-an-msix-package"></a>發佈 MSIX 套件 

在 Windows 虛擬桌面中，必須將已發佈的 MSIX 套件指派給 Active Directory 網域服務 (AD DS) 或 Azure Active Directory (Azure AD 使用者或使用者群組。

## <a name="staging"></a>預備

預備環境牽涉到兩件事：

- 將 VHD (x) 或 [CIM](#cim) 裝載至 VM。
- 通知作業系統 MSIX 套件可供註冊。

## <a name="registration"></a>註冊

註冊可讓您的使用者使用暫存 MSIX 套件。 註冊是以每個使用者為基礎。 如果您尚未為該特定使用者明確註冊應用程式，他們將無法執行應用程式。

註冊的類型有兩種： [一般] 和 [延遲]。

### <a name="regular-registration"></a>一般註冊

在一般註冊中，指派給使用者的每個應用程式都會完整註冊。 註冊會在使用者登入會話的時間進行，這可能會影響他們開始使用 Windows 虛擬桌面所需的時間。

### <a name="delayed-registration"></a>延遲的註冊

在延遲註冊中，指派給使用者的每個應用程式只會部分註冊。 部分註冊表示已註冊 [開始] 功能表圖格和按兩下檔案關聯。 註冊會在使用者登入其會話時進行，因此對開始使用 Windows 虛擬桌面所需的時間會有最大的影響。 只有當使用者在 MSIX 套件中執行應用程式時，才會完成註冊。

延遲的註冊目前是 MSIX 應用程式附加的預設設定。

## <a name="deregistration"></a>登出

取消註冊會移除使用者的已註冊但非執行中 MSIX 封裝。 當使用者登出會話時，就會進行取消註冊。 在解除註冊期間，MSIX app attach 會將使用者專屬的應用程式資料推送至本機使用者設定檔。

## <a name="destage"></a>取消暫存

Destaging 會通知作業系統，MSIX 封裝或應用程式目前未執行，而且沒有暫存給任何使用者。 這會移除 OS 中的所有參考。

## <a name="cim"></a>Cim

.CIM 是與複合圖像檔案系統 (CimFS) 相關聯的新副檔名。 掛接和卸載 CIM 檔案的速度較快。 CIM 也會耗用比 VHD 更少的 CPU 和記憶體。

下表是 VHD 與 CimFS 之間的效能比較。 這些數位是測試回合的結果，其中每個格式的 500 300 MB 檔案都在 DSv4 電腦上執行。

|  規格                          | VHD                     | CimFS   |
|---------------------------------|--------------------------|-----------|
| 平均裝入時間     | 356毫秒                     | 255毫秒      |
| 平均卸載時間   | 1615毫秒                    | 36毫秒       |
| 記憶體使用量 | 6% (8 GB)                       | 8 GB) 的 2% (       |
| CPU (計數尖峰)           | 超量多次 | 沒有影響 |

## <a name="next-steps"></a>後續步驟

如果您想要深入瞭解 MSIX app 附加，請參閱我們的 [總覽](what-is-app-attach.md) 和 [常見問題](app-attach-faq.md)。 否則，請開始 [設定應用程式連接](app-attach.md)。
