# Experiments for Scatterplots

## Experiment 0 - Establishing Baselines

Starting with 0 since this really isn't about the actual things I am interested in.

Shape perception provides a baseline for distribution perception.

- Where do people see the centers of shapes?
- How well can we estimate the center of a shape?
- Is center perception thrown off by center of mass effects?
- If asked about center of mass, do we get reasonable answers?

While these questions are explored in the perception literature, our goal is to find numerical baselines for our experiment design (online clicking from turkers).

- subject pool issues
- on-screen distortion issues
- fits-law (how accurately can you use an input device) issues
- what kinds of accuracies can we expect from the design
- are there unexpected confounds (input device variability, screen variability, ...)

0.1 - Click on the center of circles

- measure distance of click to "real center"
- vary position and size
- hypotheses: (really sanity checks)
  - H0: accuracy correlated with size (but how?)
      - if the target is small enough, this is just fits law accuracy kindof thing
      - as the target grows it is the small accuracy + any estimation accuracy
  - H1: accuracy not correlated with position (shouldn't be)
    - maybe it should be 
        - gravity effects [https://journals.sagepub.com/doi/10.2466/pms.110.1.195-212], 
        - left right ideosyncracies (rashid p104), 
        - bias towards center (starting point, minimal motion) 
  - H2: may be other confounds (input devices type, browser, order)
      - how do we find them? check for interaction with any metadata we can gather
  - H3: the variance due to variability (e.g. H2) is small enough that we still see H0,H1
- design notes
  - need to sample position & size well enough to get effects
  - **might need to be within subjects, which factors out differences in display and other things**
  - want to have position and size be decorrelated so we can see effects more easily
  - because we are trying to get at the perception / motor accuracy baseline, it will be really important to control for as much as we can
  - it is probably good enough to just check that the clicks are in the circles as the engagement check
      - mark a bad trial and warn user if not (?)
  - could put bad trials back at the end of the queue

Stimulus generation idea:
1. Stratify sampling of position (nxn grid, large and small sample per grid)
2. 2 samples (large and small) per grid cell - circle radius R and L-r
3. Sizes (arbitrary thinking) r between 10 and 90 px (so the sizes are r and 100-r)
4. Randomize center positions within grid (doesn't really balance, but the grid stratification should get us close enough)
5. Uniform random in size from 10-50 (and therefore 50-90)... could be scrambled stratified (create list of n^2 that is uniform and scrambled the order)

Image Details
1. 600x600 pixels (or is this too big? should it be 400x400 in case someone has a less than FHD screen)
2. 4x4 grid gives 32 trials
3. grids are 100-200, 200-300, 300-400, 400-500 - so even if circles are at edge, they still fit within image
4. r = scramble([10,10,15,15,20,20,25,25,30,30,35,35,40,40,45,45])
5. does color matter? 
6. solid white background


## Related Work

### Shape Perception

- "Performance statistics in centroid tasks are not the same as those used in classic decision tasks" https://escholarship.org/uc/item/5v09b1q9 (PhD dissertation)
  - Rashid, J. A. (2019). Psychophysical Inference from Centroid Estimation. UC Irvine. ProQuest ID: Rashid_uci_0030D_15926. Merritt ID: ark:/13030/m5sn5ghg. Retrieved from https://escholarship.org/uc/item/5v09b1q9
  - left/right ideosyncracies (different people had biases) - p104
  - attentional spotlight is fixed size
  - short exposures are less "data-driven" (takes time to move eyes)
  - **experiments involve many things / strategy** (estimate target (e.g. center) given stimulus), effectiveness is task dependent, people develop stratgies to be effective
    - **feedback lets people learn strategies**
    - **Strategic Estimator**
    - ?? Double Pass Methods ??
  - "Humans are remarkably accurate centroid estimators" (p9) - but biases
  - **heightened sensitivity to contours**
  - "Other kinds of centers, providing compact descriptions that areuseful for guiding behavior or attention have been proposed from biology and demonstratedpsychophysically (Kovacs et al., 1998).  Many investigations conclude that the visual systemrelies  primarily  on  boundary  information  when  performing  tasks  of  perceptual  alignment(Proffitt et al., 1983; Vos et al., 1993), or location discrimination (Hess et al., 1994).  There is certainly only a single point in space that is simultaneously closest to every point on the boundary."
  - tasks
    - hull task (center of convex hull)
    - mass task (center of mass)
  - experiment
    - LOTS of training (600)
    - considers dots vs. gabors (very different between subjects)
    - each subject has a systematic bias

- even centers of 3 and 4 points show problems https://journals.sagepub.com/doi/10.2466/pms.110.1.195-212 [libyVisualEstimationThree2010] **good references**

- people can localize the center of a cluster https://www.sciencedirect.com/science/article/abs/pii/0042698994902887 [hessLocalizationElementClusters1994] **design is left/right of a line, circular regions** - **different mechanism than when the objects are solid**
  - **cue is center of luminance (?!?)**
- clusters of elements - averaging uses multiple cues https://www.sciencedirect.com/science/article/pii/0042698995002057?via%3Dihub **design is left/right of a line** [badcockLocalizationElementClusters1996]

- tied to balance perception http://dx.doi.org/10.1037/xge0000151
  - find centers of funny shapes
    - inflation effects
    - dots are better (wood is better too)

- complex neural mechanisms that try to understand shape in ways invariant of viewing conditions [10.1093/acrefore/9780190264086.013.75] for survey [pasupathyVisualShapeObject2018]
- systematic errors in center perception (and center of mass perceception) are different in children and adults https://journals.openedition.org/cpl/430 [baud-bovyVisualLocalizationCentre2004]
- influence by "causal history" https://www.nature.com/articles/srep36245 - "Objects are not only parsed according to what features they have, but also to how or why they have those features." (experiments look at **percieved principle axes**) - suggesting that people see these axes [sproteVisualPerceptionShape2016] - *shape completion might affect us*
- symmetry matters https://link.springer.com/article/10.3758/BF03206768 [daviRoleSymmetryDetermining1992]

- how we interpret patterns of dots is short term learned - so expectations matter https://www.cell.com/current-biology/fulltext/S0960-9822(08)00876-2 (natural statistics in general - experiments are for orientation averaging) [schwarzkopfExperienceShapesUtility2008]

- shape perception is robust against transformation (rotation, scaling) https://doi.org/10.1016/j.visres.2015.04.011 [schmidtPerceptionShapeSpace2016]


Queue:
- https://www.sciencedirect.com/science/article/pii/0042698996000430 (brightness vs. center)
- https://onlinelibrary.wiley.com/doi/full/10.1111/desc.12584 kids do similar to adults (sometimes)
- https://www.frontiersin.org/articles/10.3389/fnhum.2016.00446/full (gestault phonemoma capture attention)
- model of perceptual center https://journals.sagepub.com/doi/abs/10.1068/p231085

Less important Queue:
- https://link.springer.com/article/10.3758/s13423-018-1454-5 (temporal averaging)
- https://www.nature.com/articles/s41598-019-50254-5 (general connect concepts to objects)
- https://psycnet.apa.org/record/1983-11722-001 Standardizing distances of observations within shapes.
