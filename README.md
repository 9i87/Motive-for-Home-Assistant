# Motive for Home Assistant

<div align="center">

![Home Assistant](https://img.shields.io/badge/home%20assistant-%2341BDF5.svg?style=for-the-badge&logo=home-assistant&logoColor=white)
![Version](https://img.shields.io/badge/version-1.0.0-blue?style=for-the-badge)
![License](https://img.shields.io/badge/license-MIT-green?style=for-the-badge)

**Transform your productivity into measurable goals.**

A beautifully designed productivity tracking system for Home Assistant that turns time into achievements.
Made by @v-berlin and @apfelFunker

<img width="1748" height="1606" alt="Image" src="https://github.com/user-attachments/assets/ae69df2b-00e7-4bc2-a05d-aa2267276044" />

#### Warnung
⚠️ The README.md and the comments in the code are generatet by AI. Errors may be present. If you find one feel free to create an Issue with the Tag "Readme". Thanks for your cooperation

#### Info
This Project is built for Home Assistant Docker but it will also work with HA Core. The steps you need to do are a little different but not that far off.

#### Info
The original Version named "Flow" can be found [here](). Flow is not under development anymore and the new project is called Motive. Motive will start with the same features as Flow but will get more eventually.
</div>

---

## 📋 Table of Contents

- [Features](#-features)
- [What is Motive?](#-what-is-motive)
- [Detailed Component Documentation](#-detailed-component-documentation)
  - [Input Numbers](#input-numbers-zahlen-eingaben)
  - [Input Booleans](#input-booleans-schalter)
  - [Input DateTimes](#input-datetimes-datumzeit-eingaben)
  - [Input Texts](#input-texts-text-eingaben)
  - [Template Sensors](#template-sensors-berechnete-sensoren)
- [Automation Details](#-automation-details)
- [Script Details](#-script-details)
- [Installation](#-installation)
- [Setup Guide](#-setup-guide-docker-focused)
- [Dashboard Configuration](#-dashboard-configuration)
  - [Comprehensive Dashboard](#-comprehensive-full-featured-dashboard)
  - [Compact Dashboard](#-compact-dashboard-medium-complexity)
  - [Minimal Dashboard](#-minimal-dashboard-mobile-friendly)
  - [Entities List View](#-entities-list-view-debugging--admin)
- [Architecture & Data Motive](#️-architecture--data-motive)
- [Usage](#-usage)
- [Advanced Usage Examples](#-advanced-usage-examples)
- [Customization](#-customization)
- [Troubleshooting](#-troubleshooting)
- [Contributing](#-contributing)

---

## ✨ Features

### Core Functionality
- ⏱️ **Smart Time Tracking** - Track your productive time with an intelligent timer system
- 🎯 **Dynamic Daily Goals** - Set and adjust daily goals to match your productivity needs
- 🔥 **Streak System** - Build momentum with consecutive day tracking
- 🏷️ **Category Management** - Organize work by categories (Work, Personal, Coding, Design, Engineering)

### Smart Features
- 🏥 **Sick Mode** - Pause your streak without losing progress when you're unwell
- 📈 **Progress Dashboard** - Real-time visualization of your daily achievement
- 🔔 **Smart Notifications** - Celebrate wins and gentle reminders
- 📱 **Mobile Ready** - Full control from your phone
- 🐳 **Docker Optimized** - 100% configurable via Home Assistant Web UI

---

## 🎯 What is Motive?

Motive is a productivity tracking system designed specifically for Home Assistant Docker installations. It helps you:

1. **Track Time**: Start a timer when you begin productive work
2. **Categorize Work**: Choose from predefined categories (Work, Coding, Design, etc.)
3. **Meet Daily Goals**: Set daily time goals and track your progress
4. **Build Streaks**: Maintain consecutive days of meeting your goals
5. **Stay Motivated**: Get notifications when you reach milestones

### Key Components

**Entities Created:**
- 3 Input Numbers (Daily Goal, Time Tracked, Streak Counter)
- 7 Input Booleans (Timer, Sick Mode, 5 Categories)
- 2 Input DateTimes (Timer Start, Last Reset)
- 2 Input Texts (Active Category, History Data)
- 5 Template Sensors (Progress, Status, Active Work, Timer Elapsed, Time to Goal)

**Automations:**
- Timer Toggle Handler (start/stop tracking)
- Category Selection (single category active at a time)
- Daily Reset (midnight reset with streak logic)
- Goal Reached Notification (celebrate achievements)

**Scripts:**
- Increase/Decrease Goal (adjust daily targets)
- Reset Day (manual reset if needed)
- Add Time Manually (for retroactive tracking)

---

## 📚 Detailed Component Documentation

### Input Numbers (Zahlen-Eingaben)

#### `input_number.motive_daily_goal`
**Purpose:** Stores your daily productivity goal in minutes.
- **Range:** 0-1440 minutes (0-24 hours)
- **Step:** 5 minutes
- **Default:** Set by user during initial setup
- **Usage:** This is your target for the day. When `motive_time_tracked_today` reaches or exceeds this value, your streak increments at midnight.
- **Example:** Setting to 120 means you aim to track 2 hours of productive work daily.

#### `input_number.motive_time_tracked_today`
**Purpose:** Accumulates the total productive minutes tracked today.
- **Range:** 0-1440 minutes (0-24 hours)
- **Step:** 1 minute
- **Behavior:** 
  - Automatically increments when you stop the timer
  - Resets to 0 at midnight
  - Can be manually adjusted with the `motive_add_time` script
- **Usage:** This is your daily scoreboard showing how much productive time you've logged.

#### `input_number.motive_streak_counter`
**Purpose:** Tracks consecutive days of meeting your daily goal.
- **Range:** 0-9999 days
- **Step:** 1 day
- **Behavior:**
  - Increments by 1 at midnight if `motive_time_tracked_today` >= `motive_daily_goal`
  - Resets to 0 if goal not met (unless Sick Mode is active)
  - Preserved when Sick Mode is enabled
- **Usage:** Provides motivation through gamification - the higher your streak, the more consistent you've been.

---

### Input Booleans (Schalter)

#### `input_boolean.motive_timer_active`
**Purpose:** Controls the productivity timer state (on/off).
- **Behavior:**
  - When turned ON: Records current time to `motive_timer_start`, sends notification
  - When turned OFF: Calculates elapsed time, adds to `motive_time_tracked_today`, sends summary notification
- **Automation:** Managed by "Motive - Timer Toggle Handler" automation
- **Usage:** Toggle this to start/stop tracking a work session.

#### `input_boolean.motive_sick_mode`
**Purpose:** Preserves your streak when you're unable to work due to illness or other valid reasons.
- **Behavior:**
  - When enabled before midnight: Streak is preserved even if goal not met
  - Automatically turns off after midnight reset
  - Logs sick days in history data
- **Usage:** Enable this on days when you're sick or otherwise unable to meet your goal legitimately.

#### `input_boolean.motive_cat_work`
**Purpose:** Represents the "Work" category toggle.
- **Behavior:** When enabled, disables all other categories and sets `motive_active_category` to "Work"
- **Icon:** `mdi:domain`
- **Usage:** Activate when tracking general work tasks.

#### `input_boolean.motive_cat_privat`
**Purpose:** Represents the "Personal/Private" category toggle.
- **Behavior:** When enabled, disables all other categories and sets `motive_active_category` to "Privat"
- **Icon:** `mdi:home`
- **Usage:** Activate when tracking personal projects or tasks.

#### `input_boolean.motive_cat_coding`
**Purpose:** Represents the "Coding" category toggle.
- **Behavior:** When enabled, disables all other categories and sets `motive_active_category` to "Coding"
- **Icon:** `mdi:code-braces`
- **Usage:** Activate when tracking programming or software development work.

#### `input_boolean.motive_cat_design`
**Purpose:** Represents the "Design" category toggle.
- **Behavior:** When enabled, disables all other categories and sets `motive_active_category` to "Design"
- **Icon:** `mdi:palette`
- **Usage:** Activate when tracking design, creative, or visual work.

#### `input_boolean.motive_cat_engineering`
**Purpose:** Represents the "Engineering" category toggle.
- **Behavior:** When enabled, disables all other categories and sets `motive_active_category` to "Engineering"
- **Icon:** `mdi:cog`
- **Usage:** Activate when tracking engineering, technical, or architectural work.

---

### Input DateTimes (Datum/Zeit-Eingaben)

#### `input_datetime.motive_timer_start`
**Purpose:** Records the exact timestamp when the timer was started.
- **Format:** Full date and time (YYYY-MM-DD HH:MM:SS)
- **Behavior:**
  - Set to current time when `motive_timer_active` turns ON
  - Used to calculate elapsed time while timer is running
  - Read by `sensor.motive_timer_elapsed` for real-time duration display
- **Usage:** Enables accurate time tracking by storing the session start point.

#### `input_datetime.motive_last_reset`
**Purpose:** Records when the last daily reset occurred.
- **Format:** Full date and time (YYYY-MM-DD HH:MM:SS)
- **Behavior:**
  - Updated to current time at midnight during daily reset
  - Also updated when manual reset script is run
- **Usage:** Helps verify reset operations and can be used for debugging timing issues.

---

### Input Texts (Text-Eingaben)

#### `input_text.motive_active_category`
**Purpose:** Stores the name of the currently active work category.
- **Max Length:** 20 characters
- **Default:** "Keine" (None)
- **Possible Values:** "Work", "Privat", "Coding", "Design", "Engineering", "Keine"
- **Behavior:**
  - Updated by "Motive - Kategorie Selection" automation when a category toggle is activated
  - Reset to "Keine" at midnight or when timer stops without category
- **Usage:** Allows tracking what type of work you're doing; displayed in status messages and notifications.

#### `input_text.motive_history_data`
**Purpose:** Stores historical productivity data in JSON format.
- **Max Length:** 255 characters
- **Format:** JSON object with date keys: `{"2024-01-15": {"percent": 105.5, "minutes": 127, "sick": false}, ...}`
- **Behavior:**
  - Updated at midnight with previous day's statistics
  - Automatically keeps only last 365 days to prevent overmotive
  - Includes percentage completed, minutes tracked, and sick mode status
- **Usage:** Enables historical tracking and potential future analytics/visualization features.

---

### Template Sensors (Berechnete Sensoren)

#### `sensor.motive_goal_progress`
**Purpose:** Calculates your progress toward the daily goal as a percentage.
- **Unit:** Percentage (%)
- **Calculation:** `(motive_time_tracked_today / motive_daily_goal) * 100`
- **Range:** 0-100+ (can exceed 100% if you surpass your goal)
- **Attributes:**
  - `tracked_minutes`: Current total in minutes
  - `goal_minutes`: Goal in minutes
  - `remaining_minutes`: Minutes left to reach goal (0 if goal met)
  - `time_formatted`: Tracked time in HH:MM format
  - `goal_formatted`: Goal time in HH:MM format
- **Usage:** Primary metric for dashboard visualization; drives streak logic and goal notifications.

#### `sensor.motive_status`
**Purpose:** Provides a human-readable status summary with emoji indicators.
- **Possible States:**
  - `🔴 [Category] läuft...` - Timer is active
  - `🏥 Krankheitsmodus` - Sick mode is enabled
  - `✅ Ziel erreicht!` - Goal has been met today
  - `[Category] - [X]%` - Current progress for selected category
  - `Bereit zum Starten 🚀` - Ready to begin tracking
- **Dynamic Icon:** Changes between `mdi:record-circle` (when running) and `mdi:information-outline` (when idle)
- **Usage:** Quick status overview for dashboards and mobile notifications.

#### `sensor.motive_active_work`
**Purpose:** Shows which category is currently being tracked, but only when timer is running.
- **States:**
  - Category name (Work, Privat, Coding, Design, Engineering) when timer active
  - "Inaktiv" when timer is stopped
- **Dynamic Icon:** Changes based on active category or shows pause icon when inactive
- **Attributes:**
  - `timer_running`: Boolean state of timer
  - `category`: Current category name
  - `elapsed_minutes`: Minutes elapsed in current session
  - `started_at`: Timestamp when current session started
- **Usage:** Provides detailed context about the current work session for history graphs and analytics.

#### `sensor.motive_timer_elapsed`
**Purpose:** Real-time calculation of minutes elapsed since timer started.
- **Unit:** Minutes
- **Calculation:** `(current_time - motive_timer_start) in minutes`
- **Behavior:**
  - Returns 0 when timer is not active
  - Continuously updates while timer is running
  - Resets when timer is stopped
- **Attributes:**
  - `formatted`: Time in HH:MM format
  - `start_time`: Timestamp from `motive_timer_start`
- **Usage:** Shows live progress during work sessions; useful for dashboard displays and awareness.

#### `sensor.motive_time_to_goal`
**Purpose:** Calculates how much time remains until you reach your daily goal.
- **States:**
  - "Erreicht!" when goal is met or exceeded
  - "H:MM" format showing hours and minutes remaining
- **Calculation:** `motive_daily_goal - motive_time_tracked_today`
- **Attributes:**
  - `minutes_remaining`: Numeric minutes left (0 if goal met)
- **Usage:** Motivational metric showing how close you are to completing your daily target.

---

## 🤖 Automation Details

### Motive - Timer Toggle Handler
**ID:** `d9872d0607fe426f9b999f3691acdfa2`

**Purpose:** Manages the start and stop of productivity tracking sessions.

**Triggers:**
- `input_boolean.motive_timer_active` changes from OFF to ON (timer start)
- `input_boolean.motive_timer_active` changes from ON to OFF (timer stop)

**Actions on Timer Start:**
1. Records current timestamp to `input_datetime.motive_timer_start`
2. Checks if a category is selected:
   - If no category selected (or "Keine"), automatically selects "Work" and enables `motive_cat_work`
   - Disables all other category toggles
3. Sends notification: "⏱️ Timer gestartet - Kategorie: [category]"

**Actions on Timer Stop:**
1. Retrieves start time from `input_datetime.motive_timer_start`
2. Validates start time exists and is valid
3. Calculates elapsed time:
   - `elapsed_seconds = current_time - start_time`
   - `elapsed_minutes = elapsed_seconds / 60` (rounded down)
4. Updates `input_number.motive_time_tracked_today`:
   - Adds elapsed minutes to current total
   - Caps at 1440 minutes (24 hours) maximum
5. Sends notification: "⏹️ Timer gestoppt - [X] Minuten getrackt - Heute gesamt: [Y] Minuten"

**Mode:** Single (only one instance can run at a time)

**Use Case Example:**
- You start work on a coding project at 9:00 AM
- Toggle timer ON → Notification confirms start with "Coding" category
- Work until 10:30 AM (90 minutes)
- Toggle timer OFF → 90 minutes added to daily total, notification confirms

---

### Motive - Kategorie Selection
**ID:** `b80cb47ec90a47b8919584669b1f2d46`

**Purpose:** Ensures only one work category is active at any time (mutual exclusivity).

**Triggers:**
- Any category toggle turns ON:
  - `input_boolean.motive_cat_work`
  - `input_boolean.motive_cat_privat`
  - `input_boolean.motive_cat_coding`
  - `input_boolean.motive_cat_design`
  - `input_boolean.motive_cat_engineering`

**Actions:**
1. Identifies which category was just enabled
2. Maps entity ID to category name using `category_map`:
   ```
   motive_cat_work → "Work"
   motive_cat_privat → "Privat"
   motive_cat_coding → "Coding"
   motive_cat_design → "Design"
   motive_cat_engineering → "Engineering"
   ```
3. Updates `input_text.motive_active_category` with the selected category name
4. Turns OFF all other category toggles (keeps only the triggered one ON)

**Mode:** Single

**Use Case Example:**
- Currently tracking "Work" category
- You switch to a design task and toggle "Design" ON
- Automation immediately turns OFF "Work" toggle and sets active category to "Design"
- Ensures clean category tracking without conflicts

---

### Motive - Daily Reset
**ID:** `43ac83b7ca764df298d90fafc9e6c212`

**Purpose:** Performs comprehensive midnight reset with intelligent streak management and history logging.

**Trigger:**
- Every day at midnight (00:00:00)

**Variables Calculated:**
- `goal_reached`: True if `motive_goal_progress >= 100%`
- `sick_mode`: True if `motive_sick_mode` is enabled
- `yesterday`: Previous day's date in YYYY-MM-DD format
- `progress`: Yesterday's goal progress percentage
- `minutes`: Yesterday's total tracked minutes

**Actions:**

1. **History Logging:**
   - Parses existing `motive_history_data` JSON
   - Adds yesterday's entry with date as key:
     ```json
     "2024-01-15": {
       "percent": 105.5,
       "minutes": 127,
       "sick": false
     }
     ```
   - Maintains only last 365 days (removes oldest if exceeding)
   - Saves updated JSON back to `motive_history_data`

2. **Streak Logic (conditional):**
   
   **Case A: Sick Mode Enabled**
   - Preserves current streak (no change to `motive_streak_counter`)
   - Sends notification: "🏥 Krankheitstag - Streak bleibt bei [X] Tagen"
   
   **Case B: Goal Reached**
   - Increments `motive_streak_counter` by 1
   - Sends notification: "🔥 Streak! [X] Tage in Folge!"
   
   **Case C: Goal Not Reached (Default)**
   - Resets `motive_streak_counter` to 0
   - Sends notification: "💔 Streak verloren - Neuer Start heute!"

3. **Daily Cleanup:**
   - Resets `motive_time_tracked_today` to 0
   - Turns OFF `motive_sick_mode`
   - Sets `motive_active_category` to "Keine"
   - Turns OFF all category toggles
   - Turns OFF `motive_timer_active` (stops any running timer)
   - Updates `motive_last_reset` to current timestamp

**Mode:** Single

**Use Case Example:**

**Scenario 1 - Goal Met:**
- You tracked 130 minutes with a 120-minute goal
- At midnight: Streak increments from 5 to 6 days, all counters reset, notification celebrates your streak

**Scenario 2 - Sick Day:**
- You tracked 0 minutes but enabled Sick Mode
- At midnight: Streak stays at 5 days (preserved), sick mode turns off for fresh start

**Scenario 3 - Goal Missed:**
- You tracked 80 minutes with a 120-minute goal
- At midnight: Streak resets to 0, encouragement to start fresh today

---

### Motive - Goal Reached Notification
**ID:** `bd4a3f2ab8e94c4e883caf4837aea542`

**Purpose:** Celebrates when you achieve your daily goal with an encouraging notification.

**Trigger:**
- `sensor.motive_goal_progress` rises above 99.9%

**Conditions:**
1. `motive_sick_mode` must be OFF (don't celebrate during sick days)
2. Must be more than 20 hours since last trigger (prevents spam notifications)

**Action:**
- Sends persistent notification: "🎉 Tagesziel erreicht!"
- Message includes:
  - Congratulations
  - Total minutes tracked today
  - Current streak count

**Mode:** Single

**Use Case Example:**
- Your daily goal is 120 minutes
- You've been tracking work throughout the day
- The moment you hit 120 minutes (100%), a celebration notification appears
- Won't notify again for 20 hours even if you continue tracking beyond 100%

---

## 🔧 Script Details

### motive_increase_goal
**Purpose:** Increases your daily goal by 5 minutes.

**Mode:** Single

**Sequence:**
1. Gets current value of `input_number.motive_daily_goal`
2. Adds 5 minutes
3. Caps at maximum of 1440 minutes (24 hours)
4. Sets the new value

**Use Case:**
- Your current goal is 120 minutes (2 hours)
- You're consistently meeting it and want to challenge yourself
- Run this script 6 times to increase to 150 minutes (2.5 hours)

**Usage:**
- Call from Automations & Scenes → Scripts
- Add script button to dashboard
- Create automation to adjust goal based on performance

---

### motive_decrease_goal
**Purpose:** Decreases your daily goal by 5 minutes.

**Mode:** Single

**Sequence:**
1. Gets current value of `input_number.motive_daily_goal`
2. Subtracts 5 minutes
3. Prevents going below 0 minutes
4. Sets the new value

**Use Case:**
- Your goal is too ambitious (180 minutes)
- You're struggling to maintain your streak
- Run this script 12 times to reduce to 120 minutes for more achievable goals

**Usage:**
- Call from Automations & Scenes → Scripts
- Add script button to dashboard
- Use during setup to fine-tune your optimal daily target

---

### motive_reset_day
**Purpose:** Manually resets the day's tracking (emergency reset button).

**Mode:** Single

**Sequence:**
1. Resets `motive_time_tracked_today` to 0
2. Turns OFF timer and all category toggles
3. Sets active category to "Keine"
4. Sends notification: "🔄 Reset - Tag wurde zurückgesetzt"

**Use Case:**
- You accidentally left the timer running overnight
- Time tracked is incorrect and needs to be cleared
- You want to restart the day from scratch

**Warning:** This does NOT affect your streak counter. Use carefully as it will erase today's progress.

**Usage:**
- Call from Automations & Scenes → Scripts
- Add emergency reset button to dashboard
- Typically not needed in normal operation

---

### motive_add_time
**Purpose:** Manually adds minutes to today's tracked time (for retroactive tracking).

**Mode:** Single

**Fields:**
- `minutes`: Number of minutes to add (1-480, required)

**Sequence:**
1. Prompts for number of minutes to add
2. Adds specified minutes to `motive_time_tracked_today`
3. Caps total at 1440 minutes maximum
4. Sends notification: "➕ Zeit hinzugefügt - [X] Minuten wurden manuell hinzugefügt"

**Use Case:**
- You worked for 2 hours but forgot to start the timer
- Manually add 120 minutes to account for that work
- Ensures accurate daily totals and fair streak tracking

**Usage:**
- Call from Automations & Scenes → Scripts
- Manually input desired minutes when prompted
- Useful for days when you forget to track sessions

---

## 📦 Installation

### Prerequisites

- **Home Assistant** running in Docker (tested with 2024.1.0+)
- Access to Home Assistant Web UI
- Access to configuration files via:
  - File Editor (if available as add-on in your setup)
  - Direct volume access (via Docker volume mount)
  - SSH/SFTP access to configuration directory

> **Note for Docker Users:** This guide assumes you have access to your Home Assistant configuration directory. This is typically mapped as a Docker volume (e.g., `/path/on/host:/config`).

---

## 🚀 Setup Guide (Docker Focused)

### Step 1: Access Configuration Files

You need to access your Home Assistant configuration directory. Choose one of these methods:

**Option A: File Editor Add-on (if available)**
1. Install File Editor from the Home Assistant Add-on Store (if not already installed)
2. Navigate to Settings → Add-ons → File Editor
3. Open File Editor

**Option B: Direct File Access**
1. Access your Docker host machine
2. Navigate to your Home Assistant config directory (typically where you mounted the `/config` volume)
   ```bash
   cd /path/to/your/homeassistant/config
   ```

### Step 2: Copy Configuration Files

Copy the three YAML files from this repository to your Home Assistant configuration directory:

1. **motive.yaml** - Main configuration with all entities and sensors
2. **automations.yaml** - Automations for Motive (append to existing file)
3. **scripts.yaml** - Helper scripts for Motive (append to existing file)

#### Important Notes:

- **If you already have automations.yaml and scripts.yaml:**
  - Open the Motive versions from this repository
  - Copy the content and **append** it to your existing files
  - Do NOT replace your existing files completely

- **For motive.yaml:**
  - Create a new file named `motive.yaml` in your config directory
  - Copy the entire content from the repository

### Step 3: Enable Packages Configuration

You need to tell Home Assistant to load the `motive.yaml` file as a package.

1. Open your `configuration.yaml` file
2. Add or modify the following section:

```yaml
homeassistant:
  packages: !include motive.yaml
```

**If you already have a packages section:**
```yaml
homeassistant:
  packages:
    motive: !include motive.yaml
    # your other packages...
```

**Alternative: Using a packages directory (recommended for multiple packages):**
```yaml
homeassistant:
  packages: !include_dir_named packages/
```
Then create a `packages/` directory and place `motive.yaml` inside it.

### Step 4: Restart Home Assistant

After copying the files and updating configuration.yaml:

1. Go to **Settings** → **System** → **Restart**
2. Click **Restart Home Assistant**
3. Wait for the restart to complete (usually 1-2 minutes)

### Step 5: Verify Installation

After restart, verify that Motive entities were created:

1. Go to **Settings** → **Devices & Services** → **Entities**
2. Search for "motive" - you should see:
   - `input_number.motive_daily_goal`
   - `input_number.motive_time_tracked_today`
   - `input_number.motive_streak_counter`
   - `input_boolean.motive_timer_active`
   - `input_boolean.motive_sick_mode`
   - `input_boolean.motive_cat_work`
   - `input_boolean.motive_cat_privat`
   - `input_boolean.motive_cat_coding`
   - `input_boolean.motive_cat_design`
   - `input_boolean.motive_cat_engineering`
   - `input_datetime.motive_timer_start`
   - `input_datetime.motive_last_reset`
   - `input_text.motive_active_category`
   - `input_text.motive_history_data`
   - `sensor.motive_goal_progress`
   - `sensor.motive_status`
   - `sensor.motive_active_work`
   - `sensor.motive_timer_elapsed`
   - `sensor.motive_time_to_goal`

3. Check automations:
   - Go to **Settings** → **Automations & Scenes**
   - You should see 4 new automations starting with "Motive -"

4. Check scripts:
   - Go to **Settings** → **Automations & Scenes** → **Scripts** tab
   - You should see scripts starting with "motive_"

### Step 6: Set Initial Values (via Web UI)

Now configure Motive entirely through the Web UI - no YAML editing needed!

1. **Set Your Daily Goal:**
   - Go to **Settings** → **Devices & Services** → **Entities**
   - Search for `input_number.motive_daily_goal`
   - Click on it and set your desired daily goal in minutes (e.g., 120 for 2 hours)

2. **Initialize Other Values (Optional):**
   - All other entities are automatically initialized with sensible defaults
   - You can adjust them later as needed

---

## 📊 Dashboard Configuration

Motive provides extensive dashboard options to visualize ALL your productivity data. Below are several dashboard configurations ranging from comprehensive to minimal.

---

### 🌟 Comprehensive Full-Featured Dashboard

This dashboard includes **ALL Motive entities** for complete visibility and control.

1. Go to your Home Assistant dashboard
2. Click the three dots (⋮) in the top right
3. Select **Edit Dashboard**
4. Click **+ Add Card**
5. Select **Manual Card** at the bottom
6. Copy and paste the following YAML:

```yaml
type: vertical-stack
cards:
  # ==================== STATUS & PROGRESS SECTION ====================
  - type: tile
    entity: sensor.motive_status
    name: Motive Status
    icon: mdi:information-outline
    color: blue
    vertical: false
    show_entity_picture: false
  
  - type: tile
    entity: sensor.motive_goal_progress
    name: Tagesfortschritt
    icon: mdi:progress-helper
    color: green
    vertical: false
    features:
      - type: bar-gauge
    features_position: bottom
  
  # ==================== KEY METRICS ROW ====================
  - type: horizontal-stack
    cards:
      - type: tile
        entity: input_number.motive_time_tracked_today
        name: Heute Getrackt
        color: blue
        vertical: false
        icon: mdi:clock-check
        tap_action:
          action: more-info
      
      - type: tile
        entity: input_number.motive_daily_goal
        name: Tagesziel
        color: cyan
        vertical: false
        icon: mdi:target
        tap_action:
          action: more-info
      
      - type: tile
        entity: input_number.motive_streak_counter
        name: Streak
        color: orange
        vertical: false
        icon: mdi:fire
        tap_action:
          action: more-info
  
  # ==================== TIMER CONTROL SECTION ====================
  - type: tile
    entity: input_boolean.motive_timer_active
    name: Produktivitäts-Timer
    color: red
    hide_state: false
    vertical: false
    icon: mdi:timer
    features:
      - type: toggle
    features_position: inline
  
  - type: horizontal-stack
    cards:
      - type: tile
        entity: sensor.motive_timer_elapsed
        name: Timer Laufzeit
        color: deep-purple
        vertical: false
        icon: mdi:timer-sand
        tap_action:
          action: none
      
      - type: tile
        entity: sensor.motive_time_to_goal
        name: Bis zum Ziel
        color: teal
        vertical: false
        icon: mdi:flag-checkered
        tap_action:
          action: none
  
  # ==================== CATEGORY SELECTION ====================
  - type: markdown
    content: |
      ### 🏷️ Kategorien
  
  - type: horizontal-stack
    cards:
      - type: tile
        entity: input_boolean.motive_cat_work
        name: Work
        color: green
        hide_state: true
        vertical: true
        icon: mdi:domain
        tap_action:
          action: toggle
      
      - type: tile
        entity: input_boolean.motive_cat_privat
        name: Privat
        color: purple
        hide_state: true
        vertical: true
        icon: mdi:home
        tap_action:
          action: toggle
      
      - type: tile
        entity: input_boolean.motive_cat_coding
        name: Coding
        color: blue
        hide_state: true
        vertical: true
        icon: mdi:code-braces
        tap_action:
          action: toggle
  
  - type: horizontal-stack
    cards:
      - type: tile
        entity: input_boolean.motive_cat_design
        name: Design
        color: pink
        hide_state: true
        vertical: true
        icon: mdi:palette
        tap_action:
          action: toggle
      
      - type: tile
        entity: input_boolean.motive_cat_engineering
        name: Engineer
        color: amber
        hide_state: true
        vertical: true
        icon: mdi:cog
        tap_action:
          action: toggle
      
      - type: tile
        entity: input_text.motive_active_category
        name: Aktive Kategorie
        color: indigo
        vertical: true
        icon: mdi:tag
        tap_action:
          action: more-info
  
  # ==================== ACTIVE WORK VISUALIZATION ====================
  - type: tile
    entity: sensor.motive_active_work
    name: Aktuelle Arbeit
    icon: mdi:briefcase
    color: deep-orange
    vertical: false
    tap_action:
      action: more-info
  
  # ==================== HISTORY GRAPH ====================
  - type: history-graph
    entities:
      - entity: sensor.motive_active_work
        name: Arbeitskategorie
      - entity: input_boolean.motive_timer_active
        name: Timer Status
    hours_to_show: 12
    title: Aktivitätsverlauf (12 Std)
  
  # ==================== GOAL ADJUSTMENT CONTROLS ====================
  - type: markdown
    content: |
      ### 🎯 Ziel Anpassen
  
  - type: horizontal-stack
    cards:
      - type: button
        name: Ziel -5 Min
        icon: mdi:minus-circle
        tap_action:
          action: call-service
          service: script.motive_decrease_goal
        show_name: true
        show_icon: true
        icon_height: 40px
      
      - type: tile
        entity: input_number.motive_daily_goal
        name: Aktuelles Ziel
        color: cyan
        vertical: false
        icon: mdi:bullseye-arrow
      
      - type: button
        name: Ziel +5 Min
        icon: mdi:plus-circle
        tap_action:
          action: call-service
          service: script.motive_increase_goal
        show_name: true
        show_icon: true
        icon_height: 40px
  
  # ==================== SICK MODE & RESET ====================
  - type: horizontal-stack
    cards:
      - type: tile
        entity: input_boolean.motive_sick_mode
        name: Krankheitsmodus
        color: grey
        vertical: false
        icon: mdi:hospital-box
        tap_action:
          action: toggle
      
      - type: button
        name: Tag Zurücksetzen
        icon: mdi:restore
        tap_action:
          action: call-service
          service: script.motive_reset_day
          confirmation:
            text: Möchten Sie den heutigen Tag wirklich zurücksetzen?
        show_name: true
        show_icon: true
        icon_height: 40px
  
  # ==================== DATETIME DISPLAYS ====================
  - type: markdown
    content: |
      ### 📅 Zeitinformationen
  
  - type: horizontal-stack
    cards:
      - type: tile
        entity: input_datetime.motive_timer_start
        name: Timer Startzeit
        color: light-blue
        vertical: false
        icon: mdi:clock-start
        tap_action:
          action: more-info
      
      - type: tile
        entity: input_datetime.motive_last_reset
        name: Letzter Reset
        color: brown
        vertical: false
        icon: mdi:calendar-clock
        tap_action:
          action: more-info
  
  # ==================== MANUAL TIME ENTRY ====================
  - type: markdown
    content: |
      ### ➕ Manuelle Zeiterfassung
  
  - type: button
    name: Zeit Manuell Hinzufügen
    icon: mdi:clock-plus
    tap_action:
      action: call-service
      service: script.motive_add_time
    show_name: true
    show_icon: true
    icon_height: 40px
  
  # ==================== HISTORY DATA ====================
  - type: tile
    entity: input_text.motive_history_data
    name: Historien-Daten (JSON)
    color: blue-grey
    vertical: false
    icon: mdi:database
    tap_action:
      action: more-info
```

7. Click **Save**

This comprehensive dashboard provides:
- ✅ All 5 template sensors with visualizations
- ✅ All 3 input numbers displayed with context
- ✅ All 7 input booleans (timer, sick mode, 5 categories)
- ✅ All 2 input datetimes
- ✅ All 2 input texts
- ✅ Interactive controls for all 3 scripts
- ✅ History graph showing activity timeline
- ✅ Goal adjustment buttons
- ✅ Manual time addition button
- ✅ Emergency reset button with confirmation

---

### 🎨 Compact Dashboard (Medium Complexity)

A balanced view showing essential controls and key metrics:

```yaml
type: vertical-stack
cards:
  # Status and Progress
  - type: tile
    entity: sensor.motive_status
    name: Motive Status
    color: blue
    vertical: false
  
  - type: tile
    entity: sensor.motive_goal_progress
    name: Fortschritt
    color: green
    vertical: false
    features:
      - type: bar-gauge
    features_position: bottom
  
  # Key Metrics
  - type: horizontal-stack
    cards:
      - type: tile
        entity: input_number.motive_time_tracked_today
        name: Getrackt
        color: blue
        vertical: false
      
      - type: tile
        entity: input_number.motive_streak_counter
        name: Streak
        color: orange
        vertical: false
      
      - type: tile
        entity: sensor.motive_timer_elapsed
        name: Laufzeit
        color: purple
        vertical: false
  
  # Timer Control
  - type: tile
    entity: input_boolean.motive_timer_active
    name: Timer
    color: red
    hide_state: true
    vertical: false
    features:
      - type: toggle
    features_position: inline
  
  # Categories
  - type: horizontal-stack
    cards:
      - type: tile
        entity: input_boolean.motive_cat_work
        name: Work
        color: green
        hide_state: true
        vertical: true
        tap_action:
          action: toggle
      
      - type: tile
        entity: input_boolean.motive_cat_privat
        name: Privat
        color: green
        hide_state: true
        vertical: true
        tap_action:
          action: toggle
      
      - type: tile
        entity: input_boolean.motive_cat_coding
        name: Coding
        color: green
        hide_state: true
        vertical: true
        tap_action:
          action: toggle
      
      - type: tile
        entity: input_boolean.motive_cat_design
        name: Design
        color: green
        hide_state: true
        vertical: true
        tap_action:
          action: toggle
      
      - type: tile
        entity: input_boolean.motive_cat_engineering
        name: Engineer
        color: green
        hide_state: true
        vertical: true
        tap_action:
          action: toggle
  
  # Active Work & Sick Mode
  - type: horizontal-stack
    cards:
      - type: tile
        entity: sensor.motive_active_work
        name: Aktive Arbeit
        color: deep-orange
        vertical: false
      
      - type: tile
        entity: input_boolean.motive_sick_mode
        name: Krank
        color: grey
        vertical: false
        tap_action:
          action: toggle
  
  # History
  - type: history-graph
    entities:
      - entity: sensor.motive_active_work
    hours_to_show: 10
    title: Verlauf
  
  # Goal Controls
  - type: horizontal-stack
    cards:
      - type: button
        name: -5 Min
        icon: mdi:minus
        tap_action:
          action: call-service
          service: script.motive_decrease_goal
      
      - type: tile
        entity: input_number.motive_daily_goal
        name: Ziel
        color: cyan
        vertical: false
      
      - type: button
        name: +5 Min
        icon: mdi:plus
        tap_action:
          action: call-service
          service: script.motive_increase_goal
```

---

### 📱 Minimal Dashboard (Mobile-Friendly)

Simple, clean interface for quick interactions:

```yaml
type: vertical-stack
cards:
  - type: tile
    entity: sensor.motive_goal_progress
    name: Fortschritt
    icon: mdi:progress-helper
    color: green
    vertical: false
    features:
      - type: bar-gauge
    features_position: bottom
  
  - type: horizontal-stack
    cards:
      - type: tile
        entity: input_number.motive_time_tracked_today
        name: Getrackt
        color: blue
        vertical: false
        tap_action:
          action: none
      
      - type: tile
        entity: input_number.motive_streak_counter
        name: Streak
        color: orange
        vertical: false
        tap_action:
          action: none
  
  - type: tile
    entity: input_boolean.motive_timer_active
    name: Timer
    color: red
    hide_state: true
    vertical: false
    features:
      - type: toggle
    features_position: inline
  
  - type: horizontal-stack
    cards:
      - type: tile
        entity: input_boolean.motive_cat_work
        name: Work
        color: green
        hide_state: true
        vertical: true
        tap_action:
          action: toggle
      
      - type: tile
        entity: input_boolean.motive_cat_coding
        name: Coding
        color: blue
        hide_state: true
        vertical: true
        tap_action:
          action: toggle
      
      - type: tile
        entity: input_boolean.motive_cat_design
        name: Design
        color: pink
        hide_state: true
        vertical: true
        tap_action:
          action: toggle
```

---

### 📋 Entities List View (Debugging & Admin)

For troubleshooting or seeing all raw entity states:

```yaml
type: entities
title: Motive - Alle Entitäten
entities:
  # Template Sensors
  - entity: sensor.motive_status
    name: Status
  - entity: sensor.motive_goal_progress
    name: Fortschritt (%)
  - entity: sensor.motive_active_work
    name: Aktive Arbeit
  - entity: sensor.motive_timer_elapsed
    name: Timer Laufzeit
  - entity: sensor.motive_time_to_goal
    name: Zeit bis Ziel
  
  # Input Numbers
  - type: section
    label: Zahlen-Eingaben
  - entity: input_number.motive_daily_goal
    name: Tagesziel (Min)
  - entity: input_number.motive_time_tracked_today
    name: Heute Getrackt (Min)
  - entity: input_number.motive_streak_counter
    name: Streak (Tage)
  
  # Input Booleans
  - type: section
    label: Schalter
  - entity: input_boolean.motive_timer_active
    name: Timer Aktiv
  - entity: input_boolean.motive_sick_mode
    name: Krankheitsmodus
  - entity: input_boolean.motive_cat_work
    name: Kategorie - Work
  - entity: input_boolean.motive_cat_privat
    name: Kategorie - Privat
  - entity: input_boolean.motive_cat_coding
    name: Kategorie - Coding
  - entity: input_boolean.motive_cat_design
    name: Kategorie - Design
  - entity: input_boolean.motive_cat_engineering
    name: Kategorie - Engineering
  
  # Input DateTimes
  - type: section
    label: Datum/Zeit
  - entity: input_datetime.motive_timer_start
    name: Timer Startzeit
  - entity: input_datetime.motive_last_reset
    name: Letzter Reset
  
  # Input Texts
  - type: section
    label: Text-Felder
  - entity: input_text.motive_active_category
    name: Aktive Kategorie
  - entity: input_text.motive_history_data
    name: Historien-Daten (JSON)
  
  # Scripts
  - type: section
    label: Skripte
  - type: button
    name: Ziel Erhöhen (+5 Min)
    icon: mdi:plus-circle
    tap_action:
      action: call-service
      service: script.motive_increase_goal
  - type: button
    name: Ziel Verringern (-5 Min)
    icon: mdi:minus-circle
    tap_action:
      action: call-service
      service: script.motive_decrease_goal
  - type: button
    name: Tag Zurücksetzen
    icon: mdi:restore
    tap_action:
      action: call-service
      service: script.motive_reset_day
      confirmation:
        text: Wirklich zurücksetzen?
  - type: button
    name: Zeit Manuell Hinzufügen
    icon: mdi:clock-plus
    tap_action:
      action: call-service
      service: script.motive_add_time
```

---

### 🎯 Dashboard Customization Tips

**Color Schemes:**
- Change `color:` values: `red`, `blue`, `green`, `orange`, `purple`, `pink`, `amber`, `cyan`, `teal`, `indigo`, `deep-purple`, `light-blue`, `grey`, `blue-grey`, `brown`

**Layout Adjustments:**
- `vertical: true/false` - Changes tile orientation
- `hide_state: true/false` - Shows/hides entity state text
- Remove cards you don't need
- Reorder cards by cutting and pasting YAML blocks

**Icons:**
- Find more icons at: https://materialdesignicons.com/
- Change `icon:` to any `mdi:icon-name`

**Interactive Features:**
- Add `tap_action: action: more-info` to see entity details
- Add `tap_action: action: toggle` for quick on/off switches
- Add `confirmation: text: "Are you sure?"` to buttons for safety

**Additional Visualizations:**
You can add these card types for more insights:
- `type: gauge` - Circular gauge for progress
- `type: statistic` - Large number display
- `type: bar-gauge` - Progress bar
- `type: graph` - Line graph over time

---

## 🏗️ Architecture & Data Motive

Understanding how Motive components interact helps you customize and troubleshoot effectively.

### Component Interaction Diagram

```
┌─────────────────────────────────────────────────────────────────┐
│                         USER INTERACTION                          │
└────────────────────────┬────────────────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────────────────┐
│                     DASHBOARD / UI LAYER                          │
│  • Timer Toggle                                                   │
│  • Category Buttons                                               │
│  • Goal Adjustment Scripts                                        │
└────────────────────────┬────────────────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────────────────┐
│                    INPUT ENTITIES (State Storage)                 │
│                                                                   │
│  INPUT BOOLEANS          INPUT NUMBERS         INPUT DATETIMES   │
│  • motive_timer_active     • motive_daily_goal     • motive_timer_start│
│  • motive_sick_mode        • motive_time_tracked   • motive_last_reset │
│  • motive_cat_* (5x)       • motive_streak_counter                   │
│                                                                   │
│  INPUT TEXTS                                                      │
│  • motive_active_category                                           │
│  • motive_history_data                                              │
└────────────────────────┬────────────────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────────────────┐
│                      AUTOMATION LAYER                             │
│                                                                   │
│  [Timer Toggle Handler] ─────► Start/Stop Timer                  │
│         │                      Record Time                        │
│         │                      Send Notifications                 │
│         │                                                         │
│  [Category Selection] ───────► Mutual Exclusivity                │
│         │                      Update Active Category             │
│         │                                                         │
│  [Daily Reset] ──────────────► Midnight Cleanup                  │
│         │                      Streak Logic                       │
│         │                      History Logging                    │
│         │                                                         │
│  [Goal Notification] ─────────► Celebrate Achievements            │
└────────────────────────┬────────────────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────────────────┐
│                   TEMPLATE SENSORS (Computed Values)              │
│                                                                   │
│  • sensor.motive_goal_progress     (% calculation)                  │
│  • sensor.motive_status            (status text)                    │
│  • sensor.motive_active_work       (current work display)           │
│  • sensor.motive_timer_elapsed     (real-time duration)             │
│  • sensor.motive_time_to_goal      (remaining time)                 │
└────────────────────────┬────────────────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────────────────┐
│                    VISUALIZATION LAYER                            │
│  • Progress Bars                                                  │
│  • History Graphs                                                 │
│  • Status Indicators                                              │
│  • Real-time Updates                                              │
└─────────────────────────────────────────────────────────────────┘
```

### Data Motive: Typical Work Session

```
1. USER STARTS TIMER
   ├─► input_boolean.motive_timer_active = ON
   └─► Triggers: "Timer Toggle Handler" automation
       ├─► Records: input_datetime.motive_timer_start = now()
       ├─► Sets default category if none selected
       └─► Notification: "⏱️ Timer gestartet"

2. TIMER RUNS
   ├─► sensor.motive_timer_elapsed continuously updates
   │   └─► Calculation: (now() - motive_timer_start) in minutes
   └─► Dashboard shows real-time elapsed time

3. USER STOPS TIMER
   ├─► input_boolean.motive_timer_active = OFF
   └─► Triggers: "Timer Toggle Handler" automation
       ├─► Calculates: elapsed_minutes = (now() - timer_start) / 60
       ├─► Updates: input_number.motive_time_tracked_today += elapsed_minutes
       ├─► Updates: sensor.motive_goal_progress recalculates automatically
       └─► Notification: "⏹️ Timer gestoppt - X Minuten getrackt"

4. GOAL REACHED CHECK
   └─► If sensor.motive_goal_progress >= 100%
       └─► Triggers: "Goal Reached Notification" automation
           └─► Notification: "🎉 Tagesziel erreicht!"

5. MIDNIGHT ARRIVES
   └─► Triggers: "Daily Reset" automation
       ├─► Logs yesterday's data to input_text.motive_history_data
       ├─► Checks goal achievement
       │   ├─► If goal met: motive_streak_counter++
       │   ├─► If sick mode: streak preserved
       │   └─► Otherwise: motive_streak_counter = 0
       ├─► Resets: motive_time_tracked_today = 0
       ├─► Resets: All categories and timer OFF
       └─► Notification: Streak status update
```

### Streak Logic Motivechart

```
                         [MIDNIGHT TRIGGER]
                                │
                                ▼
                    ┌───────────────────────┐
                    │  Is Sick Mode ON?     │
                    └───────────┬───────────┘
                                │
                  ┌─────────────┴─────────────┐
                  │                           │
                 YES                         NO
                  │                           │
                  ▼                           ▼
         ┌────────────────┐         ┌─────────────────┐
         │ Preserve Streak│         │ Goal Reached?   │
         │ Send 🏥 Notice │         │ (Progress≥100%) │
         └────────────────┘         └────────┬────────┘
                  │                           │
                  │              ┌────────────┴────────────┐
                  │              │                         │
                  │             YES                       NO
                  │              │                         │
                  │              ▼                         ▼
                  │     ┌────────────────┐       ┌────────────────┐
                  │     │ Increment      │       │ Reset Streak   │
                  │     │ Streak Counter │       │ to 0           │
                  │     │ Send 🔥 Notice │       │ Send 💔 Notice │
                  │     └────────────────┘       └────────────────┘
                  │              │                         │
                  └──────────────┴─────────────────────────┘
                                 │
                                 ▼
                    ┌─────────────────────────┐
                    │ Reset Time Tracked to 0 │
                    │ Turn OFF Sick Mode      │
                    │ Clear Active Category   │
                    │ Log to History          │
                    └─────────────────────────┘
```

---

## 💡 Usage

### Starting a Work Session

1. **Select a Category** (optional but recommended):
   - Tap one of the category buttons (Work, Privat, Coding, Design, Engineering)
   - Only one category can be active at a time

2. **Start the Timer**:
   - Toggle the Timer switch to ON
   - You'll receive a notification confirming the start

3. **Work on Your Task**:
   - The timer tracks your time automatically
   - You can see elapsed time in real-time

4. **Stop the Timer**:
   - Toggle the Timer switch to OFF
   - Time is automatically added to your daily total
   - You'll receive a notification with session details

### Daily Workmotive

1. **Morning**: Check your daily goal and adjust if needed
2. **During the Day**: Track work sessions with the timer
3. **Evening**: Check your progress towards the daily goal
4. **Midnight**: Automatic reset occurs
   - If goal reached: Streak increments
   - If sick mode: Streak preserved
   - Otherwise: Streak resets to 0

### Using Sick Mode

When you're sick or unable to work:

1. Enable **Sick Mode** toggle before midnight
2. Your streak will be preserved during the daily reset
3. Sick mode automatically turns off after midnight

### Adjusting Daily Goal (via Web UI)

**Method 1: Direct Edit**
1. Go to **Settings** → **Devices & Services** → **Entities**
2. Search for `input_number.motive_daily_goal`
3. Click on it and change the value

**Method 2: Using Scripts**
1. Go to **Settings** → **Automations & Scenes** → **Scripts**
2. Run `motive_increase_goal` or `motive_decrease_goal` (each changes by 5 minutes)

**Method 3: From Dashboard**
1. Add a Number card to your dashboard for `input_number.motive_daily_goal`
2. Adjust directly from the dashboard

---

## 🎓 Advanced Usage Examples

### Example 1: Multi-Session Workday

**Scenario:** You work in multiple sessions throughout the day.

**Workmotive:**
```
09:00 - Select "Work" category → Start Timer
10:30 - Stop Timer (90 minutes tracked)
        ✓ Progress: 75% (90/120 minutes)

11:00 - Select "Coding" category → Start Timer
12:00 - Stop Timer (60 minutes tracked)
        ✓ Progress: 125% (150/120 minutes)
        ✓ Goal Reached Notification appears!
        ✓ Streak will increment at midnight
```

**Result:** Exceeded daily goal, tracked across two different categories.

---

### Example 2: Sick Day Management

**Scenario:** You're sick after completing a 14-day streak.

**Workmotive:**
```
Morning - Feeling unwell, can't work
        - Enable "Sick Mode" toggle
        
Evening - No time tracked today (0/120 minutes)

23:59 - Daily reset approaching
00:00 - Midnight Reset Automation runs:
        ✓ Detects Sick Mode is ON
        ✓ Preserves streak at 14 days
        ✓ Notification: "🏥 Krankheitstag - Streak bleibt bei 14 Tagen"
        ✓ Sick Mode automatically turns OFF
        ✓ Ready for fresh start tomorrow
```

**Result:** Streak preserved, fair accounting for legitimate downtime.

---

### Example 3: Retroactive Time Tracking

**Scenario:** You forgot to start the timer for a 2-hour morning work session.

**Workmotive:**
```
14:00 - Realize you forgot to track morning work (09:00-11:00)
      - Go to Scripts → "motive_add_time"
      - Enter: 120 minutes
      - Click "Run"
      
      ✓ Notification: "➕ Zeit hinzugefügt - 120 Minuten"
      ✓ motive_time_tracked_today now shows 120 minutes
      ✓ Progress updated to 100%
      ✓ Goal reached notification may trigger
```

**Use Cases:**
- Forgot to start timer
- Worked offline/away from Home Assistant
- Retroactive tracking for yesterday (before midnight)
- Manual corrections for timer mistakes

---

### Example 4: Adaptive Goal Setting

**Scenario:** Fine-tuning your optimal daily goal over time.

**Week 1: Starting Conservative**
```
Monday   - Set goal: 60 min → Achieved 70 min ✓
Tuesday  - Goal: 60 min → Achieved 80 min ✓
Wednesday- Goal: 60 min → Achieved 90 min ✓
Thursday - Too easy! → Increase goal to 90 min
```

**Adjusting via Dashboard:**
```
1. Click "Ziel +5 Min" button 6 times
2. Goal increases: 60 → 90 minutes
3. New challenge begins Friday
```

**Week 2: Finding Balance**
```
Monday   - Goal: 90 min → Achieved 95 min ✓
Tuesday  - Goal: 90 min → Achieved 55 min ✗ (Streak lost)
Wednesday- Too aggressive! → Decrease goal to 75 min
```

**Result:** Personalized goal that's challenging but achievable.

---

### Example 5: Category-Based Work Analysis

**Scenario:** Understanding how you split time across work types.

**Day's Activity:**
```
09:00-10:30 │ Coding     │ 90 min │ Timer ON
10:30-11:30 │ Design     │ 60 min │ Timer ON
11:30-12:00 │ Break      │ —      │ Timer OFF
12:00-13:00 │ Engineering│ 60 min │ Timer ON
```

**Tracking Method:**
```
1. Start timer, select "Coding" → Work 90 min → Stop
2. Start timer, select "Design" → Work 60 min → Stop
3. Start timer, select "Engineering" → Work 60 min → Stop
```

**Results:**
- Total: 210 minutes tracked
- History graph shows three distinct work sessions
- sensor.motive_active_work captured each category change
- History data logs final totals

**Future Analysis:**
- View history graph to see time distribution
- Identify which work types consume most time
- Plan future days based on category patterns

---

### Example 6: Streak Recovery Strategy

**Scenario:** Lost a 30-day streak, need motivation to rebuild.

**Psychological Approach:**
```
Day 1 After Loss:
  - Set achievable goal (e.g., 60 minutes instead of 120)
  - Focus on rebuilding consistency, not duration
  - Celebrate small wins
  
Week 1: 7-day streak achieved
  - Gradually increase goal by 5-10 minutes
  
Week 2: 14-day streak achieved
  - Return to normal goal
  - Confidence restored
```

**Technical Setup:**
```
1. Use motive_decrease_goal to set realistic starting goal
2. Track progress without pressure
3. Use motive_increase_goal weekly as confidence builds
4. Enable sick_mode proactively for known busy days
```

---

### Example 7: Integration with Other Automations

**Scenario:** Automatically stop timer when leaving home.

**Create Custom Automation:**
```yaml
automation:
  - alias: "Motive - Auto Stop on Leave"
    trigger:
      - platform: state
        entity_id: person.your_name
        to: "not_home"
    condition:
      - condition: state
        entity_id: input_boolean.motive_timer_active
        state: "on"
    action:
      - service: input_boolean.turn_off
        target:
          entity_id: input_boolean.motive_timer_active
      - service: notify.mobile_app
        data:
          title: "Motive Timer Auto-Stopped"
          message: "Timer automatically stopped as you left home"
```

**Other Integration Ideas:**
- Start timer automatically when opening IDE (via IDE plugin + webhook)
- Pause timer during lunch hours
- Sync categories with calendar events
- Send daily summary to Telegram/Slack
- Create weekly reports in Google Sheets

---

### Example 8: History Data Analysis

**Scenario:** Understanding your productivity patterns over time.

**Access History Data:**
```
1. Go to Developer Tools → States
2. Find: input_text.motive_history_data
3. Copy the JSON data
```

**Example Data Structure:**
```json
{
  "2024-01-15": {"percent": 105.5, "minutes": 127, "sick": false},
  "2024-01-16": {"percent": 98.2, "minutes": 118, "sick": false},
  "2024-01-17": {"percent": 0.0, "minutes": 0, "sick": true},
  "2024-01-18": {"percent": 112.0, "minutes": 134, "sick": false}
}
```

**Manual Analysis:**
- Copy to spreadsheet
- Calculate average daily minutes
- Identify patterns (productive weekdays vs weekends)
- Count sick days used
- Track goal achievement rate

**Future Enhancement Ideas:**
- Create template sensor for weekly averages
- Build custom graph card for history visualization
- Export to CSV for deeper analysis
- Create achievement badges for milestones

---

### Example 9: Family/Team Shared Setup

**Scenario:** Multiple people using Motive in the same Home Assistant instance.

**Setup for Person 1:**
```yaml
# In motive.yaml or packages, duplicate all entities:
input_number:
  motive_daily_goal_person1:
    name: "Person 1 - Tägliches Ziel"
    # ... same config as motive_daily_goal
  
  motive_daily_goal_person2:
    name: "Person 2 - Tägliches Ziel"
    # ... same config
```

**Separate Dashboards:**
- Create "Motive - Person 1" dashboard
- Create "Motive - Person 2" dashboard
- Each dashboard uses respective entities
- Enables friendly competition and mutual motivation

**Comparison View:**
```yaml
type: horizontal-stack
cards:
  - type: tile
    entity: sensor.motive_goal_progress_person1
    name: "Person 1 Progress"
  - type: tile
    entity: sensor.motive_goal_progress_person2
    name: "Person 2 Progress"
```

---

### Example 10: Emergency Reset Workmotive

**Scenario:** Timer was accidentally left running overnight.

**Problem:**
```
Yesterday 18:00 - Started timer for "Coding"
                - Forgot to stop timer
Today 09:00     - Discover timer still running
                - 15 hours tracked (900 minutes!) ❌
```

**Solution:**
```
1. Stop the timer immediately
   → 900 minutes added to today's total (incorrect)

2. Run "motive_reset_day" script
   → Resets motive_time_tracked_today to 0
   → Clears all categories
   → Stops timer if still running

3. Manually track actual work:
   → Use motive_add_time script
   → Add correct amount (e.g., 120 minutes for yesterday)

4. Verify:
   → Check motive_time_tracked_today shows correct value
   → Streak remains intact (yesterday's data already logged)
```

**Prevention Tips:**
- Set phone reminder to check timer before bed
- Create automation to warn if timer runs > 4 hours
- Regular habit of reviewing dashboard evening

---

## 🎨 Customization

All customizations can be done through the Home Assistant Web UI!

### Adding New Categories

1. Go to **Settings** → **Devices & Services** → **Helpers**
2. Click **+ Create Helper** → **Toggle**
3. Name it following the pattern: `Motive Cat YourCategory` (entity_id will be `input_boolean.motive_cat_yourcategory`)
4. Choose an icon (e.g., `mdi:your-icon`)
5. Click **Create**

6. Update the automation:
   - Go to **Settings** → **Automations & Scenes**
   - Find "Motive - Kategorie Selection"
   - Click **Edit**
   - Add your new entity to:
     - Triggers list
     - The `category_map` variable
     - The entities list in the service call
   - Click **Save**

### Changing Reset Time

1. Go to **Settings** → **Automations & Scenes**
2. Find "Motive - Daily Reset"
3. Click **Edit**
4. Change the trigger time from `00:00:00` to your preferred time
5. Click **Save**

### Modifying Goal Increment Steps

1. Go to **Settings** → **Automations & Scenes** → **Scripts**
2. Edit `motive_increase_goal` or `motive_decrease_goal`
3. Change the value `+ 5` or `- 5` to your preferred increment
4. Click **Save**

### Customizing Notifications

1. Go to **Settings** → **Automations & Scenes**
2. Find the Motive automations
3. Edit the `persistent_notification.create` service calls
4. Modify the title and message text as desired
5. Click **Save**

---

## 🔧 Troubleshooting

### Entities Not Appearing After Restart

**Solution:**
1. Check that `motive.yaml` is in the correct location
2. Verify `configuration.yaml` has the packages section
3. Check for YAML syntax errors:
   - Go to **Settings** → **System** → **Restart**
   - Click **Check Configuration** before restarting
4. Review Home Assistant logs:
   - Go to **Settings** → **System** → **Logs**

### Timer Not Tracking Time

**Solution:**
1. Verify the timer started:
   - Check `input_datetime.motive_timer_start` has a recent timestamp
2. Check automation is enabled:
   - Go to **Settings** → **Automations & Scenes**
   - Ensure "Motive - Timer Toggle Handler" is enabled
3. Review automation logs:
   - Click on the automation → **Traces** tab

### Automations Not Triggering

**Solution:**
1. Check automations are enabled (not accidentally disabled)
2. Verify entity IDs match (all should use `motive_` prefix)
3. Check automation mode (should be `single`)
4. Review traces for errors

### Daily Reset Not Working

**Solution:**
1. Verify "Motive - Daily Reset" automation is enabled
2. Check the trigger time is correct
3. Ensure Home Assistant Docker container timezone is set correctly:
   ```bash
   docker exec -it homeassistant date
   ```
4. If timezone is wrong, update your Docker compose file:
   ```yaml
   environment:
     - TZ=Europe/Berlin  # Your timezone
   ```

### Streak Not Incrementing

**Solution:**
1. Goal must be reached (100% or more) before midnight
2. Verify `sensor.motive_goal_progress` shows >= 100
3. Check `input_number.motive_time_tracked_today` >= `input_number.motive_daily_goal`
4. Review "Motive - Daily Reset" automation traces

### Configuration Changes Not Taking Effect

**Solution:**
1. After editing YAML files, always restart Home Assistant
2. Check Configuration before restarting
3. Wait for full restart (watch the logs)
4. Clear browser cache if dashboard looks wrong

---

## 🤝 Contributing

Contributions are welcome! Here's how you can help:

1. **Report Bugs**: Open an issue with detailed description
2. **Suggest Features**: Share your ideas for improvements
3. **Submit Pull Requests**: Contribute code improvements
4. **Improve Documentation**: Help make the README clearer

### Development Guidelines

- Maintain Docker compatibility
- Ensure 100% Web UI configurability
- Test changes before submitting
- Update documentation for new features
- Follow existing code style

---

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

## 🙏 Acknowledgments

- Home Assistant Community
- [**@v-berlin**](https://github.com/v-berlin/) for the original concept
- [**@apfelFunker**](https://github.com/apfelFunker) for the more advanced version of my concept("Motive" is built on "Motive" by apfelFunker which is built on my original concept)
- All contributors and users

---

<div align="center">

**Built with ❤️ for the Home Assistant Docker Community**

[Report Bug](https://github.com/v-berlin/Motive/issues) · [Request Feature](https://github.com/v-berlin/Motive/issues)

</div>
