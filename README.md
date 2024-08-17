# PhysiCell_common_errors
Common PhysiCell Errors (and fixes)


## Common PhysiCell Rules Errors 

### Language Version 1.0 
## 1. Error adding rule for `phagocytose dead cell`.
__Error message:__
```
Warning! Attempted to add behavior phagocytose dead cell which is not in the dictionary.
Either fix your model or add the missing behavior to the simulation.
```
__Background:__
In PhysiCell v1.14.0 (released circa August 2024), phagocytosis of dead cells became death-model specific.
Previously, any dead cell would be phagocytosed at the rate `dead_phagocytosis_rate`.
Now, there are three phagocytosis rates:
| New Rate Name                | New Rule Name                  | Description                                      |
|------------------------------|--------------------------------|--------------------------------------------------|
| `apoptotic_phagocytosis_rate` | `phagocytose apoptotic cell` | Rate of phagocytosing apoptotic cell |
| `necrotic_phagocytosis_rate` | `phagocytose necrotic cell` | Rate of phagocytosing necrotic cell |
| `other_dead_phagocytosis_rate` | `phagocytose other dead cell` | Rate of phagocytosing dead cells using a user-defined death model |

__Solution:__ 
To produce phagocytosis results guaranteed identical to simulations run in pre-v1.14.0 PhysiCell, replace the one rule targeting `phagocytose dead cell` with three rules targeting `phagocytose apoptotic cell`, `phatocytose necrotic cell`, and `phagocytose other dead cell`.
Alternatively, if not all these death models are used in the model, only include the relevant rules.