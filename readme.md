# AIUB Social Welfare Club - SHOMOY

**Student Availability Tracker**

SHOMOY is a fully client-side web application built for the AIUB Social Welfare Club to quickly find available volunteers based on their class schedules. It processes Excel files exported from Google Forms responses, parses class timings, and lets coordinators filter students by day and required free time slots.

Try it out [here](https://tariqulislamrahat.github.io/shomoy/)

## Overview

The app runs entirely in the browser – no server, no backend, no data transmission. After uploading an Excel file containing student schedules, the data is stored locally in the browser's `localStorage`. Coordinators can then:

- Select a day (Sunday to Thursday)
- Choose one or more hourly time slots
- Search by name, ID, or department
- View instantly which students are free during the selected period

## Features

- Upload and parse `.xlsx` files from Google Forms
- Filter by day, multiple time slots, and search query
- Visual availability indicators (free all day, partially free, busy all day)
- Detailed student modal with full weekly schedule grid
- Local data persistence across sessions
- Replace or clear data via settings
- Fully responsive (desktop and mobile)
- Minimal dependencies (only SheetJS from CDN)

## How It Works

1. Club members submit their class schedules (busy times) via a Google Form.
2. Responses are exported as an Excel file.
3. The coordinator uploads the file to SHOMOY.
4. The app converts class time ranges into busy hourly slots.
5. Filtering shows only students who are free during all selected slots on the chosen day.

## Required Excel Format

The Excel file must have columns in this exact order:

1. Name
2. Student ID
3. Phone Number
4. Department
5. Sunday
6. Monday
7. Tuesday
8. Wednesday
9. Thursday

Each day column should contain class times (when the student is busy), for example:

`10:10 AM – 12:10 PM, 2:20 PM – 4:20 PM`

or

`No classes`

or left empty (treated as fully free).

Supported time formats:
- `8:00 AM - 10:00 AM`
- `12:10 PM – 2:10 PM`
- Multiple ranges separated by commas
- Dashes: `-` or `–` (en-dash)

Phone numbers: 11-digit Bangladeshi numbers. The app automatically adds a leading `0` if the number has 10 digits.

## Current Time Slots

The app uses fixed hourly slots starting at:

- 8:00 AM
- 9:00 AM
- 10:00 AM
- 11:00 AM
- 12:00 PM
- 1:00 PM
- 2:00 PM
- 3:00 PM
- 4:00 PM

A slot is marked busy if any class overlaps with it.

## Customizing Time Slots

To change or add time slots, edit the following constants near the top of the `<script>` section in `index.html`:

```javascript
const TIME_SLOTS = ['8:00', '9:00', '10:00', '11:00', '12:00', '1:00', '2:00', '3:00', '4:00'];
const TIME_LABELS = ['8:00 AM', '9:00 AM', '10:00 AM', '11:00 AM', '12:00 PM', '1:00 PM', '2:00 PM', '3:00 PM', '4:00 PM'];
```

- `TIME_SLOTS`: Internal keys used in schedule objects (keep as 24-hour strings without AM/PM)
- `TIME_LABELS`: Display text shown in buttons and modal

Example: To add a 5:00 PM slot:

```javascript
const TIME_SLOTS = ['8:00', '9:00', '10:00', '11:00', '12:00', '1:00', '2:00', '3:00', '4:00', '5:00'];
const TIME_LABELS = ['8:00 AM', '9:00 AM', '10:00 AM', '11:00 AM', '12:00 PM', '1:00 PM', '2:00 PM', '3:00 PM', '4:00 PM', '5:00 PM'];
```

You must also update the overlap detection logic in `markBusySlots()`:

```javascript
const slotMapping = [
    { key: '8:00', start: 8, end: 9 },
    { key: '9:00', start: 9, end: 10 },
    // ... existing entries
    { key: '4:00', start: 16, end: 17 },
    { key: '5:00', start: 17, end: 18 }  // new entry
];
```

For finer granularity (e.g., 30-minute slots), add more entries accordingly.

## Adding or Changing Days

Current days are defined by:

```javascript
const DAYS = ['sunday', 'monday', 'tuesday', 'wednesday', 'thursday'];
```

To add Friday:

1. Update `DAYS` array:

```javascript
const DAYS = ['sunday', 'monday', 'tuesday', 'wednesday', 'thursday', 'friday'];
```

2. Add a corresponding button in the HTML day selector (inside `.day-selector`):

```html
<button class="day-button" data-day="friday">Friday</button>
```

3. Ensure your Excel file has a "Friday" column (in position J, column 10).
4. Update the parsing loop in `processFile()` to include the new column index:

```javascript
schedule: {
    sunday: parseDaySchedule(row[4] || ''),
    monday: parseDaySchedule(row[5] || ''),
    // ... existing
    thursday: parseDaySchedule(row[8] || ''),
    friday: parseDaySchedule(row[9] || '')  // new
}
```

5. In the student modal, add a new day section:

```html
<div class="day-schedule" id="fridaySchedule">
    <h4>Friday</h4>
    <div class="schedule-slots" id="fridaySlots"></div>
</div>
```

And update the `openStudentModal()` loop to include `'friday'`.

## Usage Instructions

1. Open the live demo or your local copy of `index.html`
2. Drag and drop or select your Excel file
3. Select a day
4. (Optional) Click time slot buttons to require availability during those hours
5. (Optional) Use search bar to filter by name/ID/department
6. Click any student card for full details
7. Use the settings gear (top right) to replace or clear data

## Data Privacy

- All processing happens in your browser
- No data is uploaded or shared
- Data stored only in `localStorage` on your device
- Clear data via settings when using shared computers

## Limitations

- Only supports days defined in `DAYS` array (currently Sunday–Thursday)
- Fixed time slots (customizable with code changes)
- No automatic merging of duplicate student entries
- No export functionality

## Deployment

- Host on GitHub Pages (as currently done)
- Or simply open `index.html` locally
- No build step required

## License

MIT License – free to use, modify, and distribute.

Developed by [Tariqul Islam](https://www.facebook.com/tariqulislaamrahat/) for AIUB Social Welfare Club.
