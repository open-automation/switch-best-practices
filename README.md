# switch-style-guidelines
Switch best practices that to improve readability and assist in troubleshooting.

## Flows

### Direction
In general, it is preferred for flows to route jobs from left to right. Diversion in logic should route up or down. Adhering to this guideline too closely may cause wasted vertical screen space. A good solution for adhering to this rule is to use Portals to re-route jobs from the left within a flow.

<img src="http://i.imgur.com/uJtqsnj.png">

### Problem Jobs
Every flow should have its own “Problem jobs” folder located at the left-most bottom corner of the flow. It should be vertically-aligned with the left-most element in the flow.

<img src="https://i.imgur.com/ldKkmwy.png">

### Dividing Flow Logic
Complex flows can be divided into many Flows, grouped in Flow Groups and linked together. These flows can be linked via configured non-automanaged folders or something like Switch Portals. If this strategy is used, sub-flows should be clearly named and logically separated, with common logic reused as much as possible. Flow groups can be exported, imported, started and stopped with a single click.

<img src="https://i.imgur.com/gJbiZyF.png">

## Flow Elements

### Naming
Flow elements should be unique within the flow. A good strategy for doing this is to make them stateful and more descriptive. If you have two "Rename" elements, elaborating on what they do differently will make the flow much more readible and help when debugging messages in the log.

<img src="https://i.imgur.com/9RwnCxj.png">

#### Folder Naming
Folders should not keep their default name (e.f. "Folder 37").

### Non-automanaged Folders
Non-automanaged folders must not be the color yellow. Hot-folders to other devices or applications should be orange. Non-automanaged folders used to send jobs between flows should be blue.

<img src="https://i.imgur.com/xVrBJfG.png"> <img src="https://i.imgur.com/94LE7a6.png">

## Connections

### Conditional Connectors
Conditional connectors must be colored blue and must have a descriptive "name" property set. Any gray colored connector should be assumed to include “All jobs”.

<img src="https://i.imgur.com/1gdeQA4.png">

### Traffic-light Connectors
All configurators that have traffic light connections should use the success and error output connections. Do not rely on an error going to Problem Jobs folder whenever possible.

### Stateful Connectors
Stateful connectors (such as those from a traffic light type script or Inject configurator) should be colored to indicate its state. For example, an error connector should be colored magenta, while a success connector should be colored green.

<img src="https://i.imgur.com/VVlx2G0.png">

### Stepped Logic
Avoid cases where there are many complicated connectors being routed from a single flow element. If a job fails from the routing folder because it can't be accepted into any connector, it is difficult to troubleshoot.

<img src="https://i.imgur.com/HIoaQjw.png">

Instead, opt for a stepped logic approach where you use simpler logical determinations with a stateful resulting folder before continuing to the next logical determination.

<img src="https://i.imgur.com/Nm0a0Ym.png">

## Design Patterns
## Problem Jobs Handler
When using several sub-flows in concert, some logic, such as what to do with a Problem Job, should not be replicated within every flow. This can be handled by adding a flow, specifically tasked with handling problem jobs. The key is to write the FailMessage, FailElement, and FailFlow to private data before routing the job into the problem jobs handler.

<img src="https://i.imgur.com/EaucFti.png">

## Anti-patterns
### Routing Business Logic from Problem Jobs
Relying on routing jobs out of Problem Jobs to return into business logic should be avoided wherever possible. This can be handled better by relying on flow element's outbound connectors to handle failures. Problem Jobs should be reserved for unhandled exceptions that actually need developer attention, as much as possible.

<img src="https://i.imgur.com/rea0uY2.png">
