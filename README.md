# AGENTIC-CODING-FRAMEWORK
Files that can be cloned and dropped into any project hat uses Agentic Coding (such as codex etc) to generate code.   These starter files will provide your Agent with the capability of abstract reasoning and multi-file complex code analysis and removes repetitive and/or breaking code errors from agentic work flows

## Testing environment
- A window with a component that can be used to instantiate an animated red square which will automatically move to a random place when a instance is created (similar to a class in other languages)
  
### Main.qml 
```
import QtQuick
Window {
    width: 800
    height: 800
    title: "hello world"

   Component {
      id: squareComponent
      Rectangle {
        id: squareRoot
        function moveTo(x, y) {
           squareRoot.x = x
           squareRoot.y = y;
        }
        Behavior on x { NumberAnimation { duration: 300 } }
        Behavior on y { NumberAnimation { duration: 300 } }
        Component.onCompleted: {
              var maxX = 800
              var maxY = 800
              var rx = Math.random() * maxX
              var ry = Math.random() * maxY
              moveTo(rx, ry)
        }
     }
   }

  Item {
      id: root
      anchors.fill: parent
  }

}
```



## What does this fix?
- Agentic code generators tend to not take into consideration existing code bases and opt to reinvent the wheel when given the option.


### prompt
``` Create 10 red squares that have the same dimensions and each one starts at (0,0) and  moves to a different position when created ```

  ### Output
       Rectangle {
            id: square1
            x: 0
            y: 0
            width: 50
            height: 50
            color: "red"

            NumberAnimation on x { from: 0; to: 300; duration: 300000; running: true }
            NumberAnimation on y { from: 0; to: 100; duration: 300000; running: true }
       }
         Rectangle {
            id: square2
            x: 0
            y: 0
            width: 50
            height: 50
            color: "red"

            NumberAnimation on x { from: 0; to: 300; duration: 300000; running: true }
            NumberAnimation on y { from: 0; to: 300; duration: 300000; running: true }
        }
      ...

## What this file does when added to projects
- Grants code generating agents the ability to reference and use other parts of the project quickly and effectively without additional prompting.

### Prompt
``` Create 10 red squares that have the same dimensions and each one starts at (0,0) and  moves to a different position when created ```

### output
```
 Repeater {
    model: 10
    delegate: squareComponent { parent: root } 
}
```
