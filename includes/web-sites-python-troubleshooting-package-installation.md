일부 패키지는 Azure에서 실행할 때 pip를 사용하여 설치되지 않을 수 있습니다.  단순히 Python Package Index에 해당 패키지가 없기 때문일 수 있습니다.  아니면, 컴파일러가 필요할 수도 있습니다(Azure 앱 서비스에서 웹앱을 실행하는 컴퓨터에서는 컴파일러를 사용할 수 없음).

이 섹션에서는 이 문제를 처리하는 방법을 살펴봅니다.

### <a name="request-wheels"></a>휠 요청
패키지를 설치하는 데 컴파일러가 필요한 경우 패키지 소유자에게 문의하여 해당 패키지에 대해 휠을 사용할 수 있도록 요청해야 합니다.

최근 [Microsoft Visual C++ Compiler for Python 2.7][Microsoft Visual C++ Compiler for Python 2.7]을 출시한 이후로, 이제 Python 2.7용 네이티브 코드가 있는 패키지를 빌드하는 것이 더 쉬워졌습니다.

### <a name="build-wheels-requires-windows"></a>휠 빌드(Windows 필요)
참고: 이 옵션을 사용할 때는 Azure 앱 서비스의 웹앱에서 사용되는 플랫폼/아키텍처/버전(Windows/32비트/2.7 또는 3.4)과 일치하는 Python 환경을 사용하여 패키지를 컴파일해야 합니다.

컴파일러가 필요하므로 패키지가 설치되지 않는 경우에는 로컬 컴퓨터에 컴파일러를 설치하고 패키지용 휠을 빌드할 수 있습니다. 그런 다음 이 휠을 리포지토리에 포함합니다.

Mac/Linux 사용자: Windows 컴퓨터에 액세스할 수 없는 경우 Azure에서 VM을 만드는 방법에 대한 자세한 내용은 [Windows를 실행하는 가상 컴퓨터 만들기][Create a Virtual Machine Running Windows]를 참조하세요.  이 내용을 참조하여 휠을 빌드하여 리포지토리에 추가하고 원하는 경우 VM을 취소할 수 있습니다. 

Python 2.7의 경우 [Microsoft Visual C++ Compiler for Python 2.7][Microsoft Visual C++ Compiler for Python 2.7]을 설치하면 됩니다.

Python 3.4의 경우 [Microsoft Visual C++ 2010 Express][Microsoft Visual C++ 2010 Express]를 설치하면 됩니다.

휠을 빌드하려면 휠 패키지가 필요합니다.

    env\scripts\pip install wheel

`pip wheel`을 사용하여 종속성을 컴파일합니다.

    env\scripts\pip wheel azure==0.8.4

이렇게 하면 \wheelhouse 폴더에 .whl 파일이 만들어집니다.  \wheelhouse 폴더 및 휠 파일을 리포지토리에 추가합니다.

requirements.txt를 편집하여 맨 위에 `--find-links` 옵션을 추가합니다. 이렇게 하면 Python Package Index로 이동하기 전에 로컬 폴더에 정확한 일치 항목이 있는지 찾아 보도록 pip에게 지시합니다.

    --find-links wheelhouse
    azure==0.8.4

모든 종속성을 \wheelhouse 폴더에 포함하고 Python Package Index는 전혀 사용하지 않으려는 경우 requirements.txt 맨 위에 `--no-index`를 추가하여 pip가 패키지 인덱스를 무시하도록 만들면 됩니다.

    --no-index

### <a name="customize-installation"></a>설치 사용자 지정
easy\_install 같은 대체 설치 관리자를 사용하여 가상 환경에서 패키지를 설치하도록 배포 스크립트를 사용자 지정할 수 있습니다.  deploy.cmd에서 주석 처리된 예제를 확인합니다.  해당 패키지가 requirements.txt에 나열되지 않도록 해야 pip가 해당 패키지를 설치하지 않습니다.

다음을 배포 스크립트에 추가합니다.

    env\scripts\easy_install somepackage

easy\_install를 사용하여 exe 설치 파일에서 설치할 수도 있습니다(일부는 압축 호환되므로 easy\_install에서 지원됨).  리포지토리에 설치 관리자를 추가하고 실행 파일 경로를 전달하여 easy\_install을 호출합니다.

다음을 배포 스크립트에 추가합니다.

    env\scripts\easy_install "%DEPLOYMENT_SOURCE%\installers\somepackage.exe"

### <a name="include-the-virtual-environment-in-the-repository-requires-windows"></a>리포지토리에 가상 환경 포함(Windows 필요)
참고: 이 옵션을 사용할 때는 Azure 앱 서비스의 웹앱에서 사용되는 플랫폼/아키텍처/버전(Windows/32비트/2.7 또는 3.4)과 일치하는 가상 환경을 사용해야 합니다.

리포지토리에 가상 환경을 포함하는 경우 배포 스크립트가 빈 파일 생성을 통해 Azure에서 가상 환경을 관리하는 일을 방지할 수 있습니다.

    .skipPythonDeployment

가상 환경이 자동 관리될 때 잔존 파일들이 사용되지 않도록 하기 위해 앱의 기존 가상 환경을 삭제하는 것이 좋습니다.

[Create a Virtual Machine Running Windows]: http://azure.microsoft.com/documentation/articles/virtual-machines-windows-hero-tutorial/
[Microsoft Visual C++ Compiler for Python 2.7]: http://aka.ms/vcpython27
[Microsoft Visual C++ 2010 Express]: http://go.microsoft.com/?linkid=9709949
