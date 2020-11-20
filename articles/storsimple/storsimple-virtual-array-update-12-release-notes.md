---
title: Microsoft Azure StorSimple Virtual Array Update 1.2 版本資訊 |Microsoft Docs
description: 描述執行更新1.2 之 StorSimple Virtual Array 的重大開啟問題和解決方式。
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: article
ms.date: 05/29/2019
ms.author: alkohli
ms.openlocfilehash: 1118dfcec67a49365f1f6e5e522e98b97694d052
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2020
ms.locfileid: "94960184"
---
# <a name="storsimple-virtual-array-update-12-release-notes"></a>StorSimple Virtual Array 更新1.2 版本資訊

下列版本資訊指出 Microsoft Azure StorSimple Virtual Array 更新的重大未決問題和已解決問題。

版本資訊會持續更新。 發現有需要採取因應措施的重大問題時，就會加入新的內容。 部署 StorSimple Virtual Array 之前，請仔細檢閱版本資訊中所含的資訊。

更新1.2 對應于軟體版本 **10.0.10311.0**。

> [!IMPORTANT]
> - 更新是干擾性作業，會重新啟動您的裝置。 如果 I/O 正在進行中，裝置就會停機。 如需用來套用此更新之套件的詳細指示，請移至 [下載更新 1.2](#download-update-12)。
> - 只有當您的裝置正在執行 Update 1.0 或1.1 時，才可透過 Azure 入口網站使用更新1.2。

## <a name="whats-new-in-update-12"></a>Update 1.2 的新功能

此更新包含下列錯誤修正：

- 改進處理已刪除檔案時的復原能力。
- 改善程式碼啟動路徑中的處理例外狀況，進而減少備份、還原、雲端讀取和自動化空間回收中的失敗。

## <a name="download-update-12"></a>下載更新1。2

若要下載此更新，請移至 [Microsoft Update 目錄](https://www.catalog.update.microsoft.com/Home.aspx) 伺服器，並下載 KB4502035 套件。 此套件包含下列套件：

 - **KB4493446** ，其中包含 2012 R2 的累計 Windows 更新（最多4月2019）。 如需此匯總包含內容的詳細資訊，請移至 [4 月的安全性匯總](https://support.microsoft.com/help/4493446/windows-8-1-update-kb4493446)。
 - **KB3011067** ，它是 Microsoft Update 獨立套件檔案 WINDOWSTH-KB3011067-x64。 此檔案用來更新裝置軟體。

下載 KB4502035，並遵循這些指示，透過 [本機 WEB UI 套用更新](storsimple-virtual-array-install-update-11.md#use-the-local-web-ui)。

## <a name="issues-fixed-in-update-12"></a>在 Update 1.2 中修正的問題

下表提供本版已修正問題的摘要。

| 否。 | 功能 | 問題 |
| --- | --- | --- |
| 1 |刪除| 在舊版的軟體中，即使在刪除檔案時，裝置的使用沒有變更也會發生問題。 此版本已修正此問題。 處理已刪除的檔案時，將程式碼路徑分層變得更有彈性。|
| 2 |例外狀況處理| 在舊版的軟體中，系統重新開機後發生問題，可能會導致備份失敗、還原、從雲端讀取，以及自動空間回收。 此版本包含在啟動路徑中處理例外狀況的方式變更。|

## <a name="known-issues-in-update-12"></a>Update 1.2 中的已知問題

Update 1.2 中沒有發行的新問題。 所有發行記錄的問題都會從先前的版本中繼續執行。 若要查看先前版本所包含的已知問題摘要，請移至 [更新1.1 中的已知問題](storsimple-virtual-array-update-11-release-notes.md#known-issues-in-update-11)。

## <a name="next-steps"></a>後續步驟

下載 KB4502035，並透過 [本機 WEB UI 套用更新](storsimple-virtual-array-install-update-11.md#use-the-local-web-ui)。

## <a name="references"></a>參考

要尋找舊版本資訊嗎？ 請移至：
* [StorSimple Virtual Array 更新1.1 版本資訊](storsimple-virtual-array-update-11-release-notes.md)
* [StorSimple Virtual Array Update 1.0 版本資訊](storsimple-virtual-array-update-1-release-notes.md)
* [StorSimple Virtual Array 更新0.6 版本資訊](storsimple-virtual-array-update-06-release-notes.md)
* [StorSimple Virtual Array Update 0.5 版本資訊](storsimple-virtual-array-update-05-release-notes.md)
* [StorSimple Virtual Array Update 0.4 版本資訊](storsimple-virtual-array-update-04-release-notes.md)
* [StorSimple Virtual Array Update 0.3 版本資訊](storsimple-ova-update-03-release-notes.md)
* [StorSimple Virtual Array Update 0.1 和 0.2 版本資訊](storsimple-ova-update-01-release-notes.md)
* [StorSimple Virtual Array 正式運作版版本資訊](./storsimple-virtual-array-update-06-release-notes.md)