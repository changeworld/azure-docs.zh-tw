---
title: 疑難排解指南
titleSuffix: Microsoft Genomics
description: 深入瞭解使用 Microsoft Genomics 的疑難排解策略，包括錯誤訊息和解決方式。
keywords: 疑難排解, 錯誤, 偵錯
services: genomics
author: ruchir
ms.author: ruchir
ms.service: genomics
ms.workload: genomics
ms.topic: troubleshooting
ms.date: 10/29/2018
ms.openlocfilehash: c508c10d619cde1a16d89b446c5cfd1a3ce81daf
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96023818"
---
# <a name="troubleshooting-guide"></a>疑難排解指南

以下是使用 Microsoft Genomics 服務 (MSGEN) 時，可能發生之一些常見問題的疑難排解祕訣。

 如需與疑難排解不相關的常見問題集，請參閱[常見問題](frequently-asked-questions-genomics.md)。
## <a name="step-1-locate-error-codes-associated-with-the-workflow"></a>步驟 1：尋找與工作流程相關聯的錯誤碼

您可以透過下列方式，找到與工作流程相關聯的錯誤訊息：

1. 使用命令列並輸入 `msgen status`
2. 檢查 standardoutput.txt 的內容。

### <a name="1-using-the-command-line-msgen-status"></a>1. 使用命令列 `msgen status`

```bash
msgen status -u URL -k KEY -w ID 
```




有三個必要引數：

* URL - API 的基底 URI
* KEY - Genomics 帳戶的存取金鑰
    * 若要尋找您的 URL 和 KEY，請前往 Azure 入口網站，然後開啟您的 Microsoft Genomics 帳戶頁面。 在 [管理] 標題之下，選擇 [存取金鑰]。 您可以看到 API URL 以及存取金鑰。

  
* ID - 工作流程識別碼
    * 若要尋找您的工作流程識別碼，請輸入 `msgen list` 命令。 假設您的設定檔包含 URL 和存取金鑰，且與 msgen exe 位在相同的位置，則命令看起來會像這樣： 
        
        ```bash
        msgen list -f "config.txt"
        ```
        此命令的輸出看起來會像這樣：
        
        ```bash
            Microsoft Genomics command-line client v0.7.4
                Copyright (c) 2018 Microsoft. All rights reserved.
                
                Workflow List
                -------------
                Total Count  : 1
                
                Workflow ID     : 10001
                Status          : Completed successfully
                Message         :
                Process         : snapgatk-20180730_1
                Description     :
                Created Date    : Mon, 27 Aug 2018 20:27:24 GMT
                End Date        : Mon, 27 Aug 2018 20:55:12 GMT
                Wall Clock Time : 0h 27m 48s
                Bases Processed : 1,348,613,600 (1 GBase)
        ```

  > [!NOTE]
  >  您也可以包含設定檔的路徑，而非直接輸入 URL 和 KEY。 如果您在命令列和設定檔中包含這些引數，命令列引數將具有優先權。  

若工作識別碼為 1001，且 config.txt 檔案放在與 msgen 可執行檔相同的路徑中，則命令看起來會像這樣：

```bash
msgen status -w 1001 -f "config.txt"
```

### <a name="2--examine-the-contents-of-standardoutputtxt"></a>2. 檢查 standardoutput.txt 的內容 
找出有問題之工作流程的輸出容器。 MSGEN 會在每個工作流程執行之後建立 `[workflowfilename].logs.zip` 資料夾。 將資料夾解壓縮以檢視其內容：

* outputFileList.txt - 工作流程期間所產生輸出檔案的清單
* standarderror.txt - 此檔案是空白的。
* standardoutput.txt - 記錄執行工作流程時的所有最上層狀態，包括錯誤。
* GATK 記錄檔 - `logs` 資料夾中的其他所有檔案

若要進行疑難排解，請檢查 standardoutput.txt 的內容，並記下出現的任何錯誤訊息。


## <a name="step-2-try-recommended-steps-for-common-errors"></a>步驟 2：嘗試適用於常見錯誤的建議步驟

本節簡短說明 Microsoft Genomics 服務 (msgen) 輸出的常見錯誤，以及您可以用來解決那些錯誤的策略。 

Microsoft Genomics 服務 (msgen) 可能擲出下列兩種錯誤：

1. 內部服務錯誤：發生在服務內部，無法藉由修正參數或輸入檔案來解決的錯誤。 有時候重新提交工作流程，可能可以修正這些錯誤。
2. 輸入錯誤：可藉由使用正確的引數或修正檔案格式來解決的錯誤。

### <a name="1-internal-service-errors"></a>1. 內部服務錯誤

內部服務錯誤不是使用者可採取動作的錯誤。 您可以重新提交工作流程，但如果沒有用，請連絡 Microsoft Genomics 支援服務

| 錯誤訊息                                                                                                                            | 建議的疑難排解步驟                                                                                                                                   |
|------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 發生內部錯誤。 請嘗試重新提交工作流程。 如果您再次看到此錯誤，請連絡 Microsoft Genomics 支援服務以取得協助 | 再次提交工作流程。 如果問題持續發生，請建立支援[票證](file-support-ticket-genomics.md )來連絡 Microsoft Genomics 支援服務以取得協助。 |

### <a name="2-input-errors"></a>2. 輸入錯誤

這些錯誤是使用者可採取動作的錯誤。 根據檔案類型和錯誤碼，Microsoft Genomics 服務輸出不同的錯誤碼。 請遵循以下列出的建議疑難排解步驟。

| 檔案類型 | 錯誤碼 | 錯誤訊息                                                                           | 建議的疑難排解步驟                                                                                         |
|--------------|------------|-----------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------|
| 任意          | 701        | 讀取 [readId] 有 [numberOfBases] 個基底，但限制為 [maxReadLength]           | 此錯誤最常見的原因是檔案毀損而造成兩次讀取串連。 請檢查輸入檔。 |
| BAM          | 200        |   無法讀取檔案 '[yourFileName]'。                                                                                       | 請檢查 BAM 檔案的格式。 使用正確格式的檔案，再次提交工作流程。                                                                           |
| BAM          | 201        |  無法讀取 BAM 檔案 [File_name]。                                                                                      |請檢查 BAM 檔案的格式。  使用正確格式的檔案提交工作流程。                                                                            |
| BAM          | 202        | 無法讀取 BAM 檔案 [File_name]。 檔案太小，且遺漏標頭。                                                                                        | 請檢查 BAM 檔案的格式。  使用正確格式的檔案提交工作流程。                                                                            |
| BAM          | 203        |   無法讀取 BAM 檔案 [File_name]。 檔案的標頭已損毀。                                                                                      |請檢查 BAM 檔案的格式。  使用正確格式的檔案提交工作流程。                                                                           |
| BAM          | 204        |    無法讀取 BAM 檔案 [File_name]。 檔案的標頭已損毀。                                                                                     | 請檢查 BAM 檔案的格式。  使用正確格式的檔案提交工作流程。                                                                           |
| BAM          | 205        |    無法讀取 BAM 檔案 [File_name]。 檔案的標頭已損毀。                                                                                     | 請檢查 BAM 檔案的格式。  使用正確格式的檔案提交工作流程。                                                                            |
| BAM          | 206        |   無法讀取 BAM 檔案 [File_name]。 檔案的標頭已損毀。                                                                                      | 請檢查 BAM 檔案的格式。  使用正確格式的檔案提交工作流程。                                                                            |
| BAM          | 207        |  無法讀取 BAM 檔案 [File_name]。 檔案在靠近位移 [offset] 的地方被截斷。                                                                                       | 請檢查 BAM 檔案的格式。  使用正確格式的檔案提交工作流程。                                                                            |
| BAM          | 208        |   無效的 BAM 檔案。 讀取識別碼 [Read_Id] 在檔案 [File_name] 中沒有序列。                                                                                      | 請檢查 BAM 檔案的格式。  使用正確格式的檔案提交工作流程。                                                                             |
| FASTQ        | 300        |  無法讀取 FASTQ 檔案。 [File_name] 結尾不是新行字元。                                                                                     | 請修正 FASTQ 檔案的格式，然後再次提交工作流程。                                                                           |
| FASTQ        | 301        |   無法讀取 FASTQ 檔案 [File_name]。 FASTQ 記錄大於緩衝區大小，位移: [_offset]                                                                                      | 請修正 FASTQ 檔案的格式，然後再次提交工作流程。                                                                         |
| FASTQ        | 302        |     FASTQ 語法錯誤。 檔案 [File_name] 具有空白行。                                                                                    | 請修正 FASTQ 檔案的格式，然後再次提交工作流程。                                                                         |
| FASTQ        | 303        |       FASTQ 語法錯誤。 檔案[File_name] 有無效的起始字元，位移: [_offset]，行類型: [line_type]，字元: [_char]                                                                                  | 請修正 FASTQ 檔案的格式，然後再次提交工作流程。                                                                         |
| FASTQ        | 304      |  讀取識別碼 [_ReadID] 中有 FASTQ 語法錯誤。  檔案 [File_name] 中第一讀取批次沒有以 /1 結尾的讀取識別碼                                                                                       | 請修正 FASTQ 檔案的格式，然後再次提交工作流程。                                                                         |
| FASTQ        | 305        |  讀取識別碼 [_ReadID] 中有 FASTQ 語法錯誤。 檔案 [File_name] 中第二讀取批次沒有以 /2 結尾的讀取識別碼                                                                                      | 請修正 FASTQ 檔案的格式，然後再次提交工作流程。                                                                          |
| FASTQ        | 306        |  讀取識別碼 [_ReadID] 中有 FASTQ 語法錯誤。 檔案 [File_name] 中第一讀取組沒有以 /1 結尾的讀取識別碼                                                                                       | 請修正 FASTQ 檔案的格式，然後再次提交工作流程。                                                                          |
| FASTQ        | 307        |   讀取識別碼 [_ReadID] 中有 FASTQ 語法錯誤。 讀取識別碼結尾不是 /1 或 /2。 檔案 [File_name] 不能作為配對的 FASTQ 檔案。                                                                                      |請修正 FASTQ 檔案的格式，然後再次提交工作流程。                                                                          |
| FASTQ        | 308        |  FASTQ 讀取錯誤。 兩個讀取都以不同方式回應。 您是否選擇了正確的 FASTQ 檔案？                                                                                       | 請修正 FASTQ 檔案的格式，然後再次提交工作流程。                                                                         |
|        |       |                                                                                        |                                                                           |

## <a name="step-3-contact-microsoft-genomics-support"></a>步驟 3：連絡 Microsoft Genomics 支援服務

如果持續發生作業失敗，或是有任何其他問題，請從 Azure 入口網站連絡 Microsoft Genomics 支援服務。 您可以在[此處](file-support-ticket-genomics.md)找到如何提交支援要求的其他資訊。

## <a name="next-steps"></a>後續步驟

在本文中，您已學會如何對 Microsoft Genomics 服務的常見問題進行疑難排解並嘗試解決。 如需詳細資訊和更多一般常見問題集，請參閱[常見問題](frequently-asked-questions-genomics.md)。 
