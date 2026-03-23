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












