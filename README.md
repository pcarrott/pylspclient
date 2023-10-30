# CoqPyt

Interact with Coq files and navigate through your proofs using our Python client for [coq-lsp](https://github.com/ejgallego/coq-lsp).

Execute Coq files, retrieve the generated context and edit proofs through addition and removal of steps.

## Installation

[coq-lsp](https://github.com/ejgallego/coq-lsp) must be installed on version >= 0.1.7. Follow the installation instructions provided [here](https://github.com/ejgallego/coq-lsp#%EF%B8%8F-installation).

```bash
pip install -r requirements.txt
```

```bash
python -m pip install -e .
```

## Usage
```python
import os
from coq.base_file import CoqFile
from coq.proof_file import ProofFile
from coq.structs import TermType

# Open Coq file
with CoqFile(os.path.join(os.getcwd(), "examples/example.v")) as coq_file:
    coq_file.exec(nsteps=2)
    # Get all terms defined until now
    print("Number of terms:", len(coq_file.context.terms))
    # Filter by Tactics
    print("Number of tactics:",
        len(
            list(filter(
                lambda term: term.type == TermType.TACTIC,
                coq_file.context.terms.values(),
            ))
        )
    )

    # Enter proof
    coq_file.exec(nsteps=4)
    print("In proof:", coq_file.in_proof)
    # Get current goals
    print(coq_file.current_goals())

    # Save compiled file
    coq_file.save_vo()
    print("Compiled file exists:", os.path.exists("examples/example.vo"))
    os.remove("examples/example.vo")

    # Run remaining file
    coq_file.run()
    print("Checked:", coq_file.checked)
    # Get all terms defined until now
    print("Number of terms:", len(coq_file.context.terms))

with ProofFile(os.path.join(os.getcwd(), "examples/example.v")) as proof_file:
    # Number of proofs in the file
    print("Number of proofs:", len(proof_file.proofs))
    print("Proof:", proof_file.proofs[0].text)

    # Print steps of proof
    for step in proof_file.proofs[0].steps:
        print(step.text, end="")
    print()

    # Get the context used in the third step
    print(proof_file.proofs[0].steps[2].context)
    # Print the goals in the third step
    print(proof_file.proofs[0].steps[2].goals)

    # Print number of terms in context
    print("Number of terms:", len(proof_file.context.terms))
    # Filter for Notations only
    print("Number of notations:",
        len(
            list(filter(
                lambda term: term.type == TermType.NOTATION,
                proof_file.context.terms.values(),
            ))
        )
    )
```

### Run tests

```bash
pytest tests -s
```

## Contributing

Pull requests are welcome. 

For major changes, please open an issue first to discuss what you would like to change.

Please make sure to update tests as appropriate.

## License

[MIT](https://choosealicense.com/licenses/mit/)