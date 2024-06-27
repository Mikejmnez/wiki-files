The overall scope of the work that management thinks should be done for
a quarter (i.e., a PI) is given in the Candidate Feature list. The
Planning Agenda describes how the 200+ people in the ESDIS project will
coordinate the planning process for that work.

Information sources:

- Overall information with schedule:
  <https://wiki.earthdata.nasa.gov/display/EPS/ESDIS+Program+SAFe>
- Planning Agenda:
  <https://wiki.earthdata.nasa.gov/display/EPS/PI+Planning+for+23.4+Agenda>
- Candidate features:
  <https://wiki.earthdata.nasa.gov/display/EPS/PI+23.4+Candidate+Features#tab-Transformation>

But... The Candidate Feature list is not the only place where features
are found. Other places include:

- Features that were not completed in the previous quarter
  (*Carryover*);
- Features that are internal to the group (*Team*); and
- Features that are important to other groups in the larger collection
  of groups (aka, 'train', *Train-level*)

Regardless of source, all features are planned similarly.

## How to Plan a Feature

Based on the introduction, there are four kinds of features: Candidate,
Carryover, Team and Train. All these are planned the same way *except*
for the start of the process. The Candidate features have a more formal
origin than the remaining three feature types.

- In Jira, write an Epic for the feature.
  - Include the Acceptance Criteria (AC) for the feature in the Epic's
    AC - you may have to hunt around Jira's various 'Edit' features to
    find how to add/edit an AC
  - Bind this to the PI using a Fix Version label for the PI (e.g.,
    *Transformation PI 23.4*)
- For that feature, write one or more tickets that describe what to do.
  For each ticket (normally a *Task*):
  - write an AC
  - assign points
  - make it visible as part of the P by assigning a Fix Version label
    for the given PI (e.g., *Transformation PI 23.4*)
  - assign a person
  - map those tickets across the Sprints in the PI
- If there is a dependency between our work on the feature and some
  other team, link that using a ticket in the Epic, not the Epic itself.

If another team has feature that will depend on us in some way, make a
non-Epic (e.g., Task) ticket and link that up with the non-Epic ticket
in their Jira.

Why do all this labeling and linking? Because that's how the page of
Objectives and Risks (e.g.,
<https://wiki.earthdata.nasa.gov/display/EPS/Transformation+Train+-+PI+23.4+-+Objectives%2C+Risks%2C+and+Dependencies+Dashboard#tab-OPeNDAP>)
gets the stuff it displays.

## Candidate Features are Special

Because they are derived from perceived needs that span the whole ESDIS
group and are blessed by NASA management, the Candidate Features are
special. The only way they differ from the other three types is that
their ACs are given in a Feature Planning ticket that can be found as a
link on the Candidate Feature page. That AC should be used to write the
AC for the Epic we make for the feature. However, we need to be
circumspect about what can be done in a single quarter versus what is
shown on the Feature Request page. Make sure to write an AC that'
achievable. During review if something that is required has been
removed, that will become apparent.