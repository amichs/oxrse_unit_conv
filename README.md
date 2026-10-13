# Oxford RSE Unit Conversion (amich's version)

[![Unittest](https://github.com/OxfordRSE/oxrse_unit_conv/actions/workflows/unittest.yml/badge.svg)](https://github.com/OxfordRSE/oxrse_unit_conv/actions/workflows/unittest.yml)

## Overview

OxRSE Unit Conversion is a simple unit conversion library designed primarily for teaching collaborative GitHub
techniques. It is a python module that exposes a command line interface and commands for converting between
different units.

## Installation

You can install the script from PIP with
```shell
pip install oxrse_unit_conv
```

## Usage

### In a python script

```python
import oxrse_unit_conv

n = 42
unit_in = 'km'
unit_out = 'mile'
print(f"{n}{unit_in} in {unit_out} = {oxrse_unit_conv.convert(n, unit_in, unit_out)}")
```

The same result can be obtained by interacting with unit objects directly.
Units are converted to one another by asking one unit to convert to the other.
The `Unit.to_unit` function takes a number and a target `Unit`.

```python
import oxrse_unit_conv

n = 42
km = oxrse_unit_conv.km
mile = oxrse_unit_conv.mile
print(f"{n}{km.abbr} in {mile} = {km.to_unit(n, mile)}")
```

### CLI

## Development

### Structure

The module source code is in `./src/oxrse_unit_conv`. 
To add a new Unit, you will need to edit the `units.py` file.
That file has a section for each base SI unit, and you should place your unit in the section that corresponds
to its base SI unit. 
Thus, if you were creating a new unit of luminosity called the `sparkle`, 
you would place it under the base unit for luminous intensity, the `candela`.

A Unit has the following properties:
* `name` the unit's full name. Should be singular (e.g. `sparkle` rather than `sparkles`)
* `abbr` the unit's abbreviation. Can be the same as the name, except that spaces aren't allowed.
* `si` the unit's SI unit. Should be the SI Unit object.
* `to_si` a lambdba function to convert a number of this unit into its SI unit.
* `[from_si]` a lambda function to convert a number of the SI unit into this unit.
  If this is not specified, it is `lambda n: n / self.to_si(1)`, which reverses a simple multiplicative conversion
  (e.g. if 2sparkle = 10candela, then 10candela / 5candela/sparkle = 2sparkle).
* `[exponent=1]` the exponent of the unit. Units can only be converted where their SI units have the same exponent.
  The exponent of the unit can be different from its SI unit, 
  e.g. an acre has exponent 1 but its SI unit is square meters (exponent = 2).

Once you have written a unit (or before, if you prefer test-driven-development), write unit tests for it.

### Testing

Tests are kept in the `__tests__` directory, and this should contain a different file for each unit
with the name `test_unit_UNITNAME.py`. 
Our sparkle test file would be `test_unit_sparkle.py`.
In this file we `import unittest`, as well as the relevant units from the package 
`from oxrse_unit_conv.units import sparkle, candela`.

We should write a test or two converting known values. 
Each test is declared as a method of the `unittest.TestCase` class, and has one or more `self.assert*()` calls
where `*` represents one of a number of different assertions that `TestCase` has access to. 
For sparkle, we may want to check that we can convert both ways:

```python
import unittest
from oxrse_unit_conv.units import sparkle, candela


class TestSparkle(unittest.TestCase):
    def test_SI(self):
        self.assertTrue(sparkle.si_unit.matches(candela))

    def test_to_si(self):
        self.assertEqual(sparkle.to_si(1), 5)
    
    def test_from_si(self):
        self.assertEqual(candela.to_unit(5, sparkle), 1)


if __name__ == '__main__':
    unittest.main()
```

If we had other luminosity units, we could add other methods to test that we can convert between those, too.

Tests can be run by entering the `src/oxrse_unit_conv` directory and running the command:
```shell
python -m unittest discover  -s ../tests -t .. -v
```

### Building package

You should not need to do this, but if you want to build a version of the package, you can.
Building the package produces distribution files from the source code (that you've just updated).
This should be accompanied by an appropriate update to the semantic versioning.

```shell
python3 -m build
```

The updated files will be created in `./dist`.

## Acknowledgements

The initial setup for this python project was created following the 
[packaging tutorial](https://packaging.python.org/en/latest/tutorials/packaging-projects/).
