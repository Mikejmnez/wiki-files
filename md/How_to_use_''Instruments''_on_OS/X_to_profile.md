The *instruments* profiles can be run from the command line, but it
doesn't seem to pick up the symbols for the executables when run that
way with Xcode 9. Here's how to fix that.

## Building symbols

Even though executables are built using **-g3 -O0**, OS/X clang/gcc dies
not include those symbols in binary images built by the linker. To make
that info available to *instruments,* use the program **dsymutil** on
the *executable,* *.dylib* and/or *.so*. This may be an iterative
process; see the next section.

## Running *instruments*

Once the *<name>.dSYM* directories are built, run the instruments GUI
and:

1.  Click the menu to *Choose Target...*
    <img src="Choose_Target.png" title="Choose_Target.png" width="500"
    alt="Choose_Target.png" />
2.  Navigate to the program to run - yes, it's tedious.
    <img src="ChooseProgram.png" title="ChooseProgram.png" width="500"
    alt="ChooseProgram.png" />
    1.  Choose the program (note that the dSYM dir is there too)
    2.  provide the arguments to the program
    3.  and, below that, the full path to the working directory.

### The red dot

Now click the 'Run' button (the red dot)
<img src="TheRedDot.png" title="TheRedDot.png" width="500"
alt="TheRedDot.png" />

Here's the result
<img src="ItRuns.png" title="ItRuns.png" width="500" alt="ItRuns.png" />

Click to expand (and click the arrow in the circle to examine a
sub-hierarchy of calls)
<img src="ExpandCalls.png" title="ExpandCalls.png" width="500"
alt="ExpandCalls.png" />