# Skills:
UIKit | CoreData | SwiftUI | Combine  
Firebase | Notifications | JSON | API | Admob  
ARKit | SceneKit | MapKit 
<br />
MVC Architecture Pattern | MVVM | Dispatch (GCD)
<br />
Storyboards | AutoLayout | Localization 
<br />
Blender | Sketch | AfterEffects
<br />
Git | Github


# AppStore:

### CarLocator using Augmented Reality [AppStore Link](https://apps.apple.com/us/app/sure-car-locator-with-ar/id1495605423) | [Video Demo](https://youtu.be/TvZPVcpJHlg)
*screen capture will be smoother after fully loaded <br />
![](https://github.com/hgtlzyc/Resume/blob/887760c686ae67f6f6fbe565043187616c99eeb1/ScreenCaptures/CarLocatorGIF.gif)
<br />*screen capture will be smoother after fully loaded <br />
 - ARKit
 - MapKit
 - CoreLocation
 - Admob
 - [Lottie](https://github.com/airbnb/lottie-ios) 
 - Data Persistence
 - Blender (3D Modeling)
 - Sketch
 - After Effects
 - Localization

<br />

# Pokemon Display [Github Repo](https://github.com/hgtlzyc/PokemonDisplay)

![](https://github.com/hgtlzyc/PokemonDisplay/blob/225c53fc4e3f02d16c9ea43c0d93ae59aa1241a5/screenRecording.gif)

 - SwiftUI
 - Combine
 - Property Wrapper
 - Data Persistence
 - JSON Decode/Encode
 - API Calls [PokeAPI](https://pokeapi.co/)

#### Code snippet:
```swift
import Foundation
import Combine

enum StateCenterSubType: Hashable, CaseIterable{
    case loadingPokemonDM
}

class StateCenter: ObservableObject {
    @Published var appState = AppStates()
    
    var subscriptions = [StateCenterSubType : Set<AnyCancellable>]()
    
    init() {
        cancelAndResetSubscritions(types: StateCenterSubType.allCases)
    }
            
    func executeAction(_ action: AppAction) {
          let result = self.reduce(state: self.appState, action: action)
          self.appState = result.newState
          guard let command = result.newCommand else { return }
          command.execute(in: self)
    }
    
    private func reduce(state: AppStates, action: AppAction) -> (newState: AppStates, newCommand: AppCommand?) {
        var appState = state
        var appCommand: AppCommand? = nil
        
        switch action {
            
        //**Cases NOT showing in this snippet for demonstration purpose
            
        }
        
        return (newState: appState, newCommand: appCommand)
    }
        
    private func cancelAndResetSubscritions(types: [StateCenterSubType]) {
        types.forEach { subType in
            subscriptions[subType]?.forEach{$0.cancel()}
            subscriptions[subType] =  Set<AnyCancellable>()
        }
    }
}

```

 used flatMap to limit the calls to API, maxTasks set to 1 and delay set to 0.1s, 

```swift
     .flatMap(maxPublishers: .max(maxTasks)) { urlString -> AnyPublisher<PokemonDataModel, AppError> in
         do {
             let publisher = try generateSinglePokemonDMPublisher(urlString)
             return publisher
                 .delay(for: .seconds(delayInSeconds), scheduler: DispatchQueue(label: urlString))
                 .eraseToAnyPublisher()

         } catch let err {
             return Fail<PokemonDataModel, AppError>(error: AppError.networkError(err)).eraseToAnyPublisher()
         }
     }
```

<br />

# Quiz App based on data from NHTSA API [Github Repo](https://github.com/hgtlzyc/AllVehicleModelsAPI)
![](https://github.com/hgtlzyc/AllVehicleModelsAPI/blob/07397146fabe2191e5b10ad7192601314249c439/screenRecording.gif)
<br/>
- UIKit 
- NHTSA (National Highway Traffic Safety Administration) API [Link](https://vpic.nhtsa.dot.gov/api/)
- URLSession
- MVVM

#### Code snippet:

```swift

  ////there are 9746 makes available from the NHTSA, 
  ///only generate random ids based on the well known brands, some brands are just too hard, 
  ///Using idPool to avoid repeated elements unitl whole target set finished
  
  func generateRandomIDInRange(idPool: inout Set<Int>) -> Int {
      let targetSet: Set<Int> = [440,441,442,444,448,449,452,460,469,474,475,480,483,485,515,523,582,584]

      if idPool.isEmpty {idPool = targetSet}

      guard let id = idPool.randomElement(), let _ = idPool.remove(id) else {
          print("Unexpected case in \(#function), line \(#line)")
          return 440
      }

      return id
  }
    
  
class VehicleModelsViewModel {
    private var userAnswered = Set<String>()
    private var targetAnswers = Set<String>()
    
    var makeName: String?
    
    // MARK: - read
    var statusString: String {
        let correntlyAnsweredCount = targetAnswers.intersection(userAnswered).count
        let wrongAnswetsCount = userAnswered.subtracting(targetAnswers).count
        return "\(targetAnswers.count) models, you got \(correntlyAnsweredCount) correct, \(wrongAnswetsCount) wrong"
    }
    
    var sortedCurrentlyCorrectAnswers: [(String,Bool)] {
        return baseSetSortAndMap( targetAnswers.intersection(userAnswered) , status: true)
    }
    
    var sortedAllCorrectAndWrongAnswers: [(String,Bool)] {
        let correntlyAnsweredSet = targetAnswers.intersection(userAnswered)
        let currentlyWrongSet = targetAnswers.subtracting(userAnswered)
        
        return baseSetSortAndMap(correntlyAnsweredSet, status: true) + baseSetSortAndMap(currentlyWrongSet, status: false)
    }
    
    // MARK: - write
    func resetForNextQuestion(){
        userAnswered = Set<String>()
        targetAnswers = Set<String>()
        makeName = nil
    }
    
    func newUserAnswered(_ string: String) {
        userAnswered.insert(string)
    }
    
    func setCorrectAnswers(_ answerArr: [String]) {
        targetAnswers = Set(answerArr)
    }
    
    //Helper
    private func baseSetSortAndMap(_ baseSet: Set<String>, status: Bool) -> [(String,Bool)] {
        Array( baseSet )
            .sorted{ ($0.first ?? "a") < ($1.first ?? "a") }
            .map{($0,status)}
    }
    
}//End Of ViewModel

//in the ViewController
// MARK: - String related
func generateTargetAnswersBasedOn(_ models: [VehicleModel]) -> [String]{
    return models.map{ model in
        generateFilteredString(model.modelName)
    }
}

func generateFilteredString(_ baseString: String) -> String {
    return baseString
            .uppercased()
            .filter{
                String($0).range(of: "[A-Z0-9]", options: .regularExpression) != nil
            }
}//
    
```



# Events Reminder [Github Repo](https://github.com/hgtlzyc/EventsCoreData)
 - User Notification
 - CoreData
 - Error Handling

#### Code Snippet
```swift
//MARK: - Raw Data sorting related
extension EventListTableViewController {
    
    func sortedEventsByDate(from eventsRaw: [Event], isAssending: Bool, completion: (_ result:  Result<[Event], SortingError>) -> Void) {
        let validDatesCount = eventsRaw.compactMap{$0.eventDate}.count
        
        if eventsRaw.count != validDatesCount {
            let eventsWithNilDates = findEventsWithNilDate(eventsRaw)
            
            completion(
                .failure(
                    .nilDateInArray(
                        message: "\(eventsRaw.count - validDatesCount) nil event date",
                        eventSet: eventsWithNilDates
                    )
                )
            )//end of completion
        }//
        
        /// Date will be sorted by assending or desending,
        /// If date the same will be sorted by name
        func eventDisplayOrder(_ lh: Event, _ rh: Event, basedOn shouldAssending: Bool) -> Bool {
            guard lh.eventDate! != rh.eventDate! else {
                return (lh.eventName?.first ?? "a" ) < ( rh.eventName?.first ?? "a" )
            }
            
            let isDateAssending = lh.eventDate! < rh.eventDate!
            return shouldAssending ? isDateAssending : !isDateAssending
        }
        
        func topToBottomDisplayPredicate(_ lh: Event, _ rh: Event) -> Bool {
            return eventDisplayOrder(lh, rh, basedOn: isAssending)
        }
        
        completion(
            .success(
                eventsRaw.sorted(by: topToBottomDisplayPredicate)
            )
        )
        
    }//End Of sortedEventsByDate
    
    // MARK: - info helper
    func findEventsWithNilDate(_ rawEvents: [Event]) -> Set<Event>{
        let validEvents = Set( rawEvents.filter{$0.eventDate != nil} )
        let rawEvents = Set( rawEvents )
        
        return rawEvents.subtracting(validEvents)
    }
    
}//End Of Extension

```

<br />

## Color Master [Github Repo](https://github.com/hgtlzyc/ColorMaster)
![](https://github.com/hgtlzyc/ColorMaster/blob/ad6c900f7d95c53ab39b07c909f9aa9d4dd37352/ScreenCapture.gif)

#### Code snippet:
```swift
struct ColorController {

    static func generateTargetColor() -> Color {
        var num: CGFloat { CGFloat( Int.random(in: (60...200)) ) }
        return Color(red: num, green: num, blue: num)
    }
    
    static func generateWrongColors(within int: Int, of target: Color, total: Int) -> [Color] {
        return (1...total).map { _ -> Color in
            var off: CGFloat { CGFloat( Int.random(in: (-int ... int)) ) }
            return Color(red: target.red + off, green: target.green + off, blue: target.blue + off)
        }
    }
    
}
 //in the collectionVC
 func generateNewColorCards(){
     colorCards = [[]]
     let newTargetColor = ColorController.generateTargetColor()
     targetColor = newTargetColor
     let wrongColors = ColorController.generateWrongColors(within: levelNumber, of: newTargetColor, total: totalWrongOnes)
     let options = wrongColors + [newTargetColor]

     colorCards.append([(newTargetColor,"Target")])
     colorCards.append(options.shuffled().map{ ($0, "?") })
     collectionView.reloadData()
 }
    
```


<br />


## Number Printer [Github Repo](https://github.com/hgtlzyc/NumberPrinter)

![](https://github.com/hgtlzyc/NumberPrinter/blob/e3c97c30f9e5e29276a877744c8291d1048454aa/NumberPrinterDemo.gif)

take an Int with multiple digits and print each number in the same fashion on a single Line.

For example, if pass 257 the console might look something like this:
 
```swift
 ---          ---         ---

      |     |                 |

 ---         ---              |

|                |            |

 ---         ---              |
```

#### Code snippet:
```swift
import Foundation

struct NumberPrinter {
    
    enum NumComponents: String {
        case dashF = " ------ "
        case bothF = " |      |"
        case leftF = " |       "
       case rightF = "        |"
        case empty = "         "
       case center = "    |    "
    }

    static func generateNumString(_ int: Int) -> String {
        //convert to 2d string array like [ [components for num1], [components for num2] ...]
        let str2DArr = "\(int)".reduce( into: [ [String] ]() ) {
            $0.append( strArrayFor($1).map{$0.rawValue} )
        }
        
        return (0...4).reduce(into: [String]() ) { sum, next in
            //tempArray will be like [all the first elements] or [all the 2nd elements] ...
            var tempArr = [String]()
            str2DArr.forEach { strArray in
                tempArr.append(strArray[next])
            }
            let tempStr = tempArr.reduce(into: "") { $0 = $0 + "    " + $1 }
            sum.append(tempStr)
        }.reduce(into: "") { $0 = $0 + "\n" + $1 }
        
    }

    static func strArrayFor(_ chr: Character ) -> [NumComponents] {
        switch String(chr) {
        case "0":
            return [ .dashF, .bothF, .bothF, .bothF, .dashF ]
            
        //**Some cases NOT showing in this snippet for demonstration purpose
        
        }
    }
}
 
```

<br />

# More Codes I wrote [Github Repo](https://github.com/hgtlzyc/Playgrounds/tree/main)
 - Using Combine for nested API calls [Link](https://github.com/hgtlzyc/Playgrounds/blob/54f54837c85335f53c8c37390c43c812c9939f29/API.playground/Pages/UseCombine.xcplaygroundpage/Contents.swift)
 - Convert to Result Type for error handling [Link](https://github.com/hgtlzyc/Playgrounds/blob/54f54837c85335f53c8c37390c43c812c9939f29/API.playground/Pages/UseResultForErrorHandling.xcplaygroundpage/Contents.swift)
 - Find unique min/max value in array (generic) [Link](https://github.com/hgtlzyc/Playgrounds/blob/main/README.md#find-unique-minmax-value-in-array-generic)
 - Other Code Challanges

# Study Notes [Github Repo](https://github.com/hgtlzyc/StudyNotes)
[Swift](https://github.com/hgtlzyc/StudyNotes/tree/main/Swift)
<br />
[JavaScript](https://github.com/hgtlzyc/StudyNotes/tree/main/JS)
<br />
[Java](https://github.com/hgtlzyc/StudyNotes/tree/main/Java)
<br />
[Python](https://github.com/hgtlzyc/StudyNotes/tree/main/Python)
<br />
[React](https://github.com/hgtlzyc/StudyNotes/tree/main/React)
<br />
[Ruby](https://github.com/hgtlzyc/StudyNotes/tree/main/Ruby)
<br />
[SQL](https://github.com/hgtlzyc/StudyNotes/tree/main/SQL)
<br />
[Web](https://github.com/hgtlzyc/StudyNotes/tree/main/Web)

# Books Read
[Combine: Asynchronous Programming with Swift](https://www.raywenderlich.com/books/combine-asynchronous-programming-with-swift)
<br />
[Advanced Swift](https://www.objc.io/books/advanced-swift/)
<br />
[SwiftUI 与 Combine 编程](https://objccn.io/products/swift-ui)
<br />
[Swift in Depth](https://www.manning.com/books/swift-in-depth?gclid=EAIaIQobChMI3f_xzN2A8gIVFozICh2j0gGZEAAYAiAAEgIB8_D_BwE)
<br />
[Mastering Swift 5](https://www.packtpub.com/free-ebook/mastering-swift-5-fifth-edition/9781789139860)
<br />
[Expert Swift](https://www.raywenderlich.com/books/expert-swift)
<br />
[Swifter - Swift 开发者必备 Tips](https://objccn.io/products/swifter-tips)
<br />
[SwiftUI Apprentice](https://www.raywenderlich.com/books/swiftui-apprentice)
<br />
[SwiftUI by Tutorials](https://www.raywenderlich.com/books/swiftui-by-tutorials)
<br />
[Apple Augmented Reality by Tutorials](https://www.raywenderlich.com/books/apple-augmented-reality-by-tutorials)
<br />
<br />


# Python Projects
Inventory System [Vide Demo](https://youtu.be/3U18vwzImSo)
 - Tinker
 - SQL
 - Raspberry Pi

<br />

Info Statin [Video Demo](https://youtu.be/2j16hJu1nSY)
 - AutoCAD
 - 3D Printing
 - Linux
 - GPIO ports




