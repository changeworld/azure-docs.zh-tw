---
title: 了解最新的 Azure 客體 OS 版次 | Microsoft Docs
description: Azure 雲端服務客體作業系統的發行最新消息和 SDK 相容性。
services: cloud-services
documentationcenter: na
author: raiye
editor: ''
ms.assetid: 6306cafe-1153-44c7-8554-623b03d59a34
ms.service: cloud-services
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 4/2/2020
ms.author: raiye
ms.openlocfilehash: 95c27cd906717d52a232b5ed85eba2b93bc6569d
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618283"
---
# <a name="azure-guest-os-releases-and-sdk-compatibility-matrix"></a>Azure 客體 OS 版次與 SDK 相容性矩陣
提供適用於雲端服務的最新 Azure 客體作業系統版次的最新資訊。 此資訊協助您在客體 OS停用之前規劃升級路徑。 如果您將角色設定成使用「自動」** 客體 OS 更新 (如 [Azure 客體 OS 更新設定][Azure Guest OS Update Settings]所述)，就不一定要閱讀此頁面。

> [!IMPORTANT]
> 此頁面適用於客體作業系統上執行的雲端服務 Web 角色和背景工作角色。 不適用 **** 於 IaaS 虛擬機器。
>
>


> [!TIP]
>  請訂閱[客體 OS 更新 RSS 摘要]，以接收有關所有客體 OS 變更的最及時性通知。
>
>

> [!IMPORTANT]
> 僅支援最新 2 版的客體 OS，並可在 Azure 入口網站取得。
>
>

不確定如何更新客體作業系統嗎？ 請查看[這裡][cloud updates]。

## <a name="news-updates"></a>新聞更新

###### <a name="april-2-2020"></a>**2020年4月2日**
3 月客體 OS 已發行。 

###### <a name="march-5-2020"></a>**2020年3月5日**
2 月客體 OS 已發行。 

###### <a name="january-24-2020"></a>**2020年1月24日**
1 月客體 OS 已發行。 

###### <a name="january-8-2020"></a>**2020年1月8日**
12 月客體 OS 已發行。

###### <a name="december-5-2019"></a>**2019 年 12 月 5 日**
11 月客體 OS 已發行。

###### <a name="november-1-2019"></a>**2019 年 11 月 1 日**
10 月客體 OS 已發行。

###### <a name="october-7-2019"></a>**2019年10月7日**
9 月客體 OS 已發行。

###### <a name="september-4-2019"></a>**2019 年 9 月 4 日**
8 月客體 OS 已發行。

###### <a name="july-26-2019"></a>**2019 年 7 月 26 日**
7 月客體 OS 已發行。

###### <a name="july-8-2019"></a>**2019年7月8日**
6 月客體 OS 已發行。

###### <a name="june-6-2019"></a>**六月 6, 2019**
5 月份客體作業系統已發行。

###### <a name="may-7-2019"></a>**2019年5月7日**
4 月客體 OS 已發行。

###### <a name="march-26-2019"></a>**2019 年 3 月 26 日**
3 月客體 OS 已發行。

###### <a name="march-12-2019"></a>**2019 年 3 月 12日**
2 月客體 OS 已發行。

###### <a name="february-5-2019"></a>**2019 年 2 月 5 日**
1 月客體 OS 已發行。

###### <a name="january-24-2019"></a>**2019 年 1 月 24 日**
系列 6 客體 OS (Windows Server 2019) 已發行。

###### <a name="january-7-2019"></a>**2019 年 1 月 7 日**
12 月客體 OS 已發行。

###### <a name="december-14-2018"></a>**2018 年 12 月 14 日**
11 月客體 OS 已發行。

###### <a name="november-8-2018"></a>**2018 年 11 月 8 日**
10 月客體 OS 已發行。

###### <a name="october-12-2018"></a>**2018 年 10 月 12 日**
9 月客體 OS 已發行。

## <a name="releases"></a>版次

## <a name="family-6-releases"></a>系列 6 版次
**Windows Server 2019**

已安裝 .NET 框架:3.5, 4.7.2

> [!NOTE]
> 您可以從[這裡][Windows Azure SDK]下載 Windows Azure SDK for .NET - 3.0。
>
>安裝步驟：
>1. 請卸載任何舊版本的 MicrosoftAzure 創作工具*.msi
>2. 安裝[.NET 的 Azure SDK - 3.0][Windows Azure SDK]
>3. 重新啟動電腦
>4. 建立新的雲端服務專案並新增單一次建立角色
>5. 將作業系統系列變更為 6 並產生套件
>6. 使用 Azure 門戶或視覺化工作室將包部署到 Azure
>
>來賓 OS 系列 6 版本透過顯式禁用 TLS 1.0 和 1.1 並定義一組特定的密碼套件來強制執行 TLS 1.2。 [瞭解更多]。


| 組態字串 | 發行日期 | 停用日期 |
| --- | --- | --- |
|  WA-GUEST-OS-6.17_202003-01  |  2020年4月2日  |  帖子 6.19  |  
|  WA-GUEST-OS-6.16_202002-01  |  2020年3月5日  |  帖子 6.18  |  
|~~WA-GUEST-OS-6.15_202001-01~~|  2020年1月24日  |  2020年4月2日  |  
|~~WA-GUEST-OS-6.14_201912-01~~| 2020年1月8日 | 2020年3月5日 |  
|~~WA-GUEST-OS-6.13_201911-01~~| 2019 年 12 月 5 日 | 2020年1月24日 |  
|~~WA-GUEST-OS-6.12_201910-01~~| 2019 年 11 月 1 日 | 2020年1月8日 |  
|~~WA-GUEST-OS-6.11_201909-01~~| 2019年10月7日 | 2019 年 12 月 5 日 |  
|~~WA-GUEST-OS-6.10_201908-01~~| 2019年8月4日 | 2019 年 11 月 1 日  |  
|~~WA-GUEST-OS-6.9_201907-0~~|2019 年 7 月 26 日 | 2019年10月7日 |
|~~WA-GUEST-OS-6.8_201906-01~~|2019年7月8日 |2019年8月4日 |
|~~WA-GUEST-OS-6.7_201905-01~~ |六月 6, 2019 |2019 年 7 月 26 日 |
|~~WA-GUEST-OS-6.6_201904-01~~ |2019年5月7日 |2019年7月8日 |
|~~WA-GUEST-OS-6.5_201903-01~~ |2019 年 3 月 26 日 |六月 6, 2019 |
|~~WA-GUEST-OS-6.4_201902-01~~ |2019 年 3 月 12日 |2019年5月7日 |
|~~WA-GUEST-OS-6.3_201901-01~~ |2019 年 2 月 5 日 |2019 年 3 月 26 日 |
|~~WA-GUEST-OS-6.2_201812-01~~ |2019 年 1 月 24 日 |2019 年 3 月 12日 |
|~~WA-GUEST-OS-6.1_201811-01~~ |2019 年 1 月 24 日 |2019 年 2 月 5 日 |

## <a name="family-5-releases"></a>系列 5 版次
**Windows Server 2016**

已安裝 .NET 框架:3.5, 4.6

> [!NOTE]
> 作業系統系列 5 的 RDP 密碼至少需要 10 個字元。
>


| 組態字串 | 發行日期 | 停用日期 |
| --- | --- | --- |
|  WA-GUEST-OS-5.41_202003-01  |  2020年4月2日  |  帖子 5.43  |  
|  WA-GUEST-OS-5.40_202002-01  |  2020年3月5日  |  帖子 5.42  |  
|~~WA-GUEST-OS-5.39_202001-01~~|  2020年1月24日  |  2020年4月2日  |  
|~~WA-GUEST-OS-5.38_201912-01~~| 2020年1月8日 | 2020年3月5日 |  
|~~WA-GUEST-OS-5.37_201911-01~~| 2019 年 12 月 5 日 | 2020年1月24日 |  
|~~WA-GUEST-OS-5.36_201910-01~~| 2019 年 11 月 1 日 | 2020年1月8日 |  
|~~WA-GUEST-OS-5.35_201909-01~~| 2019年10月7日 | 2019 年 12 月 5 日 |  
|~~WA-GUEST-OS-5.34_201908-01~~|  2019年8月4日  | 2019 年 11 月 1 日 |  
|~~WA-GUEST-OS-5.33_201907-01~~| 2019 年 7 月 26 日 | 2019年10月7日 |  
|~~WA-GUEST-OS-5.32_201906-01~~|2019年7月8日 |2019年8月4日 |
|~~WA-GUEST-OS-5.31_201905-01~~ |六月 6, 2019 |2019 年 7 月 26 日 |
|~~WA-GUEST-OS-5.30_201904-01~~ |2019年5月7日 |2019年7月8日 |
|~~WA-GUEST-OS-5.29_201903-01~~ |2019 年 3 月 26 日 |六月 6, 2019 |
|~~WA-GUEST-OS-5.28_201902-01~~ |2019 年 3 月 12日 |2019年5月7日 |
|~~WA-GUEST-OS-5.27_201901-01~~ |2019 年 2 月 5 日 |2019 年 3 月 26 日 |
|~~WA-GUEST-OS-5.26_201812-01~~ |2019 年 1 月 7 日 |2019 年 3 月 12日 |
|~~WA-GUEST-OS-5.25_201811-01~~ |2018 年 12 月 14 日 |2019 年 2 月 5 日 |
|~~WA-GUEST-OS-5.24_201810-01~~ |2018 年 11 月 8 日 |2019 年 1 月 7 日 |
|~~WA-GUEST-OS-5.23_201809-01~~ |2018 年 10 月 12 日 |2018 年 12 月 14 日 |

## <a name="family-4-releases"></a>系列 4 版次
**Windows Server 2012 R2**

已安裝 .NET 框架:3.5、4.5.1、4.5.2

| 組態字串 | 發行日期 | 停用日期 |
| --- | --- | --- |
|  WA-GUEST-OS-4.76_202003-01  |  2020年4月2日  |  帖子 4.78  |  
|  WA-GUEST-OS-4.75_202002-01  |  2020年3月5日  |  帖子 4.77  |  
|~~WA-GUEST-OS-4.74_202001-01~~|  2020年1月24日  |  2020年4月2日  |  
|~~WA-GUEST-OS-4.73_201912-01~~| 2020年1月8日 | 2020年3月5日 |  
|~~WA-GUEST-OS-4.72_201911-01~~| 2019 年 12 月 5 日 | 2020年1月24日 |  
|~~WA-GUEST-OS-4.71_201910-01~~| 2019 年 11 月 1 日 | 2020年1月8日 |  
|~~WA-GUEST-OS-4.70_201909-01~~| 2019年10月7日 | 2019 年 12 月 5 日 |  
|~~WA-GUEST-OS-4.69_201908-01~~| 2019年8月4日 | 2019 年 11 月 1 日 |  
|~~WA-GUEST-OS-4.68_201907-01~~| 2019 年 7 月 26 日  | 2019年10月7日 |
|~~WA-GUEST-OS-4.67_201906-01~~| 2019年7月8日 |2019年8月4日 |
|~~WA-GUEST-OS-4.66_201905-01~~ |六月 6, 2019 |2019 年 7 月 26 日 |
|~~WA-GUEST-OS-4.65_201904-01~~ |2019年5月7日 |2019年7月8日 |
|~~WA-GUEST-OS-4.64_201903-01~~ |2019 年 3 月 26 日 |六月 6, 2019 |
|~~WA-GUEST-OS-4.63_201902-01~~ |2019 年 3 月 12日 |2019年5月7日 |
|~~WA-GUEST-OS-4.62_201901-01~~ |2019 年 2 月 5 日 |2019 年 3 月 26 日 |
|~~WA-GUEST-OS-4.61_201812-01~~ |2019 年 1 月 7 日 |2019 年 3 月 12日 |
|~~WA-GUEST-OS-4.60_201811-01~~ |2018 年 12 月 14 日 |2019 年 2 月 5 日 |
|~~WA-GUEST-OS-4.59_201810-01~~ |2018 年 11 月 8 日 |2019 年 1 月 7 日 |
|~~WA-GUEST-OS-4.58_201809-01~~ |2018 年 10 月 12 日 |2018 年 12 月 14 日 |

## <a name="family-3-releases"></a>系列 3 版次
**視窗伺服器 2012**

安裝的 .NET Framework：3.5、4.5

| 組態字串 | 發行日期 | 停用日期 |
| --- | --- | --- |
|  WA-GUEST-OS-3.83_202003-01  |  2020年4月2日  |  帖子 3.85  |  
|  WA-GUEST-OS-3.82_202002-01  |  2020年3月5日  |  帖子 3.84  |  
|~~WA-GUEST-OS-3.81_202001-01~~|  2020年1月24日  |  2020年4月2日  |  
|~~WA-GUEST-OS-3.80_201912-01~~| 2020年1月8日 | 2020年3月5日 |  
|~~WA-GUEST-OS-3.79_201911-01~~| 2019 年 12 月 5 日 | 2020年1月24日 |  
|~~WA-GUEST-OS-3.78_201910-01~~| 2019 年 11 月 1 日 | 2020年1月8日 |  
|~~WA-GUEST-OS-3.77_201909-01~~| 2019年10月7日 | 2019 年 12 月 5 日 |  
|~~WA-GUEST-OS-3.76_201908-01~~|  2019年8月4日  |  2019 年 11 月 1 日  |  
|~~WA-GUEST-OS-3.75_201907-01~~| 2019 年 7 月 26 日 | 2019年10月7日 |
|~~WA-GUEST-OS-3.74_201906-01~~| 2019年7月8日 |2019年8月4日 |
|~~WA-GUEST-OS-3.73_201905-01~~ |六月 6, 2019 |2019 年 7 月 26 日 |
|~~WA-GUEST-OS-3.72_201904-01~~ |2019年5月7日 |2019年7月8日 |
|~~WA-GUEST-OS-3.71_201903-01~~ |2019 年 3 月 26 日 |六月 6, 2019 |
|~~WA-GUEST-OS-3.70_201902-01~~ |2019 年 3 月 12日 |2019年5月7日 |
|~~WA-GUEST-OS-3.69_201901-01~~ |2019 年 2 月 5 日 |2019 年 3 月 26 日 |
|~~WA-GUEST-OS-3.68_201812-01~~ |2019 年 1 月 7 日 |2019 年 3 月 12日 |
|~~WA-GUEST-OS-3.67_201811-01~~ |2018 年 12 月 14 日 |2019 年 2 月 5 日 |
|~~WA-GUEST-OS-3.66_201810-01~~ |2018 年 11 月 8 日 |2019 年 1 月 7 日 |
|~~WA-GUEST-OS-3.65_201809-01~~ |2018 年 10 月 12 日 |2018 年 12 月 14 日 |

## <a name="family-2-releases"></a>系列 2 版次
**Windows 伺服器 2008 R2 SP1**

已安裝 .NET 框架:3.5(包括 2.0 和 3.0),4.5

| 組態字串 | 發行日期 | 停用日期 |
| --- | --- | --- |
|  WA-GUEST-OS-2.96_202003-01  |  2020年4月2日  |  帖子 2.98  |  
|  WA-GUEST-OS-2.95_202002-01  |  2020年3月5日  |  帖子 2.97  |  
|~~WA-GUEST-OS-2.94_202001-01~~|  2020年1月24日  |  2020年4月2日  |  
|~~WA-GUEST-OS-2.93_201912-01~~| 2020年1月8日 | 2020年3月5日 |  
|~~WA-GUEST-OS-2.92_201911-01~~| 2019 年 12 月 5 日 | 2020年1月24日 |  
|~~WA-GUEST-OS-2.91_201910-01~~| 2019 年 11 月 1 日 | 2020年1月8日 |  
|~~WA-GUEST-OS-2.90_201909-01~~| 2019年10月7日 | 2019 年 12 月 5 日 |  
|~~WA-GUEST-OS-2.89_201908-01~~| 2019年8月4日 | 2019 年 11 月 1 日 |  
|~~WA-GUEST-OS-2.88_201907-01~~| 2019 年 7 月 26 日 | 2019年10月7日 |
|~~WA-GUEST-OS-2.87_201906-01~~|2019年7月8日 | 2019年8月4日 |
|~~WA-GUEST-OS-2.86_201905-01~~ |六月 6, 2019 |2019 年 7 月 26 日 |
|~~WA-GUEST-OS-2.85_201904-01~~ |2019年5月7日 |2019年7月8日 |
|~~WA-GUEST-OS-2.84_201903-01~~ |2019 年 3 月 26 日 |六月 6, 2019 |
|~~WA-GUEST-OS-2.83_201902-01~~ |2019 年 3 月 12日 |2019年5月7日 |
|~~WA-GUEST-OS-2.82_201901-01~~ |2019 年 2 月 5 日 |2019 年 3 月 26 日 |
|~~WA-GUEST-OS-2.81_201812-01~~ |2019 年 1 月 7 日 |2019 年 3 月 12日 |
|~~WA-GUEST-OS-2.80_201811-01~~ |2018 年 12 月 14 日 |2019 年 2 月 5 日 |
|~~WA-GUEST-OS-2.79_201810-01~~ |2018 年 11 月 8 日 |2019 年 1 月 7 日 |
|~~WA-GUEST-OS-2.78_201809-01~~ |2018 年 10 月 12 日 |2018 年 12 月 14 日 |

## <a name="msrc-patch-updates"></a>MSRC 修補程式更新
[這裡][patches]提供每月客體 OS 版次隨附的修補程式清單。

## <a name="sdk-support"></a>SDK 支援
雖然 [Azure SDK 的淘汰原則][retire policy sdk]指出只支援 2.2 以上的版本，但特定客體 OS 系列仍允許您使用更舊的版本。 您應該一律使用最新版本的支援 SDK。

| 客體 OS 系列 | 相容的 SDK 版本 |
| --- | --- |
| 6 |版本 2.9.6+ |
| 5 |版本 2.9.5.1+ |
| 4 |版本 2.1+ |
| 3 |版本 1.8+ |
| 2 |版本 1.3+ |
| 1 |版本 1.0+ |

## <a name="guest-os-release-information"></a>客體 OS 版次資訊
有三個重要的客體 OS 版次日期：**發行**日期、**停用**日期，以及**到期**日期。 客體 OS 在入口網站時會視為可用，且可以選擇作為目標客體 OS。 當客體 OS 觸達**停用**日期時，會從 Azure 中移除。 不過，以該客體 OS 為目標的任何雲端服務將仍正常運作。

**停用**日期和**到期**日期之間的時間範圍，提供一個緩衝區，讓您可以輕鬆從一個客體 OS 轉換到到較新的客體 OS。 如果您使用「自動化」 ** 作為客體 OS，則一律會是最新版本，不必擔心過期問題。

當**到期**日期已過，任何仍在使用該客體 OS 的雲端服務將會遭到停止、刪除或強制升級。 您可以從[這裡][retirepolicy]閱讀更多有關淘汰原則的資訊。

## <a name="guest-os-family-version-explanation"></a>客體 OS 系列版本說明
客體作業系統系列以 Microsoft Windows Server 的發行版本為基礎。 客體作業系統是指執行 Azure 雲端服務的基礎作業系統。 每一個客體作業系統都有系列、版本和版次號碼。

* **Guest OS family**  
  是客體 OS 所根據的 Windows Server 作業系統版次。 例如，「系列 3」 ** 以 Windows Server 2012 為基礎。
* **客體 OS 版本**  
  客體 OS 系列映像特定，加上新客體 OS 版本產生當天可用的相關 [Microsoft Security Response Center (MSRC)][msrc] 修補程式。 不一定包含所有修補程式。

    編號從 0 開始，每增加一組新的更新就加 1。 必要時才會顯示尾端的零。 亦即，2.10 版是與 2.1 版不同且更新的版本。
* **客體 OS 版次**  
  是指客體 OS 版本的再發行版次。 如果 Microsoft 在測試期間發現問題而需要變更時，就會推出再發行版次。 最新的版次一律取代任何先前的版次，無論是否公開都一樣。 Azure 入口網站只允許使用者挑選特定版本的最新版次。 視錯誤的嚴重性而定，通常不會強制升級舊版次上執行的部署。

在下列範例中，2 是系列，12 是版本，"rel2" 是版次。

**客體作業系統版次** - 2.12 rel2

**此版次的組態字串** - WA-GUEST-OS-2.12_201208-02

客體作業系統的組態字串內嵌這項相同的資訊，還附上指出該版次考量哪些 MSRC 修補程式的日期。 在此範例中，針對 Widows Server 2008 R2 推出的 MSRC 修補程式 (達到且包含 2012 年 8 月) 已考量納入。 只會包含明確適用於該 Windows Server 版本的修補程式。 例如，如果某個 MSRC 修補程式適用於 Microsoft Office，則不會納入，因為該產品不是 Windows Server 基本映像的一部分。

## <a name="guest-os-system-update-process"></a>客體 OS 系統更新程序
此頁面包含即將推出的客體作業系統版次的相關資訊。 客戶表示想要知道何時推出版次，因為他們的雲端服務角色如果設為「自動」更新，將重新啟動。 在每月第二個星期二發行 MSRC 更新後，通常每 2-3 週才會推出客體 OS 版次。 新版次包含每個客體作業系統系列的相關 MSRC 修補程式。

Microsoft Azure 正持續發行更新。 客體作業系統只是這過程中的一項更新。 影響版次的因素太多，無法在這裡完整列出。 此外，Azure 實際上是在成千上萬個電腦上執行。 這表示不可能指出您的角色重新啟動的確切日期和時間。 我們正進行規劃來限制或控制重新開機時間。

新版次的客體作業系統發行時，需要一段時間才會完全傳播到整個 Azure。 當服務更新到新的客體作業系統時，它們會重新啟動，讓更新網域生效。 設為「自動」更新的服務會最先取得一個版次。 更新之後，您在 Azure 入口網站中會看到您的服務列出新的客體作業系統版本。 此期間可能再度發行版次。 某些版本的部署期間可能很長，而且在官方發行日期之後，可能經過許多個星期都還不會進行自動升級重新啟動。 當客體作業系統推出時，您可以從入口網站或組態檔中明確地選擇該版本。

如需有關重新啟動的大量實用資訊，以及客體和主機 OS 更新的更多技術細節線索，請參閱標題為[角色執行個體因作業系統升級而重新啟動 (英文)][restarts] 的 MSDN 部落格文章。

如果您手動更新客體 OS，請參閱[客體 OS 淘汰原則][retirepolicy]以取得詳細資訊。

## <a name="guest-os-supportability-and-retirement-policy"></a>客體 OS 支援能力和淘汰原則
如需客體 OS 可支援性和淘汰原則的說明，請參閱[這裡][retirepolicy]。

[cloud updates]: https://docs.microsoft.com/azure/cloud-services/cloud-services-update-azure-service
[客體 OS 更新 RSS 摘要]: https://raw.githubusercontent.com/MicrosoftDocs/azure-cloud-services-files/master/GuestOS/GuestOSFeed.xml
[Install .NET on a Cloud Service Role]: https://azure.microsoft.com/documentation/articles/cloud-services-dotnet-install-dotnet/?WT.mc_id=azurebg_email_Trans_963_RevisedNET_Update
[Azure Guest OS Update Settings]: cloud-services-how-to-configure-portal.md
[ssl3 announcement]: https://azure.microsoft.com/blog/2014/12/09/azure-security-ssl-3-0-update/
[Microsoft Security Advisory 3009008]: https://technet.microsoft.com/library/security/3009008.aspx
[ssl3-fixit]: https://go.microsoft.com/?linkid=9863266
[MS14-066]: https://technet.microsoft.com/library/security/ms14-066.aspx
[MS14-046]: https://technet.microsoft.com/library/security/ms14-046.aspx
[retire policy sdk]: https://msdn.microsoft.com/library/dn479282.aspx
[server and gos]: https://msdn.microsoft.com/library/dn775043.aspx
[azuresupport]: https://azure.microsoft.com/support/options/
[net install pkg]: https://www.microsoft.com/download/details.aspx?id=42643
[msrc]: https://technet.microsoft.com/security/dn440717.aspx
[update guest os portal]: https://msdn.microsoft.com/library/gg433101.aspx
[update guest os svc]: https://msdn.microsoft.com/library/gg456324.aspx
[restarts]: https://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx
[patches]: cloud-services-guestos-msrc-releases.md
[retirepolicy]: cloud-services-guestos-retirement-policy.md
[fam1retire]: cloud-services-guestos-family1-retirement.md
[fix]: https://technet.microsoft.com/library/security/ms17-010.aspx
[Windows Azure SDK]: https://www.microsoft.com/en-us/download/details.aspx?id=54917
[更]: https://docs.microsoft.com/azure/cloud-services/applications-dont-support-tls-1-2  
