[//]: <> ( # header)
[//]: <> (dubble space line break)
[//]: <> ([cheatsheet](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet))

**Questions**
- WorkParty: Required, vs Visible on Entry


---
**Explore data model:** App -> _run a search on its name_ -> Data model -> Property

---
## <a name="data-transform"/> /stub Data Transform
Data Transform is an option for copying and manipulating data.

**Creation:** App -> _right click a case_ -> +Create -> Data Transform  
_or_ click crosshair next to a Data transform input (eg.: at Work Parties)

**Usage:**
- Call from a flow action rule
- call from a connector
- [pyDefault](#data-transform-pyDefault)

### <a name="data-transform-pyDefault"/> pyDefault
pyDefault is called whenever a new case is created, and allows you to set properties on creation.



## <a name="activity"/> Activity

An Activity is an automated procedure, structured as a series of steps that execute in sequence.

**Creation:** +Create -> Technical -> Activity  
**Adding to a process:**
Select process -> open process -> + -> Utility

**Page context:**
- Primary page: provides data context for the whole Activity
- Step page: context during execution of a specific step, if not defined, Primary page becomes the Step page
- Parameter page: contains activity parameters, which can be modified by the activity itself too

**Best Practice:**

look for standard options or out of the box activities where possible.  
Replacements:
- data manipulation: [Data Transforms](#data-transform)
- data calculations: Declare Expressions
- external DB query: Report Definitions

More info [here](https://pdn.pega.com/nine-tactics-reduce-your-need-custom-activities-prpc-62-sp2) on reducing need of custom activities

### Standard API activities:

**Access:** Designer Studio -> Processes and Rules -> APIs

## <a name="work-party"/>Work party
A Pega way of defining user groups and roles such as Customer or Manager.

 **Access:** App -> _select case type_ -> Process -> Work Parties -> pyCaseManagmentDefault  
 _or_ Case Designer -> _select a case_ -> Settings -> Parties

```
Party label: unique name
Role: auto populated from Party label, identifies the group on the clipboard
Party class: must be the Data-Party class or one of its descendants
 Party Prompt: additional label info to distinguish different parties
 Data transform: can be used to define initial values. The data transform must be in the party class or a parent class.
 VOE?: Visible on Entry if checked, the users are prompted to add this party every time a case is created
 Required?: if true, this work party must be present in every new case
```

 The role identifies the party on the clipboard. Each work party is a page within the WorkParty page group, and the role is used as the page index. For example, a work party with the role Customer is identified on the clipboard as **WorkParty(Customer)**.

 ## <a name="sla"/>SLAs

Advanced SLAs using rules: Case Designer -> _select a case_ -> _select assignment_ -> Goal & Deadline -> Use existing



Aviable config using SLA rules:

Assignment ready
- Immediately (default)
- Dinamically defined on Property: delay until specific time
- Timed delay: delay with specific amount of time

Calculate service levels
- Intervall from when assigment is ready
- Set to value of property

Service level definitions
- goal interval
- deadline interval
- passed deadline interval repeats (can be for a fixed amount of time or indefinitely until the user completes the assignment)

**.pxUrgencyWork** contains the default urgency value (defaults to 10)  
**.pxUrgencyAssignSLA** contains the current urgency  
**.pyUrgencyAssignAdjust** adds option for manual addjustments via actions

## Routing
Routing identifies who will work on an assignment as a case moves through a life cycle.

**Config:** Case Designer -> _select assigment_ -> General ->  Route to  
_or_ Flow Designer -> right click shape -> View properties -> Routing
_or_ Flow Designer -> + -> Smaret shapes -> approval process

**Worklist** is a list of all open assignments for specific users  
**Workbasket** when assignments are queued for a team of users, the assignments are stored in workbaskets. A team associated with a workbasket is called a _work group_.

- ToWorklist
- ToWorkbasket
- ToWorkGroupManager
- ToSkilledGroup
- ToDecisionTable
- ToDecisionTree activity

More on the default routers [here](https://pdn.pega.com/sites/pdn.pega.com/files/help_v72/procomhelpmain.htm#zstandardrules/rule-obj-activityforflows.htm?TocPath=Reference|Standard%2520rules%2520|Activity%2520rules|_____2).

## Corespondence
**Create:**  App -> right click class ->  +Create -> Process -> Correspondence

Use _Insert property_ to add properties (represented as <<.PropName>>)

Use _Insert Rule_ (far left in config) to include content in other rules such as paragraphs, segments, sections, whole Correspondence rules or fragments.

**Add:** Flow -> Utility -> use CorrNew activity  
_or_ Flow -> + -> Smart Shape -> Email  
_or_ Case designer -> Add step -> Utilities -> Send Email

Send mail manually: use SendCorrespondence local action

## Circumstancing rules
Circumstancing rules add possibility to make difference in the process based on specific variables (eg. adjusting the SLA based on costumer importance).

**Create:** Open base rule -> open dropdown @Save button -> Specialize by circumstance

Supported circumstancing conditions:
- Single value
- Multiple value: combination of values needs to be satisfied
- Date property
- As-of date: after a specified date, or during a specified range

Circumstance by Template:  
used for circumstancing by multiple conditions.  
Need to specify a template roule.

Circumstance by Property and Date:  
Property needs to be equal Value +

|Rule to be active if...                                      | Specifydate property | Specify start date | Specify enddate |
|------------------------------------------------------------|----------------------|--------------------|-----------------|
|  date value property within date range | **Yes**                  | **Yes**                | **Yes**             |
| date value property is after range    | **Yes**                  | **Yes**                | No              |
| it needs to be effective only within date range                | No                   | **Yes**                | **Yes**             |
| it needs to be effective after certain date                    | No                   | **Yes**                | No              |

**Switch** between circumstancing rules: App -> Goto Rule and expand  
_or_ Open rule -> Actions -> View Versions

## Automated decision
- **business rules**: evaluate case data
  - **when** conditions --> if()
  - **map**: evaluates an 1 or 2d matrix, returns numeric or text res
  - **decision table**: evaluates all conditions in the row: if all true, return the result assigned to the row, else go to next row. If no rows true, returns a default result.
  - **decision tree**: evaluate each branch. If true, return result assigned to that branch, else check next branch.
- decision management rules: aviable in PegaDM apps

More info on Decision Management [here](https://pdn.pega.com/decision-management).

### Configuring when rules

**Create:** select process in Case Designer -> Start when  
_or_ +Create -> Decision -> When  
_to add more of them:_ click Actions

### Configuring decision tables and trees
Can be used in: flows, routers, activities, declare expressions.

**Create:** App -> _right click a class_ -> +Create -> Decision -> Decision table / tree

## User interface
UI rules:
- **portal rule:** every UI is built inside one, they do not hold any visual elements
- **harness rules** are refereced do contet
- **section rule** is the form in which users work
- **control rules** are added to a section to help user interaction (eg. text boxs, dropdowns etc.)
- **layouts:** organize controls in a series of rows and columns

**Access** UI deign interface: App ->  _expand rule_ -> User Interface
- dynamic layout == resposive layout

### Dynamic layout properties

**Visible when conditions**:
- _Always_
- if _not blank_ _not zero_
- bool condition
- when rule

**Action sets**
- **Event** — A trigger performed by the user, such as clicking a button, hovering a mouse pointer over a field, or entering a value in a grid
- **Action** — A response performed by the system as a result of the user event (for example, when the user clicks a button, a case is created.)
- **Conditions** — Restrictions such as when rules, which can be applied to an event and action combination (for example, you can configure conditions so that hovering over a field displays a smart tip message only if the field contains a property value.)

### Data validation
- **Required fields**
- **Editable settings** -> verify patterns, formats
- **Control types** -> verify values

**Create:** App -> _right click case_ -> Create -> Process -> Validate  
**Associate with flow action:** flow diagram -> open flow action -> Validation tab -> set validation criteria