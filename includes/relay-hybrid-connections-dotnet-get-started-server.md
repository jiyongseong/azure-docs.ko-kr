### <a name="create-a-console-application"></a>콘솔 응용 프로그램 만들기

Visual Studio에서 새 **콘솔 앱(.NET Framework)** 프로젝트를 만듭니다.

### <a name="add-the-relay-nuget-package"></a>릴레이 NuGet 패키지 추가

1. 마우스 오른쪽 단추로 새로 만든 프로젝트를 클릭한 다음 **NuGet 패키지 관리**를 선택합니다.
2. **찾아보기**를 선택한 다음 **Microsoft.Azure.Relay**를 검색합니다. 검색 결과에서 **Microsoft Azure Relay**를 선택합니다. 
3. **설치**을 선택하여 설치를 완료합니다. 대화 상자를 닫습니다.

### <a name="write-code-to-receive-messages"></a>메시지를 받는 코드 작성

1. Program.cs 파일의 맨 위에 있는 기존 `using` 문을 다음 `using` 문으로 바꿉니다.
   
    ```csharp
    using System;
    using System.IO;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.Relay;
    ```
2. 상수를 하이브리드 연결 정보의 `Program` 클래스에 추가합니다. 대괄호에 포함된 자리 표시자를 하이브리드 연결을 만들 때 가져온 값으로 바꿉니다. 정규화된 네임스페이스 이름을 사용해야 합니다.
   
    ```csharp
    private const string RelayNamespace = "{RelayNamespace}.servicebus.windows.net";
    private const string ConnectionName = "{HybridConnectionName}";
    private const string KeyName = "{SASKeyName}";
    private const string Key = "{SASKey}";
    ```
3. `Program` 클래스에 `ProcessMessagesOnConnection` 메서드를 추가합니다.
   
    ```csharp
    // The method initiates the connection.
    private static async void ProcessMessagesOnConnection(HybridConnectionStream relayConnection, CancellationTokenSource cts)
    {
        Console.WriteLine("New session");
   
        // The connection is a fully bidrectional stream. 
        // Put a stream reader and a stream writer over it.  
        // This allows you to read UTF-8 text that comes from 
        // the sender, and to write text replies back.
        var reader = new StreamReader(relayConnection);
        var writer = new StreamWriter(relayConnection) { AutoFlush = true };
        while (!cts.IsCancellationRequested)
        {
            try
            {
                // Read a line of input until a newline is encountered.
                var line = await reader.ReadLineAsync();
   
                if (string.IsNullOrEmpty(line))
                {
                    // If there's no input data, signal that 
                    // you will no longer send data on this connection,
                    // and then break out of the processing loop.
                    await relayConnection.ShutdownAsync(cts.Token);
                    break;
                }
   
                // Write the line on the console.
                Console.WriteLine(line);
   
                // Write the line back to the client, prepended with "Echo:"
                await writer.WriteLineAsync($"Echo: {line}");
            }
            catch (IOException)
            {
                // Catch an I/O exception. This likely occurred when
                // the client disconnected.
                Console.WriteLine("Client closed connection");
                break;
            }
        }
   
        Console.WriteLine("End session");
   
        // Close the connection.
        await relayConnection.CloseAsync(cts.Token);
    }
    ```
4. `Program` 클래스에 `RunAsync` 메서드를 추가합니다.
   
    ```csharp
    private static async Task RunAsync()
    {
        var cts = new CancellationTokenSource();
   
        var tokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(KeyName, Key);
        var listener = new HybridConnectionListener(new Uri(string.Format("sb://{0}/{1}", RelayNamespace, ConnectionName)), tokenProvider);
   
        // Subscribe to the status events.
        listener.Connecting += (o, e) => { Console.WriteLine("Connecting"); };
        listener.Offline += (o, e) => { Console.WriteLine("Offline"); };
        listener.Online += (o, e) => { Console.WriteLine("Online"); };
   
        // Opening the listener establishes the control channel to
        // the Azure Relay service. The control channel is continuously 
        // maintained, and is reestablished when connectivity is disrupted.
        await listener.OpenAsync(cts.Token);
        Console.WriteLine("Server listening");
   
        // Provide callback for the cancellation token that will close the listener.
        cts.Token.Register(() => listener.CloseAsync(CancellationToken.None));
   
        // Start a new thread that will continuously read the console.
        new Task(() => Console.In.ReadLineAsync().ContinueWith((s) => { cts.Cancel(); })).Start();
   
        // Accept the next available, pending connection request. 
        // Shutting down the listener allows a clean exit. 
        // This method returns null.
        while (true)
        {
            var relayConnection = await listener.AcceptConnectionAsync();
            if (relayConnection == null)
            {
                break;
            }
   
            ProcessMessagesOnConnection(relayConnection, cts);
        }
   
        // Close the listener after you exit the processing loop.
        await listener.CloseAsync(cts.Token);
    }
    ```
5. `Program` 클래스의 `Main` 메서드에 다음 코드 줄을 추가합니다.
   
    ```csharp
    RunAsync().GetAwaiter().GetResult();
    ```
   
    완성된 Program.cs 파일은 다음과 같이 표시됩니다.
   
    ```csharp
    namespace Server
    {
        using System;
        using System.IO;
        using System.Threading;
        using System.Threading.Tasks;
        using Microsoft.Azure.Relay;
   
        public class Program
        {
            private const string RelayNamespace = "{RelayNamespace}.servicebus.windows.net";
            private const string ConnectionName = "{HybridConnectionName}";
            private const string KeyName = "{SASKeyName}";
            private const string Key = "{SASKey}";
   
            public static void Main(string[] args)
            {
                RunAsync().GetAwaiter().GetResult();
            }
   
            private static async Task RunAsync()
            {
                var cts = new CancellationTokenSource();
   
                var tokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(KeyName, Key);
                var listener = new HybridConnectionListener(new Uri(string.Format("sb://{0}/{1}", RelayNamespace, ConnectionName)), tokenProvider);
   
                // Subscribe to the status events.
                listener.Connecting += (o, e) => { Console.WriteLine("Connecting"); };
                listener.Offline += (o, e) => { Console.WriteLine("Offline"); };
                listener.Online += (o, e) => { Console.WriteLine("Online"); };
   
                // Opening the listener establishes the control channel to
                // the Azure Relay service. The control channel is continuously 
                // maintained, and is reestablished when connectivity is disrupted.
                await listener.OpenAsync(cts.Token);
                Console.WriteLine("Server listening");
   
                // Provide callback for a cancellation token that will close the listener.
                cts.Token.Register(() => listener.CloseAsync(CancellationToken.None));
   
                // Start a new thread that will continuously read the console.
                new Task(() => Console.In.ReadLineAsync().ContinueWith((s) => { cts.Cancel(); })).Start();
   
                // Accept the next available, pending connection request. 
                // Shutting down the listener allows a clean exit. 
                // This method returns null.
                while (true)
                {
                    var relayConnection = await listener.AcceptConnectionAsync();
                    if (relayConnection == null)
                    {
                        break;
                    }
   
                    ProcessMessagesOnConnection(relayConnection, cts);
                }
   
                // Close the listener after you exit the processing loop.
                await listener.CloseAsync(cts.Token);
            }
   
            private static async void ProcessMessagesOnConnection(HybridConnectionStream relayConnection, CancellationTokenSource cts)
            {
                Console.WriteLine("New session");
   
                // The connection is a fully bidrectional stream. 
                // Put a stream reader and a stream writer over it.  
                // This allows you to read UTF-8 text that comes from 
                // the sender, and to write text replies back.
                var reader = new StreamReader(relayConnection);
                var writer = new StreamWriter(relayConnection) { AutoFlush = true };
                while (!cts.IsCancellationRequested)
                {
                    try
                    {
                        // Read a line of input until a newline is encountered.
                        var line = await reader.ReadLineAsync();
   
                        if (string.IsNullOrEmpty(line))
                        {
                            // If there's no input data, signal that 
                            // you will no longer send data on this connection.
                            // Then, break out of the processing loop.
                            await relayConnection.ShutdownAsync(cts.Token);
                            break;
                        }
   
                        // Write the line on the console.
                        Console.WriteLine(line);
   
                        // Write the line back to the client, prepended with "Echo:"
                        await writer.WriteLineAsync($"Echo: {line}");
                    }
                    catch (IOException)
                    {
                        // Catch an I/O exception. This likely occurred when
                        // the client disconnected.
                        Console.WriteLine("Client closed connection");
                        break;
                    }
                }
   
                Console.WriteLine("End session");
   
                // Close the connection.
                await relayConnection.CloseAsync(cts.Token);
            }
        }
    }
    ```

