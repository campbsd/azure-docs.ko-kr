---
title: "Azure Data Factory의 데이터 집합 및 연결된 서비스 | Microsoft Docs"
description: "Data Factory의 데이터 집합 및 연결된 서비스에 대해 알아봅니다. 연결된 서비스는 계산/데이터 저장소를 데이터 팩터리에 연결합니다. 데이터 집합은 입/출력 데이터를 나타냅니다."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: 
ms.date: 09/05/2017
ms.author: shlo
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: f03c91b7b27a4fb39b996599efd11242a785b2b2
ms.contentlocale: ko-kr
ms.lasthandoff: 09/25/2017

---

# <a name="datasets-and-linked-services-in-azure-data-factory"></a>Azure Data Factory의 데이터 집합 및 연결된 서비스 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [버전 1 - GA](v1/data-factory-create-datasets.md)
> * [버전 2 - 미리 보기](concepts-datasets-linked-services.md)

이 문서에서는 데이터 집합의 정의, 데이터 집합을 JSON 형식으로 정의하는 방법, Azure Data Factory V2 파이프라인에서 데이터 집합을 사용하는 방법에 대해 설명합니다. 

> [!NOTE]
> 이 문서는 현재 미리 보기 상태인 Data Factory 버전 2에 적용됩니다. GA(일반 공급) 상태인 Data Factory 버전 1 서비스를 사용 중인 경우 [Data Factory V1의 데이터 집합](v1/data-factory-create-datasets.md)을 참조하세요.

Data Factory를 처음 사용하는 경우 [Azure Data Factory 소개](introduction.md)를 참조하세요. 

## <a name="overview"></a>개요
데이터 팩터리에는 하나 이상의 파이프라인이 포함될 수 있습니다. **파이프라인**은 함께 하나의 작업을 수행하는 **활동**의 논리적 그룹화입니다. 파이프라인의 활동은 데이터에 수행할 작업을 정의합니다. 예를 들어 복사 활동을 사용하여 온-프레미스 SQL Server에서 Azure Blob 저장소로 데이터를 복사할 수 있습니다. 그런 다음 Azure HDInsight 클러스터에서 Hive 스크립트를 실행하는 Hive 활동을 사용하여 출력 데이터를 생성하도록 Blob 저장소의 데이터를 처리합니다. 마지막으로 두 번째 복사 활동을 사용하여 BI(비즈니스 인텔리전스) 보고 솔루션의 기반이 되는 Azure SQL Data Warehouse로 출력 데이터를 복사합니다. 파이프라인 및 활동에 대한 자세한 내용은 Azure Data Factory의 [파이프라인 및 활동](concepts-pipelines-activities.md) 문서를 참조하세요.

이제 **데이터 집합**은 입력 및 출력으로 **활동**에서 사용하려는 데이터를 단순히 가리키거나 참조하는 데이터의 명명된 뷰입니다. 데이터 집합은 테이블, 파일, 폴더, 문서를 비롯한 다양한 데이터 저장소 내의 데이터를 식별합니다. 예를 들어 Azure Blob 데이터 집합은 활동에서 데이터를 읽어들여야 하는 Blob 저장소의 Blob 컨테이너와 폴더를 지정합니다.

데이터 집합을 만들기 전에 **연결된 서비스**를 만들어 데이터 저장소를 데이터 팩터리에 연결해야 합니다. 연결된 서비스는 Data Factory에서 외부 리소스에 연결하는 데 필요한 연결 정보를 정의하는 연결 문자열과 같습니다. 데이터 집합은 연결된 데이터 저장소 내의 데이터 구조를 나타내고, 연결된 서비스는 데이터 원본에 연결을 정의한다고 생각하시면 됩니다. 예를 들어 Azure Storage 연결된 서비스는 저장소 계정을 데이터 팩터리에 연결합니다. Azure Blob 데이터 집합은 Blob 컨테이너 및 처리할 입력 Blob이 포함된 Azure 저장소 계정 내의 폴더를 나타냅니다.

샘플 시나리오는 다음과 같습니다. 데이터베이스를 Blob 저장소에서 Azure SQL Database로 복사하려면 두 개의 연결된 서비스, 즉 Azure Storage 및 Azure SQL Database를 만듭니다. 그런 다음 두 개의 데이터 집합, 즉 Azure Blob 데이터 집합(Azure Storage 연결된 서비스 참조), Azure SQL 테이블 데이터 집합(Azure SQL Database 연결된 서비스 참조)을 만듭니다. Azure Storage 및 Azure SQL Database 연결된 서비스에는 Data Factory가 런타임에 Azure Storage 및 Azure SQL Database 각각에 연결하는 데 사용하는 연결 문자열이 포함되어 있습니다. Azure Blob 데이터 집합은 Blob 저장소에 입력 Blob을 포함하는 Blob 컨테이너와 Blob 폴더를 지정합니다. Azure SQL 테이블 데이터 집합은 데이터를 복사할 Azure SQL Database의 SQL 테이블을 지정합니다.

다음 다이어그램에서는 Data Factory의 파이프라인, 활동, 데이터 집합 및 연결된 서비스 간의 관계를 보여 줍니다.

![파이프라인, 활동, 데이터 집합, 연결된 서비스 간 관계](media/concepts-datasets-linked-services/relationship-between-data-factory-entities.png)

## <a name="dataset-json"></a>데이터 집합 JSON
Data Factory의 데이터 집합은 다음과 같이 JSON 형식으로 정의됩니다.

```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: AzureBlob, AzureSql etc...>",
        "linkedServiceName": {
                "referenceName": "<name of linked service>",
                 "type": "LinkedServiceReference",
        },
        "structure": [
            {
                "name": "<Name of the column>",
                "type": "<Name of the type>"
            }
        ],
        "typeProperties": {
            "<type specific property>": "<value>",
            "<type specific property 2>": "<value 2>",
        }
    }
}

```
다음 표에서는 위의 JSON에서 속성을 설명합니다.

속성 | 설명 | 필수 | 기본값
-------- | ----------- | -------- | -------
name | 데이터 집합의 이름입니다. | [Azure Data Factory - 이름 지정 규칙](naming-rules.md)을 참조하세요. | 예 | 해당 없음
type | 데이터 집합의 형식입니다. | Data Factory에서 지원하는 형식(예: AzureBlob, AzureSqlTable) 중 하나를 지정합니다. <br/><br/>자세한 내용은 [데이터 집합 형식](#dataset-types)을 참조하세요. | 예 | 해당 없음
structure | 데이터 집합의 스키마입니다. | 자세한 내용은 [데이터 집합 구조](#dataset-structure)를 참조하세요. | 아니요 | 해당 없음
typeProperties | 형식 속성은 형식마다 다릅니다(예: Azure Blob, Azure SQL 테이블). 지원되는 형식 및 해당 속성에 대한 자세한 내용은 [데이터 집합 형식](#dataset-type)을 참조하세요. | 예 | 해당 없음

## <a name="dataset-example"></a>데이터 집합 예제
다음 예제에서 데이터 집합은 SQL 데이터베이스에서 MyTable이라는 테이블을 나타냅니다.

```json
{
    "name": "DatasetSample",
    "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": {
                "referenceName": "MyAzureSqlLinkedService",
                 "type": "LinkedServiceReference",
        },
        "typeProperties":
        {
            "tableName": "MyTable"
        },
    }
}

```
다음 사항에 유의하세요.

- type을 AzureSQLTable로 설정합니다.
- AzureSqlTable 형식과 관련된 tableName 형식 속성은 MyTable로 설정됩니다.
- linkedServiceName은 다음 JSON 코드 조각으로 정의된 AzureSqlDatabase 형식의 연결된 서비스를 가리킵니다.

## <a name="linked-service-example"></a>연결된 서비스 예제
AzureSqlLinkedService는 다음과 같이 정의됩니다.

```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "description": "",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>@<servername>;Password=<password>;Integrated Security=False;Encrypt=True;Connect Timeout=30"
        }
    }
}
```
앞의 JSON 코드 조각에서

- **type**은 AzureSqlDatabase로 설정됩니다.
- **connectionString** 형식 속성은 SQL 데이터베이스에 연결하는 정보를 지정합니다.

여기서 볼 수 있듯이 연결된 서비스는 Azure SQL 데이터베이스에 연결하는 방법을 정의합니다. 데이터 집합은 파이프라인의 활동에 대한 입력 및 출력으로 사용되는 테이블을 정의합니다.

## <a name="dataset-type"></a>데이터 집합 형식
사용하는 데이터 저장소에 따라 다양한 유형의 데이터 집합이 있습니다. Data Factory에서 지원하는 데이터 저장소 목록은 다음 표를 참조하세요. 해당 데이터 저장소에 대해 연결된 서비스 및 데이터 집합을 만드는 방법을 알아보려면 데이터 저장소를 클릭합니다.

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

이전 섹션의 예제에서 데이터 집합의 형식은 **AzureSqlTable**로 설정되어 있습니다. 마찬가지로 Azure Blob 데이터 집합의 경우 데이터 집합의 형식이 다음 JSON과 같이 **AzureBlob**으로 설정됩니다.

```json
{
    "name": "AzureBlobInput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": {
                "referenceName": "MyAzureStorageLinkedService",
                 "type": "LinkedServiceReference",
        }, 
 
        "typeProperties": {
            "fileName": "input.log",
            "folderPath": "adfgetstarted/inputdata",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        }
    }
}
```
## <a name="dataset-structure"></a>데이터 집합 구조
**structure** 섹션은 선택 사항입니다. 열의 이름 및 데이터 형식 컬렉션을 포함하여 데이터 집합의 스키마를 정의합니다. 형식을 변환하고 열을 원본에서 대상으로 매핑하는 데 사용되는 형식 정보를 structure 섹션에 제공합니다. 다음 예제에서 데이터 집합에는 세 개의 열, 즉 timestamp, projectname 및 pageviews가 있습니다. 형식은 각각 String, String 및 Decimal입니다.

```json
[
    { "name": "timestamp", "type": "String"},
    { "name": "projectname", "type": "String"},
    { "name": "pageviews", "type": "Decimal"}
]
```

structure의 각 열에는 다음과 같은 속성이 포함됩니다.

속성 | 설명 | 필수
-------- | ----------- | --------
name | 열의 이름입니다. | 예
type | 열의 데이터 형식입니다. | 아니요
culture | type이 `Datetime` 또는 `Datetimeoffset` .NET 형식일 때 사용할 .NET 기반 culture(문화권)입니다. 기본값은 `en-us`입니다. | 아니요
format | type이 `Datetime` 또는 `Datetimeoffset` .NET 형식일 때 사용할 format(서식) 문자열입니다. | 아니요

다음 지침은 structure 정보를 포함할 시기 및 **structure** 섹션에 포함할 내용을 결정하는 데 도움이 됩니다.

- **구조화된 데이터 원본의 경우** 원본 열을 싱크 열에 매핑하고 해당 이름이 동일하지 않은 경우에만 structure 섹션을 지정합니다. 이러한 종류의 구조화된 데이터 원본은 데이터 스키마 및 형식 정보를 데이터 자체와 함께 저장합니다. 구조화된 데이터 원본의 예로 SQL Server, Oracle 및 Azure SQL Database가 있습니다.<br/><br/>구조화된 데이터 원본에 대해 형식 정보를 이미 사용할 수 있으므로 "structure" 섹션을 포함할 때 형식 정보를 포함하면 안됩니다.
- **읽기 데이터 원본(특히 Blob 저장소)에 대한 스키마의 경우** 스키마 또는 형식 정보를 데이터와 함께 저장하지 않고도 데이터를 저장하도록 선택할 수 있습니다. 이러한 유형의 데이터 원본인 경우 원본 열을 싱크 열로 매핑할 때 구조가 포함됩니다. 또한 데이터 집합이 복사 활동에 대한 입력인 경우 구조가 포함되고 원본 데이터 집합의 데이터 형식을 싱크의 네이티브 형식으로 변환해야 합니다.<br/><br/> Data Factory는 구조에서 형식 정보를 제공하기 위한 다음 값을 지원합니다. `Int16, Int32, Int64, Single, Double, Decimal, Byte[], Boolean, String, Guid, Datetime, Datetimeoffset, and Timespan` 

[스키마 및 형식 매핑]( copy-activity-schema-and-type-mapping.md)에서 데이터 팩터리에서 원본 데이터를 싱크로 매핑하는 방법 및 구조 정보를 지정하는 시기에 대해 알아봅니다.

## <a name="create-datasets"></a>데이터 집합 만들기
이러한 도구 또는 SDK 중 하나를 사용하여 데이터 집합을 만들 수 있습니다. [.NET API](quickstart-create-data-factory-dot-net.md), [PowerShell](quickstart-create-data-factory-powershell.md), [REST API](quickstart-create-data-factory-rest-api.md), Azure Resource Manager 템플릿 및 Azure Portal

## <a name="v1-vs-v2-datasets"></a>V1과 V2 데이터 집합 비교 

Data Factory v1 및 v2 데이터 집합 사이에 몇 가지 차이점은 다음과 같습니다. 

- 외부 속성은 v2에서 지원되지 않습니다. [트리거](concepts-pipeline-execution-triggers.md)로 대체됩니다.
- 정책 및 가용성 속성은 V2에서 지원되지 않습니다. 파이프라인에 대한 시작 시간은 [트리거](concepts-pipeline-execution-triggers.md)에 따라 달라집니다.
- 범위가 지정된 데이터 집합(파이프라인에서 정의된 데이터 집합)은 V2에서 지원되지 않습니다. 

## <a name="next-steps"></a>다음 단계
다음 도구 또는 SDK 중 하나를 사용하여 파이프라인 및 데이터 집합을 만들기 위한 단계별 지침은 다음 자습서를 참조하세요. 

- [빠른 시작: .NET을 사용하여 데이터 팩터리 만들기](quickstart-create-data-factory-dot-net.md)
- [빠른 시작: PowerShell을 사용하여 데이터 팩터리 만들기](quickstart-create-data-factory-powershell.md)
- [빠른 시작: REST API를 사용하여 데이터 팩터리 만들기](quickstart-create-data-factory-rest-api.md)
- 빠른 시작: Azure Portal을 사용하여 데이터 팩터리 만들기

