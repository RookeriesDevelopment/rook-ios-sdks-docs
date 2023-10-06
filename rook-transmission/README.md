

# Rook Transmission SDK

This SDK allows to transmit Health Data to the ROOK server.

## Features

- Store, retrieve and upload sleep summaries
- Store, retrieve and upload physical summaries
- Store, retrieve and upload body summaries
- Store, retrieve and upload heart rate events
- Store, retrieve and upload oxygenation events
- Store, retrieve and upload activity events
- Upload current user's time zone 

## Installation

To add a package dependency to your Xcode project, select File > Swift Packages > Add Package Dependency and enter the repository URL [https://github.com/RookeriesDevelopment/rook-ios-transmission-sdk](https://github.com/RookeriesDevelopment/rook-ios-transmission-sdk)

![include_swftpm](include_swftpm.png)

## Usage

To configure Rook Connect Transmission, you need to follow this steps:

1. Import the apple health sdk

```swift
import RookConnectTransmission
import RookTransmission
```

 2. Add your credentials. 
 - This method should be called at the beginning of your app's launch process.

```swift
func application(_ application: UIApplication
                 didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey : Any]? = nil) -> Bool {
    
    let transmissionConfiguration: RookTransmissionConfiguration = RookTransmissionConfiguration(urlAPI: "https://api.rook-connect.dev",
                                                                                                 clientUUID: "1212122-47c1-1111-a8ce-10d3f4f43127",
                                                                                                 secretKey: "secretKeyiowjdioqjwodi")
    
    
    TransmissionSettings.shared.setConfiguration(transmissionConfiguration)
    TransmissionSettings.shared.initRookTransmission()
    return true
}
```

#### TransmissionSettings

`TransmissionSettings` configures the sdk before start using the sdk and init the sdk

| Function | Description |
| -------- | ----------- |
| `setConfiguration(_ configuration: RookTransmissionConfiguration)` | Sets the configuration with `RookTransmissionConfiguration` object |
| `setEnvironment(_ environment: RookTransmissionEnvironment)` | Sets the environment. |
| `setUserId(_ id: String?)` | Sets the user id. |
| `initRookTransmission()` | Initializes the sdk and validates if the credentials are correct |
| `isTransmissionAvailable() -> Bool` | Returns a Bool value indicating if the sdk is available to use. |

**Note: remember to set the environment, If the environment is not set, the sdk will work in sandbox**

**Note: remember to set the user id using the below method**

```swift
TransmissionSettings.shared.setUserId(with: "USER-ID")
```

There are six main classes responsible for managing health data. Each class contains a function to enqueue, get and upload a summary. The table below lists all of the classes.

| Class name | Description |
| ---------- | ----------- |
| `SleepTransmissionManager` | This class manages the sleep data. |
| `PhysicalTransmissionManager` | This class manages the physical data. |
| `BodyTransmissionManager` | This class manages the body data. |
| `HeartRateEventTransmissionManager` | This class manages the body data. |
| `OxygenationEventTransmissionManager` | This class manages the body data. |
| `ActivityEventTransmissionManager` | This class manages the body data. |

#### SleepTransmissionManager

This class contains the methods to enqueue, get, and upload sleep summaries.

| Function | Description |
| -------- | ----------- |
| `enqueueSleepSummary(with extractionData: Data, completion: @escaping(Result<Bool, Error>) -> Void)` | Stores the summary provided by `RookAppleHealth` SDK |
| `public func enqueueSleepSummary(with summary: RookSleepDataTransmissionDTO, completion: @escaping (Result<Bool, Error>) -> Void)` | Stores a sleep summary using the object `RookSleepDataTransmissionDTO` |
| `public func getSleepSummariesStored(completion: @escaping(Result<[RookSleepSummaryTransmission],Error>) -> Void)` | Retrieves an array of `RookSleepSummaryTransmission` objects.  |
| `public func uploadSleepSummaries(completion: @escaping (Result<Bool, Error>) -> Void)` | Uploads the sleep summaries stored in local. |


#### Example

To enqueue a Sleep Summary call `enqueueSleepSummary` and provide an instance of `RookSleepDataTransmissionDTO`:

```swift
func storeSleepSummary() {
  let sleepData: RookSleepDataTransmissionDTO = RookSleepDataTransmissionDTO.RookSleepDataTransmissionDTOBuilder()
      .addDateTime(date: Date())
      .addSleepDate(sleepStartDatetime: Date(),
                    sleepEndDatetime: Date(),
                    sleepDate: Date())
      .addSleepTime(sleepDurationSeconds: 100,
                    timeInBedSeconds: 100,
                    lightSleepDurationSeconds: 100,
                    remSleepDurationSeconds: 100,
                    deepSleepDurationSeconds: 100,
                    timeToFallAsleepSeconds: 100,
                    timeAwakeDuringSleepSeconds: 100)
      .addHearRateData(hrMaxBPM: 168,
                       hrMinimumBPM: 58,
                       hrAvgBPM: 63,
                       hrRestingBPM: 59,
                       hrBasalBPM: 58)
      .buildSleepDataTransmission()

  sleepTransmissionManager.enqueueSleepSummary(with: sleepData) { result in  
    switch result {
    case .success(let success):
      debugPrint(success)
    case .failure(let failure):
      debugPrint(failure)
    }
  }
}
```

Remember all the dates and date times will be send in ISO-8601 format and UTC timezone.


### Syncing Sleep data

To sync (send) all enqueued health data call `uploadSleepSummaries`, this will sync all sleep data and delete them from the internal Database.

```swift
private let sleepTransmissionManager: RookSleepTransmissionManager = RookSleepTransmissionManager()

func uploadSleepData() {
  sleepTransmissionManager.uploadSleepSummaries() { [weak self] result in
    switch result {
    case .success(let bool):
      debugPrint(bool)
    case .failure(let error):
      debugPrint(error)
    }
  }
}
```

#### PhysicalTransmissionManager

This class contains the methods to enqueue, get, and upload physical summaries.

| Function | Description |
| -------- | ----------- |
| `enqueuePhysicalSummary(with extractionData: Data, completion: @escaping(Result<Bool, Error>) -> Void)` | Stores the summary provided by `RookAppleHealth` SDK |
| `public func enqueuePhysicalSummary(with physicalDTO: RookPhysicalDataTransmissionDTO, completion: @escaping (Result<Bool, Error>) -> Void)` | Stores a sleep summary using the object `RookSleepDataTransmissionDTO` |
| `public func getPhysicalSummariesStored(completion: @escaping (Result<[RookPhysicalSummaryTransmission], Error>) -> Void)` | Retrieves an array of `RookSleepSummaryTransmission` objects.  |
| `public func uploadPhysicalSummaries(completion: @escaping (Result<Bool, Error>) -> Void)` | Uploads the sleep summaries stored in local. |


#### Example

To enqueue a physical summary call `enqueuePhysicalSummary` and provide an instance of `RookPhysicalDataTransmissionDTO`:

```swift
func storePhysicalSummary() {
  let physicalData: RookPhysicalDataTransmissionDTO = RookPhysicalDataTransmissionDTO.RookPhysicalDataTransmissionDTOBuilder()
      .addDateTime(date: Date())
      .addCaloriesData(caloriesNetIntakeKilocalories: 0,
                       caloriesExpenditureKilocalories: 0,
                       caloriesNetActiveKilocalories: 670,
                       caloriesBasalMetabolicRateKilocalories: 1900)
      .addDistanceData(physicalHealthScore: nil,
                       stepsPerDayNumber: 1790,
                       stepsGranularDataStepsPerHr: [],
                       activeStepsPerDayNumber: 500,
                       activeStepsGranularDataStepsPerHr: [],
                       walkedDistanceMeters: 2000,
                       traveledDistanceMeters: 4000,
                       traveledDistanceGranularDataMeters: [],
                       floorsClimbedNumber: 12,
                       floorsClimbedGranularDataFloors: [])
      .buildPhysicalDataTransmission()
    
    physicalTransmissionManager.enqueuePhysicalSummary(with: physicalData) { result in
      switch result {
      case .success(let success):
        debugPrint(success)
      case .failure(let failure):
        debugPrint(failure)
      }
    }
}
```

Remember all the dates and datetimes will be send in ISO-8601 format and UTC timezone.


### Syncing Physical data

To sync (send) all enqueued health data call `uploadPhysicalSummaries`, this will sync all physical data and delete them from the internal Database.

```swift
private let physicalTransmissionManager: RookPhysicalTransmissionManager = RookPhysicalTransmissionManager()

func uploadSleepData() {
  physicalTransmissionManager.uploadPhysicalSummaries() { [weak self] result in
    switch result {
    case .success(let bool):
      debugPrint(bool)
    case .failure(let error):
      debugPrint(error)
    }
  }
}
```

#### BodyTransmissionManager

This class contains the methods to enqueue, get, and upload body summaries.

| Function | Description |
| -------- | ----------- |
| `enqueueBodySummary(with extractionData: Data, completion: @escaping(Result<Bool, Error>) -> Void)` | Stores the summary provided by `RookAppleHealth` SDK |
| `public func enqueueBodySummary(with bodyDTO: RookBodyDataTransmissionDTO, completion: @escaping (Result<Bool, Error>) -> Void)` | Stores a body summary using the object `RookBodyDataTransmissionDTO` |
| `public func getBodySummariesStored(completion: @escaping(Result<[RookBodySummaryTransmission],Error>) -> Void)` | Retrieves an array of `RookBodySummaryTransmission` objects.  |
| `public func uploadBodySummaries(completion: @escaping (Result<Bool, Error>) -> Void)` | Uploads the body summaries stored in local. |


#### Example

To enqueue a physical summary call `enqueueBodySummary` and provide an instance of `RookBodyDataTransmissionDTO`:

```swift
func storeBodySummary() {
  let bodySummaryDTO: RookBodyDataTransmissionDTO = RookBodyDataTransmissionDTO.RookBodyDataTransmissionDTOBuilder()
      .addDate(date: Date())
      .addMesurements(waistCircumferenceCMNumber: 10,
                      hipCircumferenceCMNumber: 10,
                      chestCircumferenceCMNumber: 20)
      .addBodyComposition(boneCompositionPercentageNumber: 98,
                          muscleCompositionPercentageNumber: 99,
                          weightKgNumber: 78,
                          heightCMNumber: 173,
                          bmiNumber: 20)
      .buildBodyDataTransmission()
    
    bodyTransmissionManager.enqueueBodySummary(with: bodySummaryDTO) { result in
      switch result {
      case .success(let success):
        debugPrint(success)
      case .failure(let failure):
        debugPrint(failure)
      }
    }
}
```

Remember all the dates and date Times will be send in ISO-8601 format and UTC timezone.


### Syncing Body data

To sync (send) all enqueued health data call `uploadPhysicalSummaries`, this will sync all body data and delete them from the internal Database.

```swift
private let bodyTransmissionManager: RookBodyTransmissionManager = RookBodyTransmissionManager()

func uploadBodyData() {
  bodyTransmissionManager.uploadBodySummaries() { [weak self] result in
    switch result {
    case .success(let bool):
      debugPrint(bool)
    case .failure(let error):
      debugPrint(error)
    }
  }
}
```

#### HeartRateEventTransmissionManager

The Access point to transmit the heart rate events to the rook server

Use `HeartRateEventTransmissionManager` to store, retrieve and upload heart rate events.

| Function | Description |
| -------- | ----------- |
| `public func enqueueHrEvent(_ eventData: Data, completion: @escaping (Result<Bool, Error>) -> Void)` | Stores new heart rate events that comes from RookAppleHealth SDK |
| `public func enqueueHrEvents(_ events: [RookHeartRateEventTransmission], completion: @escaping (Result<Bool, Error>) -> Void)` | Stores new physical data using an array of `RookHeartRateEventTransmission` objects |
| `public func getHrEventsStored(completion: @escaping (Result<[RookHeartRateEventTransmission], Error>) -> Void)` | Returns an array of `RookHeartRateEventTransmission` objects stored locally when the user was in an activity session |
| `public func getBodyHrEventsStored(completion: @escaping (Result<[RookHeartRateEventTransmission], Error>) -> Void)` | Returns an array of `RookHeartRateEventTransmission` objects stored locally when the user was not in an activity session. |
| `public func uploadHrEvents(completion: @escaping (Result<Bool, Error>) -> Void)` | Uploads all the heart rate events stored locally and deletes all that were uploaded successfully |

#### OxygenationEventTransmissionManager

The Access point to transmit the oxygenation events to the rook server

Use `OxygenationEventTransmissionManager`  to store new, retrieve and upload oxygenation events.

| Function | Description |
| -------- | ----------- |
| `public func enqueueOxygenationEvent(_ eventData: Data, completion: @escaping (Result<Bool, Error>) -> Void)` | Stores new oxygenation events that comes from RookAppleHealth SDK |
| `public func enqueueOxygenationEvents(_ events: [RookOxygenationEventTransmission], completion: @escaping (Result<Bool, Error>) -> Void)` | Stores new physical data using an array of `RookOxygenationEventTransmission` objects. |
| `public func getBodyOxygenationEvents(completion: @escaping (Result<[RookOxygenationEventTransmission], Error>) -> Void)` | Returns an array of `RookOxygenationEventTransmission` objects stored locally when the user was not in an activity session. |
| `public func getOxygenationEvents(completion: @escaping (Result<[RookOxygenationEventTransmission], Error>) -> Void)` | Returns an array of `RookOxygenationEventTransmission` objects stored locally when the user was in an activity session |
| `public func uploadEvent(completion: @escaping (Result<Bool, Error>) -> Void)` | Uploads all the oxygenation events stored locally and deletes all that  were uploaded successfully. |

#### ActivityEventTransmissionManager

The Access point to transmit the activity events to the rook server

Use `ActivityEventTransmissionManager` to store new, retrieve and upload activity events.

| Function | Description |
| -------- | ----------- |
| `public func enqueueActivityEvent(_ eventData: Data, completion: @escaping (Result<Bool,Error>) -> Void)` | Stores new activity events that comes from RookAppleHealth SDK. |
| `public func enqueueActivityEvents(_ events: [RookActivityEventTransmission], completion: @escaping (Result<Bool, Error>) -> Void)` | Stores new physical data using an array of `RookActivityEventTransmission` objects. |
| `public func getActivityEvents(completion: @escaping (Result<[RookActivityEventTransmission], Error>) -> Void)` | Returns an array of `RookActivityEventTransmission` objects stored locally. |
| `public func uploadEvents(completion: @escaping (Result<Bool, Error>) -> Void)` | Uploads all the activity events stored locally and deletes all that were uploaded successfully. |

#### TimeZoneManager

The Access point to transmit current time zone

Use `TimeZoneManager` to upload the current user's time zone

| Function | Description |
| -------- | ----------- |
| `uploadUserTimeZone(timezone: String, offset: Int, completion: @escaping (Result<Bool, Error>) -> Void)` | Uploads the user's time zone, a user have to be added before use this method. |
