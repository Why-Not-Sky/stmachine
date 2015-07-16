**STMachine** is a **Generic [State Machine](http://en.wikipedia.org/wiki/State_Machine)** iterator in C#.

It works on a **rule file** which could be used to define state. By default **[SCXML](http://en.wikipedia.org/wiki/SCXML)** could be used to define rules.

State Machine constructor looks like
```
/// <summary>
/// <![CDATA[StateMachine<T> constructor]]>
/// </summary>
/// <param name="stateMachineInputComparer"><![CDATA[Custom IComparer<ITransitionEvent> object.
/// User can use this object to define his own custom comparer to match the input with state transition events.]]></param>
/// <param name="stateChangedCallback"><![CDATA[Callback class on state changed]]></param>
public StateMachine(ITransitionEventComparer stateMachineInputComparer
                           , IOnStateChanged stateChangedCallback)

```


### Sample Code ###

To create a rule file for mail client as Outlook, it would look like:
##### State.scxml #####
```
<scxml
       version="1.0"
       id="mailboxRules"
       initialstate="waiting-for-mail">
  <state id="waiting-for-mail">
    <transition event="from:*@fools.com" target="spam" />
    <transition event="from:*@oracle.com" target="priority-inbox" />
    <transition event="from:*@citibank.com" target="citi" />
    <transition event="from:*@*.*" target="inbox" />
  </state>
  <state id="spam">
    <transition target="waiting-for-mail" />
  </state>
  <state id="priority-inbox">
    <transition target="waiting-for-mail" />
  </state>
  <state id="citi">
    <transition target="waiting-for-mail" />
  </state>
  <state id="inbox">
    <transition target="waiting-for-mail" />
  </state>
</scxml>
```


To create the state machine for the above stated rule file,
```
// State Machine Code
StateMachineHelper stateMachineHelper = new StateMachineHelper();
StateMachine<Mail> stateMachine = new StateMachine<Mail>(stateMachineHelper, stateMachineHelper);

/* To load the State.scxml rule file
*  This will load the rule file and initialize the state machine to "waiting-for-mail" state */
stateMachine.Enter("State.scxml");


// State Machine Helper class
public class StateMachineHelper : ITransitionEventComparer, IOnStateChanged
{
    /* Optional Custom Comparer user can define to compare the events defined in 
    *  rule file with input
    *  x: event data from rule file
    *  y: input processed by the state machine */
    public int Compare(ITransitionEvent x, ITransitionEvent y)
    {
        String ruleFileEvent = x.GetEventData<String>();
        Mail mailObject = x.GetEventData<Mail>();

        Match match = (new Regex(ruleFileEvent.Replace("from:", ""))).Match(mailObject.From);
        return match.Success ? 1 : -1;
    }

    // Event triggered by State Machine, whenever state changes
    public void OnStateChanged<T>(State<T> previousState, State<T> nextState)
    {
        if (previousState.Name == "waiting-for-mail")
        {
            switch (nextState.Name)
            {
                case "spam":
                    // push mail to spam
                    break;
                case "priority-inbox":
                    // push mail to priority inbox
                    break;
                case "citi":
                    // push mail to citi
                    break;
                case "inbox":
                    // push mail to inbox
                    break;
            }
        }
    }
}
```


Then, state machine could be used as
```
// some callback function 
void OnMailReceived(Mail mail)
{
    stateMachine.Process(mail);
}
```
