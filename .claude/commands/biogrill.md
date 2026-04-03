Adversarial biological code review. Don't let me ship until the science passes scrutiny.

Steps:

1. Determine the base branch (main or master)
2. Run `git diff <base>...HEAD` to see all changes on this branch
3. For every changed line that encodes a biological assumption, challenge it across
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

4. Rate each biological assumption: VALID / QUESTIONABLE / WRONG
   - VALID: supported by the protocol, literature, or dataset metadata
   - QUESTIONABLE: plausible but undocumented; needs a comment or assertion
   - WRONG: contradicts known biology or is inconsistent with the stated protocol

5. Rate the changeset overall: SHIP IT / NEEDS COMMENT / NEEDS FIX / BLOCK
   - SHIP IT: all assumptions valid or documented
   - NEEDS COMMENT: valid assumptions but not obvious to future readers
   - NEEDS FIX: a QUESTIONABLE assumption is load-bearing and unguarded
   - BLOCK: a WRONG assumption will silently corrupt results

6. List every QUESTIONABLE or WRONG finding with: file, line, assumption, and
   what evidence or guard would resolve it

7. After fixes or comments are added, re-review from step 1
8. Only give SHIP IT when every assumption is either validated or explicitly
   documented with its justification and known limitations
