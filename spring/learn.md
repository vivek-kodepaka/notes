

If container controls object creation:

It can decide implementation

It can manage lifecycle

It can reuse singleton

It can proxy it (AOP 👀)

It can switch based on profile

Scan → Register BeanDefinitions → Instantiate Beans → Inject dependencies → Initialize → Ready

```text
BeanDefinition (metadata)
This contains:

Class name

Scope

Constructor info

Dependencies

Init methods

Lazy flag
```