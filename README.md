# aiida-common-workflows
A repository to collect ideas and first implementations for common workflow interfaces across materials science codes and plugins.
The aim consists in achieving a common interface for different codes to compute in an automated way common materials properties. 

For a specific task (for instance the relaxation of a structure), we agree on the API of a "builder factory" (named for instance following the pattern `<Code><Task>InputGenerator`), i.e. a python class that is able to return an AiiDA builder containing the required inputs to submit an AiiDA process (typically a WorkChain) computing the property.
With API we mean the name and signature of the methods that this "builder factory" has and its expected inputs.

In addition to a method `get_builder` to get the builder (see below), the class has additional methods, for instance to return information about the possible protocols available for the specific task.
Rationale: one of the goals of this InputGenerator class is to be used to automatically generate GUIs.
The GUI can be specific to the property (e.g., one for structure relaxation, one for bands calculations, ...). However, given a task (relaxation, for instance), the GUI should be autogenerated for any implementation.
Therefore, for instance, we define methods to get the list of valid protocols that the calculation accepts (that can differ between implementations), and a human-readable description.

Taking the example of the structure relaxation, a line of the kind:
```python
generator = SiestaRelaxationInputsGenerator()
builder = generator.get_builder(structure, protocol, relaxation_type)
```
returns a builder of the `SiestaRelaxWorkChain` with inputs following a specific `protocol` and `relaxation_type`.
A similar code, but using a `QuantumEspressoRelaxationInputsGenerator()` would return inputs for the `QuantumEspressoRelaxWorkChain`. More details on the specific case are in the folder RelaxWorkChain.

In addition, the WorkChain to be run should return standardized outputs among different codes, for a specific task. For instance, all should return a `StructureData` node, with an output return link labeled `relaxed_structure`, with the final structure obtained after a relaxation. Additional nodes might be allowed.
