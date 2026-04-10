Adversarial biological code review. Don't let me ship until the science passes scrutiny.

Steps:

1. Determine the base branch (main or master)
2. Run `git diff <base>...HEAD` to see all changed code
3. Read all .md planning files in the repo (SPRINT_PLAN.md, HANDOFF.md, any biogrill_*.md,
   any docs/*.md). These encode biological decisions, planned experiments, and implementation
   choices that are just as load-bearing as the code itself.

── PART A: CODE REVIEW ─────────────────────────────────────────────────────────

4. For every changed line that encodes a biological assumption, challenge it across
   these categories:

   Magic numbers and thresholds
   - Every numeric constant that derives from biology (offsets, fractions, window
     sizes, fold-change cutoffs, p-value thresholds): where does it come from?
     Is it from a paper, a protocol, or someone's intuition? Is it appropriate for
     this assay, read length, organism, or sequencing depth?
   - Are thresholds that were calibrated on one dataset being applied to another
     without re-validation?

   Coordinate systems and feature definitions
   - 0-based vs 1-based: does every genomic/transcriptomic coordinate conversion
     match the source format (GTF = 1-based inclusive; BAM = 0-based)?
   - Strand handling: are + and - strand features processed symmetrically and
     correctly? Does "5-prime" mean the right thing for each strand?
   - Feature boundaries: are start codons, stop codons, UTRs, splice sites handled
     correctly for the biological question being asked?

   Assay-specific assumptions
   - Are protocol-specific artifacts accounted for (e.g. inhibitor-dependent
     artifact zones, adapter contamination windows, PCR duplicate hotspots)?
   - Are library preparation assumptions correct (stranded vs unstranded, paired vs
     single-end, fragment length distributions)?
   - Is the sequencing depth sufficient for the statistical operation being performed?

   Normalization and composition
   - Are counts normalized in a way that is valid for the downstream comparison?
   - Are pseudocounts applied symmetrically across conditions or modalities?
   - Does the normalization assume a particular null (e.g. most genes unchanged)?
     Is that null biologically reasonable for this experiment?

   Filtering logic
   - Does every filter remove what it claims to remove, and nothing else?
   - Are filters applied in an order that could introduce bias (e.g. filtering on
     one modality before the other changes the composition of both)?
   - Are low-expression filters depth-aware, or do they use absolute count cutoffs
     that become incorrect at different sequencing depths?

   Identifiers and annotation
   - Are gene/transcript IDs from the same annotation release throughout?
   - Is version stripping (ENST00000x.5 -> ENST00000x) justified, or could it
     silently merge transcripts from different loci?
   - Are multi-transcript genes handled at the right level of resolution for the
     biological question?

── PART B: PLAN REVIEW ──────────────────────────────────────────────────────────

5. For every planned step, proposed method, and stated biological rationale in the
   .md files, challenge it across these categories:

   Biological legitimacy of the approach
   - Does the proposed method actually measure or control what it claims to?
     (e.g. "stall detection removes artifacts" - does stall detection truly separate
     biological pausing from technical artifacts, or does it remove both?)
   - Are the biological assumptions underlying each filter or normalization step
     consistent with what is known about the assay and organism?
   - Are the stated biological goals (e.g. "reduce TE outliers") actually achieved
     by the planned methods, or could confounders remain?
   - Are planned threshold values (wherever stated in prose) cited to literature,
     derived from data, or just asserted? Flag all "we will use X" without a source.

   Implementation rigor of the plan
   - Is each planned step specific enough that two engineers would implement it the
     same way? Vague steps like "validate the output" or "check the results" are not
     plans - flag them.
   - Are edge cases named? (e.g. genes with all isoforms removed, zero-depth
     transcripts, single-replicate cell types)
   - Are failure modes and their handling specified? What happens when a step
     produces unexpected output?
   - Are dependencies between steps made explicit? Can step N start before step N-1
     is fully validated, or does it assume N-1 is correct?
   - Are compute/memory requirements stated where they are non-trivial?

   Consistency between code and plan
   - Does the implemented code actually match what the plan says? Read both.
   - Are there planned steps that are documented as DONE but where the implementation
     is incomplete, approximate, or different from the plan spec?
   - Are there code behaviors (e.g. defaults, sentinel management, fallback logic)
     that are not reflected in the plan documentation?
   - Are stated blockers real blockers, or have they been resolved in code but not
     updated in the plan?

   Scientific validity of deferred decisions
   - For items marked "calibrate later" or "threshold TBD": what is the plan for
     actually calibrating them? Is the calibration approach sound?
   - For deferred tasks: is deferral scientifically safe, or does it mean the
     pipeline is currently running with an unvalidated assumption?
   - Are stretch goals or "nice to have" items clearly separated from items that
     are required for scientific validity of the output?

── RATINGS ──────────────────────────────────────────────────────────────────────

6. Rate each finding:
   - Code assumptions: VALID / QUESTIONABLE / WRONG
     - VALID: supported by the protocol, literature, or dataset metadata
     - QUESTIONABLE: plausible but undocumented; needs a comment or assertion
     - WRONG: contradicts known biology or is inconsistent with the stated protocol
   - Plan elements: SOUND / QUESTIONABLE / FLAWED
     - SOUND: specific, biologically justified, internally consistent
     - QUESTIONABLE: vague, uncited, or relies on an unvalidated assumption
     - FLAWED: the approach cannot achieve its stated goal, or a deferred calibration
       is blocking scientific validity right now

7. Rate the overall changeset + plan state: SHIP IT / NEEDS COMMENT / NEEDS FIX / BLOCK
   - SHIP IT: all code assumptions valid or documented; all plan steps specific and sound
   - NEEDS COMMENT: valid/sound but not obvious to future readers
   - NEEDS FIX: a QUESTIONABLE assumption or plan element is load-bearing and unguarded
   - BLOCK: a WRONG code assumption or FLAWED plan element will silently corrupt results
     or produce scientifically invalid output

8. List every QUESTIONABLE/WRONG/FLAWED finding with:
   - File and line (or plan section)
   - The assumption or plan element
   - Why it is questionable/wrong/flawed
   - What evidence, guard, or specificity would resolve it

9. After fixes or comments are added, re-review from step 1
10. Only give SHIP IT when every assumption is either validated or explicitly
    documented with its justification and known limitations, AND every plan step
    is specific enough to implement correctly
