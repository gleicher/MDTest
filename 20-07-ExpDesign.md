## Database Principles

- database should be logging (experiments only append), no updates (only inserts)
- avoid "free form" things wherever possible (structured data)
- have tables be structured/semantic - group like data as much as possible

## Key Entitites

- Session
  - technically, this is not "user"
  - should have uniq ids

- Trial
  - one actual experimental trial - do not use for session meta info

## Experiment Design (for an example)

Scatterplot center finding - how well do people estimate the centers of scatterplots?

Hypotheses:
- People do estimate the centers
- People are not estimating the center of the visual hull
- Accuracy depends on distribution size
- It works even for non-uniform distributions (ellipse centers)
- More complex distributions throw people off
- mixes of types doesn't throw people off
- little improvement with practice

Measure:
- accuracy (Euclidean distance, distance along axes)
- time (from show to click - weak measure on Turk)
- (optional) confidence squares

Mechanism / Trial:
- Show a scatterplot, ask user to click on center
- Time limit, but not time pressured
- Additions:
  - make sure images are pre-fetched (so loading doesn't count towards time)
  - count-down / focus check (only count down if mouse in square)
  - time limited (disappears after N seconds, need to redo)
  - after click center, show user center, ask them to draw "confidence box"

Implementation Details:
- all stimuli pre-computed (PNG files)

Experiment Workflow / Outline

- Description (probably on Turk Site)
- Consent (is this on Turk Site?)
  - if not consent, ask them to return hit?
- Uniqueness check (is this done by Turk or by us?)
  - this could be even earlier
- Qualification (not needed but keep in workflow)
  - show Ishihara plate and make sure not colorblind
  - for this experiment, colorblind is OK - but good to know
  - I forget if we are allowed to log if people don't qualify
- Training
  - instruction page
  - practice trials
    - 1-2 to make sure people see it
    - example that shows not convex hull
    - have people repeat until they clearly show can do it
      - not important for this - but repeat until "learned" is good for future
- Repeat for Each Block (this experiment might not really have blocks)
  - block order might be fixed, random, stratified (not sure how to implement that)
  - allow for rest between blocks
  - allow for instructions for block
  - allow for practice per block
  - repeat for each trial/stimulus
    - randomize order (select at random)
      - randomly insert an engagement check trial
      - if subject gets the ec wrong (?)
    - ready (maybe wait for click)
    - pre-load screen (maybe count down)
      - enforce mouse in area (?)
      - useful to have mouse centered (only count down if mouse within start circle)
    - show image
      - capture click location
      - done at click
      - potentially time out if they wait to long
    - (potentially) re-display with click overlayed, ask for rectangle
    - log results
    - (potentially - not in this experiment) show right answer
  - after block (potentially, maybe not this experiment)
    - show score
- Post-Experiment
  - Debrief (none for this experiment - except maybe give score)

Ideas / Questions:
- training is a "practice block" - if the trials are logged as regular trials
- within a block training can have a negative trial number or something
- probably want to have a numbering that denotes something as an engagement check trial
- do we want to connect trial number to stimulus
- stimulus ID might want to encode the "correct" answer for quick checking (or we can do it with a join if we have a database of the right answer x stimulus - but that seems painful)

- Maybe thinking about have "global" tables is overkill - but I think there is something to be said for tracking people across experiments (you are excluded if your IP address or TurkID has been used in any of an experiment family)

## Strawman Design

### Table 1: session ID table (Global across all experiments)

this table just stores the list of used session IDs so we can make sure we assign unique IDs each time

ID: autoincrement unique integer primary key
Timestamp

### Table 2: session info table (Global across all experiments)

we need to record info about the sessions - there was a reason i thought this should be separate from #1, but maybe it doesn't have to be.

I am thinking that this is a place to keep "user" info, so we can check for duplicate re-use, but I am not sure.

uniqueid: in case there are multiple entries for a session(?)
sessionid: a primary key from Table 1
ip address: so we can track usages
experiment: something to say what experiment this is (a sting?)
turk ID: who is the turker (or something to say none)
HIT ID: the HIT for mech turk
timestamp: this would be the start of the session
assigned condition (see note)

note this should be the same for all experiments - except for assigned condition

for experiments where each participant is assigned to a condition, we need to record that. this seems like the logical place - but it might be specific to each experiment, so it would need to be a per-experiment table

it might just be a string or number - but sometimes, the "assigned condition" might be an ordering (of blocks, or even of trials) - as a way of doing counterbalancing or stratified sampling. i think making this a string as a place to start will be good enough.

### Table 2b: Consent

This might be redundant - we might only store something in table 2 if the user consents.

So maybe we just have a field in #2 that is "consent" (binary)
or
We also have a "consent form" field with some kind of "pointer" so we know which consent form they agreed to (can't save a snapshot of the whole page, but maybe a has, or a version ID, or ...)

session id: primary key (matches from table 1 or 2)
timestamp
consent: true/false
consentform: something denoting which version of the consent form

The consentform thing may be overkill - since the consent form shouldn't change over the course of an experiment. But it could give us a nice thing in the case of an audit to allow us to say "yes, we got consent, and here is exactly what they saw"

### Table 3: (probably not)

It might be nice to have some table to track the "training". For this experiment, we can probably use the trial table (and just call it a special block)

### Table 4: Trials

This table will be per-experiment, and have a slightly different form for each experiment (since it has to record the appropriate stimulus and response for the experiment, and capture the block structure)

this assumes the experiment is a repeated measures experiment (repeat similar things over and over)

note: that for a block there is a number (is it the 1st, second, third that the user does), and then a descriptive number/name (i am calling these number/type respectively)

similarly, with trials... there is the number (which trial is this in the block), and then something that describes what the trial is (which stimulus was shown, since that's the thing that varies with each trial)

and, knowing what the "right answer" is will be useful for checking/analyzing the results quickly. this depends on the stimulus. for this experiment, it is the x/y position

i am including a generic "note" - but it could be we want something more structured to capture "stuff that we might want to remember". two potential things that come to mind: if the trial timed out (the person clicked after the stimulus went away) and if we are tracking mouse focus, if we lost focus during the trial (might be a sign of inattention)

sessionid
timestamp

blocknumber
blocktype
trialnumber
trialstimulus
answerX, answerY

responseX, responseY
responseTime

note

# Table 5 - Demographic data

this is per session (since we don't have a notion of users in a strong way).

it is probably table per experiment, since each experiment might have different questions.
(although, since the demographics need to be approved by the IRB, they will probably be similar)

i think its easier to have this as a structured record with one field per question. the questions will be the same for all subjects.

I don't know what all the questions on the demographic form are, so I just put "answer"

sessionid
timestamp

age
gender
answer
answer
answer
comments

# table 6 - end of session

I am not sure if this is feasible, but it is probably good to record when a subject is "paid" - a record of the responses we send to turk at the end of a session. kindof like keeping track of a receipt.

i think this would be that when we "post" to turk to pay a participant, we also write an entry to this "payment log"

exactly what information, i am not sure - probably the same info that gets posted to turk. if we can get the amount paid that would be nice (does turk give us info about the either in response to the final post, or from some other way?)

this can be a global table, since it is the same for all experiments. and we might want to do things like query "who participated in all experiments on a day"

sessionid
timestamp
info-posted-to-turk
any-info-we-get-back-from-turk
