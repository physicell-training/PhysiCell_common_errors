# PhysiCell_common_errors
Common PhysiCell Errors (and fixes)

## Common PhysiCell Rules Errors 

### 1. Error adding rule for `phagocytose dead cell` {#error-adding-rule-phagocytose-dead-cell}
__Error message:__
```
Warning! Attempted to add behavior phagocytose dead cell which is not in the dictionary.
Either fix your model or add the missing behavior to the simulation.
```
__Background:__
In [PhysiCell v1.14.0](https://github.com/MathCancer/PhysiCell/releases/tag/1.14.0), phagocytosis of dead cells became death-model specific.
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

### 2. Error using rules `version="2.0"`.
__Error message:__
```
**Error: Version < 3 no longer supported.
```
__Background:__
In [PhysiCell v1.14.0](https://github.com/MathCancer/PhysiCell/releases/tag/1.14.0), several changes were made to the rules language prompting a change to the rules version.
Phagoctyosis changed (see [above](#error-adding-rule-phagocytose-dead-cell)) and the meaning of `damage_rate` also changed from indicating the rate of damage being dealt to another cell (v2.0) to the rate of damage accumulation by the cell (v3.0).

__Solution:__
Update the configuration file so that the `version` attribute for the `ruleset` element is set to `"3.0"`:
```
<cell_rules>
    <rulesets>
        <ruleset protocol="CBHG" version="3.0" format="csv" enabled="true">
            <folder>./config</folder>
            <filename>rules.csv</filename>
        </ruleset>
    </rulesets>
    <settings />
</cell_rules>
```

## Common Miscellaneous PhysiCell Errors

### 1. `random_seed` not found in User Parameters.
__Error message:__
```
ERROR : Unknown parameter random_seed ! Quitting.
```
__Background:__
In [PhysiCell v1.14.0](https://github.com/MathCancer/PhysiCell/releases/tag/1.14.0), the `random_seed` can be specified within the `<options>` element of the configuration file.
The purpose is to make it more flexible to allow for the user to set the random seed by the system clock to allow for batches of different simulations.
While including in `user_parameters` is still acceptable, this is now discouraged.
However, `custom.cpp` taken from pre-v1.14.0 projects will likely call `SeedRandom(parameters.ints("random_seed"));` without first checking the existence of `random_seed` within `user_parameters`.

__Solution:__ 
Find the call to `SeedRandom(parameters.ints("random_seed"));` in the project-specific code.
__This is almost certainly in `create_cell_types( void )` at the top of `custom.cpp`.__
Either remove this call or replace with
```
if (parameters.ints.find_index("random_seed") != -1)
{
    SeedRandom(parameters.ints("random_seed"));
}
```
It is recommended to remove it and instead include `random_seed` in `<options>`:
```
<options>
...
    <random_seed>0</random_seed>
</options>
```
OR to use the system clock:
```
<options>
...
    <random_seed>system_clock</random_seed>
</options>
```