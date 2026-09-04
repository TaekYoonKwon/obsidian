There are three types of parameter callbacks. 
## 1. Pre-set Parameter Callback
Passed a list of **mutable** [[Parameters]] objects that are changed, and returns nothing. When this callback is called, it **intercepts parameter changes before they are applied**.

It takes the list **by reference**, so any changes made will affect what ultimately gets applied. It can modify the parameter list to change, add or remove entries. 

This callback is useful if one parameter is linked to another parameter, that if one changes, the other should change too. 
## 2. Set Parameter Callback
Passed a list of **immutable** list of [[Parameters]] objects, and returns ``rcl_interfaces::msg::SetParametersResult`` class. This is a list with the dimension size the same as the passed parameters. This return type indicates whether the requested change was successful or not.

This callback is still invoked before the actual parameter change, but the difference is, user can choose to accept or reject. 

====Note: When implementing set parameter callbacks, it is important to avoid cyclical dependencies. If callback of A calls callback of B which depends on A, this can easily result in race condition and eventually out-of-sync.====

## 3. Post-set Parameter Callback
Passed a list of **immutable** list of [[Parameters]] objects, and returns nothing. The main purpose of this callback is to provide the users a chance to react to changes that have been accepted. 

