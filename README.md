# WFP Schedule Planner

A single-file, interactive HTML tool for planning and visualizing bid period schedules, handover dates, and related events for workforce planning (WFP).

## Features
- **Modern UI**: Responsive, clean interface using Tailwind CSS and Google Fonts.
- **Bid Period Management**: Add, edit, and delete bid periods with automatic date calculations.
- **Customizable Calculation Rules**: Adjust SP cutoff rules, date offsets, and weekend/holiday handling.
- **Public Holidays**: Configure Melbourne public holidays for accurate working day calculations.
- **Calendar & Table Views**: Visualize all events in a color-coded calendar grid and sortable table.
- **Event Types**: Tracks SP Cutoff, SP Feedback, Trade Off, SSIM Handover, Tech/Cabin Crew Publish, Training Handover, CB Pairing Publish, Bidding Closure, Roster Build Commence, and more.
- **Color & Label Customization**: Change event colors and display names in settings.
- **Export**: Download CSV of all or visible bid periods.
- **Persistent Settings**: All data and settings are saved in your browser's local storage.

## Usage
1. Open `planning-dates-calculator.html` in any modern web browser.
2. Use the **Settings** (gear icon) to configure calculation parameters, public holidays, event labels, and colors.
3. Add new bid periods manually or use the auto-add feature for the next period.
4. View and manage all periods in the table and calendar overview.
5. Export data as CSV for reporting or backup.

## Customization
- **Calculation Parameters**: Adjust offsets and rules for each event in Settings > Calculation Parameters.
- **Public Holidays**: Add or edit holidays in Settings > Public Holidays (format: `YYYY-MM-DD Holiday Name`).
- **Event Display Names & Colors**: Change in Settings > Calendar.
- **Roster Definitions**: View standard roster periods and add new bid periods in Settings.

## Technical Notes
- All logic and UI are contained in a single HTML file. No server or build step required.
- Data is stored in browser local storage. Clearing browser data will remove your saved periods and settings.
- Designed for use by workforce planners, roster managers, and similar roles.

## License
This project is provided as-is for internal planning use. No warranty or support is provided.

## Event Types Explained
| Event Type                | Description                                                      |
|--------------------------|------------------------------------------------------------------|
| SP Cutoff                | Date when the bid period closes for submissions.                  |
| SP Feedback              | Date for providing feedback after SP Cutoff.                      |
| Trade Off Discussion     | Date for trade-off discussions.                                   |
| Final SSIM Handover      | Date when the final SSIM handover occurs.                         |
| Tech Crew Publish        | Date when the technical crew roster is published.                 |
| Cabin Crew Publish       | Date when the cabin crew roster is published.                     |
| Training Handover        | Date for training handover events.                                |
| CB Pairing Publish       | Date for publishing crew base pairings.                           |
| Bidding Closure          | Date when bidding closes for a period.                            |
| Roster Build Commence    | Date when roster building starts.                                 |
| Public Holiday           | Melbourne public holidays (affect working day calculations).      |

## Settings Reference
- **Calculation Parameters**: Set rules for how each event date is calculated (offsets, weekend/holiday handling).
- **Public Holidays**: Enter holidays in `YYYY-MM-DD Holiday Name` format (one per line).
- **Calendar**: Change event display names and colors.
- **Roster Definitions**: View and manage standard roster periods and add new bid periods.

## Data Storage & Privacy
- All data and settings are stored in your browser's local storage.
- To back up your data, use the CSV export feature.
- Clearing browser data or using a different browser/device will reset your data.

## Troubleshooting / FAQ
- **My data disappeared!**
  - Data is stored in your browser. If you clear browser storage or use a different device, your data will not persist.
- **How do I reset all settings?**
  - Clear your browser's local storage for this site.
- **How do I export/import data?**
  - Use the Export CSV feature to back up your data. Import is not currently supported.
- **Why are some dates not calculated as expected?**
  - Check your calculation parameters and public holiday list in Settings.

## Customization Examples
- To change the SP Cutoff rule to the 5th of the month, set "Day of Month" to 5 in Settings > Calculation Parameters.
- To add a new public holiday, enter a new line in Settings > Public Holidays, e.g., `2025-12-31 New Year's Eve`.
- To change the color for "Tech Crew Publish", go to Settings > Calendar and select a new color.

## Contributing
Contributions, bug reports, and feature requests are welcome! Please open an issue or submit a pull request.

## Changelog
- **2025-05-21**: Initial public release with full event customization, calendar/table views, and CSV export.

## Contact / Credits
Developed by the WFP team. For questions or feedback, please contact your internal support channel.

