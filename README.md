# mortis

`mortis` is a data processing library for Arcaea developed based on **Pydantic V2**. It provides type‑safe, object‑oriented parsing, validation, and manipulation of AFF chart files and songlist data files, guaranteeing data type correctness throughout the entire process.

## Core Features
- **AFF Chart Processing**: Full parsing of AFF file structure, with object‑oriented event manipulation via `AFFEvent` subclasses (`Tap`/`Timing`/`Arc`, etc.);
- **Songlist Parsing**: Supports reading, validating, and modifying songlist data files, managed uniformly through `SonglistItem`;
- **Type Safety**: Data validation implemented with Pydantic V2, preventing illegal data formats and automatically raising type errors at runtime;
- **Minimal API**: Core functionality is exported to the root module, allowing quick access without diving into the directory structure.

## Environment Requirements
The project was developed in the following environment; no backward‑compatibility testing or related optimizations have been performed. Future optimizations may lower the minimum required versions accordingly.

- Python >= 3.12
- pydantic >= 2.12.0
    - The project relies on Pydantic V2 for data validation and more; users should have some familiarity with it before use.

## Installation
Install via pip from PyPI:
```bash
pip install mortis
```

If you wish to contribute to development:
```bash
git clone https://github.com/IzayoiArika/mortis.git
cd mortis
pip install -e .
```

## Quick Start
The following examples cover the core scenarios of `mortis`. They can be copied and run directly (an Arcaea `.aff` file is required in advance).

### Example 1: Count the number of Taps in an AFF file
```python
import os
from mortis import AFF, Tap

aff_path = ... # Replace with the actual AFF file path
aff = AFF.load_from_path(aff_path) # Parse the AFF file

# Iterate over all events and count Taps
tap_count = 0
for event in aff.iter_events():
    if isinstance(event, Tap):
        tap_count += 1
# One‑liner: tap_count = sum(isinstance(event, Tap) for event in aff.iter_events())
print(f"Number of Taps: {tap_count}")
```

### Example 2: Mirror all objects in an AFF file and save
```python
from mortis import AFF, GameObjectEvent

aff = AFF.load_from_path(aff_path)
# Iterate events and mirror‑flip all game objects
for event in aff.iter_events():
    if isinstance(event, GameObjectEvent):
        event.mirror()
# Save the modified AFF file
aff.dump_to_path(aff_path)
# Or specify a new path: aff.dump_to_path(another_path)
```

### Example 3: Check whether a TimingGroup uses anglex/angley
```python
from mortis import AFF

aff = AFF.load_from_path(aff_path)
# Iterate all TimingGroups and check anglex/angley fields
for idx, group in enumerate(aff.iter_groups()):
    if group.anglex is not None:
        print(f"TimingGroup #{idx} uses anglex; value is {group.anglex}")
    if group.angley is not None:
        print(f"TimingGroup #{idx} uses angley; value is {group.angley}")
```

### Example 4: Parse songlist data
```python
from mortis import SonglistItem

songlist_path = ... # Replace with the actual songlist path
song_item = SonglistItem.load_from_path(songlist_path) # Parse the songlist
# Access song properties
print(f"Song ID: {song_item.id}")
print(f"All difficulties: {song_item.difficulties}")
print(f"Background image: {song_item.bg}")
```

## Contribution Guide
If there are official features not yet supported by the project, you are welcome to submit an Issue or a PR.

1. Fork this repository and clone it locally.
2. Create a feature branch: `git checkout -b feature/xxxx`
3. Test the code locally to ensure the feature works correctly.
4. Commit your changes and open a Pull Request.

The project follows the PEP 8 code style guide.

## Project Structure
Most users do not need to concern themselves with the internal structure. **All public APIs have been exported to the root module `mortis`**, so you can import directly via `from mortis import XXX`.

```
mortis/
├── aff/                  # AFF chart processing module
│   ├── events/           # Definitions of various AFF events (Tap/Hold/Arc, etc.)
│   ├── lexer/            # AFF lexer
│   ├── types/            # AFF‑related data types (coordinates/easing functions, etc.)
│   ├── aff.py            # Core AFF file operations class
│   └── timinggroup.py    # TimingGroup class
├── songlist/             # Songlist data processing module
│   ├── item.py           # SonglistItem core class
│   ├── diffs.py          # Difficulty‑related definitions
│   ├── bgs.py            # Background‑image‑related definitions
│   └── types.py          # Songlist data types
├── globalcfg.py          # Global configuration
└── utils.py              # General utility functions
```

## Frequently Asked Questions (FAQ)
- **Q: Is support for AFF/songlist currently complete?**
  A: Still under development, but it already meets most basic functional needs.
- **Q: Does it support the latest AFF/songlist features of Arcaea?**
  A: We will try to keep up with updates.

## License
This project is open‑source under the **GNU General Public License v3 (GPLv3)**. See the [LICENSE](LICENSE) file for details.

## Trivia
The project name `mortis` comes from the member codename <del>and her alternate persona's self‑reference</del> of Wakaba Mutsumi in the anime *BanG Dream! Ave Mujica*.

The name has no actual connection to the purpose of this project; it is used purely out of personal preference.