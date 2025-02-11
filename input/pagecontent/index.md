# Background

## Vulcan Schedule of Activities (SoA) Project

The core of this project is to define a usable pattern for a Clinical Trial Schedule of Activities structure using FHIR Resources and Processes, such that:
* it can be shared
* it can be interpreted, and 
* it can be implemented in healthcare systems (such as EHR or PHR systems)  

The conduct of Clinical Trials are guided by the International Conference on Harmonization (ICH) guidelines for Good Clinical Practice (GCP) E6 (R2).[^1]  Core to this is the writing of a Clinical Trial Protocol, a document intended to describe the objectives, design, methodology, statistical considerations and aspects related to the organization of the clinical trial. 

Trial protocols provide the background and rationale for conducting a study, highlighting specific research questions that are addressed, and taking into consideration ethical issues. Trial protocols must meet a standard that adheres to the principles of Good Clinical Practice (as mentioned above), and are used to obtain ethics approval by local Ethics Committees or Institutional Review Boards.

The Clinical Trial Protocol incorporates all the aspects of what is needed to define how the study is to be conducted and reviewed; for the purposes of this first iteration of the Implementation Guide we are constraining the scope to focus just on the elements incorporated in the Schedule of Activities.

### What is the Schedule of Activities?

The CDISC-GLOSS definition of the Schedule of Activities is[^2]: 

> A standardized representation of planned clinical trial activities including interventions (e.g., administering drug, surgery) and study administrative activities (e.g., obtaining informed consent, distributing clinical trial material and diaries, randomization) as well as assessments. See also schedule of assessments. Compare to study design schematic.

It is a focused section of the Protocol Document that lays out what activities are expected, and structurally when they are expected to occur.  It is a design that is interpreted by the stakeholders to drive how the study is defined, planned and performed.

## Vulcan SoA Implementation Guide (IG)

This IG is intended to provide a roadmap for adopters looking to use FHIR resources in order to support the planning and implementing Clinical Research designs.  The Schedule of Activities is defined ​in a clinical trial as a standardised representation of planned activities, including interventions (e.g., administering drug, surgery), studying administrative activities (e.g., obtaining informed consent, distributing clinical trial material and diaries, randomisation) and assessments.​

​The purpose of the Schedule of Activities is to communicate the scheduling of the planned activities required to support the research objectives of the study to enable operational implementation.​

Within the Vulcan SoA project we have considered two principal study types:
* Prospective – e.g., randomised clinical trials (RCT), concurrent cohort studies, observational studies, epidemiology, real world evidence (RWE) 
* Retrospective – e.g., cohort studies, case-control studies, real world evidence

Examples of positive outcomes for this project include:
* Participant identification – feasibility , inclusion/exclusion criteria (prospective/retrospective)​
* Participant management – appointment scheduling (prospective)​
* Real world evidence – research data selection (retrospective)​
* Audit/Inspection – regulatory oversight period identification ​
* Resource planning (prospective)​
* Logistics planning (prospective)​

This iteration for the project is designed to be directional purely - as we learn more about how the different systems work and can be interfaced. We will continue to improve the IG to accommodate more use cases, iteratively working towards a solution that will be both functional and scalable.

## References
[^1]: https://database.ich.org/sites/default/files/E6_R2_Addendum.pdf
[^2]: https://ncimetathesaurus.nci.nih.gov/ncimbrowser/ConceptReport.jsp?dictionary=NCI%20Metathesaurus&code=CL521320