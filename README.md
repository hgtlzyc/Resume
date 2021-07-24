 
## AppStore:

### CarLocator using Augmented Reality (2020) [AppStore Link](https://apps.apple.com/us/app/sure-car-locator-with-ar/id1495605423) | [Video Demo](https://youtu.be/TvZPVcpJHlg)
![](https://github.com/hgtlzyc/Resume/blob/887760c686ae67f6f6fbe565043187616c99eeb1/ScreenCaptures/CarLocatorGIF.gif)
 - ARKit
 - MapKit
 - CoreLocation
 - Admob
 - [Lottie](https://github.com/airbnb/lottie-ios) 
 - Data Persistence
 - Blender
 - Sketch
 - After Affects

<br />


## Pokemon Display [Github Repo](https://github.com/hgtlzyc/PokemonDisplay)

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
        DispatchQueue.main.async {
            let result = self.reduce(state: self.appState, action: action)
            self.appState = result.newState
            guard let command = result.newCommand else { return }
            command.execute(in: self)
        }
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

## Study Notes [Github Repo](https://github.com/hgtlzyc/StudyNotes)
#### Screen Capture
![](https://github.com/hgtlzyc/Resume/blob/6fb36f3ee8df04af76692bf668b02d5f770f6ad9/ScreenCaptures/StudyNotes.png)




