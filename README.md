# Bevolve AI: System Design Interview Question

## Problem Statement: Data collection for ESG reporting

One of the product offerings of Bevolve is track and report ESG matrices and disclosures. See [This Link](https://www.thecorporategovernanceinstitute.com/insights/guides/simple-guide-esg/) for more info.
The ESG has many reporting frameworks or standards like GRI, BRSR, CDP, TCFD, FASB etc. See more information here [ESG Frameworks](https://www.greenstoneplus.com/resources/frameworks-standards/list-of-key-esg-reporting-frameworks-and-standards)
These frameworks get revised and maybe specific to a particular Sector or Industry. For the sake of simplicity we'll work with BRSR and CDP frameworks only for this assignment.

Bevolve needs to capture the details from customers according to these Standards/frameworks and then create a report for reporting/disclosing the information defined by above frameworks. To capture the information Bevolve needs to send various assessments to customers and based on the information collected, system must map this information to relevant frameworks. A framework generally asks various questions from the customer about their business. Check here for [BRSR](https://www.sebi.gov.in/sebi_data/commondocs/may-2021/Business%20responsibility%20and%20sustainability%20reporting%20by%20listed%20entitiesAnnexure1_p.PDF) and [BRSR Guidance](https://www.sebi.gov.in/sebi_data/commondocs/may-2021/Business%20responsibility%20and%20sustainability%20reporting%20by%20listed%20entitiesAnnexure2_p.PDF)
and for [CDP](https://cdn.cdp.net/cdp-production/cms/guidance_docs/pdfs/000/005/005/original/CDP-full-corporate-questionnaire-overview_-_2024.pdf?1714053310_)

Our Catalyst team (Environment Scientists and research team) will get some information and documents from customers based on whether they need to prepare assessments for the information that is missing or the team can decide that every detail needs to be captured again. The idea is to enable the Catalyst team with AI support where they can extract as much as information from these docs and let them create assessment forms for the remaining information that we couldn't capture through AI.

The assessments are recurring and can be sent every year. If there is no change in framework then assessment forms remain the same otherwise they'll need to be modified. For now let's assume any change in assessment form for a given framework will remain manual and the Catalyst team will decide whether a particular assessment needs any change or not. These assessment forms in our system must be versioned controlled.

In a nutshell the whole requirement boils down to following:

- For every framework, the Catalyst team needs to prepare assessment forms or templates. There can be multiple such templates mapped to a particular framework/standard
- There must be a questionnaire library which Catalyst team can map to templates.
- The Catalyst team can choose templates and override, add or remove questions when they are preparing an assessment for a customer. An assessment will be a collection of questions from various templates.
- Which questions/templates they should choose totally depends upon two things: a) The framework for which they are trying to gather data b) how much information was extracted from AI which was already given by customers in some form of documents.
- One assessment can be sent to just one user at customer side or parts of an assessment can be sent to different users as they might have better understanding of the given section of an assessment. 

Customer receives the assessment in the email, on clicking on the link they'll land up on Bevolve. After this customers can start filling forms/questionnaires. The Customer can ask questions if they don't have clarity on what to fill in a given question. Here ESG AI assistant can help and recommend what to fill and from where to gather this information. If they are not satisfied with AI's help they can raise a query to the Catalyst team to help them figure out that question. The progress must be saved incrementally.

Once the information is filled it'll be reviewed by the Catalyst team. However, a use-case of auto moderation with the help of AI will be ideal to save time for the team.

## what is expected from the assignment?

For a successful assignment completion your solution must have following:

- Identifying hidden requirements
- Architecture Diagram
- Choice of stack and the reasoning(Frontend backend and AI)
- Deployment Diagram
- Database Schema(General idea of storing the information)
- Identifying common APIs (at least basic request and response structure)
- Deliberation on the architecture how things will work

## Non-functional requirements

- 100K assessment forms being sent out in a year
- 5000 concurrent users
- Data Isolation for every customers
- Data Encryption at rest and in transit
- Min 5 GB document processing in a given day
- Logging & Monitoring
- Choice of any paid service that you think will benefit the product
- The team with their experience and competency levels that you'll need to execute the project.
- CD-CI pipeline and managing 99.99% uptime
- Test Plan (Functional, Security, Performance)

Feel free to make assumptions and write delibrations based on your assumptions and limitations. 

