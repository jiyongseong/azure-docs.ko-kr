## <a name="create-a-ruby-application"></a>Ruby 응용 프로그램 만들기
자세한 내용은 [Azure에서 Ruby 응용 프로그램 만들기](../articles/virtual-machines/linux/classic/ruby-rails-web-app.md)를 참조하세요.

## <a name="configure-your-application-to-use-service-bus"></a>Service Bus를 사용하도록 응용 프로그램 구성
Service Bus를 사용하려면 저장소 REST 서비스와 통신하는 편리한 라이브러리 집합이 포함된 Azure Ruby 패키지를 다운로드하여 사용합니다.

### <a name="use-rubygems-to-obtain-the-package"></a>RubyGems를 사용하여 패키지 가져오기
1. **PowerShell**(Windows), **Terminal**(Mac) 또는 **Bash**(Unix)와 같은 명령줄 인터페이스를 사용합니다.
2. 명령 창에 "gem install azure"를 입력하여 gem 및 종속성을 설치합니다.

### <a name="import-the-package"></a>패키지 가져오기
원하는 텍스트 편집기를 사용하여 저장소를 사용하려는 Ruby 파일의 맨 위에 다음을 추가합니다.

```ruby
require "azure"
```

## <a name="set-up-a-service-bus-connection"></a>Service Bus 연결 설정
다음 코드를 사용하여 네임스페이스, 키 이름, 키, 서명자 및 호스트에 대한 값을 설정합니다.

```ruby
Azure.configure do |config|
  config.sb_namespace = '<your azure service bus namespace>'
  config.sb_sas_key_name = '<your azure service bus access keyname>'
  config.sb_sas_key = '<your azure service bus access key>'
end
signer = Azure::ServiceBus::Auth::SharedAccessSigner.new
sb_host = "https://#{Azure.sb_namespace}.servicebus.windows.net"
```

네임스페이스 값을 전체 URL이 아니라 만든 값으로 설정합니다. 예를 들어 "yourexamplenamespace.servicebus.windows.net"이 아니라 **"yourexamplenamespace"**를 사용합니다.
