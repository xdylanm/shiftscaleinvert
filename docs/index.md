# Shift Scale Invert (SSI)

A utility module for applying offsets and scaling to signals.

* Module size: 4HP (20 mm)
* Power: 10mA (+12V); 10mA (-12V)

!!! repository "Project Source"

    The project files, including schematic and layout, are available on [github](https://github.com/xdylanm/shiftscaleinvert)

## Features

Some basic CV operations, including

* 3 attenuverter inputs, with 2x gain on input A
* all inputs normaled to +5V (effectively +10V on input A with the 2x gain)
* cascaded mixed outputs (A, A+B, A+B+C)

There are many sources of inspiration for this utility module - see the references below.

## Documentation

[Theory](theory.md)

[Attenuverter Transfer Functions](notebook.ipynb)

[Assembly Guide](assembly.md)

[Schematic](assets/schematic.pdf)

## References

1. Intellijel (Triplatt)[https://intellijel.com/shop/eurorack/triplatt/]
2. Happy Nerding (3xMIA)[https://happynerding.com/category/3xmia/]
3. Noise Engineering (Lapsus Os)[https://manuals.noiseengineering.us/lo/]
4. b:art Instruments (b:10)[https://bartinstruments.com/products/b-10]