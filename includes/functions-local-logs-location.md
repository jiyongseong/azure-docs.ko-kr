Functions 호스트가 로컬로 실행되면 다음 경로에 로그를 씁니다.

```
<DefaultTempDirectory>\LogFiles\Application\Functions
```

Windows에서 `<DefaultTempDirectory>`는 TMP, TEMP, USERPROFILE 환경 변수 또는 Windows 디렉터리에서 찾은 첫 번째 값입니다.
MacOS 또는 Linux에서 `<DefaultTempDirectory>`는 TMPDIR 환경 변수입니다.

[!Note]
Functions 호스트가 시작되면 디렉터리의 기존 파일 구조를 덮어씁니다.