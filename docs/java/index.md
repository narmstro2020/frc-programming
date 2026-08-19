# Java

Robot code is Java. Not "Java-flavoured blocks", not copy-paste from last year's
repo &mdash; actual Java, with types and objects and a compiler that will tell you
exactly what it thinks of your work.

You do not need to be good at it before you touch a robot. You do need to stop
being surprised by it.

## Start with Perscript

**[Perscript](https://snakestongue.github.io/Perscript/)** is where you start.
It is straight practice &mdash; Java, C++, and Python drills &mdash; and drilling is the
part that actually moves the needle. Reading about a `for` loop does nothing.
Writing forty of them does.

[Open Perscript](https://snakestongue.github.io/Perscript/){ .md-button .md-button--primary target=_blank }

Work the Java track. Do it on your own time between meetings; we will not spend
Monday afternoons watching each other type basic syntax.

### What you need to be solid on

Before WPILib will make any sense at all:

- **Types and variables** &mdash; `int`, `double`, `boolean`, and why the difference
  between `int` and `double` division has ended more robot code than any other bug
- **Conditionals and loops** &mdash; `if`/`else`, `for`, `while`
- **Methods** &mdash; parameters, return values, and why a method that does one thing
  is worth two that do half each
- **Classes and objects** &mdash; fields, constructors, `this`
- **Interfaces and inheritance** &mdash; this one matters more than usual for us,
  because it is the whole idea behind how DigiLib swaps one motor controller for
  another

## Then: Java the way FRC uses it

FRC Java has habits that general Java tutorials will not teach you, and they are
where new programmers get lost even after they can write a loop:

- **Command-based structure** &mdash; subsystems, commands, and why almost nothing
  happens in a plain loop
- **Suppliers and lambdas** &mdash; `() -> something`, which is everywhere in modern
  WPILib and looks like line noise until it suddenly doesn't
- **Units** &mdash; WPILib has a real units library now, and using it is cheaper than
  debugging a number that turned out to be in the wrong one

!!! note "Lessons land here as we go"
    This section fills in over the fall. The sequence follows the meetings on the
    [Meetings](../meetings.md) page.
