# Routine - Exercise Automation Shell

Routine is a lightweight, terminal-based workout tracking and schedule automation manager built with Python and SQLite. It provides an interactive command-line interface (CLI) to design custom workout routines, schedule them across specific weekdays, and log daily completions.

## Features

- **Interactive Shell Mode**: Powered by Python's native `cmd` module for smooth navigation.
- **Dynamic Context Prompt**: Shows the currently active or selected workout group directly in the prompt brackets: `(Routine) [Upper Body]`.
- **Flexible Scheduling**: Target workouts to specific days (e.g., `Mon, Wed, Fri`) and view auto-generated 7-day calendars.
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

On initial startup, the program automatically generates a local relational database file named `routine.db` and maps out two tables: `groups` and `exercises`.

---

## Command Reference

| Command | Syntax | Description |
| :--- | :--- | :--- |
| **`add`** | `add` | Starts a multi-step wizard to create a new workout group, target reps/sets, and pick active days. |
| **`edit`** | `edit [idx]` | Modifies an existing group name, target exercises, or scheduled days by its index. |
| **`index`** | `index [idx \| remove idx]` | Lists all current workout groups, switches active context, or bulk-deletes selected indexes. |
| **`layout`** | `layout [NUM \| date 1-4 \| export]` | Displays a clean 7-day routine preview, queries a specific index, configures date formats, or prints to a file. |
| **`log`** | `log [add idx \| layout]` | Appends a timestamped record of completed workout targets into `routine.log` or prints historical file logs. |
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
Reps x Sets [Bench Press]: 5x5
Reps x Sets [Overhead Press]: 8x3
Reps x Sets [Tricep Pushdowns]: 12x3
Days (e.g. Mon, Wed, Fri): Mon, Thu
✓ Created Push Day
```

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
 8x3 Overhead Press
 12x3 Tricep Pushdowns
```

*Note: Layout options are strictly mutual exclusive. Commands like `layout 1 date 2` or `layout index 1` will be cleanly rejected.*

### 4. Adjusting the Layout Date Format
You can customize how calendar dates display using `layout date [1-4]`:
* `date 1`: Mon, Tue, Wed (Default)
* `date 2`: Monday, Tuesday, Wednesday
* `date 3`: MM/DD
* `date 4`: MM/DD/YYYY

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
2026-06-14 - Push Day: Bench Press (5x5), Overhead Press (8x3), Tricep Pushdowns (12x3)
```

---

## File Architecture

- **`routine`**: Main standalone Python script runner.
- **`routine.db`**: Relational database repository holding routine parameters.
- **`routine.log`**: Standard flat-text append log recording workout achievements.