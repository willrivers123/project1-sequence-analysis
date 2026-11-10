# project1-sequence-analysis
identifying mutations in S. pombe NGS reads (C7147 Project 1)

## Title and the purpose
This program compares 200-nt S. pombe next generation sequencing reads with a provided reference sequence to find nucleotide mismatches. It tests whether samples treated with a genotoxic agent carry more mutations than untreated controls.

## How to run the program
Open `will_rivers_C7147_Project_1_assessment.ipynb` in Google Colab using the "Open in Colab" symbol/dropdown. Then choose Runtime -> Run All. The setup cells download `reference.txt` and `reads.csv` automatically.

## Inputs and Outputs
Inputs: a 200-nt reference sequence and a CSV of reads, each with `read_id`, `sample`, `group` and `sequence`.

Outputs:
- `analyse_reads` returns each read's validity, error codes and mismatches.
- `summarise_samples` prints a summary table and a substitution table for each group, and returns the per-sample counts.
- `compare_positions` prints and returns group means, differences and Welch's t-test results for the three top mismatch positions.

## Code Design

1. Separate counting from printing: I split `summarise_samples` into `count_sample_results` (which only counts) and printing helpers such as `print_summary_table`. This lets me test the counts with `assert` without checking printed text. I did consider one large function, but it would have been harder to test.
2. Reuse helpers across steps: Step 5 reuses `calculate_percentage`, `get_top_positions` and `format_percentage` from Step 4. This means that ties are broken the same way in both steps, and there's no division by zero for samples with no valid reads.
3. Keep invalid reads instead of deleting them: `analyse_reads` keeps all invalid reads with `mutations=[]`, so they are still counted as invalid in Step 4. I chose this over removing them completely so that data-quality problems stay visible.

## Code Testing
1. `validate_read("ATN", "ATGC")` returned both `wrong_length` and `invalid_characters`, in the required order.
2. A sample containing only invalid reads gave `mismatch_percentage=None`. In Step 5 the same kind of sample was left out of the comparison and a note was then printed.
3. With one sample per group, `compare_positions` returned `None` for the t-statistic and p-value instead of crashing.
