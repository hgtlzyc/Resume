Contact Me: Lijia@vt.edu

# Recent Personal Project
Live Activity tracking GPS infos and using Swift Charts represent live data collected

<img width="254" alt="image" src="https://github.com/hgtlzyc/Resume/assets/37933183/048fb5bc-91cc-4e25-8c9f-c7cb7adc4297">


https://github.com/hgtlzyc/Resume/assets/37933183/db861868-7ad5-4896-bcd6-d081b357ec66




# for more recent projects please see my cover letter
# content below last updated: Sep 3, 2021

# Skills:
UIKit | CoreData | SwiftUI | Combine  
CloudKit | Firebase | Notifications | JSON | API | Admob  
ARKit | SceneKit | MapKit 
<br />
MVC Architecture Pattern | MVVM | Dispatch (GCD)
<br />
Storyboards | AutoLayout | Localization 
<br />
Blender | Sketch | AfterEffects
<br />
Git | Github


# App:

### CarLocator using Augmented Reality [AppStore Link](https://apps.apple.com/us/app/id1495605423)
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

# MultiColorMapOverlay [Github Repo](https://github.com/hgtlzyc/MultiColorMapOverlay)
*lines will be smoother with actual data points collected from device
<br />
<br/>
![](https://github.com/hgtlzyc/MultiColorMapOverlay/blob/2c78af0cb50f697965c4a64067e74e5229f8a89d/ScreenRecording.gif)

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


# Live Search From API Using Combine [Github Repo](https://github.com/hgtlzyc/MovieSearchAPI)
![](https://github.com/hgtlzyc/MovieSearchAPI/blob/1df048ba7fe97726bf9abc648904cf077a0e4d2b/LiveMovieSearchScreenRecording.gif)
 - API
 - Combine
 - URL Session
 - Cache
 <br />
 used Combine to do Live search, with .debounce will not putting much stress to the API, 
 <br />
 .removeDuplicates will prevent calling the same values 
 <br />
 Also able to handle timed out requests with .share() and .debounce

#### Code snippet:
```swift
    //(Sample Console Log)
    //h 2021-08-07 07:28:19 +0000
    //receive subscription: (RemoveDuplicates)
    //request unlimited                //new subscription created
    //ha 2021-08-07 07:28:19 +0000
    //har 2021-08-07 07:28:19 +0000
    //harr 2021-08-07 07:28:19 +0000
    //harry 2021-08-07 07:28:19 +0000  //debounce
    //receive value: (harry)
    //https://api.themoviedb.org/3/search/movie?api_key=****11d1ad54efd1c1ab382103435cee&query=harry
    //harr 2021-08-07 07:28:45 +0000
    //harry 2021-08-07 07:28:45 +0000  //remove duplicates
    //harr 2021-08-07 07:28:53 +0000
    //har 2021-08-07 07:28:53 +0000
    //ha 2021-08-07 07:28:53 +0000
    //h 2021-08-07 07:28:53 +0000
    //receive cancel                   //subscription canceled

    func setupCombine(){
        guard subscriptions.isEmpty else { return }
        
        //debounce the search based on the 'debounceInSeconds' and reduce the API calls
        //also remove duplicate calls
        let filteredPublisher = searchKeyPublisher
            .debounce(for: .seconds(debounceInSeconds), scheduler: DispatchQueue.global())
            .removeDuplicates()
            //.print()
            .share()
        
        filteredPublisher
            .sink { [weak self] searchKey in
                self?.fetchMoviesWith(searchKey)
            }
            .store(in: &subscriptions)
        
        //set internet request time out and display message
        filteredPublisher
            .debounce(for: .seconds(requestTimeOutInSeconds), scheduler: DispatchQueue.global())
            .sink { [weak self] _ in
                DispatchQueue.main.async {
                    guard let isRunning = self?.activityIndicator.isAnimating,
                          isRunning else { return }
                    self?.activityIndicator.stopAnimating()
                    self?.changeDisplayModeTo(.requestTimeOut)
                }
            }
            .store(in: &subscriptions)
        
    }//End Of Setup Combine
    
```

# Quiz App based on data from NHTSA API [Github Repo](https://github.com/hgtlzyc/AllVehicleModelsAPI)
![](https://github.com/hgtlzyc/AllVehicleModelsAPI/blob/7ff7611ec3cbc466874f06001e136fab7018f015/screenCapture.gif)
<br/>
- MVVM
- NHTSA (National Highway Traffic Safety Administration) API [Link](https://vpic.nhtsa.dot.gov/api/)
- URLSession
- Animation

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
    //Strings
    var statusString: String {
        let correntlyAnsweredCount = targetAnswers.intersection(userAnswered).count
        let totalTriesCount = userAnswered.count
        return "\(targetAnswers.count) models, \(correntlyAnsweredCount) correct, tried \(totalTriesCount) times"
    }
    
    var sortedCurrentlyCorrectAnswers: [(String,Bool)] {
        return baseSetSortAndMap( targetAnswers.intersection(userAnswered) , status: true)
    }
    
    var sortedAllCorrectAndWrongAnswers: [(String,Bool)] {
        let correntlyAnsweredSet = targetAnswers.intersection(userAnswered)
        let currentlyWrongSet = targetAnswers.subtracting(userAnswered)
        
        return baseSetSortAndMap(correntlyAnsweredSet, status: true) + baseSetSortAndMap(currentlyWrongSet, status: false)
    }
    
    //Ints
    var worngAnswersCount: Int {
        return targetAnswers.subtracting(userAnswered).count
    }
    
    // MARK: - write
    func resetForNextQuestion(){
        userAnswered = Set<String>()
        targetAnswers = Set<String>()
        makeName = nil
    }
    
    ///put the string in the user answered set, returns bool indicates if the user answer is correct
    func newUserAnswered(_ string: String) -> Bool {
        userAnswered.insert(string)
        return targetAnswers.contains(string)
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

//In The ViewController Extension
//MARK: -AnmationHelper
func animateStatusLabelBasedOn(_ isCorrect: Bool,
                               colorForCorrect: UIColor = #colorLiteral(red: 0.3411764801, green: 0.6235294342, blue: 0.1686274558, alpha: 1),
                               colorForWrong:UIColor = #colorLiteral(red: 1, green: 0.2897925377, blue: 0.2962183654, alpha: 0.6548947704),
                               duration: Double = 0.5) {
    let baseColor = statusLabel.layer.backgroundColor
    var tempColor: UIColor
    var affineTransform: CGAffineTransform?

    switch isCorrect {
    case true:
        tempColor = colorForCorrect
        affineTransform = CGAffineTransform(scaleX: 1.05, y: 1.05)

    case false:
        tempColor = colorForWrong
        affineTransform = nil
        ///extension in CALayer, keyframeAnimation using keypath
        statusLabel.layer.shake(withDuration: duration)
    }

    UIView.animate(withDuration: duration) {
        UIView.modifyAnimations(withRepeatCount: 1, autoreverses: true) {
            self.statusLabel.layer.backgroundColor = tempColor.cgColor
            if let trans = affineTransform {
                self.statusLabel.transform = trans
            }
        }

    } completion: { [weak self] _ in
        self?.statusLabel.layer.backgroundColor = baseColor
        self?.statusLabel.transform = CGAffineTransform.identity
        self?.statusLabel.layer.removeAllAnimations()
    }

}//End Of animateStatusLabel
    
```

# PairRandomizer [Github Repo](https://github.com/hgtlzyc/PairRandomizer)
- Generate random groups based on the target group size
- Change target group size
- Rearrange the list
- Delete/Add Names
- Persistance 

![](https://github.com/hgtlzyc/PairRandomizer/blob/d60b063ce473a05d5e284c725942711acde81692/ScreenCapture.gif)


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

## Action Sheet [Github Link](https://github.com/hgtlzyc/ActionSheet)
 - Programmatic UI
 - UIKit
 - Adaptive to different devices and contents


![](https://github.com/hgtlzyc/ActionSheet/blob/ce456d5884da829e59a6066b197e8b41f9c9f72e/screenCapture.gif)

Code Snippet:
<br />
```swift 

 //Action View Related Properties
 private var actionSheetView: ActionSheetView?
 private var allowsMultiSelect: Bool = false
 private var infoProvider: [ActionSheetDisplayable] = []
 private var userSelected: [ActionSheetDisplayable] = []

 // MARK: - ActionSheetView model
 struct ActionSheetInfo: ActionSheetDisplayable{
     let imageURL: String
     let title: String
     var isSelected: Bool

 }

 // MARK: - Action Sheet View Sample Usage
 let actionSheetVM = ActionSheetVM(
     title: titleString,
     dataToDisplay: infoProvider,
     allowsMultiSelect: allowsMultiSelect
 )

 actionSheetView = ActionSheetView(viewModel: actionSheetVM,
                                   delegate: self,
                                   inFrame: view.frame,
                                   showDebugPrint: true)

 guard let actionSheetView = actionSheetView else {
     print("unexpected case in \(#function)")
     return
 }

 view.addSubview(actionSheetView)
 
  
// MARK: - Action Sheet View Delegate
extension BaseViewController: ActionSheetViewDelegate {
    
    func ActionSheetViewActionUpdated(_ actionSheetViewMode: ActionSheetVM) {
        let actionSheetInfos = actionSheetViewMode.infoArray
        infoProvider = actionSheetViewMode.infoArray
        
        let selectedInfos = actionSheetInfos.filter{ $0.isSelected }
        
        logTextView.text = nil
        userSelected = []
        
        selectedInfos.forEach{ info in
            logTextView.text = logTextView.text.appending("\n \(info.title) ")
            userSelected.append(info)
        }
        
        if selectedInfos.count > 0 && actionSheetViewMode.allowsMultiSelect == false {
            dismissActionSheet()
        }
        
    }///End Of ActionSheetViewActionUpdated
    
    func ActionSheetViewRequestedDismiss() {
        dismissActionSheet()
    }

    func dismissActionSheet(){
        guard let actionSheet = actionSheetView else {
            print("unexpected case in \(#function)")
            return
        }
        
        logTextView.text = logTextView.text.appending("\n User selected \(userSelected.count) company")
        
        UIView.animate(withDuration: 0.2) {
            actionSheet.alpha = 0.0
        } completion: { _ in
            actionSheet.removeFromSuperview()
            self.actionSheetView = nil
        }
        
    }

}///End Of ActionSheetViewDelegate
        
```


## Number Printer [Github Repo](https://github.com/hgtlzyc/NumberPrinter)
 - Code Challenges

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
 - Count Consecutive Elements in Array [Link](https://github.com/hgtlzyc/Playgrounds/blob/main/README.md#count-consecutive-elements)
 - Ordered Dictionary with Copy On Write [Link](https://github.com/hgtlzyc/Playgrounds/blob/main/README.md#ordered-dictionary-with-copy-on-write)
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




