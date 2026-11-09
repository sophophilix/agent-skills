# Document structure

Derived from the Optus Infrastructure Solution Design standard, the Oreta and Telstra reference architecture template, and Transport for NSW design overview conventions. Adapt to a mandated organisational template where one exists, keeping the voice and output rules from the parent skill.

## Front matter

1. Title block: document title, programme or project name, programme number, and a document identification table.

   | Document ID | Version | Issue Date |
   |---|---|---|

2. Proprietary or confidentiality notice.
3. Document information:

   **Approvers**

   | Role | Name | Signature | Date |
   |---|---|---|---|

   **Key stakeholders**

   | Role | Name | Contact details |
   |---|---|---|

   **Version history**

   | Issue No. | Issue date | Author | Nature of amendment |
   |---|---|---|---|

4. Table of contents.
5. Table of tables.
6. Table of figures.

## Body: project solution design

```
1     Document Purpose
1.1     Document Audience            (table: Position | Use of Document)
1.2     Background
1.3     Terminology                  (table: Acronym | Term | Definition)

2     Reference Documents
2.1     Programme Requirements       (table: Type | Document Name | Ver | Released | Author | Location)
2.2     Associated Designs
2.3     Policy, Compliance and Procedure
2.4     Other References

3     Alternative Solutions Evaluation
3.1     Assessment Criteria
3.2     Alternative Solutions
3.2.1     Alternative 1
3.2.2     Alternative 2
3.2.3     Alternative 3
3.3     Pros and Cons                (one table per alternative)
3.4     Data Security Requirements
3.5     Risk Assessment for Solution Design
3.6     Conclusions                  (names the selection and the reason)

4     Design Overview
4.1     Scope
4.2     Specific Exclusions
4.3     High Level Design            (Figure 1)
4.4     Solution Critical Success Factors

5     Design Detail
5.1     Constraints
5.1.1     IT Environment
5.1.2     Standards
5.1.3     Business Constraints
5.2     Assumptions
5.3     Dependencies
5.4     Detailed End to End Design   (Figure 2)
5.5     Current State
5.6     Target State
5.7     Transition and Migration Strategy
5.8     Disaster Recovery
5.9     Leveraged Infrastructure
5.10    Decommissioned Infrastructure
5.11    Solution Interoperability
5.11.1    Known Interoperability
5.11.2    New Interoperability
5.12    Solution Compliance
5.12.1    Standard Operating Environment Compliance
5.12.2    Security Compliance
5.12.3    Maintenance Window
5.13    Identity and Access
5.14    Integration                  (references the interface register)
5.15    Data and Reporting
5.16    Servers
5.17    Storage
5.18    Backup and Restore
5.19    Network
5.20    Database
5.21    Other Hardware and Software
5.22    Client and End User Computing
5.23    Data Centre or Cloud Region

6     Non Functional Requirements
7     Operational Readiness
8     Design Components               (bill of materials)
9     Risks, Issues and Open Items
10    Definitions and Acronyms

Appendix A   Document Control and Approval
A.1     Responsibilities
A.2     Review Record
A.3     Approval
A.4     Distribution
A.5     Version Control
Appendix B   Detailed Parts List
Appendix C   Registers
```

## Body: reference architecture

Replace sections 3 to 5 with the following. Keep sections 1, 2 and 6 onwards.

```
1     Purpose
1.1     Scope
1.2     Specific Exclusions
1.3     Audience
1.4     Terminology

2     Reference Documents
2.1     Requirements
2.2     Associated Designs
2.3     Policy, Compliance and Procedure
2.4     Other References

3     Introduction
4     Architecture Principles
5     Assessing the Landscape          (patterns, one subsection per pattern)
6     Workload Characteristics
7     Qualification Criteria           (decision flow, Figure 1)
8     Methodology
9     Domain sections                  (as required: compute, storage, network, security,
                                        identity, integration, data, orchestration, operations)
```

## Application to a cloud or SaaS migration

Where the solution is a migration to a vendor-managed platform rather than infrastructure Council builds, section 5 domain subsections are read as the shared responsibility split rather than as a build specification. Each retains its heading and states which party is accountable, what the vendor provides, and what the organisation retains. Sections 5.9 and 5.10, leveraged and decommissioned infrastructure, become the principal record of what leaves the estate.

## Section content rules

| Section | Must contain |
|---|---|
| 1 Document Purpose | One sentence stating what the document defines. Not what the project does |
| 1.1 Document Audience | Every reviewing party and what each uses the document for |
| 2 Reference Documents | Version, date, author and location for every source |
| 3.1 Assessment Criteria | The criteria, stated before the alternatives, and how each is weighted or ranked |
| 3.6 Conclusions | The selected option named, and the reason, including what is given up |
| 4.1 Scope | What the solution covers, as statements not aspirations |
| 4.2 Specific Exclusions | Flat negative statements. Consistent with 4.1 |
| 4.4 Critical Success Factors | "The successful implementation of this solution is affected by the following factors:" |
| 5.1 Constraints | Register format. Every constraint has a source |
| 5.2 Assumptions | Register format. "It is assumed that…". Every assumption has an owner and review trigger |
| 5.7 Transition | Coexistence, cutover, rollback and decommissioning. All four |
| 6 NFRs | Target and verification method for every requirement |
| 9 Open items | Owner and required-by date for every entry |
```
