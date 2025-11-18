# Application Insights Showcase - Microservices Solution

## Overview
This solution consists of 4 microservices built with .NET Core 8, all integrated with Azure Application Insights for comprehensive monitoring and telemetry.

### Services:
1. **OrderService** (Port 5001) - Main API that orchestrates orders
2. **ProductService** (Port 5002) - Manages product catalog
3. **InventoryService** (Port 5003) - Handles inventory and stock
4. **PaymentService** (Port 5004) - Processes payments

## Prerequisites
- .NET Core 8 SDK
- Azure subscription with Application Insights resource
- Postman (for API testing)

## Setup Instructions

### 1. Create Azure Application Insights Resource


### 2. Configure Each Service

For each service, create an `appsettings.json` file:

**OrderService/appsettings.json:**
```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft.AspNetCore": "Warning"
    }
  },
  "AllowedHosts": "*",
  "ApplicationInsights": {
    "ConnectionString": "InstrumentationKey=..."
  }
}
```

Replace `YOUR_INSTRUMENTATION_KEY` with your actual key from step 1.

Create the same `appsettings.json` for ProductService, InventoryService, and PaymentService.

### 3. Create Project Files

For each service, create a `.csproj` file:

**OrderService/OrderService.csproj:**
```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
  <PropertyGroup>
    <TargetFramework>net8.0</TargetFramework>
    <Nullable>enable</Nullable>
    <ImplicitUsings>enable</ImplicitUsings>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.22.0" />
    <PackageReference Include="Swashbuckle.AspNetCore" Version="6.5.0" />
  </ItemGroup>
</Project>
```

Create similar `.csproj` files for ProductService, InventoryService, and PaymentService.


## Application Insights Features to Monitor

### 1. **Application Map**
- Navigate to Azure Portal → Your Application Insights resource → Application Map
- View how services communicate with each other
- See dependencies and call volumes

### 2. **Live Metrics**
- Real-time monitoring of requests, failures, and performance
- View incoming requests as they happen
- Monitor CPU, memory usage

### 3. **Performance**
- Analyze operation durations
- Identify slow dependencies
- Track service-to-service call times

### 4. **Failures**
- View failed requests and exceptions
- Analyze error rates by service
- Drill into specific failures

### 5. **Logs (Transaction Search)**
- Search for specific traces
- Filter by severity level
- Correlate logs across services

### 6. **Custom Events**
- All log statements appear as traces
- Track custom metrics
- Analyze user flows

## Key Application Insights Queries (Kusto/KQL)

Access these via Application Insights → Logs:

**1. Request Success Rate by Service:**
```kusto
requests
| summarize 
    Total = count(),
    Success = countif(success == true),
    Failed = countif(success == false),
    SuccessRate = round(100.0 * countif(success == true) / count(), 2)
  by cloud_RoleName
| order by Total desc
```

**2. Average Response Time:**
```kusto
requests
| summarize AvgDuration = avg(duration) by cloud_RoleName, name
| order by AvgDuration desc
```

**3. Failed Requests:**
```kusto
requests
| where success == false
| project timestamp, cloud_RoleName, name, resultCode, duration
| order by timestamp desc
```

**4. Dependencies Between Services:**
```kusto
dependencies
| summarize Count = count() by name, target, type
| order by Count desc
```

**5. Custom Trace Logs:**
```kusto
traces
| where message contains "Order"
| project timestamp, severityLevel, message, cloud_RoleName
| order by timestamp desc
```

**6. Exception Analysis:**
```kusto
exceptions
| summarize Count = count() by type, outerMessage
| order by Count desc
```

## Viewing Analytics in Azure Portal

1. **Navigate to Application Insights**
   - Azure Portal → Application Insights → Your resource

2. **Key Areas to Explore:**
   - **Overview**: High-level metrics
   - **Application Map**: Service topology
   - **Performance**: Response times and operations
   - **Failures**: Error analysis
   - **Live Metrics**: Real-time monitoring
   - **Logs**: Custom queries
   - **Transaction Search**: Individual request tracking

3. **End-to-End Transaction View:**
   - Go to Performance or Failures
   - Click on any operation
   - View "End-to-end transaction details"
   - See complete flow across all services


## Resources

- [Application Insights Documentation](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)
- [.NET Core 8 Documentation](https://docs.microsoft.com/dotnet/core/)
- [Kusto Query Language](https://docs.microsoft.com/azure/data-explorer/kusto/query/)