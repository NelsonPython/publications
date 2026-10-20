## Cybersecurity Maturity Model Certification (CMMC)

The [CMMC Assessment Guide Level 2](https://dodcio.defense.gov/Portals/0/Documents/CMMC/AssessmentGuideL2v2.pdf) was issued in September 2024 and government contractors, such as Crane Aerospace, began compliance projects.  As a technical writer, I was tasked with developing templates for documenting compliance with each requirement.  

When I read the guide, I realized that the content had been standardized so that each section was organized first by domain and then by individual security requirements.  The description of each requirement contained the same elements.  This made it possible for me to write a python script that parsed the content and generated templates in HTML format.  Not only did this save time, it improved accuracy and completeness.  

About the same time, I was working on another project called "Gearing up for Compliance".  Graphics of wheels and gears inspired this easy-to-read user interface that provides quick access to each template.

<img src="cmmc_circle.png" width="600" alt="CMMC User Interface">

Users click on a piece of the CMMC pie to drill down to the requirements.  Then, click on a link to select a template.

<img src="access_control.png" width="600" alt="CMMC Access Controls">

<img src="ac_template.png" width="600" alt="CMMC Access Control Template">


*CMMC 2.0 was scheduled to begin in November 2026, but it has been suspended while a comprehensive review of the program is undertaken [1].* 

---

References


[1] [About CMMC](https://dodcio.defense.gov/cmmc/About/)
