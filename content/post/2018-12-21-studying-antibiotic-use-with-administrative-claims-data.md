---
title: Studying antibiotic use with administrative claims data
author: ~
date: '2018-12-21'
slug: studying-antibiotic-use-with-administrative-claims-data
categories: []
tags: []
---

In my postdoc, I led a few projects that used administrative claims databases to characterize antibiotic use, specifically Medicare for [a project](https://doi.org/10.1136/bmj.k3155) about trends in antibiotic use in association with particular diagnoses, Truven's MarketScan database for [a project](https://doi.org/10.1093/infdis/jiy551) about seasonal macrolide prescribing and seasonal gonorrhea resistance, and MarketScan again for [a big project](https://doi.org/10.7554/eLife.39435) relating the distribution of antibiotic use and with resistance.

I wrote a version of this post as a guide for colleagues in my lab and elsewhere for thinking about using this kind of information. I've posted it here too.

# Why measure antibiotic use?

Reducing antibiotic use can lower healthcare costs, avoid
antibiotic-associated adverse events like *C. difficile* infection, and
decrease antibiotic resistance. Public health and hospital officials
want to measure antibiotic use to, for example, measure the effect of a
stewardship intervention on antibiotic use.

As scientists of antibiotic resistance, we have a more complex goal. We
develop models of the relationship between predictors of resistance,
including antibiotic use, and the resulting resistance rates. These
models can then be used to predict the future of resistance and predict
the effect that a stewardship intervention with some specified change in
antibiotic use will have on antibiotic resistance.

# Individual- and population-level use-resistance associations

There are two relatively simple extremes for thinking about this
relationship. First, an individual's antibiotic use correlates with
their resistance. A great example is the data in the plot below, adapted
from [Carothers *AIM* 2003](https://www.ncbi.nlm.nih.gov/pubmed/13679322), which
shows use of quinolones against quinolone resistance in *H. pylori*.
This study is remarkable because *H. pylori* is an
infrequently-transmitted pathogen ---we think that someone gets *H.
pylori* once and keeps it for decades--- and the study subjects are
Alaska Natives who have access to a single healthcare provider that kept
track of their antibiotic use for the \~10 years before the study.

|No. quinolone doses|Patients with quinolone-resistant bacteria|
|-------------------|------------------------------------------|
|0                  |2%                                        |
|1                  |17%                                       |
|2                  |25%                                       |
|3                  |50%                                       |
|4+                 |100%                                      |

Based on this data like this, it would be easy to predict the effect of
a change in antibiotic use within individuals. The weakness of this
approach is that it is specific to the study population. In the typical
epidemiology sense, you would say that different populations might have
different intrinsic use-resistance relationships. For example, men and
women could have different pharmacodynamics that lead to different
selective pressures for the same drug use. In the more complicated
infectious disease epidemiology sense, we can be confident of this
use-resistance relationship only for the strains of *H. pylori* that are
present in these individuals. *H. pylori* and quinolones is convenient
combination because *H. pylori* is infrequently transmitted and
quinolone resistance is probably acquired within each individual
sequentially, like in tuberculosis. You might not expect an
individual-level study like this to give you confidence about the
population-level use-resistance relationship for, say, *S. pneumoniae*
and β-lactams.

This brings me to the other extreme for thinking about use-resistance
relationships: populations. Say you have some very large, completely
separate populations. The fact that the populations are large means that
you can count on each population to have access to the same set of
circulating pathogens, so antibiotic use can select for resistance in
the same way in each population. The complete separation between
populations means that you don't have to worry about how colonization
pressure from a high-use, high-resistance population will push
resistance into a low-use population.

This approach leads to large ecological studies, like the famous
[Goossens *Lancet* 2005](https://www.ncbi.nlm.nih.gov/pubmed/15708101),
comparing antibiotic use and antibiotic use at the level of European
countries.

<img src="/img/goossens.png" style="max-width:50%">

There are a few problems with this approach, an important one being that
correlation does not prove causation. There are many things that make
France different from the Netherlands, so it may not be that antibiotic
use alone should be expected to explain all the difference in resistance
between those two places. A cross-sectional study like this also doesn't
account for the history of antibiotic use. It could be that antibiotic
use was previously high in Spain and dropped just before the study,
explaining why Spain is above the line.

# The phylodynamic approach for use-resistance associations

Many of the issues raised by the individual- and population-level
studies can be resolved with a phylodynamic approach. Roughly, the idea
is this:

-   Say you have some perfect sampling of pathogens. For each pathogen,
    you have its genome, its susceptibility profile to all relevant
    antibiotics, its collection date, and the identity of the patient
    it was sampled from.
-   Given this information, infer the phylogeny and transmission history
    of all the sampled pathogens. In particular, identify the moments
    in which a pathogen acquired or lost resistance to an antibiotic.
    Pay attention also to the lineages that dead-ended because they
    were exterminated by antibiotic use, they were competed out by a
    more fit strain, or the host simply failed to transmit the
    pathogen before it was cleared by host immunity.
-   Compare the history of resistance acquisition, resistance loss,
    susceptible lineage dead ends, or resistance lineage dead ends
    with the history of antibiotic use in the individuals that the
    pathogen was inhabiting when the event happened.

This approach unifies the individual- and population-level approaches.
Antibiotic use is treated as having a direct effect only within the
individual that used it, but an individual's antibiotic use can still
have an indirect effect on other individuals because they transmitted a
resistance pathogen (or failed to transmit a sensitive one).

The central weakness of this approach is its complexity. In particular,
sampling of pathogens will certainly not be perfect, so strong
assumptions or confident assertions will need to be made about the
pathogens' phylogenies and transmission histories. It will also be
relatively difficult to make projections about how a stewardship
intervention will affect resistance, as the project will require making
assumptions about how the intervention will change the use of all
antibiotics, about the exact individuals in which the change will occur,
and about the hosts' contact network.

Nevertheless, this approach is certainly the future of the
use-resistance modelling. Clearly it requires a very sophisticated data
set, ideally one with perfect sampling of pathogens, genomic and
phenotypic information about those pathogens, and antibiotic use
histories from all the individuals that carried those pathogen lineages.

# Data sources for antibiotic use

Here I focus on the antibiotic use part of the picture. Antibiotic use
data comes in a few major forms.

Inpatient antibiotic use data is tracked by hospitals' internal
pharmacies. Hospitals keep this data for logistical and budgetary
reasons, like ordering new stocks, as well as for policy reasons. In the
US (at least), hospitals are not required to record this data in any
kind of uniform way, so querying this data requires developing
relationships with individual hospitals, getting them to export it for
you, etc. (A hospital in Boston handed once handed one of my
collaborators a bunch of data in the form of a 40,000 page pdf
document.)

The US has at least two national surveys designed to measure healthcare
behaviors across the whole country.
[NAMCS/NHAMCS](https://www.cdc.gov/nchs/ahcd/index.htm)
are a sample of outpatient healthcare visits. The strength of that
dataset is the linkage between diagnoses and prescriptions, so you have
a good idea of what the antibiotic was intended to treat
([Fleming-Dutra *JAMA* 2016](https://www.ncbi.nlm.nih.gov/pubmed/27139059)). By
contrast, the [MEPS](https://meps.ahrq.gov/mepsweb/)
survey is a sample of households, allowing measurements of an
individual's whole antibiotic use ([Olesen *EID* 2018](https://www.ncbi.nlm.nih.gov/pubmed/30334733)).
Neither survey has very many records, so it's hard/impossible to make
inferences about specific antibiotics, diagnoses, and patient
populations.

Sales volumes databases, most notably the IQVIA (formerly known as IMS
or Quintiles) Xponent database, can give information about both
outpatient and inpatient antibiotic use by drug, location, and
prescriber ([Hicks *CID* 2015](https://www.ncbi.nlm.nih.gov/pubmed/25747410),
[Wang *ICHE*
2017](https://www.ncbi.nlm.nih.gov/pubmed/29072150)). The
strength of this data is its size and coverage: the Xponent database
includes \~70% of the outpatient prescriptions made in the US. The
disadvantage is these data are essentially a sample of prescribers and
antibiotic dispensers. The volume of antibiotic use is not recorded by
individual. IQVIA contacts a pharmacy, asks for its antibiotic sales
data, and pays for that data. It does not ask about the identity of the
individuals getting the antibiotics. The primary audience for sales
volumes databases are pharmaceutical companies who want to predict the
size of markets; they are not primarily intended for scientific
research.

The final major form of data is administrative claims records. If a
person is covered by insurance, then the healthcare provider and the
insurance both keep records of the provided care so they know how much
money to exchange. The obvious disadvantage of this data is that it only
includes people with health insurance. The advantage is that it includes
line-item information by person and by healthcare event, including
things like picking up antibiotic prescriptions or visiting a doctor or
hospital.

I have experience with three claims records systems, Medicare ([Olesen
*BMJ* 2018](https://www.ncbi.nlm.nih.gov/pubmed/30054353)),
MarketScan ([Olesen *JID* 2018]](https://www.ncbi.nlm.nih.gov/pubmed/30239814), [Olesen *eLife* 2018](https://www.ncbi.nlm.nih.gov/pubmed/30560781)), and
the Massachusetts All Payers Claims Database (APCD). Medicare claims
records come from a single provider, Medicare. The MarketScan database
is a compilation of private insurance companies' claims data. The APCD
consists of data that Massachusetts insurance providers are legally
required to provide to the state.

The rest of this document is about administrative claims records as a
measure of antibiotic use, using my experience with these three data
sets as a guide.

# Claims records are for claims

It's tempting to think of claims records as a perfect recording of
antibiotic use. In fact, there are many caveats. It's important to
understand those caveats both to understand the potential limitations of
the data and to be able to design sensitivity analyses that can quantify
those limitations.

The first and most important thing to remember about claims records is
that they are designed for billing purposes, not for scientific
research. All the data in claims records is there only because it is
useful to the insurer in keeping track of how much money it owes to
whom. Government data, like from Medicare and Medicaid, may have some
exceptions, because governments may like the idea of creating a dataset
that can be used for research purposes. That being said, Medicare claims
data exist mostly to make sure the government is not defrauded, not to
be a nice tool for researchers. Similarly, although the APCD was set up
explicitly as a way for industry and public-sector researchers to
understand healthcare in Massachusetts, the data captured are only ones
generated by the insurance providers for their own internal billing
purposes.

As a corollary, the data that are in claims records are there because it
is in the interest of those who collect and populate that data. For
example, healthcare providers' reimbursements can depend on the type of
care provided to beneficiaries as well as the beneficiaries' health
status. This incentivizes providers to describe the care and the
beneficiaries' health in the way that provides them the greater
reimbursement while not being guilty of fraud. Because the provided care
and beneficiaries' health are recorded as different kinds of codes, the
practice is called "upcoding". (The related term "coding shift"
describes a situation where the care being provided remains the same but
the codes used to describe the care change with time.)

Thus, if there were a data field in a government claims database that
was only of interest to researchers, the values populating that field
will generally be of low quality. In general, the closer a data field is
to the dollars and cents, the more reliable you can expect it to be.

# Be careful about coverage

It's crucial to have a very clear understanding of who has insurance
coverage and for what kinds of services.

For example, Medicare has many "parts", which are all quasi-separate
insurance plans. Part A is hospital insurance, Part B is outpatient
healthcare insurance, and Part D is outpatient prescription medicine
insurance. Some people are on Parts A, B, and D; some people are only on
some parts. Parts A, B, and D are called "fee-for-service", meaning that
Medicare pays healthcare providers itemwise for the services they
provide. Importantly, Medicare only has information about antibiotic use
from people who are on Part D.

Part C, usually referred to by its other name, Medicare Advantage,
includes plans that are not fee-for-service. Instead, Medicare pays an
HMO a fixed amount of money per beneficiary, depending on the
beneficiary's health. Because Medicare is not paying for individual
procedures and drugs, Medicare's claims records do not include
information about those individual's healthcare use.

As another example, the APCD includes data, roughly speaking, from all
Massachusetts health insurance providers. Note, however, that this
doesn't necessarily mean that data from Massachusetts resident is
included. If you're happy to say that you're studying a convenience
sample of insurance plan holders, then this is fine. If it's important
that your study population be all Massachusetts residents, then you'll
need to think a little harder.

Finally, it's important to note that insurance plans are not exclusive.
People can generally have as much insurance from as many providers as
they want. Medicare is particularly convenient because there's good
evidence that most people on Medicare don't have other insurance. This
is less true of private insurance plans like those in MarketScan or
APCD. Claims records therefore are not guaranteed to tell you everything
about a person's antibiotic use.

# Codes for drugs

The most important codes when working with antibiotics will be the one
encoding the type of drug. These codes usually encode the actual product
sold or the ingredient.

In the US, drug products are identified with NDCs (National Drug Code).
Each NDC has three sub-codes: "labeler" (e.g., Pfizer), "product" (e.g.,
ciprofloxacin hydrochloride 500 mg), and "package" (e.g., 1 bottle with
100 tablets). (NDCs are written in different ways depending on where you
put the hyphens between the subcodes and whether they are zero-padded,
which is a headache but doesn't introduce any ambiguity.) The FDA keeps
a public list of the currently-active NDC codes and some historical
lists, but different providers use different sets of codes. The Veterans
Administration, for example, uses many NDC codes that are not used by
the FDA. The lesson is that it's important to know what universe of drug
codes is being used in each data set.

There are many taxonomies of drug ingredients, although it seems like
there is a slow coherence around the WHO's Anatomical Therapeutic
Chemical Classification (ATC). For example, the code for ciprofloxacin
is J01MA02:

-   J = antiinfectives for systemic use
-   J01 = antibacterials for systemtic use
-   J01M = quinolone antibacterials
-   J01MA = fluoroquinolone antibacterials
-   J01MA02 = ciprofloxacin

Claims data are concerned with cost and so usually record a product code
like NDC, but as researchers we are interested in ingredients like those
encoded in the WHO ATC. In some cases, the claims record administrators
curate a product-to-ingredient mapping. For example, Medicare has a
Formulary file and MarketScan has the Red Book. Unfortunately, those
mappings may only take you part of the way. Medicare's Formulary file
links each outpatient prescription claim with a text-field ingredient
like "ciprofloxacin HCl", so that another mapping from "ciprofloxacin
HCl" to "J01MA02" is required. In other cases, you will need to map the
product to an ingredient entirely on your own, which may also require
using the trade names. For example, ciprofloxacin was marketed under
many trade names like "Ciloxan".

The NLM's Unified Medical Language System (UMLS) is the most complete
mapping I've seen between product codes and ingredient names, but it is
not easy to use.

# Codes for visits and diagnoses

Although they don't measure antibiotic use per se, it may be interesting
to know about the visits and diagnoses associated with prescriptions.
Most diagnoses are encoded using the International Classification of
Diseases, originally motivated by Florence Nightingale's experience as a
nurse in the Crimean War, trying to communicate with healthcare
providers who spoke different languages about patients' conditions. For
example, a ciprofloxacin prescription might be motivated by something as
vague as pain during urination (code 7881, "dysuria") or as specific as
"intestinal infection due to enteropathogenic *E. coli*" (code 00801).

The Ninth Revision (ICD9) was introduced in the 1970s. The US healthcare
system switched to ICD10 in October 2015; other countries changed at
other times. ICD9 and ICD10 do not have a one-to-one mapping, making it
particularly challenging to do analyses across a switch between systems.

Codes are also used to record the procedures that took place in a
healthcare event and the circumstances of the visit. For example,
Medicare claims records include a Healthcare Common Procedure Coding
System (HCPCS, pronounced "hick-picks") code that can mark an in-person
doctor's office visit. Alternatively, one might look for a Current
Procedural Terminology (CPT) code for an outpatient service.

Antibiotic might be administered during a visit but not explicitly
recorded in a claim. Two examples include inpatient stays, which
generally don't include show line-item claims for each antibiotic used,
and in-office antibiotic administrations, like the single-dose
treatments for gonorrhea. In these cases, the claims records might
include a procedure code showing that drugs were administered, but which
drugs won't be specified.

# Thinking through the chain of events

Many of the caveats about interpreting antibiotic claims data can be
organized by thinking through the data generation and capture during the
chain of events that leads to someone taking antibiotics.

First, someone has to have health insurance. Insurance providers keep
track of their plan members in a "membership" or "denominator" file.
This has enough weird caveats that I'll break that into a separate
section below.

Second, someone has to develop symptoms that make them interested in
getting antibiotics. At this point, all sort of short-circuits could
happen. Someone might not go to the doctor because they don't have
access to a doctor, or can't afford the copay, or their symptoms aren't
severe enough. Or, they might not bother going to a doctor and instead
buy antibiotic over the counter. (This is illegal and probably not very
common in the US.) Or, they might use some antibiotics left over from a
friend's or family member's prescription. These short-circuits are
difficult to account for.

Third, someone has to have an encounter with a prescriber. As discussed
above in the section about procedure and diagnosis codes, this encounter
may generate some claims records. In other cases, the encounter, if it
did happen, generates no claims ([Riedle *ICHE*
2017](https://www.ncbi.nlm.nih.gov/pubmed/27955718)). This
may because the person went to a prescriber in a healthcare system that
produced a claim not captured by the dataset being studied. Or, it may
be because the prescriber didn't file a claim, because the visit was not
eligible for insurance reimbursement, perhaps because the patient paid
out of pocket.

Fourth, the prescriber has to write a prescription. There's a whole
literature about what things beyond diagnoses modulate prescribers'
behavior.

Fifth, the patient has to fill the prescription they've been given. This
is the event that leads to the antibiotic prescription fill claim.
Again, there are all sorts of reasons that someone might have gotten a
prescription but they fail to fill it (e.g., they can't afford it, or
their symptoms aren't bad enough that they, or they just deal with the
pain for some reason). It's also possible that the fill occurred but
wasn't recorded for some reason, like if the patient pays out of pocket
so that insurance is never billed.

Finally, someone has to actually take the antibiotics they've picked up.
Compliance with prescribers' instructions is certainly lower than 100%,
and it almost certainly varies in important ways with patient
characteristics. There are a few studies that track patients'
pill-taking behavior by giving them medicine bottles with timers
measuring when the cap is opened and little springs measuring the weight
of the pills remaining in the bottle. It's unclear how those results
relate to compliance for antibiotics. The only case where there is a
clear method for testing for compliance internal to the data is with
prophylactic antibiotic use. If someone has \~12 claims for
nitrofurantoin in a year, you can guess they are on prophylaxis for
UTIs. You can then check how many days of drug were present in each fill
and compare that to the spacing between fills. For example, someone
might get 30 days of drug but wait 45 days between fills.

# Denominator files

In some cases, like Medicare, each record in the denominator file is a
single person. (Actually, the records in Medicare's data are indexed by
Medicare IDs, but the organization that provides Medicare data to
researchers cross-references those IDs to make a single,
unique-to-a-person beneficiary ID.) In other cases, like MarketScan and
APCD, each record is a person-in-a-plan. A single person might have
multiple records because they changed insurance plans. The database
provider might try to link those records with a common ID using some
inference based on other information about the plan member (e.g., their
name, address, etc.), but it's not always clear how well that works.

One way to work around these difficulties is to only include certain
people in the analysis. For example, when working with the MarketScan
and Medicare data, I only included individuals who were on insurance for
all 12 months in a year. This has the advantage that the rate of
antibiotic use ---say, claims per 1,000 people per year--- is easy to
calculate: claims in year X divided by number of members in year X. The
disadvantage is that it introduces bias by systematically excluding
people with certain kinds of healthcare behavior, which is probably
linked with their health status. A more complete approach would be to
consider antibiotic use and membership by month, but this requires more
somewhat more complex bookkeeping.

Another work-around is to be more careful (or creative) with the
denominator. When working with the APCD, we considered three
denominators: the straightforward one using the Member Eligibility file,
a more complex one where we filtered the Member Eligibility file
according to member's primary insurance, and finally the census. We
considered the second approach because it seemed like most of the
antibiotic use was coming through members' primary insurance, so
including secondary insurance members was somehow inflating the
denominator, shrinking the antibiotic use rate. We considered the third
denominator, just counting the number of people in Massachusetts,
because the APCD theoretically covers all (or most of) people in
Massachusetts. We basically picked the denominator that gave the closest
results to other estimates of antibiotic use rates.

# Units of antibiotic use

Antibiotic use can be measured with different units. US antibiotic use
is often measured in units of prescriptions, probably because of the
influence of the Xponent database and NAMCS/NHAMCS surveys. The typical
unit for population-level antibiotic use is prescriptions per 1,000
people per year.

Claims records provide somewhat different information. As described in
the "chain of events" section, a claim is not equivalent to a
prescription. Someone can get a prescription and not fill it, leading to
no claim. Or, someone can get one prescription that leads to many fills.
These other fills, called "refills", are often marked by a data field
like "fill number" in claims records. Excluding refills is a better
approximation for prescriptions, but including refills gives a better
sense of antibiotic use. The two tend to be very different only for
long-term prophylaxis, like in the nitrofurantoin for UTI example.

Claims records sometimes also include a "days' supply" field, meaning
that the pharmacist parsed the prescription for how many days of drug
were included in the thing handed to the patient. This metric for use is
further along the vector from prescriptions to fills, giving an even
more fine-grained idea of how much drug is being used. In practice, most
prescriptions for an antibiotic include the same number of days' supply,
so the results are not that much different.

One might go even further to dosage. European data are typically
measured in defined daily doses (DDD) per 1,000 people per day (DID).
The DDD is defined in the WHO ATC. For example, 1 gram of ciprofloxacin
(J01MA02) administered orally is 1 DDD. Claims records presumably
contain enough information to get the dosage. For example, an NDC code
might tell you that the thing handed to the patient was a bottle with
ten 500 mg tablets of ciprofloxacin hydrochloride, i.e., 5 DDD. I
haven't seen this kind of conversion attempted.

# Getting the data

In general, to get data, you'll need to either find someone who already
has it (and broker a collaboration with them) or make a request to get
it on your own.

Making your own request for the data will involve a few parts. You'll
prepare an application for the dataset, explaining what your research
project is, why you need each variable, etc. Different datasets and
variables require more justification. For example, Medicaid (not
Medicare) data is very hard to get because CMS is very protective of
Medicaid recipients. My guess is that Medicare and CHIA were more
challenging to get than MarketScan, but the applications are all fairly
onerous.

In some cases, requesting more specific data will require special
justification. When I wrote an application for the APCD data, there was
a special section where you had to justify why you would need 5-digit
zipcode information rather than 3-digit. More broadly speaking, there
might be different types of datasets that you can request. For example,
Medicare has three levels of data sets:

-   "Research Identifiable Files (RIFs) contain beneficiary level
    protected health information (PHI). Requests for RIF data require
    a Data Use Agreement (DUA) and are reviewed by CMS's Privacy Board
    to ensure that the beneficiary's privacy is protected and only the
    minimum data necessary are requested and justified."
-   "Limited Data Set (LDS) files also contain beneficiary level
    protected health information [... but] selected variables
    within the LDS files are blanked or ranged. LDS requests require a
    DUA, but do not go through a Privacy Board review."
-   "Public Use File (PUFs) [...] have been edited and stripped of
    all information that could be used to identify individuals." PUFs
    can be downloaded right off Medicare's website.

An important thing to remember is that, because claims data are mostly
about money, most people are interested in them for money reasons. A lot
of claims data research is about the cost of procedures, quality of
healthcare, etc. Reviewers might be surprised that you're interested in
antibiotic use because it affects bacteria, because most research using
claims data is about how healthcare affects the humans.

The folks overseeing your data request will probably ask questions about
your project. Their goal is to ensure that you have the data you need to
be able to answer the question you want to. They also want to be sure
that you actually need the data you request to answer the question at
hand.

This process will also include questions about how the data will be
stored. Claims data are protected health information (PHI). Even though
the identifiers for the people in the dataset will almost certainly be
masked, information that could potentially be used to match an
individual (e.g., the fact that so-and-so picked up a prescription for
drug D on day Y) is enough to make it PHI. This means that you'll need a
place to keep the data that is compliant with HIPAA standards.

Medicare and CHIA have a streamlined process for updating your data
query for each year. So if you made a request for a certain kind of
data, you can just request to re-up your dataset for that new year,
rather than go through the whole data request process again.

All of these requests also cost money, order of $1,000 per file (e.g.,
prescription data, diagnosis data, etc.).
