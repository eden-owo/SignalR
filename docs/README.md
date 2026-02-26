# SignalR 即時噪音監測系統 (otoSignalR)

這是一個基於 **ASP.NET Core 8.0** 開發的即時噪音數據監測系統，利用 **SignalR** 技術實現伺服器端噪音數據到網頁客戶端的即時推送。

---
description: Implementing ASP.Net MVC SignalR
---

# Beforehand

Upload link: [<kbd>Gitbook</kbd>](https://edenouos-organization.gitbook.io/notes/signalr-asp.net-mvc)[<kbd>Github</kbd>](https://github.com/eden-ouob/SignalR)

***

預期呈現：以 SignalR 及時將伺服器端數據更新至網頁上

<figure><img src=".gitbook/assets/1746520282639 (2).gif" alt=""><figcaption></figcaption></figure>

***

事前步驟：

建立新專案，專案類型為 C# ASP.Net Core Web 應用程式 (Model-View-Controller)

<figure><img src=".gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

架構選擇 .net 8.0

<figure><img src=".gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

新專案建立完成後，檢查專案內是否有包含Models, View, Controllers的結構

若有，則新增 Hubs 資料夾

若無，則須重新開啟為 ASP.NET Core Web 應用程式 (MVC) 的新專案

<figure><img src=".gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>



## 核心功能
*   **即時數據推送**: 伺服器每秒讀取 `noise.txt` 並透過 SignalR Hub 廣播。
*   **自動日誌記錄**: 系統自動在 `Logs/log.txt` 記錄資料讀取錯誤或狀態。
*   **動態前端顯示**: 網頁客戶端每 500 毫秒請求更新，並以粗體顯示噪音分貝數 (dB)。

## 技術棧 (Tech Stack)
*   **後端**: .NET 8.0 (ASP.NET Core MVC)
*   **通訊**: ASP.NET Core SignalR
*   **前端**: HTML5, CSS3, JavaScript

## 專案結構
*   `Hubs/NoiseHub.cs`: 即時通訊中心，負責讀取 `noise.txt` 並發送訊息。
*   `Controllers/SignalRController.cs`: 處理首頁路由。
*   `Views/SignalR/Index.cshtml`: 前端介面與通訊客戶端。
*   `wwwroot/js/signalr.min.js`: SignalR 客戶端 SDK。

## 實作指南

### 1. 配置服務 (Program.cs)
```csharp
builder.Services.AddSignalR();
// ...
app.MapHub<NoiseHub>("/noisehub");
```

### 2. Hub 邏輯 (NoiseHub.cs)
```csharp
public async Task SendNoiseData()
{
    var filePath = Path.Combine(AppContext.BaseDirectory, "noise.txt");
    if (File.Exists(filePath))
    {
        string noiseData = File.ReadAllText(filePath);
        await Clients.All.SendAsync("ReceiveNoiseData", noiseData);
    }
}
```

### 3. 前端連線 (Index.cshtml)
```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/noiseHub")
    .build();

connection.on("ReceiveNoiseData", (data) => {
    document.getElementById("noiseData").innerText = `噪音：${data}dB`;
});

connection.start().then(() => {
    setInterval(() => connection.invoke("SendNoiseData"), 500);
});
```

## 安裝與執行
1. 確保已安裝 .NET 8.0 SDK。
2. 在專案目錄下執行：
   ```bash
   dotnet run --project otoSignalR
   ```
3. 在輸出目錄（例如 `bin/Debug/net8.0/`）建立 `noise.txt` 並寫入數據。
4. 開啟瀏覽器訪問 `https://localhost:xxxx/SignalR`。

