---
title: Microsoft Azure StorSimple Virtual Array Update 1.2 版本資訊 |Microsoft Docs
description: 描述執行更新1.2 之 StorSimple Virtual Array 的重大未解決問題和解決方法。
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: article
ms.date: 05/29/2019
ms.author: alkohli
ms.openlocfilehash: ea7e4801dfaad533403c0f927a03735ae409cc52
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "66420599"
---
# <a name="storsimple-virtual-array-update-12-release-notes"></a>StorSimple Virtual Array 更新1.2 版本資訊

下列版本資訊指出 Microsoft Azure StorSimple Virtual Array 更新的重大未決問題和已解決問題。

版本資訊會持續更新。 發現有需要採取因應措施的重大問題時，就會加入新的內容。 部署 StorSimple Virtual Array 之前，請仔細檢閱版本資訊中所含的資訊。

更新1.2 對應于軟體版本**10.0.10311.0**。

> [!IMPORTANT]
> - 更新是干擾性作業，會重新啟動您的裝置。 如果 I/O 正在進行中，裝置就會停機。 如需用來套用此更新之套件的詳細指示，請移至[下載更新 1.2](#download-update-12)。
> - 只有當您的裝置執行的是 Update 1.0 或1.1 時，才可透過 Azure 入口網站取得更新1.2。

## <a name="whats-new-in-update-12"></a>Update 1.2 的新功能

此更新包含下列錯誤修正：

- 改善處理已刪除檔案時的復原能力。
- 改善程式碼啟動路徑中的處理例外狀況，導致備份、還原、雲端讀取和自動化空間回收失敗。

## <a name="download-update-12"></a>下載更新1。2

若要下載此更新，請移至[Microsoft Update 目錄](https://www.catalog.update.microsoft.com/Home.aspx)伺服器，並下載 KB4502035 套件。 此套件包含下列套件：

 - 包含 2012 R2 累積 Windows 更新的**KB4493446** ，最多4月2019。 如需此匯總套件中包含內容的詳細資訊，請移至[四月的每月安全性匯總](https://support.microsoft.com/help/4493446/windows-8-1-update-kb4493446)。
 - **KB3011067** ，這是一種 Microsoft Update 獨立封裝檔案 WINDOWSTH-KB3011067-x64。 此檔案用來更新裝置軟體。

下載 KB4502035，並遵循這些指示，透過[本機 WEB UI](storsimple-virtual-array-install-update-11.md#use-the-local-web-ui)來套用更新。

## <a name="issues-fixed-in-update-12"></a>在 Update 1.2 中修正的問題

下表提供本版已修正問題的摘要。

| 不會。 | 功能 | 問題 |
| --- | --- | --- |
| 1 |刪除| 在舊版的軟體中，即使檔案已刪除，裝置的使用量也不會發生問題。 此版本已修正此問題。 處理已刪除的檔案時，分層程式碼路徑會變得更有彈性。|
| 2 |例外狀況處理| 在舊版的軟體中，系統重新開機之後發生問題，可能會導致備份失敗、還原、從雲端讀取，以及自動回收空間。 此版本包含在啟動路徑中處理例外狀況的方式變更。|

## <a name="known-issues-in-update-12"></a>Update 1.2 中的已知問題

未發行任何新問題-在更新1.2 中注明。 所有發行-注明的問題都會從舊版中繼續執行。 若要查看先前版本所包含的已知問題摘要，請移至[更新1.1 中的已知問題](storsimple-virtual-array-update-11-release-notes.md#known-issues-in-update-11)。

## <a name="next-steps"></a>後續步驟

下載 KB4502035，並透過[本機 WEB UI 套用更新](storsimple-virtual-array-install-update-11.md#use-the-local-web-ui)。

## <a name="references"></a>參考資料

要尋找舊版本資訊嗎？ 請移至：
* [StorSimple Virtual Array 更新1.1 版本資訊](storsimple-virtual-array-update-11-release-notes.md)
* [StorSimple Virtual Array Update 1.0 版本資訊](storsimple-virtual-array-update-1-release-notes.md)
* [StorSimple Virtual Array 更新0.6 版本資訊](storsimple-virtual-array-update-06-release-notes.md)
* [StorSimple Virtual Array Update 0.5 版本資訊](storsimple-virtual-array-update-05-release-notes.md)
* [StorSimple Virtual Array Update 0.4 版本資訊](storsimple-virtual-array-update-04-release-notes.md)
* [StorSimple Virtual Array Update 0.3 版本資訊](storsimple-ova-update-03-release-notes.md)
* [StorSimple Virtual Array Update 0.1 和 0.2 版本資訊](storsimple-ova-update-01-release-notes.md)
* [StorSimple Virtual Array 正式運作版版本資訊](storsimple-ova-pp-release-notes.md)
