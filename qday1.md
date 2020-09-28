---
permalink: /summary/day1
---

# NorESM User Workshop 2020, Day 1

## Questions/Comments

* Where does CISM come from now. Directly from the ESCOMP repository?
    - you can find where the cism comes from from this Externals.cfg file: https://github.com/NorESMhub/NorESM/blob/master/Externals.cfg
* Can Externals.cfg "tags" refer to a commit hash instead of a tag in the repository?
    - answer? 
* Can one see who fork'ed a repository?
    - Yes, you can for instance look at the "Fork" tag at the top of the gitHub page. It will list all forks that exist on gitHub
* if i wanted to run cesm-cam in addition to oslo-cam for comparison, i should use the one from the cesm hub, not noresm2 hub, since that cesm in there already has changes
    - There are always all CESM related compsets with repository and you can perform both type of experiemtns from same repository without changing to External.cfg and switching to code. Code is maintianed in such way all CESM related code works independently. 

* How do grid resolutions relate to MM and LM
    - MM is alias for F09 and LM for F19 (1.9x2.5). Both LM and MM has the same 1-deg ocean/ice component. only the atmospheric/land differ.
    - More specifically for LM resolution, specify `--res f19_tn14 option` in the create\_newcase command. Here f19 specifies a ~2 deg finite volume grid for atm/lnd while tn14 specifies a ~1 deg tripolar grid for ocn/ice. For MM the option is `--res f09_tn14`.

* Given the parallel execution of atmos and ocean, is there a recommended ratio of procs, some rule of thumb
    - one has to firstly run some test experiments, and looking the performance of each components according to the 'timing statistics' from the model log, and adjust the cpu layout iteratively to get an balanced performance.

* Where is PI (piControl experiment) case stored?
    - Information on CMIP6 experiments: link to [noresm-exp](https://noresmhub.github.io/noresm-exp/noresm2_deck/noresm2_lm_piC.html)

* On exercise 1 Task 2, "Change `env_mach_pes.xml` for total 128 processors counts: ATM=96, CPL=96, OCN=32,  WAV=96,GLC=96, ICE=50, ROF=1, LND=45". Why don't the numbers (96,32,96,...) add up to 128? What is the rule for chosing these number?
    - you can see from the cpu layout, the sum of atm and ocn components is the total of cpus, and GLC, LND and ICE share the same amount of cpu as ATM and as CPL. Therefore, the total numer of cpu is ATM/CPL (96)+OCN (32)=128

    <img src="../img/cpupe.png" alt="cpu layout" width="800px"/>


* What are the different between `NTASKS` and `ROOTPE` in `env_mach_pes.xml`?
    - as shown in the above figure, for example, for the ATM, NTASKS is 1024, while ROOTPE for it is 0 as counting from the leftmost as 0.
    - for GLC, NTASKS is 124, its ROOTPE is 0.
    - for LND, NTASKS is 500, its ROOTPE is 124 (0-123 occupied by GLC)
    - for ICE, NTASKS is 500, while its ROOTPE is 624 (0-623 occpupied by GLC and LND)
    - for OCN, NTASKS is 156, while ROOTPE is 1024
    - CPL, NTASKS 1024 as ATM, ROOTPE0
    - so on and so forth...
