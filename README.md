# gRPC C# Sample Project

This is a GitHub-ready gRPC example using C# (.NET 6+), demonstrating a simple Greeter service with a client-server setup.

---

## 📁 Project Structure

```
GrpcCSharpSample/
├── GrpcServer/          # gRPC server project
│   ├── Program.cs
│   ├── Services/
│   │   └── GreeterService.cs
│   ├── Protos/
│   │   └── greet.proto
│   ├── GrpcServer.csproj
│   └── appsettings.json
│
├── GrpcClient/          # Console client to call gRPC service
│   ├── Program.cs
│   ├── Protos/
│   │   └── greet.proto
│   └── GrpcClient.csproj
```

---

## 🛠 Setup Instructions

### Prerequisites:
- [.NET SDK 6.0+](https://dotnet.microsoft.com/download)
- Visual Studio 2022+ or VS Code

### Build Server:
```bash
cd GrpcServer
dotnet restore
```

### Build Client:
```bash
cd GrpcClient
dotnet restore
```

---

## 📡 Greeter Proto Definition

**`Protos/greet.proto`** (shared by both Server and Client)
```proto
syntax = "proto3";

option csharp_namespace = "GrpcDemo";

package greet;

service Greeter {
  rpc SayHello (HelloRequest) returns (HelloReply);
}

message HelloRequest {
  string name = 1;
}

message HelloReply {
  string message = 1;
}
```

---

## 🖥 GrpcServer

### `Program.cs`
```csharp
var builder = WebApplication.CreateBuilder(args);
builder.Services.AddGrpc();

var app = builder.Build();
app.MapGrpcService<GreeterService>();
app.MapGet("/", () => "This server hosts a gRPC service.");
app.Run();
```

### `GreeterService.cs`
```csharp
using Grpc.Core;
using GrpcDemo;

public class GreeterService : Greeter.GreeterBase
{
    public override Task<HelloReply> SayHello(HelloRequest request, ServerCallContext context)
    {
        return Task.FromResult(new HelloReply
        {
            Message = $"Hello, {request.Name}"
        });
    }
}
```

### `GrpcServer.csproj`
```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
  <PropertyGroup>
    <TargetFramework>net6.0</TargetFramework>
  </PropertyGroup>
  <ItemGroup>
    <PackageReference Include="Grpc.AspNetCore" Version="2.52.0" />
  </ItemGroup>
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
  </ItemGroup>
</Project>
```

---

## 🧪 GrpcClient

### `Program.cs`
```csharp
using Grpc.Net.Client;
using GrpcDemo;

var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greeter.GreeterClient(channel);

var reply = await client.SayHelloAsync(new HelloRequest { Name = "Deepak" });
Console.WriteLine("Greeting: " + reply.Message);
```

### `GrpcClient.csproj`
```xml
<Project Sdk="Microsoft.NET.Sdk">
  <PropertyGroup>
    <OutputType>Exe</OutputType>
    <TargetFramework>net6.0</TargetFramework>
  </PropertyGroup>
  <ItemGroup>
    <PackageReference Include="Grpc.Net.Client" Version="2.52.0" />
    <PackageReference Include="Grpc.Tools" Version="2.52.0" PrivateAssets="All" />
    <PackageReference Include="Google.Protobuf" Version="3.24.0" />
  </ItemGroup>
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
</Project>
```

---

## ✅ Running the App

1. **Run the server**:
```bash
cd GrpcServer
dotnet run
```

2. **In another terminal, run the client**:
```bash
cd GrpcClient
dotnet run
```

> You should see: `Greeting: Hello, Deepak`

---

## 🔐 Optional Enhancements
- Add SSL certificates for secure gRPC communication.
- Add logging/interceptors.
- Add streaming/bidirectional RPC methods.
- Deploy the server to Azure App Service or Kubernetes.


