---
title: Microsoft Azure StorSimple Virtual Array Update 1.3 版本資訊 |Microsoft Docs
description: 描述執行更新1.3 的 Azure StorSimple Virtual Array 的重大未解決問題和解決方式。
ms.service: storsimple
author: v-dalc
ms.topic: article
ms.date: 01/22/2021
ms.author: alkohli
ms.openlocfilehash: 02611bdf9689d2f62f661f558fd547ea46bd4d36
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98744384"
---
# <a name="storsimple-virtual-array-update-13-release-notes"></a>StorSimple Virtual Array 更新1.3 版本資訊

下列版本資訊指出 Microsoft Azure StorSimple Virtual Array 更新的重大未決問題和已解決問題。

版本資訊會持續更新。 發現有需要採取因應措施的重大問題時，就會加入新的內容。 部署 StorSimple Virtual Array 之前，請仔細檢閱版本資訊中所含的資訊。

更新1.3 對應于軟體版本10.0.10319.0。

> [!IMPORTANT]
> - 更新1.3 是重大更新。 強烈建議您儘快安裝。
> - 您只能在執行 Update 1.2 的裝置上安裝更新1.3。
> - 更新是干擾性作業，會重新啟動您的裝置。 如果 i/o 正在進行中，裝置會造成停機時間。 如需用來套用此更新之套件的詳細指示，請移至 [下載更新 1.3](#download-update-13)。

## <a name="whats-new-in-update-13"></a>更新1.3 的新功能

此更新包含下列改良功能：

- 傳輸層安全性 (TLS) 1.2 是強制性更新，必須安裝。 從這個版本開始，TLS 1.2 會成為所有 Azure 入口網站通訊的標準通訊協定。
- 當裝置和儲存體帳戶位於兩個遠區域時，垃圾收集錯誤修正可改善垃圾收集週期的效能。
- 修正因為 blob 超時而發生的備份失敗。
- 更新的 OS/.NET framework 安全性修補程式：
  - [KB4540725](\\winsehotfix.segroup.winse.corp.microsoft.com\hotfixes\Windows6.3\RTM\KB4540725\V1.001\free\NEU\X64)：2020年3月的 SSU (服務堆疊更新) 
  - [KB4565541](\\winsehotfix.segroup.winse.corp.microsoft.com\hotfixes\Windows6.3\RTM\KB4565541\V1.014\free\NEU\X64)：2020年7月匯總套件
  - [KB4565622](\\winsehotfix.segroup.winse.corp.microsoft.com\hotfixes\Partner\DOTNET47x\KB4565622\V1.000\free\NEU\x64)：2020年7月 .NET Framework 更新

## <a name="download-update-13"></a>下載更新1。3

若要下載此更新，請移至 [Microsoft Update 目錄](https://www.catalog.update.microsoft.com/Home.aspx) 伺服器，並下載 KB4575898 套件。 此套件包含下列套件：

- **KB4540725**，其中包含 2012 R2 的累計 Windows 更新（最多3月2020）。 如需此匯總包含之內容的詳細資訊，請移至3月的 [安全性匯總](https://support.microsoft.com/help/4540725)。
- **KB4565541**，其中包含 2012 R2 的累計 Windows 更新，最高可達7月2020。 如需此匯總包含之內容的詳細資訊，請移至 [二月的每月安全性匯總](https://support.microsoft.com/help/4565541)。
- **KB4565622**，其中包含最多2020年7月的 cumulative.NET Framework 更新。 如需此匯總包含之內容的詳細資訊，請移至 [二月的每月安全性匯總](https://support.microsoft.com/help/4565622)。
- **KB3011067**，其中包含裝置軟體更新。

下載 KB4575898，並遵循這些指示，透過 [本機 WEB UI 套用更新](./storsimple-virtual-array-install-update-11.md#use-the-local-web-ui)。

## <a name="known-issues-in-update-13"></a>更新1.3 中的已知問題
Update 1.3 中沒有發行的新問題。 所有發行記錄的問題都會從先前的版本中繼續執行。 若要查看先前版本所包含的已知問題摘要，請移至 [更新1.2 中的已知問題](./storsimple-virtual-array-update-12-release-notes.md#known-issues-in-update-12)。

## <a name="next-steps"></a>後續步驟
下載 KB4575898，並透過 [本機 WEB UI 套用更新](./storsimple-virtual-array-install-update-1.md#use-the-local-web-ui)。

## <a name="references"></a>參考資料
要尋找舊版本資訊嗎？ 請移至：

- [StorSimple Virtual Array 更新1.2 版本資訊](./storsimple-virtual-array-update-12-release-notes.md)
- [StorSimple Virtual Array Update 1.0 版本資訊](./storsimple-virtual-array-update-1-release-notes.md)
- [StorSimple Virtual Array 更新0.6 版本資訊](./storsimple-virtual-array-update-06-release-notes.md)
- [StorSimple Virtual Array Update 0.5 版本資訊](./storsimple-virtual-array-update-05-release-notes.md)
- [StorSimple Virtual Array Update 0.4 版本資訊](./storsimple-virtual-array-update-04-release-notes.md)
- [StorSimple Virtual Array Update 0.3 版本資訊](./storsimple-ova-update-03-release-notes.md)
- [StorSimple Virtual Array Update 0.1 和 0.2 版本資訊](./storsimple-ova-update-01-release-notes.md)
- [StorSimple Virtual Array 正式運作版版本資訊](https://review.docs.microsoft.com/en-us/azure/storsimple/storsimple-ova-pp-release-notes)