---
title: Azure Cost Management를 위한 저장소 계정 구성 | Microsoft Docs
description: 이 문서에서는 Azure Cost Management를 위한 Azure Storage 계정 및 AWS 저장소 버킷 구성 방법을 설명합니다.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/26/2018
ms.topic: conceptual
ms.service: cost-management
manager: carmonm
ms.custom: ''
ms.openlocfilehash: 76a9fc586a1932b8b5e664b6c964f0c7d3eac4d4
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="configure-storage-accounts-for-cost-management"></a>Cost Management를 위해 저장소 계정 구성

<!--- intent: As a Cost Management user, I want to configure Cost Management to use my cloud service provider storage account to store my reports. -->

Cost Management 보고서를 Cloudyn 포털, Azure Storage 또는 AWS 저장소 버킷에 저장할 수 있습니다. Cloudyn 포털에 보고서를 저장하는 것은 무료입니다. 그러나 클라우드 서비스 공급자의 저장소에 보고서를 저장하는 것은 선택 사항이며 추가 비용이 발생합니다. 이 문서는 보고서를 저장하도록 Azure Storage 계정 및 AWS(Amazon Web Services) 저장소 버킷을 구성하는 데 도움을 줍니다.

## <a name="prerequisites"></a>필수 조건

Azure Storage 계정 또는 Amazon 저장소 버킷이 있어야 합니다.

Azure Storage 계정이 없는 경우 만들어야 합니다. Azure Storage 계정 생성에 대한 자세한 내용은 [저장소 계정 만들기](../storage/common/storage-create-storage-account.md#create-a-storage-account)를 참조하세요.

AWS 단순 저장소 서비스(S3) 버킷이 없는 경우 만들어야 합니다. S3 버킷 생성에 대한 자세한 내용은 [버킷 만들기](https://docs.aws.amazon.com/AmazonS3/latest/gsg/CreatingABucket.html)를 참조하세요.

## <a name="configure-your-azure-storage-account"></a>Azure Storage 계정 구성

Cost Management에 사용할 Azure Storage를 구성하는 과정은 간단합니다. 저장소 계정에 대한 세부 정보를 수집하고 Cloudyn 포털에 복사합니다.

1. Azure Portal ( http://portal.azure.com ) 에 로그인합니다.
2. **모든 서비스**를 선택하고 **저장소 계정**을 선택한 후 사용하려는 저장소 계정으로 스크롤한 다음, 해당 계정을 선택합니다.
3. 저장소 계정 페이지의 **설정** 아래에서 **액세스 키**를 클릭합니다.
4. **저장소 계정 이름** 및 **연결 문자열**을 key1 아래에 복사합니다.  
![Azure Storage 액세스 키](./media/storage-accounts/azure-storage-access-keys.png)  
5. Azure Portal에서 Cloudyn 포털을 열거나 https://azure.cloudyn.com으로 이동하여 로그인합니다.
6. 톱니 기호를 클릭한 다음 **보고서 저장소 관리**를 선택합니다.
7. **새로 추가 +** 를 클릭하고 Microsoft Azure가 선택되어 있는지 확인합니다. Azure Storage 계정 이름을 **이름** 영역에 붙여 넣습니다. **연결 문자열**을 해당 영역에 붙여 넣습니다. 컨테이너 이름을 입력한 다음 **저장**을 클릭합니다.  
![Azure용으로 구성된 Cloudyn 저장소](./media/storage-accounts/azure-cloudyn-storage.png)

  새 Azure 보고서 저장소 항목이 저장소 계정 목록에 나타납니다.  
    ![목록의 새 Azure 보고서 저장소](./media/storage-accounts/azure-storage-entry.png)


이제 Azure Storage에 보고서를 저장할 수 있습니다. 보고서에서 **작업**을 클릭한 다음, **보고서 예약**을 선택합니다. 보고서 이름을 지정한 다음, 고유한 URL을 추가하거나 자동으로 생성된 URL을 사용합니다. **저장소에 저장**을 선택한 다음, 저장소 계정을 선택합니다. 보고서 파일 이름에 추가할 접두사를 입력합니다. CSV 또는 JSON 파일 형식을 선택한 다음, 보고서를 저장합니다.

## <a name="configure-an-aws-storage-bucket"></a>AWS 저장소 버킷 구성

Cloudyn은 기존 AWS 자격 증명인 사용자 또는 역할을 사용하여 버킷에 보고서를 저장합니다. 액세스를 테스트하기 위해 Cloudyn은 파일 이름 _check-bucket-permission.txt_를 사용하여 작은 텍스트 파일을 버킷에 저장하려고 합니다.

Cloudyn 역할 또는 사용자에게 버킷에 대한 PutObject 권한을 제공합니다. 그런 다음, 기존 버킷을 사용하거나 보고서를 저장할 새 버킷을 만듭니다. 마지막으로, 저장소 클래스를 관리하거나, 수명 주기 규칙을 설정하거나, 불필요한 파일을 제거하는 방법을 결정합니다.

###  <a name="assign-permissions-to-your-aws-user-or-role"></a>AWS 사용자 또는 역할에 사용 권한 할당

새 정책을 만들 때 보고서를 S3 버킷에 저장하는 데 필요한 정확한 권한을 제공합니다.

1. AWS 콘솔에 로그인하고 **서비스**를 선택합니다.
2. 서비스 목록에서 **IAM**을 선택합니다.
3. 콘솔 왼쪽의 **정책**을 선택하고 **정책 만들기**를 클릭합니다.
4. **JSON** 탭을 클릭합니다.
5. 다음 정책을 사용하여 S3 버킷에 보고서를 저장할 수 있습니다. 다음 정책 예제를 복사한 후 **JSON** 탭에 붙여 넣습니다. &lt;bucketname&gt;을 버킷 이름으로 바꿉니다.

  ```
{
    "Version": "2012-10-17",
    "Statement": [
      {
        "Sid":  "CloudynSaveReport2S3",
        "Effect":      "Allow",
        "Action": [
          "s3:PutObject"
        ],
        "Resource": [
          "arn:aws:s3:::<bucketname>/*"
        ]
      }
    ]
}
```

6. **정책 검토**를 클릭합니다.  
    ![정책 검토](./media/storage-accounts/aws-policy.png)  
7. 정책 검토 페이지에서 정책의 이름을 입력합니다. 예를 들어 _CloudynSaveReport2S3_를 입력합니다.
8. **정책 만들기**를 클릭합니다.

### <a name="attach-the-policy-to-a-cloudyn-role-or-user-in-your-account"></a>정책을 계정의 Cloudyn 역할 또는 사용자에 연결

새 정책을 연결하려면 AWS 콘솔을 열고 Cloudyn 역할 또는 사용자를 편집합니다.

1. AWS 콘솔에 로그인하고 **서비스**를 선택한 후 서비스 목록에서 **IAM**을 선택합니다.
2. 콘솔 왼쪽에서 **역할** 또는 **사용자**를 선택합니다.

**역할:**

  1. Cloudyn 역할 이름을 클릭합니다.
  2. **사용 권한** 탭에서 **정책 연결**을 클릭합니다.
  3. 사용자가 만든 정책을 검색하고 선택한 다음, **정책 연결**을 클릭합니다.
    ![AWS - 역할에 대한 정책 연결](./media/storage-accounts/aws-attach-policy-role.png)

**사용자:**

1. Cloudyn 사용자를 선택합니다.
2. **사용 권한** 탭에서 **권한 추가**를 클릭합니다.
3. **권한 부여** 섹션에서 **기존 정책 직접 연결**을 선택합니다.
4. 사용자가 만든 정책을 검색하고 선택한 다음, **다음: 검토**를 클릭합니다.
5. 역할 이름에 권한 추가 페이지에서 **권한 추가**를 클릭합니다.  
    ![AWS - 사용자에 대한 정책 연결](./media/storage-accounts/aws-attach-policy-user.png)


### <a name="optional-set-permission-with-bucket-policy"></a>선택 사항: 버킷 정책을 사용하여 사용 권한 설정

버킷 정책을 사용하여 S3 버킷에 보고서를 만들기 위한 권한을 설정할 수도 있습니다. 클래식 S3 보기:

1. 기존 버킷을 만들거나 선택합니다.
2. **사용 권한** 탭을 선택하고 **버킷 정책**을 클릭합니다.
3. 다음 정책 샘플을 복사한 후 붙여 넣습니다. &lt;버킷\_이름&gt; 및 &lt;Cloudyn\_원칙&gt;을 버킷의 ARN으로 바꿉니다. Cloudyn에서 사용하는 역할 또는 사용자의 ARN을 대체합니다.

  ```
{
  "Id": "Policy1485775646248",
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "SaveReport2S3",
      "Action": [
        "s3:PutObject"
      ],
      "Effect": "Allow",
      "Resource": "<bucket_name>/*",
      "Principal": {
        "AWS": [
          "<Cloudyn_principle>"
        ]
      }
    }
  ]
}
```

4. 버킷 정책 편집기에서 **저장**을 클릭합니다.

### <a name="add-aws-report-storage-to-cloudyn"></a>Cloudyn에 AWS 보고서 저장소 추가

1. Azure Portal에서 Cloudyn 포털을 열거나 https://azure.cloudyn.com으로 이동하여 로그인합니다.
2. 톱니 기호를 클릭한 다음 **보고서 저장소 관리**를 선택합니다.
3. **새로 추가 +** 를 클릭하고 AWS가 선택되어 있는지 확인합니다.
4. 계정 및 저장소 버킷을 선택합니다. AWS 저장소 버킷의 이름이 자동으로 채워집니다.  
    ![AWS 버킷에 대한 보고서 저장소 추가](./media/storage-accounts/aws-cloudyn-storage.png)  
5. 그런 다음, **저장**을 클릭하고 **확인**을 클릭합니다.

    새 AWS 보고서 저장소 항목이 저장소 계정 목록에 나타납니다.  
    ![목록의 새 AWS 보고서 저장소](./media/storage-accounts/aws-storage-entry.png)


이제 Azure Storage에 보고서를 저장할 수 있습니다. 보고서에서 **작업**을 클릭한 다음, **보고서 예약**을 선택합니다. 보고서 이름을 지정한 다음, 고유한 URL을 추가하거나 자동으로 생성된 URL을 사용합니다. **저장소에 저장**을 선택한 다음, 저장소 계정을 선택합니다. 보고서 파일 이름에 추가할 접두사를 입력합니다. CSV 또는 JSON 파일 형식을 선택한 다음, 보고서를 저장합니다.

## <a name="next-steps"></a>다음 단계

- [Cost Management 보고서 이해](understanding-cost-reports.md)를 검토하여 Cost Management 보고서의 기본 구조 및 기능에 대해 알아보세요.
