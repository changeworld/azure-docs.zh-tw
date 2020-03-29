---
title: 微軟 Azure StorSimple 虛擬陣列更新 1.2 版本資訊*微軟文檔
description: 描述運行更新 1.2 的 StorSimple 虛擬陣列的關鍵打開問題和解決方法。
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: article
ms.date: 05/29/2019
ms.author: alkohli
ms.openlocfilehash: ea7e4801dfaad533403c0f927a03735ae409cc52
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "66420599"
---
# <a name="storsimple-virtual-array-update-12-release-notes"></a>StorSimple 虛擬陣列更新 1.2 版本資訊

下列版本資訊指出 Microsoft Azure StorSimple Virtual Array 更新的重大未決問題和已解決問題。

版本資訊會不斷更新。 發現有需要採取因應措施的重大問題時，就會加入新的內容。 部署 StorSimple Virtual Array 之前，請仔細檢閱版本資訊中所含的資訊。

更新 1.2 對應于軟體版本**10.0.10311.0**。

> [!IMPORTANT]
> - 更新是干擾性作業，會重新啟動您的裝置。 如果 I/O 正在進行中，裝置就會停機。 有關用於應用此更新的套裝軟體的詳細說明，請轉到[下載更新 1.2](#download-update-12)。
> - 僅當設備正在運行更新 1.0 或 1.1 時，更新 1.2 才可通過 Azure 門戶獲得。

## <a name="whats-new-in-update-12"></a>Update 1.2 的新功能

此更新包含以下錯誤修復：

- 在處理已刪除的檔時提高了彈性。
- 改進了代碼啟動路徑中的處理異常，減少了備份、還原、雲讀取和自動空間回收中的故障。

## <a name="download-update-12"></a>下載更新 1.2

要下載此更新，請轉到[Microsoft 更新目錄](https://www.catalog.update.microsoft.com/Home.aspx)伺服器，然後下載 KB4502035 包。 此包包含以下包：

 - **KB4493446，** 其中包含截至 2019 年 4 月的 2012 R2 的累積 Windows 更新。 有關此匯總中包含的內容的詳細資訊，請訪問[4 月每月安全匯總](https://support.microsoft.com/help/4493446/windows-8-1-update-kb4493446)。
 - **KB3011067，** 這是微軟更新獨立套裝軟體檔 WindowsTH-KB3011067-x64。 此檔案用來更新裝置軟體。

下載KB4502035，並按照這些說明[通過本地 Web UI 應用更新](storsimple-virtual-array-install-update-11.md#use-the-local-web-ui)。

## <a name="issues-fixed-in-update-12"></a>在 Update 1.2 中修正的問題

下表提供本版已修正問題的摘要。

| 否。 | 功能 | 問題 |
| --- | --- | --- |
| 1 |刪除| 在該軟體的早期版本中，即使刪除檔，設備的使用也沒有更改，因此出現問題。 此問題已在此版本中修復。 在處理已刪除的檔時，分層代碼路徑更具彈性。|
| 2 |例外狀況處理| 在軟體的早期版本中，系統重新開機後出現問題，可能導致備份、還原、從雲讀取和自動空間回收失敗。 此版本包含有關在啟動路徑中處理異常的方式的更改。|

## <a name="known-issues-in-update-12"></a>Update 1.2 中的已知問題

更新 1.2 中未注意到任何新問題。 所有發佈說明的問題都從以前的版本中結轉。 要查看以前版本中包含的已知問題的摘要，請轉到[Update 1.1 中的已知問題](storsimple-virtual-array-update-11-release-notes.md#known-issues-in-update-11)。

## <a name="next-steps"></a>後續步驟

下載KB4502035，[並通過本地 Web UI 應用更新](storsimple-virtual-array-install-update-11.md#use-the-local-web-ui)。

## <a name="references"></a>參考

要尋找舊版本資訊嗎？ 請移至：
* [StorSimple 虛擬陣列更新 1.1 版本資訊](storsimple-virtual-array-update-11-release-notes.md)
* [StorSimple Virtual Array Update 1.0 版本資訊](storsimple-virtual-array-update-1-release-notes.md)
* [StorSimple 虛擬陣列更新 0.6 版本資訊](storsimple-virtual-array-update-06-release-notes.md)
* [StorSimple Virtual Array Update 0.5 版本資訊](storsimple-virtual-array-update-05-release-notes.md)
* [StorSimple Virtual Array Update 0.4 版本資訊](storsimple-virtual-array-update-04-release-notes.md)
* [StorSimple Virtual Array Update 0.3 版本資訊](storsimple-ova-update-03-release-notes.md)
* [StorSimple Virtual Array Update 0.1 和 0.2 版本資訊](storsimple-ova-update-01-release-notes.md)
* [StorSimple Virtual Array 正式運作版版本資訊](storsimple-ova-pp-release-notes.md)
