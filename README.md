# Motive for Home Assistant

<div align="center">

![Home Assistant](https://img.shields.io/badge/home%20assistant-%2341BDF5.svg?style=for-the-badge&logo=home-assistant&logoColor=white)
![Version](https://img.shields.io/badge/version-1.0.0-blue?style=for-the-badge)
![License](https://img.shields.io/badge/license-MIT-green?style=for-the-badge)

**Transform your productivity into measurable goals.**

A beautifully designed productivity tracking system for Home Assistant that turns time into achievements.

[Features](#✨-features) • [Installation](#📦-installation) • [Setup](#🚀-setup) • [Usage](#💡-usage) • [Screenshots](#📸-screenshots)

</div>

---

## ✨ Features

### Core Functionality
- ⏱️ **Smart Time Tracking** - Track your productive time with an intelligent timer system
- 🎯 **Dynamic Daily Goals** - AI-powered goal setting based on your task list
- 🔥 **Streak System** - Build momentum with consecutive day tracking
- 🏷️ **Category Management** - Organize work by categories (Work, Personal, Coding, Design, Engineering)

### Smart Features
- 🤖 **AI Goal Assistant** - Automatically adjusts daily goals based on your todo list
- 🏥 **Sick Mode** - Pause your streak without losing progress when you're unwell
- 📈 **Progress Dashboard** - Real-time visualization of your daily achievement
- 🔔 **Smart Notifications** - Celebrate wins and gentle reminders
- 📱 **Mobile Ready** - Full control from your phone

## 📦 Installation

### Prerequisites

Ensure you have the following integrations installed:

- **Home Assistant** 2024.1.0 or newer
- **HACS** (for custom cards)
- **Your favourite AI-Integration to Update your daily goal based on a HASS ToDo-List** (optional, for AI features)

### Required HACS Components

Install through HACS → Frontend:

```yaml
- Lovelace
- Mushroom-Cards
```

## 🚀 Setup

### Step 1: Enable Packages

Add to your `configuration.yaml`:

```yaml
homeassistant:
  packages: !include_dir_named packages
```

### Step 2: Create Package Structure

Create the following directory structure in your config folder:

```
config/
├── configuration.yaml
├── automations.yaml
├── scripts.yaml
└── packages/
    └── motive.yaml
```

### Step 3: Add Motive Package

Copy the provided `motive.yaml` to your `packages/` directory.

### Step 4: Add Automations

Add the Motive automations to your `automations.yaml` with unique IDs:

```yaml
- id: 'd9872d0607fe426f9b999f3691acdfa2'
  alias: "Motive - Timer Toggle Handler"
  # ... automation content
```

### Step 5: Add Scripts

Add the control scripts to your `scripts.yaml`:

```yaml
motive_increase_goal:
  alias: "Motive - Increase Goal"
  # ... script content
```

### Step 6: Restart Home Assistant

```bash
ha core restart
```

## 💡 Usage

### Dashboard Setup

Create a new dashboard view with this configuration:

```yaml
title: Productivity
icon: mdi:target
cards:
  # Progress Bar
  - type: tile
    entity: sensor.motive_goal_progress
    name: Daily Progress
    color: green
    
  # Goal Adjuster
  - type: entities
    entities:
      - entity: input_number.motive_daily_goal
        name: Daily Goal (minutes)
        
  # Timer Control
  - type: button
    entity: input_boolean.motive_timer_active
    name: Start/Stop Timer
    
  # Category Selection
  - type: grid
    cards:
      - type: button
        entity: input_boolean.motive_cat_bega
        name: BEGA
      - type: button
        entity: input_boolean.motive_cat_privat
        name: Private
```

### Quick Start

1. **Set Your Daily Goal**
   - Navigate to Settings section
   - Adjust daily goal with +/- buttons (5-minute increments)

2. **Start Tracking**
   - Select a category (BEGA, Private, Coding, etc.)
   - Tap the timer to start tracking
   - Timer automatically saves progress when stopped

3. **Monitor Progress**
   - View real-time progress on the main dashboard
   - Check your streak counter
   - Review your productivity heatmap

4. **Sick Days**
   - Enable "Sick Mode" to pause without breaking your streak

## 📊 Entities Reference

### Input Numbers
- `input_number.motive_daily_goal` - Your daily goal in minutes
- `input_number.motive_time_tracked_today` - Today's tracked time
- `input_number.motive_streak_counter` - Current streak days
- `input_number.motive_daily_achievement` - Daily achievement percentage

### Sensors
- `sensor.motive_goal_progress` - Current progress percentage
- `sensor.motive_status` - Current working status
- `sensor.motive_active_work` - Active category when timer running
- `sensor.motive_time_to_goal` - Remaining time to reach goal

### Controls
- `input_boolean.motive_timer_active` - Start/stop timer
- `input_boolean.motive_sick_mode` - Enable sick mode
- `input_boolean.motive_cat_*` - Category selections

## 🎨 Customization

### Adjust Goal Increments

In `scripts.yaml`, modify the increment value:

```yaml
value: "{{ [states('input_number.motive_daily_goal') | int + 10, 1440] | min }}"
#                                                            ^ Change this
```

### Modify Categories

Add new categories in `motive.yaml`:

```yaml
input_boolean:
  motive_cat_yourcategory:
    name: "Your Category"
    icon: mdi:your-icon
```

### Change Reset Time

Modify the daily reset automation trigger:

```yaml
trigger:
  - platform: time
    at: "00:00:00"  # Change to your preferred reset time
```

## 🤝 Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🙏 Acknowledgments

- Home Assistant Community
- HACS Developers

---

<div align="center">

**Built with ❤️ for the Home Assistant Community**

</div>

