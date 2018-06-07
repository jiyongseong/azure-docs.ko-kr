
특정 함수 앱의 모든 기능에 대한 코드는 호스트 구성 파일 및 하나 이상의 하위 폴더를 포함하는 루트 폴더에 있습니다. 각 하위 폴더에는 다음 예제와 같이 개별 함수에 대한 코드가 포함됩니다.

```
wwwroot
 | - host.json
 | - mynodefunction
 | | - function.json
 | | - index.js
 | | - node_modules
 | | | - ... packages ...
 | | - package.json
 | - mycsharpfunction
 | | - function.json
 | | - run.csx
```

host.json 파일은 일부 런타임별 구성을 포함하며 함수 앱의 루트 폴더에 있습니다. 사용할 수 있는 설정에 대한 자세한 내용은 [host.json 참조](../articles/azure-functions/functions-host-json.md)를 참조하세요.

각 함수에는 하나 이상의 코드 파일, function.json 구성 및 기타 종속성을 포함하는 폴더가 있습니다.

