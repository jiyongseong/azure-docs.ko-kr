---
title: Azure Machine Learning의 새로운 기능
description: 이 문서에서는 Azure Machine Learning에 대한 업데이트를 자세히 설명합니다.
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.service: machine-learning
ms.workload: data-services
ms.topic: reference
ms.date: 03/28/2018
ms.openlocfilehash: 1eb4e7859ae62fca4e4a11f69abda3f767ab9596
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="whats-new-in-azure-machine-learning"></a>Azure Machine Learning의 새로운 기능

이 문서에서는 [Azure Machine Learning](../service/overview-what-is-azure-ml.md)의 새로운 릴리스에 대해 알아봅니다. 


## <a name="2018-05-sprint-5"></a>2018-05(스프린트 5)

이 릴리스의 Azure Machine Learning에서 수행할 수 있는 작업은 다음과 같습니다.
+ ResNet 50의 양자화된 버전으로 이미지를 기능화하고, 이러한 기능에 따라 분류자를 학습하고, 초단기 대기 시간 추론을 위해 [Azure에서 FPGA에 해당 모델을 배포](../service/how-to-deploy-fpga-web-service.md)합니다.

+ 다음 도메인에 대해 [사용자 지정 Azure Machine Learning 패키지](../service/reference-python-package-overview.md)를 사용하여 고정밀 기계 학습 및 딥 러닝 모델을 빠르게 빌드하고 배포합니다.
  + [Computer vision](../service/how-to-build-deploy-image-classification-models.md)
  + [텍스트 분석](../service/how-to-build-deploy-text-classification-models.md)
  + [Forecasting](../service/how-to-build-deploy-forecast-models.md)

## <a name="2018-03-sprint-4"></a>2018-03(스프린트 4)
**버전 번호**: 0.1.1801.24353&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;([버전 확인](../service/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))


다음 업데이트 중 많은 부분이 여러분이 보내 주신 피드백에 따른 직접적인 결과입니다. 앞으로도 이러한 결과를 얻을 수 있도록 여러분의 피드백을 계속 보내주십시오!

**주목할 만한 새로운 기능 및 변경 사항**

- 기본적으로 원격 Docker 기반 실행 외에도 고유한 환경의 원격 Ubuntu VM에서 스크립트를 실행하도록 지원합니다.
- 워크벤치 앱에서 새로운 환경을 사용하면 계산 대상을 만들고 CLI 기반 환경 외에도 구성을 실행할 수 있습니다.
![환경 탭](media/azure-machine-learning-release-notes/environment-page.png)
- 사용자 지정 가능한 실행 기록 보고서 ![새로운 실행 기록 보고서의 이미지](media/azure-machine-learning-release-notes/new-run-history-reports.png)

**자세한 업데이트**

다음은 이 스프린트의 Azure Machine Learning 구성 요소 영역 각각에 대한 자세한 업데이트 목록입니다.

### <a name="workbench-ui"></a>워크벤치 UI
- 사용자 지정 가능한 실행 기록 보고서
  - 실행 기록 보고서에 대한 향상된 차트 구성
    - 사용되는 진입점을 변경할 수 있습니다.
    - 최상위 필터를 추가하고 수정할 수 있습니다. ![필터 추가](media/azure-machine-learning-release-notes/add-filters.jpg)
    - 차트 및 통계를 추가하거나 수정(및 끌어서 놓기를 다시 정렬)할 수 있습니다.
    ![새 차트 만들기](media/azure-machine-learning-release-notes/configure-charts.png)

  - 실행 기록 보고서의 CRUD
  - 선택한 진입점에서 실행 시 파이프라인과 같은 역할을 담당하는 서버 쪽 보고서로 모든 기존 실행 기록 목록 보기 구성을 이동합니다.

- 환경 탭
  - 프로젝트에 새로운 계산 대상 및 실행 구성 파일을 쉽게 추가합니다. ![새 계산 대상](media/azure-machine-learning-release-notes/add-new-environments.png)
  - 간단한 양식 기반 UX를 사용하여 구성 파일을 관리하고 업데이트합니다.
  - 실행할 환경을 준비하기 위한 새 단추입니다.

- 세로 막대에 있는 파일 목록에 대한 성능 향상

### <a name="data-preparation"></a>데이터 준비 
- 이제 Azure Machine Learning Workbench를 사용하면 알려진 열의 이름을 사용하여 열을 검색할 수 있습니다.


### <a name="experimentation"></a>실험
- 이제 Azure Machine Learning Workbench는 기본적으로 고유한 Python 또는 PySpark 환경에서 스크립트를 실행하도록 지원합니다. 이 기능으로 사용자는 원격 VM에서 고유한 환경을 만들고 관리하고, Azure Machine Learning Workbench를 사용하여 해당 대상에서 해당 스크립트를 실행합니다. [Azure Machine Learning 실험 서비스 구성](experimentation-service-configuration.md)을 참조하세요. 

### <a name="model-management"></a>모델 관리
- 배포된 컨테이너를 사용자 지정하도록 지원: apt-get 등을 사용하는 외부 라이브러리를 설치하도록 허용하여 컨테이너 이미지를 사용자 지정할 수 있습니다. 더 이상 pip 설치 가능 라이브러리로 제한되지 않습니다. 자세한 내용은 [문서](model-management-custom-container.md)를 참조하세요.
  - 매니페스트, 이미지 또는 서비스 생성 명령이 포함된 `--docker-file myDockerStepsFilename` 플래그 및 파일 이름을 사용합니다.
  - 기본 이미지가 Ubuntu이며 수정할 수 없습니다.
  - 예제 명령: 
  
      ```shell
      $ az ml image create -n myimage -m mymodel.pkl -f score.py --docker-file mydockerstepsfile
      ```



## <a name="2018-01-sprint-3"></a>2018-01(스프린트 3) 
**버전 번호**: 0.1.1712.18263  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;([버전 확인](../service/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

다음은 이번 스프린트의 업데이트 및 개선 사항입니다. 아래 업데이트 중 많은 부분은 사용자가 보내 주신 피드백에 따른 직접적인 결과입니다. 


다음은 이 스프린트의 Azure Machine Learning 구성 요소 영역 각각에 대한 자세한 업데이트 목록입니다.

- 시작 시 인증 스택 강제 로그인 및 계정 선택이 업데이트됨

### <a name="workbench"></a>워크벤치
- 프로그램 추가/제거에서 앱 설치/제거 기능
- 시작 시 인증 스택 강제 로그인 및 계정 선택이 업데이트됨
- Windows의 SSO(Single Sign-On) 환경 개선
- 자격 증명이 다른 여러 테넌트에 속하는 사용자는 Workbench에 로그인 할 수 없음

### <a name="ui"></a>UI
- 일반적인 개선 사항 및 버그 수정

### <a name="notebooks"></a>노트북
- 일반적인 개선 사항 및 버그 수정

### <a name="data-preparation"></a>데이터 준비 
- 예제별 변환을 수행하는 동안 자동 제안 기능 향상
- 패턴 빈도 검사기에 대한 알고리즘 개선
- 예제별 변환을 수행하는 동안 샘플 데이터 및 피드백을 전송하는 기능 ![장치 열 변환의 피드백 보내기 링크 이미지](media/azure-machine-learning-release-notes/SendFeedbackFromDeriveColumn.png)
- Spark 런타임 개선
- Scala가 Pyspark를 대체
- 시계열 검사기에서 Data Not Applicable(데이터가 적용되지 않음)를 닫을 수 없는 문제 해결 
- HDI의 데이터 준비 실행에 대한 중단 시간 수정

### <a name="model-management-cli-updates"></a>모델 관리 CLI 업데이트 
  - 리소스 프로비저닝을 위해 구독 소유권이 더 이상 필요하지 않습니다. 리소스 그룹에 대한 contributor 액세스만으로 배포 환경을 설정할 수 있습니다.
  - 무료 구독을 위한 로컬 환경 설정을 사용하도록 설정됨 

## <a name="2017-12-sprint-2-qfe"></a>2017-12(스프린트 2 QFE) 
**버전 번호**: 0.1.1711.15323 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;([버전 확인](../service/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

QFE(Quick Fix Engineering) 릴리스로, 사소한 릴리스입니다. 여러 원격 분석 문제를 해결하고 제품 팀이 제품 사용 방법을 보다 잘 이해할 수 있도록 도와줍니다. 제품 환경을 개선하기 위한 향후 노력에 대한 내용이 포함될 수 있습니다. 

또한 두 개의 중요 업데이트가 있습니다.

- 시계열 검사기가 데이터 준비 패키지에 표시되지 않는 데이터 준비 버그를 수정했습니다.
- 명령줄 도구에서 더 이상 Azure 구독 소유자가 아니어도 Machine Learning Compute ACS 클러스터를 프로비전할 수 있습니다. 

## <a name="2017-12-sprint-2"></a>2017-12(스프린트 2)
**버전 번호**: 0.1.1711.15263 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;([버전 확인](../service/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

세 번째 Azure Machine Learning 업데이트를 시작합니다. 이 업데이트는 Workbench 앱, CLI(명령줄 인터페이스) 및 백 엔드 서비스의 향상된 기능을 포함합니다. 웃는 얼굴과 찡그린 얼굴을 보내 주시어 대단히 감사합니다. 다음 업데이트 중 많은 부분이 여러분이 보내 주신 피드백에 따른 직접적인 결과입니다. 

**주목할 만한 새로운 기능**
- [데이터 원본으로서 SQL Server 및 Azure SQL DB 지원](data-prep-appendix2-supported-data-sources.md#types) 
- [MMLSpark를 사용하여 GPU 지원이 포함된 Spark에 대한 딥 러닝](https://github.com/Azure/mmlspark/blob/master/docs/gpu-setup.md)
- [배포 시 모든 AML 컨테이너는 Azure IoT Edge 장치와 호환됨(추가 단계 필요 없음)](http://aka.ms/aml-iot-edge-blog)
- Azure Portal에서 사용 가능한 등록된 모델 목록 및 세부 정보 보기
- 사용자 이름/암호 기반 액세스 이외에 SSH 키 기반 인증을 사용하여 계산 대상 액세스 
- 데이터 준비 환경의 새 패턴 빈도 검사기 

**자세한 업데이트** 다음은 이 스프린트의 Azure Machine Learning 구성 요소 영역 각각에 대한 자세한 업데이트 목록입니다.

### <a name="installer"></a>설치 관리자
- 설치 관리자는 사용자가 다시 설치 필요 없이 버그 수정 및 새 기능을 지원할 수 있도록 자체 업데이트할 수 있습니다.

### <a name="workbench-authentication"></a>Workbench 인증
- 인증 시스템에 대한 여러 수정. 여전히 로그인 문제가 발생하는 경우 저희에게 알려주세요.
- 프록시 관리자 설정을 쉽게 찾을 수 있도록 UI가 변경되었습니다.

### <a name="workbench"></a>워크벤치
- 이제 읽기 전용 파일 보기에는 연한 파랑색 배경이 표시됩니다.
- 더 쉽게 검색할 수 있도록 편집 버튼이 오른쪽으로 이동되었습니다.
- 이제 "dsource", "dprep" 및 "ipynb" 파일 형식을 원시 텍스트 형식으로 렌더링할 수 있습니다.
- 이제 Workbench에는 사용자가 외부 IDE를 사용하여 스크립트를 편집하고 Workbench만 사용해도 풍부한 편집 환경(예: 노트북, 데이터 원본, 데이터 준비 패키지)에서 파일 형식을 편집할 수 있도록 안내하는 새로운 편집 환경이 있습니다.
- 사용자가 액세스할 수 있는 작업 영역 및 프로젝트의 목록이 이제 훨씬 빠르게 로드됩니다.

### <a name="data-preparation"></a>데이터 준비 
- 열의 문자열 패턴을 볼 수 있는 패턴 빈도 검사기. 이러한 패턴을 사용하여 데이터를 필터링할 수도 있습니다. 값 개수 검사기와 비슷한 보기가 표시됩니다. 패턴 빈도에는 고유 데이터의 개수가 아니라 데이터의 고유 패턴 개수가 표시된다는 점이 다릅니다. 또한 특정 패턴에 맞는 모든 행을 필터링할 수도 있습니다.

![제품 번호에 대한 패턴 빈도 검사기 이미지](media/azure-machine-learning-release-notes/pattern-inspector-product-number.png)

- '예제별 열 파생' 변환에서 극단적인 사례를 검토하도록 권장하면서 성능 개선

- [데이터 원본으로서 SQL Server 및 Azure SQL DB 지원](data-prep-appendix2-supported-data-sources.md#types) 

![새 SQL Server 데이터 원본 만들기 이미지](media/azure-machine-learning-release-notes/sql-server-data-source.png)

- 행 및 열 개수의 "개요" 보기를 사용하도록 설정

![행 및 열 개수의 개요 보기 이미지](media/azure-machine-learning-release-notes/row-col-count.png)

- 모든 계산 컨텍스트에서 데이터 준비 사용
- SQL Server 데이터베이스를 사용하는 데이터 원본을 모든 계산 컨텍스트에서 사용 가능
- 데이터 준비 그리드 열을 데이터 형식별로 필터링 가능
- 여러 열을 날짜로 변환할 때의 문제 해결
- 사용자가 고급 모드에서 출력 열 이름을 변경한 경우 예제별 열 파생에서 출력 열을 원본으로 선택할 수 있는 문제 해결

### <a name="job-execution"></a>작업 실행
이제 다음 단계에 따라 SSH 키 기반 인증을 사용하여 remotedocker 또는 클러스터 유형 계산 대상을 만들고 액세스할 수 있습니다.
- CLI에서 다음 명령을 사용하여 계산 대상을 연결합니다.

    ```azure-cli
    $ az ml computetarget attach remotedocker --name "remotevm" --address "remotevm_IP_address" --username "sshuser" --use-azureml-ssh-key
    ```
>[!NOTE]
>이 명령의 -k(또는 --use-azureml-ssh-key) 옵션은 SSH 키를 사용하여 사용하도록 지정합니다.

- Azure ML Workbench가 공개 키를 생성하여 콘솔에 출력합니다. 동일한 사용자 이름을 사용하여 계산 대상에 로그인하고 이 공개 키가 포함된 ~/.ssh/authorized_keys 파일을 추가합니다.

- 이 계산 대상을 준비하고 실행에 사용할 수 있으며 Azure ML Workbench는 인증을 위해 이 키를 사용합니다.  

계산 대상 만들기에 대한 자세한 내용은 [Azure Machine Learning 실험 서비스 구성](experimentation-service-configuration.md)을 참조하세요.

### <a name="visual-studio-tools-for-ai"></a>Visual Studio Tools for AI
- [Visual Studio Tools for AI](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vstoolsai-vs2017)에 대한 추가 지원입니다. 

### <a name="command-line-interface-cli"></a>CLI(Command Line Interface)
- 추가된 `az ml datasource create` 명령을 사용하면 명령줄에서 데이터 원본 파일을 만들 수 있습니다.

### <a name="model-management-and-operationalization"></a>모델 관리 및 운영화
- [운영화 시 모든 AML 컨테이너는 Azure IoT Edge 장치와 호환됨(추가 단계 필요 없음)](http://aka.ms/aml-iot-edge-blog) 
- o16n CLI의 오류 메시지 개선 사항
- 모델 관리 포털 UX에서 버그 수정  
- 세부 정보 페이지에서 모델 관리 특성에 대한 일관된 대/소문자 구분
- 실시간 점수 매기기 호출 시간 제한을 60초로 설정
- Azure Portal에서 사용 가능한 등록된 모델 목록 및 세부 정보 보기

![포털의 모델 세부 정보](media/azure-machine-learning-release-notes/model-list.jpg)

![포털의 모델 개요](media/azure-machine-learning-release-notes/model-overview-portal.jpg)

### <a name="mmlspark"></a>MMLSpark
- [GPU 지원](https://github.com/Azure/mmlspark/blob/master/docs/gpu-setup.md)이 포함된 Spark에 대한 딥 러닝
- 손쉬운 리소스 배포를 위한 리소스 관리자 템플릿 지원
- SparklyR 에코시스템에 대한 지원
- [AZTK 통합](https://github.com/Azure/aztk/wiki/Spark-on-Azure-for-Python-Users#optional-set-up-mmlspark)

### <a name="sample-projects"></a>샘플 프로젝트
- 새 Azure ML SDK 버전으로 [Iris](https://github.com/Azure/MachineLearningSamples-Iris) 및 [MMLSpark](https://github.com/Azure/mmlspark) 샘플 업데이트

### <a name="breaking-changes"></a>주요 변경 내용
- `az ml computetarget attach`에서 하위 명령으로 `--type` 스위치가 승격되었습니다. 

    - `az ml computetarget attach --type remotedocker`는 이제 `az ml computetarget attach remotedocker`입니다.
    - `az ml computetarget attach --type cluster`는 이제 `az ml computetarget attach cluster`입니다.

## <a name="2017-11-sprint-1"></a>2017-11(스프린트 1) 
**버전 번호**: 0.1.1710.31013 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;([버전 확인](../service/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

이 릴리스에서는 워크벤치 앱, CLI 및 백 엔드 서비스 계층에서 보안, 안정성 및 유지 관리 기능이 향상되었습니다. 웃는 얼굴과 찡그린 얼굴을 보내 주시어 대단히 감사합니다. 아래 업데이트 중 많은 부분이 여러분이 보내 주신 피드백에 따른 직접적인 결과입니다. 앞으로도 이러한 결과를 얻을 수 있도록 여러분의 피드백을 계속 보내주십시오!

### <a name="notable-new-features"></a>주목할 만한 새로운 기능
- Azure ML은 이제 새로운 두 Azure 지역, **유럽 서부** 및 **동남 아시아**에서 사용할 수 있습니다. 이에 따라 이전의 **미국 동부 2**, **미국 중서부**, 및 **오스트레일리아 동부** 지역과 함께 총 5개 지역에 배포됩니다.
- Python 소스 코드를 쉽게 읽고 편집할 수 있도록 Workbench 응용 프로그램에서 Python 코드 구문을 강조 표시할 수 있습니다. 
- 이제 전체 프로젝트 대신 파일에서 직접 즐겨찾는 IDE를 시작할 수 있습니다.  Workbench에서 파일을 열고 "편집"을 클릭하면 IDE(현재 VS Code 및 PyCharm이 지원됨)가 현재 파일 및 프로젝트로 시작됩니다.  [편집] 단추 옆에 있는 화살표를 클릭하여 Workbench 텍스트 편집기에서 파일을 편집할 수도 있습니다.  [편집]을 클릭할 때까지 파일을 읽기 전용으로 유지하여 실수로 변경하지 않도록 방지합니다.
- 널리 사용되는 `matplotlib` 그리기 라이브러리 버전 2.1.0이 이제 Workbench 응용 프로그램과 함께 제공됩니다.
- 데이터 준비 엔진을 위해 .NET Core 버전을 2.0으로 업그레이드했습니다. 이 경우macOS에서 응용 프로그램을 설치하는 동안 brew-install openssl에 대한 요구 사항을 제거합니다. 또한 나중에 더 흥미로운 데이터 준비 기능을 제공할 수 있는 길도 열어줍니다. 
- 버전별 응용 프로그램 홈페이지를 사용하도록 설정했으므로 현재 응용 프로그램 버전에 따라 관련성이 더 높은 릴리스 정보와 업데이트 메시지가 표시됩니다.
- 로컬 사용자 이름에 공백이 있어도 응용 프로그램을 성공적으로 설치할 수 있습니다. 

### <a name="detailed-updates"></a>자세한 업데이트
다음은 이 스프린트의 Azure Machine Learning 구성 요소 영역 각각에 대한 자세한 업데이트 목록입니다.

#### <a name="installer"></a>설치 관리자
- 앱 설치 관리자에서 이전 버전의 앱으로 만든 설치 디렉터리를 정리합니다.
- 설치 관리자가 macOS High Sierra에서 완전히 멈추는 버그를 수정했습니다.
- 설치가 실패한 경우 사용자가 설치 로그를 검토할 수 있도록 설치 관리자 디렉터리에 대한 직접적인 링크가 있습니다.
- 사용자 이름에 공백이 있는 사용자에 대해서도 설치가 작동합니다.

#### <a name="workbench-authentication"></a>Workbench 인증
- 프록시 관리자에서 인증을 지원합니다.
- 사용자가 방화벽을 통해 로그인하더라도 로그인이 성공합니다. 
- 여러 Azure 지역에 사용자의 실험 계정이 있고 한 지역을 사용할 수 없게 되더라도 앱이 더 이상 중단되지 않습니다.
- 인증이 완료되지 않고 인증 대화 상자가 계속 표시되면 앱이 로컬 캐시에서 작업 영역을 더 이상 로드하지 않습니다.

#### <a name="workbench-app"></a>Workbench 앱
- 텍스트 편집기에서 Python 코드 구문의 강조 표시를 사용할 수 있습니다.
- 텍스트 편집기의 [편집] 단추를 사용하면 IDE(VS Code 및 PyCharm이 지원됨) 또는 기본 제공 텍스트 편집기에서 파일을 편집할 수 있습니다.
- 텍스트 편집기는 기본적으로 읽기 전용 모드입니다. 
- 현재 파일이 저장되고 더 이상 손상되지 않으면 [저장] 단추의 시각적 상태가 '사용 안 함'으로 변경됩니다.
- 실행이 시작되면 Workbench에서 저장되지 않은 _모든_ 파일을 저장합니다.
- Workbench는 로컬 컴퓨터에서 마지막으로 사용한 작업 영역을 기억하여 자동으로 열리도록 합니다.
- 단일 Workbench 인스턴스만 실행할 수 있습니다. 이전에는 동일한 프로젝트에서 작동할 때 문제가 발생한 여러 인스턴스가 시작될 수 있었습니다.
- [파일] 메뉴의 "프로젝트 열기..."가 "프로젝트로 기존 폴더 추가..."로 변경되었습니다. 
- 탭 전환이 훨씬 빨라졌습니다.
- IDE 구성 대화 상자에 [도움말] 링크가 추가되었습니다.
- 피드백 양식에서 마지막으로 입력한 이메일 주소가 기억됩니다.
- 웃는 얼굴과 찡그린 얼굴 양식의 텍스트 영역이 더 커졌으므로 피드백을 자세히 보낼 수 있습니다! 
- `az ml workspace create`의 `--owner` 스위치 도움말 텍스트가 수정되었습니다.
- 사용자가 앱 버전 번호를 쉽게 보고 복사할 수 있도록 '정보' 대화 상자를 추가했습니다.
- "기능 제안" 메뉴 항목이 도움말 메뉴에 추가되었습니다.
- 실험 계정 이름이 앱 제목 표시줄의 "Azure Machine Learning Workbench" 이름 앞에 표시됩니다.
- 버전별 앱 홈페이지가 검색된 앱 버전에 따라 표시됩니다.

#### <a name="data-preparation"></a>데이터 준비 
- 잠재적인 보안 문제를 방지하기 위해 맵 검사기에서 외부 웹 사이트를 더 이상 로드할 수 없습니다.
- 히스토그램 및 값 개수 검사기에 그래프를 로그 눈금으로 표시할 수 있는 옵션이 있습니다.
- 계산이 진행 중일 때 "계산 중" 상태를 알리기 위해 데이터 품질 표시줄이 다른 색으로 표시됩니다.
- 열 메트릭에서 범주 값 열에 대한 통계가 표시됩니다.
- 데이터 원본 이름의 마지막 문자가 더 이상 잘리지 않습니다.
- 탭을 전환할 때 데이터 준비 패키지는 열린 채로 있으므로 성능이 크게 향상되었습니다.
- 데이터 원본에서 데이터 보기와 메트릭 보기 간에 전환할 때 열 순서가 더 오래 변경됩니다.
- 잘못된 `.dprep` 또는 `.dsource` 파일을 열더라도 Workbench가 더 이상 충돌하지 않습니다.
- _CSV로 쓰기_ 변환 시 데이터 준비 패키지에서 출력에 대한 상대 경로를 사용할 수 있습니다.
- _열 유지_ 변환을 사용하면 사용자가 편집할 때 추가 열을 추가할 수 있습니다.
- _바꾸기_ 메뉴에서 이제는 실제로 _값 바꾸기_ 대화 상자가 시작됩니다.
- _값 바꾸기_ 변환이 오류를 throw하는 대신 예상대로 작동합니다.
- 데이터 준비 패키지에서 이제 프로젝트 폴더 외부의 데이터 파일을 참조할 때 절대 경로를 사용하므로, 데이터 파일에 대한 절대 경로가 포함된 로컬 컨텍스트에서 패키지를 실행할 수 있습니다.
- Azure Blob을 데이터 원본으로 사용하는 경우 샘플링 전략으로 _전체 파일_이 지원됩니다.
- 생성된 Python 코드(데이터 준비 패키지에서)는 이제 CR과 LF를 모두 전달하여 Windows에서 친숙하게 되었습니다.
- 데이터 보기로 전환하는 경우 _메트릭 선택_ 드롭다운에서 속성을 숨깁니다.
- Workbench에서 Python 런타임을 사용하는 경우에도 parquet 파일을 처리할 수 있습니다. 이전에는 Spark만 사용하여 parquet 파일을 처리할 수 있었습니다. 
- 열 값을 _날짜_ 데이터 형식으로 필터링하더라도 데이터 준비 엔진이 더 이상 충돌하지 않습니다.
- 메트릭 보기에 샘플링 전략 업데이트가 적용됩니다.
- 원격 샘플링 작업이 이제 올바르게 작동합니다.

#### <a name="job-execution"></a>작업 실행
- 인수가 실행 기록 레코드에 포함됩니다.
- CLI에서 시작된 작업이 [실행 기록 작업] 패널에 자동으로 표시됩니다.
- Azure AD 테넌트에 추가된 게스트 사용자가 만든 작업이 [작업] 패널에 표시됩니다.
- 작업 패널의 취소 및 삭제 작업이 보다 안정적입니다.
- 구성 파일의 형식이 잘못된 경우 [실행] 단추를 클릭하면 오류 메시지가 트리거됩니다.
- 앱을 종료하더라도 CLI에서 시작된 작업은 더 이상 방해받지 않습니다.
- CLI에서 시작된 작업은 1시간 동안 실행한 후에도 표준 출력을 계속 내보냅니다.
- Python/PySpark에서 데이터 준비 패키지 실행이 실패하는 경우 더 나은 오류 메시지가 표시됩니다.
- `az ml experiment clean`은 이제 원격 VM에서도 Docker 이미지를 정리합니다.
- `az ml experiment clean`은 이제 macOS에서 로컬 대상에 대해 제대로 작동합니다.
- 로컬 또는 원격 Docker를 실행 대상으로 지정할 때의 오류 메시지가 정리되고 쉽게 읽을 수 있습니다.
- HDInsight 클러스터 헤드 노드 이름이 실행 대상으로 연결될 때 해당 형식이 올바르게 지정되지 않은 경우 더 나은 오류 메시지가 표시됩니다.
- 자격 증명 서비스에서 비밀을 찾을 수 없는 경우 더 나은 오류 메시지가 표시됩니다. 
- MMLSpark 라이브러리가 Apache Spark 2.2를 지원하도록 업그레이드되었습니다.
- 의료 문서에 대한 주체 인코딩 변환(메시 인코딩)이 MMLSpark에 포함되었습니다.
- `matplotlib` 버전 2.1.0이 Workbench와 함께 즉시 제공됩니다.

#### <a name="jupyter-notebook"></a>Jupyter 노트북
- Notebook 이름 검색이 Notebook 보기에서 제대로 작동합니다.
- Notebook 보기에서 Notebook을 삭제할 수 있습니다.
- Notebook 실행 환경에서 생성된 파일을 실행 기록 데이터 저장소에 업로드하는 새로운 `%upload_artifact` 매직이 추가되었습니다.
- 더 쉬운 디버깅을 위해 커널 오류가 Notebook 작업 상태에 표시됩니다.
- 사용자가 앱에서 로그아웃할 때 Jupyter 서버가 제대로 종료됩니다.

#### <a name="azure-portal"></a>Azure portal
- 유럽 서부와 동남 아시아의 새로운 두 Azure 지역에서 실험 계정과 모델 관리 계정을 만들 수 있습니다.
- 모델 관리 계정 DevTest 계획은 구독에서 처음 만드는 경우에만 사용할 수 있습니다. 
- Azure Portal의 [도움말] 링크에서 올바른 설명서 페이지를 가리키도록 업데이트되었습니다.
- [설명] 필드는 적용할 수 없으므로 Docker 이미지 세부 정보 페이지에서 제거되었습니다.
- AppInsights 및 자동 크기 조정 설정이 포함된 세부 정보가 웹 서비스 세부 정보 페이지에 추가되었습니다.
- 브라우저에서 타사 쿠키를 사용하지 않도록 설정한 경우에도 모델 관리 페이지가 렌더링됩니다. 

#### <a name="operationalization"></a>운영화
- 이름에 "score"가 있는 웹 서비스가 더 이상 실패하지 않습니다.
- 사용자가 Azure 리소스 그룹 또는 구독에 대한 참여자 액세스 권한만으로도 배포 환경을 만들 수 있습니다. 전체 구독에 대한 소유자 액세스 권한이 더 이상 필요하지 않습니다.
- 조작화 CLI는 이제 Linux에서 탭 자동 완성 기능을 사용할 수 있습니다.
- 이미지 생성 서비스에서 Azure IoT 서비스/장치에 대한 이미지 빌드를 지원합니다.

#### <a name="sample-projects"></a>샘플 프로젝트
- [_아이리스 분류_](./tutorial-classifying-iris-part-1.md) 샘플 프로젝트:
    - `iris_pyspark.py`의 이름이 `iris_spark.py`(으)로 바뀌었습니다.
    - `iris_score.py`의 이름이 `score_iris.py`(으)로 바뀌었습니다.
    - `iris.dprep` 및 `iris.dsource`은 최신 데이터 준비 엔진 업데이트를 반영하도록 업데이트되었습니다.
    - `iris.ipynb` Notebook이 HDInsight 클러스터에서 작동하도록 수정되었습니다.
    - `iris.ipynb` Notebook 셀에서 실행 기록은 켜져 있습니다.
- [_자전거 공유 데이터를 사용하여 고급 데이터 준비_](./tutorial-bikeshare-dataprep.md) 샘플 프로젝트: "오류 값 처리" 단계가 수정되었습니다.
- [_성인 인구 조사 데이터에 대한 MMLSpark_](https://github.com/Azure/MachineLearningSamples-mmlspark) 샘플 프로젝트 `docker.runconfig` 형식이 JSON에서 YAML로 업데이트되었습니다.
- [_하이퍼 매개 변수 분산 튜닝_](./scenario-distributed-tuning-of-hyperparameters.md) 샘플 프로젝트 `docker.runconfig` 형식이 JSON에서 YAML로 업데이트되었습니다.
- 새 샘플 프로젝트 [_CNTK를 사용하여 이미지 분류_](./scenario-image-classification-using-cntk.md)


## <a name="2017-10-sprint-0"></a>2017-10(스프린트 0) 
**버전 번호**: 0.1.1710.31013 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;([버전 확인](../service/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

Microsoft Ignite 2017 컨퍼런스에서 최초 공용 미리 보기를 선보인 후에 출시된 Azure Machine Learning Workbench 최초 업데이트입니다. 이 릴리스에서는 주로 안정성이 개선되고 안정화 문제가 수정되었습니다.  해결된 중요한 문제 일부는 다음과 같습니다.

### <a name="new-features"></a>새로운 기능
- macOS High Sierra가 이제 지원됩니다.

### <a name="bug-fixes"></a>버그 수정
#### <a name="workbench-experience"></a>Workbench 환경
- Workbench로 파일을 끌어다 놓으면 Workbench가 충돌합니다.
- Workbench용 IDE로 구성된 VS 코드의 터미널 창이 _az ml_ 명령을 인식하지 못합니다.

#### <a name="workbench-authentication"></a>Workbench 인증
여러 업데이트를 통해 보고된 다양한 로그인 및 인증 문제를 개선했습니다.
- 특히 인터넷 연결이 안정적이지 않은 경우 인증 창이 계속 팝업됩니다.
- 인증 토큰 만료에 대한 안정성 문제가 개선되었습니다.
- 경우에 따라 인증 창이 두 번 나타납니다.
- 인증 프로세스가 완료되고 팝업 대화 상자가 이미 닫혀 있어도 Workbench 주 창에는 "인증 중" 메시지가 여전히 표시됩니다.
- 인터넷 연결이 없는 인증 대화 상자가 빈 화면으로 팝업됩니다.

#### <a name="data-preparation"></a>데이터 준비 
- 특정 값이 필터링되면 오류 및 누락 값도 필터링됩니다.
- 샘플링 전략을 변경하면 기존의 후속 조인 작업이 제거됩니다.
- 누락 값 대체 변환 시 NaN가 고려되지 않습니다.
- Null 값이 발견되면 데이터 형식 유추가 예외를 throw합니다.

#### <a name="job-execution"></a>작업 실행
- 작업 실행이 크기 제한을 초과하기 때문에 프로젝트 폴더를 업로드하지 못한다는 명확한 오류 메시지가 없습니다.
- Python 스크립트가 작업 디렉터리를 변경하는 경우 출력 폴더에 기록된 파일이 추적되지 않습니다. 
- 활성 Azure 구독이 현재 프로젝트가 속한 구독과 다를 경우 작업 제출 시 403 오류가 발생합니다.
- Docker가 없는 경우 사용자가 Docker를 실행 대상으로 사용하려고 할 경우 명확한 오류 메시지가 반환되지 않습니다.
- _실행_ 단추를 클릭하면 .runconfig 파일이 자동으로 저장되지 않습니다.

#### <a name="jupyter-notebook"></a>Jupyter 노트북
- 사용자가 특정 로그인 유형을 사용하는 경우 노트북 서버를 시작할 수 없습니다.
- 노트북 서버 오류 메시지는 사용자가 볼 수 있는 로그에 표시되지 않습니다.

#### <a name="azure-portal"></a>Azure portal
- Azure Portal의 어두운 테마를 선택하면 모델 관리 블레이드가 검은색 상자로 표시됩니다.

#### <a name="operationalization"></a>운영화
- 매니페스트를 사용하여 웹 서비스를 업데이트하면 임의 이름의 Docker 이미지가 새로 빌드됩니다.
- 웹 서비스 로그를 Kubernetes 클러스터에서 검색할 수 없습니다.
- 사용자가 모델 관리 계정이나 ML Compute 계정 키를 만들려고 할 때 사용 권한 문제가 발생하면 잘못된 오류 메시지가 출력됩니다.

## <a name="next-steps"></a>다음 단계

[Azure Machine Learning](../service/overview-what-is-azure-ml.md)에 대한 개요를 참조하세요.
