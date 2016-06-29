# switch-style-guidelines
Switch best practices that improve readability and assist in troubleshooting.


- [Flows](#flows)
- [Flow Elements](#flow-elements)
- [Connections](#connections)
- [Metadata](#metadata)
- [Design Patterns](#design-patterns)
- [Anti-patterns](#anti-patterns)

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
#### Uniqueness
Flow elements should be unique within the flow. A good strategy for doing this is to make them stateful and more descriptive. If you have two "Rename" elements, elaborating on what they do differently will make the flow much more readible and help when debugging messages in the log.

#### Scentence Capitalization
Flow elements should be named with "scentence capitalization". This means, the first letter should be capatalized and following words should be all lowercase unless they are proper nouns or acronyms. For example: "VPP PitStop preflight result set" is better than "VPP PitStop Preflight Result Set".

<img src="https://i.imgur.com/9RwnCxj.png">

#### Folders
Folders should not keep their default name (e.g. "Folder 37").

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
Avoid cases where there are many complicated connectors (star) being routed from a single flow element. If a job fails from the routing folder because it can't be accepted into any connector, it is difficult to troubleshoot.

<img src="https://i.imgur.com/HIoaQjw.png">

Instead, opt for a stepped logic approach where you use simpler logical determinations with a stateful resulting folder before continuing to the next logical determination.

<img src="https://i.imgur.com/Nm0a0Ym.png">

## Metadata

### Naming
When declaring a dataset (XML Pickup, Ungroup, Submit Points, Check Points, etc...) the Dataset Name should be unique and descriptive. You must not leave the default dataset name ("Xml", "Ungroup", etc...).

<img src="https://i.imgur.com/efuLShX.png">

### Private Data Key Namespacing
When declaring Private Data (PD) keys, you should namespace the values with the program you're working on. For example, if a group of flows is being used to gather Switch statistics (SS), a PD key for number of flows should not be _Flow Count_. A better key would be _SS Flow Count_. This ensures developers can understand where the PD originated from as well as ensuring key names do not overlap when sent to functional flows.

## Design Patterns

### Private Data Abstraction

Abstracting your primary dataset values with Private Data is helpful in many ways. It makes flows easier to troubleshoot and read since the syntax is much simpler: _[Job.PrivateData:Key="MIS Job Number"]_ vs _[Metadata.Text:Path="/field-list/field[2]/value",Dataset="Submit",Model="XML"]_. Furthemore, if you ever reconfigured the origin of that metadata path (changing the order of questions on a Checkpoint, for example) all of the Metadata.Text references throughout the flow would have to be changed instead of a single time if you had mapped it to a PD key.

If your business logic requires you to alter a value, it is much easier to overwrite PD keys than it is to manipulate a dataset. For example, let's say you route preflight files into a Checkpoint by means of a PD value of a CSR's email address. In this checkpoint, the CSR checks the preflights before further processing the job. Perhaps you give your CSRs the ability to re-assign the preflight to another CSR. You can simply overwrite the CSR's email PD value with the re-assigned CSR's email and route it back into the same checkpoint.

### Problem Jobs Handler
When using several sub-flows in concert, some logic, such as what to do with a Problem Job, should not be replicated within every flow. This can be handled by adding a flow, specifically tasked with handling problem jobs. The key is to write the FailMessage, FailElement, and FailFlow to private data before routing the job into the problem jobs handler.

<img src="https://i.imgur.com/EaucFti.png">

### Portal Callbacks
By using Portals, you can simplify passing jobs between seperate flows. This can be done directly through hard-coded Portal Channels, but it is also advantagious to use Portal Callbacks which allows you to have a dynamic Channel. The benefit means, in the future, if you ever need to use a function flow (sub-flow invoked from a program) in another program flow, you do not have to modify the function flow to route to a new location.

Do this by writing a Portal Channel value to Private Data. This will be the channel where your function flow will route the job (callback) once it's done doing the work.

<img src="https://i.imgur.com/v8uTUsz.png">

Then, within your function flow, Assert that your Portal Callback Channel (Private Data key) is set. Use that Private Data key within the outbound portal channel.

<img src="https://i.imgur.com/dckKLqK.png">

## Anti-patterns
### Routing Business Logic from Problem Jobs
Relying on routing jobs out of Problem Jobs to return into business logic should be avoided wherever possible. This can be handled better by relying on flow element's outbound connectors to handle failures. Problem Jobs should be reserved for unhandled exceptions that actually need developer attention, as much as possible.

<img src="https://i.imgur.com/rea0uY2.png">
