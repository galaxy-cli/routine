# Routine - Exercise Automation Shell

Routine is a lightweight, terminal-based workout tracking and schedule automation manager built with Python and SQLite. It provides an interactive command-line interface (CLI) to design custom workout routines, schedule them across specific weekdays, and log daily completions.

## Features

- **Interactive Shell Mode**: Powered by Python's native `cmd` module for smooth navigation.
- **Dynamic Context Prompt**: Shows the currently active or selected workout group directly in the prompt brackets: `(Routine) [Upper Body]`.
- **Flexible Scheduling**: Target workouts to specific days (e.g., `Mon, Wed, Fri`) and view auto-generated 7-day calendars (Monday-first for weekday-name formats).
- **Optional Weights**: Add a weight in `lb` or `kg` to any exercise target, e.g. `6x5 25lb`.
- **Relational Database**: Keeps records robust and lightweight using an optimized SQLite backend with cascading deletes.
- **Strict Input Validation**: Every prompt and command argument is checked against a specific format. Bad input is rejected with a message explaining what's wrong, and wizards re-ask instead of saving junk.

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

*Note: Layout options are strictly mutually exclusive. Commands like `layout 1 date 2` or `layout index 1` will be cleanly rejected.*

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

## Input Validation

Wizard prompts (`add` / `edit`) keep asking until the value is valid. `Ctrl+C` or `Ctrl+D` cancels the whole wizard. In `edit`, pressing Enter keeps the shown `[default]`, which is validated too.

| Field | Rules |
| :--- | :--- |
| **Group name** | 1-40 characters: letters, numbers, spaces, commas, hyphens, parentheses; must start with a letter or number. Extra spaces are collapsed. Must be unique (case-insensitive). |
| **Exercises** | Comma-separated, 1-20 per group. Each is 1-40 characters: letters, numbers, spaces, hyphens; must start with a letter or number. No empty entries (e.g. trailing commas) and no duplicates (case-insensitive). |
| **Sets x Reps** | `SETSxREPS`, with sets 1-99 and reps 1-999 (`0x5` is rejected). |
| **Weight (optional)** | After sets x reps, a number greater than 0 and up to 9999 (max 2 decimals) followed by `lb` or `kg`, e.g. `6x5 25lb`. Any other unit is rejected. |
| **Days** | At least one of `Mon, Tue, Wed, Thu, Fri, Sat, Sun` (full names like `Monday` also work), separated by commas or spaces, case-insensitive. Unknown words such as `Funday` are rejected rather than ignored. Days are stored in Mon-Sun order. |

Command arguments are strict as well:

- **Indices** must be plain positive numbers within range. `edit abc`, `edit 1 2`, `layout 99` and `log add 0` are all rejected rather than falling back to the selected group. A bad `index N` no longer clears your current selection.
- **`index remove`** accepts numbers, lists (`1,3`) and ranges (`1-3`). If any part is invalid or out of range, nothing is removed.
- **Confirmations** only accept `y`/`yes` or `n`/`no` (Enter means no).
- **`layout`** takes exactly one of: nothing, `NUM`, `date 1-4`, or `export`. `layout date` with no number or `layout date 12` is rejected.
- **`log`** takes exactly `add [idx]`, `layout`, or `edit`. **`help`** only accepts a real command name, and **`exit`** takes no arguments.
- Unknown commands print an error, and pressing Enter on a blank line does nothing instead of repeating the last command.

---

## File Architecture

- **`routine`**: Main standalone Python script runner.
- **`routine.db`**: Relational database repository holding routine parameters.
- **`routine.log`**: Standard flat-text append log recording workout achievements.

Both data files live in `~/.local/share/routine/`.
