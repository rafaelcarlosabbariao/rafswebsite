---
title: 'Humanizing Sorting Algorithms in R'
date: Sun, 16 Oct 2022
draft: false
tags: [R, algorithms, data analysis] 
---

Intro
-----

I’ve just read “Algorithms to Live By: The Computer Science of Human
Decisions” and there’s a neat sorting chapter that made these concepts a
little more tangible and I will try to show some examples of these
sorting methods in R.

Sorting is the backbone of data analysis and there are a few methods
discussed in the aforementioned book:

-   Bubble Sort
-   Insertion Sort
-   Merge Sort

As a reminder, we have some regular sorting functions in base R.

    set.seed(123)
    n <- sample(1:100, 10)

    sort(n) # sorts the vector in ascending order (or descending with decreasing = TRUE) order

    ##  [1] 14 25 31 42 43 50 51 67 79 97

    rank(n) # returns the sample ranks of the values in a vector

    ##  [1]  3  9  7  1  8  4  6  5 10  2

    order(n) # returns the indicces of the vector in a sorted order

    ##  [1]  4 10  1  6  8  7  3  5  2  9

But onto the sorting algorithms mentioned….

Bubble Sort
-----------

Bubble sort is the most “brute force” sorting method of the few listed.
Appropriately enough, the analogy provided in the book is ordering books
on a bookshelf.

Given a number of disorganized books, we compare the first two adjacent
book titles and reordering the “larger” element to the right (if sorting
in ascending or alphabetical order). This is repeated sequentially until
the end of the series and until all elements are in ascending order.

For example:

Step 0: Moby Dick -&gt; Algorithms to Live By -&gt; Holy Bible Step 1:
Algorithms to Live By -&gt; Moby Dick -&gt; Holy Bible Step 2:
Algorithms to Live By -&gt; Holy Bible -&gt; Moby Dick

    bubbleSort <- function(n) {
      len <- length(n)
      for (i in 1:(len - 1)) {
        for (j in 1:(len - i)) {
          if (n[j] > n[j + 1]) {
            # Swap elements
            temp <- n[j]
            n[j] <- n[j + 1]
            n[j + 1] <- temp
          }
        }
      }
      return(n)
    }

Insertion Sort
--------------

Within the same bookshelf OCD analogy, we begin with an empty shelf.
After arbitrarily sorting one book on the shelf, we then compare the
next one with the “sorted” book, and so on until all are organized.

    insertionSort <- function(n) {
      for (i in 2:length(n)) {
        temp = n[i]  # set the first unsorted element for comparison
        j = i - 1
        while(n[j] > temp && j > 0) {
          n[j + 1] = n[j]
          j = j - 1
        }
        n[j + 1] = temp
      }
      return(n)
    }

Merge Sort
----------

The merge sort can also be referenced as the divide and conquer method,
not the fascist, age of empires kind but the clean up, everyone do your
part kind.

Back to the bookshelf thing… merge sort would involve multiple people
(imagine having friends) with an equal number of random books to start.
Each person organizes their own stack, finds a partner to merge their
stack in order and so on until all stacks are merged and sorted.

    # function to merge sorted arrays (to be used in main sorting function)
    merge <- function(x, y) {
      temp = numeric(length(x) + length(y))  # initialize temporary array
      startx = 1
      starty = 1
      j = 1  # start of temp array
      
      for (j in 1:length(temp)) {
        if ((startx <= length(x) && x[startx] < y[starty]) || starty > length(y)) {
          temp[j] = x[startx]
          startx = startx + 1
        } else {
          temp[j] = y[starty]
          starty = starty + 1
        }
      }
      temp
    }

    mergeSort <- function(n) {
      if (length(n) > 1) {
        # find mid point of array
        mid <- ceiling(length(n)/2)
        x <- mergeSort(n[1:mid])
        y <- mergeSort(n[(mid+1):length(n)])
        merge(x, y)
      } else {
        n
      }
    }

In comparing these sorting methods we find that …

    library(ggplot2)

    ## Warning: package 'ggplot2' was built under R version 4.0.3

    ## Warning: The package `ellipsis` (>= 0.3.2) is required as of rlang 1.0.0.

    ## Warning: replacing previous import 'lifecycle::last_warnings' by 'rlang::last_warnings' when loading 'tibble'

    ## Warning: replacing previous import 'ellipsis::check_dots_unnamed' by 'rlang::check_dots_unnamed' when loading 'tibble'

    ## Warning: replacing previous import 'ellipsis::check_dots_used' by 'rlang::check_dots_used' when loading 'tibble'

    ## Warning: replacing previous import 'ellipsis::check_dots_empty' by 'rlang::check_dots_empty' when loading 'tibble'

    library(microbenchmark)

    ## Warning: package 'microbenchmark' was built under R version 4.0.5

    set.seed(123)

    n <- sample(1:1000, 1000, replace = TRUE)

    comparison <- microbenchmark(
       bubbleSort = bubbleSort(n),
       insertionSort = insertionSort(n),
       mergeSort = mergeSort(n),
       times = 100
    )

    autoplot(comparison) +
      labs(title = "Sorting Algorithms Comparison",
           x = "Time (in nanoseconds)",
           y = "Algorithms") +
      theme_minimal()

    ## Coordinate system already present. Adding new coordinate system, which will replace the existing one.

![](sorting_algorithms_files/figure-markdown_strict/unnamed-chunk-5-1.png)

… merge sort is the best option, followed by insertion sort and close
after, bubble sort. This brings us into the world of space and time
complexity of algorithms.
