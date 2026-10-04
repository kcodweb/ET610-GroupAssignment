# Data

Raw data is not stored in this repository. Place the files here before running the pipeline.

## OULAD → `data/oulad/`

Download the Open University Learning Analytics Dataset (Kuzilek et al., 2017, CC BY 4.0) from
https://analyse.kmi.open.ac.uk/open_dataset and extract these seven files into `data/oulad/`:

`assessments.csv`, `courses.csv`, `studentAssessment.csv`, `studentInfo.csv`,
`studentRegistration.csv`, `studentVle.csv`, `vle.csv`

## Moodle → `data/moodle/`

`MOODLE_Data.csv` — the course interaction log (IT-2507) provided for the ET 610 assignment.
Expected columns: `Time`, `User ID`, `Event context`, `Component`, `Event name`, `Description`, `Content code`.
