


# Traffic Assignment Model - UML Class Diagram

## Class Diagram (PlantUML)

```plantuml
@startuml Traffic_Assignment_Model

!define RECTANGLE class

' Core Data Structures
class Zone {
    -zoneId: str
    -lat: float
    -lon: float
    -destList: list
    +__init__(_tmpIn: list)
}

class Node {
    -Id: str
    -lat: float
    -lon: float
    -outLinks: list
    -inLinks: list
    -label: float
    -pred: str
    -inDegree: int
    -outDegree: int
    -order: int
    -wi: float
    -xi: float
    -xj: float
    +__init__(_tmpIn: list)
}

class Link {
    -tailNode: str
    -headNode: str
    -capacity: float
    -length: float
    -fft: float
    -beta: float
    -alpha: float
    -speedLimit: float
    -NMoto: int
    -NCar: int
    -flowCar: float
    -flowScooter: float
    -cost: float
    -logLike: float
    -reasonable: bool
    -wij: float
    -xij: float
    +__init__(_tmpIn: list)
}

class Demand {
    -fromZone: str
    -toNode: str
    -demand: float
    -vehType: str
    +__init__(_tmpIn: list)
}

' Main Traffic Assignment Module
class TrafficAssignmentModel {
    +{static} inputLocation: str
    +{static} CPU_COUNT: int
    +{static} tripSet: dict
    +{static} zoneSet: dict
    +{static} linkSet: dict
    +{static} nodeSet: dict
    +{static} turn_restrict: dict
    +{static} originZones: set
    
    +{static} readDemand(): void
    +{static} readNetwork(): void
    +{static} readTurnRestrictions(filename: str): void
    +{static} DijkstraHeap(origin: str, vehType: str): void
    +{static} tracePreds(dest: str): list
    +{static} updateTravelTime(): void
    +{static} findAlpha(x_bar_car: dict, x_bar_scooter: dict, iteration: int): float
    +{static} golden_section_search(func: callable, a: float, b: float, tol: float, max_iter: int): float
    +{static} loadAON(): tuple
    +{static} writeUEresults(): void
    +{static} assignment(loading: str, algorithm: str, accuracy: float, maxIter: int, allow: int, recal: bool, parallel_loading: bool): dict
    +{static} checkNetworkHealth(): void
    +{static} savetoShapefile(): void
    +{static} get_capacity(row: object, cap_dict: dict): int
}

' Optimization and Algorithm Components
class OptimizationAlgorithms <<utility>> {
    +{static} fsolve(func: callable, initial_guess: float): tuple
    +{static} bisect(func: callable, a: float, b: float, xtol: float): float
}

class GeospatialProcessor <<utility>> {
    +{static} read_file(filepath: str): GeoDataFrame
    +{static} merge(gdf1: GeoDataFrame, gdf2: DataFrame, on: list, how: str): GeoDataFrame
    +{static} to_file(gdf: GeoDataFrame, filename: str): void
}

class NetworkAnalyzer <<utility>> {
    +{static} heapq_push(heap: list, item: tuple): void
    +{static} heapq_pop(heap: list): tuple
    +{static} calculate_BPR(flow: float, capacity: float, fft: float, alpha: float, beta: float): float
}

' External Dependencies
class ExternalLibraries <<external>> {
    +scipy.optimize
    +numpy
    +matplotlib.pyplot
    +pandas
    +geopandas
    +heapq
    +time
    +os
    +concurrent.futures
}

' Relationships
Zone ||--o{ Demand : "has origin/destination"
Node ||--o{ Link : "connected by"
Link }o--|| Node : "connects"
Demand }o--|| Zone : "originates from"
Demand }o--|| Zone : "destined to"

TrafficAssignmentModel o-- Zone : "manages"
TrafficAssignmentModel o-- Node : "manages"
TrafficAssignmentModel o-- Link : "manages"
TrafficAssignmentModel o-- Demand : "manages"

TrafficAssignmentModel ..> OptimizationAlgorithms : "uses"
TrafficAssignmentModel ..> GeospatialProcessor : "uses"
TrafficAssignmentModel ..> NetworkAnalyzer : "uses"
TrafficAssignmentModel ..> ExternalLibraries : "depends on"

' Notes
note right of TrafficAssignmentModel::assignment
  Main assignment algorithm supporting:
  - Multi-class traffic (car/scooter)
  - Frank-Wolfe (FW) algorithm
  - Method of Successive Averages (MSA)
  - Turn restrictions and penalties
end note

note right of Link
  Supports multi-modal traffic:
  - flowCar: Car flow
  - flowScooter: Scooter flow
  - NMoto/NCar: Vehicle restrictions
end note

note right of Demand
  Vehicle type classification:
  - '1': Car
  - '2': Scooter/Motorcycle
end note

@enduml
```

## Class Descriptions

### Core Data Classes

#### Zone
- 代表交通分析區域 (Traffic Analysis Zone, TAZ)
- 儲存區域ID及其目的地清單
- 座標資訊用於地理定位

#### Node
- 交通網路中的節點 (路口、端點)
- 包含Dijkstra演算法所需的標籤和前驅節點
- 支援Dial演算法的權重計算

#### Link
- 交通網路中的連結 (道路段)
- 支援多車種流量 (汽車/機車)
- 包含BPR函數參數用於旅行時間計算
- 車種禁行設定 (NMoto, NCar)

#### Demand
- OD需求矩陣的基本單元
- 包含車種資訊支援多車種指派

### Main Controller Class

#### TrafficAssignmentModel
- 主要的交通指派模型類別
- 實作Frank-Wolfe演算法和MSA方法
- 支援多車種交通指派
- 處理轉向限制和懲罰
- 整合地理資訊系統輸出

### Key Features

1. **多車種支援**: 同時處理汽車和機車的交通指派
2. **演算法選擇**: 支援Frank-Wolfe和MSA演算法
3. **轉向限制**: 處理禁止轉向和機車兩段式左轉
4. **地理資訊整合**: 支援Shapefile格式的網路資料
5. **效能優化**: 使用Heap-based Dijkstra和多執行緒處理

## Methods Summary

### 主要演算法方法
- `DijkstraHeap()`: 多車種最短路徑演算法
- `loadAON()`: All-or-Nothing交通量載入
- `findAlpha()`: 線性搜尋找最佳步長
- `assignment()`: 主要交通指派演算法

### 資料處理方法
- `readDemand()`: 讀取OD需求資料
- `readNetwork()`: 讀取道路網路資料
- `readTurnRestrictions()`: 讀取轉向限制資料
- `savetoShapefile()`: 輸出結果至GIS格式

### 輔助方法
- `updateTravelTime()`: 使用BPR函數更新旅行時間
- `checkNetworkHealth()`: 檢查網路連通性
- `tracePreds()`: 回溯最短路徑
