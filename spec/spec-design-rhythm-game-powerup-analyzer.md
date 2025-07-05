---
title: Rhythm Game Powerup Analyzer Specification
version: 1.0
date_created: 2025-07-05
owner: github-copilot-for-msa user
tags: [design, app, ocr, rhythm-game, python, opencv]
---

# Introduction

This specification defines the requirements and design for a desktop application that analyzes gameplay data from the PC rhythm game "Rift of the NecroDancer". The application captures on-screen score, hits, and beat number using OCR, then identifies and visualizes the optimal 5-second windows for activating a score-doubling powerup. The tool is intended for competitive players and streamers seeking to maximize their scores.

## 1. Purpose & Scope

The purpose of this specification is to guide the development of a Python-based desktop application that:
- Captures gameplay data from Rift of the NecroDancer via screen capture and OCR
- Allows user calibration of OCR regions for robust data extraction
- Analyzes and visualizes point density over time
- Recommends the top 3 non-overlapping 5-second windows for powerup activation
- Outputs results as a graph and logs raw data as CSV

Intended audience: developers, testers, and advanced users interested in rhythm game analytics. Assumes user has Python and required libraries installed.

## 2. Definitions

- **OCR**: Optical Character Recognition, extracting text/numbers from images
- **ROI**: Region of Interest, the area of the screen to capture for OCR
- **Beat**: The current beat number in the song, as displayed by the game
- **Score**: The player's current score, as displayed by the game
- **Hits**: The number of successful actions, as displayed by the game
- **Powerup Window**: A 5-second period during which points are doubled
- **CSV**: Comma-Separated Values, a text file format for tabular data

## 3. Requirements, Constraints & Guidelines

- **REQ-001**: The application shall allow the user to calibrate and save screen regions for OCR (score, hits, beat)
- **REQ-002**: The application shall capture screen data at a user-defined or default interval (e.g., every 100ms)
- **REQ-003**: The application shall use OCR to extract score, hits, and beat from the captured regions
- **REQ-004**: The application shall log all captured data (timestamp, beat, score, hits) to a CSV file
- **REQ-005**: The application shall analyze the data to find the top 3 non-overlapping 5-second windows with the highest point gain
- **REQ-006**: The application shall display a line graph (beat vs. score) and highlight the top 3 windows
- **REQ-007**: The application shall allow the user to re-run calibration at any time
- **REQ-008**: The application shall provide immediate OCR feedback during calibration
- **CON-001**: The application is for post-game analysis only (not real-time)
- **CON-002**: The application is intended for Windows and Linux desktop environments
- **SEC-001**: The application shall not modify or interact with the game process beyond screen capture
- **GUD-001**: Use OpenCV for region selection and display; use pytesseract for OCR
- **GUD-002**: Store calibration data in a human-readable config file (e.g., JSON)
- **GUD-003**: Log OCR confidence scores for debugging

## 4. Interfaces & Data Contracts

### Calibration Config File (JSON)
```json
{
  "score": [x, y, w, h],
  "hits": [x, y, w, h],
  "beat": [x, y, w, h]
}
```

### CSV Data Log
| timestamp | beat | score | hits |
|-----------|------|-------|------|
| float     | int  | int   | int  |

### Visualization Output
- Line graph: X-axis = beat, Y-axis = score
- Highlight: Top 3 non-overlapping 5-second windows

## 5. Acceptance Criteria

- **AC-001**: Given a user-calibrated config, when a song is played and the app is run, then the app logs all data and produces a graph with highlighted windows
- **AC-002**: The calibration tool allows the user to select and save regions, and test OCR on them
- **AC-003**: The CSV log contains all captured data with correct columns and values
- **AC-004**: The graph clearly shows beat vs. score and highlights the correct windows
- **AC-005**: The app does not interfere with the game process

## 6. Test Automation Strategy

- **Test Levels**: Unit (OCR, data parsing), Integration (screen capture + OCR), End-to-End (full workflow)
- **Frameworks**: pytest, unittest, OpenCV test utilities
- **Test Data Management**: Use sample screenshots and mock data for repeatable tests
- **CI/CD Integration**: Optional, recommend GitHub Actions for linting and test automation
- **Coverage Requirements**: Minimum 80% code coverage for core logic
- **Performance Testing**: Ensure capture and OCR loop does not drop frames at target interval

## 7. Rationale & Context

- Calibration is required due to possible UI changes, slanted text, and backgrounds
- OpenCV and pytesseract are chosen for their simplicity and cross-platform support
- Post-game analysis avoids real-time complexity and anti-cheat concerns
- CSV logging and config files make the tool portable and user-friendly

## 8. Dependencies & External Integrations

### External Systems
- **EXT-001**: Rift of the NecroDancer (PC game) - source of visual data

### Third-Party Services
- **SVC-001**: None (all processing is local)

### Infrastructure Dependencies
- **INF-001**: Desktop OS (Windows or Linux) with Python 3.x

### Data Dependencies
- **DAT-001**: Game screen output; user-calibrated regions

### Technology Platform Dependencies
- **PLT-001**: Python 3.8+; OpenCV; pytesseract; matplotlib; numpy; pandas

### Compliance Dependencies
- **COM-001**: None

## 9. Examples & Edge Cases

```python
# Example calibration config
{
  "score": [100, 200, 80, 40],
  "hits": [200, 200, 60, 40],
  "beat": [300, 200, 60, 40]
}

# Example CSV row
1688571234.123, 42, 123456, 99

# Edge case: OCR fails to read a value
# Log as empty or previous value, and log confidence score for debugging
```

## 10. Validation Criteria

- All requirements in section 3 are met
- Calibration tool works for different screen resolutions
- OCR accuracy is sufficient for typical game fonts and backgrounds
- Data log and graph output match expected format and content

## 11. Related Specifications / Further Reading

- [OpenCV Documentation](https://docs.opencv.org/)
- [pytesseract Documentation](https://pypi.org/project/pytesseract/)
- [matplotlib Documentation](https://matplotlib.org/)
- [Rift of the NecroDancer](https://store.steampowered.com/app/247080/Rift_of_the_NecroDancer/)
