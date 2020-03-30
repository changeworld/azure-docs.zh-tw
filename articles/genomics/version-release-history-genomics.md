---
title: 版本發行歷程記錄
titleSuffix: Microsoft Genomics
description: Microsoft 基因組 Python 用戶端的更新版本歷程記錄，用於修復和新功能。
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: conceptual
ms.date: 01/11/2019
ms.openlocfilehash: 20475e2cde1b42790740889cf341b3a0a0afccc0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76991077"
---
# <a name="version-release-history"></a>版本發行歷程記錄
Microsoft Genomics 小組會定期更新 Microsoft Genomics Python 用戶端，以進行修正並推出新功能。 

## <a name="latest-release"></a>最新版本
當前 Python 用戶端是版本 0.9.0。 它于 2019 年 2 月 6 日發佈，支援使用 GATK 3.5 和 GATK4 運行工作流。 它支援 gVCF 輸出，可以接受輸出壓縮的可選參數。


## <a name="release-history"></a>版本歷程記錄 
微軟基因組 Python 用戶端的新版本大約每年發佈一次。 新版 Microsoft Genomics Python 用戶端發行時，此處就會更新修正程式和功能的清單。 新版本發行時，舊版本應該會繼續支援至少 90 天。 當舊版停止支援時，此頁面便會指出這一點。 

### <a name="version-090"></a>版本 0.9.0
版本 0.9.0 支援輸出壓縮。 這相當於在 vcf`-bgzip`或`-tabix`gvcf 輸出上運行後跟。 有關詳細資訊，請參閱[常見問題](frequently-asked-questions-genomics.md)。 

### <a name="version-081"></a>0.8.1 版
0.8.1 版包含次要錯誤 (bug) 的修正。  

### <a name="version-080"></a>0.8.0 版
0.8.0 版包含 GATK4 及輸出 gVCF 的支援。  

### <a name="version-074"></a>0.7.4 版
0.7.4 版支援接受 SAS 權杖，而不是 `config.txt` 輸入中的帳戶金鑰。 如需詳細資訊，請參閱[輸入 SAS 權杖快速入門](quickstart-input-sas.md)。 

### <a name="version-073"></a>0.7.3 版
0.7.3 版包含次要錯誤 (bug) 的修正。

### <a name="version-072"></a>0.7.2 版
0.7.2 版是初始版本。 發行日期為 2017 年 11 月 1 日。
