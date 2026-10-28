# Routine - Exercise Automation Shell

Routine is a lightweight, terminal-based workout tracking and schedule automation manager built with Python and SQLite. It provides an interactive command-line interface (CLI) to design custom workout routines, schedule them across specific weekdays, and log daily completions.

## Features

- **Interactive Shell Mode**: Powered by Python's native `cmd` module for smooth navigation.
- **Dynamic Context Prompt**: Shows the currently active or selected workout group directly in the prompt brackets: `(Routine) [Upper Body]`.
- **Flexible Scheduling**: Target workouts to specific days (e.g., `Mon, Wed, Fri`) and view auto-generated 7-day calendars (Monday-first for weekday-name formats).
- **Optional Weights**: Add a weight in `lb` or `kg` to any exercise target, e.g. `6x5 25lb`.
- **Relational Database**: Keeps records robust and lightweight using an optimized SQLite backend with cascading deletes.
- **Strict Command Sanitization**: Safe execution handling preventing accidental action overlaps or argument cluttering.

---

## Getting Started

### Prerequisites
- Python 3.8 or higher
- SQLite3 (built into Python standard library)

### Execution
Clone or save the script file as `routine` (or `routine.py`), make it executable, and run it:

```bash
chmod +x routine
./routine
```

On initial startup, the program automatically generates a local relational database file named `routine.db` (in `~/.local/share/routine/`) and maps out two tables: `groups` and `exercises`.

---

## Command Reference

| Command | Syntax | Description |
| :--- | :--- | :--- |
| **`add`** | `add` | Starts a multi-step wizard to create a new workout group, set each exercise's `Sets x Reps` (with optional weight), and pick active days. |
| **`edit`** | `edit [idx]` | Modifies an existing group name, target exercises, or scheduled days by its index. |
| **`index`** | `index [idx \| remove idx]` | Lists all current workout groups, switches active context, or bulk-deletes selected indexes. |
| **`layout`** | `layout [NUM \| date 1-4 \| export]` | Displays a clean 7-day routine preview (Monday-first for `date 1` and `date 2`), queries a specific index, configures date formats, or prints to a file. |
| **`log`** | `log [add idx \| layout \| edit]` | Appends a timestamped record of completed workout targets into `routine.log`, prints the log, or opens it in your text editor. |
| **`help`** | `help [command]` | Displays instructions and summaries for program functionalities. |
| **`exit`** | `exit` (or `Ctrl+D`) | Safely disconnects the database connections and quits the runtime context. |

---

## Usage Examples

### 1. Creating a New Routine
Type `add` and follow the guided text parameters:
```text
(Routine) add
Group Name: Push Day
Exercises (comma-separated): Bench Press, Overhead Press, Tricep Pushdowns
Sets x Reps [Bench Press]: 5x5
Sets x Reps [Overhead Press]: 3x8
Sets x Reps [Tricep Pushdowns]: 3x12
Days (e.g. Mon, Wed, Fri): Mon, Thu
✓ Created Push Day
```

Targets are written as `SETSxREPS`, so `3x8` means 3 sets of 8 reps.

To attach a weight, add it after the sets and reps using `lb` or `kg`:
```text
Sets x Reps [Dumbbell Press]: 6x5 25lb
```
Spaces and case are flexible (`6x5 25 LB` is stored as `6x5 25lb`), and decimals are allowed (`3x10 12.5kg`). The weight shows up in your layout and logs:
```text
 6x5 25lb Dumbbell Press
```

*Note: Targets entered before this version were saved as-is. If you previously typed them as `Reps x Sets`, run `edit [idx]` to flip them.*

### 2. Managing Group Selections
List or change your active shell focus using numeric indices:
```text
(Routine) index
  1. Push Day
  2. Pull Day

(Routine) index 1
(Routine) [Push Day] 
```

### 3. Checking Your Workout Layout
Review what exercises are on your plate for the week or isolate a specific group:
```text
(Routine) layout
--- SCHEDULE ---
Mon
Push Day
 5x5 Bench Press
 3x8 Overhead Press
 3x12 Tricep Pushdowns
```

*Note: Layout options are strictly mutual exclusive. Commands like `layout 1 date 2` or `layout index 1` will be cleanly rejected.*

### 4. Adjusting the Layout Date Format
You can customize how calendar dates display using `layout date [1-4]`:
* `date 1`: Mon, Tue, Wed (Default)
* `date 2`: Monday, Tuesday, Wednesday
* `date 3`: MM/DD
* `date 4`: MM/DD/YYYY

`date 1` and `date 2` always list the current week Monday through Sunday. `date 3` and `date 4` show the next 7 days starting from today.

```text
(Routine) layout date 3
✓ Date format set to 3
```

### 5. Tracking Workout History
When you finish a workout, register it instantly to your text log file:
```text
(Routine) log add 1
✓ Logged
```
This logs your activity inside `routine.log` in a clean timestamped layout:
```text
2026-06-14 - Push Day: Bench Press (5x5), Overhead Press (3x8), Tricep Pushdowns (3x12)
```

Print the log with `log layout`, or open it for editing with `log edit`:
```text
(Routine) log edit
```
`log edit` opens `routine.log` in the editor named by your `$VISUAL` or `$EDITOR` environment variable. If neither is set, it falls back to `nano`, then `vim`, then `vi`. To always use nano, add this to your shell profile:
```bash
export EDITOR=nano
```

---

## File Architecture

- **`routine`**: Main standalone Python script runner.
- **`routine.db`**: Relational database repository holding routine parameters.
- **`routine.log`**: Standard flat-text append log recording workout achievements.

Both data files live in `~/.local/share/routine/`.
