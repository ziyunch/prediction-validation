# Prediction Validation
  A coding challenge for Insight Data Engineering Program

## Requirements
  Python 3.6.5

## Problem Statement
### Problem Description
  Write a program that reads in two time-ordered files and calculate the average error within the given window size.

### Input Format
  - `actual.txt` and `predicted.txt`: time-ordered line-separated lists containing pipe-delimited `hour` (integer), `stock`(string) and `price`(float);
  - `window.txt`: an integer greater than 0.

### Output Format
  - `comparison.txt`: time-ordered line-separated lists containing pipe-delimited `hour`, `hour+window`, `average error`.

## Original approach (`pv-ry.py`)
1. Dollar to penny
    Since the dollar currency only contains 2 decimal points, float is not a precise way to represent it. I've change dollar currency into penny, which have integer amounts, at the beginning. In the end when I calculate average errors, I switch to dollar and round to 2 decimal points.
2. Prepare the data structure
    dictionary: `pred_dict = {hour:{stock:price}}`;
                `time_dict = {hour:(count, error_sum)}`;
    variable: `time_count`.
3. Read and parse `predicted.txt`
    - Start with `predict.txt` since not all stock values have useful predicted information.
    - Read and parse `predict.txt` and prepare dictionary `pred_dict`.
    - Since all the predicted stock price were based on actual price for the same stock, I count the stocks for comparison in each hour when I prepare `pred_dict` and store in `time_dict`.
    - Maximum hour is also counted as `time_count`.
4. Compare with `actual.txt`
    - Read and parse `actual.txt`.
    - If find a stock at given hour in `pred_dict`, calculate the price difference between prediction and actual value and add it into `error_sum` to finish up building `time_dict`.
5. Calculate average in time windows.
    - Start with time window from 0 to `window-1`. (e.g. `0 to 1`, when `window = 2`).
    - Slide the time window by removing the previous starting hour and adding the current ending hour.
    - Calculate average error for every time window and write into file.
    - Use `dictionary.get()` to find value from time_dict, in case we don't have good prediction of stock price in certain hours.

## Comment
1. Test passed within 0.01 of the expected value;
2. It is efficient to use dictionary as container datatype, but it may occupy to much memory when scale up.

-------
(Aug 31, 2018)

## Adapted approach (`pv-ry1.py`)
1. Use `deque` from `collections` in Python's standard library. This is a list-like container with fast appends and pops on either end.
2. Since the data are time-ordered, we could use `deque` as the container datatype when we compare `pred_dict` with `actual.txt` and build `time_deque` which save count and sum of error for every hour.
3. When we slide the time window, we could also make a small `window_deque` to save the data we pop out from `time_deque` but might be useful for later time window.
4. The time complexity of `deque.append()` and `deque.leftpop()` is O(1), thus we would retain (or even improve) the efficiency while reduce memory usage.
