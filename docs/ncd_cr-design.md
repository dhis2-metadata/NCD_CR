# Cancer Registry Module - System Design Guide { #ncd-cr-design }

## Introduction

The DHIS2 Toolkit for Cancer Registry is based on internationally recognised standards and principles for population-based cancer registration, as defined by the International Agency for Research on Cancer (IARC) in [*Cancer Registration: Principles and Methods*](https://publications.iarc.who.int/Book-And-Report-Series/Iarc-Scientific-Publications/Cancer-Incidence-In-Five-Continents-Volume-IX-2007) (IARC Scientific Publications No. 160, 2021) and operationalised through the CanReg5 software developed by IARC.This toolkit includes a DHIS2 Tracker program aligned with CanReg5 data standards for individual-level cancer case registration, supporting population-based monitoring at registry level, as well as a custom DHIS2 application to export Tracker program data to CanReg5 format.
The Cancer Registry toolkit is designed to support population-based cancer registries in strengthening their routine data management processes and improving the quality, completeness, and timeliness of cancer case registration. The DHIS2 Cancer Registry tracker is not designed to provide clinical decision-support, but rather serves as an operational tool for individual-level case capture and a source of data for cancer surveillance and epidemiological analysis. It is aligned with CanReg5 data standards for individual-level cancer case registration, supporting population-based monitoring at registry level.
This system design document explains the reference configuration in DHIS2 for the cancer registry use case, including a detailed description of the DHIS2 Tracker configuration and data quality control mechanisms. This document does not address the resources and infrastructure needed to implement such a system (such as servers, power, internet connectivity, backups, training, and user support) which are covered in the **DHIS2 Tracker Implementation Guide**.
Reference metadata for this toolkit is available at: [dhis2.org/metadata-downloads](https://dhis2.org/metadata-downloads/).

### Acknowledgements

The DHIS2 Toolkit for Cancer Registry has been developed with financial support from Vital Strategies and technical guidance from the International Agency for Research on Cancer (IARC). We are grateful to IARC for providing subject matter expertise in cancer registration standards and the CanReg5 data model throughout the design and development of these tools. We would also like to thank the HISP Groups who participated in the consultations and contributed their implementation experience to the development of this toolkit.

## System Design Overview

### Background

Cancer represents one of the leading causes of morbidity and mortality worldwide, with the global burden increasingly concentrated in low- and middle-income countries (LMICs) where health systems are often least equipped to respond. Reliable, high-quality data on cancer incidence, case characteristics, and outcomes are essential for informing national cancer control planning, allocating resources, monitoring trends over time, and evaluating the impact of prevention and treatment programmes. Population-based cancer registries are the primary instrument for generating this evidence, and their systematic strengthening is a priority for global cancer control.

Individual-level cancer case registration offers significant advantages over aggregate reporting. A case-based approach allows for flexible disaggregation by tumour site, morphology, age, sex, geographic origin, and other clinically and epidemiologically relevant variables. It also enables longitudinal follow-up of registered cases, supports deduplication of records across multiple reporting sources, and facilitates the application of systematic data quality checks to identify and correct incomplete or inconsistent entries. These capabilities are critical in the cancer registry context, where cases are typically notified from multiple sources — hospitals, pathology laboratories, death certificates — and must be consolidated into a single, verified record.

Despite their importance, population-based cancer registries in many LMICs face persistent challenges in data completeness, timeliness, and sustainability. Registry operations often rely on fragmented paper-based systems or standalone software tools that are difficult to maintain, integrate poorly with national health information systems, and require significant technical capacity to operate. [CanReg5](http://www.iacr.com.fr/index.php?option=com_content&view=article&id=9:canreg5&catid=68&Itemid=445), developed by IARC, has become the internationally recognised standard software for population-based cancer registration and provides a well-established data model and set of data quality control procedures. However, its integration into broader national digital health infrastructure — including routine health information systems managed by Ministries of Health — remains limited in many settings.

The DHIS2 Cancer Registry tracker is designed to address these challenges by leveraging the DHIS2 platform — already widely deployed in national health information systems across LMICs — to support individual-level cancer case registration aligned with CanReg5 data standards. By implementing cancer registration within DHIS2, this toolkit aims to facilitate the integration of cancer surveillance data into national health information infrastructure, reduce duplication of data management efforts, and support systematic data quality control at the point of registration.

### Use case

The DHIS2 Cancer Registry toolkit is designed to support routine individual-level cancer case registration to feed into population-based cancer registries. The system is built around the DHIS2 Tracker data model, aligned with the data standards and workflows of CanReg5, the internationally recognised software for population-based cancer registration developed by IARC.

The web-based data capture component of the system design allows registry staff to record and manage individual cancer cases drawn from multiple notification sources — including hospital records, pathology and cytology laboratory reports, and death certificates — and consolidate them into a single verified registry record. The tracker program supports the capture of core data elements required for population-based cancer registration, including patient demographics, tumour characteristics, basis of diagnosis, and source of notification.

A key feature of the system design is the implementation of systematic data quality control checks embedded within the tracker program. These checks are designed to identify incomplete, inconsistent, or implausible entries at the point of data entry or during routine registry operations, supporting registries in maintaining the standards of data quality required for valid epidemiological analysis and international comparability.

While the DHIS2 Cancer Registry tracker is not designed to support clinical case management or decision support, it serves as an electronic registry tool that enables structured, standardised cancer case registration within the national DHIS2 health information infrastructure.

> **Warning**
>
> The toolkit is intended as a baseline configuration, fully aligned with CanReg5 standards for streamlined data transfer into CanReg5. System administrators may need to localize the program by adding new data elements or attributes, but modification of the baseline configuration is strongly discouraged, as this would likely break the alignment with CanReg5 and program rule logic of the data quality checks.

### Intended Users

The DHIS2 Cancer Registry system design is intended to meet the needs of users at all levels of the cancer registry system. These users may include:

- **Cancer registry managers & staff (national & sub-national)**: data users responsible for overseeing the completeness and quality of cancer case registration, monitoring registry operations, and using registry data to support cancer control planning and reporting
- **Registry data entry staff**: users responsible for the day-to-day capture and management of individual cancer cases within the tracker program, including recording case notifications received from hospitals, laboratories, and other reporting sources, and applying data quality control procedures to ensure accuracy and completeness of registry records
- **Cancer programme data managers**: users responsible for overseeing data collection workflows, data quality assurance, and reporting functions for the national cancer registry programme
- **System admins / HMIS focal points**: Ministry of Health staff and/or core DHIS2 team responsible for maintaining the DHIS2 system, supporting local adaptation of the cancer registry configuration, and providing technical support to end users
- **Implementing partners and technical assistance providers**: organisations providing technical support to national cancer registries, including IARC, HISP Groups, and other partners involved in system implementation, training, and capacity building

## Tracker

### Tracker program structure

The tracker program structure is as follows:

![Cancer registry tracker structure](resources/images/ncd_cr_cancer_registry_tracker_structure.png)

| **Stage**      | **Description**                                                                                                                                                                                                                                                                                                           |
|----------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Enrollment** | The enrollment stage collects the basic demographic data about a person, including unique identifiers, as Tracked Entity Attributes (TEAs). Several of these core TEAs such as Family Name and Given name are shared across DHIS2 Tracker programs. The Tracked Entity Type for the Cancer Registry program is ‘Person’.  |
| **Tumor**      | This stage contained the core information related to the tumor. The stage is repeatable                                                                                                                                                                                                                                   |
| **Sources**    | This stage contained the core information related to the sources associated with each tumor. The stage is repeatable                                                                                                                                                                                                      |
| **Follow-up**  | This stage contained the information for the follow-up of the patient and it’s not associated to either tumor or sources The stage is repeatable                                                                                                                                                                          |

### Tracked Entity Type

The DHIS2 Cancer Registry tracker program allows for the enrollment of a tracked entity type [TET] 'person' into the cancer registry program. Each enrolled person represents an individual cancer patient registered in the population-based cancer registry. The TET is configured at the system level and may be shared with other DHIS2 tracker programs deployed within the same national instance, in line with standard [DHIS2 implementation practice](https://docs.dhis2.org/en/implement/health/dhis2-health-data-toolkit/common-metadata-library/design.html).

### Enrollment

The enrollment stage captures the core patient demographic information required to register an individual in the cancer registry program. As best practice, registry staff should first search for an existing record before creating a new enrollment, in order to avoid duplicate registrations of the same patient.

The attributes collected at enrollment represent the minimum dataset necessary for population-based cancer registration and have been mapped to the IARC standard data requirements. There are five key tracked entity attributes collected at this stage. These attributes are configured at the tracked entity type level and may therefore be shared across other tracker programs within the same DHIS2 instance. However, care must be taken when sharing the **Sex** attribute, as the option set assigned to this attribute uses numeric codes that are referenced in multiple data quality checks throughout the program. Any modification to these codes — or substitution with a differently coded option set — would break the logic of those checks. The OptionSet Sex has been mapped with the relevant [CanReg5 dictionary](https://github.com/IARC-CSU/CanReg5/blob/release/R45/src/canreg/common/resources/dictionaries/sex.tsv).

One attribute, the Patient ID, is automatically generated by the system at the point of enrollment. The pattern follows the same convention used in CanReg5: the identifier is 8 characters long, composed of the current year in four-digit format followed by a four-digit sequential number, in the form `CURRENT_DATE(yyyy)+SEQUENTIAL(####)`.

### Tumor

The Tumor stage is the central component of the Cancer Registry tracker program. It captures all key clinical and epidemiological information related to an individual cancer case, and is where the full set of data quality checks is applied at the point of registration. For a detailed description of the quality control checks, refer to the [dedicated section](#Checks) of this document.

The stage is structured into multiple sections, several of which are hidden from the data entry interface. These hidden sections serve specific functional purposes within the system design: they support the calculation logic underlying the quality control checks, control the data entry flow through program rules, feed the analytical outputs of the program, and enable data extraction via the Cancer Registry DHIS2 custom application.

| **Section**                 | **Visibility** | **Description**                                        |
|-----------------------------|----------------|--------------------------------------------------------|
| Patient                     | Visible        | Patient information                                    |
| Tumor                       | Visible        | Tumor information                                      |
| Check Status                | Visible        | Run multiple checks                                    |
| Checks                      | Not-visible    | Stored information of each individual check            |
| Morphology topography check | Not-visible    | Multistep morphology topography check                  |
| Multiple primary tester     | Not-visible    | Multistep multiple primary tester                      |
| Rare status                 | Visible        | Visible only to users that can confit the rare status  |
| Tumour ID                   | Not-visible    | Storing information needed for the CanReg5 exportation |

#### Mandatory elements

The only formally mandatory data element in the Tumor stage is the **Tumor Number**, which serves as the reference linking each tumor record to its corresponding sources and is essential for the data extraction process via the Cancer Registry custom application.

However, in practice, all data elements in both the patient and tumor sections must be populated in order for the data quality checks to execute correctly. The sole exception is the **Grade** field, which is not required when the behaviour code indicates a value other than malignant. This requirement is enforced by the program rule *CR - Can't run the checks if all mandatory element has not values*, which prevents the checks from running when any of the required fields are missing.

![Warning message for missing variable for running the checks](resources/images/ncd_cr_run_checks.png)

#### Patient

The patient section collects the demographic and geographic information associated with the cancer case. The central date field in this section is the **Incidence Date**, which is the reference date used for all analytical outputs in CanReg5. The Event Date — the DHIS2 system date recorded at the time of data entry — is a separate field whose value is determined by local implementation decisions: it may be set to the date of data entry, aligned with the incidence date, or reflect another locally relevant date. The decision to keep the incidence date as a dedicated data element rather than using the event date for this purpose is driven by the requirements of the Cancer Registry custom extraction application, which references the data element directly.

The **Age** field must be entered manually. It is used in data quality checks, and a program rule verifies the consistency between the entered age, the date of birth, and the incidence date, returning a warning if a discrepancy is detected. Further details are provided in the [quality checks section](#Checks) of this document.

The **Address** field uses an option set that contains placeholder values and must be customised prior to implementation to reflect the administrative geography of the country or region. Rather than using a data element of value type Organisation Unit for geographic coding, the recommended approach is to use text-type data elements combined with dependent dropdown lists, implemented through program rules using the Show option group action. This allows the configuration of cascading selection menus — for example, a first element listing administrative regions, followed by a second element that displays only the districts belonging to the selected region. This approach aligns with the CanReg5 convention for address coding, where the address variable is two characters in length and typically encodes a combination of two administrative levels.

#### Tumor

The tumor section is the primary data collection component of the Tumor stage. It captures the key variables that must be recorded for every cancer case, aligned and mapped to the IARC standard data requirements, with the addition of the **Tumor Number**, which serves as a local reference linking a specific tumor record to its corresponding sources.

The Tumor Number is conceived as a unique identifier for the tumor within the registry. It may be a sequential number or any other locally defined value, and has an option set of text type with numeric values assigned. Combined with the **Patient ID** collected at enrollment, the Tumor Number constitutes the **Tumor ID** — the composite identifier that uniquely identifies a tumor record within the system.

To prevent the same Tumor Number from being assigned to more than one tumor belonging to the same patient, a dedicated mechanism has been implemented using a set of program rules operating on a hidden **Tumor ID** section. 

![Tumor number check flow](resources/images/ncd_cr_tumor_number_flow.png)

A data element **Previous Tumor Number**, of value type `MULTI_TEXT`, shares the same option set as the Tumor Number element. When a new tumor event is opened, program rules inspect the values recorded in the previous tumor event. If a Tumor Number was recorded in the previous event, that value is assigned to the Previous Tumor Number element. If both a Tumor Number and a Previous Tumor Number were present in the previous event, the two values are concatenated and stored as two separate values within the `MULTI_TEXT` field. Once the Previous Tumor Number has been populated, a further program rule checks whether the value currently entered in the Tumor Number field of the active event already exists among the values stored in Previous Tumor Number. If a match is found, an error message is displayed informing the user that the selected Tumor Number has already been assigned to this patient and that a different value must be chosen.

![Error message for tumor number duplication](resources/images/ncd_cr_tumor_number.gif)

The remaining data elements in the tumor section are aligned and mapped to the CanReg5 data standards and are used as inputs for the data quality checks described in the dedicated section of this document. With the exception of the topography field, all elements are free-selection inputs. The **Topography** field is implemented as a dependent dropdown list: the available topography codes are filtered based on the site selected by the user, so that only the topography values valid for the chosen site are presented for selection. 

To simplify data entry, each option includes the corresponding code in its name, allowing registry staff to search directly by code when selecting a value.

The options sets are mapped with the CanReg5 ICDO3.2 version:

| DHIS2 OptionSets      | **CanReg5 ICDO3.2**                                                                                           |
|-----------------------|---------------------------------------------------------------------------------------------------------------|
| **Site / Topography** | https://github.com/IARC-CSU/CanReg5/blob/release/R45/src/canreg/common/resources/dictionaries/topography.tsv  |
| **Morphology**        | https://github.com/IARC-CSU/CanReg5/blob/release/R45/src/canreg/common/resources/dictionaries/morphology4.tsv |
| **Behaviour**         | https://github.com/IARC-CSU/CanReg5/blob/release/R45/src/canreg/common/resources/dictionaries/behaviour.tsv   |
| **Basis diagnosis**   | https://github.com/IARC-CSU/CanReg5/blob/release/R45/src/canreg/common/resources/dictionaries/basis.tsv       |
| **Grade**             | https://github.com/IARC-CSU/CanReg5/blob/release/R45/src/canreg/common/resources/dictionaries/grade.tsv       |

> **Note**
>
>  The option sets used in this section, and the codes associated with each option, are mapped directly to the CanReg5 standards. It is critical that these codes are not modified during local implementation or system maintenance. Any alteration to the option codes will break the logic of the data quality checks, which rely on these values for their calculations

#### Check Status

This section allows the registry staff to trigger the execution of the data quality checks. When the user selects the **Run checks** option, a warning message is displayed for each check that has not been passed, allowing the user to review and correct the relevant entries.

As noted in the mandatory elements section, all data elements in the patient and tumor sections must have a value before the checks can be executed. The only exception is the Grade field, which is mandatory only when the behaviour code is malignant (3).

When Run checks is selected, data entry for the tumor section is blocked. If the user needs to modify any value after the checks have been run, they must uncheck the element to re-enable data entry. This behaviour is enforced by two program rules:

- CR - Block data entry if checks runned
- CR - Block data entry if checks runned - Grade

![Block data entry when run checks](resources/images/ncd_cr_block_data_entry.gif)

Once **Run checks** is selected, two additional check options become visible: 
**Run Topography Morphology check** and **Run Multiple primary check**. These 
have been implemented as separate steps because they require multi-stage 
verification and the involvement of additional program rules that cannot be 
executed in a single pass. Further details are provided in the 
[Checks section](#Checks) of this document. When the main **Run checks** option 
is selected, these two subsequent checks are mandatory in order to ensure that 
the full set of quality control checks is executed.

#### Checks section

| **Data Elements**                           | **Value Type** |
|---------------------------------------------|----------------|
| CR - Checks: Rare Age Morphology            | Boolean        |
| CR - Checks: Rare Age Topography            | Boolean        |
| CR - Checks: Rare Age Topography Morphology | Boolean        |
| CR - Checks: Rare Basis                     | Boolean        |
| CR - Checks: Invalid Grade                  | Boolean        |
| CR - Checks: Rare Sex Morphology            | Boolean        |
| CR - Checks: Invalid Sex Topography         | Boolean        |
| CR - Checks: Rare Topography Behaviour      | Boolean        |
| CR - Checks: Rare Topography Morphology     | Boolean        |
| CR - Checks: Multiple primary test result   | Option Set     |
| CR - Rare                                   | Boolean        |
| CR - Invalid                                | Boolean        |

This section is always hidden from the data entry interface. It contains all 
data quality checks, each implemented as a separate data element. By default, 
all checks are assigned a false value by the program rule *CR - Clear all 
checks*, which resets any checks that previously returned a true result 
whenever the **Run checks** element is unchecked.

The only check with a different value type is the **Multiple primary test 
result**, whose output is not a boolean but one of three possible values: 
*Duplicate primary*, *Multiple primary*, or *Unknown topography*. The full 
logic of each check is described in the [Checks section](#Checks) of this 
document.

In addition to the individual checks, this section contains two summary 
classification elements: **Rare** and **Invalid**. Like the other check 
elements, both are reset to false each time the **Run checks** element is 
unchecked. Their true values are then assigned when the checks are executed: 
the **Rare** element is set to true if any check with a rare output returns a 
true result; the **Invalid** element is set to true if any check with an 
invalid output returns a true result.

These two elements serve a dual purpose. They can be used in analytical outputs 
to restrict counts to tumors that have passed all quality control checks, and 
they can be applied as filters in working lists and line lists to identify and 
review cases flagged as rare or invalid.

A tumor is classified as **Rare** when the combination of entered values — such 
as morphology, topography, age, and other variables — represents a combination 
that can rarely occur, and a supervisor must confirm that the data entered is 
correct. Further details are provided in the 
[Rare status section](#rare-status) of this document.

A tumor is classified as **Invalid** when the data entered describes a 
combination that is anatomically and clinically impossible for a tumor to exist 
(for example a male with ovarian topography).

Following the same logic as CanReg5, the system allows users to enter and save 
any combination of values, including those that produce an invalid result. This 
is by design: invalid records can be identified retrospectively and corrected, 
and can be excluded from analytical outputs. To ensure analytical integrity, it 
is essential that the following three conditions are always applied as filters 
when generating any analytical output:

- *Run checks* = true
- *Rare* = false **OR** *Rare* = true **AND** *Confirm rare status* = true 
(see the [Rare status section](#rare-status))
- *Invalid* = false

#### Morphology topography check

| **Data Elements**                 | **Value Type** |
|-----------------------------------|----------------|
| CR - Morphology Family            | Option Set     |
| CR - Topography Morphology key    | Option Set     |
| CR - Present in the MUST list     | Boolean        |
| CR - Present in the MUST-NOT list | Boolean        |

This section is always hidden from the data entry interface. It contains the data elements used in the calculation of the Rare Topography Morphology check. The full logic of this check is described in the [Checks section](#Checks) of this document.

#### Multiple primary tester

| **Data Elements**                         | **Value Type**         |
|-------------------------------------------|------------------------|
| CR - Morphology group                     | Option Set             |
| CR - Previous morphology group            | Option Set             |
| CR - Previous morphology group (multiple) | Option Set - Multitext |
| CR - Topography group                     | Option Set             |
| CR - Previous topography group            | Option Set             |
| CR - Previous topography group (multiple) | Option Set - Multitext |
| CR - Morphology result                    | Option Set             |

This section is always hidden from the data entry interface. It contains the data elements used in the calculation of the Multiple primary test check. The full logic of this check is described in the [Checks section](#Checks) of this document

#### Rare status

This section is visible only when the tumor has been classified as rare (that is, when the **Rare** element is set to true) and is accessible exclusively to users assigned the specific role authorised to confirm the rare status. It allows a designated supervisor to review the flagged case and confirm that the data entered is correct despite the unusual combination of values.

The logic governing the rare classification and the confirmation workflow are described in detail in the [Checks section](#Checks) of this document.

![Confirm the rare status](resources/images/ncd_cr_confirm_rare_status.png)

#### Tumor ID

| **Data Elements**          | **Value Type**         |
|----------------------------|------------------------|
| CR - Previous tumor number | Option Set - Multitext |
| CR - Patient ID            | Text                   |
| CR - TUMOURID              | Text                   |

This section is always hidden from the data entry interface. It contains key 
data elements used in the extraction of cancer registry data from DHIS2 and 
its subsequent import into CanReg5 via the Cancer Registry custom application.

The **Previous Tumor Number** element is populated with the tumor number values 
collected from previous tumor events for the same patient. This mechanism 
prevents the same tumor number from being assigned to more than one tumor 
belonging to the same patient. The full logic is described in the 
[Tumor section](#Tumor) of this document.

The **Patient ID** element is auto-populated with the Patient ID collected during the enrollment phase by the program rule 
*CR - Assign value to Tumor Patient ID - Tumor*. This value is then used to 
populate the **TUMOURID** element, which serves as the unique identifier for 
the tumor record and is used to link one or more source records to a specific 
tumor during the extraction and import process. The TUMOURID is composed by 
concatenating the Patient ID, the value `01`, and the Tumor Number, as assigned 
by the program rule *CR - Assign value to TUMOURID*.

### Source

The Source stage is used to record the documentation sources from which the cancer case has been notified to the registry. Each source represents a piece of documentation — such as a hospital record, pathology report, or death certificate — that supports the registration of a specific tumor. The stage is composed of two sections:

| **Section**         | **Visibility** | **Description**                                        |
|---------------------|----------------|--------------------------------------------------------|
| Source              | Visible        | Source information                                     |
| TUMOURIDSOURCETABLE | Not-visible    | Storing information needed for the CanReg5 exportation |

#### Source section

This section collects the information related to the individual source record. 
A key aspect of the Source stage is the ability to link each source to a 
specific tumor belonging to the same enrolled patient. As described in the 
[Tracker Program Structure section](#Tracker-program-structure), a single tumor 
may have multiple sources of information associated with it. To support this, 
the user must specify the **Tumor Number** to which the source is being linked 
at the time of data entry.

If the tumor number selected in the source record has not been previously 
assigned in any tumor event for the same patient, an error message is displayed 
guiding the user to select a valid existing tumor number. This validation is 
enforced by the program rule 
*CR - Show error if the tumor number has not been assigned to any tumor*.

![Error message for tumor number](resources/images/ncd_cr_tumor_number_flow.gif)

The **Type of source** field uses placeholder values in the reference configuration and must be customised prior to implementation to reflect the source types relevant to the local registry context.

#### TUMOURIDSOURCETABLE

| **Data Elements**        | **Value Type** |
|--------------------------|----------------|
| CR - Patient ID          | Text           |
| CR - TUMOURIDSOURCETABLE | Text           |

This section is always hidden from the data entry interface. It contains key 
data elements used in the extraction of cancer registry data from DHIS2 and 
its subsequent import into CanReg5 via the Cancer Registry custom application.

The **Patient ID** element is auto-populated with the Patient ID collected 
during the enrollment phase by the program rule 
*CR - Assign value to Tumor Patient ID - Source*. This value is combined with 
the Tumor Number selected in the source section to populate the 
**TUMOURIDSOURCETABLE** element, following the same concatenation logic used 
for the TUMOURID in the Tumor stage — `Patient ID + 01 + Tumor Number` — 
assigned by the program rule *CR - Assign value to TUMOURIDSOURCETABLE*.

The **TUMOURIDSOURCETABLE** element is the key reference used during the export 
process to link each source record to its corresponding tumor, enabling the 
correct reconstruction of the tumor-source relationship when data is imported 
into CanReg5.

### Follow up

The Follow-up stage is used to record the follow-up status of the registered patient over time. It captures the date of last contact with or information about the patient, the follow-up status, and — in cases where the recorded status is death — the date of death.

![Follow up stage](resources/images/ncd_cr_follow_up.png)

## Checks

One of the central features of the DHIS2 Cancer Registry toolkit is the implementation of the data quality checks used in CanReg5 to assess the completeness and accuracy of registered cancer cases. These checks represent a core component of population-based cancer registration practice, ensuring that the data collected meets the standards required for valid epidemiological analysis and international comparability.

As described in the Tumor stage sections above, the implementation of these checks in DHIS2 requires the configuration of multiple program rules and several calculated data elements working in combination. The sections below describe each check in detail, including the variables involved, the conditions evaluated, and the expected outcomes.

The CanReg5 checks used as reference can be found it here: https://github.com/IARC-CSU/CanReg5/tree/release/R45/src/canreg/common/qualitycontrol

### Transversal considerations

Several considerations apply across all or most of the checks described in 
this section and are presented here to avoid repetition.

#### Value types for data elements and option sets

A key aspect of the correct implementation of the checks is the proper 
configuration of data element and option set value types. In CanReg5, most 
checks evaluate specific values against a range of numeric values, using the 
numeric codes assigned to each option. It is therefore essential that in DHIS2 
both the data elements and their associated option sets are configured with a 
**Number** value type, and that the program rule variables referencing option 
set values use the **code** rather than the display name. The option names may 
remain descriptive text, but the codes must be numeric.

This configuration allows CanReg5 range conditions to be translated directly 
into DHIS2 program rule expressions without modification. For example, a 
CanReg5 condition such as `morphologyNumber >= 8270 && morphologyNumber <= 8281` 
can be implemented in DHIS2 using the same expression, without the need to 
enumerate every individual value within the range. This significantly reduces 
the complexity and length of program rule expressions.

#### Grouped code logic

Some CanReg5 checks use a grouping approach to optimise the evaluation of 
topography codes, dividing the code by 10 to group a range of values. For 
example, a function may define `topographyGroup = topographyNumber / 10` and 
then evaluate `topographyGroup == 53` to cover all topography codes in the 
range 530–539. Since this division operation is not natively supported in DHIS2 
program rules, the equivalent logic must be expressed explicitly as a range 
condition. The above example would be translated in DHIS2 as 
`topography >= 530 && topography <= 539`.


