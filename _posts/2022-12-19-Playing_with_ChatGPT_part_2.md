---
layout: post
title: Playing with ChatGPT (part 2)
---

I asked ChatGPT to write some C code to fetch reads using the HTS library...

Question:
Can you write code in C to fetch reads in a BAM file from chromosome 1 using the htslib library?


Response:
<blockquote>
Sure! Here is some example code in C that uses the htslib library to fetch reads from chromosome 1 in a BAM file:

```C
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <htslib/sam.h>

int main(int argc, char *argv[]) {
    // Open the BAM file
    char *bam_fname = argv[1];
    samFile *bam_file = sam_open(bam_fname, "r");
    if (bam_file == NULL) {
        fprintf(stderr, "Error opening BAM file: %s\n", bam_fname);
        return 1;
    }

    // Load the BAM index
    hts_idx_t *bam_idx = sam_index_load(bam_file, bam_fname);
    if (bam_idx == NULL) {
        fprintf(stderr, "Error loading BAM index\n");
        return 1;
    }

    // Open the header and reference data
    bam_hdr_t *bam_hdr = sam_hdr_read(bam_file);
    if (bam_hdr == NULL) {
        fprintf(stderr, "Error reading BAM header\n");
        return 1;
    }

    // Find the index of chromosome 1 in the reference data
    int ref_id = -1;
    for (int i = 0; i < bam_hdr->n_targets; i++) {
        if (strcmp(bam_hdr->target_name[i], "1") == 0) {
            ref_id = i;
            break;
        }
    }
    if (ref_id == -1) {
        fprintf(stderr, "Error: chromosome 1 not found in BAM file\n");
        return 1;
    }

    // Initialize a BAM iterator to fetch reads from chromosome 1
    hts_itr_t *iter = sam_itr_queryi(bam_idx, ref_id, 0, 0);
    if (iter == NULL) {
        fprintf(stderr, "Error creating BAM iterator\n");
        return 1;
    }

    // Fetch the reads one by one
    bam1_t *read = bam_init1();
    while (sam_itr_next(bam_file, iter, read) >= 0) {
        // Process the read here
        // You can access the read data using the bam1_t struct
        // For example, you can use bam_get_qname() to get the read name
        // or bam_get_seq() to get the read sequence
        // ...
    }

    // Clean up
    bam_destroy1(read);
    hts_itr_destroy(iter);
    bam_hdr_destroy(bam_hdr);
    hts_idx_destroy(bam_idx);
    sam_close(bam_file);

    return 0;
}
```
</blockquote>
