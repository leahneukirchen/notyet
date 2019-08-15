## notyet: a text-based recursive task tracker

*notyet* is a simple but powerful tool to keep track of tasks you have
not yet done.  It is opinionated and targeted to software developers,
but there is no reason you cannot use it to plan other projects or
your daily life.

notyet keeps track of tasks which are each one line in a text file,
similar to the todo.txt format.
(notyet ignores a leading `#` or `//` in the text files to
allow putting tasks into C/C++ files and many scripting languages.)

Tasks can have four different states, represented by the first
non-whitespace character of the line:

* Open tasks: `-`
* Started tasks (waiting for / assigned to other people): `*`
* Maybe tasks: `?`
* Finished tasks: `x`
* WONTFIX tasks (tasks you decided not to do): `X`

After the state character, there must be whitespace, followed by the
task description.

The first two task states are regarded as undone, the last two states
are regarded as done.

Tasks can be nested by using indentation: if you indent a task deeper
than the previous task, it is regarded as a subtask.
notyet has no limit on indentation depth.
I recommend using two spaces for each level.

Additionally, a task can have a deadline if it starts with the ISO
format `YYYY-MM-DD`.

When you run notyet, it will read the task file (by default `~/.notyet`,
use `-f` to override) and *aggregate the tasks*:

* Tasks where all subtasks are done are regarded as done.
* Tasks where all subtasks are maybe are regarded as maybe.
* A sum of open tasks and total tasks is computed and appended to
  tasks which have subtasks.
* Tasks where subtasks have a deadline adopt the earliest deadline.
* Tasks with deadline display how many days are left.
* Tasks are sorted by state and alphabetically.
* By default, done tasks are not shown (use `-a` to show all tasks).

The result is printed as a tree, so you quickly see what you still
need to do.

## Example

Consider this plan of a birthday party:

```
x buy a present
- invite guests
  x Estalla
  - Zebby
  - Orraleen
  ? Xana
- 2017-10-16 birthday cake
  ? sprinkles
  - flour
  - sugar
  x chocolate
  x 1tsp salt
  - eggs
  X banana
```

We have three tasks, two of which have subtasks.  We bought a present
already.  We need to invite two more guests, Zebby and Orraleen, and
we don't know yet whether to invite Xana too.  The birthday cake needs
to be done by October 16, 2017.  We need to buy flour, sugar, and
eggs.  We already have chocolate and salt for it; and we certainly
won't put bananas into it.  Perhaps, we'll put sprinkles on top, if we
can find some.

To get an overview of what remains to do, we run:

```
% notyet -f party.ny
2017-10-16 {+14} [3/10]
- 2017-10-16 {+14} birthday cake [2/6]
  - eggs
  - flour
  - sugar
  ? sprinkles
- invite guests [1/3]
  - Orraleen
  - Zebby
  ? Xana
```

The done tasks are hidden, and we know we have 14 days left to bake the cake.

## Special directives

When a task description starts with a special directive, additional
features can be used:

* `#include FILENAME` adds the tasks of FILENAME as subtasks.
* `#includeall GLOB` behaves like `#include FILENAME` for each match
  of the glob.
* `#exec COMMANDLINE` runs the specified command and parses it as subtasks.
  This can be used to integrate external sources of tasks.
* `#splat` replaces the task itself and its direct subtasks with all
  their subtasks.  This can be used to flatten an `#includeall` or output
  of `#exec` into the task tree.

## Filtering support

If you have a large list of tasks, it can be difficult to find
particular tasks.  Thus, the arguments of the `notyet` script define a
filter:

* By default, all *words* that appear on the command line must appear
  in the task.
* If a subtask matches, the parent task matches too.
* If a task matches, all subtasks match too.
* A special behavior exists for words that start with `@`, so called *tags*:
  if multiple tags are given, it is enough if a task matches only one tag.
  E.g. the filter `@bug @feature` will show all tasks that are marked
  using `@bug` or `@feature`.

## Editing support

As notyet only aggregates tasks from potentially many files, it can be
hard to find them to mark them as done.  To help with this, run
notyet with the `-e` flag, which will spawn `vim` with a predefined macro
to visit the line a task originates from when pressing RET or TAB.

In order to provide editing support for `#exec`, the subprocess can
prefix each line of output with `FILENAME:LINENUMBER`, followed by a TAB.

## Installation

Copy the script `notyet` into your PATH.
It just needs Ruby and its standard library.

## Copyright

notyet is in the public domain.

To the extent possible under law,
Leah Neukirchen <leah@vuxu.org>
has waived all copyright and related or
neighboring rights to this work.

http://creativecommons.org/publicdomain/zero/1.0/
