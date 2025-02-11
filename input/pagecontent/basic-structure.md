# Core Implementation Model for Clinical Study Schedule of Activities (SoA)

The Schedule of Activities (SoA) forms a key part of a Clinical Trial Protocol; it is usually presented as a table with the columns detailing planned study 'visits' (Encounters) and the rows the details of study activities (Activities) that are to be undertaken at that visit, and they are key to understanding how the objectives of the study are to be implemented. The table below shows a typical example SoA:

<img src="lzzt_soa.png" alt="H2Q-LZZT-MC Schedule of Activities" width="1000px" style="float:none; margin: 0px 0px 0px 0px;" />

* The Encounters/Visits (any planned event corresponding to clinical data collection) are represented along the horizontal axis
* The Activities (the planned activities expected to occur at the event) are represented vertically
* ... with the required Activities at each Encounter shown by a cross (X) in the matrix.

The SoA in this form represents the basic activities required at each Encounter and is usually accompianed by the details of the timings of the events (weeks) and an Encounter identifier (in the example the visit numbers... V1, V2... etc.)

The SoA table is then used as the basic building block for configuring the necessary tools and applications required to manage the progress of study participants during the study. This includes detailing actvities such as when study interventions are to occur (e.g drug adminstration), and when required study evaluations are be made and the data collected and recorded.  

These SoA specifications form the basis for developing study specific Case Report Forms (CRFs): the primary data collection instrument.  Direct data collection and collation approaches such as the use of medical devices (Sensor Informed trials), or  remote study management (e.g. Decentralised Trials), and direct data transfer from service providers such as central laboratories require confirmed syntactic, structural and semantic interoperability.     

The FHIR standard (Fast Healthcare Interoperability Resources) is designed to support the exchange of electronic health records. The 'Definitional Artifacts' section of the 'Specialized' FHIR Resources category provides a set of FHIR Resources that are designed to enable research specifications such as those in a SoA to be represented in a standard format that can consumed by  eHR systems to plan and execute research protocols directly. 

---

The key consideration for the SoA is the collection of data to support the hypotheses presented in the Protocol. The CRFs used in Clinical Research are the primary interface for recording **outcomes** of activities.  They are designed to record the sets of data the study requires, and usually takes little account of the practical activities required to obtain the data. For FHIR specificied SoAs to be succesful when requirements are transcribed from the SoA for eHR implementation, a more 'workflow-based' approach to data management is required.  Specically, transcription needs to be able to clearly define:  

* **what** activities are being done
* **when** are they being done
* the **outcomes** from those activities, and potentially

* **who** is to perform/undertake the activities

# Implementing a SoA using FHIR Resources

In order to represent the SoA we needed a set of definitional FHIR resources to represent the planned activities and the relationships between them.  The work to date has focused on a Workflow view of data management, and has leveraged the existing FHIR workflow patterns to achieve this.  

SoAs do not (a) stand alone - they are study specific, and (b) describe planned activites expected to be executed for each [Patient](http://hl7.org/fhir/Patient) recuited to the study.  The two [Research--] resources have been used to associate SoA with the correct study and to recognise patients as being study participants:

* [ResearchStudy](http://hl7.org/fhir/ResearchStudy) - a representational artifact for a Research Study
* [ResearchSubject](http://hl7.org/fhir/ResearchSubject) - an artifact used to link a Patient research to the ResearchStudy

The project has also restricted its investigations and SoA IG development to using the [PlanDefinition](http://hl7.org/fhir/PlanDefinition) and [ActivityDefinition](http://hl7.org/fhir/ActivityDefinition). Other Structured Data Collection resources (e.g. Questionnaire, EventDefinition, etc.) have not been systematically investigated for their potential to define SoA requirements. The Workflow resources are defined on the [FHIR Workflow](https://hl7.org/fhir/workflow.html) page; the key points for modeling a SoA deal with the [Definition](https://hl7.org/fhir/workflow.html#definition) and [Request](https://hl7.org/fhir/workflow.html#request) patterns.

The Definitional Artifacts have been used to create the structure - schedule and activities - to support bot study SoA specification (definitions) and planned contextualization. 

The basic structure is summarized in the following diagram:

{% include basic-structure.svg %}

The high-level points are as follows:

* The **ResearchStudy** has a single _protocol_  reference to a __PlanDefinition__ for the Study Definition; under which all study activities are grouped
* Each of the activities are defined using separate resources
* Each activity is linked to the parent Study Definition through the _action_ predicate.  The _action_ predicate:  
  * defines the relationships between the activities, at the least when activities should occur relative to each other 

{% include plandef-actions.svg %}

In Research protocols many of the activities are event based. For example;  informed consent is required prior to any study activities commencing.  This is modeled using the _relatedAction_ predicate on the _action_ predicate. This is illustrated in the above diagram.  The `Baseline` Encounter is annotated in the design.  The other encounters are defined as being _before_ or _after_ this action.  It is also possible to define the interval between these actions as illustrated here (using FSH syntax - INSERT LINK TO FSH LINK):

```
Instance: H2Q-MC-LZZT-ProtocolDesign
InstanceOf: PlanDefinition
Description: "H2Q-MC-LZZT-Protocol Schedule of Activities"
Usage: #example
* status = #active
* version = "LZZT_1"
...
* action[+].definitionUri = "PlanDefinition/H2Q-MC-LZZT-Study-Visit-3"
* action[=].id = "Index-Activity-Event"
* action[=].title = "Visit-3"
* action[=].description = "Planned Visit [Visit-3]"
* action[+].definitionUri = "PlanDefinition/H2Q-MC-LZZT-Study-Visit-4"
* action[=].title = "Visit-4"
* action[=].description = "Planned Visit [Visit-4]"
* action[=].relatedAction[+].actionId = "Index-Activity-Event"
* action[=].relatedAction[=].relationship = #after
* action[=].relatedAction[=].offsetRange.low.value = 12
* action[=].relatedAction[=].offsetRange.low.code = #d
* action[=].relatedAction[=].offsetRange.high.value = 15
* action[=].relatedAction[=].offsetRange.high.code = #d
```
The Event `Visit-3` has an _id_ assigned to it. Prior and subsequent activities use the `relatedAction.actionId` to reference the pivot activity.  This allows elementary sequencing of planned elements to represent the SoA.  This sample also represents a simple windowing capability for scheduling activities relative to the index event.  There is a separate use case to address how targetted windows can be applied to the scheduling of study activities.

# Observations on how the SoA can be implemented

Given the structure discussed above, consideration has been given to how best to implement/use the planned activities within an Electronic Healthcare Record System; particularly how they can be used to manage the progress of a [ResearchSubject](http://hl7.org/fhir/ResearchSubject) through the study.   

Following the FHIR Workflow paradigm, what needs to be done has been defined using the [Definition](https://hl7.org/fhir/definition.html) patterns, which are scheduled using [Request](https://hl7.org/fhir/request.html) patterns and ultimately manifested as [Event](https://hl7.org/fhir/event.html) patterns.   The current implementation examples are constrained to the minimum requirement in order that the core concepts in a clinical trial protocol are accurately modelled

The example below shows how a defined study plan can be related to an executed activity and the required outcomes/results.

{% include implementation-plan-def.svg %}

While the diagram may look complex it is methodologically sound as it builds out an approach using a set of common archetypes.  There is a flow of planned -> scheduled -> performed activities that IG implementers can follow to build processes and software meeting the SoA objectives.  This serves as a way to drive data collection (for both prospective and retrospective use cases) and it provides a broad scope for current and future use cases.

# Alignment between the CDISC Operational Data Model (ODM) and the FHIR SoA Model

The CDISC Operational Data Model (ODM) structure is a popular model for representing planned activities in Clinical Data Management Systems (CDMS, EDC, etc.) and Clinical Trial Management System (CTMS).  It is a common way for exchanging data and metadata between data management systems. A primer on the ODM can be found as part of the specification [CDISC-ODM](cdisc-odm.html), and links to the CDISC site where full details of the model can be found on that page.

Having a standard alignment between the ODM and FHIR would enable rapid and reproducible system builds using standardised interfaces. Whether the initial study design is developed in the ODM/XML formats or using FHIR Resources, each EHR system should then be able to use the study design as part of study start up activities by a Study Builder.  There will need to be some augmentation of the process to make best use of transportable concepts (e.g., test codes, procedure codes, etc.) but it is hoped the work here will be able to be used as a core implementation model.  
 
The following ODM/XML to FHIR Resource high level mappings have been adopted:

* Map _Protocol_ to _PlanDefinition_
* Map _StudyEvent_ to _PlanDefinition_
* Map _FormDef_ to _ActivityDefinition_

The intention here is to use the _PlanDefinition_ and _ActivityDefinition_ that transcribe to the corresponding Object Identifiers (OIDs) of the ODM/XML elements.  This will be valuable for reconciling study elements.  It is important to stress that there will never going to be a complete overlap as their respecitve use cases differ. Shared labelling between the model platforms can be built.