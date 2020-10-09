---
title: 版本發行記錄
titleSuffix: Microsoft Genomics
description: Microsoft Genomics Python 用戶端更新的發行歷程記錄，以取得修正和新功能。
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: conceptual
ms.date: 01/11/2019
ms.openlocfilehash: 20475e2cde1b42790740889cf341b3a0a0afccc0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "76991077"
---
# <a name="version-release-history"></a>版本發行記錄
Microsoft Genomics 小組會定期更新 Microsoft Genomics Python 用戶端，以進行修正並推出新功能。 

## <a name="latest-release"></a>最新版本
目前的 Python 用戶端是0.9.0 版。 它已于 6 2019 年2月發行，並支援使用 GATK 3.5 和 GATK4 來執行工作流程。 它支援 gVCF 輸出，而且可以接受選擇性引數以進行輸出壓縮。


## <a name="release-history"></a>版本歷程記錄 
Microsoft Genomics Python 用戶端的新版本會每年發行一次。 新版 Microsoft Genomics Python 用戶端發行時，此處就會更新修正程式和功能的清單。 新版本發行時，舊版本應該會繼續支援至少 90 天。 當舊版停止支援時，此頁面便會指出這一點。 

### <a name="version-090"></a>版本0.9。0
版本0.9.0 包含對輸出壓縮的支援。 這相當於 `-bgzip` `-tabix` 在 .vcf 或 gvcf 輸出上執行之後。 如需詳細資訊，請參閱[常見問題集](frequently-asked-questions-genomics.md)。 

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
